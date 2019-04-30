# Long Time Interval Video Interpolation with Audio Information

### 0205: Yuzi He, Linqiu Li, Xiaofan Zhang, Guoqing Zhou

# **Introduction:**

## Video interpolation has been a popular topic in deep learning. There are a lot of studies about video interpolation, whereas all of them focused on short time interval. Interpolation for video frames with long time interval is a difficult task not only because of the inaccurate estimation of motions but also the underlying intrinsic physical ambiguities. A lot of efforts have been put into accurate capturing of subject movements, whereas getting rid of ambiguities gets less attention.

## Video comes with image parts and audio parts. Image parts are widely deployed to extract motions. However, audio parts are barely used, despite they may contain necessary information to remove ambiguity. Therefore, we incorporate audio information in our network, to achieve better video interpolation results. We deployed our model on a simple scenario with ambiguity, where a ball is moving near a wall with or without a reflection coming from hitting the wall. We have limited position information along with the audio information of this movement. Upon these, we expect we can extract if the ball collides with the wall and fill frames in between.

# ![A picture containing gauge, object, device Description automatically generated](https://github.com/xiaofanzhang1/Long-Time-Interval-Video-Interpolation-with-Audio-Information/blob/master/paths.png)

### Fig. 1. Two possible paths that causes ambiguity

## We generate multiple trajectories of the ball with molecular dynamics and render sequences of images with or without collision to video clips. For each clip, a pulse signal is added to the audio when a collision occurs in the trajectory. Inputs are the initial frame and final frame, together with the audio information of the ball. Outputs are intermediate frames.

# **Data and Model:**

# ![](https://github.com/xiaofanzhang1/Long-Time-Interval-Video-Interpolation-with-Audio-Information/blob/master/model-1.png)

### Fig. 2. The network

## As shown in Fig. 2., our model contains three parts. An encoder consisting of 5 conv layers is used to represent the image information in a smaller latent space V. Second part, which is also the most difficult and important part is to incorporate audio information. We first use a fc layer to extract information from latent variables of encoder. Then we elementwise multiply audio information. we use fc layer again, do interpolation, elementwise multiply audio information. add another fc layer. Finally, a decoder consisting of 5 conv layers is used to get output images.

## For the audio part, a control parameter T, indicating the relative time of intermediate frame, is used in the latent space to linearly interpolate of latent variables from the two input images. To include the audio information, a one-dimension convolutional network is used to preprocess the audio wave. With a mathematical intuition introduced below, we incorporate the audio information, along with the relative time of input and output frames, to a latent space W with the same dimension as V. And elementwise products are applied on the latent variables from W and V for each inputs and output, respectively.

# **Mathematical Intuition**

## Before we add the audio information, we have tested one model on the data without ambiguity. The model uses CNN encoder and do linear interpolation on the latent space V. The data set we chose is the one with one ball moving straight. It is a surprise that this model works well with a simple linear interpolation, indicating that the position information of the ball is encoded in the latent variable $$v\  \in V$$, and the linear interpolation is sufficient to do video interpolation.

## Based on this, to unfold the complexity introduced from the collision, we add a control gate W in the latent space V. The control gate behaves as “flipping” operation on the position of the ball. And the control gate takes the relative time of the frame and the collision event, t and tau respectively. A simple example rule would be as t \< tau, a flip operation is applied on v, otherwise, an identity operation is applied.

# ![](https://github.com/xiaofanzhang1/Long-Time-Interval-Video-Interpolation-with-Audio-Information/blob/master/flipping.jpg)

### Fig. 3. Intuition operation

## The control gate is achieved as first we project the latent variable *v* to a large space, which is one additional fully connected layer in the model.

# ![](https://github.com/xiaofanzhang1/Long-Time-Interval-Video-Interpolation-with-Audio-Information/blob/master/eq1.PNG)

## And then we applied elementwise product between ![](https://github.com/xiaofanzhang1/Long-Time-Interval-Video-Interpolation-with-Audio-Information/blob/master/eq2.PNG), here w is the output of the convolution layer for audio information.

$$
u = w\bigodot\tilde{v}
$$

To match with the original network, $$u$$ is projected back to V with another
fully connected layer.

$$
\tilde{u} = \tilde{\mathbf{P}}u
$$

The new latent variable $$\tilde{u} = \tilde{\mathbf{P}}(w\bigodot\mathbf{P}v)
\equiv Av\$$is equivalent to apply a matrix A on v to decide whether to flip the
position encoded in v or not.

Informal proof:

Suppose we project *v* to the $$R^{n^{2}}$$, we have

$$v \in R^{n},\ \mathbf{P} \in R^{n^{2} \times n},\tilde{\mathbf{P}} \in R^{{n
\times n}^{2}},\ w{\in R}^{n^{2}},\ A = R^{n \times n}$$, and $${\tilde{u}}_{i}
= \sum_{k}^{}{A_{\text{ij}}v_{j}}$$,

we can simply take

$$
\tilde{v} = \mathbf{P}v = \left( v^{T},v^{T},\ \ldots,\ v^{T}\  \right)^{T}
$$

$$
w = {(A_{11},\ A_{12},\ \ldots,\ A_{1n},\ A_{21},\ A_{22},\ \ldots,\ A_{2n},\ A_{31},\ \ldots.,\ A_{\text{nn}})}^{T}
$$

Where $$\mathbf{P =}{\mathbf{(}\mathbf{I}_{\mathbf{n}}\mathbf{,\
}\mathbf{I}_{\mathbf{n}}\mathbf{,\ \ldots,\
}\mathbf{I}_{\mathbf{n}}\mathbf{)}}^{T}$$

Then

$$
u = w\bigodot\tilde{v} = {(A_{11}v_{1},\ A_{12}v_{2},\ \ldots,\ A_{1n}v_{n},\ A_{21}v_{1},\ A_{22}v_{2},\ \ldots,\ A_{2n}v_{n},\ A_{31}v_{1},\ \ldots.,\ A_{\text{nn}}v_{n})}^{T}
$$

We can choose $${(\tilde{\mathbf{P}})}_{\text{ij}} = 1,\text{\ if\ }\left( i - 1
\right)*n < j \leq i*n,\text{\ otherwise}\ 0$$, such that

$$
\tilde{u} = \tilde{\mathbf{P}}u = \tilde{\mathbf{P}}(w\bigodot\mathbf{P}v) = \left( \sum_{k}^{}{A_{1j}v_{j}},\ \sum_{k}^{}{A_{2j}v_{j}},\ldots,\ \sum_{k}^{}{A_{\text{nj}}v_{j}} \right) = Av
$$

# **Results:**

## Our main purpose in this study is to figure out if audio information can help improve the quality of video interpolation.

# ![](https://github.com/xiaofanzhang1/Long-Time-Interval-Video-Interpolation-with-Audio-Information/blob/master/result.png)

### Fig. 4. Result comparing between models with and without audio information

## The results match our expectation. When there exists colliding, the model with audio information works well, whereas the other model without audio information gives vague path. After 200 epoch training, training loss decreases to 4% for the model without using audio information, and 2% for the model with using audio sound. Test loss for model with audio information is about 43.7% that of model without audio.

# **Future work**

## In real world, movements often happen without an audio signal. Other techniques are also necessary to be implemented if we want to interpolate long time interval videos. It is necessary for us to test our network on more complicated cases, for example, more balls colliding with each other in the scenario. We also believe that if the inputs include a few initial frames and final frames instead of a single frame and final frame, the model without audio information would also work well, and the model with audio model would work even better.
