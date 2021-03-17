---
title: Použití Azure HDInsight Hadoop clusteru v 1 TB datové sadě – proces týmu pro datové vědy
description: Použití vědeckého procesu týmového zpracování dat pro kompletní scénář, který využívá cluster systému Hadoop HDInsight k sestavení a nasazení modelu pomocí rozsáhlé datové sady (1 TB), která je veřejně dostupná
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
ms.openlocfilehash: e66bd0a4e56f63185d8361355d6cf8e0e29bc30b
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2020
ms.locfileid: "93305942"
---
# <a name="the-team-data-science-process-in-action---using-an-azure-hdinsight-hadoop-cluster-on-a-1-tb-dataset"></a>Vědecké zpracování týmových dat v akci – použití clusteru Azure HDInsight Hadoop s datovou sadou 1 TB

Tento názorný postup ukazuje, jak používat vědecký proces týmového zpracování dat v rámci kompletního scénáře s [Azure HDInsight Hadoopm clusterem](https://azure.microsoft.com/services/hdinsight/) pro ukládání, zkoumání, inženýry funkcí a vypnutí ukázkových dat z jedné z veřejně dostupných datových sad [Criteo](https://labs.criteo.com/downloads/download-terabyte-click-logs/) . Používá Azure Machine Learning k sestavení binárního klasifikačního modelu pro tato data. Také ukazuje, jak jeden z těchto modelů publikovat jako webovou službu.

Pomocí poznámkového bloku IPython je také možné plnit úkoly uvedené v tomto návodu. Uživatelé, kteří by chtěli tento přístup vyzkoušet, by se měli obrátit na [návod Criteo pomocí tématu připojení k rozhraní ODBC pro podregistr](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-hive-walkthrough-criteo.ipynb) .

## <a name="criteo-dataset-description"></a><a name="dataset"></a>Popis datové sady Criteo
Criteo data jsou datovou sadou předpovědi kliknutí, která je 370 GB souborů TSV komprimovaných pomocí gzip (~ 1,3 TB Uncompressed), které tvoří více než 4 300 000 000 záznamů. Vychází ze 24 dnů od kliknutí na data, která jsou k dispozici v [Criteo](https://labs.criteo.com/downloads/download-terabyte-click-logs/). Pro usnadnění práce s odborníky přes data byla k dispozici data, která jsme mohli experimentovat.

Každý záznam v této datové sadě obsahuje 40 sloupců:

* první sloupec je sloupec popisku, který označuje, jestli uživatel klikne na **Přidání** (hodnota 1), nebo neklikne na jednu (hodnota 0).
* Další 13 sloupců je číselné a
* posledních 26 jsou sloupce kategorií.

Sloupce jsou anonymity a používají řadu výčtových názvů: "Sloupec1" (pro sloupec popisek) na "Col40" (pro poslední sloupec kategorií).

Tady je výňatek z prvních 20 sloupců dvou pozorování (řádků) z této datové sady:

> `Col1    Col2    Col3    Col4    Col5    Col6    Col7    Col8    Col9    Col10    Col11    Col12    Col13    Col14    Col15            Col16            Col17            Col18            Col19        Col20`
>
> `0       40      42      2       54      3       0       0       2       16      0       1       4448    4       1acfe1ee        1b2ff61f        2e8b2631        6faef306        c6fc10d3    6fcd6dcb`
>
> `0               24              27      5               0       2       1               3       10064           9a8cb066        7a06385f        417e6103        2170fc56        acf676aa    6fcd6dcb`

V číselném i kategorií sloupci v této datové sadě chybí hodnoty. Je popsána jednoduchá metoda pro zpracování chybějících hodnot. Další podrobnosti o datech se procházejí při jejich ukládání do tabulek podregistru.

**Definice:** prochází na základě *úrokové míry (centra):* Tato metrika je procento kliknutí v datech. V této Criteo datové sadě má centrum přibližně 3,3% nebo 0,033.

## <a name="examples-of-prediction-tasks"></a><a name="mltasks"></a>Příklady úkolů předpovědi
V tomto návodu jsou řešeny dva ukázkové problémy předpovědi:

1. **Binární klasifikace** : odhadne, jestli uživatel klikl na přidání:

   * Třída 0: žádné kliknutí
   * Třída 1: klikněte na
2. **Regrese** : předpověď pravděpodobnosti kliknutí na reklamu z uživatelských funkcí.

## <a name="set-up-an-hdinsight-hadoop-cluster-for-data-science"></a><a name="setup"></a>Nastavení clusteru HDInsight Hadoop pro datové vědy
> [!NOTE]
> Tento krok je obvykle úkol **správce** .

Nastavte své prostředí pro datové vědy pro Azure pro vytváření prediktivních analytických řešení s clustery HDInsight ve třech krocích:

1. [Vytvoření účtu úložiště](../../storage/common/storage-account-create.md): Tento účet úložiště se používá k ukládání dat v Azure Blob Storage. Zde jsou uložená data používaná v clusterech HDInsight.
2. [Přizpůsobení Azure HDInsight Hadoopch clusterů pro datové vědy](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md): Tento krok vytvoří Azure HDInsight Hadoop cluster s 64. Anaconda Python 2,7 nainstalovanou na všech uzlech. Existují dva důležité kroky (popsané v tomto tématu) k dokončení přizpůsobení clusteru HDInsight.

   * Propojte účet úložiště vytvořený v kroku 1 s clusterem HDInsight při jeho vytvoření. Tento účet úložiště se používá pro přístup k datům, která se dají zpracovat v rámci clusteru.
   * Po vytvoření služby Povolte vzdálený přístup k hlavnímu uzlu clusteru. Zapamatování přihlašovacích údajů vzdáleného přístupu, které tady zadáte (liší se od přihlašovacích údajů zadaných při vytváření clusteru): proveďte následující postupy.
3. [Vytvoření pracovního prostoru Azure Machine Learning Studio (klasický)](../classic/create-workspace.md): Tento Azure Machine Learning pracovní prostor se používá k sestavování modelů strojového učení po počátečním zkoumání dat a jejich vzorkování v clusteru HDInsight.

## <a name="get-and-consume-data-from-a-public-source"></a><a name="getdata"></a>Získání a využití dat z veřejného zdroje
K datové sadě [Criteo](https://labs.criteo.com/downloads/download-terabyte-click-logs/) lze získat pøístup kliknutím na odkaz, přijetím podmínek použití a zadáním názvu. Snímek je zobrazen zde:

![Přijmout Criteo podmínky](./media/hive-criteo-walkthrough/hLxfI2E.png)

Kliknutím na **pokračovat se můžete stáhnout** a přečíst si další informace o datové sadě a její dostupnosti.

Data se nacházejí v umístění [úložiště objektů BLOB v Azure](../../storage/blobs/storage-quickstart-blobs-dotnet.md) : wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/ . "Wasb" odkazuje na umístění Azure Blob Storage.

1. Data v tomto úložišti objektů BLOB v Azure se skládají ze tří podsložek nekomprimovaných dat.

   1. Podsložka *nezpracované/počet/* obsahuje prvních 21 dnů od dne \_ 00 do dne \_ 20
   2. Podsložka *raw/vlak/* sestává z jednoho dne, dne \_ 21
   3. Podsložka *raw/test/* se skládá ze dvou dnů data, dne \_ 22 a dne \_ 23.
2. Nezpracovaná data gzip jsou také k dispozici v hlavní složce *raw/* as day_NN. gz, kde NN přechází z 00 na 23.

Alternativním přístupem k datům, prozkoumání a modelování těchto dat, která nevyžadují žádné místní soubory ke stažení, je vysvětleno dále v tomto návodu, když vytváříme tabulky podregistru.

## <a name="log-in-to-the-cluster-headnode"></a><a name="login"></a>Přihlášení ke clusteru hlavnímu uzlu
Pokud se chcete přihlásit k hlavnímu uzlu clusteru, najděte cluster pomocí [Azure Portal](https://ms.portal.azure.com) . Na levé straně klikněte na ikonu HDInsight slonů a pak dvakrát klikněte na název clusteru. Přejděte na kartu **Konfigurace** , dvakrát klikněte na ikonu připojit v dolní části stránky a po zobrazení výzvy zadejte přihlašovací údaje vzdáleného přístupu, které vás propojí s hlavnímu uzlu clusteru.

Tady je příklad typického prvního přihlášení ke clusteru hlavnímu uzlu vypadá takto:

![Přihlášení ke clusteru](./media/hive-criteo-walkthrough/Yys9Vvm.png)

Vlevo je příkazový řádek Hadoop, který je náš WorkHorse pro zkoumání dat. Všimněte si dvou užitečných adres URL – "stav Hadoop nitě" a "uzel názvu Hadoop". Adresa URL stavu příze zobrazuje průběh úlohy a adresa URL uzlu s názvem obsahuje podrobnosti o konfiguraci clusteru.

Nyní jste nastavili a připraveni začít první část návodu: zkoumání dat pomocí podregistru a získání dat pro Azure Machine Learning.

## <a name="create-hive-database-and-tables"></a><a name="hive-db-tables"></a> Vytvoření databáze a tabulek podregistru
Chcete-li vytvořit tabulky podregistru pro naši datovou sadu Criteo, otevřete příkazový *_řádek systému Hadoop_* _ na ploše hlavního uzlu a zadejte adresář podregistru zadáním příkazu.

```console
cd %hive_home%\bin
```

> [!NOTE]
> Spustí všechny příkazy podregistru v tomto návodu z adresáře bin/adresáře podadresáře. To má za starosti jakékoli problémy s cestou. Můžete použít výrazy "výzva ke složce adresáře pro podregistr", "bin podadresáře/adresář" a "příkazový řádek Hadoop" se zaměnit.
>
> [!NOTE]
> K provedení jakéhokoli dotazu na podregistr může jeden z nich vždy použít následující příkazy:
>
> `cd %hive_home%\bin`
> `hive`

Po zobrazení podregistru se REPL s označením "podregistr >", jednoduše vyvyjměte a vložte dotaz, který chcete spustit.

Následující kód vytvoří databázi "Criteo" a pak vygeneruje čtyři tabulky:

_ *tabulka pro generování počtů* na základě dnů od \_ 00 do dne \_ 20,
* tabulka, která *se má použít jako datová sada vlaku* postavená dne \_ 21.
* dvě *tabulky pro použití jako testovací datové sady, které* jsou založené na dni \_ 22 a dne \_ 23.

Rozdělte testovací sadu do dvou různých tabulek, protože jeden ze dnů je svátek. Cílem je určit, jestli model dokáže detekovat rozdíly mezi volnou a nedovolenou sazbou za kliknutí.

Ukázka skriptu [&#95;podregistr&#95;create&#95;criteo&#95;database&#95;a&#95;Tables.](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_create_criteo_database_and_tables.hql) pro usnadnění práce se tady zobrazí HQL:

```hiveql
CREATE DATABASE IF NOT EXISTS criteo;
DROP TABLE IF EXISTS criteo.criteo_count;
CREATE TABLE criteo.criteo_count (
col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
LINES TERMINATED BY '\n'
STORED AS TEXTFILE LOCATION 'wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/count';

DROP TABLE IF EXISTS criteo.criteo_train;
CREATE TABLE criteo.criteo_train (
col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
LINES TERMINATED BY '\n'
STORED AS TEXTFILE LOCATION 'wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/train';

DROP TABLE IF EXISTS criteo.criteo_test_day_22;
CREATE TABLE criteo.criteo_test_day_22 (
col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
LINES TERMINATED BY '\n'
STORED AS TEXTFILE LOCATION 'wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/test/day_22';

DROP TABLE IF EXISTS criteo.criteo_test_day_23;
CREATE TABLE criteo.criteo_test_day_23 (
col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
LINES TERMINATED BY '\n'
STORED AS TEXTFILE LOCATION 'wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/test/day_23';
```

Všechny tyto tabulky jsou externí, takže můžete odkazovat na jejich umístění Azure Blob Storage (wasb).

**Existují dva způsoby, jak spustit libovolný dotaz z podregistru:**

* **Pomocí příkazového řádku REPL podregistru** : první z nich je vydání příkazu "podregistr" a zkopírování a vložení dotazu do podregistru REPL příkazového řádku:

  ```console
  cd %hive_home%\bin
  hive
  ```

     Nyní na příkazovém řádku REPL provede vyjmutí a vložení dotazu.
* **Ukládání dotazů do souboru a spuštění příkazu** : druhý postup uloží dotazy do souboru. HQL ( [vzorový&#95;podregistr&#95;create&#95;criteo&#95;database&#95;a&#95;Tables. HQL](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_create_criteo_database_and_tables.hql)) a potom pro spuštění dotazu vydejte následující příkaz:

  ```console
  hive -f C:\temp\sample_hive_create_criteo_database_and_tables.hql
  ```

### <a name="confirm-database-and-table-creation"></a>Potvrdit vytvoření databáze a tabulky
Dále potvrďte vytvoření databáze pomocí následujícího příkazu z přihrádky podadresáře/adresáře pro podregistr:

```console
hive -e "show databases;"
```

To poskytuje:

```output
criteo
default
Time taken: 1.25 seconds, Fetched: 2 row(s)
```

Tím se potvrdí vytvoření nové databáze "Criteo".

Chcete-li zjistit, které tabulky byly vytvořeny, jednoduše vydejte příkaz z přihrádky/adresáře podregistru:

```output
hive -e "show tables in criteo;"
```

Měl by se zobrazit následující výstup:

```ouput
criteo_count
criteo_test_day_22
criteo_test_day_23
criteo_train
Time taken: 1.437 seconds, Fetched: 4 row(s)
```

## <a name="data-exploration-in-hive"></a><a name="exploration"></a> Zkoumání dat v podregistru
Nyní jste připraveni provést některé základní zkoumání dat v podregistru. Začnete tím, že počítáte počet příkladů v tabulkách vlakových a testovacích dat.

### <a name="number-of-train-examples"></a>Počet příkladů vlaků
Obsah [ukázkového&#95;podregistru&#95;count&#95;vlak&#95;table&#95;Examples. HQL](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_count_train_table_examples.hql) jsou zde uvedeny:

```hiveql
SELECT COUNT(*) FROM criteo.criteo_train;
```

To má za důsledek:

```output
192215183
Time taken: 264.154 seconds, Fetched: 1 row(s)
```

Alternativně může následující příkaz vystavit na příkazovém řádku bin nebo adresáře podadresáře:

```console
hive -f C:\temp\sample_hive_count_criteo_train_table_examples.hql
```

### <a name="number-of-test-examples-in-the-two-test-datasets"></a>Počet příkladů testů v obou testovacích datových sadách
Nyní spočítá počet příkladů ve dvou testovacích datových sadách. Obsah [ukázkového&#95;podregistru&#95;count&#95;criteo&#95;test&#95;den&#95;22&#95;tabulka&#95;příklady. HQL](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_count_criteo_test_day_22_table_examples.hql) jsou tady:

```hiveql
SELECT COUNT(*) FROM criteo.criteo_test_day_22;
```

To má za důsledek:

```output
189747893
Time taken: 267.968 seconds, Fetched: 1 row(s)
```

V obvyklých případech můžete skript volat také z adresářové přihrádky/adresáře, a to vydáním příkazu:

```console
hive -f C:\temp\sample_hive_count_criteo_test_day_22_table_examples.hql
```

Nakonec prověřte počet příkladů testů v datové sadě testu na základě dne \_ 23.

Příkaz, který se má udělat, je podobný jako na zobrazené stránce (podívejte se na [vzorový&#95;podregistr&#95;count&#95;criteo&#95;test&#95;den&#95;23&#95;příklady. HQL](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_count_criteo_test_day_23_examples.hql)):

```hiveql
SELECT COUNT(*) FROM criteo.criteo_test_day_23;
```

To poskytuje:

```output
178274637
Time taken: 253.089 seconds, Fetched: 1 row(s)
```

### <a name="label-distribution-in-the-train-dataset"></a>Distribuce popisků v datové sadě vlaků
Distribuce popisků v datové sadě vlaků je zajímavá. Pokud to chcete zobrazit, zobrazte obsah [ukázkové&#95;podregistru&#95;criteo&#95;label&#95;&#95;&#95;Table. HQL](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_label_distribution_train_table.hql):

```hiveql
SELECT Col1, COUNT(*) AS CT FROM criteo.criteo_train GROUP BY Col1;
```

To má za důsledek distribuci popisku:

```output
1       6292903
0       185922280
Time taken: 459.435 seconds, Fetched: 2 row(s)
```

Procentuální hodnota pozitivních popisků je přibližně 3,3% (v souladu s původní datovou sadou).

### <a name="histogram-distributions-of-some-numeric-variables-in-the-train-dataset"></a>Distribuce histogramu některých číselných proměnných v datové sadě vlaků
\_K zjištění toho, co vypadá, jak se bude distribuce číselných proměnných líbit, můžete použít funkci "s nativním histogramem" v podregistru. Tady je obsah [ukázkového&#95;podregistru&#95;criteo&#95;histogram&#95;numeric. HQL](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_histogram_numeric.hql):

```hiveql
SELECT CAST(hist.x as int) as bin_center, CAST(hist.y as bigint) as bin_height FROM
    (SELECT
    histogram_numeric(col2, 20) as col2_hist
    FROM
    criteo.criteo_train
    ) a
    LATERAL VIEW explode(col2_hist) exploded_table as hist;
```

To má za důsledek následující:

```output
26      155878415
2606    92753
6755    22086
11202   6922
14432   4163
17815   2488
21072   1901
24113   1283
27429   1225
30818   906
34512   723
38026   387
41007   290
43417   312
45797   571
49819   428
53505   328
56853   527
61004   160
65510   3446
Time taken: 317.851 seconds, Fetched: 20 row(s)
```

Kombinovaný pohled – kombinace v podregistru slouží k vytvoření výstupu podobného SQL namísto normálního seznamu. V této tabulce odpovídá první sloupec centru bin a druhý k frekvenci přihrádek.

### <a name="approximate-percentiles-of-some-numeric-variables-in-the-train-dataset"></a>Přibližné percentily některých číselných proměnných v datové sadě vlaků
Také úroky s číselnými proměnnými je výpočet přibližných percentilů. Pro nás je to v nativním "percentilu" \_ . Obsah [ukázkového&#95;podregistru&#95;criteo&#95;přibližná&#95;percentily. HQL](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_approximate_percentiles.hql) jsou:

```hiveql
SELECT MIN(Col2) AS Col2_min, PERCENTILE_APPROX(Col2, 0.1) AS Col2_01, PERCENTILE_APPROX(Col2, 0.3) AS Col2_03, PERCENTILE_APPROX(Col2, 0.5) AS Col2_median, PERCENTILE_APPROX(Col2, 0.8) AS Col2_08, MAX(Col2) AS Col2_max FROM criteo.criteo_train;
```

To má za důsledek:

```output
1.0     2.1418600917169246      2.1418600917169246    6.21887086390288 27.53454893115633       65535.0
Time taken: 564.953 seconds, Fetched: 1 row(s)
```

Rozdělení percentilů je úzce spojené s distribucí histogramu libovolné číselné proměnné, obvykle.

### <a name="find-number-of-unique-values-for-some-categorical-columns-in-the-train-dataset"></a>Najde počet jedinečných hodnot pro některé sloupce kategorií v datové sadě vlaků.
Pokračování v průzkumu dat, hledání pro některé sloupce kategorií, počet jedinečných hodnot, které přijmou. Provedete to tak, že zobrazíte obsah [ukázkové&#95;podregistr&#95;criteo&#95;jedinečné hodnoty&#95;&#95;categoricals. HQL](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_unique_values_categoricals.hql):

```hiveql
SELECT COUNT(DISTINCT(Col15)) AS num_uniques FROM criteo.criteo_train;
```

To má za důsledek:

```output
19011825
Time taken: 448.116 seconds, Fetched: 1 row(s)
```

Col15 má 19M jedinečné hodnoty! Použití technik Naive jako "jediného horkého kódování" ke kódování těchto kategoriích proměnných není proveditelné. Konkrétně je vysvětleno a ukázáno zejména výkonná a robustní technika [s názvem učení s](/archive/blogs/machinelearning/big-learning-made-easy-with-counts) využitím počtů pro řešení tohoto problému efektivně.

Nakonec se podívejte na počet jedinečných hodnot v některých dalších kategorií sloupcích. Obsah [ukázkového&#95;podregistru&#95;criteo&#95;jedinečné hodnoty&#95;&#95;více&#95;categoricals. HQL](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_unique_values_multiple_categoricals.hql) jsou:

```hiveql
SELECT COUNT(DISTINCT(Col16)), COUNT(DISTINCT(Col17)),
COUNT(DISTINCT(Col18), COUNT(DISTINCT(Col19), COUNT(DISTINCT(Col20))
FROM criteo.criteo_train;
```

To má za důsledek:

```output
30935   15200   7349    20067   3
Time taken: 1933.883 seconds, Fetched: 1 row(s)
```

Znovu si všimněte, že kromě Col20 mají všechny ostatní sloupce mnoho jedinečných hodnot.

### <a name="co-occurrence-counts-of-pairs-of-categorical-variables-in-the-train-dataset"></a>Počet souběžných výskytů párů proměnných kategorií v datové sadě vlaků

Počet distribucí párů kategorií proměnných je také zajímavá. To se dá určit pomocí kódu v [ukázce&#95;podregistr&#95;criteo&#95;spárovaných&#95;kategorií&#95;Counts. HQL](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_paired_categorical_counts.hql):

```hiveql
SELECT Col15, Col16, COUNT(*) AS paired_count FROM criteo.criteo_train GROUP BY Col15, Col16 ORDER BY paired_count DESC LIMIT 15;
```

V tomto případě změňte pořadí počtů podle jejich výskytu a v tomto případě se podívejte na prvních 15. To nám umožní:

```output
ad98e872        cea68cd3        8964458
ad98e872        3dbb483e        8444762
ad98e872        43ced263        3082503
ad98e872        420acc05        2694489
ad98e872        ac4c5591        2559535
ad98e872        fb1e95da        2227216
ad98e872        8af1edc8        1794955
ad98e872        e56937ee        1643550
ad98e872        d1fade1c        1348719
ad98e872        977b4431        1115528
e5f3fd8d        a15d1051        959252
ad98e872        dd86c04a        872975
349b3fec        a52ef97d        821062
e5f3fd8d        a0aaffa6        792250
265366bf        6f5c7c41        782142
Time taken: 560.22 seconds, Fetched: 15 row(s)
```

## <a name="down-sample-the-datasets-for-azure-machine-learning"></a><a name="downsample"></a> Debrat ukázku datových sad pro Azure Machine Learning
Po prozkoumání datových sad a demonstrace toho, jak tento typ průzkumu provést pro jakékoli proměnné (včetně kombinací), vypište vzorek datových sad tak, aby bylo možné sestavit modely v Azure Machine Learning. Zajistěte, aby se při výběru problému objevila tato: předána sada ukázkových atributů (hodnoty funkcí z col2-Col40), předpověď, pokud je hodnota Sloupec1 0 (žádné kliknutí), nebo 1 (klikněte).

Chcete-li vzorkovat sadu vlakových a testovacích datových sad na 1% původní velikosti, použijte funkci Native RAND () podregistru. Další skript, [ukázkový&#95;podregistr&#95;criteo&#95;převzorkovat&#95;vlak&#95;DataSet. HQL](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_downsample_train_dataset.hql) pro datovou sadu vlaků:

```hiveql
CREATE TABLE criteo.criteo_train_downsample_1perc (
col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
LINES TERMINATED BY '\n'
STORED AS TEXTFILE;

---Now downsample and store in this table

INSERT OVERWRITE TABLE criteo.criteo_train_downsample_1perc SELECT * FROM criteo.criteo_train WHERE RAND() <= 0.01;
```

To má za důsledek:

```output
Time taken: 12.22 seconds
Time taken: 298.98 seconds
```

Ukázkový skript [&#95;podregistr&#95;criteo&#95;převzorkovat&#95;test&#95;den&#95;22&#95;DataSet. HQL](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_downsample_test_day_22_dataset.hql) provede pro testovací data, den \_ 22:

```hiveql
--- Now for test data (day_22)

CREATE TABLE criteo.criteo_test_day_22_downsample_1perc (
col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
LINES TERMINATED BY '\n'
STORED AS TEXTFILE;

INSERT OVERWRITE TABLE criteo.criteo_test_day_22_downsample_1perc SELECT * FROM criteo.criteo_test_day_22 WHERE RAND() <= 0.01;
```

To má za důsledek:

```output
Time taken: 1.22 seconds
Time taken: 317.66 seconds
```

Nakonec [ukázkový skript&#95;podregistr&#95;criteo&#95;převzorkovat&#95;test&#95;den&#95;23&#95;DataSet. HQL](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_downsample_test_day_23_dataset.hql) provede pro testovací data, den \_ 23:

```hiveql
--- Finally test data day_23
CREATE TABLE criteo.criteo_test_day_23_downsample_1perc (
col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 srical feature; tring)
ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
LINES TERMINATED BY '\n'
STORED AS TEXTFILE;

INSERT OVERWRITE TABLE criteo.criteo_test_day_23_downsample_1perc SELECT * FROM criteo.criteo_test_day_23 WHERE RAND() <= 0.01;
```

To má za důsledek:

```output
Time taken: 1.86 seconds
Time taken: 300.02 seconds
```

Díky tomu jste připraveni k použití našich ukázek výukových a testovacích datových sad pro vytváření modelů v Azure Machine Learning.

Před přechodem na Azure Machine Learning, která se týká tabulky Count, existuje finální důležitá součást. V následující podsekci je tabulka Count podrobněji popsána.

## <a name="a-brief-discussion-on-the-count-table"></a><a name="count"></a> Stručná diskuze o tabulce Count
Jak jste viděli, několik proměnných kategorií má vysokou dimenzionální hodnotu. V tomto návodu se jedná o výkonnou techniku [s názvem učení se](/archive/blogs/machinelearning/big-learning-made-easy-with-counts) zakódováním těchto proměnných efektivním a robustním způsobem. Další informace o této technice najdete v zadaném odkazu.

>[!NOTE]
>V tomto návodu se zaměřuje na použití tabulek Count pro vytváření kompaktní reprezentace vysoce dimenzionálních funkcí kategorií. Toto není jediný způsob, jak zakódovat funkce kategorií; Další informace o jiných technikách, které mohou mít zájemci, je [použití algoritmu hash](https://en.wikipedia.org/wiki/Feature_hashing) [One-Hot-Encoding](https://en.wikipedia.org/wiki/One-hot) a funkcí.
>

K sestavování tabulek Count na základě počtu dat použijte data ve složce RAW/Count. V části modelování se uživatelům zobrazují informace o tom, jak sestavovat tyto tabulky Count pro funkce kategorií od začátku, nebo můžete použít předem sestavenou tabulku Count pro své průzkumy. V tom, co následuje, když se říká "předem sestavenými tabulkami Count", znamená to, že používáme tabulky Count, které byly k dispozici. Podrobné pokyny k přístupu k těmto tabulkám najdete v další části.

## <a name="build-a-model-with-azure-machine-learning"></a><a name="aml"></a> Vytvoření modelu pomocí Azure Machine Learning
Náš proces sestavení modelu v Azure Machine Learning provede následující kroky:

1. [Získat data z tabulek podregistru do Azure Machine Learning](#step1)
2. [Vytvořte experiment: Vyčistěte data a nastavte ji jako funkci s tabulkami Count.](#step2)
3. [Sestavování, výuka a určení skóre modelu](#step3)
4. [Vyhodnocení modelu](#step4)
5. [Publikování modelu jako webové služby](#step5)

Nyní jste připraveni sestavit modely v Azure Machine Learning Studiu. Naše ukázková data se ukládají jako tabulky podregistru v clusteru. Pro čtení těchto dat použijte modul Azure Machine Learning **importovat data** . Přihlašovací údaje pro přístup k účtu úložiště tohoto clusteru jsou k dispozici v následujících ohledech.

### <a name="step-1-get-data-from-hive-tables-into-azure-machine-learning-using-the-import-data-module-and-select-it-for-a-machine-learning-experiment"></a><a name="step1"></a> Krok 1: získání dat z tabulek podregistru do Azure Machine Learning pomocí modulu import dat a výběr pro experimentování ve službě Machine Learning
Začněte tím, že vyberete **+ Nový**  ->  **experiment**  ->  **prázdný experiment**. Pak z **vyhledávacího** pole v levém horním rohu vyhledejte "Import dat". Přetažením modulu pro **Import dat** na plátno experimentu (střední část obrazovky) použijte modul pro přístup k datům.

**Importovaná data** vypadají při získávání dat z tabulky podregistru:

![Import dat načte data](./media/hive-criteo-walkthrough/i3zRaoj.png)

V případě modulu **Import dat** jsou hodnoty parametrů, které jsou k dispozici na obrázku, pouze příklady řazení hodnot, které je třeba zadat. Zde jsou některé obecné pokyny k vyplnění sady parametrů pro modul **Import dat** .

1. Zvolit dotaz na podregistr pro **zdroj dat**
2. V poli **dotaz do databáze podregistru** je jednoduchý výběr * z <\_ název vaší databáze \_ . \_ název tabulky \_ je>-je dostatečně.
3. **Identifikátor URI serveru Hcatalog** : Pokud je váš cluster "ABC", je to jednoduše: https: \/ /ABC.azurehdinsight.NET
4. **Název uživatelského účtu Hadoop** : uživatelské jméno, které jste zvolili při vystavení clusteru v době od jejího provozu. (Nejedná se o uživatelské jméno vzdáleného přístupu.)
5. **Heslo uživatelského účtu Hadoop** : heslo pro uživatelské jméno, které jste zvolili při vystavení clusteru v době od jejího provozu. (Nejedná se o heslo vzdáleného přístupu.)
6. **Umístění výstupních dat** : vyberte Azure.
7. **Název účtu Azure Storage** : účet úložiště, který je přidružený ke clusteru.
8. **Klíč účtu Azure Storage** : klíč účtu úložiště přidruženého ke clusteru.
9. **Název kontejneru Azure** : Pokud je název clusteru "ABC", je to jednoduše "ABC", obvykle.

Jakmile **data importu** dokončí načítání dat (uvidíte zelenou značku v modulu), uložte tato data jako datovou sadu (s názvem podle vlastního výběru). Co vypadá takto:

![Import dat uložení dat](./media/hive-criteo-walkthrough/oxM73Np.png)

Klikněte pravým tlačítkem na výstupní port modulu **Import dat** . Tím se odhalí možnost **Uložit jako datová sada** a možnost **vizualizace** . Pokud kliknete na možnost **vizualizace** , zobrazí se 100 řádků dat spolu s pravým panelem, který je užitečný pro některé souhrnné statistiky. Pokud chcete data uložit, stačí vybrat **Uložit jako datovou sadu** a postupovat podle pokynů.

Pokud chcete vybrat uloženou datovou sadu pro použití v experimentu machine learningu, najděte datové sady pomocí **vyhledávacího** pole uvedeného na následujícím obrázku. Pak jednoduše zadejte název, ke kterému jste datovou sadu přiřadili, a přetáhněte datovou sadu na hlavní panel. Když ho přetáhnete na hlavní panel, vybere ho pro použití v modelování machine learningu.

![Přetáhněte datovou sadu na hlavní panel.](./media/hive-criteo-walkthrough/cl5tpGw.png)

> [!NOTE]
> Udělejte to jak pro vlak, tak pro testovací datové sady. Nezapomeňte také použít název databáze a názvy tabulek, které jste zadali pro tento účel. Hodnoty použité na obrázku jsou určené pouze pro ilustraci. * *
>
>

### <a name="step-2-create-an-experiment-in-azure-machine-learning-to-predict-clicks--no-clicks"></a><a name="step2"></a> Krok 2: vytvoření experimentu v Azure Machine Learning k předpovědi kliknutí/bez kliknutí
Náš Azure Machine Learning Studio (klasický) experiment vypadá takto:

![Machine Learning experiment](./media/hive-criteo-walkthrough/xRpVfrY.png)

Nyní prověřte klíčové komponenty tohoto experimentu. Nejdřív přetáhněte náš uložený vlak a test datových sad na náš plátno experimentu.

#### <a name="clean-missing-data"></a>Vyčištění chybějících dat
Modul **Vyčištění chybějících dat** podle názvu navrhuje: čistí chybějící data způsobem, který může být zadán uživatelem. Podívejte se do tohoto modulu a podívejte se na toto:

![Vyčistit chybějící data](./media/hive-criteo-walkthrough/0ycXod6.png)

Zde se můžete rozhodnout nahradit všechny chybějící hodnoty hodnotou 0. K dispozici jsou také další možnosti, které lze zobrazit v rozevíracích seznamech v modulu.

#### <a name="feature-engineering-on-the-data"></a>Příprava funkcí na data
Pro některé kategorií funkce velkých datových sad můžou existovat miliony jedinečných hodnot. Použití metod Naive, jako je například kódování One-Hot pro reprezentaci těchto vysoce dimenzionálních kategorií funkcí, je zcela neproveditelné. Tento návod ukazuje, jak používat funkce Count pomocí integrovaných Azure Machine Learning modulů pro generování kompaktní reprezentace těchto hodnot s vysokou dimenzionální kategoriíí. Konečný výsledek je menší velikost modelu, rychlejší školicí časy a metriky výkonu, které jsou srovnatelné s používáním jiných technik.

##### <a name="building-counting-transforms"></a>Vytváření transformací inventury
K sestavení funkcí Count použijte modul pro **inventarizaci sestavení** , který je k dispozici v Azure Machine Learning. Modul vypadá takto:

![Sestavování – vlastnosti modulu transformace – ](./media/hive-criteo-walkthrough/e0eqKtZ.png)
 ![ modul pro počítání transformací sestavení](./media/hive-criteo-walkthrough/OdDN0vw.png)

> [!IMPORTANT]
> Do pole **počet sloupců** zadejte sloupce, u kterých chcete provést počty. Obvykle jsou to (jak bylo zmíněno) vysoce dimenzionální kategorií sloupce. Mějte na paměti, že datová sada Criteo má 26 kategorií sloupců: od Col15 do Col40. Zde se počet všech z nich uvede a sdělte jejich indexy (od 15 do 40 oddělených čárkami, jak je znázorněno).
>

Pokud chcete modul používat v režimu MapReduce (který je vhodný pro velké datové sady), potřebujete přístup k clusteru HDInsight Hadoop (ten, který se používá pro průzkum funkcí, se dá taky pro tento účel znovu použít) a jeho přihlašovací údaje. Předchozí obrázky znázorňují, jaké hodnoty vypadají jako hodnoty (nahraďte hodnoty, které jsou k dispozici pro ilustraci, u těch, které jsou důležité pro vlastní případ použití).

![Parametry modulu](./media/hive-criteo-walkthrough/05IqySf.png)

Předchozí obrázek ukazuje, jak zadat vstupní umístění objektu BLOB. Toto umístění obsahuje data rezervovaná pro vytváření tabulek Count.

Až se tento modul dokončí, uložte transformaci později tak, že kliknete pravým tlačítkem na modul a vyberete možnost **Uložit jako transformaci** :

![Možnost Uložit jako transformaci](./media/hive-criteo-walkthrough/IcVgvHR.png)

V naší architektuře experimentu uvedené výše má datová sada "ytransform2" přesně odpovídající transformaci uloženého počtu. Ve zbývající části tohoto experimentu předpokládáme, že čtenář použil modul pro **inventarizaci sestavení** pro některá data, aby vygeneroval počty, a pak je může použít k vygenerování funkcí Count v vlakových a testovacích datových sadách.

##### <a name="choosing-what-count-features-to-include-as-part-of-the-train-and-test-datasets"></a>Výběr funkcí, které mají být zahrnuty jako součást vlakových a testovacích datových sad
Jakmile je změna počtu připravená, uživatel může vybrat, které funkce se mají zahrnout do svých vlakových a testovacích datových sad pomocí modulu **parametry tabulky pro úpravu počtu** . V případě úplnosti se tady zobrazí tento modul. V zájmu jednoduchosti je ale ve skutečnosti nepoužíváme v našem experimentu.

![Změnit parametry tabulky Count](./media/hive-criteo-walkthrough/PfCHkVg.png)

V takovém případě, jak je vidět, je třeba použít protokol lichá a sloupec Back off je ignorován. Můžete také nastavit parametry, jako je například prahová hodnota paměti, počet pseudo-předchozích příkladů, které se mají přidat k vyhlazení a zda se má použít jakýkoliv Laplacian šum. Všechny tyto funkce jsou pokročilé a je třeba poznamenat, že výchozí hodnoty jsou dobrým výchozím bodem pro uživatele, kteří jsou pro tento typ generování funkcí noví.

##### <a name="data-transformation-before-generating-the-count-features"></a>Transformace dat před generováním funkcí Count
Teď je zaměření na důležité místo pro transformaci našeho testu a testovacích dat před skutečným vygenerováním funkcí Count. Předtím, než se pro naše data použije transformace Count, se používají dva moduly pro **spuštění skriptu jazyka R** .

![Spustit moduly skriptů R](./media/hive-criteo-walkthrough/aF59wbc.png)

Toto je první skript jazyka R:

![První skript R](./media/hive-criteo-walkthrough/3hkIoMx.png)

Tento skript R přejmenuje naše sloupce na názvy "Sloupec1" na "Col40". Důvodem je, že transformace Count očekává názvy tohoto formátu.

Druhý skript R vyrovnává distribuci mezi kladnou a zápornou třídou (třídy 1 a 0 v uvedeném pořadí) po vyvzorkování negativní třídy. Tady je skript R, který ukazuje, jak to provést:

![Druhý skript R](./media/hive-criteo-walkthrough/91wvcwN.png)

V tomto jednoduchém skriptu jazyka R \_ \_ se k nastavení množství vyvážení mezi kladnou a zápornou třídou používá poměr záporného hodnoty POS. To je důležité udělat, protože vylepšení nerovnováhy tříd má obvykle vliv na výkon při klasifikacích, kde je rozdělení třídy nakloněné (v tomto případě je to v tomto případě možné, že máte 3,3% pozitivní třídy a 96,7% negativní třídu).

##### <a name="applying-the-count-transformation-on-our-data"></a>Použití transformace Count u našich dat
Nakonec můžete použít modul **použít transformaci a použít transformované** transformace v našich datových sadách pro vlak a test. Tento modul přebírá uloženou transformaci počtu jako jeden vstup a vlak nebo test datových sad jako druhý vstup a vrací data s funkcemi Count. Zobrazuje se tady:

![Použít transformační modul](./media/hive-criteo-walkthrough/xnQvsYf.png)

##### <a name="an-excerpt-of-what-the-count-features-look-like"></a>Ukázka toho, co funkce Count vypadá
To vám dá pokyn, abyste viděli, jak se funkce Count v našem případě podobají. Toto je výňatek z tohoto příkladu:

![Funkce Count](./media/hive-criteo-walkthrough/FO1nNfw.png)

V tomto výňatku se dozvíte, že pro sloupce, které se počítají, získáte počty a lichá protokolů kromě jakýchkoli relevantních odstartů.

Nyní jste připraveni sestavit Azure Machine Learning model pomocí těchto transformovaných datových sad. V další části se dozvíte, jak to lze provést.

### <a name="step-3-build-train-and-score-the-model"></a><a name="step3"></a> Krok 3: sestavování, výuka a určení skóre modelu

#### <a name="choice-of-learner"></a>Výběr učení
Nejdřív je potřeba vybrat si ho. Využijte ke zvýšení rozhodovacího stromu se dvěma třídami svůj náš učí. Tady jsou výchozí možnosti pro tento učí:

![Two-Class se zvýšily parametry rozhodovacího stromu.](./media/hive-criteo-walkthrough/bH3ST2z.png)

Pro experiment vyberte výchozí hodnoty. Výchozí hodnoty jsou smysluplné a dobrý způsob, jak získat rychlé základní hodnoty pro výkon. Pokud se rozhodnete, že budete mít směrný plán, můžete vylepšit výkon pomocí úklidu parametrů.

#### <a name="train-the-model"></a>Trénování modelu
Pro školení stačí vyvolat modul **vlakového modelu** . Do těchto dvou vstupů patří Two-Class posílený seznámení s rozhodovacím stromem a naši datovou sadu vlaků. Toto je znázorněno zde:

![Modul vlakového modelu](./media/hive-criteo-walkthrough/2bZDZTy.png)

#### <a name="score-the-model"></a>Určení skóre modelu
Jakmile budete mít školený model, jste připraveni na skóre pro testovací datovou sadu a k vyhodnocení jeho výkonu. K tomu použijte modul **skóre modelu** , který je znázorněný na následujícím obrázku, společně s modulem **vyhodnocení modelu** :

![Modul Určení skóre modelu](./media/hive-criteo-walkthrough/fydcv6u.png)

### <a name="step-4-evaluate-the-model"></a><a name="step4"></a> Krok 4: vyhodnocení modelu
Nakonec byste měli analyzovat výkon modelu. Pro dva problémy klasifikace třídy (binární) obvykle je vhodná míra AUC. Chcete-li vizualizovat tuto křivku, připojte modul určení **skóre** modelu k modulu **vyhodnocení modelu** . Kliknutí na **vizualizaci** v modulu **vyhodnocení modelu** poskytuje obrázek podobný následujícímu:

![Zhodnotit model BDT modulu](./media/hive-criteo-walkthrough/0Tl0cdg.png)

V binárních problémech klasifikace (nebo dvou tříd) je dobrým měřítkem přesnosti předpovědi plocha pod křivkou (AUC). V následující části jsou uvedeny výsledky pomocí tohoto modelu v naší datové sadě testů. Klikněte pravým tlačítkem na výstupní port modulu **vyhodnocení modelu** a pak **Vizualizujte**.

![Vizualizace modulu vyhodnocení modelu](./media/hive-criteo-walkthrough/IRfc7fH.png)

### <a name="step-5-publish-the-model-as-a-web-service"></a><a name="step5"></a> Krok 5: publikování modelu jako webové služby
Možnost publikovat Azure Machine Learning model jako webové služby s minimálním počtem Fuss je cennou funkcí pro zajištění jejich široké dostupnosti. Až to bude hotové, kdokoli může prostřednictvím webové služby volat vstupní data, která potřebují předpovědi pro, a webová služba použije model k vrácení těchto předpovědi.

Nejdřív uložte náš vycvičený model jako školený objekt modelu tak, že kliknete pravým tlačítkem na modul **vlakového modelu** a použijete možnost **Uložit jako trained model** .

Dále vytvořte vstupní a výstupní porty pro naši webovou službu:

* vstupní port přebírá data ve stejné podobě jako data, která potřebujete předpovědi pro
* výstupní port vrátí popisky s skóre a související pravděpodobnost.

#### <a name="select-a-few-rows-of-data-for-the-input-port"></a>Vyberte několik řádků dat pro vstupní port.
Je vhodné použít modul **použít transformaci SQL** k výběru pouhých 10 řádků, které budou sloužit jako data vstupních portů. Vyberte jenom tyto řádky dat pro náš vstupní port pomocí dotazu SQL zobrazeného tady:

![Vstupní data portu](./media/hive-criteo-walkthrough/XqVtSxu.png)

#### <a name="web-service"></a>Webová služba
Nyní jste připraveni spustit malý experiment, který lze použít k publikování webové služby.

#### <a name="generate-input-data-for-webservice"></a>Generovat vstupní data pro WebService
Jako zeroth krok, protože tabulka Count je rozsáhlá, pár řádků testovacích dat a generování výstupních dat s využitím funkcí Count. Tento výstup může sloužit jako formát vstupních dat pro naši webovou službu, jak je znázorněno zde:

![Vytvoření vstupních dat BDT](./media/hive-criteo-walkthrough/OEJMmst.png)

> [!NOTE]
> Pro formát vstupních dat použijte výstup modulu **Count Featurizer** . Až se tento experiment dokončí, uložte výstup z modulu **Count Featurizer** jako datovou sadu. Tato datová sada se používá pro vstupní data ve webové službě.
>
>

#### <a name="scoring-experiment-for-publishing-webservice"></a>Experiment bodování pro publikování webové služby
Základní strukturou je nejprve modul **bodového modelu** , který přijímá náš objekt trained model a několik řádků vstupních dat, která byla vygenerována v předchozích krocích pomocí modulu **Count Featurizer** . Pomocí příkazu Vybrat sloupce v datové sadě můžete vyznačit popisky s skóre a pravděpodobnosti skóre.

![Výběr sloupců v datové sadě](./media/hive-criteo-walkthrough/kRHrIbe.png)

Všimněte si, jak se dá modul **Výběr sloupců v datové sadě** použít pro filtrování dat filtrování z datové sady. Obsah je zobrazen zde:

![Filtrování pomocí modulu výběr sloupců v datové sadě](./media/hive-criteo-walkthrough/oVUJC9K.png)

Pokud chcete získat modré vstupní a výstupní porty, stačí kliknout na **připravit službu webwebservice** v pravém dolním rohu. Spuštění tohoto experimentu nám taky umožňuje publikovat webovou službu: klikněte na ikonu **publikovat webovou službu** v pravém dolním rohu, jak je vidět tady:

![Publikovat webovou službu](./media/hive-criteo-walkthrough/WO0nens.png)

Po publikování webové služby se přesměruje na stránku, která vypadá takto:

![Řídicí panel webové služby](./media/hive-criteo-walkthrough/YKzxAA5.png)

Všimněte si dvou odkazů pro WebService na levé straně:

* Služba **Request/response** (nebo záznamy o prostředcích) je určena pro jednotlivé předpovědi a je to, co se v této dílně využilo.
* Služba Batch **EXECUTION** Service (BES) se používá pro službu Batch předpovědi a vyžaduje, aby se ve službě Azure Blob Storage mohla umístit vstupní data použitá k vytvoření předpovědi.

Kliknutím na odkaz **požadavek/odpověď** přejdete na stránku, která nám poskytuje předem zakonzervovaný kód v jazycích C#, Python a R. Tento kód se dá pohodlně použít k volání webové služby. Klíč rozhraní API na této stránce se musí použít k ověřování.

Je vhodné zkopírovat tento kód Pythonu do nové buňky v IPython poznámkovém bloku.

Tady je segment kódu Pythonu se správným klíčem rozhraní API.

![Kód Pythonu](./media/hive-criteo-walkthrough/f8N4L4g.png)

Výchozí klíč rozhraní API byl nahrazen klíčem rozhraní API naší webové služby. Kliknutím na **Spustit** v této buňce v IPython poznámkovém bloku vyplníte následující odpověď:

![IPython odpověď](./media/hive-criteo-walkthrough/KSxmia2.png)

Pro dva příklady testů, které se týkají v rozhraní JSON Script v Pythonu, získáte odpovědi ve formě "popisků s skóre, pravděpodobnosti skóre". V tomto případě byly zvoleny výchozí hodnoty, které zakonzervovaný kód poskytuje (0 pro všechny číselné sloupce a řetězec "hodnota" pro všechny sloupce kategorií).

V závěru náš Názorný postup ukazuje, jak zpracovat rozsáhlou datovou sadu pomocí Azure Machine Learning. Začali jste s terabajty dat, vytvářeli předpovědní model a nasadili jste ho jako webovou službu v cloudu.