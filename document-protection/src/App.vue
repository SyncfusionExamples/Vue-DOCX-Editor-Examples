<template>
  <div>
    <div class="sample-header">
      <h1>User based restrict editing and approval workflow in Vue DOCX Editor</h1>
      <p>
        Select a user to switch between role-based editing restrictions: price-only editing with editable
        ranges, tracked changes review, comments-only review, full editing and read-only access.
      </p>
    </div>
    <div class="sample-toolbar">
      <div class="permission-holder">
        <table class="permission-table">
          <thead>
            <tr>
              <th>User</th>
              <th>Role</th>
              <th>Email</th>
              <th>Permissions</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="user in users" :key="user.email">
              <td>{{ user.name }}</td>
              <td>{{ user.role }}</td>
              <td>{{ user.email }}</td>
              <td>{{ user.description }}</td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>
    <div class="sample-toolbar">
      <label for="userSelect">Signed in user:</label>
      <select
        id="userSelect"
        v-model="selectedUserEmail"
        @change="onUserChange"
        title="Choose the user whose permissions are applied"
      >
        <option v-for="user in users" :key="user.email" :value="user.email">
          {{ user.name }}
        </option>
      </select>
      <span id="permissionBanner" class="permission-banner">
        Signed in as {{ currentUser.name }} ({{ currentUser.email }}) - {{ currentUser.role }}
      </span>
    </div>
    <div class="sample-content">
      <div class="editor-holder">
        <ejs-documenteditorcontainer
          ref="containerRef"
          height="590px"
          :serviceUrl="serviceUrl"
          :enableToolbar="true"
          :toolbarMode="'Ribbon'"
          :created="onCreated"
        >
        </ejs-documenteditorcontainer>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, provide } from 'vue';
import {
  DocumentEditorContainerComponent as EjsDocumenteditorcontainer,
  Toolbar,
  Ribbon
} from '@syncfusion/ej2-vue-documenteditor';
import documentProtectionData from './assets/data-document-protection.json';

provide('DocumentEditorContainer', [Toolbar, Ribbon]);

const serviceUrl = 'https://document.syncfusion.com/web-services/docx-editor/api/documenteditor/';

const users = [
  {
    name: 'Nancy',
    email: 'nancy@example.com',
    role: 'Price Editor',
    protectionType: 'ReadOnly',
    description: 'Can edit only the price values (editable ranges marked in the document)'
  },
  {
    name: 'Andrew',
    email: 'andrew@example.com',
    role: 'Reviewer',
    protectionType: 'RevisionsOnly',
    description: 'All edits are tracked as revisions; cannot accept or reject revisions'
  },
  {
    name: 'Steven',
    email: 'steven@example.com',
    role: 'Commenter',
    protectionType: 'CommentsOnly',
    description: 'Can insert comments only; cannot edit document content'
  },
  {
    name: 'Michael',
    email: 'michael@example.com',
    role: 'Editor',
    protectionType: 'NoProtection',
    description: 'Full editing access on the entire document'
  },
  {
    name: 'Laura',
    email: 'laura@example.com',
    role: 'Viewer',
    protectionType: 'ReadOnly',
    description: 'Read-only access to the document'
  }
];

const containerRef = ref(null);
const currentUser = ref(users[0]);
const selectedUserEmail = ref(users[0].email);

function applyUserPermissions(user) {
  const container = containerRef.value;
  if (!container || !container.ej2Instances) {
    return;
  }
  const documentEditor = container.ej2Instances.documentEditor;

  if (documentEditor.documentHelper.isDocumentProtected) {
    documentEditor.editorModule.unProtectDocument();
  }

  documentEditor.isReadOnly = false;
  documentEditor.currentUser = user.email;

  if (user.protectionType !== 'NoProtection') {
    documentEditor.editorModule.enforceProtection('', user.protectionType);
  }

  if (user.email === 'laura@example.com') {
    documentEditor.isReadOnly = true;
  }

  documentEditor.documentHelper.restrictEditingPane.showHideRestrictPane(false);
}

function onCreated() {
  const container = containerRef.value;
  if (!container || !container.ej2Instances) {
    return;
  }
  const documentEditor = container.ej2Instances.documentEditor;
  documentEditor.open(JSON.stringify(documentProtectionData));
  applyUserPermissions(currentUser.value);

  documentEditor.beforeAcceptRejectChanges = (args) => {
    if (currentUser.value.email === 'andrew@example.com') {
      args.cancel = true;
    }
  };
}

function onUserChange() {
  const selected = users.find((user) => user.email === selectedUserEmail.value);
  if (selected) {
    currentUser.value = selected;
    applyUserPermissions(selected);
  }
}
</script>

<style>
@import "../node_modules/@syncfusion/ej2-tailwind3-theme/styles/document-editor/index.css";
@import "../node_modules/@syncfusion/ej2-tailwind3-theme/styles/document-editor-container/index.css";
</style>
