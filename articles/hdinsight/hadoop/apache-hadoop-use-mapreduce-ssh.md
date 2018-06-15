---
title: Připojení MapReduce a SSH s Hadoop v HDInsight - Azure | Microsoft Docs
description: Další informace o použití SSH ke spuštění úloh MapReduce pomocí Hadoop v HDInsight.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: cgronlunb
editor: cgronlun
tags: azure-portal
ms.assetid: 844678ba-1e1f-4fda-b9ef-34df4035d547
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/10/2018
ms.author: larryfr
ms.openlocfilehash: 67e1bf6cee04eda51f5dbfc51a95614347fc2b7f
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
ms.locfileid: "31398995"
---
# <a name="use-mapreduce-with-hadoop-on-hdinsight-with-ssh"></a>Používání nástroje MapReduce s Hadoop v HDInsight pomocí protokolu SSH

[!INCLUDE [mapreduce-selector](../../../includes/hdinsight-selector-use-mapreduce.md)]

Zjistěte, jak k odesílání úloh MapReduce připojení Secure Shell (SSH) do HDInsight.

> [!NOTE]
> Pokud jste již obeznámeni s pomocí serverů se systémem Linux Hadoop, ale jsou pro vás nové do HDInsight, najdete v části [HDInsight se systémem Linux tipy](../hdinsight-hadoop-linux-information.md).

## <a id="prereq"></a>Požadavky

* Cluster HDInsight se systémem Linux (Hadoop v HDInsight)

  > [!IMPORTANT]
  > HDInsight od verze 3.4 výše používá výhradně operační systém Linux. Další informace najdete v tématu [Vyřazení prostředí HDInsight ve Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

* Klientem SSH. Další informace najdete v tématu [použití SSH s HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md)

## <a id="ssh"></a>Připojení pomocí protokolu SSH

Připojte se ke clusteru pomocí protokolu SSH. Například následující příkaz se připojí ke clusteru s názvem **myhdinsight** jako **sshuser** účet:

```bash
ssh sshuser@myhdinsight-ssh.azurehdinsight.net
```

**Pokud používáte klíč certifikátu pro ověřování SSH**, budete možná muset zadat umístění privátní klíč klientského systému, například:

```bash
ssh -i ~/mykey.key sshuser@myhdinsight-ssh.azurehdinsight.net
```

**Pokud použijete heslo pro ověřování SSH**, budete muset zadat heslo po zobrazení výzvy.

Další informace o používání SSH s HDInsight, naleznete v části [použití SSH s HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a id="hadoop"></a>Použít příkazy Hadoop

1. Po připojení ke clusteru HDInsight, použijte následující příkaz spusťte úlohu MapReduce:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount /example/data/gutenberg/davinci.txt /example/data/WordCountOutput
    ```

    Tento příkaz spustí `wordcount` třídy, která je součástí `hadoop-mapreduce-examples.jar` souboru. Použije `/example/data/gutenberg/davinci.txt` dokumentu jako vstup a výstup je uloženo na `/example/data/WordCountOutput`.

    > [!NOTE]
    > Další informace o této úlohy MapReduce a data příklad najdete v tématu [použití MapReduce v Hadoop v HDInsight](hdinsight-use-mapreduce.md).

2. Úloha vysílá podrobnosti zpracovává a vrátí informace podobná následující text po dokončení úlohy:

        File Input Format Counters
        Bytes Read=1395666
        File Output Format Counters
        Bytes Written=337623

3. Po dokončení úlohy, použijte následující příkaz k zobrazení seznamu výstupní soubory:

    ```bash
    hdfs dfs -ls /example/data/WordCountOutput
    ```

    Tento příkaz zobrazí dva soubory, `_SUCCESS` a `part-r-00000`. `part-r-00000` Soubor obsahuje výstup pro tuto úlohu.

    > [!NOTE]
    > Některé úlohy MapReduce může rozdělit do několika výsledky **část r-###** soubory. Pokud ano, použít ### příponu označte pořadí souborů.

4. Chcete-li zobrazit výstup, použijte následující příkaz:

    ```bash
    hdfs dfs -cat /example/data/WordCountOutput/part-r-00000
    ```

    Tento příkaz zobrazí seznam slova, která jsou součástí **wasb://example/data/gutenberg/davinci.txt** souboru a počet jednotlivých slov došlo k chybě. Tento text je příklad dat, která je obsažená v souboru:

        wreathed        3
        wreathing       1
        wreaths         1
        wrecked         3
        wrenching       1
        wretched        6
        wriggling       1

## <a id="summary"></a>Shrnutí

Jak vidíte, poskytují příkazy Hadoop snadný způsob, jak spouštět úlohy MapReduce v clusteru služby HDInsight a pak zobrazit výstup úlohy.

## <a id="nextsteps"></a>Další kroky

Obecné informace o úloh MapReduce v HDInsight:

* [Používání nástroje MapReduce systému HDInsight Hadoop](hdinsight-use-mapreduce.md)

Informace o jiných způsobech můžete pracovat s Hadoop v HDInsight:

* [Použijte Hive s Hadoop v HDInsight](hdinsight-use-hive.md)
* [Použijte Pig s Hadoop v HDInsight](hdinsight-use-pig.md)
