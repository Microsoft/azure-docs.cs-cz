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
ms.date: 05/15/2019
ms.author: juliako
ms.openlocfilehash: 510899e44e4ea4a90e21473ee6af546744c2be2a
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/23/2019
ms.locfileid: "66120194"
---
# <a name="streaming-policies"></a>Zásady streamování

V Azure Media Services v3 [streamování zásady](https://docs.microsoft.com/rest/api/media/streamingpolicies) vám umožňují definovat protokolů streamování a možnosti šifrování pro vaše [lokátory streamování](streaming-locators-concept.md). Media Services v3 poskytuje že některé předdefinované zásady streamování, tak, aby je mohli používat přímo pro zkušební verzi nebo produkční prostředí. 

Aktuálně k dispozici předdefinované zásady streamování:<br/>"Predefined_DownloadOnly", "Predefined_ClearStreamingOnly", "Predefined_DownloadAndClearStreaming", "Predefined_ClearKey", "Predefined_MultiDrmCencStreaming" a "Predefined_MultiDrmStreaming".

Pokud nemáte speciální požadavky (např. Pokud chcete zadat různé protokoly, potřebují používat službu vlastní doručení klíče, nebo muset použít Vymazat zvukové stopy), můžete vytvořit vlastní zásady streamování. 

 
> [!IMPORTANT]
> * Vlastnosti **streamování zásady** jsou DateTime typu jsou vždy ve formátu UTC.
> * Navrhněte omezenou sadu zásad pro svůj účet Media Service a je znovu použít pro vaše lokátory streamování pokaždé, když jsou potřeba stejné možnosti. Další informace najdete v tématu [kvóty a omezení](limits-quotas-constraints.md).

## <a name="decision-tree"></a>Rozhodovací strom

Následující rozhodovací strom vám pomůže vybrat předdefinované zásady streamování pro váš scénář.

Kliknutím na obrázek zobrazíte jeho plnou velikost.  <br/>
<a href="./media/streaming-policy/large.png" target="_blank"><img src="./media/streaming-policy/small.png"></a> 

## <a name="examples"></a>Příklady

### <a name="not-encrypted"></a>Není šifrováno

Pokud chcete ke streamování vašeho souboru v the vymazat (nešifrované), nastavte předdefinované jasné zásady streamování: na "Predefined_ClearStreamingOnly" (v .NET, můžete použít PredefinedStreamingPolicy.ClearStreamingOnly enum).

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

### <a name="encrypted"></a>Zašifrováno 

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
