---
title: 'Kurz: Vytvoření aplikace překladu pomocí WPF, C# -Translator text API'
titleSuffix: Azure Cognitive Services
description: V tomto kurzu vytvoříte aplikaci Windows Presentation Foundation (WPF), která používá rozhraní API pro rozpoznávání textu, rozpoznávání jazyka a kontrolu pravopisu s použitím jednoho klíče předplatného. Toto cvičení vám ukáže, jak používat funkce z Translator Text API a rozhraní API Bingu pro kontrolu pravopisu.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: tutorial
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: b929d0c0da2a812a1c8595536f09931e4edd0fd9
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/29/2019
ms.locfileid: "68594913"
---
# <a name="tutorial-create-a-translation-app-with-wpf"></a>Kurz: Vytvoření aplikace překladu pomocí WPF

V tomto kurzu sestavíte aplikaci [Windows Presentation Foundation (WPF)](https://docs.microsoft.com/visualstudio/designers/getting-started-with-wpf?view=vs-2017) , která používá Azure Cognitive Services pro překlad textu, rozpoznávání jazyka a kontrolu pravopisu s použitím jediného klíče předplatného. Konkrétně vaše aplikace bude volat rozhraní API z Translator Text a [Kontrola pravopisu Bingu](https://azure.microsoft.com/services/cognitive-services/spell-check/).

Co je WPF? Jedná se o architekturu uživatelského rozhraní, která vytváří klientské aplikace pro stolní počítače. Vývojová platforma WPF podporuje širokou škálu funkcí pro vývoj aplikací, včetně modelu aplikace, prostředků, ovládacích prvků, grafiky, rozložení, datových vazeb, dokumentů a zabezpečení. Je to podmnožina .NET Framework, takže pokud jste dříve vytvořili aplikace s .NET Framework pomocí ASP.NET nebo model Windows Forms, je vhodné seznámit se s programováním v prostředí. WPF používá jazyk XAML (Extensible App Markup Language) k poskytnutí deklarativního modelu pro programování aplikací, který si projdeme v nadcházejících částech.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření projektu WPF v aplikaci Visual Studio
> * Přidání sestavení a balíčků NuGet do projektu
> * Vytvoření uživatelského rozhraní aplikace pomocí jazyka XAML
> * Použijte Translator Text API k získání jazyků, překladu textu a rozpoznání zdrojového jazyka.
> * Použití rozhraní API Bingu pro kontrolu pravopisu k ověření správnosti vstupu a zlepšení přesnosti překladu
> * Spuštění aplikace WPF

### <a name="cognitive-services-used-in-this-tutorial"></a>Cognitive Services použité v tomto kurzu

Tento seznam obsahuje Cognitive Services používané v tomto kurzu. Postupujte podle odkazu a procházejte Reference k rozhraní API pro jednotlivé funkce.

| Služba | Funkce | Popis |
|---------|---------|-------------|
| Translator Text | [Získat jazyky](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-languages) | Načte Úplný seznam podporovaných jazyků pro překlad textu. |
| Translator Text | [Posunut](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate) | Přeloží text na více než 60 jazyků. |
| Translator Text | [Soustavy](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-detect) | Rozpoznávání jazyka vstupního textu. Zahrnuje hodnocení spolehlivosti pro detekci. |
| Kontrola pravopisu Bingu | [Kontrola pravopisu](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v7-reference) | Opravte chyby pravopisu, aby se zlepšila přesnost překladu. |

## <a name="prerequisites"></a>Požadavky

Než budeme pokračovat, budete potřebovat následující:

* Předplatné Azure Cognitive Services. [Získat Cognitive Services klíč](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#multi-service-resource).
* Počítač s Windows
* [Visual Studio 2017](https://www.visualstudio.com/downloads/) – Community nebo Enterprise

> [!NOTE]
> Pro tento kurz doporučujeme vytvořit předplatné v Západní USA oblasti. Jinak budete muset při práci s tímto cvičením změnit koncové body a oblasti v kódu.  

## <a name="create-a-wpf-app-in-visual-studio"></a>Vytvoření aplikace WPF v aplikaci Visual Studio

První věc, kterou je potřeba udělat, je nastavit náš projekt v sadě Visual Studio.

1. Otevřít Visual Studio. Pak vyberte **soubor > nový > projekt**.
2. Na levém panelu vyhledejte a vyberte **vizuál C#** . Pak v prostředním panelu vyberte **aplikace WPF (.NET Framework)** .
   ![Vytvoření aplikace WPF v aplikaci Visual Studio](media/create-wpf-project-visual-studio.png)
3. Pojmenujte `MSTranslatorTextDemo`projekt, nastavte verzi rozhraní Framework na **.NET Framework 4.5.2 nebo novější**a pak klikněte na tlačítko **OK**.
4. Projekt byl vytvořen. Všimněte si, že jsou otevřené dvě karty: `MainWindow.xaml` a. `MainWindow.xaml.cs` V celém tomto kurzu přidáme kód do těchto dvou souborů. První pro uživatelské rozhraní aplikace; druhý pro volání Translator Text a Kontrola pravopisu Bingu.
   ![Kontrola prostředí](media/blank-wpf-project.png)

V další části přidáme sestavení a balíček NuGet do našeho projektu pro další funkce, jako je analýza JSON.

## <a name="add-references-and-nuget-packages-to-your-project"></a>Přidání odkazů a balíčků NuGet do projektu

Náš projekt vyžaduje několik .NET Framework sestavení a NewtonSoft. JSON, které nainstalujeme pomocí Správce balíčků NuGet.

### <a name="add-net-framework-assemblies"></a>Přidat .NET Framework sestavení

Pojďme do našeho projektu přidat sestavení k serializaci a deserializaci objektů a ke správě požadavků a odpovědí HTTP.

1. Vyhledejte svůj projekt v Průzkumník řešení sady Visual Studio (pravý panel). Klikněte pravým tlačítkem na projekt a pak vyberte **přidat > odkaz...** , který otevře **Správce odkazů**.
   ![Přidat odkazy na sestavení](media/add-assemblies-sample.png)
2. Na kartě sestavení jsou uvedena všechna .NET Framework sestavení, která jsou k dispozici pro referenci. Pomocí panelu hledání v pravém horním rohu obrazovky vyhledejte tyto odkazy a přidejte je do projektu:
   * [System. Runtime. Serialization](https://docs.microsoft.com/dotnet/api/system.runtime.serialization)
   * [System.Web](https://docs.microsoft.com/dotnet/api/system.web)
   * [System.Web.Extensions](https://docs.microsoft.com/dotnet/api/system.web)
   * [System.Windows](https://docs.microsoft.com/dotnet/api/system.windows)
3. Po přidání těchto odkazů do projektu můžete kliknutím na tlačítko **OK** zavřít **Správce odkazů**.

> [!NOTE]
> Pokud se chcete dozvědět více o odkazech na sestavení, přečtěte si téma [How to: Přidejte nebo odeberte odkaz pomocí správce](https://docs.microsoft.com/visualstudio/ide/how-to-add-or-remove-references-by-using-the-reference-manager?view=vs-2017)odkazů.

### <a name="install-newtonsoftjson"></a>Nainstalovat NewtonSoft. JSON

Naše aplikace použije NewtonSoft. JSON k deserializaci objektů JSON. Postupujte podle těchto pokynů a nainstalujte balíček.

1. Vyhledejte svůj projekt v Průzkumník řešení sady Visual Studio a klikněte pravým tlačítkem na projekt. Vybrat **Spravovat balíčky NuGet...**
2. Vyhledejte kartu **Procházet** a vyberte ji.
3. Do vyhledávacího panelu zadejte [Newtonsoft. JSON](https://www.nuget.org/packages/Newtonsoft.Json/) .
   ![Vyhledejte a nainstalujte NewtonSoft. JSON.](media/add-nuget-packages.png)
4. Vyberte balíček a klikněte na **nainstalovat**.
5. Po dokončení instalace zavřete kartu.

## <a name="create-a-wpf-form-using-xaml"></a>Vytvoření formuláře WPF pomocí XAML

Chcete-li použít vaši aplikaci, budete potřebovat uživatelské rozhraní. Pomocí XAML vytvoříme formulář, který umožňuje uživatelům vybrat vstupní a překladatelské jazyky, zadat text k překladu a zobrazit výstup překladu.

Pojďme se podívat na to, co vytváříme.

![Uživatelské rozhraní WPF XAML](media/translator-text-csharp-xaml.png)

Uživatelské rozhraní zahrnuje tyto komponenty:

| Name | Typ | Popis |
|------|------|-------------|
| `FromLanguageComboBox` | Polích | Zobrazí seznam jazyků, které Microsoft Translator podporuje pro překlad textu. Uživatel vybere jazyk, ze kterého překládá. |
| `ToLanguageComboBox` | Polích | Zobrazí stejný seznam jazyků jako `FromComboBox`, ale používá se k výběru jazyka, do kterého se uživatel překládá. |
| `TextToTranslate` | TextBox | Umožňuje uživateli zadat text, který se má přeložit. |
| `TranslateButton` | Tlačítko | Pomocí tohoto tlačítka můžete překládat text. |
| `TranslatedTextLabel` | Štítek | Zobrazí překlad. |
| `DetectedLanguageLabel` | Štítek | Zobrazí zjištěný jazyk textu, který se má přeložit (`TextToTranslate`). |

> [!NOTE]
> Tento formulář vytváříme pomocí zdrojového kódu XAML, ale můžete vytvořit formulář pomocí editoru v aplikaci Visual Studio.

Pojďme do projektu přidat kód.

1. V aplikaci Visual Studio vyberte kartu pro `MainWindow.xaml`.
2. Zkopírujte tento kód do projektu a uložte.
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
3. Nyní by se měla zobrazit verze Preview uživatelského rozhraní aplikace v aplikaci Visual Studio. Měl by vypadat podobně jako na obrázku výše.

To je, váš formulář je připravený. Nyní napíšeme kód pro použití překladu textu a Kontrola pravopisu Bingu.

> [!NOTE]
> Nebojte se upravit tento formulář nebo vytvořit vlastní.

## <a name="create-your-app"></a>Vytvoření aplikace

`MainWindow.xaml.cs`obsahuje kód, který řídí naši aplikaci. V následujících částech se přidá kód pro naplnění našich rozevíracích nabídek a volání několik rozhraní API vystaveného Translator Text a Kontrola pravopisu Bingu.

* Při spuštění a `MainWindow` vytvoření instance `Languages` programu je volána metoda Translator text API pro načtení a naplnění rozevíracích seznamu pro výběr jazyka. K tomu dojde jednou na začátku každé relace.
* Po kliknutí na tlačítko **přeložit** se načtou výběr jazyka a text, kontrola pravopisu se u vstupu provede a pro uživatele se zobrazí překlad a zjištěný jazyk.
  * Metoda Translator text API je volána k překladu `TextToTranslate`textu. `Translate` Toto volání zahrnuje `to` také jazyky a `from` vybrané pomocí rozevíracích nabídek.
  * Metoda Translator text API je volána k určení `TextToTranslate`jazyka textu. `Detect`
  * Kontrola pravopisu Bingu slouží k ověřování `TextToTranslate` a úpravám chybných pravopisů.

Všechny projekty jsou zapouzdřeny ve `MainWindow : Window` třídě. Pojďme začít přidáním kódu pro nastavení klíče předplatného, deklarovat koncové body pro Translator Text a Kontrola pravopisu Bingu a inicializovat aplikaci.

1. V aplikaci Visual Studio vyberte kartu pro `MainWindow.xaml.cs`.
2. Předem vyplněné `using` příkazy nahraďte následujícím.  
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
3. `MainWindow : Window` Vyhledejte třídu a nahraďte ji tímto kódem:
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
   1. Přidejte svůj klíč předplatného Cognitive Services a uložte ho.

V tomto bloku kódu jsme deklarovali dvě členské proměnné, které obsahují informace o dostupných jazycích pro překlad:

| Proměnná | type | Popis |
|----------|------|-------------|
|`languageCodes` | pole řetězců |Aches kódu jazyka C. Služba Translator používá k identifikaci jazyků krátké kódy, například `en` pro angličtinu. |
|`languageCodesAndTitles` | Seřazený slovník | Mapuje popisné názvy z uživatelského rozhraní na krátké kódy používané v rozhraní API. Používá abecední řazení, velká a malá písmena se nerozlišují. |

V rámci tohoto `MainWindow` konstruktoru jsme přidali zpracování chyb s `HandleExceptions`. Tím je zajištěno, že je k dispozici výstraha, pokud není zpracována výjimka. Pak se spustí Kontrola, aby se ověřilo, že zadaný klíč předplatného má délku 32 znaků. Pokud je klíč menší než 32 znaků, je vyvolána chyba.

Pokud existují klíče, které mají alespoň správnou délku, `InitializeComponent()` volání získá uživatelské rozhraní tak, že vyhledá, načte a vytvoří instanci popisu XAML hlavního okna aplikace.

Nakonec jsme přidali kód pro volání metod pro načtení jazyků pro překlad a naplnění rozevíracích nabídek pro uživatelské rozhraní naší aplikace. Nedělejte si starosti, až brzy obdržíme kód za těmito hovory.

## <a name="get-supported-languages"></a>Zjištění podporovaných jazyků

Translator Text API aktuálně podporuje více než 60 jazyků. Vzhledem k tomu, že se nová jazyková podpora přidá v průběhu času, doporučujeme, abyste místo zakódujeme seznamu jazyků ve vaší aplikaci zavolali prostředek jazyky, který je vystavený Translator Text.

V této části vytvoříme `GET` požadavek na prostředek jazyků a určíme, že chceme seznam jazyků dostupných pro překlad.

> [!NOTE]
> Prostředek jazyky umožňuje filtrovat jazykovou podporu pomocí následujících parametrů dotazu: převádění, slovníku a překladu. Další informace najdete v tématu [Reference k rozhraní API](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-languages).

Než budeme pokračovat, Pojďme se podívat na ukázkový výstup pro volání do prostředku jazyky:

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

Z tohoto výstupu můžete extrahovat kód jazyka a `name` konkrétní jazyk. Naše aplikace používá NewtonSoft. JSON k deserializaci objektu JSON ([`JsonConvert.DeserializeObject`](https://www.newtonsoft.com/json/help/html/M_Newtonsoft_Json_JsonConvert_DeserializeObject__1.htm)).

Vyzvednutím místa, kde jsme v poslední části opustili, můžeme přidat metodu, která pro naši aplikaci získá podporované jazyky.

1. V aplikaci Visual Studio otevřete kartu pro `MainWindow.xaml.cs`.
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

Metoda vytvoří požadavek HTTP GET a `scope=translation` použije parametr řetězce dotazu k omezení rozsahu požadavku na podporované jazyky pro překlad. `GetLanguagesForTranslate()` Přidá se hlavička `Accept-Language` s hodnotou `en`, aby se podporované jazyky vrátily v angličtině.

Odpověď JSON se analyzuje a převede do slovníku. Pak jsou kódy jazyků přidány do `languageCodes` členské proměnné. Páry klíč-hodnota, které obsahují kódy jazyků a popisné názvy jazyků, se zkompletují a přidají do členské proměnné `languageCodesAndTitles`. Rozevírací nabídky ve formuláři zobrazují popisné názvy, ale kódy jsou potřeba k vyžádání překladu.

## <a name="populate-language-drop-down-menus"></a>Naplnit rozevírací nabídky jazyk

Uživatelské rozhraní je definováno pomocí jazyka XAML, takže nemusíte nic dělat, abyste ho nastavili více `InitializeComponent()`než volání. Jedna z věcí, kterou musíte udělat, je přidání popisných jazyků do rozevíracích nabídek **přeložit z** a **převést na** rozevírací nabídky, to se `PopulateLanguageMenus()` provádí pomocí metody.

1. V aplikaci Visual Studio otevřete kartu pro `MainWindow.xaml.cs`.
2. Přidejte tento kód do projektu pod `GetLanguagesForTranslate()` metodu:
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

Tato metoda provede iteraci ve `languageCodesAndTitles` slovníku a přidá každý klíč do obou nabídek. Po naplnění nabídek jsou výchozí hodnoty z a do jazyků nastavené na **detekovat** a v **angličtině** .

> [!TIP]
> Pokud by se nevybraly výchozí hodnoty pro nabídky, mohlo by se stát, že uživatel klikne na **Translate** (Přeložit), aniž by napřed zvolil zdrojový a cílový jazyk. Výchozí hodnoty eliminují nutnost zabývat se tímto problémem.

Nyní, `MainWindow` které bylo inicializováno a uživatelské rozhraní vytvořeno, nebude tento kód spuštěn, dokud není kliknuto na tlačítko **přeložit** .

## <a name="detect-language-of-source-text"></a>Zjistit jazyk zdrojového textu

Nyní vytvoříme metodu pro detekci jazyka zdrojového textu (text zadaný do naší textové oblasti) pomocí Translator Text API. Hodnota vrácená tímto požadavkem bude později použita v naší žádosti o překlad.

1. V aplikaci Visual Studio otevřete kartu pro `MainWindow.xaml.cs`.
2. Přidejte tento kód do projektu pod `PopulateLanguageMenus()` metodu:
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

Tato metoda vytvoří požadavek HTTP `POST` na prostředek detekce. Přebírá jeden argument, `text`který je předán společně jako tělo žádosti. Později po vytvoření naší žádosti o překlad bude text zadaný do našeho uživatelského rozhraní předán této metodě pro detekci jazyka.

Tato metoda navíc vyhodnocuje skóre spolehlivosti odpovědi. Pokud je skóre větší než `0.5`, zobrazí se zjištěný jazyk v našem uživatelském rozhraní.

## <a name="spell-check-the-source-text"></a>Kontrola pravopisu u zdrojového textu

Nyní vytvoříme metodu pro kontrolu pravopisu našeho zdrojového textu pomocí rozhraní API Bingu pro kontrolu pravopisu. Tím se zajistí, že se vám budou vracet přesné překlady z Translator Text API. Jakékoli opravy zdrojového textu jsou při kliknutí na tlačítko **přeložit** předány společně v rámci naší žádosti o překlad.

1. V aplikaci Visual Studio otevřete kartu pro `MainWindow.xaml.cs`.
2. Přidejte tento kód do projektu pod `DetectLanguage()` metodu:

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

## <a name="translate-text-on-click"></a>Při kliknutí překládat text

Poslední věc, kterou je potřeba udělat, je vytvořit metodu, která se vyvolá při kliknutí na tlačítko **přeložit** v našem uživatelském rozhraní.

1. V aplikaci Visual Studio otevřete kartu pro `MainWindow.xaml.cs`.
2. Přidejte tento kód do projektu pod `CorrectSpelling()` metodou a uložte:  
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

Prvním krokem je získání jazyků "od" a "do" a textu, který uživatel zadal do našeho formuláře. Pokud je zdrojový jazyk nastaven na **rozpoznat**, `DetectLanguage()` je volána k určení jazyka zdrojového textu. Text může být v jazyce, který nepodporuje rozhraní API pro překladatele. V takovém případě se zobrazí zpráva s informací o uživateli a vrátí se bez překladu textu.

Pokud je zdrojovým jazykem angličtina (nezáleží na tom, jestli zadaná nebo rozpoznaná), zkontrolujte pravopis textu pomocí metody `CorrectSpelling()` a proveďte případné opravy. Opravený text se přidá zpátky do textové oblasti tak, že uživatel uvidí, že byla provedena oprava.

Kód pro překlad textu by měl vypadat dobře: Sestavte identifikátor URI, vytvořte žádost, odešlete ji a analyzujte odpověď. Pole JSON může obsahovat více než jeden objekt pro překlad, ale naše aplikace ale vyžaduje jenom jednu.

Po úspěšné žádosti `TranslatedTextLabel.Content` je nahrazena `translation`, která aktualizuje uživatelské rozhraní pro zobrazení přeloženého textu.

## <a name="run-your-wpf-app"></a>Spuštění aplikace WPF

To je to, že máte aplikaci pro práci s překlady vytvořenou pomocí WPF. Chcete-li spustit aplikaci, klikněte na tlačítko **Start** v aplikaci Visual Studio.

## <a name="source-code"></a>Zdrojový kód

Zdrojový kód pro tento projekt je k dispozici na GitHubu.

* [Prozkoumat zdrojový kód](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-C-Sharp-Tutorial)

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Referenční informace k rozhraní Microsoft Translator Text API](https://docs.microsoft.com/azure/cognitive-services/Translator/reference/v3-0-reference)
