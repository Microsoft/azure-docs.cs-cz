---
title: Zeppelin poznámkové bloky & Apache Spark clusteru – Azure HDInsight
description: Podrobné pokyny k použití poznámkových bloků Zeppelin s clustery s Apache Spark v Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/18/2020
ms.openlocfilehash: e313048986beca1991e38ce2e65ea12f954170d2
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/25/2020
ms.locfileid: "77598268"
---
# <a name="use-apache-zeppelin-notebooks-with-apache-spark-cluster-on-azure-hdinsight"></a>Použití poznámkových bloků Apache Zeppelin s clusterem Apache Spark v Azure HDInsight

Clustery HDInsight Spark obsahují poznámkové bloky [Apache Zeppelin](https://zeppelin.apache.org/) , které můžete použít ke spouštění úloh [Apache Spark](https://spark.apache.org/) . V tomto článku se dozvíte, jak používat Poznámkový blok Zeppelin v clusteru HDInsight.

## <a name="prerequisites"></a>Předpoklady

* Cluster Apache Spark ve službě HDInsight. Pokyny najdete v tématu [Vytváření clusterů Apache Spark ve službě Azure HDInsight](apache-spark-jupyter-spark-sql.md).
* Schéma identifikátoru URI pro primární úložiště clusterů. To `wasb://` pro Azure Blob Storage, `abfs://` pro Azure Data Lake Storage Gen2 nebo `adl://` pro Azure Data Lake Storage Gen1. Pokud je pro Blob Storage povolený zabezpečený přenos, `wasbs://`identifikátor URI.  Další informace najdete v tématu [vyžadování zabezpečeného přenosu v Azure Storage](../../storage/common/storage-require-secure-transfer.md) .

## <a name="launch-an-apache-zeppelin-notebook"></a>Spuštění poznámkového bloku Apache Zeppelin

1. V **přehledu**clusteru Spark vyberte **Poznámkový blok Zeppelin** z **řídicích panelů clusteru**. Zadejte přihlašovací údaje Správce clusteru.  

   > [!NOTE]  
   > Do poznámkového bloku Zeppelin se můžete dostat i tak, že v prohlížeči otevřete následující adresu URL. Nahraďte **CLUSTERNAME** názvem clusteru:
   >
   > `https://CLUSTERNAME.azurehdinsight.net/zeppelin`

2. Vytvořte nový poznámkový blok. V podokně záhlaví přejděte na **Poznámkový blok** > **vytvořit novou poznámku**.

    ![Vytvoření nového poznámkového bloku Zeppelin](./media/apache-spark-zeppelin-notebook/hdinsight-create-zeppelin-notebook.png "Vytvoření nového poznámkového bloku Zeppelin")

    Zadejte název pro Poznámkový blok a pak vyberte **vytvořit poznámku**.

3. Ujistěte se, že se v hlavičce poznámkového bloku zobrazuje stav připojeno. Je označený zelenou tečkou v pravém horním rohu.

    ![Stav poznámkového bloku Zeppelin](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-connected.png "Stav poznámkového bloku Zeppelin")

4. Načtěte vzorová data do dočasné tabulky. Když vytvoříte cluster Spark ve službě HDInsight, Ukázkový datový soubor `hvac.csv`, zkopíruje se do přidruženého účtu úložiště v části `\HdiSamples\SensorSampleData\hvac`.

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

    Stiskněte **SHIFT + ENTER** nebo vyberte tlačítko **Přehrát** pro odstavec a spusťte fragment kódu. Stav v pravém horním rohu odstavce by měl pokračovat od připraveného až do dokončení. Výstup se zobrazí v dolní části stejného odstavce. Snímek obrazovky vypadá takto:

    ![Vytvořit dočasnou tabulku z nezpracovaných dat](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-load-data.png "Vytvořit dočasnou tabulku z nezpracovaných dat")

    Můžete také zadat název každého odstavce. V pravém horním rohu odstavce vyberte ikonu **Nastavení** (Sprocket) a pak vyberte **Zobrazit nadpis**.  

    > [!NOTE]  
    > % spark2 překladače není podporován v poznámkových blocích Zeppelin napříč všemi verzemi HDInsight a překladač% SH nebude podporován od HDInsight 4,0 a vyšší.

5. Nyní můžete spustit příkazy Spark SQL na `hvac` tabulce. Vložte následující dotaz do nového odstavce. Dotaz načte ID sestavení a rozdíl mezi cílovou a skutečnou teplotou pro každé sestavení v daném datu. Stiskněte **SHIFT + ENTER**.

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

   * **Skupiny:**  Přidat **targettemp**.  
   * **Hodnoty:** první. Odebrat **Datum**.  2. Přidejte **temp_diff**.  3.  Změňte agregátor z **Sum** na **AVG**.  

     Výstup se zobrazí na následujícím snímku obrazovky.

     ![Spuštění příkazu Spark SQL pomocí notebook2](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-spark-query-2.png "Spuštění příkazu Spark SQL pomocí notebook2")

## <a name="how-do-i-use-external-packages-with-the-notebook"></a>Návody použít pro Poznámkový blok externí balíčky?

Poznámkový blok Zeppelin Apache Spark v HDInsight můžete nakonfigurovat tak, aby používal externí balíčky, které se podílejí na komunitě, které nejsou zahrnuté do clusteru. Úplný seznam dostupných balíčků můžete vyhledat v [úložišti Maven](https://search.maven.org/) . Můžete také získat seznam dostupných balíčků z jiných zdrojů. Například úplný seznam balíčků, které jsou součástí komunity, je k dispozici v [balíčcích Spark](https://spark-packages.org/).

V tomto článku se dozvíte, jak používat balíček [Spark-CSV](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) s poznámkovým blokem Jupyter.

1. Otevřete nastavení překladače. V pravém horním rohu vyberte přihlášené uživatelské jméno a pak vyberte **Interpret**.

    ![Spustit překladač](./media/apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "Výstup podregistru")

2. Přejděte na **livy2**a pak vyberte **Upravit**.

    ![Změnit Interpret settings1](./media/apache-spark-zeppelin-notebook/zeppelin-use-external-package-1.png "Změnit Interpret settings1")

3. Přejděte na klíčovou `livy.spark.jars.packages`a nastavte jeho hodnotu ve formátu `group:id:version`. Pokud tedy chcete použít balíček [Spark-CSV](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) , je nutné nastavit hodnotu klíče na `com.databricks:spark-csv_2.10:1.4.0`.

    ![Změnit Interpret settings2](./media/apache-spark-zeppelin-notebook/zeppelin-use-external-package-2.png "Změnit Interpret settings2")

    Vyberte **Save (Uložit** ) a pak na **OK** , abyste restartovali Livy Interpret.

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

## <a name="use-shiro-to-configure-access-to-zeppelin-interpreters-in-enterprise-security-package-esp-clusters"></a>Pomocí Shiro nakonfigurovat přístup k překladači Zeppelin v clusterech Balíček zabezpečení podniku (ESP)
Jak je uvedeno výše, překladač `%sh` není podporován od HDInsight 4,0 a vyšší. Vzhledem k tomu, že `%sh` překladače zavádí možné problémy se zabezpečením, jako jsou například přístupové karty pomocí příkazů prostředí, bylo odebráno i z clusterů HDInsight 3,6 ESP. To znamená, že `%sh` není k dispozici, když ve výchozím nastavení kliknete na možnost **vytvořit novou poznámku** nebo v uživatelském rozhraní překladače. 

Uživatelé s privilegovanými doménami můžou použít `Shiro.ini` soubor k řízení přístupu k uživatelskému rozhraní překladače. Proto pouze tito uživatelé mohou vytvořit nové interprety `%sh` a nastavit oprávnění u každého nového interpretu `%sh`. K řízení přístupu pomocí souboru `shiro.ini` použijte následující postup:

1. Definujte novou roli pomocí existujícího názvu skupiny domén. V následujícím příkladu je `adminGroupName` skupina privilegovaných uživatelů v AAD. V názvu skupiny Nepoužívejte speciální znaky ani prázdné znaky. Znaky po `=` poskytují oprávnění pro tuto roli. `*` znamená, že skupina má úplná oprávnění.

    ```
    [roles]
    adminGroupName = *
    ```

2. Přidejte novou roli pro přístup k překladačům Zeppelin. V následujícím příkladu jsou všem uživatelům v `adminGroupName` udělen přístup k překladačům Zeppelin a jsou schopny vytvořit nové překladače. Mezi závorky můžete vložit více rolí v `roles[]`oddělené čárkami. Uživatelé s potřebnými oprávněními pak mají přístup k překladačům Zeppelin.

    ```
    [urls]
    /api/interpreter/** = authc, roles[adminGroupName]
    ```

## <a name="livy-session-management"></a>Správa relací Livy

Když spustíte první odstavec kódu v poznámkovém bloku Zeppelin, vytvoří se nová relace Livy v clusteru HDInsight Spark. Tato relace se sdílí ve všech poznámkových blocích Zeppelin, které následně vytvoříte. Pokud z nějakého důvodu dojde k usmrcení relace Livy (restart clusteru atd.), nebudete moct spouštět úlohy z poznámkového bloku Zeppelin.

V takovém případě je třeba provést následující kroky, aby bylo možné začít spouštět úlohy z Zeppelin poznámkového bloku.  

1. Restartujte překladač Livy z poznámkového bloku Zeppelin. Uděláte to tak, že v pravém horním rohu otevřete přihlášené uživatelské jméno a pak vyberete **Interpret**.

    ![Spustit překladač](./media/apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "Výstup podregistru")

2. Přejděte na **livy2**a pak vyberte **restartovat**.

    ![Restartování překladače Livy](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-restart-interpreter.png "Restartování překladače Zeppelin")

3. Spustí buňku kódu z existujícího poznámkového bloku Zeppelin. Tím se vytvoří nová relace Livy v clusteru HDInsight.

## <a name="general-information"></a>Obecné informace

### <a name="validate-service"></a>Ověřit službu

Pokud chcete službu ověřit z Ambari, přejděte do `https://CLUSTERNAME.azurehdinsight.net/#/main/services/ZEPPELIN/summary`, kde název_clusteru je název vašeho clusteru.

Chcete-li ověřit službu z příkazového řádku, SSH k hlavnímu uzlu. Přepněte uživatele na Zeppelin pomocí příkazového `sudo su zeppelin`. Stavové příkazy:

|Příkaz |Popis |
|---|---|
|`/usr/hdp/current/zeppelin-server/bin/zeppelin-daemon.sh status`|Stav služby.|
|`/usr/hdp/current/zeppelin-server/bin/zeppelin-daemon.sh --version`|Verze služby|
|`ps -aux | grep zeppelin`|Identifikujte PID.|

### <a name="log-locations"></a>Umístění protokolů

|Služba |Cesta |
|---|---|
|Zeppelin – Server|/usr/hdp/current/zeppelin-server/|
|Protokoly serveru|/var/log/zeppelin|
|Interpret konfigurace, Shiro, site. XML, log4j|/usr/HDP/Current/Zeppelin-Server/conf nebo/etc/Zeppelin/conf|
|Adresář PID|/var/run/zeppelin|

### <a name="enable-debug-logging"></a>Povolit protokolování ladění

1. Přejděte na `https://CLUSTERNAME.azurehdinsight.net/#/main/services/ZEPPELIN/summary`, kde název_clusteru je název vašeho clusteru.

1. Přejděte do **konfigurace** > **Upřesnit Zeppelin-log4j-Properties** > **log4j_properties_content**.

1. Upravte `log4j.appender.dailyfile.Threshold = INFO` na `log4j.appender.dailyfile.Threshold = DEBUG`.

1. Přidejte `log4j.logger.org.apache.zeppelin.realm=DEBUG`.

1. Uložte změny a restartujte službu.

## <a name="next-steps"></a>Další kroky

[Přehled: Apache Spark v Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Scénáře

* [Apache Spark s BI: provádějte interaktivní analýzy dat pomocí Sparku v HDInsight pomocí nástrojů BI.](apache-spark-use-bi-tools.md)
* [Apache Spark s Machine Learning: pomocí Sparku v HDInsight můžete analyzovat teplotu budovy pomocí dat TVK.](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark s Machine Learning: pomocí Sparku v HDInsight předpovídat výsledky kontroly potravin](apache-spark-machine-learning-mllib-ipython.md)
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

### <a name="manage-resources"></a>Správa prostředků

* [Správa prostředků v clusteru Apache Spark v Azure HDInsight](apache-spark-resource-manager.md)
* [Sledování a ladění úloh spuštěných v clusteru Apache Spark v HDInsight](apache-spark-job-debugging.md)
