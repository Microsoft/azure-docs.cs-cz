---
title: 'Rychlý start: Dotaz Apache HBase v Azure HDInsight - HBase Shell'
description: V tomto rychlém startu se dozvíte, jak pomocí prostředí Apache HBase spouštět dotazy Apache HBase.
keywords: hdinsight hadoop, HBase
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: quickstart
ms.date: 06/12/2019
ms.author: hrasheed
ms.openlocfilehash: d937e090895a5b02026c755b1efb7dd1e0b35000
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67054297"
---
# <a name="quickstart-query-apache-hbase-in-azure-hdinsight-with-hbase-shell"></a>Rychlý start: Dotaz Apache HBase v Azure HDInsight pomocí prostředí HBase

V tomto rychlém startu se dozvíte, jak pomocí prostředí Apache HBase vytvoření tabulky HBase, vkládání dat a potom zadejte dotaz tabulku.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

* Clustery Apache HBase. Zobrazit [vytvořit cluster](../hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster) k vytvoření clusteru HDInsight.  Zajistěte si vybrat **HBase** typ clusteru.

* Klient SSH. Další informace najdete v tématu [připojení k HDInsight (Apache Hadoop) pomocí protokolu SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="create-a-table-and-manipulate-data"></a>Vytvoření tabulky a pracovat s daty

Pro většinu osob se data zobrazí v tabulkovém formátu:

![Tabulková data HDInsight HBase](./media/query-hbase-with-hbase-shell/hdinsight-hbase-contacts-tabular.png)

V HBase (implementace [cloudu BigTable](https://cloud.google.com/bigtable/)), vypadají stejná data následovně:

![Velké objemy tabulkových dat HDInsight HBase](./media/query-hbase-with-hbase-shell/hdinsight-hbase-contacts-bigtable.png)

Můžete použít SSH pro připojení ke clusterům HBase a používání prostředí Apache HBase k vytváření tabulek HBase, vkládání dat a dotazování na data.

1. Použití `ssh` příkazu se připojte ke svému clusteru HBase. Upravte následující příkaz tak, že nahradíte `CLUSTERNAME` s názvem vašeho clusteru a potom zadejte příkaz:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. Použití `hbase shell` příkaz ke spuštění interaktivního prostředí HBase. Zadejte následující příkaz v připojení SSH:

    ```bash
    hbase shell
    ```

3. Použití `create` příkaz pro vytvoření tabulky HBase se dvěma skupinami sloupců. Zadejte následující příkaz:

    ```hbase
    create 'Contacts', 'Personal', 'Office'
    ```

4. Použití `list` příkazu zobrazte výpis všech tabulek v HBase. Zadejte následující příkaz:

    ```hbase
    list
    ```

5. Použití `put` příkaz pro vložení hodnot v zadaném sloupci v zadaný řádek v určité tabulce. Zadejte následující příkaz:

    ```hbase
    put 'Contacts', '1000', 'Personal:Name', 'John Dole'
    put 'Contacts', '1000', 'Personal:Phone', '1-425-000-0001'
    put 'Contacts', '1000', 'Office:Phone', '1-425-000-0002'
    put 'Contacts', '1000', 'Office:Address', '1111 San Gabriel Dr.'
    ```

6. Použití `scan` příkaz kontrolovat a vrátíte se `Contacts` dat tabulky. Zadejte následující příkaz:

    ```hbase
    scan 'Contacts'
    ```

7. Použití `get` příkaz k načtení obsahu řádku. Zadejte následující příkaz:

    ```hbase
    get 'Contacts', '1000'
    ```

    Zobrazit podobné výsledky jako pomocí `scan` příkaz, protože existuje pouze jeden řádek.

8. Použití `delete` příkazu k odstranění hodnota buňky v tabulce. Zadejte následující příkaz:

    ```hbase
    delete 'Contacts', '1000', 'Office:Address'
    ```

9. Použití `disable` příkazu zakažte v tabulce. Zadejte následující příkaz:

    ```hbase
    disable 'Contacts'
    ```

10. Použití `drop` příkaz pro odpojení tabulku z HBase. Zadejte následující příkaz:

    ```hbase
    drop 'Contacts'
    ```

11. Použití `exit` příkaz k zastavení interaktivní prostředí HBase. Zadejte následující příkaz:

    ```hbase
    exit
    ```

Další informace o schématu tabulky HBase najdete v tématu [Úvod do navrhování schémat HBase Apache](http://0b4af6cdc2f0c5998459-c0245c5c937c5dedcca3f1764ecc9b2f.r43.cf2.rackcdn.com/9353-login1210_khurana.pdf). Další příkazy HBase najdete v tématu [referenční příručka Apache HBase](https://hbase.apache.org/book.html#quickstart).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Po dokončení tohoto rychlého startu, můžete cluster odstranit. Pomocí HDInsight jsou vaše data uložena v Azure Storage, takže můžete clusteru bezpečně odstranit, pokud není používán. Za cluster služby HDInsight se účtují poplatky, i když se nepoužívá. Vzhledem k tomu, že poplatky za cluster představují několikanásobek poplatků za úložiště, dává ekonomický smysl odstraňovat clustery, které nejsou používány.

Odstranění clusteru, naleznete v tématu [odstranění clusteru HDInsight pomocí prohlížeče, Powershellu nebo rozhraní příkazového řádku Azure](../hdinsight-delete-cluster.md).

## <a name="next-steps"></a>Další postup

V tomto rychlém startu jste zjistili, jak pomocí prostředí Apache HBase k vytvoření tabulky HBase, vkládání dat a potom zadejte dotaz tabulku. Další informace o datech uložených v HBase, dalším článku se seznámíte se způsobem spouštění dotazů s Apache Sparkem.

> [!div class="nextstepaction"]
> [Použití Apache Sparku ke čtení a zápisu dat Apache HBase](../hdinsight-using-spark-query-hbase.md)