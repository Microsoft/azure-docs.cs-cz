---
title: Vytváření tabulek Hive a načtení dat z úložiště objektů Blob – vědecké zpracování týmových dat
description: Použití dotazů Hive k vytváření tabulek Hive a načtení dat z Azure blob storage. Rozdělení tabulek Hive a používat optimalizované řádek úložiště se sloupcovou strukturou (ORC) formátování pro zlepšení výkonu dotazů.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 625d9d5c5ecf095d4acbff625754b2065f184536
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79251658"
---
# <a name="create-hive-tables-and-load-data-from-azure-blob-storage"></a>Vytváření tabulek Hive a načtení dat z úložiště objektů Blob v Azure

Tento článek představuje obecné dotazy Hive, které vytváření tabulek Hive a načtení dat z úložiště objektů blob v Azure. Některé poskytujeme také pokyny o dělení tabulek Hive a o používání optimalizované řádek úložiště se sloupcovou strukturou (ORC) formátování pro zlepšení výkonu dotazů.

## <a name="prerequisites"></a>Požadavky
Tento článek předpokládá, že máte:

* Vytvořili jste účet Azure Storage. Pokud potřebujete pokyny, přečtěte si téma [informace o Azure Storagech účtech](../../storage/common/storage-introduction.md).
* Zřídit vlastní cluster Hadoop ve službě HDInsight.  Pokud potřebujete pokyny, přečtěte si téma [Instalace clusterů ve službě HDInsight](../../hdinsight/hdinsight-hadoop-provision-linux-clusters.md).
* Povolený vzdálený přístup ke clusteru, přihlášení a otevřít konzolu příkazového řádku Hadoopu. Pokud potřebujete pokyny, přečtěte si téma [správa Apache Hadoopch clusterů](../../hdinsight/hdinsight-administer-use-portal-linux.md).

## <a name="upload-data-to-azure-blob-storage"></a>Nahrání dat do úložiště objektů blob v Azure
Pokud jste vytvořili virtuální počítač Azure podle pokynů uvedených v části [nastavení virtuálního počítače Azure pro pokročilé analýzy](../../machine-learning/data-science-virtual-machine/overview.md), měl by se tento soubor skriptu stáhnout do složky *C:\\Users\\\<uživatelského jména\>\\dokumenty\\data vědy skripty* na virtuálním počítači. Tyto dotazy na podregistry vyžadují pouze zadání schématu dat a konfigurace služby Azure Blob Storage v příslušných polích, která budou připravena k odeslání.

Předpokládáme, že data pro tabulky podregistru jsou v **nekomprimovaném** tabulkovém formátu a že se data nahrála do výchozího (nebo do dalšího) kontejneru účtu úložiště používaného clusterem Hadoop.

Pokud chcete postupovat podle údajů o **cestách NYC taxislužby**, musíte:

* **Stáhněte** si 24 [datových souborů NYC taxislužby pro služební cestu](https://www.andresmh.com/nyctaxitrips) (soubory o 12 cestách a soubory tarifů),
* **rozbalení všech souborů** do souborů. csv a potom
* **nahrajte** je do výchozího (nebo vhodného kontejneru) účtu Azure Storage. možnosti pro takový účet se zobrazí v tématu [použití Azure Storage s clustery Azure HDInsight](../../hdinsight/hdinsight-hadoop-use-blob-storage.md) . Proces odeslání souborů. CSV do výchozího kontejneru v účtu úložiště najdete na této [stránce](hive-walkthrough.md#upload).

## <a name="submit"></a>Odeslání dotazů na podregistr
Dotazy Hive můžete odeslat pomocí:

* [Odeslání dotazů na podregistr prostřednictvím příkazového řádku Hadoop v hlavnímu uzlu clusteru Hadoop](#headnode)
* [Odeslání dotazů na podregistr pomocí editoru podregistru](#hive-editor)
* [Odeslání dotazů na podregistr pomocí příkazů Azure PowerShell](#ps)

Dotazy Hive jsou podobném SQL. Pokud jste obeznámeni s SQL, možná najdete [podregistr pro uživatele SQL tahák](https://hortonworks.com/wp-content/uploads/2013/05/hql_cheat_sheet.pdf) užitečné.

Při odesílání dotazů Hive, můžete také řídit cíl výstupu z dotazů Hive, jestli se na obrazovce nebo do místního souboru hlavního uzlu nebo do objektu blob Azure.

### <a name="headnode"></a>Odeslání dotazů na podregistr prostřednictvím příkazového řádku Hadoop v hlavnímu uzlu clusteru Hadoop
Pokud se dotaz Hive je komplexní, odesláním přímo do hlavního uzlu Hadoop cluster obvykle vede k otočení rychleji než jeho odesláním pomocí skriptů Hive Editor nebo Azure Powershellu.

Přihlaste se k hlavnímu uzlu clusteru Hadoop, otevřete příkazový řádek Hadoop na ploše hlavního uzlu a zadejte příkaz `cd %hive_home%\bin`.

Budete mít tři způsoby, jak odesílání dotazů Hive v příkazovém řádku Hadoop:

* přímo
* použití souborů. HQL
* pomocí příkazu konzoly Hive

#### <a name="submit-hive-queries-directly-in-hadoop-command-line"></a>Odesílání dotazů Hive přímo v systému Hadoop příkazového řádku.
Můžete spustit příkaz, například `hive -e "<your hive query>;` pro odeslání jednoduchých dotazů na podregistr přímo do příkazového řádku Hadoop. Tady je příklad, ve kterém červeným rámečkem popisuje příkaz, který odešle dotaz Hive a zeleného pole obsahuje výstup dotazu Hive.

![Příkaz Odeslat dotaz Hive s výstupem z dotazu Hive](./media/move-hive-tables/run-hive-queries-1.png)

#### <a name="submit-hive-queries-in-hql-files"></a>Odeslání dotazů na podregistr do souborů. HQL
Pokud dotaz Hive je složitější a obsahuje více řádků, není praktické úpravy dotazů v příkazovém řádku nebo Hive příkazové konzole. Alternativou je použití textového editoru v hlavním uzlu clusteru Hadoop k uložení dotazů na podregistr v souboru. HQL v místním adresáři hlavního uzlu. Pak se dotaz na podregistr v souboru. HQL dá odeslat pomocí argumentu `-f` následujícím způsobem:

    hive -f "<path to the '.hql' file>"

![Dotaz na podregistr v souboru. HQL](./media/move-hive-tables/run-hive-queries-3.png)

**Potlačit obrazovku stavu průběhu – Tisk dotazů na podregistr**

Ve výchozím nastavení Jakmile dotaz Hive se odešle do příkazového řádku Hadoopu, průběh úloh mapování/zmenšování se vytiskne na obrazovce. Chcete-li potlačit tisk obrazovky mapy/snížit průběh úlohy, můžete použít argument `-S` ("S" v horním případě) na příkazovém řádku následujícím způsobem:

    hive -S -f "<path to the '.hql' file>"
    hive -S -e "<Hive queries>"

#### <a name="submit-hive-queries-in-hive-command-console"></a>Odesílání dotazů Hive v příkazové konzole Hive.
Můžete také nejdřív zadat příkazovou konzolu pro podregistr spuštěním příkazu `hive` v příkazovém řádku Hadoop a pak odeslat dotazy na podregistr v konzole příkazů podregistru. Tady je příklad. V tomto příkladu zvýrazněte dva červená pole použité ke vstupu Hive příkazové konzole příkazy a dotaz Hive v příkazové konzole Hive, respektive odeslán. Zeleného pole zvýrazní výstup dotazu Hive.

![Otevřete konzolu Hive příkaz a zadejte příkaz, zobrazit výstup dotazu Hive](./media/move-hive-tables/run-hive-queries-2.png)

V předchozích příkladech přímo výstup výsledků dotazu Hive na obrazovce. Můžete také napsat výstup do místního souboru hlavního uzlu, nebo do objektu blob Azure. Potom můžete použít jiné nástroje Pokud chcete hlouběji analyzovat výstup dotazů Hive.

**Výsledky dotazu na výstupní podregistry do místního souboru.**
K vypsání výsledků dotazu Hive do místního adresáře hlavního uzlu, budete muset odeslat dotaz Hive v příkazovém řádku Hadoopu následujícím způsobem:

    hive -e "<hive query>" > <local path in the head node>

V následujícím příkladu se výstup dotazu na podregistr zapíše do souboru `hivequeryoutput.txt` v adresářovém `C:\apps\temp`.

![Výstup dotazu Hive](./media/move-hive-tables/output-hive-results-1.png)

**Výsledky dotazu na výstupní podregistr do objektu blob Azure**

Můžete také výstup výsledků dotazu Hive do objektu blob Azure, v rámci výchozího kontejneru Hadoop cluster. Dotaz Hive pro to vypadá takto:

    insert overwrite directory wasb:///<directory within the default container> <select clause from ...>

V následujícím příkladu se výstup dotazu na podregistr zapisuje do adresáře objektů BLOB `queryoutputdir` v rámci výchozího kontejneru clusteru Hadoop. Tady stačí zadat název adresáře, bez názvu objektu blob. Pokud zadáte název adresáře a objektů blob, například `wasb:///queryoutputdir/queryoutput.txt`, dojde k chybě.

![Výstup dotazu Hive](./media/move-hive-tables/output-hive-results-2.png)

Pokud otevřete výchozí kontejner clusteru Hadoop pomocí Průzkumníka služby Azure Storage, zobrazí se výstup dotazu Hive, jak je znázorněno na následujícím obrázku. Filter (zvýrazněná červeným rámečkem) můžete použít k načtení pouze objekt blob se zadaným písmena v názvech.

![Průzkumník služby Azure Storage výstup dotazu Hive](./media/move-hive-tables/output-hive-results-3.png)

### <a name="hive-editor"></a>Odeslání dotazů na podregistr pomocí editoru podregistru
Můžete také použít konzolu dotazů (Editor registru) zadáním adresy URL formuláře *https:\//\<Hadoop název clusteru >. azurehdinsight. NET/Home/HiveEditor* do webového prohlížeče. Musíte být přihlášeni se tato konzola, takže je nutné pověření clusteru Hadoop tady.

### <a name="ps"></a>Odeslání dotazů na podregistr pomocí příkazů Azure PowerShell
Můžete také použít PowerShell k odesílání dotazů Hive. Pokyny najdete v tématu [Odeslání úloh podregistru pomocí prostředí PowerShell](../../hdinsight/hadoop/apache-hadoop-use-hive-powershell.md).

## <a name="create-tables"></a>Vytvoření databáze a tabulek podregistru
Dotazy na podregistr se sdílejí v [úložišti GitHubu](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_create_db_tbls_load_data_generic.hql) a můžete je stáhnout z něj.

Tady je dotaz Hive, který vytvoří tabulku Hive.

    create database if not exists <database name>;
    CREATE EXTERNAL TABLE if not exists <database name>.<table name>
    (
        field1 string,
        field2 int,
        field3 float,
        field4 double,
        ...,
        fieldN string
    )
    ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>' lines terminated by '<line separator>'
    STORED AS TEXTFILE LOCATION '<storage location>' TBLPROPERTIES("skip.header.line.count"="1");

Tady je popis jednotlivých polí, které potřebujete k modulu plug-in a další konfigurace:

* **\<\>název databáze** : název databáze, kterou chcete vytvořit. Pokud chcete použít pouze výchozí databázi, dotaz "*vytvořit databázi...* " lze vynechat.
* **název tabulky\<\>** : název tabulky, kterou chcete vytvořit v zadané databázi. Pokud chcete použít výchozí databázi, může být tabulka přímo označována *\<názvem tabulky\>* bez \<\>název databáze.
* **oddělovač polí\<\>** : oddělovač, který omezuje pole v datovém souboru, který se má odeslat do tabulky podregistru.
* **oddělovač čáry\<\>** : oddělovač, který odděluje řádky v datovém souboru.
* **\<umístění úložiště\>** : umístění Azure Storage pro uložení dat tabulek podregistru. Pokud nezadáte *umístění \<umístění úložiště\>* , databáze a tabulky jsou ve výchozím nastavení uloženy v *podregistru/skladu/* adresáři ve výchozím kontejneru clusteru podregistru. Pokud chcete zadat umístění úložiště, umístění úložiště musí být v rámci výchozího kontejneru pro databáze a tabulky. Toto umístění se musí považovat za umístění relativní vzhledem k výchozímu kontejneru clusteru ve formátu *"wasb:///\<Directory 1 >/"* nebo *"wasb:///\<Directory 1 >/\<Directory 2 >/"* atd. Po provedení dotazu se relativní adresáře vytvoří v rámci výchozího kontejneru.
* **TBLPROPERTIES ("Skip. Header. line. Count" = "1")** : Pokud datový soubor obsahuje řádek záhlaví, je nutné tuto vlastnost přidat **na konci** dotazu *Create Table* . V opačném případě řádek záhlaví je načtena jako záznam do tabulky. Pokud datový soubor nemá řádek záhlaví, tato konfigurace může vynechat v dotazu.

## <a name="load-data"></a>Načtení dat do tabulek podregistru
Tady je dotaz Hive, který načítá data do tabulky Hive.

    LOAD DATA INPATH '<path to blob data>' INTO TABLE <database name>.<table name>;

* **\<cesta k datovým objektům blob\>** : Pokud se soubor objektu blob, který se má nahrát do tabulky podregistru, nachází ve výchozím kontejneru clusteru HDInsight Hadoop, *cesta\<k datům BLOB\>* by měla být ve formátu *adresář wasb://\<v tomto kontejneru >/\<název souboru BLOB*>. Soubor objektu blob může být také v dalších kontejneru clusteru HDInsight Hadoop. V takovém případě *\<cesta k datům blob\>* by měla být ve formátu *wasb://\<název kontejneru >\<název účtu úložiště >. blob. Core. Windows. NET/\<název souboru BLOB >* .

  > [!NOTE]
  > Data objektů blob k nahrání do tabulky Hive musí být ve výchozím nastavení nebo další kontejneru účtu úložiště pro Hadoop cluster. V opačném případě dotaz *načíst data* nevydá stížnost, že nemá přístup k datům.
  >
  >

## <a name="partition-orc"></a>Pokročilá témata: dělená tabulka a data z podregistru úložiště ve formátu ORC
Pokud jsou data velká, vytváření oddílů v tabulce je přínosné pro dotazy, které stačí kontrolovat několik oddílů v tabulce. Například je možné logicky rozdělení dat protokolu z webu data.

Kromě dělení tabulek Hive, je také vhodné k uložení dat Hive ve formátu optimalizované řádek úložiště se sloupcovou strukturou (ORC). Další informace o formátování ORC najdete v tématu <a href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+ORC#LanguageManualORC-ORCFiles" target="_blank">použití souborů ORC vylepšuje výkon při čtení, zápisu a zpracování dat v podregistru</a>.

### <a name="partitioned-table"></a>Dělenou tabulku
Tady je dotaz Hive, který vytvoří dělenou tabulku a načte data do ní.

    CREATE EXTERNAL TABLE IF NOT EXISTS <database name>.<table name>
    (field1 string,
    ...
    fieldN string
    )
    PARTITIONED BY (<partitionfieldname> vartype) ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>'
         lines terminated by '<line separator>' TBLPROPERTIES("skip.header.line.count"="1");
    LOAD DATA INPATH '<path to the source file>' INTO TABLE <database name>.<partitioned table name>
        PARTITION (<partitionfieldname>=<partitionfieldvalue>);

Při dotazování na dělené tabulky doporučujeme přidat podmínku oddílu na **začátek** klauzule `where`, což zlepší efektivitu hledání.

    select
        field1, field2, ..., fieldN
    from <database name>.<partitioned table name>
    where <partitionfieldname>=<partitionfieldvalue> and ...;

### <a name="orc"></a>Uložení dat z podregistru ve formátu ORC
Nelze načíst přímo data z úložiště objektů blob do tabulek Hive, která je uložena ve formátu ORC. Tady jsou kroky, které nutné provádět k načtení dat z Azure, objekty BLOB do tabulek Hive, které jsou uložené ve formátu ORC.

Vytvořte externí tabulku **uloženou jako textfile** a načtěte data z úložiště objektů blob do tabulky.

        CREATE EXTERNAL TABLE IF NOT EXISTS <database name>.<external textfile table name>
        (
            field1 string,
            field2 int,
            ...
            fieldN date
        )
        ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>'
            lines terminated by '<line separator>' STORED AS TEXTFILE
            LOCATION 'wasb:///<directory in Azure blob>' TBLPROPERTIES("skip.header.line.count"="1");

        LOAD DATA INPATH '<path to the source file>' INTO TABLE <database name>.<table name>;

Vytvoření interní tabulku pomocí stejné schéma jako externí tabulky v kroku 1, s oddělovačem stejné pole a uložení dat Hive ve formátu ORC.

        CREATE TABLE IF NOT EXISTS <database name>.<ORC table name>
        (
            field1 string,
            field2 int,
            ...
            fieldN date
        )
        ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>' STORED AS ORC;

Vyberte data z externí tabulky v kroku 1 a vložit do tabulky ORC

        INSERT OVERWRITE TABLE <database name>.<ORC table name>
            SELECT * FROM <database name>.<external textfile table name>;

> [!NOTE]
> Pokud tabulka TEXTFILE *\<název databáze\>.\<název externí tabulky TEXTFILE\>* má oddíly, v kroku 3 příkaz `SELECT * FROM <database name>.<external textfile table name>` vybere proměnnou oddílu jako pole ve vrácené datové sadě. Vložením do *\<název databáze\>.\<název tabulky ORC\>* selžou, protože *\<název databáze\>.\<ORC název tabulky\>* nemá proměnnou oddílu jako pole ve schématu tabulky. V takovém případě musíte konkrétně vybrat pole, která se mají vložit do *\<název databáze\>.\<název tabulky ORC\>* následujícím způsobem:
>
>

        INSERT OVERWRITE TABLE <database name>.<ORC table name> PARTITION (<partition variable>=<partition value>)
           SELECT field1, field2, ..., fieldN
           FROM <database name>.<external textfile table name>
           WHERE <partition variable>=<partition value>;

Po vložení všech dat do\<\>názvu databáze je bezpečné vyřadit *název tabulky\<externího textového souboru\>* při použití následujícího dotazu *.\<název tabulky ORC\>* :

        DROP TABLE IF EXISTS <database name>.<external textfile table name>;

Po provedení tohoto postupu, měli byste mít tabulku s daty ve formátu ORC připravené k použití.  
