# 1. Data

## 1.1 Description of the data

### train_audio/

The training data consists of short recordings of individual bird, amphibian, reptile, mammal, and insect sounds generously uploaded by users of xeno-canto.org and iNaturalist. These files have been resampled to 32 kHz where applicable to match the test set audio and converted to the ogg format. Filenames consist of \[collection\]\[file_id_in_collection\].ogg. The training data should have nearly all relevant files; we expect there is no benefit to looking for more on xeno-canto.org or iNaturalist and appreciate your cooperation in limiting the burden on their servers. If you do, please make sure to adhere to the scraping rules of these data portals.

- this suggests the provided dataset for training doesn't need to be supplemented with data from other sources

### test_soundscapes/

When you submit a notebook, the test_soundscapes directory will be populated with approximately 600 recordings to be used for scoring. They are 1 minute long and in ogg audio format, resampled to 32 kHz. The file names have the general form of BC2026_Test_file ID_site_date_time in UTC.ogg (e.g., file BC2026_Test_0001_S05_20250227_010002.ogg has file ID 0001, was recorded at site S05 on Feb 27 2025 at 01:00 UTC). It should take your submission notebook approximately five minutes to load all the test soundscapes. Not all species from the training data actually occur in the test data.

- kaggle provides the test soundscapes after submission (on kaggle)

### train_soundscapes/

Additional audio data from roughly the same recording locations as the test_soundscapes. Filenames follow the same naming convention as the test_soundscapes; although some recording sites overlap between train and test, precise recording dates and times do NOT overlap with recordings of the hidden test data. This year, some of the train_soundscapes have been labeled by expert annotators, and we provide the ground truth for a subset of train_soundscapes in train_soundscapes_labels.csv with columns filename referencing the soundscape file, start and end referencing the 5-second segment for which column primary_label provides a semicolon-separated list of species codes that have been marked as present in this segment.

### Important note

Some species with occurrences in the hidden test data might only have train samples in the labeled portion of train_soundscapes and not in the train_audio (XC and iNat data). However, not all species from train_soundscapes have occurrences in the test_soundscapes.

### Comparison of train audio and train soundscapes

| Aspect | train_audio | train_soundscapes |
| --- | --- | --- |
| **Source** | Crowdsourced uploads (xeno-canto, iNaturalist) [Certain] | Passive recorders at the competition's Pantanal sites [Certain] |
| **Recording style** | Focal: someone pointed a mic at one animal [Certain] | Unattended field recording that captures whatever calls [Certain] |
| **Length** | Short, variable-length clips [Certain] | Fixed 1-minute files, same as test [Certain] |
| **Location** | Anywhere the species occurs, with lat/lon provided [Certain] | Same sites as test, but different dates and times [Certain] |
| **Labels** | One `primary_label` per file plus optional, incomplete `secondary_labels` [Certain] | Only a subset is labeled, per 5-second segment, by experts, often with several species per segment [Certain] |
| **Label type** | Weak: the species is somewhere in the clip, not necessarily in every 5-second window [Likely] | Strong (time-localized) and multi-label [Certain] |
| **Label quality** | Recordist-assigned, with quality `rating` for XC only [Certain] | Expert-annotated [Certain] |
| **Signal-to-noise** | Usually high, with the target loud and dominant [Likely] | Low, with distant calls, overlap, wind, rain and insects [Likely] |
| **Species coverage** | Most of the 234 classes [Certain] | Includes some test species missing from train_audio [Certain] |
| **Matches test format** | No [Certain] | Yes, same file format and the same 5-second evaluation windows [Certain] |

## 1.2 Clean data

Probably data in train_audio/ need cleaning only.

- make sure every sample is in 32kHz and in ogg format
- iNat1693371 is probably an accidental/corrupted recording, contains no bird sound. Those should be dropped from data. It is short, so probably recordings shorter than xx sec are flawed.  
3 sec long samples look okay, I suggest 2 sec for xx

## 1.3 Data augmentation

### Adding noise

We have a quite large set of data already containing significant noise, so we might skip this step.  
(

- humans in the background eg. speaking, walking, laughing etc.
- sound of nature eg. rain, wind, creek, river,  thunder,  water dripping, rattling plants, etc.
- splash
- sound of machinery eg. cars, planes, trains, helicopter, chainsaw etc.
- cicadas, other animals in the background (constant noise and shorter sounds)
- white noise
- music in the background  
)

## 2. Concepts

- Noisy Student
- Power Transform
- Per-Channel Energy Normalization (PCEN)
- Mel spectogram

### 2.1 Noisy Student

a semi-supervised learning paradigm
[Noisy Student Training Paradigm](https://www.emergentmind.com/topics/noisy-student-training-paradigm)

### 2.2 Power Transform

A power transform is a family of parametric, monotonic transformations used in statistics and machine learning to stabilize variance, reduce skewness, and make non-normal data look more like a normal (Gaussian) distribution.  
In deep learning:

- stabilize gradient flow
- improve batch normalization
- soften outlier impact

You should apply the power transform first, and then standardize (or use a tool like scikit-learn's PowerTransformer(standardize=True), which automatically does both in the correct order).  
In practice, you rarely need to chain two separate transformers. You can let scikit-learn handle the sequence atomically:  
*from sklearn.preprocessing import PowerTransformer  
pt = PowerTransformer(method='yeo-johnson', standardize=True)  
X_transformed = pt.fit_transform(X)*  

Power transforms are frequently used in audio processing and deep learning when working with frequency-domain representations (like spectrograms, Mel-spectrograms, or log-Mel filterbanks) or energy/amplitude envelopes.  
