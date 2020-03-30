---
title: Vytváření tabulek Hive a načítání dat z úložiště objektů Blob – proces vědecké ho zpracování týmových dat
description: Pomocí dotazů Hive můžete vytvářet tabulky Hive a načítat data z úložiště objektů blob Azure. Rozdělit hive tabulky a použít optimalizované řádek sloupcové (ORC) formátování ke zlepšení výkonu dotazu.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79251658"
---
# <a name="create-hive-tables-and-load-data-from-azure-blob-storage"></a>Vytváření tabulek hive a načítání dat z úložiště objektů blob Azure

Tento článek představuje obecné dotazy Hive, které vytvářejí tabulky Hive a načítají data z úložiště objektů blob Azure. Některé pokyny jsou k dispozici také na dělení hive tabulek a na použití optimalizované řádky sloupcové (ORC) formátování ke zlepšení výkonu dotazu.

## <a name="prerequisites"></a>Požadavky
Tento článek předpokládá, že máte:

* Vytvořil účet Azure Storage. Pokud potřebujete pokyny, přečtěte si informace [o účtech Azure Storage](../../storage/common/storage-introduction.md).
* Zřídit vlastní cluster Hadoop se službou HDInsight.  Pokud potřebujete pokyny, přečtěte [si informace o nastavení clusterů ve službě HDInsight](../../hdinsight/hdinsight-hadoop-provision-linux-clusters.md).
* Byl povolen vzdálený přístup ke clusteru, byl přihlášen a otevřen konzola příkazového řádku Hadoop. Pokud potřebujete pokyny, přečtěte si informace [o správě clusterů Apache Hadoop](../../hdinsight/hdinsight-administer-use-portal-linux.md).

## <a name="upload-data-to-azure-blob-storage"></a>Nahrání dat do úložiště objektů blob Azure
Pokud jste vytvořili virtuální počítač Azure podle pokynů uvedených v [nastavení virtuálního počítače Azure pro pokročilou analýzu](../../machine-learning/data-science-virtual-machine/overview.md), tento soubor skriptu by měl být stažen do adresáře *C:\\Uživatelé\\\<\>\\uživatelské jméno Documents\\Data Science Scripts* na virtuálním počítači. Tyto dotazy Hive vyžadují jenom, že zadáte schéma dat a azure objektu blob konfigurace v příslušných polích pro připravenké odeslání.

Předpokládáme, že data pro tabulky Hive je v **nekomprimovaném** tabulkovém formátu a že data byla odeslána do výchozího (nebo do dalšího) kontejneru účtu úložiště používaného clusterem Hadoop.

Pokud chcete cvičit na **NYC Taxi Trip Data**, musíte:

* **stáhnout** 24 [NYC Taxi Trip Data](https://www.andresmh.com/nyctaxitrips) soubory (12 Výlet soubory a 12 Fare soubory),
* **rozbalte** všechny soubory do souborů .csv a potom
* **nahrajte** je do výchozího (nebo příslušného kontejneru) účtu Azure Storage; možnosti pro takový účet se zobrazí v [tématu Použití Služby Azure Storage s clustery Azure HDInsight.](../../hdinsight/hdinsight-hadoop-use-blob-storage.md) Proces nahrání souborů .csv do výchozího kontejneru v účtu úložiště naleznete na této [stránce](hive-walkthrough.md#upload).

## <a name="how-to-submit-hive-queries"></a><a name="submit"></a>Jak odeslat dotazy Hive
Dotazy hive lze odeslat pomocí:

* [Odeslat dotazy hive prostřednictvím příkazového řádku Hadoop v hlavovém uzlu clusteru Hadoop](#headnode)
* [Odeslání dotazů hive v Editoru úlů](#hive-editor)
* [Odeslání dotazů hive pomocí příkazů Azure PowerShellu](#ps)

Hive dotazy jsou podobné SQL. Pokud jste obeznámeni s SQL, může najít [Hive pro uživatele SQL Cheat Sheet](https://hortonworks.com/wp-content/uploads/2013/05/hql_cheat_sheet.pdf) užitečné.

Při odesílání dotazu Hive můžete také řídit cíl výstupu z dotazů Hive, ať už na obrazovce nebo do místního souboru v hlavním uzlu nebo do objektu blob Azure.

### <a name="submit-hive-queries-through-hadoop-command-line-in-headnode-of-hadoop-cluster"></a><a name="headnode"></a>Odeslat dotazy hive prostřednictvím příkazového řádku Hadoop v hlavovém uzlu clusteru Hadoop
Pokud je dotaz Hive složitý, jeho odeslání přímo do hlavního uzlu clusteru Hadoop obvykle vede k rychlejšímu otočení než jeho odeslání pomocí editoru Hive nebo skriptů Azure PowerShell.

Přihlaste se k hlavnímu uzlu clusteru Hadoop, otevřete příkazový řádek Hadoop `cd %hive_home%\bin`na ploše hlavního uzlu a zadejte příkaz .

Máte tři způsoby, jak odeslat dotazy Hive v příkazovém řádku Hadoop:

* Přímo
* pomocí souborů '.hql'
* s příkazovou konzolou Hive

#### <a name="submit-hive-queries-directly-in-hadoop-command-line"></a>Odešlete dotazy Hive přímo do příkazového řádku Hadoop.
Můžete spustit příkaz `hive -e "<your hive query>;` jako odeslat jednoduché dotazy Hive přímo v příkazovém řádku Hadoop. Zde je příklad, kde červené pole popisuje příkaz, který odešle dotaz Hive a zelené pole popisuje výstup z dotazu Hive.

![Příkaz k odeslání dotazu Hive s výstupem z dotazu Hive](./media/move-hive-tables/run-hive-queries-1.png)

#### <a name="submit-hive-queries-in-hql-files"></a>Odeslat dotazy hive v souborech '.hql'
Pokud je dotaz Hive složitější a má více řádků, úpravy dotazů v příkazovém řádku nebo příkazové konzole Hive není praktické. Alternativou je použití textového editoru v hlavním uzlu clusteru Hadoop k uložení dotazů Hive v souboru '.hql' v místním adresáři hlavního uzlu. Potom dotaz Hive v souboru '.hql' lze `-f` odeslat pomocí argumentu takto:

    hive -f "<path to the '.hql' file>"

![Dotaz hive v souboru '.hql'](./media/move-hive-tables/run-hive-queries-3.png)

**Potlačit sítotisk stavu průběhu dotazů Hive**

Ve výchozím nastavení je po odeslání dotazu Hive v příkazovém řádku Hadoop u nabídky mapy/snížení vytištěn na obrazovce. Chcete-li potlačit sítotisk mapy/snížit průběh úlohy, můžete použít argument `-S` ("S" velkými písmeny) v příkazovém řádku následujícím způsobem:

    hive -S -f "<path to the '.hql' file>"
    hive -S -e "<Hive queries>"

#### <a name="submit-hive-queries-in-hive-command-console"></a>Odešlete dotazy Hive v příkazové konzole Hive.
Příkazového konzole Hive můžete také nejprve zadat spuštěním příkazu `hive` v příkazovém řádku Hadoop a potom odeslat dotazy Hive v příkazovékonzole Hive. Zde je příklad. V tomto příkladu dvě červená pole zvýrazní příkazy použité k zadání příkazové konzole Hive a dotaz Uznaný odeslaný v příkazovékonzole Hive. Zelené pole zvýrazní výstup z dotazu Hive.

![Otevřít příkazovou konzolu Hive a zadat příkaz, zobrazit výstup dotazu Hive](./media/move-hive-tables/run-hive-queries-2.png)

Předchozí příklady přímo výstup výsledky dotazu Hive na obrazovce. Výstup můžete také zapsat do místního souboru v hlavním uzlu nebo do objektu blob Azure. Potom můžete použít jiné nástroje k další analýze výstupu dotazů Hive.

**Výsledky výstupního dotazu Hive do místního souboru.**
Chcete-li vyprostit výsledky dotazu Hive do místního adresáře v hlavním uzlu, musíte odeslat dotaz Hive v příkazovém řádku Hadoop takto:

    hive -e "<hive query>" > <local path in the head node>

V následujícím příkladu je výstup dotazu Hive `hivequeryoutput.txt` zapsán `C:\apps\temp`do souboru v adresáři .

![Výstup dotazu Hive](./media/move-hive-tables/output-hive-results-1.png)

**Výsledky výstupního dotazu hive do objektu blob Azure**

Můžete také výstup výsledků dotazu Hive do objektu blob Azure, v rámci výchozího kontejneru clusteru Hadoop. Dotaz Hive pro toje následující:

    insert overwrite directory wasb:///<directory within the default container> <select clause from ...>

V následujícím příkladu je výstup dotazu Hive zapsán `queryoutputdir` do adresáře objektů blob ve výchozím kontejneru clusteru Hadoop. Zde stačí zadat název adresáře bez názvu objektu blob. Pokud zadáte názvy adresářů i objektů blob, `wasb:///queryoutputdir/queryoutput.txt`například .

![Výstup dotazu Hive](./media/move-hive-tables/output-hive-results-2.png)

Pokud otevřete výchozí kontejner clusteru Hadoop pomocí Průzkumníka úložiště Azure, uvidíte výstup dotazu Hive, jak je znázorněno na následujícím obrázku. Filtr (zvýrazněný červeným rámečkem) můžete použít, abyste objekt blob načetl i se zadanými písmeny v názvech.

![Průzkumník úložiště Azure zobrazující výstup dotazu Hive](./media/move-hive-tables/output-hive-results-3.png)

### <a name="submit-hive-queries-with-the-hive-editor"></a><a name="hive-editor"></a>Odeslání dotazů hive v Editoru úlů
Konzolu dotazů (Hive Editor) můžete také použít zadáním adresy URL formuláře *https:\//\<Název clusteru Hadoop>.azurehdinsight.net/Home/HiveEditor* do webového prohlížeče. Musíte být přihlášeni naleznete v této konzole, a proto zde potřebujete pověření clusteru Hadoop.

### <a name="submit-hive-queries-with-azure-powershell-commands"></a><a name="ps"></a>Odeslání dotazů hive pomocí příkazů Azure PowerShellu
PowerShell můžete také použít k odeslání dotazů Hive. Pokyny najdete [v tématu Submit Hive jobs using PowerShell](../../hdinsight/hadoop/apache-hadoop-use-hive-powershell.md).

## <a name="create-hive-database-and-tables"></a><a name="create-tables"></a>Vytvoření databáze a tabulek Hive
Dotazy Hive jsou sdíleny v [úložišti GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_create_db_tbls_load_data_generic.hql) a lze stáhnout odtud.

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

Zde jsou popisy polí, která je třeba připojit, a další konfigurace:

* Název databáze : název databáze, kterou chcete vytvořit. ** \<\>** Pokud chcete použít pouze výchozí databázi, dotaz "*create database...*" lze vynechat.
* Název tabulky : název tabulky, kterou chcete vytvořit v zadané databázi. ** \<\>** Chcete-li použít výchozí databázi, může být tabulka přímo \<odkazována * \<\> názvem tabulky* bez názvu\>databáze .
* **oddělovač\>polí : oddělovač, který vymezuje pole v datovém souboru, který má být odeslán do tabulky Hive. \<**
* **oddělovač\>čar : oddělovač, který vymezuje řádky v datovém souboru. \<**
* umístění úložiště: umístění úložiště Azure pro uložení dat tabulek Hive. ** \<\>** Pokud nezadáte *umístění \<\>úložiště UMÍSTĚNÍ*, databáze a tabulky jsou ve výchozím nastavení uloženy v *podregistru/skladu/adresáři* ve výchozím kontejneru clusteru Hive. Pokud chcete zadat umístění úložiště, umístění úložiště musí být ve výchozím kontejneru pro databázi a tabulky. Toto umístění musí být označováno jako umístění vzhledem k výchozímu kontejneru clusteru ve formátu *"wasb:///\<adresář 1>/"* nebo *\<"wasb:/// adresář 1>/\<adresář 2>/"* atd. Po spuštění dotazu jsou relativní adresáře vytvořeny v rámci výchozího kontejneru.
* **TBLPROPERTIES("skip.header.line.count"="1")**: Pokud datový soubor obsahuje řádek záhlaví, musíte tuto vlastnost přidat **na konec** dotazu na *vytvoření tabulky.* V opačném případě je řádek záhlaví načten jako záznam do tabulky. Pokud datový soubor nemá řádek záhlaví, lze tuto konfiguraci v dotazu vynechat.

## <a name="load-data-to-hive-tables"></a><a name="load-data"></a>Načtení dat do tabulek Hive
Tady je dotaz Hive, který načítá data do tabulky Hive.

    LOAD DATA INPATH '<path to blob data>' INTO TABLE <database name>.<table name>;

* cesta **k datům\>objektu blob : Pokud je soubor objektů blob, který má být odeslán do tabulky Hive, ve výchozím kontejneru clusteru HDInsight Hadoop, měla by být cesta k datům objektů blob ve formátu wasb:// adresář v tomto>/ název souboru objektů blob> . \<** * \<\> * *\<\<* Soubor objektů blob může být také v další kontejner clusteru HDInsight Hadoop. V takovém případě by * \<měla\> být cesta k datům objektů blob* ve formátu *wasb://\<název kontejneru>\<název účtu úložiště>.blob.core.windows.net/\<název souboru objektu blob>*.

  > [!NOTE]
  > Data objektu blob, která mají být odeslána do tabulky Hive, musí být ve výchozím nebo dalším kontejneru účtu úložiště pro cluster Hadoop. V opačném případě se nezdaří dotaz *LOAD DATA,* který si stěžuje, že nemá přístup k datům.
  >
  >

## <a name="advanced-topics-partitioned-table-and-store-hive-data-in-orc-format"></a><a name="partition-orc"></a>Pokročilá témata: rozdělená tabulka a ukládání dat Hive ve formátu ORC
Pokud jsou data velká, rozdělení tabulky je výhodné pro dotazy, které stačí prohledat několik oddílů tabulky. Například je rozumné rozdělit data protokolu webové stránky podle dat.

Kromě rozdělení tabulek Hive je také výhodné ukládat data Hive ve formátu Optimalizovaný řádek Columnar (ORC). Další informace o formátování ORC naleznete v <a href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+ORC#LanguageManualORC-ORCFiles" target="_blank">tématu Using ORC files improve performance when Hive is reading, writing, and processing data</a>.

### <a name="partitioned-table"></a>Dělená tabulka
Zde je dotaz Hive, který vytvoří rozdělenou tabulku a načte do ní data.

    CREATE EXTERNAL TABLE IF NOT EXISTS <database name>.<table name>
    (field1 string,
    ...
    fieldN string
    )
    PARTITIONED BY (<partitionfieldname> vartype) ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>'
         lines terminated by '<line separator>' TBLPROPERTIES("skip.header.line.count"="1");
    LOAD DATA INPATH '<path to the source file>' INTO TABLE <database name>.<partitioned table name>
        PARTITION (<partitionfieldname>=<partitionfieldvalue>);

Při dotazování rozdělené tabulky, je doporučeno přidat podmínku `where` oddílu na **začátku** klauzule, která zlepšuje efektivitu vyhledávání.

    select
        field1, field2, ..., fieldN
    from <database name>.<partitioned table name>
    where <partitionfieldname>=<partitionfieldvalue> and ...;

### <a name="store-hive-data-in-orc-format"></a><a name="orc"></a>Uložení dat Hive ve formátu ORC
Data z úložiště objektů blob nelze přímo načíst do tabulek Hive, které jsou uložené ve formátu ORC. Tady jsou kroky, které je třeba provést k načtení dat z objektů BLOB Azure do tabulek Hive uložených ve formátu ORC.

Vytvořte externí tabulku **uloženou jako textový soubor** a načtěte data z úložiště objektů blob do tabulky.

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

Vytvořte vnitřní tabulku se stejným schématem jako externí tabulka v kroku 1 se stejným oddělovačem polí a uložte data Hive ve formátu ORC.

        CREATE TABLE IF NOT EXISTS <database name>.<ORC table name>
        (
            field1 string,
            field2 int,
            ...
            fieldN date
        )
        ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>' STORED AS ORC;

Výběr dat z externí tabulky v kroku 1 a vložení do tabulky ORC

        INSERT OVERWRITE TABLE <database name>.<ORC table name>
            SELECT * FROM <database name>.<external textfile table name>;

> [!NOTE]
> Pokud * \<název\>databáze tabulky\< TEXTFILE . název\> tabulky externího textového souboru* obsahuje `SELECT * FROM <database name>.<external textfile table name>` oddíly, v kroku 3 příkaz vybere proměnnou oddílu jako pole v sadě vrácených dat. Vložení do * \<názvu\>databáze\< . Název\> tabulky ORC* se nezdaří od * \<názvu\>databáze .\< Název\> tabulky ORC* nemá proměnnou oddílu jako pole ve schématu tabulky. V takovém případě je třeba konkrétně vybrat pole, která mají být vložena do * \<názvu\>databáze .\< Název\> tabulky ORC* takto:
>
>

        INSERT OVERWRITE TABLE <database name>.<ORC table name> PARTITION (<partition variable>=<partition value>)
           SELECT field1, field2, ..., fieldN
           FROM <database name>.<external textfile table name>
           WHERE <partition variable>=<partition value>;

Při použití následujícího * \<\> * dotazu po vložení všech dat do * \<\>názvu databáze je bezpečné\< přetáhnout název tabulky externího textového souboru . Název\>tabulky ORC*:

        DROP TABLE IF EXISTS <database name>.<external textfile table name>;

Po provedení tohoto postupu byste měli mít tabulku s daty ve formátu ORC připravenk použití.  
