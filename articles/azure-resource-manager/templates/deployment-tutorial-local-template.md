---
title: Kurz – nasazení místní šablony Správce prostředků Azure
description: Zjistěte, jak nasadit šablonu Azure Resource Manageru z místního počítače.
ms.date: 03/13/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: c8e3eb62fa52caeaa63808b6b9ea199bdff5c4da
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "80082255"
---
# <a name="tutorial-deploy-a-local-azure-resource-manager-template"></a>Kurz: Nasazení místní šablony Azure Resource Manager

Zjistěte, jak nasadit šablonu Azure Resource Manager u místního počítače. To trvá asi **8 minut** na dokončení.

Tento kurz je první ze série. Jak postupujete přes řadu, můžete modularizovat šablonu vytvořením propojené šablony, uložíte propojenou šablonu do účtu úložiště a zabezpečíte propojenou šablonu pomocí tokenu SAS a dozvíte se, jak vytvořit kanál DevOp pro nasazení šablon. Tato řada se zaměřuje na nasazení šablony.  Pokud se chcete naučit vývoj šablon, podívejte se na [začátečníky tutoriály](./template-tutorial-create-first-template.md).

## <a name="get-tools"></a>Získejte nástroje

Začněme tím, že se ujistíte, že máte nástroje, které potřebujete k nasazení šablon.

### <a name="command-line-deployment"></a>Nasazení příkazového řádku

K nasazení šablony potřebujete buď Azure PowerShell nebo Azure CLI. Pokyny k instalaci naleznete v následujících tématech:

- [Instalace prostředí Azure PowerShell](/powershell/azure/install-az-ps)
- [Instalace Azure CLI ve Windows](/cli/azure/install-azure-cli-windows)
- [Instalace Azure CLI na Linux](/cli/azure/install-azure-cli-linux)

Po instalaci Azure PowerShellnebo Azure CLI, ujistěte se, že přihlášení poprvé. Nápovědu najdete [v tématu Přihlášení – PowerShell](/powershell/azure/install-az-ps#sign-in) nebo [Přihlášení – Azure CLI](/cli/azure/get-started-with-azure-cli#sign-in).

### <a name="editor-optional"></a>Editor (volitelné)

Šablony jsou soubory JSON. Chcete-li zkontrolovat / upravit šablony, potřebujete dobrý editor JSON. Doporučujeme Visual Studio kód s rozšířenínástroje Správce prostředků. Pokud potřebujete nainstalovat tyto nástroje, najdete [v tématu použití kódu Visual Studia k vytvoření šablon Azure Resource Manageru](use-vs-code-to-create-template.md).

## <a name="review-template"></a>Šablona revize

Šablona použitá v tomto kurzu je podobná šabloně použité v [kurzu o šablonách rychlého startu](template-tutorial-quickstart-template.md). Máte-li zájem o vytvoření šablony, můžete projít, že výukový program. Pro dokončení tohoto kurzu však není vyžadována.

Šablona nasazuje účet úložiště, plán služby App Service a webovou aplikaci.

:::code language="json" source="~/resourcemanager-templates/get-started-deployment/local-template/azuredeploy.json":::

> [!IMPORTANT]
> Názvy účtů úložiště musí mít délku 3 až 24 znaků a používat pouze čísla a malá písmena. Název musí být jedinečný. V šabloně je název účtu úložiště název projektu s připojeným "store" a název projektu musí být mezi 3 a 11 znaků. Název projektu tedy musí splňovat požadavky na název účtu úložiště a má méně než 11 znaků.

Uložte kopii šablony do místního počítače s příponou JSON, například azuredeploy.json. Tuto šablonu nasadíte později v kurzu.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Pokud chcete začít pracovat s Azure PowerShell/Azure CLI k nasazení šablony, přihlaste se pomocí přihlašovacích údajů Azure.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Connect-AzAccount
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az login
```

---

Pokud máte více předplatných Azure, vyberte předplatné, které chcete použít:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Select-AzSubscription [SubscriptionID/SubscriptionName]
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az account set --subscription [SubscriptionID/SubscriptionName]
```

---

## <a name="create-resource-group"></a>Vytvoření skupiny prostředků

Při nasazení šablony zadáte skupinu prostředků, která bude obsahovat prostředky. Před spuštěním příkazu nasazení vytvořte skupinu prostředků pomocí azure cli nebo Azure PowerShellu. Vyberte karty v následující části kódu a vyberte si mezi Azure PowerShellem a Azure CLI. Příklady CLI v tomto článku jsou napsány pro prostředí Bash.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$projectName = Read-Host -Prompt "Enter a project name that is used to generate resource and resource group names"
$resourceGroupName = "${projectName}rg"

New-AzResourceGroup `
  -Name $resourceGroupName `
  -Location "Central US"
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
echo "Enter a project name that is used to generate resource and resource group names:"
read projectName
resourceGroupName="${projectName}rg"

az group create \
  --name $resourceGroupName \
  --location "Central US"
```

---

## <a name="deploy-template"></a>Nasazení šablony

K nasazení šablony použijte jednu nebo obě možnosti nasazení.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$projectName = Read-Host -Prompt "Enter the same project name"
$templateFile = Read-Host -Prompt "Enter the template file path and file name"
$resourceGroupName = "${projectName}rg"

New-AzResourceGroupDeployment `
  -Name DeployLocalTemplate `
  -ResourceGroupName $resourceGroupName `
  -TemplateFile $templateFile `
  -projectName $projectName `
  -verbose
```

Další informace o nasazení šablony pomocí Azure PowerShellu najdete v [tématu Nasazení prostředků pomocí šablon Správce prostředků a Azure PowerShellu](./deploy-powershell.md).

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
echo "Enter the same project name:"
read projectName
echo "Enter the template file path and file name:"
read templateFile
resourceGroupName="${projectName}rg"

az deployment group create \
  --name DeployLocalTemplate \
  --resource-group $resourceGroupName \
  --template-file $templateFile \
  --parameters projectName=$projectName \
  --verbose
```

Další informace o nasazení šablony pomocí azure cli najdete v [tématu nasazení prostředků pomocí šablon Správce prostředků a Azure CLI](./deploy-cli.md).

---

## <a name="clean-up-resources"></a>Vyčištění prostředků

Vyčistěte prostředky, které jste nasadili odstraněním skupiny prostředků.

1. Na portálu Azure vyberte **skupinu prostředků** z levé nabídky.
2. Do pole **Filtrovat podle názvu** zadejte název skupiny prostředků.
3. Vyberte název skupiny prostředků.
4. V horní nabídce vyberte **Odstranit skupinu prostředků.**

## <a name="next-steps"></a>Další kroky

Naučili jste se nasadit místní šablonu. V dalším kurzu oddělíte šablonu do hlavní šablony a propojené šablony a dozvíte se, jak uložit a zabezpečit propojenou šablonu.

> [!div class="nextstepaction"]
> [Nasazení propojené šablony](./deployment-tutorial-linked-template.md)
