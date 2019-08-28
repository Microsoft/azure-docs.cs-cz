---
title: Zkoumání a modelování dat pomocí Data Science Virtual Machine
titleSuffix: Azure
description: Provádění úloh zkoumání a modelování dat na Data Science Virtual Machine.
services: machine-learning
documentationcenter: ''
author: vijetajo
manager: cgronlun
editor: cgronlun
ms.custom: seodec18
ms.assetid: 145dfe3e-2bd2-478f-9b6e-99d97d789c62
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: vijetaj
ms.openlocfilehash: 33f1d102f128f7e63d625132c9d3c3834955747b
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70099452"
---
# <a name="ten-things-you-can-do-on-the-windows-data-science-virtual-machine"></a>Deset věcí, které vám pomůžou na Windows virtuální počítač pro datové vědy

Windows Data Science Virtual Machine (DSVM) je výkonné vývojové prostředí pro datové vědy, kde můžete provádět úlohy zkoumání a modelování dat. Prostředí je už sestavené a obsahuje několik oblíbených nástrojů pro analýzu dat, které usnadňují práci s analýzou pro místní, cloudová nebo hybridní nasazení. 

DSVM úzce spolupracuje se službami Azure. Může číst a zpracovávat data, která jsou už uložená v Azure, v Azure SQL Data Warehouse, Azure Data Lake, Azure Storage nebo Azure Cosmos DB. Může také využít jiné analytické nástroje, například Azure Machine Learning a Azure Data Factory.

V tomto článku se dozvíte, jak používat DSVM k provádění úkolů s datovou vědy a k interakci s ostatními službami Azure. Tady jsou některé věci, které můžete provést jak na datové VĚDY:

- Prozkoumejte data a vývoj modelů místně na DSVM pomocí Microsoft Machine Learning Server a Pythonu.
- Pomocí poznámkového bloku Jupyter můžete experimentovat s daty v prohlížeči pomocí Pythonu 2, Pythonu 3 a Microsoft R. (Microsoft R je podniková verze R, která je navržená pro výkon.)
- Nasaďte modely vytvořené prostřednictvím R a Pythonu na Azure Machine Learning, takže klientské aplikace budou mít k vašim modelům přístup pomocí jednoduchého rozhraní webové služby.
- Prostředky Azure spravujte pomocí Azure Portal nebo PowerShellu.
- Rozšiřte prostor úložiště a sdílejte velké datové sady/kód v celém týmu tím, že vytvoříte sdílenou složku souborů Azure jako připojenou jednotku na DSVM.
- Sdílejte kód s týmem pomocí GitHubu. Přístup k úložišti pomocí předem nainstalovaných klientů git: Uživatelské rozhraní Git bash a git.
- Přístup ke službám Azure Data and Analytics, jako je Azure Blob Storage, Azure Data Lake, Azure HDInsight (Hadoop), Azure Cosmos DB, Azure SQL Data Warehouse a Azure SQL Database.
- Sestavování sestav a řídicích panelů pomocí Power BI Desktop instance, která je předem nainstalovaná na DSVM a jejich nasazení v cloudu.
- Dynamicky Škálujte své DSVM tak, aby vyhovovaly potřebám vašeho projektu.
- Nainstalujte do svého virtuálního počítače Další nástroje.   

> [!NOTE]
> Další poplatky za využití platí pro spoustu služeb úložiště dat a analýz uvedených v tomto článku. Podrobnosti najdete na stránce s [cenami Azure](https://azure.microsoft.com/pricing/) .
> 
> 

## <a name="prerequisites"></a>Požadavky

* Budete potřebovat předplatné Azure. Můžete si [zaregistrovat bezplatnou zkušební verzi](https://azure.microsoft.com/free/).
* Pokyny pro zřízení Data Science Virtual Machine v Azure Portal jsou k dispozici v [tématu Vytvoření virtuálního počítače](https://portal.azure.com/#create/microsoft-dsvm.dsvm-windowsserver-2016).


[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="explore-data-and-develop-models-with-microsoft-machine-learning-server"></a>Prozkoumejte data a vývoj modelů pomocí Microsoft Machine Learning Server
Jazyky, jako je R a Pythonu můžete provádět datové VĚDY vaší analýzy dat přímo.

Pro R můžete použít IDE, jako je RStudio, který najdete v nabídce Start nebo na ploše. Nebo můžete použít Nástroje R pro Visual Studio. Společnost Microsoft poskytuje další knihovny nad open source CRAN R, aby bylo možné škálovatelné analýzy a schopnost analyzovat data větší, než je velikost paměti povolenou v paralelním blokovém analýze. 

Pro Python můžete použít integrované vývojové prostředí jako je Visual Studio Community Edition, který obsahuje nástroje Python Tools pro předem nainstalované rozšíření Visual Studio (PTVS). Ve výchozím nastavení je v PTVS nakonfigurovaná jenom Python 3,6, kořenové prostředí conda. Pokud chcete povolit Anaconda Python 2,7, proveďte následující kroky:

1. Vytvořte vlastní prostředí pro každou verzi tak, že přejdete na **nástroje** > **Python Tools** > **Python prostředí**a potom v edici Visual Studio Community Edition vyberete **+ Custom** .
1. Zadejte popis a nastavte cestu k předponě prostředí jako **c:\anaconda\envs\python2** pro anaconda Python 2,7.
1. Chcete-li uložit prostředí, vyberte možnost **automaticky zjišťovat** > **použití** .

Další podrobnosti o tom, jak vytvářet prostředí Pythonu, najdete v [dokumentaci k PTVS](https://aka.ms/ptvsdocs) .

Teď jste nastavili vytvoření nového projektu v Pythonu. Přejít na **soubor** > **Nový** > projektPython > a vyberte typ aplikace Python, kterou vytváříte. Prostředí Python pro aktuální projekt můžete nastavit na požadovanou verzi (Python 2,7 nebo 3,6) tak, že kliknete pravým tlačítkem na **prostředí Pythonu** a pak vyberete **Přidat nebo odebrat prostředí Pythonu**. Další informace o práci s PTVS najdete v [dokumentaci k produktu](https://aka.ms/ptvsdocs).

## <a name="use-jupyter-notebooks"></a>Použití poznámkových bloků Jupyter
Jupyter Notebook poskytuje integrované vývojové prostředí (IDE) založené na prohlížeči pro zkoumání a modelování dat. V Jupyter poznámkovém bloku můžete použít Python 2, Python 3 nebo R (Open Source i Microsoft R Server).

Jupyter Notebook spustíte tak, že v nabídce **Start** nebo na ploše vyberete ikonu **Jupyter notebook** . V příkazovém řádku DSVM můžete také spustit příkaz ```jupyter notebook``` z adresáře, ve kterém máte existující poznámkové bloky nebo kde chcete vytvořit nové poznámkové bloky.  

Po spuštění Jupyter byste měli vidět adresář, který obsahuje několik ukázkových poznámkových bloků, které jsou předem zabaleny do DSVM. Nyní můžete:

* Vyberte Poznámkový blok pro zobrazení kódu.
* Spusťte každou buňku výběrem SHIFT + ENTER.
* Spusťte celý Poznámkový blok výběrem možnosti**spuštění** **buněk** > .
* Vytvořte nový Poznámkový blok tak, že vyberete ikonu Jupyter (v levém horním rohu), kliknete na tlačítko **Nový** a pak zvolíte jazyk poznámkového bloku (označovaný také jako jádra).   

> [!NOTE]
> V současné době jsou podporovány jádra Python 2,7, Python 3,6, R, Helena a PySpark v Jupyter. Jádro R podporuje programování jak v open-source R, tak i v Microsoft R.   
> 
> 

Když jste v poznámkovém bloku, můžete prozkoumat vaše data, sestavit model a testovat model pomocí zvolených knihoven.

## <a name="train-and-deploy-models-by-using-azure-machine-learning"></a>Analýza a nasazení modelů pomocí Azure Machine Learning
Po sestavení a ověření modelu je dalším krokem obvykle jeho nasazení do produkčního prostředí. Tento krok umožňuje klientským aplikacím vyvolat předpovědi modelu v reálném čase nebo na bázi dávkového režimu. Azure Machine Learning poskytuje mechanismus pro zprovoznění modelu vytvořené v R nebo Python.

Když zprovoznění svůj model v Azure Machine Learning, Webová služba je vystavená. Umožňuje klientům, aby zadávali volání REST, která předají vstupní parametry a získají předpovědi z modelu jako výstupy.   

> [!NOTE]
> Pokud jste se ještě nezaregistrovali Azure Machine Learning, můžete získat bezplatný pracovní prostor nebo standardní pracovní prostor, a to návštěvou domovské stránky [Azure Machine Learning Studio](https://studio.azureml.net/) avýběrem možnosti Začínáme.   
> 
> 

### <a name="build-and-operationalize-python-models"></a>Sestavování a zprovoznění modelů Python
Tady je fragment kódu vyvinutý v Jupyter poznámkovém bloku Pythonu, který vytváří jednoduchý model pomocí knihovny Scikit-učení:

```python
# IRIS classification
from sklearn import datasets
from sklearn import svm
clf = svm.SVC()
iris = datasets.load_iris()
X, y = iris.data, iris.target
clf.fit(X, y)
```

Metoda, která se používá k nasazení modelů Pythonu pro Azure Machine Learning zabalí předpověď modelu do funkce a upraví ho pomocí atributů poskytovaných předinstalovanou Azure Machine Learning Python Library. Atributy označují Azure Machine Learning ID pracovního prostoru, klíč rozhraní API a vstupní a návratové parametry.  

```python
from azureml import services
@services.publish(workspaceid, auth_token)
@services.types(sep_l=float, sep_w=float, pet_l=float, pet_w=float)
@services.returns(int)  # 0, or 1, or 2
def predictIris(sep_l, sep_w, pet_l, pet_w):
    inputArray = [sep_l, sep_w, pet_l, pet_w]


return clf.predict(inputArray)
```

Klienta můžete provést volání webové služby. Obálky pro pohodlí tvoří požadavky na REST API. Zde je ukázkový kód pro využívání webové služby:

```python
# Consume through web service URL and keys
from azureml import services
@services.service(url, api_key)
@services.types(sep_l = float, sep_w = float, pet_l=float, pet_w=float)
@services.returns(float)
def IrisPredictor(sep_l, sep_w, pet_l, pet_w):
pass

IrisPredictor(3,2,3,4)
```

> [!NOTE]
> V současné době je knihovna Azure Machine Learning podporována pouze v Pythonu 2,7.   
> 
> 

### <a name="build-and-operationalize-r-models"></a>Vytváření a zprovoznění modelů R
Modely R vytvořené na Data Science Virtual Machine nebo jinde můžete nasadit do Azure Machine Learning způsobem, který se podobá tomu, jak se to dělá v Pythonu. Postup je následující:

1. Vytvořte soubor Settings. JSON, který poskytne ID pracovního prostoru a ověřovací token. 
2. Napište obálku pro funkci předpovědi modelu.
3. Chcete ```publishWebService``` -li předat obálku funkce, zavolejte v knihovně Azure Machine Learning.  

Použijte následující proceduru a fragmenty kódu k nastavení, sestavení, publikování a využívání modelu jako webové služby v Azure Machine Learning.

#### <a name="set-up"></a>Nastavení

Vytvořte soubor Settings. JSON v adresáři s názvem ```.azureml``` v domovském adresáři. Zadejte parametry z pracovního prostoru Azure Machine Learning.

Tady je struktura souborů Settings. JSON:

```json
{"workspace":{
"id"                  : "ENTER YOUR AZUREML WORKSPACE ID",
"authorization_token" : "ENTER YOUR AZUREML AUTH TOKEN"
}}
```

#### <a name="build-a-model-in-r-and-publish-it-in-azure-machine-learning"></a>Sestavení modelu v jazyce R a publikovat ve službě Azure Machine Learning

```r
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
```

#### <a name="consume-the-model-deployed-in-azure-machine-learning"></a>Používání modelu nasazené ve službě Azure Machine Learning
Pokud chcete model využívat z klientské aplikace, pomocí knihovny Azure Machine Learning vyhledejte publikovanou webovou službu podle názvu. K určení koncového bodu použijte volání rozhraníAPI.`services` Pak ho prostě zavoláte `consume` fungovat a předejte mu datový rámec pro předpovídat.

Použijte následující kód pro využívání modelu publikovaného jako Azure Machine Learning webové služby:

```r
library(AzureML)
library(lme4)
ws <- workspace(config="~/.azureml/settings.json")

s <-  services(ws, name = "sleepy lm")
s <- tail(s, 1) # use the last published function, in case of duplicate function names

ep <- endpoints(ws, s)

# Try this out, and compare with raw data
ans = consume(ep, sleepstudy)$ans
```

Další informace o [balíčcích R najdete v tématu Machine Learning Studio](/azure/machine-learning/studio-module-reference/r-packages-supported-by-azure-machine-learning).

## <a name="manage-azure-resources"></a>Správa prostředků Azure
DSVM vám neumožňuje vytvářet řešení pro analýzu místně na virtuálním počítači. Také umožňuje přístup ke službám na cloudové platformě Azure. Azure nabízí několik výpočetních, úložných a datových analýz a dalších služeb, které můžete spravovat a přistupovat z DSVM.

Ke správě předplatného Azure a cloudových prostředků máte dvě možnosti:
+ Použijte svůj prohlížeč a pokračujte na [Azure Portal](https://portal.azure.com).

+ Použijte skripty prostředí PowerShell. Spusťte Azure PowerShell z zástupce na ploše nebo z nabídky **Start** . Úplné podrobnosti najdete v [dokumentaci k Microsoft Azure PowerShell](../../powershell-azure-resource-manager.md) . 

## <a name="extend-storage-by-using-shared-file-systems"></a>Rozšiřování úložiště pomocí sdílených systémů souborů
Odborníci přes data můžou sdílet velké datové sady, kód nebo jiných prostředků v rámci týmu. DSVM má k dispozici přibližně 45 GB volného místa. Pokud chcete úložiště zvětšit, můžete použít soubory Azure a buď je připojit na jednu nebo více instancí DSVM nebo k nim přistupovat pomocí REST API. Můžete také použít [Azure Portal](../../virtual-machines/windows/attach-managed-disk-portal.md) nebo pomocí [Azure PowerShell](../../virtual-machines/windows/attach-disk-ps.md) přidat další vyhrazené datové disky. 

> [!NOTE]
> Maximální místo ve sdílené složce služby soubory Azure je 5 TB. Omezení velikosti každého souboru je 1 TB. 

Pomocí tohoto skriptu v Azure PowerShell můžete vytvořit sdílenou složku souborů Azure:

```powershell
# Authenticate to Azure.
Connect-AzAccount
# Select your subscription
Get-AzSubscription –SubscriptionName "<your subscription name>" | Select-AzSubscription
# Create a new resource group.
New-AzResourceGroup -Name <dsvmdatarg>
# Create a new storage account. You can reuse existing storage account if you want.
New-AzStorageAccount -Name <mydatadisk> -ResourceGroupName <dsvmdatarg> -Location "<Azure Data Center Name For eg. South Central US>" -Type "Standard_LRS"
# Set your current working storage account
Set-AzCurrentStorageAccount –ResourceGroupName "<dsvmdatarg>" –StorageAccountName <mydatadisk>

# Create an Azure Files share
$s = New-AzStorageShare <<teamsharename>>
# Create a directory under the file share. You can give it any name
New-AzStorageDirectory -Share $s -Path <directory name>
# List the share to confirm that everything worked
Get-AzStorageFile -Share $s
```

Teď, když jste vytvořili sdílenou složku služby soubory Azure, můžete ji připojit k libovolnému virtuálnímu počítači v Azure. Doporučujeme, abyste virtuální počítač umístili do stejného datacentra Azure jako účet úložiště, abyste se vyhnuli poplatkům za latenci a přenos dat. Tady jsou Azure PowerShell příkazy k připojení jednotky na DSVM:

```powershell
# Get the storage key of the storage account that has the Azure Files share from the Azure portal. Store it securely on the VM to avoid being prompted in the next command.
cmdkey /add:<<mydatadisk>>.file.core.windows.net /user:<<mydatadisk>> /pass:<storage key>

# Mount the Azure Files share as drive Z on the VM. You can choose another drive letter if you want.
net use z:  \\<mydatadisk>.file.core.windows.net\<<teamsharename>>
```

Teď můžete přistupovat tato jednotka, stejně jako u jakékoli jednotky na virtuálním počítači.

## <a name="share-code-in-github"></a>Sdílení kódu na GitHubu
GitHub je úložiště kódu, kde můžete najít ukázky kódu a zdroje pro různé nástroje pomocí technologií sdílených komunitou vývojářů. Git jako technologie používá ke sledování a uložené verze souborů s kódem. GitHub je také platforma, kde můžete vytvořit vlastní úložiště pro ukládání sdíleného kódu a dokumentace vašeho týmu, implementovat správu verzí a řídit, kdo má přístup k zobrazení kódu a přispívání ke kódu. 

Přejděte [stránek nápovědy Githubu](https://help.github.com/) pro další informace o používání Git. GitHub můžete použít jako jeden ze způsobů, jak spolupracovat se svým týmem, používat kód vyvinutý komunitou a přispívat kód zpátky do komunity.

DSVM se načte pomocí klientských nástrojů na příkazovém řádku a v grafickém uživatelském rozhraní pro přístup k úložišti GitHub. Nástroj příkazového řádku, který funguje s Git a GitHubem, se nazývá Git bash. Visual Studio je nainstalované na DSVM a má rozšíření Git. Ikony těchto nástrojů můžete najít v nabídce **Start** a na ploše.

Pokud chcete stáhnout kód z úložiště GitHub, můžete použít ```git clone``` příkazu. Chcete-li například stáhnout úložiště pro datové vědy publikované společností Microsoft do aktuálního adresáře, můžete spustit následující příkaz v Gitu bash:

    git clone https://github.com/Azure/DataScienceVM.git

V sadě Visual Studio můžete provádět stejné operace klonování. Následující snímek obrazovky ukazuje, jak získat přístup k nástrojům Git a GitHub v aplikaci Visual Studio:

![Snímek obrazovky sady Visual Studio se zobrazeným připojením GitHubu](./media/vm-do-ten-things/VSGit.PNG)

Další informace o tom, jak pomocí Gitu pracovat s úložištěm GitHubu, najdete v informacích dostupných na github.com. [Tahák](https://services.github.com/on-demand/downloads/github-git-cheat-sheet.pdf) je užitečný odkaz.

## <a name="access-azure-data-and-analytics-services"></a>Přístup ke službám Azure Data and Analytics
### <a name="azure-blob-storage"></a>Azure Blob Storage
Azure Blob Storage je spolehlivá, ekonomická služba pro cloudové úložiště pro velké a malé objemy dat. Tato část popisuje, jak můžete přesouvat data do úložiště objektů BLOB a přistupovat k datům uloženým v objektu blob Azure.

#### <a name="prerequisites"></a>Požadavky

* Vytvořte účet Azure Blob Storage z [Azure Portal](https://portal.azure.com).

   ![Snímek obrazovky procesu vytváření účtu úložiště v Azure Portal](./media/vm-do-ten-things/Create_Azure_Blob.PNG)

* Potvrďte, že je nástroj příkazového řádku AzCopy předem nainstalován: ```C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy.exe```. Adresář, který obsahuje AzCopy. exe, již je ve vaší proměnné prostředí PATH, takže se můžete vyhnout psaní úplné cesty k příkazu při spuštění tohoto nástroje. Další informace o nástroji AzCopy najdete v [dokumentaci k AzCopy](../../storage/common/storage-use-azcopy.md).
* Spusťte nástroj Průzkumník služby Azure Storage. Můžete si ho stáhnout z [webové stránky Průzkumník služby Storage](https://storageexplorer.com/). 

   ![Snímek obrazovky Průzkumník služby Azure Storage přístupu k účtu úložiště](./media/vm-do-ten-things/AzureStorageExplorer_v4.png)

#### <a name="move-data-from-a-vm-to-an-azure-blob-azcopy"></a>Přesunutí dat z virtuálního počítače do objektu blob Azure: AzCopy

Pokud chcete přesunout data mezi místními soubory a úložištěm objektů blob, můžete použít AzCopy v příkazovém řádku nebo v PowerShellu:

    AzCopy /Source:C:\myfolder /Dest:https://<mystorageaccount>.blob.core.windows.net/<mycontainer> /DestKey:<storage account key> /Pattern:abc.txt

Nahraďte **C:\MyFolder** cestou, kde je soubor uložený, **mystorageaccount** s názvem vašeho účtu služby Blob Storage, **myContainer** s názvem kontejneru a **klíčem účtu úložiště** pomocí přístupového klíče k úložišti objektů BLOB. Přihlašovací údaje účtu úložiště najdete v [Azure Portal](https://portal.azure.com).

Spusťte příkaz AzCopy v PowerShellu nebo z příkazového řádku. Tady je několik příkladů použití příkazu AzCopy:

```powershell
# Copy *.sql from a local machine to an Azure blob
"C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:"c:\Aaqs\Data Science Scripts" /Dest:https://[ENTER STORAGE ACCOUNT].blob.core.windows.net/[ENTER CONTAINER] /DestKey:[ENTER STORAGE KEY] /S /Pattern:*.sql

# Copy back all files from an Azure blob container to a local machine

"C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Dest:"c:\Aaqs\Data Science Scripts\temp" /Source:https://[ENTER STORAGE ACCOUNT].blob.core.windows.net/[ENTER CONTAINER] /SourceKey:[ENTER STORAGE KEY] /S
```

Po spuštění příkazu AzCopy ke zkopírování do objektu blob Azure se soubor zobrazí v Průzkumník služby Azure Storage.

![Snímek obrazovky s účtem úložiště, který zobrazuje nahraný soubor CSV](./media/vm-do-ten-things/AzCopy_run_finshed_Storage_Explorer_v3.png)

#### <a name="move-data-from-a-vm-to-an-azure-blob-azure-storage-explorer"></a>Přesunutí dat z virtuálního počítače do objektu blob Azure: Azure Storage Explorer

Data z místního souboru na VIRTUÁLNÍm počítači můžete také nahrát pomocí Průzkumník služby Azure Storage:

* Pokud chcete nahrát data do kontejneru, vyberte cílový kontejner a vyberte tlačítko **nahrát** . ![Snímek obrazovky s tlačítkem nahrát v Průzkumník služby Azure Storage](./media/vm-do-ten-things/storage-accounts.png)
* Vyberte tři tečky ( **...** ) napravo od pole **soubory** , vyberte jeden nebo více souborů, které se mají nahrát ze systému souborů, a vyberte **nahrát** a začněte nahrávat soubory. ![Snímek obrazovky dialogového okna nahrát soubory](./media/vm-do-ten-things/upload-files-to-blob.png)

#### <a name="read-data-from-an-azure-blob-machine-learning-reader-module"></a>Čtení dat z objektu blob Azure: Modul čtečky Machine Learning

V Azure Machine Learning Studio můžete použít modul import dat ke čtení dat z objektu BLOB.

![Snímek obrazovky modulu Import dat ve službě Machine Learning Studio](./media/vm-do-ten-things/AML_ReaderBlob_Module_v3.png)

#### <a name="read-data-from-an-azure-blob-python-odbc"></a>Čtení dat z objektu blob Azure: Python ODBC

Pomocí knihovny BlobService můžete číst data přímo z objektu BLOB v poznámkovém bloku Jupyter nebo v programu Pythonu.

Nejdřív Importujte požadované balíčky:

```python
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
```

Pak připojte přihlašovací údaje účtu služby Blob Storage a načtěte data z objektu BLOB:

```python
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

#unzip downloaded files if needed
#with zipfile.ZipFile(ZIPPEDLOCALFILE, "r") as z:
#    z.extractall(LOCALDIRECTORY)

df1 = pd.read_csv(LOCALFILE, header=0)
df1.columns = ['medallion','hack_license','vendor_id','rate_code','store_and_fwd_flag','pickup_datetime','dropoff_datetime','passenger_count','trip_time_in_secs','trip_distance','pickup_longitude','pickup_latitude','dropoff_longitude','dropoff_latitude']
print 'the size of the data is: %d rows and  %d columns' % df1.shape
```

Data jsou čtena jako datový rámec:

![Snímek obrazovky prvních 10 řádků dat.](./media/vm-do-ten-things/IPNB_data_readin.PNG)

### <a name="azure-data-lake"></a>Azure Data Lake
Azure Data Lake Storage je úložiště škálovatelné pro úlohy analýzy velkých objemů dat a je kompatibilní se systémem Hadoop systém souborů DFS (Distributed File System) (HDFS). Funguje s Hadoop, Spark a Azure Data Lake Analytics. V této části se dozvíte, jak můžete přesouvat data do Azure Data Lake Storage a spouštět analýzy pomocí Azure Data Lake Analytics.

#### <a name="prerequisites"></a>Požadavky

* Vytvořte instanci Azure Data Lake Analytics v [Azure Portal](https://portal.azure.com).

   ![Snímek obrazovky s vytvořením instance Data Lake Analytics z Azure Portal](./media/vm-do-ten-things/Azure_Data_Lake_Create_v2.png)

* [Modul plug-in Azure Data Lake a Stream Analytics nástrojů pro Visual Studio](https://www.microsoft.com/download/details.aspx?id=49504) je už na virtuálním počítači nainstalovaný v edici Visual Studio Community Edition. Po spuštění sady Visual Studio a přihlášení ke svému předplatnému Azure byste měli vidět svůj účet a úložiště Azure Data Analytics na levém panelu sady Visual Studio.

   ![Snímek obrazovky s modulem plug-in pro nástroje pro Data Lake v aplikaci Visual Studio](./media/vm-do-ten-things/Azure_Data_Lake_PlugIn_v2.PNG)

#### <a name="move-data-from-a-vm-to-data-lake-azure-data-lake-explorer"></a>Přesunout data z virtuálního počítače do Data Lake: Azure Data Lake Explorer

Pomocí aplikace Azure Data Lake Explorer můžete [Odeslat data z místních souborů na virtuálním počítači a data Lake Storage](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal)je.

Můžete také vytvořit datový kanál, který zprovoznění přesun dat do Azure Data Lake pomocí [Azure Data Factory](https://azure.microsoft.com/services/data-factory/). [Tento článek](https://azure.microsoft.com/blog/creating-big-data-pipelines-using-azure-data-lake-and-azure-data-factory/) vás provede kroky k sestavení datových kanálů.

#### <a name="read-data-from-an-azure-blob-to-data-lake-u-sql"></a>Přečtěte si data z objektu blob Azure do Data Lake: U-SQL

Pokud se vaše data nacházejí v úložišti objektů BLOB v Azure, můžete přímo číst data z objektu blob Azure v dotazu U-SQL. Než začnete vytvářet dotaz U-SQL, ujistěte se, že je váš účet Blob Storage propojený s vaší instancí Azure Data Lake. Přejít na Azure Portal, najděte řídicí panel Azure Data Lake Analytics, vyberte **Přidat zdroj dat**, vyberte typ úložiště **Azure Storage**a zapojte svůj název a klíč účtu úložiště Azure. Pak můžete odkazovat na data uložená v účtu úložiště.

![Snímek obrazovky dialogového okna Přidat zdroj dat](./media/vm-do-ten-things/Link_Blob_to_ADLA_v2.PNG)

V aplikaci Visual Studio můžete číst data z úložiště objektů blob, manipulovat s daty, inženýrské funkce a odesílat výsledná data buď do Azure Data Lake, nebo do úložiště objektů BLOB v Azure. Když odkazujete na data v úložišti objektů blob, použijte **wasb://** . Když odkazujete na data v Azure Data Lake, použijte **swbhdfs://** .

V aplikaci Visual Studio můžete použít následující dotazy U-SQL:

```usql
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
```

Po odeslání dotazu na server se v diagramu zobrazí stav úlohy.

![Snímek obrazovky diagramu stavu úlohy](./media/vm-do-ten-things/USQL_Job_Status.PNG)

#### <a name="query-data-in-data-lake-u-sql"></a>Dotazování na data v Data Lake: U-SQL

Po ingestování datové sady v Azure Data Lake můžete k dotazování a prozkoumávání dat použít [Jazyk U-SQL](../../data-lake-analytics/data-lake-analytics-u-sql-get-started.md) . Jazyk U-SQL je podobný T-SQL, ale kombinuje některé funkce, C# aby uživatelé mohli zapisovat přizpůsobené moduly a uživatelsky definované funkce. Můžete použít skripty v předchozím kroku.

Po odeslání dotazu na server tripdata_summary. Sdílený svazek clusteru se zobrazí v Průzkumníku Azure Data Lake. Náhled dat můžete zobrazit tak, že kliknete pravým tlačítkem na soubor.

![Snímek obrazovky se souborem CSV v Průzkumníkovi Data Lake](./media/vm-do-ten-things/USQL_create_summary.png)

Zobrazí se informace o souboru:

![Snímek obrazovky souhrnné informace o souboru](./media/vm-do-ten-things/USQL_tripdata_summary.png)

### <a name="hdinsight-hadoop-clusters"></a>Clustery HDInsight Hadoop
Azure HDInsight je spravovaný Apache Hadoop, Spark, HBA a služba v cloudu. Můžete snadno pracovat s clustery Azure HDInsight z Data Science Virtual Machine.

#### <a name="prerequisites"></a>Požadavky

* Vytvořte účet Azure Blob Storage z [Azure Portal](https://portal.azure.com). Tento účet úložiště se používá k ukládání dat pro clustery HDInsight.

   ![Snímek obrazovky s vytvořením účtu úložiště z Azure Portal](./media/vm-do-ten-things/Create_Azure_Blob.PNG)

* Přizpůsobte Azure HDInsight Hadoop clusterů z [Azure Portal](../team-data-science-process/customize-hadoop-cluster.md).
  
   Propojte účet úložiště vytvořený s clusterem HDInsight při jeho vytvoření. Tento účet úložiště se používá pro přístup k datům, které mohou být zpracovány v rámci clusteru.

   ![Výběry pro propojení účtu úložiště vytvořeného s clusterem HDInsight](./media/vm-do-ten-things/Create_HDI_v4.PNG)

* Po vytvoření pro hlavní uzel clusteru povolte přístup ke vzdálené ploše. Zapamatování přihlašovacích údajů vzdáleného přístupu, které tady zadáte, protože je budete potřebovat v dalším postupu.

   ![Tlačítko Vzdálená plocha pro povolení vzdáleného přístupu ke clusteru HDInsight](./media/vm-do-ten-things/Create_HDI_dashboard_v3.PNG)

* Vytvoření pracovního prostoru Azure Machine Learning. Vaše experimenty Machine Learning jsou uloženy v tomto pracovním prostoru Machine Learning. Na portálu vyberte zvýrazněné možnosti, jak je znázorněno na následujícím snímku obrazovky:

   ![Vytvoření pracovního prostoru Azure Machine Learningu](./media/vm-do-ten-things/Create_ML_Space.PNG)

* Zadejte parametry pro váš pracovní prostor.

   ![Zadejte parametry pracovního prostoru Machine Learning](./media/vm-do-ten-things/Create_ML_Space_step2_v2.PNG)

* Nahrajte data pomocí poznámkového bloku IPython. Importujte požadované balíčky, připojte přihlašovací údaje, vytvořte databázi v účtu úložiště a pak načtěte data do clusterů HDI.

```python
# Import required packages
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


# Create the connection to Hive by using ODBC
SERVER_NAME = 'xxx.azurehdinsight.net'
DATABASE_NAME = 'nyctaxidb'
USERID = 'xxx'
PASSWORD = 'xxxx'
DB_DRIVER = 'Microsoft Hive ODBC Driver'
driver = 'DRIVER={' + DB_DRIVER + '}'
server = 'Host=' + SERVER_NAME + ';Port=443'
database = 'Schema=' + DATABASE_NAME
hiveserv = 'HiveServerType=2'
auth = 'AuthMech=6'
uid = 'UID=' + USERID
pwd = 'PWD=' + PASSWORD
CONNECTION_STRING = ';'.join(
    [driver, server, database, hiveserv, auth, uid, pwd])
connection = pyodbc.connect(CONNECTION_STRING, autocommit=True)
cursor = connection.cursor()


# Create the Hive database and tables
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


# Upload data from Blob storage to an HDI cluster
for i in range(1, 13):
    queryString = "LOAD DATA INPATH 'wasb:///nyctaxitripraw2/trip_data_%d.csv' INTO TABLE nyctaxidb2.trip PARTITION (month=%d);" % (
        i, i)
    cursor.execute(queryString)
    queryString = "LOAD DATA INPATH 'wasb:///nyctaxifareraw2/trip_fare_%d.csv' INTO TABLE nyctaxidb2.fare PARTITION (month=%d);" % (
        i, i)
    cursor.execute(queryString)
```

Případně můžete postupovat podle [tohoto návodu](../team-data-science-process/hive-walkthrough.md) k nahrání TAXISLUŽBY dat NYC do clusteru HDI. Hlavní postup je následující:
  
* Pomocí AzCopy si stáhněte z veřejného objektu blob do místní složky CSV zip.
* Pomocí AzCopy nahrajte CSV z místní složky do clusteru HDI.
* Přihlaste se k hlavnímu uzlu clusteru Hadoop a připravte se na analýzu dat pro průzkumné testování.

Po načtení dat do clusteru HDI můžete data kontrolovat v Průzkumník služby Azure Storage. A databáze nyctaxidb se vytvořila v clusteru HDI.

#### <a name="data-exploration-hive-queries-in-python"></a>Zkoumání dat: Dotazy na podregistr v Pythonu

Vzhledem k tomu, že jsou data v clusteru Hadoop, můžete použít balíček pyodbc pro připojení ke clusterům Hadoop a dotazování databází pomocí podregistru pro průzkum a strojírenství funkcí. Můžete zobrazit existující tabulky, které jste vytvořili v kroku požadavků.

```python
queryString = """
    show tables in nyctaxidb2;
    """
pd.read_sql(queryString, connection)
```

![Zobrazení existujících tabulek](./media/vm-do-ten-things/Python_View_Existing_Tables_Hive_v3.PNG)

Podívejme se na počet záznamů, které každý měsíc a frekvence šikmý nebo není v tabulce o jízdách:

```python
queryString = """
    select month, count(*) from nyctaxidb.trip group by month;
    """
results = pd.read_sql(queryString,connection)

%matplotlib inline

results.columns = ['month', 'trip_count']
df = results.copy()
df.index = df['month']
df['trip_count'].plot(kind='bar')
```

![Diagram počet záznamů v každém měsíci](./media/vm-do-ten-things/Exploration_Number_Records_by_Month_v3.PNG)

```python
queryString = """
    SELECT tipped, COUNT(*) AS tip_freq
    FROM
    (
        SELECT if(tip_amount > 0, 1, 0) as tipped, tip_amount
        FROM nyctaxidb.fare
    )tc
    GROUP BY tipped;
    """
results = pd.read_sql(queryString, connection)

results.columns = ['tipped', 'trip_count']
df = results.copy()
df.index = df['tipped']
df['trip_count'].plot(kind='bar')
```

![Diagram frekvencí tip](./media/vm-do-ten-things/Exploration_Frequency_tip_or_not_v3.PNG)

Můžete také vypočítat vzdálenost mezi umístěním a umístěním pro vyzvednutí a pak ji porovnat s délkou cesty.

```python
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
results = pd.read_sql(queryString, connection)
results.head(5)
```

![Horní řádky vyzvednutí a vyřazení z tabulky](./media/vm-do-ten-things/Exploration_compute_pickup_dropoff_distance_v2.PNG)

```python
results.columns = ['pickup_longitude', 'pickup_latitude', 'dropoff_longitude',
                   'dropoff_latitude', 'trip_distance', 'trip_time_in_secs', 'direct_distance']
df = results.loc[results['trip_distance'] <= 100]  # remove outliers
df = df.loc[df['direct_distance'] <= 100]  # remove outliers
plt.scatter(df['direct_distance'], df['trip_distance'])
```

![Vykreslení vzdálenosti vyzvednutí/vyřazení z polohy na cestu](./media/vm-do-ten-things/Exploration_direct_distance_trip_distance_v2.PNG)

Nyní připravujeme downsampled (1%) sadu dat pro modelování. Tato data můžete použít v modulu čtečky Machine Learning.

```python
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
```

Nyní vložte obsah spojení do předchozí interní tabulky.

```python
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
```

Po chvíli uvidíte, že se data načetla v clusterech Hadoop:

```python
queryString = """
    select * from nyctaxi_downsampled_dataset limit 10;
    """
cursor.execute(queryString)
pd.read_sql(queryString, connection)
```

![Horní řádky z tabulky dat](./media/vm-do-ten-things/DownSample_Data_For_Modeling_v2.PNG)

#### <a name="read-data-from-hdi-by-using-machine-learning-reader-module"></a>Čtení dat z HDI pomocí Machine Learning: modul čtečky

K přístupu k databázi v clusteru Hadoop můžete použít také modul čtenář v Machine Learning Studio. Připojte přihlašovací údaje ke clusterům HDI a účtu úložiště Azure, aby bylo možné vytvářet modely strojového učení pomocí databáze v clusterech HDI.

![Vlastnosti modulu Reader](./media/vm-do-ten-things/AML_Reader_Hive.PNG)

Pak můžete zobrazit vyhodnocenou datovou sadu:

![Zobrazit skóre datovou sadu](./media/vm-do-ten-things/AML_Model_Results.PNG)

### <a name="azure-sql-data-warehouse-and-databases"></a>Azure SQL Data Warehouse a databáze
Azure SQL Data Warehouse je Elastický datový sklad jako služba s využitím SQL Server na podnikové úrovni.

Službu Azure SQL Data Warehouse můžete zřídit podle pokynů v [tomto článku](../../sql-data-warehouse/sql-data-warehouse-get-started-provision.md). Po zřízení služby SQL Data Warehouse můžete pomocí [tohoto návodu](../team-data-science-process/sqldw-walkthrough.md) provádět nahrávání, zkoumání a modelování dat pomocí dat v rámci SQL Data Warehouse.

#### <a name="azure-cosmos-db"></a>Azure Cosmos DB
Azure Cosmos DB je databáze NoSQL v cloudu. Můžete ji použít k práci s dokumenty, jako je JSON, a k ukládání a dotazování dokumentů.

Pro přístup k Azure Cosmos DB z DSVM použijte následující požadované kroky:

1. Sada SDK Azure Cosmos DB Python je už v DSVM nainstalovaná. Pokud ho chcete aktualizovat, ```pip install pydocumentdb --upgrade``` spusťte z příkazového řádku.
2. Vytvořte účet Azure Cosmos DB a databázi z [Azure Portal](https://portal.azure.com).
3. Stáhněte nástroj pro migraci dat Azure Cosmos DB z webu [Microsoft Download Center](https://www.microsoft.com/downloads/details.aspx?FamilyID=cda7703a-2774-4c07-adcc-ad02ddc1a44d) a rozbalte ho do libovolného adresáře.
4. Importujte data JSON (Volcano data) uložená ve [veřejném objektu BLOB](https://cahandson.blob.core.windows.net/samples/volcano.json) do Azure Cosmos DB s použitím následujících parametrů příkazu pro nástroj pro migraci. (Použijte dtui. exe z adresáře, do kterého jste nainstalovali nástroj pro migraci dat Azure Cosmos DB.) Zadejte umístění zdrojové a cílové s těmito parametry:
   
    `/s:JsonFile /s.Files:https://cahandson.blob.core.windows.net/samples/volcano.json /t:DocumentDBBulk /t.ConnectionString:AccountEndpoint=https://[DocDBAccountName].documents.azure.com:443/;AccountKey=[[KEY];Database=volcano /t.Collection:volcano1`

Po importu dat můžete přejít na Jupyter a otevřít poznámkový blok s názvem *DocumentDBSample*. Obsahuje kód Pythonu pro přístup k Azure Cosmos DB a provede základní dotazování. Další informace o Azure Cosmos DB najdete na [stránce dokumentace](https://docs.microsoft.com/azure/cosmos-db/)ke službě.

## <a name="use-power-bi-reports-and-dashboards"></a>Použití sestav Power BI a řídicích panelů 
Soubor JSON Volcano můžete vizualizovat z předchozího Azure Cosmos DB příkladu v Power BI Desktop a získat tak vizuální přehledy o datech. Podrobné kroky postupu jsou k dispozici v [Power BI článku](../../cosmos-db/powerbi-visualize.md). Tady jsou hlavní kroky:

1. Otevřete Power BI Desktop a vyberte **získat data**. Zadejte adresu URL jako: https://cahandson.blob.core.windows.net/samples/volcano.json.
2. Měly by se zobrazit záznamy JSON importované jako seznam. Převeďte seznam na tabulku, aby Power BI s ní mohl pracovat.
4. Rozbalte sloupce tak, že vyberete ikonu rozbalit (šipka).
5. Všimněte si, že umístění je pole **záznamu** . Rozbalte záznam a vyberte pouze souřadnice. **Souřadnice** je sloupec seznamu.
6. Přidejte nový sloupec, který převede sloupec souřadnic seznamu na sloupec **LatLong** s oddělovači v podobě čárky. Zřetězení dvou prvků v poli se seznamem souřadnic pomocí vzorce ```Text.From([coordinates]{1})&","&Text.From([coordinates]{0})```.
7. Převeďte sloupec **zvýšení úrovně** na typ Decimal a vyberte tlačítka pro **zavření** a **použití** .

Místo předchozích kroků můžete vložit následující kód. Skriptuje kroky používané v Rozšířený editor v Power BI k zápisu transformací dat do dotazovacího jazyka.

```pqfl
let
    Source = Json.Document(Web.Contents("https://cahandson.blob.core.windows.net/samples/volcano.json")),
    #"Converted to Table" = Table.FromList(Source, Splitter.SplitByNothing(), null, null, ExtraValues.Error),
    #"Expanded Column1" = Table.ExpandRecordColumn(#"Converted to Table", "Column1", {"Volcano Name", "Country", "Region", "Location", "Elevation", "Type", "Status", "Last Known Eruption", "id"}, {"Volcano Name", "Country", "Region", "Location", "Elevation", "Type", "Status", "Last Known Eruption", "id"}),
    #"Expanded Location" = Table.ExpandRecordColumn(#"Expanded Column1", "Location", {"coordinates"}, {"coordinates"}),
    #"Added Custom" = Table.AddColumn(#"Expanded Location", "LatLong", each Text.From([coordinates]{1})&","&Text.From([coordinates]{0})),
    #"Changed Type" = Table.TransformColumnTypes(#"Added Custom",{{"Elevation", type number}})
in
    #"Changed Type"
```

Teď máte data v datovém modelu Power BI. Vaše Power BI Desktop instance by měla vypadat takto:

![Power BI Desktop](./media/vm-do-ten-things/PowerBIVolcanoData.png)

Pomocí datového modelu můžete začít sestavovat sestavy a vizualizace. Pomocí kroků v [tomto Power BI článku](../../cosmos-db/powerbi-visualize.md#build-the-reports) sestavíte sestavu.

## <a name="scale-the-dsvm-dynamically"></a>Dynamické škálování DSVM 
DSVM můžete škálovat nahoru a dolů tak, aby vyhovovala potřebám vašeho projektu. Pokud nepotřebujete virtuální počítač používat na večer nebo na víkendech, můžete virtuální počítač vypnout z [Azure Portal](https://portal.azure.com).

> [!NOTE]
> Poplatky za výpočetní výkon se účtují, pokud použijete jenom tlačítko vypnout pro operační systém na VIRTUÁLNÍm počítači.  
> 
> 

Může se stát, že budete potřebovat zvládnout rozsáhlou analýzu a potřebujete větší kapacitu procesoru, paměti a disku. Pokud ano, můžete najít volbu velikosti virtuálních počítačů z hlediska PROCESORových jader, instancí založených na GPU pro obsáhlý Learning, kapacitu paměti a typy disků (včetně jednotek Solid-State), které splňují vaše výpočetní a rozpočtové požadavky. Úplný seznam virtuálních počítačů a jejich hodinové výpočetní ceny jsou k dispozici na stránce s cenami služby [Azure Virtual Machines](https://azure.microsoft.com/pricing/details/virtual-machines/) .

Podobně může být potřeba snížit kapacitu pro zpracování virtuálních počítačů. (Například: přesunuli jsme hlavní zatížení do clusteru Hadoop nebo Spark.) Pak můžete rozšířit cluster od [Azure Portal](https://portal.azure.com) a přejít do nastavení instance virtuálního počítače. 

## <a name="add-more-tools"></a>Přidat další nástroje
Nástroje předem připravené do DSVM mohou řešit mnoho běžných potřeb datových analýz. Tím ušetříte čas, protože nemusíte instalovat a konfigurovat prostředí jeden po druhém. Ušetří vám také peníze, protože platíte jenom za prostředky, které využijete.

K vylepšení svého analytického prostředí můžete použít další služby Azure Data and Analytics profilované v tomto článku. V některých případech možná budete potřebovat další nástroje, včetně některých vlastních partnerských nástrojů. Máte úplný přístup správce k virtuálnímu počítači, abyste mohli nainstalovat nové nástroje, které potřebujete. Můžete také nainstalovat další balíčky v Pythonu a r. nejsou předem nainstalované. Pro Python můžete použít buď ```conda``` nebo. ```pip``` Pro R můžete ```install.packages()``` použít v konzole r, nebo použít IDE a vybrat **balíčky** > **instalovat balíčky**.

## <a name="deep-learning"></a>Hloubkové učení

Kromě ukázek založených na rozhraní můžete získat sadu komplexních návodů, které byly ověřeny na DSVM. Tyto návody vám pomůžou přejít na začátek vývoje aplikací s hloubkovým učením v doménách, jako jsou obrázky a porozumění textu nebo jazyka.   


- [Spouštění neuronovéch sítí napříč různými architekturami](https://github.com/ilkarman/DeepLearningFrameworks): Tento návod ukazuje, jak migrovat kód z jednoho rozhraní do jiného. Také ukazuje, jak porovnat modely a běhový výkon napříč platformami. 

- Návod [, jak sestavit ucelené řešení pro detekci produktů v rámci imagí](https://github.com/Azure/cortana-intelligence-product-detection-from-images): Detekce obrázku je technika, která může vyhledat a klasifikovat objekty v rámci imagí. Tato technologie umožňuje přinést velké výhody v mnoha obchodních doménách v reálném čase. Například prodejci, můžete použít tuto techniku k určení, který produkt a zákazníka má neexistoval z police. Tyto informace pomáhají zase úložiště spravovat inventář produktů. 

- [Obsáhlý Learning pro zvuk](https://blogs.technet.microsoft.com/machinelearning/2018/01/30/hearing-ai-getting-started-with-deep-learning-for-audio-on-azure/): V tomto kurzu se dozvíte, jak vytvořit model hloubkového učení pro detekci zvukových událostí v [datové sadě městských zvuků](https://serv.cusp.nyu.edu/projects/urbansounddataset/urbansound8k.html). Poskytuje také přehled o tom, jak pracovat se zvukovými daty.

- [Klasifikace textových dokumentů](https://github.com/anargyri/lstm_han): Tento návod ukazuje, jak sestavit a naučit dvě neuronové síťové architektury: Hierarchická síť a dlouhodobá krátkodobá paměť (LSTM). Tyto neuronové sítě pomocí rozhraní Keras API pro obsáhlý learning zařazoval dokumenty do textu. Keras je front-end až tři nejoblíbenější architektury hloubkového učení: Microsoft Cognitive Toolkit, TensorFlow a Theano.

## <a name="summary"></a>Souhrn
Tento článek popisuje některé z věcí, které můžete provádět na Data Science Virtual Machine Microsoftu. Existuje mnoho dalších věcí, které můžete využít k tomu, aby DSVM efektivní analytické prostředí.

