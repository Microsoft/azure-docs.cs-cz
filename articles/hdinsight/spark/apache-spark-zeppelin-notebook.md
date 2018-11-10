---
title: Použití poznámkových bloků Zeppelin s clusterem Apache Spark v Azure HDInsight
description: Podrobné pokyny k používání poznámkových bloků Zeppelin s clustery Apache Spark v Azure HDInsight.
services: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/21/2018
ms.openlocfilehash: b4c79ada0a243d50aad64600de6dfdfebd59b9da
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2018
ms.locfileid: "51005832"
---
# <a name="use-zeppelin-notebooks-with-apache-spark-cluster-on-azure-hdinsight"></a>Použití poznámkových bloků Zeppelin s clusterem Apache Spark v Azure HDInsight

Clustery HDInsight Spark zahrnují poznámkových bloků Zeppelin, které můžete použít ke spuštění úlohy Spark. V tomto článku se dozvíte, jak používat Poznámkový blok Zeppelin v clusteru HDInsight.

**Požadavky:**

* Předplatné Azure. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Cluster Apache Spark ve službě HDInsight. Pokyny najdete v tématu [Vytváření clusterů Apache Spark ve službě Azure HDInsight](apache-spark-jupyter-spark-sql.md).

## <a name="launch-a-zeppelin-notebook"></a>Spusťte Poznámkový blok Zeppelin
1. Z okna clusteru Spark klikněte na tlačítko **řídicí panel clusteru**a potom klikněte na tlačítko **Poznámkový blok Zeppelin**. Po vyzvání zadejte přihlašovací údaje správce clusteru.
   
   > [!NOTE]
   > Můžete také kontaktovat Poznámkový blok Zeppelin pro váš cluster tak, že otevřete následující adresu URL v prohlížeči. Nahraďte **CLUSTERNAME** názvem clusteru:
   > 
   > `https://CLUSTERNAME.azurehdinsight.net/zeppelin`
   > 
   > 
1. Vytvořte nový poznámkový blok. Podokno záhlaví, klikněte na **Poznámkový blok**a potom klikněte na tlačítko **vytvořit nová poznámka**.
   
    ![Vytvořte nový poznámkový blok Zeppelin](./media/apache-spark-zeppelin-notebook/hdinsight-create-zeppelin-notebook.png "vytvořte nový poznámkový blok Zeppelin")
   
    Zadejte název poznámkového bloku a pak klikněte na tlačítko **vytvořit Poznámka**.
1. Také zkontrolujte, zda že záhlaví poznámkového bloku zobrazí stav připojení. Je označena zeleně tečky v pravém horním rohu.
   
    ![Stav Poznámkový blok Zeppelin](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-connected.png "stav Poznámkový blok Zeppelin")
1. Načtěte vzorová data do dočasné tabulky. Při vytváření clusteru Spark v HDInsight, ukázkový datový soubor **hvac.csv**, se zkopíruje do přidruženého účtu úložiště v rámci **\HdiSamples\SensorSampleData\hvac**.
   
    Do prázdné odstavce, který se vytvoří ve výchozím nastavení nový poznámkový blok vložte následující fragment kódu.
   
        %livy2.spark
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
   
    Stisknutím klávesy **SHIFT + ENTER** nebo klikněte na tlačítko **Přehrát** tlačítko pro odstavec spusťte fragment kódu. Stav v pravém horním rohu odstavce by měl pokračovat z PŘIPRAVENÝ, ČEKAJÍCÍ, BĚŽÍ na DOKONČENO. Ve výstupu se zobrazí v dolní části stejném paragraph. Na snímku obrazovky bude vypadat nějak takto:
   
    ![Vytvořte dočasnou tabulku z nezpracovaných dat](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-load-data.png "vytvořte dočasnou tabulku z nezpracovaných dat")
   
    Můžete také zadat název pro každý odstavec. V pravém rohu klikněte **nastavení** ikonu a pak klikněte na tlačítko **zobrazit nadpis**.

> [!NOTE]
> překladač spark2 % se nepodporuje v poznámkových bloků Zeppelin ve všech verzích HDInsight a % TV překladač nebudou z HDInsight 4.0 a vyšší podporovány.
>

1. Teď můžete spustit příkazů Spark SQL **hvac** tabulky. Vložte následující dotaz do nový odstavec. Dotaz načte ID budovy a rozdíl mezi cíl a skutečné teploty pro každou vytváření k danému datu. Stisknutím klávesy **SHIFT + ENTER**.
   
        %sql
        select buildingID, (targettemp - actualtemp) as temp_diff, date from hvac where date = "6/1/13" 
   
    **% Sql** příkaz na začátku říká poznámkovému bloku určený interpret Livy Scala.
   
    Následující snímek obrazovky ukazuje výstup.
   
    ![Spuštěním příkazu Spark SQL pomocí poznámkového bloku](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-spark-query-1.png "spuštěním příkazu Spark SQL pomocí poznámkového bloku")
   
     Chcete-li přepnout mezi různé reprezentace pro stejný výstup, klikněte na tlačítko Možnosti zobrazení (zvýrazněno obdélník). Klikněte na tlačítko **nastavení** rozhodnout, jaké consitutes klíče a hodnoty ve výstupu. Snímek obrazovky výše používá **buildingID** jako klíč a průměr **temp_diff** jako hodnotu.
1. Můžete také spustit příkazů Spark SQL pomocí proměnných v dotazu. Další fragment kódu ukazuje, jak definovat proměnnou, **Temp**, možné hodnoty chcete provádět dotazy pomocí dotazu. Při prvním spuštění dotazu, rozevíracího seznamu automaticky vyplní hodnoty, které jste zadali pro proměnnou.
   
        %sql
        select buildingID, date, targettemp, (targettemp - actualtemp) as temp_diff from hvac where targettemp > "${Temp = 65,65|75|85}" 
   
    Vložte tento fragment kódu do nový odstavec a stiskněte klávesu **SHIFT + ENTER**. Následující snímek obrazovky ukazuje výstup.
   
    ![Spuštěním příkazu Spark SQL pomocí poznámkového bloku](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-spark-query-2.png "spuštěním příkazu Spark SQL pomocí poznámkového bloku")
   
    Pro další dotazy můžete z rozevíracího seznamu vyberte novou hodnotu a spusťte dotaz znovu. Klikněte na tlačítko **nastavení** rozhodnout, jaké consitutes klíče a hodnoty ve výstupu. Snímek obrazovky výše používá **buildingID** jako klíč průměr **temp_diff** jako hodnotu, a **targettemp** jako skupina.
1. Restartujte překladač Livy ukončíte aplikaci. Uděláte to tak, otevřete nastavení interpret v uživatelské jméno v pravém horním rohu klikněte na přihlášeného a potom klikněte na tlačítko **překladač**.
   
    ![Spuštění interpretu](./media/apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "výstup Hive")
1. Přejděte do nastavení překladač Livy a potom klikněte na tlačítko **restartovat**.
   
    ![Restartujte Livy intepreter](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-restart-interpreter.png "restartovat Zeppelin intepreter")

## <a name="how-do-i-use-external-packages-with-the-notebook"></a>Použití externích balíčků s poznámkového bloku
Poznámkový blok Zeppelin můžete nakonfigurujete pro použití externího, komunitou balíčky, které nejsou součástí out-of-the-box v clusteru v clusteru Apache Spark v HDInsight (Linux). Můžete vyhledávat [úložiště Maven](http://search.maven.org/) pro úplný seznam balíčků, které jsou k dispozici. Seznam dostupných balíčků můžete získat také z jiných zdrojů. Například je k dispozici na úplný seznam balíčků z komunity [Spark balíčky](http://spark-packages.org/).

V tomto článku, uvidíte, jak používat [spark csv](http://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) balíček s poznámkovým blokem Jupyter.

1. Otevřete nastavení překladač. V pravém horním rohu klikněte na uživatelské jméno přihlášeného a potom klikněte na tlačítko **překladač**.
   
    ![Spuštění interpretu](./media/apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "výstup Hive")
1. Přejděte do nastavení překladač Livy a potom klikněte na tlačítko **upravit**.
   
    ![Změnit nastavení překladač](./media/apache-spark-zeppelin-notebook/zeppelin-use-external-package-1.png "změnit nastavení interpretu")
1. Přidejte nový klíč, volá **livy.spark.jars.packages** a nastavení jeho hodnoty ve formátu `group:id:version`. Takže pokud chcete použít [spark sdíleného svazku clusteru](http://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) balíčku, musíte nastavit jako hodnotu klíče k `com.databricks:spark-csv_2.10:1.4.0`.
   
    ![Změnit nastavení překladač](./media/apache-spark-zeppelin-notebook/zeppelin-use-external-package-2.png "změnit nastavení interpretu")
   
    Klikněte na tlačítko **Uložit** a restartujte Livy překladač.
1. **Tip**: Pokud chcete pochopit, jak dorazí na hodnotu klíče zadaného výše, tady je způsob.
   
    a. Vyhledejte balíček úložiště Maven. Pro účely tohoto kurzu jsme použili [spark csv](http://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar).
   
    b. Z úložiště, shromážděte hodnoty **GroupId**, **ArtifactId**, a **verze**.
   
    ![Použití externích balíčků s Poznámkový blok Jupyter](./media/apache-spark-zeppelin-notebook/use-external-packages-with-jupyter.png "použití externích balíčků s Poznámkový blok Jupyter")
   
    c. Zřetězit tři hodnoty oddělené dvojtečkou (**:**).
   
        com.databricks:spark-csv_2.10:1.4.0

## <a name="where-are-the-zeppelin-notebooks-saved"></a>Kde jsou uloženy poznámkových bloků Zeppelin
Poznámkových bloků Zeppelin se uloží do hlavním uzlům clusteru. Takže pokud je cluster odstranit, se odstraní také poznámkových bloků. Pokud chcete pro zachování vašich poznámkových bloků pro pozdější použití v jiných clusterech, je nutné je po dokončení spuštění úlohy exportovat. Chcete-li exportovat Poznámkový blok, klikněte na tlačítko **exportovat** ikonu, jak je znázorněno na následujícím obrázku.

![Stáhněte si Poznámkový blok](./media/apache-spark-zeppelin-notebook/zeppelin-download-notebook.png "stáhnout poznámkového bloku")

Poznámkový blok tím uložíte jako soubor JSON v umístění stahování.

## <a name="livy-session-management"></a>Správa relací Livy
Při spuštění prvního odstavce kód v Poznámkový blok Zeppelin se vytvoří nová relace Livy ve vašem clusteru HDInsight Spark. Tato relace je sdílen mezi všechny následně vytvořené poznámkových bloků Zeppelin. Pokud z nějakého důvodu Livy relace je ukončen (restartování clusteru atd.), nebudete moci spouštět úlohy z Poznámkový blok Zeppelin.

V takovém případě musíte provést následující kroky předtím, než můžete začít spouštět úlohy z Poznámkový blok Zeppelin. 

1. Restartujte překladač Livy z Poznámkový blok Zeppelin. Uděláte to tak, otevřete nastavení interpret v uživatelské jméno v pravém horním rohu klikněte na přihlášeného a potom klikněte na tlačítko **překladač**.
   
    ![Spuštění interpretu](./media/apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "výstup Hive")
1. Přejděte do nastavení překladač Livy a potom klikněte na tlačítko **restartovat**.
   
    ![Restartujte Livy intepreter](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-restart-interpreter.png "restartovat Zeppelin intepreter")
1. Spuštění buňky kódu z existující Poznámkový blok Zeppelin. Tím se vytvoří nová relace Livy v clusteru HDInsight.

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







