# verityVault

# verityVault

The training and microservices backbone behind [VerityVaultProject](https://github.com/MNderi/VerityVaultProject) — a curriculum learning pipeline for detecting logical fallacies in text using fine-tuned BERT models.

## About

This repository contains everything needed to train, export, and serve the fallacy detection models used by VerityVault. It includes Jupyter notebooks for model training, Python microservices for inference and tokenization, a data augmentation pipeline, and a frontend for interacting with the system.

## Repository Structure

```
verityVault/
├── coarseClassifier/        # Microservice for coarse-grained fallacy classification
├── fineClassifier/          # Microservice for fine-grained fallacy classification
├── criticalQuestions/       # Microservice for generating critical questions
├── tokenizeService/         # BERT tokenization microservice (REST API)
├── public/                  # Frontend interface
├── CoarseClass.ipynb        # Training notebook — coarse classifier
├── FineClassification.ipynb # Training notebook — fine classifier
├── SquenceTrain.ipynb       # Training notebook — sequence/curriculum training
├── explore.ipynb            # Data exploration and analysis
├── Augmentation.py          # Data augmentation script
├── apigenerator.py          # API generation utilities
├── data.csv                 # Fallacy dataset
├── index.js                 # Main entry point
└── package.json
```

## Microservices

The system is split into four independent microservices:

**Tokenization Service** (`tokenizeService/`)
Exposes a REST endpoint (`POST /tokenize`) that converts raw text into BERT-compatible token IDs, attention masks, and token type IDs. All classifier microservices call this service before running inference.

**Coarse Classifier** (`coarseClassifier/`)
Classifies text into one of three broad fallacy categories: fallacy of relevance, component fallacy, or equivocation. Runs inference using an exported ONNX model.

**Fine Classifier** (`fineClassifier/`)
Takes the coarse category as context and predicts the specific fallacy type within that category. Also runs via ONNX.

**Critical Questions** (`criticalQuestions/`)
Generates Socratic-style critical questions based on the detected fallacy, helping users interrogate and challenge the argument themselves.

## Training Pipeline

Training is done in three Jupyter notebooks using curriculum learning — a strategy where the model is first trained on simpler, clear-cut examples before being exposed to harder, more ambiguous ones:

1. **`explore.ipynb`** — Data exploration, class distribution analysis, and preprocessing
2. **`CoarseClass.ipynb`** — Fine-tunes BERT for coarse-grained fallacy classification
3. **`FineClassification.ipynb`** — Fine-tunes BERT for fine-grained classification within each coarse category
4. **`SquenceTrain.ipynb`** — Implements the curriculum learning sequence, ordering training examples by difficulty

Models are exported to ONNX format after training for efficient inference in the Node.js runtime.

## Data

`data.csv` contains the labelled fallacy dataset used for training. `Augmentation.py` was used to expand the dataset by generating additional training examples to address class imbalance.

## Built With

- **Python** — Training, augmentation, and tokenization service
- **Jupyter Notebooks** — Model training and experimentation
- **BERT** — Pre-trained transformer model, fine-tuned for fallacy detection
- **ONNX** — Model export format for cross-runtime inference
- **Node.js** — Microservice orchestration
- **Curriculum Learning** — Progressive training strategy for improved generalisation

## Getting Started

### 1. Start the tokenization service
```bash
cd tokenizeService
pip install -r requirements.txt
python app.py
```

### 2. Start the classifier microservices
```bash
cd coarseClassifier && npm install && node index.js
cd fineClassifier && npm install && node index.js
cd criticalQuestions && npm install && node index.js
```

### 3. Run the main app
```bash
npm install
node index.js
```

### Training (optional)
Open the notebooks in order using Jupyter:
```bash
pip install jupyter
jupyter notebook
```
Run `explore.ipynb` → `CoarseClass.ipynb` → `FineClassification.ipynb` → `SquenceTrain.ipynb`

## Related

- **[VerityVaultProject](https://github.com/MNderi/VerityVaultProject)** — The inference runtime that consumes the models trained here

## License

This project is for research and educational purposes.
