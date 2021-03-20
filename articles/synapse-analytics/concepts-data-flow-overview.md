---
title: Toky dat
description: Přehled toků dat ve službě Azure synapse Analytics
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: synapse-analytics
ms.topic: conceptual
ms.custom: references_regions
ms.date: 12/16/2020
ms.openlocfilehash: 18322afbdca94b12ef142f6e37c4d2a22170436b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "97592627"
---
# <a name="data-flows-in-azure-synapse-analytics"></a>Toky dat ve službě Azure synapse Analytics

## <a name="what-are-data-flows"></a>Co jsou datové toky?

Datové toky jsou vizuálně navržené transformacemi dat ve službě Azure synapse Analytics. Toky dat umožňují inženýrům dat vyvíjet logiku transformace dat bez psaní kódu. Výsledné toky dat se spouštějí jako aktivity v kanálech Azure synapse Analytics, které využívají Apache Spark clusterů s horizontálním škálováním kapacity. Aktivity toku dat je možné provozovat pomocí stávajících funkcí plánování, řízení, flow a monitorování Azure synapse Analytics.

Datové toky poskytují zcela vizuální prostředí bez nutnosti kódování. Vaše toky dat běží na clusterech spuštění spravovaných synapse pro zpracování dat se škálováním na více systémů. Azure synapse Analytics zpracovává všechny překlady kódu, optimalizaci cest a provádění úloh toku dat.

## <a name="getting-started"></a>Začínáme

Toky dat se vytvářejí v podokně vývoj v synapse studiu. Tok dat vytvoříte tak, že vyberete znaménko plus vedle **vývoj** a pak vyberete **tok dat**. 

![Nový tok dat](media/data-flow/new-data-flow.png)

Tato akce přejde k plátnu toku dat, kde můžete vytvořit logiku transformace. Pokud chcete začít konfigurovat transformaci zdrojového kódu, vyberte **Přidat zdroj** . Další informace najdete v tématu [transformace zdroje](../data-factory/data-flow-source.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

## <a name="authoring-data-flows"></a>Vytváření toků dat

Tok dat má jedinečné plátno pro vytváření, které usnadňuje vytváření logiky transformace. Plátno toku dat je rozdělené na tři části: horní pruh, graf a panel konfigurace. 

![Snímek obrazovky znázorňující plátno toku dat s popisem horního panelu, grafu a panelu konfigurace.](media/data-flow/canvas-1.png)

### <a name="graph"></a>Graph

Graf zobrazí datový proud transformace. Ukazuje, že se při toku dat do jedné nebo více umyvadel zobrazuje čára. Chcete-li přidat nový zdroj, vyberte možnost **Přidat zdroj**. Chcete-li přidat novou transformaci, vyberte znaménko plus na pravé straně existující transformace. Přečtěte si další informace o tom, jak [Spravovat Graf toku dat](../data-factory/concepts-data-flow-manage-graph.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

![Snímek obrazovky znázorňuje část grafu na plátně s textovým polem hledání.](media/data-flow/canvas-2.png)

### <a name="configuration-panel"></a>Panel konfigurace

Panel konfigurace zobrazuje nastavení specifická pro aktuálně vybranou transformaci. Pokud není vybraná žádná transformace, zobrazuje tok dat. V celkové konfiguraci toku dat můžete přidat parametry přes kartu **parametry** . Další informace najdete v tématu [parametry toku dat](../data-factory/parameters-data-flow.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

Každá transformace obsahuje alespoň čtyři karty konfigurace.

#### <a name="transformation-settings"></a>Nastavení transformace

První karta v podokně Konfigurace každé transformace obsahuje nastavení specifická pro tuto transformaci. Další informace najdete na stránce s dokumentací k této transformaci.

![Karta nastavení zdroje](media/data-flow/source-1.png)

#### <a name="optimize"></a>Optimalizace

Karta **optimalizace** obsahuje nastavení pro konfiguraci schémat dělení. Další informace o tom, jak optimalizovat toky dat, najdete v [Průvodci výkonem mapování výkonu toku dat](../data-factory/concepts-data-flow-performance.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

![Snímek obrazovky znázorňující kartu optimalizace](media/data-flow/optimize.png)

#### <a name="inspect"></a>Prohlížen

Karta **Kontrola** poskytuje zobrazení metadat datového proudu, který transformuje. Můžete zobrazit počty sloupců, sloupce se změnily, přidané sloupce, datové typy, pořadí sloupců a odkazy na sloupce. **Kontrola** je zobrazení vašich metadat jen pro čtení. Není nutné mít povolen režim ladění, aby bylo možné zobrazit metadata v podokně **Kontrola** .

![Zkontrolovat kartu](media/data-flow/inspect.png)

Když změníte tvar dat prostřednictvím transformací, v podokně **Kontrola** se zobrazí tok změn metadat. Pokud ve zdrojové transformaci není definované schéma, metadata se v podokně **Kontrola** nezobrazí. Nedostatek metadat je běžné ve scénářích pro posun schématu.

#### <a name="data-preview"></a>Náhled dat

Pokud je režim ladění zapnutý, karta **Náhled dat** vám poskytne interaktivní snímek dat při každé transformaci. Další informace naleznete v části [data Preview v režimu ladění](../data-factory/concepts-data-flow-debug-mode.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#data-preview).

### <a name="top-bar"></a>Horní panel

Horní panel obsahuje akce, které ovlivňují celý tok dat, jako je nastavení ověřování a ladění. Můžete také zobrazit základní kód JSON a skript toku dat logiky transformace.

## <a name="available-transformations"></a>Dostupné transformace

Seznam dostupných transformací najdete v [přehledu transformace toku dat mapování](../data-factory/data-flow-transformation-overview.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) .

## <a name="data-flow-activity"></a>Aktivita toku dat

Toky dat se provozují v kanálech Azure synapse Analytics pomocí [aktivity toku dat](../data-factory/control-flow-execute-data-flow-activity.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json). Všichni uživatelé musí zadat, který Integration runtime má použít a předávat hodnoty parametrů. Další informace najdete v části [Azure Integration runtime](../data-factory/concepts-integration-runtime.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#azure-integration-runtime).

## <a name="debug-mode"></a>Režim ladění

Režim ladění umožňuje interaktivně zobrazit výsledky jednotlivých kroků transformace při sestavování a ladění toků dat. Ladicí relaci lze použít v v nástroji při sestavování logiky toku dat a spuštění ladění kanálu s aktivitami toku dat. Další informace najdete v dokumentaci v [režimu ladění](../data-factory/concepts-data-flow-debug-mode.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

## <a name="monitoring-data-flows"></a>Monitorování toků dat

Tok dat se integruje se stávajícími možnostmi monitorování Azure synapse Analytics. Informace o tom, jak pochopit výstup monitorování toku dat, najdete v tématu [monitorování toků dat mapování](../data-factory/concepts-data-flow-monitoring.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

Tým Azure synapse Analytics vytvořil [Průvodce optimalizací výkonu](../data-factory/concepts-data-flow-performance.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) , který vám pomůže optimalizovat dobu provádění toků dat po sestavení obchodní logiky.

## <a name="next-steps"></a>Další kroky

* Naučte se vytvořit [zdrojovou transformaci](../data-factory/data-flow-source.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).
* Naučte se vytvářet toky dat v [režimu ladění](../data-factory/concepts-data-flow-debug-mode.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).
