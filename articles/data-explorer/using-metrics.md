---
title: Sledování výkonu, stavu a využití s metrikami Průzkumník dat Azure
description: Další informace o použití Průzkumníka služby Azure Data metriky pro monitorování výkonu, stavu a využití clusteru.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/01/2019
ms.openlocfilehash: a9c9f4d827d21c374bebba9d39e33b0bcad8a83e
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2019
ms.locfileid: "59050597"
---
# <a name="monitor-azure-data-explorer-performance-health-and-usage-with-metrics"></a>Sledování výkonu, stavu a využití s metrikami Průzkumník dat Azure

Azure Data Explorer je rychlá, plně spravovaná služba analýzy dat pro analýzy velkých objemů dat v reálném čase, která se streamují z aplikací, webů, zařízení IoT a dalších. Použití Průzkumníku dat Azure, nejprve vytvoříte cluster a vytvořit jednu nebo více databází v tomto clusteru. Pak můžete ingestovat data (načíst) do databáze tak, aby u ní můžete spouštět dotazy. Metriky Azure Průzkumník dat poskytují klíčové indikátory stavu a výkonu prostředků clusteru. Použijte metriky, které jsou podrobně popsané v tomto článku k monitorování stavu clusteru Průzkumník dat Azure a výkonu v váš konkrétní scénář jako samostatné metriky. Metriky můžete také použít jako základ pro provozní [řídicích panelů Azure](/azure/azure-portal/azure-portal-dashboards) a [Azure Alerts](/azure/azure-monitor/platform/alerts-metric-overview).

## <a name="prerequisites"></a>Požadavky

* Pokud ještě nemáte předplatné Azure, vytvořte [bezplatný účet Azure](https://azure.microsoft.com/free/).

* Vytvoření [clusteru a databáze](create-cluster-database-portal.md).

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

## <a name="using-metrics"></a>Pomocí metrik

V Průzkumníku dat Azure clusteru, vyberte **metriky** otevřete podokno metriky a zahájení analýzy ve vašem clusteru.

![Vybrat metriky](media/using-metrics/select-metrics.png)

V podokně metriky:

![Podokno metriky](media/using-metrics/metrics-pane.png)

1. Chcete-li vytvořit grafu metrik, vyberte **metrika** názvu a relevantní **agregace** na metriku, jak je uvedeno níže. **Prostředků** a **metrika Namespace** výběr jsou předem vybraná ke svému clusteru Průzkumník dat Azure.

    **Metrika** | **Jednotka** | **Agregace** | **Popis metriky**
    |---|---|---|---|
    | Využití mezipaměti | Procento | Avg, Max, Min | Procento prostředky přidělené mezipaměti aktuálně používané clusterem. Mezipaměť odkazuje na velikost přidělené aktivity uživatelů podle zásad definovaných mezipaměti na SSD. Mezipaměti průměrné využití 80 % nebo méně se do udržitelné stavu pro cluster. Pokud mezipaměti průměrné využití přes 80 %, měl by být clusteru [vertikálně navýšit](manage-cluster-scale-up.md) do úložiště optimalizované cenové úrovně nebo [škálované](manage-cluster-scale-out.md) na více instancí. Můžete také přizpůsobit zásady ukládání do mezipaměti (v mezipaměti menší počet dnů). Pokud je využití mezipaměti více než 100 %, velikost dat do mezipaměti podle zásady ukládání do mezipaměti, je větší, celková velikost mezipaměti v clusteru. |
    | Procesor | Procento | Avg, Max, Min | Procento přidělených výpočetních prostředků aktuálně používán počítače v clusteru. Průměrné využití procesoru 80 % nebo méně je udržitelné pro cluster. Maximální hodnota využití procesoru je 100 %, což znamená, že neexistují žádné další výpočetní prostředky, které zpracovávají data. Pokud cluster není dobře, zkontrolujte maximální hodnota CPU a určí, jestli je konkrétní procesory, které jsou blokovány. |
    | Události byly zpracovány (pro službu Event Hubs) | Počet | Max, Min, součet | Celkový počet událostí čtení ze služby event hubs a zpracovány v clusteru. Události jsou rozděleny do odmítl a události přijal modul clusteru. |
    | Latence příjmu dat | Sekundy | Avg, Max, Min | Latence dat přijatých od okamžiku, kdy data byla přijata v clusteru, dokud nebude připravené pro dotaz. Doby latence příjmu závisí na scénáři příjmu. |
    | Příjem výsledků | Počet | Počet | Celkový počet operací příjmu, které se nezdařilo a byla úspěšná. Použití **použít rozdělení** vytvoření bloků úspěšné a neúspěšné výsledky a analýza dimenze (**hodnotu** > **stav**).|
    | Ingestování využití | Procento | Avg, Max, Min | Procento skutečné prostředky používané k ingestování dat z celkové množství prostředků přidělených v zásadách kapacitu, provádět ingestování. Výchozí zásada kapacity je delší než 512 souběžných přijímání operations Console nebo 75 % investovali do ingestování prostředky clusteru. Průměrný příjem využití 80 % nebo méně, je udržitelné stavu pro cluster. Maximální hodnota ingestování využití je 100 %, což znamená, že se používá možnost příjmu všech clusteru a může vést ingestování fronty. |
    | Ingestování svazek (v MB) | Počet | Max, Min, součet | Celková velikost dat přijatých do clusteru (v MB) před kompresí. |
    | Zachování | Počet | Prům. | Sleduje schopnost reagovat na clusteru. Vrací hodnotu 1, plně interaktivní clusteru a cluster blokované nebo odpojených vrátí hodnotu 0. |
    | Doba trvání dotazu | Sekundy | Počet, Avg, Min, Max, součet | Celkový čas, dokud se výsledky dotazu jsou přijímány (nezahrnuje latenci sítě). |
    | | | |

    Další informace týkající se [podporované metriky clusteru Průzkumník dat Azure](/azure/azure-monitor/platform/metrics-supported#microsoftkustoclusters)

2. Vyberte **přidat metriku** tlačítko zobrazit několik metrik zobrazených ve stejném grafu.
3. Vyberte **+ nový graf** tlačítko zobrazit několik grafů v jednom zobrazení.
4. Pomocí nástroje pro výběr času změnit časový rozsah (výchozí: za posledních 24 hodin).
5. Použití [ **přidat filtr** a **použít rozdělení** ](/azure/azure-monitor/platform/metrics-getting-started#apply-dimension-filters-and-splitting) pro metriky, které mají dimenze.
6. Vyberte **připnout na řídicí panel** Přidat konfiguraci graf na řídicí panely tak, aby ji mohly znovu zobrazit.
7. Nastavte **nové pravidlo upozornění** vizualizovat metriky na základě sadu kritérií. Nové pravidlo výstrah bude obsahovat váš cílový prostředek, metriky, rozdělení a filtr dimenzí z grafu. Změna tohoto nastavení [podokně vytvoření pravidla upozornění](/azure/azure-monitor/platform/metrics-charts#create-alert-rules).

Další informace o používání [Průzkumníka metrik](/azure/azure-monitor/platform/metrics-getting-started).


## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Rychlé zprovoznění: Dotazování dat v Průzkumníku dat Azure](web-query-data.md)
