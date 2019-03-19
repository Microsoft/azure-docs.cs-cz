---
title: 'Kurz: Moderování videa a záznamy o studiu v rozhraní .NET – Content Moderator'
titlesuffix: Azure Cognitive Services
description: Tento kurz vám pomůže porozumět jak sestavit kompletní řešení moderování videa a přepis moderování s podporou počítače a vytvoření revize lidských v the smyčky.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: tutorial
ms.date: 03/11/2019
ms.author: pafarley
ms.openlocfilehash: 504f79186eb69fb6e6c23c1a0cd9dfd7584bb128
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/18/2019
ms.locfileid: "57904333"
---
# <a name="tutorial-video-and-transcript-moderation"></a>Kurz: Moderování videa a přepis

V tomto kurzu se dozvíte, jak sestavit kompletní řešení moderování videa a přepis moderování s podporou počítače a vytvoření revize lidských v the smyčky.

V tomto kurzu získáte informace o následujících postupech:

> [!div class="checklist"]
> - Komprese vstupních videí pro rychlejší zpracování
> - Moderování videa k získání snímků s poznatky
> - Použití časových razítek snímků k vytvoření miniatur (obrázků)
> - Odeslání časových razítek a miniatur k vytvoření kontrol videa
> - Převod řeči ve videu na text (přepis) pomocí rozhraní API funkce Media Indexer
> - Moderování přepisu pomocí služby moderování textu
> - Přidání moderovaného přepisu do kontroly videa

## <a name="prerequisites"></a>Požadavky

- Zaregistrovat [nástroj pro kontrolu Content Moderator](https://contentmoderator.cognitive.microsoft.com/) webu a vytvořte vlastní značky. Zobrazit [použití značek](Review-Tool-User-Guide/tags.md) Pokud potřebujete pomoc s tímto krokem.

    ![snímek obrazovky s vlastní značky moderování videa](images/video-tutorial-custom-tags.png)
- Spuštění ukázkové aplikace, potřebujete účet Azure, prostředek služby Azure Media Services, prostředek Azure Content Moderator a přihlašovacích údajů Azure Active Directory. Pokyny o tom, jak získat tyto najdete v tématu [rozhraní API pro moderování videa](video-moderation-api.md) průvodce.
- Stáhněte si [Video revize konzolovou aplikaci](https://github.com/MicrosoftContentModerator/VideoReviewConsoleApp) z Githubu.

## <a name="enter-credentials"></a>Zadat pověření

Upravit `App.config` a přidejte název klienta služby Active Directory, koncové body služby a klíče předplatného indikován `#####`. Potřebujete tyto informace:

    |Klíč|Popis|
    |-|-|
    |`AzureMediaServiceRestApiEndpoint`|Koncový bod pro rozhraní API služby Azure Media Services (AMS)|
    |`ClientSecret`|Klíč předplatného pro Azure Media Services|
    |`ClientId`|ID klienta pro Azure Media Services|
    |`AzureAdTenantName`|Název tenanta Active Directory zastupujícího vaši organizaci|
    |`ContentModeratorReviewApiSubscriptionKey`|Klíč předplatného pro rozhraní API pro kontrolu v Content Moderatoru|
    |`ContentModeratorApiEndpoint`|Další informace o rozhraní API Content Moderatoru|
    |`ContentModeratorTeamId`|Vytvoření týmového ID Content Moderatoru|

## <a name="examine-the-main-code"></a>Prozkoumejte hlavní kód

Třída `Program` v souboru `Program.cs` je hlavní vstupní bod do aplikace moderování videa.

### <a name="methods-of-program-class"></a>Metody třídy Program

|Metoda|Popis|
|-|-|
|`Main`|Analyzuje příkazový řádek, shromáždí vstup uživatele a spustí zpracování.|
|`ProcessVideo`|Zkomprimuje, nahraje, moderuje a vytvoří kontroly videa.|
|`CreateVideoStreamingRequest`|Vytvoří datový proud pro nahrání videa.|
|`GetUserInputs`|Shromáždí vstup uživatele. Používá se, když nejsou k dispozici žádné možnosti příkazového řádku.|
|`Initialize`|Inicializuje objekty potřebné pro proces moderování.|

### <a name="the-main-method"></a>Metoda Main

`Main()` je místo, kde se zahajuje spuštění, je to tedy místo, kde je dobré začít s vysvětlením procesu moderování videa.

[!code-csharp[Main](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/Program.cs?range=20-24,42-52,54-74)]

`Main()` zpracuje následující argumenty příkazového řádku:

- Cesta k adresáři obsahujícímu videosoubory MPEG-4 k odeslání pro moderování. Všechny soubory `*.mp4` v tomto adresáři a v jeho podadresářích se odešlou k moderování.
- Volitelně můžete použít příznak logické hodnoty (true/false) určující, jestli se mají pro účely moderování zvuku generovat přepisy textu.

Pokud nejsou přítomné žádné argumenty příkazového řádku, `Main()` zavolá metodu `GetUserInputs()`. Tato metoda vyzve uživatele, aby zadal cestu k jedinému souboru videa a určil, jestli se má vygenerovat přepis textu.

> [!NOTE]
> Konzolová aplikace používá [rozhraní API služby Azure Media Indexer](https://docs.microsoft.com/azure/media-services/media-services-process-content-with-indexer2) ke generování přepisů z nahrané zvukové stopy videa. Výsledky jsou k dispozici ve formátu WebVTT. Další informace o tomto formátu najdete v tématu [Formáty sledování textu webového videa](https://developer.mozilla.org/en-US/docs/Web/API/WebVTT_API).

### <a name="initialize-and-processvideo-methods"></a>Metody Initialize a ProcessVideo

Bez ohledu na to, jestli pochází možnosti programu z příkazového řádku nebo z interaktivního uživatelského vstupu, zavolá dále `Main()` metodu `Initialize()` k vytvoření následujících instancí:

|Třída|Popis|
|-|-|
|`AMSComponent`|Komprese videosouborů před jejich odesláním pro moderování|
|`AMSconfigurations`|Převod na rozhraní konfiguračních dat aplikace, která se nachází v souboru `App.config`|
|`VideoModerator`| Nahrávání, kódování, šifrování a moderování pomocí AMS SDK|
|`VideoReviewApi`|Správa kontrol videa ve službě Content Moderator|

Tyto třídy (s výjimkou `AMSConfigurations`, která je jednoduchá) jsou podrobněji popsané v následujících částech tohoto kurzu.

Nakonec se videosoubory postupně zpracují tak, že se pro každý z nich zavolá `ProcessVideo()`.

[!code-csharp[ProcessVideo](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/Program.cs?range=76-118)]

Metoda `ProcessVideo()` je poměrně jednoduchá. V uvedeném pořadí provádí následující operace:

- Komprimuje video.
- Nahraje video do prostředku Azure Media Services.
- Vytvoří úlohu AMS pro moderování videa.
- Vytvoří kontrolu videa v Content Moderatoru.

V následujících částech probereme podrobněji některé z jednotlivých procesů vyvolaných metodou `ProcessVideo()`. 

## <a name="compress-the-video"></a>Komprimovat videa

Aby se minimalizoval síťový provoz, převede aplikace videosoubory na formát H.264 (MPEG-4 AVC) a škáluje je na maximální šířku 640 pixelů. Doporučujeme použít kodek H.264, protože je velmi výkonný (rychlost komprese). Komprese se provádí pomocí bezplatného nástroje příkazového řádku `ffmpeg`, který je součástí řešení sady Visual Studio ve složce `Lib`. Vstupní soubory můžou mít kterýkoliv z formátů, které podporuje `ffmpeg`, včetně nejčastěji používaných formátů a kodeků videosouborů.

> [!NOTE]
> Když spouštíte program pomocí možností příkazového řádku, zadáváte adresář obsahující videosoubory k odeslání pro moderování. Zpracují se všechny soubory v tomto adresáři, které mají příponu názvu souboru `.mp4`. Pokud chcete zpracovat další přípony názvů souborů, aktualizujte metodu `Main()` v souboru `Program.cs` tak, aby zahrnovala požadované přípony.

Kód, který komprimuje jediný soubor videa, je třída `AmsComponent` v souboru `AMSComponent.cs`. Metoda, která odpovídá za tuto funkci, je tady uvedená metoda `CompressVideo()`.

[!code-csharp[CompressVideo](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/AMSComponent.cs?range=31-59)]

Kód provede následující kroky:

- Zkontroluje, že konfigurace v souboru `App.config` obsahuje všechna nutná data.
- Zkontroluje, že je přítomný binární soubor `ffmpeg`.
- Vytvoří název výstupního souboru přidáním `_c.mp4` k základnímu názvu souboru (například `Example.mp4` -> `Example_c.mp4`).
- Vytvoří řetězec příkazového řádku k provedení konverze.
- S použitím příkazového řádku spustí proces `ffmpeg`.
- Čeká na video ke zpracování.

> [!NOTE]
> Pokud víte, že už jsou videa komprimovaná pomocí H.264 a že mají odpovídající velikosti, můžete `CompressVideo()` přepsat a kompresi přeskočit.

Metoda vrátí název komprimovaného výstupního souboru.

## <a name="upload-and-moderate-the-video"></a>Nahrání a moderování videa

Než bude možné video zpracovat ve službě Content Moderation, musí být uložené ve službě Azure Media Services. Třída `Program` v souboru `Program.cs` má krátkou metodu `CreateVideoStreamingRequest()`, která vrací objekt, který reprezentuje žádost o streamování sloužící k nahrání videa.

[!code-csharp[CreateVideoStreamingRequest](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/Program.cs?range=120-133)]

Výsledný objekt `UploadVideoStreamRequest` je definovaný v souboru `UploadVideoStreamRequest.cs` (a jeho nadřazený objekt `UploadVideoRequest` v souboru `UploadVideoRequest.cs`). Tyto třídy tady nevidíte. Jsou krátké a slouží pouze k uložení komprimovaných dat videa a informací o něm. Jiná pouze datová třída `UploadAssetResult` (`UploadAssetResult.cs`) se používá k ukládání výsledků procesu nahrávání. Teď je možné vysvětlit tyto řádky v metodě `ProcessVideo()`:

[!code-csharp[ProcessVideoSnippet](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/Program.cs?range=91-104)]

Tyto řádky provedou následující úlohy:

- Vytvoří `UploadVideoStreamRequest` k odeslání komprimovaného videa.
- Pokud uživatel požaduje přepis textu, nastaví příznak žádosti `GenerateVTT`.
- Zavolá metodu `CreateAzureMediaServicesJobToModerateVideo()`, aby se nahrál a získal výsledek.

## <a name="examine-video-moderation-code"></a>Zkoumání kódu moderování videa

Metoda `CreateAzureMediaServicesJobToModerateVideo()` je v souboru `VideoModerator.cs`, kde je velká část kódu, který komunikuje se službou Azure Media Services. Zdrojový kód metody můžete vidět v následujícím výtažku.

[!code-csharp[CreateAzureMediaServicesJobToModerateVideo](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/VideoModerator.cs?range=230-283)]

Tento kód provádí následující úlohy:

- Vytvoří úlohu AMS pro zpracování, které se má provést.
- Přidá úlohy pro kódování souboru videa, jeho moderování a generování přepisu textu.
- Odešle úlohu, nahraje soubor a zahájí zpracování.
- Načte výsledky moderování, text přepisu (pokud se požaduje) a další informace.

## <a name="sample-video-moderation-output"></a>Ukázkový výstup moderování videa

Výsledek úlohy moderování videa (viz [rychlý start moderování videa](video-moderation-api.md) je datová struktura JSON obsahující výsledky moderování. V těchto výsledcích vidíte v rámci videa rozdělení do fragmentů (snímků), z nichž každý obsahuje události (klipy) s klíčovými snímky označenými příznakem pro kontrolu. Každý klíčový snímek má skóre označující pravděpodobnost, že je na něm obsah pro dospělé nebo pikantní obsah. Následující příklad ukazuje odpověď JSON:

```json
{
    "version": 2,
    "timescale": 90000,
    "offset": 0,
    "framerate": 50,
    "width": 1280,
    "height": 720,
    "totalDuration": 18696321,
    "fragments": [
    {
        "start": 0,
        "duration": 18000
    },
    {
        "start": 18000,
        "duration": 3600,
        "interval": 3600,
        "events": [
        [
        {
            "reviewRecommended": false,
            "adultScore": 0.00001,
            "racyScore": 0.03077,
            "index": 5,
            "timestamp": 18000,
            "shotIndex": 0
        }
        ]
    ]
    },
    {
        "start": 18386372,
        "duration": 119149,
        "interval": 119149,
        "events": [
        [
        {
            "reviewRecommended": true,
            "adultScore": 0.00000,
            "racyScore": 0.91902,
            "index": 5085,
            "timestamp": 18386372,
            "shotIndex": 62
        }
    ]
    ]
    }
]
}
```

Přepis zvuku z videa se vytvoří také při nastavení příznaku `GenerateVTT`.

> [!NOTE]
> Konzolová aplikace používá [rozhraní API služby Azure Media Indexer](https://docs.microsoft.com/azure/media-services/media-services-process-content-with-indexer2) ke generování přepisů z nahrané zvukové stopy videa. Výsledky jsou k dispozici ve formátu WebVTT. Další informace o tomto formátu najdete v tématu [Formáty sledování textu webového videa](https://developer.mozilla.org/en-US/docs/Web/API/WebVTT_API).

## <a name="create-a-the-human-in-the-loop-review"></a>Vytvoření kontrola lidských v the smyčky

Proces moderování vrátí seznam klíčových snímků z videa společně s přepisem zvukových stop. Dalším krokem je vytvoření kontroly v Nástroji pro kontrolu Content Moderatoru pro moderování prováděné lidmi. Po návratu k metodě `ProcessVideo()` v souboru `Program.cs` vidíte, že se volá metoda `CreateVideoReviewInContentModerator()`. Tato metoda je ve třídě `videoReviewApi`, která je v souboru `VideoReviewAPI.cs`, a je ukázaná tady.

[!code-csharp[CreateVideoReviewInContentModerator](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/VideoReviewAPI.cs?range=42-69)]

`CreateVideoReviewInContentModerator()` volá několik dalších metod k provedení následujících úloh:

> [!NOTE]
> Konzolová aplikace použije knihovnu [FFmpeg](https://ffmpeg.org/) ke generování miniatur. Tyto miniatury (obrázky) odpovídají časovým razítkům snímků ve [výstupu moderování videa](#sample-video-moderation-response).

|Úkol|Metody|File|
|-|-|-|
|Extrakce klíčových snímků z videa a vytvoření miniatur z těchto snímků|`CreateVideoFrames()`<br>`GenerateFrameImages()`|`FrameGeneratorServices.cs`|
|Kontrola přepisu textu, pokud je k dispozici, pro účely vyhledání obsahu pro dospělé nebo pikantního obsahu|`GenerateTextScreenProfanity()`| `VideoReviewAPI.cs`|
|Příprava a odeslání žádosti o kontrolu videa pro kontrolu člověkem|`CreateReviewRequestObject()`<br> `ExecuteCreateReviewApi()`<br>`CreateAndPublishReviewInContentModerator()`|`VideoReviewAPI.cs`|

Na následujícím obrázku vidíte výsledky předchozích kroků.

![Výchozí zobrazení kontroly videa](images/video-tutorial-default-view.PNG)

## <a name="process-the-transcript"></a>Proces přepisu

Dosud se kód uvedený v tomto kurzu zaměřoval na vizuální obsah. Kontrola mluveného obsahu je samostatný a volitelný proces, který (jak už bylo zmíněno) používá přepis vygenerovaný ze zvuku. Je čas podívat se, jak se vytváří přepisy textu a jak se používají při procesu kontroly. Úloha generování přepisu spadá pod službu [Azure Media Indexer](https://docs.microsoft.com/azure/media-services/media-services-index-content).

Aplikace provádí následující úlohy:

|Úkol|Metody|File|
|-|-|-|
|Určení, jestli se mají generovat přepisy textu|`Main()`<br>`GetUserInputs()`|`Program.cs`|
|Pokud ano, odeslání úlohy přepisu jako součásti moderování|`ConfigureTranscriptTask()`|`VideoModerator.cs`|
|Získání místní kopie přepisu|`GenerateTranscript()`|`VideoModerator.cs`|
|Označení snímků z videa, které obsahují nevhodný zvukový záznam příznakem|`GenerateTextScreenProfanity()`<br>`TextScreen()`|`VideoReviewAPI.cs`|
|Přidání výsledků do kontroly|`UploadScreenTextResult()`<br>`ExecuteAddTranscriptSupportFile()`|`VideoReviewAPI.cs`|

### <a name="task-configuration"></a>Konfigurace úlohy

Přejděme rovnou k odeslání úlohy přepisu. Metoda `CreateAzureMediaServicesJobToModerateVideo()` (popsaná výše) zavolá `ConfigureTranscriptTask()`.

[!code-csharp[ConfigureTranscriptTask](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/VideoModerator.cs?range=295-304)]

Konfigurace úlohy přepisu se přečte ze souboru `MediaIndexerConfig.json` v rámci složky `Lib` řešení. Vytvoří se prostředky AMS pro konfigurační soubor a pro výstup procesu přepisu. Úloha AMS vytvoří po spuštění přepis textu ze zvukové stopy videosouboru.

> [!NOTE]
> Ukázková aplikace rozpoznává řeč jenom v americké angličtině.

### <a name="transcript-generation"></a>Generování přepisu

Přepis se publikuje jako prostředek AMS. Pokud chcete prohledat přepis, jestli neobsahuje nevhodný obsah, stáhne aplikace tento prostředek ze služby Azure Media Services. `CreateAzureMediaServicesJobToModerateVideo()` zavolá `GenerateTranscript()`, jak vidíte tady, aby se načetl soubor.

[!code-csharp[GenerateTranscript](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/VideoModerator.cs?range=351-370)]

Po dokončení některých nezbytných nastavení prostředku AMS se provede vlastní stažení na základě volání `DownloadAssetToLocal()`, obecné funkce, která zkopíruje prostředek AMS do místního souboru.

## <a name="moderate-the-transcript"></a>Střední přepisu

S přepisem po ruce, se prohledá a použije při kontrole. Vytvoření kontroly spadá pod metodu `CreateVideoReviewInContentModerator()`, která k provedení úlohy zavolá `GenerateTextScreenProfanity()`. Tato metoda zase dál volá metodu `TextScreen()`, která obsahuje většinu funkcí.

`TextScreen()` provede následující úlohy:

- Analýza přepisu, která zjistí časová razítka a titulky
- Odeslání jednotlivých titulků k moderování textu
- Použití příznaku u všech snímků, které můžou obsahovat nežádoucí mluvený obsah

Podívejme se na jednotlivé úlohy podrobněji:

### <a name="initialize-the-code"></a>Inicializace kódu

Nejprve inicializujte všechny proměnné a kolekce.

[!code-csharp[TextScreen](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/VideoReviewAPI.cs?range=515-527)]

### <a name="parse-the-transcript-for-captions"></a>Analýza přepisu, která zjišťuje titulky

Dále analyzujte přepis ve formátu WebVTT, jestli obsahuje titulky a časová razítka. Nástroj pro kontrolu zobrazí tyto titulky na kartě přepisu na obrazovce kontroly videa. Časová razítka se používají k synchronizaci titulků s odpovídajícími snímky videí.

[!code-csharp[TextScreen2](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/VideoReviewAPI.cs?range=528-567)]

### <a name="moderate-captions-with-the-text-moderation-service"></a>Moderování titulků pomocí služby moderování textu

Dále zkontrolujeme analyzované textové titulky pomocí rozhraní API pro text v Content Moderatoru.

> [!NOTE]
> Klíč služby Content Moderator má omezenou rychlost v jednotkách RPS (žádosti za sekundu). Když tento limit překročíte, sada SDK vyvolá výjimku s kódem chyby 429.
>
> Klíč úrovně Free má limit nastavený na 1 RPS.

[!code-csharp[TextScreen3](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/VideoReviewAPI.cs?range=568-653)]

### <a name="text-moderation-breakdown"></a>Rozpis moderování textu

`TextScreen()` je důležitá metoda, pojďme si ji tedy rozebrat.

1. Nejdříve tato metoda načte po jednotlivých řádcích soubor přepisu. Ignoruje prázdné řádky a řádky, které obsahují `NOTE` se skóre spolehlivosti. Z *upozornění* v souboru extrahuje časová razítka a textové položky. Upozornění představuje text ze zvukové stopy a zahrnuje počáteční a koncový čas. Upozornění začíná řádkem časového razítka s řetězcem `-->`. Pak následuje jeden nebo více řádků textu.

1. Instance `CaptionScreentextResult` (definované v souboru `TranscriptProfanity.cs`) se používají k uložení informací analyzovaných z jednotlivých upozornění.  Při zjištění řádku nového časového razítka nebo při dosažení maximální délky textu 1024 znaků se do seznamu `csrList` přidá nový výsledek `CaptionScreentextResult`. 

1. Tato metoda dále odešle jednotlivá upozornění do rozhraní API služby Moderování textu. Zavolá `ContentModeratorClient.TextModeration.DetectLanguageAsync()` i `ContentModeratorClient.TextModeration.ScreenTextWithHttpMessagesAsync()` definované v sestavení `Microsoft.Azure.CognitiveServices.ContentModerator`. Aby se neomezila rychlost, udělá metoda před odesláním jednotlivých upozornění sekundovou pauzu.

1. Metoda po přijetí výsledků ze služby Moderování textu tyto výsledky analyzuje a zjišťuje, jestli vyhovují limitům nastaveným pro důvěryhodnost. Tyto hodnoty jsou určené v souboru `App.config` jako `OffensiveTextThreshold`, `RacyTextThreshold` a `AdultTextThreshold`. Nakonec se uloží také samotné problematické termíny. Všechny snímky v rámci časového rozsahu upozornění se označí příznakem, který ukazuje na to, že obsahují urážlivý nebo pikantní text nebo text pro dospělé.

1. `TextScreen()` vrátí instanci `TranscriptScreenTextResult`, která obsahuje výsledek moderování textu z videa jako celku. Tento objekt obsahuje příznaky a skóre pro různé typy problematického obsahu spolu se seznamem všech problematických termínů. Volající metoda `CreateVideoReviewInContentModerator()` zavolá metodu `UploadScreenTextResult()`, aby se tyto informace připojily ke kontrole a měli je k dispozici ti, kdo dělají kontrolu.

Na následujícím obrázku vidíte výsledek generování přepisu a postupu moderování.

![Zobrazení přepisu moderování videa](images/video-tutorial-transcript-view.PNG)

## <a name="program-output"></a>Výstup programu

V následujícím výstupu příkazového řádku z programu vidíte různé úlohy v průběhu jejich dokončení. Kromě toho jsou k dispozici ve stejném adresáři jako původní videosoubory výsledky moderování (ve formátu JSON) a přepis řeči.

```console
Microsoft.ContentModerator.AMSComponentClient
Enter the fully qualified local path for Uploading the video :
"Your File Name.MP4"
Generate Video Transcript? [y/n] : y

Video compression process started...
Video compression process completed...

Video moderation process started...
Video moderation process completed...

Video review process started...
Video Frames Creation inprogress...
Frames(83) created successfully.
Review Created Successfully and the review Id 201801va8ec2108d6e043229ba7a9e6373edec5
Video review successfully completed...

Total Elapsed Time: 00:05:56.8420355
```

## <a name="next-steps"></a>Další postup

V tomto kurzu nastavíte aplikaci, která moderates videoobsahem&mdash;včetně přepisu obsah&mdash;a vytvoří nástroj pro recenze revize. Další informace o podrobnosti o moderování videa v dalším kroku.

> [!div class="nextstepaction"]
> [Moderování videa](./video-moderation-human-review.md)
