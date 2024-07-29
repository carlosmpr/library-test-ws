---
  title: 'VueTestFile.vue'
  description: 'component description'
  pubDate: 'July 29, 2024'
  author: 'Carlos P'
  ---
  
  
  
  # VueTestFile.vue
  ## Explanation of the Code

### Purpose:
The provided code is a Vue.js component that allows users to drag and drop files or select files through a file input. It restricts the file types that can be uploaded and limits the number of files that can be selected. The component displays the selected files with their names and icons.

### Code Breakdown:
1. **Template Section**:
   - The template section defines the HTML structure of the component.
   - It includes a div element that acts as a drop zone for files.
   - An input element of type file is hidden and triggered by a label for file selection.
   - The selected files are displayed with their names and icons if any files are chosen.

2. **Script Section**:
   - The script section contains the Vue component logic.
   - It imports two icons from the `@heroicons/vue` library for displaying in the component.
   - The component's data includes allowed file extensions, selected files array, and the maximum number of files that can be selected.
   - Various methods handle drag over, file drop, file selection, file validation, and setting selected files.
   - The `extractFiles` method extracts files from the dropped items and validates them.
   - The `traverseFileTree` method recursively traverses through directories to extract files.
   - The `isValidFile` method checks if a file has a valid extension.
   - The `handleFileSelect` method is triggered when files are selected through the file input.
   - The `validateAndSetFiles` method validates the selected files and sets them in the component.

3. **Style Section**:
   - The style section contains scoped CSS for the component.

### Example Usage:
```html
<template>
  <FileUploader @setError="handleError" />
</template>

<script>
import FileUploader from './FileUploader.vue';

export default {
  components: {
    FileUploader
  },
  methods: {
    handleError(error) {
      console.error(error);
    }
  }
};
</script>
```

### External Libraries:
- The code uses the `@heroicons/vue` library to import icons for display in the component.
  
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
  