---
title: Kurz – export šablony z portálu Azure
description: Přečtěte si, jak pomocí exportované šablony dokončit vývoj šablony.
author: mumian
ms.date: 03/27/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 5a0d373fdf75f19c8fc1082593c15c14770f79c3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80369902"
---
# <a name="tutorial-use-exported-template-from-the-azure-portal"></a>Kurz: Použití exportované šablony z webu Azure Portal

V této sérii kurzů jste vytvořili šablonu pro nasazení účtu úložiště Azure. V dalších dvou kurzech přidáte *plán služby App Service* a *web*. Místo vytváření šablon od začátku se dozvíte, jak exportovat šablony z webu Azure Portal a jak používat ukázkové šablony ze [šablon Azure Quickstart](https://azure.microsoft.com/resources/templates/). Tyto šablony můžete přizpůsobit pro své použití. Tento kurz se zaměřuje na export šablon a přizpůsobení výsledku šablony. To trvá asi **14 minut** na dokončení.

## <a name="prerequisites"></a>Požadavky

Doporučujeme dokončit [kurz o výstupech](template-tutorial-add-outputs.md), ale není to nutné.

Musíte mít kód Visual Studio s rozšířením Nástroje správce prostředků a buď Azure PowerShell nebo Azure CLI. Další informace naleznete v [tématu nástroje šablony](template-tutorial-create-first-template.md#get-tools).

## <a name="review-template"></a>Šablona revize

Na konci předchozího kurzu měla vaše šablona následující JSON:

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-outputs/azuredeploy.json":::

Tato šablona funguje dobře pro nasazení účtů úložiště, ale můžete chtít přidat další prostředky k němu. Můžete exportovat šablonu z existujícího prostředku a rychle získat json pro tento prostředek.

## <a name="create-app-service-plan"></a>Vytvoření plánu služby App Service

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
1. Vyberte **Vytvořit prostředek**.
1. Ve **vyhledávání na webu Marketplace**zadejte plán **služby App Service**a pak vyberte Plán **služby App Service**.  Nevybírejte **plán služby App Service (klasický)**
1. Vyberte **Vytvořit**.
1. Zadejte:

    - **Předplatné**: vyberte předplatné Azure.
    - **Skupina prostředků**: Vyberte **Vytvořit nový** a zadejte název. Zadejte jiný název skupiny prostředků, než který jste používali v této sérii kurzů.
    - **Název**: zadejte název plánu služby App.
    - **Operační systém**: vyberte **Linux**.
    - **Oblast**: vyberte umístění Azure. Například **střední USA**.
    - **Cenová úroveň:** Chcete-li ušetřit náklady, změňte skladovou položku na **základní položku B1** (v části Vývoj/test).

    ![Portál šablon šablony šablony šablony správce prostředků](./media/template-tutorial-export-template/resource-manager-template-export.png)
1. Vyberte **Zkontrolovat a vytvořit**.
1. Vyberte **Vytvořit**. Vytvoření prostředku trvá několik okamžiků.

## <a name="export-template"></a>Export šablony

1. Vyberte **Přejít na zdroj**.

    ![Přejít k prostředku](./media/template-tutorial-export-template/resource-manager-template-export-go-to-resource.png)

1. Vyberte **Exportovat šablonu**.

    ![Šablona exportu šablony správce prostředků](./media/template-tutorial-export-template/resource-manager-template-export-template.png)

   Funkce šablony exportu přebírá aktuální stav prostředku a generuje šablonu k jeho nasazení. Export šablony může být užitečný způsob, jak rychle získat JSON, který potřebujete k nasazení prostředku.

1. Zkopírujte definici **Microsoft.Web/serverfarms** a definici parametru do šablony.

    ![Exportní šablona exportu šablony správce prostředků exportovaná šablona](./media/template-tutorial-export-template/resource-manager-template-exported-template.png)

> [!IMPORTANT]
> Exportovaná šablona je obvykle podrobnější, než byste mohli chtít při vytváření šablony. Například objekt Skladové položky v exportované šabloně má pět vlastností. Tato šablona funguje, ale můžete použít pouze **vlastnost name.** Můžete začít s exportovnou šablonou a pak ji upravit tak, jak chcete, aby vyhovovala vašim požadavkům.

## <a name="revise-existing-template"></a>Revize existující šablony

Exportovaná šablona poskytuje většinu JSON, který potřebujete, ale musíte ji přizpůsobit pro šablonu. Zvláštní pozornost věnujte rozdílům v parametrech a proměnných mezi šablonou a exportovnou šablonou. Je zřejmé, že proces exportu nezná parametry a proměnné, které jste již definovali v šabloně.

Následující příklad zvýrazní dodatky k šabloně. Obsahuje exportovaný kód a některé změny. Nejprve změní název parametru tak, aby odpovídal konvenci pojmenování. Za druhé používá parametr umístění pro umístění plánu služby app service. Za třetí odebere **název** uvnitř objektu **vlastnosti,** protože tato hodnota je redundantní s vlastností **name** na úrovni prostředků.

Zkopírujte celý soubor a nahraďte šablonu jeho obsahem.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/export-template/azuredeploy.json" range="1-77" highlight="28-31,50-69":::

## <a name="deploy-template"></a>Nasazení šablony

K nasazení šablony použijte azure CLI nebo Azure PowerShell.

Pokud jste skupinu prostředků nevytvořili, přečtěte si informace [o vytvoření skupiny prostředků](template-tutorial-create-first-template.md#create-resource-group). Příklad předpokládá, že jste nastavili proměnnou **templateFile** na cestu k souboru šablony, jak je znázorněno v [prvním kurzu](template-tutorial-create-first-template.md#deploy-template).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addappserviceplan `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storagePrefix "store" `
  -storageSKU Standard_LRS
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Chcete-li spustit tento příkaz nasazení, musíte mít [nejnovější verzi](/cli/azure/install-azure-cli) azure cli.

```azurecli
az deployment group create \
  --name addappserviceplan \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS
```

---

## <a name="verify-deployment"></a>Ověření nasazení

Nasazení můžete ověřit tak, že prozkoumáte skupinu prostředků z webu Azure Portal.

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
1. V levé nabídce vyberte **položku Skupiny prostředků**.
1. Vyberte skupinu prostředků, do které jste nasadili.
1. Skupina prostředků obsahuje účet úložiště a plán služby App Service.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud přecházíte na další kurz, nemusíte odstraňovat skupinu prostředků.

Pokud nyní zastavujete, můžete chtít vyčistit prostředky, které jste nasadili, odstraněním skupiny prostředků.

1. Na portálu Azure vyberte **skupinu prostředků** z levé nabídky.
2. Do pole **Filtrovat podle názvu** zadejte název skupiny prostředků.
3. Vyberte název skupiny prostředků.
4. V horní nabídce vyberte **Odstranit skupinu prostředků.**

## <a name="next-steps"></a>Další kroky

Zjistili jste, jak exportovat šablonu z webu Azure Portal a jak použít exportovnou šablonu pro vývoj šablony. K zjednodušení vývoje šablon můžete také použít šablony Azure Quickstart.

> [!div class="nextstepaction"]
> [Použití šablon Azure Quickstart](template-tutorial-quickstart-template.md)
