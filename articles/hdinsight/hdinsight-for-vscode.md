---
title: Azure HDInsight pro Visual Studio Code
description: Naučte se používat nástroje pro podregistr Spark & (Azure HDInsight) pro Visual Studio Code k vytváření a odesílání dotazů a skriptů.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/30/2019
ms.openlocfilehash: da5cdd36e70166d274d50fcb093c0889cf534172
ms.sourcegitcommit: bafb70af41ad1326adf3b7f8db50493e20a64926
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/25/2019
ms.locfileid: "68489019"
---
# <a name="use-spark--hive-tools-for-visual-studio-code"></a>Použití nástrojů pro podregistr Spark & pro Visual Studio Code

Naučte se používat nástroje Spark & podregistr pro Visual Studio Code k vytváření a odesílání Apache Hive dávkových úloh, interaktivních dotazů na podregistru a skriptů PySpark pro Apache Spark. Nejdřív popíšeme, jak v Visual Studio Code nainstalovat nástroje pro podregistr Spark & a pak si projdeme, jak odesílat úlohy do podregistru a Sparku.  

Nástroje Spark & podregistr lze nainstalovat na platformy podporované Visual Studio Code, které zahrnují systémy Windows, Linux a macOS. Níže najdete předpoklady pro různé platformy.


## <a name="prerequisites"></a>Požadavky

K dokončení kroků v tomto článku jsou vyžadovány následující položky:

- Cluster An HDInsight. Pokud chcete vytvořit cluster, přečtěte si téma Začínáme [se službou HDInsight](hadoop/apache-hadoop-linux-create-cluster-get-started-portal.md). Nebo cluster Spark/podregistr podporující koncový bod Livy.
- [Visual Studio Code](https://code.visualstudio.com/).
- [Mono](https://www.mono-project.com/docs/getting-started/install/). Mono se vyžaduje jenom pro Linux a macOS.
- [Nastavte PySpark interaktivní prostředí pro Visual Studio Code](set-up-pyspark-interactive-environment.md).
- Místní adresář s názvem **HDexample**.  Tento článek používá **C:\HD\HDexample**.

## <a name="install-spark--hive-tools"></a>Instalace nástrojů pro podregistr Spark &

Po dokončení požadovaných součástí můžete nainstalovat nástroje Spark & podregistr Tools for Visual Studio Code.  Provedením následujících kroků nainstalujete nástroje Spark & podregistr:

1. Otevřete Visual Studio Code.

2. V řádku nabídek přejděte k **zobrazení** > **rozšíření**.

3. Do vyhledávacího pole zadejte **Spark & podregistr**.

4. Z výsledků hledání vyberte **Spark & nástroje pro podregistr** a pak vyberte **nainstalovat**.  

   ![Podregistr Spark & pro instalaci Visual Studio Code Pythonu](./media/hdinsight-for-vscode/install-hdInsight-plugin.png)

5. V případě potřeby **znovu načíst** .


## <a name="open-work-folder"></a>Otevřít pracovní složku

Provedením následujících kroků otevřete pracovní složku a vytvořte soubor v Visual Studio Code:

1. V řádku nabídek přejděte do složky otevřít **soubor** >  **...** C:\HD\HDexample a pak vyberte tlačítko **Vybrat složku** .   >  Složka se zobrazí v zobrazení **Průzkumník** na levé straně.

2. V zobrazení **Průzkumník** vyberte složku, **HDexample**a potom klikněte na ikonu **nový soubor** vedle pracovní složky.

   ![Nový soubor](./media/hdinsight-for-vscode/new-file.png)

3. Pojmenujte nový soubor buď pomocí `.hql` (dotazů na podregistr), `.py` nebo pomocí přípony souboru (Spark Script).  V tomto příkladu se používá **HelloWorld. HQL**.

## <a name="set-the-azure-environment"></a>Nastavení prostředí Azure

U národního cloudového uživatele postupujte podle pokynů pro nastavení prostředí Azure a pak použijte **Azure:**  Přihlaste se a přihlaste se k Azure.
   
1. Klikněte na **File\Preferences\Settings**.
2. Hledat **v Azure: Cloudu**
3. Ze seznamu vyberte národní Cloud.

   ![Nastavit výchozí konfiguraci zadání přihlašovacích údajů](./media/hdinsight-for-vscode/set-default-login-entry-configuration.png)

## <a name="connect-to-azure-account"></a>Připojit k účtu Azure

Než budete moct odesílat skripty do svých clusterů z Visual Studio Code, musíte se připojit ke svému účtu Azure nebo propojit cluster (pomocí Ambari uživatelského jména/hesla nebo účtu připojeného k doméně).  Pokud se chcete připojit k Azure, proveďte následující kroky:

1. V řádku nabídek přejděte k **zobrazení** > **paleta příkazů...** a zadejte **Azure: Přihlaste se**.

    ![Nástroje pro podregistr Spark & pro přihlášení Visual Studio Code](./media/hdinsight-for-vscode/hdinsight-for-vscode-extension-login.png)

2. Pokud se chcete přihlásit k Azure, postupujte podle pokynů pro přihlášení. Po připojení se váš název účtu Azure zobrazí na stavovém řádku v dolní části okna Visual Studio Code.  
  

## <a name="link-a-cluster"></a>Propojení clusteru

### <a name="link-azure-hdinsight"></a>Odkaz: Azure HDInsight

Běžný cluster můžete propojit pomocí spravovaného uživatelského jména [Apache Ambari](https://ambari.apache.org/) nebo pomocí uživatelského jména domény propojit zabezpečený cluster Hadoop sady Security Pack (například: `user1@contoso.com`).

1. V řádku nabídek přejděte k **zobrazení** > **paleta příkazů...** a zadejte **Spark/podregistr: Propojit cluster**.

   ![příkaz propojit cluster](./media/hdinsight-for-vscode/link-cluster-command.png)

2. Vyberte typ propojeného clusteru **Azure HDInsight**.

3. Zadejte adresu URL clusteru HDInsight.

4. Zadejte uživatelské jméno Ambari, výchozí nastavení je **admin**.

5. Zadejte Ambari heslo.

6. Vyberte typ clusteru.

7. Nastavte zobrazovaný název clusteru (volitelné).

8. Zkontrolujte zobrazení **výstupu** pro ověření.

   > [!NOTE]  
   > Propojené uživatelské jméno a heslo se použijí, pokud se cluster přihlásí do předplatného Azure i v propojeném clusteru.  


### <a name="link-generic-livy-endpoint"></a>Odkaz: Obecný koncový bod Livy

1. V řádku nabídek přejděte k **zobrazení** > **paleta příkazů...** a zadejte **Spark/podregistr: Propojit cluster**.

2. Vyberte typ propojeného clusteru **obecný koncový bod Livy**.

3. Zadejte obecný koncový bod Livy, například: http\://10.172.41.42:18080.

4. Vyberte typ autorizace **Basic** nebo **žádný**.  Pokud **Basic**, pak:  
    &emsp;a. Zadejte uživatelské jméno Ambari, výchozí nastavení je **admin**.  
    &emsp;b. Zadejte Ambari heslo.

5. Zkontrolujte zobrazení **výstupu** pro ověření.

## <a name="list-clusters"></a>Výpis clusterů

1. V řádku nabídek přejděte k **zobrazení** > **paleta příkazů...** a zadejte **Spark/podregistr: Vypíše**cluster.

2. Vyberte požadované předplatné.

3. Zkontrolujte zobrazení **výstup** .  Zobrazení zobrazí vaše propojené clustery a všechny clustery v rámci vašeho předplatného Azure.

    ![Nastavení výchozí konfigurace clusteru](./media/hdinsight-for-vscode/list-cluster-result.png)

## <a name="set-default-cluster"></a>Nastavit výchozí cluster

1. Otevřete znovu složku **HDexample** , která byla vytvořena [dříve](#open-work-folder) , pokud je zavřena.  

2. Vyberte soubor **HelloWorld. HQL** , který jste vytvořili [dříve](#open-work-folder) , a otevře se v editoru skriptu.

3. Klikněte pravým tlačítkem na editor skriptů a vyberte **Spark/podregistr: Nastavte výchozí cluster**.  

4. [Připojte](#connect-to-azure-account) se k účtu Azure nebo propojte cluster, pokud jste to ještě neudělali.

5. Vyberte cluster jako výchozí cluster pro aktuální soubor skriptu. Nástroje automaticky aktualizují konfigurační soubor **. VSCode\settings.json**. 

   ![Nastavit výchozí konfiguraci clusteru](./media/hdinsight-for-vscode/set-default-cluster-configuration.png)


## <a name="submit-interactive-hive-queries-hive-batch-scripts"></a>Odesílání interaktivních dotazů na podregistry, skripty Batch v podregistru

Pomocí nástrojů pro podregistr Spark & pro Visual Studio Code můžete do svých clusterů odesílat interaktivní dotazy na podregistry a skripty pro dávkové zpracování podregistru.

1. Otevřete složku **HDexample** , která byla [dříve](#open-work-folder) vytvořena, pokud byla zavřena.  

2. Vyberte soubor **HelloWorld. HQL** , který jste vytvořili [dříve](#open-work-folder) , a otevře se v editoru skriptu.


3. Pak zkopírujte a vložte do souboru Hive následující kód a uložte ho.

    ```hiveql
    SELECT * FROM hivesampletable;
    ```

4. [Připojte](#connect-to-azure-account) se k účtu Azure nebo propojte cluster, pokud jste to ještě neudělali.

5. Pravým tlačítkem myši klikněte na Editor skriptu **, vyberte podregistr: Interaktivní** dotaz odešlete nebo použijte klávesovou zkratku **CTRL + ALT + I**.  Vyberte **podregistr: Batch** k odeslání skriptu nebo použijte klávesovou zkratku **CTRL + ALT + H**.  

6. Pokud jste nezadali výchozí cluster, vyberte cluster. Nástroje také umožňují odeslat pomocí místní nabídky místo celého souboru skriptu blok kódu. Po chvíli se výsledky dotazu zobrazí na nové kartě.

   ![Výsledky interaktivního Hivu](./media/hdinsight-for-vscode/interactive-hive-result.png)

    - Panel **výsledků** : Celý výsledek můžete uložit jako soubor CSV, JSON nebo Excel do místní cesty nebo stačí vybrat více řádků.

    - Panel **zpráv** : Když vyberete číslo **řádku** , přejdete na první řádek běžícího skriptu.

## <a name="submit-interactive-pyspark-queries"></a>Odeslání interaktivních dotazů PySpark

Interaktivní dotazy PySpark můžete odesílat pomocí následujících kroků:

1. Otevřete složku **HDexample** , která byla [dříve](#open-work-folder) vytvořena, pokud byla zavřena.  

2. Vytvořte nový soubor **HelloWorld.py** podle [předchozích](#open-work-folder) kroků.

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

4. [Připojte](#connect-to-azure-account) se k účtu Azure nebo propojte cluster, pokud jste to ještě neudělali.

5. Zvolte veškerý kód a klikněte pravým tlačítkem na editor skriptů, vyberte **Spark: PySpark Interactive** k odeslání dotazu nebo použijte klávesovou zkratku **CTRL + ALT + I**.

   ![interaktivní kontextová nabídka pyspark](./media/hdinsight-for-vscode/pyspark-interactive-right-click.png)

6. Pokud jste nezadali výchozí cluster, vyberte cluster. Po chvíli se **interaktivní** výsledky Pythonu zobrazí na nové kartě. Nástroje také umožňují odeslat pomocí místní nabídky místo celého souboru skriptu blok kódu. 

   ![interaktivní okno pyspark interaktivního Pythonu](./media/hdinsight-for-vscode/pyspark-interactive-python-interactive-window.png) 

7. Zadejte **"%% info"** a potom stiskněte **SHIFT + ENTER** pro zobrazení informací o úloze. (Volitelné)

   ![Zobrazit informace o úloze](./media/hdinsight-for-vscode/pyspark-interactive-view-job-information.png)

8. Nástroj také podporuje dotaz **Spark SQL** .

   ![Výsledek interaktivního zobrazení Pyspark](./media/hdinsight-for-vscode/pyspark-ineteractive-select-result.png)

   Stav odeslání se zobrazí vlevo od dolního stavového řádku při spouštění dotazů. Neodesílat další dotazy, pokud je stav **PySpark jádro (zaneprázdněno)** .  

   > [!NOTE] 
   >
   > Pokud v nastavení není zaškrtnuto políčko **Povolit rozšíření Pythonu** (výchozí nastavení je zaškrtnuté), vyslané výsledky interakce pyspark budou používat staré okno.
   >
   > ![pyspark interaktivní rozšíření Pythonu je zakázané.](./media/hdinsight-for-vscode/pyspark-interactive-python-extension-disabled.png)


## <a name="submit-pyspark-batch-job"></a>Odeslat dávkovou úlohu PySpark

1. Otevřete složku **HDexample** , která byla [dříve](#open-work-folder) vytvořena, pokud byla zavřena.  

2. Vytvořte nový soubor **BatchFile.py** podle [předchozích](#open-work-folder) kroků.

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

4. [Připojte](#connect-to-azure-account) se k účtu Azure nebo propojte cluster, pokud jste to ještě neudělali.

5. Klikněte pravým tlačítkem na editor skriptů a pak vyberte **Spark: PySpark Batch**nebo použijte klávesovou zkratku **CTRL + ALT + H**. 

6. Vyberte cluster, do kterého chcete odeslat PySpark úlohu. 

   ![Odeslat výsledek úlohy Pythonu](./media/hdinsight-for-vscode/submit-pythonjob-result.png) 

Po odeslání úlohy Pythonu se protokoly odeslání zobrazí v okně **výstup** v Visual Studio Code. Zobrazí se také adresa URL **uživatelského rozhraní Spark** a **Adresa URL uživatelského rozhraní příze** . Pro sledování stavu úlohy můžete otevřít adresu URL ve webovém prohlížeči.

## <a name="apache-livy-configuration"></a>Konfigurace Apache Livy

Konfigurace [Apache Livy](https://livy.incubator.apache.org/) je podporovaná, dá se nastavit na **. VSCode\settings.json** ve složce pracovního prostoru. V současné době konfigurace Livy podporuje pouze skript Pythonu. Další podrobnosti najdete v článku [LIVY Readme](https://github.com/cloudera/livy/blob/master/README.rst ).

<a id="triggerlivyconf"></a>**Jak aktivovat konfiguraci Livy**

Metoda 1  
1. V řádku nabídek přejděte na**Nastavení** **Předvolby** >  **souborů** > .  
2. Do textového pole **Nastavení hledání** zadejte **Sumission úlohy HDInsight: Livy conf**.  
3. Pro příslušný výsledek hledání vyberte **Upravit v nastavení. JSON** .

Metoda 2   
Odešlete soubor, Všimněte si, že složka. VSCode je automaticky přidána do pracovní složky. Konfiguraci Livy můžete najít kliknutím na **. vscode\settings.JSON**.

+ Nastavení projektu:

    ![Konfigurace Livy](./media/hdinsight-for-vscode/hdi-livyconfig.png)

>[!NOTE]
>Pro nastavení **driverMomory** a **executorMomry**nastavte hodnotu jednotka, například 1G nebo 1024m. 

+ Podporované konfigurace Livy:   

    **PŘÍSPĚVEK/Batches**   
    Text žádosti

    | name | description | type | 
    | :- | :- | :- | 
    | file | Soubor obsahující aplikaci, která se má provést | cesta (povinné) | 
    | proxyUser | Uživatel, který se má zosobnit při spuštění úlohy | řetězec | 
    | NázevTřídy | Application – hlavní třída Java/Spark | řetězec |
    | args | Argumenty příkazového řádku pro aplikaci | seznam řetězců | 
    | jars | JAR, která se má použít v této relaci | Seznam řetězců | 
    | pyFiles | Soubory Pythonu, které se mají použít v této relaci | Seznam řetězců |
    | files | soubory, které se mají použít v této relaci | Seznam řetězců |
    | driverMemory | Velikost paměti, která se má použít pro proces ovladače | řetězec |
    | driverCores | Počet jader, které se mají použít pro proces ovladače | int |
    | executorMemory | Velikost paměti, která se má použít pro proces prováděcího modulu | řetězec |
    | executorCores | Počet jader, které se mají použít pro každý prováděcí modul | int |
    | numExecutors | Počet prováděcích modulů, které se mají spustit pro tuto relaci | int |
    | archives | Archivy, které se mají použít v této relaci | Seznam řetězců |
    | queue | Název fronty PŘÍZe, do které byl odeslán | řetězec |
    | name | Název této relace | řetězec |
    | výrobku | Vlastnosti konfigurace Sparku | Mapa klíče = Val |

    Text odpovědi   
    Vytvořený objekt Batch.

    | name | description | type | 
    | :- | :- | :- | 
    | id | ID relace | int | 
    | appId | ID aplikace této relace |  Řetězec |
    | appInfo | Podrobné informace o aplikaci | Mapa klíče = Val |
    | protokol | Řádky protokolu | seznam řetězců |
    | state |   Stav dávky | řetězec |

>[!NOTE]
>Přiřazená konfigurace Livy se zobrazí v podokně výstup při odeslání skriptu.

## <a name="integrate-with-azure-hdinsight-from-explorer"></a>Integrace s Azure HDInsight z Průzkumníka

Do zobrazení Průzkumníka se přidalo **Azure HDInsight** . Clustery můžete procházet a spravovat přímo prostřednictvím **Azure HDInsight**.

1. [Připojte](#connect-to-azure-account) se k účtu Azure nebo propojte cluster, pokud jste to ještě neudělali.

2. V řádku nabídek přejděte na **Zobrazit** > **Průzkumníka**.

3. V levém podokně rozbalte položku **Azure HDInsight**.  Zobrazí se seznam dostupných předplatných a clusterů (Spark, Hadoop a HBA). 

   ![Předplatné Azure HDInsight](./media/hdinsight-for-vscode/hdi-azure-hdinsight-subscription.png)

4. Rozbalte cluster pro zobrazení databáze metadat podregistru a schématu tabulky.

   ![Cluster Azure HDInsight](./media/hdinsight-for-vscode/hdi-azure-hdinsight-cluster.png)


## <a name="preview-hive-table"></a>Náhled tabulky podregistru
Náhled tabulky v clusterech můžete zobrazit přímo přes **Azure HDInsight** Explorer.
1. Pokud jste to ještě neudělali, [Připojte](#connect-to-azure-account) se k účtu Azure.

2. Klikněte na ikona **Azure** ze sloupce úplně vlevo.

3. V levém podokně rozbalte položku **Azure HDInsight**. Zobrazí se seznam dostupných předplatných a clusterů.

4. Rozbalte cluster pro zobrazení databáze metadat podregistru a schématu tabulky.

5. Klikněte pravým tlačítkem na tabulku podregistru, například hivesampletable. Vyberte **Náhled**. 

   ![Podregistr Spark & pro tabulku podregistru pro Visual Studio Code Preview](./media/hdinsight-for-vscode/hdinsight-for-vscode-preview-hive-table.png)

6. Otevře se okno **výsledky náhledu** .

   ![Podregistr Spark & pro okno výsledků náhledu Code Preview pro Visual Studio](./media/hdinsight-for-vscode/hdinsight-for-vscode-preview-results-window.png)
   
- Panel **výsledků**

   Celý výsledek můžete uložit jako soubor CSV, JSON nebo Excel do místní cesty nebo stačí vybrat více řádků.

- Panel **zpráv**
   1. Pokud je počet řádků v tabulce větší než 100 řádků, zobrazí se tato zpráva: **Pro tabulku podregistru se zobrazí prvních 100 řádků**.
   2. Pokud je počet řádků v tabulce menší nebo roven 100 řádků, zobrazí se tato zpráva: **pro tabulku podregistru se zobrazí 60 řádků**.
   3. Pokud tabulka neobsahuje žádný obsah, zobrazí se zpráva: **pro tabulku podregistru se zobrazí 0 řádek**.

>[!NOTE]
>
>V systému Linux nainstalujte xclip pro povolení kopírování dat tabulky.
>
>![Podregistr Spark & pro Visual Studio Code v systému Linux](./media/hdinsight-for-vscode/hdinsight-for-vscode-preview-linux-install-xclip.png)
## <a name="additional-features"></a>Další funkce

Podregistr Spark & pro Visual Studio Code podporuje následující funkce:

- **Automatické dokončování IntelliSense**. Návrhy se zobrazují pro klíčová slova, metody, proměnné a tak dále. Různé ikony reprezentují různé typy objektů.

    ![Nástroje pro podregistr Spark & pro Visual Studio Code typy objektů IntelliSense](./media/hdinsight-for-vscode/hdinsight-for-vscode-auto-complete-objects.png)
- **Značka chyby technologie IntelliSense**. Služba jazyka podřádkuje chyby v úpravách pro skript podregistru.     
- **Zvýrazňují syntaxe**. Služba jazyka používá různé barvy k odlišení proměnných, klíčových slov, datových typů, funkcí a tak dále. 

    ![& Nástroje pro podregistr pro Spark pro Visual Studio Code zvýraznění syntaxe](./media/hdinsight-for-vscode/hdinsight-for-vscode-syntax-highlights.png)

## <a name="reader-only-role"></a>Role jenom čtenářů

Uživatelé s **rolí** **pouze** **pro čtenáře** clusteru již nemohou odeslat úlohu do clusteru HDInsight ani zobrazit databázi podregistru. Obraťte se na správce clusteru a upgradujte svoji roli na [   **operátor** clusteru HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-migrate-granular-access-cluster-configurations#add-the-hdinsight-cluster-operator-role-assignment-to-a-user) v [Azure Portal](https://ms.portal.azure.com/). Pokud znáte přihlašovací údaje Ambari, můžete ručně propojit cluster podle pokynů níže.

### <a name="browse-hdinsight-cluster"></a>Procházet cluster HDInsight  

Když při kliknutí na Průzkumníka služby Azure HDInsight rozbalíte cluster HDInsight, budete vyzváni k propojení clusteru, pokud jste pro cluster pouze roli Čtenář. Pomocí následujících kroků můžete propojit cluster prostřednictvím přihlašovacích údajů Ambari. 

### <a name="submit-job-to-hdinsight-cluster"></a>Odeslat úlohu do clusteru HDInsight

Při odesílání úlohy do clusteru HDInsight budete vyzváni, abyste cluster propojíte, pokud jste pro tento cluster pouze roli čtenářů. Pomocí následujících kroků můžete propojit cluster prostřednictvím přihlašovacích údajů Ambari. 

### <a name="link-to-cluster"></a>Odkaz na cluster

1.  Zadejte uživatelské jméno Ambari. 
2.  Zadejte heslo uživatele Ambari.

   ![Nástroje pro podregistr Spark & pro Visual Studio Code uživatelské jméno](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-username.png)

   ![Nástroje Spark & pro Visual Studio Code hesla](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-password.png)

> [!NOTE]
>
>Můžete použít Spark/podregistr: Vypíše cluster pro kontrolu propojeného clusteru.
>
>![Připojené nástroje pro Visual Studio Code Reader pro Spark &](./media/hdinsight-for-vscode/list-cluster-result.png)

## <a name="azure-data-lake-storage-gen2-adls-gen2"></a>Azure Data Lake Storage Gen2 (ADLS Gen2)

### <a name="browse-an-adls-gen2-account"></a>Procházet účet ADLS Gen2

Když kliknete na Průzkumníka Azure HDInsight a rozbalíte účet ADLS Gen2, zobrazí se výzva k zadání přístupového **klíče** k úložišti, pokud váš účet Azure nemá přístup k úložišti Gen2. Po úspěšném ověření přístupového klíče se účet ADLS Gen2 automaticky rozšíří. 

### <a name="submit-jobs-to-hdinsight-cluster-with-adls-gen2"></a>Odeslání úloh do clusteru HDInsight pomocí ADLS Gen2

Při odesílání úlohy do clusteru HDInsight s ADLS Gen2 se zobrazí výzva k zadání **přístupového klíče** k úložišti, pokud váš účet Azure nemá přístup pro zápis do úložiště Gen2.  Úloha bude úspěšně odeslána po úspěšném ověření přístupového klíče. 

![Nástroje pro podregistr Spark & pro Visual Studio Code AccessKey](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-accesskey.png)   

> [!NOTE]
> 
>Přístupový klíč pro účet úložiště můžete získat z Azure Portal. Informace najdete v tématu [zobrazení a zkopírování přístupových klíčů](https://docs.microsoft.com/azure/storage/common/storage-account-manage#access-keys).

## <a name="unlink-cluster"></a>Zrušit propojení clusteru

1. V řádku nabídek přejděte k **zobrazení** > **paleta příkazů...** a pak zadejte **Spark/podregistr: Zrušení propojení clusteru**  

2. Vyberte cluster, který chcete odpojit.  

3. Zkontrolujte zobrazení **výstupu** pro ověření.  

## <a name="sign-out"></a>Odhlásit se  

V řádku nabídek přejděte k **zobrazení** > **paleta příkazů...** a pak zadejte **Azure: Odhlaste se**.


## <a name="next-steps"></a>Další postup
Ukázku videa o použití podregistru Spark & pro Visual Studio Code najdete v tématu [podregistr spark & pro Visual Studio Code](https://go.microsoft.com/fwlink/?linkid=858706)
