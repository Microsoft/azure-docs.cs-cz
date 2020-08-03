---
author: baanders
description: zahrnutí souboru pro operace dotazů na digitální vlákna Azure
ms.service: digital-twins
ms.topic: include
ms.date: 7/28/2020
ms.author: baanders
ms.openlocfilehash: 70ff1847548c1328a709cf17c02bba3dd25ba213
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/31/2020
ms.locfileid: "87486645"
---
## <a name="query-language-features"></a>Funkce jazyka dotazů

Digitální vlákna Azure poskytuje rozsáhlé možnosti dotazování pro dvojitou graf. Dotazy jsou popsány pomocí syntaxe jako v jazyce SQL, podobně jako [dotazovací jazyk IoT Hub](../articles/iot-hub/iot-hub-devguide-query-language.md) s mnoha srovnatelnými funkcemi.

> [!NOTE]
> U všech operací dotazů na digitální vlákna Azure se rozlišují velká a malá písmena.

Tady jsou dostupné operace v jazyce Azure pro úložiště dotazů v digitálních dotazech.

Získejte digitální vlákna podle jejich...
* Model ( `IS_OF_MODEL` operátor using)
* vlastnosti (včetně [vlastností značek](../articles/digital-twins/how-to-use-tags.md))
* rozhraní
* relationships
  - vlastnosti relací

Dotazy můžete dále vylepšit pomocí následujících operací:
* Získejte vlákna v několika typech vztahů ( `JOIN` dotazů). 
  - Existují omezení počtu `JOIN` povolených (jedna úroveň pro veřejnou verzi Preview).
* Výběr pouze nejlepších výsledků dotazu ( `Select TOP` operátor)
* Použijte skalární funkce: `IS_BOOL` , `IS_DEFINED` , `IS_NULL` , `IS_NUMBER` , `IS_OBJECT` , `IS_PRIMITIVE` , `IS_STRING` , `STARTSWITH` , `ENDSWITH` .
* Použití relačních operátorů dotazu: `IN` / `NIN` , `=` , `!=` , `<` , `>` , `<=` , `>=` .
* Použijte libovolnou kombinaci ( `AND` , `OR` , `NOT` operátor) `IS_OF_MODEL` , skalárních funkcí a relačních operátorů.
* Použít pokračování: objekt dotazu má vytvořenou velikost stránky (až 100). Pomocí tokenu pokračování v následných voláních rozhraní API můžete načíst digitální vlákna na jednu stránku po dobu.