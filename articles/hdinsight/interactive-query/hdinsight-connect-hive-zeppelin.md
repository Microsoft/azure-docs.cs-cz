---
title: 'Rychlý Start: Apache Hive ve službě Azure HDInsight s Apache Zeppelin'
description: V tomto rychlém startu se dozvíte, jak používat Apache Zeppelin ke spouštění dotazů Apache Hive.
ms.service: hdinsight
ms.topic: quickstart
ms.custom: hdinsightactive
ms.date: 12/03/2019
ms.openlocfilehash: 1199fee82532eacf1f0ef41b877b970d7d9f2cf5
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2021
ms.locfileid: "98941059"
---
# <a name="quickstart-execute-apache-hive-queries-in-azure-hdinsight-with-apache-zeppelin"></a>Rychlý Start: spuštění dotazů Apache Hive ve službě Azure HDInsight s Apache Zeppelin

V tomto rychlém startu se naučíte používat Apache Zeppelin ke spouštění dotazů [Apache Hive](https://hive.apache.org/) ve službě Azure HDInsight. Clustery HDInsight s interaktivním dotazem obsahují notebooky [Apache Zeppelin](https://zeppelin.apache.org/) , které můžete použít ke spouštění interaktivních dotazů na podregistry.

Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Požadavky

An HDInsight clusteru interaktivních dotazů. V tématu [Vytvoření clusteru](../hadoop/apache-hadoop-linux-tutorial-get-started.md) vytvořte cluster HDInsight.  Nezapomeňte zvolit typ clusteru **interaktivní dotaz** .

## <a name="create-an-apache-zeppelin-note"></a>Vytvoření poznámky Apache Zeppelin

1. Nahraďte `CLUSTERNAME` názvem vašeho clusteru v následující adrese URL `https://CLUSTERNAME.azurehdinsight.net/zeppelin` . Pak zadejte adresu URL do webového prohlížeče.

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

### <a name="additional-examples"></a>Další příklady

1. Vytvořte tabulku. V poznámkovém bloku Zeppelin spusťte následující kód:

    ```hql
    %jdbc(hive)
    CREATE EXTERNAL TABLE log4jLogs (
        t1 string,
        t2 string,
        t3 string,
        t4 string,
        t5 string,
        t6 string,
        t7 string)
    ROW FORMAT DELIMITED
    FIELDS TERMINATED BY ' '
    STORED AS TEXTFILE;
    ```

1. Načte data do nové tabulky. V poznámkovém bloku Zeppelin spusťte následující kód:

    ```hql
    %jdbc(hive)
    LOAD DATA
    INPATH 'wasbs:///example/data/sample.log'
    INTO TABLE log4jLogs;
    ```

1. Vložte jeden záznam. V poznámkovém bloku Zeppelin spusťte následující kód:

    ```hql
    %jdbc(hive)
    INSERT INTO TABLE log4jLogs2
    VALUES ('A', 'B', 'C', 'D', 'E', 'F', 'G');
    ```

Další syntaxi najdete v [příručce k jazyku podregistru](https://cwiki.apache.org/confluence/display/Hive/LanguageManual) .

## <a name="clean-up-resources"></a>Vyčištění prostředků

Po dokončení rychlého startu možná budete chtít cluster odstranit. Ve službě HDInsight jsou vaše data uložená v Azure Storage, takže můžete cluster bezpečně odstranit, pokud se nepoužívá. Účtují se vám také poplatky za cluster HDInsight, a to i v případě, že se už nepoužívá. Vzhledem k tomu, že se poplatky za cluster mnohokrát účtují rychleji než poplatky za úložiště, má ekonomický smysl odstraňovat clustery, když se nepoužívají.

Pokud chcete odstranit cluster, přečtěte si téma [odstranění clusteru HDInsight pomocí prohlížeče, PowerShellu nebo rozhraní příkazového řádku Azure](../hdinsight-delete-cluster.md).

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste zjistili, jak používat Apache Zeppelin ke spouštění dotazů Apache Hive ve službě Azure HDInsight. Další informace o dotazech na podregistr najdete v dalším článku, jak spustit dotazy pomocí sady Visual Studio.

> [!div class="nextstepaction"]
> [Připojení ke službě Azure HDInsight a spouštění dotazů Apache Hive pomocí nástrojů Data Lake pro Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)
