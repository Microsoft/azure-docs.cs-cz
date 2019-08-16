---
title: Použití Apache Beeline s Apache Hive – Azure HDInsight
description: Naučte se používat klienta Beeline ke spouštění dotazů na podregistr pomocí Hadoop v HDInsight. Beeline je nástroj pro práci s HiveServer2 nad JDBC.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: hrasheed
ms.openlocfilehash: 8d94716600305d3d2a567068fc719a83ce94c83d
ms.sourcegitcommit: a6888fba33fc20cc6a850e436f8f1d300d03771f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/16/2019
ms.locfileid: "69557809"
---
# <a name="use-the-apache-beeline-client-with-apache-hive"></a>Použití klienta Apache Beeline s Apache Hive

Naučte se používat [Apache Beeline](https://cwiki.apache.org/confluence/display/Hive/HiveServer2+Clients#HiveServer2Clients-Beeline–NewCommandLineShell) ke spouštění dotazů Apache Hive v HDInsight.

Beeline je klient podregistru, který je součástí hlavních uzlů clusteru HDInsight. Beeline používá JDBC pro připojení k HiveServer2, službě hostované v clusteru HDInsight. Beeline můžete použít také k vzdálenému přístupu k podregistru v HDInsight přes Internet. V následujících příkladech jsou uvedeny nejběžnější připojovací řetězce používané pro připojení ke službě HDInsight z Beeline:

## <a name="types-of-connections"></a>Typy připojení

### <a name="from-an-ssh-session"></a>Z relace SSH

Když se připojujete z relace SSH k hlavnímu uzlu clusteru, můžete se připojit k `headnodehost` adrese na portu: `10001`

```bash
beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
```

---

### <a name="over-an-azure-virtual-network"></a>Přes Virtual Network Azure

Když se připojujete z klienta k HDInsight přes Virtual Network Azure, musíte zadat plně kvalifikovaný název domény (FQDN) hlavního uzlu clusteru. Vzhledem k tomu, že se toto připojení provádí přímo na uzlech clusteru, `10001`připojení používá port:

```bash
beeline -u 'jdbc:hive2://<headnode-FQDN>:10001/;transportMode=http'
```

Nahraďte `<headnode-FQDN>` plně kvalifikovaným názvem domény hlavnímu uzlu clusteru. K vyhledání plně kvalifikovaného názvu domény hlavnímu uzlu použijte informace v části [Správa HDInsight pomocí dokumentu Apache Ambari REST API](../hdinsight-hadoop-manage-ambari-rest-api.md#example-get-the-fqdn-of-cluster-nodes) .

---

### <a name="to-hdinsight-enterprise-security-package-esp-cluster"></a>Do clusteru HDInsight Balíček zabezpečení podniku (ESP)

Když se připojujete z klienta k clusteru balíček zabezpečení podniku (ESP) připojenému k Azure Active Directory (AAD) na počítači ve stejné sféře clusteru, musíte zadat taky název `<AAD-Domain>` domény a název účtu uživatele domény s oprávněním k. přístup ke clusteru `<username>`:

```bash
kinit <username>
beeline -u 'jdbc:hive2://<headnode-FQDN>:10001/default;principal=hive/_HOST@<AAD-Domain>;auth-kerberos;transportMode=http' -n <username>
```

Nahraďte `<username>` názvem účtu v doméně, který má oprávnění pro přístup ke clusteru. Nahraďte `<AAD-DOMAIN>` názvem Azure Active Directory (AAD), ke které je cluster připojený. Pro `<AAD-DOMAIN>` hodnotu použijte velká písmena, jinak se přihlašovací údaje nenašly. V `/etc/krb5.conf` případě potřeby vyhledejte názvy sféry.

---

### <a name="over-public-internet"></a>Přes veřejný Internet

Když se připojujete ke clusteru ESP připojenému k jiným než ESP nebo Azure Active Directory (AAD) přes veřejný Internet, musíte zadat název přihlašovacího účtu clusteru ( `admin`výchozí) a heslo. Například pomocí Beeline z klientského systému se připojte k `<clustername>.azurehdinsight.net` adrese. Toto připojení se provádí přes port `443`a je šifrované pomocí protokolu SSL:

```bash
beeline -u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2' -n admin -p password
```

Parametr `clustername` nahraďte názvem vašeho clusteru HDInsight. Nahraďte `admin` přihlašovacím účtem clusteru pro svůj cluster. Nahraďte `password` heslem přihlašovacího účtu clusteru.

---

### <a id="sparksql"></a>Použití Beeline s Apache Spark

Apache Spark poskytuje vlastní implementaci HiveServer2, která se někdy označuje jako server Spark Thrift. Tato služba používá Spark SQL k překladu dotazů namísto podregistru a může poskytovat lepší výkon v závislosti na vašem dotazu.

#### <a name="over-public-internet-with-apache-spark"></a>Přes veřejný Internet s Apache Spark

Připojovací řetězec, který se používá při připojování přes Internet, se mírně liší. Místo, kde `httpPath=/hive2` je `httpPath/sparkhive2`obsaženo:

```bash 
beeline -u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/sparkhive2' -n admin -p password
```

---

#### <a name="from-cluster-head-or-inside-azure-virtual-network-with-apache-spark"></a>Z hlav clusteru nebo uvnitř Azure Virtual Network s Apache Spark

Při přímém připojení z hlavního uzlu clusteru nebo z prostředku ve stejném Virtual Network Azure jako cluster HDInsight by se měl port `10002` použít pro server Spark Thrift `10001`místo. Následující příklad ukazuje, jak se připojit přímo k hlavnímu uzlu:

```bash
beeline -u 'jdbc:hive2://headnodehost:10002/;transportMode=http'
```

---

## <a id="prereq"></a>Požadavky

* Cluster Hadoop ve službě HDInsight. Viz Začínáme [se službou HDInsight v systému Linux](./apache-hadoop-linux-tutorial-get-started.md).

* Všimněte si [schématu identifikátoru URI](../hdinsight-hadoop-linux-information.md#URI-and-scheme) pro primární úložiště vašeho clusteru. Například `wasb://` pro `adl://` Azure Storage pro Azure Data Lake Storage Gen2 nebo pro Azure Data Lake Storage Gen1. `abfs://` Pokud je pro Azure Storage nebo data Lake Storage Gen2 povolený zabezpečený přenos, je `wasbs://` identifikátor `abfss://`URI nebo v uvedeném pořadí. Další informace najdete v tématu [zabezpečený přenos](../../storage/common/storage-require-secure-transfer.md).


* Možnost 1: Klient SSH. Další informace najdete v tématu [připojení ke službě HDInsight (Apache Hadoop) pomocí SSH](../hdinsight-hadoop-linux-use-ssh-unix.md). Většina kroků v tomto dokumentu předpokládá, že používáte Beeline z relace SSH do clusteru.

* Možnost 2:  Místní klient Beeline.


## <a id="beeline"></a>Spustit dotaz na podregistr

Tento příklad je založený na použití klienta Beeline z připojení SSH.

1. Otevřete připojení SSH ke clusteru pomocí následujícího kódu. Místo `sshuser` použijte jméno uživatele SSH pro váš cluster a místo `CLUSTERNAME` zadejte název clusteru. Po zobrazení výzvy zadejte heslo pro uživatelský účet SSH.

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. Připojte se k HiveServer2 pomocí klienta Beeline z otevřené relace SSH zadáním následujícího příkazu:

    ```bash
    beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
    ```

3. Příkazy Beeline začínají `!` znakem, `!help` například zobrazí nápovědu. U některých příkazů ale můžebýtvynecháno.`!` Například `help` funguje také.

    K dispozici `!sql`je, který se používá ke spouštění příkazů HiveQL. HiveQL je ale často používaný, takže můžete vynechat předchozí `!sql`. Následující dva příkazy jsou ekvivalentní:

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

    Tyto informace popisují sloupce v tabulce.

5. Zadáním následujících příkazů vytvořte tabulku s názvem **log4jLogs** pomocí ukázkových dat poskytnutých s clusterem HDInsight: (Podle potřeby upravte podle [schématu identifikátoru URI](../hdinsight-hadoop-linux-information.md#URI-and-scheme).)

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

    Tyto příkazy provádějí následující akce:

    * `DROP TABLE`– Pokud tabulka existuje, odstraní se.

    * `CREATE EXTERNAL TABLE`– Vytvoří **externí** tabulku v podregistru. Externí tabulky ukládají pouze definici tabulky v podregistru. Data zůstanou v původním umístění.

    * `ROW FORMAT`– Způsob formátování dat V tomto případě jsou pole v každém protokolu oddělená mezerou.

    * `STORED AS TEXTFILE LOCATION`– Kde jsou data uložena a v jakém formátu souboru.

    * `SELECT`– Vybere počet všech řádků, ve kterých sloupec **T4** obsahuje hodnotu **[Chyba]** . Tento dotaz vrátí hodnotu **3** , protože jsou tři řádky, které obsahují tuto hodnotu.

    * `INPUT__FILE__NAME LIKE '%.log'`-Podregistr se pokusí použít schéma pro všechny soubory v adresáři. V tomto případě adresář obsahuje soubory, které neodpovídají schématu. Aby se zabránilo uvolňování dat ve výsledcích, tento příkaz oznamuje podregistru, že by měl vracet pouze data ze souborů končících log. log.

   > [!NOTE]  
   > Externí tabulky by měly být použity, pokud očekáváte, že budou zdrojová data aktualizována externím zdrojem. Například automatizovaný proces odesílání dat nebo operace MapReduce.
   >
   > Vyřazení externí tabulky neodstraní data, pouze definici tabulky.

    Výstup tohoto příkazu je podobný následujícímu textu:

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

6. K ukončení Beeline použijte `!exit`.

## <a id="file"></a>Spuštění souboru HiveQL

Toto je pokračování z předchozího příkladu. Pomocí následujících kroků vytvořte soubor a pak ho spusťte pomocí Beeline.

1. Pomocí následujícího příkazu vytvořte soubor s názvem **Query. HQL**:

    ```bash
    nano query.hql
    ```

2. Jako obsah souboru použijte následující text. Tento dotaz vytvoří novou interní tabulku s názvem protokolu chyb:

    ```hiveql
    CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
    INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';
    ```

    Tyto příkazy provádějí následující akce:

   * **Create Table Pokud není** k dispozici – Pokud tabulka ještě neexistuje, vytvoří se. Vzhledem k tomu, že se klíčové slovo **External** nepoužívá, vytvoří tento příkaz interní tabulku. Interní tabulky jsou uložené v datovém skladu podregistru a jsou plně spravované podregistrem.
   * **Uloženo jako ORC** – ukládá data ve formátu optimalizovaného řádku (Orc). Formát ORC je vysoce optimalizovaný a efektivní formát pro ukládání dat z podregistru.
   * **VLOŽIT PŘEPSÁNÍ... Vyberte** možnost – vybere řádky z tabulky **log4jLogs** , která obsahuje **[Error]** , a pak data vloží do tabulky chyb.

    > [!NOTE]  
    > Na rozdíl od externích tabulek odstraní interní tabulka také podkladová data.

3. Pokud chcete soubor uložit, použijte **CTRL**+ **_X**, zadejte **Y**a nakonec **ENTER**.

4. K spuštění souboru pomocí Beeline použijte následující:

    ```bash
    beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http' -i query.hql
    ```

    > [!NOTE]  
    > Parametr spustí Beeline a spustí příkazy `query.hql` v souboru. `-i` Po dokončení `jdbc:hive2://headnodehost:10001/>` dotazu se zobrazí výzva. Můžete také spustit soubor pomocí `-f` parametru, který ukončí Beeline po dokončení dotazu.

5. Chcete-li ověřit , zda byla vytvořena tabulka chyb protokolu chyb, použijte následující příkaz, který vrátí všechnyřádky z chyb protokolu chyb:

    ```hiveql
    SELECT * from errorLogs;
    ```

    Měly by se vracet tři řádky dat, všechny obsahující **[Error]** v sloupci T4:

        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        | errorlogs.t1  | errorlogs.t2  | errorlogs.t3  | errorlogs.t4  | errorlogs.t5  | errorlogs.t6  | errorlogs.t7  |
        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        | 2012-02-03    | 18:35:34      | SampleClass0  | [ERROR]       | incorrect     | id            |               |
        | 2012-02-03    | 18:55:54      | SampleClass1  | [ERROR]       | incorrect     | id            |               |
        | 2012-02-03    | 19:25:27      | SampleClass4  | [ERROR]       | incorrect     | id            |               |
        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        3 rows selected (1.538 seconds)




## <a id="summary"></a><a id="nextsteps"></a>Další kroky

Obecnější informace o podregistru v HDInsight najdete v následujícím dokumentu:

* [Použití Apache Hive s Apache Hadoop v HDInsight](hdinsight-use-hive.md)

Další informace o dalších způsobech práce se systémem Hadoop ve službě HDInsight najdete v následujících dokumentech:

* [Použití systému Apache prasete s Apache Hadoop v HDInsight](hdinsight-use-pig.md)
* [Použití MapReduce s Apache Hadoop v HDInsight](hdinsight-use-mapreduce.md)

[azure-purchase-options]: https://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: https://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: https://azure.microsoft.com/pricing/free-trial/

[apache-tez]: https://tez.apache.org
[apache-hive]: https://hive.apache.org/
[apache-log4j]: https://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: https://azure.microsoft.com/documentation/articles/hdinsight-connect-excel-power-query/


[hdinsight-use-oozie]: hdinsight-use-oozie-linux-mac.md

[putty]: https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html


[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-submit-jobs]:submit-apache-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md


[powershell-here-strings]: https://technet.microsoft.com/library/ee692792.aspx
