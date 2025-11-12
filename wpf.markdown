layout: page
title: "WPF"
permalink: /WPF

# Hoofdstuk: WPF – Deel 2

## 1. Inleiding

In het vorige hoofdstuk maakten we kennis met **WPF (Windows Presentation Foundation)** als framework om grafische gebruikersinterfaces te bouwen in C#.  
Tot nu toe werkten we enkel met **consoletoepassingen**, maar met WPF voegen we een visuele laag toe aan onze programma’s.

### Wat is WPF?

- **WPF** is een *framework* binnen .NET waarmee je Windows-desktopapplicaties met een grafische interface kunt ontwikkelen.  
- Het combineert **XAML** (voor de visuele structuur) met **C#** (voor de logica).  
- Het biedt ondersteuning voor moderne UI-concepten zoals **data binding**, **layout management**, **events** en **stijlbeheer**.

Hoewel er opvolgers bestaan, zoals **MAUI** en **WinUI**, blijft WPF relevant dankzij zijn eenvoud, stabiliteit en lage instapdrempel.  
In de praktijk wordt C# vaker gebruikt voor **backendontwikkeling** (bijvoorbeeld met ASP.NET), maar een basiskennis van WPF blijft nuttig — bijvoorbeeld voor interne tools of prototypes.

---

## 2. Structuur van een WPF-project

Een WPF-project bestaat typisch uit **drie lagen**:

1. **C#-code (programmacode)**  
   - Dit zijn je “gewone” C#-klassen, ook wel *POCO’s* genoemd (*Plain Old C# Objects*).  
   - Ze bevatten de logica en datamodellen van je applicatie.

2. **XAML-bestanden**  
   - Beschrijven de **gebruikersinterface** in een XML-achtige syntax.  
   - Bevatten de visuele componenten: knoppen, labels, tekstvakken, enz.  
   - Kunnen zowel visueel bewerkt worden via de **Designer** als manueel in **XAML-code**.

3. **Code-behind**  
   - Voor elk XAML-bestand bestaat een bijhorend `.cs`-bestand waarin de interactielogica staat.  
   - Hier schrijf je wat er gebeurt bij gebruikersacties (zoals klikken, slepen, sluiten…).

De compiler voegt het XAML-bestand en het code-behind samen tot één klasse.

---

## 3. De User Interface-laag

### Designer en XAML

De **Designer** biedt een *What You See Is What You Get*-interface:  
je sleept componenten (buttons, labels, panels, …) op het venster en Visual Studio genereert automatisch de bijhorende XAML-code.

Wijzigingen in de **Designer** en de **XAML-editor** worden onmiddellijk in beide richtingen gesynchroniseerd.

### Belangrijke XAML-eigenschappen

Elke UI-component heeft eigenschappen (attributes), zoals:

| Eigenschap | Betekenis |
|-------------|------------|
| `Width` / `Height` | Bepaalt de grootte |
| `Margin` / `Padding` | Afstand tot andere elementen |
| `HorizontalAlignment` / `VerticalAlignment` | Uitlijning binnen de parentcontainer |
| `IsEnabled` | Activeert of deactiveert interactie |
| `Name` | De variabelenaam waarmee je het element in code kunt aanspreken |

De **`Name`-property** is niet verplicht, maar sterk aanbevolen. Ze maakt het eenvoudig om componenten in de code-behind te manipuleren.

---

## 4. Layouts en Panels

Een WPF-window kan slechts **één rechtstreeks kindelement** bevatten.  
Daarom gebruik je **layoutcontainers (panels)** om meerdere elementen te groeperen.

### Belangrijkste paneltypes

| Panel | Beschrijving |
|--------|---------------|
| **Grid** | Meest flexibele layout: je definieert rijen en kolommen. |
| **StackPanel** | Plaatst elementen onder of naast elkaar (oriëntatie instelbaar). |
| **WrapPanel** | Plaatst elementen naast elkaar en “wrapt” automatisch naar een nieuwe rij. |
| **Canvas** | Laat absolute positionering toe, maar wordt afgeraden wegens inflexibel. |

#### Grid

- Werkt met `<RowDefinition>` en `<ColumnDefinition>`.  
- Elke definitie krijgt een hoogte/breedte op drie manieren:
  - **Auto** – past zich aan aan de grootte van de inhoud.
  - **Vaste waarde (px)** – blijft constant.
  - **Steroperator (*)** – verdeelt resterende ruimte proportioneel.

Voorbeeld:

```xml
<Grid>
    <Grid.RowDefinitions>
        <RowDefinition Height="Auto"/>
        <RowDefinition Height="*"/>
    </Grid.RowDefinitions>
    <Grid.ColumnDefinitions>
        <ColumnDefinition Width="2*"/>
        <ColumnDefinition Width="*"/>
    </Grid.ColumnDefinitions>
</Grid>

## 5. Controls

### Tekstcomponenten

| Component   | Doel                                   |
|-------------|----------------------------------------|
| **Label**   | Toont korte tekst (via `Content`)      |
| **TextBlock** | Toont langere of meerregelige tekst  |
| **TextBox** | Laat de gebruiker tekst invoeren       |

### Selectiecomponenten

| Component   | Doel                                                                 |
|-------------|----------------------------------------------------------------------|
| **CheckBox**   | Meerdere opties kunnen aangevinkt worden                         |
| **RadioButton**| Slechts één optie per groep (via `GroupName`)                    |
| **ListBox**    | Lijst van items                                                   |
| **ComboBox**   | Inklapbare lijst (vergelijkbaar met een `ListBox` met dropdown)  |
| **DataGrid**   | Toont tabulaire data, kolommen worden automatisch afgeleid van de objectproperties |

---

## 6. Oefening: Layout met vijf knoppen

**Doel:** vijf knoppen toevoegen aan een venster die zich correct herschalen bij aanpassing van het vensterformaat.

### Vereisten

- Geen gebruik van `Grid` of `RowDefinitions`/`ColumnDefinitions`.  
- Enkel `Margin`, `Alignment`, `Width` en `Height` gebruiken.  
- Elke knop behoudt zijn relatieve positie of afstanden bij het herschalen.

**Belangrijk:**

- `HorizontalAlignment` kan `Left`, `Right`, `Center` of `Stretch` zijn.  
- `VerticalAlignment` kan `Top`, `Bottom`, `Center` of `Stretch` zijn.  
- Met `Stretch` laat je het element mee uitrekken met het venster.  
- Gebruik waar nodig `MinWidth`, `MinHeight`, `MaxWidth` en `MaxHeight` om te voorkomen dat knoppen uit beeld verdwijnen.

**Voorbeeld XAML (conceptueel):**
```xml
<Window ...>
  <Grid>
    <!-- De oefening vraagt om geen Grid-rows/cols te gebruiken
         — dus ieder Button wordt direct gepositioneerd met Alignment & Margin -->
    <Button Content="LinksBoven" Width="100" Height="30"
            HorizontalAlignment="Left" VerticalAlignment="Top" Margin="10"/>
    <Button Content="Boven" Width="120" Height="30"
            HorizontalAlignment="Center" VerticalAlignment="Top" Margin="0,10,0,0"/>
    <Button Content="RechtsBoven" Width="100" Height="30"
            HorizontalAlignment="Right" VerticalAlignment="Top" Margin="10"/>
    <Button Content="Links" Width="100" Height="40"
            HorizontalAlignment="Left" VerticalAlignment="Center" Margin="10"/>
    <Button Content="RechtsOnder" Width="100" Height="40"
            HorizontalAlignment="Right" VerticalAlignment="Bottom" Margin="10"/>
  </Grid>
</Window>
```

---

## 7. Code-behind

Elk XAML-bestand heeft een bijhorend `.xaml.cs`-bestand.  
Dit bestand bevat de C#-code die hoort bij het venster of de pagina.

### De `partial` keyword

Een WPF-venster bestaat uit twee gedeeltes:

- De **gegenereerde klasse** op basis van XAML.  
- De **code-behindklasse** die jij schrijft.

Het `partial` keyword zorgt ervoor dat beide delen worden samengevoegd tot één klasse bij compilatie.

---

## 8. MessageBox

Een eenvoudige manier om meldingen te tonen:

```csharp
MessageBox.Show("Hello World");
```

Er bestaan ook overloads om knoppen, pictogrammen en titels te specificeren:

```csharp
MessageBox.Show(
    "Bent u zeker dat u wilt verwijderen?",
    "Bevestiging",
    MessageBoxButton.YesNoCancel,
    MessageBoxImage.Warning
);
```

De returnwaarde is van het type `MessageBoxResult`, waarmee je kunt nagaan welke knop de gebruiker koos:

```csharp
var result = MessageBox.Show("Doorgaan?", "Vraag", MessageBoxButton.YesNo);
if (result == MessageBoxResult.Yes)
{
    // Actie uitvoeren
}
```

---

## 9. Events en Event Handlers

### Wat is een event?

Een event is een **gebeurtenis** die door een component wordt uitgestuurd, bijvoorbeeld:

- `Click` bij een knop  
- `MouseEnter` bij een muisovergang  
- `Loaded` bij het openen van een venster

Je kunt code laten uitvoeren wanneer zo’n event plaatsvindt.

### Eventhandler aanmaken

1. In Visual Studio selecteer je een component.  
2. In het *Properties*-venster klik je op het **bliksemicoon** (⚡).  
3. Dubbelklik op het gewenste event (bv. `Click`).  
   - Visual Studio maakt automatisch een methode in de code-behind aan.  
   - In de XAML wordt het event gelinkt aan die methode.

**Voorbeeld:**
```xml
<Button Name="btnSubmit" Click="btnSubmit_Click" Content="Verzend"/>
```

```csharp
private void btnSubmit_Click(object sender, RoutedEventArgs e)
{
    MessageBox.Show("Knop aangeklikt!");
}
```

### De parameters

Elke eventhandler heeft typisch twee parameters:

- `object sender` — het object dat het event uitstuurt (bv. de knop zelf)  
- `EventArgs e` (of een subtype zoals `RoutedEventArgs`) — extra informatie over het event

Je kunt de `sender` casten naar een specifieke type:

```csharp
Button b = (Button)sender;
b.Content = "Je hebt mij aangeklikt!";
```

Let op: de exacte type van `e` verschilt per event (bijv. `MouseButtonEventArgs`, `SelectionChangedEventArgs`, ...). Zorg dat de signatuur van je handler overeenkomt met wat het event verwacht.

---

## 10. Samenvatting

- **WPF** scheidt interface (XAML) en logica (C#) via de **code-behind**.  
- **Panels** bepalen de layout; gebruik bij voorkeur `Grid`, `StackPanel` of `WrapPanel`.  
- **Properties** zoals `Margin`, `Alignment` en `Name` zijn essentieel voor positionering en interactie.  
- **Events** verbinden gebruikersacties met code.  
- **MessageBox** is een handig hulpmiddel voor meldingen of eenvoudige gebruikersinteractie.
