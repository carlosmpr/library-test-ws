---
  title: 'VueTestFile.vue'
  description: 'component description'
  pubDate: 'July 29, 2024'
  author: 'Carlos P'
  ---
  
  
  
  # VueTestFile.vue
  ## Explanation of the Code

### Purpose:
The provided code is a Vue.js component that allows users to drag and drop files or select files through a file input. It restricts the file types that can be uploaded based on the `allowedExtensions` array and limits the number of files that can be uploaded to `maxFiles`.

### Components Used:
- `DocumentTextIcon` and `ArrowUpTrayIcon` from the `@heroicons/vue/24/outline` library are used for displaying icons in the component.

### Data:
- `allowedExtensions`: An array containing the allowed file extensions that can be uploaded.
- `selectedFiles`: An array to store the files selected by the user.
- `maxFiles`: A variable to define the maximum number of files that can be uploaded.

### Methods:
1. `handleDragOver(event)`: Prevents the default behavior when an item is dragged over the drop zone.
2. `handleDrop(event)`: Handles the drop event when files are dropped into the drop zone. It extracts the files and validates them.
3. `extractFiles(items)`: Extracts files from the dropped items or directories recursively and validates each file.
4. `traverseFileTree(item, filesArray)`: Recursively traverses the file tree for directories and extracts files.
5. `isValidFile(file)`: Checks if a file has a valid extension based on the `allowedExtensions` array.
6. `handleFileSelect(event)`: Handles the file selection event when files are selected through the file input.
7. `validateAndSetFiles(files)`: Validates the selected files based on the allowed extensions and maximum file limit.

### Example Usage:
```html
<template>
  <!-- Component HTML code -->
</template>

<script>
import { DocumentTextIcon, ArrowUpTrayIcon } from "@heroicons/vue/24/outline";

export default {
  components: {
    DocumentTextIcon,
    ArrowUpTrayIcon
  },
  data() {
    // Data properties
  },
  methods: {
    // Methods
  }
};
</script>

<style scoped>
/* Component-specific styles */
</style>
```

In this code snippet, the Vue.js component defines event handlers for drag and drop functionality, file selection, and file validation. It provides a user-friendly interface for uploading files with restrictions on file types and the number of files.
  
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
  