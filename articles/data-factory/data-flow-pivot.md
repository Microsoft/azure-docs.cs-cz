---
title: Azure Data Factory mapování transformace Pivot toku dat
description: Kontingenční data z řádků do sloupců pomocí Azure Data Factory mapování toku Pivot transformace dat
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: e16cac281b77f3ca93d9ef358ae806203bc8b663
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "61348451"
---
# <a name="azure-data-factory-pivot-transformation"></a>Azure data factory pivot transformace
[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Použijte Pivotu v toku dat ADF jako agregace kde má jeden nebo více sloupců seskupení jeho hodnoty jedinečných řádků transformuje na jednotlivé sloupce. V podstatě můžete otáčení hodnoty řádků do nového sloupce (dat čerpají metadata).

![Otáčení možnosti](media/data-flow/pivot1.png "otáčení 1")

## <a name="group-by"></a>Seskupit podle

![Otáčení možnosti](media/data-flow/pivot2.png "otáčení 2")

Nejprve nastavte sloupce, které chcete seskupit podle vaší pivot agregace. Můžete nastavit více než 1 sloupec tady se + znaménko vedle seznamu sloupců.

## <a name="pivot-key"></a>Klíče kontingenční tabulky

![Otáčení možnosti](media/data-flow/pivot3.png "otáčení 3")

Klíče kontingenční tabulky je sloupec, který bude ADF otáčení z řádku na sloupec. Ve výchozím nastavení bude otáčení každou jedinečnou hodnotu v datové sadě pro toto pole na sloupec. Však můžete volitelně zadat hodnoty z datové sady, kterou chcete přenechte obranu hodnot sloupců. To je sloupec, který určí, které se vytvoří nové sloupce.

## <a name="pivoted-columns"></a>Převedený sloupce

![Otáčení možnosti](media/data-flow/pivot4.png "otáčení 4")

A konečně zvolíte agregaci, kterou chcete použít pro pivotovaná hodnoty a jak byste chtěli sloupce, který se má zobrazit v nové projekce výstup z transformace.

(Volitelné) Můžete nastavit vzoru pro pojmenovávání s předponou, střední a příponu je třeba přidat do každého nový název sloupce z hodnoty řádků.

Například "Oblasti" Přesun "Prodeje" výsledkem by byla hodnoty nového sloupce od každé hodnoty prodeje, tj "25", "50", "1000" atd. Ale pokud nastavíte hodnotu předpony "Sales-", každá hodnota sloupce by přidat "Sales-" začátek hodnoty.

![Otáčení možnosti](media/data-flow/pivot5.png "otáčení 5")

Nastavení uspořádání sloupců "Normální" seskupí dohromady pivotovaná sloupce s jejich agregované hodnoty. Mezi sloupci a hodnota se alternativní nastavení uspořádání sloupců na "Laterální".

### <a name="aggregation"></a>Agregace

Nastavit agregaci, kterou chcete použít pro pivot hodnoty, klikněte na pole v dolní části podokna neseskupené sloupce. Zadáte do Tvůrce toku dat ADF kde agregační výraz sestavit a zadejte alias popisný název vaší nové agregované hodnoty.

Jazyk výrazů tok ADF Data použít k podrobnému popisu transformace převedený sloupec v Tvůrce: https://aka.ms/dataflowexpressions.

## <a name="pivot-metadata"></a>Metadata pivotu

Transformace Pivot vytvoří nové názvy sloupců, které jsou dynamické vzorce na základě příchozích dat. Klíče kontingenční vytváří hodnoty pro každý nový název sloupce. Pokud nemáte zadejte jednotlivé hodnoty a chcete vytvořit názvy dynamické sloupců pro každou jedinečnou hodnotu v klíče kontingenční tabulky, uživatelské rozhraní, nebudou zobrazeny metadata zkontrolujte, jestli se a nedojde k žádné šíření sloupce k transformaci jímky. Pokud nastavíte hodnoty pro klíče kontingenční tabulky, ADF můžete určit nové názvy sloupců a tyto názvy sloupců v zkontrolujte, jestli k dispozici, který se jímky mapování.

### <a name="landing-new-columns-in-sink"></a>Úvodní nové sloupce do jímky

I s názvy sloupců dynamické v Pivotu můžete stále jímky nové názvy sloupců a hodnoty do cílového úložiště. Stačí nastavte "Povolit schématu odchylek" na na v nastavení jímky. Neuvidíte nové dynamické názvy ve sloupci metadata, ale možnost schématu odchylek vám umožní dostat data.

### <a name="view-metadata-in-design-mode"></a>Zobrazit metadata v režimu návrhu

Pokud chcete zobrazit jako metadata v zkontrolujte, jestli se nové názvy sloupců a chcete zobrazit sloupce, které rozšíří explicitně do jímky transformace, nastavte v kartě klíče kontingenční explicitní hodnoty.

### <a name="how-to-rejoin-original-fields"></a>Jak se znovu připojit původní pole
Transformace Pivot bude pouze projektu sloupci používanými v agregaci, seskupení a otáčení akce. Pokud budete chtít zahrnout ostatní sloupce z předchozího kroku do vašeho toku, použít novou větev z předchozího kroku a použití vzoru spojení sama na sebe na připojení toku s původní metadata.

## <a name="next-steps"></a>Další postup

Zkuste [převést řádky transformace](data-flow-unpivot.md) jak proměnit hodnoty řádků hodnot sloupců. 
