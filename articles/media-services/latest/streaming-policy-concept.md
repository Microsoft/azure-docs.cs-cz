---
title: Zásady streamování ve službě Azure Media Services | Dokumenty společnosti Microsoft
description: Tento článek poskytuje vysvětlení, co jsou zásady streamování a jak jsou používány službou Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 05/28/2019
ms.author: juliako
ms.openlocfilehash: a813c77e81e51bfe13e75ed6c8d0e24b4d0fa645
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "66392930"
---
# <a name="streaming-policies"></a>Zásady streamování

Ve službě Azure Media Services v3 umožňují [zásady streamování](https://docs.microsoft.com/rest/api/media/streamingpolicies) definovat protokoly streamování a možnosti šifrování pro [lokátory streamování](streaming-locators-concept.md). Media Services v3 poskytuje některé předdefinované zásady streamování, takže je můžete použít přímo pro zkušební verzi nebo produkční prostředí. 

Aktuálně dostupné předdefinované zásady streamování:<br/>
* "Predefined_DownloadOnly"
* "Predefined_ClearStreamingOnly"
* "Predefined_DownloadAndClearStreaming"
* "Predefined_ClearKey"
* "Predefined_MultiDrmCencStreaming" 
* "Predefined_MultiDrmStreaming"

Následující "Rozhodovací strom" vám pomůže zvolit předdefinované zásady streamování pro váš scénář.

> [!IMPORTANT]
> * Vlastnosti **zásad streamování,** které jsou typu Datetime, jsou vždy ve formátu UTC.
> * Měli byste navrhnout omezenou sadu zásad pro váš účet Media Service a znovu je použít pro lokátory streamování, kdykoli jsou potřeba stejné možnosti. Další informace naleznete v [tématu Kvóty a omezení](limits-quotas-constraints.md).

## <a name="decision-tree"></a>Rozhodovací strom

Kliknutím na obrázek zobrazíte jeho plnou velikost.  

<a href="./media/streaming-policy/large.png" target="_blank"><img src="./media/streaming-policy/large.png"></a> 

Pokud šifrujete obsah, musíte vytvořit [zásady klíče obsahu](content-key-policy-concept.md), **zásady klíče obsahu** nejsou potřebné pro jasné streamování nebo stahování. 

Pokud máte zvláštní požadavky (například pokud chcete zadat různé protokoly, potřebujete použít vlastní službu doručování klíčů nebo potřebujete použít vymazat zvukovou stopu), můžete [vytvořit](https://docs.microsoft.com/rest/api/media/streamingpolicies/create) vlastní zásady streamování. 

## <a name="get-a-streaming-policy-definition"></a>Získání definice zásad streamování  

Pokud chcete zobrazit definici zásad streamování, použijte [získat](https://docs.microsoft.com/rest/api/media/streamingpolicies/get) a zadejte název zásady. Například:

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

Viz [Filtrování, řazení, stránkování entit Mediálních služeb](entities-overview.md).

## <a name="next-steps"></a>Další kroky

* [Streamování souboru](stream-files-dotnet-quickstart.md)
* [Používejte dynamické šifrování AES-128 a službu doručování klíčů](protect-with-aes128.md)
* [Použití ochrany DRM s dynamickým šifrováním a služby doručování licencí](protect-with-drm.md)
