---
title: Použití sady Azure Toolkit pro IntelliJ s Hortonworks Sandbox
description: Zjistěte, jak pomocí nástrojů HDInsight v sadě Azure Toolkit pro IntelliJ s Hortonworks Sandbox.
keywords: nástroje hadoop, hive query, intellij, hortonworks sandbox, sada azure toolkit pro intellij
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: hrasheed
ms.openlocfilehash: 2f09bd7055b987a8647a24d4ebb6ea5a27888a53
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2018
ms.locfileid: "51015583"
---
# <a name="use-hdinsight-tools-for-intellij-with-hortonworks-sandbox"></a>Použití nástrojů HDInsight pro IntelliJ s Hortonworks Sandbox

Naučte se používat nástroje HDInsight pro IntelliJ k vývoji aplikací Apache Scala a potom otestujte aplikace na [Hortonworks Sandbox](http://hortonworks.com/products/sandbox/) spuštěné v počítači. 

[IntelliJ IDEA](https://www.jetbrains.com/idea/) je Java integrované vývojové prostředí (IDE) pro vývoj softwaru pro počítače. Po vývoj a testování aplikací na Hortonworks Sandbox, můžete aplikace, které chcete přesunout [Azure HDInsight](apache-hadoop-introduction.md).

## <a name="prerequisites"></a>Požadavky

Před zahájením tohoto kurzu musíte mít tyto položky:

- Hortonworks Data Platform (HDP) 2.4 na Hortonworks Sandbox běží na místním počítači. Pokud chcete nastavit HDP, přečtěte si [začít pracovat v ekosystému Hadoop s Hadoop sandbox na virtuálním počítači](apache-hadoop-emulator-get-started.md). 
    > [!NOTE]
    > Nástroje HDInsight pro IntelliJ prošel testováním pouze s HDP 2.4. Chcete-li získat HDP 2.4, rozbalte **Hortonworks Sandbox archivu** na [Hortonworks Sandbox soubory ke stažení webu](http://hortonworks.com/downloads/#sandbox).

- [Java Developer Kit (JDK) verze 1.8 nebo vyšší](https://aka.ms/azure-jdks). Sada Azure Toolkit pro IntelliJ vyžaduje sadu JDK.

- [Edice community IntelliJ IDEA](https://www.jetbrains.com/idea/download) s [Scala](https://plugins.jetbrains.com/idea/plugin/1347-scala) modulu plug-in a [sady Azure Toolkit pro IntelliJ](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij) modulu plug-in. Nástroje HDInsight pro IntelliJ je k dispozici jako součást sady Azure Toolkit pro IntelliJ. 

Chcete-li nainstalovat moduly plug-in:

  1. Otevřete IntelliJ IDEA.
  2. Na **úvodní** stránce **konfigurovat**a pak vyberte **moduly plug-in**.
  3. V levém dolním rohu vyberte **JetBrains nainstalovat modul plug-in**.
  4. Můžete vyhledat pomocí funkce vyhledávání **Scala**a pak vyberte **nainstalovat**.
  5. K dokončení instalace, vybrat **restartovat IntelliJ IDEA**.
  6. Opakujte kroky 4 a 5 a nainstalujte **sady Azure Toolkit pro IntelliJ**. Další informace najdete v tématu [instalace sady Azure Toolkit pro IntelliJ](https://docs.microsoft.com/azure/azure-toolkit-for-intellij-installation).

## <a name="create-a-spark-scala-application"></a>Vytvoření aplikací Spark Scala

V této části vytvoříte ukázkový projekt Scala pomocí IntelliJ IDEA. V další části propojíte IntelliJ IDEA Hortonworks Sandbox (emulátor) před odesláním projektu.

1. Otevřete IntelliJ IDEA ve vašem počítači. V **nový projekt** dialogové okno pole, proveďte následující kroky:

   1. Vyberte **HDInsight** > **Spark on HDInsight (Scala)** (Spark v HDInsight (Scala)).
   2. V **nástroj pro sestavení** seznamu vyberte jednu z těchto možností podle vašemu scénáři:

    * **Maven**: podpora Scala pro vytvoření projektu průvodce.
    * **SBT**: pro správu závislostí a vývoj aplikací pro Scala projektu.

   ![Dialogové okno New Project (Nový projekt)](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-create-scala-project.png)

2. Vyberte **Další**.
3. V dalším **nový projekt** dialogové okno pole, proveďte následující kroky:

    1. V **název projektu** pole, zadejte název projektu.
    2. V **umístění projektu** zadejte umístění projektu.
    3. Vedle položky **SDK projektu** rozevíracího seznamu vyberte **nový**vyberte **JDK**, a pak určete složku pro Java JDK verze 1.7 nebo vyšší. Vyberte **Java 1.8** 2.x clusteru Spark. Vyberte **Java 1.7** 1.x clusteru Spark. Výchozí umístění je C:\Program Files\Java\jdk1.8.x_xxx.
    4. V **verzi Sparku** rozevíracího seznamu, v Průvodci vytvořením projektu Scala Spark SDK a sady SDK Scala integruje správnou verzi. Pokud je verze clusteru Spark nižší než 2.0, vyberte **Spark 1.x**. V opačném případě vyberte **Spark 2.x**. Tento příklad používá Spark 1.6.2 (Scala 2.10.5). Ujistěte se, že používáte úložiště označené **Scala 2.10.x**. Nepoužívejte úložiště označené Scala 2.11.x.
    
    ![Vytvoření IntelliJ Scala vlastnosti projektu](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-create-scala-project-properties.png)


4. Vyberte **Finish** (Dokončit).
5. Pokud **projektu** zobrazení již není otevřen, stiskněte klávesu **Alt + 1** ho otevřete.
6. V **Project Exploreru**, rozbalte projekt a pak vyberte **src**.
7. Klikněte pravým tlačítkem na **src**, přejděte na **nový**a pak vyberte **Scala třídy**.
8. V **název** pole, zadejte název. V **druh** vyberte **objekt**. Pak vyberte **OK**.

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

10. Na **sestavení** nabídce vyberte možnost **sestavení projektu**. Ujistěte se, že se sestavení dokončí úspěšně.


## <a name="link-to-the-hortonworks-sandbox"></a>Odkaz na Hortonworks Sandbox

Předtím, než můžete propojit s Hortonworks Sandbox (emulátor), musíte mít existující aplikaci IntelliJ.

Odkaz na emulátor:

1. Otevřete projekt v IntelliJ.
2. Na **zobrazení** nabídce vyberte možnost **nástroje Windows**a pak vyberte **Průzkumníka služby Azure**.
3. Rozbalte **Azure**, klikněte pravým tlačítkem na **HDInsight**a pak vyberte **propojit emulátoru**.
4. V **odkaz A nový emulátor** dialogového okna zadejte heslo, které jste nastavili pro kořenový účet Hortonworks Sandbox. Pak zadejte hodnoty, které jsou podobné těm, jež používají na následujícím snímku obrazovky. Pak vyberte **OK**. 

   ![Odkaz na dialogové okno Nový emulátor](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-link-an-emulator.png)

5. Pokud chcete nakonfigurovat emulátor, vyberte **Ano**.

Když se úspěšně připojí emulátor, emulátor (Hortonworks Sandbox) je uveden na uzel HDInsight.

## <a name="submit-the-spark-scala-application-to-the-hortonworks-sandbox"></a>Odeslání aplikace Spark Scala na Hortonworks Sandbox

Po propojení IntelliJ IDEA v emulátoru, můžete odeslat váš projekt.

K odeslání projektu na emulátor:

1. V **Project Exploreru**, klikněte pravým tlačítkem na projekt a pak vyberte **aplikace odeslat Spark pro HDInsight**.
2. Proveďte následující kroky:

    1. V **cluster Spark (pouze Linux)** rozevíracího seznamu vyberte místní Hortonworks Sandbox.
    2. V **hlavní název třídy** vyberte nebo zadejte název hlavní třídy. Pro účely tohoto kurzu je název **GroupByTest**.

3. Vyberte **odeslat**. Protokoly odeslání úlohy se zobrazí v panelu nástrojů odeslání Spark.

## <a name="next-steps"></a>Další postup

- Zjistěte, jak [použít k vytvoření aplikací Spark pro cluster HDInsight Spark Linux HDInsight Tools v sadě Azure Toolkit pro IntelliJ](../spark/apache-spark-intellij-tool-plugin.md).

- Video o nástrojích HDInsight pro IntelliJ najdete v tématu [zavést nástroje HDInsight pro IntelliJ pro vývoj pro Spark](https://www.youtube.com/watch?v=YTZzYVgut6c).

- Zjistěte, jak [vzdáleně ladit aplikace Spark v clusteru služby HDInsight pomocí sady Azure Toolkit pro IntelliJ přes SSH](../spark/apache-spark-intellij-tool-debug-remotely-through-ssh.md).

- Zjistěte, jak [ladění aplikací Spark na clusteru HDInsight Spark Linux vzdáleně pomocí nástrojů HDInsight v sadě Azure Toolkit pro IntelliJ](../spark/apache-spark-intellij-tool-plugin-debug-jobs-remotely.md).

- Zjistěte, jak [HDInsight pomocí nástrojů v sadě Azure Toolkit pro Eclipse k vytvoření aplikací Spark](../spark/apache-spark-eclipse-tool-plugin.md).

- Video o nástrojích HDInsight pro Eclipse najdete v tématu [pomocí nástrojů HDInsight pro Eclipse k vytvoření aplikací Spark](https://mix.office.com/watch/1rau2mopb6fha).
