---
title: 'Kurz: Vytvoření aplikace v jazyce Scala Maven pro Spark v Azure HDInsight pomocí IntelliJ'
description: Vytvořte aplikaci Spark napsanou v jazyce Scala, která jako sestavovací systém používá Apache Maven, s využitím existujícího archetypu Maven pro Scala, který poskytuje IntelliJ IDEA.
services: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,mvc
ms.topic: tutorial
ms.date: 05/07/2018
ms.openlocfilehash: eef755c01a6703976383ee31b52bde14d32f0110
ms.sourcegitcommit: 4eeeb520acf8b2419bcc73d8fcc81a075b81663a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/19/2018
ms.locfileid: "53604061"
---
# <a name="tutorial-create-a-scala-maven-application-for-apache-spark-in-hdinsight-using-intellij"></a>Kurz: Vytvoření aplikace v jazyce Scala Maven pro Apache Spark v HDInsight pomocí IntelliJ

V tomto kurzu se dozvíte, jak vytvořit [Apache Spark](https://spark.apache.org/) aplikaci napsanou v [Scala](https://www.scala-lang.org/) pomocí [Apache Maven](https://maven.apache.org/) s IntelliJ IDEA. Tento článek jako sestavovací systém používá Apache Maven a začíná existujícím archetypem Maven pro Scala, který poskytuje IntelliJ IDEA.  Vytvoření aplikace Scala v IntelliJ IDEA zahrnuje následující kroky:

* Použití Mavenu jako sestavovacího systému
* Aktualizace souboru POM (Project Object Model) pro zajištění převodu závislostí modulů Sparku
* Napsání aplikace v jazyce Scala
* Vygenerování souboru JAR, který je možné odeslat do clusterů HDInsight Spark
* Spuštění aplikace v clusteru Spark pomocí Livy

> [!NOTE]  
> HDInsight také nabízí nástroj v podobě modulu plug-in pro IntelliJ IDEA, který zjednodušuje proces vytváření a odesílání aplikací do clusteru HDInsight Spark v Linuxu. Další informace najdete v tématu [použití modulu plug-in nástroje HDInsight pro IntelliJ IDEA pro vytvoření a odesílání aplikací Apache Spark](apache-spark-intellij-tool-plugin.md).
> 

V tomto kurzu se naučíte:
> [!div class="checklist"]
> * Vývoj aplikace Scala Maven pomocí IntelliJ

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.


## <a name="prerequisites"></a>Požadavky

* Cluster Apache Spark ve službě HDInsight. Pokyny najdete v tématu [Vytváření clusterů Apache Spark ve službě Azure HDInsight](apache-spark-jupyter-spark-sql.md).
* Sada Oracle Java Development Kit. Můžete si ji nainstalovat [odtud](https://aka.ms/azure-jdks).
* Prostředí Java IDE. V tomto článku se používá IntelliJ IDEA 18.1.1. Můžete si ho nainstalovat [odtud](https://www.jetbrains.com/idea/download/).

## <a name="use-intellij-to-create-application"></a>Použití IntelliJ k vytvoření aplikace

1. Spusťte IntelliJ IDEA a pak vytvořte projekt. V dialogovém okně **New Project** (Nový projekt) proveďte následující kroky: 

   a. Vyberte **HDInsight** > **Spark on HDInsight (Scala)** (Spark v HDInsight (Scala)).

   b. V seznamu **Build tool** (Nástroj sestavení) vyberte podle svých potřeb některý z následujících:

      * **Maven** – pro podporu průvodce vytvořením projektu Scala
      * **SBT** – pro správu závislostí a vytváření pro projekt Scala

   ![Dialogové okno New Project (Nový projekt)](./media/apache-spark-create-standalone-application/create-hdi-scala-app.png)

1. Vyberte **Další**.

1. Průvodce vytvořením projektu Scala automaticky zjistí, jestli jste nainstalovali modul plug-in Scala. Vyberte **Install** (Nainstalovat).

   ![Kontrola modulu plug-in Scala](./media/apache-spark-create-standalone-application/Scala-Plugin-check-Reminder.PNG) 

1. Pokud si chcete stáhnout modul plug-in Scala, vyberte **OK**. Postupujte podle pokynů pro restartování IntelliJ. 

   ![Dialogové okno instalace modulu plug-in Scala](./media/apache-spark-create-standalone-application/Choose-Scala-Plugin.PNG)

1. V okně **New Project** (Nový projekt) proveďte následující kroky:  

    ![Výběr sady Spark SDK](./media/apache-spark-create-standalone-application/hdi-new-project.png)

   a. Zadejte název a umístění projektu.

   b. V rozevíracím seznamu **Project SDK** (SDK projektu) vyberte **Java 1.8** pro cluster Spark 2.x, nebo vyberte **Java 1.7** pro cluster Spark 1.x.

   c. Pomocí rozevíracího seznamu **Spark version** (Verze clusteru Spark) průvodce vytvořením projektu Scala integruje správnou verzi pro sady Spark SDK a Scala SDK. Pokud je verze clusteru Spark nižší než 2.0, vyberte **Spark 1.x**. V opačném případě vyberte **Spark 2.x**. V tomto příkladu se používá **Spark 2.0.2 (Scala 2.11.8)**.

1. Vyberte **Finish** (Dokončit).

## <a name="install-scala-plugin-for-intellij-idea"></a>Instalace modulu plug-in Scala pro IntelliJ IDEA
Nainstalujte modul plug-in Scala pomocí následujících kroků:

1. Otevřete IntelliJ IDEA.
1. Na úvodní obrazovce vyberte **Configure** (Konfigurace) a pak vyberte **Plugins** (Moduly plug-in).
   
    ![Povolení modulu plug-in Scala](./media/apache-spark-create-standalone-application/enable-scala-plugin.png)
1. V levém dolním rohu vyberte **Install JetBrains plugin** (Nainstalovat modul plug-in pro JetBrains). 
1. V dialogovém okně **Browse JetBrains Plugins** (Procházet moduly plug-in JetBrains) vyhledejte **Scala** a pak vyberte **Install** (Nainstalovat).
   
    ![Instalace modulu plug-in Scala](./media/apache-spark-create-standalone-application/install-scala-plugin.png)
1. Po úspěšné instalaci modulu plug-in je potřeba restartovat integrované vývojové prostředí (IDE).

## <a name="create-a-standalone-scala-project"></a>Vytvoření samostatného projektu Scala
1. Otevřete IntelliJ IDEA.
1. V nabídce **File** (Soubor) vyberte **New > Project** (Nový > Projekt) a vytvořte nový projekt.
1. V dialogovém okně New Project (Nový projekt) proveďte následující výběry:
   
    ![Vytvoření projektu Maven](./media/apache-spark-create-standalone-application/create-maven-project.png)
   
   * Jako typ projektu vyberte **Maven**.
   * Zadejte **Project SDK** (Sada SDK projektu). Vyberte **New** (Nová) a přejděte do instalačního adresáře Javy, obvykle `C:\Program Files\Java\jdk1.8.0_66`.
   * Vyberte možnost **Create from archetype** (Vytvořit z archetypu).
   * V seznamu archetypů vyberte **org.scala-tools.archetypes:scala-archetype-simple**. Tento archetyp vytvoří správnou adresářovou strukturu a stáhne výchozí závislosti požadované k napsání programu Scala.
1. Vyberte **Další**.
1. Zadejte odpovídající hodnoty pro **GroupId** (ID skupiny), **ArtifactId** (ID artefaktu) a **Version** (Verze). V tomto kurzu se používají následující hodnoty:

    - GroupId: com.microsoft.spark.example
    - ArtifactId: SparkSimpleApp
1. Vyberte **Další**.
1. Zkontrolujte nastavení a pak vyberte **Next** (Další).
1. Ověřte název a umístění projektu a pak vyberte **Finish** (Dokončit).
1. V levém podokně vyberte **src > test > scala > com > microsoft > spark > example**, klikněte pravým tlačítkem na **MySpec** a pak vyberte **Delete** (Odstranit). Pro aplikaci tento soubor nepotřebujete.
  
1. V dalších krocích aktualizujete soubor pom.xml o definici závislostí pro aplikaci Spark Scala. Pokud chcete, aby se tyto závislosti stáhly a převedly automaticky, musíte nakonfigurovat Maven odpovídajícím způsobem.
   
    ![Konfigurace Mavenu pro automatické stahování](./media/apache-spark-create-standalone-application/configure-maven.png)
   
   1. V nabídce **File** (Soubor) vyberte **Settings** (Nastavení).
   1. V dialogovém okně **Settings** (Nastavení) přejděte do části **Build, Execution, Deployment** > **Build Tools** > **Maven** > **Importing** (Sestavení, spuštění, nasazení > Nástroje pro sestavení > Maven > Importování).
   1. Vyberte možnost **Import Maven projects automatically** (Automaticky importovat projekty Maven).
   1. Vyberte **Apply** (Použít) a pak vyberte **OK**.
1. V levém podokně vyberte **src > main > scala > com.microsoft.spark.example** a pak dvojím kliknutím na **App** otevřete soubor App.scala.

1. Nahraďte stávající vzorový kód následujícím kódem a uložte změny. Tento kód čte data ze souboru HVAC.csv (dostupný ve všech clusterech HDInsight Spark), načítá řádky, které mají v šestém sloupci pouze jednu číslici, a zapisuje výstup do souboru **/HVACOut** ve výchozím kontejneru úložiště pro cluster.

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
1. V levém podokně dvakrát klikněte na soubor **pom.xml**.
   
   1. Mezi `<project>\<properties>` přidejte následující segmenty:
      
          <scala.version>2.10.4</scala.version>
          <scala.compat.version>2.10.4</scala.compat.version>
          <scala.binary.version>2.10</scala.binary.version>
   1. Mezi `<project>\<dependencies>` přidejte následující segmenty:
      
           <dependency>
             <groupId>org.apache.spark</groupId>
             <artifactId>spark-core_${scala.binary.version}</artifactId>
             <version>1.4.1</version>
           </dependency>
      
      Uložte změny v souboru pom.xml.
1. Vytvořte soubor .jar. IntelliJ IDEA umožňuje vytvoření souboru JAR jako artefaktu projektu. Proveďte následující kroky.
    
    1. V nabídce **File** (Soubor) vyberte **Project Structure** (Struktura projektu).
    1. V dialogovém okně **Project Structure** (Struktura projektu) vyberte **Artifacts** (Artefakty) a pak vyberte symbol plus. V automaticky otevíraném dialogovém okně vyberte **JAR** a pak **From modules with dependencies** (Z modulů se závislostmi).
       
        ![Vytvoření souboru JAR](./media/apache-spark-create-standalone-application/create-jar-1.png)
    1. V dialogovém okně **Create JAR from Modules** (Vytvořit soubor JAR z modulů) vyberte symbol tří teček (![tři tečky](./media/apache-spark-create-standalone-application/ellipsis.png)) vedle položky **Main Class** (Hlavní třída).
    1. V dialogovém okně **Select Main Class** (Vybrat hlavní třídu) vyberte třídu, která se zobrazí ve výchozím nastavení, a pak vyberte **OK**.
       
        ![Vytvoření souboru JAR](./media/apache-spark-create-standalone-application/create-jar-2.png)
    1. V dialogovém okně **Create JAR from Modules** (Vytvořit soubor JAR z modulů) se ujistěte, že je vybraná možnost **extract to the target JAR** (extrahovat do cílového souboru JAR), a pak vyberte **OK**.  Toto nastavení vytvoří jeden soubor JAR se všemi závislostmi.
       
        ![Vytvoření souboru JAR](./media/apache-spark-create-standalone-application/create-jar-3.png)
    1. Na kartě Output Layout (Rozložení výstupu) se zobrazí všechny soubory JAR zahrnuté jako součást projektu Maven. Můžete vybrat a odstranit soubory, na kterých aplikace Scala nemá přímou závislost. V případě aplikace, kterou tady vytváříte, můžete odebrat všechny soubory kromě posledního (**SparkSimpleApp compile output**). Vyberte soubory JAR, které chcete odstranit, a pak vyberte ikonu **Delete** (Odstranit).
       
        ![Vytvoření souboru JAR](./media/apache-spark-create-standalone-application/delete-output-jars.png)
       
        Ujistěte se, že je zaškrtnuté políčko **Include in project build** (Zahrnout do sestavení projektu). Tím se zajistí, že se soubor JAR vytvoří při každém sestavení nebo aktualizaci projektu. Vyberte **Apply** (Použít) a pak **OK**.
    1. V nabídce **Build** (Sestavení) vyberte **Build Artifacts** (Sestavit artefakty) a vytvořte soubor JAR. Výstupní soubor JAR se vytvoří v adresáři **\out\artifacts**.
       
        ![Vytvoření souboru JAR](./media/apache-spark-create-standalone-application/output.png)

## <a name="run-the-application-on-the-apache-spark-cluster"></a>Spuštění aplikace v clusteru Apache Spark
Ke spuštění aplikace v clusteru můžete použít následující postupy:

* **Zkopírujte soubor JAR aplikace do objektu blob úložiště Azure** přidruženého ke clusteru. Můžete k tomu použít nástroj příkazového řádku [**AzCopy**](../../storage/common/storage-use-azcopy.md). Existuje také řada dalších klientů, které můžete k nahrání dat použít. Můžete najít další informace o nich v [nahrávání dat pro úlohy Apache Hadoop v HDInsight](../hdinsight-upload-data.md).
* **Odeslat úlohu aplikace vzdáleně pomocí Apache Livy** ke clusteru Spark. Clustery Spark ve službě HDInsight zahrnují rozhraní Livy, které zveřejňuje koncové body REST pro vzdálené odesílání úloh Sparku. Další informace najdete v tématu [úlohy odeslání Apache Spark vzdáleně pomocí Apache Livy Spark na HDInsight clusterů](apache-spark-livy-rest-interface.md).

## <a name="next-step"></a>Další krok

V tomto článku jste zjistili, jak vytvářet aplikace scala Apache Spark. V dalším článku zjistíte, jak tuto aplikaci spustit v clusteru HDInsight Spark pomocí Livy.

> [!div class="nextstepaction"]
>[Vzdálené spouštění úloh na clusteru Apache Spark pomocí Apache Livy](./apache-spark-livy-rest-interface.md)

