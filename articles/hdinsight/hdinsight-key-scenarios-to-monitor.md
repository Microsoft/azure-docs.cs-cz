---
title: Monitorování výkonu clusteru – Azure HDInsight
description: Jak monitorovat stav a výkon Apache Hadoopch clusterů ve službě Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 03/09/2020
ms.openlocfilehash: 78ff8adcc2b50f89daa37112b14d219233559dab
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86075566"
---
# <a name="monitor-cluster-performance-in-azure-hdinsight"></a>Monitorování výkonu clusteru v Azure HDInsight

Monitorování stavu a výkonu clusteru HDInsight je nezbytné pro zachování optimálního výkonu a využití prostředků. Monitorování vám taky může pomáhat při detekci a adresování chyb konfigurace clusteru a potíží s uživatelským kódem.

Následující části popisují, jak monitorovat a optimalizovat zatížení clusterů, Apache Hadoopch PŘÍZových front a zjišťovat problémy s omezením úložiště.

## <a name="monitor-cluster-load"></a>Monitorování zatížení clusteru

Clustery Hadoop můžou poskytovat optimální výkon, když je zatížení clusteru rovnoměrně rozdělené napříč všemi uzly. To umožňuje spouštět úlohy zpracování bez omezení paměti RAM, procesoru nebo diskových prostředků na jednotlivých uzlech.

Pokud se chcete podívat na nejvyšší úroveň uzlů clusteru a jejich načítání, přihlaste se k [webovému uživatelskému rozhraní Ambari](hdinsight-hadoop-manage-ambari.md)a pak vyberte kartu **hostitelé** . Vaši hostitelé jsou uvedeni podle jejich plně kvalifikovaného názvu domény. Stav provozu každého hostitele se zobrazuje pomocí barevného indikátoru stavu:

| Color | Description |
| --- | --- |
| Červený | Nejméně jedna hlavní součást v hostiteli je mimo provoz. Když najedete myší, zobrazí se popis, který obsahuje seznam ovlivněných součástí. |
| Oranžový | Nejméně jedna sekundární součást v hostiteli je mimo provoz. Když najedete myší, zobrazí se popis, který obsahuje seznam ovlivněných součástí. |
| Žlutý | Server Ambari nedostal od hostitele prezenční signál po dobu delší než 3 minuty. |
| Green | Normální stav spuštění. |

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

Kritické body výkonu clusteru se můžou vyskytnout na úrovni úložiště. Tento typ kritických míst je nejčastěji kvůli *blokujícím* vstupně-výstupním operacím (v/v), ke kterým dochází, když spuštěné úlohy odesílají více vstupně-výstupních operací, než dokáže služba úložiště zvládnout. Toto blokování vytvoří frontu požadavků v/v, které čekají na zpracování až po zpracování aktuální IOs. Bloky jsou z důvodu *omezování úložiště*, což není fyzický limit, ale místo omezení služby úložiště pomocí smlouvy SLA (Service level agreement). Tento limit zajistí, že nemůžete monopolizovatovat žádného jednoho klienta ani klienta služby. Smlouva SLA omezuje počet IOs za sekundu (IOPS) pro Azure Storage. Podrobnosti najdete v tématu [škálovatelnost a cíle výkonnosti pro účty úložiště úrovně Standard](../storage/common/scalability-targets-standard-account.md).

Pokud používáte Azure Storage, přečtěte si informace o monitorování potíží souvisejících s úložištěm, včetně omezení, najdete v tématu [monitorování, diagnostika a řešení potíží s Microsoft Azure Storage](https://docs.microsoft.com/azure/storage/storage-monitoring-diagnosing-troubleshooting).

Pokud je úložiště záložního clusteru Azure Data Lake Storage (ADLS), je pravděpodobně příčinou omezení šířky pásma. Omezování v tomto případě je možné identifikovat pozorováním chyb omezení v protokolech úloh. Informace o ADLS najdete v části omezování pro příslušnou službu v těchto článcích:

* [Pokyny k ladění výkonu pro Apache Hive ve službě HDInsight a Azure Data Lake Storage](../data-lake-store/data-lake-store-performance-tuning-hive.md)
* [Pokyny k ladění výkonu pro MapReduce ve službě HDInsight a Azure Data Lake Storage](../data-lake-store/data-lake-store-performance-tuning-mapreduce.md)
* [Pokyny k ladění výkonu pro Apache Storm ve službě HDInsight a Azure Data Lake Storage](../data-lake-store/data-lake-store-performance-tuning-storm.md)

## <a name="troubleshoot-sluggish-node-performance"></a>Řešení potíží s výkonem uzlu pomalá

V některých případech může ke zpomalení dojít kvůli nedostatku místa na disku v clusteru. Prozkoumejte pomocí těchto kroků:

1. Pomocí [příkazu SSH](./hdinsight-hadoop-linux-use-ssh-unix.md) se připojte ke každému uzlu.

1. Spusťte jeden z následujících příkazů a ověřte využití disku:

    ```bash
    df -h
    du -h --max-depth=1 / | sort -h
    ```

1. Zkontrolujte výstup a zkontrolujte přítomnost velkých souborů ve `mnt` složce nebo v jiných složkách. Obvykle složky a `usercache` `appcache` (mnt/Resource/Hadoop/nitě/Local/usercache/podregistr/APPCACHE/) obsahují velké soubory.

1. Pokud jsou velké soubory, je možné, že aktuální úloha způsobuje nárůst souboru nebo neúspěšná předchozí úloha mohla přispět k tomuto problému. Pokud chcete zjistit, jestli toto chování způsobuje aktuální úloha, spusťte následující příkaz: .

    ```bash
    sudo du -h --max-depth=1 /mnt/resource/hadoop/yarn/local/usercache/hive/appcache/
    ```

1. Pokud tento příkaz indikuje konkrétní úlohu, můžete se rozhodnout ukončit úlohu pomocí příkazu, který se podobá následujícímu:

    ```bash
    yarn application -kill -applicationId <application_id>
    ```

    Nahraďte `application_id` ID aplikace. Pokud se neoznačí žádné konkrétní úlohy, přejděte k následujícímu kroku.

1. Po dokončení příkazu, nebo pokud nejsou zadány žádné konkrétní úlohy, odstraňte velké soubory, které jste identifikovali, spuštěním příkazu, který se podobá následujícímu:

    ```bash
    rm -rf filecache usercache
    ```

Další informace o potížích s místem na disku najdete v tématu [nedostatek místa na disku](./hadoop/hdinsight-troubleshoot-out-disk-space.md).

> [!NOTE]  
> Pokud máte velké soubory, které chcete zachovat, ale přispějete k potížím s nedostatkem místa na disku, je třeba škálovat cluster HDInsight a restartovat vaše služby. Po dokončení tohoto postupu a čekání na několik minut si všimněte, že je úložiště uvolněno a že je obnovený obvyklý výkon uzlu.

## <a name="next-steps"></a>Další kroky

Další informace o řešení potíží a monitorování clusterů najdete na následujících odkazech:

* [Analýza protokolů služby HDInsight](hdinsight-debug-jobs.md)
* [Ladění aplikací pomocí Apache Hadoopch protokolů PŘÍZe](hdinsight-hadoop-access-yarn-app-logs-linux.md)
* [Povolit výpisy haldy pro Apache Hadoop služby v HDInsight se systémem Linux](hdinsight-hadoop-collect-debug-heap-dump-linux.md)
