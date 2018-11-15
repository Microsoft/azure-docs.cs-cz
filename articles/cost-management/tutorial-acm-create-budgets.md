---
title: Kurz – vytvoření a Správa rozpočtů Azure | Dokumentace Microsoftu
description: Tento kurz pomůže plánu a účet pro náklady na služby Azure, které skutečně využijete.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 11/02/2018
ms.topic: conceptual
ms.service: cost-management
manager: dougeby
ms.custom: ''
ms.openlocfilehash: 49341b320df98bb08ee4f5c4ee061a51bec29ff2
ms.sourcegitcommit: db2cb1c4add355074c384f403c8d9fcd03d12b0c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/15/2018
ms.locfileid: "51686156"
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

Rozpočty jsou k dispozici pro všechny zákazníky Azure EA. Pro vytváření a správu rozpočtů musíte mít k předplatnému Azure EA přístup pro čtení. Můžete vytvořit jednotlivé rozpočty pro skupiny prostředků a předplatných EA. Nelze však vytvořit rozpočty pro fakturační účty EA.

Následující oprávnění Azure se podporuje na předplatné pro rozpočty uživatelů a skupin:

- Vlastník – Může vytvářet, upravovat a odstraňovat rozpočty u předplatných.
- Přispěvatel – Může vytvářet, upravovat a odstraňovat svoje vlastní rozpočty. Může měnit částky rozpočtu pro rozpočty, které vytvářejí jiní uživatelé.
- Čtenář – Může zobrazovat rozpočty, ke kterým má oprávnění.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

- Přihlaste se k webu Azure Portal na adrese http://portal.azure.com.

## <a name="create-a-budget-in-the-azure-portal"></a>Vytvořit rozpočet na webu Azure Portal

Rozpočet předplatné Azure můžete vytvořit pro měsíčně, čtvrtletně nebo roční období. Navigační obsah na webu Azure Portal určuje, zda rozpočet pro předplatné nebo skupinu prostředků vytvoříte.

Na webu Azure Portal, přejděte na **Správa nákladů a fakturace** &gt; **předplatná** &gt; vybrat odběr, který &gt; **rozpočty**. V tomto příkladu je rozpočtu, který vytvoříte pro předplatné, které jste vybrali.

Po vytvoření rozpočty, zobrazí se vaše aktuální útratu proti nim jednoduché zobrazení.

Klikněte na tlačítko **Add** (Přidat).

![Správa rozpočtů nákladů](./media/tutorial-acm-create-budgets/budgets01.png)

V **vytvořit rozpočtu** okno, zadejte název rozpočtu a částka rozpočtu. Zvolte buď měsíčně, čtvrtletně, nebo roční dobu trvání období. V dalším kroku vyberte koncové datum. Rozpočty vyžadují aspoň jednu cenu prahové hodnoty (% rozpočtu) a odpovídající e-mailovou adresu. Může volitelně zahrnovat až pět prahové hodnoty a pět e-mailových adres v jedné rozpočtu. Pokud je dodržena prahová hodnota rozpočtu, e-mailová oznámení dostávají obvykle za méně než osm hodin.

Tady je příklad vytvoření měsíční rozpočet pro $4500. Když je dosaženo 90 % z rozpočtu získá vygenerována výstraha e-mailu.

![Příklad měsíční rozpočtu](./media/tutorial-acm-create-budgets/monthly-budget01.png)

Při vytváření čtvrtletní rozpočtu funguje stejným způsobem jako měsíční rozpočtu. Rozdíl je, že je rozpočtu částka za čtvrtletí rovnoměrně rozdělené mezi tři měsíce ve čtvrtletí. Jak byste asi očekávali, roční částka rozpočtu rovnoměrně mezi všechny 12 měsíců kalendářního roku.

Aktuální útratu s rozpočty se aktualizuje pokaždé, když přijme aktualizovaná data fakturační náklady na správu. Obvykle, každý den.

![Aktuální útratu s rozpočty](./media/tutorial-acm-create-budgets/budgets-current-spending.png)

Po vytvoření rozpočtu, se zobrazí v analýze nákladů. Zobrazení vašemu rozpočtu ve vztahu k vaší útraty trend je jedním z prvních kroků při spuštění [analyzovat vaše náklady a výdaje](quick-acm-cost-analysis.md).

![Rozpočtu ukazuje analýzy nákladů](./media/tutorial-acm-create-budgets/cost-analysis.png)

V předchozím příkladu jste vytvořili rozpočet pro předplatné. Můžete však také vytvořit rozpočet pro skupinu prostředků. Pokud chcete vytvořit rozpočet pro skupinu prostředků, přejděte na **Správa nákladů a fakturace** &gt; **předplatná** &gt; Vybrat předplatné > **prostředků skupiny** > vyberte skupinu prostředků > **rozpočty** > a pak **přidat** rozpočtu.

## <a name="edit-a-budget"></a>Upravit rozpočtu

V závislosti na úrovni přístupu, kterou máte můžete upravit rozpočtu, chcete-li změnit jeho vlastnosti. V následujícím příkladu některé vlastnosti jsou jen pro čtení vzhledem k tomu, že má uživatel jenom oprávnění přispěvatele k předplatnému. V současné době **datum vypršení platnosti** zakázána a nemůže modifikovat po nastavení.

![Upravit rozpočtu – oprávnění Přispěvatel](./media/tutorial-acm-create-budgets/edit-budget.png)


## <a name="next-steps"></a>Další postup

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Vytvořit rozpočet na webu Azure Portal
> * Upravit rozpočtu

Přejděte k dalšímu kurzu, kde vytvořit opakované export pro vaše datům služby cost management.

> [!div class="nextstepaction"]
> [Vytvoření a správa exportovaná data](tutorial-export-acm-data.md)
