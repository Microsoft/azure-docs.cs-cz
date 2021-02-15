---
title: Kurz – nasazení místní šablony Azure Resource Manager
description: Naučte se nasadit šablonu Azure Resource Manager (šablonu ARM) z místního počítače.
ms.date: 02/10/2021
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: d8d54acfa345994edcc401170e70495b3826bfdf
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2021
ms.locfileid: "100384227"
---
# <a name="tutorial-deploy-a-local-arm-template"></a>Kurz: nasazení místní šablony ARM

Přečtěte si, jak nasadit šablonu Azure Resource Manager (šablonu ARM) z místního počítače. Dokončení trvá přibližně **8 minut** .

Tento kurz je první z řad. Jak budete postupovat prostřednictvím řady, naplánovat modularizaci šablonu vytvořením propojené šablony, uložíte propojenou šablonu v účtu úložiště a nakonfigurujete propojenou šablonu pomocí tokenu SAS a zjistíte, jak vytvořit kanál DevOps pro nasazení šablon. Tato série se zaměřuje na nasazení šablony. Pokud se chcete seznámit s vývojem šablon, přečtěte si o [začátečníkch kurzech](./template-tutorial-create-first-template.md).

## <a name="get-tools"></a>Získat nástroje

Pojďme začít tím, že si zajistěte, aby byly k dispozici nástroje, které potřebujete k nasazení šablon.

### <a name="command-line-deployment"></a>Nasazení z příkazového řádku

K nasazení šablony potřebujete buď Azure PowerShell, nebo rozhraní příkazového řádku Azure. Pokyny k instalaci najdete v těchto tématech:

- [Instalace Azure PowerShellu](/powershell/azure/install-az-ps)
- [Instalace Azure CLI ve Windows](/cli/azure/install-azure-cli-windows)
- [Instalace rozhraní příkazového řádku Azure CLI v systému Linux](/cli/azure/install-azure-cli-linux)
- [Instalace Azure CLI v macOS](/cli/azure/install-azure-cli-macos)

Po instalaci Azure PowerShell nebo rozhraní příkazového řádku Azure se ujistěte, že poprvé se přihlašujete. Nápovědu najdete v tématu věnovaném [přihlášení k prostředí PowerShell](/powershell/azure/install-az-ps#sign-in) nebo [přihlášení – Azure CLI](/cli/azure/get-started-with-azure-cli#sign-in).

### <a name="editor-optional"></a>Editor (volitelné)

Šablony jsou soubory JSON. Chcete-li zkontrolovat nebo upravit šablony, potřebujete dobrý Editor JSON. Doporučujeme Visual Studio Code s rozšířením nástroje Správce prostředků Tools. Pokud potřebujete nainstalovat tyto nástroje, přečtěte si téma [rychlý Start: vytvoření šablon ARM pomocí Visual Studio Code](quickstart-create-templates-use-visual-studio-code.md).

## <a name="review-template"></a>Zkontrolovat šablonu

Šablona nasadí účet úložiště, plán služby App Service a webovou aplikaci. Pokud vás zajímá vytváření šablony, můžete si projít [kurzem o šablonách pro rychlý Start](template-tutorial-quickstart-template.md). Pro dokončení tohoto kurzu ale není to nutné.

:::code language="json" source="~/resourcemanager-templates/get-started-deployment/local-template/azuredeploy.json":::

> [!IMPORTANT]
> Názvy účtů úložiště musí mít délku 3 až 24 znaků a musí obsahovat jenom číslice a malá písmena. Název musí být jedinečný. V šabloně je název účtu úložiště název projektu s připojeným **úložištěm** a název projektu musí být dlouhý 3 až 11 znaků. Proto název projektu musí splňovat požadavky na název účtu úložiště a má méně než 11 znaků.

Uložte kopii šablony do místního počítače s příponou _. JSON_ , například _azuredeploy.jsv_. Tuto šablonu nasadíte později v tomto kurzu.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Pokud chcete začít pracovat s Azure PowerShell/Azure CLI pro nasazení šablony, přihlaste se pomocí přihlašovacích údajů Azure.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Connect-AzAccount
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az login
```

---

Pokud máte více předplatných Azure, vyberte předplatné, které chcete použít. Nahraďte `[SubscriptionID/SubscriptionName]` a hranaté závorky `[]` informace o vašem předplatném:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Set-AzContext [SubscriptionID/SubscriptionName]
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az account set --subscription [SubscriptionID/SubscriptionName]
```

---

## <a name="create-resource-group"></a>Vytvoření skupiny prostředků

Když nasadíte šablonu, zadáte skupinu prostředků, která bude obsahovat prostředky. Před spuštěním příkazu k nasazení vytvořte skupinu prostředků. Použijte k tomu Azure CLI nebo Azure PowerShell. Vyberte karty v následující části kódu a vyberte si mezi Azure PowerShell a Azure CLI. Příklady rozhraní příkazového řádku v tomto článku se napíší pro prostředí bash.

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

Další informace o nasazování šablony pomocí Azure PowerShell najdete v tématu [nasazení prostředků pomocí šablon ARM a Azure PowerShell](./deploy-powershell.md).

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

Další informace o nasazení šablony pomocí rozhraní příkazového řádku Azure najdete v tématu [nasazení prostředků pomocí šablon ARM a Azure CLI](./deploy-cli.md).

---

## <a name="clean-up-resources"></a>Vyčištění prostředků

Vyčistěte prostředky, které jste nasadili, odstraněním skupiny prostředků.

1. Z Azure Portal v nabídce vlevo vyberte **Skupina prostředků** .
2. Do pole **Filtrovat podle názvu** zadejte název skupiny prostředků.
3. Vyberte název skupiny prostředků.
4. V horní nabídce vyberte **Odstranit skupinu prostředků** .

## <a name="next-steps"></a>Další kroky

Zjistili jste, jak nasadit místní šablonu. V dalším kurzu oddělíte šablonu do hlavní šablony a propojené šablony a naučíte se ukládat a zabezpečovat propojenou šablonu.

> [!div class="nextstepaction"]
> [Nasazení propojené šablony](./deployment-tutorial-linked-template.md)
