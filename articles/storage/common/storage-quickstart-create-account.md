---
title: Vytvořit účet úložiště – Azure Storage
description: V tomto článku s postupy zjistíte, jak vytvořit účet úložiště pomocí webu Azure portal, prostředí Azure PowerShell nebo rozhraní příkazového řádku Azure. Účet úložiště Azure poskytuje v Microsoft Azure jedinečný obor názvů pro ukládání a přístup k datovým objektům, které vytvoříte ve službě Azure Storage.
services: storage
author: tamram
ms.custom: mvc
ms.service: storage
ms.topic: article
ms.date: 05/06/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 8375f4c54dc436ecf0694ec5f629c81d3591594d
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2019
ms.locfileid: "65234178"
---
# <a name="create-a-storage-account"></a>vytvořit účet úložiště

Účet úložiště Azure obsahuje všechny datových objektů Azure Storage: objekty BLOB, soubory, fronty, tabulky a disky. Účet úložiště poskytuje jedinečný obor názvů pro vaše data služby Azure Storage, která je přístupná odkudkoli na světě prostřednictvím protokolu HTTP nebo HTTPS. Data ve vašem účtu úložiště Azure je odolné a vysoce dostupné, zabezpečené a široce škálovatelné.

V tomto článku s postupy, zjistíte, jak vytvořit účet úložiště pomocí [webu Azure portal](https://portal.azure.com/), [prostředí Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview), [rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure?view=azure-cli-latest), nebo [Azure Resource Manageru Šablona](../../azure-resource-manager/resource-group-overview.md).  

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Požadavky

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

# <a name="portaltabazure-portal"></a>[Azure Portal](#tab/azure-portal)

Žádné

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Tento článek vyžaduje modul Azure PowerShell Az verze 0.7 nebo novější. Aktuální verzi zjistíte spuštěním `Get-Module -ListAvailable Az`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-Az-ps).

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

Přihlásit do Azure a spouštět příkazy rozhraní příkazového řádku Azure v jednom ze dvou způsobů:

- Ve službě Azure Cloud Shell můžete spouštět příkazy rozhraní příkazového řádku z webu Azure portal.
- Můžete nainstalovat rozhraní příkazového řádku a spouštění místních příkazů rozhraní příkazového řádku.

### <a name="use-azure-cloud-shell"></a>Použití služby Azure Cloud Shell

Azure Cloud Shell je volně dostupné prostředí Bash, které můžete spustit přímo z webu Azure Portal. Rozhraní příkazového řádku Azure je předem nainstalovaný a nakonfigurovaný pro použití s vaším účtem. Klikněte na tlačítko **Cloud Shell** tlačítko v nabídce v pravé horní části webu Azure Portal:

[![Cloud Shell](./media/storage-quickstart-create-account/cloud-shell-menu.png)](https://portal.azure.com)

Toto tlačítko spustí interaktivní prostředí, můžete použít k provedení kroků uvedených v tomto článku s postupy:

[![Snímek obrazovky okna služby Cloud Shell na portálu](./media/storage-quickstart-create-account/cloud-shell.png)](https://portal.azure.com)

### <a name="install-the-cli-locally"></a>Místní instalace rozhraní příkazového řádku

Azure CLI můžete také nainstalovat a používat místně. Tento článek vyžaduje použití Azure CLI verze 2.0.4 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli). 

# <a name="templatetabtemplate"></a>[Šablona](#tab/template)

Žádné

---

## <a name="sign-in-to-azure"></a>Přihlásit se k Azure

# <a name="portaltabazure-portal"></a>[Azure Portal](#tab/azure-portal)

Přihlaste se k webu [Azure Portal](https://portal.azure.com).

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Přihlaste se k předplatnému Azure pomocí `Connect-AzAccount` příkaz a postupujte podle pokynů na obrazovce pokynů k ověření.

```powershell
Connect-AzAccount
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

Pokud chcete spustit Azure Cloud Shell, přihlaste se k [webu Azure portal](https://portal.azure.com).

Přihlaste se k místní instalaci rozhraní příkazového řádku, spusťte [az login](/cli/azure/reference-index#az-login) příkaz:

```cli
az login
```

# <a name="templatetabtemplate"></a>[Šablona](#tab/template)

neuvedeno

---

## <a name="create-a-storage-account"></a>vytvořit účet úložiště

Teď jste připraveni k vytvoření účtu úložiště.

Každý účet úložiště musí patřit do nějaké skupiny prostředků Azure. Skupina prostředků je logický kontejner pro seskupení služeb Azure. Při vytváření účtu úložiště máte možnost buď vytvořit novou skupinu prostředků, nebo použít některou existující skupinu prostředků. Tento článek ukazuje, jak vytvořit novou skupinu prostředků.

Účet úložiště **Univerzální v2** poskytuje přístup ke všem službám Azure Storage: objektům blob, souborům, frontám, tabulkám a diskům. Níže uvedené kroky vytvoření účtu úložiště pro obecné účely v2, ale jsou podobné kroky k vytvoření libovolného typu účtu úložiště.

# <a name="portaltabazure-portal"></a>[Azure Portal](#tab/azure-portal)

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Nejprve vytvořte novou skupinu prostředků s použitím prostředí PowerShell [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) příkaz:

```powershell
# put resource group in a variable so you can use the same group name going forward,
# without hard-coding it repeatedly
$resourceGroup = "storage-resource-group"
New-AzResourceGroup -Name $resourceGroup -Location $location
```

Pokud si nejste jisti, jakou oblast pro `-Location` parametr, můžete načíst seznam podporovaných oblastí pro vaše předplatné se [Get-AzLocation](/powershell/module/az.resources/get-azlocation) příkaz:

```powershell
Get-AzLocation | select Location
$location = "westus"
```

Dále vytvořte účet úložiště pro obecné účely verze 2 s geograficky redundantní úložiště jen pro čtení (RA-GRS) pomocí [New-AzStorageAccount](/powershell/module/az.storage/New-azStorageAccount) příkazu. Mějte na paměti, že název účtu úložiště musí být jedinečný v rámci Azure, tak hodnotu zástupného symbolu v závorkách nahraďte vlastní jedinečnou hodnotu:

```powershell
New-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name <account-name> `
  -Location $location `
  -SkuName Standard_RAGRS `
  -Kind StorageV2 
```

Pokud chcete vytvořit účet úložiště pro obecné účely verze 2 s možností jiné replikační, nahraďte požadovanou hodnotu v následující tabulce **SkuName** parametru.

|Možnost replikace  |Parametr SkuName  |
|---------|---------|
|Místně redundantní úložiště (LRS)     |Standard_LRS         |
|Zónově redundantní úložiště (ZRS)     |Standard_ZRS         |
|Geograficky redundantní úložiště (GRS)     |Standard_GRS         |
|Geograficky redundantní úložiště s přístupem pro čtení (RA-GRS)     |Standard_RAGRS         |

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

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

Dále vytvořte účet úložiště pro obecné účely verze 2 s geograficky redundantní úložiště jen pro čtení pomocí [vytvořit účet úložiště az](/cli/azure/storage/account#az_storage_account_create) příkazu. Mějte na paměti, že název účtu úložiště musí být jedinečný v rámci Azure, tak hodnotu zástupného symbolu v závorkách nahraďte vlastní jedinečnou hodnotu:

```azurecli-interactive
az storage account create \
    --name <account-name> \
    --resource-group storage-resource-group \
    --location westus \
    --sku Standard_RAGRS \
    --kind StorageV2
```

Pokud chcete vytvořit účet úložiště pro obecné účely verze 2 s možností jiné replikační, nahraďte požadovanou hodnotu v následující tabulce **sku** parametru.

|Možnost replikace  |Parametr sku  |
|---------|---------|
|Místně redundantní úložiště (LRS)     |Standard_LRS         |
|Zónově redundantní úložiště (ZRS)     |Standard_ZRS         |
|Geograficky redundantní úložiště (GRS)     |Standard_GRS         |
|Geograficky redundantní úložiště s přístupem pro čtení (RA-GRS)     |Standard_RAGRS         |

# <a name="templatetabtemplate"></a>[Šablona](#tab/template)

Prostředí Azure Powershell nebo rozhraní příkazového řádku Azure můžete použít k nasazení šablony Resource Manageru k vytvoření účtu úložiště. Šablona použitá v tomto článku postup je z [šablon Azure Resource Manageru pro rychlý Start](https://azure.microsoft.com/resources/templates/101-storage-account-create/). Chcete-li spustit skripty, vyberte **vyzkoušet** a otevřete Azure Cloud shell. Vložte skript, klikněte pravým tlačítkem na prostředí a pak vyberte **vložte**.

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

Další informace o vytváření šablon najdete v tématu:

- [Dokumentace ke službě Azure Resource Manageru](/azure/azure-resource-manager/).
- [Odkaz na šablonu účtu úložiště](/azure/templates/microsoft.storage/allversions).
- [Ukázkové šablony účtu úložiště](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Storage).

---

Další informace o dostupných možnostech replikace najdete v tématu věnovaném [možnostem replikace služby Storage](storage-redundancy.md).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete vyčistit prostředky vytvořené v rámci tohoto článku s postupy, můžete odstranit skupinu prostředků. Odstraněním skupiny prostředků odstraníte také přidružený účet úložiště a všechny další prostředky, které jsou k příslušné skupině prostředků přidružené.

# <a name="portaltabazure-portal"></a>[Azure Portal](#tab/azure-portal)

Odebrání skupiny prostředků pomocí webu Azure Portal:

1. Na webu Azure Portal rozbalením nabídky na levé straně otevřete nabídku služeb a zvolte **Skupiny prostředků**. Zobrazí se seznam skupin prostředků.
2. Vyhledejte skupinu prostředků, kterou chcete odstranit, a klikněte pravým tlačítkem na tlačítko **Další** (**...** ) na pravé straně seznamu.
3. Vyberte **Odstranit skupinu prostředků** a potvrďte tuto akci.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Chcete-li odebrat skupinu prostředků a její přidružené prostředky, včetně nového účtu úložiště, použijte [odebrat AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) příkaz:

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

Pokud chcete odebrat skupinu prostředků a její přidružené prostředky, včetně nového účtu úložiště, použijte příkaz [az group delete](/cli/azure/group#az_group_delete).

```azurecli-interactive
az group delete --name storage-resource-group
```

# <a name="templatetabtemplate"></a>[Šablona](#tab/template)

Chcete-li odebrat skupinu prostředků a její přidružené prostředky, včetně nového účtu úložiště pomocí Azure Powershellu nebo rozhraní příkazového řádku Azure.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
```

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName
```

---

## <a name="next-steps"></a>Další postup

V tomto článku s postupy vytvořili jste účet úložiště úrovně standard pro obecné účely v2. Zjistěte, jak nahrávat a stahovat objekty BLOB z účtu úložiště, pokračujte jeden z úložiště objektů Blob, šablony rychlý start.

# <a name="portaltabazure-portal"></a>[Azure Portal](#tab/azure-portal)

> [!div class="nextstepaction"]
> [Práce s objekty blob pomocí webu Azure Portal](../blobs/storage-quickstart-blobs-portal.md)

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

> [!div class="nextstepaction"]
> [Práce s objekty blob pomocí PowerShellu](../blobs/storage-quickstart-blobs-powershell.md)

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

> [!div class="nextstepaction"]
> [Práce s objekty BLOB pomocí Azure CLI](../blobs/storage-quickstart-blobs-cli.md)

# <a name="templatetabtemplate"></a>[Šablona](#tab/template)

> [!div class="nextstepaction"]
> [Práce s objekty blob pomocí webu Azure Portal](../blobs/storage-quickstart-blobs-portal.md)

---
