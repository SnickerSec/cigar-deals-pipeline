# Cigar Deals Pipeline

Automated GitHub Actions workflows for scraping cigar deal data.

## Architecture

This public repository contains only the workflow files. The actual scraper code lives in the private `cigar-things` repository. Workflows checkout the private repo at runtime and execute scrapers that save data to a PostgreSQL database.

```
cigar-deals-pipeline (public)     cigar-things (private)
├── .github/workflows/       -->  ├── app/scrapers/
│   ├── deals-*.yml               │   ├── deals/
│   ├── daily-*.yml               │   ├── daily/
│   ├── samplers-*.yml            │   └── brands/
│   └── maintenance-*.yml         ├── config/
└── README.md                     └── requirements.txt
```

## Required Secrets

Configure these secrets in this repository's Settings > Secrets and variables > Actions:

| Secret | Description |
|--------|-------------|
| `PRIVATE_REPO_TOKEN` | GitHub Personal Access Token with `repo` scope to access `SnickerSec/cigar-things` |
| `RAILWAY_DATABASE_URL` | PostgreSQL connection string for production database |

### Creating the PAT

1. Go to GitHub Settings > Developer settings > Personal access tokens > Tokens (classic)
2. Generate new token with `repo` scope (full control of private repositories)
3. Copy the token and add it as `PRIVATE_REPO_TOKEN` secret

## Workflows

### Daily Scrapers (Time-Sensitive Deals)
- `daily-01-famous-smoke-monster.yml` - Monster Deals (5 AM UTC)
- `daily-02-cigars-international-joecigar.yml` - Joe Cigar Specials (6 AM UTC)
- `daily-03-cigar-com-daily.yml` - Cigar.com Daily (7 AM UTC)
- `daily-04-cigar-place-daily.yml` - Cigar Place Daily
- `daily-05-holts-flash-sale.yml` - Holt's Flash Sales

### Main Deal Scrapers
- `deals-01-jr-cigars.yml` - JR Cigars Main Catalog
- `deals-02-famous-smoke.yml` - Famous Smoke Shop
- `deals-03-thompson-cigar.yml` - Thompson Cigar
- `deals-04-best-cigar-prices.yml` - Best Cigar Prices
- `deals-05-neptune-cigar.yml` - Neptune Cigar
- ... and more

### Sampler Scrapers
- `samplers-01-cigars-international.yml`
- `samplers-02-cigarpage.yml`
- `samplers-03-neptune-cigar.yml`
- `samplers-04-cuenca-cigars.yml`

### Maintenance
- `maintenance-01-update-brands.yml` - Update brand database
- `maintenance-02-fix-missing-brands.yml` - Fix missing brands
- `monitor-scraper-health.yml` - Health monitoring
- `data-quality-check.yml` - Data quality validation

## Manual Trigger

All workflows support manual triggering via the Actions tab or `gh` CLI:

```bash
gh workflow run "JR Cigars Main Catalog Scraper" --repo SnickerSec/cigar-deals-pipeline
```

## Data Flow

1. GitHub Actions triggers a workflow on schedule or manually
2. Workflow checks out the private `cigar-things` repository
3. Scraper runs and saves data to PostgreSQL database
4. CSV artifacts are uploaded for debugging (7-day retention)
