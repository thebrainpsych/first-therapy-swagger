# First Therapy API Documentation

Swagger/OpenAPI documentation for the [First Therapy Backend](https://github.com/thebrainpsych/first-therapy-backend) APIs.

**Live Docs:** https://thebrainpsych.github.io/first-therapy-swagger/

## API Overview

| Tag | Endpoint | Method | Auth | Description |
|-----|----------|--------|------|-------------|
| Auth | `/auth/google` | GET | No | Start Google OAuth flow |
| Auth | `/auth/oauth2callback` | GET | No | Google OAuth callback |
| Auth | `/auth/login` | POST | No | Dev-only login (returns JWT) |
| Auth | `/auth/email/start` | POST | No | Send verification email |
| Auth | `/auth/firebase` | POST | No | Firebase authentication |
| Auth | `/auth/verify-email` | GET | No | Verify email token (redirects) |
| Calendar | `/calendar/schedule` | POST | No | Schedule therapy session with Google Meet |
| Billing | `/billing/checkout` | POST | Yes | Create payment checkout session |
| Billing | `/billing/webhook` | POST | No | Payment provider webhook handler |
| Pages | `/email-verified` | GET | No | Email verification success page |
| Pages | `/invalid` | GET | No | Invalid/expired link error page |

## Project Structure

```
first-therapy-swagger/
├── index.html       # Swagger UI served via GitHub Pages
├── openapi.yaml     # OpenAPI 3.0.3 spec (source of truth)
└── README.md
```

## How to Add a New Endpoint

### 1. Open `openapi.yaml`

All endpoints are defined under the `paths:` section and reusable models under `components.schemas:`.

### 2. Add the path entry

Add your new endpoint under `paths:`. Here's a template:

```yaml
paths:
  /your/new-endpoint:
    post:                              # HTTP method (get, post, put, delete, patch)
      tags: [Auth]                     # Group under an existing tag or add a new one
      summary: Short description
      description: Longer explanation of what this endpoint does.
      security:                        # Remove this block if no auth required
        - BearerAuth: []
      requestBody:                     # Remove if GET/DELETE with no body
        required: true
        content:
          application/json:
            schema:
              $ref: "#/components/schemas/YourRequestSchema"
      responses:
        "200":
          description: Success
          content:
            application/json:
              schema:
                $ref: "#/components/schemas/YourResponseSchema"
        "400":
          description: Validation error
          content:
            application/json:
              schema:
                $ref: "#/components/schemas/ErrorResponse"
        "401":
          description: Unauthorized (only for protected routes)
          content:
            application/json:
              schema:
                $ref: "#/components/schemas/ErrorResponse"
```

### 3. Add request/response schemas (if new)

Define reusable schemas under `components.schemas:`:

```yaml
components:
  schemas:
    YourRequestSchema:
      type: object
      required: [field1, field2]
      properties:
        field1:
          type: string
          example: "value1"
        field2:
          type: integer
          example: 42

    YourResponseSchema:
      type: object
      properties:
        success:
          type: boolean
          example: true
        data:
          type: string
          example: "result"
```

### 4. Add a new tag (if needed)

If your endpoint belongs to a new group, add the tag at the top of the file:

```yaml
tags:
  - name: YourNewTag
    description: What this group of endpoints does
```

### 5. Commit and push

```bash
git add openapi.yaml
git commit -m "Add /your/new-endpoint to API docs"
git push origin main
```

GitHub Pages will auto-deploy within 1-2 minutes.

## Authentication

Protected endpoints use JWT Bearer tokens. Include the header:

```
Authorization: Bearer <your-jwt-token>
```

Tokens are obtained via `/auth/login` (dev) or `/auth/firebase` (production).

## Running Locally

Just open `index.html` in a browser, or serve it with any static server:

```bash
npx serve .
```

Then visit `http://localhost:3000`.
