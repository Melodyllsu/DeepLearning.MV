# Quantization and Training of Neural Networks for Efficient Integer-Arithmetic-Only Inference
- by google  
## 主要贡献
1. We provide a quantization scheme (section 2.1) that 
quantizesh both weights and activations as 8-bit integers, 
and just a few parameters (bias vectors) as 32-bit integers.

2. We provide a quantized inference framework that is efficiently
implementable on integer-arithmetic-only hardware
such as the Qualcomm Hexagon (sections 2.2, 2.3),
and we describe an efficient, accurate implementation on
ARM NEON (Appendix B).
3. We provide a quantized training framework (section 3)
co-designedwith our quantized inference to minimize the
loss of accuracy from quantization on real models.
4. We apply our frameworks to efficient classification and
detection systems based on MobileNets and provide
benchmark results on popular ARM CPUs (section 4)
that show significant improvements in the latency-vsaccuracy
tradeoffs for state-of-the-art MobileNet architectures,
demonstrated in ImageNet classification [3],
COCO object detection [23], and other tasks.

