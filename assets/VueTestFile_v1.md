---
  title: 'VueTestFile.vue'
  description: 'component description'
  pubDate: 'July 29, 2024'
  author: 'Carlos P'
  ---
  
  
  
  # VueTestFile.vue
  ## Explanation of the Code

### Purpose
The provided code is a Vue.js component that allows users to drag and drop files or select files through a file input. It restricts the file types that can be uploaded to a predefined list of allowed file extensions. The component also limits the number of files that can be selected and displayed.

### Code Breakdown
1. **Template Section**:
   - The template section defines the HTML structure of the component.
   - It includes a container div that listens for dragover and drop events and triggers corresponding methods.
   - An input element of type file is hidden and is triggered by clicking on a label element.
   - The selected files are displayed in a list if there are any, otherwise, a message is shown to prompt the user to drag and drop files or click to select files.

2. **Script Section**:
   - The script section contains the Vue.js component logic.
   - It imports two icons from the Heroicons Vue library for use in the component.
   - The component's data object initializes variables for allowed file extensions, selected files, and the maximum number of files that can be selected.
   - Various methods are defined:
     - `handleDragOver`: Prevents the default behavior of dragover event.
     - `handleDrop`: Extracts files from the dropped items, validates them, and sets the selected files.
     - `extractFiles`: Asynchronously extracts files from dropped items or directories and validates them.
     - `traverseFileTree`: Recursively traverses a directory to extract files.
     - `isValidFile`: Checks if a file has a valid extension based on the allowed extensions.
     - `handleFileSelect`: Handles the file selection event from the input element.
     - `validateAndSetFiles`: Validates the selected files against allowed extensions and maximum file count.

3. **Style Section**:
   - The style section contains scoped CSS for component-specific styling.

### Example Usage
To use this component in a Vue.js application, you would include it in a parent component's template and handle any errors emitted by the component. Here is an example of how you might use this component:

```html
<template>
  <div>
    <FileUploader @setError="handleError" />
  </div>
</template>

<script>
import FileUploader from './FileUploader.vue';

export default {
  components: {
    FileUploader
  },
  methods: {
    handleError(errorMessage) {
      // Handle error message from FileUploader component
      console.error(errorMessage);
    }
  }
};
</script>
```

In this example, the `FileUploader` component is included in the parent component's template, and an `setError` event is listened to handle any errors emitted by the `FileUploader` component.
  
  ## Component Code
  ```jsx
  ///jsx
  <template>
    <div
      @dragover.prevent="handleDragOver"
      @drop.prevent="handleDrop"
      class="p-4 border-2 border-dashed border-gray-300 rounded-md text-center cursor-pointer mb-4 h-96 w-96 flex overflow-y-scroll items-center justify-center"
    >
      <input
        type="file"
        @change="handleFileSelect"
        style="display: none;"
        :accept="allowedExtensions.join(',')"
        id="fileUpload"
        multiple
      />
      <label for="fileUpload" class="cursor-pointer">
        <div v-if="selectedFiles.length">
          <div class="flex flex-wrap gap-10">
            <div v-for="file in selectedFiles" :key="file.name">
              <DocumentTextIcon class="mx-auto w-8" />
              <p>{{ file.name }}</p>
            </div>
          </div>
        </div>
        <div v-else>
          <ArrowUpTrayIcon class="mx-auto w-8" />
          <p>Drag & drop files or folders here, or click to select files</p>
        </div>
      </label>
    </div>
  </template>
  
  <script>
  import { DocumentTextIcon, ArrowUpTrayIcon } from "@heroicons/vue/24/outline";
  
  export default {
    components: {
      DocumentTextIcon,
      ArrowUpTrayIcon
    },
    data() {
      return {
        allowedExtensions: [
          ".js", ".jsx", ".ts", ".tsx", ".py", ".java", ".rb", ".php",
          ".html", ".css", ".cpp", ".c", ".go", ".rs", ".swift", ".kt",
          ".m", ".h", ".cs", ".json", ".xml", ".sh", ".yml", ".yaml",
          ".vue", ".svelte", ".qwik"
        ],
        selectedFiles: [],
        maxFiles: 4
      };
    },
    methods: {
      handleDragOver(event) {
        event.preventDefault();
      },
      handleDrop(event) {
        const items = event.dataTransfer.items;
        this.extractFiles(items).then(files => this.validateAndSetFiles(files));
      },
      async extractFiles(items) {
        const filesArray = [];
        for (let i = 0; i < items.length; i++) {
          const item = items[i].webkitGetAsEntry();
          if (item.isFile) {
            const file = await new Promise(resolve => item.file(resolve));
            if (this.isValidFile(file)) {
              filesArray.push(file);
            } else {
              this.$emit("setError", `Invalid file type. Only ${this.allowedExtensions.join(", ")} files are allowed.`);
            }
          } else if (item.isDirectory) {
            await this.traverseFileTree(item, filesArray);
          }
        }
        return filesArray;
      },
      traverseFileTree(item, filesArray) {
        return new Promise(resolve => {
          if (item.isFile) {
            item.file(file => {
              if (this.isValidFile(file)) {
                filesArray.push(file);
              }
              resolve();
            });
          } else if (item.isDirectory) {
            const dirReader = item.createReader();
            dirReader.readEntries(async entries => {
              for (const entry of entries) {
                await this.traverseFileTree(entry, filesArray);
              }
              resolve();
            });
          }
        });
      },
      isValidFile(file) {
        return this.allowedExtensions.some(ext => file.name.endsWith(ext));
      },
      handleFileSelect(event) {
        const files = Array.from(event.target.files);
        this.validateAndSetFiles(files);
      },
      validateAndSetFiles(files) {
        const filteredFiles = files.filter(file => this.isValidFile(file));
        if (filteredFiles.length > this.maxFiles) {
          this.$emit("setError", `You can only upload a maximum of ${this.maxFiles} files.`);
        } else {
          this.selectedFiles = filteredFiles.slice(0, this.maxFiles);
          this.$emit("setError", "");
        }
      }
    }
  };
  </script>
  
  <style scoped>
  /* Add any component-specific styles here */
  </style>
  ///
  ```
  