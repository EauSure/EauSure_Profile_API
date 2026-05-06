# EauSure Profile API

User profile and preferences backend for the EauSure platform.

This microservice handles the storage, retrieval, and modification of user-specific metadata and application preferences. It interfaces securely with a MongoDB database and enforces access control via stateless token verification.

## Scope

- Secure retrieval of the authenticated user's profile.
- Automated, just-in-time provisioning of empty profiles for new users upon initial access.
- Modification of user metadata (bio, organization, role, phone).
- Management of application preferences, including notification toggles, measurement units, and language settings.

## Route Overview

| Method | Path | Purpose |
|------|------|------|
| `GET` | `/api/profile` | Retrieve the current user's profile or auto-create one if missing |
| `PUT` | `/api/profile` | Update the current user's metadata and preferences |

## Data Architecture

The application utilizes a structured document schema to encapsulate user data. 

**Core Metadata:**
Profiles map to a unique identifier derived from the authentication layer. Basic contact and organizational information is stored at the root level of the document.

**Preferences Object:**
Application settings are grouped into a nested preferences object:
- **Notifications:** Granular toggles for email alerts, critical-only filtering, daily summaries, and maintenance reminders.
- **Units:** Localization preferences for temperature (Celsius/Fahrenheit) and distance (Metric/Imperial).
- **Language:** System language preference.

*Note: Document creation and modification timestamps are automatically managed by the database layer.*

## Security Architecture

### Protected Endpoints
All routes interacting with profile data strictly mandate a valid authorization token. The application decodes the token payload to extract the unique user identifier securely, preventing clients from querying or modifying profiles outside their scope.

### Immutable Fields
To maintain data integrity, the application intercepts and sanitizes incoming update payloads. Structural database fields (like `userId`, internal database IDs, and creation timestamps) are strictly protected and stripped from any modification requests.

## Environment Configuration

The application requires specific environmental variables to operate securely without exposing infrastructure details in the source code.

**Core Infrastructure:**
- `MONGO_URI`: Target database connection string.
- `PORT`: Optional. The port for the Express server (defaults to `3000`).

**Authentication:**
- `JWT_SECRET`: Cryptographic key utilized to validate the signatures of incoming authorization tokens.