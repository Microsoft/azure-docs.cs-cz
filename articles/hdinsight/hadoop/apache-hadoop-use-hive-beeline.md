---
title: Použití Apache Beeline s Apache Hive – Azure HDInsight
description: Naučte se používat klienta Beeline ke spouštění dotazů na podregistr pomocí Hadoop v HDInsight. Beeline je nástroj pro práci s HiveServer2 nad JDBC.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/03/2019
ms.openlocfilehash: d6063daa649b507057fd2a4468c32dad1cd35eec
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2019
ms.locfileid: "72030428"
---
# <a name="use-the-apache-beeline-client-with-apache-hive"></a>Použití klienta Apache Beeline s Apache Hive

Naučte se používat [Apache Beeline](https://cwiki.apache.org/confluence/display/Hive/HiveServer2+Clients#HiveServer2Clients-Beeline–NewCommandLineShell) ke spouštění dotazů Apache Hive v HDInsight.

Beeline je klient podregistru, který je součástí hlavních uzlů clusteru HDInsight. Beeline používá JDBC pro připojení k HiveServer2, službě hostované v clusteru HDInsight. Beeline můžete použít také k vzdálenému přístupu k podregistru v HDInsight přes Internet. V následujících příkladech jsou uvedeny nejběžnější připojovací řetězce používané pro připojení ke službě HDInsight z Beeline:

## <a name="types-of-connections"></a>Typy připojení

### <a name="from-an-ssh-session"></a>Z relace SSH

Když se připojujete z relace SSH k hlavnímu uzlu clusteru, můžete se připojit k adrese `headnodehost` na portu `10001`:

```bash
beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
```

---

### <a name="over-an-azure-virtual-network"></a>Přes Virtual Network Azure

Když se připojujete z klienta k HDInsight přes Virtual Network Azure, musíte zadat plně kvalifikovaný název domény (FQDN) hlavního uzlu clusteru. Vzhledem k tomu, že se toto připojení provádí přímo na uzlech clusteru, připojení používá port `10001`:

```bash
beeline -u 'jdbc:hive2://<headnode-FQDN>:10001/;transportMode=http'
```

Nahraďte `<headnode-FQDN>` plně kvalifikovaným názvem domény hlavnímu uzlu clusteru. K vyhledání plně kvalifikovaného názvu domény hlavnímu uzlu použijte informace v části [Správa HDInsight pomocí dokumentu Apache Ambari REST API](../hdinsight-hadoop-manage-ambari-rest-api.md#example-get-the-fqdn-of-cluster-nodes) .

---

### <a name="to-hdinsight-enterprise-security-package-esp-cluster-using-kerberos"></a>Do clusteru HDInsight Balíček zabezpečení podniku (ESP) pomocí protokolu Kerberos

Když se připojujete z klienta k clusteru Balíček zabezpečení podniku (ESP) připojenému k Azure Active Directory (AAD) – DS na počítači ve stejné sféře clusteru, musíte zadat taky název domény `<AAD-Domain>` a název účtu uživatele domény s oprávněními. přístup ke clusteru `<username>`:

```bash
kinit <username>
beeline -u 'jdbc:hive2://<headnode-FQDN>:10001/default;principal=hive/_HOST@<AAD-Domain>;auth-kerberos;transportMode=http' -n <username>
```

Nahraďte `<username>` názvem účtu v doméně s oprávněními pro přístup ke clusteru. Nahraďte `<AAD-DOMAIN>` názvem Azure Active Directory (AAD), ke které je cluster připojený. Pro hodnotu @no__t 0 použijte řetězec velkého písmene, jinak se přihlašovací údaje nenaleznou. V případě potřeby vyhledejte v názvech sféry `/etc/krb5.conf`.

---

### <a name="over-public-or-private-endpoints"></a>Přes veřejné nebo soukromé koncové body

Při připojování ke clusteru pomocí veřejných nebo privátních koncových bodů je nutné zadat název přihlašovacího účtu clusteru (výchozí `admin`) a heslo. Například pomocí Beeline z klientského systému se připojte k adrese @no__t 0. Toto připojení se provádí přes port `443` a je šifrované pomocí protokolu SSL:

```bash
beeline -u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2' -n admin -p password
```

nebo pro soukromý koncový bod:

```bash
beeline -u 'jdbc:hive2://clustername-int.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2' -n admin -p password
```

Parametr `clustername` nahraďte názvem vašeho clusteru HDInsight. Nahraďte `admin` přihlašovacím účtem clusteru pro váš cluster. Nahraďte `password` heslem pro přihlašovací účet clusteru.

---

### <a id="sparksql"></a>Použití Beeline s Apache Spark

Apache Spark poskytuje vlastní implementaci HiveServer2, která se někdy označuje jako server Spark Thrift. Tato služba používá Spark SQL k překladu dotazů namísto podregistru a může poskytovat lepší výkon v závislosti na vašem dotazu.

#### <a name="through-public-or-private-endpoints"></a>Prostřednictvím veřejných nebo privátních koncových bodů

Použitý připojovací řetězec je trochu odlišný. Místo obsahujícího `httpPath=/hive2` je `httpPath/sparkhive2`:

```bash 
beeline -u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/sparkhive2' -n admin -p password
```

nebo pro soukromý koncový bod:

```bash 
beeline -u 'jdbc:hive2://clustername-int.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/sparkhive2' -n admin -p password
```

Parametr `clustername` nahraďte názvem vašeho clusteru HDInsight. Nahraďte `admin` přihlašovacím účtem clusteru pro váš cluster. Nahraďte `password` heslem pro přihlašovací účet clusteru.

---

#### <a name="from-cluster-head-or-inside-azure-virtual-network-with-apache-spark"></a>Z hlav clusteru nebo uvnitř Azure Virtual Network s Apache Spark

Při přímém připojení z hlavního uzlu clusteru nebo z prostředku ve stejném prostředí Azure Virtual Network jako cluster HDInsight, by se měl na serveru Spark Thrift použít port `10002` namísto `10001`. Následující příklad ukazuje, jak se připojit přímo k hlavnímu uzlu:

```bash
/usr/hdp/current/spark2-client/bin/beeline -u 'jdbc:hive2://headnodehost:10002/;transportMode=http'
```

---

## <a id="prereq"></a>Požadavky

* Cluster Hadoop ve službě HDInsight. Viz Začínáme [se službou HDInsight v systému Linux](./apache-hadoop-linux-tutorial-get-started.md).

* Všimněte si [schématu identifikátoru URI](../hdinsight-hadoop-linux-information.md#URI-and-scheme) pro primární úložiště vašeho clusteru. Například `wasb://` pro Azure Storage, `abfs://` pro Azure Data Lake Storage Gen2 nebo `adl://` pro Azure Data Lake Storage Gen1. Pokud je pro Azure Storage povolený zabezpečený přenos, je identifikátor URI `wasbs://`. Další informace najdete v tématu [zabezpečený přenos](../../storage/common/storage-require-secure-transfer.md).

* Možnost 1: klient SSH. Další informace najdete v tématu [připojení ke službě HDInsight (Apache Hadoop) pomocí SSH](../hdinsight-hadoop-linux-use-ssh-unix.md). Většina kroků v tomto dokumentu předpokládá, že používáte Beeline z relace SSH do clusteru.

* Možnost 2: místní klient Beeline.

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

3. Příkazy Beeline začínají znakem `!`, například `!help` zobrazí nápovědu. @No__t-0 však lze pro některé příkazy vynechat. Například `help` funguje také.

    K provedení příkazů HiveQL se používá `!sql`. HiveQL je ale často používaný, takže můžete vynechat předchozí `!sql`. Následující dva příkazy jsou ekvivalentní:

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

5. Zadáním následujících příkazů vytvořte tabulku s názvem **log4jLogs** pomocí ukázkových dat, která jsou součástí clusteru HDInsight: (podle potřeby podle [schématu identifikátoru URI](../hdinsight-hadoop-linux-information.md#URI-and-scheme)Proveďte revizi podle potřeby).

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

    * `DROP TABLE` – Pokud tabulka existuje, je odstraněna.

    * `CREATE EXTERNAL TABLE` – vytvoří **externí** tabulku v podregistru. Externí tabulky ukládají pouze definici tabulky v podregistru. Data zůstanou v původním umístění.

    * `ROW FORMAT` – způsob formátování dat. V tomto případě jsou pole v každém protokolu oddělená mezerou.

    * `STORED AS TEXTFILE LOCATION` – kde jsou data uložena a v jakém formátu souboru.

    * `SELECT` – vybere počet všech řádků, ve kterých sloupec **T4** obsahuje hodnotu **[Chyba]** . Tento dotaz vrátí hodnotu **3** , protože jsou tři řádky, které obsahují tuto hodnotu.

    * `INPUT__FILE__NAME LIKE '%.log'`-podregistr se pokusí použít schéma pro všechny soubory v adresáři. V tomto případě adresář obsahuje soubory, které neodpovídají schématu. Aby se zabránilo uvolňování dat ve výsledcích, tento příkaz oznamuje podregistru, že by měl vracet pouze data ze souborů končících log. log.

   > [!NOTE]  
   > Externí tabulky by měly být použity, pokud očekáváte, že budou zdrojová data aktualizována externím zdrojem. Například automatizovaný proces odesílání dat nebo operace MapReduce.
   >
   > Vyřazení externí tabulky **neodstraní data** , pouze definici tabulky.

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

2. Jako obsah souboru použijte následující text. Tento dotaz vytvoří novou interní **tabulku s názvem**protokolu chyb:

    ```hiveql
    CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
    INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';
    ```

    Tyto příkazy provádějí následující akce:

   * **Create Table Pokud neexistuje** , pokud tabulka ještě neexistuje, vytvoří se. Vzhledem k tomu, že se klíčové slovo **External** nepoužívá, vytvoří tento příkaz interní tabulku. Interní tabulky jsou uložené v datovém skladu podregistru a jsou plně spravované podregistrem.
   * **Uloženo jako ORC** – ukládá data ve formátu optimalizovaného řádku (Orc). Formát ORC je vysoce optimalizovaný a efektivní formát pro ukládání dat z podregistru.
   * **Vložit přepsání... Vyberte možnost** – vybere řádky z tabulky **log4jLogs** , která obsahuje **[Error]** , a pak data vloží do **tabulky chyb** .

    > [!NOTE]  
    > Na rozdíl od externích tabulek odstraní interní tabulka také podkladová data.

3. Pokud chcete soubor uložit, použijte **Ctrl**+ **_X**, zadejte **Y**a nakonec **ENTER**.

4. K spuštění souboru pomocí Beeline použijte následující:

    ```bash
    beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http' -i query.hql
    ```

    > [!NOTE]  
    > Parametr `-i` spustí Beeline a spustí příkazy v souboru `query.hql`. Až se dotaz dokončí, přijdete na `jdbc:hive2://headnodehost:10001/>` prompt. Můžete také spustit soubor pomocí parametru `-f`, který ukončí Beeline po dokončení dotazu.

5. Chcete-li ověřit, zda byla **vytvořena tabulka chyb** protokolu chyb, použijte následující příkaz, který vrátí všechny řádky z chyb protokolu **chyb:**

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
