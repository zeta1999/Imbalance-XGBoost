# Xgboost-With-Imbalance-And-Focal-Loss
This small project includes the codes of Weighted Imbalance Loss and Focal Loss [1] for Xgboost [2](<\url> https://github.com/dmlc/xgboost) in 2-class classification problems. The principal reason for us to use Weighted Imbalance Loss and Focal Loss is to address the problem of label-imbalanced data, which could significantly degrade the performance of Xgboost. The original Xgboost program provides a convinient way to customize the loss function, but one will be needing to compute the first+second order derivatives and implement them. The major contribution of the project to the drivation of the gradients and the implementations of them.<br/>

### Updates June/27/2019
**Special thank to @icegrid and @shaojunchao for help correct errors in the previous versions. The codes are now updated to version 0.5 and it now allows users to specify the weighted parameter \alpha and focal parameter \gamma outside the script. An API might be released later.** <br />

```
pip install something
```

For both of the loss functions, since the task is 2-class classification, the activation would be sigmoid: <br />
<a href="https://www.codecogs.com/eqnedit.php?latex=y_{i}&space;=&space;\frac{1}{1&plus;\text{exp}(-z_{i})}" target="_blank"><img src="https://latex.codecogs.com/gif.latex?y_{i}&space;=&space;\frac{1}{1&plus;\text{exp}(-z_{i})}" title="y_{i} = \frac{1}{1+\text{exp}(-z_{i})}" /></a> <br />
And below the two types of loss will be discussed respectively. <br />
### 1. Weighted Imbalance (Cross-entropoy) Loss
And combining with $\hat{y}$, which are the true labels, the weighted imbalance loss for 2-class data could be denoted as: <br />
<a href="https://www.codecogs.com/eqnedit.php?latex=l_{w}&space;=&space;-\sum_{i=1}^{m}(\alpha\hat{y}_{i}\text{log}(y_{i})&plus;(1-\hat{y}_{i})\text{log}(1-y_{i})))" target="_blank"><img src="https://latex.codecogs.com/gif.latex?l_{w}&space;=&space;-\sum_{i=1}^{m}(\alpha\hat{y}_{i}\text{log}(y_{i})&plus;(1-\hat{y}_{i})\text{log}(1-y_{i})))" title="l_{w} = -\sum_{i=1}^{m}(\alpha\hat{y}_{i}\text{log}(y_{i})+(1-\hat{y}_{i})\text{log}(1-y_{i})))" /></a>
<br />
Where $\alpha$ is the 'imbalance factor'. And $\alpha$ value greater than 1 means to put extra loss on 'classifying 1 as 0'.<br />
The gradient would be: <br />
<a href="https://www.codecogs.com/eqnedit.php?latex=\frac{\partial&space;L_{w}}{\partial&space;z_{i}}&space;=&space;-\alpha^{\hat{y}_{i}}(\hat{y}_{i}-y_{i})" target="_blank"><img src="https://latex.codecogs.com/gif.latex?\frac{\partial&space;L_{w}}{\partial&space;z_{i}}&space;=&space;-\alpha^{\hat{y}_{i}}(\hat{y}_{i}-y_{i})" title="\frac{\partial L_{w}}{\partial z_{i}} = -\alpha^{\hat{y}_{i}}(\hat{y}_{i}-y_{i})" /></a>  <br />
And the second order gradient would be: <br />
<a href="https://www.codecogs.com/eqnedit.php?latex=\frac{\partial&space;L_{w}^{2}}{\partial^{2}&space;z_{i}}&space;=&space;\alpha^{\hat{y}_{i}}(1-y_{i})(y_{i})" target="_blank"><img src="https://latex.codecogs.com/gif.latex?\frac{\partial&space;L_{w}^{2}}{\partial^{2}&space;z_{i}}&space;=&space;\alpha^{\hat{y}_{i}}(1-y_{i})(y_{i})" title="\frac{\partial L_{w}^{2}}{\partial^{2} z_{i}} = \alpha^{\hat{y}_{i}}(1-y_{i})(y_{i})" /></a>   <br />

### 2. Focal Loss
The focal loss is proposed in [1] and the expression of it would be: <br />
<a href="https://www.codecogs.com/eqnedit.php?latex=\dpi{150}&space;L_{w}&space;=&space;-\sum_{i=1}^{m}\hat{y}_{i}(1-y_{i})^{\gamma}\text{log}(y_{i})&space;&plus;&space;(1-\hat{y}_{i})y_{i}^{\gamma}\text{log}(1-y_{i})" target="_blank"><img src="https://latex.codecogs.com/gif.latex?\dpi{150}&space;L_{w}&space;=&space;-\sum_{i=1}^{m}\hat{y}_{i}(1-y_{i})^{\gamma}\text{log}(y_{i})&space;&plus;&space;(1-\hat{y}_{i})y_{i}^{\gamma}\text{log}(1-y_{i})" title="L_{w} = -\sum_{i=1}^{m}\hat{y}_{i}(1-y_{i})^{\gamma}\text{log}(y_{i}) + (1-\hat{y}_{i})y_{i}^{\gamma}\text{log}(1-y_{i})" /></a> <br />
The first order gradient would be: <br />
<a href="https://www.codecogs.com/eqnedit.php?latex=\dpi{150}&space;\small&space;{\frac{\partial&space;L_{w}}{\partial&space;z_i}&space;=&space;\gamma(y_i&plus;\hat{y}_{i}-1)(\hat{y}_{i}&plus;(-1)^{\hat{y}}y_{i})^{\gamma}\text{log}(1-\hat{y}_{i}-(-1)^{\hat{y}_{i}}y_{i})&space;&plus;&space;(-1)^{\hat{y}_i}(\hat{y}_{i}&plus;(-1)^{\hat{y}_i}y_{i})^{\gamma&plus;1}}" target="_blank"><img src="https://latex.codecogs.com/gif.latex?\dpi{150}&space;\small&space;{\frac{\partial&space;L_{w}}{\partial&space;z_i}&space;=&space;\gamma(y_i&plus;\hat{y}_{i}-1)(\hat{y}_{i}&plus;(-1)^{\hat{y}}y_{i})^{\gamma}\text{log}(1-\hat{y}_{i}-(-1)^{\hat{y}_{i}}y_{i})&space;&plus;&space;(-1)^{\hat{y}_i}(\hat{y}_{i}&plus;(-1)^{\hat{y}_i}y_{i})^{\gamma&plus;1}}" title="\small {\frac{\partial L_{w}}{\partial z_i} = \gamma(y_i+\hat{y}_{i}-1)(\hat{y}_{i}+(-1)^{\hat{y}}y_{i})^{\gamma}\text{log}(1-\hat{y}_{i}-(-1)^{\hat{y}_{i}}y_{i}) + (-1)^{\hat{y}_i}(\hat{y}_{i}+(-1)^{\hat{y}_i}y_{i})^{\gamma+1}}" /></a>    <br />
And the second order gradient would be a little bit complex. To simplify the expression, we firstly denotes the terms in the 1-st order gradient as the following notations: <br />
<a href="https://www.codecogs.com/eqnedit.php?latex=\dpi{150}&space;\begin{cases}&space;g_1&space;=&space;y_{i}(1-y_{i})\\&space;g_2=&space;\hat{y}_i&space;&plus;&space;(-1)^{\hat{y}_i}y_{i}\\&space;g_3&space;=&space;y_i&space;&plus;&space;\hat{y}_{i}-1\\&space;g_4&space;=&space;1-\hat{y}_i-(-1)^{\hat{y}_i}y_i\\&space;g_5&space;=&space;\hat{y}_i&space;&plus;&space;(-1)^{\hat{y}_i}&space;y_i&space;\end{cases}" target="_blank"><img src="https://latex.codecogs.com/gif.latex?\dpi{150}&space;\begin{cases}&space;g_1&space;=&space;y_{i}(1-y_{i})\\&space;g_2=&space;\hat{y}_i&space;&plus;&space;(-1)^{\hat{y}_i}y_{i}\\&space;g_3&space;=&space;y_i&space;&plus;&space;\hat{y}_{i}-1\\&space;g_4&space;=&space;1-\hat{y}_i-(-1)^{\hat{y}_i}y_i\\&space;g_5&space;=&space;\hat{y}_i&space;&plus;&space;(-1)^{\hat{y}_i}&space;y_i&space;\end{cases}" title="\begin{cases} g_1 = y_{i}(1-y_{i})\\ g_2= \hat{y}_i + (-1)^{\hat{y}_i}y_{i}\\ g_3 = y_i + \hat{y}_{i}-1\\ g_4 = 1-\hat{y}_i-(-1)^{\hat{y}_i}y_i\\ g_5 = \hat{y}_i + (-1)^{\hat{y}_i} y_i \end{cases}" /></a> <br />
Using the above notations, the 1-st order drivative will be: <br />
<a href="https://www.codecogs.com/eqnedit.php?latex=\dpi{150}&space;\large&space;\frac{\partial&space;L_w}{\partial&space;z_i}&space;=&space;\gamma&space;g_3&space;g_2^{\gamma}&space;\text{log}(g_4)&space;&plus;&space;(-1)^{\hat{y_i}}g_5^{\gamma&space;&plus;&space;1}" target="_blank"><img src="https://latex.codecogs.com/gif.latex?\dpi{150}&space;\large&space;\frac{\partial&space;L_w}{\partial&space;z_i}&space;=&space;\gamma&space;g_3&space;g_2^{\gamma}&space;\text{log}(g_4)&space;&plus;&space;(-1)^{\hat{y_i}}g_5^{\gamma&space;&plus;&space;1}" title="\large \frac{\partial L_w}{\partial z_i} = \gamma g_3 g_2^{\gamma} \text{log}(g_4) + (-1)^{\hat{y_i}}g_5^{\gamma + 1}" /></a> <br />
Then the 2-nd order derivative will be: <br />
<a href="https://www.codecogs.com/eqnedit.php?latex=\dpi{150}&space;\frac{\partial^{2}&space;L}{\partial&space;z_{i}^{2}}&space;=&space;g_{1}\{\gamma[(g_2^{\gamma}&plus;\gamma&space;(-1)^{\hat{y}_{i}}g_3&space;g_2^{\gamma&space;-&space;1})\text{log}(g_4)-\frac{(-1)^{\hat{y}_i}g_3&space;g_2^{\gamma}}{g_4}]&space;&plus;&space;(\gamma&plus;1)g_5^{\gamma}\}" target="_blank"><img src="https://latex.codecogs.com/gif.latex?\dpi{150}&space;\frac{\partial^{2}&space;L}{\partial&space;z_{i}^{2}}&space;=&space;g_{1}\{\gamma[(g_2^{\gamma}&plus;\gamma&space;(-1)^{\hat{y}_{i}}g_3&space;g_2^{\gamma&space;-&space;1})\text{log}(g_4)-\frac{(-1)^{\hat{y}_i}g_3&space;g_2^{\gamma}}{g_4}]&space;&plus;&space;(\gamma&plus;1)g_5^{\gamma}\}" title="\frac{\partial^{2} L}{\partial z_{i}^{2}} = g_{1}\{\gamma[(g_2^{\gamma}+\gamma (-1)^{\hat{y}_{i}}g_3 g_2^{\gamma - 1})\text{log}(g_4)-\frac{(-1)^{\hat{y}_i}g_3 g_2^{\gamma}}{g_4}] + (\gamma+1)g_5^{\gamma}\}" /></a>
<br />

### Scared by the above equations? It's ok
That's the reason I upload this to github: I have done the job and coded them into the script, you can simply use it and ignore the equations! Remember to call *Xgboost_classsifier_sklearn* class and specify the parameter *special_objective* when implementing the class to an object. The prarameters $\alpha$ and $\gamma$ can be specified by giving a value when constructing the object, and you are able to perform validation for these parameters with the Gridsearch method of SK-learn.

# Enjoy Using!
@author: Chen Wang, Dept. of Computer Science, School of Art and Science, Rutgers University (previously affiliated with University College London, Sichuan University and Northwestern Polytechnical University) <br/>
@version: 0.5

## References
[1] Lin, Tsung-Yi, Priyal Goyal, Ross Girshick, Kaiming He, and Piotr Dollár. "Focal loss for dense object detection." IEEE transactions on pattern analysis and machine intelligence (2018). <br/>
[2] Chen, Tianqi, and Carlos Guestrin. "Xgboost: A scalable tree boosting system." In Proceedings of the 22nd acm sigkdd international conference on knowledge discovery and data mining, pp. 785-794. ACM, 2016.
