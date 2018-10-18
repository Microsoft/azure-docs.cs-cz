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
ms.date: 10/16/2018
ms.author: juliako
ms.openlocfilehash: 404a40f8949add77153d3fbf53b5c68dfb866128
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2018
ms.locfileid: "49377876"
---
# <a name="tutorial-upload-encode-and-stream-videos-using-apis"></a>Kurz: Nahrávání, kódování a streamování videí pomocí rozhraní API

Služba Media Services umožňuje kódování souborů médií do formátů, které se dají přehrávat v nejrůznějších prohlížečích a zařízeních. Například můžete chtít svůj obsah streamovat ve formátu Apple HLS nebo MPEG DASH. Před streamováním je vhodné soubor digitálního média ve vysoké kvalitě zakódovat. Pokyny ke kódování najdete v tématu [Principy kódování](encoding-concept.md). V tomto kurzu se nahraje místní soubor videa a nahraný soubor se zakóduje. Můžete také zakódovat obsah, který zpřístupníte prostřednictvím adresy URL protokolu HTTPS. Další informace najdete v článku o [vytvoření vstupu úlohy z adresy URL protokolu HTTP(S)](job-input-from-http-how-to.md).

![Přehrávání videa](./media/stream-files-tutorial-with-api/final-video.png)

V tomto kurzu získáte informace o následujících postupech:    

> [!div class="checklist"]
> * Vytvoření účtu Media Services
> * Přístup k rozhraní API služby Media Services
> * Konfigurace ukázkové aplikace
> * Kontrola kódu, který provádí nahrávání, kódování a streamování
> * Spuštění aplikace
> * Testování adresy URL pro streamování
> * Vyčištění prostředků

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

Pokud nemáte nainstalovanou sadu Visual Studio, můžete získat sadu [Visual Studio Community 2017](https://www.visualstudio.com/thank-you-downloading-visual-studio/?sku=Community&rel=15).

## <a name="download-the-sample"></a>Stažení ukázky

Pomocí následujícího příkazu naklonujte do svého počítače úložiště GitHub s ukázkou streamování .NET:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```

Ukázka se nachází ve složce [UploadEncodeAndStreamFiles](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/tree/master/AMSV3Tutorials/UploadEncodeAndStreamFiles).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [media-services-cli-create-v3-account-include](../../../includes/media-services-cli-create-v3-account-include.md)]

[!INCLUDE [media-services-v3-cli-access-api-include](../../../includes/media-services-v3-cli-access-api-include.md)]

## <a name="examine-the-code-that-uploads-encodes-and-streams"></a>Kontrola kódu, který provádí nahrávání, kódování a streamování

Tato část popisuje funkce definované v souboru [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs) projektu *UploadEncodeAndStreamFiles*.

Tato ukázka provede následující akce:

1. Vytvoří novou transformaci (nejprve zkontroluje, jestli zadaná transformace existuje). 
2. Vytvoří výstupní prostředek, který se použije jako výstup úlohy kódování.
3. Vytvoří vstupní prostředek a nahraje do něj zadaný místní soubor videa. Prostředek se použije jako vstup úlohy. 
4. Pomocí vytvořeného vstupu a výstupu úlohu kódování odešle.
5. Zkontroluje stav úlohy.
6. Vytvoří streamovací lokátor (StreamingLocator).
7. Vytvoří adresy URL pro streamování.

### <a name="start-using-media-services-apis-with-net-sdk"></a>Začínáme s rozhraním API služby Media Services se sadou .NET SDK

Pokud chcete začít používat rozhraní Media Services API se sadou .NET SDK, musíte vytvořit objekt **AzureMediaServicesClient**. K vytvoření tohoto objektu, musíte zadat přihlašovací údaje, aby se klient mohl připojit k Azure pomocí Azure AD. V kódu, který jste naklonovali na začátku článku, vytvoří funkce **GetCredentialsAsync** objekt ServiceClientCredentials na základě pověření zadaných v místním konfiguračním souboru. 

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateMediaServicesClient)]

### <a name="create-an-input-asset-and-upload-a-local-file-into-it"></a>Vytvoření vstupního prostředku a nahrání místního souboru do tohoto prostředku 

Funkce **CreateInputAsset** vytvoří nový vstupní [prostředek](https://docs.microsoft.com/rest/api/media/assets) a nahraje do něj zadaný místní videosoubor. Tento prostředek se použije jako vstup ve vaší úloze kódování. Ve službě Media Services v3 můžete jako vstup úlohy použít buď prostředek, nebo obsah, který jste přes adresu URL protokolu HTTPS zpřístupnili ve svém účtu služby Media Services. Postup zakódování obsahu přes adresu URL protokolu HTTPS najdete v [tomto](job-input-from-http-how-to.md) článku.  

Ve službě Media Services v3 slouží k nahrání souborů rozhraní API služby Azure Storage. Následující fragment kódu .NET vám ukáže, jak na to.

Uvedená funkce provede následující akce:

* Vytvoří prostředek. 
* Získá [adresu SAS](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) [kontejneru v úložišti](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-dotnet?tabs=windows#upload-blobs-to-the-container) prostředku s možností zápisu.
* Přes adresu SAS odešle soubor do kontejneru v úložišti.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateInputAsset)]

### <a name="create-an-output-asset-to-store-the-result-of-a-job"></a>Vytvoření výstupního prostředku k uložení výsledku úlohy 

Výstupní [prostředek](https://docs.microsoft.com/rest/api/media/assets) uloží výsledek vaší úlohy kódování. Projekt definuje funkci **DownloadResults**, která stáhne výsledky z tohoto výstupního prostředku do výstupní složky, kde si je můžete zkontrolovat.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateOutputAsset)]

### <a name="create-a-transform-and-a-job-that-encodes-the-uploaded-file"></a>Vytvoření transformace a úlohy, která nahraný soubor zakóduje
Když kódujete nebo zpracováváte obsah v Media Services, kódování se obvykle nastaví jako předpis. Potom stačí odeslat **Úlohu**, která tento předpis použije pro video. Když odešlete novou Úlohu pro každé nové video, použije se tento předpis pro všechna videa ve vaší knihovně. V Media Services se pro předpis používá označení **transformace**. Další informace najdete v tématu [Transformace a úlohy](transform-concept.md). Ukázka popsaná v tomto kurzu definuje předpis, který zakóduje video tak, aby se dalo streamovat na nejrůznějších zařízeních s iOSem a Androidem. 

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

**Úloha** obvykle prochází následujícími stavy: **Naplánováno**, **Ve frontě**, **Zpracovávání** a **Dokončeno** (konečný stav). Pokud během provádění úlohy dojde k chybě, přejde úloha do stavu **Chyba**. Když úlohu zrušíte, změní se její stav na **Rušení** a potom na **Zrušeno**.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#WaitForJobToFinish)]

### <a name="get-a-streaminglocator"></a>Vytvoření streamovacího lokátoru

Po dokončení kódování následuje zpřístupnění videa ve výstupním prostředku, kde je k dispozici klientům pro přehrávání. Video můžete zpřístupnit ve dvou krocích: nejdřív vytvořte streamovací lokátor ([StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators)) a pak adresy URL pro streamování, které budou klienti používat. 

Proces vytváření **streamovacího lokátoru** označujeme jako publikování. Pokud nenakonfigurujete volitelný počáteční a koncový čas, je **streamovací lokátor** ve výchozím nastavení platný hned po zavolání rozhraní API a jeho platnost zrušíte až jeho odstraněním. 

Když vytváříte [streamovací lokátor](https://docs.microsoft.com/rest/api/media/streaminglocators), je potřeba zadat požadovaný název zásad streamování (**StreamingPolicyName**). V tomto příkladu budete streamovat čistý nebo také nešifrovaný obsah, takže použijete předdefinovanou zásadu čistého streamování **PredefinedStreamingPolicy.ClearStreamingOnly**.

> [!IMPORTANT]
> Pokud chcete definovat vlastní [zásady streamování](https://docs.microsoft.com/rest/api/media/streamingpolicies), doporučujeme navrhnout pro účet služby Media Service omezený počet takovýchto zásad a používat je opakovaně pro streamovací lokátory, kdykoli potřebujete stejné protokoly a možnosti šifrování. Počet záznamů StreamingPolicy je pro účty služby Media Service omezený kvótou. Neměli byste vytvářet samostatnou zásadu streamování pro každý streamovací lokátor.

Následující kód předpokládá, že funkci voláte s jedinečným názvem lokátoru.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateStreamingLocator)]

I když se ukázka v tomto tématu vztahuje na streamování, stejné volání můžete použít i k vytvoření streamovacího lokátoru pro doručování videoobsahu formou progresivního stahování.

### <a name="get-streaming-urls"></a>Vytvoření adres URL pro streamování

Teď, když máte vytvořený [streamovací lokátor](https://docs.microsoft.com/rest/api/media/streaminglocators), můžete vytvořit adresy URL pro streamování, jak to znázorňuje metoda **GetStreamingURLs** v níže uvedeném příkladu. Pokud chcete vytvořit adresu URL, musíte zřetězit název hostitele [koncového bodu hostování](https://docs.microsoft.com/rest/api/media/streamingendpoints) a cestu **streamovacího lokátoru**. V této ukázce je použit *výchozí* **koncový bod streamování**. Když poprvé vytvoříte účet Media Service, tento *výchozí* **koncový bod streamování** bude v zastaveném stavu, proto je potřeba zavolat **spuštění**.

> [!NOTE]
> V této metodě budete potřebovat název lokátoru, který jste použili při vytváření **streamovacího lokátoru** pro výstupní prostředek.

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

Pokud ze skupiny prostředků už žádné prostředky nepotřebujete, včetně účtu služby Media Services a účtu úložiště, které jste vytvořili v tomto kurzu, pak tuto dříve vytvořenou skupinu prostředků odstraňte. Můžete použít nástroj **CloudShell**.

V nástroji **CloudShell** spusťte následující příkaz:

```azurecli-interactive
az group delete --name amsResourceGroup
```

## <a name="multithreading"></a>Multithreading

Sady SDK služby Azure Media Services v3 nejsou bezpečné pro přístup z více vláken. Při vývoji vícevláknové aplikace byste měli pro každé vlákno vygenerovat a používat samostatný objekt AzureMediaServicesClient.

## <a name="next-steps"></a>Další kroky

Teď, když už víte, jak nahrávat, kódovat a streamovat videa, podívejte se na následující článek: 

> [!div class="nextstepaction"]
> [Analýza videí](analyze-videos-tutorial-with-api.md)
