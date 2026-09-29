# ColorStack USC Sponsorship Outreach Agent

An agent that finds potential sponsors, updates ColorStack USC’s outreach spreadsheet, and creates **20 personalized sponsorship email drafts each weekday**. It also monitors replies, alerts designated team members when a company is interested, and handles clear declines with an approved response.

## What the agent does

Every weekday at 8:00 a.m. Pacific Time, the agent:

1. Reads the existing outreach spreadsheet and checks which companies have already been contacted.
2. Sources potential sponsors from company websites, recruiting pages, event sponsor lists, and other public sources.
3. Verifies each company’s website, sponsorship fit, and contact information. It does not guess email addresses.
4. Adds up to 20 new, qualified companies to the spreadsheet, including source links and a reason each company is a good fit.
5. Creates 20 personalized Gmail drafts requesting sponsorship. A ColorStack officer reviews and sends the initial emails.
6. Updates each spreadsheet row with its draft link and outreach status.

If fewer than 20 companies pass verification, the agent reports the shortfall instead of filling the quota with weak or duplicate leads.

## Sourcing tools to evaluate

| Tool | Potential use | Starting cost |
| --- | --- | --- |
| [Apify](https://apify.com/pricing) | Scraping public directories and websites with existing automation tools | Free plan includes $5/month in usage credits |
| [Firecrawl](https://www.firecrawl.dev/pricing) | Extracting readable information from company websites | Free plan includes 1,000 credits/month |
| [SerpApi](https://serpapi.com/pricing) | Finding companies and relevant pages through structured search results | Free plan includes 250 searches/month |
| Python requests + Beautiful Soup | Extracting information from simple public pages | No vendor fee; requires more maintenance |

**Recommended starting approach:** Use search to discover companies, verify details on their official websites, and test Firecrawl for page extraction. Add an Apify tool only when it produces enough qualified leads to justify its cost. Track the **cost per verified contact**, not just the cost per scraped page. Pricing may change.

## Outreach spreadsheet

The agent should work with ColorStack’s existing spreadsheet. Each company row should include:

- Company name and website domain
- Industry and location
- Why the company is a good sponsorship fit
- URLs supporting the information used in the email
- Contact name, role, email address, and source
- Campaign and assigned ColorStack officer
- Status: `candidate`, `verified`, `drafted`, `sent`, `interested`, `declined`, or `needs_review`
- Gmail draft ID and conversation ID
- Dates drafted, sent, and replied to
- Notes and a `do_not_contact` flag

Before adding a company, the agent checks its domain, alternate names, previous outreach, existing email conversations, and the do-not-contact list.

## Email drafting

Each draft should:

- Address a verified person or a publicly listed team inbox.
- Mention one specific, verified reason the company may be a good partner.
- Briefly explain ColorStack USC and the relevant sponsorship opportunity.
- Offer concrete ways to participate, such as sponsoring a challenge, providing mentors or judges, hosting a workshop, or connecting with students.
- Include the current sponsorship packet as an approved link or attachment.
- Ask for a short conversation.

The agent must use confirmed event dates and benefits from the current sponsorship packet. It should never invent a relationship with a company or claim that a benefit is available without confirmation.

**Example subject:** `{{Company}} × ColorStack USC — Stacked House Sponsorship`

## Reply handling

| Reply | Agent action |
| --- | --- |
| Interested, asks for a meeting, or requests the sponsorship packet | Update the spreadsheet, notify designated officers, and assign a person to respond. |
| Clearly declines sponsorship | Mark as declined and, if enabled, send the approved response once in the same email thread. |
| Asks not to be contacted again | Add the contact and company to the do-not-contact list. |
| Asks a question, refers another person, or gives an unclear answer | Mark `needs_review` and notify the assigned officer. |
| Out-of-office message or delivery failure | Record it without treating it as a sponsorship decision. |

### Positive reply notifications

Send an alert to a configurable list of ColorStack officers. The first version can send an email notification that appears on their iPhones and MacBooks through their mail apps. The alert should include the company, a brief reply excerpt, a link to the conversation, and the assigned owner.

Send **one alert per positive reply**, even if the monitoring job retries.

### Decline response template

Enable automatic decline replies only after the team approves this wording:

> Hi {{first_name}},
>
> Thanks for letting us know and for considering ColorStack USC. We understand, and we won’t follow up about this sponsorship opportunity.
>
> Best,  
> {{sender_name}}  
> ColorStack USC

Do not send this template for ambiguous replies or requests to stop all contact. Never send it twice in the same conversation.

## Suggested implementation

- **Scheduler:** Runs at 8:00 a.m. Pacific Time on weekdays.
- **Sourcing:** Search and selected scraping tools for public information.
- **Orchestration:** Python.
- **Tracking:** Google Sheets API connected to the existing outreach spreadsheet.
- **Drafts and replies:** Gmail API connected to an authorized ColorStack mailbox.
- **Notifications:** Email to a configurable list of officers.
- **Logs:** Record candidates found, companies rejected, drafts created, replies classified, notifications sent, errors, and sourcing costs.

Store API credentials securely rather than in the repository or spreadsheet.

## Build order

1. Connect the existing spreadsheet and authorized ColorStack Gmail account.
2. Import past outreach and implement duplicate and do-not-contact checks.
3. Test company sourcing and verification without writing to the spreadsheet.
4. Create Gmail drafts and link them to spreadsheet rows.
5. Add reply monitoring and positive reply notifications.
6. Test decline classification, then enable the approved decline template.

## Success criteria

- The agent creates up to 20 **qualified, nonduplicate drafts** each weekday.
- Every spreadsheet entry includes evidence for its contact information and personalization.
- Running the same job twice does not duplicate rows, drafts, alerts, or replies.
- Positive replies alert the designated team members.
- Clear declines receive the approved response only when that feature is enabled.
- Uncertain replies go to a ColorStack officer for review.
