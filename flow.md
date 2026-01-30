"# 🏗️ Architecture Documentation

## System Overview

The India Air Quality × Health Dashboard is a full-stack web application built to monitor and visualize air pollution data across 18 major Indian cities and its correlation with respiratory health impacts.

## Architecture Diagram

```
┌─────────────────┐
│   User Browser  │
└────────┬────────┘
         │
         │ HTTPS
         ▼
┌─────────────────────────────────────┐
│      React Frontend (Port 3000)      │
│  ┌─────────────────────────────────┐ │
│  │  Components:                     │ │
│  │  - Dashboard (Main)              │ │
│  │  - IndiaMap (Interactive)        │ │
│  │  - Charts (Recharts)             │ │
│  │  - CitySelector                  │ │
│  │  - StatsCards                    │ │
│  └─────────────────────────────────┘ │
└───────────┬─────────────────────────┘
            │
            │ REST API
            ▼
┌─────────────────────────────────────┐
│    FastAPI Backend (Port 8001)      │
│  ┌─────────────────────────────────┐ │
│  │  Endpoints:                      │ │
│  │  /api/air-quality/cities         │ │
│  │  /api/air-quality/city/:id       │ │
│  │  /api/air-quality/time-series    │ │
│  │  /api/health/correlation         │ │
│  │  /api/health/insights            │ │
│  └─────────────────────────────────┘ │
└───────────┬─────────────────────────┘
            │
            │ Motor (Async)
            ▼
┌─────────────────────────────────────┐
│     MongoDB (Port 27017)            │
│  Collections:                       │
│  - aqi_data (future caching)        │
│  - health_data                      │
└─────────────────────────────────────┘
```

## Technology Stack

### Frontend
- **React 19.0**: Core UI framework
- **Recharts 3.6**: Data visualization library
- **React Simple Maps 3.0**: Interactive India map
- **Lucide React**: Icon library
- **Tailwind CSS 3.4**: Utility-first styling
- **Axios**: HTTP client for API requests
- **D3-Scale**: Color scaling for AQI heatmap

### Backend
- **FastAPI 0.110**: Modern Python web framework
- **Motor 3.3**: Async MongoDB driver
- **Pydantic**: Data validation and serialization
- **HTTPX**: Async HTTP client (prepared for external APIs)
- **Uvicorn**: ASGI server

### Database
- **MongoDB**: NoSQL document database for flexible data storage

## Data Flow

### 1. City AQI Data Flow
```
User Loads Dashboard
    ↓
Frontend requests /api/air-quality/cities
    ↓
Backend generates/fetches AQI data for 18 cities
    ↓
Returns JSON with: id, name, lat, lon, aqi, category, pollutants
    ↓
Frontend renders:
  - Stats cards (avg AQI, unhealthy cities)
  - India map with color-coded markers
  - City selector dropdown
```

### 2. City Selection Flow
```
User clicks city marker OR searches city
    ↓
Frontend sets selectedCity state
    ↓
Triggers /api/air-quality/time-series/:cityId
    ↓
Backend generates 24-hour historical data
    ↓
Frontend updates:
  - City detail panel (pollutant breakdown)
  - 24-hour trend chart
```

### 3. Health Correlation Flow
```
Dashboard initialization
    ↓
Frontend requests /api/health/correlation
    ↓
Backend calculates respiratory cases based on AQI
  Formula: cases = population × rate(aqi) × random(0.8-1.2)
    ↓
Returns city-wise AQI vs respiratory cases
    ↓
Frontend renders scatter chart
```

## Data Models

### CityAQI
```python
{
    \"id\": \"delhi\",
    \"name\": \"Delhi\",
    \"lat\": 28.6139,
    \"lon\": 77.2090,
    \"aqi\": 167,
    \"category\": \"Unhealthy\",
    \"pm25\": 33.4,
    \"pm10\": 66.8,
    \"no2\": 10.0,
    \"so2\": 3.3,
    \"co\": 13.36,
    \"o3\": 6.7,
    \"updated_at\": \"2025-01-30T12:00:00Z\"
}
```

### TimeSeriesData
```python
{
    \"timestamp\": \"2025-01-30T11:00:00Z\",
    \"aqi\": 145,
    \"pm25\": 29.0,
    \"respiratory_cases\": 8500
}
```

### HealthCorrelation
```python
{
    \"city\": \"Delhi\",
    \"aqi\": 167,
    \"respiratory_cases\": 120000
}
```

### HealthInsight
```python
{
    \"title\": \"High PM2.5 Levels in Delhi NCR\",
    \"description\": \"Particulate matter levels exceed safe limits...\",
    \"severity\": \"high\",
    \"affected_population\": 12000000
}
```

## AQI Calculation Logic

### Category Mapping
```
0-50    → Good (Green)
51-100  → Moderate (Yellow)
101-200 → Unhealthy (Orange)
201-300 → Very Unhealthy (Red)
301+    → Hazardous (Dark Red)
```

### Pollutant Simulation
```python
base = aqi / 5
pm25 = base × random(0.8, 1.2)
pm10 = base × random(1.5, 2.0)
no2 = base × random(0.3, 0.6)
so2 = base × random(0.1, 0.3)
co = base × random(0.4, 0.8)
o3 = base × random(0.2, 0.5)
```

### Respiratory Cases Correlation
```python
if aqi <= 50:    rate = 0.01
if aqi <= 100:   rate = 0.03
if aqi <= 200:   rate = 0.08
if aqi <= 300:   rate = 0.15
if aqi > 300:    rate = 0.25

cases = population × rate × random(0.8, 1.2)
```

## API Endpoints

### GET /api/air-quality/cities
**Returns**: List of all 18 cities with current AQI data
```json
[
  {
    \"id\": \"delhi\",
    \"name\": \"Delhi\",
    \"aqi\": 167,
    \"category\": \"Unhealthy\",
    \"lat\": 28.6139,
    \"lon\": 77.2090,
    \"pm25\": 33.4,
    ...
  }
]
```

### GET /api/air-quality/city/:id
**Returns**: Detailed data for a specific city
**Parameters**: city_id (path)

### GET /api/air-quality/time-series/:cityId
**Returns**: 24-hour historical data for a city
**Parameters**: city_id (path)
```json
[
  {
    \"timestamp\": \"2025-01-30T11:00:00Z\",
    \"aqi\": 145,
    \"pm25\": 29.0,
    \"respiratory_cases\": 8500
  }
]
```

### GET /api/health/correlation
**Returns**: AQI vs respiratory cases for all cities
```json
[
  {
    \"city\": \"Delhi\",
    \"aqi\": 167,
    \"respiratory_cases\": 120000
  }
]
```

### GET /api/health/insights
**Returns**: Health impact alerts and insights
```json
[
  {
    \"title\": \"High PM2.5 Levels in Delhi NCR\",
    \"description\": \"...\",
    \"severity\": \"high\",
    \"affected_population\": 12000000
  }
]
```

## Frontend Component Hierarchy

```
App (Main Container)
├── Header
│   └── Title + Subtitle
├── StatsCards Row
│   ├── National Avg AQI
│   ├── Unhealthy Cities
│   ├── Respiratory Cases
│   └── Affected Population
├── Map + Details Section
│   ├── IndiaMap (Interactive)
│   │   └── City Markers (clickable)
│   └── Sidebar
│       ├── CitySelector (search dropdown)
│       └── CityDetailPanel
│           ├── AQI Display
│           └── Pollutant Breakdown
├── Charts Section
│   ├── CorrelationChart (scatter)
│   └── TimeSeriesChart (line + bar)
└── HealthInsightsPanel
    └── Insight Cards (3)
```

## State Management

### React Hooks Used
```javascript
const [cities, setCities] = useState([])              // All cities data
const [selectedCity, setSelectedCity] = useState(null) // Current selected city
const [timeSeries, setTimeSeries] = useState([])       // 24h historical data
const [correlation, setCorrelation] = useState([])     // Health correlation
const [insights, setInsights] = useState([])           // Health insights
const [loading, setLoading] = useState(true)           // Loading state
```

### Data Fetching Strategy
1. **Initial Load**: Fetch cities, correlation, insights in parallel
2. **City Selection**: Fetch time-series data on-demand
3. **No Polling**: Static data for MVP (can add WebSocket later)

## Design System

### Color Variables (CSS Custom Properties)
```css
--background: #020617 (Deep Indigo)
--primary: #FF9933 (India Saffron)
--secondary: #138808 (India Green)
--accent: #3B82F6 (Blue)
--border: rgba(255, 255, 255, 0.1)
```

### Glassmorphism Effect
```css
.glass-card {
  background: rgba(15, 23, 42, 0.6);
  backdrop-filter: blur(24px);
  border: 1px solid rgba(255, 255, 255, 0.1);
}
```

### Typography
- **Headings**: Outfit (Google Fonts)
- **Body**: Manrope (Google Fonts)
- **Code**: JetBrains Mono

### Responsive Breakpoints
```css
md: 768px  /* Tablet and up */
lg: 1024px /* Desktop */
```

## Performance Considerations

### Frontend Optimizations
- **Lazy Loading**: Maps load only visible markers
- **Debouncing**: City search input debounced
- **Memoization**: Charts use React.memo for expensive renders
- **Code Splitting**: Can split map and charts into separate chunks

### Backend Optimizations
- **Async Operations**: All MongoDB queries are async
- **Response Models**: Pydantic serialization for fast JSON
- **CORS**: Configured for production domains
- **Connection Pooling**: Motor handles MongoDB connection pool

## Security

### CORS Configuration
```python
allow_origins=os.environ.get('CORS_ORIGINS', '*').split(',')
```
**Production**: Set specific domains in CORS_ORIGINS env variable

### Environment Variables
- Never commit `.env` files
- Use `.env.example` templates
- Store secrets in secure vault (production)

### Data Validation
- Pydantic models validate all API inputs/outputs
- Frontend validates user inputs (city search)

## Scalability

### Horizontal Scaling
- **Frontend**: Deploy to CDN (Vercel, Netlify)
- **Backend**: Run multiple FastAPI instances behind load balancer
- **Database**: MongoDB sharding for large datasets

### Caching Strategy (Future)
```python
# Cache city AQI data for 5 minutes
@lru_cache(maxsize=100)
def get_cached_city_aqi(city_id: str, timestamp: int):
    return fetch_city_aqi(city_id)
```

### Real API Integration (Future)
```python
# AQICN API integration
async def fetch_real_aqi(city: str):
    async with httpx.AsyncClient() as client:
        response = await client.get(
            f\"https://api.waqi.info/feed/{city}/?token={WAQI_TOKEN}\"
        )
        return response.json()
```

## Testing Strategy

### Frontend Tests (Future)
- **Unit Tests**: Jest for components
- **Integration Tests**: React Testing Library
- **E2E Tests**: Playwright for user flows

### Backend Tests
- **API Tests**: pytest with async support
- **Load Tests**: Locust for performance testing

### Example Test
```python
@pytest.mark.asyncio
async def test_get_cities():
    response = await client.get(\"/api/air-quality/cities\")
    assert response.status_code == 200
    assert len(response.json()) == 18
```

## Deployment

### Frontend (Vercel)
```bash
cd frontend
vercel --prod
```

### Backend (Railway)
```bash
# Configure in railway.app
# Set environment variables
# Deploy from GitHub
```

### Environment Variables
**Backend (.env)**
```
MONGO_URL=mongodb://localhost:27017
DB_NAME=air_quality_db
CORS_ORIGINS=https://yourdomain.com
```

**Frontend (.env)**
```
REACT_APP_BACKEND_URL=https://api.yourdomain.com
```

## Monitoring (Future)

### Metrics to Track
- API response times
- Error rates (4xx, 5xx)
- Database query performance
- Frontend load times
- User interactions (city clicks, searches)

### Logging
```python
logger.info(f\"City {city_id} AQI fetched: {aqi}\")
logger.error(f\"Failed to fetch data: {error}\")
```

## Future Enhancements

1. **Real-time Updates**: WebSocket for live AQI data
2. **Historical Trends**: 7-day, 30-day, yearly trends
3. **Predictions**: ML model for AQI forecasting
4. **Alerts**: Email/SMS notifications for high AQI
5. **User Accounts**: Save favorite cities, custom alerts
6. **Mobile App**: React Native version
7. **API Integration**: Connect to real AQICN/WAQI APIs
8. **Export Data**: CSV, PDF reports for urban planners

## Contributing Guidelines

1. Fork the repository
2. Create feature branch (`git checkout -b feature/amazing-feature`)
3. Commit changes (`git commit -m 'Add amazing feature'`)
4. Push to branch (`git push origin feature/amazing-feature`)
5. Open Pull Request

## License

MIT License - See LICENSE file for details
"