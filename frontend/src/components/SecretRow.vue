<!--
Copyright (c) 2020 by SAP SE or an SAP affiliate company. All rights reserved. This file is licensed under the Apache Software License, v. 2 except as noted otherwise in the LICENSE file

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

     http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
-->

<template>
  <v-list-item>
    <v-list-item-content>
      <v-list-item-title class="mb-1">
        {{secret.metadata.name}}
        <v-tooltip v-if="!isOwnSecretBinding" top>
          <template v-slot:activator="{ on }">
            <v-icon v-on="on">mdi-share</v-icon>
          </template>
          <span>Secret shared by {{secretOwner}}</span>
        </v-tooltip>
        <span style="opacity:0.5">({{relatedShootCountLabel}})</span>
      </v-list-item-title>
      <v-list-item-subtitle>
        <slot name="rowSubTitle" :data="secret.data">{{secretDescriptor}}</slot>
      </v-list-item-subtitle>
    </v-list-item-content>

    <v-list-item-action>
      <v-tooltip top>
        <template v-slot:activator="{ on }">
          <div v-on="on">
            <v-btn :disabled="isDeleteButtonDisabled" icon @click.native.stop="onDelete">
              <v-icon class="red--text">delete</v-icon>
            </v-btn>
          </div>
        </template>
        <span v-if="!isOwnSecretBinding">You can only delete secrets that are owned by you</span>
        <span v-else-if="relatedShootCount > 0">You can only delete secrets that are currently unused</span>
        <span v-else>Delete Secret</span>
      </v-tooltip>
    </v-list-item-action>

    <v-list-item-action>
      <v-tooltip top>
        <template v-slot:activator="{ on }">
          <div v-on="on">
            <v-btn :disabled="!isOwnSecretBinding" icon @click.native.stop="onUpdate">
              <v-icon class="cyan--text text--darken-2">edit</v-icon>
            </v-btn>
          </div>
        </template>
        <span v-if="!isOwnSecretBinding">You can only edit secrets that are owned by you</span>
        <span v-else>Edit Secret</span>
      </v-tooltip>
    </v-list-item-action>
  </v-list-item>
</template>

<script>
import { mapGetters } from 'vuex'
import get from 'lodash/get'
import filter from 'lodash/filter'
import { isOwnSecretBinding } from '@/utils'

export default {
  props: {
    secret: {
      type: Object
    },
    secretDescriptorKey: {
      type: String,
      default: ''
    }
  },
  computed: {
    ...mapGetters([
      'shootList'
    ]),
    secretDescriptor () {
      if (this.isOwnSecretBinding) {
        return get(this.secret, `data.${this.secretDescriptorKey}`)
      } else {
        return `Owner: ${this.secretOwner}`
      }
    },
    secretOwner () {
      return get(this.secret, 'metadata.namespace')
    },
    relatedShootCount () {
      return this.shootsByInfrastructureSecret.length
    },
    shootsByInfrastructureSecret () {
      const secretName = this.secret.metadata.name
      const predicate = item => {
        return get(item, 'spec.secretBindingName') === secretName
      }
      return filter(this.shootList, predicate)
    },
    relatedShootCountLabel () {
      const count = this.relatedShootCount
      if (count === 0) {
        return 'currently unused'
      } else {
        return `used by ${count} ${count > 1 ? 'clusters' : 'cluster'}`
      }
    },
    isOwnSecretBinding () {
      return isOwnSecretBinding(this.secret)
    },
    isDeleteButtonDisabled () {
      return this.relatedShootCount > 0 || !this.isOwnSecretBinding
    }
  },
  methods: {
    onUpdate () {
      this.$emit('update', this.secret)
    },
    onDelete () {
      this.$emit('delete', this.secret)
    }
  }
}
</script>
