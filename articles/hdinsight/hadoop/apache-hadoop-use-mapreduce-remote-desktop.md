---
title: MapReduce a vzdálené plochy se systémem Hadoop v HDInsight – Azure
description: Další informace o použití vzdálené plochy pro připojení ke clusteru Hadoop v HDInsight a spouštění úloh MapReduce.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/12/2017
ms.author: hrasheed
ROBOTS: NOINDEX
ms.openlocfilehash: ee87bd5743307ea5b9ee3ca1258e8cd985a095c5
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2018
ms.locfileid: "51012924"
---
# <a name="use-mapreduce-in-hadoop-on-hdinsight-with-remote-desktop"></a>Použití MapReduce se v clusteru Hadoop v HDInsight pomocí vzdálené plochy
[!INCLUDE [mapreduce-selector](../../../includes/hdinsight-selector-use-mapreduce.md)]

V tomto článku se dozvíte, jak připojit ke clusteru Hadoop v clusteru HDInsight pomocí vzdálené plochy a pak spusťte úlohy mapreduce je možné pomocí příkazu Hadoop.

> [!IMPORTANT]
> Vzdálená plocha je dostupná pouze na clusterech HDInsight se systémem Windows. HDInsight od verze 3.4 výše používá výhradně operační systém Linux. Další informace najdete v tématu [Vyřazení prostředí HDInsight ve Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).
>
> HDInsight 3.4 nebo větší, přečtěte si téma [použití MapReduce se službou SSH](apache-hadoop-use-mapreduce-ssh.md) informace o připojení ke clusteru HDInsight a spouštění úloh MapReduce.

## <a id="prereq"></a>Požadavky
K dokončení kroků v tomto článku, budete potřebovat následující:

* Cluster Windows systémem HDInsight (Hadoop v HDInsight)
* Klientský počítač s Windows 10, Windows 8 nebo Windows 7

## <a id="connect"></a>Připojte se přes vzdálenou plochu
Povolení vzdálené plochy pro HDInsight cluster a pak k němu připojit pomocí následujících pokynů na adrese [připojit ke clusterům HDInsight pomocí protokolu RDP](../hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp).

## <a id="hadoop"></a>Použití příkazu Hadoop
Pokud jste připojeni k ploše pro HDInsight cluster, použijte následující kroky ke spuštění úlohy MapReduce Hadoop příkazem:

1. Z plochy HDInsight, spusťte **příkazového řádku Hadoopu**. Tím se otevře nový příkazový řádek v **c:\apps\dist\hadoop-&lt;číslo verze >** adresáře.

   > [!NOTE]
   > Číslo verze se změní při aktualizaci Hadoop. **HADOOP_HOME** proměnnou prostředí je možné najít cestu. Například `cd %HADOOP_HOME%` změny adresáře do adresáře systému Hadoop, aniž by bylo potřeba znát číslo verze.
   >
   >
2. Použít **Hadoop** příkaz pro spuštění úlohy MapReduce příklad, použijte následující příkaz:

        hadoop jar hadoop-mapreduce-examples.jar wordcount wasb:///example/data/gutenberg/davinci.txt wasb:///example/data/WordCountOutput

    Tím se spustí **wordcount** třídu, která je součástí **hadoop-mapreduce-examples.jar** soubor v aktuálním adresáři. Jako vstup, použije **wasb://example/data/gutenberg/davinci.txt** dokumentu a výstup je zde: **wasb: / / / Příklad/data/WordCountOutput**.

   > [!NOTE]
   > Další informace o této úlohy MapReduce a ukázková data, najdete v části <a href="hdinsight-use-mapreduce.md">použití MapReduce se v HDInsight Hadoop</a>.
   >
   >
3. Úloha vysílá podrobnosti, jak se zpracovávají, a vrátí informace podobné následujícímu po dokončení úlohy:

        File Input Format Counters
        Bytes Read=1395666
        File Output Format Counters
        Bytes Written=337623
4. Po dokončení úlohy použijte následující příkaz k výpisu výstupní soubory uložené na **wasb://example/data/WordCountOutput**:

        hadoop fs -ls wasb:///example/data/WordCountOutput

    Mělo by se zobrazit dva soubory **_SUCCESS** a **část r-00000**. **Část r-00000** soubor obsahuje výstup pro tuto úlohu.

   > [!NOTE]
   > Některé úlohy mapreduce je možné může rozdělit mezi více výsledky **část. r ###** soubory. Pokud ano, použít ### příponu k určení pořadí souborů.
   >
   >
5. Pokud chcete zobrazit výstup, použijte následující příkaz:

        hadoop fs -cat wasb:///example/data/WordCountOutput/part-r-00000

    Zobrazí se seznam slov, které jsou součástí **wasb://example/data/gutenberg/davinci.txt** souboru spolu s počet, kolikrát se každé slovo došlo k chybě. Následuje příklad dat, která bude obsažena v souboru:

        wreathed        3
        wreathing       1
        wreaths         1
        wrecked         3
        wrenching       1
        wretched        6
        wriggling       1

## <a id="summary"></a>Shrnutí
Jak je vidět, příkaz Hadoop poskytuje snadný způsob, jak spouštět úlohy mapreduce je možné na clusteru HDInsight a pak zobrazte výstup úlohy.

## <a id="nextsteps"></a>Další kroky
Obecné informace o úlohy mapreduce je možné v HDInsight:

* [Použití MapReduce pro Hadoop v HDInsight](hdinsight-use-mapreduce.md)

Informace o jiných způsobech, jakými můžete pracovat s Hadoop v HDInsight:

* [Použití Hivu s Hadoopem v HDInsight](hdinsight-use-hive.md)
* [Použití Pigu se systémem Hadoop v HDInsight](hdinsight-use-pig.md)
