<template>
  <div class="control-section" style="width: 100%;">
    <!-- Shared container -->
    <div style="width: 760px; margin: 10px auto">
      <!-- Upload row -->
      <div style="display: flex; gap: 6px">
        <!-- Original file section-->
        <div style="width: 360px; border-radius: 6px; padding: 14px; border: 1px dashed #ccc">
          <!-- Top row -->
          <div style="display: flex; align-items: center; gap: 10px;">
            <span>Original document</span>
            <label style=" padding: 4px 10px;
              border: 1px solid #ccc;
              border-radius: 4px;
              cursor: pointer;">
              Choose file
              <input type="file"
                style="display: none;"
                accept=".docx,.sfdt"
                @change="onOriginalFileChange" />
            </label>
          </div>
          <!-- Filename + Delete icon -->
          <div style="display: flex; justify-content: space-between; align-items: center; margin-top: 6px; font-size: 12px; color: #666;">
            <span>{{ getOriginalFileName() }}</span>
            <span
              v-if="originalFile"
              class="e-icons e-trash"
              @click="removeOriginalFile"
              style="cursor: pointer;"
              title="Remove file">
            </span>
          </div>
        </div>
        <!-- Revised file section-->
        <div style="width: 360px; border-radius: 6px; padding: 14px; border: 1px dashed #ccc;">
          <!-- Top row -->
          <div style="display: flex; align-items: center; gap: 10px;">
            <span>Revised document</span>
            <label style="
              padding: 4px 10px;
              border: 1px solid #ccc;
              border-radius: 4px;
              cursor: pointer;">
              Choose file
              <input type="file"
                style="display: none;"
                accept=".docx,.sfdt"
                @change ="onRevisedFileChange" />
            </label>
          </div>
          <!-- Filename + Delete icon -->
          <div style="display: flex; justify-content: space-between; align-items: center; margin-top: 6px; font-size: 12px; color: #666;">
            <span>{{ getRevisedFileName() }}</span>
            <span
              v-if="revisedFile"
              class="e-icons e-trash"
              @click="removeRevisedFile"
              style="cursor: pointer;"
              title="Remove file">
            </span>
          </div>
        </div>
      </div>
      <!-- Checkbox, Compare & Download buttons  -->
      <div style="display: flex; justify-content: space-between; align-items: center; margin-top: 12px;">
        <!-- Checkbox -->
        <div>
          <label style="cursor: pointer;">
            <input
              type="checkbox"
              :checked="showResult"
              @change="onShowResultChange"
              style="margin-right: 6px;" />
            <span>Show comparison results with tracked changes</span>
          </label>
        </div>
        <!-- Buttons -->
        <div style="display: flex; gap: 10px; margin-right: 32px;">
          <ejs-button
            class="e-btn e-primary"
            :disabled="!originalFile || !revisedFile"
            @click="onCompare">
            Compare
          </ejs-button>
          <ejs-button
            class="e-btn e-outline e-flat e-primary"
            iconCss="e-icons e-download"
            :disabled="!compareClicked"
            @click="onDownload">
            Dowload Result Document
          </ejs-button>
        </div>
      </div>
    </div>

    <!-- Editors Area -->
    <div style="display: flex; gap: 6px; height: calc(100vh - 160px); padding: 0px 10px;">
      <!-- Left Container -->
      <div style="width: 50%; display: flex; flex-direction: column; border: 1px solid #ddd; overflow: hidden;">
        <div style="padding: 8px; font-weight: 600;" class="e-de-ctn-title">Original Document</div>
        <div style="flex: 1px; display:block;">
          <ejs-documenteditorcontainer
            id="editor1"
            ref="editor1"
            :serviceUrl="serviceUrl"
            height="100%"
            width="100%"
            :enableToolbar="false"
            :showPropertiesPane="false">
          </ejs-documenteditorcontainer>
        </div>
      </div>
      <!-- Right Editor -->
      <div style="width: 50%; display: flex; flex-direction: column; border: 1px solid #ddd; overflow: hidden;">
        <div class="e-de-ctn-title" style="display: flex; justify-content: space-between; align-items: center">
          <div style="float: left; padding: 8px; font-weight: 600; flex: 1px;">
            {{
              showResult
                ? 'Result Document (with tracked changes)'
                : 'Revised Document'
            }}
          </div>
          <ejs-button
            v-if="showResult"
            class="e-de-ctn-title"
            iconCss="e-icons e-eye"
            style="float: right; font-weight: 400; background: transparent; box-shadow: none; border-color: transparent; border-radius: 0px;
                  color: inherit; font-size: 12px;"
            title="Show/Hide Revisions Pane"
            @click="onToggleRevisionsPane">
            {{showRevisions ? ' Hide Review Pane' : ' Show Review Pane'}}
          </ejs-button>
        </div>
        <div style="flex: 1px; display:block;">
          <ejs-documenteditorcontainer
            id="editor2"
            ref="editor2"
            :serviceUrl="serviceUrl"
            height="100%"
            width="100%"
            :enableToolbar="false"
            :showPropertiesPane="false">
          </ejs-documenteditorcontainer>
        </div>
      </div>
    </div>

    <!-- Waiting Popup Overlay -->
    <div class="overlay" id="popup-overlay" style="display: none;"></div>
    <div id='waiting-popup' style="display: none;">
      <svg class="circular" height="40" width="40">
        <circle class="circle-path" cx="25" cy="25" r="20" fill="none" stroke-width="6" stroke-miterlimit="10" />
      </svg>
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent } from 'vue';
import { DocumentEditorContainerComponent } from '@syncfusion/ej2-vue-documenteditor';
import { ButtonComponent } from '@syncfusion/ej2-vue-buttons';
import { originalDocument, revisedDocument } from '../data';

export default defineComponent({
  name: 'CompareDocuments',
  components: {
    'ejs-documenteditorcontainer': DocumentEditorContainerComponent,
    'ejs-button': ButtonComponent
  },
  data() {
    return {
      originalFile: null as File | null,
      revisedFile: null as File | null,
      showResult: true,
      compareClicked: false,
      showRevisions: false,
      serviceUrl: 'https://document.syncfusion.com/web-services/docx-editor/api/documenteditor/'
    };
  },
  mounted() {
    this.$nextTick(() => {
      // Set up synced scrolling between editors
      const editor1 = (this.$refs.editor1 as any).ej2Instances.documentEditor;
      const editor2 = (this.$refs.editor2 as any).ej2Instances.documentEditor;

      if (editor1 && editor2) {
        editor1.showRevisions = false;
        editor1.open(JSON.stringify(originalDocument));
        editor2.showRevisions = false;
        editor2.open(JSON.stringify(revisedDocument));
        editor1.viewChange = () => {
          const pos = editor1.selection.getScrollPosition();
          editor2.selection.setScrollPosition(pos);
        };

        editor2.viewChange = () => {
          const pos = editor2.selection.getScrollPosition();
          editor1.selection.setScrollPosition(pos);
        };
      }
    });
  },
  methods: {
    onOriginalFileChange(e: Event) {
      const target = e.target as HTMLInputElement;
      this.originalFile = target.files ? target.files[0] : null;
    },

    onRevisedFileChange(e: Event) {
      const target = e.target as HTMLInputElement;
      this.revisedFile = target.files ? target.files[0] : null;
    },

    isSupportedFormatType(formatType: string) {
      switch (formatType) {
        case '.docx':
        case '.sfdt':
          return true;
        default:
          return false;
      }
    },

    async openFileInEditor(file: File, editorRef: any) {
      // Using slice instead of substr (which is deprecated)
      const formatType = file.name.slice(file.name.lastIndexOf('.')).toLowerCase();

      if (formatType === '.sfdt') {
        const reader = new FileReader();
        reader.onload = e => {
          const docData = (e.target as FileReader).result;
          if (docData && editorRef.ej2Instances.documentEditor) {
            editorRef.ej2Instances.documentEditor.showRevisions = false;
            editorRef.ej2Instances.documentEditor.open(docData as string);
          }
        };
        reader.readAsText(file);
      }
      else if (this.isSupportedFormatType(formatType)) {
        const formData = new FormData();
        formData.append('file', file);

        try {
          const response = await fetch(this.serviceUrl + 'Import', {
            method: 'POST',
            body: formData
          });

          const sfdtString = await response.text();
          let sfdtObject: object | null = null;

          try {
            sfdtObject = JSON.parse(sfdtString);
          } catch (e) {
            alert("Unable to display the compared document. Please try again.");
          }

          if (sfdtObject && editorRef.ej2Instances.documentEditor) {
            editorRef.ej2Instances.documentEditor.showRevisions = false;
            editorRef.ej2Instances.documentEditor.open(JSON.stringify(sfdtObject));
          } else {
            alert("Unable to display the compared document. Please try again.");
          }
        } catch (e) {
          alert('Failed to import document file. Please check if the server supports this format.');
        }
      }
      else {
        alert('This Compare Documents demo supports only DOCX and SFDT file formats. Please select a valid DOCX or SFDT document and try again.');
      }
    },

    async loadComparedDocumentAndOpen(editorRef: any, originalFile: File, revisedFile: File) {
      const formData = new FormData();
      formData.append("originalFile", originalFile);
      formData.append("revisedFile", revisedFile);
      formData.append("author", "Author");
      formData.append("dateTime", new Date().toISOString());
      try {
        const response = await fetch(this.serviceUrl + 'CompareDocuments', {
          method: 'POST',
          body: formData
        });

        if (!response.ok) {
          alert("Failed to compare the selected documents. Please try again.");
          return;
        }

        const sfdtString = await response.text();
        let sfdtObject: object | null = null;

        try {
          sfdtObject = JSON.parse(sfdtString);
        } catch {
          alert("The comparison could not be completed due to no valid JSON. Please try again.");
          return;
        }

        if (sfdtObject && editorRef.ej2Instances.documentEditor) {
          editorRef.ej2Instances.documentEditor.showRevisions = false;
          editorRef.ej2Instances.documentEditor.open(JSON.stringify(sfdtObject));
        } else {
          alert("Unable to display the comparison result. Please try again.");
        }
      } catch (e) {
        alert('Error comparing documents: ' + e);
      }
      this.showHideWaitingIndicator(false);
    },

    onShowResultChange(e: Event) {
      this.showResult = (e.target as HTMLInputElement).checked;
    },

    async onCompare() {
      this.compareClicked = false;

      setTimeout(async () => {
        if (this.originalFile && this.revisedFile) {
          const originalFileFormatType = this.originalFile.name.slice(this.originalFile.name.lastIndexOf('.')).toLowerCase();
          const revisedFileFormatType = this.revisedFile.name.slice(this.revisedFile.name.lastIndexOf('.')).toLowerCase();
          if (this.isSupportedFormatType(originalFileFormatType) && this.isSupportedFormatType(revisedFileFormatType)) {
            this.compareClicked = true;
            this.showRevisions = false;
            this.showHideWaitingIndicator(true);
            if (this.showResult) {
              if (this.originalFile) await this.openFileInEditor(this.originalFile, this.$refs.editor1 as any);
              if (this.originalFile && this.revisedFile) {
                await this.loadComparedDocumentAndOpen(this.$refs.editor2 as any, this.originalFile, this.revisedFile);
              }
            } else {
              if (this.originalFile) await this.openFileInEditor(this.originalFile, this.$refs.editor1 as any);
              if (this.revisedFile) await this.openFileInEditor(this.revisedFile, this.$refs.editor2 as any);
            }
            this.showHideWaitingIndicator(false);
          }
          else {
            alert('This Compare Documents demo supports only DOCX and SFDT file formats. Please select a valid DOCX or SFDT document and try again.');
          }
        }
        else {
          alert('This Compare Documents demo supports only DOCX and SFDT file formats. Please select a valid DOCX or SFDT document and try again.');
        }
      }, 0);
    },

    onDownload() {
      const editor2 = (this.$refs.editor2 as any).ej2Instances.documentEditor;
      if (editor2) {
        editor2.save('Result', 'Docx');
      }
    },

    getOriginalFileName() {
      if (!this.originalFile) {
        return 'Supported formats: SFDT, DOCX';
      }

      const size = Math.round(this.originalFile.size / 1024);
      return `${this.originalFile.name} (${size} KB)`;
    },

    getRevisedFileName() {
      if (!this.revisedFile) {
        return 'Supported formats: SFDT, DOCX';
      }

      const size = Math.round(this.revisedFile.size / 1024);
      return `${this.revisedFile.name} (${size} KB)`;
    },

    removeOriginalFile() {
      this.originalFile = null;
      this.compareClicked = false;
    },

    removeRevisedFile() {
      this.revisedFile = null;
      this.compareClicked = false;
    },

    showHideWaitingIndicator(show: boolean) {
      const waitingPopUp = document.getElementById('waiting-popup');
      const inActiveDiv = document.getElementById('popup-overlay');
      if (waitingPopUp && inActiveDiv) {
        inActiveDiv.style.display = show ? 'block' : 'none';
        waitingPopUp.style.display = show ? 'block' : 'none';
      }
    },

    onToggleRevisionsPane() {
      this.showRevisions = !this.showRevisions;
      const editor2 = (this.$refs.editor2 as any).ej2Instances.documentEditor;
      if (editor2) {
        editor2.showRevisions = this.showRevisions;
      }
    }

  }
});
</script>

<style>
.overlay {
    position: fixed;
    top: 0;
    left: 0;
    width: 100vw;
    height: 100vh;
    background-color: rgba(56, 56, 56, 0.5);
    z-index: 9999;
    display: none;
    cursor: not-allowed;
}

#waiting-popup {
    width: 56px;
    height: 56px;
    position: fixed;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    border-radius: 50%;
    padding: 3px;
    background: white;
    box-shadow: 0px 3px 6px rgba(0,0,0,.3);
    z-index: 10000;
    display: none;
}


.circular {
    animation: rotate 2s linear infinite;
    height: 50px;
    width: 50px;
    border-radius: 50%;
}

.circle-path {
    stroke-dasharray: 1, 200;
    stroke-dashoffset: 0;
    animation: dash 1.5s ease-in-out infinite, color 6s ease-in-out infinite;
    stroke-linecap: round;
    stroke: #ff4081;
}

@keyframes rotate {
    100% {
        transform: rotate(360deg);
    }
}

@keyframes dash {
    0% {
        stroke-dasharray: 1, 200;
        stroke-dashoffset: 0;
    }

    50% {
        stroke-dasharray: 89, 200;
        stroke-dashoffset: -35;
    }

    100% {
        stroke-dasharray: 89, 200;
        stroke-dashoffset: -124;
    }
}
</style>
