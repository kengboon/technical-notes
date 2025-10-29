![Implemented](https://img.shields.io/badge/Implemented-green?logo=TickTick&logoColor=white)

_2024.10_

# Two-stage Approach for Industrial OCR: Overcoming the Limitation of CRNN Text Recognizer

__Author:__ [Teo Keng Boon](https://github.com/kengboon)

__Keywords:__ _OCR, deep OCR, text-recognition, classification_

## Abstract

The deep learning-based optical character recognition (deep OCR) showing high accuracy and robustness compared to traditional OCR methods. However, most of the well known open source OCR libraries use convolutioal recurrent neural network (CRNN) for text recognition, fall short in the industrial use cases, i.e. recognition of irregular and non-natural languages texts e.g. product IDs and serial codes. To solve the limitation, a two-stage approach, i.e. 1) text & characters detection following by 2) character classification is used. The fine-tuning of the approach relies mainly on the image classification model, and is more reliable and explainable for the aforementioned industrial use cases.

## Background

Optical character recognition (OCR) means the recognition of characters (or texts) from images. Traditionally, the OCR is done by pattern recognition and other image processing methods. The power of deep learning improves the robustness of OCR, models can be trained to detect and recognize texts in more complex images.

The common stages of OCR and deep OCR comprise of (at least):

1. Text detection (the region that is probably text)
2. Text recognition (recognize the image into readable text)

There are many well known open source OCR framework, e.g. [EasyOCR](https://github.com/JaidedAI/EasyOCR) and [PaddleOCR](https://github.com/PaddlePaddle/PaddleOCR) (v2.x), made for scene text recognition. The models used at text recognition stage are convolutional recurrent neural network (CRNN), usually utilize bidirectional long short-term memory (BiLSTM) network or other recurrent network (RNN), that can capture character dependencies and perform well for natural language (e.g. English) text recognition.

On the other hand, the industrial OCR use cases involve recognition of irregular and non-natural language texts, e.g. product ID and serial codes. There are no implicit relationship between characters althrough the text may following some predefined pattern. The models from the open source OCR libraries frequently misrecognize such texts due to the interference of the character relationship learnt by CRNN.

## Method

To solve the problem, a two-stage approach is used instead of end-to-end text recognition by the open source OCR libraries.

The stages are as followed:

1. Text & character detection
2. Character classification

For the implementation, a pretrained [Character-Region Awareness For Text detection (CRAFT)](https://github.com/clovaai/CRAFT-pytorch) model is used for stage 1. The model outputs score map that can be post-processed to determine character regions and affinity (i.e. link to texts), the outputs contain both information that are needed for the following character classification stage and final text contructions.

The character classification model, on the other hand, is a typical deep learning-based image classification model. The backbone is a convolutional neural network (CNN), the feature embeddings are passed to fully connected layer(s) and softmax activation function, the outputs represent the probabilities of the inputs belonged to each class. The model need to be trained with images of all possible characters, techniques such as augmentations can be used so that the model can adapt to varies or more complex scenes.

The character regions extracted at stage 1 are stacked, and inferred by the character classification model at stage 2. The predicted characters are then joined into respective text using the affinity outputs from stage 1.

## Outcome

The outcome is the text recognition is actually by classify each character (can be sped up by batch inference and small inputs), without the contextual relationship between characters. The method is actually ideal for aforementioned industrial use cases, where the possible classes of characters are well-defined, and no fixed pattern or implicit relationship between characters.

Only the classification model needed to be re-trained or fine-tuned if new character(s) is introduced, or major changes on the character appearance. While training an image classification model is a fundamental and common task for deep learning in computer vision, there are well-known techniques and resources that make the improvement of model easier and more feasible.

## Limitations

The main limitations of the two-stage approach are as follow:

1. Overhead of two-stage processing and model inference.

    In constract to most open source OCR libraries, that chain the text detection model, text recognition model, and decoding together, the two-stage approach seperate the processings and model inference, overhead can be introduced but techniques such as batch or parallel processing can mitigate the problem.

2. Dependent on text detection

    Since the character is extracted based on detection at stage 1, any detection errors e.g. missed/misaligned/merged character boxes can directly affect the input into stage 2.

3. No contextual information

    There are cases where contextual information existed in the industrial use cases, e.g. the pattern and composition of product ID. The two-stage approach intended to ignore such information, preventing learnt by models, reducing the problem into recognition of random characters.

## Conclusion

The two-stage approach for deep OCR, although with some limitations, is generally ideal for OCR of industrial use cases, especially to recognize non-natural languages texts.