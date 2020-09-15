---
title: Kurz – přidání funkcí šablon
description: Přidejte do šablony Azure Resource Manager funkce šablon k vytvoření hodnot.
author: mumian
ms.date: 03/27/2020
ms.topic: tutorial
ms.author: jgao
ms.custom: devx-track-azurecli
ms.openlocfilehash: 3c2fdfffb613b8da39c2f8dcd5504c9ce58c46e4
ms.sourcegitcommit: 1fe5127fb5c3f43761f479078251242ae5688386
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/14/2020
ms.locfileid: "90069454"
---
# <a name="tutorial-add-template-functions-to-your-arm-template"></a>Kurz: Přidání funkcí šablony do šablony ARM

V tomto kurzu se naučíte, jak přidat [funkce šablon](template-functions.md) do šablony Azure Resource Manager (ARM). Funkce slouží k dynamickému vytváření hodnot. Kromě těchto funkcí šablon poskytovaných systémem můžete také vytvořit [uživatelsky definované funkce](./template-user-defined-functions.md). Dokončení tohoto kurzu trvá **7 minut** .

## <a name="prerequisites"></a>Předpoklady

Doporučujeme, abyste dokončili [kurz týkající se parametrů](template-tutorial-add-parameters.md), ale není to nutné.

Musíte mít Visual Studio Code s rozšířením Správce prostředků Tools a buď Azure PowerShell, nebo v rozhraní příkazového řádku Azure. Další informace najdete v tématu [nástroje šablon](template-tutorial-create-first-template.md#get-tools).

## <a name="review-template"></a>Zkontrolovat šablonu

Na konci předchozího kurzu má vaše šablona následující JSON:

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-sku/azuredeploy.json":::

Umístění účtu úložiště je pevně zakódováno pro **východní USA**. Je ale možné, že budete muset účet úložiště nasadit do jiných oblastí. Opět jste se stali problémem vaší šablony, která nemá flexibilitu. Můžete přidat parametr pro umístění, ale bude skvělé, pokud jeho výchozí hodnota byla větší, než jenom pevně zakódovaná hodnota.

## <a name="use-function"></a>Použití funkce

Pokud jste dokončili předchozí kurz v této sérii, už jste použili funkci. Když jste přidali **[Parameters (' Storage ')]**, použili jste funkci [Parameters](template-functions-deployment.md#parameters) . Hranaté závorky označují, že syntaxe uvnitř závorek je [výraz šablony](template-expressions.md). Správce prostředků vyřeší syntax místo toho, aby ji znovu nakládala jako hodnota literálu.

Funkce přidávají vaší šabloně flexibilitu pomocí dynamického načítání hodnot během nasazování. V tomto kurzu použijete funkci k získání umístění skupiny prostředků, kterou používáte pro nasazení.

Následující příklad zvýrazní změny pro přidání parametru s názvem **Location**.  Výchozí hodnota parametru volá funkci [Resource](template-functions-resource.md#resourcegroup) . Tato funkce vrací objekt s informacemi o skupině prostředků, která se používá k nasazení. Jedna z vlastností objektu je vlastnost umístění. Když použijete výchozí hodnotu, umístění účtu úložiště má stejné umístění jako skupina prostředků. Prostředky ve skupině prostředků nemusí sdílet stejné umístění. V případě potřeby můžete také zadat jiné umístění.

Zkopírujte celý soubor a nahraďte šablonu jeho obsahem.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-location/azuredeploy.json" range="1-44" highlight="24-27,34":::

## <a name="deploy-template"></a>Nasazení šablony

V předchozích kurzech jste v Východní USA vytvořili účet úložiště, ale skupina prostředků se vytvořila v Střed USA. V tomto kurzu se účet úložiště vytvoří ve stejné oblasti jako skupina prostředků. Použijte výchozí hodnotu pro umístění, takže tuto hodnotu parametru nemusíte zadávat. Musíte zadat nový název účtu úložiště, protože vytváříte účet úložiště v jiném umístění. Použijte například **store2** jako předponu namísto **store1**.

Pokud jste ještě nevytvořili skupinu prostředků, přečtěte si téma [Vytvoření skupiny prostředků](template-tutorial-create-first-template.md#create-resource-group). V příkladu se předpokládá, že jste nastavili proměnnou **templateFile** na cestu k souboru šablony, jak je znázorněno v [prvním kurzu](template-tutorial-create-first-template.md#deploy-template).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addlocationparameter `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storageName "{new-unique-name}"
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Jestliže chcete spustit tento příkaz nasazení, musíte mít k dispozici [nejnovější verzi](/cli/azure/install-azure-cli) Azure CLI.

```azurecli
az deployment group create \
  --name addlocationparameter \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storageName={new-unique-name}
```

---

> [!NOTE]
> Pokud se nasazení nepovedlo, pomocí **podrobného** přepínače Získejte informace o vytvářených prostředcích. K získání dalších informací pro ladění použijte přepínač **ladění** .

## <a name="verify-deployment"></a>Ověření nasazení

Nasazení můžete ověřit prozkoumáním skupiny prostředků z Azure Portal.

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
1. V nabídce vlevo vyberte **skupiny prostředků**.
1. Vyberte skupinu prostředků, do které jste nasadili.
1. Vidíte, že je prostředek účtu úložiště nasazený a má stejné umístění jako skupina prostředků.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud se chystáte pokračovat k dalšímu kurzu, nemusíte odstranit skupinu prostředků.

Pokud nyní zastavíte, budete možná chtít vyčistit prostředky, které jste nasadili, odstraněním skupiny prostředků.

1. Z Azure Portal v nabídce vlevo vyberte **Skupina prostředků** .
2. Do pole **Filtrovat podle názvu** zadejte název skupiny prostředků.
3. Vyberte název skupiny prostředků.
4. V horní nabídce vyberte **Odstranit skupinu prostředků** .

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste použili funkci při definování výchozí hodnoty pro parametr. V této sérii kurzů budete dál používat funkce. Za konec série přidáte funkce do všech sekcí šablony.

> [!div class="nextstepaction"]
> [Přidání proměnných](template-tutorial-add-variables.md)
