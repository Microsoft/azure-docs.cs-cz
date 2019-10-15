---
title: Azure HDInsight pro Visual Studio Code
description: Naučte se používat nástroje pro podregistr Spark & (Azure HDInsight) pro Visual Studio Code k vytváření a odesílání dotazů a skriptů.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/11/2019
ms.openlocfilehash: 03d0d26a21e710c07019d3ffcb13a1482a96af50
ms.sourcegitcommit: 9dec0358e5da3ceb0d0e9e234615456c850550f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/14/2019
ms.locfileid: "72311732"
---
# <a name="use-spark--hive-tools-for-visual-studio-code"></a>Použití nástrojů pro podregistr Spark & pro Visual Studio Code

Naučte se používat nástroje Spark & podregistr pro Visual Studio Code k vytváření a odesílání Apache Hive dávkových úloh, interaktivních dotazů na podregistru a skriptů PySpark pro Apache Spark. Nejprve popíšeme, jak nainstalovat nástroje Spark & v Visual Studio Code a pak si projdeme, jak odesílat úlohy do nástrojů pro podregistr Spark &.  

Nástroje Spark & podregistr lze nainstalovat na platformy podporované Visual Studio Code, které zahrnují systémy Windows, Linux a macOS. Všimněte si následujících požadavků pro různé platformy.

## <a name="prerequisites"></a>Předpoklady

K dokončení kroků v tomto článku jsou vyžadovány následující položky:

- Cluster Azure HDInsight. Pokud chcete vytvořit cluster, přečtěte si téma Začínáme [se službou HDInsight](hadoop/apache-hadoop-linux-create-cluster-get-started-portal.md). Můžete také použít cluster Spark a podregistr, který podporuje koncový bod Apache Livy.
- [Visual Studio Code](https://code.visualstudio.com/).
- [Mono](https://www.mono-project.com/docs/getting-started/install/). Mono se vyžaduje jenom pro Linux a macOS.
- [PySpark interaktivní prostředí pro Visual Studio Code](set-up-pyspark-interactive-environment.md).
- Místní adresář. Tento článek používá **C:\HD\HDexample**.

## <a name="install-spark--hive-tools"></a>Instalace nástrojů pro podregistr Spark &

Po splnění požadavků můžete nainstalovat nástroje Spark & pro Visual Studio Code pomocí následujících kroků:

1. Otevřete Visual Studio Code.

2. V řádku nabídek přejděte k **zobrazení** **rozšíření** > .

3. Do vyhledávacího pole zadejte **Spark & podregistr**.

4. Z výsledků hledání vyberte **Spark & nástroje pro podregistr** a pak vyberte **instalovat**:

   ![Podregistr Spark & pro instalaci Visual Studio Code Pythonu](./media/hdinsight-for-vscode/install-hdInsight-plugin.png)

5. V případě potřeby vyberte **znovu načíst** .

## <a name="open-a-work-folder"></a>Otevření pracovní složky

Chcete-li otevřít pracovní složku a vytvořit soubor v Visual Studio Code, postupujte podle následujících kroků:

1. V řádku nabídek přejděte na do **souboru**@no__t otevřít složku-1 **...**  > **C:\HD\HDexample**a pak vyberte tlačítko **Vybrat složku** . Složka se zobrazí v zobrazení **Průzkumník** na levé straně.

2. V zobrazení **Průzkumník** vyberte složku **HDexample** a pak vyberte ikonu **nového souboru** vedle pracovní složky:

   ![Ikona nového souboru pro Visual Studio Code](./media/hdinsight-for-vscode/visual-studio-code-new-file.png)

3. Pojmenujte nový soubor pomocí `.hql` (dotazy na podregistr) nebo přípony souboru `.py` (Spark Script). V tomto příkladu se používá **HelloWorld. HQL**.

## <a name="set-the-azure-environment"></a>Nastavení prostředí Azure

V případě národního cloudového uživatele proveďte následující kroky a nastavte prostředí Azure jako první a pak se pomocí příkazu **Azure: Sign in** přihlaste k Azure:

1. Přejděte na **soubor** > **Předvolby** > **Nastavení**.
2. Vyhledejte následující řetězec: **Azure: Cloud**.
3. Ze seznamu vyberte národní Cloud:

   ![Nastavit výchozí konfiguraci zadání přihlašovacích údajů](./media/hdinsight-for-vscode/set-default-login-entry-configuration.png)

## <a name="connect-to-an-azure-account"></a>Připojení k účtu Azure

Než budete moct odesílat skripty do svých clusterů z Visual Studio Code, musíte se připojit ke svému účtu Azure nebo propojit cluster (s použitím přihlašovacích údajů k uživatelskému jménu Apache Ambari a hesla nebo účtu připojeného k doméně). Pomocí těchto kroků se připojte k Azure:

1. V řádku nabídek přejděte k **zobrazení** **palety příkazů  > ...** a zadejte **Azure: přihlásit**se:

    ![Nástroje pro podregistr Spark & pro přihlášení Visual Studio Code](./media/hdinsight-for-vscode/hdinsight-for-vscode-extension-login.png)

2. Podle pokynů pro přihlášení Přihlaste se k Azure. Po připojení se váš název účtu Azure zobrazí na stavovém řádku v dolní části okna Visual Studio Code.  

## <a name="link-a-cluster"></a>Propojení clusteru

### <a name="link-azure-hdinsight"></a>Odkaz: Azure HDInsight

Běžný cluster můžete propojit pomocí uživatelského jména spravovaného [Apache Ambari](https://ambari.apache.org/)nebo můžete propojit zabezpečený cluster sady Hadoop s podnikovým zabezpečením pomocí uživatelského jména domény (například: `user1@contoso.com`).

1. V řádku nabídek přejděte k **zobrazení** **palety příkazů  > ...** a zadejte **Spark/podregistr: propojení clusteru**.

   ![Příkaz pro propojení palety příkazů cluster](./media/hdinsight-for-vscode/link-cluster-command.png)

2. Vyberte typ propojeného clusteru **Azure HDInsight**.

3. Zadejte adresu URL clusteru HDInsight.

4. Zadejte své uživatelské jméno Ambari; Výchozí hodnota je **admin (správce**).

5. Zadejte své heslo Ambari.

6. Vyberte typ clusteru.

7. Nastavte zobrazovaný název clusteru (volitelné).

8. Zkontrolujte zobrazení **výstupu** pro ověření.

   > [!NOTE]  
   > Propojené uživatelské jméno a heslo se použijí, pokud je cluster přihlášený k předplatnému Azure i propojený cluster.  

### <a name="link-generic-livy-endpoint"></a>Odkaz: obecný koncový bod Livy

1. V řádku nabídek přejděte k **zobrazení** **palety příkazů  > ...** a zadejte **Spark/podregistr: propojení clusteru**.

2. Vyberte typ propojeného clusteru **obecný koncový bod Livy**.

3. Zadejte obecný koncový bod Livy. Například: http @ no__t-0//10.172.41.42:18080.

4. Vyberte typ autorizace **Basic** nebo **žádný**.  Pokud vyberete **základní**:  
    @no__t – 0a. Zadejte své uživatelské jméno Ambari; Výchozí hodnota je **admin (správce**).  
    @no__t – 0b. Zadejte své heslo Ambari.

5. Zkontrolujte zobrazení **výstupu** pro ověření.

## <a name="list-clusters"></a>Výpis clusterů

1. V řádku nabídek přejděte k **zobrazení** **palety příkazů  > ...** a zadejte **Spark/podregistr: list cluster**.

2. Vyberte předplatné, které chcete.

3. Zkontrolujte zobrazení **výstup** . Toto zobrazení ukazuje váš propojený cluster (nebo clustery) a všechny clustery v rámci vašeho předplatného Azure:

    ![Nastavení výchozí konfigurace clusteru](./media/hdinsight-for-vscode/list-cluster-result1.png)

## <a name="set-the-default-cluster"></a>Nastavení výchozího clusteru

1. Otevřete složku **HDexample** , která byla popsána [dříve](#open-a-work-folder), pokud je uzavřená.  

2. Vyberte soubor **HelloWorld. HQL** , který byl vytvořen [dříve](#open-a-work-folder). Otevře se v editoru skriptů.

3. Klikněte pravým tlačítkem na editor skriptů a pak vyberte **Spark/podregistr: nastavit výchozí cluster**.  

4. [Připojte](#connect-to-an-azure-account) se k účtu Azure nebo propojte cluster, pokud jste to ještě neudělali.

5. Vyberte cluster jako výchozí cluster pro aktuální soubor skriptu. Nástroje automaticky aktualizují **.** Konfigurační soubor VSCode\settings.JSON:

   ![Nastavit výchozí konfiguraci clusteru](./media/hdinsight-for-vscode/set-default-cluster-configuration.png)

## <a name="submit-interactive-hive-queries-and-hive-batch-scripts"></a>Odeslání interaktivních dotazů na podregistr a dávkových skriptů podregistru

Pomocí nástrojů pro podregistr Spark & pro Visual Studio Code můžete do svých clusterů odesílat interaktivní dotazy na podregistry a skripty Batch v podregistru.

1. Otevřete složku **HDexample** , která byla popsána [dříve](#open-a-work-folder), pokud je uzavřená.  

2. Vyberte soubor **HelloWorld. HQL** , který byl vytvořen [dříve](#open-a-work-folder). Otevře se v editoru skriptů.

3. Zkopírujte a vložte následující kód do souboru podregistru a pak ho uložte:

    ```hiveql
    SELECT * FROM hivesampletable;
    ```

4. [Připojte](#connect-to-an-azure-account) se k účtu Azure nebo propojte cluster, pokud jste to ještě neudělali.

5. Kliknutím pravým tlačítkem myši na editor skriptů a vybráním **podregistru: Interactive** odešlete dotaz nebo použijte klávesovou zkratku CTRL + ALT + I.  Vyberte **podregistr: Batch** pro odeslání skriptu nebo použijte klávesovou zkratku CTRL + ALT + H.  

6. Pokud jste nezadali výchozí cluster, vyberte cluster. Nástroje také umožňují odeslat blok kódu namísto celého souboru skriptu pomocí místní nabídky. Po chvíli se výsledky dotazu zobrazí na nové kartě:

   ![Výsledek interaktivního dotazu Apache Hive](./media/hdinsight-for-vscode/interactive-hive-result.png)

    - Panel **výsledků** : celý výsledek můžete uložit jako soubor CSV, JSON nebo Excel do místní cesty nebo stačí vybrat více řádků.

    - Panel **zprávy** : Když vyberete číslo **řádku** , přejde na první řádek běžícího skriptu.

## <a name="submit-interactive-pyspark-queries"></a>Odeslání interaktivních dotazů PySpark

K odeslání interaktivních dotazů PySpark použijte následující postup:

1. Otevřete složku **HDexample** , která byla popsána [dříve](#open-a-work-folder), pokud je uzavřená.  

2. Podle [předchozích](#open-a-work-folder) kroků vytvořte nový soubor **HelloWorld.py** .

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

4. [Připojte](#connect-to-an-azure-account) se k účtu Azure nebo propojte cluster, pokud jste to ještě neudělali.

5. Vyberte veškerý kód, klikněte pravým tlačítkem myši na editor skriptů a vyberte **Spark: PySpark Interactive** k odeslání dotazu. Nebo použijte zkratku CTRL + ALT + I.

   ![interaktivní kontextová nabídka pyspark](./media/hdinsight-for-vscode/pyspark-interactive-right-click.png)

6. Pokud jste nezadali výchozí cluster, vyberte cluster. Po chvíli se **interaktivní výsledky Pythonu** zobrazí na nové kartě. Nástroje také umožňují odeslat blok kódu namísto celého souboru skriptu pomocí místní nabídky:

   ![interaktivní okno pyspark interaktivního Pythonu](./media/hdinsight-for-vscode/pyspark-interactive-python-interactive-window.png)

7. Zadejte **%% info**a potom stiskněte SHIFT + ENTER, abyste zobrazili informace o úloze (volitelné):

   ![informace o úloze interaktivního zobrazení pyspark](./media/hdinsight-for-vscode/pyspark-interactive-view-job-information.png)

8. Nástroj také podporuje dotaz **Spark SQL** :

   ![Výsledek interaktivního zobrazení Pyspark](./media/hdinsight-for-vscode/pyspark-ineteractive-select-result.png)

   Stav odeslání se zobrazí vlevo od dolního stavového řádku při spouštění dotazů. Neodesílat další dotazy, pokud je stav **PySpark jádro (zaneprázdněno)** .  

   > [!NOTE]
   >
   > Pokud je v nastavení **povolené rozšíření Pythonu** (ve výchozím nastavení je vybrané), budou výsledky odeslaných interakcí pyspark používat staré okno:
   >
   > ![pyspark interaktivní rozšíření Pythonu je zakázané.](./media/hdinsight-for-vscode/pyspark-interactive-python-extension-disabled.png)

## <a name="submit-pyspark-batch-job"></a>Odeslat dávkovou úlohu PySpark

1. Otevřete složku **HDexample** , kterou jste si poznamenali [dříve](#open-a-work-folder), pokud je uzavřená.  

2. Pomocí [předchozích](#open-a-work-folder) kroků vytvořte nový soubor **BatchFile.py** .

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

4. [Připojte](#connect-to-an-azure-account) se k účtu Azure nebo propojte cluster, pokud jste to ještě neudělali.

5. Klikněte pravým tlačítkem myši na editor skriptů a pak vyberte **Spark: PySpark Batch**nebo použijte klávesovou zkratku CTRL + ALT + H.

6. Vyberte cluster, do kterého chcete odeslat úlohu PySpark:

   ![Odeslat výstup výsledku úlohy Pythonu](./media/hdinsight-for-vscode/submit-pythonjob-result.png)

Po odeslání úlohy Pythonu se protokoly odeslání zobrazí v okně **výstup** v Visual Studio Code. Zobrazí se také adresa URL uživatelského rozhraní Spark a adresa URL uživatelského rozhraní příze. Pro sledování stavu úlohy můžete otevřít adresu URL ve webovém prohlížeči.

## <a name="apache-livy-configuration"></a>Konfigurace Apache Livy

Konfigurace [Apache Livy](https://livy.incubator.apache.org/) je podporovaná. Můžete ji nakonfigurovat v **. Soubor VSCode\settings.json** ve složce pracovního prostoru. V současné době konfigurace Livy podporuje pouze skript Pythonu. Další podrobnosti najdete v tématu [LIVY Readme](https://github.com/cloudera/livy/blob/master/README.rst ).

<a id="triggerlivyconf"></a>**Jak aktivovat konfiguraci Livy**

Metoda 1  
1. V řádku nabídek přejděte na **soubor** > **Předvolby** > **Nastavení**.
2. Do pole **Nastavení hledání** zadejte **odeslání úlohy HDInsight: Livy conf**.  
3. Pro příslušný výsledek hledání vyberte **Upravit v nastavení. JSON** .

Metoda 2 odešle soubor a Všimněte si, že složka. VSCode je automaticky přidána do pracovní složky. Konfiguraci Livy můžete zobrazit tak, že vyberete **. vscode\settings.JSON**.

+ Nastavení projektu:

    ![Konfigurace HDInsight Apache Livy](./media/hdinsight-for-vscode/hdi-apache-livy-config.png)

    >[!NOTE]
    >Pro nastavení **driverMemory** a **executorMemory** nastavte hodnotu a jednotku. Například: 1G nebo 1024m.

+ Podporované konfigurace Livy:

    **Příspěvek/Batches** Text žádosti

    | jméno | description | type |
    | :- | :- | :- |
    | Souborů | Soubor obsahující aplikaci, která se má provést | Cesta (povinné) |
    | proxyUser | Uživatel, který se má zosobnit při spuštění úlohy | Řetězec |
    | NázevTřídy | Application – hlavní třída Java/Spark | Řetězec |
    | args | Argumenty příkazového řádku pro aplikaci | Seznam řetězců |
    | JAR | JAR, která se má použít v této relaci | Seznam řetězců | 
    | pyFiles | Soubory Pythonu, které se mají použít v této relaci | Seznam řetězců |
    | spis | Soubory, které se mají použít v této relaci | Seznam řetězců |
    | driverMemory | Velikost paměti, která se má použít pro proces ovladače | Řetězec |
    | driverCores | Počet jader, které se mají použít pro proces ovladače | Hmot |
    | executorMemory | Velikost paměti, která se má použít pro proces prováděcího modulu | Řetězec |
    | executorCores | Počet jader, které se mají použít pro každý prováděcí modul | Hmot |
    | numExecutors | Počet prováděcích modulů, které se mají spustit pro tuto relaci | Hmot |
    | archiv | Archivy, které se mají použít v této relaci | Seznam řetězců |
    | fronta | Název fronty PŘÍZe, která se má odeslat| Řetězec |
    | jméno | Název této relace | Řetězec |
    | výrobku | Vlastnosti konfigurace Sparku | Mapa klíče = Val |

    Tělo odpovědi vytvořeného objektu Batch

    | jméno | description | type |
    | :- | :- | :- |
    | id | ID relace | Hmot |
    | appId | ID aplikace této relace | Řetězec |
    | appInfo | Podrobné informace o aplikaci | Mapa klíče = Val |
    | protokolu | Řádky protokolu | Seznam řetězců |
    | state |Stav dávky | Řetězec |

    > [!NOTE]
    > Přiřazená konfigurace Livy se zobrazí v podokně výstup při odeslání skriptu.

## <a name="integrate-with-azure-hdinsight-from-explorer"></a>Integrace s Azure HDInsight z Průzkumníka

Náhled tabulky v clusterech můžete zobrazit přímo v Průzkumníkovi služby **Azure HDInsight** :

1. Pokud jste to ještě neudělali, [Připojte](#connect-to-an-azure-account) se k účtu Azure.

2. Vyberte ikonu **Azure** ze sloupce úplně vlevo.

3. V levém podokně rozbalte položku **Azure: HDInsight**. Jsou uvedeny dostupné odběry a clustery.

4. Rozbalte cluster pro zobrazení databáze metadat podregistru a schématu tabulky.

5. Klikněte pravým tlačítkem na tabulku podregistr. Například: **hivesampletable**. Vyberte **Náhled**.

   ![Podregistr Spark & pro tabulku podregistru Visual Studio Code Preview](./media/hdinsight-for-vscode/hdinsight-for-vscode-preview-hive-table.png)

6. Otevře se okno **výsledky náhledu** :

   ![Podregistr Spark & pro Visual Studio Code náhledu okna výsledků](./media/hdinsight-for-vscode/hdinsight-for-vscode-preview-results-window.png)

- Panel výsledků

   Celý výsledek můžete uložit jako soubor CSV, JSON nebo Excel do místní cesty nebo stačí vybrat více řádků.

- Panel zpráv
   1. Pokud je počet řádků v tabulce větší než 100, zobrazí se následující zpráva: "prvních 100 řádků se zobrazí pro tabulku podregistru."
   2. Pokud je počet řádků v tabulce menší nebo roven 100, zobrazí se zpráva podobná následující: "60 řádky jsou zobrazeny pro tabulku podregistru".
   3. Pokud tabulka neobsahuje žádný obsah, zobrazí se následující zpráva: "0 řádků pro tabulku podregistru".

        >[!NOTE]
        >
        >V systému Linux nainstalujte xclip a povolte tak data kopírování z tabulky.
        >
        >![Podregistr Spark & pro Visual Studio Code v systému Linux](./media/hdinsight-for-vscode/hdinsight-for-vscode-preview-linux-install-xclip.png)

## <a name="additional-features"></a>Další funkce

Podregistr Spark & pro Visual Studio Code také podporuje následující funkce:

- **Automatické dokončování IntelliSense**. Návrhy se zobrazují pro klíčová slova, metody, proměnné a další programovací prvky. Různé ikony reprezentují různé typy objektů:

    ![Nástroje pro Visual Studio Code IntelliSense pro Spark & pro objekty](./media/hdinsight-for-vscode/hdinsight-for-vscode-auto-complete-objects.png)

- **Značka chyby technologie IntelliSense**. Podtržení chyb v rámci skriptu služby jazyka.     
- **Zvýrazňují syntaxe**. Služba jazyka používá různé barvy k odlišení proměnných, klíčových slov, datových typů, funkcí a dalších prvků programování:

    ![& Nástroje pro podregistr pro Spark pro Visual Studio Code zvýraznění syntaxe](./media/hdinsight-for-vscode/hdinsight-for-vscode-syntax-highlights.png)

## <a name="reader-only-role"></a>Role jen pro čtení

Uživatelé, kteří mají přiřazenou roli jen pro čtení pro cluster, již nemohou odesílat úlohy do clusteru HDInsight ani nemohou zobrazit databázi podregistru. Obraťte se na správce clusteru a upgradujte svoji roli na [**operátor clusteru HDInsight**](https://docs.microsoft.com/azure/hdinsight/hdinsight-migrate-granular-access-cluster-configurations#add-the-hdinsight-cluster-operator-role-assignment-to-a-user) v [Azure Portal](https://ms.portal.azure.com/). Pokud máte platné přihlašovací údaje Ambari, můžete cluster ručně propojit pomocí následujících pokynů.

### <a name="browse-the-hdinsight-cluster"></a>Procházet cluster HDInsight  

Když vyberete Azure HDInsight Explorer pro rozšíření clusteru HDInsight, budete vyzváni k propojení clusteru, pokud máte pro cluster roli jen pro čtení. Pomocí následujícího postupu můžete propojit s clusterem pomocí přihlašovacích údajů Ambari.

### <a name="submit-the-job-to-the-hdinsight-cluster"></a>Odešlete úlohu do clusteru HDInsight.

Při odesílání úlohy do clusteru HDInsight budete vyzváni k propojení clusteru, pokud jste v roli jenom pro čtení clusteru. Pomocí následujících kroků se připojte ke clusteru pomocí přihlašovacích údajů Ambari.

### <a name="link-to-the-cluster"></a>Odkaz na cluster

1. Zadejte platné uživatelské jméno Ambari.
2. Zadejte platné heslo.

   ![Nástroje pro podregistr Spark & pro Visual Studio Code uživatelské jméno](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-username.png)

   ![Nástroje Spark & pro Visual Studio Code hesla](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-password.png)

  > [!NOTE]
  >
  >K zkontrolování propojeného clusteru můžete použít `Spark / Hive: List Cluster`:
  >
  >![Připojené nástroje pro Visual Studio Code Reader pro Spark &](./media/hdinsight-for-vscode/list-cluster-result1.png)

## <a name="azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2

### <a name="browse-a-data-lake-storage-gen2-account"></a>Procházet účet Data Lake Storage Gen2

Když vyberete Azure HDInsight Explorer a rozbalíte účet Data Lake Storage Gen2, zobrazí se výzva k zadání přístupového klíče k úložišti, pokud váš účet Azure nemá přístup k Gen2 úložišti. Po ověření přístupového klíče se účet Data Lake Storage Gen2 automaticky rozšíří.

### <a name="submit-jobs-to-an-hdinsight-cluster-with-data-lake-storage-gen2"></a>Odeslání úloh do clusteru HDInsight pomocí Data Lake Storage Gen2

Když odešlete úlohu do clusteru HDInsight pomocí Data Lake Storage Gen2, zobrazí se výzva k zadání přístupového klíče k úložišti, pokud váš účet Azure nemá přístup pro zápis k Gen2 úložišti. Po ověření přístupového klíče se úloha úspěšně odešle.

![Nástroje pro podregistr Spark & pro Visual Studio Code AccessKey](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-accesskey.png)

> [!NOTE]
>
> Přístupový klíč pro účet úložiště můžete získat z Azure Portal. Další informace najdete v tématu [zobrazení a zkopírování přístupových klíčů](https://docs.microsoft.com/azure/storage/common/storage-account-manage#access-keys).

## <a name="unlink-cluster"></a>Zrušit propojení clusteru

1. V řádku nabídek přejděte na **zobrazení** **paleta příkazů** >  a pak zadejte **Spark/podregistr: odpojte cluster**.  

2. Vyberte cluster, který chcete odpojit.  

3. Prohlédněte si zobrazení **výstupu** pro ověřování.  

## <a name="sign-out"></a>Odhlásit se  

V řádku nabídek přejděte na **zobrazení** **paleta příkazů** >  a pak zadejte **Azure: odhlásit**se.

## <a name="next-steps"></a>Další kroky

Video, které ukazuje použití podregistru Spark & pro Visual Studio Code, najdete v tématu věnovaném [registru spark & pro Visual Studio Code](https://go.microsoft.com/fwlink/?linkid=858706).
