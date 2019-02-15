---
mathjax: false
tags: project usc imitation learning
title: Humanoid Imitation Learning from Diverse Sources
---
<img src="https://uscresl.github.io/humanoid-gail/architecture.svg" class="image mod-full-width" />

[This link](https://uscresl.github.io/humanoid-gail/) describes our experience implementing a system which learns locomotion skills for humanoid skeletons from imitation, and all of the supporting infrastructure and data processing necessary to do so. Our system employs a generative adversarial imitation learning (GAIL) architecture, which is a type of generative adversarial network. We successfully trained our GAIL to control a custom-designed humanoid skeleton, using expert demonstrations from a reinforcment-learned (RL) policy using that skeleton. We also explored several methods for deriving real human motion demonstrations from video and developed a preprocessing pipeline for motion capture data. Our system is work-in-progress, making it the foundation for several possible future research projects.

The complete post can be found [here](https://uscresl.github.io/humanoid-gail/).
