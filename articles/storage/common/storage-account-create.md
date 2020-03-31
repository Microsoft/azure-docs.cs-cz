---
title: vytvořit účet úložiště
titleSuffix: Azure Storage
description: Naučte se vytvořit účet úložiště pomocí portálu Azure, Azure PowerShellu nebo azure cli. Účet úložiště Azure poskytuje jedinečný obor názvů v Microsoft Azure pro ukládání a přístup k vašim datům.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 02/07/2020
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: c2d1e8b4975be0657983192df00cc434da00a6f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79255402"
---
# <a name="create-an-azure-storage-account"></a>Vytvoření účtu služby Azure Storage

Účet úložiště Azure obsahuje všechny datové objekty Azure Storage: objekty BLOB, soubory, fronty, tabulky a disky. Účet úložiště poskytuje jedinečný obor názvů pro vaše data azure storage, která je přístupná z libovolného místa na světě přes HTTP nebo HTTPS. Data ve vašem účtu úložiště Azure jsou trvanlivá a vysoce dostupná, zabezpečená a masivně škálovatelná.

V tomto článku s návody se naučíte vytvořit účet úložiště pomocí [portálu Azure](https://portal.azure.com/), [Azure PowerShellu](https://docs.microsoft.com/powershell/azure/overview), [Azure CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest)nebo [šablony Azure Resource Manager](../../azure-resource-manager/management/overview.md).  

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Požadavky

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

# <a name="portal"></a>[Portál](#tab/azure-portal)

Žádné.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Pokud chcete vytvořit účet úložiště Azure s PowerShellem, ujistěte se, že jste nainstalovali modul Azure PowerShell Az verze 0.7 nebo novější. Další informace najdete [v tématu Představení modulu Azure PowerShell Az](/powershell/azure/new-azureps-module-az).

Chcete-li najít aktuální verzi, spusťte následující příkaz:

```powershell
Get-InstalledModule -Name "Az"
```

Pokud chcete nainstalovat nebo upgradovat Azure PowerShell, přečtěte si informace [o instalaci modulu Azure PowerShell](/powershell/azure/install-Az-ps).

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

K Azure se můžete přihlásit a spouštět příkazy azure cli jedním ze dvou způsobů:

- Příkazy příkazového příkazu můžete spouštět z portálu Azure v Azure Cloud Shellu.
- Můžete nainstalovat příkaz příkazpříkazy příkazového příkazu a spustit příkazy příkazového příkazu místně.

### <a name="use-azure-cloud-shell"></a>Použití Azure Cloud Shellu

Azure Cloud Shell je volně dostupné prostředí Bash, které můžete spustit přímo z webu Azure Portal. Nastavení příkazového odpočtů Azure je předinstalované a nakonfigurované pro použití s vaším účtem. Klikněte na tlačítko **Cloud Shell** v nabídce v pravé horní části portálu Azure:

[![Prostředí cloudshellu](./media/storage-quickstart-create-account/cloud-shell-menu.png)](https://portal.azure.com)

Tlačítko spustí interaktivní prostředí, které můžete použít ke spuštění kroků popsaných v tomto článku s postupy:

[![Snímek obrazovky s oknem Cloud Shell na portálu](./media/storage-quickstart-create-account/cloud-shell.png)](https://portal.azure.com)

### <a name="install-the-cli-locally"></a>Místní instalace rozhraní příkazového řádku

Rozhraní příkazového řádku Azure můžete také nainstalovat a používat místně. Tento článek s návody vyžaduje, abyste spouštěli Azure CLI verze 2.0.4 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace rozhraní příkazového řádku Azure CLI](/cli/azure/install-azure-cli). 

# <a name="template"></a>[Šablony](#tab/template)

Žádné.

---

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

# <a name="portal"></a>[Portál](#tab/azure-portal)

Přihlaste se k [portálu Azure](https://portal.azure.com).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Přihlaste se k `Connect-AzAccount` předplatnému Azure pomocí příkazu a postupujte podle pokynů na obrazovce k ověření.

```powershell
Connect-AzAccount
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pokud chcete spustit Azure Cloud Shell, přihlaste se na [portál Azure](https://portal.azure.com).

Chcete-li se přihlásit k místní instalaci příkazu příkazu CLI, spusťte příkaz [az login:](/cli/azure/reference-index#az-login)

```azurecli-interactive
az login
```

# <a name="template"></a>[Šablony](#tab/template)

Není dostupné.

---

## <a name="create-a-storage-account"></a>vytvořit účet úložiště

Nyní jste připraveni vytvořit účet úložiště.

Každý účet úložiště musí patřit do nějaké skupiny prostředků Azure. Skupina prostředků je logický kontejner pro seskupení služeb Azure. Při vytváření účtu úložiště máte možnost buď vytvořit novou skupinu prostředků, nebo použít některou existující skupinu prostředků. Tento článek ukazuje, jak vytvořit novou skupinu prostředků.

Účet úložiště **Univerzální v2** poskytuje přístup ke všem službám Azure Storage: objektům blob, souborům, frontám, tabulkám a diskům. Kroky popsané zde vytvořit účet úložiště pro obecné účely v2, ale kroky k vytvoření libovolného typu účtu úložiště jsou podobné.

# <a name="portal"></a>[Portál](#tab/azure-portal)

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Nejprve vytvořte novou skupinu prostředků pomocí prostředí PowerShell pomocí příkazu [New-AzResourceGroup:](/powershell/module/az.resources/new-azresourcegroup)

```powershell
# put resource group in a variable so you can use the same group name going forward,
# without hard-coding it repeatedly
$resourceGroup = "storage-resource-group"
$location = "westus"
New-AzResourceGroup -Name $resourceGroup -Location $location
```

Pokud si nejste jisti, kterou `-Location` oblast pro parametr zadáte, můžete načíst seznam podporovaných oblastí pro vaše předplatné pomocí příkazu [Získat AzLocation:](/powershell/module/az.resources/get-azlocation)

```powershell
Get-AzLocation | select Location
```

Dále vytvořte účet úložiště pro obecné účely v2 s geograficky redundantním úložištěm pro čtení (RA-GRS) pomocí příkazu [New-AzStorageAccount.](/powershell/module/az.storage/New-azStorageAccount) Nezapomeňte, že název vašeho účtu úložiště musí být v rámci Azure jedinečný, proto zástupnou hodnotu v závorkách nahraďte vlastní jedinečnou hodnotou:

```powershell
New-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name <account-name> `
  -Location $location `
  -SkuName Standard_RAGRS `
  -Kind StorageV2
```

> [!IMPORTANT]
> Pokud máte v plánu používat Azure `-EnableHierarchicalNamespace $True` Data Lake [Storage](https://azure.microsoft.com/services/storage/data-lake-storage/), zadejte do tohoto seznamu parametrů.

Chcete-li vytvořit účet úložiště pro obecné účely v2 s jinou možností replikace, nahraďte požadovanou hodnotu v následující tabulce parametrem **SkuName.**

|Možnost replikace  |Parametr SkuName  |
|---------|---------|
|Místně redundantní úložiště (LRS)     |Standard_LRS         |
|Zónově redundantní úložiště (ZRS)     |Standard_ZRS         |
|Geograficky redundantní úložiště (GRS)     |Standard_GRS         |
|Geograficky redundantní úložiště s přístupem pro čtení (RA-GRS)     |Standard_RAGRS         |
|Geograficky redundantní úložiště (GZRS) (náhled)    |Standard_GZRS         |
|Geograficky redundantní úložiště pro čtení (RA-GZRS) (náhled)    |Standard_RAGZRS         |

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Napřed vytvořte novou skupinu prostředků v Azure CLI pomocí příkazu [az group create](/cli/azure/group#az_group_create).

```azurecli-interactive
az group create \
    --name storage-resource-group \
    --location westus
```

Pokud si nejste jisti, jakou oblast máte zadat v parametru `--location`, můžete pomocí příkazu [az account list-locations](/cli/azure/account#az_account_list) načíst seznam podporovaných oblastí pro vaše předplatné.

```azurecli-interactive
az account list-locations \
    --query "[].{Region:name}" \
    --out table
```

Dále vytvořte účet úložiště pro obecné účely v2 s geograficky redundantním úložištěm pro přístup ke čtení pomocí příkazu [az storage create.](/cli/azure/storage/account#az_storage_account_create) Nezapomeňte, že název vašeho účtu úložiště musí být v rámci Azure jedinečný, proto zástupnou hodnotu v závorkách nahraďte vlastní jedinečnou hodnotou:

```azurecli-interactive
az storage account create \
    --name <account-name> \
    --resource-group storage-resource-group \
    --location westus \
    --sku Standard_RAGRS \
    --kind StorageV2
```

> [!IMPORTANT]
> Pokud máte v plánu používat Azure `--enable-hierarchical-namespace true` Data Lake [Storage](https://azure.microsoft.com/services/storage/data-lake-storage/), zadejte do tohoto seznamu parametrů. 

Chcete-li vytvořit účet úložiště pro obecné účely v2 s jinou možností replikace, nahraďte parametr **sku** požadovanou hodnotu v následující tabulce.

|Možnost replikace  |Parametr sku  |
|---------|---------|
|Místně redundantní úložiště (LRS)     |Standard_LRS         |
|Zónově redundantní úložiště (ZRS)     |Standard_ZRS         |
|Geograficky redundantní úložiště (GRS)     |Standard_GRS         |
|Geograficky redundantní úložiště s přístupem pro čtení (RA-GRS)     |Standard_RAGRS         |
|Geograficky redundantní úložiště (GZRS) (náhled)    |Standard_GZRS         |
|Geograficky redundantní úložiště pro čtení (RA-GZRS) (náhled)    |Standard_RAGZRS         |

# <a name="template"></a>[Šablony](#tab/template)

Azure Powershell u tvořící příkazy k nasazení šablony Správce prostředků k vytvoření účtu úložiště. Šablona použitá v tomto článku s návody je ze [šablon rychlého startu Azure Resource Manageru](https://azure.microsoft.com/resources/templates/101-storage-account-create/). Pokud chcete skripty spustit, vyberte **Try it** a otevřete prostředí Azure Cloud. Chcete-li skript vložit, klepněte pravým tlačítkem myši na prostředí a vyberte **příkaz Vložit**.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location "$location"
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json"
```

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the location (i.e. centralus):" &&
read location &&
az group create --name $resourceGroupName --location "$location" &&
az group deployment create --resource-group $resourceGroupName --template-file "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json"
```

> [!NOTE]
> Tato šablona slouží pouze jako příklad. Existuje mnoho nastavení účtu úložiště, které nejsou nakonfigurovány jako součást této šablony. Chcete-li například použít [Azure Data Lake Storage](https://azure.microsoft.com/services/storage/data-lake-storage/), upravte `isHnsEnabledad` tuto `StorageAccountPropertiesCreateParameters` šablonu `true`nastavením vlastnosti objektu na . 

Informace o úpravě této šablony nebo vytvoření nové šablony naleznete v těchto tématech:

- [Dokumentace správce prostředků Azure](/azure/azure-resource-manager/).
- [Odkaz na šablonu účtu úložiště](/azure/templates/microsoft.storage/allversions).
- [Další ukázky šablony účtu úložiště](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Storage).

---

Další informace o dostupných možnostech replikace najdete v tématu věnovaném [možnostem replikace služby Storage](storage-redundancy.md).

## <a name="delete-a-storage-account"></a>Odstranění účtu úložiště

Odstraněním účtu úložiště odstraníte celý účet, včetně všech dat v účtu, a nelze jej vrátit zpět.

# <a name="portal"></a>[Portál](#tab/azure-portal)

1. Přejděte na účet úložiště na [webu Azure Portal](https://portal.azure.com).
1. Klepněte na **tlačítko Odstranit**.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Chcete-li odstranit účet úložiště, použijte příkaz [Remove-AzStorageAccount:](/powershell/module/az.storage/remove-azstorageaccount)

```powershell
Remove-AzStorageAccount -Name <storage-account> -ResourceGroupName <resource-group>
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Chcete-li odstranit účet úložiště, použijte příkaz [delete účtu úložiště az:](/cli/azure/storage/account#az-storage-account-delete)

```azurecli-interactive
az storage account delete --name <storage-account> --resource-group <resource-group>
```

# <a name="template"></a>[Šablony](#tab/template)

Pokud chcete odstranit účet úložiště, použijte Azure PowerShell nebo Azure CLI.

```azurepowershell-interactive
$storageResourceGroupName = Read-Host -Prompt "Enter the resource group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"
Remove-AzStorageAccount -Name $storageAccountName -ResourceGroupName $storageResourceGroupName
```

```azurecli-interactive
echo "Enter the resource group name:" &&
read resourceGroupName &&
echo "Enter the storage account name:" &&
read storageAccountName &&
az storage account delete --name storageAccountName --resource-group resourceGroupName
```

---

Případně můžete odstranit skupinu prostředků, která odstraní účet úložiště a všechny další prostředky v této skupině prostředků. Další informace o odstranění skupiny prostředků naleznete v tématu [Odstranění skupiny prostředků a prostředků](../../azure-resource-manager/management/delete-resource-group.md).

> [!WARNING]
> Odstraněný účet úložiště není možné obnovit ani není možné načíst žádný obsah, který byl součástí účtu před jeho odstraněním. Nezapomeňte si před odstraněním účtu zazálohovat všechno, co chcete uložit. To platí také pro všechny prostředky v rámci účtu – po odstranění jsou objekt blob, tabulka, fronta nebo soubor odstraněny trvale.
>
> Pokud se pokusíte odstranit účet úložiště, který je přidružený virtuálnímu počítači Azure, můžete obdržet chybu, že se účet úložiště stále používá. Nápovědu k řešení této chyby naleznete [v tématu Poradce při odstraňování chyb při odstraňování účtů úložiště](../common/storage-resource-manager-cannot-delete-storage-account-container-vhd.md).

## <a name="next-steps"></a>Další kroky

V tomto článku s návody jste vytvořili účet standardního úložiště pro obecné účely v2. Pokud se chcete dozvědět, jak nahrát a stáhnout objekty BLOB do a z účtu úložiště, pokračujte na jeden z rychlých startů úložiště objektů Blob.

# <a name="portal"></a>[Portál](#tab/azure-portal)

> [!div class="nextstepaction"]
> [Práce s objekty blob pomocí webu Azure Portal](../blobs/storage-quickstart-blobs-portal.md)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

> [!div class="nextstepaction"]
> [Práce s objekty blob pomocí PowerShellu](../blobs/storage-quickstart-blobs-powershell.md)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

> [!div class="nextstepaction"]
> [Práce s objekty BLOB pomocí příkazového příkazového příkazu Azure](../blobs/storage-quickstart-blobs-cli.md)

# <a name="template"></a>[Šablony](#tab/template)

> [!div class="nextstepaction"]
> [Práce s objekty blob pomocí webu Azure Portal](../blobs/storage-quickstart-blobs-portal.md)

---
