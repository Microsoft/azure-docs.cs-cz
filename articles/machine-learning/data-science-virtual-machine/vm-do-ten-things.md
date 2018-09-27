---
title: Deset věcí, které můžete provést na virtuální počítač pro datové vědy v Azure | Dokumentace Microsoftu
description: Na virtuální počítač pro datovou vědu provádějte různé pro zkoumání a modelování úloh.
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
ms.date: 09/24/2018
ms.author: gokuma
ms.openlocfilehash: faabdb4c2d2e434863a6bed0b2cd85a05c94eab1
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2018
ms.locfileid: "47395725"
---
# <a name="ten-things-you-can-do-on-the-windows-data-science-virtual-machine"></a>Deset věcí, které vám pomůžou na Windows virtuální počítač pro datové vědy

Windows dat virtuálního počítače VĚDY je výkonné vývojové prostředí pro datové vědy, která umožňuje provádění různých úloh zkoumání a modelování dat. Toto prostředí se dodává už připravené a jako součást balíčku s několika oblíbenými datovými analytické nástroje, které usnadňují rychlé zahájení práce díky analýzy pro místní, Cloudová nebo hybridní nasazení. Datové VĚDY úzce spolupracuje s řadou služeb Azure a je možné číst a zpracovávat data, která je již uložen v Azure, Azure SQL Data Warehouse, Azure Data Lake, Azure Storage nebo ve službě Azure Cosmos DB. Můžete taky využít další analytické nástroje, jako je Azure Machine Learning a Azure Data Factory.

V tomto článku se dozvíte, jak používat vaše DSVM k provádění různých úloh datových věd a interakci s ostatními službami Azure. Tady jsou některé věci, které můžete provést jak na datové VĚDY:

1. Zkoumání dat a vyvíjet modely místně na datové VĚDY pomocí Microsoft ML Server Pythonu
2. Pomocí poznámkového bloku Jupyter můžete experimentovat s vašimi daty v prohlížeči pomocí Python 2, Python 3, Microsoft R enterprise připravená verze R navržená pro výkon
3. Nasazovat modely sestavené pomocí R a Python v Azure Machine Learning, aby klientské aplikace měli přístup k modelů pomocí rozhraní jednoduché webové služby
4. Správa prostředků Azure pomocí webu Azure portal nebo Powershellu
5. Rozšíření vašeho prostoru úložiště a sdílet rozsáhlých datových sad / kódu napříč celý tým tak, že vytvoříte služby Azure File storage jako možnost připojit jednotku ve vaší DSVM
6. Sdílejte kód se svým týmem pomocí Githubu a přístup k úložišti s předinstalovanou klienty Git - Git Bash, Git grafického uživatelského rozhraní.
7. Přístup k různé dat a analýza služby Azure jako úložiště objektů blob v Azure, Azure Data Lake, Azure HDInsight (Hadoop), Azure Cosmos DB, Azure SQL Data Warehouse & databáze
8. Vytvářejte sestavy a řídicí panel pomocí Power BI Desktopu předinstalované na datové VĚDY a nasazovat je do cloudu
9. Dynamicky škálujte vaše DSVM podle svých potřeb projektu
10. Nainstalujte další nástroje ve vašem virtuálním počítači   

> [!NOTE]
> Poplatky za další využívání se použije pro celou řadu dalších datové úložiště a analýzy služby uvedené v tomto článku. Odkazovat [ceny za Azure](https://azure.microsoft.com/pricing/) stránce Podrobnosti.
> 
> 

**Požadavky**

* Budete potřebovat předplatné Azure. Můžete se zaregistrovat k bezplatné zkušební verzi [tady](https://azure.microsoft.com/free/).
* Pokyny pro zřízení virtuálního počítače datové vědy na portálu Azure portal najdete na adrese [vytvoření virtuálního počítače](https://portal.azure.com/#create/microsoft-dsvm.dsvm-windowsserver-2016).

## <a name="1-explore-data-and-develop-models-using-microsoft-ml-server-or-python"></a>1. Zkoumání dat a vyvíjet modely s využitím Microsoft ML Server nebo Pythonu
Jazyky, jako je R a Pythonu můžete provádět datové VĚDY vaší analýzy dat přímo.

Pro jazyk R můžete použít integrované vývojové prostředí jako RStudio, který najdete v nabídce start nebo plochy nebo nástroje R pro Visual Studio. Microsoft poskytuje další knihovny se nad Open zdroj/CRAN-R povolit škálovatelných analýz a možnosti analýzy dat větší než velikost paměti povoleno tímto způsobem paralelní analýzy bloku. 

Pro Python můžete použít integrované vývojové prostředí jako je Visual Studio Community Edition, který obsahuje nástroje Python Tools pro předem nainstalované rozšíření Visual Studio (PTVS). Ve výchozím nastavení pouze Python 3.6, prostředí conda kořenové nakonfigurované na PTVS. Chcete-li povolit Anaconda Python 2.7, je třeba provést následující kroky:

* Vytvoření vlastního prostředí pro každou verzi tak, že přejdete do **nástroje** -> **nástroje Python Tools** -> **prostředí Pythonu** a pak levým na " **+ Vlastní**"v sadě Visual Studio Community Edition
* Zadejte popis a nastavte prostředí předponovou cestu jako *c:\anaconda\envs\python2* pro Anaconda Python 2.7
* Klikněte na tlačítko **automaticky rozpoznat** a potom **použít** uložit prostředí.

Tady je vypadá vlastní prostředí v sadě Visual Studio.

![Instalační program PTVS](./media/vm-do-ten-things/PTVSSetup.png)

Zobrazit [dokumentace k PTVS](http://aka.ms/ptvsdocs) najdete další podrobnosti o tom, jak vytvořit prostředí Pythonu.

Nyní jsou nastavení k vytvoření nového projektu Pythonu. Přejděte do **souboru** -> **nový** -> **projektu** -> **Python** a vyberte typ Aplikace v Pythonu, který vytváříte. Prostředí Pythonu pro aktuální projekt můžete nastavit na požadovanou verzi (Python 2.7 nebo 3.6) kliknutím pravým tlačítkem **prostředí Pythonu**, kde vyberou **přidat nebo odebrat prostředí Pythonu**a pak výběr na požadované prostředí. Další informace o práci s nástroji PTVS v produktu [dokumentaci](http://aka.ms/ptvsdocs).

## <a name="2-using-a-jupyter-notebook-to-explore-and-model-your-data-with-python-or-r"></a>2. Pomocí poznámkového bloku Jupyter pro zkoumání a modelování dat v Pythonu nebo r.
Poznámkový blok Jupyter je výkonné prostředí, která poskytuje pro zkoumání a modelování dat založené na prohlížeči "integrované vývojové prostředí". V poznámkovém bloku Jupyter, můžete použít Python 2, Python 3 nebo R (Open Source a Microsoft R serveru).

Spusťte Poznámkový blok Jupyter, klikněte na ikonu nabídky start / ikony na ploše s názvem **Poznámkový blok Jupyter**. Na příkazovém řádku DSVM můžete také spustit příkaz ```jupyter notebook``` z adresáře, kde máte existující poznámkových bloků nebo chcete vytvořit nový poznámkových bloků.  

Po zahájení Jupyter byste měli vidět adresář, který obsahuje pár příklad poznámkových bloků, které jsou předem zabalené do datové VĚDY. Nyní můžete:

* Klikněte na tlačítko v poznámkovém bloku, abyste zobrazili kód.
* Spuštění každou buňku stisknutím kombinace kláves **SHIFT + ENTER**.
* Celý poznámkový blok spustit kliknutím na **buňky** -> **spuštění**
* Vytvořte nový poznámkový blok, klikněte na ikonu Jupyter (levém horním rohu) a pak levým na **nový** tlačítko na pravé straně a potom volbou poznámkového bloku jazyka (jader).   

> [!NOTE]
> Python 2.7, Python 3.6, R, Julia a PySpark jádrech v Jupyter se aktuálně podporují. R jádra pro podporu programování v opensourcového jazyka R i výkonné Microsoft R.   
> 
> 

Až budete v poznámkovém bloku, které může prozkoumat vaše data sestavení modelu, Testovat model na základě zvoleného knihoven.

## <a name="3-build-models-using-r-or-python-and-operationalize-them-using-azure-machine-learning"></a>3. Vytvářet modely s využitím R nebo Python a zprovoznění je pomocí Azure Machine Learning
Po vytvořené a ověřit model dalším krokem je obvykle k nasazení do produkčního prostředí. Díky tomu váš klient aplikace k vyvolání předpovědí modelu v reálném čase nebo na základě režimu služby batch. Azure Machine Learning poskytuje mechanismus pro zprovoznění modelu vytvořené v R nebo Python.

Pokud jste zprovoznění vašeho modelu ve službě Azure Machine Learning, je přístupný webovou službu, která umožňuje klientům volání REST, které předejte vstupní parametry a přijímat předpovědí z modelu jako výstup.   

> [!NOTE]
> Pokud jste ještě zatím zaregistrovali službu Azure Machine Learning, si můžete získat bezplatný pracovní prostor nebo standardní pracovní prostor [Azure Machine Learning Studio](https://studio.azureml.net/) domovskou stránku a kliknutím na "Začínáme."   
> 
> 

### <a name="build-and-operationalize-python-models"></a>Sestavení a Python zprovoznění modelů
Tady je fragment kódu s vyvinutý v Pythonu Poznámkový blok Jupyter, který sestaví jednoduchý model s použitím knihovny SciKit poučení.

    #IRIS classification
    from sklearn import datasets
    from sklearn import svm
    clf = svm.SVC()
    iris = datasets.load_iris()
    X, y = iris.data, iris.target
    clf.fit(X, y)

Metoda používá k nasazení modelů pythonu do Azure Machine Learning zabalí předpovědi modelu na funkci a upraví s atributy poskytovaných knihovnou předinstalované python Azure Machine Learning, které označují vaše Azure Machine Learning ID pracovního prostoru, klíč rozhraní API a vstupní a vrácené parametry.  

    from azureml import services
    @services.publish(workspaceid, auth_token)
    @services.types(sep_l = float, sep_w = float, pet_l=float, pet_w=float)
    @services.returns(int) #0, or 1, or 2
    def predictIris(sep_l, sep_w, pet_l, pet_w):
     inputArray = [sep_l, sep_w, pet_l, pet_w]
    return clf.predict(inputArray)

Klienta můžete provést volání webové služby. Existují obálky pohodlí, které vytvořit požadavky rozhraní REST API. Tady je ukázkový kód pro používání této webové služby.

    # Consume through web service URL and keys
    from azureml import services
    @services.service(url, api_key)
    @services.types(sep_l = float, sep_w = float, pet_l=float, pet_w=float)
    @services.returns(float)
    def IrisPredictor(sep_l, sep_w, pet_l, pet_w):
    pass

    IrisPredictor(3,2,3,4)


> [!NOTE]
> Knihovny Azure Machine Learning je podporován pouze v případě Pythonu 2.7 aktuálně.   
> 
> 

### <a name="build-and-operationalize-r-models"></a>Sestavení a R zprovoznění modelů
Můžete nasadit R historické modely budou vytvářeny na virtuální počítač pro datové vědy nebo jinde do Azure Machine Learning způsobem, který je podobný jak se to dělá pro Python. Postup je následující:

* Vytvořte soubor settings.json poskytnout ID pracovního prostoru a ověření tokenu 
* zápis obálku pro předpověď modelu funkce.
* volání ```publishWebService``` v knihovně Azure Machine Learning a zajistěte tak předání funkce obálky.  

Tady je postup a kódu fragmenty kódu, které slouží k nastavení, vytvářet, publikovat a využívat model jako webovou službu ve službě Azure Machine Learning.

#### <a name="setup"></a>Nastavení

* Vytvořte soubor settings.json v adresáři s názvem ```.azureml``` pod domovského adresáře a zadejte parametry z pracovního prostoru Azure Machine Learning:

Struktura souboru Settings.JSON:

    {"workspace":{
    "id"                  : "ENTER YOUR AZUREML WORKSPACE ID",
    "authorization_token" : "ENTER YOUR AZUREML AUTH TOKEN"
    }}


#### <a name="build-a-model-in-r-and-publish-it-in-azure-machine-learning"></a>Sestavení modelu v jazyce R a publikovat ve službě Azure Machine Learning
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

#### <a name="consume-the-model-deployed-in-azure-machine-learning"></a>Používání modelu nasazené ve službě Azure Machine Learning
Chcete-li využívají model z klientské aplikace, používáme k vyhledání publikované webové službě s použitím názvu knihovny Azure Machine Learning `services` volání rozhraní API k určení koncového bodu. Pak ho prostě zavoláte `consume` fungovat a předejte mu datový rámec pro předpovídat.
Následující kód slouží k používání modelu publikovat jako webové služby Azure Machine Learning.

    library(AzureML)
    library(lme4)
    ws <- workspace(config="~/.azureml/settings.json")

    s <-  services(ws, name = "sleepy lm")
    s <- tail(s, 1) # use the last published function, in case of duplicate function names

    ep <- endpoints(ws, s)

    # OK, try this out, and compare with raw data
    ans = consume(ep, sleepstudy)$ans

Další informace o knihovně Azure Machine Learning R najdete [tady](https://cran.r-project.org/web/packages/AzureML/AzureML.pdf).

## <a name="4-administer-your-azure-resources-using-azure-portal-or-powershell"></a>4. Správa prostředků Azure pomocí webu Azure portal nebo Powershellu
Datové VĚDY nejen umožňuje vytvářet analytické řešení místně na virtuálním počítači, ale také umožňuje přístup ke službám v cloudu Azure Microsoftu. Azure nabízí několik výpočetních, úložiště, služby analýzy dat a dalším službám, které můžete spravovat a přístup k vaší DSVM.

Ke správě předplatného a cloudové prostředky Azure můžete použít prohlížeč a přejděte [webu Azure portal](https://portal.azure.com). Můžete také pomocí Azure Powershellu ke správě vašeho předplatného Azure a prostředky prostřednictvím skriptu.
Prostředí Azure Powershell můžete spustit ze zástupce na ploše nebo v nabídce start s názvem "Microsoft Azure Powershell." Odkazovat na [dokumentaci k Microsoft Azure Powershellu](../../powershell-azure-resource-manager.md) Další informace o tom, jak můžete spravovat své předplatné Azure a prostředky pomocí skriptů Windows Powershellu.

## <a name="5-extend-your-storage-space-with-a-shared-file-system"></a>5. Prodloužit místa v úložišti s sdílený systém souborů
Odborníci přes data můžou sdílet velké datové sady, kód nebo jiných prostředků v rámci týmu. Datové VĚDY, samotné se přibližně 45GB volného místa. K rozšíření úložiště, můžete použít službu Azure File a buď ji připojit na jednu nebo víc instancí DSVM nebo k nim přistupovat přes rozhraní REST API.  Můžete také použít [webu Azure Portal](../../virtual-machines/windows/attach-managed-disk-portal.md) nebo použijte [prostředí Azure Powershell](../../virtual-machines/windows/attach-disk-ps.md) pro přidání dalších vyhrazených datových disků. 

> [!NOTE]
> Maximální místo sdílení službu Azure File je 5 TB a maximální velikost jednotlivých souborů je 1 TB. 
> 
> 

Prostředí Azure Powershell můžete použít k vytvoření sdílené složky Azure File Service. Tady je skript, který chcete spustit v prostředí Azure PowerShell k vytvoření služby sdílené složky Azure.

    # Authenticate to Azure.
    Connect-AzureRmAccount
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


Teď, když jste vytvořili sdílenou složku Azure, můžete je připojit do libovolného virtuálního počítače v Azure. Důrazně doporučujeme, aby virtuální počítač je ve stejném datovém centru Azure jako účet úložiště, aby se zabránilo latenci a data poplatky za přenos. Tady jsou příkazy jednotku můžete připojit na datové VĚDY, které můžete spustit v prostředí Azure Powershell.

    # Get storage key of the storage account that has the Azure file share from Azure portal. Store it securely on the VM to avoid prompted in next command.
    cmdkey /add:<<mydatadisk>>.file.core.windows.net /user:<<mydatadisk>> /pass:<storage key>

    # Mount the Azure file share as Z: drive on the VM. You can chose another drive letter if you wish
    net use z:  \\<mydatadisk>.file.core.windows.net\<<teamsharename>>


Teď můžete přistupovat tato jednotka, stejně jako u jakékoli jednotky na virtuálním počítači.

## <a name="6-share-code-with-your-team-using-github"></a>6. Sdílejte kód se svým týmem pomocí Githubu
GitHub je úložiště kódu místo, kde najdete řadu ukázkový kód a zdrojů k různým nástrojům pomocí různých technologií sdílené komunitou vývojářů. Git jako technologie používá ke sledování a uložené verze souborů s kódem. GitHub je také platforma kde si můžete vytvořit vlastního úložiště k uložení sdíleného kódu vašeho týmu a dokumentaci, implementaci správy verzí a také určují, kteří mají přístup k zobrazení a přispívání kódu. Přejděte [stránek nápovědy Githubu](https://help.github.com/) pro další informace o používání Git. Můžete jako jeden ze způsobů, jak spolupracovat se svým týmem, použít kód vyvinutý komunitou a kód zpětně přispívat ke komunitě GitHub.

Datové VĚDY už obsahuje načtené pomocí klientských nástrojů u obou příkazového řádku jako dobře grafického uživatelského rozhraní pro přístup k úložišti GitHub. Nástroj příkazového řádku pro práci s Git a GitHub se nazývá Git Bash. Na datové VĚDY nainstalovanou sadu Visual Studio obsahuje rozšíření Git. Ikony objem požadovaný při spuštění těchto nástrojů v nabídce start a na ploše.

Pokud chcete stáhnout kód z úložiště GitHub, můžete použít ```git clone``` příkazu. Například chcete-li stáhnout data science úložiště vydávaný microsoftem do aktuálního adresáře spustíte následující příkaz se po přihlášení ```git-bash```.

    git clone https://github.com/Azure/DataScienceVM.git

V sadě Visual Studio můžete provádět stejné operace klonování. Na následujícím snímku obrazovky ukazuje, jak získat přístup ke Gitu a Githubu nástroje v sadě Visual Studio.

![Git v sadě Visual Studio](./media/vm-do-ten-things/VSGit.PNG)

Můžete najít další informace o používání Git pracovat s úložišti GitHub z několika zdrojů, které jsou k dispozici na webu github.com. [Tahák](https://services.github.com/on-demand/downloads/github-git-cheat-sheet.pdf) je užitečný odkaz.

## <a name="7-access-various-azure-data-and-analytics-services"></a>7. Přístup k různým dat a analýza služby Azure
### <a name="azure-blob"></a>Azure Blob
Objektů blob v Azure je spolehlivé, úsporné cloudové úložiště pro data velké a malé. Tato část popisuje, jak přesunout data do objektů Blob v Azure a přístup k datům uloženým v objektu Blob Azure.

**Požadavek**

* **Vytvoření účtu Azure Blob storage z [webu Azure portal](https://portal.azure.com).**

![Create_Azure_Blob](./media/vm-do-ten-things/Create_Azure_Blob.PNG)

* Potvrďte, že předinstalované nástroje příkazového řádku AzCopy se nachází v umístění ```C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy.exe```. Adresář obsahující azcopy.exe už ve vaší proměnné prostředí PATH, abyste nemuseli zadávat cestu celý příkaz při spuštění tohoto nástroje. Další informace o nástroj AzCopy najdete [dokumentaci k AzCopy](../../storage/common/storage-use-azcopy.md)
* Spusťte nástroj Průzkumník služby Azure Storage. Můžete ho stáhnout z [Microsoft Azure Storage Explorer](http://storageexplorer.com/). 

![AzureStorageExplorer_v4](./media/vm-do-ten-things/AzureStorageExplorer_v4.png)

**Přesun dat z virtuálního počítače do objektů Blob v Azure: AzCopy**

Pro přesun dat mezi vaší místní soubory a úložiště objektů blob, můžete použít nástroj AzCopy do příkazového řádku nebo Powershellu:

    AzCopy /Source:C:\myfolder /Dest:https://<mystorageaccount>.blob.core.windows.net/<mycontainer> /DestKey:<storage account key> /Pattern:abc.txt

Nahraďte **C:\myfolder** na cestu, kde je soubor uložen, **mystorageaccount** na název účtu úložiště objektů blob, **mycontainer** názvu kontejneru **klíč účtu úložiště** na přístupový klíč k úložišti objektů blob. Můžete najít přihlašovací údaje účtu úložiště v [webu Azure portal](https://portal.azure.com).

![StorageAccountCredential_v2](./media/vm-do-ten-things/StorageAccountCredential_v2.png)

Spuštěním příkazu AzCopy v Powershellu nebo z příkazového řádku. Tady je použití příkazu AzCopy:

    # Copy *.sql from local machine to a Azure Blob
    "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:"c:\Aaqs\Data Science Scripts" /Dest:https://[ENTER STORAGE ACCOUNT].blob.core.windows.net/[ENTER CONTAINER] /DestKey:[ENTER STORAGE KEY] /S /Pattern:*.sql

    # Copy back all files from Azure Blob container to Local machine

    "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Dest:"c:\Aaqs\Data Science Scripts\temp" /Source:https://[ENTER STORAGE ACCOUNT].blob.core.windows.net/[ENTER CONTAINER] /SourceKey:[ENTER STORAGE KEY] /S



Po spuštění příkazu AzCopy pro kopírování do objektu blob Azure, zobrazí se že váš soubor se zobrazí v Průzkumníku služby Azure Storage za chvíli.

![AzCopy_run_finshed_Storage_Explorer_v3](./media/vm-do-ten-things/AzCopy_run_finshed_Storage_Explorer_v3.png)

**Přesun dat z virtuálního počítače do objektů Blob v Azure: Průzkumník služby Azure Storage**

Můžete také nahrát data z místního souboru ve virtuálním počítači pomocí Průzkumníka služby Azure Storage:

* Jak nahrát data do kontejneru, vyberte cílový kontejner a klikněte na tlačítko **nahrát** tlačítko.![ Nahrát v Průzkumníku služby Storage](./media/vm-do-ten-things/storage-accounts.png)
* Klikněte na **...**  napravo od **soubory** , vyberte jeden nebo více souborů ze systému souborů a klikněte na **nahrát** zahajte nahrávání souborů.![ Nahrání souborů do objektu blob](./media/vm-do-ten-things/upload-files-to-blob.png)

**Čtení dat z objektů Blob v Azure: modul čtečky Machine Learning**

V Azure Machine Learning Studio, můžete použít **modulu Import dat** přečíst data z objektu blob služby.

![AML_ReaderBlob_Module_v3](./media/vm-do-ten-things/AML_ReaderBlob_Module_v3.png)

**Čtení dat z objektů Blob v Azure: Python ODBC**

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

Potom zapojte přihlašovacích údajů k účtu Azure Blob a čtení dat z objektu Blob:

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

Data je určen pro čtení jako datový rámec:

![IPNB_data_readin](./media/vm-do-ten-things/IPNB_data_readin.PNG)

### <a name="azure-data-lake"></a>Azure Data Lake
Azure Data Lake Storage je vysoce škálovatelné úložiště pro úlohy analýzy velkých objemů dat a kompatibilní s HDFS Hadoop Distributed File System (). Funguje s Hadoop, Spark a Azure Data Lake Analytics. V této části se dozvíte, jak můžete přesunout data do Azure Data Lake Store a spouštění analýz pomocí Azure Data Lake Analytics.

**Požadavek**

* Vytvořit váš Azure Data Lake Analytics v [webu Azure portal](https://portal.azure.com).

![Azure_Data_Lake_Create_v2](./media/vm-do-ten-things/Azure_Data_Lake_Create_v2.png)

* **Nástroje Azure Data Lake** v **sady Visual Studio** nalezený na to [odkaz](https://www.microsoft.com/download/details.aspx?id=49504) je již nainstalována na Visual Studio Community Edition, který je na virtuálním počítači. Po spuštění sady Visual Studio a protokolování ve vašem předplatném Azure, měli byste vidět váš účet Azure Data Analytics a úložiště na levém panelu sady Visual Studio.

![Azure_Data_Lake_PlugIn_v2](./media/vm-do-ten-things/Azure_Data_Lake_PlugIn_v2.PNG)

**Přesun dat z virtuálního počítače do Data Lake: Průzkumník služby Azure Data Lake**

Můžete použít **Průzkumníka služby Azure Data Lake** k odeslání dat z místních souborů ve vašem virtuálním počítači do úložiště Data Lake.

![Azure_Data_Lake_UploadData](./media/vm-do-ten-things/Azure_Data_Lake_UploadData.PNG)

Můžete také sestavovat datové kanály pro zprovoznění vašeho přesunu dat do nebo z aplikace pomocí Azure Data Lake [Azure Data Factory(ADF)](https://azure.microsoft.com/services/data-factory/). Projít tento [článku](https://azure.microsoft.com/blog/creating-big-data-pipelines-using-azure-data-lake-and-azure-data-factory/) vás provede kroky pro vytváření dat kanály.

**Čtení dat z Azure Blob do Data Lake: U-SQL**

Pokud máte data uložená v úložišti objektů Blob v Azure, můžete přímo číst data z objektu blob Azure storage v dotazu U-SQL. Před sestavování dotazu U-SQL, ujistěte se, že svůj účet blob storage je propojený s vaší Azure Data Lake. Přejděte na **webu Azure portal**, najít řídicí panel Azure Data Lake Analytics, klikněte na tlačítko **přidat zdroj dat**, vyberte typ úložiště, **služby Azure Storage** a připojte ve vašem účtu úložiště Azure Název a klíč. Potom budete moct odkazují na data uložená v účtu úložiště.

![Zadejte účet úložiště a klíč](./media/vm-do-ten-things/Link_Blob_to_ADLA_v2.PNG)

V sadě Visual Studio můžete číst data z úložiště objektů blob, provádět některé manipulace s daty, návrh funkcí a výstupní Výsledná data do Azure Blob Storage nebo Azure Data Lake. Při odkazování na data ve službě blob storage, použijte **wasb: / /**; když odkazují na data ve službě Azure Data Lake, použijte **swbhdfs: / /**

![Datový rámec](./media/vm-do-ten-things/USQL_Read_Blob_v2.PNG)

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



Po váš dotaz se odešle na server, zobrazí se diagram znázorňující stav vaší úlohy.

![Diagram stavu úloh](./media/vm-do-ten-things/USQL_Job_Status.PNG)

**Dotazování dat ve službě Data Lake: U-SQL**

Po datové sady se ingestuje do Azure Data Lake, můžete použít [jazyk U-SQL](../../data-lake-analytics/data-lake-analytics-u-sql-get-started.md) na dotazování a prozkoumávání data. Jazyk U-SQL je podobný T-SQL, ale jsou k dispozici některé funkce z jazyka C# tak, aby uživatelé můžou zadat vlastní moduly, uživatelem definované funkce a atd. Můžete použít skripty v předchozím kroku.

Po dotazu se odesílá na server, tripdata_summary. CSV mohou nacházet za chvíli **Průzkumníka služby Azure Data Lake**, může náhled dat kliknutím pravým tlačítkem myši soubor.

![Soubor v Azure Data Lake Explorer](./media/vm-do-ten-things/USQL_create_summary.png)

Chcete-li zobrazit informace o souboru:

![Souhrn souborů](./media/vm-do-ten-things/USQL_tripdata_summary.png)

### <a name="hdinsight-hadoop-clusters"></a>Clustery HDInsight Hadoop
Azure HDInsight je spravovaná služba Apache Hadoop, Spark, HBase a Storm v cloudu. Snadno mohli pracovat s clustery Azure HDInsight z virtuálního počítače pro datové vědy.

**Požadavek**

* Vytvoření účtu Azure Blob storage z [webu Azure portal](https://portal.azure.com). Tento účet úložiště se používá k ukládání dat pro clustery HDInsight.

![Vytvoření účtu úložiště objektů Blob v Azure](./media/vm-do-ten-things/Create_Azure_Blob.PNG)

* Přizpůsobení clusterů systému Hadoop HDInsight Azure [webu Azure portal](../team-data-science-process/customize-hadoop-cluster.md)
  
  * Propojí účet služby storage vytvořené pomocí vašeho clusteru HDInsight při jeho vytvoření. Tento účet úložiště se používá pro přístup k datům, které mohou být zpracovány v rámci clusteru.

![Odkaz na účet úložiště vytvořený s clusterem HDInsight](./media/vm-do-ten-things/Create_HDI_v4.PNG)

* Povolit **vzdáleného přístupu** k hlavnímu uzlu clusteru po jeho vytvoření. Pamatovat přihlašovací údaje vzdáleného přístupu, které zadáte tady, kdykoli budete potřebovat v následujícím postupu.

![Povolit vzdálený přístup](./media/vm-do-ten-things/Create_HDI_dashboard_v3.PNG)

* Vytvoření pracovního prostoru Azure Machine Learning. Z tohoto pracovního prostoru Machine Learning se ukládají vaše experimenty strojového učení. Vyberte zvýrazněnou možností na portálu, jak je znázorněno na následujícím snímku obrazovky:

![Vytvoření pracovního prostoru Azure Machine Learningu](./media/vm-do-ten-things/Create_ML_Space.PNG)

* Zadejte parametry pro váš pracovní prostor

![Zadejte parametry pracovního prostoru Machine Learning](./media/vm-do-ten-things/Create_ML_Space_step2_v2.PNG)

* Nahrání dat pomocí IPython Notebook. Nejprve importujte požadované balíčky, zapojte přihlašovací údaje, vytvoření databáze v účtu úložiště a načtení dat do clusterů Hdinsight.

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


* Alternativně můžete použít tento [návod](../team-data-science-process/hive-walkthrough.md) odesílat data taxislužby NYC do clusteru Hdinsight. Hlavní postup je následující:
  
  * AzCopy: Stáhněte ZIP CSV z veřejné objektu blob do místní složky
  * AzCopy: nahrání rozzipovaný sdílených svazcích clusteru z místní složky na cluster Hdinsight
  * Přihlaste se k hlavnímu uzlu clusteru Hadoop a připravit pro analýzu dat průzkumného testování

Po načtení dat do clusteru HDI můžete zkontrolovat, vaše data v Průzkumníku služby Azure Storage. A budete mít databázi nyctaxidb vytvořené v clusteru Hdinsight.

**Zkoumání dat: dotazů Hive v Pythonu**

Vzhledem k tomu, že data jsou v clusteru Hadoop, můžete se připojit k databázi clustery Hadoop a dotazování pomocí Hive ke zkoumání a konstruování pyodbc balíčku. Zobrazí se existující tabulky, které jsme vytvořili v kroku požadavků.

    queryString = """
        show tables in nyctaxidb2;
        """
    pd.read_sql(queryString,connection)


![Zobrazení existujících tabulek](./media/vm-do-ten-things/Python_View_Existing_Tables_Hive_v3.PNG)

Podívejme se na počet záznamů, které každý měsíc a frekvence šikmý nebo není v tabulce o jízdách:

    queryString = """
        select month, count(*) from nyctaxidb.trip group by month;
        """
    results = pd.read_sql(queryString,connection)

    %matplotlib inline

    results.columns = ['month', 'trip_count']
    df = results.copy()
    df.index = df['month']
    df['trip_count'].plot(kind='bar')


![Diagram počet záznamů v každém měsíci](./media/vm-do-ten-things/Exploration_Number_Records_by_Month_v3.PNG)

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


![Diagram frekvencí tip](./media/vm-do-ten-things/Exploration_Frequency_tip_or_not_v3.PNG)

Můžete také compute vzdálenost mezi výstupní umístění a dropoff umístění a pak porovnejte s vzdálenost o jízdách.

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


![Sbírat míčky a dropoff tabulky](./media/vm-do-ten-things/Exploration_compute_pickup_dropoff_distance_v2.PNG)

    results.columns = ['pickup_longitude', 'pickup_latitude', 'dropoff_longitude',
                       'dropoff_latitude', 'trip_distance', 'trip_time_in_secs', 'direct_distance']
    df = results.loc[results['trip_distance']<=100] #remove outliers
    df = df.loc[df['direct_distance']<=100] #remove outliers
    plt.scatter(df['direct_distance'], df['trip_distance'])


![Diagram vyzvednutí/dropoff vzdálenost, o jízdách vzdálenosti](./media/vm-do-ten-things/Exploration_direct_distance_trip_distance_v2.PNG)

Nyní Pojďme Příprava předvýpočtem zredukovaných (1 %) sady dat pro modelování. Tato data můžete použít v modulu reader Machine Learning.

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

Po chvíli uvidíte, že data byla načtena clustery Hadoop:

    queryString = """
        select * from nyctaxi_downsampled_dataset limit 10;
        """
    cursor.execute(queryString)
    pd.read_sql(queryString,connection)


![Tabulka dat](./media/vm-do-ten-things/DownSample_Data_For_Modeling_v2.PNG)

**Čtení dat z Hdinsight pomocí služby Machine Learning: modul čtečky**

Můžete také použít **čtečky** modulu v nástroji Machine Learning Studio pro přístup k databázi v clusteru Hadoop. Připojte přihlašovací údaje clustery Hdinsight a účet služby Azure Storage umožňuje vytváření modelů strojového učení pomocí databáze v clusterech Hdinsight.

![Vlastnosti modulu Reader](./media/vm-do-ten-things/AML_Reader_Hive.PNG)

Scored datovou sadu můžete prohlížet:

![Zobrazit skóre datovou sadu](./media/vm-do-ten-things/AML_Model_Results.PNG)

### <a name="azure-sql-data-warehouse--databases"></a>Azure SQL Data Warehouse & databáze
Azure SQL Data Warehouse je elastický datový sklad jako služba s prostředí SQL serveru na podnikové úrovni.

Azure SQL Data Warehouse můžete zřídit podle pokynů uvedených v tomto [článku](../../sql-data-warehouse/sql-data-warehouse-get-started-provision.md). Jakmile zřídíte Azure SQL Data Warehouse, můžete to [návod](../team-data-science-process/sqldw-walkthrough.md) provedete nahrávání dat, zkoumání a modelování pomocí dat v SQL Data Warehouse.

#### <a name="azure-cosmos-db"></a>Azure Cosmos DB
Azure Cosmos DB je databáze NoSQL v cloudu. Umožňuje pracovat s dokumenty, jako jsou JSON a umožňuje ukládat a dotazovat se dokumenty.

Tady jsou kroky na požadavky pro přístup ke službě Azure Cosmos DB z datové VĚDY:

1. Azure Cosmos DB Python SDK je již nainstalována na datové VĚDY (Spustit ```pip install pydocumentdb --upgrade``` z příkazového řádku k aktualizaci)
2. Vytvoření účtu služby Azure Cosmos DB a databázi z [webu Azure portal](https://portal.azure.com)
3. Stahování "Nástroj pro migraci Azure Cosmos DB" [tady](http://www.microsoft.com/downloads/details.aspx?FamilyID=cda7703a-2774-4c07-adcc-ad02ddc1a44d) a extrahování na adresář podle vašeho výběru
4. Import dat JSON (sopka data) uložených v [veřejných objektů blob](https://cahandson.blob.core.windows.net/samples/volcano.json) do služby Cosmos DB pomocí následujících parametrů příkazu pro nástroj pro migraci (dtui.exe z adresáře, kam jste nainstalovali nástroj pro migraci Cosmos DB). Zadejte umístění zdrojové a cílové s těmito parametry:
   
    `/s:JsonFile /s.Files:https://cahandson.blob.core.windows.net/samples/volcano.json /t:DocumentDBBulk /t.ConnectionString:AccountEndpoint=https://[DocDBAccountName].documents.azure.com:443/;AccountKey=[[KEY];Database=volcano /t.Collection:volcano1`

Jakmile importujete data, můžete přejít do aplikace Jupyter a otevřete Poznámkový blok s názvem *DocumentDBSample* , která obsahuje kód pythonu pro přístup k Azure Cosmos DB a provádět některé základní dotazování. Další informace o službě Cosmos DB návštěvou službu [stránky dokumentace](https://docs.microsoft.com/azure/cosmos-db/).

## <a name="8-build-reports-and-dashboard-using-the-power-bi-desktop"></a>8. Vytvářejte sestavy a řídicí panel pomocí Power BI Desktopu
Můžete vizualizovat soubor sopka JSON z předchozího příkladu Cosmos DB v Power BI získat vizuální přehled o datech. Podrobné kroky postupu jsou k dispozici v [Power BI článku](../../cosmos-db/powerbi-visualize.md). Tady jsou hlavní kroky:

1. Otevřete Power BI Desktopu a "získat Data." Zadejte adresu URL jako: https://cahandson.blob.core.windows.net/samples/volcano.json
2. Měli byste vidět importovaných jako seznam záznamů JSON
3. Převést seznam na tabulku, abyste Power BI můžete pracovat se stejným
4. Rozbalit sloupce kliknutím na ikonu rozbalení (jeden s ikonou "šipku vlevo a šipka vpravo" napravo od sloupce)
5. Všimněte si, že umístění je pole "Záznamu". Rozbalte záznam a vyberte pouze souřadnice. Souřadnice je sloupec seznamu
6. Přidat nový sloupec, který chcete převést souřadnice sloupec seznamu čárkami samostatný LatLong sloupec zřetězení dvou prvků v poli souřadnic seznamu pomocí vzorce ```Text.From([coordinates]{1})&","&Text.From([coordinates]{0})```.
7. Nakonec převeďte ```Elevation``` sloupec na desítkové číslo a vyberte **Zavřít** a **použít**.

Místo předchozích krocích, můžou vložte následující kód, že skripty out kroky používají v rozšířeném editoru v Power BI, který umožňuje psát transformace dat v dotazovacím jazyce.

    let
        Source = Json.Document(Web.Contents("https://cahandson.blob.core.windows.net/samples/volcano.json")),
        #"Converted to Table" = Table.FromList(Source, Splitter.SplitByNothing(), null, null, ExtraValues.Error),
        #"Expanded Column1" = Table.ExpandRecordColumn(#"Converted to Table", "Column1", {"Volcano Name", "Country", "Region", "Location", "Elevation", "Type", "Status", "Last Known Eruption", "id"}, {"Volcano Name", "Country", "Region", "Location", "Elevation", "Type", "Status", "Last Known Eruption", "id"}),
        #"Expanded Location" = Table.ExpandRecordColumn(#"Expanded Column1", "Location", {"coordinates"}, {"coordinates"}),
        #"Added Custom" = Table.AddColumn(#"Expanded Location", "LatLong", each Text.From([coordinates]{1})&","&Text.From([coordinates]{0})),
        #"Changed Type" = Table.TransformColumnTypes(#"Added Custom",{{"Elevation", type number}})
    in
        #"Changed Type"



Teď máte data v datovém modelu Power BI. Power BI desktopu by měl vypadat takto:

![Power BI Desktop](./media/vm-do-ten-things/PowerBIVolcanoData.png)

Můžete začít vytvářet sestavy a vizualizace pomocí modelu. Můžete postupovat podle kroků v tomto [Power BI článku](../../cosmos-db/powerbi-visualize.md#build-the-reports) vytvářet sestavy. Výstup je sestava, která vypadá nějak takto.

![Power BI Desktopu zobrazení sestavy – konektor Power BI](./media/vm-do-ten-things/power_bi_connector_pbireportview2.png)

## <a name="9-dynamically-scale-your-dsvm-to-meet-your-project-needs"></a>9. Dynamicky škálujte vaše DSVM podle svých potřeb projektu
Je možné škálovat nahoru a dolů podle svých potřeb projektu datové VĚDY. Pokud není nutné používat virtuální počítač v večer nebo o víkendech, vám stačí vypnout virtuální počítač z [webu Azure portal](https://portal.azure.com).

> [!NOTE]
> Pokud používáte jenom operační systém tlačítko vypnutí na virtuálním počítači se vám účtovat poplatky za výpočty.  
> 
> 

Pokud potřebujete zpracovávat rozsáhlé analýzy a potřebujete větší kapacitu procesoru nebo paměti a/nebo disk najdete velké řadu velikostí virtuálních počítačů z hlediska Procesorových jader, založený na grafickém procesoru instancí pro obsáhlý learning, kapacita paměti a disku typy (včetně disků SSD) které splňují výpočetní prostředky a rozpočtu potřebám. Úplný seznam virtuálních počítačů spolu s jejich hodinovou cenu výpočetních prostředků je k dispozici na [ceny služby Azure Virtual Machines](https://azure.microsoft.com/pricing/details/virtual-machines/) stránky.

Podobně pokud se snižuje potřeba kapacitu zpracování virtuálních počítačů (například: přesunout hlavních úloh Hadoop nebo Spark cluster), můžete vertikálně snížit kapacitu clusteru ze [webu Azure portal](https://portal.azure.com) a přejděte na nastavení vaše instance virtuálních počítačů. Zde je snímek obrazovky.

![Nastavení instance virtuálního počítače](./media/vm-do-ten-things/VMScaling.PNG)

## <a name="10-install-additional-tools-on-your-virtual-machine"></a>10. Nainstalujte další nástroje ve vašem virtuálním počítači
Existuje několik nástrojů, které jsou předem součástí datové VĚDY, která může vyřešit řadu běžných data, která potřebuje analytics. Tím se ušetří čas se vyhnout museli instalovat a konfigurovat prostředí jednu po druhé a ušetřit peníze Plaťte jen za prostředky, které používají.

Další data a analýzy služby Azure profilována v tomto článku můžete zlepšit prostředí analytics můžete využít. V některých případech může vašim potřebám vyžadovat další nástroje, včetně některých proprietární nástroje třetích stran. Máte plný přístup správce na virtuálním počítači pro instalaci nové nástroje, které potřebujete. Můžete také nainstalovat další balíčky v Pythonu a r. nejsou předem nainstalované. Pro jazyk Python můžete použít buď ```conda``` nebo ```pip```. R můžete použít ```install.packages()``` v R konzole nebo pomocí integrovaného vývojového prostředí a zvolte možnost "**balíčky** -> **instalační balíčky...** ".

## <a name="summary"></a>Souhrn
To jsou jen některé věci, které vám pomůžou na Microsoft virtuální počítač pro datové vědy. Existuje mnoho dalších věcí, které vám pomůžou usnadnit efektivní analýzy prostředí.

