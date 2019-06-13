# Supervised symbolic music style translation
This is the code for the paper ‘Supervised symbolic music style translation using synthetic data’, accepted to ISMIR 2019.

The repository contains the following directories:
- `ismir2019_cifka` – code for training and evaluating models.
- `experiments` – configuration files for the models from the paper and a script to download the trained model checkpoints.
- `data` – data preparation recipes.

You can either download the trained models (by running `./get_models.sh` in the `experiments` directory), or train your own by following the steps below.

## Installation

Clone the repository and make sure you have Python 3.6 or later. Then run the following commands.

1. (optional) To make sure you have the right versions of the most important packages, run:
   ```sh
   pip install -r requirements.txt
   ```
   Alternatively, if you use conda, you can create your environment using
   ```sh
   conda env create -f environment.yml
   ```
   This will also install the correct versions of the CUDA and CuDNN libraries.
   
   If you wish to use different (more recent) package versions, you may skip this step; the code should still work.

2. Install the package with:

   ```sh
   pip install .
   ```

## Data

See the [data README](data/README.md) for how to prepare the data.

## Training a model

The scripts for training the models are in the `ismir2019_cifka.models` package.

The `experiments` directory has a subdirectory for each model from the paper. The `model.yaml` file in each directory contains all the hyperparameters and other settings required to train and use the model; the first line also tells you what type of model it is (i.e. `seq2seq_style` or `roll2seq_style`).  For example, to train the `all2bass` model, run the following command inside the `experiments` directory:
```sh
python -m ismir2019_cifka.models.roll2seq_style --logdir all2bass train
```
You may need to adjust the paths in `model.yaml` to point to your dataset.

## Running a model

Before running a trained model on some MIDI files, we need to use the `chop_midi` script to chop them up into segments and save them in the expected format, e.g.:
```sh
python -m ismir2019_cifka.data.chop_midi \
    --no-drums \
    --force-tempo 60 \
    --bars-per-segment 8 \
    --include-segment-id \
    song1.mid song2.mid songs.pickle
```
Then we can `run` the model, providing the input file, the output file and the target style. For example:
```sh
python -m ismir2019_cifka.models.roll2seq_style --logdir all2bass run songs.pickle output.pickle ZZREGGAE
```
To listen to the outputs, we need to convert them back to MIDI files, which involves time-stretching the music from 60 BPM to the desired tempo, assigning an instrument, and concatenating the segments of each song:
```sh
python -m ismir2019_cifka.data.notes2midi \
   --instrument 'Fretless Bass' \
   --stretch 60:115 \
   --group-by-name \
   --time-unit 4 \
   output.pickle outputs
```

## Evaluation

## Acknowledgment
This work has received funding from the European Union’s Horizon 2020 research and innovation programme under the Marie Skłodowska-Curie grant agreement No. 765068.
