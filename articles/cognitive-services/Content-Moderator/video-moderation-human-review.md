---
title: Moderování videa s lidskou recenzí - Content Moderator
titleSuffix: Azure Cognitive Services
description: K moderování nevhodného obsahu používejte nástroje pro moderování videa s pomocí stroje a lidské kontroly
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 04/30/2019
ms.author: pafarley
ms.openlocfilehash: a4e7b079367a4b4dec1d2b3d6c0afde1d8276766
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "72754158"
---
# <a name="video-moderation-with-human-review"></a>Moderování videa s lidskou recenzí

Pomocí nástroje [pro moderování videa](video-moderation-api.md) s pomocí počítače moderátora obsahu a [nástroje pro lidskou kontrolu](Review-Tool-User-Guide/human-in-the-loop.md) můžete moderovat videa a přepisy pro obsah pro dospělé (explicitní) a pikantní (sugestivní) pro dosažení nejlepších výsledků pro vaši firmu.

## <a name="video-trained-classifier-preview"></a>Klasifikátor vyškolený pro video (náhled)

Klasifikace videa s pomocí strojů je buď dosažena u modelů trénovaných obrazem, nebo u modelů s videotrénovanými. Na rozdíl od klasifikátory videa s refikací videa pro obrazové) jsou klasifikátory videa společnosti Microsoft pro dospělé a pikantní video školy. Tato metoda má za následek lepší kvalitu shody.

## <a name="shot-detection"></a>Detekce střel

Při vyřazování podrobností o klasifikaci pomáhá další inteligentní video s větší flexibilitou při analýze videí. Namísto předávací pouze snímky, Microsoft video moderování služba poskytuje shot-úrovni informace příliš. Nyní máte možnost analyzovat vaše videa na úrovni záběru a na úrovni snímků.

## <a name="key-frame-detection"></a>Detekce klíčových snímků

Namísto výstupu snímků v pravidelných intervalech služba moderování videa identifikuje a výstupy pouze potenciálně kompletní (dobré) snímky. Tato funkce umožňuje efektivní generování rámu pro analýzu na úrovni rámu pro dospělé a pikantní analýzu.

Následující výňatek ukazuje částečnou odezvu s potenciálními snímky, klíčovými snímky a skóre pro dospělé a pikantní skóre:

```json
"fragments":[  
  {  
    "start":0,
    "duration":18000
  },
  {  
    "start":18000,
    "duration":3600,
    "interval":3600,
    "events":[  
      [  
        {  
          "reviewRecommended":false,
          "adultScore":0.00001,
          "racyScore":0.03077,
          "index":5,
          "timestamp":18000,
          "shotIndex":0
        }
      ]
    ]
  },
  {  
    "start":18386372,
    "duration":119149,
    "interval":119149,
    "events":[  
      [  
        {  
          "reviewRecommended":true,
          "adultScore":0.00000,
          "racyScore":0.91902,
          "index":5085,
          "timestamp":18386372,
          "shotIndex":62
        }
      ]
    ]
```

## <a name="visualization-for-human-reviews"></a>Vizualizace pro lidské recenze

Pro více diferencovaných případů potřebují firmy řešení lidské recenze pro vykreslení videa, jeho snímků a strojově přiřazených značek. Moderátoři lidských lidí, kteří kontrolují videa a rámečky, získají úplný přehled o přehledech, změní značky a odešlou svá rozhodnutí.

![výchozí zobrazení nástroje pro kontrolu videa](images/video-review-default-view.png)

## <a name="player-view-for-video-level-review"></a>Zobrazení přehrávače pro kontrolu na úrovni videa

Binární rozhodnutí na úrovni videa jsou možná díky zobrazení přehrávače videa, které zobrazuje potenciální snímky pro dospělé a pikantní snímky. Lidoví recenzenti projíždí video s různými možnostmi rychlosti, aby prozkoumali scény. Potvrzují svá rozhodnutí přepnutím visaček.

![zobrazení nástroje pro kontrolu videa](images/video-review-player-view.PNG)

## <a name="frames-view-for-detailed-reviews"></a>Zobrazení rámců pro podrobné recenze

Podrobná recenze videa pro analýzu snímek po snímku je možná s pohledem založeným na snímku. Lidoví recenzenti zkontrolují a vyberou jeden nebo více rámců a přepínají značky, aby potvrdili svá rozhodnutí. Volitelným dalším krokem je redigování útočných snímků nebo obsahu.

![Zobrazení snímků nástrojů pro recenzování videa](images/video-review-frames-view-apply-tags.PNG)

## <a name="transcript-moderation"></a>Moderování přepisu

Videa mají obvykle hlas, který vyžaduje moderování i pro urážlivé řeči. Službu Azure Media Indexer slouží k převodu řeči na text a k odeslání přepisu pro moderování textu v nástroji pro kontrolu pomocí rozhraní API pro kontrolu moderátora obsahu.

![zobrazení nástroje pro přepis videa](images/video-review-transcript-view.png)

## <a name="next-steps"></a>Další kroky

- Začněte s [rychlým startem moderování videa](video-moderation-api.md).
- Přečtěte si, jak generovat [recenze videí](video-reviews-quickstart-dotnet.md) pro lidské recenzenty z moderovaného výstupu.
- Přidejte [recenze přepisu videa](video-transcript-reviews-quickstart-dotnet.md) do svých recenzí videí.
- Podívejte se na podrobný návod, jak vyvinout [kompletní video moderování řešení](video-transcript-moderation-review-tutorial-dotnet.md).