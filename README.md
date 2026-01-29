# TimeCapsule

> Write a letter to your future self. No login required.

[![Live Demo](https://img.shields.io/badge/demo-live-success)](https://shezanusdf.github.io/FutureMe/)
[![Firebase](https://img.shields.io/badge/Firebase-Firestore-orange)](https://firebase.google.com/)

![TimeCapsule-preview](https://github.com/user-attachments/assets/828a6c16-1f80-40af-a093-06b810c9e467)

## What is TimeCapsule?

TimeCapsule is a minimalist web app that lets you send emails to your future self—up to 5 years ahead. No authentication, no distractions, no complexity. Just open the app, write your heart out, and schedule it.

##  Features

-  **Schedule up to 5 years** - Set any future date for delivery
-  **localStorage** - Never lose what you've written
-  **Automated delivery** - GitHub Actions sends emails daily
-  **Fully responsive** - Beautiful on desktop, tablet, and mobile


## Tech Stack

### Frontend
- **HTML5** - Semantic markup
- **CSS3** - Custom styling with rem units for responsiveness
- **Vanilla JavaScript** - No frameworks, pure ES6+
- **Firebase Web SDK** - Firestore integration

### Backend
- **Firebase Firestore** - NoSQL database for scheduled emails
- **Python 3.10** - Email automation script
- **GitHub Actions** - Serverless cron job runner
- **Gmail SMTP** - Email delivery service

## 📋 How It Works

```
User writes letter
       ↓
Saved to Firestore
       ↓
GitHub Actions runs daily (9 AM UTC)
       ↓
Python script checks for scheduled emails
       ↓
Sends via Gmail SMTP
       ↓
Marks as sent in database
```


##  Installation & Setup

### Prerequisites
- Firebase account
- Gmail account with App Password enabled
- GitHub account

### 1. Clone the Repository

```bash
git clone https://github.com/shezanusdf/FutureMe.git
cd FutureMe
```

### 2. Firebase Setup

1. Create a [Firebase project](https://console.firebase.google.com/)
2. Enable Firestore Database (production mode)
3. Get your web config:
   - Project Settings → Your apps → Web
   - Copy the `firebaseConfig` object
4. Generate service account key:
   - Project Settings → Service Accounts → Generate new private key
   - Save the JSON file

### 3. Configure Frontend

Edit `script.js` and replace the Firebase config:

```javascript
const firebaseConfig = {
    apiKey: "YOUR_API_KEY",
    authDomain: "YOUR_PROJECT_ID.firebaseapp.com",
    projectId: "YOUR_PROJECT_ID",
    storageBucket: "YOUR_PROJECT_ID.appspot.com",
    messagingSenderId: "YOUR_MESSAGING_SENDER_ID",
    appId: "YOUR_APP_ID"
};
```

### 4. Gmail Setup

1. Enable 2-Factor Authentication on your Gmail
2. Generate an App Password:
   - Google Account → Security → App Passwords
   - Create password named "TimeCapsule Email Sender"
   - Save the 16-character password

### 5. GitHub Secrets

Add these secrets in your repo (Settings → Secrets and variables → Actions):

| Secret Name | Value |
|------------|-------|
| `FIREBASE_CREDENTIALS` | Entire contents of Firebase service account JSON |
| `SENDER_EMAIL` | Your Gmail address |
| `SENDER_PASSWORD` | Gmail App Password (not your regular password) |

### 6. Firestore Security Rules

In Firebase Console, add these rules:

```javascript
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /scheduled_emails/{document} {
      // Allow anyone to create documents
      allow create: if request.resource.data.keys().hasAll(['name', 'email', 'send_date', 'letter', 'created_at', 'sent'])
                    && request.resource.data.sent == false;
      
      // Prevent reads, updates, and deletes from frontend
      allow read, update, delete: if false;
    }
  }
}
```

### 7. Deploy

**GitHub Pages:**
```bash
git add .
git commit -m "Initial commit"
git push origin main
# Enable GitHub Pages in repo Settings → Pages
```

**Or use Netlify/Vercel:**
- Connect your GitHub repo
- Deploy with default settings

##  Project Structure

```
TimeCapsule/
├── index.html              # Main HTML file
├── style.css               # Responsive CSS with rem units
├── script.js               # Frontend logic with Firebase
├── send_emails.py          # Python email automation script
├── requirements.txt        # Python dependencies
├── .gitignore             # Git ignore rules
├── firestore.rules        # Firestore security rules
├── README.md              # This file
└── .github/
    └── workflows/
        └── send_emails.yml # GitHub Actions workflow
```

##  Security & Privacy

- **No user authentication** - We never store passwords or personal data beyond what you provide
- **Write-only Firestore** - Frontend can only create documents, not read others' emails
- **Backend-only reads** - Only GitHub Actions (via Admin SDK) can query the database
- **Secrets management** - All credentials stored as GitHub secrets, never in code
- **HTTPS only** - All connections encrypted

##  Cost

**$0/month** for reasonable usage:

| Service | Free Tier | Your Usage |
|---------|-----------|------------|
| Firebase Firestore | 50K reads, 20K writes/day | ~1 write per email, ~1 read per send |
| GitHub Actions | 2,000 minutes/month | ~1 minute/day |
| Gmail SMTP | 500 emails/day | Depends on your users |
| GitHub Pages | Unlimited for public repos | Static hosting |

**Total: FREE** for thousands of users 

##  Customization

### Change Email Send Time

Edit `.github/workflows/send_emails.yml`:

```yaml
schedule:
  - cron: '0 14 * * *'  # 2 PM UTC instead of 9 AM
```

Use [crontab.guru](https://crontab.guru/) to generate cron expressions.

### Customize Email Template

Edit the `send_email()` function in `send_emails.py`:

```python
html = f"""
<html>
  <body style="your-custom-styles">
    {letter_content}
  </body>
</html>
"""
```

### Change Maximum Future Date

Edit validation in `script.js`:

```javascript
fiveYearsFromNow.setFullYear(fiveYearsFromNow.getFullYear() + 10); // Allow 10 years
```

##  Troubleshooting

### Emails Not Sending

1. Check GitHub Actions logs (Actions tab → Send Scheduled Emails)
2. Verify Gmail App Password (not your regular password!)
3. Check spam folder
4. Ensure Firestore has documents with today's date

### Frontend Not Saving

1. Open browser console (F12) for errors
2. Verify Firebase config is correct
3. Check Firestore security rules allow writes
4. Ensure you're not in incognito mode (localStorage won't work)

### GitHub Actions Failing

1. Verify all 3 secrets are set correctly
2. Check Python script logs for errors
3. Ensure `requirements.txt` is in repo root
4. Verify workflow file is in `.github/workflows/`

## 🤝 Contributing

Contributions are welcome! Here's how:

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/AmazingFeature`)
3. Commit your changes (`git commit -m 'Add some AmazingFeature'`)
4. Push to the branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request

**Please maintain the minimalist philosophy.** We're not adding social features, analytics, or unnecessary complexity.

##  To-Do

- [ ] Add email preview before sending
- [ ] Support for multiple recipients
- [ ] FAQ Page
- [ ] Confirmation email when scheduled
- [ ] Rate limiting and captcha to prevent spam

##  Acknowledgments

- Inspired by [emailyourfutureself.com][(https://www.futureme.org/](https://www.emailyourfutureself.com/))
- UI inspired by classic Windows 95 aesthetic
- Built with love for people who want to reflect on their journey

<div align="center">

**⭐ Star this repo if you found it helpful!**

Made with <3 and Coffee.

</div>
