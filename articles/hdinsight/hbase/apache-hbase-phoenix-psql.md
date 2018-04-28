---
title: Hromadné načítání do Apache Phoenix pomocí psql - Azure HDInsight | Microsoft Docs
description: Použijte nástroj psql načíst hromadné načítání dat do Phoenix tabulek.
services: hdinsight
documentationcenter: ''
author: ashishthaps
manager: jhubbard
editor: cgronlun
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 11/10/2017
ms.author: ashishth
ms.openlocfilehash: 2c192707c6cf8f84d2ca1c0307770cadd5cdb8bd
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2018
---
# <a name="bulk-load-data-into-phoenix-using-psql"></a>Hromadné načtení dat do Phoenixu s využitím psql

[Apache Phoenix](http://phoenix.apache.org/) je open source, massively parallel relační databáze založená na [HBase](../hbase/apache-hbase-overview.md). Phoenix poskytuje dotazy podobné jazyku SQL nad HBase. Phoenix používá JDBC ovladače umožníte uživatelům k vytvoření, odstranění a alter SQL tabulky, indexy, zobrazení a pořadí a upsert řádky jednotlivě a hromadně. Phoenix používá nativní kompilace noSQL, místo použití prostředí MapReduce zkompilovat dotazy, k vytvoření aplikací s nízkou latencí nad HBase. Phoenix přidá společné procesorů k podpoře spouštění klienta zadaný kód v adresním prostoru serveru, provádění kódu umístěny společně s daty. Tím se minimalizují přenos dat klienta nebo serveru.  Pro práci s daty pomocí Phoenix v HDInsight, nejprve vytvořit tabulky a pak do nich můžete načíst data.

## <a name="bulk-loading-with-phoenix"></a>Hromadné načtení se Phoenix

Načíst data do HBase, včetně pomocí rozhraní API, úlohu MapReduce s TableOutputFormat, klienta nebo vložení dat ručně pomocí prostředí HBase několika způsoby. Phoenix nabízí dvě metody pro načítání dat sdíleného svazku clusteru do tabulek Phoenix: klient načítání nástroj s názvem `psql`a nástroj na základě MapReduce hromadné načtení.

`psql` Nástroj jednovláknové a je nejvhodnější pro načítání megabajtech nebo gigabajtech data. Všechny soubory CSV má být načten musí mít příponu souboru, CSV'.  Můžete také určit soubory skript SQL v `psql` příkazového řádku s příponou souboru '.sql'.

Hromadné načtení se MapReduce se používá pro mnohem větší datové svazky, obvykle v produkčních scénářích, jako MapReduce používá více vláken.

Než začnete, načítání dat, ověřte, zda je povolena Phoenix a zda jsou nastavení časového limitu dotazu podle očekávání.  Přístup k řídicím panelu Ambari clusteru HDInsight, vyberte HBase a pak na kartě konfigurace.  Přejděte dolů a ověřte, že Apache Phoenix je nastavena na `enabled` znázorněné:

![Nastavení clusteru HDInsight Apache Phoenix](./media/apache-hbase-phoenix-psql/ambari-phoenix.png)

### <a name="use-psql-to-bulk-load-tables"></a>Použití `psql` do hromadné načítání tabulek

1. Vytvořit novou tabulku a pak ho uložte dotaz s názvem souboru `createCustomersTable.sql`.

    ```sql
    CREATE TABLE Customers (
        ID varchar NOT NULL PRIMARY KEY,
        Name varchar,
        Income decimal,
        Age INTEGER,
        Country varchar);
    ```

2. Kopírování souboru CSV (ukazuje příklad obsah) jako `customers.csv` do `/tmp/` adresář pro načítání do nově vytvořené tabulku.  Použití `hdfs` příkaz pro kopírování souboru CSV do požadovaného zdrojového umístění.

    ```
    1,Samantha,260000.0,18,US
    2,Sam,10000.5,56,US
    3,Anton,550150.0,Norway
    ... 4997 more rows 
    ```

    ```bash
    hdfs dfs -copyToLocal /example/data/customers.csv /tmp/
    ```

3. Vytvořit dotaz SQL SELECT, který ověřte vstupní data načetla správně a pak ho uložte dotaz s názvem souboru `listCustomers.sql`. Můžete použít jakýkoli dotaz SQL.
     ```sql
    SELECT Name, Income from Customers group by Country;
    ```

4. Hromadné načítání dat otevřením *nové* Hadoop příkazové okno. Nejprve změňte umístění adresáře spuštění s `cd` příkaz a potom pomocí `psql` nástroj (Python `psql.py` příkaz). 

    Následující příklad očekává, že jste zkopírovali `customers.csv` souboru z účtu úložiště do vašeho místního dočasného adresáře pomocí `hdfs` jako v kroku 2 výše.

    ```bash
    cd /usr/hdp/current/phoenix-client/bin

    python psql.py ZookeeperQuorum createCustomersTable.sql /tmp/customers.csv listCustomers.sql
    ```

    > [!NOTE] 
    > Chcete-li zjistit `ZookeeperQuorum` název, vyhledejte řetězec zookeeper kvora v souboru `/etc/hbase/conf/hbase-site.xml` s názvem vlastnosti `hbase.zookeeper.quorum`.

5. Po `psql` operace byla dokončena, v příkazovém okně měla zobrazit zpráva:

    ```
    CSV Upsert complete. 5000 rows upserted
    Time: 4.548 sec(s)
    ```

## <a name="use-mapreduce-to-bulk-load-tables"></a>Používání nástroje MapReduce s tabulkami hromadné načtení

Pro vyšší propustnost načítání distribuovány na clusteru, použijte nástroj zatížení MapReduce. Tento zavaděč nejprve HFiles převede všechna data a pak poskytuje vytvořený HFiles HBase.

1. Spustí zavaděč CSV MapReduce pomocí `hadoop` s jar klienta Phoenix:

    ```bash
    hadoop jar phoenix-<version>-client.jar org.apache.phoenix.mapreduce.CsvBulkLoadTool --table CUSTOMERS --input /data/customers.csv
    ```

2. Vytvořit novou tabulku s příkazem SQL, stejně jako u `CreateCustomersTable.sql` v předchozím kroku 1.

3. Pokud chcete ověřit schéma tabulky, spusťte `!describe inputTable`.

4. Určete cestu k umístění na vstupní data, jako je například v příkladu `customers.csv` souboru. Vstupní soubory mohou být v účtu úložiště WASB/ADLS. V tomto ukázkovém scénáři se v vstupní soubory `<storage account parent>/inputFolderBulkLoad` adresáře.

5. Přejděte do adresáře spuštění pro příkaz MapReduce hromadné načtení:

    ```bash
    cd /usr/hdp/current/phoenix-client/bin
    ```

6. Vyhledejte vaše `ZookeeperQuorum` hodnotu `/etc/hbase/conf/hbase-site.xml`, název vlastnosti `hbase.zookeeper.quorum`.

7. Nastavení cesty pro třídy a spustit `CsvBulkLoadTool` nástroj příkaz:

    ```bash
    /usr/hdp/current/phoenix-client$ HADOOP_CLASSPATH=/usr/hdp/current/hbase-client/lib/hbase-protocol.jar:/etc/hbase/conf hadoop jar /usr/hdp/2.4.2.0-258/phoenix/phoenix-4.4.0.2.4.2.0-258-client.jar

    org.apache.phoenix.mapreduce.CsvBulkLoadTool --table Customers --input /inputFolderBulkLoad/customers.csv –zookeeper ZookeeperQuorum:2181:/hbase-unsecure
    ```

8. K používání nástroje MapReduce s ADLS, vyhledejte ADLS kořenový adresář, který je `hbase.rootdir` hodnotu `hbase-site.xml`. V následujícím příkazu ADLS kořenový adresář je `adl://hdinsightconf1.azuredatalakestore.net:443/hbase1`. V tomto příkazu zadejte ADLS vstupní a výstupní složky jako parametry:

    ```bash
    cd /usr/hdp/current/phoenix-client

    $HADOOP_CLASSPATH=$(hbase mapredcp):/etc/hbase/conf hadoop jar /usr/hdp/2.4.2.0-258/phoenix/phoenix-4.4.0.2.4.2.0-258-client.jar

    org.apache.phoenix.mapreduce.CsvBulkLoadTool --table Customers --input adl://hdinsightconf1.azuredatalakestore.net:443/hbase1/data/hbase/temp/input/customers.csv –zookeeper ZookeeperQuorum:2181:/hbase-unsecure --output  adl://hdinsightconf1.azuredatalakestore.net:443/hbase1/data/hbase/output1
    ```

## <a name="recommendations"></a>Doporučení

* Pro vstupní a výstupní složky, WASB nebo ADLS použijte stejné úložiště střední. K přenosu dat z WASB do ADLS, můžete použít `distcp` příkaz:

    ```bash
    hadoop distcp wasb://@.blob.core.windows.net/example/data/gutenberg adl://.azuredatalakestore.net:443/myfolder
    ```

* Použijte větší velikost pracovním uzlům. Vytvořit mapu procesy MapReduce hromadné kopírování velkých objemů dočasné výstup, které zaplnit prostor k dispozici bez-systému souborů DFS. Pro velké množství hromadné načítání použijte další a větší velikost pracovním uzlům. Počet uzlů pracovního procesu, kterou přiřadíte přímo ke svému clusteru má vliv na rychlost zpracování.

* Vstupní soubory rozdělí do bloků, ~ 10 GB. Hromadné načítání je operace náročné na úložiště, takže rozdělení vaše vstupní soubory do více výsledků bloků dat v lepší výkon.

* Vyhněte se hotspotům serveru oblast. Pokud váš klíč řádku monotónně narůstá, sekvenční zápisy HBase může vyvolávat hotspotting serveru oblast. *Solení* klíč řádku snižuje sekvenční zápisy. Phoenix poskytuje způsob, jak transparentně salt klíč řádku s solení bajtů pro konkrétní tabulku, jak je uvedeno níže.

## <a name="next-steps"></a>Další postup

* [Hromadné načítání dat s Apache Phoenix](http://phoenix.apache.org/bulk_dataload.html)
* [Použití nástrojů Apache Phoenix s clustery se systémem Linux HBase v HDInsight](../hbase/apache-hbase-phoenix-squirrel-linux.md)
* [Solené tabulky](https://phoenix.apache.org/salted.html)
* [Phoenix gramatiky](http://phoenix.apache.org/language/index.html)
