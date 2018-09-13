---
title: Azure Content Moderator – moderování videa | Dokumentace Microsoftu
description: Použít s podporou počítače moderování videa a nástroje pro recenze prováděné lidmi, obscénnost nevhodný obsah
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 01/20/2018
ms.author: sajagtap
ms.openlocfilehash: d9c01d4c2590535a4106e8e4ee79a12bdc60d956
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/12/2018
ms.locfileid: "44714492"
---
# <a name="video-moderation"></a>Moderování videa

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
