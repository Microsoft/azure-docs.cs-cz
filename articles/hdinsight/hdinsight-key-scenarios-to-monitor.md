---
title: Monitorování výkonu clusteru – Azure HDInsight
description: Postup monitorování clusteru služby HDInsight pro kapacitu a výkon.
services: hdinsight
author: maxluk
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 09/27/2017
ms.author: maxluk
ms.openlocfilehash: 434b3ecf65aaa5ecea81f5a9773f1bc6e8f6f2be
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/27/2018
ms.locfileid: "43092323"
---
# <a name="monitor-cluster-performance"></a>Monitorování výkonu clusteru

Monitorování stavu a výkonu clusteru služby HDInsight je nezbytná pro zajištění maximálního výkonu a využití prostředků. Monitorování vám také umožňují možného kódování, adresu či chyby konfigurace clusteru.

Následující části popisují, jak optimalizovat cluster načítání, YARN fronty efektivitu a usnadnění přístupu úložiště.

## <a name="cluster-loading"></a>Zatížení clusteru

Clustery Hadoop by měl zajistit rovnováhu mezi, načítají se na uzlech clusteru. Toto vyrovnávání zabraňuje úlohy související se zpracováním omezovaná paměti RAM, procesoru nebo diskové prostředky.

Pokud chcete získat podrobný pohled na uzlech clusteru a jejich načítání, přihlaste se k [webovému uživatelskému rozhraní Ambari](hdinsight-hadoop-manage-ambari.md)a pak vyberte **hostitele** kartu. Vaši hostitelé jsou seřazeny podle jejich plně kvalifikovaných názvů domény. Provozní stav každého hostitele je znázorněná indikátor barevné stavu:

| Barva | Popis |
| --- | --- |
| Červená | Alespoň jeden hlavní komponenty na hostiteli je mimo provoz. Najeďte myší zobrazíte popis tlačítka, seznamy vliv na komponenty. |
| Orange | Komponenta alespoň jeden podřízený server na hostiteli je mimo provoz. Najeďte myší zobrazíte popis tlačítka, seznamy vliv na komponenty. |
| Žlutá | Ambari Server neobdržel prezenční signál z hostitele pro více než tři minuty. |
| Zelená | Normální spuštění stavu. |

Potěší vás také sloupce zobrazující počet jader a množství paměti RAM pro každého hostitele, a průměrné využití disku a zatížení.

![Karta hostitele](./media/hdinsight-key-scenarios-to-monitor/hosts-tab.png)

Vyberte některou z hostitele pro podrobný pohled na součástí, které běží na tomto hostiteli a jejich metriky. Metriky se zobrazují jako volitelnou časová osa využití procesoru, zatížení, využití disku, využití paměti, využití sítě a počtu procesů.

![Podrobnosti hostitele](./media/hdinsight-key-scenarios-to-monitor/host-details.png)

Zobrazit [HDInsight Správa clusterů pomocí webového uživatelského rozhraní Ambari](hdinsight-hadoop-manage-ambari.md) podrobné informace o nastavení výstrah a zobrazení metrik.

## <a name="yarn-queue-configuration"></a>Konfigurace fronty YARN

Hadoop má různé služby fungovat v různých její distribuovanou platformu. YARN (zatím jiné Resource Negotiator) koordinuje těchto služeb, přidělí prostředky clusteru a spravuje přístup k běžné datové sady.

YARN rozdělí dvě odpovědnosti JobTracker, Správa prostředků a plánování a monitorování, úloh na dva procesy démon: Globální správce prostředků a jednotlivým aplikacím ApplicationMaster (dop).

Je správce prostředků *plánovači čisté*a pouze řeší dostupných prostředků mezi všechny konkurující si aplikace. Správce prostředků zajišťuje, že všechny prostředky se vždycky nacházejí v použití, optimalizaci pro různé konstant, jako je smlouvách SLA, kapacity zaručuje a tak dále. ApplicationMaster vyjedná prostředků ze Správce prostředků a pracuje s NodeManager(s) pro spouštění a sledování kontejnery a jejich spotřeby prostředků.

Když několik tenantů sdílí velký cluster, je konkurenční žádosti o prostředky clusteru. CapacityScheduler je modulární plánovače, která pomáhá při sdílení podle jejich zařazování do fronty požadavky prostředků. Podporuje také CapacityScheduler *hierarchické fronty* zajistit, že se prostředky sdílejí mezi dílčí fronty organizace, před fronty jiné aplikace můžou používat bezplatné zdroje.

YARN, umožníte nám přidělení prostředků na tyto fronty a ukazuje, zda jsou přiřazeny všechny dostupné prostředky. Chcete-li zobrazit informace o vašich front, přihlaste se k webovému uživatelskému rozhraní Ambari a pak vyberte **správce fronty YARN** z hlavní nabídky.

![Správce fronty YARN](./media/hdinsight-key-scenarios-to-monitor/yarn-queue-manager.png)

Na stránce Správce fronty YARN zobrazí seznam vašich front na levé straně společně s procento kapacity přiřazené ke každému.

![Stránka podrobností Správce fronty YARN](./media/hdinsight-key-scenarios-to-monitor/yarn-queue-manager-details.png)

Podrobnější pohled na vaše front z Ambari řídicího panelu, vyberte **YARN** služby ze seznamu na levé straně. Pak v oblasti **rychlé odkazy** rozevírací nabídce vyberte **uživatelské rozhraní správce prostředků** pod aktivní uzel.

![Odkaz uživatelského rozhraní správce prostředků nabídky](./media/hdinsight-key-scenarios-to-monitor/resource-manager-ui-menu.png)

V uživatelském rozhraní správce prostředků vyberte **Plánovač** z nabídky na levé straně. Zobrazí seznam vašich front pod *fronty aplikací*. Zde můžete zobrazit kapacitu pro každý z vašich front, jak dobře úlohy, které jsou distribuovány mezi nimi, a určuje, zda některé úlohy jsou s omezenými zdroji.

![Odkaz uživatelského rozhraní správce prostředků nabídky](./media/hdinsight-key-scenarios-to-monitor/resource-manager-ui.png)

## <a name="storage-throttling"></a>Omezení úložiště

Problémové místo výkonu clusteru může dojít, úrovni úložiště. Tento typ kritickým bodem je nejčastěji kvůli *blokování* vstupně výstupní operace (IO), které dojít v případě, že spuštěné úkoly odeslat další vstupně-výstupních operací, než dokáže zpracovat službu storage. Toto blokování vytvoří frontu požadavků na vstupně-výstupních operací čekajících na zpracování až po zpracování aktuální IOs. Bloky jsou z důvodu *omezení úložiště*, což není fyzického limitu, ale spíše omezení vyplývajícího službu storage podle smlouvy o úrovni služeb (SLA). Toto omezení se zajistí, že žádné tenanta nebo jednoho klienta může monopolizovat všechny služby. Smlouva SLA omezuje počet IOs za sekundu (IOPS) pro službu Azure Storage – podrobnosti naleznete v tématu [Azure Storage škálovatelnost a cíle výkonnosti](https://docs.microsoft.com/azure/storage/storage-scalability-targets).

Pokud používáte Azure Storage, informace o sledování problémů souvisejících se úložiště, včetně omezení, najdete v článku [monitorování, Diagnostika a řešení problémů s Microsoft Azure Storage](https://docs.microsoft.com/azure/storage/storage-monitoring-diagnosing-troubleshooting).

Pokud váš cluster záložního úložiště je Azure Data Lake Store (ADLS), vaše omezení je pravděpodobně z důvodu omezení šířky pásma. Omezení šířky pásma v tomto případě může být označen sledování chyb omezení v protokolech úloh. ADLS naleznete v části omezení pro příslušnou službu v těchto článcích:

* [Průvodce laděním výkonu pro Hive ve službě HDInsight a Azure Data Lake Store](../data-lake-store/data-lake-store-performance-tuning-hive.md)
* [Průvodce laděním výkonu pro MapReduce ve službě HDInsight a Azure Data Lake Store](../data-lake-store/data-lake-store-performance-tuning-mapreduce.md)
* [Průvodce laděním výkonu pro Storm ve službě HDInsight a Azure Data Lake Store](../data-lake-store/data-lake-store-performance-tuning-storm.md)

## <a name="next-steps"></a>Další postup

Získáte další informace o řešení potíží a monitorování clusterů prostřednictvím následujících odkazů:

* [Analýza protokolů služby HDInsight](hdinsight-debug-jobs.md)
* [Ladění aplikací pomocí protokolů YARN](hdinsight-hadoop-access-yarn-app-logs-linux.md)
* [Povolení výpisů paměti haldy pro služby Hadoop v HDInsight se systémem Linux](hdinsight-hadoop-collect-debug-heap-dump-linux.md)
