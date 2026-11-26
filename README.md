# Stapsgewijze inleiding tot mobiel programmeren met Kotlin

📖 **Lees de tutorial: [https://stahe.github.io/nl-kotlin-etape-par-etape-nestjs-oct-2026/](https://stahe.github.io/nl-kotlin-etape-par-etape-nestjs-oct-2026/)**

Deze cursus leert u hoe u een **native mobiele Android**-app schrijft met [Kotlin](https://kotlinlang.org) 2.2 en [Jetpack Compose](https://developer.android.com/compose), de declaratieve UI-bibliotheek van Android: een app waarvan de schermen **op de telefoon** worden gerenderd op basis van JSON-gegevens van een server.

Hij volgt de voortgang van de cursus [Stapsgewijze inleiding tot het mobiele framework Flutter](https://stahe.github.io/nl-flutter-etape-par-etape-sept-2026/) (die zelf is gemodelleerd naar de cursussen over [React](https://stahe.github.io/nl-react-etape-par-etape-sept-2026/), [Vue.js](https://stahe.github.io/nl-vuejs-etape-par-etape-sept-2026/) en [Angular](https://stahe.github.io/nl-angular-etape-par-etape-sept-2026/)): dezelfde structuur, dezelfde 25 voorbeelden, dezelfde server, dezelfde casus, geschreven in de Android-stijl.

| Flutter-cursus | Kotlin-cursus |
|---|---|
| Dart, widgets, een methode `build()` | Kotlin, `@Composable`-functies |
| `setState`, `StatefulWidget` | `remember`, `rememberSaveable`, `mutableStateOf` |
| de engine van Flutter rendert elke pixel | Compose, de eigen toolkit van Android (Material 3) |
| `initState`, `dispose` | effecten: `LaunchedEffect`, `DisposableEffect` |
| `InheritedWidget`, provider | `CompositionLocal`, `ViewModel`, `StateFlow` |
| go_router | Compose Navigation (`@Serializable`-routes, deep links) |
| `Future`, `Stream` | coroutines, `Flow` |
| JSON-woordenboeken, `intl` | `strings.xml`, de taal van de app (`setApplicationLocales`) – en dezelfde JSON-woordenboeken |
| het pakket `http` | OkHttp en zijn `CookieJar` voor de tokencookie |
| `shared_preferences` | DataStore, `SharedPreferences` |
| `flutter_test` | geïnstrumenteerde tests van Compose (op de emulator) |

De server blijft echter ongewijzigd: het is de JSON-server van de app **RdvMedecins** die al wordt gebruikt door de clients in React, Vue.js, Angular en Flutter.

## De aanpak: veel korte voorbeelden, daarna een casus

De cursus is opgebouwd rond **25 korte voorbeelden**, elk gericht op één enkel concept. Ze vormen samen één Android Studio-project – één activity per voorbeeld – dat bereikbaar is via een menu.

| Hoofdstuk | Inhoud | Voorbeelden |
|---|---|---|
| Aan de slag | een Android-project (Gradle, manifest, activity), `@Composable`-functies, toestand en recompositie, gebeurtenissen (gebaren, scrollen, focus), alle veldtypen, een reducer (`sealed interface`, `when`), validatie, velden die zichzelf kunnen valideren, opmaak (`java.time`, `NumberFormat`, extensies) | 01–09 |
| Componenten | parameters, functies als parameters, compositie (plaatsing, generieke componenten), levenscyclus (effecten), `CompositionLocal`, herbruikbaar gedrag en animaties, bevestigingsvenster (suspend-functie) en `Snackbar` | 10–16 |
| Navigatie | Compose Navigation: getypeerde routes, parameters, deep links, tabbalk, omleiding, guards | 17–18 |
| Asynchrone bewerkingen en gedeelde toestand | coroutines, `Flow`, debouncing, verouderde antwoorden (`mapLatest`), `produceState`, `ViewModel`, `StateFlow`, DataStore, donker thema, `SaveableStateHolder` | 19–20 |
| Internationalisering | resources in `strings.xml`, parameters, meervouden, datums, bedragen, taal van de app, vertaalde kalender | 21 |
| De server: een blackbox | installatie van de JSON-server, zijn API, 48 voorbeelden met `curl`, wat er verandert voor een mobiele client | – |
| Communiceren met de server | OkHttp, het serveradres (emulator, telefoon, `adb reverse`), de tokencookie op de telefoon, `kotlinx.serialization`, een API-toegangslaag, een `ViewModel`, servergegenereerde fouten gekoppeld aan velden | 22–25 |

Elk voorbeeld wordt gepresenteerd met zijn volledige, toegelichte code en schermafbeeldingen van de uitvoering.

## De server: een blackbox

De server is de NestJS-server uit de vorige lessen, waarvan de controllers **JSON** teruggeven. Deze les behandelt hem als een **blackbox**: we richten hem in, bestuderen zijn API en bevragen hem met `curl` – maar we hoeven zijn code niet te lezen (die is meegeleverd en toegelicht voor wie nieuwsgierig is).

- Alle fouten hebben hetzelfde formaat: `{ "statusCode": 409, "key": "ERRORS.LOGIN_TAKEN", "params": {...}, "fields": {...} }` — **sleutels** voor vertaling, nooit echte tekst;
- authenticatie via een JWT-token in een `httpOnly`-cookie: op de telefoon is het de HTTP-client (OkHttp en zijn `CookieJar`) die hem bewaart en terugstuurt;
- een „testmodus" voor de CAPTCHA, zodat u de API met `curl` kunt bevragen (en schermafbeeldingen kunt genereren via geautomatiseerde tests).

## De casus: de Android-client van RdvMedecins

Een complete applicatie voor het **maken van afspraken bij een medische praktijk**, met **alle** bestanden opgesomd en toegelicht.

- **Modern Android**: Kotlin 2.2, Jetpack Compose en Material 3; één enkele activity; Compose Navigation; `ViewModel`; coroutines; OkHttp; kotlinx.serialization; een gelaagde architectuur (gegevens, toestand, UI).
- **Drie rollen**: `ADMIN` (beheert artsen en cliënten), `DOCTOR` (plant afspraken in en annuleert ze), `USER` (de patiënt: maakt afspraken voor zichzelf, beheert zijn account).
- **Privacy**: een patiënt ontvangt nooit de namen van andere patiënten – de server stuurt ze niet mee.
- **De volledige toestand van een scherm zit in zijn route**: `Ecrans.Agenda(idMedecin = 1, jour = "2026-10-05")`; het boekingsvenster is een „dialog"-bestemming, die de knop Terug sluit.
- **Validatie aan de serverzijde**: formulieren tonen de door de API teruggegeven fouten onder elk veld; optimistische vergrendeling, dubbele namen, reeds gebruikte login, enz.
- **Sessie**: blijft behouden tussen sessies (de cookie wordt op het apparaat bewaard), hersteld bij het opstarten (`GET /api/auth/moi`), verlopen wordt op één plaats afgehandeld (antwoord 401), na opnieuw verbinden keert de app terug naar het gevraagde scherm.
- **Geschikt voor mobiel**: uitschuifmenu (of `NavigationRail` op grote schermen en in landschapsstand), lijsten in plaats van tabellen, zwevende knop, toetsenbord dat velden niet verbergt, en functionaliteit die rotatie en taalwijzigingen overleeft.
- **Frans / Engels**, met de JSON-woordenboeken van de andere clients, ook voor de kalender en de eigen tekstreeksen van Android.
- **Automatische schermafbeeldingen**: een geïnstrumenteerde test navigeert door de app als een gebruiker.
- **Uitrol**: de ondertekende productieversie (APK, Android App Bundle).

## Inhoud van de repository

```
exemples_kotlin/           de 25 korte voorbeelden (één enkel Android Studio-project)
rdvmedecins-nestjs-json/   de JSON-server van RdvMedecins (de "blackbox")
rdvmedecins_kotlin/        de Android-client voor de casus
tools/                    de Windows-scripts die schermafbeeldingen genereren
```

Elk project kunt u openen in Android Studio (`File / Open`) of compileren vanaf de opdrachtregel: `gradlew installDebug`. De apps verbinden met de server op `http://localhost:8080`, na het uitvoeren van `adb reverse tcp:8080 tcp:8080` (of een ander adres: `gradlew installDebug -Pserver=http://10.0.2.2:8080`).

## Technologieën

Kotlin 2.2 · Jetpack Compose (BOM 2026.02) · Material 3 · Navigation Compose 2.9 · Lifecycle / ViewModel 2.9 · DataStore 1.1 · AppCompat 1.7 · OkHttp 4.12 · kotlinx.serialization 1.9 · AndroidSVG 1.4 · Compose UI Test · AGP 9.4 · Gradle 9.6 · Android 8.0 (API 26) tot Android 17 (API 37) · aan de serverzijde: NestJS 10 · TypeORM · MySQL 8 / MariaDB · Passport JWT · svg-captcha

## Vereisten

- De basis van de taal Kotlin: de cursus [Kotlin](https://stahe.github.io/kotlin-oct-2026/) is een goede voorbereiding, maar de gebruikte concepten worden in de loop van de voorbeelden uitgelegd. De basis van het HTTP-protocol.
- Android Studio (dat de JDK, Gradle, de Android SDK en de emulator levert) of een Android-telefoon; Node.js en een MySQL-server (bijv. Laragon onder Windows) voor de JSON-server. Installatie-instructies staan in de cursus.

## Auteur

Deze cursus, de voorbeelden en de Android-client zijn geschreven door **Claude**, de AI van [Anthropic](https://www.anthropic.com) (oktober 2026), in opdracht van **Serge Tahé**.
