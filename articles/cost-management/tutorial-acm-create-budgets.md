---
title: Kurz – vytváření a Správa rozpočtů Azure | Microsoft Docs
description: Tento kurz vám pomůže plánovat a počítat náklady na služby Azure, které využíváte.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 10/07/2019
ms.topic: conceptual
ms.service: cost-management
manager: adwise
ms.custom: seodec18
ms.openlocfilehash: 8d8f796fa7db9cab5bcac88a293dd1b98707a571
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2019
ms.locfileid: "72025771"
---
# <a name="tutorial-create-and-manage-azure-budgets"></a>Kurz: vytvoření a Správa rozpočtů Azure

Rozpočty v Cost Management vám pomůžou plánovat a řídit organizační zodpovědnost. V případě rozpočtů můžete přihlédnout ke službám Azure, které využíváte nebo se přihlašujete během konkrétního období. Pomůžou vám informovat ostatní o útratě, aby mohli aktivně spravovat náklady, a sledovat, jak průběh stráví v průběhu času. Při překročení prahových hodnot rozpočtu, které jste vytvořili, se aktivují pouze oznámení. Žádný z vašich prostředků není ovlivněný a vaše spotřeba se neukončí. Rozpočty můžete použít k porovnání a sledování útraty při analýze nákladů.

Data o nákladech a využití jsou obvykle k dispozici do 12-16 hodin a rozpočty se za tyto náklady vyhodnocují každé čtyři hodiny. E-mailová oznámení se obvykle přijímají během 12-16 hodin.

Po výběru data vypršení platnosti se rozpočty automaticky resetují na konci období (měsíčně, čtvrtletně nebo ročně) pro stejnou částku rozpočtu. Vzhledem k tomu, že dojde k resetování se stejnou rozpočtovou částkou, je nutné vytvořit samostatné rozpočty, pokud se částky rozpočtovaných měn v budoucích obdobích liší.

Příklady v tomto kurzu vás provedou vytvořením a úpravou rozpočtu pro předplatné Azure smlouva Enterprise (EA).

Podívejte se na téma [jak vytvořit rozpočet pro monitorování útraty pomocí Azure cost management](https://www.youtube.com/watch?v=ExIVG_Gr45A) videa, abyste viděli, jak můžete vytvářet rozpočty v Azure za účelem monitorování útraty.


V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvořit rozpočet v Azure Portal
> * Úprava rozpočtu

## <a name="prerequisites"></a>Požadavky

Rozpočty se podporují pro nejrůznější typy účtů Azure. Úplný seznam podporovaných typů účtů najdete v tématu [pochopení cost management dat](understand-cost-mgt-data.md). Chcete-li zobrazit rozpočty, potřebujete alespoň oprávnění ke čtení pro váš účet Azure.

 Pro předplatná Azure EA musíte mít oprávnění ke čtení pro zobrazení rozpočtů. Chcete-li vytvořit a spravovat rozpočty, musíte mít oprávnění přispěvatele. Pro předplatná EA a skupiny prostředků můžete vytvořit jednotlivé rozpočty. Nemůžete ale vytvářet rozpočty pro fakturační účty EA.

Pro rozpočty podle uživatele a skupiny jsou podporovány následující oprávnění Azure nebo obory. Další informace o oborech najdete v tématu [pochopení a práce s obory](understand-work-scopes.md).

- Owner – může vytvořit, upravit nebo odstranit rozpočty pro předplatné.
- Přispěvatel a přispěvatel Cost Management – můžou vytvářet, upravovat nebo odstraňovat vlastní rozpočty. Může upravit rozpočtovou částku pro rozpočty vytvořené jinými uživateli.
- Čtecí zařízení a Cost Management Reader – mohou zobrazit rozpočty, ke kterým mají oprávnění.

Další informace o přiřazování oprávnění k Cost Management datům najdete v tématu [přiřazení přístupu k datům cost management](assign-access-acm-data.md).

## <a name="sign-in-to-azure"></a>Přihlaste se k Azure

- Přihlaste se k Azure Portal v https://portal.azure.com.

## <a name="create-a-budget-in-the-azure-portal"></a>Vytvořit rozpočet v Azure Portal

Můžete vytvořit rozpočet předplatného Azure pro měsíční, čtvrtletní nebo roční období. Váš navigační obsah v Azure Portal určuje, zda vytvoříte rozpočet pro předplatné nebo skupinu pro správu.

Chcete-li vytvořit nebo zobrazit rozpočet, otevřete požadovaný obor v Azure Portal a v nabídce vyberte **rozpočty** . Přejděte například na **odběry**, vyberte předplatné ze seznamu a potom v nabídce vyberte **rozpočty** . Pomocí panelu **Rozsah** můžete v rozpočtech přepnout na jiný obor, například na skupinu pro správu. Další informace o oborech najdete v tématu [pochopení a práce s obory](understand-work-scopes.md).

Po vytvoření rozpočtů se jim zobrazí jednoduché zobrazení aktuální útraty.

Klikněte na tlačítko **Přidat**.

![Příklad zobrazující seznam rozpočtů, které již byly vytvořeny](./media/tutorial-acm-create-budgets/budgets01.png)

V okně **vytvořit rozpočet** ověřte, zda je zobrazený obor správný. Vyberte všechny filtry, které chcete přidat. Filtry umožňují vytvářet rozpočty pro konkrétní náklady, jako jsou skupiny prostředků v rámci předplatného nebo služby, jako jsou virtuální počítače. Libovolný filtr, který můžete použít při analýze nákladů, se dá použít i pro rozpočet.

Jakmile identifikujete rozsah a filtry, zadejte název rozpočtu. Pak zvolte měsíční, čtvrtletní nebo roční dobu resetování rozpočtu. Toto resetované období určuje časový interval, který je analyzován rozpočtem. Náklady vyhodnocené rozpočtem začínají nulou na začátku každého nového období. Při vytváření čtvrtletního rozpočtu funguje stejným způsobem jako měsíční rozpočet. Rozdíl je v tom, že částka rozpočtu pro čtvrtletí je rovnoměrně rozdělena mezi tři měsíce čtvrtletí. Roční částka rozpočtu je rovnoměrně rozdělena mezi všechny 12 měsíců kalendářního roku.

Pokud máte předplatné s průběžnými platbami, MSDN nebo Visual Studio, fakturační období faktury se nemusí zarovnat k kalendářnímu měsíci. Pro tyto typy předplatného a skupiny prostředků můžete vytvořit rozpočet, který je zarovnán na období faktury nebo kalendářní měsíce. Pokud chcete vytvořit rozpočet zarovnaný k vašemu fakturačnímu období, vyberte období resetování **fakturačního měsíce**, **kvartálního čtvrtletí**nebo **fakturačního roku**. Pokud chcete vytvořit rozpočet zarovnaný ke kalendářnímu měsíci, vyberte období resetování **měsíčně**, **čtvrtletně**nebo **ročně**.

Dále určete datum vypršení platnosti, kdy rozpočet přestane být platný, a zastaví vyhodnocování vašich nákladů.

Na základě polí zvolených v rozpočtu je graf zobrazený, který vám umožní vybrat prahovou hodnotu, která se má pro rozpočet použít. Navrhovaný rozpočet vychází z nejvyšších předpokládaných nákladů, které se můžou v budoucích obdobích účtovat. Můžete změnit částku rozpočtu.

![Příklad znázorňující vytváření rozpočtu s měsíčními údaji o nákladech ](./media/tutorial-acm-create-budgets/monthly-budget01.png)

Po nakonfigurování hodnoty rozpočtu kliknutím na tlačítko **Další** nakonfigurujte výstrahy rozpočtu. Rozpočty vyžadují alespoň jednu prahovou hodnotu pro náklady (% rozpočtu) a odpovídající e-mailovou adresu. Volitelně můžete zahrnout až pět prahových hodnot a pět e-mailových adres v jednom rozpočtu. Po splnění prahové hodnoty rozpočtu se obvykle obdrží e-mailová oznámení za méně než 20 hodin. Další informace o oznámeních najdete v tématu [použití upozornění na náklady](cost-mgt-alerts-monitor-usage-spending.md). V následujícím příkladu se e-mailová výstraha vygeneruje, když se dosáhne 90% rozpočtu.

![Příklad znázorňující podmínky upozornění](./media/tutorial-acm-create-budgets/monthly-budget-alert.png)

Po vytvoření rozpočtu se zobrazí analýza nákladů. Pohled na rozpočet v souvislosti s trendem útraty je jedním z prvních kroků při zahájení [analýzy nákladů a útraty](quick-acm-cost-analysis.md).

![Příklad rozpočtu a výdajů zobrazených v analýze nákladů](./media/tutorial-acm-create-budgets/cost-analysis.png)

V předchozím příkladu jste vytvořili rozpočet pro předplatné. Můžete ale také vytvořit rozpočet pro skupinu prostředků. Pokud chcete vytvořit rozpočet pro skupinu prostředků, přejděte na **cost management + fakturace** &gt; **předplatná** &gt; Vyberte předplatné > **skupiny prostředků** > vyberte skupinu prostředků > **rozpočty** > a potom **Přidejte** rozpočet.

## <a name="trigger-an-action-group"></a>Aktivace skupiny akcí

Když vytváříte nebo upravujete rozpočet pro předplatné nebo obor skupiny prostředků, můžete ho nakonfigurovat tak, aby se zavolala skupina akcí. Skupina akcí může při splnění prahové hodnoty rozpočtu provádět různé akce. Další informace o skupinách akcí naleznete v tématu [Create and Manage Action Groups in the Azure Portal](../azure-monitor/platform/action-groups.md). Další informace o používání automatizace založené na rozpočtu se skupinami akcí najdete v tématu [Správa nákladů pomocí rozpočtů Azure](../billing/billing-cost-management-budget-scenario.md).

Pokud chcete vytvořit nebo aktualizovat skupiny akcí, klikněte na **Spravovat skupiny akcí** při vytváření nebo úpravách rozpočtu.

![Příklad vytvoření rozpočtu pro zobrazení skupin akcí správy](./media/tutorial-acm-create-budgets/manage-action-groups01.png)


Potom klikněte na **Přidat skupinu akcí** a vytvořte skupinu akcí.


![Obrázek okna Přidat skupinu akcí](./media/tutorial-acm-create-budgets/manage-action-groups02.png)

Po vytvoření skupiny akcí ji zavřete a vrátíte se do svého rozpočtu.

Nakonfigurujte svůj rozpočet pro použití vaší skupiny akcí, pokud je splněna jednotlivá prahová hodnota. Podporuje se až pět různých prahových hodnot.

![Příklad znázorňující výběr skupiny akcí pro podmínku upozornění](./media/tutorial-acm-create-budgets/manage-action-groups03.png)

Následující příklad ukazuje prahové hodnoty rozpočtu nastavené na 50%, 75% a 100%. Každá je nakonfigurována tak, aby aktivovala zadané akce v rámci určené skupiny akcí.

![Příklad znázorňující podmínky výstrah nakonfigurované s různými skupinami akcí a typem akcí](./media/tutorial-acm-create-budgets/manage-action-groups04.png)

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste zjistili, jak:

> [!div class="checklist"]
> * Vytvořit rozpočet v Azure Portal
> * Úprava rozpočtu

Přejděte k dalšímu kurzu a vytvořte opakovaný export pro data služby cost management.

> [!div class="nextstepaction"]
> [Vytváření a Správa exportovaných dat](tutorial-export-acm-data.md)
