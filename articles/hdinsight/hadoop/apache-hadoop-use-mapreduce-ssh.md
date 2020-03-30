---
title: Propojení MapReduce a SSH s Apache Hadoop - Azure HDInsight
description: Naučte se používat SSH ke spuštění mapreduce úloh pomocí Apache Hadoop na HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/10/2020
ms.openlocfilehash: 543bc29adc85bd767de9479607d067fadf7b0078
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75934702"
---
# <a name="use-mapreduce-with-apache-hadoop-on-hdinsight-with-ssh"></a>Použití MapReduce s Apache Hadoop na HDInsight s SSH

[!INCLUDE [mapreduce-selector](../../../includes/hdinsight-selector-use-mapreduce.md)]

Zjistěte, jak odeslat úlohy MapReduce z připojení zabezpečeného prostředí (SSH) do hdinsightu.

> [!NOTE]
> Pokud jste již obeznámeni s používáním linuxových serverů Apache Hadoop, ale jste v HDInsightu noví, přečtěte [si tipy hdinsight založený na Linuxu](../hdinsight-hadoop-linux-information.md).

## <a name="prerequisites"></a>Požadavky

Cluster Apache Hadoop na HDInsight. Viz [Vytvoření clusterů Apache Hadoop pomocí portálu Azure](../hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="use-hadoop-commands"></a>Použití příkazů Hadoop

1. Pomocí [příkazu ssh](../hdinsight-hadoop-linux-use-ssh-unix.md) se připojte ke clusteru. Upravte níže uvedený příkaz nahrazením názvu clusteru názvem clusteru a zadejte příkaz:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Po připojení ke clusteru HDInsight spusťte úlohu MapReduce pomocí následujícího příkazu:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount /example/data/gutenberg/davinci.txt /example/data/WordCountOutput
    ```

    Tento příkaz `wordcount` spustí třídu, která `hadoop-mapreduce-examples.jar` je obsažena v souboru. Používá `/example/data/gutenberg/davinci.txt` dokument jako vstup a výstup `/example/data/WordCountOutput`je uložen na .

    > [!NOTE]
    > Další informace o této úloze MapReduce a ukázkových datech naleznete v tématu [Použití mapreduce v Apache Hadoop na HDInsight](hdinsight-use-mapreduce.md).

    Úloha vydává podrobnosti při procesu a vrací informace podobné následujícímu textu po dokončení úlohy:

    ```output
    File Input Format Counters
    Bytes Read=1395666
    File Output Format Counters
    Bytes Written=337623
    ```

1. Po dokončení úlohy vypsat výstupní soubory pomocí následujícího příkazu:

    ```bash
    hdfs dfs -ls /example/data/WordCountOutput
    ```

    Tento příkaz zobrazí `_SUCCESS` dva `part-r-00000`soubory a . Soubor `part-r-00000` obsahuje výstup pro tuto úlohu.

    > [!NOTE]  
    > Některé úlohy MapReduce mohou rozdělit výsledky mezi více souborů **part-r-#######.Some** MapReduce jobs may split the results across multiple part-r-##### files. Pokud ano, použijte příponu ##### k označení pořadí souborů.

1. Chcete-li zobrazit výstup, použijte následující příkaz:

    ```bash
    hdfs dfs -cat /example/data/WordCountOutput/part-r-00000
    ```

    Tento příkaz zobrazí seznam slov obsažených v **wasbs://example/data/gutenberg/davinci.txt** souboru a počet, kolikrát došlo ke každému slovu. Následující text je příkladem dat obsažených v souboru:

    ```output
    wreathed        3
    wreathing       1
    wreaths         1
    wrecked         3
    wrenching       1
    wretched        6
    wriggling       1
    ```

## <a name="next-steps"></a>Další kroky

Jak můžete vidět, příkazy Hadoop poskytují snadný způsob, jak spustit mapreduce úlohy v clusteru HDInsight a potom zobrazit výstup úlohy. Informace o dalších způsobech práce s Hadoopem na HDInsightu:

* [Použití mapreduce na HDInsight Hadoop](hdinsight-use-mapreduce.md)
* [Použití Apache Hive s Apache Hadoop na HDInsight](hdinsight-use-hive.md)
