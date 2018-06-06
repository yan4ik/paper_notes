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
