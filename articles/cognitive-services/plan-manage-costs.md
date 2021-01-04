---
title: Plánování správy nákladů pro Azure Cognitive Services
description: Naučte se plánovat a spravovat náklady na Azure Cognitive Services pomocí analýzy nákladů v Azure Portal.
author: erhopf
ms.author: erhopf
ms.custom: subject-cost-optimization
ms.service: cognitive-services
ms.topic: how-to
ms.date: 12/15/2020
ms.openlocfilehash: 48e111ac8b2b4fd3c1e2fee568e20699896dfff3
ms.sourcegitcommit: e7152996ee917505c7aba707d214b2b520348302
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/20/2020
ms.locfileid: "97705771"
---
# <a name="plan-and-manage-costs-for-azure-cognitive-services"></a>Plánování a Správa nákladů pro Azure Cognitive Services

Tento článek popisuje, jak plánujete a spravovat náklady na Azure Cognitive Services. Nejprve pomocí cenové kalkulačky Azure pomůže naplánovat náklady na Cognitive Services před tím, než přidáte jakékoli prostředky pro službu, abyste mohli odhadnout náklady. V dalším kroku můžete při přidávání prostředků Azure zkontrolovat odhadované náklady. Po zahájení práce s prostředky Cognitive Services (například řeč, Počítačové zpracování obrazu, LUIS, Analýza textu, překladatel atd.) pomocí Cost Management funkcí nastavte rozpočty a sledujte náklady. Můžete si také projít předpokládané náklady a identifikovat trendy útraty, které identifikují oblasti, kde můžete chtít pracovat. Náklady na Cognitive Services jsou jenom částí měsíčních nákladů na faktuře Azure. I když tento článek vysvětluje, jak naplánovat a spravovat náklady na Cognitive Services, účtují se vám všechny služby a prostředky Azure, které se používají ve vašem předplatném Azure, včetně služeb třetích stran.

## <a name="prerequisites"></a>Požadavky

Analýza nákladů v Cost Management podporuje většinu typů účtů Azure, ale ne všechny. Úplný seznam podporovaných typů účtů si můžete prohlédnout v článku [Vysvětlení dat služby Cost Management](https://docs.microsoft.com/azure/cost-management-billing/costs/understand-cost-mgt-data?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn). K zobrazení dat o nákladech potřebujete alespoň oprávnění ke čtení pro účet Azure. Informace o přiřazování přístupu k datům služby Azure Cost Management najdete v článku [Přiřazení přístupu k datům](https://docs.microsoft.com/azure/cost-management/assign-access-acm-data?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

<!--Note for Azure service writer: If you have other prerequisites for your service, insert them here -->

## <a name="estimate-costs-before-using-cognitive-services"></a>Odhad nákladů před použitím Cognitive Services

Pomocí [cenové kalkulačky Azure](https://azure.microsoft.com/pricing/calculator/) můžete odhadnout náklady předtím, než přidáte Cognitive Services.

:::image type="content" source="media/cognitive-services-pricing-calculator.png" alt-text="Cenová Kalkulačka Azure pro Cognitive Services" border="true":::

Když do svého pracovního prostoru přidáte nové prostředky, vraťte se do této kalkulačky a přidejte do něj stejný prostředek, abyste mohli aktualizovat odhadované náklady.

Další informace najdete v tématu [ceny služby Azure Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/).

## <a name="understand-the-full-billing-model-for-cognitive-services"></a>Princip úplného fakturačního modelu pro Cognitive Services

Cognitive Services běží na infrastruktuře Azure, která při nasazení nového prostředku [narůstá náklady](https://azure.microsoft.com/pricing/details/cognitive-services/) . Je důležité pochopit, že další infrastruktura by mohla navýšit náklady. Pokud provedete změny nasazených prostředků, musíte tyto náklady spravovat. 

### <a name="costs-that-typically-accrue-with-cognitive-services"></a>Náklady, které obvykle nabíhají Cognitive Services

Po nasazení prostředku Azure se náklady určují podle cenové úrovně a volání rozhraní API, která provedete u svého koncového bodu. Pokud má služba, kterou používáte, úroveň závazku, může se přenášet poplatky za nadlimitní volání ve vaší úrovni.

Dodatečné náklady se můžou při používání těchto služeb časově snížit:

#### <a name="qna-maker"></a>QnA Maker

Když vytváříte prostředky pro QnA Maker, můžou se taky vytvářet prostředky pro jiné služby Azure. Mezi ně patří:

- [Azure App Service (pro modul runtime)](https://azure.microsoft.com/pricing/details/app-service/)
- [Kognitivní hledání Azure (pro data)](https://azure.microsoft.com/pricing/details/search/)
 
### <a name="costs-that-might-accrue-after-resource-deletion"></a>Náklady, které se můžou po odstranění prostředku časově snížit

#### <a name="qna-maker"></a>QnA Maker

Po odstranění prostředků QnA Maker můžou dál existovat následující prostředky. Nadále se účtují náklady, dokud je neodstraníte.

- [Azure App Service (pro modul runtime)](https://azure.microsoft.com/pricing/details/app-service/)
- [Kognitivní hledání Azure (pro data)](https://azure.microsoft.com/pricing/details/search/)

### <a name="using-monetary-credit-with-cognitive-services"></a>Použití peněžního kreditu s Cognitive Services

Můžete platit za Cognitive Services poplatky s kreditem peněžního závazku EA. Nemůžete ale použít kredit peněžních závazků EA na platby za poplatky za produkty a služby třetích stran, včetně těch, které jsou Azure Marketplace.

## <a name="create-budgets"></a>Tvorba rozpočtů

Pro účely řízení nákladů můžete vytvořit [rozpočty](https://docs.microsoft.com/azure/cost-management/tutorial-acm-create-budgets?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) a nastavit [upozornění](https://docs.microsoft.com/azure/cost-management/cost-mgt-alerts-monitor-usage-spending?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn), která účastníky automaticky upozorňují na anomálie a nebezpečí nadměrných výdajů. Upozornění jsou založena na porovnání útraty s rozpočtem a prahovými hodnotami nákladů. Rozpočty a výstrahy se vytvářejí pro předplatná Azure a skupiny prostředků, takže jsou užitečné v rámci celkové strategie monitorování nákladů. 

Rozpočty je možné vytvořit s filtry pro konkrétní prostředky nebo služby v Azure, pokud chcete mít v monitorování k dispozici větší členitost. Filtry vám pomůžou zajistit, aby nedošlo k náhodnému vytváření nových prostředků s náklady na další peníze. Další informace o možnostech filtru při vytváření rozpočtu najdete v tématu [Možnosti skupiny a filtru](https://docs.microsoft.com/azure/cost-management-billing/costs/group-filter?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="export-cost-data"></a>Export dat nákladů

[Data nákladů](https://docs.microsoft.com/azure/cost-management-billing/costs/tutorial-export-acm-data?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) můžete také exportovat do účtu úložiště. To je užitečné v případě, že potřebujete nebo jiné provádět analýzu dat pro náklady. Například finanční týmy mohou analyzovat data pomocí aplikace Excel nebo Power BI. Náklady můžete exportovat na denní, týdenní nebo měsíční plán a nastavit vlastní rozsah kalendářních dat. Export nákladových dat je doporučený způsob, jak načíst datové sady nákladů.

<!--
## Other ways to manage and reduce costs for Cognitive Services

Work with Dean to complete this section in 2021.

-->

## <a name="next-steps"></a>Další kroky

- Naučte se [optimalizovat investice do cloudu pomocí Azure cost management](https://docs.microsoft.com/azure/cost-management-billing/costs/cost-mgt-best-practices?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Přečtěte si další informace o správě nákladů pomocí [analýzy nákladů](https://docs.microsoft.com/azure/cost-management-billing/costs/quick-acm-cost-analysis?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Přečtěte si, jak se [vyhnout neočekávaným nákladům](https://docs.microsoft.com/azure/cost-management-billing/manage/getting-started?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Využijte kurz učení s asistencí [cost management](https://docs.microsoft.com/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) .
