---
title: Migrace clusteru HBase na novou verzi – Azure HDInsight
description: Jak migrovat clustery Apache HBase do novější verze v Azure HDInsight.
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/02/2020
ms.openlocfilehash: 30cda7a83feddaeb41385252a61d1dc68a881a47
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75646502"
---
# <a name="migrate-an-apache-hbase-cluster-to-a-new-version"></a>Migrace clusteru Apache HBase na novou verzi

Tento článek popisuje kroky potřebné k aktualizaci clusteru Apache HBase v Azure HDInsight na novější verzi.

Prostoje při upgradu by měla být minimální, v řádu minut. Tento prostoje je způsoben kroky vyprázdnění všech dat v paměti, pak čas na konfiguraci a restartování služeb v novém clusteru. Výsledky se budou lišit v závislosti na počtu uzlů, množství dat a dalších proměnných.

## <a name="review-apache-hbase-compatibility"></a>Projděte si kompatibilitu Apache HBase

Před upgradem Apache HBase se ujistěte, že verze HBase na zdrojovém a cílovém clusteru jsou kompatibilní. Další informace naleznete v [tématu Součásti apache hadoop a verze dostupné s HDInsight](../hdinsight-component-versioning.md).

> [!NOTE]  
> Důrazně doporučujeme zkontrolovat matici kompatibility verzí v [knize HBase](https://hbase.apache.org/book.html#upgrading). Všechny narušující nekompatibility by měly být popsány v poznámkách k verzi HBase.

Zde je ukázková matice kompatibility verzí. Y označuje kompatibilitu a N označuje potenciální nekompatibilitu:

| Typ kompatibility | Hlavní verze| Podverze | Patch |
| --- | --- | --- | --- |
| Kompatibilita s vodiči klient-server | Ne | Ano | Ano |
| Kompatibilita mezi serverem a serverem | Ne | Ano | Ano |
| Kompatibilita formátu souboru | Ne | Ano | Ano |
| Kompatibilita rozhraní API klienta | Ne | Ano | Ano |
| Binární kompatibilita klienta | Ne | Ne | Ano |
| **Kompatibilita rozhraní API s omezenou na server** |  |  |  |
| Stable | Ne | Ano | Ano |
| Vyvíjí | Ne | Ne | Ano |
| Nestabilní | Ne | Ne | Ne |
| Kompatibilita závislostí | Ne | Ano | Ano |
| Provozní kompatibilita | Ne | Ne | Ano |

## <a name="upgrade-with-same-apache-hbase-major-version"></a>Upgrade se stejnou hlavní verzí Apache HBase

Pokud chcete upgradovat cluster Apache HBase na Azure HDInsight, proveďte následující kroky:

1. Ujistěte se, že vaše aplikace je kompatibilní s novou verzí, jak je znázorněno v matici kompatibility HBase a poznámky k verzi. Otestujte aplikaci v clusteru s cílovou verzí HDInsight a HBase.

1. [Nastavte nový cílový cluster HDInsight](../hdinsight-hadoop-provision-linux-clusters.md) pomocí stejného účtu úložiště, ale s jiným názvem kontejneru:

    ![Použijte stejný účet úložiště, ale vytvořte jiný kontejner](./media/apache-hbase-migrate-new-version/same-storage-different-container.png)

1. Vyprázdnění zdrojového clusteru HBase, což je cluster, který upgradujete. HBase zapisuje příchozí data do úložiště v paměti, nazývaném _memstore_. Poté, co memstore dosáhne určité velikosti, HBase vyprázdní na disk pro dlouhodobé úložiště v účtu úložiště clusteru. Při odstranění starého clusteru jsou recyklována memstores, potenciálně ztrácí data. Chcete-li ručně vyprázdnit úložiště memstore pro každou tabulku na disk, spusťte následující skript. Nejnovější verze tohoto skriptu je na [GitHubu](https://raw.githubusercontent.com/Azure/hbase-utils/master/scripts/flush_all_tables.sh)Azure .

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

1. Zastavit ingestování do starého clusteru HBase.

1. Chcete-li zajistit, že všechna nedávná data v úložišti memstore je vyprázdněna, spusťte předchozí skript znovu.

1. Přihlaste se k [Apache Ambari](https://ambari.apache.org/) ve starém clusteru (`https://OLDCLUSTERNAME.azurehdidnsight.net`) a zastavte služby HBase. Když se zobrazí výzva k potvrzení, že chcete služby zastavit, zaškrtněte políčko pro zapnutí režimu údržby pro HBase. Další informace o připojení k Ambari a jeho používání naleznete [v tématu Správa clusterů HDInsight pomocí webového uživatelského rozhraní Ambari](../hdinsight-hadoop-manage-ambari.md).

    ![V Ambari klikněte v části Akce služby na Služby > HBase > Zastavit.](./media/apache-hbase-migrate-new-version/stop-hbase-services1.png)

    ![Zaškrtněte políčko Zapnout režim údržby u hbase a potvrďte, že](./media/apache-hbase-migrate-new-version/turn-on-maintenance-mode.png)

1. Přihlaste se k Ambari v novém clusteru HDInsight. Změňte `fs.defaultFS` nastavení HDFS tak, aby ukazovalo na název kontejneru používaný původním clusterem. Toto nastavení je v části **KONFIGURACE hdfs > > pokročilé > pokročilé jádro .**

    ![V Ambari klikněte na služby > hdfs > konfigurace > pokročilé](./media/apache-hbase-migrate-new-version/hdfs-advanced-settings.png)

    ![V Ambari změňte název kontejneru](./media/apache-hbase-migrate-new-version/change-container-name.png)

1. Pokud nepoužíváte clustery HBase s funkcí Rozšířené zápisy, tento krok přeskočte. Je potřeba pouze pro clustery HBase s funkcí Rozšířené zápisy.

   Změňte `hbase.rootdir` cestu k přechodu na kontejner původního clusteru.

    ![V Ambari změňte název kontejneru pro Rootdir HBase](./media/apache-hbase-migrate-new-version/change-container-name-for-hbase-rootdir.png)

1. Pokud upgradujete HDInsight 3.6 na 4.0, postupujte podle následujících kroků, jinak přejděte ke kroku 10:
    1. Restartujte všechny požadované služby v Ambari výběrem**možnosti Služby restartovat všechny požadované** **služby** > .
    1. Zastavte službu HBase.
    1. SSH do uzlu Zookeeper a proveďte příkaz `rmr /hbase-unsecure` [zkCli](https://github.com/go-zkcli/zkcli) k odstranění znode kořene HBase ze Zookeeperu.
    1. Restartujte HBase.

1. Pokud upgradujete na jinou verzi HDInsightu kromě verze 4.0, postupujte takto:
    1. Uložte provedené změny.
    1. Restartujte všechny požadované služby uvedené v ambari.

1. Najeďte svou aplikaci na nový cluster.

    > [!NOTE]  
    > Statické DNS pro vaši aplikaci se změní při upgradu. Místo pevného kódování tohoto DNS můžete nakonfigurovat cname v nastavení DNS názvu domény, které odkazuje na název clusteru. Další možností je použít konfigurační soubor pro vaši aplikaci, kterou můžete aktualizovat bez opětovného nasazení.

1. Začněte s požitím, abyste zjistili, zda vše funguje podle očekávání.

1. Pokud je nový cluster uspokojivý, odstraňte původní cluster.

## <a name="next-steps"></a>Další kroky

Další informace o [apache hbase](https://hbase.apache.org/) a upgradu clusterů HDInsight najdete v následujících článcích:

* [Upgrade clusteru HDInsight na novější verzi](../hdinsight-upgrade-cluster.md)
* [Sledování a správa Azure HDInsight pomocí webového uživatelského rozhraní Apache Ambari](../hdinsight-hadoop-manage-ambari.md)
* [Komponenty a verze Apache Hadoop](../hdinsight-component-versioning.md)
* [Optimalizace konfigurací pomocí Apache Ambari](../hdinsight-changing-configs-via-ambari.md#apache-hbase-optimization-with-the-ambari-web-ui)
