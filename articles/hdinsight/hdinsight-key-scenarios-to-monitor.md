---
title: Sledování výkonu clusteru – Azure HDInsight | Microsoft Docs
description: Postup sledování clusteru služby HDInsight pro kapacitu a výkon.
services: hdinsight
documentationcenter: ''
tags: azure-portal
author: maxluk
manager: jhubbard
editor: cgronlun
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.date: 09/27/2017
ms.author: maxluk
ms.openlocfilehash: 9bf49631da58de86ffa1881bca976cab86677805
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="monitor-cluster-performance"></a>Monitorování výkonu clusteru

Monitorování stavu a výkonu clusteru služby HDInsight je nezbytné pro údržbu maximální výkon a využití prostředků. Monitorování také vám může pomoct adresu možné kódování či chyby konfigurace clusteru.

Následující části popisují, jak optimalizovat cluster načítání, YARN fronty efektivitu a usnadnění úložiště.

## <a name="cluster-loading"></a>Načítání clusteru

Clustery Hadoop měli vyvážit načítání mezi uzly clusteru. Toto vyrovnávání zabrání zpracování úlohy se omezené paměti RAM, procesoru nebo diskové prostředky.

Pokud chcete získat podrobný pohled na uzly clusteru a jejich načítání, přihlaste se k [webové uživatelské rozhraní Ambari](hdinsight-hadoop-manage-ambari.md), vyberte **hostitele** kartě. Hostitelů jsou uvedeny podle jejich plně kvalifikovaných názvů domény. Operační stav každého hostitele je ukazují na ukazatel barevnou stavu:

| Barva | Popis |
| --- | --- |
| Červená | Alespoň jedna součást hlavní na hostiteli je mimo provoz. Najeďte zobrazíte popis tlačítka, seznamy vliv součásti. |
| Orange | Alespoň jeden podřízený součást na hostiteli je mimo provoz. Najeďte zobrazíte popis tlačítka, seznamy vliv součásti. |
| Žlutá | Ambari Server neobdržel prezenční signál z hostitele pro více než tři minuty. |
| Zelená | Normální spuštěném stavu. |

Uvidíte taky sloupce zobrazující počet jader a velikost paměti RAM pro každého hostitele, a průměrné využití disku a zatížení.

![Karta hostitele](./media/hdinsight-key-scenarios-to-monitor/hosts-tab.png)

Vyberte některé z názvy hostitelů pro podrobný pohled komponent spuštěných na tomto hostiteli a jejich metriky. Metriky se zobrazí jako volitelný časová osa využití procesoru, zatížení, využití disku, využití paměti, využití sítě a čísla procesů.

![Podrobnosti o hostiteli](./media/hdinsight-key-scenarios-to-monitor/host-details.png)

V tématu [Správa clusterů HDInsight pomocí webového uživatelského rozhraní Ambari](hdinsight-hadoop-manage-ambari.md) podrobné informace o nastavení výstrah a zobrazování metrik.

## <a name="yarn-queue-configuration"></a>YARN fronty konfigurace

Hadoop má různých služeb spuštěných v jeho distribuované platforma. YARN (ještě jiný prostředek Vyjednavač) koordinuje těchto služeb, přiděluje prostředky clusteru a spravuje přístup k běžné datové sady.

YARN rozděluje dvě odpovědnosti JobTracker, správy prostředků a plánovaná nebo monitorování, úlohy do dvou démoni: globální ResourceManager a jednotlivých aplikací ApplicationMaster (dop.).

Je ResourceManager *čistý Plánovač*a výhradně řeší dostupné prostředky mezi všechny konkurující si aplikace. ResourceManager zajistí, že všechny prostředky se vždycky nacházejí v použití, optimalizaci pro různé konstanty například SLA, kapacity zaručuje a tak dále. ApplicationMaster vyjedná prostředky z ResourceManager a pracuje s NodeManager(s) ke spouštění a monitorování kontejnery a jejich spotřeba prostředků.

Pokud více klientů sdílet velké clusteru, je to soutěž o zdroje do clusteru. CapacityScheduler je modulární plánovače, který pomáhá v prostředku sdílení podle queueing požadavků. Podporuje také CapacityScheduler *hierarchické fronty* zajistit, že prostředky jsou sdílené mezi dílčí fronty organizace, než front jiné aplikace mají povoleno používání uvolnění prostředků.

YARN umožňuje přidělení prostředků na tyto fronty a dozvíte se, zda všechny dostupné prostředky jsou přiřazeny. Chcete-li zobrazit informace o vaší front, přihlaste se k webové uživatelské rozhraní Ambari a poté vyberte **správce front YARN** v hlavní nabídce.

![Správce front YARN](./media/hdinsight-key-scenarios-to-monitor/yarn-queue-manager.png)

Správce front YARN stránka zobrazuje seznam vaší front na levé straně, společně s procento kapacity, které jsou přiřazené ke každému.

![Stránka Podrobnosti YARN správce front](./media/hdinsight-key-scenarios-to-monitor/yarn-queue-manager-details.png)

Pro podrobnější pohled na vaše fronty, na řídicím panelu Ambari, vyberte **YARN** služby ze seznamu na levé straně. Poté ve sloupci **rychlé odkazy** rozevírací nabídce vyberte **uživatelského rozhraní ResourceManager** pod aktivní uzel.

![Odkaz uživatelského rozhraní ResourceManager nabídky](./media/hdinsight-key-scenarios-to-monitor/resource-manager-ui-menu.png)

V uživatelském rozhraní ResourceManager vyberte **Plánovač** z nabídky na levé straně. Zobrazí se seznam vaší front pod *fronty aplikací*. Zde můžete zobrazit kapacitu použít pro každý vaší front, jak dobře jsou úlohy distribuovat mezi nimi, a jestli některé úlohy jsou omezené prostředků.

![Odkaz uživatelského rozhraní ResourceManager nabídky](./media/hdinsight-key-scenarios-to-monitor/resource-manager-ui.png)

## <a name="storage-throttling"></a>Omezení úložiště

Přetížení clusteru může dojít, na úrovni úložiště. Tento typ nízkého je nejčastěji kvůli *blokování* vstupně-výstupních operací (IO), které dojít v případě, že spuštěné úlohy odeslání více vstupně-výstupní operace, než může zpracovat služby úložiště. Toto blokování vytvoří frontu vstupně-výstupní operace požadavků čekajících na zpracování až po zpracování aktuální IOs. Na bloky jsou z důvodu *omezení úložiště*, což není fyzický limit, ale spíš omezení vyvolané službou úložiště smlouvu o úrovni služeb (SLA). Toto omezení zajišťuje, že žádná jednoho klienta nebo klienta můžete monopolizovat všechny služby. Smlouva SLA omezuje počet IOs za sekundu (IOPS) pro Azure Storage – podrobnosti naleznete v tématu [a cíle výkonnosti služby Azure Storage Scalability](https://docs.microsoft.com/azure/storage/storage-scalability-targets).

Pokud používáte Azure Storage, informace o monitorování problémy související s úložiště, včetně omezení, najdete v části [monitorování, Diagnostika a řešení Microsoft Azure Storage](https://docs.microsoft.com/azure/storage/storage-monitoring-diagnosing-troubleshooting).

Pokud váš cluster záložnímu úložišti Azure Data Lake ukládání (ADLS), vaše omezení je pravděpodobně z důvodu omezení šířky pásma. Omezení šířky pásma v takovém případě může být identifikován sledování omezení chyby v protokolech úloh. ADLS naleznete v části omezení pro příslušné služby v těchto článcích:

* [Průvodce laděním výkonu pro Hive ve službě HDInsight a Azure Data Lake Store](../data-lake-store/data-lake-store-performance-tuning-hive.md)
* [Průvodce laděním výkonu pro MapReduce ve službě HDInsight a Azure Data Lake Store](../data-lake-store/data-lake-store-performance-tuning-mapreduce.md)
* [Průvodce laděním výkonu pro Storm ve službě HDInsight a Azure Data Lake Store](../data-lake-store/data-lake-store-performance-tuning-storm.md)

## <a name="next-steps"></a>Další postup

Získáte pomocí následujících odkazů pro další informace o řešení potíží a monitorování clusterů:

* [Analýza protokolů služby HDInsight](hdinsight-debug-jobs.md)
* [Ladění aplikací pomocí protokolů YARN](hdinsight-hadoop-access-yarn-app-logs-linux.md)
* [Povolit výpisů paměti haldy pro služby Hadoop v HDInsight se systémem Linux](hdinsight-hadoop-collect-debug-heap-dump-linux.md)
