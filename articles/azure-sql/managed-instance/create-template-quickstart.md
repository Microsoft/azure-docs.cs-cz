---
title: 'Azure Resource Manager: vytvoření spravované instance Azure SQL'
description: Naučte se vytvořit spravovanou instanci Azure SQL pomocí šablony Azure Resource Manager.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: subject-armqs
ms.devlang: ''
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 06/22/2020
ms.openlocfilehash: badeb850e8d0346347a994f053fb0f64fd01240a
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "91283336"
---
# <a name="quickstart-create-an-azure-sql-managed-instance-using-an-arm-template"></a>Rychlý Start: vytvoření spravované instance Azure SQL pomocí šablony ARM

Tento rychlý Start se zaměřuje na proces nasazení šablony Azure Resource Manager (šablona ARM) pro vytvoření spravované instance Azure SQL a virtuální sítě. [Spravovaná instance Azure SQL](sql-managed-instance-paas-overview.md) je inteligentní, plně spravovaná, Škálovatelná cloudová databáze s téměř 100% paritou funkcí s databázovým strojem SQL Server.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Pokud vaše prostředí splňuje požadavky a jste obeznámeni s používáním šablon ARM, vyberte tlačítko **Nasazení do Azure**. Šablona se otevře v prostředí Azure Portal.

[![Nasazení do Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-sqlmi-new-vnet%2Fazuredeploy.json)

## <a name="prerequisites"></a>Požadavky

Pokud nemáte předplatné Azure, [Vytvořte si bezplatný účet](https://azure.microsoft.com/free/).

## <a name="review-the-template"></a>Kontrola šablony

Šablona použitá v tomto rychlém startu je jednou z [šablon pro rychlý start Azure](https://azure.microsoft.com/resources/templates/101-sqlmi-new-vnet/).

:::code language="json" source="~/quickstart-templates/101-sqlmi-new-vnet/azuredeploy.json":::

Tyto prostředky jsou definované v šabloně:

- [**Microsoft. Network/networkSecurityGroups**](/azure/templates/microsoft.Network/networkSecurityGroups)
- [**Microsoft. Network/routeTables**](/azure/templates/microsoft.Network/routeTables)
- [**Microsoft. Network/virtualNetworks**](/azure/templates/microsoft.Network/virtualNetworks)
- [**Microsoft. SQL/managedinstances**](/azure/templates/microsoft.sql/managedinstances)

Další ukázky šablon najdete v [šablonách rychlý Start pro Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Sql&pageNumber=1&sort=Popular).

## <a name="deploy-the-template"></a>Nasazení šablony

Vyberte příkaz **vyzkoušet** z následujícího bloku kódu prostředí PowerShell a otevřete Azure Cloud Shell.

> [!IMPORTANT]
> Nasazení spravované instance je dlouhodobě běžící operace. Nasazení první instance v podsíti obvykle trvá mnohem déle než nasazení do podsítě se stávajícími spravovanými instancemi. Průměrné doby zřizování najdete v tématu [operace správy spravované instance SQL](sql-managed-instance-paas-overview.md#management-operations).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-sqlmi-new-vnet/azuredeploy.json"

$resourceGroupName = "${projectName}rg"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri

Read-Host -Prompt "Press [ENTER] to continue ..."
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
read -p "Enter a project name that is used for generating resource names:" projectName &&
read -p "Enter the location (i.e. centralus):" location &&
templateUri="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-sqlmi-new-vnet/azuredeploy.json" &&
resourceGroupName="${projectName}rg" &&
az group create --name $resourceGroupName --location "$location" &&
az deployment group create --resource-group $resourceGroupName --template-uri  $templateUri &&
echo "Press [ENTER] to continue ..." &&
read
```

---

## <a name="review-deployed-resources"></a>Kontrola nasazených prostředků

Navštivte [Azure Portal](https://portal.azure.com/#blade/HubsExtension/BrowseResourceGroups) a ověřte, že je spravovaná instance ve vaší vybrané skupině prostředků. Vzhledem k tomu, že vytváření spravované instance může nějakou dobu trvat, možná budete muset zaškrtnout odkaz **nasazení** na stránce **Přehled** vaší skupiny prostředků.

- Rychlý Start, který ukazuje, jak se připojit ke spravované instanci SQL z virtuálního počítače Azure, najdete v tématu [Konfigurace připojení k virtuálnímu počítači Azure](connect-vm-instance-configure.md).
- Rychlý Start, který ukazuje, jak se připojit ke spravované instanci SQL z místního klientského počítače pomocí připojení typu Point-to-site, najdete v tématu [Konfigurace připojení typu Point-to-site](point-to-site-p2s-configure.md).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete přejít k [dalším krokům](#next-steps), můžete zachovat spravovanou instanci, ale po dokončení dalších kurzů odstraňte spravovanou instanci a související prostředky. Po odstranění spravované instance najdete informace v tématu [odstranění podsítě po odstranění spravované instance](virtual-cluster-delete.md).


Odstranění skupiny prostředků:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName
```

---

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Konfigurace virtuálního počítače Azure pro připojení ke spravované instanci Azure SQL](connect-vm-instance-configure.md)
