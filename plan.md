# LogiTrack - Build Plan

## Project Overview
**LogiTrack** is a real-time courier tracking and geofence management Flutter application with Socket.IO communication, Google Maps integration, BLoC state management, and Firebase backend.

---

## Phase 1: Project Foundation & Architecture Setup

### 1.1 Initialize Flutter Project
- [ ] Create Flutter project with `flutter create logitrack`
- [ ] Set Flutter version to 3.22+, Dart 3.4+
- [ ] Configure `pubspec.yaml` with all required dependencies

### 1.2 Setup Clean Architecture Folder Structure
- [ ] Create `lib/core/` directory with:
  - `constants/`
  - `errors/`
  - `usecases/`
  - `utils/`
- [ ] Create `lib/features/` directory structure for each feature:
  - `features/auth/`
  - `features/tracking/`
  - `features/geofence/`
  - `features/orders/`
  - `features/notifications/`
  - `features/reports/`
- [ ] Each feature follows: `data/`, `domain/`, `presentation/` layers

### 1.3 Configure Dependency Injection
- [ ] Setup `injection_container.dart` with GetIt
- [ ] Configure injectable for code generation
- [ ] Register all repositories, use cases, and BLoCs

### 1.4 Setup Core Infrastructure
- [ ] Create `Failure` class in `core/errors/`
- [ ] Create base `UseCase` abstract class
- [ ] Create API constants and environment configuration
- [ ] Setup theme and app-wide constants

---

## Phase 2: Authentication Feature

### 2.1 Firebase Authentication Setup
- [ ] Configure Firebase project and add `google-services.json` / `GoogleService-Info.plist`
- [ ] Initialize Firebase in `main.dart`
- [ ] Setup `firebase_core`, `firebase_auth` packages

### 2.2 Domain Layer - Auth
- [ ] Create `AuthEntity` with user properties
- [ ] Define `AuthRepository` abstract interface
- [ ] Create use cases:
  - `sign_in_with_email_usecase.dart`
  - `sign_in_with_google_usecase.dart`
  - `sign_out_usecase.dart`
  - `get_current_user_usecase.dart`

### 2.3 Data Layer - Auth
- [ ] Create `AuthModel` extending `AuthEntity` with JSON serialization
- [ ] Implement `AuthRepositoryImpl` with Firebase Auth
- [ ] Create `AuthRemoteDataSource` for Firebase operations

### 2.4 Presentation Layer - Auth
- [ ] Create `AuthBloc`, `AuthEvent`, `AuthState`
- [ ] Build login page UI with email/password form
- [ ] Build login page UI with Google sign-in button
- [ ] Implement auth state listening with BLoC

---

## Phase 3: Core Networking & Dio Configuration

### 3.1 Dio Client Setup
- [ ] Create `DioClient` class with base URL configuration
- [ ] Configure connection timeout and receive timeout
- [ ] Setup JSON encoder/decoder

### 3.2 Token Refresh Interceptor
- [ ] Create `TokenInterceptor` extending `Interceptor`
- [ ] Implement 401 detection and token refresh logic
- [ ] Add request queuing during token refresh
- [ ] Implement automatic retry after token refresh
- [ ] Handle refresh token expiration

### 3.3 API Error Handling
- [ ] Create `ApiException` and `NetworkException` classes
- [ ] Implement error mapping in Dio client
- [ ] Create retry mechanism for failed requests

---

## Phase 4: Real-time Communication (Socket.IO)

### 4.1 Socket.IO Infrastructure
- [ ] Add `socket_io_client` package
- [ ] Create `SocketService` singleton for connection management
- [ ] Implement connection state management (connect, disconnect, reconnect)

### 4.2 Domain Layer - Socket
- [ ] Create `LocationEntity` with lat/lng/timestamp
- [ ] Define `LocationRepository` interface
- [ ] Create `EmitLocationUseCase` and `ListenForTasksUseCase`

### 4.3 Data Layer - Socket
- [ ] Create `LocationModel` with JSON serialization
- [ ] Implement `SocketDataSource` for Socket.IO operations
- [ ] Implement `LocationRepositoryImpl`

### 4.4 Socket.IO Room Management
- [ ] Implement driver room isolation by driver ID
- [ ] Create room join/leave mechanisms
- [ ] Handle room-based event listening

### 4.5 Offline Queue for Location
- [ ] Setup SQLite database for offline storage
- [ ] Create `LocationQueue` for pending location updates
- [ ] Implement sync mechanism when connection restored
- [ ] Create background location emission service

---

## Phase 5: Google Maps & Routing Integration

### 5.1 Google Maps Setup
- [ ] Add `google_maps_flutter` package
- [ ] Configure iOS (ApiKey in AppDelegate) and Android (manifest)
- [ ] Create `MapApiKey` configuration
- [ ] Setup Google Maps API console credentials

### 5.2 Domain Layer - Maps
- [ ] Create `RouteEntity` with origin, destination, polyline points
- [ ] Define `MapRepository` interface
- [ ] Create use cases:
  - `get_current_location_usecase.dart`
  - `get_route_usecase.dart`
  - `calculate_distance_usecase.dart`

### 5.3 Data Layer - Maps
- [ ] Create `RouteModel` with polyline decoding
- [ ] Add `flutter_polyline_points` for Directions API decoding
- [ ] Implement `MapRemoteDataSource` with Google Directions API
- [ ] Implement `MapRepositoryImpl`

### 5.4 Presentation - Map UI
- [ ] Create `MapBloc`, `MapEvent`, `MapState`
- [ ] Build `MapPage` with Google Maps widget
- [ ] Display courier location marker
- [ ] Show destination marker
- [ ] Draw route polylines on map
- [ ] Show route info (distance, duration)

### 5.5 Location Tracking Service
- [ ] Implement continuous location updates
- [ ] Setup location emission every 5 seconds via Socket.IO
- [ ] Handle location permissions
- [ ] Implement battery-efficient location strategies

---

## Phase 6: Geofence & Background Tasks

### 6.1 Geofence Infrastructure
- [ ] Add `geolocator` and `workmanager` packages
- [ ] Configure background fetch capabilities
- [ ] Setup iOS location permissions (Always authorization)

### 6.2 Domain Layer - Geofence
- [ ] Create `GeofenceEntity` with coordinates and radius
- [ ] Define `GeofenceRepository` interface
- [ ] Create use cases:
  - `check_geofence_usecase.dart`
  - `register_geofence_usecase.dart`
  - `trigger_checkin_usecase.dart`

### 6.3 Data Layer - Geofence
- [ ] Create `GeofenceModel`
- [ ] Implement `GeofenceRepositoryImpl` with Geolocator
- [ ] Calculate distance using Haversine formula

### 6.4 Auto Check-in Logic
- [ ] Implement 100m radius detection
- [ ] Create background geofence monitoring with Workmanager
- [ ] Trigger API call when entering geofence
- [ ] Update order status on check-in
- [ ] Handle background execution

### 6.5 Presentation - Geofence UI
- [ ] Display geofence circle on map
- [ ] Show check-in status indicator
- [ ] Display notification on geofence entry

---

## Phase 7: Orders Feature

### 7.1 Domain Layer - Orders
- [ ] Create `OrderEntity` with order details (id, status, address, coordinates)
- [ ] Define `OrderRepository` interface
- [ ] Create use cases:
  - `get_orders_usecase.dart`
  - `get_order_details_usecase.dart`
  - `update_order_status_usecase.dart`
  - `accept_order_usecase.dart`

### 7.2 Data Layer - Orders
- [ ] Create `OrderModel` with JSON serialization
- [ ] Implement `OrderRemoteDataSource` with Dio
- [ ] Implement `OrderRepositoryImpl`
- [ ] Create `OrderLocalDataSource` with SQLite for offline

### 7.3 Presentation - Orders UI
- [ ] Create `OrderBloc`, `OrderEvent`, `OrderState`
- [ ] Build orders list page
- [ ] Build order details page
- [ ] Build order status update UI
- [ ] Implement pull-to-refresh

---

## Phase 8: Notifications (Firebase Messaging)

### 8.1 Firebase Messaging Setup
- [ ] Add `firebase_messaging` package
- [ ] Configure iOS/Android for push notifications
- [ ] Setup notification icons and channels

### 8.2 Domain Layer - Notifications
- [ ] Create `NotificationEntity`
- [ ] Define `NotificationRepository` interface
- [ ] Create use cases:
  - `get_notifications_usecase.dart`
  - `mark_notification_read_usecase.dart`

### 8.3 Data Layer - Notifications
- [ ] Create `NotificationModel`
- [ ] Implement Firebase messaging handling
- [ ] Implement local notification storage

### 8.4 Presentation - Notifications
- [ ] Create notification list UI
- [ ] Implement notification tap handling
- [ ] Display new task notifications from admin

---

## Phase 9: Reports & XML Generation

### 9.1 Domain Layer - Reports
- [ ] Create `ReportEntity` with delivery summary
- [ ] Define `ReportRepository` interface
- [ ] Create use cases:
  - `generate_daily_report_usecase.dart`
  - `export_xml_report_usecase.dart`

### 9.2 Data Layer - Reports
- [ ] Create `ReportModel` with XML serialization
- [ ] Implement `ReportRepositoryImpl`
- [ ] Create XML generator utility using `xml` package

### 9.3 Presentation - Reports UI
- [ ] Build reports page with date selection
- [ ] Display delivery statistics
- [ ] Add XML export button
- [ ] Share XML file functionality

---

## Phase 10: Firebase Firestore Integration

### 10.1 Firestore Setup
- [ ] Add `cloud_firestore` package
- [ ] Configure Firestore security rules
- [ ] Setup Firestore indexes

### 10.2 Data Layer - Firestore
- [ ] Create Firestore data sources
- [ ] Implement real-time listeners for:
  - User profiles
  - Package lists
  - Order updates from admin

### 10.3 Sync with REST API
- [ ] Implement data synchronization between Firestore and REST API
- [ ] Handle offline-first scenarios
- [ ] Manage data consistency

---

## Phase 11: BLoC State Management Polish

### 11.1 BLoC Optimization
- [ ] Ensure all states extend `Equatable`
- [ ] Implement proper state transitions
- [ ] Add loading states and error handling
- [ ] Create meaningful state error messages

### 11.2 Cross-Feature BLoC Communication
- [ ] Setup `MultiBlocProvider` in main app
- [ ] Handle BLoC dependencies properly
- [ ] Implement BLoC listeners for side effects

### 11.3 Provider Integration
- [ ] Use Provider for theme management
- [ ] Use Provider for simple shared state
- [ ] Maintain clear separation from BLoC

---

## Phase 12: Offline-First Architecture

### 12.1 SQLite Database Setup
- [ ] Add `sqflite` package
- [ ] Create database helper class
- [ ] Setup database schema for:
  - Orders cache
  - Location queue
  - Delivered orders history
  - Pending sync items

### 12.2 Offline Sync Logic
- [ ] Implement network connectivity monitoring
- [ ] Queue API calls when offline
- [ ] Sync queued items when online
- [ ] Handle sync conflicts

---

## Phase 13: Testing & Quality Assurance

### 13.1 Unit Tests
- [ ] Write tests for all use cases
- [ ] Write tests for repositories (mock dependencies)
- [ ] Test BLoC state transitions
- [ ] Test location calculations

### 13.2 Widget Tests
- [ ] Test login page UI
- [ ] Test map page components
- [ ] Test order list widget
- [ ] Test form validation

### 13.3 Integration Tests
- [ ] Test authentication flow
- [ ] Test order acceptance flow
- [ ] Test geofence trigger flow

---

## Phase 14: Performance & Security

### 14.1 Performance Optimization
- [ ] Use `const` constructors where possible
- [ ] Implement `RepaintBoundary` for map markers
- [ ] Minimize widget rebuilds with BLoC selectors
- [ ] Optimize image and asset loading

### 14.2 Security Hardening
- [ ] Move all API keys to environment variables
- [ ] Implement secure token storage
- [ ] Add SSL pinning for API calls
- [ ] Validate all user inputs

---

## Phase 15: Build & Deployment

### 15.1 iOS Build Configuration
- [ ] Configure code signing
- [ ] Setup App Icons and Splash screen
- [ ] Configure privacy permissions in Info.plist
- [ ] Build for simulator and device

### 15.2 Android Build Configuration
- [ ] Configure signing config
- [ ] Setup App Icons and Splash screen
- [ ] Configure permissions in AndroidManifest
- [ ] Build debug and release APKs

### 15.3 Documentation
- [ ] Update README with setup instructions
- [ ] Add API documentation
- [ ] Document environment variables
- [ ] Create deployment guide

---

## Phase 16: React Admin Dashboard (Optional/Future)

### 16.1 Admin Dashboard Setup
- [ ] Initialize React project
- [ ] Setup Socket.IO client for admin
- [ ] Create driver monitoring UI
- [ ] Implement task assignment interface

---

## File Naming Reference

| Type | Convention | Example |
|------|------------|---------|
| BLoC | `[feature]_bloc.dart` | `tracking_bloc.dart` |
| Event | `[feature]_event.dart` | `tracking_event.dart` |
| State | `[feature]_state.dart` | `tracking_state.dart` |
| Entity | `[name]_entity.dart` | `location_entity.dart` |
| Model | `[name]_model.dart` | `location_model.dart` |
| Repository | `[feature]_repository.dart` | `tracking_repository.dart` |
| Use Case | `[action]_[feature]_usecase.dart` | `get_location_usecase.dart` |
| Page | `[name]_page.dart` | `map_page.dart` |
| Widget | `[name]_widget.dart` | `location_marker_widget.dart` |

---

## Key Dependencies Summary

```yaml
# State Management
flutter_bloc: ^8.1.0
provider: ^6.1.0
equatable: ^2.0.5

# Networking
dio: ^5.4.0

# Real-time
socket_io_client: ^2.0.0

# Maps & Location
google_maps_flutter: ^2.5.0
flutter_polyline_points: ^1.0.0
geolocator: ^11.0.0
workmanager: ^0.5.0

# Data Serialization
json_annotation: ^4.8.0
json_serializable: ^6.7.0
xml: ^6.5.0

# Database
sqflite: ^2.3.0
path: ^1.8.0

# Firebase
firebase_core: ^2.24.0
firebase_auth: ^4.16.0
cloud_firestore: ^4.14.0
firebase_messaging: ^14.7.0

# DI
get_it: ^7.6.0
injectable: ^2.3.0

# UI
google_fonts: ^6.1.0
shimmer: ^3.0.0
flutter_svg: ^2.0.0
```
