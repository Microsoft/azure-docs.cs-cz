---
title: Nástroje pro Azure HDInsight – použít Visual Studio Code pro Hive, LLAP nebo PySpark | Dokumentace Microsoftu
description: Další informace o použití nástroje Azure HDInsight pro Visual Studio Code k vytvoření a odeslání dotazů a skriptů.
Keywords: Visual Studio Code,Azure HDInsight Tools,Hive,Python,PySpark,Spark,HDInsight,Hadoop,LLAP,Interactive Hive,Interactive Query
services: HDInsight
documentationcenter: ''
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/04/2019
ms.openlocfilehash: 3301f3cdea1795db3b17bc2fa7da05bf362c744d
ms.sourcegitcommit: e51e940e1a0d4f6c3439ebe6674a7d0e92cdc152
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/08/2019
ms.locfileid: "55895064"
---
# <a name="use-azure-hdinsight-tools-for-visual-studio-code"></a>Použití Azure HDInsight Tools pro Visual Studio Code

Zjistěte, jak používat Azure HDInsight Tools for Visual Studio Code k vytvoření a odesílání dávkových úloh Hive Apache Hive, interaktivních dotazů Hive a skriptů PySpark pro Apache Spark. Nejprve popíšeme, jak nainstalovat nástroje HDInsight tools ve Visual Studio Code a potom provedeme procesem odeslání úlohy Hive a Spark.  

Nástroje Azure HDInsight lze nainstalovat na platformách, které podporuje Visual Studio Code, mezi které patří Windows, Linux a macOS. Níže najdete požadované součásti pro různé platformy.


## <a name="prerequisites"></a>Požadavky

Jsou vyžadovány k dokončení kroků v tomto článku následující položky:

- HDInsight cluster. Vytvoření clusteru najdete v tématu [Začínáme s HDInsight](hadoop/apache-hadoop-linux-create-cluster-get-started-portal.md).
- [Visual Studio Code](https://code.visualstudio.com/).
- [Mono](https://www.mono-project.com/docs/getting-started/install/). Mono se pouze požadované pro systémy Linux a macOS.
- [Rozšíření Azure Account](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) pro Visual Studio Code.
- [Nastavení interaktivního prostředí PySpark pro Visual Studio Code](set-up-pyspark-interactive-environment.md).
- Místní adresář s názvem **HDexample**.  Tento článek používá **C:\HD\HDexample**.

## <a name="install-azure-hdinsight-tools"></a>Instalace nástrojů pro Azure HDInsight

Po dokončení požadavků, instalací nástroje Azure HDInsight pro Visual Studio Code.  Proveďte následující kroky k instalaci nástroje Azure HDInsight:

1. Otevřete Visual Studio Code.

2. V řádku nabídek, přejděte na **zobrazení** > **rozšíření**.

3. Do vyhledávacího pole zadejte **HDInsight**.

4. Vyberte **nástroje Azure HDInsight** z výsledků hledání a pak vyberte **nainstalovat**.  

   ![HDInsight pro Visual Studio kódu Python instalaci](./media/hdinsight-for-vscode/install-hdInsight-plugin.png)

5. Vyberte **Reload** aktivovat **nástroje Azure HDInsight** rozšíření po instalaci.


## <a name="open-hdinsight-work-folder"></a>Otevřít pracovní složky HDInsight

Proveďte následující kroky, chcete-li otevřít pracovní složky a vytvoření souboru v aplikaci Visual Studio Code:

1. V řádku nabídek, přejděte na **souboru** > **otevřít složku...**   >  **C:\HD\HDexample**a pak **vybrat složku** tlačítko. Složka se zobrazí v **Explorer** zobrazení na levé straně.

2. Z **Explorer** zobrazit, vyberte složku, **HDexample**a pak **nový soubor** ikonu vedle pracovní složky.

   ![Nový soubor](./media/hdinsight-for-vscode/new-file.png)

3. Pojmenujte nový soubor s příponou .py (skriptu Spark) nebo .hql (dotazy Hive).  Tento příklad používá **HelloWorld.hql**.

## <a name="connect-to-hdinsight-cluster"></a>Připojte se ke clusteru HDInsight

Před odesláním skriptů ke clusterům HDInsight ze sady Visual Studio Code, budete muset připojit ke svému účtu Azure, nebo připojit cluster (pomocí nástroje Ambari uživatelského jména a hesla nebo doméně připojené k účtu).  Proveďte následující kroky pro připojení k Azure:

1. Přejděte na řádku nabídek **zobrazení** > **paleta příkazů...** a zadejte **HDInsight: Přihlášení**.

    ![Nástroje HDInsight pro Visual Studio Code přihlášení](./media/hdinsight-for-vscode/hdinsight-for-vscode-extension-login.png)

2. Přihlášení podle pokynů **výstup** podokně.
    + Pro prostředí Azure globální **HDInsight: Přihlášení** spustí příkaz **přihlášení k Azure** akce v Průzkumníku HDInsight a naopak.

        ![Pokyny pro azure k přihlášení](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-signin.png)

    + Pro jiné prostředí postupujte podle pokynů přihlášení.

        ![Pokyny pro jiné prostředí k přihlášení](./media/hdinsight-for-vscode/hdi-azure-hdinsight-hdinsight-signin.png)

 Jakmile budete hotovi, název svého účtu Azure se zobrazí ve stavovém řádku v levé dolní části okna Visual Studio Code.  
  

<h2 id="linkcluster">Vytvoření odkazu: Azure HDInsight</h2>

Normální cluster můžete propojit s použitím [Apache Ambari](https://ambari.apache.org/) spravovat uživatelské jméno nebo propojit zabezpečený cluster Hadoop služby balíček zabezpečení podniku pomocí doména uživatelské jméno (například: user1@contoso.com).

1. Přejděte na řádku nabídek **zobrazení** > **paleta příkazů...** a zadejte **HDInsight: Propojení clusteru**.

   ![příkaz Link clusteru](./media/hdinsight-for-vscode/link-cluster-command.png)

2. Vyberte typ propojené clusteru **Azure HDInsight**.

3. Zadejte adresu URL clusteru HDInsight.

4. Zadejte uživatelské jméno Ambari, výchozí hodnota je **správce**.

5. Zadejte heslo Ambari.

6. Vyberte typ clusteru.

7. Kontrola **výstup** zobrazení pro ověření.

   > [!NOTE]  
   > Propojené uživatelské jméno a heslo se použijí, pokud cluster jak zaznamenána v rámci předplatného Azure a propojené clusteru.  


## <a name="create-link-generic-livy-endpoint"></a>Vytvoření odkazu: Koncový bod obecného Livy

1. Přejděte na řádku nabídek **zobrazení** > **paleta příkazů...** a zadejte **HDInsight: Propojení clusteru**.

2. Vyberte typ propojené clusteru **obecný koncový bod Livy**.

3. Zadejte obecné Livy koncového bodu, například: http\:/ / 10.172.41.42:18080.

4. Vyberte typ autorizace **základní** nebo **žádný**.  Pokud **základní**, pak:  
    &emsp;a. Zadejte uživatelské jméno Ambari, výchozí hodnota je **správce**.  
    &emsp;b. Zadejte heslo Ambari.

5. Kontrola **výstup** zobrazení pro ověření.

## <a name="list-hdinsight-clusters"></a>Výpis clusterů HDInsight

1. Přejděte na řádku nabídek **zobrazení** > **paleta příkazů...** a zadejte **HDInsight: Seznam clusteru**.

2. Vyberte požadované předplatné.

3. Zkontrolujte **výstup** zobrazení.  Zobrazení se zobrazí propojené clusterů a všech clusterů v rámci vašeho předplatného Azure.

    ![Nastavit výchozí konfiguraci clusteru](./media/hdinsight-for-vscode/list-cluster-result.png)

## <a name="set-default-cluster"></a>Nastavení výchozího clusteru

1. Znovu otevřete složku **HDexample** vytvořili [starší](#open-hdinsight-work-folder) zavřeno.  

2. Vyberte soubor **HelloWorld.hql** vytvořili [starší](#open-hdinsight-work-folder) a otevře se v editoru skriptů.

3. [Připojit](#connect-to-hdinsight-cluster) ke svému účtu Azure, pokud jste to ještě neudělali.

4. Pravým tlačítkem myši na editor skriptů a vyberte **HDInsight: Nastavit výchozí clusteru**.  

5. Vyberte cluster, jako výchozí clusteru pro aktuální soubor skriptu. Automaticky aktualizovat konfigurační soubor nástroje **. VSCode\settings.json**. 

   ![Výchozí konfigurace clusteru sady](./media/hdinsight-for-vscode/set-default-cluster-configuration.png)

## <a name="set-the-azure-environment"></a>Nastavení prostředí Azure

1. [Připojit](#connect-to-hdinsight-cluster) ke svému účtu Azure, pokud jste to ještě neudělali.

2. Přejděte na řádku nabídek **zobrazení** > **paleta příkazů...** a zadejte **HDInsight: Nastavení prostředí Azure**.

3. Vyberte prostředí jako váš výchozí položku pro přihlášení.

4. Mezitím nástroj již uchránila váš výchozí přihlašovací jméno položku v **. VSCode\settings.json**. Můžete také přímo aktualizovat ho v tomto konfiguračním souboru. 

   ![Výchozí přihlášení položku konfigurace sady](./media/hdinsight-for-vscode/set-default-login-entry-configuration.png)


## <a name="submit-interactive-hive-queries-hive-batch-scripts"></a>Odešlete interaktivní dotazy Hive, dávkových skriptů Hive

Nástroje HDInsight pro Visual Studio Code můžete odeslat interaktivních dotazů Hive a dávkových skriptů Hive do clusterů HDInsight.

1. Znovu otevřete složku **HDexample** vytvořili [starší](#open-hdinsight-work-folder) zavřeno.  

2. Vyberte soubor **HelloWorld.hql** vytvořili [starší](#open-hdinsight-work-folder) a otevře se v editoru skriptů.

3. [Připojit](#connect-to-hdinsight-cluster) ke svému účtu Azure, pokud jste to ještě neudělali.

4. Pak zkopírujte a vložte do souboru Hive následující kód a uložte ho.

    ```hiveql
    SELECT * FROM hivesampletable;
    ```

5. Pravým tlačítkem myši na editor skriptů, vyberte **HDInsight: Interaktivní Hive** chcete odeslat dotaz, nebo použijte klávesovou zkratku **Ctrl + Alt + I**.  Vyberte **HDInsight: Hive Batch** odešlete skript, nebo použijte klávesovou zkratku **Ctrl + Alt + H**.  

6. Vyberte cluster, pokud jste nezadali výchozí clusteru. Nástroje také umožňují odeslat pomocí místní nabídky místo celého souboru skriptu blok kódu. Po chvíli se zobrazí výsledky dotazu v nové záložce.

   ![Výsledky interaktivního Hivu](./media/hdinsight-for-vscode/interactive-hive-result.png)

    - **VÝSLEDKY** panelu: Můžete uložit celý výsledek jako soubor CSV, JSON nebo Excel na místní cestu nebo stačí vybrat více řádků.

    - **ZPRÁVY** panelu: Když vyberete **řádku** číslo, přeskočí první řádek spuštěného skriptu.

## <a name="submit-interactive-pyspark-queries"></a>Odešlete interaktivní dotazy PySpark

1. Znovu otevřete složku **HDexample** vytvořili [starší](#open-hdinsight-work-folder) zavřeno.  

2. Vytvořte nový soubor **HelloWorld.py** následující [starší](#open-hdinsight-work-folder) kroky.

3. Zobrazí se dialogové okno doporučení rozšíření Python a pokud jste nenainstalovali Python pro kontrolu požadovaných součástí.  Instalace a znovu načíst Visual Studio Code k dokončení instalace.

    >![HDInsight pro Visual Studio kódu Python instalaci](./media/hdinsight-for-vscode/hdinsight-vscode-install-python.png)

4. [Připojit](#connect-to-hdinsight-cluster) ke svému účtu Azure, pokud jste to ještě neudělali.

5. Zkopírujte a vložte následující kód do souboru skriptu:
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

6. Pravým tlačítkem myši na editor skriptů, vyberte **HDInsight: PySpark interaktivní** chcete odeslat dotaz, nebo použijte klávesovou zkratku **Ctrl + Alt + I**.  

7. Vyberte cluster, pokud jste nezadali výchozí clusteru. Nástroje také umožňují odeslat pomocí místní nabídky místo celého souboru skriptu blok kódu. Po chvíli se zobrazí výsledky dotazu v nové záložce.

   ![Odeslat výsledek úlohy Pythonu](./media/hdinsight-for-vscode/pyspark-interactive-result.png) 

8. Nástroj podporuje také **SQL klauzule** dotazu.

   ![Odeslat výsledek úlohy Python](./media/hdinsight-for-vscode/pyspark-ineteractive-select-result.png) Stav odeslání se zobrazí v levém dolním stavovém řádku při spouštění dotazů. Neodesílejte další dotazy, pokud je stav **jádra PySpark (zaneprázdněn)**.  

>[!NOTE]  
>Clustery můžete spravovat informace o relaci. Definované proměnné, funkce a hodnoty, které odpovídají jsou uloženy v relaci, takže může být odkazováno mezi víc volání služeb pro stejného clusteru. 

### <a name="pyspark3-is-not-supported-with-spark2223"></a>PySpark3 nepodporuje Spark2.2/2.3

PySpark3 se už nepodporuje s clusterem Spark 2.2 a Spark2.3 clusterem, podporovány jsou pouze "PySpark" pro Python. Je známo, problém, který odešle Spark 2.2/2.3 selhat s Python3.

   ![Chyba získávání pythonu3 odeslat](./media/hdinsight-for-vscode/hdi-azure-hdinsight-py3-error.png)

Postupujte podle kroků použít Python2.x: 

1. Instalovat Python 2.7 do místního počítače a přidejte ho do systémové cesty.

2. Restartujte Visual Studio Code.

3. Přepnout Python 2 kliknutím **Python XXX** na stav panelu a potom vyberte cíl Python.

   ![Vyberte verzi pythonu](./media/hdinsight-for-vscode/hdi-azure-hdinsight-select-python.png)


## <a name="submit-pyspark-batch-job"></a>Odeslání úlohy služby batch PySpark

1. Znovu otevřete složku **HDexample** vytvořili [starší](#open-hdinsight-work-folder) zavřeno.  

2. Vytvořte nový soubor **BatchFile.py** následující [starší](#open-hdinsight-work-folder) kroky.

3. [Připojit](#connect-to-hdinsight-cluster) ke svému účtu Azure, pokud jste to ještě neudělali.

4. Zkopírujte a vložte následující kód do souboru skriptu:

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

Po odeslání úlohy Python odeslání protokolů se objeví v **výstup** okna ve Visual Studio Code. **URL uživatelského rozhraní Spark** a **URL uživatelského rozhraní Yarn** jsou také uvedeny. Adresu URL můžete otevřít ve webovém prohlížeči a sledovat stav úlohy.

## <a name="apache-livy-configuration"></a>Konfigurace Apache Livy

[Apache Livy](https://livy.incubator.apache.org/) konfigurace je podporovaná, to je možné nastavit **. VSCode\settings.json** ve složce pracovní prostor. Konfigurace livy v současné době podporuje pouze skript Pythonu. Další podrobnosti najdete v tématu [Livy README](https://github.com/cloudera/livy/blob/master/README.rst ).

<a id="triggerlivyconf"></a>**Jak aktivovat konfigurace livy**

1 – Metoda  
1. V řádku nabídek, přejděte na **souboru** > **Předvolby** > **nastavení**.  
2. V **nastavení vyhledávání** textového pole zadejte **Sumission úlohu HDInsight: Livy Conf**.  
3. Vyberte **upravit v settings.json** pro příslušné hledání výsledku.

2 – metoda   
Odešlete soubor, Všimněte si, že složka .vscode je automaticky přidán do pracovní složky. Kliknutím můžete najít konfiguraci livy **.vscode\settings.json**.

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
    | className | Hlavní třída Java/Spark aplikace | řetězec |
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
    | appId | Id aplikace pro tuto relaci |  String |
    | appInfo | Informace o podrobné aplikace | Mapování klíč = val |
    | log | Řádky protokolu | seznam řetězců |
    | state |   Stav služby batch | řetězec |

>[!NOTE]
>Přiřazené konfigurace livy se zobrazí v podokně výstupů když odešlete skript.

## <a name="integrate-with-azure-hdinsight-from-explorer"></a>Integrace s Azure HDInsight z Průzkumníka

**Azure HDInsight** byl přidán do zobrazení Průzkumníka. Můžete procházet a spravovat je přímo pomocí clusterů **Azure HDInsight**.

1. [Připojit](#connect-to-hdinsight-cluster) ke svému účtu Azure, pokud jste to ještě neudělali.

2. V řádku nabídek, přejděte na **zobrazení** > **Explorer**.

3. V levém podokně rozbalte **AZURE HDINSIGHT**.  Zobrazí se dostupných předplatných a clusterů (HBase, Spark a Hadoop jsou podporovány). 

   ![Předplatné Azure HDInsight](./media/hdinsight-for-vscode/hdi-azure-hdinsight-subscription.png)

4. Rozbalte cluster a zobrazit schéma databáze a tabulky metadat hive.

   ![Cluster Azure HDInsight](./media/hdinsight-for-vscode/hdi-azure-hdinsight-cluster.png)


## <a name="additional-features"></a>Další funkce

HDInsight pro Visual Studio Code podporuje následující funkce:

- **Automatické dokončování IntelliSense**. Návrhy překryvné – klíčové slovo, metody, proměnné a tak dále. Různé ikony představují různé druhy objektů.

    ![Nástroje HDInsight pro typy objektů IntelliSense ve Visual Studio Code](./media/hdinsight-for-vscode/hdinsight-for-vscode-auto-complete-objects.png)
- **Značky chyb IntelliSense**. Služba jazyka podtrhuje úpravy chyby skriptu Hive.     
- **Zvýraznění syntaxe**. Služba jazyka používá různé barvy k rozlišení proměnné, klíčová slova, datový typ, funkce a tak dále. 

    ![Nástroje HDInsight pro Visual Studio Code zvýraznění syntaxe](./media/hdinsight-for-vscode/hdinsight-for-vscode-syntax-highlights.png)


## <a name="unlink-cluster"></a>Zrušit propojení clusteru

1. Přejděte na řádku nabídek **zobrazení** > **paleta příkazů...** a pak zadejte **HDInsight: Zrušit propojení clusteru**.  

2. Vyberte cluster se zrušit propojení.  

3. Kontrola **výstup** zobrazení pro ověření.  


## <a name="logout"></a>Odhlásit  

Přejděte na řádku nabídek **zobrazení** > **paleta příkazů...** a pak zadejte **HDInsight: Odhlášení**.  Bude automaticky otevírané okno s oznámením dolní pravém rohu **odhlášení úspěšně!**.


## <a name="next-steps"></a>Další postup
Video ukázku použití HDInsight pro Visual Studio Code najdete v tématu [HDInsight pro Visual Studio Code](https://go.microsoft.com/fwlink/?linkid=858706)
