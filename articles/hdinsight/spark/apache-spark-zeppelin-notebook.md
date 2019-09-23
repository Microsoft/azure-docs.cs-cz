---
title: Použití poznámkových bloků Zeppelin s clusterem Apache Spark v Azure HDInsight
description: Podrobné pokyny k použití poznámkových bloků Zeppelin s clustery s Apache Spark v Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/04/2019
ms.openlocfilehash: 26634e2fe23e0a23540638c4559af6e11eccbe72
ms.sourcegitcommit: a19bee057c57cd2c2cd23126ac862bd8f89f50f5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/23/2019
ms.locfileid: "71180728"
---
# <a name="use-apache-zeppelin-notebooks-with-apache-spark-cluster-on-azure-hdinsight"></a>Použití poznámkových bloků Apache Zeppelin s clusterem Apache Spark v Azure HDInsight

Clustery HDInsight Spark obsahují poznámkové bloky [Apache Zeppelin](https://zeppelin.apache.org/) , které můžete použít ke spouštění úloh [Apache Spark](https://spark.apache.org/) . V tomto článku se dozvíte, jak používat Poznámkový blok Zeppelin v clusteru HDInsight.

**Požadavky:**

* Předplatné Azure. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Cluster Apache Spark ve službě HDInsight. Pokyny najdete v tématu [Vytváření clusterů Apache Spark ve službě Azure HDInsight](apache-spark-jupyter-spark-sql.md).
* Schéma identifikátoru URI pro primární úložiště clusterů. To je `wasb://` pro Azure `abfs://` BLOB Storage pro Azure Data Lake Storage Gen2 nebo `adl://` pro Azure Data Lake Storage Gen1. Pokud je pro Blob Storage povolený zabezpečený přenos, identifikátor URI `wasbs://`by byl.  Další informace najdete [v tématu o vyžadování zabezpečeného přenosu v Azure Storage](../../storage/common/storage-require-secure-transfer.md) .

## <a name="launch-an-apache-zeppelin-notebook"></a>Spuštění poznámkového bloku Apache Zeppelin

1. V **přehledu**clusteru Spark vyberte **Poznámkový blok Zeppelin** z **řídicích panelů clusteru**. Zadejte přihlašovací údaje Správce clusteru.  

   > [!NOTE]  
   > Do poznámkového bloku Zeppelin se můžete dostat i tak, že v prohlížeči otevřete následující adresu URL. Nahraďte **CLUSTERNAME** názvem clusteru:
   >
   > `https://CLUSTERNAME.azurehdinsight.net/zeppelin`

2. Vytvořte nový poznámkový blok. V podokně záhlaví přejděte na **Poznámkový blok** > **vytvořit novou poznámku**.

    ![Vytvoření nového poznámkového bloku Zeppelin](./media/apache-spark-zeppelin-notebook/hdinsight-create-zeppelin-notebook.png "Vytvoření nového poznámkového bloku Zeppelin")

    Zadejte název pro Poznámkový blok a pak vyberte **vytvořit poznámku**.

3. Ujistěte se, že se v hlavičce poznámkového bloku zobrazuje stav připojeno. Je označena zelenou tečkou v pravém horním rohu.

    ![Stav poznámkového bloku Zeppelin](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-connected.png "Stav poznámkového bloku Zeppelin")

4. Načtěte vzorová data do dočasné tabulky. Při vytváření clusteru Spark ve službě HDInsight se Ukázkový datový soubor `hvac.csv`zkopíruje do přidruženého účtu úložiště v části. `\HdiSamples\SensorSampleData\hvac`

    V novém poznámkovém bloku vložte následující fragment kódu do prázdného odstavce, který je ve výchozím nastavení vytvořen.

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

    Stiskněte **SHIFT + ENTER** nebo klikněte na tlačítko **Přehrát** pro odstavec a spusťte fragment kódu. Stav v pravém horním rohu odstavce by měl pokračovat od připraveného až do dokončení. Výstup se zobrazí v dolní části stejného odstavce. Snímek obrazovky vypadá takto:

    ![Vytvořit dočasnou tabulku z nezpracovaných dat](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-load-data.png "Vytvořit dočasnou tabulku z nezpracovaných dat")

    Můžete také zadat název každého odstavce. V pravém horním rohu odstavce vyberte ikonu **Nastavení** (Sprocket) a pak vyberte **Zobrazit nadpis**.  

    > [!NOTE]  
    > % spark2 překladače není podporován v poznámkových blocích Zeppelin napříč všemi verzemi HDInsight a překladač% SH nebude podporován od HDInsight 4,0 a vyšší.

5. V `hvac` tabulce teď můžete spustit příkazy Spark SQL. Vložte následující dotaz do nového odstavce. Dotaz načte ID sestavení a rozdíl mezi cílovou a skutečnou teplotou pro každé sestavení v daném datu. Stiskněte **SHIFT + ENTER**.

    ```sql
    %sql
    select buildingID, (targettemp - actualtemp) as temp_diff, date from hvac where date = "6/1/13" 
    ```  

    Příkaz **% SQL** na začátku upozorňuje na Poznámkový blok na použití překladače Scala Livy.

6. Vyberte ikonu **pruhového grafu** pro změnu zobrazení.  **Nastavení**, která se zobrazí po výběru **pruhového grafu**, vám umožní vybrat **klíče**a **hodnoty**.  Výstup se zobrazí na následujícím snímku obrazovky.

    ![Spuštění příkazu Spark SQL pomocí notebook1](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-spark-query-1.png "Spuštění příkazu Spark SQL pomocí notebook1")

7. Příkazy Spark SQL můžete také spouštět pomocí proměnných v dotazu. Další fragment kódu ukazuje, jak definovat proměnnou `Temp`v dotazu s možnými hodnotami, se kterými se chcete dotazovat. Při prvním spuštění dotazu se automaticky vyplní rozevírací seznam hodnotami, které jste zadali pro proměnnou.

    ```sql
    %sql  
    select buildingID, date, targettemp, (targettemp - actualtemp) as temp_diff from hvac where targettemp > "${Temp = 65,65|75|85}"
    ```

    Vložte tento fragment kódu do nového odstavce a stiskněte **SHIFT + ENTER**. Pak v rozevíracím seznamu **tempa** vyberte **65** . 

8. Vyberte ikonu **pruhového grafu** pro změnu zobrazení.  Pak vyberte **Nastavení** a proveďte následující změny:

   * **Skupiny**  Přidat **targettemp**.  
   * **Hodnota** 1. Odebrat **Datum**.  2. Přidat **temp_diff**.  3.  Změňte agregátor z **Sum** na **AVG**.  

     Výstup se zobrazí na následujícím snímku obrazovky.

     ![Spuštění příkazu Spark SQL pomocí notebook2](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-spark-query-2.png "Spuštění příkazu Spark SQL pomocí notebook2")

9. Pro ukončení aplikace restartujte překladač Livy. Uděláte to tak, že v pravém horním rohu otevřete přihlášené uživatelské jméno a pak vyberete **Interpret**.  

    ![Spustit překladač](./media/apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "Výstup podregistru")

10. Přejděte na **Livy**a pak vyberte **restartovat**.  Na příkazovém řádku vyberte **OK** .

    ![Restartování překladače Livy](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-restart-interpreter.png "Restartování překladače Zeppelin")

## <a name="how-do-i-use-external-packages-with-the-notebook"></a>Návody použít pro Poznámkový blok externí balíčky?
Poznámkový blok Zeppelin Apache Spark v HDInsight můžete nakonfigurovat tak, aby používal externí balíčky, které se podílejí na komunitě, které nejsou zahrnuté do clusteru. Úplný seznam dostupných balíčků můžete vyhledat v [úložišti Maven](https://search.maven.org/) . Můžete také získat seznam dostupných balíčků z jiných zdrojů. Například úplný seznam balíčků, které jsou součástí komunity, je k dispozici v [balíčcích Spark](https://spark-packages.org/).

V tomto článku se dozvíte, jak pomocí poznámkového bloku Jupyter použít balíček [Spark-CSV](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) .

1. Otevřete nastavení překladače. V pravém horním rohu vyberte přihlášené uživatelské jméno a pak vyberte **Interpret**.

    ![Spustit překladač](./media/apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "Výstup podregistru")

2. Přejděte na **Livy**a pak vyberte **Upravit**.

    ![Změnit Interpret settings1](./media/apache-spark-zeppelin-notebook/zeppelin-use-external-package-1.png "Změnit Interpret settings1")

3. Přidejte nový klíč s názvem `livy.spark.jars.packages`a nastavte jeho hodnotu ve formátu. `group:id:version` Pokud tedy chcete použít balíček [Spark-CSV](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) , je nutné nastavit hodnotu klíče na `com.databricks:spark-csv_2.10:1.4.0`.

    ![Změnit Interpret settings2](./media/apache-spark-zeppelin-notebook/zeppelin-use-external-package-2.png "Změnit Interpret settings2")

    Vyberte **Uložit** a pak restartujte překladač Livy.

4. Pokud chcete pochopit, jak se dorazí na hodnotu klíče, který jste zadali výše, tady je postup.
   
    a. Vyhledejte balíček v úložišti Maven. V tomto článku jsme použili [Spark-CSV](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar).
   
    b. Z úložiště Shromážděte hodnoty pro ID **skupiny**, **ArtifactId**a **verzi**.
   
    ![Použití externích balíčků s poznámkovým blokem Jupyter](./media/apache-spark-zeppelin-notebook/use-external-packages-with-jupyter.png "Použití externích balíčků s poznámkovým blokem Jupyter")
   
    c. Zřetězí tři hodnoty oddělené dvojtečkou ( **:** ).
   
        com.databricks:spark-csv_2.10:1.4.0

## <a name="where-are-the-zeppelin-notebooks-saved"></a>Kam se ukládají poznámkové bloky Zeppelin?
Poznámkové bloky Zeppelin se ukládají do hlavních clusteru. Takže pokud cluster odstraníte, poznámkové bloky se taky odstraní. Pokud chcete zachovat své poznámkové bloky pro pozdější použití v jiných clusterech, musíte je po dokončení spouštění úloh exportovat. Chcete-li exportovat Poznámkový blok, vyberte ikonu **exportu** , jak je znázorněno na obrázku níže.

![Stáhnout Poznámkový blok](./media/apache-spark-zeppelin-notebook/zeppelin-download-notebook.png "Stáhnout Poznámkový blok")

Tím uložíte Poznámkový blok jako soubor JSON do umístění pro stahování.

## <a name="livy-session-management"></a>Správa relací Livy
Když spustíte první odstavec kódu v poznámkovém bloku Zeppelin, vytvoří se nová relace Livy v clusteru HDInsight Spark. Tato relace se sdílí ve všech poznámkových blocích Zeppelin, které následně vytvoříte. Pokud z nějakého důvodu dojde k usmrcení relace Livy (restart clusteru atd.), nebudete moct spouštět úlohy z poznámkového bloku Zeppelin.

V takovém případě je třeba provést následující kroky, aby bylo možné začít spouštět úlohy z Zeppelin poznámkového bloku.  

1. Restartujte překladač Livy z poznámkového bloku Zeppelin. Uděláte to tak, že v pravém horním rohu otevřete přihlášené uživatelské jméno a pak vyberete **Interpret**.

    ![Spustit překladač](./media/apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "Výstup podregistru")

2. Přejděte na **Livy**a pak vyberte **restartovat**.

    ![Restartování překladače Livy](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-restart-interpreter.png "Restartování překladače Zeppelin")

3. Spustí buňku kódu z existujícího poznámkového bloku Zeppelin. Tím se vytvoří nová relace Livy v clusteru HDInsight.

## <a name="seealso"></a>Viz také
* [Přehled Apache Spark ve službě Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Scénáře
* [Apache Spark s BI: Provádění interaktivní analýzy dat pomocí Sparku v HDInsight pomocí nástrojů BI](apache-spark-use-bi-tools.md)
* [Apache Spark s Machine Learning: Použití Sparku ve službě HDInsight k analýze teploty budovy pomocí dat TVK](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark s Machine Learning: Předpověď výsledků kontroly potravin pomocí Sparku v HDInsight](apache-spark-machine-learning-mllib-ipython.md)
* [Analýza webového protokolu pomocí Apache Spark ve službě HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Vytvoření a spouštění aplikací
* [Vytvoření samostatné aplikace pomocí Scala](apache-spark-create-standalone-application.md)
* [Vzdálené spouštění úloh na clusteru Apache Spark s využitím Apache Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Nástroje a rozšíření
* [Použití modulu plug-in nástrojů HDInsight pro IntelliJ nápad k vytváření a odesílání aplikací v Apache Spark Scala](apache-spark-intellij-tool-plugin.md)
* [Použití modulu plug-in nástrojů HDInsight pro IntelliJ NÁPADu při vzdáleném ladění aplikací Apache Spark](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Jádra dostupná pro Poznámkový blok Jupyter v clusteru Apache Spark pro HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Použití externích balíčků s poznámkovými bloky Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalace Jupyteru do počítače a připojení ke clusteru HDInsight Spark](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Spravovat prostředky
* [Správa prostředků v clusteru Apache Spark v Azure HDInsight](apache-spark-resource-manager.md)
* [Sledování a ladění úloh spuštěných v clusteru Apache Spark v HDInsight](apache-spark-job-debugging.md)

[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[azure-purchase-options]: https://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: https://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[azure-create-storageaccount]:../../storage/common/storage-create-storage-account.md 
