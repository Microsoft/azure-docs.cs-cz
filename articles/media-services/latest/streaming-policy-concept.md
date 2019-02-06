---
title: Streamování zásady ve službě Azure Media Services | Dokumentace Microsoftu
description: Tento článek obsahuje vysvětlení, co jsou zásady streamování a jak se používají Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 02/03/2019
ms.author: juliako
ms.openlocfilehash: 10600d8f3ff4e08b8d90f28ec15d3cb0c56bcae0
ms.sourcegitcommit: 947b331c4d03f79adcb45f74d275ac160c4a2e83
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2019
ms.locfileid: "55746740"
---
# <a name="streaming-policies"></a>Zásady streamování

V Azure Media Services v3 [streamování zásady](https://docs.microsoft.com/rest/api/media/streamingpolicies) vám umožňují definovat protokolů streamování a možnosti šifrování pro vaše [lokátory streamování](streaming-locators-concept.md). Můžete buď použít jednu z předdefinovaných zásad streamování nebo vytvořit vlastní zásadu. Předdefinované datové proudy zásady, které se aktuálně k dispozici jsou: "Predefined_DownloadOnly", "Predefined_ClearStreamingOnly", "Predefined_DownloadAndClearStreaming", "Predefined_ClearKey", "Predefined_MultiDrmCencStreaming" a "Predefined_MultiDrmStreaming".

> [!IMPORTANT]
> * Vlastnosti **streamování zásady** jsou DateTime typu jsou vždy ve formátu UTC.
> * Navrhněte omezenou sadu zásad pro svůj účet Media Service a je znovu použít pro vaše lokátory streamování pokaždé, když jsou potřeba stejné možnosti. 

## <a name="examples"></a>Příklady

### <a name="not-encrypted"></a>Není šifrováno

Pokud chcete ke streamování vašeho souboru v the vymazat (nešifrované), nastavte předdefinované jasné zásady streamování: na "Predefined_ClearStreamingOnly" (v .NET, můžete použít PredefinedStreamingPolicy.ClearStreamingOnly).

```csharp
StreamingLocator locator = await client.StreamingLocators.CreateAsync(
    resourceGroup,
    accountName,
    locatorName,
    new StreamingLocator
    {
        AssetName = assetName,
        StreamingPolicyName = PredefinedStreamingPolicy.ClearStreamingOnly
    });
```

### <a name="encrypted"></a>Šifrované 

Pokud potřebujete k šifrování obsahu pomocí šifrování obálky a cenc, nastavte zásadu "Predefined_MultiDrmCencStreaming". Tato zásada udává, že chcete vygenerovat a nastavit v lokátoru dva symetrické klíče (obálku a CENC). Tím dojde k nastavení obálky a šifrování PlayReady a Widevine (klíč se doručí klientovi pro přehrávání na základě nakonfigurovaných licencí DRM).

```csharp
StreamingLocator locator = await client.StreamingLocators.CreateAsync(
    resourceGroup,
    accountName,
    locatorName,
    new StreamingLocator
    {
        AssetName = assetName,
        StreamingPolicyName = "Predefined_MultiDrmCencStreaming",
        DefaultContentKeyPolicyName = contentPolicyName
    });
```

Pokud chcete zašifrovat datový proud s CBCS (FairPlay), použijte "Predefined_MultiDrmStreaming".

## <a name="filtering-ordering-paging"></a>Filtrování, řazení, stránkování

Zobrazit [filtrování, řazení, stránkování, Media Services entit](entities-overview.md).

## <a name="next-steps"></a>Další postup

* [Streamování souboru](stream-files-dotnet-quickstart.md)
* [Použití dynamického šifrování AES-128 a doručení klíče služby](protect-with-aes128.md)
* [Pomocí DRM dynamického šifrování a licence služby pro doručování](protect-with-drm.md)
