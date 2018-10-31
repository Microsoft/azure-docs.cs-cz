---
title: Spustit příklady Hadoop MapReduce v HDInsight – Azure
description: Začněte používat ukázky MapReduce v soubory jar zahrnuté v HDInsight. Připojte se ke clusteru pomocí SSH a pak pomocí příkazu Hadoop ke spuštění ukázkové úlohy.
keywords: Příklad jar hadoop, hadoop příklady jar, hadoop mapreduce příklady, příklady mapreduce
services: hdinsight
author: jasonwhowell
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: jasonh
ms.openlocfilehash: 8b6502c23221410af95e71938fbd29f5da04bc9a
ms.sourcegitcommit: 1d3353b95e0de04d4aec2d0d6f84ec45deaaf6ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2018
ms.locfileid: "50250848"
---
# <a name="run-the-mapreduce-examples-included-in-hdinsight"></a>Spuštění příkladů MapReduce v HDInsight

[!INCLUDE [samples-selector](../../../includes/hdinsight-run-samples-selector.md)]

Zjistěte, jak spustit MapReduce příklady součástí systému Hadoop v HDInsight.

## <a name="prerequisites"></a>Požadavky

* **HDInsight cluster**: viz [Začínáme používat Hadoop pomocí Hivu ve službě HDInsight v Linuxu](apache-hadoop-linux-tutorial-get-started.md)

    > [!IMPORTANT]
    > HDInsight od verze 3.4 výše používá výhradně operační systém Linux. Další informace najdete v tématu [Vyřazení prostředí HDInsight ve Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

* **Klient SSH**: Další informace najdete v tématu [použití SSH se službou HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="the-mapreduce-examples"></a>Příklady MapReduce

**Umístění**: jsou ukázky umístěny na clusteru HDInsight na `/usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar`.

**Obsah**: následující ukázky jsou obsaženy v archivu:

* `aggregatewordcount`: Agregaci na základě program mapreduce, který spočítá slova v vstupních souborů.
* `aggregatewordhist`: Agregaci na základě program mapreduce, která vypočítá histogram slov ve vstupních souborů.
* `bbp`: Program mapreduce, která používá Bailey. Borwein Plouffe vypočítat přesné číslice čísla pí.
* `dbcount`: Příklad úlohu, která vrátí počet zobrazení stránky protokolů uložených v databázi.
* `distbbp`: Program mapreduce, která používá BBP typu Vzorec pro výpočet přesné bits pí.
* `grep`: Program mapreduce, který počítá shody regulárního výrazu ve vstupu.
* `join`: Úloha, která provádí spojení přes seřazený, rovnoměrně rozdělit na oddíly datové sady.
* `multifilewc`: Úlohu, která počítá slova z více souborů.
* `pentomino`: Dlaždice, kterým program mapreduce k vyhledání řešení pentomino problémy.
* `pi`: Program mapreduce, který odhaduje pí pomocí dál Monte Carlo metody.
* `randomtextwriter`: Program mapreduce, která zapisuje 10 GB náhodné textových dat podle počtu uzlů.
* `randomwriter`: Program mapreduce, která zapisuje 10 GB náhodných dat na jeden uzel.
* `secondarysort`: Příklad definování sekundární řazení do fáze zmenšit.
* `sort`: Program mapreduce, který seřadí dat zapsaných náhodné zapisovače.
* `sudoku`Řešitel: sudoku.
* `teragen`: Data pro terasort generovat.
* `terasort`: Spusťte terasort.
* `teravalidate`: Kontrola výsledků terasort.
* `wordcount`: Program mapreduce, který spočítá slova v vstupních souborů.
* `wordmean`: Program mapreduce, který počítá průměrná délka slova ve vstupních souborů.
* `wordmedian`: Program mapreduce, který počítá Střední délka slova ve vstupních souborů.
* `wordstandarddeviation`: Program mapreduce, který počítá směrodatné odchylky poměru délky slov ve vstupních souborů.

**Zdrojový kód**: zdrojový kód pro tyto ukázky je zahrnuta v clusteru HDInsight na `/usr/hdp/current/hadoop-client/src/hadoop-mapreduce-project/hadoop-mapreduce-examples`.

## <a name="run-the-wordcount-example"></a>Spusťte příklad wordcount

1. Připojení k HDInsight pomocí SSH. Další informace najdete v tématu [Použití SSH se službou HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Z `username@#######:~$` výzvu, použijte následující příkaz k seznamu vzorků:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar
    ```

    Tento příkaz vygeneruje seznam ukázky z předchozí části tohoto dokumentu.

3. Pomocí následujícího příkazu můžete zobrazit nápovědu pro konkrétní vzorku. V takovém případě **wordcount** vzorku:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount
    ```

    Zobrazí se následující zpráva:

        Usage: wordcount <in> [<in>...] <out>

    Tato zpráva znamená, že můžete zadat několik vstupní cesta pro dokumenty zdroje. Konečné cesty je ukládat výstup (počet slov v dokumentech zdroje).

4. Použijte následující postupy k počítání všechna slova v poznámkových blocích z Leonardo Da Vinci, které jsou k dispozici jako ukázková data pro cluster:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount /example/data/gutenberg/davinci.txt /example/data/davinciwordcount
    ```

    Zadejte pro tuto úlohu je pro čtení z `/example/data/gutenberg/davinci.txt`. Výstup v tomto příkladu je uložen v `/example/data/davinciwordcount`. Obě cesty jsou umístěny na výchozí úložiště clusteru, není místním systému souborů.

   > [!NOTE]
   > Jak je uvedeno v nápovědě pro ukázka wordcountu, můžete také zadat více vstupních souborů. Například `hadoop jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount /example/data/gutenberg/davinci.txt /example/data/gutenberg/ulysses.txt /example/data/twowordcount` počítá slova v davinci.txt a ulysses.txt.

5. Po dokončení úlohy následujícím příkazem zobrazíte výstup:

    ```bash
    hdfs dfs -cat /example/data/davinciwordcount/*
    ```

    Tento příkaz připojí všechny soubory výstup vytvořený úlohou. Zobrazí výstup do konzoly. Výstup se bude podobat následujícímu:

        zum     1
        zur     1
        zwanzig 1
        zweite  1

    Každý řádek představuje slovo a tom, kolikrát k němu ve vstupních datech.

## <a name="the-sudoku-example"></a>V příkladu Sudoku

[Sudoku](https://en.wikipedia.org/wiki/Sudoku) se skládá z devíti 3 × 3 mřížky ještě nám zbývá logiku. Některé buňky v mřížce mají čísla, zatímco jiné jsou prázdné a cílem je řešení pro prázdné buňky. Předchozí odkaz obsahuje další informace o ještě nám zbývá, ale účely této ukázky je řešení pro prázdné buňky. Takže naše mělo by být soubor, který je v následujícím formátu:

* Devět řádky devět sloupců
* Každý sloupec může obsahovat buď v podobě čísla nebo `?` (což znamená prázdné buňky)
* Buňky jsou odděleny mezerou

Existuje určitá způsob, jak vytvořit Sudoku hádanky; nelze opakovat číslo ve sloupci nebo řádku. V clusteru HDInsight, který je vytvořený řádně je příklad. Je umístěný na adrese `/usr/hdp/*/hadoop/src/hadoop-mapreduce-project/hadoop-mapreduce-examples/src/main/java/org/apache/hadoop/examples/dancing/puzzle1.dta` a obsahuje následující text:

    8 5 ? 3 9 ? ? ? ?
    ? ? 2 ? ? ? ? ? ?
    ? ? 6 ? 1 ? ? ? 2
    ? ? 4 ? ? 3 ? 5 9
    ? ? 8 9 ? 1 4 ? ?
    3 2 ? 4 ? ? 8 ? ?
    9 ? ? ? 8 ? 5 ? ?
    ? ? ? ? ? ? 2 ? ?
    ? ? ? ? 4 5 ? 7 8

Pokud chcete spustit tento příklad problém prostřednictvím Sudoku příklad, použijte následující příkaz:

```bash
yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar sudoku /usr/hdp/*/hadoop/src/hadoop-mapreduce-project/hadoop-mapreduce-examples/src/main/java/org/apache/hadoop/examples/dancing/puzzle1.dta
```

Výsledky se zobrazí podobný následujícímu textu:

    8 5 1 3 9 2 6 4 7
    4 3 2 6 7 8 1 9 5
    7 9 6 5 1 4 3 8 2
    6 1 4 8 2 3 7 5 9
    5 7 8 9 6 1 4 2 3
    3 2 9 4 5 7 8 1 6
    9 4 7 2 8 6 5 3 1
    1 8 5 7 3 9 2 6 4
    2 6 3 1 4 5 9 7 8

## <a name="pi--example"></a>Příklad PI (pí)

Ukázka pi používá statistického (dál Monte Carlo) způsob, jak odhadnout hodnotu čísla pí. Body jsou umístěné náhodně v hranatých jednotky. Druhou mocninu také obsahuje kruh. Pravděpodobnost, že body spadají do kruhu rovnají obsah kruhu, pi nebo 4. Z hodnoty 4R se dá odhadnout hodnotu čísla pí. R je poměr počtu bodů, které jsou uvnitř kroužku celkového počtu bodů, které jsou v rámci buňky. Čím větší vzorek bodů použít, je tím lepší odhadu.

Použijte následující příkaz pro spuštění této ukázky. Tento příkaz používá k odhadu hodnoty pí 16 mapy s 10 000 000 vzorků:

```bash
yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar pi 16 10000000
```

Hodnota vrácená tímto příkazem se podobá **3.14159155000000000000**. Odkazy jsou prvních 10 desetinná čísla pí 3.1415926535.

## <a name="10-gb-greysort-example"></a>Příklad Greysort 10 GB

GraySort jsou v podstatě srovnávacího testu. Metrika je dosáhnout při řazení velké objemy dat, obvykle 100 TB minimální řazení rychlost (TB za minutu).

Tato ukázka používá mírné 10 GB dat, aby mohla být poměrně rychle spustit. Používá MapReduce aplikace vyvinuté tak, že Owen O'Malley a Arun Murthy. Tyto aplikace získané roční kritérium řazení terabajt pro obecné účely ("daytona") v roce 2009 s mírou 0.578 TB za minutu (100 TB za 173 minut). Další informace o tomto a dalších řazení srovnávací testy, najdete v článku [Sortbenchmark](http://sortbenchmark.org/) lokality.

Tato ukázka používá tři páry programů MapReduce:

* **TeraGen**: program A MapReduce, který generuje data seřadit řádky

* **TeraSort**: ukázkové vstupní data a řadit data do celkového pořadí pomocí MapReduce

    TeraSort je standardní řazení MapReduce, s výjimkou vlastního rozdělovače. Dělicí používá seřazený seznam N-1 vzorkovány klíče, které definovat rozsah klíče pro každý zmenšit. Zejména všechny klíče takové, které ukázkový [i-1] < = klíč < ukázka [i] odešlou ke snížení i. Tato dělicí metoda záruky, že výstupy snížit i všechny jsou menší, než se výstup snížit i + 1.

* **TeraValidate**: program A MapReduce, který ověří, že výstup globálně seřazené

    Vytvoří jednu mapu každý soubor ve výstupním adresáři a každé mapování zajistí, že každý klíč je menší nebo rovna předchozí. Mapování funkce generuje záznamy první a poslední klíče každého souboru. Funkci reduce zajistí, že první klíč souboru je větší než poslední klíč souboru i-1. Žádné problémy s označené jako výstup fáze snížit, pomocí klíčů, které jsou mimo pořadí.

Pomocí následujících kroků pro generování dat, řazení a ověřte výstup:

1. Generovat 10 GB dat, která je uložena na výchozím úložištěm clusteru HDInsight na `/example/data/10GB-sort-input`:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teragen -Dmapred.map.tasks=50 100000000 /example/data/10GB-sort-input
    ```

    `-Dmapred.map.tasks` Říká Hadoop kolik mapy úkolů pro tuto úlohu. Poslední dva parametry dáte pokyn, aby úloha vytvoření 10 GB dat a uložte ho na `/example/data/10GB-sort-input`.

2. Chcete-li seřadit data použijte následující příkaz:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar terasort -Dmapred.map.tasks=50 -Dmapred.reduce.tasks=25 /example/data/10GB-sort-input /example/data/10GB-sort-output
    ```

    `-Dmapred.reduce.tasks` Říká Hadoop, kolik omezení úloh pro úlohu. Poslední dva parametry jsou pouze vstupní a výstupní umístění pro data.

3. Pomocí následujícího postupu ověřit data generovaná řazení:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teravalidate -Dmapred.map.tasks=50 -Dmapred.reduce.tasks=25 /example/data/10GB-sort-output /example/data/10GB-sort-validate
    ```

## <a name="next-steps"></a>Další postup

V tomto článku jste zjistili, jak ke spuštění ukázky zahrnuté v clusterech HDInsight založených na Linuxu. Kurzy o používání Pig, Hive a MapReduce s HDInsight najdete v následujících tématech:

* [Použití Pigu se systémem Hadoop v HDInsight](hdinsight-use-pig.md)
* [Použití Hivu s Hadoopem v HDInsight](hdinsight-use-hive.md)
* [Použití MapReduce se systémem Hadoop v HDInsight](hdinsight-use-mapreduce.md)

[hdinsight-submit-jobs]:submit-apache-hadoop-jobs-programmatically.md
[hdinsight-introduction]:apache-hadoop-introduction.md



[hdinsight-samples]: hdinsight-run-samples.md

