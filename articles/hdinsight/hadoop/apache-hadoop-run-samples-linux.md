---
title: Spuštění Apache Hadoop MapReduce příklady na HDInsight - Azure
description: Začínáme používat MapReduce ukázky v jar soubory zahrnuté v HDInsight. Pomocí SSH se připojte ke clusteru a potom pomocí příkazu Hadoop spusťte ukázkové úlohy.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 12/12/2019
ms.openlocfilehash: 58f7d99af638c8d03bbce46b7fcf8204aaca11d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75435753"
---
# <a name="run-the-mapreduce-examples-included-in-hdinsight"></a>Spuštění příkladů MapReduce zahrnutých do HDInsightu

[!INCLUDE [samples-selector](../../../includes/hdinsight-run-samples-selector.md)]

Přečtěte si, jak spustit příklady MapReduce, které jsou součástí Apache Hadoop na HDInsight.

## <a name="prerequisites"></a>Požadavky

* Cluster Apache Hadoop na HDInsight. Viz [Začínáme s HDInsight na Linuxu](./apache-hadoop-linux-tutorial-get-started.md).

* Klient SSH. Další informace naleznete [v tématu Připojení k HDInsight (Apache Hadoop) pomocí SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="the-mapreduce-examples"></a>Příklady MapReduce

Ukázky jsou umístěny v `/usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar`clusteru HDInsight na adrese . Zdrojový kód pro tyto ukázky je `/usr/hdp/current/hadoop-client/src/hadoop-mapreduce-project/hadoop-mapreduce-examples`součástí clusteru HDInsight na adrese .

V tomto archivu jsou uloženy následující ukázky:

|Ukázka |Popis |
|---|---|
|aggregatewordcount|Spočítá slova ve vstupních souborech.|
|aggregatewordhist|Vypočítá histogram slov ve vstupních souborech.|
|bbp|Používá Bailey-Borwein-Plouffe k výpočtu přesných číslic Pi.|
|počet dbcount|Spočítá protokoly zobrazení stránky uložené v databázi.|
|distbbp|Používá vzorec typu BBP k výpočtu přesných bitů Pí.|
|grep|Spočítá shody regulární výraz ve vstupu.|
|join|Provede spojení přes seřazené, rovnoměrně rozdělené datové sady.|
|multifilewc|Počítá slova z několika souborů.|
|pentomino|Tile pokládání program najít řešení problémů pentomino.|
|Pi|Odhaduje Pi pomocí kvazi-Monte Carlo metody.|
|randomtextwriter|Zapíše 10 GB náhodných textových dat na uzel.|
|randomwriter|Zapíše 10 GB náhodných dat na uzel.|
|sekundární řazení|Definuje sekundární řazení do fáze snížení.|
|sort|Seřadí data napsaná náhodným zapisovatelem.|
|Sudoku|Řešitel sudoku.|
|teragen|Generovat data pro terasort.|
|terasort|Projeďte terasort.|
|teravalidate|Kontrola výsledků terasortu.|
|počet slov|Spočítá slova ve vstupních souborech.|
|slovní mean|Spočítá průměrnou délku slov ve vstupních souborech.|
|medián slov|Spočítá střední délku slov ve vstupních souborech.|
|wordstandarddeviation|Spočítá směrodatnou odchylku délky slov ve vstupních souborech.|

## <a name="run-the-wordcount-example"></a>Spuštění příkladu počtu slov

1. Připojte se k HDInsight pomocí SSH. Nahraďte `CLUSTER` název clusteru a zadejte následující příkaz:

    ```cmd
    ssh sshuser@CLUSTER-ssh.azurehdinsight.net
    ```

2. Z relace SSH použijte následující příkaz k zobrazení ukázek:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar
    ```

    Tento příkaz vygeneruje seznam vzorku z předchozí části tohoto dokumentu.

3. Pomocí následujícího příkazu získáte nápovědu k určité ukázce. V tomto případě ukázka **počtu slov:**

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount
    ```

    Zobrazí se následující zpráva:

    ```output
    Usage: wordcount <in> [<in>...] <out>
    ```

    Tato zpráva označuje, že můžete poskytnout několik vstupních cest pro zdrojové dokumenty. Konečná cesta je, kde je uložen výstup (počet slov ve zdrojových dokumentech).

4. Pomocí následujícího příkazu spočítejte všechna slova v poznámkových blocích Leonarda da Vinciho, která jsou k dispozici jako ukázková data s vaším clusterem:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount /example/data/gutenberg/davinci.txt /example/data/davinciwordcount
    ```

    Vstup pro tuto úlohu je číst z `/example/data/gutenberg/davinci.txt`. Výstup pro tento příklad `/example/data/davinciwordcount`je uložen v aplikaci . Obě cesty jsou umístěny ve výchozím úložišti pro cluster, nikoli v místním systému souborů.

   > [!NOTE]  
   > Jak je uvedeno v nápovědě pro ukázku počtu slov, můžete také zadat více vstupních souborů. Například `hadoop jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount /example/data/gutenberg/davinci.txt /example/data/gutenberg/ulysses.txt /example/data/twowordcount` by počítat slova v obou davinci.txt a ulysses.txt.

5. Po dokončení úlohy zobrazte výstup pomocí následujícího příkazu:

    ```bash
    hdfs dfs -cat /example/data/davinciwordcount/*
    ```

    Tento příkaz zřetězí všechny výstupní soubory vytvořené úlohou. Zobrazí výstup do konzoly. Výstup se bude podobat následujícímu:

        zum     1
        zur     1
        zwanzig 1
        zweite  1

    Každý řádek představuje slovo a kolikrát k němu došlo ve vstupních datech.

## <a name="the-sudoku-example"></a>Příklad Sudoku

[Sudoku](https://en.wikipedia.org/wiki/Sudoku) je logická hádanka tvořená devíti mřížkami 3x3. Některé buňky v mřížce mají čísla, zatímco jiné jsou prázdné a cílem je vyřešit prázdné buňky. Předchozí odkaz má více informací o puzzle, ale účelem tohoto vzorku je vyřešit pro prázdné buňky. Takže náš vstup by měl být soubor, který je v následujícím formátu:

* Devět řádků po devíti sloupcích
* Každý sloupec může obsahovat `?` číslo nebo (což označuje prázdnou buňku)
* Buňky jsou odděleny mezerou

Existuje určitý způsob, jak postavit sudoku hádanky; číslo ve sloupci nebo řádku nelze opakovat. V clusteru HDInsight je příklad, který je správně sestaven. Nachází se `/usr/hdp/*/hadoop/src/hadoop-mapreduce-project/hadoop-mapreduce-examples/src/main/java/org/apache/hadoop/examples/dancing/puzzle1.dta` na a obsahuje následující text:

    8 5 ? 3 9 ? ? ? ?
    ? ? 2 ? ? ? ? ? ?
    ? ? 6 ? 1 ? ? ? 2
    ? ? 4 ? ? 3 ? 5 9
    ? ? 8 9 ? 1 4 ? ?
    3 2 ? 4 ? ? 8 ? ?
    9 ? ? ? 8 ? 5 ? ?
    ? ? ? ? ? ? 2 ? ?
    ? ? ? ? 4 5 ? 7 8

Chcete-li spustit tento příklad problému v příkladu Sudoku, použijte následující příkaz:

```bash
yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar sudoku /usr/hdp/*/hadoop/src/hadoop-mapreduce-project/hadoop-mapreduce-examples/src/main/java/org/apache/hadoop/examples/dancing/puzzle1.dta
```

Výsledky se podobají následujícímu textu:

    8 5 1 3 9 2 6 4 7
    4 3 2 6 7 8 1 9 5
    7 9 6 5 1 4 3 8 2
    6 1 4 8 2 3 7 5 9
    5 7 8 9 6 1 4 2 3
    3 2 9 4 5 7 8 1 6
    9 4 7 2 8 6 5 3 1
    1 8 5 7 3 9 2 6 4
    2 6 3 1 4 5 9 7 8

## <a name="pi--example"></a>Příklad Pi (π)

Vzorek pí používá statistickou metodu (kvazi-Monte Carlo) k odhadu hodnoty pí. Body jsou umístěny náhodně v jednotce náměstí. Čtverec také obsahuje kruh. Pravděpodobnost, že body spadají do kruhu, se rovná ploše kruhu, pi/4. Hodnota pí lze odhadnout z hodnoty 4R. R je poměr počtu bodů, které jsou uvnitř kruhu, k celkovému počtu bodů, které jsou uvnitř čtverce. Čím větší je vzorek použitých bodů, tím lepší je odhad.

Ke spuštění této ukázky použijte následující příkaz. Tento příkaz používá 16 map s 10 000 000 vzorky pro odhad hodnoty pí:

```bash
yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar pi 16 10000000
```

Hodnota vrácená tímto příkazem je podobná **hodnotě 3.1415915500000000000000**. Pro referenci je prvních 10 desetinných míst pí 3,1415926535.

## <a name="10-gb-graysort-example"></a>10 GB GraySort příklad

GraySort je srovnávací řazení. Metrika je rychlost řazení (TB/min), které je dosaženo při třídění velkého množství dat, obvykle minimálně 100 TB.

Tato ukázka používá skromný 10 GB dat tak, aby bylo možné spustit poměrně rychle. Používá aplikace MapReduce vyvinuté Owenem O'Malleym a Arunem Murthym. Tyto aplikace získaly v roce 2009 roční univerzální terabajtovou referenční hodnotu s mírou 0,578 TB/min (100 TB za 173 minut). Další informace o tomto a dalších srovnávacích testech řazení naleznete na webu [Sort Benchmark.](https://sortbenchmark.org/)

Tato ukázka používá tři sady programů MapReduce:

* **TeraGen**: Program MapReduce, který generuje řádky dat k řazení

* **TeraSort**: Vzorky vstupních dat a používá MapReduce k řazení dat do celkové objednávky

    TeraSort je standardní MapReduce řazení, s výjimkou vlastní partitioner. Rozdělovač používá seřazený seznam n-1 vzorkované klíče, které definují rozsah klíčů pro každý snížit. Zejména všechny klíče tak, aby vzorek[i-1] <= < klíč vzorek[i] jsou odesílány ke snížení i. Tento partitioner zaručuje, že výstupy reduce i jsou všechny menší než výstup snížit i + 1.

* **TeraValidate**: Program MapReduce, který ověřuje, zda je výstup globálně seřazen

    Vytvoří jednu mapu na soubor ve výstupním adresáři a každá mapa zajistí, že každá klávesa je menší nebo rovna předchozí. Funkce mapy generuje záznamy prvního a posledního klíče každého souboru. Funkce reduce zajišťuje, že první klíč souboru i je větší než poslední klíč souboru i-1. Všechny problémy jsou hlášeny jako výstup fáze snížení, s klíči, které jsou mimo pořadí.

Pomocí následujících kroků můžete generovat data, seřadit a pak ověřit výstup:

1. Vygenerujte 10 GB dat, která jsou uložena `/example/data/10GB-sort-input`ve výchozím úložišti clusteru HDInsight na adrese :

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teragen -Dmapred.map.tasks=50 100000000 /example/data/10GB-sort-input
    ```

    Hadoop `-Dmapred.map.tasks` říká, kolik mapových úkolů použít pro tuto úlohu. Poslední dva parametry instruují úlohu k vytvoření 10 GB dat a k jejich uložení na adrese `/example/data/10GB-sort-input`.

2. K seřazení dat použijte následující příkaz:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar terasort -Dmapred.map.tasks=50 -Dmapred.reduce.tasks=25 /example/data/10GB-sort-input /example/data/10GB-sort-output
    ```

    Hadoop `-Dmapred.reduce.tasks` říká, kolik snížit úkoly pro práci. Poslední dva parametry jsou pouze vstupní a výstupní umístění pro data.

3. K ověření dat generovaných řazením použijte následující:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teravalidate -Dmapred.map.tasks=50 -Dmapred.reduce.tasks=25 /example/data/10GB-sort-output /example/data/10GB-sort-validate
    ```

## <a name="next-steps"></a>Další kroky

Z tohoto článku jste se dozvěděli, jak spustit ukázky, které jsou součástí clusterů HDInsight založených na Linuxu. Výukové programy o používání pig, hive a mapreduce s HDInsight najdete v následujících tématech:

* [Použití Apache Hive s Apache Hadoop na HDInsight](hdinsight-use-hive.md)
* [Použití MapReduce s Apache Hadoop na HDInsight](hdinsight-use-mapreduce.md)