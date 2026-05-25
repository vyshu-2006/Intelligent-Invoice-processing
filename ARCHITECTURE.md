# System Architecture

## Overview

The Intelligent Invoice Processing system is built on a modular, event-driven architecture using n8n as the orchestration platform with AI-powered intelligence from OpenAI's GPT-4.

## Architecture Diagram

```
┌─────────────────────────────────────────────────────────────────┐
│                     INPUT SOURCES                               │
├─────────────────────────────────────────────────────────────────┤
│   Google Drive Monitoring  │  Webhook API  │  Manual Upload     │
└──────────┬──────────────────────┬──────────────────────┬────────┘
           │                      │                      │
           └──────────┬───────────┴───────────┬──────────┘
                      │                       │
           ┌──────────▼───────────────────────▼────────┐
           │    FILE VALIDATION & EXTRACTION          │
           │  - PDF Format Validation                 │
           │  - Text Extraction from PDF              │
           │  - Error Handling                        │
           └──────────┬─────────────────────────────────┘
                      │
           ┌──────────▼─────────────────────────────────┐
           │    AI-POWERED DATA EXTRACTION             │
           │  - GPT-4 Intelligence                     │
           │  - 20+ Data Field Extraction              │
           │  - Confidence Scoring                     │
           └──────────┬─────────────────────────────────┘
                      │
           ┌──────────▼─────────────────────────────────┐
           │  VALIDATION & QUALITY CONTROL             │
           │  - Data Consistency Checks                │
           │  - Required Field Validation              │
           │  - Confidence Thresholds                  │
           └──────────┬─────────────────────────────────┘
                      │
         ┌────────────┴────────────┐
         │                         │
    ┌────▼──────┐            ┌────▼──────┐
    │ APPROVED  │            │  REVIEW   │
    │ (High     │            │  (Low     │
    │ Confidence)            │ Confidence)
    │           │            │           │
    ├───────────┼────────────┤───────────┤
    │ • Google  │            │ • Google  │
    │   Sheets  │            │   Sheets  │
    │ • Emails  │            │ • Emails  │
    │ • Logs    │            │ • Logs    │
    └────┬──────┘            └────┬──────┘
         │                        │
         └────────┬───────────────┘
                  │
        ┌─────────▼────────────┐
        │  AUDIT & ANALYTICS   │
        │  - Processing Logs   │
        │  - Metrics Tracking  │
        │  - Weekly Reports    │
        └──────────────────────┘
```

## Core Components

### 1. Input Layer
**Responsibility**: Receive invoice data from multiple sources

- **Google Drive Trigger**: Monitors specified folder for new files
  - Polls every 1 minute
  - Automatic file detection
  - Download and forwarding

- **Webhook Endpoint**: HTTP POST interface
  - Direct invoice submission
  - Real-time processing
  - Immediate response

### 2. Validation Layer
**Responsibility**: Ensure data quality and format compliance

- **File Type Validation**: 
  - PDF format verification
  - MIME type checking
  - Rejection of unsupported formats

- **PDF Extraction**:
  - Text content extraction
  - Encoding handling
  - Error recovery

### 3. Intelligence Layer
**Responsibility**: Extract structured data using AI

- **GPT-4 Integration**:
  - Natural language processing
  - Invoice data extraction
  - Field mapping and structuring
  - JSON output generation

- **Custom Prompts**:
  - Specialized invoice processing
  - Multi-language support potential
  - Custom field extraction rules

### 4. Quality Control Layer
**Responsibility**: Validate and score extracted data

- **Data Validation**:
  - Required field checks
  - Data type validation
  - Consistency verification
  - Format compliance

- **Confidence Scoring**:
  - Extraction accuracy assessment
  - Field-level confidence
  - Overall invoice confidence
  - Threshold-based routing

### 5. Decision & Routing Layer
**Responsibility**: Determine processing path

- **Conditional Logic**:
  - Confidence score evaluation
  - Amount thresholds
  - Vendor approval rules
  - Custom business rules

- **Dual-Path Routing**:
  - Auto-approval path
  - Manual review path

### 6. Storage Layer
**Responsibility**: Persist processed data

- **Google Sheets Integration**:
  - Approved Invoices sheet
  - Pending Review sheet
  - Processing Log sheet
  - Real-time updates

- **Data Structure**:
  - 20+ structured fields
  - Indexed by invoice number
  - Audit trail maintained

### 7. Communication Layer
**Responsibility**: Notify stakeholders

- **Email Notifications**:
  - Approval confirmations
  - Review alerts
  - Weekly reports
  - HTML templates

- **Gmail Integration**:
  - OAuth2 authentication
  - Formatted messages
  - Attachment support

### 8. Analytics Layer
**Responsibility**: Generate insights and reports

- **Weekly Triggers**:
  - Scheduled execution
  - Consistent timing
  - Automated distribution

- **Metrics Collection**:
  - Processing volumes
  - Approval rates
  - Error frequencies
  - Performance trends

## Data Flow

### Synchronous Path (Webhook)
```
Webhook Request
    ↓
File Validation
    ↓
PDF Extraction
    ↓
GPT-4 Processing
    ↓
Validation & Scoring
    ↓
Routing Decision
    ↓
Sheet Update
    ↓
Email Notification
    ↓
Webhook Response
```

**Response Time**: 10-30 seconds per invoice

### Asynchronous Path (Google Drive)
```
File Detection
    ↓
Download
    ↓
Processing Pipeline
    ↓ (same as above)
    ↓
Sheet Update
    ↓
Email Notification
```

**Polling Interval**: Every 1 minute

### Scheduled Path (Weekly Analytics)
```
Weekly Trigger (Monday 8 AM)
    ↓
Read Processing Log
    ↓
Aggregate Metrics
    ↓
Generate Report
    ↓
Send Email
```

## Integration Points

### External Services

1. **Google Drive**
   - Purpose: File storage and monitoring
   - Authentication: OAuth2
   - Operations: Monitor, Download

2. **Google Sheets**
   - Purpose: Data persistence and organization
   - Authentication: OAuth2
   - Operations: Create, Read, Update

3. **Gmail**
   - Purpose: Email notifications
   - Authentication: OAuth2
   - Operations: Send formatted emails

4. **OpenAI API**
   - Purpose: Intelligent data extraction
   - Authentication: API Key
   - Model: GPT-4 (gpt-4-mini)
   - Operations: Text analysis and extraction

### Internal n8n Services

1. **Webhooks**: HTTP endpoints for direct submission
2. **Scheduling**: Cron-based trigger system
3. **Logging**: Execution logs and debugging
4. **Error Handling**: Graceful failure management

## Security Architecture

### Authentication
- **OAuth2 Flows**: Google services
- **API Keys**: OpenAI secure storage
- **Credentials Management**: n8n credential vault

### Data Protection
- **Encryption in Transit**: HTTPS/TLS
- **Encryption at Rest**: Google Drive encryption
- **Access Control**: OAuth2 scopes
- **Audit Logging**: Complete transaction history

### Compliance
- **Data Privacy**: Secure credential storage
- **Audit Trail**: All transactions logged
- **Error Logging**: Secure error handling
- **Retention**: Configurable data retention

## Scalability Considerations

### Current Capacity
- **Single Workflow**: Handles 100+ invoices/day
- **Concurrent Processing**: Limited by n8n instance
- **API Rate Limits**: OpenAI and Google quotas

### Scaling Strategies
1. **Workflow Optimization**: Parallel processing
2. **Batch Processing**: Group invoice handling
3. **Queue Management**: Process prioritization
4. **Resource Allocation**: n8n instance sizing

## Reliability & Availability

### Error Handling
- Invalid PDF: User notification + rejection
- Extraction failure: Manual review queue
- API timeouts: Retry logic
- Network errors: Graceful degradation

### Monitoring
- Execution logs review
- Email notification tracking
- Sheet update verification
- Weekly report generation

### Backup & Recovery
- Google Sheets automatic backup
- Processing log maintained
- Webhook response confirmation
- Audit trail for recovery

## Future Enhancements

1. **Machine Learning**: Custom models for specific invoice types
2. **Advanced Analytics**: Dashboards and real-time metrics
3. **ERP Integration**: Direct posting to accounting systems
4. **Document Intelligence**: Multi-page document handling
5. **OCR Enhancement**: Improved text extraction
6. **Mobile Notifications**: Push notifications for alerts
7. **API-First Design**: External system integration
8. **Blockchain**: Immutable audit trail

## Performance Metrics

### Processing Performance
- Average extraction time: 5-8 seconds
- Validation time: 2-3 seconds
- Sheet update time: 1-2 seconds
- Total per-invoice: 10-30 seconds

### System Reliability
- Success rate: 95%+
- Error handling: Automatic
- Recovery time: < 1 minute
- Uptime: 99%+

## Cost Considerations

### API Costs
- **OpenAI GPT-4**: Per-token pricing
- **Google APIs**: Free tier available
- **n8n**: Self-hosted or cloud options

### Optimization
- Batch processing for bulk invoices
- Selective GPT-4 usage
- Caching common extractions
- Efficient sheet operations
