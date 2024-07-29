---
  title: 'VueTestFile.vue'
  description: 'component description'
  pubDate: 'July 29, 2024'
  author: 'Carlos P'
  ---
  
  
  
  # VueTestFile.vue
  ## Explanation of the Code

### Purpose
The provided code is a Vue.js component that allows users to drag and drop files or select files using a file input. It restricts the file types that can be uploaded to a predefined list of allowed file extensions and limits the number of files that can be uploaded. The component displays the selected files with their names and icons.

### Code Breakdown
- The component template consists of a `div` element that serves as the drop zone for files. Inside this `div`, there is an `input` element of type file that is hidden and triggered when the user clicks on the drop zone. The selected files are displayed with their names and icons.
- The component uses two icons imported from the `@heroicons/vue` library: `DocumentTextIcon` and `ArrowUpTrayIcon`.
- The component's data contains an array of allowed file extensions, an array to store selected files, and a maximum number of files that can be uploaded.
- The component defines several methods:
  - `handleDragOver`: Prevents the default behavior when dragging files over the drop zone.
  - `handleDrop`: Handles the drop event, extracts files from the event data, and validates them.
  - `extractFiles`: Extracts files from the dropped items, validates them, and returns an array of valid files.
  - `traverseFileTree`: Recursively traverses a file tree to extract files from directories.
  - `isValidFile`: Checks if a file has a valid extension based on the allowed extensions.
  - `handleFileSelect`: Handles the file selection event and validates the selected files.
  - `validateAndSetFiles`: Validates the selected files, limits the number of files, and updates the `selectedFiles` array.

### Example Usage
```html
<template>
  <FileUploader @setError="handleError" />
</template>

<script>
import FileUploader from './FileUploader.vue';

export default {
  components: {
    FileUploader,
  },
  methods: {
    handleError(message) {
      // Handle error message
      console.error(message);
    },
  },
};
</script>
```

In this example, the `FileUploader` component is used within a parent component. The parent component listens for the `setError` event emitted by `FileUploader` to handle any error messages that occur during file selection.
  
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
  