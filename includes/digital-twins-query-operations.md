---
author: baanders
description: zahrnutí souboru pro operace dotazů na digitální vlákna Azure
ms.service: digital-twins
ms.topic: include
ms.date: 7/28/2020
ms.author: baanders
ms.openlocfilehash: 333a7ec4ae0e5c8cbc94a603e2ccf81ee92e7d48
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2020
ms.locfileid: "92078455"
---
## <a name="query-language-features"></a>Funkce jazyka dotazů

Digitální vlákna Azure poskytuje rozsáhlé možnosti dotazování pro dvojitou graf. Dotazy jsou popsány pomocí syntaxe jako v jazyce SQL, podobně jako [dotazovací jazyk IoT Hub](../articles/iot-hub/iot-hub-devguide-query-language.md) s mnoha srovnatelnými funkcemi.

> [!NOTE]
> U všech operací dotazů na digitální vlákna Azure se rozlišují velká a malá písmena.

Tady jsou dostupné operace v jazyce dotazů Azure Digital revlákens.

Získejte digitální vlákna podle jejich...
* Model ( `IS_OF_MODEL` operátor using)
* vlastnosti (včetně [vlastností značek](../articles/digital-twins/how-to-use-tags.md))
* rozhraní
* relationships
  - vlastnosti relací

Dotazy můžete dále vylepšit pomocí následujících operací:
* Získejte vlákna v několika typech vztahů ( `JOIN` dotazů). 
  - Během období Preview `JOIN` je povolená až pět úrovní.
* Výběr pouze nejlepších výsledků dotazu ( `Select TOP` operátor)
* Počet položek v sadě výsledků dotazu pomocí `Select COUNT`
* Použít projekce k výběru sloupců, které bude dotaz vracet
* Použijte skalární funkce: `IS_BOOL` , `IS_DEFINED` , `IS_NULL` , `IS_NUMBER` , `IS_OBJECT` , `IS_PRIMITIVE` , `IS_STRING` , `STARTSWITH` , `ENDSWITH` .
* Použití relačních operátorů dotazu: `IN` / `NIN` , `=` , `!=` , `<` , `>` , `<=` , `>=` .
* Použijte libovolnou kombinaci ( `AND` , `OR` , `NOT` operátor) `IS_OF_MODEL` , skalárních funkcí a relačních operátorů.
* Použít pokračování: objekt dotazu má vytvořenou velikost stránky (až 100). Pomocí tokenu pokračování v následných voláních rozhraní API můžete načíst digitální vlákna na jednu stránku po dobu.