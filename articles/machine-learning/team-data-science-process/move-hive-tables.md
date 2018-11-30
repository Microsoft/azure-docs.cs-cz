---
title: Vytváření tabulek Hive a načtení dat z úložiště objektů Blob v Azure | Dokumentace Microsoftu
description: Vytváření tabulek Hive a načtení dat do objektu blob do tabulky hive
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.component: team-data-science-process
ms.topic: article
ms.date: 11/04/2017
ms.author: tdsp
ms.custom: (previous author=deguhath, ms.author=deguhath)
ms.openlocfilehash: 42911c347cd055f37f7fe8f31b6d22cc18a78662
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/28/2018
ms.locfileid: "52442876"
---
# <a name="create-hive-tables-and-load-data-from-azure-blob-storage"></a>Vytváření tabulek Hive a načtení dat z úložiště objektů Blob v Azure

Tento článek představuje obecné dotazy Hive, které vytváření tabulek Hive a načtení dat z úložiště objektů blob v Azure. Některé poskytujeme také pokyny o dělení tabulek Hive a o používání optimalizované řádek úložiště se sloupcovou strukturou (ORC) formátování pro zlepšení výkonu dotazů.

## <a name="prerequisites"></a>Požadavky
Tento článek předpokládá, že máte:

* Vytvoření účtu služby Azure storage. Pokud potřebujete získat pokyny, přečtěte si téma [účty Azure storage](../../storage/common/storage-create-storage-account.md).
* Zřídit vlastní cluster Hadoop ve službě HDInsight.  Pokud potřebujete získat pokyny, přečtěte si téma [přizpůsobit Azure HDInsight Hadoop clusterů pro pokročilou analýzu](customize-hadoop-cluster.md).
* Povolený vzdálený přístup ke clusteru, přihlášení a otevřít konzolu příkazového řádku Hadoopu. Pokud potřebujete získat pokyny, přečtěte si téma [přístup k hlavní uzel z clusteru Hadoop](customize-hadoop-cluster.md).

## <a name="upload-data-to-azure-blob-storage"></a>Nahrání dat do úložiště objektů blob v Azure
Pokud jste vytvořili virtuální počítač Azure podle pokynů uvedených v [nastavení virtuálního počítače Azure pro pokročilou analýzu](../data-science-virtual-machine/setup-virtual-machine.md), tento soubor skriptu by byly staženy do *C:\\uživatelů \\ \<uživatelské jméno\>\\dokumenty\\Data Science skripty* adresář na virtuálním počítači. Zapojte své vlastní schéma dat a konfigurace úložiště objektů blob v Azure do příslušných polí bude připravené k odeslání vyžadují jenom tyto dotazy Hive.

Předpokládáme, že data do tabulek Hive je v **nekomprimované** formátu tabulky a že je nahraná data na výchozí hodnotu (a další) kontejneru účtu úložiště používá Hadoop cluster.

Pokud chcete postup na **Data o jízdách taxislužby NYC**, budete muset:

* **Stáhněte si** 24 [Data o jízdách taxislužby NYC](http://www.andresmh.com/nyctaxitrips) soubory (12 cesty a 12 tarif souborů)
* **Rozbalte** všechny soubory do souborů CSV a pak
* **Nahrát** je výchozí (nebo odpovídajícího kontejneru), který byl vytvořen pomocí postupu uvedeného v účtu úložiště Azure [přizpůsobit Azure HDInsight Hadoop clusterů pro pokročilé analýzy procesu a technologie](customize-hadoop-cluster.md)tématu. Postup nahrání souborů CSV do výchozí kontejner v účtu úložiště najdete v tomto [stránky](hive-walkthrough.md#upload).

## <a name="submit"></a>Postup odesílání dotazů Hive
Dotazy Hive můžete odeslat pomocí:

1. [Odesílání dotazů Hive pomocí příkazového řádku Hadoopu v hlavního uzlu clusteru Hadoop](#headnode)
2. [Odesílání dotazů Hive pomocí editoru Hive](#hive-editor)
3. [Odesílání dotazů Hive pomocí příkazů prostředí PowerShell pro Azure](#ps)

Dotazy Hive jsou podobném SQL. Pokud jste obeznámeni s SQL, můžete zjistit [Hive pro Ošidit seznam uživatelů SQL](http://hortonworks.com/wp-content/uploads/2013/05/hql_cheat_sheet.pdf) užitečné.

Při odesílání dotazů Hive, můžete také řídit cíl výstupu z dotazů Hive, jestli se na obrazovce nebo do místního souboru hlavního uzlu nebo do objektu blob Azure.

### <a name="headnode"></a> 1. Odesílání dotazů Hive pomocí příkazového řádku Hadoopu v hlavního uzlu clusteru Hadoop
Pokud se dotaz Hive je komplexní, odesláním přímo do hlavního uzlu Hadoop cluster obvykle vede k otočení rychleji než jeho odesláním pomocí skriptů Hive Editor nebo Azure Powershellu.

Přihlaste se k hlavnímu uzlu clusteru Hadoop, otevřete příkazový řádek systému Hadoop v klientských počítačích k hlavnímu uzlu a zadejte příkaz `cd %hive_home%\bin`.

Budete mít tři způsoby, jak odesílání dotazů Hive v příkazovém řádku Hadoop:

* přímo
* použití souborů .hql
* pomocí příkazu konzoly Hive

#### <a name="submit-hive-queries-directly-in-hadoop-command-line"></a>Odesílání dotazů Hive přímo v systému Hadoop příkazového řádku.
Můžete spustit příkaz jako `hive -e "<your hive query>;` odeslat jednoduchých dotazů Hive přímo v systému Hadoop příkazového řádku. Tady je příklad, ve kterém červeným rámečkem popisuje příkaz, který odešle dotaz Hive a zeleného pole obsahuje výstup dotazu Hive.

![Vytvoření pracovního prostoru](./media/move-hive-tables/run-hive-queries-1.png)

#### <a name="submit-hive-queries-in-hql-files"></a>Odesílání dotazů Hive v souborech .hql
Pokud dotaz Hive je složitější a obsahuje více řádků, není praktické úpravy dotazů v příkazovém řádku nebo Hive příkazové konzole. Alternativou je uložit do souboru .hql do místního adresáře hlavního uzlu dotazů Hive pomocí textového editoru v k hlavnímu uzlu clusteru Hadoop. Pak můžete odeslat dotaz Hive v souboru .hql pomocí `-f` argument následujícím způsobem:

    hive -f "<path to the .hql file>"

![Vytvoření pracovního prostoru](./media/move-hive-tables/run-hive-queries-3.png)

**Potlačit obrazovka průběhu stavové dotazů Hive**

Ve výchozím nastavení Jakmile dotaz Hive se odešle do příkazového řádku Hadoopu, průběh úloh mapování/zmenšování se vytiskne na obrazovce. Pokud chcete potlačit obrazovky tisku průběhu úloh mapování/zmenšování, můžete použít argument `-S` ("S" velkými písmeny) v příkazu řádek následujícím způsobem:

    hive -S -f "<path to the .hql file>"
    hive -S -e "<Hive queries>"

#### <a name="submit-hive-queries-in-hive-command-console"></a>Odesílání dotazů Hive v příkazové konzole Hive.
Příkaz konzolu Hive můžete zadat také nejprve spuštěním příkazu `hive` v systému Hadoop příkazového řádku a potom odesílání dotazů Hive v příkazové konzole Hive. Tady je příklad. V tomto příkladu zvýrazněte dva červená pole použité ke vstupu Hive příkazové konzole příkazy a dotaz Hive v příkazové konzole Hive, respektive odeslán. Zeleného pole zvýrazní výstup dotazu Hive.

![Vytvoření pracovního prostoru](./media/move-hive-tables/run-hive-queries-2.png)

V předchozích příkladech přímo výstup výsledků dotazu Hive na obrazovce. Můžete také napsat výstup do místního souboru hlavního uzlu, nebo do objektu blob Azure. Potom můžete použít jiné nástroje Pokud chcete hlouběji analyzovat výstup dotazů Hive.

**Výstup výsledků dotazu Hive do místního souboru.**
K vypsání výsledků dotazu Hive do místního adresáře hlavního uzlu, budete muset odeslat dotaz Hive v příkazovém řádku Hadoopu následujícím způsobem:

    hive -e "<hive query>" > <local path in the head node>

V následujícím příkladu je výstup dotazu Hive zapsán do souboru `hivequeryoutput.txt` v adresáři `C:\apps\temp`.

![Vytvoření pracovního prostoru](./media/move-hive-tables/output-hive-results-1.png)

**Výstup výsledků dotazu Hive do objektu blob Azure**

Můžete také výstup výsledků dotazu Hive do objektu blob Azure, v rámci výchozího kontejneru Hadoop cluster. Dotaz Hive pro to vypadá takto:

    insert overwrite directory wasb:///<directory within the default container> <select clause from ...>

V následujícím příkladu je výstup dotazu Hive zapsán do adresáře objektů blob `queryoutputdir` v rámci výchozího kontejneru Hadoop cluster. Tady stačí zadat název adresáře, bez názvu objektu blob. Pokud zadáte názvy adresáře a objektů blob, jako například, je vržena chyba `wasb:///queryoutputdir/queryoutput.txt`.

![Vytvoření pracovního prostoru](./media/move-hive-tables/output-hive-results-2.png)

Pokud otevřete výchozí kontejner clusteru Hadoop pomocí Průzkumníka služby Azure Storage, zobrazí se výstup dotazu Hive, jak je znázorněno na následujícím obrázku. Filter (zvýrazněná červeným rámečkem) můžete použít k načtení pouze objekt blob se zadaným písmena v názvech.

![Vytvoření pracovního prostoru](./media/move-hive-tables/output-hive-results-3.png)

### <a name="hive-editor"></a> 2. Odesílání dotazů Hive pomocí editoru Hive
Můžete také použít konzolu dotazu (Hive Editor) tak, že zadáte adresu URL ve formátu *https://<Hadoop cluster name>.azurehdinsight.net/Home/HiveEditor* do webového prohlížeče. Musíte být přihlášeni se tato konzola, takže je nutné pověření clusteru Hadoop tady.

### <a name="ps"></a> 3. Odesílání dotazů Hive pomocí příkazů prostředí PowerShell pro Azure
Můžete také použít PowerShell k odesílání dotazů Hive. Pokyny najdete v tématu [Hive odeslání úlohy pomocí prostředí PowerShell](../../hdinsight/hadoop/apache-hadoop-use-hive-powershell.md).

## <a name="create-tables"></a>Vytvořit databázi Hive a tabulky
Dotazy Hive jsou sdílené v [úložiště GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_create_db_tbls_load_data_generic.hql) a odtud se dají stáhnout.

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

* **<database name>**: název databáze, kterou chcete vytvořit. Pokud chcete použít výchozí databázi dotaz *vytvořit databázi...*  lze vynechat.
* **<table name>**: název tabulky, kterou chcete vytvořit v rámci zadané databázi. Pokud chcete použít výchozí databázi, se v tabulce můžete přímo odkazuje *<table name>* bez <database name>.
* **<field separator>**: oddělovač, který odděluje citaci pole v datovém souboru k odeslání do tabulky Hive.
* **<line separator>**: oddělovač, který odděluje citaci řádků v datovém souboru služby.
* **<storage location>**: umístění úložiště Azure storage k ukládání dat tabulky Hive. Pokud nezadáte *umístění <storage location>* , databáze a tabulky uložené v *hive/warehouse/* adresáře ve výchozím kontejneru clusteru Hive ve výchozím nastavení. Pokud chcete zadat umístění úložiště, umístění úložiště musí být v rámci výchozího kontejneru pro databáze a tabulky. Toto umístění se musí se označuje jako umístění vzhledem k výchozí kontejner clusteru ve formátu *"wasb: / / / / / < adresář 1 > /"* nebo *"wasb: / / / / / < adresář 1 > / < adresář 2 > /"* atd. Po spuštění dotazu relativní adresáře se vytvoří v rámci výchozího kontejneru.
* **TBLPROPERTIES("Skip.Header.line.Count"="1")**: datový soubor obsahuje řádek záhlaví, budete muset přidat tuto vlastnost **na konci** z *vytvořit tabulku* dotazu. V opačném případě řádek záhlaví je načtena jako záznam do tabulky. Pokud datový soubor nemá řádek záhlaví, tato konfigurace může vynechat v dotazu.

## <a name="load-data"></a>Načtení dat do tabulek Hive
Tady je dotaz Hive, který načítá data do tabulky Hive.

    LOAD DATA INPATH '<path to blob data>' INTO TABLE <database name>.<table name>;

* **<path to blob data>**: Pokud soubor objektu blob k nahrání do tabulky Hive je ve výchozím kontejneru clusteru HDInsight Hadoop *<path to blob data>* by měl být ve formátu *"wasb: / / / / /<directory in this container> / <blob file name>'*. Soubor objektu blob může být také v dalších kontejneru clusteru HDInsight Hadoop. V takovém případě *<path to blob data>* by měl být ve formátu *"wasb: / /<container name><storage account name>.blob.core.windows.net/<blob file name>"*.

  > [!NOTE]
  > Data objektů blob k nahrání do tabulky Hive musí být ve výchozím nastavení nebo další kontejneru účtu úložiště pro Hadoop cluster. V opačném případě *NAČÍST DATA* stěžovali, nelze přístup k datům se dotaz nezdaří.
  >
  >

## <a name="partition-orc"></a>Pokročilá témata: dělené tabulky a úložiště dat Hive ve formátu ORC
Pokud jsou data velká, vytváření oddílů v tabulce je přínosné pro dotazy, které stačí kontrolovat několik oddílů v tabulce. Například je možné logicky rozdělení dat protokolu z webu data.

Kromě dělení tabulek Hive, je také vhodné k uložení dat Hive ve formátu optimalizované řádek úložiště se sloupcovou strukturou (ORC). Další informace o formátování ORC najdete v tématu <a href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+ORC#LanguageManualORC-ORCFiles" target="_blank">soubory ORC pomocí zlepšuje výkon při čtení, zápisu a zpracování dat Hive</a>.

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

Při dotazování na dělené tabulky, doporučuje se přidat podmínku oddílu v **začátek** z `where` klauzule jako to zvyšuje účinnost vyhledávání výrazně.

    select
        field1, field2, ..., fieldN
    from <database name>.<partitioned table name>
    where <partitionfieldname>=<partitionfieldvalue> and ...;

### <a name="orc"></a>Data Store Hive ve formátu ORC
Nelze načíst přímo data z úložiště objektů blob do tabulek Hive, která je uložena ve formátu ORC. Tady jsou kroky, které nutné provádět k načtení dat z Azure, objekty BLOB do tabulek Hive, které jsou uložené ve formátu ORC.

Vytvoření externí tabulky **ULOŽEN jako textový soubor** a načtení dat z úložiště objektů blob do tabulky.

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
> Pokud textový soubor tabulky  *<database name>.<external textfile table name>* má oddíly, v KROKU 3 `SELECT * FROM <database name>.<external textfile table name>` vybere příkaz jako pole v sadě dat vrácené proměnnou oddílu. Vložení do aplikace  *<database name>.<ORC table name>* selhání od  *<database name>.<ORC table name>* ve schématu tabulky jako pole nemá žádné proměnné oddílu. V takovém případě musíte konkrétně vyberte pole, která se má vložit do  *<database name>.<ORC table name>* následujícím způsobem:
>
>

        INSERT OVERWRITE TABLE <database name>.<ORC table name> PARTITION (<partition variable>=<partition value>)
           SELECT field1, field2, ..., fieldN
           FROM <database name>.<external textfile table name>
           WHERE <partition variable>=<partition value>;

Můžete bezpečně vyřadit *<external textfile table name>* při pomocí následujícího dotazu po všechna data byla vložena do *<database name>.<ORC table name>*:

        DROP TABLE IF EXISTS <database name>.<external textfile table name>;

Po provedení tohoto postupu, měli byste mít tabulku s daty ve formátu ORC připravené k použití.  
