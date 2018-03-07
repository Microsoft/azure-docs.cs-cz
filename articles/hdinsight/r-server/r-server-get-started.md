---
title: "Začínáme s R Serverem ve službě HDInsight – Azure| Dokumentace Microsoftu"
description: "Naučíte se vytvořit Apache Spark v clusteru HDInsight, který obsahuje R Server, a odeslat do clusteru skript R."
services: HDInsight
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: b5e111f3-c029-436c-ba22-c54a4a3016e3
ms.service: HDInsight
ms.custom: hdinsightactive
ms.devlang: R
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 08/14/2017
ms.author: bradsev
ms.openlocfilehash: aa7f2e6f44036738756391ecaa265c57c093c42c
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/24/2018
---
# <a name="get-started-with-r-server-on-hdinsight"></a>Začínáme s R Serverem ve službě HDInsight

Azure HDInsight zahrnuje možnost integrace R Serveru do clusteru HDInsight. Díky této možnosti můžou skripty R používat Spark a MapReduce ke spouštění distribuovaných výpočtů. V tomto článku se dozvíte, jak vytvořit R Server v clusteru HDInsight. Pak se naučíte, jak spustit skript R, který předvádí použití Sparku k distribuovaným výpočtům jazyka R.


## <a name="prerequisites"></a>Požadavky

* **Předplatné Azure:** Než začnete tento kurz, musíte mít předplatné Azure. Další informace najdete v článku [Získání bezplatné zkušební verze Microsoft Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **Klient Secure Shell (SSH):** Klient SSH slouží k vzdálenému připojení ke clusteru HDInsight a spouštění příkazů přímo v clusteru. Další informace najdete v tématu [Použití SSH se službou HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).
* **Klíče SSH (volitelné):** Účet SSH používaný k připojení ke clusteru můžete zabezpečit pomocí hesla nebo veřejného klíče. Použití hesla je jednodušší a umožňuje začít bez nutnosti vytvářet pár veřejného a privátního klíče. Použití klíče je ale bezpečnější.

  > [!NOTE]
  > Postupy v tomto článku předpokládají použití hesla.


## <a name="automate-cluster-creation"></a>Automatizace vytváření clusterů

Vytváření instancí HDInsight R Serverů můžete automatizovat pomocí šablon Azure Resource Manageru, sady SDK nebo PowerShellu.

* Informace o vytváření instancí R Serveru pomocí šablony Azure Resource Manageru najdete v tématu [Nasazení clusteru HDInsight s R Serverem](https://azure.microsoft.com/resources/templates/101-hdinsight-rserver/).
* Pokud chcete vytvořit instanci R Server pomocí sady .NET SDK, přečtěte si [vytváření clusterů se systémem Linux v HDInsight pomocí sady .NET SDK](../hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md).
* Pokud chcete nasadit R Server pomocí PowerShellu, přečtěte si [vytváření clusterů se systémem Linux v HDInsight pomocí Azure PowerShellu](../hdinsight-hadoop-create-linux-clusters-azure-powershell.md).


<a name="create-hdi-custer-with-aure-portal"></a>
## <a name="create-a-cluster-by-using-the-azure-portal"></a>Vytvoření clusteru pomocí webu Azure Portal

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

2. Vyberte **Nový** > **Inteligentní funkce a analýzy** > **HDInsight**.

    ![Obrázek vytváření nového clusteru](./media/r-server-get-started/newcluster.png)

3. V prostředí **Rychlé vytvoření** zadejte název clusteru do pole **Název clusteru**. Pokud máte více předplatných Azure, v poli **Předplatné** vyberte to, které chcete použít.

    ![Výběr názvu clusteru a předplatného](./media/r-server-get-started/clustername.png)

4. Vyberte **Typ clusteru**. Otevře se podokno **Konfigurace clusteru**. V podokně **Konfigurace clusteru** vyberte následující možnosti:

    * **Typ clusteru:** Vyberte **R Server**.
    * **Verze:** Vyberte verzi R Serveru, která se má v clusteru nainstalovat. Aktuálně dostupná verze je **R Server 9.1 (HDI 3.6)**. Poznámky k dostupným verzím R Serveru jsou k dispozici na webu [docs.microsoft.com](https://docs.microsoft.com/machine-learning-server/whats-new-in-r-server#r-server-91).
    * **Komunitní verze RStudia pro R Server:** Toto integrované vývojové prostředí (IDE) v prohlížeči se ve výchozím nastavení nainstaluje na hraniční uzel. Pokud si jeho instalaci nepřejete, zrušte zaškrtnutí tohoto políčka. Pokud zvolíte jeho instalaci, adresu URL pro přístup k přihlašovací stránce RStudio Serveru najdete po vytvoření clusteru na portálu v podokně aplikace pro váš cluster.
    * Pro další možnosti ponechte výchozí hodnoty a pomocí tlačítka **Vybrat** uložte typ clusteru.

        ![Snímek podokna „Typ clusteru“](./media/r-server-get-started/clustertypeconfig.png)

5. V podokně **Základy** zadejte do polí **Uživatelské jméno pro přihlášení ke clusteru** a **Jméno pro přihlášení ke clusteru** zadat uživatelské jméno a heslo (v tomto pořadí) pro cluster.

6. Do pole **Uživatelské jméno Secure Shell (SSH)** zadejte uživatelské jméno pro SSH. SSH se používá pro vzdálené připojení ke clusteru pomocí klienta SSH. Uživatele SSH můžete zadat v tomto poli, nebo až po vytvoření clusteru (na kartě **Konfigurace** pro cluster).
   
   > [!NOTE] 
   > R Server je nakonfigurovaný, aby očekával uživatelské jméno SSH „remoteuser“. Pokud použijete jiné uživatelské jméno, po vytvoření clusteru budete muset provést dodatečný krok.

7. Ponechte zaškrtnuté políčko **Použít stejné heslo jako pro přihlášení ke clusteru** pro použití typu ověřování **HESLO**, pokud nedáváte přednost použití veřejného klíče. Pokud chcete mít přístup k R Serveru v clusteru přes vzdáleného klienta, jako je R Tools for Visual Studio, RStudio nebo jiné integrované vývojové prostředí (IDE), potřebujete pár veřejného a privátního klíče. Pokud instalujete komunitní verzi RStudio Serveru, musíte zvolit heslo SSH.     

   Pokud chcete vytvořit a používat pár veřejného a privátního klíče, zrušte zaškrtnutí políčka **Použít stejné heslo jako pro přihlášení ke clusteru**. Potom vyberte **VEŘEJNÝ KLÍČ** a pokračujte následujícím způsobem. Tyto pokyny předpokládají, že máte nainstalované prostředí Cygwin, ve kterém je dostupný příkaz ssh-keygen nebo ekvivalentní příkaz.

   a. Vygenerujte na svém přenosném počítači z příkazového řádku pár veřejného a privátního klíče:

        ssh-keygen -t rsa -b 2048

   b. Postupujte na příkazovém řádku podle zobrazených pokynů, pojmenujte soubor klíče a pak zadejte přístupové heslo pro zvýšení zabezpečení. Vaše obrazovka by měla vypadat přibližně jako na následujícím obrázku:

      ![Příkazový řádek SSH v systému Windows](./media/r-server-get-started/sshcmdline.png)

      Tento příkaz vytvoří soubor privátního klíče a soubor veřejného klíče s názvem <název_souboru_privátního_klíče>.pub. V tomto příkladu jde o soubory furiosa a furiosa.pub:

      ![Výsledek příkazu dir v příkladu](./media/r-server-get-started/dir.png)

   c. Soubor veřejného klíče (&#42;.pub) zadejte při přiřazování přihlašovacích údajů clusteru HDI. Potvrďte skupinu prostředků a oblast a vyberte **Další**.

      ![Podokno Přihlašovací údaje](./media/r-server-get-started/publickeyfile.png)  

   d. Změňte oprávnění k souboru privátního klíče na svém počítači:

        chmod 600 <private-key-filename>

   e. Použijte soubor privátního klíče spolu s SSH ke vzdálenému přihlášení:

        ssh –i <private-key-filename> remoteuser@<hostname public ip>

      Nebo použijte privátní klíč jako součást definice výpočetního kontextu Hadoop Spark pro R Server na klientovi. Další informace najdete v článku [Vytvoření výpočetního kontextu pro Spark](https://docs.microsoft.com/machine-learning-server/r/how-to-revoscaler-spark).

8. Funkce rychlého vytvoření vás navede do podokna **Úložiště**. Tady můžete vybrat účet úložiště, který se má použít pro primární umístění systému souborů HDFS používaného clusterem. Vyberte nový nebo existující účet služby Azure Storage nebo existující účet Data Lake Store.

    - Pokud vyberete účet služby Azure Storage, můžete vybrat existující účet tak, že vyberete možnost **Vybrat účet úložiště** a pak vyberete požadovaný účet. Nový účet můžete vytvořit pomocí odkazu **Vytvořit nový** v části **Vybrat účet úložiště**.

      > [!NOTE]
      > Pokud vyberete **Nový**, musíte zadat název pro nový účet úložiště. Pokud je název přijat, zobrazí se zelené zaškrtnutí.

      Pro **Výchozí kontejner** se ve výchozím nastavení použije název clusteru. Ponechte tuto výchozí hodnotu.

      Pokud jste vybrali nový účet úložiště, použijte v dialogu možnost **Umístění** a vyberte pro něj oblast.  

         ![Podokno Zdroj dat](./media/r-server-get-started/datastore.png)  

      > [!IMPORTANT]
      > Výběrem umístění pro výchozí zdroj dat se nastaví také umístění clusteru HDInsight. Cluster i výchozí zdroj dat musí být ve stejné oblasti.

    - Pokud chcete použít existující účet Data Lake Store, vyberte požadovaný účet. Pak přidejte ke clusteru identitu *ADD*, abyste umožnili přístup k úložišti. Další informace o tomto procesu najdete v tématu [Vytvoření clusteru HDInsight se službou Data Lake Store pomocí webu Azure Portal](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-hdinsight-hadoop-use-portal).

    Pomocí tlačítka **Vybrat** uložte konfiguraci zdroje dat.


9. Zobrazí se podokno **Souhrn**, ve kterém můžete zkontrolovat všechna nastavení. Tady můžete změnit velikost clusteru – upravit počet serverů v clusteru. Můžete také zadat akce skriptů, které chcete spustit. Pokud si nejste jisti, že potřebujete větší cluster, ponechte výchozí počet pracovních uzlů, tedy **4**. V podokně jsou také zobrazeny odhadované náklady na provoz clusteru.

    ![Souhrn clusteru](./media/r-server-get-started/clustersummary.png)

   > [!NOTE]
   > V případě potřeby můžete velikost clusteru změnit později na webu Azure Portal (**Cluster** > **Nastavení** > **Škálování clusteru**) a navýšit nebo snížit tak počet pracovních uzlů. Změna velikosti může být užitečná ke snížení kapacity clusteru, když se zrovna nepoužívá, nebo ke zvýšení kapacity pro splnění požadavků větších úloh.
   >
   >

   Mezi faktory, které byste měli vzít v úvahu při nastavování velikosti clusteru, datových uzlů a hraničních uzlů, patří:  

   * Výkon distribuovaných analýz R Serveru ve Sparku je v případě velkých dat úměrný počtu pracovních uzlů.  

   * Výkon analýz R Serveru lineárně klesá s velikostí analyzovaných dat. Příklad:  

     * Pro malá až středně velká data je výkon nejlepší při analýze v místním výpočetním kontextu na hraničním uzlu. Další informace o scénářích, kdy místní výpočetní kontext a výpočetní kontext Spark pracují nejlépe, najdete v článku [Možnosti výpočetního kontextu pro R Server ve službě HDInsight](r-server-compute-contexts.md).<br>
     * Pokud se přihlásíte k hraničnímu uzlu a spustíte svůj skript R, všechny funkce kromě funkcí ScaleR rx se spustí *místně* na hraničním uzlu. Proto by velikost paměti a počet jader na hraničním uzlu měly být nastavené odpovídajícím způsobem. Totéž platí, pokud používáte R Server ve službě HDInsight jako vzdálený výpočetní kontext ze svého přenosného počítače.

   Pomocí tlačítka **Vybrat** uložte konfiguraci cen uzlů.

   ![Podokno s cenovými úrovněmi uzlů](./media/r-server-get-started/pricingtier.png)

   Je uvedený také odkaz na **stažení šablony a parametrů**. Výběrem tohoto odkazu zobrazíte skripty, které můžete použít k automatizaci vytváření clusteru s použitím vybrané konfigurace. Po vytvoření clusteru jsou tyto skripty k dispozici také v položce pro váš cluster na webu Azure Portal.

   > [!NOTE]
   > Vytvoření clusteru nějakou dobu trvá, obvykle přibližně 20 minut. Proces vytváření můžete zkontrolovat v dlaždici na Úvodním panelu nebo v položce **Oznámení** na levé straně stránky.
   >
   >

<a name="connect-to-rstudio-server"></a>
## <a name="connect-to-rstudio-server"></a>Připojení k RStudio Serveru

Pokud jste se rozhodli do instalace zahrnout komunitní verzi RStudio Serveru, přístup k přihlašovací stránce RStudia můžete získat způsoby.

- Přejděte na následující adresu URL (kde *NÁZEV_CLUSTERU* je název clusteru, který jste vytvořili):

    https://*NÁZEV_CLUSTERU*.azurehdinsight.net/rstudio/

- Otevřete položku pro váš cluster na webu Azure Portal, vyberte rychlý odkaz **Řídicí panely R Serveru** a pak vyberte **Řídicí panel RStudia**:

  ![Přístup k řídicímu panelu RStudia](./media/r-server-get-started/rstudiodashboard1.png)

  ![Přístup k řídicímu panelu RStudia](./media/r-server-get-started/rstudiodashboard2.png)

> [!IMPORTANT]
> Bez ohledu na použitou metodu bude při prvním přihlášení potřeba dvojí ověření. Při prvním ověření zadejte ID uživatele a heslo správce clusteru. Až se zobrazí druhá výzva, zadejte ID uživatele a heslo SSH. Další přihlášení už budou vyžadovat jen ID uživatele a heslo pro SSH.

<a name="connect-to-edge-node"></a>
## <a name="connect-to-the-r-server-edge-node"></a>Připojení k hraničnímu uzlu R Serveru

Následujícím příkazem se připojte pomocí SSH k hraničnímu uzlu R Serveru v clusteru HDInsight:

   `ssh USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net`

> [!NOTE]
> IP adresu pro `USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net` najdete na webu Azure Portal. Vyberte cluster a pak vyberte **Všechna nastavení** > **Aplikace** > **RServer**. Zobrazí se informace o koncovém bodu SSH pro hraniční uzel.
>
> ![Obrázek koncového bodu SSH pro hraniční uzel](./media/r-server-get-started/sshendpoint.png)
>
>

Pokud jste použili heslo k zabezpečení uživatelského účtu SSH, zobrazí se výzva k jeho zadání. Pokud jste použili veřejný klíč, budete pravděpodobně muset použít parametr `-i` k určení odpovídajícího privátního klíče. Příklad:

    ssh -i ~/.ssh/id_rsa USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net

Další informace najdete v tématu [Připojení ke službě HDInsight (Hadoop) pomocí SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

Po připojení se dostanete na příkazový řádek, který bude vypadat nějak takto:

    sername@ed00-myrser:~$

<a name="enable-concurrent-users"></a>
## <a name="enable-multiple-concurrent-users"></a>Povolení několika souběžných uživatelů

Několik souběžných uživatelů můžete povolit přidáním dalších uživatelů pro hraniční uzel, na kterém je spuštěna komunitní verze RStudia.

Při vytváření clusteru HDInsight musíte zadat dva uživatele: uživatele HTTP a uživatele SSH:

![Zadání přihlašovacích údajů uživatele clusteru a uživatele SSH](./media/r-server-get-started/concurrent-users-1.png)

- **Uživatelské jméno přihlášení clusteru:** Uživatel HTTP pro ověřování prostřednictvím brány HDInsight, která slouží k ochraně vytvořených clusterů HDInsight. Tento uživatel HTTP slouží k přístupu k uživatelskému rozhraní Ambari, uživatelskému rozhraní YARN a dalším komponentám uživatelského rozhraní.
- **Uživatelské jméno Secure Shell (SSH):** Uživatel SSH pro přístup ke clusteru přes Secure Shell. Tento uživatel je uživatel v systému Linux pro všechny hlavní uzly, pracovní uzly a hraniční uzly. Proto můžete použít SSH pro přístup k jakémukoli uzlu ve vzdáleném clusteru.

Komunitní verze RStudio Serveru použitá v Microsoft R Serveru v clusteru typu HDInsight přijímá jako přihlašovací mechanismus pouze linuxové uživatelské jméno a heslo. Nepodporuje předávání tokenů. Takže pokud jste vytvořili nový cluster a chcete pro přístup k němu používat RStudio, musíte se přihlásit dvakrát.

1. Přihlaste se pomocí přihlašovacích údajů uživatele HTTP prostřednictvím brány HDInsight: 

    ![Zadávání přihlašovacích údajů uživatele HTTP.](./media/r-server-get-started/concurrent-users-2a.png)

2. Pomocí přihlašovacích údajů uživatele SSH se přihlaste k RStudiu:
  
    ![Zadávání přihlašovacích údajů uživatele SSH.](./media/r-server-get-started/concurrent-users-2b.png)

V současné době můžete při zřizování clusteru HDInsight vytvořit pouze jeden uživatelský účet SSH. Takže pokud chcete povolit přístup k Microsoft R Serveru v clusterech HDInsight více uživatelům, je nutné vytvořit další uživatele v systému Linux.

Protože je komunitní verze RStudio Serveru spuštěná na hraničním uzlu clusteru, zahrnuje tento postup tři kroky:

1. přihlášení k hraničnímu uzlu pomocí vytvořeného uživatele SSH,
2. přidání dalších uživatelů Linuxu na hraničním uzlu a
3. Použití komunitní verze RStudia s vytvořeným uživatelem.

### <a name="use-the-created-ssh-user-to-log-in-to-the-edge-node"></a>Přihlášení k hraničnímu uzlu pomocí vytvořeného uživatele SSH

Stáhněte si jakýkoli nástroj SSH (například PuTTY) a přihlaste se s použitím existujícího uživatele SSH. Pak postupujte podle pokynů uvedených v tématu [Připojení ke službě HDInsight (Hadoop) pomocí SSH](../hdinsight-hadoop-linux-use-ssh-unix.md) a získejte přístup k hraničnímu uzlu. Adresa hraničního uzlu pro R Server v clusteru HDInsight je: **název_clusteru-ed-ssh.azurehdinsight.net**


### <a name="add-more-linux-users-to-the-edge-node"></a>Přidání dalších uživatelů Linuxu na hraničním uzlu

Pokud chcete přidat uživatele na hraničním uzlu, spusťte následující příkazy:

    sudo useradd yournewusername -m
    sudo passwd yourusername

Měly by se vrátit následující položky: 

![Výstup příkazů sudo](./media/r-server-get-started/concurrent-users-3.png)

Zobrazenou výzvu „Aktuální heslo protokolu Kerberos:“ ignorujte stisknutím klávesy Enter. Možnost `-m` v příkazu `useradd` označuje, že systém pro uživatele vytvoří domovskou složku. Tuto složku vyžaduje komunitní verze RStudia.


### <a name="use-the-rstudio-community-version-with-the-created-user"></a>Použití komunitní verze RStudia s vytvořeným uživatelem

S použitím vytvořeného uživatele se přihlaste k RStudiu:

![Přihlašovací stránka RStudia](./media/r-server-get-started/concurrent-users-4.png)

Všimněte si, že RStudio označuje, že se ke clusteru přihlašujete pomocí nového uživatele (zde například **sshuser6**): 

![Umístění nového uživatele na panelu příkazů Rstudia](./media/r-server-get-started/concurrent-users-5.png)

Souběžně se můžete z jiného okna prohlížeče přihlásit také pomocí původních přihlašovacích údajů (ve výchozím nastavení to je **sshuser**).

Můžete odeslat úlohu pomocí funkcí ScaleR. Tady je příklad příkazů použitých ke spuštění úlohy:

    # Set the HDFS (Azure Blob storage) location of example data
    bigDataDirRoot <- "/example/data"

    # Create a local folder for storing data temporarily
    source <- "/tmp/AirOnTimeCSV2012"
    dir.create(source)

    # Download data to the tmp folder
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

    # Set the directory in bigDataDirRoot to load the data
    inputDir <- file.path(bigDataDirRoot,"AirOnTimeCSV2012")

    # Create the directory
    rxHadoopMakeDir(inputDir)

    # Copy the data from source to input
    rxHadoopCopyFromLocal(source, bigDataDirRoot)

    # Define the HDFS (Blob storage) file system
    hdfsFS <- RxHdfsFileSystem()

    # Create an info list for the airline data
    airlineColInfo <- list(
    DAY_OF_WEEK = list(type = "factor"),
    ORIGIN = list(type = "factor"),
    DEST = list(type = "factor"),
    DEP_TIME = list(type = "integer"),
    ARR_DEL15 = list(type = "logical"))

    # Get all the column names
    varNames <- names(airlineColInfo)

    # Define the text data source in HDFS
    airOnTimeData <- RxTextData(inputDir, colInfo = airlineColInfo, varsToKeep = varNames, fileSystem = hdfsFS)

    # Define the text data source in the local system
    airOnTimeDataLocal <- RxTextData(source, colInfo = airlineColInfo, varsToKeep = varNames)

    # Specify the formula to use
    formula = "ARR_DEL15 ~ ORIGIN + DAY_OF_WEEK + DEP_TIME + DEST"

    # Define the Spark compute context
    mySparkCluster <- RxSpark()

    # Set the compute context
    rxSetComputeContext(mySparkCluster)

    # Run a logistic regression
    system.time(
        modelSpark <- rxLogit(formula, data = airOnTimeData)
    )

    # Display a summary
    summary(modelSpark)


Všimněte si, že odeslané úlohy jsou v uživatelském rozhraní YARN uvedeny pod různými uživatelskými jmény:

![Seznam uživatelů v uživatelském rozhraní YARN](./media/r-server-get-started/concurrent-users-6.png)

Všimněte si také, že nově přidaní uživatelé nemají v Linuxu oprávnění root. Mají ale stejný přístup ke všem souborům ve vzdáleném systému souborů HDFS a úložišti objektů Blob.


<a name="use-r-console"></a>
## <a name="use-the-r-console"></a>Použití konzoly R

1. Z relace SSH pomocí následujícího příkazu spusťte konzolu R:  

        R

2. Zobrazený výstup by měl vypadat přibližně takto:
    
        R version 3.2.2 (2015-08-14) -- "Fire Safety"
        Copyright (C) 2015 The R Foundation for Statistical Computing
        Platform: x86_64-pc-linux-gnu (64-bit)

        R is free software and comes with ABSOLUTELY NO WARRANTY.
        You are welcome to redistribute it under certain conditions.
        Type 'license()' or 'licence()' for distribution details.

        Natural language support but running in an English locale

        R is a collaborative project with many contributors.
        Type 'contributors()' for more information and
        'citation()' on how to cite R or R packages in publications.

        Type 'demo()' for some demos, 'help()' for on-line help, or
        'help.start()' for an HTML browser interface to help.
        Type 'q()' to quit R.

        Microsoft R Server version 8.0: an enhanced distribution of R
        Microsoft packages Copyright (C) 2016 Microsoft Corporation

        Type 'readme()' for release notes.
        >

3. Na příkazovém řádku `>` můžete zadat kód R. R Server obsahuje balíčky umožňující snadnou interakci se systémem Hadoop a spouštění distribuovaných výpočtů. Například pomocí následujícího příkazu zobrazíte kořen výchozího systému souborů pro cluster HDInsight:

        rxHadoopListFiles("/")

4. Můžete také použít adresování ve stylu úložiště objektů Blob:

        rxHadoopListFiles("wasb:///")


## <a name="use-r-server-on-hdi-from-a-remote-instance-of-microsoft-r-server-or-microsoft-r-client"></a>Použití R Serveru ve službě HDInsight ze vzdálené instance Microsoft R Serveru nebo klienta Microsoft R Client

Můžete nastavit přístup k výpočetnímu kontextu HDInsight Hadoop Spark ze vzdálené instance Microsoft R Serveru nebo klienta Microsoft R Client spuštěné na počítači nebo přenosném počítači. Další informace viz část „Použití Microsoft R Serveru jako klienta Hadoop“ v článku [Vytvoření výpočetního kontextu pro Spark](https://docs.microsoft.com/machine-learning-server/r/how-to-revoscaler-spark#more-spark-scenarios). Abyste to mohli provést, při definování výpočetního kontextu RxSpark na svém přenosném počítači zadejte následující možnosti: hdfsShareDir, shareDir, sshUsername, sshHostname, sshSwitches a sshProfileScript. Tady je příklad:


    myNameNode <- "default"
    myPort <- 0

    mySshHostname  <- 'rkrrehdi1-ed-ssh.azurehdinsight.net'  # HDI secure shell hostname
    mySshUsername  <- 'remoteuser'# HDI SSH username
    mySshSwitches  <- '-i /cygdrive/c/Data/R/davec'   # HDI SSH private key

    myhdfsShareDir <- paste("/user/RevoShare", mySshUsername, sep="/")
    myShareDir <- paste("/var/RevoShare" , mySshUsername, sep="/")

    mySparkCluster <- RxSpark(
      hdfsShareDir = myhdfsShareDir,
      shareDir     = myShareDir,
      sshUsername  = mySshUsername,
      sshHostname  = mySshHostname,
      sshSwitches  = mySshSwitches,
      sshProfileScript = '/etc/profile',
      nameNode     = myNameNode,
      port         = myPort,
      consoleOutput= TRUE
    )


## <a name="use-a-compute-context"></a>Použití výpočetního kontextu

Výpočetní kontext vám umožňuje řídit, jestli se výpočty provádějí místně na hraničním uzlu, nebo jsou distribuovány napříč uzly v clusteru HDInsight.

1. V RStudio Serveru nebo konzole R (v rámci relace SSH) pomocí následujícího kódu načtěte ukázková data do výchozího úložiště pro službu HDInsight:

        # Set the HDFS (Blob storage) location of example data
        bigDataDirRoot <- "/example/data"

        # Create a local folder for storing data temporarily
        source <- "/tmp/AirOnTimeCSV2012"
        dir.create(source)

        # Download data to the tmp folder
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

        # Set the directory in bigDataDirRoot to load the data into
        inputDir <- file.path(bigDataDirRoot,"AirOnTimeCSV2012")

        # Make the directory
        rxHadoopMakeDir(inputDir)

        # Copy the data from source to input
        rxHadoopCopyFromLocal(source, bigDataDirRoot)

2. Vytvořte nějaké informace o datech a nadefinujte dva zdroje dat, abyste s daty mohli pracovat:

        # Define the HDFS (Blob storage) file system
        hdfsFS <- RxHdfsFileSystem()

        # Create an info list for the airline data
        airlineColInfo <- list(
             DAY_OF_WEEK = list(type = "factor"),
             ORIGIN = list(type = "factor"),
             DEST = list(type = "factor"),
             DEP_TIME = list(type = "integer"),
             ARR_DEL15 = list(type = "logical"))

        # Get all the column names
        varNames <- names(airlineColInfo)

        # Define the text data source in HDFS
        airOnTimeData <- RxTextData(inputDir, colInfo = airlineColInfo, varsToKeep = varNames, fileSystem = hdfsFS)

        # Define the text data source in the local system
        airOnTimeDataLocal <- RxTextData(source, colInfo = airlineColInfo, varsToKeep = varNames)

        # Formula to use
        formula = "ARR_DEL15 ~ ORIGIN + DAY_OF_WEEK + DEP_TIME + DEST"

3. Pak na nad daty spusťte logistickou regresi využívající místní výpočetní kontext:

        # Set a local compute context
        rxSetComputeContext("local")

        # Run a logistic regression
        system.time(
           modelLocal <- rxLogit(formula, data = airOnTimeDataLocal)
        )

        # Display a summary
        summary(modelLocal)

    Zobrazený výstup by měl končit řádky, které vypadají přibližně takto:

        Data: airOnTimeDataLocal (RxTextData Data Source)
        File name: /tmp/AirOnTimeCSV2012
        Dependent variable(s): ARR_DEL15
        Total independent variables: 634 (Including number dropped: 3)
        Number of valid observations: 6005381
        Number of missing observations: 91381
        -2*LogLikelihood: 5143814.1504 (Residual deviance on 6004750 degrees of freedom)

        Coefficients:
                         Estimate Std. Error z value Pr(>|z|)
         (Intercept)   -3.370e+00  1.051e+00  -3.208  0.00134 **
         ORIGIN=JFK     4.549e-01  7.915e-01   0.575  0.56548
         ORIGIN=LAX     5.265e-01  7.915e-01   0.665  0.50590
         ......
         DEST=SHD       5.975e-01  9.371e-01   0.638  0.52377
         DEST=TTN       4.563e-01  9.520e-01   0.479  0.63172
         DEST=LAR      -1.270e+00  7.575e-01  -1.676  0.09364 .
         DEST=BPT         Dropped    Dropped Dropped  Dropped

         ---

         Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1

         Condition number of final variance-covariance matrix: 11904202
         Number of iterations: 7

4. Nakonec spusťte stejnou logistickou regresi s použitím kontextu Spark. Kontext Spark distribuuje zpracování do všech pracovních uzlů v clusteru HDInsight.

        # Define the Spark compute context
        mySparkCluster <- RxSpark()

        # Set the compute context
        rxSetComputeContext(mySparkCluster)

        # Run a logistic regression
        system.time(  
           modelSpark <- rxLogit(formula, data = airOnTimeData)
        )
        
        # Display a summary
        summary(modelSpark)


   > [!NOTE]
   > Distribuovat výpočty napříč uzly clusteru můžete také pomocí MapReduce. Další informace o výpočetním kontextu najdete v tématu [Možnosti výpočetního kontextu pro R Server ve službě HDInsight](r-server-compute-contexts.md).


## <a name="distribute-r-code-to-multiple-nodes"></a>Distribuování kódu R do více uzlů

S R Serverem můžete snadno vzít existující kód R a spustit ho napříč několika uzly v clusteru pomocí příkazu `rxExec`. Tato funkce je užitečná při úklidu parametrů nebo provádění simulací. Následující kód je příklad použití příkazu `rxExec`:

    rxExec( function() {Sys.info()["nodename"]}, timesToRun = 4 )

Pokud stále používáte kontext Spark nebo MapReduce, tento příkaz vrátí hodnotu `nodename` (název uzlu) všech pracovních uzlů, ve kterých je spuštěný kód `(Sys.info()["nodename"])`. Například v clusteru se čtyřmi uzly by se měl zobrazit výstup podobný následujícímu:

    $rxElem1
        nodename
    "wn3-myrser"

    $rxElem2
        nodename
    "wn0-myrser"

    $rxElem3
        nodename
    "wn3-myrser"

    $rxElem4
        nodename
    "wn3-myrser"


## <a name="access-data-in-hive-and-parquet"></a>Přístup k datům v Hive a Parquet

Funkce, která je k dispozici v R Serveru 9.1, umožňuje přímý přístup k datům v Hive a Parquet pro použití ve funkcích ScaleR ve výpočetním kontextu Spark. Tyto možnosti jsou dostupné prostřednictvím nových funkcí zdroje dat ScaleR s názvem RxHiveData a RxParquetData. Tyto funkce pomocí Spark SQL načtou data přímo do struktury DataFrame ve Sparku, aby je ScaleR mohl analyzovat.  

Následující kód nabízí několik příkladů použití těchto nových funkcí:

    #Create a Spark compute context
    myHadoopCluster <- rxSparkConnect(reset = TRUE)

    #Retrieve some sample data from Hive and run a model
    hiveData <- RxHiveData("select * from hivesampletable",
                     colInfo = list(devicemake = list(type = "factor")))
    rxGetInfo(hiveData, getVarInfo = TRUE)

    rxLinMod(querydwelltime ~ devicemake, data=hiveData)

    #Retrieve some sample data from Parquet and run a model
    rxHadoopMakeDir('/share')
    rxHadoopCopyFromLocal(file.path(rxGetOption('sampleDataDir'), 'claimsParquet/'), '/share/')
    pqData <- RxParquetData('/share/claimsParquet',
                     colInfo = list(
                age    = list(type = "factor"),
               car.age = list(type = "factor"),
                  type = list(type = "factor")
             ) )
    rxGetInfo(pqData, getVarInfo = TRUE)

    rxNaiveBayes(type ~ age + cost, data = pqData)

    #Check on Spark data objects, clean up, and close the Spark session
    lsObj <- rxSparkListData() #Two data objects are cached
    lsObj
    rxSparkRemoveData(lsObj)
    rxSparkListData() #It should show an empty list
    rxSparkDisconnect(myHadoopCluster)


Další informace o těchto nových funkcích najdete v online nápovědě v R Serveru pomocí příkazů `?RxHivedata` a `?RxParquetData`.  


## <a name="install-additional-r-packages-on-the-edge-node"></a>Instalace dalších balíčků R na hraničním uzlu

Chcete-li nainstalovat na hraniční uzel další balíčky, můžete přímo v konzole R použít příkaz `install.packages()`, zatímco jste k hraničnímu uzlu připojeni přes SSH. Pokud však potřebujete balíčky R nainstalovat na pracovní uzly clusteru, musíte použít akci skriptu.

Akce skriptů jsou skripty Bash, které se používají k provádění změn konfigurace clusteru HDInsight nebo k instalaci dalšího softwaru, jako jsou například další balíčky R. Pokud chcete nainstalovat další balíčky pomocí akce skriptu, použijte následující postup.

> [!IMPORTANT]
> Instalace dalších balíčků R pomocí akcí skriptů je možná až po vytvoření clusteru. Nepoužívejte tento postup během vytváření clusteru, protože skript se spoléhá na úplnou instalaci a konfiguraci R Serveru.
>
>

1. Na webu [Azure Portal](https://portal.azure.com) vyberte svůj R Server v clusteru HDInsight.

2. V podokně **Nastavení** vyberte **Akce skriptů** > **Odeslat novou**.

   ![Obrázek podokna Akce skriptů](./media/r-server-get-started/scriptaction.png)

3. V podokně **Odeslat akci skriptu** zadejte následující informace:

   * **Název:** Popisný název pro identifikaci tohoto skriptu.

   * **Identifikátor URI skriptu Bash:**`http://mrsactionscripts.blob.core.windows.net/rpackages-v01/InstallRPackages.sh`

   * **Hlavní:** Tato položka by měla zůstat nezaškrtnutá.

   * **Pracovní:** Tato položka by měla zůstat nezaškrtnutá.

   * **Zookeeper:** Tato položka by měla zůstat nezaškrtnutá.

   * **Hraniční uzly:** Tato položka by měla zůstat nezaškrtnutá.

   * **Parametry:** Balíčky R určené k instalaci – například `bitops stringr arules`.

   * **Zachovat tento skript...:** Tato položka by měla být vybraná.  

   > [!NOTE]
   > Ve výchozím nastavení se všechny balíčky R instalují ze snímku úložiště Microsoft R Application Network, který je konzistentní s nainstalovanou verzí R Serveru. Pokud nainstalujete novější verze balíčků, vystavujete se riziku nekompatibility. Tento typ instalace však můžete provést zadáním `useCRAN` jako prvního prvku seznamu balíčků, například `useCRAN bitops, stringr, arules`.  
   > 
   > Některé balíčky R vyžadují další linuxové systémové knihovny. Pro usnadnění práce jsme předinstalovali potřebné závislosti pro 100 nejoblíbenějších balíčků R. Pokud ale balíčky R, které instalujete, vyžadují ještě další knihovny, budete si muset stáhnout základní skript, které zde používáme, a přidat další kroky k instalaci příslušných systémových knihoven. Upravený skript je pak nutné odeslat do veřejného kontejneru objektů blob v úložišti Azure Storage a k instalaci balíčků použít upravený skript.
   >
   > Další informace o vývoji akcí skriptů najdete v tématu [Vývoj akcí skriptů](../hdinsight-hadoop-script-actions-linux.md).  
   >
   >

   ![Přidání akce skriptu](./media/r-server-get-started/submitscriptaction.png)

4. Vyberte **Vytvořit** a spusťte skript. Po dokončení skriptu jsou balíčky R k dispozici na všech pracovních uzlech.


## <a name="configure-microsoft-r-server-operationalization"></a>Konfigurace operacionalizace Microsoft R Serveru

Po dokončení modelování vašich dat můžete model operacionalizovat za účelem provádění předpovědí. Pokud chcete nakonfigurovat operacionalizaci Microsoft R Serveru, proveďte následující kroky:

1. Použijte příkaz `ssh` na hraničním uzlu – například: 

       ssh -L USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net

2. Přejděte do adresáře příslušné verze a pomocí příkazu `sudo dotnet` spusťte soubor .dll. 

   Pro Microsoft R Server 9.1:

       cd /usr/lib64/microsoft-r/rserver/o16n/9.1.0
       sudo dotnet Microsoft.RServer.Utils.AdminUtil/Microsoft.RServer.Utils.AdminUtil.dll

   Pro Microsoft R Server 9.0:

       cd /usr/lib64/microsoft-deployr/9.0.1
       sudo dotnet Microsoft.DeployR.Utils.AdminUtil/Microsoft.DeployR.Utils.AdminUtil.dll

3. Pokud chcete nakonfigurovat operacionalizaci Microsoft R Serveru s použitím jednotné konfigurace, postupujte následovně:

   a. Vyberte `Configure R Server for Operationalization`.

   b. Vyberte `A. One-box (web + compute nodes)`.

   c. Zadejte heslo pro uživatele `admin`.

   ![Jednotná operacionalizace](./media/r-server-get-started/admin-util-one-box-.png)

4. Volitelně můžete spustit diagnostický test následujícím způsobem:

   a. Vyberte `6. Run diagnostic tests`.

   b. Vyberte `A. Test configuration`.

   c. Jako uživatelské jméno zadejte `admin` a pak zadejte heslo z předchozího kroku konfigurace.

   d. Potvrďte `Overall Health = pass`.

   e. Ukončete nástroj pro správu.

   f. Ukončete připojení SSH.

   ![Diagnostika operacionalizace](./media/r-server-get-started/admin-util-diagnostics.png)


>[!NOTE]
>Pokud dochází k dlouhým zpožděním při pokusech o využívání webové služby vytvořené pomocí funkcí mrsdeploy ve výpočetním kontextu Sparku, možná budete muset přidat některé chybějící složky. Pokaždé, když je aplikace Spark vyvolaná z webové služby pomocí funkcí mrsdeploy, patří uživateli *rserve2*. Náhradní řešení tohoto problému:

    #Create these required folders for user rserve2 in local and HDFS
    hadoop fs -mkdir /user/RevoShare/rserve2
    hadoop fs -chmod 777 /user/RevoShare/rserve2

    mkdir /var/RevoShare/rserve2
    chmod 777 /var/RevoShare/rserve2


    #Create a new Spark compute context 
    rxSparkConnect(reset = TRUE)


V této fázi je konfigurace operacionalizace dokončena. Nyní můžete použít balíček mrsdeploy na klientovi RClient pro připojení k operacionalizaci na hraničním uzlu. Pak můžete začít používat jeho funkce, jako je třeba [vzdálené spuštění](https://docs.microsoft.com/machine-learning-server/r/how-to-execute-code-remotely) a [webové služby](https://docs.microsoft.com/machine-learning-server/operationalize/concept-what-are-web-services). V závislosti na tom, jestli je váš cluster nastavený ve virtuální síti, může být potřeba nastavit přesměrování portu tunelovým propojením přes přihlášení SSH.

### <a name="r-server-cluster-on-a-virtual-network"></a>Cluster R Serveru ve virtuální síti

Ověřte, že je na hraničním uzlu povolený provoz přes port 12800. Tímto způsobem můžete hraniční uzel použít pro připojení k funkci operacionalizace.


    library(mrsdeploy)

    remoteLogin(
        deployr_endpoint = "http://[your-cluster-name]-ed-ssh.azurehdinsight.net:12800",
        username = "admin",
        password = "xxxxxxx"
    )


Pokud se `remoteLogin()` nemůže připojit k hraničnímu uzlu, ale můžete se k němu připojit přes SSH, budete muset ověřit, jestli je správně nastavené pravidlo pro povolení provozu na portu 12800. Pokud bude tento problém přetrvávat, můžete jako alternativní řešení použít nastavení přesměrování portu tunelovým propojením přes SSH. Pokyny najdete v následující části.

### <a name="r-server-cluster-not-set-up-on-a-virtual-network"></a>Cluster R Serveru vytvořený mimo virtuální síť

Pokud váš cluster není vytvořený ve virtuální síti nebo pokud máte potíže s připojením přes virtuální síť, můžete použít přesměrování portu tunelovým propojením přes SSH:

    ssh -L localhost:12800:localhost:12800 USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net

Můžete ho taky vytvořit v PuTTY:

![Připojení SSH pomocí PuTTY](./media/r-server-get-started/putty.png)

Jakmile máte aktivní relaci SSH, provoz z portu 12800 vašeho počítače se prostřednictvím relace SSH přesměruje na port 12800 hraničního uzlu. Nezapomeňte v metodě `remoteLogin()` použít adresu `127.0.0.1:12800`. Tato metoda vyvolá přihlášení k operacionalizaci hraničního uzlu prostřednictvím přesměrování portu.


    library(mrsdeploy)

    remoteLogin(
        deployr_endpoint = "http://127.0.0.1:12800",
        username = "admin",
        password = "xxxxxxx"
    )


## <a name="scale-microsoft-r-server-operationalization-compute-nodes-on-hdinsight-worker-nodes"></a>Škálování výpočetních uzlů operacionalizace Microsoft R Serveru na pracovní uzly HDInsight

### <a name="decommission-the-worker-nodes"></a>Vyřazení pracovních uzlů z provozu

Microsoft R Server v současné době není spravován přes YARN. Pokud se pracovní uzly nevyřadí z provozu, YARN ResourceManager nebude fungovat podle očekávání, protože nebude vědět o prostředcích, které server využívá. Abyste této situaci zabránili, doporučujeme před horizontálním navýšením kapacity výpočetních uzlů vyřadit z provozu pracovní uzly.

Vyřazení pracovních uzlů z provozu:

1. Přihlaste se ke konzole Ambari clusteru HDInsight a vyberte kartu **Hosts** (hostitelé).
2. Vyberte pracovní uzly určené k vyřazení z provozu a pak vyberte **Actions** (Akce)  > **Selected Hosts** (Vybraní hostitelé)  > **Hosts (Hostitelé)**  > **Turn ON Maintenance Mode** (Zapnout režim údržby). Například na následujícím obrázku jsme vybrali k vyřazení z provozu uzly wn3 a wn4.  

   ![Snímek obrazovky příkazů pro zapnutí režimu údržby](./media/r-server-get-started/get-started-operationalization.png)  

3. Vyberte **Actions** (Akce)  > **Selected Hosts** (Vybraní hostitelé)  > **DataNodes** (Datové uzly)  > **Decommission** (Vyřadit z provozu).
4. Vyberte **Actions** (Akce)  > **Selected Hosts** (Vybraní hostitelé)  > **NodeManagers** (Správci uzlů)  > **Decommission** (Vyřadit z provozu).
5. Vyberte **Actions** (Akce)  > **Selected Hosts** (Vybraní hostitelé)  > **DataNodes** (Datové uzly)  > **Stop** (Zastavit).
6. Vyberte **Actions** (Akce)  > **Selected Hosts** (Vybraní hostitelé)  > **NodeManagers** (Správci uzlů)  > **Stop** (Zastavit).
7. Vyberte **Actions** (Akce) > **Selected Hosts** (Vybraní hostitelé) > **Hosts** (Hostitelé)  > **Stop All Components** (Zastavit všechny komponenty).
8. Zrušte výběr pracovních uzlů a vyberte hlavní uzly.
9. Vyberte **Actions** (Akce) > **Selected Hosts** (Vybraní hostitelé) > **Hosts** (Hostitelé)  > **Restart All Components** (Restartovat všechny komponenty).

### <a name="configure-compute-nodes-on-each-decommissioned-worker-node"></a>Konfigurace výpočetních uzlů na každém z vyřazených pracovních uzlů

1. Pomocí SSH se připojte ke každému vyřazenému pracovnímu uzlu.
2. Spusťte nástroj pro správu pomocí příkazu `dotnet /usr/lib64/microsoft-deployr/9.0.1/Microsoft.DeployR.Utils.AdminUtil/Microsoft.DeployR.Utils.AdminUtil.dll`.
3. Zadáním `1` vyberte možnost `Configure R Server for Operationalization`.
4. Zadáním `c` vyberte možnost `C. Compute node`. Tento krok na pracovním uzlu nakonfiguruje výpočetní uzel.
5. Ukončete nástroj pro správu.

### <a name="add-compute-nodes-details-on-the-web-node"></a>Přidání podrobností o výpočetních uzlech do webového uzlu

Jakmile budou všechny vyřazené pracovní uzly nakonfigurované tak, aby běžely na výpočetním uzlu, přejděte zpět do hraničního uzlu a přidejte IP adresy vyřazených pracovních uzlů do konfigurace webového uzlu Microsoft R Serveru:

1. Připojení k hraničnímu uzlu pomocí SSH
2. Spusťte `vi /usr/lib64/microsoft-deployr/9.0.1/Microsoft.DeployR.Server.WebAPI/appsettings.json`.
3. Vyhledejte část `URIs` (Identifikátory URI) a přidejte IP adresy a porty pracovních uzlů.

    ![Příkazový řádek pro hraniční uzel](./media/r-server-get-started/get-started-op-cmd.png)


## <a name="troubleshoot"></a>Řešení potíží

Pokud narazíte na problémy s vytvářením clusterů HDInsight, podívejte se na [požadavky na řízení přístupu](../hdinsight-administer-use-portal-linux.md#create-clusters).


## <a name="next-steps"></a>Další kroky

Nyní byste rozumět tomu, jak vytvořit cluster služby HDInsight s R Serverem. Měli byste mít také přehled o základech používání konzoly R přes SSH. Následující témata popisují další způsoby správy a práce s R Serverem ve službě HDInsight:

* [Možnosti výpočetního kontextu pro R Server ve službě HDInsight](r-server-compute-contexts.md)
* [Možnosti služby Azure Storage pro R Server ve službě HDInsight](r-server-storage.md)
