---
title: 'Kurz: Scala Maven App for Spark & IntelliJ – Azure HDInsight'
description: Kurz – vytvoření aplikace Spark napsané v Scala s Apache Maven jako systém sestavení a stávající Maven Archetype pro Scala poskytované IntelliJ NÁPADem.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: tutorial
ms.custom: hdinsightactive,mvc
ms.date: 02/28/2020
ms.openlocfilehash: aa23b61967b27fefba863255721f4a0709ec02d5
ms.sourcegitcommit: 1fa2bf6d3d91d9eaff4d083015e2175984c686da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/01/2020
ms.locfileid: "78204538"
---
# <a name="tutorial-create-a-scala-maven-application-for-apache-spark-in-hdinsight-using-intellij"></a>Kurz: Vytvoření aplikace Scala Maven pro Apache Spark v HDInsight pomocí IntelliJ

V tomto kurzu se naučíte, jak vytvořit aplikaci [Apache Spark](./apache-spark-overview.md) napsanou v [Scala](https://www.scala-lang.org/) pomocí [Apache Maven](https://maven.apache.org/) s IntelliJ nápadem. Tento článek jako sestavovací systém používá Apache Maven a začíná existujícím archetypem Maven pro Scala, který poskytuje IntelliJ IDEA.  Vytvoření aplikace Scala v IntelliJ IDEA zahrnuje následující kroky:

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

## <a name="prerequisites"></a>Předpoklady

* Cluster Apache Spark ve službě HDInsight. Pokyny najdete v tématu [Vytváření clusterů Apache Spark ve službě Azure HDInsight](apache-spark-jupyter-spark-sql.md).

* [Sada Oracle Java Development Kit](https://www.azul.com/downloads/azure-only/zulu/).  V tomto kurzu se používá Java verze 8.0.202.

* Prostředí Java IDE. V tomto článku se používá [INTELLIJ nápad Community ver.  2018.3.4](https://www.jetbrains.com/idea/download/).

* Azure Toolkit for IntelliJ.  Viz [instalace Azure Toolkit for IntelliJ](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij-create-hello-world-web-app#installation-and-sign-in).

## <a name="install-scala-plugin-for-intellij-idea"></a>Instalace modulu plug-in Scala pro IntelliJ IDEA

K instalaci modulu plug-in Scala proveďte následující kroky:

1. Otevřete IntelliJ IDEA.

2. Na úvodní obrazovce přejděte ke **konfiguraci** > **modulů plug-in** a otevřete okno **moduly plug-in** .

    ![IntelliJ nápad povolit modul plug-in Scala](./media/apache-spark-create-standalone-application/enable-scala-plugin1.png)

3. Vyberte **nainstalovat** pro modul plug-in Scala, který je vybraný v novém okně.  

    ![IntelliJ nápad nainstalovat modul plug-in Scala](./media/apache-spark-create-standalone-application/install-scala-plugin.png)

4. Po úspěšné instalaci modulu plug-in je potřeba restartovat integrované vývojové prostředí (IDE).

## <a name="use-intellij-to-create-application"></a>Použití IntelliJ k vytvoření aplikace

1. Spusťte IntelliJ nápad a výběrem **vytvořit nový projekt** otevřete okno **Nový projekt** .

2. V levém podokně vyberte **Apache Spark/HDInsight** .

3. V hlavním okně vyberte **projekt Spark (Scala)** .

4. V rozevíracím seznamu **Nástroj sestavení** vyberte jednu z následujících hodnot:
      * **Maven** for Scala – Průvodce vytvořením projektu – podpora.
      * **SBT** pro správu závislostí a sestavení projektu Scala.

   ![IntelliJ dialogového okna Nový projekt](./media/apache-spark-create-standalone-application/intellij-project-apache-spark.png)

5. Vyberte **Další**.

6. V okně **Nový projekt** zadejte následující informace:  

  	|  Vlastnost   | Popis   |  
  	| ----- | ----- |  
  	|Název projektu| Zadejte název.|  
  	|Umístění projektu&nbsp;| Zadejte požadované umístění pro uložení projektu.|
  	|Projektový SDK| Tato akce bude při prvním používání NÁPADu prázdná.  Vyberte **Nový...** a přejděte k JDK.|
  	|Verze Sparku|Průvodce vytvořením integruje správnou verzi sady Spark SDK a Scala SDK. Pokud je verze clusteru Spark nižší než 2.0, vyberte **Spark 1.x**. V opačném případě vyberte **Spark 2.x**. V tomto příkladu se používá **Spark 2.3.0 (Scala 2.11.8)** .|

    ![IntelliJ nápad výběru sady Spark SDK](./media/apache-spark-create-standalone-application/hdi-scala-new-project.png)

7. Vyberte **Finish** (Dokončit).

## <a name="create-a-standalone-scala-project"></a>Vytvoření samostatného projektu Scala

1. Spusťte IntelliJ nápad a výběrem **vytvořit nový projekt** otevřete okno **Nový projekt** .

2. V levém podokně vyberte **Maven** .

3. Zadejte **Project SDK** (Sada SDK projektu). Pokud je tato možnost prázdná, vyberte **Nový...** a přejděte do instalačního adresáře Java.

4. Zaškrtněte políčko **vytvořit z Archetype** .  

5. V seznamu archetypů vyberte **org.scala-tools.archetypes:scala-archetype-simple**. Tento Archetype vytvoří správnou adresářovou strukturu a stáhne požadované výchozí závislosti pro zápis programu Scala.

    ![IntelliJ nápad vytvořit projekt Maven](./media/apache-spark-create-standalone-application/intellij-project-create-maven.png)

6. Vyberte **Další**.

7. Rozbalte možnost **souřadnice artefaktů**. Poskytněte příslušné hodnoty pro **GroupID**a **ArtifactId**. **Název**a **umístění** budou automaticky vyplněny. V tomto kurzu se používají následující hodnoty:

    - **GroupID:** com. Microsoft. spark. example
    - **ArtifactId:** SparkSimpleApp

    ![IntelliJ nápad vytvořit projekt Maven](./media/apache-spark-create-standalone-application/intellij-artifact-coordinates.png)

8. Vyberte **Další**.

9. Zkontrolujte nastavení a pak vyberte **Next** (Další).

10. Ověřte název a umístění projektu a pak vyberte **Finish** (Dokončit).  Import projektu bude trvat několik minut.

11. Po importu projektu v levém podokně přejděte do **SparkSimpleApp** > **src** > **test** > **scala** > **com** > **Microsoft** > **Spark** > **příklad**.  Klikněte pravým tlačítkem na **MySpec**a pak vyberte **Odstranit...** . Tento soubor pro aplikaci nepotřebujete.  V dialogovém okně vyberte **OK** .
  
12. V následujících krocích aktualizujete **pom. XML** a definujete závislosti pro aplikaci Spark Scala. Pokud chcete, aby se tyto závislosti stáhly a převedly automaticky, musíte nakonfigurovat Maven odpovídajícím způsobem.

13. V nabídce **soubor** vyberte **Nastavení** a otevřete okno **Nastavení** .

14. V okně **Nastavení** přejděte na **sestavení, spuštění, nasazení** > nástroje pro **sestavení** > **Maven** > **Import**.

15. Zaškrtněte políčko **importovat projekty Maven automaticky** .

16. Vyberte **Apply** (Použít) a pak vyberte **OK**.  Pak se vrátíte do okna projektu.

    ![Konfigurace Mavenu pro automatické stahování](./media/apache-spark-create-standalone-application/configure-maven-download.png)

17. V levém podokně přejděte do **src** > **main** > **Scala** > **com. Microsoft. spark. example**a dvakrát klikněte na **aplikace** a otevřete App. Scala.

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

    1. V nabídce **soubor** vyberte možnost **struktura projektu...** .

    2. V okně **struktura projektu** přejděte na **artefakty** > **symbol plus +**  > **jar** > **z modulů se závislostmi...** .

        ![IntelliJ strukturu projektu NÁPADu přidat jar](./media/apache-spark-create-standalone-application/hdinsight-create-jar1.png)

    3. V okně **vytvořit jar z modulů** vyberte ikonu složky v textovém poli **hlavní třída** .

    4. V okně **Vybrat hlavní třídu** vyberte třídu, která se zobrazí ve výchozím nastavení, a pak vyberte **OK**.

        ![Výběr třídy IntelliJ NÁPADu struktury projektu](./media/apache-spark-create-standalone-application/hdinsight-create-jar2.png)

    5. V okně **vytvořit jar z modulů** zkontrolujte, že je vybraná možnost **extrahovat do cílového jar** , a pak vyberte **OK**.  Toto nastavení vytvoří jeden soubor JAR se všemi závislostmi.

        ![IntelliJ NÁPADu struktury projektu jar z modulu](./media/apache-spark-create-standalone-application/hdinsight-create-jar3.png)

    6. Karta **rozložení výstupu** obsahuje seznam všech jar, které jsou zahrnuty jako součást projektu Maven. Můžete vybrat a odstranit soubory, na kterých aplikace Scala nemá přímou závislost. Pro aplikaci, kterou vytváříte, můžete odebrat všechny, ale jenom poslední (**SparkSimpleApp**). Vyberte jar, který chcete odstranit, a potom vyberte negativní symbol **-** .

        ![IntelliJ NÁPADu struktury projektu odstranit výstup](./media/apache-spark-create-standalone-application/hdi-delete-output-jars.png)

        Zajistěte, aby bylo zaškrtnuto políčko **zahrnout do sestavení projektu** , které zajistí, že jar bude vytvořen pokaždé, když je projekt sestaven nebo aktualizován. Vyberte **použít** a pak **OK**.

    7. Chcete-li vytvořit jar, přejděte na **sestavení** > **artefakty** sestavení > **sestavování**. Projekt se zkompiluje přibližně po 30 sekundách.  Výstupní soubor JAR se vytvoří v adresáři **\out\artifacts**.

        ![Výstup artefaktů projektu IntelliJ](./media/apache-spark-create-standalone-application/hdi-artifact-output-jar.png)

## <a name="run-the-application-on-the-apache-spark-cluster"></a>Spuštění aplikace v clusteru Apache Spark

Ke spuštění aplikace v clusteru můžete použít následující postupy:

* **Zkopírujte jar aplikace do objektu blob Azure Storage** přidruženého ke clusteru. Můžete k tomu použít nástroj příkazového řádku [**AzCopy**](../../storage/common/storage-use-azcopy.md). Existuje také řada dalších klientů, které můžete k nahrání dat použít. Další informace najdete v informacích o [nahrání dat pro úlohy Apache Hadoop v HDInsight](../hdinsight-upload-data.md).

* **Použijte Apache Livy k vzdálenému odeslání úlohy aplikace** do clusteru Spark. Clustery Spark ve službě HDInsight zahrnují rozhraní Livy, které zveřejňuje koncové body REST pro vzdálené odesílání úloh Sparku. Další informace najdete v tématu [vzdálené odesílání Apache Spark úloh pomocí Apache Livy s clustery Spark v HDInsight](apache-spark-livy-rest-interface.md).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud nebudete tuto aplikaci nadále používat, odstraňte cluster, který jste vytvořili, pomocí následujících kroků:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

1. Do **vyhledávacího** pole v horní části zadejte **HDInsight**.

1. V části **služby**vyberte **clustery HDInsight** .

1. V seznamu clusterů HDInsight, které se zobrazí, vyberte **...** vedle clusteru, který jste vytvořili pro účely tohoto kurzu.

1. Vyberte **Odstranit**. Vyberte **Ano**.

![Portál Azure na webu HDInsight – odstranění clusteru](./media/apache-spark-create-standalone-application/hdinsight-azure-portal-delete-cluster.png "Odstranit cluster HDInsight")

## <a name="next-step"></a>Další krok

V tomto článku jste zjistili, jak vytvořit aplikaci Apache Spark Scala. V dalším článku zjistíte, jak tuto aplikaci spustit v clusteru HDInsight Spark pomocí Livy.

> [!div class="nextstepaction"]
>[Vzdálené spouštění úloh na clusteru Apache Spark s využitím Apache Livy](./apache-spark-livy-rest-interface.md)
