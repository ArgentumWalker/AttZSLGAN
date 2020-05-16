# ZSL-AttnGAN

### Based on:
https://github.com/EthanZhu90/ZSL_GAN

https://github.com/davidstap/AttnGAN

### Done changes
Main idea of this solution is to integrate two methods with as low as possible interference in their architectures.

ZSL-GAN may be integrated into AttnGan in several parts of it architecture:
1. ZSL-GAN may be integrated in sentence encoder training in order to obtain sentence embeddings that not distinguishable from visual features and allows discriminator model to predict image class.
2. ZSL-GAN may also use words embeddings instead of sentence embedding. This, however, requires implementation of additional model that will map word sequence to a vector with fixed dimensionality (we don't want to predict all visual features by one word).
3. Lastly, we can apply ZSL-GAN instead intermediate generators. This approach have disadvantage of previous one and also requires implementation of additional visual features extractors.

As a result, I choose first approach in order to avoid implementation of additional models.

In AttnGAN there are so-called Conditionong Augmentation model that transforms basic sentence embedding into normal distribution. I decided to replace it with ZSL-GAN generator model to retain overall architecture complexity. Another option was to place it between original sentence embedding and CA model. However, both ZSL-GAN generator and CA model outputs are stochastic. Their placement one after another may lead to unstable learning.

In the original AttnGAN model DAMSM loss applied to pure sentence embedding that should become not distinguishable from visual features. However, in new model ZSL-GAN generator target is exactly to produce such not distinguishable embedding. According to that, DAMSM loss now uses ZSL-GAN sentence embedding instead of RNN one.

While training encoders, vanilla AttnGAN uses only DAMSM loss. However, as we integrate ZSL-GAN into AttnGAN, we also have generator and discriminator losses from ZSL-GAN. The idea is to add generator loss to DAMSM loss with coefficient. Discriminator loss optimized separately as in ZSL-GAN. Note, that unlike in ZSL-GAN where visual features is given, we also optimize image encoder from AttnGAN with ZSL-GAN losses.

For AttnGAN generator training I also added ZSL-GAN generator loss as an additive term with coefficient.

In such setup new model may possibly benefit from joint training instead of two-step training. Also it may benefit from removing sentence term from DAMSM loss as it forces sentence embedding be more deterministic.

### Important TODO's
1. Implement more logging. There are no logging at all. Only console output and images that saved every few batches.