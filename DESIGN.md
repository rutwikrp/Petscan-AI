# PetScan AI - Design Document

**Version**: 1.0  
**Date**: February 13, 2026  
**Status**: MVP Design Phase

## Table of Contents
1. [System Architecture](#system-architecture)
2. [Technology Stack](#technology-stack)
3. [Data Models](#data-models)
4. [API Design](#api-design)
5. [AI/ML Architecture](#aiml-architecture)
6. [User Interface Design](#user-interface-design)
7. [Security Architecture](#security-architecture)
8. [Performance Considerations](#performance-considerations)
9. [Deployment Strategy](#deployment-strategy)

---

## 1. System Architecture

### 1.1 High-Level Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                     Mobile Application                       │
│                        (Flutter)                             │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐      │
│  │   UI Layer   │  │ Business     │  │   Data       │      │
│  │  (Widgets)   │──│   Logic      │──│   Layer      │      │
│  └──────────────┘  └──────────────┘  └──────────────┘      │
└─────────────────────────────────────────────────────────────┘
                            │
                            │ HTTPS/REST API
                            ▼
┌─────────────────────────────────────────────────────────────┐
│                      Backend Services                        │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐      │
│  │   API        │  │  Auth        │  │  Storage     │      │
│  │  Gateway     │──│  Service     │  │  Service     │      │
│  └──────────────┘  └──────────────┘  └──────────────┘      │
│                                                              │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐      │
│  │   AI/ML      │  │  Analytics   │  │  Notification│      │
│  │  Service     │  │  Service     │  │  Service     │      │
│  └──────────────┘  └──────────────┘  └──────────────┘      │
└─────────────────────────────────────────────────────────────┘
                            │
                            ▼
┌─────────────────────────────────────────────────────────────┐
│                    Data & AI Layer                           │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐      │
│  │  Firebase/   │  │  Cloud       │  │  AI Models   │      │
│  │  Firestore   │  │  Storage     │  │  (Gemini)    │      │
│  └──────────────┘  └──────────────┘  └──────────────┘      │
└─────────────────────────────────────────────────────────────┘
```

### 1.2 Architecture Patterns

- **Frontend**: MVVM (Model-View-ViewModel) pattern with Flutter
- **Backend**: Microservices architecture with serverless functions
- **Data Flow**: Unidirectional data flow with state management (Provider/Riverpod)
- **Communication**: RESTful APIs with JSON payloads

---

## 2. Technology Stack

### 2.1 Mobile Application

**Framework**: Flutter 3.x
- **Language**: Dart 3.x
- **State Management**: Provider or Riverpod
- **Navigation**: GoRouter
- **Local Storage**: Hive or SQLite
- **Image Processing**: image_picker, camera plugins
- **HTTP Client**: dio or http package

**Key Dependencies**:
```yaml
dependencies:
  flutter: sdk: flutter
  provider: ^6.0.0
  dio: ^5.0.0
  image_picker: ^1.0.0
  camera: ^0.10.0
  firebase_core: ^2.0.0
  firebase_auth: ^4.0.0
  cloud_firestore: ^4.0.0
  firebase_storage: ^11.0.0
  google_sign_in: ^6.0.0
  pdf: ^3.10.0
  fl_chart: ^0.60.0
  cached_network_image: ^3.2.0
```

### 2.2 Backend Services

**Primary Stack**:
- **Cloud Platform**: Firebase (MVP) or AWS
- **Authentication**: Firebase Auth
- **Database**: Cloud Firestore (NoSQL)
- **Storage**: Firebase Storage or AWS S3
- **Functions**: Cloud Functions (Node.js/Python)
- **AI/ML**: Google Gemini Vision API or Hugging Face

**Alternative Stack** (for scaling):
- **API Gateway**: AWS API Gateway
- **Compute**: AWS Lambda
- **Database**: MongoDB Atlas or PostgreSQL (RDS)
- **Cache**: Redis

### 2.3 AI/ML Stack

- **Primary Model**: Google Gemini Vision API
- **Backup/Custom Models**: TensorFlow Lite, PyTorch Mobile
- **Training**: Hugging Face Transformers, Google Vertex AI
- **Image Processing**: OpenCV, PIL
- **Model Format**: TFLite for on-device inference (future)

---

## 3. Data Models

### 3.1 User Model

```dart
class User {
  String id;                    // Unique user ID
  String email;                 // Email address
  String? phoneNumber;          // Phone number (optional)
  String displayName;           // User's name
  String? photoUrl;             // Profile picture URL
  DateTime createdAt;           // Account creation date
  DateTime lastLoginAt;         // Last login timestamp
  String preferredLanguage;     // en, hi, mr
  SubscriptionTier tier;        // free, premium
  int scansRemaining;           // For free tier
  DateTime? subscriptionExpiry; // Premium expiry date
  bool consentGiven;            // Privacy consent
}

enum SubscriptionTier { free, premium }
```

### 3.2 Pet Model

```dart
class Pet {
  String id;                    // Unique pet ID
  String userId;                // Owner's user ID
  String name;                  // Pet's name
  Species species;              // dog, cat
  String? breed;                // Breed name (e.g., "Indie", "Labrador")
  Gender? gender;               // male, female, unknown
  DateTime? dateOfBirth;        // Birth date
  double? weight;               // Weight in kg
  String? photoUrl;             // Pet profile picture
  DateTime createdAt;           // Profile creation date
  DateTime updatedAt;           // Last update
  Map<String, dynamic>? metadata; // Additional info
}

enum Species { dog, cat }
enum Gender { male, female, unknown }
```

### 3.3 Scan Model

```dart
class Scan {
  String id;                    // Unique scan ID
  String userId;                // User who performed scan
  String petId;                 // Pet being scanned
  BodyPart bodyPart;            // Area scanned
  ScanType scanType;            // photo, video
  String mediaUrl;              // Cloud storage URL
  DateTime timestamp;           // Scan date/time
  ScanStatus status;            // pending, processing, completed, failed
  ScanResult? result;           // Analysis result
  Map<String, dynamic>? metadata; // Camera settings, etc.
}

enum BodyPart { eyes, skin, teeth, ears, paws, gait, general }
enum ScanType { photo, video }
enum ScanStatus { pending, processing, completed, failed }
```

### 3.4 Scan Result Model

```dart
class ScanResult {
  String scanId;                // Reference to scan
  List<Detection> detections;   // List of detected issues
  double overallConfidence;     // Overall confidence score
  Severity overallSeverity;     // Highest severity found
  String? annotatedImageUrl;    // Image with bounding boxes
  List<Recommendation> recommendations; // Action items
  DateTime analyzedAt;          // Analysis timestamp
  String modelVersion;          // AI model version used
}

class Detection {
  String issueType;             // e.g., "skin_allergy", "eye_redness"
  String displayName;           // User-friendly name
  double confidence;            // 0.0 to 1.0
  Severity severity;            // mild, moderate, urgent
  String description;           // Explanation
  BoundingBox? location;        // Location in image
}

class BoundingBox {
  double x, y, width, height;   // Normalized coordinates
}

enum Severity { mild, moderate, urgent }

class Recommendation {
  String title;                 // Short title
  String description;           // Detailed recommendation
  RecommendationType type;      // monitor, home_care, vet_visit
  List<String>? actionItems;    // Specific steps
}

enum RecommendationType { monitor, homeCare, vetVisit }
```

### 3.5 Database Schema (Firestore)

```
users/
  {userId}/
    - email, displayName, tier, scansRemaining, etc.
    
pets/
  {petId}/
    - userId, name, species, breed, etc.
    
scans/
  {scanId}/
    - userId, petId, bodyPart, mediaUrl, status, etc.
    
results/
  {scanId}/
    - detections[], recommendations[], etc.
    
subscriptions/
  {userId}/
    - tier, startDate, expiryDate, transactionId, etc.
```

---

## 4. API Design

### 4.1 Authentication Endpoints

```
POST   /api/v1/auth/register
POST   /api/v1/auth/login
POST   /api/v1/auth/logout
POST   /api/v1/auth/refresh-token
POST   /api/v1/auth/verify-phone
```

### 4.2 User Management

```
GET    /api/v1/users/me
PUT    /api/v1/users/me
DELETE /api/v1/users/me
PATCH  /api/v1/users/me/language
PATCH  /api/v1/users/me/consent
```

### 4.3 Pet Management

```
GET    /api/v1/pets
POST   /api/v1/pets
GET    /api/v1/pets/{petId}
PUT    /api/v1/pets/{petId}
DELETE /api/v1/pets/{petId}
```

### 4.4 Scan Operations

```
POST   /api/v1/scans                    # Create new scan
GET    /api/v1/scans/{scanId}           # Get scan details
GET    /api/v1/scans?petId={petId}      # List scans for pet
POST   /api/v1/scans/{scanId}/analyze   # Trigger AI analysis
GET    /api/v1/scans/{scanId}/result    # Get analysis result
POST   /api/v1/scans/{scanId}/share     # Generate shareable report
```

### 4.5 Media Upload

```
POST   /api/v1/media/upload-url         # Get signed upload URL
POST   /api/v1/media/validate           # Validate image quality
```

### 4.6 Subscription

```
GET    /api/v1/subscription/status
POST   /api/v1/subscription/purchase
POST   /api/v1/subscription/cancel
```

### 4.7 Example Request/Response

**POST /api/v1/scans**

Request:
```json
{
  "petId": "pet_123",
  "bodyPart": "eyes",
  "scanType": "photo",
  "mediaUrl": "https://storage.../image.jpg"
}
```

Response:
```json
{
  "success": true,
  "data": {
    "scanId": "scan_456",
    "status": "processing",
    "estimatedTime": 8,
    "message": "Analysis in progress"
  }
}
```

**GET /api/v1/scans/scan_456/result**

Response:
```json
{
  "success": true,
  "data": {
    "scanId": "scan_456",
    "detections": [
      {
        "issueType": "eye_redness",
        "displayName": "Eye Redness",
        "confidence": 0.87,
        "severity": "moderate",
        "description": "Detected redness in the left eye area",
        "location": {"x": 0.3, "y": 0.4, "width": 0.2, "height": 0.15}
      }
    ],
    "overallConfidence": 0.87,
    "overallSeverity": "moderate",
    "annotatedImageUrl": "https://storage.../annotated.jpg",
    "recommendations": [
      {
        "title": "Monitor Closely",
        "description": "Watch for increased discharge or squinting",
        "type": "monitor",
        "actionItems": ["Check twice daily", "Note any changes"]
      },
      {
        "title": "Consider Vet Visit",
        "description": "If symptoms persist for 48 hours",
        "type": "vetVisit"
      }
    ],
    "analyzedAt": "2026-02-13T10:30:00Z",
    "modelVersion": "v1.2.0"
  }
}
```

---

## 5. AI/ML Architecture

### 5.1 Model Pipeline

```
Image Upload → Preprocessing → AI Model → Post-processing → Result
```

**Preprocessing Steps**:
1. Image validation (format, size, quality)
2. Blur detection (reject if too blurry)
3. Resize/normalize (standard dimensions)
4. Brightness/contrast adjustment
5. Region of interest extraction

**AI Model Processing**:
1. Send to Google Gemini Vision API with custom prompt
2. Receive structured JSON response
3. Parse detections and confidence scores

**Post-processing**:
1. Filter low-confidence detections (<60%)
2. Apply business rules (severity mapping)
3. Generate recommendations based on detections
4. Create annotated images with bounding boxes
5. Store results in database

### 5.2 Gemini Vision API Integration

**Prompt Template**:
```
Analyze this {bodyPart} image of a {species} ({breed}) for health issues.

Detect the following conditions:
- [List of conditions for this body part]

For each detection, provide:
1. Issue type (from predefined list)
2. Confidence score (0-100)
3. Severity (mild/moderate/urgent)
4. Brief description
5. Bounding box coordinates if applicable

Return results as JSON.
```

**API Call Structure**:
```python
import google.generativeai as genai

def analyze_pet_image(image_path, body_part, species, breed):
    model = genai.GenerativeModel('gemini-1.5-pro-vision')
    
    prompt = generate_prompt(body_part, species, breed)
    image = load_image(image_path)
    
    response = model.generate_content([prompt, image])
    result = parse_response(response.text)
    
    return result
```

### 5.3 Supported Detection Categories

**Eyes** (4 conditions):
- Redness/inflammation
- Discharge (clear, yellow, green)
- Cloudiness
- Swelling

**Skin** (5 conditions):
- Allergic reactions/rashes
- Hot spots
- Fungal infections
- Parasites (fleas, ticks)
- Wounds/lesions

**Teeth** (3 conditions):
- Tartar buildup
- Gum inflammation
- Broken/damaged teeth

**Ears** (3 conditions):
- Ear mites
- Infections
- Excessive wax

**Gait** (2 conditions):
- Limping
- Mobility issues

**General** (3 conditions):
- Coat condition
- Body condition score
- Visible abnormalities

### 5.4 Model Performance Targets

- **Accuracy**: 80-90% on validation set
- **Response Time**: <10 seconds per image
- **False Positive Rate**: <15%
- **False Negative Rate**: <10% (prioritize sensitivity)

### 5.5 Future: On-Device ML

For offline capability (future phase):
- Convert models to TensorFlow Lite
- Deploy on-device for basic scans
- Sync results when online
- Target model size: <50MB

---

## 6. User Interface Design

### 6.1 Design Principles

- **Simplicity**: Minimal steps to complete a scan
- **Guidance**: Clear instructions at every step
- **Feedback**: Immediate visual/haptic feedback
- **Accessibility**: High contrast, screen reader support
- **Localization**: Support for English, Hindi, Marathi

### 6.2 Color Palette

```
Primary Colors:
- Brand Blue: #2196F3
- Brand Green: #4CAF50 (success/healthy)
- Warning Orange: #FF9800
- Alert Red: #F44336

Neutral Colors:
- Background Light: #FFFFFF
- Background Dark: #121212
- Text Primary: #212121
- Text Secondary: #757575
- Divider: #E0E0E0

Severity Colors:
- Mild: #FFF9C4 (light yellow)
- Moderate: #FFE0B2 (light orange)
- Urgent: #FFCDD2 (light red)
```

### 6.3 Typography

```
Font Family: Roboto (Android), SF Pro (iOS)

Headings:
- H1: 32sp, Bold
- H2: 24sp, Semi-bold
- H3: 20sp, Medium

Body:
- Body 1: 16sp, Regular
- Body 2: 14sp, Regular
- Caption: 12sp, Regular

Buttons:
- Button Text: 16sp, Medium, Uppercase
```

### 6.4 Screen Flow

```
Splash Screen
    ↓
Onboarding (first time)
    ↓
Login/Register
    ↓
Home Dashboard
    ├─→ Pet Profile List
    │       ├─→ Add Pet
    │       └─→ Edit Pet
    ├─→ New Scan
    │       ├─→ Select Pet
    │       ├─→ Select Body Part
    │       ├─→ Camera Capture
    │       ├─→ Review Image
    │       ├─→ Processing
    │       └─→ Results
    ├─→ Scan History
    │       └─→ View Past Result
    ├─→ Settings
    │       ├─→ Account
    │       ├─→ Language
    │       ├─→ Privacy
    │       └─→ Subscription
    └─→ Premium Upgrade
```

### 6.5 Key Screens

**Home Dashboard**:
- Pet carousel (horizontal scroll)
- Quick scan button (prominent)
- Recent scans (3-5 items)
- Health tips/alerts
- Subscription status banner (if free tier)

**Camera Capture Screen**:
- Live camera preview
- Overlay guide (e.g., eye outline for eye scans)
- Instructions text at top
- Capture button (center bottom)
- Flash toggle
- Gallery picker (bottom left)

**Results Screen**:
- Pet info header
- Annotated image (zoomable)
- Detection cards (expandable)
  - Issue name + icon
  - Confidence bar
  - Severity badge
  - Description
- Recommendations section
- Action buttons: Share, Save PDF, New Scan
- Disclaimer footer

**Scan History**:
- List view with thumbnails
- Filter by pet, date, body part
- Sort by date (newest first)
- Severity indicators
- Tap to view full result

### 6.6 UI Components

**Custom Widgets**:
- PetCard: Display pet info with avatar
- ScanCard: Scan history item
- DetectionCard: Show detection with severity
- ConfidenceBar: Visual confidence indicator
- SeverityBadge: Color-coded severity label
- CameraOverlay: Guided capture overlay
- RecommendationCard: Action recommendations

### 6.7 Responsive Design

- Support screen sizes: 4.5" to 7" (phones/small tablets)
- Adaptive layouts for portrait/landscape
- Safe area handling for notches/punch holes
- Minimum touch target: 48x48 dp

---

## 7. Security Architecture

### 7.1 Authentication & Authorization

**Authentication Flow**:
1. User enters credentials
2. Firebase Auth validates
3. JWT token issued (1 hour expiry)
4. Refresh token stored securely (30 days)
5. Token included in API headers

**Authorization Levels**:
- Public: Unauthenticated (login, register only)
- User: Authenticated user (own data only)
- Premium: Paid subscribers (additional features)
- Admin: Internal use (future)

### 7.2 Data Security

**In Transit**:
- TLS 1.3 for all API calls
- Certificate pinning (production)
- No sensitive data in URLs

**At Rest**:
- Firestore encryption by default
- Storage bucket encryption
- Local storage: Encrypted with flutter_secure_storage

**Sensitive Data Handling**:
- No PII in logs
- Anonymize data for analytics
- Secure deletion on account removal

### 7.3 Privacy Compliance

**Data Minimization**:
- Collect only necessary data
- No location tracking
- Optional fields for pet details

**User Consent**:
- Explicit consent on first use
- Granular permissions (camera, storage)
- Opt-in for data usage in AI training

**Data Rights**:
- View all stored data
- Export data (JSON format)
- Delete account and all data
- Revoke consent anytime

### 7.4 API Security

**Rate Limiting**:
- 100 requests/minute per user
- 10 scans/hour per user (abuse prevention)
- Exponential backoff on failures

**Input Validation**:
- File type whitelist (JPEG, PNG, MP4)
- Max file size: 10MB (photos), 50MB (videos)
- Image dimension limits: 4000x4000 px
- Sanitize all user inputs

**API Key Management**:
- Rotate keys quarterly
- Separate keys for dev/staging/prod
- Store in environment variables
- Never commit to version control

---

## 8. Performance Considerations

### 8.1 Mobile App Performance

**App Size**:
- Target: <50MB (Android), <60MB (iOS)
- Use code splitting
- Lazy load non-critical features
- Compress assets

**Launch Time**:
- Cold start: <2 seconds
- Warm start: <1 second
- Use splash screen for perceived performance

**Memory Usage**:
- Target: <150MB RAM
- Dispose unused resources
- Cache management (max 100MB)
- Image compression before upload

**Battery Optimization**:
- Minimize background tasks
- Batch network requests
- Use efficient image formats (WebP)

### 8.2 Backend Performance

**Response Times**:
- API endpoints: <500ms (p95)
- AI analysis: <10 seconds (p95)
- Image upload: <5 seconds for 5MB

**Scalability**:
- Auto-scale Cloud Functions
- Firestore indexes for common queries
- CDN for static assets
- Connection pooling

**Caching Strategy**:
- User profile: 5 minutes
- Pet data: 10 minutes
- Scan results: 1 hour
- Static content: 24 hours

### 8.3 Database Optimization

**Firestore Best Practices**:
- Denormalize for read performance
- Composite indexes for complex queries
- Pagination (20 items per page)
- Avoid deep nesting (max 3 levels)

**Query Optimization**:
```dart
// Efficient: Use indexed fields
scans.where('userId', isEqualTo: uid)
     .where('status', isEqualTo: 'completed')
     .orderBy('timestamp', descending: true)
     .limit(20);

// Avoid: Full collection scans
scans.where('petId', isEqualTo: petId).get(); // Without index
```

### 8.4 Image Optimization

**Upload Pipeline**:
1. Client-side compression (80% quality)
2. Resize to max 1920x1080
3. Convert to WebP (if supported)
4. Generate thumbnail (300x300)
5. Upload to Cloud Storage
6. Store URLs in Firestore

**Delivery**:
- Use CDN for image serving
- Lazy load images in lists
- Progressive image loading
- Cache with CachedNetworkImage

---

## 9. Deployment Strategy

### 9.1 Development Environments

**Local Development**:
- Flutter dev environment
- Firebase emulators (Auth, Firestore, Storage)
- Mock AI responses for testing

**Staging**:
- Separate Firebase project
- Limited AI quota
- Test data only
- Internal testing

**Production**:
- Production Firebase project
- Full AI quota
- Real user data
- Monitoring enabled

### 9.2 CI/CD Pipeline

```
Code Push → GitHub
    ↓
GitHub Actions Triggered
    ↓
Run Tests (Unit, Widget, Integration)
    ↓
Build APK/IPA
    ↓
Deploy to Firebase App Distribution (Beta)
    ↓
Manual Approval
    ↓
Deploy to Play Store/App Store (Production)
```

**Automated Checks**:
- Linting (flutter analyze)
- Unit tests (>80% coverage)
- Widget tests
- Integration tests (critical flows)
- Security scans

### 9.3 Release Strategy

**MVP Release** (Q1 2026):
- Soft launch in Mumbai
- Invite-only beta (100 users)
- Gather feedback
- Iterate quickly

**Public Release** (Q2 2026):
- Open to all Indian users
- Marketing campaign
- Monitor metrics closely
- Scale infrastructure

**Versioning**:
- Semantic versioning (MAJOR.MINOR.PATCH)
- Force update for critical bugs
- Graceful degradation for old versions

### 9.4 Monitoring & Analytics

**Application Monitoring**:
- Firebase Crashlytics (crash reporting)
- Firebase Performance Monitoring
- Custom metrics (scan success rate, AI accuracy)

**User Analytics**:
- Firebase Analytics (user behavior)
- Funnel analysis (onboarding, scan flow)
- Retention metrics (DAU, MAU, churn)

**Business Metrics**:
- Scan volume (daily, weekly, monthly)
- Conversion rate (free to premium)
- AI cost per scan
- User satisfaction (in-app surveys)

**Alerts**:
- Error rate >5%
- API latency >1s
- AI service downtime
- Unusual traffic patterns

### 9.5 Rollback Plan

**Immediate Rollback**:
- Critical bugs affecting >10% users
- Data loss/corruption
- Security vulnerabilities

**Rollback Process**:
1. Disable new version in stores
2. Promote previous version
3. Notify users via push notification
4. Fix issue in hotfix branch
5. Deploy patched version

---

## 10. Future Enhancements

### Phase 2 (Q3-Q4 2026)
- Offline mode with on-device ML
- Video analysis for gait detection
- Multi-pet household support
- Vet consultation booking
- Health reminders (vaccination, deworming)

### Phase 3 (2027)
- Wearable integration (activity tracking)
- Breed-specific health insights
- Community features (pet parent forums)
- Telemedicine integration
- Support for additional animals (rabbits, birds)

### Phase 4 (2027+)
- B2B features for veterinarians
- Insurance integration
- Pet health records (PHR)
- AI-powered nutrition recommendations
- Predictive health analytics

---

## Appendix

### A. Design Tools & Resources

- **UI Design**: Figma
- **Prototyping**: Figma, Adobe XD
- **Icons**: Material Icons, Custom SVGs
- **Illustrations**: unDraw, Custom illustrations
- **Stock Photos**: Unsplash, Pexels (pet images)

### B. Development Resources

- **Documentation**: Notion, Confluence
- **Version Control**: GitHub
- **Project Management**: Jira, Linear
- **Communication**: Slack, Discord

### C. Testing Devices

**Android**:
- Samsung Galaxy A52 (mid-range)
- Xiaomi Redmi Note 10 (budget)
- OnePlus 9 (flagship)

**iOS**:
- iPhone 12 (common)
- iPhone SE 2022 (budget)
- iPhone 14 Pro (flagship)

---

**Document Revision History**:
- v1.0 (Feb 13, 2026): Initial design document for MVP

**Contributors**:
- Product Team
- Engineering Team
- Design Team
- AI/ML Team

**Next Review Date**: March 2026 (post-beta feedback)
