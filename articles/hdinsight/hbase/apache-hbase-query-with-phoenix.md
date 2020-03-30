---
title: 'Úvodní příručka: Apache HBase & Apache Phoenix - Azure HDInsight'
description: V tomto rychlém startu se dozvíte, jak používat Apache Phoenix v HDInsight. Přečtěte si také, jak nainstalovat a nastavit SQLLine v počítači pro připojení ke clusteru HBase v HDInsight.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: quickstart
ms.date: 06/12/2019
ms.author: hrasheed
ms.openlocfilehash: 1c400e41c4c10023d2595bde8c0d62e26184cf05
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "79370317"
---
# <a name="quickstart-query-apache-hbase-in-azure-hdinsight-with-apache-phoenix"></a>Úvodní příručka: Dotaz na Apache HBase v Azure HDInsight s Apache Phoenix

V tomto rychlém startu se dozvíte, jak pomocí Apache Phoenix spustit dotazy HBase v Azure HDInsight. Apache Phoenix je SQL dotazovací engine pro Apache HBase. Je přístupný jako ovladač JDBC a umožňuje dotazování a správu tabulek HBase pomocí SQL. [SQLLine](http://sqlline.sourceforge.net/) je nástroj příkazového řádku pro spuštění SQL.

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

## <a name="prerequisites"></a>Požadavky

* Cluster Apache HBase. Viz [Vytvoření clusteru](../hadoop/apache-hadoop-linux-tutorial-get-started.md) a vytvoření clusteru HDInsight.  Ujistěte se, že jste zvolili typ clusteru **HBase.**

* Klient SSH. Další informace naleznete [v tématu Připojení k HDInsight (Apache Hadoop) pomocí SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="identify-a-zookeeper-node"></a>Identifikace uzlu ZooKeeper

Když se připojíte ke clusteru HBase, musíte se připojit k jednomu z uzlů Apache ZooKeeper. Každý cluster HDInsight má tři uzly ZooKeeper. Curl lze použít k rychlé identifikaci uzlu ZooKeeper. Upravte níže uvedený příkaz `PASSWORD` `CLUSTERNAME` curl nahrazením a příslušnými hodnotami a poté jej zadejte do příkazového řádku:

```cmd
curl -u admin:PASSWORD -sS -G https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER
```

Část výstupu bude vypadat podobně jako:

```output
    {
      "href" : "http://hn1-brim.432dc3rlshou3ocf251eycoapa.bx.internal.cloudapp.net:8080/api/v1/clusters/myCluster/hosts/zk0-brim.432dc3rlshou3ocf251eycoapa.bx.internal.cloudapp.net/host_components/ZOOKEEPER_SERVER",
      "HostRoles" : {
        "cluster_name" : "myCluster",
        "component_name" : "ZOOKEEPER_SERVER",
        "host_name" : "zk0-brim.432dc3rlshou3ocf251eycoapa.bx.internal.cloudapp.net"
      }
```

Poznamenejte si `host_name` hodnotu pro pozdější použití.

## <a name="create-a-table-and-manipulate-data"></a>Vytvoření tabulky a manipulace s daty

Pomocí SSH se můžete připojit ke clusterům HBase a potom pomocí Apache Phoenix vytvářet tabulky HBase, vkládat data a data dotazů.

1. Pomocí `ssh` příkazu se můžete připojit ke clusteru HBase. Upravte níže uvedený `CLUSTERNAME` příkaz nahrazením názvem clusteru a zadejte příkaz:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. Změňte adresář na klienta Phoenix. Zadejte následující příkaz:

    ```bash
    cd /usr/hdp/current/phoenix-client/bin
    ```

3. Spusťte [SQLLine](http://sqlline.sourceforge.net/). Upravte níže uvedený `ZOOKEEPER` příkaz nahrazením dříve identifikovaného uzlu ZooKeeper a zadejte příkaz:

    ```bash
    ./sqlline.py ZOOKEEPER:2181:/hbase-unsecure
    ```

4. Vytvořte tabulku HBase. Zadejte následující příkaz:

    ```sql
    CREATE TABLE Company (company_id INTEGER PRIMARY KEY, name VARCHAR(225));
    ```

5. Pomocí příkazu `!tables` SQLLine můžete vypsat všechny tabulky v HBase. Zadejte následující příkaz:

    ```sqlline
    !tables
    ```

6. Vložte hodnoty do tabulky. Zadejte následující příkaz:

    ```sql
    UPSERT INTO Company VALUES(1, 'Microsoft');
    UPSERT INTO Company VALUES(2, 'Apache');
    ```

7. Dotaz na tabulku. Zadejte následující příkaz:

    ```sql
    SELECT * FROM Company;
    ```

8. Odstranit záznam. Zadejte následující příkaz:

    ```sql
    DELETE FROM Company WHERE COMPANY_ID=1;
    ```

9. Odhoďte stůl. Zadejte následující příkaz:

    ```hbase
    DROP TABLE Company;
    ```

10. Pomocí příkazu `!quit` SQLLine ukončete sqlline. Zadejte následující příkaz:

    ```sqlline
    !quit
    ```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Po dokončení rychlého startu můžete cluster odstranit. Pomocí HDInsight jsou vaše data uložena v Azure Storage, takže můžete clusteru bezpečně odstranit, pokud není používán. Za cluster služby HDInsight se účtují poplatky, i když se nepoužívá. Vzhledem k tomu, že poplatky za cluster představují několikanásobek poplatků za úložiště, dává ekonomický smysl odstraňovat clustery, které nejsou používány.

Pokud chcete odstranit cluster, přečtěte si informace [o odstranění clusteru HDInsight pomocí prohlížeče, PowerShellu nebo rozhraní příkazového příkazu k Azure](../hdinsight-delete-cluster.md).

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste se naučili používat Apache Phoenix ke spouštění dotazů HBase v Azure HDInsight. Chcete-li se dozvědět více o Apache Phoenix, další článek poskytne hlubší vyšetření.

> [!div class="nextstepaction"]
> [Apache Phoenix ve službě HDInsight](../hdinsight-phoenix-in-hdinsight.md)
