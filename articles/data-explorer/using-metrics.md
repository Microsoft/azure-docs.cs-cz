---
title: Monitorování výkonu, stavu a využití Azure Průzkumník dat s metrikami
description: Naučte se používat metriky Azure Průzkumník dat k monitorování výkonu, stavu a využití clusteru.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/19/2020
ms.openlocfilehash: 82aa7f782dbb1842a29d55eef8983edd4afce8eb
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/19/2020
ms.locfileid: "76277403"
---
# <a name="monitor-azure-data-explorer-performance-health-and-usage-with-metrics"></a>Monitorování výkonu, stavu a využití Azure Průzkumník dat s metrikami

Azure Data Explorer je rychlá, plně spravovaná služba analýzy dat pro analýzy velkých objemů dat v reálném čase, která se streamují z aplikací, webů, zařízení IoT a dalších. Pokud chcete použít Azure Průzkumník dat, musíte nejdřív vytvořit cluster a v tomto clusteru vytvořit jednu nebo víc databází. Pak data ingestujte do databáze, abyste na ni mohli spouštět dotazy. Metriky Azure Průzkumník dat poskytují klíčové indikátory pro stav a výkon prostředků clusteru. Pomocí metrik, které jsou podrobně popsané v tomto článku, můžete monitorovat stav a výkon služby Azure Průzkumník dat clusteru v konkrétním scénáři jako samostatné metriky. Metriky můžete použít také jako základ pro operační [řídicí panely Azure](/azure/azure-portal/azure-portal-dashboards) a [výstrahy Azure](/azure/azure-monitor/platform/alerts-metric-overview).

## <a name="prerequisites"></a>Požadavky

* Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet Azure](https://azure.microsoft.com/free/).

* Vytvořte [cluster a databázi](create-cluster-database-portal.md).

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k [Portálu Azure](https://portal.azure.com/).

## <a name="using-metrics"></a>Použití metrik

V clusteru Azure Průzkumník dat vyberte **metriky** a otevřete podokno metriky a začněte analyzovat v clusteru.

![Vybrat metriky](media/using-metrics/select-metrics.png)

V podokně metriky:

![Podokno metriky](media/using-metrics/metrics-pane.png)

1. Chcete-li vytvořit graf metriky, vyberte možnost název **metriky** a relevantní **agregace** na metriku. Pro váš cluster Azure Průzkumník dat se předem vybraly výběry oboru názvů **prostředků** a **metriky** . Další informace o různých metrikách najdete v tématu [podporované metriky služby Azure Průzkumník dat](#supported-azure-data-explorer-metrics).
1. Výběrem tlačítka **Přidat metriku** zobrazíte více metrik vykreslených ve stejném grafu.
1. Kliknutím na tlačítko **+ Nový graf** zobrazíte více grafů v jednom zobrazení.
1. Pomocí nástroje pro výběr času můžete změnit časový rozsah (výchozí: posledních 24 hodin).
1. Použijte [ **přidejte filtr** a **použijte rozdělení** ](/azure/azure-monitor/platform/metrics-getting-started#apply-dimension-filters-and-splitting) pro metriky, které mají rozměry.
1. Vyberte **Připnout na řídicí panel** , abyste mohli přidat konfiguraci grafu do řídicích panelů, abyste ho mohli znovu zobrazit.
1. Nastavte **nové pravidlo výstrahy** pro vizualizaci metrik pomocí zadaných kritérií. Nové pravidlo upozorňování bude obsahovat cílový prostředek, metriku, rozdělení a filtrování dimenzí v grafu. Tato nastavení upravte v [podokně vytvoření pravidla výstrahy](/azure/azure-monitor/platform/metrics-charts#create-alert-rules).

Další informace o použití [Průzkumník metrik](/azure/azure-monitor/platform/metrics-getting-started).

## <a name="supported-azure-data-explorer-metrics"></a>Podporované metriky služby Azure Průzkumník dat

Podporované metriky služby Azure Průzkumník dat jsou v závislosti na využití rozdělené do různých kategorií. 

### <a name="cluster-health-metrics"></a>Metriky stavu clusteru

Metriky stavu clusteru sledují obecný stav clusteru. To zahrnuje využití prostředků a přijímání a rychlost odezvy.

**Metrika** | **Jednotce** | **Agregace** | **Popis metriky**
|---|---|---|---|
| Využití mezipaměti | Procento | Střední, Max, min | Procento přidělených prostředků mezipaměti, které tento cluster aktuálně používá. Mezipaměť je velikost jednotky SSD přidělená aktivitě uživatele podle definovaných zásad mezipaměti. Průměrné využití mezipaměti 80% nebo méně je udržitelný stav pro cluster. Pokud je průměrné využití mezipaměti nad 80%, cluster by se měl [škálovat až](manage-cluster-vertical-scaling.md) na cenovou úroveň optimalizované pro úložiště nebo [škálovat](manage-cluster-horizontal-scaling.md) na více instancí. Případně můžete zásadu mezipaměti upravit (méně dní v mezipaměti). Pokud je využití mezipaměti větší než 100%, velikost dat, která se mají ukládat do mezipaměti, podle zásad ukládání do mezipaměti, je větší než celková velikost mezipaměti v clusteru. |
| Procesor | Procento | Střední, Max, min | Procento přidělených výpočetních prostředků, které jsou aktuálně používány počítači v clusteru. Průměrný procesor o 80% nebo méně je udržitelný pro cluster. Maximální hodnota CPU je 100%, což znamená, že pro zpracování dat nejsou k dispozici žádné další výpočetní prostředky. Pokud cluster nefunguje dobře, zkontrolujte maximální hodnotu CPU, abyste zjistili, jestli jsou blokované konkrétní procesory. |
| Využití příjmu | Procento | Střední, Max, min | Procento skutečných prostředků používaných k ingestování dat z celkových přidělených prostředků v zásadě kapacity k provádění ingestování. Výchozí zásady kapacity neobsahují více než 512 souběžných operací příjmu nebo 75% prostředků clusteru, které jsou investovaly do ingestování. Průměrné využití příjmu 80% nebo méně je udržitelný stav pro cluster. Maximální hodnota využití příjmu je 100%, což znamená, že se používá veškerá schopnost ingestování clusteru a výsledkem může být fronta přijímání. |
| Zachovat naživu | Počet | Průměr | Sleduje rychlost odezvy clusteru. Plně reagující cluster vrátí hodnotu 1 a blokovaný nebo odpojený cluster vrátí hodnotu 0. |
| Celkový počet příkazů s omezením | Počet | Střední, Max, min, Sum | Počet příkazů s omezením (zamítnutých) v clusteru, protože bylo dosaženo maximálního povoleného počtu souběžných (paralelních) příkazů. |
| Celkový počet rozsahů | Počet | Střední, Max, min, Sum | Celkový počet rozsahů dat v clusteru. Změny v této metrikě můžou znamenat obrovské změny struktury dat a vysoké zatížení clusteru, protože sloučení rozsahů dat je náročné na procesor. |
| | | | |

### <a name="export-health-and-performance-metrics"></a>Exportovat metriky stavu a výkonu

Vyexportujte metriky stavu a výkonu, které sledují obecný stav a výkon operací exportu, jako je opožděnost, výsledky, počet záznamů a využití.

**Metrika** | **Jednotce** | **Agregace** | **Popis metriky**
|---|---|---|---|
Průběžný export počtu exportovaných záznamů    | Počet | Součet | Celkový počet záznamů exportovaných z clusteru. |
Maximální počet minut zpoždění pro průběžný export |    Počet   | Max.   | Maximální hodnota v minutách exportovaných záznamů|
Počet nevyřízených položek průběžného exportu | Počet | Max.   | Maximální hodnota nedokončených operací exportu
Výsledek průběžného exportu    | Počet |   Počet   | Celkový počet průběžných operací exportu podle výsledku. Metrika zahrnuje název a databázi průběžného exportu. 
Využití exportu |    Procento | Max.   | Použití definovaného slotu pro operace exportu
| | | | |

### <a name="ingestion-health-and-performance-metrics"></a>Metriky stavu přijímání a výkonu

Metriky stavu příjmu a výkonu sledují celkový stav a výkon operací příjmu, jako je latence, výsledky a objem.

**Metrika** | **Jednotce** | **Agregace** | **Popis metriky**
|---|---|---|---|
| Zpracované události (pro Event/centra IoT) | Počet | Max, min, Sum | Celkový počet událostí načtených z Center událostí a zpracovávaných clusterem. Události jsou rozdělené na odmítnuté události a události akceptované modulem clusteru. |
| Latence přijímání | Sekund | Střední, Max, min | Latence přijímaných dat od okamžiku, kdy byla data v clusteru přijata, dokud nebudou připravené pro dotaz. Doba latence příjmu závisí na scénáři přijímání. |
| Výsledek ingestování | Počet | Počet | Celkový počet neúspěšných a úspěšných operací příjmu. Pomocí **použít rozdělení** můžete vytvořit intervaly úspěchu a výsledky selhání a analyzovat dimenze (**hodnota** > **stav**).|
| Objem příjmu (v MB) | Počet | Max, Sum | Celková velikost dat, která se ingestují do clusteru (v MB) před kompresí |
| | | | |  

### <a name="query-performance"></a>Výkon dotazů

Metrika výkonu dotazů sleduje dobu trvání dotazu a celkový počet souběžných nebo omezených dotazů.

**Metrika** | **Jednotce** | **Agregace** | **Popis metriky**
|---|---|---|---|
| Doba trvání dotazu | Milisekundy | Prům., min, Max, Sum | Celková doba do přijetí výsledků dotazu (nezahrnuje latenci sítě). |
| Celkový počet souběžných dotazů | Počet | Střední, Max, min, Sum | Počet dotazů běží paralelně v clusteru. Tato metrika je dobrým způsobem, jak odhadnout zatížení clusteru. |
| Celkový počet omezených dotazů | Počet | Střední, Max, min, Sum | Počet neomezených (zamítnutých) dotazů v clusteru. Maximální povolený počet souběžných (paralelních) dotazů je definovaný v zásadách souběžného dotazování. |
| | | | |

### <a name="streaming-ingest-metrics"></a>Metriky příjmu datových proudů

Metriky ingestování streamování sleduje data příjmu streamování a rychlost požadavků, dobu trvání a výsledky.

**Metrika** | **Jednotce** | **Agregace** | **Popis metriky**
|---|---|---|---|
Přenosová rychlost ingestování datových proudů |    Počet   | RateRequestsPerSecond | Celkový objem dat přijatých do clusteru. |
Doba ingestování streamování   | Milisekundy  | Střední, Max, min | Celková doba trvání všech požadavků na příjem dat ze streamování |
Rychlost přijímání požadavků pro streamování   | Počet | Počet, prům., Max, min, Sum | Celkový počet požadavků na příjem dat ze streamování |
Výsledek ingestování streamování | Počet | Průměr   | Celkový počet požadavků na příjem dat ze streamování podle typu výsledku. |
| | | | |

Další informace o [podporovaných metrikách clusteru Azure Průzkumník dat](/azure/azure-monitor/platform/metrics-supported#microsoftkustoclusters)


## <a name="next-steps"></a>Další kroky

* [Kurz: ingestování a dotazování dat monitorování v Azure Průzkumník dat](/azure/data-explorer/ingest-data-no-code)
* [Monitorování operací ingestování v Azure Průzkumník dat pomocí diagnostických protokolů](/azure/data-explorer/using-diagnostic-logs)
* [Rychlý start: Dotazování na data v Azure Data Exploreru](web-query-data.md)
