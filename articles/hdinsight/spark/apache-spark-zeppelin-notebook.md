---
title: Zeppelin poznámkové bloky & Apache Spark clusteru – Azure HDInsight
description: Podrobné pokyny k použití poznámkových bloků Zeppelin s clustery s Apache Spark v Azure HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/23/2020
ms.openlocfilehash: 969dda4b57deb35d745cf7c7d263b78e6fe577d2
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104867538"
---
# <a name="use-apache-zeppelin-notebooks-with-apache-spark-cluster-on-azure-hdinsight"></a>Použití poznámkových bloků Apache Zeppelin s clusterem Apache Spark ve službě Azure HDInsight

Clustery HDInsight Spark obsahují poznámkové bloky [Apache Zeppelin](https://zeppelin.apache.org/) . Pomocí poznámkových bloků spusťte Apache Spark úlohy. V tomto článku se dozvíte, jak používat Poznámkový blok Zeppelin v clusteru HDInsight.

## <a name="prerequisites"></a>Požadavky

* Cluster Apache Spark ve službě HDInsight. Pokyny najdete v tématu [Vytváření clusterů Apache Spark ve službě Azure HDInsight](apache-spark-jupyter-spark-sql.md).
* Schéma identifikátoru URI pro primární úložiště clusterů. Schéma bude `wasb://` pro Azure Blob Storage pro `abfs://` Azure Data Lake Storage Gen2 nebo `adl://` pro Azure Data Lake Storage Gen1. Pokud je pro Blob Storage povolený zabezpečený přenos, identifikátor URI by byl `wasbs://` .  Další informace najdete v tématu [vyžadování zabezpečeného přenosu v Azure Storage](../../storage/common/storage-require-secure-transfer.md) .

## <a name="launch-an-apache-zeppelin-notebook"></a>Spuštění poznámkového bloku Apache Zeppelin

1. V **přehledu** clusteru Spark vyberte **Poznámkový blok Zeppelin** z **řídicích panelů clusteru**. Zadejte přihlašovací údaje Správce clusteru.  

   > [!NOTE]  
   > Do poznámkového bloku Zeppelin se můžete dostat i tak, že v prohlížeči otevřete následující adresu URL. Nahraďte **název_clusteru** názvem vašeho clusteru:
   >
   > `https://CLUSTERNAME.azurehdinsight.net/zeppelin`

2. Vytvořte nový poznámkový blok. V podokně záhlaví přejděte na **Poznámkový blok**  >  **vytvořit novou poznámku**.

    :::image type="content" source="./media/apache-spark-zeppelin-notebook/hdinsight-create-zeppelin-notebook.png " alt-text="Vytvoření nového poznámkového bloku Zeppelin" border="true":::

    Zadejte název pro Poznámkový blok a pak vyberte **vytvořit poznámku**.

3. Ujistěte se, že se v hlavičce poznámkového bloku zobrazuje stav připojeno. Je označený zelenou tečkou v pravém horním rohu.

    :::image type="content" source="./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-connected.png " alt-text="Stav poznámkového bloku Zeppelin" border="true":::

4. Načtěte vzorová data do dočasné tabulky. Při vytváření clusteru Spark ve službě HDInsight se Ukázkový datový soubor `hvac.csv` zkopíruje do přidruženého účtu úložiště v části `\HdiSamples\SensorSampleData\hvac` .

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

    Stiskněte **SHIFT + ENTER** nebo vyberte tlačítko **Přehrát** pro odstavec a spusťte fragment kódu. Stav v pravém horním rohu odstavce by měl pokračovat od připraveného až do dokončení. Výstup se zobrazí v dolní části stejného odstavce. Snímek obrazovky vypadá jako na následujícím obrázku:

    :::image type="content" source="./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-load-data.png " alt-text="Vytvořit dočasnou tabulku z nezpracovaných dat" border="true":::

    Můžete také zadat název každého odstavce. V pravém horním rohu odstavce vyberte ikonu **Nastavení** (Sprocket) a pak vyberte **Zobrazit nadpis**.  

    > [!NOTE]  
    > % spark2 překladače není podporován v poznámkových blocích Zeppelin napříč všemi verzemi HDInsight a překladač% SH nebude podporován od HDInsight 4,0 a vyšší.

5. V tabulce teď můžete spustit příkazy Spark SQL `hvac` . Vložte následující dotaz do nového odstavce. Dotaz načte ID sestavení. Také rozdíl mezi cílovou a skutečnou teplotou pro každé sestavení v daném datu. Stiskněte **SHIFT + ENTER**.

    ```sql
    %sql
    select buildingID, (targettemp - actualtemp) as temp_diff, date from hvac where date = "6/1/13"
    ```  

    Příkaz **% SQL** na začátku upozorňuje na Poznámkový blok na použití překladače Scala Livy.

6. Vyberte ikonu **pruhového grafu** pro změnu zobrazení.  **Nastavení**, která se zobrazí po výběru **pruhového grafu**, vám umožní vybrat **klíče** a **hodnoty**.  Výstup se zobrazí na následujícím snímku obrazovky.

    :::image type="content" source="./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-spark-query-1.png " alt-text="Spuštění příkazu Spark SQL pomocí notebook1" border="true":::

7. Příkazy Spark SQL můžete také spouštět pomocí proměnných v dotazu. Další fragment kódu ukazuje, jak definovat proměnnou `Temp` v dotazu s možnými hodnotami, se kterými se chcete dotazovat. Při prvním spuštění dotazu se automaticky vyplní rozevírací seznam hodnotami, které jste zadali pro proměnnou.

    ```sql
    %sql  
    select buildingID, date, targettemp, (targettemp - actualtemp) as temp_diff from hvac where targettemp > "${Temp = 65,65|75|85}"
    ```

    Vložte tento fragment kódu do nového odstavce a stiskněte **SHIFT + ENTER**. Pak v rozevíracím seznamu **tempa** vyberte **65** .

8. Vyberte ikonu **pruhového grafu** pro změnu zobrazení.  Pak vyberte **Nastavení** a proveďte následující změny:

   * **Skupiny:**  Přidat **targettemp**.  
   * **Hodnoty:** první. Odebrat **Datum**.  2. Přidejte **temp_diff**.  3.  Změňte agregátor z **Sum** na **AVG**.  

     Výstup se zobrazí na následujícím snímku obrazovky.

     :::image type="content" source="./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-spark-query-2.png " alt-text="Spuštění příkazu Spark SQL pomocí notebook2" border="true":::

## <a name="how-do-i-use-external-packages-with-the-notebook"></a>Návody použít pro Poznámkový blok externí balíčky?

Poznámkový blok Zeppelin Apache Spark v HDInsight může používat externí balíčky, které se podílejí na komunitě, které nejsou zahrnuté v clusteru. Vyhledejte v [úložišti Maven](https://search.maven.org/) úplný seznam balíčků, které jsou k dispozici. Můžete také získat seznam dostupných balíčků z jiných zdrojů. Například úplný seznam balíčků, které jsou součástí komunity, je k dispozici v [balíčcích Spark](https://spark-packages.org/).

V tomto článku se dozvíte, jak pomocí Jupyter Notebook balíčku [Spark-CSV](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) .

1. Otevřete nastavení překladače. V pravém horním rohu vyberte přihlášené uživatelské jméno a pak vyberte **Interpret**.

    :::image type="content" source="./media/apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png " alt-text="Spustit překladač" border="true":::

2. Přejděte na **livy2** a pak vyberte **Upravit**.

    :::image type="content" source="./media/apache-spark-zeppelin-notebook/zeppelin-use-external-package-1.png " alt-text="Změnit Interpret settings1" border="true":::

3. Přejděte na klíč `livy.spark.jars.packages` a nastavte jeho hodnotu ve formátu `group:id:version` . Pokud tedy chcete použít balíček [Spark-CSV](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) , je nutné nastavit hodnotu klíče na `com.databricks:spark-csv_2.10:1.4.0` .

    :::image type="content" source="./media/apache-spark-zeppelin-notebook/zeppelin-use-external-package-2.png " alt-text="Změnit Interpret settings2" border="true":::

    Vyberte **Save (Uložit** ) a pak na **OK** , abyste restartovali Livy Interpret.

4. Pokud chcete pochopit, jak se dorazí na hodnotu klíče, který jste zadali výše, tady je postup.

    a. Vyhledejte balíček v úložišti Maven. V tomto článku jsme použili [Spark-CSV](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar).

    b. Z úložiště Shromážděte hodnoty pro ID **skupiny**, **ArtifactId** a **verzi**.

    :::image type="content" source="./media/apache-spark-zeppelin-notebook/use-external-packages-with-jupyter.png " alt-text="Použití externích balíčků s Jupyter Notebook" border="true":::

    c. Zřetězí tři hodnoty oddělené dvojtečkou (**:**).

    ```
    com.databricks:spark-csv_2.10:1.4.0
    ```

## <a name="where-are-the-zeppelin-notebooks-saved"></a>Kam se ukládají poznámkové bloky Zeppelin?

Poznámkové bloky Zeppelin se ukládají do hlavních clusteru. Takže pokud cluster odstraníte, poznámkové bloky se taky odstraní. Pokud chcete zachovat své poznámkové bloky pro pozdější použití v jiných clusterech, musíte je po dokončení spouštění úloh exportovat. Chcete-li exportovat Poznámkový blok, vyberte ikonu **exportu** , jak je znázorněno na obrázku níže.

:::image type="content" source="./media/apache-spark-zeppelin-notebook/zeppelin-download-notebook.png " alt-text="Stáhnout Poznámkový blok" border="true":::

Tato akce uloží Poznámkový blok jako soubor JSON do umístění pro stahování.

## <a name="use-shiro-to-configure-access-to-zeppelin-interpreters-in-enterprise-security-package-esp-clusters"></a>Slouží `Shiro` ke konfiguraci přístupu k překladačům Zeppelin v clusterech balíček zabezpečení podniku (ESP).

Jak bylo uvedeno výše, `%sh` překladač není podporován od HDInsight 4,0 a vyšší. Vzhledem `%sh` k tomu, že překladač zavádí možné problémy se zabezpečením, jako jsou například přístupové karty pomocí příkazů prostředí, byl odebrán také z clusterů HDInsight 3,6 ESP. To znamená, že `%sh` překladač není k dispozici, když ve výchozím nastavení kliknete na možnost **vytvořit novou poznámku** nebo v uživatelském rozhraní překladače.

Uživatelé privilegovaných domén mohou použít `Shiro.ini` soubor k řízení přístupu k uživatelskému rozhraní překladače. Pouze tito uživatelé mohou vytvořit nové `%sh` překladače a nastavit oprávnění u každého nového `%sh` překladače. K řízení přístupu pomocí `shiro.ini` souboru použijte následující postup:

1. Definujte novou roli pomocí existujícího názvu skupiny domén. V následujícím příkladu `adminGroupName` je skupina privilegovaných uživatelů v AAD. V názvu skupiny Nepoužívejte speciální znaky ani prázdné znaky. Znaky po `=` poskytnutí oprávnění pro tuto roli. `*` znamená, že skupina má úplná oprávnění.

    ```
    [roles]
    adminGroupName = *
    ```

2. Přidejte novou roli pro přístup k překladačům Zeppelin. V následujícím příkladu mají všichni uživatelé v nástroji `adminGroupName` udělen přístup k překladačům Zeppelin a mohou vytvářet nové překladače. Mezi závorky můžete umístit více rolí `roles[]` oddělených čárkami. Uživatelé s potřebnými oprávněními pak mají přístup k překladačům Zeppelin.

    ```
    [urls]
    /api/interpreter/** = authc, roles[adminGroupName]
    ```

## <a name="livy-session-management"></a>Správa relací Livy

První odstavec s kódem v Zeppelin poznámkovém bloku vytvoří novou relaci Livy v clusteru. Tato relace se sdílí ve všech poznámkových blocích Zeppelin, které vytvoříte později. Pokud se relace Livy z nějakého důvodu ukončila, úlohy se z poznámkového bloku Zeppelin nespustí.

V takovém případě je třeba provést následující kroky, aby bylo možné začít spouštět úlohy z Zeppelin poznámkového bloku.  

1. Restartujte překladač Livy z poznámkového bloku Zeppelin. Uděláte to tak, že v pravém horním rohu otevřete přihlášené uživatelské jméno a pak vyberete **Interpret**.

    :::image type="content" source="./media/apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png " alt-text="Spustit překladač" border="true":::

2. Přejděte na **livy2** a pak vyberte **restartovat**.

    :::image type="content" source="./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-restart-interpreter.png " alt-text="Restartování překladače Livy" border="true":::

3. Spustí buňku kódu z existujícího poznámkového bloku Zeppelin. Tento kód vytvoří novou relaci Livy v clusteru HDInsight.

## <a name="general-information"></a>Obecné informace

### <a name="validate-service"></a>Ověřit službu

Pokud chcete službu ověřit z Ambari, přejděte do `https://CLUSTERNAME.azurehdinsight.net/#/main/services/ZEPPELIN/summary` umístění název_clusteru, kde je název vašeho clusteru.

Chcete-li ověřit službu z příkazového řádku, SSH k hlavnímu uzlu. Přepněte uživatele na Zeppelin pomocí příkazu `sudo su zeppelin` . Stavové příkazy:

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
|Interpret konfigurace, `Shiro` , site.xml, log4j|/usr/HDP/Current/Zeppelin-Server/conf nebo/etc/Zeppelin/conf|
|Adresář PID|/var/run/zeppelin|

### <a name="enable-debug-logging"></a>Povolit protokolování ladění

1. Přejděte do `https://CLUSTERNAME.azurehdinsight.net/#/main/services/ZEPPELIN/summary` složky název_clusteru, kde je název vašeho clusteru.

1. Přejděte do **Konfigurace**  >  **Upřesnit Zeppelin-log4j-Properties**  >  **log4j_properties_content**.

1. Upravit `log4j.appender.dailyfile.Threshold = INFO` na `log4j.appender.dailyfile.Threshold = DEBUG` .

1. Přidat `log4j.logger.org.apache.zeppelin.realm=DEBUG` .

1. Uložte změny a restartujte službu.

## <a name="next-steps"></a>Další kroky

* [Přehled: Apache Spark v Azure HDInsight](apache-spark-overview.md)
* [Jádra dostupná pro Jupyter Notebook v clusteru Apache Spark pro HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Nainstalujte do počítače Jupyter a připojte ho ke clusteru HDInsight Spark](apache-spark-jupyter-notebook-install-locally.md)
