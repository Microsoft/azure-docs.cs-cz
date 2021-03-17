---
title: 'Rychlý Start: nové přiřazení zásad s portálem'
description: V tomto rychlém startu použijete Azure Portal k vytvoření přiřazení Azure Policy k identifikaci prostředků, které nedodržují předpisy.
ms.date: 01/29/2021
ms.topic: quickstart
ms.openlocfilehash: e5cbf31e897b5be404327efa254eb90ead990f5f
ms.sourcegitcommit: 54e1d4cdff28c2fd88eca949c2190da1b09dca91
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2021
ms.locfileid: "99220883"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources"></a>Rychlý Start: vytvoření přiřazení zásady pro identifikaci prostředků, které nedodržují předpisy

Prvním krokem k porozumění dodržování předpisů v Azure je zjištění stavu vašich prostředků.
Tento rychlý start vás provede procesem vytvoření přiřazení zásady pro identifikaci virtuálních počítačů, které nepoužívají spravované disky.

Na konci tohoto procesu úspěšně identifikujete virtuální počítače, které nepoužívají spravované disky. _Neodpovídají_ přiřazení zásad.

## <a name="prerequisites"></a>Požadavky

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="create-a-policy-assignment"></a>Vytvoření přiřazení zásad

V tomto rychlém startu vytvoříte přiřazení zásady a přiřadíte _virtuální počítače auditu, které nepoužívají definici zásad spravované disky_ .

1. Spusťte službu Azure Policy v Azure Portal tak, že vyberete **všechny služby** a pak vyhledáte a vyberete **zásadu**.

   :::image type="content" source="./media/assign-policy-portal/search-policy.png" alt-text="Snímek obrazovky s vyhledáváním zásad ve všech službách" border="false":::

1. Na levé straně stránky služby Azure Policy vyberte **Přiřazení**. Přiřazení je zásada, která byla přiřazena, aby proběhla v rámci zadaného oboru.

   :::image type="content" source="./media/assign-policy-portal/select-assignments.png" alt-text="Snímek obrazovky s výběrem stránky přiřazení ze stránky přehled zásad" border="false":::

1. V horní části stránky **Zásady – Přiřazení** vyberte **Přiřadit zásadu**.

   :::image type="content" source="./media/assign-policy-portal/select-assign-policy.png" alt-text="Snímek obrazovky s výběrem možnosti přiřadit zásadu ze stránky přiřazení" border="false":::

1. Na stránce **přiřadit zásadu** nastavte **obor** tak, že vyberete tři tečky a pak vyberete buď skupinu pro správu nebo předplatné. Volitelně můžete vybrat skupinu prostředků. Obor určuje, pro které prostředky nebo seskupení prostředků se toto přiřazení zásady bude vynucovat. Pak použijte tlačítko **Vybrat** v dolní části stránky **Rozsah** .

   V tomto příkladu se používá předplatné **Contoso** . Vaše předplatné se bude lišit.

1. Prostředky je možné vyloučit na základě **oboru**. **Vyloučení** začínají na úrovni o jednu nižší, než je úroveň **oboru**. **Vyloučení** jsou volitelná, takže toto pole prozatím ponechte prázdné.

1. Výběrem tří teček **Definice zásady** otevřete seznam dostupných definic. Služba Azure Policy obsahuje integrované definice zásad, které můžete použít. K dispozici je jich celá řada, například:

   - Vynucovat použití značky a její hodnoty
   - Použít značku a její hodnotu
   - Zdědit značku ze skupiny prostředků, pokud chybí

   Částečný seznam dostupných integrovaných zásad najdete v tématu [Azure Policy Samples](./samples/index.md).

1. Prohledejte seznam definic zásad a najděte definici _Audit virtuálních počítačů, které nepoužívají spravované disky_. Vyberte tuto zásadu a pak použijte tlačítko **Vybrat** .

   :::image type="content" source="./media/assign-policy-portal/select-available-definition.png" alt-text="Snímek obrazovky s filtrováním dostupných definic" border="false":::

1. Do pole **Název přiřazení** se automaticky vyplní název vybrané zásady, který však můžete změnit. Pro účely tohoto příkladu ponechte _Audit virtuálních počítačů, které nepoužívají spravované disky_. Volitelně můžete přidat také **Popis**. Popis obsahuje podrobnosti o tomto přiřazení zásady.
   Pole **Přiřadil** se automaticky vyplní podle toho, kdo je přihlášený. Toto pole je volitelné, takže do něj můžete zadat vlastní hodnoty.

1. Nechte vynucení zásad _zapnuté_. Další informace najdete v tématu věnovaném [přiřazení zásad – režim vynucení](./concepts/assignment-structure.md#enforcement-mode).

1. V dolní části stránky vyberte **Další** a na kartě **parametry** v horní části stránky se přejděte k dalšímu segmentu Průvodce přiřazením.

1. Pokud definice zásady vybraná na kartě **základy** obsahuje parametry, nakonfigurují se na této kartě. Vzhledem k tomu, že _virtuální počítače pro audit, které nepoužívají spravované disky_ , nemají žádné parametry, v dolní části stránky **nebo v horní** části stránky vyberte možnost **Další** a přejděte k dalšímu segmentu Průvodce přiřazením.

1. Políčko **Vytvořit spravovanou identitu** ponechte nezaškrtnuté. Toto políčko _musí_ být zaškrtnuto, pokud zásada nebo iniciativa zahrnuje zásadu s efektem [deployIfNotExists](./concepts/effects.md#deployifnotexists) nebo [Modify](./concepts/effects.md#modify) . Protože zásady použité pro tento rychlý Start nejsou, ponechte pole prázdné. Další informace najdete v tématech věnovaných [spravovaným identitám](../../active-directory/managed-identities-azure-resources/overview.md) a [principu fungování zabezpečení náprav](./how-to/remediate-resources.md#how-remediation-security-works).

1. V dolní části stránky nebo na kartě **zprávy o neshodě** v horní části stránky vyberte **Další** a přejděte k dalšímu segmentu Průvodce přiřazením.

1. Nastavte **zprávu nesplňující požadavky** na _virtuální počítače by měly používat spravovaný disk_. Tato vlastní zpráva se zobrazí, pokud je prostředek odepřený nebo nevyhovujícím prostředkům během pravidelného vyhodnocení.

1. V dolní části stránky klikněte na tlačítko **Další** nebo na kartě **Revize + vytvořit** v horní části stránky přejděte k dalšímu segmentu Průvodce přiřazením.

1. Zkontrolujte vybrané možnosti a potom v dolní části stránky vyberte **vytvořit** .

Nyní můžete identifikovat nekompatibilní prostředky, abyste pochopili stav dodržování předpisů vašeho prostředí.

## <a name="identify-non-compliant-resources"></a>Zjištění nevyhovujících prostředků

Na levé straně stránky vyberte **dodržování předpisů** . Pak vyhledejte _auditované virtuální počítače, které nepoužívají přiřazení zásad spravovaných disků_ , které jste vytvořili.

:::image type="content" source="./media/assign-policy-portal/policy-compliance.png" alt-text="Snímek obrazovky s podrobnostmi o kompatibilitě na stránce dodržování zásad" border="false":::

Pokud nějaké existující prostředky nedodržují předpisy tohoto nového přiřazení, zobrazí se v části **nekompatibilní prostředky**.

Pokud se napříč stávajícími prostředky vyhodnotí nějaká podmínka a zjistí hodnotu True, takové prostředky se označí jako nekompatibilní s příslušnou zásadou. Následující tabulka ukazuje, jak různé účinky zásad pracují s vyhodnocením podmínek pro zjištění výsledného stavu dodržování předpisů. I když se v Azure Portal logika hodnocení nezobrazuje, zobrazí se výsledky stavu dodržování předpisů. Výsledný stav je buď kompatibilní, nebo nekompatibilní.

| Stav prostředku | Účinek | Vyhodnocení zásad | Stav dodržování předpisů |
| --- | --- | --- | --- |
| Nové nebo aktualizované | Audit, úprava, AuditIfNotExist | Ano | Nevyhovující předpisům |
| Nové nebo aktualizované | Audit, úprava, AuditIfNotExist | Ne | Odpovídající |
| Existuje | Deny, audit, připojit, upravit, DeployIfNotExist, AuditIfNotExist | Ano | Nevyhovující předpisům |
| Existuje | Deny, audit, připojit, upravit, DeployIfNotExist, AuditIfNotExist | Ne | Odpovídající |

> [!NOTE]
> Účinky DeployIfNotExist a AuditIfNotExist vyžadují, aby příkaz IF byl pravdivý a aby podmínka existence neodpovídala hodnotě FALSE. Pokud má hodnotu TRUE, aktivuje podmínka IF vyhodnocení podmínky existence pro související prostředky.

## <a name="clean-up-resources"></a>Vyčištění prostředků

K odebrání vytvořeného přiřazení použijte následující postup:

1. Na levé straně stránky služby Azure Policy vyberte **Dodržování předpisů** (nebo **Přiřazení**) a vyhledejte přiřazení zásady _Audit virtuálních počítačů, které nepoužívají spravované disky_, které jste vytvořili.

1. Klikněte pravým tlačítkem na _Auditovat virtuální počítače, které nepoužívají správu zásad spravovaných disků_ , a vyberte **Odstranit přiřazení**.

   :::image type="content" source="./media/assign-policy-portal/delete-assignment.png" alt-text="Snímek obrazovky s použitím kontextové nabídky k odstranění přiřazení ze stránky dodržování předpisů." border="false":::

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste přiřadili definici zásady k oboru prostředků a vyhodnotili jste její sestavu dodržování předpisů.
Definice zásady ověří, že všechny prostředky v oboru jsou v souladu s předpisy, a určí, které z nich nejsou.

Další informace o přiřazování zásad k ověření, že jsou nové prostředky kompatibilní, najdete v tomto kurzu:

> [!div class="nextstepaction"]
> [Vytváření a správa zásad](./tutorials/create-and-manage.md)