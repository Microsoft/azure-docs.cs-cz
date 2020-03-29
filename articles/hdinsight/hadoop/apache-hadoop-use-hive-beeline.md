---
title: Použití Apache Beeline s Apache Hive - Azure HDInsight
description: Přečtěte si, jak pomocí klienta Beeline spouštět dotazy Hive s Hadoopem na HDInsightu. Beeline je nástroj pro práci s HiveServer2 přes JDBC.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 03/09/2020
ms.openlocfilehash: 77a451cb9f6598bbe7013f4215cfa7cab40186bd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79037544"
---
# <a name="use-the-apache-beeline-client-with-apache-hive"></a>Použití klienta Apache Beeline s Apache Hivem

Přečtěte si, jak pomocí [Apache Beeline](https://cwiki.apache.org/confluence/display/Hive/HiveServer2+Clients#HiveServer2Clients-Beeline–NewCommandLineShell) spouštět dotazy Apache Hive na HDInsightu.

Beeline je klient Hive, který je součástí hlavního uzlu clusteru HDInsight. Pokud chcete Beeline nainstalovat místně, přečtěte si níže [informace o instalaci klienta beeline](#install-beeline-client). Beeline používá JDBC pro připojení k HiveServer2, službě hostované v clusteru HDInsight. Můžete také použít Beeline pro vzdálený přístup k Hive na HDInsight přes internet. Následující příklady poskytují nejběžnější připojovací řetězce používané pro připojení k HDInsight z Beeline.

## <a name="types-of-connections"></a>Typy připojení

### <a name="from-an-ssh-session"></a>Z relace SSH

Při připojování z relace SSH k hlavnímu uzlu `headnodehost` clusteru `10001`se pak můžete připojit k adrese na portu :

```bash
beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
```

---

### <a name="over-an-azure-virtual-network"></a>Přes virtuální síť Azure

Při připojování z klienta k HDInsight přes virtuální síť Azure, musíte zadat plně kvalifikovaný název domény (FQDN) hlavního uzlu clusteru. Vzhledem k tomu, že toto připojení je `10001`naváděno přímo k uzlům clusteru, používá připojení port :

```bash
beeline -u 'jdbc:hive2://<headnode-FQDN>:10001/;transportMode=http'
```

Nahraďte `<headnode-FQDN>` plně kvalifikovanýnázev domény hlavového uzlu clusteru. Chcete-li najít plně kvalifikovaný název domény headnode, použijte informace v spravovat HDInsight pomocí dokumentu [Apache Ambari REST API.](../hdinsight-hadoop-manage-ambari-rest-api.md#example-get-the-fqdn-of-cluster-nodes)

---

### <a name="to-hdinsight-enterprise-security-package-esp-cluster-using-kerberos"></a>Cluster balíčků HDInsight Enterprise Security Package (ESP) pomocí protokolu Kerberos

Při připojování z klienta ke clusteru balíčku zabezpečení rozlehlé sítě (ESP) připojovanému ke službě Azure Active Directory (AAD)-DS v počítači ve stejné sféře clusteru je nutné také zadat název `<AAD-Domain>` domény a název uživatelského účtu domény s oprávněními k přístupu ke clusteru `<username>`:

```bash
kinit <username>
beeline -u 'jdbc:hive2://<headnode-FQDN>:10001/default;principal=hive/_HOST@<AAD-Domain>;auth-kerberos;transportMode=http' -n <username>
```

Nahraďte `<username>` název účtu v doméně oprávněními pro přístup ke clusteru. Nahraďte `<AAD-DOMAIN>` název služby Azure Active Directory (AAD), ke kterému je cluster připojen. Pro hodnotu `<AAD-DOMAIN>` použijte řetězec velkých písmen, jinak pověření nebude nalezeno. V `/etc/krb5.conf` případě potřeby zkontrolujte názvy sfér.

Jak najít adresu URL JDBC z Ambari:

1. Z webového prohlížeče `https://CLUSTERNAME.azurehdinsight.net/#/main/services/HIVE/summary`přejděte `CLUSTERNAME` na , kde je název clusteru. Ujistěte se, že hiveServer2 je spuštěn.

1. Ke kopírování adresy URL JDBC serveru HiveServer2 použijte schránku.

---

### <a name="over-public-or-private-endpoints"></a>Přes veřejné nebo soukromé koncové body

Při připojování ke clusteru pomocí veřejných nebo privátní `admin`koncové body, musíte zadat název přihlašovacího účtu clusteru (výchozí) a heslo. Například pomocí Beeline z klientského systému pro připojení k `clustername.azurehdinsight.net` adrese. Toto připojení je `443`provedeno přes port a je šifrováno pomocí ssl.

Parametr `clustername` nahraďte názvem vašeho clusteru HDInsight. Nahraďte `admin` přihlašovacím účtem clusteru pro váš cluster. Pro clustery ESP použijte úplný hlavní název user@domain.comjednotky (například). Nahraďte `password` heslem přihlašovacího účtu clusteru.

```bash
beeline -u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2' -n admin -p 'password'
```

nebo pro soukromý koncový bod:

```bash
beeline -u 'jdbc:hive2://clustername-int.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2' -n admin -p 'password'
```

Soukromé koncové body odkazují na základní vyrovnávání zatížení, ke kterému lze přistupovat pouze z virtuálních virtuálních připojení ve stejné oblasti. Další informace [najdete v tématu omezení globálního partnerského vztahu virtuální sítě a vyvyčovávačů zatížení.](../../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers) `curl` Příkaz s `-v` možností můžete použít k řešení problémů s připojením s veřejnými nebo soukromými koncovými body před použitím čáry.

---

### <a name="use-beeline-with-apache-spark"></a>Použití Beeline s Apache Spark

Apache Spark poskytuje vlastní implementaci HiveServer2, která je někdy označována jako Spark Thrift server. Tato služba používá Spark SQL k řešení dotazů namísto Hive a může poskytovat lepší výkon v závislosti na dotazu.

#### <a name="through-public-or-private-endpoints"></a>Prostřednictvím veřejných nebo soukromých koncových bodů

Použitý připojovací řetězec se mírně liší. Místo toho, `httpPath=/hive2` aby `httpPath/sparkhive2`obsahovala je to . Parametr `clustername` nahraďte názvem vašeho clusteru HDInsight. Nahraďte `admin` přihlašovacím účtem clusteru pro váš cluster. Pro clustery ESP použijte úplný hlavní název user@domain.comjednotky (například). Nahraďte `password` heslem přihlašovacího účtu clusteru.

```bash
beeline -u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/sparkhive2' -n admin -p 'password'
```

nebo pro soukromý koncový bod:

```bash
beeline -u 'jdbc:hive2://clustername-int.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/sparkhive2' -n admin -p 'password'
```

Soukromé koncové body odkazují na základní vyrovnávání zatížení, ke kterému lze přistupovat pouze z virtuálních virtuálních připojení ve stejné oblasti. Další informace [najdete v tématu omezení globálního partnerského vztahu virtuální sítě a vyvyčovávačů zatížení.](../../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers) `curl` Příkaz s `-v` možností můžete použít k řešení problémů s připojením s veřejnými nebo soukromými koncovými body před použitím čáry.

---

#### <a name="from-cluster-head-or-inside-azure-virtual-network-with-apache-spark"></a>Z hlavy clusteru nebo uvnitř Virtuální sítě Azure s Apache Spark

Při připojování přímo z hlavního uzlu clusteru nebo z prostředku uvnitř stejné `10002` virtuální sítě Azure jako cluster `10001`HDInsight by měl být port použit pro server Spark Thrift namísto . Následující příklad ukazuje, jak se připojit přímo k hlavnímu uzlu:

```bash
/usr/hdp/current/spark2-client/bin/beeline -u 'jdbc:hive2://headnodehost:10002/;transportMode=http'
```

---

## <a name="prerequisites-for-examples"></a>Předpoklady pro příklady

* Cluster Hadoop na HDInsight. Viz [Začínáme s HDInsight na Linuxu](./apache-hadoop-linux-tutorial-get-started.md).

* Všimněte si [schématu IDENTIFIKÁTORU URI](../hdinsight-hadoop-linux-information.md#URI-and-scheme) pro primární úložiště clusteru. Například `wasb://` pro Azure `abfs://` Storage, pro Azure Data `adl://` Lake Storage Gen2 nebo pro Azure Data Lake Storage Gen1. Pokud je pro Azure Storage povolený `wasbs://`zabezpečený přenos, je identifikátor URI . Další informace naleznete v tématu [zabezpečený přenos](../../storage/common/storage-require-secure-transfer.md).

* Možnost 1: Klient SSH. Další informace naleznete [v tématu Připojení k HDInsight (Apache Hadoop) pomocí SSH](../hdinsight-hadoop-linux-use-ssh-unix.md). Většina kroků v tomto dokumentu předpokládá, že používáte Beeline z relace SSH do clusteru.

* Možnost 2: Místní klient Beeline.

## <a name="run-a-hive-query"></a>Spuštění dotazu Hive

Tento příklad je založen na použití klienta Beeline z připojení SSH.

1. Otevřete připojení SSH ke clusteru s níže uvedeným kódem. Místo `sshuser` použijte jméno uživatele SSH pro váš cluster a místo `CLUSTERNAME` zadejte název clusteru. Po zobrazení výzvy zadejte heslo pro uživatelský účet SSH.

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. Připojte se k HiveServer2 s klientem Beeline z otevřené relace SSH zadáním následujícího příkazu:

    ```bash
    beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
    ```

3. Příkazy příkazy Beeline začínají `!` `!help` znakem, například zobrazuje nápovědu. Nicméně `!` lze vynechat pro některé příkazy. Například `help` také funguje.

    Je , `!sql`který se používá ke spuštění příkazy HiveQL. HiveQL se však používá tak běžně, že můžete `!sql`vynechat předchozí . Následující dva příkazy jsou rovnocenné:

    ```hiveql
    !sql show tables;
    show tables;
    ```

    V novém clusteru je uvedena pouze jedna tabulka: **hivesampletable**.

4. Pomocí následujícího příkazu zobrazte schéma pro tabulku hivesampletable:

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

5. Zadejte následující příkazy k vytvoření tabulky s názvem **log4jLogs** pomocí ukázkových dat dodaných s clusterem HDInsight: (Podle potřeby revidovat na základě [schématu URI](../hdinsight-hadoop-linux-information.md#URI-and-scheme).)

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

    Tyto příkazy provést následující akce:

    |Příkaz |Popis |
    |---|---|
    |DROP TABULKA|Pokud tabulka existuje, je odstraněna.|
    |VYTVOŘIT EXTERNÍ TABULKU|Vytvoří **externí** tabulku v podregistru. Externí tabulky ukládají pouze definici tabulky v Hive. Data zůstanou v původním umístění.|
    |FORMÁT ŘÁDKU|Jak jsou data formátována. V tomto případě jsou pole v každém protokolu oddělena mezerou.|
    |ULOŽENÉ JAKO UMÍSTĚNÍ TEXTOVÉHO SOUBORU|Kde jsou data uložena a v jakém formátu souboru.|
    |SELECT|Vybere počet všech řádků, ve kterých sloupec **t4** obsahuje hodnotu **[ERROR]**. Tento dotaz vrátí hodnotu **3,** protože existují tři řádky, které obsahují tuto hodnotu.|
    |INPUT__FILE__NAME JAKO '%.log'|Hive se pokusí použít schéma pro všechny soubory v adresáři. V tomto případě adresář obsahuje soubory, které neodpovídají schématu. Chcete-li zabránit vrácení dat ve výsledcích, tento příkaz říká Hive, že by měl vracet data pouze ze souborů končících na .log.|

   > [!NOTE]  
   > Externí tabulky by měly být použity, pokud očekáváte, že podkladová data budou aktualizována externím zdrojem. Například automatizovaný proces odesílání dat nebo operace MapReduce.
   >
   > Uvolněním externí tabulky **neodstraníte** data, pouze definice tabulky.

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

6. Exit Beeline:

    ```bash
    !exit
    ```

## <a name="run-a-hiveql-file"></a>Spuštění souboru HiveQL

Toto je pokračování z předchozího příkladu. Pomocí následujících kroků vytvořte soubor a spusťte jej pomocí funkce Beeline.

1. Pomocí následujícího příkazu vytvořte soubor s názvem **query.hql**:

    ```bash
    nano query.hql
    ```

1. Jako obsah souboru použijte následující text. Tento dotaz vytvoří novou "interní" tabulku s názvem **errorLogs**:

    ```hiveql
    CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
    INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';
    ```

    Tyto příkazy provést následující akce:

    |Příkaz |Popis |
    |---|---|
    |Vytvořit tabulku, pokud neexistuje|Pokud tabulka ještě neexistuje, je vytvořena. Vzhledem k tomu, **že** externí klíčové slovo není použit, tento příkaz vytvoří vnitřní tabulku. Interní tabulky jsou uloženy v datovém skladu Hive a jsou zcela spravovány společností Hive.|
    |ULOŽENO JAKO ORC|Ukládá data ve formátu Optimalizovaný řádek Sloupcový (ORC). Formát ORC je vysoce optimalizovaný a efektivní formát pro ukládání dat Hive.|
    |VLOŽIT PŘEPIŠTE ... Vyberte|Vybere řádky z tabulky **log4jLogs,** které obsahují **[ERROR],** a pak vloží data do tabulky **errorLogs.**|

    > [!NOTE]  
    > Na rozdíl od externích tabulek odstranění maže vnitřní tabulka také podkladová data.

1. Chcete-li soubor uložit, použijte **kombinaci kláves Ctrl**+**X**, zadejte **hodnotu Y**a nakonec **zadejte**.

1. Ke spuštění souboru pomocí beeline použijte následující:

    ```bash
    beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http' -i query.hql
    ```

    > [!NOTE]  
    > Parametr `-i` spustí Beeline a spustí `query.hql` příkazy v souboru. Po dokončení dotazu se dostanete `jdbc:hive2://headnodehost:10001/>` na výzvu. Můžete také spustit soubor `-f` pomocí parametru, který ukončí Beeline po dokončení dotazu.

1. Chcete-li ověřit, zda byla vytvořena tabulka **errorLogs,** použijte následující příkaz k vrácení všech řádků z **errorLogs**:

    ```hiveql
    SELECT * from errorLogs;
    ```

    Měly by být vráceny tři řádky dat, všechny obsahující **[ERROR]** ve sloupci t4:

        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        | errorlogs.t1  | errorlogs.t2  | errorlogs.t3  | errorlogs.t4  | errorlogs.t5  | errorlogs.t6  | errorlogs.t7  |
        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        | 2012-02-03    | 18:35:34      | SampleClass0  | [ERROR]       | incorrect     | id            |               |
        | 2012-02-03    | 18:55:54      | SampleClass1  | [ERROR]       | incorrect     | id            |               |
        | 2012-02-03    | 19:25:27      | SampleClass4  | [ERROR]       | incorrect     | id            |               |
        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        3 rows selected (0.813 seconds)

## <a name="install-beeline-client"></a>Instalace klienta beeline

Přestože je Beeline součástí hlavního uzlu clusteru HDInsight, můžete ji nainstalovat do místního počítače.  Níže uvedené kroky k instalaci Beeline na místním počítači jsou založeny na [Windows Subsystem pro Linux](https://docs.microsoft.com/windows/wsl/install-win10).

1. Aktualizujte seznamy balíčků. Do prostředí bash zadejte následující příkaz:

    ```bash
    sudo apt-get update
    ```

1. Pokud není nainstalována, nainstalujte jazyk Java. Můžete zkontrolovat pomocí `which java` příkazu.

    1. Pokud není nainstalován žádný balíček java, zadejte následující příkaz:

        ```bash
        sudo apt install openjdk-11-jre-headless
        ```

    1. Otevřete soubor bashrc (obvykle se nachází `nano ~/.bashrc`v ~/.bashrc): .

    1. Pozměňte spis bashrc. Na konec souboru přidejte následující řádek:

        ```bash
        export JAVA_HOME=/usr/lib/jvm/java-1.11.0-openjdk-amd64
        ```

        Pak stiskněte **Ctrl+X**, pak **Y**a zadejte.

1. Stáhněte si archivy Hadoop a Beeline, zadejte následující příkazy:

    ```bash
    wget https://archive.apache.org/dist/hadoop/core/hadoop-2.7.3/hadoop-2.7.3.tar.gz
    wget https://archive.apache.org/dist/hive/hive-1.2.1/apache-hive-1.2.1-bin.tar.gz
    ```

1. Rozbalte archivy, zadejte následující příkazy:

    ```bash
    tar -xvzf hadoop-2.7.3.tar.gz
    tar -xvzf apache-hive-1.2.1-bin.tar.gz
    ```

1. Dále změnit spis bashrc. Budete muset identifikovat cestu k místu, kde byly archivy rozbaleny. Pokud používáte [Podsystém Windows pro Linux](https://docs.microsoft.com/windows/wsl/install-win10)a postupovali jste `/mnt/c/Users/user/`přesně `user` podle kroků, vaše cesta by byla , kde je vaše uživatelské jméno.

    1. Otevřete soubor:`nano ~/.bashrc`

    1. Upravte níže uvedené příkazy s příslušnou cestou a zadejte je na konci souboru bashrc:

        ```bash
        export HADOOP_HOME=/path_where_the_archives_were_unpacked/hadoop-2.7.3
        export HIVE_HOME=/path_where_the_archives_were_unpacked/apache-hive-1.2.1-bin
        PATH=$PATH:$HIVE_HOME/bin
        ```

    1. Pak stiskněte **Ctrl+X**, pak **Y**a zadejte.

1. Zavřete a znovu otevřete relaci bash.

1. Otestujte připojení. Použijte formát připojení z [více než veřejné nebo soukromé koncové body](#over-public-or-private-endpoints), výše.

## <a name="next-steps"></a>Další kroky

* Obecnější informace o Hive v HDInsight, najdete [v tématu Použití Apache Hive s Apache Hadoop na HDInsight](hdinsight-use-hive.md)

* Další informace o dalších způsobech práce s Hadoopem na HDInsight najdete v tématu [Použití mapreduce s Apache Hadoop na HDInsightu](hdinsight-use-mapreduce.md)
