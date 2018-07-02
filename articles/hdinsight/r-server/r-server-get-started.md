---
title: Začínáme se službou ML Services v HDInsight – Azure | Microsoft Docs
description: Naučíte se vytvořit Apache Spark v clusteru HDInsight, který obsahuje službu ML Services, a odeslat do clusteru skript R.
services: hdinsight
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: b5e111f3-c029-436c-ba22-c54a4a3016e3
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: R
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 06/27/2018
ms.author: nitinme
ms.openlocfilehash: efc7ada12f722b0447712594de496e933bde3d36
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37053428"
---
# <a name="get-started-with-ml-services-on-azure-hdinsight"></a>Začínáme se službou ML Services v Azure HDInsight

Azure HDInsight umožňuje vytvořit cluster služby ML Services. Díky této možnosti můžou skripty R používat Spark a MapReduce ke spouštění distribuovaných výpočtů. V tomto dokumentu se naučíte vytvořit cluster služby ML Services v HDInsight a následně spustit skript R, který ukazuje použití Sparku k distribuovaným výpočtům jazyka R.

## <a name="prerequisites"></a>Požadavky

* **Předplatné Azure:** Než začnete tento kurz, musíte mít předplatné Azure. Další informace najdete v článku [Získání bezplatné zkušební verze Microsoft Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **Klient Secure Shell (SSH):** Klient SSH slouží k vzdálenému připojení ke clusteru HDInsight a spouštění příkazů přímo v clusteru. Další informace najdete v tématu [Použití SSH se službou HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).


<a name="create-hdi-custer-with-aure-portal"></a>
## <a name="create-the-cluster-using-the-azure-portal"></a>Vytvoření clusteru pomocí webu Azure Portal

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

2. Klikněte na **Vytvořit prostředek** > **Data a analýzy** > **HDInsight**.

3. V části **Základy** zadejte následující informace:

    * **Název clusteru:** Název clusteru HDInsight.
    * **Předplatné:** Vyberte předplatné, které chcete použít.
    * **Uživatelské jméno přihlášení clusteru** a **Heslo přihlášení clusteru**: Přihlašovací údaje pro přístup ke clusteru pomocí protokolu HTTPS. Tyto přihlašovací údaje se používají i pro přístup ke službám, jako jsou webové uživatelské rozhraní Ambari nebo REST API.
    * **Uživatelské jméno Secure Shell (SSH:)** Přihlašovací údaje používané pro přístup ke clusteru přes SSH. Ve výchozím nastavení je heslo stejné jako pro přihlášení ke clusteru.
    * **Skupina prostředků:** Skupina prostředků, ve které se cluster vytváří.
    * **Umístění:** Oblast Azure, ve které se cluster vytváří.

        ![Základní informace o clusteru](./media/r-server-get-started/clustername.png)

4. Vyberte **Typ clusteru** a pak v části **Konfigurace clusteru** zadejte následující hodnoty:

    * **Typ clusteru**: ML Services

    * **Operační systém:** Linux

    * **Verze**: ML Server 9.3 (HDI 3.6). Zpráva k vydání verze pro ML Server 9.3 je dostupná na webu [docs.microsoft.com](https://docs.microsoft.com/machine-learning-server/whats-new-in-machine-learning-server).

    * **Komunitní verze R Studia pro ML Server:** Toto integrované vývojové prostředí (IDE) v prohlížeči se ve výchozím nastavení nainstaluje na hraniční uzel. Pokud si jeho instalaci nepřejete, zrušte zaškrtnutí tohoto políčka. Pokud zvolíte jeho instalaci, adresu URL pro přístup k přihlašovací stránce RStudio Serveru najdete po vytvoření clusteru v okně aplikace portálu pro váš cluster.

        ![Základní informace o clusteru](./media/r-server-get-started/clustertypeconfig.png)

4. Po výběru typu clusteru použijte tlačítko __Vybrat__ k výběru typu clusteru. Dále stisknutím tlačítka __Další__ dokončete základní konfiguraci.

5. V části **Úložiště** vyberte nebo vytvořte účet úložiště. Pro ukázkový postup v tomto dokumentu ponechte všechna ostatní pole v této části na výchozích hodnotách. Stisknutím tlačítka __Další__ uložte konfiguraci úložiště.

    ![Nastavení účtu úložiště pro HDInsight](./media/r-server-get-started/cluster-storage.png)

6. V části **Souhrn** zkontrolujte konfiguraci clusteru. Pomocí odkazů __Upravit__ opravte případná chybná nastavení. Nakonec pomocí tlačítka __Vytvořit__ cluster vytvořte.

    ![Nastavení účtu úložiště pro HDInsight](./media/r-server-get-started/clustersummary.png)

    > [!NOTE]
    > Vytvoření clusteru trvá přibližně 20 minut.

<a name="connect-to-rstudio-server"></a>
## <a name="connect-to-rstudio-server"></a>Připojení k RStudio Serveru

Pokud jste zvolili instalaci RStudio Serveru Community Edition jako součást svého clusteru HDInsight, přístup k přihlašovací stránce RStudia můžete získat jednou z následujících dvou metod:

* **Možnost 1** – Přejděte na následující adresu URL (kde **CLUSTERNAME** je název clusteru služby ML Services, který jste vytvořili):

        https://CLUSTERNAME.azurehdinsight.net/rstudio/

* **Možnost 2** – Otevřete cluster služby ML Services na portálu Azure Portal kliknutím na **Řídicí panely služby ML Services** v části **Rychlé odkazy**.

     ![Nastavení účtu úložiště pro HDInsight](./media/r-server-get-started/dashboard-quick-links.png)

    V části **Řídicí panely R Serveru** klikněte na **R Studio Server**.

    ![Nastavení účtu úložiště pro HDInsight](./media/r-server-get-started/r-studio-server-dashboard.png)

   > [!IMPORTANT]
   > Bez ohledu na použitou metodu bude při prvním přihlášení potřeba dvojí ověření.  Po zobrazení první výzvy k ověření zadejte *ID uživatele* a *heslo* správce clusteru. Po zobrazení druhé výzvy k ověření zadejte *ID uživatele* a *heslo* SSH. Při dalším přihlašování už budete potřebovat jenom přihlašovací údaje SSH.

Po připojení by vaše obrazovka měla vypadat podobně jako na následujícím snímku obrazovky:

![Připojení k RStudiu](./media/r-server-get-started/connect-to-r-studio.png)

## <a name="run-a-sample-job"></a>Spuštění ukázkové úlohy

Můžete odeslat úlohu pomocí funkcí ScaleR. Tady je příklad příkazů použitých ke spuštění úlohy:

    # Set the HDFS (WASB) location of example data.
    bigDataDirRoot <- "/example/data"

    # Create a local folder for storaging data temporarily.
    source <- "/tmp/AirOnTimeCSV2012"
    dir.create(source)

    # Download data to the tmp folder.
    remoteDir <- "https://packages.revolutionanalytics.com/datasets/AirOnTimeCSV2012"
    download.file(file.path(remoteDir, "airOT201201.csv"), file.path(source, "airOT201201.csv"))
    download.file(file.path(remoteDir, "airOT201202.csv"), file.path(source, "airOT201202.csv"))
    download.file(file.path(remoteDir, "airOT201203.csv"), file.path(source, "airOT201203.csv"))
    download.file(file.path(remoteDir, "airOT201204.csv"), file.path(source, "airOT201204.csv"))
    download.file(file.path(remoteDir, "airOT201205.csv"), file.path(source, "airOT201205.csv"))
    download.file(file.path(remoteDir, "airOT201206.csv"), file.path(source, "airOT201206.csv"))
    download.file(file.path(remoteDir, "airOT201207.csv"), file.path(source, "airOT201207.csv"))
    download.file(file.path(remoteDir, "airOT201208.csv"), file.path(source, "airOT201208.csv"))
    download.file(file.path(remoteDir, "airOT201209.csv"), file.path(source, "airOT201209.csv"))
    download.file(file.path(remoteDir, "airOT201210.csv"), file.path(source, "airOT201210.csv"))
    download.file(file.path(remoteDir, "airOT201211.csv"), file.path(source, "airOT201211.csv"))
    download.file(file.path(remoteDir, "airOT201212.csv"), file.path(source, "airOT201212.csv"))

    # Set directory in bigDataDirRoot to load the data.
    inputDir <- file.path(bigDataDirRoot,"AirOnTimeCSV2012")

    # Create the directory.
    rxHadoopMakeDir(inputDir)

    # Copy the data from source to input.
    rxHadoopCopyFromLocal(source, bigDataDirRoot)

    # Define the HDFS (WASB) file system.
    hdfsFS <- RxHdfsFileSystem()

    # Create info list for the airline data.
    airlineColInfo <- list(
    DAY_OF_WEEK = list(type = "factor"),
    ORIGIN = list(type = "factor"),
    DEST = list(type = "factor"),
    DEP_TIME = list(type = "integer"),
    ARR_DEL15 = list(type = "logical"))

    # Get all the column names.
    varNames <- names(airlineColInfo)

    # Define the text data source in HDFS.
    airOnTimeData <- RxTextData(inputDir, colInfo = airlineColInfo, varsToKeep = varNames, fileSystem = hdfsFS)

    # Define the text data source in local system.
    airOnTimeDataLocal <- RxTextData(source, colInfo = airlineColInfo, varsToKeep = varNames)

    # Specify the formula to use.
    formula = "ARR_DEL15 ~ ORIGIN + DAY_OF_WEEK + DEP_TIME + DEST"

    # Define the Spark compute context.
    mySparkCluster <- RxSpark()

    # Set the compute context.
    rxSetComputeContext(mySparkCluster)

    # Run a logistic regression.
    system.time(
        modelSpark <- rxLogit(formula, data = airOnTimeData)
    )

    # Display a summary.
    summary(modelSpark)

<a name="connect-to-edge-node"></a>
## <a name="connect-to-the-cluster-edge-node"></a>Připojení k hraničnímu uzlu clusteru

V této části se dozvíte, jak se připojit k hraničnímu uzlu clusteru HDInsight se službou ML Services pomocí SSH. Informace o použití SSH najdete v tématu [Použití SSH se službou HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

Tady je příkaz SSH pro připojení k hraničnímu uzlu clusteru služby ML Services:

   `ssh USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net`

Příkaz SSH pro váš cluster zjistíte na webu Azure Portal tak, že kliknete na název clusteru, pak na **SSH + přihlašovací údaje clusteru** a jako **Název hostitele** vyberete hraniční uzel. Zobrazí se informace o koncovém bodu SSH pro hraniční uzel.

![Obrázek koncového bodu SSH pro hraniční uzel](./media/r-server-get-started/sshendpoint.png)

Pokud jste použili heslo k zabezpečení uživatelského účtu SSH, zobrazí se výzva k jeho zadání. Pokud jste použili veřejný klíč, bude pravděpodobně muset použít parametr `-i` k určení odpovídajícího privátního klíče. Příklad:

    ssh -i ~/.ssh/id_rsa USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net

Po připojení se dostanete na příkazový řádek, který bude vypadat nějak takto:

    sshuser@ed00-myrclu:~$

<a name="use-r-console"></a>
## <a name="use-the-r-console"></a>Použití konzoly R

1. Z relace SSH pomocí následujícího příkazu spusťte konzolu R:  

        R

2. Měl by se zobrazit výstup s verzí ML Serveru a dalšími informacemi.
    
3. Na příkazovém řádku `>` můžete zadat kód R. Služba ML Services v HDInsight obsahuje balíčky umožňující snadnou interakci se systémem Hadoop a spouštění distribuovaných výpočtů. Například pomocí následujícího příkazu zobrazíte kořen výchozího systému souborů pro cluster HDInsight:

        rxHadoopListFiles("/")

4. Můžete také použít adresování ve stylu WASB.

        rxHadoopListFiles("wasb:///")

5. Pokud chcete konzolu R zavřít, použijte následující příkaz:

        quit()

## <a name="automated-cluster-creation"></a>Automatizované vytváření clusterů

Vytváření clusteru služby ML Services pro HDInsight můžete automatizovat pomocí sady SDK nebo PowerShellu.

<!---* To create an ML Server cluster using an Azure Resource Management template, see [Deploy an R Server for HDInsight cluster](https://azure.microsoft.com/resources/templates/101-hdinsight-rserver/).--->
* Informace o vytvoření clusteru služby ML Services pomocí sady .NET SDK najdete v článku o [vytváření clusterů se systémem Linux v HDInsight pomocí sady .NET SDK](../hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md).
* Informace o vytvoření clusteru služby ML Services pomocí PowerShellu najdete v článku o [vytváření clusterů HDInsight pomocí Azure PowerShellu](../hdinsight-hadoop-create-linux-clusters-azure-powershell.md).

## <a name="delete-the-cluster"></a>Odstranění clusteru

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="troubleshoot"></a>Řešení potíží

Pokud narazíte na problémy s vytvářením clusterů HDInsight, podívejte se na [požadavky na řízení přístupu](../hdinsight-administer-use-portal-linux.md#create-clusters).

## <a name="next-steps"></a>Další kroky

V tomto článku jste zjistili, jak vytvořit nový cluster služby ML Services v Azure HDInsight, a seznámili jste se se základy používání konzoly R z relace SSH. Následující články popisují další způsoby správy a práce se službou ML Services v HDInsight:

* [Odesílání úloh z Nástrojů R pro Visual Studio](r-server-submit-jobs-r-tools-vs.md)
* [Správa clusteru služby ML Services v HDInsight](r-server-hdinsight-manage.md)
* [Zprovoznění clusteru služby ML Services v HDInsight](r-server-operationalize.md)
* [Možnosti výpočetního kontextu pro cluster služby ML Services v HDInsight](r-server-compute-contexts.md)
* [Možnosti Azure Storage pro cluster služby ML Services v HDInsight](r-server-storage.md)
