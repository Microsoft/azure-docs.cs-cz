---
title: Kurz – přidání funkcí šablony do šablony Azure Resource Manager
description: Přidejte do šablony Azure Resource Manager funkce šablon k vytvoření hodnot.
services: azure-resource-manager
author: mumian
manager: carmonmills
ms.service: azure-resource-manager
ms.date: 10/04/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: ca6e7f326ba67eac33e4dee5fa844d7676ab864b
ms.sourcegitcommit: 4d177e6d273bba8af03a00e8bb9fe51a447196d0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/04/2019
ms.locfileid: "71963958"
---
# <a name="tutorial-add-template-functions-to-your-resource-manager-template"></a>Kurz: Přidání funkcí šablony do šablony Správce prostředků

V tomto kurzu se naučíte, jak do šablony přidat [funkce šablon](resource-group-template-functions.md) . Funkce slouží k dynamickému vytváření hodnot. Kromě těchto funkcí šablon poskytovaných systémem můžete také vytvořit [uživatelsky definované funkce](./template-user-defined-functions.md). Dokončení tohoto kurzu trvá **7 minut** .

## <a name="prerequisites"></a>Požadavky

Doporučujeme, abyste dokončili [kurz týkající se parametrů](template-tutorial-add-parameters.md), ale není to nutné.

Musíte mít Visual Studio Code s rozšířením Správce prostředků Tools a buď Azure PowerShell, nebo v rozhraní příkazového řádku Azure. Další informace najdete v tématu [nástroje šablon](template-tutorial-create-first-template.md#get-tools).

## <a name="review-your-template"></a>Kontrola šablony

Na konci předchozího kurzu má vaše šablona následující JSON:

[!code-json[](~/resourcemanager-templates/get-started-with-templates/add-sku/azuredeploy.json)]

Umístění účtu úložiště je pevně zakódováno pro **východní USA**. Je ale možné, že budete muset účet úložiště nasadit do jiných oblastí. Opět jste se stali problémem vaší šablony, která nemá flexibilitu. Můžete přidat parametr pro umístění, ale bude skvělé, pokud jeho výchozí hodnota byla větší, než jenom pevně zakódovaná hodnota.

## <a name="use-function"></a>Použití funkce

Pokud jste dokončili předchozí kurz v této sérii, už jste použili funkci. Když jste přidali **[Parameters (' Storage ')]** , použili jste funkci [Parameters](resource-group-template-functions-deployment.md#parameters) . Hranaté závorky označují, že syntaxe uvnitř závorek je [výraz šablony](template-expressions.md). Správce prostředků vyřeší syntax místo toho, aby ji znovu nakládala jako hodnota literálu.

Funkce přidávají vaší šabloně flexibilitu pomocí dynamického načítání hodnot během nasazování. V tomto kurzu použijete funkci k získání umístění skupiny prostředků, kterou používáte pro nasazení.

Následující příklad zvýrazní změny pro přidání parametru s názvem **Location**.  Výchozí hodnota parametru volá funkci [Resource](resource-group-template-functions-resource.md#resourcegroup) . Tato funkce vrací objekt s informacemi o skupině prostředků, která se používá k nasazení. Jedna z vlastností objektu je vlastnost umístění. Když použijete výchozí hodnotu, umístění účtu úložiště má stejné umístění jako skupina prostředků. Prostředky ve skupině prostředků nemusí sdílet stejné umístění. V případě potřeby můžete také zadat jiné umístění.

Zkopírujte celý soubor a nahraďte šablonu jeho obsahem.

[!code-json[](~/resourcemanager-templates/get-started-with-templates/add-location/azuredeploy.json?range=1-44&highlight=24-27,34)]

## <a name="deploy-template"></a>Nasadit šablonu

V předchozích kurzech jste v Východní USA vytvořili účet úložiště, ale skupina prostředků se vytvořila v Střed USA. V tomto kurzu se účet úložiště vytvoří ve stejné oblasti jako skupina prostředků. Použijte výchozí hodnotu pro umístění, takže tuto hodnotu parametru nemusíte zadávat. Musíte zadat nový název účtu úložiště, protože vytváříte účet úložiště v jiném umístění. Použijte například **store2** jako předponu namísto **store1**.

Pokud jste ještě nevytvořili skupinu prostředků, přečtěte si téma [Vytvoření skupiny prostředků](template-tutorial-create-first-template.md#create-resource-group). V příkladu se předpokládá, že jste nastavili proměnnou **templateFile** na cestu k souboru šablony, jak je znázorněno v [prvním kurzu](template-tutorial-create-first-template.md#deploy-template).

# <a name="powershelltabazure-powershell"></a>[Prostředí](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addlocationparameter `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storageName "{new-unique-name}"
```

# <a name="azure-clitabazure-cli"></a>[Rozhraní příkazového řádku Azure](#tab/azure-cli)

```azurecli
az group deployment create \
  --name addlocationparameter \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storageName={new-unique-name}
```

---

## <a name="verify-the-deployment"></a>Ověření nasazení

Nasazení můžete ověřit prozkoumáním skupiny prostředků z Azure Portal.

1. Přihlaste se k [Azure Portal](https://portal.azure.com).
1. V nabídce vlevo vyberte **skupiny prostředků**.
1. Vyberte skupinu prostředků, do které jste nasadili.
1. Vidíte, že je prostředek účtu úložiště nasazený a má stejné umístění jako skupina prostředků.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud se chystáte pokračovat k dalšímu kurzu, nemusíte odstranit skupinu prostředků.

Pokud nyní zastavíte, budete možná chtít vyčistit prostředky, které jste nasadili, odstraněním skupiny prostředků.

1. Z Azure Portal v nabídce vlevo vyberte **Skupina prostředků** .
2. Do pole **filtrovat podle názvu** zadejte název skupiny prostředků.
3. Vyberte název skupiny prostředků.
4. V horní nabídce vyberte **Odstranit skupinu prostředků** .

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste použili funkci při definování výchozí hodnoty pro parametr. V této sérii kurzů budete dál používat funkce. Za konec série přidáte funkce do všech sekcí šablony.

> [!div class="nextstepaction"]
> [Přidat proměnné](template-tutorial-add-variables.md)
