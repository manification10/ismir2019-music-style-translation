# Data preparation

This directory contains the pipelines for pre-processing MIDI files and saving them in the format expected by the models.

Before proceeding, install the Python code by following the steps in the [main README](../README.md#installation).

## Bodhidharma data

The `bodhidharma` directory contains the recipe for pre-processing the Bodhidharma test dataset. To run it, simply execute `./prepare.sh`; the dataset will be downloaded automatically.

## Parallel data

The `parallel` directory contains the recipe for pre-processing the synthetic parallel data for training, validation and testing.

Put your MIDI files in `01_src` or edit the `prepare.sh` script to make the `src_dir` variable point to their location.
The filenames must have the form `{song}.{style}.{number}.mid`, e.g. `Autumn Leaves.ZZJAZZ.00.mid`. Note that `number`
must be a two-digit number (e.g. 00) and `style` must not contain a period. Each song should be present in at least two different styles, otherwise training pairs cannot be formed.
The files need to have correct time signature and tempo information so that they can be split on downbeats.

Once your files are in place, run `./prepare.sh`. This will perform the following steps (you might want to tweak them
depending on your setup):

1. Filter the files to keep only those that are entirely in 4/4 or 12/8 time. You may skip this step by putting your files
   in `02_filtered` directly.
   
2. Distribute the files into numbered subfolders (00 to 59 by default) called ‘shards’. This is controlled by the file
   `shards.tsv`, which maps song names to shard numbers. If you are using a different set of songs, you can remove or rename
   this file and your songs will be distributed randomly.
   
   For the paper, we used shards 00–54 for training, 55 for validation and 56 for testing (shards 57–59 are unused).

3. Normalize the tempo to 60 BPM, extract the notes of each accompaniment track and chop them into 8-bar segments. The result
   is a pickle file for each shard containing all the segments of a given accompaniment track (e.g. `Bass/00.pickle`).

   The tracks are matched based on their name, but you can also use the program number.
   Run `python -m ismir2019_cifka.data.chop_midi -h` to see the available options.
