---
title: Kurz – nástroje Spark & podregistr pro VSCode (aplikace Spark)
description: Kurz – použití nástrojů pro podregistr Spark & pro VSCode k vývoji aplikací Spark, které jsou napsané v Pythonu, a jejich odeslání do fondu Apache Spark bez serveru (Preview).
services: synapse-analytics
author: hrasheed-msft
ms.author: jejiang
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: spark
ms.date: 09/03/2020
ms.openlocfilehash: 99b2b04d0f29d92b503cc0bed2460b79cfa6c354
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2020
ms.locfileid: "93315658"
---
# <a name="tutorial-create-an-apache-spark-applications-with-vscode-using-a-synapse-workspace"></a>Kurz: vytvoření Apache Sparkch aplikací pomocí VSCode pomocí pracovního prostoru synapse

Naučte se používat nástroje Apache Spark & podregistr pro Visual Studio Code. Pomocí nástrojů můžete vytvářet a odesílat Apache Hive dávkové úlohy, interaktivní dotazy podregistru a skripty PySpark pro Apache Spark. Nejdřív popíšeme, jak nainstalovat nástroje Spark & pro Visual Studio Code. Pak vás provedeme odesláním úloh do nástrojů Spark & podregistr.

Nástroje Spark & podregistr je možné nainstalovat na platformy, které Visual Studio Code podporuje. Všimněte si následujících požadavků pro různé platformy.

## <a name="prerequisites"></a>Předpoklady

K dokončení kroků v tomto článku jsou vyžadovány následující položky:

- Apache Spark fond bez serveru. Pokud chcete vytvořit fond Apache Spark bez serveru, přečtěte si téma [Vytvoření fondu Apache Spark pomocí Azure Portal](../../synapse-analytics/quickstart-create-apache-spark-pool-portal.md).
- [Visual Studio Code](https://code.visualstudio.com/).
- [Mono](https://www.mono-project.com/docs/getting-started/install/). Mono se vyžaduje jenom pro Linux a macOS.
- [PySpark interaktivní prostředí pro Visual Studio Code](../../hdinsight/set-up-pyspark-interactive-environment.md).
- Místní adresář. Tento článek používá  **C:\HD\Synaseexample**.

## <a name="install-spark--hive-tools"></a>Instalace nástrojů pro podregistr Spark &

Po splnění požadavků můžete nainstalovat nástroje Spark & pro Visual Studio Code pomocí následujících kroků:

1. Otevřete Visual Studio Code.

2. V řádku nabídek přejděte k **zobrazení**  >  **rozšíření**.

3. Do vyhledávacího pole zadejte **Spark & podregistr**.

4. Z výsledků hledání vyberte **Spark & nástroje pro podregistr** a pak vyberte **instalovat** :

     ![Podregistr Spark & pro instalaci Visual Studio Code Pythonu](./media/vscode-tool-synapse/install-hdInsight-plugin.png)

5. V případě potřeby vyberte **znovu načíst** .

## <a name="open-a-work-folder"></a>Otevření pracovní složky

Chcete-li otevřít pracovní složku a vytvořit soubor v Visual Studio Code, postupujte podle následujících kroků:

1. V řádku nabídek přejděte do **File**  >  **složky otevřít soubor...**  >  **C:\HD\Synaseexample** a pak vyberte tlačítko **Vybrat složku** . Složka se zobrazí v zobrazení **Průzkumník** na levé straně.

2. V zobrazení **Průzkumník** vyberte složku **Synaseexample** a pak vyberte ikonu **nového souboru** vedle pracovní složky:

     ![Ikona nového souboru pro Visual Studio Code](./media/vscode-tool-synapse/visual-studio-code-new-file.png)

3. Pojmenujte nový soubor pomocí `.py` přípony souboru (Spark Script). V tomto příkladu se používá **HelloWorld.py**.

## <a name="connect-to-your-spark-pools"></a>Připojení ke svým fondům Spark

Přihlaste se k předplatnému Azure a připojte se k vašim fondům Spark.

### <a name="sign-in-to-your-azure-subscription"></a>Přihlaste se ke svému předplatnému Azure

Pomocí těchto kroků se připojte k Azure:

1. V řádku nabídek přejděte k **zobrazení**  >  **paleta příkazů...** a zadejte **Azure: přihlásit** se:

     ![Nástroje pro podregistr Spark & pro přihlášení Visual Studio Code](./media/vscode-tool-synapse/hdinsight-for-vscode-extension-login.png)

2. Podle pokynů pro přihlášení Přihlaste se k Azure. Po připojení se název vašeho účtu Azure zobrazí na stavovém řádku v dolní části okna Visual Studio Code.

## <a name="set-the-default-spark-pool"></a>Nastavit výchozí fond Spark

1. Otevřete složku **Synaseexample** , která byla popsána [dříve](#open-a-work-folder), pokud je uzavřená.  

2. Vyberte soubor **HelloWorld.py** , který jste vytvořili [dříve](#open-a-work-folder). Otevře se v editoru skriptů.

3. Klikněte pravým tlačítkem na editor skriptů a pak vyberte **synapse: Nastavte výchozí fond Spark**.  

4. Pokud jste to ještě neudělali, [Připojte](#connect-to-your-spark-pools) se k účtu Azure.

5. Vyberte fond Spark jako výchozí fond Spark pro aktuální soubor skriptu. Nástroje automaticky aktualizují **.VSCode\settings.js** konfiguračního souboru:

     ![Nastavit výchozí konfiguraci clusteru](./media/vscode-tool-synapse/set-default-cluster-configuration.png)

## <a name="submit-interactive-synapse-pyspark-queries-to-spark-pool"></a>Odeslání interaktivních dotazů synapse PySpark do fondu Spark

Uživatelé mohou provádět synapse PySpark Interactive ve fondu Spark následujícími způsoby:

### <a name="using-the-synapse-pyspark-interactive-command-in-py-file"></a>Použití interaktivního příkazu synapse PySpark v souboru PY
K odeslání dotazů pomocí interaktivního příkazu PySpark použijte následující postup:

1. Otevřete složku **Synaseexample** , která byla popsána [dříve](#open-a-work-folder), pokud je uzavřená.  

2. Podle [předchozích](#open-a-work-folder) kroků vytvořte nový soubor **HelloWorld.py** .

3. Zkopírujte a vložte následující kód do souboru skriptu:

```python
import sys
from operator import add
from pyspark.sql import SparkSession, Row
 
spark = SparkSession\
 .builder\
 .appName("PythonWordCount")\
 .getOrCreate()
 
data = [Row(col1='pyspark and spark', col2=1), Row(col1='pyspark', col2=2), Row(col1='spark vs hadoop', col2=2), Row(col1='spark', col2=2), Row(col1='hadoop', col2=2)]
df = spark.createDataFrame(data)
lines = df.rdd.map(lambda r: r[0])
 
counters = lines.flatMap(lambda x: x.split(' ')) \
 .map(lambda x: (x, 1)) \
 .reduceByKey(add)
 
output = counters.collect()
sortedCollection = sorted(output, key = lambda r: r[1], reverse = True)
 
for (word, count) in sortedCollection:
 print("%s: %i" % (word, count))
```

4. V pravém dolním rohu okna se zobrazí výzva k instalaci jádra PySpark/synapse Pyspark. Kliknutím na tlačítko **nainstalovat** můžete pokračovat v instalaci PySpark/synapse PySpark. nebo kliknutím na tlačítko **Přeskočit** tento krok přeskočte.

     ![nainstalovat jádro pyspark](./media/vscode-tool-synapse/install-the-pyspark-kernel.png)

5. Pokud ho potřebujete nainstalovat později, můžete přejít na **File**  >  **Preference**  >  **Nastavení** předvoleb souborů a pak zrušit kontrolu **HDInsight: Povolit v nastavení Přeskočit instalaci Pyspark** . 
    
     ![Povolit instalaci Skip pyspark](./media/vscode-tool-synapse/enable-skip-pyspark-installation.png)

6. Pokud je instalace úspěšná v kroku 4, v pravém dolním rohu okna se zobrazí okno se zprávou "PySpark/synapse Pyspark se úspěšně nainstalovalo". Kliknutím na tlačítko **znovu** načíst okno znovu načtete.

     ![pyspark se úspěšně nainstaloval.](./media/vscode-tool-synapse/pyspark-kernel-installed-successfully.png)

7. V řádku nabídek přejděte k **zobrazení**  >  **paleta příkazů...** nebo použijte klávesovou zkratku **SHIFT + CTRL + P** a zadejte **Python: vyberte interpreter a spusťte Jupyter Server**.

     ![Výběr interpretu pro spuštění serveru Jupyter](./media/vscode-tool-synapse/select-interpreter-to-start-jupyter-server.png)

8. Vyberte níže uvedenou možnost Pythonu.

     ![Vyberte níže uvedenou možnost.](./media/vscode-tool-synapse/choose-the-below-option.png)
    
9. V řádku nabídek přejděte k **zobrazení**  >  **paleta příkazů...** nebo použijte klávesovou zkratku **SHIFT + CTRL + P** a zadejte **Developer: reload Window**.

     ![znovu načíst okno](./media/vscode-tool-synapse/reload-window.png)

10. Pokud jste to ještě neudělali, [Připojte](#connect-to-your-spark-pools) se k účtu Azure.

11. Vyberte veškerý kód, klikněte pravým tlačítkem na editor skriptů a vyberte **synapse: Pyspark Interactive** pro odeslání dotazu. 

     ![interaktivní kontextová nabídka pyspark](./media/vscode-tool-synapse/pyspark-interactive-right-click.png)

12. Vyberte fond Spark, pokud jste nezadali výchozí fond Spark. Po chvíli se **interaktivní výsledky Pythonu** zobrazí na nové kartě. Kliknutím na PySpark přepněte jádro na **synapse PySpark** , potom znovu odešlete vybraný kód a kód se úspěšně spustí. Nástroje také umožňují odeslat blok kódu namísto celého souboru skriptu pomocí místní nabídky:

     ![interaktivit](./media/vscode-tool-synapse/pyspark-interactive-python-interactive-window.png)

### <a name="perform-interactive-query-in-py-file-using-a--comment"></a>Provést interaktivní dotaz v souboru PY pomocí komentáře #%%

1. Přidejte **#%%** před kód pro získání prostředí poznámkového bloku.

     ![Přidat #%%](./media/vscode-tool-synapse/run-cell.png)

2. Klikněte na **buňku Run (spustit** ). Po chvíli se interaktivní výsledky Pythonu zobrazí na nové kartě. Kliknutím na PySpark přepněte jádro na **synapse PySpark** , potom klikněte na znovu **Spustit buňku** a kód se úspěšně spustí. 

     ![spuštění výsledků buňky](./media/vscode-tool-synapse/run-cell-get-results.png)

## <a name="leverage-ipynb-support-from-python-extension"></a>Využití podpory IPYNB z rozšíření Pythonu

1. Jupyter Notebook můžete vytvořit pomocí příkazu z palety příkazů nebo vytvořením nového souboru. ipynb v pracovním prostoru. Další informace najdete v tématu [práce s poznámkovými bloky Jupyter v Visual Studio Code](https://code.visualstudio.com/docs/python/jupyter-support)

2. Klikněte na tlačítko **Spustit buňku** , podle pokynů **nastavte výchozí fond Spark** (důrazně doporučujeme, abyste před otevřením poznámkového bloku pokaždé nastavili výchozí cluster nebo fond) a pak **znovu načtěte** okno.

     ![nastavit výchozí fond Spark a znovu načíst](./media/vscode-tool-synapse/set-the-default-spark-pool-and-reload.png)

3. Kliknutím na PySpark přepnete jádro do **synapse PySpark** a potom po chvíli klikněte na **buňka Run (spustit** ). zobrazí se výsledek.

     ![spuštění výsledků ipynb](./media/vscode-tool-synapse/run-ipynb-file-results.png)


> [!NOTE]
>
>1. MS-Python >= verze 2020.5.78807 není v tomto rozsahu podporována. Jedná se o [známý problém](#known-issues).
>  
>2. Přepněte se na jádro synapse Pyspark. doporučujeme zakázat automatické nastavení na webu Azure Portal. V opačném případě může trvat delší dobu, než se cluster probudí a nastaví se jádro synapse při prvním použití. 
>
>    ![Automatické nastavení](./media/vscode-tool-synapse/auto-settings.png)

## <a name="submit-pyspark-batch-job-to-spark-pool"></a>Odeslat dávkovou úlohu PySpark do fondu Spark

1. Otevřete složku **Synaseexample** , kterou jste si poznamenali [dříve](#open-a-work-folder), pokud je uzavřená.  

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

4. Pokud jste to ještě neudělali, [Připojte](#connect-to-your-spark-pools) se k účtu Azure.

5. Klikněte pravým tlačítkem na editor skriptů a pak vyberte **synapse: PySpark Batch**.

6. Vyberte fond Spark pro odeslání úlohy PySpark do:

     ![Odeslat výstup výsledku úlohy Pythonu](./media/vscode-tool-synapse/submit-pythonjob-result.png)

Po odeslání dávkové úlohy do fondu Spark se v okně **výstup** v Visual Studio Code zobrazí protokoly odeslání. Zobrazí se taky adresa URL **uživatelského rozhraní Spark** a adresa URL **uživatelského rozhraní aplikace pro úlohy Spark** . Pro sledování stavu úlohy můžete otevřít adresu URL ve webovém prohlížeči.

## <a name="access-and-manage-synapse-workspace"></a>Přístup a Správa pracovního prostoru synapse

V Azure Exploreru můžete provádět různé operace v prostředí Spark &ch nástrojů pro podregistr pro VSCode. Z Průzkumníka Azure.

![Image Azure](./media/vscode-tool-synapse/azure.png)

### <a name="launch-workspace"></a>Spustit pracovní prostor

1. V Průzkumníku Azure přejděte na **synapse** , rozbalte ji a zobrazte seznam předplatných synapse.

     ![Průzkumník synapse](./media/vscode-tool-synapse/synapse-explorer.png)

2. Klikněte na odběr pracovního prostoru synapse, rozbalte ho a zobrazte seznam pracovních prostorů.

3. Klikněte pravým tlačítkem myši na pracovní prostor a pak vyberte **zobrazit Apache Spark aplikace**. stránka Apache Spark aplikace na webu synapse Studio se otevře.

     ![Klikněte pravým tlačítkem na pracovní prostor.](./media/vscode-tool-synapse/right-click-on-workspace.png)

     ![aplikace synapse Studio](./media/vscode-tool-synapse/synapse-studio-application.png)

4. Rozbalte pracovní prostor, zobrazí se **výchozí úložiště** a **fondy Sparku** .

5. Klikněte pravým tlačítkem na **výchozí úložiště** , zobrazí se **Úplná cesta kopírování** a **otevřená v synapse studiu** . 

     ![Klikněte pravým tlačítkem na výchozí úložiště.](./media/vscode-tool-synapse/right-click-on-default-storage.png)

     - Klikněte na **Kopírovat úplnou cestu**. adresa URL primárního adls Gen2 účtu se zkopíruje, takže ji můžete vložit tam, kde potřebujete Marketplace.

     - V **synapse studiu** klikněte na otevřít. primární účet úložiště se otevře v synapse studiu.

     ![Výchozí úložiště v synapse studiu](./media/vscode-tool-synapse/default-storage-in-synapse-studio.png)

6. Rozbalte **výchozí úložiště** a zobrazí se primární účet úložiště.

7. Rozbalte **fondy Sparku** , zobrazí se všechny fondy Spark v pracovním prostoru.

     ![Rozbalit fond úložiště](./media/vscode-tool-synapse/expand-storage-pool.png)


## <a name="known-issues"></a>Známé problémy

### <a name="ms-python-2020578807-version-is-not-supported-on-this-extention"></a>v tomto rozsahu se nepodporuje verze 2020.5.78807 v jazyce MS-Python >=. 

"Nepovedlo se připojit k poznámkovým blokům Jupyter." je známý problém verze Pythonu >= 2020.5.78807. Pro předcházení tomuto problému doporučujeme, aby uživatelé používali **[2020.4.76186](https://github.com/microsoft/vscode-python/releases/download/2020.4.76186/ms-python-release.vsix)** verze MS-Pythonu.

![známé problémy](./media/vscode-tool-synapse/known-issue.png)


## <a name="next-steps"></a>Další kroky

- [Azure Synapse Analytics](../overview-what-is.md)
- [Vytvoření nového fondu Apache Spark pro pracovní prostor Azure synapse Analytics](../../synapse-analytics/quickstart-create-apache-spark-pool-studio.md)
