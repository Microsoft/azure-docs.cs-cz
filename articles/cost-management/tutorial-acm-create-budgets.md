---
title: Kurz – vytvoření a Správa rozpočtů Azure | Dokumentace Microsoftu
description: Tento kurz pomůže plánu a účet pro náklady na služby Azure, které skutečně využijete.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 11/12/2019
ms.topic: conceptual
ms.service: cost-management-billing
manager: adwise
ms.custom: seodec18
ms.openlocfilehash: edb46bc361c515439a93d9c3d0b9987bebe4b1b1
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2019
ms.locfileid: "74229879"
---
# <a name="tutorial-create-and-manage-azure-budgets"></a>Kurz: Vytvoření a Správa rozpočtů Azure

Rozpočty ve službě Cost Management pomáhají plánovat a řídit odpovědnost v organizaci. Pomocí rozpočtů můžete zodpovídat za služby Azure, které využíváte nebo k jejichž odběru jste po určitou dobu přihlášení. Pomáhají informovat ostatní o jejich útraty aktivně spravovat náklady a sledovat, jak útraty postupuje v čase. Při překročení prahových hodnot rozpočtu, který jste vytvořili, se aktivují jenom oznámení. Žádná z vašich prostředků neovlivní a spotřebu není zastavená. Rozpočty můžete porovnat a sledovat výdaje podle analýzu nákladů.

Data o nákladech a využití jsou obvykle k dispozici do 12-16 hodin a rozpočty se za tyto náklady vyhodnocují každé čtyři hodiny. E-mailová oznámení se obvykle přijímají během 12-16 hodin.

Rozpočty automaticky resetovat na konci období (měsíčně, čtvrtletně nebo ročně) pro velkou rozpočtu vyberete datum vypršení platnosti v budoucnu. Protože resetují se stejnou velikostí rozpočtu, budete muset vytvořit samostatné rozpočty při rozpočtu peněžních hodnot se liší v budoucích obdobích.

Příklady v tomto kurzu vás provede procesem vytvoření a úprava rozpočet pro předplatné Azure Enterprise Agreement (EA).

Podívejte se na téma [jak vytvořit rozpočet pro monitorování útraty pomocí Azure cost management](https://www.youtube.com/watch?v=ExIVG_Gr45A) videa, abyste viděli, jak můžete vytvářet rozpočty v Azure za účelem monitorování útraty.


V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvořit rozpočet na webu Azure Portal
> * Upravit rozpočtu

## <a name="prerequisites"></a>Požadavky

Rozpočty se podporují pro nejrůznější typy účtů Azure. Úplný seznam podporovaných typů účtů najdete v tématu [pochopení cost management dat](understand-cost-mgt-data.md). Chcete-li zobrazit rozpočty, potřebujete alespoň oprávnění ke čtení pro váš účet Azure.

 Pro předplatná Azure EA musíte mít oprávnění ke čtení pro zobrazení rozpočtů. Vytvoření a Správa rozpočtů, musí mít oprávnění přispěvatele. Můžete vytvořit jednotlivé rozpočty pro skupiny prostředků a předplatných EA. Nelze však vytvořit rozpočty pro fakturační účty EA.

Pro rozpočty podle uživatele a skupiny jsou podporovány následující oprávnění Azure nebo obory. Další informace o oborech najdete v tématu [pochopení a práce s obory](understand-work-scopes.md).

- Vlastník – Může vytvářet, upravovat a odstraňovat rozpočty u předplatných.
- Přispěvatel a přispěvatelů Cost Management – můžete vytvořit, upravit nebo odstranit vlastní rozpočty. Může měnit částky rozpočtu pro rozpočty, které vytvářejí jiní uživatelé.
- Čtenář a čtečky Cost Management – můžete zobrazit rozpočty, ke kterým mají oprávnění.

Další informace o přiřazování oprávnění k Cost Management datům najdete v tématu [přiřazení přístupu k datům cost management](assign-access-acm-data.md).

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

- Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.

## <a name="create-a-budget-in-the-azure-portal"></a>Vytvořit rozpočet na webu Azure Portal

Rozpočet předplatné Azure můžete vytvořit pro měsíčně, čtvrtletně nebo roční období. Váš navigační obsah v Azure Portal určuje, zda vytvoříte rozpočet pro předplatné nebo skupinu pro správu.

Chcete-li vytvořit nebo zobrazit rozpočet, otevřete požadovaný obor v Azure Portal a v nabídce vyberte **rozpočty** . Přejděte například na **odběry**, vyberte předplatné ze seznamu a potom v nabídce vyberte **rozpočty** . Pomocí panelu **Rozsah** můžete v rozpočtech přepnout na jiný obor, například na skupinu pro správu. Další informace o oborech najdete v tématu [pochopení a práce s obory](understand-work-scopes.md).

Po vytvoření rozpočty, zobrazí se vaše aktuální útratu proti nim jednoduché zobrazení.

Klikněte na **Přidat**.

![Příklad zobrazující seznam rozpočtů, které již byly vytvořeny](./media/tutorial-acm-create-budgets/budgets01.png)

V okně **vytvořit rozpočet** ověřte, zda je zobrazený obor správný. Vyberte všechny filtry, které chcete přidat. Filtry umožňují vytvářet rozpočty pro konkrétní náklady, jako jsou skupiny prostředků v rámci předplatného nebo služby, jako jsou virtuální počítače. Libovolný filtr, který můžete použít při analýze nákladů, se dá použít i pro rozpočet.

Jakmile identifikujete rozsah a filtry, zadejte název rozpočtu. Pak zvolte měsíční, čtvrtletní nebo roční dobu resetování rozpočtu. Toto resetované období určuje časový interval, který je analyzován rozpočtem. Náklady vyhodnocené rozpočtem začínají nulou na začátku každého nového období. Při vytváření čtvrtletní rozpočtu funguje stejným způsobem jako měsíční rozpočtu. Rozdíl je, že je rozpočtu částka za čtvrtletí rovnoměrně rozdělené mezi tři měsíce ve čtvrtletí. Roční částka rozpočtu je rovnoměrně rozdělena mezi všechny 12 měsíců kalendářního roku.

Pokud máte předplatné s průběžnými platbami, MSDN nebo Visual Studio, fakturační období faktury se nemusí zarovnat k kalendářnímu měsíci. Pro tyto typy předplatného a skupiny prostředků můžete vytvořit rozpočet, který je zarovnán na období faktury nebo kalendářní měsíce. Pokud chcete vytvořit rozpočet zarovnaný k vašemu fakturačnímu období, vyberte období resetování **fakturačního měsíce**, **kvartálního čtvrtletí**nebo **fakturačního roku**. Pokud chcete vytvořit rozpočet zarovnaný ke kalendářnímu měsíci, vyberte období resetování **měsíčně**, **čtvrtletně**nebo **ročně**.

Dále určete datum vypršení platnosti, kdy rozpočet přestane být platný, a zastaví vyhodnocování vašich nákladů.

Na základě polí zvolených v rozpočtu je graf zobrazený, který vám umožní vybrat prahovou hodnotu, která se má pro rozpočet použít. Navrhovaný rozpočet vychází z nejvyšších předpokládaných nákladů, které se můžou v budoucích obdobích účtovat. Můžete změnit částku rozpočtu.

![Příklad znázorňující vytváření rozpočtu s měsíčními údaji o nákladech ](./media/tutorial-acm-create-budgets/monthly-budget01.png)

Po nakonfigurování hodnoty rozpočtu kliknutím na tlačítko **Další** nakonfigurujte výstrahy rozpočtu. Rozpočty vyžadují aspoň jednu cenu prahové hodnoty (% rozpočtu) a odpovídající e-mailovou adresu. Může volitelně zahrnovat až pět prahové hodnoty a pět e-mailových adres v jedné rozpočtu. Po splnění prahové hodnoty rozpočtu se obvykle obdrží e-mailová oznámení za méně než 20 hodin. Další informace o oznámeních najdete v tématu [použití upozornění na náklady](cost-mgt-alerts-monitor-usage-spending.md). V následujícím příkladu se e-mailová výstraha vygeneruje, když se dosáhne 90% rozpočtu. Pokud vytvoříte rozpočet pomocí rozhraní API pro rozpočtování, můžete také přiřadit role lidem pro příjem výstrah. Přiřazování rolí lidem není v Azure Portal podporováno. Další informace o rozhraní API pro rozpočty Azure najdete v tématu [rozpočty rozhraní API](/rest/api/consumption/budgets).

![Příklad znázorňující podmínky upozornění](./media/tutorial-acm-create-budgets/monthly-budget-alert.png)

Po vytvoření rozpočtu, se zobrazí v analýze nákladů. Pohled na rozpočet v souvislosti s trendem útraty je jedním z prvních kroků při zahájení [analýzy nákladů a útraty](quick-acm-cost-analysis.md).

![Příklad snazší plánování rozpočtu a výdaje podle analýzy nákladů](./media/tutorial-acm-create-budgets/cost-analysis.png)

V předchozím příkladu jste vytvořili rozpočet pro předplatné. Můžete však také vytvořit rozpočet pro skupinu prostředků. Pokud chcete vytvořit rozpočet pro skupinu prostředků, přejděte na **cost management +** &gt; **předplatná** fakturace &gt; vyberte předplatné > **skupiny prostředků** > vyberte skupinu prostředků > **rozpočty** > a pak **Přidejte** rozpočet.

## <a name="trigger-an-action-group"></a>Aktivace skupiny akcí

Když vytváříte nebo upravujete rozpočet pro předplatné nebo obor skupiny prostředků, můžete ho nakonfigurovat tak, aby se zavolala skupina akcí. Skupina akcí může při splnění prahové hodnoty rozpočtu provádět různé akce. Skupiny akcí se aktuálně podporují jenom pro obory předplatného a skupiny prostředků. Další informace o skupinách akcí naleznete v tématu [Create and Manage Action Groups in the Azure Portal](../azure-monitor/platform/action-groups.md). Další informace o používání automatizace založené na rozpočtu se skupinami akcí najdete v tématu [Správa nákladů pomocí rozpočtů Azure](../billing/billing-cost-management-budget-scenario.md).



Pokud chcete vytvořit nebo aktualizovat skupiny akcí, klikněte na **Spravovat skupiny akcí** při vytváření nebo úpravách rozpočtu.

![Příklad vytvoření rozpočtu pro zobrazení skupin akcí správy](./media/tutorial-acm-create-budgets/manage-action-groups01.png)


Potom klikněte na **Přidat skupinu akcí** a vytvořte skupinu akcí.


![Obrázek okna Přidat skupinu akcí](./media/tutorial-acm-create-budgets/manage-action-groups02.png)

Po vytvoření skupiny akcí ji zavřete a vrátíte se do svého rozpočtu.

Nakonfigurujte svůj rozpočet pro použití vaší skupiny akcí, pokud je splněna jednotlivá prahová hodnota. Podporuje se až pět různých prahových hodnot.

![Příklad znázorňující výběr skupiny akcí pro podmínku upozornění](./media/tutorial-acm-create-budgets/manage-action-groups03.png)

Následující příklad ukazuje prahové hodnoty rozpočtu nastavené na 50%, 75% a 100%. Každá je nakonfigurována tak, aby aktivovala zadané akce v rámci určené skupiny akcí.

![Příklad znázorňující podmínky výstrah nakonfigurované s různými skupinami akcí a typem akcí](./media/tutorial-acm-create-budgets/manage-action-groups04.png)

Integrace rozpočtu se skupinami akcí funguje jenom pro skupiny akcí, které mají zakázané společné schéma výstrah. Další informace o zakázání schématu najdete v tématu [návody povolení společného schématu výstrah?](../azure-monitor/platform/alerts-common-schema.md#how-do-i-enable-the-common-alert-schema)

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Vytvořit rozpočet na webu Azure Portal
> * Upravit rozpočtu

Přejděte k dalšímu kurzu, kde vytvořit opakované export pro vaše datům služby cost management.

> [!div class="nextstepaction"]
> [Vytváření a Správa exportovaných dat](tutorial-export-acm-data.md)
