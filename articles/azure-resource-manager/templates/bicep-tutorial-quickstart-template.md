---
title: Kurz – použití šablon rychlý Start pro Azure Resource Manager vývoj bicep
description: Naučte se používat šablony Azure pro rychlý Start k dokončení vývoje bicep.
author: mumian
ms.date: 03/10/2021
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: cf655885e01fe6bca99a82c82d6bbbc4c1a080b3
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/11/2021
ms.locfileid: "102632424"
---
# <a name="tutorial-use-azure-quickstart-templates-for-azure-resource-manager-bicep-development"></a>Kurz: použití šablon pro rychlý Start Azure pro Azure Resource Manager vývoj bicep

[Šablony pro rychlý Start Azure](https://azure.microsoft.com/resources/templates/) jsou úložištěm pro komunity, které přispěly k šablonám JSON. Můžete použít ukázkové šablony v bicep vývoj. V tomto kurzu najdete definici prostředků webu, dekompilujete ji do bicep a přidáte ji do souboru bicep. Dokončení trvá přibližně **12 minut** .

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="prerequisites"></a>Požadavky

Doporučujeme, abyste dokončili [kurz týkající se exportovaných šablon](bicep-tutorial-export-template.md), ale není to nutné.

Musíte mít Visual Studio Code s rozšířením bicep a buď Azure PowerShell nebo Azure CLI. Další informace najdete v tématu [bicep Tools](bicep-tutorial-create-first-bicep.md#get-tools).

## <a name="review-bicep-file"></a>Zkontrolovat soubor bicep

Na konci předchozího kurzu měl váš soubor bicep následující obsah:

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/export-template/azuredeploy.bicep":::

Tento soubor bicep funguje pro nasazení účtů úložiště a plánů služby App Service, ale můžete chtít do něj přidat web. Předem připravené šablony můžete použít k rychlému zjištění formátu JSON, který je potřeba k nasazení prostředku. Než šablony Azure pro rychlý Start nabízejí ukázky bicep, můžete k převodu šablon JSON na soubory bicep použít nástroje pro převod.

## <a name="find-template"></a>Najít šablonu

Šablony pro rychlý Start Azure v současné době poskytují pouze šablony JSON. Existují nástroje, které můžete použít k dekompilaci šablon JSON do šablon bicep.

1. Otevření [šablon pro rychlý Start Azure](https://azure.microsoft.com/resources/templates/)
1. Do **Hledat** zadejte _nasazení webové aplikace Linux_.
1. Vyberte dlaždici s názvem **nasazení základní webové aplikace pro Linux**. Pokud máte potíže s jeho hledáním, tady je [přímý odkaz](https://azure.microsoft.com/resources/templates/101-webapp-basic-linux/).
1. Vyberte **Procházet na GitHubu**.
1. Vyberte _azuredeploy.js_. Toto je šablona, kterou můžete použít.
1. Vyberte **Nezpracovaná** a pak vytvořte kopii adresy URL.
1. Přejděte na **https://bicepdemo.z22.web.core.windows.net/** , vyberte **dekompilovat** a pak zadejte adresu URL nezpracované šablony.
1. Zkontrolujte šablonu bicep. Konkrétně vyhledejte `Microsoft.Web/sites` prostředek.

    ![Web rychlý Start pro šablonu Správce prostředků](./media/bicep-tutorial-quickstart-template/resource-manager-template-quickstart-template-web-site.png)

    Pokud jste pracovali na šablonách JSON, existuje několik důležitých funkcí bicep, které si můžete v tomto novém prostředku poznamenat.

    V případě šablon JSON pro ARM je nutné ručně zadat závislosti prostředků pomocí vlastnosti _dependsOn_ . Prostředek webu závisí na prostředku plánu služby App Service. Pokud v bicep odkazujete na jakoukoliv vlastnost prostředku pomocí symbolického názvu, je automaticky přidána vlastnost dependsOn.

    Můžete snadno odkazovat na ID prostředku z symbolického názvu plánu služby App Service (appServicePlanName.id), který bude přeložen na funkci resourceId (...) v kompilované šabloně JSON.

## <a name="revise-existing-bicep-file"></a>Revidovat existující soubor bicep

Slučte šablonu dekompilovaného rychlého startu s existujícím souborem bicep. Stejně jako to, co jste provedli v předchozím kurzu, aktualizujte symbolický název prostředku a název prostředku, aby odpovídal vaší konvenci pojmenování.

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/quickstart-template/azuredeploy.bicep" range="1-73" highlight="20-25,28,61-71":::

Název webové aplikace musí být v rámci Azure jedinečný. Aby nedocházelo k duplicitním názvům, byla `webAppPortalName` Proměnná aktualizována z `var webAppPortalName_var = '${webAppName}-webapp'` na `var webAppPortalName = '${webAppName}${uniqueString(resourceGroup().id)}'` .

## <a name="deploy-bicep-file"></a>Nasadit soubor bicep

K nasazení šablony bicep použijte rozhraní příkazového řádku Azure nebo Azure PowerShell.

Pokud jste ještě nevytvořili skupinu prostředků, přečtěte si téma [Vytvoření skupiny prostředků](bicep-tutorial-create-first-bicep.md#create-resource-group). V příkladu se předpokládá, že jste nastavili proměnnou **bicepFile** na cestu k souboru bicep, jak je znázorněno v [prvním kurzu](bicep-tutorial-create-first-bicep.md#deploy-bicep-file).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Chcete-li spustit tuto rutinu nasazení, je nutné mít [nejnovější verzi](/powershell/azure/install-az-ps) Azure PowerShell.

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addwebapp `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $bicepFile `
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
  --template-file $bicepFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS webAppName=demoapp
```

---

> [!NOTE]
> Pokud se nasazení nepovedlo, pomocí `verbose` přepínače Získejte informace o vytvářených prostředcích. K `debug` získání dalších informací pro ladění použijte přepínač.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud se chystáte pokračovat k dalšímu kurzu, nemusíte odstranit skupinu prostředků.

Pokud nyní zastavíte, budete možná chtít vyčistit prostředky, které jste nasadili, odstraněním skupiny prostředků.

1. Z Azure Portal v nabídce vlevo vyberte **Skupina prostředků** .
2. Do pole **Filtrovat podle názvu** zadejte název skupiny prostředků.
3. Vyberte název skupiny prostředků.
4. V horní nabídce vyberte **Odstranit skupinu prostředků** .

## <a name="next-steps"></a>Další kroky

Zjistili jste, jak používat šablonu pro rychlý Start pro vývoj v bicep. V dalším kurzu přidáte do prostředků značky.

> [!div class="nextstepaction"]
> [Přidání značek](bicep-tutorial-add-tags.md)
