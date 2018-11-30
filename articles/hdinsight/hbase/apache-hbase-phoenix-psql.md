---
title: Hromadné načítání do Apache Phoenixu s využitím psql – Azure HDInsight
description: Pomocí nástroje psql načítání hromadné načtení dat do Phoenixu tabulek.
services: hdinsight
author: ashishthaps
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/10/2017
ms.author: ashishth
ms.openlocfilehash: 8b14550adf89f866cf3b736db049cc671db5b765
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/26/2018
ms.locfileid: "52314503"
---
# <a name="bulk-load-data-into-apache-phoenix-using-psql"></a>Hromadné načtení dat do Apache Phoenixu s využitím psql

[Apache Phoenix](http://phoenix.apache.org/) je masivně paralelní relační databáze založená na open source [Apache HBase](../hbase/apache-hbase-overview.md). Phoenix nabízí podobném SQL dotazy nad HBase. Phoenix používá ovladače JDBC umožňuje uživatelům vytvářet, odstraňovat a změnit tabulek, indexů, zobrazení a pořadí a upsert řádky SQL jednotlivě a hromadně. Phoenix používá nativní kompilace noSQL místo použití prostředí MapReduce ke kompilaci dotazů, k vytvoření aplikace s nízkou latencí nad HBase. Phoenix přidá společné procesory, které podporují spouštění kódu dodaná klientem v adresním prostoru serveru, spouští kód společně umístěné s daty. Tím se minimalizují přenos dat klienta nebo serveru.  Pro práci s daty v HDInsight pomocí Phoenix, vytvoření tabulky a pak načíst data do nich.

## <a name="bulk-loading-with-apache-phoenix"></a>Hromadné načtení se Apache Phoenix

Existuje více způsobů, jak dostat data do HBase, včetně použití klientského rozhraní API pro úlohu MapReduce s TableOutputFormat, nebo vložení dat ručně pomocí prostředí HBase. Phoenix nabízí dvě metody pro načítání dat ve formátu CSV do tabulky Phoenix: klient načítá nástroj s názvem `psql`a nástroj zatížení na základě MapReduce hromadně.

`psql` Nástroj je s jedním vláknem a je nejvhodnější pro načítání megabajtech nebo gigabajtech data. Všechny soubory sdíleného svazku clusteru, který se má načíst musí mít příponu "CSV".  Můžete také určit soubory skriptů SQL v `psql` příkazového řádku s příponou ".sql".

Hromadné načtení se MapReduce se používá pro větší objemy dat, obvykle v produkčních scénářích, jako MapReduce používá více vláken.

Než začnete, načítání dat, ověřte, že je povoleno Phoenix a zda jsou nastavení časového limitu dotazu podle očekávání.  Přístup k vašemu clusteru HDInsight [Apache Ambari](https://ambari.apache.org/) řídicí panel, vyberte HBase a potom na kartě konfigurace.  Přejděte dolů a ověřte, že Apache Phoenix nastavený na `enabled` uvedeno:

![Nastavení clusteru HDInsight Apache Phoenix](./media/apache-hbase-phoenix-psql/ambari-phoenix.png)

### <a name="use-psql-to-bulk-load-tables"></a>Použití `psql` hromadné načítání tabulek

1. Vytvořit novou tabulku a pak uložte dotaz s názvem souboru `createCustomersTable.sql`.

    ```sql
    CREATE TABLE Customers (
        ID varchar NOT NULL PRIMARY KEY,
        Name varchar,
        Income decimal,
        Age INTEGER,
        Country varchar);
    ```

2. Kopírování souboru CSV (ukazuje příklad obsah) jako `customers.csv` do `/tmp/` adresáře pro načtení do nově vytvořené tabulky.  Použití `hdfs` příkazu kopírování souboru CSV do požadovaného zdrojového umístění.

    ```
    1,Samantha,260000.0,18,US
    2,Sam,10000.5,56,US
    3,Anton,550150.0,Norway
    ... 4997 more rows 
    ```

    ```bash
    hdfs dfs -copyToLocal /example/data/customers.csv /tmp/
    ```

3. Vytvořit dotaz SQL SELECT k ověření správně načíst vstupní data a pak uložte dotaz s názvem souboru `listCustomers.sql`. Můžete použít jakýkoli dotaz SQL.
     ```sql
    SELECT Name, Income from Customers group by Country;
    ```

4. Hromadné načtení dat tak, že otevřete *nové* Hadoop příkazové okno. Nejprve přejděte do adresáře spuštění s `cd` příkaz a pak použít `psql` nástroj (Python `psql.py` příkaz). 

    Následující příklad očekává, že jste si zkopírovali `customers.csv` souboru z účtu úložiště do vašeho místního dočasného adresáře pomocí `hdfs` jako v kroku 2 výše.

    ```bash
    cd /usr/hdp/current/phoenix-client/bin

    python psql.py ZookeeperQuorum createCustomersTable.sql /tmp/customers.csv listCustomers.sql
    ```

    > [!NOTE] 
    > Chcete-li zjistit `ZookeeperQuorum` název, vyhledejte [Apache ZooKeeper](https://zookeeper.apache.org/) kvora řetězce v souboru `/etc/hbase/conf/hbase-site.xml` s názvem vlastnosti `hbase.zookeeper.quorum`.

5. Po `psql` operace dokončí, zobrazí se zpráva v příkazovém okně:

    ```
    CSV Upsert complete. 5000 rows upserted
    Time: 4.548 sec(s)
    ```

## <a name="use-mapreduce-to-bulk-load-tables"></a>Použití MapReduce pro hromadné načítání tabulek

Pro vyšší propustnost načítání distribuované přes clusteru, použijte nástroj zatížení MapReduce. Tento zavaděč nejprve převádí HFiles všechna data a pak poskytuje vytvořený HFiles HBase.

1. Spustit zavaděč MapReduce sdíleného svazku clusteru pomocí `hadoop` příkaz jar Phoenix klienta:

    ```bash
    hadoop jar phoenix-<version>-client.jar org.apache.phoenix.mapreduce.CsvBulkLoadTool --table CUSTOMERS --input /data/customers.csv
    ```

2. Vytvořit novou tabulku s příkazem SQL, stejně jako u `CreateCustomersTable.sql` v předchozím kroku 1.

3. Pokud chcete ověřit schéma tabulky, spusťte `!describe inputTable`.

4. Určit cestu k umístění na vstupní data, například ukázkový `customers.csv` souboru. Vstupní soubory mohou být ve vašem účtu úložiště WASB nebo ADLS. V tomto ukázkovém scénáři se v vstupní soubory `<storage account parent>/inputFolderBulkLoad` adresáře.

5. Přejděte do adresáře spuštění pro příkaz MapReduce hromadné načtení:

    ```bash
    cd /usr/hdp/current/phoenix-client/bin
    ```

6. Vyhledejte vaši `ZookeeperQuorum` hodnota v `/etc/hbase/conf/hbase-site.xml`, s názvem vlastnosti `hbase.zookeeper.quorum`.

7. Cesta k třídě a spustit `CsvBulkLoadTool` nástroj příkazu:

    ```bash
    /usr/hdp/current/phoenix-client$ HADOOP_CLASSPATH=/usr/hdp/current/hbase-client/lib/hbase-protocol.jar:/etc/hbase/conf hadoop jar /usr/hdp/2.4.2.0-258/phoenix/phoenix-4.4.0.2.4.2.0-258-client.jar

    org.apache.phoenix.mapreduce.CsvBulkLoadTool --table Customers --input /inputFolderBulkLoad/customers.csv –zookeeper ZookeeperQuorum:2181:/hbase-unsecure
    ```

8. Používání nástroje MapReduce s ADLS, vyhledejte ADLS kořenový adresář, který je `hbase.rootdir` hodnota v `hbase-site.xml`. V následujícím příkazu je kořenový adresář ADLS `adl://hdinsightconf1.azuredatalakestore.net:443/hbase1`. V tomto příkazu zadejte ADLS vstupní a výstupní složky jako parametry:

    ```bash
    cd /usr/hdp/current/phoenix-client

    $HADOOP_CLASSPATH=$(hbase mapredcp):/etc/hbase/conf hadoop jar /usr/hdp/2.4.2.0-258/phoenix/phoenix-4.4.0.2.4.2.0-258-client.jar

    org.apache.phoenix.mapreduce.CsvBulkLoadTool --table Customers --input adl://hdinsightconf1.azuredatalakestore.net:443/hbase1/data/hbase/temp/input/customers.csv –zookeeper ZookeeperQuorum:2181:/hbase-unsecure --output  adl://hdinsightconf1.azuredatalakestore.net:443/hbase1/data/hbase/output1
    ```

## <a name="recommendations"></a>Doporučení

* Použijte stejné střední úložiště pro vstupní a výstupní složky, WASB nebo ADLS. Přenos dat z WASB do ADLS, můžete použít `distcp` příkaz:

    ```bash
    hadoop distcp wasb://@.blob.core.windows.net/example/data/gutenberg adl://.azuredatalakestore.net:443/myfolder
    ```

* Použijte větší velikost pracovních uzlů. Mapa procesu hromadného kopírování MapReduce vytvořit velké množství dočasná výstupní, které vyplnit místo k dispozici bez-systému souborů DFS. Pro velký objem hromadné načítání použijte další a větší velikost pracovních uzlů. Počet uzlů pracovního procesu, kterou můžete přidělit přímo do vašeho clusteru má vliv na rychlost zpracování.

* Rozdělte vstupních souborů do bloků ~ 10 GB. Hromadné načítání je operace náročné na úložiště, takže rozdělení vstupních souborů do více bloků výsledkem lepší výkon.

* Vyhněte se hotspotům serveru oblasti. Pokud váš klíč řádku monotónně narůstá, sekvenční zápisy HBase může vyvolávat hotspotting serveru oblasti. *Solení* klíč řádku snižuje sekvenční zápisy. Phoenix poskytuje způsob, jak transparentně salt klíč řádku s "solení" bajtů pro určité tabulce, jak je uvedeno níže.

## <a name="next-steps"></a>Další postup

* [Hromadné načítání dat s Apache Phoenix](http://phoenix.apache.org/bulk_dataload.html)
* [Použití Apache Phoenixu s clustery založené na Linuxu Apache HBase v HDInsight](../hbase/apache-hbase-phoenix-squirrel-linux.md)
* [Solené tabulky](https://phoenix.apache.org/salted.html)
* [Gramatika Phoenix](http://phoenix.apache.org/language/index.html)
