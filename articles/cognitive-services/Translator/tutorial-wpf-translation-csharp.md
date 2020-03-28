---
title: 'Kurz: Vytvoření překladové aplikace s WPF, C# - Překladač Text API'
titleSuffix: Azure Cognitive Services
description: V tomto kurzu vytvoříte aplikaci WPF k provedení překladu textu, detekce jazyka a kontroly pravopisu pomocí jediného klíče předplatného.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: tutorial
ms.date: 02/10/2020
ms.author: swmachan
ms.openlocfilehash: ecb42d200eb8808f6bfa4cfb91e98909e350038b
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77118616"
---
# <a name="tutorial-create-a-translation-app-with-wpf"></a>Kurz: Vytvoření překladové aplikace s WPF

V tomto kurzu vytvoříte [aplikaci WPF (Windows Presentation Foundation),](https://docs.microsoft.com/visualstudio/designers/getting-started-with-wpf?view=vs-2019) která používá služby Azure Cognitive Services pro překlad textu, rozpoznávání jazyka a kontrolu pravopisu pomocí jediného klíče předplatného. Konkrétně vaše aplikace bude volat API z Překladač text a [Kontrola pravopisu Bing](https://azure.microsoft.com/services/cognitive-services/spell-check/).

Co je WPF (Windows Presentation Foundation)? Je to rozhraní ui, které vytváří desktopové klientské aplikace. Vývojová platforma WPF podporuje širokou sadu funkcí pro vývoj aplikací, včetně modelu aplikace, prostředků, ovládacích prvků, grafiky, rozložení, datové vazby, dokumentů a zabezpečení. Je to podmnožina rozhraní .NET Framework, takže pokud jste dříve vytvořili aplikace s rozhraním .NET Framework pomocí ASP.NET nebo Windows Forms, programovací prostředí by mělo být známé. WPF používá extensible app Markup Language (XAML) poskytnout deklarativní model pro programování aplikací, které budeme kontrolovat v následujících částech.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření projektu WPF v sadě Visual Studio
> * Přidání sestavení a balíčků NuGet do projektu
> * Vytvoření uživatelského uživatelského uživatelského nastavení aplikace pomocí XAML
> * Použití rozhraní Translator Text API pro získání jazyků, překlad textu a detekci zdrojového jazyka
> * Použití rozhraní API pro kontrolu pravopisu Bingu k ověření vašeho vstupu a zlepšení přesnosti překladu
> * Spuštění aplikace WPF

### <a name="cognitive-services-used-in-this-tutorial"></a>Služby Cognitive Services použité v tomto kurzu

Tento seznam obsahuje služby Cognitive Services použité v tomto kurzu. Chcete-li procházet odkaz na rozhraní API pro jednotlivé funkce, postupujte podle odkazu.

| Služba | Funkce | Popis |
|---------|---------|-------------|
| Translator Text | [Získat jazyky](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-languages) | Načtěte úplný seznam podporovaných jazyků pro překlad textu. |
| Translator Text | [Překlad](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate) | Přeložte text do více než 60 jazyků. |
| Translator Text | [Zjistit](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-detect) | Detekujte jazyk vstupního textu. Zahrnuje skóre spolehlivosti pro detekci. |
| Kontrola pravopisu Bingu | [Kontrola pravopisu](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v7-reference) | Opravte pravopisné chyby, abyste zlepšili přesnost překladu. |

## <a name="prerequisites"></a>Požadavky

Než budeme pokračovat, budete potřebovat následující:

* Předplatné Azure Cognitive Services. [Získejte klíč služeb Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#create-a-new-azure-cognitive-services-resource).
* Počítač se systémem Windows
* [Visual Studio 2019](https://www.visualstudio.com/downloads/) – komunita nebo podnik

> [!NOTE]
> Doporučujeme vytvořit předplatné v oblasti Západní USA pro tento kurz. V opačném případě budete muset změnit koncové body a oblasti v kódu při práci prostřednictvím tohoto cvičení.  

## <a name="create-a-wpf-app-in-visual-studio"></a>Vytvoření aplikace WPF v Sadě Visual Studio

První věc, kterou musíme udělat, je nastavit náš projekt v sadě Visual Studio.

1. Otevřete sadu Visual Studio. Vyberte **možnost Vytvořit nový projekt**.
1. V **okně Vytvořit nový projekt**vyhledejte a vyberte **wpf aplikaci (.NET Framework).** Můžete vybrat C# z **jazyka** zúžit možnosti.
1. Vyberte **další**a pojmenujte projekt `MSTranslatorTextDemo`.
1. Nastavte verzi architektury na **rozhraní .NET Framework 4.7.2** nebo novější a vyberte **vytvořit**.
   ![Zadejte název a verzi architektury v sadě Visual Studio.](media/name-wpf-project-visual-studio.png)

Projekt byl vytvořen. Všimněte si, že jsou otevřeny `MainWindow.xaml` `MainWindow.xaml.cs`dvě karty: a . V průběhu tohoto kurzu budeme přidávat kód do těchto dvou souborů. Upravíme `MainWindow.xaml` uživatelské rozhraní aplikace. Upravíme `MainWindow.xaml.cs` pro naše volání na Překladač text a Kontrola pravopisu Bing.
   ![Kontrola prostředí](media/blank-wpf-project.png)

V další části přidáme sestavení a balíček NuGet do našeho projektu pro další funkce, jako je analýza JSON.

## <a name="add-references-and-nuget-packages-to-your-project"></a>Přidání odkazů a balíčků NuGet do projektu

Náš projekt vyžaduje několik sestavení rozhraní .NET Framework a NewtonSoft.Json, které nainstalujeme pomocí správce balíčků NuGet.

### <a name="add-net-framework-assemblies"></a>Přidání sestavení rozhraní .NET Framework

Přidáme sestavení do našeho projektu serializovat a rekonstruovat objekty a spravovat požadavky http a odpovědi.

1. Vyhledejte projekt v Průzkumníku řešení sady Visual Studio. Klikněte pravým tlačítkem myši na projekt a potom vyberte **Přidat odkaz >**, ve kterém se otevře Správce **odkazů**.
1. Na kartě **Sestavení** jsou uvedena všechna sestavení rozhraní .NET Framework, na která je k dispozici odkaz. Pomocí vyhledávacího panelu v pravém horním horním horním panelu vyhledejte odkazy.
   ![Přidání odkazů na sestavení](media/add-assemblies-2019.png)
1. Vyberte následující odkazy pro váš projekt:
   * [System.Runtime.Serialization](https://docs.microsoft.com/dotnet/api/system.runtime.serialization)
   * [System.Web](https://docs.microsoft.com/dotnet/api/system.web)
   * System.Web.Extensions
   * [System.windows](https://docs.microsoft.com/dotnet/api/system.windows)
1. Po přidání těchto odkazů do projektu můžete klepnutím na **tlačítko OK** zavřít **Správce odkazů**.

> [!NOTE]
> Pokud se chcete dozvědět více o odkazech na sestavení, [přečtěte si informace o tématu Jak: Přidání nebo odebrání odkazu pomocí Správce odkazů](https://docs.microsoft.com/visualstudio/ide/how-to-add-or-remove-references-by-using-the-reference-manager?view=vs-2019).

### <a name="install-newtonsoftjson"></a>Instalace NewtonSoft.Json

Naše aplikace bude používat NewtonSoft.Json k rekonstrukci objektů JSON. Podle těchto pokynů nainstalujte balíček.

1. Vyhledejte projekt v Průzkumníku řešení sady Visual Studio a klikněte na něj pravým tlačítkem myši. Vyberte **spravovat nugetové balíčky**.
1. Vyhledejte a vyberte kartu **Procházet.**
1. Zadejte [NewtonSoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/) do vyhledávacího panelu.

    ![Vyhledejte a nainstalujte NewtonSoft.Json](media/nuget-package-manager.png)

1. Vyberte balíček a klepněte na tlačítko **Instalovat**.
1. Po dokončení instalace zavřete kartu.

## <a name="create-a-wpf-form-using-xaml"></a>Vytvoření formuláře WPF pomocí protokolu XAML

Chcete-li aplikaci používat, budete potřebovat uživatelské rozhraní. Pomocí XAML vytvoříme formulář, který uživatelům umožní vybrat jazyky zadávání a překladu, zadávat text k překladu a zobrazí výstup překladu.

Podívejme se, co stavíme.

![Uživatelské rozhraní WPF XAML](media/translator-text-csharp-xaml.png)

Uživatelské rozhraní obsahuje tyto součásti:

| Name (Název) | Typ | Popis |
|------|------|-------------|
| `FromLanguageComboBox` | ComboBox | Zobrazí seznam jazyků podporovaných aplikací Microsoft Translator pro překlad textu. Uživatel vybere jazyk, ze kterého překládá. |
| `ToLanguageComboBox` | ComboBox | Zobrazí stejný seznam jazyků `FromComboBox`jako , ale slouží k výběru jazyka, do který uživatel překládá. |
| `TextToTranslate` | TextBox | Umožňuje uživateli zadat text, který má být přeložen. |
| `TranslateButton` | Tlačítko | Toto tlačítko slouží k překladu textu. |
| `TranslatedTextLabel` | Popisek | Zobrazí překlad. |
| `DetectedLanguageLabel` | Popisek | Zobrazí zjištěný jazyk textu, který má`TextToTranslate`být přeložen ( ). |

> [!NOTE]
> Tento formulář vytváříme pomocí zdrojového kódu XAML, ale formulář můžete vytvořit pomocí editoru v sadě Visual Studio.

Přidáme kód do našeho projektu.

1. V sadě Visual Studio `MainWindow.xaml`vyberte kartu pro aplikaci .
1. Zkopírujte tento kód do projektu a pak vyberte **soubor > Uložit MainWindow.xaml** pro uložení změn.
   ```xaml
   <Window x:Class="MSTranslatorTextDemo.MainWindow"
           xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
           xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
           xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
           xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
           xmlns:local="clr-namespace:MSTranslatorTextDemo"
           mc:Ignorable="d"
           Title="Microsoft Translator" Height="400" Width="700" BorderThickness="0">
       <Grid>
           <Label x:Name="label" Content="Microsoft Translator" HorizontalAlignment="Left" Margin="39,6,0,0" VerticalAlignment="Top" Height="49" FontSize="26.667"/>
           <TextBox x:Name="TextToTranslate" HorizontalAlignment="Left" Height="23" Margin="42,160,0,0" TextWrapping="Wrap" VerticalAlignment="Top" Width="600" FontSize="14" TabIndex="3"/>
           <Label x:Name="EnterTextLabel" Content="Text to translate:" HorizontalAlignment="Left" Margin="40,129,0,0" VerticalAlignment="Top" FontSize="14"/>
           <Label x:Name="toLabel" Content="Translate to:" HorizontalAlignment="Left" Margin="304,58,0,0" VerticalAlignment="Top" FontSize="14"/>

           <Button x:Name="TranslateButton" Content="Translate" HorizontalAlignment="Left" Margin="39,206,0,0" VerticalAlignment="Top" Width="114" Height="31" Click="TranslateButton_Click" FontSize="14" TabIndex="4" IsDefault="True"/>
           <ComboBox x:Name="ToLanguageComboBox"
                   HorizontalAlignment="Left"
                   Margin="306,88,0,0"
                   VerticalAlignment="Top"
                   Width="175" FontSize="14" TabIndex="2">

           </ComboBox>
           <Label x:Name="fromLabel" Content="Translate from:" HorizontalAlignment="Left" Margin="40,58,0,0" VerticalAlignment="Top" FontSize="14"/>
           <ComboBox x:Name="FromLanguageComboBox"
               HorizontalAlignment="Left"
               Margin="42,88,0,0"
               VerticalAlignment="Top"
               Width="175" FontSize="14" TabIndex="1"/>
           <Label x:Name="TranslatedTextLabel" Content="Translation is displayed here." HorizontalAlignment="Left" Margin="39,255,0,0" VerticalAlignment="Top" Width="620" FontSize="14" Height="85" BorderThickness="0"/>
           <Label x:Name="DetectedLanguageLabel" Content="Autodetected language is displayed here." HorizontalAlignment="Left" Margin="39,288,0,0" VerticalAlignment="Top" Width="620" FontSize="14" Height="84" BorderThickness="0"/>
       </Grid>
   </Window>
   ```
Nyní byste měli vidět náhled uživatelského rozhraní aplikace v sadě Visual Studio. Mělo by to vypadat podobně jako na obrázku výše.

To je ono, vaše forma je připravena. Nyní napíšeme nějaký kód pro použití překladu textu a kontroly pravopisu Bingu.

> [!NOTE]
> Nebojte se vyladit tento formulář, nebo vytvořit svůj vlastní.

## <a name="create-your-app"></a>Vytvoření aplikace

`MainWindow.xaml.cs`obsahuje kód, který řídí naši aplikaci. V několika dalších částech přidáme kód k naplnění našich rozevíracích nabídek a zavoláme hrstku rozhraní API vystavených překladačem textu a kontrolou pravopisu Bingu.

* Při spuštění programu `MainWindow` a je vytvořena `Languages` instance, metoda Překladač text rozhraní API je volána k načtení a naplnění našeho výběru jazyka rozevírací seznamy. To se děje jednou na začátku každé relace.
* Po **klepnutí** na tlačítko Přeložit se načte výběr jazyka uživatele a text, na vstupu se provede kontrola pravopisu a uživateli se zobrazí překlad a zjištěný jazyk.
  * Metoda `Translate` překladač text api je volána přeložit text z `TextToTranslate`. Toto volání `to` také `from` zahrnuje jazyky a vybrané pomocí rozevíracích nabídek.
  * Metoda `Detect` překladač text api je volána `TextToTranslate`k určení jazyka textu .
  * Kontrola pravopisu bingu `TextToTranslate` se používá k ověření a úpravě překlepů.

Celý náš projekt je zapouzdřen ve `MainWindow : Window` třídě. Začněme přidáním kódu pro nastavení klíče předplatného, deklarování koncových bodů pro text překladače a kontrolu pravopisu Bingu a inicializaci aplikace.

1. V sadě Visual Studio `MainWindow.xaml.cs`vyberte kartu pro aplikaci .
1. Nahraďte předem `using` vyplněné příkazy následujícím.  
   ```csharp
   using System;
   using System.Windows;
   using System.Net;
   using System.Net.Http;
   using System.IO;
   using System.Collections.Generic;
   using System.Linq;
   using System.Text;
   using Newtonsoft.Json;
   ```
1. Vyhledejte `MainWindow : Window` třídu a nahraďte ji tímto kódem:
   ```csharp
   {
       // This sample uses the Cognitive Services subscription key for all services. To learn more about
       // authentication options, see: https://docs.microsoft.com/azure/cognitive-services/authentication.
       const string COGNITIVE_SERVICES_KEY = "YOUR_COG_SERVICES_KEY";
       // Endpoints for Translator Text and Bing Spell Check
       public static readonly string TEXT_TRANSLATION_API_ENDPOINT = "https://api.cognitive.microsofttranslator.com/{0}?api-version=3.0";
       const string BING_SPELL_CHECK_API_ENDPOINT = "https://westus.api.cognitive.microsoft.com/bing/v7.0/spellcheck/";
       // An array of language codes
       private string[] languageCodes;

       // Dictionary to map language codes from friendly name (sorted case-insensitively on language name)
       private SortedDictionary<string, string> languageCodesAndTitles =
           new SortedDictionary<string, string>(Comparer<string>.Create((a, b) => string.Compare(a, b, true)));

       // Global exception handler to display error message and exit
       private static void HandleExceptions(object sender, UnhandledExceptionEventArgs args)
       {
           Exception e = (Exception)args.ExceptionObject;
           MessageBox.Show("Caught " + e.Message, "Error", MessageBoxButton.OK, MessageBoxImage.Error);
           System.Windows.Application.Current.Shutdown();
       }
       // MainWindow constructor
       public MainWindow()
       {
           // Display a message if unexpected error is encountered
           AppDomain.CurrentDomain.UnhandledException += new UnhandledExceptionEventHandler(HandleExceptions);

           if (COGNITIVE_SERVICES_KEY.Length != 32)
           {
               MessageBox.Show("One or more invalid API subscription keys.\n\n" +
                   "Put your keys in the *_API_SUBSCRIPTION_KEY variables in MainWindow.xaml.cs.",
                   "Invalid Subscription Key(s)", MessageBoxButton.OK, MessageBoxImage.Error);
               System.Windows.Application.Current.Shutdown();
           }
           else
           {
               // Start GUI
               InitializeComponent();
               // Get languages for drop-downs
               GetLanguagesForTranslate();
               // Populate drop-downs with values from GetLanguagesForTranslate
               PopulateLanguageMenus();
           }
       }
   // NOTE:
   // In the following sections, we'll add code below this.
   }
   ```
1. Přidejte klíč předplatného služby Cognitive Services a uložte ho.

V tomto bloku kódu jsme deklarovali dvě členské proměnné, které obsahují informace o dostupných jazycích pro překlad:

| Proměnná | Typ | Popis |
|----------|------|-------------|
|`languageCodes` | Pole řetězců |Uchovává kódy jazyků. Služba Translator používá k identifikaci jazyků krátké kódy, například `en` pro angličtinu. |
|`languageCodesAndTitles` | Seřazený slovník | Mapuje popisné názvy z uživatelského rozhraní na krátké kódy používané v rozhraní API. Používá abecední řazení, velká a malá písmena se nerozlišují. |

Potom v `MainWindow` rámci konstruktoru jsme přidali `HandleExceptions`zpracování chyb s . Toto zpracování chyb zajišťuje, že je k dispozici výstraha, pokud není zpracována výjimka. Poté je spuštěna kontrola pro potvrzení, že zadaný klíč předplatného má délku 32 znaků. Pokud je klíč menší než/větší než 32 znaků, je vyvolána chyba.

Pokud existují klíče, které mají alespoň `InitializeComponent()` správnou délku, volání získá uživatelské rozhraní válcování vyhledáním, načtením a vytvoření matný popis okna hlavní aplikace.

Nakonec jsme přidali kód pro volání metod pro načtení jazyků pro překlad a naplnění rozevíracích nabídek pro uživatelské rozhraní naší aplikace. Nebojte se, brzy se dostaneme ke kódu za těmi toto hovory.

## <a name="get-supported-languages"></a>Zjištění podporovaných jazyků

Překladač text api v současné době podporuje více než 60 jazyků. Vzhledem k tomu, že v průběhu času bude přidána nová jazyková podpora, doporučujeme volat prostředek Jazyky vystavený překladačem textu, nikoli pevný mač jazyk ve vaší aplikaci.

V této části vytvoříme `GET` požadavek na prostředek Jazyky a určíme, že chceme, aby byl k dispozici seznam jazyků k překladu.

> [!NOTE]
> Prostředek Jazyky umožňuje filtrovat jazykovou podporu s následujícími parametry dotazu: přepis, slovník a překlad. Další informace naleznete v [tématu odkaz na rozhraní API](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-languages).

Než budeme pokračovat, podívejme se na ukázkový výstup pro volání zdroje Jazyky:

```json
{
  "translation": {
    "af": {
      "name": "Afrikaans",
      "nativeName": "Afrikaans",
      "dir": "ltr"
    },
    "ar": {
      "name": "Arabic",
      "nativeName": "العربية",
      "dir": "rtl"
    }
    // Additional languages are provided in the full JSON output.
}
```

Z tohoto výstupu můžeme extrahovat `name` kód jazyka a konkrétního jazyka. Naše aplikace používá NewtonSoft.Json k rekonstrukci objektu[`JsonConvert.DeserializeObject`](https://www.newtonsoft.com/json/help/html/M_Newtonsoft_Json_JsonConvert_DeserializeObject__1.htm)JSON ( ).

Když v poslední části zjistíme, kde jsme skončili, přidáme do naší aplikace metodu, jak do naší aplikace získat podporované jazyky.

1. V sadě Visual Studio `MainWindow.xaml.cs`otevřete kartu pro .
2. Přidejte tento kód do projektu:
   ```csharp
   // ***** GET TRANSLATABLE LANGUAGE CODES
   private void GetLanguagesForTranslate()
   {
       // Send request to get supported language codes
       string uri = String.Format(TEXT_TRANSLATION_API_ENDPOINT, "languages") + "&scope=translation";
       WebRequest WebRequest = WebRequest.Create(uri);
       WebRequest.Headers.Add("Accept-Language", "en");
       WebResponse response = null;
       // Read and parse the JSON response
       response = WebRequest.GetResponse();
       using (var reader = new StreamReader(response.GetResponseStream(), UnicodeEncoding.UTF8))
       {
           var result = JsonConvert.DeserializeObject<Dictionary<string, Dictionary<string, Dictionary<string, string>>>>(reader.ReadToEnd());
           var languages = result["translation"];

           languageCodes = languages.Keys.ToArray();
           foreach (var kv in languages)
           {
               languageCodesAndTitles.Add(kv.Value["name"], kv.Key);
           }
       }
   }
   // NOTE:
   // In the following sections, we'll add code below this.
   ```

Metoda `GetLanguagesForTranslate()` vytvoří požadavek HTTP GET `scope=translation` a použije parametr řetězce dotazu k omezení rozsahu požadavku na podporované jazyky pro překlad. Přidá se hlavička `Accept-Language` s hodnotou `en`, aby se podporované jazyky vrátily v angličtině.

Odpověď JSON je analyzována a převedena na slovník. Poté jsou kódy jazyka `languageCodes` přidány do členské proměnné. Páry klíč-hodnota, které obsahují kódy jazyků a popisné názvy jazyků, se zkompletují a přidají do členské proměnné `languageCodesAndTitles`. Rozevírací nabídky ve formuláři zobrazují popisné názvy, ale kódy jsou potřebné k vyžádání překladu.

## <a name="populate-language-drop-down-menus"></a>Naplnit rozevírací nabídky jazyka

Uživatelské rozhraní je definováno pomocí XAML, takže nemusíte dělat mnoho pro `InitializeComponent()`jeho nastavení kromě volání . Jedna věc, kterou musíte udělat, je přidat popisné názvy jazyků do **překladu z** a **přeložit do** rozevíracích nabídek. Metoda `PopulateLanguageMenus()` přidá názvy.

1. V sadě Visual Studio `MainWindow.xaml.cs`otevřete kartu pro .
2. Přidejte tento kód do `GetLanguagesForTranslate()` projektu pod metodou:
   ```csharp
   private void PopulateLanguageMenus()
   {
       // Add option to automatically detect the source language
       FromLanguageComboBox.Items.Add("Detect");

       int count = languageCodesAndTitles.Count;
       foreach (string menuItem in languageCodesAndTitles.Keys)
       {
           FromLanguageComboBox.Items.Add(menuItem);
           ToLanguageComboBox.Items.Add(menuItem);
       }

       // Set default languages
       FromLanguageComboBox.SelectedItem = "Detect";
       ToLanguageComboBox.SelectedItem = "English";
   }
   // NOTE:
   // In the following sections, we'll add code below this.
   ```

Tato metoda itrerates `languageCodesAndTitles` přes slovník a přidá každou klávesu do obou nabídek. Po naplnění nabídek jsou výchozí jazyky z a do nastaveny na **rozpoznat** a **angličtina** v uvedeném pořadí.

> [!TIP]
> Pokud by se nevybraly výchozí hodnoty pro nabídky, mohlo by se stát, že uživatel klikne na **Translate** (Přeložit), aniž by napřed zvolil zdrojový a cílový jazyk. Výchozí hodnoty eliminují nutnost zabývat se tímto problémem.

Nyní, `MainWindow` když byla inicializována a uživatelské rozhraní vytvořeno, tento kód nebude spuštěn, dokud kliknete na tlačítko **Přeložit.**

## <a name="detect-language-of-source-text"></a>Zjišťování jazyka zdrojového textu

Nyní vytvoříme metodu pro detekci jazyka zdrojového textu (text zadaný do naší textové oblasti) pomocí rozhraní Translator Text API. Hodnota vrácená tímto požadavkem bude použita v naší žádosti o překlad později.

1. V sadě Visual Studio `MainWindow.xaml.cs`otevřete kartu pro .
2. Přidejte tento kód do `PopulateLanguageMenus()` projektu pod metodou:
   ```csharp
   // ***** DETECT LANGUAGE OF TEXT TO BE TRANSLATED
   private string DetectLanguage(string text)
   {
       string detectUri = string.Format(TEXT_TRANSLATION_API_ENDPOINT ,"detect");

       // Create request to Detect languages with Translator Text
       HttpWebRequest detectLanguageWebRequest = (HttpWebRequest)WebRequest.Create(detectUri);
       detectLanguageWebRequest.Headers.Add("Ocp-Apim-Subscription-Key", COGNITIVE_SERVICES_KEY);
       detectLanguageWebRequest.Headers.Add("Ocp-Apim-Subscription-Region", "westus");
       detectLanguageWebRequest.ContentType = "application/json; charset=utf-8";
       detectLanguageWebRequest.Method = "POST";

       // Send request
       var serializer = new System.Web.Script.Serialization.JavaScriptSerializer();
       string jsonText = serializer.Serialize(text);

       string body = "[{ \"Text\": " + jsonText + " }]";
       byte[] data = Encoding.UTF8.GetBytes(body);

       detectLanguageWebRequest.ContentLength = data.Length;

       using (var requestStream = detectLanguageWebRequest.GetRequestStream())
           requestStream.Write(data, 0, data.Length);

       HttpWebResponse response = (HttpWebResponse)detectLanguageWebRequest.GetResponse();

       // Read and parse JSON response
       var responseStream = response.GetResponseStream();
       var jsonString = new StreamReader(responseStream, Encoding.GetEncoding("utf-8")).ReadToEnd();
       dynamic jsonResponse = serializer.DeserializeObject(jsonString);

       // Fish out the detected language code
       var languageInfo = jsonResponse[0];
       if (languageInfo["score"] > (decimal)0.5)
       {
           DetectedLanguageLabel.Content = languageInfo["language"];
           return languageInfo["language"];
       }
       else
           return "Unable to confidently detect input language.";
   }
   // NOTE:
   // In the following sections, we'll add code below this.
   ```

Tato metoda vytvoří `POST` požadavek HTTP pro prostředek Detect. Trvá jeden argument `text`, , který je předán spolu jako tělo požadavku. Později, když vytvoříme náš požadavek na překlad, text zadaný do našeho ui bude předán této metodě pro detekci jazyka.

Kromě toho tato metoda vyhodnotí skóre spolehlivosti odpovědi. Pokud je skóre `0.5`větší než , zobrazí se v našem uživatelském rozhraní zjištěný jazyk.

## <a name="spell-check-the-source-text"></a>Kontrola pravopisu zdrojového textu

Nyní vytvoříme metodu kontroly pravopisu našeho zdrojového textu pomocí rozhraní API pro kontrolu pravopisu Bingu. Kontrola pravopisu zajišťuje, že získáme zpět přesné překlady z translatorového textového rozhraní API. Veškeré opravy zdrojového textu jsou předány v naší žádosti o překlad po kliknutí na tlačítko **Přeložit.**

1. V sadě Visual Studio `MainWindow.xaml.cs`otevřete kartu pro .
2. Přidejte tento kód do `DetectLanguage()` projektu pod metodou:

```csharp
// ***** CORRECT SPELLING OF TEXT TO BE TRANSLATED
private string CorrectSpelling(string text)
{
    string uri = BING_SPELL_CHECK_API_ENDPOINT + "?mode=spell&mkt=en-US";

    // Create a request to Bing Spell Check API
    HttpWebRequest spellCheckWebRequest = (HttpWebRequest)WebRequest.Create(uri);
    spellCheckWebRequest.Headers.Add("Ocp-Apim-Subscription-Key", COGNITIVE_SERVICES_KEY);
    spellCheckWebRequest.Method = "POST";
    spellCheckWebRequest.ContentType = "application/x-www-form-urlencoded"; // doesn't work without this

    // Create and send the request
    string body = "text=" + System.Web.HttpUtility.UrlEncode(text);
    byte[] data = Encoding.UTF8.GetBytes(body);
    spellCheckWebRequest.ContentLength = data.Length;
    using (var requestStream = spellCheckWebRequest.GetRequestStream())
        requestStream.Write(data, 0, data.Length);
    HttpWebResponse response = (HttpWebResponse)spellCheckWebRequest.GetResponse();

    // Read and parse the JSON response; get spelling corrections
    var serializer = new System.Web.Script.Serialization.JavaScriptSerializer();
    var responseStream = response.GetResponseStream();
    var jsonString = new StreamReader(responseStream, Encoding.GetEncoding("utf-8")).ReadToEnd();
    dynamic jsonResponse = serializer.DeserializeObject(jsonString);
    var flaggedTokens = jsonResponse["flaggedTokens"];

    // Construct sorted dictionary of corrections in reverse order (right to left)
    // This ensures that changes don't impact later indexes
    var corrections = new SortedDictionary<int, string[]>(Comparer<int>.Create((a, b) => b.CompareTo(a)));
    for (int i = 0; i < flaggedTokens.Length; i++)
    {
        var correction = flaggedTokens[i];
        var suggestion = correction["suggestions"][0];  // Consider only first suggestion
        if (suggestion["score"] > (decimal)0.7)         // Take it only if highly confident
            corrections[(int)correction["offset"]] = new string[]   // dict key   = offset
                { correction["token"], suggestion["suggestion"] };  // dict value = {error, correction}
    }

    // Apply spelling corrections, in order, from right to left
    foreach (int i in corrections.Keys)
    {
        var oldtext = corrections[i][0];
        var newtext = corrections[i][1];

        // Apply capitalization from original text to correction - all caps or initial caps
        if (text.Substring(i, oldtext.Length).All(char.IsUpper)) newtext = newtext.ToUpper();
        else if (char.IsUpper(text[i])) newtext = newtext[0].ToString().ToUpper() + newtext.Substring(1);

        text = text.Substring(0, i) + newtext + text.Substring(i + oldtext.Length);
    }
    return text;
}
// NOTE:
// In the following sections, we'll add code below this.
```

## <a name="translate-text-on-click"></a>Přeložit text na kliknutí

Poslední věc, kterou musíme udělat, je vytvořit metodu, která je vyvolána po kliknutí na tlačítko **Přeložit** v našem uživatelském rozhraní.

1. V sadě Visual Studio `MainWindow.xaml.cs`otevřete kartu pro .
1. Přidejte tento kód do `CorrectSpelling()` projektu pod metodu a uložte:  
   ```csharp
   // ***** PERFORM TRANSLATION ON BUTTON CLICK
   private async void TranslateButton_Click(object sender, EventArgs e)
   {
       string textToTranslate = TextToTranslate.Text.Trim();

       string fromLanguage = FromLanguageComboBox.SelectedValue.ToString();
       string fromLanguageCode;

       // auto-detect source language if requested
       if (fromLanguage == "Detect")
       {
           fromLanguageCode = DetectLanguage(textToTranslate);
           if (!languageCodes.Contains(fromLanguageCode))
           {
               MessageBox.Show("The source language could not be detected automatically " +
                   "or is not supported for translation.", "Language detection failed",
                   MessageBoxButton.OK, MessageBoxImage.Error);
               return;
           }
       }
       else
           fromLanguageCode = languageCodesAndTitles[fromLanguage];

       string toLanguageCode = languageCodesAndTitles[ToLanguageComboBox.SelectedValue.ToString()];

       // spell-check the source text if the source language is English
       if (fromLanguageCode == "en")
       {
           if (textToTranslate.StartsWith("-"))    // don't spell check in this case
               textToTranslate = textToTranslate.Substring(1);
           else
           {
               textToTranslate = CorrectSpelling(textToTranslate);
               TextToTranslate.Text = textToTranslate;     // put corrected text into input field
           }
       }
       // handle null operations: no text or same source/target languages
       if (textToTranslate == "" || fromLanguageCode == toLanguageCode)
       {
           TranslatedTextLabel.Content = textToTranslate;
           return;
       }

       // send HTTP request to perform the translation
       string endpoint = string.Format(TEXT_TRANSLATION_API_ENDPOINT, "translate");
       string uri = string.Format(endpoint + "&from={0}&to={1}", fromLanguageCode, toLanguageCode);

       System.Object[] body = new System.Object[] { new { Text = textToTranslate } };
       var requestBody = JsonConvert.SerializeObject(body);

       using (var client = new HttpClient())
       using (var request = new HttpRequestMessage())
       {
           request.Method = HttpMethod.Post;
           request.RequestUri = new Uri(uri);
           request.Content = new StringContent(requestBody, Encoding.UTF8, "application/json");
           request.Headers.Add("Ocp-Apim-Subscription-Key", COGNITIVE_SERVICES_KEY);
           request.Headers.Add("Ocp-Apim-Subscription-Region", "westus");
           request.Headers.Add("X-ClientTraceId", Guid.NewGuid().ToString());

           var response = await client.SendAsync(request);
           var responseBody = await response.Content.ReadAsStringAsync();

           var result = JsonConvert.DeserializeObject<List<Dictionary<string, List<Dictionary<string, string>>>>>(responseBody);
           var translation = result[0]["translations"][0]["text"];

           // Update the translation field
           TranslatedTextLabel.Content = translation;
       }
   }
   ```

Prvním krokem je získat jazyky "z" a "do" a text, který uživatel zadal do našeho formuláře. Pokud je zdrojový jazyk **Detect**nastaven `DetectLanguage()` na detect , je volána k určení jazyka zdrojového textu. Text může být v jazyce, který rozhraní API překladače nepodporuje. V takovém případě zobrazte zprávu, která informuje uživatele, a vraťte se bez překladu textu.

Pokud je zdrojovým jazykem angličtina (nezáleží na tom, jestli zadaná nebo rozpoznaná), zkontrolujte pravopis textu pomocí metody `CorrectSpelling()` a proveďte případné opravy. Opravený text je přidán zpět do textové oblasti, takže uživatel uvidí, že byla provedena oprava.

Kód přeložit text by měl vypadat povědomě: sestavení identifikátoru URI, vytvoření požadavku, odeslání a analýza odpovědi. Pole JSON může obsahovat více než jeden objekt pro překlad, ale naše aplikace vyžaduje pouze jeden.

Po úspěšném `TranslatedTextLabel.Content` požadavku je nahrazen `translation`a , který aktualizuje uživatelské rozhraní pro zobrazení přeloženého textu.

## <a name="run-your-wpf-app"></a>Spuštění aplikace WPF

To je ono, máte pracovní překlad aplikace postavena pomocí WPF. Pokud chcete aplikaci spustit, klikněte v Sadě Visual Studio na tlačítko **Start.**

## <a name="source-code"></a>Zdrojový kód

Zdrojový kód pro tento projekt je k dispozici na GitHubu.

* [Prozkoumat zdrojový kód](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-C-Sharp-Tutorial)

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Referenční informace k rozhraní Microsoft Translator Text API](https://docs.microsoft.com/azure/cognitive-services/Translator/reference/v3-0-reference)
