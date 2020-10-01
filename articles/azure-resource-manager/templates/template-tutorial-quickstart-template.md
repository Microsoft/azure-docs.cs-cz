---
title: Kurz – použití šablon pro rychlý Start
description: Naučte se používat šablony Azure pro rychlý Start k dokončení vývoje šablon.
author: mumian
ms.date: 03/27/2020
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: 445e7ce2d6e609d75bff38bb3d049a87f184be12
ms.sourcegitcommit: 06ba80dae4f4be9fdf86eb02b7bc71927d5671d3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/01/2020
ms.locfileid: "91613590"
---
# <a name="tutorial-use-azure-quickstart-templates"></a>Kurz: použití šablon Azure pro rychlý Start

[Šablony Azure pro rychlý Start](https://azure.microsoft.com/resources/templates/) jsou úložištěm, které vám poskytla komunita. Můžete použít ukázkové šablony pro vývoj šablon. V tomto kurzu najdete definici prostředků webu a přidáte ji do své vlastní šablony. Dokončení trvá přibližně **12 minut** .

## <a name="prerequisites"></a>Předpoklady

Doporučujeme, abyste dokončili [kurz týkající se exportovaných šablon](template-tutorial-export-template.md), ale není to nutné.

Musíte mít Visual Studio Code s rozšířením Správce prostředků Tools a buď Azure PowerShell, nebo v rozhraní příkazového řádku Azure. Další informace najdete v tématu [nástroje šablon](template-tutorial-create-first-template.md#get-tools).

## <a name="review-template"></a>Zkontrolovat šablonu

Na konci předchozího kurzu má vaše šablona následující JSON:

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/export-template/azuredeploy.json":::

Tato šablona funguje pro nasazení účtů úložiště a plánů služby App Service, ale můžete chtít do ní přidat web. Předem připravené šablony můžete použít k rychlému zjištění formátu JSON, který je potřeba k nasazení prostředku.

## <a name="find-template"></a>Najít šablonu

1. Otevření [šablon pro rychlý Start Azure](https://azure.microsoft.com/resources/templates/)
1. Do **Hledat**zadejte **nasazení webové aplikace Linux**.
1. Vyberte jeden s nadpisem **nasazení základní webové aplikace pro Linux**. Pokud máte potíže s jeho hledáním, tady je [přímý odkaz](https://azure.microsoft.com/resources/templates/101-webapp-basic-linux/).
1. Vyberte **Procházet na GitHubu**.
1. Vyberte **azuredeploy.js**.
1. Zkontrolujte šablonu. Konkrétně vyhledejte `Microsoft.Web/sites` prostředek.

    ![Web rychlý Start pro šablonu Správce prostředků](./media/template-tutorial-quickstart-template/resource-manager-template-quickstart-template-web-site.png)

## <a name="revise-existing-template"></a>Revidovat existující šablonu

Sloučit šablonu pro rychlý Start se stávající šablonou:

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/quickstart-template/azuredeploy.json" range="1-108" highlight="32-45,49,85-100":::

Název webové aplikace musí být v rámci Azure jedinečný. Aby nedocházelo k duplicitním názvům, byla proměnná **webAppPortalName** aktualizována z **"webAppPortalName": "[Concat (Parameters (' webAppName '), '-WebApp ')]"** na **"webAppPortalName": "[Concat (Parameters (' webAppName '), uniqueString (resourceName (). ID))]"**.

Přidejte čárku na konec `Microsoft.Web/serverfarms` definice pro oddělení definice prostředků od `Microsoft.Web/sites` definice.

V tomto novém prostředku si můžete všimnout několika důležitých funkcí.

Všimněte si, že má element s názvem **dependsOn** , který je nastavený na plán služby App Service. Toto nastavení se vyžaduje, protože plán služby App Service musí existovat před vytvořením webové aplikace. Element **dependsOn** oznamuje správce prostředků, jak objednat prostředky pro nasazení.

Vlastnost **serverFarmId** používá funkci [ResourceID](template-functions-resource.md#resourceid) . Tato funkce získá jedinečný identifikátor prostředku. V tomto případě získá jedinečný identifikátor plánu služby App Service. Webová aplikace je přidružená k jednomu konkrétnímu plánu služby App Service.

## <a name="deploy-template"></a>Nasazení šablony

K nasazení šablony použijte rozhraní příkazového řádku Azure nebo Azure PowerShell.

Pokud jste ještě nevytvořili skupinu prostředků, přečtěte si téma [Vytvoření skupiny prostředků](template-tutorial-create-first-template.md#create-resource-group). V příkladu se předpokládá, že jste nastavili proměnnou **templateFile** na cestu k souboru šablony, jak je znázorněno v [prvním kurzu](template-tutorial-create-first-template.md#deploy-template).

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

Jestliže chcete spustit tento příkaz nasazení, musíte mít k dispozici [nejnovější verzi](/cli/azure/install-azure-cli) Azure CLI.

```azurecli
az deployment group create \
  --name addwebapp \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS webAppName=demoapp
```

---

> [!NOTE]
> Pokud se nasazení nepovedlo, pomocí **podrobného** přepínače Získejte informace o vytvářených prostředcích. K získání dalších informací pro ladění použijte přepínač **ladění** .

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud se chystáte pokračovat k dalšímu kurzu, nemusíte odstranit skupinu prostředků.

Pokud nyní zastavíte, budete možná chtít vyčistit prostředky, které jste nasadili, odstraněním skupiny prostředků.

1. Z Azure Portal v nabídce vlevo vyberte **Skupina prostředků** .
2. Do pole **Filtrovat podle názvu** zadejte název skupiny prostředků.
3. Vyberte název skupiny prostředků.
4. V horní nabídce vyberte **Odstranit skupinu prostředků** .

## <a name="next-steps"></a>Další kroky

Zjistili jste, jak používat šablonu pro rychlý Start pro vývoj šablon. V dalším kurzu přidáte do prostředků značky.

> [!div class="nextstepaction"]
> [Přidání značek](template-tutorial-add-tags.md)
