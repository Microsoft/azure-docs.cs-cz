---
title: Hromadné načítání do Apache Phoenix pomocí psql - Azure HDInsight
description: Použití nástroje psql k načtení hromadných načtení dat do tabulek Apache Phoenix v Azure HDInsight
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/17/2019
ms.openlocfilehash: 845c4a62aee04a8acdc645ba4c41f1f5496537c3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75552606"
---
# <a name="bulk-load-data-into-apache-phoenix-using-psql"></a>Hromadné načtení dat do Apache Phoenixu s využitím psql

[Apache Phoenix](https://phoenix.apache.org/) je open source, masivně paralelní relační databáze postavená na [Apache HBase](../hbase/apache-hbase-overview.md). Phoenix poskytuje dotazy podobné SQL přes HBase. Phoenix používá ovladače JDBC, které uživatelům umožňují vytvářet, odstraňovat a měnit tabulky SQL, indexy, zobrazení a sekvence a řádky upsert jednotlivě a hromadně. Phoenix používá noSQL nativní kompilaci spíše než pomocí MapReduce ke kompilaci dotazů, k vytvoření aplikací s nízkou latencí nad HBase. Phoenix přidává koprocesory pro podporu spuštění kódu dodaného klientem v adresním prostoru serveru, provádění kódu spoluumístěné s daty. Tím se minimalizuje přenos dat klient/server.  Chcete-li pracovat s daty pomocí Phoenix v HDInsight, nejprve vytvořte tabulky a načtěte data do nich.

## <a name="bulk-loading-with-apache-phoenix"></a>Hromadné načítání s Apache Phoenix

Existuje několik způsobů, jak získat data do HBase, včetně použití rozhraní API klienta, úlohy MapReduce s TableOutputFormat nebo ručního zadávání dat pomocí prostředí HBase. Phoenix poskytuje dvě metody pro načítání dat CSV do `psql`phoenixtovacích tabulek: nástroj pro načítání klienta s názvem a nástroj hromadného načítání založený na MapReduce.

Nástroj `psql` je jednovláknový a je nejvhodnější pro načítání megabajtů nebo gigabajtů dat. Všechny soubory CSV, které mají být načteny, musí mít příponu souboru '.csv'.  Můžete také zadat soubory skriptu SQL v příkazovém `psql` řádku s příponou souboru .sql.

Hromadné načítání s MapReduce se používá pro mnohem větší datové svazky, obvykle v produkčních scénářích, jako MapReduce používá více vláken.

Než začnete načítat data, ověřte, zda je povolena phoenix a že nastavení časového limitu dotazu jsou podle očekávání.  Získejte přístup k řídicímu panelu [Apache Ambari](https://ambari.apache.org/) clusteru HDInsight, vyberte HBase a pak na kartu Konfigurace.  Posuňte se dolů `enabled` a ověřte, zda je Apache Phoenix nastaven tak, jak je znázorněno:

![Nastavení clusteru Apache Phoenix HDInsight](./media/apache-hbase-phoenix-psql/apache-ambari-phoenix.png)

### <a name="use-psql-to-bulk-load-tables"></a>Slouží `psql` k hromadným načtením tabulek

1. Vytvořte soubor `createCustomersTable.sql`s názvem a zkopírujte níže uvedený kód do souboru. Pak tento soubor uložte a zavřete.

    ```sql
    CREATE TABLE Customers (
        ID varchar NOT NULL PRIMARY KEY,
        Name varchar,
        Income decimal,
        Age INTEGER,
        Country varchar);
    ```

1. Vytvořte soubor `listCustomers.sql`s názvem a zkopírujte níže uvedený kód do souboru. Pak tento soubor uložte a zavřete.

    ```sql
    SELECT * from Customers;
    ```

1. Vytvořte soubor `customers.csv`s názvem a zkopírujte níže uvedený kód do souboru. Pak tento soubor uložte a zavřete.

    ```txt
    1,Samantha,260000.0,18,US
    2,Sam,10000.5,56,US
    3,Anton,550150.0,42,Norway
    ```

1. Vytvořte soubor `customers2.csv`s názvem a zkopírujte níže uvedený kód do souboru. Pak tento soubor uložte a zavřete.

    ```txt
    4,Nicolle,180000.0,22,US
    5,Kate,210000.5,24,Canada
    6,Ben,45000.0,32,Poland
    ```

1. Otevřete příkazový řádek a změňte adresář na umístění nově vytvořených souborů. Nahraďte název_clusteru CLUSTERNAME níže skutečným názvem clusteru HBase. Poté spusťte kód pro nahrání souborů do hlavního uzlu clusteru:

    ```cmd
    scp customers.csv customers2.csv createCustomersTable.sql listCustomers.sql sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/tmp
    ```

1. Pomocí [příkazu ssh](../hdinsight-hadoop-linux-use-ssh-unix.md) se připojte ke clusteru. Upravte níže uvedený příkaz nahrazením názvu clusteru názvem clusteru a zadejte příkaz:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Z relace ssh změňte adresář na umístění nástroje **psql.** Proveďte následující příkaz:

    ```bash
    cd /usr/hdp/current/phoenix-client/bin
    ```

1. Hromadné načtení dat. Níže uvedený kód vytvoří tabulku **Zákazníci** a potom data nahraje.

    ```bash
    python psql.py /tmp/createCustomersTable.sql /tmp/customers.csv
    ```

    Po `psql` dokončení operace byste měli vidět zprávu podobnou takto:

    ```output
    csv columns from database.
    CSV Upsert complete. 3 rows upserted
    Time: 0.081 sec(s)
    ```

1. Můžete pokračovat `psql` v zobrazení obsahu tabulky Zákazníci. Spusťte níže uvedený kód:

    ```bash
    python psql.py /tmp/listCustomers.sql
    ```

    Případně můžete použít [HBase shell](./query-hbase-with-hbase-shell.md), nebo [Apache Zeppelin](./apache-hbase-phoenix-zeppelin.md) k dotazování na data.

1. Nahrajte další data. Nyní, když tabulka již existuje, příkaz určuje tabulku. Proveďte následující příkaz:

    ```bash
    python psql.py -t CUSTOMERS /tmp/customers2.csv
    ```

## <a name="use-mapreduce-to-bulk-load-tables"></a>Použití mapreduce k hromadnému načtení tabulek

Pro načítání s vyšší propustností distribuované v clusteru použijte nástroj MapReduce load. Tento zavaděč nejprve převede všechna data do HFiles a pak poskytuje vytvořené HFiles na HBase.

1. Tato část pokračuje s ssh relace a objekty vytvořené dříve. Pomocí výše **uvedených** kroků vytvořte tabulku Zákazníci a soubor **customers.csv** podle potřeby. V případě potřeby znovu navázat ssh připojení.

1. Zkrátit obsah tabulky **Zákazníci.** Z otevřené relace ssh spusťte níže uvedené příkazy:

    ```bash
    hbase shell
    truncate 'CUSTOMERS'
    exit
    ```

1. Zkopírujte `customers.csv` soubor z hlavního uzlu do Azure Storage.

    ```bash
    hdfs dfs -put /tmp/customers.csv wasbs:///tmp/customers.csv
    ```

1. Změna adresáře spuštění pro příkaz MapReduce hromadného načtení:

    ```bash
    cd /usr/hdp/current/phoenix-client
    ```

1. Spusťte nakladač CSV `hadoop` MapReduce pomocí příkazu s nádobou klienta Phoenix:

    ```bash
    HADOOP_CLASSPATH=/usr/hdp/current/hbase-client/lib/hbase-protocol.jar:/etc/hbase/conf hadoop jar phoenix-client.jar org.apache.phoenix.mapreduce.CsvBulkLoadTool --table Customers --input /tmp/customers.csv
    ```

    Po dokončení nahrávání byste měli vidět zprávu podobnou takto:

    ```output
    19/12/18 18:30:57 INFO client.ConnectionManager$HConnectionImplementation: Closing master protocol: MasterService
    19/12/18 18:30:57 INFO client.ConnectionManager$HConnectionImplementation: Closing zookeeper sessionid=0x26f15dcceff02c3
    19/12/18 18:30:57 INFO zookeeper.ZooKeeper: Session: 0x26f15dcceff02c3 closed
    19/12/18 18:30:57 INFO zookeeper.ClientCnxn: EventThread shut down
    19/12/18 18:30:57 INFO mapreduce.AbstractBulkLoadTool: Incremental load complete for table=CUSTOMERS
    19/12/18 18:30:57 INFO mapreduce.AbstractBulkLoadTool: Removing output directory /tmp/50254426-aba6-400e-88eb-8086d3dddb6
    ```

1. Pokud chcete použít MapReduce s Azure Data Lake Storage, vyhledejte kořenový adresář Úložiště datových jezer, což je `hbase.rootdir` hodnota v . `hbase-site.xml` V následujícím příkazu je `adl://hdinsightconf1.azuredatalakestore.net:443/hbase1`kořenový adresář úložiště datového jezera . V tomto příkazu zadejte vstupní a výstupní složky úložiště datových jezer jako parametry:

    ```bash
    cd /usr/hdp/current/phoenix-client

    $HADOOP_CLASSPATH=$(hbase mapredcp):/etc/hbase/conf hadoop jar /usr/hdp/2.4.2.0-258/phoenix/phoenix-4.4.0.2.4.2.0-258-client.jar

    org.apache.phoenix.mapreduce.CsvBulkLoadTool --table Customers --input adl://hdinsightconf1.azuredatalakestore.net:443/hbase1/data/hbase/temp/input/customers.csv –zookeeper ZookeeperQuorum:2181:/hbase-unsecure --output  adl://hdinsightconf1.azuredatalakestore.net:443/hbase1/data/hbase/output1
    ```

1. Chcete-li dotaz a zobrazit data, můžete použít **psql,** jak je popsáno výše. Můžete také použít [HBase shell](./query-hbase-with-hbase-shell.md), nebo [Apache Zeppelin](./apache-hbase-phoenix-zeppelin.md).

## <a name="recommendations"></a>Doporučení

* Stejné paměťové médium používejte pro vstupní i výstupní složky, azure storage (WASB) nebo Azure Data Lake Storage (ADL). Chcete-li přenášet data z Azure Storage `distcp` do úložiště datových jezer, můžete použít příkaz:

    ```bash
    hadoop distcp wasb://@.blob.core.windows.net/example/data/gutenberg adl://.azuredatalakestore.net:443/myfolder
    ```

* Použijte větší pracovní uzly. Mapové procesy hromadného kopírování MapReduce vytvářejí velké množství dočasného výstupu, které zaplní dostupné místo bez dfs. Pro velké množství hromadné načítání použijte více pracovních uzlů větší velikosti. Počet pracovních uzlů, které přidělujete clusteru, přímo ovlivňuje rychlost zpracování.

* Rozdělit vstupní soubory na ~10-GB bloky. Hromadné načítání je operace náročná na úložiště, takže rozdělení vstupních souborů do více bloků dat má za následek lepší výkon.

* Vyhněte se oblastní server hotspoty. Pokud je klíč řádku monotonically zvýšení, HBase sekvenční zápisy může vyvolat oblast serveru hotspotting. *Solení* řádku klíč snižuje sekvenční zápisy. Phoenix poskytuje způsob, jak transparentně sůl řádek klíč s solení bajt pro konkrétní tabulku, jak je uvedeno níže.

## <a name="next-steps"></a>Další kroky

* [Hromadné načítání dat s Apache Phoenix](https://phoenix.apache.org/bulk_dataload.html)
* [Používejte Apache Phoenix s linuxovými clustery Apache HBase v HDInsightu](../hbase/apache-hbase-query-with-phoenix.md)
* [Slané stoly](https://phoenix.apache.org/salted.html)
* [Apache Phoenix gramatika](https://phoenix.apache.org/language/index.html)
