---
title: Použití sady Nástrojů Azure pro IntelliJ s sandboxem Hortonworks
description: Přečtěte si, jak používat nástroje HDInsight v sadě nástrojů Azure toolkit pro IntelliJ s hortonworks sandboxem.
keywords: hadoop nástroje, dotaz úlu,intellij,hortonworks pískoviště, azurová sada nástrojů pro intellij
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 05/16/2018
ms.openlocfilehash: 65a15a8506b88e95e14af8c87bcbe33087301519
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75647828"
---
# <a name="use-hdinsight-tools-for-intellij-with-hortonworks-sandbox"></a>Použití nástrojů HDInsight pro IntelliJ s sandboxem Hortonworks

Naučte se používat nástroje HDInsight pro IntelliJ k vývoji aplikací Apache Scala a potom otestovat aplikace v [sandboxu Hortonworks spuštěném](https://hortonworks.com/products/sandbox/) v počítači. 

[IntelliJ IDEA](https://www.jetbrains.com/idea/) je java integrované vývojové prostředí (IDE) pro vývoj počítačového softwaru. Po vývoji a testování aplikací v sandboxu Hortonworks můžete aplikace přesunout do [Azure HDInsight](apache-hadoop-introduction.md).

## <a name="prerequisites"></a>Požadavky

Než začnete tento článek, musíte mít následující položky:

- Hortonworks Data Platform (HDP) 2.4 na Hortonworks Sandbox běží na místním počítači. Pokud chcete nastavit HDP, přečtěte si informace [o tom, jak začít v ekosystému Apache Hadoop s pískovištěm Hadoop na virtuálním počítači](apache-hadoop-emulator-get-started.md). 
    > [!NOTE]
    > Nástroje HDInsight pro IntelliJ byly testovány pouze s HDP 2.4. Chcete-li získat HDP 2.4, rozbalte **Hortonworks Sandbox Archive** na [Hortonworks Sandbox ke stažení stránky](https://hortonworks.com/downloads/#sandbox).

- [Java Developer Kit (JDK) verze 1.8 nebo novější](https://aka.ms/azure-jdks). Azure Toolkit pro IntelliJ vyžaduje JDK.

- [Komunitní edice IntelliJ IDEA](https://www.jetbrains.com/idea/download) s modulem plug-in [Scala](https://plugins.jetbrains.com/idea/plugin/1347-scala) a [sadou Azure Toolkit for IntelliJ.](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij) Nástroje HDInsight pro IntelliJ jsou k dispozici jako součást sady Nástrojů Azure pro IntelliJ. 

Instalace modulů plug-in:

  1. Otevřete IntelliJ IDEA.
  2. Na **úvodní** stránce vyberte **Konfigurovat**a pak vyberte **Moduly plug-in**.
  3. V levém dolním rohu vyberte **modul plug-in Instalovat jetBrains**.
  4. Pomocí funkce vyhledávání vyhledejte **Scalu**a pak vyberte **Instalovat**.
  5. Chcete-li instalaci dokončit, vyberte **možnost Restartovat aplikaci IntelliJ IDEA**.
  6. Opakováním kroků 4 a 5 nainstalujte **sadu Nástrojů Azure pro IntelliJ**. Další informace naleznete [v tématu Instalace sady Nástrojů Azure pro IntelliJ](https://docs.microsoft.com/azure/azure-toolkit-for-intellij-installation).

## <a name="create-an-apache-spark-scala-application"></a>Vytvoření aplikace Apache Spark Scala

V této části vytvoříte ukázkový projekt Scala pomocí IntelliJ IDEA. V další části propojíte IntelliJ IDEA s hortonworks sandbox (emulátor) před odesláním projektu.

1. Otevřete intelliJ IDEA v počítači. V dialogovém okně **Nový projekt** proveďte tyto kroky:

   1. Vyberte **HDInsight** > **Spark na HDInsight (Scala)**.
   2. V seznamu **nástrojů sestavení** vyberte jednu z následujících možností na základě vašeho scénáře:

      * **Maven**: Pro podporu průvodce vytvořením projektu Scala.
      * **SBT**: Pro správu závislostí a vytváření pro projekt Scala.

   ![Intellij vytvořit nový projekt Scala](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-create-scala-project.png)

2. Vyberte **další**.
3. V dalším dialogovém okně **Nový projekt** proveďte následující kroky:

   1. Do pole **Název projektu** zadejte název projektu.
   2. Do pole **Umístění projektu** zadejte umístění projektu.
   3. Vedle rozevíracího seznamu **Sady Project SDK** vyberte **Nový**, vyberte **JDK**a zadejte složku pro Java JDK verze 1.7 nebo novější. Pro cluster Spark 2.x vyberte **Java 1.8.** Pro cluster Spark 1.x vyberte **Java 1.7.** Výchozí umístění je C:\Program Files\Java\jdk1.8.x_xxx.
   4. V rozevíracím seznamu **verze Spark** integruje Průvodce vytvořením projektu Scala správnou verzi pro Spark SDK a Scala SDK. Pokud je verze clusteru Spark nižší než 2.0, vyberte **Spark 1.x**. V opačném případě vyberte **Spark 2.x**. Tento příklad používá Spark 1.6.2 (Scala 2.10.5). Ujistěte se, že používáte repozitář označený **Scala 2.10.x**. Nepoužívejte repozitář označený Scala 2.11.x.
    
      ![Vytvoření vlastností projektu IntelliJ Scala](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-create-scala-project-properties.png)


4. Vyberte **Finish** (Dokončit).
5. Pokud zobrazení **Project** ještě není otevřené, otevřete ho stisknutím **klávesalt+1.**
6. V **Průzkumníkovi projektu**rozbalte projekt a vyberte **src**.
7. Klepněte pravým tlačítkem myši na **položku src**, přejděte na **nový**a pak vyberte **třídu Scala**.
8. Zadejte název do pole **Název**. V poli **DRUH** vyberte **Objekt**. Potom vyberte **OK**.

    ![Dialogové okno Vytvořit novou třídu Scaly](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-create-new-scala-class.png)

9. Do souboru .scala vložte následující kód:

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

10. V nabídce **Sestavení** vyberte **Build project**. Ujistěte se, že kompilace úspěšně dokončí.


## <a name="link-to-the-hortonworks-sandbox"></a>Odkaz na hortonworks pískoviště

Před propojením s renesorištěm Hortonworks (emulátorem) musíte mít existující aplikaci IntelliJ.

Propojení s emulátorem:

1. Otevřete projekt v aplikaci IntelliJ.
2. V nabídce **View** vyberte **Nástroje Windows**a pak vyberte **Průzkumníka Azure**.
3. Rozbalte **Azure**, klikněte pravým tlačítkem myši na **HDInsight**a vyberte **propojit emulátor**.
4. V dialogovém okně **Nový emulátor propojení** zadejte heslo, které jste nastavili pro kořenový účet pískoviště Hortonworks. Dále zadejte hodnoty podobné těm, které byly použity na následujícím snímku obrazovky. Potom vyberte **OK**. 

   ![Dialogové okno Propojit nový emulátor](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-link-an-emulator.png)

5. Chcete-li nakonfigurovat emulátor, vyberte **možnost Ano**.

Když je emulátor úspěšně připojen, emulátor (Hortonworks Sandbox) je uveden v uzlu HDInsight.

## <a name="submit-the-spark-scala-application-to-the-hortonworks-sandbox"></a>Odeslání aplikace Spark Scala do sandboxu Hortonworks

Po propojení IntelliJ IDEA s emulátorem můžete odeslat svůj projekt.

Odeslání projektu emulátoru:

1. V **Průzkumníkovi projektu**klikněte pravým tlačítkem myši na projekt a potom vyberte **Odeslat aplikaci Spark do HDInsightu**.
2. Proveďte následující kroky:

    1. V rozevíracím seznamu **clusteru Spark (pouze Linux)** vyberte místní sandbox Hortonworks.
    2. Do pole **Název hlavní třídy** vyberte nebo zadejte název hlavní třídy. Pro tento článek je název **GroupByTest**.

3. Vyberte **Odeslat**. Protokoly odeslání úlohy jsou zobrazeny v okně nástroje pro odesílání spark.

## <a name="next-steps"></a>Další kroky

- Naučte se [používat nástroje HDInsight v Sadě nástrojů Azure pro IntelliJ k vytváření aplikací Apache Spark pro cluster HDInsight Spark Linux](../spark/apache-spark-intellij-tool-plugin.md).

- Video o nástrojích HDInsight tools for IntelliJ [najdete v tématu Introduce HDInsight Tools for IntelliJ for Apache Spark development](https://www.youtube.com/watch?v=YTZzYVgut6c).

- Naučte se [vzdáleně ladit aplikace Apache Spark v clusteru HDInsight pomocí Azure Toolkit for IntelliJ prostřednictvím SSH](../spark/apache-spark-intellij-tool-debug-remotely-through-ssh.md).

- Naučte se [používat nástroje HDInsight v Sadě nástrojů Azure pro IntelliJ k vzdálenému ladění aplikací Apache Spark v clusteru HDInsight Spark Linux](../spark/apache-spark-intellij-tool-plugin-debug-jobs-remotely.md).

- Naučte se [používat nástroje HDInsight v sadě nástrojů Azure Toolkit for Eclipse k vytváření aplikací Apache Spark](../spark/apache-spark-eclipse-tool-plugin.md).

