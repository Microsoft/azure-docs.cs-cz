---
title: Vytvoření virtuálního počítače s SQL Server pomocí šablony ARM
description: Naučte se, jak vytvořit SQL Server na virtuálním počítači Azure pomocí šablony Azure Resource Manager (šablona ARM).
author: MashaMSFT
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: mathoma
ms.date: 06/29/2020
ms.service: virtual-machines-sql
ms.openlocfilehash: 21909a9c7a1edda5059b9e83cf8dda243862b966
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "88660203"
---
# <a name="quickstart-create-sql-server-vm-using-an-arm-template"></a>Rychlý Start: Vytvoření virtuálního počítače s SQL Server pomocí šablony ARM

Pomocí této šablony Azure Resource Manager (šablony ARM) můžete nasadit SQL Server na virtuálním počítači Azure (VM). 

[!INCLUDE [About Azure Resource Manager](../../../../includes/resource-manager-quickstart-introduction.md)]

Pokud vaše prostředí splňuje požadavky a jste obeznámeni s používáním šablon ARM, vyberte tlačítko **Nasazení do Azure**. Šablona se otevře v prostředí Azure Portal.

[![Nasazení do Azure](../../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-sql-vm-new-storage%2fazuredeploy.json)

## <a name="prerequisites"></a>Požadavky

Šablona nástroje SQL Server VM ARM vyžaduje následující:

- Nejnovější verzi rozhraní příkazového [řádku Azure CLI](/cli/azure/install-azure-cli) nebo [PowerShellu](/powershell/scripting/install/installing-powershell). 
- Předem nakonfigurovaná [Skupina prostředků](../../../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups) s připravenou [virtuální sítí](../../../virtual-network/quick-create-portal.md) a [podsítí](../../../virtual-network/virtual-network-manage-subnet.md#add-a-subnet).
- Předplatné Azure. Pokud ho nemáte, než začnete, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).


## <a name="review-the-template"></a>Kontrola šablony

Šablona použitá v tomto rychlém startu je jednou z [šablon pro rychlý start Azure](https://azure.microsoft.com/resources/templates/101-sql-vm-new-storage/).

:::code language="json" source="~/quickstart-templates/101-sql-vm-new-storage/azuredeploy.json":::

V šabloně je definováno pět prostředků Azure: 

- [Microsoft. Network/publicIpAddresses](/azure/templates/microsoft.network/publicipaddresses): Vytvoří veřejnou IP adresu. 
- [Microsoft. Network/networkSecurityGroups](/azure/templates/microsoft.network/networksecuritygroups): vytvoří skupinu zabezpečení sítě. 
- [Microsoft. Network/networkInterfaces](/azure/templates/microsoft.network/networkinterfaces): nakonfiguruje síťové rozhraní. 
- [Microsoft. COMPUTE/virtualMachines](/azure/templates/microsoft.compute/virtualmachines): vytvoří virtuální počítač v Azure. 
- [Microsoft. SqlVirtualMachine/SqlVirtualMachines](/azure/templates/microsoft.sqlvirtualmachine/sqlvirtualmachines): zaregistruje virtuální počítač u poskytovatele prostředků virtuálního počítače SQL. 

Další SQL Server v šablonách virtuálních počítačů Azure najdete v [galerii šablon rychlý Start](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Sqlvirtualmachine&pageNumber=1&sort=Popular).


## <a name="deploy-the-template"></a>Nasazení šablony

1. Vyberte následující obrázek a přihlaste se k Azure a otevřete šablonu. Šablona vytvoří virtuální počítač, na kterém je nainstalovaná zamýšlená verze SQL Server, a zaregistrované u poskytovatele prostředků virtuálního počítače SQL. 

   [![Nasazení do Azure](../../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-sql-vm-new-storage%2fazuredeploy.json)

2. Vyberte nebo zadejte následující hodnoty.

    * **Předplatné**: vyberte předplatné Azure.
    * **Skupina prostředků**: Skupina prostředků připravená pro váš virtuální počítač SQL Server. 
    * **Oblast**: Vyberte oblast.  Například **USA – střed**.
    * **Název virtuálního počítače**: zadejte název pro SQL Server virtuální počítač. 
    * **Velikost virtuálního počítače**: v rozevíracím seznamu vyberte odpovídající velikost pro virtuální počítač.
    * **Existující Virtual Network název**: zadejte název připravené virtuální sítě pro váš virtuální počítač s SQL Server. 
    * **Existující skupina prostředků virtuální**sítě: zadejte skupinu prostředků, ve které byla vaše virtuální síť připravena. 
    * **Název existující podsítě**: název připravené podsítě. 
    * **Nabídka Image**: vyberte SQL Server a image Windows serveru, které nejlépe vyhovují vašim obchodním potřebám. 
    * **SKU SQL**: vyberte edici SQL Server SKU, která nejlépe vyhovuje vašim obchodním potřebám. 
    * **Uživatelské jméno správce**: uživatelské jméno pro správce virtuálního počítače. 
    * **Heslo správce**: heslo používané účtem správce virtuálních počítačů. 
    * **Typ úlohy úložiště**: typ úložiště pro zatížení, které nejlépe odpovídá vašemu podniku. 
    * **Počet datových disků SQL**: počet disků, které SQL Server používá pro datové soubory.  
    * **Cesta k datům**: cesta k datovým souborům SQL Server. 
    * **Počet disků protokolu SQL**: počet disků, které SQL Server používá pro soubory protokolu. 
    * **Cesta protokolu**: cesta pro soubory protokolu SQL Server. 
    * **Umístění**: umístění všech prostředků, tato hodnota by měla zůstat ve výchozím nastavení `[resourceGroup().location]` . 

3. Vyberte **Zkontrolovat a vytvořit**. Po úspěšném nasazení SQL Server virtuálního počítače se zobrazí oznámení.

K nasazení šablony se použije Azure Portal. Kromě Azure Portal můžete použít také Azure PowerShell, rozhraní příkazového řádku Azure a REST API. Další informace o dalších metodách nasazení najdete v tématu [Nasazení šablon](../../../azure-resource-manager/templates/deploy-powershell.md).

## <a name="review-deployed-resources"></a>Kontrola nasazených prostředků

K ověření nasazených prostředků můžete použít rozhraní příkazového řádku Azure. 


```azurecli-interactive
echo "Enter the resource group where your SQL Server VM exists:" &&
read resourcegroupName &&
az resource list --resource-group $resourcegroupName 
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, odstraňte skupinu prostředků pomocí Azure CLI nebo Azure PowerShell:

# <a name="cli"></a>[Rozhraní příkazového řádku](#tab/CLI)

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

---

## <a name="next-steps"></a>Další kroky

Podrobný kurz, který vás provede procesem vytvoření šablony, najdete v těchto tématech:

> [!div class="nextstepaction"]
> [ Kurz: vytvoření a nasazení první šablony ARM](/azure/azure-resource-manager/templates/template-tutorial-create-first-template)

Další způsoby nasazení SQL Server virtuálního počítače najdete v těchto tématech: 
- [Azure Portal](create-sql-vm-portal.md)
- [PowerShell](create-sql-vm-powershell.md)

Další informace najdete v tématu [přehled SQL Server na virtuálních počítačích Azure](sql-server-on-azure-vm-iaas-what-is-overview.md).
