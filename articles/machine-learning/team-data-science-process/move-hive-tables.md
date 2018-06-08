---
title: Vytváření tabulek Hive a načtení dat z Azure Blob Storage | Microsoft Docs
description: Vytváření tabulek Hive a načtení dat do objektu blob do tabulek hive
services: machine-learning,storage
documentationcenter: ''
author: deguhath
manager: jhubbard
editor: cgronlun
ms.assetid: cff9280d-18ce-4b66-a54f-19f358d1ad90
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/04/2017
ms.author: deguhath
ms.openlocfilehash: 474eb7122de59d12c69b7c1021cfdff8548c5a25
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/07/2018
ms.locfileid: "34837952"
---
# <a name="create-hive-tables-and-load-data-from-azure-blob-storage"></a>Vytváření tabulek Hive a načtení dat z Azure Blob Storage
Toto téma představuje obecné dotazů Hive, které vytváření tabulek Hive a načtení dat z Azure blob storage. Některé pokyny jsou tu taky o dělení tabulek Hive a o používání optimalizované řádek sloupcovém (ORC) formátování pro zlepšení výkonu dotazů.

To **nabídky** odkazy na témata, které popisují, jak ingestovat data do cílové prostředí, kde můžete data ukládat a zpracovávat během tým datové vědy procesu (TDSP).

[!INCLUDE [cap-ingest-data-selector](../../../includes/cap-ingest-data-selector.md)]

## <a name="prerequisites"></a>Požadavky
Tento článek předpokládá, že máte:

* Vytvořit účet úložiště Azure. Pokud budete potřebovat pokyny, najdete v části [účty Azure storage](../../storage/common/storage-create-storage-account.md).
* Zřizuje přizpůsobené clusteru Hadoop se službou HDInsight.  Pokud budete potřebovat pokyny, najdete v části [přizpůsobit Azure HDInsight Hadoop clusterů pro pokročilou analýzu](customize-hadoop-cluster.md).
* Povoleno pro vzdálený přístup ke clusteru, přihlášení a otevřít konzolu příkazového řádku Hadoop. Pokud budete potřebovat pokyny, najdete v části [přístup hlavního uzlu Hadoop clusteru](customize-hadoop-cluster.md).

## <a name="upload-data-to-azure-blob-storage"></a>Nahrání dat do Azure blob storage
Pokud jste vytvořili virtuální počítač Azure podle pokynů uvedených v [nastavení virtuálního počítače Azure pro pokročilou analýzu](../data-science-virtual-machine/setup-virtual-machine.md), tento soubor skriptu by byly staženy *C:\\uživatelů \\ \<uživatelské jméno\>\\dokumenty\\datové vědy skripty* adresář na virtuálním počítači. Tyto dotazy Hive vyžadují jenom zařaďte vlastní schéma dat a konfigurace úložiště objektů blob Azure do příslušných polí bude připravená k odeslání.

Předpokládáme, že se data pro tabulek Hive v **nekomprimované** tabulkovém formátu, a že data byl odeslán na výchozí hodnoty (nebo dalších) kontejneru účtu úložiště používaný v clusteru Hadoop.

Pokud chcete postupem na **NYC taxíkem cestě Data**, budete muset:

* **Stáhněte si** 24 [NYC taxíkem cestě Data](http://www.andresmh.com/nyctaxitrips) soubory (12 cestě soubory a soubory 12 tarif),
* **Rozbalte** všechny soubory do souborů .csv a potom
* **Nahrát** je výchozí (nebo odpovídajícího kontejneru) účet úložiště Azure, který byl vytvořen pomocí pokynů uvedených v části [přizpůsobit Azure HDInsight Hadoop clusterů pro pokročilé analýzy proces a technologie](customize-hadoop-cluster.md)tématu. Postup nahrání souborů .csv do výchozího kontejneru v účtu úložiště najdete v tomto [stránky](hive-walkthrough.md#upload).

## <a name="submit"></a>Postup odesílání dotazů Hive
Dotazy Hive můžete odeslat pomocí:

1. [Odesílání dotazů Hive prostřednictvím Hadoop příkazového řádku v headnode clusteru Hadoop](#headnode)
2. [Odesílání dotazů Hive pomocí editoru Hive](#hive-editor)
3. [Odesílání dotazů Hive pomocí příkazů prostředí PowerShell Azure](#ps)

Dotazy Hive jsou podobné jazyku SQL. Pokud jste obeznámeni s SQL, můžete zjistit [Hive pro SQL uživatelé cheaty list](http://hortonworks.com/wp-content/uploads/2013/05/hql_cheat_sheet.pdf) užitečné.

Při odesílání dotazů Hive, můžete také ovládat cíl výstupu z dotazů Hive, ať to na obrazovce nebo do místního souboru z hlavního uzlu nebo do objektu blob Azure.

### <a name="headnode"></a> 1. Odesílání dotazů Hive prostřednictvím Hadoop příkazového řádku v headnode clusteru Hadoop
Pokud se dotaz Hive komplexní, odesílání přímo v hlavního uzlu hadoop clusteru obvykle vede k rychlejší otáčení, než odesílání pomocí editoru Hive nebo Azure PowerShell skriptů.

Přihlaste se k hlavnímu uzlu clusteru Hadoop, otevřete příkazový řádek Hadoop na ploše hlavního uzlu a zadejte příkaz `cd %hive_home%\bin`.

Máte tři způsoby, jak odesílání dotazů Hive Hadoop příkazového řádku:

* přímo
* pomocí souborů .hql
* s konzolou příkaz Hive

#### <a name="submit-hive-queries-directly-in-hadoop-command-line"></a>Odesílání dotazů Hive přímo v Hadoop příkazového řádku.
Můžete spustit příkaz jako `hive -e "<your hive query>;` k odesílání dotazů Hive jednoduché přímo v Hadoop příkazového řádku. Tady je příklad, kde červeným rámečkem popisuje příkaz, který odešle dotaz Hive, a popisuje zelené pole výstup dotaz Hive.

![Vytvořit pracovní prostor](./media/move-hive-tables/run-hive-queries-1.png)

#### <a name="submit-hive-queries-in-hql-files"></a>Odesílání dotazů Hive v souborech .hql
Pokud dotaz Hive je složitější a má více řádků, není praktické úpravy dotazů v příkazovém řádku nebo konzoly příkazu Hive. Alternativou je uložit do souboru .hql do místního adresáře hlavního uzlu dotazů Hive pomocí textového editoru v hlavního uzlu clusteru Hadoop. Potom pomocí jde odeslat dotaz Hive v souboru .hql `-f` argument následujícím způsobem:

    hive -f "<path to the .hql file>"

![Vytvořit pracovní prostor](./media/move-hive-tables/run-hive-queries-3.png)

**Potlačit tisk obrazovky stav průběhu dotazů Hive**

Ve výchozím nastavení po odeslání dotazu Hive v Hadoop příkazového řádku, průběh úlohy mapy nebo snižte se vytiskne na obrazovce. Chcete-li potlačit print obrazovky průběh úlohy mapy nebo snižte, můžete použít argument `-S` ("S velkými písmeny) v příkazu řádek následujícím způsobem:

    hive -S -f "<path to the .hql file>"
.    Hive -S -e "<Hive queries>"

#### <a name="submit-hive-queries-in-hive-command-console"></a>Odesílání dotazů Hive v konzole příkaz Hive.
Příkaz konzole Hive můžete zadat také nejprve spuštěním příkazu `hive` v Hadoop příkazového řádku a pak odesílání dotazů Hive v konzole příkaz Hive. Tady je příklad. V tomto příkladu zvýrazněte dva červená pole příkazy používané k zadání Hive konzoly příkazu a dotaz Hive odeslání v konzole příkaz Hive v uvedeném pořadí. Pole zelená označuje výstup dotaz Hive.

![Vytvořit pracovní prostor](./media/move-hive-tables/run-hive-queries-2.png)

V předchozích příkladech přímo výstupu podregistru výsledky dotazu na obrazovce. Může také zapisovat výstup do místního souboru z hlavního uzlu, nebo do objektu blob Azure. Potom můžete dalších nástrojů k další analýze výstup dotazů Hive.

**Výstup výsledků dotazu Hive do místního souboru.**
K vypsání výsledků dotazu Hive do místního adresáře z hlavního uzlu, budete muset odeslat dotaz Hive v příkazovém řádku Hadoop následujícím způsobem:

    hive -e "<hive query>" > <local path in the head node>

V následujícím příkladu výstupu podregistru dotazu zápisu do souboru `hivequeryoutput.txt` v adresáři `C:\apps\temp`.

![Vytvořit pracovní prostor](./media/move-hive-tables/output-hive-results-1.png)

**Výsledky dotazu výstupu podregistru do objektu blob Azure**

Můžete také výstup výsledků dotazu Hive do objektu blob Azure, v rámci výchozího kontejneru clusteru Hadoop. Pro tento dotaz Hive vypadá takto:

    insert overwrite directory wasb:///<directory within the default container> <select clause from ...>

V následujícím příkladu výstupu podregistru dotazu je zapsán do adresáře, objektů blob `queryoutputdir` v rámci výchozího kontejneru clusteru Hadoop. Zde stačí zadat název adresáře, bez názvu objektu blob. Pokud například zadáte názvy adresáře a objektů blob, je vyvolána chyba `wasb:///queryoutputdir/queryoutput.txt`.

![Vytvořit pracovní prostor](./media/move-hive-tables/output-hive-results-2.png)

Pokud otevřete výchozí kontejner clusteru Hadoop pomocí Průzkumníka úložiště Azure, uvidíte výstup dotaz Hive, jak je znázorněno na následujícím obrázku. Filtr (zvýrazněné podle červeným rámečkem) můžete použít pouze načíst objekt blob se zadaná písmena v názvech.

![Vytvořit pracovní prostor](./media/move-hive-tables/output-hive-results-3.png)

### <a name="hive-editor"></a> 2. Odesílání dotazů Hive pomocí editoru Hive
Můžete také použít konzolu dotazu (Hive Editor) tak, že zadáte adresu URL ve formátu *https://<Hadoop cluster name>.azurehdinsight.net/Home/HiveEditor* do webového prohlížeče. Musí být zaznamenána v najdete v této konzoly a proto je třeba zde pověření clusteru Hadoop.

### <a name="ps"></a> 3. Odesílání dotazů Hive pomocí příkazů prostředí PowerShell Azure
Můžete také použít PowerShell k odesílání dotazů Hive. Pokyny najdete v tématu [úlohy odeslání Hive pomocí prostředí PowerShell](../../hdinsight/hadoop/apache-hadoop-use-hive-powershell.md).

## <a name="create-tables"></a>Vytvoření databáze Hive a tabulky
Sdílené dotazy Hive v [úložiště GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_create_db_tbls_load_data_generic.hql) a z ní si můžete stáhnout.

Zde je dotaz Hive, který vytvoří tabulku Hive.

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

Zde je uveden popis pole, která je potřeba zařadit a další konfigurace:

* **<database name>**: název databáze, kterou chcete vytvořit. Pokud chcete použít výchozí databázi, dotaz *vytvoření databáze...*  lze vynechat.
* **<table name>**: název tabulky, který chcete vytvořit v rámci zadaná databáze. Pokud chcete použít výchozí databáze, v tabulce může být přímo na které odkazuje *<table name>* bez <database name>.
* **<field separator>**: oddělovač, který vymezuje pole v datovém souboru k odeslání na tabulku Hive.
* **<line separator>**: oddělovač, který vymezuje řádků v datovém souboru.
* **<storage location>**: umístění úložiště Azure pro uložení dat tabulek Hive. Pokud nezadáte *umístění <storage location>* , databáze a tabulky jsou uloženy v *hive neboskladu/* adresář ve výchozím kontejneru Hive clusteru ve výchozím nastavení. Pokud chcete zadat umístění úložiště, musí být v rámci výchozího kontejneru databáze a tabulky umístění úložiště. Toto umístění musí být označuje jako umístění relativně k výchozí kontejner clusteru ve formátu *' wasb: / / / < adresář 1 > / "* nebo *' wasb: / / / < adresář 1 > / < adresář 2 > /'* atd. Po provedení dotazu relativní adresáře jsou vytvořeny v rámci výchozího kontejneru.
* **TBLPROPERTIES("Skip.Header.line.Count"="1")**: Pokud datový soubor obsahuje řádek záhlaví, budete muset přidat tato vlastnost **na konci** z *vytvořit tabulku* dotazu. Řádek záhlaví, jinak je načten jako záznamu do tabulky. Pokud datový soubor nemá řádek záhlaví, můžete tuto konfiguraci vynechat v dotazu.

## <a name="load-data"></a>Načtení dat do tabulek Hive
Zde je dotaz Hive, který načítá data do tabulky Hive.

    LOAD DATA INPATH '<path to blob data>' INTO TABLE <database name>.<table name>;

* **<path to blob data>**: Pokud je objekt blob soubor k odeslání do tabulky Hive ve výchozím kontejneru clusteru HDInsight Hadoop *<path to blob data>* by měl být ve formátu *' wasb: / / /<directory in this container> / <blob file name>'*. Soubor blob může být také v další kontejner clusteru HDInsight Hadoop. V takovém případě *<path to blob data>* by měl být ve formátu *' wasb: / /<container name><storage account name>.blob.core.windows.net/<blob file name>'*.

  > [!NOTE]
  > Data objektů blob k odeslání na tabulku Hive musí být ve výchozím nastavení nebo další kontejneru účtu úložiště pro Hadoop cluster. V opačném *načítání dat* nesouhlasících, že nelze přístup k datům se dotaz nezdaří.
  >
  >

## <a name="partition-orc"></a>Rozšířené témata: tabulka a úložiště Hive data ve formátu ORC na oddíly
Pokud data velká, vytváření oddílů v tabulce je výhodné pro dotazy, které stačí ke kontrole několik oddílů tabulky. Například je možné logicky oddílu data protokolu webu pomocí kalendářní data.

Kromě vytváření oddílů tabulek Hive, je také užitečné k ukládání dat Hive ve formátu optimalizované řádek sloupcovém (ORC). Další informace o ORC formátování naleznete v tématu <a href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+ORC#LanguageManualORC-ORCFiles" target="_blank">a souborů ORC pomocí zvyšuje výkon při čtení, zápisu a zpracování dat Hive</a>.

### <a name="partitioned-table"></a>Dělenou tabulku
Zde je dotaz Hive, který vytvoří dělenou tabulku a načte data do ní.

    CREATE EXTERNAL TABLE IF NOT EXISTS <database name>.<table name>
    (field1 string,
    ...
    fieldN string
    )
    PARTITIONED BY (<partitionfieldname> vartype) ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>'
         lines terminated by '<line separator>' TBLPROPERTIES("skip.header.line.count"="1");
    LOAD DATA INPATH '<path to the source file>' INTO TABLE <database name>.<partitioned table name>
        PARTITION (<partitionfieldname>=<partitionfieldvalue>);

Při dotazování dělené tabulky, doporučuje se přidejte podmínku oddílu v **začátku** z `where` klauzule jako to zvyšuje účinnost vyhledávání výrazně.

    select
        field1, field2, ..., fieldN
    from <database name>.<partitioned table name>
    where <partitionfieldname>=<partitionfieldvalue> and ...;

### <a name="orc"></a>Ukládání dat Hive ve formátu ORC
Nelze načíst přímo dat z úložiště objektů blob do tabulek Hive, které je uložený ve formátu ORC. Tady jsou kroky, které nutné provádět k načtení dat z Azure objektů BLOB do tabulek Hive uložený ve formátu ORC.

Vytvoření externí tabulky **ULOŽENÝ textový soubor AS** a načtení dat z úložiště objektů blob do tabulky.

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

Vytvoření interní tabulky se stejným schématem jako externí tabulky v kroku 1, se stejnou oddělovačem polí a ukládat Hive data ve formátu ORC.

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
> Pokud v tabulce textový soubor  *<database name>.<external textfile table name>* má oddíly, v KROKU 3 `SELECT * FROM <database name>.<external textfile table name>` příkaz vybere proměnnou oddílu jako pole v sadě vrácená data. Vkládání do  *<database name>.<ORC table name>* selhání od  *<database name>.<ORC table name>* v poli v schématu tabulky nemá proměnnou oddílu. V takovém případě budete muset konkrétně vyberte pole, která má být vložen do  *<database name>.<ORC table name>* následujícím způsobem:
>
>

        INSERT OVERWRITE TABLE <database name>.<ORC table name> PARTITION (<partition variable>=<partition value>)
           SELECT field1, field2, ..., fieldN
           FROM <database name>.<external textfile table name>
           WHERE <partition variable>=<partition value>;

Je bezpečné vyřadit *<external textfile table name>* při pomocí následujícího dotazu po všechna data byla vložena do *<database name>.<ORC table name>*:

        DROP TABLE IF EXISTS <database name>.<external textfile table name>;

Až projdete tímto postupem, měli byste mít tabulku s daty ve formátu ORC připravené k použití.  
