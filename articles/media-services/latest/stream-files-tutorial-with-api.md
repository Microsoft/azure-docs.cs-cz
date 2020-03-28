---
title: Nahrávání, kódování a streamování pomocí mediálních služeb v3
titleSuffix: Azure Media Services
description: Kurz, který ukazuje, jak nahrát soubor, kódovat video a streamovat obsah pomocí Azure Media Services v3.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: tutorial
ms.custom: mvc
ms.date: 03/22/2019
ms.author: juliako
ms.openlocfilehash: 4e40d26e392219fb751328bc54855d87e80bae19
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80345998"
---
# <a name="tutorial-upload-encode-and-stream-videos-with-media-services-v3"></a>Kurz: Nahrávání, kódování a streamování videí pomocí Mediálních služeb v3

> [!NOTE]
> I když tento kurz používá příklady [sady .NET SDK,](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.liveevent?view=azure-dotnet) obecné kroky jsou stejné pro [rozhraní REST API](https://docs.microsoft.com/rest/api/media/liveevents), ROZHRANÍ [CLI](https://docs.microsoft.com/cli/azure/ams/live-event?view=azure-cli-latest)nebo jiné podporované [sady SDK](media-services-apis-overview.md#sdks).

Azure Media Services umožňuje kódovat mediální soubory do formátů, které se přehrávají v široké škále prohlížečů a zařízení. Například můžete chtít svůj obsah streamovat ve formátu Apple HLS nebo MPEG DASH. Před streamováním je vhodné soubor digitálního média ve vysoké kvalitě zakódovat. Nápovědu k kódování najdete v tématu [Koncept kódování](encoding-concept.md). V tomto kurzu se nahraje místní soubor videa a nahraný soubor se zakóduje. Můžete také zakódovat obsah, který zpřístupníte prostřednictvím adresy URL HTTPS. Další informace najdete v článku o [vytvoření vstupu úlohy z adresy URL protokolu HTTP(S)](job-input-from-http-how-to.md).

![Přehrání videa pomocí programu Azure Media Player](./media/stream-files-tutorial-with-api/final-video.png)

V tomto kurzu získáte informace o následujících postupech:

> [!div class="checklist"]
> * Stáhněte si ukázkovou aplikaci popsanou v tématu.
> * Zkontrolujte kód, který odesílá, kóduje a streamuje.
> * Spusťte aplikaci.
> * Otestujte adresu URL streamování.
> * Vyčistěte prostředky.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

- Pokud nemáte nainstalovaný Visual Studio, můžete získat [Visual Studio Community 2017](https://www.visualstudio.com/thank-you-downloading-visual-studio/?sku=Community&rel=15).
- [Vytvořte účet mediálních služeb](create-account-cli-how-to.md).<br/>Nezapomeňte si pamatovat hodnoty, které jste použili pro název skupiny prostředků a název účtu Mediální služby.
- Postupujte podle kroků v [rozhraní Access Azure Media Services API s rozhraním příkazového příkazového příkazu k Síti Azure](access-api-cli-how-to.md) a uložte přihlašovací údaje. Budete je muset použít pro přístup k rozhraní API.

## <a name="download-and-set-up-the-sample"></a>Stažení a nastavení ukázky

Klonujte úložiště GitHub, které má ukázku streamování .NET do vašeho počítače pomocí následujícího příkazu:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```

Ukázka se nachází ve složce [UploadEncodeAndStreamFiles](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/tree/master/AMSV3Tutorials/UploadEncodeAndStreamFiles).

Otevřete [soubor appsettings.json](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/appsettings.json) ve staženém projektu. Nahraďte hodnoty pověřeními, která jste získali z [přístupu k api](access-api-cli-how-to.md).

## <a name="examine-the-code-that-uploads-encodes-and-streams"></a>Kontrola kódu, který provádí nahrávání, kódování a streamování

Tato část popisuje funkce definované v souboru [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs) projektu *UploadEncodeAndStreamFiles*.

Tato ukázka provede následující akce:

1. Vytvoří novou **transformaci** (nejprve zkontroluje, zda zadaná transformace existuje).
2. Vytvoří výstupní **prostředek,** který se používá jako výstup **úlohy**kódování.
3. Vytvořte vstupní **datový zdroj** a nahraje do něj zadaný místní video soubor. Prostředek se použije jako vstup úlohy.
4. Odešle úlohu kódování pomocí vstupního a výstupního, který byl vytvořen.
5. Zkontroluje stav úlohy.
6. Vytvoří **lokátor streamování**.
7. Vytvoří adresy URL pro streamování.

### <a name="start-using-media-services-apis-with-net-sdk"></a><a id="start_using_dotnet" />Začínáme s rozhraním API služby Media Services se sadou .NET SDK

Pokud chcete začít používat rozhraní Media Services API se sadou .NET SDK, musíte vytvořit objekt **AzureMediaServicesClient**. Chcete-li vytvořit objekt, musíte zadat přihlašovací údaje potřebné pro klienta pro připojení k Azure pomocí Azure AD. V kódu, který jste naklonovali na začátku článku, vytvoří funkce **GetCredentialsAsync** objekt ServiceClientCredentials na základě pověření zadaných v místním konfiguračním souboru.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateMediaServicesClient)]

### <a name="create-an-input-asset-and-upload-a-local-file-into-it"></a>Vytvoření vstupního prostředku a nahrání místního souboru do tohoto prostředku

Funkce **CreateInputAsset** vytvoří nový vstupní [datový zdroj](https://docs.microsoft.com/rest/api/media/assets) a nahraje do něj zadaný místní video soubor. Tento **prostředek se** používá jako vstup do úlohy kódování. Ve službě Media Services v3 může být vstupem do **úlohy** **datový zdroj** nebo obsah, který zpřístupníte účtu Mediálních služeb prostřednictvím adres URL HTTPS. Informace o kódování z adresy URL HTTPS najdete v [tomto](job-input-from-http-how-to.md) článku.

Ve službě Media Services v3 slouží k nahrání souborů rozhraní API služby Azure Storage. Následující fragment kódu .NET vám ukáže, jak na to.

Uvedená funkce provede následující akce:

* Vytvoří **datový zdroj**.
* Získá zapisovatelnou [adresu URL SAS](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) do kontejneru datového zdroje [v úložišti](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-dotnet#upload-blobs-to-a-container).

    Pokud používáte funkci [ListContainerSas](https://docs.microsoft.com/rest/api/media/assets/listcontainersas) datového zdroje k získání adres URL SAS, všimněte si, že tato funkce vrátí více adres URL SAS, protože pro každý účet úložiště existují dva klíče účtu úložiště. Účet úložiště má dva klíče, protože umožňuje bezproblémové střídání klíčů účtu úložiště (například změnit jeden při použití druhého a pak začít používat nový klíč a otáčet druhý klíč). 1. Adresa URL SAS představuje klíč úložiště1 a druhý klíč úložiště2.
* Odešle soubor do kontejneru v úložišti pomocí adresy URL SAS.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateInputAsset)]

### <a name="create-an-output-asset-to-store-the-result-of-a-job"></a>Vytvoření výstupního prostředku k uložení výsledku úlohy

Výstup [Asset](https://docs.microsoft.com/rest/api/media/assets) ukládá výsledek úlohy kódování. Projekt definuje funkci **DownloadResults**, která stáhne výsledky z tohoto výstupního prostředku do výstupní složky, kde si je můžete zkontrolovat.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateOutputAsset)]

### <a name="create-a-transform-and-a-job-that-encodes-the-uploaded-file"></a>Vytvoření transformace a úlohy, která nahraný soubor zakóduje

Při kódování nebo zpracování obsahu ve službě Media Services je běžnývzor nastavení kódování jako recept. Potom stačí odeslat **Úlohu**, která tento předpis použije pro video. Odesláním nových pracovních míst pro každé nové video použijete tento recept na všechna videa v knihovně. Recept v Media Services se nazývá **Transformace**. Další informace najdete v tématu [Transformace a úlohy](transform-concept.md). Ukázka popsaná v tomto kurzu definuje předpis, který zakóduje video tak, aby se dalo streamovat na nejrůznějších zařízeních s iOSem a Androidem.

#### <a name="transform"></a>Transformace

Když vytváříte novou instanci [Transformace](https://docs.microsoft.com/rest/api/media/transforms), musíte určit, co má být jejím výstupem. Objekt **TransformOutput** v níže uvedeném kódu je povinný parametr. Každý objekt **TransformOutput** obsahuje **Předvolbu**. **Předvolba** popisuje podrobné pokyny operací zpracování videa nebo zvuku, které se používají ke generování požadovaného objektu **TransformOutput**. Ukázka popsaná v tomto článku používá předdefinovanou předvolbu s názvem **AdaptiveStreaming**. Tato předvolba zakóduje vstupní video na základě vstupního rozlišení a přenosové rychlosti do automaticky generované dvojice přenosová rychlost / rozlišení (tzv. bitrate ladder) a vytvoří soubory ISO MP4 s videem H.264 a zvukem AAC odpovídající jednotlivým dvojicím přenosová rychlost / rozlišení. Informace o této předvolbě najdete v tématu o [automatickém generování dvojic bitrate ladder](autogen-bitrate-ladder.md).

Můžete použít předdefinovanou předvolbu EncoderNamedPreset, nebo si vytvořit vlastní. Další informace najdete v tématu o [postupu přizpůsobení předvoleb kodéru](customize-encoder-presets-how-to.md).

Než začnete vytvářet [transformaci](https://docs.microsoft.com/rest/api/media/transforms), ověřte si nejdřív pomocí metody **Get**, jestli už neexistuje (viz kód níže). Pokud entita v Media Services v3 neexistuje, metoda **Get** vrátí hodnotu **null** (v názvu se nerozlišují malá a velká písmena).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#EnsureTransformExists)]

#### <a name="job"></a>Úloha

Jak je uvedeno výše, objekt [Transformace](https://docs.microsoft.com/rest/api/media/transforms) je předpis a [Úloha](https://docs.microsoft.com/rest/api/media/jobs) je vlastní požadavek na službu Media Services, aby **transformaci** použila na daný vstupní videoobsah nebo zvukový obsah. **Úloha** určuje informace, jako je umístění vstupního videa a umístění pro výstup.

V tomto příkladu je vstupní video nahrané z místního počítače. Pokud se chcete dozvědět, jak kódovat z adresy URL HTTPS, přečtěte si [tento](job-input-from-http-how-to.md) článek.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#SubmitJob)]

### <a name="wait-for-the-job-to-complete"></a>Čekání na dokončení úlohy

Úloze chvíli trvá, než se dokončí, a když k tomu dojde, budete na to pravděpodobně chtít upozornit. Ukázka kódu níže ukazuje, jak dotazování služby pro stav [úlohy](https://docs.microsoft.com/rest/api/media/jobs). Dotazování není doporučeným osvědčeným postupem pro produkční aplikace z důvodu potenciální latence. Pokud se dotazování u některého účtu používá nadměrně, je možné ho omezit. Místo dotazování by vývojáři měli používat službu Event Grid.

Služba Event Grid je navržená pro vysokou dostupnost, konzistentní výkon a dynamické škálování. Díky službě Event Grid můžou vaše aplikace naslouchat událostem a reagovat na ně, ať už pocházejí z kterékoli služby Azure. Události můžou pocházet i z vlastních zdrojů. Jednoduché, reaktivní zpracování událostí založené na protokolu HTTP pomáhá sestavovat efektivní řešení prostřednictvím inteligentního filtrování a směrování událostí.  Další informace najdete v článku [Směrování událostí na vlastní webový koncový bod](job-state-events-cli-how-to.md).

**Úloha** obvykle prochází následujícími stavy: **Naplánováno**, **Ve frontě**, **Zpracovávání** a **Dokončeno** (konečný stav). Pokud během provádění úlohy dojde k chybě, přejde úloha do stavu **Chyba**. Pokud je úloha v procesu zrušení, dostanete **zrušení** a **zrušení,** když je hotovo.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#WaitForJobToFinish)]

### <a name="job-error-codes"></a>Kódy chyb úlohy

Viz [Chybové kódy](https://docs.microsoft.com/rest/api/media/jobs/get#joberrorcode).

### <a name="get-a-streaming-locator"></a>Získejte vyhledávač datových proudů

Po dokončení kódování následuje zpřístupnění videa ve výstupním prostředku, kde je k dispozici klientům pro přehrávání. Můžete ji zpřístupnit ve dvou krocích: nejprve [vytvořte lokátor streamování](https://docs.microsoft.com/rest/api/media/streaminglocators)a za druhé vytvořte adresy URL streamování, které mohou klienti používat.

Proces vytváření **lokátoru streamování se** nazývá publikování. Ve výchozím nastavení je **lokátor streamování** platný ihned po volání rozhraní API a trvá, dokud není odstraněn, pokud nenakonfigurujete volitelný počáteční a koncový čas.

Při vytváření [StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators), budete muset zadat požadovaný **StreamingPolicyName**. V tomto příkladu budete streamovat v jasném (nebo nešifrovaném obsahu), aby se používala předdefinovaná zásada prostého streamování (**PredefinedStreamingPolicy.ClearStreamingOnly).**

> [!IMPORTANT]
> Při použití vlastní [zásady streamování](https://docs.microsoft.com/rest/api/media/streamingpolicies), měli byste navrhnout omezenou sadu těchto zásad pro váš účet Media Service a znovu použít pro streamingLocators vždy, když jsou potřeba stejné možnosti šifrování a protokoly. Váš účet služby Media Service má kvótu pro počet položek zásad streamování. Pro každý lokátor streamování byste neměli vytvářet nové zásady streamování.

Následující kód předpokládá, že voláte funkci s jedinečným lokatorName.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateStreamingLocator)]

Zatímco ukázka v tomto tématu popisuje streamování, můžete použít stejné volání k vytvoření lokátoru streamování pro doručování videa prostřednictvím postupného stahování.

### <a name="get-streaming-urls"></a>Vytvoření adres URL pro streamování

Nyní, když byl vytvořen [lokátor streamování,](https://docs.microsoft.com/rest/api/media/streaminglocators) můžete získat adresy URL streamování, jak je znázorněno v **adrese GetStreamingURL**. Chcete-li vytvořit adresu URL, musíte zřetězit název hostitele [koncového bodu streamování](https://docs.microsoft.com/rest/api/media/streamingendpoints) a cestu **lokátoru streamování.** V této ukázce se používá *výchozí* **koncový bod streamování.** Při prvním vytvoření účtu služby Media Service bude tento *výchozí* **koncový bod streamování** v zastaveném stavu, takže je třeba zavolat **start**.

> [!NOTE]
> V této metodě potřebujete locatorName, který byl použit při vytváření **streamování Lokátor** pro výstupní asset.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#GetStreamingURLs)]

### <a name="clean-up-resources-in-your-media-services-account"></a>Vyčištění prostředků v účtu služby Media Services

Obecně byste měli vyčistit vše kromě objektů, které plánujete znovu použít (obvykle budete znovu používat transformace a budete zachovat StreamingLocators atd.). Pokud chcete, aby byl váš účet po experimentování čistý, odstraňte prostředky, které neplánujete znovu použít. Například následující kód odstraní úlohy:

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CleanUp)]

## <a name="run-the-sample-app"></a>Spuštění ukázkové aplikace

1. Stisknutím kombinace kláves Ctrl+F5 spusťte aplikaci *EncodeAndStreamFiles.*
2. Zkopírujte jednu z adresy URL pro streamování z konzoly.

Tento příklad uvádí adresy URL, které můžete použít k přehrávání videa pomocí různých protokolů:

![Příklad výstupu znázorňujícího adresy URL pro streamované video služby Media Services](./media/stream-files-tutorial-with-api/output.png)

## <a name="test-the-streaming-url"></a>Testování adresy URL pro streamování

Tento článek používá k otestování streamu přehrávač Azure Media Player.

> [!NOTE]
> Pokud se přehrávač hostuje na webu HTTPS, nezapomeňte adresu URL aktualizovat tak, aby obsahovala „https“. 

1. Otevřete webový prohlížeč [https://aka.ms/azuremediaplayer/](https://aka.ms/azuremediaplayer/)a přejděte na .
2. Do pole **URL:** vložte jednu z hodnot adresy URL streamování, které jste získali při spuštění aplikace.
3. Vyberte **možnost Aktualizovat přehrávač**.

Azure Media Player lze použít pro testování, ale neměl by se používat v produkčním prostředí.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud ze skupiny prostředků už žádné prostředky nepotřebujete, včetně účtu služby Media Services a účtu úložiště, které jste vytvořili v tomto kurzu, pak tuto dříve vytvořenou skupinu prostředků odstraňte.

Proveďte následující příkaz příkazového příkazu příkazu příkazu:

```azurecli
az group delete --name amsResourceGroup
```

## <a name="multithreading"></a>Multithreading

Sady Azure Media Services v3 SDK nejsou bezpečné pro přístup z více vláken. Při vývoji vícevláknové aplikace byste měli generovat a používat nový objekt AzureMediaServicesClient na vlákno.

## <a name="ask-questions-give-feedback-get-updates"></a>Ptejte se, podávejte zpětnou vazbu, získejte aktualizace

Podívejte se na článek [komunity Mediálních služeb Azure](media-services-community.md) a podívejte se na různé způsoby, jak můžete klást otázky, poskytovat zpětnou vazbu a získat aktualizace o mediálních službách.

## <a name="next-steps"></a>Další kroky

Teď, když už víte, jak nahrávat, kódovat a streamovat videa, podívejte se na následující článek: 

> [!div class="nextstepaction"]
> [Analýza videí](analyze-videos-tutorial-with-api.md)
