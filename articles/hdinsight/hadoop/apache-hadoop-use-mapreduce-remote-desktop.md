---
title: MapReduce a Vzdálená plocha s Hadoop v HDInsight - Azure | Microsoft Docs
description: Naučte se používat vzdálené plochy pro připojení k systému Hadoop v HDInsight a spuštění úloh MapReduce.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 9d3a7b34-7def-4c2e-bb6c-52682d30dee8
ms.service: hdinsight
ms.devlang: na
ms.topic: conceptual
ms.date: 01/12/2017
ms.author: larryfr
ROBOTS: NOINDEX
ms.openlocfilehash: eb59a510085a9f08e63f17cec1de2044905f914a
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="use-mapreduce-in-hadoop-on-hdinsight-with-remote-desktop"></a>Používání nástroje MapReduce v Hadoop v HDInsight pomocí vzdálené plochy
[!INCLUDE [mapreduce-selector](../../../includes/hdinsight-selector-use-mapreduce.md)]

V tomto článku se dozvíte, jak se připojit k systému Hadoop v clusteru HDInsight pomocí vzdálené plochy a pak spusťte úloh MapReduce pomocí příkazu Hadoop.

> [!IMPORTANT]
> Vzdálená plocha je dostupná pouze na clustery HDInsight se systémem Windows. HDInsight od verze 3.4 výše používá výhradně operační systém Linux. Další informace najdete v tématu [Vyřazení prostředí HDInsight ve Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).
>
> HDInsight 3.4 nebo větší, projděte si téma [používání MapReduce s SSH](apache-hadoop-use-mapreduce-ssh.md) informace o připojení ke clusteru HDInsight a spuštění úloh MapReduce.

## <a id="prereq"></a>Požadavky
Pokud chcete provést kroky v tomto článku, budete potřebovat následující:

* Cluster HDInsight se systémem Windows (Hadoop v HDInsight)
* Klientský počítač se systémem Windows 10, Windows 8 nebo Windows 7

## <a id="connect"></a>Připojit pomocí vzdálené plochy
Povolení vzdálené plochy pro HDInsight cluster a pak připojit pomocí pokynů uvedených v [připojení ke clusterům HDInsight pomocí protokolu RDP](../hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp).

## <a id="hadoop"></a>Použijte příkaz Hadoop
Když se připojíte k ploše pro HDInsight cluster, použijte následující kroky a spusťte úlohu MapReduce pomocí příkazu Hadoop:

1. Z plochy HDInsight, spusťte **Hadoop příkazového řádku**. Otevře se nové příkazový řádek v **c:\apps\dist\hadoop-&lt;číslo verze >** adresáře.

   > [!NOTE]
   > Číslo verze se změní, když se aktualizuje Hadoop. **HADOOP_HOME** proměnnou prostředí můžete použít k vyhledání cestu. Například `cd %HADOOP_HOME%` změny adresáře do adresáře Hadoop, aniž by bylo potřeba znát číslo verze.
   >
   >
2. Použít **Hadoop** příkazu Spustit úlohu MapReduce příklad, použijte následující příkaz:

        hadoop jar hadoop-mapreduce-examples.jar wordcount wasb:///example/data/gutenberg/davinci.txt wasb:///example/data/WordCountOutput

    Tím se spustí **wordcount** třídy, která je součástí **hadoop-mapreduce-examples.jar** soubor v aktuálním adresáři. Jako vstup, použije **wasb://example/data/gutenberg/davinci.txt** dokumentu a výstup je uložený v: **wasb: / / / Příklad/data/WordCountOutput**.

   > [!NOTE]
   > Další informace o této úlohy MapReduce a data příklad najdete v tématu <a href="hdinsight-use-mapreduce.md">použití MapReduce v HDInsight Hadoop</a>.
   >
   >
3. Úloha vysílá podrobnosti, jako je zpracován a vrátí informace podobná následující po dokončení úlohy:

        File Input Format Counters
        Bytes Read=1395666
        File Output Format Counters
        Bytes Written=337623
4. Po dokončení úlohy použijte následující příkaz k zobrazení seznamu výstupní soubory uložené v **wasb://example/data/WordCountOutput**:

        hadoop fs -ls wasb:///example/data/WordCountOutput

    To by měl zobrazit dva soubory, **_SUCCESS** a **část r-00000**. **Část r-00000** soubor obsahuje výstup pro tuto úlohu.

   > [!NOTE]
   > Některé úlohy MapReduce může rozdělit do několika výsledky **část r-###** soubory. Pokud ano, použít ### příponu označte pořadí souborů.
   >
   >
5. Chcete-li zobrazit výstup, použijte následující příkaz:

        hadoop fs -cat wasb:///example/data/WordCountOutput/part-r-00000

    Zobrazí se seznam slova, která jsou součástí **wasb://example/data/gutenberg/davinci.txt** souboru, společně s počet jednotlivých slov došlo k chybě. Následuje příklad dat, která bude obsažená v souboru:

        wreathed        3
        wreathing       1
        wreaths         1
        wrecked         3
        wrenching       1
        wretched        6
        wriggling       1

## <a id="summary"></a>Shrnutí
Jak vidíte, příkaz Hadoop poskytuje snadný způsob, jak spouštět úlohy MapReduce v clusteru HDInsight a pak zobrazit výstup úlohy.

## <a id="nextsteps"></a>Další kroky
Obecné informace o úloh MapReduce v HDInsight:

* [Používání nástroje MapReduce systému HDInsight Hadoop](hdinsight-use-mapreduce.md)

Informace o jiných způsobech můžete pracovat s Hadoop v HDInsight:

* [Použijte Hive s Hadoop v HDInsight](hdinsight-use-hive.md)
* [Použijte Pig s Hadoop v HDInsight](hdinsight-use-pig.md)
