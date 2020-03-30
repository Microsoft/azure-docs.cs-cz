---
title: 'Úvodní příručka: Apache Hive v Azure HDInsight s Apache Zeppelin'
description: V tomto rychlém startu se dozvíte, jak pomocí Apache Zeppelin spustit dotazy Apache Hive.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: quickstart
ms.custom: hdinsightactive
ms.date: 12/03/2019
ms.openlocfilehash: 49b576fd511d17616880e5d981fd3f649de797df
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "79367920"
---
# <a name="quickstart-execute-apache-hive-queries-in-azure-hdinsight-with-apache-zeppelin"></a>Úvodní příručka: Spouštění dotazů Apache Hive v Azure HDInsight pomocí Apache Zeppelin

V tomto rychlém startu se dozvíte, jak pomocí Apache Zeppelin spouštět dotazy [Apache Hive](https://hive.apache.org/) v Azure HDInsight. Clustery interaktivních dotazů HDInsight zahrnují poznámkové bloky [Apache Zeppelin,](https://zeppelin.apache.org/) které můžete použít ke spuštění interaktivních dotazů Hive.

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

## <a name="prerequisites"></a>Požadavky

Cluster interaktivních dotazů HDInsight. Viz [Vytvoření clusteru](../hadoop/apache-hadoop-linux-tutorial-get-started.md) a vytvoření clusteru HDInsight.  Nezapomeňte zvolit typ clusteru **interaktivních dotazů.**

## <a name="create-an-apache-zeppelin-note"></a>Vytvoření poznámky Apache Zeppelin

1. Nahraďte `CLUSTERNAME` název clusteru v `https://CLUSTERNAME.azurehdinsight.net/zeppelin`následující adrese URL . Poté zadejte adresu URL do webového prohlížeče.

2. Zadejte své přihlašovací uživatelské jméno a heslo pro přihlášení k clusteru. Na stránce Zeppelin můžete buď vytvořit novou poznámku, nebo otevřít existující poznámky. **HiveSample** obsahuje některé ukázkové hive dotazy.  

    ![HDInsight Interaktivní dotaz zeppelin](./media/hdinsight-connect-hive-zeppelin/hdinsight-hive-zeppelin.png)

3. Vyberte **Vytvořit novou poznámku**.

4. V dialogovém okně **Vytvořit novou poznámku** zadejte nebo vyberte následující hodnoty:

    - Poznámka název: Zadejte název poznámky.
    - Výchozí překladač: V rozevíracím seznamu vyberte **jdbc.**

5. Vyberte **Vytvořit poznámku**.

6. Do oddílu kódu zadejte následující dotaz Hive a stiskněte **Shift + Enter**:

    ```hive
    %jdbc(hive)
    show tables
    ```

    ![HDInsight Interaktivní dotaz zeppelin spustí dotaz](./media/hdinsight-connect-hive-zeppelin/hdinsight-hive-zeppelin-query.png)

    Příkaz **%jdbc(hive)** v prvním řádku informuje poznámkový blok, aby používal interpret JDBC Hive.

    Dotaz vrátí jednu tabulku Hive s názvem **hivesampletable**.

    Následují dva další dotazy Hive, které můžete spustit proti **hivesampletable**:

    ```hive
    %jdbc(hive)
    select * from hivesampletable limit 10

    %jdbc(hive)
    select ${group_name}, count(*) as total_count
    from hivesampletable
    group by ${group_name=market,market|deviceplatform|devicemake}
    limit ${total_count=10}
    ```

    Ve srovnání s tradiční Hive, výsledky dotazu vrátit musí být rychlejší.

### <a name="additional-examples"></a>Další příklady

1. Vytvořte tabulku. Spusťte níže uvedený kód v notebooku Zeppelin:

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

1. Načtěte data do nové tabulky. Spusťte níže uvedený kód v notebooku Zeppelin:

    ```hql
    %jdbc(hive)
    LOAD DATA
    INPATH 'wasbs:///example/data/sample.log'
    INTO TABLE log4jLogs;
    ```

1. Vložte jeden záznam. Spusťte níže uvedený kód v notebooku Zeppelin:

    ```hql
    %jdbc(hive)
    INSERT INTO TABLE log4jLogs2
    VALUES ('A', 'B', 'C', 'D', 'E', 'F', 'G');
    ```

Další syntaxi naleznete v [příručce jazyka Hive.](https://cwiki.apache.org/confluence/display/Hive/LanguageManual)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Po dokončení rychlého startu můžete cluster odstranit. S HDInsight, vaše data jsou uloženy ve službě Azure Storage, takže můžete bezpečně odstranit clusteru, když není v provozu. Účtuje se vám také cluster HDInsight, i když se nepoužívá. Vzhledem k tomu, že poplatky za cluster jsou mnohonásobně vyšší než poplatky za úložiště, má ekonomické smysl odstranit clustery, když nejsou používány.

Pokud chcete odstranit cluster, přečtěte si informace [o odstranění clusteru HDInsight pomocí prohlížeče, PowerShellu nebo rozhraní příkazového příkazu k Azure](../hdinsight-delete-cluster.md).

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste se naučili používat Apache Zeppelin ke spouštění dotazů Apache Hive v Azure HDInsight. Další informace o dotazech Hive, další článek vám ukáže, jak provádět dotazy s Visual Studio.

> [!div class="nextstepaction"]
> [Připojení k Azure HDInsight a spouštění dotazů Apache Hive pomocí nástrojů data lake pro Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)
