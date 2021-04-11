---
title: Analýza videí pomocí Media Services V3
description: Naučte se analyzovat videa pomocí Azure Media Services.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: tutorial
ms.date: 03/22/2021
ms.author: inhenkel
ms.openlocfilehash: f9c52544160e52c29b2d0e53aa957b40017dc8df
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104870473"
---
# <a name="tutorial-analyze-videos-with-media-services-v3"></a>Kurz: analýza videí pomocí Media Services V3

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Tento kurz vám ukáže, jak analyzovat video pomocí služby Azure Media Services. Existuje mnoho případů, ve kterých můžete potřebovat získat podrobnější přehled o zaznamenaném video nebo audio obsahu. Pokud chtějí organizace například dosáhnout vyšší spokojenosti zákazníků, mohou převést záznamy řeči zákaznické podpory na text a vytvořit prohledávatelný katalog s rejstříky a řídicími panely.

V tomto kurzu získáte informace o následujících postupech:

> [!div class="checklist"]
> * Stáhněte si ukázkovou aplikaci popsanou v tématu.
> * Projděte si kód, který analyzuje zadané video.
> * Spusťte aplikaci.
> * Prohlédněte si výstup.
> * Vyčistěte prostředky.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="compliance-privacy-and-security"></a>Dodržování předpisů, ochrana osobních údajů a zabezpečení
 
Jako důležité připomenutí musíte dodržovat všechny použitelné zákony při používání Video Indexer. Nemusíte používat Video Indexer ani žádnou jinou službu Azure způsobem, který porušuje práva ostatních. Před nahráním videí, včetně jakýchkoli biometrických dat, do služby Video Indexer pro zpracování a ukládání, musíte mít všechna patřičná práva, včetně všech příslušných souhlasů, od jednotlivců ve videu. Pokud se chcete dozvědět o dodržování předpisů, ochraně osobních údajů a zabezpečení v Video Indexer, [podmínky Azure Cognitive Services](https://azure.microsoft.com/support/legal/cognitive-services-compliance-and-privacy/). Zásady ochrany osobních údajů od Microsoftu a jejich zpracování najdete v [prohlášení o zásadách ochrany osobních údajů](https://privacy.microsoft.com/PrivacyStatement)od Microsoftu, na základě [podmínek pro online služby](https://www.microsoft.com/licensing/product-licensing/products) (OST) a v [dodatku pro zpracování dat](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=67) (DPA). Další informace o ochraně osobních údajů, včetně uchovávání, odstranění nebo zničení dat, jsou k dispozici v OST a [zde](../video-indexer/faq.md). Pomocí Video Indexer souhlasíte s tím, že jste vázáni Cognitive Services podmínky, OST, DPA a prohlášení o zásadách ochrany osobních údajů.

## <a name="prerequisites"></a>Požadavky

- Pokud nemáte nainstalovanou aplikaci Visual Studio, Získejte [Visual Studio Community 2019](https://www.visualstudio.com/thank-you-downloading-visual-studio/?sku=Community&rel=15).
- [Vytvořte účet Media Services](./create-account-howto.md).<br/>Nezapomeňte si pamatovat hodnoty, které jste použili pro název skupiny prostředků a název účtu Media Services.
- Postupujte podle kroků v [části přístup k rozhraní API Azure Media Services pomocí Azure CLI](./access-api-howto.md) a přihlašovací údaje uložte. Budete je muset použít pro přístup k rozhraní API.

## <a name="download-and-configure-the-sample"></a>Stažení a konfigurace ukázky

Do svého počítače naklonujte pomocí následujícího příkazu úložiště GitHub obsahující ukázku .NET:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```

Tato ukázka se nachází ve složce [AnalyzeVideos](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/tree/master/AMSV3Tutorials/AnalyzeVideos).

Ve staženém projektu otevřete [appsettings.js](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/AnalyzeVideos/appsettings.json) . Nahraďte hodnoty přihlašovacími údaji, které jste získali při [přístupu k rozhraním API](./access-api-howto.md).

## <a name="examine-the-code-that-analyzes-the-specified-video"></a>Kontrola kódu, který analyzuje dané video

Tato část prozkoumává funkce definované v souboru [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/AnalyzeVideos/Program.cs) z projektu *AnalyzeVideos*.

Ukázka dokončí následující akce:

1. Vytvoří **transformaci** a **úlohu** , která analyzuje vaše video.
2. Vytvoří vstupní **Asset** a nahraje do něj video. Prostředek se použije jako vstup úlohy.
3. Vytvoří výstupní prostředek, který ukládá výstup úlohy.
4. Odešle úlohu.
5. Zkontroluje stav úlohy.
6. Stáhne soubory, které jsou výsledkem spuštění úlohy.

### <a name="start-using-media-services-apis-with-net-sdk"></a>Začínáme s rozhraním API služby Media Services se sadou .NET SDK

Pokud chcete začít používat rozhraní Media Services API se sadou .NET SDK, musíte vytvořit objekt **AzureMediaServicesClient**. K vytvoření tohoto objektu, musíte zadat přihlašovací údaje, aby se klient mohl připojit k Azure pomocí Azure AD. V kódu, který jste naklonovali na začátku článku, vytvoří funkce **GetCredentialsAsync** objekt ServiceClientCredentials na základě pověření zadaných v místním konfiguračním souboru. 

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#CreateMediaServicesClient)]

### <a name="create-an-input-asset-and-upload-a-local-file-into-it"></a>Vytvoření vstupního prostředku a nahrání místního souboru do tohoto prostředku 

Funkce **CreateInputAsset** vytvoří nový vstupní [Asset](/rest/api/media/assets) a nahraje zadaný místní videosoubor do souboru. Tento prostředek se použije jako vstup ve vaší úloze kódování. Ve službě Media Services v3 můžete jako vstup úlohy použít buď prostředek, nebo obsah, který jste přes adresu URL protokolu HTTPS zpřístupnili ve svém účtu služby Media Services. Informace o tom, jak kódovat z adresy URL HTTPS, najdete v [tomto](job-input-from-http-how-to.md) článku.  

Ve službě Media Services v3 slouží k nahrání souborů rozhraní API služby Azure Storage. Následující fragment kódu .NET vám ukáže, jak na to.

Následující funkce dokončí tyto akce:

* Vytvoří Asset.
* Získá zapisovatelnou [adresu URL SAS](../../storage/common/storage-sas-overview.md) kontejneru assetu [v úložišti](../../storage/blobs/storage-quickstart-blobs-dotnet.md#upload-blobs-to-a-container).

    Pokud používáte funkci [ListContainerSas](/rest/api/media/assets/listcontainersas) assetu k získání adres URL SAS, Všimněte si, že funkce vrací několik adres URL SAS, protože pro každý účet úložiště jsou k dispozici dva klíče účtu úložiště. Účet úložiště má dva klíče, protože umožňuje plynulé střídání klíčů účtu úložiště (například při použití druhého, zahájení použití nového klíče a střídání druhého klíče). První adresa URL SAS představuje úložiště klíč1 a druhé úložiště key2.
* Nahraje soubor do kontejneru v úložišti pomocí adresy URL SAS.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#CreateInputAsset)]

### <a name="create-an-output-asset-to-store-the-result-of-the-job"></a>Vytvoření výstupního prostředku k uložení výsledku úlohy

Výstupní [Asset](/rest/api/media/assets) ukládá výsledek vaší úlohy. Projekt definuje funkci **DownloadResults**, která stáhne výsledky z tohoto výstupního prostředku do výstupní složky, kde si je můžete zkontrolovat.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#CreateOutputAsset)]

### <a name="create-a-transform-and-a-job-that-analyzes-videos"></a>Vytvoření transformace a úlohy, která analyzuje videa

Při kódování nebo zpracování obsahu v Media Services se jedná o společný vzor pro nastavení kódování jako recept. Potom stačí odeslat **Úlohu**, která tento předpis použije pro video. Když odešlete nové úlohy pro každé nové video, použijete tento recept na všechna videa v knihovně. Recept v Media Services se nazývá **transformace**. Další informace najdete v tématu [transformace a úlohy](./transforms-jobs-concept.md). Ukázka popsaná v tomto kurzu definuje předpis, který analyzuje zadané video.

#### <a name="transform"></a>Transformace

Když vytváříte novou instanci [Transformace](/rest/api/media/transforms), musíte určit, co má být jejím výstupem. **TransformOutput**  je povinný parametr. Každý objekt **TransformOutput** obsahuje **Předvolbu**. **Předvolba** popisuje podrobné pokyny operací zpracování videa a/nebo audia, které se používají ke generování požadovaného objektu **TransformOutput**. V tomto příkladu se používá předvolba **VideoAnalyzerPreset** a jazyk ("en-US") je předán konstruktoru ( `new VideoAnalyzerPreset("en-US")` ). Tato předvolba vám umožňuje z videa extrahovat několik audio a video přehledů. Pokud potřebujete z videa extrahovat pouze několik audio přehledů, můžete použít předvolbu **AudioAnalyzerPreset**.

Při vytváření **transformace** si nejdříve zajistěte, pokud už existuje, pomocí metody **Get** , jak je znázorněno v následujícím kódu. Pokud entita v Media Services v3 neexistuje, metoda **Get** vrátí hodnotu **null** (v názvu se nerozlišují malá a velká písmena).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#EnsureTransformExists)]

#### <a name="job"></a>Úloha

Jak je uvedeno výše, objekt [Transformace](/rest/api/media/transforms) je předpis a [Úloha](/rest/api/media/jobs) je vlastní požadavek na službu Media Services, aby **transformaci** použila na daný vstupní videoobsah nebo zvukový obsah. **Úloha** určuje informace, jako je umístění vstupního videa a umístění výstupu. Umístění videa můžete zadat pomocí adres URL protokolu HTTPS, adres URL podpisu SAS nebo prostředků, které máte ve svém účtu služby Media Services.

V tomto příkladu je vstupem úlohy místní video.  

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#SubmitJob)]

### <a name="wait-for-the-job-to-complete"></a>Čekání na dokončení úlohy

Dokončení úlohy trvá déle. V takovém případě chcete být upozorněni. Existují různé možnosti, jak získat upozornění na dokončení [úlohy](/rest/api/media/jobs) . Nejjednodušší možnost (zobrazená tady) je použití cyklického dotazování.

Cyklické dotazování není doporučeným osvědčeným postupem pro produkční aplikace kvůli možné latenci. Pokud se dotazování u některého účtu používá nadměrně, je možné ho omezit. Místo dotazování by vývojáři měli používat službu Event Grid.

Služba Event Grid je navržená pro vysokou dostupnost, konzistentní výkon a dynamické škálování. Díky službě Event Grid můžou vaše aplikace naslouchat událostem a reagovat na ně, ať už pocházejí z kterékoli služby Azure. Události můžou pocházet i z vlastních zdrojů. Jednoduché, reaktivní zpracování událostí založené na protokolu HTTP pomáhá sestavovat efektivní řešení prostřednictvím inteligentního filtrování a směrování událostí. Další informace najdete v tématu [Směrování událostí do vlastního webového koncového bodu](monitoring/job-state-events-cli-how-to.md).

**Úloha** obvykle prochází následujícími stavy: **Naplánováno**, **Ve frontě**, **Zpracovávání** a **Dokončeno** (konečný stav). Pokud se úloha dokončí v rámci chyby, zobrazí se **chybový** stav. Pokud se úloha právě ruší, vrátíte se **a po dokončení** se **zruší** .

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#WaitForJobToFinish)]

### <a name="job-error-codes"></a>Kódy chyb úlohy

Viz [kódy chyb](/rest/api/media/jobs/get#joberrorcode).

### <a name="download-the-result-of-the-job"></a>Stažení výsledku úlohy

Následující funkce stáhne výsledky z výstupního [prostředku](/rest/api/media/assets) do složky "Output", abyste mohli kontrolovat výsledky úlohy.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#DownloadResults)]

### <a name="clean-up-resources-in-your-media-services-account"></a>Vyčištění prostředků v účtu služby Media Services

[!INCLUDE [clean-up-warning](includes/clean-up-warning.md)]

Obecně byste měli vyčistit vše kromě objektů, které plánujete použít (obvykle budete znovu používat transformaci a trvalé StreamingLocators). Pokud chcete, aby se Váš účet vyčistil po experimentování, odstraňte prostředky, které nechcete znovu použít. Například následující kód odstraní úlohu a výstupní prostředek:

### <a name="delete-resources-with-code"></a>Odstranění prostředků s kódem

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#CleanUp)]

Můžete použít také rozhraní příkazového řádku.

[!INCLUDE [clean-up-resources-cli](includes/clean-up-resources-cli.md)]

## <a name="run-the-sample-app"></a>Spuštění ukázkové aplikace

Stisknutím kombinace kláves CTRL + F5 spusťte aplikaci *AnalyzeVideos* .

Když program spustíte, úloha vytvoří miniatury každého obličeje, který ve videu najde. Zároveň vytvoří soubor insights.json.

## <a name="examine-the-output"></a>Prozkoumání výstupu

Výstupní soubor analyzování videa se nazývá insights.json. Tento soubor obsahuje přehledy o vašem videu. Popis jednotlivých elementů nalezených v souboru JSON získáte v článku [Inteligentní funkce médií](./analyzing-video-audio-files-concept.md).

## <a name="multithreading"></a>Multithreading

> [!WARNING]
> Sady SDK Azure Media Services V3 nejsou bezpečné pro přístup z více vláken. Při práci s aplikacemi s více vlákny byste měli vytvořit nový objekt AzureMediaServicesClient pro každé vlákno.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Kurz: Nahrávání, kódování a streamování souborů](stream-files-tutorial-with-api.md)
