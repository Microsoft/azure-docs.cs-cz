---
title: Prozkoumat data a model ve Windows
titleSuffix: Azure Data Science Virtual Machine
description: Provádění úloh zkoumání a modelování dat na Data Science Virtual Machine Windows.
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
ms.custom: devx-track-python
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 05/08/2020
ms.openlocfilehash: b570968a66a0cfd60ac4d6ce6dd7dc31a1003240
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89440452"
---
# <a name="data-science-with-a-windows-data-science-virtual-machine"></a>Datové vědy s Data Science Virtual Machine Windows

Windows Data Science Virtual Machine (DSVM) je výkonné vývojové prostředí pro datové vědy, kde můžete provádět úlohy zkoumání a modelování dat. Prostředí je už sestavené a obsahuje několik oblíbených nástrojů pro analýzu dat, které usnadňují práci s analýzou pro místní, cloudová nebo hybridní nasazení. 

DSVM úzce spolupracuje se službami Azure. Může číst a zpracovávat data, která jsou už uložená v Azure, v Azure synapse (dřív SQL DW), Azure Data Lake, Azure Storage nebo Azure Cosmos DB. Může také využít jiné analytické nástroje, jako je Azure Machine Learning.

V tomto článku se dozvíte, jak používat DSVM k provádění úkolů s datovou vědy a k interakci s ostatními službami Azure. Tady jsou některé z akcí, které můžete provést na DSVM:

- Pomocí poznámkového bloku Jupyter můžete experimentovat s daty v prohlížeči pomocí Python 2, Python 3 a Microsoft R. (Microsoft R je podniková verze R, která je připravená na výkon.)
- Prozkoumejte data a vývoj modelů místně na DSVM pomocí Microsoft Machine Learning Server a Pythonu.
- Prostředky Azure spravujte pomocí Azure Portal nebo PowerShellu.
- Rozšiřte prostor úložiště a sdílejte velké datové sady/kód v celém týmu tím, že vytvoříte sdílenou složku souborů Azure jako připojenou jednotku na DSVM.
- Sdílejte kód s týmem pomocí GitHubu. Přístup k úložišti pomocí předem nainstalovaných klientů git: Git bash a git.
- Přístup ke službám Azure Data and Analytics, jako je Azure Blob Storage, Azure Cosmos DB, Azure synapse (dřív SQL DW) a Azure SQL Database.
- Sestavování sestav a řídicích panelů pomocí Power BI Desktop instance, která je předem nainstalovaná na DSVM a jejich nasazení v cloudu.

- Nainstalujte do svého virtuálního počítače Další nástroje.   

> [!NOTE]
> Další poplatky za využití platí pro spoustu služeb úložiště dat a analýz uvedených v tomto článku. Podrobnosti najdete na stránce s [cenami Azure](https://azure.microsoft.com/pricing/) .
> 
> 

## <a name="prerequisites"></a>Požadavky

* Potřebujete předplatné Azure. Můžete si [zaregistrovat bezplatnou zkušební verzi](https://azure.microsoft.com/free/).
* Pokyny pro zřízení Data Science Virtual Machine v Azure Portal jsou k dispozici v [tématu Vytvoření virtuálního počítače](https://portal.azure.com/#create/microsoft-dsvm.dsvm-windowsserver-2016).


[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]


## <a name="use-jupyter-notebooks"></a>Použití poznámkových bloků Jupyter
Jupyter Notebook poskytuje integrované vývojové prostředí (IDE) založené na prohlížeči pro zkoumání a modelování dat. V Jupyter poznámkovém bloku můžete použít Python 2, Python 3 nebo R (Open Source i Microsoft R Server).

Jupyter Notebook spustíte tak, že v nabídce **Start** nebo na ploše vyberete ikonu **Jupyter notebook** . V příkazovém řádku DSVM můžete také spustit příkaz ```jupyter notebook``` z adresáře, ve kterém máte existující poznámkové bloky nebo kde chcete vytvořit nové poznámkové bloky.  

Po spuštění Jupyter přejděte do `/notebooks` adresáře, kde najdete ukázkové poznámkové bloky, které jsou předem zabaleny do DSVM. Nyní můžete:

* Vyberte Poznámkový blok pro zobrazení kódu.
* Spusťte každou buňku výběrem SHIFT + ENTER.
* Spusťte celý Poznámkový blok **Cell**výběrem možnosti  >  **spuštění**buněk.
* Vytvořte nový Poznámkový blok tak, že vyberete ikonu Jupyter (v levém horním rohu), kliknete na tlačítko **Nový** a pak zvolíte jazyk poznámkového bloku (označovaný také jako jádra).   

> [!NOTE]
> V současné době jsou podporovány jádra Python 2,7, Python 3,6, R, Helena a PySpark v Jupyter. Jádro R podporuje programování jak v open-source R, tak i v Microsoft R.   
> 
> 

Když jste v poznámkovém bloku, můžete prozkoumat vaše data, sestavit model a testovat model pomocí zvolených knihoven.

## <a name="explore-data-and-develop-models-with-microsoft-machine-learning-server"></a>Prozkoumejte data a vývoj modelů pomocí Microsoft Machine Learning Server
Pomocí jazyků, jako je R a Python, můžete provádět analýzu dat přímo na DSVM.

Pro R můžete použít IDE, jako je RStudio, který najdete v nabídce Start nebo na ploše. Nebo můžete použít Nástroje R pro Visual Studio. Společnost Microsoft poskytuje další knihovny nad open source CRAN R, aby bylo možné škálovatelné analýzy a schopnost analyzovat data větší, než je velikost paměti povolenou v paralelním blokovém analýze. 

Pro Python můžete použít integrované vývojové prostředí (IDE), jako je Visual Studio Community Edition, které má předinstalované rozšíření Python Tools for Visual Studio (PTVS). Ve výchozím nastavení je v PTVS nakonfigurovaná jenom Python 3,6, kořenové prostředí conda. Pokud chcete povolit Anaconda Python 2,7, proveďte následující kroky:

1. Vytvořte vlastní prostředí pro každou verzi tak, že přejdete na **nástroje**  >  **Python Tools**  >  **Python prostředí**a potom v edici Visual Studio Community Edition vyberete **+ Custom** .
1. Zadejte popis a nastavte cestu k předponě prostředí jako **c:\anaconda\envs\python2** pro anaconda Python 2,7.
1. **Auto Detect**  >  Chcete-li uložit prostředí, vyberte možnost automaticky zjišťovat**použití** .

Další podrobnosti o tom, jak vytvářet prostředí Pythonu, najdete v [dokumentaci k PTVS](https://aka.ms/ptvsdocs) .

Teď jste nastavili vytvoření nového projektu v Pythonu. Přejít na **soubor**  >  **Nový**  >  **projekt**  >  **Python** a vyberte typ aplikace Python, kterou vytváříte. Prostředí Python pro aktuální projekt můžete nastavit na požadovanou verzi (Python 2,7 nebo 3,6) tak, že kliknete pravým tlačítkem na **prostředí Pythonu** a pak vyberete **Přidat nebo odebrat prostředí Pythonu**. Další informace o práci s PTVS najdete v [dokumentaci k produktu](https://aka.ms/ptvsdocs).



## <a name="manage-azure-resources"></a>Správa prostředků Azure
DSVM vám neumožňuje vytvářet řešení pro analýzu místně na virtuálním počítači. Také umožňuje přístup ke službám na cloudové platformě Azure. Azure nabízí několik výpočetních, úložných a datových analýz a dalších služeb, které můžete spravovat a přistupovat z DSVM.

Ke správě předplatného Azure a cloudových prostředků máte dvě možnosti:
+ Použijte svůj prohlížeč a pokračujte na [Azure Portal](https://portal.azure.com).

+ Použijte skripty prostředí PowerShell. Spusťte Azure PowerShell z zástupce na ploše nebo z nabídky **Start** . Úplné podrobnosti najdete v [dokumentaci k Microsoft Azure PowerShell](../../powershell-azure-resource-manager.md) . 

## <a name="extend-storage-by-using-shared-file-systems"></a>Rozšiřování úložiště pomocí sdílených systémů souborů
Vědečtí data mohou sdílet velké datové sady, kód nebo jiné prostředky v rámci týmu. DSVM má k dispozici přibližně 45 GB volného místa. Pokud chcete úložiště zvětšit, můžete použít soubory Azure a buď je připojit na jednu nebo více instancí DSVM nebo k nim přistupovat pomocí REST API. Můžete také použít [Azure Portal](../../virtual-machines/windows/attach-managed-disk-portal.md) nebo pomocí [Azure PowerShell](../../virtual-machines/windows/attach-disk-ps.md) přidat další vyhrazené datové disky. 

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

Teď máte přístup k této jednotce, stejně jako máte na virtuálním počítači nějakou normální jednotku.

## <a name="share-code-in-github"></a>Sdílení kódu na GitHubu
GitHub je úložiště kódu, kde můžete najít ukázky kódu a zdroje pro různé nástroje pomocí technologií sdílených komunitou vývojářů. Používá Git jako technologii pro sledování a ukládání verzí souborů s kódem. GitHub je také platforma, kde můžete vytvořit vlastní úložiště pro ukládání sdíleného kódu a dokumentace vašeho týmu, implementovat správu verzí a řídit, kdo má přístup k zobrazení kódu a přispívání ke kódu. 

Další informace o tom, jak používat Git, najdete na stránkách s informacemi o [GitHubu](https://help.github.com/) . GitHub můžete použít jako jeden ze způsobů, jak spolupracovat se svým týmem, používat kód vyvinutý komunitou a přispívat kód zpátky do komunity.

DSVM se načte pomocí klientských nástrojů na příkazovém řádku a v grafickém uživatelském rozhraní pro přístup k úložišti GitHub. Nástroj příkazového řádku, který funguje s Git a GitHubem, se nazývá Git bash. Visual Studio je nainstalované na DSVM a má rozšíření Git. Ikony těchto nástrojů můžete najít v nabídce **Start** a na ploše.

Chcete-li stáhnout kód z úložiště GitHub, použijte ```git clone``` příkaz. Chcete-li například stáhnout úložiště pro datové vědy publikované společností Microsoft do aktuálního adresáře, můžete spustit následující příkaz v Gitu bash:

```bash
git clone https://github.com/Azure/DataScienceVM.git
```

V aplikaci Visual Studio můžete provést stejnou operaci klonování. Následující snímek obrazovky ukazuje, jak získat přístup k nástrojům Git a GitHub v aplikaci Visual Studio:

![Snímek obrazovky sady Visual Studio se zobrazeným připojením GitHubu](./media/vm-do-ten-things/VSGit.png)

Další informace o tom, jak pomocí Gitu pracovat s úložištěm GitHubu, najdete v informacích dostupných na github.com. [Tahák list](https://services.github.com/on-demand/downloads/github-git-cheat-sheet.pdf) je užitečným odkazem.

## <a name="access-azure-data-and-analytics-services"></a>Přístup ke službám Azure Data and Analytics
### <a name="azure-blob-storage"></a>Azure Blob Storage
Azure Blob Storage je spolehlivá, ekonomická služba pro cloudové úložiště pro velké a malé objemy dat. Tato část popisuje, jak můžete přesouvat data do úložiště objektů BLOB a přistupovat k datům uloženým v objektu blob Azure.

#### <a name="prerequisites"></a>Požadavky

* Vytvořte účet Azure Blob Storage z [Azure Portal](https://portal.azure.com).

   ![Snímek obrazovky procesu vytváření účtu úložiště v Azure Portal](./media/vm-do-ten-things/create-azure-blob.png)

* Potvrďte, že je nástroj příkazového řádku AzCopy předem nainstalován: ```C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy.exe``` . Adresář, který obsahuje azcopy.exe, již je ve vaší proměnné prostředí PATH, takže se můžete vyhnout psaní úplné cesty k příkazu při spuštění tohoto nástroje. Další informace o nástroji AzCopy najdete v [dokumentaci k AzCopy](../../storage/common/storage-use-azcopy.md).
* Spusťte Nástroj Průzkumník služby Azure Storage. Můžete si ho stáhnout z  [webové stránky Průzkumník služby Storage](https://storageexplorer.com/). 

   ![Snímek obrazovky Průzkumník služby Azure Storage přístupu k účtu úložiště](./media/vm-do-ten-things/AzureStorageExplorer_v4.png)

#### <a name="move-data-from-a-vm-to-an-azure-blob-azcopy"></a>Přesunutí dat z virtuálního počítače do objektu blob Azure: AzCopy

Pokud chcete přesunout data mezi místními soubory a úložištěm objektů blob, můžete použít AzCopy v příkazovém řádku nebo v PowerShellu:

```powershell
AzCopy /Source:C:\myfolder /Dest:https://<mystorageaccount>.blob.core.windows.net/<mycontainer> /DestKey:<storage account key> /Pattern:abc.txt
```

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

#### <a name="move-data-from-a-vm-to-an-azure-blob-azure-storage-explorer"></a>Přesunutí dat z virtuálního počítače do objektu blob Azure: Průzkumník služby Azure Storage

Data z místního souboru na VIRTUÁLNÍm počítači můžete také nahrát pomocí Průzkumník služby Azure Storage:

* Pokud chcete nahrát data do kontejneru, vyberte cílový kontejner a vyberte tlačítko **nahrát** . ![ Snímek obrazovky s tlačítkem nahrát v Průzkumník služby Azure Storage](./media/vm-do-ten-things/storage-accounts.png)
* Vyberte tři tečky (**...**) napravo od pole **soubory** , vyberte jeden nebo více souborů, které se mají nahrát ze systému souborů, a vyberte **nahrát** a začněte nahrávat soubory. ![ Snímek obrazovky dialogového okna nahrát soubory](./media/vm-do-ten-things/upload-files-to-blob.png)

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

![Snímek obrazovky prvních 10 řádků dat](./media/vm-do-ten-things/IPNB_data_readin.png)


### <a name="azure-synapse-analytics-formerly-sql-dw-and-databases"></a>Azure synapse Analytics (dříve SQL DW) a databáze
Azure synapse Analytics (dřív SQL DW) je Elastický datový sklad jako služba s využitím SQL Server na podnikové úrovni.

Analýzy Azure synapse můžete zřídit podle pokynů v [tomto článku](../../sql-data-warehouse/sql-data-warehouse-get-started-provision.md). Po zřízení Azure synapse Analytics můžete pomocí [tohoto návodu](../team-data-science-process/sqldw-walkthrough.md) provádět nahrávání, zkoumání a modelování dat pomocí dat v rámci služby Azure synapse Analytics.

#### <a name="azure-cosmos-db"></a>Azure Cosmos DB
Azure Cosmos DB je databáze NoSQL v cloudu. Můžete ji použít k práci s dokumenty, jako je JSON, a k ukládání a dotazování dokumentů.

Pro přístup k Azure Cosmos DB z DSVM použijte následující požadované kroky:

1. Sada SDK Azure Cosmos DB Python je už v DSVM nainstalovaná. Pokud ho chcete aktualizovat, spusťte ```pip install pydocumentdb --upgrade``` z příkazového řádku.
2. Vytvořte účet Azure Cosmos DB a databázi z [Azure Portal](https://portal.azure.com).
3. Stáhněte nástroj pro migraci dat Azure Cosmos DB z webu [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=53595) a rozbalte ho do libovolného adresáře.
4. Importujte data JSON (Volcano data) uložená ve [veřejném objektu BLOB](https://data.humdata.org/dataset/a60ac839-920d-435a-bf7d-25855602699d/resource/7234d067-2d74-449a-9c61-22ae6d98d928/download/volcano.json) do Azure Cosmos DB s použitím následujících parametrů příkazu pro nástroj pro migraci. (Použijte dtui.exe z adresáře, do kterého jste nainstalovali Azure Cosmos DB Nástroj pro migraci dat.) Zadejte zdrojové a cílové umístění s těmito parametry:
   
    `/s:JsonFile /s.Files:https://data.humdata.org/dataset/a60ac839-920d-435a-bf7d-25855602699d/resource/7234d067-2d74-449a-9c61-22ae6d98d928/download/volcano.json /t:DocumentDBBulk /t.ConnectionString:AccountEndpoint=https://[DocDBAccountName].documents.azure.com:443/;AccountKey=[[KEY];Database=volcano /t.Collection:volcano1`

Po importu dat můžete přejít na Jupyter a otevřít poznámkový blok s názvem *DocumentDBSample*. Obsahuje kód Pythonu pro přístup k Azure Cosmos DB a provede základní dotazování. Další informace o Azure Cosmos DB najdete na [stránce dokumentace](https://docs.microsoft.com/azure/cosmos-db/)ke službě.

## <a name="use-power-bi-reports-and-dashboards"></a>Použití sestav Power BI a řídicích panelů 
Soubor JSON Volcano můžete vizualizovat z předchozího Azure Cosmos DB příkladu v Power BI Desktop a získat tak vizuální přehledy o datech. Podrobné pokyny jsou k dispozici v [článku Power BI](../../cosmos-db/powerbi-visualize.md). Tady je postup vysoké úrovně:

1. Otevřete Power BI Desktop a vyberte **Načíst data**. Zadejte adresu URL jako: `https://cahandson.blob.core.windows.net/samples/volcano.json` .
2. Měly by se zobrazit záznamy JSON importované jako seznam. Převeďte seznam na tabulku, aby Power BI s ní mohl pracovat.
4. Rozbalte sloupce tak, že vyberete ikonu rozbalit (šipka).
5. Všimněte si, že umístění je pole **záznamu** . Rozbalte záznam a vyberte pouze souřadnice. **Souřadnice** je sloupec seznamu.
6. Přidejte nový sloupec, který převede sloupec souřadnic seznamu na sloupec **LatLong** s oddělovači v podobě čárky. Zřetězení dvou prvků v poli se seznamem souřadnic pomocí vzorce ```Text.From([coordinates]{1})&","&Text.From([coordinates]{0})``` .
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

Nyní máte data v datovém modelu Power BI. Vaše Power BI Desktop instance by měla vypadat takto:

![Power BI Desktop](./media/vm-do-ten-things/PowerBIVolcanoData.png)

Pomocí datového modelu můžete začít sestavovat sestavy a vizualizace. Pomocí kroků v [tomto Power BI článku](../../cosmos-db/powerbi-visualize.md#build-the-reports) sestavíte sestavu.

## <a name="scale-the-dsvm-dynamically"></a>Dynamické škálování DSVM 
DSVM můžete škálovat nahoru a dolů tak, aby vyhovovala potřebám vašeho projektu. Pokud nepotřebujete virtuální počítač používat na večer nebo na víkendech, můžete virtuální počítač vypnout z [Azure Portal](https://portal.azure.com).

> [!NOTE]
> Poplatky za výpočetní výkon se účtují, pokud použijete jenom tlačítko vypnout pro operační systém na VIRTUÁLNÍm počítači. Místo toho byste měli zrušit přidělení DSVM pomocí Azure Portal nebo Cloud Shell.
> 
> 

Může se stát, že budete potřebovat zvládnout rozsáhlou analýzu a potřebujete větší kapacitu procesoru, paměti a disku. Pokud ano, můžete najít volbu velikosti virtuálních počítačů z hlediska PROCESORových jader, instancí založených na GPU pro obsáhlý Learning, kapacitu paměti a typy disků (včetně jednotek Solid-State), které splňují vaše výpočetní a rozpočtové požadavky. Úplný seznam virtuálních počítačů a jejich hodinové výpočetní ceny jsou k dispozici na stránce s cenami služby [Azure Virtual Machines](https://azure.microsoft.com/pricing/details/virtual-machines/) .


## <a name="add-more-tools"></a>Přidat další nástroje
Nástroje předem připravené do DSVM mohou řešit mnoho běžných potřeb datových analýz. Tím ušetříte čas, protože nemusíte instalovat a konfigurovat prostředí jeden po druhém. Ušetří vám také peníze, protože platíte jenom za prostředky, které využijete.

K vylepšení svého analytického prostředí můžete použít další služby Azure Data and Analytics profilované v tomto článku. V některých případech možná budete potřebovat další nástroje, včetně některých vlastních partnerských nástrojů. Máte úplný přístup správce k virtuálnímu počítači, abyste mohli nainstalovat nové nástroje, které potřebujete. Můžete také nainstalovat další balíčky v Pythonu a R, které nejsou předem nainstalované. Pro Python můžete použít buď ```conda``` nebo ```pip``` . Pro R můžete použít ```install.packages()``` v konzole r, nebo použít IDE a vybrat **balíčky**  >  **instalovat balíčky**.

## <a name="deep-learning"></a>Hloubkové učení

Kromě ukázek založených na rozhraní můžete získat sadu komplexních návodů, které byly ověřeny na DSVM. Tyto návody vám pomůžou přejít na začátek vývoje aplikací s hloubkovým učením v doménách, jako jsou obrázky a porozumění textu nebo jazyka.   


- [Spouštění neuronovéch sítí napříč různými architekturami](https://github.com/ilkarman/DeepLearningFrameworks): Tento návod ukazuje, jak migrovat kód z jednoho rozhraní do jiného. Také ukazuje, jak porovnat modely a běhový výkon napříč platformami. 

- Návod, [jak sestavit ucelené řešení pro detekci produktů v rámci imagí](https://github.com/Azure/cortana-intelligence-product-detection-from-images): detekce obrázku je technika, která dokáže vyhledat a klasifikovat objekty v rámci imagí. Tato technologie umožňuje přinést velké výhody v mnoha obchodních doménách v reálném čase. Maloobchodní prodejci můžou například pomocí této techniky určit, který produkt si zákazník vybral z poličky. Tyto informace pomáhají v úložištích spravovat inventář produktů. 

- [Obsáhlý Learning pro zvuk](https://blogs.technet.microsoft.com/machinelearning/2018/01/30/hearing-ai-getting-started-with-deep-learning-for-audio-on-azure/): v tomto kurzu se dozvíte, jak vytvořit model hloubkového učení pro detekci zvukových událostí v [datové sadě městských zvuků](https://serv.cusp.nyu.edu/projects/urbansounddataset/urbansound8k.html). Poskytuje také přehled o tom, jak pracovat se zvukovými daty.

- [Klasifikace textových dokumentů](https://github.com/anargyri/lstm_han): Tento návod ukazuje, jak vytvořit a vyškolit dvě neuronové síťové architektury: hierarchická síť s upozorněním a dlouhodobě krátkodobá paměť (LSTM). Tyto sítě neuronové používají rozhraní Keras API pro obsáhlý Learning ke klasifikaci textových dokumentů. 

## <a name="summary"></a>Shrnutí
Tento článek popisuje některé z věcí, které můžete provádět na Data Science Virtual Machine Microsoftu. Existuje mnoho dalších věcí, které můžete využít k tomu, aby DSVM efektivní analytické prostředí.

