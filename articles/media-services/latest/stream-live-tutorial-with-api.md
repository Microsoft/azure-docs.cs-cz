---
title: Stream za provozu pomocí Azure Media Services v3 pomocí .NET | Dokumentace Microsoftu
description: Tento kurz vás provede postupem živého streamování s Media Services v3 s použitím .NET Core.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 01/28/2019
ms.author: juliako
ms.openlocfilehash: 49598eb8579e20dd20ca63d11529ba106a510102
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55170517"
---
# <a name="tutorial-stream-live-with-media-services-v3-using-net"></a>Kurz: Stream živé pomocí Media Services v3 pomocí .NET

Ve službě Azure Media Services [živé události](https://docs.microsoft.com/rest/api/media/liveevents) zodpovídají za zpracování obsahu živého streamování. Živá událost obsahuje vstupní koncový bod (adresa URL ingestu), pak poskytnete kodér služby live Encoding. Živá událost přijímat živé vstupní datové proudy z live encoder a zpřístupňuje je prostřednictvím jednoho nebo více datových proudů [koncové body streamování](https://docs.microsoft.com/rest/api/media/streamingendpoints). Události v reálném čase také poskytuje koncový bod ve verzi preview (adresa URL náhledu), který používáte k zobrazení náhledu a ověření datového proudu před dalším zpracováním a doručením. Tento kurz ukazuje, jak použít .NET Core k vytvoření **průchozího** typu události v reálném čase. 

> [!NOTE]
> Než budete pokračovat, přečtěte si téma [Živé streamování s Media Services v3](live-streaming-overview.md). 

V tomto kurzu získáte informace o následujících postupech:    

> [!div class="checklist"]
> * Stažení ukázkové aplikace popsané v tématu
> * Kontrola kódu, který provádí živé streamování
> * Sledování události s [Azure Media Player](http://amp.azure.net/libs/amp/latest/docs/index.html) na webu http://ampdemo.azureedge.net
> * Vyčištění prostředků

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

K dokončení kurzu potřebujete následující:

- Instalace sady Visual Studio Code nebo Visual Studio.
- [Vytvoření účtu Media Services](create-account-cli-how-to.md).<br/>Ujistěte se, že hodnoty, které jste použili pro název skupiny prostředků a název účtu Media Services mějte na paměti.
- Postupujte podle kroků v [rozhraní API k přístupu k Azure Media Services pomocí Azure CLI](access-api-cli-how-to.md) a uložte přihlašovací údaje. Je potřeba použít pro přístup k rozhraní API.
- Fotoaparát nebo zařízení (jako je přenosný počítač), které se používá k vysílání události
- Místní kodér pro kódování v reálném čase, který převádí signály z kamery na datové proudy, které se odesílají do služby živého streamování Media Services. Datový proud musí být ve formátu **RTMP** nebo **Smooth Streaming**.

## <a name="download-and-configure-the-sample"></a>Stažení a konfigurace ukázky aplikace

Pomocí následujícího příkazu naklonujte do svého počítače úložiště GitHub s ukázkou streamování .NET:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials.git
 ```

Ukázku živého streamování najdete ve složce [Live](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/tree/master/NETCore/Live/MediaV3LiveApp) (Živé).

Otevřít [appsettings.json](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/Live/MediaV3LiveApp/appsettings.json) stažený projekt. Nahraďte hodnoty s přihlašovacími údaji, které jste získali z [přístup k rozhraní API](access-api-cli-how-to.md).

> [!IMPORTANT]
> Tato ukázka používá pro každý zdroj jedinečnou příponu. Pokud zrušíte ladění nebo ukončit aplikaci bez nutnosti jeho spuštění, skončíte s více živé události ve vašem účtu. <br/>Ujistěte se, že k zastavení spuštěného živé události. V opačném případě se vám za ně budou **účtovat poplatky**.

## <a name="examine-the-code-that-performs-live-streaming"></a>Kontrola kódu, který provádí živé streamování

Tato část prozkoumává funkce definované v souboru [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/Live/MediaV3LiveApp/Program.cs) projektu *MediaV3LiveApp*.

V ukázce se pro každý zdroj vytvoří jedinečná přípona, aby nedocházelo ke kolizím názvů, když ukázku spustíte vícekrát bez vyčištění.

> [!IMPORTANT]
> Tato ukázka používá pro každý zdroj jedinečnou příponu. Pokud zrušíte ladění nebo ukončit aplikaci bez nutnosti jeho spuštění, skončíte s více živé události ve vašem účtu. <br/>
> Ujistěte se, že k zastavení spuštěného živé události. V opačném případě se vám za ně budou **účtovat poplatky**.
 
### <a name="start-using-media-services-apis-with-net-sdk"></a>Začínáme s rozhraním API služby Media Services se sadou .NET SDK

Pokud chcete začít používat rozhraní Media Services API se sadou .NET SDK, musíte vytvořit objekt **AzureMediaServicesClient**. K vytvoření tohoto objektu, musíte zadat přihlašovací údaje, aby se klient mohl připojit k Azure pomocí Azure AD. V kódu, který jste naklonovali na začátku článku, vytvoří funkce **GetCredentialsAsync** objekt ServiceClientCredentials na základě pověření zadaných v místním konfiguračním souboru. 

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CreateMediaServicesClient)]

### <a name="create-a-live-event"></a>Vytvoření živé události

Tato část ukazuje, jak vytvořit **předávací** typu živá událost (LiveEventEncodingType nastavena na hodnotu None). Pokud chcete vytvořit živá událost, která je povolená pro kódování v reálném čase LiveEventEncodingType nastaven na hodnotu **standardní**. 

Některá další nastavení, která můžete chtít zadat při vytváření události v reálném čase:

* Umístění Media Services 
* Protokol streamování pro živá událost (v současné době jsou podporovány protokoly RTMP nebo Smooth Streaming).<br/>Možnost protokolu nelze změnit během živá událost nebo jeho přidružené výstupů za běhu. Pokud požadujete různé protokoly, měli byste vytvořit samostatné živá událost pro každý protokol streamování.  
* Omezení IP adres u ingestování a náhledu. Můžete definovat IP adresy, které jsou povoleno ingestování videa tato živá událost. Povolené IP adresy se dají zadat jako jedna IP adresa (třeba 10.0.0.1), rozsah IP adres pomocí IP adresy a masky podsítě CIDR (třeba 10.0.0.1/22) nebo rozsah IP adres a maska podsítě v desítkovém zápisu s tečkou (třeba 10.0.0.1(255.255.252.0)).<br/>Pokud nezadáte žádné IP adresy a neexistuje definice pravidla, nebude povolená žádná IP adresa. Pokud chcete povolit libovolnou IP adresy, vytvořte pravidlo a nastavte 0.0.0.0/0.<br/>IP adresy musí být v jednom z následujících formátů: Adresu IpV4 s 4 číslice, rozsah adres CIDR.
* Při vytváření události můžete nastavit automatické spouštění. <br/>Když automatické spuštění je nastavena na hodnotu true, živá událost se spustí po jeho vytvoření. To znamená, co nejdříve startsrunning živá událost fakturace začíná. Musíte explicitně volat Stop prostředku živá událost, která zastaví další fakturace. Další informace najdete v tématu [živá událost stavy a fakturace](live-event-states-billing.md).

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CreateLiveEvent)]

### <a name="get-ingest-urls"></a>Získání ingestovaných adres URL

Po vytvoření živá událost můžete získat ingestované adresy URL, které poskytnete kodér služby live Encoding. Kodér tyto adresy URL používá ke vkládání živého proudu.

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#GetIngestURL)]

### <a name="get-the-preview-url"></a>Získání adresy URL náhledu

Použijte previewEndpoint a vytvořte náhled a ověřte, že se skutečně přijímá vstup z kodéru.

> [!IMPORTANT]
> Než budete pokračovat, ujistěte se, že se video přenáší na adresu URL náhledu.

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#GetPreviewURLs)]

### <a name="create-and-manage-live-events-and-live-outputs"></a>Vytvoření a správa živé události a Live výstupy

Jakmile se datový proud plyne do živá událost, je můžete událost streamování zahájit tím tak, že vytvoříte Asset, Live výstup a Lokátor streamování. Datový proud se tak archivuje a zpřístupní se divákům prostřednictvím koncového bodu streamování. 

#### <a name="create-an-asset"></a>Vytvoření prostředku

Vytvoření Assetu pro výstup živého používat.

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CreateAsset)]

#### <a name="create-a-live-output"></a>Vytvoření živé výstupu

Live výstupů spuštění při vytvoření a přestanou při odstranění. Při odstranění výstupní Live nejsou odstraněním podkladových prostředků a obsahu v prostředku.

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CreateLiveOutput)]

#### <a name="create-a-streaming-locator"></a>Vytvořit lokátor streamování

> [!NOTE]
> Po vytvoření účtu Media Services se do vašeho účtu přidá **výchozí** koncový bod streamování ve stavu **Zastaveno**. Pokud chcete spustit streamování vašeho obsahu a využít výhod dynamického balení a dynamického šifrování, musí koncový bod streamování, ze kterého chcete streamovat obsah, být ve stavu **Spuštěno**. 

Když publikujete Live výstupního prostředku pomocí Lokátor streamování, živá událost (až do délky okna DVR) bude dál zobrazit až do vypršení platnosti lokátoru streamování nebo odstranění, podle toho, co nastane dřív.

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CreateStreamingLocator)]

```csharp

// Get the url to stream the output
ListPathsResponse paths = await client.StreamingLocators.ListPathsAsync(resourceGroupName, accountName, locatorName);

foreach (StreamingPath path in paths.StreamingPaths)
{
    UriBuilder uriBuilder = new UriBuilder();
    uriBuilder.Scheme = "https";
    uriBuilder.Host = streamingEndpoint.HostName;

    uriBuilder.Path = path.Paths[0];
    // Get the URL from the uriBuilder: uriBuilder.ToString()
}
```

### <a name="cleaning-up-resources-in-your-media-services-account"></a>Vyčištění zdrojů v účtu služby Media Services

Pokud jste dokončili streamování událostí a chcete dříve zřízené prostředky vyčistit, postupujte podle následujícího návodu.

* Zastavte odesílání datového proudu z kodéru.
* Zastavte živou událost. Po zastavení živá událost, nebudou účtovat žádné poplatky. Když bude potřeba kanál znovu spustit, bude mít stejnou ingestovanou adresu URL, takže nebude nutné kodér znovu konfigurovat.
* Pokud nechcete pokračovat v poskytování archivu živé události ve formě datového proudu na vyžádání, můžete koncový bod streamování zastavit. Pokud se živá událost je v zastaveném stavu, nebudou účtovat žádné poplatky za.

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CleanupLiveEventAndOutput)]

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CleanupLocatorAssetAndStreamingEndpoint)]

Následující kód ukazuje, jak vyčistit účtu všechny živé události:

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CleanupAccount)]   

## <a name="watch-the-event"></a>Sledování události

Pokud chcete sledovat událost, zkopírujte adresu URL streamování, které jste získali při spuštění kódu v [vytvořit lokátor streamování](#create-a-streaminglocator) a použijte přehrávač dle vašeho výběru. K testování streamování na webu http://ampdemo.azureedge.net můžete použít [Azure Media Player](http://amp.azure.net/libs/amp/latest/docs/index.html). 

Živá událost automaticky převede na obsah na vyžádání při zastavení. I po zastavení a odstranění události můžou uživatelé streamovat archivovaný obsah jako video na vyžádání, a to tak dlouho, dokud asset neodstraníte. Asset nemůžete odstranit, pokud ho událost používá. Nejdřív odstraňte událost. 

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud ze skupiny prostředků už žádné prostředky nepotřebujete, včetně účtu služby Media Services a účtu úložiště, které jste vytvořili v tomto kurzu, pak tuto dříve vytvořenou skupinu prostředků odstraňte.

Spusťte následující příkaz rozhraní příkazového řádku:

```azurecli-interactive
az group delete --name amsResourceGroup
```

> [!IMPORTANT]
> Opuštění živá událost s sebou nese náklady fakturačních nákladů. Mějte na paměti, pokud dojde k chybě projektu nebo programu, nebo je uzavření z jakéhokoli důvodu, mohou způsobit živá událost spuštěná ve fakturační stavu.

## <a name="next-steps"></a>Další postup

[Streamování souborů](stream-files-tutorial-with-api.md)
 
