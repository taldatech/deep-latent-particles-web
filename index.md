---
layout: default
---
<style>
div.mw {
  max-width: 1000px;
  margin: auto;
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

<b>Architecture:</b> DLP comes in two flavors depending on the scene type: (1) <b>Masked model</b> and (2) <b>Object-based model</b>.

<ol>
  <li><b>Masked model:</b> designed for non-object scenes (e.g., faces from CelebA), PointNet++ and Gaussian maps model the local regions around the particles, and the rest (e.g., the background) is propagated from the encdoer (\(\Phi_{bypass} \)).</li>
  <li><b>Object-based model:</b> designed for object-based scenes (e.g., CLEVRER), PointNet++ models the global regions (e.g., the background) and Gaussian maps (optionally) and a separate Glimpse decoder model the objects and their masks.</li>
</ol>

<b>Architecture - Encoder (Posterior):</b>

<br>

<b>Architecture - Prior:</b>

<br>

<b>Architecture - Decoder (Likelihood):</b>
<br>

<b>Training and Optimization:</b>

$$ \mathcal{L}_{E_{\phi}}(x) = ELBO(x),$$
<br>
ALOT OF CONTENT<br>
ALOT OF CONTENT<br>
ALOT OF CONTENT<br>
ALOT OF CONTENT<br>
ALOT OF CONTENT<br>
ALOT OF CONTENT<br>
ALOT OF CONTENT<br>
ALOT OF CONTENT<br>
ALOT OF CONTENT<br>
ALOT OF CONTENT<br>
ALOT OF CONTENT<br>
ALOT OF CONTENT<br>
ALOT OF CONTENT<br>
ALOT OF CONTENT<br>
ALOT OF CONTENT<br>
ALOT OF CONTENT<br>
ALOT OF CONTENT<br>
ALOT OF CONTENT<br>
ALOT OF CONTENT<br>
ALOT OF CONTENT<br>
ALOT OF CONTENT<br>
ALOT OF CONTENT<br>
ALOT OF CONTENT<br>
ALOT OF CONTENT
</div>

<h1 align="center">
  <br>
Results
  <br>
</h1>

<div class="mw">
Results Summary
</div>


<h1 align="center">
  <br>
Code & Interactive Demo
  <br>
</h1>

<div class="mw">
Link to GitHub, Open in Colab
</div>

<h1 align="center">
  <br>
References
  <br>
</h1>

<div class="mw">
[1] Jakab, Tomas, et al. "Unsupervised learning of object landmarks through conditional image generation." Advances in neural information processing systems 31 (2018).
<br>
[2] ref2
</div>

