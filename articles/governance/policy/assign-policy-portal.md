---
title: Vytvoření zásady pro audit prostředků pomocí portálu – Azure Policy
description: Tento článek vás provede kroky k vytvoření definice zásady pro identifikaci prostředků, které nedodržují předpisy.
author: DCtheGeek
ms.author: dacoulte
ms.date: 12/06/2018
ms.topic: quickstart
ms.service: azure-policy
ms.openlocfilehash: 6433c5f90b56489e92ec76aab5c9a0d0c6aeb508
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/10/2019
ms.locfileid: "72254825"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources"></a>Rychlý Start: vytvoření přiřazení zásady pro identifikaci prostředků, které nedodržují předpisy

Prvním krokem při porozumění dodržování předpisů v Azure je určení stavu vašich prostředků.
Tento rychlý Start vás provede procesem vytvoření přiřazení zásady pro identifikaci virtuálních počítačů, které nepoužívají spravované disky.

Na konci tohoto procesu úspěšně identifikujete virtuální počítače, které nepoužívají spravované disky. Neodpovídají přiřazení zásad.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný](https://azure.microsoft.com/free/) účet před tím, než začnete.

## <a name="create-a-policy-assignment"></a>Vytvoření přiřazení zásady

V tomto rychlém startu vytvoříte přiřazení zásady a přiřadíte *virtuální počítače auditu, které nepoužívají definici zásad spravované disky* .

1. Spusťte službu Azure Policy v Azure Portal tak, že kliknete na **všechny služby**a pak vyhledáte a vyberete **zásadu**.

   ![Vyhledat zásady ve všech službách](./media/assign-policy-portal/search-policy.png)

1. Na levé straně stránky Azure Policy vyberte **přiřazení** . Přiřazení je zásada, která byla přiřazena k uskutečnění v rámci určitého oboru.

   ![Stránka pro výběr přiřazení na stránce Přehled zásad](./media/assign-policy-portal/select-assignments.png)

1. V horní části stránky **přiřazení zásad** vyberte **přiřadit zásadu** .

   ![Přiřazení definice zásady ze stránky přiřazení](./media/assign-policy-portal/select-assign-policy.png)

1. Na stránce **přiřadit zásadu** vyberte **obor** kliknutím na tři tečky a vyberte skupinu pro správu nebo předplatné. Volitelně můžete vybrat skupinu prostředků. Obor určuje, pro které prostředky nebo skupiny prostředků se toto přiřazení zásady bude uplatňovat. Pak klikněte na tlačítko **Vybrat** v dolní části stránky **Rozsah** .

   V tomto příkladu se používá předplatné **Contoso** . Vaše předplatné se bude lišit.

1. Prostředky je možné vyloučit na základě **oboru**. **Vyloučení** se spustí na jednu úroveň nižší než úroveň **rozsahu**. **Vyloučení** jsou volitelná, takže je teď nechte prázdné.

1. Kliknutím na tři tečky **definice zásad** otevřete seznam dostupných definic. Azure Policy obsahuje integrované definice zásad, které můžete použít. K dispozici je mnoho, například:

   - Vymáhat značku a její hodnotu
   - Použít značku a její hodnotu
   - Vyžadovat SQL Server verzi 12,0

   Částečný seznam dostupných integrovaných zásad najdete v tématu [Azure Policy Samples](./samples/index.md).

1. Procházejte seznamem definice zásad a vyhledejte *virtuální počítače auditu, které nepoužívají definici spravovaných disků* . Klikněte na tuto zásadu a pak klikněte na **Vybrat**.

   ![Najít správnou definici zásady](./media/assign-policy-portal/select-available-definition.png)

1. **Název přiřazení** se automaticky vyplní názvem zásady, který jste vybrali, ale můžete ho změnit. V tomto příkladu ponechte *audit virtuálních počítačů, které nepoužívají spravované disky*. Můžete také přidat volitelný **Popis**. Popis poskytuje podrobnosti o tomto přiřazení zásady.
   **Přiřazeno** automaticky se vyplní na základě toho, kdo je přihlášený. Toto pole je volitelné, takže lze zadat vlastní hodnoty.

1. Ponechte položku **vytvořit spravovanou identitu** nezaškrtnutou. Toto políčko _musí_ být zaškrtnuto, pokud zásada nebo iniciativa zahrnuje zásadu s efektem [deployIfNotExists](./concepts/effects.md#deployifnotexists) . Protože zásady použité pro tento rychlý Start nejsou, ponechte pole prázdné. Další informace najdete v tématech [spravované identity](../../active-directory/managed-identities-azure-resources/overview.md) a [Jak funguje řešení potíží](./how-to/remediate-resources.md#how-remediation-security-works).

1. Klikněte na tlačítko **Přiřadit**.

Nyní můžete identifikovat nekompatibilní prostředky, abyste pochopili stav dodržování předpisů vašeho prostředí.

## <a name="identify-non-compliant-resources"></a>Identifikace prostředků, které nedodržují předpisy

Na levé straně stránky vyberte **dodržování předpisů** . Pak vyhledejte **auditované virtuální počítače, které nepoužívají přiřazení zásad spravovaných disků** , které jste vytvořili.

![Podrobnosti o kompatibilitě na stránce dodržování zásad](./media/assign-policy-portal/policy-compliance.png)

Pokud nějaké existující prostředky nedodržují předpisy tohoto nového přiřazení, zobrazí se v části **nekompatibilní prostředky**.

Když se podmínka vyhodnotí proti stávajícím prostředkům a najde hodnotu true, pak se tyto prostředky označí jako nevyhovující zásadám. Následující tabulka ukazuje, jak různé účinky zásad fungují s hodnocením podmínek pro výsledný stav dodržování předpisů. I když se v Azure Portal logika hodnocení nezobrazuje, zobrazí se výsledky stavu dodržování předpisů. Výsledek stavu dodržování předpisů je buď vyhovující, nebo není kompatibilní.

| **Stav prostředku** | **Až** | **Vyhodnocení zásad** | **Stav dodržování předpisů** |
| --- | --- | --- | --- |
| Neexistuje | Deny, audit, Append @ no__t-0, DeployIfNotExist @ no__t-1, AuditIfNotExist @ no__t-2 | Hodnota TRUE | Nedodržující předpisy |
| Neexistuje | Deny, audit, Append @ no__t-0, DeployIfNotExist @ no__t-1, AuditIfNotExist @ no__t-2 | False (Nepravda) | Kompatibilní |
| Nový | Audit, AuditIfNotExist @ no__t-0 | Hodnota TRUE | Nedodržující předpisy |
| Nový | Audit, AuditIfNotExist @ no__t-0 | False (Nepravda) | Kompatibilní |

\* efekty Append, DeployIfNotExist a AuditIfNotExist vyžadují, aby příkaz IF byl pravdivý.
Účinky také vyžadují, aby podmínka existence nesplňovala předpisy. V případě hodnoty TRUE aktivuje podmínka IF vyhodnocení podmínky existence pro související prostředky.

## <a name="clean-up-resources"></a>Vyčištění prostředků

K odebrání vytvořeného přiřazení použijte následující postup:

1. Na levé straně stránky Azure Policy vyberte **dodržování předpisů** (nebo **přiřazení**) a vyhledejte **auditované virtuální počítače, které nepoužívají přiřazení zásad spravované disky** , které jste vytvořili.

1. Klikněte pravým tlačítkem na **Auditovat virtuální počítače, které nepoužívají zásady spravované disky** a vyberte **Odstranit přiřazení** .

   ![Odstranění přiřazení ze stránky dodržování předpisů](./media/assign-policy-portal/delete-assignment.png)

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste přiřadili definici zásady k oboru a vyhodnotili sestavu dodržování předpisů.
Definice zásady ověří, že všechny prostředky v oboru jsou v souladu s předpisy, a určí, které z nich nejsou.

Další informace o přiřazování zásad k ověření, že jsou nové prostředky kompatibilní, najdete v tomto kurzu:

> [!div class="nextstepaction"]
> [Vytváření a Správa zásad](./tutorials/create-and-manage.md)