---
title: Moderování videa s lidskou kontrolu - Content Moderatoru
titlesuffix: Azure Cognitive Services
description: Použít s podporou počítače moderování videa a nástroje pro recenze prováděné lidmi, obscénnost nevhodný obsah
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 01/20/2018
ms.author: sajagtap
ms.openlocfilehash: 43a43ddcbfc656a3eb5a274e1bb63a473b7c89a2
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55867711"
---
# <a name="video-moderation-with-human-review"></a>Moderování videa s recenze prováděné lidmi

Použití Content Moderator s podporou počítače [moderování videa](video-moderation-api.md) a [nástroj pro recenze prováděné lidmi](Review-Tool-User-Guide/human-in-the-loop.md) moderování videa a záznamy o studiu pro dospělé (explicitně) a pikantní obsah (sugestivní) k dosažení nejlepších výsledků pro vaši firmu.

## <a name="video-trained-classifier-preview"></a>Třídění školení videa (preview)

S podporou počítače videa klasifikace se buď dosahuje prostřednictvím image Trénink modelů nebo video trénované modely. Na rozdíl od školení obrázku videa třídění společnosti Microsoft pro dospělé nebo pikantního videa třídění natrénovaný pomocí videa. Tato metoda má za následek lepší kvalitu shody.

## <a name="shot-detection"></a>Detekci

Při výstupu podrobnosti klasifikace, další videa inteligentní funkce pomáhají s větší flexibilitou videí analýzy. Místo výstupu pouze snímky, poskytuje služba moderování videa příliš informace na úrovni snímku. Teď máte možnost analyzovat vaše videa na úrovni snímek a snímek.
 
## <a name="key-frame-detection"></a>Detekce klíčových snímků

Služba moderování videa místo výstupu rámce v pravidelných intervalech, identifikuje a vypíše pouze potenciálně kompletní snímky (dobré). Tato funkce umožňuje generování efektivní rámce pro analýzu pro dospělé nebo pikantního úrovni rámce.

Následující výpis ukazuje částečné odpovědi s potenciálním snímky, klíčových snímků a pro dospělé nebo pikantního skóre:

    "fragments": [
    {
      "start": 0,
      "duration": 18000
    },
    {
      "start": 18000,
      "duration": 3600,
      "interval": 3600,
      "events": [
        [
          {
            "reviewRecommended": false,
            "adultScore": 0.00001,
            "racyScore": 0.03077,
            "index": 5,
            "timestamp": 18000,
            "shotIndex": 0
          }
        ]
      ]
    },
    {
      "start": 18386372,
      "duration": 119149,
      "interval": 119149,
      "events": [
        [
          {
            "reviewRecommended": true,
            "adultScore": 0.00000,
            "racyScore": 0.91902,
            "index": 5085,
            "timestamp": 18386372,
            "shotIndex": 62
          }
        ]
      ]


## <a name="visualization-for-human-reviews"></a>Vizualizace pro recenze prováděné lidmi

Další informace odlišování případy, podniky potřebují řešení recenze prováděné lidmi pro vykreslení videa, snímky a značky počítač přiřazený. Lidských moderátorů kontrola videa a snímků získat ucelený pohled přehledy, upravit značky a odešlete své rozhodnutí.

![Video revize nástroj výchozí zobrazení](images/video-review-default-view.png)

## <a name="player-view-for-video-level-review"></a>Zobrazení Player ke kontrole úrovni videa

Binární rozhodování o úrovni videa jsou možné zobrazení videopřehrávače, který znázorňuje potenciální pro dospělé nebo pikantního snímků. Lidské revidující přejděte na video s různé možnosti rychlost prozkoumat informace pro pokročilé uživatele. Potvrzení rozhodnutí poznamenáno přepnutím značky.

![zobrazení přehrávač videa kontroly nástroje](images/video-review-player-view.PNG)

## <a name="frames-view-for-detailed-reviews"></a>Zobrazení snímků pro podrobné revize

Podrobná videa revize pro analýzu snímků snímků je možné se zobrazením založených na snímcích. Lidské revidující zkontrolujte a vyberte jeden nebo více snímků a značky pro potvrzení rozhodnutí poznamenáno přepínat. Volitelné dalším krokem je redigování urážlivé snímků nebo obsahu.

![zobrazení snímků nástroje video revize](images/video-review-frames-view-apply-tags.PNG)

## <a name="transcript-moderation"></a>Moderování přepisu

Videa obvykle mají hlasové, které potřebuje moderování i pro urážlivé řeči. Pomocí služby Azure Media Indexer umožňuje převádět řeč na text a použijte k odeslání přepisu pro moderování textu v rámci nástroje pro recenze rozhraní API Content Moderatoru revize.

![zobrazení přepisu videa kontroly nástroje](images/video-review-transcript-view.png)

## <a name="next-steps"></a>Další postup

Začínáme s [moderování videa quickstart](video-moderation-api.md). 

Zjistěte, jak generovat [video kontroly](video-reviews-quickstart-dotnet.md) pro vaše recenzenty lidí z moderované výstupu.

Přidat [přepis videa kontroly](video-transcript-reviews-quickstart-dotnet.md) na video kontroly.

Podívejte se na podrobný kurz o tom, jak vyvíjet [dokončení moderování videa řešení](video-transcript-moderation-review-tutorial-dotnet.md). 
