---
title: Migrace clusteru služby HBase na novou verzi - Azure HDInsight | Microsoft Docs
description: Postup migrace clustery HBase na novou verzi.
services: hdinsight
documentationcenter: ''
tags: azure-portal
author: ashishthaps
manager: jhubbard
editor: cgronlun
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: ashishth
ms.openlocfilehash: 3ca982e7fc0ce56bee2ee2e193c82a78fac44362
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2018
---
# <a name="migrate-an-hbase-cluster-to-a-new-version"></a>Migrace clusteru služby HBase na novou verzi

Clustery založené na úlohy, jako jsou snadný upgrade - Spark a Hadoop, najdete v části [clusteru HDInsight se Upgrade na novější verzi](../hdinsight-upgrade-cluster.md):

1. Zálohujte data přechodný (místně uložené).
2. Odstraňte existující cluster.
3. Vytvoření nového clusteru ve stejné podsíti virtuální sítě.
4. Umožňuje importovat data přechodný.
5. Spuštění úlohy a pokračovat ve zpracování na novém clusteru.

Upgrade clusteru služby HBase, je potřeba udělat další kroky, jak je popsáno v tomto článku.

> [!NOTE]
> Výpadek při upgradu by měl být minimální v řádu minut. Tento výpadek je způsobená kroky k vyprázdnění všech dat v paměti a potom čas ke konfiguraci a znovu spusťte služby v novém clusteru. Výsledky se budou lišit v závislosti na počtu uzlů, množství dat a jiné proměnné.

## <a name="review-hbase-compatibility"></a>Kontrola kompatibility HBase

Před upgradem HBase, ujistěte se, že jsou kompatibilní verze HBase v clusterech zdrojové a cílové. Další informace najdete v tématu [Hadoop součásti a verze, které jsou k dispozici s HDInsight](../hdinsight-component-versioning.md).

> [!NOTE]
> Důrazně doporučujeme, abyste si prošli matice kompatibility verze v [HBase kniha](https://hbase.apache.org/book.html#upgrading).

Tady je matice verze kompatibility příkladu, kde Y označuje kompatibility a N označuje potenciální nekompatibilita:

| Typ kompatibility | Hlavní verze| Podverze | Oprava |
| --- | --- | --- | --- |
| Klient-Server přenosová kompatibility | Ne | Ano | Ano |
| Servery kompatibility | Ne | Ano | Ano |
| Kompatibilita formátů souborů | Ne | Ano | Ano |
| Kompatibilita klientského rozhraní API | Ne | Ano | Ano |
| Binární kompatibilitu klienta | Ne | Ne | Ano |
| **Serverové omezené rozhraní API kompatibility** |  |  |  |
| Stable | Ne | Ano | Ano |
| Vyvíjející se | Ne | Ne | Ano |
| Nestabilním | Ne | Ne | Ne |
| Závislost kompatibility | Ne | Ano | Ano |
| Provozní kompatibility | Ne | Ne | Ano |

> [!NOTE]
> Žádné nekompatibility ukončování řádků by měl popsané v poznámkách k verzi verze HBase.

## <a name="upgrade-with-same-hbase-major-version"></a>Upgrade se stejnou hlavní verzi HBase

V následujícím scénáři je pro upgrade HDInsight 3.4 na 3.6 (jsou součástí Apache HBase 1.1.2) se stejnou hlavní verzi HBase. Jiné verze upgrady jsou podobné, dokud nejsou žádné problémy s kompatibilitou mezi zdrojovým a cílovým verzemi.

1. Ujistěte se, že aplikace je kompatibilní s novou verzi, jak je znázorněno v poznámkách matice a verze kompatibility HBase. Testování vaší aplikace v clusteru se systémem cílovou verzi sady HDInsight a HBase.

2. [Nastavit nový cílový cluster HDInsight](../hdinsight-hadoop-provision-linux-clusters.md) pomocí stejného účtu úložiště, ale s názvem jiný kontejner:

    ![Použít stejný účet úložiště, ale vytvoření různých kontejneru](./media/apache-hbase-migrate-new-version/same-storage-different-container.png)

3. Vyprázdnění cluster HBase zdroje. To je cluster, ze kterého provádíte upgrade. HBase zapisuje příchozí data do úložiště v paměti názvem _metody_. Po metody dosáhne určité velikosti, metody vyprazdňuje na disk pro dlouhodobé uložení v účtu úložiště clusteru. Při odstraňování původního clusteru, jsou recyklovány, potenciálně ke ztrátě dat memstores. Chcete-li ručně vyprázdnit metody pro každou tabulku na disk, spusťte následující skript. Nejnovější verze tento skript je na Azure [Githubu](https://raw.githubusercontent.com/Azure/hbase-utils/master/scripts/flush_all_tables.sh).

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
    
4. Zastavte přijímání v původním clusteru HBase.
5. Aby se zajistilo, že žádná poslední data v metody vyprazdňuje, předchozí skript znovu spusťte.
6. Přihlaste se k Ambari v původním clusteru (https://OLDCLUSTERNAME.azurehdidnsight.net) a zastavení služby HBase. Po zobrazení výzvy k potvrzení, že byste chtěli zastavit služby, zaškrtněte políčko k zapnutí v režimu údržby pro HBase. Další informace o připojení k a pomocí nástroje Ambari najdete v tématu [Správa clusterů HDInsight pomocí webového uživatelského rozhraní Ambari](../hdinsight-hadoop-manage-ambari.md).

    ![V Ambari klikněte na kartu služby, potom HBase v levé nabídce pak zastavit v části Akce služby](./media/apache-hbase-migrate-new-version/stop-hbase-services.png)

    ![Zkontrolujte zapnout v režimu údržby u zaškrtávacího políčka HBase a potom potvrďte](./media/apache-hbase-migrate-new-version/turn-on-maintenance-mode.png)

7. Přihlaste se k Ambari v novém clusteru HDInsight. Změna `fs.defaultFS` HDFS nastavení tak, aby odkazoval na kontejneru název používaný v původním clusteru. Toto nastavení je v části **HDFS > konfigurací > Upřesnit > Upřesnit core-site**.

    ![V Ambari klikněte na kartu služby, potom HDFS v levé nabídce pak kartě konfigurací, pak kartě Upřesnit pod](./media/apache-hbase-migrate-new-version/hdfs-advanced-settings.png)

    ![V Ambari změňte název kontejneru](./media/apache-hbase-migrate-new-version/change-container-name.png)

8. Uložte provedené změny.
9. Jak Ambari, restartujte všechny požadované služby.
10. Bod vaší aplikace do nového clusteru.

    > [!NOTE]
    > Statické DNS pro vaši aplikaci se změní při upgradu. Místo pevně kódováno DNS, můžete nakonfigurovat záznam CNAME v nastavení DNS pro název domény, které odkazuje na název clusteru. Další možností je použití konfiguračního souboru pro vaši aplikaci, kterou můžete aktualizovat bez opětovného nasazení.

11. Spusťte přijímání zobrazíte, když všechno funguje podle očekávání.
12. Pokud nový cluster je vyhovující, odstraňte původní cluster.

## <a name="next-steps"></a>Další postup

Další informace o HBase a upgrade clusterů HDInsight, naleznete v následujících článcích:

* [Upgrade clusteru HDInsight na novější verzi](../hdinsight-upgrade-cluster.md)
* [Sledování a správě Azure HDInsight pomocí Ambari webového uživatelského rozhraní](../hdinsight-hadoop-manage-ambari.md)
* [Komponent systému Hadoop a verze](../hdinsight-component-versioning.md)
* [Optimalizace konfigurace pomocí nástroje Ambari](../hdinsight-changing-configs-via-ambari.md#hbase-optimization-with-the-ambari-web-ui)
