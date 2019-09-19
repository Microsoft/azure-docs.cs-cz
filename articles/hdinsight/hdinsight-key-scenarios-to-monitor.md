---
title: Monitorování výkonu clusteru – Azure HDInsight
description: Jak monitorovat stav a výkon Apache Hadoopch clusterů ve službě Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/29/2019
ms.openlocfilehash: 7a7544ef9fe5724d1f6c11918411a76461d908e5
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2019
ms.locfileid: "71104405"
---
# <a name="monitor-cluster-performance-in-azure-hdinsight"></a>Monitorování výkonu clusteru v Azure HDInsight

Monitorování stavu a výkonu clusteru HDInsight je nezbytné pro zachování optimálního výkonu a využití prostředků. Monitorování vám taky může pomáhat při detekci a adresování chyb konfigurace clusteru a potíží s uživatelským kódem.

Následující části popisují, jak monitorovat a optimalizovat zatížení clusterů, Apache Hadoopch PŘÍZových front a zjišťovat problémy s omezením úložiště.

## <a name="monitor-cluster-load"></a>Monitorování zatížení clusteru

Clustery Hadoop můžou poskytovat optimální výkon, když je zatížení clusteru rovnoměrně rozdělené napříč všemi uzly. To umožňuje spouštět úlohy zpracování bez omezení paměti RAM, procesoru nebo diskových prostředků na jednotlivých uzlech.

Pokud se chcete podívat na nejvyšší úroveň uzlů clusteru a jejich načítání, přihlaste se k [webovému uživatelskému rozhraní Ambari](hdinsight-hadoop-manage-ambari.md)a pak vyberte kartu **hostitelé** . Vaši hostitelé jsou uvedeni podle jejich plně kvalifikovaného názvu domény. Stav provozu každého hostitele se zobrazuje pomocí barevného indikátoru stavu:

| Barva | Popis |
| --- | --- |
| Červená | Nejméně jedna hlavní součást v hostiteli je mimo provoz. Když najedete myší, zobrazí se popis, který obsahuje seznam ovlivněných součástí. |
| Oranžová | Nejméně jedna sekundární součást v hostiteli je mimo provoz. Když najedete myší, zobrazí se popis, který obsahuje seznam ovlivněných součástí. |
| Žlutá | Server Ambari neobdržel prezenční signál od hostitele po dobu delší než 3 minuty. |
| Zelená | Normální stav spuštění. |

Zobrazí se také sloupce zobrazující počet jader a velikost paměti RAM pro každého hostitele a využití disku a průměr zatížení.

![Přehled karty hostitelé Apache Ambari](./media/hdinsight-key-scenarios-to-monitor/apache-ambari-hosts-tab.png)

Pro podrobný pohled na komponenty běžící na daném hostiteli a jejich metrikách vyberte libovolný název hostitele. Metriky se zobrazují jako vybraná časová osa využití procesoru, zatížení, využití disku, využití paměti, využití sítě a počet procesů.

![Přehled informací o hostiteli Apache Ambari](./media/hdinsight-key-scenarios-to-monitor/apache-ambari-host-details.png)

Podrobnosti o nastavení výstrah a zobrazování metrik najdete v tématu [Správa clusterů HDInsight pomocí webového uživatelského rozhraní Apache Ambari](hdinsight-hadoop-manage-ambari.md) .

## <a name="yarn-queue-configuration"></a>Konfigurace fronty PŘÍZe

Hadoop má v distribuované platformě spuštěné různé služby. PŘÍZe (ještě jiný projednatel prostředků) tyto služby koordinuje a přiděluje prostředky clusteru, aby se zajistilo, že se zatížení rovnoměrně distribuuje napříč clusterem.

PŘÍZe rozdělí dvě zodpovědnosti JobTracker, správu prostředků a plánování a monitorování úloh do dvou procesů démonů: globální Správce prostředků a ApplicationMaster pro jednotlivé aplikace.

Správce prostředků je *čistě Plánovač*a výhradně arbitrates dostupné prostředky mezi všemi konkurenčními aplikacemi. Správce prostředků zajistí, aby se všechny prostředky vždycky používaly, optimalizace pro různé konstanty, jako je SLA, záruky kapacity a tak dále. ApplicationMaster vyjednává prostředky z Správce prostředků a spolupracuje s NodeManager pro spouštění a monitorování kontejnerů a jejich spotřeby prostředků.

Pokud více tenantů sdílí velký cluster, existuje konkurence pro prostředky clusteru. CapacityScheduler je připojitelný Plánovač, který pomáhá při sdílení prostředků zařazováním požadavků do fronty. CapacityScheduler také podporuje *hierarchické fronty* , aby bylo zajištěno, že prostředky jsou sdíleny mezi podfrontami organizace, předtím, než fronty jiných aplikací můžou používat bezplatné prostředky.

PŘÍZe nám umožňuje přidělit prostředky těmto frontám a zjistit, jestli jsou přiřazené všechny dostupné prostředky. Chcete-li zobrazit informace o frontách, přihlaste se k webovému uživatelskému rozhraní Ambari a pak v horní nabídce vyberte možnost **správce fronty příze** .

![Správce fronty Ambari nitě Apache](./media/hdinsight-key-scenarios-to-monitor/apache-yarn-queue-manager.png)

Stránka správce fronty PŘÍZe zobrazuje seznam front na levé straně a procento kapacity, která je jim přiřazena.

![Stránka podrobností Správce fronty PŘÍZe](./media/hdinsight-key-scenarios-to-monitor/yarn-queue-manager-details.png)

Podrobnější pohled na vaše fronty získáte tak, že na řídicím panelu Ambari vyberete ze seznamu na levé straně službu **příze** . Pak v rozevírací nabídce **Rychlé odkazy** vyberte **Správce prostředků uživatelské rozhraní** pod aktivním uzlem.

![Odkazy v nabídce uživatelského rozhraní Správce prostředků](./media/hdinsight-key-scenarios-to-monitor/resource-manager-ui-menu-link.png)

V uživatelském rozhraní Správce prostředků v nabídce na levé straně vyberte **Scheduler** . Zobrazí se seznam front pod *frontami aplikací*. Tady vidíte kapacitu, která se používá pro každou frontu, jak dobře jsou úlohy distribuované mezi nimi a jestli jsou nějaké úlohy omezené na prostředky.

![Nabídka uživatelského rozhraní Apache HAdoop Správce prostředků](./media/hdinsight-key-scenarios-to-monitor/resource-manager-ui-menu.png)

## <a name="storage-throttling"></a>Omezování úložiště

Kritické body výkonu clusteru se můžou vyskytnout na úrovni úložiště. Tento typ kritických míst je nejčastěji způsobený tím, že *blokuje* operace vstupu a výstupu (v/v), ke kterým dochází, když vaše spuštěné úlohy odesílají více vstupně-výstupních operací, než dokáže služba úložiště zvládnout. Toto blokování vytvoří frontu požadavků v/v, které čekají na zpracování až po zpracování aktuální IOs. Bloky jsou způsobené *omezením úložiště*, což není fyzický limit, ale místo omezení služby úložiště pomocí smlouvy o úrovni služeb (SLA). Tento limit zajistí, že nemůžete monopolizovatovat žádného jednoho klienta ani klienta služby. Smlouva SLA omezuje počet IOs za sekundu (IOPS) pro Azure Storage. Podrobnosti najdete v tématu [Azure Storage škálovatelnost a cíle výkonu](https://docs.microsoft.com/azure/storage/storage-scalability-targets).

Pokud používáte Azure Storage, přečtěte si informace o monitorování potíží souvisejících s úložištěm, včetně omezení, najdete v tématu [monitorování, diagnostika a řešení potíží s Microsoft Azure Storage](https://docs.microsoft.com/azure/storage/storage-monitoring-diagnosing-troubleshooting).

Pokud je úložiště záložního clusteru Azure Data Lake Storage (ADLS), je pravděpodobně příčinou omezení šířky pásma. Omezování v tomto případě je možné identifikovat pozorováním chyb omezení v protokolech úloh. Informace o ADLS najdete v části omezování pro příslušnou službu v těchto článcích:

* [Pokyny k ladění výkonu pro Apache Hive ve službě HDInsight a Azure Data Lake Storage](../data-lake-store/data-lake-store-performance-tuning-hive.md)
* [Pokyny k ladění výkonu pro MapReduce ve službě HDInsight a Azure Data Lake Storage](../data-lake-store/data-lake-store-performance-tuning-mapreduce.md)
* [Pokyny k ladění výkonu pro Apache Storm ve službě HDInsight a Azure Data Lake Storage](../data-lake-store/data-lake-store-performance-tuning-storm.md)

## <a name="next-steps"></a>Další kroky

Další informace o řešení potíží a monitorování clusterů najdete na následujících odkazech:

* [Analýza protokolů služby HDInsight](hdinsight-debug-jobs.md)
* [Ladění aplikací pomocí Apache Hadoopch protokolů PŘÍZe](hdinsight-hadoop-access-yarn-app-logs-linux.md)
* [Povolit výpisy haldy pro Apache Hadoop služby v HDInsight se systémem Linux](hdinsight-hadoop-collect-debug-heap-dump-linux.md)
