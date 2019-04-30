# Long-Time-Interval-Video-Interpolation-with-Audio-Information
### 0205: Yuzi He, Linqiu Li, Xiaofan Zhang, Guoqing Zhou

## Introduction:
#### Video interpolation has been a popular topic in deep learning. There are a lot of studies about video interpolation, whereas all of them focused on short time interval. Interpolation for video frames with long time interval is a difficult task not only because of the inaccurate estimation of motions but also the underlying intrinsic physical ambiguities. A lot of efforts have been put into accurate capturing of subject movements, whereas getting rid of ambiguities gets less attention. 
#### Video comes with image parts and audio parts. Image parts are widely deployed to extract motions. However, audio parts are barely used, despite they may contain necessary information to remove ambiguity. Therefore, we incorporate audio information in our network, to achieve better video interpolation results. We deployed our model on a simple scenario with ambiguity, where a ball is moving near a wall with or without a reflection coming from hitting the wall. We have limited position information along with the audio information of this movement. Upon these, we expect we can extract if the ball collides with the wall and fill frames in between.
# ![two paths]()
