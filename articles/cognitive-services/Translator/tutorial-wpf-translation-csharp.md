---
title: Tom, jak psát aplikace WPF překladač Microsoft pomocí C#, kognitivní služeb Azure | Microsoft Docs
description: Zjistěte, jak používat službu text překladač převede text, získat lokalizované seznam podporovaných jazyků a další.
services: cognitive-services
author: Jann-Skotdal
manager: chriswendt1
ms.service: cognitive-services
ms.component: translator-text
ms.devlang: csharp
ms.topic: article
ms.date: 10/25/2017
ms.author: v-jansko
ms.openlocfilehash: fb58fd087de09561a0ea930748562e595d3dde1c
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342771"
---
# <a name="how-to-write-a-microsoft-translator-wpf-application-in-c"></a>Tom, jak psát aplikace Microsoft překladač WPF v jazyce C#

V tomto kurzu využijeme nástroj pro překlad na interaktivní text použití Microsoft překladač Text rozhraní API (V3), součástí kognitivní služby společnosti Microsoft v Azure. Dozvíte, jak:

> [!div class="checklist"]
> * Žádost o seznam sad krátké kódy pro jazyky podporované službou
> * Načtení seznamu názvy lokalizovaných jazyků odpovídající tyto kódy jazyků
> * Získat přeložená verze uživatelem zadaný text z jednoho jazyka do druhého

Tato aplikace také funkce integrace s dva další kognitivní službami Microsoftu.

|||
|-|-|
|[Analýza textu](https://azure.microsoft.com/services/cognitive-services/text-analytics/)|Umožňuje volitelně automaticky zjišťovat zdroje jazyk textu k převodu|
|[Kontrola pravopisu v Bingu](https://azure.microsoft.com/services/cognitive-services/spell-check/)|Pro angličtinu zdrojový text, používá k odstranění pravopisné chyby tak, aby překlad přesnější

![[Kurz program spuštěn]](media/translator-text-csharp-session.png)

## <a name="prerequisites"></a>Požadavky

Chcete v tomto kurzu potřebujete libovolná edice Visual Studio 2017, včetně edice Community.

Budete také potřebovat předplatné klíče pro tyto tři služby Azure, které jsou používané v programu. Klíč pro službu překladač Text můžete získat z řídicího panelu Azure. Volná cenová úroveň je k dispozici, které umožňuje překládat až 2 000 znaků za měsíc zdarma.

Analýza textu i kontrola pravopisu Bing služeb nabídka bezplatné zkušební verze, které jste si zaregistrovat na [zkuste kognitivní služby](https://azure.microsoft.com/try/cognitive-services/). Můžete také vytvořit odběr služby buď přes řídicí panel Azure. Analýza textu obsahuje volné vrstvy.

Zdrojový kód pro tento kurz je k dispozici níže. Vaše předplatné klíče musí být zkopírován do zdrojového kódu jako proměnné `TEXT_TRANSLATION_API_SUBSCRIPTION_KEY`a tak dále v `MainWindow.xaml.cs`.

> [!IMPORTANT]
> Analýza textu služba je k dispozici v několika oblastech. Identifikátor URI v našem kurzu zdrojový kód je v `westus` oblasti, která je oblast použít bezplatné zkušební verze. Pokud máte předplatné v jiné oblasti, aktualizují odpovídajícím způsobem tento identifikátor URI.

## <a name="source-code"></a>Zdrojový kód

Toto je zdrojový kód pro text Microsoft Translator rozhraní API. Pokud chcete spustit aplikaci, zkopírujte do příslušný soubor v nový projekt WPF v sadě Visual Studio zdrojový kód.

### <a name="mainwindowxamlcs"></a>MainWindow.xaml.cs

Toto je soubor kódu, který poskytuje funkce aplikace.

```cs
using System;
using System.Windows;
using System.Net;
using System.Net.Http;
using System.IO;
using System.Collections.Generic;
using System.Linq;
using System.Text;

// NOTE: Add assembly references to System.Runtime.Serialization, System.Web, and System.Web.Extensions.

// NOTE: Install the Newtonsoft.Json NuGet package.
using Newtonsoft.Json;

namespace MSTranslatorTextDemo
{
    /// <summary>
    /// This WPF application demonstrates the use of the Microsoft Translator Text API to translate a brief text string
    /// one language to another. The langauges are selected from a drop-down menu. The text of the translation is displayed.
    /// The source language may optionally be automatically detected.  English text is spell-checked.
    /// </summary>
    public partial class MainWindow : Window
    {
        // Translator text subscription key from Microsoft Azure dashboard
        const string TEXT_TRANSLATION_API_SUBSCRIPTION_KEY = "ENTER KEY HERE";
        const string TEXT_ANALYTICS_API_SUBSCRIPTION_KEY = "ENTER KEY HERE";
        const string BING_SPELL_CHECK_API_SUBSCRIPTION_KEY = "ENTER KEY HERE";

        public static readonly string TEXT_TRANSLATION_API_ENDPOINT = "https://api.cognitive.microsofttranslator.com/{0}?api-version=3.0";
        const string TEXT_ANALYTICS_API_ENDPOINT = "https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/";
        const string BING_SPELL_CHECK_API_ENDPOINT = "https://api.cognitive.microsoft.com/bing/v7.0/spellcheck/";

        private string[] languageCodes;     // array of language codes

        // Dictionary to map language code from friendly name (sorted case-insensitively on language name)
        private SortedDictionary<string, string> languageCodesAndTitles =
            new SortedDictionary<string, string>(Comparer<string>.Create((a, b) => string.Compare(a, b, true)));

        public MainWindow()
        {
            // at least show an error dialog when we get an unexpected error
            AppDomain.CurrentDomain.UnhandledException += new UnhandledExceptionEventHandler(HandleExceptions);

            if (TEXT_TRANSLATION_API_SUBSCRIPTION_KEY.Length != 32
                || TEXT_ANALYTICS_API_SUBSCRIPTION_KEY.Length != 32
                || BING_SPELL_CHECK_API_SUBSCRIPTION_KEY.Length != 32)
            {
                MessageBox.Show("One or more invalid API subscription keys.\n\n" +
                    "Put your keys in the *_API_SUBSCRIPTION_KEY variables in MainWindow.xaml.cs.",
                    "Invalid Subscription Key(s)", MessageBoxButton.OK, MessageBoxImage.Error);
                System.Windows.Application.Current.Shutdown();
            }
            else
            {
                InitializeComponent();          // start the GUI
                GetLanguagesForTranslate();     // get codes and friendly names of languages that can be translated
                PopulateLanguageMenus();        // fill the drop-down language lists
            }
        }

        // Global exception handler to display error message and exit
        private static void HandleExceptions(object sender, UnhandledExceptionEventArgs args)
        {
            Exception e = (Exception)args.ExceptionObject;
            MessageBox.Show("Caught " + e.Message, "Error", MessageBoxButton.OK, MessageBoxImage.Error);
            System.Windows.Application.Current.Shutdown();
        }

        // ***** POPULATE LANGUAGE MENUS
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

            // set default languages
            FromLanguageComboBox.SelectedItem = "Detect";
            ToLanguageComboBox.SelectedItem = "English";
        }

        // ***** DETECT LANGUAGE OF TEXT TO BE TRANSLATED
        private string DetectLanguage(string text)
        {
            string uri = TEXT_ANALYTICS_API_ENDPOINT + "languages?numberOfLanguagesToDetect=1";

            // create request to Text Analytics API
            HttpWebRequest detectLanguageWebRequest = (HttpWebRequest)WebRequest.Create(uri);
            detectLanguageWebRequest.Headers.Add("Ocp-Apim-Subscription-Key", TEXT_ANALYTICS_API_SUBSCRIPTION_KEY);
            detectLanguageWebRequest.Method = "POST";

            // create and send body of request
            var serializer = new System.Web.Script.Serialization.JavaScriptSerializer();
            string jsonText = serializer.Serialize(text);

            string body = "{ \"documents\": [ { \"id\": \"0\", \"text\": " + jsonText + "} ] }";
            byte[] data = Encoding.UTF8.GetBytes(body);
            detectLanguageWebRequest.ContentLength = data.Length;

            using (var requestStream = detectLanguageWebRequest.GetRequestStream())
                requestStream.Write(data, 0, data.Length);

            HttpWebResponse response = (HttpWebResponse)detectLanguageWebRequest.GetResponse();

            // read and and parse JSON response
            var responseStream = response.GetResponseStream();
            var jsonString = new StreamReader(responseStream, Encoding.GetEncoding("utf-8")).ReadToEnd();
            dynamic jsonResponse = serializer.DeserializeObject(jsonString);

            // fish out the detected language code
            var languageInfo = jsonResponse["documents"][0]["detectedLanguages"][0];
            if (languageInfo["score"] > (decimal)0.5)
                return languageInfo["iso6391Name"];
            else
                return "";
        }

        // ***** CORRECT SPELLING OF TEXT TO BE TRANSLATED
        private string CorrectSpelling(string text)
        {
            string uri = BING_SPELL_CHECK_API_ENDPOINT + "?mode=spell&mkt=en-US";

            // create request to Bing Spell Check API
            HttpWebRequest spellCheckWebRequest = (HttpWebRequest)WebRequest.Create(uri);
            spellCheckWebRequest.Headers.Add("Ocp-Apim-Subscription-Key", BING_SPELL_CHECK_API_SUBSCRIPTION_KEY);
            spellCheckWebRequest.Method = "POST";
            spellCheckWebRequest.ContentType = "application/x-www-form-urlencoded"; // doesn't work without this

            // create and send body of request
            string body = "text=" + System.Web.HttpUtility.UrlEncode(text);
            byte[] data = Encoding.UTF8.GetBytes(body);
            spellCheckWebRequest.ContentLength = data.Length;
            using (var requestStream = spellCheckWebRequest.GetRequestStream())
                requestStream.Write(data, 0, data.Length);
            HttpWebResponse response = (HttpWebResponse)spellCheckWebRequest.GetResponse();

            // read and parse JSON response and get spelling corrections
            var serializer = new System.Web.Script.Serialization.JavaScriptSerializer();
            var responseStream = response.GetResponseStream();
            var jsonString = new StreamReader(responseStream, Encoding.GetEncoding("utf-8")).ReadToEnd();
            dynamic jsonResponse = serializer.DeserializeObject(jsonString);
            var flaggedTokens = jsonResponse["flaggedTokens"];

            // construct sorted dictionary of corrections in reverse order in string (right to left)
            // so that making a correction can't affect later indexes
            var corrections = new SortedDictionary<int, string[]>(Comparer<int>.Create((a, b) => b.CompareTo(a)));
            for (int i = 0; i < flaggedTokens.Length; i++)
            {
                var correction = flaggedTokens[i];
                var suggestion = correction["suggestions"][0];  // consider only first suggestion
                if (suggestion["score"] > (decimal)0.7)         // take it only if highly confident
                    corrections[(int)correction["offset"]] = new string[]   // dict key   = offset
                        { correction["token"], suggestion["suggestion"] };  // dict value = {error, correction}
            }

            // apply the corrections in order from right to left
            foreach (int i in corrections.Keys)
            {
                var oldtext = corrections[i][0];
                var newtext = corrections[i][1];

                // apply capitalization from original text to correction - all caps or initial caps
                if (text.Substring(i, oldtext.Length).All(char.IsUpper)) newtext = newtext.ToUpper();
                else if (char.IsUpper(text[i])) newtext = newtext[0].ToString().ToUpper() + newtext.Substring(1);

                text = text.Substring(0, i) + newtext + text.Substring(i + oldtext.Length);
            }

            return text;
        }

        // ***** GET TRANSLATABLE LANGUAGE CODES
        private void GetLanguagesForTranslate()
        {
            // send request to get supported language codes
            string uri = String.Format(TEXT_TRANSLATION_API_ENDPOINT, "languages") + "&scope=translation";
            WebRequest WebRequest = WebRequest.Create(uri);
            WebRequest.Headers.Add("Ocp-Apim-Subscription-Key", TEXT_TRANSLATION_API_SUBSCRIPTION_KEY);
            WebRequest.Headers.Add("Accept-Language", "en");
            WebResponse response = null;
            // read and parse the JSON response
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
                request.Headers.Add("Ocp-Apim-Subscription-Key", TEXT_TRANSLATION_API_SUBSCRIPTION_KEY);
                request.Headers.Add("X-ClientTraceId", Guid.NewGuid().ToString());

                var response = await client.SendAsync(request);
                var responseBody = await response.Content.ReadAsStringAsync();

                var result = JsonConvert.DeserializeObject<List<Dictionary<string, List<Dictionary<string, string>>>>>(responseBody);
                var translations = result[0]["translations"];
                var translation = translations[0]["text"];

                // Update the translation field
                TranslatedTextLabel.Content = translation;
            }
        }
    }
}
```

### <a name="mainwindowxaml"></a>MainWindow.xaml

Tento soubor definuje uživatelské rozhraní pro aplikaci, formuláře WPF. Pokud chcete navrhnout vlastní verzi formuláře, není nutné tuto XAML.

```xml
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
        <Label x:Name="TranslatedTextLabel" Content="Translation appears here" HorizontalAlignment="Left" Margin="39,255,0,0" VerticalAlignment="Top" Width="620" FontSize="14" Height="85" BorderThickness="0"/>
    </Grid>
</Window>
```

## <a name="service-endpoints"></a>Koncové body služby

Služba Microsoft Translator má řada koncové body, které poskytují různé funkce překladu. Jsou ty, které používáme v tomto kurzu:

|||
|-|-|
|`Languages`|Vrací sadu jazyky jiné operace rozhraní API překladač Text v současné době podporuje.|
|`Translate`|Zadaný zdrojový text, kód jazyka zdroje a cílový jazyk kódu, vrátí posunutí zdrojového textu cílový jazyk.|

## <a name="the-translation-app"></a>Překlad aplikace

Uživatelské rozhraní naše Překladač aplikace vytvořená s využitím Windows Presentation Foundation (WPF). Pomocí následujících kroků vytvořte nový projekt WPF v sadě Visual Studio.

* Z **soubor** nabídce zvolte **nový > projekt**.
* V okně Nový projekt otevřete **nainstalovaná > šablony > Visual C#**. Seznam dostupných projektů nenachází šablon, které se zobrazí v centru dialogového okna.
* Zajistěte, aby **rozhraní .NET Framework 4.5.2** je vybrán v rozevírací nabídce nahoře seznam šablon projektu.
* Klikněte na tlačítko **aplikace WPF (rozhraní .NET Framework)** v seznamu šablon projektu.
* Pomocí pole v dolní části dialogového okna, název nového projektu a řešení, který jej obsahuje.
* Klikněte na tlačítko **OK** k vytvoření nového projektu a řešení.

![[Vytvořit nové aplikaci WPF v sadě Visual Studio]](media/translator-text-csharp-new-project.png)

Do projektu přidejte odkazy na následující sestavení rozhraní .NET framework.

* System.Runtime.Serialization
* System.Web
* System.Web.Extensions

Rovněž nainstalovat balíček NuGet `Newtonsoft.Json` do projektu.

Nyní najdete `MainWindow.xaml` soubor v Průzkumníku řešení a otevřete ji. Je prázdné původně. Zde je co dokončení uživatelské rozhraní by měl vypadat, poznámkami s názvy ovládacích prvků modře. Použijte stejné názvy ovládacích prvků v uživatelském rozhraní, protože zároveň jsou v kódu.

![[Poznámkou zobrazení hlavní okno v návrháři Visual Studio]](media/translator-text-csharp-xaml.png)

> [!NOTE]
> Zdrojový kód pro tento kurz zahrnuje zdroji XAML pro tento formulář. Můžete ji vložit do projektu místo vytvoření formuláře v sadě Visual Studio.

* `FromLanguageComboBox` *(ComboBox)*  – Zobrazuje seznam jazyků podporovaných v Microsoft Translator pro překlad textu. Uživatel vybere jazyk, ve kterém jsou překladu z.
* `ToLanguageComboBox` *(ComboBox)*  -Zobrazí stejný seznam jazyků jako `FromComboBox`, ale slouží k výběru jazyka je k překladu uživatele.
* `TextToTranslate` *(Textového pole)*  -Uživatel zadá text, který se tady přeložit.
* `TranslateButton` *(Tlačítko)*  -Uživatel klikne na toto tlačítko (nebo stiskne klávesu Enter) převod daného textu.
* `TranslatedTextLabel` *(Název)*  -Překlad pro text uživatele, zobrazí se zde.

Pokud vytvoříte vlastní verzi tohoto formuláře, není to nutné, aby bylo *přesně* jako náš. Ale ujistěte se, že jazyk rozevírací seznamy jsou dost široké, aby se zabránilo vyjímání vypnout název jazyka.

## <a name="the-mainwindow-class"></a>MainWindow – třída

Soubor modelu code-behind `MainWindow.xaml.cs` je, kde kód přejde, který zpřístupňuje programu udělat, co dělá. Práce se odehrává na dvakrát:

* Při spuštění programu. Když `MainWindow` se vytvořit instanci, jsme načíst seznam jazyků pomocí převaděče `GetLanguagesForTranslate` a `GetLanguageNames` rozhraní API a naplnit naše rozevíracích nabídek s nimi. Tato úloha se provádí jednou na začátku každé relaci.

* Když uživatel klikne **přeložit** tlačítko načteme volba jazyka uživatele a jejich zadaný text. Nám zavolat `Translate` rozhraní API k provedení překlad. Říkáme může také další funkce určit jazyk textu a opravte pravopis před překlad.

Podíváme, jak začneme Naše třída:

```cs
public partial class MainWindow : Window
{
    // Translator text subscription key from Microsoft Azure dashboard
    const string TEXT_TRANSLATION_API_SUBSCRIPTION_KEY = "ENTER KEY HERE";
    const string TEXT_ANALYTICS_API_SUBSCRIPTION_KEY = "ENTER KEY HERE";
    const string BING_SPELL_CHECK_API_SUBSCRIPTION_KEY = "ENTER KEY HERE";

    public static readonly string TEXT_TRANSLATION_API_ENDPOINT = "https://api.cognitive.microsofttranslator.com/{0}?api-version=3.0";
    const string TEXT_ANALYTICS_API_ENDPOINT = "https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/";
    const string BING_SPELL_CHECK_API_ENDPOINT = "https://api.cognitive.microsoft.com/bing/v7.0/spellcheck/";

    private string[] languageCodes;     // array of language codes

    // Dictionary to map language code from friendly name (sorted case-insensitively on language name)
    private SortedDictionary<string, string> languageCodesAndTitles =
        new SortedDictionary<string, string>(Comparer<string>.Create((a, b) => string.Compare(a, b, true)));

    public MainWindow()
    {
        // at least show an error dialog when we get an unexpected error
        AppDomain.CurrentDomain.UnhandledException += new UnhandledExceptionEventHandler(HandleExceptions);

        if (TEXT_TRANSLATION_API_SUBSCRIPTION_KEY.Length != 32
            || TEXT_ANALYTICS_API_SUBSCRIPTION_KEY.Length != 32
            || BING_SPELL_CHECK_API_SUBSCRIPTION_KEY.Length != 32)
        {
            MessageBox.Show("One or more invalid API subscription keys.\n\n" +
                "Put your keys in the *_API_SUBSCRIPTION_KEY variables in MainWindow.xaml.cs.",
                "Invalid Subscription Key(s)", MessageBoxButton.OK, MessageBoxImage.Error);
            System.Windows.Application.Current.Shutdown();
        }
        else
        {
            InitializeComponent();          // start the GUI
            GetLanguagesForTranslate();     // get codes and friendly names of languages that can be translated
            PopulateLanguageMenus();        // fill the drop-down language lists
        }
    }
// more to come
}
```

Dva členské proměnné deklarovány zde obsahovat informace o našich jazycích:

|||
|-|-|
|`languageCodes`<br>pole řetězců|Ukládá do mezipaměti kódy jazyků. Služba překladač používá krátké kódy, jako například `en` pro angličtinu, k identifikaci jazyky.|
|`languageCodesAndTitles`<br>Sorteddictionary –|Mapy "popisné" názvy v uživatelském rozhraní zpět na krátké kódy používané v rozhraní API. Zachovány seřazený abecedně bez ohledu na případ.|

Kód první provést v naší aplikaci `MainWindow` konstruktor. Nejprve nastavíme metodu `HandleExceptions` jako globální obslužné rutiny. Tímto způsobem jsme alespoň nastavení výstrahy chyba není zpracovává všechny výjimky.

Dále jsme zkontrolujte Ujistěte se, že předplatné klíče rozhraní API jsou všechny přesně 32 znaků. Pokud nejsou, pravděpodobně důvodem je, že *někdo* nebyl vložit v jejich klíče rozhraní API (tsk). V takovém případě jsme zobrazí se chybová zpráva a nichž. (Tento test předávání neznamená klíčů jsou platné, samozřejmě.)

Pokud budeme mít klíče, které jsou alespoň správné délka `InitializeComponent()` volání získá uživatelské rozhraní vrácení hledání, načítání a vytváření instancí XAML Popis hlavního okna aplikace.

Nakonec nastavíme jazyk rozevíracích nabídek. Tato úloha vyžaduje tři samostatné metodě volání. Jsme projít tyto metody podrobně v následujících částech.

## <a name="get-supported-languages"></a>Získání podporovaných jazyků

Služba Microsoft Translator podporuje celkem 61 jazyky v psaní tohoto textu a informace mohou být přidány čas od času. Proto se má osvědčené nechcete pevně podporované jazyky v programu. Službu překladač požádejte místo toho, jaké jazyky podporuje. Všechny podporované jazykové lze přeložit do dalších podporovaných jazyků.

Volání `Languages` rozhraní API pro získání seznamu podporované jazyky.

`Languages` Rozhraní API přebírá volitelný parametr dotazu GET *oboru*. *obor* může mít jednu ze tří hodnot: `translation`, `transliteration`, a `dictionary`. Použijeme hodnotu `translation`.

`Languages` Rozhraní API také přebírá volitelné záhlaví HTTP, `Accept-Language`. Hodnotu této hlavičky určuje jazyk, ve kterém jsou vráceny názvy podporované jazyky. Hodnota musí být ve správném formátu značku jazyka BCP 47. Použijeme hodnotu `en` získat názvy jazyk v angličtině.

`Languages` Rozhraní API vrátí odpověď JSON, který vypadá jako následující.

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
    },
...
}
```

Chceme extrahovat kódy jazyků (například `af`) a názvy jazyků (například `Afrikaans`). Můžeme použít metodu NewtonSoft.Json [JsonConvert.DeserializeObject](https://www.newtonsoft.com/json/help/html/M_Newtonsoft_Json_JsonConvert_DeserializeObject__1.htm) k tomu.

Pozadí replikace vytvoříme metodu načtení kódy jazyků a jejich názvy.

```cs
private void GetLanguagesForTranslate()
{
    // send request to get supported language codes
    string uri = String.Format(TEXT_TRANSLATION_API_ENDPOINT, "languages") + "&scope=translation";
    WebRequest WebRequest = WebRequest.Create(uri);
    WebRequest.Headers.Add("Ocp-Apim-Subscription-Key", TEXT_TRANSLATION_API_SUBSCRIPTION_KEY);
    WebRequest.Headers.Add("Accept-Language", "en");
    WebResponse response = null;
    // read and parse the JSON response
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
```

`GetLanguagesForTranslate()` nejprve vytvoří žádost HTTP. `scope=translation` Řetězec parametr určuje, že má být jazyky podporované pro překlad text dotazu. Klíč rozhraní API překlad Text předplatné přidáme do našich hlavičky žádosti. Můžeme také přidat `Accept-Language` záhlaví s hodnotou `en` označíte, chceme podporované jazyky, vrátí se v angličtině.

Po dokončení žádosti nám analyzovat odpověď JSON a převést jej do slovníku. Přidáme jazyk kódy, které mají `languageCodes` členské proměnné. Jsme pak projít páry klíč/hodnota, které obsahují kódy a názvy popisný jazyk a přidejte je do `languageCodesAndTitles` členské proměnné. (Rozevíracích nabídek v našem formuláře zobrazení popisných názvů, ale potřebujeme kódy požádat o překlad.)

## <a name="populate-the-language-menus"></a>Naplnění nabídky Jazyk

Většina našich uživatelské rozhraní je definován v jazyce XAML, takže jsme nemusíte dělat mnohem nastavit kromě volání `InitializeComponent()`. Pouze dalším krokem, je potřeba udělat, je přidat názvy popisný jazyk Komu a z rozevírací seznamy, které se provádí v `PopulateLanguageMenus()`.

```cs
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

    // set default languages
    FromLanguageComboBox.SelectedItem = "Detect";
    ToLanguageComboBox.SelectedItem = "English";
}
```

Naplnění v nabídkách je přehledné řádu iterování přes `languageCodesAndTitles` slovníku a přidávání každý klíč, který je "popisný" název, do obou nabídek. Po naplnění v nabídkách, nastaví výchozí do a z jazyky zjistit (pro automatické rozpoznání jazyk) a angličtinu.

> [!TIP]
> Pokud pro naše nabídky jsme nemáte nastavené výchozí výběr, můžete kliknout na uživatele **přeložit** bez výběr na nebo z jazyka. Výchozí hodnoty eliminovat potřebu řešení tohoto problému.

Nyní `MainWindow` byl inicializován, vytváření uživatelského rozhraní. Jsme Nezískávat řízení znovu dokud uživatel klikne na **přeložit** tlačítko.

## <a name="perform-translation"></a>Překlad

Když uživatel klikne na **přeložit**, vyvolá grafického subsystému WPF `TranslateButton_Click()` obslužné rutiny události, tady uvedené.

```cs
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
        request.Headers.Add("Ocp-Apim-Subscription-Key", TEXT_TRANSLATION_API_SUBSCRIPTION_KEY);
        request.Headers.Add("X-ClientTraceId", Guid.NewGuid().ToString());

        var response = await client.SendAsync(request);
        var responseBody = await response.Content.ReadAsStringAsync();

        var result = JsonConvert.DeserializeObject<List<Dictionary<string, List<Dictionary<string, string>>>>>(responseBody);
        var translations = result[0]["translations"];
        var translation = translations[0]["text"];

        // Update the translation field
        TranslatedTextLabel.Content = translation;
    }
}
```

Zde načteme Komu a z jazyků, společně s text uživatel zadal, z formuláře.

Pokud zdroj jazyk nastavena zjistit, říkáme `DetectLanguage()` Určuje jazyk textu. Text může být v jazyce, který není rozhraní API překladač podporu (mnoho další jazyky lze zjistit než lze přeložit) nebo rozhraní API Analytics Text nemusí být možné zjišťovat. Zobrazuje se v takovém případě zprávy, která se informovat o tom uživatele a vraťte zpět bez překladu.

Pokud jazyk zdrojového je angličtina (jestli zjištěna nebo zadán), jsme-kontrola pravopisu textu s `CorrectSpelling()` a použití opravy. Opravené text je nenadívané zpět do vstupního pole, tak uživatel ví, že byl oprav. (Uživatel může předcházet text se překlad vztahuje s pomlčka k potlačení kontroly pravopisu.)

Pokud uživatel nebyl zadali jakýkoli text, nebo pokud na a z jazyků jsou stejné, je nutné žádné překlad. V takovém případě jsme brání vytvoření požadavku.

Kód k provedení této žádosti překlad by měla vypadat povědomě. Jsme sestavení identifikátor URI, vytvořit žádost, odešle a analyzovat odpověď. K zobrazení textu, jsme ho uložit `TranslatedTextLabel` ovládacího prvku.

Jsme předat text, který `Translate` rozhraní API v serializovaných pole JSON v textu požadavku POST. Pole JSON, které může obsahovat více částí textu přeložit, ale zde jsme právě obsahují jeden.

Záhlaví HTTP pojmenované `X-ClientTraceId` je volitelný. Hodnota musí být identifikátor GUID. ID klienta zadaný trasování je užitečné požadavky trace, když věcí nefungují podle očekávání. Být užitečné, ale musí být hodnota X ClientTraceID zaznamenána klientem. ID trasování klienta a datum požadavky pomůžete společnosti Microsoft diagnostikovat problémy, které mohou nastat.

> [!NOTE]
> Tento kurz se zaměřuje na službu Microsoft Translator, aby naše dokumentace nepokrývá `DetectLanguage()` a `CorrectSpelling()` metody podrobně. Analýza textu a kontrola pravopisu Bing služby zadejte odpovědi ve formátu JSON, nikoli XML a analýza textu vyžaduje, že požadavek také formátovat jako JSON. Tyto charakteristiky účtu pro většinu kódu rozdíly z metod, které jste už viděli.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Referenční dokumentace rozhraní API služby Microsoft překladač textu](http://docs.microsofttranslator.com/text-translate.html)
