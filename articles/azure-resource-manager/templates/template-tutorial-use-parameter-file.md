---
title: Kurz – použití souboru parametrů k nasazení šablony
description: Použijte soubory parametrů, které obsahují hodnoty, které se mají použít k nasazení šablony Azure Resource Manager.
author: mumian
ms.date: 03/27/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 0f7b966d4241716d71779e966de5d408711e4543
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80371778"
---
# <a name="tutorial-use-parameter-files-to-deploy-your-arm-template"></a>Kurz: Nasazení šablony ARM pomocí souborů parametrů

V tomto kurzu se dozvíte, jak pomocí [souborů parametrů](parameter-files.md) ukládat hodnoty, které předáte během nasazení. V předchozích kurzech jste použili vposlední části parametry s příkazem nasazení. Tento přístup fungoval při testování šablony Správce prostředků Azure (ARM), ale při automatizaci nasazení může být jednodušší předat sadu hodnot pro vaše prostředí. Soubory parametrů usnadňují balíček hodnot parametrů pro konkrétní prostředí. V tomto kurzu vytvoříte soubory parametrů pro vývojová a produkční prostředí. To trvá asi **12 minut** na dokončení.

## <a name="prerequisites"></a>Požadavky

Doporučujeme dokončit [kurz o značky](template-tutorial-add-tags.md), ale to není nutné.

Musíte mít kód Visual Studio s rozšířením Nástroje správce prostředků a buď Azure PowerShell nebo Azure CLI. Další informace naleznete v [tématu nástroje šablony](template-tutorial-create-first-template.md#get-tools).

## <a name="review-template"></a>Šablona revize

Šablona má mnoho parametrů, které můžete poskytnout během nasazení. Na konci předchozího kurzu vaše šablona vypadala takto:

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.json":::

Tato šablona funguje dobře, ale nyní chcete snadno spravovat parametry, které předáte pro šablonu.

## <a name="add-parameter-files"></a>Přidání souborů parametrů

Parametr soubory jsou JSON soubory se strukturou, která je podobná vaší šablony. V souboru zadáte hodnoty parametrů, které chcete předat během nasazení.

V kódu VS vytvořte nový soubor s následujícím obsahem. Uložte soubor s názvem **azuredeploy.parameters.dev.json**.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.parameters.dev.json":::

Tento soubor je soubor parametrů pro vývojové prostředí. Všimněte si, že používá Standard_LRS pro účet úložiště, názvy prostředků s předponou **dev** a nastaví značku **prostředí** na **Dev**.

Znovu vytvořte nový soubor s následujícím obsahem. Uložte soubor s názvem **azuredeploy.parameters.prod.json**.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.parameters.prod.json":::

Tento soubor je soubor parametrů pro produkční prostředí. Všimněte si, že používá Standard_GRS pro účet úložiště, názvy prostředků s předponou **contoso** a nastaví značku **prostředí** na **produkční prostředí**. V reálném produkčním prostředí byste také chtěli používat službu aplikace s skladovou položkou než zdarma, ale budeme i nadále používat tuto skladovou položku pro tento kurz.

## <a name="deploy-template"></a>Nasazení šablony

K nasazení šablony použijte azure cli nebo Azure PowerShell.

Jako poslední test šablony vytvoříme dvě nové skupiny prostředků. Jeden pro prostředí pro dev a jeden pro produkční prostředí.

Nejprve se nasadíme do prostředí pro dev.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$templateFile = "{path-to-the-template-file}"
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

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Chcete-li spustit tento příkaz nasazení, musíte mít [nejnovější verzi](/cli/azure/install-azure-cli) azure cli.

```azurecli
templateFile="{path-to-the-template-file}"
devParameterFile="{path-to-azuredeploy.parameters.dev.json}"
az group create \
  --name myResourceGroupDev \
  --location "East US"
az deployment group create \
  --name devenvironment \
  --resource-group myResourceGroupDev \
  --template-file $templateFile \
  --parameters $devParameterFile
```

---

Nyní se nasadíme do produkčního prostředí.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

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

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
prodParameterFile="{path-to-azuredeploy.parameters.prod.json}"
az group create \
  --name myResourceGroupProd \
  --location "West US"
az deployment group create \
  --name prodenvironment \
  --resource-group myResourceGroupProd \
  --template-file $templateFile \
  --parameters $prodParameterFile
```

---

## <a name="verify-deployment"></a>Ověření nasazení

Nasazení můžete ověřit tak, že prozkoumáte skupiny prostředků z webu Azure Portal.

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
1. V levé nabídce vyberte **položku Skupiny prostředků**.
1. Zobrazí se dvě nové skupiny prostředků, které jste nasadili v tomto kurzu.
1. Vyberte skupinu prostředků a zobrazte nasazené prostředky. Všimněte si, že odpovídají hodnotám, které jste zadali v souboru parametrů pro toto prostředí.

## <a name="clean-up-resources"></a>Vyčištění prostředků

1. Na portálu Azure vyberte **skupinu prostředků** z levé nabídky.
2. Do pole **Filtrovat podle názvu** zadejte název skupiny prostředků. Pokud jste dokončili tuto řadu, máte tři skupiny prostředků k odstranění - myResourceGroup, myResourceGroupDev a myResourceGroupProd.
3. Vyberte název skupiny prostředků.
4. V horní nabídce vyberte **Odstranit skupinu prostředků.**

## <a name="next-steps"></a>Další kroky

Gratulujeme, dokončili jste tento úvod k nasazení šablon do Azure. Dejte nám vědět, pokud máte nějaké připomínky a návrhy v sekci zpětná vazba. Děkujeme!

Další série kurzů jde do více podrobností o nasazení šablon.

> [!div class="nextstepaction"]
> [Nasazení místní šablony](./deployment-tutorial-local-template.md)
