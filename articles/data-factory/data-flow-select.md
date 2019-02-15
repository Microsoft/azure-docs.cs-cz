---
title: Azure Data Factory mapování vyberte transformace toku dat
description: Azure Data Factory mapování vyberte transformace toku dat
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: 6b33568354653e2b222dd99d7a933de252646f9e
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2019
ms.locfileid: "56271657"
---
# <a name="azure-data-factory-mapping-data-flow-select-transformation"></a>Azure Data Factory mapování vyberte transformace toku dat

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Pomocí této transformace pro sloupec selektivitu (snížení počtu sloupců) nebo alias sloupce a názvy datového proudu.

Vyberte transformací, která vám umožní na alias celého datového proudu, nebo sloupce v tomto datovém proudu přiřadit různé názvy (aliasy) a pak odkazovat na tyto nové názvy později ve svém toku dat. Tato transformace je užitečné pro scénáře spojení sama na sebe. Způsob, jak implementovat vlastní spojení v toku dat ADF je provést "Vyberte" transformace datového proudu, větev "Novou větev" a pak ihned poté přidá. Tento datový proud teď bude mít nový název, který můžete použít pro připojení zpět do původního datového proudu, vytváření spojení sama:

![Spojení sama na sebe](media/data-flow/selfjoin.png "spojení sama na sebe")

V diagramu vyberte transformací, která je v horní části. Toto je aliasing původního datového proudu na "OrigSourceBatting". V transformačním spojení higlighted pod ní uvidíte, že používáme tento datový proud vyberte alias jako pravé spojení, abychom mohli odkazovat na stejný klíč v levé a pravé straně vnitřní spojení.

Vyberte lze také jako způsob, jak zrušit výběr sloupce ze svého datového toku. Například pokud máte 6 sloupce, které jsou definované v jímku, ale chcete vybrat konkrétní 3 pro transformaci a pak směrovat do jímky, můžete vybrat pouze tyto 3 s použitím vyberte transformace.

> [!NOTE]
> Je nutné vypnout "Vybrat vše" a vyberte jenom určité sloupce

Možnosti

Výchozí nastavení pro "Vyberte" je zahrnout všechny příchozí sloupce a zachovat původní názvy. Můžete alias datový proud nastavením název vyberte transformace.

Alias jednotlivé sloupce zrušte zaškrtnutí možnosti "Vybrat vše" a použijte mapování sloupců v dolní části.

![Vyberte transformační](media/data-flow/select001.png "vybrat Alias")
