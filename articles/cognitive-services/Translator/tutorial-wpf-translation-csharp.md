---
title: 'Kurz: Vytvoření aplikace překladu s WPF, C# – Translator Text API'
titleSuffix: Azure Cognitive Services
description: V tomto kurzu vytvoříte aplikaci Windows Presentation Foundation (WPF), který používá rozhraní API služeb Cognitive Service pro překlady textů, detekce jazyka a klíčem jedno předplatné kontrolu pravopisu. V tomto cvičení se ukazují, jak používat funkce z rozhraní Translator Text API a rozhraní API kontrola pravopisu Bingu.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: tutorial
ms.date: 02/13/2019
ms.author: erhopf
ms.openlocfilehash: 97766472ea5f7b62a452e6cc5a71a77426e975ad
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56235406"
---
# <a name="tutorial-create-a-translation-app-with-wpf"></a>Kurz: Vytvoření aplikace překladu s WPF

V tomto kurzu vytvoříte [Windows Presentation Foundation (WPF)](https://docs.microsoft.com/visualstudio/designers/getting-started-with-wpf?view=vs-2017) aplikaci, která používá Azure Cognitive Services pro překlady textů, detekce jazyka a kontroly pravopisu s klíčem jedno předplatné. Konkrétně aplikace bude volat rozhraní API z Translator Text a [kontrolu pravopisu Bingu](https://azure.microsoft.com/services/cognitive-services/spell-check/).

Co je WPF? Je architekturu uživatelského rozhraní, který vytváří aplikace klasické pracovní plochy klienta. Vývojová platforma WPF podporuje širokou škálu funkce pro vývoj aplikací, včetně modelu aplikace, prostředky, ovládací prvky, grafiky, rozložení, datové vazby, dokumenty a zabezpečení. Je podmnožinou rozhraní .NET Framework, takže pokud jste dříve vytvořili aplikace s rozhraním .NET Framework pomocí technologie ASP.NET nebo Windows Forms, by mělo být známé programovací prostředí. WPF používá aplikace Extensible Markup Language (XAML) předávat deklarativní model for programování, což si probereme v nadcházejících částech.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření projektu WPF v sadě Visual Studio
> * Přidat sestavení a balíčky NuGet do projektu
> * Vytvoření uživatelského rozhraní vaší aplikace pomocí XAML
> * Translator Text API využít k získání jazyků, překlad textu a rozpoznání zdrojového jazyka
> * Použití rozhraní API Bingu pro kontrolu pravopisu zkontrolovat ověřit svůj vstup a zvýšit přesnost překladu
> * Spuštění aplikace WPF

### <a name="cognitive-services-used-in-this-tutorial"></a>V tomto kurzu používá služeb cognitive Services

Tento seznam obsahuje služeb Cognitive Services použité v tomto kurzu. Přejděte na odkaz procházením referenci rozhraní API pro jednotlivé funkce.

| Služba | Funkce | Popis |
|---------|---------|-------------|
| Translator Text | [Získání jazyků](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-languages) | Načtěte úplný seznam podporovaných jazyků pro překlady textů. |
| Translator Text | [Translate](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate) | Překlad textu do více než 60 jazyků. |
| Translator Text | [Zjištění](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-detect) | Zjistit jazyk vstupního textu. Zahrnuje skóre spolehlivosti pro zjišťování. |
| Kontrola pravopisu Bingu | [Kontrola pravopisu](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v7-reference) | Opravte pravopisné chyby zvyšte přesnost překladu. |

## <a name="prerequisites"></a>Požadavky

Abychom mohli pokračovat, budete potřebovat následující:

* Předplatné Azure Cognitive Services. [Získání klíče služeb Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#multi-service-subscription).
* Počítače s Windows
* [Visual Studio 2017](https://www.visualstudio.com/downloads/) – Community nebo Enterprise

> [!NOTE]
> Doporučujeme vytvořit předplatné v oblasti západní USA pro účely tohoto kurzu. V opačném případě bude potřeba změnit koncových bodů a oblastmi v kódu při práci prostřednictvím tohoto cvičení.  

## <a name="create-a-wpf-app-in-visual-studio"></a>Vytvoření aplikace WPF v sadě Visual Studio

První věc, kterou budeme muset udělat nastaven našem projektu v sadě Visual Studio.

1. Otevřete sadu Visual Studio. Potom vyberte **soubor > Nový > projekt**.
2. Na panelu vlevo vyhledejte a vyberte **Visual C#** . Vyberte **aplikace WPF (.NET Framework)** panelu System center.
   ![Vytvoření aplikace WPF v sadě Visual Studio](media/create-wpf-project-visual-studio.png)
3. Pojmenujte svůj projekt, nastavte na verzi rozhraní framework **rozhraní .NET Framework 4.5.2 nebo novější**, pak klikněte na tlačítko **OK**.
4. Váš projekt se vytvořil. Všimněte si, že jsou otevřené dvě karty: `MainWindow.xaml` a `MainWindow.xaml.cs`. V celém tomto kurzu přidáme kód pro tyto dva soubory. První pro uživatelské rozhraní aplikace; Ten Translator Text API a kontrolu pravopisu Bingu naše volání.
   ![Projděte si prostředí](media/blank-wpf-project.png)

V další části vytvoříme přidat sestavení a balíček NuGet do projektu pro další funkce, jako je analýza formátu JSON.

## <a name="add-references-and-nuget-packages-to-your-project"></a>Přidání odkazů a balíčků NuGet do projektu

Náš projekt vyžaduje několik sestavení rozhraní .NET Framework a NewtonSoft.Json, které nainstalujeme pomocí Správce balíčků NuGet.

### <a name="add-net-framework-assemblies"></a>Přidat sestavení rozhraní .NET Framework

Přidejme sestavení do projektu k serializaci a deserializaci objektů a ke správě požadavků a odpovědí HTTP.

1. Vyhledejte svůj projekt v Průzkumníku řešení sady Visual Studio (pravý panel). Klikněte pravým tlačítkem na projekt a pak vyberte **Přidat > odkaz...** , které se otevře **správce odkazů**.
   ![Přidat odkazy na sestavení](media/add-assemblies-sample.png)
2. Karta sestavení obsahuje seznam všech sestavení rozhraní .NET Framework, které jsou k dispozici pro odkazování. Vyhledejte tyto odkazy a přidat je do svého projektu pomocí panelu hledání v pravém horním rohu obrazovky:
   * [System.Runtime.Serialization](https://docs.microsoft.com/dotnet/api/system.runtime.serialization?view=netframework-4.7.2)
   * [System.Web](https://docs.microsoft.com/dotnet/api/system.web?view=netframework-4.7.2)
   * [System.Web.Extensions](https://docs.microsoft.com/dotnet/api/system.web?view=netframework-4.7.2)
3. Po přidání těchto odkazů do vašeho projektu, můžete kliknout na **OK** zavřete **správce odkazů**.

> [!NOTE]
> Pokud chcete další informace o odkazech na sestavení, přečtěte si téma [jak: Přidání nebo odebrání odkazu pomocí Správce odkazů](https://docs.microsoft.com/visualstudio/ide/how-to-add-or-remove-references-by-using-the-reference-manager?view=vs-2017).

### <a name="install-newtonsoftjson"></a>Nainstalujte NewtonSoft.Json

Naše aplikace bude používat k deserializaci objektů JSON NewtonSoft.Json. Postupujte podle těchto pokynů k instalaci balíčku.

1. Vyhledejte svůj projekt v Průzkumníku řešení sady Visual Studio a klikněte pravým tlačítkem na projekt. Vyberte **spravovat balíčky NuGet...** .
2. Vyhledejte a vyberte **Procházet** kartu.
3. Typ [NewtonSoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/) do panelu hledání.
   ![Vyhledání a instalaci NewtonSoft.Json](media/add-nuget-packages.png)
4. Vyberte balíček a klikněte na tlačítko **nainstalovat**.
5. Po dokončení instalace zavřete kartu.

## <a name="create-a-wpf-form-using-xaml"></a>Vytvoření formuláře WPF pomocí XAML

Používání vaší aplikace, budete potřebovat uživatelské rozhraní. Pomocí XAML, vytvoříme formuláře, který umožňuje uživatelům vybrat jazyky vstup a překladu, zadejte text k přeložení, a zobrazí výstup překladu.

Pojďme se podívat, co vytváříme.

![Uživatelské rozhraní WPF XAML](media/translator-text-csharp-xaml.png)

Uživatel interfacer obsahuje tyto součásti:

| Název | Typ | Popis |
|------|------|-------------|
| `FromLanguageComboBox` | Pole se seznamem | Zobrazuje seznam jazyků podporovaných Microsoft Translatoru pro překlad textu. Uživatel vybere jazyk, ze kterého překládá. |
| `ToLanguageComboBox` | Pole se seznamem | Zobrazí seznam stejné jazyky jako `FromComboBox`, ale slouží k výběru uživatele se přitom přeložte do jazyka. |
| `TextToTranslate` | TextBox | Umožňuje uživateli zadat text k převodu. |
| `TranslateButton` | Tlačítko | Pomocí tohoto tlačítka můžete přeložit text. |
| `TranslatedTextLabel` | Štítek | Zobrazí překlad. |
| `DetectedLanguageLabel` | Štítek | Zobrazí zjištěný jazyk textu, který se přeložit (`TextToTranslate`). |

> [!NOTE]
> Vytváříme tento formulář pomocí zdrojové stránky XAML, ale můžete vytvořit formulář v editoru v sadě Visual Studio.

Přidejme do projektu kódu.

1. V sadě Visual Studio, vyberte kartu `MainWindow.xaml`.
2. Zkopírujte tento kód do vašeho projektu a uložte.
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
3. Teď byste měli vidět aplikaci prvku uživatelského rozhraní v sadě Visual Studio ve verzi preview. By měla vypadat podobně jako na obrázku výše.

To je vše, formuláře je připravený. Nyní Pojďme napsání kódu pro překlad textu a kontrolu pravopisu Bingu.

> [!NOTE]
> Můžete upravit tento formulář nebo si vytvořte svoje vlastní.

## <a name="create-your-app"></a>Vytvoření aplikace

`MainWindow.xaml.cs` obsahuje kód, který řídí naši aplikaci. V následujících částech budeme přidejte kód k naplnění naší rozevíracích nabídek a k volání na několik rozhraní API, které jsou vystavené Translator Text API a kontrolu pravopisu Bingu.

* Při spuštění programu a `MainWindow` je vytvořena instance, `Languages` metoda rozhraní Translator Text API je volána k načtení a naplnění naší jazyk výběr rozevírací seznamy. K tomu dochází jednou na začátku každé relace.
* Když **přeložit** po kliknutí na tlačítko, textu a výběr jazyka uživatele se načítají, na vstupu je provedena kontrola pravopisu a překladu a zjištěný jazyk se zobrazují pro daného uživatele.
  * `Translate` Metoda rozhraní Translator Text API je volána k přeložení textu z `TextToTranslate`. Toto volání zahrnuje také `to` a `from` jazyky vybrané pomocí rozevírací nabídky.
  * `Detect` Metoda rozhraní Translator Text API je volána k určit jazyk textu `TextToTranslate`.
  * Pro kontrolu pravopisu Bingu se používá k ověření `TextToTranslate` a upravte pravopisné chyby.

Všechny naše projektu, je zapouzdřena v `MainWindow : Window` třídy. Začněme přidáním kódu pro nastavení váš klíč předplatného, deklarujte koncové body pro Translator Text API a kontrolu pravopisu Bingu a inicializace aplikace.

1. V sadě Visual Studio, vyberte kartu `MainWindow.xaml.cs`.
2. Nahraďte předem naplněných `using` příkazy následujícím kódem.  
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
3. Vyhledejte `MainWindow : Window` třídy a nahraďte tento kód:
   ```csharp
   {
       // This sample uses the Cognitive Services subscription key for all services. To learn more about
       // authentication options, see: https://docs.microsoft.com/azure/cognitive-services/authentication.
       const string COGNITIVE_SERVICES_KEY = "YOUR_COG_SERVICES_KEY";
       // Endpoints for Translator Text and Bing Spell Check
       public static readonly string TEXT_TRANSLATION_API_ENDPOINT = "https://api.cognitive.microsofttranslator.com/{0}?api- version=3.0";
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
           System.Windows.app.Current.Shutdown();
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
               System.Windows.app.Current.Shutdown();
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
   4. Přidejte klíč předplatného služeb Cognitive Services a uložte.

V tomto bloku kódu jsme jste deklarovány dva členské proměnné, které obsahují informace o jazycích k dispozici pro překlad:

| Proměnná | Type | Popis |
|----------|------|-------------|
|`languageCodes` | pole řetězců |C aches kódech jazyků. Služba Translator používá k identifikaci jazyků krátké kódy, například `en` pro angličtinu. |
|`languageCodesAndTitles` | Seřazený slovník | Mapuje popisné názvy z uživatelského rozhraní na krátké kódy používané v rozhraní API. Používá abecední řazení, velká a malá písmena se nerozlišují. |

Pak v rámci `MainWindow` konstruktoru, přidali jsme zpracování chyb pomocí `HandleExceptions`. Tím se zajistí, že se zadal výstrahu, pokud výjimka není ošetřena. Pak spustí potvrďte zadaný klíč předplatného je 32 znaků. Pokud je klíč, menší nebo větší než 32 znaků, je vržena chyba.

Pokud jsou klíče, které jsou správnou délku aspoň `InitializeComponent()` volání získá uživatelského rozhraní tak, že vyhledání, načítání a vytvoření instance XAML popis okna hlavní aplikace se zajištěním provozu.

Nakonec jsme přidali kód pro volání metody k načtení jazyků pro překlad a k naplnění rozevíracího seznamu místních nabídek pro naši aplikaci uživatelského rozhraní. Nedělejte si starosti, všechno do kódu těchto volání brzy.

## <a name="get-supported-languages"></a>Zjištění podporovaných jazyků

Translator Text API aktuálně podporuje více než 60 jazyků. Vzhledem k tomu, že časem se přidá nová podpora jazyků, doporučujeme zatelefonovat jazyky prostředek vystavený Translator Text spíše než hardcoding seznamu jazyk v aplikaci.

V této části vytvoříme `GET` požadavek na prostředek jazyky, určení, že má být seznam jazyků, které jsou k dispozici pro překlad.

> [!NOTE]
> Prostředek jazyky vám umožní filtrovat jazykovou podporu s následujícími parametry dotazu: přepis, slovník a překladu. Další informace najdete v tématu [reference k rozhraní API](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-languages).

Než půjdeme dál, Pojďme se podívat na ukázkový výstup pro volání prostředku jazyky:

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

Z tohoto výstupu si společnost Microsoft může extrahovat kód jazyka a `name` konkrétního jazyka. Naše aplikace používá NewtonSoft.Json k deserializaci objektu JSON ([`JsonConvert.DeserializeObject`](https://www.newtonsoft.com/json/help/html/M_Newtonsoft_Json_JsonConvert_DeserializeObject__1.htm)).

Výběr tam, kde jsme přestali v předchozí části, přidáme metodu k získání podporované jazyky pro naši aplikaci.

1. V sadě Visual Studio, otevřete kartu `MainWindow.xaml.cs`.
2. Tento kód vložte do projektu:
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

`GetLanguagesForTranslate()` Metoda vytvoří požadavek HTTP GET a používá `scope=translation` parametru řetězce dotazu se používá k omezení oboru požadavek na podporované jazyky pro překlad. Přidá se hlavička `Accept-Language` s hodnotou `en`, aby se podporované jazyky vrátily v angličtině.

Odpověď JSON je analyzovat a převést do slovníku. Pak kódech jazyků se přidají do `languageCodes` členské proměnné. Páry klíč-hodnota, které obsahují kódy jazyků a popisné názvy jazyků, se zkompletují a přidají do členské proměnné `languageCodesAndTitles`. Rozevíracích nabídek ve formuláři zobrazí popisné názvy, ale kódy jsou potřeba k vyžádání překlad.

## <a name="populate-language-drop-down-menus"></a>Naplnění jazyk rozevíracích nabídek

Uživatelské rozhraní je definován pomocí XAML, takže není nutné provést mnoho nastavení kromě volání `InitializeComponent()`. Jednu věc, kterou je třeba provést je přidána jména popisný jazyka **přeložit z** a **přeložit na** rozevíracích nabídek, používá se k tomu `PopulateLanguageMenus()` metoda.

1. V sadě Visual Studio, otevřete kartu `MainWindow.xaml.cs`.
2. Tento kód vložte do projektu následující `GetLanguagesForTranslate()` metody:
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

Tato metoda Iteruje přes `languageCodesAndTitles` slovníku a přidá každý klíč do obou nabídky. Po zaplnění nabídky, výchozí od a do jazyky jsou nastaveny na **rozpoznat** a **Angličtina** v uvedeném pořadí.

> [!TIP]
> Pokud by se nevybraly výchozí hodnoty pro nabídky, mohlo by se stát, že uživatel klikne na **Translate** (Přeložit), aniž by napřed zvolil zdrojový a cílový jazyk. Výchozí hodnoty eliminují nutnost zabývat se tímto problémem.

Teď, když `MainWindow` byl inicializován a vytvoření uživatelského rozhraní, tento kód nebude spuštěno až **přeložit** po kliknutí na tlačítko.

## <a name="detect-language-of-source-text"></a>Zjistit jazyk zdrojového textu

Teď přejdeme k vytvoření metody pro detekci jazyka zdrojový text (text zadaný do našich textová oblast) pomocí rozhraní Translator Text API. Hodnota vrácená tato žádost se použije v naší žádosti o překlad později.

1. V sadě Visual Studio, otevřete kartu `MainWindow.xaml.cs`.
2. Tento kód vložte do projektu následující `PopulateLanguageMenus()` metody:
   ```csharp
   // ***** DETECT LANGUAGE OF TEXT TO BE TRANSLATED
   private string DetectLanguage(string text)
   {
       string detectUri = string.Format(TEXT_TRANSLATION_API_ENDPOINT ,"detect");

       // Create request to Detect languages with Translator Text
       HttpWebRequest detectLanguageWebRequest = (HttpWebRequest)WebRequest.Create(detectUri);
       detectLanguageWebRequest.Headers.Add("Ocp-Apim-Subscription-Key", COGNITIVE_SERVICES_KEY);
       detectLanguageWebRequest.Headers.Add("Ocp-Apim-Subscription-Region", "westus");
       detectLanguageWebRequest.ContentType = "app/json; charset=utf-8";
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

Tato metoda vytvoří HTTP `POST` požadavek na zjištění prostředků. Přijímá jeden argument, `text`, která se předají jako text žádosti. Později jsme při vytváření žádosti o našich překladu, text zadaný do našich uživatelského rozhraní se předají do této metody pro rozpoznání jazyka.

Kromě toho tato metoda vyhodnotí skóre spolehlivosti odpovědi. Pokud je větší než skóre `0.5`, pak zjištěný jazyk se zobrazí v naší uživatelské rozhraní.

## <a name="spell-check-the-source-text"></a>Zdrojový text pro kontrolu pravopisu

Teď přejdeme k vytvoření Zkontrolujte metodu napsat naše zdrojový text pomocí rozhraní API Bingu pro kontrolu pravopisu zkontrolovat. Tím se zajistí, že ozveme přesné překlady z rozhraní Translator Text API. Všechny opravy zdrojový text se předají v našich překladu při požadavku **přeložit** po kliknutí na tlačítko.

1. V sadě Visual Studio, otevřete kartu `MainWindow.xaml.cs`.
2. Tento kód vložte do projektu následující `DetectLanguage()` metody:

```csharp
// ***** CORRECT SPELLING OF TEXT TO BE TRANSLATED
private string CorrectSpelling(string text)
{
    string uri = BING_SPELL_CHECK_API_ENDPOINT + "?mode=spell&mkt=en-US";

    // Create a request to Bing Spell Check API
    HttpWebRequest spellCheckWebRequest = (HttpWebRequest)WebRequest.Create(uri);
    spellCheckWebRequest.Headers.Add("Ocp-Apim-Subscription-Key", COGNITIVE_SERVICES_KEY);
    spellCheckWebRequest.Method = "POST";
    spellCheckWebRequest.ContentType = "app/x-www-form-urlencoded"; // doesn't work without this

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

## <a name="translate-text-on-click"></a>Přeložení textu při kliknutí

Poslední věcí, které musíme udělat, je vytvoření metody, která je vyvolána při **přeložit** po kliknutí na tlačítko v naší uživatelské rozhraní.

1. V sadě Visual Studio, otevřete kartu `MainWindow.xaml.cs`.
2. Tento kód vložte do projektu následující `CorrectSpelling()` metoda a uložte:  
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
           request.Content = new StringContent(requestBody, Encoding.UTF8, "app/json");
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

Prvním krokem je získání "od" a "do" jazyky a textu v našem formuláři zadané uživatelem. Pokud zdrojový jazyk je nastavená na **rozpoznat**, `DetectLanguage()` je volána k určit jazyk zdrojového textu. Text může být v jiném jazyce, který Translator API nepodporuje. V takovém případě zobrazte zprávu informovat uživatele a vraťte zpět bez překladu textu.

Pokud je zdrojovým jazykem angličtina (nezáleží na tom, jestli zadaná nebo rozpoznaná), zkontrolujte pravopis textu pomocí metody `CorrectSpelling()` a proveďte případné opravy. Opravený text je přidána zpět do textového pole tak, aby se uživateli zobrazí, že oprava proběhla.

Kód můžete přeložit text by měla vypadat povědomě: identifikátor URI sestavení, vytvořit žádost o, odeslat ho a analyzovat odpověď. Pole JSON může obsahovat více než jeden objekt překladu, ale naše aplikace vyžaduje pouze jeden.

Po úspěšném požadavku `TranslatedTextLabel.Content` nahradí `translation`, jaké aktualizace uživatelského rozhraní k zobrazení přeloženého textu.

## <a name="run-your-wpf-app"></a>Spuštění aplikace WPF

To je vše, máte funkční aplikaci překlad vytvořené pomocí grafického subsystému WPF. Ke spuštění vaší aplikace, klikněte na tlačítko **Start** tlačítko v sadě Visual Studio.

## <a name="source-code"></a>Zdrojový kód

Zdrojový kód pro tento projekt je k dispozici na Githubu.

* [Prozkoumejte zdrojový kód](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-C-Sharp-Tutorial)

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Referenční informace k rozhraní Microsoft Translator Text API](https://docs.microsoft.com/azure/cognitive-services/Translator/reference/v3-0-reference)
