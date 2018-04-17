---
title: 10 způsobů, jak na datové vědě virtuálního počítače v Azure | Microsoft Docs
description: Proveďte různé zkoumání dat a modelování úloh na vědecké zpracování dat virtuálního počítače.
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
editor: cgronlun
ms.assetid: 145dfe3e-2bd2-478f-9b6e-99d97d789c62
ms.service: machine-learning
ms.component: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/04/2017
ms.author: gokuma
ms.openlocfilehash: 09c48b92e39aa347c97d42358b50c338851fa59b
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="ten-things-you-can-do-on-the-data-science-virtual-machine"></a>Deset věcí, které můžete provádět na virtuálních počítačích pro vědecké zkoumání dat

Virtuální počítač pro vědecké účely dat společnosti Microsoft (DSVM) je výkonný data vědecké účely vývojového prostředí, které umožňuje provádět různé úlohy zkoumání a modelování data. Prostředí obsahuje již vytvořené a připojené několik oblíbených data analýzy nástroje, které usnadňují rychle začít používat analýzy pro místní Cloud a hybridní nasazení. DSVM úzce spolupracuje s řadou služeb Azure a se bude moct číst a zpracovávat data, která je již uložen v Azure, Azure SQL Data Warehouse, Azure Storage a Azure Data Lake nebo v Azure Cosmos DB. Můžou taky jiných analytics nástrojů, například Azure Machine Learning a Azure Data Factory.

V tomto článku se dozvíte, jak používat vaše DSVM k provádění různých úloh datové vědy a komunikovat s jinými službami Azure. Tady jsou některé z akcí, které můžete provést jak na DSVM:

1. Prozkoumejte data a vyvíjet modely místně na DSVM použití serveru Microsoft ML, Python
2. Pomocí poznámkového bloku Jupyter a experimentovat s daty v prohlížeči pomocí R Python 2, Python 3, Microsoft enterprise připravené verze R určený pro výkon
3. Nasazení modely vytvářeny pomocí R a Python v Azure Machine Learning, tak klientské aplikace mají přístup k vaší modelů pomocí jednoduchého webového rozhraní služby
4. Správa prostředků Azure pomocí portálu Azure nebo prostředí Powershell
5. Rozšíření prostor úložiště a sdílet rozsáhlých datových sad / kódu napříč celý tým vytvoření Azure File storage jako připojit jednotku ve vašem DSVM
6. Sdílet kódu se svým týmem pomocí Githubu a přístup k vaší úložiště pomocí předem nainstalovaná klienti Git - Git Bash a Git grafickým uživatelským rozhraním.
7. Přístup k různým Azure data a analýzy službám jako úložiště objektů blob v Azure, Azure Data Lake, Azure HDInsight (Hadoop), Cosmos databáze Azure, Azure SQL Data Warehouse & databáze
8. Vytvářejte sestavy a řídicí panel pomocí Power BI Desktop předinstalované na DSVM a nasadit je do cloudu
9. Dynamické škálování vašeho DSVM potřebám vašeho projektu
10. Nainstalujte další nástroje na virtuálním počítači   

> [!NOTE]
> Další využití poplatky pro mnoho dalších datové úložiště a analýzy služby uvedené v tomto článku. Odkazovat [Azure – ceny](https://azure.microsoft.com/pricing/) stránce Podrobnosti.
> 
> 

**Požadavky**

* Budete potřebovat předplatné Azure. Můžete si zaregistrovat bezplatnou zkušební verzi [zde](https://azure.microsoft.com/free/).
* Pokyny pro zřízení virtuálního počítače vědecké účely dat na portálu Azure jsou k dispozici v [vytvoření virtuálního počítače](https://portal.azure.com/#create/microsoft-ads.standard-data-science-vmstandard-data-science-vm).

## <a name="1-explore-data-and-develop-models-using-microsoft-ml-server-or-python"></a>1. Prozkoumejte data a vývoj modelů pomocí Microsoft ML Server nebo Python
Jazyky jako R a Python můžete provádět analýzy vaše data na DSVM vpravo.

Pro R můžete použít rozhraní IDE, jako Rstudia, který se nachází v nabídce start nebo plochy nebo R nástrojů pro Visual Studio. Společnost Microsoft poskytuje další knihovny nad Open zdroj/CRAN-R umožnit škálovatelný analýzy a schopnost analyzovat data větší než velikost paměti, který je povolená díky paralelní bloku analýzy. 

Pro jazyk Python můžete použít rozhraní IDE, jako Visual Studio Community Edition, který má nástroje Python Tools pro Visual Studio (PTVS) rozšíření předinstalován. Ve výchozím nastavení pouze Python 3.6 prostředí conda kořenové je nakonfigurován na PTVS. Pokud chcete povolit Anaconda Python 2.7, budete muset provést následující kroky:

* Vytvoření vlastního prostředí pro každou verzi přechodem na **nástroje** -> **Python Tools** -> **prostředí Python** a potom kliknutím na možnost " **+ Vlastní**"v aplikaci Visual Studio 2015 Community Edition
* Zadejte popis a nastavení cesty předponu prostředí jako *c:\anaconda\envs\python2* pro Anaconda Python 2.7
* Klikněte na tlačítko **automatické rozpoznání** a potom **použít** uložit prostředí.

Zde je, jak nastavení vlastní prostředí vypadá v sadě Visual Studio.

![Instalační program PTVS](./media/vm-do-ten-things/PTVSSetup.png)

Najdete v článku [dokumentaci k těmto nástrojům](https://github.com/Microsoft/PTVS/wiki/Selecting-and-Installing-Python-Interpreters#hey-i-already-have-an-interpreter-on-my-machine-but-ptvs-doesnt-seem-to-know-about-it) další podrobnosti o tom, jak vytvořit prostředí Python.

Nyní jsou nastavení k vytvoření nového projektu Python. Přejděte na **soubor** -> **nový** -> **projektu** -> **Python** a vyberte typ Aplikace Python, které vytváříte. Prostředí Python pro aktuální projekt můžete nastavit na požadovanou verzi (Anaconda 2.7 nebo 3.6) kliknutím pravým tlačítkem **prostředí Python**, vyberete **prostředí Python přidat nebo odebrat**a potom výběr na požadované prostředí. Můžete najít další informace o práci s PTVS na produktu [dokumentace](https://github.com/Microsoft/PTVS/wiki) stránky.

## <a name="2-using-a-jupyter-notebook-to-explore-and-model-your-data-with-python-or-r"></a>2. Pomocí poznámkového bloku Jupyter a prozkoumejte modelování svá data pomocí Python nebo R
Poznámkového bloku Jupyter je výkonný prostředí, který poskytuje založené na prohlížeči "IDE" pro zkoumání dat a modelování. Můžete použít Python 2, Python 3 nebo R (Open Source a serveru Microsoft R) v poznámkového bloku Jupyter.

Ke spuštění poznámkového bloku Jupyter, klikněte na ikonu nabídky start / ikony na ploše s názvem **Poznámkový blok Jupyter**. Na příkazovém řádku DSVM můžete také spustit příkaz ```jupyter notebook``` z adresáře, kde máte existující poznámkových bloků, nebo chcete vytvořit nový poznámkových bloků.  

Jakmile jste spustili Jupyter, měli byste vidět adresář, který obsahuje pár příklad poznámkových bloků, které jsou předem zabalené do DSVM. Nyní můžete:

* klikněte v poznámkovém bloku zobrazit kód.
* Spusťte jednotlivých buněk tak, že stisknete **zadejte SHIFT**.
* Kliknutím na spustit celý poznámkový blok **buňky** -> **spustit**
* Kliknutím na ikonu Jupyter (levém horním rohu) a potom kliknutím na vytvořit nový poznámkový blok **nový** tlačítko vpravo a pak vyberete poznámkového bloku jazyka (také označované jako jádra).   

> [!NOTE]
> Aktuálně jsou podporovány Python 2.7, Python 3.6, R, Dita a PySpark jádra v Jupyter. Jádro R podporuje programování v Open source R jak původce Microsoft R.   
> 
> 

Jakmile jsou v poznámkovém bloku, které můžete prozkoumat vaše data, sestavení modelu, otestování modelu pomocí vybraného knihovny.

## <a name="3-build-models-using-r-or-python-and-operationalize-them-using-azure-machine-learning"></a>3. Vytvářet modely R nebo Python a Operationalize jejich používání pomocí Azure Machine Learning
Jakmile máte vytvořené a ověřit váš model dalším krokem je obvykle k nasazení do produkčního prostředí. To umožňuje aplikacím vyvolání předpovědi modelu v reálném čase nebo na základě režimu dávky vašeho klienta. Azure Machine Learning poskytuje mechanismus pro zprovoznit model součástí R nebo Python.

Když jste zprovoznit modelu v Azure Machine Learning, je vystaven webové služby, která umožňuje klientům volání REST, které předejte vstupní parametry a přijímat předpovědi jako výstup z modelu.   

> [!NOTE]
> Pokud jste ještě nezaregistrovali jste se pro Azure Machine Learning, můžete získat volného prostoru nebo standardní pracovní prostor, navštivte stránky [Azure Machine Learning Studio](https://studio.azureml.net/) domovské stránky a kliknutím na "Začínáme."   
> 
> 

### <a name="build-and-operationalize-python-models"></a>Sestavení a zprovoznit Python modely
Zde je fragment kódu vyvinuté v poznámkového bloku Jupyter Python, který sestaví jednoduchého modelu pomocí SciKit další knihovny.

    #IRIS classification
    from sklearn import datasets
    from sklearn import svm
    clf = svm.SVC()
    iris = datasets.load_iris()
    X, y = iris.data, iris.target
    clf.fit(X, y)

Metoda použít k nasazení do Azure Machine Learning zabalí předpovědi modelu do funkce modely python a upraví s atributy poskytované předem nainstalovaná knihovna python Azure Machine Learning, které označují vaší Azure Machine Learning ID pracovního prostoru, klíč rozhraní API a parametry vstupní a zpět.  

    from azureml import services
    @services.publish(workspaceid, auth_token)
    @services.types(sep_l = float, sep_w = float, pet_l=float, pet_w=float)
    @services.returns(int) #0, or 1, or 2
    def predictIris(sep_l, sep_w, pet_l, pet_w):
     inputArray = [sep_l, sep_w, pet_l, pet_w]
    return clf.predict(inputArray)

Klienta můžete nyní volání webové služby. Existují obálky pohodlí, které vytvořit požadavky REST API. Tady je ukázkový kód využívají webové služby.

    # Consume through web service URL and keys
    from azureml import services
    @services.service(url, api_key)
    @services.types(sep_l = float, sep_w = float, pet_l=float, pet_w=float)
    @services.returns(float)
    def IrisPredictor(sep_l, sep_w, pet_l, pet_w):
    pass

    IrisPredictor(3,2,3,4)


> [!NOTE]
> Knihovna Azure Machine Learning je podporována pouze na Python 2.7 aktuálně.   
> 
> 

### <a name="build-and-operationalize-r-models"></a>Sestavení a zprovoznit R modely
Můžete nasadit R modely vytvořené na datové vědě virtuální počítač nebo jinde do Azure Machine Learning způsobem, který je podobný tomu pro jazyk Python. Postup je následující:

* Vytvořte soubor settings.json poskytnout ID pracovního prostoru a ověření tokenu 
* Obálka pro modelu předpovědi funkce zápisu
* volání ```publishWebService``` v knihovně Azure Machine Learning předávat obálku funkce.  

Zde je postup a kód fragmenty kódu, které slouží k nastavení, vytvářet, publikovat a využívat model jako webovou službu v Azure Machine Learning.

#### <a name="setup"></a>Nastavení

* Vytvořte soubor settings.json pod adresář s názvem ```.azureml``` pod domovského adresáře a zadejte parametry z pracovního prostoru Azure Machine Learning:

Settings.JSON strukturu souborů:

    {"workspace":{
    "id"                  : "ENTER YOUR AZUREML WORKSPACE ID",
    "authorization_token" : "ENTER YOUR AZUREML AUTH TOKEN"
    }}


#### <a name="build-a-model-in-r-and-publish-it-in-azure-machine-learning"></a>Vytvoření modelu v R a publikujete ho v Azure Machine Learning
    library(AzureML)
    ws <- workspace(config="~/.azureml/settings.json")

    if(!require("lme4")) install.packages("lme4")
    library(lme4)
    set.seed(1)
    train <- sleepstudy[sample(nrow(sleepstudy), 120),]
    m <- lm(Reaction ~ Days + Subject, data = train)

    # Define a prediction function to publish based on the model:
    sleepyPredict <- function(newdata){
          predict(m, newdata=newdata)
    }

    ep <- publishWebService(ws, fun = sleepyPredict, name="sleepy lm", inputSchema = sleepstudy, data.frame=TRUE)

#### <a name="consume-the-model-deployed-in-azure-machine-learning"></a>Využívat model nasadit v Azure Machine Learning
Využívat modelu z klientské aplikace, budeme používat knihovny Azure Machine Learning k vyhledání publikovanou webovou službu pomocí názvu `services` volání rozhraní API k určení koncový bod. Pak stačí zavolat `consume` funkce a předejte v data snímku na předpovědět.
Následující kód slouží k využívat model publikován jako webové služby Azure Machine Learning.

    library(AzureML)
    library(lme4)
    ws <- workspace(config="~/.azureml/settings.json")

    s <-  services(ws, name = "sleepy lm")
    s <- tail(s, 1) # use the last published function, in case of duplicate function names

    ep <- endpoints(ws, s)

    # OK, try this out, and compare with raw data
    ans = consume(ep, sleepstudy)$ans

Další informace o knihovně Azure Machine Learning R naleznete [zde](https://cran.r-project.org/web/packages/AzureML/AzureML.pdf).

## <a name="4-administer-your-azure-resources-using-azure-portal-or-powershell"></a>4. Správa prostředků Azure pomocí portálu Azure nebo prostředí Powershell
DSVM nejen je možné sestavit vaše řešení analytics místně na virtuálním počítači, ale také umožňuje přístup ke službám v cloudu Azure společnosti Microsoft. Azure poskytuje několik výpočty, úložiště, analytické služby data a dalším službám, které můžete spravovat a přistupovat z vašeho DSVM.

Ke správě předplatného a cloudových prostředků Azure můžete použít prohlížeč a přejděte [portál Azure](https://portal.azure.com). Prostředí Azure Powershell můžete použít také ke správě vašeho předplatného Azure a prostředky pomocí skriptu.
Prostředí Azure Powershell můžete spustit pomocí zástupce na ploše nebo z nabídky start s názvem "Microsoft Azure Powershell." Odkazovat na [dokumentace k Microsoft Azure Powershell](../../powershell-azure-resource-manager.md) Další informace o tom, jak můžete spravovat vaše předplatné Azure a prostředkům pomocí skriptů prostředí Windows Powershell.

## <a name="5-extend-your-storage-space-with-a-shared-file-system"></a>5. Rozšíření prostor úložiště pomocí systému sdílený soubor
Datových vědců můžete sdílet rozsáhlých datových sad, kódu nebo jiným prostředkům v rámci týmu. DSVM samotné má přibližně 45GB volného místa. Rozšířit úložiště, můžete použít službu souboru Azure a buď ji připojit na jeden nebo více instancí DSVM nebo přístup přes rozhraní REST API.  Můžete také použít [portálu Azure](../../virtual-machines/windows/attach-managed-disk-portal.md) nebo použijte [prostředí Azure Powershell](../../virtual-machines/windows/attach-disk-ps.md) přidat velmi vyhrazené datových disků. 

> [!NOTE]
> Maximální místo sdílené složky souboru služby Azure je 5 TB a maximální velikost jednotlivých souborů je 1 TB. 
> 
> 

Prostředí Azure Powershell můžete použít k vytvoření Azure služba souborů složky. Zde je skript, který chcete spustit v prostředí Azure PowerShell k vytvoření služby Azure sdílené složky.

    # Authenticate to Azure.
    Login-AzureRmAccount
    # Select your subscription
    Get-AzureRmSubscription –SubscriptionName "<your subscription name>" | Select-AzureRmSubscription
    # Create a new resource group.
    New-AzureRmResourceGroup -Name <dsvmdatarg>
    # Create a new storage account. You can reuse existing storage account if you wish.
    New-AzureRmStorageAccount -Name <mydatadisk> -ResourceGroupName <dsvmdatarg> -Location "<Azure Data Center Name For eg. South Central US>" -Type "Standard_LRS"
    # Set your current working storage account
    Set-AzureRmCurrentStorageAccount –ResourceGroupName "<dsvmdatarg>" –StorageAccountName <mydatadisk>

    # Create a Azure File Service Share
    $s = New-AzureStorageShare <<teamsharename>>
    # Create a directory under the FIle share. You can give it any name
    New-AzureStorageDirectory -Share $s -Path <directory name>
    # List the share to confirm that everything worked
    Get-AzureStorageFile -Share $s


Teď, když vytvoříte sdílenou složku Azure, můžete ji připojit žádné virtuální počítač v Azure. Důrazně doporučujeme, aby virtuální počítač ve stejném datovém centru Azure jako účet úložiště, náklady na přenos latence a data. Tady jsou příkazy připojit jednotku na DSVM, který můžete spustit v prostředí Azure Powershell.

    # Get storage key of the storage account that has the Azure file share from Azure portal. Store it securely on the VM to avoid prompted in next command.
    cmdkey /add:<<mydatadisk>>.file.core.windows.net /user:<<mydatadisk>> /pass:<storage key>

    # Mount the Azure file share as Z: drive on the VM. You can chose another drive letter if you wish
    net use z:  \\<mydatadisk>.file.core.windows.net\<<teamsharename>>


Nyní můžete zobrazit tuto jednotku stejně jako všechny normální jednotky ve virtuálním počítači.

## <a name="6-share-code-with-your-team-using-github"></a>6. Sdílet kódu se svým týmem pomocí Githubu
GitHub je úložiště kódu, kde můžete najít mnoho ukázkový kód a zdroje k různým nástrojům pomocí různých technologií, které jsou sdíleny komunity vývojářů. Git jako technologie používá ke sledování a uložit verzí soubory kódu. GitHub je také platforma, kde můžete vytvořit vlastní úložiště ukládání sdíleného kódu vašeho týmu a dokumentace, implementovat verzí a také ovládat, kteří mají přístup k zobrazení a přispívat kódu. Přejděte [stránky nápovědy Githubu](https://help.github.com/) pro další informace o použití Git. GitHub můžete použít jako jeden ze způsobů, jak se svým týmem spolupracovat, použít kód vyvinuté komunitou a přispívat kódu zpět do komunity.

DSVM už dodává s klientskými nástroji načíst na obou příkazového řádku jako dobře grafického uživatelského rozhraní pro přístup k úložišti GitHub. Nástroj příkazového řádku pro práci s Gitu a Githubu se nazývá Git Bash. Na DSVM nainstalovanou sadu Visual Studio má rozšíření Git. Můžete najít ikony spuštění těchto nástrojů v nabídce start a na ploše.

Chcete-li stáhnout kód z úložiště Githubu, použijte ```git clone``` příkaz. Například ke stažení úložiště vědecké účely data publikovaná společností Microsoft v aktuálním adresáři můžete spustit následující příkaz v ```git-bash```.

    git clone https://github.com/Azure/DataScienceVM.git

V sadě Visual Studio můžete to udělat stejné operace klonování. Následující snímek obrazovky ukazuje, jak pro přístup k Gitu a Githubu nástroje v sadě Visual Studio.

![Git v sadě Visual Studio](./media/vm-do-ten-things/VSGit.PNG)

Můžete najít další informace o použití Git pracovat s úložiště GitHub z několika zdrojů, které jsou k dispozici na webu github.com. [Tahák](https://training.github.com/kit/downloads/github-git-cheat-sheet.pdf) je užitečné referenční.

## <a name="7-access-various-azure-data-and-analytics-services"></a>7. Přístup k různým Azure službám data a analýzy
### <a name="azure-blob"></a>Azure Blob
Objektů blob v Azure je spolehlivé a ekonomické cloudové úložiště pro data velká a malá. Tato část popisuje, jak můžete přesunout data do Azure Blob a přístup k datům uloženým v objektu Blob Azure.

**Požadavek**

* **Vytvoření účtu úložiště objektů Blob v Azure z [portál Azure](https://portal.azure.com).**

![Create_Azure_Blob](./media/vm-do-ten-things/Create_Azure_Blob.PNG)

* Potvrďte, že je nástroj příkazového řádku AzCopy předem nainstalovaná nalezený na ```C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy.exe```. Adresář obsahující azcopy.exe již je na vašem proměnné prostředí PATH na nemuseli zadávat cestu celý příkaz i při spuštění tohoto nástroje. Další informace o nástroj AzCopy, najdete v části [dokumentaci k AzCopy](../../storage/common/storage-use-azcopy.md)
* Spusťte nástroj Azure Storage Explorer. Lze ji stáhnout z [Microsoft Azure Storage Explorer](http://storageexplorer.com/). 

![AzureStorageExplorer_v4](./media/vm-do-ten-things/AzureStorageExplorer_v4.png)

**Přesun dat z virtuálního počítače do objektu Blob Azure: AzCopy**

Pro přesun dat mezi vaší místní soubory a úložiště objektů blob, můžete použít AzCopy v příkazového řádku nebo prostředí PowerShell:

    AzCopy /Source:C:\myfolder /Dest:https://<mystorageaccount>.blob.core.windows.net/<mycontainer> /DestKey:<storage account key> /Pattern:abc.txt

Nahraďte **C:\myfolder** na cestu, kde je uložen soubor, **můj_účet_úložiště** na název účtu úložiště objektů blob **můj_kontejner** název kontejneru, **klíč účtu úložiště** na přístupový klíč k úložišti objektů blob. Můžete najít přihlašovací údaje účtu úložiště v [portál Azure](https://portal.azure.com).

![StorageAccountCredential_v2](./media/vm-do-ten-things/StorageAccountCredential_v2.png)

V prostředí PowerShell nebo z příkazového řádku, spusťte příkaz AzCopy. Tady je některé příklady použití nástroje AzCopy příkazu:

    # Copy *.sql from local machine to a Azure Blob
    "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:"c:\Aaqs\Data Science Scripts" /Dest:https://[ENTER STORAGE ACCOUNT].blob.core.windows.net/[ENTER CONTAINER] /DestKey:[ENTER STORAGE KEY] /S /Pattern:*.sql

    # Copy back all files from Azure Blob container to Local machine

    "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Dest:"c:\Aaqs\Data Science Scripts\temp" /Source:https://[ENTER STORAGE ACCOUNT].blob.core.windows.net/[ENTER CONTAINER] /SourceKey:[ENTER STORAGE KEY] /S



Po spuštění příkazu AzCopy zkopírujte do objektu blob Azure, uvidíte, že váš soubor se zobrazí v Azure Storage Explorer za chvíli.

![AzCopy_run_finshed_Storage_Explorer_v3](./media/vm-do-ten-things/AzCopy_run_finshed_Storage_Explorer_v3.png)

**Přesun dat z virtuálního počítače do objektu Blob Azure: Azure Storage Explorer**

Můžete také nahrát data z místního souboru v virtuálního počítače pomocí Průzkumníka úložiště Azure:

* Pokud chcete nahrát data do kontejneru, vyberte cílový kontejner a klikněte na **nahrát** tlačítko.![ Nahrát ve Storage Exploreru](./media/vm-do-ten-things/storage-accounts.png)
* Klikněte na **...**  napravo **soubory** , vyberte jeden nebo více souborů ze systému souborů a klikněte na **nahrát** zahájíte nahrávání souborů.![ Nahrání souborů do objektu blob](./media/vm-do-ten-things/upload-files-to-blob.png)

**Čtení dat z Azure Blob: modul čtečky Machine Learning**

V nástroji Azure Machine Learning Studio, můžete použít **importovat Data modulu** číst data z objektu blob služby.

![AML_ReaderBlob_Module_v3](./media/vm-do-ten-things/AML_ReaderBlob_Module_v3.png)

**Čtení dat z Azure Blob: Python ODBC**

Můžete použít **BlobService** knihovny číst data přímo z objektu blob v programu Poznámkový blok Jupyter nebo Python.

Nejprve importujte požadované balíčky:

    import pandas as pd
    from pandas import Series, DataFrame
    import numpy as np
    import matplotlib.pyplot as plt
    from time import time
    import pyodbc
    import os
    from azure.storage.blob import BlobService
    import tables
    import time
    import zipfile
    import random

Potom zařaďte přihlašovací údaje účtu Azure Blob a čtení dat z objektu Blob:

    CONTAINERNAME = 'xxx'
    STORAGEACCOUNTNAME = 'xxxx'
    STORAGEACCOUNTKEY = 'xxxxxxxxxxxxxxxx'
    BLOBNAME = 'nyctaxidataset/nyctaxitrip/trip_data_1.csv'
    localfilename = 'trip_data_1.csv'
    LOCALDIRECTORY = os.getcwd()
    LOCALFILE =  os.path.join(LOCALDIRECTORY, localfilename)

    #download from blob
    t1 = time.time()
    blob_service = BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)
    blob_service.get_blob_to_path(CONTAINERNAME,BLOBNAME,LOCALFILE)
    t2 = time.time()
    print(("It takes %s seconds to download "+BLOBNAME) % (t2 - t1))

    #unzipping downloaded files if needed
    #with zipfile.ZipFile(ZIPPEDLOCALFILE, "r") as z:
    #    z.extractall(LOCALDIRECTORY)

    df1 = pd.read_csv(LOCALFILE, header=0)
    df1.columns = ['medallion','hack_license','vendor_id','rate_code','store_and_fwd_flag','pickup_datetime','dropoff_datetime','passenger_count','trip_time_in_secs','trip_distance','pickup_longitude','pickup_latitude','dropoff_longitude','dropoff_latitude']
    print 'the size of the data is: %d rows and  %d columns' % df1.shape

Data se čtou v jako snímek dat:

![IPNB_data_readin](./media/vm-do-ten-things/IPNB_data_readin.PNG)

### <a name="azure-data-lake"></a>Azure Data Lake
Azure Data Lake Storage je velkého rozsahu úložiště pro úlohy analýzy velkých objemů dat a kompatibilní s Hadoop Distributed File System (HDFS). Funguje s Hadoop, Spark a Azure Data Lake Analytics. V této části se dozvíte, jak můžete přesun dat do Azure Data Lake Store a spustit analytics pomocí Azure Data Lake Analytics.

**Požadavek**

* Vytvoření vaší Azure Data Lake Analytics v [portál Azure](https://portal.azure.com).

![Azure_Data_Lake_Create_v2](./media/vm-do-ten-things/Azure_Data_Lake_Create_v2.png)

* **Nástrojů Azure Data Lake** v **Visual Studio** najít v této [odkaz](https://www.microsoft.com/download/details.aspx?id=49504) je již nainstalován na Visual Studio Community Edition, který je na virtuálním počítači. Po spuštění sady Visual Studio a protokolování ve vašem předplatném Azure, měli byste vidět účet Azure Data Analytics a úložiště v levém panelu sady Visual Studio.

![Azure_Data_Lake_PlugIn_v2](./media/vm-do-ten-things/Azure_Data_Lake_PlugIn_v2.PNG)

**Přesun dat z virtuálního počítače do Data Lake: Azure Data Lake Explorer**

Můžete použít **Azure Data Lake Explorer** ukládat data z místních souborů ve virtuálním počítači do úložiště Data Lake.

![Azure_Data_Lake_UploadData](./media/vm-do-ten-things/Azure_Data_Lake_UploadData.PNG)

Můžete také vytvořit datovém kanálu pro zprovoznění vašeho přesun dat do nebo z pomocí Azure Data Lake [Azure dat Factory(ADF)](https://azure.microsoft.com/services/data-factory/). Pokus [článku](https://azure.microsoft.com/blog/creating-big-data-pipelines-using-azure-data-lake-and-azure-data-factory/) vás provedou kroky k vytvoření data kanálů.

**Čtení dat z objektu Blob Azure do Data Lake: U-SQL**

Pokud máte data uložená v úložišti objektů Blob v Azure, můžete přímo přečíst data z objektu blob úložiště Azure v dotazu U-SQL. Před skládání dotazu U-SQL, zajistěte, aby že váš účet úložiště objektů blob je propojený s vaší Azure Data Lake. Přejděte na **portál Azure**, najít řídicí panel Azure Data Lake Analytics, klikněte na **přidat zdroj dat**, vyberte typ úložiště pro **Azure Storage** a zařadit ho ve vašem účtu úložiště Azure Název a klíč. Budete pak moci referenční data uložená v účtu úložiště.

![Zadejte účet úložiště a klíč](./media/vm-do-ten-things/Link_Blob_to_ADLA_v2.PNG)

V sadě Visual Studio můžete číst data z úložiště objektů blob, provést některé manipulaci s daty, konstruování a výstupní Výsledná data do Azure Data Lake nebo úložiště objektů Blob Azure. Když odkazujete data v úložišti objektů blob, použijte **wasb: / /**; když odkazujete data v Azure Data Lake použití **swbhdfs: / /**

![Data rámečku](./media/vm-do-ten-things/USQL_Read_Blob_v2.PNG)

V sadě Visual Studio můžete použít následující dotazy U-SQL:

    @a =
        EXTRACT medallion string,
                hack_license string,
                vendor_id string,
                rate_code string,
                store_and_fwd_flag string,
                pickup_datetime string,
                dropoff_datetime string,
                passenger_count int,
                trip_time_in_secs double,
                trip_distance double,
                pickup_longitude string,
                pickup_latitude string,
                dropoff_longitude string,
                dropoff_latitude string

        FROM "wasb://<Container name>@<Azure Blob Storage Account Name>.blob.core.windows.net/<Input Data File Name>"
        USING Extractors.Csv();

    @b =
        SELECT vendor_id,
        COUNT(medallion) AS cnt_medallion,
        SUM(passenger_count) AS cnt_passenger,
        AVG(trip_distance) AS avg_trip_dist,
        MIN(trip_distance) AS min_trip_dist,
        MAX(trip_distance) AS max_trip_dist,
        AVG(trip_time_in_secs) AS avg_trip_time
        FROM @a
        GROUP BY vendor_id;

    OUTPUT @b   
    TO "swebhdfs://<Azure Data Lake Storage Account Name>.azuredatalakestore.net/<Folder Name>/<Output Data File Name>"
    USING Outputters.Csv();

    OUTPUT @b   
    TO "wasb://<Container name>@<Azure Blob Storage Account Name>.blob.core.windows.net/<Output Data File Name>"
    USING Outputters.Csv();



Po dotazu je odeslána na server, se zobrazí diagram zobrazující stav úlohy.

![Diagram stavu úlohy](./media/vm-do-ten-things/USQL_Job_Status.PNG)

**Dotaz na data v Data Lake: U-SQL**

Po datová sada je konzumována do Azure Data Lake, můžete použít [jazykem U-SQL](../../data-lake-analytics/data-lake-analytics-u-sql-get-started.md) pro dotazování a data prozkoumat. Jazyk U-SQL je podobná T-SQL, ale spojuje některé funkce z jazyka C#, tak, aby uživatelé můžete napsat vlastní moduly, uživatelem definované funkce a atd. Můžete použít skripty v předchozím kroku.

Po dotazu je odeslána na server, tripdata_summary. Sdílený svazek clusteru najdete krátce v **Azure Data Lake Explorer**, vám může zobrazte náhled dat, podle klikněte pravým tlačítkem na soubor.

![Soubor v Průzkumníku služby Azure Data Lake](./media/vm-do-ten-things/USQL_create_summary.png)

Chcete-li zobrazit informace o souboru:

![Souhrn souborů](./media/vm-do-ten-things/USQL_tripdata_summary.png)

### <a name="hdinsight-hadoop-clusters"></a>Clustery HDInsight Hadoop
Azure HDInsight je spravovaná služba Apache Hadoop, Spark, HBase nebo Storm v cloudu. Můžete snadno pracovat s Azure HDInsight clustery z datové vědy virtuálního počítače.

**Požadavek**

* Vytvoření účtu úložiště objektů Blob v Azure z [portál Azure](https://portal.azure.com). Tento účet úložiště se používá k ukládání dat pro clustery služby HDInsight.

![Vytvořit účet úložiště objektů Blob v Azure](./media/vm-do-ten-things/Create_Azure_Blob.PNG)

* Přizpůsobení clusterů systému Hadoop HDInsight Azure z [portálu Azure](../team-data-science-process/customize-hadoop-cluster.md)
  
  * Propojení účtu úložiště vytvořeném k vašemu clusteru HDInsight, když je vytvořeno. Tento účet úložiště se používá pro přístup k datům, která může být zpracována v rámci clusteru.

![Odkaz na účet úložiště, které jsou vytvořené pomocí clusteru HDInsight](./media/vm-do-ten-things/Create_HDI_v4.PNG)

* Povolit **vzdáleného přístupu** k hlavnímu uzlu clusteru po jeho vytvoření. Pamatovat přihlašovací údaje vzdáleného přístupu, zde určíte jako musíte je v následujícím postupu.

![Povolte vzdálený přístup](./media/vm-do-ten-things/Create_HDI_dashboard_v3.PNG)

* Vytvořte pracovní prostor služby Azure Machine Learning. Vaše strojovým učením jsou uloženy v tomto pracovním prostoru Machine Learning. Vyberte zvýrazněný možnosti portálu, jak je znázorněno na následujícím snímku obrazovky:

![Vytvoření pracovního prostoru Azure Machine Learningu](./media/vm-do-ten-things/Create_ML_Space.PNG)

* Zadejte parametry pro pracovní prostor

![Zadejte parametry pracovního prostoru Machine Learning](./media/vm-do-ten-things/Create_ML_Space_step2_v2.PNG)

* Nahrání dat pomocí IPython Poznámkový blok. Importujte ho nejprve požadované balíčky, zařaďte přihlašovací údaje, vytvořit db ve vašem účtu úložiště a pak načíst data do clusterů HDI.

        #Import required Packages
        import pyodbc
        import time as time
        import json
        import os
        import urllib
        import urllib2
        import warnings
        import re
        import pandas as pd
        import matplotlib.pyplot as plt
        from azure.storage.blob import BlobService
        warnings.filterwarnings("ignore", category=UserWarning, module='urllib2')


        #Create the connection to Hive using ODBC
        SERVER_NAME='xxx.azurehdinsight.net'
        DATABASE_NAME='nyctaxidb'
        USERID='xxx'
        PASSWORD='xxxx'
        DB_DRIVER='Microsoft Hive ODBC Driver'
        driver = 'DRIVER={' + DB_DRIVER + '}'
        server = 'Host=' + SERVER_NAME + ';Port=443'
        database = 'Schema=' + DATABASE_NAME
        hiveserv = 'HiveServerType=2'
        auth = 'AuthMech=6'
        uid = 'UID=' + USERID
        pwd = 'PWD=' + PASSWORD
        CONNECTION_STRING = ';'.join([driver,server,database,hiveserv,auth,uid,pwd])
        connection = pyodbc.connect(CONNECTION_STRING, autocommit=True)
        cursor=connection.cursor()


        #Create Hive database and tables
        queryString = "create database if not exists nyctaxidb;"
        cursor.execute(queryString)

        queryString = """
                        create external table if not exists nyctaxidb.trip
                        (
                            medallion string,
                            hack_license string,
                            vendor_id string,
                            rate_code string,
                            store_and_fwd_flag string,
                            pickup_datetime string,
                            dropoff_datetime string,
                            passenger_count int,
                            trip_time_in_secs double,
                            trip_distance double,
                            pickup_longitude double,
                            pickup_latitude double,
                            dropoff_longitude double,
                            dropoff_latitude double)  
                        PARTITIONED BY (month int)
                        ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' lines terminated by '\\n'
                        STORED AS TEXTFILE LOCATION 'wasb:///nyctaxidbdata/trip' TBLPROPERTIES('skip.header.line.count'='1');
                    """
        cursor.execute(queryString)

        queryString = """
                        create external table if not exists nyctaxidb.fare
                        (
                            medallion string,
                            hack_license string,
                            vendor_id string,
                            pickup_datetime string,
                            payment_type string,
                            fare_amount double,
                            surcharge double,
                            mta_tax double,
                            tip_amount double,
                            tolls_amount double,
                            total_amount double)
                        PARTITIONED BY (month int)
                        ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' lines terminated by '\\n'
                        STORED AS TEXTFILE LOCATION 'wasb:///nyctaxidbdata/fare' TBLPROPERTIES('skip.header.line.count'='1');
                    """
        cursor.execute(queryString)


        #Upload data from blob storage to HDI cluster
        for i in range(1,13):
            queryString = "LOAD DATA INPATH 'wasb:///nyctaxitripraw2/trip_data_%d.csv' INTO TABLE nyctaxidb2.trip PARTITION (month=%d);"%(i,i)
            cursor.execute(queryString)
            queryString = "LOAD DATA INPATH 'wasb:///nyctaxifareraw2/trip_fare_%d.csv' INTO TABLE nyctaxidb2.fare PARTITION (month=%d);"%(i,i)  
            cursor.execute(queryString)


* Alternativně můžete to provést [návod](../team-data-science-process/hive-walkthrough.md) odesílat data NYC taxíkem do clusteru HDI. Hlavní kroky zahrnují:
  
  * AzCopy: Stáhněte komprimované CSV z veřejného objektu blob do místní složky
  * AzCopy: nahrajte rozbalené sdílených svazcích clusteru z místní složky do clusteru HDI
  * Přihlaste se k hlavnímu uzlu clusteru Hadoop a příprava pro analýzu nahodilého dat

Po načtení dat do clusteru HDI, můžete zkontrolovat vaše data v Azure Storage Explorer. A máte databáze nyctaxidb, vytvořené v clusteru HDI.

**Zkoumání dat: dotazů Hive v Pythonu**

Vzhledem k tomu, že data jsou v clusteru Hadoop, můžete se připojit k databázi clusterů systému Hadoop a dotazování pomocí Hive uděláte zkoumání a konstruování pyodbc balíčku. Můžete zobrazit existující tabulky, které jsme vytvořili v kroku požadovaných součástí.

    queryString = """
        show tables in nyctaxidb2;
        """
    pd.read_sql(queryString,connection)


![Zobrazit existující tabulky](./media/vm-do-ten-things/Python_View_Existing_Tables_Hive_v3.PNG)

Podívejme se na počet záznamů v každém měsíci a frekvence šikmý nebo není v tabulce cesty:

    queryString = """
        select month, count(*) from nyctaxidb.trip group by month;
        """
    results = pd.read_sql(queryString,connection)

    %matplotlib inline

    results.columns = ['month', 'trip_count']
    df = results.copy()
    df.index = df['month']
    df['trip_count'].plot(kind='bar')


![Vykreslení počet záznamů v každém měsíci](./media/vm-do-ten-things/Exploration_Number_Records_by_Month_v3.PNG)

    queryString = """
        SELECT tipped, COUNT(*) AS tip_freq
        FROM
        (
            SELECT if(tip_amount > 0, 1, 0) as tipped, tip_amount
            FROM nyctaxidb.fare
        )tc
        GROUP BY tipped;
        """
    results = pd.read_sql(queryString,connection)

    results.columns = ['tipped', 'trip_count']
    df = results.copy()
    df.index = df['tipped']
    df['trip_count'].plot(kind='bar')


![Vykreslení frekvencí tipu](./media/vm-do-ten-things/Exploration_Frequency_tip_or_not_v3.PNG)

Můžete také výpočetní vzdálenost mezi výstupní umístění a dropoff umístění a porovnejte je s vzdálenost cesty.

    queryString = """
                    select pickup_longitude, pickup_latitude, dropoff_longitude, dropoff_latitude, trip_distance, trip_time_in_secs,
                        3959*2*2*atan((1-sqrt(1-pow(sin((dropoff_latitude-pickup_latitude)
                        *radians(180)/180/2),2)-cos(pickup_latitude*radians(180)/180)
                        *cos(dropoff_latitude*radians(180)/180)*pow(sin((dropoff_longitude-pickup_longitude)*radians(180)/180/2),2)))
                        /sqrt(pow(sin((dropoff_latitude-pickup_latitude)*radians(180)/180/2),2)
                        +cos(pickup_latitude*radians(180)/180)*cos(dropoff_latitude*radians(180)/180)*
                        pow(sin((dropoff_longitude-pickup_longitude)*radians(180)/180/2),2))) as direct_distance
                        from nyctaxidb.trip
                        where month=1
                            and pickup_longitude between -90 and -30
                            and pickup_latitude between 30 and 90
                            and dropoff_longitude between -90 and -30
                            and dropoff_latitude between 30 and 90;
                """
    results = pd.read_sql(queryString,connection)
    results.head(5)


![Vyzvednutí a dropoff tabulky](./media/vm-do-ten-things/Exploration_compute_pickup_dropoff_distance_v2.PNG)

    results.columns = ['pickup_longitude', 'pickup_latitude', 'dropoff_longitude',
                       'dropoff_latitude', 'trip_distance', 'trip_time_in_secs', 'direct_distance']
    df = results.loc[results['trip_distance']<=100] #remove outliers
    df = df.loc[df['direct_distance']<=100] #remove outliers
    plt.scatter(df['direct_distance'], df['trip_distance'])


![Vykreslení vyzvednutí/dropoff vzdálenost vzdálenost cesty](./media/vm-do-ten-things/Exploration_direct_distance_trip_distance_v2.PNG)

Nyní Pojďme Příprava nižší vzorků (1 %) sady dat pro modelování. Tyto údaje můžete použít v modul čtečky Machine Learning.

        queryString = """
        create  table if not exists nyctaxi_downsampled_dataset_testNEW (
        medallion string,
        hack_license string,
        vendor_id string,
        rate_code string,
        store_and_fwd_flag string,
        pickup_datetime string,
        dropoff_datetime string,
        pickup_hour string,
        pickup_week string,
        weekday string,
        passenger_count int,
        trip_time_in_secs double,
        trip_distance double,
        pickup_longitude double,
        pickup_latitude double,
        dropoff_longitude double,
        dropoff_latitude double,
        direct_distance double,
        payment_type string,
        fare_amount double,
        surcharge double,
        mta_tax double,
        tip_amount double,
        tolls_amount double,
        total_amount double,
        tipped string,
        tip_class string
        )
        row format delimited fields terminated by ','
        lines terminated by '\\n'
        stored as textfile;
        """
        cursor.execute(queryString)

        --- now insert contents of the join into the preceding internal table

        queryString = """
        insert overwrite table nyctaxi_downsampled_dataset_testNEW
        select
        t.medallion,
        t.hack_license,
        t.vendor_id,
        t.rate_code,
        t.store_and_fwd_flag,
        t.pickup_datetime,
        t.dropoff_datetime,
        hour(t.pickup_datetime) as pickup_hour,
        weekofyear(t.pickup_datetime) as pickup_week,
        from_unixtime(unix_timestamp(t.pickup_datetime, 'yyyy-MM-dd HH:mm:ss'),'u') as weekday,
        t.passenger_count,
        t.trip_time_in_secs,
        t.trip_distance,
        t.pickup_longitude,
        t.pickup_latitude,
        t.dropoff_longitude,
        t.dropoff_latitude,
        t.direct_distance,
        f.payment_type,
        f.fare_amount,
        f.surcharge,
        f.mta_tax,
        f.tip_amount,
        f.tolls_amount,
        f.total_amount,
        if(tip_amount>0,1,0) as tipped,
        if(tip_amount=0,0,
        if(tip_amount>0 and tip_amount<=5,1,
        if(tip_amount>5 and tip_amount<=10,2,
        if(tip_amount>10 and tip_amount<=20,3,4)))) as tip_class
        from
        (
        select
        medallion,
        hack_license,
        vendor_id,
        rate_code,
        store_and_fwd_flag,
        pickup_datetime,
        dropoff_datetime,
        passenger_count,
        trip_time_in_secs,
        trip_distance,
        pickup_longitude,
        pickup_latitude,
        dropoff_longitude,
        dropoff_latitude,
        3959*2*2*atan((1-sqrt(1-pow(sin((dropoff_latitude-pickup_latitude)
        radians(180)/180/2),2)-cos(pickup_latitude*radians(180)/180)
        *cos(dropoff_latitude*radians(180)/180)*pow(sin((dropoff_longitude-pickup_longitude)*radians(180)/180/2),2)))
        /sqrt(pow(sin((dropoff_latitude-pickup_latitude)*radians(180)/180/2),2)
        +cos(pickup_latitude*radians(180)/180)*cos(dropoff_latitude*radians(180)/180)*pow(sin((dropoff_longitude-pickup_longitude)*radians(180)/180/2),2))) as direct_distance,
        rand() as sample_key

        from trip
        where pickup_latitude between 30 and 90
            and pickup_longitude between -90 and -30
            and dropoff_latitude between 30 and 90
            and dropoff_longitude between -90 and -30
        )t
        join
        (
        select
        medallion,
        hack_license,
        vendor_id,
        pickup_datetime,
        payment_type,
        fare_amount,
        surcharge,
        mta_tax,
        tip_amount,
        tolls_amount,
        total_amount
        from fare
        )f
        on t.medallion=f.medallion and t.hack_license=f.hack_license and t.pickup_datetime=f.pickup_datetime
        where t.sample_key<=0.01
        """
        cursor.execute(queryString)

Po chvíli uvidíte, že data se načetl v clusterů systému Hadoop:

    queryString = """
        select * from nyctaxi_downsampled_dataset limit 10;
        """
    cursor.execute(queryString)
    pd.read_sql(queryString,connection)


![Tabulka dat](./media/vm-do-ten-things/DownSample_Data_For_Modeling_v2.PNG)

**Čtení dat z HDI pomocí Machine Learning: modul čtečky**

Můžete také používat **čtečky** modulu v nástroji Machine Learning Studio pro přístup k databázi v clusteru Hadoop. Připojte přihlašovací údaje HDI clusterů a účet úložiště Azure, chcete-li povolit vytváření modelů strojového učení pomocí databáze v clusterech HDI.

![Vlastnosti modulu Reader](./media/vm-do-ten-things/AML_Reader_Hive.PNG)

Scored datovou sadu lze následně zobrazit:

![Zobrazení skóre pro magnitudu datové sady](./media/vm-do-ten-things/AML_Model_Results.PNG)

### <a name="azure-sql-data-warehouse--databases"></a>Azure SQL Data Warehouse & databáze
Azure SQL Data Warehouse je elastické datového skladu jako služba podnikových prostředí systému SQL Server.

Můžete zřídit Azure SQL Data Warehouse pomocí následujících pokynů uvedených v tomto [článku](../../sql-data-warehouse/sql-data-warehouse-get-started-provision.md). Po zřízení Azure SQL Data Warehouse, můžete to [návod](../team-data-science-process/sqldw-walkthrough.md) udělat nahrání dat, zkoumání a modelování pomocí dat v rámci SQL Data Warehouse.

#### <a name="azure-cosmos-db"></a>Azure Cosmos DB
Azure Cosmos DB je databáze NoSQL v cloudu. Ji umožňuje pracovat s dokumenty jako JSON a umožňuje ukládání a dotazování dokumentů.

Následující kroky na požadavky pro přístup k databázi Azure Cosmos z DSVM:

1. Azure Cosmos DB Python SDK je již nainstalován na DSVM (Spustit ```pip install pydocumentdb --upgrade``` z příkazového řádku k aktualizaci)
2. Vytvoření účtu Azure Cosmos databáze a databáze z [portálu Azure](https://portal.azure.com)
3. Stáhnout "Nástroj pro migraci Azure Cosmos DB" z [zde](http://www.microsoft.com/downloads/details.aspx?FamilyID=cda7703a-2774-4c07-adcc-ad02ddc1a44d) a extrahování k adresáři podle svého výběru
4. Umožňuje importovat data JSON (sopka data) uložené na [veřejného objektu blob](https://cahandson.blob.core.windows.net/samples/volcano.json) do databáze Cosmos s následující parametry příkazu pro nástroj pro migraci (dtui.exe z adresáře, kam jste nainstalovali nástroj pro migraci DB Cosmos). Zadejte umístění zdrojové a cílové s těmito parametry:
   
    /s:JsonFile /s.Files:https://cahandson.blob.core.windows.net/samples/volcano.json /t:DocumentDBBulk /t.ConnectionString:AccountEndpoint=https://[DocDBAccountName].documents.azure.com:443/;AccountKey=[[KEY];Database=volcano /t.Collection:volcano1

Jakmile importujete data, můžete přejít do Jupyter a otevřete Poznámkový blok s názvem *DocumentDBSample* obsahující kód python pro přístup k databázi Cosmos Azure a provést některé základní dotazování. Další informace o Cosmos DB návštěvou službu [stránky dokumentace, která](https://docs.microsoft.com/azure/cosmos-db/).

## <a name="8-build-reports-and-dashboard-using-the-power-bi-desktop"></a>8. Vytvářejte sestavy a řídicí panel pomocí Power BI Desktop
Můžete vizualizovat souboru JSON sopka z předchozího příkladu Cosmos DB v Power BI visual proniknout do data. Podrobné pokyny jsou k dispozici v [Power BI článku](../../cosmos-db/powerbi-visualize.md). Zde jsou základní kroky:

1. Otevřít Power BI Desktop a "získat Data." Zadejte adresu URL jako: https://cahandson.blob.core.windows.net/samples/volcano.json
2. Měli byste vidět importovaných jako seznam záznamů JSON
3. Převést seznam na tabulku, Power BI mohli pracovat se stejným
4. Rozbalte sloupce kliknutím na ikonu rozbalení (jeden ikonou "šipku vlevo a šipku vpravo" na pravé straně sloupec)
5. Všimněte si, že umístění je na pole "Záznam". Rozbalte položku a vyberte pouze souřadnice. Souřadnice je sloupec seznamu
6. Umožňuje přidat nový sloupec a převádět souřadnic sloupec seznamu čárkami samostatný LatLong sloupec zřetězení dva elementy pole souřadnic seznamu pomocí vzorce ```Text.From([coordinates]{1})&","&Text.From([coordinates]{0})```.
7. Nakonec převést ```Elevation``` sloupec desetinných míst a vyberte **Zavřít** a **použít**.

Místo předchozích kroků, můžete vložte následující kód, skripty out s postupem v editoru Advanced v Power BI, který umožňuje zapisovat transformace dat v dotazovací jazyk.

    let
        Source = Json.Document(Web.Contents("https://cahandson.blob.core.windows.net/samples/volcano.json")),
        #"Converted to Table" = Table.FromList(Source, Splitter.SplitByNothing(), null, null, ExtraValues.Error),
        #"Expanded Column1" = Table.ExpandRecordColumn(#"Converted to Table", "Column1", {"Volcano Name", "Country", "Region", "Location", "Elevation", "Type", "Status", "Last Known Eruption", "id"}, {"Volcano Name", "Country", "Region", "Location", "Elevation", "Type", "Status", "Last Known Eruption", "id"}),
        #"Expanded Location" = Table.ExpandRecordColumn(#"Expanded Column1", "Location", {"coordinates"}, {"coordinates"}),
        #"Added Custom" = Table.AddColumn(#"Expanded Location", "LatLong", each Text.From([coordinates]{1})&","&Text.From([coordinates]{0})),
        #"Changed Type" = Table.TransformColumnTypes(#"Added Custom",{{"Elevation", type number}})
    in
        #"Changed Type"



Nyní máte data v Power BI datového modelu. Power BI ploše by měl vypadat takto:

![Power BI Desktop](./media/vm-do-ten-things/PowerBIVolcanoData.png)

Můžete začít vytvářet sestavy a vizualizací pomocí datového modelu. Můžete postupovat podle kroků v tomto [Power BI článku](../../cosmos-db/powerbi-visualize.md#build-the-reports) pro vytvoření sestavy. Výstup je sestavu, která vypadá takto.

![Power BI Desktop zobrazení sestavy - Power BI connector](./media/vm-do-ten-things/power_bi_connector_pbireportview2.png)

## <a name="9-dynamically-scale-your-dsvm-to-meet-your-project-needs"></a>9. Dynamické škálování vašeho DSVM potřebám vašeho projektu
Je možné škálovat nahoru a dolů DSVM potřebám vašeho projektu. Pokud nemusíte používat virtuální počítač v večer nebo o víkendech, můžete právě vypnout virtuální počítač z [portál Azure](https://portal.azure.com).

> [!NOTE]
> Pokud používáte pouze tlačítko vypnutí operačního systému ve virtuálním počítači platit poplatky výpočty.  
> 
> 

Pokud je potřeba zpracovat některé rozsáhlé analýzy a potřebovat větší kapacitu procesoru nebo paměti a disku můžete najít velké volba velikostí virtuálních počítačů z hlediska jader procesoru, bázi GPU instancí hloubkové learning, kapacita paměti a typy disků (včetně jednotky SSD.) které splňují vaše výpočetní a rozpočtových potřebám. Úplný seznam virtuálních počítačů spolu s jejich hodinové výpočetní ceny je k dispozici na [ceny virtuálních počítačů Azure](https://azure.microsoft.com/pricing/details/virtual-machines/) stránky.

Podobně pokud snižuje potřeba kapacity zpracování virtuálního počítače (například: přesunout hlavní zatížení na Hadoop nebo Spark cluster), je možné škálovat dolů je cluster ze [portál Azure](https://portal.azure.com) a nastavení vaší instance virtuálních počítačů. Zde je snímek.

![Nastavení instance virtuálních počítačů](./media/vm-do-ten-things/VMScaling.PNG)

## <a name="10-install-additional-tools-on-your-virtual-machine"></a>10. Nainstalujte další nástroje na virtuálním počítači
Existuje několik nástrojů předem integrovaných v DSVM, můžete vyřešit řadu běžných data, která potřebuje analytics. Tím ušetříte čas vyhnout museli instalovat a konfigurovat vašich prostředích po jednom a ušetřit peníze platebním pouze pro prostředky, že používáte.

Můžete využít další data a analýzy služby Azure profilovaným v tomto článku k vylepšení prostředí analýzy. V některých případech může vyžadovat vašim potřebám dalších nástrojů, včetně některá vlastnické nástroje třetích stran. Máte plný přístup správce na virtuální počítač pro instalaci nové nástroje, které potřebujete. Můžete taky nainstalovat další balíčky Python a R, která nejsou předem nainstalovaná. Pro jazyk Python můžete použít buď ```conda``` nebo ```pip```. Pro R můžete použít ```install.packages()``` v R konzole nebo pomocí rozhraní IDE a zvolte "**balíčky** -> **instalovat balíčky...** ".

## <a name="summary"></a>Souhrn
Toto jsou jen některé z akcí, které můžete provést na Microsoft Data vědecké účely virtuálního počítače. Existuje mnoho dalších věcí, které můžete provést, aby bylo prostředí efektivní analýzu.

