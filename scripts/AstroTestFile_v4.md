---
  title: 'AstroTestFile.astro'
  description: 'component description'
  pubDate: 'July 29, 2024'
  author: 'Carlos P'
  ---
  
  
  
  # AstroTestFile.astro
  ## Explanation of FileDropZone Component

This code snippet is a React component that creates a file drop zone where users can drag and drop files or folders to upload them. It also allows users to select files by clicking on the drop zone.

### Libraries and Dependencies
- **React**: This code is written in React, a JavaScript library for building user interfaces.
- **@heroicons/react**: This library provides icons used in the component.

### Variables
- **allowedExtensions**: An array containing file extensions that are allowed to be uploaded.
- **maxFiles**: Maximum number of files that can be uploaded.

### State Variables
- **selectedFiles**: An array that holds the files selected by the user.
- **error**: Holds any error messages related to file selection.

### Functions
1. **handleDragOver**: Prevents the default behavior when a file is dragged over the drop zone.
2. **handleDrop**: Handles the drop event when files are dropped into the drop zone.
3. **extractFiles**: Extracts files from the dropped items and validates them against allowed extensions.
4. **traverseFileTree**: Recursively traverses a directory to extract files.
5. **isValidFile**: Checks if a file has a valid extension.
6. **handleFileSelect**: Handles file selection when files are selected using the file input.
7. **validateAndSetFiles**: Validates selected files against allowed extensions and maximum file count.

### Usage
To use the `FileDropZone` component, you can simply include `<FileDropZone />` in your React application where you want the file drop zone to appear.

```jsx
<FileDropZone />
```

When the component is rendered, users can drag and drop files or folders into the designated area, or they can click to select files. The selected files will be displayed with their names and icons. If there are any errors, such as invalid file types or exceeding the maximum file count, appropriate error messages will be shown.
  
  ## Component Code
  ```jsx
  ///jsx
  ---
import { useState } from 'react';
import { DocumentTextIcon, ArrowUpTrayIcon } from '@heroicons/react/24/outline';

const allowedExtensions = [
  ".js", ".jsx", ".ts", ".tsx", ".py", ".java", ".rb", ".php",
  ".html", ".css", ".cpp", ".c", ".go", ".rs", ".swift", ".kt",
  ".m", ".h", ".cs", ".json", ".xml", ".sh", ".yml", ".yaml",
  ".vue", ".svelte", ".qwik"
];
const maxFiles = 4;

const FileDropZone = () => {
  const [selectedFiles, setSelectedFiles] = useState([]);
  const [error, setError] = useState('');

  const handleDragOver = (e) => {
    e.preventDefault();
  };

  const handleDrop = async (e) => {
    e.preventDefault();
    const items = e.dataTransfer.items;
    const filesArray = await extractFiles(items);
    validateAndSetFiles(filesArray);
  };

  const extractFiles = async (items) => {
    const filesArray = [];
    for (let i = 0; i < items.length; i++) {
      const item = items[i].webkitGetAsEntry();
      if (item.isFile) {
        const file = await new Promise((resolve) => item.file(resolve));
        if (isValidFile(file)) {
          filesArray.push(file);
        } else {
          setError(`Invalid file type. Only ${allowedExtensions.join(", ")} files are allowed.`);
        }
      } else if (item.isDirectory) {
        await traverseFileTree(item, filesArray);
      }
    }
    return filesArray;
  };

  const traverseFileTree = (item, filesArray) => {
    return new Promise((resolve) => {
      if (item.isFile) {
        item.file((file) => {
          if (isValidFile(file)) {
            filesArray.push(file);
          }
          resolve();
        });
      } else if (item.isDirectory) {
        const dirReader = item.createReader();
        dirReader.readEntries(async (entries) => {
          for (const entry of entries) {
            await traverseFileTree(entry, filesArray);
          }
          resolve();
        });
      }
    });
  };

  const isValidFile = (file) => allowedExtensions.some((ext) => file.name.endsWith(ext));

  const handleFileSelect = (e) => {
    const files = Array.from(e.target.files);
    validateAndSetFiles(files);
  };

  const validateAndSetFiles = (files) => {
    const filteredFiles = files.filter(isValidFile);
    if (filteredFiles.length + selectedFiles.length > maxFiles) {
      setError(`You can only upload a maximum of ${maxFiles} files.`);
    } else {
      setSelectedFiles((prev) => [...prev, ...filteredFiles].slice(0, maxFiles));
      setError("");
    }
  };

  return (
    <div
      onDragOver={handleDragOver}
      onDrop={handleDrop}
      class="p-4 border-2 border-dashed border-gray-300 rounded-md text-center cursor-pointer mb-4 h-96 w-96 flex overflow-y-scroll items-center justify-center"
    >
      <input
        type="file"
        onChange={handleFileSelect}
        style={{ display: "none" }}
        accept={allowedExtensions.join(",")}
        id="fileUpload"
        multiple
      />
      <label for="fileUpload" class="cursor-pointer">
        {selectedFiles.length > 0 ? (
          <div class="flex flex-wrap gap-10">
            {selectedFiles.map((file) => (
              <div key={file.name}>
                <DocumentTextIcon class="mx-auto w-8" />
                <p>{file.name}</p>
              </div>
            ))}
          </div>
        ) : (
          <div>
            <ArrowUpTrayIcon class="mx-auto w-8" />
            <p>Drag & drop files or folders here, or click to select files</p>
          </div>
        )}
      </label>
    </div>
  );
};

export default FileDropZone;
---

<FileDropZone />
  ///
  ```
  