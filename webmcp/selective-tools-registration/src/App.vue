<template>
  <div id="app">
    <ejs-documenteditorcontainer
      ref="container"
      :serviceUrl="serviceUrl"
      :height="height"
      :width="width"
      :enableToolbar="true"
      :enableWebMcp="true"
      toolbarMode="Ribbon"
      :webMcpSettings="webMcpSettings">
    </ejs-documenteditorcontainer>
  </div>
</template>

<script setup>
import { provide, ref } from 'vue';
import {
  DocumentEditorContainerComponent as EjsDocumenteditorcontainer,
  Toolbar,
  Ribbon
} from '@syncfusion/ej2-vue-documenteditor';

const container = ref(null);
const height = '100%';
const width = '100%';
const serviceUrl =
  'https://document.syncfusion.com/web-services/docx-editor/api/documenteditor/';

const webMcpSettings = {
  name: 'container',
  // Register only the required tools
  tools: [
    'getDocumentBookmarks',
    'getSelectionText',
    'find',
    'findAll',
    'goToPage'
  ]
};


provide('DocumentEditorContainer', [Toolbar, Ribbon]);
</script>
<style>
@import "../node_modules/@syncfusion/ej2-tailwind3-theme/styles/document-editor/index.css";
@import "../node_modules/@syncfusion/ej2-tailwind3-theme/styles/document-editor-container/index.css";
</style>