---
title: Azure Data Factory mapování klíčů transformace náhradní toku dat
description: Jak vygenerovat po sobě jdoucími hodnotami klíče pomocí služby Azure Data Factory mapování toku náhradní klíč transformace dat
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: eaa1c577f7e208400d3430222b006e0dbbd7956a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "61350432"
---
# <a name="mapping-data-flow-surrogate-key-transformation"></a>Mapování klíčových transformace náhradní toku dat

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Přidání narůstající libovolné klíčové hodnoty neobchodní vaší sady řádků toku dat pomocí transformace náhradní klíč. To je užitečné při návrhu tabulky dimenzí v analytické datový model hvězdicového schématu kde každý člen v tabulkách dimenze musí mít jedinečný klíč, který je součástí klíče, neobchodní metodologie Kimball datového skladu.

![Náhradní klíče transformace](media/data-flow/surrogate.png "náhradní klíče transformace")

"Klíčový sloupec" je název, který vám poskytne do nového sloupce klíče náhrady.

"Počáteční hodnota" se počáteční bod inkrementální hodnotu.

## <a name="increment-keys-from-existing-sources"></a>Přírůstek klíče ze stávajících zdrojů

Pokud chcete spustit vaše pořadí z hodnoty, který existuje ve zdroji, můžete použít sloupec odvozené transformace hned za svoji náhradní klíč transformaci a sečtení dvou hodnot:

![SK přidat maximální](media/data-flow/sk006.png "náhradní klíč transformace přidat Max")

Naplnit s předchozí maximální hodnota klíče, jsou dvě techniky, které můžete použít:

### <a name="database-sources"></a>Databázovým zdrojům

Pomocí možnosti "Dotaz" Vyberte MAX() ze zdroje pomocí transformace zdroje:

![Náhradní klíče dotazu](media/data-flow/sk002.png "náhradní klíče transformačního dotazu")

### <a name="file-sources"></a>Souborové zdroje

Pokud vaše předchozí maximální hodnota je v souboru, můžete použít svoji transformaci zdroj spolu s agregační transformaci a pomocí funkce MAX() výrazu k získání předchozí maximální hodnoty:

![Náhradní soubor klíče](media/data-flow/sk008.png "náhradní soubor klíče")

V obou případech musíte připojit příchozí nových dat spolu s zdroje, který obsahuje předchozí maximální hodnota:

![Náhradní klíče spojení](media/data-flow/sk004.png "náhradní klíče spojení")

## <a name="next-steps"></a>Další postup

Tyto příklady používají [připojení](data-flow-join.md) a [odvozené sloupce](data-flow-derived-column.md) transformace.
