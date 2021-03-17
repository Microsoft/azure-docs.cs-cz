---
title: Kurz – přidání modulů do souboru Azure Resource Manager bicep
description: Použijte moduly k zapouzdření komplexních podrobností o nezpracované deklaraci prostředků.
author: mumian
ms.date: 03/10/2021
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 6efd9c230df49c83adc17361082af85b0ef9edc5
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/11/2021
ms.locfileid: "102633134"
---
# <a name="tutorial-add-modules-to-azure-resource-manager-bicep-file"></a>Kurz: přidání modulů do souboru Azure Resource Manager bicep

V [předchozím kurzu](bicep-tutorial-use-parameter-file.md)jste zjistili, jak použít soubor parametrů k nasazení souboru bicep. V tomto kurzu se naučíte používat moduly bicep k zapouzdření složitých podrobností o nezpracované deklaraci prostředků. Moduly lze sdílet a znovu použít v rámci vašeho řešení.  Dokončení trvá přibližně **12 minut** .

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="prerequisites"></a>Požadavky

Doporučujeme, abyste dokončili [kurz týkající se souboru parametrů](bicep-tutorial-use-parameter-file.md), ale není to nutné.

Musíte mít Visual Studio Code s rozšířením bicep a buď Azure PowerShell nebo Azure CLI. Další informace najdete v tématu [bicep Tools](bicep-tutorial-create-first-bicep.md#get-tools).

## <a name="review-bicep-file"></a>Zkontrolovat soubor bicep

Na konci předchozího kurzu měl váš soubor bicep následující obsah:

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.bicep":::

Tento soubor bicep funguje dobře. U větších řešení ale budete chtít soubor bicep rozdělit do mnoha souvisejících modulů, takže můžete tyto moduly sdílet a znovu použít. Aktuální soubor bicep nasadí účet úložiště, plán služby App Service a Web.  Pojďme účet úložiště rozdělit do modulu.

## <a name="create-bicep-module"></a>Vytvořit modul bicep

Každý soubor bicep lze spotřebovat jako modul, takže není k dispozici žádná konkrétní syntaxe pro definování modulu. Vytvořte soubor _Storage. bicep_ s následujícím obsahem:

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-module/storage.bicep":::

Tento modul obsahuje prostředek účtu úložiště a související parametry a proměnné. Hodnoty parametru _Location_ a parametrů _resourceTags_ byly odebrány. Tyto hodnoty budou předány z hlavního souboru bicep.

## <a name="consume-bicep-module"></a>Spotřebovat modul bicep

Nahraďte definici prostředků účtu úložiště v existujícím _azuredeploy. bicep_ následujícím obsahem bicep:

```bicep
module stg './storage.bicep' = {
  name: 'storageDeploy'
  params: {
    storagePrefix: storagePrefix
    location: location
    resourceTags: resourceTags
  }
}
```

- **Module**: klíčové slovo
- **symbolický název** (STG): Toto je identifikátor pro modul.
- **soubor modulu**: cesta k modulu v tomto příkladu je určena pomocí relativní cesty (./Storage.bicep). Všechny cesty v bicep musí být zadané pomocí oddělovače adresáře dopředného lomítka (/), aby se zajistila konzistentní kompilace více platforem. Zpětné lomítko Windows ( \) znak není podporovaný.

Pokud chcete načíst koncový bod úložiště, aktualizujte výstup v _azuredeploy. bicep_ na následující bicep:

```bicep
output storageEndpoint object = stg.outputs.storageEndpoint
```

Dokončený azuredeploy. bicep má následující obsah:

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-module/azuredeploy.bicep":::

## <a name="deploy-template"></a>Nasazení šablony

K nasazení šablony použijte rozhraní příkazového řádku Azure nebo Azure PowerShell.

Pokud jste ještě nevytvořili skupinu prostředků, přečtěte si téma [Vytvoření skupiny prostředků](bicep-tutorial-create-first-bicep.md#create-resource-group). V příkladu se předpokládá, že jste nastavili `bicepFile` proměnnou na cestu k souboru bicep, jak je znázorněno v [prvním kurzu](bicep-tutorial-create-first-bicep.md#deploy-bicep-file).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Chcete-li spustit tuto rutinu nasazení, je nutné mít [nejnovější verzi](/powershell/azure/install-az-ps) Azure PowerShell.

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addmodule `
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
  --name addmodule \
  --resource-group myResourceGroup \
  --template-file $bicepFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS webAppName=demoapp
```

---
> [!NOTE]
> Pokud se nasazení nepovedlo, pomocí `verbose` přepínače Získejte informace o vytvářených prostředcích. K `debug` získání dalších informací pro ladění použijte přepínač.

## <a name="verify-deployment"></a>Ověření nasazení

Nasazení můžete ověřit prozkoumáním skupin prostředků z Azure Portal.

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
1. V nabídce vlevo vyberte **skupiny prostředků**.
1. V tomto kurzu se zobrazí dvě nové skupiny prostředků, které jste nasadili.
1. Vyberte buď skupinu prostředků, a zobrazte nasazené prostředky. Všimněte si, že odpovídají hodnotám, které jste zadali v souboru parametrů pro toto prostředí.

## <a name="clean-up-resources"></a>Vyčištění prostředků

1. Z Azure Portal v nabídce vlevo vyberte **Skupina prostředků** .
2. Do pole **Filtrovat podle názvu** zadejte název skupiny prostředků. Pokud jste tuto řadu dokončili, máte tři skupiny prostředků pro odstranění – **myResourceGroup**, **myResourceGroupDev** a **myResourceGroupProd**.
3. Vyberte název skupiny prostředků.
4. V horní nabídce vyberte **Odstranit skupinu prostředků** .

## <a name="next-steps"></a>Další kroky

Gratulujeme, dokončili jste tento Úvod k nasazení souborů bicep do Azure. Dejte nám vědět, pokud máte komentáře a návrhy v části věnované zpětné vazbě. Díky!

Další série kurzů se podrobněji seznámí s nasazením šablon.

> [!div class="nextstepaction"]
> [Přidat moduly](./bicep-tutorial-add-modules.md)
