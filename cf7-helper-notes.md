# 📬 Contact Form 7 (CF7) Helper Notes

## ✉️ Add CC and BCC

To add **CC** or **BCC** recipients, use this in the **"Additional Headers"** section of the Mail tab:

```text
Cc: email@example.com
Bcc: info@example.com
```

---

## 💡 Common Field Snippets

### 🎨 Custom Form Layout with Labels & Placeholder

```html
<div class="cf7_hide_labels">
  <div class="row form-row">
    <div class="col-md-12">
      <label for="firstname">First Name (Required)</label>
      [text* firstname id:firstname placeholder "First Name*"]
    </div>
  </div>

  <div class="row form-row">
    <div class="col-md-12">
      <label for="lastname">Last Name (Required)</label>
      [text* lastname id:lastname placeholder "Last Name*"]
    </div>
  </div>

  <div class="row form-row">
    <div class="col-md-12">
      <label for="emailaddress">Email (Required)</label>
      [email* emailaddress id:emailaddress placeholder "Email*"]
    </div>
  </div>

  <div class="row form-row">
    <div class="col-md-12">
      <label for="phone">Phone (Required)</label>
      [tel* phone id:phone minlength:10 maxlength:10 placeholder "Phone*"]
    </div>
  </div>

  <div class="row form-row">
    <div class="col-md-12">
      <label for="message">Message</label>
      [textarea message id:message placeholder "How Can We Help You?*"]
    </div>
  </div>

  <!-- UTM Tracking -->
  <div class="row form-row">
    <div class="col-md-12">
      [hidden utm_source default:get] [hidden utm_medium default:get] [hidden
      utm_campaign default:get] [hidden utm_content default:get] [hidden
      utm_term default:get] [submit class:btn "Submit"]
    </div>
  </div>
</div>
```

---

## 📨 Mail Tab Examples

### Mail 1: Admin Notification

**From:**

```text
[_site_title] <donotreply@example.com>
```

**Subject:**

```text
Home Page Lead Form Filled
```

**Message Body:**

```html
<p>Dear Admin,</p>
<p>Lead form submitted, below are the details:</p>
<p>First Name: [firstname]</p>
<p>Last Name: [lastname]</p>
<p>Email: [emailaddress]</p>
<p>Phone Number: [phone]</p>
<p>Message: [message]</p>
<p>URL: [_url]</p>
<p>UTM Source: [utm_source]</p>
<p>UTM Medium: [utm_medium]</p>
<p>UTM Campaign: [utm_campaign]</p>
<p>UTM Content: [utm_content]</p>
<p>UTM Term: [utm_term]</p>

-- This is a notification that a contact form was submitted on your website
([_site_title] [_site_url]).
```

### Mail 2: Auto Responder to User

**To:**

```text
[firstname] <[emailaddress]>
```

**Subject:**

```text
Thanks for your interest in [_site_title]
```

**Body:**

```html
<p>Dear [firstname],</p>

<p>
  Thanks for your interest in [_site_title]! We look forward to discussing our
  services with you. We will be getting in touch with you shortly using the
  information you provided.
</p>
```

---

## 📎 File Upload Fields

### Careers – File Upload (Single/Multiple)

```html
<div class="row form-row">
  <div class="col-sm-12">
    <label for="message">Upload Documents</label>
    [file file filetypes:pdf|txt|doc|docx|jpg|jpeg|png limit:10mb multiple]
    <p class="file-note">
      Upload Documents. PDF, TXT, DOC, DOCX are accepted file types. Limit is 10
      mb.
    </p>
  </div>
</div>
```

### Careers – Multiple File Upload (with Limits)

```html
<div class="col-sm-4">
  <label for="file1">Attach files</label>
  [mfile file1 limit:10485760 filetypes:pdf|txt|doc|docx|jpg|jpeg|png
  blacklist-types:exe|bat|com min-file:1 max-file:5]
</div>
```

Note - Use the plugin `Drag and Drop Multiple File Upload for Contact Form 7` for multiple file uploads.

> 💡 In Mail tab, use:

```text
[resume]
```

…under **Attachments** to send the uploaded files.

---

## 🔁 Reply-To Header

In **Additional Headers**, to set reply email to the user's email:

```text
Reply-To: [emailaddress]
```

Or a static fallback:

```text
Reply-To: email@example.com
```

---

## ✅ Redirect to Thank You Page

Add the following **in the Form tab footer**:

```html
<script type="text/javascript">
  document.addEventListener(
    "wpcf7mailsent",
    function (event) {
      if ("223" == event.detail.contactFormId) {
        window.location.href = "{site_url}/thank-you-quote/";
      }
    },
    false
  );
</script>
```

> Replace `223` with your actual form ID and update the URL accordingly.

---

## 🧠 Bonus Tips

- Always use `required` fields with `*` in CF7: `[text* name]`
- UTM fields help track marketing source info
- Avoid using `mailto:` as email From—use domain-based addresses
- Use placeholders to guide user input
- Hidden fields can help with spam filtering or lead tracking
