---
title: Živé vysílání pomocí mediálních služeb v3
titleSuffix: Azure Media Services
description: Přečtěte si, jak streamovat živě pomocí Azure Media Services v3.
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
ms.openlocfilehash: 0b6667965ddd1fce30bb2da2593e2a9274b595ed
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "79472012"
---
# <a name="tutorial-stream-live-with-media-services"></a>Kurz: Streamujte živě s mediálními službami

> [!NOTE]
> I když kurz používá příklady [sady .NET SDK,](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.liveevent?view=azure-dotnet) obecné kroky jsou stejné pro [rozhraní REST API](https://docs.microsoft.com/rest/api/media/liveevents), ROZHRANÍ [CLI](https://docs.microsoft.com/cli/azure/ams/live-event?view=azure-cli-latest)nebo jiné podporované [sady SDK](media-services-apis-overview.md#sdks).

Ve službě Azure Media Services jsou [živé události](https://docs.microsoft.com/rest/api/media/liveevents) zodpovědné za zpracování živého streamování obsahu. Živá událost poskytuje vstupní koncový bod (ingestující adresu URL), který pak poskytnete živému kodéru. Živá událost přijímá živé vstupní proudy z živého kodéru a zpřístupňuje je pro streamování prostřednictvím jednoho nebo více [koncových bodů streamování](https://docs.microsoft.com/rest/api/media/streamingendpoints). Živé události také poskytují koncový bod náhledu (adresu URL náhledu), který používáte k zobrazení náhledu a ověření datového proudu před dalším zpracováním a doručením. Tento kurz ukazuje, jak použít .NET Core k vytvoření **průchozího** typu události v reálném čase.

V tomto kurzu získáte informace o následujících postupech:

> [!div class="checklist"]
> * Stáhněte si ukázkovou aplikaci popsanou v tématu.
> * Zkontrolujte kód, který provádí živé vysílání.
> * Podívejte se na událost [https://ampdemo.azureedge.net](https://ampdemo.azureedge.net)s Azure Media [Player](https://amp.azure.net/libs/amp/latest/docs/index.html) na adrese .
> * Vyčistěte prostředky.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

K dokončení kurzu potřebujete následující položky:

- Nainstalujte kód sady Visual Studio nebo visual studio.
- [Vytvořte účet mediálních služeb](create-account-cli-how-to.md).<br/>Nezapomeňte si pamatovat hodnoty, které používáte pro název skupiny prostředků a název účtu Mediální služby.
- Postupujte podle kroků v [rozhraní Access Azure Media Services API s rozhraním příkazového příkazového příkazu k Síti Azure](access-api-cli-how-to.md) a uložte přihlašovací údaje. Budete je muset použít pro přístup k rozhraní API.
- Fotoaparát nebo zařízení (například přenosný počítač), které se používá k vysílání události.
- Místní živý kodér, který převádí signály z kamery na datové proudy odesílané službě Živé streamování služby Media Services, viz [doporučené místní živé kodéry](recommended-on-premises-live-encoders.md). Datový proud musí být ve formátu **RTMP** nebo **Smooth Streaming**.

> [!TIP]
> Než budete pokračovat, přečtěte si téma [Živé streamování s Media Services v3](live-streaming-overview.md). 

## <a name="download-and-configure-the-sample"></a>Stažení a konfigurace ukázky

Pomocí následujícího příkazu naklonujte do svého počítače úložiště GitHub s ukázkou streamování .NET:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials.git
 ```

Ukázku živého streamování najdete ve složce [Live](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/tree/master/NETCore/Live/MediaV3LiveApp) (Živé).

Otevřete [soubor appsettings.json](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/Live/MediaV3LiveApp/appsettings.json) ve staženém projektu. Nahraďte hodnoty pověřeními, která jste získali z [přístupu k api](access-api-cli-how-to.md).

> [!IMPORTANT]
> Tato ukázka používá jedinečnou příponu pro každý prostředek. Pokud ladění zrušíte nebo ukončíte, aniž byste ji provedli, skončíte s více živými událostmi ve vašem účtu. <br/>Ujistěte se, že zastavíte spuštěné živé události. V opačném případě vám bude **účtováno!**

## <a name="examine-the-code-that-performs-live-streaming"></a>Kontrola kódu, který provádí živé streamování

Tato část prozkoumává funkce definované v souboru [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/Live/MediaV3LiveApp/Program.cs) projektu *MediaV3LiveApp*.

Ukázka vytvoří jedinečnou příponu pro každý prostředek, takže nemáte názvové kolize, pokud spustíte vzorek vícekrát bez vyčištění.

> [!IMPORTANT]
> Tato ukázka používá jedinečnou příponu pro každý prostředek. Pokud ladění zrušíte nebo ukončíte, aniž byste ji provedli, skončíte s více živými událostmi ve vašem účtu. <br/>
> Ujistěte se, že zastavíte spuštěné živé události. V opačném případě vám bude **účtováno!**

### <a name="start-using-media-services-apis-with-net-sdk"></a>Začínáme s rozhraním API služby Media Services se sadou .NET SDK

Pokud chcete začít používat rozhraní Media Services API se sadou .NET SDK, musíte vytvořit objekt **AzureMediaServicesClient**. K vytvoření tohoto objektu, musíte zadat přihlašovací údaje, aby se klient mohl připojit k Azure pomocí Azure AD. V kódu, který jste naklonovali na začátku článku, vytvoří funkce **GetCredentialsAsync** objekt ServiceClientCredentials na základě pověření zadaných v místním konfiguračním souboru. 

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CreateMediaServicesClient)]

### <a name="create-a-live-event"></a>Vytvoření živé události

Tato část ukazuje, jak vytvořit **předávací** typ živé události (LiveEventEncodingType nastavený na žádný). Další informace o dostupných typech živých událostí naleznete v [tématu Typy živých událostí](live-events-outputs-concept.md#live-event-types). 
 
Při vytváření živé události můžete určit:

* Umístění mediálních služeb.
* Protokol streamování pro živé události (v současné době jsou podporovány protokoly RTMP a Smooth Streaming).<br/>Možnost protokolu nelze změnit, pokud je spuštěna živá událost nebo přidružené živé výstupy. Pokud požadujete různé protokoly, vytvořte pro každý protokol streamování samostatnou živou událost.  
* Omezení IP adres u ingestování a náhledu. Můžete definovat IP adresy, které mohou ingestovat video do této živé události. Povolené IP adresy se dají zadat jako jedna IP adresa (třeba 10.0.0.1), rozsah IP adres pomocí IP adresy a masky podsítě CIDR (třeba 10.0.0.1/22) nebo rozsah IP adres a maska podsítě v desítkovém zápisu s tečkou (třeba 10.0.0.1(255.255.252.0)).<br/>Pokud nejsou zadány žádné adresy IP a neexistuje žádná definice pravidla, nebude povolena žádná adresa IP. Pokud chcete povolit libovolnou IP adresy, vytvořte pravidlo a nastavte 0.0.0.0/0.<br/>IP adresy musí být v jednom z následujících formátů: IpV4 adresa se čtyřmi čísly nebo rozsah adres CIDR.
* Při vytváření události můžete určit její automatické spuštění. <br/>Je-li automatické spuštění nastaveno na hodnotu true, bude živá událost spuštěna po vytvoření. To znamená, že fakturace začíná, jakmile se spustí živá událost. Chcete-li zastavit další fakturaci, je nutné explicitně volat Stop na prostředku živé události. Další informace naleznete v tématu [Stavy živých událostí a fakturace](live-event-states-billing.md).
* Chcete-li, aby byla adresa URL ingestování prediktivní, nastavte režim "marnost". Podrobné informace naleznete v tématu [Živé události ingestování adres URL](live-events-outputs-concept.md#live-event-ingest-urls).

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CreateLiveEvent)]

### <a name="get-ingest-urls"></a>Získání ingestovaných adres URL

Po vytvoření živé události můžete získat ingestující adresy URL, které poskytnete živému kodéru. Kodér tyto adresy URL používá ke vkládání živého proudu.

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#GetIngestURL)]

### <a name="get-the-preview-url"></a>Získání adresy URL náhledu

Použijte previewEndpoint a vytvořte náhled a ověřte, že se skutečně přijímá vstup z kodéru.

> [!IMPORTANT]
> Než budete pokračovat, ujistěte se, že video přetéká na adresu URL náhledu.

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#GetPreviewURLs)]

### <a name="create-and-manage-live-events-and-live-outputs"></a>Vytváření a správa živých událostí a živých výstupů

Jakmile budete mít datový proud proudící do živé události, můžete začít streamování události vytvořením asset, live výstup a streamování Lokátor. Datový proud se tak archivuje a zpřístupní se divákům prostřednictvím koncového bodu streamování.

#### <a name="create-an-asset"></a>Vytvoření prostředku

Vytvořte datový zdroj pro živý výstup, který se má použít.

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CreateAsset)]

#### <a name="create-a-live-output"></a>Vytvoření živého výstupu

Živé výstupy se spustí při vytváření a zastaví se při odstranění. Když odstraníte živý výstup, neodstraníte podkladový datový zdroj a obsah v datovém zdroji.

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CreateLiveOutput)]

#### <a name="create-a-streaming-locator"></a>Vytvoření lokátoru streamování

> [!NOTE]
> Při vytvoření účtu Media Services je k vašemu účtu ve stavu **Zastaveno** přidán **výchozí** koncový bod streamování. Chcete-li spustit streamování obsahu a využít [výhod dynamického balení](dynamic-packaging-overview.md) a dynamického šifrování, koncový bod streamování, ze kterého chcete streamovat obsah, musí být ve stavu **Spuštěno.**

Když publikujete datový zdroj živého výstupu pomocí lokátoru streamování, živá událost (až do délky okna DVR) bude i nadále zobrazitelná až do vypršení platnosti nebo odstranění lokátoru streamování, podle toho, co nastane dříve.

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

Pokud jste skončili streamování událostí a chcete vyčistit prostředky zřízené dříve, postupujte podle následujícího postupu:

* Zastavte odesílání datového proudu z kodéru.
* Zastavte živou událost. Jakmile je živá událost zastavena, nebude jí účtovány žádné poplatky. Když bude potřeba kanál znovu spustit, bude mít stejnou ingestovanou adresu URL, takže nebude nutné kodér znovu konfigurovat.
* Pokud nechcete pokračovat v poskytování archivu živé události ve formě datového proudu na vyžádání, můžete koncový bod streamování zastavit. Pokud je živá událost v zastaveném stavu, nebudou jí účtovány žádné poplatky.

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CleanupLiveEventAndOutput)]

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CleanupLocatorAssetAndStreamingEndpoint)]

## <a name="watch-the-event"></a>Sledování události

Chcete-li událost sledovat, zkopírujte adresu URL streamování, kterou jste získali při spuštění kódu popsaného v části Vytvoření lokátoru streamování. Můžete použít přehrávač médií podle vašeho výběru. [Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/index.html) je k https://ampdemo.azureedge.netdispozici k testování datového proudu na adrese .

Živá událost automaticky převede události na obsah na vyžádání, když je zastaven. I po zastavení a odstranění události mohou uživatelé streamovat archivovaný obsah jako video na vyžádání, pokud datový zdroj neodstraníte. Datový zdroj nelze odstranit, pokud jej používá událost. událost musí být nejprve odstraněna.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud ze skupiny prostředků už žádné prostředky nepotřebujete, včetně účtu služby Media Services a účtu úložiště, které jste vytvořili v tomto kurzu, pak tuto dříve vytvořenou skupinu prostředků odstraňte.

Proveďte následující příkaz příkazového příkazu příkazu příkazu:

```azurecli-interactive
az group delete --name amsResourceGroup
```

> [!IMPORTANT]
> Po spuštění živé události vznikají náklady na fakturaci. Uvědomte si, pokud projekt /program dojde k chybě nebo je uzavřen z jakéhokoli důvodu, může opustit live událost běží ve stavu fakturace.

## <a name="ask-questions-give-feedback-get-updates"></a>Ptejte se, podávejte zpětnou vazbu, získejte aktualizace

Podívejte se na článek [komunity Mediálních služeb Azure](media-services-community.md) a podívejte se na různé způsoby, jak můžete klást otázky, poskytovat zpětnou vazbu a získat aktualizace o mediálních službách.

## <a name="next-steps"></a>Další kroky

[Streamování souborů](stream-files-tutorial-with-api.md)
 
