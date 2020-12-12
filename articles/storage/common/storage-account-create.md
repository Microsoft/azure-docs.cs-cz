---
title: Vytvoření účtu úložiště
titleSuffix: Azure Storage
description: Naučte se vytvořit účet úložiště pro ukládání objektů blob, souborů, front a tabulek. Účet úložiště Azure poskytuje jedinečný obor názvů v Microsoft Azure pro čtení a zápis dat.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/11/2020
ms.author: tamram
ms.subservice: common
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: c8d07e53e259fff28e662f0c1ca3934eabf015e8
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2020
ms.locfileid: "97357445"
---
# <a name="create-a-storage-account"></a>Vytvoření účtu úložiště

Účet úložiště Azure obsahuje všechny vaše Azure Storage datové objekty: objekty blob, soubory, fronty, tabulky a disky. Účet úložiště poskytuje jedinečný obor názvů pro data Azure Storage, která jsou přístupná odkudkoli na světě přes protokol HTTP nebo HTTPS. Data v účtu úložiště Azure jsou trvalá a vysoce dostupná, zabezpečená a rozsáhlá.

V tomto článku se dozvíte, jak vytvořit účet úložiště pomocí [Azure Portal](https://portal.azure.com/), [Azure POWERSHELL](/powershell/azure/), [Azure CLI](/cli/azure)nebo [šablony pro Azure Resource Manager](../../azure-resource-manager/management/overview.md).  

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Požadavky

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

Žádné

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Pokud chcete vytvořit účet Azure Storage pomocí PowerShellu, ujistěte se, že máte nainstalovaný Azure PowerShell modul AZ verze 0,7 nebo novější. Další informace najdete v tématu [představení modulu Azure PowerShell AZ Module](/powershell/azure/new-azureps-module-az).

Aktuální verzi zjistíte spuštěním následujícího příkazu:

```powershell
Get-InstalledModule -Name "Az"
```

Pokud chcete nainstalovat nebo upgradovat Azure PowerShell, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-Az-ps).

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Můžete se přihlásit k Azure a spustit příkazy rozhraní příkazového řádku Azure CLI jedním ze dvou způsobů:

- Příkazy rozhraní příkazového řádku můžete spustit z Azure Portal v Azure Cloud Shell.
- Můžete nainstalovat rozhraní příkazového řádku a spustit příkazy rozhraní příkazového řádku místně.

### <a name="use-azure-cloud-shell"></a>Použití služby Azure Cloud Shell

Azure Cloud Shell je volně dostupné prostředí Bash, které můžete spustit přímo z webu Azure Portal. Rozhraní příkazového řádku Azure je předem nainstalované a nakonfigurované pro použití s vaším účtem. V nabídce v pravé horní části Azure Portal klikněte na tlačítko **Cloud Shell** :

[![Cloud Shell](./media/storage-quickstart-create-account/cloud-shell-menu.png)](https://portal.azure.com)

Tlačítko spustí interaktivní prostředí, které můžete použít ke spuštění kroků popsaných v tomto článku s návody:

[![Snímek obrazovky zobrazující okno Cloud Shell na portálu](./media/storage-quickstart-create-account/cloud-shell.png)](https://portal.azure.com)

### <a name="install-the-cli-locally"></a>Místní instalace rozhraní příkazového řádku

Rozhraní příkazového řádku Azure můžete také nainstalovat a používat místně. Příklady v tomto článku vyžadují Azure CLI verze 2.0.4 nebo novější. Pokud `az --version` chcete najít nainstalovanou verzi, spusťte příkaz. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace rozhraní příkazového řádku Azure CLI](/cli/azure/install-azure-cli).

# <a name="template"></a>[Šablona](#tab/template)

Žádné

---

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

Přihlaste se na [Azure Portal](https://portal.azure.com).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Přihlaste se k předplatnému Azure pomocí `Connect-AzAccount` příkazu a podle pokynů na obrazovce proveďte ověření.

```powershell
Connect-AzAccount
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pokud chcete spustit Azure Cloud Shell, přihlaste se k [Azure Portal](https://portal.azure.com).

K místní instalaci rozhraní příkazového řádku se přihlaste spuštěním příkazu [AZ Login](/cli/azure/reference-index#az-login) :

```azurecli-interactive
az login
```

# <a name="template"></a>[Šablona](#tab/template)

–

---

## <a name="create-a-storage-account"></a>Vytvoření účtu úložiště

Každý účet úložiště musí patřit do nějaké skupiny prostředků Azure. Skupina prostředků je logický kontejner pro seskupení služeb Azure. Při vytváření účtu úložiště máte možnost buď vytvořit novou skupinu prostředků, nebo použít některou existující skupinu prostředků. V tomto článku se dozvíte, jak vytvořit novou skupinu prostředků.

Účet úložiště **Univerzální v2** poskytuje přístup ke všem službám Azure Storage: objektům blob, souborům, frontám, tabulkám a diskům. Zde popsané kroky vytvoří účet úložiště pro obecné účely v2, ale postup vytvoření libovolného typu účtu úložiště je podobný. Další informace o typech účtů úložiště a dalším nastavení účtu úložiště najdete v článku s [přehledem účtu Azure Storage](storage-account-overview.md).

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Nejdřív pomocí příkazu [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) vytvořte novou skupinu prostředků s PowerShellem:

```powershell
# put resource group in a variable so you can use the same group name going forward,
# without hard-coding it repeatedly
$resourceGroup = "storage-resource-group"
$location = "westus"
New-AzResourceGroup -Name $resourceGroup -Location $location
```

Pokud si nejste jistí, kterou oblast pro parametr určíte `-Location` , můžete získat seznam podporovaných oblastí pro vaše předplatné pomocí příkazu [Get-AzLocation](/powershell/module/az.resources/get-azlocation) :

```powershell
Get-AzLocation | select Location
```

Dále vytvořte účet úložiště pro obecné účely v2 s geograficky redundantním úložištěm s přístupem pro čtení (RA-GRS) pomocí příkazu [New-AzStorageAccount](/powershell/module/az.storage/New-azStorageAccount) . Mějte na paměti, že název účtu úložiště musí být v rámci Azure jedinečný, proto nahraďte hodnotu zástupný symbol v závorkách vlastní jedinečnou hodnotou:

```powershell
New-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name <account-name> `
  -Location $location `
  -SkuName Standard_RAGRS `
  -Kind StorageV2
```

> [!IMPORTANT]
> Pokud plánujete použít [Azure Data Lake Storage](https://azure.microsoft.com/services/storage/data-lake-storage/), zahrňte `-EnableHierarchicalNamespace $True` do tohoto seznamu parametrů.

Pokud chcete vytvořit účet úložiště pro obecné účely v2 s jinou možností replikace, nahraďte požadovanou hodnotu v následující tabulce pro parametr **SkuName** .

|Možnost replikace  |Parametr SkuName  |
|---------|---------|
|Místně redundantní úložiště (LRS)     |Standard_LRS         |
|Zónově redundantní úložiště (ZRS)     |Standard_ZRS         |
|Geograficky redundantní úložiště (GRS)     |Standard_GRS         |
|Geograficky redundantní úložiště s přístupem pro čtení (RA-GRS)     |Standard_RAGRS         |
|Geograficky zónově redundantní úložiště (GZRS)    |Standard_GZRS         |
|Geograficky zónově redundantní úložiště s přístupem pro čtení (RA-GZRS)    |Standard_RAGZRS         |

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

V dalším kroku vytvořte účet úložiště pro obecné účely v2 s geograficky redundantním úložištěm s přístupem pro čtení pomocí příkazu [AZ Storage Account Create](/cli/azure/storage/account#az_storage_account_create) . Mějte na paměti, že název účtu úložiště musí být v rámci Azure jedinečný, proto nahraďte hodnotu zástupný symbol v závorkách vlastní jedinečnou hodnotou:

```azurecli-interactive
az storage account create \
    --name <account-name> \
    --resource-group storage-resource-group \
    --location westus \
    --sku Standard_RAGRS \
    --kind StorageV2
```

> [!IMPORTANT]
> Pokud plánujete použít [Azure Data Lake Storage](https://azure.microsoft.com/services/storage/data-lake-storage/), zahrňte `--enable-hierarchical-namespace true` do tohoto seznamu parametrů.

Pokud chcete vytvořit účet úložiště pro obecné účely v2 s jinou možností replikace, nahraďte požadovanou hodnotu v následující tabulce pro parametr **SKU** .

|Možnost replikace  |Parametr sku  |
|---------|---------|
|Místně redundantní úložiště (LRS)     |Standard_LRS         |
|Zónově redundantní úložiště (ZRS)     |Standard_ZRS         |
|Geograficky redundantní úložiště (GRS)     |Standard_GRS         |
|Geograficky redundantní úložiště s přístupem pro čtení (RA-GRS)     |Standard_RAGRS         |
|Geograficky zónově redundantní úložiště (GZRS)    |Standard_GZRS         |
|Geograficky zónově redundantní úložiště s přístupem pro čtení (RA-GZRS)    |Standard_RAGZRS         |

# <a name="template"></a>[Šablona](#tab/template)

K nasazení Správce prostředků šablony pro vytvoření účtu úložiště můžete použít buď Azure PowerShell, nebo rozhraní příkazového řádku Azure. Šablona použitá v tomto článku s návody je od [Azure Resource Manager šablon pro rychlý Start](https://azure.microsoft.com/resources/templates/101-storage-account-create/). Chcete-li spustit skripty, vyberte příkaz **zkusit** pro otevření Azure Cloud Shell. Skript vložíte tak, že kliknete pravým tlačítkem na prostředí a pak vyberete **Vložit**.

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
> Tato šablona slouží pouze jako příklad. K dispozici je mnoho nastavení účtu úložiště, která nejsou nakonfigurována jako součást této šablony. Například pokud chcete použít [Azure Data Lake Storage](https://azure.microsoft.com/services/storage/data-lake-storage/), měli byste tuto šablonu upravit nastavením `isHnsEnabledad` vlastnosti `StorageAccountPropertiesCreateParameters` objektu na `true` . 

Informace o tom, jak upravit tuto šablonu nebo vytvořit nové, najdete v těchto tématech:

- [Azure Resource Manager dokumentaci](../../azure-resource-manager/index.yml).
- [Odkaz na šablonu účtu úložiště](/azure/templates/microsoft.storage/allversions)
- [Další ukázky šablon účtu úložiště](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Storage)

---

## <a name="delete-a-storage-account"></a>Odstranění účtu úložiště

Odstranění účtu úložiště odstraní celý účet včetně všech dat v účtu a nedá se vrátit zpátky.

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

1. V [Azure Portal](https://portal.azure.com)přejděte na účet úložiště.
1. Klikněte na **Odstranit**.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Pokud chcete odstranit účet úložiště, použijte příkaz [Remove-AzStorageAccount](/powershell/module/az.storage/remove-azstorageaccount) :

```powershell
Remove-AzStorageAccount -Name <storage-account> -ResourceGroupName <resource-group>
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pokud chcete odstranit účet úložiště, použijte příkaz [AZ Storage Account Delete](/cli/azure/storage/account#az-storage-account-delete) :

```azurecli-interactive
az storage account delete --name <storage-account> --resource-group <resource-group>
```

# <a name="template"></a>[Šablona](#tab/template)

Pokud chcete odstranit účet úložiště, použijte buď Azure PowerShell, nebo rozhraní příkazového řádku Azure.

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

Alternativně můžete odstranit skupinu prostředků, která odstraní účet úložiště a všechny další prostředky v dané skupině prostředků. Další informace o odstranění skupiny prostředků najdete v tématu [odstranění prostředků a skupiny prostředků](../../azure-resource-manager/management/delete-resource-group.md).

> [!WARNING]
> Odstraněný účet úložiště není možné obnovit ani není možné načíst žádný obsah, který byl součástí účtu před jeho odstraněním. Nezapomeňte si před odstraněním účtu zazálohovat všechno, co chcete uložit. To platí také pro všechny prostředky v rámci účtu – po odstranění jsou objekt blob, tabulka, fronta nebo soubor odstraněny trvale.
>
> Pokud se pokusíte odstranit účet úložiště, který je přidružený virtuálnímu počítači Azure, můžete obdržet chybu, že se účet úložiště stále používá. Pomoc při řešení této chyby najdete v tématu [řešení chyb při odstraňování účtů úložiště](../../virtual-machines/troubleshooting/index.yml).

## <a name="next-steps"></a>Další kroky

- [Přehled účtu úložiště](storage-account-overview.md)
- [Upgrade na účet úložiště pro obecné účely verze 2](storage-account-upgrade.md)
- [Přesunutí účtu Azure Storage do jiné oblasti](storage-account-move.md)
- [Obnovení odstraněného účtu úložiště](storage-account-recover.md)