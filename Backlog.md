# Agile Backlog

**Generated**: 2025-12-10T06:20:26.636830
**Total Epics**: 6
**Total Stories**: 18
**Total Points**: 99
**Estimation Scale**: fibonacci
**SSCS Compliance**: v2.0
**TDD Workflow**: RED → GREEN → REFACTOR
**Branch Naming**: `feature/{issue-number}-{slug}`


## Epic #1000: Authentication & User Management

Implement secure JWT-based authentication system with role-based access control for DJs and Admins

### User Stories

#### Story #1001: As a DJ, I want to register an account so that I can access the streaming platform

**Points**: 5 | **Type**: feature | **Status**: unstarted

Implement user registration endpoint with email/password validation, password hashing, and JWT token generation. Store user credentials securely in PostgreSQL with encrypted sensitive fields.

**Acceptance Criteria**:
1. Given a new user with valid email and password, When they submit registration form, Then account is created with hashed password and JWT tokens are returned
2. Given a user with invalid email format, When they attempt registration, Then a 400 Bad Request error is returned with validation details
3. Given a user with existing email, When they attempt registration, Then a 409 Conflict error is returned
4. Given a registered user, When they provide correct credentials to login endpoint, Then access and refresh tokens are returned with 24h expiry

**TDD Workflow**:
- 🔴 RED: `WIP: red tests for user registration and JWT authentication`
- 🟢 GREEN: `green: user registration endpoint with JWT token generation`
- 🔵 REFACTOR: `refactor: extract password validation and token utilities`

**Branch**: `feature/1001-user-registration-jwt`

**Labels**: user-story, authentication, security

#### Story #1002: As a DJ, I want to refresh my access token so that I can maintain authenticated sessions

**Points**: 3 | **Type**: feature | **Status**: unstarted

Implement token refresh mechanism using refresh tokens. Validate refresh token, generate new access token, and rotate refresh token for security.

**Acceptance Criteria**:
1. Given a valid refresh token, When user requests token refresh, Then a new access token is returned with 24h expiry
2. Given an expired refresh token, When user requests token refresh, Then a 401 Unauthorized error is returned
3. Given an invalid refresh token, When user requests token refresh, Then a 401 Unauthorized error is returned
4. Given a successful refresh, When new access token is generated, Then old refresh token is rotated

**TDD Workflow**:
- 🔴 RED: `WIP: red tests for token refresh mechanism`
- 🟢 GREEN: `green: token refresh endpoint with rotation`
- 🔵 REFACTOR: `refactor: improve token validation and error handling`

**Branch**: `feature/1002-token-refresh`

**Labels**: user-story, authentication, security

#### Story #1003: As a system administrator, I want role-based access control so that I can manage permissions for DJs and Admins

**Points**: 5 | **Type**: feature | **Status**: unstarted

Implement RBAC middleware with scope validation (streams:read, streams:write, playlists:read, playlists:write, podcasts:read, podcasts:write, monetization:read, monetization:write, domains:read, domains:write, analytics:read). Enforce permissions on all protected endpoints.

**Acceptance Criteria**:
1. Given a DJ with 'streams:write' scope, When they access stream creation endpoint, Then request is authorized
2. Given a DJ without 'streams:write' scope, When they access stream creation endpoint, Then a 403 Forbidden error is returned
3. Given an Admin role, When they access any endpoint, Then request is authorized with full permissions
4. Given a JWT without required scope, When accessing protected resource, Then a 403 Forbidden error with scope details is returned

**TDD Workflow**:
- 🔴 RED: `WIP: red tests for RBAC middleware and scope validation`
- 🟢 GREEN: `green: RBAC middleware with scope enforcement`
- 🔵 REFACTOR: `refactor: extract permission checking utilities and add comprehensive documentation`

**Branch**: `feature/1003-rbac-scope-validation`

**Labels**: user-story, authentication, authorization, security


## Epic #2000: Live Streaming (Shoutcast Relay)

Implement Shoutcast source registration, relay worker orchestration, HLS segmentation with FFmpeg, and live stream endpoints for listeners

### User Stories

#### Story #2001: As a DJ, I want to register my Shoutcast source so that I can relay my live stream to listeners

**Points**: 8 | **Type**: feature | **Status**: unstarted

Implement POST /v1/streams endpoint to register Shoutcast source with validation, encrypt stream_key, store configuration in PostgreSQL, and trigger Kubernetes Job to spin up Relay Worker Pod that connects to Shoutcast and begins HLS segmentation.

**Acceptance Criteria**:
1. Given a DJ with valid Shoutcast URL and credentials, When they submit stream registration, Then stream record is created and Relay Worker Pod is deployed
2. Given a DJ with invalid Shoutcast URL, When they submit registration, Then a 422 Unprocessable Entity error is returned
3. Given a registered stream, When Relay Worker connects to Shoutcast, Then HLS segments are generated every 6 seconds
4. Given a Relay Worker Pod, When it starts, Then it sends heartbeat to API every 30 seconds with listener count

**TDD Workflow**:
- 🔴 RED: `WIP: red tests for shoutcast registration and relay worker orchestration`
- 🟢 GREEN: `green: stream registration endpoint with kubernetes job deployment`
- 🔵 REFACTOR: `refactor: extract shoutcast validation and relay worker configuration`

**Branch**: `feature/2001-shoutcast-registration`

**Labels**: user-story, live-streaming, infrastructure

#### Story #2002: As a DJ, I want to retrieve live stream metadata so that I can monitor current track, bitrate, and listener count

**Points**: 5 | **Type**: feature | **Status**: unstarted

Implement GET /v1/streams/{stream_id}/status endpoint that queries Shoutcast admin API for current_track and bitrate, queries Relay Worker for listener count and connection status, and returns combined real-time data.

**Acceptance Criteria**:
1. Given a live stream with active Relay Worker, When DJ requests status, Then current track, bitrate, listeners, uptime, and relay_connected status are returned
2. Given a stream with offline Shoutcast source, When DJ requests status, Then a 503 Service Unavailable error is returned
3. Given a stream with crashed Relay Worker, When DJ requests status, Then relay_connected is false and listener count is 0
4. Given a private stream, When unauthorized user requests status, Then a 403 Forbidden error is returned

**TDD Workflow**:
- 🔴 RED: `WIP: red tests for live stream metadata retrieval`
- 🟢 GREEN: `green: stream status endpoint with shoutcast and relay worker queries`
- 🔵 REFACTOR: `refactor: improve error handling and add caching for metadata`

**Branch**: `feature/2002-stream-metadata`

**Labels**: user-story, live-streaming, monitoring

#### Story #2003: As a listener, I want to access live HLS stream so that I can play audio in my browser or mobile app

**Points**: 5 | **Type**: feature | **Status**: unstarted

Implement GET /v1/streams/{stream_id}/live.m3u8 endpoint that serves HLS manifest from Relay Worker, enforces authentication for private streams, checks monetization_enabled subscription status, and caches manifest at CDN with 30s TTL.

**Acceptance Criteria**:
1. Given a public live stream, When listener requests HLS manifest, Then manifest with TS segment URLs is returned
2. Given a private stream without authentication, When listener requests manifest, Then a 403 Forbidden error is returned
3. Given a monetized stream without active subscription, When listener requests manifest, Then a 403 Forbidden error is returned
4. Given a valid HLS manifest, When listener plays stream, Then TS segments are fetched from CDN with 60s cache TTL

**TDD Workflow**:
- 🔴 RED: `WIP: red tests for live HLS manifest serving`
- 🟢 GREEN: `green: HLS manifest endpoint with authentication and CDN caching`
- 🔵 REFACTOR: `refactor: extract authentication middleware and improve CDN integration`

**Branch**: `feature/2003-live-hls-endpoint`

**Labels**: user-story, live-streaming, cdn


## Epic #3000: Audio File Upload & On-Demand Playlists

Implement direct MP3 file uploads, background HLS transcoding with FFmpeg, playlist creation with mixed upload/external sources, and on-demand streaming endpoints

### User Stories

#### Story #3001: As a DJ, I want to upload MP3 files so that I can use them in playlists and podcasts

**Points**: 8 | **Type**: feature | **Status**: unstarted

Implement POST /v1/uploads/audio endpoint with multipart/form-data handling, file validation (audio/mpeg, max 100MB), virus scanning, S3 storage under /uploads/audio/{user_id}/{upload_id}/, and background job to generate HLS segments with FFmpeg.

**Acceptance Criteria**:
1. Given a DJ with valid MP3 file under 100MB, When they upload file, Then file is stored in S3 and transcoding job is enqueued
2. Given a DJ with file over 100MB, When they upload file, Then a 413 Payload Too Large error is returned
3. Given a DJ with non-MP3 file, When they upload file, Then a 400 Bad Request error is returned
4. Given an uploaded file, When transcoding completes, Then HLS segments are generated and upload status is set to 'ready'

**TDD Workflow**:
- 🔴 RED: `WIP: red tests for audio file upload and transcoding`
- 🟢 GREEN: `green: file upload endpoint with S3 storage and background transcoding`
- 🔵 REFACTOR: `refactor: extract file validation and improve error handling`

**Branch**: `feature/3001-audio-file-upload`

**Labels**: user-story, file-upload, transcoding

#### Story #3002: As a DJ, I want to create on-demand playlists so that I can provide curated audio streams to listeners

**Points**: 5 | **Type**: feature | **Status**: unstarted

Implement POST /v1/playlists endpoint that accepts tracks from uploads or external URLs, validates all sources (upload_id exists and ready, external URL returns audio/mpeg), stores playlist metadata in PostgreSQL, and generates HLS manifest.

**Acceptance Criteria**:
1. Given a DJ with valid uploaded tracks, When they create playlist, Then playlist record is created and HLS manifest is generated
2. Given a DJ with external URL tracks, When they create playlist, Then external URLs are validated via HEAD request
3. Given a DJ with invalid upload_id, When they create playlist, Then a 422 Unprocessable Entity error is returned
4. Given a created playlist, When listener requests HLS manifest, Then concatenated HLS manifest with all track segments is returned

**TDD Workflow**:
- 🔴 RED: `WIP: red tests for playlist creation and validation`
- 🟢 GREEN: `green: playlist creation endpoint with track validation`
- 🔵 REFACTOR: `refactor: extract track validation utilities and improve HLS manifest generation`

**Branch**: `feature/3002-playlist-creation`

**Labels**: user-story, playlists, validation

#### Story #3003: As a listener, I want to stream on-demand playlists so that I can listen to curated audio content

**Points**: 5 | **Type**: feature | **Status**: unstarted

Implement GET /v1/playlists/{playlist_id}/stream.m3u8 endpoint that serves pre-generated or dynamically generated HLS manifest, enforces authentication for private playlists, checks monetization subscription, and caches at CDN with 30s TTL.

**Acceptance Criteria**:
1. Given a public playlist, When listener requests HLS manifest, Then manifest with TS segment URLs is returned
2. Given a private playlist without authentication, When listener requests manifest, Then a 403 Forbidden error is returned
3. Given a monetized playlist without subscription, When listener requests manifest, Then a 403 Forbidden error is returned
4. Given a valid playlist manifest, When listener plays stream, Then TS segments are fetched from CDN with 60s cache TTL

**TDD Workflow**:
- 🔴 RED: `WIP: red tests for on-demand playlist streaming`
- 🟢 GREEN: `green: playlist HLS endpoint with authentication and CDN caching`
- 🔵 REFACTOR: `refactor: extract authentication middleware and improve manifest caching`

**Branch**: `feature/3003-playlist-streaming`

**Labels**: user-story, playlists, streaming


## Epic #4000: Podcast Hosting & RSS with Custom Domains

Implement podcast series creation, episode management, RSS 2.0 feed generation, custom domain registration with DNS/TLS automation via Let's Encrypt

### User Stories

#### Story #4001: As a DJ, I want to create a podcast series with custom domain so that I can host my podcast at podcast.djname.com

**Points**: 8 | **Type**: feature | **Status**: unstarted

Implement POST /v1/podcasts endpoint that creates podcast metadata, validates custom domain ownership via DNS TXT record, initiates DNS CNAME creation, provisions TLS certificate via Let's Encrypt ACME, and generates RSS feed URL.

**Acceptance Criteria**:
1. Given a DJ with valid podcast metadata and custom domain, When they create podcast, Then podcast record is created and DNS verification is initiated
2. Given a DJ with invalid custom domain, When they create podcast, Then a 400 Bad Request error is returned
3. Given a DJ with domain already in use, When they create podcast, Then a 409 Conflict error is returned
4. Given a verified custom domain, When TLS provisioning completes, Then RSS feed is accessible at https://podcast.djname.com/rss.xml

**TDD Workflow**:
- 🔴 RED: `WIP: red tests for podcast creation and custom domain setup`
- 🟢 GREEN: `green: podcast creation endpoint with DNS and TLS automation`
- 🔵 REFACTOR: `refactor: extract DNS validation and TLS provisioning utilities`

**Branch**: `feature/4001-podcast-custom-domain`

**Labels**: user-story, podcasts, dns, tls

#### Story #4002: As a DJ, I want to add episodes to my podcast so that I can publish new content

**Points**: 5 | **Type**: feature | **Status**: unstarted

Implement POST /v1/podcasts/{podcast_id}/episodes endpoint that accepts episode metadata with upload_id or external media_url, validates source, stores episode record, and triggers RSS feed regeneration with proper iTunes tags.

**Acceptance Criteria**:
1. Given a DJ with valid episode metadata and upload_id, When they add episode, Then episode record is created and RSS feed is regenerated
2. Given a DJ with external media_url, When they add episode, Then URL is validated via HEAD request
3. Given a DJ with invalid upload_id, When they add episode, Then a 422 Unprocessable Entity error is returned
4. Given a monetized episode, When RSS feed is generated, Then enclosure URL is a signed URL requiring subscription

**TDD Workflow**:
- 🔴 RED: `WIP: red tests for podcast episode creation`
- 🟢 GREEN: `green: episode creation endpoint with RSS regeneration`
- 🔵 REFACTOR: `refactor: extract RSS generation utilities and improve validation`

**Branch**: `feature/4002-podcast-episodes`

**Labels**: user-story, podcasts, rss

#### Story #4003: As a podcast listener, I want to subscribe to RSS feed so that I can receive new episodes in my podcast app

**Points**: 5 | **Type**: feature | **Status**: unstarted

Implement GET https://{custom_domain}/rss.xml endpoint that generates valid RSS 2.0 XML with iTunes tags, enforces monetization checks for paywalled episodes, caches at CDN with 300s TTL, and validates against RSS 2.0 DTD.

**Acceptance Criteria**:
1. Given a public podcast, When listener requests RSS feed, Then valid RSS 2.0 XML with all episodes is returned
2. Given a monetized podcast, When listener requests RSS without subscription, Then paywalled episodes have signed enclosure URLs
3. Given a valid RSS feed, When parsed by podcast apps, Then all iTunes tags are correctly formatted
4. Given an updated podcast, When RSS is regenerated, Then CDN cache is invalidated

**TDD Workflow**:
- 🔴 RED: `WIP: red tests for RSS feed generation and validation`
- 🟢 GREEN: `green: RSS feed endpoint with iTunes tags and CDN caching`
- 🔵 REFACTOR: `refactor: extract RSS generation and improve XML validation`

**Branch**: `feature/4003-rss-feed-generation`

**Labels**: user-story, podcasts, rss, cdn


## Epic #5000: Monetization & Ad Insertion

Implement Stripe subscription plans, subscription management, paywall enforcement for streams/playlists/podcasts, and ad insertion hooks for live and on-demand content

### User Stories

#### Story #5001: As a DJ, I want to create subscription plans so that I can monetize my content

**Points**: 5 | **Type**: feature | **Status**: unstarted

Implement POST /v1/monetization/plans endpoint that creates subscription plan in database, integrates with Stripe to create product and price, stores stripe_product_id and stripe_price_id, and returns plan details.

**Acceptance Criteria**:
1. Given a DJ with valid plan details, When they create plan, Then plan record is created and Stripe product/price are generated
2. Given a DJ with invalid price_cents, When they create plan, Then a 400 Bad Request error is returned
3. Given a created plan, When retrieved, Then stripe_product_id and stripe_price_id are included
4. Given a plan with features array, When stored, Then features are persisted as JSONB in PostgreSQL

**TDD Workflow**:
- 🔴 RED: `WIP: red tests for subscription plan creation`
- 🟢 GREEN: `green: subscription plan endpoint with Stripe integration`
- 🔵 REFACTOR: `refactor: extract Stripe utilities and improve error handling`

**Branch**: `feature/5001-subscription-plans`

**Labels**: user-story, monetization, stripe

#### Story #5002: As a listener, I want to subscribe to a plan so that I can access premium content

**Points**: 8 | **Type**: feature | **Status**: unstarted

Implement POST /v1/monetization/subscriptions endpoint that creates Stripe customer (if not exists), attaches payment method, creates Stripe subscription, stores subscription record with stripe_subscription_id, and handles webhook events for status updates.

**Acceptance Criteria**:
1. Given a listener with valid payment method, When they subscribe to plan, Then Stripe subscription is created and subscription record is stored
2. Given a listener with invalid payment method, When they subscribe, Then a 402 Payment Required error is returned
3. Given a Stripe webhook event 'invoice.payment_succeeded', When received, Then subscription status is updated to 'active'
4. Given a Stripe webhook event 'customer.subscription.deleted', When received, Then subscription status is updated to 'canceled'

**TDD Workflow**:
- 🔴 RED: `WIP: red tests for subscription creation and webhook handling`
- 🟢 GREEN: `green: subscription endpoint with Stripe integration and webhooks`
- 🔵 REFACTOR: `refactor: extract webhook handling and improve subscription management`

**Branch**: `feature/5002-subscription-management`

**Labels**: user-story, monetization, stripe, webhooks

#### Story #5003: As a DJ, I want to insert ads into my live streams so that I can generate ad revenue

**Points**: 8 | **Type**: feature | **Status**: unstarted

Implement POST /v1/streams/{stream_id}/ads endpoint that configures ad markers (position_seconds, duration_seconds, ad_type), stores in database, and instructs Relay Worker to insert ads at specified positions by switching FFmpeg input to ad source.

**Acceptance Criteria**:
1. Given a DJ with valid ad markers, When they configure ads, Then ad markers are stored and Relay Worker is notified
2. Given a live stream with pre-roll ad at 300s, When stream reaches 300s, Then ad segment is inserted before resuming main feed
3. Given a live stream with mid-roll ad at 1800s, When stream reaches 1800s, Then ad segment is inserted
4. Given an ad insertion, When completed, Then Relay Worker logs ad event and resumes main feed

**TDD Workflow**:
- 🔴 RED: `WIP: red tests for ad insertion configuration`
- 🟢 GREEN: `green: ad insertion endpoint with relay worker integration`
- 🔵 REFACTOR: `refactor: extract ad scheduling utilities and improve relay worker communication`

**Branch**: `feature/5003-ad-insertion`

**Labels**: user-story, monetization, ads, live-streaming


## Epic #6000: Analytics & Reporting

Implement analytics collection for live streams, playlists, and podcasts with date-range queries, CSV/JSON export, and visual dashboard integration

### User Stories

#### Story #6001: As a DJ, I want to view live stream analytics so that I can monitor listener engagement

**Points**: 5 | **Type**: feature | **Status**: unstarted

Implement GET /v1/analytics/streams/{stream_id} endpoint that aggregates analytics table for daily_listeners, peak_listeners, and total_listens over date range, supports from/to query parameters, and returns JSON with daily breakdown.

**Acceptance Criteria**:
1. Given a DJ with live stream, When they request analytics with date range, Then daily listener counts are returned
2. Given a DJ with invalid date range, When they request analytics, Then a 400 Bad Request error is returned
3. Given a stream with no analytics data, When DJ requests analytics, Then empty arrays are returned with zero totals
4. Given analytics data older than 2 years, When queried, Then data is anonymized per GDPR requirements

**TDD Workflow**:
- 🔴 RED: `WIP: red tests for live stream analytics`
- 🟢 GREEN: `green: stream analytics endpoint with date range aggregation`
- 🔵 REFACTOR: `refactor: extract analytics aggregation utilities and add caching`

**Branch**: `feature/6001-stream-analytics`

**Labels**: user-story, analytics, reporting

#### Story #6002: As a DJ, I want to view playlist analytics so that I can track play counts

**Points**: 3 | **Type**: feature | **Status**: unstarted

Implement GET /v1/analytics/playlists/{playlist_id} endpoint that aggregates analytics table for daily play_count and total_plays over date range, supports from/to query parameters, and returns JSON with daily breakdown.

**Acceptance Criteria**:
1. Given a DJ with playlist, When they request analytics with date range, Then daily play counts are returned
2. Given a DJ with invalid date range, When they request analytics, Then a 400 Bad Request error is returned
3. Given a playlist with no analytics data, When DJ requests analytics, Then empty arrays are returned with zero totals
4. Given analytics data, When exported, Then CSV format is available with date, play_count columns

**TDD Workflow**:
- 🔴 RED: `WIP: red tests for playlist analytics`
- 🟢 GREEN: `green: playlist analytics endpoint with date range aggregation`
- 🔵 REFACTOR: `refactor: extract analytics utilities and add CSV export`

**Branch**: `feature/6002-playlist-analytics`

**Labels**: user-story, analytics, reporting

#### Story #6003: As a DJ, I want to view podcast analytics so that I can track episode downloads

**Points**: 3 | **Type**: feature | **Status**: unstarted

Implement GET /v1/analytics/podcasts/{podcast_id} endpoint that aggregates analytics table for daily_downloads and total_downloads over date range, supports from/to query parameters, and returns JSON with daily breakdown.

**Acceptance Criteria**:
1. Given a DJ with podcast, When they request analytics with date range, Then daily download counts are returned
2. Given a DJ with invalid date range, When they request analytics, Then a 400 Bad Request error is returned
3. Given a podcast with no analytics data, When DJ requests analytics, Then empty arrays are returned with zero totals
4. Given analytics data, When exported, Then JSON format includes episode-level breakdown

**TDD Workflow**:
- 🔴 RED: `WIP: red tests for podcast analytics`
- 🟢 GREEN: `green: podcast analytics endpoint with date range aggregation`
- 🔵 REFACTOR: `refactor: extract analytics utilities and improve episode-level reporting`

**Branch**: `feature/6003-podcast-analytics`

**Labels**: user-story, analytics, reporting

