---
title: Parametry řetězce dotazu rozhraní V3 API
ms.topic: include
ms.date: 06/30/2020
ms.openlocfilehash: 47727f6df772669fa323a10cd099764a6d35cb6a
ms.sourcegitcommit: 32592ba24c93aa9249f9bd1193ff157235f66d7e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/01/2020
ms.locfileid: "85610777"
---
Parametry řetězce dotazu rozhraní V3 API zahrnují:

|Parametr dotazu|Název portálu LUIS|Typ|Verze|Výchozí|Účel|
|--|--|--|--|--|--|
|`log`|Uložit protokoly|Boolean|V2 & V3|false (nepravda)|Dotaz na uložení v souboru protokolu. Výchozí hodnota je false.|
|`query`|-|řetězec|Jenom V3|Žádná výchozí hodnota – v žádosti GET se vyžaduje.|**V v2**je utterance, který má být předpovězen, v `q` parametru. <br><br>**V v3**je funkce předána do `query` parametru.|
|`show-all-intents`|Zahrnout skóre pro všechny záměry|Boolean|Jenom V3|false (nepravda)|Vrátí všechny záměry s odpovídajícím skóre v objektu **předpověď. záměrs** . Záměry jsou vráceny jako objekty v nadřazeném `intents` objektu. To umožňuje programový přístup bez nutnosti najít záměr v poli: `prediction.intents.give` . V v2 byly vráceny v poli. |
|`verbose`|Zahrnout další podrobnosti o entitách|Boolean|V2 & V3|false (nepravda)|**V v2**při nastavení na hodnotu true byly vráceny všechny předpovězené záměry. Pokud potřebujete všechny předpovídané záměry, použijte parametr V3 `show-all-intents` .<br><br>**V v3**tento parametr poskytuje pouze podrobnosti o metadatech entit pro předpovědi entit.  |
|`timezoneOffset`|-|řetězec|V2|-|Časové pásmo bylo použito pro datetimeV2 entity.|
|`datetimeReference`|-|řetězec|Technologie|-|[Časové pásmo](../luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity) bylo použito pro datetimeV2 entity. Nahrazuje `timezoneOffset` z v2.|