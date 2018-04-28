---
title: Vytvoření aplikace Scala ke spuštění na clustery Spark - Azure HDInsight | Microsoft Docs
description: Vytvoření Spark aplikace napsané v jazyce Scala s Apache Maven jako systém sestavení a existující archetype Maven pro Scala poskytované IntelliJ IDEA.
services: hdinsight
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: b2467a40-a340-4b80-bb00-f2c3339db57b
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 11/28/2017
ms.author: nitinme
ms.openlocfilehash: d64d60112641992a5a56ee5d129e7cbe754bde9d
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2018
---
# <a name="create-a-scala-maven-application-to-run-on-apache-spark-cluster-on-hdinsight"></a>Vytvoření aplikace Scala Maven ke spuštění v clusteru Apache Spark v HDInsight

Zjistěte, jak vytvořit aplikaci Spark napsané v jazyce Scala pomocí nástroje Maven s IntelliJ IDEA. Článek používá Apache Maven jako systém sestavení a spuštění se existující archetype Maven Scala poskytované IntelliJ IDEA.  Vytvoření aplikace Scala v IntelliJ IDEA zahrnuje následující kroky:

* Používání Maven jako systém sestavení.
* Aktualizujte soubor projektu objektu modelu (POM) o vyřešení závislostí modulu Spark.
* Zápis v jazyce Scala vaší aplikace.
* Generovat soubor jar, který lze odeslat do clusterů HDInsight Spark.
* Spuštění aplikace na clusteru Spark pomocí Livy.

> [!NOTE]
> HDInsight také poskytuje nástroj k usnadnění procesu vytváření a odesílání aplikací do clusteru HDInsight Spark v Linux na IntelliJ IDEA modulu plug-in. Další informace najdete v tématu [pomocí modulu plug-in nástroje HDInsight pro IntelliJ IDEA pro vytvoření a odesílání aplikací Spark](apache-spark-intellij-tool-plugin.md).
> 
> 

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Cluster Apache Spark v HDInsight. Pokyny najdete v tématu [clusterů vytvořit Apache Spark v Azure HDInsight](apache-spark-jupyter-spark-sql.md).
* Java Development kit Oracle. Můžete nainstalovat z [zde](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).
* Java IDE. Tento článek používá IntelliJ IDEA 15.0.1. Můžete nainstalovat z [zde](https://www.jetbrains.com/idea/download/).

## <a name="install-scala-plugin-for-intellij-idea"></a>Instalace modulu plug-in Scala pro IntelliJ IDEA
Pokud instalace IntelliJ IDEA není nebyl vyzván k povolení modulu plug-in Scala, spusťte IntelliJ IDEA a projít následující kroky k instalaci modulu plug-in:

1. Spusťte IntelliJ IDEA a na úvodní obrazovce klikněte na **konfigurace** a pak klikněte na **modulů plug-in**.
   
    ![Povolení modulu plug-in scala](./media/apache-spark-create-standalone-application/enable-scala-plugin.png)
2. Na další obrazovce klikněte na tlačítko **JetBrains instalace modulu plug-in** z levého dolního rohu. V **procházet modulů plug-in JetBrains** dialogové okno otevře, vyhledejte Scala a pak klikněte na **nainstalovat**.
   
    ![Instalace modulu plug-in scala](./media/apache-spark-create-standalone-application/install-scala-plugin.png)
3. Poté, co modul plug-in nainstaluje úspěšně, klikněte na tlačítko **restartujte IntelliJ IDEA tlačítko** restartování rozhraní IDE.

## <a name="create-a-standalone-scala-project"></a>Vytvoření projektu samostatné Scala
1. Spusťte IntelliJ IDEA a vytvoření nového projektu. V dialogovém okně Nový projekt vyberte následující možnosti a pak klikněte na tlačítko **Další**.
   
    ![Vytvořte projekt Maven](./media/apache-spark-create-standalone-application/create-maven-project.png)
   
   * Vyberte **Maven** jako typ projektu.
   * Zadejte **projektu SDK**. Kliknutím na tlačítko Nová a přejděte do instalačního adresáře nástroje Java, obvykle `C:\Program Files\Java\jdk1.8.0_66`.
   * Vyberte **vytvořit z archetype** možnost.
   * V seznamu archetypes vyberte **org.scala tools.archetypes:scala archetype jednoduchý**. Tato akce vytvoří správné adresářovou strukturu a stáhnout požadované výchozí závislosti zápis Scala program.
2. Zadejte příslušné hodnoty pro **GroupId**, **ArtifactId**, a **verze**. Klikněte na **Další**.
3. V další dialogové okno, kde můžete zadat domovský adresář Maven a další nastavení uživatele, přijměte výchozí hodnoty a klikněte na **Další**.
4. V dialogovém okně poslední zadejte název projektu a umístění a pak klikněte na tlačítko **Dokončit**.
5. Odstranit **MySpec.Scala** souborů **src\test\scala\com\microsoft\spark\example**. Není to nutné pro aplikaci.
6. V případě potřeby přejmenujte výchozí zdroj a testovací soubory. V levém podokně IntelliJ IDEA, přejděte na **src\main\scala\com.microsoft.spark.example**. Klikněte pravým tlačítkem na **App.scala**, klikněte na tlačítko **Refaktorovat**klikněte přejmenovat soubor a v dialogovém okně zadejte nový název pro aplikaci a pak na **Refaktorovat**.
   
    ![Přejmenování souborů](./media/apache-spark-create-standalone-application/rename-scala-files.png)  
7. V dalších krocích aktualizujte pom.xml k definování závislostí u aplikací Spark Scala. Tyto závislosti se stáhnou a automaticky vyřešen musíte nakonfigurovat Maven odpovídajícím způsobem.
   
    ![Konfigurace Maven pro automatické stahování](./media/apache-spark-create-standalone-application/configure-maven.png)
   
   1. Z **soubor** nabídky, klikněte na tlačítko **nastavení**.
   2. V **nastavení** dialogové okno pole, přejděte na **sestavení, provádění nasazení** > **nástroje sestavení** > **Maven**  >  **Import**.
   3. Vyberte možnost **automaticky importovat projekty Maven**.
   4. Klikněte na tlačítko **použít**a potom klikněte na **OK**.
8. Aktualizujte Scala zdrojový soubor, který patří kódu aplikace. Otevřete existující ukázkového kódu nahraďte následujícím kódem a uložte změny. Tento kód čte data z HVAC.csv (k dispozici na všech clusterech HDInsight Spark), načte řádky, které mají ve sloupci šesté pouze jednu číslici a zapíše výstup do **/HVACOut** pod výchozí kontejner úložiště pro cluster.
   
        package com.microsoft.spark.example
   
        import org.apache.spark.SparkConf
        import org.apache.spark.SparkContext
   
        /**
          * Test IO to wasb
          */
        object WasbIOTest {
          def main (arg: Array[String]): Unit = {
            val conf = new SparkConf().setAppName("WASBIOTest")
            val sc = new SparkContext(conf)
   
            val rdd = sc.textFile("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
   
            //find the rows which have only one digit in the 7th column in the CSV
            val rdd1 = rdd.filter(s => s.split(",")(6).length() == 1)
   
            rdd1.saveAsTextFile("wasb:///HVACout")
          }
        }
9. Aktualizace souboru pom.xml.
   
   1. V rámci `<project>\<properties>` přidejte následující:
      
          <scala.version>2.10.4</scala.version>
          <scala.compat.version>2.10.4</scala.compat.version>
          <scala.binary.version>2.10</scala.binary.version>
   2. V rámci `<project>\<dependencies>` přidejte následující:
      
           <dependency>
             <groupId>org.apache.spark</groupId>
             <artifactId>spark-core_${scala.binary.version}</artifactId>
             <version>1.4.1</version>
           </dependency>
      
      Uložte změny do pom.xml.
10. Vytvoření souboru .jar. IntelliJ IDEA umožňuje vytváření JAR jako artefakt projektu. Proveďte následující kroky.
    
    1. Z **soubor** nabídky, klikněte na tlačítko **strukturu projektu**.
    2. V **strukturu projektu** dialogové okno, klikněte na tlačítko **artefakty** a pak klikněte na plus symbol. V dialogovém okně automaticky otevírané okno klikněte na **JAR**a potom klikněte na **z modulů se závislostmi**.
       
        ![Vytvoření JAR](./media/apache-spark-create-standalone-application/create-jar-1.png)
    3. V **vytvořit JAR z modulů** dialogové okno pole, klikněte na tlačítko se třemi tečkami (![třemi tečkami](./media/apache-spark-create-standalone-application/ellipsis.png) ) proti **hlavní třídy**.
    4. V **vyberte třídu hlavní** dialogovém okně vyberte třídu, která se zobrazí ve výchozím nastavení a pak klikněte na tlačítko **OK**.
       
        ![Vytvoření JAR](./media/apache-spark-create-standalone-application/create-jar-2.png)
    5. V **vytvořit JAR z modulů** dialogové okno pole, ujistěte se, že možnost **extrahovat k cíli JAR** je vybrána a potom klikněte na **OK**. Tím se vytvoří jeden JAR s všechny závislosti.
       
        ![Vytvoření JAR](./media/apache-spark-create-standalone-application/create-jar-3.png)
    6. Karta Výstup rozložení uvádí všechny JAR, které jsou součástí na projekt Maven. Můžete vybrat a odstranit ty, na kterém aplikace Scala má žádné přímé závislost. Pro aplikaci tady vytváříme, můžete odebrat všechny uzly s výjimkou poslední (**SparkSimpleApp kompilace výstup**). Vyberte JAR odstranit a potom klikněte na **odstranit** ikonu.
       
        ![Vytvoření JAR](./media/apache-spark-create-standalone-application/delete-output-jars.png)
       
        Zajistěte, aby **sestavení na Ujistěte se,** je políčko zaškrtnuto, který zajistí, aby jar vytvořit pokaždé, když je vytvořené nebo aktualizované projektu. Klikněte na tlačítko **použít** a potom **OK**.
    7. V řádku nabídek klikněte na **sestavení**a potom klikněte na **zkontrolujte projektu**. Můžete také kliknout na **sestavení artefaktů** vytvořit jar. Výstup jar se vytvořil v rámci **\out\artifacts**.
       
        ![Vytvoření JAR](./media/apache-spark-create-standalone-application/output.png)

## <a name="run-the-application-on-the-spark-cluster"></a>Spuštění aplikace na clusteru Spark
Ke spuštění aplikace v clusteru, postupujte takto:

* **Zkopírujte jar aplikace do objektu blob úložiště Azure** přidružen ke clusteru. Můžete použít [ **AzCopy**](../../storage/common/storage-use-azcopy.md), nástroj příkazového řádku, tak. Existuje mnoho také ostatní klienty, který můžete použít k nahrání dat. Můžete najít další informace o nich v [nahrávání dat pro úlohy Hadoop do HDInsight](../hdinsight-upload-data.md).
* **Odeslat úlohu aplikaci vzdáleně pomocí Livy** do clusteru Spark. Clustery Spark v HDInsight zahrnuje Livy, který zveřejňuje koncové body REST pro vzdálené odesílání úloh Spark. Další informace najdete v tématu [úlohy odeslání Spark vzdáleně pomocí Spark pomocí Livy clusterů v HDInsight](apache-spark-livy-rest-interface.md).

## <a name="next-step"></a>Další krok

V tomto článku jste zjistili, jak vytvořit aplikaci Spark scala. Přechodu na další článku se dozvíte, jak ke spuštění této aplikace v clusteru HDInsight Spark pomocí Livy.

> [!div class="nextstepaction"]
>[Vzdálené spouštění úloh na clusteru Sparku pomocí Livy](apache-spark-livy-rest-interface.md)

