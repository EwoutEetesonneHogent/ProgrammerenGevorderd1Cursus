# ADO.NET: Database toegang in C#

## 1. Introductie tot ADO.NET

### 1.1 Wat is ADO.NET?

**ADO.NET** staat voor **ActiveX Data Objects for .NET**. Je hoeft deze afkorting niet uit je hoofd te leren - zelfs docenten weten vaak niet precies waarvoor alle letters staan. Wat wel belangrijk is, is begrijpen wat ADO.NET doet.

ADO.NET is een **standaard interface voor data toegang** in .NET applicaties. Het is een **platform-agnostisch systeem** om data te benaderen en te manipuleren uit verschillende bronnen.

**Belangrijke kenmerken:**
- Werkt met verschillende database types (niet alleen Microsoft SQL Server)
- Kan verschillende data sources aanspreken (databases, XML files, Excel files, etc.)
- Bruikbaar in verschillende soorten applicaties (web, Windows, console)
- Biedt een abstractielaag tussen de applicatie en de data source

### 1.2 De architectuur van ADO.NET

ADO.NET bestaat uit **twee hoofdonderdelen**:

```
┌─────────────────────────────────────┐
│   APPLICATIE                        │
│   (Console/WPF/Web)                 │
└──────────────┬──────────────────────┘
               ↓
┌─────────────────────────────────────┐
│   ADO.NET                           │
│   (Platform-agnostische laag)       │
└──────────────┬──────────────────────┘
               ↓
┌─────────────────────────────────────┐
│   DATA PROVIDER                     │
│   (Microsoft.Data.SqlClient)        │
└──────────────┬──────────────────────┘
               ↓
┌─────────────────────────────────────┐
│   DATA SOURCE                       │
│   (SQL Server Database)             │
└─────────────────────────────────────┘
```

**1. ADO.NET Core**
- De generieke, platform-onafhankelijke functionaliteit
- Werkt met elk type data source

**2. Data Provider**
- Technologie-specifieke implementatie
- In deze cursus: **Microsoft.Data.SqlClient** voor SQL Server
- Andere providers: MySQL, PostgreSQL, Oracle, etc.

## 2. SQL Server installatie en configuratie

### 2.1 Wat installeren we?

Bij het werken met databases installeren we **twee aparte componenten**:

**1. SQL Server Express (Database Engine)**
- De database server zelf
- Draait als een service op je computer
- Heeft geen user interface
- Beheert de databases

**2. SQL Server Management Studio (SSMS)**
- Grafische tool om databases te beheren
- Kan verbinden met de Database Engine
- Gebruikt om databases, tabellen, queries te maken

⚠️ **BELANGRIJK:** Dit zijn twee aparte dingen! De Database Engine moet draaien voordat je ermee kunt verbinden.

### 2.2 Controleren of SQL Server draait

**Via Task Manager:**

1. Open Task Manager (Ctrl + Shift + Esc)
2. Ga naar de tab "Services"
3. Zoek naar: **MSSQL$SQLEXPRESS**
4. Status moet **"Running"** zijn

Als de status niet "Running" is:
- Rechtsklik op de service
- Kies "Start"

**Veelvoorkomend probleem:** Als je programma niet kan verbinden met de database, controleer eerst of deze service draait!

### 2.3 Database aanmaken in SQL Server Management Studio

**Stap 1: Verbinden**
```
1. Open SQL Server Management Studio
2. Server name: (local)\SQLEXPRESS  (of gewoon . voor localhost)
3. Authentication: Windows Authentication
4. Klik Connect
```

**Stap 2: Nieuwe database**
```
1. Rechtsklik op "Databases"
2. Kies "New Database"
3. Geef een naam (bijv. "People")
4. Klik OK
```

**Stap 3: Nieuwe tabel**
```
1. Expand de database
2. Rechtsklik op "Tables"
3. Kies "New" → "Table"
```

**Stap 4: Kolommen definiëren**

Voorbeeld voor een "Person" tabel:

| Column Name | Data Type    | Allow Nulls |
|-------------|--------------|-------------|
| ID          | int          | ☐           |
| Name        | varchar(MAX) | ☐           |
| Email       | varchar(MAX) | ☐           |
| BirthDate   | date         | ☐           |

**Stap 5: Primary Key instellen**
```
1. Rechtsklik op de "ID" kolom
2. Kies "Set Primary Key"
```

**Stap 6: Auto-increment (Identity)**
```
1. Selecteer de "ID" kolom
2. In Column Properties (rechts onderaan)
3. Expand "Identity Specification"
4. Zet "(Is Identity)" op "Yes"
5. Identity Increment: 1
6. Identity Seed: 1
```

⚠️ **LET OP:** Soms moet je een instelling wijzigen om tabellen aan te kunnen passen:
```
Tools → Options → Designers
→ Uncheck "Prevent saving changes that require table re-creation"
```

Dit staat standaard aan om te voorkomen dat je per ongeluk data verliest, maar kan tijdens ontwikkeling lastig zijn.

### 2.4 Data invoeren in SSMS

```
1. Rechtsklik op de tabel
2. Kies "Edit Top 200 Rows"
3. Vul data in (ID wordt automatisch ingevuld als Identity is ingeschakeld)
4. Data wordt direct opgeslagen bij het naar de volgende rij gaan
```

## 3. Visual Studio integratie

### 3.1 Database verbinding toevoegen in Visual Studio

Je kunt databases ook beheren vanuit Visual Studio zelf via de **Server Explorer**.

**Stappen:**
```
1. View → Server Explorer
2. Rechtsklik op "Data Connections"
3. Kies "Add Connection"
4. Data source: Microsoft SQL Server
5. Server name: .\SQLEXPRESS (of localhost\SQLEXPRESS)
6. Authentication: Windows Authentication
7. Vink "Trust server certificate" aan (voor lokale development)
8. Select database: Kies je database uit de lijst
9. Test Connection
10. OK
```

**Voordelen van Server Explorer:**
- Database beheren zonder SSMS te openen
- Direct in Visual Studio werken
- Connection String automatisch genereren
- Tables, views, stored procedures direct aanpassen

### 3.2 Database aanmaken in Visual Studio

Je kunt zelfs nieuwe databases aanmaken in Visual Studio:

```
1. Server Explorer → Data Connections
2. Rechtsklik → "Create New SQL Server Database"
3. Server name: .\SQLEXPRESS
4. Database name: (bijv. "BankExample")
5. OK
```

### 3.3 Tabellen aanmaken in Visual Studio

```
1. Expand je database in Server Explorer
2. Rechtsklik op "Tables"
3. "Add New Table"
4. Definieer kolommen in de designer
5. In de properties kun je Identity Specification instellen
6. Update (Ctrl+Shift+U) om de tabel aan te maken
```

**Voordeel:** Visual Studio toont het SQL script dat gegenereerd wordt, zodat je kunt leren hoe CREATE TABLE statements werken.

### 3.4 Connection String ophalen

Dit is de makkelijkste manier om een correcte Connection String te krijgen:

```
1. Server Explorer → Data Connections
2. Rechtsklik op je verbinding
3. Kies "Properties"
4. Kopieer de "Connection String" waarde
```

## 4. NuGet Package Manager

### 4.1 Wat is NuGet?

**NuGet** is de package manager van Visual Studio. Het is vergelijkbaar met:
- **npm** voor JavaScript
- **pip** voor Python
- **Maven** voor Java

**Wat doet een package manager?**
- Installeert vooraf geschreven code (packages/libraries)
- Beheert dependencies (andere packages die nodig zijn)
- Houdt packages up-to-date
- Maakt code herbruikbaar zonder het wiel opnieuw uit te vinden

### 4.2 Package installeren via GUI

**Methode 1: Via rechtsklik**
```
1. Rechtsklik op je project in Solution Explorer
2. Kies "Manage NuGet Packages"
3. Ga naar de "Browse" tab
4. Zoek naar: Microsoft.Data.SqlClient
5. Selecteer de package
6. Klik "Install"
7. Accept de license agreements
```

**Belangrijke tabs:**
- **Installed:** Packages die al geïnstalleerd zijn in dit project
- **Browse:** Alle beschikbare packages zoeken en installeren
- **Updates:** Beschikbare updates voor geïnstalleerde packages

### 4.3 Package installeren inline (sneller!)

Visual Studio kan packages automatisch installeren wanneer je ze probeert te gebruiken:

```csharp
// Type dit:
private SqlConnection _connection;

// Visual Studio herkent SqlConnection niet
// Klik op het lampje (💡) of druk Ctrl + .
// Kies: "Install package 'Microsoft.Data.SqlClient'"
// Package wordt automatisch geïnstalleerd!
```

Dit bespaart tijd en voorkomt dat je moet onthouden welke package je nodig hebt.

### 4.4 De juiste package kiezen

⚠️ **KRITIEK - VERKEERDE PACKAGE:**

Er zijn **twee** SQL Client packages:

❌ **System.Data.SqlClient** 
- Legacy/verouderd
- Deprecated
- Niet meer gebruiken!

✅ **Microsoft.Data.SqlClient**
- Moderne versie
- Actief onderhouden
- Deze gebruiken!

**Hoe herken je een goede package?**
- ✅ Vinkje van Microsoft naast de package
- Hoge download counts (miljoenen downloads)
- Recent ge-update (laatste update niet jaren geleden)
- Goede documentatie

### 4.5 Dependencies

Wanneer je een package installeert, zie je vaak dat er **meerdere** packages geïnstalleerd worden:

```
Installing Microsoft.Data.SqlClient...
  → Also installing: Microsoft.Data.SqlClient.SNI.runtime
  → Also installing: Microsoft.Identity.Client
  → Also installing: System.Configuration.ConfigurationManager
  ... etc
```

**Waarom?**
- De hoofdpackage heeft **dependencies** (afhankelijkheden)
- Deze worden automatisch meegeïnstalleerd
- Als je de hoofdpackage verwijdert, worden dependencies ook verwijderd

**In Solution Explorer:**
```
Dependencies
  └─ Packages
      ├─ Microsoft.Data.SqlClient (Top-level package)
      └─ [Transitive] Microsoft.Data.SqlClient.SNI.runtime
      └─ [Transitive] Microsoft.Identity.Client
```

## 5. Connection Strings

### 5.1 Wat is een Connection String?

Een **Connection String** is vergelijkbaar met een postadres - het vertelt je applicatie waar de database te vinden is en hoe ermee te verbinden.

**Analogie met een brief:**
- Naam van ontvanger → Database naam
- Straat + nummer → Server naam
- Postcode + plaats → Instance naam
- Land → Authentication methode

**Voorbeeld Connection String:**
```
Data Source=.\SQLEXPRESS;Initial Catalog=People;Integrated Security=True;Trust Server Certificate=True
```

**Onderdelen:**
- **Data Source:** Waar staat de database? (`.` = localhost, `\SQLEXPRESS` = instance naam)
- **Initial Catalog:** Welke database op die server?
- **Integrated Security:** Gebruik Windows authenticatie (geen username/password nodig)
- **Trust Server Certificate:** Voor lokale development (accepteer self-signed certificaten)

### 5.2 Connection String opslag (Best Practice)

❌ **FOUT: Connection String overal herhalen**
```csharp
// In Mapper1.cs
var conn = new SqlConnection("Data Source=.\\SQLEXPRESS;...");

// In Mapper2.cs
var conn = new SqlConnection("Data Source=.\\SQLEXPRESS;...");

// In Mapper3.cs
var conn = new SqlConnection("Data Source=.\\SQLEXPRESS;...");
```

**Problemen:**
- Code duplicatie
- Als Connection String verandert, moet je het overal aanpassen
- Grote kans op typefouten (één letter fout = niet werkend)
- Moeilijk te onderhouden

✅ **CORRECT: Centraal opslaan**

Maak een aparte klasse:

```csharp
namespace YourProject.Data
{
    internal static class DBConfig
    {
        public const string ConnectionString = 
            @"Data Source=.\SQLEXPRESS;Initial Catalog=People;Integrated Security=True;Trust Server Certificate=True";
    }
}
```

**Gebruik:**
```csharp
var connection = new SqlConnection(DBConfig.ConnectionString);
```

**Waarom deze aanpak?**
- **static class:** Geen instanties nodig
- **const:** Waarde kan niet veranderen, is ook automatisch static
- **@-teken (literal string):** Voorkomt escape character problemen
- **Eén plek:** Als Connection String verandert, pas je het op één plek aan

### 5.3 Literal Strings (@-operator)

**Probleem zonder @:**
```csharp
string path = "C:\Users\Name\Documents";  // ❌ FOUT!
// \U = onbekende escape sequence
// \N = onbekende escape sequence
// \D = onbekende escape sequence
```

**Escape characters:**
- `\n` = nieuwe regel
- `\t` = tab
- `\\` = backslash
- `\"` = aanhalingsteken

**Zonder @ moet je escapen:**
```csharp
string path = "C:\\Users\\Name\\Documents";  // Werkt, maar lelijk
string connStr = "Data Source=.\\SQLEXPRESS;...";
```

**Met @ (literal string):**
```csharp
string path = @"C:\Users\Name\Documents";  // ✅ Alles letterlijk
string connStr = @"Data Source=.\SQLEXPRESS;...";
```

**Voordelen:**
- Geen escape characters nodig
- Wat je ziet is wat je krijgt
- Leesbaarder
- Minder foutgevoelig bij copy-paste

⚠️ **AANBEVELING:** Gebruik altijd `@` voor Connection Strings en file paths!

## 6. Basis database operaties

### 6.1 De twee kernobjecten

ADO.NET werkt met twee hoofdobjecten:

**1. SqlConnection**
- Vertegenwoordigt de verbinding met de database
- Moet geopend worden voor gebruik
- Moet gesloten worden na gebruik
- Implementeert IDisposable

**2. SqlCommand**
- Vertegenwoordigt een SQL query/commando
- Bevat de SQL tekst
- Gekoppeld aan een SqlConnection
- Voert queries uit

### 6.2 Connection openen en sluiten

**Basis pattern:**
```csharp
SqlConnection connection = null;

try
{
    connection = new SqlConnection(DBConfig.ConnectionString);
    connection.Open();
    
    // Werk met de database
}
catch (Exception ex)
{
    Console.WriteLine($"Something went wrong: {ex.Message}");
}
finally
{
    if (connection != null)
    {
        connection.Close();
    }
}
```

**Mogelijke exceptions:**
- **ArgumentException:** Connection String is null of leeg
- **InvalidOperationException:** Connection is al open
- **SqlException:** Database fout (server niet gevonden, login failed, etc.)
- **ConfigurationErrorsException:** Configuratie probleem

### 6.3 Using statement (moderne aanpak)

**Waarom using?**

SqlConnection implementeert **IDisposable**, wat betekent dat het een `Dispose()` methode heeft die resources proper opruimt.

**Oude stijl (met accolades):**
```csharp
using (SqlConnection connection = new SqlConnection(DBConfig.ConnectionString))
{
    connection.Open();
    
    // Werk met de database
    
}  // connection.Dispose() wordt automatisch aangeroepen
```

**Moderne stijl (zonder accolades):**
```csharp
using SqlConnection connection = new SqlConnection(DBConfig.ConnectionString);
connection.Open();

// Werk met de database

// connection.Dispose() wordt aangeroepen aan het einde van de scope
```

✅ **AANBEVELING:** Gebruik altijd `using` voor database connecties!

**Voordelen:**
- Automatische cleanup
- Kan niet vergeten te sluiten
- Werkt ook bij exceptions
- Korter en leesbaarder

### 6.4 Close() vs Dispose()

**Wat is het verschil?**

**Close():**
```csharp
connection.Close();
// Sluit de verbinding
// Kan later opnieuw geopend worden
connection.Open();  // Dit werkt!
```

**Analogie:** Deur dichtdoen

**Dispose():**
```csharp
connection.Dispose();
// Ruimt het object volledig op
// Kan NIET opnieuw gebruikt worden
connection.Open();  // FOUT! Object is disposed
```

**Analogie:** Huis afbreken (deur kan niet meer open)

**Waarom Dispose gebruiken?**
- Proper resource management
- Voorkomt memory leaks
- Best practice voor IDisposable objecten
- `using` roept automatisch Dispose() aan

## 7. SQL Commands uitvoeren

### 7.1 SqlCommand aanmaken

Een SqlCommand heeft **twee parameters** nodig:

```csharp
SqlCommand command = new SqlCommand(sqlQuery, connection);
```

**Parameter 1: SQL Query (string)**
```csharp
string query = "SELECT * FROM Person";
```

**Parameter 2: Geopende SqlConnection**
```csharp
SqlConnection connection = new SqlConnection(DBConfig.ConnectionString);
connection.Open();
```

**Alternatieve manier:**
```csharp
using SqlConnection connection = new SqlConnection(DBConfig.ConnectionString);
connection.Open();

using SqlCommand command = connection.CreateCommand();
command.CommandText = "SELECT * FROM Person";
```

### 7.2 De drie Execute methodes

Er zijn **drie manieren** om een SqlCommand uit te voeren, afhankelijk van wat je terug wilt krijgen:

#### 7.2.1 ExecuteReader - Meerdere waarden ophalen

**Wanneer gebruiken?**
- SELECT queries die meerdere rijen teruggeven
- SELECT queries die één rij met meerdere kolommen teruggeven
- Wanneer je door resultaten wilt loopen

**Voorbeeld:**
```csharp
using SqlConnection connection = new SqlConnection(DBConfig.ConnectionString);
connection.Open();

using SqlCommand command = new SqlCommand("SELECT * FROM Person", connection);
using SqlDataReader reader = command.ExecuteReader();

if (reader.HasRows)
{
    while (reader.Read())
    {
        // Toegang tot data via kolom index
        Console.WriteLine($"{reader[0]} {reader[1]} {reader[2]}");
        
        // Of via kolom naam (beter!)
        Console.WriteLine($"{reader["ID"]} {reader["Name"]} {reader["Email"]}");
    }
}
```

**SqlDataReader methodes:**
- **HasRows:** Zijn er resultaten?
- **Read():** Ga naar de volgende rij (returns true als er een rij is)
- **[index] of [columnName]:** Haal waarde op

⚠️ **TIP:** Gebruik kolomnamen in plaats van indices - het is duidelijker en minder foutgevoelig!

#### 7.2.2 ExecuteNonQuery - Geen resultaat verwacht

**Wanneer gebruiken?**
- INSERT statements
- UPDATE statements
- DELETE statements
- Alles wat de database wijzigt maar geen data teruggeeft

**Voorbeeld:**
```csharp
using SqlConnection connection = new SqlConnection(DBConfig.ConnectionString);
connection.Open();

using SqlCommand command = new SqlCommand(
    "DELETE FROM Person WHERE ID = 5", 
    connection
);

int rowsAffected = command.ExecuteNonQuery();
Console.WriteLine($"{rowsAffected} rows deleted");
```

**Return waarde:**
- Aantal beïnvloede rijen (rows affected)
- INSERT: meestal 1
- UPDATE: aantal ge-update rijen
- DELETE: aantal verwijderde rijen

#### 7.2.3 ExecuteScalar - Exact één waarde

**Wanneer gebruiken?**
- SELECT query die exact één waarde teruggeeft
- Aggregate functies (COUNT, SUM, AVG, MAX, MIN)
- Ophalen van één specifieke waarde (bijv. een ID)

**Voorbeelden:**

**1. Aantal rijen tellen:**
```csharp
using SqlConnection connection = new SqlConnection(DBConfig.ConnectionString);
connection.Open();

using SqlCommand command = new SqlCommand(
    "SELECT COUNT(*) FROM Person", 
    connection
);

int count = (int)command.ExecuteScalar();
Console.WriteLine($"Total persons: {count}");
```

**2. Minimum/Maximum waarde:**
```csharp
using SqlCommand command = new SqlCommand(
    "SELECT MIN(BirthDate) FROM Person", 
    connection
);

DateTime oldestBirthDate = (DateTime)command.ExecuteScalar();
Console.WriteLine($"Oldest person born: {oldestBirthDate:dd/MM/yyyy}");
```

**3. Specifieke waarde ophalen:**
```csharp
using SqlCommand command = new SqlCommand(
    "SELECT Name FROM Person WHERE ID = 1", 
    connection
);

string name = (string)command.ExecuteScalar();
```

⚠️ **LET OP:** ExecuteScalar returnt `object`, dus je moet casten naar het juiste type!

**ExecuteReader vs ExecuteScalar:**

```csharp
// ❌ FOUT: ExecuteScalar voor hele rij
var person = command.ExecuteScalar();  
// Geeft alleen de EERSTE kolom van de EERSTE rij

// ✅ CORRECT: ExecuteReader voor hele rij
using SqlDataReader reader = command.ExecuteReader();
if (reader.Read())
{
    int id = (int)reader["ID"];
    string name = (string)reader["Name"];
    string email = (string)reader["Email"];
}
```

**Samenvatting:**

| Methode          | Gebruik voor                          | Return type       |
|------------------|---------------------------------------|-------------------|
| ExecuteReader    | Meerdere rijen/kolommen ophalen       | SqlDataReader     |
| ExecuteNonQuery  | INSERT/UPDATE/DELETE                  | int (rows affected)|
| ExecuteScalar    | Exact één waarde (COUNT, MAX, etc.)   | object            |

## 8. SQL Parameters (KRITIEK BELANGRIJK!)

### 8.1 Het probleem met string concatenation

❌ **GEVAARLIJK - NOOIT DOEN:**

```csharp
// ZEER ONVEILIG!
string name = Console.ReadLine();
string email = Console.ReadLine();

string query = $"INSERT INTO Person (Name, Email) VALUES ('{name}', '{email}')";
SqlCommand command = new SqlCommand(query, connection);
command.ExecuteNonQuery();
```

**Waarom is dit gevaarlijk?**

**1. SQL Injection aanvallen**

Stel dat een gebruiker dit invoert als naam:
```
'; DROP TABLE Person; --
```

De query wordt dan:
```sql
INSERT INTO Person (Name, Email) VALUES (''; DROP TABLE Person; --', 'test@test.com')
```

**Resultaat:** Je hele tabel wordt verwijderd! 💥

**2. Performance problemen**
- Database moet elke keer de query opnieuw parsen
- Geen query plan caching mogelijk
- Langzamer bij veel queries

**3. Type matching problemen**
- DateTime formats verschillen per regio
- Decimalen met punt vs komma
- Moet exact het formaat van de database matchen

### 8.2 De oplossing: SQL Parameters

✅ **VEILIG - ALTIJD DOEN:**

```csharp
string name = Console.ReadLine();
string email = Console.ReadLine();
DateTime birthDate = DateTime.Parse(Console.ReadLine());

string query = @"INSERT INTO Person (Name, Email, BirthDate) 
                 VALUES (@Name, @Email, @BirthDate)";

using SqlCommand command = new SqlCommand(query, connection);

// Parameters toevoegen
command.Parameters.AddWithValue("@Name", name);
command.Parameters.AddWithValue("@Email", email);
command.Parameters.AddWithValue("@BirthDate", birthDate);

command.ExecuteNonQuery();
```

**Wat gebeurt er hier?**

1. **Placeholders** in de query (`@Name`, `@Email`, `@BirthDate`)
2. **Parameters** worden toegevoegd met `AddWithValue()`
3. Database weet: "Dit zijn letterlijke waarden, geen uitvoerbare code"
4. SQL Injection is **onmogelijk**

### 8.3 Voordelen van parameters

**1. Security (Veiligheid)** 🔒
- SQL Injection is onmogelijk
- Data wordt behandeld als data, niet als code
- Automatische escaping van speciale karakters

**2. Performance** ⚡
- Query plan caching
- Database kan queries hergebruiken
- Sneller bij herhaalde queries

**3. Type Safety** ✅
- Automatische type conversie
- Geen formaat problemen met DateTime
- Decimalen worden correct afgehandeld
- Werkt ongeacht regionale instellingen

**4. Leesbaarheid** 📖
- Duidelijk wat variabelen zijn
- Geen complex string concatenation
- Makkelijker te debuggen

### 8.4 Parameters in verschillende query types

**INSERT met parameters:**
```csharp
string query = @"INSERT INTO Person (Name, Email, BirthDate) 
                 VALUES (@Name, @Email, @BirthDate)";
                 
command.Parameters.AddWithValue("@Name", "John Doe");
command.Parameters.AddWithValue("@Email", "john@example.com");
command.Parameters.AddWithValue("@BirthDate", new DateTime(1990, 5, 15));
```

**UPDATE met parameters:**
```csharp
string query = @"UPDATE Person 
                 SET Name = @Name, Email = @Email 
                 WHERE ID = @ID";
                 
command.Parameters.AddWithValue("@Name", "Jane Doe");
command.Parameters.AddWithValue("@Email", "jane@example.com");
command.Parameters.AddWithValue("@ID", 5);
```

**SELECT met WHERE clause:**
```csharp
string query = @"SELECT * FROM Person 
                 WHERE Name LIKE @SearchTerm 
                 AND BirthDate > @MinDate";
                 
command.Parameters.AddWithValue("@SearchTerm", "%John%");
command.Parameters.AddWithValue("@MinDate", new DateTime(1980, 1, 1));
```

**DELETE met parameters:**
```csharp
string query = "DELETE FROM Person WHERE ID = @ID";
command.Parameters.AddWithValue("@ID", 10);
```

### 8.5 Speciale gevallen

**Optionele parameters (nullable):**
```csharp
int? optionalAge = null;

command.Parameters.AddWithValue("@Age", 
    optionalAge ?? (object)DBNull.Value);
```

**Multiple values (geen IN clause met AddWithValue!):**
```csharp
// ❌ Dit werkt NIET:
command.Parameters.AddWithValue("@IDs", "1,2,3,4,5");
// WHERE ID IN (@IDs)  -- Dit faalt!

// ✅ Gebruik een andere benadering:
// Optie 1: Meerdere parameters
WHERE ID IN (@ID1, @ID2, @ID3)

// Optie 2: Dynamische query (voorzichtig!)
// Optie 3: Table-Valued Parameters (advanced)
```

### 8.6 Naamgeving conventies

**Parameter namen:**
- Beginnen altijd met `@`
- Gebruik duidelijke namen die matchen met kolomnamen
- CamelCase of PascalCase (wees consistent)

**Voorbeelden:**
```csharp
// ✅ Goed:
@Name, @Email, @BirthDate
@FirstName, @LastName
@UserID, @ProductID

// ❌ Slecht:
@n, @e, @bd
@param1, @param2
@x, @y, @z
```

### 8.7 DateTime parameters (veelvoorkomend probleem)

**Probleem zonder parameters:**
```csharp
// ❌ Werkt vaak niet of geeft verkeerde datum:
DateTime date = new DateTime(2015, 10, 10);
string query = $"INSERT INTO Person (BirthDate) VALUES ('{date}')";
// Formaat hangt af van regionale instellingen!
```

**Oplossing met parameters:**
```csharp
// ✅ Werkt altijd correct:
DateTime date = new DateTime(2015, 10, 10);
command.Parameters.AddWithValue("@BirthDate", date);
// Database handelt conversie af
```

**DateTime formatteren voor output:**
```csharp
DateTime birthDate = (DateTime)reader["BirthDate"];
Console.WriteLine(birthDate.ToString("dd/MM/yyyy"));  // 15/02/1990
Console.WriteLine(birthDate.ToString("yyyy-MM-dd"));  // 1990-02-15
```

### 8.8 Veelgemaakte fouten

❌ **FOUT 1: String concatenation gebruiken**
```csharp
// NOOIT DOEN!
string query = $"SELECT * FROM Person WHERE Name = '{name}'";
```

❌ **FOUT 2: Parameters vergeten toe te voegen**
```csharp
string query = "SELECT * FROM Person WHERE ID = @ID";
// Vergeten: command.Parameters.AddWithValue("@ID", 5);
command.ExecuteReader();  // FOUT! Parameter @ID niet gevonden
```

❌ **FOUT 3: Verkeerde parameter naam**
```csharp
string query = "SELECT * FROM Person WHERE ID = @PersonID";
command.Parameters.AddWithValue("@ID", 5);  // Naam matcht niet!
```

❌ **FOUT 4: Parameter in verkeerde volgorde**
```csharp
// Dit maakt niet uit! Parameters zijn op naam, niet op volgorde
command.Parameters.AddWithValue("@Email", "test@test.com");
command.Parameters.AddWithValue("@Name", "John");
// Volgorde in query maakt niet uit voor parameters
```

## 9. Exception Handling in ADO.NET

### 9.1 Waarom try-catch?

Bij database operaties kunnen **veel dingen** fout gaan:
- Server is niet bereikbaar
- Database bestaat niet
- Tabel bestaat niet
- Column bestaat niet
- Type mismatch
- Constraint violations (Primary Key, Foreign Key)
- Network timeout
- Permissions problemen

⚠️ **REGEL:** Elke database operatie moet in een try-catch blok!

### 9.2 Basis exception handling

```csharp
try
{
    using SqlConnection connection = new SqlConnection(DBConfig.ConnectionString);
    connection.Open();
    
    using SqlCommand command = new SqlCommand("SELECT * FROM Person", connection);
    using SqlDataReader reader = command.ExecuteReader();
    
    while (reader.Read())
    {
        Console.WriteLine(reader["Name"]);
    }
}
catch (Exception ex)
{
    Console.WriteLine($"Something went wrong: {ex.Message}");
}
```

### 9.3 Exception propagation

**Hoe werkt exception propagation?**

Wanneer een exception optreedt:

1. **Stopt de huidige functie** - code na de exception wordt niet uitgevoerd
2. **Zoekt naar try-catch** - eerst in de huidige functie
3. **Gaat omhoog in de call stack** - naar de functie die deze aanriep
4. **Herhaalt tot gevonden** - of tot aan Main()
5. **Unhandled exception** - als nergens opgevangen = crash

**Voorbeeld:**
```csharp
static void Main()
{
    try
    {
        GetOldestPerson();  // Roept functie aan
    }
    catch (Exception ex)
    {
        Console.WriteLine($"Error in Main: {ex.Message}");
    }
}

static DateTime GetOldestPerson()
{
    try
    {
        using SqlConnection connection = new SqlConnection(DBConfig.ConnectionString);
        connection.Open();  // Exception hier!
        
        // Deze code wordt niet uitgevoerd
        return DateTime.Now;
    }
    catch (SqlException ex)
    {
        Console.WriteLine($"Database error: {ex.Message}");
        throw;  // Propageer verder naar Main
    }
}
```

### 9.4 Throw vs Return

**throw keyword:**

```csharp
catch (Exception ex)
{
    Console.WriteLine(ex.Message);
    throw;  // Gooi exception verder omhoog
}
```

**Wanneer throw gebruiken?**
- Je wilt de exception loggen maar ook laten afhandelen door hogere lagen
- Je wilt niet dat de functie een return waarde geeft bij een error
- Je wilt dat de applicatie stopt bij kritieke fouten

**Return waarde gebruiken:**

```csharp
static DateTime? GetOldestBirthDate()  // Nullable!
{
    try
    {
        // Database operatie
        return oldestDate;
    }
    catch (Exception ex)
    {
        Console.WriteLine(ex.Message);
        return null;  // Geef null terug bij fout
    }
}

// In aanroepende code:
DateTime? result = GetOldestBirthDate();
if (result != null)
{
    Console.WriteLine($"Oldest: {result.Value:dd/MM/yyyy}");
}
else
{
    Console.WriteLine("Could not retrieve date");
}
```

**Nullable types:**
```csharp
// Normaal: DateTime kan niet null zijn
DateTime date = null;  // ❌ FOUT!

// Nullable: DateTime? kan wel null zijn
DateTime? date = null;  // ✅ OK
```

### 9.5 Veelgemaakte fouten

❌ **FOUT 1: Lege catch (ZEER SLECHT!)**
```csharp
try
{
    // Database operatie
}
catch
{
    // Helemaal niets!
}
// Programma doet alsof er niets gebeurd is
// Developer weet niet dat er een fout is
// User weet niet dat er een fout is
// ONACCEPTABEL!
```

**Waarom is dit zo erg?**
- Silent failures - niemand weet dat er iets fout ging
- Moeilijk te debuggen
- Data inconsistenties
- Security risico's

✅ **MINIMUM: Log de fout**
```csharp
catch (Exception ex)
{
    Console.WriteLine($"Error: {ex.Message}");
    // Of: schrijf naar log file
    // Of: toon melding aan gebruiker
}
```

❌ **FOUT 2: Nutteloos re-throw**
```csharp
try
{
    // Code
}
catch (Exception ex)
{
    throw;  // Alleen throw, niets anders
}
// Dit heeft GEEN nut!
```

**Waarom heeft dit geen nut?**
- Exception propageert automatisch als je hem niet vangt
- Dit vangt en gooit direct terug
- Exact hetzelfde als geen try-catch

✅ **CORRECT: Re-throw na actie**
```csharp
try
{
    // Code
}
catch (Exception ex)
{
    Console.WriteLine($"Error: {ex.Message}");
    throw;  // Log EN propageer
}
```

❌ **FOUT 3: Alle return paden niet afhandelen**
```csharp
static DateTime GetDate()
{
    try
    {
        // Database operatie
        return date;
    }
    catch (Exception ex)
    {
        Console.WriteLine(ex.Message);
        // Geen return of throw!
    }
}
// Compiler error: "Not all code paths return a value"
```

✅ **CORRECT: Alle paden afhandelen**
```csharp
// Optie 1: throw in catch
catch (Exception ex)
{
    Console.WriteLine(ex.Message);
    throw;
}

// Optie 2: return in catch (met nullable)
catch (Exception ex)
{
    Console.WriteLine(ex.Message);
    return null;
}
```

## 10. Transactions

### 10.1 Wat is een transaction?

Een **transaction** is een groep database operaties die:
- **Allemaal moeten slagen** (commit)
- **OF allemaal moeten mislukken** (rollback)
- **Nooit half-half** kunnen zijn

**Analogie: Bankrekening**
```
Actie: € 100 overmaken van Account A naar Account B

Stap 1: Trek € 100 af van Account A
Stap 2: Voeg € 100 toe aan Account B

Zonder transaction:
- Stap 1 lukt → Account A: -€ 100
- Stap 2 faalt → Account B: +€ 0
- Geld is VERDWENEN! 💸

Met transaction:
- Stap 1 lukt → Tijdelijk: Account A: -€ 100
- Stap 2 faalt → ROLLBACK
- Resultaat: Beide accounts ongewijzigd ✅
```

### 10.2 ACID principes

Transactions volgen de **ACID** principes:

**A - Atomicity (Atomiciteit)**
- All or nothing
- Geen halve transacties
- Alles lukt of alles wordt teruggedraaid

**C - Consistency (Consistentie)**
- Database blijft in geldige staat
- Constraints worden gerespecteerd
- Geen ongeldige data

**I - Isolation (Isolatie)**
- Transactions beïnvloeden elkaar niet
- Tussenresultaten zijn niet zichtbaar voor anderen
- Voorkomen van race conditions

**D - Durability (Duurzaamheid)**
- Gecommitte data blijft behouden
- Zelfs bij crashes of power failure
- Permanent opgeslagen

### 10.3 Transaction implementatie

**Stappen:**

1. **Connection openen**
2. **Transaction beginnen**
3. **Commands koppelen aan transaction**
4. **Commands uitvoeren**
5. **Succes? → Commit**
6. **Fout? → Rollback**

**Code voorbeeld:**

```csharp
public void TransferMoney(int fromAccountID, int toAccountID, decimal amount)
{
    using SqlConnection connection = new SqlConnection(DBConfig.ConnectionString);
    connection.Open();
    
    using SqlTransaction transaction = connection.BeginTransaction();
    
    try
    {
        // Stap 1: Geld aftrekken
        using SqlCommand withdrawCommand = new SqlCommand(
            @"UPDATE BankAccounts 
              SET Balance = Balance - @Amount 
              WHERE ID = @AccountID",
            connection,
            transaction  // ← Koppel aan transaction!
        );
        withdrawCommand.Parameters.AddWithValue("@Amount", amount);
        withdrawCommand.Parameters.AddWithValue("@AccountID", fromAccountID);
        withdrawCommand.ExecuteNonQuery();
        
        // Stap 2: Geld toevoegen
        using SqlCommand depositCommand = new SqlCommand(
            @"UPDATE BankAccounts 
              SET Balance = Balance + @Amount 
              WHERE ID = @AccountID",
            connection,
            transaction  // ← Koppel aan transaction!
        );
        depositCommand.Parameters.AddWithValue("@Amount", amount);
        depositCommand.Parameters.AddWithValue("@AccountID", toAccountID);
        depositCommand.ExecuteNonQuery();
        
        // Beide gelukt? Commit!
        transaction.Commit();
        Console.WriteLine("Transfer successful!");
    }
    catch (Exception ex)
    {
        // Iets ging fout? Rollback!
        transaction.Rollback();
        Console.WriteLine($"Transfer failed: {ex.Message}");
        throw;
    }
}
```

### 10.4 Belangrijke details

**1. Transaction koppelen aan commands:**
```csharp
// ❌ FOUT: Transaction niet gekoppeld
SqlCommand cmd = new SqlCommand(query, connection);
// Command is niet onderdeel van transaction!

// ✅ CORRECT:
SqlCommand cmd = new SqlCommand(query, connection, transaction);
```

**2. Commit OF Rollback - altijd één van beide:**
```csharp
// ❌ FOUT: Vergeten te committen
try
{
    // Commands uitvoeren
    // Vergeten: transaction.Commit();
}
// Transaction wordt niet doorgevoerd!

// ✅ CORRECT:
try
{
    // Commands
    transaction.Commit();  // ← Altijd!
}
catch
{
    transaction.Rollback();  // ← Bij fout
}
```

**3. Connection moet open blijven:**
```csharp
// ❌ FOUT:
connection.Open();
// ... commando 1 ...
connection.Close();  // Te vroeg gesloten!
// ... commando 2 ... FOUT!

// ✅ CORRECT:
connection.Open();
// ... alle commando's ...
transaction.Commit();
connection.Close();  // Pas aan het einde
```

### 10.5 Praktische voorbeelden

**Voorbeeld 1: Bestelling met producten**
```csharp
public void CreateOrder(int customerID, List<OrderItem> items)
{
    using SqlConnection connection = new SqlConnection(DBConfig.ConnectionString);
    connection.Open();
    using SqlTransaction transaction = connection.BeginTransaction();
    
    try
    {
        // 1. Maak bestelling aan
        using SqlCommand orderCmd = new SqlCommand(
            "INSERT INTO Orders (CustomerID, OrderDate) VALUES (@CustomerID, @Date); SELECT SCOPE_IDENTITY();",
            connection, transaction
        );
        orderCmd.Parameters.AddWithValue("@CustomerID", customerID);
        orderCmd.Parameters.AddWithValue("@Date", DateTime.Now);
        int orderID = Convert.ToInt32(orderCmd.ExecuteScalar());
        
        // 2. Voeg producten toe
        foreach (var item in items)
        {
            using SqlCommand itemCmd = new SqlCommand(
                "INSERT INTO OrderItems (OrderID, ProductID, Quantity) VALUES (@OrderID, @ProductID, @Quantity)",
                connection, transaction
            );
            itemCmd.Parameters.AddWithValue("@OrderID", orderID);
            itemCmd.Parameters.AddWithValue("@ProductID", item.ProductID);
            itemCmd.Parameters.AddWithValue("@Quantity", item.Quantity);
            itemCmd.ExecuteNonQuery();
        }
        
        transaction.Commit();
    }
    catch
    {
        transaction.Rollback();
        throw;
    }
}
```

**Voorbeeld 2: Cascade delete**
```csharp
public void DeleteCustomer(int customerID)
{
    using SqlConnection connection = new SqlConnection(DBConfig.ConnectionString);
    connection.Open();
    using SqlTransaction transaction = connection.BeginTransaction();
    
    try
    {
        // 1. Delete bestellingen
        using SqlCommand deleteOrders = new SqlCommand(
            "DELETE FROM Orders WHERE CustomerID = @ID",
            connection, transaction
        );
        deleteOrders.Parameters.AddWithValue("@ID", customerID);
        deleteOrders.ExecuteNonQuery();
        
        // 2. Delete klant
        using SqlCommand deleteCustomer = new SqlCommand(
            "DELETE FROM Customers WHERE ID = @ID",
            connection, transaction
        );
        deleteCustomer.Parameters.AddWithValue("@ID", customerID);
        deleteCustomer.ExecuteNonQuery();
        
        transaction.Commit();
    }
    catch
    {
        transaction.Rollback();
        throw;
    }
}
```

### 10.6 Wanneer transactions gebruiken?

✅ **GEBRUIK transactions bij:**
- Geld transfers
- Multi-step registraties
- Bestellingen met meerdere items
- Cascade deletes
- Updates over meerdere tabellen
- Alles waar data consistent moet blijven

❌ **NIET nodig bij:**
- Enkele SELECT query
- Enkele INSERT zonder dependencies
- Read-only operaties
- Independent updates

## 11. Best Practices & Veelgemaakte Fouten

### 11.1 Connection management

✅ **GOED:**
```csharp
// Gebruik using voor automatische cleanup
using SqlConnection connection = new SqlConnection(DBConfig.ConnectionString);
connection.Open();
// Werk met database
// Automatisch Dispose() aan einde scope
```

❌ **FOUT:**
```csharp
// Connectie niet sluiten
SqlConnection connection = new SqlConnection(DBConfig.ConnectionString);
connection.Open();
// Werk met database
// Vergeten te sluiten! Memory leak!
```

### 11.2 SQL Queries

✅ **GOED:**
```csharp
// Queries in aparte constanten of variabelen
private const string GET_ALL_PERSONS = "SELECT * FROM Person";
private const string INSERT_PERSON = 
    @"INSERT INTO Person (Name, Email) 
      VALUES (@Name, @Email)";

// Gebruik in code
using SqlCommand cmd = new SqlCommand(INSERT_PERSON, connection);
```

❌ **FOUT:**
```csharp
// Queries hard-coded overal in je code
using SqlCommand cmd = new SqlCommand(
    "SELECT * FROM Person WHERE ID = " + id,  // Geen parameters!
    connection
);
```

### 11.3 Parameters

✅ **GOED:**
```csharp
// ALTIJD parameters gebruiken
command.Parameters.AddWithValue("@Name", name);
command.Parameters.AddWithValue("@Email", email);
```

❌ **FOUT:**
```csharp
// String concatenation/interpolation
string query = $"INSERT INTO Person (Name) VALUES ('{name}')";
// SQL INJECTION RISICO!
```

### 11.4 Exception Handling

✅ **GOED:**
```csharp
try
{
    // Database operatie
}
catch (Exception ex)
{
    Console.WriteLine($"Error: {ex.Message}");
    // OF: Log naar file
    // OF: Toon aan gebruiker
    throw;  // Optioneel: propageer verder
}
```

❌ **FOUT:**
```csharp
try
{
    // Database operatie
}
catch
{
    // Helemaal niets - ZEER SLECHT!
}
```

### 11.5 Code organisatie

✅ **GOED:**
```csharp
// Aparte mapper klassen
public class PersonMapper
{
    private const string ConnectionString = DBConfig.ConnectionString;
    
    public List<Person> GetAll() { }
    public Person GetById(int id) { }
    public void Insert(Person person) { }
    public void Update(Person person) { }
    public void Delete(int id) { }
}
```

❌ **FOUT:**
```csharp
// Alles in één klasse
static void Main()
{
    // 500 regels database code hier
    // Onleesbaar en moeilijk te onderhouden
}
```

### 11.6 Connection String opslag

✅ **GOED:**
```csharp
// Centraal in aparte klasse
internal static class DBConfig
{
    public const string ConnectionString = @"...";
}

// Gebruik overal
var conn = new SqlConnection(DBConfig.ConnectionString);
```

❌ **FOUT:**
```csharp
// Overal gedupliceerd
var conn1 = new SqlConnection("Data Source=...;");
var conn2 = new SqlConnection("Data Source=...;");
var conn3 = new SqlConnection("Data Source=...;");
// Eén typo en het werkt niet meer!
```

## 12. Puntenaftrek bij evaluaties

### 12.1 Veel voorkomende fouten die punten kosten

🚨 **KRITIEKE FOUTEN (veel punten aftrek):**

**1. Geen parameters gebruiken**
```csharp
❌ String interpolation/concatenation in queries
✅ Altijd AddWithValue() gebruiken
```

**2. Database code in presentation laag**
```csharp
❌ SqlCommand in je WPF window
✅ Database code alleen in Persistence laag (3-lagen model)
```

**3. Connection niet sluiten**
```csharp
❌ Connection open laten staan
✅ Altijd using statement gebruiken
```

**4. Lege catch blocks**
```csharp
❌ catch { } zonder enige actie
✅ Minimaal exception loggen
```

**5. Nutteloze re-throw**
```csharp
❌ catch (Exception ex) { throw; }  // Alleen throw, niets anders
✅ catch (Exception ex) { Log(ex); throw; }  // Actie + throw
```

### 12.2 Theorie vragen

Bij mondelinge verdediging kunnen theorie vragen gesteld worden zoals:

**Veelvoorkomende vragen:**
- Waarom gebruiken we parameters in SQL commando's?
- Wat is het verschil tussen ExecuteReader, ExecuteNonQuery en ExecuteScalar?
- Leg uit wat een transaction is en wanneer je die gebruikt
- Wat is het verschil tussen Close() en Dispose()?
- Waarom gebruiken we een using statement?
- Wat is SQL Injection en hoe voorkom je het?
- Wat is exception propagation?

**Tip:** Deze vragen zijn niet om je te pesten, maar om te controleren of je de concepten begrijpt die je praktisch gebruikt!

## 13. Integratie met 3-Lagen Model

### 13.1 Waar hoort ADO.NET thuis?

In het 3-lagen model hoort **alle database code** thuis in de **Persistence laag**.

```
┌─────────────────────────────────────┐
│   PRESENTATION                      │
│   (Console/WPF)                     │
│   ❌ GEEN SqlConnection             │
│   ❌ GEEN SqlCommand                │
└──────────────┬──────────────────────┘
               ↓
┌─────────────────────────────────────┐
│   DOMAIN                            │
│   (Business Logic)                  │
│   ❌ GEEN SqlConnection             │
│   ❌ GEEN SqlCommand                │
└──────────────┬──────────────────────┘
               ↓
┌─────────────────────────────────────┐
│   PERSISTENCE                       │
│   ✅ SqlConnection                  │
│   ✅ SqlCommand                     │
│   ✅ Transactions                   │
│   ✅ Mappers/Repositories           │
└─────────────────────────────────────┘
```

### 13.2 Mapper voorbeeld

**PersonMapper.cs (in Persistence laag):**

```csharp
namespace YourApp.Persistence
{
    public class PersonMapper
    {
        public List<Person> GetAll()
        {
            List<Person> persons = new List<Person>();
            
            try
            {
                using SqlConnection connection = new SqlConnection(DBConfig.ConnectionString);
                connection.Open();
                
                using SqlCommand command = new SqlCommand(
                    "SELECT ID, Name, Email, BirthDate FROM Person",
                    connection
                );
                
                using SqlDataReader reader = command.ExecuteReader();
                
                while (reader.Read())
                {
                    persons.Add(new Person
                    {
                        ID = (int)reader["ID"],
                        Name = (string)reader["Name"],
                        Email = (string)reader["Email"],
                        BirthDate = (DateTime)reader["BirthDate"]
                    });
                }
            }
            catch (Exception ex)
            {
                Console.WriteLine($"Database error: {ex.Message}");
                throw;
            }
            
            return persons;
        }
        
        public void Insert(Person person)
        {
            try
            {
                using SqlConnection connection = new SqlConnection(DBConfig.ConnectionString);
                connection.Open();
                
                using SqlCommand command = new SqlCommand(
                    @"INSERT INTO Person (Name, Email, BirthDate) 
                      VALUES (@Name, @Email, @BirthDate)",
                    connection
                );
                
                command.Parameters.AddWithValue("@Name", person.Name);
                command.Parameters.AddWithValue("@Email", person.Email);
                command.Parameters.AddWithValue("@BirthDate", person.BirthDate);
                
                command.ExecuteNonQuery();
            }
            catch (Exception ex)
            {
                Console.WriteLine($"Database error: {ex.Message}");
                throw;
            }
        }
        
        public void Delete(int id)
        {
            try
            {
                using SqlConnection connection = new SqlConnection(DBConfig.ConnectionString);
                connection.Open();
                
                using SqlCommand command = new SqlCommand(
                    "DELETE FROM Person WHERE ID = @ID",
                    connection
                );
                
                command.Parameters.AddWithValue("@ID", id);
                command.ExecuteNonQuery();
            }
            catch (Exception ex)
            {
                Console.WriteLine($"Database error: {ex.Message}");
                throw;
            }
        }
    }
}
```

### 13.3 Package installatie in 3-lagen model

⚠️ **BELANGRIJK:** Installeer de SqlClient package **ALLEEN** in de Persistence laag!

```
Solution 'MyApp'
├─ MyApp.Startup
│  └─ Dependencies (geen SqlClient)
├─ MyApp.Presentation  
│  └─ Dependencies (geen SqlClient)
├─ MyApp.Domain
│  └─ Dependencies (geen SqlClient)
└─ MyApp.Persistence
   └─ Dependencies
      └─ Microsoft.Data.SqlClient ✅
```

**Waarom?**
- Scheiding van verantwoordelijkheden
- Persistence laag is de enige die met database praat
- Andere lagen hebben package niet nodig
- Betere architectuur

## 14. Samenvatting

### 14.1 De belangrijkste concepten

**ADO.NET Fundamentals:**
- ADO.NET = platform-agnostische data access layer
- SqlConnection = verbinding met database
- SqlCommand = query/commando uitvoeren
- SqlDataReader = resultaten lezen

**Drie Execute methodes:**
- **ExecuteReader:** Meerdere rijen/kolommen ophalen
- **ExecuteNonQuery:** INSERT/UPDATE/DELETE (geen resultaat)
- **ExecuteScalar:** Exact één waarde (COUNT, MAX, etc.)

**Security - Parameters:**
- ALTIJD parameters gebruiken (nooit string concatenation!)
- AddWithValue() voor elke dynamische waarde
- Voorkomt SQL Injection
- Betere performance
- Automatische type handling

**Exception Handling:**
- Altijd try-catch bij database operaties
- Minimaal: log de fout
- Optioneel: throw voor propagation
- NOOIT lege catch blocks!

**Transactions:**
- Groepeer gerelateerde operaties
- All or nothing (ACID)
- Commit bij succes
- Rollback bij fout
- Gebruik bij: geld transfers, multi-step operaties

**Using Statement:**
- Automatische resource cleanup
- Gebruik voor SqlConnection, SqlCommand, SqlDataReader
- Moderne syntax: `using var connection = ...`
- Voorkomt memory leaks

**Best Practices:**
- Connection String centraal opslaan
- Mapper klassen per entity
- Queries in constanten
- using statements voor alle IDisposable objecten
- Proper exception handling

### 14.2 Checklist voor evaluaties

✅ **Controleer altijd:**

**Security:**
- [ ] Alle queries gebruiken parameters
- [ ] Geen string concatenation/interpolation in queries
- [ ] Connection String niet in code zelf

**Resource Management:**
- [ ] using statements voor alle connections
- [ ] using statements voor alle commands
- [ ] using statements voor alle readers
- [ ] Geen open connecties vergeten

**Exception Handling:**
- [ ] try-catch rond alle database operaties
- [ ] Exceptions worden gelogd (minimaal)
- [ ] Geen lege catch blocks
- [ ] Nutteloze re-throw vermeden

**Code Organisatie:**
- [ ] Database code alleen in Persistence laag
- [ ] Mappers per entity
- [ ] Connection String in aparte config klasse
- [ ] Queries in constanten/variabelen

**Transactions:**
- [ ] Gebruikt waar nodig (multi-step operaties)
- [ ] Altijd commit OF rollback
- [ ] Alle commands gekoppeld aan transaction
- [ ] Proper exception handling

**3-Lagen Model:**
- [ ] SqlClient package alleen in Persistence laag
- [ ] Geen database code in Presentation/Domain
- [ ] Proper dependencies (Persistence → Domain)

### 14.3 Veelvoorkomende valkuilen

**1. Type casting vergeten**
```csharp
// ❌ FOUT:
string name = reader["Name"];  // Compile error

// ✅ CORRECT:
string name = (string)reader["Name"];
```

**2. Column naam verkeerd**
```csharp
// ❌ FOUT:
int id = (int)reader["Id"];  // Runtime error als kolom "ID" heet

// ✅ CORRECT:
int id = (int)reader["ID"];  // Exact zoals in database
```

**3. Reader niet sluiten**
```csharp
// ❌ FOUT:
SqlDataReader reader = command.ExecuteReader();
// Werk met reader
// Vergeten te sluiten!

// ✅ CORRECT:
using SqlDataReader reader = command.ExecuteReader();
// Automatisch disposed
```

**4. Transaction niet committen**
```csharp
// ❌ FOUT:
using SqlTransaction transaction = connection.BeginTransaction();
// Commands uitvoeren
// Vergeten transaction.Commit()!
// Rollback gebeurt automatisch

// ✅ CORRECT:
try
{
    // Commands
    transaction.Commit();
}
catch
{
    transaction.Rollback();
    throw;
}
```

**5. Datetime formatting**
```csharp
// ❌ FOUT:
DateTime date = (DateTime)reader["BirthDate"];
Console.WriteLine(date);  // Lelijk format met uren

// ✅ CORRECT:
DateTime date = (DateTime)reader["BirthDate"];
Console.WriteLine(date.ToString("dd/MM/yyyy"));  // 15/02/1990
```

---

## Bijlage: Complete voorbeeldcode

### Voorbeeld 1: Volledige CRUD Mapper

```csharp
using Microsoft.Data.SqlClient;
using System;
using System.Collections.Generic;

namespace BankApp.Persistence
{
    public class BankAccountMapper
    {
        private const string GET_ALL = "SELECT ID, AccountHolder, Balance FROM BankAccounts";
        private const string GET_BY_ID = "SELECT ID, AccountHolder, Balance FROM BankAccounts WHERE ID = @ID";
        private const string INSERT = "INSERT INTO BankAccounts (AccountHolder, Balance) VALUES (@Holder, @Balance)";
        private const string UPDATE = "UPDATE BankAccounts SET AccountHolder = @Holder, Balance = @Balance WHERE ID = @ID";
        private const string DELETE = "DELETE FROM BankAccounts WHERE ID = @ID";
        
        public List<BankAccount> GetAll()
        {
            List<BankAccount> accounts = new List<BankAccount>();
            
            try
            {
                using SqlConnection connection = new SqlConnection(DBConfig.ConnectionString);
                connection.Open();
                
                using SqlCommand command = new SqlCommand(GET_ALL, connection);
                using SqlDataReader reader = command.ExecuteReader();
                
                while (reader.Read())
                {
                    accounts.Add(new BankAccount
                    {
                        ID = (int)reader["ID"],
                        AccountHolder = (string)reader["AccountHolder"],
                        Balance = (decimal)reader["Balance"]
                    });
                }
            }
            catch (Exception ex)
            {
                Console.WriteLine($"Error retrieving accounts: {ex.Message}");
                throw;
            }
            
            return accounts;
        }
        
        public BankAccount GetById(int id)
        {
            try
            {
                using SqlConnection connection = new SqlConnection(DBConfig.ConnectionString);
                connection.Open();
                
                using SqlCommand command = new SqlCommand(GET_BY_ID, connection);
                command.Parameters.AddWithValue("@ID", id);
                
                using SqlDataReader reader = command.ExecuteReader();
                
                if (reader.Read())
                {
                    return new BankAccount
                    {
                        ID = (int)reader["ID"],
                        AccountHolder = (string)reader["AccountHolder"],
                        Balance = (decimal)reader["Balance"]
                    };
                }
                
                return null;
            }
            catch (Exception ex)
            {
                Console.WriteLine($"Error retrieving account: {ex.Message}");
                throw;
            }
        }
        
        public void Insert(BankAccount account)
        {
            try
            {
                using SqlConnection connection = new SqlConnection(DBConfig.ConnectionString);
                connection.Open();
                
                using SqlCommand command = new SqlCommand(INSERT, connection);
                command.Parameters.AddWithValue("@Holder", account.AccountHolder);
                command.Parameters.AddWithValue("@Balance", account.Balance);
                
                command.ExecuteNonQuery();
            }
            catch (Exception ex)
            {
                Console.WriteLine($"Error inserting account: {ex.Message}");
                throw;
            }
        }
        
        public void Update(BankAccount account)
        {
            try
            {
                using SqlConnection connection = new SqlConnection(DBConfig.ConnectionString);
                connection.Open();
                
                using SqlCommand command = new SqlCommand(UPDATE, connection);
                command.Parameters.AddWithValue("@ID", account.ID);
                command.Parameters.AddWithValue("@Holder", account.AccountHolder);
                command.Parameters.AddWithValue("@Balance", account.Balance);
                
                command.ExecuteNonQuery();
            }
            catch (Exception ex)
            {
                Console.WriteLine($"Error updating account: {ex.Message}");
                throw;
            }
        }
        
        public void Delete(int id)
        {
            try
            {
                using SqlConnection connection = new SqlConnection(DBConfig.ConnectionString);
                connection.Open();
                
                using SqlCommand command = new SqlCommand(DELETE, connection);
                command.Parameters.AddWithValue("@ID", id);
                
                command.ExecuteNonQuery();
            }
            catch (Exception ex)
            {
                Console.WriteLine($"Error deleting account: {ex.Message}");
                throw;
            }
        }
        
        public void TransferMoney(int fromAccountID, int toAccountID, decimal amount)
        {
            using SqlConnection connection = new SqlConnection(DBConfig.ConnectionString);
            connection.Open();
            
            using SqlTransaction transaction = connection.BeginTransaction();
            
            try
            {
                // Withdraw from source account
                using SqlCommand withdrawCommand = new SqlCommand(
                    "UPDATE BankAccounts SET Balance = Balance - @Amount WHERE ID = @AccountID",
                    connection,
                    transaction
                );
                withdrawCommand.Parameters.AddWithValue("@Amount", amount);
                withdrawCommand.Parameters.AddWithValue("@AccountID", fromAccountID);
                withdrawCommand.ExecuteNonQuery();
                
                // Deposit to destination account
                using SqlCommand depositCommand = new SqlCommand(
                    "UPDATE BankAccounts SET Balance = Balance + @Amount WHERE ID = @AccountID",
                    connection,
                    transaction
                );
                depositCommand.Parameters.AddWithValue("@Amount", amount);
                depositCommand.Parameters.AddWithValue("@AccountID", toAccountID);
                depositCommand.ExecuteNonQuery();
                
                // Both succeeded - commit
                transaction.Commit();
                Console.WriteLine("Transfer successful!");
            }
            catch (Exception ex)
            {
                // Something went wrong - rollback
                transaction.Rollback();
                Console.WriteLine($"Transfer failed: {ex.Message}");
                throw;
            }
        }
    }
}
```

### Voorbeeld 2: DBConfig klasse

```csharp
namespace BankApp.Persistence
{
    internal static class DBConfig
    {
        public const string ConnectionString = 
            @"Data Source=.\SQLEXPRESS;Initial Catalog=BankExample;Integrated Security=True;Trust Server Certificate=True";
    }
}
```

### Voorbeeld 3: Model klasse

```csharp
namespace BankApp.Domain.Model
{
    public class BankAccount
    {
        public int ID { get; set; }
        public string AccountHolder { get; set; }
        public decimal Balance { get; set; }
    }
}
```

Dit hoofdstuk bevat alle essentiële informatie over ADO.NET die je nodig hebt voor dit vak, inclusief best practices, veelgemaakte fouten, en uitgebreide code voorbeelden.
