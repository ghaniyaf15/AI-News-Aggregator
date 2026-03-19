# AI News Aggregator

An AI-powered news aggregator that collects, processes, and curates AI-related news from multiple sources, generates personalized summaries, and sends daily email digests.

## Features

- **Multi-source scraping**: YouTube videos, OpenAI blog, Anthropic news
- **AI-powered summarization**: GPT-4o-mini generates concise article summaries
- **Personalized curation**: Ranks articles based on user interests and expertise
- **Duplicate detection**: Identifies and filters duplicate content
- **Email delivery**: Sends styled HTML/plain text digests via Gmail
- **Docker deployment**: Containerized PostgreSQL database

## Architecture

The application follows a 6-phase pipeline:
1. **Scraping** - RSS feeds from YouTube, OpenAI, Anthropic
2. **Processing** - Extract transcripts and convert to Markdown
3. **Digestion** - Generate AI summaries
4. **Duplicate Detection** - Identify similar content
5. **Curation** - Rank by user relevance
6. **Email Distribution** - Send personalized digest

## Setup Instructions

### Prerequisites
- Python 3.12+
- Docker Desktop
- Gmail account with app password

### 1. Clone and Install Dependencies
```bash
git clone <repository-url>
cd ai-news-aggregator
pip install openai sqlalchemy psycopg2-binary feedparser youtube-transcript-api pydantic python-dotenv requests markdown markdownify beautifulsoup4
```

### 2. Configure Environment
Copy the example environment file:
```bash
cp app/example.env .env
```

Edit `.env` with your credentials:
```env
OPENAI_API_KEY=your_openai_api_key
MY_EMAIL=your_email@gmail.com
APP_PASSWORD=your_gmail_app_password
POSTGRES_USER=postgres
POSTGRES_PASSWORD=postgres
POSTGRES_DB=ai_news_aggregator
POSTGRES_HOST=localhost
POSTGRES_PORT=5432
```

### 3. Start Database
This project is designed to work with PostgreSQL in Docker, but it can also run using a local SQLite database (no Docker required).

**Option A (PostgreSQL + Docker) — recommended for production-like behavior**
```bash
docker-compose -f docker/docker-compose.yml up -d
```

**Option B (SQLite, no Docker)**
1. Create a `.env` file from the example:
   ```bash
   cp app/example.env .env
   ```
2. Enable SQLite by uncommenting the `DATABASE_URL` line in `.env` (or add it yourself):
   ```env
   DATABASE_URL=sqlite:///ai_news_aggregator.db
   ```

### 4. Initialize Database
```bash
python -c "from app.database.create_tables import create_all_tables; create_all_tables()"
```

### 5. Run the Pipeline
```bash
python main.py
```

Default parameters: 24 hours, top 10 articles. Customize with:
```bash
python main.py --hours 48 --top_n 5
```

## Configuration

### YouTube Channels
Edit `app/config.py` to add more channels:
```python
YOUTUBE_CHANNELS = [
    "UC1234567890",  # Add channel IDs
]
```

### User Profile
Customize `app/profiles/user_profile.py` for different users.

## API Keys Required

- **OpenAI API Key**: For summarization, ranking, and email generation
- **Gmail App Password**: For sending emails (enable 2FA and create app password)

## Database Schema

- `youtube_videos`: Video metadata and transcripts
- `openai_articles`: OpenAI blog posts
- `anthropic_articles`: Anthropic news with Markdown content
- `digests`: AI-generated summaries with duplicate tracking

## Troubleshooting

### Docker Issues
- Ensure Docker Desktop is running
- Check port 5432 availability

### Database Connection
- Verify PostgreSQL container is running: `docker ps`
- Check connection: `docker exec -it ai-news-aggregator-db psql -U postgres -d ai_news_aggregator`

### Missing Dependencies
- Install additional packages if needed
- For docling alternative, the project uses markdownify for HTML conversion

## Development

### Adding New Sources
1. Create scraper in `app/scrapers/`
2. Add database model in `app/database/models.py`
3. Update repository methods
4. Integrate into `app/runner.py`

### Customizing AI Agents
- Modify prompts in `app/agent/`
- Adjust Pydantic models for different outputs

## License

This project is for educational purposes as part of the AI News Aggregator assignment.
