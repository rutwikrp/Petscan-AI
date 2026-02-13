# PetScan AI - Requirements Document

**Version**: 1.0  
**Date**: February 13, 2026  
**Status**: MVP Requirements

## Table of Contents
1. [Functional Requirements](#functional-requirements)
2. [Non-Functional Requirements](#non-functional-requirements)
3. [User Stories](#user-stories)
4. [Use Cases](#use-cases)
5. [Acceptance Criteria](#acceptance-criteria)
6. [Constraints and Assumptions](#constraints-and-assumptions)

---

## 1. Functional Requirements

### 1.1 User Management

#### FR-UM-001: User Registration
**Priority**: Must Have  
**Description**: Users shall be able to register using phone number, email, or Google account.

**Requirements**:
- Support phone number registration with OTP verification
- Support email registration with password (min 8 characters)
- Support Google OAuth sign-in
- Validate email format and phone number format
- Check for duplicate accounts
- Send welcome email/SMS upon successful registration

#### FR-UM-002: User Authentication
**Priority**: Must Have  
**Description**: Users shall be able to securely log in and log out.

**Requirements**:
- Support login via phone/email/Google
- Implement JWT-based session management
- Token expiry: 1 hour (access), 30 days (refresh)
- Auto-logout after 30 days of inactivity
- "Remember me" option for convenience

#### FR-UM-003: Profile Management
**Priority**: Must Have  
**Description**: Users shall be able to view and edit their profile information.

**Requirements**:
- Display user name, email, phone, profile picture
- Allow editing of name and profile picture
- Allow changing password (with current password verification)
- Allow changing preferred language (English, Hindi, Marathi)
- Display subscription tier and scans remaining

#### FR-UM-004: Account Deletion
**Priority**: Must Have  
**Description**: Users shall be able to delete their account and all associated data.

**Requirements**:
- Provide "Delete Account" option in settings
- Show confirmation dialog with consequences
- Require password re-authentication
- Delete all user data (profile, pets, scans, results)
- Send confirmation email after deletion
- Irreversible action with 30-day grace period

### 1.2 Pet Profile Management

#### FR-PM-001: Create Pet Profile
**Priority**: Must Have  
**Description**: Users shall be able to create profiles for their pets.

**Requirements**:
- Required fields: Pet name, species (dog/cat)
- Optional fields: Breed, gender, date of birth, weight, photo
- Support for indie/mixed breeds
- Validate data types and ranges
- Upload pet photo (max 5MB, JPEG/PNG)
- Allow multiple pets per user (max 10)

#### FR-PM-002: View Pet Profiles
**Priority**: Must Have  
**Description**: Users shall be able to view all their pet profiles.

**Requirements**:
- Display pet list on home screen
- Show pet photo, name, species, age
- Show recent scan count and last scan date
- Support horizontal carousel view
- Tap to view detailed profile

#### FR-PM-003: Edit Pet Profile
**Priority**: Must Have  
**Description**: Users shall be able to update pet information.

**Requirements**:
- Allow editing all pet fields
- Update pet photo
- Show last updated timestamp
- Validate changes before saving

#### FR-PM-004: Delete Pet Profile
**Priority**: Must Have  
**Description**: Users shall be able to remove pet profiles.

**Requirements**:
- Show confirmation dialog
- Warn about deletion of associated scans
- Soft delete with 7-day recovery period
- Permanently delete after 7 days

### 1.3 Scan Capture

#### FR-SC-001: Body Part Selection
**Priority**: Must Have  
**Description**: Users shall select which body part to scan.

**Requirements**:
- Support 6 body parts: Eyes, Skin, Teeth, Ears, Paws, Gait
- Display visual icons for each body part
- Show brief description of what to capture
- Provide example images for guidance

#### FR-SC-002: Camera Capture
**Priority**: Must Have  
**Description**: Users shall capture photos using the device camera.

**Requirements**:
- Open native camera interface
- Display overlay guide for selected body part
- Show real-time instructions (e.g., "Move closer", "Hold steady")
- Support flash toggle
- Capture button with haptic feedback
- Preview captured image before submission

#### FR-SC-003: Gallery Upload
**Priority**: Should Have  
**Description**: Users shall be able to upload existing photos from gallery.

**Requirements**:
- Access device photo gallery
- Filter by image files only
- Preview selected image
- Validate image quality before upload

#### FR-SC-004: Video Capture
**Priority**: Should Have  
**Description**: Users shall capture short videos for gait analysis.

**Requirements**:
- Record 5-10 second videos
- Show recording timer
- Auto-stop at 10 seconds
- Preview video before submission
- Max file size: 50MB

#### FR-SC-005: Image Quality Validation
**Priority**: Must Have  
**Description**: System shall validate image quality before processing.

**Requirements**:
- Check for blur (reject if blur score >70%)
- Check brightness (reject if too dark/bright)
- Check resolution (min 640x480)
- Check file format (JPEG, PNG only)
- Provide feedback and allow retry

### 1.4 AI Analysis

#### FR-AI-001: Image Processing
**Priority**: Must Have  
**Description**: System shall process uploaded images using AI models.

**Requirements**:
- Send image to AI service within 2 seconds of upload
- Display processing status with progress indicator
- Estimated time: 5-10 seconds
- Handle API failures gracefully with retry (max 3 attempts)
- Timeout after 30 seconds

#### FR-AI-002: Health Issue Detection
**Priority**: Must Have  
**Description**: AI shall detect common health issues from images.

**Requirements**:
- Detect 8-12 predefined health conditions
- Return confidence score (0-100%) for each detection
- Assign severity level (mild, moderate, urgent)
- Provide brief description of detected issue
- Include bounding box coordinates for visual annotation

#### FR-AI-003: Multi-Issue Detection
**Priority**: Must Have  
**Description**: System shall detect multiple issues in a single scan.

**Requirements**:
- Support up to 5 simultaneous detections
- Rank by severity and confidence
- Filter out low-confidence detections (<60%)
- Highlight most critical issue

#### FR-AI-004: Breed-Specific Analysis
**Priority**: Should Have  
**Description**: AI shall consider breed-specific characteristics.

**Requirements**:
- Adjust detection thresholds for breed-specific traits
- Account for normal variations (e.g., wrinkly skin in Pugs)
- Provide breed-specific health tips

### 1.5 Results and Reporting

#### FR-RR-001: Display Scan Results
**Priority**: Must Have  
**Description**: Users shall view detailed scan results.

**Requirements**:
- Show annotated image with bounding boxes
- List all detections with confidence bars
- Display severity badges (color-coded)
- Show overall health score
- Include analysis timestamp and model version

#### FR-RR-002: Recommendations
**Priority**: Must Have  
**Description**: System shall provide actionable recommendations.

**Requirements**:
- Generate 2-5 recommendations per scan
- Categorize: Monitor, Home Care, Vet Visit
- Provide specific action items
- Include timeline (e.g., "Monitor for 48 hours")
- Avoid medical advice or prescriptions

#### FR-RR-003: Disclaimer Display
**Priority**: Must Have  
**Description**: Every report shall include a medical disclaimer.

**Requirements**:
- Display prominent disclaimer on results screen
- Text: "This is not a medical diagnosis. Consult a veterinarian for professional advice."
- Require acknowledgment before sharing
- Include in PDF exports

#### FR-RR-004: Report Sharing
**Priority**: Should Have  
**Description**: Users shall be able to share scan reports.

**Requirements**:
- Generate shareable link (expires in 7 days)
- Export as PDF with all details
- Share via email, WhatsApp, SMS
- Include QR code for easy access

#### FR-RR-005: Report Export
**Priority**: Should Have  
**Description**: Users shall export reports as PDF.

**Requirements**:
- Include pet info, scan details, detections, recommendations
- Professional formatting with branding
- Max file size: 2MB
- Save to device storage

### 1.6 Scan History

#### FR-SH-001: View Scan History
**Priority**: Must Have  
**Description**: Users shall view all past scans.

**Requirements**:
- Display scans in reverse chronological order
- Show thumbnail, date, body part, severity
- Paginate (20 scans per page)
- Pull-to-refresh functionality

#### FR-SH-002: Filter and Search
**Priority**: Should Have  
**Description**: Users shall filter scan history.

**Requirements**:
- Filter by pet
- Filter by body part
- Filter by date range
- Filter by severity level
- Search by keywords

#### FR-SH-003: Trend Analysis
**Priority**: Should Have  
**Description**: Users shall view health trends over time.

**Requirements**:
- Display line chart of severity scores
- Show frequency of issues by type
- Compare scans for same body part
- Highlight improvements or deteriorations

#### FR-SH-004: Delete Scan
**Priority**: Must Have  
**Description**: Users shall delete individual scans.

**Requirements**:
- Show confirmation dialog
- Delete scan and associated media
- Remove from history immediately
- Cannot be undone

### 1.7 Subscription and Monetization

#### FR-SM-001: Free Tier Limits
**Priority**: Must Have  
**Description**: Free users shall have limited scans per month.

**Requirements**:
- Limit: 3-5 scans per month
- Reset on 1st of each month
- Display remaining scans on home screen
- Show upgrade prompt when limit reached

#### FR-SM-002: Premium Subscription
**Priority**: Must Have  
**Description**: Users shall be able to purchase premium subscription.

**Requirements**:
- Monthly plan: ₹199/month
- Annual plan: ₹1,999/year (save 17%)
- Unlimited scans
- Advanced features (trends, priority support)
- Process payment via Google Play/App Store

#### FR-SM-003: Subscription Management
**Priority**: Must Have  
**Description**: Users shall manage their subscription.

**Requirements**:
- View current plan and expiry date
- Upgrade/downgrade plans
- Cancel subscription (effective at period end)
- View payment history
- Restore purchases on new device

#### FR-SM-004: Trial Period
**Priority**: Should Have  
**Description**: New users shall receive a trial period.

**Requirements**:
- 7-day free trial for premium features
- No credit card required
- Auto-convert to free tier after trial
- One-time offer per user

### 1.8 Privacy and Consent

#### FR-PC-001: Privacy Consent
**Priority**: Must Have  
**Description**: Users shall provide explicit consent for data usage.

**Requirements**:
- Show consent screen on first launch
- Explain data collection and usage
- Separate consent for AI training data
- Allow opt-out of non-essential data collection
- Store consent timestamp

#### FR-PC-002: Data Access
**Priority**: Must Have  
**Description**: Users shall view all stored data.

**Requirements**:
- Provide "Download My Data" option
- Export as JSON file
- Include all user data, pet profiles, scans, results
- Generate within 24 hours

#### FR-PC-003: Data Deletion
**Priority**: Must Have  
**Description**: Users shall request data deletion.

**Requirements**:
- Delete all personal data
- Anonymize data used in AI training
- Complete within 30 days
- Send confirmation email

### 1.9 Notifications

#### FR-NT-001: Push Notifications
**Priority**: Should Have  
**Description**: Users shall receive relevant push notifications.

**Requirements**:
- Scan complete notification
- Urgent severity alert
- Subscription expiry reminder (7 days before)
- Health tips (weekly, opt-in)
- Allow notification preferences

#### FR-NT-002: In-App Notifications
**Priority**: Should Have  
**Description**: Users shall see in-app notifications.

**Requirements**:
- New feature announcements
- Maintenance alerts
- Promotional offers
- Dismissible notifications

### 1.10 Localization

#### FR-LC-001: Multi-Language Support
**Priority**: Must Have  
**Description**: App shall support multiple languages.

**Requirements**:
- English (default)
- Hindi
- Marathi
- Auto-detect device language
- Allow manual language selection
- Translate all UI text and notifications

#### FR-LC-002: Regional Customization
**Priority**: Should Have  
**Description**: App shall adapt to regional preferences.

**Requirements**:
- Currency: INR (₹)
- Date format: DD/MM/YYYY
- Time format: 12-hour
- Breed database: Include Indian breeds (Indie, Rajapalayam, etc.)

---

## 2. Non-Functional Requirements

### 2.1 Performance Requirements

#### NFR-PF-001: Response Time
**Priority**: Must Have

**Requirements**:
- App launch: <2 seconds (cold start)
- Screen transitions: <300ms
- API calls: <500ms (p95)
- AI analysis: <10 seconds (p95)
- Image upload: <5 seconds for 5MB file

#### NFR-PF-002: Scalability
**Priority**: Must Have

**Requirements**:
- Support 10,000 concurrent users (MVP)
- Handle 50,000 scans per day
- Auto-scale backend services
- Database query optimization for <100ms response

#### NFR-PF-003: Resource Usage
**Priority**: Must Have

**Requirements**:
- App size: <50MB (Android), <60MB (iOS)
- RAM usage: <150MB during normal operation
- Battery drain: <5% per hour of active use
- Network usage: <10MB per scan (excluding image upload)

### 2.2 Reliability Requirements

#### NFR-RL-001: Availability
**Priority**: Must Have

**Requirements**:
- System uptime: 99% (excluding planned maintenance)
- Planned maintenance: <4 hours per month
- Advance notice: 48 hours for maintenance
- Graceful degradation during partial outages

#### NFR-RL-002: Data Integrity
**Priority**: Must Have

**Requirements**:
- Zero data loss for completed scans
- Automatic backup: Daily
- Backup retention: 30 days
- Point-in-time recovery: 7 days

#### NFR-RL-003: Error Handling
**Priority**: Must Have

**Requirements**:
- Graceful error messages (no technical jargon)
- Automatic retry for transient failures
- Offline mode for viewing history
- Error logging for debugging

### 2.3 Security Requirements

#### NFR-SC-001: Authentication Security
**Priority**: Must Have

**Requirements**:
- Password hashing: bcrypt with salt
- JWT token encryption
- Secure token storage (Keychain/Keystore)
- Session timeout: 1 hour
- Multi-device login support

#### NFR-SC-002: Data Encryption
**Priority**: Must Have

**Requirements**:
- TLS 1.3 for all network communication
- End-to-end encryption for sensitive data
- Encrypted storage for local data
- Secure key management

#### NFR-SC-003: API Security
**Priority**: Must Have

**Requirements**:
- Rate limiting: 100 requests/minute per user
- Input validation and sanitization
- SQL injection prevention
- XSS protection
- CSRF protection

#### NFR-SC-004: Privacy Protection
**Priority**: Must Have

**Requirements**:
- No PII in logs or analytics
- Anonymized data for AI training
- Secure data deletion (overwrite, not just flag)
- Compliance with Indian IT Act

### 2.4 Usability Requirements

#### NFR-US-001: User Interface
**Priority**: Must Have

**Requirements**:
- Intuitive navigation (95% task completion rate)
- Consistent design language (Material Design/iOS HIG)
- Touch targets: Min 48x48 dp
- Readable text: Min 14sp for body text
- High contrast ratios (WCAG AA)

#### NFR-US-002: Accessibility
**Priority**: Should Have

**Requirements**:
- Screen reader support (TalkBack/VoiceOver)
- Adjustable font sizes
- Color-blind friendly palette
- Keyboard navigation support
- Alt text for images

#### NFR-US-003: Onboarding
**Priority**: Must Have

**Requirements**:
- First-time user tutorial (<2 minutes)
- Contextual help tooltips
- Sample scan walkthrough
- Skip option for experienced users

### 2.5 Compatibility Requirements

#### NFR-CP-001: Platform Support
**Priority**: Must Have

**Requirements**:
- Android: 8.0 (API 26) and above
- iOS: 13.0 and above
- Support 95% of devices in target market

#### NFR-CP-002: Network Compatibility
**Priority**: Must Have

**Requirements**:
- Work on 3G, 4G, 5G, WiFi
- Adaptive quality based on connection speed
- Offline mode for basic features
- Resume interrupted uploads

#### NFR-CP-003: Camera Compatibility
**Priority**: Must Have

**Requirements**:
- Support rear camera (primary)
- Min resolution: 8MP
- Auto-focus support
- Flash support (optional)

### 2.6 Maintainability Requirements

#### NFR-MT-001: Code Quality
**Priority**: Must Have

**Requirements**:
- Code coverage: >80%
- Linting: Zero critical issues
- Documentation: All public APIs
- Modular architecture (MVVM)

#### NFR-MT-002: Monitoring
**Priority**: Must Have

**Requirements**:
- Crash reporting (Firebase Crashlytics)
- Performance monitoring
- User analytics (Firebase Analytics)
- Custom event tracking

#### NFR-MT-003: Updates
**Priority**: Must Have

**Requirements**:
- Over-the-air updates for minor changes
- Force update for critical bugs
- Backward compatibility: 2 versions
- Release notes for each update

### 2.7 Compliance Requirements

#### NFR-CM-001: Legal Compliance
**Priority**: Must Have

**Requirements**:
- Comply with Indian IT Act 2000
- Terms of Service and Privacy Policy
- Age restriction: 18+ (or parental consent)
- No medical claims (avoid regulatory issues)

#### NFR-CM-002: Data Residency
**Priority**: Should Have

**Requirements**:
- Store Indian user data in Indian servers
- Comply with data localization norms
- Cross-border data transfer agreements

### 2.8 Cost Requirements

#### NFR-CT-001: Operational Costs
**Priority**: Must Have

**Requirements**:
- AI cost: <₹2 per scan
- Infrastructure: <₹10,000/month for 5,000 users
- Total operational cost: <₹50,000/month (MVP)

#### NFR-CT-002: Development Costs
**Priority**: Must Have

**Requirements**:
- MVP development: ₹8-18 lakhs
- Timeline: 3-4 months
- Team size: 4-6 people

---

## 3. User Stories

### Epic 1: User Onboarding

**US-001**: As a new user, I want to register quickly so I can start using the app.
- Acceptance: Register in <2 minutes using phone/email/Google

**US-002**: As a user, I want to create my pet's profile so the app knows about my pet.
- Acceptance: Create profile with name, species, breed in <1 minute

**US-003**: As a first-time user, I want a tutorial so I understand how to use the app.
- Acceptance: Complete tutorial in <2 minutes, with skip option

### Epic 2: Health Scanning

**US-004**: As a pet owner, I want to scan my dog's eyes so I can check for issues.
- Acceptance: Capture photo with guidance, receive results in <15 seconds

**US-005**: As a user, I want clear instructions so I take good quality photos.
- Acceptance: See overlay guide and real-time feedback during capture

**US-006**: As a user, I want to know if my photo is good enough before submitting.
- Acceptance: Get instant quality feedback with option to retake

**US-007**: As a user, I want to see what health issues were detected.
- Acceptance: View annotated image with detections, confidence, and severity

**US-008**: As a user, I want recommendations on what to do next.
- Acceptance: See 2-5 actionable recommendations based on severity

### Epic 3: History and Tracking

**US-009**: As a user, I want to see all my past scans so I can track my pet's health.
- Acceptance: View chronological list with thumbnails and key info

**US-010**: As a user, I want to see health trends over time.
- Acceptance: View charts showing severity trends for each body part

**US-011**: As a user, I want to filter scans by pet or body part.
- Acceptance: Apply filters and see filtered results instantly

### Epic 4: Subscription

**US-012**: As a free user, I want to know how many scans I have left.
- Acceptance: See scan count on home screen, updated after each scan

**US-013**: As a user, I want to upgrade to premium for unlimited scans.
- Acceptance: Purchase premium in <3 taps, instant activation

**US-014**: As a premium user, I want to access advanced features.
- Acceptance: Unlock trends, priority support, and unlimited scans

### Epic 5: Privacy and Control

**US-015**: As a user, I want to control how my data is used.
- Acceptance: Opt-in/out of AI training, see clear privacy policy

**US-016**: As a user, I want to download all my data.
- Acceptance: Request data export, receive JSON file within 24 hours

**US-017**: As a user, I want to delete my account and all data.
- Acceptance: Delete account with confirmation, data removed in 30 days

---

## 4. Use Cases

### UC-001: New User Performs First Scan

**Actor**: New Pet Owner  
**Precondition**: User has installed the app  
**Trigger**: User opens app for the first time

**Main Flow**:
1. User sees onboarding screens
2. User registers with phone number
3. User verifies OTP
4. User creates pet profile (name: "Bruno", species: Dog, breed: Indie)
5. User sees home screen with "Start Scan" button
6. User taps "Start Scan"
7. User selects pet (Bruno)
8. User selects body part (Eyes)
9. Camera opens with eye overlay guide
10. User captures photo of Bruno's eyes
11. User reviews photo and confirms
12. App uploads and processes image (8 seconds)
13. User sees results: "Eye Redness detected (82% confidence, Moderate severity)"
14. User reads recommendations: "Monitor for 48 hours, clean gently"
15. User taps "Done" and returns to home

**Postcondition**: Scan saved in history, scan count decremented

**Alternative Flows**:
- 10a. Photo is blurry → System prompts retry
- 12a. Network error → System retries 3 times, then shows error
- 13a. No issues detected → System shows "No issues found, pet looks healthy"

### UC-002: Premium User Views Health Trends

**Actor**: Premium Subscriber  
**Precondition**: User has performed 5+ scans over 2 months  
**Trigger**: User taps "View Trends" on pet profile

**Main Flow**:
1. User navigates to pet profile (Bella)
2. User taps "Health Trends"
3. System displays line chart of severity scores over time
4. User sees skin issues decreasing from "Moderate" to "Mild"
5. User taps on data point to see scan details
6. User views specific scan from 1 month ago
7. User compares with latest scan
8. User sees improvement notification
9. User shares trend chart with vet via WhatsApp

**Postcondition**: User has insights into pet's health progress

### UC-003: Free User Reaches Scan Limit

**Actor**: Free Tier User  
**Precondition**: User has used 5/5 scans this month  
**Trigger**: User attempts to start a new scan

**Main Flow**:
1. User taps "Start Scan"
2. System shows "Scan limit reached" dialog
3. Dialog shows: "You've used all 5 scans this month"
4. Dialog offers: "Upgrade to Premium for unlimited scans"
5. User taps "View Plans"
6. System shows pricing: Monthly ₹199, Annual ₹1,999
7. User selects Annual plan
8. System redirects to Google Play payment
9. User completes payment
10. System activates premium subscription
11. User returns to app, sees "Premium" badge
12. User successfully starts new scan

**Postcondition**: User is premium subscriber with unlimited scans

**Alternative Flows**:
- 5a. User taps "Not Now" → Returns to home, can try again next month
- 9a. Payment fails → System shows error, allows retry

### UC-004: User Shares Scan Report with Vet

**Actor**: Pet Owner  
**Precondition**: User has completed scan with urgent severity  
**Trigger**: User wants to consult vet

**Main Flow**:
1. User views scan result showing "Ear Infection (Urgent)"
2. User taps "Share Report"
3. System generates PDF report (2 seconds)
4. User selects "Email"
5. User enters vet's email: vet@example.com
6. System sends email with PDF attachment
7. User receives confirmation: "Report sent successfully"
8. Vet receives email with report and disclaimer

**Postcondition**: Vet has access to scan report for consultation

---

## 5. Acceptance Criteria

### AC-001: User Registration
- User can register with phone, email, or Google in <2 minutes
- OTP is received within 30 seconds
- Duplicate accounts are prevented
- Welcome message is displayed after registration

### AC-002: Pet Profile Creation
- User can create pet profile with required fields in <1 minute
- Pet photo upload works for JPEG/PNG up to 5MB
- User can create up to 10 pet profiles
- Profile is saved and visible on home screen

### AC-003: Scan Capture
- Camera opens within 1 second
- Overlay guide is displayed for selected body part
- Photo quality validation works (blur detection)
- User can retake photo if quality is poor
- Captured photo is uploaded within 5 seconds

### AC-004: AI Analysis
- Analysis completes within 10 seconds for 95% of scans
- Detections have confidence scores between 60-100%
- Severity is correctly assigned (mild/moderate/urgent)
- Annotated image shows bounding boxes
- Results are saved in database

### AC-005: Results Display
- Results screen shows all detections clearly
- Confidence is displayed as percentage and visual bar
- Severity is color-coded (yellow/orange/red)
- Recommendations are actionable and specific
- Disclaimer is prominently displayed

### AC-006: Scan History
- All scans are displayed in reverse chronological order
- Pagination works (20 items per page)
- Filters work correctly (pet, body part, date)
- Tapping a scan opens full results
- Delete scan removes it from history

### AC-007: Subscription
- Free users are limited to 5 scans/month
- Scan count resets on 1st of each month
- Premium purchase flow completes in <3 taps
- Premium features are unlocked immediately
- Subscription status is displayed correctly

### AC-008: Performance
- App launches in <2 seconds (cold start)
- Screen transitions are smooth (<300ms)
- No crashes during normal operation
- App uses <150MB RAM
- Battery drain is <5% per hour

### AC-009: Security
- All API calls use HTTPS
- Passwords are hashed (not stored in plain text)
- JWT tokens expire after 1 hour
- User data is encrypted at rest
- Account deletion removes all data

### AC-010: Localization
- App detects device language and switches automatically
- All UI text is translated (English, Hindi, Marathi)
- Currency is displayed as ₹ (INR)
- Date format is DD/MM/YYYY

---

## 6. Constraints and Assumptions

### 6.1 Constraints

**Technical Constraints**:
- Must use Flutter for cross-platform development
- Must use Firebase or AWS for backend
- AI processing must be cloud-based (no on-device for MVP)
- Must support Android 8.0+ and iOS 13.0+
- Must work on devices with 8MP+ camera

**Business Constraints**:
- MVP budget: ₹8-18 lakhs
- Development timeline: 3-4 months
- Operational cost: <₹50,000/month
- AI cost: <₹2 per scan
- Initial launch: Mumbai only

**Regulatory Constraints**:
- Cannot make medical claims
- Must comply with Indian IT Act 2000
- Must have clear disclaimer on all reports
- Cannot prescribe treatments
- Must obtain user consent for data usage

**Resource Constraints**:
- Team size: 4-6 people
- AI training data: Limited labeled pet images
- Testing devices: 3-4 Android, 2-3 iOS devices
- Beta testers: 100 users for MVP

### 6.2 Assumptions

**User Assumptions**:
- Users have smartphones with 8MP+ camera
- Users have internet access (3G or better)
- Users can read English, Hindi, or Marathi
- Users are willing to pay ₹199/month for premium
- Users will follow photo capture guidelines

**Technical Assumptions**:
- Google Gemini API will be available and affordable
- Firebase can scale to 10,000 users
- AI models will achieve 80-90% accuracy
- Image upload will work on 3G networks
- Cloud storage costs will remain predictable

**Business Assumptions**:
- Pet owners in Mumbai will adopt the app
- Freemium model will drive conversions (5-10%)
- Vet partnerships will be possible (future)
- Market size is sufficient (500K+ pet owners in Mumbai)
- Competition will not significantly impact adoption

**Data Assumptions**:
- Sufficient pet image datasets are available for training
- Indian breed data can be collected or sourced
- User-generated data will improve AI over time
- Privacy concerns will not hinder adoption
- Data localization requirements will not increase costs significantly

---

## 7. Out of Scope (MVP)

The following features are explicitly out of scope for the MVP and will be considered for future releases:

**Features**:
- Wearable device integration
- Real-time video streaming analysis
- Multi-pet household management
- Vet consultation booking
- Telemedicine integration
- Community forums
- Pet health records (PHR)
- Nutrition recommendations
- Activity tracking
- Medication reminders
- Support for animals other than dogs and cats

**Platforms**:
- Web application
- Desktop application
- Smart TV app
- Wearable app (Apple Watch, etc.)

**Regions**:
- International markets (focus on India only)
- Rural areas (focus on urban Mumbai)

**Advanced Features**:
- On-device ML (offline mode)
- 3D body scanning
- Genetic health predictions
- Insurance integration
- Blockchain-based health records

---

## 8. Success Metrics

### 8.1 User Acquisition
- 1,000 downloads in first month
- 5,000 downloads in first quarter
- 50% organic growth rate

### 8.2 User Engagement
- 60% of users complete first scan within 24 hours
- 40% of users perform 2+ scans in first month
- 30% monthly active users (MAU)
- Average 3 scans per active user per month

### 8.3 Conversion
- 5-10% free to premium conversion rate
- 70% trial to paid conversion rate
- <5% monthly churn rate

### 8.4 Technical Performance
- 99% uptime
- <2% crash rate
- <10 seconds average AI processing time
- 4.0+ app store rating

### 8.5 Business Metrics
- Break-even: 500 premium subscribers
- Target: 1,000 premium subscribers in 6 months
- Customer acquisition cost (CAC): <₹500
- Lifetime value (LTV): >₹2,000

---

**Document Approval**:
- Product Manager: _______________
- Engineering Lead: _______________
- Design Lead: _______________
- QA Lead: _______________

**Next Steps**:
1. Review and approve requirements
2. Create detailed technical specifications
3. Design UI/UX mockups
4. Set up development environment
5. Begin sprint planning

**Document Revision History**:
- v1.0 (Feb 13, 2026): Initial requirements document for MVP
