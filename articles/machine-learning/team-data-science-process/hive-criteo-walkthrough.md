---
title: Použití Azure HDInsight Hadoop Cluster na 1 TB datová sada – vědecké zpracování týmových dat
description: Použití vědeckého zpracování týmových dat začátku do konce scénář použití clusteru HDInsight Hadoop pro sestavování a nasazování modelů pomocí velkou veřejně dostupnou datovou sadu (1 TB)
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.component: team-data-science-process
ms.topic: article
ms.date: 11/29/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 2c2c06a222f3ac949f8e8e6b4aed1b00c0593b6d
ms.sourcegitcommit: 58dc0d48ab4403eb64201ff231af3ddfa8412331
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/26/2019
ms.locfileid: "55080221"
---
# <a name="the-team-data-science-process-in-action---using-an-azure-hdinsight-hadoop-cluster-on-a-1-tb-dataset"></a>Vědecké zpracování týmových dat v akci – pomocí clusteru Azure HDInsight Hadoop na 1 TB datové sady

Tento návod ukazuje, jak používat vědecké zpracování týmových dat ve scénáři začátku do konce se [clusteru Azure HDInsight Hadoop](https://azure.microsoft.com/services/hdinsight/) k ukládání, zkoumat, funkce technik a ukázková data z jednoho z veřejně dostupné dolů[ Criteo](http://labs.criteo.com/downloads/download-terabyte-click-logs/) datové sady. Využívá Azure Machine Learning a začít vytvářet binární klasifikační model pro tato data. Také ukazuje, jak k publikování některého z těchto modelů jako webové služby.

Je také možné použít k provádění úloh, které jsou uvedené v tomto názorném postupu IPython notebook. Uživatelé, kteří chtěli vyzkoušet tohoto přístupu by se měli obrátit [Criteo návod používání Hive ODBC připojení](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-hive-walkthrough-criteo.ipynb) tématu.

## <a name="dataset"></a>Popis Criteo datové sady
Criteo dat je predikce klikněte na datovou sadu, přibližně 370 GB souborů gzip komprimované TSV (~1.3TB nekomprimovaný), zahrnující více než 4.3 miliard záznamů. Je převzata z 24 dní klikněte na data k dispozici ve [Criteo](http://labs.criteo.com/downloads/download-terabyte-click-logs/). Pro usnadnění práce odborníci přes data byla data dostupná pro nás můžete experimentovat s rozbaleny.

Každý záznam v této datové sadě obsahuje 40 sloupců:

* první sloupec je sloupec popisek, který označuje, zda uživatel klikne **přidat** (hodnota 1) nebo není klikněte na jednu (hodnotou 0)
* dále 13 sloupce obsahují číselné hodnoty a a
* posledních 26 jsou zařazené do kategorií sloupce

Sloupce, které jsou anonymní a použijte řadu Výčtový názvy: "Sloupec1" (pro popisek sloupce) do "Col40" (pro poslední sloupec zařazené do kategorií).

Tady je výpisem prvních 20 sloupce vyjádření dva (řádky) z této sestavy:

    Col1    Col2    Col3    Col4    Col5    Col6    Col7    Col8    Col9    Col10    Col11    Col12    Col13    Col14    Col15            Col16            Col17            Col18            Col19        Col20

    0       40      42      2       54      3       0       0       2       16      0       1       4448    4       1acfe1ee        1b2ff61f        2e8b2631        6faef306        c6fc10d3    6fcd6dcb
    0               24              27      5               0       2       1               3       10064           9a8cb066        7a06385f        417e6103        2170fc56        acf676aa    6fcd6dcb

Jsou chybějící hodnoty zařazené do kategorií a číselné sloupce v této datové sadě. Je popsána jednoduchý způsob zpracování chybějících hodnot. Další podrobnosti dat jsou probírány při ukládání do tabulek Hive.

**Definice:** *Interaktivní kurz (KONT):* Toto je procento kliknutí v datech. V této datové sadě Criteo PEV.cenu je přibližně % 3.3 nebo 0.033.

## <a name="mltasks"></a>Příklady úloh predikcí
V tomto názorném postupu se tak vyřeší dva ukázkové předpovědi problémy:

1. **Binární klasifikace**: Předpovídá, jestli uživatel kliknutí na add:

   * Třída 0: Žádné kliknutím
   * Třídy 1: Klikněte na
2. **Regrese**: Předpovídá pravděpodobnost ad kliknutím uživatelské funkce.

## <a name="setup"></a>Nastavte si HDInsight Hadoop cluster pro datové vědy
**Poznámka:** Obvykle se jedná **správce** úloh.

Nastavení prostředí Azure pro datové vědy pro vytváření řešení prediktivní analýzy s využitím clusterů HDInsight ve třech krocích:

1. [Vytvoření účtu úložiště](../../storage/common/storage-quickstart-create-account.md): Tento účet úložiště se používá k ukládání dat ve službě Azure Blob Storage. Data používaná v clusterech HDInsight je zde uloženy.
2. [Přizpůsobení clusterů Azure HDInsight Hadoop pro vědecké zkoumání dat](customize-hadoop-cluster.md): Tento krok vytvoří cluster s 64bitovým kompilátorem Anaconda Python 2.7 nainstalované na všech uzlech Azure HDInsight Hadoop. Existují dva důležité kroky (popsané v tomto tématu) k dokončení při vlastním nastavení clusteru HDInsight.

   * Je třeba propojit účet úložiště vytvořený v kroku 1 s vaším clusterem HDInsight při jeho vytvoření. Tento účet úložiště se používá pro přístup k datům, které mohou být zpracovány v rámci clusteru.
   * Po vytvoření je nutné povolit vzdálený přístup k hlavnímu uzlu clusteru. Pamatovat přihlašovací údaje vzdáleného přístupu, které zadáte tady (liší od zadaného clusteru při jeho vytváření): budete potřebovat následující postup.
3. [Vytvoření pracovního prostoru Azure Machine Learning studio](../studio/create-workspace.md): Tento pracovní prostor Azure Machine Learning je určená k vytváření modelů strojového učení po počáteční zkoumání a dolů vzorkování v clusteru HDInsight.

## <a name="getdata"></a>Získání a využívat data z veřejné zdroje
[Criteo](http://labs.criteo.com/downloads/download-terabyte-click-logs/) datová sada je přístupná, klikněte na odkaz, přijetí podmínek použití a poskytnutí názvu. Jak to vypadá snímku je znázorněna zde:

![Přijměte podmínky Criteo](./media/hive-criteo-walkthrough/hLxfI2E.png)

Klikněte na tlačítko **pokračovat ke stažení** Další informace o datové sady a jeho dostupnosti.

Data se nachází ve veřejném [úložiště objektů blob v Azure](../../storage/blobs/storage-dotnet-how-to-use-blobs.md) umístění: wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/. "wasb" odkazuje na umístění úložiště objektů Blob v Azure.

1. Data v této veřejné blob storage se skládá z tři podsložky rozzipovaný data.

   1. Podsložky *raw nebo count/* obsahuje první 21 dnů dat – denně\_00 den\_20
   2. Podsložky *nezpracované/trénování/* se skládá z jednoho dne dat, den\_21
   3. Podsložky *nezpracované/testovací/* se skládá ze dvou dnů dat, den\_22 a den\_23
2. Pro ty, kteří chtějí s daty nezpracovaná gzip, jsou také k dispozici ve složce hlavní *nezpracované /* jako day_NN.gz, kde NN přejde od 00 do 23.

Alternativním přístupem k přístupu, prozkoumejte a modelu, který tato data, která nevyžaduje žádné místní soubory ke stažení je vysvětlen později v tomto názorném postupu při vytváření tabulek Hive.

## <a name="login"></a>Přihlaste se k hlavnímu uzlu clusteru
Chcete-li přihlásit se k hlavnímu uzlu clusteru, použijte [webu Azure portal](https://ms.portal.azure.com) k vyhledání clusteru. Klikněte na ikonu Slon HDInsight na levé straně a potom dvakrát klikněte na název vašeho clusteru. Přejděte **konfigurace** kartu, dvakrát klikněte na ikonu připojit v dolní části stránky a zadejte svoje přihlašovací údaje vzdáleného přístupu po zobrazení výzvy. Tím přejdete k hlavnímu uzlu clusteru.

Zde je, jak vypadá typické prvním přihlášení k hlavnímu uzlu clusteru:

![Přihlaste se ke clusteru](./media/hive-criteo-walkthrough/Yys9Vvm.png)

Na levé straně je "Hadoop příkazového řádku", což je naše Centrem pro zkoumání dat. Všimněte si, že dvě užitečné adresy URL – "Hadoop Yarn stavu" a "Hadoop název uzlu". Adresa URL yarn stavu zobrazuje průběh úlohy a adresu URL uzlu název obsahuje údaje o konfiguraci clusteru.

Nyní jsou nastaveny a jste připravení začít první část návodu: zkoumání dat pomocí Hivu a příprava dat pro Azure Machine Learning.

## <a name="hive-db-tables"></a> Vytvořit databázi Hive a tabulky
K vytvoření tabulky Hive pro datovou sadu Criteo, otevřete ***příkazového řádku Hadoopu*** v klientských počítačích k hlavnímu uzlu a zadejte adresář Hive zadáním příkazu

    cd %hive_home%\bin

> [!NOTE]
> Spustit všechny příkazy Hive v tomto názorném postupu z koše Hive / directory řádku. To se postará o případné problémy cestu automaticky. Můžete využít podmínky "Hive directory řádku", "Hive bin / directory řádku" a "příkazového řádku Hadoopu" Zaměnitelně.
>
> [!NOTE]
> Ke spuštění libovolného dotazu Hive, jeden vždy použijte následující příkazy:
>
>

        cd %hive_home%\bin
        hive

Jakmile se objeví Hive REPL s "hive >"podepsat, stačí vyjmout a vložit dotaz k jeho provedení.

Následující kód vytvoří databázi "criteo" a poté vygeneruje 4 tabulky:

* *tabulku pro generování počty* založená na den dnů\_00 den\_20,
* *tabulky pro použití jako datovou sadu trénování* založená na den\_21, a
* dvě *tabulky pro použití jako datových sad testů* založená na den\_22 a den\_23 v uvedeném pořadí.

Testovací datové rozdělte do dvou různých tabulek, protože je jeden volný den. Cílem je určit, pokud model lze zjistit rozdíly mezi svátku a mimo svátků kurz, klikněte na tlačítko procházení.

Skript [ukázka&#95;hive&#95;vytvořit&#95;criteo&#95;databáze&#95;a&#95;tables.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_create_criteo_database_and_tables.hql) se tady zobrazí pro usnadnění práce:

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

Tyto tabulky jsou externí, takže můžete jednoduše přejděte na jejich umístění Azure Blob Storage (wasb).

**Ke spuštění dotazu Hive v ANY dvěma způsoby:**

1. **Použití Hive REPL příkazového řádku**: První je vydat příkaz "hive" a zkopírujte a vložte dotaz na podregistr REPL příkazového řádku. Provedete to udělat:

        cd %hive_home%\bin
        hive

     Teď v REPL příkazového řádku, vyjmutí a vložení dotazu jej provede.
2. **Ukládání dotazů do souboru a provádění příkazu**: Druhým je uložit do souboru .hql dotazy ([ukázka&#95;hive&#95;vytvořit&#95;criteo&#95;databáze&#95;a&#95;tables.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_create_criteo_database_and_tables.hql)) a potom vydat příkaz k provedení dotazu:

        hive -f C:\temp\sample_hive_create_criteo_database_and_tables.hql

### <a name="confirm-database-and-table-creation"></a>Ověřte vytvoření databáze a tabulky
Dále zkontrolujte vytvoření databáze pomocí následujícího příkazu z koše Hive / directory řádku:

        hive -e "show databases;"

Díky tomu:

        criteo
        default
        Time taken: 1.25 seconds, Fetched: 2 row(s)

Tím potvrdíte vytvoření nové databáze "criteo".

Pokud chcete zobrazit, jaké tabulky byly vytvořeny, jednoduše vydejte příkaz tady z koše Hive / directory řádku:

        hive -e "show tables in criteo;"

Pak by měl zobrazit následující výstup:

        criteo_count
        criteo_test_day_22
        criteo_test_day_23
        criteo_train
        Time taken: 1.437 seconds, Fetched: 4 row(s)

## <a name="exploration"></a> Zkoumání dat v Hivu
Teď jste připravení na provádění některých základních zkoumání v podregistru. Začněte tím, že počítání příklady vlaku a data tabulky testů.

### <a name="number-of-train-examples"></a>Řadu příkladů trénování
Obsah [ukázka&#95;hive&#95;počet&#95;trénování&#95;tabulky&#95;examples.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_count_train_table_examples.hql) se tady zobrazí:

        SELECT COUNT(*) FROM criteo.criteo_train;

To poskytuje:

        192215183
        Time taken: 264.154 seconds, Fetched: 1 row(s)

Můžete také jeden může také vydejte následující příkaz z koše Hive / directory řádku:

        hive -f C:\temp\sample_hive_count_criteo_train_table_examples.hql

### <a name="number-of-test-examples-in-the-two-test-datasets"></a>Řadu příkladů testu v obou datových sadách testů
Nyní počet příklady v obou datových sadách testů. Obsah [ukázka&#95;hive&#95;počet&#95;criteo&#95;testování&#95;den&#95;22&#95;tabulky&#95;examples.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_count_criteo_test_day_22_table_examples.hql) se nacházíte tady:

        SELECT COUNT(*) FROM criteo.criteo_test_day_22;

To poskytuje:

        189747893
        Time taken: 267.968 seconds, Fetched: 1 row(s)

Obvyklým způsobem, může také volání skriptu z koše Hive / directory řádku vydání příkazu:

        hive -f C:\temp\sample_hive_count_criteo_test_day_22_table_examples.hql

Nakonec zkontrolujte počet testů příklady v této datové sadě testů založené na den\_23.

K tomu příkaz je podobný tomu, který právě vidíte (odkazovat na [ukázka&#95;hive&#95;počet&#95;criteo&#95;testování&#95;den&#95;23&#95;examples.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_count_criteo_test_day_23_examples.hql)):

        SELECT COUNT(*) FROM criteo.criteo_test_day_23;

Díky tomu:

        178274637
        Time taken: 253.089 seconds, Fetched: 1 row(s)

### <a name="label-distribution-in-the-train-dataset"></a>Distribuce označení v datové sadě trénování
Distribuce označení v datové sadě trénování je relevantní. Tento údaj zobrazíte, zobrazit obsah [ukázka&#95;hive&#95;criteo&#95;popisek&#95;distribuce&#95;trénování&#95;table.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_label_distribution_train_table.hql):

        SELECT Col1, COUNT(*) AS CT FROM criteo.criteo_train GROUP BY Col1;

To poskytuje distribuce označení:

        1       6292903
        0       185922280
        Time taken: 459.435 seconds, Fetched: 2 row(s)

Všimněte si, že procento kladné popisky o 3.3 % (stejně jako u původní datové sady).

### <a name="histogram-distributions-of-some-numeric-variables-in-the-train-dataset"></a>Histogram distribuce některé číselné proměnné v datové sadě trénování
Můžete použít pro Hive nativní "histogram\_číselné" funkce a zjistěte, jak vypadá distribuce číselné proměnné. Tady je obsah [ukázka&#95;hive&#95;criteo&#95;histogram&#95;numeric.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_histogram_numeric.hql):

        SELECT CAST(hist.x as int) as bin_center, CAST(hist.y as bigint) as bin_height FROM
            (SELECT
            histogram_numeric(col2, 20) as col2_hist
            FROM
            criteo.criteo_train
            ) a
            LATERAL VIEW explode(col2_hist) exploded_table as hist;

To poskytuje následující:

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

LATERÁLNÍ zobrazení – rozbalit kombinace v Hive slouží SQL jako výstup namísto seznamu obvyklé. Všimněte si, že v této tabulce, na první sloupec odpovídá bin center a druhou pro frekvenci bin.

### <a name="approximate-percentiles-of-some-numeric-variables-in-the-train-dataset"></a>Přibližná percentily některé číselné proměnné v datové sadě trénování
Také zajímá číselné proměnné je výpočet přibližné percentil. Hive je nativní "percentilu\_přibližně" to udělá za nás. Obsah [ukázka&#95;hive&#95;criteo&#95;přibližné&#95;percentiles.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_approximate_percentiles.hql) jsou:

        SELECT MIN(Col2) AS Col2_min, PERCENTILE_APPROX(Col2, 0.1) AS Col2_01, PERCENTILE_APPROX(Col2, 0.3) AS Col2_03, PERCENTILE_APPROX(Col2, 0.5) AS Col2_median, PERCENTILE_APPROX(Col2, 0.8) AS Col2_08, MAX(Col2) AS Col2_max FROM criteo.criteo_train;

To poskytuje:

        1.0     2.1418600917169246      2.1418600917169246    6.21887086390288 27.53454893115633       65535.0
        Time taken: 564.953 seconds, Fetched: 1 row(s)

Distribuce percentily úzce souvisí s histogram distribuce jakékoli číselné proměnné obvykle.

### <a name="find-number-of-unique-values-for-some-categorical-columns-in-the-train-dataset"></a>Zjistit počet jedinečných hodnot pro některé zařazené do kategorií sloupců v datové sadě trénování
Pokračování zkoumání dat, najdete, pro některé sloupce zařazené do kategorií počtem jedinečných hodnot, které přebírají. Chcete-li to provést, zobrazit obsah [ukázka&#95;hive&#95;criteo&#95;jedinečný&#95;hodnoty&#95;categoricals.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_unique_values_categoricals.hql):

        SELECT COUNT(DISTINCT(Col15)) AS num_uniques FROM criteo.criteo_train;

To poskytuje:

        19011825
        Time taken: 448.116 seconds, Fetched: 1 row(s)

Všimněte si, že Col15 má jedinečné hodnoty 19M! Pomocí technik naivní jako "horkou jeden kódování" ke kódování těchto vysokým počtem rozměrů zařazené do kategorií proměnné není možné. Zejména, výkonné a robustní technika nazývá [učení s počty](http://blogs.technet.com/b/machinelearning/archive/2015/02/17/big-learning-made-easy-with-counts.aspx) pro efektivní řeší tento problém je vysvětlené a jsme vám ukázali.

Nakonec se podívejte na počet jedinečných hodnot pro některé zařazené do kategorií sloupce také. Obsah [ukázka&#95;hive&#95;criteo&#95;jedinečný&#95;hodnoty&#95;více&#95;categoricals.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_unique_values_multiple_categoricals.hql) jsou:

        SELECT COUNT(DISTINCT(Col16)), COUNT(DISTINCT(Col17)),
        COUNT(DISTINCT(Col18), COUNT(DISTINCT(Col19), COUNT(DISTINCT(Col20))
        FROM criteo.criteo_train;

To poskytuje:

        30935   15200   7349    20067   3
        Time taken: 1933.883 seconds, Fetched: 1 row(s)

Znovu Všimněte si, že kromě Col20, všechny ostatní sloupce mnoho jedinečné hodnoty.

### <a name="co-occurrence-counts-of-pairs-of-categorical-variables-in-the-train-dataset"></a>Párů zařazené do kategorií proměnných v datové sadě trénování se počítá společného výskytu

Počty společného výskytu párů zařazené do kategorií proměnných je také zajímavé. To se dá určit pomocí kódu v [ukázka&#95;hive&#95;criteo&#95;spárované&#95;zařazené do kategorií&#95;counts.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_paired_categorical_counts.hql):

        SELECT Col15, Col16, COUNT(*) AS paired_count FROM criteo.criteo_train GROUP BY Col15, Col16 ORDER BY paired_count DESC LIMIT 15;

Reverzní řadit počty podle jejich výskytu a hledat v horní části stránky 15 v tomto případě. Tento produkt nám nabízí:

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

## <a name="downsample"></a> Dolů ukázkové datové sady pro Azure Machine Learning
Prozkoumat datové sady a ukázal, jak lze provést zadáním průzkumu proměnných (včetně kombinace), dolů ukázkové sady dat tak, aby modelů ve službě Azure Machine Learning se dají. Vzpomínáte, který se zaměřuje problém: vzhledem k sadě atributů příkladu (hodnoty funkcí z Col2 - Col40), předpovědět, jestli Sloupec1 je 0 (žádné kliknutím) nebo 1 (kliknutí).

Dolů ukázkové datové sady trénování a testování na původní velikost % 1, pomocí funkce RAND() nativní pro Hive. Další skriptu [ukázka&#95;hive&#95;criteo&#95;převzorkovat&#95;trénování&#95;dataset.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_downsample_train_dataset.hql) to dělá pro trénování datovou sadu:

        CREATE TABLE criteo.criteo_train_downsample_1perc (
        col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
        LINES TERMINATED BY '\n'
        STORED AS TEXTFILE;

        ---Now downsample and store in this table

        INSERT OVERWRITE TABLE criteo.criteo_train_downsample_1perc SELECT * FROM criteo.criteo_train WHERE RAND() <= 0.01;

To poskytuje:

        Time taken: 12.22 seconds
        Time taken: 298.98 seconds

Skript [ukázka&#95;hive&#95;criteo&#95;převzorkovat&#95;testování&#95;den&#95;22&#95;dataset.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_downsample_test_day_22_dataset.hql) to udělá za testovacích dat, den\_22:

        --- Now for test data (day_22)

        CREATE TABLE criteo.criteo_test_day_22_downsample_1perc (
        col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
        LINES TERMINATED BY '\n'
        STORED AS TEXTFILE;

        INSERT OVERWRITE TABLE criteo.criteo_test_day_22_downsample_1perc SELECT * FROM criteo.criteo_test_day_22 WHERE RAND() <= 0.01;

To poskytuje:

        Time taken: 1.22 seconds
        Time taken: 317.66 seconds


Nakonec skript [ukázka&#95;hive&#95;criteo&#95;převzorkovat&#95;testování&#95;den&#95;23&#95;dataset.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_downsample_test_day_23_dataset.hql) to udělá za testovacích dat, den\_23:

        --- Finally test data day_23
        CREATE TABLE criteo.criteo_test_day_23_downsample_1perc (
        col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 srical feature; tring)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
        LINES TERMINATED BY '\n'
        STORED AS TEXTFILE;

        INSERT OVERWRITE TABLE criteo.criteo_test_day_23_downsample_1perc SELECT * FROM criteo.criteo_test_day_23 WHERE RAND() <= 0.01;

To poskytuje:

        Time taken: 1.86 seconds
        Time taken: 300.02 seconds

Díky tomu budete chtít použít naše dolů vzorky trénování a testování datových sad pro vytváření modelů ve službě Azure Machine Learning.

Před přechodem na Azure Machine Learning, která se týká tabulky count není konečný důležitou součástí. V následující dílčí části tabulce počet je podrobněji popsána podrobněji.

## <a name="count"></a> Stručný popis v tabulce počet
Jak už jste viděli, několik kategorií proměnné mají velmi vysoké dimenzionalitu. V tomto návodu, volá účinnou techniku [učení s počty](http://blogs.technet.com/b/machinelearning/archive/2015/02/17/big-learning-made-easy-with-counts.aspx) ke kódování těchto proměnných v efektivní, se zobrazí robustní způsobem. Další informace o této technice je v odkazu.

[!NOTE]
>V tomto názorném postupu se zaměřuje na použití počet tabulek pro vytvoření compact reprezentace vysokým počtem rozměrů zařazené do kategorií funkce. To není jediný způsob, jak kódovat zařazené do kategorií funkcí. Další informace o jiných technikách zúčastněných uživatelů najdete v dokumentu [jedním hot-encoding](http://en.wikipedia.org/wiki/One-hot) a [hashování](http://en.wikipedia.org/wiki/Feature_hashing).
>

K sestavení počet tabulek na údaje o počtu, použijte data ve složce raw nebo count. Uživatelům se zobrazí v části modelování, jak vytvořit tyto tabulky počet kategorií funkcí od začátku, nebo můžete také použít předem sestavených počet tabulku pro jejich průzkumy. V jaké způsobem při "předchystané počet tabulek" označují, myslíme pomocí počet tabulek, které byly zadány. Podrobné pokyny o tom, jak přistupovat k tyto tabulky jsou uvedené v další části.

## <a name="aml"></a> Vytvoření modelu s Azure Machine Learning
Náš model vytváření procesů ve službě Azure Machine Learning zahrnuje následující kroky:

1. [Získat data z tabulek Hive do Azure Machine Learning](#step1)
2. [Vytvoření experimentu: Vyčištění dat a zpracování volných se počet tabulek](#step2)
3. [Vytváření, trénování a modul score model](#step3)
4. [Vyhodnocení modelu](#step4)
5. [Publikování modelu jako webové služby](#step5)

Nyní jste připraveni k sestavení modelů v Azure Machine Learning studio. Naše dolů jen Vzorkovaná data budou uloženy jako tabulek Hive v clusteru. Pomocí služby Azure Machine Learning **Import dat** modul tato data přečíst. Přihlašovací údaje pro přístup k účtu úložiště tohoto clusteru jsou uvedené v následující.

### <a name="step1"></a> Krok 1: Získání dat z tabulek Hive do Azure Machine Learning pomocí modulu Import dat a vyberte pro experimentu služby machine learning
Začněte výběrem **+ nová** -> **EXPERIMENT** -> **prázdný Experiment**. Pak v **hledání** nahoře vlevo, vyhledejte "Import dat" pole. Přetáhnout myší **Import dat** modulů na experiment plátno (střední části okna) pro použití modulu pro přístup k datům.

To je, co **Import dat** vypadá podobně jako při získávání dat z tabulky Hive:

![Import dat získá data](./media/hive-criteo-walkthrough/i3zRaoj.png)

Pro **Import dat** modulu, hodnoty parametrů, které jsou k dispozici na obrázku jsou jenom příklady řazení je potřeba zadat hodnoty. Tady je některé obecné pokyny o tom, jak vyplnit sady parametrů **Import dat** modulu.

1. Zvolte možnost "Dotaz Hive" pro **zdroj dat**
2. V **databázový dotaz Hive** pole jednoduchý příkaz SELECT * FROM < váš\_databáze\_name.your\_tabulky\_název >-je dostatečná.
3. **Identifikátor URI serveru Hcatalog**: Pokud je váš cluster "abc", je to jednoduše: https://abc.azurehdinsight.net
4. **Název uživatelského účtu systému Hadoop**: Uživatelské jméno zvolené při uvedení do provozu clusteru. (Nikoli vzdáleného přístupu uživatele název!)
5. **Heslo uživatelského účtu systému Hadoop**: Heslo pro uživatelské jméno zvolené při uvedení do provozu clusteru. (Ne heslo vzdáleného přístupu.)
6. **Umístění výstupních dat**: Zvolte možnost "Azure"
7. **Název účtu služby Azure storage**: Účet úložiště, které jsou přidružené ke clusteru
8. **Klíč účtu úložiště Azure**: Klíč účtu úložiště přidruženého clusteru.
9. **Název kontejneru Azure**: Pokud je název clusteru "abc", pak toto je jednoduše "abc", obvykle.

Jednou **Import dat** dokončení získání dat (vidíte zelené značky na modul), uložit tato data jako datovou sadu (s názvem podle vašeho výběru). Jak to vypadá:

![Umožňuje importovat Data uložit data](./media/hive-criteo-walkthrough/oxM73Np.png)

Klikněte pravým tlačítkem na výstupní port modulu **Import dat** modulu. Zobrazí se **uložit jako datovou sadu** možnost a **vizualizovat** možnost. **Vizualizovat** možnost, if kliknuto, zobrazuje 100 řádků dat, včetně pravý panel, který je vhodný pro některé souhrnnou statistiku. Chcete-li uložit data, stačí vybrat **uložit jako datovou sadu** a postupujte podle pokynů.

Vyberte uloženou datovou sadu pro použití v experimentu machine learning, vyhledejte datových sad pomocí **hledání** pole je znázorněno na následujícím obrázku. Jednoduše zadejte název dáte datové sady částečně na přístup a přetáhněte datovou sadu na hlavním panelu. Zastaví na hlavní panel vybere pro použití v machine learning modelování.

![Datová sada Drage na hlavním panelu](./media/hive-criteo-walkthrough/cl5tpGw.png)

> [!NOTE]
> To lze proveďte trénování a datových sad testů. Nezapomeňte použít název databáze a názvy tabulek, které jste zadali pro tento účel. Hodnoty použité na obrázku jsou pouze pro ilustraci purposes.* *
>
>

### <a name="step2"></a> Krok 2: Vytvoření jednoduchého experimentu v Azure Machine Learning k predikci kliknutí / žádné kliknutí
Náš Azure Machine Learning studio experiment vypadá takto:

![Experimentu služby Machine Learning](./media/hive-criteo-walkthrough/xRpVfrY.png)

Nyní prozkoumejte klíčové součásti tohoto experimentu. Přetáhněte naše uložených vlaku a nejdřív otestovat datové sady do našich plátno experimentu.

#### <a name="clean-missing-data"></a>Vyčištění chybějících dat
**Vyčištění chybějících dat** modul provádí co její název napovídá: vyčistí chybějící data způsobem, který může být zadán uživatel. Podívejte se do tohoto modulu a prohlédnout si to:

![Vyčištění chybějících dat](./media/hive-criteo-walkthrough/0ycXod6.png)

Tady se rozhodli nahradit všechny chybějící hodnoty 0. Existují další možnosti, které lze zobrazit pomocí rozevíracích seznamů v modulu.

#### <a name="feature-engineering-on-the-data"></a>Vytváření funkcí pro data
Mohou existovat miliony jedinečné hodnoty pro některé funkce zařazené do kategorií rozsáhlých datových sad. Pomocí metod naivní například horká jeden kódování představující vysokým počtem rozměrů zařazené do kategorií funkcí, je zcela bylo neproveditelné. Tento návod ukazuje, jak používat funkce počet použití integrované moduly Azure Machine Learning k vygenerování compact reprezentace těchto proměnných vysokým počtem rozměrů zařazené do kategorií. Konečný výsledek je menší velikost modelu, kratší časy školení a metriky výkonu, který jsou poměrně srovnatelné s další metody.

##### <a name="building-counting-transforms"></a>Vytváření, počítací transformace
Chcete-li vytvářet funkce count, použijte **sestavení počítání transformace** modul, který je k dispozici ve službě Azure Machine Learning. V modulu vypadá takto:

![Vlastnosti modulu počítání transformace sestavení](./media/hive-criteo-walkthrough/e0eqKtZ.png)
![modul sestavení počítání transformace](./media/hive-criteo-walkthrough/OdDN0vw.png)

> [!IMPORTANT]
> V **počet sloupců** zadejte sloupce, které chcete provádět počty. Ty jsou obvykle (jak je uvedeno) vysokým počtem rozměrů zařazené do kategorií sloupce. Mějte na paměti, že datová sada Criteo má 26 zařazené do kategorií sloupce: z Col15 k Col40. Tady, Spolehněte se na všechny z nich a umožňují jejich indexy (z 15 na 40 oddělených čárkami, jak je znázorněno).
>

Pokud chcete použít modul v režimu MapReduce (vhodné pro velké datové sady), potřebujete přístup ke clusteru HDInsight Hadoop (jaký se používá pro zkoumání funkcí lze opětovně použít pro tento účel také) a přihlašovací údaje. Předchozí obrázky znázorňují jaké vyplněné hodnoty vypadat (Nahraďte hodnoty ilustrativní s těmi, které jsou relevantní pro vlastní případy použití).

![Parametry modulu](./media/hive-criteo-walkthrough/05IqySf.png)

Předchozí obrázek ukazuje, jak zadat umístění vstupnímu objektu blob. Toto umístění obsahuje data vyhrazené pro vytváření počet tabulek v.

Po dokončení tohoto modulu, uložit transformace pro později tak, že kliknete pravým tlačítkem v modulu a vyberete **uloží jako transformace** možnost:

![Možnost "Uložit jako transformace"](./media/hive-criteo-walkthrough/IcVgvHR.png)

V našem experiment architektuře zobrazené výše datové sady "ytransform2" odpovídá přesně transformace uloženého počtu. Pro zbývající část tohoto experimentu, předpokládá se, že čtecí modul používá **sestavení počítání transformace** modulu na některá data ke generování počty a můžete pak pomocí těchto počtů pro generování počet funkce u datových sad trénování a testování.

##### <a name="choosing-what-count-features-to-include-as-part-of-the-train-and-test-datasets"></a>Výběr, jaký počet funkcí zahrnout jako součást datové sady trénování a testování
Jednou připraveného počet transformace, uživatel může zvolit jaké funkce, které chcete zahrnout do jejich trénování a testování pomocí datových sad **upravit parametry tabulky Count** modulu. Pro úplnost je znázorněna zde tento modul. Ale v zájmu jednoduchosti nepoužívejte skutečně ho v našich experimentu.

![Úprava tabulky počet parametrů](./media/hive-criteo-walkthrough/PfCHkVg.png)

V tomto případě jak je vidět, se použijí protokol změn a regrese sloupce se ignoruje. Můžete také nastavit parametry jako prahovou hodnotu bin uvolňování paměti, kolik částečně předchozí příklady přidat pro vyhlazení a jestli se mají ale nemusíte používat jakékoli Laplacian šumu. Všechny tyto pokročilé funkce a je potřeba poznamenat, že výchozí hodnoty jsou dobrým výchozím bodem pro uživatele, kteří jsou k tomuto typu funkce generování nové.

##### <a name="data-transformation-before-generating-the-count-features"></a>Transformace dat před generováním počet funkcí
Nyní je zaměřena na důležitý bod o transformaci naše trénování a testování dat před skutečně generování počet funkcí. Všimněte si, že existují dvě **spustit skript jazyka R** moduly používané před počet transformací, která platí pro naše data.

![Spustit skript jazyka R moduly](./media/hive-criteo-walkthrough/aF59wbc.png)

Zde je první skript jazyka R:

![První skript jazyka R](./media/hive-criteo-walkthrough/3hkIoMx.png)

Tento skript R přejmenuje abychom sloupce na názvy "Sloupec1" k "Col40". Je to proto, že počet transformací, která se očekává, že názvy tohoto formátu.

Druhý skriptu jazyka R vyrovnává rozdělení mezi kladnou a zápornou třídy (třídy 1 a 0) dolů – vzorkováním negativní třídy. Skript jazyka R se tady ukazuje, jak to udělat:

![Druhý skriptu jazyka R](./media/hive-criteo-walkthrough/91wvcwN.png)

V tomto jednoduchý skript jazyka R "pos\_neg\_poměr" se používá k nastavení velikosti rovnováhu mezi pozitivní a negativní třídy. To je důležité provést, protože vylepšení imbalance třídy obvykle má přinese zlepšení výkonu pro klasifikaci problémy, kde třída distribuce je zkosený (Vzpomínáte, že v tomto případě máte kladné a záporné třídu 96.7 % 3.3 %).

##### <a name="applying-the-count-transformation-on-our-data"></a>Použití transformace počet u našich dat
Nakonec můžete použít **použít transformace** modulu pro použití počet transformací na naše trénování a testování datových sad. Tento modul přebírá uloženého počtu transformace jako jeden vstup a trénování nebo testovací datové sady jako ostatní vstupy a vrací data pomocí funkce count. Je znázorněna zde:

![Použít modul transformace](./media/hive-criteo-walkthrough/xnQvsYf.png)

##### <a name="an-excerpt-of-what-the-count-features-look-like"></a>Výňatek funkcích počet vypadat
Je poučné zobrazíte vypadat počet funkcí v našem případě. Tady je výpisem toto:

![Počet funkcí](./media/hive-criteo-walkthrough/FO1nNfw.png)

Tento výpis ukazuje, že u sloupců na můžete získat počtů a protokolovat číslům umocněným na druhou kromě všechny relevantní backoffs.

Teď jste připravení sestavit model ve službě Azure Machine Learning pomocí těchto transformovaných datových sad. V další části ukazuje, jak to lze provést.

### <a name="step3"></a> Krok 3: Vytváření, trénování a modul score model

#### <a name="choice-of-learner"></a>Volba learner
Nejprve musíte zvolit learner. Použijte jako naše learner dvěma třídami posíleného rozhodovacího stromu. Tady jsou výchozí možnosti pro tento learner:

![Two-Class Boosted Decision Tree parametry](./media/hive-criteo-walkthrough/bH3ST2z.png)

Experiment vyberte výchozí hodnoty. Všimněte si, že jsou obvykle smysluplné výchozí hodnoty a vhodným způsobem, jak získat rychlé směrné plány na výkon. Můžete zlepšit výkon cílit na konkrétní parametry Pokud budete chtít po směrný plán.

#### <a name="train-the-model"></a>Trénování modelu
Školení, jednoduše vyvolat **Train Model** modulu. Dva vstupy jsou learner Two-Class Boosted Decision Tree a trénování datovou sadu. To je znázorněna zde:

![Modul trénování modelu](./media/hive-criteo-walkthrough/2bZDZTy.png)

#### <a name="score-the-model"></a>Ohodnocení modelu
Jakmile budete mít trénovaného modelu, jste připraveni ke stanovení skóre pro datovou sadu testů a vyhodnotit její výkon. To provést pomocí **určení skóre modelu** modulu je znázorněno na následujícím obrázku, spolu s **Evaluate Model** modul:

![Modul Určení skóre modelu](./media/hive-criteo-walkthrough/fydcv6u.png)

### <a name="step4"></a> Krok 4: Vyhodnocení modelu
Nakonec byste měli provést analýzu výkonu modelu. Pro dva problémy (binární) klasifikace třídy, je dobré míru obvykle AUC. Vizualizace těchto informací, připojení **určení skóre modelu** modulu **Evaluate Model** modul pro to. Kliknutím na **vizualizovat** na **Evaluate Model** modulu vrací obrázek, jako je následující:

![Vyhodnocení modulu Tabelární model](./media/hive-criteo-walkthrough/0Tl0cdg.png)

V binárním souboru (nebo dvě třídy) klasifikaci problémy, dobré míra přesnost předpovědi je v oblasti pod křivky (AUC). Následující část popisuje naši výsledků pomocí tohoto modelu v naší datové sadě testů. Tím získáte klikněte pravým tlačítkem na výstupní port modulu **Evaluate Model** modul a poté **vizualizovat**.

![Vizualizujte modulu pro vyhodnocení modelu](./media/hive-criteo-walkthrough/IRfc7fH.png)

### <a name="step5"></a> Krok 5: Publikování modelu jako webové služby
Možnost publikovat jako webové služby s minimální fuss model ve službě Azure Machine Learning je užitečné funkce pro vytváření široce dostupné. Po dokončení, každý uživatel mohl provádět volání do webové služby s využitím vstupní data, která potřebují předpovědi a webová služba používá model se vraťte tyto předpovědi.

Nejprve je třeba uložte naše trénovaného modelu jako objekt Trénovaného modelu. To kliknutím pravým tlačítkem myši **Train Model** modulu a použití **uložit jako Trénovaného modelu** možnost.

V dalším kroku vytvoříte vstupní a výstupní porty pro naši webovou službu:

* vstupní port přebírá data ve formuláři stejné jako data, která potřebujete předpovědi
* na výstupní port vrátí popisky vyhodnocení a přidružené pravděpodobnosti.

#### <a name="select-a-few-rows-of-data-for-the-input-port"></a>Vybrat několik řádků dat pro vstupní port
Je vhodné použít **použít transformace SQL** modul se vybere pouze 10 řádků, který má sloužit jako vstupní port data. Vyberte pouze na tyto řádky dat pro naše vstupní port pomocí jazyka SQL je vidět tady:

![Data z vstupního portu](./media/hive-criteo-walkthrough/XqVtSxu.png)

#### <a name="web-service"></a>Webová služba
Nyní jste připraveni spustit malý experiment, který slouží k publikování naši webovou službu.

#### <a name="generate-input-data-for-webservice"></a>Generovat vstupní data pro webovou službu
Jako ID nultého krok protože je velký počet tabulka, trvat pár řádků testovací data a vygenerovat výstupní data z něj pomocí funkcí, počet. To může sloužit jako formát vstupní data pro naši webovou službu. To je znázorněna zde:

![Vytvoření BDT vstupních dat](./media/hive-criteo-walkthrough/OEJMmst.png)

> [!NOTE]
> Pro vstupní data formátu, použijte výstup **počet Featurizer** modulu. Jakmile to dokončí spouštění experimentovat, uložte si výstup z **počet Featurizer** modul jako datovou sadu. Tato datová sada se používá pro vstupní data v webové služby.
>
>

#### <a name="scoring-experiment-for-publishing-webservice"></a>Vyhodnocování experimentu pro publikování webové služby
Nejprve se zobrazí, jak to vypadá. Základní struktura je **Score Model** modul, který přijímá naše trénovaného modelu objektu a vstupních dat, které byly generovány v předchozích krocích pomocí pár řádků **počet Featurizer** modulu. Do projektu si Scored popisky a skóre pravděpodobnosti pomocí "Výběr sloupců v datové sadě".

![Výběr sloupců v datové sadě](./media/hive-criteo-walkthrough/kRHrIbe.png)

Všimněte si, že jak **výběr sloupců v datové sadě** modul lze použít pro "filtrování" data z datové sady. Obsah se tady zobrazí:

![Filtrování pomocí výběr sloupců v datové sadě modulu](./media/hive-criteo-walkthrough/oVUJC9K.png)

Chcete-li získat modré vstupní a výstupní porty, stačí kliknout **připravit webovou službu** v pravém dolním rohu. Spuštění tohoto experimentu také umožňuje publikovat webové služby: klikněte na tlačítko **publikovat webovou službu** ikony v dolní pravé, jak je znázorněno zde:

![Publikování webové služby](./media/hive-criteo-walkthrough/WO0nens.png)

Po publikování webové služby získat přesměrováni na stránku, která vypadá takto:

![Řídicí panel webové služby](./media/hive-criteo-walkthrough/YKzxAA5.png)

Všimněte si, že dva odkazy pro webové služby na levé straně:

* **Žádostí a odpovědí** služby (nebo RRS) je určená pro jeden předpovědi a je, co byly využity v tomto seminář.
* **BATCH EXECUTION** Service (BES) se používá k předpovědím služby batch a vyžaduje, že se vstupní data používá k vytvoření predikcí se nacházejí ve službě Azure Blob Storage.

Kliknutím na odkaz **žádostí a odpovědí** přebírá nám na stránku, která poskytuje v každém okamžiku předem konzervované kód v C#, python a R. Tento kód můžete jednoduše použít pro volání webové služby. Všimněte si, že klíč rozhraní API na této stránce se musí použít pro ověřování.

Je vhodné zkopírovat tuto kódu v pythonu do nové buňky v IPython notebook.

Tady je segment kódu v pythonu se správným klíčem rozhraní API.

![Kód v Pythonu](./media/hive-criteo-walkthrough/f8N4L4g.png)

Všimněte si, že výchozí klíč rozhraní API bylo nahrazeno tématem klíč rozhraní API pro naše webové služby. Kliknutím na **spustit** na tuto buňku v IPython notebook provede následující odpověď:

![Odpověď IPython](./media/hive-criteo-walkthrough/KSxmia2.png)

Pro obě testování příkladů dotázáni na (v rámci JSON skript pythonu), získáte zpět odpovědi v podobě "Scored popisky, Scored pravděpodobnosti". V tomto případě výchozí hodnoty byly vybrány, předem uvedeny kód poskytuje (0 pro všechny číselné sloupce a řetězec "value" pro všechny sloupce zařazené do kategorií).

Tím končí naše názorný postup ukazuje, jak zpracovávat rozsáhlé datové sady pomocí Azure Machine Learning. Práce s terabajtu dat, vytvořen prediktivního modelu a nasadit jako webovou službu v cloudu.

