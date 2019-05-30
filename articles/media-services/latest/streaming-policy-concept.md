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
ms.date: 05/28/2019
ms.author: juliako
ms.openlocfilehash: a813c77e81e51bfe13e75ed6c8d0e24b4d0fa645
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/30/2019
ms.locfileid: "66392930"
---
# <a name="streaming-policies"></a>Zásady streamování

V Azure Media Services v3 [streamování zásady](https://docs.microsoft.com/rest/api/media/streamingpolicies) vám umožňují definovat protokolů streamování a možnosti šifrování pro vaše [lokátory streamování](streaming-locators-concept.md). Media Services v3 poskytuje že některé předdefinované zásady streamování, tak, aby je mohli používat přímo pro zkušební verzi nebo produkční prostředí. 

Aktuálně k dispozici předdefinované zásady streamování:<br/>
* "Predefined_DownloadOnly.
* "Predefined_ClearStreamingOnly.
* 'Predefined_DownloadAndClearStreaming'
* "Predefined_ClearKey.
* 'Predefined_MultiDrmCencStreaming' 
* 'Predefined_MultiDrmStreaming'

Následující "rozhodovací strom" pomáhá výběru předdefinovaných datových proudů zásady pro váš scénář.

> [!IMPORTANT]
> * Vlastnosti **streamování zásady** jsou DateTime typu jsou vždy ve formátu UTC.
> * Navrhněte omezenou sadu zásad pro svůj účet Media Service a je znovu použít pro vaše lokátory streamování pokaždé, když jsou potřeba stejné možnosti. Další informace najdete v tématu [kvóty a omezení](limits-quotas-constraints.md).

## <a name="decision-tree"></a>Rozhodovací strom

Kliknutím na obrázek zobrazíte jeho plnou velikost.  

<a href="./media/streaming-policy/large.png" target="_blank"><img src="./media/streaming-policy/large.png"></a> 

Pokud šifrování obsahu, je potřeba vytvořit [zásad klíče k obsahu](content-key-policy-concept.md), **zásad klíče k obsahu** není potřeba pro streamování nebo stažení. 

Pokud nemáte speciální požadavky (např. Pokud chcete zadat různé protokoly, potřebují používat službu vlastní doručení klíče, nebo muset použít Vymazat zvukové stopy), můžete si [vytvořit](https://docs.microsoft.com/rest/api/media/streamingpolicies/create) vlastní zásady streamování. 

## <a name="get-a-streaming-policy-definition"></a>Získat definici zásady streamování  

Pokud chcete zobrazit definici zásady streamování, používat [získat](https://docs.microsoft.com/rest/api/media/streamingpolicies/get) a zadejte název zásady. Příklad:

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

Zobrazit [filtrování, řazení, stránkování, Media Services entit](entities-overview.md).

## <a name="next-steps"></a>Další postup

* [Streamování souboru](stream-files-dotnet-quickstart.md)
* [Použití dynamického šifrování AES-128 a doručení klíče služby](protect-with-aes128.md)
* [Pomocí DRM dynamického šifrování a licence služby pro doručování](protect-with-drm.md)
