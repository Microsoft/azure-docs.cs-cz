---
title: 'Kurz: Sestavení aplikace Entity Linking – C#'
titlesuffix: Azure Cognitive Services
description: Pomocí rozhraní Entity Linking API můžete analyzovat text a spojovat pojmenované entity s relevantními položkami ve znalostní bázi.
services: cognitive-services
author: DavidLiCIG
manager: cgronlun
ms.service: cognitive-services
ms.component: entity-linking-intelligence
ms.topic: tutorial
ms.date: 07/06/2016
ms.author: davl
ms.openlocfilehash: 907b4cab483f1bf63a864094530784f9c632a1c8
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/19/2018
ms.locfileid: "46365634"
---
# <a name="tutorial-build-an-entity-linking-app-with-c"></a>Kurz: Sestavení aplikace Entity Linking pomocí C#

Entity Linking od Microsoftu je nástroj pro zpracování přirozeného jazyka určený k analýze textu a spojování pojmenovaných entit s relevantními položkami ve znalostní bázi. 

Tento kurz se věnuje spojování entit za použití klientské knihovny Entity Linking jako balíčku NuGet. 

### <a name="Prerequisites">Požadavky</a>

- Visual Studio 2015
- Klíč rozhraní API služby Microsoft Cognitive Services
- Získání klientské knihovny a příkladu
- Balíček NuGet služby Microsoft Entity Linking

Klientskou knihovnu rozhraní API služby Entity Linking Intelligence Service si můžete stáhnout prostřednictvím sady [SDK](https://www.github.com/microsoft/cognitive-entitylinking-windows). Stažený soubor ZIP je potřeba extrahovat do složky podle vašeho výběru, velká část uživatelů volí složku sady Visual Studio 2015.

### <a name="step-1-subscribe-entity-linking-intelligence-service-and-get-your-own-key">Krok 1: Přihlášení k odběru služby Entity Linking Intelligence Service a získání klíče</a>
Než budete moct službu Entity Linking Intelligence Service používat, musíte se zaregistrovat a získat klíč rozhraní API. Viz [Předplatná](https://www.microsoft.com/cognitive-services/en-us/sign-up). V tomto kurzu můžete použít primární i sekundární klíč.

### <a name="step-2-create-a-new-project-in-visual-studio">Krok 2: Vytvoření nového projektu v sadě Visual Studio</a>

Začněme vytvořením nového projektu v sadě Visual Studio. Nejprve v nabídce Start spusťte sadu Visual Studio 2015. Potom vytvořte nový projekt výběrem položek **Nainstalováno → Šablony → Visual C# → Univerzální aplikace pro Windows → Prázdná aplikace** pro šablonu projektu:

 ![Vytvoření univerzální aplikace](./Images/CreateUWP.png)

### <a name="step-3-add-the-entity-linking-nuget-package-to-your-project">Krok 3: Přidání balíčku NuGet služby Entity Linking do vašeho projektu</a>

Entity Linking služby Cognitive Services se vydává jako balíček NuGet.org a je potřeba ho před použitím nainstalovat.
Pokud ho chcete do projektu přidat, přejděte na kartu **Průzkumník řešení**, klikněte na projekt pravým tlačítkem myši a vyberte **Spravovat balíčky NuGet**.

Nejprve v okně **Správce balíčků NuGet** vyberte v pravém horním rohu NuGet.org jako **Zdroj balíčku**. V levém horním rohu vyberte **Procházet** a do vyhledávacího pole zadejte „ProjectOxford.EntityLinking“. Vyberte balíček NuGet **Microsoft.ProjectOxford.EntityLinking** a klikněte na **Nainstalovat**.

Potom vyhledejte a nainstalujte Newtonsoft.Json. Pokud budete vyzváni ke kontrole změn, klikněte na **OK**. Pokud se vám zobrazí licenční podmínky pro Entity Linking, klikněte na **Přijmout**.

Nástroj Entity Linking je teď nainstalován jako součást vaší aplikace. Můžete to ověřit tak, že zkontrolujete, jestli je odkaz Microsoft.ProjectOxford.EntityLinking součástí vašeho projektu v Průzkumníku řešení.

 ![Zahrnutá knihovna NuGet v projektu](./Images/NugetLibraryInProject.png)
 
### <a name="step-4-add-an-input-and-output-text-block-to-your-apps-xaml">Krok 4: Přidání vstupního a výstupního textového bloku do XAML vaší aplikace</a>
V **Průzkumníku řešení** přejděte na MainPage.xaml a potom na soubor poklikejte. Tím ho otevřete v novém okně. Abyste si práci usnadnili, můžete poklikat na tlačítko **XAML** na kartě **Návrhář**. Skryjete tak **vizuálního návrháře** a vyhradíte celý prostor pro zobrazení kódu.

 ![Zahrnutá knihovna NuGet v projektu](./Images/UWPMainPage.png)
 
Vzhledem k tomu, že se jedná o textovou službu, je nejlepším způsobem pro vizualizaci funkce vytvoření vstupního a výstupního textového bloku. Uděláte to tak, že do **mřížky** přidáte následující kód XAML. Tento kód přidá tři komponenty, vstupní textové pole, výstupní textový blok a tlačítko pro spuštění.
 
 ```XAML
 <Grid Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">
    <Grid.RowDefinitions>
        <RowDefinition Height="*"/>
        <RowDefinition Height="*" />
        <RowDefinition Height="50" />
    </Grid.RowDefinitions>
    <TextBox x:Name="inputBox" Grid.Row="0" TextWrapping="Wrap" Text="Enter a paragraph" Margin="10" AcceptsReturn="True" />
    <TextBlock x:Name="outputBlock" Grid.Row="1" TextWrapping="Wrap" Text="Result will be here" Margin="10" />
    <Button x:Name="button" Grid.Row="2" Content="Get Result" />
</Grid>
 ```
 
### <a name="step-5-proceed-to-add-entity-linking-intelligence-service">Krok 5: Přidání služby Entity Linking Intelligence Service</a>
 
Uživatelské rozhraní je teď vytvořené. Než budeme moct použít službu Entity Linking, musíte přidat obslužnou rutinu kliknutí na tlačítko. Otevřete soubor **MainPage.xaml** v **Průzkumníku řešení**. Obslužnou rutinu kliknutí na tlačítko přidejte na konec tlačítka.
 
 ```XAML
 <Button x:Name="button" Grid.Row="2" Content="Get Result" Click="button_Click" />
 ```
 
Obslužnou rutinu kliknutí na tlačítko je potřeba implementovat do kódu. Otevřete soubor **MainPage.xaml.cs** v **Průzkumníku řešení** a implementujte kliknutí na tlačítko. EntityLinkingServiceClient představuje obálku pro načtení odpovědí služby Entity Linking. Argument konstruktoru pro EntityLinkingServiceClient je klíč předplatného služby Cognitive Services. Vložte klíč předplatného, který jste získali v **kroku 1**, a použijte ho k volání služby Entity Linking. 

Níže je příklad kódu, který do odpovědi přidá „wikipediaId“ pomocí služby Entity Linking Service. 
 
 ```csharp
 private async void button_Click(object sender, RoutedEventArgs e)
{
    var text = this.inputBox.Text;
    var client = new EntityLinkingServiceClient("Your subscription key","https://api.labs.cognitive.microsoft.com");
    var linkResponse = await client.LinkAsync(text);
    var result = string.Join(", ", linkResponse.Select(i => i.WikipediaID).ToList());
    this.outputBlock.Text = result;
}
 ```
 
Nyní jste připraveni ke spuštění vaší první aplikace Entity Linking pro zpracování přirozeného jazyka. Stisknutím **klávesy F5** kompilujte a spusťte aplikaci. Do vstupního pole vložte fragmenty textu nebo odstavce. Stiskněte tlačítko Get Result (Získat výsledek) a sledujte identifikované entity ve výstupním bloku.
 
 ![Výsledek pro ukázku UWP](./Images/DemoCodeResult.png)
 
### <a name="summary">Souhrn</a>
 
V tomto kurzu jste se naučili vytvořit aplikaci využívají klientskou knihovnu služby Entity Linking Intelligence Service pomocí několika řádků jazyka C# a kódu XAML. 

