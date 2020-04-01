---
title: Monitorování Průzkumníka dat Azure pomocí stavu prostředků
description: Ke sledování prostředků Azure Data Exploreru použijte Azure Resource Health.
author: orspod
ms.author: orspodek
ms.reviewer: prvavill
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/31/2020
ms.openlocfilehash: 9b8b1b40e972d7719ef2cf495ed12cb4ed3478fd
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2020
ms.locfileid: "80479367"
---
# <a name="monitor-azure-data-explorer-using-resource-health-preview"></a>Monitorování Průzkumníka dat Azure pomocí stavu prostředků (preview)

[Resource Health](/azure/service-health/resource-health-overview) for Azure Data Explorer vás informuje o stavu vašeho prostředku Průzkumníka dat Azure a poskytuje užitečné doporučení k řešení problémů. Stav prostředků se aktualizuje každých 1-2 minut a hlásí aktuální a minulý stav vašich zdrojů. 

Stav prostředků určuje stav prostředku Průzkumníka dat Azure kontrolou různých kontrol stavu, jako jsou:
* Dostupnost prostředků
* Selhání dotazů

## <a name="access-resource-health-reporting"></a>Sestavy stavu prostředků aplikace Access

1. Na [webu Azure Portal](https://portal.azure.com/)vyberte **sledovat** ze seznamu služeb.
1. Vyberte možnost > **Stav zdroje** **služby**.
1. V rozevíracím souboru **Předplatným** vyberte předplatné. V rozevíracím seznamu **Typ prostředku** vyberte Průzkumník **dat Azure**.
1. Výsledná tabulka uvádí všechny prostředky ve zvoleném předplatném. Každý prostředek bude mít ikonu stavu označující stav prostředku.
1. Vyberte zdroj, chcete-li zobrazit [jeho stav prostředku](#resource-health-status) a doporučení.

    ![Přehled](media/monitor-with-resource-health/resource-health-overview.png)

## <a name="resource-health-status"></a>Stav zdroje

Stav prostředku je zobrazen s jedním z následujících stavů, k dispozici, není k dispozici a neznámý.

### <a name="available"></a>K dispozici.

Stav K **dispozici** označuje, že váš prostředek Azure Data Explorer je v pořádku a nemá žádné problémy.

![K dispozici.](media/monitor-with-resource-health/available.png)

### <a name="unavailable"></a>Neaktivní

Stav **Nedostupné** označuje, že je pokračující problém s prostředek Azure Data Explorer, který způsobí, že není k dispozici pro dotazy a ingestování. Například uzly ve vašem prostředku Průzkumníka dat Azure může mít neočekávaně restartován. Pokud váš prostředek Průzkumník dat Azure zůstane v tomto stavu delší dobu, obraťte se na [podporu]().

![Neaktivní](media/monitor-with-resource-health/unavailable.png)

### <a name="unknown"></a>Není známo

Stav **Neznámý** označuje, že **stav prostředků** neobdržel informace o tomto prostředku Průzkumníka dat Azure více než 10 minut. Tento stav není definitivní označení stavu prostředků Azure Data Explorer, ale je důležitým datovým bodem v procesu řešení potíží. Pokud váš cluster Průzkumník dat Azure funguje podle očekávání, stav se během několika minut změní na **K dispozici.** **Neznámý** stav může naznačovat, že událost v platformě ovlivňuje prostředek. 

> [!TIP]
> Stav prostředků clusteru Azure Data Explorer bude **Neznámý,** pokud je v zastaveném stavu.

![Není známo](media/monitor-with-resource-health/unknown.png)

## <a name="historical-information"></a>Historické informace

V podokně **Stav prostředků** > **historie stavu**získáte přístup k až čtyřem týdnům informací o stavu prostředků. Vyberte šipku pro další informace o problémech se stavem hlášených v tomto podokně. 

![Historie](media/monitor-with-resource-health/healthhistory.png)

## <a name="next-steps"></a>Další kroky

* [Konfigurace výstrah stavu prostředků](https://docs.microsoft.com/azure/service-health/resource-health-alert-arm-template-guide)
* [Kurz: Ingestování a monitorování dotazů v Průzkumníku dat Azure](ingest-data-no-code.md)
* [Sledování výkonu, stavu a využití Azure Data Exploreru pomocí metrik](using-metrics.md)