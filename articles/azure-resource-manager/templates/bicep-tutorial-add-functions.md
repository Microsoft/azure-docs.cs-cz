---
title: Kurz – přidání funkcí do Azure Resource Manager souborů bicep
description: Přidejte do souborů bicep funkce pro vytvoření hodnot.
author: mumian
ms.date: 03/10/2021
ms.topic: tutorial
ms.author: jgao
ms.custom: references_regions
ms.openlocfilehash: b909beb0cce9ad04ba00068ee25247520dcff47d
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/11/2021
ms.locfileid: "102633151"
---
# <a name="tutorial-add-functions-to-azure-resource-manager-bicep-file"></a>Kurz: Přidání funkcí do souboru Azure Resource Manager bicep

V tomto kurzu se naučíte přidávat [funkce šablon](template-functions.md) do souboru bicep. Funkce slouží k dynamickému vytváření hodnot. Kromě těchto funkcí šablon poskytovaných systémem můžete také vytvořit [uživatelsky definované funkce](./template-user-defined-functions.md). Dokončení tohoto kurzu trvá **7 minut** .

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="prerequisites"></a>Požadavky

Doporučujeme, abyste dokončili [kurz týkající se parametrů](bicep-tutorial-add-parameters.md), ale není to nutné.

Musíte mít Visual Studio Code s rozšířením bicep a buď Azure PowerShell nebo Azure CLI. Další informace najdete v tématu [bicep Tools](bicep-tutorial-create-first-bicep.md#get-tools).

## <a name="review-bicep-file"></a>Zkontrolovat soubor bicep

Na konci předchozího kurzu měl váš soubor bicep následující obsah:

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/add-sku/azuredeploy.bicep":::

Umístění účtu úložiště je pevně zakódováno pro **východní USA**. Je ale možné, že budete muset účet úložiště nasadit do jiných oblastí. Znovu jste se stali problémem vašeho souboru bicep, který nemá flexibilní flexibilitu. Můžete přidat parametr pro umístění, ale bude skvělé, pokud jeho výchozí hodnota byla větší, než jenom pevně zakódovaná hodnota.

## <a name="use-function"></a>Použití funkce

Funkce přidávají k souboru bicep flexibilitu pomocí dynamického získávání hodnot během nasazování. V tomto kurzu použijete funkci k získání umístění skupiny prostředků, kterou používáte pro nasazení.

Následující příklad ukazuje změny pro přidání parametru s názvem `location` . Výchozí hodnota parametru volá funkci [Resource](template-functions-resource.md#resourcegroup) . Tato funkce vrací objekt s informacemi o skupině prostředků, která se používá k nasazení. Jedna z vlastností objektu je vlastnost umístění. Když použijete výchozí hodnotu, umístění účtu úložiště má stejné umístění jako skupina prostředků. Prostředky ve skupině prostředků nemusí sdílet stejné umístění. V případě potřeby můžete také zadat jiné umístění.

Zkopírujte celý soubor a nahraďte soubor bicep jeho obsahem.

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/add-location/azuredeploy.bicep" range="1-30" highlight="18,22":::

## <a name="deploy-bicep-file"></a>Nasadit soubor bicep

V předchozích kurzech jste v Východní USA vytvořili účet úložiště, ale skupina prostředků se vytvořila v Střed USA. V tomto kurzu se účet úložiště vytvoří ve stejné oblasti jako skupina prostředků. Použijte výchozí hodnotu pro umístění, takže tuto hodnotu parametru nemusíte zadávat. Musíte zadat nový název účtu úložiště, protože vytváříte účet úložiště v jiném umístění. Použijte například **store2** jako předponu namísto **store1**.

Pokud jste ještě nevytvořili skupinu prostředků, přečtěte si téma [Vytvoření skupiny prostředků](bicep-tutorial-create-first-bicep.md#create-resource-group). V příkladu se předpokládá, že jste nastavili `bicepFile` proměnnou na cestu k souboru bicep, jak je znázorněno v [prvním kurzu](bicep-tutorial-create-first-bicep.md#deploy-bicep-file).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Chcete-li spustit tuto rutinu nasazení, je nutné mít [nejnovější verzi](/powershell/azure/install-az-ps) Azure PowerShell.

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addlocationparameter `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $bicepFile `
  -storageName "{new-unique-name}"
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Jestliže chcete spustit tento příkaz nasazení, musíte mít k dispozici [nejnovější verzi](/cli/azure/install-azure-cli) Azure CLI.

```azurecli
az deployment group create \
  --name addlocationparameter \
  --resource-group myResourceGroup \
  --template-file $bicepFile \
  --parameters storageName={new-unique-name}
```

---

> [!NOTE]
> Pokud se nasazení nepovedlo, pomocí `verbose` přepínače Získejte informace o vytvářených prostředcích. K `debug` získání dalších informací pro ladění použijte přepínač.

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

V tomto kurzu jste použili funkci při definování výchozí hodnoty pro parametr. V této sérii kurzů budete dál používat funkce. Za konec série přidáte funkce do všech sekcí souboru bicep.

> [!div class="nextstepaction"]
> [Přidání proměnných](bicep-tutorial-add-variables.md)
