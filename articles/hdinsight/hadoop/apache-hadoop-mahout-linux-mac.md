---
title: Generování doporučení pomocí Apache Mahout a HDInsight (SSH) – Azure
description: Naučte se používat knihovnu Machine Learning pro Apache Mahout ke generování doporučení filmů pomocí HDInsight (Hadoop).
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/24/2019
ms.openlocfilehash: a3919cf84714b69776222fa35d3163e0915869f7
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2019
ms.locfileid: "70881976"
---
# <a name="generate-movie-recommendations-using-apache-mahout-with-apache-hadoop-in-hdinsight-ssh"></a>Generování filmových doporučení pomocí Apache Mahout s Apache Hadoop v HDInsight (SSH)

[!INCLUDE [mahout-selector](../../../includes/hdinsight-selector-mahout.md)]

Naučte se používat knihovnu Machine Learning pro [Apache Mahout](https://mahout.apache.org) se službou Azure HDInsight ke generování doporučení filmu.

Mahout je knihovna [strojového učení](https://en.wikipedia.org/wiki/Machine_learning) pro Apache Hadoop. Mahout obsahuje algoritmy pro zpracování dat, jako je filtrování, klasifikace a clusteringu. V tomto článku využijete modul pro doporučení ke generování filmových doporučení založených na videích, které vaši přátelé viděli.

## <a name="prerequisites"></a>Požadavky

* Cluster Apache Hadoop v HDInsight. Viz Začínáme [se službou HDInsight v systému Linux](./apache-hadoop-linux-tutorial-get-started.md).

* Klient SSH. Další informace najdete v tématu [připojení ke službě HDInsight (Apache Hadoop) pomocí SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="apache-mahout-versioning"></a>Apache Mahout – Správa verzí

Další informace o verzi Mahout ve službě HDInsight najdete v tématu [verze a Apache Hadoop komponenty služby HDInsight](../hdinsight-component-versioning.md).

## <a name="recommendations"></a>Princip doporučení

Jednou z funkcí, které poskytuje Mahout, je modul doporučení. Tento modul přijímá data ve formátu `userID`, `itemId`a `prefValue` (Předvolby pro položku). Mahout může následně provádět analýzu souběžných výskytů k určení: *Uživatelé, kteří mají přednost pro položku, mají také předvolby pro tyto ostatní položky*. Mahout pak určí uživatele s preferencemi, které se dají použít k doporučení.

Následující pracovní postup je zjednodušený příklad, který používá data filmu:

* **Společný výskyt**: Jana, Alice a Bob mají všechny se *konflikty hvězdičkami*, *Empire se*zastará zpět a *vrátí Jedi*. Mahout určuje, že uživatelé, kteří jako kterýkoli z těchto filmů líbí jeden z těchto filmů, mají také další dva.

* **Společný výskyt**: Bob a Alice se také líbilo *s fiktivním Menace*, *útokem klonů*a *Revengeem Sith*. Mahout určuje, že uživatelé, kteří se k předchozím třem filmům líbí, stejně jako tyto tři filmy.

* **Doporučení podobnosti**: Vzhledem k tomu, že Jana se mi líbilo s prvními tři filmy, Mahout vyhledá videa, která se líbí ostatním uživatelům s podobnými preferencemi, ale Jana nesledoval (a). V tomto případě Mahout doporučuje *fiktivní Menace*, *útok klonů*a *Revengey Sith*.

### <a name="understanding-the-data"></a>Porozumění datům

[GroupLens Research](https://grouplens.org/datasets/movielens/) poskytuje data hodnocení pro filmy ve formátu, který je kompatibilní s Mahout. Tato data jsou k dispozici ve výchozím úložišti clusteru na `/HdiSamples/HdiSamples/MahoutMovieData`adrese.

Existují dva soubory, `moviedb.txt` a. `user-ratings.txt` `user-ratings.txt` Soubor se používá při analýze. `moviedb.txt` Slouží k poskytnutí informací o textu uživatelsky přívětivé při prohlížení výsledků.

Data obsažená v `userID`User-ratings. txt mají strukturu, `movieID`, `userRating` `timestamp`a, která indikuje, jak vysoké má každý uživatel hodnocení filmu. Tady je příklad dat:

    196    242    3    881250949
    186    302    3    891717742
    22     377    1    878887116
    244    51     2    880606923
    166    346    1    886397596

## <a name="run-the-analysis"></a>Spuštění analýzy

Pomocí následujícího příkazu v rámci připojení SSH ke clusteru spusťte úlohu doporučení:

```bash
mahout recommenditembased -s SIMILARITY_COOCCURRENCE -i /HdiSamples/HdiSamples/MahoutMovieData/user-ratings.txt -o /example/data/mahoutout --tempDir /temp/mahouttemp
```

> [!NOTE]  
> Dokončení úlohy může trvat několik minut a může spustit více úloh MapReduce.

## <a name="view-the-output"></a>Zobrazit výstup

1. Po dokončení úlohy použijte následující příkaz k zobrazení vygenerovaného výstupu:

    ```bash
    hdfs dfs -text /example/data/mahoutout/part-r-00000
    ```

    Výstup se zobrazí takto:

        1    [234:5.0,347:5.0,237:5.0,47:5.0,282:5.0,275:5.0,88:5.0,515:5.0,514:5.0,121:5.0]
        2    [282:5.0,210:5.0,237:5.0,234:5.0,347:5.0,121:5.0,258:5.0,515:5.0,462:5.0,79:5.0]
        3    [284:5.0,285:4.828125,508:4.7543354,845:4.75,319:4.705128,124:4.7045455,150:4.6938777,311:4.6769233,248:4.65625,272:4.649266]
        4    [690:5.0,12:5.0,234:5.0,275:5.0,121:5.0,255:5.0,237:5.0,895:5.0,282:5.0,117:5.0]

    První sloupec je `userID`. Hodnoty obsažené v ' [' a '] ' jsou `movieId`:.`recommendationScore`

2. K poskytnutí dalších informací o doporučeních můžete použít výstup spolu s MovieDB. txt. Nejprve zkopírujte soubory místně pomocí následujících příkazů:

    ```bash
    hdfs dfs -get /example/data/mahoutout/part-r-00000 recommendations.txt
    hdfs dfs -get /HdiSamples/HdiSamples/MahoutMovieData/* .
    ```

    Tento příkaz zkopíruje výstupní data do souboru s názvem **Recommendations. txt** v aktuálním adresáři společně s datovými soubory videa.

3. Pomocí následujícího příkazu vytvořte skript v jazyce Python, který vyhledá názvy filmů pro data ve výstupu doporučení:

    ```bash
    nano show_recommendations.py
    ```

    Po otevření editoru použijte jako obsah souboru následující text:

   ```python
   #!/usr/bin/env python

   import sys

   if len(sys.argv) != 5:
        print "Arguments: userId userDataFilename movieFilename recommendationFilename"
        sys.exit(1)

   userId, userDataFilename, movieFilename, recommendationFilename = sys.argv[1:]

   print "Reading Movies Descriptions"
   movieFile = open(movieFilename)
   movieById = {}
   for line in movieFile:
       tokens = line.split("|")
       movieById[tokens[0]] = tokens[1:]
   movieFile.close()

   print "Reading Rated Movies"
   userDataFile = open(userDataFilename)
   ratedMovieIds = []
   for line in userDataFile:
       tokens = line.split("\t")
       if tokens[0] == userId:
           ratedMovieIds.append((tokens[1],tokens[2]))
   userDataFile.close()

   print "Reading Recommendations"
   recommendationFile = open(recommendationFilename)
   recommendations = []
   for line in recommendationFile:
       tokens = line.split("\t")
       if tokens[0] == userId:
           movieIdAndScores = tokens[1].strip("[]\n").split(",")
           recommendations = [ movieIdAndScore.split(":") for movieIdAndScore in movieIdAndScores ]
           break
   recommendationFile.close()

   print "Rated Movies"
   print "------------------------"
   for movieId, rating in ratedMovieIds:
       print "%s, rating=%s" % (movieById[movieId][0], rating)
   print "------------------------"

   print "Recommended Movies"
   print "------------------------"
   for movieId, score in recommendations:
       print "%s, score=%s" % (movieById[movieId][0], score)
   print "------------------------"
   ```

    Data uložíte tak, že stisknete **CTRL + X**, **Y**a nakonec **ENTER** .

4. Spusťte skript Pythonu. Následující příkaz předpokládá, že jste v adresáři, do kterého se stáhly všechny soubory:

    ```bash
    python show_recommendations.py 4 user-ratings.txt moviedb.txt recommendations.txt
    ```

    Tento příkaz vyhledá doporučení vygenerovaná pro ID uživatele 4.

   * Soubor **User-ratings. txt** slouží k načtení hodnocení filmů, které byly ohodnoceny.

   * Soubor **MovieDB. txt** slouží k načtení názvů filmů.

   * **Doporučení. txt** slouží k načtení doporučení filmu pro tohoto uživatele.

     Výstup z tohoto příkazu je podobný následujícímu textu:

       7 let v Tibet (1997), skóre = 5.0 Indiana Novotný a poslední Crusade (1989), skore = 5.0 JAWS (1975), skore = 5.0 Sense and Sensibility (1995), skóre = 5.0 den nezávislosti (ID4) (1996), skóre = 5.0 můj nejlepší přítel (1997), skóre = 5.0 Jerry Maguire (1996), skore = 5.0 Scream 2 (1997), skóre = 5.0 čas k ukončení, A (1996), skóre = 5.0

## <a name="delete-temporary-data"></a>Odstranit dočasná data

Úlohy Mahout neodstraňují dočasná data vytvořená během zpracování úlohy. `--tempDir` Parametr je zadán v příkladu úlohy pro izolaci dočasných souborů do konkrétní cesty pro snadné odstranění. Chcete-li odstranit dočasné soubory, použijte následující příkaz:

```bash
hdfs dfs -rm -f -r /temp/mahouttemp
```

> [!WARNING]  
> Chcete-li znovu spustit příkaz, je nutné také odstranit výstupní adresář. K odstranění tohoto adresáře použijte následující:
>
> `hdfs dfs -rm -f -r /example/data/mahoutout`


## <a name="next-steps"></a>Další postup

Teď, když jste se naučili, jak používat Mahout, můžete zjistit další způsoby práce s daty v HDInsight:

* [Apache Hive se službou HDInsight](hdinsight-use-hive.md)
* [Apache prasete s HDInsight](hdinsight-use-pig.md)
* [MapReduce se službou HDInsight](hdinsight-use-mapreduce.md)