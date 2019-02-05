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
ms.date: 01/30/2019
ms.openlocfilehash: a969c026d702c423bee4871651c8b4fa26b3d37a
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/04/2019
ms.locfileid: "55700940"
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

V tomto kurzu se naučíte:
> [!div class="checklist"]
> * Vývoj aplikace Scala Maven pomocí IntelliJ

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

* Cluster Apache Spark ve službě HDInsight. Pokyny najdete v tématu [Vytváření clusterů Apache Spark ve službě Azure HDInsight](apache-spark-jupyter-spark-sql.md).
* [Oracle Java Development kit](https://www.azul.com/downloads/azure-only/zulu/).  Tento kurz používá verzi 8.0.202 Java.
* Prostředí Java IDE. Tento článek používá [verze IntelliJ IDEA Community  2018.3.4](https://www.jetbrains.com/idea/download/).
* Azure Toolkit for IntelliJ.  Zobrazit [instalace sady Azure Toolkit pro IntelliJ](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij-installation?view=azure-java-stable).

## <a name="install-scala-plugin-for-intellij-idea"></a>Instalace modulu plug-in Scala pro IntelliJ IDEA
Proveďte následující postup instalace modulu plug-in Scala:

1. Otevřete IntelliJ IDEA.

2. Na úvodní obrazovce, přejděte na **konfigurovat** > **moduly plug-in** otevřít **moduly plug-in** okna.
   
    ![Povolení modulu plug-in Scala](./media/apache-spark-create-standalone-application/enable-scala-plugin.png)

3. Vyberte **nainstalovat** pro modul plug-in Scala, který je mezi vybranými položkami v novém okně.  
 
    ![Instalace modulu plug-in Scala](./media/apache-spark-create-standalone-application/install-scala-plugin.png)

4. Po úspěšné instalaci modulu plug-in je potřeba restartovat integrované vývojové prostředí (IDE).


## <a name="use-intellij-to-create-application"></a>Použití IntelliJ k vytvoření aplikace

1. Spusťte IntelliJ IDEA a vyberte **vytvořit nový projekt** otevřít **nový projekt** okna.

2. Vyberte **Azure Spark/HDInsight** v levém podokně.

3. Vyberte **Project Spark (Scala)** z hlavního okna.

4. Z **nástroj pro sestavení** rozevíracího seznamu vyberte jednu z následujících akcí:
      * **Maven** podpory jazyků Scala vytvoření projektu průvodce.
      * **SBT** ke správě závislostí a vývoj aplikací pro Scala projektu.

   ![Dialogové okno New Project (Nový projekt)](./media/apache-spark-create-standalone-application/create-hdi-scala-app.png)

5. Vyberte **Další**.

6. V **nový projekt** okno, zadejte následující informace:  

  	|  Vlastnost   | Popis   |  
  	| ----- | ----- |  
  	|Název projektu| Zadejte název.|  
  	|Projekt&nbsp;umístění| Zadejte požadované umístění pro uložení projektu.|
  	|Sadu SDK projektu| To bude na první použití NÁPAD.  Vyberte **nové...**  a přejděte do vaší sady JDK.|
  	|Verze Sparku|V Průvodci vytvořením integruje správnou verzi pro Spark SDK a Scala SDK. Pokud je verze clusteru Spark nižší než 2.0, vyberte **Spark 1.x**. V opačném případě vyberte **Spark 2.x**. Tento příklad používá **Spark 2.3.0 (Scala 2.11.8)**.|

    ![Výběr sady Spark SDK](./media/apache-spark-create-standalone-application/hdi-new-project.png)

7. Vyberte **Finish** (Dokončit).

## <a name="create-a-standalone-scala-project"></a>Vytvoření samostatného projektu Scala

1. Spusťte IntelliJ IDEA a vyberte **vytvořit nový projekt** otevřít **nový projekt** okna.

2. Vyberte **Maven** v levém podokně.

3. Zadejte **Project SDK** (Sada SDK projektu). Pokud je prázdný, vyberte **nový...**  a přejděte do instalačního adresáře jazyka Java.

4. Vyberte **vytvořit z archetype** zaškrtávací políčko.  

5. V seznamu archetypů vyberte **org.scala-tools.archetypes:scala-archetype-simple**. Tato archetype vytvoří správný adresářovou strukturu a soubory ke stažení vyžaduje výchozí závislosti pro zápis aplikace Scala.

    ![Vytvoření projektu Maven](./media/apache-spark-create-standalone-application/create-maven-project.png)

6. Vyberte **Další**.

7. Zadejte odpovídající hodnoty pro **GroupId** (ID skupiny), **ArtifactId** (ID artefaktu) a **Version** (Verze). V tomto kurzu se používají následující hodnoty:

    - **GroupId:** com.microsoft.spark.example
    - **ArtifactId:** SparkSimpleApp

8. Vyberte **Další**.

9. Zkontrolujte nastavení a pak vyberte **Next** (Další).

10. Ověřte název a umístění projektu a pak vyberte **Finish** (Dokončit).  Projekt bude trvat několik minut, než importovat.

11. Jakmile je projekt importoval, v levém podokně přejděte do **SparkSimpleApp** > **src** > **testování**  >  **scala** > **com** > **microsoft** > **spark**  >  **příklad**.  Klikněte pravým tlačítkem na **MySpec**a pak vyberte **odstranit...** . Pro aplikaci tento soubor nepotřebujete.  Vyberte **OK** v dialogovém okně.
  
12. V dalších krocích, aktualizujete **pom.xml** k definování závislostí pro aplikace Spark Scala. Pokud chcete, aby se tyto závislosti stáhly a převedly automaticky, musíte nakonfigurovat Maven odpovídajícím způsobem.

13. Z **souboru** nabídce vyberte možnost **nastavení** otevřít **nastavení** okna.

14. Z **nastavení** okno, přejděte na **sestavení, provádění nasazení** > **Build Tools** > **Maven**  >  **Import**.

15. Vyberte **automaticky importovat projekty Maven** zaškrtávací políčko.

16. Vyberte **Apply** (Použít) a pak vyberte **OK**.  Potom se vrátíte do okna projektu.
   
    ![Konfigurace Mavenu pro automatické stahování](./media/apache-spark-create-standalone-application/configure-maven.png)
   

17. V levém podokně přejděte do **src** > **hlavní** > **scala** > **com.microsoft.spark.example**a potom dvakrát klikněte na panel **aplikace** otevřete App.scala.

18. Nahraďte stávající vzorový kód následujícím kódem a uložte změny. Tento kód čte data ze souboru HVAC.csv (dostupný ve všech clusterech HDInsight Spark), načítá řádky, které mají v šestém sloupci pouze jednu číslici, a zapisuje výstup do souboru **/HVACOut** ve výchozím kontejneru úložiště pro cluster.

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
19. V levém podokně dvakrát klikněte na soubor **pom.xml**.  
   
20. Mezi `<project>\<properties>` přidejte následující segmenty:
      
          <scala.version>2.11.8</scala.version>
          <scala.compat.version>2.11.8</scala.compat.version>
          <scala.binary.version>2.11</scala.binary.version>

21. Mezi `<project>\<dependencies>` přidejte následující segmenty:
      
           <dependency>
             <groupId>org.apache.spark</groupId>
             <artifactId>spark-core_${scala.binary.version}</artifactId>
             <version>2.3.0</version>
           </dependency>
      
    Uložte změny v souboru pom.xml.

22. Vytvořte soubor .jar. IntelliJ IDEA umožňuje vytvoření souboru JAR jako artefaktu projektu. Proveďte následující kroky.
    
    1. Z **souboru** příkaz **strukturu projektu...** .

    2. Z **strukturu projektu** okno, přejděte na **artefakty** > **na symbol plus +** > **JAR**  >  **z modulů se závislosti...** .
       
        ![Vytvoření souboru JAR](./media/apache-spark-create-standalone-application/create-jar-1.png)

    3. V **vytvořit JAR z modulů** okna, vyberte ikonu složky v **hlavní třída** textového pole.

    4. V **vyberte hlavní třída** okna, vyberte třídu, která se zobrazí ve výchozím nastavení a pak vyberte **OK**.
       
        ![Vytvoření souboru JAR](./media/apache-spark-create-standalone-application/create-jar-2.png)

    5. V **vytvořit JAR z modulů** okna, zkontrolujte **extrahovat cílový soubor JAR** je vybrané a pak vyberte možnost **OK**.  Toto nastavení vytvoří jeden soubor JAR se všemi závislostmi.
       
        ![Vytvoření souboru JAR](./media/apache-spark-create-standalone-application/create-jar-3.png)

    6. **Výstup rozložení** karta obsahuje seznam všech JAR, které jsou zahrnuté jako součást projektu Maven. Můžete vybrat a odstranit soubory, na kterých aplikace Scala nemá přímou závislost. V případě aplikace, kterou tady vytváříte, můžete odebrat všechny soubory kromě posledního (**SparkSimpleApp compile output**). Vyberte JAR odstranit a potom vyberte záporného symbolu **-**.
       
        ![Vytvoření souboru JAR](./media/apache-spark-create-standalone-application/delete-output-jars.png)
       
        Zkontrolujte, zda **zahrnout do sestavení projektu** zaškrtávací políčko zaškrtnuto, které zajišťuje, že soubor jar vytvoří pokaždé, když je sestavena nebo aktualizuje projekt. Vyberte **použít** a potom **OK**.

    7. Chcete-li vytvořit soubor jar, přejděte na **sestavení** > **artefakty sestavení** > **sestavení**. Projekt zkompiluje během přibližně 30 sekund.  Výstupní soubor JAR se vytvoří v adresáři **\out\artifacts**.
       
        ![Vytvoření souboru JAR](./media/apache-spark-create-standalone-application/output.png)

## <a name="run-the-application-on-the-apache-spark-cluster"></a>Spuštění aplikace v clusteru Apache Spark
Ke spuštění aplikace v clusteru můžete použít následující postupy:

* **Zkopírujte soubor JAR aplikace do objektu blob úložiště Azure** přidruženého ke clusteru. Můžete k tomu použít nástroj příkazového řádku [**AzCopy**](../../storage/common/storage-use-azcopy.md). Existuje také řada dalších klientů, které můžete k nahrání dat použít. Můžete najít další informace o nich v [nahrávání dat pro úlohy Apache Hadoop v HDInsight](../hdinsight-upload-data.md).

* **Odeslat úlohu aplikace vzdáleně pomocí Apache Livy** ke clusteru Spark. Clustery Spark ve službě HDInsight zahrnují rozhraní Livy, které zveřejňuje koncové body REST pro vzdálené odesílání úloh Sparku. Další informace najdete v tématu [úlohy odeslání Apache Spark vzdáleně pomocí Apache Livy Spark na HDInsight clusterů](apache-spark-livy-rest-interface.md).

## <a name="next-step"></a>Další krok

V tomto článku jste zjistili, jak vytvářet aplikace scala Apache Spark. V dalším článku zjistíte, jak tuto aplikaci spustit v clusteru HDInsight Spark pomocí Livy.

> [!div class="nextstepaction"]
>[Vzdálené spouštění úloh na clusteru Apache Spark pomocí Apache Livy](./apache-spark-livy-rest-interface.md)
