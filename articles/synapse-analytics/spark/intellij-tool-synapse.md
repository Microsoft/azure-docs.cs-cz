---
title: Výuka – Sada nástrojů Azure pro IntelliJ (aplikace Spark)
description: Kurz – Pomocí sady Nástrojů Azure pro IntelliJ vyvíjejte aplikace Spark, které jsou napsané v Scale, a odešlete je do fondu Apache Spark (preview).
services: synapse-analytics
author: v-jiche
ms.author: v-jiche
ms.reviewer: jrasnick, carlrab
ms.service: synapse-analytics
ms.topic: tutorial
ms.date: 04/15/2020
ms.openlocfilehash: 310dfec2bbd1bf6cb69f6293becead30487d6cbd
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81422652"
---
# <a name="tutorial-use-azure-toolkit-for-intellij-to-create-apache-spark-applications-for-spark-pools-preview"></a>Kurz: Pomocí sady Nástrojů Azure pro IntelliJ vytvořte aplikace Apache Spark pro fondy Spark (preview)

Tento kurz ukazuje, jak používat modul plug-in Azure Toolkit for IntelliJ k vývoji aplikací Apache Spark, které jsou napsané v [Scala](https://www.scala-lang.org/), a pak je odeslat do fondu Spark (preview) přímo z integrovaného vývojového prostředí IntelliJ (IDE). Modul plug-in můžete použít několika způsoby:

- Vyvíjejte a odesílejte aplikaci Scala Spark ve fondu Spark.
- Přístup k prostředkům fondů Spark.
- Vyvíjejte a spouštějte aplikaci Scala Spark místně.

V tomto kurzu se naučíte:
> [!div class="checklist"]
>
> - Použití modulu plug-in Azure Toolkit for IntelliJ
> - Vývoj aplikací Apache Spark
> - Odeslání žádosti do fondů Spark

## <a name="prerequisites"></a>Požadavky

- [IntelliJ IDEA Komunitní verze](https://www.jetbrains.com/idea/download/download-thanks.html?platform=windows&code=IIC).
- Plugin azure toolkit 3.27.0-2019.2 – Instalace z [úložiště pluginů IntelliJ](/java/azure/intellij/azure-toolkit-for-intellij-installation?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [JDK (verze 1.8)](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).
- Scala Plugin - Nainstalujte z [intelliJ plugin úložiště](/hdinsight/spark/apache-spark-intellij-tool-plugin#install-scala-plugin-for-intellij-idea.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).
- Tento předpoklad je určen pouze pro uživatele systému Windows.

  Při spuštění místní aplikace Spark Scala v počítači se systémem Windows se může stát výjimka, jak je vysvětleno v [SPARK-2356](https://issues.apache.org/jira/browse/SPARK-2356). K výjimce dochází, protože v systému Windows chybí program WinUtils.exe.
  Chcete-li tuto chybu vyřešit, stáhněte [spustitelný soubor WinUtils](http://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe) do umístění, například **C:\WinUtils\bin**. Potom přidejte proměnnou prostředí **HADOOP_HOME**a nastavte hodnotu proměnné na **C:\WinUtils**.

## <a name="create-a-spark-scala-application-for-a-spark-pool"></a>Vytvoření aplikace Spark Scala pro fond Spark

1. Spusťte IntelliJ IDEA a vyberte **Vytvořit nový projekt,** chcete-li otevřít okno **Nový projekt.**
2. V levém podokně vyberte **Apache Spark/HDInsight.**
3. V hlavním okně vyberte **Spark Project se vzorky (Scala).**
4. V rozevíracím seznamu **Nástroj sestavení** vyberte jeden z následujících typů:

   - **Podpora** průvodce vytvořením projektu Scala.
   - **SBT** pro správu závislostí a vytváření pro projekt Scala.

    ![Dialogové okno IntelliJ IDEA Nový projekt](./media/intellij-tool-synapse/create-synapse-application01.png)

5. Vyberte **Další**.
6. V okně **Nový projekt** zadejte následující informace:

    | Vlastnost | Popis |
    | ----- | ----- |
    |Název projektu| Zadejte název. Tento kurz používá oblast `myApp`.|
    |Umístění&nbsp;projektu| Zadejte hledané umístění pro uložení projektu.|
    |Sada SDK projektu| Při prvním použití IDEA může být prázdný. Vyberte **Nový...** a přejděte do jdk.|
    |Verze Spark|Průvodce vytvořením integruje správnou verzi pro Spark SDK a Scala SDK. Synapse podporuje pouze **Spark 2.4.0**.|

    ![Výběr sady Apache Spark SDK](./media/intellij-tool-synapse/create-synapse-application02.png)

7. Vyberte **Finish** (Dokončit). Může trvat několik minut, než bude projekt k dispozici.
8. Projekt Spark automaticky vytvoří artefakt pro vás. Chcete-li artefakt zobrazit, proveďte následující činnost:

   a. Na řádku nabídek přejděte na **Strukturu projektu souborů...** > **Project Structure...**.

   b. V okně **Struktura projektu** vyberte **artefakty**.

   c. Po zobrazení artefaktu vyberte **Zrušit.**

    ![Informace o artefaktu v dialogovém okně](./media/intellij-tool-synapse/default-artifact-dialog.png)

9. Najít **LogQuery** z **myApp** > **src** > **hlavní** > **scala ukázkový**> **sample**> **LogQuery**. Tento kurz používá **LogQuery** ke spuštění.

   ![Příkazy pro vytvoření třídy Scala z projektu](./media/intellij-tool-synapse/select-run-file.png)

## <a name="connect-to-your-spark-pools"></a>Připojení k bazénům Spark

Přihlaste se k předplatnému Azure a připojte se ke svým fondům Spark.

### <a name="sign-in-to-your-azure-subscription"></a>Přihlášení k předplatnému Azure

1. Na řádku nabídek přejděte na **Zobrazit** > **nástroj Průzkumníka Windows.** > **Azure Explorer**

   ![IntelliJ IDEA zobrazuje Průzkumníka Azure](./media/intellij-tool-synapse/show-azure-explorer1.png)

2. V Průzkumníkovi Azure klikněte pravým tlačítkem myši na uzel **Azure** a pak vyberte **Přihlásit se**.

   ![Průzkumník IntelliJ IDEA klikněte pravým tlačítkem na Azure](./media/intellij-tool-synapse/explorer-rightclick-azure.png)

3. V dialogovém okně **Přihlášení k Azure** zvolte Přihlášení k **zařízení**a pak **vyberte Přihlásit se**.

    ![Přihlášení do azure IntelliJ IDEA](./media/intellij-tool-synapse/intellij-view-explorer2.png)

4. V dialogovém okně **Přihlášení zařízení Azure** klikněte na Kopírovat&**Otevřít**.

   ![Přihlášení zařízení IntelliJ IDEA azure](./media/intellij-tool-synapse/intellij-view-explorer5.png)

5. V rozhraní prohlížeče vložte kód a klepněte na tlačítko **Další**.

   ![Microsoft zadá kód dialogové okno pro HDI](./media/intellij-tool-synapse/intellij-view-explorer6.png)

6. Zadejte svá pověření Azure a zavřete prohlížeč.

   ![Společnost Microsoft zadá dialogové okno e-mailu pro rozhraní HDI.](./media/intellij-tool-synapse/intellij-view-explorer7.png)

7. Po přihlášení zobrazí dialogové okno **Vybrat předplatná** všechna předplatná Azure, která jsou přidružená k přihlašovacím údajům. Vyberte předplatné a klepněte na tlačítko **Vybrat**.

    ![Dialogové okno Select Subscriptions (Výběr předplatných)](./media/intellij-tool-synapse/Select-Subscriptions.png)

8. Z **Průzkumníka Azure**rozbalte **Apache Spark na Synapse** a zobrazte pracovní prostory, které jsou ve vašich předplatných.

    ![Hlavní zobrazení aplikace IntelliJ IDEA Azure Explorer](./media/intellij-tool-synapse/azure-explorer-workspace.png)

9. Chcete-li zobrazit fondy Spark, můžete dále rozšířit pracovní prostor.

    ![Účty úložiště Azure Exploreru](./media/intellij-tool-synapse/azure-explorer-pool.png)

## <a name="remote-run-a-spark-scala-application-on-a-spark-pool"></a>Vzdálené spuštění aplikace Spark Scala ve fondu Spark

Po vytvoření aplikace Scala ji můžete vzdáleně spustit.

1. Kliknutím na ikonu otevřete okno **Spustit/ladit konfigurace.**

    ![Příkaz Odeslat aplikaci Spark do HDInsight](./media/intellij-tool-synapse/open-configuration-window.png)

2. V dialogovém okně **Spustit/ladit** konfigurace **+** klepněte na tlačítko a vyberte **možnost Apache Spark v synapse**.

    ![Příkaz Odeslat aplikaci Spark do HDInsight](./media/intellij-tool-synapse/create-synapse-configuration02.png)

3. V okně **Konfigurace spuštění/ladění** zadejte následující hodnoty a pak vyberte **OK**:

    |Vlastnost |Hodnota |
    |----|----|
    |Zapalovací bazény|Vyberte fondy Spark, na kterých chcete spustit aplikaci.|
    |Vyberte artefakt, který chcete odeslat.|Ponechte výchozí nastavení.|
    |Název hlavní třídy|Výchozí hodnota je hlavní třída z vybraného souboru. Třídu můžete změnit výběrem tři tečky(**...**) a výběrem jiné třídy.|
    |Konfigurace úloh|Můžete změnit výchozí klíč a hodnoty. Další informace naleznete v [tématu Apache Livy REST API](https://livy.incubator.apache.org./docs/latest/rest-api.html).|
    |Argumenty příkazového řádku|V případě potřeby můžete zadat argumenty oddělené mezerou pro hlavní třídu.|
    |Odkazované sklenice a odkazované soubory|Můžete zadat cesty pro odkazované jars a soubory, pokud existuje. Můžete také procházet soubory ve virtuálním systému souborů Azure, který v současné době podporuje pouze cluster ADLS Gen 2. Další informace: [Konfigurace Apache Spark](https://spark.apache.org/docs/latest/configuration.html#runtime-environment) a Jak [nahrát prostředky do clusteru](../../storage/blobs/storage-quickstart-blobs-storage-explorer.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).|
    |Úložiště pro nahrávání úloh|Rozbalte a odhalte další možnosti.|
    |Typ úložiště|**Vrozenou možnost Použít objekt blob Azure k nahrání** z rozevíracího seznamu.|
    |Účet úložiště|Zadejte svůj účet úložiště.|
    |Klíč úložiště|Zadejte klíč úložiště.|
    |Kontejner úložiště|Po zadání **účtu úložiště** a **klíče úložiště** vyberte kontejner úložiště z rozevíracího seznamu.|

    ![Dialogové okno Odeslání jiskry](./media/intellij-tool-synapse/create-synapse-configuration03.png)

4. Kliknutím na ikonu **SparkJobRun** odešlete projekt do vybraného fondu Spark. Karta **Vzdálená úloha Spark v clusteru** zobrazuje průběh provádění úloh y v dolní části. Aplikaci můžete zastavit kliknutím na červené tlačítko.

    ![Okno odeslání Apache Spark](./media/intellij-tool-synapse/remotely-run-synapse.png)

    ![Dialogové okno Odeslání jiskry](./media/intellij-tool-synapse/remotely-run-result.png)

## <a name="local-rundebug-apache-spark-applications"></a>Místní run/ladění aplikací Apache Spark

Podle následujících pokynů můžete nastavit místní spuštění a místní ladění pro úlohu Apache Spark.

### <a name="scenario-1-do-local-run"></a>Scénář 1: Místní spuštění

1. Otevřete dialogové okno **Spustit/ladit konfigurace,** vyberte znaménko plus (**+**). Pak vyberte **možnost Apache Spark na Synapse.** Zadejte informace pro **název**, **Hlavní název třídy,** který chcete uložit.

    ![Intellij Spustit konfigurace ladění místní spuštění](./media/intellij-tool-synapse/local-run-synapse.png)

    - Proměnné prostředí a umístění WinUtils.exe jsou určeny pouze pro uživatele systému Windows.
    - Proměnné prostředí: Proměnnou prostředí systému lze automaticky zjistit, pokud jste ji již dříve nastavili a není třeba ji ručně přidávat.
    - [Umístění winutils.exe](http://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe): Umístění winutils můžete zadat kliknutím na ikonu složky vpravo.

2. Poté klikněte na tlačítko místní přehrávání.

    ![Intellij Spustit konfigurace ladění místní spuštění](./media/intellij-tool-synapse/local-run-synapse01.png)

3. Po dokončení místního spuštění, pokud skript obsahuje výstup, můžete zkontrolovat výstupní soubor z **výchozího datového** > **__souboru__**.

    ![Výsledek místního spuštění projektu Intellij](./media/intellij-tool-synapse/spark-local-run-result.png)

### <a name="scenario-2-do-local-debugging"></a>Scénář 2: Místní ladění

1. Otevřete skript **LogQuery,** nastavte zarážky.
2. Chcete-li provést místní ladění, klepněte na ikonu **místního ladění.**

    ![Výsledek místního spuštění projektu Intellij](./media/intellij-tool-synapse/local-debug-synapse.png)

## <a name="access-and-manage-synapse-workspace"></a>Přístup a správa synapse workspace

V Azure Exploreru můžete provádět různé operace v rámci Azure Toolkit pro IntelliJ. Na řádku nabídek přejděte na **Zobrazit** > **nástroj Průzkumníka Windows.** > **Azure Explorer**

### <a name="launch-workspace"></a>Spuštění pracovního prostoru

1. Z Průzkumníka Azure přejděte **na Apache Spark v Synapse**a rozbalte ho.

    ![Hlavní zobrazení aplikace IntelliJ IDEA Azure Explorer](./media/intellij-tool-synapse/azure-explorer-workspace.png)

2. Klikněte pravým tlačítkem myši na pracovní prostor a pak vyberte **Spustit pracovní prostor**, otevře se web.

    ![Podrobnosti aplikace Zobrazení úlohy Spark](./media/intellij-tool-synapse/launch-workspace-synapse.png)

    ![Podrobnosti aplikace Zobrazení úlohy Spark](./media/intellij-tool-synapse/launch-workspace-result.png)

## <a name="spark-console"></a>Jiskrová konzola

Můžete spustit Spark Local Console (Scala) nebo Spark Livy Interactive Session Console (Scala).

### <a name="spark-local-console-scala"></a>Jiskra místní konzole (Scala)

Ujistěte se, že jste winutils splnili. EXE předpoklad.

1. Na řádku nabídek přejděte na **Spusťte** > **upravit konfigurace...**.
2. Z okna **Spustit/ladit konfigurace,** v levém podokně, přejděte na **Apache Spark na Synapse** > **[Spark na Synapse] myApp**.
3. V hlavním okně vyberte kartu **Místní spuštění.**
4. Zadejte následující hodnoty a pak vyberte **OK**:

    |Vlastnost |Hodnota |
    |----|----|
    |Proměnné prostředí|Ujistěte se, že hodnota HADOOP_HOME je správná.|
    |Umístění winutils.exe|Ujistěte se, že cesta je správná.|

    ![Konfigurace místní konzoly](./media/intellij-tool-synapse/local-console-synapse01.png)

5. Z projektu přejděte na **myApp** > **src** > **hlavní** > **scala** > **myApp**.
6. Na řádku nabídek přejděte na**konzolu** >  **Tools** > Spark**Spusťte místní konzolu Spark (Scala).**
7. Pak se mohou zobrazit dvě dialogová okna s dotazem, zda chcete automaticky opravit závislosti. Pokud ano, vyberte **možnost Automatická oprava**.

    ![Dialogové okno IntelliJ IDEA Spark Auto Fix1](./media/intellij-tool-synapse/intellij-console-autofix1.png)

    ![Dialogové okno IntelliJ IDEA Spark Auto Fix2](./media/intellij-tool-synapse/intellij-console-autofix2.png)

8. Konzole by měla vypadat podobně jako na obrázku níže. V okně konzoly zadejte `sc.appName`a stiskněte kombinaci kláves CTRL+Enter. Výsledek se zobrazí. Místní konzoli můžete zastavit kliknutím na červené tlačítko.

    ![Výsledek místní konzoly IntelliJ IDEA](./media/intellij-tool-synapse/local-console-result.png)

### <a name="spark-livy-interactive-session-console-scala"></a>Interaktivní konzola pro interaktivní relace Spark Livy (Scala)

Je podporována pouze na IntelliJ 2018.2 a 2018.3.

1. Na řádku nabídek přejděte na **Spusťte** > **upravit konfigurace...**.

2. Z okna **Spustit/ladit konfigurace,** v levém podokně, přejděte na **Apache Spark na synapse** > **[Spark na synapse] myApp**.

3. V hlavním okně vyberte kartu **Vzdálené spuštění v clusteru.**

4. Zadejte následující hodnoty a pak vyberte **OK**:

    |Vlastnost |Hodnota |
    |----|----|
    |Zapalovací bazény|Vyberte fondy Spark, na kterých chcete spustit aplikaci.|
    ||

    ![Konfigurace sady interaktivních konzol](./media/intellij-tool-synapse/interactive-console-configuration.png)

5. Z projektu přejděte na **myApp** > **src** > **hlavní** > **scala** > **myApp**.

6. Na řádku nabídek přejděte na**konzolu** >  **Tools** > Spark**Console Run Spark Livy Interactive Session Console (Scala).**
7. Konzole by měla vypadat podobně jako na obrázku níže. V okně konzoly zadejte `sc.appName`a stiskněte kombinaci kláves CTRL+Enter. Výsledek se zobrazí. Místní konzoli můžete zastavit kliknutím na červené tlačítko.

    ![Výsledek interaktivní konzoly IntelliJ IDEA](./media/intellij-tool-synapse/interactive-console-result.png)

### <a name="send-selection-to-spark-console"></a>Odeslání výběru do konzoly Spark

Je vhodné předvídat výsledek skriptu odesláním nějakého kódu do místní konzole nebo Livy Interactive Session Console (Scala). Můžete zvýraznit nějaký kód v souboru Scala a pak kliknout pravým tlačítkem na **Odeslat výběr do konzole Spark**. Vybraný kód bude odeslán do konzole a bude hotovo. Výsledek se zobrazí za kódem v konzole. Konzola zkontroluje chyby, pokud existují.

   ![Odeslat výběr do konzole Spark](./media/intellij-tool-synapse/send-selection-to-console.png)

## <a name="next-steps"></a>Další kroky

- [Azure Synapse Analytics](../overview-what-is.md)
- [Vytvoření nového fondu Apache Spark pro pracovní prostor Azure Synapse Analytics](../../synapse-analytics/quickstart-create-apache-spark-pool.md)
