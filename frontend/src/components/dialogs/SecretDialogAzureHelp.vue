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
  <secret-dialog-help
    title="About Azure Secrets"
    color="blue darken-1"
    backgroundSrc="/static/background_azure.svg"
    :value="value"
    @input="onInput"
  >
    <template v-slot:help-content>
      <div class="help-content">
        <p>
          Before you can provision and access a Kubernetes cluster on Azure, you need to add account credentials.
          The Gardener needs the credentials to provision and operate the Azure infrastructure for your Kubernetes cluster.
        </p>
        <p>
          Ensure that the account has the <b>contributor</b> role.
        </p>
        <p>
          Read the
          <a href="https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure"
          target="_blank"
          class="blue--text text--darken-1">
          IAM Console help section<v-icon style="font-size:80%">mdi-open-in-new</v-icon></a> on how to manage your credentials and subscriptions.
        </p>
      </div>
    </template>
  </secret-dialog-help>
</template>

<script>
import SecretDialogHelp from '@/components/dialogs/SecretDialogHelp'

export default {
  components: {
    SecretDialogHelp
  },
  props: {
    value: {
      type: Boolean,
      required: true
    }
  },
  methods: {
    onInput (value) {
      this.$emit('input', value)
    }
  }
}
</script>
