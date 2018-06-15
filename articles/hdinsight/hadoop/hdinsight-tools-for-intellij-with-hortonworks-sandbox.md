---
title: Použijte Azure sadu nástrojů pro IntelliJ s Hortonworks karanténě | Microsoft Docs
description: Naučte se používat nástroje HDInsight pro IntelliJ s Hortonworks karanténě v Azure nástrojů.
keywords: nástroje hadoop hive dotaz, intellij, hortonworks karanténě, azure nástrojů pro intellij
services: HDInsight
documentationcenter: ''
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: b587cc9b-a41a-49ac-998f-b54d6c0bdfe0
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: jgao
ms.openlocfilehash: 59251287f7597955fe22700cc96527b9476753c1
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
ms.locfileid: "34202539"
---
# <a name="use-hdinsight-tools-for-intellij-with-hortonworks-sandbox"></a>Použití nástroje HDInsight pro IntelliJ s Hortonworks karanténě

Naučte se používat nástroje HDInsight pro IntelliJ k vývoji aplikací Apache Scala a poté otestujte aplikace na [Hortonworks karanténě](http://hortonworks.com/products/sandbox/) běžícího na vašem počítači. 

[IntelliJ IDEA](https://www.jetbrains.com/idea/) je Java integrované vývojové prostředí (IDE) pro vývoj počítačového softwaru. Po vývoj a testování vaší aplikace na Hortonworks karanténě, můžete přesunout aplikace, které chcete [Azure HDInsight](apache-hadoop-introduction.md).

## <a name="prerequisites"></a>Požadavky

Před zahájením tohoto kurzu musíte mít tyto položky:

- Hortonworks Data Platform (HDP) 2.4 na Hortonworks karanténě spuštěné v místním počítači. HDP, naleznete v tématu [Začínáme v ekosystému Hadoop s Hadoop izolovaném prostoru na virtuálním počítači](apache-hadoop-emulator-get-started.md). 
    > [!NOTE]
    > Nástroje HDInsight pro IntelliJ byl testován pouze s HDP 2.4. Chcete-li získat HDP 2.4, rozbalte **Hortonworks karanténě archivu** na [Hortonworks karanténě stáhne lokality](http://hortonworks.com/downloads/#sandbox).

- [Java Developer Kit (JDK) verze 1,8 nebo novější](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html). Azure nástrojů pro IntelliJ vyžaduje JDK.

- [Edice community IntelliJ IDEA](https://www.jetbrains.com/idea/download) s [Scala](https://plugins.jetbrains.com/idea/plugin/1347-scala) modulu plug-in a [nástrojů Azure pro IntelliJ](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij) modulu plug-in. Nástroje HDInsight pro IntelliJ je k dispozici jako součást nástrojů Azure pro IntelliJ. 

Instalace modulů plug-in:

  1. Otevřete IntelliJ IDEA.
  2. Na **úvodní** vyberte **konfigurace**a potom vyberte **modulů plug-in**.
  3. V levém dolním rohu vyberte **modul plug-in instalovat JetBrains**.
  4. Můžete vyhledat pomocí funkce vyhledávání **Scala**a potom vyberte **nainstalovat**.
  5. Chcete-li dokončit instalaci, vyberte **restartujte IntelliJ IDEA**.
  6. Opakujte kroky 4 a 5 instalace **nástrojů Azure pro IntelliJ**. Další informace najdete v tématu [instalaci nástrojů Azure pro IntelliJ](https://docs.microsoft.com/azure/azure-toolkit-for-intellij-installation).

## <a name="create-a-spark-scala-application"></a>Vytvoření aplikací Spark Scala

V této části vytvoříte projekt Scala ukázka pomocí IntelliJ IDEA. V další části můžete propojit IntelliJ IDEA Hortonworks karanténě (emulátoru) před odesláním projektu.

1. Otevřete IntelliJ IDEA ve vašem počítači. V **nový projekt** dialogové okno pole, proveďte následující kroky:

   1. Vyberte **HDInsight** > **Spark v HDInsight (Scala)**.
   2. V **nástroj pro sestavení** seznam, vyberte jednu z následujících založený na váš scénář:

    * **Maven**: Podpora průvodce Scala pro vytvoření projektu.
    * **SBT**: pro správu závislosti a sestavování Scala projektu.

   ![Dialogové okno Nový projekt](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-create-scala-project.png)

2. Vyberte **Next** (Další).
3. V dalším **nový projekt** dialogové okno pole, proveďte následující kroky:

    1. V **název projektu** zadejte název projektu.
    2. V **projektu umístění** zadejte umístění projektu.
    3. Vedle položky **SDK projektu** rozevíracího seznamu vyberte **nový**, vyberte **JDK**, a pak určete složku pro JDK Java verze 1.7 nebo novější. Vyberte **Java 1.8** 2.x clusteru Spark. Vyberte **Java 1.7** 1.x clusteru Spark. Výchozí umístění je C:\Program Files\Java\jdk1.8.x_xxx.
    4. V **Spark verze** rozevíracího seznamu, Průvodce vytvořením projektu Scala integruje správnou verzi sady SDK Spark a Scala SDK. Pokud verze clusteru Spark je starší než 2.0, vyberte **Spark 1.x**. Jinak vyberte možnost **Spark2.x**. Tento příklad používá Spark 1.6.2 (Scala 2.10.5). Ujistěte se, že používáte úložiště označena **Scala 2.10.x**. Nepoužívejte úložiště označena Scala 2.11.x.
    
    ![Vytvoření IntelliJ Scala vlastnosti projektu](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-create-scala-project-properties.png)


4. Vyberte **Finish** (Dokončit).
5. Pokud **projektu** zobrazení ještě není otevřené, stiskněte **Alt + 1** ho otevřete.
6. V **Project Exploreru**, rozbalte projekt a potom vyberte **src**.
7. Klikněte pravým tlačítkem na **src**, přejděte na příkaz **nový**a potom vyberte **Scala třída**.
8. V **název** pole, zadejte název. V **druhu** vyberte **objekt**. Pak vyberte **OK**.

    ![Dialogové okno Vytvořit novou třídu Scala](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-create-new-scala-class.png)

9. V souboru .scala vložte následující kód:

        import java.util.Random
        import org.apache.spark.{SparkConf, SparkContext}
        import org.apache.spark.SparkContext._

        /**
        * Usage: GroupByTest [numMappers] [numKVPairs] [valSize] [numReducers]
        */
        object GroupByTest {
            def main(args: Array[String]) {
                val sparkConf = new SparkConf().setAppName("GroupBy Test")
                var numMappers = 3
                var numKVPairs = 10
                var valSize = 10
                var numReducers = 2

                val sc = new SparkContext(sparkConf)

                val pairs1 = sc.parallelize(0 until numMappers, numMappers).flatMap { p =>
                val ranGen = new Random
                var arr1 = new Array[(Int, Array[Byte])](numKVPairs)
                for (i <- 0 until numKVPairs) {
                    val byteArr = new Array[Byte](valSize)
                    ranGen.nextBytes(byteArr)
                    arr1(i) = (ranGen.nextInt(Int.MaxValue), byteArr)
                }
                arr1
                }.cache
                // Enforce that everything has been calculated and in cache.
                pairs1.count

                println(pairs1.groupByKey(numReducers).count)
            }
        }

10. Na **sestavení** nabídce vyberte možnost **sestavení projektu**. Ujistěte se, že kompilace dokončen úspěšně.


## <a name="link-to-the-hortonworks-sandbox"></a>Odkaz na Hortonworks karanténě

Předtím, než můžete propojit Hortonworks karanténě (emulátoru), musí mít existující aplikaci IntelliJ.

Propojit s emulátor:

1. Otevřete projekt v IntelliJ.
2. Na **zobrazení** nabídce vyberte možnost **nástroje Windows**a potom vyberte **Azure Explorer**.
3. Rozbalte položku **Azure**, klikněte pravým tlačítkem na **HDInsight**a potom vyberte **odkaz emulátor**.
4. V **odkaz A nový emulátor** dialogové okno pole, zadejte heslo, které jste nastavili pro kořenový účet Hortonworks karanténě. Potom zadejte hodnoty, které jsou podobné těm, které jsou používány na následujícím snímku obrazovky. Pak vyberte **OK**. 

   ![Dialogové okno Nový emulátor odkaz](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-link-an-emulator.png)

5. Chcete-li nakonfigurovat emulátoru, vyberte **Ano**.

Když na emulátoru úspěšně připojí, emulátoru (Hortonworks karanténě) je uvedena na uzlu HDInsight.

## <a name="submit-the-spark-scala-application-to-the-hortonworks-sandbox"></a>Odesílání aplikací Spark Scala k izolovanému prostoru Hortonworks

Po propojení IntelliJ IDEA pro emulátor, můžete odeslat projektu.

K odeslání projektu na emulátoru:

1. V **Project Exploreru**, klikněte pravým tlačítkem na projekt a pak vyberte **aplikace odeslání Spark na HDInsight**.
2. Proveďte následující kroky:

    1. V **cluster Spark (pouze Linux)** rozevíracího seznamu vyberte vaše místní Hortonworks karanténě.
    2. V **hlavní název třídy** pole, vyberte nebo zadejte název hlavní třídy. V tomto kurzu je název **GroupByTest**.

3. Vyberte **odeslání**. Protokoly odeslání úlohy se zobrazí v okně Nástroj odeslání Spark.

## <a name="next-steps"></a>Další postup

- Zjistěte, jak [vytvoření aplikací Spark pro cluster služby HDInsight Spark Linux pomocí nástroje HDInsight v Azure nástrojů pro IntelliJ](../spark/apache-spark-intellij-tool-plugin.md).

- Video o nástroje HDInsight pro IntelliJ najdete v tématu [zavést nástroje HDInsight pro IntelliJ pro vývoj Spark](https://www.youtube.com/watch?v=YTZzYVgut6c).

- Zjistěte, jak [vzdálené ladění aplikací Spark v clusteru HDInsight pomocí sady Azure Toolkit pro IntelliJ prostřednictvím SSH](../spark/apache-spark-intellij-tool-debug-remotely-through-ssh.md).

- Zjistěte, jak [použití nástrojů HDInsight v Azure nástrojů pro IntelliJ k ladění aplikací Spark vzdáleně v clusteru HDInsight Spark Linux](../spark/apache-spark-intellij-tool-plugin-debug-jobs-remotely.md).

- Zjistěte, jak [pomocí nástrojů HDInsight v Azure nástrojů pro Eclipse k vytvoření aplikací Spark](../spark/apache-spark-eclipse-tool-plugin.md).

- Video o nástroje HDInsight pro prostředí Eclipse, najdete v [pomocí nástrojů HDInsight pro Eclipse k vytvoření aplikací Spark](https://mix.office.com/watch/1rau2mopb6fha).
