---
title: Kurz – vytvoření a Správa rozpočtů Azure | Dokumentace Microsoftu
description: Tento kurz pomůže plánu a účet pro náklady na služby Azure, které skutečně využijete.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 05/14/2019
ms.topic: conceptual
ms.service: cost-management
manager: dougeby
ms.custom: seodec18
ms.openlocfilehash: eab45948b5f931377396d93d93e8955ba0f3e767
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2019
ms.locfileid: "65792842"
---
# <a name="tutorial-create-and-manage-azure-budgets"></a>Kurz: Vytvoření a Správa rozpočtů Azure

Rozpočty ve službě Cost Management pomáhají plánovat a řídit odpovědnost v organizaci. Pomocí rozpočtů můžete zodpovídat za služby Azure, které využíváte nebo k jejichž odběru jste po určitou dobu přihlášení. Pomáhají informovat ostatní o jejich útraty aktivně spravovat náklady a sledovat, jak útraty postupuje v čase. Při překročení prahových hodnot rozpočtu, který jste vytvořili, se aktivují jenom oznámení. Žádná z vašich prostředků neovlivní a spotřebu není zastavená. Rozpočty můžete porovnat a sledovat výdaje podle analýzu nákladů.

Měsíční rozpočty vyhodnocují útraty každé čtyři hodiny. Data a oznámení o spotřebovaných prostředků jsou však k dispozici do osmi hodin.  

Rozpočty automaticky resetovat na konci období (měsíčně, čtvrtletně nebo ročně) pro velkou rozpočtu vyberete datum vypršení platnosti v budoucnu. Protože resetují se stejnou velikostí rozpočtu, budete muset vytvořit samostatné rozpočty při rozpočtu peněžních hodnot se liší v budoucích obdobích.

Příklady v tomto kurzu vás provede procesem vytvoření a úprava rozpočet pro předplatné Azure Enterprise Agreement (EA).

Podívejte [vytvoření rozpočtu monitorování vaší útraty pomocí služby Azure Cost Management](https://www.youtube.com/watch?v=ExIVG_Gr45A) videu se dozvíte, jak můžete vytvořit rozpočty v Azure a monitorováním výdajů.


V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvořit rozpočet na webu Azure Portal
> * Upravit rozpočtu

## <a name="prerequisites"></a>Požadavky

Rozpočty jsou podporované pro různé typy účtů Azure. Chcete-li zobrazit úplný seznam typů podporovaných účtů, najdete v článku [datům Cost managementu pochopit](understand-cost-mgt-data.md). Chcete-li zobrazit rozpočty, potřebují minimálně čtení přístup ke svému účtu Azure.

 Pro předplatná Azure EA musí mít přístup pro čtení k zobrazení rozpočtů. Vytvoření a Správa rozpočtů, musí mít oprávnění přispěvatele. Můžete vytvořit jednotlivé rozpočty pro skupiny prostředků a předplatných EA. Nelze však vytvořit rozpočty pro fakturační účty EA.

Následující oprávnění Azure, nebo oborů, podporuje na předplatné pro rozpočty uživatelů a skupin. Další informace o oborech najdete v tématu [pochopení a práci s obory](understand-work-scopes.md).

- Vlastník – Může vytvářet, upravovat a odstraňovat rozpočty u předplatných.
- Přispěvatel a přispěvatelů Cost Management – můžete vytvořit, upravit nebo odstranit vlastní rozpočty. Může měnit částky rozpočtu pro rozpočty, které vytvářejí jiní uživatelé.
- Čtenář a čtečky Cost Management – můžete zobrazit rozpočty, ke kterým mají oprávnění.

Další informace o přiřazování oprávnění k datům služby Cost Management najdete v tématu [přiřazení přístupu k datům služby Cost Management](assign-access-acm-data.md).

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

- Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.

## <a name="create-a-budget-in-the-azure-portal"></a>Vytvořit rozpočet na webu Azure Portal

Rozpočet předplatné Azure můžete vytvořit pro měsíčně, čtvrtletně nebo roční období. Navigační obsah na webu Azure Portal určuje, zda vytvořit rozpočet pro předplatné nebo skupinu pro správu.

K vytvoření ani zobrazení rozpočtu, otevřete v webu Azure portal a vyberte požadovaný obor **rozpočty** v nabídce. Například, přejděte na **předplatná**, vyberte předplatné, ze seznamu a pak vyberte **rozpočty** v nabídce. Použití **oboru** obranné přepnout do jiného oboru, jako jsou skupiny pro správu v rozpočtech. Další informace o oborech najdete v tématu [pochopení a práci s obory](understand-work-scopes.md).

Po vytvoření rozpočty, zobrazí se vaše aktuální útratu proti nim jednoduché zobrazení.

Klikněte na tlačítko **Add** (Přidat).

![Cost Management rozpočty uvedené na webu Azure Portal](./media/tutorial-acm-create-budgets/budgets01.png)

V **vytvořit rozpočtu** okno, zadejte název rozpočtu a částka rozpočtu. Zvolte buď měsíčně, čtvrtletně, nebo roční dobu trvání období. V dalším kroku vyberte koncové datum. Rozpočty vyžadují aspoň jednu cenu prahové hodnoty (% rozpočtu) a odpovídající e-mailovou adresu. Může volitelně zahrnovat až pět prahové hodnoty a pět e-mailových adres v jedné rozpočtu. Pokud je dodržena prahová hodnota rozpočtu, e-mailová oznámení dostávají obvykle za méně než osm hodin. Další informace o oznámeních najdete v tématu [použití nákladů výstrahy](cost-mgt-alerts-monitor-usage-spending.md).

Pokud máte předplatné s průběžnými platbami, MSDN nebo Visual Studio, nemusí zarovnat fakturačního období vaší faktury ke kalendářnímu měsíci. Pro tyto typy předplatných a skupin prostředků můžete vytvořit rozpočtu, který je v souladu, fakturační období nebo kalendářních měsících. K vytvoření rozpočtu zarovnán fakturační období, vyberte interval resetování fakturační měsíc, fakturace, čtvrtletí nebo roku fakturace. Pokud chcete vytvořit rozpočtu kalendářním měsícům, vyberte období obnovení měsíčně, čtvrtletně nebo ročně.

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

## <a name="trigger-an-action-group"></a>Aktivační událost skupiny akcí

Když vytvoříte nebo upravíte rozpočet pro obor skupiny prostředků nebo předplatného, můžete nakonfigurovat k volání skupiny akcí. Skupina akcí můžete provádět řadu různých akcí při dosažení prahové hodnoty vašeho rozpočtu. Další informace o skupinách akcí najdete v tématu [vytvořit a spravovat skupiny akcí na webu Azure Portal](../azure-monitor/platform/action-groups.md). Další informace o použití služby automation na základě rozpočtu se skupiny akcí najdete v tématu [Správa nákladů s Azure rozpočty](../billing/billing-cost-management-budget-scenario.md).

Vytvoření nebo aktualizace skupiny akcí, klikněte na tlačítko **spravovat skupiny akcí** při vytváření nebo úpravách rozpočtu.

![Příklad vytvoření rozpočtu zobrazíte spravovat skupiny akcí](./media/tutorial-acm-create-budgets/manage-action-groups01.png)

Klepnutím na tlačítko **přidat skupinu akcí** a vytvořit skupinu akcí.


![Obrázek dialogového okna Přidat akce skupiny](./media/tutorial-acm-create-budgets/manage-action-groups02.png)

Za akci se vytvoří skupina, zavřete okno se vraťte k vašemu rozpočtu.

Konfigurace vašeho rozpočtu na použití skupiny vaše akce při splnění jednotlivé prahovou hodnotu. Až pět různé prahové hodnoty jsou podporovány.

![Příklad zobrazující výběr skupiny akce pro podmínku upozornění](./media/tutorial-acm-create-budgets/manage-action-groups03.png)

Následující příklad ukazuje prahové hodnoty nastavit na 50 %, 75 % a 100 %. Každý je nakonfigurován k aktivaci zadaných akcí v rámci skupiny určené akce.

![Příklad zobrazující podmínky upozornění, které jsou nakonfigurované s různými skupin akcí a typ akce](./media/tutorial-acm-create-budgets/manage-action-groups04.png)

## <a name="next-steps"></a>Další postup

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Vytvořit rozpočet na webu Azure Portal
> * Upravit rozpočtu

Přejděte k dalšímu kurzu, kde vytvořit opakované export pro vaše datům služby cost management.

> [!div class="nextstepaction"]
> [Vytvoření a správa exportovaná data](tutorial-export-acm-data.md)
