---
title: 'Rychlý start: Dotazy Apache Hive spustit v Azure HDInsight – Apache Zeppelin'
description: V tomto rychlém startu se dozvíte, jak k použití Apache Zeppelinu ke spuštění dotazy Apache Hive.
keywords: hdinsight hadoop, hive, interactive query, LLAP
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: quickstart
ms.date: 05/06/2019
ms.author: hrasheed
ms.openlocfilehash: 1642c64b0b14c2e290aad689399b59d896660a28
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67056695"
---
# <a name="quickstart-execute-apache-hive-queries-in-azure-hdinsight-with-apache-zeppelin"></a>Rychlý start: Dotazy Apache Hive spustit v Azure HDInsight s Apache Zeppelin

V tomto rychlém startu se dozvíte, jak k použití Apache Zeppelinu ke spuštění [Apache Hive](https://hive.apache.org/) dotazy v Azure HDInsight. Clustery HDInsight Interactive Query zahrnují [Apache Zeppelin](https://zeppelin.apache.org/) poznámkových bloků, které můžete použít ke spuštění interaktivních dotazů Hive.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Cluster HDInsight Interactive Query. Zobrazit [vytvořit cluster](../hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster) k vytvoření clusteru HDInsight.  Nezapomeňte vybrat **Interactive Query** typ clusteru.

## <a name="create-an-apache-zeppelin-note"></a>Vytvoření Zeppelinu poznámky Apache

1. Nahraďte `CLUSTERNAME` s názvem vašeho clusteru v následující adrese URL `https://CLUSTERNAME.azurehdinsight.net/zeppelin`. Potom zadejte adresu URL ve webovém prohlížeči.

2. Zadejte svoje uživatelské jméno přihlášení clusteru a heslo. Na stránce Zeppelin můžete vytvoří novou poznámku. nebo otevřete stávající poznámky. **HiveSample** obsahuje několik ukázkových dotazů nástroje Hive.  

    ![HDInsight Interactive Query zeppelin](./media/hdinsight-connect-hive-zeppelin/hdinsight-hive-zeppelin.png)

3. Vyberte **vytvořit nová poznámka**.

4. Z **vytvořit nová poznámka** dialogové okno, zadejte nebo vyberte následující hodnoty:

    - Poznámka: název: Zadejte název pro poznámku.
    - Výchozí překladač: Vyberte **jdbc** z rozevíracího seznamu.

5. Vyberte **vytvořit poznámku**.

6. Zadejte následující dotaz Hive v části kódu a stiskněte klávesu **Shift + Enter**:

    ```hive
    %jdbc(hive)
    show tables
    ```

    ![HDInsight Interactive Query zeppelin spustí dotaz](./media/hdinsight-connect-hive-zeppelin/hdinsight-hive-zeppelin-query.png)

    **%Jdbc(hive)** příkaz na prvním řádku říká poznámkovému bloku určený interpret Hive JDBC.

    Dotaz se vrátí jeden tabulkou Hive se nazývají **hivesampletable**.

    Tady jsou dva další dotazy Hive, které je možné spustit proti **hivesampletable**:

    ```hive
    %jdbc(hive)
    select * from hivesampletable limit 10

    %jdbc(hive)
    select ${group_name}, count(*) as total_count
    from hivesampletable
    group by ${group_name=market,market|deviceplatform|devicemake}
    limit ${total_count=10}
    ```

    Porovnání s tradičním Hive, výsledky dotazu vrátit musí rychleji.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Po dokončení tohoto rychlého startu, můžete cluster odstranit. Pomocí HDInsight jsou vaše data uložena v Azure Storage, takže můžete clusteru bezpečně odstranit, pokud není používán. Za cluster služby HDInsight se účtují poplatky, i když se nepoužívá. Vzhledem k tomu, že poplatky za cluster představují několikanásobek poplatků za úložiště, dává ekonomický smysl odstraňovat clustery, které nejsou používány.

Odstranění clusteru, naleznete v tématu [odstranění clusteru HDInsight pomocí prohlížeče, Powershellu nebo rozhraní příkazového řádku Azure](../hdinsight-delete-cluster.md).

## <a name="next-steps"></a>Další postup

V tomto rychlém startu jste zjistili, jak používat Apache Zeppelinu ke spouštění dotazů Apache Hive v Azure HDInsight. Další informace o dotazy Hive, dalším článku se ukazují, jak provádět dotazy pomocí sady Visual Studio.

> [!div class="nextstepaction"]
> [Připojení k Azure HDInsight a spouštět dotazy Apache Hive pomocí nástrojů Data Lake pro Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)
