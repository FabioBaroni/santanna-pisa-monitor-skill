# Sant'Anna Pisa Monitor

Hermes Agent skill for monitoring job postings, admissions, PhD programs, and courses from the **Scuola Superiore Sant'Anna** in Pisa, Italy.

## What It Does

Automatically tracks and extracts information from the university website:

- **Job postings** — Research contracts, postdoc positions, teaching roles, technical/administrative staff
- **PhD admissions** — Open positions, application deadlines, special deadlines (e.g. Economics)
- **Degree program admissions** — Bachelor (I livello) and Master (II livello) entrance exam deadlines
- **Seasonal Schools** — Short intensive courses (36 per academic year) with dates and deadlines
- **University Master programs** — 1st and 2nd level Master degree programs

## Features

- Zero API keys required — uses the university's public website
- Parses 8 key pages for complete coverage
- Filters job postings by 11 categories (staff type, role, etc.)
- Tracks ~36 Seasonal School courses per year
- Standardized report output format
- Ready for daily cronjob automation via Hermes Agent

## Installation

### Via Hermes Agent

```
hermes skills install FabioBaroni/santanna-pisa-monitor-skill
```

### Manual Installation

Copy the `SKILL.md` file to your Hermes skills directory:

```bash
# Clone the repo
git clone https://github.com/FabioBaroni/santanna-pisa-monitor-skill.git

# Copy the skill to your Hermes Agent skills directory
cp santanna-pisa-monitor-skill/SKILL.md ~/.hermes/skills/research/santanna-pisa/
```

## Usage

### One-shot check

Load the skill, then ask your Hermes Agent to:
> "Check Sant'Anna Pisa for new job postings"
> "Show me upcoming PhD deadlines"
> "What Seasonal Schools are coming up?"

### Daily monitoring (cronjob)

Set up an automated daily report that gets delivered to your messaging platform:

```
hermes cron create --schedule "0 9 * * *" --deliver telegram --prompt "Load the santanna-pisa skill and run the full monitoring workflow described in it, then generate a standardized daily report."
```

The agent will navigate 8 pages, extract all deadlines and opportunities, and send you a concise daily summary.

## Pages Monitored

| Category | Pages | Content |
|----------|-------|---------|
| Jobs | 4 pages | Active postings, expiring deadlines, 11 role categories |
| PhD Admissions | 2 pages | Open positions, general + special deadlines |
| Degree Programs | 3 pages | Bachelor/Master entrance exam deadlines |
| Seasonal School | 2 pages | ~36 short courses with dates per academic year |

## Notes

- The Sant'Anna website is Drupal-based with server-side rendering (no JS needed for data)
- URLs for PDFs and specific pages change annually — the skill uses stable hub pages
- Seasonal School pages have a multi-column layout that requires scrolling to capture all courses
- No official RSS feeds exist — monitoring is done via browser automation

## License

MIT License — see [LICENSE](LICENSE) for details.

## Author

Baroni Fabio
