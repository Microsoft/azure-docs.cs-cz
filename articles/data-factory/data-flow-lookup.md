---
title: Azure Data Factory mapování transformace vyhledávání toku dat
description: Azure Data Factory mapování transformace vyhledávání toku dat
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: 197f5ba9d6921f4a9921b7074b9e05162d3e37b8
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2019
ms.locfileid: "64868124"
---
# <a name="azure-data-factory-mapping-data-flow-lookup-transformation"></a>Azure Data Factory mapování transformace vyhledávání toku dat

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Přidat odkaz na data z jiného zdroje do toku dat pomocí vyhledávání. Transformace vyhledávání vyžaduje definovaný zdroj, který odkazuje na referenční tabulku a odpovídá na klíčové pole.

![Transformace vyhledávání](media/data-flow/lookup1.png "vyhledávání")

Vyberte klíče, které chcete porovnává příchozí datový proud pole a pole ze zdroje odkazu na pole. Musíte nejprve vytvoříte nový zdroj na plátně návrhu toku dat použít jako pravá strana pro vyhledávání.

Když shoda nenajde, výsledné řádky a sloupce ze zdrojového odkazu se přidají do vašeho datového toku. Můžete zvolit pole, která zájmu, které chcete zahrnout do na konci toku dat pro jímku.

## <a name="match--no-match"></a>Odpovídá / žádná shoda

Po transformaci vaše vyhledávání vám pomůže následující transformace kontrolu výsledků jednotlivých řádků shoda s použitím výrazu funkce `isMatch()` provádět další možnosti v logice podle Určuje, jestli vyhledávání výsledkem odpovídající řádek nebo ne.

## <a name="optimizations"></a>Optimalizace

Ve službě Data Factory Data proudí provést v prostředí Spark horizontálním navýšením kapacity. Pokud vaše datová sada se vejde do pracovního uzlu paměťový prostor, doporučujeme optimalizovat výkon vyhledávání.

![Vysílání spojení](media/data-flow/broadcast.png "vysílání spojení")

### <a name="broadcast-join"></a>Vysílání spojení

Vyberte vlevo nebo vpravo vysílání spojení požádat o ADF můžete nabízet celou datovou sadu z obou stranách relaci vyhledávání do paměti.

### <a name="data-partitioning"></a>Dělení dat

Můžete také určit, dělení dat tak, že vyberete "Nastavit dělení" na kartě Optimalizace transformace vyhledávání k vytvoření sad dat, která lépe vejde do paměti na jeden pracovního procesu.

## <a name="next-steps"></a>Další postup

[Připojte se k](data-flow-join.md) a [Exists](data-flow-exists.md) transformace provádění podobných úloh v mapování Data proudí ADF. Podívejte se na tyto transformace Další.
