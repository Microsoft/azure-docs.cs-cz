---
title: Použití Apache Beeline s Apache Hive – Azure HDInsight
description: Další informace o použití Beeline klienta ke spouštění dotazů Hive se systémem Hadoop v HDInsight. Beeline je nástroj pro práci s HiveServer2 prostřednictvím JDBC.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
keywords: beeline hive, hive beeline
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 04/20/2018
ms.author: hrasheed
ms.openlocfilehash: 5b4798b183b44ef33b24a61c4f995b3ae7b3b9d0
ms.sourcegitcommit: 698ba3e88adc357b8bd6178a7b2b1121cb8da797
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/07/2018
ms.locfileid: "53014103"
---
# <a name="use-the-apache-beeline-client-with-apache-hive"></a>Použití Apache Beeline klienta s Apache Hive

Další informace o použití [Apache Beeline](https://cwiki.apache.org/confluence/display/Hive/HiveServer2+Clients#HiveServer2Clients-Beeline–NewCommandLineShell) spustit dotazy Apache Hive v HDInsight.

Beeline je klient Hive, který je součástí hlavní uzly clusteru HDInsight. Beeline používá JDBC pro připojení k serveru HiveServer2, služba hostovaná v clusteru HDInsight. Také vám pomůže Beeline Hive v HDInsight přistupovat vzdáleně přes internet. Následující příklady popisují nejběžnější připojovací řetězce pro připojení k HDInsight z Beeline:

* __Použití Beeline z připojení SSH k hlavnímu uzlu nebo hraničnímu uzlu__: `-u 'jdbc:hive2://headnodehost:10001/;transportMode=http'`
* __Použití Beeline na klientovi, připojení k HDInsight prostřednictvím služby Azure Virtual Network__: `-u 'jdbc:hive2://<headnode-FQDN>:10001/;transportMode=http'`
* __Použití Beeline na klientovi, připojení k HDInsight prostřednictvím veřejného Internetu__: `-u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2' -n admin -p password`

> [!NOTE]
> Nahraďte `admin` s účet přihlášení clusteru pro váš cluster.
>
> Nahraďte `password` se heslo pro účet přihlášení clusteru.
>
> Parametr `clustername` nahraďte názvem vašeho clusteru HDInsight.
>
> Při připojování ke clusteru přes virtuální síť, nahraďte `<headnode-FQDN>` s plně kvalifikovaný název domény hlavního uzlu clusteru.

## <a id="prereq"></a>Požadavky

* Hadoop založených na Linuxu v clusteru HDInsight verze 3.4 nebo vyšší.

  > [!IMPORTANT]
  > HDInsight od verze 3.4 výše používá výhradně operační systém Linux. Další informace najdete v tématu [Vyřazení prostředí HDInsight ve Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

* Místní Beeline klienta nebo klienta SSH. Většina kroků v tomto dokumentu předpokládají, že používáte Beeline z relace SSH do clusteru. Informace o spouštění Beeline z mimo cluster najdete v tématu [použití Beeline vzdáleně](#remote) oddílu.

    Další informace o používání SSH najdete v tématu [použití SSH se službou HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a id="beeline"></a>Spuštění dotazu Hive

1. Při spouštění Beeline, musíte zadat připojovací řetězec pro HiveServer2 v clusteru HDInsight:

    * Když se připojíte přes veřejný internet, je nutné zadat název účtu přihlášení clusteru (výchozí `admin`) a heslo. Například použití Beeline ze systému klienta pro připojení k `<clustername>.azurehdinsight.net` adresu. Toto připojení se provádí přes port `443`a je zašifrovaná pomocí SSL:

        ```bash
        beeline -u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2' -n admin -p password
        ```

    * Když se připojujete z relace SSH k hlavnímu uzlu clusteru, můžete se připojit k `headnodehost` adresu na portu `10001`:

        ```bash
        beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
        ```

    * Když se připojíte přes virtuální síť Azure, musíte plně kvalifikovaný název domény (FQDN) hlavního uzlu clusteru. Protože toto připojení se provádí přímo na uzlech clusteru, připojení používá port `10001`:

        ```bash
        beeline -u 'jdbc:hive2://<headnode-FQDN>:10001/;transportMode=http'
        ```

2. Příkazy beeline začínat `!` znak, třeba `!help` zobrazí nápovědu. Ale `!` může vynechat některé příkazy. Například `help` také funguje.

    Je `!sql`, který se používá k provedení příkazy HiveQL. HiveQL se ale tak běžně používá, můžete vynechat předchozí `!sql`. Následující dva příkazy jsou ekvivalentní:

    ```hiveql
    !sql show tables;
    show tables;
    ```

    Na novém clusteru je uveden pouze jednu tabulku: **hivesampletable**.

3. Chcete-li zobrazit schéma pro hivesampletable použijte následující příkaz:

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

4. Zadejte následující příkazy k vytvoření tabulky s názvem **log4jLogs** pomocí ukázkových dat, které jsou součástí clusteru HDInsight:

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

    * `DROP TABLE` – Pokud existuje v tabulce, je odstranit.

    * `CREATE EXTERNAL TABLE` -Vytvoří **externí** tabulky v Hivu. Externí tabulky pouze uložte definici tabulky Hive. Data zůstane v původním umístění.

    * `ROW FORMAT` -Způsob formátování data. V tomto případě pole v každém protokolu jsou oddělené mezerou.

    * `STORED AS TEXTFILE LOCATION` -Pokud jsou data uložená a v jakém formátu souboru.

    * `SELECT` – Počet všech řádků vybere kde sloupec **t4** obsahuje hodnotu **[Chyba]**. Tento dotaz vrátí hodnotu **3** jsou tři řádky, které obsahují tuto hodnotu.

    * `INPUT__FILE__NAME LIKE '%.log'` -Hive se pokusí použít schéma pro všechny soubory v adresáři. V takovém případě adresář obsahuje soubory, které neodpovídají schématu. Chcete-li zabránit uvolňování paměti ve výsledcích, tento příkaz sděluje Hive, že ji by měl vrátit pouze data ze souborů s koncovkou. log.

  > [!NOTE]
  > Pokud očekáváte, že podkladová data aktualizovat externího zdroje je třeba použít externí tabulky. Například automatizovaných datových odesílat operaci MapReduce nebo procesu.
  >
  > Vyřazení externí tabulky neodpovídá **není** odstranit data, pouze definici tabulky.

    Výstup tohoto příkazu se podobá následujícímu textu:

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

### <a id="file"></a>Použití Beeline umožňuje spustit soubor HiveQL

Pomocí následujících kroků vytvořte soubor a pak ji spustit pomocí Beeline.

1. Pomocí následujícího příkazu vytvořte soubor s názvem **query.hql**:

    ```bash
    nano query.hql
    ```

2. Použijte následující text jako obsah souboru. Tento dotaz vytvoří novou tabulku "vnitřní" s názvem **nepřenesl**:

    ```hiveql
    CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
    INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';
    ```

    Tyto příkazy provádět následující akce:

    * **Vytvoření tabulky IF NOT EXISTS** – Pokud tabulka již neexistuje, vytvoří se. Vzhledem k tomu, **externí** – klíčové slovo se nepoužívá, tento příkaz vytvoří interní tabulku. Interní tabulky jsou uložené v datovém skladu Hive a jsou kompletně spravované pomocí Hive.
    * **ULOŽENÉ jako ORC** – ukládá data ve formátu optimalizované řádek úložiště se sloupcovou strukturou (ORC). Formát ORC je vysoce optimalizovaných a efektivní formát pro ukládání dat Hive.
    * **VLOŽIT PŘEPSÁNÍ... Vyberte** -vybere řádky z **log4jLogs** tabulce, která obsahuje **[Chyba]**, pak vloží data do **nepřenesl** tabulky.

    > [!NOTE]
    > Na rozdíl od externích tabulek vyřadit interní tabulku odstraní podkladová data.

3. Chcete-li uložit soubor, použijte **Ctrl**+**_X**, zadejte **Y**a nakonec **Enter**.

4. Pomocí následujících spusťte soubor pomocí Beeline:

    ```bash
    beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http' -i query.hql
    ```

    > [!NOTE]
    > `-i` Parametr spustí Beeline a provede příkazy ve `query.hql` souboru. Po dokončení dotazu se dostanete na `jdbc:hive2://headnodehost:10001/>` řádku. Můžete také spustit soubor pomocí `-f` parametr, který ukončí Beeline po dokončení dotazu.

5. Pro ověření, že **nepřenesl** byla vytvořena tabulka, použijte následující příkaz vrátí všechny řádky z **nepřenesl**:

    ```hiveql
    SELECT * from errorLogs;
    ```

    Tří řádků dat by měla být vrácena, všechny obsahující **[Chyba]** ve sloupci t4:

        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        | errorlogs.t1  | errorlogs.t2  | errorlogs.t3  | errorlogs.t4  | errorlogs.t5  | errorlogs.t6  | errorlogs.t7  |
        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        | 2012-02-03    | 18:35:34      | SampleClass0  | [ERROR]       | incorrect     | id            |               |
        | 2012-02-03    | 18:55:54      | SampleClass1  | [ERROR]       | incorrect     | id            |               |
        | 2012-02-03    | 19:25:27      | SampleClass4  | [ERROR]       | incorrect     | id            |               |
        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        3 rows selected (1.538 seconds)

## <a id="remote"></a>Použití Beeline vzdáleně

Pokud máte Beeline nainstalovaný místně a připojte se přes veřejný internet, použijte následující parametry:

* __Připojovací řetězec__: `-u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2'`

* __Přihlašovací jméno clusteru__: `-n admin`

* __Heslo přihlášení clusteru__ `-p 'password'`

Nahradit `clustername` připojovacího řetězce s názvem vašeho clusteru HDInsight.

Nahraďte `admin` s názvem přihlášení ke clusteru, a nahraďte `password` heslem pro vaše přihlášení ke clusteru.

Pokud máte nainstalovaný místně Beeline a připojení přes virtuální síť Azure, použijte následující parametry:

* __Připojovací řetězec__: `-u 'jdbc:hive2://<headnode-FQDN>:10001/;transportMode=http'`

Pokud chcete zjistit plně kvalifikovaný název domény hlavního uzlu, použijte informace v [Správa HDInsight pomocí rozhraní Ambari REST API](../hdinsight-hadoop-manage-ambari-rest-api.md#example-get-the-fqdn-of-cluster-nodes) dokumentu.

## <a id="sparksql"></a>Použití Beeline se Sparkem

Spark poskytuje vlastní implementaci serveru HiveServer2, který se někdy označuje jako server Spark Thrift. Tato služba překladu místo Hive pomocí Spark SQL a může poskytovat lepší výkon v závislosti na dotazu.

__Připojovací řetězec__ použít při připojení přes internet se mírně liší. Anglický `httpPath=/hive2` je `httpPath/sparkhive2`. Následuje příklad připojení přes internet:

```bash 
beeline -u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/sparkhive2' -n admin -p password
```

Když se připojujete přímo z hlavního uzlu clusteru, nebo z prostředků ve stejné virtuální síti Azure jako HDInsight cluster, přenést `10002` byste měli použít pro server Spark Thrift, nikoli `10001`. Následuje příklad připojení přímo k hlavnímu uzlu:

```bash
beeline -u 'jdbc:hive2://headnodehost:10002/;transportMode=http'
```

## <a id="summary"></a><a id="nextsteps"></a>Další kroky

Další obecné informace o Hivu ve službě HDInsight najdete v následujícím dokumentu:

* [Použití Hivu s Hadoopem v HDInsight](hdinsight-use-hive.md)

Další informace o jiných způsobech mohl pracovat s Hadoop v HDInsight najdete v následujících dokumentech:

* [Použití Pigu se systémem Hadoop v HDInsight](hdinsight-use-pig.md)
* [Použití MapReduce se systémem Hadoop v HDInsight](hdinsight-use-mapreduce.md)

Pokud používáte pomocí Hive Tez, najdete v následujících dokumentech:

* [Použití uživatelského rozhraní Tez na HDInsight se systémem Windows](../hdinsight-debug-tez-ui.md)
* [Použití zobrazení Ambari Tez na HDInsight založených na Linuxu](../hdinsight-debug-ambari-tez-view.md)

[azure-purchase-options]: https://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: https://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: https://azure.microsoft.com/pricing/free-trial/

[apache-tez]: https://tez.apache.org
[apache-hive]: https://hive.apache.org/
[apache-log4j]: https://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: https://azure.microsoft.com/documentation/articles/hdinsight-connect-excel-power-query/


[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md

[putty]: https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html


[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-submit-jobs]:submit-apache-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md


[powershell-here-strings]: https://technet.microsoft.com/library/ee692792.aspx
