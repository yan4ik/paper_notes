# Attention Is All You Need

Source: [paper](https://arxiv.org/abs/1706.03762)

We propose a new simple network architecture, the `Transformer`, based solely on attention mechanisms, dispensing with recurrence and convolutions entirely.

## Building Blocks

Great source: [The Illustrated Transformer](http://jalammar.github.io/illustrated-transformer/)

### Scaled Dot-Product Attention

**Big Idea:** combine embeddings of a sequence in such a way that every new embedding "knows" about every other embedding.

```python
def attention(query, key, value):

    d_k = query.size(-1)
    
    scores = torch.matmul(query, key.transpose(-2, -1)) \
             / math.sqrt(d_k)
   
    p_attn = F.softmax(scores, dim = -1)

    return torch.matmul(p_attn, value)
```

We have an (unordered) sequence of embeddings.

Transform these embeddings into queries, keys and values (just multiply by 3 parameter matrices).

Then for each element in the sequence do the following:
 * get the query vector for this element.
 * calculate "similarity" weights for all elements in the sequence - dot product between the query vector and all keys.
 * sum value vectors with weights from the previous step.
 
The result of all of this is just a new sequence of embeddings.

**Note:** in practice these steps are done simultaneously.

### Multi-Headed Attention

Just calculate several such new embeddings for a given sequence.

Then concat them and pool through an fc layer.
