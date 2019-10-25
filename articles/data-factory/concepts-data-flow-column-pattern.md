---
title: Vzory sloupců v Azure Data Factory tok dat mapování
description: Vytváření zobecněných vzorů pro transformaci dat pomocí vzorů sloupců v Azure Data Factory mapování toků dat
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/21/2019
ms.openlocfilehash: 0c9a3c2ef05f4a11933ca7fc81c7c0f87a612293
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2019
ms.locfileid: "72789931"
---
# <a name="using-column-patterns-in-mapping-data-flow"></a>Použití vzorů sloupců v mapování toku dat

Několik transformací toku dat mapování vám umožní odkazovat na sloupce šablon na základě vzorů namísto pevně kódovaných názvů sloupců. Tato shoda se označuje jako *vzory sloupců*. Můžete definovat vzory, které odpovídají sloupcům na základě názvu, datového typu, datového proudu nebo pozice místo vyžadování přesných názvů polí. Existují dva scénáře, kdy jsou vhodné vzory sloupců:

* Pokud se pole příchozího zdroje často mění, například případ změny sloupců v textových souborech nebo NoSQL databázích. Tento scénář je známý jako [posun schématu](concepts-data-flow-schema-drift.md).
* Pokud chcete provést běžnou operaci pro velkou skupinu sloupců. Například, chcete-li přetypování každého sloupce, který má "Total" v názvu sloupce na hodnotu Double.

Vzory sloupců jsou aktuálně k dispozici v odvozeném sloupci, agregace, výběru a transformaci jímky.

## <a name="column-patterns-in-derived-column-and-aggregate"></a>Vzory sloupců v odvozeném sloupci a agregace

Chcete-li přidat vzor sloupce do odvozeného sloupce nebo na kartě agregace agregované transformace, klikněte na ikonu se symbolem plus napravo od existujícího sloupce. Vyberte možnost **přidat model sloupce**. 

![vzory sloupců](media/data-flow/columnpattern.png "Vzory sloupců")

K zadání podmínky shody použijte [Tvůrce výrazů](concepts-data-flow-expression-builder.md) . Vytvořte logický výraz, který odpovídá sloupcům na základě `name`, `type`, `stream`a `position` sloupce. Vzor bude mít vliv na libovolný sloupec, posunovaná nebo definovaná, kde podmínka vrátí hodnotu true.

Dvě pole výrazu pod podmínkou shody určují nové názvy a hodnoty ovlivněných sloupců. Použijte `$$` k odkazování na existující hodnotu odpovídajícího pole. Levý rámeček výrazu definuje název a pravé pole výrazu definuje hodnotu.

![vzory sloupců](media/data-flow/columnpattern2.png "Vzory sloupců")

Výše uvedený vzor sloupce odpovídá každému sloupci typu Double a vytvoří jeden agregovaný sloupec na základě shody. Název nového sloupce se shoduje s názvem sloupce, který je zřetězený s příponou _Total. Hodnota nového sloupce je zaokrouhlená, agregovaná suma existující hodnoty typu Double.

Pokud chcete ověřit, jestli je vaše shoda podmínky správná, můžete ověřit výstupní schéma definovaných sloupců na kartě **zkontrolovat** nebo získat snímek dat na kartě **Náhled dat** . 

![vzory sloupců](media/data-flow/columnpattern3.png "Vzory sloupců")

## <a name="rule-based-mapping-in-select-and-sink"></a>Mapování na základě pravidel pro výběr a jímku

Při mapování sloupců ve zdroji a výběru transformací můžete přidat buď pevné mapování, nebo mapování na základě pravidel. Pokud znáte schéma vašich dat a očekáváte, že konkrétní sloupce ze zdrojové datové sady budou vždycky odpovídat specifickým statickým názvům, použijte pevné mapování. Pokud pracujete s flexibilními schématy, použijte mapování na základě pravidel k sestavení porovnávání vzorů na základě `name`, `type`, `stream`a `position` sloupců. Můžete mít libovolnou kombinaci s pevným mapováním a mapování na základě pravidel. 

Chcete-li přidat mapování na základě pravidla, klikněte na tlačítko **Přidat mapování** a vyberte **mapování na základě pravidel**.

![mapování na základě pravidel](media/data-flow/rule2.png "Mapování na základě pravidel")

Do pole levý výraz zadejte podmínku vaší logické shody. V poli pravého výrazu určete, k čemu se odpovídající sloupec namapuje. Použijte `$$` k odkazování na existující název odpovídajícího pole.

Pokud kliknete na ikonu Dvojitá šipka dolů, můžete zadat podmínku mapování regulárního výrazu.

Kliknutím na ikonu brýlí vedle mapování založeného na pravidlech zobrazíte, které definované sloupce se shodují a k čemu se namapují.

![mapování na základě pravidel](media/data-flow/rule1.png "Mapování na základě pravidel")

Ve výše uvedeném příkladu se vytvoří dvě mapování založená na pravidlech. První z nich přebírá všechny sloupce, které nemají název ' video ' a mapuje je na jejich existující hodnoty. Druhé pravidlo používá regulární výraz pro shodu se všemi sloupci, které začínají na ' video ' a mapuje je na sloupec ' movieId '.

Pokud vaše pravidlo vede k více identickým mapováním, povolte **Přeskočit duplicitní vstupy** nebo **Přeskočit duplicitní výstupy** , aby nedocházelo k duplicitám.

## <a name="pattern-matching-expression-values"></a>Hodnoty výrazů pro porovnávání vzorů.

* `$$` překládá k názvu nebo hodnotě každé shody v době běhu.
* `name` představuje název každého příchozího sloupce.
* `type` představuje datový typ každého příchozího sloupce.
* `stream` představuje název spojený s každým datovým proudem nebo transformaci v toku.
* `position` je pořadové místo sloupců v toku dat

## <a name="next-steps"></a>Další kroky
* Další informace o [jazyku výrazu](data-flow-expression-functions.md) mapování dat pro transformaci dat
* Použijte vzory sloupců v [transformaci jímky](data-flow-sink.md) a [Vyberte transformaci](data-flow-select.md) pomocí mapování založeného na pravidlech.
