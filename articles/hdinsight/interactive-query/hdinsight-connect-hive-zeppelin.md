---
title: 'Rychlý Start: Apache Hive ve službě Azure HDInsight s Apache Zeppelin'
description: V tomto rychlém startu se dozvíte, jak používat Apache Zeppelin ke spouštění dotazů Apache Hive.
keywords: HDInsight, Hadoop, podregistr, interaktivní dotaz, LLAP
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: quickstart
ms.date: 05/06/2019
ms.author: hrasheed
ms.openlocfilehash: 36d9e9b34deb4bc6cd5f599cfe2d09a12f680730
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73494299"
---
# <a name="quickstart-execute-apache-hive-queries-in-azure-hdinsight-with-apache-zeppelin"></a>Rychlý Start: spuštění dotazů Apache Hive ve službě Azure HDInsight s Apache Zeppelin

V tomto rychlém startu se naučíte používat Apache Zeppelin ke spouštění dotazů [Apache Hive](https://hive.apache.org/) ve službě Azure HDInsight. Clustery HDInsight s interaktivním dotazem obsahují notebooky [Apache Zeppelin](https://zeppelin.apache.org/) , které můžete použít ke spouštění interaktivních dotazů na podregistry.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Předpoklady

An HDInsight clusteru interaktivních dotazů. V tématu [Vytvoření clusteru](../hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster) vytvořte cluster HDInsight.  Nezapomeňte zvolit typ clusteru **interaktivní dotaz** .

## <a name="create-an-apache-zeppelin-note"></a>Vytvoření poznámky Apache Zeppelin

1. Nahraďte `CLUSTERNAME` názvem vašeho clusteru v následující adrese URL `https://CLUSTERNAME.azurehdinsight.net/zeppelin`. Pak zadejte adresu URL do webového prohlížeče.

2. Zadejte své uživatelské jméno a heslo pro přihlášení ke clusteru. Na stránce Zeppelin můžete buď vytvořit novou poznámku, nebo otevřít existující poznámky. **HiveSample** obsahuje několik ukázkových dotazů na podregistr.  

    ![Zeppelin interaktivní dotazy HDInsight](./media/hdinsight-connect-hive-zeppelin/hdinsight-hive-zeppelin.png)

3. Vyberte **vytvořit novou poznámku**.

4. V dialogovém okně **vytvořit novou poznámku** zadejte nebo vyberte následující hodnoty:

    - Poznámka název: zadejte název poznámky.
    - Výchozí Interpret: v rozevíracím seznamu vyberte **JDBC** .

5. Vyberte **vytvořit poznámku**.

6. Do části Code (kód) zadejte následující dotaz na podregistr a stiskněte klávesu **SHIFT + ENTER**:

    ```hive
    %jdbc(hive)
    show tables
    ```

    ![Dotaz Zeppelin běžící na HDInsight Interactive](./media/hdinsight-connect-hive-zeppelin/hdinsight-hive-zeppelin-query.png)

    Příkaz **% JDBC (podregistr)** v prvním řádku instruuje Poznámkový blok, aby použil překladač podregistru JDBC.

    Dotaz vrátí jednu tabulku podregistru s názvem **hivesampletable**.

    Níže jsou uvedené dva další dotazy na podregistry, které můžete spustit proti **hivesampletable**:

    ```hive
    %jdbc(hive)
    select * from hivesampletable limit 10

    %jdbc(hive)
    select ${group_name}, count(*) as total_count
    from hivesampletable
    group by ${group_name=market,market|deviceplatform|devicemake}
    limit ${total_count=10}
    ```

    V porovnání s tradičním podregistrem se výsledky dotazu vrátí rychleji.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Po dokončení rychlého startu možná budete chtít cluster odstranit. Pomocí HDInsight jsou vaše data uložena v Azure Storage, takže můžete clusteru bezpečně odstranit, pokud není používán. Za cluster služby HDInsight se účtují poplatky, i když se nepoužívá. Vzhledem k tomu, že poplatky za cluster představují několikanásobek poplatků za úložiště, dává ekonomický smysl odstraňovat clustery, které nejsou používány.

Pokud chcete odstranit cluster, přečtěte si téma [odstranění clusteru HDInsight pomocí prohlížeče, PowerShellu nebo rozhraní příkazového řádku Azure](../hdinsight-delete-cluster.md).

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste zjistili, jak používat Apache Zeppelin ke spouštění dotazů Apache Hive ve službě Azure HDInsight. Další informace o dotazech na podregistr najdete v dalším článku, jak spustit dotazy pomocí sady Visual Studio.

> [!div class="nextstepaction"]
> [Připojení ke službě Azure HDInsight a spouštění dotazů Apache Hive pomocí nástrojů Data Lake pro Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)
