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
:contentReference[oaicite:0]{index=0}
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
