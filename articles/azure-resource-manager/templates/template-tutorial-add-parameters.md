---
title: Výuka - přidání parametrů do šablony
description: Přidejte parametry do šablony Azure Resource Manager, aby byla opakovaně použitelná.
author: mumian
ms.date: 03/27/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: b4f8d5098fc9cf2f91139979cae430594edac5af
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80369871"
---
# <a name="tutorial-add-parameters-to-your-arm-template"></a>Kurz: Přidání parametrů do šablony ARM

V [předchozím kurzu](template-tutorial-add-resource.md)jste se naučili, jak přidat účet úložiště do šablony a nasadit ho. V tomto kurzu se dozvíte, jak vylepšit šablonu Azure Resource Manager (ARM) přidáním parametrů. Tento kurz trvá přibližně **14 minut.**

## <a name="prerequisites"></a>Požadavky

Doporučujeme dokončit [výukový program o zdrojích](template-tutorial-add-resource.md), ale není to nutné.

Musíte mít kód Visual Studio s rozšířením Nástroje správce prostředků a buď Azure PowerShell nebo Azure CLI. Další informace naleznete v [tématu nástroje šablony](template-tutorial-create-first-template.md#get-tools).

## <a name="review-template"></a>Šablona revize

Na konci předchozího kurzu měla vaše šablona následující JSON:

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-storage/azuredeploy.json":::

Možná jste si všimli, že došlo k potížím s touto šablonou. Název účtu úložiště je pevně zakódovaný. Tuto šablonu můžete použít pouze k nasazení stejného účtu úložiště pokaždé. Chcete-li nasadit účet úložiště s jiným názvem, budete muset vytvořit novou šablonu, což samozřejmě není praktický způsob, jak automatizovat vaše nasazení.

## <a name="make-template-reusable"></a>Nastavení šablony opakovaně použitelné

Chcete-li šablonu znovu použít, přidejte parametr, který můžete použít k předání názvu účtu úložiště. Zvýrazněné JSON v následujícím příkladu ukazuje, co se změnilo v šabloně. Parametr **storageName** je identifikován jako řetězec. Maximální délka je nastavena na 24 znaků, aby se zabránilo všechny názvy, které jsou příliš dlouhé.

Zkopírujte celý soubor a nahraďte šablonu jeho obsahem.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-name/azuredeploy.json" range="1-26" highlight="4-10,15":::

## <a name="deploy-template"></a>Nasazení šablony

Pojďme nasadit šablonu. Následující příklad nasadí šablonu pomocí Azure CLI nebo PowerShellu. Všimněte si, že zadáte název účtu úložiště jako jednu z hodnot v příkazu nasazení. Pro název účtu úložiště zadejte stejný název, který jste použili v předchozím kurzu.

Pokud jste skupinu prostředků nevytvořili, přečtěte si informace [o vytvoření skupiny prostředků](template-tutorial-create-first-template.md#create-resource-group). Příklad předpokládá, že jste nastavili proměnnou **templateFile** na cestu k souboru šablony, jak je znázorněno v [prvním kurzu](template-tutorial-create-first-template.md#deploy-template).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addnameparameter `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storageName "{your-unique-name}"
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Chcete-li spustit tento příkaz nasazení, musíte mít [nejnovější verzi](/cli/azure/install-azure-cli) azure cli.

```azurecli
az deployment group create \
  --name addnameparameter \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storageName={your-unique-name}
```

---

## <a name="understand-resource-updates"></a>Principy aktualizací prostředků

V předchozí části jste nasadili účet úložiště se stejným názvem, který jste vytvořili dříve. Možná se divíte, jak je prostředek ovlivněn přeřazením.

Pokud prostředek již existuje a ve vlastnostech není zjištěna žádná změna, nebude provedena žádná akce. Pokud prostředek již existuje a vlastnost se změnila, bude aktualizován. Pokud prostředek neexistuje, je vytvořen.

Tento způsob zpracování aktualizací znamená, že vaše šablona může obsahovat všechny prostředky, které potřebujete pro řešení Azure. Šablonu můžete bezpečně znovu nasadit a vědět, že prostředky jsou změněny nebo vytvořeny pouze v případě potřeby. Pokud jste například přidali soubory do účtu úložiště, můžete účet úložiště znovu nasadit, aniž byste tyto soubory ztratili.

## <a name="customize-by-environment"></a>Přizpůsobení podle prostředí

Parametry umožňují přizpůsobit nasazení tím, že zadáte hodnoty vhodné pro konkrétní prostředí. Můžete například předat různé hodnoty na základě toho, zda nasazujete do prostředí pro vývoj, testování a výrobu.

Předchozí šablona vždy nasadila Standard_LRS účet úložiště. Můžete chtít flexibilitu k nasazení různých skum v závislosti na prostředí. Následující příklad ukazuje změny přidat parametr pro skladovou položku. Zkopírujte celý soubor a vložte přes šablonu.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-sku/azuredeploy.json" range="1-40" highlight="10-23,32":::

Parametr **storageSKU** má výchozí hodnotu. Tato hodnota se používá, když hodnota není zadánběhem nasazení. Má také seznam povolených hodnot. Tyto hodnoty odpovídají hodnotám, které jsou potřebné k vytvoření účtu úložiště. Nechcete, aby uživatelé šablony předávat ve skum, které nefungují.

## <a name="redeploy-template"></a>Opětovné nasazení šablony

Jste připraveni k nasazení znovu. Vzhledem k tomu, že výchozí skladová položka je nastavena na **Standard_LRS**, není nutné zadat hodnotu pro tento parametr.

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

Chcete-li zobrazit flexibilitu šablony, pojďme nasadit znovu. Tento čas nastavit skladovou položku parametr **Standard_GRS**. Můžete buď předat nový název a vytvořit jiný účet úložiště, nebo použít stejný název k aktualizaci stávajícího účtu úložiště. Obě možnosti fungují.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name usedefaultsku `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storageName "{your-unique-name}" `
  -storageSKU Standard_GRS
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az deployment group create \
  --name usedefaultsku \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storageSKU=Standard_GRS storageName={your-unique-name}
```

---

Nakonec spusťme ještě jeden test a uvidíme, co se stane, když předáte skladovou položku, která není jednou z povolených hodnot. V takovém případě otestujeme scénář, kdy si uživatel šablony myslí, že **základní** je jedním z skum.

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

Příkaz selže okamžitě s chybovou zprávou, která uvádí, které hodnoty jsou povoleny. Správce prostředků identifikuje chybu před zahájením nasazení.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud přecházíte na další kurz, nemusíte odstraňovat skupinu prostředků.

Pokud nyní zastavujete, můžete chtít vyčistit prostředky, které jste nasadili, odstraněním skupiny prostředků.

1. Na portálu Azure vyberte **skupinu prostředků** z levé nabídky.
2. Do pole **Filtrovat podle názvu** zadejte název skupiny prostředků.
3. Vyberte název skupiny prostředků.
4. V horní nabídce vyberte **Odstranit skupinu prostředků.**

## <a name="next-steps"></a>Další kroky

Vylepšili jste šablonu vytvořenou v [prvním kurzu](template-tutorial-create-first-template.md) přidáním parametrů. V dalším kurzu se dozvíte o funkcích šablony.

> [!div class="nextstepaction"]
> [Přidání funkcí šablony](template-tutorial-add-functions.md)