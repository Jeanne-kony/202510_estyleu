## Dataset Description

### Files
- **train.csv**: The training dataset  
- **test.csv**: The test dataset  
- **sample_submission.csv**: A sample submission file in the correct format  

### Columns
| Column Name | Description |
|--------------|-------------|
| **Id** | Real estate ID |
| **Type** | Real estate type |
| **Region** | Characteristics of surrounding area |
| **MunicipalityCode** | City code |
| **Prefecture** | Prefecture name |
| **Municipality** | City name |
| **DistrictName** | District name |
| **NearestStation** | Nearest station name |
| **TimeToNearestStation** | Time to the nearest station (including character values) |
| **MinTimeToNearestStation** | Minimum time to the nearest station (minutes), generated from `TimeToNearestStation` |
| **MaxTimeToNearestStation** | Maximum time to the nearest station (minutes), generated from `TimeToNearestStation` |
| **TradePrice** | Trade prices (Yen); **Target** |
| **FloorPlan** | Floor plan |
| **Area** | The surveyed area (m²) |
| **AreaIsGreaterFlag** | True if area ≥ 2000 m² (values capped at 2000) |
| **LandShape** | Land shape |
| **Frontage** | Frontage (m) |
| **FrontageIsGreaterFlag** | True if original Frontage was “50.0m or longer”; numeric conversion applied |
| **TotalFloorArea** | Total floor area (m²) |
| **TotalFloorAreaIsGreaterFlag** | True if original TotalFloorArea was “2,000 m² or greater”; numeric conversion applied |
| **BuildingYear** | Construction year of building |
| **PrewarBuilding** | True if building was constructed before 1945 (converted “before the war” entries to 1945) |
| **Structure** | Building structure: `SRC` = Steel frame reinforced concrete, `RC` = Reinforced concrete, `S` = Steel frame, `LS` = Light steel structure, `B` = Concrete block, `W` = Wooden |
| **Use** | Current usage (e.g., house, office, shop, factory, warehouse, workshop, parking lot, etc.) |
| **Purpose** | Purpose of future use (e.g., house, shop, office, factory, warehouse, etc.) |
| **Direction** | Frontage road direction |
| **Classification** | Frontage road classification |
| **Breadth** | Frontage road breadth (m) |
| **CityPlanning** | Use districts designated by the City Planning Act |
| **CoverageRatio** | Maximum building coverage ratio (%) |
| **FloorAreaRatio** | Maximum floor-area ratio (%) |
| **Year** | Transaction year (generated from Period) |
| **Quarter** | Transaction year-quarter (generated from Period) |
| **Renovation** | Renovation? |
| **Remarks** | Note |
