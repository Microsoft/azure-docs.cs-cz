---
title: Předem sestavený odkaz na doménu – LUIS
titleSuffix: Azure Cognitive Services
description: Odkaz na předem sestavené domény, které představují předem sestavené kolekce záměrů a entit z Language Understandingch inteligentních služeb (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 17d72ad51974bb0fb741ae19ebb19f313e646c62
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/30/2019
ms.locfileid: "71677636"
---
# <a name="prebuilt-domain-reference-for-your-luis-app"></a>Předem sestavený odkaz na doménu pro aplikaci LUIS
Tento odkaz obsahuje informace o [předem sestavených doménách](luis-how-to-use-prebuilt-domains.md), které představují předem sestavené kolekce záměrů a entit, které Luis nabídky.

[Vlastní domény](luis-how-to-start-new-app.md)na rozdíl od začátku bez záměrů a modelů. Do vlastního modelu můžete přidat všechny předem připravené domény a entity.

## <a name="custom-domains-per-language"></a>Vlastní domény na jazyk

Následující tabulka shrnuje aktuálně podporované domény. Podpora angličtiny je obvykle více dokončena než jiné. 

| Typ entity       | EN-US      | ZH-CN   | Německo    | FR     | ES    | IT      | PT – BR |  JP  |      KO |        NL |    TR |
|:-----------------:|:-------:|:-------:|:-----:|:------:|:-----:|:-------:| :-------:| :-------:| :-------:| :-------:|  :-------:| 
| Kalendář  | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
|Komunikace  | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| E-mail     | ✓    | ✓       | ✓   | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| HomeAutomation          | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| Poznámky     | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| Místa   | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| RestaurantReservation  | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| ToDo     | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| Veřejné služby      | ✓    | ✓        | ✓    | ✓      | ✓     | ✓       | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| Před        | ✓    | ✓        | ✓    | ✓      | ✓     | ✓       | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| Web    | ✓    | ✓        | ✓    | ✓      | ✓     | ✓       | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |

Předem připravené domény nejsou v nástroji **podporované** :

* Francouzština (Kanada)
* Hindština
* Španělština – Mexiko

## <a name="next-steps"></a>Další kroky

Seznamte se s [jednoduchou entitou](reference-entity-simple.md). 