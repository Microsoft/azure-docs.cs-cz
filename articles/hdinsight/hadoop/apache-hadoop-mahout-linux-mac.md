---
title: Generování doporučení pomocí Mahout a HDInsight (SSH) - Azure | Microsoft Docs
description: Další informace o použití Apache Mahout strojového učení knihovny pro generování doporučení s HDInsight (Hadoop).
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: c78ec37c-9a8c-4bb6-9e38-0bdb9e89fbd7
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 01/29/2018
ms.author: larryfr
ms.openlocfilehash: 357df033c4d3fd0da84ea595d68802546aee3e9a
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="generate-movie-recommendations-by-using-apache-mahout-with-linux-based-hadoop-in-hdinsight-ssh"></a>Generování doporučení pomocí Apache Mahout se systémem Linux Hadoop v HDInsight (SSH)

[!INCLUDE [mahout-selector](../../../includes/hdinsight-selector-mahout.md)]

Další informace o použití [Apache Mahout](http://mahout.apache.org) knihovny machine learning s Azure HDInsight ke generování doporučení.

Je mahout [strojového učení] [ ml] knihovny pro Apache Hadoop. Mahout obsahuje algoritmy pro zpracování dat, jako je například filtrování, klasifikace a clustering. V tomto článku použijete modul doporučení k vygenerování film doporučení, které jsou založeny na filmy, které jste viděli vašich přátel.

## <a name="prerequisites"></a>Požadavky

* Cluster HDInsight se systémem Linux. Informace o vytváření jeden najdete v tématu [začít používat systémem Linux Hadoop v HDInsight][getstarted].

> [!IMPORTANT]
> HDInsight od verze 3.4 výše používá výhradně operační systém Linux. Další informace najdete v tématu [Vyřazení prostředí HDInsight ve Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

* Klientem SSH. Další informace najdete v dokumentu [Použití SSH se službou HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="mahout-versioning"></a>Správa verzí mahout

Další informace o verzi nástroje Mahout v prostředí HDInsight najdete v tématu [HDInsight verze a komponent systému Hadoop](../hdinsight-component-versioning.md).

## <a name="recommendations"></a>Vysvětlení doporučení

Jednou z funkcí, které zajišťuje Mahout je modul doporučení. Tento modul přijímá data ve formátu `userID`, `itemId`, a `prefValue` (předvoleb pro položku). Mahout pak můžete provádět analýzy společné výskyt určit: *uživatelé, kteří mají předvolbu pro položku také mít předvolbu pro tyto další položky*. Mahout pak určuje uživatelé s předvolby jako položky, které se dají použít tak, aby doporučení.

Následující pracovní postup je zjednodušená příklad, který používá film data:

* **společné výskyt**: Jan, Alice a Bob všechny líbilo *hvězdičky válek*, *Empire stávky zpět*, a *návrat Jedi*. Mahout Určuje, že uživatelé, kteří také jako některého z těchto filmy jako další dvě.

* **společné výskyt**: Bob a Alice také líbilo *The Menace fiktivní*, *útoku klonů*, a *odvety Sith*. Mahout Určuje, že uživatelé, kteří líbilo předchozí tři filmy také jako tyto tři filmy.

* **Podobnosti doporučení**: protože Jan líbilo první tři filmy, Mahout vypadá na filmy ostatní s líbilo podobných předvolby, ale nebyla Jan sledovaná (líbilo nebo hodnocení). V takovém případě se doporučuje Mahout *The Menace fiktivní*, *útoku klonů*, a *odvety Sith*.

### <a name="understanding-the-data"></a>Pochopení dat

Pohodlně [GroupLens Research] [ movielens] poskytuje hodnocení data pro filmy ve formátu, který je kompatibilní s Mahout. Tato data jsou k dispozici na váš cluster výchozí úložiště na `/HdiSamples/HdiSamples/MahoutMovieData`.

Existují dva soubory, `moviedb.txt` a `user-ratings.txt`. `user-ratings.txt` Soubor se používá během analýzy. `moviedb.txt` Slouží k poskytování popisný text informace při zobrazení výsledků.

Data obsažená v ratings.txt uživatel má struktura `userID`, `movieID`, `userRating`, a `timestamp`, která určuje, jak vysoce každý uživatel hodnocení filmu. Tady je příklad dat:

    196    242    3    881250949
    186    302    3    891717742
    22    377    1    878887116
    244    51    2    880606923
    166    346    1    886397596

## <a name="run-the-analysis"></a>Spuštění analýzy

Z připojení SSH do clusteru použijte následující příkaz pro spuštění úlohy doporučení:

```bash
mahout recommenditembased -s SIMILARITY_COOCCURRENCE -i /HdiSamples/HdiSamples/MahoutMovieData/user-ratings.txt -o /example/data/mahoutout --tempDir /temp/mahouttemp
```

> [!NOTE]
> Úloha může trvat několik minut na dokončení a může spustit víc úloh MapReduce.

## <a name="view-the-output"></a>Zobrazit výstup

1. Po dokončení úlohy pro zobrazení generovaný výstup použijte následující příkaz:

    ```bash
    hdfs dfs -text /example/data/mahoutout/part-r-00000
    ```

    Výstup vypadá takto:

        1    [234:5.0,347:5.0,237:5.0,47:5.0,282:5.0,275:5.0,88:5.0,515:5.0,514:5.0,121:5.0]
        2    [282:5.0,210:5.0,237:5.0,234:5.0,347:5.0,121:5.0,258:5.0,515:5.0,462:5.0,79:5.0]
        3    [284:5.0,285:4.828125,508:4.7543354,845:4.75,319:4.705128,124:4.7045455,150:4.6938777,311:4.6769233,248:4.65625,272:4.649266]
        4    [690:5.0,12:5.0,234:5.0,275:5.0,121:5.0,255:5.0,237:5.0,895:5.0,282:5.0,117:5.0]

    První sloupec je `userID`. Hodnoty obsažené v ' [' a ']' jsou `movieId`:`recommendationScore`.

2. Můžete použít výstup, společně s moviedb.txt, poskytovat další informace o doporučení. Nejdřív zkopírujte soubory místně pomocí následujících příkazů:

    ```bash
    hdfs dfs -get /example/data/mahoutout/part-r-00000 recommendations.txt
    hdfs dfs -get /HdiSamples/HdiSamples/MahoutMovieData/* .
    ```

    Tento příkaz zkopíruje výstupní data do souboru s názvem **recommendations.txt** v aktuálním adresáři, společně s film datových souborů.

3. Pomocí následujícího příkazu vytvořte skript jazyka Python, který vyhledá názvy film pro data ve výstupu doporučení:

    ```bash
    nano show_recommendations.py
    ```

    Když se otevře editor, použijte jako obsah souboru následující text:

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

    Stiskněte klávesu **Ctrl-X**, **Y**a v neposlední řadě **Enter** uložit data.

4. Spusťte skript Python. Tento příkaz předpokládá, že jsou v adresáři, kde byly staženy všechny soubory:

    ```bash
    python show_recommendations.py 4 user-ratings.txt moviedb.txt recommendations.txt
    ```

    Tento příkaz zjistí doporučení vygenerované uživatelské ID 4.

    * **Uživatele ratings.txt** souboru se používá k načtení filmy, které nemají hodnocení.

    * **Moviedb.txt** souboru se používá k načtení názvy videa.

    * **Recommendations.txt** se používá k načtení film doporučení pro tohoto uživatele.

     Výstup tohoto příkazu je podobná následující text:

        Sedm let v Tibet (1997), stanovení skóre = 5.0 Indiana Petr a poslední Crusade (1989), stanovení skóre = 5.0 Jaws (1975) skóre = 5.0 smysl a citlivosti (1995), skóre = 5.0 nezávislost den (ID4) (1996), skóre = 5.0 Moje nejlepší přítel svatbě (1997), stanovení skóre = 5.0 Jerry Maguire (1996) skóre = 5.0 Scream 2 (1997) skóre = 5.0 dobu Kill, (1996), stanovení skóre = 5.0

## <a name="delete-temporary-data"></a>Odstranit dočasná data

Mahout úlohy neodebírejte dočasná data, která je vytvořena při zpracování úlohy. `--tempDir` v úloze příklad izolovat dočasných souborů do určité cesty pro snadné odstranění je zadán parametr. Chcete-li odebrat dočasné soubory, použijte následující příkaz:

```bash
hdfs dfs -rm -f -r /temp/mahouttemp
```

> [!WARNING]
> Pokud chcete znovu spustit příkaz, musíte také odstranit výstupnímu adresáři. Chcete-li odstranit tento adresář použijte následující:
>
> `hdfs dfs -rm -f -r /example/data/mahoutout`


## <a name="next-steps"></a>Další postup

Teď, když jste se naučili použití Mahout, zjišťovat další způsoby, jak pracovat s daty v HDInsight:

* [Hive s HDInsight](hdinsight-use-hive.md)
* [Pig s HDInsight](hdinsight-use-pig.md)
* [MapReduce s HDInsight](hdinsight-use-mapreduce.md)

[build]: http://mahout.apache.org/developers/buildingmahout.html
[movielens]: http://grouplens.org/datasets/movielens/
[100k]: http://files.grouplens.org/datasets/movielens/ml-100k.zip
[getstarted]:apache-hadoop-linux-tutorial-get-started.md
[upload]: hdinsight-upload-data.md
[ml]: http://en.wikipedia.org/wiki/Machine_learning
[forest]: http://en.wikipedia.org/wiki/Random_forest
[enableremote]: ./media/hdinsight-mahout/enableremote.png
[connect]: ./media/hdinsight-mahout/connect.png
[hadoopcli]: ./media/hdinsight-mahout/hadoopcli.png
[tools]: https://github.com/Blackmist/hdinsight-tools
