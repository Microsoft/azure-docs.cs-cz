---
title: Použití poznámkových bloků Zeppelin s clusterem Apache Spark v Azure HDInsight | Microsoft Docs
description: Podrobné pokyny o tom, jak používat poznámkových bloků Zeppelin s clustery Apache Spark v Azure HDInsight.
services: hdinsight
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: df489d70-7788-4efa-a089-e5e5006421e2
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 02/21/2018
ms.author: nitinme
ms.openlocfilehash: b2f47dce058af7a39366c06d0b33117a66ed116a
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2018
ms.locfileid: "31521997"
---
# <a name="use-zeppelin-notebooks-with-apache-spark-cluster-on-azure-hdinsight"></a>Použití poznámkových bloků Zeppelin s clusterem Apache Spark v Azure HDInsight

Clustery HDInsight Spark zahrnují poznámkových bloků Zeppelin, které můžete použít ke spuštění úloh Spark. V tomto článku zjistěte, jak používat Zeppelin poznámkového bloku na clusteru HDInsight.

> [!NOTE]
> Poznámkových bloků Zeppelin jsou dostupné pouze pro 1.6.3 Spark v HDInsight 3.5 a 2.1.0 Spark v HDInsight 3.6.
>

**Požadavky:**

* Předplatné Azure. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Cluster Apache Spark v HDInsight. Pokyny najdete v tématu [clusterů vytvořit Apache Spark v Azure HDInsight](apache-spark-jupyter-spark-sql.md).

## <a name="launch-a-zeppelin-notebook"></a>Spusťte Zeppelin Poznámkový blok
1. Z okna clusteru Spark klikněte na tlačítko **řídicí panel clusteru**a potom klikněte na **Zeppelin Poznámkový blok**. Po vyzvání zadejte přihlašovací údaje správce clusteru.
   
   > [!NOTE]
   > Může také dosáhnout Zeppelin Poznámkový blok pro váš cluster tak, že otevřete následující adresu URL v prohlížeči. Nahraďte **CLUSTERNAME** názvem clusteru:
   > 
   > `https://CLUSTERNAME.azurehdinsight.net/zeppelin`
   > 
   > 
2. Vytvořte nový poznámkový blok. V podokně záhlaví, klikněte na tlačítko **poznámkového bloku**a potom klikněte na **vytvořit novou poznámku**.
   
    ![Vytvořte nový poznámkový blok Zeppelin](./media/apache-spark-zeppelin-notebook/hdinsight-create-zeppelin-notebook.png "vytvořte nový poznámkový blok Zeppelin")
   
    Zadejte název pro poznámkový blok a pak klikněte na tlačítko **vytvořit Poznámka**.
3. Taky se ujistěte, že hlavičku poznámkového bloku zobrazí stav připojených. Označuje zelená tečky v pravém horním rohu.
   
    ![Stav poznámkového bloku Zeppelin](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-connected.png "Zeppelin poznámkového bloku stav")
4. Načtěte vzorová data do dočasné tabulky. Když vytvoříte Spark cluster v HDInsight, ukázkový datový soubor, **hvac.csv**, se zkopíruje do přidruženého účtu úložiště v rámci **\HdiSamples\SensorSampleData\hvac**.
   
    V prázdné odstavce, který se vytvoří ve výchozím nastavení v nový poznámkový blok vložte následující fragment kódu.
   
        %livy.spark
        //The above magic instructs Zeppelin to use the Livy Scala interpreter
   
        // Create an RDD using the default Spark context, sc
        val hvacText = sc.textFile("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
   
        // Define a schema
        case class Hvac(date: String, time: String, targettemp: Integer, actualtemp: Integer, buildingID: String)
   
        // Map the values in the .csv file to the schema
        val hvac = hvacText.map(s => s.split(",")).filter(s => s(0) != "Date").map(
            s => Hvac(s(0), 
                    s(1),
                    s(2).toInt,
                    s(3).toInt,
                    s(6)
            )
        ).toDF()
   
        // Register as a temporary table called "hvac"
        hvac.registerTempTable("hvac")
   
    Stiskněte klávesu **SHIFT + ENTER** nebo klikněte na tlačítko **přehrání** tlačítko odstavce ke spuštění fragmentu. Stav na pravém horním rohu odstavce by měl průběhu z připravené, čeká na vyřízení, SPUŠTĚNÁ na DOKONČENO. Výstup se zobrazí v dolní části stejné odstavce. Na snímku obrazovky vypadá takto:
   
    ![Vytvořte dočasnou tabulku z nezpracovaná data](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-load-data.png "vytvořit dočasnou tabulku od nezpracovaných dat")
   
    Můžete zadat také název jednotlivých odstavců. V pravém rohu, klikněte na **nastavení** ikonu a pak klikněte na tlačítko **zobrazit nadpis**.
5. Teď můžete spustit příkazy Spark SQL na **TVK** tabulky. Vložte následující dotaz nový odstavec. Dotaz načte ID budovy a rozdíl mezi cíl a skutečný teploty pro každé sestavení v určitém dni. Stiskněte klávesu **SHIFT + ENTER**.
   
        %sql
        select buildingID, (targettemp - actualtemp) as temp_diff, date from hvac where date = "6/1/13" 
   
    **% Sql** na začátku informuje používat překladač Livy Scala poznámkového bloku.
   
    Následující snímek obrazovky ukazuje výstup.
   
    ![Spustit příkaz Spark SQL pomocí poznámkového bloku](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-spark-query-1.png "spustit příkaz Spark SQL pomocí poznámkového bloku")
   
     Klikněte na tlačítko Možnosti zobrazení (zvýrazněných v obdélníku) přepínat mezi různé reprezentace pro stejný výstup. Klikněte na tlačítko **nastavení** zvolte co consitutes klíče a hodnoty ve výstupu. Výše uvedený snímek obrazovky používá **buildingID** jako klíč a průměr **temp_diff** jako hodnotu.
6. Můžete také spustit příkazy Spark SQL pomocí proměnných v dotazu. Následující fragment kódu ukazuje, jak k definování proměnné, **Temp**, v dotazu s možné hodnoty mají být zobrazeny s. Při prvním spuštění dotazu, rozevírací seznam se automaticky zadá hodnoty, které jste zadali pro proměnnou.
   
        %sql
        select buildingID, date, targettemp, (targettemp - actualtemp) as temp_diff from hvac where targettemp > "${Temp = 65,65|75|85}" 
   
    Vložte tento fragment kódu nový odstavec a stiskněte klávesu **SHIFT + ENTER**. Následující snímek obrazovky ukazuje výstup.
   
    ![Spustit příkaz Spark SQL pomocí poznámkového bloku](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-spark-query-2.png "spustit příkaz Spark SQL pomocí poznámkového bloku")
   
    Pro následné dotazy můžete vybrat novou hodnotu z rozevíracího seznamu a spusťte dotaz znovu. Klikněte na tlačítko **nastavení** zvolte co consitutes klíče a hodnoty ve výstupu. Výše uvedený snímek obrazovky používá **buildingID** jako klíč průměr **temp_diff** jako hodnotu, a **targettemp** jako skupinu.
7. Překladač Livy ukončete aplikaci restartujte. Uděláte to tak, otevřete překladač nastavení kliknutím na příkaz přihlášeného v uživatelské jméno v pravém horním rohu a pak klikněte na tlačítko **překladač**.
   
    ![Spustí překladač](./media/apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "výstupu podregistru")
8. Přejděte na nastavení překladač Livy a pak klikněte na **restartujte**.
   
    ![Restartujte Livy intepreter](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-restart-interpreter.png "restartujte Zeppelin intepreter")

## <a name="how-do-i-use-external-packages-with-the-notebook"></a>Jak používat externí balíčky s poznámkového bloku?
Můžete nakonfigurovat Zeppelin poznámkového bloku v clusteru Apache Spark v HDInsight (Linux) používat externí, komunity podílí balíčky, které nejsou zahrnuté out-of-the-box v clusteru. Můžete hledat [Maven úložiště](http://search.maven.org/) úplný seznam balíčků, které jsou k dispozici. Seznam dostupných balíčků můžete také získat z jiných zdrojů. Například je k dispozici úplný seznam balíčků podílí komunity [Spark balíčky](http://spark-packages.org/).

V tomto článku, zobrazí se postup použití [spark csv](http://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) balíček s poznámkovým blokem Jupyter.

1. Otevřete nastavení překladač. V pravém horním rohu klikněte přihlášeného v uživatelské jméno a potom klikněte na **překladač**.
   
    ![Spustí překladač](./media/apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "výstupu podregistru")
2. Přejděte na nastavení překladač Livy a pak klikněte na **upravit**.
   
    ![Změna nastavení překladač](./media/apache-spark-zeppelin-notebook/zeppelin-use-external-package-1.png "změnit nastavení překladač")
3. Přidejte nový klíč, nazývá **livy.spark.jars.packages** a nastavení jeho hodnoty ve formátu `group:id:version`. Takže pokud chcete použít [spark csv](http://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) balíčku, je nutné nastavit hodnotu klíče, který se `com.databricks:spark-csv_2.10:1.4.0`.
   
    ![Změna nastavení překladač](./media/apache-spark-zeppelin-notebook/zeppelin-use-external-package-2.png "změnit nastavení překladač")
   
    Klikněte na tlačítko **Uložit** a pak restartujte Livy překladač.
4. **Tip**: Pokud chcete pochopit, jak přijaty ve výše uvedených hodnotu klíče, zde je způsob.
   
    a. Najděte balíček v úložišti Maven. V tomto kurzu jsme použili [spark csv](http://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar).
   
    b. Z úložiště, shromážděte hodnoty **GroupId**, **ArtifactId**, a **verze**.
   
    ![Použijte externí balíčky s Poznámkový blok Jupyter](./media/apache-spark-zeppelin-notebook/use-external-packages-with-jupyter.png "použijte externí balíčky s poznámkového bloku Jupyter")
   
    c. Řetězení tři hodnoty oddělené dvojtečkou (**:**).
   
        com.databricks:spark-csv_2.10:1.4.0

## <a name="where-are-the-zeppelin-notebooks-saved"></a>Kam se ukládají poznámkových bloků Zeppelin?
Poznámkových bloků Zeppelin se uloží do headnodes clusteru. Takže pokud odstranění clusteru, se odstraní také poznámkových bloků. Pokud chcete zachovat poznámkové bloky pro pozdější použití v jiných clusterech, je nutné je po dokončení spuštění úlohy exportovat. Chcete-li exportovat Poznámkový blok, klikněte na tlačítko **exportovat** ikonu, jak je znázorněno na obrázku níže.

![Stáhnout poznámkového bloku](./media/apache-spark-zeppelin-notebook/zeppelin-download-notebook.png "stáhnout poznámkového bloku")

To umožňuje ušetřit poznámkového bloku jako soubor JSON ve vašem umístění stahování.

## <a name="livy-session-management"></a>Správa relací Livy
Když spustíte prvním odstavci kódu v poznámkovém Zeppelin, je vytvořit novou relaci Livy v clusteru HDInsight Spark. Tuto relaci je sdílen na všech poznámkových bloků Zeppelin, které následně vytvoříte. Pokud z nějakého důvodu Livy relace je ukončená (restartování clusteru atd.), nebudete moci spouštět úlohy z Zeppelin poznámkového bloku.

V takovém případě musíte provést následující kroky předtím, než můžete začít spouštět úlohy z Zeppelin Poznámkový blok. 

1. Restartujte překladač Livy z Zeppelin poznámkového bloku. Uděláte to tak, otevřete překladač nastavení kliknutím na příkaz přihlášeného v uživatelské jméno v pravém horním rohu a pak klikněte na tlačítko **překladač**.
   
    ![Spustí překladač](./media/apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "výstupu podregistru")
2. Přejděte na nastavení překladač Livy a pak klikněte na **restartujte**.
   
    ![Restartujte Livy intepreter](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-restart-interpreter.png "restartujte Zeppelin intepreter")
3. Spusťte buňky kódu z existujícího Zeppelin poznámkového bloku. Tím se vytvoří novou relaci Livy v clusteru HDInsight.

## <a name="seealso"></a>Viz také
* [Přehled: Apache Spark v Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Scénáře
* [Spark s BI: Provádějte interaktivní analýzy dat pomocí Sparku v HDInsight pomocí nástrojů BI](apache-spark-use-bi-tools.md)
* [Spark s Machine Learning: Používejte Spark v HDInsight pro analýzu teploty v budově pomocí dat HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Spark s Machine Learning: Používejte Spark v HDInsight k předpovědím výsledků kontrol potravin](apache-spark-machine-learning-mllib-ipython.md)
* [Analýza protokolu webu pomocí Sparku v HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Vytvoření a spouštění aplikací
* [Vytvoření samostatné aplikace pomocí Scala](apache-spark-create-standalone-application.md)
* [Vzdálené spouštění úloh na clusteru Sparku pomocí Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Nástroje a rozšíření
* [Modul plug-in nástroje HDInsight pro IntelliJ IDEA pro vytvoření a odesílání aplikací Spark Scala](apache-spark-intellij-tool-plugin.md)
* [Použití modulu plug-in nástroje HDInsight pro IntelliJ IDEA pro vzdálené ladění aplikací Spark](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Jádra dostupná pro poznámkový blok Jupyter v clusteru Sparku pro HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Použití externích balíčků s poznámkovými bloky Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalace Jupyteru do počítače a připojení ke clusteru HDInsight Spark](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Správa prostředků
* [Správa prostředků v clusteru Apache Spark v Azure HDInsight](apache-spark-resource-manager.md)
* [Sledování a ladění úloh spuštěných v clusteru Apache Spark v HDInsight](apache-spark-job-debugging.md)

[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-create-storageaccount]:../../storage/common/storage-create-storage-account.md 







