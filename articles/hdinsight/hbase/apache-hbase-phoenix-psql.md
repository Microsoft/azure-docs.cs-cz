---
title: Hromadné načítání do Apache Phoenix s využitím psql – Azure HDInsight
description: Načtení dat hromadného načtení do Apache Phoenix tabulek v Azure HDInsight pomocí nástroje psql
author: ashishthaps
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/10/2017
ms.author: ashishth
ms.openlocfilehash: f00f6bcf07cbdc5aeaeb04aeccf7e88cf4822dbf
ms.sourcegitcommit: 9dec0358e5da3ceb0d0e9e234615456c850550f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/14/2019
ms.locfileid: "72311706"
---
# <a name="bulk-load-data-into-apache-phoenix-using-psql"></a>Hromadné načtení dat do Apache Phoenixu s využitím psql

[Apache Phoenix](https://phoenix.apache.org/) je open source, široce paralelní relační databáze postavená na [Apache HBA](../hbase/apache-hbase-overview.md). Phoenix poskytuje dotazy podobné SQL přes HBA. Phoenix používá ovladače JDBC k tomu, aby uživatelům umožnila vytvářet, odstraňovat a měnit tabulky, indexy, zobrazení a sekvence SQL a Upsert řádky jednotlivě a hromadně. Phoenix používá nativní kompilaci noSQL namísto použití MapReduce ke kompilaci dotazů a k vytváření aplikací s nízkou latencí nad adaptéry HBA. Phoenix přidá spoluprocesory pro podporu spouštění kódu zadaného klientem v adresním prostoru serveru a provádění kódu společně umístěného s daty. Tím se minimalizuje přenos dat mezi klientem a serverem.  Chcete-li pracovat s daty pomocí nástroje Phoenix v HDInsight, nejprve vytvořte tabulky a potom do nich načtěte data.

## <a name="bulk-loading-with-apache-phoenix"></a>Hromadné načítání pomocí Apache Phoenix

Existuje několik způsobů, jak získat data do adaptérů HBA, včetně použití klientských rozhraní API, úlohy MapReduce s TableOutputFormat, nebo vložení dat ručně pomocí prostředí HBA. Phoenix poskytuje dvě metody pro načítání dat CSV do tabulek Phoenix: Nástroj pro načítání klientů s názvem `psql` a nástroj hromadného načtení založeného na MapReduce.

Nástroj `psql` je tvořen jedním vláknem a je nejvhodnější pro načítání megabajtů a gigabajtů dat. Všechny soubory CSV, které mají být načteny, musí mít příponu souboru. csv.  Soubory skriptu SQL můžete zadat také v příkazovém řádku `psql` s příponou souboru. SQL.

Hromadné načítání pomocí MapReduce se používá pro mnohem větší objemy dat, obvykle v produkčních scénářích, protože MapReduce používá více vláken.

Než začnete s načtením dat, ověřte, že je povolená možnost Phoenix a že nastavení časového limitu dotazu jsou očekávaná.  Přejděte na řídicí panel [Apache Ambari](https://ambari.apache.org/) clusteru HDInsight, vyberte adaptéry HBA a pak kartu konfigurace.  Posuňte se dolů a ověřte, že Apache Phoenix je nastavená na `enabled`, jak je znázorněno níže:

![Apache Phoenix nastavení clusteru HDInsight](./media/apache-hbase-phoenix-psql/apache-ambari-phoenix.png)

### <a name="use-psql-to-bulk-load-tables"></a>Pro hromadné načítání tabulek použít `psql`

1. Vytvořte novou tabulku a pak dotaz uložte s názvem filename `createCustomersTable.sql`.

    ```sql
    CREATE TABLE Customers (
        ID varchar NOT NULL PRIMARY KEY,
        Name varchar,
        Income decimal,
        Age INTEGER,
        Country varchar);
    ```

2. Zkopírujte soubor CSV (příklad zobrazeného obsahu) jako `customers.csv` do adresáře `/tmp/` pro načtení do nově vytvořené tabulky.  Pomocí příkazu `hdfs` zkopírujte soubor CSV do požadovaného zdrojového umístění.

    ```
    1,Samantha,260000.0,18,US
    2,Sam,10000.5,56,US
    3,Anton,550150.0,Norway
    ... 4997 more rows 
    ```

    ```bash
    hdfs dfs -copyToLocal /example/data/customers.csv /tmp/
    ```

3. Vytvořte dotaz SQL SELECT a ověřte, zda jsou správně načtena vstupní data, a pak dotaz uložte s názvem filename `listCustomers.sql`. Můžete použít libovolný dotaz SQL.
     ```sql
    SELECT Name, Income from Customers group by Country;
    ```

4. Hromadně načtěte data otevřením *nového* okna příkazového řádku Hadoop. Nejprve změňte umístění spouštěcího adresáře pomocí příkazu `cd` a pak použijte nástroj `psql` (příkaz Python `psql.py`). 

    Následující příklad očekává, že jste zkopírovali soubor `customers.csv` z účtu úložiště do místního dočasného adresáře pomocí `hdfs` jako v kroku 2 výše.

    ```bash
    cd /usr/hdp/current/phoenix-client/bin

    python psql.py ZookeeperQuorum createCustomersTable.sql /tmp/customers.csv listCustomers.sql
    ```

    > [!NOTE]   
    > Název `ZookeeperQuorum` určíte tak, že v souboru vyhledáte řetězec [Apache Zookeeper](https://zookeeper.apache.org/) kvora `/etc/hbase/conf/hbase-site.xml` s názvem vlastnosti `hbase.zookeeper.quorum`.

5. Po dokončení operace `psql` by se měla zobrazit zpráva v příkazovém okně:

    ```
    CSV Upsert complete. 5000 rows upserted
    Time: 4.548 sec(s)
    ```

## <a name="use-mapreduce-to-bulk-load-tables"></a>Použití MapReduce k hromadnému načítání tabulek

Pro nahrání vyšší propustnosti distribuované přes cluster použijte nástroj pro načtení MapReduce. Tento zavaděč nejprve převede všechna data na HFiles a potom poskytne vytvořenou HFiles pro adaptéry HBA.

1. Spusťte zavaděč sdíleného svazku clusteru MapReduce pomocí příkazu `hadoop` s jar klienta v Phoenixu:

    ```bash
    hadoop jar phoenix-<version>-client.jar org.apache.phoenix.mapreduce.CsvBulkLoadTool --table CUSTOMERS --input /data/customers.csv
    ```

2. Vytvoří novou tabulku s příkazem SQL, jako s `CreateCustomersTable.sql` v předchozím kroku 1.

3. Chcete-li ověřit schéma tabulky, spusťte `!describe inputTable`.

4. Určete cestu k umístění vstupních dat, jako je například ukázkový soubor `customers.csv`. Vstupní soubory můžou být ve vašem účtu úložiště WASB/ADLS. V tomto ukázkovém scénáři se vstupní soubory nacházejí v adresáři `<storage account parent>/inputFolderBulkLoad`.

5. Přejděte do spouštěcího adresáře pro příkaz MapReduce Bulk Load:

    ```bash
    cd /usr/hdp/current/phoenix-client/bin
    ```

6. Vyhledejte hodnotu `ZookeeperQuorum` v `/etc/hbase/conf/hbase-site.xml` s názvem vlastnosti `hbase.zookeeper.quorum`.

7. Nastavte cestu k cestě a spusťte příkaz nástroje `CsvBulkLoadTool`:

    ```bash
    /usr/hdp/current/phoenix-client$ HADOOP_CLASSPATH=/usr/hdp/current/hbase-client/lib/hbase-protocol.jar:/etc/hbase/conf hadoop jar /usr/hdp/2.4.2.0-258/phoenix/phoenix-4.4.0.2.4.2.0-258-client.jar

    org.apache.phoenix.mapreduce.CsvBulkLoadTool --table Customers --input /inputFolderBulkLoad/customers.csv –zookeeper ZookeeperQuorum:2181:/hbase-unsecure
    ```

8. Pokud chcete použít MapReduce s Azure Data Lake Storage, vyhledejte Data Lake Storage kořenový adresář, což je hodnota `hbase.rootdir` v `hbase-site.xml`. V následujícím příkazu je Data Lake Storage kořenový adresář `adl://hdinsightconf1.azuredatalakestore.net:443/hbase1`. V tomto příkazu zadejte vstupní a výstupní složky Data Lake Storage jako parametry:

    ```bash
    cd /usr/hdp/current/phoenix-client

    $HADOOP_CLASSPATH=$(hbase mapredcp):/etc/hbase/conf hadoop jar /usr/hdp/2.4.2.0-258/phoenix/phoenix-4.4.0.2.4.2.0-258-client.jar

    org.apache.phoenix.mapreduce.CsvBulkLoadTool --table Customers --input adl://hdinsightconf1.azuredatalakestore.net:443/hbase1/data/hbase/temp/input/customers.csv –zookeeper ZookeeperQuorum:2181:/hbase-unsecure --output  adl://hdinsightconf1.azuredatalakestore.net:443/hbase1/data/hbase/output1
    ```

## <a name="recommendations"></a>Doporučení

* Pro vstupní i výstupní složky použijte stejné paměťové médium, buď Azure Storage (WASB) nebo Azure Data Lake Storage (ADL). Pro přenos dat z Azure Storage do Data Lake Storage můžete použít příkaz `distcp`:

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
