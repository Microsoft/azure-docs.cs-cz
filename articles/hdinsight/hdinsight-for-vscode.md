---
title: Nástroje Azure HDInsight - použití Visual Studio Code Hive, LLAP nebo pySpark | Microsoft Docs
description: Další informace o použití nástroje Azure HDInsight pro Visual Studio Code pro vytvoření a odesílání dotazů a skriptů.
Keywords: VS Code,Azure HDInsight Tools,Hive,Python,PySpark,Spark,HDInsight,Hadoop,LLAP,Interactive Hive,Interactive Query
services: HDInsight
documentationcenter: ''
author: jejiang
manager: ''
editor: jgao
tags: azure-portal
ms.assetid: ''
ms.service: HDInsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/27/2017
ms.author: jejiang
ms.openlocfilehash: 8c976e5508c928943e2a5e4820f72520554f9b5d
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2018
---
# <a name="use-azure-hdinsight-tools-for-visual-studio-code"></a>Pomocí nástroje Azure HDInsight pro Visual Studio Code

Další informace o použití nástroje Azure HDInsight pro Visual Studio Code (kód VS) k vytvoření a odeslání úlohy Hive batch, interaktivních dotazů Hive a skripty, pySpark. Nástroje Azure HDInsight lze nainstalovat na platformách, které jsou podporovány VS Code. Mezi ně patří Windows, Linux a systému macOS. Můžete najít požadavky pro různé platformy.


## <a name="prerequisites"></a>Požadavky

Následující položky jsou požadovány pro dokončení kroků v tomto článku:

- HDInsight cluster.  Vytvoření clusteru s podporou naleznete v tématu [Začínáme s HDInsight]( hdinsight-hadoop-linux-tutorial-get-started.md).
- [Visual Studio Code](https://www.visualstudio.com/products/code-vs.aspx).
- [Mono](http://www.mono-project.com/docs/getting-started/install/). Mono je pouze požadované pro systémy Linux a systému macOS.

## <a name="install-the-hdinsight-tools"></a>Instalace nástrojů HDInsight
   
Po dokončení instalace požadované součásti, můžete nainstalovat nástroje Azure HDInsight pro VS Code. 

**K nástrojům nainstalovat Azure HDInsight**

1. Otevřete Visual Studio Code.

2. V levém podokně vyberte **rozšíření**. Do vyhledávacího pole zadejte **HDInsight**.

3. Vedle **nástroje Azure HDInsight**, vyberte **nainstalovat**. Za několik sekund **nainstalovat** tlačítko se změní na **opětovného načtení**.

4. Vyberte **opětovného načtení** aktivovat **nástroje Azure HDInsight** rozšíření.

5. Vyberte **opětovného načtení okno** k potvrzení. Můžete zobrazit **nástroje Azure HDInsight** v **rozšíření** podokně.

   ![HDInsight pro Visual Studio kód Python instalace](./media/hdinsight-for-vscode/install-hdInsight-plugin.png)

## <a name="open-hdinsight-workspace"></a>Otevřete pracovní prostor pro HDInsight

Vytvořte pracovní prostor v produktu VS Code předtím, než se můžete připojit k Azure.

**Otevřete pracovní prostor**

1. Na **soubor** nabídce vyberte možnost **otevřít složku**. Potom jako pracovní složky určit existující složku nebo vytvořit nový. Složka se zobrazí v levém podokně.

2. V levém podokně, vyberte **nový soubor** Ikona vedle pracovní složky.

   ![Nový soubor](./media/hdinsight-for-vscode/new-file.png)

3. Pojmenujte nový soubor s .hql (dotazů Hive) nebo přípona souboru .py (Spark skript). Všimněte si, že **XXXX_hdi_settings.json** konfigurační soubor je automaticky přidán do pracovní složky.

4. Otevřete **XXXX_hdi_settings.json** z **EXPLORER**, nebo klikněte pravým tlačítkem na editor skriptů a vyberte **nastavení konfigurace**. Můžete nakonfigurovat položku přihlášení, výchozího clusteru a parametry úlohy odeslání, jak je znázorněno v ukázce v souboru. Můžete také ponechat ostatní parametry prázdné.

## <a name="connect-to-hdinsight-cluster"></a>Připojení ke clusteru HDInsight

Než skripty můžete odeslat ke clusterům HDInsight z VS Code, musíte buď připojit ke svému účtu Azure, nebo odkaz clusteru (pomocí nástroje Ambari uživatelského jména a hesla nebo doméně připojené k účtu).

**Pro připojení k Azure**

1. Pokud ještě nemáte je, vytvořte nové pracovní složky a nový soubor skriptu.

2. Klikněte pravým tlačítkem na editor skriptů a potom v místní nabídce vyberte **HDInsight: přihlášení**. Můžete také zadat **Ctrl + Shift + P**a potom zadejte **HDInsight: přihlášení**.

    ![Nástroje HDInsight pro Visual Studio Code přihlášení](./media/hdinsight-for-vscode/hdinsight-for-vscode-extension-login.png)

3. Pokud chcete přihlásit, postupujte podle pokynů přihlášení v **výstup** podokně.

    **Azure:** ![nástroje HDInsight pro Visual Studio Code přihlašovací informace](./media/hdinsight-for-vscode/hdinsight-for-vscode-extension-Azurelogin-info.png)

    Poté, co jste připojení, název účtu Azure se zobrazí na stavovém řádku v levém dolním okna VS Code. 

    > [!NOTE]
    > Z důvodu problému známé ověřování Azure budete muset otevřít prohlížeč v privátním režimu nebo incognito. Pokud váš účet Azure má dva faktory povoleno, doporučujeme pomocí ověřování phone místo ověření PIN kódu.
  

4. Klikněte pravým tlačítkem na editor skriptů otevřete v místní nabídce. V místní nabídce můžete provádět následující úlohy:

    - Odhlásit se
    - Seznam clustery
    - Sada výchozí clustery
    - Odeslání interaktivních dotazů Hive
    - Odeslání dávky skripty Hive
    - Odeslání interaktivních dotazů PySpark
    - Odeslání PySpark dávkové skripty
    - Konfigurace sady

**Propojení clusteru**

Můžete propojit normální clusteru pomocí Ambari spravované uživatelské jméno, také propojení clusteru hadoop zabezpečení pomocí uživatelského jména domény (například: user1@contoso.com).
1. Otevřete paletu příkaz výběrem **CTRL + SHIFT + P**a potom zadejte **HDInsight: propojení cluster**.

   ![příkaz clusteru odkaz](./media/hdinsight-for-vscode/link-cluster-command.png)

2. Zadejte HDInsight adresu URL clusteru služby -> vstup-uživatelské jméno > zadejte heslo -> vyberte typ clusteru -> ho ukazuje úspěch informace pokud předány ověření.
   
   ![Dialogové okno odkaz clusteru](./media/hdinsight-for-vscode/link-cluster-process.png)

   > [!NOTE]
   > Pokud cluster jak zaznamenána v rámci předplatného Azure a propojené cluster používáme propojené uživatelské jméno a heslo. 
   
3. Cluster s podporou propojené můžete zobrazit pomocí příkazu **seznamu clusteru**. Teď můžete odeslat skript do tohoto propojené clusteru.

   ![propojené clusteru](./media/hdinsight-for-vscode/linked-cluster.png)

4. Také můžete zrušit propojení cluster pomocí vložení **HDInsight: zrušení propojení cluster** z palety příkaz.

## <a name="list-hdinsight-clusters"></a>Clustery HDInsight seznamu

Chcete-li otestovat připojení, můžete vytvořit seznam clusterům HDInsight:

**Seznam clusterů HDInsight v rámci vašeho předplatného Azure**
1. Otevřete pracovní prostor a připojte se k Azure. Další informace najdete v tématu [prostoru otevřete HDInsight](#open-hdinsight-workspace) a [připojit k Azure](#connect-to-azure).

2. Klikněte pravým tlačítkem na editor skriptů a potom vyberte **HDInsight: Cluster seznamu** v místní nabídce. 

3. Clustery Hive a Spark se zobrazí v **výstup** podokně.

    ![Nastavit výchozí konfiguraci clusteru](./media/hdinsight-for-vscode/list-cluster-result.png)

## <a name="set-a-default-cluster"></a>Nastavení výchozího clusteru
1. Otevřete pracovní prostor a připojit se k Azure. V tématu [prostoru otevřete HDInsight](#open-hdinsight-workspace) a [připojit k Azure](#connect-to-azure).

2. Klikněte pravým tlačítkem na editor skriptů a potom vyberte **HDInsight: nastavení clusteru výchozí**. 

3. Vyberte cluster jako výchozího clusteru pro aktuální soubor skriptu. Automaticky aktualizovat konfigurační soubor nástroje **XXXX_hdi_settings.json**. 

   ![Výchozí konfigurace clusteru sady](./media/hdinsight-for-vscode/set-default-cluster-configuration.png)

## <a name="set-the-azure-environment"></a>Nastavení prostředí Azure 
1. Otevřete paletu příkaz výběrem **CTRL + SHIFT + P**.

2. Zadejte **HDInsight: nastavte prostředí Azure**.

3. Vyberte jeden ze způsobů z Azure a AzureChina jako váš výchozí položku přihlášení.

4. Mezitím, má nástroj již uložené vaše výchozí přihlášení položku v **XXXX_hdi_settings.json**. Aktualizovány také přímo na tento konfigurační soubor. 

   ![výchozí přihlášení položku konfigurace sady](./media/hdinsight-for-vscode/set-default-login-entry-configuration.png)

## <a name="submit-interactive-hive-queries"></a>Odeslání interaktivních dotazů Hive

Pomocí nástroje HDInsight pro VS Code můžete odeslat interaktivních dotazů Hive ke clusterům HDInsight interaktivní dotazu.

1. Pokud ještě nemáte je, vytvořte nové pracovní složky a nový soubor skriptu Hive.

2. Připojit k účtu Azure a pak nakonfigurujte výchozího clusteru, pokud jste tak již neučinili.

3. Zkopírujte a vložte následující kód do souboru Hive a pak ho uložte.

    ```hiveql
    SELECT * FROM hivesampletable;
    ```
3. Klikněte pravým tlačítkem na editor skriptů a potom vyberte **HDInsight: Hive interaktivní** odeslat dotaz. Nástroje lze také odeslat blok kódu místo souboru celou skriptu pomocí místní nabídky. Jakmile, na nové kartě se zobrazují výsledky dotazu.

   ![interaktivní výsledek Hive](./media/hdinsight-for-vscode/interactive-hive-result.png)

    - **VÝSLEDKY** panely: celý výsledek uložit jako soubor CSV, JSON nebo Excel místní cestu nebo stačí vybrat více řádků.

    - **ZPRÁVY** panely: Když vyberete **řádku** číslo, přejde na první řádek spouštění skriptu.

Spuštění interaktivního dotazu trvá mnohem kratší dobu, než [dávková úloha Hive](#submit-hive-batch-scripts).

## <a name="submit-hive-batch-scripts"></a>Odeslání dávky skripty Hive

1. Pokud ještě nemáte je, vytvořte nové pracovní složky a nový soubor skriptu Hive.

2. Připojit k účtu Azure a pak nakonfigurujte výchozího clusteru, pokud jste tak již neučinili.

3. Zkopírujte a vložte následující kód do souboru Hive a pak ho uložte.

    ```hiveql
    SELECT * FROM hivesampletable;
    ```
3. Klikněte pravým tlačítkem na editor skriptů a potom vyberte **HDInsight: Hive Batch** se odeslat úlohu Hive. 

4. Vyberte cluster, do kterého chcete odeslat.  

    Po odeslání úlohy Hive údaje úspěšné odeslání a jobid se zobrazí v **výstup** panelu. Úlohy Hive se rovněž otevře **webový PROHLÍŽEČ**, který zobrazuje protokoly v reálném čase úlohy a stav.

   ![odeslat výsledek úlohy Hive](./media/hdinsight-for-vscode/submit-Hivejob-result.png)

[Odesílání interaktivních dotazů Hive](#submit-interactive-hive-queries) trvá mnohem kratší dobu, než odesílání dávkovou úlohu.

## <a name="submit-interactive-pyspark-queries"></a>Odeslání interaktivních dotazů PySpark
Nástroje HDInsight pro VS Code můžete také odeslat interaktivních dotazů PySpark ke clusterům Spark.
1. Vytvořte nový pracovní složky a nový soubor skriptu s příponou .py, pokud ještě nemáte je.

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
4. Zvýrazněte tyto skripty. Klikněte pravým tlačítkem editor skriptů a vyberte **HDInsight: PySpark interaktivní**.

5. Pokud jste ještě nenainstalovali **Python** rozšíření v produktu VS Code, vyberte **nainstalovat** tlačítko jak je znázorněno na následujícím obrázku:

    ![HDInsight pro Visual Studio kód Python instalace](./media/hdinsight-for-vscode/hdinsight-vscode-install-python.png)

6. Pokud jste to ještě neudělali, nainstalujte prostředí Python ve vašem systému. 
   - Pro systém Windows, stáhněte a nainstalujte [Python](https://www.python.org/downloads/). Zkontrolujte, zda je `Python` a `pip` jsou v systému CESTU.

   - Pokyny pro systému macOS a Linux najdete v tématu [nastavení PySpark interaktivní prostředí pro Visual Studio Code](set-up-pyspark-interactive-environment.md).

7. Vyberte cluster, do kterého se mají odeslat dotaz PySpark. Po, se brzy výsledek dotazu zobrazí na kartě nové správné:

   ![Odeslání úlohy výsledek Python](./media/hdinsight-for-vscode/pyspark-interactive-result.png) 
8. Tento nástroj podporuje také **SQL klauzule** dotazu.

   ![Odeslání úlohy Python výsledek](./media/hdinsight-for-vscode/pyspark-ineteractive-select-result.png) odeslání stav se zobrazí nalevo od dolní stav panelu když používáte dotazy. Nemáte odesílat jiné dotazy, pokud je stav **jádra PySpark (zaneprázdněn)**. 

>[!NOTE]
>Clustery udržovat relaci informace. Definované proměnné, funkce a odpovídající hodnoty jsou uchovávány v této relaci, tak může být odkazováno napříč více volání služby pro stejného clusteru. 
 

## <a name="submit-pyspark-batch-job"></a>Odeslání PySpark dávkovou úlohu

1. Vytvořte nový pracovní složky a nový soubor skriptu s příponou .py, pokud ještě nemáte je.

2. Pokud jste tak již neučinili, připojte k účtu Azure.

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
4. Klikněte pravým tlačítkem na editor skriptů a potom vyberte **HDInsight: PySpark Batch**. 

5. Vyberte cluster, do kterého se mají odeslat úlohu PySpark. 

   ![Odeslání úlohy výsledek Python](./media/hdinsight-for-vscode/submit-pythonjob-result.png) 

Po odeslání úlohy Python odeslání protokolů se objeví v **výstup** okno v produktu VS Code. **Adresa URL uživatelského rozhraní Spark** a **adresa URL uživatelského rozhraní Yarn** jsou také uvedeny. Adresu URL můžete otevřít ve webovém prohlížeči a sledovat stav úlohy.


   


## <a name="additional-features"></a>Další funkce

HDInsight pro VS Code podporuje následující funkce:

- **Automatické dokončování IntelliSense**. Návrhy překryvné pro – klíčové slovo, metody, proměnné a tak dále. Různé ikony představují různé typy objektů.

    ![Nástroje HDInsight pro Visual Studio kódu IntelliSense typy objektů](./media/hdinsight-for-vscode/hdinsight-for-vscode-auto-complete-objects.png)
- **Chyba značky IntelliSense**. Služba jazyka podtrhne úpravy chyby skriptu Hive.     
- **Syntaxe označuje**. Služba jazyka pomocí různých barev odlišit od proměnné, klíčová slova, datový typ, funkce a tak dále. 

    ![Nástroje HDInsight pro Visual Studio Code syntaxe označuje](./media/hdinsight-for-vscode/hdinsight-for-vscode-syntax-highlights.png)

## <a name="next-steps"></a>Další postup

### <a name="demo"></a>Ukázka
* HDInsight pro VS Code: [Video](https://go.microsoft.com/fwlink/?linkid=858706)

### <a name="tools-and-extensions"></a>Nástroje a rozšíření

* [Použití nástrojů Azure pro IntelliJ k ladění aplikací Spark vzdáleně prostřednictvím sítě VPN](spark/apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Použití nástrojů Azure pro IntelliJ k ladění aplikací Spark vzdáleně přes SSH](spark/apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Použití nástroje HDInsight pro IntelliJ s Hortonworks karanténě](hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [Používat nástroje HDInsight pro vytvoření aplikací Spark v Azure nástrojů pro Eclipse](spark/apache-spark-eclipse-tool-plugin.md)
* [Použití poznámkových bloků Zeppelin s clusterem Sparku v HDInsight](spark/apache-spark-zeppelin-notebook.md)
* [Jádra dostupná pro poznámkový blok Jupyter v clusteru Sparku pro HDInsight](spark/apache-spark-jupyter-notebook-kernels.md)
* [Použití externích balíčků s poznámkovými bloky Jupyter](spark/apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalace Jupyteru do počítače a připojení ke clusteru HDInsight Spark](spark/apache-spark-jupyter-notebook-install-locally.md)
* [Vizualizace dat Hive pomocí Microsoft Power BI ve službě Azure HDInsight](hadoop/apache-hadoop-connect-hive-power-bi.md)
* [Vizualizace interaktivní dotazu Hive pomocí Power BI v Azure HDInsight](./interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md).
* [Nastavení PySpark interaktivní prostředí pro Visual Studio Code](set-up-pyspark-interactive-environment.md)
* [Použití Zeppelin ke spouštění dotazů Hive v Azure HDInsight ](./hdinsight-connect-hive-zeppelin.md)

### <a name="scenarios"></a>Scénáře
* [Spark s BI: Provádějte interaktivní analýzy dat pomocí Sparku v HDInsight pomocí nástrojů BI](spark/apache-spark-use-bi-tools.md)
* [Spark s Machine Learning: Používejte Spark v HDInsight pro analýzu teploty v budově pomocí dat HVAC](spark/apache-spark-ipython-notebook-machine-learning.md)
* [Spark s Machine Learning: Používejte Spark v HDInsight k předpovědím výsledků kontrol potravin](spark/apache-spark-machine-learning-mllib-ipython.md)
* [Analýza protokolu webu pomocí Sparku v HDInsight](spark/apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-running-applications"></a>Vytvoření a spuštění aplikace
* [Vytvoření samostatné aplikace pomocí Scala](spark/apache-spark-create-standalone-application.md)
* [Vzdálené spouštění úloh na clusteru Sparku pomocí Livy](spark/apache-spark-livy-rest-interface.md)

### <a name="manage-resources"></a>Správa prostředků
* [Správa prostředků v clusteru Apache Spark v Azure HDInsight](spark/apache-spark-resource-manager.md)
* [Sledování a ladění úloh spuštěných v clusteru Apache Spark v HDInsight](spark/apache-spark-job-debugging.md)



