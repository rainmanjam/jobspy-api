# JobSpy Docker API

A Docker-containerized FastAPI application that provides secure API access to the Python JobSpy library, allowing you to search for jobs across multiple platforms including LinkedIn, Indeed, Glassdoor, Google, ZipRecruiter, Bayt, and Naukri.

## Features

- **Comprehensive Job Search**: Search across multiple job boards with a single API call
- **API Key Authentication**: Secure your API with x-api-key header authentication
- **Rate Limiting**: Prevent abuse with configurable rate limits
- **Caching**: Improve performance with response caching
- **Proxy Support**: Configure global proxies via environment variables
- **Customizable Defaults**: Set default search parameters via environment variables
- **CORS Support**: Enable cross-origin requests for frontend integration
- **Health Checks**: Monitor application health with dedicated endpoints
- **Comprehensive Logging**: Track API usage and troubleshoot issues

## Star History

[![Star History Chart](https://api.star-history.com/svg?repos=rainmanjam/jobspy-api&type=Date)](https://www.star-history.com/#rainmanjam/jobspy-api&Date)

## Support the Project

If you find JobSpy Docker API useful, please consider:
- ⭐️ Star this repository on GitHub: https://github.com/rainmanjam/jobspy-api
- 🍴 Fork it to contribute and customize.
- 👤 Follow the repo to stay updated with new features and releases.
- 📥 Pull the Docker image from Docker Hub:
```bash
  docker pull rainmanjam/jobspy-api:latest
```
  or visit https://hub.docker.com/r/rainmanjam/jobspy-api and doing the same there.

## Getting Started

### Prerequisites

- [Docker](https://docs.docker.com/get-docker/)
- [Docker Compose](https://docs.docker.com/compose/install/) (optional, but recommended)

### Running with Docker

#### Build and run the Docker container

```bash
# Build the Docker image
docker build -t jobspy-api .

# Run the container
docker run -p 8000:8000 \
  -e API_KEYS=your-api-key-1 \
  -e ENABLE_API_KEY_AUTH=true \
  jobspy-api
```

#### Additional Docker run options

You can configure the application by passing environment variables:

```bash
docker run -p 8000:8000 \
  -e API_KEYS=your-api-key-1,your-api-key-2 \
  -e DEFAULT_COUNTRY_INDEED=USA \
  -e DEFAULT_PROXIES=user:pass@host:port,user:pass@host:port \
  -e LOG_LEVEL=INFO \
  jobspy-api
```

### Running with Docker Compose

#### Production setup

1. Edit the environment variables in `docker-compose.yml` to match your requirements:

```yaml
environment:
  # API Security
  - API_KEYS=your-api-key-1,your-api-key-2
  - ENABLE_API_KEY_AUTH=true
  
  # Proxy Configuration (if needed)
  - DEFAULT_PROXIES=user:pass@host:port,user:pass@host:port
  
  # Other settings as needed
  - DEFAULT_COUNTRY_INDEED=USA
```

2. Start the application with Docker Compose:

```bash
docker-compose up -d
```

3. Access the API documentation at [http://localhost:8000/docs](http://localhost:8000/docs)

#### Development setup

For development with auto-reload:

```bash
# Uses docker-compose.dev.yml which mounts local directory and enables auto-reload
docker-compose -f docker-compose.dev.yml up
```

### Stopping the application

```bash
# If running with docker-compose
docker-compose down

# If running with docker
docker stop <container_id>
```

### Using the Makefile

The project includes a Makefile for common tasks:

```bash
# Show all available commands
make help

# Basic commands
make install            # Install dependencies
make run                # Run development server
make test               # Run tests
make docker-build       # Build Docker image with both version and latest tags
make docker-buildx      # Build multi-arch Docker image with both version and latest tags
make docker-push        # Push Docker image to Docker Hub (both version and latest tags)
make docker-pushx       # Push multi-arch Docker image to Docker Hub (both version and latest tags)
make docker-compose-up  # Start with Docker Compose (production)
make docker-compose-dev # Start with Docker Compose (development)

# Combined commands for streamlined workflows
make dev                # Run development server with auto-reload
make prod               # Build and run production container
make clean-start        # Remove containers, rebuild and start
make update             # Update dependencies and rebuild
make test-and-build     # Run tests and build if they pass
make ci                 # Run full CI pipeline (test, build, run)
make logs               # Show logs from running containers
make restart            # Restart running containers
make rebuild            # Rebuild and restart containers
```

## One-Click Deploy

[![Deploy on RepoCloud](https://d16t0pc4846x52.cloudfront.net/deploylobe.svg)](https://repocloud.io/details/JobSpy%20API/)

## Configuration

### Environment Variables

You can configure the application using environment variables:

| Variable | Description | Default |
|----------|-------------|---------|
| **API Security** | | |
| API_KEYS | Comma-separated list of valid API keys | [] |
| ENABLE_API_KEY_AUTH | Enable API key authentication | true |
| API_KEY_HEADER_NAME | Header name for API key | x-api-key |
| **Rate Limiting** | | |
| RATE_LIMIT_ENABLED | Enable rate limiting | true |
| RATE_LIMIT_REQUESTS | Maximum requests per timeframe | 100 |
| RATE_LIMIT_TIMEFRAME | Timeframe for rate limiting in seconds | 3600 |
| **Proxy Configuration** | | |
| DEFAULT_PROXIES | Comma-separated list of proxies | [] |
| CA_CERT_PATH | Path to CA Certificate file for proxies | null |
| **JobSpy Default Settings** | | |
| DEFAULT_SITE_NAMES | Default job boards to search | all available boards |
| DEFAULT_RESULTS_WANTED | Default number of results per site | 20 |
| DEFAULT_DISTANCE | Default distance in miles | 50 |
| DEFAULT_DESCRIPTION_FORMAT | Format of job description | markdown |
| DEFAULT_COUNTRY_INDEED | Default country for Indeed searches | null |
| **Caching** | | |
| ENABLE_CACHE | Enable response caching | true |
| CACHE_EXPIRY | Cache expiry time in seconds | 3600 |
| **Logging & CORS** | | |
| LOG_LEVEL | Logging level (INFO, DEBUG, etc.) | INFO |
| ENVIRONMENT | Environment name (development, production) | development |
| CORS_ORIGINS | Allowed origins for CORS | * |
| **API Documentation** | | |
| ENABLE_SWAGGER_UI | Enable Swagger UI docs | true |
| ENABLE_REDOC | Enable ReDoc documentation | true |
| SWAGGER_UI_PATH | URL path for Swagger UI | /docs |
| REDOC_PATH | URL path for ReDoc | /redoc |

### Environment Variable Override Chain

The application follows a specific precedence for loading environment variables:

1. Command line arguments
2. Docker Compose environment section
3. `.env` file in the project root
4. Dockerfile ENV values
5. Dockerfile ARG defaults

Note: `.env.local` is **not** loaded automatically by default. It's only used when:
- Explicitly loaded in your code
- Specified in the `env_file` section of docker-compose.yml
- Using the development setup with docker-compose.dev.yml

To explicitly load `.env.local`:
```bash
# Run the helper script before starting the application
python scripts/load_local_env.py
```

This loading order is important to understand when troubleshooting environment variable issues:
- Values from higher in the list override values from lower in the list
- When values appear to be incorrect, check at which level they're being defined
- Docker Compose environment variables can override `.env` values, which is a common source of confusion

### Debugging Environment Variables

The project includes several scripts to help debug environment variable issues:

```bash
# Check environment variables and configuration
python scripts/check_env.py

# Verify environment variable loading
python scripts/verify_env_loading.py

# Debug environment variable conflicts
python scripts/debug_env_conflicts.py

# Check configuration consistency
python scripts/check_config_consistency.py

# Inside Docker container
docker-compose run --rm jobspy-api python /app/scripts/check_env.py
```

## Examples of Disabling Documentation

### In docker-compose.yml:
```yaml
services:
  jobspy-api:
    # ...
    environment:
      # Disable API documentation in production
      - ENABLE_SWAGGER_UI=false
      - ENABLE_REDOC=false
```

### Using Docker run command:
```bash
docker run -p 8000:8000 \
  -e ENABLE_SWAGGER_UI=false \
  -e ENABLE_REDOC=false \
  jobspy-api
```

### In .env file:
```env
ENABLE_SWAGGER_UI=false
ENABLE_REDOC=false
```

## API Usage

All API endpoints require an API key to be passed in the `x-api-key` header if authentication is enabled.

### Endpoints

- `GET /api/v1/search_jobs` - Search for jobs with optional pagination and output format (`format=json|csv`)
- `GET /health` - Returns the health status of the API
- `GET /ping` - Simple ping endpoint for monitoring

### Parameters for `search_jobs`

| Parameter                | Type           | Description                                                                                  | Default      |
|--------------------------|----------------|----------------------------------------------------------------------------------------------|--------------|
| format                   | string         | Output format: `json` (default) or `csv`. If `csv`, returns a downloadable CSV file.         | json         |
| site_name                | list or string | Job sites to search on (indeed, linkedin, zip_recruiter, glassdoor, google, bayt, naukri)    | all          |
| search_term              | string         | Job search term                                                                              |              |
| google_search_term       | string         | Search term for Google jobs (only parameter for filtering Google jobs)                       |              |
| location                 | string         | Job location                                                                                 |              |
| distance                 | integer        | Distance in miles                                                                            | 50           |
| job_type                 | string         | Job type (fulltime, parttime, internship, contract)                                          |              |
| proxies                  | list           | List of proxies in format `user:pass@host:port`                                              |              |
| is_remote                | boolean        | Remote job filter                                                                            |              |
| results_wanted           | integer        | Number of job results per site                                                               | 20           |
| easy_apply               | boolean        | Filters for jobs hosted on the job board site                                                |              |
| description_format       | string         | Format of job description (`markdown`, `html`)                                               | markdown     |
| offset                   | integer        | Start search from this offset                                                                |              |
| hours_old                | integer        | Filter jobs by hours since posted                                                            |              |
| verbose                  | integer        | Controls verbosity (0=errors, 1=warnings, 2=all logs)                                        | 2            |
| linkedin_fetch_description | boolean      | Fetch full description and direct job url for LinkedIn                                       |              |
| linkedin_company_ids     | list[int]      | Search LinkedIn jobs with specific company ids                                               |              |
| country_indeed           | string         | Country for Indeed & Glassdoor                                                               |              |
| enforce_annual_salary    | boolean        | Converts wages to annual salary                                                              |              |
| ca_cert                  | string         | Path to CA Certificate file for proxies                                                      |              |

## CSV Output Example

You can request results as a CSV file by adding `?format=csv` to your request:

```bash
curl -X 'GET' 'http://localhost:8000/api/v1/search_jobs?site_name=indeed&search_term=engineer&format=csv' -H 'accept: text/csv' -o jobs.csv
```

The response will be a downloadable CSV file with all job fields.

## JobPost Schema

The API returns job objects with the following fields (fields may vary by provider):

| Field                  | Description                                      | Providers                |
|------------------------|--------------------------------------------------|--------------------------|
| title                  | Job title                                        | All                      |
| company                | Company name                                     | All                      |
| company_url            | Company website                                  | All                      |
| job_url                | Direct job posting URL                           | All                      |
| location.country       | Country                                          | All                      |
| location.city          | City                                             | All                      |
| location.state         | State                                            | All                      |
| is_remote              | Remote job flag                                  | All                      |
| description            | Job description                                  | All                      |
| job_type               | Job type (fulltime, parttime, etc.)              | All                      |
| job_function           | Job function/category                            | All                      |
| interval               | Salary interval (yearly, monthly, etc.)          | All                      |
| min_amount             | Minimum salary                                   | All                      |
| max_amount             | Maximum salary                                   | All                      |
| currency               | Salary currency                                  | All                      |
| salary_source          | Source of salary info                            | All                      |
| date_posted            | Date posted                                      | All                      |
| emails                 | Emails found in posting                          | All                      |
| job_level              | Job level                                        | LinkedIn                 |
| company_industry       | Company industry                                 | LinkedIn, Indeed         |
| company_country        | Company country                                  | Indeed                   |
| company_addresses      | Company addresses                                | Indeed                   |
| company_employees_label| Company size label                               | Indeed                   |
| company_revenue_label  | Company revenue label                            | Indeed                   |
| company_description    | Company description                              | Indeed                   |
| company_logo           | Company logo URL                                 | Indeed                   |
| skills                 | Required skills                                  | Naukri                   |
| experience_range       | Experience range                                 | Naukri                   |
| company_rating         | Company rating                                   | Naukri                   |
| company_reviews_count  | Company reviews count                            | Naukri                   |
| vacancy_count          | Number of vacancies                              | Naukri                   |
| work_from_home_type    | Work from home type                              | Naukri                   |

## Supported Countries for Indeed/Glassdoor

|                      |              |            |                |
|----------------------|--------------|------------|----------------|
| Argentina            | Australia*   | Austria*   | Bahrain        |
| Belgium*             | Brazil*      | Canada*    | Chile          |
| China                | Colombia     | Costa Rica | Czech Republic |
| Denmark              | Ecuador      | Egypt      | Finland        |
| France*              | Germany*     | Greece     | Hong Kong*     |
| Hungary              | India*       | Indonesia  | Ireland*       |
| Israel               | Italy*       | Japan      | Kuwait         |
| Luxembourg           | Malaysia     | Mexico*    | Morocco        |
| Netherlands*         | New Zealand* | Nigeria    | Norway         |
| Oman                 | Pakistan     | Panama     | Peru           |
| Philippines          | Poland       | Portugal   | Qatar          |
| Romania              | Saudi Arabia | Singapore* | South Africa   |
| South Korea          | Spain*       | Sweden     | Switzerland*   |
| Taiwan               | Thailand     | Turkey     | Ukraine        |
| United Arab Emirates | UK*          | USA*       | Uruguay        |
| Venezuela            | Vietnam*     |            |                |

(\* indicates also supported by Glassdoor)

## Troubleshooting

### API Issues
- If you receive a 429 error, you've exceeded the rate limit or the underlying job boards are blocking requests
- For Google jobs, use very specific search terms in the google_search_term parameter
- For Indeed searches, use precise search syntax with quotes and operators
- For high-volume usage, configure proxies to avoid being blocked

### Docker Troubleshooting

- **Container exits immediately**: Check the logs with `docker logs <container_id>`
- **Can't access the API**: Make sure ports are correctly mapped and the container is running
- **API key issues**: Ensure API_KEYS environment variable is set correctly
- **Proxy issues**: If using proxies, make sure they're correctly formatted and working
- **Permission issues**: If mounting volumes, ensure proper permissions are set
  - Shell scripts need execute permissions: `chmod +x scripts/*.sh`
  - For Windows users, Git may change line endings - use `git config --global core.autocrlf input` 
  - The container uses a special entrypoint script that fixes permissions automatically
- **Image tags**: Both `latest` and the version number are pushed to Docker Hub. If you don't see the version tag, ensure you are using the latest Makefile and pushing with `make docker-push` or `make docker-pushx`.

### Script Permission Issues

- Ensure all scripts have execute permissions:
  ```bash
  chmod +x scripts/*.sh
  ```

- For Windows users, ensure line endings are correct:
  ```bash
  git config --global core.autocrlf input
  ```

### Environment Variable Issues

If you're experiencing issues with environment variables:

1. **Verify variable values**: Use the debugging scripts to see which values are active
   ```bash
   python scripts/check_env.py
   ```

2. **Check variable precedence**: Remember that Docker Compose environment values override `.env` files
   ```bash
   # See the full override chain
   bash scripts/debug_env_load_order.sh
   ```

3. **Watch for conflicts**: Look for conflicting definitions in different places
   ```bash
   python scripts/debug_env_conflicts.py
   ```

4. **Docker environment**: When running in Docker, use this command to debug
   ```bash
   docker-compose run --rm jobspy-api bash -c "env | grep -E 'API_KEY|ENABLE_|LOG_LEVEL'"
   ```

5. **Inspect container**: If necessary, inspect the container directly
   ```bash
   docker-compose exec jobspy-api bash
   ```

6. **API configuration endpoint**: If the application is running, check
   ```
   http://localhost:8000/api-config
   http://localhost:8000/config-sources
   ```

### Common Issues and Solutions

1. **API authentication not working**:
   - Ensure `ENABLE_API_KEY_AUTH=true` and `API_KEYS` is set correctly
   - Verify you're including the proper header in requests (`x-api-key`)
   - Check `/auth-status` endpoint for diagnostics

2. **Container fails to start**:
   - Check logs with `docker-compose logs jobspy-api`
   - Ensure script permissions are correct: `chmod +x scripts/*.sh`
   - Try running with the debug configuration: `docker-compose -f docker-compose.dev.yml up`

3. **API errors with 500 status code**:
   - Check Docker logs for detailed error information
   - Increase logging level: `LOG_LEVEL=DEBUG`
   - Look for specific scraper errors related to job boards

4. **Changes to `.env` file not taking effect**:
   - Remember Docker Compose may have overriding environment variables
   - Rebuild container: `docker-compose build` then `docker-compose up -d`
   - Check effective values with `/config-sources` endpoint

## Versioning and Releases

The JobSpy Docker API follows [Semantic Versioning](https://semver.org/) (MAJOR.MINOR.PATCH).

### Current Version

```bash
# View the current version
python -c "from app import __version__; print(__version__)"

# Or using make
make version
```

Current version: **1.0.0**

### Version Management

The project provides convenient commands for updating version numbers:

```bash
# Increment patch version (1.0.0 -> 1.0.1)
make version-patch

# Increment minor version (1.0.0 -> 1.1.0)
make version-minor

# Increment major version (1.0.0 -> 2.0.0)
make version-major
```

These commands update the version in `app/__init__.py` automatically.

### Release Process

#### Creating a GitHub Release

1. Update the version number using the appropriate make command
2. Update the CHANGELOG.md with details of changes
3. Commit changes: `git commit -am "Bump version to X.Y.Z"`
4. Create a git tag: `git tag -a vX.Y.Z -m "Version X.Y.Z"`
5. Push changes: `git push && git push --tags`
6. Go to GitHub and create a new release based on the tag
   - Navigate to: https://github.com/[username]/job-spy-fastapi/releases/new
   - Select the tag
   - Add release notes
   - Publish the release

#### Docker Image Releases

Releases are automatically published to Docker Hub on new GitHub releases:

```bash
# Build and push to Docker Hub (both version and latest tags)
make docker-push
```

This will build the Docker image with the current version tag **and** the `latest` tag and publish both to Docker Hub.

For multi-arch builds:

```bash
make docker-pushx
```

#### Using Specific Versions

You can run a specific version of the API using Docker:

```bash
# Pull a specific version
docker pull username/jobspy-api:1.0.0

# Or pull the latest tag
docker pull username/jobspy-api:latest

# Run a specific version
docker run -p 8000:8000 username/jobspy-api:1.0.0

# Or run the latest
docker run -p 8000:8000 username/jobspy-api:latest
```

### Changelog

The project maintains a detailed changelog in the CHANGELOG.md file, which includes:
- New features
- Bug fixes
- Breaking changes
- Deprecation notices

Always check the changelog before upgrading to a new version, especially for major releases.

## API Versioning

The JobSpy Docker API uses URL path versioning to ensure backward compatibility as the API evolves.

### Current Version

- **v1** - The current stable API version (e.g., `/api/v1/search_jobs`)

### Versioning Strategy

- All API endpoints are versioned with a `v{number}` in the URL path
- Breaking changes will only be introduced in new API versions
- Older API versions will remain supported for a reasonable deprecation period
- Non-breaking enhancements may be added to existing versions

### Using API Versions

Always include the version in your API requests:

```bash
# Using the v1 API
curl -X 'GET' \
  'http://localhost:8000/api/v1/search_jobs?site_name=indeed&search_term=software%20engineer' \
  -H 'accept: application/json' \
  -H 'x-api-key: your-api-key'
```

### Version Lifecycle

- **Current** - v1 (active development, fully supported)
- **Future** - When v2 is released, v1 will enter maintenance mode
- **Deprecated** - Versions in this state will be announced with a timeline for removal
- **Retired** - Versions that are no longer available

Version deprecation notices will be posted in release notes and the API will return deprecation warning headers for endpoints approaching retirement.

## Available Parameters

| Parameter | Type | Description |
|-----------|------|-------------|
| **Pagination Parameters** | | |
| paginate | boolean | Enable pagination (default: false) |
| page | integer | Page number when pagination is enabled (default: 1) |
| page_size | integer | Number of results per page when pagination is enabled (default: 10, max: 100) |
| **Basic Search Parameters** | | |
| site_name | list or string | Job sites to search on (indeed, linkedin, zip_recruiter, glassdoor, google, bayt, naukri) |
| search_term | string | Job search term |
| google_search_term | string | Search term for Google jobs (only parameter for filtering Google jobs) |
| location | string | Job location |
| distance | integer | Distance in miles (default: 50) |
| **Job Filters** | | |
| job_type | string | Job type (fulltime, parttime, internship, contract) |
| is_remote | boolean | Remote job filter |
| hours_old | integer | Filters jobs by the number of hours since the job was posted |
| easy_apply | boolean | Filters for jobs that are hosted on the job board site |
| **Advanced Parameters** | | |
| results_wanted | integer | Number of results per site (default: 20) |
| description_format | string | Format of job description (markdown, html) (default: markdown) |
| offset | integer | Starts the search from an offset |
| verbose | integer | Controls verbosity (0: errors only, 1: errors+warnings, 2: all logs) (default: 2) |
| linkedin_fetch_description | boolean | Fetch full LinkedIn descriptions (slower) (default: false) |
| linkedin_company_ids | list of integers | LinkedIn company IDs to filter by |
| country_indeed | string | Country filter for Indeed & Glassdoor (default: USA) |
| enforce_annual_salary | boolean | Convert wages to annual salary (default: false) |
| ca_cert | string | Path to CA Certificate file for proxies |

## Response Format

The API returns results in two possible formats, depending on whether pagination is enabled:

### Standard Response (paginate=false)

```json
{
  "count": 42,
  "jobs": [
    {
      "SITE": "linkedin",
      "TITLE": "Software Engineer",
      "COMPANY": "Example Corp",
      "LOCATION": "San Francisco, CA",
      "DATE": "2023-06-01",
      "LINK": "https://www.linkedin.com/jobs/view/123456789",
      "DESCRIPTION": "Job description markdown text...",
      // ...additional job fields
    },
    // ...more jobs
  ],
  "cached": false
}
```

### Paginated Response (paginate=true)

```json
{
  "count": 42,
  "total_pages": 5,
  "current_page": 1,
  "page_size": 10,
  "jobs": [
    // ...array of job objects (max 10 in this example)
  ],
  "cached": false,
  "next_page": "http://localhost:8000/api/v1/search_jobs?paginate=true&page=2&...",
  "previous_page": null
}
```

## Caching Behavior

Results are cached based on search parameters to improve performance and reduce load on job sites:

- Cache is enabled by default but can be disabled using the `ENABLE_CACHE` environment variable
- Default cache expiry is 1 hour (3600 seconds), configurable via `CACHE_EXPIRY`
- The `cached` field in the response indicates whether results came from cache
- Cached results are returned only when the exact same search parameters are used

## Limitations

### Indeed limitations
Only one from this list can be used in a search:
- hours_old
- job_type & is_remote
- easy_apply

### LinkedIn limitations
Only one from this list can be used in a search:
- hours_old
- easy_apply

## Error Handling

The API provides descriptive error responses with suggestions for fixing common issues:

### Validation Errors

When you provide invalid parameters, the API will return:
- The invalid parameter
- What was wrong with it
- Valid options to use instead
- Suggestions for fixing the issue

Example response for an invalid site name:

```json
{
  "error": "Invalid job site name(s)",
  "invalid_values": ["linkdin"],
  "valid_sites": ["indeed", "linkedin", "zip_recruiter", "glassdoor", "google", "bayt", "naukri"],
  "suggestions": [
    {
      "parameter": "site_name",
      "message": "'linkdin' is not a valid job site",
      "suggestion": "Use one or more of the valid job sites: indeed, linkedin, zip_recruiter, glassdoor, google, bayt, naukri",
      "expected_type": "string or list",
      "description": "Job sites to search on (e.g., indeed, linkedin)"
    }
  ]
}
```

### Parameter Combination Errors

Some parameters cannot be used together. The API will explain these limitations:

```json
{
  "error": "Invalid parameter combination for Indeed",
  "message": "Indeed searches cannot combine hours_old with job_type, is_remote, or easy_apply",
  "suggestion": "Use either hours_old OR job filtering parameters, but not both"
}
```

### General Error Suggestions

For other errors, the API will suggest potential fixes:

```json
{
  "error": "Error scraping jobs",
  "message": "Connection timed out",
  "suggestion": "The request timed out. Try reducing the number of job sites or results_wanted"
}
```

### Error Handling

The API returns standard HTTP status codes:

- `200 OK` - Request was successful
- `400 Bad Request` - Invalid parameters
- `403 Forbidden` - Missing or invalid API key
- `404 Not Found` - Requested page not found (when using pagination)
- `429 Too Many Requests` - Rate limit exceeded
- `500 Internal Server Error` - Server error (usually from job board sites)

Error responses include detailed information:

```json
{
  "error": "Error type",
  "detail": "Detailed error message",
  "status_code": 400,
  "path": "/api/v1/search_jobs"
}
```

## Troubleshooting
