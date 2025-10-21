# Train Dataset Information

## Basic Statistics

- **Total Records**: 300,000
- **Total Columns**: 35
- **Memory Usage**: 298.86 MB

## Column Details

| Column | Non-Null Count | Null Count | Null % | Data Type |
|--------|---------------|------------|--------|-----------|
| Id | 300,000 | 0 | 0.0% | int64 |
| Type | 300,000 | 0 | 0.0% | object |
| Region | 205,418 | 94,582 | 31.5% | object |
| MunicipalityCode | 300,000 | 0 | 0.0% | int64 |
| Prefecture | 300,000 | 0 | 0.0% | object |
| Municipality | 300,000 | 0 | 0.0% | object |
| DistrictName | 299,853 | 147 | 0.0% | object |
| NearestStation | 290,777 | 9,223 | 3.1% | object |
| TimeToNearestStation | 287,266 | 12,734 | 4.2% | object |
| MinTimeToNearestStation | 287,266 | 12,734 | 4.2% | float64 |
| MaxTimeToNearestStation | 284,129 | 15,871 | 5.3% | float64 |
| TradePrice | 300,000 | 0 | 0.0% | int64 |
| FloorPlan | 83,230 | 216,770 | 72.3% | object |
| Area | 300,000 | 0 | 0.0% | int64 |
| AreaIsGreaterFlag | 300,000 | 0 | 0.0% | int64 |
| LandShape | 204,816 | 95,184 | 31.7% | object |
| Frontage | 186,296 | 113,704 | 37.9% | float64 |
| FrontageIsGreaterFlag | 300,000 | 0 | 0.0% | bool |
| TotalFloorArea | 113,432 | 186,568 | 62.2% | float64 |
| TotalFloorAreaIsGreaterFlag | 300,000 | 0 | 0.0% | int64 |
| BuildingYear | 195,662 | 104,338 | 34.8% | float64 |
| PrewarBuilding | 300,000 | 0 | 0.0% | int64 |
| Structure | 197,187 | 102,813 | 34.3% | object |
| Use | 192,827 | 107,173 | 35.7% | object |
| Purpose | 104,410 | 195,590 | 65.2% | object |
| Direction | 204,790 | 95,210 | 31.7% | object |
| Classification | 201,967 | 98,033 | 32.7% | object |
| Breadth | 200,353 | 99,647 | 33.2% | float64 |
| CityPlanning | 288,292 | 11,708 | 3.9% | object |
| CoverageRatio | 282,231 | 17,769 | 5.9% | float64 |
| FloorAreaRatio | 282,231 | 17,769 | 5.9% | float64 |
| Year | 300,000 | 0 | 0.0% | int64 |
| Quarter | 300,000 | 0 | 0.0% | int64 |
| Renovation | 78,428 | 221,572 | 73.9% | object |
| Remarks | 19,764 | 280,236 | 93.4% | object |

## Target Variable (TradePrice) Statistics

- **Mean**: ¥42,907,274
- **Median**: ¥25,000,000
- **Min**: ¥310
- **Max**: ¥30,000,000,000
- **Std Dev**: ¥203,200,158

## High Missing Value Columns (>30%)

- **Remarks**: 93.4% missing
- **Renovation**: 73.9% missing
- **FloorPlan**: 72.3% missing
- **Purpose**: 65.2% missing
- **TotalFloorArea**: 62.2% missing
- **Frontage**: 37.9% missing
- **Use**: 35.7% missing
- **BuildingYear**: 34.8% missing
- **Structure**: 34.3% missing
- **Breadth**: 33.2% missing
- **Classification**: 32.7% missing
- **Direction**: 31.7% missing
- **LandShape**: 31.7% missing
- **Region**: 31.5% missing
