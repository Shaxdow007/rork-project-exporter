# 🚀 Rork Project Exporter

Export your **Rork projects** directly from the browser into a ZIP file — no paid plan, no GitHub integration required.

---

## ✨ Features

* 📁 Keeps full **folder structure**
* 📄 Extracts **all visible source code files**
* ⚡ Works directly in **browser console**
* 🔐 No clipboard permission required
* 📦 Generates a **ZIP file automatically**
* 🧾 Includes `EXPORT_REPORT.txt` with details
* ⚠️ Handles missing/binary files with warnings

---

## 📸 Demo (optional)

*Add screenshots here if you want*

---

## 🛠️ How to Use

1. Open your Rork project
   👉 Example: `https://rork.com/p/...`

2. Make sure the **file tree (left sidebar)** is visible

3. Open DevTools:

   * Press `F12` or `Ctrl + Shift + I`

4. Go to the **Console** tab

5. Paste the script:

   ```js
   /**
 * Rork Project Exporter - No Clipboard Version
 *
 * Exports visible Rork project files into a ZIP while keeping folder structure.
 *
 * Usage:
 * 1. Open your Rork project page.
 * 2. Make sure the left file tree is visible.
 * 3. Open DevTools > Console.
 * 4. Paste this script and press Enter.
 *
 * Note:
 * This script only exports files visible/readable in the browser UI.
 * Binary files may fail if Rork does not expose a preview.
 */

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

6. Press **Enter**

7. Wait for the process to finish ⏳

8. A ZIP file will automatically download 🎉

---

## 📦 Output

The ZIP contains:

* All extracted files (same structure as Rork)
* `EXPORT_REPORT.txt`:

  * Total files found
  * Successful exports
  * Failed files (if any)

---

## ⚠️ Limitations

This tool depends on what Rork exposes in the UI:

* ❌ Some binary files may fail:

  * Videos (`.mp4`)
  * PDFs
  * Fonts
* ❌ Hidden or unloaded files cannot be exported
* ❌ If Rork changes UI, script may need update

---

## 🧠 Tips

* Keep the tab **focused** while running
* Do not switch tabs during export
* If some files fail → check `EXPORT_REPORT.txt`
* You can manually replace missing assets later

---

## 🐛 Troubleshooting

### No files found

👉 Make sure:

* File tree is visible
* You are on the project page

---

### Empty or failed files

👉 Usually caused by:

* UI not fully loaded
* File not visible
* Unsupported file type

---

### No download

👉 Check:

* Browser popup blocking
* DevTools console for errors

---

## ⚖️ Disclaimer

This tool is for **educational and personal use only**.

Make sure you respect:

* Rork’s Terms of Service
* Project ownership and licenses

---

## ❤️ Contributing

Feel free to:

* Improve the script
* Fix compatibility issues
* Add support for more file types

---

## ⭐ Support

If this helped you:

* ⭐ Star the repo
* 🧑‍💻 Share with others

---

## 👨‍💻 Author

Built by shaxdow007 — improved with community 💡
