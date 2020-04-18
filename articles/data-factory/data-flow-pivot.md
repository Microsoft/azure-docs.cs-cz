---
title: Mapování transformace pivotu toku dat
description: Kontingenční data z řádků do sloupců pomocí transformace pivotu toku toku dat mapování datové toku Azure Data Factory
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/30/2019
ms.openlocfilehash: 980d7c3e1b1f69e76c091e2a4a74c8e5a4d0bb64
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/17/2020
ms.locfileid: "81606359"
---
# <a name="azure-data-factory-pivot-transformation"></a>Transformace pivotu Azure pro datové továrny

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Pivot v Toku dat adf použijte jako agregaci, kde jeden nebo více sloupců seskupení má své odlišné hodnoty řádků transformované do jednotlivých sloupců. V podstatě můžete převést hodnoty řádků do nových sloupců (převést data na metadata).

![Možnosti pivotu](media/data-flow/pivot1.png "otočný čep 1")

## <a name="group-by"></a>Seskupit podle

![Možnosti pivotu](media/data-flow/pivot2.png "otočná čep")

Nejprve nastavte sloupce, podle kterých chcete seskupit pro pivot agregaci. Zde můžete nastavit více než 1 sloupec se znaménkem + vedle seznamu sloupců.

## <a name="pivot-key"></a>Kontingenční klávesa

![Možnosti pivotu](media/data-flow/pivot3.png "pivot 3")

Kontingenční klávesa je sloupec, ve které se adf přenese z řádku na sloupec. Ve výchozím nastavení se každá jedinečná hodnota v datové sadě pro toto pole překloní ke sloupci. Volitelně však můžete zadat hodnoty z datové sady, kterou chcete přejít k hodnotám sloupců. Toto je sloupec, který určí nové sloupce, které budou vytvořeny.

## <a name="pivoted-columns"></a>Otočné sloupce

![Možnosti pivotu](media/data-flow/pivot4.png "otočná čep")

Nakonec zvolíte agregaci, kterou chcete použít pro otočné hodnoty, a způsob, jakým chcete, aby se sloupce zobrazily v nové výstupní projekci z transformace.

(Nepovinné) Vzor pojmenování můžete nastavit s předponou, prostřední a příponou, která má být přidána ke každému novému názvu sloupce z hodnot řádků.

Například otočení "Prodej" podle "Region" by mělo za následek nové hodnoty sloupců z každé hodnoty prodeje, tj. Pokud však nastavíte hodnotu předpony "Sales-", každá hodnota sloupce přidá "Prodej-" na začátek hodnoty.

![Možnosti pivotu](media/data-flow/pivot5.png "otočný čep 5")

Nastavení uspořádání sloupců na hodnotu Normální seskupí všechny otočné sloupce s jejich agregovanými hodnotami. Nastavení uspořádání sloupců na "Laterální" se bude střídat mezi sloupcem a hodnotou.

### <a name="aggregation"></a>Agregace

Chcete-li nastavit agregaci, kterou chcete použít pro hodnoty kontingenčního čepu, klikněte na pole v dolní části podokna Otočné sloupce. Vstoupíte do tvůrce výrazů toku dat a zadejte, kde můžete vytvořit výraz agregace a zadat popisný název aliasu pro nové agregované hodnoty.

Jazyk toku dat ADF slouží k popisu transformací otočných sloupců v Tvůrce výrazů: https://aka.ms/dataflowexpressions.

## <a name="pivot-metadata"></a>Kontingenční metadata

Transformace Pivot vytvoří nové názvy sloupců, které jsou dynamické na základě příchozích dat. Kontingenční klávesa vytváří hodnoty pro každý nový název sloupce. Pokud nezadáte jednotlivé hodnoty a chcete vytvořit názvy dynamických sloupců pro každou jedinečnou hodnotu v kontingenčním klíči, uživatelské tlačítko nezobrazí metadata v kontrole a nebude žádné šíření sloupců do transformace jímky. Pokud nastavíte hodnoty pro kontingenční klíč, pak ADF můžete určit nové názvy sloupců a tyto názvy sloupců budou k dispozici v kontrole a jímce mapování.

### <a name="generate-a-new-model-from-dynamic-columns"></a>Generovat nový model z dynamických sloupců

Pivot generuje nové názvy sloupců dynamicky na základě hodnot řádků. Tyto nové sloupce můžete změnit na metadata, na která lze později v toku dat odkazovat. Chcete-li to provést, klikněte na kartu Náhled dat. Všechny nové sloupce generované transformací pivotu se zobrazí s ikonou "driftované" v záhlaví tabulky. Kliknutím na tlačítko "Mapa unášená" proměníte tyto nové sloupce v metadata, což je učiní součástí modelu toku dat.

![Kontingenční sloupce](media/data-flow/newpivot1.png "Posunuté sloupce kontingenčního pole mapy")

### <a name="landing-new-columns-in-sink"></a>Přistání nových sloupců v umyvadle

I s dynamickými názvy sloupců v kontingenčním zařízení můžete nové názvy sloupců a hodnoty stále utajet do cílového úložiště. Stačí nastavit "Povolit posun schématu" v nastavení umyvadla. Nové dynamické názvy se v metadatech sloupce nezobrazí, ale možnost posunu schématu vám umožní data přistát.

### <a name="view-metadata-in-design-mode"></a>Zobrazení metadat v návrhovém režimu

Pokud chcete zobrazit nové názvy sloupců jako metadata v zkontrolovat a chcete zobrazit sloupce šířit explicitně transformace jímky, nastavte explicitní hodnoty na kartě kontingenční klíč.

### <a name="how-to-rejoin-original-fields"></a>Jak znovu připojit původní pole
Transformace pivotu promítne pouze sloupce použité v agregaci, seskupení a kontingenční akci. Pokud chcete zahrnout další sloupce z předchozího kroku v toku, použijte novou větev z předchozího kroku a použijte vzor vlastního spojení pro připojení toku s původními metadaty.

## <a name="next-steps"></a>Další kroky

Zkuste [přeměnu unpivot](data-flow-unpivot.md) uzasete hodnoty sloupců na hodnoty řádků. 
