# Far-field Simulation for Low-NA Metalens

## 项目简介

本项目是一个**早期 demo / work in progress**，目标是搭建一个最小化的 Python 工作流，用于模拟 low numerical aperture (low-NA) metalens 的远场 (far-field) 强度分布。

在 low-NA 条件下，可以合理使用 **标量衍射近似 (scalar diffraction approximation)** 与 **傅里叶光学 (Fourier optics)** 方法来估计远场。该近似并非普遍适用于所有 metalens —— 随着 NA 增大、结构特征尺寸接近波长或偏振耦合变得显著时，标量近似的误差会增大，需要改用全矢量仿真。

**当前仓库尚无可运行的仿真代码。** 以下内容描述了项目的设计意图、物理背景与后续计划。

## 项目动机

- 建立 metalens 相位分布 (phase profile) 与远场强度分布 (far-field intensity) 之间的直观联系。
- 在进入 FDTD、RCWA 等计算量更大的全波仿真之前，先建立对傅里叶光学与数值采样的直觉。
- 检查 aperture shape、phase profile、sampling interval、FFT convention 等因素对远场结果的影响。
- 作为后续更完整的 metalens 仿真项目的最小起点。

## 物理模型与近似条件

### 当前设计思路（尚未实现）

- **Low numerical aperture**：假设 NA 足够小，使得傍轴 (paraxial) 与标量近似可接受。
- **Scalar diffraction approximation**：忽略电磁场的矢量特性，用单一标量复振幅描述场。
- **Monochromatic field**：默认单波长，除非后续代码显式支持多波长。
- **Thin phase mask / aperture transmittance 模型**：metalens 被建模为一个薄的相位掩模，透过率函数为 $t(x, y) = A(x, y) \exp[i \phi(x, y)]$，其中 $A(x, y)$ 为孔径函数，$\phi(x, y)$ 为相位分布。
- **Fraunhofer-style propagation**：在满足远场条件时，远场复振幅正比于孔径上复振幅的傅里叶变换 (Fourier transform)。远场强度可通过 FFT 计算。

### 重要限制

- **不适用**于强非局域 (strongly nonlocal) metasurface。
- **不能替代** full-wave simulation (FDTD / RCWA / FEM)。
- 默认**不处理** high-NA vectorial focusing。
- 默认**不包含** fabrication errors 或制造公差建模。
- 坐标约定、强度归一化方式、FFT shift / ifftshift 约定、采样规则**尚待定义和验证**。
- 不要将 "far field" 简单等同于 "focal plane"，除非在具体光学配置中明确说明。

## 当前仓库状态

**占位 / 初始化阶段。** `simulation.ipynb` 中仅包含占位代码，尚未实现任何与 metalens 或远场仿真相关的功能。

## 仓库结构

```
far-field_simulation-_for-_low-_NA-_metalens/
├── .git/               # Git 版本控制
├── README.md           # 本文件
└── simulation.ipynb    # 主 notebook（当前仅含占位代码）
```

- `simulation.ipynb`：计划中的主仿真 notebook，目前仅含占位 Python 单元格。

## 安装方式（计划中）

当前仓库无可运行代码，以下为**后续计划的安装方式**：

```bash
# 创建虚拟环境
python -m venv venv

# 激活虚拟环境 (Windows PowerShell)
.\venv\Scripts\Activate.ps1

# 激活虚拟环境 (Linux / macOS)
source venv/bin/activate
```

根据后续代码实际使用的库，可能需要安装的依赖（**暂定，取决于最终实现**）：

- `numpy`
- `scipy`
- `matplotlib`
- `jupyter`（用于运行 notebook）

在没有 `requirements.txt` 的情况下，按需手动安装即可。

## 快速开始（计划中）

当前没有可运行的仿真脚本。计划中的运行方式：

1. 启动 Jupyter：
   ```bash
   jupyter notebook simulation.ipynb
   ```
2. 按 notebook 中的单元格顺序执行。

具体命令取决于 `simulation.ipynb` 中最终实现的代码。

## 预期输出（计划中）

当仿真代码实现后，预期输出可包括：

- 远场强度分布图 (far-field intensity map)
- 归一化强度分布 (normalized intensity)
- 焦斑 / 远场剖面曲线 (far-field profile)
- matplotlib 渲染的图像或保存的 figure 文件

以上均为计划功能，**当前尚未实现**。

## 数值与物理 Sanity Checks

即使当前尚无测试文件，以下检查项可作为后续验证的标准：

- **Array shape 一致性**：相位分布、孔径函数、输出场的数组维度是否匹配。
- **物理单位统一**：波长、孔径直径、采样间隔的单位是否一致（推荐统一使用 meter 或 micrometer）。
- **Wavelength 合理性**：确认仿真波长与 metalens 设计波长一致。
- **Aperture diameter**：孔径是否在物理上合理，采样是否足够。
- **Sampling interval**：是否满足 Nyquist 采样条件。相位变化剧烈时需注意欠采样。
- **FFT shift / ifftshift convention**：是否正确使用了 `numpy.fft.fftshift` / `ifftshift`，避免坐标原点偏移。
- **Intensity normalization**：远场强度是否经过合理归一化，是否可比较不同参数下的结果。
- **Far-field 坐标定义**：横轴究竟是角度 (angle)、空间频率 (spatial frequency) 还是探测器平面坐标 (detector-plane coordinate)，需在代码和文档中明确。
- **低 NA 近似合理性**：随 NA 增大，scalar / paraxial 近似不再可靠，需检查 NA 值是否在合理范围内。
- **Circular aperture 对照**：如果实现了圆孔孔径，可与解析 Airy pattern 做 sanity check。

## Roadmap

- [ ] 在 `simulation.ipynb` 中添加可复现的 far-field 仿真 example。
- [ ] 添加 sampling-condition checks（Nyquist 条件等）。
- [ ] 添加 FFT / Fraunhofer convention 的单元测试。
- [ ] 用 circular aperture 的解析 Airy pattern 做对照验证。
- [ ] 实现 metalens phase-profile generation（例如 hyperboloidal phase profile）。
- [ ] 明确坐标系、归一化约定并写入文档。
- [ ] 未来可考虑扩展：Fresnel propagation、angular spectrum propagation。

## License

No license has been specified yet.
