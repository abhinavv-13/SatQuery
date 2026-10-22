SatQuery AI

An interactive vision-language system for analyzing multimodal remote-sensing imagery through natural-language queries.

SatQuery AI routes text queries and satellite imagery (GeoTIFF/TIFF, PNG, JPEG) to domain-adapted remote-sensing models. Instead of relying on a single generic vision-language model, the backend validates image metadata, classifies the task intent, runs the appropriate specialist pipeline (single-image VQA, region grounding, bi-temporal change detection, or optical-SAR fusion), and returns grounded answers with visual overlays and an execution trace.

Features

Natural-Language Query Interface: Query optical, multispectral, and SAR satellite images without manual GIS preprocessing.

Task Routing & Orchestration: Validates image count, format, coordinate reference system (CRS), and modality before selecting specialist models from the registry.

Single-Image Analysis:

Visual Question Answering (VQA)

Scene captioning and land-cover description

Text-guided region grounding (bounding boxes and segmentation masks)

Bi-Temporal Change Analysis: Processes co-registered multi-date image pairs to generate spatial change masks, quantify land-cover shifts, and answer change-related queries (CDVQA).

Optical-SAR Cross-Modal Fusion: Extracts combined structural and spectral information from co-registered optical/multispectral and Synthetic Aperture Radar (SAR) pairs.

Execution Trace & Visual Evidence: Returns model confidence scores, execution time, applied parameters, and toggleable map layers (Optical, SAR, NDVI, Change Map).

Architecture

User Input (Satellite Images + Text Query)
       │
       ▼
┌─────────────────────────────────────────────────────────┐
│ 1. Input & Metadata Validation                          │
│    - Image Information Collector (CRS, bands, stats)    │
│    - Query Intent Detection                             │
│    - Task & Modality Identification                     │
└──────────────────────────┬──────────────────────────────┘
                           │
                           ▼
┌─────────────────────────────────────────────────────────┐
│ 2. AI Orchestrator & Model Registry                     │
│    - Validates compatibility & selects workflow         │
│    - Routes to Chat Layer or Specialist Pipeline        │
└──────┬───────────────────┬───────────────────────┬──────┘
       │                   │                       │
       ▼                   ▼                       ▼
┌──────────────┐   ┌───────────────┐   ┌──────────────────┐
│ Single-Image │   │ Bi-Temporal   │   │ Cross-Modal      │
│ Pipeline     │   │ Pipeline      │   │ Pipeline         │
│ - VQA        │   │ - Co-reg      │   │ - Optical + SAR  │
│ - Captioning │   │ - Change Map  │   │   Fusion         │
│ - Grounding  │   │ - CDVQA       │   │ - Feature Extr.  │
└──────┬───────┘   └───────┬───────┘   └─────────┬────────┘
       │                   │                     │
       └───────────────────┼─────────────────────┘
                           │
                           ▼
┌─────────────────────────────────────────────────────────┐
│ 3. Result Fusion & Output Generation                    │
│    - Text explanation + confidence score                │
│    - Visual overlays (boxes, masks, change maps)        │
│    - Auditable execution summary (JSON + Web UI)        │
└─────────────────────────────────────────────────────────┘


Core Components

Component

Description

Input Validator

Checks file format (GeoTIFF/TIFF), band count, resolution, and spatial co-registration across paired inputs.

Intent Classifier

Parses the user prompt to determine whether the request requires general chat, single-image VQA/grounding, change detection, or multimodal fusion.

Model Registry

Modular configuration allowing specialist weights and pipelines to be added or swapped without changing core routing logic.

Specialist Pipelines

Fine-tuned remote-sensing models adapted on BigEarthNet and evaluated across VRSBench, RSVQA, CDVQA, and SEN1-2.

Result Fusion

Combines spatial outputs (bounding boxes, segmentation masks) with natural-language responses and logs the execution trace.

Supported Inputs & Tasks

Input Type

Modalities

Supported Tasks

Example Queries

Single Image

Optical, Multispectral, or SAR

VQA, Scene Captioning, Region Grounding

"Describe the land-cover and major objects visible in this image."



"Highlight the water body referred to in the query."

Bi-Temporal Pair

Two co-registered images ($t_1, t_2$)

Change Detection, Change Description, CDVQA

"What changed between these two dates, and where did the change occur?"



"Has the built-up area increased, decreased, or remained unchanged?"

Cross-Modal Pair

Co-registered Optical + SAR pair

Joint Feature Extraction, Flood/Water & Built-Up Mapping

"Use the optical and SAR images together to identify built-up and water-covered regions."

Tech Stack

Frontend: React.js, Tailwind CSS, JavaScript

Backend: Python, FastAPI, REST API

AI / ML: PyTorch, Hugging Face Transformers, Together AI (LLM orchestration)

Image & Geospatial Processing: OpenCV, Pillow, NumPy, Rasterio / GDAL

Datasets & Benchmarks:

BigEarthNet (Multisensor Sentinel-1 SAR & Sentinel-2 Optical adaptation)

VRSBench & RSVQA (Single-image VQA, captioning, and grounding)

CDVQA & SECOND (Bi-temporal change question answering and segmentation)

SEN1-2 (Optical-SAR fusion)

Comparison with Existing Tools

Platform / Model

Natural-Language Chat

VQA / Grounding

Change Analysis

Optical + SAR

Unified Workflow

Google Earth Engine

No

No

Yes

Yes

No

ArcGIS Pro

Partial

Partial

Yes

Yes

No

Sentinel Hub / EO Browser

No

No

Partial

Yes

No

QGIS + Plugins

No

Partial

Yes

Partial

No

GeoChat

Yes

Yes

Partial

No

No

RS-ChatGPT / ChatEarthNet

Yes

Partial

No

No

No

ChangeChat

Partial

No

Yes

No

No

Prithvi / AlphaEarth

No

No

Partial

No

No

Chat2Geo

Yes

Partial

Partial

Partial

Partial

SatQuery AI

Yes

Yes

Yes

Yes

Yes

Project Structure

satquery-ai/
├── backend/
│   ├── app/
│   │   ├── main.py                    # FastAPI entrypoint
│   │   ├── config.py                  # Environment & registry settings
│   │   ├── api/
│   │   │   ├── routes_query.py        # Query & analysis endpoints
│   │   │   ├── routes_upload.py       # Image upload & metadata validation
│   │   │   └── routes_reports.py      # Report export
│   │   ├── orchestrator/
│   │   │   ├── intent_classifier.py   # Task & query intent classification
│   │   │   ├── input_validator.py     # Format, CRS, and co-registration checks
│   │   │   ├── router.py              # Workflow planner & model selector
│   │   │   └── fusion.py              # Output integration & confidence calculation
│   │   ├── specialists/
│   │   │   ├── registry.py            # Model Registry
│   │   │   ├── vqa_grounding.py       # Single-image VQA, captioning & grounding
│   │   │   ├── change_analysis.py     # Bi-temporal CDVQA & change mask generation
│   │   │   └── optical_sar_fusion.py  # Optical-SAR fusion pipeline
│   │   └── preprocessing/
│   │       ├── geotiff_loader.py      # GeoTIFF band normalization
│   │       └── indices.py             # NDVI, NDWI & SAR preprocessing
│   ├── weights/                       # Model checkpoints & adapters
│   └── requirements.txt
├── frontend/
│   ├── public/
│   ├── src/
│   │   ├── components/
│   │   │   ├── MapCanvas.jsx          # Image viewer & overlay renderer
│   │   │   ├── LayerController.jsx    # Optical, SAR, NDVI & Change Map toggles
│   │   │   ├── QueryConsole.jsx       # Query input & presets
│   │   │   ├── ExecutionTracePanel.jsx# Confidence & execution trace panel
│   │   │   └── Sidebar.jsx            # Session history & sample loader
│   │   ├── App.jsx
│   │   └── main.jsx
│   ├── package.json
│   └── tailwind.config.js
├── scripts/
│   ├── finetune_bigearthnet.py        # Domain adaptation script
│   └── evaluate_benchmarks.py         # Benchmark evaluation (VRSBench, RSVQA, CDVQA)
└── README.md


Setup & Installation

Prerequisites

Python 3.10+

Node.js 18+ and npm

CUDA-enabled GPU (optional, recommended for local inference)

1. Clone the Repository

git clone https://github.com/your-username/satquery-ai.git
cd satquery-ai


2. Backend Setup

cd backend
python -m venv venv

# Windows
venv\Scripts\activate
# Linux / macOS
source venv/bin/activate

pip install -r requirements.txt


Create a .env file in backend/:

TOGETHER_API_KEY=your_api_key_here
ORCHESTRATOR_MODEL=meta-llama/Llama-3-70b-chat-hf
DEVICE=cuda
MODEL_REGISTRY_PATH=./weights/registry_config.json


Start the FastAPI server:

uvicorn app.main:app --host 0.0.0.0 --port 8000 --reload


3. Frontend Setup

cd frontend
npm install
npm run dev


The web interface will be available at http://localhost:5173.

API Usage & Execution Trace

Request (POST /api/v1/analyze)

curl -X POST "http://localhost:8000/api/v1/analyze" \
  -F "query=What changed between these two dates, and where did the change occur?" \
  -F "image_t1=@./samples/kosi_river_2024_pre.tif" \
  -F "image_t2=@./samples/kosi_river_2024_post.tif"


Response

{
  "status": "success",
  "query": "What changed between these two dates, and where did the change occur?",
  "answer": "The scene shows the Kosi River basin with surrounding agricultural land. Significant flooding is visible in the northern sectors, with a 34% increase in surface water coverage. A distinct built-up settlement is grounded in the northeast quadrant. SAR cross-validation is recommended for deeper subsidence analysis.",
  "confidence": 0.92,
  "visual_evidence": {
    "change_map_url": "/outputs/masks/kosi_flood_change_mask.png",
    "bounding_boxes": [
      {
        "label": "flooded_water_expansion",
        "bbox": [312, 240, 645, 590],
        "confidence": 0.94
      },
      {
        "label": "built_up_settlement",
        "bbox": [520, 310, 610, 405],
        "confidence": 0.89
      }
    ],
    "available_layers": ["Optical", "SAR", "NDVI", "Change Map"]
  },
  "execution_trace": {
    "selected_task": "bi_temporal_change_vqa_and_grounding",
    "input_validation": {
      "image_count": 2,
      "format": "GeoTIFF",
      "crs": "EPSG:32645",
      "co_registered": true
    },
    "models_used": [
      {
        "task": "vqa_grounding",
        "model": "RSVQA-LLaVA",
        "parameters": { "temperature": 0.1, "box_threshold": 0.35 }
      },
      {
        "task": "change_detection",
        "model": "CDVQA",
        "parameters": { "change_threshold": 0.5 }
      }
    ],
    "execution_time_sec": 2.3,
    "steps": 4
  }
}


References

BigEarthNet: https://huggingface.co/datasets/bigearthnet

VRSBench: https://github.com/Chen-Delin/VRSBench

RSVQA: https://ieeexplore.ieee.org/document/9172634

CDVQA / SECOND: https://arxiv.org/abs/2305.16318

SEN1-2: https://github.com/leonardobeno/SEN1-2

GeoChat: https://arxiv.org/abs/2311.15826

ChangeChat: https://github.com/ChangeChat/ChangeChat

DOTA: https://captain-whu.github.io/DOTA/

License

MIT License
