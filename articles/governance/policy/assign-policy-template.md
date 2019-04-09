---
title: Vytvoření přiřazení zásady pomocí šablony Resource Manageru
description: Tento článek vás provede kroky k vytvoření přiřazení zásady pro identifikaci neodpovídajících prostředků pomocí šablony Resource Manageru.
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/13/2019
ms.topic: quickstart
ms.service: azure-policy
manager: carmonm
ms.openlocfilehash: 3b7ceb07fe874ebc012696f73c21d5c270cb3cee
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2019
ms.locfileid: "59268989"
---
# <a name="create-a-policy-assignment-to-identify-non-compliant-resources-by-using-a-resource-manager-template"></a>Vytvoření přiřazení zásady pro identifikaci neodpovídajících prostředků pomocí šablony Resource Manageru

Prvním krokem k porozumění dodržování předpisů v Azure je zjištění stavu vašich prostředků.
Tento rychlý start vás provede procesem vytvoření přiřazení zásady pro identifikaci virtuálních počítačů, které nepoužívají spravované disky.

Na konci tohoto procesu úspěšně identifikujete virtuální počítače, které nepoužívají spravované disky. *Neodpovídají* přiřazení zásad.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="create-a-policy-assignment"></a>Vytvoření přiřazení zásady

V tomto rychlém startu vytvoříte přiřazení zásady a přiřadíte předdefinovanou definici zásady *Audit virtuálních počítačů, které nepoužívají spravované disky*. Částečný seznam dostupných předdefinovaných zásad najdete v tématu [ukázky zásad](./samples/index.md).

Existuje několik metod pro vytváření přiřazení zásad. V tomto rychlém startu použijete [šablonu pro rychlý Start](https://azure.microsoft.com/resources/templates/101-azurepolicy-assign-builtinpolicy-resourcegroup/).
Tady je kopie šablony:

[!code-json[policy-assignment](~/quickstart-templates/101-azurepolicy-assign-builtinpolicy-resourcegroup/azuredeploy.json)]

> [!NOTE]
> Služba Azure zásad je zdarma.  Další informace najdete v tématu [přehled Azure Policy](./overview.md).

1. Vyberte pro přihlášení k webu Azure portal a otevřete šablonu na následujícím obrázku:

   [![Deploy šablonu zásad do Azure](./media/assign-policy-template/deploy-to-azure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azurepolicy-assign-builtinpolicy-resourcegroup%2Fazuredeploy.json)

1. Vyberte nebo zadejte následující hodnoty:

   | Název | Hodnota |
   |------|-------|
   | Předplatné | Vyberte své předplatné Azure. |
   | Skupina prostředků | Vyberte **vytvořit nový**, zadejte název a potom vyberte **OK**. Na snímku obrazovky je název skupiny prostředků *mypolicyquickstart<Date in MMDD>rg*. |
   | Umístění | Vyberte oblast. Například **USA (střed)**. |
   | Název přiřazení zásady | Zadejte název přiřazení zásady. Pokud chcete, můžete použít zobrazení definic zásad. Například **Audit virtuálních počítačů, které nepoužívají spravované disky**. |
   | Název rg | Zadejte název skupiny prostředků, ve které chcete přiřadit zásadu. V tomto rychlém startu použijte výchozí hodnotu **[resourceGroup () .name]**. **[resourceGroup()](../../azure-resource-manager/resource-group-template-functions-resource.md#resourcegroup)**  je funkce šablony, která načte skupinu prostředků. |
   | ID definice zásady | Zadejte **/providers/Microsoft.Authorization/policyDefinitions/0a914e76-4921-4c19-b460-a2d36003525a**. |
   | Souhlasím s podmínkami a ujednáními uvedenými nahoře | (Vybrat) |

1. Vyberte **Koupit**.

Některé další prostředky:

- Další ukázky šablon najdete v tématu [šablona Azure Quickstart](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Authorization&pageNumber=1&sort=Popular).
- Chcete-li zobrazit odkaz na šablonu, přejděte na [referenčními informacemi k šablonám Azure](/azure/templates/microsoft.authorization/allversions).
- Zjistěte, jak vyvíjet šablony Resource Manageru, najdete v článku [dokumentace ke službě Azure Resource Manageru](/azure/azure-resource-manager/).
- Další nasazení na úrovni předplatného najdete v tématu [vytvoření skupiny prostředků a prostředků na úrovni předplatného](../../azure-resource-manager/deploy-to-subscription.md).

## <a name="identify-non-compliant-resources"></a>Identifikace prostředků, které nedodržují předpisy

Vyberte **dodržování předpisů** v levé části stránky. Vyhledejte **Audit virtuálních počítačů, které nepoužívají spravované disky** přiřazení zásady, které jste vytvořili.

![Stránka s přehledem zásad dodržování předpisů](./media/assign-policy-template/policy-compliance.png)

Pokud existují nějaké stávající prostředky, které nedodržují předpisy tohoto nového přiřazení, zobrazí se pod **nekompatibilní prostředky**.

Další informace najdete v tématu [jak funguje dodržování předpisů](./how-to/get-compliance-data.md#how-compliance-works).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Odebrat přiřazení vytvořené, postupujte podle těchto kroků:

1. Na levé straně stránky služby Azure Policy vyberte **Dodržování předpisů** (nebo **Přiřazení**) a vyhledejte přiřazení zásady **Audit virtuálních počítačů, které nepoužívají spravované disky**, které jste vytvořili.

1. Klikněte pravým tlačítkem myši **Audit virtuálních počítačů, které nepoužívají spravované disky** přiřazení zásady a vyberte **odstranit přiřazení**.

   ![Odstranit přiřazení stránce Přehled na dodržování předpisů](./media/assign-policy-template/delete-assignment.png)

## <a name="next-steps"></a>Další postup

V tomto rychlém startu přiřadit předdefinovanou definici zásady k oboru a vyhodnotit své sestavy dodržování předpisů. Definice zásad ověří, že všechny prostředky v příslušném oboru jsou kompatibilní a určuje ty, které nejsou.

Další informace o přiřazování zásad pro ověření, že nové prostředky jsou kompatibilní, i nadále najdete v tomto kurzu:

> [!div class="nextstepaction"]
> [Vytváření a Správa zásad](./tutorials/create-and-manage.md)