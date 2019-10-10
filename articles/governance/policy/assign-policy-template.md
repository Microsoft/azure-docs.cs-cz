---
title: Vytvoření přiřazení pomocí šablony Správce prostředků
description: Tento článek vás provede jednotlivými kroky použití šablony Správce prostředků k vytvoření přiřazení zásady pro identifikaci prostředků, které nedodržují předpisy.
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/13/2019
ms.topic: quickstart
ms.service: azure-policy
ms.openlocfilehash: 842b29160e3abaea5edc4bfb4f2e1c345e3ffc85
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/10/2019
ms.locfileid: "72254813"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-by-using-a-resource-manager-template"></a>Rychlý Start: vytvoření přiřazení zásady pro identifikaci prostředků, které nedodržují předpisy, pomocí šablony Správce prostředků

Prvním krokem při porozumění dodržování předpisů v Azure je určení stavu vašich prostředků.
Tento rychlý Start vás provede procesem vytvoření přiřazení zásady pro identifikaci virtuálních počítačů, které nepoužívají spravované disky.

Na konci tohoto procesu úspěšně identifikujete virtuální počítače, které nepoužívají spravované disky. Neodpovídají přiřazení zásad.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný](https://azure.microsoft.com/free/) účet před tím, než začnete.

## <a name="create-a-policy-assignment"></a>Vytvoření přiřazení zásady

V tomto rychlém startu vytvoříte přiřazení zásady a přiřadíte definici předdefinované zásady nazvanou *audit virtuálních počítačů, které nepoužívají spravované disky*. Částečný seznam dostupných integrovaných zásad najdete v tématu [Azure Policy Samples](./samples/index.md).

Existuje několik způsobů, jak vytvořit přiřazení zásad. V tomto rychlém startu použijete [šablonu pro rychlý Start](https://azure.microsoft.com/resources/templates/101-azurepolicy-assign-builtinpolicy-resourcegroup/).
Tady je kopie šablony:

[!code-json[policy-assignment](~/quickstart-templates/101-azurepolicy-assign-builtinpolicy-resourcegroup/azuredeploy.json)]

> [!NOTE]
> Služba Azure Policy je zadarmo.  Další informace najdete v tématu [přehled Azure Policy](./overview.md).

1. Vyberte následující obrázek pro přihlášení do Azure Portal a otevřete šablonu:

   [@no__t – 1Deploy šablony zásad do Azure](./media/assign-policy-template/deploy-to-azure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azurepolicy-assign-builtinpolicy-resourcegroup%2Fazuredeploy.json)

1. Vyberte nebo zadejte následující hodnoty:

   | Jméno | Hodnota |
   |------|-------|
   | Předplatné | Vyberte své předplatné Azure. |
   | Skupina prostředků | Vyberte **vytvořit novou**, zadejte název a pak vyberte **OK**. Na snímku obrazovky je název skupiny prostředků *mypolicyquickstart @ no__t-1Date v MMDD > RG*. |
   | Umístění | Vyberte oblast. Například **střed USA**. |
   | Název přiřazení zásady | Zadejte název přiřazení zásady. Pokud chcete, můžete použít zobrazení definice zásad. Například proveďte **audit virtuálních počítačů, které nepoužívají spravované disky**. |
   | Název RG | Zadejte název skupiny prostředků, ke které chcete zásadu přiřadit. V tomto rychlém startu použijte výchozí hodnotu **[resourceName (). name]** . Skupina prostředků **[()](../../azure-resource-manager/resource-group-template-functions-resource.md#resourcegroup)** je šablonou funkce, která načítá skupinu prostředků. |
   | ID definice zásady | Zadejte **/providers/Microsoft.Authorization/policyDefinitions/0a914e76-4921-4C19-b460-a2d36003525a**. |
   | Souhlasím s podmínkami a ujednáními uvedenými nahoře | Vybrali |

1. Vyberte **koupit**.

Některé další zdroje informací:

- Další šablony ukázek najdete v tématu [Šablona Azure pro rychlý Start](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Authorization&pageNumber=1&sort=Popular).
- Odkaz na šablonu zobrazíte tak, že přejdete na [odkaz šablony Azure](/azure/templates/microsoft.authorization/allversions).
- Další informace o vývoji šablon Správce prostředků najdete v [dokumentaci k Azure Resource Manager](/azure/azure-resource-manager/).
- Pokud chcete zjistit nasazení na úrovni předplatného, přečtěte si téma [Vytvoření skupin prostředků a prostředků na úrovni předplatného](../../azure-resource-manager/deploy-to-subscription.md).

## <a name="identify-non-compliant-resources"></a>Identifikace prostředků, které nedodržují předpisy

Na levé straně stránky vyberte **dodržování předpisů** . Pak vyhledejte **auditované virtuální počítače, které nepoužívají přiřazení zásad spravovaných disků** , které jste vytvořili.

![Stránka s přehledem dodržování zásad](./media/assign-policy-template/policy-compliance.png)

Pokud nějaké existující prostředky nedodržují předpisy tohoto nového přiřazení, zobrazí se v části **nekompatibilní prostředky**.

Další informace najdete v tématu [Jak funguje dodržování předpisů](./how-to/get-compliance-data.md#how-compliance-works).

## <a name="clean-up-resources"></a>Vyčištění prostředků

K odebrání vytvořeného přiřazení použijte následující postup:

1. Na levé straně stránky Azure Policy vyberte **dodržování předpisů** (nebo **přiřazení**) a vyhledejte **auditované virtuální počítače, které nepoužívají přiřazení zásad spravované disky** , které jste vytvořili.

1. Klikněte pravým tlačítkem na **Auditovat virtuální počítače, které nepoužívají správu zásad spravovaných disků** , a vyberte **Odstranit přiřazení**.

   ![Odstranění přiřazení na stránce Přehled dodržování předpisů](./media/assign-policy-template/delete-assignment.png)

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste přiřadili integrovanou definici zásady k oboru a vyhodnotili sestavu dodržování předpisů. Definice zásady ověří, že všechny prostředky v oboru jsou v souladu s předpisy, a určí, které z nich nejsou.

Další informace o přiřazování zásad k ověření, že jsou nové prostředky kompatibilní, najdete v tomto kurzu:

> [!div class="nextstepaction"]
> [Vytváření a Správa zásad](./tutorials/create-and-manage.md)