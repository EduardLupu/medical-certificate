# CNAS Medical Certificate OCR App

A Streamlit app that extracts structured data (series, number, CNP, dates, diagnostic codes, checkboxes, etc.) from scanned Romanian "Certificat de Concediu Medical" documents, using PaddleOCR and OpenCV with a fixed ROI map.

## Features
- OCR tuned for the Romanian CNAS medical certificate layout (via `roi_map.json`)
- Uses PaddleOCR `TextRecognition` on top of PaddlePaddle
- Bilingual interface: English / Romana
- Upload a scan or pick a sample from the local `certificate/` folder
- Live progress bar while processing
- Preview image with green boxes over all detected ROIs (fields)
- Per-field details: extracted value, confidence score, and "Needs review" flags (low confidence, wrong length, empty, etc.)
- Export results as Excel (.xlsx), JSON, or CSV
- History panel with previews, per-certificate results, review info, and downloads of the original image plus Excel
- Optional debug mode showing intermediate ROI preprocessing layers for tuning OCR

## 1. Prerequisites
- Python 3.10+
- A machine that can run PaddlePaddle (CPU is enough)
- Install the PaddlePaddle runtime first (pick the wheel that matches your OS/CPU if needed):
  ```bash
  pip install paddlepaddle==3.2.0
  ```
- Then install PaddleOCR and the rest of the dependencies (see below for the full list).

## 2. Clone & Setup
From your terminal:
```bash
# Clone or copy project folder
cd medical-certificate

# Create virtual environment
python -m venv .venv

# Activate (Windows PowerShell)
.venv\Scripts\activate

# On macOS / Linux the activate step is usually:
# source .venv/bin/activate

# Upgrade pip (recommended)
python -m pip install --upgrade pip

# Install dependencies
pip install -r requirements.txt
```

## 3. Run the App
From the project root (with the virtual environment activated):
```bash
python -m streamlit run app.py
```

Open the URL shown in the terminal (usually http://localhost:8501).
A hosted version is available at: medical-certificate.streamlit.app

## Project Structure
```
medical-certificate/
|-- app.py                 # Streamlit front-end: UI, history, exports
|-- ocr_utils.py           # OCR + preprocessing + ROI-based field extraction
|-- roi_map.json           # ROI definitions (field name, type, relative coordinates)
|-- certificate/           # Optional sample images for quick testing
|-- .paddlex/
|   |-- official_models/
|       |-- en_PP-OCRv5_mobile_rec/   # PaddleOCR recognition model directory
|-- requirements.txt       # Dependencies
|-- README.md              # This file
```

The OCR engine uses `paddleocr.TextRecognition` with the model name `en_PP-OCRv5_mobile_rec` and the model directory:
```
.paddlex/official_models/en_PP-OCRv5_mobile_rec
```
If the model is not present, PaddleOCR may attempt to download it into that folder on first run. In offline or locked-down environments, pre-download the model and place it there.

## 4. How to Use
1. Launch the app.
2. In the sidebar, choose your language (English / Romana).
3. Upload a `.png`, `.jpg`, or `.jpeg` scan of a CNAS medical certificate, or select a sample from the `certificate/` dropdown if you have sample files there.
4. The app shows a progress bar while loading the image, aligning and warping the certificate, and running ROI-based OCR per field.
5. Main area layout:
   - Left: preview image with green rectangles and field names over each ROI.
   - Right tabs:
     - "Values / Valori": structured OCR output (table) plus download buttons.
     - "Confidences & Review / Confidenta si revizuire": table with per-field confidence, review flag, reason, type, description.
6. Download the output in your preferred format (Excel, JSON, CSV).
7. Scroll down to the History section to re-open older runs, see previews, inspect field values and review info, and redownload original images and Excel files.
8. Enable debug mode in the sidebar to see per-ROI debug images and intermediate layers under the preview.

## 5. Troubleshooting
**Paddle / dependency install issues**  
If installing packages fails, ensure pip is up to date:
```bash
python -m pip install --upgrade pip
pip install -r requirements.txt
```
On some platforms, PaddlePaddle may require a specific wheel. Consult the PaddlePaddle installation docs for your OS/CPU and adjust the install command accordingly.

**OCR fails with a message about PaddleOCR or models**  
If you see an error similar to:
> "OCR failed to run. If you are offline, download PaddleOCR model in advance or allow temporary network access."

Then:
- Make sure `paddlepaddle` and `paddleocr` are correctly installed in the active virtual environment.
- Allow the app to download the recognition model on first run, or pre-download and place it under:
  ```
  .paddlex/official_models/en_PP-OCRv5_mobile_rec
  ```

**Virtual environment problems**  
If you get errors about missing `pyvenv.cfg` or the venv being corrupted:
```powershell
Remove-Item -Recurse -Force .venv      # PowerShell on Windows
python -m venv .venv
.venv\Scripts\activate
python -m pip install --upgrade pip
pip install -r requirements.txt
```
Use `rm -rf .venv` and `source .venv/bin/activate` on macOS / Linux.

## 6. Requirements Summary
The main dependencies (see `requirements.txt` for exact versions):

| Package | Purpose |
|---------|---------|
| streamlit | Web UI framework |
| paddlepaddle | Deep learning runtime for PaddleOCR |
| paddleocr | OCR models and `TextRecognition` API |
| opencv-python-headless | Image loading, preprocessing, warping |
| pillow | Image handling via PIL |
| numpy | Array operations |
| pandas | Tabular data, exports (Excel/CSV/JSON) |
| openpyxl | Excel writer engine for pandas |

Example `requirements.txt` (current stack):
```
streamlit~=1.50.0
paddlepaddle==3.2.0
paddleocr~=3.3.2
opencv-python-headless
pillow~=11.3.0
numpy~=2.2.6
openpyxl
pandas~=2.3.3
```

## License
MIT License - free to use and modify. Intended primarily for internal automation and academic use on Romanian CNAS medical certificates.
