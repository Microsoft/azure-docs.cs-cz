---
title: Začínáme s rozhraním API Entity propojení | Microsoft Docs
description: Analýza textu a odkaz pomocí rozhraní API propojení Entity v léčby služby s názvem entity k příslušné položky ve znalostní bázi.
services: cognitive-services
author: DavidLiCIG
manager: wkwok
ms.service: cognitive-services
ms.component: entity-linking-intelligence
ms.topic: article
ms.date: 07/06/2016
ms.author: davl
ms.openlocfilehash: 54c4a3bbb3637c248bd7705ed291633368b542c9
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342494"
---
# <a name="get-started-with-entity-linking-api-in-c35"></a>Začínáme s Entity propojení rozhraní API v jazyce C&#35;

Propojování Entity společnosti Microsoft je nástroj přirozeného jazyka zpracování k analýze textu a propojit s názvem entity příslušné položky ve znalostní bázi. 

V tomto kurzu prozkoumá entity propojení pomocí klientské knihovny propojení Entity jako balíčku NuGet. 

### <a name="Prerequisites">Požadavky</a>

- Visual Studio 2015
- Klíč rozhraní API služby Microsoft kognitivní
- Nainstalujte klienta knihovny a příklad
- Balíček NuGet propojení Microsoft Entity

Můžete ho stáhnout Entity propojení Intelligence Service API klientské knihovny prostřednictvím [SDK](https://www.github.com/microsoft/cognitive-entitylinking-windows). Musí být extrahovány do složky podle svého výběru souboru zip staženého, mnoho uživatelů vyberte složku, Visual Studio 2015.

### <a name="step-1-subscribe-entity-linking-intelligence-service-and-get-your-own-key">Krok 1: Přihlášení k odběru do Entity propojení Intelligence služby a získat klíč</a>
Před použitím služby Intelligence propojení Entity, musíte se zaregistrovat pro klíč rozhraní API. V tématu [odběry](https://www.microsoft.com/cognitive-services/en-us/sign-up). Jak primární a sekundární klíč lze použít v tomto kurzu.

### <a name="step-2-create-a-new-project-in-visual-studio"> Krok 2: Vytvořte nový projekt v sadě Visual Studio</a>

Začněme vytvořením nového projektu v sadě Visual Studio. Nejprve spusťte Visual Studio 2015 z nabídky Start. Pak vytvořte nový projekt tak, že vyberete **nainstalován → šablon → Visual C# → prázdnou aplikaci pro Windows Universal →** pro vaše šablona projektu:

 ![Vytvoří univerzální aplikace](./Images/CreateUWP.png)

### <a name="step-3-add-the-entity-linking-nuget-package-to-your-project">Krok 3: Přidání Entity propojení balíčku NuGet do projektu</a>

Propojování entity služby kognitivní Services vydání jako balíček NuGet.org a musí být nainstalovaný, abyste mohli používat.
Chcete-li ji přidat do projektu, přejděte na **Průzkumníku řešení** , klikněte pravým tlačítkem na projekt a vyberte **spravovat balíčky Nuget**.

V první **Správce balíčků NuGet** okně vyberte NuGet.org jako vaše **zdroj balíčku** v pravém horním rohu. Vyberte **Procházet** v levém horním rohu a do vyhledávacího pole zadejte "ProjectOxford.EntityLinking". Vyberte **Microsoft.ProjectOxford.EntityLinking** NuGet balíček a klikněte na tlačítko **nainstalovat**.

Hledat v dalším kroku, Newtonsoft.Json a instalaci. Pokud budete vyzváni ke zkontrolování změn, klikněte na tlačítko **OK**. Pokud se zobrazí s licenčními podmínkami EntityLinking, klikněte na tlačítko **souhlasím**.

Propojování entity je nyní nainstalován jako součást vaší aplikace. Si můžete ověřit kontrolou, která ** Microsoft.ProjectOxford.EntityLinking** odkaz se nachází v rámci projektu v Průzkumníku řešení.

 ![Knihovna nuget zahrnutý v projektu](./Images/NugetLibraryInProject.png)
 
### <a name="step-4-add-an-input-and-output-text-block-to-your-apps-xaml">Krok 4: Přidejte vstup a výstup blok textu do vaší aplikace XAML</a>
Přejděte do ** MainPage.xaml ** v **Průzkumníku**, pak poklikejte na soubor, který se otevře v novém okně. Pro usnadnění práce, můžete dvakrát kliknete na **XAML** tlačítka na **Návrhář** kartě to bude skrýt **vizuálního návrháře** a veškeré místo pro zobrazení kódu rezervovat.

 ![Knihovna nuget zahrnutý v projektu](./Images/UWPMainPage.png)
 
Jako text služby je nejlepší způsob, jak vizualizovat funkce vytváření vstup a bloku text výstupu. Chcete-li to provést, přidejte následující XAML v **mřížky**. Tento kód přidá tři komponenty, vstupní textové pole, výstupního bloku textu a tlačítko start.
 
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
 
### <a name="step-5-proceed-to-add-entity-linking-intelligence-service">Krok 5: Pokračujte přidat službu Intelligence propojení Entity</a>
 
Uživatelské rozhraní je nyní vytvořena. Před použitím služby propojení Entity, musíme obslužná rutina kliknutí na tlačítko Přidat. Otevřete **MainPage.xaml** z **Průzkumníku řešení**. Přidejte obslužnou rutinu button_Click na konci tlačítko.
 
 ```XAML
 <Button x:Name="button" Grid.Row="2" Content="Get Result" Click="button_Click" />
 ```
 
Obslužná rutina kliknutí na tlačítko musí implementovat v kódu. Otevřete **MainPage.xaml.cs** z **Průzkumníku řešení** implementovat kliknutí na tlačítko. EntityLinkingServiceClient je obálku pro načtení propojení Entity odpovědí. Argument konstruktoru EntityLinkingServiceClient je klíč předplatného kognitivní služby. Vložte klíč předplatného jste získali v **kroku 1** volat službu propojení Entity. 

Níže je příklad kód, který přidá do odpovědi "wikipediaId" pomocí služby propojení Entity. 
 
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
 
Nyní jste připraveni ke spuštění vaší první přirozeného jazyka zpracování Entity propojení aplikace. Stiskněte **klávesy F5** pro zkompilování a spuštění aplikace. Vložit fragmenty textu nebo odstavců do vstupního pole. Klikněte na tlačítko "Získat výsledky" a sledovat zjištěné entity v bloku výstup.
 
 ![Ukázka UWP výsledek](./Images/DemoCodeResult.png)
 
### <a name="summary">Souhrn</a>
 
V tomto kurzu jste zjistili, jak vytvořit aplikaci využít Entity propojení Intelligence služby klientské knihovny jenom pár řádků jazyka C# a kód XAML. 

