---
title: Vývoj Python streamování úloh MapReduce s HDInsight - Azure | Microsoft Docs
description: Naučte se používat jazyk Python ve streamování úloh MapReduce. Hadoop poskytuje streamování rozhraní API pro MapReduce pro zápis do jiných jazyků než Java.
services: hdinsight
keyword: mapreduce python,python map reduce,python mapreduce
documentationcenter: ''
author: Blackmist
manager: cgronlun
editor: cgronlun
tags: azure-portal
ms.assetid: 7631d8d9-98ae-42ec-b9ec-ee3cf7e57fb3
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/10/2018
ms.author: larryfr
ms.openlocfilehash: b5e19f81c3e869347f21ab3c70a70016196b946d
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
ms.locfileid: "31400510"
---
# <a name="develop-python-streaming-mapreduce-programs-for-hdinsight"></a>Vývoj Python streamování MapReduce programy pro HDInsight

Naučte se používat jazyk Python ve streamování MapReduce operations. Hadoop poskytuje streamování rozhraní API pro MapReduce, který umožňuje zapisovat mapy a omezit funkce v jiných jazyků než Java. Kroky v tomto dokumentu implementovat mapy a snížit součásti v Pythonu.

## <a name="prerequisites"></a>Požadavky

* Systémem Linux Hadoop v HDInsight clusteru

  > [!IMPORTANT]
  > Kroky v tomto dokumentu vyžadují clusteru služby HDInsight, který používá Linux. HDInsight od verze 3.4 výše používá výhradně operační systém Linux. Další informace najdete v tématu [Vyřazení prostředí HDInsight ve Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

* V textovém editoru

  > [!IMPORTANT]
  > Textový editor, musí používat LF jako ukončování řádků. Pomocí konec řádku Line FEED způsobuje chyby při spuštění úlohy MapReduce v clusterech HDInsight se systémem Linux.

* `ssh` a `scp` příkazy, nebo [prostředí Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-3.8.0)

## <a name="word-count"></a>Počet slov

V tomto příkladu je, že počet základní slov implementované v python reduktorem a mapper. Mapper věty dělí do jednotlivých slov a reduktorem slučuje slova a počty k vytvoření výstupu.

Následující vývojový diagram znázorňuje, co se stane při mapy a snížit fáze.

![Obrázek procesu mapreduce](./media/apache-hadoop-streaming-python/HDI.WordCountDiagram.png)

## <a name="streaming-mapreduce"></a>Streamování MapReduce

Hadoop umožňuje zadat soubor, který obsahuje mapy a snížit logiky, která se používá v rámci úlohy. Požadavky na konkrétní mapy a snížit logiky jsou:

* **Vstupní**: mapy a snížit součásti musí číst vstupní data z STDIN.
* **Výstup**: mapy a snížit součásti musí zapsat do STDOUT výstupní data.
* **Formát dat**: data využívat a vytváří musí být dvojice klíč/hodnota, oddělených tabulátorem.

Python můžete snadno zpracování těchto požadavků pomocí `sys` modulu číst z stdin – a pomocí `print` tisknout na STDOUT. Zbývající úloh je jednoduše formátování dat pomocí na kartě (`\t`) znak mezi klíčem a hodnotou.

## <a name="create-the-mapper-and-reducer"></a>Vytvoření mapper a reduktorem

1. Vytvořte soubor s názvem `mapper.py` a použít následující kód jako obsah:

   ```python
   #!/usr/bin/env python

   # Use the sys module
   import sys

   # 'file' in this case is STDIN
   def read_input(file):
       # Split each line into words
       for line in file:
           yield line.split()

   def main(separator='\t'):
       # Read the data using read_input
       data = read_input(sys.stdin)
       # Process each word returned from read_input
       for words in data:
           # Process each word
           for word in words:
               # Write to STDOUT
               print '%s%s%d' % (word, separator, 1)

   if __name__ == "__main__":
       main()
   ```

2. Vytvořte soubor s názvem **reducer.py** a použít následující kód jako obsah:

   ```python
   #!/usr/bin/env python

   # import modules
   from itertools import groupby
   from operator import itemgetter
   import sys

   # 'file' in this case is STDIN
   def read_mapper_output(file, separator='\t'):
       # Go through each line
       for line in file:
           # Strip out the separator character
           yield line.rstrip().split(separator, 1)

   def main(separator='\t'):
       # Read the data using read_mapper_output
       data = read_mapper_output(sys.stdin, separator=separator)
       # Group words and counts into 'group'
       #   Since MapReduce is a distributed process, each word
       #   may have multiple counts. 'group' will have all counts
       #   which can be retrieved using the word as the key.
       for current_word, group in groupby(data, itemgetter(0)):
           try:
               # For each word, pull the count(s) for the word
               #   from 'group' and create a total count
               total_count = sum(int(count) for current_word, count in group)
               # Write to stdout
               print "%s%s%d" % (current_word, separator, total_count)
           except ValueError:
               # Count was not a number, so do nothing
               pass

   if __name__ == "__main__":
       main()
   ```

## <a name="run-using-powershell"></a>Spuštění pomocí prostředí PowerShell

K zajištění, že soubory mají právo konce řádků, použijte následující skript prostředí PowerShell:

[!code-powershell[main](../../../powershell_scripts/hdinsight/streaming-python/streaming-python.ps1?range=138-140)]

Pomocí následujícího skriptu prostředí PowerShell k odesílání souborů, spusťte úlohu a zobrazte výstup:

[!code-powershell[main](../../../powershell_scripts/hdinsight/streaming-python/streaming-python.ps1?range=5-134)]

## <a name="run-from-an-ssh-session"></a>Spuštění z relace SSH

1. Z vývojového prostředí, ve stejném adresáři jako `mapper.py` a `reducer.py` soubory, použijte následující příkaz:

    ```bash
    scp mapper.py reducer.py username@clustername-ssh.azurehdinsight.net:
    ```

    Nahraďte `username` s uživatelským jménem SSH pro váš cluster a `clustername` s názvem vašeho clusteru.

    Tento příkaz zkopíruje soubory z místního systému k hlavnímu uzlu.

    > [!NOTE]
    > Pokud jste použili heslo k zabezpečení účtu SSH, zobrazí se výzva k zadání hesla. Pokud jste použili klíč SSH, budete možná muset použít `-i` parametr a cestu k privátnímu klíči. Například, `scp -i /path/to/private/key mapper.py reducer.py username@clustername-ssh.azurehdinsight.net:`.

2. Připojte se ke clusteru pomocí protokolu SSH:

    ```bash
    ssh username@clustername-ssh.azurehdinsight.net`
    ```

    Další informace o najdete v tématu [použití SSH s HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

3. K zajištění, že mapper.py a reducer.py konců správné čar, použijte následující příkazy:

    ```bash
    perl -pi -e 's/\r\n/\n/g' mapper.py
    perl -pi -e 's/\r\n/\n/g' reducer.py
    ```

4. Použijte následující příkaz pro spuštění úlohy MapReduce.

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar -files mapper.py,reducer.py -mapper mapper.py -reducer reducer.py -input /example/data/gutenberg/davinci.txt -output /example/wordcountout
    ```

    Tento příkaz má následující části:

   * **hadoop streaming.jar**: používá při provádění operací streamování MapReduce. Je rozhraní Hadoop externí MapReduce kódem, který zadáte.

   * **-soubory**: Přidá zadané soubory do úlohy MapReduce.

   * **-mapper**: informuje Hadoop, který soubor má použít jako mapper.

   * **-reduktorem**: informuje Hadoop, který soubor má použít jako reduktorem.

   * **-vstupní**: slova vstupního souboru, který jsme měli počítat z.

   * **-výstupu**: adresář, který je výstup zapsán do.

    Jak funguje úlohu MapReduce, proces se zobrazí jako procenta.

        15/02/05 19:01:04 informace o mapreduce. Úloha: % 0 mapy snížit 0 % 15/02/05 19:01:16 informace o mapreduce. Úloha: 100 % mapy snížit 0 % 15/02/05 19:01:27 informace o mapreduce. Úloha: 100 % mapy snížit 100 %


5. Chcete-li zobrazit výstup, použijte následující příkaz:

    ```bash
    hdfs dfs -text /example/wordcountout/part-00000
    ```

    Tento příkaz zobrazí seznam slova a jak často slovo došlo k chybě.

## <a name="next-steps"></a>Další postup

Teď, když jste se naučili použití úlohy streamování MapRedcue s HDInsight, pomocí následujících odkazů a prozkoumejte další způsoby, jak pracovat s Azure HDInsight.

* [Použití Hivu se službou HDInsight](hdinsight-use-hive.md)
* [Použití Pigu se službou HDInsight](hdinsight-use-pig.md)
* [Použití úloh MapReduce se službou HDInsight](hdinsight-use-mapreduce.md)
