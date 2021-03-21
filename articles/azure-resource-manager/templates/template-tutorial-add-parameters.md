---
title: Kurz – přidání parametrů do šablony
description: Přidejte parametry do šablony Azure Resource Manager (šablona ARM), abyste je mohli opakovaně používat.
author: mumian
ms.date: 03/31/2020
ms.topic: tutorial
ms.author: jgao
ms.custom: devx-track-azurecli
ms.openlocfilehash: e983f8499cbeaf400a8da6f48d7f6c8b75c4795a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "97107058"
---
# <a name="tutorial-add-parameters-to-your-arm-template"></a>Kurz: Přidání parametrů do šablony ARM

V [předchozím kurzu](template-tutorial-add-resource.md)jste zjistili, jak do šablony přidat účet úložiště a nasadit ho. V tomto kurzu se naučíte, jak vylepšit šablonu Azure Resource Manager (šablonu ARM) přidáním parametrů. Dokončení tohoto kurzu trvá přibližně **14 minut** .

## <a name="prerequisites"></a>Předpoklady

Doporučujeme, abyste dokončili [kurz o prostředcích](template-tutorial-add-resource.md), ale není to nutné.

Musíte mít Visual Studio Code s rozšířením Správce prostředků Tools a buď Azure PowerShell, nebo v rozhraní příkazového řádku Azure. Další informace najdete v tématu [nástroje šablon](template-tutorial-create-first-template.md#get-tools).

## <a name="review-template"></a>Zkontrolovat šablonu

Na konci předchozího kurzu má vaše šablona následující JSON:

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-storage/azuredeploy.json":::

Možná jste si všimli, že došlo k potížím s touto šablonou. Název účtu úložiště je pevně zakódovaný. Danou šablonu proto můžete použít jen k nasazení stejného účtu úložiště. Chcete-li nasadit účet úložiště s jiným názvem, bude nutné vytvořit novou šablonu, což zjevně není praktický způsob, jak automatizovat nasazení.

## <a name="make-template-reusable"></a>Nastavit šablonu jako znovu

Aby bylo možné šablonu znovu použít, přidejte parametr, který můžete použít k předání názvu účtu úložiště. Zvýrazněný kód JSON v následujícím příkladu ukazuje, co se změnilo ve vaší šabloně. `storageName`Parametr je identifikován jako řetězec. Maximální délka je nastavená na 24 znaků, aby nedocházelo k příliš dlouhým názvům.

Zkopírujte celý soubor a nahraďte šablonu jeho obsahem.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-name/azuredeploy.json" range="1-26" highlight="4-10,15":::

## <a name="deploy-template"></a>Nasazení šablony

Pojďme šablonu nasadit. Následující příklad nasadí šablonu pomocí Azure CLI nebo PowerShellu. Všimněte si, že jako jednu z hodnot v příkazu nasazení zadáte název účtu úložiště. Jako název účtu úložiště zadejte stejný název, který jste použili v předchozím kurzu.

Pokud jste ještě nevytvořili skupinu prostředků, přečtěte si téma [Vytvoření skupiny prostředků](template-tutorial-create-first-template.md#create-resource-group). V příkladu se předpokládá, že jste nastavili `templateFile` proměnnou na cestu k souboru šablony, jak je znázorněno v [prvním kurzu](template-tutorial-create-first-template.md#deploy-template).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addnameparameter `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storageName "{your-unique-name}"
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Jestliže chcete spustit tento příkaz nasazení, musíte mít k dispozici [nejnovější verzi](/cli/azure/install-azure-cli) Azure CLI.

```azurecli
az deployment group create \
  --name addnameparameter \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storageName={your-unique-name}
```

---

## <a name="understand-resource-updates"></a>Principy aktualizací prostředků

V předchozí části jste nasadili účet úložiště se stejným názvem, který jste vytvořili dříve. Možná vás zajímá, jak je prostředek ovlivněný znovu nasazením.

Pokud prostředek už existuje a ve vlastnostech se nezjistí žádná změna, neprovede se žádná akce. Jestliže prostředek už existuje a vlastnost se změnila, prostředek se aktualizuje. V případě, že prostředek neexistuje, vytvoří se.

Tato možnost manipulace s aktualizacemi znamená, že vaše šablona může zahrnovat všechny prostředky, které potřebujete pro řešení Azure. Šablonu můžete bezpečně znovu nasadit a zjistit, jestli se prostředky v případě potřeby změnily nebo vytvářejí. Pokud jste například přidali soubory do svého účtu úložiště, můžete znovu nasadit účet úložiště bez ztráty těchto souborů.

## <a name="customize-by-environment"></a>Přizpůsobit podle prostředí

Parametry umožňují přizpůsobit nasazení tím, že zadáte hodnoty vhodné pro konkrétní prostředí. Můžete například předat různé hodnoty na základě toho, jestli nasazujete do prostředí pro vývoj, testování a produkci.

Předchozí šablona vždycky nasadila **Standard_LRS** účet úložiště. Možná budete chtít, aby flexibilita nasadila různé SKU v závislosti na prostředí. Následující příklad ukazuje změny pro přidání parametru pro SKLADOVOU položku. Zkopírujte celý soubor a vložte ho do šablony.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-sku/azuredeploy.json" range="1-40" highlight="10-23,32":::

`storageSKU`Parametr má výchozí hodnotu. Tato hodnota se používá v případě, že při nasazení není zadána hodnota. Obsahuje také seznam povolených hodnot. Tyto hodnoty odpovídají hodnotám, které jsou potřeba k vytvoření účtu úložiště. Nechcete, aby uživatelé šablony předávali do SKU, které nefungují.

## <a name="redeploy-template"></a>Opětovné nasazení šablony

Jste připraveni znovu nasadit. Vzhledem k tomu, že výchozí SKU je nastavená na **Standard_LRS**, nemusíte zadávat hodnotu pro tento parametr.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addskuparameter `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storageName "{your-unique-name}"
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az deployment group create \
  --name addskuparameter \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storageName={your-unique-name}
```

---

> [!NOTE]
> Pokud se nasazení nepovedlo, pomocí `verbose` přepínače Získejte informace o vytvářených prostředcích. K `debug` získání dalších informací pro ladění použijte přepínač.

Chcete-li zobrazit flexibilitu šablony, nasaďte se znovu. Tentokrát nastavte parametr SKU na **Standard_GRS**. Můžete buď předat nový název a vytvořit jiný účet úložiště, nebo použít stejný název k aktualizaci stávajícího účtu úložiště. Obě možnosti fungují.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name usenondefaultsku `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storageName "{your-unique-name}" `
  -storageSKU Standard_GRS
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az deployment group create \
  --name usenondefaultsku \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storageSKU=Standard_GRS storageName={your-unique-name}
```

---

Nakonec spustíme ještě jeden test a zjistíte, co se stane, když předáte SKU, které není jednou z povolených hodnot. V tomto případě testujeme scénář, ve kterém bude uživatel vaší šablony považovat za **základ** jednu z SKU.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name testskuparameter `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storageName "{your-unique-name}" `
  -storageSKU basic
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az deployment group create \
  --name testskuparameter \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storageSKU=basic storageName={your-unique-name}
```

---

Příkaz se okamžitě nezdařil s chybovou zprávou, která uvádí, které hodnoty jsou povoleny. Správce prostředků identifikuje chybu před spuštěním nasazení.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud se chystáte pokračovat k dalšímu kurzu, nemusíte odstranit skupinu prostředků.

Pokud nyní zastavíte, budete možná chtít vyčistit prostředky, které jste nasadili, odstraněním skupiny prostředků.

1. Z Azure Portal v nabídce vlevo vyberte **Skupina prostředků** .
2. Do pole **Filtrovat podle názvu** zadejte název skupiny prostředků.
3. Vyberte název skupiny prostředků.
4. V horní nabídce vyberte **Odstranit skupinu prostředků** .

## <a name="next-steps"></a>Další kroky

Vylepšili jste šablonu vytvořenou v [prvním kurzu](template-tutorial-create-first-template.md) přidáním parametrů. V dalším kurzu se dozvíte o funkcích šablon.

> [!div class="nextstepaction"]
> [Přidat funkce šablony](template-tutorial-add-functions.md)
