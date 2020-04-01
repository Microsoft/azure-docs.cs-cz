---
title: 'Kurz: Aplikace Scala Maven pro Spark & IntelliJ – Azure HDInsight'
description: Tutorial - Vytvořte aplikaci Spark napsanou v Scale s Apache Maven jako systém sestavení a stávající maven archetype pro Scala poskytované IntelliJ IDEA.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: tutorial
ms.custom: hdinsightactive,mvc
ms.date: 02/28/2020
ms.openlocfilehash: aa23b61967b27fefba863255721f4a0709ec02d5
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "78204538"
---
# <a name="tutorial-create-a-scala-maven-application-for-apache-spark-in-hdinsight-using-intellij"></a>Kurz: Vytvoření aplikace Scala Maven pro Apache Spark v HDInsight pomocí IntelliJ

V tomto kurzu se dozvíte, jak vytvořit aplikaci [Apache Spark](./apache-spark-overview.md) napsanou v [Scale](https://www.scala-lang.org/) pomocí [Apache Maven](https://maven.apache.org/) s IntelliJ IDEA. Tento článek jako sestavovací systém používá Apache Maven a začíná existujícím archetypem Maven pro Scala, který poskytuje IntelliJ IDEA.  Vytvoření aplikace Scala v IntelliJ IDEA zahrnuje následující kroky:

* Použití Mavenu jako sestavovacího systému
* Aktualizace souboru POM (Project Object Model) pro zajištění převodu závislostí modulů Sparku
* Napsání aplikace v jazyce Scala
* Vygenerování souboru JAR, který je možné odeslat do clusterů HDInsight Spark
* Spuštění aplikace v clusteru Spark pomocí Livy

V tomto kurzu se naučíte:
> [!div class="checklist"]
> * Instalace modulu plug-in Scala pro IntelliJ IDEA
> * Vývoj aplikace Scala Maven pomocí IntelliJ
> * Vytvoření samostatného projektu Scala

## <a name="prerequisites"></a>Požadavky

* Cluster Apache Spark ve službě HDInsight. Pokyny najdete v tématu [Vytváření clusterů Apache Spark ve službě Azure HDInsight](apache-spark-jupyter-spark-sql.md).

* [Sada Oracle Java Development Kit](https://www.azul.com/downloads/azure-only/zulu/).  Tento kurz používá java verzi 8.0.202.

* Prostředí Java IDE. Tento článek používá [IntelliJ IDEA Společenství ver.  2018.3.4](https://www.jetbrains.com/idea/download/).

* Azure Toolkit pro IntelliJ.  Viz [Instalace sady nástrojů Azure pro IntelliJ](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij-create-hello-world-web-app#installation-and-sign-in).

## <a name="install-scala-plugin-for-intellij-idea"></a>Instalace modulu plug-in Scala pro IntelliJ IDEA

Při instalaci modulu plug-in Scala proveďte následující kroky:

1. Otevřete IntelliJ IDEA.

2. Na úvodní obrazovce přejděte na **Konfigurovat** > **moduly plug-in** a otevřete okno **Moduly plug-in.**

    ![IntelliJ IDEA povolit scala plugin](./media/apache-spark-create-standalone-application/enable-scala-plugin1.png)

3. Vyberte **Instalovat** pro modul plug-in Scala, který se objevuje v novém okně.  

    ![IntelliJ IDEA nainstalovat plugin scala](./media/apache-spark-create-standalone-application/install-scala-plugin.png)

4. Po úspěšné instalaci modulu plug-in je potřeba restartovat integrované vývojové prostředí (IDE).

## <a name="use-intellij-to-create-application"></a>Použití IntelliJ k vytvoření aplikace

1. Spusťte IntelliJ IDEA a vyberte **Vytvořit nový projekt,** chcete-li otevřít okno **Nový projekt.**

2. V levém podokně vyberte **Apache Spark/HDInsight.**

3. V hlavním okně vyberte **Spark Project (Scala).**

4. V rozevíracím seznamu **Nástrojem sestavení** vyberte jednu z následujících hodnot:
      * **Podpora** průvodce vytvořením projektu Scala.
      * **SBT** pro správu závislostí a vytváření pro projekt Scala.

   ![Dialogové okno IntelliJ Nový projekt](./media/apache-spark-create-standalone-application/intellij-project-apache-spark.png)

5. Vyberte **další**.

6. V okně **Nový projekt** zadejte následující informace:  

  	|  Vlastnost   | Popis   |  
  	| ----- | ----- |  
  	|Název projektu| Zadejte název.|  
  	|Umístění&nbsp;projektu| Zadejte požadované umístění pro uložení projektu.|
  	|Sada SDK projektu| Při prvním použití IDEA to bude prázdné.  Vyberte **Nový...** a přejděte do jdk.|
  	|Verze Spark|Průvodce vytvořením integruje správnou verzi pro Spark SDK a Scala SDK. Pokud je verze clusteru Spark nižší než 2.0, vyberte **Spark 1.x**. V opačném případě vyberte **Spark 2.x**. Tento příklad používá **Spark 2.3.0 (Scala 2.11.8)**.|

    ![IntelliJ IDEA Výběr sady Spark SDK](./media/apache-spark-create-standalone-application/hdi-scala-new-project.png)

7. Vyberte **Finish** (Dokončit).

## <a name="create-a-standalone-scala-project"></a>Vytvoření samostatného projektu Scala

1. Spusťte IntelliJ IDEA a vyberte **Vytvořit nový projekt,** chcete-li otevřít okno **Nový projekt.**

2. V levém podokně vyberte **Maven.**

3. Zadejte **Project SDK** (Sada SDK projektu). Pokud je prázdné, vyberte **Nový...** a přejděte do instalačního adresáře Javy.

4. Zaškrtněte políčko **Vytvořit z archetypu.**  

5. V seznamu archetypů vyberte **org.scala-tools.archetypes:scala-archetype-simple**. Tento archetyp vytvoří správnou adresářovou strukturu a stáhne požadované výchozí závislosti pro zápis programu Scala.

    ![IntelliJ IDEA vytvořit projekt Maven](./media/apache-spark-create-standalone-application/intellij-project-create-maven.png)

6. Vyberte **další**.

7. Rozbalte **souřadnice artefaktů**. Zadejte příslušné hodnoty pro **GroupId**a **ArtifactId**. **Název**a **Umístění** se automaticky vyplní. V tomto kurzu se používají následující hodnoty:

    - **Skupinové id:** com.microsoft.spark.example
    - **ArtefaktId:** Aplikace SparkSimple

    ![IntelliJ IDEA vytvořit projekt Maven](./media/apache-spark-create-standalone-application/intellij-artifact-coordinates.png)

8. Vyberte **další**.

9. Zkontrolujte nastavení a pak vyberte **Next** (Další).

10. Ověřte název a umístění projektu a pak vyberte **Finish** (Dokončit).  Import projektu bude trvat několik minut.

11. Po importu projektu přejděte z levého podokna na **SparkSimpleApp** > **src** > **test** > **scala** > **com** > **Microsoft** > **Spark** > **Example**.  Klepněte pravým tlačítkem myši na **položku MySpec**a vyberte příkaz **Odstranit...**. Tento soubor pro aplikaci nepotřebujete.  V dialogovém okně vyberte **OK.**
  
12. V následujících krocích aktualizujete **pom.xml** a definujete závislosti pro aplikaci Spark Scala. Pokud chcete, aby se tyto závislosti stáhly a převedly automaticky, musíte nakonfigurovat Maven odpovídajícím způsobem.

13. V nabídce **Soubor** vyberte **Nastavení,** abyste otevřeli okno **Nastavení.**

14. V okně **Nastavení** přejděte na **build, execution, deployment** > **build tools** > **maven** > **importing**.

15. Zaškrtněte **políčko Importovat projekty Maven automaticky.**

16. Vyberte **Apply** (Použít) a pak vyberte **OK**.  Poté budete vráceni do okna projektu.

    ![Konfigurace Mavenu pro automatické stahování](./media/apache-spark-create-standalone-application/configure-maven-download.png)

17. V levém podokně přejděte na**hlavní** > **scala** > **com.microsoft.spark.example** **src** > a potom poklikejte na App.scala. **App**

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

    1. V nabídce **Soubor** vyberte **Struktura projektu...**.

    2. V okně **Struktura projektu** přejděte na **Artefakty** > **symbol plus +** > **JAR** > **Z modulů se závislostmi...**.

        ![IntelliJ IDEA struktura projektu přidat jar](./media/apache-spark-create-standalone-application/hdinsight-create-jar1.png)

    3. V okně **Vytvořit JAR z modulů** vyberte ikonu složky v textovém poli **Hlavní třída.**

    4. V okně **Vybrat hlavní třídu** vyberte třídu, která se zobrazí ve výchozím nastavení, a pak vyberte **OK**.

        ![IntelliJ IDEA struktura projektu vybrat třídu](./media/apache-spark-create-standalone-application/hdinsight-create-jar2.png)

    5. V okně **Vytvořit JAR z modulů** zkontrolujte, zda je **vybrána možnost extrakce do cílové horečné barvy,** a pak vyberte **OK**.  Toto nastavení vytvoří jeden soubor JAR se všemi závislostmi.

        ![Nádoba struktury projektu IntelliJ IDEA z modulu](./media/apache-spark-create-standalone-application/hdinsight-create-jar3.png)

    6. Na kartě **Rozložení výstupu** jsou uvedeny všechny nádoby, které jsou zahrnuty jako součást projektu Maven. Můžete vybrat a odstranit soubory, na kterých aplikace Scala nemá přímou závislost. Pro aplikaci, kterou zde vytváříte, můžete odstranit všechny kromě posledního (**SparkSimpleApp kompilovaný výstup**). Vyberte nádoby, které chcete odstranit, a pak vyberte záporný symbol **-**.

        ![Struktura projektu IntelliJ IDEA odstraní výstup](./media/apache-spark-create-standalone-application/hdi-delete-output-jars.png)

        Ujistěte se, že je zaškrtnuté políčko **Zahrnout do sestavení projektu,** které zajistí, že nádoba je vytvořena při každém sestavení nebo aktualizaci projektu. Vyberte **Použít** a pak **OK**.

    7. Chcete-li vytvořit nádobu, přejděte na **sestavení** > **sestavení artefaktů** > **sestavení**. Projekt se zkompiluje asi za 30 sekund.  Výstupní soubor JAR se vytvoří v adresáři **\out\artifacts**.

        ![Výstup artefaktů projektu IntelliJ IDEA](./media/apache-spark-create-standalone-application/hdi-artifact-output-jar.png)

## <a name="run-the-application-on-the-apache-spark-cluster"></a>Spuštění aplikace v clusteru Apache Spark

Ke spuštění aplikace v clusteru můžete použít následující postupy:

* **Zkopírujte nádobu aplikace do objektu blob úložiště Azure** přidruženého ke clusteru. Můžete k tomu použít nástroj příkazového řádku [**AzCopy**](../../storage/common/storage-use-azcopy.md). Existuje také řada dalších klientů, které můžete k nahrání dat použít. Více o nich najdete na [uploadu dat pro pracovní místa Apache Hadoop v HDInsight](../hdinsight-upload-data.md).

* **Pomocí Apache Livy můžete vzdáleně odeslat úlohu aplikace** do clusteru Spark. Clustery Spark ve službě HDInsight zahrnují rozhraní Livy, které zveřejňuje koncové body REST pro vzdálené odesílání úloh Sparku. Další informace najdete [v tématu Submit Apache Spark úlohy na dálku pomocí Apache Livy s clustery Spark na HDInsight](apache-spark-livy-rest-interface.md).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud nebudete nadále používat tuto aplikaci, odstraňte cluster, který jste vytvořili pomocí následujících kroků:

1. Přihlaste se k [portálu Azure](https://portal.azure.com/).

1. Do pole **Hledat** v horní části zadejte **HDInsight**.

1. V části **Služby** **vyberte clustery HDInsight** .

1. V seznamu clusterů HDInsight, který se zobrazí, vyberte **...** vedle clusteru, který jste vytvořili pro tento kurz.

1. Vyberte **Odstranit**. Vyberte **ano**.

![Odstranění clusteru webu AZURE HDInsight](./media/apache-spark-create-standalone-application/hdinsight-azure-portal-delete-cluster.png "Odstranění clusteru HDInsight")

## <a name="next-step"></a>Další krok

V tomto článku jste se naučili, jak vytvořit aplikaci Apache Spark scala. V dalším článku zjistíte, jak tuto aplikaci spustit v clusteru HDInsight Spark pomocí Livy.

> [!div class="nextstepaction"]
>[Spouštění úloh na dálku v clusteru Apache Spark pomocí Apache Livy](./apache-spark-livy-rest-interface.md)
