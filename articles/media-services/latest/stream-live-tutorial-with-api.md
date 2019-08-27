---
title: Živé streamování s Azure Media Services V3 | Microsoft Docs
description: Tento kurz vás provede jednotlivými kroky streamování živě s Media Services V3.
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
ms.date: 06/13/2019
ms.author: juliako
ms.openlocfilehash: b69bd62cb9bbe44fb37b3f3660c2f20f3965384e
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/27/2019
ms.locfileid: "70051574"
---
# <a name="tutorial-stream-live-with-media-services"></a>Kurz: Živé streamování pomocí Media Services

> [!NOTE]
> I když tento kurz používá příklady [sady .NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.liveevent?view=azure-dotnet) , jsou obecné kroky stejné pro [REST API](https://docs.microsoft.com/rest/api/media/liveevents), [CLI](https://docs.microsoft.com/cli/azure/ams/live-event?view=azure-cli-latest)nebo jiné podporované sady [SDK](media-services-apis-overview.md#sdks).

V Azure Media Services za zpracování obsahu živého streamování odpovídají [živé události](https://docs.microsoft.com/rest/api/media/liveevents) . Živá událost poskytuje vstupní koncový bod (adresa URL pro příjem), který pak poskytnete pro živý kodér. Živá událost přijímá živé vstupní datové proudy z kodéru Live a zpřístupňuje je pro streamování prostřednictvím jednoho nebo více [koncových bodů streamování](https://docs.microsoft.com/rest/api/media/streamingendpoints). Živé události také poskytují koncový bod verze Preview (adresa URL náhledu), který používáte k zobrazení náhledu a ověření datového proudu před dalším zpracováním a doručením. Tento kurz ukazuje, jak použít .NET Core k vytvoření **průchozího** typu události v reálném čase. 

V tomto kurzu získáte informace o následujících postupech:    

> [!div class="checklist"]
> * Stáhněte si ukázkovou aplikaci popsanou v tématu.
> * Kontrola kódu, který provádí živé streamování
> * Sledování události s [Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/index.html) na webu https://ampdemo.azureedge.net
> * Vyčištění prostředků

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

K dokončení kurzu potřebujete následující:

- Nainstalujte Visual Studio Code nebo Visual Studio.
- [Vytvoření účtu Media Services](create-account-cli-how-to.md).<br/>Nezapomeňte si pamatovat hodnoty, které jste použili pro název skupiny prostředků a název účtu Media Services.
- Postupujte podle kroků v [části přístup k rozhraní API Azure Media Services pomocí Azure CLI](access-api-cli-how-to.md) a přihlašovací údaje uložte. Budete je muset použít pro přístup k rozhraní API.
- Fotoaparát nebo zařízení (jako je přenosný počítač), které se používá k vysílání události
- Místní kodér pro kódování v reálném čase, který převádí signály z kamery na datové proudy, které se odesílají do služby živého streamování Media Services. Datový proud musí být ve formátu **RTMP** nebo **Smooth Streaming**.

> [!TIP]
> Než budete pokračovat, přečtěte si téma [Živé streamování s Media Services v3](live-streaming-overview.md). 

## <a name="download-and-configure-the-sample"></a>Stažení a konfigurace ukázky

Pomocí následujícího příkazu naklonujte do svého počítače úložiště GitHub s ukázkou streamování .NET:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials.git
 ```

Ukázku živého streamování najdete ve složce [Live](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/tree/master/NETCore/Live/MediaV3LiveApp) (Živé).

Ve staženém projektu otevřete [appSettings. JSON](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/Live/MediaV3LiveApp/appsettings.json) . Nahraďte hodnoty přihlašovacími údaji, které jste získali při [přístupu k rozhraním API](access-api-cli-how-to.md).

> [!IMPORTANT]
> Tato ukázka používá pro každý zdroj jedinečnou příponu. Pokud ladění zrušíte nebo ukončíte bez spuštění aplikace přes, skončíte s několika živými událostmi ve vašem účtu. <br/>Nezapomeňte zastavit běžící živé události. V opačném případě se vám za ně budou **účtovat poplatky**.

## <a name="examine-the-code-that-performs-live-streaming"></a>Kontrola kódu, který provádí živé streamování

Tato část prozkoumává funkce definované v souboru [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/Live/MediaV3LiveApp/Program.cs) projektu *MediaV3LiveApp*.

V ukázce se pro každý zdroj vytvoří jedinečná přípona, aby nedocházelo ke kolizím názvů, když ukázku spustíte vícekrát bez vyčištění.

> [!IMPORTANT]
> Tato ukázka používá pro každý zdroj jedinečnou příponu. Pokud ladění zrušíte nebo ukončíte bez spuštění aplikace přes, skončíte s několika živými událostmi ve vašem účtu. <br/>
> Nezapomeňte zastavit běžící živé události. V opačném případě se vám za ně budou **účtovat poplatky**.
 
### <a name="start-using-media-services-apis-with-net-sdk"></a>Začínáme s rozhraním API služby Media Services se sadou .NET SDK

Pokud chcete začít používat rozhraní Media Services API se sadou .NET SDK, musíte vytvořit objekt **AzureMediaServicesClient**. K vytvoření tohoto objektu, musíte zadat přihlašovací údaje, aby se klient mohl připojit k Azure pomocí Azure AD. V kódu, který jste naklonovali na začátku článku, vytvoří funkce **GetCredentialsAsync** objekt ServiceClientCredentials na základě pověření zadaných v místním konfiguračním souboru. 

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CreateMediaServicesClient)]

### <a name="create-a-live-event"></a>Vytvořit živou událost

V této části se dozvíte, jak vytvořit **průchozí** typ živé události (LiveEventEncodingType set to None). Další informace o dostupných typech živých událostí naleznete v tématu [typy událostí typu Live](live-events-outputs-concept.md#live-event-types). 
 
Některé věci, které byste mohli chtít zadat při vytváření živé události:

* Umístění Media Services 
* Protokol streamování pro živou událost (aktuálně se podporují i protokoly RTMP a Smooth Streaming).<br/>Možnost protokolu nejde změnit, pokud je spuštěná živá událost nebo jejich přidružené živé výstupy. Pokud požadujete různé protokoly, měli byste pro každý protokol streamování vytvořit samostatnou živou událost.  
* Omezení IP adres u ingestování a náhledu. Můžete definovat IP adresy, které můžou ingestovat video do této živé události. Povolené IP adresy se dají zadat jako jedna IP adresa (třeba 10.0.0.1), rozsah IP adres pomocí IP adresy a masky podsítě CIDR (třeba 10.0.0.1/22) nebo rozsah IP adres a maska podsítě v desítkovém zápisu s tečkou (třeba 10.0.0.1(255.255.252.0)).<br/>Pokud nezadáte žádné IP adresy a neexistuje definice pravidla, nebude povolená žádná IP adresa. Pokud chcete povolit libovolnou IP adresy, vytvořte pravidlo a nastavte 0.0.0.0/0.<br/>IP adresy musí být v jednom z následujících formátů: Adresa IpV4 se čtyřmi čísly, rozsahem adres CIDR.
* Při vytváření události můžete nastavit automatické spouštění. <br/>Pokud je vlastnost autostart nastavena na hodnotu true, spustí se po vytvoření živá událost. To znamená, že fakturace začne ihned po spuštění živé události. Chcete-li zastavit další fakturaci, je nutné explicitně volat stop u prostředku živé události. Další informace najdete v tématu [stavy událostí Live a fakturace](live-event-states-billing.md).
* Aby se adresa URL pro ingestování mohla odhadnout, nastavte režim "individuální". Podrobné informace najdete v tématu [adresy URL pro příjem živých událostí](live-events-outputs-concept.md#live-event-ingest-urls).

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CreateLiveEvent)]

### <a name="get-ingest-urls"></a>Získání ingestovaných adres URL

Jakmile se vytvoří živá událost, můžete získat adresy URL pro příjem dat, které poskytnete kodéru Live. Kodér tyto adresy URL používá ke vkládání živého proudu.

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#GetIngestURL)]

### <a name="get-the-preview-url"></a>Získání adresy URL náhledu

Použijte previewEndpoint a vytvořte náhled a ověřte, že se skutečně přijímá vstup z kodéru.

> [!IMPORTANT]
> Než budete pokračovat, ujistěte se, že se video přenáší na adresu URL náhledu.

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#GetPreviewURLs)]

### <a name="create-and-manage-live-events-and-live-outputs"></a>Vytváření a Správa živých událostí a živých výstupů

Jakmile datový proud přetéká do živé události, můžete zahájit streamování událostí vytvořením Assetu, živého výstupu a lokátoru streamování. Datový proud se tak archivuje a zpřístupní se divákům prostřednictvím koncového bodu streamování. 

#### <a name="create-an-asset"></a>Vytvoření prostředku

Vytvořte Asset pro živý výstup, který se má použít.

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CreateAsset)]

#### <a name="create-a-live-output"></a>Vytvořit živý výstup

Živé výstupy začínají při vytváření a při odstranění se zastaví. Když odstraníte živý výstup, neodstraníte základní Asset a obsah v assetu.

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CreateLiveOutput)]

#### <a name="create-a-streaming-locator"></a>Vytvoření lokátoru streamování

> [!NOTE]
> Po vytvoření účtu Media Services se do vašeho účtu přidá **výchozí** koncový bod streamování ve stavu **Zastaveno**. Pokud chcete spustit streamování vašeho obsahu a využít výhod [dynamického balení](dynamic-packaging-overview.md) a dynamického šifrování, musí koncový bod streamování, ze kterého chcete streamovat obsah, být ve stavu **spuštěno** . 

Když publikujete živý výstupní prostředek pomocí lokátoru streamování, bude se dál zobrazovat živá událost (až do délky okna DVR), dokud nevyprší platnost nebo odstranění lokátoru streamování, podle toho, co nastane dřív.

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
* Zastaví živou událost. Jakmile se živá událost zastaví, neúčtují se žádné poplatky. Když bude potřeba kanál znovu spustit, bude mít stejnou ingestovanou adresu URL, takže nebude nutné kodér znovu konfigurovat.
* Pokud nechcete pokračovat v poskytování archivu živé události ve formě datového proudu na vyžádání, můžete koncový bod streamování zastavit. Pokud je živá událost v zastaveném stavu, neúčtují se žádné poplatky.

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CleanupLiveEventAndOutput)]

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CleanupLocatorAssetAndStreamingEndpoint)]

## <a name="watch-the-event"></a>Sledování události

Pokud chcete sledovat událost, zkopírujte adresu URL streamování, kterou jste dostali, když jste spustili kód popsaný v tématu Vytvoření lokátoru streamování a použití přehrávače podle vašeho výběru. K testování streamování na webu https://ampdemo.azureedge.net můžete použít [Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/index.html). 

Živá událost automaticky převádí události na obsah na vyžádání při zastavení. I po zastavení a odstranění události můžou uživatelé streamovat archivovaný obsah jako video na vyžádání, a to tak dlouho, dokud asset neodstraníte. Asset nemůžete odstranit, pokud ho událost používá. Nejdřív odstraňte událost. 

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud ze skupiny prostředků už žádné prostředky nepotřebujete, včetně účtu služby Media Services a účtu úložiště, které jste vytvořili v tomto kurzu, pak tuto dříve vytvořenou skupinu prostředků odstraňte.

Spusťte následující příkaz rozhraní příkazového řádku:

```azurecli-interactive
az group delete --name amsResourceGroup
```

> [!IMPORTANT]
> Ponechávání živých událostí při provozu se účtují náklady na fakturaci. Mějte na paměti, že pokud dojde k chybě projektu nebo programu nebo z jakéhokoli důvodu dojde k jeho zavření, může dojít k tomu, že živá událost běží ve stavu fakturace.

## <a name="ask-questions-give-feedback-get-updates"></a>Položte otázky, sdělte nám svůj názor, Získejte aktualizace.

Podívejte se na článek o [komunitě Azure Media Services](media-services-community.md) a podívejte se na různé způsoby, jak můžete klást otázky, sdělit svůj názor a získávat aktualizace Media Services.

## <a name="next-steps"></a>Další kroky

[Streamování souborů](stream-files-tutorial-with-api.md)
 
