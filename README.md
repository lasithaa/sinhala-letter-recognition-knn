# Sinhala Letter Recognition (KNN)

A small machine learning project that recognises handwritten Sinhala letters.
You draw a letter in a Tkinter window and a K-Nearest Neighbors (KNN) model
predicts which letter it is.

**Letters supported:** අ, ඇ, එ, උ (folders `a`, `ae`, `e`, `u`)

## Demo

![Sinhala letter predictor app](screenshots/app.png)

## Results

| Metric | Value |
|---|---|
| Images in dataset | 192 (60 / 44 / 44 / 44 per letter) |
| Best settings | `n_neighbors=3`, `weights="uniform"` |
| Cross-validation accuracy | 97.3% |
| Test accuracy | 97.4% (38 of 39 unseen images) |

The test set is small (39 images), so treat the numbers as a rough guide.

## How it works

1. **Preprocess** every image: grayscale, invert so the letter is white on black,
   crop to the letter, pad to a square, resize to 32x32, flatten to 1024 numbers.
2. **Train** a KNN classifier. `GridSearchCV` (5-fold cross-validation) picks the
   best `k` and vote weighting.
3. **Evaluate** on 20% of the data the model never saw (classification report and
   confusion matrix).
4. **Save** the final model with `joblib`.
5. **Predict** in a Tkinter app: the letter drawn on the canvas goes through the
   exact same preprocessing before it reaches the model.

## Project structure

```
.
├── 01_train_model.ipynb   # preprocessing, training, evaluation, saving
├── 02_tkinter_app.ipynb   # draw-and-predict app
├── knn_sinhala.joblib     # trained model
├── sample_images/         # a few example training images
├── screenshots/           # app screenshot
├── requirements.txt
└── README.md
```

The full dataset is not included. Training expects this layout
(folder name = label):

```
dataset/
  a/   image1.png ...
  ae/
  e/
  u/
```

## Run it

```bash
pip install -r requirements.txt
jupyter notebook
```

- To use the ready-made model, open `02_tkinter_app.ipynb` and run the cells in order.
  A drawing window opens. Draw a letter and press **Predict**.
- To retrain, put your images in a `dataset/` folder as shown above and run
  `01_train_model.ipynb`.

Tkinter ships with Python. On macOS the Sinhala font used is `Sinhala Sangam MN`;
change `FONT_NAME` in the app cell if you use another system.

## Notes

- Preprocessing settings (`IMG_SIZE`, `INK_THRESHOLD`, `PADDING_RATIO`) must be
  identical in training and in the app.
- Set the **Brush size** slider close to the stroke thickness of your dataset images.
- KNN keeps every training image, so it gets slower as the dataset grows.
  A small CNN is the natural next step for many more letters.

## Ideas for improvement

- Add more letters (ආ, ඉ, ඊ, ...) and more images per letter
- Add images drawn in the app to the training data
- Try a CNN
