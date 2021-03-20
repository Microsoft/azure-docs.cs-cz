---
title: Parametry řetězce dotazu rozhraní V3 API
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: include
ms.date: 06/30/2020
ms.openlocfilehash: 14380dd781fc1eebfe7edb0a816ff8af0f2f17b4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "91309420"
---
Parametry řetězce dotazu rozhraní V3 API zahrnují:

|Parametr dotazu|Název portálu LUIS|Typ|Verze|Výchozí|Účel|
|--|--|--|--|--|--|
|`log`|Uložit protokoly|boolean|V2 & V3|false (nepravda)|Dotaz na uložení v souboru protokolu. Výchozí hodnota je false.|
|`query`|-|řetězec|Jenom V3|Žádná výchozí hodnota – v žádosti GET se vyžaduje.|**V v2** je utterance, který má být předpovězen, v `q` parametru. <br><br>**V v3** je funkce předána do `query` parametru.|
|`show-all-intents`|Zahrnout skóre pro všechny záměry|boolean|Jenom V3|false (nepravda)|Vrátí všechny záměry s odpovídajícím skóre v objektu **předpověď. záměrs** . Záměry jsou vráceny jako objekty v nadřazeném `intents` objektu. To umožňuje programový přístup bez nutnosti najít záměr v poli: `prediction.intents.give` . V v2 byly vráceny v poli. |
|`verbose`|Zahrnout další podrobnosti o entitách|boolean|V2 & V3|false (nepravda)|**V v2** při nastavení na hodnotu true byly vráceny všechny předpovězené záměry. Pokud potřebujete všechny předpovídané záměry, použijte parametr V3 `show-all-intents` .<br><br>**V v3** tento parametr poskytuje pouze podrobnosti o metadatech entit pro předpovědi entit.  |
|`timezoneOffset`|-|řetězec|V2|-|Časové pásmo bylo použito pro datetimeV2 entity.|
|`datetimeReference`|-|řetězec|Technologie|-|[Časové pásmo](../luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity) bylo použito pro datetimeV2 entity. Nahrazuje `timezoneOffset` z v2.|