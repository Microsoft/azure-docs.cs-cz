---
title: 'Rychlý Start: Apache HBA & Apache Phoenix – Azure HDInsight'
description: V tomto rychlém startu se dozvíte, jak používat Apache Phoenix v HDInsight. Přečtěte si také, jak nainstalovat a nastavit SQLLine na počítači pro připojení k clusteru HBA v HDInsight.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: quickstart
ms.date: 06/12/2019
ms.author: hrasheed
ms.openlocfilehash: 1c400e41c4c10023d2595bde8c0d62e26184cf05
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "79370317"
---
# <a name="quickstart-query-apache-hbase-in-azure-hdinsight-with-apache-phoenix"></a>Rychlý Start: dotazování Apache HBA ve službě Azure HDInsight pomocí Apache Phoenix

V tomto rychlém startu se dozvíte, jak používat Apache Phoenix ke spouštění dotazů HBA v Azure HDInsight. Apache Phoenix je dotazovací modul SQL pro Apache HBA. Je přístupný jako ovladač JDBC a umožňuje dotazování a správu tabulek HBase pomocí SQL. [SQLLine](http://sqlline.sourceforge.net/) je nástroj příkazového řádku pro spuštění SQL.

Pokud ještě předplatné Azure nemáte, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Předpoklady

* Cluster Apache HBA. V tématu [Vytvoření clusteru](../hadoop/apache-hadoop-linux-tutorial-get-started.md) vytvořte cluster HDInsight.  Ujistěte se, že jste vybrali typ clusteru **HBA** .

* Klient SSH. Další informace najdete v tématu [připojení ke službě HDInsight (Apache Hadoop) pomocí SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="identify-a-zookeeper-node"></a>Identifikujte uzel ZooKeeper

Když se připojíte ke clusteru HBA, budete se muset připojit k jednomu z Apache ZooKeeper uzlů. Každý cluster HDInsight má tři ZooKeeper uzly. K rychlé identifikaci uzlu ZooKeeper lze použít kudrlinkou. Úpravou níže uvedeného příkazu získáte nahrazením `PASSWORD` a `CLUSTERNAME` s odpovídajícími hodnotami a potom do příkazového řádku zadejte příkaz:

```cmd
curl -u admin:PASSWORD -sS -G https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER
```

Část výstupu bude vypadat přibližně takto:

```output
    {
      "href" : "http://hn1-brim.432dc3rlshou3ocf251eycoapa.bx.internal.cloudapp.net:8080/api/v1/clusters/myCluster/hosts/zk0-brim.432dc3rlshou3ocf251eycoapa.bx.internal.cloudapp.net/host_components/ZOOKEEPER_SERVER",
      "HostRoles" : {
        "cluster_name" : "myCluster",
        "component_name" : "ZOOKEEPER_SERVER",
        "host_name" : "zk0-brim.432dc3rlshou3ocf251eycoapa.bx.internal.cloudapp.net"
      }
```

Poznamenejte si hodnotu pro `host_name` pozdější použití.

## <a name="create-a-table-and-manipulate-data"></a>Vytvoření tabulky a manipulace s daty

Pomocí SSH se můžete připojit k clusterům HBA a pak pomocí Apache Phoenix vytvořit tabulky HBA, vkládat data a dotazovat data.

1. Pomocí `ssh` příkazu se připojte ke clusteru HBA. Níže uvedený příkaz upravte nahrazením `CLUSTERNAME` názvem vašeho clusteru a zadáním příkazu:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. Změňte adresář na klienta v Phoenixu. Zadejte následující příkaz:

    ```bash
    cd /usr/hdp/current/phoenix-client/bin
    ```

3. Spusťte [SQLLine](http://sqlline.sourceforge.net/). Níže uvedený příkaz upravte tak, že nahradíte `ZOOKEEPER` uzel Zookeeper, který jste identifikovali dříve, a pak zadáte příkaz:

    ```bash
    ./sqlline.py ZOOKEEPER:2181:/hbase-unsecure
    ```

4. Vytvořte tabulku HBA. Zadejte následující příkaz:

    ```sql
    CREATE TABLE Company (company_id INTEGER PRIMARY KEY, name VARCHAR(225));
    ```

5. Použijte příkaz SQLLine `!tables` k vypsání všech tabulek v adaptérech HBA. Zadejte následující příkaz:

    ```sqlline
    !tables
    ```

6. Vloží hodnoty do tabulky. Zadejte následující příkaz:

    ```sql
    UPSERT INTO Company VALUES(1, 'Microsoft');
    UPSERT INTO Company VALUES(2, 'Apache');
    ```

7. Dotaz na tabulku Zadejte následující příkaz:

    ```sql
    SELECT * FROM Company;
    ```

8. Odstraní záznam. Zadejte následující příkaz:

    ```sql
    DELETE FROM Company WHERE COMPANY_ID=1;
    ```

9. Přetáhněte tabulku. Zadejte následující příkaz:

    ```hbase
    DROP TABLE Company;
    ```

10. Pomocí příkazu SQLLine `!quit` Ukončete SQLLine. Zadejte následující příkaz:

    ```sqlline
    !quit
    ```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Po dokončení rychlého startu možná budete chtít cluster odstranit. Pomocí HDInsight jsou vaše data uložena v Azure Storage, takže můžete clusteru bezpečně odstranit, pokud není používán. Za cluster služby HDInsight se účtují poplatky, i když se nepoužívá. Vzhledem k tomu, že poplatky za cluster představují několikanásobek poplatků za úložiště, dává ekonomický smysl odstraňovat clustery, které nejsou používány.

Pokud chcete odstranit cluster, přečtěte si téma [odstranění clusteru HDInsight pomocí prohlížeče, PowerShellu nebo rozhraní příkazového řádku Azure](../hdinsight-delete-cluster.md).

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste zjistili, jak používat Apache Phoenix ke spouštění dotazů HBA v Azure HDInsight. Další informace o Apache Phoenix najdete v dalším článku o hlubším hodnocení.

> [!div class="nextstepaction"]
> [Apache Phoenix ve službě HDInsight](../hdinsight-phoenix-in-hdinsight.md)
