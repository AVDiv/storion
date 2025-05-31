# Storion Platform

A comprehensive news aggregation and analysis platform that collects, processes, and delivers personalized news content. The platform consists of multiple interconnected components working together to provide users with intelligent news curation and insights.

## Platform Architecture

The Storion platform is built with a microservices architecture consisting of the following components:

### Core Components

1. **Storion Collector Engine** (`storion-collector-engine/`) - Python-based news extraction service
2. **Storion Aggregation Workflow** (`storion-aggregation-workflow/`) - Prefect-based ML processing pipeline
3. **Storion Backend** (`storion-backend/`) - NestJS REST API backend
4. **Storion App** (`storion-mobile-app/`) - Ionic React mobile application

### Infrastructure Components

5. **Kafka Self-Hosted Setup** (`kafka-self-hosted-setup/`) - Message streaming infrastructure
6. **Utilities** (`utils/`) - Helper scripts and tools

## Prerequisites

Before setting up the Storion platform, ensure you have the following accounts and tools:

### Required Accounts

- **Google Cloud Platform** - For storage, credentials, and cloud services
- **MongoDB Atlas** (or local MongoDB) - For user data storage
- **Neo4j AuraDB** (or local Neo4j) - For graph-based news data
- **Prefect Cloud** - For workflow orchestration
- **Modal** - For cloud ML model execution
- **PostHog** - For analytics and user tracking

### Required Tools

- **Docker & Docker Compose** - For containerization
- **Python 3.11+** with **UV** package manager
- **Node.js 18+** with **pnpm** package manager
- **Ionic CLI** and **Capacitor CLI** (for mobile development)

## Setup Guide

Follow this step-by-step guide to set up the complete Storion platform:

### Phase 1: Infrastructure Setup

#### 1. Set up Kafka Infrastructure

```fish
cd kafka-self-hosted-setup
# Follow the README.md for Kafka setup
docker-compose up -d
```

**Purpose**: Message streaming between collector and processing services

#### 2. Set up Cloud Services

- **MongoDB**: Create database for user data and preferences
- **Neo4j AuraDB**: Create graph database for news articles and relationships
- **Google Cloud Storage**: Create buckets for artifacts and code storage
- **Prefect Cloud**: Create workspace and obtain API credentials
- **Modal**: Set up account for ML model execution
- **PostHog**: Create project for analytics

### Phase 2: Data Collection & Processing

#### 3. Deploy Collector Engine

```fish
cd storion-collector-engine
# Follow the README.md for collector setup
docker-compose up -d
```

**Purpose**: Collects news from RSS feeds and web sources, publishes to Kafka

#### 4. Deploy Aggregation Workflow

```fish
cd storion-aggregation-workflow
# Follow the README.md for workflow setup
uv sync
prefect deploy --all
```

**Purpose**: Processes raw news data with ML models, creates article groups and summaries

### Phase 3: API & Frontend

#### 5. Deploy Backend API

```fish
cd storion-backend
# Follow the README.md for backend setup
pnpm install
pnpm run start:prod
```

**Purpose**: Provides REST API for authentication, user management, and data access

#### 6. Deploy Mobile Application

```fish
cd storion-app
# Follow the README.md for app setup
pnpm install
ionic cap run android  # or ios
```

**Purpose**: Mobile interface for users to consume personalized news

## Component Dependencies

The components should be set up in this specific order due to their dependencies:

```
┌─────────────────┐    ┌──────────────────┐
│ Kafka Setup     │──▶│ Aggregation      │
│ (Infrastructure)│    │ Workflow         │
└─────────────────┘    └─────────┬────────┘
          ▲                      │
          │                      ▼
┌─────────┴───────┐    ┌──────────────────┐
│ Collector       │    │ Cloud Services   │
│ Engine          │    │ (Databases, etc) │
└─────────────────┘    └─────────┬────────┘
                                 │
                                 ▼
                       ┌──────────────────┐
                       │ Backend API      │
                       └─────────┬────────┘
                                 │
                                 ▼
                       ┌──────────────────┐
                       │ Mobile App       │
                       └──────────────────┘
```

## Data Flow

1. **Collection**: Collector Engine gathers news from various sources
2. **Streaming**: Raw news data is published to Kafka topics
3. **Processing**: Aggregation Workflow consumes Kafka messages and applies ML processing
4. **Storage**: Processed articles and relationships are stored in Neo4j
5. **API**: Backend provides REST endpoints for accessing processed data
6. **Consumption**: Mobile app displays personalized news feeds to users

## Environment Configuration

Each component requires specific environment variables. Create `.env` files in each directory:

### Shared Environment Variables

```env
# Kafka Configuration
KAFKA_BOOTSTRAP_SERVERS=localhost:9092

# Neo4j Configuration
NEO4J_URI=neo4j+s://your-auradb-instance.databases.neo4j.io
NEO4J_USERNAME=neo4j
NEO4J_PASSWORD=your-neo4j-password

# MongoDB Configuration
DATABASE_CONNECTION_STRING=mongodb+srv://user:pass@cluster.mongodb.net/storion

# PostHog Analytics
POSTHOG_API_KEY=your-posthog-api-key
POSTHOG_HOST=https://app.posthog.com
```

Refer to each component's README for complete environment variable lists.

## Monitoring & Operations

### Health Checks

- **Kafka**: `http://localhost:8080` (Conduktor Console)
- **Collector Engine**: Check Docker container logs
- **Aggregation Workflow**: Monitor in Prefect Cloud dashboard
- **Backend API**: `http://localhost:3000/docs` (API documentation)
- **Mobile App**: Device logs and PostHog analytics

### Logs & Debugging

```fish
# View Kafka logs
cd kafka-self-hosted-setup && docker-compose logs -f

# View Collector logs
cd storion-collector-engine && docker-compose logs -f

# View Backend logs
cd storion-backend && pnpm run start:dev

# View Workflow logs
# Check Prefect Cloud dashboard
```

## Development Workflow

1. **Start Infrastructure**: Kafka and databases
2. **Run Collector**: Begin news collection
3. **Monitor Processing**: Check Prefect workflow execution
4. **Develop API**: Backend development with hot reload
5. **Test Mobile**: Use Ionic dev server or device testing

## Production Deployment

### Cloud Deployment Checklist

- [ ] Kafka cluster on cloud provider (Confluent Cloud, AWS MSK, etc.)
- [ ] MongoDB Atlas cluster configured
- [ ] Neo4j AuraDB instance running
- [ ] Prefect Cloud workflows deployed
- [ ] Modal functions deployed
- [ ] Backend API deployed (AWS, GCP, etc.)
- [ ] Mobile apps published to app stores

### Security Considerations

- Use environment-specific credentials
- Enable TLS/SSL for all database connections
- Implement proper API authentication and authorization
- Use secrets management for production deployments
- Enable monitoring and alerting

## Troubleshooting

### Common Issues

1. **Kafka Connection Errors**: Verify Kafka broker accessibility
2. **Database Connection Failures**: Check connection strings and credentials
3. **ML Model Loading Issues**: Ensure sufficient memory and GPU resources
4. **API Authentication Problems**: Verify JWT secrets and OAuth configuration
5. **Mobile Build Errors**: Check platform-specific requirements

### Getting Help

- Check individual component READMEs for detailed troubleshooting
- Review logs for specific error messages
- Verify all prerequisites are installed and configured
- Ensure services are started in the correct order
