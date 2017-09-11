## Efficient Natural Language Response Suggestion for Smart Reply

Source: [arxiv](https://arxiv.org/abs/1705.00652)

### Dot Product Scoring Model

```Python
class DotProductScoringModel(nn.Module):
    def __init__(self, input_size_q, input_size_a):
        super(DotProductScoringModel, self).__init__()
        
        self.embedding_dim = 320
        
        self.question_embedding = nn.Embedding(input_size_q, self.embedding_dim)
        self.question_embedding.weight.data[0] = 0
        self.answer_embedding = nn.Embedding(input_size_a, self.embedding_dim)
        self.answer_embedding.weight.data[0] = 0
        
        self.fc1_q = nn.Linear(self.embedding_dim, 300)
        self.fc1_a = nn.Linear(self.embedding_dim, 300)
        
        self.fc2_q = nn.Linear(300, 300)
        self.fc2_a = nn.Linear(300, 300)

        self.fc3_q = nn.Linear(300, 500)
        self.fc3_a = nn.Linear(300, 500)
                
        
    def embedd_questions(self, questions):
        questions = self.question_embedding(questions).sum(dim=1).squeeze()
        
        q = F.tanh(self.fc1_q(questions))
        q = F.tanh(self.fc2_q(q))
        q = F.tanh(self.fc3_q(q))
        
        return q
    
    
    def embedd_answers(self, answers):
        answers = self.answer_embedding(answers).sum(dim=1).squeeze()
    
        a = F.tanh(self.fc1_a(answers))
        a = F.tanh(self.fc2_a(a))
        a = F.tanh(self.fc3_a(a))

        return a
    
    
    def forward(self, questions, answers):
        q = self.embedd_questions(questions)
        a = self.embedd_answers(answers)
                
        return (q * a).sum(dim=1)
```

### Multiple Negatives Loss

```python
def multiple_negatives_loss(correct_scores, incorrect_scores):
    denominator = torch.log(torch.exp(correct_scores) + torch.exp(incorrect_scores).sum(dim=1))
    loss = correct_scores - denominator
    return (-loss.sum(dim=0) / batch_size)[0]    
```

```python
for batch in proc_batch_gen:

    my_batch_q, my_batch_a = batch
    
    pad_batch_q = Variable(torch.LongTensor(pad_batch(my_batch_q))).cuda(USE_GPU_NUM)
    pad_batch_a = Variable(torch.LongTensor(pad_batch(my_batch_a))).cuda(USE_GPU_NUM)    

    correct_scores = yanet(pad_batch_q, pad_batch_a)


    neg_batch_q = Variable(torch.LongTensor(pad_batch([my_batch_q[j // curr_batch_len] for j in range(curr_batch_len ** 2) if j % curr_batch_len != 0]))).cuda(USE_GPU_NUM)
    neg_batch_a = Variable(torch.LongTensor(pad_batch([my_batch_a[j % curr_batch_len] for j in range(curr_batch_len ** 2) if (j % curr_batch_len) != (j // curr_batch_len)]))).cuda(USE_GPU_NUM)

    incorrect_scores = yanet(neg_batch_q, neg_batch_a).resize(curr_batch_len, curr_batch_len - 1)
    
    
    loss = multiple_negatives_loss(correct_scores, incorrect_scores)
```
