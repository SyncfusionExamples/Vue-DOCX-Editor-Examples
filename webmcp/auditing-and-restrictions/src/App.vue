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
      :beforeWebMcpToolExecute="beforeWebMcpToolExecute"
      :created="created">
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

const webMcpSettings = {
  name: 'container'
};

provide('DocumentEditorContainer', [Toolbar, Ribbon]);

const created = () => {
  const documentEditorContainer = container.value.ej2Instances;

  // Set document name
  documentEditorContainer.documentEditor.documentName = 'WebMCP Tool Demo';

  // Update document title
  document.title = 'WebMCP Tool Demo - DocumentEditorContainer';
};
const beforeWebMcpToolExecute = (args)=>{
  const { toolName, toolArgs } = args;

    // Audit all WebMCP tool invocations
    console.log(`[WebMCP] Executing: ${toolName}`, toolArgs);

    const commandName = toolName?.includes('_')
        ? toolName.substring(toolName.indexOf('_') + 1)
        : toolName;

    // Restrict a specific tool
    if (commandName === 'saveDocument') {
        args.cancel = true;
        args.cancellationResponse =
            'The saveDocument operation has been restricted.';
    }
}

</script>
<style>
@import "../node_modules/@syncfusion/ej2-tailwind3-theme/styles/document-editor/index.css";
@import "../node_modules/@syncfusion/ej2-tailwind3-theme/styles/document-editor-container/index.css";
</style>