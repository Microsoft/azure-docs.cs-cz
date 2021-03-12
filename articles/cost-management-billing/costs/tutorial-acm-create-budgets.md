---
title: 'Kurz: Vytváření a správa rozpočtů Azure'
description: Tento kurz vám pomůže plánovat a počítat náklady na služby Azure, které využíváte.
author: bandersmsft
ms.author: banders
ms.date: 03/09/2021
ms.topic: tutorial
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: adwise
ms.custom: seodec18
ms.openlocfilehash: 2c0853b68e432fa94e661e68486b9e58b21aa845
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/11/2021
ms.locfileid: "103012422"
---
# <a name="tutorial-create-and-manage-azure-budgets"></a>Kurz: Vytváření a správa rozpočtů Azure

Rozpočty ve službě Cost Management pomáhají plánovat a řídit odpovědnost v organizaci. Pomůžou vám informovat ostatní o jejich výdajích, aby mohli aktivně spravovat náklady, a umožní vám sledovat, jak se výdaje vyvíjejí v průběhu času. Výstrahy můžete nakonfigurovat na základě skutečných nákladů nebo předpokládaných nákladů, abyste se ujistili, že vaše útrata spadá do limitu útraty vaší organizace. Při překročení vámi vytvořených prahových hodnot rozpočtu se aktivují pouze oznámení. Žádný z vašich prostředků není ovlivněný a vaše spotřeba není zastavena. Rozpočty můžete použít k porovnání a sledování výdajů při analýze nákladů.

Data o nákladech a využití jsou obvykle k dispozici během 8 až 24 hodin a rozpočty se na základě těchto nákladů vyhodnocují vždy po 24 hodinách. Nezapomeňte se seznámit se specifiky [aktualizací údajů o využití a nákladech](./understand-cost-mgt-data.md#cost-and-usage-data-updates-and-retention). Po dosažení prahové hodnoty rozpočtu se obvykle rozesílají e-mailová oznámení během jedné hodiny od vyhodnocení.

Po výběru budoucího data vypršení platnosti se rozpočty automaticky obnoví na konci období (měsíčně, čtvrtletně nebo ročně) pro stejnou částku rozpočtu. Vzhledem k tomu, že dojde k obnovení se stejnou částkou rozpočtu, musíte vytvořit samostatné rozpočty, pokud se rozpočtované částky v budoucích obdobích liší. Po vypršení platnosti rozpočtu se automaticky odstraní.

Příklady v tomto kurzu vás provedou vytvořením a úpravou rozpočtu pro předplatné se smlouvou Azure Enterprise (EA).

Podívejte se na video zobrazující, [jak použít rozpočty na předplatná pomocí webu Azure Portal](https://www.youtube.com/watch?v=UrkHiUx19Po), a zjistěte, jak můžete vytvářet rozpočty v Azure za účelem sledování výdajů. Další videa najdete v [kanálu služby Cost Management na YouTube](https://www.youtube.com/c/AzureCostManagement).

>[!VIDEO https://www.youtube.com/embed/UrkHiUx19Po]

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření rozpočtu na portálu Azure Portal
> * Vytváření a úpravy rozpočtů pomocí PowerShellu
> * Vytvoření rozpočtu pomocí šablony Azure Resource Manageru

## <a name="prerequisites"></a>Požadavky

Rozpočty se podporují pro následující typy rozsahů a účtů Azure:

- Rozsahy řízení přístupu na základě role Azure (Azure RBAC)
    - Skupiny pro správu
    - Předplatné
- Rozsahy smlouvy Enterprise
    - Fakturační účet
    - Oddělení
    - Registrační účet
- Jednotlivé smlouvy
    - Fakturační účet
- Rozsahy smluv se zákazníky Microsoftu
    - Fakturační účet
    - Fakturační profil
    - Oddíl faktury
    - Zákazník
- Rozsahy AWS
    - Externí účet
    - Externí předplatné


Pokud chcete zobrazit rozpočty, potřebujete přinejmenším oprávnění ke čtení k vašemu účtu Azure.

Pokud máte nové předplatné, nemůžete rovnou vytvořit rozpočet ani využívat další funkce služby Cost Management. Může trvat až 48 hodin, než budete moct využívat všechny funkce služby Cost Management.

Pokud máte předplatná Azure EA, musíte mít oprávnění ke čtení, abyste si mohli rozpočty zobrazit. Chcete-li rozpočty vytvářet a spravovat, musíte mít oprávnění přispěvatele.

Pro rozpočty se podporují následující oprávnění nebo obory Azure pro předplatná podle uživatelů a skupin. Další informace o oborech najdete v článku o [principech oborů a práci s nimi](understand-work-scopes.md).

- Vlastník – Může vytvářet, upravovat a odstraňovat rozpočty u předplatných.
- Přispěvatel a přispěvatel služby Cost Management – Může vytvářet, upravovat a odstraňovat vlastní rozpočty. Může měnit částky rozpočtu pro rozpočty, které vytvářejí jiní uživatelé.
- Čtenář a čtenář služby Cost Management – Může zobrazovat rozpočty, ke kterým má oprávnění.

Další informace o přiřazování oprávnění k datům služby Cost Management najdete v tématu [Přiřazení přístupu k datům služby Cost Management](./assign-access-acm-data.md).

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

- Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-budget-in-the-azure-portal"></a>Vytvoření rozpočtu na portálu Azure Portal

Můžete si vytvořit rozpočet pro předplatné Azure na dobu jednoho měsíce, čtvrtletí nebo roku.

Chcete-li vytvořit nebo zobrazit rozpočet, otevřete obor v Azure Portal a v nabídce vyberte **rozpočty** . Přejděte například na **Předplatná**, vyberte předplatné ze seznamu a pak v nabídce vyberte **Rozpočty**. Pomocí možnosti **Obor** můžete v části Rozpočty přepnout na jiný obor, například na skupinu pro správu. Další informace o oborech najdete v článku o [principech oborů a práci s nimi](understand-work-scopes.md).

Po vytvoření rozpočtů se zobrazí jednoduchý přehled vašich aktuálních výdajů v rámci rozpočtů.

Vyberte **Přidat**.

:::image type="content" source="./media/tutorial-acm-create-budgets/budgets-cost-management.png" alt-text="Screenthost zobrazující seznam rozpočtů, které již byly vytvořeny." lightbox="./media/tutorial-acm-create-budgets/budgets-cost-management.png" :::

V okně **Vytvořit rozpočet** se ujistěte, že je zobrazený obor správný. Vyberte všechny filtry, které chcete přidat. Filtry umožňují vytvářet rozpočty pro konkrétní náklady, jako jsou skupiny prostředků v rámci předplatného nebo služby, jako jsou virtuální počítače. Jakýkoli filtr, který můžete použít při analýze nákladů, lze použít i pro rozpočet.

Po identifikaci oboru a filtrů zadejte název rozpočtu. Pak zvolte měsíční, čtvrtletní nebo roční období pro obnovení rozpočtu. Doba obnovení určuje časový interval, který je analyzován rozpočtem. Náklady vyhodnocené rozpočtem začínají nulou na začátku každého nového období. Vytváření čtvrtletního rozpočtu funguje stejným způsobem jako pro měsíční rozpočet. Rozdíl je v tom, že částka rozpočtu pro čtvrtletí se rovnoměrně rozdělí mezi tři měsíce čtvrtletí. Částka ročního rozpočtu se rovnoměrně rozdělí mezi všech 12 měsíců kalendářního roku.

Pokud máte předplatné s průběžnými platbami, MSDN nebo předplatné sady Visual Studio, nemusí se fakturační období shodovat s kalendářním měsícem. Pro tyto typy předplatného a skupiny prostředků můžete vytvořit rozpočet, který se shoduje s vaším fakturačním obdobím nebo kalendářním měsícem. Pokud chcete vytvořit rozpočet přizpůsobený vašemu fakturačnímu období, vyberte jako období obnovení **fakturační měsíc**, **fakturační čtvrtletí** nebo **fakturační rok**. Jestliže chcete vytvořit rozpočet přizpůsobený kalendářnímu měsíci, vyberte **měsíční**, **čtvrtletní**  nebo **roční** období obnovení.

Dále určete datum vypršení platnosti, ke kterému rozpočet přestane být platný a přestane vyhodnocovat vaše náklady.

Na základě dosud zvolených polí v rozpočtu se zobrazí graf, který vám pomůže vybrat prahovou hodnotu, jež se má použít pro rozpočet. Navrhovaný rozpočet vychází z nejvyšších předpokládaných nákladů, které by vám mohly v budoucnu vzniknout. Částku rozpočtu můžete změnit.

:::image type="content" source="./media/tutorial-acm-create-budgets/create-monthly-budget.png" alt-text="Snímek obrazovky znázorňující vytváření rozpočtu s měsíčními údaji o nákladech" lightbox="./media/tutorial-acm-create-budgets/create-monthly-budget.png" :::

Po nakonfigurování hodnoty rozpočtu vyberte možnost **Další** a nakonfigurujte výstrahy rozpočtu pro skutečné náklady a prognózy rozpočtovaných výstrah.

## <a name="configure-actual-costs-budget-alerts"></a>Konfigurace výstrah rozpočtu skutečných nákladů

Rozpočty vyžadují alespoň jednu prahovou hodnotu pro náklady (% rozpočtu) a odpovídající e-mailovou adresu. Do jednoho rozpočtu můžete volitelně zahrnout až pět prahových hodnot a pět e-mailových adres. Po dosažení prahové hodnoty rozpočtu se obvykle rozesílají e-mailová oznámení během jedné hodiny od vyhodnocení. Pro skutečné náklady, které jste narostli ve vztahu k nakonfigurovaným prahovým hodnotám rozpočtu, se generují výstrahy rozpočtu skutečných nákladů.

## <a name="configure-forecasted-budget-alerts"></a>Konfigurace předplánovaných výstrah rozpočtu

Předpovědi s prognózou poskytují Pokročilá oznámení o tom, že trendy výdajů budou nejspíš překročit váš rozpočet. Výstrahy využívají [předpokládané náklady předpovědi](quick-acm-cost-analysis.md#understand-forecast). Výstrahy jsou generovány, pokud projekce prognózy nákladů překročí nastavenou prahovou hodnotu. Můžete nakonfigurovat prahovou hodnotu prognózy (% rozpočtu). Pokud je splněna předpokládaná prahová hodnota rozpočtu, jsou oznámení obvykle odesílána během hodiny vyhodnocení.

Chcete-li přepínat mezi konfigurací výstrahy a upozornění na skutečnou prognózu, použijte `Type` pole při konfiguraci výstrahy, jak je znázorněno na následujícím obrázku.

Pokud chcete dostávat e-maily, přidejte adresu azure-noreply@microsoft.com do seznamu schválených odesílatelů, aby e-maily nechodily do složky nevyžádané pošty. Další informace o oznámeních naleznete v tématu [Použití upozornění na náklady](./cost-mgt-alerts-monitor-usage-spending.md).

V následujícím příkladu se vytvoří e-mailová výstraha, když je dosaženo 90% rozpočtu. Pokud vytvoříte rozpočet pomocí rozhraní API pro rozpočty, můžete také lidem přiřadit role pro příjem upozornění. Na webu Azure Portal není přiřazování rolí lidem podporováno. Další informace o rozhraní API pro rozpočty Azure najdete v tématu popisujícím [rozhraní API pro rozpočty](/rest/api/consumption/budgets). Pokud chcete, aby se e-mailové upozornění posílalo v jiném jazyce, projděte si téma [Podporovaná národní prostředí pro e-maily s upozorněními na rozpočet](manage-automation.md#supported-locales-for-budget-alert-emails).

Omezení upozornění podporují rozsah 0,01% až 1000% prahové hodnoty rozpočtu, kterou jste zadali.

:::image type="content" source="./media/tutorial-acm-create-budgets/budget-set-alert.png" alt-text="Snímek obrazovky znázorňující podmínky upozornění" lightbox="./media/tutorial-acm-create-budgets/budget-set-alert.png" :::

Vytvořený rozpočet se zobrazí v analýze nákladů. Zobrazení rozpočtu ve vztahu k trendu výdajů je jedním z prvních kroků při zahájení [analýzy nákladů a výdajů](./quick-acm-cost-analysis.md).

:::image type="content" source="./media/tutorial-acm-create-budgets/cost-analysis.png" alt-text="Snímek obrazovky znázorňující příklad rozpočtu s útratami zobrazenými v analýze nákladů" lightbox="./media/tutorial-acm-create-budgets/cost-analysis.png" :::

V předchozím příkladu jste vytvořili rozpočet pro předplatné. Můžete také vytvořit rozpočet pro skupinu prostředků. Pokud chcete vytvořit rozpočet pro skupinu prostředků, přejděte na **Správa nákladů a fakturace** &gt; **Předplatné** &gt; vyberte předplatné > **Skupiny prostředků** > vyberte skupinu prostředků > **Rozpočty** > a pak kliknutím na **Přidat** přidejte rozpočet.

### <a name="create-a-budget-for-combined-azure-and-aws-costs"></a>Vytvoření rozpočtu pro kombinované náklady na Azure a AWS

Náklady na Azure a AWS můžete seskupit tak, že k vašemu konektoru přiřadíte skupinu pro správu a také příslušné konsolidované a propojené účty. Přiřaďte předplatná Azure ke stejné skupině pro správu. Potom vytvořte rozpočet pro kombinované náklady.

1. Ve službě Cost Management vyberte **Rozpočty**.
1. Vyberte **Přidat**.
1. Vyberte **Změnit rozsah** a potom vyberte skupinu pro správu.
1. Pokračujte ve vytváření rozpočtu, dokud ho nedokončíte.

## <a name="costs-in-budget-evaluations"></a>Náklady ve vyhodnocení rozpočtu

Vyhodnocení rozpočtových nákladů nyní zahrnují rezervovanou instanci a data nákupu. Pokud se na vás vztahují poplatky, můžete dostávat upozornění, protože poplatky jsou začleněny do vašich hodnocení. Přihlaste se k [Azure Portal](https://portal.azure.com) a ověřte, že pro nové náklady jsou správně nakonfigurovány prahové hodnoty rozpočtu. Vaše poplatky za Azure se nezměnily. Rozpočty se teď vyhodnocují na základě ucelenější sady vašich nákladů. Pokud se na vás poplatky nevztahují, chování rozpočtu se nezmění.

Jestliže chcete nové náklady vyfiltrovat, aby se rozpočty vyhodnotily jenom na základě poplatků za využití Azure, přidejte do svého rozpočtu následující filtry:

- Typ vydavatele: Azure
- Typ poplatku: Využití

Vyhodnocení rozpočtových nákladů vychází ze skutečných nákladů a nezahrnuje amortizaci. Další informace o možnostech filtrování, které máte k dispozici v rozpočtech, najdete v tématu týkajícím se [principů možností seskupování a filtrování](group-filter.md).

## <a name="trigger-an-action-group"></a>Aktivace skupiny akcí

Když vytváříte nebo upravujete rozpočet pro předplatné nebo obor skupiny prostředků, můžete ho nakonfigurovat tak, aby volal skupinu akcí. Skupina akcí může při dosažení prahové hodnoty rozpočtu provádět různé akce. 

Skupiny akcí jsou aktuálně podporovány jenom pro předplatná a obory skupin prostředků. Další informace o vytváření skupin akcí najdete v tématu [Konfigurace základních nastavení skupin akcí](../../azure-monitor/alerts/action-groups.md#configure-basic-action-group-settings). 

Další informace o používání automatizace pro rozpočty pomocí skupin akcí najdete v tématu popisujícím [správu nákladů pomocí rozpočtů Azure](../manage/cost-management-budget-scenario.md).

Chcete-li vytvořit nebo aktualizovat skupiny akcí, vyberte možnost **spravovat skupinu akcí** při vytváření nebo úpravách rozpočtu.

:::image type="content" source="./media/tutorial-acm-create-budgets/trigger-action-group.png" alt-text="Snímek obrazovky znázorňující příklad vytvoření rozpočtu pro zobrazení skupin akcí správy." lightbox="./media/tutorial-acm-create-budgets/trigger-action-group.png" :::

Potom vyberte **Přidat skupinu akcí** a vytvořte skupinu akcí.

Integrace rozpočtu se skupinami akcí funguje jenom pro skupiny akcí, které mají zakázané běžné schéma upozornění. Další informace o zakázání schématu najdete v tématu popisujícím [možnosti povolení běžného schématu upozornění](../../azure-monitor/alerts/alerts-common-schema.md#how-do-i-enable-the-common-alert-schema).

## <a name="create-and-edit-budgets-with-powershell"></a>Vytváření a úpravy rozpočtů pomocí PowerShellu

Pokud jste zákazníkem se smlouvou EA, můžete rozpočty vytvářet a upravovat programově pomocí modulu Azure PowerShell. Pokud chcete stáhnout nejnovější verzi Azure PowerShellu, spusťte následující příkaz:

```azurepowershell-interactive
install-module -name Az
```

Následující ukázkové příkazy vytvoří rozpočet.

```azurepowershell-interactive
#Sign into Azure Powershell with your account

Connect-AzAccount

#Select a subscription to to monitor with a budget

select-AzSubscription -Subscription "Your Subscription"

#Create an action group email receiver and corresponding action group

$email1 = New-AzActionGroupReceiver -EmailAddress test@test.com -Name EmailReceiver1
$ActionGroupId = (Set-AzActionGroup -ResourceGroupName YourResourceGroup -Name TestAG -ShortName TestAG -Receiver $email1).Id

#Create a monthly budget that sends an email and triggers an Action Group to send a second email. Make sure the StartDate for your monthly budget is set to the first day of the current month. Note that Action Groups can also be used to trigger automation such as Azure Functions or Webhooks.

New-AzConsumptionBudget -Amount 100 -Name TestPSBudget -Category Cost -StartDate 2020-02-01 -TimeGrain Monthly -EndDate 2022-12-31 -ContactEmail test@test.com -NotificationKey Key1 -NotificationThreshold 0.8 -NotificationEnabled -ContactGroup $ActionGroupId
```

## <a name="create-a-budget-with-an-azure-resource-manager-template"></a>Vytvoření rozpočtu pomocí šablony Azure Resource Manageru

Pomocí šablony Azure Resource Manageru můžete vytvořit rozpočet. Pokud chcete šablonu použít, projděte si téma [Vytvoření rozpočtu pomocí šablony Azure Resource Manageru](quick-create-budget-template.md).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud jste vytvořili rozpočet a už ho nepotřebujete, projděte si jeho podrobnosti a odstraňte ho.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Vytvoření rozpočtu na portálu Azure Portal
> * Vytváření a úpravy rozpočtů pomocí PowerShellu
> * Vytvoření rozpočtu pomocí šablony Azure Resource Manageru

V dalším kurzu se dozvíte, jak vytvořit opakovaný export pro data služby Cost Management.

> [!div class="nextstepaction"]
> [Vytvoření a správa exportovaných dat](tutorial-export-acm-data.md)