# Eumelos.AI Project Status Report

## 1. Current Status

The project is currently a semi-complete full-stack application consisting of a React frontend and a Spring Boot backend.

- **Frontend (`unisched`)**:
  - **Tech Stack**: React 19, Vite, Lucide Icons.
  - **UI**: High-quality dark-themed UI (Glassmorphism) with rebranding to **Eumelos.AI** visible on the login page.
  - **Features**: Authentication (Student/Admin), Dashboard, Course Management, Schedule Conflict Checking.
- **Backend (`FSAD_project`)**:
  - **Tech Stack**: Spring Boot (Maven), Hibernate/JPA, Spring Security + OAuth2.
  - **Database**: MySQL.
  - **Security**: JWT-based authentication with a Custom OAuth2 flow for Google Login.

---

## 2. Backend Issues

The backend has several critical and minor issues that need resolution:

1. **Critical: Invalid Spring Boot Version**: The `pom.xml` specifies version `4.0.5`. Since Spring Boot 4.x is not yet released/stable, this will cause build failures. It needs to be downgraded to a stable version (e.g., `3.4.x`).
2. **Critical: Missing Environment Variables**: The `application.properties` relies on `${GOOGLE_CLIENT_SECRET}` and `${RECAPTCHA_SECRET}`. If these aren't defined in the system environment, the application will fail to start.
3. **Database Connection**: The datasource URL is hardcoded to `jdbc:mysql://localhost:3306/course_scheduler`. If the MySQL schema isn't created manually, the app will crash on startup.
4. **Security/OAuth2 Handshake**: The `SessionCreationPolicy.IF_REQUIRED` is set, which is necessary for OAuth2 redirects, but may cause session management complexities if not handled carefully alongside JWTs.

---

## 3. Linkings (FE/BE Integration)

The connection between the frontend and backend is handled through two layers:

- **Development Proxy**: The `vite.config.js` in the frontend acts as a bridge. It redirects any requested path starting with `/auth`, `/course`, `/enrollment`, etc., to `http://localhost:8080`. This avoids CORS issues during development.
- **API Fetch Wrapper**: The `src/api.js` file centralizes all network requests.
  - It retrieves the JWT from `localStorage`.
  - It automatically adds the `Authorization: Bearer <token>` header to all protected requests.
  - It handles 401/403 errors by clearing local storage and redirecting to the login page.

---

## 4. Cons & Technical Debt

1. **Naming Inconsistencies**: The project root is `frontendv3`, the backend is `FSAD_project`, and the frontend is `unisched`. This can be confusing for deployment and maintenance.
2. **Hardcoded Configurations**: Many settings (like the Google Recaptcha site key in `LoginPage.jsx`) are hardcoded rather than being pulled from environment variables.
3. **Lack of Unified Build**: There is no top-level script to start both FE and BE simultaneously, requiring manual terminal management.
4. **Type Safety**: There is no shared type definition or schema (like OpenAPI/Swagger) being enforced on the frontend, which might lead to runtime errors if the backend API changes.
