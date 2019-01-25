---
title: Nahrávání, kódování a streamování pomocí služby Azure Media Services | Microsoft Docs
description: Tento kurz popisuje, jak nahrát soubor, zakódovat video a streamovat obsah v Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: tutorial
ms.custom: mvc
ms.date: 01/23/2019
ms.author: juliako
ms.openlocfilehash: 051de9b68b6cf830592a7cf8bdad7808e044fbcc
ms.sourcegitcommit: b4755b3262c5b7d546e598c0a034a7c0d1e261ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2019
ms.locfileid: "54888707"
---
# <a name="tutorial-upload-encode-and-stream-videos-using-apis"></a>Kurz: Nahrávání, kódování a streamování videí pomocí rozhraní API

Azure Media Services umožňuje kódování souborů médií do formátů, které můžete přehrát na širokou škálu prohlížečů a zařízení. Například můžete chtít svůj obsah streamovat ve formátu Apple HLS nebo MPEG DASH. Před streamováním je vhodné soubor digitálního média ve vysoké kvalitě zakódovat. Pokyny ke kódování najdete v tématu [Principy kódování](encoding-concept.md). V tomto kurzu se nahraje místní soubor videa a nahraný soubor se zakóduje. Můžete také zakódovat obsah, který zpřístupníte prostřednictvím adresy URL protokolu HTTPS. Další informace najdete v článku o [vytvoření vstupu úlohy z adresy URL protokolu HTTP(S)](job-input-from-http-how-to.md).

![Přehrávání videa](./media/stream-files-tutorial-with-api/final-video.png)

V tomto kurzu získáte informace o následujících postupech:    

> [!div class="checklist"]
> * Přístup k rozhraní API služby Media Services
> * Konfigurace ukázkové aplikace
> * Kontrola kódu, který provádí nahrávání, kódování a streamování
> * Spuštění aplikace
> * Testování adresy URL pro streamování
> * Vyčištění prostředků

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

- Pokud nemáte nainstalovanou sadu Visual Studio, můžete získat sadu [Visual Studio Community 2017](https://www.visualstudio.com/thank-you-downloading-visual-studio/?sku=Community&rel=15).
- Nainstalovat a používat rozhraní příkazového řádku místně, musíte mít Azure CLI verze 2.0 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli). 

    V současné době všechny [Media Services v3 CLI](https://aka.ms/ams-v3-cli-ref) příkazy fungují ve službě Azure Cloud Shell. Doporučujeme používat rozhraní příkazového řádku místně.

- [Vytvoření účtu Media Services](create-account-cli-how-to.md).

    Ujistěte se, že hodnoty, které jste použili pro název skupiny prostředků a název účtu Media Services mějte na paměti

## <a name="download-the-sample"></a>Stažení ukázky

Pomocí následujícího příkazu naklonujte do svého počítače úložiště GitHub s ukázkou streamování .NET:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```

Ukázka se nachází ve složce [UploadEncodeAndStreamFiles](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/tree/master/AMSV3Tutorials/UploadEncodeAndStreamFiles).

[!INCLUDE [media-services-v3-cli-access-api-include](../../../includes/media-services-v3-cli-access-api-include.md)]

## <a name="examine-the-code-that-uploads-encodes-and-streams"></a>Kontrola kódu, který provádí nahrávání, kódování a streamování

Tato část popisuje funkce definované v souboru [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs) projektu *UploadEncodeAndStreamFiles*.

Tato ukázka provede následující akce:

1. Vytvoří novou **transformace** (nejprve, zkontroluje, jestli existuje Zadaná transformace). 
2. Vytvoří výstup **Asset** , který se používá jako kódování **úlohy**výstup.
3. Vytvoření vstupní **Asset** a nahraje zadaného místního souboru videa do něj. Prostředek se použije jako vstup úlohy. 
4. Odešle úlohy kódování pomocí vstup a výstup, který byl vytvořen.
5. Zkontroluje stav úlohy.
6. Vytvoří **Lokátor streamování**.
7. Vytvoří adresy URL pro streamování.

### <a name="a-idstartusingdotnet-start-using-media-services-apis-with-net-sdk"></a><a id="start_using_dotnet" />Začněte používat rozhraní API služby Media Services pomocí sady .NET SDK

Pokud chcete začít používat rozhraní Media Services API se sadou .NET SDK, musíte vytvořit objekt **AzureMediaServicesClient**. K vytvoření tohoto objektu, musíte zadat přihlašovací údaje, aby se klient mohl připojit k Azure pomocí Azure AD. V kódu, který jste naklonovali na začátku článku, vytvoří funkce **GetCredentialsAsync** objekt ServiceClientCredentials na základě pověření zadaných v místním konfiguračním souboru. 

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateMediaServicesClient)]

### <a name="create-an-input-asset-and-upload-a-local-file-into-it"></a>Vytvoření vstupního prostředku a nahrání místního souboru do tohoto prostředku 

Funkce **CreateInputAsset** vytvoří nový vstupní [prostředek](https://docs.microsoft.com/rest/api/media/assets) a nahraje do něj zadaný místní videosoubor. To **Asset** se používá jako vstup pro úlohu kódování. V Media Services v3, vstup **úlohy** může být buď **Asset**, nebo to může být obsah, který je k dispozici pro váš účet Media Services prostřednictvím adresy URL HTTPS. Postup zakódování obsahu přes adresu URL protokolu HTTPS najdete v [tomto](job-input-from-http-how-to.md) článku.  

Ve službě Media Services v3 slouží k nahrání souborů rozhraní API služby Azure Storage. Následující fragment kódu .NET vám ukáže, jak na to.

Uvedená funkce provede následující akce:

* Vytvoří **Asset** 
* Získá zapisovatelný [adresy URL SAS](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) do assetu [kontejneru ve službě storage](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-dotnet?tabs=windows#upload-blobs-to-the-container)
* Přes adresu SAS odešle soubor do kontejneru v úložišti.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateInputAsset)]

### <a name="create-an-output-asset-to-store-the-result-of-a-job"></a>Vytvoření výstupního prostředku k uložení výsledku úlohy 

Výstupní [prostředek](https://docs.microsoft.com/rest/api/media/assets) uloží výsledek vaší úlohy kódování. Projekt definuje funkci **DownloadResults**, která stáhne výsledky z tohoto výstupního prostředku do výstupní složky, kde si je můžete zkontrolovat.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateOutputAsset)]

### <a name="create-a-transform-and-a-job-that-encodes-the-uploaded-file"></a>Vytvoření transformace a úlohy, která nahraný soubor zakóduje

Když kódujete nebo zpracováváte obsah v Media Services, kódování se obvykle nastaví jako předpis. Potom stačí odeslat **Úlohu**, která tento předpis použije pro video. Odešlete nové úlohy pro každé nové video, můžete se má použít tento předpisu pro všechna videa v knihovně. V Media Services se pro předpis používá označení **transformace**. Další informace najdete v tématu [transformuje a úlohy](transform-concept.md). Ukázka popsaná v tomto kurzu definuje předpis, který zakóduje video tak, aby se dalo streamovat na nejrůznějších zařízeních s iOSem a Androidem. 

#### <a name="transform"></a>Transformace

Když vytváříte novou instanci [Transformace](https://docs.microsoft.com/rest/api/media/transforms), musíte určit, co má být jejím výstupem. Objekt **TransformOutput** v níže uvedeném kódu je povinný parametr. Každý objekt **TransformOutput** obsahuje **Předvolbu**. **Předvolba** popisuje podrobné pokyny operací zpracování videa nebo zvuku, které se používají ke generování požadovaného objektu **TransformOutput**. Ukázka popsaná v tomto článku používá předdefinovanou předvolbu s názvem **AdaptiveStreaming**. Tato předvolba zakóduje vstupní video na základě vstupního rozlišení a přenosové rychlosti do automaticky generované dvojice přenosová rychlost / rozlišení (tzv. bitrate ladder) a vytvoří soubory ISO MP4 s videem H.264 a zvukem AAC odpovídající jednotlivým dvojicím přenosová rychlost / rozlišení. Informace o této předvolbě najdete v tématu o [automatickém generování dvojic bitrate ladder](autogen-bitrate-ladder.md).

Můžete použít předdefinovanou předvolbu EncoderNamedPreset, nebo si vytvořit vlastní. Další informace najdete v tématu o [postupu přizpůsobení předvoleb kodéru](customize-encoder-presets-how-to.md).

Než začnete vytvářet [transformaci](https://docs.microsoft.com/rest/api/media/transforms), ověřte si nejdřív pomocí metody **Get**, jestli už neexistuje (viz kód níže).  Pokud entita v Media Services v3 neexistuje, metoda **Get** vrátí hodnotu **null** (v názvu se nerozlišují malá a velká písmena).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#EnsureTransformExists)]

#### <a name="job"></a>Úloha

Jak je uvedeno výše, objekt [Transformace](https://docs.microsoft.com/rest/api/media/transforms) je předpis a [Úloha](https://docs.microsoft.com/rest/api/media/jobs) je vlastní požadavek na službu Media Services, aby **transformaci** použila na daný vstupní videoobsah nebo zvukový obsah. **Úloha** určuje informace, jako je umístění vstupního videa a umístění pro výstup.

V tomto příkladu je vstupní video nahrané z místního počítače. Postup zakódování obsahu přes adresu URL protokolu HTTPS najdete v [tomto](job-input-from-http-how-to.md) článku.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#SubmitJob)]

### <a name="wait-for-the-job-to-complete"></a>Čekání na dokončení úlohy

Úloze chvíli trvá, než se dokončí, a když k tomu dojde, budete na to pravděpodobně chtít upozornit. Následující ukázka kódu je příkladem toho, jak se ve službě dotazovat na stav [úlohy](https://docs.microsoft.com/rest/api/media/jobs). Dotazování nepatří mezi doporučené postupy, jak sestavovat aplikace, protože může mít prodlevu. Pokud se dotazování u některého účtu používá nadměrně, je možné ho omezit. Místo dotazování by vývojáři měli používat službu Event Grid.

Služba Event Grid je navržená pro vysokou dostupnost, konzistentní výkon a dynamické škálování. Díky službě Event Grid můžou vaše aplikace naslouchat událostem a reagovat na ně, ať už pocházejí z kterékoli služby Azure. Události můžou pocházet i z vlastních zdrojů. Jednoduché, reaktivní zpracování událostí založené na protokolu HTTP pomáhá sestavovat efektivní řešení prostřednictvím inteligentního filtrování a směrování událostí.  Další informace najdete v článku [Směrování událostí na vlastní webový koncový bod](job-state-events-cli-how-to.md).

**Úlohy** obvykle prochází následujících stavů: **Naplánované**, **ve frontě**, **zpracování**, **dokončeno** (konečný stav). Pokud během provádění úlohy dojde k chybě, přejde úloha do stavu **Chyba**. Když úlohu zrušíte, změní se její stav na **Rušení** a potom na **Zrušeno**.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#WaitForJobToFinish)]

### <a name="get-a-streaming-locator"></a>Získat Lokátor streamování

Po dokončení kódování následuje zpřístupnění videa ve výstupním prostředku, kde je k dispozici klientům pro přehrávání. Můžete to provést ve dvou krocích: nejdřív vytvořte [Lokátor streamování](https://docs.microsoft.com/rest/api/media/streaminglocators)a druhý, sestavení adresy URL pro streamování, které můžou klienti používat. 

Proces vytváření **Lokátor streamování** nazývá publikování. Ve výchozím nastavení **Lokátor streamování** platnost okamžitě po provedení volání rozhraní API a trvá, dokud je odstraníme, pokud nenakonfigurujete volitelné počáteční a koncový čas. 

Když vytváříte [streamovací lokátor](https://docs.microsoft.com/rest/api/media/streaminglocators), je potřeba zadat požadovaný název zásad streamování (**StreamingPolicyName**). V tomto příkladu budete streamovat čistý nebo také nešifrovaný obsah, takže použijete předdefinovanou zásadu čistého streamování **PredefinedStreamingPolicy.ClearStreamingOnly**.

> [!IMPORTANT]
> Při použití vlastního [streamování zásad](https://docs.microsoft.com/rest/api/media/streamingpolicies), by měly omezenou sadu zásad návrhu pro svůj účet Media Service a znovu je použít pro vaše StreamingLocators pokaždé, když jsou potřeba stejné možnosti šifrování a protokoly. Kvóta pro počet položek streamování zásady, které má váš účet Media Service. By neměl být vytváření nových zásad streamování pro každý Lokátor streamování.

Následující kód předpokládá, že funkci voláte s jedinečným názvem lokátoru.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateStreamingLocator)]

Během ukázky v tomto tématu popisuje streamování, můžete použít stejné volání Pokud chcete vytvořit lokátor streamování pro doručování videa přes progresivní stahování.

### <a name="get-streaming-urls"></a>Vytvoření adres URL pro streamování

Teď, když [Lokátor streamování](https://docs.microsoft.com/rest/api/media/streaminglocators) byl vytvořen, můžete získat adresy URL pro streamování, jak je znázorněno v **GetStreamingURLs**. Sestavit adresu URL, je nutné zřetězit [koncový bod streamování](https://docs.microsoft.com/rest/api/media/streamingendpoints) název hostitele a **Lokátor streamování** cestu. V této ukázce *výchozí* **koncový bod streamování** se používá. Při prvním vytvoření účtů Media Service, to *výchozí* **koncový bod streamování** budou v zastaveném stavu, takže je potřeba volat **Start**.

> [!NOTE]
> V této metodě, je třeba locatorName, který jste použili při vytváření **Lokátor streamování** pro výstup Asset.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#GetStreamingURLs)]

### <a name="clean-up-resources-in-your-media-services-account"></a>Vyčištění prostředků v účtu služby Media Services

Obecně platí, že byste měli vyčistit všechno kromě objektů, které máte v plánu použít znovu, (obvykle jsou to transformace, streamovací lokátory apod.). Pokud chcete účet po experimentování vyčistit, měli byste odstranit prostředky, které nemáte v plánu znovu použít.  Následující kód například odstraní Úlohy.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CleanUp)]

## <a name="run-the-sample-app"></a>Spuštění ukázkové aplikace

1. Stisknutím kombinace kláves Ctrl+F5 spusťte aplikaci *EncodeAndStreamFiles*.
2. Zkopírujte jednu z adresy URL pro streamování z konzoly.

Tento příklad uvádí adresy URL, které můžete použít k přehrávání videa pomocí různých protokolů:

![Výstup](./media/stream-files-tutorial-with-api/output.png)

## <a name="test-the-streaming-url"></a>Testování adresy URL pro streamování

Tento článek používá k otestování streamu přehrávač Azure Media Player. 

> [!NOTE]
> Pokud se přehrávač hostuje na webu HTTPS, nezapomeňte adresu URL aktualizovat tak, aby obsahovala „https“. 

1. Otevřete webový prohlížeč a přejděte na adresu [https://aka.ms/azuremediaplayer/](https://aka.ms/azuremediaplayer/).
2. Do pole **URL:** vložte jednu z hodnot adres URL pro streamování, které jste získali při spuštění aplikace. 
3. Stiskněte **Update Player** (Aktualizovat přehrávač).

Azure Media Player můžete použít pro účely testování, nesmí se ale používat v produkčním prostředí. 

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud ze skupiny prostředků už žádné prostředky nepotřebujete, včetně účtu služby Media Services a účtu úložiště, které jste vytvořili v tomto kurzu, pak tuto dříve vytvořenou skupinu prostředků odstraňte.

Spusťte následující příkaz rozhraní příkazového řádku:

```azurecli
az group delete --name amsResourceGroup
```

## <a name="multithreading"></a>Multithreading

Sady SDK služby Azure Media Services v3 nejsou bezpečné pro přístup z více vláken. Při vývoji vícevláknové aplikace byste měli pro každé vlákno vygenerovat a používat samostatný objekt AzureMediaServicesClient.

## <a name="next-steps"></a>Další postup

Teď, když už víte, jak nahrávat, kódovat a streamovat videa, podívejte se na následující článek: 

> [!div class="nextstepaction"]
> [Analýza videí](analyze-videos-tutorial-with-api.md)
