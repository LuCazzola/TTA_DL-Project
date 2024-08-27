# Test Time Adaptation (TTA) project 

Test Time Adaptation (TTA) explores the possibility to improve a model's performaces working at test time instead of fine tuning it in a "traditional" way. That can be a really effective and helpfull practice mostly for 2 reasons:
1) 💥 Fine tuning itself might be not so straight forward. It really depends on the architecture, but it can be challenging.
2) 💸 Big models require non neglectable computational capacity & data to work with. **(Lots of money)**.

Our obective is to implement a TTA solution to improve an existent image classifier.

<br>

## Design
The backbone model of choice is [**Contrastive Language–Image Pre-training (CLIP)**](https://openai.com/index/clip/), a well known model by OpenAI trained with the contrastive learning paradigma, capable of making zero-shot classification.

<br>

A possible TTA solution for CLIP as [**Test-Time Prompt Tuning (TPT)**](https://arxiv.org/abs/2209.07511)

<br>
<div align=center><img src="imgs/TPT.png" width="800" /></div>
<br>

What TPT does is basically:
* Consider one image at time and augment it N times.
* Push the augmented images and the original one togheter with a set of prompts through the CLIP image and text encoders.
* Compute the entropy of all augmentations + the original and keep the best 10% (minimizing the entropy).
* Average the top 10% distributions obtaining a marginal distribution, so compute again the (marginal) entropy.
Prompts can be either handcrafted ("a photo of a {label}" or whatever) or learned via promp learner such as [**CoOp**](https://arxiv.org/abs/2109.01134). Adding a prompt learner also adds the possibility to actually use the computed marginal entropy as our model's loss function.

<br>

## Our Contribution

For the most part we focussed on finding better alternatives to the image augmentation methods proposed in TPT :

#### Image Augmentations
1) **PreAugment** : applies only random crop to the image
2) [**AugMix**](https://arxiv.org/abs/1912.02781)     : the method used in the original TPT implementation, technique which mixes randomly generated augmentations and
uses a Jensen-Shannon loss to enforce consistency
3) [**AutoAugment**](https://arxiv.org/abs/1805.09501) : a reinforcement learning based method which augment an image according to the one maximizing accuracy (trained on ImageNet)
4) **DiffusionAugment** : based on using a [diffusion model](https://huggingface.co/lambdalabs/sd-image-variations-diffusers) to generate augmentations from the input image

**N.B.** $\rightarrow$ implementation matters! checkout the provided notebook

<br>

Testing on ImageNet-A we scored :

<div align=center>

| Augmentation Technique | Avg Accuracy (%)  |
| ---------------------- | ----------------- |
| PreAugment             | 27.51             |
| AugMix                 | 28.80             |
| **AutoAugment**        | **30.36**         |
| DiffusionAugment       | check notebook!!! |
</div>





