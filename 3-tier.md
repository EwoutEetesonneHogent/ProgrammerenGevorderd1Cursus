# Het 3-Lagen Model in C#

## 1. Introductie: Van werkende code naar goed gestructureerde code

Tot nu toe hebben we vooral gefocust op code die werkt. We schrijven onze code waar het ons uitkomt, misschien met hier en daar wat aandacht voor structuur en verantwoordelijkheden, maar zonder echte, strikte regels. Een klasse die aangemaakt wordt kan van alles doen, maar wat die klasse precies moet doen hangt vaak af van hoe we op dat moment denken.

### De elektriciën-analogie

Het probleem met deze aanpak is te vergelijken met een elektricien die een nieuwbouw komt aansluiten, maar voor elke lamp en elk stopcontact rechtstreeks een kabel trekt vanuit de zekeringkast naar waar die nodig is. Het werkt - je kunt het licht aanzetten en je apparaten gebruiken - maar:

- Als je een stopcontact wilt toevoegen, moet je een hele nieuwe kabel trekken vanaf de zekeringkast
- Als je een licht wilt vervangen, moet je de volledige bedrading aanpassen
- Het is niet proper, niet onderhoudbaar en moeilijk om te begrijpen hoe alles verbonden is

Wanneer we daarentegen een propere zekeringkast opzetten met duidelijke afspraken en best practices, wordt alles:
- Veel makkelijker te onderhouden
- Veel makkelijker uit te breiden
- Veel overzichtelijker

Dit is precies wat we nu gaan doen met onze code.

## 2. Wat is een design pattern?

Een **design pattern** is een set van afspraken over hoe we onze projecten gaan structureren. Het is een breed concept met verschillende implementaties. Binnen design patterns werken we meestal met duidelijke lagen - stukken van onze code die vaste verantwoordelijkheden hebben. Dit zorgt ervoor dat onze code gescheiden wordt en dat we makkelijker één laag kunnen isoleren om aanpassingen te doen.

### Bekende design patterns

Er zijn veel gekende design patterns, zoals:
- **MVC** (Model-View-Controller)
- **MVVM** (Model-View-ViewModel)
- **PAC** (Presentation-Abstraction-Control)
- **Factory pattern**
- **Redux pattern**

### Het juiste pattern voor het juiste probleem

**Belangrijk:** Een design pattern is altijd gemaakt om een bepaald probleem op te lossen. Omdat er verschillende programmeerprobleemzijn, bestaat er niet één "one size fits all" design pattern.

Voorbeelden:
- Voor een **API zonder frontend** (zoals in Gevorderd Programmeren 2): een ander design pattern
- Voor een **Unity game**: weer een ander design pattern
- Voor onze **desktop applicaties met WPF**: het 3-lagen model

Elk type technologie heeft verschillende problemen die opgelost moeten worden, dus vraagt om een andere benadering.

## 3. Het 3-Lagen Model

We gaan werken met het **3-lagen model** (Engels: **3-tier model**). Dit is een vrij eenvoudig design pattern dat perfect geschikt is voor onze toepassingen.

### Overzicht van de lagen

Het model bestaat uit (wonder boven wonder) 3 lagen, waarbij elke laag een duidelijke verantwoordelijkheid heeft:

```
┌─────────────────────────────┐
│   PRESENTATIELAAG           │  <- Wat de gebruiker ziet
│   (Console/WPF)             │
└─────────────────────────────┘
              ↕
┌─────────────────────────────┐
│   DOMEINLAAG                │  <- Het kloppend hart (logica)
│   (Business Logic)          │
└─────────────────────────────┘
              ↕
┌─────────────────────────────┐
│   PERSISTENTIELAAG          │  <- Dataopslag
│   (Database/Files)          │
└─────────────────────────────┘
```

### 3.1 Presentatielaag

De **presentatielaag** bevat alles wat de eindgebruiker ziet en waarmee die interacteert.

**In deze cursus:**
- **Eerst**: Console applicaties (basis)
- **Later**: WPF applicaties (grafische user interface)

**Verantwoordelijkheden:**
- Input van de gebruiker verwerken
- Output teruggeven aan de gebruiker
- Minimale validatie (bijvoorbeeld: is de input wel een getal?)
- **Zo snel mogelijk** doorverwijzen naar de domeinlaag

⚠️ **WAARSCHUWING:** Zet niet te veel logica in de presentatielaag! De presentatielaag is bedoeld om data binnen te krijgen, eventueel een klein beetje transformatie of validatie te doen, maar moet zo snel mogelijk naar de domeinlaag. Al de complexiteit moet in de domeinlaag zitten.

### 3.2 Domeinlaag (Logica-/Business-/Servicelaag)

De **domeinlaag** is het kloppend hart van de applicatie. Deze laag faciliteert de communicatie tussen de andere twee lagen.

**Ook wel genoemd:**
- Logica laag
- Business laag  
- Service laag

**Verantwoordelijkheden:**
- Intelligente verwerking van data
- Validatie afdwingen
- Lijsten interpreteren en aanpassen
- Delegeren tussen de verschillende lagen
- **Platform-onafhankelijke** logica

⚠️ **BELANGRIJK:** De domeinlaag is puur C#. Deze is niet gerelateerd aan een specifiek platform (console, WPF, etc.). Dit zorgt ervoor dat de domeinlaag altijd behouden kan worden, zelfs als we de presentatie- of persistentielaag vervangen.

**Analogie:** Je kunt een arm of een teen verliezen en overleven, maar je hart is essentieel. Hetzelfde geldt voor de domeinlaag - dit is het meest essentiële onderdeel van de applicatie. De rest kunnen we vervangen door iets anders.

### 3.3 Persistentielaag (Datalaag)

De **persistentielaag** is de plaats waar we onze data persisteren - data die langer leeft dan de lifetime van onze applicatie.

**In deze cursus:**
- **Eerst**: In-memory data (in een Collection/List)
- **Later**: ADO.NET met een echte database

**Verantwoordelijkheden:**
- Data opslaan
- Data ophalen
- Data beheren

⚠️ **LET OP:** Bij het afsluiten van de applicatie gaat in-memory data verloren. Later in de cursus gebruiken we databases waar data permanent wordt opgeslagen.

### 3.4 Startupplaag

Sommige docenten werken met slechts 3 lagen en plaatsen de startup-logica in de presentatielaag. Wij gebruiken echter een aparte **startupplaag** om de scheiding tussen lagen strikter te handhaven.

**Waarom een aparte startupplaag?**

Om het project op te starten hebben we ergens een klasse nodig die:
- Elk van de lagen kan opbouwen
- Weet heeft van alle 3 de andere projecten
- Referenties heeft naar alle 3 de lagen

Als we dit in de presentatielaag zouden doen, zou de presentatielaag een directe referentie naar de persistentielaag hebben. Dat willen we vermijden.

**Wat doet de startupplaag?**

De startup voert **3 stappen** uit bij het opstarten:

1. **Persistentielaag aanmaken**: Alle klassen in de persistentielaag worden aangemaakt
2. **Domeinlaag aanmaken**: De domeincontroller/domeinmanager wordt aangemaakt, en referenties naar de persistentielaag worden geïnjecteerd via dependency injection
3. **Presentatielaag aanmaken**: De application klasse wordt aangemaakt en krijgt referenties naar de domeinmanager

Na deze stappen doet de startup **niets meer**. De lagen nemen het over.

## 4. Communicatieregels tussen lagen

### Het belangrijkste principe

De communicatie tussen de presentatielaag en de persistentielaag verloopt **ALTIJD** via de domeinlaag.

```
USER
  ↓
PRESENTATION → DOMAIN → PERSISTENCE
                  ↓           ↓
                  ←───────────┘
  ↑               ↓
  └───────────────┘
```

**Het verloop:**

1. Gebruiker typt een commando in (bijv. "geef me de sales totals")
2. **Presentatie → Domain**: Request wordt doorgestuurd naar de domeinlaag
3. **Domain → Persistence**: Domeinlaag stuurt query door naar de datalaag
4. **Persistence → Domain**: Datalaag haalt data op en geeft terug
5. **Domain → Presentation**: Domeinlaag verwerkt/transformeert data en geeft terug
6. **Presentation → User**: Resultaat wordt getoond aan gebruiker

### 🚨 KRITIEKE REGEL - PUNTENAFTREK

**NOOIT presentatie ↔ persistence rechtstreeks!**

Als de presentatie- en persistentielaag rechtstreeks communiceren, is het duidelijk dat het concept niet begrepen is. Dit levert veel punten verlies op bij:
- Tussentijdse evaluaties
- Eindopdrachten
- Projecten

### De pijlen in het schema

Let op de richting van de pijlen:
- Presentatie → Domein
- Domein → Persistentie  
- Persistentie → Domein
- Domein → Presentatie

**Nooit:** Presentatie → Persistentie of Persistentie → Presentatie

## 5. Opzetten van de projectstructuur

### 5.1 Solution en projecten aanmaken

We beginnen met het aanmaken van een **blank solution** en voegen daar stap voor stap onze projecten aan toe.

**Stap 1: Blank Solution**
```
File → New → Project → Blank Solution
Naam: Wine (of een andere projectnaam)
```

Dit geeft ons een lege solution waarin nog geen projecten zitten. We kunnen deze nog niet opstarten omdat er nog geen code is.

**Stap 2: Startup Project (Console App)**
```
Rechtsklik op solution → Add → New Project
Template: Console App
Naam: Wine.Startup
```

⚠️ **NAAMCONVENTIE:** Gebruik altijd `[SolutionNaam].[ProjectNaam]` als naming pattern. Dit zorgt voor propere namespaces.

**Stap 3: Class Libraries voor de 3 lagen**

Voeg 3 class library projecten toe:

```
Add → New Project → Class Library
Namen:
- Wine.Presentation
- Wine.Domain
- Wine.Persistence
```

**Verschil tussen Console App en Class Library:**
- Een **Console App** kan opgestart worden (heeft een entry point)
- Een **Class Library** is gewoon een verzameling code, kan niet zelfstandig opstarten
- Een solution moet minimaal één opstartbaar project bevatten

### 5.2 Startup project instellen

Als je projecten in de verkeerde volgorde hebt aangemaakt, kan Visual Studio proberen het verkeerde project op te starten.

**Foutmelding:**
```
A project with an Output Type of Class Library cannot be started directly.
```

**Oplossing:**
```
Rechtsklik op Wine.Startup → Set as Startup Project
```

Het startup project wordt **vet** weergegeven in de Solution Explorer en verschijnt naast de start-knop.

### 5.3 Dependencies configureren

⚠️ **KRITIEK:** Dependencies moeten correct gelegd worden, anders werkt het project niet.

**We hebben in totaal 5 dependencies nodig:**

1. **Startup → Presentation**
2. **Startup → Domain**
3. **Startup → Persistence**
4. **Presentation → Domain**
5. **Persistence → Domain**

**Hoe een dependency toevoegen:**
```
Rechtsklik op Dependencies (in het project)
→ Add Project Reference
→ Vink de gewenste projecten aan
→ OK
```

**Dependencies schema:**
```
       STARTUP
      /   |   \
     ↓    ↓    ↓
PRESENT DOMAIN PERSIST
     ↓           ↓
      \         /
       ↓       ↓
         DOMAIN
```

**Dependencies verwijderen (indien nodig):**
- Rechtsklik op Dependencies → Add Project Reference → Uitvinken
- Of: Dependencies uitklappen → Projects uitklappen → Rechtsklik op dependency → Remove

⚠️ **WAARSCHUWING:** Tel je dependencies! Er moeten er exact 5 zijn. Te veel of te weinig betekent dat de structuur fout is.

### 5.4 Verificatie

**Checklist voordat je verder gaat:**
- ✅ 1 Solution
- ✅ 4 Projecten (Startup, Presentation, Domain, Persistence)
- ✅ Startup is ingesteld als startup project (vet in Solution Explorer)
- ✅ 5 Dependencies correct gelegd
- ✅ Project kan opstarten (ook al doet het nog niets)

## 6. Namespaces en toegankelijkheid

### 6.1 Namespaces

Een **namespace** geeft klassen een "achternaam" waardoor ze uniek identificeerbaar zijn, zelfs als er meerdere klassen met dezelfde naam bestaan.

**Analogie:** Net zoals mensen een voornaam en achternaam hebben, hebben klassen een klassenaam en een namespace.

**Voorbeeld:**
```csharp
// In Wine.Domain
namespace Wine.Domain
{
    public class DomainManager { }
}

// In Wine.Domain.Model
namespace Wine.Domain.Model
{
    public class DomainManager { }  // Andere klasse met zelfde naam!
}
```

De compiler kan deze twee onderscheiden omdat ze verschillende "achternamen" (namespaces) hebben:
- `Wine.Domain.DomainManager`
- `Wine.Domain.Model.DomainManager`

### 6.2 Namespace structuur

**REGEL:** Namespaces moeten altijd de folder structuur weerspiegelen.

**Opbouw:**
```
[SolutionNaam].[ProjectNaam].[FolderStructuur]
```

**Voorbeeld:**
```
Wine.Domain.Model.Bottle
│    │      │     └─ Klassenaam
│    │      └─────── Folder
│    └────────────── Project
└─────────────────── Solution
```

**In de code:**
```csharp
namespace Wine.Domain.Model
{
    public class Bottle
    {
        // ...
    }
}
```

### 6.3 Visual Studio hulp

Visual Studio helpt met namespace management:

**Automatisch:**
- Bij het aanmaken van een klasse wordt de namespace automatisch ingevuld op basis van de locatie
- Bij het verplaatsen van een klasse wordt de namespace meestal automatisch aangepast

**Handmatig controleren:**
Als de namespace niet overeenkomt met de folder structuur, toont Visual Studio **3 witte puntjes** (suggestie).

**Snelkoppeling om alle namespaces te synchroniseren:**
```
Rechtsklik op Solution → Sync Namespaces
```

⚠️ **TIP:** Dit is een nieuwe feature - gebruik deze om alle namespaces in één keer correct te zetten!

### 6.4 Using statements

Met `using` statements kunnen we klassen uit andere namespaces gebruiken zonder telkens de volledige namespace te typen.

**Zonder using:**
```csharp
Wine.Domain.Repository.IWineRepository repository = new Wine.Persistence.WineMapper();
```

**Met using:**
```csharp
using Wine.Domain.Repository;
using Wine.Persistence;

IWineRepository repository = new WineMapper();
```

**Using toevoegen via Visual Studio:**
1. Klik op de rode squiggle onder de onbekende klasse
2. Klik op het gloeilampje (💡)
3. Kies: `using [namespace];`

### 6.5 Access modifiers: public vs internal

**Internal** (default voor klassen):
- Alleen toegankelijk binnen hetzelfde project
- Gebruik dit waar mogelijk voor betere encapsulatie

**Public:**
- Toegankelijk vanuit andere projecten
- Nodig wanneer klassen gebruikt worden in andere lagen

⚠️ **VEELGEMAAKTE FOUT:** Alles `public` maken "voor de zekerheid"

**GOEDE PRAKTIJK:** Maak klassen zo restrictief mogelijk. Gebruik `internal` tenzij je de klasse in een ander project nodig hebt.

**Checklist bij compiler errors:**

Als een klasse niet herkend wordt, controleer:
1. ✅ **Type fout?** Geen typfouten in de naam
2. ✅ **Dependency aanwezig?** Juiste project reference toegevoegd
3. ✅ **Access modifier?** Is de klasse `public` (niet `internal`)
4. ✅ **Using statement?** Juiste namespace geïmporteerd

## 7. Implementatie van de lagen

### 7.1 Minimale klassen per laag

Elke laag heeft minimaal de volgende klassen nodig:

**Presentation:**
- `Application` klasse (bijv. `WineApplication`)

**Domain:**
- `DomainManager` klasse (bijv. `DomainManager`)
- Repository interfaces (bijv. `IWineRepository`)
- Model klassen (bijv. `Bottle`)

**Persistence:**
- Mapper klassen (bijv. `WineMapper`)

**Startup:**
- `Program` klasse (gegenereerd)

### 7.2 Model klassen

Model klassen representeren de data in ons domein.

**Voorbeeld: Bottle klasse**
```csharp
namespace Wine.Domain.Model
{
    public class Bottle
    {
        public string Name { get; set; }
        public int Year { get; set; }

        public Bottle(string name, int year)
        {
            Name = name;
            Year = year;
        }
    }
}
```

⚠️ **LET OP:** Model klassen moeten `public` zijn omdat ze gebruikt worden in meerdere lagen.

### 7.3 Repository interfaces

**Het probleem:**

De domeinlaag moet de persistentielaag kunnen aanspreken, maar heeft geen dependency naar de persistentielaag. Dit is een bewuste keuze - dependencies kunnen maar in één richting.

**De oplossing: Interfaces**

We maken een **interface** in de domeinlaag die geïmplementeerd wordt door de mapper in de persistentielaag.

**Stap 1: Interface in Domain**
```csharp
namespace Wine.Domain.Repository
{
    public interface IWineRepository
    {
        List<Bottle> GetAllSoldBottles();
    }
}
```

**Stap 2: Implementatie in Persistence**
```csharp
using Wine.Domain.Repository;
using Wine.Domain.Model;

namespace Wine.Persistence
{
    public class WineMapper : IWineRepository
    {
        private List<Bottle> _bottles;

        public WineMapper()
        {
            _bottles = new List<Bottle>
            {
                new Bottle("Chateau Margaux", 2015),
                new Bottle("Penfolds Grange", 2018)
            };
        }

        public List<Bottle> GetAllSoldBottles()
        {
            return _bottles;
        }
    }
}
```

**Waarom dit werkt:**

Door **polymorfisme** is een object van type `WineMapper` ook een object van type `IWineRepository`. De domeinlaag kan naar de interface verwijzen (die in dezelfde laag zit), terwijl de implementatie in de persistentielaag zit.

⚠️ **BELANGRIJK:** Je moet de methode op **twee plaatsen** definiëren:
1. In de interface (`IWineRepository`)
2. In de implementatie (`WineMapper`)

### 7.4 Dependency Injection

**Dependency Injection** betekent dat een klasse zijn afhankelijkheden via de constructor ontvangt in plaats van ze zelf aan te maken.

**Zonder Dependency Injection (slecht):**
```csharp
public class DomainManager
{
    private WineMapper _mapper;

    public DomainManager()
    {
        _mapper = new WineMapper();  // Tight coupling!
    }
}
```

**Met Dependency Injection (goed):**
```csharp
public class DomainManager
{
    private readonly IWineRepository _repository;

    public DomainManager(IWineRepository repository)
    {
        _repository = repository;
    }
}
```

**Voordelen:**
- **Loose coupling:** DomainManager weet niet welke concrete implementatie gebruikt wordt
- **Testbaarheid:** Makkelijk om te testen met mock-objecten
- **Flexibiliteit:** Implementatie kan makkelijk vervangen worden

### 7.5 Application klasse (Presentation)

De application klasse is het kloppend hart van de presentatielaag.

**Basis implementatie:**
```csharp
namespace Wine.Presentation
{
    public class WineApplication
    {
        private readonly DomainManager _domainManager;

        public WineApplication(DomainManager domainManager)
        {
            _domainManager = domainManager;
        }

        public void Start()
        {
            Console.WriteLine("Do you want to print the sales for the last year? (yes/no)");
            string input = Console.ReadLine();

            if (input == "yes")
            {
                int salesCount = _domainManager.GetTotalSaleCount();
                Console.WriteLine($"Total sales: {salesCount}");
            }
            else
            {
                Console.WriteLine("Program stopped.");
            }
        }
    }
}
```

**Later met WPF:**

Wanneer we WPF gebruiken, wordt elk window een aparte klasse. De application klasse wordt dan verantwoordelijk voor:
- Windows aanmaken en tonen
- Windows verbergen
- Data doorgeven tussen windows

### 7.6 DomainManager klasse

De domeinmanager is het centrale punt waar alles samenkomt.

```csharp
using Wine.Domain.Repository;
using Wine.Domain.Model;

namespace Wine.Domain
{
    public class DomainManager
    {
        private readonly IWineRepository _repository;

        public DomainManager(IWineRepository repository)
        {
            _repository = repository;
        }

        public int GetTotalSaleCount()
        {
            List<Bottle> soldBottles = _repository.GetAllSoldBottles();
            return soldBottles.Count;
        }
    }
}
```

**Taak van de DomainManager:**
- Data ophalen van de persistentielaag
- Data transformeren/verwerken
- Validatie uitvoeren
- Resultaat teruggeven aan presentatielaag

### 7.7 Startup: alles aan elkaar knopen

De startup klasse maakt alle lagen aan en koppelt ze via dependency injection.

```csharp
using Wine.Presentation;
using Wine.Domain;
using Wine.Persistence;
using Wine.Domain.Repository;

namespace Wine.Startup
{
    class Program
    {
        static void Main(string[] args)
        {
            // Stap 1: Persistence laag aanmaken
            IWineRepository wineMapper = new WineMapper();

            // Stap 2: Domain laag aanmaken (met dependency injection)
            DomainManager domainManager = new DomainManager(wineMapper);

            // Stap 3: Presentation laag aanmaken (met dependency injection)
            WineApplication application = new WineApplication(domainManager);

            // Start de applicatie
            application.Start();
        }
    }
}
```

**Waarom deze volgorde?**
- We beginnen onderaan (Persistence)
- Gaan naar boven (Domain, met referentie naar Persistence)
- Eindigen bovenaan (Presentation, met referentie naar Domain)

⚠️ **LET OP:** Na deze setup doet de startup klasse niets meer. De lagen nemen het over.

## 8. Praktisch voorbeeld: data ophalen en tonen

Laten we het volledige proces doorlopen van gebruikersinput tot data-weergave.

### 8.1 De flow

```
┌─────────────────────────────────────────────────┐
│ 1. USER                                         │
│    "Do you want to print sales?"                │
│    Input: "yes"                                 │
└────────────────┬────────────────────────────────┘
                 ↓
┌─────────────────────────────────────────────────┐
│ 2. PRESENTATION (WineApplication.Start())       │
│    if (input == "yes")                          │
│    → domainManager.GetTotalSaleCount()          │
└────────────────┬────────────────────────────────┘
                 ↓
┌─────────────────────────────────────────────────┐
│ 3. DOMAIN (DomainManager.GetTotalSaleCount())   │
│    → repository.GetAllSoldBottles()             │
└────────────────┬────────────────────────────────┘
                 ↓
┌─────────────────────────────────────────────────┐
│ 4. PERSISTENCE (WineMapper.GetAllSoldBottles()) │
│    return _bottles;  // List<Bottle>            │
└────────────────┬────────────────────────────────┘
                 ↓
┌─────────────────────────────────────────────────┐
│ 5. DOMAIN (DomainManager)                       │
│    soldBottles.Count  // Transform data         │
│    return count;                                │
└────────────────┬────────────────────────────────┘
                 ↓
┌─────────────────────────────────────────────────┐
│ 6. PRESENTATION (WineApplication)               │
│    Console.WriteLine($"Total: {salesCount}")    │
└────────────────┬────────────────────────────────┘
                 ↓
┌─────────────────────────────────────────────────┐
│ 7. USER                                         │
│    Sees output: "Total sales: 2"                │
└─────────────────────────────────────────────────┘
```

### 8.2 Stap voor stap code

**Presentation → Domain:**
```csharp
// In WineApplication.Start()
int salesCount = _domainManager.GetTotalSaleCount();
```

Dit roept de methode aan in de DomainManager.

**Domain → Persistence:**
```csharp
// In DomainManager.GetTotalSaleCount()
List<Bottle> soldBottles = _repository.GetAllSoldBottles();
```

De DomainManager vraagt alle flessen op bij de repository.

**Persistence → Domain (data terug):**
```csharp
// In WineMapper.GetAllSoldBottles()
return _bottles;  // Return List<Bottle>
```

De mapper geeft de lijst met flessen terug.

**Domain → Presentation (verwerkte data terug):**
```csharp
// In DomainManager.GetTotalSaleCount()
return soldBottles.Count;  // Transform list to count
```

De DomainManager verwerkt de data (telt aantal flessen) en geeft integer terug.

**Presentation → User:**
```csharp
// In WineApplication.Start()
Console.WriteLine($"Total sales: {salesCount}");
```

Het resultaat wordt getoond aan de gebruiker.

### 8.3 Navigeren tussen klassen in Visual Studio

**Methode 1: Solution Explorer**
```
Rechtsklik op projectnaam → Expand → Klik op klasse
```

**Methode 2: Code Search (snelste)**
```
Ctrl + , (comma)
Type: "DomainManager"
Enter
```

**Methode 3: Ctrl + Click**
```
Ctrl ingedrukt houden + Click op klassenaam
→ Spring naar definitie
```

**Methode 4: Generate Method**
```
Type: _domainManager.GetTotalSaleCount();
→ Rode squiggle verschijnt (methode bestaat niet)
→ Ctrl + . (punt)
→ "Generate method 'GetTotalSaleCount'"
→ Enter
```

Dit maakt automatisch een stub aan in de DomainManager:
```csharp
public int GetTotalSaleCount()
{
    throw new NotImplementedException();
}
```

⚠️ **TIP:** Vergeet niet de `NotImplementedException` te verwijderen en echte logica toe te voegen!

## 9. Belangrijke regels en veelgemaakte fouten

### 9.1 De 4 gouden regels

#### Regel 1: Nooit directe communicatie tussen Presentation en Persistence

🚨 **MEESTE PUNTENAFTREK HIER**

```
❌ FOUT:
Presentation ←→ Persistence

✅ CORRECT:
Presentation → Domain → Persistence
```

**Waarom dit zo belangrijk is:**

Als de presentatie- en persistentielaag rechtstreeks communiceren, kunnen we de lagen niet los van elkaar vervangen. Het hele doel van het 3-lagen model - flexibiliteit en onderhoudbaarheid - gaat dan verloren.

#### Regel 2: Gebruik zo min mogelijk domein klassen in Presentation

De presentatielaag moet **zo eenvoudig mogelijk** blijven.

```csharp
❌ FOUT:
// In WineApplication
List<Bottle> bottles = _domainManager.GetAllBottles();
foreach (var bottle in bottles)
{
    // Complex processing in presentation layer
    if (bottle.Year > 2010 && bottle.Name.Contains("Chateau"))
    {
        // ...
    }
}

✅ CORRECT:
// In WineApplication
int count = _domainManager.GetRecentChateauBottleCount();
Console.WriteLine($"Count: {count}");
```

**Complexe verwerking hoort in de domeinlaag, niet in de presentatielaag.**

#### Regel 3: Namespaces komen overeen met project- en folderstructuur

```
✅ CORRECT:
Wine.Domain.Model.Bottle
│    │      │     └─ Class in Model folder
│    │      └─────── Folder
│    └────────────── Project
└─────────────────── Solution

❌ FOUT:
Wine.Presentation.Model.Bottle  // Bottle zit in Domain.Model!
```

**Controle:**
```
Rechtsklik op solution → Sync Namespaces
```

#### Regel 4: Domain werkt met repository interfaces, NOOIT rechtstreeks met mappers

```csharp
❌ FOUT:
// In DomainManager
private readonly WineMapper _mapper;  // Rechtstreekse referentie!

✅ CORRECT:
// In DomainManager
private readonly IWineRepository _repository;  // Via interface
```

### 9.2 Validatie: waar hoort het thuis?

Een veelgestelde vraag: "Moet validatie in de Presentation of in de Domain?"

**Beslissingskader:**

Stel jezelf de vraag: **"Als ik de presentatielaag vervang (Console → WPF), wil ik deze code behouden?"**

**Voorbeeld 1: Nummer validatie in Console**
```csharp
// In Console Application
string input = Console.ReadLine();
if (!int.TryParse(input, out int number))
{
    Console.WriteLine("Invalid number!");
}
```

**Vraag:** Heb ik dit nodig in een WPF applicatie?
**Antwoord:** Nee, in WPF kan ik een NumericUpDown control gebruiken die alleen nummers accepteert.

**Conclusie:** Deze validatie hoort in de **presentatielaag** (console-specifiek).

**Voorbeeld 2: Business rule validatie**
```csharp
// Regel: Year moet tussen 1900 en huidige jaar liggen
if (bottle.Year < 1900 || bottle.Year > DateTime.Now.Year)
{
    throw new ArgumentException("Invalid year");
}
```

**Vraag:** Is deze regel platform-afhankelijk?
**Antwoord:** Nee, deze business rule geldt altijd, ongeacht de UI.

**Conclusie:** Deze validatie hoort in de **domeinlaag** (platform-onafhankelijk).

**Vuistregel:**
- **Platform-specifieke validatie** → Presentatielaag
- **Business rules** → Domeinlaag

### 9.3 Veelgemaakte fouten bij dependencies

**Fout 1: Verkeerd aantal dependencies**
```
❌ Meer of minder dan 5 dependencies
✅ Exact 5 dependencies
```

**Fout 2: Verkeerde richting**
```
❌ Domain → Presentation
❌ Persistence → Presentation
✅ Presentation → Domain
✅ Persistence → Domain
```

**Fout 3: Circulaire dependencies**
```
❌ Domain → Persistence EN Persistence → Domain
```

Dit kan niet! Dependencies werken maar in één richting. Vandaar de oplossing met interfaces.

### 9.4 Veelgemaakte fouten bij access modifiers

**Fout 1: Alles public maken**
```csharp
❌ LAZY:
public class Helper { }  // Gebruikt alleen in dit project

✅ CORRECT:
internal class Helper { }  // Zo restrictief mogelijk
```

**Fout 2: Vergeten public te maken waar nodig**
```csharp
❌ FOUT:
// In Domain
internal class DomainManager { }  // Presentation kan er niet bij!

✅ CORRECT:
public class DomainManager { }  // Nodig in andere projecten
```

**Vuistregel:**
- Klassen die alleen in hun eigen project gebruikt worden: `internal`
- Klassen die in andere projecten gebruikt worden: `public`

### 9.5 Veelgemaakte fouten bij namespaces

**Fout 1: Namespace niet aangepast na verplaatsen**
```csharp
❌ FOUT:
// File bevindt zich in Domain/Model/
namespace Wine.Domain  // Verkeerde namespace!
{
    public class Bottle { }
}

✅ CORRECT:
namespace Wine.Domain.Model  // Matched folder structuur
{
    public class Bottle { }
}
```

**Fout 2: Inconsistente naming**
```csharp
❌ FOUT:
Wine.Presentation
Wine.BusinessLogic  // Niet "Domain"!
Wine.Data          // Niet "Persistence"!

✅ CORRECT:
Wine.Presentation
Wine.Domain
Wine.Persistence
```

### 9.6 Read-only fields

Het gebruik van `readonly` is niet verplicht, maar wel een **best practice**.

```csharp
✅ BEST PRACTICE:
private readonly IWineRepository _repository;

public DomainManager(IWineRepository repository)
{
    _repository = repository;
}
// _repository kan nu niet meer veranderen na constructie
```

**Waarom readonly?**
- Maakt de intentie duidelijk: deze waarde mag niet veranderen
- Voorkomt bugs door onbedoelde hertoewijzing
- Maakt code veiliger en voorspelbaarder

### 9.7 Constructor vs Start() methode

**Vraag:** Moet logica in de constructor of in een aparte `Start()` methode?

```csharp
// Optie 1: Alles in constructor
public WineApplication(DomainManager domainManager)
{
    _domainManager = domainManager;
    
    Console.WriteLine("Welcome!");
    // ... alle startup logica
}

// Optie 2: Aparte Start() methode
public WineApplication(DomainManager domainManager)
{
    _domainManager = domainManager;
}

public void Start()
{
    Console.WriteLine("Welcome!");
    // ... alle startup logica
}
```

**Aanbeveling:** Gebruik een aparte `Start()` methode.

**Voordelen:**
- Betere scheiding van verantwoordelijkheden (constructor = initialisatie, Start = logica)
- Leesbaarder (constructor blijft klein)
- Testbaarder

**Vuistregel:** Probeer functies niet groter te maken dan wat op je scherm past. Als je moet scrollen, is de functie te groot en moet deze opgedeeld worden.

## 10. Evolutie doorheen het semester

### 10.1 Waar we nu staan

**Huidige setup:**
- **Presentation:** Console applicatie
- **Domain:** Eenvoudige logica
- **Persistence:** In-memory lists

**Kenmerken:**
- Data verdwijnt bij afsluiten
- Geen grafische interface
- Basis functionaliteit

### 10.2 Waar we naartoe gaan

**Later in het semester:**

**Presentation:**
```
Console App → WPF Application
- Grafische user interface
- Meerdere windows
- Event-driven programming
```

**Persistence:**
```
In-memory Lists → ADO.NET Database
- Permanente data opslag
- SQL queries
- Echte database (bijv. SQL Server)
```

**Domain:**
```
Blijft grotendeels hetzelfde!
(Dat is precies het punt van het 3-lagen model)
```

### 10.3 Waarom het nu moeilijk lijkt

In het begin voelt het 3-lagen model als overkill:

**Zonder 3-lagen model:**
```csharp
// 5 minuten werk
class Program
{
    static void Main()
    {
        List<Bottle> bottles = new List<Bottle> { /* ... */ };
        Console.WriteLine($"Total: {bottles.Count}");
    }
}
```

**Met 3-lagen model:**
```
20 minuten werk:
- 4 projecten aanmaken
- 5 dependencies configureren
- Interfaces definiëren
- Dependency injection opzetten
- ...
```

**MAAR:** Naarmate projecten groter worden, win je deze tijdsinvestering terug (en meer) door:
- Betere structuur
- Makkelijker onderhoud
- Makkelijker uitbreiden
- Makkelijker testen
- Herbruikbaarheid van lagen

**Analogie:** Het is groeipijn. Je leert nu de juiste manier, zodat je op termijn tijd wint.

### 10.4 Toekomstige toepassingen

**Tussentijdse opdracht:**
- Waarschijnlijk met 3-lagen model (afhankelijk van jaar/docent)

**Eindopdracht:**
- Zeker met 3-lagen model
- WPF + Database + Domeinlogica

**Gevorderd Programmeren 2:**
- Ander design pattern (voor API's)
- Maar zelfde principes van scheiding en verantwoordelijkheden

**Professioneel werk:**
- Grotere projecten vereisen altijd goede structuur
- 3-lagen model (of varianten) zijn industriestandaard

## 11. Tips en shortcuts

### 11.1 Visual Studio shortcuts

**Navigatie:**
```
Ctrl + ,          → Code Search (zoek naar klassen/files)
Ctrl + Click      → Ga naar definitie
F12              → Ga naar definitie (alternatief)
Ctrl + -         → Ga terug naar vorige locatie
```

**Code generatie:**
```
Ctrl + .         → Quick Actions menu
                   - Generate method
                   - Add using statement
                   - Implement interface
                   - Etc.
```

**Refactoring:**
```
F2               → Rename (hernoemt overal)
Ctrl + R, R      → Rename (alternatief)
```

### 11.2 Productiviteitstips

**Tip 1: Gebruik Code Search**

In plaats van door Solution Explorer navigeren:
```
Ctrl + , → Type "DomainManager" → Enter
```

**Tip 2: Laat Visual Studio werk doen**

Genereer methodes in plaats van handmatig typen:
```
Type: _repository.GetAllBottles();
→ Ctrl + . → Generate method
```

**Tip 3: Sync Namespaces regelmatig**
```
Rechtsklik op Solution → Sync Namespaces
```

**Tip 4: Gebruik de juiste naming conventions**
```
✅ SolutionName.ProjectName.FolderStructure
✅ PascalCase voor klassen
✅ _camelCase voor private fields
✅ camelCase voor parameters
```

## 12. Checklist voor een correct 3-lagen model

Gebruik deze checklist om te verifiëren dat je 3-lagen model correct is opgezet:

### Projectstructuur
- [ ] 1 Solution met logische naam
- [ ] 4 Projecten: Startup, Presentation, Domain, Persistence
- [ ] Naming: `[Solution].[Project]` pattern
- [ ] Startup project is ingesteld als startup project (vet in Solution Explorer)

### Dependencies
- [ ] Exact 5 dependencies (niet meer, niet minder)
- [ ] Startup → Presentation
- [ ] Startup → Domain  
- [ ] Startup → Persistence
- [ ] Presentation → Domain
- [ ] Persistence → Domain
- [ ] Geen omgekeerde dependencies (bijv. Domain → Presentation)

### Namespaces
- [ ] Alle namespaces volgen pattern: `[Solution].[Project].[FolderStructure]`
- [ ] Geen warnings over mismatched namespaces
- [ ] `Sync Namespaces` uitgevoerd

### Access Modifiers
- [ ] Klassen die in andere projecten gebruikt worden: `public`
- [ ] Klassen die alleen lokaal gebruikt worden: `internal`
- [ ] Niet alles standaard `public`

### Communicatie tussen lagen
- [ ] Presentation roept alleen Domain aan (niet Persistence)
- [ ] Domain gebruikt repository interfaces (niet rechtstreeks mappers)
- [ ] Alle communicatie Presentation ↔ Persistence via Domain

### Code kwaliteit
- [ ] Dependency Injection correct geïmplementeerd
- [ ] Readonly fields waar mogelijk
- [ ] Functies niet te lang (maximaal één scherm)
- [ ] Duidelijke verantwoordelijkheden per laag
- [ ] Minimale logica in Presentation
- [ ] Business logica in Domain
- [ ] Data access in Persistence

### Werking
- [ ] Project compileert zonder errors
- [ ] Project start op zonder crashes
- [ ] Data flow werkt: User → Presentation → Domain → Persistence → Domain → Presentation → User

## 13. Veelgestelde vragen (FAQ)

### Waarom geen dependency van Domain naar Persistence?

**Vraag:** Waarom kunnen we geen dependency leggen van Domain naar Persistence? Dat zou toch logischer zijn?

**Antwoord:** Dependencies kunnen maar in één richting. Als Domain → Persistence EN Persistence → Domain zou kunnen, krijgen we een circulaire dependency (kip-ei probleem). Visual Studio kan dan niet bepalen welke laag eerst gecompileerd moet worden. Door interfaces te gebruiken, lossen we dit op: Persistence → Domain (voor de interface), en Domain gebruikt die interface zonder directe dependency op Persistence te hebben.

### Waarom interfaces in Domain en niet in Persistence?

**Vraag:** Zou het niet logischer zijn om de repository interface in de Persistence laag te zetten?

**Antwoord:** Nee, want dan kan Domain er niet bij. Domain heeft geen dependency op Persistence, dus kan geen klassen uit Persistence aanspreken. Door de interface in Domain te plaatsen, kan Domain ernaar verwijzen, terwijl Persistence (die wel een dependency op Domain heeft) de interface kan implementeren.

### Moet ik altijd exact deze structuur gebruiken?

**Vraag:** Zijn deze namen (Presentation, Domain, Persistence) verplicht?

**Antwoord:** Nee, verschillende docenten en bedrijven gebruiken verschillende namen:
- Presentation / UI / View / Client
- Domain / Business / Logic / Service
- Persistence / Data / Repository / DAL (Data Access Layer)

Het belangrijkste is dat de **verantwoordelijkheden** duidelijk zijn en de **communicatieregels** gevolgd worden.

### Waarom een aparte Startup laag?

**Vraag:** Waarom niet gewoon alles in Presentation starten?

**Antwoord:** Als we in Presentation starten, moet Presentation dependencies hebben naar Domain EN Persistence (om alles te kunnen aanmaken). Dit schendt de regel dat Presentation en Persistence elkaar niet rechtstreeks mogen kennen. Door een aparte Startup laag te maken, schenden we deze regel alleen bij het opstarten (eenmalig), niet tijdens de hele runtime van de applicatie.

### Kan ik meerdere DomainManagers hebben?

**Vraag:** Voor een grote applicatie: kan ik meerdere DomainManager klassen hebben?

**Antwoord:** Technisch kan dat, maar het is beter om te werken met één centrale DomainManager die eventueel andere service-klassen aanroept. Bijvoorbeeld:
```
DomainManager
    → UserService
    → ProductService  
    → OrderService
```

De DomainManager blijft het centrale punt, maar delegeert specifieke taken aan gespecialiseerde services.

### Hoe test ik een 3-lagen model?

**Vraag:** Hoe kan ik mijn lagen afzonderlijk testen?

**Antwoord:** Dat is precies een van de voordelen van het 3-lagen model! Je kunt:
- **Domain testen** met mock repositories (zonder echte database)
- **Presentation testen** met mock DomainManager (zonder echte business logica)
- Elke laag onafhankelijk testen

Later in de cursus komen unit testing en mocking aan bod.

## 14. Samenvatting

Het 3-lagen model is een design pattern dat onze code structureert in drie (eigenlijk vier) duidelijke lagen met vaste verantwoordelijkheden:

**De lagen:**
1. **Presentation** - User interface en interactie
2. **Domain** - Business logica (het kloppend hart)
3. **Persistence** - Data opslag en ophaling
4. **Startup** - Initialisatie en koppeling

**De communicatie:**
- Altijd via Domain: Presentation ↔ Domain ↔ Persistence
- Nooit rechtstreeks: Presentation ↔ Persistence

**De technische oplossing:**
- **Dependencies:** Eenrichtingsverkeer (Presentation → Domain ← Persistence)
- **Interfaces:** Domain definieert interfaces die Persistence implementeert
- **Dependency Injection:** Afhankelijkheden via constructors

**De voordelen:**
- Flexibiliteit (lagen kunnen vervangen worden)
- Onderhoudbaarheid (duidelijke structuur)
- Testbaarheid (lagen onafhankelijk testbaar)
- Schaalbaarheid (groeit mee met project)

**De vier gouden regels:**
1. Nooit directe communicatie Presentation ↔ Persistence
2. Minimaal domein klassen in Presentation
3. Namespaces volgen project-/folderstructuur
4. Domain werkt met interfaces, niet met concrete mappers

Het 3-lagen model voelt in het begin als extra werk voor kleine projecten, maar is een investering die zich uitbetaalt naarmate projecten groter en complexer worden. Het is de professionele manier om software te structureren en vormt de basis voor alle grote(re) applicaties die je in je carrière gaat ontwikkelen.

---

## Bijlage: Complete codevoorbeeld

Voor referentie, hier is een volledig werkend voorbeeld van een minimaal 3-lagen model:

### Wine.Domain.Model.Bottle
```csharp
namespace Wine.Domain.Model
{
    public class Bottle
    {
        public string Name { get; set; }
        public int Year { get; set; }

        public Bottle(string name, int year)
        {
            Name = name;
            Year = year;
        }
    }
}
```

### Wine.Domain.Repository.IWineRepository
```csharp
using Wine.Domain.Model;

namespace Wine.Domain.Repository
{
    public interface IWineRepository
    {
        List<Bottle> GetAllSoldBottles();
    }
}
```

### Wine.Domain.DomainManager
```csharp
using Wine.Domain.Repository;
using Wine.Domain.Model;

namespace Wine.Domain
{
    public class DomainManager
    {
        private readonly IWineRepository _repository;

        public DomainManager(IWineRepository repository)
        {
            _repository = repository;
        }

        public int GetTotalSaleCount()
        {
            List<Bottle> soldBottles = _repository.GetAllSoldBottles();
            return soldBottles.Count;
        }
    }
}
```

### Wine.Persistence.WineMapper
```csharp
using Wine.Domain.Repository;
using Wine.Domain.Model;

namespace Wine.Persistence
{
    public class WineMapper : IWineRepository
    {
        private List<Bottle> _bottles;

        public WineMapper()
        {
            _bottles = new List<Bottle>
            {
                new Bottle("Chateau Margaux", 2015),
                new Bottle("Penfolds Grange", 2018)
            };
        }

        public List<Bottle> GetAllSoldBottles()
        {
            return _bottles;
        }
    }
}
```

### Wine.Presentation.WineApplication
```csharp
using Wine.Domain;

namespace Wine.Presentation
{
    public class WineApplication
    {
        private readonly DomainManager _domainManager;

        public WineApplication(DomainManager domainManager)
        {
            _domainManager = domainManager;
        }

        public void Start()
        {
            Console.WriteLine("Do you want to print the sales for the last year? (yes/no)");
            string input = Console.ReadLine();

            if (input?.ToLower() == "yes")
            {
                int salesCount = _domainManager.GetTotalSaleCount();
                Console.WriteLine($"Total sales: {salesCount}");
            }
            else
            {
                Console.WriteLine("Program stopped.");
            }
        }
    }
}
```

### Wine.Startup.Program
```csharp
using Wine.Presentation;
using Wine.Domain;
using Wine.Persistence;
using Wine.Domain.Repository;

namespace Wine.Startup
{
    class Program
    {
        static void Main(string[] args)
        {
            // Stap 1: Persistence laag
            IWineRepository wineMapper = new WineMapper();

            // Stap 2: Domain laag (met dependency injection)
            DomainManager domainManager = new DomainManager(wineMapper);

            // Stap 3: Presentation laag (met dependency injection)
            WineApplication application = new WineApplication(domainManager);

            // Start applicatie
            application.Start();
        }
    }
}
```

Dit voorbeeld demonstreert alle kernconcepten van het 3-lagen model en kan gebruikt worden als referentie bij het opzetten van je eigen projecten.
