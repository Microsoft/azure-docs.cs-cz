---
title: Prozkoumání dat a modelu ve Windows
titleSuffix: Azure Data Science Virtual Machine
description: Provádějte úlohy průzkumu dat a modelování na virtuálním počítači Windows Data Science.
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 8da8cd7110cd17d0aadd52cce1263c7c0fcfdf5c
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2020
ms.locfileid: "80632174"
---
# <a name="ten-things-you-can-do-on-the-windows-data-science-virtual-machine"></a>Deset věcí, které můžete dělat na virtuálním počítači s datovými vědami windows

Windows Data Science Virtual Machine (DSVM) je výkonné vývojové prostředí datové vědy, kde můžete provádět úlohy průzkumu dat a modelování. Prostředí je již vytvořeno a dodáváno s několika oblíbenými nástroji pro analýzu dat, které usnadňují zahájení analýzy pro místní, cloudová nebo hybridní nasazení. 

DSVM úzce spolupracuje se službami Azure. Umí číst a zpracovávat data, která už jsou uložená v Azure, azure SQL Data Warehouse, Azure Data Lake, Azure Storage nebo Azure Cosmos DB. Může také využívat další analytické nástroje, jako je Azure Machine Learning a Azure Data Factory.

V tomto článku se dozvíte, jak používat DSVM k provádění úloh datové vědy a interakci s jinými službami Azure. Zde jsou některé z věcí, které můžete dělat na DSVM:

- Prozkoumejte data a vyvíjejte modely místně na DSVM pomocí Microsoft Machine Learning Server a Python.
- Pomocí poznámkového bloku Jupyter můžete experimentovat s daty v prohlížeči pomocí Pythonu 2, Pythonu 3 a Microsoftu R. (Microsoft R je verze R připravená pro podniky určená pro výkon.)
- Nasazujte modely vytvořené prostřednictvím R a Pythonu v Azure Machine Learning, aby klientské aplikace měly přístup k vašim modelům pomocí jednoduchého rozhraní webové služby.
- Spravujte prostředky Azure pomocí portálu Azure nebo PowerShellu.
- Rozšiřte svůj úložný prostor a sdílejte rozsáhlé datové sady/kódy v celém týmu vytvořením sdílené složky Azure Files jako připojitelné jednotky na vašem DSVM.
- Sdílejte kód se svým týmem pomocí GitHubu. Přístup k úložišti pomocí předinstalovaných klientů Git: Git Bash a Git GUI.
- Získejte přístup k datům a analytickým službám Azure, jako je úložiště objektů blob Azure, Azure Data Lake, Azure HDInsight (Hadoop), Azure Cosmos DB, Azure SQL Data Warehouse a Azure SQL Database.
- Vytvářejte sestavy a řídicí panel pomocí instance Power BI Desktop, která je předinstalovaná na DSVM, a nasaďte je v cloudu.
- Dynamicky škálujte svůj DSVM tak, aby vyhovoval potřebám vašeho projektu.
- Nainstalujte do virtuálního počítače další nástroje.   

> [!NOTE]
> Další poplatky za využití platí pro mnoho služeb pro ukládání dat a analýzy uvedených v tomto článku. Podrobnosti najdete na stránce [s cenami Azure.](https://azure.microsoft.com/pricing/)
> 
> 

## <a name="prerequisites"></a>Požadavky

* Potřebujete předplatné Azure. Můžete [se zaregistrovat k bezplatné zkušební verzi](https://azure.microsoft.com/free/).
* Pokyny pro zřizování virtuálního počítače pro datové vědy na webu Azure Portal jsou dostupné v [části Vytvoření virtuálního počítače](https://portal.azure.com/#create/microsoft-dsvm.dsvm-windowsserver-2016).


[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="explore-data-and-develop-models-with-microsoft-machine-learning-server"></a>Prozkoumejte data a vyvíjejte modely pomocí serveru Microsoft Machine Learning Server
Můžete použít jazyky jako R a Python, aby vaše analýzy dat přímo na DSVM.

Pro R můžete použít ide jako RStudio, které lze nalézt v nabídce Start nebo na ploše. Nebo můžete použít nástroje R pro visual studio. Společnost Microsoft poskytla další knihovny nad open source CRAN R, které umožňují škálovatelné analýzy a schopnost analyzovat data větší než velikost paměti povolenou v paralelní analýze bloků dat. 

Pro Python můžete použít ide, jako je Visual Studio Community Edition, který má python tools for Visual Studio (PTVS) rozšíření předinstalovaný. Ve výchozím nastavení je na PTVS nakonfigurován pouze Python 3.6, kořenové prostředí Conda. Chcete-li povolit Anaconda Python 2.7, postupujte takto:

1. Vytvořte vlastní prostředí pro každou verzi tak, že přejdete na **Nástroje** > **Python Tools** > **Python prostředí**a pak výběrem + **Vlastní** v Visual Studio Community Edition.
1. Uveďte popis a nastavte cestu předpony prostředí jako **c:\anaconda\envs\python2** pro Anaconda Python 2.7.
1. Chcete-li uložit prostředí, vyberte **možnost Automaticky rozpoznat** > **použít.**

Další podrobnosti o vytváření prostředí Pythonu najdete v dokumentaci k [PTVS.](https://aka.ms/ptvsdocs)

Nyní jste nastaveni na vytvoření nového projektu Pythonu. Přejděte do **souboru** > **Nový** > **projekt** > **Pythonu** a vyberte typ aplikace Pythonu, kterou vytváříte. Prostředí Pythonu pro aktuální projekt můžete nastavit na požadovanou verzi (Python 2.7 nebo 3.6) tak, že kliknete pravým tlačítkem myši na **prostředí Pythonu** a pak vyberete **Přidat nebo odebrat prostředí Pythonu**. Více informací o práci s PTVS naleznete v [dokumentaci](https://aka.ms/ptvsdocs)k produktu .

## <a name="use-jupyter-notebooks"></a>Použití poznámkových bloků Jupyter
Jupyter Notebook poskytuje ide založené na prohlížeči pro zkoumání dat a modelování. Python 2, Python 3 nebo R (open source i Microsoft R Server) můžete použít v poznámkovém bloku Jupyter.

Chcete-li spustit poznámkový blok Jupyter, vyberte ikonu **Jupyter Notebook** v nabídce **Start** nebo na ploše. V příkazovém řádku DSVM můžete ```jupyter notebook``` příkaz spustit také z adresáře, ve kterém máte existující poznámkové bloky nebo kde chcete vytvořit nové poznámkové bloky.  

Po spuštění Jupyter, měli byste vidět adresář, který obsahuje několik příkladů poznámkových bloků, které jsou předem zabaleny do DSVM. Nyní můžete:

* Vyberte poznámkový blok, abyste viděli kód.
* Spusťte každou buňku výběrem Shift+Enter.
* Spusťte celý poznámkový blok výběrem **možnosti** > **Spustit buňku**.
* Vytvořte nový poznámkový blok výběrem ikony Jupyter (levý horní roh), výběrem tlačítka **Nový** vpravo a výběrem jazyka poznámkového bloku (označovaný také jako jádra).   

> [!NOTE]
> V současné době jsou podporována jádra Python2.7, Python 3.6, R, Julia a PySpark v Jupyteru. Jádro R podporuje programování v open source R i Microsoft R.   
> 
> 

Když jste v poznámkovém bloku, můžete prozkoumat data, vytvořit model a otestovat model pomocí vybraných knihoven.

## <a name="train-and-deploy-models-by-using-azure-machine-learning"></a>Trénování a nasazování modelů pomocí Azure Machine Learning
Po sestrojit a ověřit model, dalším krokem je obvykle nasadit do produkčního prostředí. Tento krok umožňuje klientským aplikacím vyvolat předpovědi modelu v reálném čase nebo na základě dávkového režimu. Azure Machine Learning poskytuje mechanismus pro zprovoznění modelu integrovaného v R nebo Pythonu.

Když svůj model zprovozníte v Azure Machine Learning, zobrazí se webová služba. Umožňuje klientům provádět volání REST, které předávají vstupní parametry a přijímají předpovědi z modelu jako výstupy.

### <a name="build-and-operationalize-python-models"></a>Vytváření a zprovoznění modelů Pythonu
Zde je úryvek kódu vyvinutý v poznámkovém bloku Python Jupyter, který vytváří jednoduchý model pomocí knihovny Scikit-learn:

```python
# IRIS classification
from sklearn import datasets
from sklearn import svm
clf = svm.SVC()
iris = datasets.load_iris()
X, y = iris.data, iris.target
clf.fit(X, y)
```

Metoda, která se používá k nasazení modelů Pythonu do Azure Machine Learning zabalí předpověď modelu do funkce a zdobí ji atributy poskytované předinstalovanou knihovnou Azure Machine Learning Python. Atributy označují ID pracovního prostoru Azure Machine Learning, klíč rozhraní API a parametry vstupního a vráceného.  

```python
from azureml import services
@services.publish(workspaceid, auth_token)
@services.types(sep_l=float, sep_w=float, pet_l=float, pet_w=float)
@services.returns(int)  # 0, or 1, or 2
def predictIris(sep_l, sep_w, pet_l, pet_w):
    inputArray = [sep_l, sep_w, pet_l, pet_w]


return clf.predict(inputArray)
```

Klient nyní může volat do webové služby. Obálky pohodlí vytvořit požadavky rozhraní API REST. Zde je ukázkový kód pro využití webové služby:

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
> V současné době je knihovna Azure Machine Learning podporovaná jenom v Pythonu 2.7.   
> 
> 

### <a name="build-and-operationalize-r-models"></a>Vytváření a zprovoznění modelů R
Můžete nasadit R modely postavené na virtuálním počítači datové vědy nebo jinde na Azure Machine Learning způsobem, který je podobný, jak se to dělá pro Python. Postup je následující:

1. Vytvořte soubor settings.json, který poskytne ID pracovního prostoru a ověřovací token. 
2. Napište obálku pro funkci předpovědět modelu.
3. Volání ```publishWebService``` v knihovně Azure Machine Learning předat obálku funkce.  

Pomocí následujícího postupu a fragmentů kódu můžete nastavit, sestavit, publikovat a využívat model jako webovou službu v Azure Machine Learning.

#### <a name="set-up"></a>Nastavit

Vytvořte soubor settings.json pod ```.azureml``` adresářem volaným v domovském adresáři. Zadejte parametry z pracovního prostoru Azure Machine Learning.

Zde je struktura souborů settings.json:

```json
{"workspace":{
"id"                  : "ENTER YOUR AZUREML WORKSPACE ID",
"authorization_token" : "ENTER YOUR AZUREML AUTH TOKEN"
}}
```

#### <a name="build-a-model-in-r-and-publish-it-in-azure-machine-learning"></a>Sestavení modelu v R a jeho publikování v Azure Machine Learning

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

#### <a name="consume-the-model-deployed-in-azure-machine-learning"></a>Využití modelu nasazeného v Azure Machine Learning
Chcete-li využívat model z klientské aplikace, použijte knihovnu Azure Machine Learning k vyhledání publikované webové služby podle názvu. Pomocí `services` volání rozhraní API určit koncový bod. Pak stačí zavolat `consume` funkci a předat v datovém rámci, který má být předpovězen.

Pomocí následujícího kódu využijte model publikovaný jako webová služba Azure Machine Learning:

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

Další informace o [balíčcích R v Machine Learning Studiu](/azure/machine-learning/studio-module-reference/r-packages-supported-by-azure-machine-learning).

## <a name="manage-azure-resources"></a>Správa prostředků Azure
DSVM neumožňuje pouze vytvářet analytické řešení místně na virtuálním počítači. Umožňuje také přístup ke službám na cloudové platformě Azure. Azure poskytuje několik výpočetních, úložných, datových analýz a dalších služeb, které můžete spravovat a přistupovat z vašeho DSVM.

Chcete-li spravovat předplatné Azure a cloudové prostředky, máte dvě možnosti:
+ Použijte prohlížeč a přejděte na [portál Azure](https://portal.azure.com).

+ Použijte skripty prostředí PowerShell. Spusťte Azure PowerShell z zástupce na ploše nebo z nabídky **Start.** Podrobné informace najdete v [dokumentaci k Prostředí Microsoft Azure PowerShell.](../../powershell-azure-resource-manager.md) 

## <a name="extend-storage-by-using-shared-file-systems"></a>Rozšíření úložiště pomocí sdílených systémů souborů
Datoví vědci mohou sdílet velké datové sady, kód nebo jiné prostředky v rámci týmu. DSVM má k dispozici asi 45 GB místa. Chcete-li rozšířit úložiště, můžete použít soubory Azure a připojit je na jednu nebo více instancí DSVM nebo k němu přistupovat prostřednictvím rozhraní REST API. Můžete taky použít [portál Azure](../../virtual-machines/windows/attach-managed-disk-portal.md) nebo použít Azure [PowerShell](../../virtual-machines/windows/attach-disk-ps.md) k přidání dalších vyhrazených datových disků. 

> [!NOTE]
> Maximální místo ve sdílené složce Soubory Azure je 5 TB. Limit velikosti pro každý soubor je 1 TB. 

Tento skript můžete použít v Azure PowerShellu k vytvoření sdílené složky Azure Files:

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

Teď, když jste vytvořili sdílenou složku Souborů Azure, můžete ji připojit do libovolného virtuálního počítače v Azure. Doporučujeme umístit virtuální počítač do stejného datového centra Azure jako účet úložiště, abyste se vyhnuli latenci a poplatkům za přenos dat. Tady jsou příkazy Azure PowerShellu pro připojení jednotky na DSVM:

```powershell
# Get the storage key of the storage account that has the Azure Files share from the Azure portal. Store it securely on the VM to avoid being prompted in the next command.
cmdkey /add:<<mydatadisk>>.file.core.windows.net /user:<<mydatadisk>> /pass:<storage key>

# Mount the Azure Files share as drive Z on the VM. You can choose another drive letter if you want.
net use z:  \\<mydatadisk>.file.core.windows.net\<<teamsharename>>
```

Nyní můžete přistupovat k této jednotce stejně jako normální disk na virtuálním počítači.

## <a name="share-code-in-github"></a>Sdílení kódu na GitHubu
GitHub je úložiště kódu, kde můžete najít ukázky kódu a zdroje pro různé nástroje pomocí technologií sdílených komunitou vývojářů. Používá Git jako technologii pro sledování a ukládání verzí souborů kódu. GitHub je také platforma, kde si můžete vytvořit vlastní úložiště pro ukládání sdíleného kódu a dokumentace vašeho týmu, implementovat správu verzí a řídit, kdo má přístup k zobrazení a contribute kódu. 

Další informace o používání Gitu najdete na [stránkách nápovědy githubu.](https://help.github.com/) GitHub můžete použít jako jeden ze způsobů spolupráce s týmem, použití kódu vyvinutého komunitou a vrácení kódu komunitě.

DSVM je dodáván s klientskými nástroji na příkazovém řádku a na GUI pro přístup k úložišti GitHub. Nástroj příkazového řádku, který funguje s Gitem a GitHubem, se nazývá Git Bash. Visual Studio je nainstalované na DSVM a má rozšíření Git. Ikony těchto nástrojů najdete v nabídce **Start** a na ploše.

Chcete-li stáhnout kód z úložiště GitHub, použijte ```git clone``` příkaz. Chcete-li například stáhnout úložiště datových věd publikované společností Microsoft do aktuálního adresáře, můžete v git bash spustit následující příkaz:

    git clone https://github.com/Azure/DataScienceVM.git

V sadě Visual Studio můžete provést stejnou operaci klonování. Následující snímek obrazovky ukazuje, jak získat přístup k nástrojům Git a GitHub v Sadě Visual Studio:

![Snímek obrazovky visual studia se zobrazeným připojením GitHub](./media/vm-do-ten-things/VSGit.PNG)

Další informace o používání Gitu pro práci s úložištěm GitHub uvidíte z prostředků dostupných na github.com. [Tahák](https://services.github.com/on-demand/downloads/github-git-cheat-sheet.pdf) je užitečný odkaz.

## <a name="access-azure-data-and-analytics-services"></a>Přístup ke službám Azure pro data a analýzy
### <a name="azure-blob-storage"></a>Azure Blob Storage
Úložiště objektů blob Azure je spolehlivá a ekonomická služba cloudového úložiště pro velké i malé objemy dat. Tato část popisuje, jak můžete přesunout data do úložiště objektů Blob a přístup k datům uloženým v objektu blob Azure.

#### <a name="prerequisites"></a>Požadavky

* Vytvořte si účet úložiště objektů blob Azure z [webu Azure Portal](https://portal.azure.com).

   ![Snímek obrazovky s procesem vytváření účtu úložiště na webu Azure Portal](./media/vm-do-ten-things/Create_Azure_Blob.PNG)

* Zkontrolujte, zda je nástroj AzCopy příkazového řádku předinstalovaný: ```C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy.exe```. Adresář, který obsahuje nástroj azcopy.exe, je již v proměnné prostředí PATH, takže se při spuštění tohoto nástroje můžete vyhnout zadávání úplné cesty příkazů. Další informace o nástroji AzCopy naleznete v [dokumentaci k AzCopy](../../storage/common/storage-use-azcopy.md).
* Spusťte nástroj Průzkumník a ukládání dat Azure. Můžete si ji stáhnout z [webové stránky Průzkumníka úložiště](https://storageexplorer.com/). 

   ![Snímek obrazovky s Průzkumníkem úložišť Azure přistupujícík k účtu úložiště](./media/vm-do-ten-things/AzureStorageExplorer_v4.png)

#### <a name="move-data-from-a-vm-to-an-azure-blob-azcopy"></a>Přesunutí dat z virtuálního počítače do objektu blob Azure: AzCopy

Chcete-li přesouvat data mezi místními soubory a úložištěm objektů Blob, můžete použít AzCopy na příkazovém řádku nebo v Prostředí PowerShell:

    AzCopy /Source:C:\myfolder /Dest:https://<mystorageaccount>.blob.core.windows.net/<mycontainer> /DestKey:<storage account key> /Pattern:abc.txt

Nahraďte **C:\myfolder** s cestou, kde je uložen soubor, **mystorageaccount** s názvem účtu úložiště objektů blob, **mycontainer** s názvem kontejneru a **klíč účtu úložiště** s klíčem přístupkového úložiště objektů Blob. Svoje přihlašovací údaje k účtu úložiště najdete na [webu Azure Portal](https://portal.azure.com).

Spusťte příkaz AzCopy v prostředí PowerShell nebo z příkazového řádku. Zde je několik příkladů použití příkazu AzCopy:

```powershell
# Copy *.sql from a local machine to an Azure blob
"C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:"c:\Aaqs\Data Science Scripts" /Dest:https://[ENTER STORAGE ACCOUNT].blob.core.windows.net/[ENTER CONTAINER] /DestKey:[ENTER STORAGE KEY] /S /Pattern:*.sql

# Copy back all files from an Azure blob container to a local machine

"C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Dest:"c:\Aaqs\Data Science Scripts\temp" /Source:https://[ENTER STORAGE ACCOUNT].blob.core.windows.net/[ENTER CONTAINER] /SourceKey:[ENTER STORAGE KEY] /S
```

Po spuštění příkazu AzCopy ke kopírování do objektu blob Azure se váš soubor zobrazí v Průzkumníku úložiště Azure.

![Snímek obrazovky s účtem úložiště zobrazující nahraný soubor CSV](./media/vm-do-ten-things/AzCopy_run_finshed_Storage_Explorer_v3.png)

#### <a name="move-data-from-a-vm-to-an-azure-blob-azure-storage-explorer"></a>Přesunutí dat z virtuálního počítače do objektu blob Azure: Azure Storage Explorer

Data z místního souboru ve virtuálním počítači můžete taky nahrát pomocí Průzkumníka úložiště Azure:

* Chcete-li odeslat data do kontejneru, vyberte cílový kontejner a vyberte tlačítko **Odeslat.** ![Snímek obrazovky s tlačítkem nahrávání v Průzkumníkovi úložiště Azure](./media/vm-do-ten-things/storage-accounts.png)
* Vyberte tři tečky (**...**) napravo od pole **Soubory,** vyberte jeden nebo více souborů, které chcete nahrát ze systému souborů, a vyberte **Nahrát,** abyste začali nahrávat soubory. ![Snímek obrazovky s dialogovým oknem Nahrát soubory](./media/vm-do-ten-things/upload-files-to-blob.png)

#### <a name="read-data-from-an-azure-blob-machine-learning-reader-module"></a>Čtení dat z modulu Azure blob: Machine Learning reader

V Azure Machine Learning Studio můžete použít modul Import dat ke čtení dat z vašeho objektu blob.

![Snímek obrazovky s modulem Import dat ve Studiu strojového učení](./media/vm-do-ten-things/AML_ReaderBlob_Module_v3.png)

#### <a name="read-data-from-an-azure-blob-python-odbc"></a>Čtení dat z objektu blob Azure: Python ODBC

Knihovna BlobService můžete použít ke čtení dat přímo z objektu blob v poznámkovém bloku Jupyter nebo v programu Pythonu.

Nejprve importujte požadované balíčky:

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

Potom připojte přihlašovací údaje účtu úložiště objektů Blob a přečtěte si data z objektu blob:

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

![Snímek obrazovky s prvními 10 řádky dat](./media/vm-do-ten-things/IPNB_data_readin.PNG)

### <a name="azure-data-lake"></a>Azure Data Lake
Azure Data Lake Storage je úložiště hyperškálování pro úlohy analýzy velkých objemů dat a je kompatibilní s hadoopským distribuovaným souborovým systémem (HDFS). Spolupracuje s Hadoopem, Sparkem a Azure Data Lake Analytics. V této části se dozvíte, jak můžete přesunout data do Azure Data Lake Storage a spustit analýzy pomocí Azure Data Lake Analytics.

#### <a name="prerequisites"></a>Požadavky

* Vytvořte si instanci Azure Data Lake Analytics na [webu Azure Portal](https://portal.azure.com).

   ![Snímek obrazovky s vytvořením instance Data Lake Analytics z webu Azure Portal](./media/vm-do-ten-things/Azure_Data_Lake_Create_v2.png)

* Nástroje [Azure Data Lake a Stream Analytics pro modul plug-in Visual Studia](https://www.microsoft.com/download/details.aspx?id=49504) už jsou nainstalované ve Visual Studiu Community Edition na virtuálním počítači. Po spuštění Sady Visual Studio a přihlášení k předplatnému Azure byste měli vidět svůj účet Azure Data Analytics a úložiště v levém panelu Visual Studia.

   ![Snímek obrazovky s modulem plug-in pro nástroje Datového jezera v Sadě Visual Studio](./media/vm-do-ten-things/Azure_Data_Lake_PlugIn_v2.PNG)

#### <a name="move-data-from-a-vm-to-data-lake-azure-data-lake-explorer"></a>Přesunutí dat z virtuálního počítače do datového jezera: Azure Data Lake Explorer

Azure Data Lake Explorer můžete použít k [nahrání dat z místních souborů ve virtuálním počítači do úložiště datových jezer](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal).

Můžete také vytvořit datový kanál pro zprovoznění vašeho přesunu dat do nebo z Azure Data Lake pomocí [Azure Data Factory](https://azure.microsoft.com/services/data-factory/). [Tento článek](https://azure.microsoft.com/blog/creating-big-data-pipelines-using-azure-data-lake-and-azure-data-factory/) vás provede kroky k vytvoření datových kanálů.

#### <a name="read-data-from-an-azure-blob-to-data-lake-u-sql"></a>Čtení dat z objektu blob Azure do datového jezera: U-SQL

Pokud se vaše data nacházejí v úložišti objektů Blob Azure, můžete přímo číst data z objektu blob Azure v dotazu U-SQL. Než vytvoříte dotaz U-SQL, ujistěte se, že váš účet úložiště objektů Blob je propojený s instancí Azure Data Lake. Přejděte na portál Azure, najděte řídicí panel Azure Data Lake Analytics, vyberte **Přidat zdroj dat**, vyberte typ úložiště Azure **Storage**a připojte název a klíč účtu úložiště Azure. Pak můžete odkazovat na data uložená v účtu úložiště.

![Snímek obrazovky s dialogovém oknem Přidat zdroj dat](./media/vm-do-ten-things/Link_Blob_to_ADLA_v2.PNG)

V Sadě Visual Studio můžete číst data z úložiště objektů Blob, manipulovat s daty, inženýrovat funkce a odesílat výsledná data do azure datového jezera nebo úložiště objektů blob Azure. Když odkazujete na data v úložišti objektů Blob, použijte **wasb://**. Když odkazujete na data v Azure Data Lake, použijte **swbhdfs://**.

V sadě Visual Studio můžete použít následující dotazy U-SQL:

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

Po odeslání dotazu na server diagram zobrazuje stav úlohy.

![Snímek obrazovky se stavovým diagramem úlohy](./media/vm-do-ten-things/USQL_Job_Status.PNG)

#### <a name="query-data-in-data-lake-u-sql"></a>Data dotazu v datovém jezeře: U-SQL

Po požití datové sady v Azure Data Lake, můžete použít [jazyk U-SQL](../../data-lake-analytics/data-lake-analytics-u-sql-get-started.md) k dotazování a prozkoumání dat. Jazyk U-SQL je podobný T-SQL, ale kombinuje některé funkce z Jazyka C#, takže uživatelé mohou psát přizpůsobené moduly a uživatelem definované funkce. Skripty můžete použít v předchozím kroku.

Po odeslání dotazu na server tripdata_summary. CsV se zobrazí v Azure Data Lake Explorer. Data můžete zobrazit kliknutím pravým tlačítkem myši na soubor.

![Snímek obrazovky se souborem CSV v Průzkumníku datového jezera](./media/vm-do-ten-things/USQL_create_summary.png)

Zobrazí se informace o souboru:

![Snímek obrazovky se souhrnnými informacemi o souboru](./media/vm-do-ten-things/USQL_tripdata_summary.png)

### <a name="hdinsight-hadoop-clusters"></a>Clustery HDInsight Hadoop
Azure HDInsight je spravovaná služba Apache Hadoop, Spark, HBase a Storm v cloudu. S clustery Azure HDInsight můžete snadno pracovat z virtuálního počítače pro datové vědy.

#### <a name="prerequisites"></a>Požadavky

* Vytvořte si účet úložiště objektů blob Azure z [webu Azure Portal](https://portal.azure.com). Tento účet úložiště se používá k ukládání dat pro clustery HDInsight.

   ![Snímek obrazovky s vytvořením účtu úložiště z webu Azure Portal](./media/vm-do-ten-things/Create_Azure_Blob.PNG)

* Přizpůsobte clustery Azure HDInsight Hadoop z [webu Azure Portal](../team-data-science-process/customize-hadoop-cluster.md).
  
   Propojte účet úložiště vytvořený s clusterem HDInsight, když je vytvořen. Tento účet úložiště se používá pro přístup k datům, která lze zpracovat v rámci clusteru.

   ![Výběrpro propojení účtu úložiště vytvořeného s clusterem HDInsight](./media/vm-do-ten-things/Create_HDI_v4.PNG)

* Po jeho vytvoření povolte přístup ke vzdálené ploše k hlavnímu uzlu clusteru. Zapamatujte si pověření vzdáleného přístupu, které zde zadáte, protože je budete potřebovat v následujícím postupu.

   ![Tlačítko Vzdálená plocha pro povolení vzdáleného přístupu ke clusteru HDInsight](./media/vm-do-ten-things/Create_HDI_dashboard_v3.PNG)

* Vytvořte pracovní prostor Azure Machine Learning. Vaše experimenty machine learningu jsou uloženy v tomto pracovním prostoru Machine Learning. Vyberte zvýrazněné možnosti na portálu, jak je znázorněno na následujícím snímku obrazovky:

   ![Vytvoření pracovního prostoru Azure Machine Learning](./media/vm-do-ten-things/Create_ML_Space.PNG)

* Zadejte parametry pracovního prostoru.

   ![Zadat parametry pracovního prostoru Strojového učení](./media/vm-do-ten-things/Create_ML_Space_step2_v2.PNG)

* Nahrajte data pomocí poznámkového bloku IPython. Importujte požadované balíčky, zapojte přihlašovací údaje, vytvořte databázi v účtu úložiště a pak načtěte data do clusterů HDI.

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

Případně můžete podle [tohoto návodu](../team-data-science-process/hive-walkthrough.md) nahrát data NYC Taxi do clusteru HDI. Mezi hlavní kroky patří:
  
* Pomocí azcopy ke stažení csv zipů z veřejného objektu blob do místní složky.
* Pomocí aplikace AzCopy můžete nahrát rozepnuté soubory CSV z místní složky do clusteru HDI.
* Přihlaste se k hlavnímu uzlu clusteru Hadoop a připravte se na analýzu průzkumných dat.

Po načtení dat do clusteru HDI můžete zkontrolovat data v Průzkumníku azure storage exploreru. A nyctaxidb databáze byla vytvořena v clusteru HDI.

#### <a name="data-exploration-hive-queries-in-python"></a>Zkoumání dat: Hive dotazy v Pythonu

Vzhledem k tomu, že data jsou v clusteru Hadoop, můžete použít balíček pyodbc pro připojení k clusterům Hadoop a databázím dotazů pomocí Hive k průzkumu a inženýrství funkcí. Existující tabulky, které jste vytvořili, můžete zobrazit v kroku předpokladu.

```python
queryString = """
    show tables in nyctaxidb2;
    """
pd.read_sql(queryString, connection)
```

![Zobrazit existující tabulky](./media/vm-do-ten-things/Python_View_Existing_Tables_Hive_v3.PNG)

Podívejme se na počet záznamů v každém měsíci a frekvence šikmé nebo ne v tabulce cesty:

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

![Vykreslení počtu záznamů v každém měsíci](./media/vm-do-ten-things/Exploration_Number_Records_by_Month_v3.PNG)

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

![Vykreslení frekvencí špiček](./media/vm-do-ten-things/Exploration_Frequency_tip_or_not_v3.PNG)

Můžete také vypočítat vzdálenost mezi místem vyzvednutí a místem předání a poté ji porovnat se vzdáleností cesty.

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

![Horní řady stolku a drop-off stolu](./media/vm-do-ten-things/Exploration_compute_pickup_dropoff_distance_v2.PNG)

```python
results.columns = ['pickup_longitude', 'pickup_latitude', 'dropoff_longitude',
                   'dropoff_latitude', 'trip_distance', 'trip_time_in_secs', 'direct_distance']
df = results.loc[results['trip_distance'] <= 100]  # remove outliers
df = df.loc[df['direct_distance'] <= 100]  # remove outliers
plt.scatter(df['direct_distance'], df['trip_distance'])
```

![Vykreslení vzdálenosti vyzvednutí/odsávání do vzdálenosti jízdy](./media/vm-do-ten-things/Exploration_direct_distance_trip_distance_v2.PNG)

Nyní připravíme downsampled (1 procento) sadu dat pro modelování. Tato data můžete použít v modulu čtečky strojového učení.

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

Po chvíli uvidíte, že data byla načtena v clusterech Hadoop:

```python
queryString = """
    select * from nyctaxi_downsampled_dataset limit 10;
    """
cursor.execute(queryString)
pd.read_sql(queryString, connection)
```

![Horní řady dat z tabulky](./media/vm-do-ten-things/DownSample_Data_For_Modeling_v2.PNG)

### <a name="azure-sql-data-warehouse-and-databases"></a>Azure SQL Data Warehouse a databáze
Azure SQL Data Warehouse je elastický datový sklad jako služba se zkušenostmi sql serveru podnikové třídy.

Datový sklad Azure SQL můžete zřídit podle pokynů v [tomto článku](../../sql-data-warehouse/sql-data-warehouse-get-started-provision.md). Po zřízení datového skladu SQL můžete [tento návod](../team-data-science-process/sqldw-walkthrough.md) použít k nahrávání, zkoumání a modelování dat pomocí dat v datovém skladu SQL.

#### <a name="azure-cosmos-db"></a>Azure Cosmos DB
Azure Cosmos DB je databáze NoSQL v cloudu. Můžete ji použít k práci s dokumenty, jako je JSON, a k ukládání a dotazování dokumentů.

Pro přístup k Azure Cosmos DB z DSVM použijte následující nezbytné kroky:

1. Sada Azure Cosmos DB Python SDK je už nainstalovaná v modulu DSVM. Chcete-li jej ```pip install pydocumentdb --upgrade``` aktualizovat, spusťte jej z příkazového řádku.
2. Vytvořte účet ADb Azure Cosmos a databázi z [webu Azure Portal](https://portal.azure.com).
3. Stáhněte si nástroj pro migraci dat Azure Cosmos DB ze [služby Stažení softwaru a](https://www.microsoft.com/download/details.aspx?id=53595) extrahujte je do adresáře podle vašeho výběru.
4. Importujte data JSON (data sopky) uložená ve [veřejném objektu blob](https://cahandson.blob.core.windows.net/samples/volcano.json) do azure cosmos db s následujícími parametry příkazů do nástroje pro migraci. (Použijte dtui.exe z adresáře, do kterého jste nainstalovali Nástroj pro migraci dat Azure Cosmos DB.) Zadejte zdrojové a cílové umístění s těmito parametry:
   
    `/s:JsonFile /s.Files:https://cahandson.blob.core.windows.net/samples/volcano.json /t:DocumentDBBulk /t.ConnectionString:AccountEndpoint=https://[DocDBAccountName].documents.azure.com:443/;AccountKey=[[KEY];Database=volcano /t.Collection:volcano1`

Po importu dat můžete přejít na Jupyter a otevřít poznámkový blok s názvem *DocumentDBSample*. Obsahuje kód Pythonu pro přístup k Azure Cosmos DB a provést některé základní dotazování. Další informace o Azure Cosmos DB najdete na [stránce dokumentace služby](https://docs.microsoft.com/azure/cosmos-db/).

## <a name="use-power-bi-reports-and-dashboards"></a>Použití sestav a řídicích panelů Power BI 
Soubor Volcano JSON můžete vizualizovat z předchozího příkladu Azure Cosmos DB v Power BI Desktopu a získat vizuální přehled o datech. Podrobné kroky jsou k dispozici v [článku Power BI](../../cosmos-db/powerbi-visualize.md). Zde jsou kroky na vysoké úrovni:

1. Otevřete Power BI Desktop a vyberte **Načíst data**. Zadejte adresu `https://cahandson.blob.core.windows.net/samples/volcano.json`URL jako: .
2. Měli byste vidět záznamy JSON importované jako seznam. Převeďte seznam na tabulku, aby s ním Mohl Pracovat Power BI.
4. Rozbalte sloupce výběrem ikony rozbalit (šipku).
5. Všimněte si, že umístění je **pole záznam.** Rozbalte záznam a vyberte pouze souřadnice. **Souřadnice** je sloupec seznamu.
6. Přidáním nového sloupce převedete sloupec souřadnic seznamu na sloupec **LatLong** oddělený čárkami. Zřetězit dva prvky v poli seznamu souřadnic pomocí vzorce ```Text.From([coordinates]{1})&","&Text.From([coordinates]{0})```.
7. Převeďte sloupec **Výška** na desetinné číslo a vyberte tlačítka **Zavřít** a **Aplikovat.**

Namísto předchozích kroků můžete vložit následující kód. Skripty z kroků použitých v rozšířeném editoru v Power BI k zápisu transformace dat v dotazovacím jazyce.

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

Nyní máte data v datovém modelu Power BI. Instance Power BI Desktop by se měla zobrazit takto:

![Power BI Desktop](./media/vm-do-ten-things/PowerBIVolcanoData.png)

Můžete začít vytvářet sestavy a vizualizace pomocí datového modelu. Pomocí kroků v [tomto článku power bi](../../cosmos-db/powerbi-visualize.md#build-the-reports) můžete vytvořit sestavu.

## <a name="scale-the-dsvm-dynamically"></a>Dynamické škálování DSVM 
Můžete vertikálně navýšit kapacitu dsvm nahoru a dolů tak, aby vyhovovalpotřebám vašeho projektu. Pokud virtuální počítač nepotřebujete používat ve večerních nebo o víkendech, můžete virtuální počítač vypnout z [portálu Azure](https://portal.azure.com).

> [!NOTE]
> Účtuje vám výpočetní poplatky, pokud použijete pouze tlačítko vypnutí operačního systému na virtuálním počítači.  
> 
> 

Možná budete muset zpracovat některé rozsáhlé analýzy a potřebujete více kapacity procesoru, paměti nebo disku. Pokud ano, můžete najít výběr velikostí virtuálních počítačů z hlediska jader procesoru, instancí založených na GPU pro hloubkové učení, kapacitu paměti a typy disků (včetně jednotek SSD), které splňují vaše výpočetní a rozpočtové potřeby. Úplný seznam virtuálních počítačů spolu s jejich hodinovými cenami za výpočetní výkon je dostupný na [stránce s cenami virtuálních počítačů Azure.](https://azure.microsoft.com/pricing/details/virtual-machines/)

Podobně může snížit vaše potřeba kapacity zpracování virtuálních počítačových společností. (Například: přesunuli jste hlavní úlohu do clusteru Hadoop nebo Spark.) Potom můžete vertikálně zmenšit kapacitu clusteru z [portálu Azure](https://portal.azure.com) a přejít na nastavení instance virtuálního počítače. 

## <a name="add-more-tools"></a>Přidání dalších nástrojů
Nástroje předem zabudované do dsvm může řešit mnoho běžných potřeb analýzy dat. To vám ušetří čas, protože není nutné instalovat a konfigurovat prostředí jeden po druhém. To také šetří peníze, protože budete platit pouze za zdroje, které používáte.

Další datové a analytické služby Azure profilované v tomto článku můžete použít k vylepšení analytického prostředí. V některých případech můžete potřebovat další nástroje, včetně některých proprietárních partnerských nástrojů. Máte úplný přístup pro správu na virtuálním počítači k instalaci nových nástrojů, které potřebujete. Můžete také nainstalovat další balíčky v Pythonu a R, které nejsou předinstalované. Pro Python můžete použít ```conda``` ```pip```buď nebo . Pro R můžete ```install.packages()``` použít v konzole R nebo použít ide a vyberte **balíčky** > **instalační balíčky**.

## <a name="deep-learning"></a>Hloubkové učení

Kromě ukázky založené na rozhraní, můžete získat sadu komplexní návody, které byly ověřeny na DSVM. Tyto návody vám pomohou nastartovat vývoj aplikací s hloubkovým učením v doménách, jako je porozumění obrázkům a textu a jazykům.   


- [Spuštění neuronové sítě v různých architekturách](https://github.com/ilkarman/DeepLearningFrameworks): Tento návod ukazuje, jak migrovat kód z jednoho rozhraní do druhého. Také ukazuje, jak porovnat modely a výkon za běhu napříč rámci. 

- [Návod k vytvoření komplexního řešení pro detekci produktů v rámci bitových kopií](https://github.com/Azure/cortana-intelligence-product-detection-from-images): Detekce obrazu je technika, která dokáže najít a klasifikovat objekty v rámci bitových kopií. Tato technologie má potenciál přinést obrovské odměny v mnoha reálných obchodních oblastech. Maloobchodníci mohou například tuto techniku použít k určení produktu, který zákazník vyzvedl z regálu. Tyto informace zase pomáhají ukládají spravovat zásoby produktů. 

- [Hluboké učení pro zvuk](https://blogs.technet.microsoft.com/machinelearning/2018/01/30/hearing-ai-getting-started-with-deep-learning-for-audio-on-azure/): Tento kurz ukazuje, jak trénovat model hlubokého učení pro detekci zvukových událostí v [datové sadě městských zvuků](https://serv.cusp.nyu.edu/projects/urbansounddataset/urbansound8k.html). Poskytuje také přehled o tom, jak pracovat se zvukovými daty.

- [Klasifikace textových dokumentů](https://github.com/anargyri/lstm_han): Tento návod ukazuje, jak vytvořit a trénovat dvě architektury neuronové sítě: Hierarchická síť pozornosti a síť lstm (Long Term Memory). Tyto neuronové sítě používají rozhraní KERAS API pro hluboké učení ke klasifikaci textových dokumentů. Keras je front-end ke třem nejoblíbenějším rozhraním pro hluboké učení: Microsoft Cognitive Toolkit, TensorFlow a Theano.

## <a name="summary"></a>Souhrn
Tento článek popisuje některé z věcí, které můžete dělat na Microsoft Data Science Virtual Machine. Existuje mnoho dalších věcí, které můžete udělat, aby se DSVM efektivní analytické prostředí.

