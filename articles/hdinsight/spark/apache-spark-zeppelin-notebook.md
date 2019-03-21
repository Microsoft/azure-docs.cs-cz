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
ms.date: 03/04/2019
ms.openlocfilehash: 18660b41f4413d3ae1cd820cd645ddb540397906
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/18/2019
ms.locfileid: "58095891"
---
# <a name="use-apache-zeppelin-notebooks-with-apache-spark-cluster-on-azure-hdinsight"></a>Použití poznámkových bloků Apache Zeppelin s clusterem Apache Spark v Azure HDInsight

Clustery HDInsight Spark zahrnují [Apache Zeppelin](https://zeppelin.apache.org/) poznámkových bloků, které můžete použít ke spuštění [Apache Spark](https://spark.apache.org/) úlohy. V tomto článku se dozvíte, jak používat Poznámkový blok Zeppelin v clusteru HDInsight.

**Požadavky:**

* Předplatné Azure. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Cluster Apache Spark ve službě HDInsight. Pokyny najdete v tématu [Vytváření clusterů Apache Spark ve službě Azure HDInsight](apache-spark-jupyter-spark-sql.md).
* Schéma identifikátoru URI jako primární úložiště vašich clusterů. To může být `wasb://` Azure Blob Storage, `abfs://` pro Azure Data Lake Storage Gen2 nebo `adl://` pro Azure Data Lake Storage Gen1. Pokud pro úložiště objektů Blob nebo Data Lake Storage Gen2 je povoleno zabezpečený přenos, identifikátor URI by `wasbs://` nebo `abfss://`v uvedeném pořadí.  Viz také [vyžadovat zabezpečený přenos ve službě Azure Storage](../../storage/common/storage-require-secure-transfer.md) Další informace.

## <a name="launch-an-apache-zeppelin-notebook"></a>Spuštění Zeppelinu Apache poznámkového bloku

1. Z clusteru Spark **přehled**vyberte **Poznámkový blok Zeppelin** z **řídicí panely clusteru**. Zadejte přihlašovací údaje Správce clusteru.  

   > [!NOTE]  
   > Můžete také kontaktovat Poznámkový blok Zeppelin pro váš cluster tak, že otevřete následující adresu URL v prohlížeči. Nahraďte **CLUSTERNAME** názvem clusteru:
   >
   > `https://CLUSTERNAME.azurehdinsight.net/zeppelin`

2. Vytvořte nový poznámkový blok. V podokně záhlaví, přejděte na **Poznámkový blok** > **vytvořit nová poznámka**.

    ![Vytvořte nový poznámkový blok Zeppelin](./media/apache-spark-zeppelin-notebook/hdinsight-create-zeppelin-notebook.png "vytvořte nový poznámkový blok Zeppelin")

    Zadejte název pro poznámkový blok a pak vyberte **vytvořit Poznámka**.

3. Zajistěte, aby Poznámkový blok záhlaví se zobrazuje připojených stav. Je označena zeleně tečky v pravém horním rohu.

    ![Stav Poznámkový blok Zeppelin](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-connected.png "stav Poznámkový blok Zeppelin")

4. Načtěte vzorová data do dočasné tabulky. Při vytváření clusteru Spark v HDInsight, ukázkový datový soubor `hvac.csv`, se zkopíruje do přidruženého účtu úložiště v rámci `\HdiSamples\SensorSampleData\hvac`.

    Do prázdné odstavce, který se vytvoří ve výchozím nastavení nový poznámkový blok vložte následující fragment kódu.

    ```scala
    %livy2.spark
    //The above magic instructs Zeppelin to use the Livy Scala interpreter

    // Create an RDD using the default Spark context, sc
    val hvacText = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

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
    ```

    Stisknutím klávesy **SHIFT + ENTER** nebo klikněte na tlačítko **Přehrát** tlačítko pro odstavec spusťte fragment kódu. Stav v pravém horním rohu odstavce by měl pokračovat z PŘIPRAVENÝ, ČEKAJÍCÍ, BĚŽÍ na DOKONČENO. Ve výstupu se zobrazí v dolní části stejném paragraph. Na snímku obrazovky bude vypadat nějak takto:

    ![Vytvořte dočasnou tabulku z nezpracovaných dat](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-load-data.png "vytvořte dočasnou tabulku z nezpracovaných dat")

    Můžete také zadat název pro každý odstavec. V pravém horním rohu odstavce, vyberte **nastavení** ikonu (velkými ozubenými) a pak vyberte **zobrazit nadpis**.  

    > [!NOTE]  
    > překladač spark2 % se nepodporuje v poznámkových bloků Zeppelin ve všech verzích HDInsight a % TV překladač nebudou z HDInsight 4.0 a vyšší podporovány.

5. Teď můžete spustit příkazů Spark SQL `hvac` tabulky. Vložte následující dotaz do nový odstavec. Dotaz načte ID budovy a rozdíl mezi cíl a skutečné teploty pro každou vytváření k danému datu. Stisknutím klávesy **SHIFT + ENTER**.

    ```sql
    %sql
    select buildingID, (targettemp - actualtemp) as temp_diff, date from hvac where date = "6/1/13" 
    ```  

    **% Sql** příkaz na začátku říká poznámkovému bloku určený interpret Livy Scala.

6. Vyberte **pruhový graf** ikonu ke změně zobrazení.  **nastavení**, která se zobrazí po výběru **pruhový graf**, umožňuje zvolit **klíče**, a **hodnoty**.  Následující snímek obrazovky ukazuje výstup.

    ![Spuštěním příkazu Spark SQL pomocí poznámkového bloku](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-spark-query-1.png "spuštěním příkazu Spark SQL pomocí poznámkového bloku")

7. Můžete také spustit příkazů Spark SQL pomocí proměnných v dotazu. Další fragment kódu ukazuje, jak definovat proměnnou, `Temp`, možné hodnoty chcete provádět dotazy pomocí dotazu. Při prvním spuštění dotazu, rozevíracího seznamu automaticky vyplní hodnoty, které jste zadali pro proměnnou.

    ```sql
    %sql  
    select buildingID, date, targettemp, (targettemp - actualtemp) as temp_diff from hvac where targettemp > "${Temp = 65,65|75|85}"
    ```

    Vložte tento fragment kódu do nový odstavec a stiskněte klávesu **SHIFT + ENTER**. Potom vyberte **65** z **Temp** rozevíracího seznamu tis. 

8. Vyberte **pruhový graf** ikonu ke změně zobrazení.  Potom vyberte **nastavení** a proveďte následující změny:

   * **Skupiny:**  Přidat **targettemp**.  
   * **Hodnoty:** 1. Odebrat **datum**.  2. Přidat **temp_diff**.  3.  Změnit agregátoru z **součet** k **AVG**.  

     Následující snímek obrazovky ukazuje výstup.

     ![Spuštěním příkazu Spark SQL pomocí poznámkového bloku](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-spark-query-2.png "spuštěním příkazu Spark SQL pomocí poznámkového bloku")

9. Restartujte překladač Livy ukončíte aplikaci. Uděláte to tak, otevřete nastavení překladač výběrem přihlášeného uživatelského jména v pravém horním rohu a pak vyberte **překladač**.  

    ![Spuštění interpretu](./media/apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "výstup Hive")

10. Přejděte k položce **livy**a pak vyberte **restartovat**.  Vyberte **OK** do příkazového řádku.

    ![Restartujte Livy intepreter](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-restart-interpreter.png "restartovat Zeppelin intepreter")

## <a name="how-do-i-use-external-packages-with-the-notebook"></a>Použití externích balíčků s poznámkového bloku
Poznámkový blok Zeppelin můžete nakonfigurovat v clusteru Apache Spark v HDInsight používat externí, komunitou balíčky, které nejsou součástí out-of-the-box v clusteru. Můžete vyhledávat [úložiště Maven](https://search.maven.org/) pro úplný seznam balíčků, které jsou k dispozici. Seznam dostupných balíčků můžete získat také z jiných zdrojů. Například je k dispozici na úplný seznam balíčků z komunity [Spark balíčky](https://spark-packages.org/).

V tomto článku, uvidíte, jak používat [spark csv](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) balíček s poznámkovým blokem Jupyter.

1. Otevřete nastavení překladač. V pravém horním rohu vyberte přihlášeného uživatelského jména a pak vyberte **překladač**.

    ![Spuštění interpretu](./media/apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "výstup Hive")

2. Přejděte k položce **livy**a pak vyberte **upravit**.

    ![Změnit nastavení překladač](./media/apache-spark-zeppelin-notebook/zeppelin-use-external-package-1.png "změnit nastavení interpretu")

3. Přidejte nový klíč s názvem `livy.spark.jars.packages`a nastavte jej na hodnotu ve formátu `group:id:version`. Takže pokud chcete použít [spark sdíleného svazku clusteru](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) balíčku, musíte nastavit jako hodnotu klíče k `com.databricks:spark-csv_2.10:1.4.0`.

    ![Změnit nastavení překladač](./media/apache-spark-zeppelin-notebook/zeppelin-use-external-package-2.png "změnit nastavení interpretu")

    Vyberte **Uložit** a restartujte Livy překladač.

4. Pokud chcete pochopit, jak dorazí na hodnotu klíče zadaného výše, tady je způsob.
   
    a. Vyhledejte balíček úložiště Maven. Pro účely tohoto kurzu jsme použili [spark csv](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar).
   
    b. Z úložiště, shromážděte hodnoty **GroupId**, **ArtifactId**, a **verze**.
   
    ![Použití externích balíčků s Poznámkový blok Jupyter](./media/apache-spark-zeppelin-notebook/use-external-packages-with-jupyter.png "použití externích balíčků s Poznámkový blok Jupyter")
   
    c. Zřetězit tři hodnoty oddělené dvojtečkou (**:**).
   
        com.databricks:spark-csv_2.10:1.4.0

## <a name="where-are-the-zeppelin-notebooks-saved"></a>Kde jsou uloženy poznámkových bloků Zeppelin
Poznámkových bloků Zeppelin se uloží do hlavním uzlům clusteru. Takže pokud je cluster odstranit, se odstraní také poznámkových bloků. Pokud chcete pro zachování vašich poznámkových bloků pro pozdější použití v jiných clusterech, je nutné je po dokončení spuštění úlohy exportovat. Chcete-li exportovat Poznámkový blok, vyberte **exportovat** ikonu, jak je znázorněno na následujícím obrázku.

![Stáhněte si Poznámkový blok](./media/apache-spark-zeppelin-notebook/zeppelin-download-notebook.png "stáhnout poznámkového bloku")

Poznámkový blok tím uložíte jako soubor JSON v umístění stahování.

## <a name="livy-session-management"></a>Správa relací Livy
Při spuštění prvního odstavce kód v Poznámkový blok Zeppelin se vytvoří nová relace Livy ve vašem clusteru HDInsight Spark. Tato relace je sdílen mezi všechny následně vytvořené poznámkových bloků Zeppelin. Pokud z nějakého důvodu Livy relace je ukončen (restartování clusteru atd.), nebudete moci spouštět úlohy z Poznámkový blok Zeppelin.

V takovém případě musíte provést následující kroky předtím, než můžete začít spouštět úlohy z Poznámkový blok Zeppelin.  

1. Restartujte překladač Livy z Poznámkový blok Zeppelin. Uděláte to tak, otevřete nastavení překladač výběrem přihlášeného uživatelského jména v pravém horním rohu a pak vyberte **překladač**.

    ![Spuštění interpretu](./media/apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "výstup Hive")

2. Přejděte k položce **livy**a pak vyberte **restartovat**.

    ![Restartujte Livy intepreter](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-restart-interpreter.png "restartovat Zeppelin intepreter")

3. Spuštění buňky kódu z existující Poznámkový blok Zeppelin. Tím se vytvoří nová relace Livy v clusteru HDInsight.

## <a name="seealso"></a>Viz také
* [Přehled: Apache Spark v Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Scénáře
* [Apache Spark s BI: Provádějte interaktivní analýzy dat pomocí Sparku v HDInsight pomocí nástrojů BI](apache-spark-use-bi-tools.md)
* [Apache Spark s Machine Learning: Použití Sparku v HDInsight pro analýzu stavební teploty pomocí dat HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark s Machine Learning: Použití Sparku v HDInsight k předpovědím výsledků kontroly potravin](apache-spark-machine-learning-mllib-ipython.md)
* [Analýza protokolu webu pomocí Apache Spark v HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Vytvoření a spouštění aplikací
* [Vytvoření samostatné aplikace pomocí Scala](apache-spark-create-standalone-application.md)
* [Vzdálené spouštění úloh na clusteru Apache Spark pomocí Apache Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Nástroje a rozšíření
* [Pomocí modulu plug-in nástroje HDInsight pro IntelliJ IDEA pro vytvoření a odesílání aplikací Apache Spark Scala](apache-spark-intellij-tool-plugin.md)
* [Použití modulu plug-in nástroje HDInsight pro IntelliJ IDEA pro vzdálené ladění aplikací Apache Spark](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Jádra dostupná pro poznámkový blok Jupyter v clusteru Apache Spark pro HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Použití externích balíčků s poznámkovými bloky Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalace Jupyteru do počítače a připojení ke clusteru HDInsight Spark](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Správa prostředků
* [Správa prostředků v clusteru Apache Spark v Azure HDInsight](apache-spark-resource-manager.md)
* [Sledování a ladění úloh spuštěných v clusteru Apache Spark v HDInsight](apache-spark-job-debugging.md)

[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[azure-purchase-options]: https://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: https://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[azure-create-storageaccount]:../../storage/common/storage-create-storage-account.md 
