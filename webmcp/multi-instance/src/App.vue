<template>
  <div style="display: flex; width: 100%; height: 100%;">
    <ejs-documenteditorcontainer
      ref="container1"
      :serviceUrl="serviceUrl"
      height="100%"
      width="50%"
      :enableToolbar="true"
      :enableWebMcp="true"
      toolbarMode="Ribbon"
      :webMcpSettings="webMcpSettings1">
    </ejs-documenteditorcontainer>

    <ejs-documenteditorcontainer
      ref="container2"
      :serviceUrl="serviceUrl"
      height="100%"
      width="50%"
      :enableToolbar="true"
      :enableWebMcp="true"
      toolbarMode="Ribbon"
      :webMcpSettings="webMcpSettings2">
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

const container1 = ref(null);
const container2 = ref(null);

const serviceUrl =
  'https://document.syncfusion.com/web-services/docx-editor/api/documenteditor/';

const webMcpSettings1 = {
  name: 'documentEditor1'
};

const webMcpSettings2 = {
  name: 'documentEditor2'
};

provide('DocumentEditorContainer', [Toolbar, Ribbon]);
</script>
<style>
@import "../node_modules/@syncfusion/ej2-tailwind3-theme/styles/document-editor/index.css";
@import "../node_modules/@syncfusion/ej2-tailwind3-theme/styles/document-editor-container/index.css";
</style>