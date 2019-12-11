---
title: Kurz – vytvoření a Správa rozpočtů Azure | Dokumentace Microsoftu
description: Tento kurz pomůže plánu a účet pro náklady na služby Azure, které skutečně využijete.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 12/09/2019
ms.topic: conceptual
ms.service: cost-management-billing
manager: adwise
ms.custom: seodec18
ms.openlocfilehash: 4b0e5be0e5f71fa61e3a6c6fa39e720c4c298898
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2019
ms.locfileid: "74967932"
---
# <a name="tutorial-create-and-manage-azure-budgets"></a>Kurz: Vytvoření a Správa rozpočtů Azure

Rozpočty ve službě Cost Management pomáhají plánovat a řídit odpovědnost v organizaci. Pomocí rozpočtů můžete zodpovídat za služby Azure, které využíváte nebo k jejichž odběru jste po určitou dobu přihlášení. Pomáhají informovat ostatní o jejich útraty aktivně spravovat náklady a sledovat, jak útraty postupuje v čase. Při překročení prahových hodnot rozpočtu, který jste vytvořili, se aktivují jenom oznámení. Žádná z vašich prostředků neovlivní a spotřebu není zastavená. Rozpočty můžete porovnat a sledovat výdaje podle analýzu nákladů.

Data o nákladech a využití jsou obvykle k dispozici do 12-16 hodin a rozpočty se za tyto náklady vyhodnocují každé čtyři hodiny. E-mailová oznámení se obvykle přijímají během 12-16 hodin.

Rozpočty automaticky resetovat na konci období (měsíčně, čtvrtletně nebo ročně) pro velkou rozpočtu vyberete datum vypršení platnosti v budoucnu. Protože resetují se stejnou velikostí rozpočtu, budete muset vytvořit samostatné rozpočty při rozpočtu peněžních hodnot se liší v budoucích obdobích.

Příklady v tomto kurzu vás provede procesem vytvoření a úprava rozpočet pro předplatné Azure Enterprise Agreement (EA).

Podívejte se na téma [použití rozpočtů na předplatná pomocí Azure Portalového](https://www.youtube.com/watch?v=UrkHiUx19Po) videa, abyste viděli, jak můžete v Azure vytvářet rozpočty za účelem monitorování útraty.


V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvořit rozpočet na webu Azure Portal
> * Upravit rozpočtu

## <a name="prerequisites"></a>Předpoklady

Rozpočty se podporují pro nejrůznější typy účtů Azure. Úplný seznam podporovaných typů účtů najdete v tématu [pochopení cost management dat](understand-cost-mgt-data.md). Chcete-li zobrazit rozpočty, potřebujete alespoň oprávnění ke čtení pro váš účet Azure.

 Pro předplatná Azure EA musíte mít oprávnění ke čtení pro zobrazení rozpočtů. Vytvoření a Správa rozpočtů, musí mít oprávnění přispěvatele. Můžete vytvořit jednotlivé rozpočty pro skupiny prostředků a předplatných EA. Nelze však vytvořit rozpočty pro fakturační účty EA.

Pro rozpočty podle uživatele a skupiny jsou podporovány následující oprávnění Azure nebo obory. Další informace o oborech najdete v tématu [pochopení a práce s obory](understand-work-scopes.md).

- Vlastník – Může vytvářet, upravovat a odstraňovat rozpočty u předplatných.
- Přispěvatel a přispěvatelů Cost Management – můžete vytvořit, upravit nebo odstranit vlastní rozpočty. Může měnit částky rozpočtu pro rozpočty, které vytvářejí jiní uživatelé.
- Čtenář a čtečky Cost Management – můžete zobrazit rozpočty, ke kterým mají oprávnění.

Další informace o přiřazování oprávnění k datům služby Cost Management najdete v tématu [přiřazení přístupu k datům služby Cost Management](assign-access-acm-data.md).

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

- Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.

## <a name="create-a-budget-in-the-azure-portal"></a>Vytvořit rozpočet na webu Azure Portal

Rozpočet předplatné Azure můžete vytvořit pro měsíčně, čtvrtletně nebo roční období. Váš navigační obsah v portálu Microsoft Azure určuje, jestli vytvoříte rozpočet pro předplatné nebo skupinu pro správu.

Pokud chcete vytvořit nebo zobrazit rozpočet, otevřete požadovaný obor v portálu Microsoft Azure a v nabídce vyberte **Rozpočty**. Přejděte například na **odběry**, vyberte předplatné ze seznamu a potom v nabídce vyberte **rozpočty** . Pomocí panelu **Rozsah** můžete v rozpočtech přepnout na jiný obor, například na skupinu pro správu. Další informace o oborech najdete v tématu [pochopení a práce s obory](understand-work-scopes.md).

Po vytvoření rozpočty, zobrazí se vaše aktuální útratu proti nim jednoduché zobrazení.

Vyberte **Přidat**.

![Příklad zobrazující seznam rozpočtů, které již byly vytvořeny](./media/tutorial-acm-create-budgets/budgets01.png)

V okně **Vytvořit rozpočet** se ujistěte, že je zobrazený obor správný. Zvolte všechny filtry, které chcete přidat. Filtry umožňují vytvářet rozpočty pro konkrétní náklady, jako jsou skupiny prostředků v rámci předplatného nebo služby, jako jsou virtuální počítače. Každý filtr, který můžete použít při analýze nákladů, se dá použít i na rozpočet.

Jakmile určíte rozsah a filtry, zadejte název rozpočtu. Pak zvolte měsíční, čtvrtletní nebo roční dobu resetování rozpočtu. Tento interval určuje časový rozsah, který je analyzován rozpočtem. Náklady vyhodnocené rozpočtem začínají na začátku každého nového období nulou. Při vytváření čtvrtletní rozpočtu funguje stejným způsobem jako měsíční rozpočtu. Rozdíl je, že je rozpočtu částka za čtvrtletí rovnoměrně rozdělené mezi tři měsíce ve čtvrtletí. Částka ročního rozpočtu je rovnoměrně rozdělena mezi všech 12 měsíců kalendářního roku.

Pokud máte předplatné s průběžnými platbami, MSDN nebo Visual Studio, fakturační období faktury nemusí přesně odpovídat kalendářnímu měsíci. Pro tyto typy předplatného a skupiny prostředků můžete vytvořit rozpočet, který bude odpovídat období faktury nebo kalendářním měsícům. Pokud chcete vytvořit rozpočet zarovnaný k vašemu fakturačnímu období, vyberte období resetování **fakturačního měsíce**, **kvartálního čtvrtletí**nebo **fakturačního roku**. Pokud chcete vytvořit rozpočet zarovnaný ke kalendářnímu měsíci, vyberte období resetování **měsíčně**, **čtvrtletně**nebo **ročně**.

Dále určete datum vypršení platnosti, kdy rozpočet přestane platit a přestane vyhodnocovat vaše náklady.

Na základě polí zvolených dosud v rozpočtu se zobrazí graf, který vám umožní vybrat prahovou hodnotu, která se má použít pro rozpočet. Navrhovaný rozpočet vychází z nejvyšších předpokládaných nákladů, které můžou v budoucích obdobích naběhnout. Částku rozpočtu můžete změnit.

![Příklad znázorňující vytváření rozpočtu s měsíčními údaji o nákladech ](./media/tutorial-acm-create-budgets/monthly-budget01.png)

Po konfiguraci částky rozpočtu vyberte **Další** a nakonfigurujte upozornění rozpočtu. Rozpočty vyžadují aspoň jednu cenu prahové hodnoty (% rozpočtu) a odpovídající e-mailovou adresu. Může volitelně zahrnovat až pět prahové hodnoty a pět e-mailových adres v jedné rozpočtu. Po splnění prahové hodnoty rozpočtu se obvykle obdrží e-mailová oznámení za méně než 20 hodin. Další informace o oznámeních najdete v tématu [použití upozornění na náklady](cost-mgt-alerts-monitor-usage-spending.md). V následujícím příkladu se e-mailová výstraha vygeneruje, když se dosáhne 90% rozpočtu. Pokud vytvoříte rozpočet pomocí rozhraní API pro rozpočtování, můžete také přiřadit role lidem pro příjem výstrah. Přiřazování rolí lidem není v Azure Portal podporováno. Další informace o rozhraní API pro rozpočty Azure najdete v tématu [rozpočty rozhraní API](/rest/api/consumption/budgets).

![Příklad znázorňující podmínky upozornění](./media/tutorial-acm-create-budgets/monthly-budget-alert.png)

Po vytvoření rozpočtu, se zobrazí v analýze nákladů. Zobrazení vašemu rozpočtu ve vztahu k vaší útraty trend je jedním z prvních kroků při spuštění [analyzovat vaše náklady a výdaje](quick-acm-cost-analysis.md).

![Příklad snazší plánování rozpočtu a výdaje podle analýzy nákladů](./media/tutorial-acm-create-budgets/cost-analysis.png)

V předchozím příkladu jste vytvořili rozpočet pro předplatné. Můžete však také vytvořit rozpočet pro skupinu prostředků. Pokud chcete vytvořit rozpočet pro skupinu prostředků, přejděte na **Správa nákladů a fakturace** &gt; **předplatná** &gt; Vybrat předplatné > **prostředků skupiny** > vyberte skupinu prostředků > **rozpočty** > a pak **přidat** rozpočtu.

## <a name="trigger-an-action-group"></a>Aktivace skupiny akcí

Když vytváříte nebo upravujete rozpočet pro předplatné nebo obor skupiny prostředků, můžete ho nakonfigurovat tak, aby volal skupinu akcí. Skupina akcí může při dosažení prahové hodnoty rozpočtu provádět různé akce. Skupiny akcí se aktuálně podporují jenom pro obory předplatného a skupiny prostředků. Další informace o skupinách akcí naleznete v tématu [Create and Manage Action Groups in the Azure Portal](../azure-monitor/platform/action-groups.md). Další informace o používání automatizace založené na rozpočtu se skupinami akcí najdete v tématu [Správa nákladů pomocí rozpočtů Azure](../billing/billing-cost-management-budget-scenario.md).



Chcete-li vytvořit nebo aktualizovat skupiny akcí, vyberte možnost **Spravovat skupiny akcí** při vytváření nebo úpravách rozpočtu.

![Příklad vytvoření rozpočtu pro zobrazení skupin akcí správy](./media/tutorial-acm-create-budgets/manage-action-groups01.png)


Pak vyberte **Přidat skupinu akcí** a vytvořte skupinu akcí.


![Obrázek okna Přidat skupinu akcí](./media/tutorial-acm-create-budgets/manage-action-groups02.png)

Po vytvoření skupiny akcí ji zavřete a vrátíte se do svého rozpočtu.

Nakonfigurujte svůj rozpočet pro použití vaší skupiny akcí, pokud je splněna jednotlivá prahová hodnota. Podporuje se až pět různých prahových hodnot.

![Příklad znázorňující výběr skupiny akcí pro podmínku upozornění](./media/tutorial-acm-create-budgets/manage-action-groups03.png)

Následující příklad ukazuje prahové hodnoty rozpočtu nastavené na 50%, 75% a 100%. Každá je nakonfigurována tak, aby aktivovala zadané akce v rámci určené skupiny akcí.

![Příklad znázorňující podmínky výstrah nakonfigurované s různými skupinami akcí a typem akcí](./media/tutorial-acm-create-budgets/manage-action-groups04.png)

Integrace rozpočtu se skupinami akcí funguje jenom pro skupiny akcí, které mají zakázané společné schéma výstrah. Další informace o zakázání schématu najdete v tématu [návody povolení společného schématu výstrah?](../azure-monitor/platform/alerts-common-schema.md#how-do-i-enable-the-common-alert-schema)

## <a name="edit-an-existing-budget"></a>Upravit existující rozpočet
Chcete-li provést změny v existujícím rozpočtu, přejděte do okna hlavní rozpočty a vyberte rozpočet, který chcete upravit. V okně Podrobnosti rozpočtu vyberte **Upravit rozpočet** a proveďte potřebné změny a pak vyberte **Uložit**.

![Příklad znázorňující úpravu rozpočtu](./media/tutorial-acm-create-budgets/edit-budget.png)


## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Vytvořit rozpočet na webu Azure Portal
> * Upravit rozpočtu

Přejděte k dalšímu kurzu, kde vytvořit opakované export pro vaše datům služby cost management.

> [!div class="nextstepaction"]
> [Vytvoření a správa exportovaná data](tutorial-export-acm-data.md)
