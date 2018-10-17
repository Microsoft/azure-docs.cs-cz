---
title: 'Kurz: Vytvoření aplikace WPF pro Translator Text s využitím C#'
titleSuffix: Azure Cognitive Services
description: V tomto kurzu se dozvíte, jak sestavit aplikaci WPF v jazyce C#, která používá rozhraní Translator Text API k překladu textu, načtení lokalizovaného seznamu podporovaných jazyků a dalším akcím.
services: cognitive-services
author: noellelacharite
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-text
ms.topic: tutorial
ms.date: 07/20/2018
ms.author: nolachar
ms.openlocfilehash: 97660985b275bbe4384acb3fc92be8aaa0b57881
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2018
ms.locfileid: "46123959"
---
# <a name="tutorial-write-a-wpf-application-for-translator-text-using-c35"></a>Kurz: Vytvoření aplikace WPF pro Translator Text s využitím C#

V tomto kurzu vytvoříte interaktivní nástroj pro překlad textu pomocí rozhraní Translator Text API (V3), které je součástí Microsoft Cognitive Services v Azure. Dozvíte se, jak provést tyto akce:

> [!div class="checklist"]
> * Načtení seznamu jazyků, které služba podporuje
> * Překlad uživatelem zadaného textu z jednoho jazyka do druhého

Tato aplikace nabízí také integraci s dvěma dalšími službami Microsoft Cognitive Services.

|||
|-|-|
|[Analýza textu](https://azure.microsoft.com/services/cognitive-services/text-analytics/)|Umožňuje volitelné automatické rozpoznání zdrojového jazyka textu, který se má přeložit.|
|[Kontrola pravopisu Bingu](https://azure.microsoft.com/services/cognitive-services/spell-check/)|U textu, jehož zdrojovým jazykem je angličtina, umožňuje opravy pravopisných chyb, aby byl překlad přesnější.

![[Spuštění programu s kurzem]](media/translator-text-csharp-session.png)

## <a name="prerequisites"></a>Požadavky

Ke spuštění tohoto kódu ve Windows budete potřebovat [Visual Studio 2017](https://www.visualstudio.com/downloads/). (Bude stačit bezplatná verze Community Edition.)

Budete také potřebovat klíče předplatného pro tři služby Azure, které se v tomto programu používají. Klíč pro službu Translator Text lze získat z řídicího panelu Azure. K dispozici je cenová úroveň Free, která umožňuje bezplatný překlad až dvou milionů znaků za měsíc.

Služby Analýza textu a Kontrola pravopisu Bingu nabízejí bezplatné zkušební verze, které si můžete zaregistrovat na stránce [Zkuste služby Cognitive Services](https://azure.microsoft.com/try/cognitive-services/). Předplatné pro kteroukoli službu si můžete vytvořit také prostřednictvím řídicího panelu Azure. Služba Analýza textu má cenovou úroveň Free.

Zdrojový kód pro tento kurz je k dispozici níže. Vaše klíče předplatného je nutné zkopírovat do zdrojového kódu v `MainWindow.xaml.cs` jako proměnné `TEXT_TRANSLATION_API_SUBSCRIPTION_KEY` atd.

> [!IMPORTANT]
> Služba Analýza textu je dostupná ve více oblastech. Identifikátor URI ve zdrojovém kódu v tomto kurzu je pro oblast `westus`, což je oblast používaná pro bezplatné zkušební verze. Pokud máte předplatné v jiné oblasti, aktualizujte tento identifikátor URI odpovídajícím způsobem.

## <a name="source-code"></a>Zdrojový kód

Toto je zdrojový kód pro rozhraní Microsoft Translator Text API. Když chcete tuto aplikaci spustit, zkopírujte zdrojový kód do příslušného souboru v novém projektu WPF v sadě Visual Studio.

### <a name="mainwindowxamlcs"></a>MainWindow.xaml.cs

Toto je soubor kódu, který řídí funkčnost aplikace.

```csharp
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
    /// This WPF application demonstrates the use of the Microsoft Translator Text API to translate a brief text string from
    /// one language to another. The languages are selected from a drop-down menu. The text of the translation is displayed.
    /// The source language may optionally be automatically detected. English text is spell-checked.
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
            // at least show an error dialog if there's an unexpected error
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

            // read and parse JSON response
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

Tento soubor definuje uživatelské rozhraní pro aplikaci, formulář WPF. Pokud chcete navrhnout vlastní verzi formuláře, nebudete potřebovat tento soubor XAML.

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

Služba Microsoft Translator má určitý počet koncových bodů, které poskytují různé funkce překladu. V tomto kurzu se používají tyto:

|||
|-|-|
|`Languages`|Vrátí sadu jazyků, které aktuálně podporují jiné operace rozhraní Translator Text API.|
|`Translate`|Příslušný zdrojový text, kód zdrojového jazyka a kód cílového jazyka. Vrátí překlad zdrojového textu do cílového jazyka.|

## <a name="the-translation-app"></a>Aplikace pro překlad

Uživatelské rozhraní aplikace Translator se vytváří pomocí WPF (Windows Presentation Foundation). Vytvořte nový projekt WPF v sadě Visual Studio pomocí následujícího postupu.

* V nabídce **Soubor** zvolte **Nový > Projekt**.
* V okně Nový projekt otevřete **Nainstalováno > Šablony > Visual C#**. Uprostřed dialogového okna se zobrazí seznam dostupných šablon projektů.
* Zkontrolujte, že v rozevírací nabídce nad seznamem šablon projektů je vybraná položka **.NET Framework 4.5.2**.
* V seznamu šablon projektů klikněte na položku **Aplikace WPF (.NET Framework)**.
* Pomocí polí v dolní části dialogového okna určete název nového projektu a řešení, které ho obsahuje.
* Kliknutím na **OK** vytvořte nový projekt a příslušné řešení.

![[Vytvoření nové aplikace WPF v sadě Visual Studio]](media/translator-text-csharp-new-project.png)

Přidejte do projektu odkazy na následující sestavení rozhraní .NET Framework.

* System.Runtime.Serialization
* System.Web
* System.Web.Extensions

Nainstalujte do projektu také balíček NuGet `Newtonsoft.Json`.

Pak v Průzkumníku řešení vyhledejte soubor `MainWindow.xaml` a otevřete ho. Na začátku je prázdný. Dokončené uživatelské rozhraní by mělo vypadat jako na obrázku, s názvy ovládacích prvků uvedenými modře. Pro ovládací prvky ve vašem uživatelském rozhraní použijte stejné názvy, protože se vyskytují také v kódu.

![[Zobrazení hlavního okna v návrháři sady Visual Studio s názvy ovládacích prvků]](media/translator-text-csharp-xaml.png)

> [!NOTE]
> Zdrojový kód pro tento kurz zahrnuje zdroj XAML pro tento formulář. Tento zdroj můžete vložit do svého projektu, takže nemusíte formulář vytvářet v sadě Visual Studio.

* `FromLanguageComboBox` *(pole se seznamem)* – Zobrazuje seznam jazyků, které Microsoft Translator podporuje pro překlad textu. Uživatel vybere jazyk, ze kterého překládá.
* `ToLanguageComboBox` *(pole se seznamem)* – Zobrazuje stejný seznam jazyků jako `FromComboBox`, ale slouží k výběru jazyka, do kterého uživatel překládá.
* `TextToTranslate` *(textové pole)* – Sem uživatel zadá text, který se má přeložit.
* `TranslateButton` *(tlačítko)* – Když chce uživatel přeložit daný text, klikne na toto tlačítko (nebo stiskne klávesu Enter).
* `TranslatedTextLabel` *(popisek)* – Tady se zobrazí překlad textu daného uživatele.

Pokud vytváříte vlastní verzi tohoto formuláře, nemusí vypadat *úplně přesně* jako ten, který se používá tady. Ujistěte se však, že rozevírací seznamy pro jazyk mají dostatečnou šířku, aby nedocházelo k odříznutí části názvu jazyka.

## <a name="the-mainwindow-class"></a>Třída MainWindow

Do souboru kódu `MainWindow.xaml.cs` se umístí kód, který zajišťuje funkčnost daného programu. Tato akce probíhá ve dvou fázích:

* Po spuštění programu a vytvoření instance `MainWindow` se načte seznam jazyků, které Translator a rozhraní API používají, a naplní se jimi příslušné rozevírací nabídky. Tato úloha se provede jednou, na začátku každé relace.

* Když uživatel klikne na tlačítko **Translate** (Přeložit), načtou se vybrané jazyky a zadaný text daného uživatele a pak se k provedení překladu volá rozhraní `Translate` API. Můžou se volat také další funkce, které určí jazyk textu a před překladem provedou opravy jeho pravopisu.

Podívejte se na začátek této třídy:

```csharp
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
        // at least show an error dialog if there's an unexpected error
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

Dvě členské proměnné, které jsou tady deklarované, uchovávají informace o příslušných jazycích:

|||
|-|-|
|`languageCodes`<br>pole řetězce|Uchovává kódy jazyků. Služba Translator používá k identifikaci jazyků krátké kódy, například `en` pro angličtinu.|
|`languageCodesAndTitles`<br>seřazený slovník|Mapuje popisné názvy z uživatelského rozhraní na krátké kódy používané v rozhraní API. Používá abecední řazení, velká a malá písmena se nerozlišují.|

Prvním kódem, který aplikace spouští, je konstruktor `MainWindow`. Nejprve nastavte metodu `HandleExceptions` jako globální obslužnou rutinu chyb. Pokud se tedy výjimka neošetří, zobrazí se nejméně jedno upozornění na chybu.

Pak zkontrolujte, že všechny klíče předplatného rozhraní API mají délku přesně 32 znaků. Pokud nemají, nejpravděpodobnějším důvodem je, že *někdo* svoje klíče rozhraní API nevložil. V tom případě zobrazte chybovou zprávu a proveďte nápravu. (Pokud je tento test úspěšný, samozřejmě to neznamená, že jsou klíče platné.)

Pokud mají některé klíče aspoň správnou délku, volání metody `InitializeComponent()` způsobí, že uživatelské rozhraní začne pracovat tak, že vyhledá, načte a vytvoří instanci popisu XAML hlavního okna aplikace.

Nakonec nastavte rozevírací nabídky pro jazyk. Tato úloha vyžaduje tři samostatná volání metody, která jsou podrobně popsaná v následujících částech.

## <a name="get-supported-languages"></a>Zjištění podporovaných jazyků

V době psaní tohoto textu služba Microsoft Translator podporovala celkem 61 jazyků, přičemž čas od času se můžou přidávat další. Je tedy vhodné nekódovat podporované jazyky napevno do programu. Místo toho zadejte na službu Translator dotaz, jaké jazyky podporuje. Každý podporovaný jazyk lze přeložit do libovolného jiného podporovaného jazyka.

K načtení seznamu podporovaných jazyků použijte volání rozhraní `Languages` API.

Rozhraní `Languages` API používá volitelný parametr dotazu GET *scope*. Parametr *scope* může mít jednu ze tří hodnot: `translation`, `transliteration` nebo `dictionary`. V tomto kódu se používá hodnota `translation`.

Rozhraní `Languages` API používá také volitelnou hlavičku HTTP `Accept-Language`. Hodnota této hlavičky určuje jazyk, ve kterém se názvy podporovaných jazyků vrátí. Touto hodnotou by měl být jazykový tag BCP 47 ve správném formátu. V tomto kódu se používá hodnota `en`, která načte názvy jazyků v angličtině.

Rozhraní `Languages` API vrací odpověď JSON, která vypadá takto:

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

K tomu, aby kódy jazyků (například `af`) a názvy jazyků (například `Afrikaans`) bylo možné extrahovat, používá tento kód metodu NewtonSoft.Json [JsonConvert.DeserializeObject](https://www.newtonsoft.com/json/help/html/M_Newtonsoft_Json_JsonConvert_DeserializeObject__1.htm).

S využitím těchto základních znalostí vytvořte následující metodu, která zajistí načtení kódů jazyků a jejich názvů.

```csharp
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

Metoda `GetLanguagesForTranslate()` nejprve vytvoří požadavek HTTP. Parametr řetězce dotazu `scope=translation` si vyžádá jenom ty jazyky, které se podporují pro překlad textu. Do hlaviček požadavku se přidá klíč rozhraní Text Translation API. Přidá se hlavička `Accept-Language` s hodnotou `en`, aby se podporované jazyky vrátily v angličtině.

Po dokončení požadavku se odpověď JSON analyzuje a převede na slovník a pak se příslušné kódy jazyků přidají do členské proměnné `languageCodes`. Páry klíč-hodnota, které obsahují kódy jazyků a popisné názvy jazyků, se zkompletují a přidají do členské proměnné `languageCodesAndTitles`. (Rozevírací nabídky ve formuláři zobrazují popisné názvy, ale k požadavku na překlad se vyžadují kódy.)

## <a name="populate-the-language-menus"></a>Naplnění nabídek jazyků

Většina uživatelského rozhraní se definuje v XAML, takže jeho nastavení nevyžaduje kromě volání metody `InitializeComponent()` téměř nic dalšího. Jedinou další věcí, kterou je potřeba udělat, je přidání popisných názvů jazyků do rozevíracích nabídek **Translate from** (Přeložit z jazyka) a **Translate to** (Přeložit do jazyka), což se provede pomocí metody `PopulateLanguageMenus()`.

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

    // set default languages
    FromLanguageComboBox.SelectedItem = "Detect";
    ToLanguageComboBox.SelectedItem = "English";
}
```

Naplnění nabídek je jednoduchá akce, při které se provede iterace s použitím slovníku `languageCodesAndTitles` a přidání jednotlivých klíčů (popisných názvů) do obou nabídek. Po naplnění nabídek je výchozí zdrojový jazyk nastavený na možnost **Detect** (Rozpoznat) (automatické rozpoznání jazyka) a jako výchozí cílový jazyk je nastavená možnost **English** (angličtina).

> [!TIP]
> Pokud by se nevybraly výchozí hodnoty pro nabídky, mohlo by se stát, že uživatel klikne na **Translate** (Přeložit), aniž by napřed zvolil zdrojový a cílový jazyk. Výchozí hodnoty eliminují nutnost zabývat se tímto problémem.

Po inicializaci třídy `MainWindow` a vytvoření uživatelského rozhraní kód počká, než uživatel klikne na tlačítko **Translate** (Přeložit).

## <a name="perform-translation"></a>Provedení překladu

Když uživatel klikne na **Translate** (Přeložit), WPF vyvolá obslužnou rutinu události `TranslateButton_Click()`, která je zobrazená tady:

```csharp
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

Prvním krokem je načtení zdrojového a cílového jazyka a textu, který uživatel zadal, z příslušného formuláře.

Pokud je pro zdrojový jazyk nastavená možnost **Detect** (Rozpoznat), provede se volání metody `DetectLanguage()`, která určí jazyk daného textu. Text může být v jazyce, který rozhraní Translator API nepodporují (rozpoznat lze mnohem více jazyků, než je možné přeložit) nebo který rozhraní API pro analýzu textu neumí rozpoznat. V tom případě se zobrazí zpráva s informací pro uživatele a žádný překlad se nevrátí.

Pokud je zdrojovým jazykem angličtina (nezáleží na tom, jestli zadaná nebo rozpoznaná), zkontrolujte pravopis textu pomocí metody `CorrectSpelling()` a proveďte případné opravy. Opravený text se vloží zpět do vstupního pole, takže uživatel bude vědět, že se daná oprava provedla. (Pokud chce uživatel potlačit opravy pravopisu, může před překládaný text zadat spojovník.)

Pokud uživatel nezadal žádný text nebo pokud je zdrojový jazyk stejný jako cílový jazyk, neprovede se žádný překlad a danému požadavku je možné se vyhnout.

Kód pro provedení požadavku na překlad by měl vypadat povědomě: sestavení identifikátoru URI, vytvoření požadavku, jeho odeslání a analýza odpovědi. Pokud chcete daný text zobrazit, odešlete ho do ovládacího prvku `TranslatedTextLabel`.

V dalším kroku se text předá do rozhraní `Translate` API v serializovaném poli JSON v textu požadavku POST. Pole JSON může obsahovat více částí textu pro překlad, ale povinná je tady jenom jedna část.

Hlavička HTTP s názvem `X-ClientTraceId` je nepovinná. Hodnota by měla být identifikátor GUID. Klientem poskytnuté ID trasování je užitečné k trasování požadavků v případě, že vše nefunguje podle očekávání. Aby však hodnota X-ClientTraceID mohla být užitečná, musí ji klient zaznamenávat. ID trasování klienta a datum požadavků můžou Microsoftu usnadnit diagnostiku problémů, které můžou nastat.

> [!NOTE]
> Tento kurz se zaměřuje na službu Microsoft Translator, a proto metody `DetectLanguage()` a `CorrectSpelling()` nejsou podrobně popsané.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Referenční informace k rozhraní Microsoft Translator Text API](https://docs.microsoft.com/azure/cognitive-services/Translator/reference/v3-0-reference)
