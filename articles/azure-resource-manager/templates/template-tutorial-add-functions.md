---
title: Výuka - přidání funkcí šablony
description: Přidejte do šablony Azure Resource Manageru funkce šablony a vytvořte hodnoty.
author: mumian
ms.date: 03/27/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 6bb95d5a8022c6ac94dd8b7c777c65ff5e20617a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80369919"
---
# <a name="tutorial-add-template-functions-to-your-arm-template"></a>Kurz: Přidání funkcí šablony do šablony ARM

V tomto kurzu se dozvíte, jak přidat [funkce šablony](template-functions.md) do šablony Správce prostředků Azure (ARM). Funkce se používají k dynamickému vytváření hodnot. Kromě těchto funkcí šablony poskytované systémem můžete také vytvářet [uživatelem definované funkce](./template-user-defined-functions.md). Tento kurz trvá **7 minut.**

## <a name="prerequisites"></a>Požadavky

Doporučujeme dokončit [kurz o parametrech](template-tutorial-add-parameters.md), ale není to nutné.

Musíte mít kód Visual Studio s rozšířením Nástroje správce prostředků a buď Azure PowerShell nebo Azure CLI. Další informace naleznete v [tématu nástroje šablony](template-tutorial-create-first-template.md#get-tools).

## <a name="review-template"></a>Šablona revize

Na konci předchozího kurzu měla vaše šablona následující JSON:

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-sku/azuredeploy.json":::

Umístění účtu úložiště je pevně zakódováno do **usa – východ**. Však může být nutné nasadit účet úložiště do jiných oblastí. Opět čelíte problému šablony, která postrádá flexibilitu. Můžete přidat parametr pro umístění, ale bylo by skvělé, kdyby jeho výchozí hodnota dávala větší smysl než jen pevně zakódovaná hodnota.

## <a name="use-function"></a>Použít funkci

Pokud jste dokončili předchozí kurz v této sérii, jste již použili funkci. Když jste přidali **"[parameters('storageName')]"**, použili jste funkci [parameters.](template-functions-deployment.md#parameters) Závorky označují, že syntaxe uvnitř závorek je [výraz šablony](template-expressions.md). Správce prostředků vyřeší syntaxi, nikoli ji považuje za hodnotu literálu.

Funkce přidávají šabloně flexibilitu dynamickým získáváním hodnot během nasazení. V tomto kurzu pomocí funkce získat umístění skupiny prostředků, které používáte pro nasazení.

Následující příklad zvýrazní změny pro přidání parametru nazývaného **umístění**.  Výchozí hodnota parametru volá funkci [resourceGroup.](template-functions-resource.md#resourcegroup) Tato funkce vrátí objekt s informacemi o skupině prostředků používané pro nasazení. Jednou z vlastností objektu je vlastnost umístění. Při použití výchozí hodnoty má umístění účtu úložiště stejné umístění jako skupina prostředků. Prostředky uvnitř skupiny prostředků nemusí sdílet stejné umístění. V případě potřeby můžete také zadat jiné umístění.

Zkopírujte celý soubor a nahraďte šablonu jeho obsahem.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-location/azuredeploy.json" range="1-44" highlight="24-27,34":::

## <a name="deploy-template"></a>Nasazení šablony

V předchozích kurzech jste vytvořili účet úložiště v USA – východ, ale vaše skupina prostředků byla vytvořena ve střední části USA. V tomto kurzu je váš účet úložiště vytvořen ve stejné oblasti jako skupina prostředků. Pro umístění použijte výchozí hodnotu, takže tuto hodnotu parametru nemusíte zajišťovat. Pro účet úložiště musíte zadat nový název, protože vytváříte účet úložiště v jiném umístění. Například použijte **store2** jako předponu namísto **store1**.

Pokud jste skupinu prostředků nevytvořili, přečtěte si informace [o vytvoření skupiny prostředků](template-tutorial-create-first-template.md#create-resource-group). Příklad předpokládá, že jste nastavili proměnnou **templateFile** na cestu k souboru šablony, jak je znázorněno v [prvním kurzu](template-tutorial-create-first-template.md#deploy-template).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addlocationparameter `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storageName "{new-unique-name}"
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Chcete-li spustit tento příkaz nasazení, musíte mít [nejnovější verzi](/cli/azure/install-azure-cli) azure cli.

```azurecli
az deployment group create \
  --name addlocationparameter \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storageName={new-unique-name}
```

---

## <a name="verify-deployment"></a>Ověření nasazení

Nasazení můžete ověřit tak, že prozkoumáte skupinu prostředků z webu Azure Portal.

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
1. V levé nabídce vyberte **položku Skupiny prostředků**.
1. Vyberte skupinu prostředků, do které jste nasadili.
1. Uvidíte, že prostředek účtu úložiště byl nasazen a má stejné umístění jako skupina prostředků.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud přecházíte na další kurz, nemusíte odstraňovat skupinu prostředků.

Pokud nyní zastavujete, můžete chtít vyčistit prostředky, které jste nasadili, odstraněním skupiny prostředků.

1. Na portálu Azure vyberte **skupinu prostředků** z levé nabídky.
2. Do pole **Filtrovat podle názvu** zadejte název skupiny prostředků.
3. Vyberte název skupiny prostředků.
4. V horní nabídce vyberte **Odstranit skupinu prostředků.**

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste použili funkci při definování výchozí hodnoty parametru. V této sérii kurzů budete pokračovat v používání funkcí. Na konci série přidáte funkce do každé části šablony.

> [!div class="nextstepaction"]
> [Přidání proměnných](template-tutorial-add-variables.md)
