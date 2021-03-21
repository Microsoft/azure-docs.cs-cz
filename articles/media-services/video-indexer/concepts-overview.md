---
title: Pojmy Video Indexer – Azure
titleSuffix: Azure Media Services Video Indexer
description: Tento článek poskytuje stručný přehled Azure Media Services Video Indexer terminologie a koncepce.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 01/19/2021
ms.author: juliako
ms.openlocfilehash: 41c9dfe9251da3bddb16ff507ebd512713c3b88a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98633853"
---
# <a name="video-indexer-concepts"></a>Video Indexer koncepty

Tento článek poskytuje stručný přehled Azure Media Services Video Indexer terminologie a koncepce.

## <a name="audiovideocombined-insights"></a>Zvuk/video/kombinované přehledy

Když nahrajete videa do Video Indexer, analyzují vizuály i zvuk spuštěním různých modelů AI. Jak Video Indexer analyzuje vaše video, přehledy, které jsou extrahovány modely AI. Další informace najdete v tématu [Přehled](video-indexer-overview.md).

## <a name="confidence-scores"></a>Hodnocení spolehlivosti

Hodnocení spolehlivosti indikuje jistotu v přehledu. Je číslo mezi 0,0 a 1,0. Čím vyšší je skóre – tím větší je jistota v odpovědi. Třeba 

```json
"transcript":[
{
  "id":1,
  "text":"Well, good morning everyone and welcome to",
  "confidence":0.8839,
  "speakerId":1,
  "language":"en-US",
  "instances":[
     {
    "adjustedStart":"0:00:10.21",
    "adjustedEnd":"0:00:12.81",
    "start":"0:00:10.21",
    "end":"0:00:12.81"
     }
  ]
},
```

## <a name="content-moderation"></a>Moderování obsahu

Pomocí textových a vizuálních modelů pro moderování obsahu můžete udržet uživatele v bezpečí před nevhodným obsahem a ověřit, že obsah, který publikujete, odpovídá hodnotám vaší organizace. Můžete automaticky blokovat určitá videa nebo upozornit uživatele na obsah. Další informace najdete v tématu [přehledy: moderování vizuálního a textového obsahu](video-indexer-output-json-v2.md#visualcontentmoderation). 

## <a name="blocks"></a>Bloky   

Jsou určeny k tomu, aby bylo snazší projít data. Blok může být rozdělený třeba podle toho, jak se mění přednášející, nebo když nastane dlouhá pauza.  

## <a name="project-and-editor"></a>Projekt a Editor

Web [video indexer](https://www.videoindexer.ai/) umožňuje použít hloubkové přehledy videa k: vyhledání správného mediálního obsahu, vyhledání částí, které vás zajímají, a použití výsledků k vytvoření zcela nového projektu. Po vytvoření se projekt dá vykreslit a stáhnout z Video Indexer a použije se ve svých vlastních aplikacích pro úpravy nebo v pracovních postupech.

Tato funkce může být užitečná v některých případech: 

* Vytváření zvýraznění filmů pro přípojná místa.
* Použití starých klipů videí v přetypováních zpráv.
* Vytváření kratšího obsahu pro sociální média

Další informace najdete v tématu [vytvoření projektů pomocí editoru](use-editor-create-project.md).

## <a name="keyframes"></a>Klíčové snímky

Video Indexer vybere rámce, které nejlépe reprezentují každý snímek. Klíčové snímky jsou reprezentativní snímky vybrané z celého videa na základě vlastností estetického zobrazení (například kontrast a stabilita). Další informace najdete v tématu [scény, snímky a klíčové snímky](scenes-shots-keyframes.md).

## <a name="time-range-vs-adjusted-time-range"></a>časový rozsah vs. upravený časový rozsah   

TimeRange je časovým rozsahem v původním videu. AdjustedTimeRange je časový rozsah relativní k aktuálnímu seznamu testů. Vzhledem k tomu, že můžete vytvořit seznam stop z různých řádků různých videí, můžete použít 1 hodiny videa a použít na něj pouze 1 čáru, například 10:00-10:15. V takovém případě budete mít seznam stop s 1 řádkem, kde časový rozsah je 10:00-10:15, ale adjustedTimeRange je 00:00-00:15. 

## <a name="widgets"></a>Widgety

Video Indexer podporuje vkládání widgetů do vašich aplikací. Další informace najdete v tématu [vkládání video indexer widgetů ve vašich aplikacích](video-indexer-embed-widgets.md).

## <a name="summarized-insights"></a>Shrnuté přehledy  

Shrnuté přehledy obsahují agregované zobrazení dat: plošky, témata a emoce. Například místo toho, aby se načetly jednotlivé tisíce časových rozsahů a kontrolovaly, které plošky jsou v něm, shrnuté přehledy obsahují všechny plošky a pro každý z nich, časové rozsahy, ve kterých se zobrazuje, a% času, ve kterém se zobrazí.  

## <a name="next-steps"></a>Další kroky

- [přehled](video-indexer-overview.md)
- [Přehledy](video-indexer-output-json-v2.md)
