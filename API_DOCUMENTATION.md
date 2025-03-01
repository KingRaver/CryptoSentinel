# CryptoSentinel API Documentation

## Introduction

The CryptoSentinel API provides programmatic access to our institutional-grade crypto market intelligence platform. This RESTful API enables developers, analysts, and organizations to integrate our advanced analytics capabilities into their own applications, trading systems, and research platforms.

**API Version:** v1  
**Base URL:** `https://api.cryptosentinel.ai/v1`  
**Release Status:** Production  

## Authentication

### API Keys

All requests to the CryptoSentinel API require authentication using API keys. Each API key is associated with a specific subscription plan that determines rate limits and feature access.

#### Key Types

| Key Type | Description | Use Case |
|----------|-------------|----------|
| Public Key | Used for user identification | Include in request headers |
| Secret Key | Used for request signing | Used to generate signatures |

#### Authentication Headers

```
X-CS-API-Key: your_public_key
X-CS-Timestamp: current_unix_timestamp
X-CS-Signature: generated_hmac_signature
```

#### Signature Generation

Signatures are generated using HMAC-SHA256:

```python
import hmac
import hashlib
import time

def generate_signature(secret_key, timestamp, request_body=''):
    message = f"{timestamp}{request_body}"
    signature = hmac.new(
        secret_key.encode(),
        message.encode(),
        hashlib.sha256
    ).hexdigest()
    return signature

# Example usage
public_key = "pk_live_12345abcdef"
secret_key = "sk_live_67890ghijkl"
timestamp = str(int(time.time()))
request_body = '{"token":"BTC","timeframe":"4h"}'

signature = generate_signature(secret_key, timestamp, request_body)
```

### JWT Authentication (Enterprise Only)

Enterprise customers can utilize JWT-based authentication for enhanced security and user-specific permissions:

```
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
```

## API Endpoints

### Market Data

#### Get Token Market Data

```
GET /market/data/{token}
```

Retrieves current market data for a specific token.

**Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| token | string | Yes | Token symbol (e.g., BTC, ETH) |

**Response:**

```json
{
  "token": "BTC",
  "price": 57342.18,
  "volume_24h": 38547921345.67,
  "change_24h": 2.37,
  "market_cap": 1123487659034.56,
  "last_updated": "2025-03-01T12:34:56Z",
  "high_24h": 58123.45,
  "low_24h": 56789.12
}
```

#### Get Multiple Token Data

```
GET /market/data
```

Retrieves market data for multiple tokens in a single request.

**Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| tokens | string | Yes | Comma-separated list of token symbols |

**Response:**

```json
{
  "tokens": [
    {
      "token": "BTC",
      "price": 57342.18,
      "volume_24h": 38547921345.67,
      "change_24h": 2.37
    },
    {
      "token": "ETH",
      "price": 3421.79,
      "volume_24h": 17283945678.12,
      "change_24h": 1.89
    }
  ],
  "last_updated": "2025-03-01T12:34:56Z"
}
```

### Smart Money Analysis

#### Get Smart Money Indicators

```
GET /analysis/smart-money/{token}
```

Returns comprehensive smart money indicators for a specific token.

**Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| token | string | Yes | Token symbol |
| timeframe | string | No | Analysis timeframe (1h, 4h, 24h) - Default: 24h |

**Response:**

```json
{
  "token": "ETH",
  "timeframe": "24h",
  "analysis_time": "2025-03-01T12:34:56Z",
  "smart_money_indicators": {
    "volume_z_score": 2.73,
    "price_volume_divergence": true,
    "stealth_accumulation": {
      "detected": true,
      "confidence": 0.87,
      "pattern_strength": "high"
    },
    "abnormal_volume": true,
    "volume_vs_hourly_avg": 1.43,
    "volume_vs_daily_avg": 1.87,
    "unusual_trading_hours": ["hour_3", "hour_7"],
    "volume_cluster_detected": true
  },
  "interpretation": {
    "summary": "Strong institutional accumulation detected",
    "confidence": 0.91,
    "action_recommendation": "potential_entry",
    "historical_similarity": 0.86
  }
}
```

#### Get Volume Analysis

```
GET /analysis/volume/{token}
```

Provides detailed volume pattern analysis.

**Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| token | string | Yes | Token symbol |
| lookback | integer | No | Hours to look back - Default: 24 |

**Response:**

```json
{
  "token": "SOL",
  "analysis_time": "2025-03-01T12:34:56Z",
  "volume_analysis": {
    "current_volume": 2734567890.12,
    "avg_volume": 1823456789.34,
    "volume_trend": "significant_increase",
    "volume_change_pct": 49.97,
    "volume_patterns": [
      {
        "pattern": "accumulation",
        "confidence": 0.89,
        "detected_at": "2025-03-01T08:15:23Z"
      },
      {
        "pattern": "breakout_preceding",
        "confidence": 0.76,
        "detected_at": "2025-03-01T10:45:12Z"
      }
    ],
    "volume_anomalies": [
      {
        "timestamp": "2025-03-01T09:30:00Z",
        "z_score": 2.87,
        "significance": "high"
      }
    ]
  },
  "historical_context": {
    "similar_patterns": 7,
    "avg_outcome": "+8.3% (24h)",
    "confidence": 0.84
  }
}
```

### Correlation Analysis

#### Get Token Correlations

```
GET /analysis/correlations/{token}
```

Returns correlation data between the specified token and other major tokens.

**Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| token | string | Yes | Token symbol |
| timeframe | string | No | Correlation timeframe (24h, 7d, 30d) - Default: 7d |
| limit | integer | No | Number of correlations to return - Default: 10 |

**Response:**

```json
{
  "base_token": "BTC",
  "timeframe": "7d",
  "analysis_time": "2025-03-01T12:34:56Z",
  "correlations": [
    {
      "token": "ETH",
      "price_correlation": 0.87,
      "volume_correlation": 0.73,
      "direction": "same",
      "strength": "strong"
    },
    {
      "token": "SOL",
      "price_correlation": 0.81,
      "volume_correlation": 0.68,
      "direction": "same",
      "strength": "strong"
    },
    {
      "token": "AVAX",
      "price_correlation": -0.34,
      "volume_correlation": 0.21,
      "direction": "opposite",
      "strength": "moderate"
    }
  ],
  "correlation_shifts": [
    {
      "token_pair": "BTC/SOL",
      "previous": 0.92,
      "current": 0.81,
      "shift": -0.11,
      "significance": "moderate"
    }
  ]
}
```

#### Get Market Correlation Matrix

```
GET /analysis/correlations/matrix
```

Returns a complete correlation matrix for specified tokens.

**Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| tokens | string | Yes | Comma-separated list of token symbols |
| timeframe | string | No | Correlation timeframe (24h, 7d, 30d) - Default: 7d |

**Response:**

```json
{
  "timeframe": "7d",
  "analysis_time": "2025-03-01T12:34:56Z",
  "matrix": {
    "BTC": {
      "BTC": 1.0,
      "ETH": 0.87,
      "SOL": 0.81
    },
    "ETH": {
      "BTC": 0.87,
      "ETH": 1.0,
      "SOL": 0.79
    },
    "SOL": {
      "BTC": 0.81,
      "ETH": 0.79,
      "SOL": 1.0
    }
  },
  "notable_relationships": [
    {
      "token_pair": "BTC/ETH",
      "relationship": "strong_positive",
      "significance": "high"
    }
  ]
}
```

### Market Intelligence

#### Get Token Analysis

```
GET /intelligence/analysis/{token}
```

Provides comprehensive market analysis for a specific token.

**Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| token | string | Yes | Token symbol |
| format | string | No | Response format (json, text) - Default: json |

**Response:**

```json
{
  "token": "AVAX",
  "analysis_time": "2025-03-01T12:34:56Z",
  "market_data": {
    "price": 38.72,
    "change_24h": 3.87,
    "volume_24h": 1872345678.90
  },
  "technical_analysis": {
    "trend": "bullish",
    "strength": "moderate",
    "support_levels": [36.45, 34.78],
    "resistance_levels": [40.12, 42.87]
  },
  "volume_analysis": {
    "pattern": "accumulation",
    "z_score": 1.87,
    "significance": "moderate"
  },
  "smart_money_indicators": {
    "detected": true,
    "confidence": 0.83,
    "activity": "stealth_accumulation"
  },
  "market_context": {
    "sector_performance": "+2.7%",
    "relative_strength": "outperforming",
    "correlation_summary": "decoupling_from_btc"
  },
  "narrative": "AVAX showing signs of institutional accumulation with volume patterns suggesting a potential breakout. Smart money indicators point to stealth buying while price consolidates. The token is outperforming its sector with decreased correlation to BTC over the past 24 hours."
}
```

#### Get Alerts

```
GET /intelligence/alerts
```

Retrieves active market alerts based on configured conditions.

**Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| tokens | string | No | Comma-separated list of token symbols to filter |
| types | string | No | Alert types (smart_money, volume, correlation, price) |
| significance | string | No | Alert significance (all, high, critical) - Default: all |

**Response:**

```json
{
  "alerts": [
    {
      "id": "alert_12345",
      "token": "SOL",
      "type": "smart_money",
      "detected_at": "2025-03-01T10:15:23Z",
      "significance": "high",
      "description": "Institutional accumulation detected with 2.7σ volume anomaly",
      "confidence": 0.91,
      "supporting_data": {
        "volume_z_score": 2.73,
        "price_action": "consolidating",
        "similar_historical_events": 7
      }
    },
    {
      "id": "alert_12346",
      "token": "NEAR",
      "type": "correlation",
      "detected_at": "2025-03-01T11:32:47Z",
      "significance": "medium",
      "description": "Decreasing correlation with layer-1 tokens",
      "confidence": 0.84,
      "supporting_data": {
        "correlation_shift": -0.17,
        "affected_relationships": ["NEAR/SOL", "NEAR/AVAX"],
        "potential_cause": "sector_rotation"
      }
    }
  ],
  "total_count": 2,
  "analysis_time": "2025-03-01T12:34:56Z"
}
```

### Historical Data

#### Get Historical Patterns

```
GET /historical/patterns/{token}
```

Retrieves historical patterns for a specific token.

**Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| token | string | Yes | Token symbol |
| pattern_type | string | No | Pattern type (smart_money, volume, correlation) |
| limit | integer | No | Number of patterns to return - Default: 10 |

**Response:**

```json
{
  "token": "BTC",
  "pattern_type": "smart_money",
  "patterns": [
    {
      "id": "pattern_789012",
      "detected_at": "2025-02-15T08:34:21Z",
      "pattern": "accumulation",
      "confidence": 0.87,
      "duration_hours": 8.5,
      "peak_z_score": 2.43,
      "outcome": {
        "price_change_24h": 7.82,
        "price_change_72h": 12.47,
        "followed_pattern": true
      }
    },
    {
      "id": "pattern_789013",
      "detected_at": "2025-01-27T14:12:38Z",
      "pattern": "distribution",
      "confidence": 0.92,
      "duration_hours": 12.3,
      "peak_z_score": 2.87,
      "outcome": {
        "price_change_24h": -5.34,
        "price_change_72h": -8.91,
        "followed_pattern": true
      }
    }
  ],
  "total_patterns_found": 27
}
```

#### Get Historical Correlation

```
GET /historical/correlation
```

Retrieves historical correlation data between tokens.

**Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| token_pair | string | Yes | Token pair (e.g., BTC/ETH) |
| timeframe | string | No | Time period (7d, 30d, 90d, 180d, 1y) - Default: 30d |
| resolution | string | No | Data point resolution (1h, 4h, 1d) - Default: 1d |

**Response:**

```json
{
  "token_pair": "BTC/ETH",
  "timeframe": "30d",
  "resolution": "1d",
  "correlation_data": [
    {
      "timestamp": "2025-02-01T00:00:00Z",
      "correlation": 0.87,
      "btc_price": 54321.78,
      "eth_price": 3210.45
    },
    {
      "timestamp": "2025-02-02T00:00:00Z",
      "correlation": 0.89,
      "btc_price": 55432.19,
      "eth_price": 3287.65
    }
  ],
  "summary": {
    "avg_correlation": 0.83,
    "max_correlation": 0.91,
    "min_correlation": 0.72,
    "trend": "increasing"
  }
}
```

### User Management

#### Get User Configuration

```
GET /user/configuration
```

Retrieves the current user's configuration settings.

**Response:**

```json
{
  "user_id": "user_123456",
  "subscription": {
    "plan": "analyst",
    "features": ["smart_money_alerts", "api_access", "historical_data"],
    "rate_limits": {
      "requests_per_minute": 60,
      "requests_per_day": 5000
    }
  },
  "alert_configuration": {
    "enabled_tokens": ["BTC", "ETH", "SOL", "AVAX", "NEAR"],
    "alert_types": ["smart_money", "volume", "correlation"],
    "notification_channels": ["api", "email", "mobile"]
  },
  "api_settings": {
    "public_key": "pk_live_12345abcdef",
    "rate_limit_remaining": 4823,
    "rate_limit_reset": "2025-03-02T00:00:00Z"
  }
}
```

#### Update Alert Configuration

```
PUT /user/configuration/alerts
```

Updates the user's alert configuration.

**Request Body:**

```json
{
  "enabled_tokens": ["BTC", "ETH", "SOL", "AVAX", "NEAR", "BNB"],
  "alert_types": ["smart_money", "volume"],
  "notification_channels": ["api", "email"]
}
```

**Response:**

```json
{
  "status": "success",
  "message": "Alert configuration updated successfully",
  "updated_configuration": {
    "enabled_tokens": ["BTC", "ETH", "SOL", "AVAX", "NEAR", "BNB"],
    "alert_types": ["smart_money", "volume"],
    "notification_channels": ["api", "email"]
  }
}
```

### Webhook Management (Enterprise Only)

#### Create Webhook

```
POST /webhooks
```

Creates a new webhook for real-time event notifications.

**Request Body:**

```json
{
  "url": "https://example.com/crypto-webhooks",
  "events": ["smart_money_alert", "correlation_shift", "volume_anomaly"],
  "tokens": ["BTC", "ETH", "SOL"],
  "secret": "whsec_12345abcdefg"
}
```

**Response:**

```json
{
  "id": "webhook_123456",
  "url": "https://example.com/crypto-webhooks",
  "events": ["smart_money_alert", "correlation_shift", "volume_anomaly"],
  "tokens": ["BTC", "ETH", "SOL"],
  "status": "active",
  "created_at": "2025-03-01T12:34:56Z"
}
```

#### List Webhooks

```
GET /webhooks
```

Retrieves all webhooks for the current user.

**Response:**

```json
{
  "webhooks": [
    {
      "id": "webhook_123456",
      "url": "https://example.com/crypto-webhooks",
      "events": ["smart_money_alert", "correlation_shift", "volume_anomaly"],
      "tokens": ["BTC", "ETH", "SOL"],
      "status": "active",
      "created_at": "2025-03-01T12:34:56Z"
    }
  ]
}
```

## Rate Limits

Rate limits vary by subscription tier and are enforced on a per-minute and per-day basis:

| Plan | Requests/Minute | Requests/Day | Burst Capacity |
|------|----------------|--------------|----------------|
| Free | 10 | 100 | 15 |
| Trader | 30 | 1,000 | 45 |
| Analyst | 60 | 5,000 | 90 |
| Professional | 120 | 12,000 | 180 |
| Enterprise | Custom | Custom | Custom |

### Rate Limit Headers

All API responses include rate limit information in the headers:

```
X-RateLimit-Limit: 60
X-RateLimit-Remaining: 58
X-RateLimit-Reset: 1646168400
```

### Exceeding Rate Limits

When rate limits are exceeded, the API will return a `429 Too Many Requests` response:

```json
{
  "error": {
    "code": "rate_limit_exceeded",
    "message": "Rate limit exceeded. Please try again in 37 seconds.",
    "reset_at": 1646168400
  }
}
```

## Error Handling

The API uses standard HTTP status codes and returns detailed error information:

### Status Codes

| Code | Description |
|------|-------------|
| 200 | OK - Request succeeded |
| 400 | Bad Request - Invalid parameters |
| 401 | Unauthorized - Authentication failed |
| 403 | Forbidden - Insufficient permissions |
| 404 | Not Found - Resource doesn't exist |
| 429 | Too Many Requests - Rate limit exceeded |
| 500 | Internal Server Error - Server-side issue |

### Error Response Format

```json
{
  "error": {
    "code": "invalid_token",
    "message": "The token 'INVALID' is not supported or doesn't exist.",
    "param": "token",
    "help_url": "https://docs.cryptosentinel.ai/errors/invalid_token"
  }
}
```

## SDK Libraries

Official SDK libraries are available for popular programming languages:

### Python

```python
pip install cryptosentinel
```

**Usage Example:**

```python
from cryptosentinel import CryptoSentinel

# Initialize the client
client = CryptoSentinel(
    api_key="pk_live_12345abcdef",
    api_secret="sk_live_67890ghijkl"
)

# Get smart money analysis
analysis = client.smart_money.get_analysis(token="BTC")

# Check for signals
if analysis.has_accumulation_signal():
    signal_strength = analysis.signal_strength
    print(f"BTC accumulation detected! Signal strength: {signal_strength}")
    
    # Get supporting data
    volume_z_score = analysis.volume_z_score
    historical_context = analysis.get_historical_context()
    
    print(f"Volume Z-Score: {volume_z_score}")
    print(f"Similar historical patterns: {historical_context.similar_patterns}")
    print(f"Average outcome: {historical_context.avg_outcome}")
```

### JavaScript

```bash
npm install cryptosentinel-js
```

**Usage Example:**

```javascript
const CryptoSentinel = require('cryptosentinel-js');

// Initialize the client
const client = new CryptoSentinel({
  apiKey: 'pk_live_12345abcdef',
  apiSecret: 'sk_live_67890ghijkl'
});

// Get volume analysis
async function analyzeVolume() {
  try {
    const volumeAnalysis = await client.analysis.getVolumeAnalysis('ETH');
    
    if (volumeAnalysis.volumeTrend === 'significant_increase') {
      console.log('ETH volume surge detected!');
      console.log(`Volume change: ${volumeAnalysis.volumeChangePct}%`);
      
      // Check for patterns
      const patterns = volumeAnalysis.volumePatterns;
      patterns.forEach(pattern => {
        console.log(`Pattern: ${pattern.pattern}`);
        console.log(`Confidence: ${pattern.confidence}`);
      });
    }
  } catch (error) {
    console.error('API Error:', error.message);
  }
}

analyzeVolume();
```

## Webhook Events

For Enterprise customers, webhooks deliver real-time event notifications:

### Event Types

| Event Type | Description |
|------------|-------------|
| `smart_money_alert` | Triggered when smart money activity is detected |
| `volume_anomaly` | Triggered when abnormal volume patterns are detected |
| `correlation_shift` | Triggered when significant correlation changes occur |
| `price_movement` | Triggered for significant price movements |

### Event Payload Example

```json
{
  "event": "smart_money_alert",
  "created_at": "2025-03-01T12:34:56Z",
  "data": {
    "token": "SOL",
    "alert_type": "accumulation",
    "confidence": 0.91,
    "z_score": 2.73,
    "detected_at": "2025-03-01T12:30:22Z",
    "supporting_data": {
      "volume_increase": 47.3,
      "price_change": 1.2,
      "historical_similarity": 0.86
    }
  },
  "api_version": "v1"
}
```

### Webhook Security

Webhook payloads include a signature header for verification:

```
X-CS-Webhook-Signature: t=1646168400,v1=5257a869e7ecebeda32affa62cdca3fa51cad7e77a0e56ff536d0ce8e108d8bd
```

Verify the signature using your webhook secret:

```python
import hmac
import hashlib

def verify_webhook_signature(payload, signature_header, webhook_secret):
    # Extract timestamp and signature from header
    parts = signature_header.split(',')
    timestamp = parts[0].split('=')[1]
    signature = parts[1].split('=')[1]
    
    # Create the signed payload
    signed_payload = f"{timestamp}.{payload}"
    
    # Compute expected signature
    expected_signature = hmac.new(
        webhook_secret.encode(),
        signed_payload.encode(),
        hashlib.sha256
    ).hexdigest()
    
    # Compare signatures (constant-time comparison recommended)
    return hmac.compare_digest(signature, expected_signature)
```

## Best Practices

### Efficient Data Retrieval

- Use specific endpoints rather than general ones when possible
- Request only the data you need using filter parameters
- Implement caching for frequently accessed data
- Use webhooks for real-time updates instead of polling

### Rate Limit Management

- Implement exponential backoff for retry logic
- Use bulk endpoints to reduce request count
- Monitor rate limit headers and adjust request timing
- Consider upgrading your subscription if consistently hitting limits

### Security

- Store API keys securely (environment variables, secrets manager)
- Rotate API keys periodically
- Validate webhook signatures for all incoming webhooks
- Use HTTPS for all API calls

### Error Handling

- Implement proper error handling with appropriate retry logic
- Log API errors for debugging and monitoring
- Handle rate limit errors with timed retries
- Gracefully degrade functionality when API is unavailable

## Support

If you encounter any issues or have questions about the API:

- **Documentation:** [https://docs.cryptosentinel.ai](https://docs.cryptosentinel.ai)
- **API Status:** [https://status.cryptosentinel.ai](https://status.cryptosentinel.ai)
- **Developer Discord:** [https://discord.gg/cryptosentinel-dev](https://discord.gg/cryptosentinel-dev)
- **Email Support:** [api-support@cryptosentinel.ai](mailto:api-support@cryptosentinel.ai)

Enterprise customers have access to priority support and dedicated integration assistance.

---

© 2025 CryptoSentinel AI - [Terms of Service](https://cryptosentinel.ai/terms) | [Privacy Policy](https://cryptosentinel.ai/privacy)
