---
title: Nástroje pro úložiště Azure zásobníku
description: Další informace o Azure zásobníku úložiště dat nástroje pro přenos
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/30/2018
ms.author: mabrigg
ms.reviewer: xiaofmao
ms.openlocfilehash: e26a38b8fd7d008a46eba2c41075c5af09a6616a
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2018
---
# <a name="tools-for-azure-stack-storage"></a>Nástroje pro úložiště Azure zásobníku

*Platí pro: Azure zásobníku integrované systémy a Azure zásobníku Development Kit*

Zásobník Microsoft Azure poskytuje sadu služeb úložiště pro disky, objekty BLOB, tabulky, fronty a funkce správy účtu. Pokud chcete ke správě nebo přesun dat do nebo z zásobník úložiště Azure, můžete použít sadu nástrojů Azure Storage. Tento článek obsahuje rychlý přehled dostupných nástrojů.

Nástroj, který nejlépe vyhovuje, závisí na vaše požadavky:
* [AzCopy](#azcopy)

    Nástroj příkazového řádku specifické pro úložiště, které si můžete stáhnout ke zkopírování dat z jednoho objektu do druhého v rámci účtu úložiště nebo mezi účty úložiště.

* [Azure PowerShell](#azure-powershell)

    Prostředí příkazového řádku založené na úlohách a skriptovací jazyk, hlavně pro správu systému.

* [Azure CLI](#azure-cli)

    Open source, a platformy nástroj, který obsahuje sadu příkazů pro práci s platformy Azure a Azure zásobníku.

* [Microsoft Storage Exploreru (Preview)](#microsoft-azure-storage-explorer)

    Snadno použitelný samostatné aplikace pomocí uživatelského rozhraní.

Z důvodu úložiště služby rozdíly mezi Azure a Azure zásobníku může být některé specifické požadavky na jednotlivé nástroje popsané v následujících částech. Porovnání mezi Azure zásobníku úložiště a úložiště Azure najdete v tématu [zásobník úložiště Azure: rozdíly a aspekty](azure-stack-acs-differences.md).


## <a name="azcopy"></a>AzCopy
AzCopy je nástroj příkazového řádku určený ke zkopírování dat do a z Microsoft Azure Blob a Table storage pomocí jednoduchých příkazů s optimální výkon. Data můžete zkopírovat z jednoho objektu do druhého v rámci účtu úložiště nebo mezi účty úložiště. Existují dvě verze AzCopy: AzCopy ve Windows a AzCopy v systému Linux. Azure zásobníku podporuje jenom verze systému Windows. 
 
### <a name="download-and-install-azcopy"></a>Stáhněte a nainstalujte AzCopy 

[Stáhněte si](https://aka.ms/azcopyforazurestack) podporovanou verzi systému Windows zásobníku Azure AzCopy. Můžete nainstalovat a použít AzCopy v zásobníku Azure stejně jako s Azure. Další informace najdete v tématu [přenos dat pomocí nástroje příkazového řádku Azcopy](../../storage/common/storage-use-azcopy.md). 

 - Pro 1802 na aktualizaci nebo novější verze [stáhnout AzCopy 7.1.0](https://aka.ms/azcopyforazurestack20170417).
 - Pro předchozí verze [stáhnout AzCopy 5.0.0](https://aka.ms/azcopyforazurestack20150405).

### <a name="azcopy-command-examples-for-data-transfer"></a>AzCopy příkladech pro přenos dat
Následující příklady ukazují několik typické scénáře pro kopírování dat do a z Azure zásobník objektů BLOB. Další informace najdete v tématu [přenos dat pomocí nástroje příkazového řádku Azcopy](../../storage/storage-use-azcopy.md). 
#### <a name="download-all-blobs-to-local-disk"></a>Stažení všech objektů blob na místní disk
```azcopy
AzCopy.exe /source:https://myaccount.blob.local.azurestack.external/mycontainer /dest:C:\myfolder /sourcekey:<key> /S
```
#### <a name="upload-single-file-to-virtual-directory"></a>Jediný soubor nahrát do virtuálního adresáře 
```azcopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.local.azurestack.external/mycontainer/vd /DestKey:key /Pattern:abc.txt
```
#### <a name="move-data-between-azure-and-azure-stack-storage"></a>Přesun dat mezi Azure a Azure zásobník úložiště 
Asynchronní data přenos mezi Azure Storage a Azure zásobníku není podporován. je třeba zadat o přenos pomocí **/SyncCopy** možnost. 
```azcopy 
Azcopy /Source:https://myaccount.blob.local.azurestack.external/mycontainer /Dest:https://myaccount2.blob.core.windows.net/mycontainer2 /SourceKey:AzSKey /DestKey:Azurekey /S /SyncCopy
```

### <a name="azcopy-known-issues"></a>Azcopy – známé problémy
* Všechny operace AzCopy úložiště souborů není k dispozici, protože soubor úložiště ještě není k dispozici v zásobníku Azure.
* Asynchronní data přenos mezi Azure Storage a Azure zásobníku není podporován. Můžete zadat o přenos pomocí **/SyncCopy** možnost Kopírovat data.
* Zásobník úložiště Azure nepodporuje Linux verzi Azcopy. 

## <a name="azure-powershell"></a>Azure PowerShell
Prostředí Azure PowerShell je modul, který obsahuje rutiny pro správu služeb v Azure a Azure zásobníku. Je to prostředí příkazového řádku založené na úlohách a skriptovací jazyk určený speciálně pro správu systému.

### <a name="install-and-configure-powershell-for-azure-stack"></a>Instalace a konfigurace prostředí PowerShell pro Azure zásobníku
Azure zásobníku kompatibilní prostředí Azure PowerShell moduly jsou nutné k práci s Azure zásobníku. Další informace najdete v tématu [instalaci prostředí PowerShell pro Azure zásobníku](azure-stack-powershell-install.md) a [nakonfigurovat prostředí PowerShell Azure zásobník uživatele](azure-stack-powershell-configure-user.md) Další informace.

### <a name="powershell-sample-script-for-azure-stack"></a>Skript prostředí PowerShell pro Azure zásobníku 
Tato ukázka předpokládá, že máte úspěšně [instalaci prostředí PowerShell pro Azure zásobníku](azure-stack-powershell-install.md). Tento skript vám pomůže dokončit konfiguraci a požádat vašeho klienta Azure zásobníku pověření pro přidání účtu do místního prostředí PowerShell. Potom skript bude nastavit výchozí předplatné Azure, vytvořte nový účet úložiště v Azure, vytvořte nový kontejner v rámci tohoto nového účtu úložiště a nahrajte existující soubor bitové kopie (binární rozsáhlý objekt) kontejneru. Po skript obsahuje seznam všech objektů BLOB v kontejneru, vytvoří nový cílový adresář v místním počítači a stáhnout soubor bitové kopie.

1. Nainstalujte [modulů prostředí Azure PowerShell kompatibilní s Azure zásobníku](azure-stack-powershell-install.md).  
2. Stažení [nástroje potřebné pro práci s Azure zásobníku](azure-stack-powershell-download.md).  
3. Otevřete **Windows PowerShell ISE** a **spustit jako správce**, klikněte na tlačítko **soubor** > **nový** k vytvoření nového souboru skriptu.
4. Zkopírujte následující skript a vložte na nový soubor skriptu.
5. Aktualizujte proměnné skriptu na základě svého nastavení konfigurace. 
6. Poznámka: Tento skript má být spuštěný v kořenu stažené **AzureStack_Tools**. 

```PowerShell 
# begin

$ARMEvnName = "AzureStackUser" # set AzureStackUser as your Azure Stack environemnt name
$ARMEndPoint = "https://management.local.azurestack.external" 
$GraphAudiance = "https://graph.windows.net/" 
$AADTenantName = "<myDirectoryTenantName>.onmicrosoft.com" 

$SubscriptionName = "basic" # Update with the name of your subscription.
$ResourceGroupName = "myTestRG" # Give a name to your new resource group.
$StorageAccountName = "azsblobcontainer" # Give a name to your new storage account. It must be lowercase!
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
Set-AzureRmEnvironment -Name $ARMEvnName -GraphEndpoint $GraphAudiance

# Login
$TenantID = Get-AzsDirectoryTenantId -AADTenantName $AADTenantName -EnvironmentName $ARMEvnName
Login-AzureRmAccount -EnvironmentName $ARMEvnName -TenantId $TenantID 

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
```

### <a name="powershell-known-issues"></a>PowerShell – známé problémy 
Aktuální verze kompatibilní prostředí Azure PowerShell modulu pro zásobník Azure je 1.2.10. Se liší od nejnovější verzi prostředí Azure PowerShell. Tento rozdíl ovlivňuje operace služby úložiště:

* Návratová hodnota formát `Get-AzureRmStorageAccountKey` ve verzi 1.2.10 má dvě vlastnosti: `Key1` a `Key2`, zatímco aktuální verze Azure vrátí pole obsahující všechny klíče účtu.
   ```
   # This command gets a specific key for a Storage account, 
   # and works for Azure PowerShell version 1.4, and later versions.
   (Get-AzureRmStorageAccountKey -ResourceGroupName "RG01" `
   -AccountName "MyStorageAccount").Value[0]

   # This command gets a specific key for a Storage account, 
   # and works for Azure PowerShell version 1.3.2, and previous versions.
   (Get-AzureRmStorageAccountKey -ResourceGroupName "RG01" `
   -AccountName "MyStorageAccount").Key1

   ```
   Další informace najdete v tématu [Get-AzureRmStorageAccountKey](https://docs.microsoft.com/powershell/module/azurerm.storage/Get-AzureRmStorageAccountKey?view=azurermps-4.1.0).

## <a name="azure-cli"></a>Azure CLI
Rozhraní příkazového řádku Azure je prostředí příkazového řádku Azure pro správu prostředků Azure. Můžete ji nainstalovat v systému macOS, Linux a Windows a spusťte jej z příkazového řádku. 

Rozhraní příkazového řádku Azure je optimalizovaná pro správu a správě prostředků Azure z příkazového řádku a vytvářet skripty pro automatizaci, které fungují s Azure Resource Manager. Poskytuje řadu nalézt v portálu Azure zásobníku, včetně přístupu k datům bohaté stejné funkce.

Azure zásobníku vyžaduje Azure CLI verze 2.0. Další informace o instalaci a konfiguraci Azure CLI s Azure zásobníku najdete v tématu [instalace a konfigurace rozhraní příkazového řádku Azure zásobníku](azure-stack-version-profiles-azurecli2.md). Další informace o tom, jak pomocí Azure CLI 2.0 provádět několik úloh práci s prostředky v účtu Azure zásobník úložiště najdete v tématu [použití Azure CLI2.0 s Azure Storage](../../storage/storage-azure-cli.md)

### <a name="azure-cli-sample-script-for-azure-stack"></a>Azure CLI ukázkový skript pro Azure zásobníku 
Po dokončení instalace rozhraní příkazového řádku a konfigurace, zkuste následující kroky pro práci s ukázkového skriptu malé prostředí pro interakci s prostředky Azure zásobníku úložiště. Skript nejprve vytvoří nový kontejner ve vašem účtu úložiště, pak odešle existující soubor (jako objekt blob) do tohoto kontejneru, obsahuje seznam všech objektů BLOB v kontejneru a nakonec stáhne soubor do cílového umístění v místním počítači, který určíte. Před spuštěním tohoto skriptu, ujistěte se, můžete úspěšně připojit a přihlásit se k cíli zásobník Azure. 
1. Otevřete svém oblíbeném textovém editoru, pak zkopírujte a vložte uvedený skript do editoru.
2. Aktualizujte tento skript proměnné tak, aby odrážela nastavení konfigurace. 
3. Po aktualizaci nezbytné proměnné, uložte skript a ukončete editor. Další kroky předpokládají, že jste s názvem vaší my_storage_sample.sh skriptu.
4. Označte do skriptu jako spustitelný soubor, v případě potřeby: `chmod +x my_storage_sample.sh`
5. Spusťte skript. Například v Bash: `./my_storage_sample.sh`

```bash
#!/bin/bash
# A simple Azure Stack Storage example script

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
```

## <a name="microsoft-azure-storage-explorer"></a>Microsoft Azure Storage Explorer

Microsoft Azure Storage Explorer je samostatná aplikace od společnosti Microsoft. Umožňuje snadno pracovat s Azure Storage a Azure zásobníku úložiště dat v systému Windows, systému macOS a Linux. Pokud chcete snadný způsob, jak spravovat data zásobník úložiště Azure, můžete použít Microsoft Azure Storage Explorer.

 - Další informace o konfiguraci Azure Storage Explorer pro práci s zásobník Azure najdete v tématu [Storage Explorer připojit k předplatnému Azure zásobníku](azure-stack-storage-connect-se.md).
 - Další informace o Microsoft Azure Storage Explorer najdete v tématu [Začínáme se Storage Explorerem (Preview)](../../vs-azure-tools-storage-manage-with-storage-explorer.md)

## <a name="next-steps"></a>Další postup
* [Storage Explorer připojení k předplatnému Azure zásobníku](azure-stack-storage-connect-se.md)
* [Začínáme se Storage Explorerem (Preview)](../../vs-azure-tools-storage-manage-with-storage-explorer.md)
* [Konzistentní s Azure storage: rozdíly a důležité informace](azure-stack-acs-differences.md)
* [Úvod do Microsoft Azure Storage](../../storage/common/storage-introduction.md)

