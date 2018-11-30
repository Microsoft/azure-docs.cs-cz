---
title: Nástroje pro Azure HDInsight – použít Visual Studio Code pro Hive, LLAP nebo PySpark | Dokumentace Microsoftu
description: Další informace o použití nástroje Azure HDInsight pro Visual Studio Code k vytvoření a odeslání dotazů a skriptů.
Keywords: VS Code,Azure HDInsight Tools,Hive,Python,PySpark,Spark,HDInsight,Hadoop,LLAP,Interactive Hive,Interactive Query
services: HDInsight
documentationcenter: ''
author: jejiang
ms.author: jejiang
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/27/2017
ms.openlocfilehash: 9603751db01eaffdf9fbe26164aed53017c5e23c
ms.sourcegitcommit: 345b96d564256bcd3115910e93220c4e4cf827b3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/28/2018
ms.locfileid: "52499531"
---
# <a name="use-azure-hdinsight-tools-for-visual-studio-code"></a>Použití Azure HDInsight Tools pro Visual Studio Code

Další informace o použití [Azure HDInsight Tools for Visual Studio Code](https://docs.microsoft.com/azure/hdinsight/hdinsight-for-vscode) (VS Code) k vytvoření a odeslání [Apache Hive](https://hive.apache.org/) dávkové úlohy, interaktivní dotazy Apache Hive a skriptů PySpark. Nástroje Azure HDInsight lze nainstalovat na platformách, které jsou podporovány ve VS Code. Patří sem Windows, Linux a macOS. Můžete najít požadované součásti pro různé platformy.


## <a name="prerequisites"></a>Požadavky

Jsou vyžadovány k dokončení kroků v tomto článku následující položky:

- HDInsight cluster. Vytvoření clusteru najdete v tématu [Začínáme s HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md).
- [Visual Studio Code](https://www.visualstudio.com/products/code-vs.aspx).
- [Mono](http://www.mono-project.com/docs/getting-started/install/). Mono se pouze požadované pro systémy Linux a macOS.

## <a name="install-the-hdinsight-tools"></a>Instalace nástrojů HDInsight
   
Po dokončení instalace požadované součásti můžete nainstalovat nástroje Azure HDInsight pro VS Code. 

### <a name="to-install-azure-hdinsight-tools"></a>Instalace nástrojů pro Azure HDInsight

1. Otevřete Visual Studio Code.

2. V levém podokně vyberte **rozšíření**. Do vyhledávacího pole zadejte **HDInsight**.

3. Vedle položky **nástroje Azure HDInsight**vyberte **nainstalovat**. Po pár sekundách **nainstalovat** tlačítko se změní na **Reload**.

4. Vyberte **Reload** aktivovat **nástroje Azure HDInsight** rozšíření.

5. Vyberte **znovu načíst okno** potvrďte. Můžete zobrazit **nástroje Azure HDInsight** v **rozšíření** podokně.

   ![HDInsight pro Visual Studio kódu Python instalaci](./media/hdinsight-for-vscode/install-hdInsight-plugin.png)

## <a name="open-hdinsight-workspace"></a>Otevřete pracovní prostor HDInsight

Vytvoření pracovního prostoru v nástroji VS Code, předtím, než se můžete připojit k Azure.

### <a name="to-open-a-workspace"></a>Otevřete pracovní prostor

1. Na **souboru** nabídce vyberte možnost **otevřít složku**. Poté určíte existující složku jako vaše pracovní složky nebo vytvořte novou. Složka se zobrazí v levém podokně.

2. V levém podokně, vyberte **nový soubor** ikonu vedle pracovní složky.

   ![Nový soubor](./media/hdinsight-for-vscode/new-file.png)

3. Pojmenujte nový soubor s příponou .py (skriptu Spark) nebo .hql (dotazy Hive). 

## <a name="connect-to-hdinsight-cluster"></a>Připojte se ke clusteru HDInsight

Před odesláním skriptů do clusterů HDInsight z VS Code, budete muset připojit ke svému účtu Azure, nebo připojit cluster (pomocí nástroje Ambari uživatelského jména a hesla nebo doméně připojené k účtu).

### <a name="to-connect-to-azure"></a>Pro připojení k Azure

1. Pokud ještě nemáte je vytvoření nové pracovní složky a nový soubor skriptu.

2. Pravým tlačítkem myši na editor skriptů a pak v místní nabídce vyberte **HDInsight: přihlášení**. Můžete také zadat **Ctrl + Shift + P**a pak zadejte **HDInsight: přihlášení**.

    ![Nástroje HDInsight pro Visual Studio Code přihlášení](./media/hdinsight-for-vscode/hdinsight-for-vscode-extension-login.png)

3. Pro přihlášení, přihlaste podle pokynů **výstup** podokně.
    + Pro globální prostředí HDInsight přihlášení aktivují Azure přihlášení v procesu.

        ![Pokyny pro azure k přihlášení](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-signin.png)

    + Pro jiné prostředí postupujte podle pokynů přihlášení.

        ![Pokyny pro jiné prostředí k přihlášení](./media/hdinsight-for-vscode/hdi-azure-hdinsight-hdinsight-signin.png)

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

<h3 id="linkcluster">Propojení clusteru</h3>

Normální cluster můžete propojit s použitím [Apache Ambari](https://ambari.apache.org/) spravovat uživatelské jméno nebo propojit zabezpečený cluster Hadoop služby balíček zabezpečení podniku pomocí doména uživatelské jméno (například: user1@contoso.com).
1. Výběrem otevřete paletu příkazů **CTRL + SHIFT + P**a pak zadejte **HDInsight: propojení clusteru**.

   ![příkaz Link clusteru](./media/hdinsight-for-vscode/link-cluster-command.png)

2. HDInsight zadejte adresu URL clusteru -> vstup -> uživatelské jméno zadejte heslo -> vyberte typ clusteru -> se informace zobrazí úspěch předán ověření.
   
   ![Dialogové okno clusteru odkaz](./media/hdinsight-for-vscode/link-cluster-process.png)

   > [!NOTE]
   > Propojené uživatelské jméno a heslo se použijí, pokud cluster jak zaznamenána v rámci předplatného Azure a propojené clusteru. 
   
3. Propojené clusteru můžete zobrazit pomocí příkazu **clusteru seznamu**. Nyní můžete odeslat skript do tohoto clusteru propojené.

   ![propojené clusteru](./media/hdinsight-for-vscode/linked-cluster.png)

4. Také můžete zrušit propojení clusteru podle vložení **HDInsight: zrušení propojení clusteru** z palety příkazů.


### <a name="to-link-a-generic-apache-livy-endpoint"></a>Chcete-li propojit koncový bod obecného Apache Livy

1. Výběrem otevřete paletu příkazů **CTRL + SHIFT + P**a pak zadejte **HDInsight: propojení clusteru**.
2. Vyberte **koncový bod obecného Livy**.
3. Zadejte obecné Livy koncového bodu, například: http://10.172.41.42:18080.
4. Vyberte **základní** potřebovat při autorizaci pro obecný Livy koncový bod, jinak vyberte **žádný**.
5. Název vstupu uživatele při vyberte **základní** v step4.
6. Zadejte heslo, když vyberte **základní** v step4.
7. Obecný koncový bod livy propojení se úspěšně vytvořilo.

   ![propojené obecnému clusteru livy](./media/hdinsight-for-vscode/link-cluster-process-generic-livy.png)

## <a name="list-hdinsight-clusters"></a>Výpis clusterů HDInsight

Pokud chcete otestovat připojení, můžete vytvořit seznam clusterů HDInsight:

### <a name="to-list-hdinsight-clusters-under-your-azure-subscription"></a>Seznam clusterů HDInsight v rámci vašeho předplatného Azure
1. Otevřete pracovní prostor a pak se připojte k Azure. Další informace najdete v tématu [pracovní prostor otevřít HDInsight](#open-hdinsight-workspace) a [připojit se k Azure](#connect-to-hdinsight-cluster).

2. Pravým tlačítkem myši na editor skriptů a pak vyberte **HDInsight: Cluster seznamu** v místní nabídce. 

3. Clustery HDInsight se zobrazí v **výstup** podokně.

    ![Nastavit výchozí konfiguraci clusteru](./media/hdinsight-for-vscode/list-cluster-result.png)

## <a name="set-a-default-cluster"></a>Nastavit výchozí clusteru
1. Otevřete pracovní prostor a připojení k Azure. Zobrazit [pracovní prostor otevřít HDInsight](#open-hdinsight-workspace) a [připojit se k Azure](#connect-to-hdinsight-cluster).

2. Pravým tlačítkem myši na editor skriptů a pak vyberte **HDInsight: Cluster nastavit výchozí**. 

3. Vyberte cluster, jako výchozí clusteru pro aktuální soubor skriptu. Automaticky aktualizovat konfigurační soubor nástroje **. VSCode\settings.json**. 

   ![Výchozí konfigurace clusteru sady](./media/hdinsight-for-vscode/set-default-cluster-configuration.png)

## <a name="set-the-azure-environment"></a>Nastavení prostředí Azure
1. Výběrem otevřete paletu příkazů **CTRL + SHIFT + P**.

2. Zadejte **HDInsight: nastavení prostředí Azure**.

3. Vyberte prostředí, jako je například "Azure" nebo "AzureChina" jako váš výchozí položku pro přihlášení.

4. Mezitím nástroj již uchránila váš výchozí přihlašovací jméno položku v **. VSCode\settings.json**. Můžete také přímo aktualizovat ho v tomto konfiguračním souboru. 

   ![Výchozí přihlášení položku konfigurace sady](./media/hdinsight-for-vscode/set-default-login-entry-configuration.png)

## <a name="submit-interactive-hive-queries-hive-batch-scripts"></a>Odešlete interaktivní dotazy Hive, dávkových skriptů Hive

Pomocí nástrojů HDInsight pro VS Code můžete odeslat interaktivních dotazů Hive, dávkových skriptů Hive do clusterů HDInsight.

1. Pokud ještě nemáte pracovní složku a soubor skriptu Hive, vytvořte nové.

2. Připojení ke clusterům Azure účet nebo odkaz.

3. Pak zkopírujte a vložte do souboru Hive následující kód a uložte ho.

    ```hiveql
    SELECT * FROM hivesampletable;
    ```
4. Pravým tlačítkem myši na editor skriptů, vyberte **HDInsight: interaktivní Hive** chcete odeslat dotaz, nebo použijte klávesovou zkratku **Ctrl + Alt + I**. Vyberte **HDInsight: Hive Batch** odešlete skript, nebo použijte klávesovou zkratku **Ctrl + Alt + H**. 

5. Vyberte cluster, pokud jste nezadali výchozí clusteru. Nástroje také umožňují odeslat pomocí místní nabídky místo celého souboru skriptu blok kódu. Po chvíli zobrazí výsledky dotazu v nové záložce.

   ![Výsledky interaktivního Hivu](./media/hdinsight-for-vscode/interactive-hive-result.png)

    - Panel **RESULTS** (VÝSLEDKY): Celý výsledek můžete uložit do souboru CSV, JSON nebo Excel nebo můžete vybrat jenom několik řádků.

    - Panel **MESSAGES** (ZPRÁVY): Když vyberete číslo řádku **Line**, přejdete na první řádek spuštěného skriptu.

## <a name="submit-interactive-pyspark-queries"></a>Odešlete interaktivní dotazy PySpark

### <a name="to-submit-interactive-pyspark-queries-to-hdinsight-spark-clusters"></a>K zadávání dotazů interaktivní PySpark na clusterech HDInsight Spark.

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
4. Zvýrazněte tento skript. Klikněte pravým tlačítkem myši na editor skriptů a vyberte **HDInsight: PySpark interaktivní**, nebo použijte klávesovou zkratku **Ctrl + Alt + I**.

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

### <a name="to-disable-environment-check"></a>Chcete-li zakázat kontrolu prostředí

Ve výchozím nastavení, nástrojů HDInsight zkontroluje prostředí a nainstalujte závislé balíčky při odeslání interaktivní dotazy PySpark. Chcete-li zakázat kontrolu prostředí, nastavte **hdinsight.disablePysparkEnvironmentValidation** k **Ano** pod **UŽIVATELSKÁ nastavení**.

   ![Nastavit prostředí zaškrtnutí od nastavení](./media/hdinsight-for-vscode/hdi-azure-hdinsight-environment-check.png)

Alternativně klepněte na tlačítko **zakázat ověření** tlačítko otevře dialogové okno.

   ![Nastavte zaškrtávací prostředí z dialogového okna](./media/hdinsight-for-vscode/hdi-azure-hdinsight-environment-check-dialog.png)

### <a name="pyspark3-is-not-supported-with-spark2223"></a>PySpark3 nepodporuje Spark2.2/2.3

PySpark3 se už nepodporuje s clusterem Spark 2.2 a Spark2.3 clusterem, podporovány jsou pouze "PySpark" pro Python. Je známo, problém, který odešle Spark 2.2/2.3 selhat s Python3.

   ![Chyba získávání pythonu3 odeslat](./media/hdinsight-for-vscode/hdi-azure-hdinsight-py3-error.png)

Postupujte podle kroků použít Python2.x: 

1. Instalovat Python 2.7 do místního počítače a přidejte ho do systémové cesty.

2. Restartujte VSCode.

3. Přepnout Python 2 kliknutím **Python XXX** na stav panelu a potom vyberte cíl Python.

   ![Vyberte verzi pythonu](./media/hdinsight-for-vscode/hdi-azure-hdinsight-select-python.png)

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
4. Pravým tlačítkem myši na editor skriptů a pak vyberte **HDInsight: PySpark Batch**, nebo použijte klávesovou zkratku **Ctrl + Alt + H**. 

5. Vyberte cluster, do které chcete odeslat úlohu PySpark. 

   ![Odeslat výsledek úlohy Pythonu](./media/hdinsight-for-vscode/submit-pythonjob-result.png) 

Po odeslání úlohy Python odeslání protokolů se objeví v **výstup** okna ve VS Code. **URL uživatelského rozhraní Spark** a **URL uživatelského rozhraní Yarn** jsou také uvedeny. Adresu URL můžete otevřít ve webovém prohlížeči a sledovat stav úlohy.

## <a name="apache-livy-configuration"></a>Konfigurace Apache Livy

[Apache Livy](https://livy.incubator.apache.org/) konfigurace je podporovaná, může být nastavena na **. VSCode\settings.json** ve složce pracovní prostor. Konfigurace livy v současné době podporuje pouze skript Pythonu. Další podrobnosti najdete v tématu [Livy README](https://github.com/cloudera/livy/blob/master/README.rst ).

<a id="triggerlivyconf"></a>**Jak aktivovat konfigurace livy**
   
Můžete najít na **souboru** nabídce vyberte možnost **Předvolby**a zvolte **nastavení** v místní nabídce. Klikněte na tlačítko **nastavení pracovního prostoru** kartu, budete moct začít nastavit konfiguraci livy.

Také můžete odeslat soubor, Všimněte si, že složka .vscode je automaticky přidán do pracovní složky. Kliknutím můžete najít konfiguraci livy **.vscode\settings.json**.

+ Nastavení projektu:

    ![Konfigurace Livy](./media/hdinsight-for-vscode/hdi-livyconfig.png)

>[!NOTE]
>Pro nastavení **driverMomory** a **executorMomry**, nastavte hodnotu s jednotkou, například 1 g nebo 1024 m. 

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
    | appId | Id aplikace pro tuto relaci |  Řetězec |
    | appInfo | Informace o podrobné aplikace | Mapování klíč = val |
    | protokol | Řádky protokolu | seznam řetězců |
    | state |   Stav služby batch | řetězec |

>[!NOTE]
>Přiřazené konfigurace livy se zobrazí v podokně výstupů když odešlete skript.

## <a name="integrate-with-azure-hdinsight-from-explorer"></a>Integrace s Azure HDInsight z Průzkumníka

Azure HDInsight je přidaný do na levém panelu. Můžete procházet a přímé správě clusteru.

1. Rozbalte **AZURE HDINSIGHT**, pokud není přihlášení, zobrazí se **přihlášení do Azure...**  odkaz.

    ![Přihlaste se obrázek odkazu](./media/hdinsight-for-vscode/hid-azure-hdinsight-sign-in.png)

2. Klikněte na tlačítko **přihlášení k Azure**, se zobrazí místní okno přihlášení odkaz a kód v pravé dolní části.

    ![Pokyny pro jiné prostředí k přihlášení](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-signin-code.png)

3. Klikněte na tlačítko **zkopírovat a otevřít** tlačítko se otevře prohlížeč, vložte kód, klikněte na tlačítko **pokračovat** tlačítko, zobrazí se vám v pomocném parametru o přihlášení úspěšně.

4. Po přihlášení dostupných předplatných a clusterů (HBase, Spark a Hadoop jsou podporovány) budou zobrazeny v **AZURE HDINSIGHT**. 

   ![Předplatné Azure HDInsight](./media/hdinsight-for-vscode/hdi-azure-hdinsight-subscription.png)

5. Rozbalte cluster a zobrazit schéma databáze a tabulky metadat hive.

   ![Cluster Azure HDInsight](./media/hdinsight-for-vscode/hdi-azure-hdinsight-cluster.png)

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

* [Ladění aplikací Apache Spark vzdáleně přes síť VPN pomocí sady Azure Toolkit pro IntelliJ](spark/apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Ladění aplikací Apache Spark vzdáleně přes SSH pomocí sady Azure Toolkit pro IntelliJ](spark/apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Použití nástrojů HDInsight pro IntelliJ s Hortonworks Sandbox](hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [Vytváření aplikací Apache Spark pomocí nástrojů HDInsight v sadě Azure Toolkit pro Eclipse](spark/apache-spark-eclipse-tool-plugin.md)
* [Použití poznámkových bloků Apache Zeppelin s clusterem Apache Spark v HDInsight](spark/apache-spark-zeppelin-notebook.md)
* [Jádra dostupná pro poznámkový blok Jupyter v clusteru Apache Spark pro HDInsight](spark/apache-spark-jupyter-notebook-kernels.md)
* [Použití externích balíčků s poznámkovými bloky Jupyter](spark/apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalace Jupyteru do počítače a připojení ke clusteru HDInsight Spark](spark/apache-spark-jupyter-notebook-install-locally.md)
* [Vizualizace dat pomocí Microsoft Power BI v Azure HDInsight s Apache Hive](hadoop/apache-hadoop-connect-hive-power-bi.md)
* [Vizualizace dat Interactive Query Hive pomocí Power BI v Azure HDInsight](./interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md).
* [Nastavení interaktivního prostředí PySpark pro Visual Studio Code](set-up-pyspark-interactive-environment.md)
* [Použití Apache Zeppelinu ke spuštění dotazy Apache Hive v Azure HDInsight ](./hdinsight-connect-hive-zeppelin.md)

### <a name="scenarios"></a>Scénáře
* [Apache Spark s BI: provádějte interaktivní analýzy dat pomocí Sparku v HDInsight pomocí nástrojů BI](spark/apache-spark-use-bi-tools.md)
* [Apache Spark s Machine Learning: používejte Spark v HDInsight pro analýzu stavební teploty pomocí dat HVAC](spark/apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark s Machine Learning: používejte Spark v HDInsight k předpovědím výsledků kontroly potravin](spark/apache-spark-machine-learning-mllib-ipython.md)
* [Analýza protokolu webu pomocí Apache Spark v HDInsight](spark/apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-running-applications"></a>Vytváření a spouštění aplikací
* [Vytvoření samostatné aplikace pomocí Scala](spark/apache-spark-create-standalone-application.md)
* [Vzdálené spouštění úloh na clusteru Apache Spark pomocí Apache Livy](spark/apache-spark-livy-rest-interface.md)

### <a name="manage-resources"></a>Správa prostředků
* [Správa prostředků v clusteru Apache Spark v Azure HDInsight](spark/apache-spark-resource-manager.md)
* [Sledování a ladění úloh spuštěných v clusteru Apache Spark v HDInsight](spark/apache-spark-job-debugging.md)



