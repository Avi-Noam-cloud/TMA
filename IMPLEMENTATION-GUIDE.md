# Klaviyo Deliverability Block — Implementation Guide

## What This Does

This HTML block reduces the promotional-threshold signals that cause Gmail, Outlook, and Yahoo to route your Klaviyo emails to the **Promotions** tab instead of the **Primary** inbox.

It works by applying the same core techniques as Mailmend:

| Technique | What It Targets |
|---|---|
| **Structural flattening** | Reduces nested table depth (a key broadcast signal) to 1 level |
| **Text-weight balancing** | Increases plain-text-to-HTML ratio to mimic personal correspondence |
| **Preheader displacement** | Uses zero-width characters to prevent promotional boilerplate from leaking into Gmail's snippet preview |
| **Semantic anchors** | Inserts `mailto:` links and reply-oriented language that score as "personal" in classification |
| **Conversation simulation** | Adds first-person, question/response language patterns that Gmail's classifier associates with one-to-one mail |
| **CSS fingerprint reset** | Strips template-builder CSS signatures (MJML, Litmus, Klaviyo drag-and-drop) that identify bulk sends |
| **Dynamic personalization** | Uses Klaviyo merge tags (`{{first_name}}`, `{{email}}`, `{{organization.name}}`) as additional personalization signals |

## How to Implement in Klaviyo

### Step 1: Open Your Template

1. Go to **Content** → **Templates** in Klaviyo
2. Open the template you want to optimize
3. Click **Edit HTML/CSS** (source code view)

### Step 2: Insert the Block

1. Copy the entire contents of `klaviyo-deliverability-block.html`
2. Paste it **immediately after** the opening `<body>` tag
3. Your main email content should come **after** the deliverability block
4. Save the template

### Step 3: Validate with A/B Testing

1. Create a campaign using the modified template
2. Use Klaviyo's **A/B test** feature:
   - **Variant A**: Template WITH the deliverability block
   - **Variant B**: Original template WITHOUT the block (control)
3. Split your audience 50/50
4. Measure **open rate** and **click rate** — Primary inbox placement will show as higher opens from Gmail recipients

### Step 4: Monitor & Iterate

- Check placement weekly using Gmail seed accounts
- If Gmail updates its filtering (watch for announcements about Gemini-enhanced filtering), the block may need recalibration
- Re-test after any major template redesign

## Template Structure Example

```html
<!DOCTYPE html>
<html>
<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
</head>
<body>

  <!-- PASTE DELIVERABILITY BLOCK HERE -->
  {% include 'klaviyo-deliverability-block.html' %}

  <!-- YOUR NORMAL EMAIL CONTENT BELOW -->
  <table width="100%" cellpadding="0" cellspacing="0">
    <tr>
      <td>
        <!-- Email body -->
      </td>
    </tr>
  </table>

</body>
</html>
```

## Important Notes

- **Do NOT nest** this block inside other `<table>` elements — it must sit at the top level of `<body>`
- The hidden text is **invisible** to recipients but **visible** to MBP parsers
- The `&#847;` entities are zero-width non-breaking spaces used as padding — do not remove them
- Klaviyo merge tags (`{{first_name}}`, `{{email}}`, etc.) will render dynamically per recipient, adding real personalization signals
- This block adds ~3KB to your email — well within acceptable size limits

## Additional Best Practices for Primary Inbox Placement

Beyond the HTML block, follow these practices to maximize deliverability:

1. **Keep your main template simple** — avoid more than 2 levels of nested tables
2. **Maintain a high text-to-image ratio** — aim for at least 60% text
3. **Limit the number of links** — fewer than 5 unique URLs per email
4. **Use a real reply-to address** — not a no-reply@ address
5. **Personalize subject lines** — include `{{first_name}}` or other dynamic content
6. **Clean your list regularly** — remove unengaged subscribers (no opens in 90+ days)
7. **Avoid spam trigger words** in subject lines — "free", "act now", "limited time"
8. **Authenticate your domain** — ensure SPF, DKIM, and DMARC are properly configured in Klaviyo
