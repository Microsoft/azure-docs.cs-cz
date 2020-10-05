---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/04/2020
ms.author: trbye
ms.custom: devx-track-csharp
ms.openlocfilehash: 05cf63f741e287112ace76a5249ae2ca8d01f34f
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "91376929"
---
## <a name="prerequisites"></a>Předpoklady

Než začnete, nezapomeňte:

> [!div class="checklist"]
> * [Nastavení vývojového prostředí a vytvoření prázdného projektu](../../../../quickstarts/setup-platform.md?tabs=dotnet&pivots=programmming-language-csharp)
> * [Vytvoření prostředku Azure Speech](../../../../overview.md#try-the-speech-service-for-free)
> * [Nahrání zdrojového souboru do objektu blob Azure](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)

## <a name="open-your-project-in-visual-studio"></a>Otevřete projekt v aplikaci Visual Studio

Prvním krokem je ujistit se, že máte projekt otevřený v aplikaci Visual Studio.

1. Spusťte Visual Studio 2019.
2. Načtěte projekt a otevřete `Program.cs` .

## <a name="add-a-reference-to-newtonsoftjson"></a>Přidat odkaz na Newtonsoft.Jsna

1. V Průzkumník řešení klikněte pravým tlačítkem myši na projekt **HelloWorld** a pak výběrem **možnosti spravovat balíčky NuGet** Zobrazte správce balíčků NuGet.
1. V pravém horním rohu Najděte rozevírací seznam **zdroj balíčku** a ujistěte se, že **`nuget.org`** je vybraná možnost.
1. V levém horním rohu vyberte **Procházet**.
1. Do vyhledávacího pole zadejte *newtonsoft.js* a vyberte **ENTER**.
1. Ve výsledcích hledání vyberte v balíčku [**Newtonsoft.Js**](https://www.nuget.org/packages/Newtonsoft.Json) a pak vyberte **instalovat** a nainstalujte nejnovější stabilní verzi.
1. Přijetím všech smluv a licencí spusťte instalaci.
   Po instalaci balíčku se v okně **konzoly Správce balíčků** zobrazí potvrzení.

## <a name="start-with-some-boilerplate-code"></a>Začínáme s některým často používaným kódem

Pojďme přidat kód, který funguje jako kostra pro náš projekt.

```csharp
class Program
{
    // Replace with your subscription key
    const string SubscriptionKey = "YourSubscriptionKey";

    // Update with your service region
    const string Region = "YourServiceRegion";
    const int Port = 443;
 
    // Recordings and locale
    const string Locale = "en-US";
    const string RecordingsBlobUri = "YourFileUrl";
 
    // Name and description
    const string Name = "Simple transcription";
    const string Description = "Simple transcription description";
 
    const string SpeechToTextBasePath = "api/speechtotext/v2.0/";
 
    static async Task Main()
    {
        // Cognitive Services follows security best practices.
        // If you experience connectivity issues, see:
        // https://docs.microsoft.com/en-us/dotnet/framework/network-programming/tls
 
        await TranscribeAsync();
    }
 
    static async Task TranscribeAsync()
    {
        Console.WriteLine("Starting transcriptions client...");
    }
}
```

[!INCLUDE [placeholder-replacements](../placeholder-replacement.md)]

## <a name="json-wrappers"></a>Obálky JSON

Jak REST API přijímá požadavky ve formátu JSON a také vrátí výsledky ve formátu JSON, můžeme s nimi pracovat jenom s řetězci, ale to se nedoporučuje.
Aby bylo možné žádosti a odpovědi snadněji spravovat, deklarujeme několik tříd, které se použijí pro serializaci nebo deserializaci JSON.

Pokračujte a uveďte jejich deklarace po `TranscribeAsync` .

```csharp
public class ModelIdentity
{
    ModelIdentity(Guid id) => Id = id;

    public Guid Id { get; private set; }

    public static ModelIdentity Create(Guid Id) => new ModelIdentity(Id);
}

public class Transcription
{
    [JsonConstructor]
    Transcription(
        Guid id,
        string name,
        string description,
        string locale,
        DateTime createdDateTime,
        DateTime lastActionDateTime,
        string status,
        Uri recordingsUrl,
        IReadOnlyDictionary<string, string> resultsUrls)
    {
        Id = id;
        Name = name;
        Description = description;
        CreatedDateTime = createdDateTime;
        LastActionDateTime = lastActionDateTime;
        Status = status;
        Locale = locale;
        RecordingsUrl = recordingsUrl;
        ResultsUrls = resultsUrls;
    }

    public string Name { get; set; }

    public string Description { get; set; }

    public string Locale { get; set; }

    public Uri RecordingsUrl { get; set; }

    public IReadOnlyDictionary<string, string> ResultsUrls { get; set; }

    public Guid Id { get; set; }

    public DateTime CreatedDateTime { get; set; }

    public DateTime LastActionDateTime { get; set; }

    public string Status { get; set; }

    public string StatusMessage { get; set; }
}

public class TranscriptionDefinition
{
    TranscriptionDefinition(
        string name,
        string description,
        string locale,
        Uri recordingsUrl,
        IEnumerable<ModelIdentity> models)
    {
        Name = name;
        Description = description;
        RecordingsUrl = recordingsUrl;
        Locale = locale;
        Models = models;
        Properties = new Dictionary<string, string>
        {
            ["PunctuationMode"] = "DictatedAndAutomatic",
            ["ProfanityFilterMode"] = "Masked",
            ["AddWordLevelTimestamps"] = "True"
        };
    }

    public string Name { get; set; }

    public string Description { get; set; }

    public Uri RecordingsUrl { get; set; }

    public string Locale { get; set; }

    public IEnumerable<ModelIdentity> Models { get; set; }

    public IDictionary<string, string> Properties { get; set; }

    public static TranscriptionDefinition Create(
        string name,
        string description,
        string locale,
        Uri recordingsUrl)
        => new TranscriptionDefinition(name, description, locale, recordingsUrl, new ModelIdentity[0]);
}
```

## <a name="create-and-configure-an-http-client"></a>Vytvoření a konfigurace klienta http
První věc, kterou budeme potřebovat, je klient http, který má správnou základní adresu URL a sadu ověřování.
Vložte tento kód do `TranscribeAsync` .

```csharp
var client = new HttpClient
{
    Timeout = TimeSpan.FromMinutes(25),
    BaseAddress = new UriBuilder(Uri.UriSchemeHttps, $"{Region}.cris.ai", Port).Uri,
    DefaultRequestHeaders =
    {
        { "Ocp-Apim-Subscription-Key", SubscriptionKey }
    }
};
```

## <a name="generate-a-transcription-request"></a>Generovat žádost o přepis
Dále vygenerujeme žádost o přepis. Přidejte tento kód do `TranscribeAsync` .

```csharp
var transcriptionDefinition =
    TranscriptionDefinition.Create(
        Name,
        Description,
        Locale,
        new Uri(RecordingsBlobUri));

var res = JsonConvert.SerializeObject(transcriptionDefinition);
var sc = new StringContent(res);
sc.Headers.ContentType = JsonMediaTypeFormatter.DefaultMediaType;
```

## <a name="send-the-request-and-check-its-status"></a>Odeslat žádost a ověřit její stav
Nyní pošleme požadavek službě rozpoznávání řeči a zkontrolujeme kód prvotní odezvy. Tento kód odpovědi jednoduše indikuje, jestli služba požadavek přijala. Služba vrátí adresu URL v hlavičkách odpovědi, kde je umístění, kde bude uložený stav přepisu.

```csharp
Uri transcriptionLocation = null;
using (var response = await client.PostAsync($"{SpeechToTextBasePath}Transcriptions/", sc))
{
    if (!response.IsSuccessStatusCode)
    {
        Console.WriteLine("Error {0} starting transcription.", response.StatusCode);
        return;
    }

    transcriptionLocation = response.Headers.Location;
}
```

## <a name="wait-for-the-transcription-to-complete"></a>Počkejte, až se přepis dokončí.
Vzhledem k tomu, že služba zpracovává přepis asynchronně, musíme dotazovat se na jeho stav, a to v každém případě často. Zkontrolujeme každých 5 sekund.

Stav můžeme zjistit načtením obsahu na adrese URL, kterou jsme dostali při odeslání žádosti. Když získáme obsah zpátky, deserializovatme ho do jedné z našich pomocných tříd, aby bylo snazší pracovat s.

Toto je kód pro cyklické dotazování se zobrazením stavu pro vše s výjimkou úspěšného dokončení. provedeme to ještě dál.

```csharp
Console.WriteLine($"Created transcription at location {transcriptionLocation}.");
Console.WriteLine("Checking status.");

var completed = false;

// Check for the status of our transcriptions periodically
while (!completed)
{
    Transcription transcription = null;
    using (var response = await client.GetAsync(transcriptionLocation.AbsolutePath))
    {
        var contentType = response.Content.Headers.ContentType;
        if (response.IsSuccessStatusCode &&
            string.Equals(contentType.MediaType, "application/json", StringComparison.OrdinalIgnoreCase))
        {
            transcription = await response.Content.ReadAsAsync<Transcription>();
        }
        else
        {
            Console.WriteLine("Error with status {0} getting transcription result", response.StatusCode);
            continue;
        }
    }

    switch (transcription.Status)
    {
        case "Failed":
            completed = true;
            Console.WriteLine("Transcription failed. Status: {0}", transcription.StatusMessage);
            break;

        case "Succeeded":
            break;

        case "Running":
            Console.WriteLine("Transcription is still running.");
            break;

        case "NotStarted":
            Console.WriteLine("Transcription has not started.");
            break;
    }

    await Task.Delay(TimeSpan.FromSeconds(5));
}

Console.WriteLine("Press any key...");
Console.ReadKey();
```

## <a name="display-the-transcription-results"></a>Zobrazení výsledků přepisu
Po úspěšném dokončení přepisu budou výsledky uloženy v jiné adrese URL, kterou můžeme získat z odpovědi na stav. Tady vytvoříme požadavek na stažení těchto výsledků do dočasného souboru před čtením a deserializací.
Po načtení výsledků je můžete vytisknout do konzoly. Do popisku přidejte následující kód `case "Succeeded":` .

```csharp
completed = true;
var webClient = new WebClient();
var filename = Path.GetTempFileName();
webClient.DownloadFile(transcription.ResultsUrls["channel_0"], filename);
var results = File.ReadAllText(filename);
Console.WriteLine($"Transcription succeeded. Results: {Environment.NewLine}{results}");
File.Delete(filename);
```

## <a name="check-your-code"></a>Kontrolovat kód
V tomto okamžiku by váš kód měl vypadat takto: (do této verze jsme přidali nějaké komentáře.)

```csharp
using Newtonsoft.Json;
using System;
using System.Collections.Generic;
using System.IO;
using System.Net;
using System.Threading.Tasks;
using System.Net.Http;
using System.Net.Http.Formatting;

namespace BatchClient
{
    class Program
    {
        // Replace with your subscription key
        const string SubscriptionKey = "YourSubscriptionKey";

        // Update with your service region
        const string Region = "YourServiceRegion";
        const int Port = 443;

        // Recordings and locale
        const string Locale = "en-US";
        const string RecordingsBlobUri = "YourFileUrl";

        // Name and description
        const string Name = "Simple transcription";
        const string Description = "Simple transcription description";

        const string SpeechToTextBasePath = "api/speechtotext/v2.0/";

        static async Task Main()
        {
            // For non-Windows 10 users.
            ServicePointManager.SecurityProtocol = SecurityProtocolType.Tls12;

            await TranscribeAsync();
        }

        static async Task TranscribeAsync()
        {
            Console.WriteLine("Starting transcriptions client...");

            // Create the client object and authenticate
            var client = new HttpClient
            {
                Timeout = TimeSpan.FromMinutes(25),
                BaseAddress = new UriBuilder(Uri.UriSchemeHttps, $"{Region}.cris.ai", Port).Uri,
                DefaultRequestHeaders =
                {
                    { "Ocp-Apim-Subscription-Key", SubscriptionKey }
                }
            };

            var transcriptionDefinition =
                TranscriptionDefinition.Create(
                    Name,
                    Description,
                    Locale,
                    new Uri(RecordingsBlobUri));

            var res = JsonConvert.SerializeObject(transcriptionDefinition);
            var sc = new StringContent(res);
            sc.Headers.ContentType = JsonMediaTypeFormatter.DefaultMediaType;

            Uri transcriptionLocation = null;

            using (var response = await client.PostAsync($"{SpeechToTextBasePath}Transcriptions/", sc))
            {
                if (!response.IsSuccessStatusCode)
                {
                    Console.WriteLine("Error {0} starting transcription.", response.StatusCode);
                    return;
                }

                transcriptionLocation = response.Headers.Location;
            }

            Console.WriteLine($"Created transcription at location {transcriptionLocation}.");
            Console.WriteLine("Checking status.");

            var completed = false;

            // Check for the status of our transcriptions periodically
            while (!completed)
            {
                Transcription transcription = null;

                // Get all transcriptions for the user
                using (var response = await client.GetAsync(transcriptionLocation.AbsolutePath))
                {
                    var contentType = response.Content.Headers.ContentType;
                    if (response.IsSuccessStatusCode &&
                        string.Equals(contentType.MediaType, "application/json", StringComparison.OrdinalIgnoreCase))
                    {
                        transcription = await response.Content.ReadAsAsync<Transcription>();
                    }
                    else
                    {
                        Console.WriteLine("Error with status {0} getting transcription result", response.StatusCode);
                        continue;
                    }
                }

                // For each transcription in the list we check the status
                switch (transcription.Status)
                {
                    case "Failed":
                        completed = true;
                        Console.WriteLine("Transcription failed. Status: {0}", transcription.StatusMessage);
                        break;

                    case "Succeeded":
                        completed = true;
                        var webClient = new WebClient();
                        var filename = Path.GetTempFileName();
                        webClient.DownloadFile(transcription.ResultsUrls["channel_0"], filename);
                        var results = File.ReadAllText(filename);
                        Console.WriteLine($"Transcription succeeded. Results: {Environment.NewLine}{results}");
                        File.Delete(filename);
                        break;

                    case "Running":
                        Console.WriteLine("Transcription is still running.");
                        break;

                    case "NotStarted":
                        Console.WriteLine("Transcription has not started.");
                        break;
                }

                await Task.Delay(TimeSpan.FromSeconds(5));
            }

            Console.WriteLine("Press any key...");
            Console.ReadKey();
        }
    }

    public class ModelIdentity
    {
        ModelIdentity(Guid id) => Id = id;

        public Guid Id { get; private set; }

        public static ModelIdentity Create(Guid Id) => new ModelIdentity(Id);
    }

    public class Transcription
    {
        [JsonConstructor]
        Transcription(
            Guid id,
            string name,
            string description,
            string locale,
            DateTime createdDateTime,
            DateTime lastActionDateTime,
            string status,
            Uri recordingsUrl,
            IReadOnlyDictionary<string, string> resultsUrls)
        {
            Id = id;
            Name = name;
            Description = description;
            CreatedDateTime = createdDateTime;
            LastActionDateTime = lastActionDateTime;
            Status = status;
            Locale = locale;
            RecordingsUrl = recordingsUrl;
            ResultsUrls = resultsUrls;
        }

        public string Name { get; set; }

        public string Description { get; set; }

        public string Locale { get; set; }

        public Uri RecordingsUrl { get; set; }

        public IReadOnlyDictionary<string, string> ResultsUrls { get; set; }

        public Guid Id { get; set; }

        public DateTime CreatedDateTime { get; set; }

        public DateTime LastActionDateTime { get; set; }

        public string Status { get; set; }

        public string StatusMessage { get; set; }
    }

    public class TranscriptionDefinition
    {
        TranscriptionDefinition(
            string name,
            string description,
            string locale,
            Uri recordingsUrl,
            IEnumerable<ModelIdentity> models)
        {
            Name = name;
            Description = description;
            RecordingsUrl = recordingsUrl;
            Locale = locale;
            Models = models;
            Properties = new Dictionary<string, string>
            {
                ["PunctuationMode"] = "DictatedAndAutomatic",
                ["ProfanityFilterMode"] = "Masked",
                ["AddWordLevelTimestamps"] = "True"
            };
        }

        public string Name { get; set; }

        public string Description { get; set; }

        public Uri RecordingsUrl { get; set; }

        public string Locale { get; set; }

        public IEnumerable<ModelIdentity> Models { get; set; }

        public IDictionary<string, string> Properties { get; set; }

        public static TranscriptionDefinition Create(
            string name,
            string description,
            string locale,
            Uri recordingsUrl)
            => new TranscriptionDefinition(name, description, locale, recordingsUrl, new ModelIdentity[0]);
    }
}
```

## <a name="build-and-run-your-app"></a>Sestavení a spuštění aplikace

Nyní jste připraveni sestavit aplikaci a otestovat rozpoznávání řeči pomocí služby Speech.

1. **Zkompilujte kód** -z panelu nabídek v aplikaci Visual Studio, vyberte **sestavení**  >  **řešení**sestavení.
2. **Spusťte aplikaci** – z řádku nabídek zvolte **ladění**  >  **Spustit ladění** nebo stiskněte klávesu **F5**.
3. **Spustit rozpoznávání** – zobrazí výzvu k vymluvenému vynechání fráze v angličtině. Váš hlas se odešle službě Speech, přepisu jako text a vykreslí se v konzole nástroje.

## <a name="next-steps"></a>Další kroky

[!INCLUDE [footer](./footer.md)]
