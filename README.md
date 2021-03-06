## Unsupervised Single Image Underwater Depth Estimation (UW-Net) 
[[Project]](http://www.ee.iitm.ac.in/comp_photolab/project-underwater.html) [[Paper]](https://arxiv.org/pdf/1905.10595.pdf) 

This repository contains the tensorflow implementation for UW-Net and includes the scripts to train and test the network. 
The code is written and maintained by [Honey Gupta](https://github.com/honeygupta).
## Getting Started

### Package requirements
 * The following packages are required to run the codes. Note: the version of the packages were the ones we used and are suggestive, not mandatory.
    * python = 3.6
    * click = 7.0
    * tensorflow-gpu = 1.11
    * scikit-image = 0.15

### Prepare dataset
* You can download any existing underwater image dataset or create your own by collecting images from the internet (while considering their copyrights). 
	* Put images from each domain i.e RGBD terrestrial images in the form of .npy files and 2D underwater images in different folders 
* Create the csv file as input to the data loader. 
	* Edit the uwnet_datasets.py file. 
	
	    For example, if your dataset contains 1449 RGBD images and <1449 underwater images as .png files, you can just edit the uwnet_datasets.py as following
	```python
	DATASET_TO_SIZES = {
    'air2water_train': 1449
	}
	PATH_TO_CSV = {
    'air2water_train': 'input/air2water_train.csv'
	}
	DATASET_TO_IMAGETYPE = {
    'air2water_train': '.png'
	}
	``` 
	* Run create_uwnet_dataset.py
	```bash
	python create_uwnet_dataset --image_path_a=/path/to/folder/with/rgbd/npy/files \
     --image_path_b=/path/to/folder/containing/underwater/images --dataset_name="air2water_train"
	```

### Training
* Create the configuration file. The configuration file contains basic information for training/testing. An example of the configuration file could be fond at configs/exp_01.json. 

* Start training:
    ```bash
    python main.py \
        --to_train=1 \
        --log_dir=out/exp_01 \
        --config_filename=configs/exp_01.json
    ```
* Check the intermediate results.
	* Tensorboard
	```bash
	tensorboard --port=6006 --logdir=out/exp_01/#timestamp# 
	```
	* Check the html visualization at  ```out/exp_01/#timestamp#/epoch_#id#.html```  

### Restoring from the previous checkpoint
```bash
python main.py \
    --to_train=2 \
    --log_dir=out/exp_01 \
    --config_filename=configs/exp_01.json \
    --checkpoint_dir=out/exp_01/#timestamp#
```
### Testing 

You can test the models in two ways: (1) using the test.py file that does not require the RGBD dataset (2) Using the main.py file.
I recommend using the test.py as it does not require the RGBD dataset.

* Create the testing dataset
	* Edit the uwnet_datasets.py file the same way as training
	    * A sample entry for the dataset by Berman et al. can be found in the script
	* Create the csv file as the input to the data loader. 
    To create the csv file containing only the underwater image list:
    ```bash
    python create_uwnet_dataset.py \
         --image_path_b=/path/to/folder/containing/underwater/images --dataset_name="hazelines" --mode="test"
    ```
    To create a csv file containg both UW and RGBD file names:
    ```bash
    python create_uwnet_dataset.py --image_path_a=/path/to/folder/with/rgbd/npy/files \
         --image_path_b=/path/to/folder/containing/underwater/images --dataset_name="hazelines"
    ```
    
     
* Run testing
    ```bash
    python test.py \
        --log_dir=out/exp_01_test \
        --config_filename=configs/exp_01_test.json \
        --checkpoint_dir=out/exp_01/#timestamp# 
    ```
    
    OR

    ```bash
    python main.py \
        --to_train=0 \
        --log_dir=out/exp_01_test \
        --config_filename=configs/exp_01_test.json \
        --checkpoint_dir=out/exp_01/#timestamp# 
    ```
The result is saved in out/exp_01_test/#timestamp#. 

#### Using the pre-trained model
Download the pre-trained models from [here](https://drive.google.com/file/d/1zkOwdPFP3NVdBhfa-7bdIkOa5HxzNyqT/view?usp=sharing) and extract the files in the working folder.

In the provided checkpoints, the folder ```checkpoints/pre-trained/``` contains the model trained on our collected underwater dataset and 
```checkpoints/finetune``` contains the model from ```checkpoints/pre-trained/``` fine-tuned on Berman et al.'s dataset for 20 epochs.

To test on the pre-trained models, change the entry for --checkpoint_dir in the above command line script.   

## Citation
Kindly cite our paper if this repository is useful for your research.

### Bibtex:

```
@article{gupta2019unsupervised, 
  title={Unsupervised Single Image Underwater Depth Estimation}, 
  author={Gupta, Honey and Mitra, Kaushik}, 
  journal={arXiv preprint arXiv:1905.10595}, 
  year={2019}
}
```

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## Acknowledgements
This repository is partially built upon the [CycleGAN](https://github.com/leehomyc/cyclegan-1) repository written by Harry Yang and Nathan Silberman.


