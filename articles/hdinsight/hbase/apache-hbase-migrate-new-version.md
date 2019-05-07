---
title: Migrace clusteru HBase na novou verzi – Azure HDInsight
description: Jak migrovat clustery HBase na novou verzi.
author: ashishthaps
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: ashishth
ms.openlocfilehash: a152b815daeefa4c199af9b159eee8e5783971e2
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65143327"
---
# <a name="migrate-an-apache-hbase-cluster-to-a-new-version"></a>Migrace clusteru Apache HBase na novou verzi.

Tento článek popisuje kroky potřebné k aktualizaci vašeho clusteru Apache HBase v Azure HDInsight na novější verzi.

> [!NOTE]  
> Prostoje při upgradu by měl být minimální v řádu minut. Tento výpadek je způsobeno kroky k vyprázdnění všech dat v paměti, pak čas ke konfiguraci a znovu spusťte služby v novém clusteru. Vaše výsledky se budou lišit v závislosti na počtu uzlů, velikost dat a jiné proměnné.

## <a name="review-apache-hbase-compatibility"></a>Kontrola kompatibility Apache HBase

Před upgradem Apache HBase, ujistěte se, že jsou kompatibilní verze HBase na zdrojových a cílových clusterech. Další informace najdete v tématu [komponenty a verze k dispozici s HDInsight Apache Hadoopu](../hdinsight-component-versioning.md).

> [!NOTE]  
> Důrazně doporučujeme, abyste si přečetli matici kompatibility verzí v [HBase knihy](https://hbase.apache.org/book.html#upgrading).

Tady je matici kompatibility verzí příklad. Y označuje kompatibilitu a N označuje potenciální nekompatibility:

| Typ kompatibility | Hlavní verze| Podverze | Oprava |
| --- | --- | --- | --- |
| Kompatibilita přenosový Klient-Server | Ne | Ano | Ano |
| Servery kompatibility | Ne | Ano | Ano |
| Kompatibilita formátů souborů | Ne | Ano | Ano |
| Kompatibilita s klientským rozhraní API | Ne | Ano | Ano |
| Binární kompatibilita s klientským | Ne | Ne | Ano |
| **Serverové omezené kompatibilitu s rozhraními API** |  |  |  |
| Stable | Ne | Ano | Ano |
| Vyvíjejí | Ne | Ne | Ano |
| Nestabilní | Ne | Ne | Ne |
| Kompatibilita závislostí | Ne | Ano | Ano |
| Provozní kompatibility | Ne | Ne | Ano |

> [!NOTE]  
> Jakékoli zásadní nekompatibility by měl popsané v zpráva k vydání verze HBase.

## <a name="upgrade-with-same-apache-hbase-major-version"></a>Upgrade se stejnou hlavní verzi Apache HBase

Upgrade clusteru Apache HBase v Azure HDInsight, proveďte následující kroky:

1. Ujistěte se, že je vaše aplikace kompatibilní s novou verzí, jak je znázorněno v HBase kompatibility matice a release notes. Otestujte aplikaci v clusteru se systémem cílovou verzi sady HDInsight a HBase.

2. [Nastavit nový cluster HDInsight cílové](../hdinsight-hadoop-provision-linux-clusters.md) pomocí stejného účtu úložiště, ale s jiným kontejnerem název:

    ![Použijte stejný účet úložiště, ale vytvoření různých kontejneru](./media/apache-hbase-migrate-new-version/same-storage-different-container.png)

3. Vyprázdnění váš cluster HBase zdroj, který je clusteru, kterou upgradujete. HBase zapisuje příchozí data do úložiště v paměti volá _paměťového úložiště_. Po paměťového úložiště dosáhne určité velikosti, vyprázdní HBase ho na disk pro dlouhodobé uložení v účtu úložiště clusteru. Při odstraňování původní cluster, memstores jsou recyklovány, přijít o data. Chcete-li ručně vyprázdnit paměťového úložiště pro každou tabulku na disk, spusťte následující skript. Nejnovější verze tohoto skriptu je na Azure [Githubu](https://raw.githubusercontent.com/Azure/hbase-utils/master/scripts/flush_all_tables.sh).

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
    
            Provide hostname as a parameter (i.e. hn0, hn1 or wn2 etc.).
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
    
4. Zastavte příjem pro původní cluster HBase.
5. Chcete-li zajistit všechna poslední data v metody vyprazdňuje, spusťte znovu skript pro předchozí.
6. Přihlaste se k [Apache Ambari](https://ambari.apache.org/) v původním clusteru (https://OLDCLUSTERNAME.azurehdidnsight.net) a zastavit služby HBase. Po zobrazení výzvy potvrďte, že chcete zastavit služby, zaškrtněte políčko Zapnout režim údržby pro HBase. Další informace o připojení k a pomocí nástroje Ambari, naleznete v tématu [HDInsight Správa clusterů pomocí webového uživatelského rozhraní Ambari](../hdinsight-hadoop-manage-ambari.md).

    ![V Ambari, klepněte na položku služby > HBase > Zastavit v části Akce služby](./media/apache-hbase-migrate-new-version/stop-hbase-services.png)

    ![Zkontrolujte zapnout v režimu údržby pro HBase zaškrtávací políčko a potom potvrdit](./media/apache-hbase-migrate-new-version/turn-on-maintenance-mode.png)

7. Přihlaste se k Ambari na novém clusteru HDInsight. Změnit `fs.defaultFS` HDFS nastavení tak, aby odkazoval na název kontejneru původní cluster používat. Toto nastavení je v části **HDFS > Konfigurace > Upřesnit > Upřesnit základního webu**.

    ![V Ambari, klepněte na položku služby > HDFS > Konfigurace > Upřesnit](./media/apache-hbase-migrate-new-version/hdfs-advanced-settings.png)

    ![V Ambari změňte název kontejneru](./media/apache-hbase-migrate-new-version/change-container-name.png)

8. **Pokud nepoužíváte clustery HBase pomocí funkce rozšířeného zapisuje, tento krok přeskočte. Je potřeba pouze pro clustery HBase pomocí funkce rozšířeného zapisuje.**
   
   Změnit `hbase.rootdir` cestu tak, aby odkazoval na kontejner k původnímu clusteru.

    ![V Ambari změňte název kontejneru pro HBase rootdir](./media/apache-hbase-migrate-new-version/change-container-name-for-hbase-rootdir.png)
1. Pokud upgradujete HDInsight 3.6 4.0, postupujte podle následujících kroků, v opačném případě přejděte ke kroku 10:
    1. Restartujte všechny požadované služby v Ambari tak, že vyberete **služby** > **restartujte všechny požadované**.
    1. Zastavte službu HBase.
    1. SSH k uzlu Zookeeper a spustit [zkCli](https://github.com/go-zkcli/zkcli) příkaz `rmr /hbase-unsecure` znode kořenové HBase odebrání Zookeeper.
    1. Restartujte HBase.
1. Pokud provádíte upgrade na jakoukoli jinou verzi HDInsight kromě 4.0, postupujte podle těchto kroků:
    1. Uložte provedené změny.
    1. Je určeno Ambari, restartujte všechny požadované služby.
1. Bod aplikace do nového clusteru.

    > [!NOTE]  
    > Statické DNS pro vaši aplikaci se změní při upgradu. Místo pevného kódování této služby DNS, můžete nakonfigurovat záznam CNAME v nastavení DNS pro název domény, které odkazuje na název clusteru. Další možností je použít konfigurační soubor pro vaši aplikaci, kterou můžete aktualizovat bez opětovného nasazení.

12. Začněte tak příjem dat, pokud chcete zobrazit, pokud všechno funguje podle očekávání.
13. Pokud nový cluster nachází uspokojivé kvality, odstraňte původní cluster.

## <a name="next-steps"></a>Další postup

Další informace o [Apache HBase](https://hbase.apache.org/) a inovace clusterů HDInsight, naleznete v následujících článcích:

* [Upgrade clusteru HDInsight na novější verzi](../hdinsight-upgrade-cluster.md)
* [Monitorování a správa Azure HDInsight pomocí webového uživatelského rozhraní Apache Ambari](../hdinsight-hadoop-manage-ambari.md)
* [Apache Hadoop komponenty a verze](../hdinsight-component-versioning.md)
* [Optimalizace konfigurace pomocí nástroje Apache Ambari](../hdinsight-changing-configs-via-ambari.md#apache-hbase-optimization-with-the-ambari-web-ui)
