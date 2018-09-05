---
title: Nástroje HDInsight pro Azure – použití nástroje Visual Studio Code pro Hive, LLAP nebo pySpark
description: Další informace o použití nástroje Azure HDInsight pro Visual Studio Code k vytvoření a odeslání dotazů a skriptů.
keywords: VS Code, Azure HDInsight Tools, Hive, Python, PySpark, Spark, HDInsight, Hadoop, LLAP, Interactive Hive, Interactive Query
services: hdinsight
author: jejiang
ms.author: jejiang
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/27/2017
ms.openlocfilehash: 58f930b7bb1dee8f8f95b6627ebf70fe095126c0
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/05/2018
ms.locfileid: "43697845"
---
# <a name="use-azure-hdinsight-tools-for-visual-studio-code"></a>Použití Azure HDInsight Tools pro Visual Studio Code

Další informace o použití nástroje Azure HDInsight pro Visual Studio Code (VS Code) pro vytvoření a odesílání dávkových úloh Hive, interaktivních dotazů Hive a skriptů pySpark. Nástroje Azure HDInsight lze nainstalovat na platformách, které jsou podporovány ve VS Code. Patří sem Windows, Linux a macOS. Můžete najít požadované součásti pro různé platformy.


## <a name="prerequisites"></a>Požadavky

Jsou vyžadovány k dokončení kroků v tomto článku následující položky:

- HDInsight cluster. Vytvoření clusteru najdete v tématu [Začínáme s HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md).
- [Visual Studio Code](https://www.visualstudio.com/products/code-vs.aspx).
- [Mono](http://www.mono-project.com/docs/getting-started/install/). Mono se pouze požadované pro systémy Linux a macOS.

## <a name="install-the-hdinsight-tools"></a>Instalace nástrojů HDInsight
   
Po dokončení instalace požadované součásti můžete nainstalovat nástroje Azure HDInsight pro VS Code. 

**Azure HDInsight nainstalovat nástroje**

1. Otevřete Visual Studio Code.

2. V levém podokně vyberte **rozšíření**. Do vyhledávacího pole zadejte **HDInsight**.

3. Vedle položky **nástroje Azure HDInsight**vyberte **nainstalovat**. Po pár sekundách **nainstalovat** tlačítko se změní na **Reload**.

4. Vyberte **Reload** aktivovat **nástroje Azure HDInsight** rozšíření.

5. Vyberte **znovu načíst okno** potvrďte. Můžete zobrazit **nástroje Azure HDInsight** v **rozšíření** podokně.

   ![HDInsight pro Visual Studio kódu Python instalaci](./media/hdinsight-for-vscode/install-hdInsight-plugin.png)

## <a name="open-hdinsight-workspace"></a>Otevřete pracovní prostor HDInsight

Vytvoření pracovního prostoru v nástroji VS Code, předtím, než se můžete připojit k Azure.

**Otevřete pracovní prostor**

1. Na **souboru** nabídce vyberte možnost **otevřít složku**. Poté určíte existující složku jako vaše pracovní složky nebo vytvořte novou. Složka se zobrazí v levém podokně.

2. V levém podokně, vyberte **nový soubor** ikonu vedle pracovní složky.

   ![Nový soubor](./media/hdinsight-for-vscode/new-file.png)

3. Pojmenujte nový soubor s příponou .py (skriptu Spark) nebo .hql (dotazy Hive). Všimněte si, že **XXXX_hdi_settings.json** konfigurační soubor je automaticky přidán do pracovní složky.

4. Otevřít **XXXX_hdi_settings.json** z **EXPLORER**, nebo klikněte pravým tlačítkem na skript editoru vyberte **nastavit konfiguraci**. Můžete nakonfigurovat přihlášení položky, výchozí clusteru a parametrů odeslání úlohy, jak je znázorněno v ukázce v souboru. Můžete také nechat zbývající parametry prázdné.

## <a name="connect-to-hdinsight-cluster"></a>Připojte se ke clusteru HDInsight

Před odesláním skriptů do clusterů HDInsight z VS Code, budete muset připojit ke svému účtu Azure, nebo připojit cluster (pomocí nástroje Ambari uživatelského jména a hesla nebo doméně připojené k účtu).

**Pro připojení k Azure**

1. Pokud ještě nemáte je vytvoření nové pracovní složky a nový soubor skriptu.

2. Pravým tlačítkem myši na editor skriptů a pak v místní nabídce vyberte **HDInsight: přihlášení**. Můžete také zadat **Ctrl + Shift + P**a pak zadejte **HDInsight: přihlášení**.

    ![Přihlaste se nástroje HDInsight pro Visual Studio Code](./media/hdinsight-for-vscode/hdinsight-for-vscode-extension-login.png)

3. Pro přihlášení, přihlaste podle pokynů **výstup** podokně.

    **Azure:** ![HDInsight Tools for Visual Studio Code přihlašovací údaje](./media/hdinsight-for-vscode/hdinsight-for-vscode-extension-Azurelogin-info.png)

    Jakmile budete hotovi, název svého účtu Azure se zobrazí ve stavovém řádku v levé dolní části okna nástroje VS Code. 

    > [!NOTE]
    > Kvůli potížím s známé ověřování Azure budete muset otevřít prohlížeč v privátním režimu nebo v anonymním režimu prohlížeče. Pokud váš účet Azure má dva faktory povoleno, doporučujeme ověřovací telefonní místo PIN kódu ověřování.
  

4. Pravým tlačítkem myši na editor skriptů, otevřete místní nabídku. V místní nabídce můžete provádět následující úlohy:

    - Odhlásit se
    - Výpis clusterů
    - Sada výchozích clusterů
    - Odesílání interaktivních dotazů Hive
    - Odeslání skriptů dávky Hive
    - Odešlete interaktivní dotazy PySpark
    - Odeslání dávkových skriptů PySpark
    - Konfigurace sady

<a id="linkcluster"></a>**Propojení clusteru**

Můžete propojit normální cluster pomocí Ambari, spravovat uživatelské jméno, také propojit zabezpečení clusteru hadoop s použitím doména uživatelské jméno (například: user1@contoso.com).
1. Výběrem otevřete paletu příkazů **CTRL + SHIFT + P**a pak zadejte **HDInsight: propojení clusteru**.

   ![příkaz Link clusteru](./media/hdinsight-for-vscode/link-cluster-command.png)

2. HDInsight zadejte adresu URL clusteru -> vstup -> uživatelské jméno zadejte heslo -> vyberte typ clusteru -> se informace zobrazí úspěch předán ověření.
   
   ![Dialogové okno clusteru odkaz](./media/hdinsight-for-vscode/link-cluster-process.png)

   > [!NOTE]
   > Propojené uživatelské jméno a heslo se použijí, pokud cluster jak zaznamenána v rámci předplatného Azure a propojené clusteru. 
   
3. Propojené clusteru můžete zobrazit pomocí příkazu **clusteru seznamu**. Nyní můžete odeslat skript do tohoto clusteru propojené.

   ![propojené clusteru](./media/hdinsight-for-vscode/linked-cluster.png)

4. Také můžete zrušit propojení clusteru podle vložení **HDInsight: zrušení propojení clusteru** z palety příkazů.

## <a name="list-hdinsight-clusters"></a>Výpis clusterů HDInsight

Pokud chcete otestovat připojení, můžete vytvořit seznam clusterů HDInsight:

**Seznam clusterů HDInsight v rámci vašeho předplatného Azure**
1. Otevřete pracovní prostor a pak se připojte k Azure. Další informace najdete v tématu [pracovní prostor otevřít HDInsight](#open-hdinsight-workspace) a [připojit se k Azure](#connect-to-azure).

2. Pravým tlačítkem myši na editor skriptů a pak vyberte **HDInsight: Cluster seznamu** v místní nabídce. 

3. Clustery Hive a Spark se zobrazí v **výstup** podokně.

    ![Nastavit výchozí konfiguraci clusteru](./media/hdinsight-for-vscode/list-cluster-result.png)

## <a name="set-a-default-cluster"></a>Nastavit výchozí clusteru
1. Otevřete pracovní prostor a připojení k Azure. Zobrazit [pracovní prostor otevřít HDInsight](#open-hdinsight-workspace) a [připojit se k Azure](#connect-to-azure).

2. Pravým tlačítkem myši na editor skriptů a pak vyberte **HDInsight: Cluster nastavit výchozí**. 

3. Vyberte cluster, jako výchozí clusteru pro aktuální soubor skriptu. Automaticky aktualizovat konfigurační soubor nástroje **XXXX_hdi_settings.json**. 

   ![Výchozí konfigurace clusteru sady](./media/hdinsight-for-vscode/set-default-cluster-configuration.png)

## <a name="set-the-azure-environment"></a>Nastavení prostředí Azure 
1. Výběrem otevřete paletu příkazů **CTRL + SHIFT + P**.

2. Zadejte **HDInsight: nastavení prostředí Azure**.

3. Vyberte jeden ze způsobů z Azure a AzureChina jako váš výchozí položku pro přihlášení.

4. Mezitím nástroj již uchránila váš výchozí přihlašovací jméno položku v **XXXX_hdi_settings.json**. Můžete také přímo aktualizovat ho v tomto konfiguračním souboru. 

   ![Výchozí přihlášení položku konfigurace sady](./media/hdinsight-for-vscode/set-default-login-entry-configuration.png)

## <a name="submit-interactive-hive-queries"></a>Odesílání interaktivních dotazů Hive

Pomocí nástrojů HDInsight pro VS Code můžete odeslat interaktivních dotazů Hive ke clusterům HDInsight interactive query.

1. Pokud ještě nemáte pracovní složku a soubor skriptu Hive, vytvořte nové.

2. Pokud jste to ještě neudělali, připojte se k účtu Azure a pak nakonfigurujte výchozí cluster.

3. Pak zkopírujte a vložte do souboru Hive následující kód a uložte ho.

    ```hiveql
    SELECT * FROM hivesampletable;
    ```
3. Klikněte pravým tlačítkem myši na editor skriptů a vyberte **HDInsight: Hive Interactive** (HDInsight: interaktivní Hive) a odešlete tak dotaz. Nástroje také umožňují odeslat pomocí místní nabídky místo celého souboru skriptu blok kódu. Brzy potom se na nové kartě zobrazí výsledky dotazu.

   ![Výsledky interaktivního Hivu](./media/hdinsight-for-vscode/interactive-hive-result.png)

    - Panel **RESULTS** (VÝSLEDKY): Celý výsledek můžete uložit do souboru CSV, JSON nebo Excel nebo můžete vybrat jenom několik řádků.

    - Panel **MESSAGES** (ZPRÁVY): Když vyberete číslo řádku **Line**, přejdete na první řádek spuštěného skriptu.

Spuštění interaktivního dotazu zabere mnohem kratší dobu než [spuštění dávkové úlohy Hive](#submit-hive-batch-scripts).

## <a name="submit-hive-batch-scripts"></a>Odeslání skriptů dávky Hive

1. Pokud ještě nemáte pracovní složku a soubor skriptu Hive, vytvořte nové.

2. Pokud jste to ještě neudělali, připojte se k účtu Azure a pak nakonfigurujte výchozí cluster.

3. Pak zkopírujte a vložte do souboru Hive následující kód a uložte ho.

    ```hiveql
    SELECT * FROM hivesampletable;
    ```
3. Klikněte pravým tlačítkem myši na editor skriptů a vyberte **HDInsight: Hive Batch** (HDInsight: Dávka Hive) a odešlete tak úlohu Hive. 

4. Vyberte cluster, do kterého ji chcete odeslat.  

    Po odeslání úlohy Hive se na panelu **OUTPUT** (VÝSTUP) zobrazí informace o úspěšném odeslání a ID úlohy. Úloha Hive otevře také **WEBOVÝ PROHLÍŽEČ**, ve kterém se zobrazí protokoly a stav úlohy v reálném čase.

   ![odeslání výsledku úlohy Hive](./media/hdinsight-for-vscode/submit-Hivejob-result.png)

[Odeslání interaktivních dotazů Hive](#submit-interactive-hive-queries) zabere mnohem kratší dobu než odeslání dávkové úlohy.

## <a name="submit-interactive-pyspark-queries"></a>Odešlete interaktivní dotazy PySpark
Nástroje HDInsight pro VS Code také umožňuje odeslat interaktivní dotazy PySpark ke clusterům Spark.
1. Pokud ještě nemáte je vytvoření nové pracovní složky a nový soubor skriptu s příponou .py.

2. Pokud jste tak ještě neučinili, připojte ke svému účtu Azure.

3. Zkopírujte a vložte následující kód do souboru skriptu:
   ```python
   from operator import add
   lines = spark.read.text("/HdiSamples/HdiSamples/FoodInspectionData/README").rdd.map(lambda r: r[0])
   counters = lines.flatMap(lambda x: x.split(' ')) \
                .map(lambda x: (x, 1)) \
                .reduceByKey(add)

   coll = counters.collect()
   sortedCollection = sorted(coll, key = lambda r: r[1], reverse = True)

   for i in range(0, 5):
        print(sortedCollection[i])
   ```
4. Zvýrazněte tyto skripty. Klikněte pravým tlačítkem myši na editor skriptů a vyberte **HDInsight: PySpark interaktivní**.

5. Pokud jste ještě nenainstalovali **Python** rozšíření ve VS Code, vyberte **nainstalovat** tlačítko, jak je znázorněno na následujícím obrázku:

    ![HDInsight pro Visual Studio kódu Python instalaci](./media/hdinsight-for-vscode/hdinsight-vscode-install-python.png)

6. Pokud jste tak dosud neučinili, nainstalujte prostředí Pythonu ve vašem systému. 
   - Pro Windows, stáhněte a nainstalujte [Python](https://www.python.org/downloads/). Ujistěte se, `Python` a `pip` jsou ve vašem systému CESTU.

   - Pokyny pro macOS a Linux najdete v tématu [nastavení interaktivního prostředí PySpark pro Visual Studio Code](set-up-pyspark-interactive-environment.md).

7. Vyberte cluster, do které chcete odeslat dotaz PySpark. Brzy NATO výsledku dotazu můžete vidět v nové záložce vpravo:

   ![Odeslat výsledek úlohy Pythonu](./media/hdinsight-for-vscode/pyspark-interactive-result.png) 
8. Nástroj podporuje také **SQL klauzule** dotazu.

   ![Odeslat výsledek úlohy Python](./media/hdinsight-for-vscode/pyspark-ineteractive-select-result.png) Stav odeslání se zobrazí v levém dolním stavovém řádku při spouštění dotazů. Neodesílejte další dotazy, pokud je stav **jádra PySpark (zaneprázdněn)**. 

>[!NOTE]
>Clustery můžete spravovat informace o relaci. Definované proměnné, funkce a hodnoty, které odpovídají jsou uloženy v relaci, takže může být odkazováno mezi víc volání služeb pro stejného clusteru. 
 

## <a name="submit-pyspark-batch-job"></a>Odeslání úlohy služby batch PySpark

1. Pokud ještě nemáte je vytvoření nové pracovní složky a nový soubor skriptu s příponou .py.

2. Připojte se ke svému účtu Azure, pokud jste tak již neučinili.

3. Zkopírujte a vložte následující kód do souboru skriptu:

    ```python
    from __future__ import print_function
    import sys
    from operator import add
    from pyspark.sql import SparkSession
    if __name__ == "__main__":
        spark = SparkSession\
            .builder\
            .appName("PythonWordCount")\
            .getOrCreate()
    
        lines = spark.read.text('/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv').rdd.map(lambda r: r[0])
        counts = lines.flatMap(lambda x: x.split(' '))\
                    .map(lambda x: (x, 1))\
                    .reduceByKey(add)
        output = counts.collect()
        for (word, count) in output:
            print("%s: %i" % (word, count))
        spark.stop()
    ```
4. Pravým tlačítkem myši na editor skriptů a pak vyberte **HDInsight: PySpark Batch**. 

5. Vyberte cluster, do které chcete odeslat úlohu PySpark. 

   ![Odeslat výsledek úlohy Pythonu](./media/hdinsight-for-vscode/submit-pythonjob-result.png) 

Po odeslání úlohy Python odeslání protokolů se objeví v **výstup** okna ve VS Code. **URL uživatelského rozhraní Spark** a **URL uživatelského rozhraní Yarn** jsou také uvedeny. Adresu URL můžete otevřít ve webovém prohlížeči a sledovat stav úlohy.

>[!NOTE]
>PySpark3 nepodporuje už Livy 0.4 (což je cluster Hdinsight spark 2.2). Pouze "PySpark" je podporován Python. Je známo, problém, který se spark 2.2 odeslat selhat s python3.
   
## <a name="livy-configuration"></a>Konfigurace Livy
Konfigurace Livy se podporuje, může být nastavena v nastavení projektu ve složce pracovní prostor. Další podrobnosti najdete v tématu [Livy README](https://github.com/cloudera/livy/blob/master/README.rst ).

+ Nastavení projektu:

    ![Konfigurace Livy](./media/hdinsight-for-vscode/hdi-livyconfig.png)

+ Podporované konfigurace Livy:   

    **/Batches příspěvku**   
    Text žádosti

    | jméno | description | type | 
    | :- | :- | :- | 
    | soubor | Soubor obsahující aplikaci k provedení | Cesta (povinné) | 
    | proxyUser | Uživatel k zosobnění při spuštění úlohy | řetězec | 
    | Název třídy | Hlavní třída Java/Spark aplikace | řetězec |
    | args | Argumenty příkazového řádku pro aplikaci | seznam řetězců | 
    | Kromě souborů JAR | Kromě souborů JAR pro použití v této relaci | Seznam řetězců | 
    | pyFiles | Soubory Pythonu, který se má použít v této relaci | Seznam řetězců |
    | souborů | soubory, který se má použít v této relaci | Seznam řetězců |
    | driverMemory | Velikost paměti pro účely driver procesu | řetězec |
    | driverCores | Počet jader pro účely driver procesu | int |
    | executorMemory | Množství paměti podle proces prováděcího modulu | řetězec |
    | executorCores | Počet jader pro každý prováděcího modulu | int |
    | numExecutors | Počtu prováděcích procesů ke spuštění pro tuto relaci | int |
    | archivy | Archivuje se použije v této relaci | Seznam řetězců |
    | fronta | Název fronty YARN, ke které odeslání | řetězec |
    | jméno | Název této relace | řetězec |
    | conf | Vlastnosti konfigurace Spark | Mapování klíč = val |

    Text odpovědi   
    Vytvořený objekt služby Batch.

    | jméno | description | type | 
    | :- | :- | :- | 
    | id | Id relace | int | 
    | ID aplikace | Id aplikace pro tuto relaci |  Řetězec |
    | appInfo | Informace o podrobné aplikace | Mapování klíč = val |
    | protokol | Řádky protokolu | seznam řetězců |
    | state |   Stav služby batch | řetězec |


## <a name="additional-features"></a>Další funkce

HDInsight pro VS Code podporuje následující funkce:

- **Automatické dokončování IntelliSense**. Návrhy překryvné – klíčové slovo, metody, proměnné a tak dále. Různé ikony představují různé druhy objektů.

    ![Nástroje HDInsight pro typy objektů IntelliSense ve Visual Studio Code](./media/hdinsight-for-vscode/hdinsight-for-vscode-auto-complete-objects.png)
- **Značky chyb IntelliSense**. Služba jazyka podtrhuje úpravy chyby skriptu Hive.     
- **Zvýraznění syntaxe**. Služba jazyka používá různé barvy k rozlišení proměnné, klíčová slova, datový typ, funkce a tak dále. 

    ![Nástroje HDInsight pro Visual Studio Code zvýraznění syntaxe](./media/hdinsight-for-vscode/hdinsight-for-vscode-syntax-highlights.png)

## <a name="next-steps"></a>Další postup

### <a name="demo"></a>Ukázka
* HDInsight pro VS Code: [videa](https://go.microsoft.com/fwlink/?linkid=858706)

### <a name="tools-and-extensions"></a>Nástroje a rozšíření

* [Použití sady Azure Toolkit pro IntelliJ pro ladění aplikací Spark vzdáleně přes síť VPN](spark/apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Použití sady Azure Toolkit pro IntelliJ pro ladění aplikací Spark vzdáleně přes SSH](spark/apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Použití nástrojů HDInsight pro IntelliJ s Hortonworks Sandbox](hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [Pomocí nástrojů HDInsight v sadě Azure Toolkit pro Eclipse k vytvoření aplikací Spark](spark/apache-spark-eclipse-tool-plugin.md)
* [Použití poznámkových bloků Zeppelin s clusterem Sparku v HDInsight](spark/apache-spark-zeppelin-notebook.md)
* [Jádra dostupná pro poznámkový blok Jupyter v clusteru Sparku pro HDInsight](spark/apache-spark-jupyter-notebook-kernels.md)
* [Použití externích balíčků s poznámkovými bloky Jupyter](spark/apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalace Jupyteru do počítače a připojení ke clusteru HDInsight Spark](spark/apache-spark-jupyter-notebook-install-locally.md)
* [Vizualizace dat Hive pomocí Microsoft Power BI ve službě Azure HDInsight](hadoop/apache-hadoop-connect-hive-power-bi.md)
* [Vizualizace dat Interactive Query Hive pomocí Power BI v Azure HDInsight](./interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md).
* [Nastavení interaktivního prostředí PySpark pro Visual Studio Code](set-up-pyspark-interactive-environment.md)
* [Použití Zeppelinu ke spouštění dotazů Hive v Azure HDInsight ](./hdinsight-connect-hive-zeppelin.md)

### <a name="scenarios"></a>Scénáře
* [Spark s BI: Provádějte interaktivní analýzy dat pomocí Sparku v HDInsight pomocí nástrojů BI](spark/apache-spark-use-bi-tools.md)
* [Spark s Machine Learning: Používejte Spark v HDInsight pro analýzu teploty v budově pomocí dat HVAC](spark/apache-spark-ipython-notebook-machine-learning.md)
* [Spark s Machine Learning: Používejte Spark v HDInsight k předpovědím výsledků kontrol potravin](spark/apache-spark-machine-learning-mllib-ipython.md)
* [Analýza protokolu webu pomocí Sparku v HDInsight](spark/apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-running-applications"></a>Vytváření a spouštění aplikací
* [Vytvoření samostatné aplikace pomocí Scala](spark/apache-spark-create-standalone-application.md)
* [Vzdálené spouštění úloh na clusteru Sparku pomocí Livy](spark/apache-spark-livy-rest-interface.md)

### <a name="manage-resources"></a>Správa prostředků
* [Správa prostředků v clusteru Apache Spark v Azure HDInsight](spark/apache-spark-resource-manager.md)
* [Sledování a ladění úloh spuštěných v clusteru Apache Spark v HDInsight](spark/apache-spark-job-debugging.md)



