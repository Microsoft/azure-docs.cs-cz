---
title: Kurz – použití šablon Azure pro rychlý Start | Microsoft Docs
description: Naučte se používat šablony Azure pro rychlý Start k dokončení vývoje šablon.
services: azure-resource-manager
author: mumian
manager: carmonmills
ms.service: azure-resource-manager
ms.date: 10/04/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: a29d86d105579dda7c12b885e2977406f7b598a4
ms.sourcegitcommit: be344deef6b37661e2c496f75a6cf14f805d7381
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/07/2019
ms.locfileid: "72001481"
---
# <a name="tutorial-use-azure-quickstart-templates"></a>Kurz: použití šablon Azure pro rychlý Start

[Šablony Azure pro rychlý Start](https://azure.microsoft.com/resources/templates/) jsou úložištěm, které vám poskytla komunita. Můžete použít ukázkové šablony pro vývoj šablon. V tomto kurzu najdete definici prostředků webu a přidáte ji do své vlastní šablony. Dokončení trvá přibližně **12 minut** .

## <a name="prerequisites"></a>Požadavky

Doporučujeme, abyste dokončili [kurz týkající se exportovaných šablon](template-tutorial-export-template.md), ale není to nutné.

Musíte mít Visual Studio Code s rozšířením Správce prostředků Tools a buď Azure PowerShell, nebo v rozhraní příkazového řádku Azure. Další informace najdete v tématu [nástroje šablon](template-tutorial-create-first-template.md#get-tools).

## <a name="review-your-template"></a>Kontrola šablony

Na konci předchozího kurzu má vaše šablona následující JSON:

[!code-json[](~/resourcemanager-templates/get-started-with-templates/export-template/azuredeploy.json)]

Tato šablona funguje pro nasazení účtů úložiště a plánů služby App Service, ale můžete chtít do ní přidat web. Předem připravené šablony můžete použít k rychlému zjištění formátu JSON, který je potřeba k nasazení prostředku.

## <a name="find-a-template"></a>Najít šablonu

1. Otevření [šablon pro rychlý Start Azure](https://azure.microsoft.com/resources/templates/)
1. Do **Hledat**zadejte **nasazení webové aplikace Linux**.
1. Vyberte jeden s nadpisem **nasazení základní webové aplikace pro Linux**. Pokud máte potíže s jeho hledáním, tady je [přímý odkaz](https://azure.microsoft.com/resources/templates/101-webapp-basic-linux/).
1. Vyberte **Procházet na GitHubu**.
1. Vyberte **azuredeploy. JSON**.
1. Zkontrolujte šablonu. Zejména vyhledejte prostředek `Microsoft.Web/sites`.

    ![Web rychlý Start pro šablonu Správce prostředků](./media/template-tutorial-quickstart-template/resource-manager-template-quickstart-template-web-site.png)

## <a name="revise-the-existing-template"></a>Revidovat existující šablonu

Sloučit šablonu pro rychlý Start se stávající šablonou:

[!code-json[](~/resourcemanager-templates/get-started-with-templates/quickstart-template/azuredeploy.json?range=1-108&highlight=32-45,49,85-100)]

Název WebApp musí být v rámci Azure jedinečný. Aby nedocházelo k duplicitním názvům, byla proměnná **webAppPortalName** aktualizována z **"webAppPortalName": "[Concat (Parameters (' webAppName '), '-WebApp ')]"** na **"webAppPortalName": "[Concat (Parameters (' webAppName '), uniqueString (Resource (). ID))] "** .

Přidáním čárky na konec definice `Microsoft.Web/serverfarms` oddělte definici prostředků od definice `Microsoft.Web/sites`.

V tomto novém prostředku si můžete všimnout několika důležitých funkcí.

Všimněte si, že má element s názvem **dependsOn** , který je nastavený na plán služby App Service. Toto nastavení se vyžaduje, protože plán služby App Service musí existovat před vytvořením webové aplikace. Element **dependsOn** oznamuje správce prostředků, jak objednat prostředky pro nasazení.

Vlastnost **serverFarmId** používá funkci [ResourceID](resource-group-template-functions-resource.md#resourceid) . Tato funkce získá jedinečný identifikátor prostředku. V tomto případě získá jedinečný identifikátor plánu služby App Service. Webová aplikace je přidružená k jednomu konkrétnímu plánu služby App Service.

## <a name="deploy-the-template"></a>Nasazení šablony

K nasazení šablony použijte rozhraní příkazového řádku Azure nebo Azure PowerShell.

Pokud jste ještě nevytvořili skupinu prostředků, přečtěte si téma [Vytvoření skupiny prostředků](template-tutorial-create-first-template.md#create-resource-group). V příkladu se předpokládá, že jste nastavili proměnnou **templateFile** na cestu k souboru šablony, jak je znázorněno v [prvním kurzu](template-tutorial-create-first-template.md#deploy-template).

# <a name="powershelltabazure-powershell"></a>[Prostředí](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addwebapp `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storagePrefix "store" `
  -storageSKU Standard_LRS `
  -webAppName demoapp
```

# <a name="azure-clitabazure-cli"></a>[Rozhraní příkazového řádku Azure](#tab/azure-cli)

```azurecli
az group deployment create \
  --name addwebapp \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS webAppName=demoapp
```

---

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud se chystáte pokračovat k dalšímu kurzu, nemusíte odstranit skupinu prostředků.

Pokud nyní zastavíte, budete možná chtít vyčistit prostředky, které jste nasadili, odstraněním skupiny prostředků.

1. Z Azure Portal v nabídce vlevo vyberte **Skupina prostředků** .
2. Do pole **filtrovat podle názvu** zadejte název skupiny prostředků.
3. Vyberte název skupiny prostředků.
4. V horní nabídce vyberte **Odstranit skupinu prostředků** .

## <a name="next-steps"></a>Další kroky

Zjistili jste, jak používat šablonu pro rychlý Start pro vývoj šablon. V dalším kurzu přidáte do prostředků značky.

> [!div class="nextstepaction"]
> [Přidat značky](template-tutorial-add-tags.md)
