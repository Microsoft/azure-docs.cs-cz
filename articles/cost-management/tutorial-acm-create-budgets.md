---
title: Kurz – vytvoření a Správa rozpočtů Azure | Dokumentace Microsoftu
description: Tento kurz pomůže plánu a účet pro náklady na služby Azure, které skutečně využijete.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 12/07/2018
ms.topic: conceptual
ms.service: cost-management
manager: dougeby
ms.custom: seodec18
ms.openlocfilehash: fc38c6278590e213674a2522169f988eaeea9cc4
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53081349"
---
# <a name="tutorial-create-and-manage-azure-budgets"></a>Kurz: Vytvoření a Správa rozpočtů Azure

Rozpočty ve službě Cost Management pomáhají plánovat a řídit odpovědnost v organizaci. Pomocí rozpočtů můžete zodpovídat za služby Azure, které využíváte nebo k jejichž odběru jste po určitou dobu přihlášení. Pomáhají informovat ostatní o jejich útraty aktivně spravovat náklady a sledovat, jak útraty postupuje v čase. Při překročení prahových hodnot rozpočtu, který jste vytvořili, se aktivují jenom oznámení. Žádná z vašich prostředků neovlivní a spotřebu není zastavená. Rozpočty můžete porovnat a sledovat výdaje podle analýzu nákladů.

Rozpočty automaticky resetovat na konci období (měsíčně, čtvrtletně nebo ročně) pro velkou rozpočtu vyberete datum vypršení platnosti v budoucnu. Protože resetují se stejnou velikostí rozpočtu, budete muset vytvořit samostatné rozpočty při rozpočtu peněžních hodnot se liší v budoucích obdobích.

Příklady v tomto kurzu vás provede procesem vytvoření a úprava rozpočet pro předplatné Azure Enterprise Agreement (EA).

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvořit rozpočet na webu Azure Portal
> * Upravit rozpočtu

## <a name="prerequisites"></a>Požadavky

Rozpočty jsou k dispozici pro všechny zákazníky Azure EA. Musíte mít oprávnění ke čtení pro rozpočty zobrazení předplatného Azure EA. Vytvoření a Správa rozpočtů, musí mít oprávnění přispěvatele. Můžete vytvořit jednotlivé rozpočty pro skupiny prostředků a předplatných EA. Nelze však vytvořit rozpočty pro fakturační účty EA.

Následující oprávnění Azure se podporuje na předplatné pro rozpočty uživatelů a skupin:

- Vlastník – Může vytvářet, upravovat a odstraňovat rozpočty u předplatných.
- Přispěvatel a přispěvatelů Cost Management – můžete vytvořit, upravit nebo odstranit vlastní rozpočty. Může měnit částky rozpočtu pro rozpočty, které vytvářejí jiní uživatelé.
- Čtenář a čtečky Cost Management – můžete zobrazit rozpočty, ke kterým mají oprávnění.

Další informace o přiřazování oprávnění k datům služby Cost Management najdete v tématu [přiřazení přístupu k datům služby Cost Management](assign-access-acm-data.md).

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

- Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.

## <a name="create-a-budget-in-the-azure-portal"></a>Vytvořit rozpočet na webu Azure Portal

Rozpočet předplatné Azure můžete vytvořit pro měsíčně, čtvrtletně nebo roční období. Navigační obsah na webu Azure Portal určuje, zda rozpočet pro předplatné nebo skupinu prostředků vytvoříte.

Na webu Azure Portal, přejděte na **Správa nákladů a fakturace** &gt; **předplatná** &gt; vybrat odběr, který &gt; **rozpočty**. V tomto příkladu je rozpočtu, který vytvoříte pro předplatné, které jste vybrali.

Po vytvoření rozpočty, zobrazí se vaše aktuální útratu proti nim jednoduché zobrazení.

Klikněte na tlačítko **Add** (Přidat).

![Cost Management rozpočty uvedené na webu Azure Portal](./media/tutorial-acm-create-budgets/budgets01.png)

V **vytvořit rozpočtu** okno, zadejte název rozpočtu a částka rozpočtu. Zvolte buď měsíčně, čtvrtletně, nebo roční dobu trvání období. V dalším kroku vyberte koncové datum. Rozpočty vyžadují aspoň jednu cenu prahové hodnoty (% rozpočtu) a odpovídající e-mailovou adresu. Může volitelně zahrnovat až pět prahové hodnoty a pět e-mailových adres v jedné rozpočtu. Pokud je dodržena prahová hodnota rozpočtu, e-mailová oznámení dostávají obvykle za méně než osm hodin.

Tady je příklad vytvoření měsíční rozpočet pro $4500. Když je dosaženo 90 % z rozpočtu získá vygenerována výstraha e-mailu.

![Příklad informace zobrazené v seznamu vytvořit rozpočtu](./media/tutorial-acm-create-budgets/monthly-budget01.png)

Při vytváření čtvrtletní rozpočtu funguje stejným způsobem jako měsíční rozpočtu. Rozdíl je, že je rozpočtu částka za čtvrtletí rovnoměrně rozdělené mezi tři měsíce ve čtvrtletí. Jak byste asi očekávali, roční částka rozpočtu rovnoměrně mezi všechny 12 měsíců kalendářního roku.

Aktuální útratu s rozpočty se aktualizuje pokaždé, když přijme aktualizovaná data fakturační náklady na správu. Obvykle, každý den.

![Informace z příkladu zobrazuje aktuální útratu s rozpočty](./media/tutorial-acm-create-budgets/budgets-current-spending.png)

Po vytvoření rozpočtu, se zobrazí v analýze nákladů. Zobrazení vašemu rozpočtu ve vztahu k vaší útraty trend je jedním z prvních kroků při spuštění [analyzovat vaše náklady a výdaje](quick-acm-cost-analysis.md).

![Příklad snazší plánování rozpočtu a výdaje podle analýzy nákladů](./media/tutorial-acm-create-budgets/cost-analysis.png)

V předchozím příkladu jste vytvořili rozpočet pro předplatné. Můžete však také vytvořit rozpočet pro skupinu prostředků. Pokud chcete vytvořit rozpočet pro skupinu prostředků, přejděte na **Správa nákladů a fakturace** &gt; **předplatná** &gt; Vybrat předplatné > **prostředků skupiny** > vyberte skupinu prostředků > **rozpočty** > a pak **přidat** rozpočtu.

## <a name="edit-a-budget"></a>Upravit rozpočtu

V závislosti na úrovni přístupu, kterou máte můžete upravit rozpočtu, chcete-li změnit jeho vlastnosti. V následujícím příkladu některé vlastnosti jsou jen pro čtení vzhledem k tomu, že má uživatel jenom oprávnění přispěvatele k předplatnému. V současné době **datum vypršení platnosti** zakázána a nemůže modifikovat po nastavení.

![Příklad úpravy rozpočtu, chcete-li změnit různé vlastnosti](./media/tutorial-acm-create-budgets/edit-budget.png)


## <a name="next-steps"></a>Další postup

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Vytvořit rozpočet na webu Azure Portal
> * Upravit rozpočtu

Přejděte k dalšímu kurzu, kde vytvořit opakované export pro vaše datům služby cost management.

> [!div class="nextstepaction"]
> [Vytvoření a správa exportovaná data](tutorial-export-acm-data.md)
