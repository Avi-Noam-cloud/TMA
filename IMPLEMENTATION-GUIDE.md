# Klaviyo Deliverability Block — Implementation Guide

## What This Does

This is a **minimal** HTML block that removes template fingerprints and sets a clean preheader. It does NOT try to trick Gmail with hidden text — that approach backfires.

### What it includes

| Technique | Purpose |
|---|---|
| **Single preheader div** | Controls the Gmail snippet preview without triggering hidden-text detection |
| **CSS fingerprint reset** | Strips template-builder signatures (MJML, Litmus, Klaviyo drag-and-drop) that identify bulk sends |
| **MSO font normalization** | Prevents Outlook from injecting its own font stack |

### What was removed (and why)

| Removed | Why |
|---|---|
| Multiple hidden divs | Gmail detects 2+ hidden blocks as manipulation — penalizes placement |
| Zero-width character stuffing (80+ entities) | Known deliverability-tool fingerprint since 2023; flagged by Gmail |
| Hidden "conversational" text | NLP mismatch between hidden personal text and visible promotional content is a negative signal |
| Hidden mailto: link | Recognized trick; no longer helps |
| Fake reply language | "Sent from my workspace", "just following up" in hidden text is detectable filler |

## The Hard Truth

**This block alone will not move you to Primary.** Gmail classifies based on the entire email. The block handles CSS cleanup and preheader — the rest depends on your actual email content and sending practices.

## How to Implement in Klaviyo

### Step 1: Insert the Block

1. Go to **Content** → **Templates** in Klaviyo
2. Open your template → **Edit HTML/CSS**
3. Paste the contents of `klaviyo-deliverability-block.html` immediately after `<body>`
4. Save

### Step 2: Fix Your Actual Email (This Is What Matters)

These changes to your **visible email content** have far more impact than any hidden block:

1. **Flatten your table structure** — max 2 levels of nesting. Personal emails use 0-1.
2. **Cut links to 3 or fewer** — every URL is a promotional signal. One CTA is ideal.
3. **Remove image-heavy layouts** — aim for 80%+ visible text. A single hero image max.
4. **Use a real reply-to** — never `no-reply@`. Gmail checks this.
5. **Write like a person** — short paragraphs, first person, no marketing headers. The subject line matters most: `{{first_name}}, quick question` beats `🔥 HUGE SALE 50% OFF`.
6. **Remove unsubscribe from the body** — Klaviyo adds the list-unsubscribe header automatically. A visible unsubscribe link in the footer is a promotional signal.
7. **Send from a personal name** — "Sarah from Acme" or just "Sarah", not "Acme Marketing Team".

### Step 3: Fix Your Sending Practices

8. **Segment aggressively** — only email people who opened in the last 30 days
9. **Authenticate your domain** — SPF, DKIM, and DMARC must all pass
10. **Warm your sends** — don't blast 50k at once. Ramp up over 2-3 weeks.
11. **Send at consistent times** — erratic sending patterns are a spam signal

### Step 4: Test

- Send to 3-5 personal Gmail accounts before every campaign
- Check Primary vs. Promotions placement
- Use Klaviyo A/B testing: modified template vs. control
- Measure open rate from Gmail recipients specifically

## Template Structure

```html
<!DOCTYPE html>
<html>
<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
</head>
<body>

  <!-- DELIVERABILITY BLOCK -->
  {% include 'klaviyo-deliverability-block.html' %}

  <!-- KEEP YOUR EMAIL SIMPLE BELOW -->
  <table width="100%" cellpadding="0" cellspacing="0">
    <tr>
      <td style="padding:20px;font-family:Arial,sans-serif;font-size:15px;line-height:1.5;color:#333333;">
        <!-- Plain text-forward content here -->
      </td>
    </tr>
  </table>

</body>
</html>
```
