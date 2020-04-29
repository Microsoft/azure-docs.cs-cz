---
title: 'Rychlý Start: nové přiřazení zásad s portálem'
description: V tomto rychlém startu použijete Azure Portal k vytvoření přiřazení Azure Policy k identifikaci prostředků, které nedodržují předpisy.
ms.date: 03/24/2020
ms.topic: quickstart
ms.openlocfilehash: 3a514478f3cefa49c37c3431a80b578dff480584
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "80240005"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources"></a>Rychlý Start: vytvoření přiřazení zásady pro identifikaci prostředků, které nedodržují předpisy

Prvním krokem k porozumění dodržování předpisů v Azure je zjištění stavu vašich prostředků.
Tento rychlý start vás provede procesem vytvoření přiřazení zásady pro identifikaci virtuálních počítačů, které nepoužívají spravované disky.

Na konci tohoto procesu úspěšně identifikujete virtuální počítače, které nepoužívají spravované disky. _Neodpovídají_ přiřazení zásad.

## <a name="prerequisites"></a>Požadavky

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný](https://azure.microsoft.com/free/) účet před tím, než začnete.

## <a name="create-a-policy-assignment"></a>Vytvoření přiřazení zásady

V tomto rychlém startu vytvoříte přiřazení zásady a přiřadíte _virtuální počítače auditu, které nepoužívají definici zásad spravované disky_ .

1. Spusťte službu Azure Policy na webu Azure Portal tak, že kliknete na **Všechny služby** a pak vyhledáte a vyberete **Zásady**.

   :::image type="content" source="./media/assign-policy-portal/search-policy.png" alt-text="Vyhledat zásady ve všech službách" border="false":::

1. Na levé straně stránky služby Azure Policy vyberte **Přiřazení**. Přiřazení je zásada, která byla přiřazena, aby proběhla v rámci zadaného oboru.

   :::image type="content" source="./media/assign-policy-portal/select-assignments.png" alt-text="Stránka pro výběr přiřazení na stránce Přehled zásad" border="false":::

1. V horní části stránky **Zásady – Přiřazení** vyberte **Přiřadit zásadu**.

   :::image type="content" source="./media/assign-policy-portal/select-assign-policy.png" alt-text="Přiřazení definice zásady ze stránky přiřazení" border="false":::

1. Na stránce **Přiřadit zásadu** vyberte **Obor** tak, že kliknete na tři tečky a vyberete skupinu pro správu nebo předplatné. Volitelně můžete vybrat skupinu prostředků. Obor určuje, pro které prostředky nebo skupiny prostředků se toto přiřazení zásady bude vynucovat. Pak v dolní části stránky **Obor** klikněte na **Vybrat**.

   V tomto příkladu se používá předplatné **Contoso** . Vaše předplatné se bude lišit.

1. Prostředky je možné vyloučit na základě **oboru**. **Vyloučení** začínají na úrovni o jednu nižší, než je úroveň **oboru**. **Vyloučení** jsou volitelná, takže toto pole prozatím ponechte prázdné.

1. Výběrem tří teček **Definice zásady** otevřete seznam dostupných definic. Služba Azure Policy obsahuje integrované definice zásad, které můžete použít. K dispozici je jich celá řada, například:

   - Vynucovat použití značky a její hodnoty
   - Použít značku a její hodnotu
   - Zdědit značku ze skupiny prostředků, pokud chybí

   Částečný seznam dostupných integrovaných zásad najdete v tématu [Azure Policy Samples](./samples/index.md).

1. Prohledejte seznam definic zásad a najděte definici _Audit virtuálních počítačů, které nepoužívají spravované disky_. Klikněte na tuto zásadu a potom na **Vybrat**.

   :::image type="content" source="./media/assign-policy-portal/select-available-definition.png" alt-text="Vyhledání správné definice zásady" border="false":::

1. Do pole **Název přiřazení** se automaticky vyplní název vybrané zásady, který však můžete změnit. Pro účely tohoto příkladu ponechte _Audit virtuálních počítačů, které nepoužívají spravované disky_. Volitelně můžete přidat také **Popis**. Popis obsahuje podrobnosti o tomto přiřazení zásady.
   Pole **Přiřadil** se automaticky vyplní podle toho, kdo je přihlášený. Toto pole je volitelné, takže do něj můžete zadat vlastní hodnoty.

1. Políčko **Vytvořit spravovanou identitu** ponechte nezaškrtnuté. Toto políčko _musí_ být zaškrtnuto, pokud zásada nebo iniciativa zahrnuje zásadu s efektem [deployIfNotExists](./concepts/effects.md#deployifnotexists) . Protože zásady použité pro tento rychlý Start nejsou, ponechte pole prázdné. Další informace najdete v tématech věnovaných [spravovaným identitám](../../active-directory/managed-identities-azure-resources/overview.md) a [principu fungování zabezpečení náprav](./how-to/remediate-resources.md#how-remediation-security-works).

1. Klikněte na **Přiřadit**.

Nyní můžete identifikovat nekompatibilní prostředky, abyste pochopili stav dodržování předpisů vašeho prostředí.

## <a name="identify-non-compliant-resources"></a>Identifikace prostředků, které nedodržují předpisy

Na levé straně stránky vyberte **dodržování předpisů** . Pak vyhledejte **auditované virtuální počítače, které nepoužívají přiřazení zásad spravovaných disků** , které jste vytvořili.

:::image type="content" source="./media/assign-policy-portal/policy-compliance.png" alt-text="Podrobnosti o kompatibilitě na stránce dodržování zásad" border="false":::

Pokud nějaké existující prostředky nedodržují předpisy tohoto nového přiřazení, zobrazí se v části **nekompatibilní prostředky**.

Pokud se napříč stávajícími prostředky vyhodnotí nějaká podmínka a zjistí hodnotu True, takové prostředky se označí jako nekompatibilní s příslušnou zásadou. Následující tabulka ukazuje, jak různé účinky zásad pracují s vyhodnocením podmínek pro zjištění výsledného stavu dodržování předpisů. I když se v Azure Portal logika hodnocení nezobrazuje, zobrazí se výsledky stavu dodržování předpisů. Výsledný stav je buď kompatibilní, nebo nekompatibilní.

| **Stav prostředku** | **Účinek** | **Vyhodnocení zásad** | **Stav dodržování předpisů** |
| --- | --- | --- | --- |
| Existuje | Deny, Audit, Append\*, DeployIfNotExist\*, AuditIfNotExist\* | True | Nevyhovující předpisům |
| Existuje | Deny, Audit, Append\*, DeployIfNotExist\*, AuditIfNotExist\* | False | Odpovídající |
| Nová | Audit, AuditIfNotExist\* | True | Nevyhovující předpisům |
| Nová | Audit, AuditIfNotExist\* | False | Odpovídající |

\* Účinky Append, DeployIfNotExist a AuditIfNotExist vyžadují, aby byl příkaz IF nastaven na TRUE.
Tyto účinky také vyžadují, aby existovala podmínka, která musí nabývat hodnoty FALSE, aby byla zásada vyhodnocena jako Nevyhovující předpisům. Pokud má hodnotu TRUE, aktivuje podmínka IF vyhodnocení podmínky existence pro související prostředky.

## <a name="clean-up-resources"></a>Vyčištění prostředků

K odebrání vytvořeného přiřazení použijte následující postup:

1. Na levé straně stránky služby Azure Policy vyberte **Dodržování předpisů** (nebo **Přiřazení**) a vyhledejte přiřazení zásady **Audit virtuálních počítačů, které nepoužívají spravované disky**, které jste vytvořili.

1. Klikněte pravým tlačítkem na **Auditovat virtuální počítače, které nepoužívají správu zásad spravovaných disků** , a vyberte **Odstranit přiřazení**.

   :::image type="content" source="./media/assign-policy-portal/delete-assignment.png" alt-text="Odstranění přiřazení ze stránky dodržování předpisů" border="false":::

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste přiřadili definici zásady k oboru prostředků a vyhodnotili jste její sestavu dodržování předpisů.
Definice zásady ověří, že všechny prostředky v oboru jsou v souladu s předpisy, a určí, které z nich nejsou.

Další informace o přiřazování zásad k ověření, že jsou nové prostředky kompatibilní, najdete v tomto kurzu:

> [!div class="nextstepaction"]
> [Vytváření a správa zásad](./tutorials/create-and-manage.md)