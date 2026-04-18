# LogiTrack - Agent Coding Guidelines

## Project Overview

**LogiTrack** is a comprehensive, real-time courier tracking and geofence management solution built with **Flutter**. It demonstrates advanced OOP concepts, real-time Socket.IO communication, complex Google Maps integration, and enterprise-grade state management.

---

## Architecture Principles

### Clean Architecture (Layered)
This project strictly follows **Clean Architecture** with **Repository Pattern**:

```
lib/
├── core/                      # Shared utilities, constants, themes
│   ├── constants/
│   ├── errors/
│   ├── usecases/
│   └── utils/
├── features/                  # Feature-based modules
│   └── [feature_name]/
│       ├── data/             # Repositories, data sources, models
│       │   ├── datasources/
│       │   ├── models/
│       │   └── repositories/
│       ├── domain/           # Entities, use cases, repository interfaces
│       │   ├── entities/
│       │   ├── repositories/
│       │   └── usecases/
│       └── presentation/     # BLoCs, UI, widgets
│           ├── bloc/
│           ├── pages/
│           └── widgets/
├── injection_container.dart  # GetIt dependency injection setup
└── main.dart
```

### Dependency Rule
- Source code dependencies point inward
- **Presentation** layer depends on **Domain** layer
- **Data** layer depends on **Domain** layer
- **Domain** layer has NO dependencies on outer layers

---

## State Management

### BLoC Pattern (Primary)
- Use `flutter_bloc` for complex UI states (Map Loading, Route Calculation, Socket Connection)
- Each feature should have its own BLoC/Cubit
- States must extend `Equatable` for efficient comparison

```dart
// Example BLoC structure
class TrackingBloc extends Bloc<TrackingEvent, TrackingState> {
  final GetCurrentLocationUseCase getLocation;
  
  TrackingBloc({required this.getLocation}) : super(TrackingInitial()) {
    on<StartTracking>(_onStartTracking);
  }
}
```

### Provider (Secondary)
- Use for simple dependency injection scenarios
- Good for theme management, simple state sharing

---

## Key Packages & Usage

| Category | Package | Purpose |
|----------|---------|---------|
| **State Management** | `flutter_bloc`, `provider` | BLoC pattern and DI |
| **Networking** | `dio` | HTTP with interceptors for token refresh |
| **Real-time** | `socket_io_client` | Bidirectional Socket.IO communication |
| **Maps** | `google_maps_flutter` | Core map display |
| **Routing** | `flutter_polyline_points` | Google Directions API polyline decoding |
| **Location** | `geolocator`, `workmanager` | Background geofencing |
| **JSON** | `json_annotation`, `json_serializable` | Type-safe serialization |
| **XML** | `xml` | Enterprise report generation |
| **Database** | `sqflite` | SQL offline storage |
| **Firebase** | `firebase_core`, `firebase_auth`, `cloud_firestore`, `firebase_messaging` | Backend services |
| **DI** | `get_it`, `injectable` | Service locator |
| **Utilities** | `equatable`, `google_fonts`, `shimmer`, `flutter_svg` | UI polish |

---

## Coding Standards

### 1. OOP & Models
- Use `json_serializable` for immutable, type-safe models
- All entities must have `Equatable` for value equality
- Repository pattern: interface in domain, implementation in data

```dart
// Entity (Domain layer)
class LocationEntity extends Equatable {
  final double latitude;
  final double longitude;
  final DateTime timestamp;

  const LocationEntity({
    required this.latitude,
    required this.longitude,
    required this.timestamp,
  });

  @override
  List<Object?> get props => [latitude, longitude, timestamp];
}

// Model (Data layer)
@JsonSerializable()
class LocationModel extends LocationEntity {
  const LocationModel({
    required super.latitude,
    required super.longitude,
    required super.timestamp,
  });

  factory LocationModel.fromJson(Map<String, dynamic> json) => 
      _$LocationModelFromJson(json);
  
  Map<String, dynamic> toJson() => _$LocationModelToJson(this);
}
```

### 2. API & Token Handling
- Use **Dio Interceptors** for OAuth2 token refresh
- Auto-retry on 401 Unauthorized
- Queue requests during token refresh

### 3. Real-time Communication (Socket.IO)
- Emit driver location every 5 seconds
- Use Socket.IO rooms for driver isolation
- Implement offline queue with SQLite sync

### 4. Geofencing
- Background geofence using `Geolocator` + `Workmanager`
- Auto check-in when entering 100m radius
- Trigger API call to update order status

### 5. Firebase Integration
- Auth: Google Sign-In & Email/Password
- Push notifications via `firebase_messaging`
- Firestore for non-location data (profiles, package lists)

### 6. SOLID Principles
- **S**ingle Responsibility: One class per feature
- **O**pen/Closed: Extend via inheritance, don't modify
- **L**iskov Substitution: Subtypes must be substitutable
- **I**nterface Segregation: Small, focused interfaces
- **D**ependency Inversion: Depend on abstractions

---

## File Naming Conventions

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

## Testing Guidelines

- Unit tests for use cases and repositories
- Widget tests for UI components
- Mock dependencies using `mockito` or `mocktail`
- Test BLoC states and events

---

## Important Notes

1. **Flutter Version**: 3.22+, Dart 3.4+
2. **Offline-First**: Always queue data locally before network sync
3. **Performance**: Use `const` constructors, minimize widget rebuilds
4. **Security**: Never hardcode API keys; use environment variables
5. **Background Tasks**: Geofencing must work when app is in background

---

## Resources

- [BLoC Library](https://bloclibrary.dev)
- [Flutter Clean Architecture](https://resocoder.com/flutter-clean-architecture-tdd/)
- [Socket.IO Client](https://socket.io/docs/v4/client-api/)
- [Google Maps Flutter](https://pub.dev/packages/google_maps_flutter)
