---
title: Streamujte živě s Azure Media Services v3 s použitím .NET Core | Microsoft Docs
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
ms.date: 10/16/2018
ms.author: juliako
ms.openlocfilehash: bd149177a91bc0d5897723df2fad50fef11a37ef
ms.sourcegitcommit: b4a46897fa52b1e04dd31e30677023a29d9ee0d9
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2018
ms.locfileid: "49392331"
---
# <a name="stream-live-with-azure-media-services-v3-using-net-core"></a>Streamujte živě s Azure Media Services v3 s použitím .NET Core

Ve službě Media Services zodpovídají za zpracování obsahu živého streamování události [LiveEvent](https://docs.microsoft.com/rest/api/media/liveevents). LiveEvent poskytuje vstupní koncový bod (ingestovanou adresu URL), kterou pak poskytnete do kodéru pro kódování v reálném čase. LiveEvent přijímá vstupní datové proudy v reálném čase z kodéru pro kódování v reálném čase a zpřístupňuje je ke streamování prostřednictvím jednoho nebo více koncových bodů streamování [StreamingEndpoints](https://docs.microsoft.com/rest/api/media/streamingendpoints). Události LiveEvent poskytují také koncový bod náhledu (adresu URL náhledu), který se používá k zobrazení náhledu a ověření datového proudu před dalším zpracováním a doručením. Tento kurz ukazuje, jak použít .NET Core k vytvoření **průchozího** typu události v reálném čase. 

> [!NOTE]
> Než budete pokračovat, přečtěte si téma [Živé streamování s Media Services v3](live-streaming-overview.md). 

V tomto kurzu získáte informace o následujících postupech:    

> [!div class="checklist"]
> * Vytvoření účtu Media Services
> * Přístup k rozhraní API služby Media Services
> * Konfigurace ukázkové aplikace
> * Kontrola kódu, který provádí živé streamování
> * Sledování události s [Azure Media Player](http://amp.azure.net/libs/amp/latest/docs/index.html) na webu http://ampdemo.azureedge.net
> * Vyčištění prostředků

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

K dokončení kurzu potřebujete následující:

* Nainstalovat Visual Studio Code nebo Visual Studio
* Fotoaparát nebo zařízení (jako je přenosný počítač), které se používá k vysílání události
* Místní kodér pro kódování v reálném čase, který převádí signály z kamery na datové proudy, které se odesílají do služby živého streamování Media Services. Datový proud musí být ve formátu **RTMP** nebo **Smooth Streaming**.

## <a name="download-the-sample"></a>Stažení ukázky

Pomocí následujícího příkazu naklonujte do svého počítače úložiště GitHub s ukázkou streamování .NET:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials.git
 ```

Ukázku živého streamování najdete ve složce [Live](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/tree/master/NETCore/Live/MediaV3LiveApp) (Živé).

> [!IMPORTANT]
> Tato ukázka používá pro každý zdroj jedinečnou příponu. Pokud zrušíte ladění nebo ukončíte aplikaci, aniž by proběhla, budete mít v účtu několik událostí LiveEvent. <br/>
> Spuštěné události LiveEvent je potřeba zastavit. V opačném případě se vám za ně budou **účtovat poplatky**.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [media-services-cli-create-v3-account-include](../../../includes/media-services-cli-create-v3-account-include.md)]

[!INCLUDE [media-services-v3-cli-access-api-include](../../../includes/media-services-v3-cli-access-api-include.md)]

## <a name="examine-the-code-that-performs-live-streaming"></a>Kontrola kódu, který provádí živé streamování

Tato část prozkoumává funkce definované v souboru [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/Live/MediaV3LiveApp/Program.cs) projektu *MediaV3LiveApp*.

V ukázce se pro každý zdroj vytvoří jedinečná přípona, aby nedocházelo ke kolizím názvů, když ukázku spustíte vícekrát bez vyčištění.

> [!IMPORTANT]
> Tato ukázka používá pro každý zdroj jedinečnou příponu. Pokud zrušíte ladění nebo ukončíte aplikaci, aniž by proběhla, budete mít v účtu několik událostí LiveEvent. <br/>
> Spuštěné události LiveEvent je potřeba zastavit. V opačném případě se vám za ně budou **účtovat poplatky**.
 
### <a name="start-using-media-services-apis-with-net-sdk"></a>Začínáme s rozhraním API služby Media Services se sadou .NET SDK

Pokud chcete začít používat rozhraní Media Services API se sadou .NET SDK, musíte vytvořit objekt **AzureMediaServicesClient**. K vytvoření tohoto objektu, musíte zadat přihlašovací údaje, aby se klient mohl připojit k Azure pomocí Azure AD. V kódu, který jste naklonovali na začátku článku, vytvoří funkce **GetCredentialsAsync** objekt ServiceClientCredentials na základě pověření zadaných v místním konfiguračním souboru. 

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CreateMediaServicesClient)]

### <a name="create-a-live-event"></a>Vytvoření živé události

V této části vidíte, jak vytvořit **průchozí** typ události LiveEvent (pro LiveEventEncodingType je nastavená hodnota Žádné). Pokud chcete vytvořit událost LiveEvent, u které je povolené kódování v reálném čase, nastavte pro LiveEventEncodingType hodnotu Základní. 

Některá další nastavení, která můžete chtít zadat při vytváření události v reálném čase:

* Umístění Media Services 
* Protokol pro streamování událostí v reálném čase (v současné době se podporují protokoly RTMP a Smooth Streaming)
       
    Když jsou událost LiveEvent nebo s ní spojené výstupy LiveOutput spuštěné, nedá se vybraný protokol změnit. Pokud požadujete různé protokoly, měli byste pro každý protokol streamování vytvořit samostatné události LiveEvent.  
* Omezení IP adres u ingestování a náhledu. Můžete definovat IP adresy, u kterých je povolené ingestování videa do této události LiveEvent. Povolené IP adresy se dají zadat jako jedna IP adresa (třeba 10.0.0.1), rozsah IP adres pomocí IP adresy a masky podsítě CIDR (třeba 10.0.0.1/22) nebo rozsah IP adres a maska podsítě v desítkovém zápisu s tečkou (třeba 10.0.0.1(255.255.252.0)).
    
    Pokud nezadáte žádné IP adresy a neexistuje definice pravidla, nebude povolená žádná IP adresa. Pokud chcete povolit libovolnou IP adresy, vytvořte pravidlo a nastavte 0.0.0.0/0.

Při vytváření události můžete nastavit automatické spouštění. 

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CreateLiveEvent)]

### <a name="get-ingest-urls"></a>Získání ingestovaných adres URL

Po vytvoření kanálu můžete získat ingestované adresy URL, které poskytnete kodéru pro kódování v reálném čase. Kodér tyto adresy URL používá ke vkládání živého proudu.

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#GetIngestURL)]

### <a name="get-the-preview-url"></a>Získání adresy URL náhledu

Použijte previewEndpoint a vytvořte náhled a ověřte, že se skutečně přijímá vstup z kodéru.

> [!IMPORTANT]
> Než budete pokračovat, ujistěte se, že se video přenáší na adresu URL náhledu.

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#GetPreviewURLs)]

### <a name="create-and-manage-liveevents-and-liveoutputs"></a>Vytváření a správa událostí LiveEvent a výstupů LiveOutput

Jakmile se datový proud přenáší do události LiveEvent, můžete zahájit událost streamování vytvořením prostředku, výstupu LiveOutput a lokátoru streamování StreamingLocator. Datový proud se bude archivovat a zpřístupní se divákům prostřednictvím koncového bodu streamování StreamingEndpoint. 

#### <a name="create-an-asset"></a>Vytvoření prostředku

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CreateAsset)]

Vytvořte prostředek pro výstup LiveOutput, který se má použít.

#### <a name="create-a-liveoutput"></a>Vytvoření události LiveOutput

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CreateLiveOutput)]

#### <a name="create-a-streaminglocator"></a>Vytvoření lokátoru streamování StreamingLocator

> [!NOTE]
> Po vytvoření účtu Media Services se do vašeho účtu přidá **výchozí** koncový bod streamování ve stavu **Zastaveno**. Pokud chcete spustit streamování vašeho obsahu a využít výhod dynamického balení a dynamického šifrování, musí koncový bod streamování, ze kterého chcete streamovat obsah, být ve stavu **Spuštěno**. 

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
* Zastavte událost LiveEvent. Po zastavení události LiveEvent se nebudou účtovat žádné poplatky. Když bude potřeba kanál znovu spustit, bude mít stejnou ingestovanou adresu URL, takže nebude nutné kodér znovu konfigurovat.
* Pokud nechcete dál poskytovat archiv živé události ve formě datového proudu na vyžádání, můžete koncový bod streamování StreamingEndpoint zastavit. Když je událost LiveEvent zastavená, nebudou se účtovat žádné poplatky.

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CleanupLiveEventAndOutput)]

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CleanupLocatorAssetAndStreamingEndpoint)]

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CleanupAccount)]   

## <a name="watch-the-event"></a>Sledování události

Pokud chcete sledovat událost, zkopírujte adresu URL streamování získanou při spuštění kódu, jak je popsané v tématu [Vytvoření lokátoru streamování StreamingLocator](#create-a-streaminglocator), a použijte přehrávač podle svého výběru. K testování streamování na webu http://ampdemo.azureedge.net můžete použít [Azure Media Player](http://amp.azure.net/libs/amp/latest/docs/index.html). 

Při zastavení se živá událost automaticky převede na obsah na vyžádání. I po zastavení a odstranění události můžou uživatelé streamovat archivovaný obsah jako video na vyžádání, a to tak dlouho, dokud asset neodstraníte. Asset nemůžete odstranit, pokud ho událost používá. Nejdřív odstraňte událost. 

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud ze skupiny prostředků už žádné prostředky nepotřebujete, včetně účtu služby Media Services a účtu úložiště, které jste vytvořili v tomto kurzu, pak tuto dříve vytvořenou skupinu prostředků odstraňte. Můžete použít nástroj **CloudShell**.

V nástroji **CloudShell** spusťte následující příkaz:

```azurecli-interactive
az group delete --name amsResourceGroup
```

> [!IMPORTANT]
> Když necháte událost LiveEvent spuštěnou, budou se účtovat poplatky. Nezapomeňte, že pokud z nějakého důvodu dojde u projektu nebo programu k chybě nebo se ukončí, může zůstat událost LiveEvent spuštěná a budou se účtovat poplatky.

## <a name="next-steps"></a>Další kroky

[Streamování souborů](stream-files-tutorial-with-api.md)

