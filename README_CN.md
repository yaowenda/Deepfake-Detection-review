# Deepfake Detection review

> 本文档基于我们的综述论文《[人脸深度伪造检测方法研究综述](https://www.cjig.cn/zh/article/doi/10.11834/jig.240586/)》整理而成。
> 
> **论文发表信息：**
> 
> - 期刊：《中国图象图形学报》(Journal of Image and Graphics)
> - 发表地址：https://www.cjig.cn/zh/article/doi/10.11834/jig.240586/
> - DOI: 10.11834/jig.240586
> 
> **文档说明：**
> - 本文档总结了深度伪造检测领域的主要方法、数据集和评价指标
> - 内容截止至 **2025年1月**，该领域发展迅速，欢迎有志者合作更新此仓库
> - 作者邮箱：1046748784@qq.com

---

![deepfake](assets/deepfake-1768741063136-2.jpeg)

*Falsehood may prevail for a moment, but truth will be proven by time.*

## 📚 目录

- [数据集](#数据集)
- [评价指标](#评价指标)
- [检测方法分类](#检测方法分类)
  - [图像级检测方法](#图像级检测方法)
    - [基于空间域的检测方法](#基于空间域的检测方法)
    - [基于频率域的检测方法](#基于频率域的检测方法)
  - [视频级检测方法](#视频级检测方法)
    - [基于时空不一致的检测方法](#基于时空不一致的检测方法)
    - [基于生物特征的检测方法](#基于生物特征的检测方法)
    - [基于多模态的检测方法](#基于多模态的检测方法)
  - [文本生成图像/视频检测方法](#文本生成图像视频检测方法)
- [研究挑战与瓶颈](#研究挑战与瓶颈)
- [未来研究方向](#未来研究方向)

---

## 数据集

| 数据集名称 | 年份 | 描述 | 规模 | 特点 |
|-----------|------|------|------|------|
| **[Celeb-DF](https://github.com/yuezunli/celeb-deepfakeforensics)** | 2019 | 高质量换脸数据集 | 590个真实视频，5,639个伪造视频 | 高质量伪造，检测难度大，从YouTube收集名人视频 |
| **[FaceForensics++](https://github.com/ondyari/FaceForensics)** | 2019 | 多种伪造方法数据集 | 1,000个真实视频，约5,000个伪造视频 | 包含多种伪造技术（DeepFakes, Face2Face, FaceSwap, NeuralTextures等），多种压缩质量版本 |
| **[WildDeepfake](https://github.com/OpenTAI/wild-deepfake)** | 2021 | 真实场景数据集 | 707个视频，提取7,314个面部序列 | 从互联网收集，更接近真实应用场景，场景和人物多样性高 |

| 数据集名称 | 年份 | 描述 | 规模 | 特点 |
|-----------|------|------|------|------|
| **[DFDC (Deepfake Detection Challenge)](https://ai.facebook.com/datasets/dfdc)** | 2020 | Facebook发布的大规模数据集 | 约23,564个真实视频片段，104,500个伪造视频片段 | 包含多种伪造方法和真实场景，由3,426位演员拍摄，总视频片段超过100,000 |
| **[DeeperForensics-1.0](https://github.com/EndlessSora/DeeperForensics-1.0)** | 2020 | 大规模真实场景人脸伪造检测基准 | 48,475个真实视频，11,000个伪造视频，共60,000个视频，约1,760万帧 | 包含真实扰动，face swapping方法生成，包含隐藏测试集 |
| **[FakeAVCeleb](https://github.com/DashyantSingh/FakeAVCeleb)** | 2021 | 音视频联合伪造数据集 | 500个真实视频，19,500个伪造视频/音视频对 | 包含音频和视频伪造，支持多模态检测，音视频同步分析 |
| **[AV-Deepfake1M](https://arxiv.org/abs/2311.15308)** | 2023 | 大规模音视频数据集 | 286,721个真实视频，860,039个伪造视频 | 大规模、多样性高，支持音视频联合检测，总样本超过100万 |
| **[LAV-DF](https://github.com/ControlNet/LAV-DF)** | 2023 | 局部音频视频伪造数据集 | - | 支持局部伪造检测和定位，细粒度多模态分类 |

### 文本生成图像/视频数据集

| 数据集名称 | 年份 | 描述 | 规模 | 特点 |
|-----------|------|------|------|------|
| **[DiffusionDB](https://github.com/poloclub/diffusiondb)** | 2022 | 大规模文本到图像生成数据集 | 约14,000,000张生成图像（无真实图像） | 由Stable Diffusion生成，包含约180万个不同prompt和超参数设置，用于文本到图像生成研究 |
| **[DiffusionForensics (DIRE)](https://github.com/ZhendongWang6/DIRE)** | 2023 | 扩散模型生成图像检测数据集 | 约40,000个真实图像，约40,000个生成图像 | 覆盖多个域（LSUN-Bedroom, ImageNet, CelebA-HQ等），包含多种diffusion模型生成的图像，用于通用AI生成图像检测 |
| **[DiFF](https://github.com/xaCheng1996/DiFF)** | 2024 | 扩散模型人脸伪造数据集 | 超过500,000张生成图像，包含真实人脸图像 | 使用13种生成方法，在4种不同条件下生成，包含约30,000个prompts，专注于面部伪造检测 |

---

## 评价指标

### 分类性能指标

- **准确率 (Accuracy)**: 正确分类的样本占总样本的比例
- **精确率 (Precision)**: 预测为正例中真正为正例的比例
- **召回率 (Recall)**: 真正例中被正确预测的比例
- **F1分数 (F1-Score)**: 精确率和召回率的调和平均数
- **AUC (Area Under Curve)**: ROC曲线下面积，衡量分类器整体性能

### 定位性能指标

- **AP@阈值 (Average Precision)**: 在特定IoU阈值下的平均精确率
- **mAP (mean Average Precision)**: 多个类别或阈值的平均AP值

### 泛化性能指标

- **跨数据集准确率**: 在一个数据集上训练，在另一个数据集上测试的性能
- **鲁棒性指标**: 对压缩、噪声、裁剪等攻击的抵抗能力

---

## 检测方法分类

### 图像级检测方法

#### 基于空间域的检测方法

基于空间域的检测方法主要关注图像像素级别的特征，通过分析图像的空间分布、纹理、边缘等特征来识别伪造痕迹。

**代表性工作：**(见论文Table 1)

| 方法 | 数据集 | AUC检测结果(%) | 跨数据集AUC(%) | 文章链接 |
|------|--------|---------------|-------------------|---------|
| **Matern et al** | FaceForensics | 86.60 | — | [paper](https://ieeexplore.ieee.org/document/8638330) |
| **Zhou et al** | 自建库 | 92.70 | 自建库: 85.40 | [paper](https://ieeexplore.ieee.org/document/8014963) |
| **Li and Lyu** | DeepfakeTIMIT | 93.20 | — | [paper](https://arxiv.org/abs/1811.00656) |
| **Face X-ray** | FaceForensics++ | 98.52 | Celeb-DF: 80.58 | [paper](https://arxiv.org/abs/1912.13458) |
| **MesoNet** | FaceForensics++ | 98.40(ACC) | — | [paper](https://arxiv.org/abs/1809.00888) |
| **RFM** | Celeb-DF | 99.97 | — | [paper](https://arxiv.org/abs/2104.06609) |
| **Muti-attention** | FaceForensics++ | 99.29 | Celeb-DF: 67.44 | [paper](https://arxiv.org/abs/2103.02406) |
| **Wodajo et al** | DFDC | 99.90 | — | [paper](https://arxiv.org/abs/2307.07036) |
| **Heo et al** | DFDC | 97.80(ACC) | — | [paper](https://link.springer.com/article/10.1007/s10489-022-03867-9) |
| **Nguyen et al** | FaceForensics++ | 99.37 | Celeb-DF: 57.50 | [paper](https://arxiv.org/abs/1810.11215) |
| **TAR** | FaceForensics++ | 99.80(ACC) | 自建库: 89.50 | [paper](https://arxiv.org/abs/2105.06117) |
| **Aghasanli et al** | 自建库 | 99.70(ACC) | 自建库: 84(ACC) | [paper](https://ieeexplore.ieee.org/document/10350382) |
| **multiLID** | 自建库 | 100(ACC) | — | [paper](https://arxiv.org/abs/2307.02347) |
| **Cheng et al** | DiFF | 99.99(ACC) | — | [paper](https://arxiv.org/abs/2401.15859) |
| **Amoroso et al** | 自建库 | 99.68 | 自建库: 99.68 | [paper](https://arxiv.org/abs/2304.00500) |

**发展趋势：**从单一特征到多特征融合，从全局检测到局部定位，从监督学习到自监督/对比学习

#### 基于频率域的检测方法

基于频率域的检测方法通过分析图像的频域特征来识别伪造痕迹，因为生成模型在频域会留下特定的伪影。

**代表性工作：**(见论文Table 2)

| 方法 | 数据集 | AUC检测结果(%) | 跨数据集AUC(%) | 文章链接 |
|------|---------|------|--------|------|
| **Li et al** | FaceForensics++ | 93.40 | Celeb-DF: 79.30 | [paper](https://arxiv.org/abs/2103.09096) |
| **Frank et al** | CelebA | 99.91(ACC) | — | [paper](https://arxiv.org/abs/2003.08685) |
| **F3-Net** | FaceForensics++ | 98.10 | Celeb-DF: 65.17 | [paper](https://arxiv.org/abs/2007.09355) |
| **PEL** | FaceForensics++ | 97.63 | Celeb-DF: 69.18 | [paper](https://arxiv.org/abs/2112.13977) |
| **M2TR** | FaceForensics++ | 99.51 | Celeb-DF: 68.20 | [paper](https://arxiv.org/abs/2104.09770) |
| **MPSM** | FaceForensics++ | 99.46 | DFDC: 76.53 | [paper](https://arxiv.org/abs/2105.02577) |
| **papa et al** | 自建库 | 100(ACC) | — | [paper](https://ieeexplore.ieee.org/document/10156981/) |
| **Liu et al** | DiffusionForensics | 100 | DiffusionDB: 73.21 | [paper](https://ieeexplore.ieee.org/document/10608232) |
| **D4** | 自建库 | 93.00(ACC) | — | [paper](https://arxiv.org/abs/2202.05687) |

**发展趋势：**空间域和频域特征的有效融合，针对不同生成模型的频域特征分析，压缩鲁棒性提升

---

### 视频级检测方法

#### 基于时空不一致的检测方法

视频级检测方法利用时间信息，通过分析帧间的不一致性来识别伪造。

**代表性工作：**(见论文Table 3)

| 方法 | 数据集 | AUC检测结果(%) | 跨数据集AUC(%) | 文章链接 |
|------|---------|------|--------|------|
| **Sabir et al** | FaceForensics++ | 99.60 | — | [paper](https://arxiv.org/abs/1905.00582) |
| **FSSpotter** | FaceForensics++ | 100 | Celeb-DF: 76.26 | [paper](https://ieeexplore.ieee.org/document/9102914/) |
| **Zheng et al** | FaceForensics++ | 99.70 | Celeb-DF: 86.90 | [paper](https://arxiv.org/abs/2108.06693) |
| **Gu et al** | FaceForensics++ | 98.93 | Celeb-DF: 77.65 | [paper](https://ojs.aaai.org/index.php/AAAI/article/view/19955) |
| **STDT** | FaceForensics++ | 99.80 | Celeb-DF: 69.78 | [paper](https://arxiv.org/abs/2207.06612) |
| **TALL-Swin** | FaceForensics++ | 99.87 | Celeb-DF: 90.79 | [paper](https://arxiv.org/abs/2403.10261) |

**发展趋势：**从单帧检测到多帧时序分析，从监督学习到无监督/自监督学习，从全局时序到局部时序不一致性检测

#### 基于生物特征的检测方法

利用人脸生物特征（如眨眼、心率、血流、瞳孔反应等）来检测伪造，因为这些特征在伪造视频中往往不自然。

**代表性工作：**(见论文Table 4)

| 方法 | 数据集 | AUC检测结果(%) | 跨数据集AUC(%) | 文章链接 |
|------|---------|------|--------|------|
| **Ciftci et al** | FaceForensics++ | 91.07(ACC) | Celeb-DF: 86.48(ACC) | [paper](https://arxiv.org/abs/1901.02212) |
| **DeepFakesON-Phys** | DFDC | 98.20 | — | [paper](https://arxiv.org/abs/2010.00400) |
| **Mao and Yang** | FaceForensics++ | 96.13(ACC) | Celeb-DF: 86.57(ACC) | [paper](https://arxiv.org/abs/2110.15561) |
| **Saif et al** | FaceForensics++ | 99.00(AUC) | Celeb-DF: 95.00 | [paper](https://ideas.repec.org/a/eee/tefoso/v205y2024ics0040162524002671.html) |

**发展趋势：**多生物特征融合，低质量视频下的生物特征提取，实时生物特征检测

#### 基于多模态的检测方法

利用音频、视频等多种模态信息（音视频同步性、唇动与音频一致性、融合多模态特征）进行联合检测，通过分析模态间的一致性来识别伪造。

**代表性工作：**(见论文Table 5)

| 方法 | 数据集 | AUC检测结果(%) | 跨数据集AUC(%) | 文章链接 |
|------|---------|------|--------|------|
| **Cai et al** | LAV-DF | 99.00 | — | [paper](https://arxiv.org/abs/2204.06228) |
| **Shahzad et al** | FakeAVCeleb | 94.00(ACC) | — | [paper](https://ieeexplore.ieee.org/document/9980296/) |
| **AVoiD-DF** | FakeAVCeleb | 89.20 | DFDC: 80.60 | [paper](https://ieeexplore.ieee.org/document/10081373/) |
| **AVFF** | FakeAVCeleb | 99.10 | DFDC: 86.20 | [paper](https://arxiv.org/abs/2406.02951) |
| **Yin et al** | FakeAVCeleb | 99.97 | LAV-DF: 63.80 | [paper](https://link.springer.com/article/10.1007/s11263-024-02128-1) |
| **Song et al** | 自建库 | 95.70 | — | [paper](https://arxiv.org/abs/2410.23623) |

**发展趋势：**跨模态特征的有效融合，对异步场景的鲁棒性提升，局部多模态伪造检测和定位，自监督多模态学习

---

### 文本生成图像/视频检测方法

随着文本生成图像/视频技术的流行（如DALL-E、Stable Diffusion、Runway等），针对这类生成内容的检测方法也逐渐发展。检测思路有：分析扩散模型生成图像的特定痕迹、检测文本生成内容的特征模式、识别生成式AI的指纹特征

**代表性工作：**(混杂在上面提到的这些论文中)

| 方法 | 核心思想 | 特点 |  |  |
|------|---------|------|------|------|
| **Aghasanli et al** | 自建库             | 99.70(ACC) | 自建库: 84(ACC)    | [paper](https://ieeexplore.ieee.org/document/10350382) |
| **multiLID**        | 自建库             | 100(ACC)   | —                  | [paper](https://arxiv.org/abs/2307.02347) |
| **Cheng et al**     | DiFF               | 99.99(ACC) | — | [paper](https://arxiv.org/abs/2401.15859) |
| **Amoroso et al** | 自建库 | 99.68 | 自建库: 99.68 | [paper](https://arxiv.org/abs/2304.00500) |
| **papa et al** | 自建库 | 100(ACC) | — | [paper](https://ieeexplore.ieee.org/document/10156981/) |
| **Liu et al** | DiffusionForensics | 100 | DiffusionDB: 73.21 | [paper](https://ieeexplore.ieee.org/document/10608232) |
| **D4** | 自建库 | 93.00(ACC) | — | [paper](https://arxiv.org/abs/2202.05687) |
| **Song et al** | 自建库 | 95.70 | — | [paper](https://arxiv.org/abs/2410.23623) |

**挑战：**扩散模型留下的痕迹更细微、不同生成模型的痕迹差异大、需要针对不同生成技术的专门检测方法

---

## 研究挑战与瓶颈

以下挑战总结自大量论文，可参考文中的引用

1. 泛化能力不足：不同生成方法留下的痕迹差异大；模型过度拟合特定数据集的特征；缺乏通用的伪造特征表示
2. 真实环境下的鲁棒性：伪造痕迹在压缩后可能被削弱；模型在高质量数据上训练，对低质量数据泛化差；缺乏真实场景的多样化训练数据
3. 对抗攻击的脆弱性：检测模型本身存在脆弱性；缺乏对抗训练；检测特征容易被绕过
4. 实时性与计算效率：复杂的特征提取和融合过程；缺乏模型压缩和优化；多模态处理需要更多计算资源
5. 多模态融合的复杂性：不同模态的特征空间差异大；缺乏有效的跨模态对齐方法；对网络延迟、压缩等导致的异步处理不足

---

## 未来研究方向

以下研究方向总结自大量论文，可参考文中的引用

1. 通用化检测方法：学习通用的伪造特征表示，提升跨数据集和跨方法的泛化能力
2. 鲁棒性提升：空间域和频域特征的有效融合；知识蒸馏提升泛化能力；在多样化真实场景数据上训练
3. 实时检测系统：模型压缩和量化、知识蒸馏、边缘计算部署等
4. 多模态深度融合：跨模态对齐和正则化；对异步场景的鲁棒性；自监督多模态学习；局部多模态伪造检测和定位
5. 对抗鲁棒性：对抗训练、可解释性分析、防御机制设计
6. 可解释性检测：可视化伪造区域、解释检测依据、提升用户信任度
7. 公平性和多样性：多样化的训练数据、减少对不同人群的偏差、公平性评估指标

---

