---
title: Odeslání úloh z nástrojů R pro Visual Studio – Azure HDInsight
description: Odešlete úlohy R z místního počítače Visual Studio do clusteru HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/19/2019
ms.openlocfilehash: 73d1478ec2d6c90428f22a30ec82634df115d2f5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75435258"
---
# <a name="submit-jobs-from-r-tools-for-visual-studio"></a>Odesílání úloh z Nástrojů R pro Visual Studio

[Nástroje R pro visual studio](https://marketplace.visualstudio.com/items?itemName=MikhailArkhipov007.RTVS2019) (RTVS) je bezplatné rozšíření s otevřeným zdrojovým kódem pro edice Community (free), Professional a Enterprise sady Visual Studio [2017](https://www.visualstudio.com/downloads/)a [Visual Studio 2015 Update 3](https://go.microsoft.com/fwlink/?LinkId=691129) nebo vyšší. RTVS není k dispozici pro [Visual Studio 2019](https://docs.microsoft.com/visualstudio/porting/port-migrate-and-upgrade-visual-studio-projects?view=vs-2019).

RTVS vylepšuje váš pracovní postup R tím, že nabízí nástroje, jako je [interaktivní okno R](https://docs.microsoft.com/visualstudio/rtvs/interactive-repl) (REPL), intellisense (dokončení kódu), [vizualizace vykreslení](https://docs.microsoft.com/visualstudio/rtvs/visualizing-data) prostřednictvím knihoven R, jako jsou ggplot2 a ggviz, [ladění kódu R](https://docs.microsoft.com/visualstudio/rtvs/debugging)a další.

## <a name="set-up-your-environment"></a>Nastavení prostředí

1. Nainstalujte [nástroje R pro aplikaci Visual Studio](/visualstudio/rtvs/installing-r-tools-for-visual-studio).

    ![Instalace RTVS do Visual Studia 2017](./media/r-server-submit-jobs-r-tools-vs/install-r-tools-for-vs.png)

2. Vyberte zatížení *datové vědy a analytických aplikací* a vyberte **podporu jazyka R**, podporu **runtime pro vývoj jazyka R**a možnosti **klienta Microsoft R.**

3. Musíte mít veřejné a soukromé klíče pro ověřování SSH.
   <!-- {TODO tbd, no such file yet}[use SSH with HDInsight](hdinsight-hadoop-linux-use-ssh-windows.md) -->

4. Nainstalujte [server ML do](https://msdn.microsoft.com/microsoft-r/rserver-install-windows) počítače. ML Server [`RevoScaleR`](https://msdn.microsoft.com/microsoft-r/scaler/scaler) poskytuje `RxSpark` funkce a.

5. Nainstalujte [PuTTY,](https://www.putty.org/) abyste poskytli výpočetní kontext pro spouštění `RevoScaleR` funkcí z místního klienta do clusteru HDInsight.

6. Máte možnost použít nastavení datové vědy pro prostředí sady Visual Studio, které poskytuje nové rozložení pro váš pracovní prostor pro nástroje R.
   1. Chcete-li uložit aktuální nastavení sady Visual Studio, použijte příkaz **Nástroje > nastavení importu a exportu,** pak vyberte **Exportovat vybraná nastavení prostředí** a zadejte název souboru. Chcete-li tato nastavení obnovit, použijte stejný příkaz a vyberte **Importovat vybraná nastavení prostředí**.

   2. Přejděte na položku nabídky **Nástroje R** a vyberte Nastavení **datové vědy...**.

       ![Nastavení datové vědy sady Visual Studio](./media/r-server-submit-jobs-r-tools-vs/data-science-settings.png)

      > [!NOTE]  
      > Pomocí přístupu v kroku 1 můžete také uložit a obnovit vlastní rozložení datových vědců, místo opakování příkazu **Nastavení datové vědy.**

## <a name="execute-local-r-methods"></a>Spustit místní metody R

1. Vytvořte cluster služby HDInsight ML Services.
2. Nainstalujte [rozšíření RTVS](https://docs.microsoft.com/visualstudio/rtvs/installation).
3. Stáhněte si [ukázky zip souboru](https://github.com/Microsoft/RTVS-docs/archive/master.zip).
4. Otevřete `examples/Examples.sln` spuštění řešení v sadě Visual Studio.
5. Otevřete `1-Getting Started with R.R` soubor `A first look at R` ve složce řešení.
6. Začněte v horní části souboru a stisknutím kombinace kláves Ctrl+Enter odešlete každý řádek po jednom do okna Interaktivní R. Některé řádky může chvíli trvat, jak se instalace balíčků.
    * Případně můžete vybrat všechny řádky v souboru R (Ctrl+A), pak buď spustit všechny (Ctrl+Enter), nebo vybrat ikonu Spustit interaktivní na panelu nástrojů.

        ![Visual Studio spustit interaktivní](./media/r-server-submit-jobs-r-tools-vs/execute-interactive1.png)

7. Po spuštění všech řádků ve skriptu byste měli vidět výstup podobný tomuto:

    ![Nástroje Visual Studio workspace R](./media/r-server-submit-jobs-r-tools-vs/visual-studio-workspace.png)

## <a name="submit-jobs-to-an-hdinsight-ml-services-cluster"></a>Odeslání úloh do clusteru služby HDInsight ML Services

Pomocí klienta Microsoft ML Server/Microsoft R z počítače se systémem Windows vybaveného putty můžete vytvořit výpočetní kontext, který bude spouštět distribuované `RevoScaleR` funkce z místního klienta do clusteru HDInsight. Slouží `RxSpark` k vytvoření výpočetního kontextu, zadání uživatelského jména, hraničního uzlu clusteru Apache Hadoop, přepínačů SSH atd.

1. Adresa hraničního uzlu služby ML `CLUSTERNAME-ed-ssh.azurehdinsight.net` `CLUSTERNAME` services na serveru HDInsight je místo, kde je název clusteru služby ML Services.

1. Vložte následující kód do interaktivního okna R v sadě Visual Studio a změňte hodnoty proměnných nastavení tak, aby odpovídaly vašemu prostředí.

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

   ![apache jiskra nastavení kontextu](./media/r-server-submit-jobs-r-tools-vs/apache-spark-context.png)

1. V interaktivním okně R proveďte následující příkazy:

    ```R
    rxHadoopCommand("version") # should return version information
    rxHadoopMakeDir("/user/RevoShare/newUser") # creates a new folder in your storage account
    rxHadoopCopy("/example/data/people.json", "/user/RevoShare/newUser") # copies file to new folder
    ```

    Zobrazený výstup by měl vypadat přibližně takto:

    ![Úspěšné spuštění](./media/r-server-submit-jobs-r-tools-vs/successful-rx-commands.png) příkazu rx
1. Ověřte, zda `rxHadoopCopy` byl `people.json` soubor úspěšně zkopírován `/user/RevoShare/newUser` z ukázkové datové složky do nově vytvořené složky:

    1. V podokně clusteru SLUŽBY HDInsight ML v Azure vyberte **účty úložiště** z nabídky na levé straně.

        ![Účty úložiště Azure HDInsight](./media/r-server-submit-jobs-r-tools-vs/hdinsight-storage-accounts.png)

    2. Vyberte výchozí účet úložiště pro váš cluster a poznamenejte si název kontejneru nebo adresáře.

    3. V levém podokně účtu úložiště vyberte **Kontejnery.**

        ![Kontejnery úložiště Azure HDInsight](./media/r-server-submit-jobs-r-tools-vs/hdi-storage-containers.png)

    4. Vyberte název kontejneru clusteru, přejděte do **uživatelské** složky (možná budete muset kliknout na *tlačítko Načíst více* v dolní části seznamu), pak vyberte *RevoShare*, pak **newUser**. Soubor `people.json` by měl být `newUser` zobrazen ve složce.

        ![HDInsight zkopíroval umístění složky souborů](./media/r-server-submit-jobs-r-tools-vs/hdinsight-copied-file.png)

1. Po dokončení používání aktuálního kontextu Apache Spark, musíte ho zastavit. Nelze spustit více kontextů najednou.

    ```R
    rxStopEngine(mySparkCluster)
    ```

## <a name="next-steps"></a>Další kroky

* [Možnosti výpočetního kontextu pro služby ML na hdinsightu](r-server-compute-contexts.md)
* [Kombinace ScaleR a SparkR](../hdinsight-hadoop-r-scaler-sparkr.md) poskytuje příklad předpovědí zpoždění letu letecké společnosti.
