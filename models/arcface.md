# ArcFace: Additive Angular Margin Loss for Deep Face Recognition

Source: [arxiv](http://arxiv.org/abs/1801.07698)

[//]: # (Image References)

[image1]: ../img/arcface_arcface.png
[image2]: ../img/arcface_geom.png
[image3]: ../img/arcface_logit.png
[image4]: ../img/arcface_analysis.png

To enhance the discriminative power of the Softmax loss we propose a novel supervisor signal, `additive angular margin` (ArcFace), which has a better geometrical interpretation than supervision signals proposed so far.

## ArcFace

![alt text][image1]

Difference from traditional Softmax:
 * we normalize the weights of the last fully-connected layer (by row).
 * we normalize embedding features from convolutional network (this can be done with batchnorm).
 * after these two steps the dot products after the fc layer are basically cosines.
 * for the correct class we calculate cos(theta + m) instead of cos(theta).
 
What this means in English: correct scores are penalized (in a smart way).

```python
    def forward(self, x, target):
        # propagate x thorough the whole network EXCEPT the last fc layer
        x = self.conv_net(x)
        
        # normalize features
        x = F.normalize(x, p=2, dim=1)
        
        # fully-connected layer (notice the normalization)
        cosine = F.linear(x, F.normalize(self.classifier.weight, p=2, dim=1))        
        sine = torch.sqrt(1.0 - torch.pow(cosine, 2) + 1e-6)
        
        # cos(theta + m) = cos(theta) * cos(m) - sin(theta) * sin(m)
        phi = cosine * self.cos_m - sine * self.sin_m
        
        # add margin only for correct class
        one_hot = torch.zeros(cosine.size()).cuda()
        one_hot.scatter_(1, target.data.view(-1, 1), 1)
        one_hot = Variable(one_hot)
        output = (one_hot * phi) + ((1.0 - one_hot) * cosine) 
                
        return output * self.sphere_radius, x
```

## Geometric Interpretation

![alt text][image2]

How to interpret:
 * let's say there are only two classes.
 * let's examine the boundary point for the first class: cos(theta_1 + m) = cos(theta_2).
 * this means that for that point m = theta_2 - theta_1, which is exactly the arc margin!
 
## Target Logit Analysis

![alt text][image3]

![alt text][image4]
