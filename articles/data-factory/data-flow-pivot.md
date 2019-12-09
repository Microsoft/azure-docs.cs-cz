---
title: Transformace kontingenčního toku mapování dat
description: Kontingenční data z řádků do sloupců pomocí Azure Data Factory mapování pivotu toku dat
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/30/2019
ms.openlocfilehash: 8f23b5e61e1aee83172a12466fac8d5b5003fea8
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2019
ms.locfileid: "74930292"
---
# <a name="azure-data-factory-pivot-transformation"></a>Transformace pivotu služby Azure Data Factory


Použijte Pivot v toku dat ADF jako agregaci, kde jeden nebo více sloupců seskupení má jedinečné hodnoty řádků transformované na jednotlivé sloupce. V podstatě můžete hodnoty v řádcích vyhodnotit do nových sloupců (data převeďte do metadat).

![Možnosti pivotu](media/data-flow/pivot1.png "kontingenční tabulka 1")

## <a name="group-by"></a>Seskupit podle

![Možnosti pivotu](media/data-flow/pivot2.png "kontingenční tabulka 2")

Nejprve nastavte sloupce, které chcete seskupit podle pro agregaci pivotu. Můžete nastavit více než 1 sloupec se symbolem + vedle seznamu sloupců.

## <a name="pivot-key"></a>Klávesa Pivot

![Možnosti pivotu](media/data-flow/pivot3.png "Pivot 3")

Kontingenční klíč je sloupec, který bude ADF od řádku po sloupec. Ve výchozím nastavení se všechny jedinečné hodnoty v datové sadě pro toto pole Překlopí do sloupce. Volitelně můžete zadat hodnoty z datové sady, které chcete překlopit do hodnot sloupců. Toto je sloupec, který určí nové sloupce, které budou vytvořeny.

## <a name="pivoted-columns"></a>Sloupce v kontingenčním sloupci

![Možnosti pivotu](media/data-flow/pivot4.png "kontingenční tabulka 4")

Nakonec zvolíte agregaci, kterou chcete použít pro hodnoty v kontingenčním okně, a způsob, jakým chcete sloupce zobrazit v nové výstupní projekci z transformace.

Volitelné Můžete nastavit vzor pojmenování s předponou, střední a příponou, která se má přidat do každého nového názvu sloupce z hodnot řádků.

Například při překlopení "prodej" podle "oblasti" bude výsledkem nové hodnoty sloupce z každé hodnoty prodeje, tj. "25", "50", "1000" atd. Pokud však nastavíte hodnotu předpony "Sales-", každá hodnota sloupce by do začátku hodnoty přidala "Sales –".

![Možnosti pivotu](media/data-flow/pivot5.png "Pivot 5")

Když nastavíte uspořádání sloupců na "normální", budou se seskupovat všechny kontingenční sloupce s agregovanými hodnotami. Nastavení uspořádání sloupců na stranu "bočního" se bude střídat mezi sloupcem a hodnotou.

### <a name="aggregation"></a>Agregace

Chcete-li nastavit agregaci, kterou chcete použít pro hodnoty pivotu, klikněte na pole v dolní části podokna Pivoted Columns. Do Tvůrce výrazů toku dat ADF zadáte, kde můžete sestavit agregační výraz a zadat popisný název aliasu pro nové agregované hodnoty.

Použijte jazyk výrazu toku dat ADF k popisu transformací pivoted Columns v Tvůrci výrazů: https://aka.ms/dataflowexpressions.

## <a name="pivot-metadata"></a>Metadata pivotu

Transformace Pivot vytvoří nové názvy sloupců, které jsou dynamické na základě vašich příchozích dat. Klávesa Pivot vytvoří hodnoty pro každý nový název sloupce. Pokud neurčíte jednotlivé hodnoty a chcete pro každou jedinečnou hodnotu v kontingenčním klíči vytvořit dynamické názvy sloupců, nezobrazí se v uživatelském rozhraní tato metadata při kontrole a nebude k transformaci jímky zacházet žádné šíření sloupců. Pokud nastavíte hodnoty pro Pivot Key, potom může ADF určit nové názvy sloupců a tyto názvy sloupců budou k dispozici v mapování kontroly a jímky.

### <a name="generate-a-new-model-from-dynamic-columns"></a>Generování nového modelu z dynamických sloupců

Kontingenční tabulka dynamicky generuje nové názvy sloupců založené na hodnotách řádků. Tyto nové sloupce můžete převést na metadata, která lze později odkazovat v toku dat. Uděláte to tak, že kliknete na kartu náhled dat. Všechny nové sloupce vygenerované transformací pivotu se zobrazí v záhlaví tabulky s ikonou "se stejnou". Kliknutím na tlačítko "mapa se posunem" zapnete tyto nové sloupce do metadat, čímž se jejich součástí modelu toku dat.

![Kontingenční sloupce](media/data-flow/newpivot1.png "Mapování odhozených kontingenčních sloupců")

### <a name="landing-new-columns-in-sink"></a>Vykládku nových sloupců v jímky

I když jsou názvy dynamických sloupců v kontingenčním diagramu, můžete stále ukládat nové názvy a hodnoty sloupců do cílového úložiště. Stačí nastavit možnost "zapnout posun schématu" na zapnuto v nastavení jímky. V metadatech sloupců se neobjeví nové dynamické názvy, ale možnost posunu schématu vám umožní tato data obložit.

### <a name="view-metadata-in-design-mode"></a>Zobrazit metadata v režimu návrhu

Pokud chcete zobrazit nové názvy sloupců jako metadata a chcete zobrazit sloupce, které se šíří explicitně do transformace jímky, pak nastavte explicitní hodnoty na kartě Pivot Key.

### <a name="how-to-rejoin-original-fields"></a>Postup opětovného připojení původních polí
Transformace pivotu bude promítnout pouze sloupce používané v akci agregace, seskupení a Pivot. Pokud chcete zahrnout další sloupce z předchozího kroku v toku, použijte novou větev z předchozího kroku a pomocí vzoru pro vlastní spojení připojte tok s původními metadaty.

## <a name="next-steps"></a>Další kroky

Zkuste [transformaci Unpivot](data-flow-unpivot.md) , aby se hodnoty sloupce přepnuly na hodnoty řádků. 
