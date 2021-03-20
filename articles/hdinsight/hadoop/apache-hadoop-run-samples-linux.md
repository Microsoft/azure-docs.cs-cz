---
title: Příklady spuštění Apache Hadoop MapReduce na HDInsight – Azure
description: Začněte používat Ukázky MapReduce v souborech jar obsažených v HDInsight. Pomocí SSH se připojte ke clusteru a potom pomocí příkazu Hadoop spusťte ukázkové úlohy.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 12/12/2019
ms.openlocfilehash: 5e75a39b1f9e8503097914b0c9e735915f9ae667
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98943225"
---
# <a name="run-the-mapreduce-examples-included-in-hdinsight"></a>Spuštění příkladů MapReduce obsažených v HDInsight

[!INCLUDE [samples-selector](../../../includes/hdinsight-run-samples-selector.md)]

Naučte se spouštět příklady MapReduceů, které jsou součástí Apache Hadoop v HDInsight.

## <a name="prerequisites"></a>Předpoklady

* Cluster Apache Hadoop v HDInsight. Viz Začínáme [se službou HDInsight v systému Linux](./apache-hadoop-linux-tutorial-get-started.md).

* Klient SSH. Další informace najdete v tématu [Připojení ke službě HDInsight (Apache Hadoop) pomocí SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="the-mapreduce-examples"></a>Příklady MapReduce

Ukázky se nacházejí v clusteru HDInsight na adrese `/usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar` . Zdrojový kód pro tyto ukázky je součástí clusteru HDInsight na adrese `/usr/hdp/current/hadoop-client/src/hadoop-mapreduce-project/hadoop-mapreduce-examples` .

V tomto archivu jsou uvedené následující ukázky:

|Ukázka |Popis |
|---|---|
|aggregatewordcount|Spočítá slova ve vstupních souborech.|
|aggregatewordhist|Vypočítá histogram slov ve vstupních souborech.|
|bbp|Používá Bailey-Borwein-Plouffe k výpočtu přesných číslic v PI.|
|dbcount|Spočítá protokoly PageView uložené v databázi.|
|distbbp|K výpočtu přesných bitů PI používá vzorec BBP typu.|
|grep|Počítá shodu regulárního výrazu ve vstupu.|
|join|Provede spojení se setříděnými a rovnoměrně rozdělenými datovými sadami.|
|multifilewc|Spočítá slova z několika souborů.|
|pentomino|Program pro rozložení dlaždic, který vyhledá řešení, která pentomino problémy.|
|čísla|Odhadne číslo pí pomocí kvazi-Monte Carlo metody.|
|randomtextwriter|Zapisuje 10 GB náhodných textových dat na uzel.|
|randomwriter|Zapisuje 10 GB náhodných dat na uzel.|
|secondarysort|Definuje sekundární řazení do fáze zmenšení.|
|sort|Seřadí data zapsaná náhodným zapisovačem.|
|sudoku|Řešitel sudoku|
|teragen|Vygenerujte data pro terasort.|
|terasort|Spusťte terasort.|
|teravalidate|Kontrola výsledků terasort|
|WORDCOUNT|Spočítá slova ve vstupních souborech.|
|wordmean|Spočítá průměrnou délku slov ve vstupních souborech.|
|wordmedian|Spočítá medián slov ve vstupních souborech.|
|wordstandarddeviation|Počítá směrodatnou odchylku délky slov ve vstupních souborech.|

## <a name="run-the-wordcount-example"></a>Spuštění příkladu WORDCOUNT

1. Připojte se ke službě HDInsight pomocí SSH. Nahraďte `CLUSTER` názvem vašeho clusteru a potom zadejte následující příkaz:

    ```cmd
    ssh sshuser@CLUSTER-ssh.azurehdinsight.net
    ```

2. Z relace SSH použijte následující příkaz k vypsání ukázek:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar
    ```

    Tento příkaz vygeneruje seznam ukázek z předchozí části tohoto dokumentu.

3. Pomocí následujícího příkazu můžete získat nápovědu ke konkrétní ukázce. V tomto případě je to ukázka **WORDCOUNT** :

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount
    ```

    Zobrazí se následující zpráva:

    ```output
    Usage: wordcount <in> [<in>...] <out>
    ```

    Tato zpráva znamená, že pro zdrojové dokumenty můžete zadat několik vstupních cest. Poslední cesta je místo, kde je uložen výstup (počet slov ve zdrojových dokumentech).

4. Pomocí následujícího parametru můžete spočítat všechna slova v poznámkových blocích Leonardo da Vinci, která jsou k dispozici jako vzorová data ve vašem clusteru:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount /example/data/gutenberg/davinci.txt /example/data/davinciwordcount
    ```

    Vstup pro tuto úlohu je načtený z `/example/data/gutenberg/davinci.txt` . Výstup v tomto příkladu je uložen v `/example/data/davinciwordcount` . Obě cesty se nacházejí ve výchozím úložišti pro cluster, nikoli v místním systému souborů.

   > [!NOTE]  
   > Jak je uvedeno v nápovědě ke ukázce WORDCOUNT, můžete také zadat více vstupních souborů. Například `hadoop jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount /example/data/gutenberg/davinci.txt /example/data/gutenberg/ulysses.txt /example/data/twowordcount` by bylo třeba spočítat slova v davinci.txt i ulysses.txt.

5. Po dokončení úlohy použijte následující příkaz k zobrazení výstupu:

    ```bash
    hdfs dfs -cat /example/data/davinciwordcount/*
    ```

    Tento příkaz zřetězí všechny výstupní soubory vytvářené úlohou. Zobrazí výstup do konzoly. Výstup se bude podobat následujícímu:

    ```output
    zum     1
    zur     1
    zwanzig 1
    zweite  1
    ```

    Každý řádek představuje slovo a kolikrát k němu došlo ve vstupních datech.

## <a name="the-sudoku-example"></a>Příklad Sudoku

[Sudoku](https://en.wikipedia.org/wiki/Sudoku) je logicová skládanka tvořená devíti mřížkami 3x3. Některé buňky v mřížce mají čísla, zatímco ostatní jsou prázdné, a cílem je vyřešit prázdné buňky. Předchozí odkaz obsahuje další informace o skládankě, ale účelem této ukázky je vyřešit prázdné buňky. Takže náš vstup by měl být soubor, který je v následujícím formátu:

* Devět řádků devíti sloupců
* Každý sloupec může obsahovat buď číslo, nebo `?` (což označuje prázdnou buňku).
* Buňky jsou oddělené mezerou.

Existuje určitý způsob, jak vytvořit Sudoku puzzle; nelze opakovat číslo v rámci sloupce nebo řádku. V clusteru HDInsight je příklad, který je správně vytvořen. Je umístěný na adrese `/usr/hdp/*/hadoop/src/hadoop-mapreduce-project/hadoop-mapreduce-examples/src/main/java/org/apache/hadoop/examples/dancing/puzzle1.dta` a obsahuje následující text:

```output
8 5 ? 3 9 ? ? ? ?
? ? 2 ? ? ? ? ? ?
? ? 6 ? 1 ? ? ? 2
? ? 4 ? ? 3 ? 5 9
? ? 8 9 ? 1 4 ? ?
3 2 ? 4 ? ? 8 ? ?
9 ? ? ? 8 ? 5 ? ?
? ? ? ? ? ? 2 ? ?
? ? ? ? 4 5 ? 7 8
```

Chcete-li spustit tento ukázkový problém prostřednictvím příkladu Sudoku, použijte následující příkaz:

```bash
yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar sudoku /usr/hdp/*/hadoop/src/hadoop-mapreduce-project/hadoop-mapreduce-examples/src/main/java/org/apache/hadoop/examples/dancing/puzzle1.dta
```

Výsledky se zobrazí podobně jako následující text:

```output
8 5 1 3 9 2 6 4 7
4 3 2 6 7 8 1 9 5
7 9 6 5 1 4 3 8 2
6 1 4 8 2 3 7 5 9
5 7 8 9 6 1 4 2 3
3 2 9 4 5 7 8 1 6
9 4 7 2 8 6 5 3 1
1 8 5 7 3 9 2 6 4
2 6 3 1 4 5 9 7 8
```

## <a name="pi--example"></a>PI (π) – příklad

Ukázka PI používá statistickou metodu (kvazi-Monte Carlo) k odhadu hodnoty pí. Body se umístí náhodně do čtverce jednotek. Čtverec také obsahuje kruh. Pravděpodobnost, že body spadají do kruhu, je rovna oblasti kružnice, PI/4. Hodnota pí může být odhadnuta z hodnoty 4R. R je poměr počtu bodů, které jsou uvnitř kruhu, na celkový počet bodů, které jsou uvnitř čtverce. Čím větší je vzorek použitých bodů, tím lepší je odhad.

K provedení této ukázky použijte následující příkaz. Tento příkaz k odhadu hodnoty pí používá 16 map s 10 000 000 ukázkami.

```bash
yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar pi 16 10000000
```

Hodnota vrácená tímto příkazem je podobná **3.14159155000000000000**. V případě odkazů je prvních 10 desetinných míst hodnoty PI 3,1415926535.

## <a name="10-gb-graysort-example"></a>GraySort příklad 10 GB

GraySort je srovnávací řazení. Metrika je míra řazení (TB/min), která se dosáhne při řazení velkých objemů dat, obvykle minimálně 100 TB.

Tato ukázka používá mírné 10 GB dat, aby je bylo možné spustit relativně rychle. Používá aplikace MapReduce vyvinuté Owen O'Malley a Arun Murthy. Tyto aplikace vyhrály srovnávací srovnávací výraz pro řazení ("Daytona") terabajtu v 2009, s rychlostí 0,578 TB/min (100 TB za 173 minuty). Další informace o tomto a dalších srovnávacích srovnávacích testech najdete na webu [řazení srovnávacích](https://sortbenchmark.org/) testů.

Tato ukázka používá tři sady MapReduce programů:

* **TeraGen**: program MapReduce, který generuje řádky dat k seřazení

* **TeraSort**: vyvzorkuje vstupní data a pomocí MapReduce seřadí data do celkového pořadí.

    TeraSort je standardní řazení MapReduce, s výjimkou vlastního rozdělovače. Dělicí metoda používá seřazený seznam N-1 ukázkových klíčů, které definují rozsah klíčů pro každé zmenšení. Konkrétně jsou odesílány všechny klíče, jako je ukázka [i-1] <= ukázka < klíče [i], aby se snížila i. Tento dělicí metoda zaručuje, že výstupy s omezením i jsou všechny menší než výstupy s omezením i + 1.

* **TeraValidate**: program MapReduce, který ověřuje, že výstup je globálně seřazený

    Vytvoří jednu mapu na soubor ve výstupním adresáři a každá mapa zajistí, že každý klíč je menší nebo roven předchozímu. Funkce map generuje záznamy o první a poslední klíč každého souboru. Funkce zmenšení zajišťuje, že první klíč souboru i je větší než poslední klíč souboru i-1. Jakékoli problémy jsou hlášeny jako výstup fáze zmenšení s klíči, které jsou mimo pořadí.

Pomocí následujících kroků vygenerujte data, seřaďte je a pak ověřte výstup:

1. Vygenerujte 10 GB dat, která se ukládají do výchozího úložiště clusteru HDInsight v `/example/data/10GB-sort-input` :

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teragen -Dmapred.map.tasks=50 100000000 /example/data/10GB-sort-input
    ```

    `-Dmapred.map.tasks`Instruuje, kolik úloh mapování se má pro tuto úlohu použít. Poslední dva parametry instruují úlohu, aby vytvořila 10 GB dat a ukládal ji na `/example/data/10GB-sort-input` .

2. Data seřaďte pomocí následujícího příkazu:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar terasort -Dmapred.map.tasks=50 -Dmapred.reduce.tasks=25 /example/data/10GB-sort-input /example/data/10GB-sort-output
    ```

    V této části jsou `-Dmapred.reduce.tasks` informace o počtu úloh, které se mají u úlohy používat. Poslední dva parametry jsou pouze vstupní a výstupní umístění pro data.

3. K ověření dat generovaných řazením použijte následující:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teravalidate -Dmapred.map.tasks=50 -Dmapred.reduce.tasks=25 /example/data/10GB-sort-output /example/data/10GB-sort-validate
    ```

## <a name="next-steps"></a>Další kroky

V tomto článku jste zjistili, jak spustit ukázky, které jsou součástí clusterů HDInsight se systémem Linux. Výukové programy týkající se použití prasete, podregistru a MapReduce se službou HDInsight najdete v následujících tématech:

* [Použití Apache Hive s Apache Hadoop v HDInsight](hdinsight-use-hive.md)
* [Použití MapReduce s Apache Hadoop v HDInsight](hdinsight-use-mapreduce.md)
