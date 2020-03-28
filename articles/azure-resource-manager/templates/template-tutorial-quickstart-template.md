---
title: Kurz – použití šablon rychlého startu
description: Přečtěte si, jak pomocí šablon Azure QuickStart dokončit vývoj šablon.
author: mumian
ms.date: 03/27/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 8747a4614cb8106ce80c6caef0aae36111d2c384
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80369851"
---
# <a name="tutorial-use-azure-quickstart-templates"></a>Kurz: Použití šablon Azure Quickstart

[Šablony Azure Quickstart](https://azure.microsoft.com/resources/templates/) je úložiště šablon, do kterých přispěla komunita. Ukázkové šablony můžete použít ve vývoji šablony. V tomto kurzu najdete definici prostředků webu a přidáte ji do vlastní šablony. To trvá asi **12 minut** na dokončení.

## <a name="prerequisites"></a>Požadavky

Doporučujeme dokončit [kurz o exportovaných šablonách](template-tutorial-export-template.md), ale není to nutné.

Musíte mít kód Visual Studio s rozšířením Nástroje správce prostředků a buď Azure PowerShell nebo Azure CLI. Další informace naleznete v [tématu nástroje šablony](template-tutorial-create-first-template.md#get-tools).

## <a name="review-template"></a>Šablona revize

Na konci předchozího kurzu měla vaše šablona následující JSON:

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/export-template/azuredeploy.json":::

Tato šablona funguje pro nasazení účtů úložiště a plánů služby App Service, ale možná do ní budete chtít přidat web. Pomocí předem vytvořených šablon můžete rychle zjistit JSON, který je nutný pro nasazení prostředku.

## <a name="find-template"></a>Najít šablonu

1. Otevření [šablon Azure Quickstart](https://azure.microsoft.com/resources/templates/)
1. Ve **vyhledávání**zadejte **nasazení linuxové webové aplikace**.
1. Vyberte ten s názvem **Nasadit základní linuxovou webovou aplikaci**. Pokud máte potíže najít, tady je [přímý odkaz](https://azure.microsoft.com/resources/templates/101-webapp-basic-linux/).
1. Vyberte **Procházet na GitHubu**.
1. Vyberte **azuredeploy.json**.
1. Zkontrolujte šablonu. Zejména vyhledejte `Microsoft.Web/sites` prostředek.

    ![Web rychlého spuštění šablony Resource Manager](./media/template-tutorial-quickstart-template/resource-manager-template-quickstart-template-web-site.png)

## <a name="revise-existing-template"></a>Revize existující šablony

Sloučit šablonu rychlého startu s existující šablonou:

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/quickstart-template/azuredeploy.json" range="1-108" highlight="32-45,49,85-100":::

Název webové aplikace musí být jedinečný v celém Azure. Aby se zabránilo duplicitním názvům, byla proměnná **webAppPortalName** aktualizována z **"webAppPortalName": "[concat(parameters('webAppName'), '-webapp')]"** na **"webAppPortalName": "[concat(parameters('webAppName'), uniqueString(resourceGroup().id))]"**.

Přidejte čárku na konci `Microsoft.Web/serverfarms` definice oddělit definici prostředku `Microsoft.Web/sites` od definice.

V tomto novém prostředku je třeba zaznamenat několik důležitých funkcí.

Všimněte si, že má prvek s názvem **dependsOn,** který je nastaven na plán služby aplikace. Toto nastavení je povinné, protože plán služby App Service musí existovat před vytvořením webové aplikace. Element **dependsOn** říká Resource Manageru, jak objednat prostředky pro nasazení.

Vlastnost **serverFarmId** používá funkci [resourceId.](template-functions-resource.md#resourceid) Tato funkce získá jedinečný identifikátor prostředku. V takovém případě získá jedinečný identifikátor pro plán služby aplikace. Webová aplikace je přidružená k jednomu konkrétnímu plánu služby app service.

## <a name="deploy-template"></a>Nasazení šablony

K nasazení šablony použijte azure CLI nebo Azure PowerShell.

Pokud jste skupinu prostředků nevytvořili, přečtěte si informace [o vytvoření skupiny prostředků](template-tutorial-create-first-template.md#create-resource-group). Příklad předpokládá, že jste nastavili proměnnou **templateFile** na cestu k souboru šablony, jak je znázorněno v [prvním kurzu](template-tutorial-create-first-template.md#deploy-template).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addwebapp `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storagePrefix "store" `
  -storageSKU Standard_LRS `
  -webAppName demoapp
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Chcete-li spustit tento příkaz nasazení, musíte mít [nejnovější verzi](/cli/azure/install-azure-cli) azure cli.

```azurecli
az deployment group create \
  --name addwebapp \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS webAppName=demoapp
```

---

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud přecházíte na další kurz, nemusíte odstraňovat skupinu prostředků.

Pokud nyní zastavujete, můžete chtít vyčistit prostředky, které jste nasadili, odstraněním skupiny prostředků.

1. Na portálu Azure vyberte **skupinu prostředků** z levé nabídky.
2. Do pole **Filtrovat podle názvu** zadejte název skupiny prostředků.
3. Vyberte název skupiny prostředků.
4. V horní nabídce vyberte **Odstranit skupinu prostředků.**

## <a name="next-steps"></a>Další kroky

Naučili jste se používat šablonu rychlého startu pro vývoj šablony. V dalším kurzu přidáte značky do prostředků.

> [!div class="nextstepaction"]
> [Přidání značek](template-tutorial-add-tags.md)
