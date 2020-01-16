---
title: 'Rychlý Start: nové přiřazení zásad pomocí šablon'
description: V tomto rychlém startu použijete šablonu Správce prostředků k vytvoření přiřazení zásady pro identifikaci prostředků, které nedodržují předpisy.
ms.date: 11/25/2019
ms.topic: quickstart
ms.openlocfilehash: 8b9b0024e5c15c78c6777b8657839791484d66b5
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/15/2020
ms.locfileid: "75980505"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-by-using-a-resource-manager-template"></a>Rychlý Start: vytvoření přiřazení zásady pro identifikaci prostředků, které nedodržují předpisy, pomocí šablony Správce prostředků

Prvním krokem k porozumění dodržování předpisů v Azure je zjištění stavu vašich prostředků.
Tento rychlý start vás provede procesem vytvoření přiřazení zásady pro identifikaci virtuálních počítačů, které nepoužívají spravované disky.

Na konci tohoto procesu úspěšně identifikujete virtuální počítače, které nepoužívají spravované disky. _Neodpovídají_ přiřazení zásad.

## <a name="prerequisites"></a>Požadavky

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="create-a-policy-assignment"></a>Vytvoření přiřazení zásady

V tomto rychlém startu vytvoříte přiřazení zásady a přiřadíte definici předdefinované zásady nazvanou _audit virtuálních počítačů, které nepoužívají spravované disky_. Částečný seznam dostupných integrovaných zásad najdete v tématu [Azure Policy Samples](./samples/index.md).

Existuje několik způsobů, jak vytvořit přiřazení zásad. V tomto rychlém startu použijete [šablonu pro rychlý Start](https://azure.microsoft.com/resources/templates/101-azurepolicy-assign-builtinpolicy-resourcegroup/).
Tady je kopie šablony:

[!code-json[policy-assignment](~/quickstart-templates/101-azurepolicy-assign-builtinpolicy-resourcegroup/azuredeploy.json)]

> [!NOTE]
> Služba Azure Policy je zadarmo. Další informace najdete v tématu [přehled Azure Policy](./overview.md).

1. Vyberte následující obrázek pro přihlášení do Azure Portal a otevřete šablonu:

   [![nasadit šablonu zásad do Azure](./media/assign-policy-template/deploy-to-azure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azurepolicy-assign-builtinpolicy-resourcegroup%2Fazuredeploy.json)

1. Vyberte nebo zadejte následující hodnoty:

   | Name (Název) | Hodnota |
   |------|-------|
   | Předplatné | Vyberte své předplatné Azure. |
   | Skupina prostředků | Vyberte **vytvořit novou**, zadejte název a pak vyberte **OK**. Název skupiny prostředků na snímku obrazovky je _mypolicyquickstart\<datum v MMDD\>RG_. |
   | Umístění | Vyberte oblast. Například **USA (střed)** . |
   | Název přiřazení zásady | Zadejte název přiřazení zásady. Pokud chcete, můžete použít zobrazení definice zásad. Například proveďte **audit virtuálních počítačů, které nepoužívají spravované disky**. |
   | Název RG | Zadejte název skupiny prostředků, ke které chcete zásadu přiřadit. V tomto rychlém startu použijte výchozí hodnotu **[resourceName (). name]** . Skupina prostředků **[()](../../azure-resource-manager/templates/template-functions-resource.md#resourcegroup)** je šablonou funkce, která načítá skupinu prostředků. |
   | ID definice zásady | Zadejte **/providers/Microsoft.Authorization/policyDefinitions/0a914e76-4921-4C19-b460-a2d36003525a**. |
   | Souhlasím s podmínkami a ujednáními uvedenými nahoře | Vybrali |

1. Vyberte **Koupit**.

Některé další zdroje informací:

- Další šablony ukázek najdete v tématu [Šablona Azure pro rychlý Start](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Authorization&pageNumber=1&sort=Popular).
- Odkaz na šablonu zobrazíte tak, že přejdete na [odkaz šablony Azure](/azure/templates/microsoft.authorization/allversions).
- Další informace o vývoji šablon Správce prostředků najdete v [dokumentaci k Azure Resource Manager](../../azure-resource-manager/management/overview.md).
- Pokud chcete zjistit nasazení na úrovni předplatného, přečtěte si téma [Vytvoření skupin prostředků a prostředků na úrovni předplatného](../../azure-resource-manager/templates/deploy-to-subscription.md).

## <a name="identify-non-compliant-resources"></a>Identifikace prostředků, které nedodržují předpisy

Vyberte **dodržování předpisů** v levé části stránky. Vyhledejte **Audit virtuálních počítačů, které nepoužívají spravované disky** přiřazení zásady, které jste vytvořili.

![Stránka s přehledem dodržování zásad](./media/assign-policy-template/policy-compliance.png)

Pokud existují nějaké stávající prostředky, které nedodržují předpisy tohoto nového přiřazení, zobrazí se pod **nekompatibilní prostředky**.

Další informace najdete v tématu [Jak funguje dodržování předpisů](./how-to/get-compliance-data.md#how-compliance-works).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Odebrat přiřazení vytvořené, postupujte podle těchto kroků:

1. Na levé straně stránky služby Azure Policy vyberte **Dodržování předpisů** (nebo **Přiřazení**) a vyhledejte přiřazení zásady **Audit virtuálních počítačů, které nepoužívají spravované disky**, které jste vytvořili.

1. Klikněte pravým tlačítkem na **Auditovat virtuální počítače, které nepoužívají správu zásad spravovaných disků** , a vyberte **Odstranit přiřazení**.

   ![Odstranění přiřazení na stránce Přehled dodržování předpisů](./media/assign-policy-template/delete-assignment.png)

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste přiřadili integrovanou definici zásady k oboru a vyhodnotili sestavu dodržování předpisů. Definice zásad ověří, že všechny prostředky v příslušném oboru jsou kompatibilní a určuje ty, které nejsou.

Další informace o přiřazování zásad pro ověření, že nové prostředky jsou kompatibilní, i nadále najdete v tomto kurzu:

> [!div class="nextstepaction"]
> [Vytváření a správa zásad](./tutorials/create-and-manage.md)