---
title: Výuka - přidání proměnné do šablony
description: Přidejte proměnné do šablony Azure Resource Manager, abyste zjednodušili syntaxi.
author: mumian
ms.date: 03/27/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: b1df86e5b593edec784de21e21a4399274d820bb
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2020
ms.locfileid: "80411687"
---
# <a name="tutorial-add-variables-to-your-arm-template"></a>Kurz: Přidání proměnných do šablony ARM

V tomto kurzu se dozvíte, jak přidat proměnnou do šablony Správce prostředků Azure (ARM). Proměnné zjednodušují šablony tím, že umožňují napsat výraz jednou a znovu jej použít v celé šabloně. Tento kurz trvá **7 minut.**

## <a name="prerequisites"></a>Požadavky

Doporučujeme dokončit [výukový program o funkcích](template-tutorial-add-functions.md), ale není to nutné.

Musíte mít kód Visual Studio s rozšířením Nástroje správce prostředků a buď Azure PowerShell nebo Azure CLI. Další informace naleznete v [tématu nástroje šablony](template-tutorial-create-first-template.md#get-tools).

## <a name="review-template"></a>Šablona revize

Na konci předchozího kurzu měla vaše šablona následující JSON:

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-location/azuredeploy.json":::

Parametr pro název účtu úložiště je obtížné použít, protože je třeba zadat jedinečný název. Pokud jste dokončili předchozí kurzy v této sérii, jste pravděpodobně unaveni hádat jedinečný název. Tento problém vyřešíte přidáním proměnné, která vytvoří jedinečný název pro účet úložiště.

## <a name="use-variable"></a>Použít proměnnou

Následující příklad zvýrazní změny pro přidání proměnné do šablony, která vytvoří jedinečný název účtu úložiště. Zkopírujte celý soubor a nahraďte šablonu jeho obsahem.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-variable/azuredeploy.json" range="1-47" highlight="5-9,29-31,36":::

Všimněte si, že obsahuje proměnnou s názvem **uniqueStorageName**. Tato proměnná používá čtyři funkce k vytvoření hodnoty řetězce.

Jste již obeznámeni s [parametry](template-functions-deployment.md#parameters) funkce, takže nebudeme zkoumat.

Jste také obeznámeni s funkcí [resourceGroup.](template-functions-resource.md#resourcegroup) V tomto případě získáte **id** vlastnost namísto **vlastnosti umístění,** jak je znázorněno v předchozím kurzu. **Id** vlastnost vrátí úplný identifikátor skupiny prostředků, včetně ID předplatného a název skupiny prostředků.

Funkce [uniqueString](template-functions-string.md#uniquestring) vytvoří hodnotu hash 13 znaků. Vrácená hodnota je určena parametry, které předáte. V tomto kurzu použijete ID skupiny prostředků jako vstup pro hodnotu hash. To znamená, že můžete nasadit tuto šablonu do různých skupin prostředků a získat jinou jedinečnou hodnotu řetězce. Však získáte stejnou hodnotu, pokud nasadíte do stejné skupiny prostředků.

Funkce [concat](template-functions-string.md#concat) přebírá hodnoty a kombinuje je. Pro tuto proměnnou přebírá řetězec z parametru a řetězec z funkce uniqueString a kombinuje je do jednoho řetězce.

Parametr **storagePrefix** umožňuje předat předponu, která vám pomůže identifikovat účty úložiště. Můžete vytvořit vlastní konvence pojmenování, která usnadňuje identifikaci účtů úložiště po nasazení z dlouhého seznamu prostředků.

Nakonec si všimněte, že název úložiště je nyní nastavena na proměnnou namísto parametru.

## <a name="deploy-template"></a>Nasazení šablony

Pojďme nasadit šablonu. Nasazení této šablony je jednodušší než předchozí šablony, protože zadáte pouze předponu pro název úložiště.

Pokud jste skupinu prostředků nevytvořili, přečtěte si informace [o vytvoření skupiny prostředků](template-tutorial-create-first-template.md#create-resource-group). Příklad předpokládá, že jste nastavili proměnnou **templateFile** na cestu k souboru šablony, jak je znázorněno v [prvním kurzu](template-tutorial-create-first-template.md#deploy-template).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addnamevariable `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storagePrefix "store" `
  -storageSKU Standard_LRS
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Chcete-li spustit tento příkaz nasazení, musíte mít [nejnovější verzi](/cli/azure/install-azure-cli) azure cli.

```azurecli
az deployment group create \
  --name addnamevariable \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS
```

---

> [!NOTE]
> Pokud se nasazení nezdařilo, zobrazte protokoly ladění pomocí **přepínače ladění** s příkazem deployment.  Můžete také použít **podrobný** přepínač k zobrazení úplné protokoly ladění.

## <a name="verify-deployment"></a>Ověření nasazení

Nasazení můžete ověřit tak, že prozkoumáte skupinu prostředků z webu Azure Portal.

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
1. V levé nabídce vyberte **položku Skupiny prostředků**.
1. Vyberte skupinu prostředků, do které jste nasadili.
1. Uvidíte, že prostředek účtu úložiště byl nasazen. Název účtu úložiště je **úložiště** plus řetězec náhodných znaků.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud přecházíte na další kurz, nemusíte odstraňovat skupinu prostředků.

Pokud nyní zastavujete, můžete chtít vyčistit prostředky, které jste nasadili, odstraněním skupiny prostředků.

1. Na portálu Azure vyberte **skupinu prostředků** z levé nabídky.
2. Do pole **Filtrovat podle názvu** zadejte název skupiny prostředků.
3. Vyberte název skupiny prostředků.
4. V horní nabídce vyberte **Odstranit skupinu prostředků.**

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste přidali proměnnou, která vytvoří jedinečný název pro účet úložiště. V dalším kurzu vrátíte hodnotu z účtu nasazeného úložiště.

> [!div class="nextstepaction"]
> [Přidání výstupů](template-tutorial-add-outputs.md)
