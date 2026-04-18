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

## Design System (Google Stitch)

**IMPORTANT: All UI implementations MUST follow `stitch_code.html` as the source of truth.**

This project follows Google Stitch design principles for a premium, anti-generic UI. The reference file `stitch_code.html` contains the canonical implementation of all screens and components. When implementing UI:

1. **Reference `stitch_code.html`** for exact color values, spacing, typography, and component behaviors
2. **Extract colors** from the Tailwind config in the HTML (e.g., `primary: #094cb2`, `surface-container-lowest: #ffffff`)
3. **Match component states** exactly as shown (active/inactive nav items, hover effects, shadows)
4. **Follow the HTML structure** for layout hierarchy and responsive breakpoints
5. **Use Material Symbols icons** with appropriate FILL variant for active states

### Source Files
- **UI Reference:** `stitch_code.html` - Google Stitch generated screens
- **Design Specs:** This AGENTS.md - Color palette, typography, and component standards

### Color Palette

| Role | Color | Hex |
|------|-------|-----|
| Primary | Deep Blue | `#094cb2` |
| Primary Container | Medium Blue | `#3366cc` |
| Primary Fixed | Light Blue | `#d9e2ff` |
| Inverse Primary | Pale Blue | `#b1c5ff` |
| Background | Off-White | `#faf9fa` |
| Surface | Off-White | `#faf9fa` |
| Surface Bright | Off-White | `#faf9fa` |
| Surface Container Lowest | White | `#ffffff` |
| Surface Container Low | Warm Gray Light | `#f5f3f4` |
| Surface Container | Warm Gray | `#efedee` |
| Surface Container High | Gray | `#e9e8e9` |
| Surface Container Highest | Dark Gray | `#e3e2e3` |
| Surface Dim | Dim Gray | `#dbdadb` |
| On Surface | Charcoal | `#1b1c1d` |
| On Surface Variant | Steel | `#434653` |
| Tertiary | Gold | `#6d5e00` |
| Tertiary Container | Amber | `#bfab49` |
| Tertiary Fixed | Pale Gold | `#f9e37a` |
| Tertiary Fixed Dim | Muted Gold | `#dcc661` |
| Error | Red | `#ba1a1a` |
| Error Container | Light Red | `#ffdad6` |
| Outline | Medium Gray | `#737784` |
| Outline Variant | Light Gray | `#c3c6d5` |

### Typography

| Style | Font | Size | Weight | Usage |
|-------|------|------|--------|-------|
| Headline | Noto Serif | 32-48px | Bold | Page titles, serif emphasis |
| Body | Inter | 14-16px | Regular/Medium | Descriptions, content |
| Label | Public Sans | 10-14px | Medium/Semibold | Buttons, captions, uppercase metadata |
| Display | Noto Serif Italic | 24px | Bold | Logo only |

### Border Radius System
- Default: `2px`
- LG: `4px`
- XL: `8px`
- Full: `12px` (buttons, inputs)
- 2XL: `16px` (cards)
- 3XL: `24px` (bottom sheets)

---

## Token Efficiency Principles

### Core Philosophy
Token efficiency is a **first-class concern** in this project. Every API call, Socket.IO emission, and background task must be evaluated for its necessity and cost.

### Token Cost Categories

| Resource | Cost Impact | Monitoring Priority |
|----------|-------------|-------------------|
| **Google Maps API** | Per request (Directions, Geocoding, Places) | Critical |
| **Socket.IO Connections** | Server resource allocation | High |
| **Firebase Services** | Firestore reads/writes, Auth operations | High |
| **Location Updates** | Battery + Network + API usage | Critical |
| **Push Notifications** | FCM costs per message | Medium |

---

## State Management

### BLoC Pattern (Primary)
- Use `flutter_bloc` for complex UI states (Map Loading, Route Calculation, Socket Connection)
- Each feature should have its own BLoC/Cubit
- States must extend `Equatable` for efficient comparison
- **Use `select` and `where` to prevent unnecessary rebuilds**

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
| **UI** | `equatable`, `google_fonts`, `shimmer`, `flutter_svg`, `material_symbols_icons` | UI polish, icons |

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
- **Token refresh must be rate-limited to prevent refresh loop**

### 3. Real-time Communication (Socket.IO)
- Emit driver location **every 5 seconds only when actively tracking**
- **Pause emissions when app is backgrounded**
- Use Socket.IO rooms for driver isolation
- Implement offline queue with SQLite sync
- **Batch location updates if offline before emitting**

### 4. Google Maps API Efficiency (CRITICAL)

#### Directions API
- **Cache route results** - Same origin/destination within 30 minutes = reuse
- **Simplify polylines** before display (reduce point count)
- Request only `overview_polyline` for map display, full details on demand
- Use `flutter_polyline_points` for client-side decoding

#### Location Updates
- Use `distanceFilter` to limit location updates (minimum 10 meters)
- Batch location收集 when offline, emit single batch when online
- Throttle location requests to **once per 5 seconds maximum**

#### Map Rendering
- Limit visible markers to viewport + buffer zone
- Use `MarkerCluster` for multiple nearby markers
- **Disable map tiles fetching when not visible** (TabView optimization)

### 5. Geofencing
- Background geofence using `Geolocator` + `Workmanager`
- Auto check-in when entering 100m radius
- Trigger API call to update order status
- **Deduplicate geofence triggers** - prevent multiple check-ins for same location

### 6. Firebase Integration
- Auth: Google Sign-In & Email/Password
- Push notifications via `firebase_messaging`
- Firestore for non-location data (profiles, package lists)
- **Use Firestore queries with limits** - never fetch entire collections
- **Enable Firestore caching** for offline-first reads

### 7. Token-Aware Caching Strategy

```
Cache Hierarchy (in order of token cost):
1. Memory Cache (in-memory, free) - Session data, current user
2. SQLite Cache (local, free) - Route results, order lists
3. SharedPreferences (local, free) - Simple flags, settings
4. Network (token cost) - Only when cache miss
```

### 8. Request Deduplication
- Maintain request fingerprint map to prevent duplicate API calls
- Use `workmanager` for background sync instead of polling
- **Reject redundant location emissions** if coordinates haven't changed significantly

### 9. SOLID Principles
- **S**ingle Responsibility: One class per feature
- **O**pen/Closed: Extend via inheritance, don't modify
- **L**iskov Substitution: Subtypes must be substitutable
- **I**nterface Segregation: Small, focused interfaces
- **D**ependency Inversion: Depend on abstractions

---

## UI Component Standards

### Theme Data (Flutter)

```dart
// Color Scheme
const Color primary = Color(0xFF094CB2);
const Color primaryContainer = Color(0xFF3366CC);
const Color background = Color(0xFFF9FAFA);
const Color surface = Color(0xFFF9FAFA);
const Color surfaceContainerLowest = Color(0xFFFFFFFF);
const Color surfaceContainer = Color(0xFFEFEDEE);
const Color onSurface = Color(0xFF1B1C1D);
const Color onSurfaceVariant = Color(0xFF434653);
const Color tertiary = Color(0xFF6D5E00);
const Color tertiaryContainer = Color(0xFFBFAB49);
const Color error = Color(0xFFBA1A1A);
const Color outline = Color(0xFF737784);
const Color outlineVariant = Color(0xFFC3C6D5);

// Border Radius
const double radiusLg = 4.0;    // 4px
const double radiusXl = 8.0;    // 8px
const double radiusFull = 12.0; // 12px - buttons, inputs
const double radius2xl = 16.0;  // 16px - cards
const double radius3xl = 24.0;  // 24px - bottom sheets
```

### Card Widget
- Border radius: 16px
- Background: Surface Container Lowest `#FFFFFF`
- Border: 1px Outline Variant `rgba(195, 198, 213, 0.3)`
- Padding: 24px
- Shadow: `BoxShadow(color: Colors.black.withOpacity(0.06), blurRadius: 8, offset: Offset(0, 2))`

### Button Widget
- Primary: Gradient from Primary to Primary Container
- Border radius: 12px (Full)
- Height: 48px minimum
- Text: Public Sans, 14px, Bold, uppercase tracking
- Active state: translateY(-2px) with deeper shadow
- Disabled: 50% opacity

### Bottom Navigation Bar
- Height: 80px + safe area
- Background: Surface Container Lowest with 90% opacity + backdrop blur
- Border top: 1px Outline Variant at 10% opacity
- Icons: Material Symbols, 24px
- Active: Primary color, filled variant
- Inactive: On Surface Variant color
- Border radius on top: 24px

### Top App Bar
- Height: 64px + safe area
- Background: Surface Container Lowest with 80% opacity + backdrop blur
- Logo: Noto Serif Italic, Blue 800
- Icons: Material Symbols, 24px

### Progress/Step Indicator
- Height: 4px bars
- Active: Primary fill
- Completed: Primary fill
- Pending: Surface Variant fill
- Border radius: Full

### Timeline Component
- Line width: 2px
- Line color: Outline Variant
- Active dot: 16px, Primary fill, ring shadow
- Inactive dot: 16px, Surface Variant fill

### Bottom Sheet
- Border radius: 32px top corners
- Backdrop blur: 20px
- Handle: 48px x 6px, centered, Surface Variant
- Shadow: `BoxShadow(color: Colors.black.withOpacity(0.08), blurRadius: 48, offset: Offset(0, -12))`

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
| Theme | `app_theme.dart` | Theme configuration |

---

## Testing Guidelines

- Unit tests for use cases and repositories
- Widget tests for UI components
- Mock dependencies using `mockito` or `mocktail`
- Test BLoC states and events
- **Token consumption tests** - verify API call counts

---

## Important Notes

1. **Flutter Version**: 3.22+, Dart 3.4+
2. **Offline-First**: Always queue data locally before network sync
3. **Performance**: Use `const` constructors, minimize widget rebuilds
4. **Security**: Never hardcode API keys; use environment variables
5. **Background Tasks**: Geofencing must work when app is in background
6. **Token Budget**: Every feature should have a "token budget" consideration in PR reviews

---

## Token Budget Checklist

Before shipping any feature, verify:

- [ ] API calls are cached where possible (30-min TTL for routes)
- [ ] Location emissions are throttled (max 1 per 5 seconds)
- [ ] Socket.IO reconnects use exponential backoff
- [ ] Firestore queries have appropriate limits
- [ ] Map markers are clustered when > 10
- [ ] Background tasks are batched, not individual requests
- [ ] No polling loops without exponential backoff
- [ ] Debug logs for API calls are disabled in release

---

## Design Anti-Patterns (BANNED)

- **No emojis** - use Material Symbols icons only
- **No pure black** (#000000) - use Charcoal `#1B1C1D`
- **No neon/outer glow shadows** - subtle, diffused shadows only
- **No overlapping elements** - clean spatial separation
- **No Inter for headlines** - Noto Serif for display/headlines
- **No generic placeholder names** - use realistic sample data
- **No centered hero sections** - prefer asymmetric layouts
- **No 3-column equal card grids** - use asymmetric grids

---

## Resources

- [BLoC Library](https://bloclibrary.dev)
- [Flutter Clean Architecture](https://resocoder.com/flutter-clean-architecture-tdd/)
- [Socket.IO Client](https://socket.io/docs/v4/client-api/)
- [Google Maps Flutter](https://pub.dev/packages/google_maps_flutter)
- [Google Maps API Best Practices](https://developers.google.com/maps/api-security-best-practices)
- [Firebase Pricing](https://firebase.google.com/pricing)
- [Material Symbols Icons](https://fonts.google.com/icons)
- [Noto Serif Font](https://fonts.google.com/noto)
- [Inter Font](https://fonts.google.com/inter)
- [Public Sans Font](https://fonts.google.com/specimen/Public+Sans)
- [Google Stitch](https://labs.google.com/stitch)
