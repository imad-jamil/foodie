# Foodie - FLYT-skjema

Dette dokumentet viser flyten i appen, fra oppstart til alle hovedfunksjoner.

## 1. App Oppstart & Autentisering

```mermaid
flowchart TD
    Start([App Starter]) --> LoadFonts[Last inn Nunito fonts]
    LoadFonts --> ShowSplash[Vis Custom Splash Screen]
    ShowSplash --> FetchLocation[Hent GPS-lokasjon]
    FetchLocation --> FetchRestaurants[Hent ALLE restauranter<br/>innen 50km radius]

    FetchRestaurants --> CheckAuth{Sjekk<br/>autentisering}
    CheckAuth -->|Ny bruker| ShowOnboarding[Vis Onboarding]
    CheckAuth -->|Eksisterende bruker| LoadUserData[Last brukerdata]

    ShowOnboarding --> CompleteOnboarding[Fullfør onboarding<br/>- Navn<br/>- Brukernavn<br/>- Diett preferanser]
    CompleteOnboarding --> CreateUser[Opprett brukerprofil<br/>i Supabase]
    CreateUser --> LoadUserData

    LoadUserData --> LoadLists[Last brukerens lister]
    LoadUserData --> LoadActivities[Last brukerens aktiviteter]
    LoadUserData --> LoadFeed[Last feed aktiviteter]

    LoadLists --> HideSplash[Skjul Splash Screen]
    LoadActivities --> HideSplash
    LoadFeed --> HideSplash

    HideSplash --> MainApp[Vis Hovedapp]
```

## 2. Hovednavigasjon (Bottom Tabs)

```mermaid
flowchart LR
    MainApp([Hovedapp]) --> Tab1[Home Tab]
    MainApp --> Tab2[Restaurants Tab]
    MainApp --> Tab3[Search Button<br/>Senter ⊕]
    MainApp --> Tab4[Maps Tab]
    MainApp --> Tab5[Profile Tab]

    style Tab3 fill:#09292B,color:#fff
```

## 3. Home Screen Flyt

```mermaid
flowchart TD
    Home([Home Screen]) --> ShowLocation[Vis brukerens<br/>lokasjon]
    ShowLocation --> ShowNearby[Vis 4 nærmeste<br/>restauranter]

    ShowNearby --> CalculateDistance[Beregn kjøreavstand<br/>med Google Maps API]

    ShowLocation --> ShowCuisines[Vis Cuisine-kategorier<br/>American, Dessert, Italian, Japanese]

    ShowLocation --> ShowFeed[Vis Activity Feed<br/>Siste 5 aktiviteter]

    Home --> SearchBar{Klikk på<br/>søkefelt?}
    SearchBar -->|Ja| OpenSearch[Åpne Search Screen]

    ShowNearby --> TapCard{Tap på<br/>restaurant?}
    TapCard -->|Ja| OpenBottomSheet[Åpne Restaurant<br/>Bottom Sheet]

    ShowCuisines --> TapCuisine{Tap på<br/>cuisine?}
    TapCuisine -->|Ja| NavigateRestaurants[Naviger til Restaurants Tab<br/>med filter]

    ShowFeed --> InteractFeed{Interaksjon<br/>i feed?}
    InteractFeed -->|Check-in knapp| ToggleVisited[Toggle besøkt status]
    InteractFeed -->|Save/Favorite knapp| ToggleList[Toggle liste]
    InteractFeed -->|Tap kort| OpenBottomSheet
```

## 4. Restaurant Bottom Sheet Flyt

```mermaid
flowchart TD
    BottomSheet([Restaurant<br/>Bottom Sheet]) --> ShowDetails[Vis detaljer:<br/>- Bilde<br/>- Navn & Cuisine<br/>- Adresse<br/>- Kjøreavstand & tid<br/>- Åpningstider<br/>- Prisklasse<br/>- Tags & Highlights]

    ShowDetails --> ActionButtons[Action Buttons]

    ActionButtons --> CheckIn{Check-in<br/>knapp}
    CheckIn -->|Klikk| MarkVisited[Merk som besøkt]
    MarkVisited --> AddToBeenList[Legg til i<br/>'Been' liste]
    MarkVisited --> CreateActivity1[Opprett VISITED<br/>aktivitet]

    ActionButtons --> SaveBtn{Save<br/>knapp}
    SaveBtn -->|Ikke besøkt| AddToToDo[Legg til i<br/>'To Do' liste]
    SaveBtn -->|Besøkt| AddToFavorites[Legg til i<br/>'Favorites' liste]
    AddToToDo --> CreateActivity2[Opprett SAVED<br/>aktivitet]
    AddToFavorites --> CreateActivity3[Opprett FAVORITED<br/>aktivitet]

    ActionButtons --> RateBtn{Rate<br/>knapp}
    RateBtn -->|Klikk| OpenRatingSheet[Åpne Rating Sheet]
    OpenRatingSheet --> EnterRatings[Angi ratings:<br/>- Food<br/>- Atmosphere<br/>- Service<br/>- Review tekst]
    EnterRatings --> SaveReview[Lagre review]
    SaveReview --> CreateActivity4[Opprett REVIEWED<br/>aktivitet]

    ActionButtons --> ShareBtn[Share knapp]
    ActionButtons --> DirectionsBtn[Directions knapp<br/>Åpne Google Maps]
```

## 5. Search Screen Flyt

```mermaid
flowchart TD
    Search([Search Screen]) --> ShowTabs[Vis tabs:<br/>Restaurants, Members]

    ShowTabs --> RestaurantsTab{Restaurants<br/>Tab}
    RestaurantsTab --> SearchBar[Søkefelt<br/>auto-fokusert]
    SearchBar --> TypeQuery{Skriv søk}
    TypeQuery -->|Tom| ShowEmptyState[Vis empty state]
    TypeQuery -->|Tekst| SearchContent[Søk i:<br/>- Navn<br/>- Cuisine<br/>- Kategori<br/>- Tags<br/>- Highlights]

    SearchContent --> ShowResults[Vis resultater<br/>med bilde & adresse]
    ShowResults --> NoResults{Ingen<br/>treff?}
    NoResults -->|Ja| ShowNoResults[Vis ingen resultater]
    NoResults -->|Nei| ShowCount[Vis antall treff]

    ShowResults --> TapResult{Tap på<br/>restaurant?}
    TapResult -->|Ja| OpenBottomSheet2[Åpne Bottom Sheet<br/>Search screen i bakgrunn]

    ShowResults --> CheckInBtn[Check-in knapp]
    ShowResults --> SaveFavBtn[Save/Favorite knapp]

    ShowTabs --> MembersTab{Members<br/>Tab}
    MembersTab --> ComingSoon[Vis Coming Soon]

    Search --> CloseBtn{Klikk X<br/>knapp}
    CloseBtn -->|Ja| ReturnHome[Returner til Home]
```

## 6. Restaurants Tab (YourList) Flyt

```mermaid
flowchart TD
    YourList([Restaurants Tab]) --> ShowListTabs[Vis liste-tabs:<br/>To Do, Been, Favorites]

    ShowListTabs --> SelectTab{Velg tab}
    SelectTab --> ShowRestaurants[Vis restauranter<br/>i valgt liste]

    ShowRestaurants --> ShowFilters[Vis filter-knapper:<br/>Cuisine, Price, Dietary]
    ShowRestaurants --> ShowSort[Vis sortering:<br/>Distance, Date, Rating]

    ShowFilters --> ApplyFilter{Bruk<br/>filter?}
    ApplyFilter -->|Ja| FilterList[Filtrer liste]
    FilterList --> UpdateDisplay[Oppdater visning]

    ShowSort --> ApplySort{Velg<br/>sortering?}
    ApplySort -->|Distance| SortByDistance[Sorter etter<br/>kjøreavstand]
    ApplySort -->|Date| SortByDate[Sorter etter<br/>dato lagt til]
    ApplySort -->|Rating| SortByRating[Sorter etter<br/>rating]

    ShowRestaurants --> CardDesign[Kort design:<br/>- Avrundet bilde venstre<br/>- Navn & cuisine<br/>- Kjøreavstand & tid<br/>- Åpen/Stengt status<br/>- Prisklasse<br/>- Tags<br/>- Visited/New badge]

    CardDesign --> TapCard{Tap på<br/>kort?}
    TapCard -->|Ja| OpenBottomSheet3[Åpne Bottom Sheet]
```

## 7. Maps Screen Flyt

```mermaid
flowchart TD
    Maps([Maps Screen]) --> LoadMap[Last kart<br/>Apple Maps iOS / Google Maps Android]
    LoadMap --> CenterUser[Sentrer på<br/>brukerens lokasjon]

    CenterUser --> ShowPins[Vis alle restauranter<br/>som fargede pins]
    ShowPins --> PinColors[Pin-farger:<br/>🟢 Grønn = Besøkt<br/>🔴 Rød = Favoritt<br/>🟡 Gul = Lagret<br/>⚪ Grå = Ikke besøkt]

    Maps --> MapControls[Kontroller:<br/>- Center knapp<br/>- Filter knapp]

    MapControls --> CenterBtn{Center<br/>knapp}
    CenterBtn -->|Klikk| RecenterMap[Sentrer kart<br/>på bruker]

    MapControls --> FilterBtn{Filter<br/>knapp}
    FilterBtn -->|Klikk| OpenFilterSheet[Åpne Filter Sheet]
    OpenFilterSheet --> FilterOptions[Filter-alternativer:<br/>- Show by list<br/>- Cuisine<br/>- Price<br/>- Dietary]
    FilterOptions --> ApplyMapFilter[Bruk filter]
    ApplyMapFilter --> UpdatePins[Oppdater synlige pins]

    ShowPins --> TapPin1{Første<br/>tap på pin?}
    TapPin1 -->|Ja| ShowCallout[Vis callout<br/>med bilde & navn]
    ShowCallout --> TapPin2{Andre tap<br/>på callout?}
    TapPin2 -->|Ja| OpenBottomSheet4[Åpne Bottom Sheet]
```

## 8. Profile Screen Flyt

```mermaid
flowchart TD
    Profile([Profile Screen]) --> ShowHeader[Vis profil-header:<br/>- Avatar initialene<br/>- Navn<br/>- Member since]

    ShowHeader --> ShowStats[Vis stats:<br/>- Followers 🔒<br/>- Following<br/>- Rank 🔒]

    ShowStats --> ActionButtons[Action-knapper]
    ActionButtons --> EditProfile{Edit Profile<br/>knapp}
    EditProfile -->|Klikk| OpenEditProfile[Åpne Edit Profile Screen]
    OpenEditProfile --> EditFields[Rediger:<br/>- Navn<br/>- Brukernavn]
    EditFields --> SaveProfile[Lagre endringer<br/>i Supabase]

    ActionButtons --> ShareProfile[Share Profile knapp]
    ActionButtons --> MoreOptions[More Options<br/>Åpne Settings]
    MoreOptions --> OpenSettings[Åpne Settings Screen]
    OpenSettings --> SettingsOptions[Innstillinger:<br/>- Dietary Preferences<br/>- Logout]

    Profile --> QuickAccess[Quick Access Rows]
    QuickAccess --> BeenRow{Been<br/>row}
    BeenRow -->|Klikk| NavToBeen[Naviger til<br/>Restaurants tab - Been]

    QuickAccess --> WantToTryRow{Want to Try<br/>row}
    WantToTryRow -->|Klikk| NavToToDo[Naviger til<br/>Restaurants tab - To Do]

    QuickAccess --> RecsRow[Recs for You 🔒]

    Profile --> StatsCards[Stats-kort:<br/>- Rank on Beli 🔒<br/>- Current Streak]

    Profile --> Challenge[2025 Challenge:<br/>- Progresjon mot 50 restauranter<br/>- Progress bar<br/>- Dager igjen]
```

## 9. Data Flow & State Management

```mermaid
flowchart TD
    Store([Zustand Store]) --> RestaurantsState[Restaurants State]
    Store --> UsersState[Users State]
    Store --> ListsState[Lists State]
    Store --> ActivitiesState[Activities State]
    Store --> LocationCache[User Location Cache]
    Store --> DirectionsCache[Directions Cache]

    RestaurantsState --> Supabase1[(Supabase<br/>restaurants table)]
    UsersState --> Supabase2[(Supabase<br/>users table)]
    ListsState --> Supabase3[(Supabase<br/>lists & list_items)]
    ActivitiesState --> Supabase4[(Supabase<br/>activities table)]

    Actions[User Actions] --> OptimisticUpdate[Optimistic UI Update]
    OptimisticUpdate --> UpdateStore[Oppdater Zustand Store]
    UpdateStore --> RenderUI[Render UI umiddelbart]

    OptimisticUpdate --> BackgroundSync[Background Sync<br/>med Supabase]
    BackgroundSync --> Success{Suksess?}
    Success -->|Ja| KeepUpdate[Behold endring]
    Success -->|Nei| RevertUpdate[Tilbakestill endring<br/>Last data på nytt]

    LocationCache --> GoogleMapsAPI[Google Maps<br/>Directions API]
    GoogleMapsAPI --> CalculateRoutes[Beregn<br/>kjøreavstand & tid]
    CalculateRoutes --> CacheResults[Cache resultater<br/>per restaurant]
```

## 10. Nøkkelteknologier

```mermaid
mindmap
  root((Restaurant<br/>Discovery App))
    Frontend
      React Native 0.76.7
      Expo SDK 53
      TypeScript
      Nativewind TailwindCSS
    Navigation
      React Navigation
      Bottom Tabs
      Stack Navigator
    State Management
      Zustand
      AsyncStorage
      Optimistic Updates
    UI Components
      Gorhom Bottom Sheet
      React Native Maps
      Lucide Icons
      Expo Linear Gradient
    Backend
      Supabase
        Authentication
        PostgreSQL Database
        Real-time subscriptions
    APIs
      Google Maps Directions API
      Location Services
        expo-location
    Features
      GPS Location
      Real-time Distance Calculation
      Restaurant Discovery
      Lists & Collections
      Activity Feed
      Reviews & Ratings
```

## 11. Aktivitetstyper & Feed

```mermaid
flowchart LR
    UserAction([Brukerhandling]) --> ActivityType{Aktivitetstype}

    ActivityType -->|Legge til i To Do| SavedActivity[SAVED<br/>Gul bookmark ikon]
    ActivityType -->|Legge til i Favorites| FavoritedActivity[FAVORITED<br/>Rødt hjerte ikon]
    ActivityType -->|Check-in| VisitedActivity[VISITED<br/>Grønn checkmark]
    ActivityType -->|Rate restaurant| RatedActivity[RATED<br/>Vis stjerner]
    ActivityType -->|Write review| ReviewedActivity[REVIEWED<br/>Vis review tekst]

    SavedActivity --> CreateInSupabase[Opprett i<br/>Supabase activities]
    FavoritedActivity --> CreateInSupabase
    VisitedActivity --> CreateInSupabase
    RatedActivity --> CreateInSupabase
    ReviewedActivity --> CreateInSupabase

    CreateInSupabase --> UpdateFeed[Oppdater Activity Feed]
    UpdateFeed --> ShowInHome[Vis i Home Screen<br/>Siste 5 aktiviteter]
```

## 12. Filtrering & Sortering

```mermaid
flowchart TD
    Filters([Filtre & Sortering]) --> CuisineFilter[Cuisine Filter:<br/>American, Dessert,<br/>Italian, Japanese]
    Filters --> PriceFilter[Price Filter:<br/>1-4 dollar signs]
    Filters --> DietaryFilter[Dietary Filter:<br/>Halal, Vegan, Vegetarian]

    Filters --> DistanceSort[Distance Sort:<br/>Kjøreavstand fra bruker]
    Filters --> DateSort[Date Sort:<br/>Nyeste først]
    Filters --> RatingSort[Rating Sort:<br/>Høyeste først]

    CuisineFilter --> ApplyToMaps[Bruk på Maps Screen]
    PriceFilter --> ApplyToMaps
    DietaryFilter --> ApplyToMaps

    CuisineFilter --> ApplyToList[Bruk på Restaurants Tab]
    PriceFilter --> ApplyToList
    DietaryFilter --> ApplyToList

    DistanceSort --> ApplyToList
    DateSort --> ApplyToList
    RatingSort --> ApplyToList

    ApplyToMaps --> UpdatePins[Oppdater synlige pins]
    ApplyToList --> UpdateCards[Oppdater kortliste]
```

---

## Oppsummering av Flyten

### Oppstartssekvens:
1. Last fonts og vis splash screen
2. Hent GPS-lokasjon
3. Hent alle restauranter innen 50km
4. Sjekk autentisering (ny/eksisterende bruker)
5. Hvis ny: Vis onboarding → Opprett profil
6. Hvis eksisterende: Last brukerdata
7. Skjul splash → Vis hovedapp

### Hovedfunksjoner:
- **Home**: Nærmeste restauranter, cuisine-kategorier, activity feed
- **Restaurants**: Lister (To Do, Been, Favorites) med filtrering og sortering
- **Search**: Søk i restauranter med interaktive resultater
- **Maps**: Visuelt kart med fargekodede pins og filtre
- **Profile**: Brukerstatistikk, lister, utfordringer, innstillinger

### Data-flyt:
- Optimistic updates for øyeblikkelig UI-respons
- Background sync med Supabase for persistent lagring
- Caching av lokasjons- og rute-data for ytelse
- Real-time updates av aktiviteter i feeden
