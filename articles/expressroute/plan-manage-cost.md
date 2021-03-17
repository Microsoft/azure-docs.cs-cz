---
title: Plánování správy nákladů pro Azure ExpressRoute
description: Naučte se plánovat a spravovat náklady na Azure ExpressRoute pomocí analýzy nákladů v Azure Portal.
author: duongau
ms.author: duau
ms.custom: subject-cost-optimization
ms.service: expressroute
ms.topic: how-to
ms.date: 11/30/2020
ms.openlocfilehash: de8405477611d62b8a46e8b6b645887cc4d30099
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/26/2021
ms.locfileid: "98784237"
---
# <a name="plan-and-manage-costs-for-azure-expressroute"></a>Plánování a Správa nákladů pro Azure ExpressRoute

Tento článek popisuje, jak plánujete a spravovat náklady na ExpressRoute. Nejprve pomocí cenové kalkulačky Azure pomůže naplánovat náklady na ExpressRoute ještě před tím, než přidáte jakékoli prostředky pro službu, abyste mohli odhadnout náklady. Pak při přidávání prostředků Azure si Projděte odhadované náklady. 

Po zahájení používání prostředků ExpressRoute použijte funkce Cost Management k nastavení rozpočtů a monitorování nákladů. Můžete si také projít předpokládané náklady a identifikovat trendy útraty, které identifikují oblasti, kde můžete chtít pracovat. 

Pamatujte, že náklady na ExpressRoute jsou jenom částí měsíčních nákladů na faktuře Azure. I když tento článek vysvětluje, jak plánovat a spravovat náklady na ExpressRoute, účtuje se vám všechny služby a prostředky Azure, které se používají ve vašem předplatném Azure, včetně služeb třetích stran.

## <a name="prerequisites"></a>Požadavky

Analýza nákladů v Cost Management podporuje většinu typů účtů Azure, ale ne všechny. Úplný seznam podporovaných typů účtů si můžete prohlédnout v článku [Vysvětlení dat služby Cost Management](../cost-management-billing/costs/understand-cost-mgt-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn). K zobrazení dat o nákladech potřebujete alespoň oprávnění ke čtení pro účet Azure. 

Informace o přiřazování přístupu k datům služby Azure Cost Management najdete v článku [Přiřazení přístupu k datům](../cost-management-billing/costs/assign-access-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="local-vs-standard-vs-premium"></a>Místní vs. Standard vs. Premium

ExpressRoute má tři různé SKU okruhů: [*Local*](./expressroute-faqs.md#expressroute-local), *Standard* a [*Premium*](./expressroute-faqs.md#expressroute-premium). Způsob, jakým se vám účtují ExpressRoute využití, se liší mezi těmito třemi typy SKU. S místními SKU se vám automaticky účtují neomezený datový tarif. S SKU Standard a Premium můžete vybrat mezi datovým tarifem měřený nebo neomezený. Všechna data příchozího přenosu dat jsou bezplatná s výjimkou použití doplňku Global Reach. Je důležité pochopit, které typy SKU a datový tarif nejlépe fungují pro vaše úlohy, aby se co nejlépe optimalizoval náklady a rozpočet.

## <a name="estimate-costs"></a>Odhadněte náklady

Pomocí [cenové kalkulačky Azure](https://azure.microsoft.com/pricing/calculator/) můžete odhadnout náklady předtím, než vytvoříte okruh ExpressRoute. 

1. Na levé straně vyberte **sítě** a začněte tím, že vyberete **Azure ExpressRoute** . 

1. V závislosti na umístění partnerského vztahu vyberte příslušnou *zónu* . Pokud chcete v rozevíracím seznamu vybrat příslušnou *zónu* , přečtěte si téma [poskytovatelé připojení ExpressRoute](./expressroute-locations-providers.md#partners) . 

1. Pak vyberte *SKU*, *rychlost obvodu* a *datový tarif* , pro který chcete odhadnout. 

1. V části *Další přenos odchozích dat* zadejte odhad v GB, kolik odchozích dat můžete v průběhu měsíce použít. 

1. Nakonec můžete přidat *doplněk Global REACH* k odhadu.

Následující snímek obrazovky ukazuje odhad nákladů pomocí kalkulačky:

:::image type="content" source="media/plan-manage-cost/capacity-calculator-cost-estimate.png" alt-text="Odhad nákladů ExpressRoute ve kalkulačkě Azure":::

Další informace najdete v tématu [ceny služby Azure ExpressRoute](https://azure.microsoft.com/pricing/details/expressroute/).

### <a name="expressroute-gateway-estimated-cost"></a>Odhadované náklady brány ExpressRoute

Pokud k propojení virtuální sítě s okruhem ExpressRoute používáte bránu ExpressRoute, použijte následující postup k odhadu nákladů na využití brány.

1. Na levé straně vyberte **sítě** a začněte výběrem **VPN Gateway** . 

1. Vyberte *oblast* pro bránu a pak změňte *typ* na **ExpressRoute Gateway**.

1. Z rozevíracího seznamu vyberte *typ brány* .

1. Zadejte *hodiny brány*. (720 hodin = 30 dní)

## <a name="understand-the-full-billing-model-for-expressroute"></a>Pochopení modelu úplné fakturace pro ExpressRoute

ExpressRoute běží na infrastruktuře Azure, která při nasazení nového prostředku nasazuje náklady spolu s ExpressRoute. Je důležité pochopit, že další infrastruktura by mohla navýšit náklady. Pokud provedete změny nasazených prostředků, musíte tyto náklady spravovat. 

### <a name="costs-that-typically-accrue-with-expressroute"></a>Náklady, které obvykle nabíhají s ExpressRoute

Při vytváření okruhu ExpressRoute se můžete rozhodnout vytvořit bránu ExpressRoute pro propojení vaší virtuální sítě s okruhem. Brány se účtují hodinovou sazbou a náklady na okruh ExpressRoute. Podívejte se na [ceny ExpressRoute](https://azure.microsoft.com/en-us/pricing/details/expressroute) a vyberte brány ExpressRoute, abyste viděli sazby různých SKU brány.
 
### <a name="costs-might-accrue-after-resource-deletion"></a>Náklady se můžou po odstranění prostředku časově rozlišit.

Pokud máte po odstranění okruhu ExpressRoute bránu ExpressRoute, bude se vám účtovat náklady, dokud ji neodstraníte.

### <a name="using-azure-prepayment-credit"></a>Používání předplacených kreditů Azure

Za poplatky za ExpressRoute můžete platit pomocí kreditu za předplacenou službu Azure (dříve označovaný jako peněžní závazek). Nemůžete však použít kredit plateb Azure k placení poplatků za produkty a služby třetích stran, včetně těch, které jsou Azure Marketplace.

## <a name="monitor-costs"></a>Sledovat náklady

Při používání prostředků Azure s ExpressRoute se účtují náklady. Náklady na jednotku využívání prostředků Azure se liší podle časových intervalů (sekundy, minuty, hodiny a dny) nebo podle využití jednotek (bajty, megabajtů atd.). Jakmile začne použití ExpressRoute, účtují se náklady a náklady se vám budou zobrazovat v [analýze nákladů](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

Když použijete analýzu nákladů, zobrazí se v grafech a tabulkách v různých časových intervalech náklady na okruh ExpressRoute. Některé příklady jsou podle dne, aktuálního a předchozího měsíce a roku. Také se zobrazí náklady na rozpočty a předpokládané náklady. Přepínání na delší zobrazení v průběhu času vám může pomáhat identifikovat trendy útraty. A vidíte, kde mohlo dojít k předanému nadměrnému útratu. Pokud jste vytvořili rozpočty, můžete také snadno zjistit, kde jsou překročeny.

Zobrazení nákladů na ExpressRoute při analýze nákladů:

1. Přihlaste se k portálu Azure.

1. Přejděte na **odběry**, v seznamu vyberte předplatné a v nabídce vyberte  **Analýza nákladů** . Vyberte **obor** , který se má při analýze nákladů přepnout na jiný obor. Ve výchozím nastavení se náklady na služby zobrazují v prvním prstenovém grafu.

    Skutečné měsíční náklady se zobrazí při počátečním otevření analýzy nákladů. Tady je příklad zobrazující všechny měsíční náklady na používání.

    :::image type="content" source="media/plan-manage-cost/cost-analysis-pane.png" alt-text="Příklad znázorňující nahromaděné náklady na předplatné":::
    

1.  Chcete-li zúžit náklady na jednu službu, například ExpressRoute, vyberte možnost **Přidat filtr** a potom vyberte možnost **název služby**. Pak vyberte **ExpressRoute**.

    Tady je příklad znázorňující náklady jenom pro ExpressRoute.

    :::image type="content" source="media/plan-manage-cost/cost-analysis-pane-expressroute.png" alt-text="Příklad znázorňující akumulované náklady pro ExpressRoute":::

V předchozím příkladu vidíte aktuální náklady za službu. Zobrazují se také náklady podle oblastí Azure (umístění) a ExpressRoute nákladů podle skupiny prostředků. Z tohoto místa můžete sami prozkoumat náklady.

## <a name="create-budgets-and-alerts"></a>Vytvoření rozpočtů a upozornění

Pro účely řízení nákladů můžete vytvořit [rozpočty](../cost-management-billing/costs/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) a nastavit [upozornění](../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn), která účastníky automaticky upozorňují na anomálie a nebezpečí nadměrných výdajů. Upozornění jsou založena na porovnání útraty s rozpočtem a prahovými hodnotami nákladů. Rozpočty a výstrahy se vytvářejí pro předplatná Azure a skupiny prostředků, takže jsou užitečné v rámci celkové strategie monitorování nákladů. 

Rozpočty je možné vytvořit s filtry pro konkrétní prostředky nebo služby v Azure, pokud chcete mít v monitorování k dispozici větší členitost. Filtry vám pomůžou zajistit, aby nedošlo k náhodnému vytváření nových prostředků s náklady na další peníze. Další informace o možnostech filtru při vytváření rozpočtu najdete v tématu [Možnosti skupiny a filtru](../cost-management-billing/costs/group-filter.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="export-cost-data"></a>Export dat nákladů

[Data nákladů](../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) můžete také exportovat do účtu úložiště. To je užitečné v případě, že potřebujete nebo jiné provádět analýzu dat pro náklady. Finanční tým může například analyzovat data pomocí aplikace Excel nebo Power BI. Náklady můžete exportovat na denní, týdenní nebo měsíční plán a nastavit vlastní rozsah kalendářních dat. Export nákladových dat je doporučený způsob, jak načíst datové sady nákladů.

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o tom, jak ceny pracují s Azure ExpressRoute. Podívejte se na téma [Přehled cen v Azure ExpressRoute](https://azure.microsoft.com/en-us/pricing/details/expressroute/).
- Naučte se [optimalizovat investice do cloudu pomocí Azure cost management](../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Přečtěte si další informace o správě nákladů pomocí [analýzy nákladů](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Přečtěte si, jak se [vyhnout neočekávaným nákladům](../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Využijte kurz učení s asistencí [cost management](/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) .