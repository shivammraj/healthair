"# 🌍 India Air Quality × Health Dashboard

**Real-time monitoring of air pollution and its impact on respiratory health across 18 major Indian cities**

![Dashboard Preview](https://img.shields.io/badge/Status-Live-success)
![React](https://img.shields.io/badge/React-19.0-blue)
![FastAPI](https://img.shields.io/badge/FastAPI-0.110-green)
![MongoDB](https://img.shields.io/badge/MongoDB-Ready-brightgreen)

## 🎯 Features

### 📊 Real-Time Monitoring
- **18 Major Indian Cities**: Delhi, Mumbai, Bangalore, Chennai, Kolkata, Hyderabad, Pune, Ahmedabad, Jaipur, Lucknow, Kanpur, Nagpur, Indore, Bhopal, Patna, Chandigarh, Coimbatore, Visakhapatnam
- **Live AQI Data**: Air Quality Index with color-coded severity levels
- **Pollutant Breakdown**: PM2.5, PM10, NO₂, SO₂, CO, O₃ measurements

### 🗺️ Interactive Heatmap
- India map with clickable city markers
- Color-coded AQI visualization (Green → Yellow → Orange → Red)
- Real-time city selection and details

### 📈 Data Visualization
- **Correlation Chart**: AQI vs Respiratory Disease Cases scatter plot
- **24-Hour Trends**: Time-series analysis of AQI and health impacts
- **Health Insights**: Critical pollution alerts and affected population stats

### 💎 Modern UI/UX
- **Glassmorphism Design**: Backdrop blur effects with India-themed colors
- **Responsive Layout**: Works on desktop and tablets
- **Dark Theme**: Professional dashboard aesthetic
- **Outfit & Manrope Fonts**: Modern, readable typography

## 🚀 Quick Start

### Prerequisites
- Node.js 16+ and Yarn
- Python 3.11+
- MongoDB (local or cloud)

### Installation

1. **Clone the repository**
```bash
git clone <your-repo-url>
cd air-quality-dashboard
```

2. **Backend Setup**
```bash
cd backend
pip install -r requirements.txt

# Configure environment variables
cp .env.example .env
# Edit .env with your MongoDB connection string

# Start backend
uvicorn server:app --host 0.0.0.0 --port 8001 --reload
```

3. **Frontend Setup**
```bash
cd frontend
yarn install

# Configure environment variables
cp .env.example .env
# Set REACT_APP_BACKEND_URL to your backend URL

# Start frontend
yarn start
```

4. **Access the dashboard**
Open http://localhost:3000 in your browser

## 📁 Project Structure

```
air-quality-dashboard/
├── backend/
│   ├── server.py          # FastAPI application with air quality APIs
│   ├── requirements.txt   # Python dependencies
│   └── .env              # Backend environment variables
├── frontend/
│   ├── src/
│   │   ├── App.js        # Main dashboard component
│   │   ├── App.css       # Custom styles
│   │   ├── index.css     # Global styles with design tokens
│   │   └── components/   # Reusable UI components
│   ├── package.json      # Node dependencies
│   └── .env             # Frontend environment variables
├── design_guidelines.json # UI/UX design specifications
└── README.md
```

## 🔌 API Endpoints

### Air Quality
- `GET /api/air-quality/cities` - Get all cities with current AQI
- `GET /api/air-quality/city/:id` - Get specific city details
- `GET /api/air-quality/time-series/:cityId` - Get 24-hour historical data

### Health Data
- `GET /api/health/correlation` - AQI vs respiratory cases correlation
- `GET /api/health/insights` - Health impact insights and alerts

## 🎨 Design System

### Color Palette
- **Background**: Deep Indigo (#020617)
- **Primary (Saffron)**: #FF9933 - Warnings & highlights
- **Secondary (Green)**: #138808 - Safe/good indicators
- **Accent (Blue)**: #3B82F6 - Information

### AQI Categories
- **Good** (0-50): Green #22C55E
- **Moderate** (51-100): Yellow #EAB308
- **Unhealthy** (101-200): Orange #F97316
- **Very Unhealthy** (201-300): Red #EF4444
- **Hazardous** (300+): Dark Red #991B1B

## 🛠️ Tech Stack

### Frontend
- **React 19** - UI framework
- **Recharts** - Data visualization
- **React Simple Maps** - Interactive India map
- **Lucide React** - Icon library
- **Tailwind CSS** - Utility-first styling
- **Axios** - HTTP client

### Backend
- **FastAPI** - Modern Python API framework
- **Motor** - Async MongoDB driver
- **Pydantic** - Data validation
- **HTTPX** - Async HTTP client

### Database
- **MongoDB** - NoSQL database for caching air quality data

## 📊 Data Sources

- **Air Quality**: Simulated data based on real Indian city patterns
- **Health Correlation**: Algorithm-generated respiratory disease cases correlated with AQI levels
- **Future Integration**: Ready for AQICN/WAQI API or other real-time air quality APIs

## 🌟 Key Highlights

- ✅ **Frontend-Only Option**: Can run without backend using mock data
- ✅ **API-Ready**: Backend endpoints prepared for real API integration
- ✅ **Scalable**: MongoDB structure supports historical data storage
- ✅ **Urban Planner Focused**: Designed for decision-makers and researchers
- ✅ **Git-Ready**: Organized structure for easy deployment

## 🚢 Deployment

### Vercel (Frontend)
```bash
cd frontend
vercel
```

### Railway/Render (Backend)
```bash
# Configure environment variables in dashboard
# Deploy from GitHub repository
```

### Docker
```bash
# Coming soon
docker-compose up
```

## 📝 Environment Variables

### Backend (.env)
```env
MONGO_URL=mongodb://localhost:27017
DB_NAME=air_quality_db
CORS_ORIGINS=*
```

### Frontend (.env)
```env
REACT_APP_BACKEND_URL=http://localhost:8001
```

## 🤝 Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

## 📄 License

This project is open source and available under the MIT License.

## 👨‍💻 Author

Built with ❤️ for urban planners and health researchers monitoring air quality in India

---

**Note**: This dashboard uses simulated data for demonstration. For production use, integrate with real air quality APIs like AQICN, WAQI, or OpenAQ.
"