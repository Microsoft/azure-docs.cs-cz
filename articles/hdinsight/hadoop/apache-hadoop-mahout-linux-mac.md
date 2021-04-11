---
title: Generování doporučení pomocí Apache Mahout ve službě Azure HDInsight
description: Naučte se používat knihovnu Machine Learning pro Apache Mahout ke generování filmových doporučení v HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 05/14/2020
ms.openlocfilehash: 4c3484c1e65eb70be484a4a2eebda21a41dbf1ea
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105936865"
---
# <a name="generate-recommendations-using-apache-mahout-in-azure-hdinsight"></a>Generování doporučení pomocí Apache Mahout ve službě Azure HDInsight

Naučte se používat knihovnu Machine Learning pro [Apache Mahout](https://mahout.apache.org) se službou Azure HDInsight ke generování doporučení filmu.

Mahout je knihovna [strojového učení](https://en.wikipedia.org/wiki/Machine_learning) pro Apache Hadoop. Mahout obsahuje algoritmy pro zpracování dat, jako je filtrování, klasifikace a clusteringu. V tomto článku využijete modul pro doporučení ke generování filmových doporučení založených na videích, které vaši přátelé viděli.

Mahout je k dispozici. v HDInsight 3,6 a není k dispozici v HDInsight 4,0. Další informace o verzi Mahout ve službě HDInsight najdete v tématu [verze komponent HDInsight 3,6](../hdinsight-36-component-versioning.md).

## <a name="prerequisites"></a>Požadavky

Cluster Apache Hadoop v HDInsight. Viz Začínáme [se službou HDInsight v systému Linux](./apache-hadoop-linux-tutorial-get-started.md).

## <a name="understanding-recommendations"></a>Princip doporučení

Jednou z funkcí, které poskytuje Mahout, je modul doporučení. Tento modul přijímá data ve formátu `userID` , `itemId` a `prefValue` (Předvolby pro položku). Mahout může následně provádět analýzu souběžných výskytů k určení: *Uživatelé, kteří mají přednost pro položku, mají také předvolby pro tyto ostatní položky*. Mahout pak určí uživatele s preferencemi, které se dají použít k doporučení.

Následující pracovní postup je zjednodušený příklad, který používá data filmu:

* **Navracení**: Jana, Alice a Bob mají všechny *konflikty hvězdiček*, *Empire se* přestará zpět a *vrátí Jedi*. Mahout určuje, že uživatelé, kteří jako kterýkoli z těchto filmů líbí jeden z těchto filmů, mají také další dva.

* **Společný výskyt**: Bob a Alice se také staly *fiktivním Menace*, *útokem klonů* a *Revengeem Sith*. Mahout určuje, že uživatelé, kteří se k předchozím třem filmům líbí, stejně jako tyto tři filmy.

* **Doporučení na podobnost**: vzhledem k tomu, že Jana se líbí prvnímu třimu videu, Mahout vyhledá filmy, které s podobnými preferencemi líbí, ale Jana se nesleduje (líbí se nebo hodnotil). V tomto případě Mahout doporučuje *fiktivní Menace*, *útok klonů* a *Revengey Sith*.

### <a name="understanding-the-data"></a>Porozumění datům

[GroupLens Research](https://grouplens.org/datasets/movielens/) poskytuje data hodnocení pro filmy ve formátu, který je kompatibilní s Mahout. Tato data jsou k dispozici ve výchozím úložišti clusteru na adrese `/HdiSamples/HdiSamples/MahoutMovieData` .

Existují dva soubory, `moviedb.txt` a `user-ratings.txt` . `user-ratings.txt`Soubor se používá při analýze. `moviedb.txt`Slouží k poskytnutí informací o textu uživatelsky přívětivé při prohlížení výsledků.

Data obsažená v `user-ratings.txt` mají strukturu `userID` , `movieID` , `userRating` a `timestamp` , která indikuje, jak vysoké má každý uživatel hodnocení filmu. Tady je příklad dat:

```output
    196    242    3    881250949
    186    302    3    891717742
    22     377    1    878887116
    244    51     2    880606923
    166    346    1    886397596
```

## <a name="run-the-analysis"></a>Spuštění analýzy

1. Připojte se ke clusteru pomocí [příkazu SSH](../hdinsight-hadoop-linux-use-ssh-unix.md) . Níže uvedený příkaz upravte tak, že ho nahradíte názvem clusteru a pak zadáte tento příkaz:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Pomocí následujícího příkazu spusťte úlohu doporučení:

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

    ```output
    1    [234:5.0,347:5.0,237:5.0,47:5.0,282:5.0,275:5.0,88:5.0,515:5.0,514:5.0,121:5.0]
    2    [282:5.0,210:5.0,237:5.0,234:5.0,347:5.0,121:5.0,258:5.0,515:5.0,462:5.0,79:5.0]
    3    [284:5.0,285:4.828125,508:4.7543354,845:4.75,319:4.705128,124:4.7045455,150:4.6938777,311:4.6769233,248:4.65625,272:4.649266]
    4    [690:5.0,12:5.0,234:5.0,275:5.0,121:5.0,255:5.0,237:5.0,895:5.0,282:5.0,117:5.0]
    ```

    První sloupec je `userID` . Hodnoty obsažené v ' [' a '] ' jsou `movieId` : `recommendationScore` .

2. K poskytnutí dalších informací o doporučeních můžete použít výstup spolu s moviedb.txt. Nejprve zkopírujte soubory místně pomocí následujících příkazů:

    ```bash
    hdfs dfs -get /example/data/mahoutout/part-r-00000 recommendations.txt
    hdfs dfs -get /HdiSamples/HdiSamples/MahoutMovieData/* .
    ```

    Tento příkaz zkopíruje výstupní data do souboru s názvem **recommendations.txt** v aktuálním adresáři společně s datovými soubory videa.

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

    Data uložíte tak, že stisknete **CTRL + X**, **Y** a nakonec **ENTER** .

4. Spusťte skript Pythonu. Následující příkaz předpokládá, že jste v adresáři, do kterého se stáhly všechny soubory:

    ```bash
    python show_recommendations.py 4 user-ratings.txt moviedb.txt recommendations.txt
    ```

    Tento příkaz vyhledá doporučení vygenerovaná pro ID uživatele 4.

   * **user-ratings.txt** soubor se používá k načtení vyhodnocených filmů.

   * Soubor **moviedb.txt** slouží k načtení názvů filmů.

   * **recommendations.txt** slouží k načtení doporučení filmu pro tohoto uživatele.

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

## <a name="delete-temporary-data"></a>Odstranit dočasná data

Úlohy Mahout neodstraňují dočasná data vytvořená během zpracování úlohy. `--tempDir`Parametr je zadán v příkladu úlohy pro izolaci dočasných souborů do konkrétní cesty pro snadné odstranění. Chcete-li odstranit dočasné soubory, použijte následující příkaz:

```bash
hdfs dfs -rm -f -r /temp/mahouttemp
```

> [!WARNING]  
> Chcete-li znovu spustit příkaz, je nutné také odstranit výstupní adresář. K odstranění tohoto adresáře použijte následující:
>
> `hdfs dfs -rm -f -r /example/data/mahoutout`

## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili, jak používat Mahout, můžete zjistit další způsoby práce s daty v HDInsight:

* [Apache Hive se službou HDInsight](hdinsight-use-hive.md)
* [MapReduce se službou HDInsight](hdinsight-use-mapreduce.md)
