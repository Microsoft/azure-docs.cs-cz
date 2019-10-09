---
title: Kurz – použití souboru parametrů pro usnadnění nasazení šablony Azure Resource Manager
description: Použijte soubory parametrů, které obsahují hodnoty, které se mají použít k nasazení šablony Azure Resource Manager.
services: azure-resource-manager
author: mumian
manager: carmonmills
ms.service: azure-resource-manager
ms.date: 10/04/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: fa29ea3d2f6edbbb016ce5c0c74415a5e765e85a
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2019
ms.locfileid: "72177552"
---
# <a name="tutorial-use-parameter-files-to-deploy-your-resource-manager-template"></a>Kurz: použití souborů parametrů k nasazení šablony Správce prostředků

V tomto kurzu se naučíte používat [soubory parametrů](resource-manager-parameter-files.md) k uložení hodnot, které předáte během nasazování. V předchozích kurzech jste v příkazu nasazení použili vložené parametry. Tento přístup pracoval při testování šablony, ale když je automatizace nasazení snazší, předejte sadu hodnot pro vaše prostředí. Soubory parametrů usnadňují zabalení hodnot parametrů pro konkrétní prostředí. V tomto kurzu vytvoříte soubory parametrů pro vývojová a produkční prostředí. Dokončení trvá přibližně **12 minut** .

## <a name="prerequisites"></a>Požadavky

Doporučujeme, abyste dokončili [kurz týkající se značek](template-tutorial-add-tags.md), ale není to nutné.

Musíte mít Visual Studio Code s rozšířením Správce prostředků Tools a buď Azure PowerShell, nebo v rozhraní příkazového řádku Azure. Další informace najdete v tématu [nástroje šablon](template-tutorial-create-first-template.md#get-tools).

## <a name="review-your-template"></a>Kontrola šablony

Vaše šablona má mnoho parametrů, které můžete během nasazení zadat. Na konci předchozího kurzu vaše šablona vypadala takto:

[!code-json[](~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.json)]

Tato šablona funguje dobře, ale nyní chcete snadno spravovat parametry, které předáváte pro šablonu.

## <a name="add-parameter-files"></a>Přidat soubory parametrů

Soubory parametrů jsou soubory JSON se strukturou, která je podobná vaší šabloně. V souboru zadejte hodnoty parametrů, které chcete předat během nasazování.

V VS Code vytvořte nový soubor s následujícím obsahem. Uložte soubor s názvem **azuredeploy. Parameters. dev. JSON**.

[!code-json[](~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.parameters.dev.json)]

Tento soubor je vaším souborem parametrů pro vývojové prostředí. Všimněte si, že pro účet úložiště používá Standard_LRS, pojmenuje prostředky s předponou pro **vývoj** a nastaví značku **prostředí** na **dev**.

Znovu vytvořte nový soubor s následujícím obsahem. Uložte soubor s názvem **azuredeploy. Parameters. prod. JSON**.

[!code-json[](~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.parameters.prod.json)]

Tento soubor je vaším souborem parametrů pro produkční prostředí. Všimněte si, že pro účet úložiště používá Standard_GRS, názvy prostředků s předponou **Contoso** a nastaví značku **prostředí** na **produkční**. V reálném produkčním prostředí byste také chtěli použít službu App Service s jinou skladovou jednotkou než bezplatnou, ale pro tento kurz bude tato SKU nadále používat.

## <a name="deploy-the-template"></a>Nasazení šablony

K nasazení šablony použijte rozhraní příkazového řádku Azure nebo Azure PowerShell.

Jako konečný test šablony vytvoříme dvě nové skupiny prostředků. Jednu pro vývojové prostředí a jednu pro produkční prostředí.

Nejdřív nasadíme do vývojového prostředí.

# <a name="powershelltabazure-powershell"></a>[Prostředí](#tab/azure-powershell)

```azurepowershell
$templateFile = "{provide-the-path-to-the-template-file}"
$parameterFile="{path-to-azuredeploy.parameters.dev.json}"
New-AzResourceGroup `
  -Name myResourceGroupDev `
  -Location "East US"
New-AzResourceGroupDeployment `
  -Name devenvironment `
  -ResourceGroupName myResourceGroupDev `
  -TemplateFile $templateFile `
  -TemplateParameterFile $parameterFile
```

# <a name="azure-clitabazure-cli"></a>[Rozhraní příkazového řádku Azure](#tab/azure-cli)

```azurecli
templateFile="{provide-the-path-to-the-template-file}"
az group create \
  --name myResourceGroupDev \
  --location "East US"
az group deployment create \
  --name devenvironment \
  --resource-group myResourceGroupDev \
  --template-file $templateFile \
  --parameters azuredeploy.parameters.dev.json
```

---

Nyní nasadíme do produkčního prostředí.

# <a name="powershelltabazure-powershell"></a>[Prostředí](#tab/azure-powershell)

```azurepowershell
$parameterFile="{path-to-azuredeploy.parameters.prod.json}"
New-AzResourceGroup `
  -Name myResourceGroupProd `
  -Location "West US"
New-AzResourceGroupDeployment `
  -Name prodenvironment `
  -ResourceGroupName myResourceGroupProd `
  -TemplateFile $templateFile `
  -TemplateParameterFile $parameterFile
```

# <a name="azure-clitabazure-cli"></a>[Rozhraní příkazového řádku Azure](#tab/azure-cli)

```azurecli
az group create \
  --name myResourceGroupProd \
  --location "West US"
az group deployment create \
  --name prodenvironment \
  --resource-group myResourceGroupProd \
  --template-file $templateFile \
  --parameters azuredeploy.parameters.prod.json
```

---

## <a name="verify-the-deployment"></a>Ověření nasazení

Nasazení můžete ověřit prozkoumáním skupin prostředků z Azure Portal.

1. Přihlaste se k [Azure Portal](https://portal.azure.com).
1. V nabídce vlevo vyberte **skupiny prostředků**.
1. V tomto kurzu se zobrazí dvě nové skupiny prostředků, které jste nasadili.
1. Vyberte buď skupinu prostředků, a zobrazte nasazené prostředky. Všimněte si, že odpovídají hodnotám, které jste zadali v souboru parametrů pro toto prostředí.

## <a name="clean-up-resources"></a>Vyčištění prostředků

1. Z Azure Portal v nabídce vlevo vyberte **Skupina prostředků** .
2. Do pole **filtrovat podle názvu** zadejte název skupiny prostředků. Pokud jste tuto řadu dokončili, máte tři skupiny prostředků pro odstranění – myResourceGroup, myResourceGroupDev a myResourceGroupProd.
3. Vyberte název skupiny prostředků.
4. V horní nabídce vyberte **Odstranit skupinu prostředků** .

## <a name="next-steps"></a>Další kroky

Gratulujeme, dokončili jste tento Úvod k nasazování šablon do Azure. Dejte nám vědět, pokud máte komentáře a návrhy v části věnované zpětné vazbě. Dík!

Jste připraveni přejít k pokročilejším koncepcím o šablonách. V dalším kurzu se dozvíte víc o používání Referenční dokumentace k šablonám, které vám pomůžou definovat prostředky k nasazení.

> [!div class="nextstepaction"]
> [Využít odkaz na šablonu](resource-manager-tutorial-create-encrypted-storage-accounts.md)
