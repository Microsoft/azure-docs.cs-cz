---
title: Odesílání úloh z Nástroje R pro Visual Studio – Azure HDInsight
description: Odešlete úlohy R z místního počítače s Visual Studiem do clusteru HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/19/2019
ms.openlocfilehash: 4c02632c44d095a6d1d1c583a1ab201acad63bf9
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2019
ms.locfileid: "74229002"
---
# <a name="submit-jobs-from-r-tools-for-visual-studio"></a>Odesílání úloh z Nástrojů R pro Visual Studio

[Nástroje R pro Visual Studio](https://www.visualstudio.com/vs/rtvs/) (RTVS) je bezplatné open source rozšíření pro edice Enterprise (Free), Professional a Enterprise sady [Visual Studio 2017](https://www.visualstudio.com/downloads/)a [Visual Studio 2015 Update 3](https://go.microsoft.com/fwlink/?LinkId=691129) nebo vyšší. RTVS není pro [Visual Studio 2019](https://docs.microsoft.com/visualstudio/porting/port-migrate-and-upgrade-visual-studio-projects?view=vs-2019)k dispozici.

RTVS vylepšuje pracovní postup R tím, že nabízí nástroje, jako je například [okno interaktivní R](https://docs.microsoft.com/visualstudio/rtvs/interactive-repl) (REPL), IntelliSense (dokončování kódu), [vizualizaci grafu](https://docs.microsoft.com/visualstudio/rtvs/visualizing-data) pomocí knihoven R, jako jsou ggplot2 a Ggviz, [ladění kódu R](https://docs.microsoft.com/visualstudio/rtvs/debugging)a další.

## <a name="set-up-your-environment"></a>Nastavení prostředí

1. Nainstalujte [nástroje R pro Visual Studio](/visualstudio/rtvs/installing-r-tools-for-visual-studio).

    ![Instalace RTVS v aplikaci Visual Studio 2017](./media/r-server-submit-jobs-r-tools-vs/install-r-tools-for-vs.png)

2. Vyberte úlohu pro *datové vědy a analytické aplikace* a pak vyberte **podporu jazyka r**, **podporu modulu runtime pro vývoj**v jazyce r a možnosti **Microsoft R Client** .

3. Pro ověřování SSH musíte mít veřejné a privátní klíče.
   <!-- {TODO tbd, no such file yet}[use SSH with HDInsight](hdinsight-hadoop-linux-use-ssh-windows.md) -->

4. Nainstalujte na svém počítači [ml Server](https://msdn.microsoft.com/microsoft-r/rserver-install-windows) . ML Server poskytuje funkce [`RevoScaleR`](https://msdn.microsoft.com/microsoft-r/scaler/scaler) a `RxSpark`.

5. Nainstalujte si výstup, který poskytuje výpočetní [kontext pro spouštění](https://www.putty.org/) `RevoScaleR`ch funkcí z místního klienta do clusteru HDInsight.

6. Máte možnost použít Nastavení pro datové vědy pro prostředí sady Visual Studio, které poskytuje nové rozložení pro váš pracovní prostor pro nástroje jazyka R.
   1. Pokud chcete uložit aktuální nastavení sady Visual Studio, použijte příkaz **nástroje > import a export nastavení** a pak vyberte **exportovat vybraná nastavení prostředí** a zadejte název souboru. Chcete-li tato nastavení obnovit, použijte stejný příkaz a vyberte možnost **Importovat vybrané nastavení prostředí**.

   2. Přejděte na položku nabídky **Nástroje jazyka R** a pak vyberte **nastavení pro datové vědy...** .

       ![Nastavení pro datové vědy sady Visual Studio](./media/r-server-submit-jobs-r-tools-vs/data-science-settings.png)

      > [!NOTE]  
      > Pomocí přístupu v kroku 1 můžete také uložit a obnovit individuální rozvržení dat pro odborníky na data, a ne opakovat příkaz **nastavení pro datové vědy** .

## <a name="execute-local-r-methods"></a>Spustit místní metody jazyka R

1. Vytvořte cluster služeb HDInsight ML.
2. Nainstalujte [rozšíření RTVS](https://docs.microsoft.com/visualstudio/rtvs/installation).
3. Stáhněte si [soubor zip s ukázkami](https://github.com/Microsoft/RTVS-docs/archive/master.zip).
4. Otevřete `examples/Examples.sln` pro spuštění řešení v aplikaci Visual Studio.
5. Otevřete soubor `1-Getting Started with R.R` ve složce řešení `A first look at R`.
6. V horní části souboru stiskněte kombinaci kláves CTRL + ENTER pro odeslání každého řádku, a to po jednom, do okna Interaktivní R. Některé řádky můžou při instalaci balíčků chvíli trvat.
    * Případně můžete vybrat všechny řádky v souboru R (CTRL + A), pak buď provést vše (CTRL + ENTER), nebo na panelu nástrojů vybrat ikonu spustit interaktivní.

        ![Interaktivní spuštění sady Visual Studio](./media/r-server-submit-jobs-r-tools-vs/execute-interactive1.png)

7. Po spuštění všech řádků ve skriptu byste měli vidět výstup podobný tomuto:

    ![Nástroje jazyka R pro Visual Studio Workspace](./media/r-server-submit-jobs-r-tools-vs/visual-studio-workspace.png)

## <a name="submit-jobs-to-an-hdinsight-ml-services-cluster"></a>Odeslání úloh do clusteru služby HDInsight ML

Pomocí Microsoft ML Server/Microsoft R Client z počítače s Windows vybaveného pro zápis můžete vytvořit výpočetní kontext, který spustí distribuované `RevoScaleR` funkce z místního klienta do vašeho clusteru HDInsight. Použijte `RxSpark` k vytvoření výpočetního kontextu, zadání uživatelského jména, hraničního uzlu Apache Hadoopho clusteru, přepínačů SSH a tak dále.

1. Adresa hraničního uzlu služby ML v HDInsight je `CLUSTERNAME-ed-ssh.azurehdinsight.net`, kde `CLUSTERNAME` je název clusteru služeb ML.

1. Vložte následující kód do okna Interaktivní R v aplikaci Visual Studio a změňte hodnoty proměnných nastavení tak, aby odpovídaly vašemu prostředí.

    ```R
    # Setup variables that connect the compute context to your HDInsight cluster
    mySshHostname <- 'r-cluster-ed-ssh.azurehdinsight.net ' # HDI secure shell hostname
    mySshUsername <- 'sshuser' # HDI SSH username
    mySshClientDir <- "C:\\Program Files (x86)\\PuTTY"
    mySshSwitches <- '-i C:\\Users\\azureuser\\r.ppk' # Path to your private ssh key
    myHdfsShareDir <- paste("/user/RevoShare", mySshUsername, sep = "/")
    myShareDir <- paste("/var/RevoShare", mySshUsername, sep = "/")
    mySshProfileScript <- "/usr/lib64/microsoft-r/3.3/hadoop/RevoHadoopEnvVars.site"

    # Create the Spark Cluster compute context
    mySparkCluster <- RxSpark(
          sshUsername = mySshUsername,
          sshHostname = mySshHostname,
          sshSwitches = mySshSwitches,
          sshProfileScript = mySshProfileScript,
          consoleOutput = TRUE,
          hdfsShareDir = myHdfsShareDir,
          shareDir = myShareDir,
          sshClientDir = mySshClientDir
    )

    # Set the current compute context as the Spark compute context defined above
    rxSetComputeContext(mySparkCluster)
    ```

   ![Apache Spark – nastavení kontextu](./media/r-server-submit-jobs-r-tools-vs/apache-spark-context.png)

1. V okně Interaktivní R spusťte následující příkazy:

    ```R
    rxHadoopCommand("version") # should return version information
    rxHadoopMakeDir("/user/RevoShare/newUser") # creates a new folder in your storage account
    rxHadoopCopy("/example/data/people.json", "/user/RevoShare/newUser") # copies file to new folder
    ```

    Zobrazený výstup by měl vypadat přibližně takto:

    ![úspěšné spuštění příkazu RX](./media/r-server-submit-jobs-r-tools-vs/successful-rx-commands.png)
1. Ověřte, že `rxHadoopCopy` úspěšně zkopíroval `people.json` soubor z ukázkové složky dat do nově vytvořené `/user/RevoShare/newUser` složky:

    1. Z podokna clusteru služby HDInsight ML v Azure v nabídce vlevo vyberte **účty úložiště** .

        ![Účty Azure HDInsight Storage](./media/r-server-submit-jobs-r-tools-vs/hdinsight-storage-accounts.png)

    2. Vyberte výchozí účet úložiště pro váš cluster a poznamenejte si název kontejneru nebo adresáře.

    3. V nabídce na levé straně v podokně účtu úložiště vyberte **kontejnery** .

        ![Kontejnery Azure HDInsight Storage](./media/r-server-submit-jobs-r-tools-vs/hdi-storage-containers.png)

    4. Vyberte název kontejneru vašeho clusteru, přejděte do složky **uživatele** (možná budete muset kliknout na *načíst další* v dolní části seznamu) a pak vyberte *RevoShare*a **nový_uživatel**. `people.json` soubor by měl být zobrazen ve složce `newUser`.

        ![HDInsight zkopíroval umístění složky souborů](./media/r-server-submit-jobs-r-tools-vs/hdinsight-copied-file.png)

1. Po dokončení používání aktuálního kontextu Apache Spark je nutné ho zastavit. Nelze spustit více kontextů najednou.

    ```R
    rxStopEngine(mySparkCluster)
    ```

## <a name="next-steps"></a>Další kroky

* [Možnosti výpočetního kontextu pro služby ML v HDInsight](r-server-compute-contexts.md)
* [Kombinování škály a Sparku](../hdinsight-hadoop-r-scaler-sparkr.md) poskytuje příklad zpoždění leteckého letu předpovědi.
