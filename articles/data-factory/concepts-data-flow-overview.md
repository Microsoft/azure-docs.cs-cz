---
title: Toky dat mapování
description: Přehled mapování datových toků v Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 12/19/2019
ms.openlocfilehash: 210c1814325e689dd70af9caa7fad08deed933e1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79243793"
---
# <a name="what-are-mapping-data-flows"></a>Co jsou toky dat mapování?

Mapování toků dat jsou vizuálně navržené transformace dat v Azure Data Factory. Toky dat umožňují technikům dat vyvíjet logiku grafické transformace dat bez psaní kódu. Výsledné toky dat se spouštějí jako aktivity v rámci kanálů Azure Data Factory, které používají clustery Spark s horizontálním navýšením kapacity. Aktivity toku dat lze zprovoznit prostřednictvím stávajících možností plánování, řízení, toku a monitorování v datové toku.

Mapování datových toků poskytuje plně vizuální prostředí bez nutnosti kódování. Vaše toky dat budou spuštěny ve vlastním clusteru spuštění pro zpracování dat s horizontálním navýšením kapacity. Azure Data Factory zpracovává všechny překlady kódu, optimalizace cest a provádění úloh toku dat.

## <a name="getting-started"></a>Začínáme

Chcete-li vytvořit tok dat, vyberte znaménko plus v části **Výrobní zdroje**a pak vyberte **Tok dat**. 

![Nový tok dat](media/data-flow/newdataflow2.png "nový tok dat")

Tím přejdete na plátno toku dat, kde můžete vytvořit logiku transformace. Chcete-li začít konfigurovat transformaci zdroje, vyberte **Přidat zdroj.** Další informace naleznete v [tématu Transformace zdroje](data-flow-source.md).

## <a name="data-flow-canvas"></a>Plátno toku dat

Plátno toku dat je rozděleno do tří částí: horní lišty, grafu a konfiguračního panelu. 

![Plátno](media/data-flow/canvas1.png "Plátno")

### <a name="graph"></a>Graph

Graf zobrazuje transformační proud. Zobrazuje linii zdrojových dat, jak toky do jednoho nebo více propadů. Chcete-li přidat nový zdroj, vyberte **Přidat zdroj**. Chcete-li přidat novou transformaci, vyberte znaménko plus v pravém dolním části existující transformace.

![Plátno](media/data-flow/canvas2.png "Plátno")

### <a name="azure-integration-runtime-data-flow-properties"></a>Vlastnosti toku dat integrace Azure

![Tlačítko Ladění](media/data-flow/debugbutton.png "Tlačítko Ladění")

Když začnete pracovat s datovými toky v adf, budete chtít zapnout přepínač "Ladění" pro toky dat v horní části uznaného prohlížeče. To bude spin-up clusteru Azure Databricks použít pro interaktivní ladění, náhledy dat a spuštění ladění kanálu. Velikost clusteru, který se využívá, můžete nastavit výběrem vlastního [prostředí Azure Integration Runtime](concepts-integration-runtime.md). Relace ladění zůstane naživu až 60 minut po posledním náhledu dat nebo spuštění posledního kanálu ladění.

Při zprovoznění vašich kanálů s aktivitami toku dat, ADF bude používat Azure Integration Runtime spojené s [aktivitou](control-flow-execute-data-flow-activity.md) ve vlastnosti "Spustit na".

Výchozí prostředí Azure Integration Runtime je malý cluster jednopracovních uzlů se 4 jádry, který vám umožní zobrazit náhled dat a rychle spustit kanály ladění s minimálními náklady. Pokud provádíte operace s velkými datovými sadami, nastavte větší konfiguraci Azure IR.

ADF můžete dát pokyn k udržování fondu prostředků clusteru (VM) nastavením TTL ve vlastnostech toku dat Azure IR. To bude mít za následek rychlejší provádění úloh y na následné aktivity.

#### <a name="azure-integration-runtime-and-data-flow-strategies"></a>Strategie runtime integrace Azure a toku dat

##### <a name="execute-data-flows-in-parallel"></a>Paralelní provádění toků dat

Pokud souběžně spustíte toky dat v kanálu, ADF se bude pro každé spuštění aktivity spouštět samostatné clustery Azure Databricks na základě nastavení v prostředí Runtime integrace Azure připojeného ke každé aktivitě. Chcete-li navrhnout paralelní spuštění v kanálech ADF, přidejte aktivity toku dat bez omezení priority v unovém rozhraní.

Z těchto tří možností bude tato možnost pravděpodobně spuštěna v co nejkratším čase. Každý paralelní tok dat se však spustí současně v samostatných clusterech, takže řazení událostí je nedeterministické.

Pokud provádíte aktivity toku dat paralelně uvnitř vašich kanálů, doporučujeme nepoužívat TTL. Důvodem je, že paralelní spuštění toků dat současně pomocí stejného prostředí Azure Integration Runtime bude mít za následek více instance tepléfondu pro vaši datovou továrnu.

##### <a name="overload-single-data-flow"></a>Přetížení jednoho toku dat

Pokud vložíte veškerou logiku do jednoho toku dat, adf se všechny spustí ve stejném kontextu spuštění úlohy na jedné instanci clusteru Spark.

Tuto možnost může být obtížné sledovat a řešit problémy, protože vaše obchodní pravidla a obchodní logika budou změť dohromady. Tato možnost také neposkytuje mnoho opětovné použitelnosti.

##### <a name="execute-data-flows-serially"></a>Sériové spuštění toků dat

Pokud spustíte aktivity toku dat v sériovém v kanálu a nastavili jste TTL v konfiguraci Azure IR, pak ADF znovu použije výpočetní prostředky (VM), což má za následek rychlejší následné spuštění. Stále obdržíte nový kontext Spark pro každé spuštění.

Z těchto tří možností to bude pravděpodobně trvat nejdelší dobu, než se provede end-to-end. Ale poskytuje čisté oddělení logických operací v každém kroku toku dat.

### <a name="configuration-panel"></a>Konfigurační panel

Konfigurační panel zobrazuje nastavení specifická pro aktuálně vybranou transformaci. Pokud není vybrána žádná transformace, zobrazí tok dat. V celkové konfiguraci toku dat můžete upravit název a popis na kartě **Obecné** nebo přidat parametry na kartě **Parametry.** Další informace naleznete v [tématu Mapování parametrů toku dat](parameters-data-flow.md).

Každá transformace má alespoň čtyři karty konfigurace.

#### <a name="transformation-settings"></a>Nastavení transformace

První karta v konfiguračním podokně každé transformace obsahuje nastavení specifická pro tuto transformaci. Další informace naleznete na stránce dokumentace transformace.

![Karta Nastavení zdroje](media/data-flow/source1.png "Karta Nastavení zdroje")

#### <a name="optimize"></a>Optimalizace

Karta **Optimalizace** obsahuje nastavení konfigurace schémat dělení.

![Optimalizovat](media/data-flow/optimize1.png "Optimalizace")

Výchozí nastavení je **Použít aktuální dělení**, které instruuje Azure Data Factory k použití schématu dělení nativní pro toky dat spuštěné na Sparku. Ve většině případů doporučujeme toto nastavení.

Existují případy, kde můžete chtít upravit dělení. Například pokud chcete výstup transformace do jednoho souboru v jezeře, vyberte **jeden oddíl** v transformaci jímky.

Dalším případem, kdy můžete chtít řídit schémata dělení je optimalizace výkonu. Úprava dělení poskytuje kontrolu nad distribucí dat mezi výpočetní uzly a optimalizace lokality dat, které mohou mít pozitivní i negativní vliv na celkový výkon toku dat. Další informace naleznete v [průvodci výkonem toku dat](concepts-data-flow-performance.md).

Chcete-li změnit rozdělení na libovolnou transformaci, vyberte kartu **Optimalizace** a vyberte přepínač **Nastavit dělení.** Poté se zobrazí řada možností pro dělení. Nejlepší způsob dělení se bude lišit v závislosti na svazcích dat, kandidátských klíčích, hodnotách null a mohutnosti. 

Osvědčeným postupem je začít s výchozím dělením a potom vyzkoušet různé možnosti dělení. Můžete testovat pomocí spuštění ladění kanálu a zobrazit čas spuštění a využití oddílu v každé transformace seskupení ze zobrazení monitorování. Další informace naleznete v [tématu Monitorování toků dat](concepts-data-flow-monitoring.md).

K dispozici jsou následující možnosti dělení.

##### <a name="round-robin"></a>Kruhové dotazování 

Kruhové dotazování je jednoduchý oddíl, který automaticky distribuuje data rovnoměrně mezi oddíly. Pokud nemáte dobré klíčové kandidáty k implementaci solidní, inteligentní strategie dělení, použijte kruhové dotazování. Můžete nastavit počet fyzických oddílů.

##### <a name="hash"></a>Hodnota hash

Azure Data Factory vytvoří hash sloupců k výrobě jednotné oddíly tak, aby řádky s podobnými hodnotami budou spadat do stejného oddílu. Při použití možnosti Hash otestujte možné zkosení oddílu. Můžete nastavit počet fyzických oddílů.

##### <a name="dynamic-range"></a>Dynamický rozsah

Dynamický rozsah bude používat dynamické rozsahy Spark založené na zadaných sloupech nebo výrazech. Můžete nastavit počet fyzických oddílů. 

##### <a name="fixed-range"></a>Pevný rozsah

Vytvořte výraz, který poskytuje pevný rozsah pro hodnoty v rámci sloupců dělených dat. Chcete-li se vyhnout zkosení oddílu, měli byste mít dobré znalosti o datech před použitím této možnosti. Hodnoty, které zadáte pro výraz, budou použity jako součást funkce oddílu. Můžete nastavit počet fyzických oddílů.

##### <a name="key"></a>Klíč

Pokud máte dobré znalosti o mohutnost dat, dělení klíčů může být dobrou strategií. Dělení klíčů vytvoří oddíly pro každou jedinečnou hodnotu ve sloupci. Počet oddílů nelze nastavit, protože číslo bude založeno na jedinečných hodnotách v datech.

#### <a name="inspect"></a>Zkontrolovat

Karta **Zkontrolovat** poskytuje zobrazení metadat datového proudu, který transformujete. Můžete zobrazit počty sloupců, změny sloupců, přidané sloupce, datové typy, řazení sloupců a odkazy na sloupce. **Kontrola** je zobrazení metadat jen pro čtení. K zobrazení metadat v podokně **Zkontrolovat** nemusíte mít povolený režim ladění.

![Zkontrolovat](media/data-flow/inspect1.png "Zkontrolovat")

Při změně tvaru dat prostřednictvím transformací se v podokně **Kontrola** zobrazí tok změn metadat. Pokud ve zdrojové transformaci není definované schéma, metadata se v podokně **Zkontrolovat** nezobrazí. Nedostatek metadat je běžné ve scénářích posunu schématu.

#### <a name="data-preview"></a>Náhled dat

Pokud je režim ladění zapnutý, karta **Náhled dat** poskytuje interaktivní snímek dat při každé transformaci. Další informace naleznete [v tématu Náhled dat v režimu ladění](concepts-data-flow-debug-mode.md#data-preview).

### <a name="top-bar"></a>Horní panel

Horní pruh obsahuje akce, které ovlivňují celý tok dat, jako je ukládání a ověřování. Můžete také přepínat mezi režimy grafu a konfigurace pomocí tlačítek **Zobrazit graf** a **Skrýt graf.**

![Skrýt graf](media/data-flow/hideg.png "Skrýt graf")

Pokud graf skryjete, můžete procházet transformační uzly bočně pomocí tlačítek **Předchozí** a **Další.**

![Předchozí a další tlačítka](media/data-flow/showhide.png "předchozí a další tlačítka")

## <a name="next-steps"></a>Další kroky

* Naučte se, jak vytvořit [zdrojovou transformaci](data-flow-source.md).
* Přečtěte si, jak vytvářet toky dat v [režimu ladění](concepts-data-flow-debug-mode.md).
