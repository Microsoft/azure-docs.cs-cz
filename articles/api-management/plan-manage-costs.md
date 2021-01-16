---
title: Plánování a Správa nákladů pro Azure API Management
description: Naučte se plánovat a spravovat náklady na Azure API Management pomocí analýzy nákladů v Azure Portal.
author: dlepow
ms.author: apimpm
ms.custom: subject-cost-optimization
ms.service: api-management
ms.topic: how-to
ms.date: 12/15/2020
ms.openlocfilehash: 90449a29f58f31827e2a36b5f6c41563e88ac032
ms.sourcegitcommit: 25d1d5eb0329c14367621924e1da19af0a99acf1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/16/2021
ms.locfileid: "98251348"
---
# <a name="plan-and-manage-costs-for-api-management"></a>Plánování a Správa nákladů na API Management

Tento článek popisuje, jak plánujete a spravovat náklady na Azure API Management. Nejprve pomocí cenové kalkulačky Azure pomůže naplánovat náklady na API Management před tím, než přidáte jakékoli prostředky pro službu, abyste mohli odhadnout náklady. Po zahájení práce s prostředky API Management použijte funkce Cost Management k nastavení rozpočtů a monitorování nákladů. Můžete si také projít předpokládané náklady a identifikovat trendy útraty, které identifikují oblasti, kde můžete chtít pracovat. 

Náklady na API Management jsou jenom částí měsíčních nákladů na faktuře Azure. I když tento článek vysvětluje, jak naplánovat a spravovat náklady na API Management, účtují se vám všechny služby a prostředky Azure, které se používají ve vašem předplatném Azure, včetně služeb třetích stran.

## <a name="prerequisites"></a>Předpoklady

Analýza nákladů v Cost Management podporuje většinu typů účtů Azure, ale ne všechny. Úplný seznam podporovaných typů účtů si můžete prohlédnout v článku [Vysvětlení dat služby Cost Management](../cost-management-billing/costs/understand-cost-mgt-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn). K zobrazení dat o nákladech potřebujete alespoň oprávnění ke čtení pro účet Azure. Informace o přiřazování přístupu k datům služby Azure Cost Management najdete v článku [Přiřazení přístupu k datům](../cost-management-billing/costs/assign-access-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="estimate-costs-before-using-api-management"></a>Odhad nákladů před použitím API Management

Pomocí [cenové kalkulačky Azure](https://azure.microsoft.com/pricing/calculator/) můžete odhadnout náklady předtím, než přidáte API Management. 

1. Vyhledejte *API Management* nebo vyberte možnost **integrace**  >  **API Management**.
1. Vyberte **zobrazení** a přidejte výchozí odhad nákladů pro instanci služby API Management.

> [!NOTE]
> Náklady zobrazené v tomto příkladu jsou pouze pro demonstrační účely. Nejnovější informace o cenách najdete v tématu [API Management ceny](https://azure.microsoft.com/pricing/details/api-management/) .

:::image type="content" source="media/plan-manage-costs/pricing-calculator-developer-tier.png" alt-text="Odhadované náklady na vrstvu pro vývojáře":::

* Výchozí odhad nákladů vychází z instance služby API Management ve [vrstvě služeb](api-management-features.md) pro **vývojáře** s 1 [kapacitní jednotkou](api-management-capacity.md). Úroveň pro vývojáře je určena pro případy neprodukčního použití a hodnocení. Neplatí smlouvou o úrovni služeb.

* Chcete-li odhadnout náklady na další jednotky kapacity nebo jinou úroveň služby, vyberte v rozevíracích seznamech **jednotky** a **úroveň** jiné možnosti.

* V závislosti na dostupnosti funkcí a úrovni služby se můžou další poplatky použít pro použití [bran](self-hosted-gateway-overview.md)v místním prostředí.

Další informace o cenách a funkcích najdete v těchto tématech:

* [Ceny API Management](https://azure.microsoft.com/pricing/details/api-management/)
* [Porovnání na základě funkcí API Management vrstev Azure](api-management-features.md)

### <a name="using-monetary-credit-with-api-management"></a>Použití peněžního kreditu s API Management

Můžete platit za API Management poplatky s kreditem peněžního závazku EA. Kredity peněžních závazků EA ale nemůžete použít k placení poplatků za produkty a služby třetích stran, včetně těch, které jsou Azure Marketplace.

## <a name="monitor-costs"></a>Sledovat náklady

Při používání prostředků Azure s API Management se vám účtují náklady. Náklady na jednotku využívání prostředků Azure se liší podle časových intervalů (sekundy, minuty, hodiny a dny) nebo podle využití jednotek (bajty, megabajtů atd.). Jakmile začnete API Management používání, účtují se náklady a náklady se vám budou zobrazovat v [analýze nákladů](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

Když použijete analýzu nákladů, zobrazí se API Management náklady v grafech a tabulkách pro různé časové intervaly. Některé příklady jsou podle dne, aktuálního a předchozího měsíce a roku. Také se zobrazí náklady na rozpočty a předpokládané náklady. Přepínání na delší zobrazení v průběhu času vám může pomáhat identifikovat trendy útraty. A vidíte, kde mohlo dojít k předanému nadměrnému útratu. Pokud jste vytvořili rozpočty, můžete také snadno zjistit, kde jsou překročeny.

> [!NOTE]
> Náklady zobrazené v tomto příkladu jsou pouze pro demonstrační účely. Vaše náklady se budou lišit v závislosti na využití prostředků a aktuálních cenách.

Postup zobrazení API Managementch nákladů při analýze nákladů:

1. Přihlaste se na [Azure Portal](https://azure.microsoft.com).
1. Otevřete okno **cost management + fakturace** , v nabídce vyberte možnost **Správa nákladů** a pak vyberte **obor fakturace**. Vyberte například předplatné ze seznamu.
1. V nabídce vyberte **cost management** a pak vyberte **Analýza nákladů**.
1. Ve výchozím nastavení se v prvním prstenovém grafu zobrazí měsíční náklady na všechny služby. 

    :::image type="content" source="media/plan-manage-costs/api-management-cost-analysis.png" alt-text="Měsíční náklady na předplatné":::

1. Chcete-li zúžit náklady na jednu službu, například API Management, vyberte možnost **Přidat filtr** a potom vyberte možnost **název služby**. Pak vyberte **API Management**.

    :::image type="content" source="media/plan-manage-costs/api-management-apim-cost-analysis.png" alt-text="Příklad znázorňující nahromaděné náklady na API Management":::

V předchozím příkladu vidíte aktuální náklady za službu. Zobrazují se také náklady podle oblastí Azure (umístění) a API Managementch nákladů podle skupiny prostředků. Z tohoto místa můžete sami prozkoumat náklady.

## <a name="create-budgets"></a>Tvorba rozpočtů

Pro účely řízení nákladů můžete vytvořit [rozpočty](../cost-management-billing/costs/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) a nastavit [upozornění](../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn), která účastníky automaticky upozorňují na anomálie a nebezpečí nadměrných výdajů. Upozornění jsou založena na porovnání útraty s rozpočtem a prahovými hodnotami nákladů. Rozpočty a výstrahy se vytvářejí pro předplatná Azure a skupiny prostředků, takže jsou užitečné v rámci celkové strategie monitorování nákladů. 

Rozpočty je možné vytvořit s filtry pro konkrétní prostředky nebo služby v Azure, pokud chcete mít v monitorování k dispozici větší členitost. Filtry vám pomůžou zajistit, aby nedošlo k náhodnému vytváření nových prostředků s náklady na další peníze. Další informace o možnostech filtru při vytváření rozpočtu najdete v tématu [Možnosti skupiny a filtru](../cost-management-billing/costs/group-filter.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="export-cost-data"></a>Export dat nákladů

[Data nákladů](../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) můžete také exportovat do účtu úložiště. To je užitečné v případě, že potřebujete další analýzu dat pro náklady. Finanční tým může například analyzovat data pomocí aplikace Excel nebo Power BI. Náklady můžete exportovat na denní, týdenní nebo měsíční plán a nastavit vlastní rozsah kalendářních dat. Export nákladových dat je doporučený způsob, jak načíst datové sady nákladů.

## <a name="other-ways-to-manage-and-reduce-costs-for-api-management"></a>Další způsoby správy a snížení nákladů na API Management

### <a name="choose-tier"></a>Zvolit vrstvu

Přečtěte si [porovnání na základě funkcí na úrovních API Management Azure](api-management-features.md) , které vám pomůžou určit, která úroveň služby může být vhodná pro vaše scénáře. Různé úrovně služeb podporují kombinace funkcí a možností navržených pro různé případy použití s různými náklady. [Upgradujte](upgrade-and-scale.md) na jinou úroveň služby kdykoli.

* Úroveň služby **spotřeby** nabízí odlehčenou možnost bez serveru, která nepřináší žádné pevné náklady. Účtují se vám na základě počtu volání rozhraní API ke službě nad určitou prahovou hodnotou. Kapacita se také automaticky škáluje na základě zatížení služby.
* Další API Management úrovně přináší měsíční náklady a poskytují větší propustnost a bohatší sady funkcí pro vyhodnocení a produkční úlohy.

### <a name="scale-using-capacity-units"></a>Škálování pomocí kapacitních jednotek

S výjimkou úrovně služby spotřeby API Management podporuje škálování přidáním nebo odebráním [*kapacitních jednotek*](api-management-capacity.md). Po zvýšení zátěže na instanci API Management může být přidání kapacitních jednotek výhodnější než upgrade na vyšší úroveň služby. Maximální počet jednotek závisí na úrovni služby.

Každá jednotka kapacity má určitou schopnost zpracování požadavků, která závisí na úrovni služby. Například jednotka úrovně Basic má odhadovanou maximální propustnost přibližně 1 000 požadavků za sekundu. 

Při přidávání nebo odebírání jednotek, kapacity a nákladů se poměrně mění. Například dvě jednotky úrovně Standard poskytují odhadovanou propustnost přibližně 2 000 požadavků za sekundu. Skutečná propustnost se může lišit v důsledku velikosti požadavků nebo odpovědí, způsobů připojení, počtu klientů, které provádějí požadavky, a dalších faktorů.

[Sledujte](api-management-howto-use-azure-monitor.md) metriku kapacity pro vaši instanci API Management, abyste se mohli rozhodnout, jestli chcete škálovat nebo upgradovat instanci API Management tak, aby vyhovovala většímu zatížení.

## <a name="next-steps"></a>Další kroky

- Naučte se [optimalizovat investice do cloudu pomocí Azure cost management](../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Přečtěte si další informace o správě nákladů pomocí [analýzy nákladů](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Přečtěte si, jak se [vyhnout neočekávaným nákladům](../cost-management-billing/manage/getting-started.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Využijte kurz učení s asistencí [cost management](https://docs.microsoft.com/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) .
- Přečtěte si o [kapacitě](api-management-capacity.md)API Management.
- Přečtěte si postup, jak škálovat a upgradovat API Management pomocí [Azure Portal](upgrade-and-scale.md)a další informace o automatickém [škálování](api-management-howto-autoscale.md).