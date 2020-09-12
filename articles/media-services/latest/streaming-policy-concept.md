---
title: Zásady streamování v Azure Media Services | Microsoft Docs
description: Tento článek obsahuje vysvětlení, které zásady streamování jsou a jak se používají v Azure Media Services.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 05/28/2019
ms.author: inhenkel
ms.openlocfilehash: 9ecb4e0b8a74a163632967781c84d1a110ab3a9a
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/02/2020
ms.locfileid: "89297208"
---
# <a name="streaming-policies"></a>Zásady streamování

V Azure Media Services V3 umožňují [zásady streamování](/rest/api/media/streamingpolicies) definovat protokoly streamování a možnosti šifrování pro vaše [Lokátory streamování](streaming-locators-concept.md). Media Services V3 poskytuje některé předdefinované zásady streamování, abyste je mohli použít přímo pro zkušební verze nebo pro produkční prostředí. 

Aktuálně dostupné předdefinované zásady streamování:<br/>
* Predefined_DownloadOnly
* Predefined_ClearStreamingOnly
* Predefined_DownloadAndClearStreaming
* Predefined_ClearKey
* Predefined_MultiDrmCencStreaming 
* Predefined_MultiDrmStreaming

Následující "rozhodovací strom" vám pomůže vybrat předdefinované zásady streamování pro váš scénář.

> [!IMPORTANT]
> * Vlastnosti **zásad streamování** , které jsou typu DateTime, jsou vždycky ve formátu UTC.
> * Měli byste navrhnout omezené sady zásad pro svůj účet Media Service a znovu je použít pro Lokátory streamování, pokud jsou potřeba stejné možnosti. Další informace najdete v tématu [kvóty a omezení](limits-quotas-constraints.md).

## <a name="decision-tree"></a>Rozhodovací strom

Kliknutím na obrázek zobrazíte jeho plnou velikost.  

[![Diagram znázorňující rozhodovací strom, který je navržený tak, aby vám pomohly zvolit předdefinované zásady streamování pro váš scénář.](./media/streaming-policy/large.png)](./media/streaming-policy/large.png#lightbox)

Při šifrování obsahu je potřeba vytvořit [zásadu klíče obsahu](content-key-policy-concept.md), **zásady klíčů obsahu** není potřeba pro vymazání streamování nebo stahování. 

Pokud máte zvláštní požadavky (například pokud chcete zadat různé protokoly, potřebujete použít službu pro doručování klíčů, nebo potřebujete použít jasné zvukové stopy), můžete [vytvořit](/rest/api/media/streamingpolicies/create) vlastní zásadu streamování. 

## <a name="get-a-streaming-policy-definition"></a>Získání definice zásad streamování  

Pokud chcete zobrazit definici zásady streamování, použijte [Get](/rest/api/media/streamingpolicies/get) a zadejte název zásady. Například:

### <a name="rest"></a>REST

Požadavek:

```
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Media/mediaServices/contosomedia/streamingPolicies/clearStreamingPolicy?api-version=2018-07-01
```

Odpověď:

```
{
  "name": "clearStreamingPolicy",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Media/mediaservices/contosomedia/streamingPolicies/clearStreamingPolicy",
  "type": "Microsoft.Media/mediaservices/streamingPolicies",
  "properties": {
    "created": "2018-08-08T18:29:30.8501486Z",
    "noEncryption": {
      "enabledProtocols": {
        "download": true,
        "dash": true,
        "hls": true,
        "smoothStreaming": true
      }
    }
  }
}
```

## <a name="filtering-ordering-paging"></a>Filtrování, řazení, stránkování

Viz téma [filtrování, řazení, stránkování Media Services entit](entities-overview.md).

## <a name="next-steps"></a>Další kroky

* [Streamování souboru](stream-files-dotnet-quickstart.md)
* [Použití dynamického šifrování AES-128 a služby doručování klíčů](protect-with-aes128.md)
* [Použití ochrany DRM s dynamickým šifrováním a služby doručování licencí](protect-with-drm.md)
