---
title: 'Úvodní příručka: Dotaz apache hbase v Azure HDInsight - HBase Shell'
description: V tomto rychlém startu se dozvíte, jak pomocí Apache HBase Shell spustit dotazy Apache HBase.
keywords: hdinsight,hadoop,HBase
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: quickstart
ms.date: 06/12/2019
ms.author: hrasheed
ms.openlocfilehash: 572262cbece26171f9a67bf073906fa2dfd4d8e1
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "79371065"
---
# <a name="quickstart-query-apache-hbase-in-azure-hdinsight-with-hbase-shell"></a>Úvodní příručka: Dotaz apache hbase v Azure HDInsight s HBase Shell

V tomto rychlém startu se dozvíte, jak pomocí prostředí Apache HBase vytvořit tabulku HBase, vložit data a potom se na tabulku dotazovat.

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

## <a name="prerequisites"></a>Požadavky

* Cluster Apache HBase. Viz [Vytvoření clusteru](../hadoop/apache-hadoop-linux-tutorial-get-started.md) a vytvoření clusteru HDInsight.  Ujistěte se, že jste zvolili typ clusteru **HBase.**

* Klient SSH. Další informace naleznete [v tématu Připojení k HDInsight (Apache Hadoop) pomocí SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="create-a-table-and-manipulate-data"></a>Vytvoření tabulky a manipulace s daty

Pro většinu osob se data zobrazí v tabulkovém formátu:

![Tabulková data HDInsight Apache HBase](./media/query-hbase-with-hbase-shell/hdinsight-hbase-contacts-tabular.png)

V HBase (implementace [Cloud BigTable](https://cloud.google.com/bigtable/)) vypadají stejná data takto:

![HDInsight Apache HBase BigTable data](./media/query-hbase-with-hbase-shell/hdinsight-hbase-contacts-bigtable.png)

Pomocí SSH se můžete připojit ke clusterům HBase a potom pomocí prostředí Apache HBase vytvořit tabulky HBase, vložit data a data dotazů.

1. Pomocí `ssh` příkazu se můžete připojit ke clusteru HBase. Upravte níže uvedený `CLUSTERNAME` příkaz nahrazením názvem clusteru a zadejte příkaz:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. Pomocí `hbase shell` příkazu spusťte interaktivní prostředí HBase. V připojení SSH zadejte následující příkaz:

    ```bash
    hbase shell
    ```

3. Příkaz `create` slouží k vytvoření tabulky HBase s rodinami se dvěma sloupci. Zadejte následující příkaz:

    ```hbase
    create 'Contacts', 'Personal', 'Office'
    ```

4. Příkaz `list` slouží k zobrazení seznamu všech tabulek v HBase. Zadejte následující příkaz:

    ```hbase
    list
    ```

5. Příkaz `put` slouží k vložení hodnot do zadaného sloupce v zadaném řádku v určité tabulce. Zadejte následující příkaz:

    ```hbase
    put 'Contacts', '1000', 'Personal:Name', 'John Dole'
    put 'Contacts', '1000', 'Personal:Phone', '1-425-000-0001'
    put 'Contacts', '1000', 'Office:Phone', '1-425-000-0002'
    put 'Contacts', '1000', 'Office:Address', '1111 San Gabriel Dr.'
    ```

6. Pomocí `scan` příkazu můžete skenovat a vracet data `Contacts` tabulky. Zadejte následující příkaz:

    ```hbase
    scan 'Contacts'
    ```

7. Pomocí `get` příkazu načíst obsah řádku. Zadejte následující příkaz:

    ```hbase
    get 'Contacts', '1000'
    ```

    Zobrazí se podobné výsledky `scan` jako pomocí příkazu, protože existuje pouze jeden řádek.

8. Příkaz `delete` slouží k odstranění hodnoty buňky v tabulce. Zadejte následující příkaz:

    ```hbase
    delete 'Contacts', '1000', 'Office:Address'
    ```

9. Pomocí `disable` příkazu tabulku zakažte. Zadejte následující příkaz:

    ```hbase
    disable 'Contacts'
    ```

10. Pomocí `drop` příkazu můžete přetáhnout tabulku z HBase. Zadejte následující příkaz:

    ```hbase
    drop 'Contacts'
    ```

11. Pomocí `exit` příkazu zastavte interaktivní prostředí HBase. Zadejte následující příkaz:

    ```hbase
    exit
    ```

Další informace o schématu tabulky HBase naleznete v [tématu Úvod do návrhu schématu Apache HBase](http://0b4af6cdc2f0c5998459-c0245c5c937c5dedcca3f1764ecc9b2f.r43.cf2.rackcdn.com/9353-login1210_khurana.pdf). Další příkazy HBase najdete v tématu [Referenční příručka Apache HBase](https://hbase.apache.org/book.html#quickstart).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Po dokončení rychlého startu můžete cluster odstranit. Pomocí HDInsight jsou vaše data uložena v Azure Storage, takže můžete clusteru bezpečně odstranit, pokud není používán. Za cluster služby HDInsight se účtují poplatky, i když se nepoužívá. Vzhledem k tomu, že poplatky za cluster představují několikanásobek poplatků za úložiště, dává ekonomický smysl odstraňovat clustery, které nejsou používány.

Pokud chcete odstranit cluster, přečtěte si informace [o odstranění clusteru HDInsight pomocí prohlížeče, PowerShellu nebo rozhraní příkazového příkazu k Azure](../hdinsight-delete-cluster.md).

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste se naučili používat Prostředí Apache HBase k vytvoření tabulky HBase, vložení dat a následnému dotazování na tabulku. Chcete-li se dozvědět více o datech uložených v HBase, další článek vám ukáže, jak provádět dotazy s Apache Spark.

> [!div class="nextstepaction"]
> [Použití Apache Sparku ke čtení a zápisu dat Apache HBase](../hdinsight-using-spark-query-hbase.md)