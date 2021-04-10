---
title: Kurz – přidání parametrů do souboru Azure Resource Manager bicep
description: Přidejte do souboru bicep parametry, abyste ho mohli znovu použít.
author: mumian
ms.date: 03/10/2021
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 00df2ffc6272011127c5a1eb0c1e302011f8de5f
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "102632777"
---
# <a name="tutorial-add-parameters-to-azure-resource-manager-bicep-file"></a>Kurz: Přidání parametrů do souboru Azure Resource Manager bicep

V [předchozím kurzu](bicep-tutorial-create-first-bicep.md)jste zjistili, jak nasadit účet úložiště. V tomto kurzu se naučíte, jak vylepšit soubor bicep přidáním parametrů. Dokončení tohoto kurzu trvá přibližně **14 minut** .

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="prerequisites"></a>Požadavky

Doporučujeme, abyste dokončili [Vytvoření prvního souboru bicep](bicep-tutorial-create-first-bicep.md), ale není to nutné.

Musíte mít Visual Studio Code s rozšířením bicep a buď Azure PowerShell nebo Azure CLI. Další informace najdete v tématu [bicep Tools](bicep-tutorial-create-first-bicep.md#get-tools).

## <a name="review-bicep-file"></a>Zkontrolovat soubor bicep

Na konci předchozího kurzu vaše bicep vypadá takto:

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/add-storage/azuredeploy.bicep":::

Možná jste si všimli, že došlo k potížím s tímto souborem bicep. Název účtu úložiště je pevně zakódovaný. Tento soubor bicep můžete použít jenom k nasazení stejného účtu úložiště pokaždé, když. Pokud chcete nasadit účet úložiště s jiným názvem, budete muset vytvořit nový soubor bicep, který zjevně nepředstavuje praktický způsob, jak automatizovat nasazení.

## <a name="make-bicep-file-reusable"></a>Nastavit soubor bicep jako opakovaně použitelný

Aby bylo možné soubor bicep opakovaně použít, přidejte parametr, který můžete použít k předání názvu účtu úložiště. Následující soubor bicep ukazuje, co se změnilo v souboru. `storageName`Parametr je identifikován jako řetězec. Maximální délka je nastavená na 24 znaků, aby nedocházelo k příliš dlouhým názvům.

Zkopírujte celý soubor a nahraďte ho následujícím obsahem.

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/add-name/azuredeploy.bicep" range="1-15" highlight="1-3,6":::

Všimněte si, že parametry lze odkazovat přímo pomocí jejich názvů v bicep ve srovnání s vyžádáním [Parameters (' Storage ')] v šabloně JSON ARM.

## <a name="deploy-bicep-file"></a>Nasadit soubor bicep

Pojďme nasadit soubor bicep. Následující příklad nasadí soubor bicep pomocí Azure CLI nebo PowerShellu. Všimněte si, že jako jednu z hodnot v příkazu nasazení zadáte název účtu úložiště. Jako název účtu úložiště zadejte stejný název, který jste použili v předchozím kurzu.

Pokud jste ještě nevytvořili skupinu prostředků, přečtěte si téma [Vytvoření skupiny prostředků](bicep-tutorial-create-first-bicep.md#create-resource-group). V příkladu se předpokládá, že jste nastavili `bicepFile` proměnnou na cestu k souboru bicep, jak je znázorněno v [prvním kurzu](bicep-tutorial-create-first-bicep.md#deploy-bicep-file).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Chcete-li spustit tuto rutinu nasazení, je nutné mít [nejnovější verzi](/powershell/azure/install-az-ps) Azure PowerShell.

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addnameparameter `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $bicepFile `
  -storageName "{your-unique-name}"
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Jestliže chcete spustit tento příkaz nasazení, musíte mít k dispozici [nejnovější verzi](/cli/azure/install-azure-cli) Azure CLI.

```azurecli
az deployment group create \
  --name addnameparameter \
  --resource-group myResourceGroup \
  --template-file $bicepFile \
  --parameters storageName={your-unique-name}
```

---

## <a name="understand-resource-updates"></a>Principy aktualizací prostředků

V předchozí části jste nasadili účet úložiště se stejným názvem, který jste vytvořili dříve. Možná vás zajímá, jak je prostředek ovlivněný znovu nasazením.

Pokud prostředek už existuje a ve vlastnostech se nezjistí žádná změna, neprovede se žádná akce. Jestliže prostředek už existuje a vlastnost se změnila, prostředek se aktualizuje. V případě, že prostředek neexistuje, vytvoří se.

Tato možnost manipulace s aktualizacemi znamená, že váš soubor bicep může zahrnovat všechny prostředky, které potřebujete pro řešení Azure. Soubor bicep můžete bezpečně znovu nasadit a zjistit, že se prostředky v případě potřeby změnily nebo vytvářejí. Pokud jste například přidali soubory do svého účtu úložiště, můžete znovu nasadit účet úložiště bez ztráty těchto souborů.

## <a name="customize-by-environment"></a>Přizpůsobit podle prostředí

Parametry umožňují přizpůsobit nasazení tím, že zadáte hodnoty vhodné pro konkrétní prostředí. Můžete například předat různé hodnoty na základě toho, jestli nasazujete do prostředí pro vývoj, testování a produkci.

Předchozí soubor bicep vždycky nasadil účet úložiště **Standard_LRS** . Možná budete chtít, aby flexibilita nasadila různé SKU v závislosti na prostředí. Následující příklad ukazuje změny pro přidání parametru pro SKLADOVOU položku. Zkopírujte celý soubor a vložte ho do souboru bicep.

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/add-sku/azuredeploy.bicep" range="1-27" highlight="5-15,21":::

`storageSKU`Parametr má výchozí hodnotu. Tato hodnota se používá v případě, že při nasazení není zadána hodnota. Obsahuje také seznam povolených hodnot. Tyto hodnoty odpovídají hodnotám, které jsou potřeba k vytvoření účtu úložiště. Nechcete, aby uživatelé souboru bicep předávali v SKU, které nefungují.

## <a name="redeploy-bicep-file"></a>Znovu nasadit soubor bicep

Jste připraveni znovu nasadit. Vzhledem k tomu, že výchozí SKU je nastavená na **Standard_LRS**, nemusíte zadávat hodnotu pro tento parametr.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addskuparameter `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $bicepFile `
  -storageName "{your-unique-name}"
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az deployment group create \
  --name addskuparameter \
  --resource-group myResourceGroup \
  --template-file $bicepFile \
  --parameters storageName={your-unique-name}
```

---

> [!NOTE]
> Pokud se nasazení nepovedlo, pomocí `verbose` přepínače Získejte informace o vytvářených prostředcích. K `debug` získání dalších informací pro ladění použijte přepínač.

Pro zobrazení flexibility souboru bicep se znovu nasaďte. Tentokrát nastavte parametr SKU na **Standard_GRS**. Můžete buď předat nový název a vytvořit jiný účet úložiště, nebo použít stejný název k aktualizaci stávajícího účtu úložiště. Obě možnosti fungují.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name usenondefaultsku `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $bicepFile `
  -storageName "{your-unique-name}" `
  -storageSKU Standard_GRS
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az deployment group create \
  --name usenondefaultsku \
  --resource-group myResourceGroup \
  --template-file $bicepFile \
  --parameters storageSKU=Standard_GRS storageName={your-unique-name}
```

---

Nakonec spustíme ještě jeden test a zjistíte, co se stane, když předáte SKU, které není jednou z povolených hodnot. V takovém případě testujeme scénář, ve kterém si uživatel vašeho souboru bicep myslí, že **se jedná o** jednu z SKU.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name testskuparameter `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $bicepFile `
  -storageName "{your-unique-name}" `
  -storageSKU basic
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az deployment group create \
  --name testskuparameter \
  --resource-group myResourceGroup \
  --template-file $bicepFile \
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

Vylepšili jste soubor bicep vytvořený v [prvním kurzu](bicep-tutorial-create-first-bicep.md) přidáním parametrů. V dalším kurzu se dozvíte o funkcích bicep.

> [!div class="nextstepaction"]
> [Přidat funkce](bicep-tutorial-add-functions.md)
