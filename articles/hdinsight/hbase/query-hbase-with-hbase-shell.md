---
title: 'Rychlý Start: dotazování Apache HBA v prostředí Azure HDInsight – HBA'
description: V tomto rychlém startu se dozvíte, jak používat prostředí Apache HBA pro spouštění dotazů Apache HBA.
keywords: HDInsight, Hadoop, HBA
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: quickstart
ms.date: 06/12/2019
ms.openlocfilehash: 358e058667cb1750a6fc97dffebcbfb2c6e06f84
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2021
ms.locfileid: "98942922"
---
# <a name="quickstart-query-apache-hbase-in-azure-hdinsight-with-hbase-shell"></a>Rychlý Start: dotazování Apache HBA ve službě Azure HDInsight pomocí prostředí HBA

V tomto rychlém startu se dozvíte, jak pomocí prostředí Apache HBA vytvořit tabulku HBA, vkládat data a pak dotazovat tabulku.

Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Požadavky

* Cluster Apache HBA. V tématu [Vytvoření clusteru](../hadoop/apache-hadoop-linux-tutorial-get-started.md) vytvořte cluster HDInsight.  Ujistěte se, že jste vybrali typ clusteru **HBA** .

* Klient SSH. Další informace najdete v tématu [Připojení ke službě HDInsight (Apache Hadoop) pomocí SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="create-a-table-and-manipulate-data"></a>Vytvoření tabulky a manipulace s daty

Pro většinu osob se data zobrazí v tabulkovém formátu:

![Tabulková data HDInsight Apache HBA](./media/query-hbase-with-hbase-shell/hdinsight-hbase-contacts-tabular.png)

V rámci adaptérů HBA (implementace [cloudu BigTable](https://cloud.google.com/bigtable/)) vypadají stejná data jako:

![BigTable data o službě HDInsight Apache HBA](./media/query-hbase-with-hbase-shell/hdinsight-hbase-contacts-bigtable.png)

Pomocí SSH se můžete připojit k clusterům HBA a pak pomocí prostředí Apache HBA vytvořit tabulky HBA, vkládat data a dotazovat data.

1. Pomocí `ssh` příkazu se připojte ke clusteru HBA. Níže uvedený příkaz upravte nahrazením `CLUSTERNAME` názvem vašeho clusteru a zadáním příkazu:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. Pomocí `hbase shell` příkazu spusťte interaktivní prostředí pro adaptéry HBA. Do připojení SSH zadejte následující příkaz:

    ```bash
    hbase shell
    ```

3. Pomocí `create` příkazu vytvořte tabulku HBA se dvěma skupinami sloupců. Zadejte následující příkaz:

    ```hbase
    create 'Contacts', 'Personal', 'Office'
    ```

4. `list`K vypsání všech tabulek v adaptérech HBA použijte příkaz. Zadejte následující příkaz:

    ```hbase
    list
    ```

5. Použijte `put` příkaz pro vložení hodnot do zadaného sloupce v zadaném řádku v konkrétní tabulce. Zadejte následující příkaz:

    ```hbase
    put 'Contacts', '1000', 'Personal:Name', 'John Dole'
    put 'Contacts', '1000', 'Personal:Phone', '1-425-000-0001'
    put 'Contacts', '1000', 'Office:Phone', '1-425-000-0002'
    put 'Contacts', '1000', 'Office:Address', '1111 San Gabriel Dr.'
    ```

6. Pomocí `scan` příkazu naskenujte a vraťte `Contacts` data tabulky. Zadejte následující příkaz:

    ```hbase
    scan 'Contacts'
    ```

7. `get`K načtení obsahu řádku použijte příkaz. Zadejte následující příkaz:

    ```hbase
    get 'Contacts', '1000'
    ```

    Podobné výsledky se zobrazí při použití `scan` příkazu, protože existuje pouze jeden řádek.

8. Pomocí `delete` příkazu můžete odstranit hodnotu buňky v tabulce. Zadejte následující příkaz:

    ```hbase
    delete 'Contacts', '1000', 'Office:Address'
    ```

9. Pomocí `disable` příkazu zakažte tabulku. Zadejte následující příkaz:

    ```hbase
    disable 'Contacts'
    ```

10. `drop`K vyřazení tabulky z HBA použijte příkaz. Zadejte následující příkaz:

    ```hbase
    drop 'Contacts'
    ```

11. Pomocí `exit` příkazu zastavte prostředí HBA interaktivní prostředí. Zadejte následující příkaz:

    ```hbase
    exit
    ```

Další informace o schématu tabulky HBA najdete v tématu [Úvod do návrhu schématu Apache HBA](http://0b4af6cdc2f0c5998459-c0245c5c937c5dedcca3f1764ecc9b2f.r43.cf2.rackcdn.com/9353-login1210_khurana.pdf). Další příkazy HBase najdete v tématu [Referenční příručka Apache HBase](https://hbase.apache.org/book.html#quickstart).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Po dokončení rychlého startu možná budete chtít cluster odstranit. Pomocí HDInsight jsou vaše data uložena v Azure Storage, takže můžete clusteru bezpečně odstranit, pokud není používán. Za cluster služby HDInsight se účtují poplatky, i když se nepoužívá. Vzhledem k tomu, že poplatky za cluster představují několikanásobek poplatků za úložiště, dává ekonomický smysl odstraňovat clustery, které nejsou používány.

Pokud chcete odstranit cluster, přečtěte si téma [odstranění clusteru HDInsight pomocí prohlížeče, PowerShellu nebo rozhraní příkazového řádku Azure](../hdinsight-delete-cluster.md).

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste zjistili, jak pomocí prostředí Apache HBA vytvářet tabulky HBA, vkládat data a dotazovat se na tabulku. Další článek s informacemi uloženými v Hbach vám ukáže, jak spustit dotazy pomocí Apache Spark.

> [!div class="nextstepaction"]
> [Použití Apache Sparku ke čtení a zápisu dat Apache HBase](../hdinsight-using-spark-query-hbase.md)