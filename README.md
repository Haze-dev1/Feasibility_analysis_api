# Feasibility Analysis Tool

A comprehensive business idea feasibility analysis tool that evaluates startup ideas using multiple data sources and generates detailed reports with AI-powered insights.

## Features

- **Multi-source Analysis**: Analyzes ideas using Reddit trends, competition data, investor interest, and news mentions
- **AI-Powered Reports**: Generates detailed feasibility reports using Ollama's Llama 3.2 model
- **PDF Report Generation**: Creates downloadable PDF reports with analysis and recommendations
- **Dual Interface**: Both REST API and interactive web interface available
- **Real-time Data**: Fetches live data from Reddit, Google Search, AngelList, and Google News

## Architecture

The project consists of two main components:

1. **`api.py`** - Flask REST API for programmatic access
2. **`stream.py`** - Streamlit web interface for interactive analysis

## Requirements

### Python Dependencies

```bash
pip install flask streamlit praw requests serpapi beautifulsoup4 fpdf2
```

### External Dependencies

- **Ollama**: Required for AI analysis
  ```bash
  # Install Ollama
  curl -fsSL https://ollama.ai/install.sh | sh
  
  # Pull Llama 3.2 model
  ollama pull llama3.2:1b
  ```

### API Keys Required

1. **Reddit API**: Create an app at https://www.reddit.com/prefs/apps
2. **SerpAPI**: Get your key at https://serpapi.com/
3. Update the credentials in both files:
   - `REDDIT_CLIENT_ID`
   - `REDDIT_CLIENT_SECRET`
   - `SERPAPI_KEY`

## Installation

1. Clone the repository:
```bash
git clone <repository-url>
cd feasibility-analysis-tool
```

2. Install dependencies:
```bash
pip install -r requirements.txt
```

3. Set up API credentials in both `api.py` and `stream.py`

4. Install and configure Ollama with Llama 3.2 model

## Usage

### Web Interface (Streamlit)

Launch the interactive web interface:

```bash
streamlit run stream.py
```

1. Enter your business idea in the text input
2. Click "Analyze Idea" to start the analysis
3. View the feasibility scores and AI analysis
4. Download the PDF report

### REST API (Flask)

Start the Flask API server:

```bash
python api.py
```

The API will be available at `http://localhost:5000`

#### API Endpoints

##### 1. Complete Analysis with PDF Generation
```http
POST /analyze-and-generate
Content-Type: application/json

{
    "idea": "Your business idea here",
    "return_json": false  // Optional: set to true to get JSON response instead of PDF
}
```

## Scoring Methodology

The tool evaluates ideas across four key metrics:

### 1. Reddit Trend Score (30% weight)
- Searches Reddit for posts related to your idea
- Calculates average upvotes using logarithmic scaling
- Higher scores indicate stronger community interest

### 2. Competition Score (25% weight)
- Uses SerpAPI to analyze search results
- Inverse relationship: fewer competitors = higher score
- Ranges from 25 (high competition) to 95 (low competition)

### 3. Investor Interest Score (25% weight)
- Scrapes AngelList for related startups
- Scores based on number of similar projects receiving funding
- Range: 30-90 based on investor activity

### 4. News Mentions Score (20% weight)
- Analyzes Google News coverage
- Higher scores for ideas with more media attention
- Range: 30-90 based on article count

### Final Score Calculation
```
Final Score = (Reddit × 0.3) + (Competition × 0.25) + (Investor × 0.25) + (News × 0.2)
```

## Output

### Feasibility Scores
- Individual scores for each metric (0-100)
- Weighted final feasibility score
- Clear breakdown of strengths and weaknesses

### AI Analysis Report
- Detailed analysis using Ollama's Llama 3.2 model
- Strategic recommendations
- Market insights and potential challenges
- Actionable next steps

### PDF Report
- Professional formatting with all scores and analysis
- Downloadable for sharing and reference
- Includes both quantitative metrics and qualitative insights

## Configuration

### Customizing Weights
Modify the scoring weights in the `get_feasibility_score()` function:

```python
scores["final_feasibility_score"] = round(
    (scores["reddit_trend_score"] * 0.3) +      # Adjust Reddit weight
    (scores["competition_score"] * 0.25) +      # Adjust competition weight
    (scores["investor_interest_score"] * 0.25) + # Adjust investor weight
    (scores["news_mentions_score"] * 0.2)       # Adjust news weight
)
```

### Customizing Search Parameters
- **Reddit search limit**: Modify `limit` parameter in `get_reddit_trend_score()`
- **Subreddit targeting**: Change `subreddit` parameter for specific communities
- **Search timeframes**: Adjust `sort` parameter for different time periods

## Error Handling

The tool includes robust error handling:
- API rate limiting protection with sleep intervals
- Fallback scores when data sources are unavailable
- Graceful degradation if external services fail
- Detailed error logging for debugging
