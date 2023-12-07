
# Track Quality for Level-1 Track Finding

This project has the utilities for creating track quality classifiers for the Level-1 track finding. It includes:

1. Datasets
2. Models
3. Analysis Tools

## Datasets

Datasets is a general utility for translating root files generated by the level-1 tracking ntuple makers into python .h5 files split into X and y test and train

### Usage
```
Dataset = TrackDataSet("Dataset_name")
```

Instantiate the Dataset class, for more dataset classes see dataset.py

```
Dataset.load_data_from_root("root_file_path.root",n_batches)
```

Load the root datafile, providing the full file path of the root file. N_batches is used if you want to use exactly the same number of events across files for direct comparision. If the full dataset is needed set to a large value 100,000+

```
Dataset.generate_test_train()
```

Generate the test train split, this will currently automatically do a shuffle, feature transformation, a particle ID and fake balancing to the dataset, and create a 0.1 test train split. generate_test() and generate_train() can be used to only create testing and training datasets. See dataset.py to create your own features or balancing

```
Dataset.save_test_train_h5("filepath")
```

Save the files, this will create the filepath directory and create an X_train.h5, X_test.h5, y_train.h5 and y_test.h5. It will also create a json file describing the files

## Models

A model is an algorithm that predicts y based on X, within this folder there is the generic class TrackQualityModel that is inherted from. There are a number of general quality models:
1. CutTrackQualityModel
    - Performs basic $\chi^2$ cuts on the tracks
2. GBDTTrackQualityModel
    Subclassed into:
     - SklearnClassifierModel (Using Sklearn)
     - XGBoostClassifierModel (Xgboost using the sklearn interface)
     - FullXGBoostClassifierModel (Xgboost with full xgboost interface with more parameters to tweak)
     - TFDFClassifierModel (tensorflow decision forests, soon to be depreciated)
3. NNTrackQualityModel
    - Unused, needs updating and expanding

### Training

To train a model first generate a dataset as above.
Create the model with name "name"
```
model = XGBoostClassifierModel("name")
```
Load the dataset located in "Train_Dataset_path"
```
model.load_data("Train_Dataset_path")
```

Train the model
```
model.train()
```
Save the model in the filepath 
```
model.save_model("filepath")
```

Parameters of the model can be adapated before training with:

```
model.min_child_weight['value'] =  1.37
model.alpha['value']            =  0.93
model.early_stopping['value']   =  5
model.learning_rate['value']    =  0.32
model.n_estimators['value']     =  60
model.subsample['value']        =  0.25
model.max_depth['value']        =  3 
model.gamma['value']            =  0.0	
model.rate_drop['value']        =  0.79
model.skip_drop['value']        =  0.15
```

### Evaluating
Assuming a model has been already trained as above, first create the model
```
model = XGBoostClassifierModel("name")
```
load the saved model
```
model.load_model("filepath")
```
load the testing dataset
```
model.load_data("Test_Dataset_path")
```
test the model
```
model.test()
```
Evaluate the model, runs a ROC curve calculation and creates some plots found in the save_dir. Also runs the ROC calculation in $\eta$, $p_T$, $z_0$ and $\phi$ bins.
```
model.evaluate(plot=True,save_dir="save_dir")
```
Plot model importances
```
plot_model(model,"save_dir")
```
As the evaluation takes a "long" time the model can be saved after this step and the predictions and ROC calculations will be saved in arrays

```
model.full_save("save_dir")
```
And loaded:
```
model.full_load("save_dir")
```

### Synthesis

In order to evaluate the quantisation of the model and the firmware usage there are utilities to synthesize the model.

To use the HLS and HDL options you will need a vivado install and to add it to your path with the following:
```
export PATH="$/opt/Xilinx/Vitis_HLS/2021.2/include:$PATH"
source /opt/Xilinx/Vivado/2021.2/settings64.sh
export BUILD_VIVADO_VERSION=2021.2/
export BUILD_VIVADO_BASE=/opt/Xilinx/Vivado/
```


To run the synthesis first define which precisions to use in a list, single precisions are allowed but in a list format
```
precisions = ['ap_fixed<12,6>','ap_fixed<11,6>','ap_fixed<11,5>','ap_fixed<10,6>','ap_fixed<10,5>','ap_fixed<10,4>']
``` 
Then run the synthesis
```
synth_model(model,sim=True,hdl=True,hls=True,cpp=True,onnx=True,python=True,
                 test_events=10000,
                 precisions=precisions,
                 save_dir="filepath")
```

The Sim flag runs a C-simulation of the BDT needed to evaluate if the precision selected loses performance
The HDL flag runs the HDL synthesis and produces a resource vs precision plot
The HLS flag runs the HLS synthesis and produces a resource vs precision plot
The cpp flag runs a c++ version of the tree needed for bit accurate emulation in CMSSW, the model produced is ported to CMSSW
The onnx flag generates an onnx model needed for deployment as simulation in CMSSW
The python flag runs a python version of the tree for comparison


### Model to Model comparion

plot_ROC_bins([model.eta_roc_dict,cutmodel.eta_roc_dict],
                    [name_list[i]+" XGB threshold = "+str(threshold),name_list[i]+" Cut"],
                    "Projects/"+folder+"/",
                    variable=Parameter_config["eta"]["branch"],
                    var_range=Parameter_config["eta"]["range"],
                    n_bins=Parameter_config["eta"]["bins"],
                    typesetvar=Parameter_config["eta"]["typeset"],
                    what=plottype, threshold = threshold)

plot_ROC([model.roc_dict,cutmodel.roc_dict],[name_list[i]+" XGB",name_list[i]+" Cut"],"Projects/"+folder+"/")

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