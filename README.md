# StatBot - Match Statistics Scrapper

StatBot is a Go-based automated system that scrapes basketball player statistics from various international leagues and websites, notifying subscribers via a Telegram bot whenever new match data is available.

## 🚀 Features

- **Multi-Source Scraping**: Support for various platforms including:
  - ESPN
  - Eurobasket
  - NBL (Australia)
  - B.League (Japan)
  - BNXT League (Belgium/Netherlands)
  - British Basketball League
  - B3 League
- **Automated Monitoring**: Background ticker runs every 5 minutes to fetch the latest data.
- **Duplicate Prevention**: Implements data hashing to ensure subscribers only receive notifications for new/updated statistics.
- **Telegram Integration**:
  - `/subscribe`: Request access to notifications.
  - `/approve <id>`: Admin command to approve new subscribers.
  - `/check`: Check the status of the background ticker.
- **Containerized**: Fully dockerized setup with PostgreSQL.

## 🛠 Tech Stack

- **Language**: Go (Golang)
- **Database**: PostgreSQL (with GORM)
- **Scraping Libraries**: 
  - [Colly](https://github.com/gocolly/colly) (Static scraping)
  - [Rod](https://github.com/go-rod/rod) (Dynamic scraping with Chromium)
- **Notification**: [Telegram Bot API](https://github.com/go-telegram-bot-api/telegram-bot-api)
- **Configuration**: [Viper](https://github.com/spf13/viper)
- **Deployment**: Docker & Docker Compose

## 📋 Prerequisites

- Docker and Docker Compose
- Telegram Bot Token (obtain from [@BotFather](https://t.me/BotFather))
- Your Telegram User ID (for Admin privileges)

## ⚙️ Setup

1. **Clone the repository**:
   ```bash
   git clone <repository-url>
   cd match-stat-scrapper
   ```

2. **Configure the application**:
   Copy the example config and fill in your details:
   ```bash
   cp config.yaml.example config.yaml
   ```
   Edit `config.yaml`:
   - Set `telegram.token` to your bot token.
   - Set `telegram.adminID` to your personal Telegram ID.
   - Update database credentials if necessary.

3. **Run with Docker**:
   ```bash
   docker-compose up --build -d
   ```

## 🔄 Project Structure & Flow

### Application Flow
1. **Initialization**: `main.go` connects to the database, seeds initial player URLs, and starts both the Telegram bot and the background ticker.
2. **Scheduling**: The `job.StartTicker` runs every 5 minutes.
3. **Processing**: `processor.FetchPlayerStats` iterates through all tracked players in the database.
4. **Scraping**: Based on the URL pattern, the appropriate scraper is selected (using `Colly` for static HTML or `Rod/Chromium` for JS-heavy sites).
5. **Deduplication**: Scraped stats are hashed and compared against the `scrapped_data` table in PostgreSQL.
6. **Notification**: If the data is new, it is stringified and sent to all approved Telegram subscribers via `notifier.PublishToSubscribers`.

### Key Directories
- `/scrapper`: Contains specialized logic for each supported website.
- `/processor`: Orchestrates the scraping and data validation.
- `/notifier`: Manages Telegram bot interactions and notifications.
- `/models`: Database and API data structures.
- `/db`: Database connection, repository patterns, and seeders.

## 🤖 Bot Commands

- `/start`: Initialize interaction with the bot.
- `/subscribe`: Request to receive automatic statistics updates.
- `/check`: Returns the current ticker count (useful for debugging).
- `/approve <chatID>`: (Admin only) Approves a user's subscription request.