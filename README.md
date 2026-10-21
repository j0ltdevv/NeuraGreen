# Eco-AI Tracker

A lucid AI usage tracker that measures the **environmental and financial footprint** of your LLM usage.

Eco-AI Tracker is a mobile analytics dashboard (Flutter/Dart) that turns invisible AI consumption into readable metrics: CO₂e emissions, water usage, token volume, and USD cost — per model, over time, with budgets and alerts.

> Concept — *make the invisible visible.*
> Engagement — **privacy-first** (never reads your prompt content), **offline-first** (full history consultation without network), **total transparency** of every calculation.

---

## Features

### Dashboard
- **Total footprint card** — cumulative CO₂e in kg, with a tangible equivalent (e.g. trees absorbed per year).
- **Secondary metrics** — water usage and financial cost at a glance.
- **Model comparison** — side-by-side bar charts comparing CO₂e vs. cost per model.
- **Usage timeline** — token usage over time with an interactive line chart.
- **Liquid Glass UI** — frosted glass surfaces (`BackdropFilter`), rim-lighting borders, animated ambient light blobs; dark theme optimized for OLED displays.

### Budgets & alerts
- Define monthly budgets for **CO₂e**, **water**, and **cost**.
- Local notifications when a budget is exceeded.
- **Eco-efficiency alerts** — automatically suggests a lower-impact model when a high-emission model dominates usage.

### Data & privacy
- **Secure API key storage** via `flutter_secure_storage` (Keychain / Keystore) — keys are never written to disk in plain text and never committed.
- **OpenAI integration** — read-only usage/billing endpoints (`/v1/invoices`, line items), mapped into a normalized usage log.
- **Offline-first** — all usage history is persisted locally (Hive); the dashboard works fully offline.
- **Export** — CSV and JSON export of usage history from the settings screen.

### Planned (roadmap)
- Gemini / Mistral / Anthropic usage ingestion.
- Predictive simulation of a prompt's CO₂e & cost before execution.
- Home-screen widgets (iOS/Android) with background refresh.

---

## Tech stack

| Layer | Technology |
| --- | --- |
| Framework | Flutter (Dart, SDK ≥ 3.1) |
| State management | `flutter_riverpod` (Notifier/NotifierProvider) |
| Local storage | `hive` + `hive_flutter` (generated adapters) |
| Secure storage | `flutter_secure_storage` |
| HTTP | `dio` |
| Charts | `fl_chart` (bar + line charts) |
| Notifications | `flutter_local_notifications` |
| Serialization | `freezed_annotation` / `json_serializable` (generated) |
| Motion | `flutter_animate` |
| Targets | Android, iOS (plus web/windows/macOS/Linux scaffolds) |

---

## Project structure

```
lib/
├── main.dart                     # App bootstrap: Hive init, notifications, theme
├── core/
│   ├── app_colors.dart           # Dark "Liquid Glass" palette
│   ├── app_constants.dart        # Model impact & cost coefficients
│   ├── extensions.dart
│   └── services/
│       ├── openai_service.dart   # Read-only OpenAI usage/billing fetch
│       ├── storage_service.dart  # Secure key storage
│       └── notification_service.dart
├── data/models/                  # Hive models + generated adapters
│   ├── ai_usage_log.dart
│   ├── user_settings.dart
│   ├── openai_invoice_item.dart
│   └── usage_point.dart
├── features/
│   ├── dashboard/                # Dashboard state + screen (charts, metrics)
│   └── settings/                 # Keys, budgets, export
└── presentation/widgets/
    └── glass_card.dart           # Reusable frosted-glass card
```

---

## Getting started

### Prerequisites
- Flutter SDK ≥ 3.1 ([install](https://docs.flutter.dev/get-started/install))
- A code editor with the Flutter/Dart plugins

### Run

```bash
flutter pub get
flutter run          # pick an Android/iOS device or emulator
```

> If you modify any `@HiveType` / `@freezed` / JSON-annotated model, regenerate the `.g.dart` files with:

```bash
dart run build_runner build --delete-conflicting-outputs
```

### Configure API keys

1. Launch the app and open the **Settings** screen.
2. Paste your API keys (OpenAI, Gemini, Mistral). Keys are saved in the platform secure storage (Keychain/Keystore).
3. Optionally set CO₂e / water / cost budgets.

The dashboard fetches OpenAI usage through read-only billing endpoints; refresh with the refresh button in the header.

---

## How the impact is estimated

Every usage log (provider, model, tokens, timestamp, cost) is converted into impact metrics using per-model coefficients:

| Metric | Formula |
| --- | --- |
| Energy / CO₂e | `(tokens / 1000) × CO₂e_grams_per_1000_tokens` |
| Water | `(tokens / 1000) × water_liters_per_1000_tokens` |
| Cost | taken from the provider's invoice (USD) |

Current coefficients (in `lib/core/app_constants.dart`):

| Model | CO₂e (g / 1000 tok) | Water (L / 1000 tok) |
| --- | --- | --- |
| Gemini Pro | 0.03 | 0.00026 |
| GPT-4 | 10.0 (placeholder) | 0.1 (placeholder) |
| Mistral Large | 8.0 (placeholder) | 0.08 (placeholder) |
| default | 10.0 | 0.1 |

> **Honest limits:** Gemini's figure follows Google's published claim (0.03 g CO₂e / 0.26 ml water per median prompt, assumed ≈ 1000 tokens). Other coefficients are conservative placeholders that should be refined as better data becomes available; real impact depends heavily on the data center's energy mix.

---

## License

[MIT](LICENSE) © 2026 j0ltdevv
