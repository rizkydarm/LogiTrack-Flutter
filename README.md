# 📦 LogiTrack - Courier Management & Real-time Geofence System

[![Flutter](https://img.shields.io/badge/Flutter-3.22+-02569B?logo=flutter&logoColor=white)](https://flutter.dev)
[![Dart](https://img.shields.io/badge/Dart-3.4+-0175C2?logo=dart&logoColor=white)](https://dart.dev)
[![BLoC](https://img.shields.io/badge/State-BLoC-0082C9?logo=bloc&logoColor=white)](https://bloclibrary.dev)
[![Firebase](https://img.shields.io/badge/Backend-Firebase_&_Socket.IO-FFCA28?logo=firebase&logoColor=black)](#)
[![Google Maps](https://img.shields.io/badge/API-Google_Maps_Platform-4285F4?logo=googlemaps&logoColor=white)](#)

**LogiTrack** is a comprehensive, real-time courier tracking and geofence management solution built with **Flutter** for the mobile workforce and **React** for the admin dashboard. This project is a portfolio demonstration of advanced **OOP concepts**, **real-time Socket.IO communication**, **complex Google Maps integration**, and **enterprise-grade state management**.

> **📌 Portfolio Focus:** This project specifically showcases skills in **SQL/REST API design, JSON/XML parsing, OAuth2 Token handling, Google Geofence/Routing, and clean Flutter architecture (Provider/BLoC).**

---

## ✨ Core Features (Aligned with Job Requirements)

### 🚚 Real-time Courier Tracking (Socket.IO)
- **Bidirectional Communication:** Utilizes `socket_io_client` to emit driver location every 5 seconds and listen for "New Task" events from the admin web dashboard.
- **Room Management:** Implements Socket.IO rooms to isolate location streams per driver, reducing server load.
- **Offline Queue:** Local SQLite storage ensures location data is queued and synced when network is restored.

### 🗺️ Google Maps, Geofence & Routing (Advanced API Usage)
- **Dynamic Routing:** Fetches real-time routes using **Google Directions API**. Polyline decoding and optimization are handled using efficient OOP models.
- **Geofence Auto Check-in:** Implements background geofencing using `Geolocator` and `Workmanager`. When a courier enters a 100m radius of the delivery point, the app automatically triggers an API call to update order status.
- **Secure Token Handling:** Demonstrates **OAuth 2.0** token refresh logic using **Dio Interceptors**. When a `401 Unauthorized` occurs, the interceptor automatically fetches a new access token and retries the original request seamlessly.

### 🧠 Advanced State Management (BLoC & Provider)
- **Clean Architecture:** Strict separation of `Presentation`, `Domain`, and `Data` layers using **SOLID** principles.
- **BLoC Pattern:** Manages complex UI states like Map Loading, Route Calculation, and Socket Connection Status.
- **Dependency Injection:** Uses `GetIt` for service location, making the codebase highly testable and modular.

### 🔥 Firebase Ecosystem
- **Authentication:** Google Sign-In & Email/Password via `firebase_auth`.
- **Push Notifications:** Uses `firebase_messaging` for instant task assignments (simulating admin-to-courier commands).
- **Real-time Firestore:** Used for non-location-critical data like user profiles and static package lists, demonstrating familiarity with NoSQL listening streams.

### 💾 Data Handling (SQL, JSON, XML)
- **Local Persistence:** `sqflite` (SQL) for offline caching of delivered orders.
- **API Integration:** `dio` + `json_serializable` for robust JSON serialization.
- **XML Report Generation:** Utility class using `xml` package to export daily delivery logs in XML format (as required by legacy enterprise systems).

---

## 📸 Screenshots & Demo

| Tracking Map & Geofence | Admin Assignment (React Web) | Route Optimization |
| :---: | :---: | :---: |
| ![Flutter Map Screenshot](https://via.placeholder.com/250x500/0B2C4A/FFFFFF?text=Flutter+Map+Geofence) | ![React Web Screenshot](https://via.placeholder.com/600x300/20232A/61DAFB?text=React+Admin+Socket.IO) | ![Route Screenshot](https://via.placeholder.com/250x500/1A73E8/FFFFFF?text=Google+Routing) |
| *Geofence activation & live location via Socket.IO* | *Admin dashboard with real-time driver monitoring* | *Polyline generation using Directions API* |

> ⚠️ **Note:** Replace placeholder images with actual screenshots/video link of your app for maximum portfolio impact.

---

## 📚 Tech Stack & Flutter Packages

This project intentionally uses a curated set of libraries that align perfectly with the job description keywords.

| Category | Package | Why This Was Chosen (Portfolio Justification) |
| :--- | :--- | :--- |
| **State Management** | `flutter_bloc` & `provider` | Demonstrates advanced understanding of **BLoC pattern** for complex flows and **Provider** for simple dependency injection. |
| **Networking & Token** | `dio` | Features **Interceptors** to handle token refresh and `401` errors, showcasing "pengalaman akses APIs token". |
| **Real-time** | `socket_io_client` | Direct implementation of **Socket.IO** protocol, not just Firebase listeners. |
| **Google Maps** | `google_maps_flutter` | Core map display. |
| **Routing** | `flutter_polyline_points` | Decodes **Google Directions API** JSON responses. |
| **Geofence & Location** | `geolocator` & `workmanager` | Handles background location and **geofencing** triggers. |
| **OOP & JSON** | `json_annotation`, `json_serializable` | Generates immutable, type-safe models (**OOP Concept**) from **REST API JSON**. |
| **XML Handling** | `xml` | Provides ability to parse and generate **XML** documents for enterprise reporting. |
| **Local Database** | `sqflite` | **SQL** database for offline-first architecture. |
| **Firebase** | `firebase_core`, `firebase_auth`, `cloud_firestore`, `firebase_messaging` | Demonstrates "familiar menggunakan **Firebase**". |
| **Architecture** | `get_it` & `injectable` | Service Locator for clean **Dependency Injection**. |
| **Utilities** | `equatable` | Simplifies value equality in BLoC states/cubits. |
| **UI** | `google_fonts`, `shimmer`, `flutter_svg` | Polished, production-ready UI components. |

---

## 🏛️ Project Architecture (OOP & Clean Architecture)

This project strictly follows **Clean Architecture** combined with **Repository Pattern** to ensure testability and separation of concerns.
