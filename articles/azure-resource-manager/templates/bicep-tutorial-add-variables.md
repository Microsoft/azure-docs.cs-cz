---
title: Kurz – přidání proměnné do souboru Azure Resource Manager bicep
description: K zjednodušení syntaxe přidejte do souboru bicep proměnné.
author: mumian
ms.date: 03/10/2021
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: da2755c1f2c0f9fa891fe1a99b1fed21f64492c8
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "102632471"
---
# <a name="tutorial-add-variables-to-azure-resource-manager-bicep-file"></a>Kurz: Přidání proměnných do souboru Azure Resource Manager bicep

V tomto kurzu se dozvíte, jak přidat proměnnou do souboru bicep. Proměnné zjednodušují soubory bicep tím, že umožňují napsat výraz jednou a znovu ho použít v celém souboru bicep. Dokončení tohoto kurzu trvá **7 minut** .

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="prerequisites"></a>Požadavky

Doporučujeme, abyste dokončili [kurz týkající se funkcí](bicep-tutorial-add-functions.md), ale není to nutné.

Musíte mít Visual Studio Code s rozšířením bicep a buď Azure PowerShell nebo Azure CLI. Další informace najdete v tématu [bicep Tools](bicep-tutorial-create-first-bicep.md#get-tools).

## <a name="review-bicep-file"></a>Zkontrolovat soubor bicep

Na konci předchozího kurzu měl váš soubor bicep následující obsah:

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/add-location/azuredeploy.bicep":::

Parametr názvu účtu úložiště je nevratný, protože je nutné zadat jedinečný název. Pokud jste dokončili předchozí kurzy v této sérii, budete pravděpodobně už vás unavuje jedinečný název. Tento problém vyřešíte tak, že přidáte proměnnou, která vytvoří jedinečný název pro účet úložiště.

## <a name="use-variable"></a>Použít proměnnou

Následující příklad ukazuje změny pro přidání proměnné do souboru bicep, který vytvoří jedinečný název účtu úložiště. Zkopírujte celý soubor a nahraďte soubor bicep jeho obsahem.

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/add-variable/azuredeploy.bicep" range="1-31" highlight="1-3,19,22":::

Všimněte si, že obsahuje proměnnou s názvem `uniqueStorageName` . Tato proměnná používá tři funkce k vytvoření řetězcové hodnoty.

Jste obeznámeni s funkcí [Resource](template-functions-resource.md#resourcegroup) . V takovém případě získáte `id` vlastnost namísto `location` vlastnosti, jak je znázorněno v předchozím kurzu. `id`Vlastnost vrátí úplný identifikátor skupiny prostředků, včetně ID předplatného a názvu skupiny prostředků.

Funkce [uniqueString](template-functions-string.md#uniquestring) vytvoří hodnotu hash znaku 13 znaků. Vrácená hodnota je určena parametry, které předáte. V tomto kurzu použijete ID skupiny prostředků jako vstup pro hodnotu hash. To znamená, že můžete tento soubor bicep nasadit do různých skupin prostředků a získat jinou jedinečnou řetězcovou hodnotu. Pokud však nasadíte do stejné skupiny prostředků, získáte stejnou hodnotu.

Bicep podporuje syntaxi [řetězcové interpolace](https://en.wikipedia.org/wiki/String_interpolation#) . Pro tuto proměnnou přebírá řetězec z parametru a řetězce z `uniqueString` funkce a kombinuje je do jednoho řetězce.

`storagePrefix`Parametr vám umožní předat předponu, která vám pomůže identifikovat účty úložiště. Můžete vytvořit vlastní zásadu vytváření názvů, která usnadňuje identifikaci účtů úložiště po nasazení z dlouhého seznamu prostředků.

Nakonec si všimněte, že název úložiště je nyní nastaven na proměnnou namísto parametru.

## <a name="deploy-bicep-file"></a>Nasadit soubor bicep

Pojďme nasadit soubor bicep. Nasazení tohoto souboru bicep je snazší než u předchozích souborů bicep, protože zadáváte jenom předponu pro název úložiště.

Pokud jste ještě nevytvořili skupinu prostředků, přečtěte si téma [Vytvoření skupiny prostředků](bicep-tutorial-create-first-bicep.md#create-resource-group). V příkladu se předpokládá, že jste nastavili `bicepFile` proměnnou na cestu k souboru bicep, jak je znázorněno v [prvním kurzu](bicep-tutorial-create-first-bicep.md#deploy-bicep-file).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Chcete-li spustit tuto rutinu nasazení, je nutné mít [nejnovější verzi](/powershell/azure/install-az-ps) Azure PowerShell.

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addnamevariable `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $bicepFile `
  -storagePrefix "store" `
  -storageSKU Standard_LRS
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Jestliže chcete spustit tento příkaz nasazení, musíte mít k dispozici [nejnovější verzi](/cli/azure/install-azure-cli) Azure CLI.

```azurecli
az deployment group create \
  --name addnamevariable \
  --resource-group myResourceGroup \
  --template-file $bicepFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS
```

---

> [!NOTE]
> Pokud se nasazení nepovedlo, pomocí `verbose` přepínače Získejte informace o vytvářených prostředcích. K `debug` získání dalších informací pro ladění použijte přepínač.

## <a name="verify-deployment"></a>Ověření nasazení

Nasazení můžete ověřit prozkoumáním skupiny prostředků z Azure Portal.

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
1. V nabídce vlevo vyberte **skupiny prostředků**.
1. Vyberte skupinu prostředků, do které jste nasadili.
1. Vidíte, že byl nasazen prostředek účtu úložiště. Název účtu úložiště je **uložený** spolu s řetězcem náhodných znaků.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud se chystáte pokračovat k dalšímu kurzu, nemusíte odstranit skupinu prostředků.

Pokud nyní zastavíte, budete možná chtít vyčistit prostředky, které jste nasadili, odstraněním skupiny prostředků.

1. Z Azure Portal v nabídce vlevo vyberte **Skupina prostředků** .
2. Do pole **Filtrovat podle názvu** zadejte název skupiny prostředků.
3. Vyberte název skupiny prostředků.
4. V horní nabídce vyberte **Odstranit skupinu prostředků** .

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste přidali proměnnou, která pro účet úložiště vytvoří jedinečný název. V dalším kurzu vrátíte hodnotu z nasazeného účtu úložiště.

> [!div class="nextstepaction"]
> [Přidat výstupy](bicep-tutorial-add-outputs.md)
