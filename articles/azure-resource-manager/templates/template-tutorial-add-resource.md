---
title: Kurz – přidání zdroje do šablony
description: Popisuje postup vytvoření první šablony Azure Resource Manager. Dozvíte se o syntaxi souboru šablony a o tom, jak nasadit účet úložiště.
author: mumian
ms.date: 03/27/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 79e337b411f9d115d93050ebeee346a526913d39
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80371738"
---
# <a name="tutorial-add-a-resource-to-your-arm-template"></a>Kurz: Přidání zdroje do šablony ARM

V [předchozím kurzu](template-tutorial-create-first-template.md)jste se naučili, jak vytvořit prázdnou šablonu a nasadit ji. Nyní jste připraveni nasadit skutečný prostředek. V tomto kurzu přidáte účet úložiště. Dokončení tohoto kurzu trvá přibližně **9 minut.**

## <a name="prerequisites"></a>Požadavky

Doporučujeme dokončit [úvodní kurz o šablonách](template-tutorial-create-first-template.md), ale není to nutné.

Musíte mít kód Visual Studio s rozšířením Nástroje správce prostředků a buď Azure PowerShell nebo Azure CLI. Další informace naleznete v [tématu nástroje šablony](template-tutorial-create-first-template.md#get-tools).

## <a name="add-resource"></a>Přidání zdroje

Chcete-li přidat definici účtu úložiště do existující šablony, podívejte se na zvýrazněné JSON v následujícím příkladu. Místo pokusu o kopírování částí šablony zkopírujte celý soubor a nahraďte šablonu jejím obsahem.

Nahraďte **{provide-unique-name}** jedinečným názvem účtu úložiště.

> [!IMPORTANT]
> Název účtu úložiště musí být jedinečný v rámci Azure. Název musí mít pouze malá písmena nebo čísla. Nesmí být delší než 24 znaků. Můžete zkusit vzor pojmenování, jako je použití **store1** jako předponu a přidání iniciály a dnešní datum. Například název, který používáte může vypadat **store1abc09092019**.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-storage/azuredeploy.json" range="1-19" highlight="5-17":::

Hádání jedinečný název pro účet úložiště není snadné a nefunguje dobře pro automatizaci velkých nasazení. Později v této sérii kurzů budete používat funkce šablony, které usnadňují vytvoření jedinečného názvu.

## <a name="resource-properties"></a>Vlastnosti prostředků

Možná se divíte, jak najít vlastnosti, které mají být používány pro každý typ prostředku. Pomocí odkazu [na šablonu ARM](/azure/templates/) můžete najít typy prostředků, které chcete nasadit.

Každý prostředek, který nasadíte, má alespoň následující tři vlastnosti:

- **typ**: Typ zdroje. Tato hodnota je kombinací oboru názvů poskytovatele prostředků a typu prostředku (například Microsoft.Storage/storageAccounts).
- **apiVersion**: Verze rozhraní REST API pro vytvoření prostředku. Každý poskytovatel prostředků publikoval vlastní verze rozhraní API, takže tato hodnota je specifická pro typ.
- **název**: Název prostředku.

Většina prostředků má také vlastnost **umístění,** která nastavuje oblast, kde je prostředek nasazen.

Ostatní vlastnosti se liší podle typu prostředku a verze rozhraní API. Je důležité pochopit připojení mezi verzí rozhraní API a dostupnými vlastnostmi, takže pojďme přejít do podrobností.

V tomto kurzu jste do šablony přidali účet úložiště. Tuto verzi rozhraní API vidíte na [adrese storageAccounts 2019-04-01](/azure/templates/microsoft.storage/2019-04-01/storageaccounts). Všimněte si, že jste do šablony nepřidali všechny vlastnosti. Mnoho vlastností je volitelné. Poskytovatel prostředků Microsoft.Storage může vydat novou verzi rozhraní API, ale verze, kterou nasazujete, se nemusí měnit. Můžete pokračovat v používání této verze a vědět, že výsledky vašeho nasazení bude konzistentní.

Pokud se zobrazí starší verze rozhraní API, jako je [storageAccounts 2016-05-01](/azure/templates/microsoft.storage/2016-05-01/storageaccounts), uvidíte, že jsou k dispozici menší sada vlastností.

Pokud se rozhodnete změnit verzi rozhraní API pro prostředek, ujistěte se, že vyhodnotit vlastnosti pro tuto verzi a upravit šablonu odpovídajícím způsobem.

## <a name="deploy-template"></a>Nasazení šablony

Šablonu můžete nasadit k vytvoření účtu úložiště. Pojmenujte své nasazení jiným názvem, abyste ho mohli snadno najít v historii.

Pokud jste skupinu prostředků nevytvořili, přečtěte si informace [o vytvoření skupiny prostředků](template-tutorial-create-first-template.md#create-resource-group). Příklad předpokládá, že jste nastavili proměnnou **templateFile** na cestu k souboru šablony, jak je znázorněno v [prvním kurzu](template-tutorial-create-first-template.md#deploy-template).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addstorage `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Chcete-li spustit tento příkaz nasazení, musíte mít [nejnovější verzi](/cli/azure/install-azure-cli) azure cli.

```azurecli
az deployment group create \
  --name addstorage \
  --resource-group myResourceGroup \
  --template-file $templateFile
```

---

Dvě možné selhání nasazení, ke kterým může dojít:

- Chyba: Code=AccountNameInvalid; Message={provide-unique-name} není platný název účtu úložiště. Název účtu úložiště musí mít délku 3 až 24 znaků a používat pouze čísla a malá písmena.

    V šabloně nahraďte **{provide-unique-name}** jedinečným názvem účtu úložiště.  Viz [Přidání zdroje](#add-resource).

- Chyba: Code=StorageAccountAlreadyTaken; Message=Účet úložiště s názvem store1abc09092019 je již přijata.

    V šabloně zkuste jiný název účtu úložiště.

Toto nasazení trvá déle než vaše nasazení prázdné šablony, protože je vytvořen účet úložiště. To může trvat asi minutu, ale je obvykle rychlejší.

## <a name="verify-deployment"></a>Ověření nasazení

Nasazení můžete ověřit tak, že prozkoumáte skupinu prostředků z webu Azure Portal.

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
1. V levé nabídce vyberte **položku Skupiny prostředků**.
1. Vyberte skupinu prostředků, do které jste nasadili.
1. Uvidíte, že účet úložiště byl nasazen.
1. Všimněte si, že popisek nasazení nyní říká: **Nasazení: 2 Úspěšné**.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud přecházíte na další kurz, nemusíte odstraňovat skupinu prostředků.

Pokud nyní zastavujete, můžete chtít vyčistit prostředky, které jste nasadili, odstraněním skupiny prostředků.

1. Na portálu Azure vyberte **skupinu prostředků** z levé nabídky.
2. Do pole **Filtrovat podle názvu** zadejte název skupiny prostředků.
3. Vyberte název skupiny prostředků.
4. V horní nabídce vyberte **Odstranit skupinu prostředků.**

## <a name="next-steps"></a>Další kroky

Vytvořili jste jednoduchou šablonu pro nasazení účtu úložiště Azure.  V pozdějších kurzech se dozvíte, jak přidat parametry, proměnné, prostředky a výstupy do šablony. Tyto funkce jsou stavebními kameny pro mnohem složitější šablony.

> [!div class="nextstepaction"]
> [Přidat parametry](template-tutorial-add-parameters.md)