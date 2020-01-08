---
title: Použití Apache Beeline s Apache Hive – Azure HDInsight
description: Naučte se používat klienta Beeline ke spouštění dotazů na podregistr pomocí Hadoop v HDInsight. Beeline je nástroj pro práci s HiveServer2 nad JDBC.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: 39217a883863fd663b02cafea699dcbc4e070dfb
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75435729"
---
# <a name="use-the-apache-beeline-client-with-apache-hive"></a>Použití klienta Apache Beeline s Apache Hive

Naučte se používat [Apache Beeline](https://cwiki.apache.org/confluence/display/Hive/HiveServer2+Clients#HiveServer2Clients-Beeline–NewCommandLineShell) ke spouštění dotazů Apache Hive v HDInsight.

Beeline je klient podregistru, který je součástí hlavních uzlů clusteru HDInsight. Pokud chcete místně nainstalovat Beeline, přečtěte si článek [instalace klienta Beeline](#install-beeline-client)níže. Beeline používá JDBC pro připojení k HiveServer2, službě hostované v clusteru HDInsight. Beeline můžete použít také k vzdálenému přístupu k podregistru v HDInsight přes Internet. V následujících příkladech jsou uvedeny nejběžnější připojovací řetězce používané pro připojení ke službě HDInsight z Beeline:

## <a name="types-of-connections"></a>Typy připojení

### <a name="from-an-ssh-session"></a>Z relace SSH

Při připojování z relace SSH ke clusteru hlavnímu uzlu se můžete připojit k adrese `headnodehost` na portu `10001`:

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

Když se připojujete z klienta k clusteru Balíček zabezpečení podniku (ESP) připojenému k Azure Active Directory (AAD) – DS na počítači ve stejné sféře clusteru, musíte zadat taky název domény `<AAD-Domain>` a název účtu uživatele domény s oprávněními k přístupu ke clusteru `<username>`:

```bash
kinit <username>
beeline -u 'jdbc:hive2://<headnode-FQDN>:10001/default;principal=hive/_HOST@<AAD-Domain>;auth-kerberos;transportMode=http' -n <username>
```

Nahraďte `<username>` názvem účtu v doméně s oprávněními pro přístup ke clusteru. Nahraďte `<AAD-DOMAIN>` názvem Azure Active Directory (AAD), ke které je cluster připojený. Pro hodnotu `<AAD-DOMAIN>` použijte velká písmena, jinak se přihlašovací údaje nenašly. V případě potřeby ověřte `/etc/krb5.conf` názvů sféry.

---

### <a name="over-public-or-private-endpoints"></a>Přes veřejné nebo soukromé koncové body

Při připojování ke clusteru pomocí veřejných nebo privátních koncových bodů je nutné zadat název přihlašovacího účtu clusteru (výchozí `admin`) a heslo. Například pomocí Beeline z klientského systému se připojte k `clustername.azurehdinsight.net` adrese. Toto připojení se provádí prostřednictvím `443`portu a je šifrované pomocí protokolu SSL:

```bash
beeline -u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2' -n admin -p 'password'
```

nebo pro soukromý koncový bod:

```bash
beeline -u 'jdbc:hive2://clustername-int.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2' -n admin -p 'password'
```

Parametr `clustername` nahraďte názvem vašeho clusteru HDInsight. Nahraďte `admin` účtem přihlášení clusteru pro váš cluster. U clusterů ESP použijte úplný název uživatele (například user@domain.com). Nahraďte `password` heslem pro přihlašovací účet clusteru.

Soukromé koncové body odkazují na základní nástroj pro vyrovnávání zatížení, ke kterému se dá dostat jenom z partnerského vztahu virtuální sítě ve stejné oblasti. Další informace najdete v tématu [omezení globálních partnerských vztahů virtuální sítě a nástrojů pro vyrovnávání zatížení](../../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers) . Pomocí příkazu `curl` s možností `-v` můžete řešit problémy s připojením s veřejnými nebo soukromými koncovými body před použitím Beeline.

---

### <a id="sparksql"></a>Použití Beeline s Apache Spark

Apache Spark poskytuje vlastní implementaci HiveServer2, která se někdy označuje jako server Spark Thrift. Tato služba používá Spark SQL k překladu dotazů namísto podregistru a může poskytovat lepší výkon v závislosti na vašem dotazu.

#### <a name="through-public-or-private-endpoints"></a>Prostřednictvím veřejných nebo privátních koncových bodů

Použitý připojovací řetězec je trochu odlišný. Místo obsahující `httpPath=/hive2` `httpPath/sparkhive2`:

```bash
beeline -u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/sparkhive2' -n admin -p 'password'
```

nebo pro soukromý koncový bod:

```bash
beeline -u 'jdbc:hive2://clustername-int.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/sparkhive2' -n admin -p 'password'
```

Parametr `clustername` nahraďte názvem vašeho clusteru HDInsight. Nahraďte `admin` účtem přihlášení clusteru pro váš cluster. U clusterů ESP použijte úplný název uživatele (např. user@domain.com). Nahraďte `password` heslem pro přihlašovací účet clusteru.

Soukromé koncové body odkazují na základní nástroj pro vyrovnávání zatížení, ke kterému se dá dostat jenom z partnerského vztahu virtuální sítě ve stejné oblasti. Další informace najdete v tématu [omezení globálních partnerských vztahů virtuální sítě a nástrojů pro vyrovnávání zatížení](../../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers) . Pomocí příkazu `curl` s možností `-v` můžete řešit problémy s připojením s veřejnými nebo soukromými koncovými body před použitím Beeline.

---

#### <a name="from-cluster-head-or-inside-azure-virtual-network-with-apache-spark"></a>Z hlav clusteru nebo uvnitř Azure Virtual Network s Apache Spark

Při přímém připojení k hlavnímu uzlu clusteru nebo z prostředku ve stejné službě Azure Virtual Network jako cluster HDInsight by se měl `10002` portů místo `10001`použít pro server Spark Thrift. Následující příklad ukazuje, jak se připojit přímo k hlavnímu uzlu:

```bash
/usr/hdp/current/spark2-client/bin/beeline -u 'jdbc:hive2://headnodehost:10002/;transportMode=http'
```

---

## <a id="prereq"></a>Požadavky

* Cluster Hadoop ve službě HDInsight. Viz Začínáme [se službou HDInsight v systému Linux](./apache-hadoop-linux-tutorial-get-started.md).

* Všimněte si [schématu identifikátoru URI](../hdinsight-hadoop-linux-information.md#URI-and-scheme) pro primární úložiště vašeho clusteru. Například `wasb://` Azure Storage, `abfs://` pro Azure Data Lake Storage Gen2 nebo `adl://` pro Azure Data Lake Storage Gen1. Je-li pro Azure Storage povolen zabezpečený přenos, je identifikátor URI `wasbs://`. Další informace najdete v tématu [zabezpečený přenos](../../storage/common/storage-require-secure-transfer.md).

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

3. Příkazy Beeline začínají znakem `!`, například `!help` zobrazí nápovědu. Pro některé příkazy však může být `!` vynecháno. Například `help` také funguje.

    K dispozici je `!sql`, který se používá ke spouštění příkazů HiveQL. HiveQL je ale často používaný, takže můžete vynechat předchozí `!sql`. Následující dva příkazy jsou ekvivalentní:

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

    Tyto příkazy provedou následující akce:

    * `DROP TABLE` – Pokud tabulka existuje, je odstraněna.

    * `CREATE EXTERNAL TABLE` – vytvoří **externí** tabulku v podregistru. Externí tabulky ukládají pouze definici tabulky v podregistru. Data zůstanou v původním umístění.

    * `ROW FORMAT` – způsob formátování dat V tomto případě jsou pole v každém protokolu oddělená mezerou.

    * `STORED AS TEXTFILE LOCATION` – kde jsou uložena data a v jakém formátu souboru.

    * `SELECT` – vybere počet všech řádků, ve kterých sloupec **T4** obsahuje hodnotu **[Chyba]** . Tento dotaz vrátí hodnotu **3** , protože jsou tři řádky, které obsahují tuto hodnotu.

    * `INPUT__FILE__NAME LIKE '%.log'` – podregistr se pokusí použít schéma pro všechny soubory v adresáři. V tomto případě adresář obsahuje soubory, které neodpovídají schématu. Aby se zabránilo uvolňování dat ve výsledcích, tento příkaz oznamuje podregistru, že by měl vracet pouze data ze souborů končících log. log.

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

## <a name="run-a-hiveql-file"></a>Spuštění souboru HiveQL

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

    Tyto příkazy provedou následující akce:

   * **Create Table Pokud neexistuje** , pokud tabulka ještě neexistuje, vytvoří se. Vzhledem k tomu, že se klíčové slovo **External** nepoužívá, vytvoří tento příkaz interní tabulku. Interní tabulky jsou uložené v datovém skladu podregistru a jsou plně spravované podregistrem.
   * **Uloženo jako ORC** – ukládá data ve formátu optimalizovaného řádku (Orc). Formát ORC je vysoce optimalizovaný a efektivní formát pro ukládání dat z podregistru.
   * **Vložit přepsání... Vyberte možnost** – vybere řádky z tabulky **log4jLogs** , která obsahuje **[Error]** , a pak data vloží do **tabulky chyb** .

    > [!NOTE]  
    > Na rozdíl od externích tabulek odstraní interní tabulka také podkladová data.

3. Pokud chcete soubor uložit, použijte **Ctrl**+**X**, zadejte **Y**a nakonec **ENTER**.

4. K spuštění souboru pomocí Beeline použijte následující:

    ```bash
    beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http' -i query.hql
    ```

    > [!NOTE]  
    > Parametr `-i` spustí Beeline a spustí příkazy v souboru `query.hql`. Po dokončení dotazu přijdete na `jdbc:hive2://headnodehost:10001/>`ovou výzvu. Můžete také spustit soubor pomocí parametru `-f`, který ukončí Beeline po dokončení dotazu.

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
        3 rows selected (0.813 seconds)

## <a name="install-beeline-client"></a>Nainstalovat klienta Beeline

I když je Beeline obsažený v hlavních uzlech clusteru HDInsight, možná ho budete chtít nainstalovat na místní počítač.  Následující kroky pro instalaci Beeline na místním počítači jsou založené na subsystému [Windows pro Linux](https://docs.microsoft.com/windows/wsl/install-win10).

1. Aktualizujte seznamy balíčků. Do prostředí bash zadejte následující příkaz:

    ```bash
    sudo apt-get update
    ```

1. Pokud není nainstalovaný, nainstalujte Java. Můžete se podívat pomocí příkazu `which java`.

    1. Pokud není nainstalován žádný balíček Java, zadejte následující příkaz:

        ```bash
        sudo apt install openjdk-11-jre-headless
        ```

    1. Opravte soubor bashrc (obvykle se nachází v ~/.bashrc). Otevřete soubor pomocí `nano ~/.bashrc` a na konci souboru přidejte následující řádek:

        ```bash
        export JAVA_HOME=/usr/lib/jvm/java-1.11.0-openjdk-amd64
        ```

        Pak stiskněte klávesy **CTRL + X**, pak **Y**a potom zadejte.

1. Stáhněte si archivy Hadoop a Beeline, zadejte následující příkazy:

    ```bash
    wget https://archive.apache.org/dist/hadoop/core/hadoop-2.7.3/hadoop-2.7.3.tar.gz
    wget https://archive.apache.org/dist/hive/hive-1.2.1/apache-hive-1.2.1-bin.tar.gz
    ```

1. Rozbalte archivy a zadejte následující příkazy:

    ```bash
    tar -xvzf hadoop-2.7.3.tar.gz
    tar -xvzf apache-hive-1.2.1-bin.tar.gz
    ```

1. Dále opravte soubor bashrc. Budete muset určit cestu, kam se archivy nebalí. Pokud používáte [subsystém Windows pro Linux](https://docs.microsoft.com/windows/wsl/install-win10)a provedli jste přesně tento postup, vaše cesta by byla `/mnt/c/Users/user/`, kde `user` je vaše uživatelské jméno.

    1. Otevřete soubor: `nano ~/.bashrc`
    1. Níže uvedené příkazy upravte podle příslušné cesty a pak je zadejte na konci souboru bashrc:

        ```bash
        export HADOOP_HOME=/$(path_where_the_archives_were_unpacked)/hadoop-2.7.3
        export HIVE_HOME=/$(path_where_the_archives_were_unpacked)/apache-hive-1.2.1-bin
        PATH=$PATH:$HIVE_HOME/bin
        ```

    1. Pak stiskněte klávesy **CTRL + X**, pak **Y**a potom zadejte.

1. Zavřete a znovu otevřete relaci bash.

1. Otestujte připojení. Použijte formát připojení z [více než veřejných nebo privátních koncových bodů](#over-public-or-private-endpoints)výše.

## <a name="next-steps"></a>Další kroky

* Obecnější informace o podregistru v HDInsight najdete v tématu [použití Apache Hive s Apache Hadoop v HDInsight](hdinsight-use-hive.md) .

* Další informace o dalších způsobech práce se systémem Hadoop ve službě HDInsight najdete v tématu [použití MapReduce s Apache Hadoop v HDInsight](hdinsight-use-mapreduce.md) .
