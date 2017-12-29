# pix2code2

pix2code iS a network introduced by Tony Beltramelli which transforms image designs for different UIs into their respective DSL code. 

The original paper found [here](https://arxiv.org/abs/1705.07962) and the original demo code found [here](https://github.com/tonybeltramelli/pix2code)

pix2code2 is an attempt to improve that network through the use of autoencoders. The network involves pre-training first the autoencoder on the GUI images using convulation neural networks (CNN) and deconvolution, or its transpose. The two networks are summarized below.

![pix2code_model](https://github.com/fjbriones/pix2code2/blob/master/pix2code_model.PNG)
pix2code model from original paper

![pix2code2_model](https://github.com/fjbriones/pix2code2/blob/master/pix2code2_model.PNG)
Proposed pix2code2 model

It can be observed that the only difference between the two networks instead of convolutional networks, patterned after VGGNet, inside the pix2code network, it was trained outside the network and inside the autoencoder. Instead of the parameters being optimized inside the pix2code network, they are optimized inside the autoencoder and the output of the encoder is then fed to the pix2code2 network. The rationale behind this is that the feature maps which represents the latent vector in a general autoencoder may have a direct relationship with that of a DSL code of a network, which is just a textual representation of the network. As the autoencoder tries to optimize the parameters of the network, it learns the different feautures of the image itself unlike the original pix2code network which just tries to optimize with the output parameter as just the code of the network.
