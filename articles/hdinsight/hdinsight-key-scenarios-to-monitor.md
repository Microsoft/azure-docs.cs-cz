---
title: Sledování výkonu clusteru – Azure HDInsight
description: Jak sledovat stav a výkon clusterů Apache Hadoop v Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 03/09/2020
ms.openlocfilehash: 75ac5a7fc352f877573d79a004d8da761c6f1cef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79082876"
---
# <a name="monitor-cluster-performance-in-azure-hdinsight"></a>Sledování výkonu clusteru v Azure HDInsight

Sledování stavu a výkonu clusteru HDInsight je nezbytné pro zachování optimálního výkonu a využití prostředků. Monitorování vám také může pomoci zjistit a vyřešit chyby konfigurace clusteru a problémy s uživatelským kódem.

Následující části popisují, jak sledovat a optimalizovat zatížení clusterů, front YARN Apache Hadoop a zjistit problémy omezení úložiště.

## <a name="monitor-cluster-load"></a>Monitorování zatížení clusteru

Hadoop clustery může poskytovat optimální výkon při zatížení clusteru je rovnoměrně rozložena na všech uzlech. To umožňuje úlohy zpracování spustit bez omezení ram, procesoru nebo diskových prostředků na jednotlivých uzlech.

Chcete-li získat pohled na vysoké úrovni na uzly clusteru a jejich načítání, přihlaste se k [webovému uživatelskému uživatelskému uživatelskému nastavení Ambari](hdinsight-hadoop-manage-ambari.md)a vyberte kartu **Hosts** . Vaši hostitelé jsou uvedeni podle plně kvalifikovaných názvů domén. Provozní stav každého hostitele je zobrazen barevným ukazatelem stavu:

| Barvy | Popis |
| --- | --- |
| Červený | Nejméně jedna hlavní součást na hostiteli je vypnutá. Najeďte přes, chcete-li zobrazit popis, který obsahuje seznam ovlivněných součástí. |
| Orange | Nejméně jedna sekundární součást na hostiteli je vypnutá. Najeďte přes, chcete-li zobrazit popis, který obsahuje seznam ovlivněných součástí. |
| Žlutý | Server Ambari neobdržel prezenční signál od hostitele více než 3 minuty. |
| Green | Normální provozní stav. |

Zobrazí se také sloupce zobrazující počet jader a velikost paměti RAM pro každého hostitele a průměr využití disku a zatížení.

![Přehled karet Apache Ambari hostí](./media/hdinsight-key-scenarios-to-monitor/apache-ambari-hosts-tab.png)

Vyberte libovolný z názvů hostitelů pro podrobný pohled na součásti spuštěné na tomto hostiteli a jejich metriky. Metriky jsou zobrazeny jako volitelná časová osa využití procesoru, zatížení, využití disku, využití paměti, využití sítě a počtu procesů.

![Podrobnosti o hostiteli Apache Ambari](./media/hdinsight-key-scenarios-to-monitor/apache-ambari-host-details.png)

Podrobnosti o nastavení výstrah a zobrazení metrik y najdete [v tématu Správa clusterů HDInsight pomocí webového uživatelského rozhraní Apache Ambari.](hdinsight-hadoop-manage-ambari.md)

## <a name="yarn-queue-configuration"></a>Konfigurace fronty YARN

Hadoop má různé služby běžící napříč distribuovanou platformou. YARN (Ještě jiný vyjednavač prostředků) koordinuje tyto služby a přiděluje prostředky clusteru, aby bylo zajištěno, že jakékoli zatížení je rovnoměrně rozloženo v rámci clusteru.

YARN rozděluje dvě povinnosti JobTracker, řízení zdrojů a plánování/monitorování úloh na dva daemony: globálního správce zdrojů a applicationmaster (AM) pro aplikaci.

Správce prostředků je *čistý plánovač*a pouze rozhoduje dostupné prostředky mezi všemi konkurenčními aplikacemi. Správce prostředků zajišťuje, že všechny prostředky jsou vždy používány, optimalizace pro různé konstanty, jako jsou sla, záruky kapacity a tak dále. ApplicationMaster vyjedná prostředky ze Správce prostředků a spolupracuje s NodeManager (y) ke spuštění a monitorování kontejnerů a jejich spotřeby prostředků.

Když více klientů sdílí velký cluster, existuje konkurence pro prostředky clusteru. CapacityScheduler je připojitelný plánovač, který pomáhá při sdílení prostředků zařazováním požadavků do fronty. CapacityScheduler také podporuje *hierarchické fronty,* aby bylo zajištěno, že prostředky jsou sdíleny mezi podfronty organizace, než fronty jiných aplikací mohou používat volné prostředky.

YARN nám umožňuje přidělit prostředky do těchto front a ukazuje, zda jsou přiřazeny všechny dostupné prostředky. Chcete-li zobrazit informace o frontách, přihlaste se k webovému uživatelskému uživatelskému uživatelskému panelu Ambari a v horní nabídce vyberte **Správce front yarnů.**

![Správce front YARN Apache Ambari](./media/hdinsight-key-scenarios-to-monitor/apache-yarn-queue-manager.png)

Stránka Správce front YARN zobrazuje seznam front vlevo spolu s procentem kapacity přiřazenými každému z nich.

![Stránka podrobností správce front YARN](./media/hdinsight-key-scenarios-to-monitor/yarn-queue-manager-details.png)

Podrobnější pohled na fronty z řídicího panelu Ambari vyberte službu **YARN** ze seznamu vlevo. Potom v rozevírací nabídce **Rychlé odkazy** vyberte pod aktivním uzem **vyberte ui Správce prostředků.**

![Odkazy na nabídky nabídky ui Správce prostředků](./media/hdinsight-key-scenarios-to-monitor/resource-manager-ui-menu-link.png)

V ui Správce prostředků vyberte **Plánovač** z levé nabídky. Seznam front se zobrazí pod *frontami aplikací*. Zde můžete zobrazit kapacitu použitou pro každou frontu, jak dobře jsou mezi nimi rozděleny úlohy a zda jsou některé úlohy omezeny na zdroje.

![Nabídka ui správce zdrojů Apache HAdoop](./media/hdinsight-key-scenarios-to-monitor/resource-manager-ui-menu.png)

## <a name="storage-throttling"></a>Omezení úložiště

Problémové místo výkonu clusteru může dojít na úrovni úložiště. Tento typ kritického místa je nejčastěji z důvodu *blokování* operací vstupu a výstupu (IO), ke kterým dochází, když spuštěné úlohy odeslat více vstupně-výstupních operací, než služba úložiště může zpracovat. Toto blokování vytvoří frontu vi požadavků čekajících na zpracování až po zpracování aktuálnívimes. Bloky jsou z důvodu *omezení úložiště*, což není fyzické omezení, ale spíše omezení uložené službou úložiště smlouvou o úrovni služby (SLA). Toto omezení zajišťuje, že žádný jeden klient nebo klient může monopolizovat službu. SLA omezuje počet vod za sekundu (IOPS) pro Azure Storage – podrobnosti najdete v [tématu škálovatelnost a cíle výkonu pro účty standardního úložiště](../storage/common/scalability-targets-standard-account.md).

Pokud používáte Azure Storage, informace o monitorování problémů souvisejících s úložištěm, včetně omezení, najdete v tématu [Monitorování, diagnostika a řešení potíží s úložištěm Microsoft Azure](https://docs.microsoft.com/azure/storage/storage-monitoring-diagnosing-troubleshooting).

Pokud je úložiště zálohování vašeho clusteru Azure Data Lake Storage (ADLS), omezení je s největší pravděpodobností z důvodu omezení šířky pásma. Omezení v tomto případě lze identifikovat sledováním chyb omezení v protokolech úloh. Pro ADLS naleznete v části omezení pro příslušnou službu v těchto článcích:

* [Pokyny pro ladění výkonu pro Apache Hive v HDInsightu a Azure Data Lake Storage](../data-lake-store/data-lake-store-performance-tuning-hive.md)
* [Pokyny k ladění výkonu pro MapReduce na HDInsight a Azure Data Lake Storage](../data-lake-store/data-lake-store-performance-tuning-mapreduce.md)
* [Pokyny k ladění výkonu pro Apache Storm v HDInsightu a Azure Data Lake Storage](../data-lake-store/data-lake-store-performance-tuning-storm.md)

## <a name="troubleshoot-sluggish-node-performance"></a>Poradce při potížích s pomalým výkonem uzlu

V některých případech může dojít k pomalálost z důvodu nedostatku místa na disku v clusteru. Prozkoumejte pomocí těchto kroků:

1. Pomocí [příkazu ssh](./hdinsight-hadoop-linux-use-ssh-unix.md) se připojte ke každému z uzlů.

1. Zkontrolujte využití disku spuštěním jednoho z následujících příkazů:

    ```bash
    df -h
    du -h --max-depth=1 / | sort -h
    ```

1. Zkontrolujte výstup a zkontrolujte přítomnost všech velkých `mnt` souborů ve složce nebo jiných složkách. Složky `usercache`, a `appcache` (mnt/resource/hadoop/yarn/local/usercache/hive/appcache/) obvykle obsahují velké soubory.

1. Pokud existují velké soubory, buď aktuální úloha je příčinou růstu souboru nebo selhání předchozí úlohy může přispět k tomuto problému. Chcete-li zkontrolovat, zda je toto chování způsobeno aktuální úlohou, spusťte následující příkaz:

    ```bash
    sudo du -h --max-depth=1 /mnt/resource/hadoop/yarn/local/usercache/hive/appcache/
    ```

1. Pokud tento příkaz označuje určitou úlohu, můžete úlohu ukončit pomocí příkazu, který se podobá následujícímu:

    ```bash
    yarn application -kill -applicationId <application_id>
    ```

    Nahraďte `application_id` id aplikace. Pokud nejsou uvedeny žádné konkrétní úlohy, přejděte k dalšímu kroku.

1. Po dokončení výše uvedeného příkazu nebo pokud nejsou označeny žádné konkrétní úlohy, odstraňte velké soubory, které jste identifikovali spuštěním příkazu, který se podobá následujícímu:

    ```bash
    rm -rf filecache usercache
    ```

Další informace týkající se problémů s místem na disku naleznete [v tématu Nedostatek místa na disku](./hadoop/hdinsight-troubleshoot-out-disk-space.md).

> [!NOTE]  
> Pokud máte velké soubory, které chcete zachovat, ale přispívají k problému s nízkým prostorem na disku, je nutné vertikálně navýšit kapacitu clusteru HDInsight a restartovat služby. Po dokončení tohoto postupu a počkejte několik minut, zjistíte, že úložiště je uvolněna a uzel je obnoven obvyklý výkon.

## <a name="next-steps"></a>Další kroky

Další informace o řešení potíží a monitorování clusterů naleznete na následujících odkazech:

* [Analýza protokolů služby HDInsight](hdinsight-debug-jobs.md)
* [Ladění aplikací pomocí protokolů Apache Hadoop YARN](hdinsight-hadoop-access-yarn-app-logs-linux.md)
* [Povolení výpisů hald pro služby Apache Hadoop na Linuxu založeném na HDInsightu](hdinsight-hadoop-collect-debug-heap-dump-linux.md)
