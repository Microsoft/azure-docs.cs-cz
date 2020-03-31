---
title: Živý přepis
titleSuffix: Azure Media Services
description: Přečtěte si o živém přepisu Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 11/19/2019
ms.author: juliako
ms.openlocfilehash: b364b6e70e3b5723c483bc3435f0c3a152c03aa9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79499876"
---
# <a name="live-transcription-preview"></a>Živý přepis (náhled)

Azure Media Service poskytuje video, zvuk a text v různých protokolech. Když publikujete živý přenos pomocí MPEG-DASH nebo HLS/CMAF, pak spolu s videem a zvukem naše služba doručí přepisovaný text v TTML kompatibilním s IMSC1.1. Dodávka je zabalena do fragmentů MPEG-4 Part 30 (ISO/IEC 14496-30). Pokud používáte doručení přes HLS/TS, pak text je dodáván jako chunked VTT.

Tento článek popisuje, jak povolit živý přepis při streamování živé události pomocí Azure Media Services v3. Než budete pokračovat, ujistěte se, že jste obeznámeni s používáním media services v3 REST API (podrobnosti najdete v [tomto kurzu).](stream-files-tutorial-with-rest.md) Měli byste být také obeznámeni s konceptem [živého streamování.](live-streaming-overview.md) Doporučujeme dokončit kurz [Stream live s mediálními službami.](stream-live-tutorial-with-api.md)

> [!NOTE]
> V současné době je živý přepis k dispozici pouze jako funkce náhledu v oblasti Západní USA 2. Podporuje přepis mluvených slov v angličtině k textu. Odkaz na rozhraní API pro tuto funkci je umístěn níže – protože je ve verzi Preview, podrobnosti nejsou k dispozici v našich dokumentech REST.

## <a name="creating-the-live-event"></a>Vytvoření živé události

Chcete-li vytvořit živou událost, odešlete operaci PUT do verze 2019-05-01-preview, například:

```
PUT https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/liveEvents/:liveEventName?api-version=2019-05-01-preview&autoStart=true 
```

Operace má následující tělo (kde je vytvořena předávací živá událost s RTMP jako protokol ingest). Všimněte si přidání vlastnosti přepisů. Jedinou povolenou hodnotou jazyka je en US.

```
{ 
  "properties": { 
    "description": "Demonstrate how to enable live transcriptions", 
    "input": { 
      "streamingProtocol": "RTMP", 
      "accessControl": { 
        "ip": { 
          "allow": [ 
            { 
              "name": "Allow All", 
              "address": "0.0.0.0", 
              "subnetPrefixLength": 0 
            } 
          ] 
        } 
      } 
    }, 
    "preview": { 
      "accessControl": { 
        "ip": { 
          "allow": [ 
            { 
              "name": "Allow All", 
              "address": "0.0.0.0", 
              "subnetPrefixLength": 0 
            } 
          ] 
        } 
      } 
    }, 
    "encoding": { 
      "encodingType": "None" 
    }, 
    "transcriptions": [ 
      { 
        "language": "en-US" 
      } 
    ], 
    "vanityUrl": false, 
    "streamOptions": [ 
      "Default" 
    ] 
  }, 
  "location": "West US 2" 
} 
```

Zosvědčovat stav živé události, dokud nepřejde do stavu "Spuštěno", což znamená, že nyní můžete odeslat příspěvek rtmp feed. Nyní můžete postupovat podle stejných kroků jako v tomto kurzu, jako je kontrola zdroje náhledu a vytváření živých výstupů.

## <a name="transcription-delivery-and-playback"></a>Doručování a přehrávání přepisů

Přečtěte si článek [dynamický přehled obalů](dynamic-packaging-overview.md#to-prepare-your-source-files-for-delivery) o tom, jak naše služba využívá dynamické balení k doručování videa, zvuku a textu v různých protokolech. Když publikujete živý přenos pomocí MPEG-DASH nebo HLS/CMAF, pak spolu s videem a zvukem naše služba doručí přepisovaný text v TTML kompatibilním s IMSC1.1. Tato dodávka je zabalena do fragmentů MPEG-4 Part 30 (ISO/IEC 14496-30). Pokud používáte doručení přes HLS/TS, pak je text doručen jako chunked VTT. K přehrávání datového proudu můžete použít webový přehrávač, jako je [azure media player.](use-azure-media-player.md)  

> [!NOTE]
> Pokud používáte Azure Media Player, použijte verzi 2.3.3 nebo novější.

## <a name="known-issues"></a>Známé problémy

Pro náhled jsou známé problémy s živým přepisem:

* Tato funkce je k dispozici pouze v západní USA 2.
* Aplikace musí používat rozhraní API preview, popsaná ve [specifikaci OpenAPI služby Media Services v3](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/preview/2019-05-01-preview/streamingservice.json).
* Jediným podporovaným jazykem je angličtina (en-us).
* S ochranou obsahu je podporováno pouze šifrování obálek AES.

## <a name="next-steps"></a>Další kroky

* [Přehled mediálních služeb](media-services-overview.md)
