---
title: Použití Azure Toolkit for IntelliJ s izolovaným prostorem Hortonworks
description: Naučte se používat nástroje HDInsight v Azure Toolkit for IntelliJ s izolovaným prostorem (Hortonworks).
keywords: nástroje Hadoop, dotaz na podregistry, IntelliJ, Hortonworks Sandbox, sada nástrojů Azure pro IntelliJ
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 05/16/2018
ms.openlocfilehash: 986c2783f3d130114da93406bfaf70d5c038d5ed
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2020
ms.locfileid: "82856146"
---
# <a name="use-hdinsight-tools-for-intellij-with-hortonworks-sandbox"></a>Používání nástrojů HDInsight pro IntelliJ s izolovaným prostorem Hortonworks

Naučte se používat nástroje HDInsight pro IntelliJ k vývoji aplikací Apache Scala a testování aplikací v [Hortonworks izolovaném prostoru (sandbox)](https://hortonworks.com/products/sandbox/) spuštěném ve vašem počítači. 

[INTELLIJ nápad](https://www.jetbrains.com/idea/) je Java INTEGRATED Development Environment (IDE) pro vývoj počítačového softwaru. Po vývoji a testování aplikací v izolovaném prostoru (Hortonworks) můžete aplikace přesunout do služby [Azure HDInsight](apache-hadoop-introduction.md).

## <a name="prerequisites"></a>Požadavky

Než začnete tento článek, musíte mít následující položky:

- Hortonworks data Platform (HDP) 2,4 v izolovaném prostoru Hortonworks běžícím na místním počítači. Pokud chcete nastavit HDP, přečtěte si téma Začínáme [v Apache Hadoop ekosystému s izolovaným prostorem Hadoop na virtuálním počítači](apache-hadoop-emulator-get-started.md). 
    > [!NOTE]
    > Nástroje HDInsight pro IntelliJ byly testovány pouze pomocí HDP 2,4. Pokud chcete získat HDP 2,4, rozbalte **archiv izolovaného prostoru Hortonworks** na [webu Hortonworks pro stahování souborů izolovaného prostoru](https://hortonworks.com/downloads/#sandbox).

- [Java Developer Kit (JDK) verze 1,8 nebo novější](https://aka.ms/azure-jdks). Azure Toolkit for IntelliJ vyžaduje JDK.

- [INTELLIJ myšlenku Community Edition](https://www.jetbrains.com/idea/download) s modulem plug-in [Scala](https://plugins.jetbrains.com/idea/plugin/1347-scala) a modulem plug-in [Azure Toolkit for IntelliJ](https://docs.microsoft.com/azure/developer/java/toolkit-for-intellij) . Nástroje HDInsight pro IntelliJ jsou k dispozici jako součást Azure Toolkit for IntelliJ.

Instalace modulů plug-in:

  1. Otevřete IntelliJ IDEA.
  2. Na **úvodní** stránce vyberte **Konfigurovat**a pak vyberte **moduly plug-in**.
  3. V levém dolním rohu vyberte **nainstalovat modul plug-in JetBrains**.
  4. Pomocí vyhledávací funkce vyhledejte **Scala**a pak vyberte **nainstalovat**.
  5. Pokud chcete dokončit instalaci, vyberte **restartovat INTELLIJ nápad**.
  6. Opakujte kroky 4 a 5 a nainstalujte **Azure Toolkit for IntelliJ**. Další informace najdete v tématu [instalace Azure Toolkit for IntelliJ](https://docs.microsoft.com/azure/azure-toolkit-for-intellij-installation).

## <a name="create-an-apache-spark-scala-application"></a>Vytvoření aplikace Apache Spark Scala

V této části vytvoříte ukázkový projekt Scala pomocí NÁPADu IntelliJ. V další části propojíte IntelliJ nápad s izolovaným prostorem Hortonworks (emulátor) před odesláním projektu.

1. Otevřete IntelliJ nápad na svém počítači. V dialogovém okně **Nový projekt** proveďte tyto kroky:

   1. Vyberte **HDInsight** > **Spark v HDInsight (Scala)**.
   2. V seznamu **Nástroj sestavení** vyberte jednu z následujících možností v závislosti na vašem scénáři:

      * **Maven**: v Průvodci vytvořením projektu Scala je podpora.
      * **SBT**: pro správu závislostí a vytváření projektu Scala.

   ![IntelliJ vytvořit nový projekt Scala](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-create-scala-project.png)

2. Vyberte **Další**.
3. V dalším dialogovém okně **Nový projekt** proveďte následující kroky:

   1. Do pole **název projektu** zadejte název projektu.
   2. Do pole **umístění projektu** zadejte umístění projektu.
   3. Vedle rozevíracího seznamu **sada SDK projektu** vyberte **Nový**, vyberte **JDK**a pak zadejte složku pro Java JDK verze 1,7 nebo novější. Vyberte **Java 1,8** pro cluster Spark 2. x. Vyberte **Java 1,7** pro cluster Spark 1. x. Výchozí umístění je C:\Program Files\Java\jdk1.8. x_xxx.
   4. V rozevíracím seznamu **verze Sparku** se v Průvodci vytvořením projektu Scala integruje správná verze sady Spark SDK a sady Scala SDK. Pokud je verze clusteru Spark nižší než 2.0, vyberte **Spark 1.x**. V opačném případě vyberte **Spark 2.x**. V tomto příkladu se používá Spark 1.6.2 (Scala 2.10.5). Ujistěte se, že používáte úložiště označené **Scala 2.10. x**. Nepoužívejte úložiště označené jako Scala 2.11. x.
    
      ![Vytvoření vlastností projektu IntelliJ Scala](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-create-scala-project-properties.png)


4. Vyberte **Finish** (Dokončit).
5. Pokud zobrazení **projektu** ještě není otevřené, otevřete ho stisknutím **kombinace kláves ALT + 1** .
6. V **Průzkumníku projektu**rozbalte projekt a pak vyberte **Src**.
7. Klikněte pravým tlačítkem na **Src**, přejděte na **Nový**a pak vyberte **Třída Scala**.
8. Zadejte název do pole **Název**. V poli **druh** vyberte **objekt**. Pak vyberte **OK**.

    ![Dialogové okno vytvořit novou třídu Scala](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-create-new-scala-class.png)

9. Do souboru. Scala vložte následující kód:

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

10. V nabídce **sestavení** vyberte **sestavit projekt**. Zajistěte, aby se kompilace úspěšně dokončí.


## <a name="link-to-the-hortonworks-sandbox"></a>Odkaz na izolovaný prostor Hortonworks

Než budete moct připojit k izolovanému prostoru Hortonworks (emulátor), musíte mít existující aplikaci IntelliJ.

Připojení k emulátoru:

1. Otevřete projekt v IntelliJ.
2. V nabídce **zobrazení** vyberte možnost **okna nástrojů**a pak vyberte **Průzkumník Azure**.
3. Rozbalte **Azure**, klikněte pravým tlačítkem na **HDInsight**a pak vyberte **propojit emulátor**.
4. V dialogovém okně **připojit k novému emulátoru** zadejte heslo, které jste nastavili pro kořenový účet Hortonworks izolovaného prostoru (sandboxu). Dále zadejte hodnoty podobné těm, které jsou použity na následujícím snímku obrazovky. Pak vyberte **OK**. 

   ![Dialogové okno propojit nový emulátor](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-link-an-emulator.png)

5. Chcete-li nakonfigurovat emulátor, vyberte možnost **Ano**.

Po úspěšném připojení emulátoru se emulátor (Hortonworks izolovaného prostoru (sandbox) zobrazí v uzlu HDInsight.

## <a name="submit-the-spark-scala-application-to-the-hortonworks-sandbox"></a>Odeslání aplikace Spark Scala do izolovaného prostoru (sandbox) Hortonworks

Po propojení IntelliJ NÁPADu k emulátoru můžete odeslat svůj projekt.

Odeslání projektu do emulátoru:

1. V **Průzkumníku projektu**klikněte pravým tlačítkem na projekt a pak vyberte **Odeslat aplikaci Spark do HDInsight**.
2. Proveďte následující kroky:

    1. V rozevíracím seznamu **cluster Spark (pouze Linux)** vyberte místní izolovaný prostor Hortonworks.
    2. V poli **název hlavní třídy** vyberte nebo zadejte název hlavní třídy. V tomto článku je název **GroupByTest**.

3. Vyberte **Odeslat**. Protokoly odeslání úlohy se zobrazí v okně nástroje pro odeslání Sparku.

## <a name="next-steps"></a>Další kroky

- Naučte se [používat nástroje HDInsight v Azure Toolkit for IntelliJ k vytváření Apache Sparkch aplikací pro cluster HDInsight Spark Linux](../spark/apache-spark-intellij-tool-plugin.md).

- Video o nástrojích HDInsight pro IntelliJ najdete v tématu [představení nástrojů HDInsight pro IntelliJ pro vývoj Apache Spark](https://www.youtube.com/watch?v=YTZzYVgut6c).

- Naučte se [vzdáleně ladit Apache Spark aplikace v clusteru HDInsight s Azure Toolkit for IntelliJ prostřednictvím SSH](../spark/apache-spark-intellij-tool-debug-remotely-through-ssh.md).

- Naučte se [používat nástroje HDInsight v Azure Toolkit for IntelliJ ke vzdálenému ladění Apache Spark aplikací v clusteru HDInsight Spark Linux](../spark/apache-spark-intellij-tool-plugin-debug-jobs-remotely.md).

- Naučte se [používat nástroje HDInsight v Azure Toolkit for Eclipse k vytváření Apache Sparkch aplikací](../spark/apache-spark-eclipse-tool-plugin.md).

