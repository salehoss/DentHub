# DentHub
You are a senior full-stack engineer and UI/UX designer.

Build a complete university dental education portal called:

DentHub

IMPORTANT:
I am providing an Excel spreadsheet named:

BDS 1 timetable.xlsx

The spreadsheet is the SOURCE OF TRUTH for the semester schedule.

DO NOT invent a timetable.
DO NOT manually replace the spreadsheet with generic mock lectures.
DO NOT ignore any worksheet.
DO NOT combine weeks incorrectly.

Read and parse the uploaded Excel file.

==================================================
1. PURPOSE
==================================================

DentHub is a university dental student portal designed to make lecture
content extremely easy to access.

Students should be able to:

Login
→ Open DentHub
→ Select a week
→ See that week's exact timetable
→ Select a lecture
→ Access lecture notes
→ Access PDF/slides
→ Watch recordings
→ Access resources
→ Practice questions

The UI should be heavily inspired by the provided dark university
StudentHub screenshots, but the branding must say:

DentHub

NEVER use "StudentHub" anywhere in the final interface.

==================================================
2. TECHNOLOGY
==================================================

Build the application with:

- Next.js
- React
- TypeScript
- Tailwind CSS
- shadcn/ui
- Lucide icons

Preferred:

- Next.js App Router
- React Server Components where appropriate
- Zod
- date-fns
- PostgreSQL
- Prisma
- Auth.js / NextAuth architecture

The first version can use structured local timetable data generated from
the Excel spreadsheet.

The architecture must make it easy to connect PostgreSQL later.

==================================================
3. EXCEL TIMETABLE
==================================================

Read:

BDS 1 timetable.xlsx

It contains these worksheets:

BDS 1 term 1 week 1
BDS 1 term 1 week 2
BDS 1 term 1 week 3
BDS 1 term 1 week 4
BDS 1 term 1 week 5
BDS 1 term 1 week 6
BDS 1 term 1 week 7
BDS 1 term 1 week 8
BDS 1 term 1 week 9
BDS 1 term 1 week 10
BDS 1 term 1 week 11
BDS 1 term 1 week 12
BDS 1 term 1 week 13
BDS 1 term 1 week 14
BDS 1 term 1 week 15

There are exactly 15 semester weeks in this file.

Each worksheet represents one academic week.

Parse every worksheet.

==================================================
4. IMPORTANT EXCEL PARSING RULES
==================================================

The timetable is a visual spreadsheet rather than a normalized database.

Interpret it intelligently.

Typical structure:

Row 1:
BDS 1 Timetable 2026-2027

Row 2:
Term 1 / week X

Row 3:
Dates / weekday headings

Column A:
Time slots

Remaining columns:
Days

Cells contain:

- lecture title
- lecturer
- lab
- PBL
- TBL
- practical
- exam
- break
- room/location
- online class
- explicit time

A cell may contain multiple pieces of information separated by line
breaks.

For example:

Permanent maxillary incisors II
1:00 - 1:50
(Dr. Lina)

Parse this as:

{
  title: "Permanent maxillary incisors II",
  startTime: "13:00",
  endTime: "13:50",
  lecturer: "Dr. Lina"
}

Another example:

HB1 Lab
1:00 - 3:00
--------------
DHS1 Lab
1:00 - 3:00

This represents TWO events, not one.

Create:

HB1 Lab
13:00-15:00

and

DHS1 Lab
13:00-15:00

A divider such as:

--------------

often indicates separate events.

==================================================
5. TIME PARSING
==================================================

Prefer an explicit time written inside a timetable cell.

If no explicit time appears inside the cell, use the row's time slot.

Examples:

9:00-9:50
10:00-10:50
11:00-11:50
12:00-12:50
1:00-1:50
2:00-2:50
3:00-3:50

Normalize internal times to 24-hour values.

Example:

{
  startTime: "09:00",
  endTime: "09:50"
}

But display them to students as:

09:00 AM – 09:50 AM

==================================================
6. DATES
==================================================

Extract actual dates from the worksheet column headers.

Do not create fake dates.

The timetable includes semester dates beginning around:

August 2026

and continuing through:

December 2026.

Examples from the spreadsheet include:

Week 5:
Monday 21/9/2026
Tuesday 22/9/2026
Wednesday 23/9/2026
Thursday 24/9/2026

Week 6:
Monday 28/9/2026
Tuesday 29/9/2026
Wednesday 30/9/2026
Thursday 1/10/2026
Sunday 4/10/2026 — Online

Week 7:
Monday 5/10/2026
Tuesday 6/10/2026
Wednesday 7/10/2026
Thursday 8/10/2026

Week 8:
Midterm Exams
12–15/10/2026

and online lectures:
Sunday 18/10/2026

Week 9:
Sunday 18/10/2026
Monday 19/10/2026
Tuesday 20/10/2026
Wednesday 21/10/2026
Thursday 22/10/2026

Week 10:
Monday 26/10/2026
Tuesday 27/10/2026
Wednesday 28/10/2026
Thursday 29/10/2026
Sunday 1/11/2026 — Online

Week 11:
2/11/2026 – 5/11/2026

Week 12:
9/11/2026 – 12/11/2026
Sunday 15/11/2026 — Online

Week 13:
16/11/2026 – 19/11/2026

Week 14:
23/11/2026 – 26/11/2026

Week 15 includes:

Monday 30/11/2026

Martyr's Day
Tuesday 1/12/2026

UAE National Day
Wednesday 2/12/2026

IMPORTANT:
The source spreadsheet contains at least one suspicious date:

Thursday 4/12/2025

in Week 15.

Do NOT silently invent a correction.

Store source anomalies separately, for example:

{
  sourceDate: "4/12/2025",
  suspectedDate: "3/12/2026",
  requiresReview: true
}

Display a subtle administrator warning instead of changing source data
without acknowledgement.

Apply the same rule to any other obvious date inconsistency found while
parsing the spreadsheet.

==================================================
7. BREAKS
==================================================

Cells containing only:

Break

are NOT normal lectures.

Represent them separately:

type: "BREAK"

They may appear visually in the schedule but:

- should not open a lecture page
- should not appear in lecture counts
- should not have resources
- should not appear in search results unless specifically requested

==================================================
8. HOLIDAYS
==================================================

Recognize:

Martyr's Day

and:

UAE National Day

as calendar/holiday events.

Display holiday cards differently from lectures.

For example:

🇦🇪 UAE National Day
No scheduled lectures

Do not create lecture pages for holidays.

==================================================
9. MIDTERM WEEK
==================================================

Week 8 is marked:

Midterm Exams: 12 - 15/10/2026

Show this clearly.

For example:

WEEK 8
12 OCT – 18 OCT

MIDTERM EXAMS

Do not populate Monday–Thursday with invented lectures when the
spreadsheet has none.

Sunday online classes from the spreadsheet must still appear.

==================================================
10. ONLINE CLASSES
==================================================

Some Sundays are marked:

Online

Represent those events as:

mode: "ONLINE"

Display an icon such as:

Video / Wifi

and a badge:

ONLINE

Do not invent Zoom/Teams links.

If no meeting URL is supplied, show:

"Online meeting link not available."

==================================================
11. EVENT TYPES
==================================================

The spreadsheet includes several kinds of academic events.

Support:

LECTURE
LAB
PBL
TBL
PRACTICAL
EXAM
ONLINE
BREAK
HOLIDAY
OTHER

Determine type from cell text where possible.

Examples:

"PBL 10"
→ PBL

"TBL 2 session 1"
→ TBL

"DCP1 Lab"
→ LAB

"Practical Exam"
→ EXAM / PRACTICAL

==================================================
12. EXAMPLE REAL LECTURES
==================================================

The spreadsheet includes content such as:

Structure and properties of teeth

Moisture Control, Rubber Dam

Principles and practice of screening for oral diseases

Dental development timing and sequence

Comparative dental anatomy

Diet and dental caries

History taking

Digital and AI-Dentistry

Permanent maxillary incisors

Permanent mandibular incisors

Caries classification

Dental charting

Instrumentation in Operative Dentistry

Permanent canine

Permanent premolars

Permanent molars

Introduction to Impressions in prosthodontics

Elastic Impression Materials

Non-Elastic Impression Materials

Gypsum Products

Extra-oral & Intra-oral Soft tissue examination

Dental Indices

Saliva and dental caries

Caries microbiology

Observational and experimental studies

Cross sectional studies

Cohort studies

RCTs

Causation

Confounding

PBL

TBL

Mutation and chromosomal abnormalities

Regulation of gene expression

DNA damage & repair

Recombinant DNA technology and genetic engineering

Cell structure & function

Cytoplasm & cytoplasmic organelles

Nucleus

Epithelium

Glandular epithelium

Cell cycle

Mitosis

Meiosis & gametogenesis

Hematopoiesis

Introduction to Microbiology

Host-parasite relationship

Sterilization & disinfection

Staphylococci

Streptococci

Immunology

Introduction to Biochemistry

Nutrition

Energy production

and other content appearing in the uploaded Excel file.

DO NOT use this list instead of parsing the spreadsheet.

The spreadsheet remains the source of truth.

==================================================
13. DATA MODEL
==================================================

Convert spreadsheet content into a normalized structure.

Example:

interface Semester {
  id: string;
  name: string;
  academicYear: string;
  weeks: AcademicWeek[];
}

interface AcademicWeek {
  id: string;
  weekNumber: number;
  label: string;
  startDate: string;
  endDate: string;
  events: ScheduleEvent[];
}

interface ScheduleEvent {
  id: string;

  weekNumber: number;

  date: string;
  dayName: string;

  title: string;

  lecturer?: string;

  startTime?: string;
  endTime?: string;

  location?: string;

  type:
    | "LECTURE"
    | "LAB"
    | "PBL"
    | "TBL"
    | "PRACTICAL"
    | "EXAM"
    | "ONLINE"
    | "BREAK"
    | "HOLIDAY"
    | "OTHER";

  mode: "ONSITE" | "ONLINE";

  course?: string;

  sourceSheet: string;
  sourceCell?: string;

  resources: LectureResource[];

  sourceWarning?: string;
}

interface LectureResource {
  id: string;

  type:
    | "PDF"
    | "SLIDES"
    | "NOTES"
    | "VIDEO"
    | "RECORDING"
    | "LINK"
    | "QUESTIONS";

  title: string;

  url?: string;
}

==================================================
14. PRESERVE SOURCE DATA
==================================================

Create a development script such as:

scripts/import-timetable.ts

or:

scripts/parse-timetable.ts

The script must:

1. Open BDS 1 timetable.xlsx
2. Read all 15 sheets
3. Parse dates
4. Parse time slots
5. Split multiple events within cells
6. Detect lecturers
7. Detect labs/PBL/TBL/exams
8. Detect online classes
9. Detect breaks
10. Detect holidays
11. Preserve original source references
12. Generate normalized JSON or database records

Example output:

data/timetable.json

DO NOT require the application to parse Excel on every page request.

Instead:

Excel
→ import script
→ normalized data
→ application

==================================================
15. WEEK NAVIGATION
==================================================

The lecture page must have:

← Previous Week

Week 1

Next Week →

Also create a week dropdown:

Week 1
Week 2
Week 3
...
Week 15

URL structure:

/lectures?week=1
/lectures?week=2
...
/lectures?week=15

The selected week must survive page refresh.

==================================================
16. WEEK HEADER
==================================================

Show:

Week 6

28 Sep – 4 Oct 2026

If the week contains online Sunday classes show:

Includes Sunday Online Classes

If it contains exams:

MIDTERM WEEK

If holidays:

HOLIDAY WEEK

==================================================
17. SCHEDULE DESIGN
==================================================

Desktop layout:

Monday | Tuesday | Wednesday | Thursday | Sunday

Only show days present for that week.

Each day is a vertical column.

Header example:

MONDAY

28
SEP

Lecture cards underneath.

Each lecture card contains:

Time

Subject/title

Lecturer

Type

Room/location

Online badge when applicable

Resource indicators

Example:

09:00 – 09:50

Diet and dental caries

Dr. Abdulkhaliq

LECTURE

Lecture Hall E7 / M31-023

[Notes] [Slides] [Recording]

==================================================
18. MOBILE SCHEDULE
==================================================

Do NOT squeeze five columns onto a phone.

Mobile must show one day at a time.

Example:

Week 6

MONDAY
28 September

‹ Previous Day        Next Day ›

Then show lecture cards vertically.

Add a horizontal day selector:

MON
TUE
WED
THU
SUN

==================================================
19. DENTHUB BRANDING
==================================================

Top-left:

DentHub

Use a tasteful dentistry-oriented icon.

Possible icon:

tooth

Do not make the design childish.

Brand personality:

modern
premium
clinical
professional
academic

==================================================
20. DARK UI
==================================================

Use a dark interface inspired by the screenshots.

Background:

#09090B

Sidebar:

#08090D

Cards:

#18181B

Card hover:

#202024

Borders:

#2A2A30

Primary text:

#FAFAFA

Secondary text:

#A1A1AA

Main accent:

#6366F1

Secondary accent:

#8B5CF6

Success:

#10B981

Warning:

#F59E0B

Danger:

#EF4444

Use:

- rounded cards
- subtle borders
- minimal shadows
- premium typography
- clean spacing
- compact information density

==================================================
21. SIDEBAR
==================================================

Create a fixed desktop sidebar.

Logo:

DentHub

Navigation:

Home

Lectures

Units

Practice

Resources

Sheets

Recordings

Calendar

Bookmarks

Search

At bottom:

Settings

Profile

Log Out

Highlight the active route with purple/indigo styling.

Mobile:

Convert sidebar into a drawer.

==================================================
22. DASHBOARD
==================================================

Route:

/

Create header:

Welcome Back 👋

Subtitle:

Here's your DentHub overview.

Create an announcement card.

Example:

Keep up with this week's lectures.

View your timetable, notes, recordings and resources from one place.

[Open This Week →]

==================================================
23. DASHBOARD STATISTICS
==================================================

Show:

Semester Weeks
15

Lectures
calculate dynamically

Resources
calculate dynamically

Upcoming Classes
calculate dynamically

Do not hard-code lecture totals.

==================================================
24. TODAY'S SCHEDULE
==================================================

Dashboard section:

Today's Lectures

Automatically determine today's date.

If events are scheduled:

show them chronologically.

If none:

calendar icon

No lectures scheduled for today.

Add button:

View Weekly Schedule

==================================================
25. CURRENT WEEK
==================================================

Automatically determine which timetable week contains today's date.

Dashboard card:

Current Week

Week 6
28 Sep – 4 Oct

Show progress:

Monday
Tuesday
Wednesday
Thursday
Sunday

==================================================
26. LECTURE DETAIL PAGE
==================================================

Every actual lecture should be clickable.

Route:

/lectures/[id]

Example:

DentHub

Permanent maxillary incisors II

Dr. Lina

Sunday, 4 October 2026

01:00 PM – 01:50 PM

ONLINE

Tabs:

Overview
Notes
Slides
Recordings
Resources
Practice

==================================================
27. LECTURE OVERVIEW
==================================================

Show:

Lecture title

Lecturer

Date

Week number

Time

Location

Course/category

Delivery mode

Source timetable information

Then:

Important Notice

Lecture Notes

Slides

Recordings

Resources

Practice Questions

==================================================
28. NOTES
==================================================

Every lecture can eventually have notes.

Data:

{
  title,
  content,
  lectureId,
  createdAt,
  updatedAt
}

Support Markdown.

If no note exists:

No notes have been uploaded for this lecture yet.

==================================================
29. SLIDES AND PDFs
==================================================

Each lecture can contain:

Lecture Slides

Handouts

PDFs

Study Sheets

Example resource:

Thoracic Wall.pdf

Show:

PDF icon

file name

file size

Download

Preview

Do not create fake download URLs.

==================================================
30. RECORDINGS
==================================================

Lecture page should support multiple recordings.

Example:

Recording

2 recordings

[video player]

Recording 1
Recording 2

Supported:

YouTube
Vimeo
MP4
external university recording URL

Never fabricate recording links.

==================================================
31. PREVIOUS / NEXT LECTURE
==================================================

At the bottom:

← Previous Lecture

Next Lecture →

Order lectures chronologically across the semester.

Ignore:

breaks
holidays

when calculating previous/next lecture.

==================================================
32. SEARCH
==================================================

Global search.

Keyboard:

Ctrl + K
Cmd + K

Search:

lecture title
lecturer
week
date
course
PBL
TBL
labs
resources
notes
recordings

Example:

"caries"

could return:

Diet and dental caries

Caries classification I

Caries classification II

Caries microbiology

Saliva and dental caries

==================================================
33. FILTERS
==================================================

Schedule filters:

Week

Day

Lecturer

Type

Course

Delivery mode

Filters must work together.

Examples:

Week 12 + ONLINE

LAB only

Dr. Lina

PBL only

==================================================
34. CALENDAR
==================================================

Route:

/calendar

Views:

Month
Week
Agenda

Populate the calendar from the Excel timetable.

Clicking an event opens its lecture page.

==================================================
35. RESOURCES
==================================================

Route:

/resources

Categories:

Notes
Slides
PDF
Recording
Video
External resource
Practice questions

Filters:

Week
Lecture
Type
Lecturer

==================================================
36. BOOKMARKS
==================================================

Allow students to bookmark:

lectures
notes
PDFs
recordings

Route:

/bookmarks

Persist bookmarks in local storage initially.

Create architecture allowing database persistence later.

==================================================
37. PRACTICE
==================================================

Route:

/practice

Display:

Lecture Questions

Weekly Questions

Bookmarked Questions

Recently Attempted

Question Bank

Do not generate medical/dental clinical claims as authoritative
education content automatically.

Use placeholder/demo educational questions unless actual approved
question-bank content is supplied.

==================================================
38. UNIVERSITY LOGIN
==================================================

The project will later connect to a University ID.

Create:

/login

Design:

DentHub

Access your university learning content.

Button:

Continue with University ID

During development:

Demo Student

Do NOT ask the student to enter their university password directly into
DentHub.

Create an authentication abstraction supporting an official university:

OAuth 2.0

OpenID Connect

SAML

or officially supported university API.

==================================================
39. UNIVERSITY PROVIDER
==================================================

Create:

lib/integrations/university/

types.ts
provider.ts
mock-provider.ts

Interface:

interface UniversityProvider {
  authenticate(): Promise<UniversitySession>;

  getStudent(): Promise<Student>;

  getTimetable(): Promise<ScheduleEvent[]>;

  getLectureResources(
    lectureId: string
  ): Promise<LectureResource[]>;
}

Use:

MockUniversityProvider

until official university documentation is supplied.

==================================================
40. SECURITY
==================================================

Never:

store university passwords

scrape protected university pages without authorization

bypass university authentication

hard-code student credentials

fake successful University ID integration

University authentication must use an approved institution mechanism.

==================================================
41. FILE STRUCTURE
==================================================

Use something similar to:

app/
  layout.tsx
  page.tsx

  login/
    page.tsx

  lectures/
    page.tsx

    [id]/
      page.tsx

  calendar/
    page.tsx

  resources/
    page.tsx

  recordings/
    page.tsx

  practice/
    page.tsx

  bookmarks/
    page.tsx

  settings/
    page.tsx

components/

  layout/
    sidebar.tsx
    topbar.tsx
    mobile-sidebar.tsx

  dashboard/
    welcome-card.tsx
    semester-stats.tsx
    current-week.tsx
    todays-lectures.tsx

  schedule/
    week-selector.tsx
    week-header.tsx
    schedule-board.tsx
    day-column.tsx
    lecture-card.tsx
    mobile-day-selector.tsx

  lecture/
    lecture-header.tsx
    lecture-tabs.tsx
    overview.tsx
    notes.tsx
    resources.tsx
    recordings.tsx
    lecture-navigation.tsx

  search/
    command-search.tsx

  ui/

lib/

  timetable/
    parser.ts
    types.ts
    dates.ts
    normalize.ts

  integrations/
    university/
      types.ts
      provider.ts
      mock-provider.ts

data/
  timetable.json

scripts/
  import-timetable.ts

public/

prisma/
  schema.prisma

==================================================
42. DYNAMIC DATA
==================================================

Never create UI code like:

if week === 1 show X
if week === 2 show Y

Instead:

const selectedWeek = timetable.weeks.find(...)

and render dynamically.

All 15 weeks should use the SAME reusable components.

==================================================
43. DATA QUALITY
==================================================

The Excel source contains formatting inconsistencies.

Your parser must tolerate:

extra newlines

multiple spaces

different capitalization

Dr.
Dr
Prof.

times written as:

2.00-2.50

and:

2:00 - 2:50

room names embedded in column headings

multiple lecturers

multiple events inside one cell

empty cells

Sunday online columns

typos in source data

Do not crash because formatting differs slightly.

==================================================
44. SOURCE TRACEABILITY
==================================================

Every parsed event should retain:

sourceSheet

and ideally:

sourceCell

Example:

{
  sourceSheet: "BDS 1 term 1 week 6",
  sourceCell: "B9"
}

This allows administrators to check a lecture against the original
Excel timetable.

==================================================
45. IMPORT REPORT
==================================================

After parsing the spreadsheet, generate an import report.

Example:

DentHub timetable import

Sheets found: 15
Weeks imported: 15
Events imported: 123
Lectures: 70
Labs: 30
PBL/TBL: 15
Online classes: 12
Holidays: 2
Warnings: 2

Warnings:

- suspicious date found
- ambiguous lecturer
- time could not be determined

Do not silently discard unrecognized cells.

==================================================
46. ADMIN IMPORT PAGE
==================================================

Create optional development/admin page:

/admin/import

Show:

Timetable Source

BDS 1 timetable.xlsx

15 sheets detected

[Re-import timetable]

Then display:

weeks
events
warnings
errors

This functionality must be protected from ordinary students in a
production environment.

==================================================
47. RESPONSIVE DESIGN
==================================================

Desktop:

fixed sidebar
wide weekly timetable
multiple days side-by-side

Tablet:

2-3 columns

Mobile:

one day at a time
bottom or drawer navigation

Ensure there is no horizontal overflow that makes the app difficult to
use.

==================================================
48. EMPTY STATES
==================================================

Provide good empty states.

Examples:

No lectures scheduled for this day.

No resources have been uploaded.

No recording available.

No notes available.

No search results.

No bookmarked lectures.

==================================================
49. LOADING STATES
==================================================

Create:

DashboardSkeleton

ScheduleSkeleton

LectureCardSkeleton

LecturePageSkeleton

ResourceSkeleton

==================================================
50. ACCESSIBILITY
==================================================

Use:

semantic HTML

keyboard navigation

ARIA labels where appropriate

visible focus states

accessible dialogs

accessible menus

good contrast

==================================================
51. PERFORMANCE
==================================================

Do not load the full Excel workbook in the browser.

Parse Excel during import/build/server process.

Generate normalized JSON/database records.

Use server components where suitable.

Lazy-load recording/video components.

==================================================
52. REQUIRED FIRST-RUN EXPERIENCE
==================================================

When DentHub starts:

Dashboard

shows:

DentHub

Welcome Back

Today's lectures

Current week

Upcoming lecture

Semester progress

Student selects:

Lectures

Then sees:

Week 1 | Week 2 | ... | Week 15

Click:

Week 6

Then sees the exact timetable from:

BDS 1 term 1 week 6

including Monday, Tuesday, Wednesday, Thursday and Sunday Online.

==================================================
53. DO NOT INVENT CONTENT
==================================================

This is extremely important.

DO NOT invent:

lecture dates

lecture names

lecturers

labs

rooms

times

online classes

exam dates

holiday dates

The uploaded BDS 1 timetable.xlsx is authoritative for schedule content.

If the spreadsheet is ambiguous:

preserve the source

and flag:

Needs Review

==================================================
54. DO NOT MAKE ONLY A MOCKUP
==================================================

The final result must be functional.

Buttons need to work.

Week switching must work.

Lecture links must work.

Search must work.

Mobile navigation must work.

Timetable data must come from the imported spreadsheet.

Do not produce only HTML resembling a screenshot.

==================================================
55. COMPLETE CODE
==================================================

Generate COMPLETE files.

Do not output:

TODO

"rest of code"

"existing code"

"implement later"

"..."

or abbreviated function bodies.

Every referenced file must exist.

Every import must resolve.

==================================================
56. README
==================================================

Create README.md explaining:

Installation

npm install

Development

npm run dev

How timetable import works

How to run:

npm run import:timetable

How:

BDS 1 timetable.xlsx

becomes:

data/timetable.json

How to add another semester Excel workbook

How to attach notes/slides/recordings

How University ID will later be integrated

How data anomalies are reviewed

==================================================
57. FINAL VERIFICATION
==================================================

Before finishing:

Confirm exactly 15 workbook sheets were imported.

Confirm Weeks 1–15 exist.

Confirm actual dates from each worksheet were used.

Confirm Sunday online lectures appear where supplied.

Confirm Week 8 is represented as midterm week.

Confirm Week 15 holidays are represented.

Confirm source date anomalies are flagged.

Confirm split lab cells become separate events.

Confirm Break is not treated as a lecture.

Confirm lecture pages are clickable.

Confirm mobile timetable works.

Confirm every page says DentHub, not StudentHub.

Confirm there are no fake university credentials.

==================================================
58. FINAL PRODUCT GOAL
==================================================

The finished application should feel like a real modern dental-school
student platform.

The primary experience should be:

DentHub
→ Week
→ Day
→ Lecture
→ Notes / Slides / Recording / Resources

A student should be able to find the material for a lecture in seconds.

Now inspect the uploaded BDS 1 timetable.xlsx file first, parse ALL 15
worksheets, create normalized schedule data, and then generate the
complete DentHub application.

Do not stop after creating only the timetable parser.

Build the complete working website.
