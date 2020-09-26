---
title: Toky dat mapování
description: Přehled toků mapování dat v Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: references_regions
ms.date: 09/02/2020
ms.openlocfilehash: 7d73d832f96d087964c46c6c735c0385832c08db
ms.sourcegitcommit: 5dbea4631b46d9dde345f14a9b601d980df84897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91370908"
---
# <a name="mapping-data-flows-in-azure-data-factory"></a>Mapování toků dat v Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

## <a name="what-are-mapping-data-flows"></a>Co jsou toky dat mapování?

Mapování datových toků je vizuálně navržené transformace dat v Azure Data Factory. Toky dat umožňují inženýrům dat vyvíjet logiku transformace dat bez psaní kódu. Výsledné toky dat se spouštějí jako aktivity v rámci Azure Data Factory kanálů, které používají clustery Apache Spark s horizontálním navýšení kapacity. Aktivity toku dat je možné provozovat pomocí stávajících funkcí Azure Data Factory plánování, řízení, flow a monitorování.

Mapování toků dat poskytuje zcela vizuální prostředí bez nutnosti kódování. Vaše datové toky se spouštějí na clusterech spuštění spravovaných ADF pro zpracování dat se škálováním na více systémů. Azure Data Factory zpracovává všechny překlady kódu, optimalizaci cest a provádění úloh toku dat.

## <a name="getting-started"></a>Začínáme

Toky dat se vytvářejí v podokně prostředky továrny, jako jsou kanály a datové sady. Tok dat vytvoříte tak, že vyberete znaménko plus vedle **prostředků výrobce**a pak vyberete **tok dat**. 

![Nový tok dat](media/data-flow/new-data-flow.png "Nový tok dat")

Tato akce přejde k plátnu toku dat, kde můžete vytvořit logiku transformace. Pokud chcete začít konfigurovat transformaci zdrojového kódu, vyberte **Přidat zdroj** . Další informace najdete v tématu [transformace zdroje](data-flow-source.md).

## <a name="authoring-data-flows"></a>Vytváření toků dat

Mapování toku dat má jedinečné plátno pro vytváření, které usnadňuje sestavování logiky transformace. Plátno toku dat je rozdělené na tři části: horní pruh, graf a panel konfigurace. 

![Plátno](media/data-flow/canvas1.png "Plátno")

### <a name="graph"></a>Graph

Graf zobrazí datový proud transformace. Ukazuje, že se při toku dat do jedné nebo více umyvadel zobrazuje čára. Chcete-li přidat nový zdroj, vyberte možnost **Přidat zdroj**. Chcete-li přidat novou transformaci, vyberte znaménko plus na pravé straně existující transformace. Přečtěte si další informace o tom, jak [Spravovat Graf toku dat](concepts-data-flow-manage-graph.md).

![Plátno](media/data-flow/canvas2.png "Plátno")

### <a name="configuration-panel"></a>Panel konfigurace

Panel konfigurace zobrazuje nastavení specifická pro aktuálně vybranou transformaci. Pokud není vybraná žádná transformace, zobrazuje tok dat. V celkové konfiguraci toku dat můžete upravit název a popis na kartě **Obecné** nebo přidat parametry přes kartu **parametry** . Další informace najdete v tématu [mapování parametrů toku dat](parameters-data-flow.md).

Každá transformace obsahuje alespoň čtyři karty konfigurace.

#### <a name="transformation-settings"></a>Nastavení transformace

První karta v podokně Konfigurace každé transformace obsahuje nastavení specifická pro tuto transformaci. Další informace najdete na stránce s dokumentací k této transformaci.

![Karta nastavení zdroje](media/data-flow/source1.png "Karta nastavení zdroje")

#### <a name="optimize"></a>Optimalizace

Karta **optimalizace** obsahuje nastavení pro konfiguraci schémat dělení. Další informace o tom, jak optimalizovat toky dat, najdete v [Průvodci výkonem mapování výkonu toku dat](concepts-data-flow-performance.md).

![Snímek obrazovky zobrazuje kartu optimalizace, která zahrnuje možnost oddílu, typ oddílu a počet oddílů.](media/data-flow/optimize.png)

#### <a name="inspect"></a>Prohlížen

Karta **Kontrola** poskytuje zobrazení metadat datového proudu, který transformuje. Můžete zobrazit počty sloupců, sloupce se změnily, přidané sloupce, datové typy, pořadí sloupců a odkazy na sloupce. **Kontrola** je zobrazení vašich metadat jen pro čtení. Není nutné mít povolen režim ladění, aby bylo možné zobrazit metadata v podokně **Kontrola** .

![Prohlížen](media/data-flow/inspect1.png "Prohlížen")

Když změníte tvar dat prostřednictvím transformací, v podokně **Kontrola** se zobrazí tok změn metadat. Pokud ve zdrojové transformaci není definované schéma, metadata se v podokně **Kontrola** nezobrazí. Nedostatek metadat je běžné ve scénářích pro posun schématu.

#### <a name="data-preview"></a>Náhled dat

Pokud je režim ladění zapnutý, karta **Náhled dat** vám poskytne interaktivní snímek dat při každé transformaci. Další informace naleznete v části [data Preview v režimu ladění](concepts-data-flow-debug-mode.md#data-preview).

### <a name="top-bar"></a>Horní panel

Horní panel obsahuje akce, které ovlivňují celý tok dat, jako je ukládání a ověřování. Můžete také zobrazit základní kód JSON a skript toku dat logiky transformace. Další informace najdete v části [skript toku dat](data-flow-script.md).

## <a name="available-transformations"></a>Dostupné transformace

Seznam dostupných transformací najdete v [přehledu transformace toku dat mapování](data-flow-transformation-overview.md) .

## <a name="data-flow-activity"></a>Aktivita toku dat

Mapování toků dat je v rámci kanálů ADF provozováno pomocí [aktivity toku dat](control-flow-execute-data-flow-activity.md). Všichni uživatelé musí zadat, který Integration runtime má použít a předávat hodnoty parametrů. Další informace najdete v části [Azure Integration runtime](concepts-integration-runtime.md#azure-integration-runtime).

## <a name="debug-mode"></a>Režim ladění

Režim ladění umožňuje interaktivně zobrazit výsledky jednotlivých kroků transformace při sestavování a ladění toků dat. Ladicí relaci lze použít v v nástroji při sestavování logiky toku dat a spuštění ladění kanálu s aktivitami toku dat. Další informace najdete v dokumentaci v [režimu ladění](concepts-data-flow-debug-mode.md).

## <a name="monitoring-data-flows"></a>Monitorování toků dat

Mapování toku dat se integruje s existujícími možnostmi monitorování Azure Data Factory. Informace o tom, jak pochopit výstup monitorování toku dat, najdete v tématu [monitorování toků dat mapování](concepts-data-flow-monitoring.md).

Tým Azure Data Factory vytvořil [Průvodce optimalizací výkonu](concepts-data-flow-performance.md) , který vám pomůže optimalizovat dobu provádění toků dat po sestavení obchodní logiky.

## <a name="available-regions"></a>Dostupné oblasti

Datové toky mapování jsou k dispozici v následujících oblastech:

| Oblast Azure | Toky dat v ADF | Toky dat v synapse studiu |
| ------------ | ----------------- | ---------------------------- |
|  Austrálie – střed | | |  
| Austrálie – střed 2 | | |
| Austrálie – východ | ✓ |  ✓ |
| Australia Southeast   | ✓ | ✓ |
| Brazil South  | ✓ |  |
| Střední Kanada | ✓ |  |
| Indie – střed | ✓ |   ✓ |
| USA – střed    | ✓ |   ✓ |
| Čína – východ |      | ✓ |
| Čína – východ 2  |   |    |
| Čína – neregionální | | |
| Čína – sever |     | |
| Čína – sever 2 | |  |
| Východní Asie | ✓ | |
| East US   | ✓ | ✓ |
| USA – východ 2 | ✓ | ✓ |
| Francie – střed | ✓ | ✓ |
| Francie – jih  | | |
| Německo – střed (Svrchovan) | | |
| Německo – bez ohledu na oblast (Svrchovan) | | |
| Německo – sever (veřejné) | | |
| Německo – Severovýchod (Svrchovan) | | |
| Německo – středozápad (veřejné) |  | ✓ |
| Japan East | ✓ |  |
| Japonsko – západ |  | |
| Jižní Korea – střed | ✓ |  |
| Jižní Korea – jih | | |
| USA – středosever  | ✓ | ✓ |
| Severní Evropa  | ✓ |    |
| Norsko – východ | | |
| Norsko – západ | | |
| Jižní Afrika – sever    | ✓ | |
| Jižní Afrika – západ |  |    |
| Středojižní USA  | | ✓ |
| Indie – jih | | |
| Southeast Asia    | ✓ | ✓ |
| Švýcarsko – sever |   |  |
| Švýcarsko – západ | | |
| Spojené arabské emiráty – střed | | |
| Spojené arabské emiráty sever |  |    |
| Spojené království – jih  | ✓ |   | ✓ |
| Spojené království – západ |     | ✓ |
| US DoD – střed | |  |
| US DoD – východ | |  |
| USA (Gov) – Arizona |      |  |
| US Gov – bez ohledu na oblast | |  |
| USA (Gov) – Texas | |  |
| USA (Gov) – Virginia |     |  |
| USA – středozápad |     | ✓ |
| West Europe   | ✓ |   ✓ |
| Západní Indie | | |
| USA – západ   | ✓ |   |
| Západní USA 2 | ✓ |   ✓ | 

## <a name="next-steps"></a>Další kroky

* Naučte se vytvořit [zdrojovou transformaci](data-flow-source.md).
* Naučte se vytvářet toky dat v [režimu ladění](concepts-data-flow-debug-mode.md).
