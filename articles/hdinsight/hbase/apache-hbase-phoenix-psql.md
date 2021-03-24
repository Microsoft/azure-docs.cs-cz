---
title: Hromadné načítání do Apache Phoenix s využitím psql – Azure HDInsight
description: Načtení dat hromadného načtení do Apache Phoenix tabulek v Azure HDInsight pomocí nástroje psql
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/17/2019
ms.openlocfilehash: d1ddf69acda442212500200eb6dc326dcbcb3c1b
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2021
ms.locfileid: "104867793"
---
# <a name="bulk-load-data-into-apache-phoenix-using-psql"></a>Hromadné načtení dat do Apache Phoenixu s využitím psql

[Apache Phoenix](https://phoenix.apache.org/) je open source, široce paralelní relační databáze postavená na [Apache HBA](../hbase/apache-hbase-overview.md). Phoenix poskytuje dotazy podobné SQL přes HBA. Phoenix používá ovladače JDBC k tomu, aby uživatelům umožnila vytvářet, odstraňovat a měnit tabulky, indexy, zobrazení a sekvence SQL a Upsert řádky jednotlivě a hromadně. Phoenix používá nativní kompilaci noSQL namísto použití MapReduce ke kompilaci dotazů a k vytváření aplikací s nízkou latencí nad adaptéry HBA. Phoenix přidává spoluprocesory k podpoře spouštění kódu zadaného klientem v adresním prostoru serveru a provádění kódu společně umístěného s daty. Tím se minimalizuje přenos dat mezi klientem a serverem.  Chcete-li pracovat s daty pomocí nástroje Phoenix v HDInsight, nejprve vytvořte tabulky a potom do nich načtěte data.

## <a name="bulk-loading-with-apache-phoenix"></a>Hromadné načítání pomocí Apache Phoenix

Existuje několik způsobů, jak získat data do adaptérů HBA, včetně použití klientských rozhraní API, úlohy MapReduce s TableOutputFormat, nebo vložení dat ručně pomocí prostředí HBA. Phoenix poskytuje dvě metody pro načítání dat CSV do tabulek Phoenix: Nástroj pro načítání klientů s názvem `psql` a nástroj pro hromadné načítání založené na MapReduce.

Tento `psql` Nástroj je založený na jednom vlákně a je nejvhodnější pro načítání megabajtů a gigabajtů dat. Všechny soubory CSV, které mají být načteny, musí mít příponu souboru. csv.  Soubory skriptu SQL můžete zadat také na `psql` příkazovém řádku s příponou souboru. SQL.

Hromadné načítání pomocí MapReduce se používá pro mnohem větší objemy dat, obvykle v produkčních scénářích, protože MapReduce používá více vláken.

Než začnete s načtením dat, ověřte, že je povolená možnost Phoenix a že nastavení časového limitu dotazu jsou očekávaná.  Přejděte na řídicí panel [Apache Ambari](https://ambari.apache.org/) clusteru HDInsight, vyberte adaptéry HBA a pak kartu konfigurace.  Posuňte se dolů a ověřte, že Apache Phoenix je nastavená na `enabled` jak je znázorněno níže:

:::image type="content" source="./media/apache-hbase-phoenix-psql/apache-ambari-phoenix.png" alt-text="Apache Phoenix nastavení clusteru HDInsight" border="true":::

### <a name="use-psql-to-bulk-load-tables"></a>Použít `psql` k hromadnému načítání tabulek

1. Vytvořte soubor s názvem `createCustomersTable.sql` a zkopírujte níže uvedený kód do souboru. Pak soubor uložte a zavřete ho.

    ```sql
    CREATE TABLE Customers (
        ID varchar NOT NULL PRIMARY KEY,
        Name varchar,
        Income decimal,
        Age INTEGER,
        Country varchar);
    ```

1. Vytvořte soubor s názvem `listCustomers.sql` a zkopírujte níže uvedený kód do souboru. Pak soubor uložte a zavřete ho.

    ```sql
    SELECT * from Customers;
    ```

1. Vytvořte soubor s názvem `customers.csv` a zkopírujte níže uvedený kód do souboru. Pak soubor uložte a zavřete ho.

    ```txt
    1,Samantha,260000.0,18,US
    2,Sam,10000.5,56,US
    3,Anton,550150.0,42,Norway
    ```

1. Vytvořte soubor s názvem `customers2.csv` a zkopírujte níže uvedený kód do souboru. Pak soubor uložte a zavřete ho.

    ```txt
    4,Nicolle,180000.0,22,US
    5,Kate,210000.5,24,Canada
    6,Ben,45000.0,32,Poland
    ```

1. Otevřete příkazový řádek a změňte adresář na umístění nově vytvořených souborů. V poli název clusteru nahraďte skutečným názvem vašeho clusteru HBA. Pak spusťte kód pro nahrání souborů do hlavnímu uzlu clusteru:

    ```cmd
    scp customers.csv customers2.csv createCustomersTable.sql listCustomers.sql sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/tmp
    ```

1. Připojte se ke clusteru pomocí [příkazu SSH](../hdinsight-hadoop-linux-use-ssh-unix.md) . Níže uvedený příkaz upravte tak, že ho nahradíte názvem clusteru a pak zadáte tento příkaz:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. V relaci SSH změňte adresář na umístění nástroje **psql** . Spusťte následující příkaz:

    ```bash
    cd /usr/hdp/current/phoenix-client/bin
    ```

1. Hromadně načtěte data. Následující kód vytvoří tabulku **Customers** a pak data nahrajte.

    ```bash
    python psql.py /tmp/createCustomersTable.sql /tmp/customers.csv
    ```

    Po `psql` dokončení operace by se měla zobrazit zpráva podobná následující:

    ```output
    csv columns from database.
    CSV Upsert complete. 3 rows upserted
    Time: 0.081 sec(s)
    ```

1. Můžete pokračovat `psql` v používání k zobrazení obsahu tabulky Customers. Spusťte následující kód:

    ```bash
    python psql.py /tmp/listCustomers.sql
    ```

    Alternativně můžete k dotazování na data použít [prostředí HBA](./query-hbase-with-hbase-shell.md)nebo [Apache Zeppelin](./apache-hbase-phoenix-zeppelin.md) .

1. Nahrajte další data. Nyní, když tabulka již existuje, příkaz tuto tabulku určí. Spusťte následující příkaz:

    ```bash
    python psql.py -t CUSTOMERS /tmp/customers2.csv
    ```

## <a name="use-mapreduce-to-bulk-load-tables"></a>Použití MapReduce k hromadnému načítání tabulek

Pro nahrání vyšší propustnosti distribuované přes cluster použijte nástroj pro načtení MapReduce. Tento zavaděč nejprve převede všechna data na HFiles a potom poskytne vytvořenou HFiles pro adaptéry HBA.

1. Tato část pokračuje v relaci SSH a objekty, které jste vytvořili dříve. Vytvořte tabulku **Customers** a **customers.csv** soubor podle potřeby pomocí výše uvedeného postupu. V případě potřeby znovu vytvořte připojení SSH.

1. Ořízne obsah tabulky **Customers** . V otevřené relaci SSH spusťte následující příkazy:

    ```bash
    hbase shell
    truncate 'CUSTOMERS'
    exit
    ```

1. Zkopírujte `customers.csv` soubor z hlavnímu uzlu do Azure Storage.

    ```bash
    hdfs dfs -put /tmp/customers.csv wasbs:///tmp/customers.csv
    ```

1. Přejděte do spouštěcího adresáře pro příkaz MapReduce Bulk Load:

    ```bash
    cd /usr/hdp/current/phoenix-client
    ```

1. Spusťte zavaděč sdíleného svazku clusteru MapReduce pomocí `hadoop` příkazu s jar klienta v Phoenixu:

    ```bash
    HADOOP_CLASSPATH=/usr/hdp/current/hbase-client/lib/hbase-protocol.jar:/etc/hbase/conf hadoop jar phoenix-client.jar org.apache.phoenix.mapreduce.CsvBulkLoadTool --table Customers --input /tmp/customers.csv
    ```

    Po dokončení nahrávání by se měla zobrazit zpráva podobná následující:

    ```output
    19/12/18 18:30:57 INFO client.ConnectionManager$HConnectionImplementation: Closing master protocol: MasterService
    19/12/18 18:30:57 INFO client.ConnectionManager$HConnectionImplementation: Closing zookeeper sessionid=0x26f15dcceff02c3
    19/12/18 18:30:57 INFO zookeeper.ZooKeeper: Session: 0x26f15dcceff02c3 closed
    19/12/18 18:30:57 INFO zookeeper.ClientCnxn: EventThread shut down
    19/12/18 18:30:57 INFO mapreduce.AbstractBulkLoadTool: Incremental load complete for table=CUSTOMERS
    19/12/18 18:30:57 INFO mapreduce.AbstractBulkLoadTool: Removing output directory /tmp/50254426-aba6-400e-88eb-8086d3dddb6
    ```

1. Pokud chcete použít MapReduce s Azure Data Lake Storage, vyhledejte Data Lake Storage kořenový adresář, což je `hbase.rootdir` hodnota v `hbase-site.xml` . V následujícím příkazu je Data Lake Storage kořenový adresář `adl://hdinsightconf1.azuredatalakestore.net:443/hbase1` . V tomto příkazu zadejte vstupní a výstupní složky Data Lake Storage jako parametry:

    ```bash
    cd /usr/hdp/current/phoenix-client

    $HADOOP_CLASSPATH=$(hbase mapredcp):/etc/hbase/conf hadoop jar /usr/hdp/2.4.2.0-258/phoenix/phoenix-4.4.0.2.4.2.0-258-client.jar

    org.apache.phoenix.mapreduce.CsvBulkLoadTool --table Customers --input adl://hdinsightconf1.azuredatalakestore.net:443/hbase1/data/hbase/temp/input/customers.csv –zookeeper ZookeeperQuorum:2181:/hbase-unsecure --output  adl://hdinsightconf1.azuredatalakestore.net:443/hbase1/data/hbase/output1
    ```

1. K dotazování a zobrazení dat můžete použít **psql** , jak je popsáno výše. Můžete také použít [prostředí HBA](./query-hbase-with-hbase-shell.md)nebo [Apache Zeppelin](./apache-hbase-phoenix-zeppelin.md).

## <a name="recommendations"></a>Doporučení

* Pro vstupní i výstupní složky použijte stejné paměťové médium, buď Azure Storage (WASB) nebo Azure Data Lake Storage (ADL). Chcete-li přenést data z Azure Storage do Data Lake Storage, můžete použít `distcp` příkaz:

    ```bash
    hadoop distcp wasb://@.blob.core.windows.net/example/data/gutenberg adl://.azuredatalakestore.net:443/myfolder
    ```

* Použijte pracovní uzly větší velikosti. Mapové procesy MapReduce hromadné kopie vytvářejí velké objemy dočasného výstupu, které naplňují dostupný prostor, který není DFS. Pro velké množství hromadných načtení použijte více a větší pracovní uzly. Počet pracovních uzlů přidělených vašemu clusteru má přímý vliv na rychlost zpracování.

* Oddělte vstupní soubory do bloků o velikosti až 10 GB. Hromadné načítání je operace náročná na úložiště, takže rozdělení vstupních souborů do více bloků má za následek lepší výkon.

* Vyhněte se hotspotům serveru oblastí. Pokud je váš klíč řádku rovnoměrně zvětšujícíý, můžou se sekvenční zápisy vyvolávat v oblasti Server hotspotting. *Nasolení* klíče řádku zkracuje sekvenční zápis. Phoenix poskytuje způsob, jak transparentně nasolete klíč řádku s použitím dvoubajtového bajtu pro konkrétní tabulku, jak je odkazováno níže.

## <a name="next-steps"></a>Další kroky

* [Hromadné načítání dat pomocí Apache Phoenix](https://phoenix.apache.org/bulk_dataload.html)
* [Použití Apache Phoenix s clustery Apache HBA založenými na systému Linux v HDInsight](../hbase/apache-hbase-query-with-phoenix.md)
* [Nasolené tabulky](https://phoenix.apache.org/salted.html)
* [Gramatika Apache Phoenix](https://phoenix.apache.org/language/index.html)
