---
title: Azure Media Services živý přepis | Microsoft Docs
description: Tento článek vysvětluje, co je Azure Media Services živý přepis.
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
ms.openlocfilehash: 875d8f498fc8405a53d8fb60631c52ce09b30a62
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75431931"
---
# <a name="live-transcription-preview"></a>Živý přepis (Preview)

Azure Media Service nabízí video, zvuk a teď text v různých protokolech. Když publikujete živý stream pomocí MPEG-POMLČKy nebo HLS/CMAF, pak společně s videem a zvukem doručí naše služba text přepisu v IMSC 1.1 kompatibilní TTML, zabalený na fragmenty MPEG-4 Part 30 (ISO/IEC 14496-30). Pokud používáte doručování přes HLS/TS, text se doručí jako VTTý v bloku. 

Tento článek popisuje, jak povolit živý přepis při streamování živé události s Azure Media Services V3. Než budete pokračovat, ujistěte se, že jste obeznámeni s používáním rozhraní REST API Media Services V3 (podrobnosti najdete v [tomto kurzu](stream-files-tutorial-with-rest.md) ). Měli byste se také seznámit s konceptem [živého streamování](live-streaming-overview.md) . Při Media Services kurzu se doporučuje dokončit [Stream živě](stream-live-tutorial-with-api.md) . 

> [!NOTE]
> V současné době je živý přepis dostupný jenom jako funkce Preview v oblasti Západní USA 2. Podporuje přepis mluvených slov v angličtině a textu. Odkaz na rozhraní API této funkce je v tomto dokumentu – protože je ve verzi Preview, podrobnosti nejsou k dispozici u našich dokumentů REST. 

## <a name="creating-the-live-event"></a>Vytváří se živá událost. 

Chcete-li vytvořit živou událost, měli byste odeslat operaci PUT do verze 2019-05-01, například: 

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

Měli byste se dotázat na stav živé události, dokud nepřejde do stavu spuštěno, což znamená, že teď můžete poslat příspěvek RTMP. Nyní můžete postupovat stejným způsobem jako v tomto kurzu, jako je například kontrola kanálu náhledu a vytváření živých výstupů. 

## <a name="delivery-and-playback"></a>Doručení a přehrávání 

Přečtěte si článek [Přehled dynamického balení](dynamic-packaging-overview.md#to-prepare-your-source-files-for-delivery) , jak naše služba používá dynamické balení k doručování videa, zvuku a textu v různých protokolech. Když publikujete živý stream pomocí MPEG-POMLČKy nebo HLS/CMAF, pak společně s videem a zvukem doručí naše služba text přepisu v IMSC 1.1 kompatibilní TTML, zabalený na fragmenty MPEG-4 Part 30 (ISO/IEC 14496-30). Pokud používáte doručování přes HLS/TS, text se doručí jako VTTý v bloku. K přehrání datového proudu můžete použít webový přehrávač, například [Azure Media Player](use-azure-media-player.md) .  

> [!NOTE]
>  Pokud používáte Azure Media Player, použijte verzi 2.3.3 nebo novější.

## <a name="known-issues"></a>Známé problémy 

Ve verzi Preview jsou tady uvedené známé problémy s živým přepisem. 

* Tato funkce je k dispozici pouze v Západní USA 2.
* Aplikace musí používat rozhraní API verze Preview popsané ve specifikaci [Media Services V3 openapi Specification](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/preview/2019-05-01-preview/streamingservice.json) .
* Jediným podporovaným jazykem je angličtina (EN-US).
* V souvislosti s ochranou obsahu je podporována pouze šifrování obálek AES.

## <a name="next-steps"></a>Další kroky

[Přehled Media Services](media-services-overview.md)
