# 🚀 Rork Project Exporter

Export **Rork projects** directly from your browser into a ZIP file — no paid plan or GitHub integration required.

---

## ✨ Features

* 📁 Preserves full **folder structure**
* 📄 Extracts **all visible source code files**
* ⚡ Runs directly in the **browser console**
* 🔐 No clipboard permissions required
* 📦 Automatically generates a **ZIP file**
* 🧾 Includes `EXPORT_REPORT.txt` with export details
* ⚠️ Handles missing or unsupported files gracefully

---

## 🛠️ Usage

1. Open your Rork project
   Example: `https://rork.com/p/...`

2. Make sure the **file tree (left sidebar)** is visible

3. Open Developer Tools

   * Press `F12` or `Ctrl + Shift + I`

4. Go to the **Console** tab

5. Paste the script below ⬇️

6. Press **Enter**

7. Wait for the export to complete

8. Your ZIP file will download automatically 🎉

---

## 📜 Script

```js
(async () => {
  console.clear();
  console.log("Rork Project Exporter started");

  const sleep = (ms) => new Promise((resolve) => setTimeout(resolve, ms));

  async function loadJSZip() {
    if (window.JSZip) return;

    const script = document.createElement("script");
    script.src = "https://cdnjs.cloudflare.com/ajax/libs/jszip/3.10.1/jszip.min.js";
    document.head.appendChild(script);

    await new Promise((resolve, reject) => {
      script.onload = resolve;
      script.onerror = reject;
    });
  }

  function cleanPathName(name) {
    return (name || "unknown")
      .trim()
      .replace(/[\\:*?"<>|]/g, "_");
  }

  async function expandAllFolders() {
    while (true) {
      const closedFolders = [
        ...document.querySelectorAll(
          'button[data-radix-collection-item][data-state="closed"]'
        ),
      ];

      if (!closedFolders.length) break;

      console.log("Expanding folders:", closedFolders.length);

      for (const folder of closedFolders) {
        folder.click();
        await sleep(150);
      }

      await sleep(1000);
    }
  }

  function isFileButton(button) {
    return !!button.querySelector("svg.lucide-file");
  }

  function getItemName(button) {
    return cleanPathName(
      button.querySelector("p.truncate")?.textContent ||
        button.querySelector("span.truncate")?.textContent ||
        button.querySelector("p")?.textContent ||
        button.querySelector("span")?.textContent
    );
  }

  function getFolderPath(button) {
    const parts = [];
    let current = button;

    while (current) {
      const name = getItemName(current);
      if (name) parts.unshift(name);

      const region = current.closest('div[role="region"][id^="radix-"]');
      if (!region) break;

      current = document.querySelector(`button[aria-controls="${region.id}"]`);
    }

    if (isFileButton(button)) parts.pop();

    return parts.join("/");
  }

  function readVisibleEditorText() {
    const editor =
      document.querySelector(".cm-content") ||
      document.querySelector('div[role="textbox"]') ||
      document.querySelector("pre") ||
      document.querySelector("code");

    if (!editor) return "";

    return editor.innerText || editor.textContent || "";
  }

  async function readImageBase64() {
    const image = document.querySelector('img[src^="data:image/"]');
    if (!image) return null;

    return image.src.split(";base64,")[1] || null;
  }

  const binaryExtensions = [
    ".png",
    ".jpg",
    ".jpeg",
    ".gif",
    ".webp",
    ".ico",
    ".svg",
    ".mp4",
    ".mov",
    ".mp3",
    ".wav",
    ".pdf",
    ".zip",
    ".ttf",
    ".woff",
    ".woff2",
  ];

  const imageExtensions = [".png", ".jpg", ".jpeg", ".gif", ".webp", ".ico"];

  function getExtension(filename) {
    const index = filename.lastIndexOf(".");
    return index === -1 ? "" : filename.slice(index).toLowerCase();
  }

  try {
    await loadJSZip();

    const zip = new JSZip();
    const successful = [];
    const failed = [];

    await expandAllFolders();

    const files = [
      ...document.querySelectorAll("button[data-radix-collection-item]"),
    ].filter(isFileButton);

    console.log("Files found:", files.length);

    if (!files.length) {
      throw new Error("No files found. Open the left file tree first.");
    }

    for (let i = 0; i < files.length; i++) {
      const button = files[i];
      const fileName = getItemName(button);
      const folderPath = getFolderPath(button);
      const filePath = folderPath ? `${folderPath}/${fileName}` : fileName;
      const extension = getExtension(fileName);

      console.log(`[${i + 1}/${files.length}] Reading ${filePath}`);

      button.scrollIntoView({ block: "center" });
      button.click();

      await sleep(3500);

      if (binaryExtensions.includes(extension)) {
        const base64 = await readImageBase64();

        if (base64 && imageExtensions.includes(extension)) {
          zip.file(filePath, base64, { base64: true });
          successful.push(filePath);
        } else {
          zip.file(
            `${filePath}.EXPORT_WARNING.txt`,
            "Binary file preview was not available in the browser UI."
          );
          failed.push(filePath);
        }
      } else {
        const text = readVisibleEditorText();

        if (text.trim()) {
          zip.file(filePath, text);
          successful.push(filePath);
        } else {
          zip.file(
            `${filePath}.EMPTY_OR_FAILED.txt`,
            "Could not read this file from the visible editor."
          );
          failed.push(filePath);
        }
      }
    }

    zip.file(
      "EXPORT_REPORT.txt",
      [
        "Rork Project Export Report",
        `Date: ${new Date().toISOString()}`,
        `Files found: ${files.length}`,
        `Successful: ${successful.length}`,
        `Failed: ${failed.length}`,
        "",
        "Successful files:",
        successful.join("\n"),
        "",
        "Failed files:",
        failed.join("\n"),
      ].join("\n")
    );

    const blob = await zip.generateAsync({ type: "blob" });

    const downloadLink = document.createElement("a");
    downloadLink.href = URL.createObjectURL(blob);
    downloadLink.download = "rork_project_export.zip";
    document.body.appendChild(downloadLink);
    downloadLink.click();
    downloadLink.remove();

    alert(
      `Export complete.\nFiles: ${files.length}\nSuccessful: ${successful.length}\nFailed: ${failed.length}\n\nCheck EXPORT_REPORT.txt inside the ZIP.`
    );
  } catch (error) {
    console.error("Export failed:", error);
    alert("Export failed: " + error.message);
  }
})();
```

---

## 📦 Output

The ZIP file contains:

* All extracted files (original structure preserved)
* `EXPORT_REPORT.txt`:

  * Total files detected
  * Successfully exported files
  * Failed or skipped files

---

## ⚠️ Limitations

This tool depends on what Rork exposes in its UI:

* Some **binary files may not export correctly** (e.g. `.mp4`, `.pdf`, fonts)
* Files not visible or not loaded in the UI cannot be extracted
* UI changes in Rork may require script updates

---

## 🧠 Tips

* Keep the tab **focused** while running
* Avoid switching tabs during export
* If files fail → check `EXPORT_REPORT.txt`
* Replace missing assets manually if needed

---

## 🐛 Troubleshooting

### ❌ No files found

* Ensure the file tree is visible
* Make sure you are on a valid project page

---

### ⚠️ Empty or failed files

* File may not be fully loaded
* File may not be visible in UI
* Unsupported file types may fail

---

### ⛔ No download triggered

* Check browser download blocking
* Inspect console for errors

---

## ⚖️ Disclaimer

This tool is for **educational and personal use only**.

Respect:

* Rork’s Terms of Service
* Project ownership and licensing

---

## ❤️ Contributing

Contributions are welcome:

* Improve the script
* Fix compatibility issues
* Add support for more file types

---

## ⭐ Support

If this project helped you:

* ⭐ Star the repo
* 🔗 Share it with others

---

## 👨‍💻 Author

**Shaxdow007**
