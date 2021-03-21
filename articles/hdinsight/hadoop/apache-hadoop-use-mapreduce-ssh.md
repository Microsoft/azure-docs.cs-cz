---
title: Připojení MapReduce a SSH pomocí Apache Hadoop – Azure HDInsight
description: Naučte se používat SSH ke spouštění úloh MapReduce pomocí Apache Hadoop ve službě HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 01/10/2020
ms.openlocfilehash: 991b91d1feee185d17bbf01266e0392f347e1a66
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98939636"
---
# <a name="use-mapreduce-with-apache-hadoop-on-hdinsight-with-ssh"></a>Použití MapReduce s Apache Hadoop v HDInsight pomocí SSH

[!INCLUDE [mapreduce-selector](../../../includes/hdinsight-selector-use-mapreduce.md)]

Přečtěte si, jak odesílat MapReduce úlohy z připojení SSH (Secure Shell) do HDInsight.

> [!NOTE]
> Pokud jste již obeznámeni s používáním Apache Hadoopch serverů se systémem Linux, ale jste novinkou ke službě HDInsight, přečtěte si [tipy pro HDInsight se systémem Linux](../hdinsight-hadoop-linux-information.md).

## <a name="prerequisites"></a>Předpoklady

Cluster Apache Hadoop v HDInsight. Další informace najdete v tématu [Vytvoření clusterů Apache Hadoop pomocí Azure Portal](../hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="use-hadoop-commands"></a>Použití příkazů Hadoop

1. Připojte se ke clusteru pomocí [příkazu SSH](../hdinsight-hadoop-linux-use-ssh-unix.md) . Níže uvedený příkaz upravte tak, že ho nahradíte názvem clusteru a pak zadáte tento příkaz:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Po připojení ke clusteru HDInsight pomocí následujícího příkazu spusťte úlohu MapReduce:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount /example/data/gutenberg/davinci.txt /example/data/WordCountOutput
    ```

    Tento příkaz spustí `wordcount` třídu, která je obsažena v `hadoop-mapreduce-examples.jar` souboru. Používá `/example/data/gutenberg/davinci.txt` dokument jako vstup a výstup je uložen na `/example/data/WordCountOutput` .

    > [!NOTE]
    > Další informace o této úloze MapReduce a ukázkových datech najdete v tématu [použití MapReduce v Apache Hadoop ve službě HDInsight](hdinsight-use-mapreduce.md).

    Úloha vygeneruje podrobnosti v průběhu IT procesů a při dokončení úlohy vrátí informace podobné následujícímu textu:

    ```output
    File Input Format Counters
    Bytes Read=1395666
    File Output Format Counters
    Bytes Written=337623
    ```

1. Po dokončení úlohy použijte následující příkaz k vypsání výstupních souborů:

    ```bash
    hdfs dfs -ls /example/data/WordCountOutput
    ```

    Tento příkaz zobrazí dva soubory, `_SUCCESS` a `part-r-00000` . `part-r-00000`Soubor obsahuje výstup pro tuto úlohu.

    > [!NOTE]  
    > Některé úlohy MapReduce mohou rozdělit výsledky napříč více soubory, které jsou **součástí jazyka r-#** # # # #. Pokud ano, použijte k označení pořadí souborů příponu # # # # #.

1. Výstup zobrazíte pomocí následujícího příkazu:

    ```bash
    hdfs dfs -cat /example/data/WordCountOutput/part-r-00000
    ```

    Tento příkaz zobrazí seznam slov, která jsou obsažena v souboru **wasbs://example/data/gutenberg/davinci.txt** a počet pokusů o každé slovo. Následující text je příkladem dat, která jsou obsažena v souboru:

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

Jak vidíte, příkazy Hadoop poskytují snadný způsob, jak spouštět úlohy MapReduce v clusteru HDInsight, a pak zobrazit výstup úlohy. Informace o dalších způsobech, jak můžete pracovat se systémem Hadoop ve službě HDInsight:

* [Použití MapReduce v HDInsight Hadoop](hdinsight-use-mapreduce.md)
* [Použití Apache Hive s Apache Hadoop v HDInsight](hdinsight-use-hive.md)
