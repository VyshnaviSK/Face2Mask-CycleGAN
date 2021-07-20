# Face2Mask-CycleGAN

This program uses the CycleGAN architecture for style transfer application of images. It converts images of people wearing masks to images of the same people without masks and vice versa.


![image](https://user-images.githubusercontent.com/67890345/126367971-c20861c6-9559-4275-a9e0-dfba8f74b627.png)

__Image 1: Given testing dataset images without masks, obtained images with masks on__

![image](https://user-images.githubusercontent.com/67890345/126368013-bf54e0cb-33d3-456e-a7ad-522e358b6e1a.png)

__Image 2: Given testing dataset images with masks, obtained images without masks__

The idea of base CycleGAN architechture and code for the same has been taken from https://arxiv.org/pdf/1703.10593.pdf and https://github.com/junyanz/pytorch-CycleGAN-and-pix2pix respectively. 

The training datasets are to be placed in folder dataset/trainA and dataset/trainB respectively. Same to be followed for testing dataset.

To run the program, open the CycleGAN_Face_Mask.ipynb file in Google Colab, connect to GPU and run the cells. 

Below contain the overview and explanation of code structure: 

__train.py__ is a general-purpose training script.

__test.py__ is a general-purpose test script. Once you have trained your model with train.py, you can use this script to test the model. It will load a saved model from --checkpoints_dir and save the results to --results_dir.

__Directory: Data: Contains all the modules related to data loading and preprocessing.__
1. __init__.py implements the interface between this package and training and test scripts. train.py and test.py call from data import create_dataset and dataset = create_dataset(opt) to create a dataset given the option opt.
2. __base_dataset.py__ implements an abstract base class (ABC) for datasets. It also includes common transformation functions (e.g., get_transform, scale_width), which can be later used in subclasses.
3. __image_folder.py__ implements an image folder class.
4. __template_dataset.py__ provides a dataset template with detailed documentation.
5. __unaligned_dataset.py__ includes a dataset class that can load unaligned/unpaired datasets. It assumes that two directories to host training images from domain A /path/to/data/trainA and from domain B /path/to/data/trainB respectively. Then you can train the model with the dataset flag --dataroot /path/to/data. Similarly, you need to prepare two directories /path/to/data/testA and /path/to/data/testB during test time.
6. __single_dataset.py___ includes a dataset class that can load a set of single images specified by the path --dataroot /path/to/data. It can be used for generating CycleGAN results only for one side with the model option -model test.

__Directory: Models: contains modules related to objective functions, optimizations, and network architectures.__

1. __init__.py implements the interface between this package and training and test scripts. train.py and test.py call from models import create_model and model = create_model(opt) to create a model given the option opt. You also need to call model.setup(opt) to properly initialize the model.
2. __base_model.py__ implements an abstract base class (ABC) for models. It also includes commonly used helper functions (e.g., setup, test, update_learning_rate, save_networks, load_networks), which can be later used in subclasses.
3. __template_model.py__ provides a model template with detailed documentation. Check out this file if you plan to implement your own model.
4. __cycle_gan_model.py__ implements the CycleGAN model, for learning image-to-image translation without paired data. The model training requires --dataset_mode unaligned dataset. By default, it uses a --netG resnet_9blocks ResNet generator, a --netD basic discriminator and a least-square GANs objective (--gan_mode lsgan).
5. __networks.py__ module implements network architectures (both generators and discriminators), as well as normalization layers, initialization methods, optimization scheduler (i.e., learning rate policy), and GAN objective function (vanilla, lsgan, wgangp).
6. __test_model.py__ implements a model that can be used to generate CycleGAN results for only one direction. This model will automatically set --dataset_mode single, which only loads the images from one set. 


__Directory: options__
1. __init__.py is required to make Python treat the directory options as containing packages,
2.__base_options.py__ includes options that are used in both training and test. It also implements a few helper functions such as parsing, printing, and saving the options. It also gathers additional options defined in modify_commandline_options functions in both dataset class and model class.
3. __train_options.py__ includes options that are only used during training time.
4. __test_options.py__ includes options that are only used during test time.


__Directory: utils- contain miscellaneous helper functions__
1. __init__.py is required to make Python treat the directory util as containing packages
2. __html.py__ implements a module that saves images into a single HTML file.
3. __image_pool.py__ implements an image buffer that stores previously generated images. This buffer enables us to update discriminators using a history of generated images rather than the ones produced by the latest generators. The size of the buffer is controlled by the flag --pool_size.
4. __visualizer.py__ includes several functions that can display/save images and print/save logging information. It uses a Python library visdom for display and a Python library dominate (wrapped in HTML) for creating HTML files with images.
5. __util.py__ consists of simple helper functions such as tensor2im (convert a tensor array to a numpy image array), diagnose_network (calculate and print the mean of average absolute value of gradients), and mkdirs (create multiple directories).

__Directory: Scripts: contain all bash scripts of the same__

