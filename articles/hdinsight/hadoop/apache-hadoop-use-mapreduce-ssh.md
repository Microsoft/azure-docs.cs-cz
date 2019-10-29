---
title: Připojení MapReduce a SSH pomocí Apache Hadoop – Azure HDInsight
description: Naučte se používat SSH ke spouštění úloh MapReduce pomocí Apache Hadoop ve službě HDInsight.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/10/2018
ms.author: hrasheed
ms.openlocfilehash: b4075de1a184896d598c11d09ae2b2bda5e257ed
ms.sourcegitcommit: 38251963cf3b8c9373929e071b50fd9049942b37
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/29/2019
ms.locfileid: "73044513"
---
# <a name="use-mapreduce-with-apache-hadoop-on-hdinsight-with-ssh"></a>Použití MapReduce s Apache Hadoop v HDInsight pomocí SSH

[!INCLUDE [mapreduce-selector](../../../includes/hdinsight-selector-use-mapreduce.md)]

Přečtěte si, jak odesílat MapReduce úlohy z připojení SSH (Secure Shell) do HDInsight.

> [!NOTE]
> Pokud jste již obeznámeni s používáním Apache Hadoopch serverů se systémem Linux, ale jste novinkou ke službě HDInsight, přečtěte si [tipy pro HDInsight se systémem Linux](../hdinsight-hadoop-linux-information.md).

## <a id="prereq"></a>Požadavky

* Cluster HDInsight se systémem Linux (Hadoop v HDInsight)

* Klient SSH. Další informace najdete v tématu [Použití SSH se službou HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md) .

## <a id="ssh"></a>Připojit pomocí SSH

Připojte se ke clusteru pomocí SSH. Například následující příkaz se připojí ke clusteru s názvem **myhdinsight** jako účet **sshuser** :

```bash
ssh sshuser@myhdinsight-ssh.azurehdinsight.net
```

**Pokud používáte klíč certifikátu pro ověřování SSH**, možná budete muset zadat umístění privátního klíče v klientském systému, například:

```bash
ssh -i ~/mykey.key sshuser@myhdinsight-ssh.azurehdinsight.net
```

**Pokud používáte heslo pro ověřování SSH**, po zobrazení výzvy musíte zadat heslo.

Další informace o použití SSH se službou HDInsight najdete v tématu [Použití SSH se službou HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a id="hadoop"></a>Použití příkazů Hadoop

1. Po připojení ke clusteru HDInsight pomocí následujícího příkazu spusťte úlohu MapReduce:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount /example/data/gutenberg/davinci.txt /example/data/WordCountOutput
    ```

    Tento příkaz spustí třídu `wordcount`, která je obsažena v souboru `hadoop-mapreduce-examples.jar`. Používá jako vstup dokument `/example/data/gutenberg/davinci.txt` a výstup je uložený na `/example/data/WordCountOutput`.

    > [!NOTE]
    > Další informace o této úloze MapReduce a ukázkových datech najdete v tématu [použití MapReduce v Apache Hadoop ve službě HDInsight](hdinsight-use-mapreduce.md).

2. Úloha vygeneruje podrobnosti v průběhu IT procesů a při dokončení úlohy vrátí informace podobné následujícímu textu:

        File Input Format Counters
        Bytes Read=1395666
        File Output Format Counters
        Bytes Written=337623

3. Po dokončení úlohy použijte následující příkaz k vypsání výstupních souborů:

    ```bash
    hdfs dfs -ls /example/data/WordCountOutput
    ```

    Tento příkaz zobrazí dva soubory `_SUCCESS` a `part-r-00000`. `part-r-00000` soubor obsahuje výstup pro tuto úlohu.

    > [!NOTE]  
    > Některé úlohy MapReduce mohou rozdělit výsledky napříč více soubory, které jsou **součástí jazyka r-#** # # # #. Pokud ano, použijte k označení pořadí souborů příponu # # # # #.

4. Výstup zobrazíte pomocí následujícího příkazu:

    ```bash
    hdfs dfs -cat /example/data/WordCountOutput/part-r-00000
    ```

    Tento příkaz zobrazí seznam slov, která jsou obsažena v souboru **wasb://example/data/Gutenberg/DaVinci.txt** , a počet, kolikrát se každé slovo objevilo. Následující text je příkladem dat, která jsou obsažena v souboru:

        wreathed        3
        wreathing       1
        wreaths         1
        wrecked         3
        wrenching       1
        wretched        6
        wriggling       1

## <a id="summary"></a>Shrnutí

Jak vidíte, příkazy Hadoop poskytují snadný způsob, jak spouštět úlohy MapReduce v clusteru HDInsight, a pak zobrazit výstup úlohy.

## <a id="nextsteps"></a>Další kroky

Obecné informace o úlohách MapReduce v HDInsight:

* [Použití MapReduce v HDInsight Hadoop](hdinsight-use-mapreduce.md)

Informace o dalších způsobech, jak můžete pracovat se systémem Hadoop ve službě HDInsight:

* [Použití Apache Hive s Apache Hadoop v HDInsight](hdinsight-use-hive.md)
* [Použití systému Apache prasete s Apache Hadoop v HDInsight](hdinsight-use-pig.md)
