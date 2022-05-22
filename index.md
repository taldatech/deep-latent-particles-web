---
layout: default
---
<style>
div.mw {
  max-width: 1000px;
  margin: auto;
}

.center {
  margin-left: auto;
  margin-right: auto;
}

.centerimg {
  display: block;
  margin-left: auto;
  margin-right: auto;
}

table, th, tr, td{
  border:1px dotted black;
  border-collapse: collapse;
  border-color: #3396FF;
}

tr:nth-child(4), tr:nth-child(7) {
 border-bottom: solid 2px #3396FF;
}

.grid { 
  display: grid;
  grid-template-columns: 1fr 1fr;
  grid-gap: 20px;
  align-items: stretch;
  justify-items: center;
  }
.grid img {
  border: 1px solid #ccc;
  box-shadow: 2px 2px 6px 0px  rgba(0,0,0,0.3);
  max-width: 100%;
}

.textbg {
  background-color: #f18973;
}
</style>

<h1 align="center">
  <br>
Abstract
  <br>
</h1>
<div class="mw">
<blockquote style="text-align: center;">
        We propose a new representation of visual data that disentangles object position from appearance. Our method, termed Deep Latent Particles (DLP), decomposes the visual input into low-dimensional latent ``particles'', where each particle is described by its spatial location and features of its surrounding region.
         To drive learning of such representations, we follow a VAE-based approach and introduce a prior for particle positions based on a spatial-softmax architecture, and a modification of the evidence lower bound loss inspired by the Chamfer distance between particles. 
         We demonstrate that our DLP representations are useful for downstream tasks such as unsupervised keypoint (KP) detection, image manipulation, and video prediction for scenes composed of multiple dynamic objects. In addition, we show that our probabilistic interpretation of the problem naturally provides uncertainty estimates for particle locations, which can be used for model selection, among other tasks.
</blockquote>
</div>

<h1 align="center">
  <br>
Method
  <br>
</h1>

<div class="mw">
<b>Goal:</b> represent an image as a set of particles, where each particle is described by its spatial location \( z_p = (x,y) \), where \( (x,y) \) are the coordinates of pixels, and latent features \(z_{\alpha} \) that describe the visual features in the surrounding region. 

$$ \{(z_p^i, z_{\alpha}^i)\}_{i=1}^{K}. $$

<b>Architecture:</b> 
<br>
DLP comes in two flavors depending on the scene type: (1) <b>Masked model</b> and (2) <b>Object-based model</b>.

<ol>
  <li><b>Masked model:</b> designed for non-object scenes (e.g., faces from CelebA), PointNet++ and Gaussian maps model the local regions around the particles, and the rest (e.g., the background) is propagated from the encdoer (\(\Phi_{bypass} \)).</li>
  <li><b>Object-based model:</b> designed for object-based scenes (e.g., CLEVRER), PointNet++ models the global regions (e.g., the background) and Gaussian maps (optionally) and a separate Glimpse decoder model the objects and their masks.</li>
</ol>

<b>Architecture - Encoder (Posterior):</b>
<br>
The encdoer is composed of two components: (1) <b>Position encoder</b> and (2) <b>Appearance encoder</b>.
<br>
<img src="https://raw.githubusercontent.com/taldatech/deep-latent-particles-web/main/assets/dlp_encoder.gif" style="height:250px" class="centerimg">

<ol>
  <li><b>Position encoder:</b> outputs keypoints -- the spatial location \( z_p = (x,y) \) of interesting areas, where \( (x,y) \) are the coordinates of pixels.</li>
  <li><b>Appearance encoder:</b> extracts patches (or <i>glimpses</i>) of pre-determined size centered around \( z_p \) and encodes them to latent variables \(z_{\alpha} \).</li>
</ol>

<br>

<b>Architecture - Prior:</b>
<br>
The prior addresses the question: what are the interesting areas in the image? Inspired by KeyNet [1], we extract points-of-intereset in the image by applying spatial-Softmax (SSM) over feature maps extracted from patches in the image. 
We term the set of extracted prior keypoints as <i>keypoint proposals</i>. 

<br>

<b>Architecture - Decoder (Likelihood):</b>
<br>
The decoder architecture depends on the scene type as described in the begining.
<br>
<img src="https://raw.githubusercontent.com/taldatech/deep-latent-particles-web/main/assets/dlp_decoder.gif" style="height:250px" class="centerimg">
<ol>
  <li><b>Masked model:</b> PointNet++ and Gaussian maps model the local regions around the particles, and the rest (e.g., the background) is propagated from the encdoer (\(\Phi_{bypass} \)).</li>
  <li><b>Object-based model:</b> PointNet++ models the global regions (e.g., the background) and Gaussian maps (optionally) and a separate Glimpse decoder model the objects and their masks.</li>
</ol>
<br>

<b>Architecture - Putting it All Together:</b>
<br>
<img src="https://raw.githubusercontent.com/taldatech/deep-latent-particles-web/main/assets/dlp_arch_all.PNG" style="height:300px" class="centerimg">
<br>

<b>Training and Optimization:</b>
<br>
The model is optimized as a variational autoencoder (VAE) with objective of maximizing the evidence lower bound (ELBO):

$$ \log p_\theta(x) \geq \mathbb{E}_{q(z|x)} \left[\log p_\theta(x|z)\right] - KL(q(z|x) \Vert p(z)) \doteq ELBO(x) $$

The ELBO is decomposed to the <i>reconstruction error</i> and a <i>KL-divergence</i> regularization term.
<br>
However, here we have two <b>unordered</b> sets (or <i>point clouds</i>) of position latent variables: the posterior keypoints and the keypoint proposals from the prior. 
Note that <i>the number of points in each set may also differ</i>.
<br>
Inspired by the <b>Chamfer distance</b> between two sets \( S_1 \) and \( S_2 \):
$$ d_{CH}(S_1, S_2) = \sum_{x \in S_1}\min_{y \in S_2}||x-y||_2^2 + \sum_{y \in S_2}\min_{x \in S_1}||x-y||_2^2. $$
<img src="https://raw.githubusercontent.com/taldatech/deep-latent-particles-web/main/assets/chamfer_distance.gif" style="height:300px" class="centerimg">
<br>
Animation by Luke Hawkes [2].
<br>
We propose the <b>Chamfer-KL</b>, a <i>novel modification</i> for the KL term:
$$ d_{CH-KL}(S_1, S_2) = \sum_{x \in S_1}\min_{y \in S_2}KL(x \Vert y) + \sum_{y \in S_2}\min_{x \in S_1}KL(x \Vert y). $$
Note that the Chamfer-KL is not a meteric and maintains the properties of the standard KL term.
<br>
Intuitively, the prior proposes interesting locations for keypoints based on SSM and the posterior
picks good locations that align with the reconstruction objective, whilst not being limited by the mean operation of the SSM.
<br>
Our ablative analysis shows that this modification is crucial for the performance of the model, and the method does not work without it.

</div>

<h1 align="center">
  <br>
Results
  <br>
</h1>

<div class="mw">
<b>Unsupervised Keypoint Linear Regression on Face Landmarks:</b>
<br>
The standard benchmark of unsupervised keypoint discovery -- the linear regression error in predicting annotated keypoints from the discovered keypoints on faces from the CelebA and MAFL datasets.
<br>
The input to the regressor is the keypoint coordinates, and since our method naturally provides uncertainty estimate (the variance of the coordinates) we also experiment with adding the varaince as input features to the regressor).
<br>
As can be seen in table below, DLP's performance is state-of-the-art and the full table can be found in the paper.
<table class="center">
  <tr>
    <th>Method</th>
    <th>K (number of unsupervised KP)</th>
    <th>Error on MAFL (lower is better)</th>
  </tr>
  <tr>
    <td>Zhang (Zhang et al., 2018)</td>
    <td> 30 </td>
	<td> 3.16 </td>
  </tr>
  <tr>
    <td>KeyNet (Jakab et al., 2018)</td>
    <td>30</td>
    <td>2.58</td>
  </tr>
  <tr>
    <td></td>
    <td>50</td>
    <td>2.54</td>
  </tr>
  <tr>
    <td>Ours</td>
    <td>25</td>
    <td>2.87</td>
  </tr>
  <tr>
    <td> </td>
    <td>30</td>
    <td><b>2.56</b></td>
  </tr>
  <tr>
    <td> </td>
    <td>50</td>
    <td><b>2.43</b></td>
  </tr>
  <tr>
    <td>Ours+ (with variance features)</td>
    <td>25</td>
    <td><b>2.52</b></td>
  </tr>
  <tr>
    <td> </td>
    <td>30</td>
    <td>2.49</td>
  </tr>
  <tr>
    <td> </td>
    <td>50</td>
    <td>2.42</td>
  </tr>
</table>
<b>Information from Uncertainty:</b>
<br>
We trained DLP with \( K=25 \) particles and used the mean \(\mu \) and the log-variance \( \log(\sigma^2) \) as features for the supervised regression task described above.
<br>
As seen in the table above, DLP outperforms KeyNet with \( K=50 \), even though the number of input features to the regressor is the same.
<br>
The uncertainty can be further used for model selction and for filtering out low-confidence particles (e.g., filtering bounding boxes of objects), please see the paper for more details.
<br>
<b>Particle-based Image Manipulation:</b>
<br>
We have implemented a GUI (see interactive demo below) where one can move the particles around and see their effect on the resulting reconstruction. In addition, the features of each particle can be modified to change its appearance (demonstrated on CLEVRER below).
<br>
<main class="grid">
  <img src="https://raw.githubusercontent.com/taldatech/deep-latent-particles-web/main/assets/celeb_manip_2.gif" alt="CelebA">
  <img src="https://raw.githubusercontent.com/taldatech/deep-latent-particles-web/main/assets/traffic_manip.gif" alt="Traffic">
  <img src="https://raw.githubusercontent.com/taldatech/deep-latent-particles-web/main/assets/clevrer_manip_1.gif" alt="CLEVRER">
  <img src="https://raw.githubusercontent.com/taldatech/deep-latent-particles-web/main/assets/clevrer_manip_2.gif" alt="CLEVRER">
</main>
<br>
<b>Particle-based Video Prediction:</b>
<br>
We present a simple idea for particle-based video prediction -- building a graph from the particles and using a Graph Convolutional Network (GCN) to predict the temporal change of particles. Please see the paper for more details.
<br>
<main class="grid">
  <img src="https://raw.githubusercontent.com/taldatech/deep-latent-particles-web/main/assets/traffic_pred_2.gif" alt="TrafficVP">
  <img src="https://raw.githubusercontent.com/taldatech/deep-latent-particles-web/main/assets/traffic_pred_3.gif" alt="TrafficVP">
  <img src="https://raw.githubusercontent.com/taldatech/deep-latent-particles-web/main/assets/traffic_pred_4.gif" alt="TrafficVP">
  <img src="https://raw.githubusercontent.com/taldatech/deep-latent-particles-web/main/assets/traffic_pred_5.gif" alt="TrafficVP">
</main>

</div>

<h1 align="center">
  <br>
Code & Interactive Demo
  <br>
</h1>

<div class="mw">
Code, pre-trained models and interactive demo are available on <a href="https://github.com/taldatech/deep-latent-particles-pytorch">GitHub</a>:
<br>
<a href="https://github.com/taldatech/deep-latent-particles-pytorch"><img src="https://github.githubassets.com/images/modules/logos_page/GitHub-Mark.png" style="height:200px" class="centerimg"></a>
</div>

<h1 align="center">
  <br>
References
  <br>
</h1>

<div class="mw">
[1] Jakab, Tomas, et al. "Unsupervised learning of object landmarks through conditional image generation." Advances in neural information processing systems 31 (2018).
<br>
[2] Luke Hawkes - <a href="https://www.youtube.com/watch?v=P4IyrsWicfs" class="textbg">A visual representation of the Chamfer distance function</a>.
</div>

