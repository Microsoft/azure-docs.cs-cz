---
title: 'Quickstart: New policy assignment with portal'
description: In this quickstart, you use Azure portal to create an Azure Policy assignment to identify non-compliant resources.
ms.date: 12/06/2018
ms.topic: quickstart
ms.openlocfilehash: 737faafa65afb87640f7f69b02093c058bd77424
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2019
ms.locfileid: "74216814"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources"></a>Quickstart: Create a policy assignment to identify non-compliant resources

Prvním krokem k porozumění dodržování předpisů v Azure je zjištění stavu vašich prostředků.
Tento rychlý start vás provede procesem vytvoření přiřazení zásady pro identifikaci virtuálních počítačů, které nepoužívají spravované disky.

Na konci tohoto procesu úspěšně identifikujete virtuální počítače, které nepoužívají spravované disky. *Neodpovídají* přiřazení zásad.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný](https://azure.microsoft.com/free/) účet před tím, než začnete.

## <a name="create-a-policy-assignment"></a>Vytvoření přiřazení zásady

In this quickstart, you create a policy assignment and assign the *Audit VMs that do not use managed disks* policy definition.

1. Spusťte službu Azure Policy na webu Azure Portal tak, že kliknete na **Všechny služby** a pak vyhledáte a vyberete **Zásady**.

   ![Search for Policy in All Services](./media/assign-policy-portal/search-policy.png)

1. Na levé straně stránky služby Azure Policy vyberte **Přiřazení**. Přiřazení je zásada, která byla přiřazena, aby proběhla v rámci zadaného oboru.

   ![Select Assignments page from Policy Overview page](./media/assign-policy-portal/select-assignments.png)

1. V horní části stránky **Zásady – Přiřazení** vyberte **Přiřadit zásadu**.

   ![Assign a policy definition from Assignments page](./media/assign-policy-portal/select-assign-policy.png)

1. Na stránce **Přiřadit zásadu** vyberte **Obor** tak, že kliknete na tři tečky a vyberete skupinu pro správu nebo předplatné. Volitelně můžete vybrat skupinu prostředků. Obor určuje, pro které prostředky nebo skupiny prostředků se toto přiřazení zásady bude vynucovat. Pak v dolní části stránky **Obor** klikněte na **Vybrat**.

   V tomto příkladu se používá předplatné **Contoso**. Vaše předplatné se bude lišit.

1. Prostředky je možné vyloučit na základě **oboru**. **Vyloučení** začínají na úrovni o jednu nižší, než je úroveň **oboru**. **Vyloučení** jsou volitelná, takže toto pole prozatím ponechte prázdné.

1. Výběrem tří teček **Definice zásady** otevřete seznam dostupných definic. Služba Azure Policy obsahuje integrované definice zásad, které můžete použít. K dispozici je jich celá řada, například:

   - Vynucovat použití značky a její hodnoty
   - Použít značku a její hodnotu
   - Vyžadovat SQL Server verze 12.0

   For a partial list of available built-in policies, see [Azure Policy samples](./samples/index.md).

1. Prohledejte seznam definic zásad a najděte definici *Audit virtuálních počítačů, které nepoužívají spravované disky*. Klikněte na tuto zásadu a potom na **Vybrat**.

   ![Vyhledání správné definice zásady](./media/assign-policy-portal/select-available-definition.png)

1. Do pole **Název přiřazení** se automaticky vyplní název vybrané zásady, který však můžete změnit. Pro účely tohoto příkladu ponechte *Audit virtuálních počítačů, které nepoužívají spravované disky*. Volitelně můžete přidat také **Popis**. Popis obsahuje podrobnosti o tomto přiřazení zásady.
   Pole **Přiřadil** se automaticky vyplní podle toho, kdo je přihlášený. Toto pole je volitelné, takže do něj můžete zadávat vlastní hodnoty.

1. Políčko **Vytvořit spravovanou identitu** ponechte nezaškrtnuté. This box _must_ be checked when the policy or initiative includes a policy with the [deployIfNotExists](./concepts/effects.md#deployifnotexists) effect. As the policy used for this quickstart doesn't, leave it blank. Další informace najdete v tématech věnovaných [spravovaným identitám](../../active-directory/managed-identities-azure-resources/overview.md) a [principu fungování zabezpečení náprav](./how-to/remediate-resources.md#how-remediation-security-works).

1. Klikněte na **Přiřadit**.

You're now ready to identify non-compliant resources to understand the compliance state of your environment.

## <a name="identify-non-compliant-resources"></a>Identifikace prostředků, které nedodržují předpisy

Select **Compliance** in the left side of the page. Then locate the **Audit VMs that do not use managed disks** policy assignment you created.

![Compliance details on the Policy Compliance page](./media/assign-policy-portal/policy-compliance.png)

If there are any existing resources that aren't compliant with this new assignment, they appear under **Non-compliant resources**.

Pokud se napříč stávajícími prostředky vyhodnotí nějaká podmínka a zjistí hodnotu True, takové prostředky se označí jako nekompatibilní s příslušnou zásadou. Následující tabulka ukazuje, jak různé účinky zásad pracují s vyhodnocením podmínek pro zjištění výsledného stavu dodržování předpisů. Although you don't see the evaluation logic in the Azure portal, the compliance state results are shown. Výsledný stav je buď kompatibilní, nebo nekompatibilní.

| **Stav prostředku** | **Účinek** | **Vyhodnocení zásad** | **Stav dodržování předpisů** |
| --- | --- | --- | --- |
| Existuje | Deny, Audit, Append\*, DeployIfNotExist\*, AuditIfNotExist\* | Pravda | Nevyhovující předpisům |
| Existuje | Deny, Audit, Append\*, DeployIfNotExist\*, AuditIfNotExist\* | Nepravda | V souladu s předpisy |
| Novinka | Audit, AuditIfNotExist\* | Pravda | Nevyhovující předpisům |
| Novinka | Audit, AuditIfNotExist\* | Nepravda | V souladu s předpisy |

\* Účinky Append, DeployIfNotExist a AuditIfNotExist vyžadují, aby byl příkaz IF nastaven na TRUE.
Tyto účinky také vyžadují, aby existovala podmínka, která musí nabývat hodnoty FALSE, aby byla zásada vyhodnocena jako Nevyhovující předpisům. Pokud má hodnotu TRUE, aktivuje podmínka IF vyhodnocení podmínky existence pro související prostředky.

## <a name="clean-up-resources"></a>Vyčištění prostředků

To remove the assignment created, follow these steps:

1. Na levé straně stránky služby Azure Policy vyberte **Dodržování předpisů** (nebo **Přiřazení**) a vyhledejte přiřazení zásady **Audit virtuálních počítačů, které nepoužívají spravované disky**, které jste vytvořili.

1. Klikněte pravým tlačítkem na přiřazení zásady **Audit virtuálních počítačů, které nepoužívají spravované disky** a vyberte **Odstranit přiřazení**.

   ![Delete an assignment from the Compliance page](./media/assign-policy-portal/delete-assignment.png)

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste přiřadili definici zásady k oboru prostředků a vyhodnotili jste její sestavu dodržování předpisů.
The policy definition validates that all the resources in the scope are compliant and identifies which ones aren't.

To learn more about assigning policies to validate that new resources are compliant, continue to the tutorial for:

> [!div class="nextstepaction"]
> [Vytváření a správa zásad](./tutorials/create-and-manage.md)