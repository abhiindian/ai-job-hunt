# Search Queries for Job Scraper

<!-- SETUP: Customize these queries based on your skills, target roles, and location -->

## Search Sites

Primary (Indian job market):
- **naukri.com** - largest Indian job board
- **linkedin.com/jobs** - LinkedIn job listings (filter: India / your city / Remote)
- **instahyre.com** - curated tech/startup roles in India
- **wellfound.com** (formerly AngelList) - Indian and global startup roles
- **foundit.in** (formerly Monster India) - broad Indian job board
- **hirist.tech** / **cutshort.io** - Indian tech-focused boards

Primary (International / remote job market):
- **linkedin.com/jobs** - global listings (filter: country / Remote)
- **indeed.com** - global aggregator (works across regions)
- **glassdoor.com** - global listings with company/salary context
- **wellfound.com** - global startup roles (many remote)
- **remoteok.com** / **weworkremotely.com** - remote-first international roles

Secondary (company career pages via Google):
- Direct Google searches with `site:` filters for known target companies

## Query Categories

Queries are grouped by priority. Each query should be combined with your location terms (e.g. "Bengaluru", "Mumbai", "India", "Remote") where the site supports it. For international/remote roles, use the country name or "Remote" in place of a city.

### Priority 1: [YOUR_PRIMARY_ROLE_TYPE]

These match your strongest and most desired career direction.

```
site:naukri.com "[YOUR_PRIMARY_JOB_TITLE]" [YOUR_CITY]
site:linkedin.com/jobs "[YOUR_PRIMARY_JOB_TITLE]" [YOUR_CITY] India
site:linkedin.com/jobs "[YOUR_PRIMARY_JOB_TITLE]" Remote
site:wellfound.com "[YOUR_KEY_SKILL]" [YOUR_CITY]
```

### Priority 2: [YOUR_DOMAIN_EXPERTISE]

These match your domain expertise.

```
site:naukri.com [YOUR_DOMAIN_KEYWORD_1] [YOUR_CITY]
site:instahyre.com [YOUR_DOMAIN_KEYWORD_2] [YOUR_CITY]
site:linkedin.com/jobs [YOUR_DOMAIN_KEYWORD_1] [YOUR_CITY] India OR Remote
site:indeed.com [YOUR_DOMAIN_KEYWORD_2] [YOUR_COUNTRY]
```

### Priority 3: [YOUR_ADJACENT_ROLE_TYPE]

Adjacent roles you could pivot into.

```
site:naukri.com "[YOUR_ADJACENT_TITLE_1]" [YOUR_KEY_SKILL] [YOUR_CITY]
site:linkedin.com/jobs "[YOUR_ADJACENT_TITLE_2]" [YOUR_KEY_SKILL] Remote
```

### Priority 4: Broader Technical / Remote

Wider net for general technical roles, including international remote.

```
site:naukri.com [YOUR_KEY_SKILL] developer [YOUR_CITY]
site:linkedin.com/jobs "[YOUR_KEY_SKILL] developer" [YOUR_CITY] India
site:weworkremotely.com [YOUR_KEY_SKILL]
site:remoteok.com [YOUR_KEY_SKILL]
```

## Location Filter

When evaluating results, verify the job location matches your target. Define acceptable areas:
- [YOUR_CITY] and surrounding metro area (e.g. Bengaluru, Mumbai, Pune, Hyderabad, Delhi NCR, Chennai)
- Other Indian cities you would relocate to: [ACCEPTABLE_CITY_1], [ACCEPTABLE_CITY_2]
- **Remote (India)** - India-based remote roles
- **Remote (International)** - global remote roles open to your timezone/country
- [ON_SITE_INTL_AREA] (on-site international - only if open to relocation / visa sponsorship)
- [TOO_FAR_AREA] (excluded - e.g. on-site roles with no relocation support)

For international on-site roles, check whether the posting offers **visa sponsorship / relocation** before including it. Flag roles that require work authorization you do not hold.

## Date Filter

Only include jobs posted within the last 14 days, or with an application deadline that has not yet passed. If a posting date cannot be determined, include it but flag as "date unknown".

## Adapting Queries

If the user specifies a focus area, select queries from the matching category and also generate 2-3 custom queries for that focus. For example:
- "/scrape [focus_area]" -> relevant category queries + custom focus-specific queries
- "/scrape remote" -> prioritize LinkedIn Remote, Wellfound, RemoteOK, and We Work Remotely queries
- "/scrape india" -> prioritize Naukri, Instahyre, and LinkedIn India queries
