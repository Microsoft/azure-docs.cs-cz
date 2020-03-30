---
title: 'Úvodní příručka: Nové přiřazení zásad pomocí portálu'
description: V tomto rychlém startu použijete portál Azure k vytvoření přiřazení zásad Azure k identifikaci nekompatibilních prostředků.
ms.date: 03/24/2020
ms.topic: quickstart
ms.openlocfilehash: 3a514478f3cefa49c37c3431a80b578dff480584
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "80240005"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources"></a>Úvodní příručka: Vytvoření přiřazení zásad k identifikaci nekompatibilních prostředků

Prvním krokem k porozumění dodržování předpisů v Azure je zjištění stavu vašich prostředků.
Tento rychlý start vás provede procesem vytvoření přiřazení zásady pro identifikaci virtuálních počítačů, které nepoužívají spravované disky.

Na konci tohoto procesu úspěšně identifikujete virtuální počítače, které nepoužívají spravované disky. _Neodpovídají_ přiřazení zásad.

## <a name="prerequisites"></a>Požadavky

Pokud nemáte předplatné Azure, vytvořte si [bezplatný](https://azure.microsoft.com/free/) účet, než začnete.

## <a name="create-a-policy-assignment"></a>Vytvoření přiřazení zásady

V tomto rychlém startu vytvoříte přiřazení zásad a přiřadíte _virtuální počítače auditu, které nepoužívají definici zásad spravovaného disku._

1. Spusťte službu Azure Policy na webu Azure Portal tak, že kliknete na **Všechny služby** a pak vyhledáte a vyberete **Zásady**.

   :::image type="content" source="./media/assign-policy-portal/search-policy.png" alt-text="Hledání zásad ve všech službách" border="false":::

1. Na levé straně stránky služby Azure Policy vyberte **Přiřazení**. Přiřazení je zásada, která byla přiřazena, aby proběhla v rámci zadaného oboru.

   :::image type="content" source="./media/assign-policy-portal/select-assignments.png" alt-text="Stránka Vybrat přiřazení na stránce Přehled zásad" border="false":::

1. V horní části stránky **Zásady – Přiřazení** vyberte **Přiřadit zásadu**.

   :::image type="content" source="./media/assign-policy-portal/select-assign-policy.png" alt-text="Přiřazení definice zásady ze stránky Přiřazení" border="false":::

1. Na stránce **Přiřadit zásadu** vyberte **Obor** tak, že kliknete na tři tečky a vyberete skupinu pro správu nebo předplatné. Volitelně můžete vybrat skupinu prostředků. Obor určuje, pro které prostředky nebo skupiny prostředků se toto přiřazení zásady bude vynucovat. Pak v dolní části stránky **Obor** klikněte na **Vybrat**.

   Tento příklad používá předplatné **Contoso.** Vaše předplatné se bude lišit.

1. Prostředky je možné vyloučit na základě **oboru**. **Vyloučení** začínají na úrovni o jednu nižší, než je úroveň **oboru**. **Vyloučení** jsou volitelná, takže toto pole prozatím ponechte prázdné.

1. Výběrem tří teček **Definice zásady** otevřete seznam dostupných definic. Služba Azure Policy obsahuje integrované definice zásad, které můžete použít. K dispozici je jich celá řada, například:

   - Vynucovat použití značky a její hodnoty
   - Použít značku a její hodnotu
   - Zdědit značku ze skupiny prostředků, pokud chybí

   Částečný seznam dostupných předdefinovaných zásad najdete v [tématu ukázky zásad Azure](./samples/index.md).

1. Prohledejte seznam definic zásad a najděte definici _Audit virtuálních počítačů, které nepoužívají spravované disky_. Klikněte na tuto zásadu a potom na **Vybrat**.

   :::image type="content" source="./media/assign-policy-portal/select-available-definition.png" alt-text="Vyhledání správné definice zásady" border="false":::

1. Do pole **Název přiřazení** se automaticky vyplní název vybrané zásady, který však můžete změnit. Pro účely tohoto příkladu ponechte _Audit virtuálních počítačů, které nepoužívají spravované disky_. Volitelně můžete přidat také **Popis**. Popis obsahuje podrobnosti o tomto přiřazení zásady.
   Pole **Přiřadil** se automaticky vyplní podle toho, kdo je přihlášený. Toto pole je volitelné, takže do něj můžete zadat vlastní hodnoty.

1. Políčko **Vytvořit spravovanou identitu** ponechte nezaškrtnuté. Toto políčko _musí_ být zaškrtnuto, pokud zásada nebo iniciativa obsahuje zásadu s efektem [deployIfNotExists.](./concepts/effects.md#deployifnotexists) Vzhledem k tomu, že zásady použité pro tento rychlý start nejsou, ponechte ji prázdnou. Další informace najdete v tématech věnovaných [spravovaným identitám](../../active-directory/managed-identities-azure-resources/overview.md) a [principu fungování zabezpečení náprav](./how-to/remediate-resources.md#how-remediation-security-works).

1. Klikněte na **Přiřadit**.

Nyní jste připraveni identifikovat nekompatibilní prostředky, abyste pochopili stav dodržování předpisů vašeho prostředí.

## <a name="identify-non-compliant-resources"></a>Identifikace prostředků, které nedodržují předpisy

V levé části stránky vyberte **Dodržování předpisů.** Potom vyhledejte **virtuální počítače auditu, které nepoužívají** přiřazení zásad spravované disky, které jste vytvořili.

:::image type="content" source="./media/assign-policy-portal/policy-compliance.png" alt-text="Podrobnosti o dodržování předpisů na stránce Dodržování zásad" border="false":::

Pokud existují existující prostředky, které nejsou kompatibilní s tímto novým **přiřazením,** zobrazí se v části Nekompatibilní prostředky .

Pokud se napříč stávajícími prostředky vyhodnotí nějaká podmínka a zjistí hodnotu True, takové prostředky se označí jako nekompatibilní s příslušnou zásadou. Následující tabulka ukazuje, jak různé účinky zásad pracují s vyhodnocením podmínek pro zjištění výsledného stavu dodržování předpisů. I když nevidíte logiku hodnocení na webu Azure Portal, zobrazí se výsledky stavu dodržování předpisů. Výsledný stav je buď kompatibilní, nebo nekompatibilní.

| **Stav prostředku** | **Účinek** | **Vyhodnocení zásad** | **Stav dodržování předpisů** |
| --- | --- | --- | --- |
| Existuje | Deny, Audit, Append\*, DeployIfNotExist\*, AuditIfNotExist\* | True | Nevyhovující předpisům |
| Existuje | Deny, Audit, Append\*, DeployIfNotExist\*, AuditIfNotExist\* | False | Odpovídající |
| Nová | Audit, AuditIfNotExist\* | True | Nevyhovující předpisům |
| Nová | Audit, AuditIfNotExist\* | False | Odpovídající |

\* Účinky Append, DeployIfNotExist a AuditIfNotExist vyžadují, aby byl příkaz IF nastaven na TRUE.
Tyto účinky také vyžadují, aby existovala podmínka, která musí nabývat hodnoty FALSE, aby byla zásada vyhodnocena jako Nevyhovující předpisům. Pokud má hodnotu TRUE, aktivuje podmínka IF vyhodnocení podmínky existence pro související prostředky.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Chcete-li odebrat vytvořené přiřazení, postupujte takto:

1. Na levé straně stránky služby Azure Policy vyberte **Dodržování předpisů** (nebo **Přiřazení**) a vyhledejte přiřazení zásady **Audit virtuálních počítačů, které nepoužívají spravované disky**, které jste vytvořili.

1. Klepněte pravým tlačítkem myši na **moduly Auditovat virtuální počítače, které nepoužívají** přiřazení zásad spravovaného disku, a vyberte příkaz **Odstranit přiřazení**.

   :::image type="content" source="./media/assign-policy-portal/delete-assignment.png" alt-text="Odstranění přiřazení ze stránky Dodržování předpisů" border="false":::

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste přiřadili definici zásady k oboru prostředků a vyhodnotili jste její sestavu dodržování předpisů.
Definice zásadověřuje, že všechny prostředky v oboru jsou kompatibilní a identifikuje, které z nich nejsou.

Další informace o přiřazení zásad k ověření, že nové prostředky jsou kompatibilní, pokračujte v kurzu pro:

> [!div class="nextstepaction"]
> [Vytváření a správa zásad](./tutorials/create-and-manage.md)