---
title: Azure obsahu moderátora - Video přerušování | Microsoft Docs
description: Střední nevhodný obsah pomocí s asistencí počítač video přerušování a lidské zkontrolujte nástroje
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 01/20/2018
ms.author: sajagtap
ms.openlocfilehash: fb26c9af55381c80a3f520b1a0068d8f72c91061
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342414"
---
# <a name="video-moderation"></a>Moderování videa

Moderátora obsahu použít počítač s asistencí [video přerušování](video-moderation-api.md) a [lidské Zkontrolujte nástroj](Review-Tool-User-Guide/human-in-the-loop.md) střední videa a přepisy pro dospělé (explicitní) a zájem (sugestivní) obsah tak, aby co nejlépe pro vaší firmy.

## <a name="video-trained-classifier"></a>Cvičení video třídění

Počítače s asistencí video klasifikace buď dosáhnout s modely image cvičení nebo video trénované modely. Na rozdíl od Trénink image video třídění je společnosti Microsoft pro dospělé a zájem video třídění trénink na videa. Tato metoda je výsledkem lepší kvalitu shody.

## <a name="shot-detection"></a>Snímek detekce

Při výstupu podrobnosti klasifikace, další video intelligence pomáhá větší flexibilitu v Analýza videa. Místo výstup vytvořeného právě rámce, poskytuje služby společnosti Microsoft video přerušování příliš informace na úrovni snímek. Nyní máte možnost analyzovat videa na úroveň snímek a na úrovni rámce.
 
## <a name="key-frame-detection"></a>Detekce klíčových snímků

Místo výstupu rámce v pravidelných intervalech, službu video přerušování identifikuje a výstupy pouze potenciálně dokončení rámce (dobré). Tato funkce umožňuje efektivní rámce generování úrovni rámce pro dospělé a zájem analýzy.

Následující extract ukazuje částečné odpovědi s potenciálním snímky, klíčových snímků a pro dospělé a zájem skóre:

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


## <a name="visualization-for-human-reviews"></a>Vizualizaci lidského recenze

Další informace nuanced případech podnikům potřebovat lidského zkontrolujte řešení pro vykreslování videa, jeho snímky a počítač přiřazen značky. Lidské moderátorů kontrole videa a rámce získat úplné zobrazení k přehledům, změňte značky a odeslání jejich rozhodnutí.

![Video kontrolní nástroj pro výchozí zobrazení](images/video-review-default-view.png)

## <a name="player-view-for-video-level-review"></a>Zobrazení Player ke kontrole úrovni video

Video úrovni binární rozhodnutí, která jsou vytvářeny možné pomocí zobrazení přehrávání videa, které obsahuje potenciální rámce pro dospělé a zájem. Lidské kontroloři přejděte na video s různé možnosti rychlost prozkoumat na pozadí. Potvrzení jejich rozhodnutí přepnutím značek.

![Video Zkontrolujte nástroj player zobrazení](images/video-review-player-view.PNG)

## <a name="frames-view-for-detailed-reviews"></a>Zobrazení rámce pro podrobné recenze

Kontrolu podrobné videa pro analýzu jednotlivých snímcích je možné s zobrazení na základě snímků. Lidské kontroloři zkontrolujte a vyberte jeden nebo více snímků a přepínání značky potvrďte své rozhodnutí. Volitelné dalším krokem je redigování urážlivé rámce nebo obsahu.

![Video Zkontrolujte nástroj rámce zobrazení](images/video-review-frames-view-apply-tags.PNG)

## <a name="transcript-moderation"></a>Přepis přerušování

Videa obvykle mají hlasové přes které potřebuje přerušování také pro urážlivé řeči. Používat službu Azure Media Indexer převod řeči na text a použít obsahu moderátora zkontrolujte API k odeslání přepis pro přerušování text v rámci nástroje revize.

![Video kontrolní nástroj pro přepis zobrazení](images/video-review-transcript-view.png)

## <a name="next-steps"></a>Další postup

Začínáme s [rychlý start video přerušování](video-moderation-api.md). 

Zjistěte, jak vygenerovat [video zkontroluje](video-reviews-quickstart-dotnet.md) pro lidské recenzenti z moderované výstupu.

Přidat [video přepis zkontroluje](video-transcript-reviews-quickstart-dotnet.md) k vaší video recenze.

Podívejte se na podrobný kurz o tom, jak vyvíjet [dokončení video přerušování řešení](video-transcript-moderation-review-tutorial-dotnet.md). 
