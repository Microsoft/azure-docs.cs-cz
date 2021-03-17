---
title: Migrace clusteru HBA do nové verze – Azure HDInsight
description: Postup Migrace clusterů Apache HBA do novější verze ve službě Azure HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 01/02/2020
ms.openlocfilehash: 24a0c09ba78c668dab017ec80adda19f59d89a4f
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2021
ms.locfileid: "98942977"
---
# <a name="migrate-an-apache-hbase-cluster-to-a-new-version"></a>Migrace clusteru Apache HBA na novou verzi

Tento článek popisuje kroky potřebné k aktualizaci clusteru Apache HBA v Azure HDInsight na novější verzi.

Výpadek během upgradu by měl být v řádu minut minimální. Toto výpadky je způsobené kroky pro vyprázdnění všech dat v paměti a pak čas konfigurace a restartování služeb v novém clusteru. Vaše výsledky se budou lišit v závislosti na počtu uzlů, množství dat a dalších proměnných.

## <a name="review-apache-hbase-compatibility"></a>Kontrola kompatibility Apache HBA

Před upgradem serverů Apache HBA ověřte, zda jsou verze adaptérů HBA na zdrojovém a cílovém clusteru kompatibilní. Další informace najdete v tématu [Apache Hadoop součásti a verze, které jsou k dispozici v HDInsight](../hdinsight-component-versioning.md).

> [!NOTE]  
> Důrazně doporučujeme, abyste si přečtěte matrici kompatibility verzí v [knize HBA](https://hbase.apache.org/book.html#upgrading). V poznámkách k verzi HBA by se měly popsat případné nekompatibility.

Tady je příklad matice kompatibility verzí. Y značí kompatibilitu a N označuje potenciální nekompatibilitu:

| Typ kompatibility | Hlavní verze| Podverze | Patch |
| --- | --- | --- | --- |
| Client-Server kompatibilita drátů | N | Y | Y |
| Server-Server kompatibilita | N | Y | Y |
| Kompatibilita formátu souborů | N | Y | Y |
| Kompatibilita rozhraní API klienta | N | Y | Y |
| Binární kompatibilita klienta | N | N | Y |
| **Omezená Kompatibilita rozhraní API na straně serveru** |  |  |  |
| Stable | N | Y | Y |
| Vyvíjejí | N | N | Y |
| Nestabilní | N | N | N |
| Kompatibilita závislostí | N | Y | Y |
| Provozní kompatibilita | N | N | Y |

## <a name="upgrade-with-same-apache-hbase-major-version"></a>Upgradovat se stejnými hlavními verzemi Apache HBA

Pokud chcete upgradovat cluster Apache HBA v Azure HDInsight, proveďte následující kroky:

1. Ujistěte se, že je aplikace kompatibilní s novou verzí, jak je znázorněno v matrici kompatibility HBA a v poznámkách k verzi. Otestujte aplikaci v clusteru s cílovou verzí HDInsight a HBA.

1. [Nastavte nový cílový cluster HDInsight](../hdinsight-hadoop-provision-linux-clusters.md) pomocí stejného účtu úložiště, ale s jiným názvem kontejneru:

   ![Použijte stejný účet úložiště, ale vytvořte jiný kontejner.](./media/apache-hbase-migrate-new-version/same-storage-different-container.png)

1. Vyprázdněte svůj zdrojový cluster HBA, což je cluster, který upgradujete. HBA zapisuje příchozí data do úložiště v paměti, které se označuje jako _setSize paměťového úložiště_. Jakmile setSize paměťového úložiště dosáhne určité velikosti, HBA ji vyprázdní na disk pro dlouhodobé uložení v účtu úložiště clusteru. Při odstraňování starého clusteru se memstores recykluje, potenciálně ztratí data. Chcete-li ručně vyprázdnit setSize paměťového úložiště pro každou tabulku na disk, spusťte následující skript. Nejnovější verzi tohoto skriptu je na [GitHubu](https://raw.githubusercontent.com/Azure/hbase-utils/master/scripts/flush_all_tables.sh)Azure.

    ```bash
    #!/bin/bash
    
    #-------------------------------------------------------------------------------#
    # SCRIPT TO FLUSH ALL HBASE TABLES.
    #-------------------------------------------------------------------------------#
    
    LIST_OF_TABLES=/tmp/tables.txt
    HBASE_SCRIPT=/tmp/hbase_script.txt
    TARGET_HOST=$1
    
    usage ()
    {
        if [[ "$1" == "-h" ]] || [[ "$1" == "--help" ]]
        then
            cat << ...
    
    Usage: 
    
    $0 [hostname]
    
    Providing hostname is optional and not required when the script is executed within HDInsight cluster with access to 'hbase shell'.
    
    However hostname should be provided when executing the script as a script-action from HDInsight portal.
    
    For Example:
    
        1.  Executing script inside HDInsight cluster (where 'hbase shell' is 
            accessible):
    
            $0 
    
            [No need to provide hostname]
    
        2.  Executing script from HDinsight Azure portal:
    
            Provide Script URL.
    
            Provide hostname as a parameter (i.e. hn0, hn1, hn2.. or wn2 etc.).
    ...
            exit
        fi
    }
    
    validate_machine ()
    {
        THIS_HOST=`hostname`
    
        if [[ ! -z "$TARGET_HOST" ]] && [[ $THIS_HOST  != $TARGET_HOST* ]]
        then
            echo "[INFO] This machine '$THIS_HOST' is not the right machine ($TARGET_HOST) to execute the script."
            exit 0
        fi
    }
    
    get_tables_list ()
    {
    hbase shell << ... > $LIST_OF_TABLES 2> /dev/null
        list
        exit
    ...
    }
    
    add_table_for_flush ()
    {
        TABLE_NAME=$1
        echo "[INFO] Adding table '$TABLE_NAME' to flush list..."
        cat << ... >> $HBASE_SCRIPT
            flush '$TABLE_NAME'
    ...
    }
    
    clean_up ()
    {
        rm -f $LIST_OF_TABLES
        rm -f $HBASE_SCRIPT
    }
    
    ########
    # MAIN #
    ########
    
    usage $1
    
    validate_machine
    
    clean_up
    
    get_tables_list
    
    START=false
    
    while read LINE 
    do 
        if [[ $LINE == TABLE ]] 
        then
            START=true
            continue
        elif [[ $LINE == *row*in*seconds ]]
        then
            break
        elif [[ $START == true ]]
        then
            add_table_for_flush $LINE
        fi
    
    done < $LIST_OF_TABLES
    
    cat $HBASE_SCRIPT
    
    hbase shell $HBASE_SCRIPT << ... 2> /dev/null
    exit
    ...
    
    ```

1. Zastavte ingestování starému clusteru HBA.

1. Pokud chcete mít jistotu, že všechna poslední data v setSize paměťového úložiště jsou vyprázdněná, spusťte předchozí skript znovu.

1. Přihlaste se k [Apache Ambari](https://ambari.apache.org/) v původním clusteru ( `https://OLDCLUSTERNAME.azurehdidnsight.net` ) a zastavte služby HBA. Po zobrazení výzvy k potvrzení, že chcete zastavit služby, zaškrtněte políčko pro zapnutí režimu údržby pro adaptéry HBA. Další informace o připojení a používání Ambari najdete v tématu [Správa clusterů HDInsight pomocí webového uživatelského rozhraní Ambari](../hdinsight-hadoop-manage-ambari.md).

    ![V Ambari klikněte na služby > HBA > zastavit v části akce služby](./media/apache-hbase-migrate-new-version/stop-hbase-services1.png)

    ![Zaškrtněte políčko Zapnout režim údržby pro adaptéry HBA a pak potvrďte](./media/apache-hbase-migrate-new-version/turn-on-maintenance-mode.png)

1. Pokud nepoužíváte clustery HBA s funkcí Vylepšené zápisy, přeskočte tento krok. Je potřeba jenom pro clustery clusterů s funkcí rozšířené zápisy.

   Zazálohujte WAL do adresáře HDFS tak, že spustíte níže uvedené příkazy z relace SSH na libovolném uzlu Zookeeper nebo uzlech pracovního procesu původního clusteru.
   
   ```bash
   hdfs dfs -mkdir /hbase-wal-backup**
   hdfs dfs -cp hdfs://mycluster/hbasewal /hbase-wal-backup**
   ```
    
1. Přihlaste se k Ambari na novém clusteru HDInsight. Změňte `fs.defaultFS` Nastavení HDFS tak, aby odkazovalo na název kontejneru používaného původním clusterem. Toto nastavení je uvedené v části **HDFS > config > advanced > Advanced Core-site**.

   ![V Ambari klikněte na služby > HDFS > konfigurace > Upřesnit.](./media/apache-hbase-migrate-new-version/hdfs-advanced-settings.png)

   ![V Ambari změňte název kontejneru.](./media/apache-hbase-migrate-new-version/change-container-name.png)

1. Pokud nepoužíváte clustery HBA s funkcí Vylepšené zápisy, přeskočte tento krok. Je potřeba jenom pro clustery clusterů s funkcí rozšířené zápisy.

   Změňte `hbase.rootdir` cestu tak, aby odkazovala na kontejner původního clusteru.

   ![V Ambari změňte název kontejneru pro adaptéry HBA RootDir](./media/apache-hbase-migrate-new-version/change-container-name-for-hbase-rootdir.png)
    
1. Pokud nepoužíváte clustery HBA s funkcí Vylepšené zápisy, přeskočte tento krok. Je potřeba jenom pro HBA clustery s funkcí rozšířené zápisy a jenom v případech, kdy byl původní cluster clusterem HBA s funkcí rozšířené zápisu.

   Vyčistěte data Zookeeper a WAL FS pro tento nový cluster. Na libovolném uzlu Zookeeper nebo uzlech pracovních procesů vydejte následující příkazy:

   ```bash
   hbase zkcli
   rmr /hbase-unsecure
   quit

   hdfs dfs -rm -r hdfs://mycluster/hbasewal**
   ```

1. Pokud nepoužíváte clustery HBA s funkcí Vylepšené zápisy, přeskočte tento krok. Je potřeba jenom pro clustery clusterů s funkcí rozšířené zápisy.
   
   Obnovte WAL dir do nového clusteru HDFS z relace SSH na libovolném uzlu Zookeeper nebo pracovních uzlech nového clusteru.
   
   ```bash
   hdfs dfs -cp /hbase-wal-backup/hbasewal hdfs://mycluster/**
   ```
   
1. Pokud upgradujete HDInsight 3,6 na 4,0, postupujte podle následujících kroků, jinak přejděte ke kroku 13:

    1. V Ambari restartujte všechny požadované služby, a to tak, že vyberete **služby**  >  **restartovat všechny požadované**.
    1. Zastavte službu HBA.
    1. Pomocí SSH na uzel Zookeeper a spuštěním příkazu [zkCli](https://github.com/go-zkcli/zkcli) `rmr /hbase-unsecure` odeberte z Zookeeper kořenový znode HBA.
    1. Restartujte HBA.

1. Pokud upgradujete na jinou verzi HDInsight kromě 4,0, postupujte následovně:
    1. Uložte provedené změny.
    1. Restartujte všechny požadované služby, které jsou označeny nástrojem Ambari.

1. Nasměrujte svoji aplikaci na nový cluster.

    > [!NOTE]  
    > Statická služba DNS pro vaši aplikaci se při upgradu změní. Místo hardwarového kódování tohoto DNS můžete nakonfigurovat CNAME v nastavení DNS názvu domény, které odkazuje na název clusteru. Další možností je použít pro vaši aplikaci konfigurační soubor, který můžete aktualizovat bez opětovného nasazení.

1. Spusťte ingestování, abyste viděli, jestli všechno funguje podle očekávání.

1. Pokud je nový cluster uspokojivý, odstraňte původní cluster.

## <a name="next-steps"></a>Další kroky

Další informace o [Apache HBA](https://hbase.apache.org/) a upgradu clusterů HDInsight najdete v následujících článcích:

* [Upgrade clusteru HDInsight na novější verzi](../hdinsight-upgrade-cluster.md)
* [Monitorování a Správa Azure HDInsight pomocí webového uživatelského rozhraní Apache Ambari](../hdinsight-hadoop-manage-ambari.md)
* [Apache Hadoop součásti a verze](../hdinsight-component-versioning.md)
* [Optimalizace Apache HBase](../optimize-hbase-ambari.md)
