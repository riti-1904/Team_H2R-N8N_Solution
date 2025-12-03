# Credentials Setup Guide for Invoice Automation

## Overview
This project requires 3 main credentials:
1. Gmail (Email trigger + notifications)
2. Google Sheets (Read POs, write results)
3. OpenAI (AI validation)

---

## STEP 1: Gmail Setup (Email Trigger & Notifications)

### Option A: Gmail OAuth2 (Recommended)

1. **Create Gmail App Password**
   ```
   Go to: https://myaccount.google.com/security
   → 2-Step Verification (enable if not already)
   → App Passwords
   → Select "Mail" and "Windows Computer"
   → Generate 16-character password
   → Copy password (example: abcd efgh ijkl mnop)
   ```

2. **In n8n:**
   ```
   Workflows → Credentials → Gmail
   → Click "Authenticate with Gmail"
   → Follow OAuth flow
   → Grant permissions
   → Save
   ```

3. **Configure Workflow:**
   - Gmail Trigger node → Select your Gmail account
   - Email nodes → Use same Gmail credential

### Option B: Gmail SMTP (Alternative)

1. **Enable Less Secure App Access**
   ```
   https://myaccount.google.com/lesssecureapps
   → Turn ON (if not using 2FA)
   ```

2. **Get App Password** (if using 2FA)
   ```
   Same steps as Option A
   ```

3. **In n8n:**
   ```
   Credentials → Email (SMTP)
   Host: smtp.gmail.com
   Port: 587
   Email: your-email@gmail.com
   Password: [16-char app password]
   ```

---

## STEP 2: Google Sheets Setup (Read & Write)

1. **Enable Google Sheets API**
   ```
   Go to: https://console.cloud.google.com
   → New Project
   → Search "Google Sheets API"
   → Enable
   ```


2. **Create OAuth Credentials**
   ```
   → Credentials (left sidebar)
   → Create Credentials → OAuth 2.0 Client ID
   → Application type: Desktop
   → Create
   → Download JSON
   ```

3. **In n8n:**
   ```
   Credentials → Google Sheets
   → Click "Authenticate with Google"
   → Login with your Google account
   → Grant Sheets & Drive access
   → Save
   ```

4. **Share Google Sheet**
   ```
   Create sheet: "Invoice_Processing_Data"
   → Share with: n8n service account email
   → Grant Editor access
   ```

5. **Create Tabs & Columns**
   ```
   Tab 1: PurchaseOrders
   Columns: po_number, vendor_name, amount, line_item_description, 
            expected_delivery_date, created_date, status
   
   Tab 2: ProcessedInvoices
   Columns: invoice_id, invoice_number, order_number, vendor_name, 
            vendor_email, amount, tax, sub_total, invoice_date, due_date, 
            customer_name, validation_status, risk_score, approval_status, 
            next_action, processed_date
   ```

---

## STEP 3: OpenAI API Setup (AI Validation)

1. **Get API Key**
   ```
   Go to: https://platform.openai.com/api-keys
   → Login/Signup
   → Create new secret key
   → Copy immediately (won't be shown again)
   ```

2. **Set Usage Limits** (IMPORTANT - Prevent surprises)
   ```
   https://platform.openai.com/account/billing/limits
   → Hard limit: $10-50/month
   → Soft limit: $5/month (alert)
   ```

3. **In n8n:**
   ```
   Credentials → OpenAI
   API Key: [Paste your sk-... key]
   → Save
   ```

4. **Choose Model**
   ```
   In workflow: OpenAI node
   Model: gpt-4-turbo (best quality)
      OR gpt-3.5-turbo (cheaper)
   Temperature: 0.3 (consistent results)
   Max Tokens: 2000
   ```

---

## TESTING CREDENTIALS

### Test Gmail
```
1. Create test email: invoice_test@gmail.com
2. Send test message to your n8n mailbox
3. Check n8n execution log
4. Should see email in "Recent Emails"
```

### Test Google Sheets
```
1. Add test row to PurchaseOrders sheet
2. Run workflow with "Get rows" node
3. Should see data in output
```

### Test OpenAI
```
1. Run workflow with OpenAI node
2. Check execution logs
3. Should see AI response
4. Check API usage: https://platform.openai.com/usage/overview
```

---

## SECURITY BEST PRACTICES

✅ **DO:**
- Store API keys in n8n Credentials (encrypted)
- Use environment variables for sensitive data
- Rotate keys every 3-6 months
- Set usage limits on all APIs
- Use OAuth2 instead of passwords
- Monitor API usage regularly

❌ **DON'T:**
- Commit credentials to Git
- Share API keys via email/chat
- Use same key for multiple projects
- Leave API keys in code comments
- Store credentials in plain text files

---

## TROUBLESHOOTING

### Gmail Issues
```
Problem: "Failed to authenticate"
Solution: 
- Enable 2FA
- Generate new App Password
- Clear n8n cache
- Re-authenticate
```

### Google Sheets Issues
```
Problem: "Sheet not found"
Solution:
- Verify sheet name matches exactly
- Share sheet with n8n service account
- Check permissions (Editor access needed)
```

### OpenAI Issues
```
Problem: "Invalid API key"
Solution:
- Verify key starts with "sk-"
- Check if key is still active
- Regenerate key if needed
- Check account balance
```

---
## CREDENTIAL ROTATION

### Monthly Checklist
- [ ] Review API usage & costs
- [ ] Check for unauthorized access
- [ ] Update API keys if needed
- [ ] Verify all services connected

### Quarterly Checklist
- [ ] Rotate API keys
- [ ] Review and update permissions
- [ ] Audit access logs
- [ ] Update documentation

---

## SUPPORT
For credential issues:
1. Check n8n logs: Workflows → Executions
2. Verify API service status
3. Test credentials individually
4. Contact support if stuck


## **5. SUMMARY TABLE - ALL CREDENTIALS**

**File:** `credentials_summary.md`


# Credentials Summary

| Service | Type | Required | Status | Location |
|---------|------|----------|--------|----------|
| Gmail | OAuth2 | Yes | ✅ Active | n8n Credentials |
| Google Sheets | OAuth2 | Yes | ✅ Active | n8n Credentials |
| OpenAI | API Key | Yes | ✅ Active | n8n Credentials |

## Credentials Used in Workflow

### Gmail Trigger Node

Credential: Gmail
Action: Watch for emails
Folder: INBOX


### Get POs Node

Credential: Google Sheets
Operation: Read rows
Spreadsheet: Invoice_Processing_Data
Sheet: PurchaseOrders


### Store Results Node

Credential: Google Sheets
Operation: Append rows
Spreadsheet: Invoice_Processing_Data
Sheet: ProcessedInvoices


### OpenAI Validation Node

Credential: OpenAI
Model: gpt-4-turbo
Temperature: 0.3
Max Tokens: 2000


### Email Notification Nodes

Credential: Gmail
Send to: manager@company.com (REQUIRES_REVIEW)
Send to: vendor@company.com (AUTO_APPROVED)
Send to: escalation@company.com (REJECTED)

---

## **FINAL CREDENTIALS PACKAGE**

Create a folder: `credentials_templates/`

**Contents:**
```
credentials_templates/
├── gmail_credentials_template.json
├── google_sheets_credentials_template.json
├── openai_credentials_template.json
├── email_service_credentials_template.json
├── CREDENTIALS_SETUP_GUIDE.md
├── credentials_summary.md
└── README.md

```


## **README for Credentials Folder**

**File:** `credentials_templates/README.md`


# Credentials Templates

This folder contains templates for all credentials needed by the Invoice Automation project.

## What's Inside

1. **gmail_credentials_template.json**
   - Email trigger configuration
   - OAuth2 setup instructions

2. **google_sheets_credentials_template.json**
   - Google Sheets API configuration
   - Spreadsheet structure
   - Read/Write operations

3. **openai_credentials_template.json**
   - OpenAI API key setup
   - Model selection
   - Cost estimation

4. **email_service_credentials_template.json**
   - Email notification configuration
   - SMTP alternative

5. **CREDENTIALS_SETUP_GUIDE.md**
   - Step-by-step setup for each service
   - Testing procedures
   - Troubleshooting

6. **credentials_summary.md**
   - Quick reference table
   - Credential usage in workflow

## Quick Start

1. Read `CREDENTIALS_SETUP_GUIDE.md` first
2. Get credentials for each service:
   - Gmail (OAuth2)
   - Google Sheets (OAuth2)
   - OpenAI (API Key)
3. Enter credentials in n8n
4. Test each credential
5. Run workflow

## Security Notes

⚠️ **NEVER commit actual credentials to Git!**

These are TEMPLATES only. Replace placeholders with:
- YOUR_CLIENT_ID
- YOUR_CLIENT_SECRET
- YOUR_API_KEY

Keep actual credentials:
- In n8n encrypted storage
- In environment variables
- In secure credential manager

## Support

For issues, check:
1. Credentials are correct in n8n
2. APIs are enabled in service accounts
3. Permissions are granted
4. Usage limits aren't exceeded

Need help? See CREDENTIALS_SETUP_GUIDE.md


