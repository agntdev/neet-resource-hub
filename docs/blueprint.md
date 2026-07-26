# NEET Resource Hub — Bot specification

**Archetype:** content

**Voice:** warm and concise — write every user-facing message, button label, error, and empty state in this voice.

A Telegram bot for NEET repeaters/drop-year students to discover, organize, and access official/authorized study resources (NCERT, PYQs, mock tests, etc.) via subject/chapter/topic filters, quick-action buttons, and free-text search. Features include bookmarking, study plan creation, mock test practice with instant scoring, and a moderation system for flagged resources.

> This is the complete contract for the bot. Implement EVERY entry point, flow, feature, integration, and edge case below. The completeness review checks the bot against this document after each build pass.

## Primary audience

- NEET repeaters
- drop-year students

## Success criteria

- Users access at least 5 official resources per week
- Study plan exports are completed by 20% of active users
- Admin chat receives and resolves 90% of flagged resources within 24h

## Entry points

Every feature must be reachable from the bot's command/button surface (button-first; only /start and /help are slash commands).

- **/start** (command, actor: user, command: /start) — Open main menu with quick-access buttons
- **Search** (button, actor: user, callback: search:start) — Initiate resource search flow
- **Browse by Subject** (button, actor: user, callback: browse:subjects) — View resources organized by subject
- **My Bookmarks** (button, actor: user, callback: bookmarks:list) — Access saved resources
- **Mock Test** (button, actor: user, callback: mocktest:start) — Begin timed practice test
- **Study Plan** (button, actor: user, callback: plan:start) — Create/modify study schedule

## Flows

### main_menu
_Trigger:_ /start

1. Display greeting with quick-buttons: Search, Browse by Subject, My Bookmarks, Mock Test, Study Plan

_Data touched:_ User

### resource_search
_Trigger:_ search:start

1. Show subject buttons
2. Show chapter buttons for selected subject
3. Show resource type buttons
4. Accept free-text query if filters skipped
5. Display 10 resource cards with Save/Open buttons

_Data touched:_ Resource, Search query

### mock_test
_Trigger:_ mocktest:start

1. List available tests
2. Start timed single-question flow
3. Show score and recommendations after completion

_Data touched:_ Resource

### study_plan
_Trigger:_ plan:start

1. Show plan options
2. Add/remove resources
3. Export as PDF/summary

_Data touched:_ Collections

### flagging
_Trigger:_ user flag action

1. Capture flagged resource ID
2. Send alert to admin chat with details

_Data touched:_ Resource

## Data entities

Durable data (must survive a restart) uses the toolkit's persistent store, never in-memory maps.

- **Resource** _(retention: persistent)_ — Study material metadata
  - fields: name, category, subject, class, chapter/topic tags, language, short description, link/source, upload authorization status
- **User** _(retention: persistent)_ — Student preferences and saved data
  - fields: telegram id, preferred subjects, language, resource types
- **Search query** _(retention: session)_ — Saved search parameters
  - fields: query text, filters applied
- **Collections** _(retention: persistent)_ — User bookmarks and study plans
  - fields: resource IDs, plan structure

## Integrations

- **Telegram** (required) — Bot API messaging
Call external APIs against their real contract (correct endpoints, ids, params); credentials from env. Do not fake responses.

## Owner controls

- Approve/reject flagged resources
- Add/remove official resources
- Generate usage reports

## Notifications

- Flagged resource alerts to admin chat with metadata
- Study plan completion reminders to users

## Permissions & privacy

- Public access for all Telegram users
- User data (preferences/bookmarks) stored securely
- No unauthorized file sharing or data collection

## Edge cases

- No resources found for search
- User attempts to access unapproved content
- Multiple simultaneous flag reports for same resource

## Required tests

- End-to-end search flow with filters and free-text fallback
- Mock test submission with scoring and follow-up recommendations
- Flagging system with admin chat notifications

## Assumptions

- Owner will pre-populate official resource database
- Resource authorization checks are automated via metadata
