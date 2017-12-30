# pix2code2

pix2code is a network introduced by Tony Beltramelli which transforms image designs for different UIs into their respective DSL code. 

The original paper found [here](https://arxiv.org/abs/1705.07962) and the original demo code found [here](https://github.com/tonybeltramelli/pix2code)

pix2code2 is an attempt to improve that network through the use of autoencoders. The network involves pre-training first the autoencoder on the GUI images using convulation neural networks (CNN) and deconvolution, or its transpose. The two networks are summarized below.

![pix2code_model](https://github.com/fjbriones/pix2code2/blob/master/pix2code_model.PNG)
pix2code model from original paper

![pix2code2_model](https://github.com/fjbriones/pix2code2/blob/master/pix2code2_model.PNG)
Proposed pix2code2 model

It can be observed that the only difference between the two networks instead of convolutional networks, patterned after VGGNet, inside the pix2code network, it was trained outside the network and inside the autoencoder. Instead of the parameters being optimized inside the pix2code network, they are optimized inside the autoencoder and the output of the encoder is then fed to the pix2code2 network. The rationale behind this is that the feature maps which represents the latent vector in a general autoencoder may have a direct relationship with that of a DSL code of a network, which is just a textual representation of the network. As the autoencoder tries to optimize the parameters of the network, it learns the different feautures of the image itself unlike the original pix2code network which just tries to optimize with the output parameter as just the code of the network.

## Code:
Parts of the code here are made by Tony Beltramelli and is subject to its corresponding licenses, see LINCENSE_pix2code file for more information. Some are modified such as to fit the proposed pixc2code2 network. The datasets are also from the original pix2code network.

So far, the network has been trained on the web-dataset and weights are included in this repository

## Usage of Code:

Prepare the data:
```sh
# reassemble and unzip the data
cd datasets
zip -F pix2code_datasets.zip --out datasets.zip
unzip datasets.zip

cd ../model

# split training set and evaluation set while ensuring no training example in the evaluation set
# usage: build_datasets.py <input path> <distribution (default: 6)>
./build_datasets.py ../datasets/web/all_data

# transform images (normalized pixel values and resized pictures) in training dataset to numpy arrays (smaller files if you need to upload the set to train your model in the cloud)
# usage: convert_imgs_to_arrays.py <input path> <output path>
./convert_imgs_to_arrays.py ../datasets/web/training_set ../datasets/web/training_features
```
Train the model:
```sh
cd model

# provide input path to training data and output path to save trained model and metadata
# usage: train.py <input path> <output path> <train_autoencoder (default: 0)>
./train.py ../datasets/web/training_set ../bin

# train on images pre-processed as arrays
./train.py ../datasets/web/training_features ../bin

# train with autoencoder
./train.py ../datasets/web/training_features ../bin 1
```

Generate code for batch of GUIs in web evaluation set, if you want to use pretrained weights, unzip this [file](https://1drv.ms/u/s!Ao8Y5FscWK9imo0GtV5u3sXOr6sc_A) and copy the pix2code2.h5 file to the bin folder:
```sh
mkdir code
cd model

# generate DSL code (.gui file), the default search method is greedy
# usage: generate.py <trained weights path> <trained model name> <input image> <output path> <search method (default: greedy)>
./generate.py ../bin pix2code2 ../datasets/web/eval_set ../code

# equivalent to command above
./generate.py ../bin pix2code2 ../datasets/web/eval_set ../code greedy

# generate DSL code with beam search and a beam width of size 3
./generate.py ../bin pix2code2 ../datasets/web/eval_set ../code 3
```

Generate code for a single GUI image:
```sh
mkdir code
cd model

# generate DSL code (.gui file), the default search method is greedy
# usage: sample.py <trained weights path> <trained model name> <input image> <output path> <search method (default: greedy)>
./sample.py ../bin pix2code2 ../test_gui.png ../code

# equivalent to command above
./sample.py ../bin pix2code2 ../test_gui.png ../code greedy

# generate DSL code with beam search and a beam width of size 3
./sample.py ../bin pix2code2 ../test_gui.png ../code 3
```

Compile generated code to target language:
```sh
cd compiler

# compile .gui file to HTML/CSS (Bootstrap style)
./web-compiler.py <input file path>.gui
```

## Sample Test results
Sample input image and output html file can be found on the folder [tests](https://github.com/fjbriones/pix2code2/tree/master/tests). All of these are from the eval_set of the web images dataset. The compilers and datasets used for recreating the code were still derived from the original work of Tony Beltramelli.
