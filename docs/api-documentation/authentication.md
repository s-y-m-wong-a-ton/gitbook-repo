---
icon: lock
---

# Authentication

Authentication verifies a user's identity to ensure they're authorized to access resources. The TaskFlow API uses [API keys](https://en.wikipedia.org/wiki/API_key) to authenticate requests. You can view and manage your API keys in the TaskFlow Dashboard.

You must make all API requests over [HTTPS](http://en.wikipedia.org/wiki/HTTP_Secure). Plain [HTTP](https://en.wikipedia.org/wiki/HTTP) calls will fail. API calls without authentication will also fail.

## Getting your API key

Your API key is a unique identifier starting with `sk_live_`, for example, `sk_live_abc123def456ghi789`.

**To generate a key:**

1. Log into your dashboard at https://dashboard.taskflow.com.
2. Navigate to **Settings** > **API Keys**.
3. Click **Generate New Key**.
4. Copy your key immediately—it won't be shown again.
5. Store it securely using environment variables.

## Using your API key

Include your API key in every request using the `Authorization` header with the `Bearer` scheme:

```http
Authorization: Bearer YOUR_API_KEY
```

Replace `YOUR_API_KEY` with your actual key.

### Example request

#### **cURL**

```bash
curl -X GET "https://api.taskflow.com/api/projects" \
  -H "Authorization: Bearer YOUR_API_KEY"
```

## Security best practices

❌ **Don't:**

* Share your API key publicly (forums, chat, documentation)
* Store API keys directly in code
* Commit keys to source control (Git, SVN)
* Use the same key for development and production
* Leave compromised keys active

✅ **Do:**

* Store keys in environment variables or secret managers
* Use different keys per environment (dev, staging, prod)
* Add `*.env` to your `.gitignore` file
* Rotate keys every 90 days minimum
* Revoke keys immediately when compromised
* Revoke keys when team members leave

### Rotating keys

Rotate your API keys regularly:

* **Minimum:** Every 90 days
* **Immediately if:** Key is compromised or shared accidentally
* **When:** Team members with key access leave

### Revoking keys

If a key is compromised:

1. Go to **dashboard** > **API Keys**.
2. Click **Revoke** next to the compromised key.
3. Generate a new key.
4. Update your application with the new key.

## Authentication errors

### 401 Unauthorized

**Cause:** Invalid, missing, or revoked API key

**Example response:**

```json
{
  "error": {
    "code": "INVALID_API_KEY",
    "message": "The provided API key is invalid or has been revoked."
  }
}
```

**Solutions:**

* Verify your API key is correct (check for typos)
* Ensure you're using the correct format: `Bearer YOUR_API_KEY`
* Check if the key was revoked in the dashboard
* Confirm you're using the right environment's key (dev vs. prod)

### 403 Forbidden

**Cause:** Valid API key but insufficient permissions

**Example response:**

```json
{
  "error": {
    "code": "INSUFFICIENT_PERMISSIONS",
    "message": "Your API key doesn't have access to this resource."
  }
}
```

**Solutions:**

* Check which scopes your key has in the dashboard
* Verify you're accessing resources you own
* Upgrade your API plan if you need additional permissions
* Contact support@taskflow.com if you believe this is an error

### 429 Rate limit exceeded

**Cause:** Too many requests in a short time period

**Example response:**

```json
{
  "error": {
    "code": "RATE_LIMIT_EXCEEDED",
    "message": "Rate limit exceeded. Try again in 45 seconds."
  }
}
```

**Rate limits:**

* Standard: 100 requests/minute
* Pro: 1,000 requests/minute
* Enterprise: Custom limits

**Solution:** Wait for the time indicated in the `Retry-After` response header before making more requests.

## Code examples

### cURL

```bash
curl -X GET "https://api.taskflow.com/api/projects" \
  -H "Authorization: Bearer YOUR_API_KEY"
```

### JavaScript

```javascript
const response = await fetch('https://api.taskflow.com/api/projects', {
  headers: {
    'Authorization': 'Bearer YOUR_API_KEY'
  }
});

if (response.ok) {
  const projects = await response.json();
  console.log(projects);
} else {
  console.error(`Error: ${response.status}`);
}
```

### Python

```python
import requests

headers = {'Authorization': 'Bearer YOUR_API_KEY'}
response = requests.get('https://api.taskflow.com/api/projects', headers=headers)

if response.status_code == 200:
    projects = response.json()
    print(projects)
else:
    print(f"Error: {response.status_code}")
```

## Next steps

Now that you understand authentication:

* Make your first API call
* Explore available endpoints
* Learn about rate limiting
