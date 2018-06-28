---
title: Odesílání úloh z R nástrojů pro Visual Studio – Azure HDInsight | Microsoft Docs
description: Odeslání úlohy R ze svého místního počítače v sadě Visual Studio do clusteru HDInsight.
services: hdinsight
documentationcenter: ''
author: maxluk
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: maxluk
ms.openlocfilehash: eb7b538737e54b2c3d8a32e2ba65f7e0762a302c
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37047152"
---
# <a name="submit-jobs-from-r-tools-for-visual-studio"></a>Odesílání úloh z Nástrojů R pro Visual Studio

[R Tools pro Visual Studio](https://www.visualstudio.com/vs/rtvs/) (RTVS) je bezplatná, open-source rozšíření pro Community (zdarma), Professional a verze Enterprise Edition obou [Visual Studio 2017](https://www.visualstudio.com/downloads/), a [Visual Studio 2015 Update 3](http://go.microsoft.com/fwlink/?LinkId=691129)nebo vyšší.

RTVS vylepšuje pracovní postup R prostřednictvím nabídky Nástroje, jako [R interaktivních okna](https://docs.microsoft.com/visualstudio/rtvs/interactive-repl) (REPL), intellisense (doplňování kódu), [vykreslení vizualizace](https://docs.microsoft.com/visualstudio/rtvs/visualizing-data) prostřednictvím knihovny R jako ggplot2 a ggviz, [Ladění kódu R](https://docs.microsoft.com/visualstudio/rtvs/debugging)a další.

## <a name="set-up-your-environment"></a>Nastavení prostředí

1. Nainstalujte [R Tools pro Visual Studio](https://docs.microsoft.com/visualstudio/rtvs/installation).

    ![Instalace RTVS v Visual Studio 2017](./media/r-server-submit-jobs-r-tools-vs/install-r-tools-for-vs.png)

2. Vyberte *vědecké zpracování dat a analytických aplikací* úlohy, vyberte **podporu jazyka R**, **podporu Runtime pro vývoj R**, a  **R klienta Microsoft** možnosti.

3. Je potřeba mít veřejné a soukromé klíče pro ověřování SSH.
<!-- {TODO tbd, no such file yet}[use SSH with HDInsight](hdinsight-hadoop-linux-use-ssh-windows.md) -->

4. Nainstalujte [ML serveru](https://msdn.microsoft.com/microsoft-r/rserver-install-windows) na váš počítač. ML Server poskytuje [ `RevoScaleR` ](https://msdn.microsoft.com/microsoft-r/scaler/scaler) a `RxSpark` funkce.

5. Nainstalujte [PuTTY](http://www.putty.org/) zajistit výpočetní kontext spuštění `RevoScaleR` funkce z místního klienta ke svému clusteru HDInsight.

6. Máte možnost použít vědecké účely nastavení dat pro vaše prostředí sady Visual Studio, který poskytuje nové rozložení pracovního prostoru pro nástroje R.
    1. Pokud chcete uložit aktuální nastavení sady Visual Studio, použijte **nástroje > Nastavení importu a exportu** příkaz a pak vyberte **Export vybrané nastavení prostředí** a zadejte název souboru. K obnovení těchto nastavení, použijte stejný příkaz a vyberte **importovat vybrané nastavení prostředí**.

    2. Přejděte na **R nástroje** nabídky položky, pak vyberte **datové vědy nastavení...** .

        ![Nastavení dat vědecké účely...](./media/r-server-submit-jobs-r-tools-vs/data-science-settings.png)

    > [!NOTE]
    > Pomocí přístupu v kroku 1, můžete také uložit a obnovit rozložení vědecký pracovník přizpůsobené data místo opakování **nastavení vědecké účely dat** příkaz.

## <a name="execute-local-r-methods"></a>Spustit místní metody R

1. Vytvoření vaší [clusteru služby HDInsight ML](r-server-get-started.md).
2. Nainstalujte [RTVS rozšíření](https://docs.microsoft.com/visualstudio/rtvs/installation).
3. Stažení [soubor zip ukázky](https://github.com/Microsoft/RTVS-docs/archive/master.zip).
4. Otevřete `examples/Examples.sln` spustíte řešení v sadě Visual Studio.
5. Otevřete `1-Getting Started with R.R` v soubor `A first look at R` složce řešení.
6. Spouštění v horní části souboru, stiskněte klávesy Ctrl + Enter k odeslání každého řádku jeden po druhém, do okna R interaktivní. Některé řádky může chvíli trvat, instalace balíčků.
    * Alternativně můžete vybrat všechny řádky v souboru R (Ctrl + A), pak buď spustit všechny (Ctrl + Enter) nebo vybrat provést interaktivní ikonu na panelu nástrojů.
        ![Spuštění interaktivního](./media/r-server-submit-jobs-r-tools-vs/execute-interactive.png)

7. Po spuštění všech řádků ve skriptu, měli byste vidět výstup podobný tomuto:

    ![Nastavení dat vědecké účely...](./media/r-server-submit-jobs-r-tools-vs/workspace.png)

## <a name="submit-jobs-to-an-hdinsight-ml-services-cluster"></a>Odesílání úloh do clusteru služby HDInsight ML

Pomocí klienta Microsoft ML Server nebo Microsoft R z Windows počítače vybavené PuTTY, můžete vytvořit výpočetní kontext, který se spustí distribuované `RevoScaleR` funkce z místního klienta ke svému clusteru HDInsight. Použití `RxSpark` vytvořit výpočetní kontext, vaše uživatelské jméno, clusteru Hadoop hraniční uzel, přepínače SSH a tak dále.

1. Chcete-li najít název hostitele hraniční uzel, otevřete podokno clusteru vaší ML služby HDInsight v Azure a poté vyberte **Secure Shell (SSH)** v horní nabídce v podokně přehled.

    ![Secure Shell (SSH)](./media/r-server-submit-jobs-r-tools-vs/ssh.png)

2. Kopírování **název hostitele hraniční uzel** hodnotu.

    ![Název hostitele hraničního uzlu](./media/r-server-submit-jobs-r-tools-vs/edge-node.png)

3. Vložte následující kód do okna R interaktivní v sadě Visual Studio, změna hodnoty proměnných instalační program tak, aby odpovídaly prostředí.

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
    
    ![Nastavení kontext Spark](./media/r-server-submit-jobs-r-tools-vs/spark-context.png)

4. V okně R interaktivní spuštěním následujících příkazů:

    ```R
    rxHadoopCommand("version") # should return version information
    rxHadoopMakeDir("/user/RevoShare/newUser") # creates a new folder in your storage account
    rxHadoopCopy("/example/data/people.json", "/user/RevoShare/newUser") # copies file to new folder
    ```

    Zobrazený výstup by měl vypadat přibližně takto:

    ![Úspěšné provedení příkazu rx](./media/r-server-submit-jobs-r-tools-vs/rx-commands.png)

5. Ověřte, zda `rxHadoopCopy` úspěšně zkopírovaný `people.json` soubor ze složky dat příklad nově vytvořený `/user/RevoShare/newUser` složky:

    1. Z podokna clusteru služby HDInsight ML v Azure, vyberte **účty úložiště** z nabídky na levé straně.

        ![Účty úložiště](./media/r-server-submit-jobs-r-tools-vs/storage-accounts.png)

    2. Vyberte výchozí účet úložiště pro cluster, provedení poznamenejte si název kontejneru nebo adresáře.

    3. Vyberte **kontejnery** z nabídky na levé straně na vaše podokně účet úložiště.

        ![Containers](./media/r-server-submit-jobs-r-tools-vs/containers.png)

    4. Vyberte název kontejneru vašeho clusteru, přejděte na **uživatele** složky (možná budete muset klikněte na tlačítko *načtěte více* v dolní části seznamu), pak vyberte *RevoShare*, pak **newUser**. `people.json` Soubor má být zobrazena v `newUser` složky.

        ![Zkopírovaný soubor](./media/r-server-submit-jobs-r-tools-vs/copied-file.png)

6. Jakmile budete hotovi, v aktuálním kontextu Spark, je nutné zastavit. Nelze spustit více kontexty najednou.

    ```R
    rxStopEngine(mySparkCluster)
    ```

## <a name="next-steps"></a>Další postup

* [Výpočetní kontextu možnosti pro ML služby v HDInsight](r-server-compute-contexts.md)
* [Kombinování ScaleR a SparkR](../hdinsight-hadoop-r-scaler-sparkr.md) poskytuje příklad letecká společnost letu zpoždění předpovědi.
<!-- * You can also submit R jobs with the [R Studio Server](hdinsight-submit-jobs-from-r-studio-server.md) -->
