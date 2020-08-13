# kapre
Tensorflow.Keras Audio Preprocessors. Written and maintained by Keunwoo Choi.

Why bother to save STFT/melspectrograms to your storage? Just do it on-the-fly on-GPU.

How demanding is the computation? [Check out this paper!](https://arxiv.org/abs/1706.05781)

## Contents
- [News](#news)
- [Installation](#installation)
- [Usage](#usage)
- [How to cite](#citation)


## News

* 13 Aug 2020
  - 0.3.0-rc
    - A breaking change with Tensorflow 2.0 and more tests. Some features are removed.

* 29 Jul 2020
  - 0.2.0
    - Change melspectrogram filterbank from `norm=1` to `norm='slaney'` (w.r.t. Librosa) due to the update from Librosa ([#77](https://github.com/keunwoochoi/kapre/issues/77)). 
    This would change the behavior of melspectrogram slightly.
    - Bump librosa version to 0.7.2 or higher.
* 17 Mar 2020
  - 0.1.8
    - added `utils.Delta` layer
* 20 Feb 2020
  - Kapre ver 0.1.7
    - No vanilla Keras dependency
    - Tensorflow >= 1.15 only
    - Not tested on Python 2.7 anymore; only on Python 3.6 and 3.7 locally (by `tox`) and 3.6 on Travis 
    
* 20 Feb 2019
  - Kapre ver 0.1.4
    - Fixed amplitude-to-decibel error as raised in [#46](https://github.com/keunwoochoi/kapre/issues/46)
     
* March 2018
  - Kapre ver 0.1.3
    - Kapre is on Pip again
    - Add unit tests
    - Remove `Datasets`
    - Remove some codes while adding more dependency on Librosa to make it cleaner and more stable
      - and therefore `htk` option enabled in `Melspectrogram`
      
* 9 July 2017
  - Kapre ver 0.1.1, aka 'pretty stable' with a [benchmark paper](https://arxiv.org/abs/1706.05781)
    - Remove STFT, python3 compatible
    - A full documentation in this readme.md
    - pip version is updated

## Installation
([↑up to contents](#contents))

Kapre is now working with tensorflow >= 2.0
 
```sh
pip install git+https://github.com/keunwoochoi/kapre.git
```

## Usage
### Layers
([↑up to contents](#contents))

Audio preprocessing layers
* `Spectrogram`, `Melspectrogram` in [time_frequency.py](https://github.com/keunwoochoi/kapre/blob/master/kapre/time_frequency.py)
* `AmplitudeToDB`, `Normalization2D` in [utils.py](https://github.com/keunwoochoi/kapre/blob/master/kapre/utils.py)
* `Filterbank` in [filterbank.py](https://github.com/keunwoochoi/kapre/blob/master/kapre/time_frequency.py)
* `AdditiveNoise` in [augmentation.py](https://github.com/keunwoochoi/kapre/blob/master/kapre/augmentation.py)

## One-shot example
([↑up to contents](#contents))

* More examples on [example folder](https://github.com/keunwoochoi/kapre/tree/master/examples)

### Using Mel-spectrogram
```python
from tensorflow.keras.models import Sequential
from tensorflow.keras.layers import Conv2D, BatchNormalization, ReLU, GlobalAveragePooling2D, Dense, Softmax
from kapre.time_frequency import STFT
from kapre.composed import get_melspectrogram_layer, get_log_frequency_spectrogram_layer

# 6 channels (!), maybe 1-sec audio signal, for an example.
input_shape = (6, 44100)
sr = 44100
model = Sequential()
# A STFT layer
model.add(STFT(n_fft=2048, win_length=2018, hop_length=1024,
               window_fn=None, pad_end=False,
               input_data_format='channels_last', output_data_format='channels_last'))
# Alternatively, you may want to use a melspectrogram layer
# melgram_layer = get_melspectrogram_layer()
# or log-frequency layer
# log_stft_layer = get_log_frequency_spectrogram_layer() 

# add more layers as you want
model.add(Conv2D(32, (3, 3), strides=(2, 2)))
model.add(BatchNormalization())
model.add(ReLU())
model.add(GlobalAveragePooling2D())
model.add(Dense(10))
model.add(Softmax())

# Compile the model
model.compile('adam', 'categorical_crossentropy') # if single-label classification

# train it with raw audio sample inputs
# for example, you may have functions that load your data as below.
x = load_x() # e.g., x.shape = (10000, 6, 44100)
y = load_y() # e.g., y.shape = (10000, 10) if it's 10-class classification
# then..
model.fit(x, y)
# Done!
```

# Citation
([↑up to contents](#contents))

Please cite this paper if you use Kapre for your work.

```
@inproceedings{choi2017kapre,
  title={Kapre: On-GPU Audio Preprocessing Layers for a Quick Implementation of Deep Neural Network Models with Keras},
  author={Choi, Keunwoo and Joo, Deokjin and Kim, Juho},
  booktitle={Machine Learning for Music Discovery Workshop at 34th International Conference on Machine Learning},
  year={2017},
  organization={ICML}
}
```
