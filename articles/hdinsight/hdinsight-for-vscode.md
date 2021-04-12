---
title: Azure HDInsight pro Visual Studio Code
description: Naučte se používat nástroje Spark & podregistr (Azure HDInsight) pro Visual Studio Code. Pomocí nástrojů můžete vytvářet a odesílat dotazy a skripty.
ms.service: hdinsight
ms.topic: how-to
ms.date: 10/20/2020
ms.custom: devx-track-python
ms.openlocfilehash: d098af394906dc120a252bdcda65fb3af31e28c8
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104865787"
---
# <a name="use-spark--hive-tools-for-visual-studio-code"></a>Použití nástrojů pro podregistr Spark & pro Visual Studio Code

Naučte se používat nástroje Apache Spark & podregistr pro Visual Studio Code. Pomocí nástrojů můžete vytvářet a odesílat Apache Hive dávkové úlohy, interaktivní dotazy podregistru a skripty PySpark pro Apache Spark. Nejdřív popíšeme, jak nainstalovat nástroje Spark & pro Visual Studio Code. Pak vás provedeme odesláním úloh do nástrojů Spark & podregistr.  

Nástroje Spark & podregistr je možné nainstalovat na platformy, které Visual Studio Code podporuje. Všimněte si následujících požadavků pro různé platformy.

## <a name="prerequisites"></a>Požadavky

K dokončení kroků v tomto článku jsou vyžadovány následující položky:

- Cluster Azure HDInsight. Pokud chcete vytvořit cluster, přečtěte si téma Začínáme [se službou HDInsight](hadoop/apache-hadoop-linux-create-cluster-get-started-portal.md). Můžete také použít cluster Spark a podregistr, který podporuje koncový bod Apache Livy.
- [Visual Studio Code](https://code.visualstudio.com/).
- [Mono](https://www.mono-project.com/docs/getting-started/install/). Mono se vyžaduje jenom pro Linux a macOS.
- [PySpark interaktivní prostředí pro Visual Studio Code](set-up-pyspark-interactive-environment.md).
- Místní adresář. Tento článek používá  **C:\HD\HDexample**.

## <a name="install-spark--hive-tools"></a>Instalace nástrojů pro podregistr Spark &

Po splnění požadavků můžete nainstalovat nástroje Spark & pro Visual Studio Code pomocí následujících kroků:

1. Otevřete Visual Studio Code.

2. V řádku nabídek přejděte k **zobrazení**  >  **rozšíření**.

3. Do vyhledávacího pole zadejte **Spark & podregistr**.

4. Z výsledků hledání vyberte **Spark & nástroje pro podregistr** a pak vyberte **instalovat**:

   :::image type="content" source="./media/hdinsight-for-vscode/install-hdInsight-plugin.png" alt-text="Podregistr Spark & pro instalaci Visual Studio Code Pythonu":::

5. V případě potřeby vyberte **znovu načíst** .

## <a name="open-a-work-folder"></a>Otevření pracovní složky

Chcete-li otevřít pracovní složku a vytvořit soubor v Visual Studio Code, postupujte podle následujících kroků:

1. V řádku nabídek přejděte do   >  **složky otevřít soubor...**  >  **C:\HD\HDexample** a pak vyberte tlačítko **Vybrat složku** . Složka se zobrazí v zobrazení **Průzkumník** na levé straně.

2. V zobrazení **Průzkumník** vyberte složku **HDexample** a pak vyberte ikonu **nového souboru** vedle pracovní složky:

   :::image type="content" source="./media/hdinsight-for-vscode/visual-studio-code-new-file.png" alt-text="Ikona nového souboru pro Visual Studio Code":::

3. Pojmenujte nový soubor tak, že použijete buď `.hql` příponu souboru (dotazy na podregistr) nebo `.py` (skript Spark). V tomto příkladu se používá **HelloWorld. HQL**.

## <a name="set-the-azure-environment"></a>Nastavení prostředí Azure

V případě národního cloudového uživatele proveďte následující kroky a nastavte prostředí Azure jako první a pak se pomocí příkazu **Azure: Sign in** přihlaste k Azure:

1. Přejděte na   >    >  **Nastavení** předvoleb souboru.
2. Vyhledejte následující řetězec: **Azure: Cloud**.
3. Ze seznamu vyberte národní Cloud:

   :::image type="content" source="./media/hdinsight-for-vscode/set-default-login-entry-configuration.png" alt-text="Nastavit výchozí konfiguraci zadání přihlašovacích údajů":::

## <a name="connect-to-an-azure-account"></a>Připojení k účtu Azure

Než budete moct odesílat skripty do svých clusterů z Visual Studio Code, uživatel se může přihlásit k předplatnému Azure nebo [propojit cluster HDInsight](#link-a-cluster). K připojení ke clusteru HDInsight použijte přihlašovací údaje pro Ambari uživatelské jméno/heslo nebo připojené k doméně pro cluster ESP. Pomocí těchto kroků se připojte k Azure:

1. V řádku nabídek přejděte k **zobrazení**  >  **paleta příkazů...** a zadejte **Azure: přihlásit** se:

   :::image type="content" source="./media/hdinsight-for-vscode/hdinsight-for-vscode-extension-login.png" alt-text="Nástroje pro podregistr Spark & pro přihlášení Visual Studio Code":::

2. Podle pokynů pro přihlášení Přihlaste se k Azure. Po připojení se název vašeho účtu Azure zobrazí na stavovém řádku v dolní části okna Visual Studio Code.  

## <a name="link-a-cluster"></a>Propojení clusteru

### <a name="link-azure-hdinsight"></a>Odkaz: Azure HDInsight

Běžný cluster můžete propojit pomocí uživatelského jména spravovaného [Apache Ambari](https://ambari.apache.org/)nebo můžete propojit zabezpečený cluster sady Hadoop s podnikovým zabezpečením pomocí uživatelského jména domény (například: `user1@contoso.com` ).

1. V řádku nabídek přejděte k **zobrazení**  >  **paleta příkazů...** a zadejte **Spark/podregistr: propojení clusteru**.

   :::image type="content" source="./media/hdinsight-for-vscode/link-cluster-command.png" alt-text="Příkaz pro propojení palety příkazů cluster":::

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

1. V řádku nabídek přejděte k **zobrazení**  >  **paleta příkazů...** a zadejte **Spark/podregistr: propojení clusteru**.

2. Vyberte typ propojeného clusteru **obecný koncový bod Livy**.

3. Zadejte obecný koncový bod Livy. Například: http \: //10.172.41.42:18080.

4. Vyberte typ autorizace **Basic** nebo **žádný**.  Pokud vyberete **základní**:  
   
   1. Zadejte své uživatelské jméno Ambari; Výchozí hodnota je **admin (správce**).  

   2. Zadejte své heslo Ambari.

5. Zkontrolujte zobrazení **výstupu** pro ověření.

## <a name="list-clusters"></a>Výpis clusterů

1. V řádku nabídek přejděte k **zobrazení**  >  **paleta příkazů...** a zadejte **Spark/podregistr: list cluster**.

2. Vyberte předplatné, které chcete.

3. Zkontrolujte zobrazení **výstup** . Toto zobrazení ukazuje váš propojený cluster (nebo clustery) a všechny clustery v rámci vašeho předplatného Azure:

   :::image type="content" source="./media/hdinsight-for-vscode/list-cluster-result1.png" alt-text="Nastavení výchozí konfigurace clusteru":::

## <a name="set-the-default-cluster"></a>Nastavení výchozího clusteru

1. Otevřete složku **HDexample** , která byla popsána [dříve](#open-a-work-folder), pokud je uzavřená.  

2. Vyberte soubor **HelloWorld. HQL** , který byl vytvořen [dříve](#open-a-work-folder). Otevře se v editoru skriptů.

3. Klikněte pravým tlačítkem na editor skriptů a pak vyberte **Spark/podregistr: nastavit výchozí cluster**.  

4. [Připojte](#connect-to-an-azure-account) se k účtu Azure nebo propojte cluster, pokud jste to ještě neudělali.

5. Vyberte cluster jako výchozí cluster pro aktuální soubor skriptu. Nástroje automaticky aktualizují **.VSCode\settings.js** konfiguračního souboru:

   :::image type="content" source="./media/hdinsight-for-vscode/set-default-cluster-configuration.png" alt-text="Nastavit výchozí konfiguraci clusteru":::

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

   :::image type="content" source="./media/hdinsight-for-vscode/interactive-hive-result.png" alt-text="Výsledek interaktivního dotazu Apache Hive":::

   - Panel **výsledků** : celý výsledek můžete uložit jako soubor CSV, JSON nebo Excel do místní cesty nebo stačí vybrat více řádků.

   - Panel **zprávy** : Když vyberete číslo **řádku** , přejde na první řádek běžícího skriptu.

## <a name="submit-interactive-pyspark-queries"></a>Odeslání interaktivních dotazů PySpark

Uživatelé mohou provádět PySpark interaktivně následujícími způsoby:

### <a name="using-the-pyspark-interactive-command-in-py-file"></a>Použití interaktivního příkazu PySpark v souboru PY
K odeslání dotazů pomocí interaktivního příkazu PySpark použijte následující postup:

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

4. V pravém dolním rohu okna se zobrazí výzva k instalaci jádra PySpark/synapse Pyspark. Kliknutím na tlačítko **nainstalovat** můžete pokračovat v instalaci PySpark/synapse PySpark. nebo kliknutím na tlačítko **Přeskočit** tento krok přeskočte.

   :::image type="content" source="./media/hdinsight-for-vscode/install-the-pyspark-kernel.png" alt-text="Snímek obrazovky ukazuje možnost Přeskočit instalaci PySpark.":::

5. Pokud ho potřebujete nainstalovat později, můžete přejít na   >    >  **Nastavení** předvoleb souborů a pak zrušit kontrolu **HDInsight: Povolit v nastavení Přeskočit instalaci Pyspark** . 
    
    :::image type="content" source="./media/hdinsight-for-vscode/enable-skip-pyspark-installation.png" alt-text="Snímek obrazovky s možností povolit instalaci funkce Skip Pyspark":::

6. Pokud instalace proběhla úspěšně v kroku 4, v pravém dolním rohu okna se zobrazí okno se zprávou "PySparked installeded" (úspěšně nainstalováno). Kliknutím na tlačítko **znovu** načíst okno znovu načtete.

   :::image type="content" source="./media/hdinsight-for-vscode/pyspark-kernel-installed-successfully.png" alt-text="pyspark se úspěšně nainstaloval.":::


7. V řádku nabídek přejděte k **zobrazení**  >  **paleta příkazů...** nebo použijte klávesovou zkratku **SHIFT + CTRL + P** a zadejte **Python: vyberte interpreter a spusťte Jupyter Server**.

   :::image type="content" source="./media/hdinsight-for-vscode/select-interpreter-to-start-jupyter-server.png" alt-text="Výběr interpretu pro spuštění serveru Jupyter":::

8. Vyberte níže uvedenou možnost Pythonu.

   :::image type="content" source="./media/hdinsight-for-vscode/choose-the-below-option.png" alt-text="Vyberte níže uvedenou možnost.":::
    
9. V řádku nabídek přejděte k **zobrazení**  >  **paleta příkazů...** nebo použijte klávesovou zkratku **SHIFT + CTRL + P** a zadejte **Developer: reload Window**.

    :::image type="content" source="./media/hdinsight-for-vscode/reload-window.png" alt-text="znovu načíst okno":::

10. [Připojte](#connect-to-an-azure-account) se k účtu Azure nebo propojte cluster, pokud jste to ještě neudělali.

11. Vyberte veškerý kód, klikněte pravým tlačítkem na editor skriptů a vyberte **Spark: PySpark Interactive/synapse: PySpark Interactive** k odeslání dotazu. 

    :::image type="content" source="./media/hdinsight-for-vscode/pyspark-interactive-right-click.png" alt-text="interaktivní kontextová nabídka pyspark":::

12. Pokud jste nezadali výchozí cluster, vyberte cluster. Po chvíli se **interaktivní výsledky Pythonu** zobrazí na nové kartě. Kliknutím na PySpark přepněte jádro na **PySpark/synapse PySpark** a kód se úspěšně spustí. Pokud chcete přepnout na jádro synapse Pyspark, doporučujeme zakázat automatické nastavení v Azure Portal. V opačném případě může trvat delší dobu, než se cluster probudí a nastaví se jádro synapse při prvním použití. Pokud nástroje umožňují také odeslat blok kódu namísto celého souboru skriptu pomocí místní nabídky:

    :::image type="content" source="./media/hdinsight-for-vscode/pyspark-interactive-python-interactive-window.png" alt-text="interaktivní okno pyspark interaktivního Pythonu":::

13. Zadejte **%% info** a potom stiskněte SHIFT + ENTER, abyste zobrazili informace o úloze (volitelné):

    :::image type="content" source="./media/hdinsight-for-vscode/pyspark-interactive-view-job-information.png" alt-text="informace o úloze interaktivního zobrazení pyspark":::

Nástroj také podporuje dotaz **Spark SQL** :

  :::image type="content" source="./media/hdinsight-for-vscode/pyspark-ineteractive-select-result.png" alt-text="výsledek interaktivního zobrazení pyspark":::


### <a name="perform-interactive-query-in-py-file-using-a--comment"></a>Provést interaktivní dotaz v souboru PY pomocí komentáře #%%

1. Přidejte **#%%** před kód py k získání prostředí poznámkového bloku.

   :::image type="content" source="./media/hdinsight-for-vscode/run-cell.png" alt-text="Přidat #%%":::

2. Klikněte na **buňku Run (spustit**). Po chvíli se interaktivní výsledky Pythonu zobrazí na nové kartě. Kliknutím na PySpark přepněte jádro na PySpark/synapse PySpark, potom klikněte na znovu **Spustit buňku** a kód se úspěšně spustí.

   :::image type="content" source="./media/hdinsight-for-vscode/run-cell-get-results.png" alt-text="spuštění výsledků buňky":::

## <a name="leverage-ipynb-support-from-python-extension"></a>Využití podpory IPYNB z rozšíření Pythonu

1. Jupyter Notebook můžete vytvořit pomocí příkazu z palety příkazů nebo vytvořením nového souboru. ipynb v pracovním prostoru. Další informace najdete v tématu [práce s poznámkovými bloky Jupyter v Visual Studio Code](https://code.visualstudio.com/docs/python/jupyter-support)

2. Klikněte na tlačítko **Spustit buňku** , podle pokynů **nastavte výchozí fond Spark** (důrazně doporučujeme, abyste před otevřením poznámkového bloku pokaždé nastavili výchozí cluster nebo fond) a pak **znovu načtěte** okno.

   :::image type="content" source="./media/hdinsight-for-vscode/set-the-default-spark-pool-and-reload.png" alt-text="nastavit výchozí fond Spark a znovu načíst":::

3. Kliknutím na PySpark přepnete jádro na **PySpark/synapse PySpark** a potom po chvíli klikněte na **buňka Run (spustit**). zobrazí se výsledek.

   :::image type="content" source="./media/hdinsight-for-vscode/run-ipynb-file-results.png" alt-text="spuštění výsledků ipynb":::


> [!NOTE]
>
> ["MS-python >= verze 2020.5.78807 není v tomto rozšíření podporována."](#issues-changed) bylo vyřešeno. Aktualizujte prosím **Microsoft-Python** na **nejnovější verzi** hned teď.

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

5. Klikněte pravým tlačítkem myši na editor skriptů a pak vyberte **Spark: PySpark Batch** nebo * * synapse: PySpark Batch * * *.

6. Vyberte cluster nebo fond Spark pro odeslání úlohy PySpark do:

   :::image type="content" source="./media/hdinsight-for-vscode/submit-pythonjob-result.png" alt-text="Odeslat výstup výsledku úlohy Pythonu":::

Po odeslání úlohy Pythonu se protokoly odeslání zobrazí v okně **výstup** v Visual Studio Code. Zobrazí se také adresa URL uživatelského rozhraní Spark a adresa URL uživatelského rozhraní příze. Pokud odešlete úlohu Batch do fondu Apache Spark, zobrazí se také adresa URL uživatelského rozhraní historie Sparku a adresa URL uživatelského rozhraní aplikace Spark. Pro sledování stavu úlohy můžete otevřít adresu URL ve webovém prohlížeči.

## <a name="integrate-with-hdinsight-identity-broker-hib"></a>Integrace se službou HDInsight identity broker (HIB)

### <a name="connect-to-your-hdinsight-esp-cluster-with-id-broker-hib"></a>Připojení ke clusteru HDInsight ESP pomocí zprostředkovatele ID (HIB)

Pomocí běžných kroků se přihlaste k předplatnému Azure, abyste se připojili ke clusteru HDInsight ESP pomocí zprostředkovatele ID (HIB). Po přihlášení se v Azure Exploreru zobrazí seznam clusterů. Další pokyny najdete v tématu [připojení ke clusteru HDInsight](#connect-to-an-azure-account).

### <a name="run-a-hivepyspark-job-on-an-hdinsight-esp-cluster-with-id-broker-hib"></a>Spuštění úlohy podregistru nebo PySpark v clusteru HDInsight ESP s zprostředkovatelem ID (HIB)

Pro spuštění úlohy podregistru můžete postupovat podle běžných kroků k odeslání úlohy do clusteru HDInsight ESP se zprostředkovatelem ID (HIB). Další pokyny najdete v tématu [odeslání interaktivních dotazů na podregistr a dávkových skriptů podregistru](#submit-interactive-hive-queries-and-hive-batch-scripts) .

Pokud chcete spustit interaktivní úlohu PySpark, můžete postupovat podle běžných kroků k odeslání úlohy do clusteru HDInsight ESP se zprostředkovatelem ID (HIB). Další pokyny najdete v tématu [odeslání interaktivních dotazů PySpark](#submit-interactive-pyspark-queries) .

Pokud chcete spustit dávkovou úlohu PySpark, můžete postupovat podle běžných kroků k odeslání úlohy do clusteru HDInsight ESP se zprostředkovatelem ID (HIB). Další pokyny najdete v tématu [odeslání úlohy služby Batch pro PySpark](#submit-pyspark-batch-job) .

## <a name="apache-livy-configuration"></a>Konfigurace Apache Livy

Konfigurace [Apache Livy](https://livy.incubator.apache.org/) je podporovaná. Můžete ji nakonfigurovat v souboru **.VSCode\settings.js** v souboru ve složce pracovního prostoru. V současné době konfigurace Livy podporuje pouze skript Pythonu. Další informace najdete v tématu [LIVY Readme](https://github.com/cloudera/livy/blob/master/README.rst ).

<a id="triggerlivyconf"></a>**Jak aktivovat konfiguraci Livy**

### <a name="method-1"></a>Metoda 1  

1. V řádku nabídek přejděte na   >    >  **Nastavení** předvolby souborů.
2. Do pole **Nastavení hledání** zadejte **odeslání úlohy HDInsight: Livy conf**.  
3. Pro příslušný výsledek hledání vyberte **Upravit v settings.js** .

### <a name="method-2"></a>Metoda 2

Odešlete soubor a Všimněte si, že se `.vscode` Složka automaticky přidala do pracovní složky. Konfiguraci Livy můžete zobrazit výběrem **.vscode\settings.jszapnuto**.

- Nastavení projektu:

  :::image type="content" source="./media/hdinsight-for-vscode/hdi-apache-livy-config.png" alt-text="Konfigurace HDInsight Apache Livy":::

  >[!NOTE]
  >Pro nastavení **driverMemory** a **executorMemory** nastavte hodnotu a jednotku. Například: 1G nebo 1024m.

- Podporované konfigurace Livy:

  **PŘÍSPĚVEK/Batches**
  
  **Text žádosti**

  | name | description | typ |
  | --- | --- | --- |
  |  – soubor | Soubor obsahující aplikaci, která se má provést | Cesta (povinné) |
  | proxyUser | Uživatel, který se má zosobnit při spuštění úlohy | Řetězec |
  | NázevTřídy | Application – hlavní třída Java/Spark | Řetězec |
  | args | Argumenty příkazového řádku pro aplikaci | Seznam řetězců |
  | JAR | JAR, která se má použít v této relaci | Seznam řetězců | 
  | pyFiles | Soubory Pythonu, které se mají použít v této relaci | Seznam řetězců |
  | files | Soubory, které se mají použít v této relaci | Seznam řetězců |
  | driverMemory | Velikost paměti, která se má použít pro proces ovladače | Řetězec |
  | driverCores | Počet jader, které se mají použít pro proces ovladače | Int |
  | executorMemory | Velikost paměti, která se má použít pro proces prováděcího modulu | Řetězec |
  | executorCores | Počet jader, které se mají použít pro každý prováděcí modul | Int |
  | numExecutors | Počet prováděcích modulů, které se mají spustit pro tuto relaci | Int |
  | archiv | Archivy, které se mají použít v této relaci | Seznam řetězců |
  | fronta | Název fronty PŘÍZe, která se má odeslat| Řetězec |
  | name | Název této relace | Řetězec |
  | výrobku | Vlastnosti konfigurace Sparku | Mapa klíče = Val |

  **Tělo odpovědi** Vytvořený objekt Batch.

  | name | description | typ |
  | --- | ---| --- |
  | ID | ID relace | Int |
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

   :::image type="content" source="./media/hdinsight-for-vscode/hdinsight-for-vscode-preview-hive-table.png" alt-text="Podregistr Spark & pro tabulku podregistru Visual Studio Code Preview":::

6. Otevře se okno **výsledky náhledu** :

   :::image type="content" source="./media/hdinsight-for-vscode/hdinsight-for-vscode-preview-results-window.png" alt-text="Podregistr Spark & pro Visual Studio Code náhledu okna výsledků":::

- Panel výsledků

   Celý výsledek můžete uložit jako soubor CSV, JSON nebo Excel do místní cesty nebo stačí vybrat více řádků.

- Panel zpráv

  1. Pokud je počet řádků v tabulce větší než 100, zobrazí se následující zpráva: "prvních 100 řádků se zobrazí pro tabulku podregistru."
  2. Pokud je počet řádků v tabulce menší nebo roven 100, zobrazí se následující zpráva: "60 řádky jsou zobrazeny pro tabulku podregistru."
  3. Pokud tabulka neobsahuje žádný obsah, zobrazí se následující zpráva: " `0 rows are displayed for Hive table.` "

     >[!NOTE]
     >
     >V systému Linux nainstalujte xclip a povolte tak data kopírování z tabulky.
     >
     >:::image type="content" source="./media/hdinsight-for-vscode/hdinsight-for-vscode-preview-linux-install-xclip.png" alt-text="Podregistr Spark & pro Visual Studio Code v systému Linux":::

## <a name="additional-features"></a>Další funkce

Podregistr Spark & pro Visual Studio Code také podporuje následující funkce:

- **Automatické dokončování IntelliSense**. Návrhy se zobrazují pro klíčová slova, metody, proměnné a další programovací prvky. Různé ikony reprezentují různé typy objektů:

    :::image type="content" source="./media/hdinsight-for-vscode/hdinsight-for-vscode-auto-complete-objects.png" alt-text="Nástroje pro Visual Studio Code IntelliSense pro Spark & pro objekty":::

- **Značka chyby technologie IntelliSense**. Podtržení chyb v rámci skriptu služby jazyka.     
- **Zvýrazňují syntaxe**. Služba jazyka používá různé barvy k odlišení proměnných, klíčových slov, datových typů, funkcí a dalších prvků programování:

    :::image type="content" source="./media/hdinsight-for-vscode/hdinsight-for-vscode-syntax-highlights.png" alt-text="& nástroje pro podregistr pro Spark pro Visual Studio Code zvýraznění syntaxe":::

## <a name="reader-only-role"></a>Role jen pro čtení

Uživatelé, kterým byla přiřazena role jen pro čtení pro cluster, nemohou odesílat úlohy do clusteru HDInsight ani zobrazit databázi podregistru. Obraťte se na správce clusteru a upgradujte svoji roli na [**operátor clusteru HDInsight**](./hdinsight-migrate-granular-access-cluster-configurations.md#add-the-hdinsight-cluster-operator-role-assignment-to-a-user) v [Azure Portal](https://portal.azure.com/). Pokud máte platné přihlašovací údaje Ambari, můžete cluster ručně propojit pomocí následujících pokynů.

### <a name="browse-the-hdinsight-cluster"></a>Procházet cluster HDInsight  

Když vyberete Azure HDInsight Explorer pro rozšíření clusteru HDInsight, budete vyzváni k propojení clusteru, pokud máte pro cluster roli jen pro čtení. Pomocí následujícího postupu můžete propojit s clusterem pomocí přihlašovacích údajů Ambari.

### <a name="submit-the-job-to-the-hdinsight-cluster"></a>Odešlete úlohu do clusteru HDInsight.

Při odesílání úlohy do clusteru HDInsight budete vyzváni k propojení clusteru, pokud jste v roli jenom pro čtení clusteru. Pomocí následujících kroků se připojte ke clusteru pomocí přihlašovacích údajů Ambari.

### <a name="link-to-the-cluster"></a>Odkaz na cluster

1. Zadejte platné uživatelské jméno Ambari.
2. Zadejte platné heslo.

   :::image type="content" source="./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-username.png" alt-text="Nástroje pro podregistr Spark & pro Visual Studio Code uživatelské jméno":::

   :::image type="content" source="./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-password.png" alt-text="Nástroje Spark & pro Visual Studio Code hesla":::

   > [!NOTE]
   >
   >V případě, že se `Spark / Hive: List Cluster` chcete podívat na propojený cluster, můžete použít:
   >
   >:::image type="content" source="./media/hdinsight-for-vscode/list-cluster-result1.png" alt-text="Připojené nástroje pro Visual Studio Code Reader pro Spark &":::

## <a name="azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2

### <a name="browse-a-data-lake-storage-gen2-account"></a>Procházet účet Data Lake Storage Gen2

Vyberte Průzkumníka Azure HDInsight a rozbalte účet Data Lake Storage Gen2. Pokud váš účet Azure nemá přístup k Gen2 úložišti, budete vyzváni k zadání přístupového klíče úložiště. Po ověření přístupového klíče se účet Data Lake Storage Gen2 automaticky rozšíří.

### <a name="submit-jobs-to-an-hdinsight-cluster-with-data-lake-storage-gen2"></a>Odeslání úloh do clusteru HDInsight pomocí Data Lake Storage Gen2

Odešlete úlohu do clusteru HDInsight pomocí Data Lake Storage Gen2. Pokud váš účet Azure nemá přístup pro zápis k Gen2 úložišti, budete vyzváni k zadání přístupového klíče úložiště. Po ověření přístupového klíče se úloha úspěšně odešle.

:::image type="content" source="./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-accesskey.png" alt-text="Nástroje pro podregistr Spark & pro Visual Studio Code AccessKey":::

> [!NOTE]
>
> Přístupový klíč pro účet úložiště můžete získat z Azure Portal. Další informace najdete v tématu [Správa přístupových klíčů účtu úložiště](../storage/common/storage-account-keys-manage.md).

## <a name="unlink-cluster"></a>Zrušit propojení clusteru

1. V řádku nabídek přejděte na **Zobrazit**  >  **paleta příkazů** a pak zadejte **Spark/podregistr: odpojte cluster**.  

2. Vyberte cluster, který chcete odpojit.  

3. Prohlédněte si zobrazení **výstupu** pro ověřování.  

## <a name="sign-out"></a>Odhlásit se  

V řádku nabídek přejděte na **Zobrazit**  >  **paleta příkazů** a pak zadejte **Azure: odhlásit** se.

## <a name="issues-changed"></a>Problémy změněny

Pro tento problém se v tomto rozšíření nepodporuje verze MS-Python >= 2020.5.78807. Aktualizujte prosím **Microsoft-Python** na **nejnovější verzi** hned teď.


## <a name="next-steps"></a>Další kroky

Video, které ukazuje použití podregistru Spark & pro Visual Studio Code, najdete v tématu věnovaném [registru spark & pro Visual Studio Code](https://go.microsoft.com/fwlink/?linkid=858706).