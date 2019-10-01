---
title: Globálně distribuované transakční a analytické úložiště pro kontejnery Azure Cosmos
description: Přečtěte si o transakčních a analytických úložištích a možnostech jejich konfigurace pro kontejnery Azure Cosmos.
author: rimman
ms.author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/30/2019
ms.reviewer: sngun
ms.openlocfilehash: 27ca2102ee95273fbedd1a870e57d2ae3318e879
ms.sourcegitcommit: 6fe40d080bd1561286093b488609590ba355c261
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/01/2019
ms.locfileid: "71703385"
---
# <a name="globally-distributed-transactional-and-analytical-storage-for-azure-cosmos-containers"></a>Globálně distribuované transakční a analytické úložiště pro kontejnery Azure Cosmos

Služba Azure Cosmos Container je interně založená na dvou úložištích – transakční modul úložiště a aktualizovatelný modul analytického úložiště. Úložné moduly jsou strukturované a optimalizované pro zápis pro rychlejší aktualizace. Každý z nich je však kódován odlišně:

* **Transakční modul úložiště** – je kódovaný v řádkovém formátu pro rychlé transakční čtení a dotazy.

* **Analytický modul úložiště** – je kódovaný ve sloupcovém formátu pro rychlé analytické dotazy a kontroly.

![Moduly úložiště a mapování Azure Cosmos DB API](./media/globally-distributed-transactional-analytical-storage/storage-engines-api-mapping.png)

Transakční modul úložiště je zálohovaný místními SSD, zatímco analytické úložiště je uložené v nenákladovém úložišti SSD mimo cluster. Následující tabulka zachycuje významné rozdíly mezi transakčním a analytickým úložištěm.


|Funkce  |Transakční úložiště  |Analytické úložiště |
|---------|---------|---------|
|Maximální velikost úložiště na jeden kontejner Azure Cosmos |   Bez omezení      |    Bez omezení     |
|Maximální velikost úložiště na klíč logického oddílu   |   10 GB      |   Bez omezení      |
|Kódování úložiště  |   Řádky orientované pomocí interního formátu.   |   Sloupce orientované pomocí formátu Apache Parquet. |
|Místní úložiště |   Replikované úložiště, které je založené na místním/souběžném clusteru SSD. |  Replikované úložiště zajištěné nenáročným vzdáleným/vypnutým clusterem SSD.       |
|Stálost  |    99,99999 (7-9 s)     |  99,99999 (7-9 s)       |
|Rozhraní API, která přistupují k datům  |   SQL, MongoDB, Cassandra, Gremlin, Tables a Etcd.       | Apache Spark         |
|Uchování (Time-to-Live nebo TTL)   |  Řízené zásadami nakonfigurovanými na kontejneru Azure Cosmos pomocí vlastnosti `DefaultTimeToLive`.       |   Řízené zásadami nakonfigurovanými na kontejneru Azure Cosmos pomocí vlastnosti `ColumnStoreTimeToLive`.      |
|Cena za GB    |   0,25/GB      |  $0,02/GB       |
|Cena za transakce úložiště    | Zajištěná propustnost se účtuje $0,008 za 100 RU/s s hodinovou fakturací.        |  Propustnost na základě spotřeby se účtuje $0,05 pro 10 000 transakcí zápisu a $0,004 pro transakce s 10 000m čtením.       |

## <a name="benefits-of-transactional-and-analytical-storage"></a>Výhody transakčního a analytického úložiště

### <a name="no-etl-operations"></a>Žádné operace ETL

Tradiční analytické kanály jsou složité s více fázemi, které vyžadují operace extrakce, transformace a načítání (ETL) do a z výpočetních a úložných vrstev. Výsledkem je komplexní architektury zpracování dat. Což znamená vysoké náklady na více fází úložiště a výpočetních prostředků a vysokou latenci kvůli obrovským objemům dat přenesených mezi různými fázemi úložiště a výpočetními prostředky.  

Nehrozí žádná režie na provádění operací ETL pomocí Azure Cosmos DB. Každý kontejner Azure Cosmos je zálohovaný pomocí transakčního i analytického strojového úložiště a přenos dat mezi transakčním a analytickým modulem úložiště se provádí v rámci databázového stroje a bez směrování sítě. Výsledná latence a náklady jsou v porovnání s tradičními analytickými řešeními výrazně nižší. A získáte jeden globálně distribuovaný úložný systém pro transakční i analytické úlohy.  

### <a name="store-multiple-versions-update-and-query-the-analytical-storage"></a>Uložení více verzí, aktualizace a dotazování analytického úložiště

Analytické úložiště je plně aktualizovatelné a obsahuje kompletní historii všech transakčních aktualizací, ke kterým došlo v kontejneru Azure Cosmos.

Jakékoli aktualizace transakčního úložiště jsou zaručené k analýze úložiště do 30 sekund. 

### <a name="globally-distributed-multi-master-analytical-storage"></a>Globálně distribuované analytické úložiště s více hlavními servery

Pokud je váš účet Azure Cosmos vymezený na jednu oblast, jsou uložená data (v transakčním a analytickém úložišti) v kontejnerech také vymezena v jedné oblasti. Na druhou stranu platí, že pokud je účet Azure Cosmos globálně distribuovaný, data uložená v kontejnerech se také globálně distribuují.

U účtů Azure Cosmos konfigurovaných s více oblastmi zápisu se zápisy do kontejneru (do transakčního i analytického úložiště) vždycky provádějí v místní oblasti, a proto jsou rychlé.

Pro jeden nebo více oblastí Azure Cosmos účet bez ohledu na to, jestli je jedna oblast pro zápis (jedna předloha) nebo více oblastí zápisu (označovaná také jako vícenásobný příklad), provádějí se transakční i analytické čtení a dotazy v dané oblasti místně.

### <a name="performance-isolation-between-transactional-and-analytical-workloads"></a>Izolace výkonu mezi transakčními a analytickými úlohami

V dané oblasti transakční úlohy fungují na transakčním a řádkovém úložišti vašeho kontejneru. Na druhé straně analytické úlohy působí v rámci analytického a sloupcového úložiště vašeho kontejneru. Tyto dva úložné motory pracují nezávisle a poskytují striktní izolaci výkonu mezi úlohami.

Transakční úlohy využívají zřízenou propustnost (ru). Na rozdíl od transakčních úloh je propustnost analytických úloh založena na skutečné spotřebě. Analytické úlohy využívají prostředky na vyžádání.

### <a name="on-demand-snapshots-and-time-travel-analytics"></a>Snímky na vyžádání a analýza času – cestování

Snímky dat uložených v analytickém úložišti vašich kontejnerů Azure Cosmos můžete kdykoli pořizovat voláním příkazu `CreateSnapshot (name, timestamp)` na kontejneru. Snímky jsou pojmenovány "záložky" v historii aktualizací, které byly někdy provedeny ve vašem kontejneru.

![Snímky na vyžádání a analýza času – cestování](./media/globally-distributed-transactional-analytical-storage/ondemand-analytical-data-snapshots.png)

V době vytváření snímku můžete kromě názvu zadat časové razítko, které definuje stav kontejneru v historii aktualizací. Data snímku pak můžete načíst do Sparku a provádět dotazy.

V současné době můžete na vyžádání kdykoli pořizovat snímky, takže automatické pořizování snímků na základě plánu nebo vlastních zásad se zatím nepodporují.

### <a name="configure-and-tier-data-between-transactional-and-analytical-storage-independently"></a>Nezávisle nakonfigurujte data a vrstvu mezi transakčním a analytickým úložištěm.

V závislosti na vašem scénáři můžete nezávisle povolit nebo zakázat každý ze dvou modulů úložiště. Konfigurace pro jednotlivé scénáře jsou následující:

|Scénář |Nastavení transakčního úložiště  |Nastavení analytického úložiště |
|---------|---------|---------|
|Výhradně spouštění analytických úloh (s nekonečným uchováváním) |  DefaultTimeToLive = 0       |  ColumnStoreTimeToLive =-1       |
|Spouštění transakčních úloh výhradně (s nekonečným uchováváním)  |   DefaultTimeToLive =-1      |  ColumnStoreTimeToLive = 0       |
|Spuštění transakčních i analytických úloh (s nekonečným uchováváním)   |   DefaultTimeToLive =-1      | ColumnStoreTimeToLive =-1        |
|Spuštění transakčních a analytických úloh (s různými intervaly uchovávání informací, označované také jako vrstvení úložiště)  |  DefaultTimeToLive = <Value1>       |     ColumnStoreTimeToLive = <Value2>    |

1. **Konfigurace kontejneru výhradně pro analytické úlohy (s nekonečným uchováváním)**

   Kontejner Azure Cosmos můžete nakonfigurovat výhradně pro analytické úlohy. Tato konfigurace má výhodu, kdy nemusíte platit za transakční úložiště. Pokud je vaším cílem použít kontejner jenom pro analytické úlohy, můžete transakční úložiště zakázat nastavením `DefaultTimeToLive` na 0 v kontejneru Cosmos a můžete povolit analytické úložiště s nekonečným uchováním nastavením `ColumnStoreTimeToLive` na-1.

   ![Analytické úlohy s nekonečným uchováváním](./media/globally-distributed-transactional-analytical-storage/analytical-workload-configuration.png)

1. **Konfigurace kontejneru výhradně pro transakční úlohy (s nekonečné uchovávání)**

   Kontejner Azure Cosmos můžete nakonfigurovat výhradně pro transakční úlohy. Analytické úložiště můžete zakázat nastavením `ColumnStoreTimeToLive` na 0 v kontejneru a můžete povolit analytické úložiště s nekonečným uchováváním nastavením `DefaultTimeToLive` na-1.

   ![Transakční úlohy s nekonečným uchováváním](./media/globally-distributed-transactional-analytical-storage/transactional-workload-configuration.png)

1. **Konfigurace kontejneru pro transakční i analytické úlohy (s nekonečným uchováváním)**

   Kontejner Azure Cosmos můžete nakonfigurovat pro transakční i analytické úlohy s úplnou izolací výkonu mezi nimi. Analytické úložiště můžete povolit nastavením `ColumnStoreTimeToLive` na-1 a povolením transakčního úložiště s nekonečným uchováním nastavením `DefaultTimeToLive ` na-1.

   ![Transakční a analytické úlohy s nekonečným uchováváním](./media/globally-distributed-transactional-analytical-storage/analytical-transactional-configuration-infinite-retention.png)

1. **Konfigurace kontejneru pro transakční i analytické úlohy s využitím vrstvení úložiště**

   Kontejner Azure Cosmos můžete nakonfigurovat pro transakční i analytické úlohy s úplnou izolací výkonu mezi nimi a různými intervaly uchovávání. Azure Cosmos DB vynutilo, aby se analytické úložiště vždycky zachovalo delší dobu než transakční úložiště.

   Transakční úložiště můžete povolit s nekonečným uchováváním nastavením `DefaultTimeToLive` na < hodnoty 1 > a povolením analytického úložiště nastavením `ColumnStoreTimeToLive` na < hodnoty 2 >. Azure Cosmos DB vyhodnotí, že < hodnota 2 > je vždycky větší než < hodnota 1 >.

   ![Transakční a analytické úlohy s využitím vrstev úložiště](./media/globally-distributed-transactional-analytical-storage/analytical-transactional-configuration-specified-retention.png)

## <a name="next-steps"></a>Další kroky

* [Čas do provozu v Azure Cosmos DB](time-to-live.md)
