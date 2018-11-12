---
title: Odesílání úloh z nástrojů R pro Visual Studio – Azure HDInsight
description: Odesílání úloh R z místního počítače Visual Studio do clusteru služby HDInsight.
services: hdinsight
ms.service: hdinsight
author: maxluk
ms.author: maxluk
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/27/2018
ms.openlocfilehash: 400cb16e4f4440283a783116c4ee843bc0a7344c
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51248567"
---
# <a name="submit-jobs-from-r-tools-for-visual-studio"></a>Odesílání úloh z Nástrojů R pro Visual Studio

[Nástroje R pro Visual Studio](https://www.visualstudio.com/vs/rtvs/) (RTVS) je zdarma, open source rozšíření pro Community (zdarma), Professional a verze Enterprise Edition obou [Visual Studio 2017](https://www.visualstudio.com/downloads/), a [Visual Studio 2015 Update 3](https://go.microsoft.com/fwlink/?LinkId=691129)nebo vyšší.

RTVS vylepšuje pracovního postupu R tím, že nabízí nástroje, jako [interaktivní okno R](https://docs.microsoft.com/visualstudio/rtvs/interactive-repl) (REPL), technologie intellisense (dokončování kódu), [vykreslení vizualizace](https://docs.microsoft.com/visualstudio/rtvs/visualizing-data) prostřednictvím knihoven jazyka R, jako jsou ggplot2 a ggviz, [Ladění kódu R](https://docs.microsoft.com/visualstudio/rtvs/debugging)a provádění dalších akcí.

## <a name="set-up-your-environment"></a>Nastavení prostředí

1. Nainstalujte [nástroje R pro Visual Studio](https://docs.microsoft.com/visualstudio/rtvs/installation).

    ![Instalace RTVS v sadě Visual Studio 2017](./media/r-server-submit-jobs-r-tools-vs/install-r-tools-for-vs.png)

2. Vyberte *pro datové vědy a analytické aplikace* úloh, vyberte **podpora jazyka R**, **podpora modulu CLR pro vývoj v jazyce R**, a  **Microsoft R Client** možnosti.

3. Musíte mít veřejného a privátního klíče pro ověřování SSH.
<!-- {TODO tbd, no such file yet}[use SSH with HDInsight](hdinsight-hadoop-linux-use-ssh-windows.md) -->

4. Nainstalujte [ML Server](https://msdn.microsoft.com/microsoft-r/rserver-install-windows) na svém počítači. ML Server poskytuje [ `RevoScaleR` ](https://msdn.microsoft.com/microsoft-r/scaler/scaler) a `RxSpark` funkce.

5. Nainstalujte [PuTTY](http://www.putty.org/) poskytnout výpočetního kontextu pro spuštění `RevoScaleR` funkcí z místního klienta do clusteru HDInsight.

6. Máte možnost použít nastavení pro datové vědy pro prostředí sady Visual Studio, který poskytuje nové rozložení pro váš pracovní prostor pro nástroje R.
    1. Chcete-li uložit aktuální nastavení sady Visual Studio, použijte **nástroje > Nastavení importu a exportu** příkaz a pak vyberte **exportovat vybrané nastavení prostředí** a zadejte název souboru. Obnovení těchto nastavení, použijte stejný příkaz a vyberte **importovat vybrané nastavení prostředí**.

    2. Přejděte **nástroje R** nabídka položek, vyberte **nastavení pro datové vědy...** .

        ![Nastavení pro datové vědy...](./media/r-server-submit-jobs-r-tools-vs/data-science-settings.png)

    > [!NOTE]
    > Pomocí přístupu v kroku 1, můžete také uložit a obnovit rozložení mezi odborníky přes data individuální namísto opakování **nastavení pro datové vědy** příkazu.

## <a name="execute-local-r-methods"></a>Spusťte místní metody R

1. Vytvoření vašeho [clusteru služby HDInsight ML](r-server-get-started.md).
2. Nainstalujte [RTVS rozšíření](https://docs.microsoft.com/visualstudio/rtvs/installation).
3. Stáhněte si [soubor zip, ukázky](https://github.com/Microsoft/RTVS-docs/archive/master.zip).
4. Otevřít `examples/Examples.sln` spustíte řešení v sadě Visual Studio.
5. Otevřít `1-Getting Started with R.R` soubor `A first look at R` složku řešení.
6. Spouští se v horní části souboru, stiskněte klávesy Ctrl + Enter zaslání každého řádku, jeden po druhém, interaktivní okno R. Některé řádky může nějakou dobu, během instalace balíčků.
    * Alternativně je můžete vybrat všechny řádky v souboru jazyka R (Ctrl + A), pak provést všechny (Ctrl + Enter) nebo vybrat spuštění interaktivní ikonu na panelu nástrojů.
        ![Spustit interaktivní](./media/r-server-submit-jobs-r-tools-vs/execute-interactive.png)

7. Po spuštění všech řádků ve skriptu, byste měli vidět výstup podobný tomuto:

    ![Nastavení pro datové vědy...](./media/r-server-submit-jobs-r-tools-vs/workspace.png)

## <a name="submit-jobs-to-an-hdinsight-ml-services-cluster"></a>Odesílání úloh do clusteru služby HDInsight ML

Pomocí klienta Microsoft ML Server nebo Microsoft R v počítači Windows pomocí klienta PuTTY, můžete vytvořit výpočetní kontext, který se spustí distribuované `RevoScaleR` funkcí z místního klienta do clusteru HDInsight. Použití `RxSpark` vytvoření výpočetního kontextu zadání vašeho uživatelského jména, Hadoop cluster hraniční uzel, přepínače SSH a tak dále.

1. Chcete-li najít název hostitele hraničního uzlu, otevřete podokno vaší služby ML HDInsight clusteru v Azure a pak vyberte **Secure Shell (SSH)** v horní nabídce podokno s přehledem.

    ![Secure Shell (SSH)](./media/r-server-submit-jobs-r-tools-vs/ssh.png)

2. Kopírovat **název hostitele hraničního uzlu** hodnotu.

    ![Název hostitele hraničního uzlu](./media/r-server-submit-jobs-r-tools-vs/edge-node.png)

3. Vložte následující kód do R interaktivního okna v sadě Visual Studio, změna hodnot proměnných instalační program, aby odpovídaly vašemu prostředí.

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
    
    ![Nastavit kontext Spark](./media/r-server-submit-jobs-r-tools-vs/spark-context.png)

4. Spusťte následující příkazy v R interaktivním okně:

    ```R
    rxHadoopCommand("version") # should return version information
    rxHadoopMakeDir("/user/RevoShare/newUser") # creates a new folder in your storage account
    rxHadoopCopy("/example/data/people.json", "/user/RevoShare/newUser") # copies file to new folder
    ```

    Zobrazený výstup by měl vypadat přibližně takto:

    ![Úspěšné provedení příkazu příjmu](./media/r-server-submit-jobs-r-tools-vs/rx-commands.png)

5. Ověřte, že `rxHadoopCopy` úspěšně zkopírovala `people.json` soubor ze složky příklad dat na nově vytvořený `/user/RevoShare/newUser` složky:

    1. Z podokna clusteru služby HDInsight ML v Azure, vyberte **účty úložiště** z nabídky na levé straně.

        ![Účty úložiště](./media/r-server-submit-jobs-r-tools-vs/storage-accounts.png)

    2. Vyberte výchozí účet úložiště pro váš cluster, a poznamenejte si název kontejneru/adresáři.

    3. Vyberte **kontejnery** v levé nabídce na podokno vašeho účtu úložiště.

        ![Containers](./media/r-server-submit-jobs-r-tools-vs/containers.png)

    4. Vyberte název kontejneru vašeho clusteru, přejděte **uživatele** složky (možná muset kliknout na *načíst další* v dolní části seznamu) a pak vyberte *RevoShare*, pak **newUser**. `people.json` Souboru má být zobrazen v `newUser` složky.

        ![Zkopírovaný soubor](./media/r-server-submit-jobs-r-tools-vs/copied-file.png)

6. Až budete hotovi, pomocí aktuálního kontextu Sparku, je potřeba ho zastavit. Nelze spustit více kontexty najednou.

    ```R
    rxStopEngine(mySparkCluster)
    ```

## <a name="next-steps"></a>Další postup

* [Možnosti výpočetního kontextu pro služby ML v HDInsight](r-server-compute-contexts.md)
* [Kombinování ScaleR a SparkR](../hdinsight-hadoop-r-scaler-sparkr.md) poskytuje příklad předpovědi zpoždění letů letecká společnost.
<!-- * You can also submit R jobs with the [R Studio Server](hdinsight-submit-jobs-from-r-studio-server.md) -->
