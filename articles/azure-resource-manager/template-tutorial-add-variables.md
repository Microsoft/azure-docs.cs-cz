---
title: Kurz – přidání proměnné do šablony
description: K zjednodušení syntaxe přidejte proměnné do šablony Azure Resource Manager.
author: mumian
ms.date: 10/04/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 0598da3c060b8a8055ffb045fe4aae60b3806060
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/22/2019
ms.locfileid: "74406051"
---
# <a name="tutorial-add-variables-to-your-resource-manager-template"></a>Kurz: Přidání proměnných do šablony Správce prostředků

V tomto kurzu se dozvíte, jak přidat proměnnou do šablony. Proměnné zjednodušují vaše šablony tím, že umožňují napsat výraz jednou a znovu ho použít v celé šabloně. Dokončení tohoto kurzu trvá **7 minut** .

## <a name="prerequisites"></a>Požadavky

Doporučujeme, abyste dokončili [kurz týkající se funkcí](template-tutorial-add-functions.md), ale není to nutné.

Musíte mít Visual Studio Code s rozšířením Správce prostředků Tools a buď Azure PowerShell, nebo v rozhraní příkazového řádku Azure. Další informace najdete v tématu [nástroje šablon](template-tutorial-create-first-template.md#get-tools).

## <a name="review-template"></a>Zkontrolovat šablonu

Na konci předchozího kurzu má vaše šablona následující JSON:

[!code-json[](~/resourcemanager-templates/get-started-with-templates/add-location/azuredeploy.json)]

Parametr názvu účtu úložiště je nevratný, protože je nutné zadat jedinečný název. Pokud jste dokončili předchozí kurzy v této sérii, budete pravděpodobně už vás unavuje jedinečný název. Tento problém vyřešíte tak, že přidáte proměnnou, která vytvoří jedinečný název pro účet úložiště.

## <a name="use-variable"></a>Použít proměnnou

Následující příklad zvýrazní změny a přidá proměnnou do šablony, která vytvoří jedinečný název účtu úložiště. Zkopírujte celý soubor a nahraďte šablonu jeho obsahem.

[!code-json[](~/resourcemanager-templates/get-started-with-templates/add-variable/azuredeploy.json?range=1-47&highlight=5-9,29-31,36)]

Všimněte si, že obsahuje proměnnou s názvem **uniqueStorageName**. Tato proměnná používá čtyři funkce k vytvoření řetězcové hodnoty.

Již jste obeznámeni s funkcí [Parameters](resource-group-template-functions-deployment.md#parameters) , takže ji nebudeme posuzovat.

Také jste obeznámeni se funkcí [Resource](resource-group-template-functions-resource.md#resourcegroup) . V takovém případě získáte vlastnost **ID** místo vlastnosti **Location** , jak je znázorněno v předchozím kurzu. Vlastnost **ID** vrátí úplný identifikátor skupiny prostředků, včetně ID předplatného a názvu skupiny prostředků.

Funkce [uniqueString](resource-group-template-functions-string.md#uniquestring) vytvoří hodnotu hash znaku 13 znaků. Vrácená hodnota je určena parametry, které předáte. V tomto kurzu použijete ID skupiny prostředků jako vstup pro hodnotu hash. To znamená, že můžete tuto šablonu nasadit do různých skupin prostředků a získat jinou jedinečnou řetězcovou hodnotu. Pokud však nasadíte do stejné skupiny prostředků, získáte stejnou hodnotu.

Funkce [Concat](resource-group-template-functions-string.md#concat) přebírá hodnoty a kombinuje je. Pro tuto proměnnou přebírá řetězec z parametru a řetězce z funkce uniqueString a kombinuje je do jednoho řetězce.

Parametr **storagePrefix** vám umožní předat předponu, která vám pomůže identifikovat účty úložiště. Můžete vytvořit vlastní zásadu vytváření názvů, která usnadňuje identifikaci účtů úložiště po nasazení z dlouhého seznamu prostředků.

Nakonec si všimněte, že název úložiště je nyní nastaven na proměnnou namísto parametru.

## <a name="deploy-template"></a>Nasazení šablony

Pojďme šablonu nasadit. Nasazení této šablony je snazší než u předchozích šablon, protože zadáváte pouze předponu názvu úložiště.

Pokud jste ještě nevytvořili skupinu prostředků, přečtěte si téma [Vytvoření skupiny prostředků](template-tutorial-create-first-template.md#create-resource-group). V příkladu se předpokládá, že jste nastavili proměnnou **templateFile** na cestu k souboru šablony, jak je znázorněno v [prvním kurzu](template-tutorial-create-first-template.md#deploy-template).

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addnamevariable `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storagePrefix "store" `
  -storageSKU Standard_LRS
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az group deployment create \
  --name addnamevariable \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS
```

---

## <a name="verify-deployment"></a>Ověření nasazení

Nasazení můžete ověřit prozkoumáním skupiny prostředků z Azure Portal.

1. Přihlaste se na web [Azure Portal ](https://portal.azure.com).
1. V nabídce vlevo vyberte **skupiny prostředků**.
1. Vyberte skupinu prostředků, do které jste nasadili.
1. Vidíte, že byl nasazen prostředek účtu úložiště. Název účtu úložiště je **uložený** spolu s řetězcem náhodných znaků.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud se chystáte pokračovat k dalšímu kurzu, nemusíte odstranit skupinu prostředků.

Pokud nyní zastavíte, budete možná chtít vyčistit prostředky, které jste nasadili, odstraněním skupiny prostředků.

1. Na portálu Azure Portal vyberte v nabídce nalevo **Skupina prostředků**.
2. Do pole **Filtrovat podle názvu** zadejte název skupiny prostředků.
3. Vyberte název skupiny prostředků.
4. V nabídce nahoře vyberte **Odstranit skupinu prostředků**.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste přidali proměnnou, která pro účet úložiště vytvoří jedinečný název. V dalším kurzu vrátíte hodnotu z nasazeného účtu úložiště.

> [!div class="nextstepaction"]
> [Přidat výstupy](template-tutorial-add-outputs.md)
