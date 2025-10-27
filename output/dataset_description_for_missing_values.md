# 不動産価格予測コンペティション - データセット詳細＋欠損値処理ガイド

## コンペティション概要
関東地方の不動産データから、最終的な取引価格（TradePrice）を予測するコンペティション。

## 評価指標
Root-Mean-Squared-Logarithmic-Error (RMSLE)

---

## データセット構造

### ファイル
- **train.csv**: 学習データ（300,000件）
- **test.csv**: テストデータ（75,000件）
- **sample_submission.csv**: 提出フォーマット

---

## ターゲット変数の特性

### TradePrice（取引価格）
- **平均**: ¥42,907,274
- **中央値**: ¥25,000,000
- **最小値**: ¥310
- **最大値**: ¥30,000,000,000
- **標準偏差**: ¥203,200,158
- **歪度（Skewness）**: 46.54（極めて右に歪んでいる）
- **尖度（Kurtosis）**: 3326.41（極めて外れ値が多い）
- **対数変換後の歪度**: 0.36（ほぼ正規分布）

**⚠️ 重要**: 極端な右歪みがあるため、RMSLE評価や予測モデルには対数変換が推奨される。

---

## カラム定義と欠損値情報

### 1. 物件の基本情報（欠損少ない）

| カラム名 | 説明 | データ型 | 欠損率 | 処理推奨 |
|---------|------|---------|--------|----------|
| **Id** | 物件ID | int64 | 0% | - |
| **Type** | 物件種別 | object | 0% | - |
| **Prefecture** | 都道府県名 | object | 0% | - |
| **Municipality** | 市区町村名 | object | 0% | - |
| **MunicipalityCode** | 市区町村コード | int64 | 0% | - |
| **DistrictName** | 地区名 | object | 0.05% | 削除 or "Unknown" |
| **Area** | 敷地面積（m²） | int64 | 0% | - |
| **AreaIsGreaterFlag** | 面積が2000m²以上 | int64 | 0% | - |
| **Year** | 取引年 | int64 | 0% | - |
| **Quarter** | 取引四半期 | int64 | 0% | - |

**Type（物件種別）の内訳**:
- 住宅地: 196,714件（65.6%）
- 宅地（現況：建物等）: 76,346件（25.4%）
- 中古マンション等: 13,996件（4.7%）
- 林地: 7,651件（2.6%）
- その他（農地、宅地（現況：農地、山林、雑種地等））: 5,293件（1.8%）

### 2. 駅・アクセス情報

| カラム名 | 説明 | データ型 | 欠損率（Train） | 欠損率（Test） | 欠損パターン | 処理推奨 |
|---------|------|---------|----------------|---------------|-------------|----------|
| **NearestStation** | 最寄駅名 | object | 3.1% | 3.1% | ランダム | "No Station" or 最頻値 |
| **TimeToNearestStation** | 最寄駅までの時間（文字列） | object | 4.2% | 4.3% | ランダム | MinTime/MaxTimeから再構築 |
| **MinTimeToNearestStation** | 最寄駅までの最短時間（分） | float64 | 4.2% | 4.3% | ランダム | 中央値 or エリア別中央値 |
| **MaxTimeToNearestStation** | 最寄駅までの最長時間（分） | float64 | 5.3% | 5.3% | ランダム | MinTimeから推定 or 削除 |

**重要な特徴**:
- 駅情報の欠損は「駅が遠い/不明」を意味する可能性が高い
- 欠損時のTradePrice平均は約4300万円で、データ全体の平均とほぼ同じ
- Train/Test間の欠損率の差は小さい（0.01%程度）

**MinTimeToNearestStation統計**:
- 平均: 11.4分
- 中央値: 9.0分
- 最小値: 0分（駅直結）
- 最大値: 180分（3時間）

### 3. 地理・エリア情報

| カラム名 | 説明 | データ型 | 欠損率 | 欠損パターン | 処理推奨 |
|---------|------|---------|--------|-------------|----------|
| **Region** | 周辺地域の特徴 | object | 31.5% | 物件種別依存（MNAR） | Type別に"Unknown" or 削除 |

**Region（地域特性）の内訳（欠損なしの場合）**:
- 住宅地: 85.3%
- 商業地: 10.3%
- 準工業地: 1.8%
- 工業地: 1.3%
- その他: 1.3%

**欠損パターン分析**:
- **Type別欠損率**:
  - 林地: 99.9%欠損（構造的な欠損 - MNAR）
  - 農地: 95.7%欠損（構造的な欠損 - MNAR）
  - 住宅地: 17.4%欠損
  - 宅地（建物等）: 57.4%欠損
- 欠損時のTradePrice平均: ¥32,991,426（-23%）
- 非欠損時のTradePrice平均: ¥47,753,803

### 4. 建物情報（欠損中～高）

| カラム名 | 説明 | データ型 | 欠損率 | 欠損パターン | 処理推奨 |
|---------|------|---------|--------|-------------|----------|
| **FloorPlan** | 間取り | object | 72.3% | 建物なし（MNAR） | Type別処理 or "No Building" |
| **TotalFloorArea** | 延床面積（m²） | float64 | 62.2% | 建物なし（MNAR） | 0埋め or Type別中央値 |
| **TotalFloorAreaIsGreaterFlag** | 延床面積が2000m²以上 | int64 | 0% | - |
| **BuildingYear** | 建築年 | float64 | 34.8% | 建物なし（MNAR） | 削除 or 築年数に変換 |
| **PrewarBuilding** | 戦前建築フラグ | int64 | 0% | - |
| **Structure** | 建物構造 | object | 34.3% | 建物なし（MNAR） | "No Building" or 削除 |
| **Use** | 現在の用途 | object | 35.7% | 建物なし（MNAR） | "Vacant Land" or Type別 |
| **Purpose** | 将来の用途 | object | 65.2% | 不明（MAR） | "Unknown" or 削除 |
| **Renovation** | リノベーション有無 | object | 73.9% | 不明（MAR） | "Unknown" or "No" |

**FloorPlan（間取り）の主な値**（欠損なしの場合）:
- 3LDK: 14.1%
- 4LDK: 13.5%
- 3DK: 9.4%
- 2LDK: 8.8%
- その他: 54.2%
- ユニーク値数: 86

**Structure（建物構造）の内訳**（欠損なしの場合）:
- W（木造）: 54.7%
- RC（鉄筋コンクリート）: 23.1%
- S（鉄骨造）: 11.2%
- SRC（鉄骨鉄筋コンクリート）: 7.3%
- LS（軽量鉄骨）: 2.6%
- B（ブロック造）: 1.1%

**⚠️ 重要な発見**:
- **FloorPlan欠損時**: 平均価格¥49,171,882（+15%高い）→ 土地のみ物件が高額の可能性
- **TotalFloorArea欠損時**: 平均価格¥34,538,353（-19%低い）→ 建物なし物件
- **Renovation欠損時**: 平均価格¥48,672,082（+13%高い）→ 新築または土地のみ
- これらの欠損は「建物の存在しない土地」を示すMNAR（Not At Random）の可能性が高い

### 5. 土地情報（欠損中）

| カラム名 | 説明 | データ型 | 欠損率 | 欠損パターン | 処理推奨 |
|---------|------|---------|--------|-------------|----------|
| **LandShape** | 土地の形状 | object | 31.7% | 種別依存（MAR） | "Unknown" or 最頻値 |
| **Frontage** | 間口（m） | float64 | 37.9% | 測定不可（MAR） | 中央値 or Area/Type別推定 |
| **FrontageIsGreaterFlag** | 間口が50m以上 | bool | 0% | - |
| **Direction** | 前面道路の方角 | object | 31.7% | 測定不可（MAR） | "Unknown" or 削除 |

**LandShape（土地形状）の内訳**（欠損なしの場合）:
- ほぼ整形: 42.8%
- 台形: 20.9%
- 不整形: 20.1%
- 長方形: 11.8%
- その他: 4.4%

**⚠️ 重要な発見**:
- **Frontage欠損時**: 平均価格¥27,661,611（-35%低い）→ 不整形地や測定困難な土地
- **Frontage非欠損時**: 平均価格¥52,212,320（+88%高い）→ 整形地・優良物件

### 6. 道路情報（欠損中）

| カラム名 | 説明 | データ型 | 欠損率 | 欠損パターン | 処理推奨 |
|---------|------|---------|--------|-------------|----------|
| **Classification** | 前面道路の種類 | object | 32.7% | 道路なし（MNAR） | "No Road" or Type別 |
| **Breadth** | 前面道路の幅（m） | float64 | 33.2% | 道路なし（MNAR） | 0埋め or 中央値 |

**Classification（道路種類）の内訳**（欠損なしの場合）:
- 市区町村道: 67.8%
- 私道: 18.2%
- 都道府県道: 9.5%
- 国道: 3.2%
- その他: 1.3%

**⚠️ 重要な発見**:
- **Breadth欠損時**: 平均価格¥25,080,770（-42%低い）
- **Breadth非欠損時**: 平均価格¥51,773,413（+106%高い）
- 道路情報の欠損は価格に大きな負の影響（接道義務違反の可能性）

### 7. 都市計画・法規制情報（欠損少）

| カラム名 | 説明 | データ型 | 欠損率 | 欠損パターン | 処理推奨 |
|---------|------|---------|--------|-------------|----------|
| **CityPlanning** | 都市計画法による用途地域 | object | 3.9% | 市街化調整区域等（MAR） | "Outside City Planning" |
| **CoverageRatio** | 建ぺい率（%） | float64 | 5.9% | 規制なし（MAR） | 中央値 or Area別推定 |
| **FloorAreaRatio** | 容積率（%） | float64 | 5.9% | 規制なし（MAR） | 中央値 or Area別推定 |

**CityPlanning（用途地域）の主な値**（欠損なしの場合）:
- 第一種低層住居専用地域: 26.8%
- 第一種住居地域: 17.6%
- 第二種住居地域: 11.2%
- 市街化調整区域: 10.8%
- 準住居地域: 8.9%
- その他: 24.7%
- ユニーク値数: 20

**CoverageRatio統計**（欠損なしの場合）:
- 平均: 59.8%
- 中央値: 60.0%
- 最小値: 30%
- 最大値: 90%

**FloorAreaRatio統計**（欠損なしの場合）:
- 平均: 186.3%
- 中央値: 200.0%
- 最小値: 50%
- 最大値: 1300%

### 8. 備考情報（欠損非常に高）

| カラム名 | 説明 | データ型 | 欠損率 | 欠損パターン | 処理推奨 |
|---------|------|---------|--------|-------------|----------|
| **Remarks** | 備考 | object | 93.4% | 特記事項なし（MAR） | 削除 or フラグ化 |

**⚠️ 重要な発見**:
- **Remarks欠損時**: 平均価格¥43,194,777（ほぼ平均通り）
- **Remarks非欠損時**: 平均価格¥38,830,733（-10%低い）
- 備考がある物件は何らかの問題がある可能性

---

## 欠損値パターンの総合分析

### 1. MCAR (完全にランダムな欠損)
該当なし - すべての欠損には何らかのパターンがある

### 2. MAR (ランダムな欠損)
他の観測変数に依存するが、欠損自体は条件付きでランダム:
- **駅情報系**: NearestStation, TimeToNearestStation（地方エリアで欠損多い）
- **LandShape, Direction**: 測定困難な物件
- **CityPlanning, CoverageRatio, FloorAreaRatio**: 都市計画区域外
- **Remarks**: 特記事項がない通常物件

### 3. MNAR (非ランダムな欠損)
欠損自体が情報を持つ構造的欠損:
- **FloorPlan, TotalFloorArea, Structure**: 建物が存在しない（土地のみ物件）
- **BuildingYear**: 建物が存在しない or 築年不詳
- **Frontage, Breadth**: 道路に接していない or 測定不可能
- **Region**: 林地・農地など住宅地以外
- **Renovation**: 新築または土地のみ

**⚠️ 重要**: MNAR変数は単純な補完（平均値・中央値埋め）は不適切。欠損自体を示すフラグ変数の作成が推奨される。

---

## Train/Test データの一貫性

### 欠損率の差異（Train vs Test）
すべてのカラムで欠損率の差は±0.15%以内で、データの分布は一貫している。

**主な差異**:
| カラム | Train欠損率 | Test欠損率 | 差 |
|--------|------------|-----------|-----|
| TotalFloorArea | 62.19% | 62.33% | +0.14% |
| Structure | 34.27% | 34.41% | +0.14% |
| Frontage | 37.90% | 38.01% | +0.11% |

→ Train/Testで異なる補完戦略は不要

---

## カテゴリカル変数の特性

### 高カーディナリティ変数（ユニーク値が多い）
| カラム | ユニーク値数 | 処理推奨 |
|--------|------------|----------|
| NearestStation | 1,743 | Target Encoding推奨 |
| DistrictName | 12,436 | Target Encoding推奨 |
| Municipality | 348 | Label Encoding可 |
| FloorPlan | 86 | One-Hot or Target Encoding |
| CityPlanning | 20 | One-Hot Encoding可 |

### 低カーディナリティ変数（ユニーク値が少ない）
| カラム | ユニーク値数 | 処理推奨 |
|--------|------------|----------|
| Type | 10 | One-Hot Encoding |
| Prefecture | 7 | One-Hot Encoding |
| Structure | 6 | One-Hot Encoding |
| LandShape | 5 | One-Hot Encoding |
| Direction | 8 | One-Hot Encoding |

---

## 欠損値処理の推奨戦略

### 戦略1: 削除
- **Remarks**: 93.4%欠損、情報量少ない → 削除推奨
- **MaxTimeToNearestStation**: MinTimeから推定可能 → 削除可
- **Purpose**: 65.2%欠損、予測への寄与不明 → 削除検討

### 戦略2: フラグ化（MNAR変数に推奨）
建物関連の欠損は「建物なし」を意味する可能性が高い:
```python
# 欠損フラグの作成
df['HasBuilding'] = df['TotalFloorArea'].notnull().astype(int)
df['HasFloorPlan'] = df['FloorPlan'].notnull().astype(int)
df['HasStructure'] = df['Structure'].notnull().astype(int)
df['HasRoadAccess'] = df['Breadth'].notnull().astype(int)
```

### 戦略3: カテゴリカル変数の補完
```python
# 欠損を明示的なカテゴリとして扱う
categorical_cols = ['Region', 'LandShape', 'Structure', 'Use', 'Purpose', 
                   'Direction', 'Classification', 'CityPlanning', 'Renovation']
for col in categorical_cols:
    df[col] = df[col].fillna('Unknown')
```

### 戦略4: 数値変数の補完
```python
# Type別の中央値で補完（MAR変数）
for col in ['MinTimeToNearestStation', 'Frontage', 'Breadth', 
            'CoverageRatio', 'FloorAreaRatio']:
    df[col] = df.groupby('Type')[col].transform(
        lambda x: x.fillna(x.median())
    )

# 建物関連は0埋め（MNAR変数）
df['TotalFloorArea'] = df['TotalFloorArea'].fillna(0)
df['BuildingYear'] = df['BuildingYear'].fillna(0)
```

### 戦略5: Target Encoding（高カーディナリティ変数）
```python
# NearestStation, DistrictNameなど
# Cross-validationベースのTarget Encodingを推奨
from category_encoders import TargetEncoder
encoder = TargetEncoder(cols=['NearestStation', 'DistrictName'])
```

---

## 特徴量エンジニアリングの推奨

### 1. 欠損パターンからの特徴量
```python
# 建物情報の欠損数（土地のみ物件の指標）
building_cols = ['FloorPlan', 'TotalFloorArea', 'BuildingYear', 'Structure']
df['MissingBuildingInfo'] = df[building_cols].isnull().sum(axis=1)

# 道路情報の欠損数（接道問題の指標）
road_cols = ['Frontage', 'Breadth', 'Classification', 'Direction']
df['MissingRoadInfo'] = df[road_cols].isnull().sum(axis=1)
```

### 2. 築年数の計算
```python
# BuildingYearから築年数を計算
df['BuildingAge'] = df['Year'] - df['BuildingYear']
df.loc[df['BuildingYear'] == 0, 'BuildingAge'] = -1  # 建物なしは-1
```

### 3. 面積関連の特徴量
```python
# 延床面積率（建物の階数の指標）
df['FloorAreaRatioActual'] = df['TotalFloorArea'] / df['Area']
df['FloorAreaRatioActual'] = df['FloorAreaRatioActual'].fillna(0)
```

---

## 検証推奨事項

### 1. 欠損値の可視化
- 欠損値のヒートマップ（missingno library）
- Type別の欠損パターン分析
- 価格帯別の欠損パターン分析

### 2. 補完方法の検証
- 異なる補完方法でのCV scoreの比較
- 欠損フラグの有効性検証
- Type別補完 vs 全体補完の比較

### 3. モデルへの影響
- 欠損値ありモデル vs 完全データモデル
- LightGBM/XGBoost（欠損値に強い）vs 他のモデル
- 欠損フラグの特徴量重要度

---

## 注意事項

1. **RMSLE評価のため対数変換が必須**: `np.log1p(TradePrice)`を使用
2. **外れ値の扱い**: 最大値が平均の698倍、中央値の1,200倍 → Robust Scalingを検討
3. **Train/Testの一貫性**: 同じ補完戦略を両方に適用すること
4. **リーケージ防止**: Target Encodingや統計量での補完はCV内で実施
5. **Type変数の重要性**: Type別の補完戦略が最も効果的な可能性が高い

---

## まとめ: ChatGPTに渡す際のポイント

このドキュメントには以下の情報が含まれています:

✅ **基本情報**: データ構造、評価指標、ターゲット変数の分布
✅ **欠損値の詳細**: 各カラムの欠損率、Train/Testでの一貫性
✅ **欠損パターン**: MCAR/MAR/MNARの分類と根拠
✅ **欠損と価格の関係**: 欠損の有無による価格差の定量分析
✅ **カテゴリカル変数の特性**: ユニーク値数、頻度分布
✅ **数値変数の統計量**: 平均、中央値、範囲
✅ **具体的な処理戦略**: コード例付きの推奨手法
✅ **検証方法**: 補完方法の評価手法

このドキュメントをChatGPTに渡すことで、データの特性を踏まえた適切な欠損値処理戦略を提案してもらえます。
