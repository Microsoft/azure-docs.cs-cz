---
title: Sledování výkonu, využití & stavu Azure v Průzkumníku dat pomocí metrik
description: Zjistěte, jak pomocí metrik Průzkumníka dat Azure sledovat výkon, stav a využití clusteru.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/19/2020
ms.openlocfilehash: 1319b8cd6ac8a0eb83381c24bcde9996458e47a7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77560300"
---
# <a name="monitor-azure-data-explorer-performance-health-and-usage-with-metrics"></a>Sledování výkonu, stavu a využití Azure Data Exploreru pomocí metrik

Azure Data Explorer je rychlá, plně spravovaná služba analýzy dat pro analýzy velkých objemů dat v reálném čase, která se streamují z aplikací, webů, zařízení IoT a dalších. Pokud chcete použít Azure Data Explorer, musíte nejdříve vytvořit cluster a v něm vytvořit jednu nebo více databází. Do databáze potom ingestujete (načtete) data, abyste se na ně mohli dotazovat spouštěním dotazů. Metriky Průzkumníka dat Azure poskytují klíčové ukazatele stavu a výkonu prostředků clusteru. Pomocí metrik, které jsou podrobně popsané v tomto článku ke sledování stavu a výkonu clusteru Azure Data Explorer ve vašem konkrétním scénáři jako samostatné metriky. Metriky můžete také použít jako základ pro provozní [ovládací panely Azure](/azure/azure-portal/azure-portal-dashboards) a Azure [Alerts](/azure/azure-monitor/platform/alerts-metric-overview).

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud ho nemáte, můžete si vytvořit [bezplatný účet Azure](https://azure.microsoft.com/free/).
* [Cluster a databáze](create-cluster-database-portal.md).

## <a name="using-metrics"></a>Použití metrik

1. Přihlaste se k [portálu Azure](https://portal.azure.com/).
1. V clusteru Průzkumníka dat Azure vyberte **Metriky,** chcete-li otevřít podokno metrik a zahájit analýzu v clusteru.
    ![Vyberte](media/using-metrics/select-metrics.png)metriky .
1. Podokno Metriky: ![Metriky](media/using-metrics/metrics-pane.png)
    1. Chcete-li vytvořit graf metriky, vyberte **název metriky** a příslušnou **agregaci** podle metriky. Výběr **y oboru** a oboru názvů **metriky** jsou předem vybrané pro váš cluster Průzkumník dat Azure. Další informace o různých metrikách najdete v [tématu podporované metriky Průzkumníka dat Azure](#supported-azure-data-explorer-metrics).
    1. Výběrem **možnosti Přidat metriku** zobrazíte více metrik vykreslených ve stejném grafu.
    1. Výběrem **možnosti + Nový graf** zobrazíte více grafů v jednom zobrazení.
    1. Pomocí výběru času můžete změnit časový rozsah (výchozí: posledních 24 hodin).
    1. Použijte [ **použít filtr Přidat** a Použít **rozdělení** ](/azure/azure-monitor/platform/metrics-getting-started#apply-dimension-filters-and-splitting) pro metriky, které mají dimenze.
    1. Výběrem **možnosti Připnout na řídicí panel** přidáte konfiguraci grafu do řídicích panelů, abyste ji mohli znovu zobrazit.
    1. Nastavte **nové pravidlo výstrahy** pro vizualizaci metrik pomocí nastavených kritérií. Nové pravidlo upozornění bude zahrnovat cílové dimenze zdroje, metriky, rozdělení a filtrování z grafu. Upravte tato nastavení v [podokně vytvoření pravidla výstrah](/azure/azure-monitor/platform/metrics-charts#create-alert-rules).

Další informace o použití [Průzkumníka metrik](/azure/azure-monitor/platform/metrics-getting-started).

## <a name="supported-azure-data-explorer-metrics"></a>Podporované metriky Průzkumníka dat Azure

Podporované metriky Průzkumníka dat Azure jsou rozděleny do různých kategorií podle využití. 

### <a name="cluster-health-metrics"></a>Metriky stavu clusteru

Metriky stavu clusteru sledovat celkový stav clusteru. To zahrnuje využití prostředků a ingestování a odezvu.

**Metrika** | **Jednotka** | **Agregace** | **Popis metriky** | **Dimenze** |
|---|---|---|---|---|
| Využití mezipaměti | Procento | Vztaž, Max, Min | Procento přidělených prostředků mezipaměti, které cluster aktuálně používá. Mezipaměť je velikost SSD přidělené pro aktivitu uživatele podle definovaných zásad mezipaměti. Průměrné využití mezipaměti 80 % nebo méně je udržitelný stav clusteru. Pokud je průměrné využití mezipaměti vyšší než 80 %, měl by být cluster [škálován na](manage-cluster-vertical-scaling.md) cenovou úroveň optimalizovanou úložištěm nebo [škálovat](manage-cluster-horizontal-scaling.md) na více instancí. Případně můžete upravit zásady mezipaměti (méně dní v mezipaměti). Pokud využití mezipaměti je více než 100 %, velikost dat, které mají být uloženy do mezipaměti, podle zásad ukládání do mezipaměti, je větší než celková velikost mezipaměti v clusteru. | Žádný |
| Procesor | Procento | Vztaž, Max, Min | Procento přidělených výpočetních prostředků, které jsou aktuálně používány počítači v clusteru. Průměrný procesor 80 % nebo méně je pro cluster udržitelný. Maximální hodnota procesoru je 100 %, což znamená, že neexistují žádné další výpočetní prostředky pro zpracování dat. Pokud cluster nefunguje dobře, zkontrolujte maximální hodnotu procesoru a zjistěte, zda jsou blokovány určité procesory. | Žádný |
| Využití požití | Procento | Vztaž, Max, Min | Procento skutečných prostředků použitých k ingestování dat z celkových prostředků přidělených v zásadách kapacity k provedení ingestování. Výchozí zásady kapacity není více než 512 souběžných operací ingestování nebo 75 % prostředků clusteru investovaných do ingestování. Průměrné využití ingestování 80 % nebo méně je udržitelný stav pro cluster. Maximální hodnota využití ingestování je 100 %, což znamená, že se používá veškerá schopnost ingestování clusteru a může dojít k vytvoření fronty ingestování. | Žádný |
| Udržujte naživu | Počet | Avg | Sleduje odezvu clusteru. Plně responzivní cluster vrátí hodnotu 1 a blokovaný nebo odpojený cluster vrátí hodnotu 0. |
| Celkový počet příkazů s omezením | Počet | Vztaž, Max, Min, Součet | Počet příkazů omezení (odmítnutých) v clusteru, protože bylo dosaženo maximálního povoleného počtu souběžných (paralelních) příkazů. | Žádný |
| Celkový počet rozsahů | Počet | Vztaž, Max, Min, Součet | Celkový počet datových rozsahů v clusteru. Změny v této metriky může znamenat masivní změny struktury dat a vysoké zatížení clusteru, protože sloučení rozsahu dat je aktivita náročné na procesor. | Žádný |
| | | | |

### <a name="export-health-and-performance-metrics"></a>Exportovat metriky stavu a výkonu

Metriky stavu a výkonu exportu sledují celkový stav a výkon exportních operací, jako je zpoždění, výsledky, počet záznamů a využití.

**Metrika** | **Jednotka** | **Agregace** | **Popis metriky** | **Dimenze** |
|---|---|---|---|---|
Nepřetržitý exportní počet exportovaných záznamů    | Počet | Součet | Počet exportovaných záznamů ve všech úlohách průběžného exportu. | Žádný |
Nepřetržitý export max zpoždění |    Počet   | Maximum   | Zpoždění (v minutách) hlášené nepřetržité exportúlohy v clusteru. | Žádný |
Počet čekajících na trvalý export průběžně exportu | Počet | Maximum   | Počet čekajících úloh průběžného exportu. Tyto úlohy jsou připraveny ke spuštění, ale čekají ve frontě, pravděpodobně z důvodu nedostatečné kapacity). 
Výsledek nepřetržitého exportu    | Počet |   Počet   | Výsledek selhání a úspěchu každého nepřetržitého exportu. | ContinuousExportName |
Využití exportu |    Procento | Maximum   | Použitá exportní kapacita z celkové exportní kapacity v clusteru (mezi 0 a 100). | Žádný |
| | | | |

### <a name="ingestion-health-and-performance-metrics"></a>Metriky stavu a výkonu požití

Metriky stavu a výkonu při ingestování sledují celkový stav a výkon operací ingestování, jako je latence, výsledky a objem.

**Metrika** | **Jednotka** | **Agregace** | **Popis metriky** | **Dimenze** |
|---|---|---|---|---|
| Zpracované události (pro centra událostí/IoT) | Počet | Max, Min, Součet | Celkový počet událostí přečtených z center událostí a zpracovaných clusterem. Události jsou rozděleny na události odmítnuté a události přijaté clusterového modulu. | EventStatus |
| Latence požití | Sekundy | Vztaž, Max, Min | Latence přijatých dat od okamžiku, kdy byla data přijata v clusteru, dokud nebude připravena k dotazu. Doba latence přijím závisí na scénáři ingestování. | Žádný |
| Výsledek požití | Počet | Počet | Celkový počet operací ingestování, které se nezdařily a byly úspěšné. Pomocí **použít rozdělení** k vytvoření bloků úspěchu a selhání výsledků a analýze dimenzí (**Stav****hodnoty** > ).| IngestionResultDetails |
| Svazek požití (v MB) | Počet | Max, Součet | Celková velikost dat požitých do clusteru (v MB) před kompresí. | Databáze |
| | | | |  

### <a name="query-performance"></a>Výkon dotazů

Metriky výkonu dotazu sledují dobu trvání dotazu a celkový počet souběžných nebo omezené dotazy.

**Metrika** | **Jednotka** | **Agregace** | **Popis metriky** | **Dimenze** |
|---|---|---|---|---|
| Doba trvání dotazu | Milisekund | Vztaž, Min, Max, Součet | Celkový čas do přijetí výsledků dotazu (nezahrnuje latenci sítě). | Stav dotazu |
| Celkový počet souběžných dotazů | Počet | Vztaž, Max, Min, Součet | Počet dotazů spustit paralelně v clusteru. Tato metrika je dobrý způsob, jak odhadnout zatížení clusteru. | Žádný |
| Celkový počet dotazů s omezením | Počet | Vztaž, Max, Min, Součet | Počet dotazů s omezením (odmítnuto) v clusteru. Maximální počet povolených souběžných (paralelních) dotazů je definován v zásadách souběžných dotazů. | Žádný |
| | | | |

### <a name="streaming-ingest-metrics"></a>Metriky ingestování streamování

Metriky streamování sledování dat streamování a rychlosti požadavků, doby trvání a výsledků.

**Metrika** | **Jednotka** | **Agregace** | **Popis metriky** | **Dimenze** |
|---|---|---|---|---|
Rychlost streamování ingestování dat |    Počet   | RateRequestsPerSecond | Celkový objem dat požitých do clusteru. | Žádný |
Doba trvání ingestování streamování   | Milisekund  | Vztaž, Max, Min | Celková doba trvání všech požadavků na streamování ingestování. | Žádný |
Rychlost požadavků ingestování datových proudů   | Počet | Počet, Avg, Max, Min, Součet | Celkový počet požadavků na streamování při ingestování. | Žádný |
Výsledek ingestování streamování | Počet | Avg   | Celkový počet požadavků na streamování při ingestování podle typu výsledku. | Výsledek |
| | | | |

Další informace o [podporovaných metrikách clusteru Azure Data Explorer](/azure/azure-monitor/platform/metrics-supported#microsoftkustoclusters).


## <a name="next-steps"></a>Další kroky

* [Kurz: Ingestování a monitorování dotazů v Průzkumníku dat Azure](/azure/data-explorer/ingest-data-no-code)
* [Monitorování operací ingestování Průzkumníka dat Azure pomocí diagnostických protokolů](/azure/data-explorer/using-diagnostic-logs)
* [Rychlý start: Dotazování na data v Azure Data Exploreru](web-query-data.md)
