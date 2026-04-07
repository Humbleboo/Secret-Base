'''mermaid
graph TD
    %% 定义样式
    classDef layerFill fill:#f9f9f9,stroke:#333,stroke-width:2px,stroke-dasharray: 5 5;
    classDef dataBox fill:#e1f5fe,stroke:#0288d1,stroke-width:1px;
    classDef processBox fill:#fff3e0,stroke:#f57c00,stroke-width:1px;
    classDef modelBox fill:#e8f5e9,stroke:#388e3c,stroke-width:1px;
    classDef resultBox fill:#f3e5f5,stroke:#7b1fa2,stroke-width:1px;

    subgraph 数据获取与预处理层
        A1[(全球土地覆盖数据)]:::dataBox
        A2[(野火燃烧面积产品)]:::dataBox
        A3[(NDVI长时序植被指数)]:::dataBox
        A4[(气象要素与干旱指数)]:::dataBox
        B1[多源数据时空匹配与质量控制]:::processBox
        A1 --> B1
        A2 --> B1
        A3 --> B1
        A4 --> B1
    end

    subgraph 空间基准物理重构层
        B1 --> C1{高纬度网格是否存在面积畸变?}:::processBox
        C1 -->|是| C2[引入WGS84椭球体参数]:::modelBox
        C2 --> C3["构建积分反演模型 (S = R²∫∫cosφ dφ dλ)"]:::modelBox
        C3 --> C4[度级别网格严密换算为km²]:::processBox
        C4 --> C5((高精度无畸变空间数据底座)):::dataBox
    end

    subgraph 长时序演进特征与突变识别层
        C5 --> D1[Theil-Sen Median 趋势计算]:::modelBox
        C5 --> D2[Mann-Kendall 显著性检验]:::modelBox
        D1 --> D3[宏观植被净增减趋势提取]:::processBox
        D2 --> D3
        C5 --> D4[BFAST 算法时间序列分解]:::modelBox
        D4 --> D5[精准锚定野火扰动突变节点]:::processBox
    end

    subgraph 反事实框架因果解耦层
        D3 --> E1[提取火前5-10年基线环境数据]:::processBox
        D5 --> E1
        E1 --> E2[构建 ARIMA/LSTM 时间序列预测模型]:::modelBox
        E2 --> E3[模拟反事实状态: 假设未发生野火的理想变绿轨迹]:::processBox
        E3 --> E4[与真实遥感观测值做差积分]:::modelBox
        E4 --> E5((野火变绿抑制效应定量剥离)):::resultBox
    end

    subgraph 空间机制探测与归因层
        E5 --> F1[引入地理探测器 Geodetector]:::modelBox
        F1 --> F2[因子探测: 评估单要素解释力 q 值]:::processBox
        F1 --> F3[交互探测: 识别多因子非线性叠加效应]:::processBox
        F2 --> F4[干旱/纬度等环境因子空间驱动归因分析]:::processBox
        F3 --> F4
    end

    subgraph 预期成果层
        F4 --> G1[/高精度网格校正数据集/]:::resultBox
        F4 --> G2[/野火抑制效应时空演变规律报告/]:::resultBox
        F4 --> G3[/高水平SCI/核心期刊论文/]:::resultBox
    end
'''
