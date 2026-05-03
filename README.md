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
   // paste rork-exporter.js here
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
