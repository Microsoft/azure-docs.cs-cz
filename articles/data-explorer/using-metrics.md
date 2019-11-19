---
title: Monitorování výkonu, stavu a využití Azure Průzkumník dat s metrikami
description: Naučte se používat metriky Azure Průzkumník dat k monitorování výkonu, stavu a využití clusteru.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/01/2019
ms.openlocfilehash: f5b47a5ae9d13711233d0e4852ec487af7344622
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/19/2019
ms.locfileid: "74173795"
---
# <a name="monitor-azure-data-explorer-performance-health-and-usage-with-metrics"></a>Monitorování výkonu, stavu a využití Azure Průzkumník dat s metrikami

Azure Data Explorer je rychlá, plně spravovaná služba analýzy dat pro analýzy velkých objemů dat v reálném čase, která se streamují z aplikací, webů, zařízení IoT a dalších. Pokud chcete použít Azure Průzkumník dat, musíte nejdřív vytvořit cluster a v tomto clusteru vytvořit jednu nebo víc databází. Pak data ingestujte do databáze, abyste na ni mohli spouštět dotazy. Metriky Azure Průzkumník dat poskytují klíčové indikátory pro stav a výkon prostředků clusteru. Pomocí metrik, které jsou podrobně popsané v tomto článku, můžete monitorovat stav a výkon služby Azure Průzkumník dat clusteru v konkrétním scénáři jako samostatné metriky. Metriky můžete použít také jako základ pro operační [řídicí panely Azure](/azure/azure-portal/azure-portal-dashboards) a [výstrahy Azure](/azure/azure-monitor/platform/alerts-metric-overview).

## <a name="prerequisites"></a>Požadavky

* Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet Azure](https://azure.microsoft.com/free/).

* Vytvořte [cluster a databázi](create-cluster-database-portal.md).

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlásit se na [Azure Portal](https://portal.azure.com/).

## <a name="using-metrics"></a>Použití metrik

V clusteru Azure Průzkumník dat vyberte **metriky** a otevřete podokno metriky a začněte analyzovat v clusteru.

![Vybrat metriky](media/using-metrics/select-metrics.png)

V podokně metriky:

![Podokno metriky](media/using-metrics/metrics-pane.png)

1. Chcete-li vytvořit graf metriky, vyberte možnost název **metriky** a relevantní **agregace** na jednu metriku, jak je popsáno níže. Výběry oboru názvů **prostředku** a **metriky** jsou předem vybrané do vašeho clusteru Azure Průzkumník dat.

    **Metrika** | **Jednotce** | **Agregace** | **Popis metriky**
    |---|---|---|---|
    | Využití mezipaměti | Procento | Střední, Max, min | Procento přidělených prostředků mezipaměti, které tento cluster aktuálně používá. Mezipaměť odkazuje na velikost jednotky SSD přidělené aktivitě uživatele podle definovaných zásad mezipaměti. Průměrné využití mezipaměti 80% nebo méně je udržitelný stav pro cluster. Pokud je průměrné využití mezipaměti nad 80%, cluster by se měl [škálovat až](manage-cluster-vertical-scaling.md) na cenovou úroveň optimalizované pro úložiště nebo [škálovat](manage-cluster-horizontal-scaling.md) na více instancí. Případně můžete zásadu mezipaměti upravit (méně dní v mezipaměti). Pokud je využití mezipaměti větší než 100%, velikost dat, která se mají ukládat do mezipaměti, podle zásad ukládání do mezipaměti, je větší než celková velikost mezipaměti v clusteru. |
    | Procesor | Procento | Střední, Max, min | Procento přidělených výpočetních prostředků, které jsou aktuálně používány počítači v clusteru. Průměrný procesor o 80% nebo méně je udržitelný pro cluster. Maximální hodnota CPU je 100%, což znamená, že pro zpracování dat nejsou k dispozici žádné další výpočetní prostředky. Pokud cluster nefunguje dobře, zkontrolujte maximální hodnotu CPU, abyste zjistili, jestli jsou blokované konkrétní procesory. |
    | Zpracované události (pro Event Hubs) | Počet | Max, min, Sum | Celkový počet událostí načtených z Center událostí a zpracovávaných clusterem. Události jsou rozdělené na odmítnuté události a události akceptované modulem clusteru. |
    | Latence přijímání | Sekundy | Střední, Max, min | Latence přijímaných dat od okamžiku, kdy byla data v clusteru přijata, dokud nebudou připravené pro dotaz. Doba latence příjmu závisí na scénáři přijímání. |
    | Výsledek ingestování | Počet | Počet | Celkový počet neúspěšných a úspěšných operací příjmu. Pomocí **použít rozdělení** můžete vytvořit intervaly úspěchu a výsledky selhání a analyzovat dimenze (**hodnota** > **stav**).|
    | Využití příjmu | Procento | Střední, Max, min | Procento skutečných prostředků používaných k ingestování dat z celkových přidělených prostředků v zásadě kapacity k provádění ingestování. Výchozí zásady kapacity neobsahují více než 512 souběžných operací příjmu nebo 75% prostředků clusteru, které jsou investovaly do ingestování. Průměrné využití příjmu 80% nebo méně je udržitelný stav pro cluster. Maximální hodnota využití příjmu je 100%, což znamená, že se používá veškerá schopnost ingestování clusteru a výsledkem může být fronta přijímání. |
    | Objem příjmu (v MB) | Počet | Max, min, Sum | Celková velikost dat, která se ingestují do clusteru (v MB) před kompresí |
    | Zachovat naživu | Počet | Volání | Sleduje rychlost odezvy clusteru. Plně reagující cluster vrátí hodnotu 1 a blokovaný nebo odpojený cluster vrátí hodnotu 0. |
    | Doba trvání dotazu | Sekundy | Count, AVG, min, Max, suma | Celková doba do přijetí výsledků dotazu (nezahrnuje latenci sítě). |
    | | | |

    Další informace týkající se [podporovaných metrik clusteru Azure Průzkumník dat](/azure/azure-monitor/platform/metrics-supported#microsoftkustoclusters)

2. Výběrem tlačítka **Přidat metriku** zobrazíte více metrik vykreslených ve stejném grafu.
3. Kliknutím na tlačítko **+ Nový graf** zobrazíte více grafů v jednom zobrazení.
4. Pomocí nástroje pro výběr času můžete změnit časový rozsah (výchozí: posledních 24 hodin).
5. Použijte [ **přidejte filtr** a **použijte rozdělení** ](/azure/azure-monitor/platform/metrics-getting-started#apply-dimension-filters-and-splitting) pro metriky, které mají rozměry.
6. Vyberte **Připnout na řídicí panel** , abyste mohli přidat konfiguraci grafu do řídicích panelů, abyste ho mohli znovu zobrazit.
7. Nastavte **nové pravidlo výstrahy** pro vizualizaci metrik pomocí zadaných kritérií. Nové pravidlo upozorňování bude obsahovat cílový prostředek, metriku, rozdělení a filtrování dimenzí v grafu. Tato nastavení upravte v [podokně vytvoření pravidla výstrahy](/azure/azure-monitor/platform/metrics-charts#create-alert-rules).

Další informace o použití [Průzkumník metrik](/azure/azure-monitor/platform/metrics-getting-started).


## <a name="next-steps"></a>Další kroky

* [Kurz: ingestování a dotazování dat monitorování v Azure Průzkumník dat](/azure/data-explorer/ingest-data-no-code)
* [Monitorování operací ingestování v Azure Průzkumník dat pomocí diagnostických protokolů](/azure/data-explorer/using-diagnostic-logs)
* [Rychlý start: Dotazování na data v Azure Data Exploreru](web-query-data.md)
