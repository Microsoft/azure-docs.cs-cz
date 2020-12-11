---
title: Kurz – přidání prostředku do šablony
description: Popisuje kroky k vytvoření první šablony Azure Resource Manager (šablona ARM). Dozvíte se o syntaxi souboru šablony a o tom, jak nasadit účet úložiště.
author: mumian
ms.date: 03/27/2020
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: 49cee5c98c4099e214a732371269e935db353152
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/11/2020
ms.locfileid: "97106967"
---
# <a name="tutorial-add-a-resource-to-your-arm-template"></a>Kurz: Přidání prostředku do šablony ARM

V [předchozím kurzu](template-tutorial-create-first-template.md)jste zjistili, jak vytvořit prázdnou šablonu Azure Resource Manager (šablonu ARM) a nasadit ji. Teď můžete nasadit skutečný prostředek. V tomto kurzu přidáte účet úložiště. Dokončení tohoto kurzu trvá přibližně **9 minut** .

## <a name="prerequisites"></a>Předpoklady

Doporučujeme, abyste dokončili [Úvodní kurz týkající se šablon](template-tutorial-create-first-template.md), ale není to nutné.

Musíte mít Visual Studio Code s rozšířením Správce prostředků Tools a buď Azure PowerShell, nebo v rozhraní příkazového řádku Azure. Další informace najdete v tématu [nástroje šablon](template-tutorial-create-first-template.md#get-tools).

## <a name="add-resource"></a>Přidat prostředek

Pokud chcete přidat definici účtu úložiště do existující šablony, podívejte se na zvýrazněný kód JSON v následujícím příkladu. Místo kopírování oddílů šablony zkopírujte celý soubor a nahraďte šablonu jeho obsahem.

Nahraďte `{provide-unique-name}` a složené závorky `{}` jedinečným názvem účtu úložiště.

> [!IMPORTANT]
> Název účtu úložiště musí být jedinečný v rámci Azure. Název musí obsahovat jenom malá písmena nebo číslice. Nemůže být delší než 24 znaků. Můžete vyzkoušet způsob pojmenování, jako je například použití **store1** jako předpona a přidání vašich počátečních a dnešních dat. Název, který použijete, může například vypadat jako **store1abc09092019**.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-storage/azuredeploy.json" range="1-19" highlight="5-17":::

Odhadování jedinečného názvu účtu úložiště není jednoduché a nefunguje dobře pro automatizaci rozsáhlých nasazení. Později v této sérii kurzů budete používat funkce šablon, které usnadňují vytváření jedinečných názvů.

## <a name="resource-properties"></a>Vlastnosti prostředku

Možná vás zajímá, jak najít vlastnosti, které se mají použít pro každý typ prostředku. Pomocí [odkazu na šablonu ARM](/azure/templates/) můžete najít typy prostředků, které chcete nasadit.

Každý prostředek, který nasazujete, má alespoň následující tři vlastnosti:

- `type`: Typ prostředku. Tato hodnota je kombinací oboru názvů poskytovatele prostředků a typu prostředku, jako je například `Microsoft.Storage/storageAccounts` .
- `apiVersion`: Verze REST API, která se má použít pro vytvoření prostředku. Každý poskytovatel prostředků publikuje své vlastní verze rozhraní API, takže tato hodnota je specifická pro daný typ.
- `name`: Název prostředku.

Většina prostředků má také `location` vlastnost, která nastaví oblast, kde je prostředek nasazen.

Ostatní vlastnosti se liší podle typu prostředku a verze rozhraní API. Je důležité pochopit připojení mezi verzí rozhraní API a dostupnými vlastnostmi, takže se můžete pustit do více podrobností.

V tomto kurzu jste do šablony přidali účet úložiště. Tuto verzi rozhraní API si můžete prohlédnout v [storageAccounts 2019-04-01](/azure/templates/microsoft.storage/2019-04-01/storageaccounts). Všimněte si, že jste do šablony nepřidali všechny vlastnosti. Mnohé z vlastností jsou volitelné. `Microsoft.Storage`Poskytovatel prostředků může uvolnit novou verzi rozhraní API, ale verze, kterou nasazujete, se nemusí měnit. Tuto verzi můžete dál používat a ví, že výsledky nasazení budou konzistentní.

Pokud si zobrazíte starší verzi rozhraní API, například [storageAccounts 2016-05-01](/azure/templates/microsoft.storage/2016-05-01/storageaccounts), uvidíte, že je k dispozici menší sada vlastností.

Pokud se rozhodnete změnit verzi rozhraní API pro určitý prostředek, ujistěte se, že jste vyhodnotili vlastnosti této verze a odpovídajícím způsobem upravte šablonu.

## <a name="deploy-template"></a>Nasazení šablony

Šablonu můžete nasadit pro vytvoření účtu úložiště. Dejte vašemu nasazení jiný název, abyste ho mohli snadno najít v historii.

Pokud jste ještě nevytvořili skupinu prostředků, přečtěte si téma [Vytvoření skupiny prostředků](template-tutorial-create-first-template.md#create-resource-group). V příkladu se předpokládá, že jste nastavili `templateFile` proměnnou na cestu k souboru šablony, jak je znázorněno v [prvním kurzu](template-tutorial-create-first-template.md#deploy-template).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addstorage `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Jestliže chcete spustit tento příkaz nasazení, musíte mít k dispozici [nejnovější verzi](/cli/azure/install-azure-cli) Azure CLI.

```azurecli
az deployment group create \
  --name addstorage \
  --resource-group myResourceGroup \
  --template-file $templateFile
```

---

> [!NOTE]
> Pokud se nasazení nepovedlo, pomocí `verbose` přepínače Získejte informace o vytvářených prostředcích. K `debug` získání dalších informací pro ladění použijte přepínač.

Dvě možné chyby nasazení, se kterými se můžete setkat:

- `Error: Code=AccountNameInvalid; Message={provide-unique-name}` není platný název účtu úložiště. Název účtu úložiště musí být dlouhý 3 až 24 znaků a obsahovat jenom číslice a malá písmena.

    V šabloně nahraďte `{provide-unique-name}` jedinečným názvem účtu úložiště. Viz [Přidat prostředek](#add-resource).

- `Error: Code=StorageAccountAlreadyTaken; Message=The storage account named store1abc09092019` je již obsazeno.

    V šabloně zkuste použít jiný název účtu úložiště.

Toto nasazení trvá déle než při nasazení prázdné šablony, protože je vytvořený účet úložiště. Může to trvat přibližně minutu, ale obvykle je rychlejší.

## <a name="verify-deployment"></a>Ověření nasazení

Nasazení můžete ověřit prozkoumáním skupiny prostředků z Azure Portal.

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
1. V nabídce vlevo vyberte **skupiny prostředků**.
1. Vyberte skupinu prostředků, do které jste nasadili.
1. Vidíte, že je nasazený účet úložiště.
1. Všimněte si, že jmenovka nasazení teď říká: **nasazení: 2 bylo úspěšné**.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud se chystáte pokračovat k dalšímu kurzu, nemusíte odstranit skupinu prostředků.

Pokud nyní zastavíte, budete možná chtít vyčistit prostředky, které jste nasadili, odstraněním skupiny prostředků.

1. Z Azure Portal v nabídce vlevo vyberte **Skupina prostředků** .
2. Do pole **Filtrovat podle názvu** zadejte název skupiny prostředků.
3. Vyberte název skupiny prostředků.
4. V horní nabídce vyberte **Odstranit skupinu prostředků** .

## <a name="next-steps"></a>Další kroky

Vytvořili jste jednoduchou šablonu pro nasazení účtu úložiště Azure. V dalších kurzech se dozvíte, jak přidat parametry, proměnné, prostředky a výstupy do šablony. Tyto funkce jsou stavebními bloky pro mnohem složitější šablony.

> [!div class="nextstepaction"]
> [Přidání parametrů](template-tutorial-add-parameters.md)
