---
title: Kurz – Export šablony z Azure Portal
description: Naučte se používat exportovanou šablonu k dokončení vývoje šablon.
author: mumian
ms.date: 10/04/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 6c22a8e2b479ed7d3a225bc649d89f5302d0f536
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/22/2019
ms.locfileid: "74406010"
---
# <a name="tutorial-use-exported-template-from-the-azure-portal"></a>Kurz: použití exportované šablony z Azure Portal

V této sérii kurzů jste vytvořili šablonu pro nasazení účtu úložiště Azure. V následujících dvou kurzech můžete přidat *plán App Service* a *Web*. Místo vytváření šablon od začátku se naučíte exportovat šablony z Azure Portal a jak používat ukázkové šablony ze [šablon Azure pro rychlý Start](https://azure.microsoft.com/resources/templates/). Tyto šablony můžete přizpůsobit pro použití. Tento kurz se zaměřuje na export šablon a přizpůsobení výsledku pro šablonu. Dokončení trvá přibližně **14 minut** .

## <a name="prerequisites"></a>Požadavky

Doporučujeme, abyste dokončili [kurz týkající se výstupů](template-tutorial-add-outputs.md), ale není to nutné.

Musíte mít Visual Studio Code s rozšířením Správce prostředků Tools a buď Azure PowerShell, nebo v rozhraní příkazového řádku Azure. Další informace najdete v tématu [nástroje šablon](template-tutorial-create-first-template.md#get-tools).

## <a name="review-template"></a>Zkontrolovat šablonu

Na konci předchozího kurzu má vaše šablona následující JSON:

[!code-json[](~/resourcemanager-templates/get-started-with-templates/add-outputs/azuredeploy.json)]

Tato šablona funguje dobře pro nasazení účtů úložiště, ale můžete chtít přidat další prostředky. Můžete exportovat šablonu z existujícího prostředku, abyste mohli rychle získat kód JSON pro daný prostředek.

## <a name="create-app-service-plan"></a>Vytvoření plánu služby App Service

1. Přihlaste se na web [Azure Portal ](https://portal.azure.com).
1. Vyberte **vytvořit prostředek**.
1. V **části Hledat na Marketplace**zadejte **App Service plán**a potom vyberte **App Service plán**.  Nevybírat **plán App Service (klasický)**
1. Vyberte **Vytvořit**.
1. Napište

    - **Předplatné:** Vyberte své předplatné Azure.
    - **Skupina prostředků**: vyberte **vytvořit novou** a zadejte název. Zadejte jiný název skupiny prostředků než ten, který jste používali v této sérii kurzů.
    - **Název**: zadejte název plánu služby App Service.
    - **Operační systém**: vyberte **Linux**.
    - **Oblast**: vyberte umístění Azure. Například **střed USA**.
    - **Cenová úroveň**: Pokud chcete ušetřit náklady, změňte SKU na **Basic B1** (vývoj/test).

    ![Portál šablony Správce prostředků Exportovat šablonu](./media/template-tutorial-export-template/resource-manager-template-export.png)
1. Vyberte možnost **zkontrolovat a vytvořit**.
1. Vyberte **Vytvořit**. Vytvoření prostředku chvíli trvá.

## <a name="export-template"></a>Export šablony

1. Vyberte **Přejít k prostředku**.

    ![Přejít k prostředku](./media/template-tutorial-export-template/resource-manager-template-export-go-to-resource.png)

1. Vyberte **Exportovat šablonu**.

    ![Šablona exportu šablony Správce prostředků](./media/template-tutorial-export-template/resource-manager-template-export-template.png)

   Funkce Exportovat šablonu převezme aktuální stav prostředku a vygeneruje šablonu, která ho nasadí. Export šablony může být užitečný způsob, jak rychle získat JSON, který potřebujete k nasazení prostředku.

1. Zkopírujte definici **Microsoft. Web/serverových farem** a definici parametru do šablony.

    ![Šablona exportu exportované šablony Správce prostředků šablony](./media/template-tutorial-export-template/resource-manager-template-exported-template.png)

> [!IMPORTANT]
> Vyexportovaná šablona je typicky podrobnější, než když budete chtít vytvořit šablonu. Například objekt SKU v exportované šabloně má pět vlastností. Tato šablona funguje, ale můžete pouze použít vlastnost **Name** . Můžete začít s exportovanou šablonou a pak ji upravit tak, jak budete chtít podle svých požadavků.

## <a name="revise-existing-template"></a>Revidovat existující šablonu

Vyexportovaná šablona vám poskytne většinu formátu JSON, který potřebujete, ale je nutné ji přizpůsobit pro šablonu. Věnujte zvláštní pozornost rozdílům v parametrech a proměnných mezi šablonou a exportovanou šablonou. Proces exportu neznáte parametry a proměnné, které jste již definovali v šabloně.

Následující příklad zvýrazní přidání do šablony. Obsahuje exportovaný kód a některé změny. Nejprve změní název parametru tak, aby odpovídal vaší konvenci pojmenování. Za druhé používá parametr Location pro umístění plánu služby App Service. Třetí, odebere **název** uvnitř objektu **Properties** , protože tato hodnota je redundantní s vlastností **Name** na úrovni prostředku.

Zkopírujte celý soubor a nahraďte šablonu jeho obsahem.

[!code-json[](~/resourcemanager-templates/get-started-with-templates/export-template/azuredeploy.json?range=1-77&highlight=28-31,50-69)]

## <a name="deploy-template"></a>Nasazení šablony

K nasazení šablony použijte rozhraní příkazového řádku Azure nebo Azure PowerShell.

Pokud jste ještě nevytvořili skupinu prostředků, přečtěte si téma [Vytvoření skupiny prostředků](template-tutorial-create-first-template.md#create-resource-group). V příkladu se předpokládá, že jste nastavili proměnnou **templateFile** na cestu k souboru šablony, jak je znázorněno v [prvním kurzu](template-tutorial-create-first-template.md#deploy-template).

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addappserviceplan `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storagePrefix "store" `
  -storageSKU Standard_LRS
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az group deployment create \
  --name addappserviceplan \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS
```

---

## <a name="verify-deployment"></a>Ověření nasazení

Nasazení můžete ověřit prozkoumáním skupiny prostředků z Azure Portal.

1. Přihlaste se na web [Azure Portal ](https://portal.azure.com).
1. V nabídce vlevo vyberte **skupiny prostředků**.
1. Vyberte skupinu prostředků, do které jste nasadili.
1. Skupina prostředků obsahuje účet úložiště a plán App Service.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud se chystáte pokračovat k dalšímu kurzu, nemusíte odstranit skupinu prostředků.

Pokud nyní zastavíte, budete možná chtít vyčistit prostředky, které jste nasadili, odstraněním skupiny prostředků.

1. Na portálu Azure Portal vyberte v nabídce nalevo **Skupina prostředků**.
2. Do pole **Filtrovat podle názvu** zadejte název skupiny prostředků.
3. Vyberte název skupiny prostředků.
4. V nabídce nahoře vyberte **Odstranit skupinu prostředků**.

## <a name="next-steps"></a>Další kroky

Zjistili jste, jak exportovat šablonu z Azure Portal a jak používat exportovanou šablonu pro vývoj šablon. K zjednodušení vývoje šablon můžete také použít šablony Azure pro rychlý Start.

> [!div class="nextstepaction"]
> [Použití šablon Azure pro rychlý Start](template-tutorial-quickstart-template.md)
