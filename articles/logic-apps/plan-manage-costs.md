---
title: Plánování správy nákladů na Azure Logic Apps
description: Naučte se plánovat a spravovat náklady na Azure Logic Apps pomocí analýzy nákladů v Azure Portal
ms.service: logic-apps
ms.reviewer: estfan, logicappspm, azla
ms.topic: how-to
ms.custom: subject-cost-optimization
ms.date: 01/29/2021
ms.openlocfilehash: 58e12862cf00b500bced105d67fede8599c2a257
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/30/2021
ms.locfileid: "99180451"
---
# <a name="plan-and-manage-costs-for-azure-logic-apps"></a>Plánování a Správa nákladů na Azure Logic Apps

Tento článek vám pomůže plánovat a spravovat náklady na Azure Logic Apps. Před vytvořením nebo přidáním prostředků pomocí této služby můžete odhadnout náklady pomocí cenové kalkulačky Azure. Po zahájení používání prostředků Logic Apps můžete nastavit rozpočty a monitorovat náklady pomocí [Azure cost management](https://docs.microsoft.com/azure/cost-management-billing/cost-management-billing-overview?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn). Pro identifikaci oblastí, kde můžete chtít pracovat, můžete také zkontrolovat předpokládané náklady a monitorovat trendy útraty.

Mějte na paměti, že náklady na Logic Apps jsou jenom částmi měsíčních nákladů na faktuře Azure. I když tento článek vysvětluje, jak odhadnout a spravovat náklady na Logic Apps, účtují se vám všechny služby a prostředky Azure, které se používají ve vašem předplatném Azure, včetně všech služeb třetích stran. Až budete obeznámeni se správou nákladů na Logic Apps, můžete použít podobné metody pro správu nákladů na všechny služby Azure používané ve vašem předplatném.

## <a name="prerequisites"></a>Požadavky

<!--Note for Azure service writer: This section covers prerequisites for the Cost Management's Cost Analysis feature. Add other prerequisites needed for your service after the Cost Management prerequisites. -->

[Azure cost management](https://docs.microsoft.com/azure/cost-management-billing/cost-management-billing-overview?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) podporuje většinu typů účtů Azure. Pokud chcete zobrazit všechny podporované typy účtů, přečtěte si téma [principy cost management dat](https://docs.microsoft.com/azure/cost-management-billing/costs/understand-cost-mgt-data?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn). Pokud chcete zobrazit data o nákladech, potřebujete přinejmenším oprávnění ke čtení k vašemu účtu Azure.

Informace o přiřazování přístupu k datům služby Azure Cost Management najdete v článku [Přiřazení přístupu k datům](https://docs.microsoft.com/azure/cost-management/assign-access-acm-data?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

<!--Note for Azure service writer: If you have other prerequisites for your service, add them here -->

<a name="understand-billing-model"></a>

## <a name="understand-the-billing-model"></a>Pochopení modelu fakturace

Azure Logic Apps běží na infrastruktuře Azure, která při nasazení nových prostředků [narůstá náklady](https://azure.microsoft.com/pricing/details/logic-apps/) . Ujistěte se, že rozumíte [modelu fakturace pro Logic Apps službu spolu se souvisejícími prostředky Azure](logic-apps-pricing.md)a když provedete změny nasazených prostředků, spravujte náklady vyplývající z těchto závislostí.

<a name="typical-costs"></a>

### <a name="costs-that-typically-accrue-with-azure-logic-apps"></a>Náklady, které obvykle nabíhají Azure Logic Apps

Služba Logic Apps v závislosti na prostředcích, které vytvoříte a použijete, používá různé cenové modely:

* Prostředky aplikace logiky, které vytvoříte a spustíte ve víceklientské Logic Apps službě, používají [cenový model pro spotřebu](../logic-apps/logic-apps-pricing.md#consumption-pricing).

* Prostředky aplikace logiky, které vytvoříte a spustíte v [prostředí ISE (Integration Service Environment)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) , používají [pevný cenový model](../logic-apps/logic-apps-pricing.md#fixed-pricing).

Tady jsou další zdroje, které vám při vytváření služby Logic Apps účtují náklady:

* [Účet pro integraci](../logic-apps/logic-apps-pricing.md#integration-accounts) je samostatný prostředek, který vytvoříte a propojíte s Logic Apps pro vytváření integrací B2B. Účty pro integraci využívají [pevný cenový model](../logic-apps/logic-apps-pricing.md#integration-accounts) , ve kterém je sazba založená na typu nebo *úrovni* integračního účtu, který používáte.

* [ISE](../logic-apps/logic-apps-pricing.md#fixed-pricing) je samostatný prostředek, který vytvoříte jako umístění nasazení pro Logic Apps, které potřebují přímý přístup k prostředkům ve virtuální síti. ISEs použijte [pevný cenový model](../logic-apps/logic-apps-pricing.md#fixed-pricing) , ve kterém je sazba založená na SKU ISE, kterou vytvoříte, a na dalších nastaveních.

* [Vlastní konektor](../logic-apps/logic-apps-pricing.md#consumption-pricing) je samostatný prostředek, který vytvoříte pro REST API, který nemá předem vytvořený konektor, který byste mohli použít ve svých aplikacích logiky. Vlastní spuštění konektoru používá [cenový model spotřeby](../logic-apps/logic-apps-pricing.md#consumption-pricing) s výjimkou případů, kdy je použijete v ISE.

* V rámci služby Logic Apps více tenantů, [uchovávání dat a spotřeba úložiště](../logic-apps/logic-apps-pricing.md#data-retention) s využitím [pevného cenového modelu](../logic-apps/logic-apps-pricing.md#fixed-pricing)se účtují náklady. Například vstupy a výstupy z historie spouštění jsou uchovávány v pozadí úložiště, které se liší od prostředků úložiště, které nezávisle vytváříte, spravujete a přistupujete z aplikace logiky.

  V ISE se neúčtují náklady na uchovávání dat a využití úložiště.

<a name="costs-after-resource-deletion"></a>

### <a name="costs-might-accrue-after-resource-deletion"></a>Náklady se můžou po odstranění prostředku časově rozlišit.

<!--Note to Azure service writer: You might need to sync with your product team to identify resources that continue to exist after those ones for your service are deleted. If you're certain that no resources can exist after those for your service are deleted, then omit this section. -->

Po odstranění aplikace logiky nebude služba Logic Apps vytvářet ani spouštět nové instance pracovního postupu. Všechny probíhající a probíhající běhy ale budou pokračovat, dokud se nedokončí. V závislosti na počtu těchto spuštění může tento proces nějakou dobu trvat. Další informace najdete v tématu [Správa aplikací logiky](manage-logic-apps-with-azure-portal.md#delete-logic-apps).

Pokud máte tyto prostředky po odstranění aplikace logiky, tyto prostředky nadále existují a nabíhají náklady, dokud je neodstraníte:

* Prostředky Azure, které vytváříte a spravujete nezávisle na aplikaci logiky, která se k těmto prostředkům připojuje, například aplikace funkcí Azure, centra událostí, služby Event Grid atd.

* Účty pro integraci

* Prostředí služby Integration Service (ISEs)

  Pokud [ISE odstraníte](ise-manage-integration-service-environment.md#delete-ise), bude dál existovat přidružená virtuální síť Azure, podsítě a další související prostředky. Po odstranění ISE možná budete muset počkat na určitý počet hodin, než budete moct zkusit odstranit virtuální síť nebo podsítě.

### <a name="using-monetary-credit-with-azure-logic-apps"></a>Použití peněžního kreditu s Azure Logic Apps

Můžete platit za Azure Logic Apps poplatky s kreditem peněžního závazku EA. Kredity peněžních závazků EA ale nemůžete použít k placení poplatků za produkty a služby třetích stran, včetně těch, které jsou Azure Marketplace.

<a name="estimate-costs"></a>

## <a name="estimate-costs"></a>Odhadněte náklady

Než začnete vytvářet prostředky s Azure Logic Apps, Odhadněte náklady pomocí [cenové kalkulačky Azure](https://azure.microsoft.com/pricing/calculator/). Další informace najdete v [cenovém modelu pro Azure Logic Apps](../logic-apps/logic-apps-pricing.md).

1. Na [stránce cenové kalkulačky Azure](https://azure.microsoft.com/pricing/calculator/)v nabídce vlevo vyberte **integrace**  >  **Azure Logic Apps**.

   ![Snímek obrazovky zobrazující cenovou kalkulačku Azure s vybraným Azure Logic Apps](./media/plan-manage-costs/add-azure-logic-apps-pricing-calculator.png)

1. Posuňte se dolů, dokud nebudete moct zobrazit Azure Logic Apps cenové kalkulačky. V různých oddílech pro prostředky Azure, které přímo souvisejí s Azure Logic Apps, zadejte počet prostředků, které plánujete použít, a počet intervalů, po kterých můžete tyto prostředky používat.

   Tento snímek obrazovky ukazuje příklad odhadu nákladů pomocí kalkulačky:

   ![Příklad znázorňující odhadované náklady v cenové kalkulačkě Azure](./media/plan-manage-costs/example-logic-apps-pricing-calculator.png)

1. Pokud chcete při vytváření a používání nových souvisejících prostředků aktualizovat odhadované náklady, vraťte se k této kalkulačkě a aktualizujte tyto prostředky.

<a name="create-budgets-alerts"></a>

## <a name="create-budgets-and-alerts"></a>Vytvoření rozpočtů a upozornění

Aby vám pomohla aktivně spravovat náklady pro svůj účet nebo předplatné Azure, můžete vytvářet [rozpočty](https://docs.microsoft.com/azure/cost-management/tutorial-acm-create-budgets?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) a [výstrahy](https://docs.microsoft.com/azure/cost-management/cost-mgt-alerts-monitor-usage-spending?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) pomocí služby [Azure cost management a fakturace](https://docs.microsoft.com/azure/cost-management-billing/cost-management-billing-overview?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) a možností.  Rozpočty a výstrahy se vytvářejí pro předplatná Azure a skupiny prostředků, takže jsou užitečné v rámci celkové strategie monitorování nákladů.

Na základě útraty, které jsou v porovnání s rozpočtovými a nákladové prahové hodnoty, výstrahy automaticky informují účastníky o anomáliích útraty a rizikech Pokud chcete sledovat více členitosti, můžete také vytvořit rozpočty, které používají filtry pro konkrétní prostředky nebo služby v Azure. Filtry vám pomůžou zajistit, že nechtěně nevytváříte nové prostředky, které vám povedou peníze. Další informace o možnostech filtru najdete v tématu [Možnosti skupiny a filtru](https://docs.microsoft.com/azure/cost-management-billing/costs/group-filter?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

<a name="monitor-costs"></a>

## <a name="monitor-costs"></a>Sledovat náklady

Náklady na jednotku využívání prostředků se liší podle časových intervalů, například sekund, minut, hodin a dnů, nebo podle využití jednotek, například bajtů, megabajtů a tak dále. Některé příklady jsou podle dne, aktuálního a předchozího měsíce a roku. Přepínání na delší zobrazení v průběhu času vám může pomáhat identifikovat trendy útraty. Při použití funkcí analýzy nákladů můžete zobrazit náklady jako grafy a tabulky v různých časových intervalech. Pokud jste vytvořili rozpočty a prognózy nákladů, můžete také snadno najít, kde jsou rozpočty překročeny a kdy mohlo dojít k nadměrnému trvání.

Po zahájení práce s náklady na prostředky, které se v Azure vytvářejí nebo začnou používat, můžete tyto náklady zkontrolovat a monitorovat následujícími způsoby:

* [Monitorování spuštění aplikace logiky a využití úložiště](#monitor-billing-metrics) pomocí Azure monitor

* Spuštění [analýzy nákladů](https://docs.microsoft.com/azure/cost-management/quick-acm-cost-analysis?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) pomocí [Azure cost management a fakturace](https://docs.microsoft.com/azure/cost-management-billing/cost-management-billing-overview?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)

<a name="monitor-billing-metrics"></a>

### <a name="monitor-logic-app-executions-and-storage-consumption"></a>Monitorování spuštění aplikace logiky a využití úložiště

Pomocí Azure Monitor můžete zobrazit tyto metriky pro konkrétní aplikaci logiky:

* Fakturovatelné provádění akcí
* Fakturovatelná spuštění triggerů
* Využití fakturace pro provádění nativních operací
* Využití fakturace pro spuštění standardních konektorů
* Využití fakturace pro spotřebu úložiště
* Fakturovatelná spuštění celkem

<a name="execution-storage-metrics"></a>

#### <a name="view-execution-and-storage-consumption-metrics"></a>Zobrazit metriky spotřeby pro spuštění a úložiště

1. V Azure Portal Najděte a otevřete aplikaci logiky. V nabídce aplikace logiky v části **monitorování** vyberte **metriky**.

1. V pravém podokně v části **název grafu** otevřete seznam **metriky** a vyberte požadovanou metriku.

   > [!NOTE]
   > Spotřeba úložiště se měří jako počet jednotek úložiště (GB), které vaše aplikace logiky používá, a účtuje se. Spuštění, které v úložišti používá méně než 500 MB, se nemusí zobrazit v zobrazení monitorování, ale pořád se účtují.

   ![Snímek obrazovky, který zobrazuje podokno metriky s otevřeným seznamem metrik.](./media/logic-apps-pricing/select-metric.png)

1. V pravém horním rohu podokna vyberte časové období, které chcete.

1. Pokud chcete zobrazit další data o spotřebě úložiště, konkrétně akce vstupu a výstupu v historii spuštění vaší aplikace logiky, [postupujte podle těchto kroků](#view-input-output-sizes).

<a name="view-input-output-sizes"></a>

#### <a name="view-action-input-and-output-sizes-in-run-history"></a>Zobrazit vstupní a výstupní velikosti akce v historii spuštění

1. V Azure Portal Najděte a otevřete aplikaci logiky.

1. V nabídce aplikace logiky zvolte **Přehled**.

1. V podokně na pravé straně v části **historie spuštění** vyberte běh, který obsahuje vstupy a výstupy, které chcete zobrazit.

1. V části **spuštění aplikace logiky** vyberte **Spustit podrobnosti**.

1. V podokně s **podrobnostmi o spuštění aplikace logiky** v tabulce akce, které uvádí stav a dobu trvání jednotlivých akcí, vyberte akci, kterou chcete zobrazit.

1. V podokně **Akce aplikace logiky** Najděte velikosti pro vstupy a výstupy této akce. V části **vstupy odkaz** a **výstupy** vyhledejte odkazy na tyto vstupy a výstupy.

   > [!NOTE]
   > U smyček se pouze akce nejvyšší úrovně zobrazují velikosti pro jejich vstupy a výstupy. Pro akce uvnitř vnořených smyček se vstupy a výstupy zobrazují nulové velikosti a žádné odkazy.

<a name="run-cost-analysis"></a>

### <a name="run-cost-analysis-by-using-azure-cost-management-and-billing"></a>Spuštění analýzy nákladů pomocí Azure Cost Management a fakturace

Pokud chcete zkontrolovat náklady na službu Logic Apps v závislosti na konkrétním oboru, například v rámci předplatného Azure, můžete využít možnosti [analýzy nákladů](https://docs.microsoft.com/azure/cost-management/quick-acm-cost-analysis?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) v [Azure cost management a fakturaci](https://docs.microsoft.com/azure/cost-management-billing/cost-management-billing-overview?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

1. V Azure Portal otevřete požadovaný obor, například předplatné Azure. V nabídce vlevo v části **cost management** vyberte **Analýza nákladů**.

   Když poprvé otevřete podokno analýza nákladů, v horním grafu se zobrazí skutečné a předpokládané náklady na využití napříč všemi službami v rámci předplatného aktuálního měsíce.

   ![Snímek obrazovky, který zobrazuje podokno Azure Portal a analýza nákladů s příkladem pro skutečné a předpokládané náklady v předplatném.](./media/plan-manage-costs/cost-analysis-total-actual-forecasted-costs.png)

   > [!TIP]
   > Chcete-li změnit obory, klikněte v podokně **Analýza nákladů** na panelu filtry na možnost filtr **oboru** . V podokně **Vybrat obor** přepněte na požadovaný obor.

   V prstencovém grafu se zobrazí aktuální náklady podle služeb Azure, podle oblasti Azure (umístění) a podle skupiny prostředků.

   ![Snímek obrazovky, který zobrazuje Azure Portal a podokno analýza nákladů pomocí ukázkových prstencových grafů pro služby, oblasti a skupiny prostředků.](./media/plan-manage-costs/cost-analysis-donut-charts.png)

1. Pokud chcete graf filtrovat do konkrétní oblasti, například služby nebo prostředku, na panelu Filtry vyberte **Přidat filtr**.

1. V seznamu na levé straně vyberte typ filtru, například **název služby**. V seznamu na pravé straně vyberte filtr, například **Logic Apps**. Až budete hotovi, vyberte zelený symbol zaškrtnutí.

   ![Snímek obrazovky, který zobrazuje Azure Portal a podokno analýza nákladů s výběry filtru.](./media/plan-manage-costs/cost-analysis-add-service-name-filter.png)

   Tady je například výsledek pro službu Logic Apps:

   ![Snímek obrazovky, který zobrazuje Azure Portal a podokno analýza nákladů s výsledky filtrovanými podle "Logic Apps".](./media/plan-manage-costs/cost-analysis-total-actual-costs-service.png)

### <a name="export-cost-data"></a>Export dat nákladů

Pokud potřebujete provést další analýzu dat pro náklady, můžete [exportovat nákladovou data](https://docs.microsoft.com/azure/cost-management-billing/costs/tutorial-export-acm-data?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) do účtu úložiště. Finanční tým může například analyzovat tato data pomocí aplikace Excel nebo Power BI. Náklady můžete exportovat na denní, týdenní nebo měsíční plán a nastavit vlastní rozsah kalendářních dat. Export nákladových dat je doporučený způsob, jak načíst datové sady nákladů.

## <a name="other-ways-to-manage-and-reduce-costs"></a>Další způsoby správy a snížení nákladů

<!-- Note to Azure service writer: This section is optional. Other than using the Cost Management methods above, your service probably has other specific ways to minimize costs. For example, you might have best practice advice or specific ways to reduce costs that are specific to your service. If so, try to add that guidance here or at least summarize key points. Try to be as prescriptive as possible. If you have more comprehensive content, add links to your other published articles or sections here.

Add a statement that discusses any recommended settings for your service that might help keep the charges minimal if a service isn't being actively used by the customer. For example: Will turning off a VM help to get no charges for the specific VM resource?

Otherwise, if no other cost-saving recommendations or best practices exist to reduce costs, cut this section.
-->

Pokud chcete snížit náklady na Logic AP a související prostředky, zkuste tyto možnosti:

* Pokud je to možné, využijte [integrované triggery a akce](../connectors/apis-list.md#built-in), které s cenou méně poběží na spuštění než [triggery spravovaného konektoru a akce](../connectors/apis-list.md#managed-connectors).

  Například může být možné snížit náklady při přístupu k jiným prostředkům pomocí [akce http](../connectors/connectors-native-http.md) nebo voláním funkce, kterou jste vytvořili pomocí [služby Azure Functions](../azure-functions/functions-overview.md) a pomocí [integrované Azure Functions akce](../logic-apps/logic-apps-azure-functions.md). Použití Azure Functions také má za následek i náklady, takže se ujistěte, že jste porovnali své možnosti.

* [Zadejte přesné podmínky triggeru](logic-apps-workflow-actions-triggers.md#trigger-conditions) pro spuštění pracovního postupu.

  Můžete například určit, že Trigger se aktivuje jenom v případě, že cílový web vrátí vnitřní chybu serveru. V definici JSON triggeru použijte `conditions` vlastnost k určení podmínky, která odkazuje na stavový kód triggeru.

* Pokud aktivační událost obsahuje verzi cyklického dotazování a verzi Webhooku, zkuste použít verzi Webhooku, která čeká na zadanou událost před vypálením, a ne při pravidelné kontrole události.

* Zavolejte svoji aplikaci logiky prostřednictvím jiné služby, aby se Trigger aktivoval jenom v případě, že by se měl pracovní postup spustit.

  Aplikaci logiky můžete volat například z funkce, kterou vytvoříte a spustíte pomocí služby Azure Functions. Podívejte se například na téma [volání nebo Aktivace Logic Apps pomocí Azure functions a Azure Service Bus](logic-apps-scenario-function-sb-trigger.md).

* [Zakažte aplikace logiky](manage-logic-apps-with-azure-portal.md#disable-or-enable-logic-apps) , které nemusíte spouštět nepřetržitě, nebo [odstraňte aplikace logiky](manage-logic-apps-with-azure-portal.md#delete-logic-apps) , které už nepotřebujete. Pokud je to možné, zakažte všechny další prostředky, které nepotřebujete trvale aktivní.

## <a name="next-steps"></a>Další kroky

* [Optimalizace investic do cloudu se službou Azure Cost Management](https://docs.microsoft.com/azure/cost-management-billing/costs/cost-mgt-best-practices?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
* [Správa nákladů pomocí analýzy nákladů](https://docs.microsoft.com/azure/cost-management-billing/costs/quick-acm-cost-analysis?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
* [Jak zabránit neočekávaným nákladům](https://docs.microsoft.com/azure/cost-management-billing/understand/analyze-unexpected-charges?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
* Pořídit kurz učení s asistencí [cost management](https://docs.microsoft.com/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)


