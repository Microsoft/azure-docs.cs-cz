---
title: 'Rychlý start: Dotaz Apache HBase v Azure HDInsight – Apache Phoenix'
description: Další informace o použití Apache Phoenixu v HDInsight. Také zjistěte, jak nainstalovat a nastavit SQLLine ve vašem počítači pro připojení ke clusteru služby HBase v HDInsight.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: quickstart
ms.date: 05/08/2019
ms.author: hrasheed
ms.openlocfilehash: 46606a991ce878a3335c2c605a4040c9520d5128
ms.sourcegitcommit: 1fbc75b822d7fe8d766329f443506b830e101a5e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/14/2019
ms.locfileid: "65596200"
---
# <a name="quickstart-query-apache-hbase-in-azure-hdinsight-with-apache-phoenix"></a>Rychlý start: Dotaz Apache HBase v Azure HDInsight s Apache Phoenix

V tomto rychlém startu se dozvíte, jak používat Apache Phoenix ke spouštění dotazů HBase v Azure HDInsight. Apache Phoenix je modul dotazů SQL pro Apache HBase. Je přístupný jako ovladač JDBC a umožňuje dotazování a správu tabulek HBase pomocí SQL. [SQLLine](http://sqlline.sourceforge.net/) je nástroj příkazového řádku ke spuštění SQL.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

* Clustery Apache HBase. Zobrazit [vytvořit cluster](../hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster) k vytvoření clusteru HDInsight.  Zajistěte si vybrat **HBase** typ clusteru.

* Klient SSH. Další informace najdete v tématu [připojení k HDInsight (Apache Hadoop) pomocí protokolu SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="identify-a-zookeeper-node"></a>Identifikaci uzlu ZooKeeper

Když se připojíte ke clusteru služby HBase, musíte připojit k jednomu z uzlů Apache ZooKeeper. Každý cluster HDInsight má tři uzly ZooKeeper. Curl je možné rychle identifikovat uzlu ZooKeeper. Upravte následující příkaz curl nahrazením `PASSWORD` a `CLUSTERNAME` příslušnými hodnotami a pak v příkazovém řádku zadejte příkaz:

```cmd
curl -u admin:PASSWORD -sS -G https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER
```

Část výstup bude vypadat podobně jako:

```output
    {
      "href" : "http://hn1-brim.432dc3rlshou3ocf251eycoapa.bx.internal.cloudapp.net:8080/api/v1/clusters/myCluster/hosts/zk0-brim.432dc3rlshou3ocf251eycoapa.bx.internal.cloudapp.net/host_components/ZOOKEEPER_SERVER",
      "HostRoles" : {
        "cluster_name" : "myCluster",
        "component_name" : "ZOOKEEPER_SERVER",
        "host_name" : "zk0-brim.432dc3rlshou3ocf251eycoapa.bx.internal.cloudapp.net"
      }
```

Poznamenejte si hodnoty pro `host_name` pro pozdější použití.

## <a name="create-a-table-and-manipulate-data"></a>Vytvoření tabulky a pracovat s daty

Můžete použít SSH pro připojení ke clusterům HBase a pak pomocí Apache Phoenix vytváření tabulek HBase, vkládání dat a dotazování na data.

1. Použití `ssh` příkazu se připojte ke svému clusteru HBase. Upravte následující příkaz tak, že nahradíte `CLUSTERNAME` s názvem vašeho clusteru a potom zadejte příkaz:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. Změňte adresář na Phoenix klienta. Zadejte následující příkaz:

    ```bash
    cd /usr/hdp/current/phoenix-client/bin
    ```

3. Spuštění [SQLLine](http://sqlline.sourceforge.net/). Upravte následující příkaz tak, že nahradíte `ZOOKEEPER` uzlu ZooKeeper, dřív identifikovali, zadejte příkaz:

    ```bash
    ./sqlline.py ZOOKEEPER:2181:/hbase-unsecure
    ```

4. Vytvoření tabulky HBase. Zadejte následující příkaz:

    ```sql
    CREATE TABLE Company (company_id INTEGER PRIMARY KEY, name VARCHAR(225));
    ```

5. Použít SQLLine `!tables` příkazu zobrazte výpis všech tabulek v HBase. Zadejte následující příkaz:

    ```sqlline
    !tables
    ```

6. Vložení hodnoty v tabulce. Zadejte následující příkaz:

    ```sql
    UPSERT INTO Company VALUES(1, 'Microsoft');
    UPSERT INTO Company VALUES(2, 'Apache');
    ```

7. Dotaz tabulku. Zadejte následující příkaz:

    ```sql
    SELECT * FROM Company;
    ```

8. Odstranění záznamu. Zadejte následující příkaz:

    ```sql
    DELETE FROM Company WHERE COMPANY_ID=1;
    ```

9. Odstranit tabulku. Zadejte následující příkaz:

    ```hbase
    DROP TABLE Company;
    ```

10. Použít SQLLine `!quit` příkaz pro ukončení SQLLine. Zadejte následující příkaz:

    ```sqlline
    !quit
    ```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Po dokončení tohoto rychlého startu, můžete cluster odstranit. Pomocí HDInsight jsou vaše data uložena v Azure Storage, takže můžete clusteru bezpečně odstranit, pokud není používán. Za cluster služby HDInsight se účtují poplatky, i když se nepoužívá. Vzhledem k tomu, že poplatky za cluster představují několikanásobek poplatků za úložiště, dává ekonomický smysl odstraňovat clustery, které nejsou používány.

Odstranění clusteru, naleznete v tématu [odstranění clusteru HDInsight pomocí prohlížeče, Powershellu nebo rozhraní příkazového řádku Azure](../hdinsight-delete-cluster.md).

## <a name="next-steps"></a>Další postup

V tomto rychlém startu jste zjistili, jak používat Apache Phoenix ke spouštění dotazů HBase v Azure HDInsight. Další informace o Apache Phoenix, další článek poskytuje podrobnější zkoumání.

> [!div class="nextstepaction"]
> [Apache Phoenix v HDInsight](../hdinsight-phoenix-in-hdinsight.md)

## <a name="see-also"></a>Viz také

* [Ruční SQLLine](http://sqlline.sourceforge.net/#manual).
* [Apache Phoenix gramatiky](https://phoenix.apache.org/language/index.html).
* [Apache Phoenix za 15 minut nebo i rychleji](https://phoenix.apache.org/Phoenix-in-15-minutes-or-less.html)
* [Přehled HDInsight HBase](./apache-hbase-overview.md)
