---
title: Kurz – Export šablony JSON z Azure Portal pro vývoj bicep
description: Naučte se používat exportovanou šablonu JSON k dokončení vývoje bicep.
author: mumian
ms.date: 03/01/2021
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 0cba91a8c021f4baa90d1a4264bf84d3c56930ec
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101748029"
---
# <a name="tutorial-use-exported-json-template-from-the-azure-portal"></a>Kurz: použití exportované šablony JSON z Azure Portal

V této sérii kurzů jste vytvořili soubor bicep pro nasazení účtu úložiště Azure. V následujících dvou kurzech můžete přidat *plán App Service* a *Web*. Místo vytváření šablon od začátku se naučíte exportovat šablony JSON z Azure Portal a jak používat ukázkové šablony ze [šablon Azure pro rychlý Start](https://azure.microsoft.com/resources/templates/). Tyto šablony můžete přizpůsobit pro použití. Tento kurz se zaměřuje na export šablon a přizpůsobení výsledku pro soubor bicep. Dokončení trvá přibližně **14 minut** .

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="prerequisites"></a>Požadavky

Doporučujeme, abyste dokončili [kurz týkající se výstupů](bicep-tutorial-add-outputs.md), ale není to nutné.

Musíte mít Visual Studio Code s rozšířením bicep a buď Azure PowerShell nebo Azure CLI. Další informace najdete v tématu [bicep Tools](bicep-tutorial-create-first-bicep.md#get-tools).

## <a name="review-bicep-file"></a>Zkontrolovat soubor bicep

Na konci předchozího kurzu měl váš soubor bicep následující obsah:

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/add-outputs/azuredeploy.bicep":::

Tento soubor bicep se dobře hodí pro nasazení účtů úložiště, ale můžete do něj přidat další prostředky. Můžete exportovat šablonu JSON z existujícího prostředku a rychle tak získat JSON pro daný prostředek. A pak převeďte JSON na bicep před tím, než je můžete přidat do souboru bicep.

## <a name="create-app-service-plan"></a>Vytvoření plánu služby App Service

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
1. Vyberte **Vytvořit prostředek**.
1. V **části Hledat na Marketplace** zadejte **App Service plán** a potom vyberte **App Service plán**.  Nevybírat **plán App Service (klasický)**
1. Vyberte **Vytvořit**.
1. Zadejte:

    - **Předplatné**: vyberte své předplatné Azure.
    - **Skupina prostředků**: vyberte **vytvořit novou** a zadejte název. Zadejte jiný název skupiny prostředků než ten, který jste používali v této sérii kurzů.
    - **Název**: zadejte název plánu služby App Service.
    - **Operační systém**: vyberte **Linux**.
    - **Oblast**: vyberte umístění Azure. Například **USA – střed**.
    - **Cenová úroveň**: Pokud chcete ušetřit náklady, změňte SKU na **Basic B1** (vývoj/test).

    ![Portál šablony Správce prostředků Exportovat šablonu](./media/bicep-tutorial-export-template/resource-manager-template-export.png)
1. Vyberte možnost **zkontrolovat a vytvořit**.
1. Vyberte **Vytvořit**. Vytvoření prostředku chvíli trvá.

## <a name="export-template"></a>Export šablony

V současné době Azure Portal podporuje pouze export šablon JSON. Existují nástroje, které můžete použít k dekompilaci šablon JSON k bicep souborů.

1. Vyberte **Přejít k prostředku**.

    ![Přejít k prostředku](./media/bicep-tutorial-export-template/resource-manager-template-export-go-to-resource.png)

1. Vyberte **Exportovat šablonu**.

    ![Šablona exportu šablony Správce prostředků](./media/bicep-tutorial-export-template/resource-manager-template-export-template.png)

   Funkce Exportovat šablonu převezme aktuální stav prostředku a vygeneruje šablonu, která ho nasadí. Export šablony může být užitečný způsob, jak rychle získat JSON, který potřebujete k nasazení prostředku.

1. `Microsoft.Web/serverfarms`Definice a definice parametru jsou součásti, které potřebujete.

    ![Šablona exportu exportované šablony Správce prostředků šablony](./media/bicep-tutorial-export-template/resource-manager-template-exported-template.png)

    > [!IMPORTANT]
    > Vyexportovaná šablona je typicky podrobnější, než když budete chtít vytvořit šablonu. Například objekt SKU v exportované šabloně má pět vlastností. Tato šablona funguje, ale můžete ji použít jenom v této `name` Vlastnosti. Můžete začít s exportovanou šablonou a pak ji upravit tak, jak budete chtít podle svých požadavků.

1. Vyberte **Stáhnout**.  Stažený soubor zip obsahuje **template.jsv** a **parameters.jsna**. Soubory rozbalte.
1. Přejděte na **https://bicepdemo.z22.web.core.windows.net/** , vyberte **dekompilovat** a pak otevřete **template.jsna**. Šablonu získáte v bicep.

## <a name="revise-existing-bicep-file"></a>Revidovat existující soubor bicep

Decomplied vyexportovaná šablona poskytuje většinu bicep, které potřebujete, ale je nutné ji přizpůsobit pro váš soubor bicep. Věnujte zvláštní pozornost rozdílům v parametrech a proměnných mezi souborem bicep a exportovaným souborem bicep. V jasném případě proces exportu neví parametry a proměnné, které jste již definovali v souboru bicep.

Následující příklad zvýrazní přidání do souboru bicep. Obsahuje exportovaný kód a některé změny. Nejprve změní název parametru tak, aby odpovídal vaší konvenci pojmenování. Za druhé používá parametr Location pro umístění plánu služby App Service. Třetí, odebere některé vlastnosti, kde je výchozí hodnota přesně.

Zkopírujte celý soubor a nahraďte soubor bicep jeho obsahem.

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/export-template/azuredeploy.bicep" range="1-53" highlight="18,34-51":::

## <a name="deploy-bicep-file"></a>Nasadit soubor bicep

K nasazení souboru bicep použijte rozhraní příkazového řádku Azure nebo Azure PowerShell.

Pokud jste ještě nevytvořili skupinu prostředků, přečtěte si téma [Vytvoření skupiny prostředků](bicep-tutorial-create-first-bicep.md#create-resource-group). V příkladu se předpokládá, že jste nastavili `bicepFile` proměnnou na cestu k souboru bicep, jak je znázorněno v [prvním kurzu](bicep-tutorial-create-first-bicep.md#deploy-bicep-file).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Chcete-li spustit tuto rutinu nasazení, je nutné mít [nejnovější verzi](/powershell/azure/install-az-ps) Azure PowerShell.

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addappserviceplan `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $bicepFile `
  -storagePrefix "store" `
  -storageSKU Standard_LRS
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Jestliže chcete spustit tento příkaz nasazení, musíte mít k dispozici [nejnovější verzi](/cli/azure/install-azure-cli) Azure CLI.

```azurecli
az deployment group create \
  --name addappserviceplan \
  --resource-group myResourceGroup \
  --template-file $bicepFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS
```

---

> [!NOTE]
> Pokud se nasazení nepovedlo, pomocí `verbose` přepínače Získejte informace o vytvářených prostředcích. K `debug` získání dalších informací pro ladění použijte přepínač.

## <a name="verify-deployment"></a>Ověření nasazení

Nasazení můžete ověřit prozkoumáním skupiny prostředků z Azure Portal.

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
1. V nabídce vlevo vyberte **skupiny prostředků**.
1. Vyberte skupinu prostředků, do které jste nasadili.
1. Skupina prostředků obsahuje účet úložiště a plán App Service.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud se chystáte pokračovat k dalšímu kurzu, nemusíte odstranit skupinu prostředků.

Pokud nyní zastavíte, budete možná chtít vyčistit prostředky, které jste nasadili, odstraněním skupiny prostředků.

1. Z Azure Portal v nabídce vlevo vyberte **Skupina prostředků** .
2. Do pole **Filtrovat podle názvu** zadejte název skupiny prostředků.
3. Vyberte název skupiny prostředků.
4. V horní nabídce vyberte **Odstranit skupinu prostředků** .

## <a name="next-steps"></a>Další kroky

Zjistili jste, jak exportovat šablonu JSON z Azure Portal, jak převést šablonu JSON na soubor bicep a jak používat exportovanou šablonu pro vývoj bicep. K zjednodušení vývoje bicep můžete také použít šablony Azure pro rychlý Start.

> [!div class="nextstepaction"]
> [Použití šablon Azure pro rychlý Start](bicep-tutorial-quickstart-template.md)
