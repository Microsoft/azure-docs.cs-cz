---
title: Notebooky Zeppelin & clusteru Apache Spark - Azure HDInsight
description: Podrobné pokyny, jak používat notebooky Zeppelin s clustery Apache Spark v Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/18/2020
ms.openlocfilehash: e313048986beca1991e38ce2e65ea12f954170d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77598268"
---
# <a name="use-apache-zeppelin-notebooks-with-apache-spark-cluster-on-azure-hdinsight"></a>Použití poznámkových bloků Apache Zeppelin s clusterem Apache Spark ve službě Azure HDInsight

Clustery HDInsight Spark zahrnují notebooky [Apache Zeppelin,](https://zeppelin.apache.org/) které můžete použít ke spuštění úloh [Apache Spark.](https://spark.apache.org/) V tomto článku se dozvíte, jak používat poznámkový blok Zeppelin v clusteru HDInsight.

## <a name="prerequisites"></a>Požadavky

* Cluster Apache Spark ve službě HDInsight. Pokyny najdete v tématu [Vytváření clusterů Apache Spark ve službě Azure HDInsight](apache-spark-jupyter-spark-sql.md).
* Schéma URI pro primární úložiště clusterů. To by `wasb://` bylo pro Azure `abfs://` Blob Storage, pro `adl://` Azure Data Lake Storage Gen2 nebo pro Azure Data Lake Storage Gen1. Pokud je pro úložiště objektů BLOB povolen `wasbs://`zabezpečený přenos, identifikátor URI bude .  Další informace najdete [v tématu Require secure transfer in Azure Storage](../../storage/common/storage-require-secure-transfer.md) .

## <a name="launch-an-apache-zeppelin-notebook"></a>Spuštění notebooku Apache Zeppelin

1. V přehledu **clusteru**Spark vyberte ze **zřídicích panelů clusteru poznámkový blok** **Zeppelin** . Zadejte pověření správce pro cluster.  

   > [!NOTE]  
   > Poznámkový blok Zeppelin pro váš cluster můžete také kontaktovat otevřením následující adresy URL ve vašem prohlížeči. Nahraďte **název clusteru názvem clusteru:**
   >
   > `https://CLUSTERNAME.azurehdinsight.net/zeppelin`

2. Vytvořte nový poznámkový blok. V podokně záhlaví přejděte na **Poznámkový blok** > **Vytvořit novou poznámku**.

    ![Vytvoření nového notebooku Zeppelin](./media/apache-spark-zeppelin-notebook/hdinsight-create-zeppelin-notebook.png "Vytvoření nového notebooku Zeppelin")

    Zadejte název poznámkového bloku a vyberte **Vytvořit poznámku**.

3. Ujistěte se, že záhlaví poznámkového bloku zobrazuje připojený stav. Je označena zelenou tečkou v pravém horním rohu.

    ![Stav notebooku Zeppelin](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-connected.png "Stav notebooku Zeppelin")

4. Načtěte vzorová data do dočasné tabulky. Když vytvoříte cluster Spark v HDInsightu, `hvac.csv`ukázkový datový soubor , `\HdiSamples\SensorSampleData\hvac`se zkopíruje do přidruženého účtu úložiště v části .

    Do prázdného odstavce, který je ve výchozím nastavení vytvořen v novém poznámkovém bloku, vložte následující úryvek.

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

    Stisknutím **kláves SHIFT + ENTER** nebo vyberte tlačítko **Přehrát** pro odstavec, abyste spouštěli výstřižek. Stav v pravém rohu odstavce by měl postupovat od READY, PENDING, Running to FINISHED. Výstup se zobrazí v dolní části stejného odstavce. Snímek obrazovky vypadá takto:

    ![Vytvoření dočasné tabulky z nezpracovaných dat](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-load-data.png "Vytvoření dočasné tabulky z nezpracovaných dat")

    Ke každému odstavci můžete také zadat název. V pravém rohu odstavce vyberte ikonu **Nastavení** (řetězové kolo) a pak vyberte **Zobrazit název**.  

    > [!NOTE]  
    > %spark2 interpret není podporován v poznámkových blocích Zeppelin ve všech verzích HDInsight a %sh interpret nebude podporován od HDInsight 4.0 a dále.

5. Nyní můžete spustit Příkazy `hvac` Spark SQL v tabulce. Vložte následující dotaz do nového odstavce. Dotaz načte ID budovy a rozdíl mezi cílovou a skutečnou teplotou pro každou budovu k danému datu. Stiskněte **klávesu SHIFT + ENTER**.

    ```sql
    %sql
    select buildingID, (targettemp - actualtemp) as temp_diff, date from hvac where date = "6/1/13"
    ```  

    Příkaz **%sql** na začátku informuje poznámkový blok, aby používal interpret Livy Scala.

6. Chcete-li zobrazení změnit, vyberte ikonu **Pruhový graf.**  **nastavení**, které se zobrazí po výběru **pruhového grafu**, umožňuje zvolit **klávesy**a **hodnoty**.  Následující snímek obrazovky ukazuje výstup.

    ![Spuštění příkazu Spark SQL pomocí poznámkového bloku1](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-spark-query-1.png "Spuštění příkazu Spark SQL pomocí poznámkového bloku1")

7. Příkazy Spark SQL můžete také spustit pomocí proměnných v dotazu. Další úryvek ukazuje, jak definovat `Temp`proměnnou , v dotazu s možnými hodnotami, se kterými chcete dotazovat. Při prvním spuštění dotazu je automaticky vyplněnrozerozevírací seznam s hodnotami, které jste zadali pro proměnnou.

    ```sql
    %sql  
    select buildingID, date, targettemp, (targettemp - actualtemp) as temp_diff from hvac where targettemp > "${Temp = 65,65|75|85}"
    ```

    Vložte tento úryvek do nového odstavce a stiskněte **klávesu SHIFT + ENTER**. Pak vrozené **rozevírací** seznamu Temp vyberte **65.**

8. Chcete-li zobrazení změnit, vyberte ikonu **Pruhový graf.**  Pak vyberte **nastavení** a proveďte následující změny:

   * **Skupiny:**  Přidejte **targettemp**.  
   * **Hodnoty:** 1. Odebrat **datum**.  2. Přidejte **temp_diff**.  3.  Změňte agregátor ze **sumy** na **AVG**.  

     Následující snímek obrazovky ukazuje výstup.

     ![Spuštění příkazu Spark SQL pomocí poznámkového bloku2](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-spark-query-2.png "Spuštění příkazu Spark SQL pomocí poznámkového bloku2")

## <a name="how-do-i-use-external-packages-with-the-notebook"></a>Jak se používají externí balíčky s poznámkovým blokem?

Notebook Zeppelin můžete nakonfigurovat v clusteru Apache Spark na HDInsightu tak, aby používal externí balíčky, které jsou součástí clusteru. V [úložišti Maven](https://search.maven.org/) můžete vyhledat úplný seznam dostupných balíčků. Můžete také získat seznam dostupných balíčků z jiných zdrojů. Úplný seznam balíčků, do kterých přispěla komunita, je například k dispozici na [sparku Packages](https://spark-packages.org/).

V tomto článku uvidíte, jak používat [balíček spark-csv](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) s poznámkovým blokem Jupyter.

1. Otevřít nastavení tlumočníka. V pravém horním rohu vyberte přihlášené uživatelské jméno a pak vyberte **Interpret .**

    ![Spuštění tlumočníka](./media/apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "Výstup úlu")

2. Přejděte na **livy2**a vyberte **možnost Upravit**.

    ![Změna nastavení interpretu1](./media/apache-spark-zeppelin-notebook/zeppelin-use-external-package-1.png "Změna nastavení interpretu1")

3. Přejděte `livy.spark.jars.packages`na klávesu a nastavte `group:id:version`její hodnotu ve formátu . Takže pokud chcete použít [balíček spark-csv,](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) musíte nastavit hodnotu `com.databricks:spark-csv_2.10:1.4.0`klíče na .

    ![Změna nastavení interpretu2](./media/apache-spark-zeppelin-notebook/zeppelin-use-external-package-2.png "Změna nastavení interpretu2")

    Chcete-li **restartovat** interpret livy, vyberte uložit a potom **OK.**

4. Pokud chcete pochopit, jak se dostat na hodnotu klíče zadaného výše, zde je návod.

    a. Vyhledejte balíček v úložišti Maven. Pro tento článek jsme použili [spark-csv](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar).

    b. Z úložiště shromážděte hodnoty **groupId**, **ArtifactId**a **Version**.

    ![Použití externích balíčků s notebookem Jupyter](./media/apache-spark-zeppelin-notebook/use-external-packages-with-jupyter.png "Použití externích balíčků s notebookem Jupyter")

    c. Zřetězit tři hodnoty oddělené dvojtečkou (**:**).

        com.databricks:spark-csv_2.10:1.4.0

## <a name="where-are-the-zeppelin-notebooks-saved"></a>Kde jsou uloženy notebooky Zeppelin?

Notebooky Zeppelin jsou uloženy do hlavových uzlů clusteru. Pokud tedy cluster odstraníte, budou odstraněny také poznámkové bloky. Pokud chcete zachovat poznámkové bloky pro pozdější použití v jiných clusterech, musíte je exportovat po dokončení spuštění úloh. Chcete-li exportovat poznámkový blok, vyberte ikonu **Exportovat,** jak je znázorněno na obrázku níže.

![Stáhnout poznámkový blok](./media/apache-spark-zeppelin-notebook/zeppelin-download-notebook.png "Stažení poznámkového bloku")

Tím uložíte poznámkový blok jako soubor JSON do umístění pro stahování.

## <a name="use-shiro-to-configure-access-to-zeppelin-interpreters-in-enterprise-security-package-esp-clusters"></a>Použití Shiro ke konfiguraci přístupu k protokolům Zeppelin Interprets v clusterech balíčků zabezpečení rozlehlé sítě (ESP)
Jak je uvedeno `%sh` výše, interpret není podporován od HDInsight 4.0 a dále. Navíc vzhledem `%sh` k tomu, že interpret zavádí potenciální problémy se zabezpečením, jako je například přístupové karty pomocí příkazů prostředí, byl také odebrán z clusterů HDInsight 3.6 ESP. To `%sh` znamená, že interpret není k dispozici po kliknutí na **vytvořit novou poznámku** nebo v interpretu ui ve výchozím nastavení. 

Uživatelé privilegované domény `Shiro.ini` mohou soubor využít k řízení přístupu k uživatelskému uživatelskému uživatelskému nastavení interpretu. Proto pouze tito uživatelé `%sh` mohou vytvářet nové `%sh` interprety a nastavit oprávnění pro každý nový interpret. Chcete-li řídit `shiro.ini` přístup pomocí souboru, použijte následující kroky:

1. Definujte novou roli pomocí existujícího názvu skupiny domény. V následujícím příkladu `adminGroupName` je skupina privilegovaných uživatelů v AAD. V názvu skupiny nepoužívejte speciální znaky ani mezery. Znaky po `=` udělení oprávnění pro tuto roli. `*`znamená, že skupina má úplná oprávnění.

    ```
    [roles]
    adminGroupName = *
    ```

2. Přidejte novou roli pro přístup k tlumočníkům Zeppelin. V následujícím příkladu mají `adminGroupName` všichni uživatelé přístup k tlumočníkům Zeppelin a jsou schopni vytvářet nové tlumočníky. Mezi závorky v oblasti `roles[]`můžete umístit více rolí oddělených čárkami. Poté mohou uživatelé, kteří mají potřebná oprávnění, přistupovat k tlumočníkům Zeppelin.

    ```
    [urls]
    /api/interpreter/** = authc, roles[adminGroupName]
    ```

## <a name="livy-session-management"></a>Livy session management

Když spustíte první odstavec kódu v poznámkovém bloku Zeppelin, vytvoří se v clusteru HDInsight Spark nová relace Livy. Tato relace je sdílena mezi všemi poznámkovými bloky Zeppelin, které následně vytvoříte. Pokud z nějakého důvodu livy relace je zabit (restartování clusteru, a tak dále), nebudete moci spustit úlohy z notebooku Zeppelin.

V takovém případě je nutné provést následující kroky, než můžete spustit úlohy z poznámkového bloku Zeppelin.  

1. Restartujte interpreta Livy z notebooku Zeppelin. Chcete-li tak učinit, otevřete nastavení interpretu výběrem přihlášeného uživatelského jména v pravém horním rohu a vyberte **interpretovat**.

    ![Spuštění tlumočníka](./media/apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "Výstup úlu")

2. Přejděte na **livy2**a vyberte **možnost restartovat**.

    ![Restartujte interpreta Livy](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-restart-interpreter.png "Restartujte tlumočníka Zeppelin")

3. Spusťte buňku kódu z existujícího notebooku Zeppelin. Tím se vytvoří nová relace Livy v clusteru HDInsight.

## <a name="general-information"></a>Obecné informace

### <a name="validate-service"></a>Ověřit službu

Chcete-li ověřit službu z Ambari, přejděte na `https://CLUSTERNAME.azurehdinsight.net/#/main/services/ZEPPELIN/summary` místo, kde clusterCLUSTERNAME je název clusteru.

Chcete-li ověřit službu z příkazového řádku, SSH do hlavního uzlu. Přepnout uživatele na zeppelin pomocí příkazu `sudo su zeppelin`. Příkazy o stavu:

|Příkaz |Popis |
|---|---|
|`/usr/hdp/current/zeppelin-server/bin/zeppelin-daemon.sh status`|Stav služby.|
|`/usr/hdp/current/zeppelin-server/bin/zeppelin-daemon.sh --version`|Verze služby.|
|`ps -aux | grep zeppelin`|Identifikovat PID.|

### <a name="log-locations"></a>Protokolovat umístění

|Služba |Cesta |
|---|---|
|zeppelin-server|/usr/hdp/current/zeppelin-server/|
|Protokoly serveru|/var/log/zeppelin|
|Konfigurační interpret, Shiro, site.xml, log4j|/usr/hdp/current/zeppelin-server/conf nebo /etc/zeppelin/conf|
|Adresář PID|/var/run/zeppelin|

### <a name="enable-debug-logging"></a>Povolit protokolování ladění

1. Přejděte `https://CLUSTERNAME.azurehdinsight.net/#/main/services/ZEPPELIN/summary` na místo, kde clusterNAME je název clusteru.

1. Přejděte na **CONFIGS** > **Advanced zeppelin-log4j-properties** > **log4j_properties_content**.

1. Změnit `log4j.appender.dailyfile.Threshold = INFO` `log4j.appender.dailyfile.Threshold = DEBUG`na .

1. Přidat `log4j.logger.org.apache.zeppelin.realm=DEBUG`.

1. Uložte změny a restartujte službu.

## <a name="next-steps"></a>Další kroky

[Přehled: Apache Spark v Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Scénáře

* [Apache Spark s BI: Provádění interaktivní analýzy dat pomocí Spark v HDInsightu pomocí nástrojů BI](apache-spark-use-bi-tools.md)
* [Apache Spark se strojovým učením: Použijte Spark v HDInsightu pro analýzu teploty budovy pomocí dat HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark s machine learningem: Využijte Spark v HDInsightu k předvídání výsledků kontroly potravin](apache-spark-machine-learning-mllib-ipython.md)
* [Analýza protokolu webových stránek pomocí Apache Spark v HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Vytvoření a spouštění aplikací

* [Vytvoření samostatné aplikace pomocí Scala](apache-spark-create-standalone-application.md)
* [Spouštění úloh na dálku v clusteru Apache Spark pomocí Apache Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Nástroje a rozšíření

* [Použití HDInsight Tools Plugin pro IntelliJ IDEA k vytvoření a odeslání aplikací Apache Spark Scala](apache-spark-intellij-tool-plugin.md)
* [Použití HDInsight Tools Plugin pro IntelliJ IDEA k ladění aplikací Apache Spark na dálku](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Jádra dostupná pro notebook Jupyter v clusteru Apache Spark pro HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Použijte externí balíčky s poznámkovými bloky Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Nainstalujte do počítače Jupyter a připojte ho ke clusteru HDInsight Spark](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Správa prostředků

* [Správa prostředků v clusteru Apache Spark v Azure HDInsight](apache-spark-resource-manager.md)
* [Sledování a ladění úloh spuštěných v clusteru serveru Apache Spark v HDInsight](apache-spark-job-debugging.md)
