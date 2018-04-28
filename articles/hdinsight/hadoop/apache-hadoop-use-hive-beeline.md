---
title: Použití Beeline s Apache Hive - Azure HDInsight | Microsoft Docs
description: Další informace o použití Beeline klienta ke spouštění dotazů Hive se systémem Hadoop v HDInsight. Beeline je nástroj pro práci s HiveServer2 prostřednictvím JDBC.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
keywords: beeline hive, hive beeline
ms.assetid: 3adfb1ba-8924-4a13-98db-10a67ab24fca
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: na
ms.topic: conceptual
ms.date: 04/20/2018
ms.author: larryfr
ms.openlocfilehash: 970ccf19b5668bd57118fcabc5018c60352ebde7
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2018
---
# <a name="use-the-beeline-client-with-apache-hive"></a>Pomocí klienta Beeline s Apache Hive

Další informace o použití [Beeline](https://cwiki.apache.org/confluence/display/Hive/HiveServer2+Clients#HiveServer2Clients-Beeline–NewCommandLineShell) ke spouštění dotazů Hive v HDInsight.

Beeline je klient Hive, který je zahrnutý o hlavních uzlech clusteru HDInsight. Beeline JDBC používá pro připojení k HiveServer2, službě hostované na clusteru HDInsight. Můžete taky Beeline pro přístup k Hive v HDInsight vzdáleně přes internet. Následující příklady obsahují nejběžnější připojovací řetězce, použít pro připojení k HDInsight z Beeline:

* __Pomocí Beeline z připojení SSH headnode nebo hraniční uzel__: `-u 'jdbc:hive2://headnodehost:10001/;transportMode=http'`
* __V klientovi připojení k HDInsight přes virtuální síť Azure pomocí Beeline__: `-u 'jdbc:hive2://<headnode-FQDN>:10001/;transportMode=http'`
* __Pomocí Beeline v klientovi připojení k HDInsight prostřednictvím veřejného Internetu__: `-u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2' -n admin -p password`

> [!NOTE]
> Nahraďte `admin` s účtem přihlášení clusteru pro cluster.
>
> Nahraďte `password` pomocí hesla pro účet přihlášení clusteru.
>
> Parametr `clustername` nahraďte názvem vašeho clusteru HDInsight.
>
> Při připojování ke clusteru prostřednictvím virtuální síť, nahraďte `<headnode-FQDN>` s plně kvalifikovaný název domény clusteru headnode.

## <a id="prereq"></a>Požadavky

* Systémem Linux Hadoop na verzi clusteru HDInsight 3.4 nebo vyšší.

  > [!IMPORTANT]
  > HDInsight od verze 3.4 výše používá výhradně operační systém Linux. Další informace najdete v tématu [Vyřazení prostředí HDInsight ve Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

* Klientem SSH nebo místní Beeline klienta. Většina kroky v tomto dokumentu předpokládají, že používáte Beeline z relace SSH do clusteru. Informace o spouštění Beeline z mimo cluster najdete v tématu [vzdáleně pomocí Beeline](#remote) části.

    Další informace o používání SSH najdete v tématu [použití SSH s HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a id="beeline"></a>Spouštění dotazů Hive

1. Při spouštění Beeline, musí zadat připojovací řetězec pro HiveServer2 v clusteru HDInsight:

    * Při připojování prostřednictvím veřejného Internetu, je nutné zadat název clusteru přihlašovací účet (výchozí `admin`) a heslo. Například pomocí Beeline ze systému klienta pro připojení k `<clustername>.azurehdinsight.net` adresu. Toto připojení se provádí přes port `443`a je zašifrovaná pomocí SSL:

        ```bash
        beeline -u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2' -n admin -p password
        ```

    * Při připojování z relace SSH do clusteru headnode, se můžete připojit k `headnodehost` adresu pro port `10001`:

        ```bash
        beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
        ```

    * Při připojování přes virtuální síť Azure, je nutné zadat plně kvalifikovaný název domény (FQDN) hlavního uzlu clusteru. Vzhledem k tomu, že toto připojení se provádí přímo na uzlech clusteru, připojení používá port `10001`:

        ```bash
        beeline -u 'jdbc:hive2://<headnode-FQDN>:10001/;transportMode=http'
        ```

2. Příkazy beeline začínat `!` znak, třeba `!help` zobrazí nápovědu. Ale `!` lze vynechat pro některé příkazy. Například `help` funguje taky.

    Došlo `!sql`, který se používá k provedení příkazy HiveQL. HiveQL se ale tak běžně používá, můžete vynechat předchozím `!sql`. Odpovídají následující dva příkazy:

    ```hiveql
    !sql show tables;
    show tables;
    ```

    Na novém clusteru, je uvedena pouze jednu tabulku: **hivesampletable**.

3. Pomocí následujícího příkazu můžete zobrazit schéma pro hivesampletable:

    ```hiveql
    describe hivesampletable;
    ```

    Tento příkaz vrátí následující informace:

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

    Tyto informace popisují sloupců v tabulce.

4. Zadejte následující příkazy a vytvořte tabulku s názvem **log4jLogs** pomocí ukázkových dat, které jsou součástí clusteru HDInsight:

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
    STORED AS TEXTFILE LOCATION 'wasb:///example/data/';
    SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs 
        WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' 
        GROUP BY t4;
    ```

    Tyto příkazy provádět následující akce:

    * `DROP TABLE` – Pokud je v tabulce existuje, je odstraněn.

    * `CREATE EXTERNAL TABLE` -Vytvoří **externí** tabulky v Hive. Externí tabulky pouze uložit definici tabulky Hive. Data je ponechán v původním umístění.

    * `ROW FORMAT` -Způsob formátování data. V takovém případě polí v každém protokolu jsou oddělené mezerou.

    * `STORED AS TEXTFILE LOCATION` -Tam, kde jsou data uložena a jaký formát souboru.

    * `SELECT` – Počet všech řádků vybere kde sloupec **t4** obsahuje hodnotu **[Chyba]**. Tento dotaz vrací hodnotu **3** jsou tři řádky, které obsahují tuto hodnotu.

    * `INPUT__FILE__NAME LIKE '%.log'` -Hive se pokusí použít schéma pro všechny soubory v adresáři. V takovém případě adresáře obsahuje soubory, které neodpovídají žádné schéma. Aby se zabránilo paměti data ve výsledcích, tento příkaz informuje Hive, že má být vrácen pouze data ze souborů končící na. log.

  > [!NOTE]
  > Externí tabulky by měl být použit při očekáváte, že v základních datech aktualizovat externího zdroje. Například procesu nahrávání automatizované dat nebo operace MapReduce.
  >
  > Vyřazení externí tabulku nemá **není** odstranit data, jenom definici tabulky.

    Výstup tohoto příkazu je podobná následující text:

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

5. Chcete-li ukončit Beeline, použijte `!exit`.

### <a id="file"></a>Spusťte soubor HiveQL pomocí Beeline

Použijte následující postup k vytvoření souboru a potom spustit pomocí Beeline.

1. Pomocí následujícího příkazu vytvořte soubor s názvem **query.hql**:

    ```bash
    nano query.hql
    ```

2. Použijte následující text jako obsah souboru. Tento dotaz vytvoří novou tabulku "interní" s názvem **errorLogs**:

    ```hiveql
    CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
    INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';
    ```

    Tyto příkazy provádět následující akce:

    * **Vytvoření tabulka není v případě existuje** – Pokud tabulky již neexistuje, vytvoří se. Vzhledem k tomu **externí** – klíčové slovo není, tento příkaz vytvoří interní tabulku. Interní tabulky jsou uložena v datovém skladu Hive a jsou zcela spravovány Hive.
    * **ULOŽENÉ ORC AS** – ukládá data ve formátu optimalizované řádek sloupcovém (ORC). Formát ORC je vysoce optimalizovaný a efektivní formát pro ukládání dat Hive.
    * **PŘEPSAT INSERT... Vyberte** -vybere řádky z **log4jLogs** tabulku, která obsahují **[Chyba]**, pak vkládá data do **errorLogs** tabulky.

    > [!NOTE]
    > Na rozdíl od externích tabulek se odstraní vyřazení interní tabulku v základních datech.

3. Chcete-li uložit soubor, použijte **Ctrl**+**_X**, pak zadejte **Y**a v neposlední řadě **Enter**.

4. Ke spuštění souboru pomocí Beeline použijte následující:

    ```bash
    beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http' -i query.hql
    ```

    > [!NOTE]
    > `-i` Parametr spustí Beeline a spouští příkazy v `query.hql` souboru. Po dokončení dotazu, které přicházejí na `jdbc:hive2://headnodehost:10001/>` řádku. Můžete taky spustit soubor pomocí `-f` parametr, který ukončí Beeline po dokončení dotazu.

5. Ověřit, jestli **errorLogs** tabulka byla vytvořena, použijte následující příkaz a vrátí všechny řádky z **errorLogs**:

    ```hiveql
    SELECT * from errorLogs;
    ```

    Tří řádků dat má být vrácen, všechny obsahující **[Chyba]** v t4 sloupce:

        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        | errorlogs.t1  | errorlogs.t2  | errorlogs.t3  | errorlogs.t4  | errorlogs.t5  | errorlogs.t6  | errorlogs.t7  |
        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        | 2012-02-03    | 18:35:34      | SampleClass0  | [ERROR]       | incorrect     | id            |               |
        | 2012-02-03    | 18:55:54      | SampleClass1  | [ERROR]       | incorrect     | id            |               |
        | 2012-02-03    | 19:25:27      | SampleClass4  | [ERROR]       | incorrect     | id            |               |
        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        3 rows selected (1.538 seconds)

## <a id="remote"></a>Použít Beeline vzdáleně

Pokud máte Beeline nainstalovány místně a připojit prostřednictvím veřejného Internetu, použijte následující parametry:

* __Připojovací řetězec__: `-u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2'`

* __Přihlašovací jméno clusteru__: `-n admin`

* __Heslo pro přihlášení clusteru__ `-p 'password'`

Nahraďte `clustername` v připojovacím řetězci s názvem clusteru HDInsight.

Nahraďte `admin` s názvem vašeho clusteru přihlášení a nahradit `password` heslem pro váš cluster přihlášení.

Pokud máte Beeline nainstalovány místně a připojení přes virtuální síť Azure, použijte následující parametry:

* __Připojovací řetězec__: `-u 'jdbc:hive2://<headnode-FQDN>:10001/;transportMode=http'`

Najít plně kvalifikovaný název domény headnode, použijte informace v [spravovat HDInsight pomocí Ambari REST API](../hdinsight-hadoop-manage-ambari-rest-api.md#example-get-the-fqdn-of-cluster-nodes) dokumentu.

## <a id="sparksql"></a>Beeline pomocí Spark

Spark poskytuje svou vlastní implementaci systému HiveServer2, která se někdy označuje jako serveru Spark Thrift. Tato služba používá Spark SQL při překladu místo Hive a může poskytovat lepší výkon v závislosti na svůj dotaz.

__Připojovací řetězec__ použít při připojení přes internet se mírně liší. Místo obsahující `httpPath=/hive2` je `httpPath/sparkhive2`. Následuje příklad připojení přes internet:

```bash 
beeline -u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/sparkhive2' -n admin -p password
```

Pokud se připojujete přímo z hlavního uzlu clusteru, nebo z prostředků ve stejné virtuální síti Azure jako HDInsight cluster, port `10002` se mají použít pro serveru Spark Thrift místo `10001`. Následuje příklad připojovat přímo k hlavnímu uzlu:

```bash
beeline -u 'jdbc:hive2://headnodehost:10002/;transportMode=http'
```

## <a id="summary"></a><a id="nextsteps"></a>Další kroky

Další obecné informace o Hive v HDInsight najdete v následujícím dokumentu:

* [Použijte Hive s Hadoop v HDInsight](hdinsight-use-hive.md)

Další informace o jiných způsobech, že pracujete s Hadoop v HDInsight najdete v následujících dokumentech:

* [Použijte Pig s Hadoop v HDInsight](hdinsight-use-pig.md)
* [Používání nástroje MapReduce s Hadoop v HDInsight](hdinsight-use-mapreduce.md)

Pokud používáte s Hive Tez, najdete v následujících dokumentech:

* [Pomocí uživatelského rozhraní Tez na HDInsight se systémem Windows](../hdinsight-debug-tez-ui.md)
* [Použití zobrazení Ambari Tez na HDInsight se systémem Linux](../hdinsight-debug-ambari-tez-view.md)

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/library/dn479185.aspx

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[apache-tez]: http://tez.apache.org
[apache-hive]: http://hive.apache.org/
[apache-log4j]: http://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: http://azure.microsoft.com/documentation/articles/hdinsight-connect-excel-power-query/


[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md

[putty]: http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html


[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-submit-jobs]:submit-apache-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md


[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx
