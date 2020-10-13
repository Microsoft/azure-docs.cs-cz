---
author: baanders
description: zahrnutí souboru pro operace dotazů na digitální vlákna Azure
ms.service: digital-twins
ms.topic: include
ms.date: 7/28/2020
ms.author: baanders
ms.openlocfilehash: 450ea0b8024192f6f351b4893fe6e8e07db236f7
ms.sourcegitcommit: 50802bffd56155f3b01bfb4ed009b70045131750
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91931970"
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
* Použijte skalární funkce: `IS_BOOL` , `IS_DEFINED` , `IS_NULL` , `IS_NUMBER` , `IS_OBJECT` , `IS_PRIMITIVE` , `IS_STRING` , `STARTSWITH` , `ENDSWITH` .
* Použití relačních operátorů dotazu: `IN` / `NIN` , `=` , `!=` , `<` , `>` , `<=` , `>=` .
* Použijte libovolnou kombinaci ( `AND` , `OR` , `NOT` operátor) `IS_OF_MODEL` , skalárních funkcí a relačních operátorů.
* Použít pokračování: objekt dotazu má vytvořenou velikost stránky (až 100). Pomocí tokenu pokračování v následných voláních rozhraní API můžete načíst digitální vlákna na jednu stránku po dobu.