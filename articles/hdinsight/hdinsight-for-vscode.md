---
title: Kód Azure HDInsight pro Visual Studio
description: Přečtěte si, jak používat nástroje Spark & Hive (Azure HDInsight) pro kód Visual Studia k vytváření a odesílání dotazů a skriptů.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/11/2019
ms.openlocfilehash: 9a81868d678b4c0277e904e879c73185a378bf70
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75435688"
---
# <a name="use-spark--hive-tools-for-visual-studio-code"></a>Použití nástrojů Spark & Hive pro kód sady Visual Studio

Naučte se používat Spark & Hive Tools for Visual Studio Code k vytváření a odesílání dávkových úloh Apache Hive, interaktivních dotazů Hive a skriptů PySpark pro Apache Spark. Nejprve popíšeme, jak nainstalovat Spark & Hive Tools v kódu Visual Studia, a pak si projdeme, jak odeslat úlohy do Spark & Hive Tools.  

Spark & Hive Tools lze nainstalovat na platformách, které jsou podporovány Visual Studio Code, které zahrnují Windows, Linux a macOS. Všimněte si následujících předpokladů pro různé platformy.

## <a name="prerequisites"></a>Požadavky

Následující položky jsou vyžadovány pro dokončení kroků v tomto článku:

- Cluster Azure HDInsight. Pokud chcete vytvořit cluster, přečtěte si informace [o tom, jak začít pracovat s HDInsight](hadoop/apache-hadoop-linux-create-cluster-get-started-portal.md). Nebo použijte cluster Spark a Hive, který podporuje koncový bod Apache Livy.
- [Visual Studio kód](https://code.visualstudio.com/).
- [Mono](https://www.mono-project.com/docs/getting-started/install/). Mono je vyžadováno pouze pro Linux a macOS.
- [Interaktivní prostředí PySpark pro kód sady Visual Studio](set-up-pyspark-interactive-environment.md).
- Místní adresář. Tento článek používá **C:\HD\HDexample**.

## <a name="install-spark--hive-tools"></a>Instalace nástrojů Spark & Hive

Po splnění požadavků můžete nainstalovat Spark & Hive Tools pro visual studio kód následujícím způsobem:

1. Otevřete Visual Studio Code.

2. Na řádku nabídek přejděte na **Zobrazit** > **rozšíření**.

3. Do vyhledávacího pole zadejte **Spark & Hive**.

4. Ve výsledcích hledání vyberte **Nástroje pro & podhled Spark** a pak vyberte **Instalovat**:

   ![Instalace aplikace Spark & Hive for Visual Studio Code Python install](./media/hdinsight-for-vscode/install-hdInsight-plugin.png)

5. V případě potřeby vyberte **znovu načíst.**

## <a name="open-a-work-folder"></a>Otevření pracovní složky

Chcete-li otevřít pracovní složku a vytvořit soubor v kódu sady Visual Studio, postupujte takto:

1. Na řádku nabídek přejděte na Složku pro otevření **souborů...** > **Open Folder...**  >  **C:\HD\HDexample**a pak vyberte tlačítko **Vybrat složku.** Složka se zobrazí v zobrazení **Průzkumníka** vlevo.

2. V **zobrazení Průzkumníka** vyberte složku **HDexample** a pak vedle pracovní složky vyberte ikonu **Nový soubor:**

   ![ikona nového souboru kódu visual studia](./media/hdinsight-for-vscode/visual-studio-code-new-file.png)

3. Nový soubor pojmenujte `.hql` pomocí přípony souboru `.py` (Hive queries) nebo (Spark script). Tento příklad používá **HelloWorld.hql**.

## <a name="set-the-azure-environment"></a>Nastavení prostředí Azure

Pro uživatele národního cloudu nejprve nastavte prostředí Azure a pak pomocí příkazu **Azure: Přihlásit** se k Azure:

1. Přejděte do**nastavení****předvoleb** >  **souborů** > .
2. Hledejte podle následujícího řetězce: **Azure: Cloud**.
3. Vyberte národní cloud ze seznamu:

   ![Nastavení výchozí konfigurace položky přihlášení](./media/hdinsight-for-vscode/set-default-login-entry-configuration.png)

## <a name="connect-to-an-azure-account"></a>Připojení k účtu Azure

Než budete moct odesílat skripty do clusterů z visual studia Code, musíte se buď připojit ke svému účtu Azure, nebo propojit cluster (pomocí přihlašovacích údajů k uživatelskému jménu a heslu Apache Ambari nebo účtu k připojení k doméně). Chcete-li se připojit k Azure, postupujte takto:

1. Na řádku nabídek přejděte na **Zobrazit** > **paletu příkazů...** a zadejte **Azure: Přihlásit se**:

    ![Nástroje Spark & Hive pro přihlášení kódu sady Visual Studio](./media/hdinsight-for-vscode/hdinsight-for-vscode-extension-login.png)

2. Podle přihlašovacích pokynů se přihlaste k Azure. Po připojení se název účtu Azure zobrazí na stavovém řádku v dolní části okna Kódu Visual Studia.  

## <a name="link-a-cluster"></a>Propojení clusteru

### <a name="link-azure-hdinsight"></a>Odkaz: Azure HDInsight

Normální cluster můžete propojit pomocí uživatelského jména spravovaného [Apache Ambari](https://ambari.apache.org/)nebo můžete propojit zabezpečený cluster Hadoop sady `user1@contoso.com`Enterprise Security Pack pomocí uživatelského jména domény (například: ).

1. Z řádku nabídek přejděte na **Zobrazit** > **paletu příkazů...** a zadejte **Spark / Hive: Link a Cluster**.

   ![Příkaz propojení palety příkazů, příkaz](./media/hdinsight-for-vscode/link-cluster-command.png)

2. Vyberte typ propojeného **clusteru Azure HDInsight**.

3. Zadejte adresu URL clusteru HDInsight.

4. Zadejte své uživatelské jméno Ambari; Výchozí je **admin**.

5. Zadejte heslo ambari.

6. Vyberte typ clusteru.

7. Nastavte zobrazovaný název clusteru (volitelné).

8. Zkontrolujte zobrazení **VÝSTUPU** pro ověření.

   > [!NOTE]  
   > Připojené uživatelské jméno a heslo se používají, pokud cluster usměrňuje přihlášení k předplatnému Azure a propojuje cluster.  

### <a name="link-generic-livy-endpoint"></a>Odkaz: Obecný koncový bod Livy

1. Z řádku nabídek přejděte na **Zobrazit** > **paletu příkazů...** a zadejte **Spark / Hive: Link a Cluster**.

2. Vyberte propojený typ **clusteru Obecný koncový bod Livy**.

3. Zadejte obecný koncový bod Livy. Například:\:http //10.172.41.42:18080.

4. Vyberte typ autorizace **Basic** nebo **None**.  Pokud vyberete **základní**:  
    &emsp;A. Zadejte své uživatelské jméno Ambari; Výchozí je **admin**.  
    &emsp;B. Zadejte heslo ambari.

5. Zkontrolujte zobrazení **VÝSTUPU** pro ověření.

## <a name="list-clusters"></a>Seznam clusterů

1. Z řádku nabídek přejděte na **Zobrazit** > **paletu příkazů...** a zadejte **Spark / Hive: List Cluster**.

2. Vyberte požadované předplatné.

3. Zkontrolujte zobrazení **VÝSTUP.** Toto zobrazení zobrazuje propojený cluster (nebo clustery) a všechny clustery v rámci předplatného Azure:

    ![Nastavení výchozí konfigurace clusteru](./media/hdinsight-for-vscode/list-cluster-result1.png)

## <a name="set-the-default-cluster"></a>Nastavení výchozího clusteru

1. Znovu otevřete složku **HDexample,** která byla popsána [dříve](#open-a-work-folder), pokud je uzavřena.  

2. Vyberte soubor **HelloWorld.hql,** který byl vytvořen [dříve](#open-a-work-folder). Otevře se v editoru skriptů.

3. Klikněte pravým tlačítkem myši na editor skriptů a vyberte **Spark / Hive: Set Default Cluster**.  

4. [Připojte](#connect-to-an-azure-account) se ke svému účtu Azure nebo propojte cluster, pokud jste tak ještě neučinili.

5. Vyberte cluster jako výchozí cluster pro aktuální soubor skriptu. Nástroje automaticky aktualizují **. Konfigurační soubor VSCode\settings.json:**

   ![Nastavení výchozí konfigurace clusteru](./media/hdinsight-for-vscode/set-default-cluster-configuration.png)

## <a name="submit-interactive-hive-queries-and-hive-batch-scripts"></a>Odeslání interaktivních dotazů hive a dávkových skriptů Hive

Pomocí nástroje Spark & Hive pro kód sady Visual Studio můžete do clusterů odesílat interaktivní dotazy Hive a dávkové skripty Hive.

1. Znovu otevřete složku **HDexample,** která byla popsána [dříve](#open-a-work-folder), pokud je uzavřena.  

2. Vyberte soubor **HelloWorld.hql,** který byl vytvořen [dříve](#open-a-work-folder). Otevře se v editoru skriptů.

3. Zkopírujte a vložte do souboru Hive následující kód a uložte ho:

    ```hiveql
    SELECT * FROM hivesampletable;
    ```

4. [Připojte](#connect-to-an-azure-account) se ke svému účtu Azure nebo propojte cluster, pokud jste tak ještě neučinili.

5. Klikněte pravým tlačítkem myši na editor skriptů a **výběrem možnosti Hive: Interaktivní** odešlete dotaz nebo použijte klávesovou zkratku Ctrl+Alt+I.  Vyberte **Úl: Dávka** pro odeslání skriptu nebo použijte klávesovou zkratku Ctrl+Alt+H.  

6. Pokud jste nezadali výchozí cluster, vyberte cluster. Nástroje také umožňují odeslat blok kódu namísto celého souboru skriptu pomocí kontextové nabídky. Po chvíli se výsledky dotazu zobrazí na nové kartě:

   ![Výsledek interaktivního dotazu Apache Hive](./media/hdinsight-for-vscode/interactive-hive-result.png)

    - **Panel VÝSLEDKY:** Celý výsledek můžete uložit jako soubor CSV, JSON nebo Excel na místní cestu nebo stačí vybrat více řádků.

    - **Panel ZPRÁVY:** Když vyberete číslo **řádku,** přejde na první řádek spuštěného skriptu.

## <a name="submit-interactive-pyspark-queries"></a>Odeslat interaktivní dotazy PySpark

Chcete-li odeslat interaktivní dotazy PySpark, postupujte takto:

1. Znovu otevřete složku **HDexample,** která byla popsána [dříve](#open-a-work-folder), pokud je uzavřena.  

2. Vytvořte nový **soubor HelloWorld.py** podle [předchozích](#open-a-work-folder) kroků.

3. Zkopírujte a vložte do souboru skriptu následující kód:

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

4. [Připojte](#connect-to-an-azure-account) se ke svému účtu Azure nebo propojte cluster, pokud jste tak ještě neučinili.

5. Vyberte veškerý kód, klikněte pravým tlačítkem myši na editor skriptů a **výběrem možnosti Spark: PySpark Interactive** odešlete dotaz. Nebo použijte zástupce Ctrl+Alt+I.

   ![interaktivní kontextová nabídka pyspark](./media/hdinsight-for-vscode/pyspark-interactive-right-click.png)

6. Pokud jste nezadali výchozí cluster, vyberte cluster. Po několika okamžicích se výsledky **Pythonu Interactive** zobrazí na nové kartě. Nástroje také umožňují odeslat blok kódu namísto celého souboru skriptu pomocí kontextové nabídky:

   ![interaktivní okno interaktivní python pyspark](./media/hdinsight-for-vscode/pyspark-interactive-python-interactive-window.png)

7. Zadejte **%%info**a stisknutím kláves Shift+Enter zobrazte informace o úloze (volitelné):

   ![interaktivní zobrazení informací o práci pyspark](./media/hdinsight-for-vscode/pyspark-interactive-view-job-information.png)

8. Nástroj také podporuje dotaz **Spark SQL:**

   ![Výsledek interaktivního zobrazení Pyspark](./media/hdinsight-for-vscode/pyspark-ineteractive-select-result.png)

   Stav odeslání se při spouštění dotazů zobrazí vlevo od dolního stavového řádku. Neodesílejte další dotazy, pokud je stav **PySpark Kernel (zaneprázdněn)**.  

   > [!NOTE]
   >
   > Když je **rozšíření Pythonu povoleno** v nastavení (je ve výchozím nastavení vybrané), předložené výsledky interakce pyspark použijí staré okno:
   >
   > ![rozšíření interaktivní python pyspark zakázáno](./media/hdinsight-for-vscode/pyspark-interactive-python-extension-disabled.png)

## <a name="submit-pyspark-batch-job"></a>Odeslat dávkovou úlohu PySpark

1. Znovu otevřete složku **HDexample,** o které jste [diskutovali dříve](#open-a-work-folder), pokud je uzavřena.  

2. Vytvořte nový **soubor BatchFile.py** podle [předchozích](#open-a-work-folder) kroků.

3. Zkopírujte a vložte do souboru skriptu následující kód:

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

4. [Připojte](#connect-to-an-azure-account) se ke svému účtu Azure nebo propojte cluster, pokud jste tak ještě neučinili.

5. Klikněte pravým tlačítkem myši na editor skriptů a pak vyberte **Spark: PySpark Batch**nebo použijte klávesovou zkratku Ctrl+Alt+H.

6. Vyberte cluster, do který chcete odeslat úlohu PySpark:

   ![Odeslat výstup výsledku úlohy Pythonu](./media/hdinsight-for-vscode/submit-pythonjob-result.png)

Po odeslání úlohy Pythonu se v okně **VÝSTUP** v kódu sady Visual Studio zobrazí protokoly odeslání. Zobrazí se také adresa URL ui spark a adresa url ui příze. Adresu URL můžete otevřít ve webovém prohlížeči a sledovat stav úlohy.

## <a name="apache-livy-configuration"></a>Konfigurace Apache Livy

[Konfigurace Apache Livy](https://livy.incubator.apache.org/) je podporována. Můžete jej nakonfigurovat v rozhraní **. Soubor VSCode\settings.json** ve složce pracovního prostoru. V současné době konfigurace Livy podporuje pouze skript Pythonu. Další podrobnosti viz [Livy README](https://github.com/cloudera/livy/blob/master/README.rst ).

<a id="triggerlivyconf"></a>**Jak spustit konfiguraci Livy**

Metoda 1  
1. V řádku nabídek přejděte do**nastavení****předvoleb** >  **souborů** > .
2. Do pole **Nastavení vyhledávání** zadejte **hdinsight job submission: Livy Conf**.  
3. Vyberte **Upravit v souboru settings.json** pro příslušný výsledek hledání.

Metoda 2 Odešlete soubor a všimněte si, že složka VSCODE je automaticky přidána do pracovní složky. Konfiguraci Livy můžete zobrazit výběrem souboru **.vscode\settings.json**.

+ Nastavení projektu:

    ![Konfigurace HDInsight Apache Livy](./media/hdinsight-for-vscode/hdi-apache-livy-config.png)

    >[!NOTE]
    >Pro nastavení **driverMemory** a **executorMemory** nastavte hodnotu a jednotku. Například: 1g nebo 1024m.

+ Podporované konfigurace Livy:

    **POST /dávky** Tělo požadavku

    | jméno | description | type |
    | :- | :- | :- |
    |  – soubor | Soubor obsahující aplikaci ke spuštění | Cesta (povinné) |
    | proxyUživatel | Uživatel zosobnit při spuštění úlohy | Řetězec |
    | Classname | Aplikace Java/Spark hlavní třída | Řetězec |
    | args | Argumenty příkazového řádku pro aplikaci | Seznam řetězců |
    | Sklenice | Sklenice, které mají být použity v této relaci | Seznam řetězců | 
    | pySoubory | Soubory Pythonu, které mají být použity v této relaci | Seznam řetězců |
    | files | Soubory, které mají být použity v této relaci | Seznam řetězců |
    | driverMemory | Velikost paměti, která má být využita pro proces ovladače | Řetězec |
    | driverCores | Počet jader, které mají být pro proces ovladače | Int |
    | executorMemory | Velikost paměti, která má být využita na proces vykonavatele | Řetězec |
    | executorCores | Počet jader, které mají být pro každého vykonavatele | Int |
    | numExecutors | Počet vykonavatelů, kteří mají být spuštěni pro tuto relaci | Int |
    | Archiv | Archivy, které mají být použity v této relaci | Seznam řetězců |
    | fronta | Název fronty YARN, do které má být odeslána| Řetězec |
    | jméno | Název této relace | Řetězec |
    | Conf | Vlastnosti konfigurace jiskry | Mapa klíče=val |

    Tělo odezvy Vytvořený objekt Batch.

    | jméno | description | type |
    | :- | :- | :- |
    | id | ID relace | Int |
    | appId | ID aplikace této relace | Řetězec |
    | appInfo | Podrobné informace o aplikaci | Mapa klíče=val |
    | Protokolu | Řádky protokolu | Seznam řetězců |
    | state |Stav dávky | Řetězec |

    > [!NOTE]
    > Přiřazená konfigurace Livy se zobrazí ve výstupním podokně při odeslání skriptu.

## <a name="integrate-with-azure-hdinsight-from-explorer"></a>Integrace s Azure HDInsight z Průzkumníka

Zobrazit náhled tabulky Hive ve vašich clusterech můžete zobrazit přímo prostřednictvím průzkumníka **Azure HDInsight:**

1. [Pokud](#connect-to-an-azure-account) jste to ještě neudělali, připojte se ke svému účtu Azure.

2. Vyberte ikonu **Azure** ze sloupce zcela vlevo.

3. V levém podokně rozbalte **azure: HDINSIGHT**. Dostupné předplatná a clustery jsou uvedeny.

4. Rozbalte cluster a zobrazte databázi metadat Hive a schéma tabulky.

5. Klikněte pravým tlačítkem myši na tabulku Úl. Například: **hivesampletable**. Vyberte **Náhled**.

   ![Podregistr Spark & pro podregistr kódu Visual Studia](./media/hdinsight-for-vscode/hdinsight-for-vscode-preview-hive-table.png)

6. Otevře se okno **Náhled výsledků:**

   ![Okno výsledků náhledu kódu aplikace Spark & Hive pro visual studio](./media/hdinsight-for-vscode/hdinsight-for-vscode-preview-results-window.png)

- Panel VÝSLEDKY

   Celý výsledek můžete uložit jako soubor CSV, JSON nebo Excel na místní cestu nebo stačí vybrat více řádků.

- Panel ZPRÁVY
   1. Pokud je počet řádků v tabulce větší než 100, zobrazí se následující zpráva: "Prvních 100 řádků jsou zobrazeny pro tabulku Hive."
   2. Pokud je počet řádků v tabulce menší nebo roven 100, zobrazí se zpráva jako následující: "Pro tabulku Hive se zobrazí 60 řádků."
   3. Pokud v tabulce není žádný obsah, zobrazí se následující zpráva: "Pro tabulku Hive se zobrazí 0 řádků."

        >[!NOTE]
        >
        >V Linuxu nainstalujte xclip, abyste povolili data kopírovací tabulky.
        >
        >![Spark & Hive pro kód Visual Studia v Linuxu](./media/hdinsight-for-vscode/hdinsight-for-vscode-preview-linux-install-xclip.png)

## <a name="additional-features"></a>Další funkce

Spark & Hive pro Visual Studio Code také podporuje následující funkce:

- **Automatické dokončování technologie IntelliSense**. Návrhy pop-up pro klíčová slova, metody, proměnné a další programovací prvky. Různé ikony představují různé typy objektů:

    ![Nástroje Spark & Hive pro objekty Visual Studio Code IntelliSense](./media/hdinsight-for-vscode/hdinsight-for-vscode-auto-complete-objects.png)

- **Značka chyb technologie IntelliSense**. Jazyková služba podtrhne chyby při úpravách ve skriptu Hive.     
- **Syntaxe zdůrazňuje**. Služba jazyka používá různé barvy k rozlišení proměnných, klíčových slov, datového typu, funkcí a dalších programovacích prvků:

    ![Spark & Hive Nástroje pro zvýraznění syntaxe kódu sady Visual Studio](./media/hdinsight-for-vscode/hdinsight-for-vscode-syntax-highlights.png)

## <a name="reader-only-role"></a>Role pouze pro čtenáře

Uživatelé, kterým je přiřazena role pouze pro čtení pro cluster, již nemohou odesílat úlohy do clusteru HDInsight, ani nemohou zobrazit databázi Hive. Obraťte se na správce clusteru a upgradujte svou roli na [**operátora clusteru HDInsight**](https://docs.microsoft.com/azure/hdinsight/hdinsight-migrate-granular-access-cluster-configurations#add-the-hdinsight-cluster-operator-role-assignment-to-a-user) na [webu Azure Portal](https://ms.portal.azure.com/). Pokud máte platná pověření Ambari, můžete ručně propojit cluster pomocí následujících pokynů.

### <a name="browse-the-hdinsight-cluster"></a>Procházení clusteru HDInsight  

Když vyberete průzkumníka Azure HDInsight pro rozšíření clusteru HDInsight, budete vyzváni k propojení clusteru, pokud máte roli pouze pro čtečku pro cluster. Pomocí následující metody můžete propojit cluster pomocí pověření Ambari.

### <a name="submit-the-job-to-the-hdinsight-cluster"></a>Odeslání úlohy do clusteru HDInsight

Při odesílání úlohy do clusteru HDInsight budete vyzváni k propojení clusteru, pokud jste v roli pouze pro čtenáře pro cluster. Pomocí následujících kroků můžete propojit cluster pomocí pověření Ambari.

### <a name="link-to-the-cluster"></a>Propojení se clusterem

1. Zadejte platné uživatelské jméno Ambari.
2. Zadejte platné heslo.

   ![Nástroje Spark & Hive pro uživatelské jméno kódu sady Visual Studio](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-username.png)

   ![Nástroje spark & Hive pro heslo kódu sady Visual Studio](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-password.png)

  > [!NOTE]
  >
  >Můžete zkontrolovat `Spark / Hive: List Cluster` propojený cluster:
  >
  >![Nástroje Spark & Hive pro propojenou čtečku kódů visual studia](./media/hdinsight-for-vscode/list-cluster-result1.png)

## <a name="azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2

### <a name="browse-a-data-lake-storage-gen2-account"></a>Procházení účtu Data Lake Storage Gen2

Když vyberete průzkumníka Azure HDInsight pro rozšíření účtu Data Lake Storage Gen2, budete vyzváni k zadání přístupového klíče úložiště, pokud váš účet Azure nemá přístup k úložišti Gen2. Po ověření přístupového klíče se účet Gen2 úložiště datového jezera automaticky rozbalí.

### <a name="submit-jobs-to-an-hdinsight-cluster-with-data-lake-storage-gen2"></a>Odeslání úloh do clusteru HDInsight s datem Jezera Gen2

Když odešlete úlohu do clusteru HDInsight pomocí Data Lake Storage Gen2, budete vyzváni k zadání přístupového klíče úložiště, pokud váš účet Azure nemá přístup pro zápis do úložiště Gen2. Po ověření přístupového klíče bude úloha úspěšně odeslána.

![Nástroje spark & Hive pro přístupový klíč kódu sady Visual Studio](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-accesskey.png)

> [!NOTE]
>
> Přístupový klíč pro účet úložiště můžete získat z webu Azure Portal. Další informace naleznete v [tématu Správa přístupových klíčů účtu úložiště](../storage/common/storage-account-keys-manage.md).

## <a name="unlink-cluster"></a>Zrušení propojení clusteru

1. Na řádku nabídek přejděte na **Zobrazit** > **paletu příkazů**a zadejte Spark / **Hive: Unlink a Cluster**.  

2. Vyberte cluster, který chcete odpojit.  

3. Zobrazení **VÝSTUP** naleznete v zobrazení VÝSTUP.  

## <a name="sign-out"></a>Odhlásit se  

Na řádku nabídek přejděte na **Zobrazit** > **paletu příkazů**a zadejte **Azure: Odhlásit se**.

## <a name="next-steps"></a>Další kroky

Video, které ukazuje použití Spark & Hive pro kód Visual Studio, najdete v [tématu Spark & Hive for Visual Studio Code](https://go.microsoft.com/fwlink/?linkid=858706).
