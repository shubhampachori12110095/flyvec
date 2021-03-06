# FlyVec
> Sparse Binary Word Embeddings Inspired by the Fruit Fly Brain


Code based on the ICLR 2021 paper [Can a Fruit Fly Learn Word Embeddings?](https://arxiv.org/abs/2101.06887).

In this work we use a well-established neurobiological network motif from the mushroom body of the fruit fly brain to learn sparse binary word embeddings from raw unstructured text. This package allows the user to access pre-trained word embeddings and generate sparse binary hash codes for individual words. 

Interactive demos of the learned concepts available at [flyvec.org](http://flyvec.org).

## Install

`pip install flyvec`

## How to use

### Basic Usage
An example below illustrates how one can access the binary word embedding for individual tokens for a default hash length `k=50`. 

```
import numpy as np
from flyvec import FlyVec

model = FlyVec.load()
embed_info = model.get_sparse_embedding("market"); embed_info
```




    {'token': 'market',
     'id': 1180,
     'embedding': array([0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0,
            0, 0, 0, 0, 0, 0, 1, 0, 0, 0, 1, 0, 0, 0, 1, 0, 0, 0, 0, 0, 0, 0,
            1, 0, 0, 0, 0, 0, 0, 0, 0, 0, 1, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0,
            0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 1, 0, 0, 0, 0, 0, 0, 1, 0, 0,
            0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0,
            0, 0, 1, 0, 0, 0, 0, 0, 0, 0, 1, 1, 0, 0, 0, 0, 0, 0, 1, 1, 0, 1,
            0, 0, 0, 1, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0,
            0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0,
            0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0,
            0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 1, 1, 0, 0, 0, 0, 0, 0,
            1, 0, 0, 1, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 1, 0, 0, 0, 1, 0, 0, 0,
            0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 1, 0, 0, 0, 0, 0, 0, 0, 0,
            0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 1, 0, 0, 0, 0, 0, 0, 0, 0,
            1, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0,
            0, 0, 0, 0, 0, 0, 1, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0,
            0, 0, 0, 0, 0, 0, 1, 0, 0, 0, 0, 0, 1, 0, 0, 0, 0, 0, 0, 0, 0, 0,
            1, 0, 0, 0, 1, 0, 0, 0, 0, 0, 0, 0, 0, 0, 1, 0, 0, 0, 0, 1, 0, 0,
            0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 1, 0, 1, 0, 0, 0, 0, 0, 0, 0,
            0, 0, 0, 0], dtype=int8)}



### Changing the Hash Length
The user can obtain the FlyVec embeddings for any hash length using the following example. 

```
small_embed = model.get_sparse_embedding("market", 4); np.sum(small_embed['embedding'])
```




    4



### Handling "unknown" tokens

FlyVec uses a simple, word-based tokenizer. The provided model uses a vocabulary with about 20,000 words, all lower-cased, with special tokens for numbers (`<NUM>`) and unknown words (`<UNK>`). Unknown tokens have the token id of `0`, which can be used to filter unknown tokens.

```
unk_embed = model.get_sparse_embedding("DefNotAWord")
if unk_embed['id'] == 0:
    print("I AM THE UNKNOWN TOKEN DON'T USE ME FOR ANYTHING IMPORTANT")
```

    I AM THE UNKNOWN TOKEN DON'T USE ME FOR ANYTHING IMPORTANT


### Batch generating word embeddings
Embeddings for individual words in a sentence can be obtained using this snippet. 

```
sentence = "Supreme Court dismissed the criminal charges."
tokens = model.tokenize(sentence)
embedding_info = [model.get_sparse_embedding(t) for t in tokens]
embeddings = np.array([e['embedding'] for e in embedding_info])
print("TOKENS: ", [e['token'] for e in embedding_info])
print("EMBEDDINGS: ", embeddings)
```

    TOKENS:  ['supreme', 'court', 'dismissed', 'the', 'criminal', 'charges']
    EMBEDDINGS:  [[0 1 0 ... 0 0 0]
     [0 0 0 ... 0 0 0]
     [0 0 0 ... 0 1 0]
     [0 0 0 ... 0 0 0]
     [0 0 0 ... 0 1 0]
     [0 0 0 ... 0 1 0]]


### FlyVec vocabulary

The vocabulary under the hood uses the gensim `Dictionary` and can be accessed by either IDs (`int`s) or Tokens (`str`s).

```
# The tokens in the vocabulary
print(model.token_vocab[:5])

# The IDs that correspond to those tokens
print(model.vocab[:5])

# The dictionary object itself
model.dictionary;
```

    ['properties', 'a', 'among', 'and', 'any']
    [2, 3, 4, 5, 6]


### Training

Please note that training `FlyVec` on your own custom corpus is not currently supported. 

# Citation

If you use this in your work, please cite:

```
@article{liang2021can,
  title={Can a Fruit Fly Learn Word Embeddings?},
  author={Liang, Yuchen and Ryali, Chaitanya K and Hoover, Benjamin and Grinberg, Leopold and Navlakha, Saket and Zaki, Mohammed J and Krotov, Dmitry},
  journal={arXiv preprint arXiv:2101.06887},
  year={2021}
  url={https://arxiv.org/abs/2101.06887}
}
```
