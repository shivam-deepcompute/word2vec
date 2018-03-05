# Word2Vec

## Introduction

Word2Vec is great tool for word embeddings, i.e, vector representation of a word.

### What is word vector

"Machines find difficulty in understanding the text", well thats not very accurate, there exists various machine learning models which are able to digest raw text. But to do some learning on text we need some form of encoding, and for that we have learned word representated as vector which in fact capture meaningful syntactic and semantic regularities in a very simple way.

In other words, its easier to get essence of word in vector representation(just some number).

### The Algorithm
The algorithm uses a simple neural network which has an input layer, an output layer and a hidden layer in between them.

![Image result for syn0 and syn1 in word2vec](https://raw.githubusercontent.com/qiangsiwei/blog/gh-pages/_figures/2016-05-04-word2vec/2016-05-04-word2vec_1.png)

The image above visualises the neural network. In word2vec, the `Input Layer` is termed as `syn0` and `Output Layer` as `syn1`.

When we perform vector generation for text we get two file -

- vectors.bin - This file contains we vector representation of word.
- vocab.txt - All the unique words and their frequency.

### How we got `vectors.bin`?

When the words are passed to the input layer, it tries to predict the context word, i.e, suppose the text corpus contains `Taj Mahal`, Taj and Mahal will pass through the input layer and the output layer will try to predict the context for "Taj", which is "Mahal" based on the vicinity they appear in the text corpus. And accordingly the weights are adjusted in the hidden layer using back-propagation to input layer, so that the algorithm predicts this context with certain degree of accuracy.

When the network has learnt all the vectors using the above process, the algorithm dumps the input layer(syn0) as vectors.bin

### What about syn1?

Well, syn1 is destroyed in the original implementation after vector learning is done.

### But, we saved syn1

In this implementation we also dump out the syn1.

### Why? Because-

There are situation in which we need to regenerate the entire vector even for small new text corpus.

In that case we need the context matrix, i.e, syn1 has to be present there because even if we generate the vector for new tokens and merge them in the existing vector space, the new vectors will not be inline with context of the existing vector space.

So, with help of `syn1`, we will give new tokens the context that they should have in the vector space and adjust their position in the vector space accordingly.

This is termed as incremental word vector generation.

### Format of dumped syn0 and syn1

The format of syn0 and syn1 is not exactly the same as of dumped by original word2vec, we are directly dumping it just the vectors with out any other information in it like, token name, total vectors, and dimension.

### What problem did it solve

Well, you dont need to regenerate the entire vector space with entire data and keeping on waiting. New sentence comes vectors are generated for new tokens, push them to existing vector space with the context.

## Using Word2vec

### Make word2vec ready 

`git clone https://github.com/deep-compute/word2vec`
`cd word2vec`
`make Makefile`

### Usage

```
Parameters for training:
    -train <file>
            Use text data from <file> to train the model
    -output <file>
            Use <file> to save the resulting word vectors / word clusters
    -output-syn1 <file>
            Use <file> to save the syn1 matrix (syn0 is the word vectors)
    -size <int>
            Set size of word vectors; default is 100
    -window <int>
            Set max skip length between words; default is 5
    -sample <float>
            Set threshold for occurrence of words. Those that appear with higher frequency in the training data will be randomly down-sampled; default is 1e-3, useful range is (0, 1e-5)
    -hs <int>
            Use Hierarchical Softmax; default is 0 (not used)
    -negative <int>
            Number of negative examples; default is 5, common values are 3 - 10 (0 = not used)
    -threads <int>
            Use <int> threads (default 12)
    -iter <int>
            Run more training iterations (default 5)
    -min-count <int>
            This will discard words that appear less than <int> times; default is 5
    -alpha <float>
            Set the starting learning rate; default is 0.025 for skip-gram and 0.05 for CBOW
    -classes <int>
            Output word classes rather than word vectors; default number of classes is 0 (vectors are written)
    -debug <int>
            Set the debug mode (default = 2 = more info during training)
    -binary <int>
            Save the resulting vectors in binary moded; default is 0 (off)
    -save-vocab <file>
            The vocabulary will be saved to <file>
    -read-vocab <file>
            The vocabulary will be read from <file>, not constructed from the training data
    -cbow <int>
            Use the continuous bag of words model; default is 1 (use 0 for skip-gram model)
```

### Example

```
./word2vec -train data.txt -output vec -size 100 - output-syn1 context.bin -window 5 -sample 1e-4 -negative 5 -binary 1 -cbow 1 -iter 3
```



