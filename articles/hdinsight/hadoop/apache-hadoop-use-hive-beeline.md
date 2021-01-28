---
title: Použití Apache Beeline s Apache Hive – Azure HDInsight
description: Naučte se používat klienta Beeline ke spouštění dotazů na podregistr pomocí Hadoop v HDInsight. Beeline je nástroj pro práci s HiveServer2 nad JDBC.
ms.service: hdinsight
ms.topic: how-to
ms.date: 10/28/2020
ms.custom: contperf-fy21q1, contperf-fy21q2
ms.openlocfilehash: e8b7478ba64da0f99a9b7a710222ff2953795adf
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2021
ms.locfileid: "98943203"
---
# <a name="use-the-apache-beeline-client-with-apache-hive"></a>Použití klienta Apache Beeline s Apache Hivem

Tento článek popisuje, jak pomocí klienta [Apache Beeline](https://cwiki.apache.org/confluence/display/Hive/HiveServer2+Clients#HiveServer2Clients-Beeline–NewCommandLineShell) z příkazového řádku vytvářet a spouštět dotazy Apache Hive přes připojení SSH.

## <a name="background"></a>Pozadí

Beeline je klient podregistru, který je součástí hlavních uzlů clusteru HDInsight. Pokud se chcete připojit ke klientovi Beeline nainstalovanému v clusteru HDInsight nebo místně nainstalovat Beeline, přečtěte si článek [připojení k Apache Beeline nebo](connect-install-beeline.md)jeho instalace. Beeline používá JDBC pro připojení k HiveServer2, službě hostované v clusteru HDInsight. Beeline můžete použít také k vzdálenému přístupu k podregistru v HDInsight přes Internet. V následujících příkladech jsou uvedeny nejběžnější připojovací řetězce používané pro připojení ke službě HDInsight z Beeline.

## <a name="prerequisites-for-examples"></a>Předpoklady pro příklady

* Cluster Hadoop ve službě HDInsight. Viz Začínáme [se službou HDInsight v systému Linux](./apache-hadoop-linux-tutorial-get-started.md).

* Všimněte si schématu identifikátoru URI pro primární úložiště vašeho clusteru. Například  `wasb://` pro Azure Storage pro `abfs://` Azure Data Lake Storage Gen2 nebo `adl://` pro Azure Data Lake Storage Gen1. Pokud je pro Azure Storage povolený zabezpečený přenos, je identifikátor URI `wasbs://` . Další informace najdete v tématu [zabezpečený přenos](../../storage/common/storage-require-secure-transfer.md).

* Klient SSH. Další informace najdete v tématu [Připojení ke službě HDInsight (Apache Hadoop) pomocí SSH](../hdinsight-hadoop-linux-use-ssh-unix.md). Většina kroků v tomto dokumentu předpokládá, že používáte Beeline z relace SSH do clusteru. Můžete také použít místního klienta Beeline, ale tyto kroky nejsou zahrnuty v tomto článku.

## <a name="run-a-hive-query"></a>Spuštění dotazu Hive

Tento příklad je založený na použití klienta Beeline z připojení SSH.

1. Otevřete připojení SSH ke clusteru pomocí následujícího kódu. Místo `sshuser` použijte jméno uživatele SSH pro váš cluster a místo `CLUSTERNAME` zadejte název clusteru. Po zobrazení výzvy zadejte heslo pro uživatelský účet SSH.

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. Připojte se k HiveServer2 pomocí klienta Beeline z otevřené relace SSH zadáním následujícího příkazu:

    ```bash
    beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
    ```

3. Příkazy Beeline začínají `!` znakem, například `!help` zobrazí nápovědu. `!`U některých příkazů ale může být vynecháno. Například `help` funguje také.

    K dispozici `!sql` je, který se používá ke spouštění příkazů HiveQL. HiveQL je ale často používaný, takže můžete vynechat předchozí `!sql` . Následující dva příkazy jsou ekvivalentní:

    ```hiveql
    !sql show tables;
    show tables;
    ```

    V novém clusteru je uvedena pouze jedna tabulka: **hivesampletable**.

4. K zobrazení schématu pro hivesampletable použijte následující příkaz:

    ```hiveql
    describe hivesampletable;
    ```

    Tento příkaz vrátí následující informace:

    ```output
    +-----------------------+------------+----------+--+
    |       col_name        | data_type  | comment  |
    +-----------------------+------------+----------+--+
    | clientid              | string     |          |
    | querytime             | string     |          |
    | market                | string     |          |
    | deviceplatform        | string     |          |
    | devicemake            | string     |          |
    | devicemodel           | string     |          |
    | state                 | string     |          |
    | country               | string     |          |
    | querydwelltime        | double     |          |
    | sessionid             | bigint     |          |
    | sessionpagevieworder  | bigint     |          |
    +-----------------------+------------+----------+--+
    ```

    Tyto informace popisují sloupce v tabulce.

5. Zadáním následujících příkazů vytvořte tabulku s názvem **log4jLogs** pomocí ukázkových dat, která jsou součástí clusteru HDInsight: (podle potřeby podle schématu identifikátoru URI Proveďte revizi podle potřeby).

    ```hiveql
    DROP TABLE log4jLogs;
    CREATE EXTERNAL TABLE log4jLogs (
        t1 string,
        t2 string,
        t3 string,
        t4 string,
        t5 string,
        t6 string,
        t7 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
    STORED AS TEXTFILE LOCATION 'wasbs:///example/data/';
    SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs
        WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log'
        GROUP BY t4;
    ```

    Tyto příkazy provedou následující akce:

    |Příkaz |Popis |
    |---|---|
    |DROP TABLE|Pokud tabulka existuje, je odstraněna.|
    |VYTVOŘIT EXTERNÍ TABULKU|Vytvoří **externí** tabulku v podregistru. Externí tabulky ukládají pouze definici tabulky v podregistru. Data zůstanou v původním umístění.|
    |FORMÁT ŘÁDKU|Způsob formátování dat. V tomto případě jsou pole v každém protokolu oddělená mezerou.|
    |ULOŽENO JAKO UMÍSTĚNÍ TEXTFILE|Kde jsou data uložena a v jakém formátu souboru.|
    |SELECT|Vybere počet všech řádků, ve kterých sloupec **T4** obsahuje hodnotu **[Chyba]**. Tento dotaz vrátí hodnotu **3** , protože jsou tři řádky, které obsahují tuto hodnotu.|
    |INPUT__FILE__NAME jako je%. log|Podregistr se pokusí použít schéma pro všechny soubory v adresáři. V tomto případě adresář obsahuje soubory, které neodpovídají schématu. Aby se zabránilo uvolňování dat ve výsledcích, tento příkaz oznamuje podregistru, že by měl vracet pouze data ze souborů končících log. log.|

   > [!NOTE]  
   > Externí tabulky by měly být použity, pokud očekáváte, že budou zdrojová data aktualizována externím zdrojem. Například automatizovaný proces odesílání dat nebo operace MapReduce.
   >
   > Vyřazení externí tabulky **neodstraní data** , pouze definici tabulky.

    Výstup tohoto příkazu je podobný následujícímu textu:

    ```output
    INFO  : Tez session hasn't been created yet. Opening session
    INFO  :

    INFO  : Status: Running (Executing on YARN cluster with App id application_1443698635933_0001)

    INFO  : Map 1: -/-      Reducer 2: 0/1
    INFO  : Map 1: 0/1      Reducer 2: 0/1
    INFO  : Map 1: 0/1      Reducer 2: 0/1
    INFO  : Map 1: 0/1      Reducer 2: 0/1
    INFO  : Map 1: 0/1      Reducer 2: 0/1
    INFO  : Map 1: 0(+1)/1  Reducer 2: 0/1
    INFO  : Map 1: 0(+1)/1  Reducer 2: 0/1
    INFO  : Map 1: 1/1      Reducer 2: 0/1
    INFO  : Map 1: 1/1      Reducer 2: 0(+1)/1
    INFO  : Map 1: 1/1      Reducer 2: 1/1
    +----------+--------+--+
    |   sev    | count  |
    +----------+--------+--+
    | [ERROR]  | 3      |
    +----------+--------+--+
    1 row selected (47.351 seconds)
    ```

6. Konec Beeline:

    ```bash
    !exit
    ```

## <a name="run-a-hiveql-file"></a>Spuštění souboru HiveQL

Tento příklad je pokračování z předchozího příkladu. Pomocí následujících kroků vytvořte soubor a pak ho spusťte pomocí Beeline.

1. Pomocí následujícího příkazu vytvořte soubor s názvem **Query. HQL**:

    ```bash
    nano query.hql
    ```

1. Jako obsah souboru použijte následující text. Tento dotaz vytvoří novou interní **tabulku s názvem** protokolu chyb:

    ```hiveql
    CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
    INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';
    ```

    Tyto příkazy provedou následující akce:

    |Příkaz |Popis |
    |---|---|
    |CREATE TABLE, POKUD NEEXISTUJE|Pokud tabulka ještě neexistuje, vytvoří se. Vzhledem k tomu, že se klíčové slovo **External** nepoužívá, vytvoří tento příkaz interní tabulku. Interní tabulky jsou uložené v datovém skladu podregistru a jsou plně spravované podregistrem.|
    |ULOŽENO JAKO ORC|Ukládá data ve formátu optimalizovaného řádku (ORC). Formát ORC je vysoce optimalizovaný a efektivní formát pro ukládání dat z podregistru.|
    |VLOŽIT PŘEPSÁNÍ... VYBRALI|Vybere řádky z tabulky **log4jLogs** , které obsahují **[Error]**, a pak data vloží **do tabulky chyb** .|

    > [!NOTE]  
    > Na rozdíl od externích tabulek odstraní interní tabulka také podkladová data.

1. Pokud chcete soubor uložit, použijte **CTRL +** + , zadejte **Y** a nakonec **ENTER**.

1. K spuštění souboru pomocí Beeline použijte následující:

    ```bash
    beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http' -i query.hql
    ```

    > [!NOTE]  
    > `-i`Parametr spustí Beeline a spustí příkazy v `query.hql` souboru. Po dokončení dotazu se zobrazí `jdbc:hive2://headnodehost:10001/>` výzva. Můžete také spustit soubor pomocí `-f` parametru, který ukončí Beeline po dokončení dotazu.

1. Chcete-li ověřit, zda byla **vytvořena tabulka chyb** protokolu chyb, použijte následující příkaz, který vrátí všechny řádky z chyb protokolu **chyb:**

    ```hiveql
    SELECT * from errorLogs;
    ```

    Měly by se vracet tři řádky dat, všechny obsahující **[Error]** v sloupci T4:

    ```output
    +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
    | errorlogs.t1  | errorlogs.t2  | errorlogs.t3  | errorlogs.t4  | errorlogs.t5  | errorlogs.t6  | errorlogs.t7  |
    +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
    | 2012-02-03    | 18:35:34      | SampleClass0  | [ERROR]       | incorrect     | id            |               |
    | 2012-02-03    | 18:55:54      | SampleClass1  | [ERROR]       | incorrect     | id            |               |
    | 2012-02-03    | 19:25:27      | SampleClass4  | [ERROR]       | incorrect     | id            |               |
    +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
    3 rows selected (0.813 seconds)
    ```

## <a name="next-steps"></a>Další kroky

* Obecnější informace o podregistru v HDInsight najdete v tématu [použití Apache Hive s Apache Hadoop v HDInsight](hdinsight-use-hive.md) .

* Další informace o dalších způsobech práce se systémem Hadoop ve službě HDInsight najdete v tématu [použití MapReduce s Apache Hadoop v HDInsight](hdinsight-use-mapreduce.md) .
