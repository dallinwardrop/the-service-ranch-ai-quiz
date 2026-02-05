# Service Ranch AI Readiness Quiz - Setup Instructions

## Quick Start

Your quiz is ready to deploy! Here's how to get it collecting leads in Google Sheets.

---

## Step 1: Host the Quiz

### Option A: Simple Web Hosting (Recommended)
1. Upload `service-ranch-ai-quiz.html` to your website
2. Or use a free service like:
   - **Netlify** (drag and drop the HTML file)
   - **Vercel** (connect to GitHub)
   - **GitHub Pages** (free, simple)

### Option B: Quick Test
- Just open the HTML file in your browser to test locally
- Won't save to Google Sheets yet, but you can see how it works

---

## Step 2: Set Up Google Sheets Integration

### Create Your Google Sheet

1. Go to [Google Sheets](https://sheets.google.com)
2. Create a new spreadsheet
3. Name it "Service Ranch Leads" or whatever you want
4. Add these column headers in Row 1:
   - **A1:** Timestamp
   - **B1:** Email
   - **C1:** Score
   - **D1:** Rating
   - **E1:** Estimated Loss
   - **F1:** Answers (JSON)
   - **G1:** Category Scores (JSON)

### Create the Apps Script

1. In your Google Sheet, go to **Extensions > Apps Script**
2. Delete any default code
3. Paste this code:

```javascript
function doPost(e) {
  try {
    // Open your spreadsheet
    var sheet = SpreadsheetApp.openById('YOUR_SPREADSHEET_ID').getActiveSheet();
    
    // Parse the incoming data
    var data = JSON.parse(e.postData.contents);
    
    // Add a new row with the data
    sheet.appendRow([
      new Date(),                           // Timestamp
      data.email,                           // Email
      data.totalScore,                      // Score (0-50)
      data.rating,                          // Rating (Ranch Boss, etc.)
      data.estimatedLoss,                   // Estimated annual loss
      JSON.stringify(data.answers),         // All answers
      JSON.stringify(data.categoryScores)   // Category breakdown
    ]);
    
    return ContentService.createTextOutput(JSON.stringify({success: true}))
      .setMimeType(ContentService.MimeType.JSON);
      
  } catch (error) {
    return ContentService.createTextOutput(JSON.stringify({success: false, error: error.toString()}))
      .setMimeType(ContentService.MimeType.JSON);
  }
}
```

4. **Replace `YOUR_SPREADSHEET_ID`** with your actual spreadsheet ID
   - Find it in the URL: `https://docs.google.com/spreadsheets/d/SPREADSHEET_ID_HERE/edit`
   - Copy everything between `/d/` and `/edit`

5. Click **Save** (disk icon)
6. Click **Deploy > New deployment**
7. Click the gear icon next to "Select type" and choose **Web app**
8. Settings:
   - **Description:** "Service Ranch Quiz Webhook"
   - **Execute as:** Me
   - **Who has access:** Anyone
9. Click **Deploy**
10. **Copy the Web App URL** (looks like `https://script.google.com/macros/s/...../exec`)

### Connect It to Your Quiz

1. Open `service-ranch-ai-quiz.html` in a text editor
2. Find this line (near the top of the `<script>` section):
   ```javascript
   const GOOGLE_SHEETS_URL = 'PASTE_YOUR_DEPLOYMENT_URL_HERE';
   ```
3. Replace `PASTE_YOUR_DEPLOYMENT_URL_HERE` with your Web App URL from step 10 above
4. Save the file
5. Re-upload to your website

---

## Step 3: Test It!

1. Go to your quiz URL
2. Complete the quiz with a test email (use your own email)
3. Check your Google Sheet - you should see a new row with your data!

**Troubleshooting:**
- Make sure the Apps Script is deployed as "Anyone can access"
- Check the browser console (F12) for any errors
- The script uses `no-cors` mode, so you won't see responses in the console - just check the sheet

---

## Step 4: Set Up Email Automation (Optional)

### Option A: Zapier (Easiest)
1. Sign up at [zapier.com](https://zapier.com)
2. Create a new Zap:
   - **Trigger:** Google Sheets - New Row
   - **Action:** Email - Send Email (or your email platform)
3. Customize the email to include their score and next steps

### Option B: Google Sheets + Gmail (Free)
Use Apps Script to auto-send emails when new rows are added:

```javascript
function onEdit(e) {
  var sheet = e.source.getActiveSheet();
  var row = e.range.getRow();
  
  // Only proceed if this is a new row (not the header)
  if (row > 1) {
    var email = sheet.getRange(row, 2).getValue(); // Column B
    var score = sheet.getRange(row, 3).getValue(); // Column C
    var rating = sheet.getRange(row, 4).getValue(); // Column D
    
    var subject = "Your Service Ranch AI Readiness Score: " + score + "/50";
    var body = "Hi there,\n\n" +
               "Thanks for taking the AI Readiness Assessment!\n\n" +
               "Your Score: " + score + "/50 - \"" + rating + "\"\n\n" +
               "Ready to dive deeper? Schedule a call: [YOUR_CALENDAR_LINK]\n\n" +
               "Best,\nService Ranch Team";
    
    GmailApp.sendEmail(email, subject, body);
  }
}
```

### Option C: Use Your Existing CRM
Most CRMs (HubSpot, Salesforce, etc.) can import from Google Sheets via:
- Zapier
- Native integrations
- CSV export/import

---

## What Data Gets Collected

For each quiz submission, you'll get:

- **Timestamp** - When they took it
- **Email** - Their email address
- **Score** - Their total score (0-50)
- **Rating** - "Ranch Boss", "Trail Boss", etc.
- **Estimated Loss** - Calculated dollar amount
- **Answers** - JSON of all their responses (for deeper analysis)
- **Category Scores** - Breakdown by category

---

## Marketing the Quiz

### Embed It
Add this to any page on your site:
```html
<iframe src="YOUR_QUIZ_URL" width="100%" height="800px" frameborder="0"></iframe>
```

### Direct Link
Share the URL directly:
- Email campaigns
- LinkedIn posts
- Facebook groups
- Industry forums

### Landing Page
Use the quiz AS your landing page - it's designed to be standalone

---

## Next Steps

1. ✅ Host the quiz
2. ✅ Set up Google Sheets
3. ✅ Test with your own email
4. Set up email automation
5. Start driving traffic!

### Drive Traffic To Your Quiz:

**LinkedIn Strategy:**
- Post: "Most landscaping companies are losing $180K/year to manual processes. Take this 3-minute assessment to find out where your money is going."
- Share in relevant groups
- Run LinkedIn ads targeting landscaping business owners

**Email Your List:**
Subject: "How much is manual data entry costing you?"

**Industry Forums:**
- LawnSite
- Hardscape groups
- Local business associations

**Facebook/Instagram:**
- Before/After: "We helped [Company] recover $240K by eliminating double data entry"
- Share quiz results (with permission)

---

## Support

If you run into issues:
1. Check the browser console (F12) for errors
2. Verify the Google Sheets deployment URL is correct
3. Make sure the spreadsheet ID in Apps Script matches your sheet
4. Test the Apps Script directly in Google

Questions? Email contact@serviceranch.com

---

## Pro Tips

**Segment Your Leads by Score:**
- 0-19 (Lost in Weeds): High urgency, maximum pain
- 20-27 (All Hands): Ready for help, clear problems
- 28-34 (Working Ranch): Good prospects, need education
- 35-42 (Trail Boss): Lower urgency, long-term nurture
- 43-50 (Ranch Boss): Probably not buyers, but could be referral partners

**Follow-Up Cadence:**
- Day 0: Auto-email with results summary
- Day 2: Personal email addressing their #1 pain point
- Day 7: Case study of similar company
- Day 14: "$497 audit" offer
- Day 21: "Revenue Roundup" consultation

**Use the Data:**
The JSON in column F contains all their answers - you can analyze:
- Which pain points are most common?
- Where do most companies score lowest?
- What's the average estimated loss?
Use this to refine your marketing messaging!
