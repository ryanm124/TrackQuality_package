
# Track Quality for Level-1 Track Finding

This project has the utilities for creating track quality classifiers for the Level-1 track finding. It includes:

1. Datasets
2. Models
3. Analysis Tools

## Datasets

Datasets is a general utility for translating root files generated by the level-1 tracking ntuple makers into python .h5 files split into X and y test and train

### Usage
``
Dataset = TrackDataSet("Dataset_name")
``

Instantiate the Dataset class, for more dataset classes see dataset.py

``Dataset.load_data_from_root("root_file_path.root",n_batches)
``

Load the root datafile, providing the full file path of the root file. N_batches is used if you want to use exactly the same number of events across files for direct comparision. If the full dataset is needed set to a large value 100,000+

``
Dataset.generate_test_train()
``

Generate the test train split, this will currently automatically do a shuffle, feature transformation, a particle ID and fake balancing to the dataset, and create a 0.1 test train split. generate_test() and generate_train() can be used to only create testing and training datasets. See dataset.py to create your own features or balancing

``
Dataset.save_test_train_h5("filepath")
``

Save the files, this will create the filepath directory and create an X_train.h5, X_test.h5, y_train.h5 and y_test.h5. It will also create a json file describing the files

## Models

A model is an algorithm that predicts y based on X, within this folder there is the generic class TrackQualityModel that is inherted from. There are a number of general quality models:
1. CutTrackQualityModel

## Installation

Instructions for installing the project.

## Usage

Instructions for using the project.

## Contributing

Guidelines for contributing to the project.

## License

Information about the project's license.

## Known bugs to fix

Dataset config dict showing wrong number of events, h5filepath and loaded timestamp

Dataset saves test or train when one is not generated
