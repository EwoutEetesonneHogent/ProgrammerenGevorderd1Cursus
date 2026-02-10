# WPF – Windows Presentation Foundation

## Inleiding

WPF (Windows Presentation Foundation) is een framework van Microsoft om grafische user interface-applicaties te maken in C#. Tot nu toe hebben we enkel met console-applicaties gewerkt. Een console-applicatie is eenvoudig: je typt er iets in en haalt er iets uit. Maar zodra een applicatie meer informatie moet tonen of meer acties moet toelaten van de gebruiker, merken we snel dat de console zijn beperkingen heeft. Daarom stappen we over naar grafische user interfaces met WPF.

### Voordelen van WPF

Een groot voordeel van WPF is **databinding**: we kunnen in onze code bijvoorbeeld een lijst van personen definiëren en in WPF een component maken dat die lijst weergeeft. Als we die lijst op de juiste manier updaten, zal het component dat verbonden is met die lijst automatisch de wijzigingen reflecteren. Dat vergt wel wat werk — het werkt niet 100% out of the box — maar eenmaal het opgezet is, is het een zeer snelle manier van werken.

> **Opmerking:** Databinding is een concept dat in veel andere front-end frameworks ook terugkomt (React, Angular, Vue, etc.). Als je het concept nu al begrijpt in een eenvoudiger systeem als WPF, zul je het later veel sneller oppikken in andere technologieën.

### WPF in context

- **Voorganger:** WinForms — je zult dit nog vaak tegenkomen op het internet (bijv. Stack Overflow). WinForms is niet volledig deprecated, maar WPF kan alles wat WinForms kan en meer.
- **Opvolgers:** .NET MAUI (cross-platform), WinUI (voor Windows 8+ stijl apps). MAUI is gericht op cross-platform ontwikkeling (Windows, Android, iOS), maar er kruipt veel meer werk in het maken van een MAUI-interface dan in een WPF-interface.
- **Platformbeperking:** WPF draait enkel op Windows. Wie op Mac werkt, moet goed zoeken of WPF aan de praat te krijgen is. Probeer dit op voorhand uit — niet de week voor de eindevaluatie!

### Waarom nog WPF leren?

WPF is misschien niet de meest recente technologie, maar het biedt een perfecte balans:

- Het is **niet irrelevant of deprecated** — het wordt nog steeds gebruikt, alleen niet meer voor grote business-applicaties.
- Het is **relatief eenvoudig** — we hoeven geen drie lessen te besteden voordat we een werkende button op het scherm krijgen.
- De **vaardigheden zijn overdraagbaar** — concepten zoals databinding, event handling en layout management komen in alle moderne front-end frameworks terug.

In de industrie wordt meestal een C#-backend geschreven (bijv. ASP.NET) die gekoppeld wordt aan een web-frontend (React, Angular, Vue, etc.). De vraag naar C# front-end developers is beperkt, maar het is altijd handig om snel een user interface in elkaar te kunnen zetten — al is het maar voor een intern tooltje of een snelle demo voor de klant.

---

## Installatie

Om WPF te gebruiken moet het juiste onderdeel in Visual Studio geïnstalleerd zijn:

1. Open **Visual Studio Installer**
2. Klik op **Modify**
3. Vink het blokje **".NET Desktop Development"** aan
4. Klik op **Modify** om te installeren

Niet alles in dat workload-pakket is nodig (bijv. Blend voor Visual Studio of GitHub Copilot), maar het belangrijkste is dat .NET Desktop Development aanstaat.

> **Tip:** Test dit op voorhand! Wacht niet tot de week voor de eindevaluatie om te ontdekken dat WPF niet geïnstalleerd is.

---

## De architectuur van WPF

### Lagen binnen WPF

Het principe van lagen is ons niet vreemd — we kennen het drielagenmodel met een presentatielaag, domeinlaag en persistentielaag. Binnen de presentatielaag van een WPF-applicatie kunnen we verder inzoomen en zien we verschillende onderdelen:

1. **Programmacode (POCO's):** Gewone C#-klassen zonder WPF-specifieke kennis. Dit zijn onze "Plain Old C# Objects".
2. **Code-behind:** Per window een C#-klasse die de logica bevat om componenten aan te spreken.
3. **Visueel gedeelte:** De interface die de gebruiker ziet, te bekijken en bewerken via twee views:
   - **Designer view** — een What You See Is What You Get (WYSIWYG) editor
   - **XAML view** — een XML-achtige definitietaal

### Designer vs. XAML

De designer en de XAML tonen **exact dezelfde informatie**, maar op twee verschillende manieren:

- **Designer:** Grafisch, snel, drag-and-drop. Handig voor eenvoudige layouts, maar wordt al snel beperkt bij complexere opstellingen.
- **XAML:** Tekst-gebaseerd, meer controle, beter voor complexe en schaalbare layouts.

Een aanpassing in de XAML wordt direct zichtbaar in de designer en omgekeerd. Ze zijn altijd gesynchroniseerd.

> **Tip:** In Visual Studio kun je de weergave aanpassen: vertical split, horizontal split, of slechts één van de twee tonen. Je kunt de XAML-editor ook uitpoppen naar een apart scherm.

### Handige vensters in de Designer

- **Toolbox:** Een lijst van alle beschikbare WPF-componenten (Button, Image, Label, ListBox, ComboBox, Calendar, Expander, etc.).
- **Document Outline:** Toont de hiërarchie van je window — handig naarmate je layout complexer wordt. Je kunt hier ook tijdelijk elementen verbergen zonder ze in commentaar te zetten.
- **Properties:** Alle instelbare eigenschappen van het geselecteerde component (achtergrondkleur, font size, marges, hoogte, breedte, etc.). Een button heeft gemakkelijk 100+ aanpasbare properties.

---

## XAML (eXtensible Application Markup Language)

XAML staat voor **eXtensible Application Markup Language**. Het verschilt slechts één letter van XML (eXtensible Markup Language) — en dat is geen toeval. XAML is een WPF-specifieke variant van XML.

### Basisstructuur

De structuur van XAML-tags is vergelijkbaar met XML en HTML:

```xml
<!-- Open en sluit tag -->
<Button>Klik hier</Button>

<!-- Self-closing tag -->
<Button Content="Klik hier" />
```

### Belangrijke regels

- **Tags zijn hoofdlettergevoelig.** We schrijven ze met een hoofdletter omdat ze verwijzen naar C#-klassen (bijv. `Button`, `Image`, `Grid`). Met een kleine letter herkent de compiler ze niet.
- **XAML is géén HTML.** Je kunt hier geen `<div>`, `<section>` of `<a>` gebruiken. Alle tags zijn WPF-specifiek.
- **Attributen** worden op dezelfde manier meegegeven als in HTML/XML.

### Properties definiëren: twee manieren

**Inline (op de tag zelf):**

```xml
<Button Content="Klik mij" FontWeight="Bold" FontSize="50" />
```

**Als geneste elementen (property element syntax):**

```xml
<Button>
    <Button.Content>Klik mij</Button.Content>
    <Button.FontWeight>Bold</Button.FontWeight>
    <Button.FontSize>50</Button.FontSize>
</Button>
```

Functioneel is er **geen verschil** tussen deze twee manieren. De tweede vorm is leesbaarder wanneer een element veel attributen heeft.

---

## Het Window

Alles begint bij het **Window**. Elke WPF-applicatie die iets visueel wil tonen, heeft minstens één window nodig.

### Eigenschappen van een Window

- **Icon:** Een icoontje in de titelbalk (bijv. een potloodje voor een edit-window).
- **Title:** De titel die in de titelbalk wordt getoond (vergelijkbaar met het `<title>`-element in HTML).
- **Systeemknoppen:** Minimaliseren, maximaliseren, sluiten — deze kun je ook via WPF aansturen.
- **Eén child component:** Een window kan maximaal **één rechtstreekse child** bevatten.

### Waarom maar één child?

Een window heeft, net als een button, een `Content`-attribuut. In dat content-attribuut kan slechts één element zitten. De oplossing: we plaatsen een **panel** als child, en in dat panel plaatsen we vervolgens zoveel elementen als we willen.

> **Foutmelding:** Als je de foutmelding "Window can only have one content attribute" krijgt, is de oplossing altijd: gebruik een panel.

---

## Gemeenschappelijke attributen van WPF-componenten

Alle WPF-componenten delen een aantal attributen:

### Name

Een unieke identifier voor je component, vergelijkbaar met het `id`-attribuut in HTML.

- De `Name` is **niet verplicht**. Als je er geen meegeeft, genereert de compiler automatisch namen (bijv. `Button1`, `Button2`, `Button3`).
- Als je er wel een meegeeft, **moet die uniek** zijn binnen het window.
- De `Name` is essentieel voor de code-behind: hiermee spreken we componenten aan vanuit C#.

### Width en Height

Alle componenten kunnen een breedte en hoogte hebben:

- **Vaste waarde:** bijv. `Width="100"` — het component is altijd 100 pixels breed.
- **Auto:** het component neemt de grootte aan die het zelf nodig denkt te hebben. Bij een button is dat gebaseerd op de tekst die erin staat; bij een image op de standaard afmetingen van de afbeelding.

Je kunt ook **MinWidth/MinHeight** en **MaxWidth/MaxHeight** instellen om grenzen te stellen aan de schaalbaarheid.

> **Let op:** Stel geen min en max op bijna dezelfde waarde in (bijv. MinWidth="200" en MaxWidth="201") om schaalproblemen te omzeilen — dat vliegt niet op bij een evaluatie.

### Margin

Margin bepaalt de afstand van het element tot zijn **parent-element** (niet tot het window!).

```xml
<!-- 10 pixels afstand aan alle zijden van de parent -->
<Button Margin="10" />

<!-- Afzonderlijke waarden: links, boven, rechts, onder -->
<Button Margin="10,20,10,20" />
```

Voorbeeld: als je in een button een image steekt met `Margin="10"`, blijft die image 10 pixels van de rand van de **button** — niet van het window.

### Padding

Padding werkt in de omgekeerde richting van margin: het element houdt zijn **children** op afstand van zijn eigen rand.

### IsEnabled

Bepaalt of een component interactief is:

```xml
<!-- Button is zichtbaar maar niet aanklikbaar -->
<Button IsEnabled="False" Content="Uitgeschakeld" />
```

Om een element onzichtbaar te maken, gebruik je `Opacity` of `Visibility` — dat zijn aparte properties.

### HorizontalAlignment en VerticalAlignment

Zeer krachtige properties om te bepalen waar een component zich positioneert:

**HorizontalAlignment:**

| Waarde | Gedrag |
|--------|--------|
| `Left` | Plakt aan de linkerkant van de parent |
| `Right` | Plakt aan de rechterkant van de parent |
| `Center` | Gecentreerd horizontaal |
| `Stretch` | Neemt de volledige beschikbare breedte in **(standaard)** |

**VerticalAlignment:**

| Waarde | Gedrag |
|--------|--------|
| `Top` | Plakt aan de bovenkant van de parent |
| `Bottom` | Plakt aan de onderkant van de parent |
| `Center` | Gecentreerd verticaal |
| `Stretch` | Neemt de volledige beschikbare hoogte in **(standaard)** |

> **Standaardwaarde:** Zowel HorizontalAlignment als VerticalAlignment staan standaard op **Stretch**. Een component zal zich altijd proberen uit te rekken, tenzij:
> - Een andere alignment wordt ingesteld
> - Een vaste Width/Height wordt meegegeven
> - Een parent panel het overschrijft (bijv. StackPanel)

**Voorrangsregels:**

- `Width` overschrijft altijd `Stretch` — als je een vaste breedte instelt, wordt stretch genegeerd.
- Als `Margin` en `Width` conflicteren (bijv. Margin="100" en Width="100" terwijl het scherm te klein is), kan een deel van het component buiten beeld vallen.

### Voorbeeld: Alignment in actie

```xml
<Grid>
    <!-- Linksboven -->
    <Button Content="Linksboven"
            HorizontalAlignment="Left"
            VerticalAlignment="Top"
            Width="120" Height="35"
            Margin="10" />

    <!-- Boven (meegroeiend in breedte) -->
    <Button Content="Boven"
            VerticalAlignment="Top"
            Height="25"
            Margin="10" />

    <!-- Links (meegroeiend in hoogte) -->
    <Button Content="Links"
            HorizontalAlignment="Left"
            Width="80"
            Margin="10" />

    <!-- Centrum (meegroeiend in beide richtingen) -->
    <Button Content="Centrum"
            Margin="100" />

    <!-- Rechtsonder -->
    <Button Content="Rechtsonder"
            HorizontalAlignment="Right"
            VerticalAlignment="Bottom"
            Width="120" Height="35"
            Margin="10" />
</Grid>
```

Bij het herschalen van het window:
- **Linksboven** blijft altijd op dezelfde afstand van de linkerbovenhoek.
- **Boven** groeit en krimpt mee in breedte, behoudt een vaste hoogte.
- **Links** groeit en krimpt mee in hoogte, behoudt een vaste breedte.
- **Centrum** groeit en krimpt mee in beide richtingen.
- **Rechtsonder** blijft altijd op dezelfde afstand van de rechteronderhoek.

---

## Tekstcomponenten

WPF heeft drie belangrijke tekstcomponenten:

| Component | Doel |
|-----------|------|
| **Label** | Tekst tonen (één regel). Content-property. |
| **TextBlock** | Tekst tonen (meerdere regels). Text-property. |
| **TextBox** | Tekst invoeren door de gebruiker. Text-property. |

> **Verwarrend maar belangrijk:** TextBlock is om tekst te *tonen*, TextBox is om tekst te *typen/bewerken*.

---

## Buttons, RadioButtons en CheckBoxes

- **Button:** Heeft een `Content`-property waarin tekst, maar ook een image of ander element kan staan.
- **CheckBox:** Kan onafhankelijk aangevinkt worden — meerdere checkboxes kunnen tegelijk aangevinkt zijn.
- **RadioButton:** Wordt gegroepeerd per parent-element. Binnen één groep kan slechts één radiobutton geselecteerd zijn. Gebruik `GroupName` als je meerdere sets radiobuttons onder dezelfde parent hebt.

---

## Lijstcomponenten

| Component | Beschrijving |
|-----------|-------------|
| **ListBox** | Toont een eenvoudige lijst van items |
| **ComboBox** | Een openklapbare ListBox (dropdown) |
| **DataGrid** | Gestructureerde weergave van data in rijen en kolommen. Leidt automatisch kolommen af van de properties van je objecten. |

De DataGrid is bijzonder handig: als je een lijst van `Persoon`-objecten met een `Voornaam` en `Achternaam` property erin steekt, maakt hij automatisch twee kolommen aan. Dit gedrag kun je uitzetten en zelf configureren.

---

## Panels

Een panel is een component dat visueel weinig of niets voorstelt, maar gemaakt is om **andere elementen te bevatten**. Aangezien een window slechts één rechtstreekse child kan hebben, plaatsen we in 99% van de gevallen een panel als eerste child.

### Canvas (NIET gebruiken!)

De Canvas laat je elementen positioneren met absolute coördinaten (links/rechts/boven/onder van de rand). Dit levert geen schaalbare layouts op en wordt daarom **niet aangeraden**.

### WrapPanel

Plaatst elementen naast elkaar tot er geen plaats meer is, en wrapt dan naar de volgende rij.

- **Standaard oriëntatie:** Horizontaal
- **Herschaling:** Als het venster kleiner wordt, schuiven elementen automatisch door naar de volgende rij.

### StackPanel

Stapelt elementen op zonder ze te wrappen.

- **Verticaal (standaard):** Plaatst elementen onder elkaar en rekt ze horizontaal uit.
- **Horizontaal:** Plaatst elementen naast elkaar en rekt ze verticaal uit.

> **Let op:** Een StackPanel overschrijft de alignment van zijn children in de stapelrichting. Als een button in een verticaal StackPanel staat, wordt de `VerticalAlignment` van die button genegeerd — het StackPanel bepaalt de positie.

### Grid

Het meest flexibele en interessante panel. Iets complexer om op te zetten, maar het biedt de meeste controle.

#### Column- en RowDefinitions

Standaard heeft een Grid één rij en één kolom. Om een bruikbare layout te maken, definiëren we er meer:

```xml
<Grid>
    <Grid.RowDefinitions>
        <RowDefinition Height="20" />
        <RowDefinition Height="*" />
        <RowDefinition Height="20" />
    </Grid.RowDefinitions>
    <Grid.ColumnDefinitions>
        <ColumnDefinition Width="1*" />
        <ColumnDefinition Width="2*" />
    </Grid.ColumnDefinitions>
</Grid>
```

#### Drie manieren om breedte/hoogte in te stellen

**Auto:**
```xml
<ColumnDefinition Width="Auto" />
```
De kolom neemt de breedte aan die de elementen erin nodig hebben. Herschaalt niet mee met het venster.

**Vast (hardcoded):**
```xml
<ColumnDefinition Width="100" />
```
De kolom is altijd precies 100 pixels breed.

**Star-operator (*):**
```xml
<ColumnDefinition Width="1*" />
<ColumnDefinition Width="2*" />
```
Verdeelt de **resterende ruimte** proportioneel. In dit voorbeeld:
- Totaal: 1 + 2 = 3 delen
- Kolom 1 krijgt 1/3 van de resterende ruimte
- Kolom 2 krijgt 2/3 van de resterende ruimte

Bij een vensterbreedte van 300 pixels: kolom 1 = 100px, kolom 2 = 200px.
Bij 600 pixels: kolom 1 = 200px, kolom 2 = 400px.

De star-operator maakt layouts die **mooi meeschalen** met het venster.

#### Panels nesten

Je kunt panels in panels plaatsen: een Grid in een WrapPanel in een StackPanel, enzovoort. Zoveel nesten als nodig.

---

## App.xaml en het opstartproces

Een WPF-project bevat standaard drie belangrijke bestanden:

1. **App.xaml** — Het startpunt van de applicatie.
2. **MainWindow.xaml** — Het standaard eerste venster.
3. **AssemblyInfo.cs** — Metadata, hier blijven we vanaf.

### StartupUri

In `App.xaml` staat een `StartupUri` die bepaalt welk window geopend wordt bij het opstarten:

```xml
<Application x:Class="MijnApp.App"
             StartupUri="MainWindow.xaml">
</Application>
```

Als je je window hernoemt, moet je deze verwijzing ook aanpassen. Anders krijg je een `IOException`:

```
Cannot locate resource 'mainwindow.xaml'
```

### Windows hernoemen: vijf plaatsen

Als je een window wilt hernoemen, moet dat op minstens **vijf plaatsen** correct staan:

1. De bestandsnaam van het `.xaml`-bestand
2. De bestandsnaam van het `.xaml.cs`-bestand
3. De klassenaam in de code-behind
4. De constructornaam in de code-behind
5. De `x:Class` verwijzing in de XAML

> **Tip:** Gebruik **F2** (Rename) in Visual Studio om klassenamen te hernoemen — dat past alle referenties in één keer aan. Maar het is vaak **makkelijker om een nieuw window aan te maken** en het oude te verwijderen, dan om alles handmatig te hernoemen.

---

## Code-Behind

Elk window is een C#-klasse die uit twee delen bestaat:

1. **De XAML-definitie:** De visuele elementen (buttons, labels, grids, etc.)
2. **De code-behind:** Een C#-klasse met de logica

### Het partial keyword

De code-behind klasse heeft het `partial` keyword:

```csharp
public partial class MainWindow : Window
{
    public MainWindow()
    {
        InitializeComponent();
    }
}
```

`partial` betekent dat deze klasse door de compiler **gecombineerd** wordt met een andere klasse. De compiler zet de XAML om naar C#-code die stap voor stap de visuele elementen opbouwt, en voegt die samen met de code-behind tot één volwaardige klasse.

> **Vergelijking:** De XAML-code `<Button Content="Hallo" FontSize="50"/>` wordt achter de schermen omgezet naar iets als: "maak een nieuw Button-object aan, stel Content in op 'Hallo', stel FontSize in op 50, voeg toe aan het parent panel."

### InitializeComponent()

De methode `InitializeComponent()` staat standaard in de constructor. Laat deze staan — je applicatie werkt mogelijk nog zonder, maar deze methode initialiseert alle visuele componenten. Blijf ervan af.

### Code-behind en vensters: de vuistregel

Elk window heeft precies **één** code-behind bestand. Als je 5 windows hebt, heb je 5 code-behind bestanden. Als je er 20 hebt, heb je er 20. Zo simpel is het.

---

## MessageBox

De `MessageBox` toont een pop-up venster. Handig voor bevestigingen, foutmeldingen of snelle debugging.

### Eenvoudig gebruik

```csharp
MessageBox.Show("Hello World");
```

### Met titel, knoppen en icoon

```csharp
MessageBox.Show(
    "Weet u zeker dat u dit item wilt verwijderen?",  // Bericht
    "Bevestiging",                                      // Titel
    MessageBoxButton.YesNoCancel,                       // Knoppen
    MessageBoxImage.Question                            // Icoon
);
```

**Beschikbare MessageBoxButton-waarden:**
- `OK`
- `OKCancel`
- `YesNo`
- `YesNoCancel`

**Beschikbare MessageBoxImage-waarden** (4 unieke iconen):
- `Information` / `Asterisk` — blauw info-icoon
- `Warning` / `Exclamation` — gele driehoek
- `Error` / `Hand` / `Stop` — rood icoon
- `Question` — vraagteken

### Resultaat opvragen

De `MessageBox.Show()` methode retourneert een `MessageBoxResult` die aangeeft op welke knop geklikt is:

```csharp
MessageBoxResult resultaat = MessageBox.Show(
    "Wilt u doorgaan?",
    "Bevestiging",
    MessageBoxButton.YesNo,
    MessageBoxImage.Warning
);

if (resultaat == MessageBoxResult.Yes)
{
    // Gebruiker klikte op Yes
}
```

> **Tip voor debugging:** Breakpoints zijn de beste manier om te debuggen, maar voor een snelle output kan een MessageBox handig zijn. Gebruik dit uiteraard niet in productiecode — gebruik dan logging.

---

## Events en Event Handlers

### Wat is een event?

Een event is een **gebeurtenis** waar je op kunt abonneren. Componenten vuren continu events af:

- **Click** — als er op een button geklikt wordt
- **MouseEnter** — als de muis over een component beweegt
- **MouseLeave** — als de muis een component verlaat
- **MouseDoubleClick** — bij dubbelklikken
- **Checked** — als een checkbox of radiobutton aangevinkt wordt
- **Loaded** — als een component ingeladen is
- **TextChanged** — als tekst in een TextBox verandert
- **SelectionChanged** — als een selectie in een ListBox verandert

### Event handler aanmaken

Er zijn meerdere manieren om een event handler te koppelen:

**Via het Properties-venster (bliksemschicht-icoon):**
1. Selecteer het component
2. Klik op het bliksemschicht-icoon (⚡) in het Properties-venster
3. Zoek het gewenste event (bijv. `Click`)
4. Typ een naam (bijv. `BtnOpslaan_Click`) en druk op Enter
5. Visual Studio genereert de methode in de code-behind én koppelt het event in de XAML

**Via de XAML:**
```xml
<Button Content="Klik mij" Click="BtnOpslaan_Click" />
```

Bij het typen van `Click="` toont IntelliSense een suggestie `<New Event Handler>`. Klik op Tab om automatisch een handler te laten genereren.

**Via dubbelklik in Properties:**
Dubbelklik op het event-veld in het Properties-venster. Visual Studio genereert automatisch een naam en de bijbehorende methode.

### Signatuur van een event handler

Elke event handler heeft twee parameters:

```csharp
private void BtnOpslaan_Click(object sender, RoutedEventArgs e)
{
    // sender = het object dat het event heeft verstuurd
    // e = extra informatie over het event
}
```

**Parameter 1: `object sender`**

Het object dat het event heeft verstuurd. In dit geval de button. Aangezien het type `object` is (de ultieme basisklasse), moeten we casten:

```csharp
if (sender is Button senderButton)
{
    senderButton.Content = "Ik ben geklikt!";
}
```

**Parameter 2: EventArgs (of een afgeleide)**

Bevat informatie over het event. Het specifieke type hangt af van het event:

| Event | EventArgs type |
|-------|---------------|
| Click | `RoutedEventArgs` |
| MouseDown/Up | `MouseButtonEventArgs` |
| Drop | `DragEventArgs` |
| TextChanged | `TextChangedEventArgs` |
| KeyDown | `KeyEventArgs` |

`MouseButtonEventArgs` bevat bijvoorbeeld welke muisknop ingedrukt is (`Left`, `Right`, `Middle`) en hoeveel keer er geklikt is.

> **Belangrijk:** De signatuur moet exact overeenkomen met wat het event verwacht. Je kunt het type van de EventArgs **breder** maken (bijv. `EventArgs` in plaats van `RoutedEventArgs`), maar **niet smaller** (bijv. `Button` in plaats van `object` voor de sender). Dit heet covariance/contravariance.

### Veelvoorkomende fouten met events

1. **Naam mismatch:** De naam in de XAML (`Click="BtnKlik"`) komt niet overeen met de methodenaam in de code-behind (`BtnClick`). Foutmelding: *"does not contain a definition for..."*
2. **Verkeerde signatuur:** De parameters van de methode komen niet overeen met wat het event verwacht.

> **Tip:** Laat Visual Studio de event handler genereren in plaats van hem handmatig te typen. Dat voorkomt typfouten en onjuiste signaturen.

### Eén handler voor meerdere componenten

Je kunt dezelfde event handler aan meerdere componenten koppelen:

```xml
<Button Name="BtnEen" Content="Knop 1" Click="AlleKnoppen_Click" />
<Button Name="BtnTwee" Content="Knop 2" Click="AlleKnoppen_Click" />
<Button Name="BtnDrie" Content="Knop 3" Click="AlleKnoppen_Click" />
```

In de code-behind kun je dan via de `sender` differentiëren:

```csharp
private void AlleKnoppen_Click(object sender, RoutedEventArgs e)
{
    if (sender is Button button)
    {
        button.Content = button.Name + " is geklikt!";
    }
}
```

Op deze manier vermijd je een `switch`-case of vijf aparte `if`-statements.

---

## Multi-Window Applicaties

### Startup aanpassen

Standaard gebruikt een WPF-applicatie `StartupUri` om het eerste window te openen. Voor meer controle kunnen we dit vervangen door het **Startup-event**:

1. Verwijder de `StartupUri` property uit `App.xaml`
2. Abonneer op het `Startup`-event (via het Properties-venster of in XAML)
3. Maak en toon je window handmatig:

```csharp
// In App.xaml.cs
private void Application_Startup(object sender, StartupEventArgs e)
{
    FirstWindow window = new FirstWindow();
    window.Show();
}
```

Dit is vooral nuttig bij het drielagenmodel, waar we bij het opstarten eerst configuratie willen uitvoeren (database verbinden, validatie checken, etc.) voordat we een window tonen.

> **Let op:** Als je enkel het Startup-event definieert maar vergeet een window te openen, draait de applicatie in de achtergrond zonder iets te tonen!

### Navigeren tussen vensters

Om een nieuw window te openen en het huidige te sluiten:

```csharp
private void BtnOpenSecondWindow_Click(object sender, RoutedEventArgs e)
{
    SecondWindow secondWindow = new SecondWindow();
    secondWindow.Show();
    this.Close();
}
```

> **Volgorde is belangrijk!** Maak **eerst** het nieuwe window aan en toon het, en **sluit daarna pas** het huidige. Anders kan de applicatie onverwacht afsluiten (zie: ShutdownMode).

### ShutdownMode

De `ShutdownMode` property in `App.xaml` bepaalt wanneer de applicatie afsluit:

| Waarde | Gedrag |
|--------|--------|
| `OnLastWindowClose` **(standaard)** | De applicatie sluit af zodra het laatste window gesloten wordt |
| `OnMainWindowClose` | De applicatie sluit af zodra het hoofdvenster gesloten wordt |
| `OnExplicitShutdown` | De applicatie sluit pas af als je expliciet `Application.Current.Shutdown()` aanroept |

Met de standaard `OnLastWindowClose` geldt: als je het huidige window sluit vóórdat je het nieuwe opent, zijn er even **nul** open vensters. De applicatie denkt dan dat alles gesloten is en sluit zichzelf af.

### Close vs. Hide

| Methode | Effect |
|---------|--------|
| `window.Close()` | Het window wordt **verwijderd**. Alle data erin is weg. Een nieuw `new FirstWindow()` is een volledig nieuw object. |
| `window.Hide()` | Het window wordt **verborgen** maar blijft bestaan. Data en state blijven behouden. Je kunt het later opnieuw tonen met `window.Show()`. |

**Wanneer Close gebruiken:** Als je de data in het window niet meer nodig hebt.

**Wanneer Hide gebruiken:** Als je de data wilt behouden, bijv. als een gebruiker al 4 boeken heeft gezocht voor een reservatie en even naar zijn profiel wil navigeren.

> **Let op bij Hide:** Een verborgen window telt nog als "open" voor de ShutdownMode. Als al je zichtbare vensters gesloten zijn maar er nog een hidden window bestaat, blijft de applicatie draaien in de achtergrond.

---

## DispatcherUnhandledException

### Wat is een Unhandled Exception?

Wanneer een exception nergens in de code wordt opgevangen (door een try-catch), propageert die omhoog via de stack trace. Als ze helemaal bovenaan komt zonder opgevangen te worden, is het een **unhandled exception** en zal de applicatie crashen.

### Het event afhandelen

In `App.xaml` kunnen we het `DispatcherUnhandledException`-event afhandelen. Dit wordt getriggerd **net voordat** de applicatie zou crashen:

```csharp
// In App.xaml.cs
private void App_DispatcherUnhandledException(object sender,
    System.Windows.Threading.DispatcherUnhandledExceptionEventArgs e)
{
    // 1. Log wegschrijven
    string logBestand = DateTime.Now.ToString("yyyyMMdd_HHmmss") + ".txt";
    File.WriteAllText(logBestand, e.Exception.ToString());

    // 2. Gebruiker informeren
    MessageBox.Show(
        "Er is een onverwachte fout opgetreden. De applicatie wordt afgesloten.",
        e.Exception.Message,
        MessageBoxButton.OK,
        MessageBoxImage.Error
    );

    // OPTIONEEL: crashen voorkomen (NIET aanbevolen!)
    // e.Handled = true;
}
```

### Wat zit er in de Exception?

De `e.Exception.ToString()` bevat:
- Het **type** van de exception (bijv. `NotImplementedException`)
- De **message** (bijv. "Crash me Daddy")
- De volledige **stack trace** — welke functies zijn doorlopen

### Handled = true: waarom NIET doen

Je kunt `e.Handled = true` instellen om te voorkomen dat de applicatie crasht. Dit wordt **sterk afgeraden**, want:

- Ergens in je applicatie is een functie halverwege gestopt.
- Mogelijk zijn meerdere functies halverwege afgebroken.
- De applicatie bevindt zich in een onvoorspelbare toestand.
- Dit kan allerlei onverwachte problemen veroorzaken.

**In de meeste gevallen is crashen de juiste oplossing.** Het DispatcherUnhandledException-event is bedoeld als **laatste verdedigingslinie** om:
- Log files weg te schrijven
- De gebruiker te informeren

Het is **geen vervanging** voor try-catch blokken in je code! Hoe eerder in je code je exceptions opvangt, hoe beter je het probleem kunt afhandelen.

> **Vergelijking:** Stel je webservice is offline. Met een try-catch in je code kun je de gebruiker melden: "De service is niet bereikbaar, maar we hebben een lokale kopie van gisteren. Wilt u die gebruiken?" Met enkel een DispatcherUnhandledException kun je alleen maar zeggen: "Er ging iets fout. Doei."

---

## Modellen vs. DTO's: naamgeving

Een veelgestelde vraag is of er in de domeinlaag met **modellen** of met **DTO's** gewerkt moet worden.

De aanpak:
- **Domeinlaag:** Gebruik modellen (met constructoren, validatie, setters, etc.)
- **Presentatielaag:** Gebruik presentatie-specifieke DTO's
- **Persistentielaag:** Gebruik persistentie-specifieke DTO's (ook wel "entries" genoemd)

Wat betreft naamgeving mag je zelf kiezen wat het duidelijkst is:
- `ProductDTO` en `Product` (model)
- `Product` (DTO) en `ProductModel`
- Of zelfs beide gewoon `Product`, onderscheiden door hun **namespace**

> **Tip:** In een gevorderd vak leer je werken met dezelfde naam in verschillende namespaces. Voorlopig is het prima om een achtervoegsel te gebruiken dat voor jou duidelijk is, zolang de naam logisch is.

---

## Samenvatting

| Concept | Kernpunt |
|---------|---------|
| WPF | Microsoft framework voor grafische UI's in C#, Windows-only |
| XAML | XML-achtige taal om de UI te definiëren, gesynchroniseerd met de designer |
| Window | Rootcomponent, maximaal 1 direct child |
| Panels | Grid, StackPanel, WrapPanel — om meerdere elementen te bevatten |
| Grid | Meest flexibel panel, werkt met rij/kolom definities en star-operator |
| Code-behind | C#-klasse achter elk window, gecombineerd via `partial` keyword |
| Events | Gebeurtenissen op componenten, gekoppeld via event handlers |
| MessageBox | Pop-up voor berichten, bevestigingen en simpele debugging |
| Multi-window | Meerdere vensters, let op ShutdownMode en volgorde van open/close |
| DispatcherUnhandledException | Laatste kans om te loggen/informeren bij een onafgehandelde exception |
