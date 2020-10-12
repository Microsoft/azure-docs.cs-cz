---
title: Moderování videa pomocí recenze pro člověka – Content Moderator
titleSuffix: Azure Cognitive Services
description: Použití moderování videa s asistencí počítače a nástroje pro kontrolu k mírnému nevhodnému obsahu
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: pafarley
ms.openlocfilehash: 0c031a890efc7fad7e5d9caefce3b0e66c515d90
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "81404241"
---
# <a name="video-moderation-with-human-review"></a>Moderování videa díky lidské kontrole

K dosažení nejlepších výsledků pro vaši firmu použijte nástroj pro [moderování](video-moderation-api.md) a [kontrolu](Review-Tool-User-Guide/human-in-the-loop.md) videa, který je Content moderator s podporou počítačů, a získejte tak střední videa a Přepisy obsahu pro dospělé (explicitní) a pikantní (sugestivní).

## <a name="video-trained-classifier-preview"></a>Třídění učené videem (Preview)

Klasifikace videa s asistencí počítače se dosáhla i u modelů vyškolených imagí nebo na základě videí. Na rozdíl od klasifikátorů pro video, které jsou v obrazech vyškolené, je k dispikantníi videa od Microsoftu a jeho třídění. Tato metoda má za následek lepší shodu kvality.

## <a name="shot-detection"></a>Detekce snímku

Když vydáte podrobnosti klasifikace, další video analýza pomáhá s větší flexibilitou při analýze videí. Služba pro moderování videa od Microsoftu poskytuje také informace na úrovni snímku. Teď máte možnost analyzovat vaše videa na úrovni snímku a na úrovni snímků.

## <a name="key-frame-detection"></a>Detekce klíčových snímků

Místo vyplňování snímků v pravidelných intervalech služba pro moderování videa identifikuje a produkuje pouze potenciálně kompletní (dobré) snímky. Tato funkce umožňuje efektivní generování snímků pro dospělé a pikantní analýzu na úrovni snímků.

Následující extrakce ukazuje částečnou odpověď s potenciálními snímky, klíčovými snímky a pikantní a výsledky pro dospělé:

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

Pro další odlišit případy potřebují firmy řešení pro lidské recenze pro vykreslování videa, jeho rámců a značek přiřazených k počítači. Lidé, kteří si kontrolují videa a snímky, získají kompletní pohled na přehledy, změny značek a odeslání jejich rozhodnutí.

![výchozí zobrazení nástroje pro kontrolu videa](images/video-review-default-view.png)

## <a name="player-view-for-video-level-review"></a>Zobrazení přehrávače pro kontrolu na úrovni videa

Binární rozhodnutí na úrovni videa se umožňují pomocí zobrazení přehrávače videa, které zobrazuje potenciální dospělé a pikantní snímky. Recenzenti přecházejí na video s různými možnostmi rychlosti, aby prozkoumali scény. Potvrdili si jejich rozhodnutí tím, že je vypřepínáním značek.

![zobrazení přehrávače nástrojů pro kontrolu videa](images/video-review-player-view.PNG)

## <a name="frames-view-for-detailed-reviews"></a>Zobrazení snímků pro podrobné Revize

Podrobná kontrola videí pro analýzu snímků po snímku je umožněna pomocí zobrazení založeného na snímcích. Recenzenti kontrolují výběr jednoho nebo více rámců a přepnuli si jejich rozhodnutí. Volitelným dalším krokem je redigování urážlivých rámců nebo obsahu.

![zobrazení snímků nástroje pro kontrolu videa](images/video-review-frames-view-apply-tags.PNG)

## <a name="transcript-moderation"></a>Moderování přepisu

Videa obvykle využívají hlas nad tím, že se vyžaduje moderování a také urážlivý hlas. Služba Azure Media Indexer slouží k převodu řeči na text Content Moderator a k odeslání přepisu pro moderování textu v rámci nástroje pro kontrolu.

![zobrazení přepisu nástroje pro kontrolu videa](images/video-review-transcript-view.png)

## <a name="next-steps"></a>Další kroky

- Začněte s [rychlým startem pro moderování videa](video-moderation-api.md).
- Naučte se generovat [recenze videí](video-reviews-quickstart-dotnet.md) pro své lidské kontrolory ze svého moderovaného výstupu.
- Přidejte [recenze přepisu videa](video-transcript-reviews-quickstart-dotnet.md) do recenze videí.
- Podrobné informace o tom, jak vyvíjet [kompletní řešení pro moderování videa](video-transcript-moderation-review-tutorial-dotnet.md), najdete v podrobném kurzu.