---
title: Kurz – Azure Toolkit for IntelliJ (aplikace Spark)
description: Kurz – použití Azure Toolkit for IntelliJ k vývoji aplikací Spark, které jsou napsané v Scala a jejich odeslání do fondu Apache Spark (Preview).
services: synapse-analytics
author: hrasheed-msft
ms.author: jejiang
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: spark
ms.date: 04/15/2020
ms.openlocfilehash: c17addc313954fbca5b81c4594d7317065350c09
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91249565"
---
# <a name="tutorial-create-an-apache-spark-application-with-intellij-using-a-synapse-workspace"></a>Kurz: Vytvoření aplikace Apache Spark s použitím IntelliJ pomocí pracovního prostoru synapse

V tomto kurzu se dozvíte, jak používat modul plug-in Azure Toolkit for IntelliJ k vývoji Apache Spark aplikací, které jsou napsané v [Scala](https://www.scala-lang.org/), a pak je odeslat do fondu Spark (Preview) přímo z integrovaného vývojového prostředí (IDE) IntelliJ. Modul plug-in můžete použít několika způsoby:

- Vývoj a odesílání aplikace Scala Spark ve fondu Spark.
- Přístup k prostředkům fondů Spark
- Vývoj a spouštění aplikace Scala Spark lokálně

V tomto kurzu se naučíte:
> [!div class="checklist"]
>
> - Použití modulu plug-in Azure Toolkit for IntelliJ
> - Vývoj aplikací Apache Spark
> - Odeslat aplikaci do fondů Spark

## <a name="prerequisites"></a>Požadavky

- [Komunita INTELLIJ nápadu](https://www.jetbrains.com/idea/download/download-thanks.html?platform=windows&code=IIC)
- Modul plug-in Azure Toolkit 3.27.0-2019.2 – instalace z [úložiště modulu plug-in IntelliJ](/java/azure/intellij/azure-toolkit-for-intellij-installation?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [JDK (verze 1,8)](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).
- Modul plug-in Scala – nainstalujte z [úložiště modulu plug-in IntelliJ](/azure/hdinsight/spark/apache-spark-intellij-tool-plugin#install-scala-plugin-for-intellij-idea).
- Tato požadovaná součást je určena pouze pro uživatele systému Windows:

  Když spouštíte místní aplikaci Spark Scala na počítači se systémem Windows, může se zobrazit výjimka, jak je vysvětleno v [Spark-2356](https://issues.apache.org/jira/browse/SPARK-2356). K výjimce dochází, protože ve Windows chybí WinUtils.exe.
  Chcete-li tuto chybu vyřešit, stáhněte [spustitelný soubor WinUtils](http://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe) do umístění, jako je například **C:\WinUtils\bin**. Pak přidejte proměnnou prostředí **HADOOP_HOME**a nastavte hodnotu proměnné na **C:\WinUtils**.

## <a name="create-a-spark-scala-application-for-a-spark-pool"></a>Vytvoření aplikace Spark Scala pro fond Spark

1. Spusťte IntelliJ nápad a výběrem **vytvořit nový projekt** otevřete okno **Nový projekt** .
2. V levém podokně vyberte **Apache Spark/HDInsight** .
3. V hlavním okně vyberte **projekt Spark s ukázkami (Scala)** .
4. V rozevíracím seznamu **Nástroj sestavení** vyberte jeden z následujících typů:

   - **Maven** for Scala – Průvodce vytvořením projektu – podpora.
   - **SBT** pro správu závislostí a sestavení projektu Scala.

    ![IntelliJ nápad – nový projekt – dialogové okno](./media/intellij-tool-synapse/create-synapse-application01.png)

5. Vyberte **Další**.
6. V okně **Nový projekt** zadejte následující informace:

    | Vlastnost | Popis |
    | ----- | ----- |
    |Název projektu| Zadejte název. Tento kurz používá oblast `myApp`.|
    |&nbsp;Umístění projektu| Zadejte požadované umístění pro uložení projektu.|
    |Projektový SDK| Může být prázdná při prvním použití NÁPADu. Vyberte **Nový...** a přejděte k JDK.|
    |Verze Sparku|Průvodce vytvořením integruje správnou verzi sady Spark SDK a Scala SDK. Synapse podporuje pouze **Spark 2.4.0**.|
    |||

    ![Výběr sady Apache Spark SDK](./media/intellij-tool-synapse/create-synapse-application02.png)

7. Vyberte **Dokončit**. Může to trvat několik minut, než bude projekt k dispozici.
8. Projekt Spark automaticky vytvoří artefakt za vás. Chcete-li zobrazit artefakt, proveďte následující postup:

   a. Z řádku nabídek přejděte na **soubor**  >  **struktura projektu...**.

   b. V okně **struktura projektu** vyberte **artefakty**.

   c. Po zobrazení artefaktu vyberte **Zrušit** .

    ![Informace o artefaktu v dialogovém okně](./media/intellij-tool-synapse/default-artifact-dialog.png)

9. Vyhledejte **LogQuery** z **MyApp**  >  **Src**  >  **Main**  >  **Scala** >  **Sample** >  **LogQuery**. Tento kurz používá **LogQuery** ke spuštění.

   ![Příkazy pro vytvoření třídy Scala z projektu](./media/intellij-tool-synapse/select-run-file.png)

## <a name="connect-to-your-spark-pools"></a>Připojení ke svým fondům Spark

Přihlaste se k předplatnému Azure a připojte se k vašim fondům Spark.

### <a name="sign-in-to-your-azure-subscription"></a>Přihlaste se ke svému předplatnému Azure

1. V řádku nabídek přejděte k **zobrazení**  >  **nástrojů Windows**  >  **Azure Explorer**.

   ![IntelliJ nápad ukazuje Azure Explorer](./media/intellij-tool-synapse/show-azure-explorer1.png)

2. V Průzkumníku Azure klikněte pravým tlačítkem na uzel **Azure** a pak vyberte **Přihlásit**se.

   ![Průzkumník nápadů pro IntelliJ klikněte pravým tlačítkem myši na Azure.](./media/intellij-tool-synapse/explorer-rightclick-azure.png)

3. V dialogovém okně **přihlášení do Azure** zvolte přihlášení k **zařízení**a pak vyberte **Přihlásit**se.

    ![IntelliJ nápad – přihlášení k Azure](./media/intellij-tool-synapse/intellij-view-explorer2.png)

4. V dialogovém okně **přihlášení k zařízení Azure** vyberte **Kopírovat&otevřít**.

   ![IntelliJ nápad – přihlášení k zařízení Azure](./media/intellij-tool-synapse/intellij-view-explorer5.png)

5. V rozhraní prohlížeče vložte kód a pak vyberte **Další**.

   ![Microsoft zadá pro HDI dialog Code (kód).](./media/intellij-tool-synapse/intellij-view-explorer6.png)

6. Zadejte svoje přihlašovací údaje Azure a pak zavřete prohlížeč.

   ![Dialog Microsoftu vstupuje do e-mailu pro HDI](./media/intellij-tool-synapse/intellij-view-explorer7.png)

7. Po přihlášení se v dialogovém okně **Vybrat odběry** zobrazí všechna předplatná Azure, která jsou přidružená k přihlašovacím údajům. Vyberte své předplatné a pak vyberte **Vybrat**.

    ![Dialogové okno Select Subscriptions (Výběr předplatných)](./media/intellij-tool-synapse/Select-Subscriptions.png)

8. V **Průzkumníkovi Azure**rozbalte **Apache Spark na synapse** a zobrazte tak pracovní prostory, které jsou ve vašich předplatných.

    ![IntelliJ nápad v hlavním zobrazení Průzkumníka Azure](./media/intellij-tool-synapse/azure-explorer-workspace.png)

9. Chcete-li zobrazit fondy Spark, můžete pracovní prostor dále rozšířit.

    ![Účty úložiště Azure Explorer](./media/intellij-tool-synapse/azure-explorer-pool.png)

## <a name="remote-run-a-spark-scala-application-on-a-spark-pool"></a>Vzdálené spuštění aplikace Spark Scala ve fondu Spark

Po vytvoření aplikace v Scala můžete tuto aplikaci spustit vzdáleně.

1. Otevřete okno **Konfigurace spuštění/ladění** výběrem ikony.

    ![Odeslání aplikace Spark do HDInsight – příkaz 1](./media/intellij-tool-synapse/open-configuration-window.png)

2. V dialogovém okně **Spustit/ladit konfigurace** vyberte **+** a pak vyberte **Apache Spark v synapse**.

    ![Odeslání aplikace Spark do HDInsight – příkaz 2](./media/intellij-tool-synapse/create-synapse-configuration02.png)

3. V okně **Spustit/ladit konfigurace** zadejte následující hodnoty a pak vyberte **OK**:

    |Vlastnost |Hodnota |
    |----|----|
    |Fondy Spark|Vyberte fondy Spark, na kterých chcete aplikaci spustit.|
    |Vyberte artefakt, který se má odeslat.|Ponechte výchozí nastavení.|
    |Název hlavní třídy|Výchozí hodnota je hlavní třída z vybraného souboru. Třídu můžete změnit tak, že vyberete tři tečky (**...**) a zvolíte jinou třídu.|
    |Konfigurace úloh|Můžete změnit výchozí klíč a hodnoty. Další informace najdete v článku [Apache Livy REST API](http://livy.incubator.apache.org./docs/latest/rest-api.html).|
    |Argumenty příkazového řádku|V případě potřeby můžete zadat argumenty oddělené mezerou pro hlavní třídu.|
    |Odkazované jar a odkazované soubory|Můžete zadat cesty pro odkazované jar a soubory, pokud existují. Můžete také procházet soubory ve virtuálním systému souborů Azure, který v současné době podporuje jenom cluster ADLS Gen2. Další informace: [Apache Spark konfiguraci](https://spark.apache.org/docs/latest/configuration.html#runtime-environment) a [Postup nahrání prostředků do clusteru](../../storage/blobs/storage-quickstart-blobs-storage-explorer.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).|
    |Úložiště nahrávání úlohy|Rozbalením zobrazíte další možnosti.|
    |Typ úložiště|Vyberte **použít Azure Blob k nahrání** nebo **použití výchozího účtu úložiště clusteru k nahrání** z rozevíracího seznamu.|
    |Účet úložiště|Zadejte svůj účet úložiště.|
    |Klíč úložiště|Zadejte svůj klíč úložiště.|
    |Kontejner úložiště|Po zadání **účtu úložiště** a **klíče úložiště** vyberte z rozevíracího seznamu svůj kontejner úložiště.|

    ![Dialogové okno pro odeslání Sparku 1](./media/intellij-tool-synapse/create-synapse-configuration03.png)

4. Vyberte ikonu **SparkJobRun** a odešlete projekt do vybraného fondu Spark. **Vzdálená úloha Spark na kartě cluster** zobrazuje průběh provádění úlohy v dolní části. Aplikaci můžete zastavit tak, že vyberete tlačítko červené.

    ![Okno pro odeslání Apache Spark](./media/intellij-tool-synapse/remotely-run-synapse.png)

    ![Dialogové okno pro odeslání Sparku 2](./media/intellij-tool-synapse/remotely-run-result.png)

## <a name="local-rundebug-apache-spark-applications"></a>Místní spouštěné/ladicí Apache Spark aplikace

Můžete postupovat podle pokynů níže pro nastavení místního běhu a místního ladění pro úlohu Apache Spark.

### <a name="scenario-1-do-local-run"></a>Scénář 1: místní spuštění

1. Otevřete dialog **Konfigurace spuštění/ladění** a vyberte znaménko plus ( **+** ). Pak vyberte možnost **Apache Spark v synapse** . Zadejte informace pro **název**, **název hlavní třídy** , který chcete uložit.

    ![IntelliJ spustit konfiguraci ladění s místním spuštěním 1](./media/intellij-tool-synapse/local-run-synapse.png)

    - Proměnné prostředí a WinUtils.exe umístění jsou pouze pro uživatele systému Windows.
    - Proměnné prostředí: proměnná prostředí systému může být automaticky rozpoznána, pokud jste ji nastavili před a není nutné ji přidat ručně.
    - [ UmístěníWinUtils.exe](http://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe): umístění WinUtils můžete zadat tak, že na pravé straně vyberete ikonu složky.

2. Pak vyberte místní tlačítko Play.

    ![IntelliJ spustit konfiguraci ladění v místním běhu 2](./media/intellij-tool-synapse/local-run-synapse01.png)

3. Po dokončení místního spuštění, pokud skript obsahuje výstup, můžete výstupní soubor kontrolovat z dat ve **data**  >  **__výchozím nastavení__**.

    ![Výsledek místního běhu projektu IntelliJ 1](./media/intellij-tool-synapse/spark-local-run-result.png)

### <a name="scenario-2-do-local-debugging"></a>Scénář 2: proveďte místní ladění

1. Otevřete skript **LogQuery** , nastavte zarážky.
2. Pro místní ladění vyberte ikonu **místního ladění** .

    ![Výsledek místního běhu projektu IntelliJ 2](./media/intellij-tool-synapse/local-debug-synapse.png)

## <a name="access-and-manage-synapse-workspace"></a>Přístup a Správa pracovního prostoru synapse

V Azure Exploreru můžete v rámci Azure Toolkit for IntelliJ provádět různé operace. V řádku nabídek přejděte k **zobrazení**  >  **nástrojů Windows**  >  **Azure Explorer**.

### <a name="launch-workspace"></a>Spustit pracovní prostor

1. V Azure Exploreru přejděte na **Apache Spark na synapse**a pak ho rozbalte.

    ![IntelliJ nápad v hlavním zobrazení Průzkumníka Azure](./media/intellij-tool-synapse/azure-explorer-workspace.png)

2. Klikněte pravým tlačítkem myši na pracovní prostor a vyberte možnost **Spustit pracovní prostor**, web bude otevřen.

    ![Zobrazení úlohy Spark podrobnosti o aplikaci 1](./media/intellij-tool-synapse/launch-workspace-synapse.png)

    ![Zobrazení úlohy Spark podrobnosti o aplikaci 2](./media/intellij-tool-synapse/launch-workspace-result.png)

## <a name="spark-console"></a>Konzola Spark

Můžete spustit místní konzolu Spark (Scala) nebo spustit konzolu Spark Livy Interactive Session (Scala).

### <a name="spark-local-console-scala"></a>Místní konzola Spark (Scala)

Ujistěte se, že jste splnili požadavky na WINUTILS.EXE.

1. Z řádku nabídek přejděte na **Spustit**  >  **úpravy konfigurace...**.
2. V okně **Konfigurace spuštění/ladění** v levém podokně přejděte na **Apache Spark v synapse**  >  **[Spark on synapse] MyApp**.
3. V hlavním okně vyberte kartu **spustit místně** .
4. Zadejte následující hodnoty a pak vyberte **OK**:

    |Vlastnost |Hodnota |
    |----|----|
    |Proměnné prostředí|Zajistěte, aby byla hodnota HADOOP_HOME správná.|
    |Umístění WINUTILS.exe|Zkontrolujte, zda je cesta správná.|

    ![Konfigurace sady místních konzol](./media/intellij-tool-synapse/local-console-synapse01.png)

5. Z projektu přejděte do **MyApp**  >  **Src**  >  **Main**  >  **Scala**  >  **MyApp**.
6. V řádku nabídek přejděte do konzoly **nástroje**  >  **Spark**  >  **spustit prostředí Spark místní konzola (Scala)**.
7. Pak se můžou zobrazit dvě dialogová okna, která vás požádají, pokud chcete automaticky opravovat závislosti. Pokud ano, vyberte možnost **automaticky opravit**.

    ![IntelliJ nápad Spark auto Fix Dialog1](./media/intellij-tool-synapse/intellij-console-autofix1.png)

    ![IntelliJ nápad Spark auto Fix dialog2](./media/intellij-tool-synapse/intellij-console-autofix2.png)

8. Konzola by měla vypadat podobně jako na obrázku níže. V okně konzoly zadejte `sc.appName` a stiskněte kombinaci kláves CTRL + ENTER. Zobrazí se výsledek. Místní konzolu můžete zastavit tak, že vyberete červené tlačítko.

    ![Výsledek místní konzoly IntelliJ](./media/intellij-tool-synapse/local-console-result.png)

### <a name="spark-livy-interactive-session-console-scala"></a>Konzola pro interaktivní relace Spark Livy (Scala)

Podporuje se jenom na IntelliJ 2018,2 a 2018,3.

1. Z řádku nabídek přejděte na **Spustit**  >  **úpravy konfigurace...**.

2. V okně **Konfigurace spuštění/ladění** v levém podokně přejděte na **Apache Spark v synapse**  >  **[Spark on synapse] MyApp**.

3. V hlavním okně vyberte kartu **vzdáleně spustit na clusteru** .

4. Zadejte následující hodnoty a pak vyberte **OK**:

    |Vlastnost |Hodnota |
    |----|----|
    |Název hlavní třídy| Vyberte název hlavní třídy.| 
    |Fondy Spark|Vyberte fondy Spark, na kterých chcete aplikaci spustit.|
    ||

    ![Konfigurace sady interaktivní konzoly](./media/intellij-tool-synapse/interactive-console-configuration.png)

5. Z projektu přejděte do **MyApp**  >  **Src**  >  **Main**  >  **Scala**  >  **MyApp**.

6. V řádku nabídek přejděte do konzoly **nástroje**  >  **Spark**  >  **Spustit Livy interaktivní relace (Scala) Sparku**.
7. Konzola by měla vypadat podobně jako na obrázku níže. V okně konzoly zadejte `sc.appName` a stiskněte kombinaci kláves CTRL + ENTER. Zobrazí se výsledek. Místní konzolu můžete zastavit tak, že vyberete červené tlačítko.

    ![Výsledek IntelliJ NÁPADu pro interaktivní konzolu](./media/intellij-tool-synapse/interactive-console-result.png)

### <a name="send-selection-to-spark-console"></a>Odeslat výběr do konzoly Spark

Výsledek skriptu můžete chtít zobrazit tak, že odešlete nějaký kód do místní konzoly nebo do konzoly Livy Interactive relace (Scala). Uděláte to tak, že v souboru Scala zvýrazníte nějaký kód a potom kliknete pravým tlačítkem myši na **Odeslat výběr do konzoly Spark**. Vybraný kód se odešle do konzoly a provede se. Výsledek se zobrazí za kódem v konzole nástroje. Konzola zkontroluje existující chyby.

   ![Odeslat výběr do konzoly Spark](./media/intellij-tool-synapse/send-selection-to-console.png)

## <a name="next-steps"></a>Další kroky

- [Azure Synapse Analytics](../overview-what-is.md)
- [Vytvoření nového fondu Apache Spark pro pracovní prostor Azure synapse Analytics](../../synapse-analytics/quickstart-create-apache-spark-pool-studio.md)
