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
      :webMcpSettings="webMcpSettings"
      :created="created"
      :beforeWebMcpToolExecute="beforeWebMcpToolExecute">
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

const serviceUrl =
  'https://document.syncfusion.com/web-services/docx-editor/api/documenteditor/';

const height = '100%';
const width = '100%';

const documentEditorSettings = {
  showRuler: true,
  showBookmarks: true,
  showHiddenMarks: true
};

const webMcpSettings = {
  name: 'container'
};

let webMcpTools = [];

provide('DocumentEditorContainer', [Toolbar, Ribbon]);

const created = () => {
  const documentEditorContainer = container.value.ej2Instances;

  // Get the registered WebMCP tools
  webMcpTools = documentEditorContainer.getWebMcpTools();

  // Set document name
  documentEditorContainer.documentEditor.documentName = 'WebMCP Tool Demo';

  // Update document title
  document.title = 'WebMCP Tool Demo - DocumentEditorContainer';
};

</script>
<style>
@import "../node_modules/@syncfusion/ej2-tailwind3-theme/styles/document-editor/index.css";
@import "../node_modules/@syncfusion/ej2-tailwind3-theme/styles/document-editor-container/index.css";
</style>