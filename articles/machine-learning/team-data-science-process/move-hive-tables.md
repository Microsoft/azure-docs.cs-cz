---
title: Vytváření tabulek podregistrů a načítání dat z úložiště objektů BLOB – vědecké zpracování týmových dat
description: Pomocí dotazů na podregistry můžete vytvářet tabulky podregistru a načítat data z úložiště objektů BLOB v Azure. Pro zlepšení výkonu dotazů používejte tabulky podregistru oddílů a formátování optimalizovaného řádku (ORC).
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
ms.openlocfilehash: 5d61c0f5f26bc46b9c4a5bc4a793df1e10710004
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96006725"
---
# <a name="create-hive-tables-and-load-data-from-azure-blob-storage"></a>Vytváření tabulek podregistru a načítání dat z Azure Blob Storage

Tento článek představuje obecné dotazy na podregistry, které vytvářejí tabulky podregistru a načítají data z Azure Blob Storage. Některé doprovodné materiály jsou k dispozici také pro vytváření oddílů tabulek podregistru a při použití formátování optimalizovaného řádku (ORC) ke zlepšení výkonu dotazů.

## <a name="prerequisites"></a>Požadavky
V tomto článku se předpokládá, že máte následující:

* Vytvořili jste účet Azure Storage. Pokud potřebujete pokyny, přečtěte si téma [informace o Azure Storagech účtech](../../storage/common/storage-introduction.md).
* Byl zřízen přizpůsobený cluster Hadoop se službou HDInsight.  Pokud potřebujete pokyny, přečtěte si téma [Instalace clusterů ve službě HDInsight](../../hdinsight/hdinsight-hadoop-provision-linux-clusters.md).
* Byl povolen vzdálený přístup ke clusteru, přihlášený a otevřela konzolu Hadoop Command-Line. Pokud potřebujete pokyny, přečtěte si téma [správa Apache Hadoopch clusterů](../../hdinsight/hdinsight-administer-use-portal-linux.md).

## <a name="upload-data-to-azure-blob-storage"></a>Nahrání dat do služby Azure Blob Storage
Pokud jste virtuální počítač Azure vytvořili podle pokynů uvedených v části [nastavení virtuálního počítače Azure pro pokročilou analýzu](../../machine-learning/data-science-virtual-machine/overview.md), měl by se tento soubor skriptu stáhnout do adresáře *C: \\ Uživatelé \\ \<user name\> \\ soubory \\ data vědy* na virtuálním počítači. Tyto dotazy na podregistry vyžadují pouze zadání schématu dat a konfigurace služby Azure Blob Storage v příslušných polích, která budou připravena k odeslání.

Předpokládáme, že data pro tabulky podregistru jsou v **nekomprimovaném** tabulkovém formátu a že se data nahrála do výchozího (nebo do dalšího) kontejneru účtu úložiště používaného clusterem Hadoop.

Pokud chcete postupovat podle údajů o **cestách NYC taxislužby**, musíte:

* **Stáhněte** si 24 [datových souborů NYC taxislužby pro služební cestu](https://www.andresmh.com/nyctaxitrips) (soubory o 12 cestách a soubory tarifů),
* **rozbalení všech souborů** do souborů. csv a potom
* **nahrajte** je do výchozího (nebo vhodného kontejneru) účtu Azure Storage. možnosti pro takový účet se zobrazí v tématu [použití Azure Storage s clustery Azure HDInsight](../../hdinsight/hdinsight-hadoop-use-blob-storage.md) . Proces odeslání souborů. CSV do výchozího kontejneru v účtu úložiště najdete na této [stránce](hive-walkthrough.md#upload).

## <a name="how-to-submit-hive-queries"></a><a name="submit"></a>Odeslání dotazů na podregistr
Dotazy na podregistr lze odeslat pomocí:

* [Odeslání dotazů na podregistr prostřednictvím příkazového řádku Hadoop v hlavnímu uzlu clusteru Hadoop](#headnode)
* [Odeslání dotazů na podregistr pomocí editoru podregistru](#hive-editor)
* [Odeslání dotazů na podregistr pomocí příkazů Azure PowerShell](#ps)

Dotazy na podregistr jsou podobné jazyku SQL. Pokud jste obeznámeni s SQL, možná najdete [podregistr pro uživatele SQL tahák](https://hortonworks.com/wp-content/uploads/2013/05/hql_cheat_sheet.pdf) užitečné.

Při odesílání dotazu na podregistr můžete také určit cíl výstupu z dotazů na podregistr, ať už se nachází na obrazovce, nebo na místní soubor v hlavním uzlu nebo objektu blob Azure.

### <a name="submit-hive-queries-through-hadoop-command-line-in-headnode-of-hadoop-cluster"></a><a name="headnode"></a>Odeslání dotazů na podregistr prostřednictvím příkazového řádku Hadoop v hlavnímu uzlu clusteru Hadoop
Pokud je dotaz na podregistr složitý, jeho odeslání přímo do hlavního uzlu clusteru Hadoop obvykle vede k rychlejšímu zapínání od odeslání pomocí editoru podregistru nebo Azure PowerShell skriptů.

Přihlaste se k hlavnímu uzlu clusteru Hadoop, otevřete příkazový řádek Hadoop na ploše hlavního uzlu a zadejte příkaz `cd %hive_home%\bin` .

Existují tři způsoby, jak odeslat dotazy na podregistr do příkazového řádku Hadoop:

* přímých
* použití souborů. HQL
* pomocí příkazové konzole pro podregistr

#### <a name="submit-hive-queries-directly-in-hadoop-command-line"></a>Odesílat dotazy na podregistr přímo v příkazovém řádku Hadoop.
Můžete spustit příkaz, jako je například `hive -e "<your hive query>;` odeslání jednoduchých dotazů na podregistr přímo do příkazového řádku Hadoop. Tady je příklad, kde červené pole vypíše příkaz, který odešle dotaz na podregistr, a zelené pole vypíše výstup z dotazu na podregistr.

![Příkaz pro odeslání dotazu na podregistr s výstupem z dotazu na podregistr](./media/move-hive-tables/run-hive-queries-1.png)

#### <a name="submit-hive-queries-in-hql-files"></a>Odeslání dotazů na podregistr do souborů. HQL
Když je dotaz na podregistr složitější a má více řádků, úprava dotazů v příkazovém řádku nebo konzole příkazů podregistru není praktická. Alternativou je použití textového editoru v hlavním uzlu clusteru Hadoop k uložení dotazů na podregistr v souboru. HQL v místním adresáři hlavního uzlu. Pak se dotaz na podregistr v souboru. HQL dá odeslat pomocí `-f` následujícího argumentu:

```console
hive -f "<path to the '.hql' file>"
```

![Dotaz na podregistr v souboru. HQL](./media/move-hive-tables/run-hive-queries-3.png)

**Potlačit obrazovku stavu průběhu – Tisk dotazů na podregistr**

Ve výchozím nastavení se po odeslání dotazu na podregistr do příkazového řádku Hadoop na obrazovce vytiskne průběh úlohy mapa/zmenšování. Chcete-li potlačit tisk obrazovky mapy/snížit průběh úlohy, můžete použít argument `-S` ("S" v horním případě) na příkazovém řádku následujícím způsobem:

```console
hive -S -f "<path to the '.hql' file>"
hive -S -e "<Hive queries>"
```

#### <a name="submit-hive-queries-in-hive-command-console"></a>Odešlete dotazy na podregistr v konzole příkazů pro podregistr.
Můžete taky nejdřív zadat příkazovou konzolu pro podregistr spuštěním příkazu `hive` v příkazovém řádku Hadoop a pak odeslat dotazy na podregistr v konzole příkazů pro podregistr. Zde je příklad. V tomto příkladu jsou dvě červená pole zvýrazněné příkazy, které slouží k zadání konzoly příkazového řádku, a dotaz na podregistr odeslaný do konzoly příkazového řádku v uvedeném pořadí. Zelené pole zvýrazní výstup z dotazu na podregistr.

![Otevřete příkazový konzolu pro podregistr a zadejte příkaz, zobrazte výstup dotazu na podregistry.](./media/move-hive-tables/run-hive-queries-2.png)

Předchozí příklady přímo vychází z výstupů dotazů na podregistry na obrazovce. Výstup můžete také zapsat do místního souboru na hlavní uzel nebo do objektu blob Azure. Pak můžete pomocí dalších nástrojů analyzovat výstup dotazů na podregistry.

**Výsledky dotazu na výstupní podregistry do místního souboru.**
Pro výstup výsledků dotazu na podregistr do místního adresáře v hlavním uzlu je třeba odeslat dotaz na podregistr do příkazového řádku Hadoop následujícím způsobem:

```console
hive -e "<hive query>" > <local path in the head node>
```

V následujícím příkladu je výstup dotazu na podregistr zapsán do souboru `hivequeryoutput.txt` v adresáři `C:\apps\temp` .

![Snímek obrazovky zobrazuje výstup dotazu na podregistr v okně příkazového řádku Hadoop.](./media/move-hive-tables/output-hive-results-1.png)

**Výsledky dotazu na výstupní podregistr do objektu blob Azure**

Můžete také výstup výsledků dotazu na podregistr do objektu blob Azure v rámci výchozího kontejneru clusteru Hadoop. Dotaz na podregistr pro tento postup je následující:

```console
insert overwrite directory wasb:///<directory within the default container> <select clause from ...>
```

V následujícím příkladu se výstup dotazu na podregistr zapisuje do adresáře objektů BLOB `queryoutputdir` v rámci výchozího kontejneru clusteru Hadoop. V tomto případě stačí zadat název adresáře bez názvu objektu BLOB. Pokud zadáte název adresáře a objektů blob, například, je vyvolána chyba `wasb:///queryoutputdir/queryoutput.txt` .

![Snímek obrazovky se zobrazí v okně příkazového řádku Hadoop předchozí příkaz.](./media/move-hive-tables/output-hive-results-2.png)

Pokud otevřete výchozí kontejner clusteru Hadoop pomocí Průzkumník služby Azure Storage, můžete zobrazit výstup dotazu na podregistr, jak je znázorněno na následujícím obrázku. Můžete použít filtr (zvýrazněný červeně), chcete-li načíst pouze objekt BLOB se zadaným písmeny v názvu.

![Průzkumník služby Azure Storage zobrazení výstupu dotazu na podregistr](./media/move-hive-tables/output-hive-results-3.png)

### <a name="submit-hive-queries-with-the-hive-editor"></a><a name="hive-editor"></a>Odeslání dotazů na podregistr pomocí editoru podregistru
Můžete také použít konzolu dotazů (Editor registru) zadáním adresy URL formuláře *https: \/ / \<Hadoop cluster name> . azurehdinsight.NET/Home/HiveEditor* do webového prohlížeče. Je nutné, abyste byli přihlášeni v konzole nástroje, a proto budete potřebovat přihlašovací údaje pro cluster Hadoop.

### <a name="submit-hive-queries-with-azure-powershell-commands"></a><a name="ps"></a>Odeslání dotazů na podregistr pomocí příkazů Azure PowerShell
Pomocí prostředí PowerShell můžete také odesílat dotazy na podregistr. Pokyny najdete v tématu [Odeslání úloh podregistru pomocí prostředí PowerShell](../../hdinsight/hadoop/apache-hadoop-use-hive-powershell.md).

## <a name="create-hive-database-and-tables"></a><a name="create-tables"></a>Vytvoření databáze a tabulek podregistru
Dotazy na podregistr se sdílejí v [úložišti GitHubu](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_create_db_tbls_load_data_generic.hql) a můžete je stáhnout z něj.

Tady je dotaz na podregistr, který vytváří tabulku podregistru.

```hiveql
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
```

Tady jsou popisy polí, která potřebujete připojit, a další konfigurace:

* **\<database name\>**: název databáze, kterou chcete vytvořit. Pokud chcete použít pouze výchozí databázi, dotaz "*vytvořit databázi...*" lze vynechat.
* **\<table name\>**: název tabulky, kterou chcete vytvořit v zadané databázi. Pokud chcete použít výchozí databázi, může být tabulka přímo označována *\<table name\>* bez \<database name\> .
* **\<field separator\>**: oddělovač, který omezuje pole v datovém souboru, který se má odeslat do tabulky podregistru.
* **\<line separator\>**: oddělovač, který omezuje řádky v datovém souboru.
* **\<storage location\>**: umístění Azure Storage pro uložení dat tabulek podregistru. Pokud nezadáte *umístění \<storage location\>*, databáze a tabulky budou ve výchozím nastavení uloženy v *podregistru/skladu/* adresáři ve výchozím kontejneru clusteru podregistru. Pokud chcete zadat umístění úložiště, umístění úložiště musí být ve výchozím kontejneru pro databáze a tabulky. Toto umístění musí být označováno jako umístění relativní vzhledem k výchozímu kontejneru clusteru ve formátu *"wasb:/// \<directory 1> /"* nebo *"wasb:/// \<directory 1> / \<directory 2> /"* atd. Po provedení dotazu se relativní adresáře vytvoří v rámci výchozího kontejneru.
* **TBLPROPERTIES ("Skip. Header. line. Count" = "1")**: Pokud datový soubor obsahuje řádek záhlaví, je nutné tuto vlastnost přidat **na konci** dotazu *Create Table* . V opačném případě se řádek záhlaví načte jako záznam do tabulky. Pokud datový soubor nemá řádek záhlaví, tato konfigurace může být v dotazu vynechána.

## <a name="load-data-to-hive-tables"></a><a name="load-data"></a>Načtení dat do tabulek podregistru
Tady je dotaz na podregistr, který načte data do tabulky podregistru.

```hiveql
LOAD DATA INPATH '<path to blob data>' INTO TABLE <database name>.<table name>;
```

* **\<path to blob data\>**: Pokud se soubor objektu blob, který se má nahrát do tabulky podregistru, nachází ve výchozím kontejneru clusteru HDInsight Hadoop, *\<path to blob data\>* měl by být ve formátu *"wasb:// \<directory in this container> / \<blob file name> "*. Soubor BLOB může být taky v dodatečném kontejneru clusteru HDInsight Hadoop. V takovém případě *\<path to blob data\>* by měla být ve formátu *"wasb:// \<container name> @ \<storage account name> . blob.Core.Windows.NET/ \<blob file name> "*.

  > [!NOTE]
  > Data objektu blob, která se mají nahrát do tabulky podregistru, musí být ve výchozím nebo dodatečném kontejneru účtu úložiště pro cluster Hadoop. V opačném případě dotaz *načíst data* nevydá stížnost, že nemá přístup k datům.
  >
  >

## <a name="advanced-topics-partitioned-table-and-store-hive-data-in-orc-format"></a><a name="partition-orc"></a>Pokročilá témata: dělená tabulka a data z podregistru úložiště ve formátu ORC
Pokud jsou data velká, vytváření oddílů tabulky je užitečné pro dotazy, které potřebují pouze vyhledat několik oddílů v tabulce. Například je vhodné rozdělit data protokolu webového serveru podle data.

Kromě vytváření oddílů tabulek podregistru je také užitečné ukládat data z podregistru do formátu optimalizovaného řádku (ORC). Další informace o formátování ORC najdete v tématu <a href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+ORC#LanguageManualORC-ORCFiles" target="_blank">použití souborů ORC vylepšuje výkon při čtení, zápisu a zpracování dat v podregistru</a>.

### <a name="partitioned-table"></a>Dělená tabulka
Tady je dotaz na podregistr, který vytvoří dělenou tabulku a načte do ní data.

```hiveql
CREATE EXTERNAL TABLE IF NOT EXISTS <database name>.<table name>
(field1 string,
...
fieldN string
)
PARTITIONED BY (<partitionfieldname> vartype) ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>'
    lines terminated by '<line separator>' TBLPROPERTIES("skip.header.line.count"="1");
LOAD DATA INPATH '<path to the source file>' INTO TABLE <database name>.<partitioned table name>
    PARTITION (<partitionfieldname>=<partitionfieldvalue>);
```

Při dotazování na dělené tabulky doporučujeme přidat podmínku oddílu na **začátek** `where` klauzule, což zlepší efektivitu hledání.

```hiveql
select
    field1, field2, ..., fieldN
from <database name>.<partitioned table name>
where <partitionfieldname>=<partitionfieldvalue> and ...;
```

### <a name="store-hive-data-in-orc-format"></a><a name="orc"></a>Uložení dat z podregistru ve formátu ORC
Data z úložiště objektů BLOB není možné přímo načíst do tabulek podregistru, které jsou uložené ve formátu ORC. Tady jsou kroky, které musíte provést, abyste načetli data z objektů blob Azure do tabulek podregistru uložených ve formátu ORC.

Vytvořte externí tabulku **uloženou jako textfile** a načtěte data z úložiště objektů blob do tabulky.

```hiveql
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
```

Vytvořte interní tabulku se stejným schématem, jako má externí tabulka v kroku 1, se stejným oddělovačem polí a uložte data z podregistru ve formátu ORC.

```hiveql
CREATE TABLE IF NOT EXISTS <database name>.<ORC table name>
(
    field1 string,
    field2 int,
    ...
    fieldN date
)
ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>' STORED AS ORC;
```

Vyberte data z externí tabulky v kroku 1 a vložte je do tabulky ORC.

```hiveql
INSERT OVERWRITE TABLE <database name>.<ORC table name>
    SELECT * FROM <database name>.<external textfile table name>;
```

> [!NOTE]
> Pokud *\<database name\> \<external textfile table name\> tabulka textfile* má v kroku 3 oddíly, `SELECT * FROM <database name>.<external textfile table name>` příkaz vybere proměnnou oddílu jako pole ve vrácené datové sadě. Vložení do *\<database name\> . \<ORC table name\>* neproběhne od *\<database name\> . \<ORC table name\>* nemá proměnnou oddílu jako pole ve schématu tabulky. V takovém případě je nutné konkrétně vybrat pole, do kterých mají být vložena *\<database name\> . \<ORC table name\>* následujícím způsobem:
>
>

```hiveql
INSERT OVERWRITE TABLE <database name>.<ORC table name> PARTITION (<partition variable>=<partition value>)
    SELECT field1, field2, ..., fieldN
    FROM <database name>.<external textfile table name>
    WHERE <partition variable>=<partition value>;
```

Po *\<external text file table name\>* vložení všech dat do můžete bezpečně vyřadit při použití následujícího dotazu *\<database name\> . \<ORC table name\>*:

```hiveql
    DROP TABLE IF EXISTS <database name>.<external textfile table name>;
```

Po provedení tohoto postupu byste měli mít tabulku s daty ve formátu ORC, která je připravena k použití.  
