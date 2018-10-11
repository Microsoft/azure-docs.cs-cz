---
title: Nástroje pro úložiště Azure Stack | Dokumentace Microsoftu
description: Další informace o službě Azure Stack úložiště dat nástrojů pro přenos
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/10/2018
ms.author: mabrigg
ms.reviewer: xiaofmao
ms.openlocfilehash: 77b25b7a5291786fe81953499c730fdac506e5c8
ms.sourcegitcommit: 4b1083fa9c78cd03633f11abb7a69fdbc740afd1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/10/2018
ms.locfileid: "49078779"
---
# <a name="use-data-transfer-tools-for-azure-stack-storage"></a>Použití nástrojů pro přenos dat pro úložiště Azure Stack

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

Microsoft Azure Stack nabízí sadu služeb úložišť pro disky, objekty BLOB, tabulky, fronty a funkce pro správu účtu. Pokud chcete spravovat nebo přesun dat do nebo z úložiště služby Azure Stack, můžete použít sadu nástrojů pro Azure storage. Tento článek obsahuje přehled dostupných nástrojů.

Vaše požadavky určují, které tyto nástroje vám nejvíce vyhovuje:

* [AzCopy](#azcopy)

    Specifické pro úložiště, příkazového řádku nástroj, který si můžete stáhnout pro kopírování dat z jednoho objektu na jiný objekt v rámci účtu úložiště nebo mezi účty úložiště.

* [Azure PowerShell](#azure-powershell)

    Prostředí založené na úlohách, příkazového řádku a skriptovací jazyk určený speciálně pro správu systému.

* [Azure CLI](#azure-cli)

    Open source, multiplatformní nástroj, který obsahuje sadu příkazů pro práci s Azure a Azure Stackem platformy.

* [Průzkumník úložišť Microsoft](#microsoft-azure-storage-explorer)

    Snadné použití samostatné aplikace s uživatelským rozhraním.

* [Blobfuse ](#blobfuse)

    Ovladač virtuálním souborovém systému pro Azure Blob Storage, což vám umožní přistupovat k existující data objektů blob bloku v účtu úložiště v systému Linux. 

Vzhledem k rozdílům služby storage mezi Azure a Azure Stack může být některé specifické požadavky pro jednotlivé nástroje popsané v následujících částech. Porovnání mezi službou úložiště služby Azure Stack a Azure storage najdete v tématu [úložiště služby Azure Stack: rozdíly a aspekty](azure-stack-acs-differences.md).

## <a name="azcopy"></a>AzCopy

AzCopy je nástroj příkazového řádku určený ke kopírování dat do a z Microsoft Azure blob a table storage pomocí jednoduchých příkazů s optimálním výkonem. Data z jednoho objektu můžete zkopírovat do jiného v rámci účtu úložiště nebo mezi účty úložiště.

### <a name="download-and-install-azcopy"></a>Stáhněte a nainstalujte nástroje AzCopy

Existují dvě verze nástroje azcopy: AzCopy ve Windows a AzCopy v Linuxu.

 - **AzCopy ve Windows**
    - Stáhněte si podporovanou verzi nástroje AzCopy pro Azure Stack. Můžete nainstalovat a používat AzCopy ve službě Azure Stack stejným způsobem jako Azure. Další informace najdete v tématu [AzCopy ve Windows](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy).
        - Pro 1802 update nebo novější verze [stáhnout AzCopy 7.1.0](https://aka.ms/azcopyforazurestack20170417).
        - U předchozích verzí [stáhnout AzCopy 5.0.0](https://aka.ms/azcopyforazurestack20170417).

 - **AzCopy v Linuxu**

    - AzCopy v Linuxu podporuje aktualizace Azure Stack 1802 nebo novější verze. Můžete nainstalovat a používat AzCopy ve službě Azure Stack stejným způsobem jako Azure. Další informace najdete v tématu [AzCopy v Linuxu](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-linux).

### <a name="azcopy-command-examples-for-data-transfer"></a>Příklady příkazů AzCopy pro přenos dat

Následující příklady jsou některé typické scénáře pro kopírování dat do a z objektů BLOB služby Azure Stack. Další informace najdete v tématu [AzCopy ve Windows](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-linux) a [AzCopy v Linuxu](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-linux).

### <a name="download-all-blobs-to-a-local-disk"></a>Všechny objekty BLOB můžete stáhnout na místní disk

**Windows**

````AzCopy
AzCopy.exe /source:https://myaccount.blob.local.azurestack.external/mycontainer /dest:C:\myfolder /sourcekey:<key> /S
````

**Linux**

````AzCopy
azcopy \
    --source https://myaccount.blob.local.azurestack.external/mycontainer \
    --destination /mnt/myfiles \
    --source-key <key> \
    --recursive
````

### <a name="upload-single-file-to-virtual-directory"></a>Nahrání jednoho souboru do virtuálního adresáře

**Windows**

```AzCopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.local.azurestack.external/mycontainer/vd /DestKey:key /Pattern:abc.txt
```

**Linux**

````AzCopy
azcopy \
    --source /mnt/myfiles/abc.txt \
    --destination https://myaccount.blob.local.azurestack.external/mycontainer/vd/abc.txt \
    --dest-key <key>
````

### <a name="move-data-between-azure-and-azure-stack-storage"></a>Přesun dat mezi Azure a Azure Stackem úložiště

Asynchronní datový přenos mezi Azure storage a Azure Stack se nepodporuje. Je třeba zadat o přenos pomocí **/SyncCopy** nebo **--synchronní kopie** možnost.

**Windows**

````AzCopy
Azcopy /Source:https://myaccount.blob.local.azurestack.external/mycontainer /Dest:https://myaccount2.blob.core.windows.net/mycontainer2 /SourceKey:AzSKey /DestKey:Azurekey /S /SyncCopy
````

**Linux**

````AzCopy
azcopy \
    --source https://myaccount1.blob.local.azurestack.external/myContainer/ \
    --destination https://myaccount2.blob.core.windows.net/myContainer/ \
    --source-key <key1> \
    --dest-key <key2> \
    --include "abc.txt" \
    --sync-copy
````

### <a name="azcopy-known-issues"></a>Azcopy známé problémy

 - Všechny operace AzCopy na soubor úložiště není k dispozici, protože soubor úložiště ještě není k dispozici ve službě Azure Stack.
 - Asynchronní datový přenos mezi Azure storage a Azure Stack se nepodporuje. Můžete určit přenos pomocí **/SyncCopy** možnost Kopírovat data.
 - Verzi Azcopy Linuxu podporuje pouze 1802 update nebo novější verze. A nepodporuje službu Table service.

## <a name="azure-powershell"></a>Azure PowerShell

Prostředí Azure PowerShell je modul, který nabízí rutiny pro správu služeb v Azure a Azure Stack. Je to prostředí založené na úlohách, příkazového řádku a skriptovací jazyk určený speciálně pro správu systému.

### <a name="install-and-configure-powershell-for-azure-stack"></a>Instalace a konfigurace Powershellu pro Azure Stack

Moduly prostředí Azure PowerShell kompatibilní služby Azure Stack jsou vyžadována pro práci s Azure Stack. Další informace najdete v tématu [instalace Powershellu pro Azure Stack](azure-stack-powershell-install.md) a [konfigurace uživatele služby Azure Stack Powershellu prostředí](azure-stack-powershell-configure-user.md) Další informace.

### <a name="powershell-sample-script-for-azure-stack"></a>Powershellu ukázkový skript pro službu Azure Stack 

Tento příklad předpokládá, že jste úspěšně [nainstalované prostředí PowerShell pro Azure Stack](azure-stack-powershell-install.md). Tento skript vám pomůže dokončit konfiguraci a požádejte svého tenanta služby Azure Stack přihlašovací údaje pro přidání účtu do místního prostředí PowerShell. Skript se pak, nastavit výchozí předplatné Azure, vytvořit nový účet úložiště v Azure, vytvořit nový kontejner v rámci tohoto nového účtu úložiště a nahrajte existující soubor bitové kopie (blob) do tohoto kontejneru. Po skript vypíše všechny objekty BLOB v tomto kontejneru, bude vytvoření nového cílového adresáře v místním počítači a stáhněte si soubor bitové kopie.

1. Nainstalujte [moduly Azure Powershellu kompatibilní s Azure Stack](azure-stack-powershell-install.md).
2. Ve službě [Azure Stack development Kit by měl být blobEndpoint](azure-stack-powershell-download.md) .
3. Otevřít **Windows PowerShell ISE** a **spustit jako správce**, klikněte na tlačítko **souboru** > **nový** k vytvoření nového souboru skriptu.
4. Zkopírujte níže uvedený skript a vložte do nového souboru skriptu.
5. Aktualizujte proměnné skript na základě svého nastavení konfigurace.
   > [!NOTE]
   > Tento skript má ke spuštění v kořenovém adresáři pro **AzureStack_Tools**.

```PowerShell  
# begin

$ARMEvnName = "AzureStackUser" # set AzureStackUser as your Azure Stack environemnt name
$ARMEndPoint = "https://management.local.azurestack.external" 
$GraphAudiance = "https://graph.windows.net/" 
$AADTenantName = "<myDirectoryTenantName>.onmicrosoft.com" 

$SubscriptionName = "basic" # Update with the name of your subscription.
$ResourceGroupName = "myTestRG" # Give a name to your new resource group.
$StorageAccountName = "azsblobcontainer" # Give a name to your new storage account. It must be lowercase.
$Location = "Local" # Choose "Local" as an example.
$ContainerName = "photo" # Give a name to your new container.
$ImageToUpload = "C:\temp\Hello.jpg" # Prepare an image file and a source directory in your local computer.
$DestinationFolder = "C:\temp\downlaod" # A destination directory in your local computer.

# Import the Connect PowerShell module"
Set-ExecutionPolicy RemoteSigned -Scope CurrentUser -Force
Import-Module .\Connect\AzureStack.Connect.psm1

# Configure the PowerShell environment
# Register an AzureRM environment that targets your Azure Stack instance
Add-AzureRmEnvironment -Name $ARMEvnName -ARMEndpoint $ARMEndPoint 

# Set the GraphEndpointResourceId value
Set-AzureRmEnvironment -Name $ARMEvnName -GraphEndpoint $GraphAudience

# Login
$TenantID = Get-AzsDirectoryTenantId -AADTenantName $AADTenantName -EnvironmentName $ARMEvnName
Add-AzureRmAccount -EnvironmentName $ARMEvnName -TenantId $TenantID 

# Set a default Azure subscription.
Select-AzureRmSubscription -SubscriptionName $SubscriptionName

# Create a new Resource Group 
New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Location

# Create a new storage account.
New-AzureRmStorageAccount -ResourceGroupName $ResourceGroupName -Name $StorageAccountName -Location $Location -Type Standard_LRS

# Set a default storage account.
Set-AzureRmCurrentStorageAccount -StorageAccountName $StorageAccountName -ResourceGroupName $ResourceGroupName 

# Create a new container.
New-AzureStorageContainer -Name $ContainerName -Permission Off

# Upload a blob into a container.
Set-AzureStorageBlobContent -Container $ContainerName -File $ImageToUpload

# List all blobs in a container.
Get-AzureStorageBlob -Container $ContainerName

# Download blobs from the container:
# Get a reference to a list of all blobs in a container.
$blobs = Get-AzureStorageBlob -Container $ContainerName

# Create the destination directory.
New-Item -Path $DestinationFolder -ItemType Directory -Force  

# Download blobs into the local destination directory.
$blobs | Get-AzureStorageBlobContent –Destination $DestinationFolder

# end
````

### <a name="powershell-known-issues"></a>PowerShell – známé problémy

Aktuální kompatibilní modul verzi Azure Powershellu pro Azure Stack je 1.2.11 pro operace uživatelů. To se liší od nejnovější verzi Azure Powershellu. Operace služby úložiště má vliv na tento rozdíl:

* Návratová hodnota formátu `Get-AzureRmStorageAccountKey` ve verzi 1.2.11 má dvě vlastnosti: `Key1` a `Key2`, ale aktuální verze Azure vrátí pole obsahující všechny klíče účtu.

   ```
   # This command gets a specific key for a storage account, 
   # and works for Azure PowerShell version 1.4, and later versions.
   (Get-AzureRmStorageAccountKey -ResourceGroupName "RG01" `
   -AccountName "MyStorageAccount").Value[0]

   # This command gets a specific key for a storage account, 
   # and works for Azure PowerShell version 1.3.2, and previous versions.
   (Get-AzureRmStorageAccountKey -ResourceGroupName "RG01" `
   -AccountName "MyStorageAccount").Key1

   ```

   Další informace najdete v tématu [Get-AzureRmStorageAccountKey](https://docs.microsoft.com/powershell/module/azurerm.storage/Get-AzureRmStorageAccountKey?view=azurermps-4.1.0).

## <a name="azure-cli"></a>Azure CLI

Azure CLI je prostředí příkazového řádku Azure pro správu prostředků Azure. Můžete nainstalovat v systému macOS, Linux a Windows a spouštět z příkazového řádku.

Rozhraní příkazového řádku Azure je optimalizovaná pro správu prostředků Azure z příkazového řádku a pro vytváření skriptů pro automatizaci, které fungují s Azure Resource Managerem. Poskytuje řadu stejných funkcí najít na portálu Azure Stack, včetně velké množství dat přístup.

Azure Stack vyžaduje použití Azure CLI verze 2.0 nebo novější. Další informace ohledně instalace a konfigurace Azure CLI s Azure Stackem najdete v tématu [instalace a konfigurace rozhraní příkazového řádku Azure Stack](azure-stack-version-profiles-azurecli2.md). Další informace o tom, jak provést několik úloh, práci s prostředky ve vašem účtu úložiště Azure Stack pomocí Azure CLI najdete v tématu [pomocí Azure CLI s Azure storage](../../storage/storage-azure-cli.md)

### <a name="azure-cli-sample-script-for-azure-stack"></a>Ukázkový skript Azure CLI pro službu Azure Stack

Po dokončení instalace rozhraní příkazového řádku a konfigurace, zkuste následující kroky při práci s malé prostředí ukázkový skript pro interakci s prostředky úložiště služby Azure Stack. Skript provede následující akce:

* Vytvoří nový kontejner v účtu úložiště.
* Nahrání existující soubor (jako objekt blob) do kontejneru.
* Vypíše všechny objekty BLOB v kontejneru.
* Stáhne soubor do cílového umístění na místním počítači, který zadáte.

Před spuštěním tohoto skriptu, ujistěte se, že můžete úspěšně připojit k a přihlaste se k cílové službě Azure Stack.

1. Otevřete oblíbený textový editor, pak zkopírujte a vložte předchozí skript do editoru.
2. Aktualizujte tento skript proměnné tak, aby odrážely nastavení konfigurace.
3. Po aktualizaci nezbytné proměnné, uložte skript a ukončete editor. Další kroky předpokládají, že pojmenujete skriptu **my_storage_sample.sh**.
4. Označí skript jako spustitelný soubor, v případě potřeby: `chmod +x my_storage_sample.sh`
5. Spusťte skript. Například v prostředí Bash: `./my_storage_sample.sh`

```bash
#!/bin/bash
# A simple Azure Stack storage example script

export AZURESTACK_RESOURCE_GROUP=<resource_group_name>
export AZURESTACK_RG_LOCATION="local"
export AZURESTACK_STORAGE_ACCOUNT_NAME=<storage_account_name>
export AZURESTACK_STORAGE_CONTAINER_NAME=<container_name>
export AZURESTACK_STORAGE_BLOB_NAME=<blob_name>
export FILE_TO_UPLOAD=<file_to_upload>
export DESTINATION_FILE=<destination_file>

echo "Creating the resource group..."
az group create --name $AZURESTACK_RESOURCE_GROUP --location $AZURESTACK_RG_LOCATION

echo "Creating the storage account..."
az storage account create --name $AZURESTACK_STORAGE_ACCOUNT_NAME --resource-group $AZURESTACK_RESOURCE_GROUP --account-type Standard_LRS

echo "Creating the blob container..."
az storage container create --name $AZURESTACK_STORAGE_CONTAINER_NAME --account-name $AZURESTACK_STORAGE_ACCOUNT_NAME

echo "Uploading the file..."
az storage blob upload --container-name $AZURESTACK_STORAGE_CONTAINER_NAME --file $FILE_TO_UPLOAD --name $AZURESTACK_STORAGE_BLOB_NAME --account-name $AZURESTACK_STORAGE_ACCOUNT_NAME

echo "Listing the blobs..."
az storage blob list --container-name $AZURESTACK_STORAGE_CONTAINER_NAME --account-name $AZURESTACK_STORAGE_ACCOUNT_NAME --output table

echo "Downloading the file..."
az storage blob download --container-name $AZURESTACK_STORAGE_CONTAINER_NAME --account-name $AZURESTACK_STORAGE_ACCOUNT_NAME --name $AZURESTACK_STORAGE_BLOB_NAME --file $DESTINATION_FILE --output table

echo "Done"
````

## <a name="microsoft-azure-storage-explorer"></a>Průzkumník služby Microsoft Azure storage

Microsoft Azure storage explorer je samostatná aplikace od Microsoftu. Umožňuje jednoduchá práci s Azure storage a Azure Stack úložiště dat na Windows, macOS a počítačů s Linuxem. Pokud chcete snadný způsob, jak spravovat vaše data služby úložiště Azure Stack, zvažte použití Průzkumníka služby Microsoft Azure storage.

* Další informace o konfiguraci služby Azure storage explorer pro práci s Azure Stackem najdete v tématu [připojení storage Exploreru k předplatnému Azure Stack](azure-stack-storage-connect-se.md).
* Další informace o Průzkumníku služby Microsoft Azure storage najdete v tématu [Začínáme se storage Explorerem](../../vs-azure-tools-storage-manage-with-storage-explorer.md)

## <a name="blobfuse"></a>Blobfuse 

[Blobfuse](https://github.com/Azure/azure-storage-fuse) je ovladač virtuálním souborovém systému pro Azure Blob Storage, což vám umožní přistupovat k existující data objektů blob bloku v účtu úložiště v systému Linux. Je služba pro úložiště objektů Azure Blob Storage a proto nemá hierarchického oboru názvů. Tento obor názvů používá schéma virtuální direcectory s použitím lomítky poskytuje Blobfuse `/` jako oddělovač. Blobfuse funguje na platformě Azure a Azure Stack. 

Další informace o připojení úložiště objektů Blob jako systém souborů s Blobfuse v Linuxu najdete v tématu [postup připojení služby Blob storage jako systém souborů s Blobfuse](https://docs.microsoft.com/azure/storage/blobs/storage-how-to-mount-container-linux). 

Pro službu Azure Stack **blobEndpoint** musí být zadaná kromě accountName, accountKey/sasToken, containerName, při konfiguraci přihlašovacích údajů účtu úložiště v kroku přípravy na připojení. 

Ve službě Azure Stack development Kit by měl být blobEndpoint `myaccount.blob.local.azurestack.external`. V systémech pro Azure Stack integrované kontaktujte správce cloudu, pokud si nejste jisti o váš koncový bod. 

Uvědomte si, že accountKey a sasToken lze pouze nakonfigurovaný jeden po druhém. Když je klíč účtu úložiště, konfigurační soubor přihlašovacích údajů je v následujícím formátu: 

```text  
    accountName myaccount 
    accountKey myaccesskey== 
    containerName mycontainer 
    blobEndpoint myaccount.blob.local.azurestack.external
```

Když je zadaný token sdíleného přístupového, konfigurační soubor přihlašovacích údajů je v následujícím formátu:

```text  
    accountName myaccount 
    sasToken ?mysastoken 
    containerName mycontainer 
    blobEndpoint myaccount.blob.local.azurestack.external
```

## <a name="next-steps"></a>Další postup

* [Připojení Průzkumníka služby storage k předplatnému Azure Stack](azure-stack-storage-connect-se.md)
* [Začínáme se storage Explorerem](../../vs-azure-tools-storage-manage-with-storage-explorer.md)
* [Konzistentní s Azure storage: rozdíly a aspekty](azure-stack-acs-differences.md)
* [Úvod do Microsoft Azure storage](../../storage/common/storage-introduction.md)
