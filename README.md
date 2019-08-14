# Project Albion

![Ooops, this image isn't working](images/WhatsApp_Image_2019-08-14_at_20.08.45.png)

## Brief description
With this file, you can generate imaginary British place names. 

For example, in the past, it has created _Dunby_, _Mongrave_, _Carneyh_,  _Warer Forest_ and _Balcland on the Green_. 

## Dataset

14679 English place names have been collected from the [Oxford Reference Website](https://www.oxfordreference.com/view/10.1093/acref/9780199609086.001.0001/acref-9780199609086).

Non-alphabetic characters (except space, hyphon and the newline character) have been removed from the dataset.
Place names such as _Curthwaite, East_ have been turned around to obtain _East Curthwaite_. 

Then, the dataset was put into a .txt file.

## Inspirations

- "Dinosaur Island" assignment from Andrew Ng's [Sequence Models course](https://www.coursera.org/learn/nlp-sequence-models)
  - Creating your own RNN (no API) to generate Dinosaur names 
  - *upsides*: gain profound insight into how RNNs work, respectable results
  - *downsides*: not practical since no API is being used, very simple network, takes lots of iterations to train
  
- A [Machine Learning Mastery article](https://machinelearningmastery.com/text-generation-lstm-recurrent-neural-networks-python-keras/)
  - how to generate Alice-in-Wonderland-like text in python using Keras' LSTMs
  - *upsides*: everything well explained, in particular how to turn your dataset into training examples & save weights 
  - *downsides*: instead of single words, text was being created, which makes a slight difference in creating training examples
 
 - The [guide to the Sequential model](https://keras.io/getting-started/sequential-model-guide/) on Keras
   - if you're not sure how to use Keras, consult this
   - Using the Sequential model is an easy way to build a simple RNN
   - in a subsection, there is also a code example to using an LSTM for sequence labelling (e.g. generating the next character)
   
## Generating training examples
### Convert to integers
First of all, an integer was assigned to every character in the (final) dataset. Then, all the place names from the dataset were re-expressed as sequences of integers instead of characters. 

### Padding
Unlike in most character-based models, I wasn't trying to generate text (e.g. weatherforecast, Shakespearean poetry, etc.). Instead, I wanted to just generate words.

What we want to do is to make training examples (X, Y) where X = sequence of characters (the window) and Y = following character (like a label).

In text generation, a window of characters which has a given _sequence length_ can be moved across the text in the dataset and except for the last few letters, there will always be a "next letter". 

For our model, we want to capture all sequences and their "label" (next letter), we need something which creates the following training examples from the place name *London*:

| X | Y |
| --- | --- |
| L | O |
| LO | N |
| LON | D |
| LOND | O |
| LONDO | N |
| LONDON | \n |

But since the words are short and differ in length, we need to pad them with spaces so that they are all of the same length.

I decided to add 27 spaces before every place name and then insert however many spaces are required to get to the standard length between the last character and \n. 

A place name that has integer denotation `6829`would now look like this: `1111111111111111111111111116829111111111111111111111110`. 

This way, I could slide a window which comprises 28 chars along each (padded) place name and thus create training examples as in the table above.

## Model
I am using 3 LSTMs and a Dense layer stacked together. The LSTMs detect long-term dependencies between letters in the word. To learn more about LSTMs, either visit [this post]https://medium.com/datathings/the-magic-of-lstm-neural-networks-6775e8b540cd) for some basic understanding, or [this mlreview article](https://medium.com/mlreview/understanding-lstm-and-its-diagrams-37e2f46f1714) for in-depth details. 
I chose a low dropout rate (0.01) since I have a lot of training examples which makes overfitting unlikely. 

### Saving weights
I'm using the checkpoint method to save and reuse weights generated in earlier epochs. Thus, output can be generated without having to train the NN every time. 

## CPU vs. GPU
I have uploaded two versions, depending on which device you have in use. Note that unless you are using the preloaded weights in the CPU version, the model might take very long to train. 


