---
title: Kurz – nové přiřazení zásad s Azure Portal
description: V tomto kurzu použijete Azure Portal k vytvoření přiřazení Azure Policy k identifikaci prostředků, které nedodržují předpisy.
ms.topic: tutorial
ms.date: 10/07/2020
ms.openlocfilehash: 9a07e490525ce532f8f843b30b3b83715e65ce3c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "91826592"
---
# <a name="tutorial-create-a-policy-assignment-to-identify-non-compliant-resources"></a>Kurz: vytvoření přiřazení zásady pro identifikaci prostředků, které nedodržují předpisy

Prvním krokem k porozumění dodržování předpisů v Azure je zjištění stavu vašich prostředků. Azure Policy podporuje auditování stavu serveru s podporou ARC pomocí zásad konfigurace hostů. Zásady konfigurace hosta nepoužívají konfigurace, auditují pouze nastavení v rámci počítače. Tento kurz vás provede procesem vytvoření a přiřazení zásady a určení, které ze serverů s povoleným obloukem nemají nainstalovaného agenta Log Analytics.

Na konci tohoto procesu úspěšně identifikujete počítače, které nemají nainstalovaného agenta Log Analytics pro systém Windows nebo Linux. _Neodpovídají_ přiřazení zásad.

## <a name="prerequisites"></a>Požadavky

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="create-a-policy-assignment"></a>Vytvoření přiřazení zásad

V tomto kurzu vytvoříte přiřazení zásady a přiřadíte _\[ verzi Preview]: Agent Log Analytics by měl být nainstalovaný v definici zásad pro počítače se systémem Linux ARC Azure_ .

1. Spusťte službu Azure Policy na webu Azure Portal tak, že kliknete na **Všechny služby** a pak vyhledáte a vyberete **Zásady**.

   :::image type="content" source="./media/tutorial-assign-policy-portal/search-policy.png" alt-text="Vyhledat zásady ve všech službách" border="false":::

1. Na levé straně stránky služby Azure Policy vyberte **Přiřazení**. Přiřazení je zásada, která byla přiřazena, aby proběhla v rámci zadaného oboru.

   :::image type="content" source="./media/tutorial-assign-policy-portal/select-assignment.png" alt-text="Stránka pro výběr přiřazení na stránce Přehled zásad" border="false":::

1. V horní části stránky **Zásady – Přiřazení** vyberte **Přiřadit zásadu**.

   :::image type="content" source="./media/tutorial-assign-policy-portal/select-assign-policy.png" alt-text="Přiřazení definice zásady ze stránky přiřazení" border="false":::

1. Na stránce **Přiřadit zásadu** vyberte **Obor** tak, že kliknete na tři tečky a vyberete skupinu pro správu nebo předplatné. Volitelně můžete vybrat skupinu prostředků. Obor určuje, pro které prostředky nebo seskupení prostředků se toto přiřazení zásady bude vynucovat. Pak v dolní části stránky **Obor** klikněte na **Vybrat**.

   V tomto příkladu se používá předplatné **partnera Aerospace** . Vaše předplatné se bude lišit.

1. Prostředky je možné vyloučit na základě **oboru**. **Vyloučení** začínají na úrovni o jednu nižší, než je úroveň **oboru**. **Vyloučení** jsou volitelná, takže toto pole prozatím ponechte prázdné.

1. Výběrem tří teček **Definice zásady** otevřete seznam dostupných definic. Služba Azure Policy obsahuje integrované definice zásad, které můžete použít. K dispozici je jich celá řada, například:

   - Vynucovat použití značky a její hodnoty
   - Použít značku a její hodnotu
   - Zdědit značku ze skupiny prostředků, pokud chybí

   Částečný seznam dostupných integrovaných zásad najdete v tématu [Azure Policy Samples](../../../governance/policy/samples/index.md).

1. Pokud jste povolili agenta serverů s podporou ARC na počítači se systémem Windows, Projděte si seznam definic zásad a vyhledejte _\[ verzi Preview]: Log Analytics agenta by se měl nainstalovat do definice počítačů s Windows Azure ARC_ . V případě počítače se systémem Linux Najděte odpovídající _\[ verzi Preview]: Agent Log Analytics by měl být nainstalovaný v definici zásad pro počítače se systémem Linux Azure ARC_ . Klikněte na tuto zásadu a potom na **Vybrat**.

   :::image type="content" source="./media/tutorial-assign-policy-portal/select-available-definition.png" alt-text="Vyhledání správné definice zásady" border="false":::

1. Do pole **Název přiřazení** se automaticky vyplní název vybrané zásady, který však můžete změnit. V tomto příkladu nechejte _\[ Preview]: Agent Log Analytics by měl být nainstalovaný na vašich počítačích Microsoft Azure ARC nebo ve_ _\[ verzi Preview]: Agent Log Analytics by měl být nainstalovaný na počítačích se systémem Linux Azure ARC_ v závislosti na tom, který z nich jste vybrali. Volitelně můžete přidat také **Popis**. Popis obsahuje podrobnosti o tomto přiřazení zásady.
   Pole **Přiřadil** se automaticky vyplní podle toho, kdo je přihlášený. Toto pole je volitelné, takže do něj můžete zadat vlastní hodnoty.

1. Políčko **Vytvořit spravovanou identitu** ponechte nezaškrtnuté. Toto políčko _musí_ být zaškrtnuto, pokud zásada nebo iniciativa zahrnuje zásadu s efektem [deployIfNotExists](../../../governance/policy/concepts/effects.md#deployifnotexists) . Protože zásady použité pro tento rychlý Start nejsou, ponechte pole prázdné. Další informace najdete v tématech věnovaných [spravovaným identitám](../../../active-directory/managed-identities-azure-resources/overview.md) a [principu fungování zabezpečení náprav](../../../governance/policy/how-to/remediate-resources.md#how-remediation-security-works).

1. Klikněte na **Přiřadit**.

Nyní můžete identifikovat nekompatibilní prostředky, abyste pochopili stav dodržování předpisů vašeho prostředí.

## <a name="identify-non-compliant-resources"></a>Zjištění nevyhovujících prostředků

Na levé straně stránky vyberte **dodržování předpisů** . Pak vyhledejte **\[ verzi Preview]: Agent Log Analytics by měl být nainstalovaný na vašich počítačích s Windows Azure ARC nebo ve** **\[ verzi Preview]: Log Analytics agent by měl být nainstalovaný na přiřazeném přiřazení zásady pro počítače se systémem Linux Azure ARC** .

:::image type="content" source="./media/tutorial-assign-policy-portal/policy-compliance.png" alt-text="Podrobnosti o kompatibilitě na stránce dodržování zásad" border="false":::

Pokud nějaké existující prostředky nedodržují předpisy tohoto nového přiřazení, zobrazí se v části **nekompatibilní prostředky**.

Pokud se napříč stávajícími prostředky vyhodnotí nějaká podmínka a zjistí hodnotu True, takové prostředky se označí jako nekompatibilní s příslušnou zásadou. Následující tabulka ukazuje, jak různé účinky zásad pracují s vyhodnocením podmínek pro zjištění výsledného stavu dodržování předpisů. I když se v Azure Portal logika hodnocení nezobrazuje, zobrazí se výsledky stavu dodržování předpisů. Výsledný stav je buď kompatibilní, nebo nekompatibilní.

| **Stav prostředku** | **Účinek** | **Vyhodnocení zásad** | **Stav dodržování předpisů** |
| --- | --- | --- | --- |
| Existuje | Deny, Audit, Append\*, DeployIfNotExist\*, AuditIfNotExist\* | Ano | Nevyhovující předpisům |
| Existuje | Deny, Audit, Append\*, DeployIfNotExist\*, AuditIfNotExist\* | Ne | Odpovídající |
| Nová | Audit, AuditIfNotExist\* | Ano | Nevyhovující předpisům |
| Nová | Audit, AuditIfNotExist\* | Ne | Odpovídající |

\* Účinky Append, DeployIfNotExist a AuditIfNotExist vyžadují, aby byl příkaz IF nastaven na TRUE.
Tyto účinky také vyžadují, aby existovala podmínka, která musí nabývat hodnoty FALSE, aby byla zásada vyhodnocena jako Nevyhovující předpisům. Pokud má hodnotu TRUE, aktivuje podmínka IF vyhodnocení podmínky existence pro související prostředky.

## <a name="clean-up-resources"></a>Vyčištění prostředků

K odebrání vytvořeného přiřazení použijte následující postup:

1. Na levé straně stránky Azure Policy vyberte **dodržování předpisů** (nebo **přiřazení**) a vyhledejte **\[ verzi Preview]: Agent Log Analytics by měl být nainstalovaný na vašich počítačích s Windows Azure ARC nebo ve** **\[ verzi Preview]: Agent na platformě Linux Azure ARC, který jste vytvořili, by měl být nainstalovaný Log Analytics agenta** .

1. Klikněte pravým tlačítkem na přiřazení zásady a vyberte **Odstranit přiřazení**.

   :::image type="content" source="./media/tutorial-assign-policy-portal/delete-assignment.png" alt-text="Odstranění přiřazení ze stránky dodržování předpisů" border="false":::

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste přiřadili definici zásady k oboru a vyhodnotili sestavu dodržování předpisů. Definice zásady ověří, že všechny prostředky v oboru jsou v souladu s předpisy, a určí, které z nich nejsou. Teď jste připraveni monitorovat počítače serverů s podporou ARC Azure pomocí Azure Monitor pro virtuální počítače.

Pokud se chcete dozvědět, jak monitorovat a sledovat výkon, běžící proces a jejich závislosti z počítače, přejděte k kurzu:

> [!div class="nextstepaction"]
> [Povolit Azure Monitor pro virtuální počítače](tutorial-enable-vm-insights.md)