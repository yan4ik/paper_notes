# Attention Is All You Need

Source: [paper](https://arxiv.org/abs/1706.03762)

We propose a new simple network architecture, the `Transformer`, based solely on attention mechanisms, dispensing with recurrence and convolutions entirely.

## Building Blocks

Great source: [The Illustrated Transformer](http://jalammar.github.io/illustrated-transformer/)

### Scaled Dot-Product Attention

**Big Idea:** combine embeddings of a sequence in such a way that every new embedding "knows" about every other embedding.

```python
class Attention(torch.nn.Module):

    def __init__(self, embedding_dim, attention_dim):
        
        super(Attention, self).__init__()
        
        self.attention_dim = attention_dim
        
        self.query_t = torch.nn.Linear(embedding_dim, attention_dim)
        self.key_t = torch.nn.Linear(embedding_dim, attention_dim)
        self.value_t = torch.nn.Linear(embedding_dim, attention_dim)
        
        
    def forward(self, x):
        
        # (batch_size, seq_len, attention_dim)
        queries = self.query_t(x)
        keys = self.key_t(x)
        values = self.value_t(x)
        
        # (batch_size, seq_len, seq_len)
        scores = queries @ keys.transpose(-2, -1) / attention_dim**0.5
        scores = torch.nn.functional.softmax(scores, dim =-1)

        # (batch_size, seq_len, attention_dim)
        new_embedding = scores @ values
        
        return new_embedding
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

```python
class MultiHeadAttention(torch.nn.Module):

    def __init__(self, num_heads, model_dim):
        
        super(MultiHeadAttention, self).__init__()
        
        self.num_heads = num_heads
        self.model_dim = model_dim
        self.attention_dim = model_dim // num_heads
        
        self.query_t = torch.nn.Linear(self.model_dim, self.model_dim)
        self.key_t = torch.nn.Linear(self.model_dim, self.model_dim)
        self.value_t = torch.nn.Linear(self.model_dim, self.model_dim)
        
        self.out = torch.nn.Linear(model_dim, model_dim)

        
    def forward(self, x):
        
        batch_size, seq_len = x.shape[:2]
        
        # (batch_size, seq_len, num_heads, attention_dim)
        queries = self.query_t(x).view(batch_size, seq_len, self.num_heads, self.attention_dim)
        keys = self.key_t(x).view(batch_size, seq_len, self.num_heads, self.attention_dim)
        values = self.value_t(x).view(batch_size, seq_len, self.num_heads, self.attention_dim)
        
        # (batch_size, num_heads, seq_len, attention_dim)
        queries = queries.transpose(1, 2)
        keys = keys.transpose(1, 2)
        values = values.transpose(1, 2)
        
        # Key point: all matrix multiplications in attention calculation
        # are broadcasted across first two dims - (batch_size, num_heads).
        #
        # It's exactly like in non multi-head case, just now we broadcast
        # against two dimensions (not just batch_size).
        
        # (batch_size, num_heads, seq_len, seq_len)
        scores = queries @ keys.transpose(-2, -1) / attention_dim**0.5
        scores = torch.nn.functional.softmax(scores, dim =-1)

        # (batch_size, num_heads, seq_len, attention_dim)
        new_embedding = scores @ values
        
        # (batch_size, seq_len, model_dim)
        concat = new_embedding.transpose(1,2).contiguous().view(batch_size, -1, self.model_dim)
        
        return self.out(concat)
```
