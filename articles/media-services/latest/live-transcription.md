---
title: Živý přepis
titleSuffix: Azure Media Services
description: Přečtěte si o Azure Media Services živý přepis.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "79499876"
---
# <a name="live-transcription-preview"></a>Živý přepis (Preview)

Služba Azure Media Service nabízí video, zvuk a text v různých protokolech. Když publikujete živý datový proud pomocí MPEG-POMLČKy nebo HLS/CMAF, pak společně s videem a zvukem doručí naše služba přepisu text v IMSC 1.1 kompatibilní TTML. Doručení se zabalí do fragmentů MPEG-4 Part 30 (ISO/IEC 14496-30). Pokud používáte doručování přes HLS/TS, text se doručí jako VTTý v bloku.

Tento článek popisuje, jak povolit živý přepis při streamování živé události s Azure Media Services V3. Než budete pokračovat, ujistěte se, že jste obeznámeni s používáním rozhraní REST API pro Media Services V3 (podrobnosti najdete v [tomto kurzu](stream-files-tutorial-with-rest.md) ). Měli byste se také seznámit s konceptem [živého streamování](live-streaming-overview.md) . Při Media Services kurzu se doporučuje dokončit [Stream živě](stream-live-tutorial-with-api.md) .

> [!NOTE]
> V současné době je živý přepis dostupný jenom jako funkce Preview v oblasti Západní USA 2. Podporuje přepis mluvených slov v angličtině a textu. Referenční informace k rozhraní API pro tuto funkci najdete tady: protože je ve verzi Preview. podrobnosti nejsou k dispozici u našich dokumentů REST.

## <a name="creating-the-live-event"></a>Vytváří se živá událost.

Chcete-li vytvořit živou událost, odešlete operaci PUT do verze 2019-05-01-Preview, například:

```
PUT https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/liveEvents/:liveEventName?api-version=2019-05-01-preview&autoStart=true 
```

Operace má následující text (kde se vytvoří předávací živá událost s RTMP jako protokol ingestování). Všimněte si přidání vlastnosti přepisy. Jediná povolená hodnota pro jazyk je en-US.

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

Cyklické dotazování na stav živé události, dokud nepřejde do stavu spuštěno, což znamená, že teď můžete poslat příspěvek RTMP. Nyní můžete postupovat stejným způsobem jako v tomto kurzu, jako je například kontrola kanálu Preview a vytváření živých výstupů.

## <a name="transcription-delivery-and-playback"></a>Přepisovat doručování a přehrávání

Přečtěte si článek [Přehled dynamického balení](dynamic-packaging-overview.md#to-prepare-your-source-files-for-delivery) , jak naše služba používá dynamické balení k doručování videa, zvuku a textu v různých protokolech. Když publikujete živý datový proud pomocí MPEG-POMLČKy nebo HLS/CMAF, pak společně s videem a zvukem doručí naše služba přepisu text v IMSC 1.1 kompatibilní TTML. Toto doručení se zabalí do fragmentů MPEG-4 Part 30 (ISO/IEC 14496-30). Pokud používáte doručování přes HLS/TS, text se doručuje jako VTT v bloku. K přehrání datového proudu můžete použít webový přehrávač, například [Azure Media Player](use-azure-media-player.md) .  

> [!NOTE]
> Pokud používáte Azure Media Player, použijte verzi 2.3.3 nebo novější.

## <a name="known-issues"></a>Známé problémy

Pro verzi Preview jsou zde známé problémy s živým přepisem:

* Tato funkce je k dispozici pouze v Západní USA 2.
* Aplikace musí používat rozhraní API verze Preview, které jsou popsané ve [specifikaci Media Services V3 openapi Specification](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/preview/2019-05-01-preview/streamingservice.json).
* Jediným podporovaným jazykem je angličtina (EN-US).
* S ochranou obsahu je podporována pouze šifrování obálek AES.

## <a name="next-steps"></a>Další kroky

* [Přehled Media Services](media-services-overview.md)
