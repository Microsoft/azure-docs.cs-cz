---
title: Vývoj streamovacích úloh MapReduce s HDInsight – Azure v Pythonu
description: Zjistěte, jak pomocí Pythonu v streamovacích úloh MapReduce. Hadoop pro MapReduce poskytuje rozhraní API pro streamování, pro zápis v jiných jazycích než Java.
services: hdinsight
keyword: mapreduce python,python map reduce,python mapreduce
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 04/10/2018
ms.author: hrasheed
ms.openlocfilehash: 5ed61eb05d0aa4360e042097d6b8c632c17ea59c
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2018
ms.locfileid: "51008721"
---
# <a name="develop-python-streaming-mapreduce-programs-for-hdinsight"></a>Vývoj programů MapReduce se streamováním pro HDInsight v Pythonu

Naučte se používat Python v MapReduce operací datového proudu. Hadoop poskytuje rozhraní API pro streamování pro MapReduce, která umožňuje zápis mapy a omezit funkce v jiných jazycích než Java. Kroky v tomto dokumentu implementujte mapy a snížit komponent v Pythonu.

## <a name="prerequisites"></a>Požadavky

* V clusteru HDInsight Hadoop založených na Linuxu

  > [!IMPORTANT]
  > Kroky v tomto dokumentu vyžadují cluster HDInsight s Linuxem. HDInsight od verze 3.4 výše používá výhradně operační systém Linux. Další informace najdete v tématu [Vyřazení prostředí HDInsight ve Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

* Textový editor

  > [!IMPORTANT]
  > Textový editor, musíte použít LF jako ukončení řádku. Použití řádku konec CRLF způsobí chyby při spuštění úlohy MapReduce v clusterech HDInsight založených na Linuxu.

* `ssh` a `scp` příkazy, nebo [prostředí Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-3.8.0)

## <a name="word-count"></a>Počet slov

V tomto příkladu je že to počet slov základní implementované v python mapovací a redukční funkci. Mapovač konce věty do jednotlivých slov a redukční funkci agreguje slova a k vytvoření výstupu se počítá.

Následující diagram znázorňuje, co se stane během mapy a snížit fáze.

![znázornění procesu mapreduce](./media/apache-hadoop-streaming-python/HDI.WordCountDiagram.png)

## <a name="streaming-mapreduce"></a>Streamování MapReduce

Hadoop umožňuje zadat soubor, který obsahuje mapu a snížit logiku, která používají úlohy. Konkrétní požadavky na mapě a snížit logiky jsou:

* **Vstupní**: mapy a snížit součásti musí číst vstupní data ze STDIN.
* **Výstup**: mapy a snížit součásti musí zapsat výstupní data do STDOUT.
* **Formát dat**: data používat a vytváří musí být pár klíč hodnota oddělené tabulátorem.

Python pomocí snadno zvládne tyto požadavky `sys` modulu pro čtení z STDIN a pomocí `print` tisknout do STDOUT. Zbývající úloh je jednoduše formátování dat pomocí karty (`\t`) znak mezi klíčem a hodnotou.

## <a name="create-the-mapper-and-reducer"></a>Vytvoření mapovací a redukční funkci

1. Vytvořte soubor s názvem `mapper.py` a použijte následující kód jako obsah:

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

2. Vytvořte soubor s názvem **reducer.py** a použijte následující kód jako obsah:

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

K zajištění, že soubory mají právo konce řádků, použijte následující příkaz powershellu:

[!code-powershell[main](../../../powershell_scripts/hdinsight/streaming-python/streaming-python.ps1?range=138-140)]

Pomocí následujícího skriptu prostředí PowerShell pro nahrávání souborů, spuštění úlohy a zobrazte výstup:

[!code-powershell[main](../../../powershell_scripts/hdinsight/streaming-python/streaming-python.ps1?range=5-134)]

## <a name="run-from-an-ssh-session"></a>Spusťte z relace SSH

1. Z vývojového prostředí, ve stejném adresáři jako `mapper.py` a `reducer.py` soubory, použijte následující příkaz:

    ```bash
    scp mapper.py reducer.py username@clustername-ssh.azurehdinsight.net:
    ```

    Nahraďte `username` s uživatelským jménem SSH pro váš cluster a `clustername` s názvem vašeho clusteru.

    Tento příkaz zkopíruje soubory z místního systému k hlavnímu uzlu.

    > [!NOTE]
    > Pokud jste použili heslo k zabezpečení účtu SSH, zobrazí se výzva k zadání hesla. Pokud jste použili klíče SSH, bude pravděpodobně nutné použít `-i` parametr a cesta k privátnímu klíči. Například, `scp -i /path/to/private/key mapper.py reducer.py username@clustername-ssh.azurehdinsight.net:`.

2. Připojte se ke clusteru pomocí SSH:

    ```bash
    ssh username@clustername-ssh.azurehdinsight.net`
    ```

    Další informace o najdete v tématu [použití SSH se službou HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

3. K zajištění mapper.py a reducer.py mají správnou ukončení řádků, použijte následující příkazy:

    ```bash
    perl -pi -e 's/\r\n/\n/g' mapper.py
    perl -pi -e 's/\r\n/\n/g' reducer.py
    ```

4. Použijte následující příkaz pro spuštění úlohy MapReduce.

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar -files mapper.py,reducer.py -mapper mapper.py -reducer reducer.py -input /example/data/gutenberg/davinci.txt -output /example/wordcountout
    ```

    Tento příkaz má následující části:

   * **hadoop streaming.jar**: při provádění operací streamování MapReduce. Rozhraní systému Hadoop, s externí kód MapReduce, který zadáte.

   * **– soubory**: Přidá zadané soubory do úlohy MapReduce.

   * **-Mapovač**: Určuje soubor, který chcete použít jako mapovač Hadoop.

   * **-redukční funkci**: Určuje soubor, který chcete použít jako redukční funkci Hadoop.

   * **-vstupní**: vstupního souboru, který jsme měli počítat slova z.

   * **-výstupní**: adresáře, který je výstup zapsán.

    Jak funguje úlohu MapReduce, proces se zobrazí jako procenta.

        15/02/05 19:01:04 informace o mapreduce. Úloha: mapování 0 % snížení 0 % 15/02/05 19:01:16 informace o mapreduce. Úloha: mapování 100 % snížení 0 % 15/02/05 19:01:27. informace o mapreduce. Úloha: mapování 100 % snížení 100 %


5. Pokud chcete zobrazit výstup, použijte následující příkaz:

    ```bash
    hdfs dfs -text /example/wordcountout/part-00000
    ```

    Tento příkaz zobrazí seznam slov, a jak často slovo došlo k chybě.

## <a name="next-steps"></a>Další postup

Teď, když jste se naučili použití datových proudů úloh MapRedcue s HDInsight, pomocí následujících odkazů a prozkoumejte další možnosti, jak pracovat s Azure HDInsight.

* [Použití Hivu se službou HDInsight](hdinsight-use-hive.md)
* [Použití Pigu se službou HDInsight](hdinsight-use-pig.md)
* [Použití úloh MapReduce se službou HDInsight](hdinsight-use-mapreduce.md)
