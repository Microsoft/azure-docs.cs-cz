---
title: Generování doporučení pomocí Apache Mahout v Azure HDInsight
description: Naučte se používat knihovnu strojového učení Apache Mahout ke generování filmových doporučení pomocí HDInsight (Hadoop).
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/03/2020
ms.openlocfilehash: 33110e9f1d45fcd11e5f4cad1b589ab929a9472d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75767632"
---
# <a name="generate-movie-recommendations-using-apache-mahout-with-apache-hadoop-in-hdinsight-ssh"></a>Generovat film doporučení pomocí Apache Mahout s Apache Hadoop v HDInsight (SSH)

[!INCLUDE [mahout-selector](../../../includes/hdinsight-selector-mahout.md)]

Naučte se používat knihovnu strojového učení [Apache Mahout](https://mahout.apache.org) s Azure HDInsight ke generování doporučení pro filmy.

Mahout je knihovna [strojového učení](https://en.wikipedia.org/wiki/Machine_learning) pro Apache Hadoop. Mahout obsahuje algoritmy pro zpracování dat, jako je například filtrování, klasifikace a clustering. V tomto článku použijete modul doporučení ke generování doporučení filmu, které jsou založeny na filmech, které viděli vaši přátelé.

## <a name="prerequisites"></a>Požadavky

Cluster Apache Hadoop na HDInsight. Viz [Začínáme s HDInsight na Linuxu](./apache-hadoop-linux-tutorial-get-started.md).

## <a name="apache-mahout-versioning"></a>Apache Mahout správa verzí

Další informace o verzi programu Mahout v HDInsightu najdete v [tématu VERZE HDInsight a součásti Apache Hadoop](../hdinsight-component-versioning.md).

## <a name="understanding-recommendations"></a>Principy doporučení

Jednou z funkcí, které poskytuje Mahout je modul doporučení. Tento modul přijímá data ve `userID` `itemId`formátu `prefValue` , , a (preference pro položku). Mahout pak může provést analýzu kovýskytu, aby zjistil: *uživatelé, kteří mají přednost pro položku, mají také přednost pro tyto další položky*. Mahout pak určuje uživatele s předvolbami podobné položky, které lze použít k doporučení.

Následující pracovní postup je zjednodušený příklad, který používá filmová data:

* **Co-výskyt**: Joe, Alice, a Bob všichni rádi *Star Wars*, *Impérium vrací úder*, a Návrat *Jedi*. Mahout určuje, že uživatelé, kteří mají rádi některý z těchto filmů také jako další dva.

* **Co-výskyt**: Bob a Alice také rád *Phantom Hrozba*, *Útok klonů*, a *Pomsta Sithů*. Mahout určuje, že uživatelé, kteří měli rádi předchozí tři filmy také jako tyto tři filmy.

* **Doporučení podobnosti**: Vzhledem k tomu, že se Joeovi líbily první tři filmy, Mahout se dívá na filmy, které se líbily ostatním s podobnými preferencemi, ale Joe se nedíval (líbilo se / hodnoceno). V tomto případě Mahout doporučuje *Fantomovou hrozbu*, *Útok klonů*a *Pomstu Sithů*.

### <a name="understanding-the-data"></a>Principy dat

[Společnost GroupLens Research](https://grouplens.org/datasets/movielens/) pohodlně poskytuje údaje o hodnocení filmů ve formátu, který je kompatibilní s mahoutem. Tato data jsou k dispozici ve `/HdiSamples/HdiSamples/MahoutMovieData`výchozím úložišti clusteru na adrese .

Existují dva soubory `moviedb.txt` `user-ratings.txt`a . Soubor `user-ratings.txt` se používá během analýzy. Slouží `moviedb.txt` k poskytování uživatelsky přívětivých textových informací při prohlížení výsledků.

Data obsažená `user-ratings.txt` v písmenu `userID` `movieID`a) mají strukturu , , `userRating`a `timestamp`, která označuje, jak vysoce každý uživatel hodnotil film. Zde je příklad dat:

    196    242    3    881250949
    186    302    3    891717742
    22     377    1    878887116
    244    51     2    880606923
    166    346    1    886397596

## <a name="run-the-analysis"></a>Spuštění analýzy

1. Pomocí [příkazu ssh](../hdinsight-hadoop-linux-use-ssh-unix.md) se připojte ke clusteru. Upravte níže uvedený příkaz nahrazením názvu clusteru názvem clusteru a zadejte příkaz:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Ke spuštění úlohy doporučení použijte následující příkaz:

    ```bash
    mahout recommenditembased -s SIMILARITY_COOCCURRENCE -i /HdiSamples/HdiSamples/MahoutMovieData/user-ratings.txt -o /example/data/mahoutout --tempDir /temp/mahouttemp
    ```

> [!NOTE]  
> Úloha může trvat několik minut a může spustit více úloh MapReduce.

## <a name="view-the-output"></a>Zobrazit výstup

1. Po dokončení úlohy zobrazte generovaný výstup pomocí následujícího příkazu:

    ```bash
    hdfs dfs -text /example/data/mahoutout/part-r-00000
    ```

    Výstup se zobrazí takto:

    ```output
    1    [234:5.0,347:5.0,237:5.0,47:5.0,282:5.0,275:5.0,88:5.0,515:5.0,514:5.0,121:5.0]
    2    [282:5.0,210:5.0,237:5.0,234:5.0,347:5.0,121:5.0,258:5.0,515:5.0,462:5.0,79:5.0]
    3    [284:5.0,285:4.828125,508:4.7543354,845:4.75,319:4.705128,124:4.7045455,150:4.6938777,311:4.6769233,248:4.65625,272:4.649266]
    4    [690:5.0,12:5.0,234:5.0,275:5.0,121:5.0,255:5.0,237:5.0,895:5.0,282:5.0,117:5.0]
    ```

    První sloupec je `userID`. Hodnoty obsažené v [[' a ']' jsou: `movieId``recommendationScore`.

2. Můžete použít výstup, spolu s moviedb.txt, poskytnout další informace o doporučení. Nejprve zkopírujte soubory místně pomocí následujících příkazů:

    ```bash
    hdfs dfs -get /example/data/mahoutout/part-r-00000 recommendations.txt
    hdfs dfs -get /HdiSamples/HdiSamples/MahoutMovieData/* .
    ```

    Tento příkaz zkopíruje výstupní data do souboru s názvem **recommendations.txt** v aktuálním adresáři spolu s datovými soubory filmu.

3. Pomocí následujícího příkazu vytvořte skript Pythonu, který vyhledá názvy filmů pro data ve výstupu doporučení:

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

    Stisknutím **kláves Ctrl-X**, **Y**a nakonec **enter** data uložte.

4. Spusťte skript Pythonu. Následující příkaz předpokládá, že se nacházejí v adresáři, kde byly staženy všechny soubory:

    ```bash
    python show_recommendations.py 4 user-ratings.txt moviedb.txt recommendations.txt
    ```

    Tento příkaz se zabývá doporučeními generovanými pro ID uživatele 4.

   * Soubor **user-ratings.txt** se používá k načtení filmů, které byly hodnoceny.

   * Soubor **moviedb.txt** se používá k načtení názvů filmů.

   * Soubor **recommendations.txt** slouží k načtení doporučení pro film pro tohoto uživatele.

     Výstup z tohoto příkazu je podobný následujícímu textu:

        ```output
        Seven Years in Tibet (1997), score=5.0
        Indiana Jones and the Last Crusade (1989), score=5.0
        Jaws (1975), score=5.0
        Sense and Sensibility (1995), score=5.0
        Independence Day (ID4) (1996), score=5.0
        My Best Friend's Wedding (1997), score=5.0
        Jerry Maguire (1996), score=5.0
        Scream 2 (1997), score=5.0
        Time to Kill, A (1996), score=5.0
        ```

## <a name="delete-temporary-data"></a>Odstranění dočasných dat

Úlohy Mahout neodeberou dočasná data, která jsou vytvořena při zpracování úlohy. Parametr `--tempDir` je určen v ukázkové úloze izolovat dočasné soubory do konkrétní cesty pro snadné odstranění. Chcete-li dočasné soubory odebrat, použijte následující příkaz:

```bash
hdfs dfs -rm -f -r /temp/mahouttemp
```

> [!WARNING]  
> Chcete-li příkaz spustit znovu, musíte také odstranit výstupní adresář. K odstranění tohoto adresáře použijte následující:
>
> `hdfs dfs -rm -f -r /example/data/mahoutout`

## <a name="next-steps"></a>Další kroky

Nyní, když jste se naučili používat Mahout, objevte další způsoby práce s daty na HDInsightu:

* [Apache Hive s HDInsight](hdinsight-use-hive.md)
* [MapReduce s HDInsight](hdinsight-use-mapreduce.md)
