---
title: Plánování správy nákladů pro Azure Cognitive Services
description: Naučte se plánovat a spravovat náklady na Azure Cognitive Services pomocí analýzy nákladů v Azure Portal.
author: erhopf
ms.author: erhopf
ms.custom: subject-cost-optimization
ms.service: cognitive-services
ms.topic: how-to
ms.date: 12/15/2020
ms.openlocfilehash: db99fa5caff27a24aa04e4780b25ade3f7c25496
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "101699925"
---
# <a name="plan-and-manage-costs-for-azure-cognitive-services"></a>Plánování a Správa nákladů pro Azure Cognitive Services

Tento článek popisuje, jak plánujete a spravovat náklady na Azure Cognitive Services. Nejprve pomocí cenové kalkulačky Azure pomůže naplánovat náklady na Cognitive Services před tím, než přidáte jakékoli prostředky pro službu, abyste mohli odhadnout náklady. V dalším kroku můžete při přidávání prostředků Azure zkontrolovat odhadované náklady. Po zahájení práce s prostředky Cognitive Services (například řeč, Počítačové zpracování obrazu, LUIS, Analýza textu, překladatel atd.) pomocí Cost Management funkcí nastavte rozpočty a sledujte náklady. Můžete si také projít předpokládané náklady a identifikovat trendy útraty, které identifikují oblasti, kde můžete chtít pracovat. Náklady na Cognitive Services jsou jenom částí měsíčních nákladů na faktuře Azure. I když tento článek vysvětluje, jak naplánovat a spravovat náklady na Cognitive Services, účtují se vám všechny služby a prostředky Azure, které se používají ve vašem předplatném Azure, včetně služeb třetích stran.

## <a name="prerequisites"></a>Požadavky

Analýza nákladů v Cost Management podporuje většinu typů účtů Azure, ale ne všechny. Úplný seznam podporovaných typů účtů si můžete prohlédnout v článku [Vysvětlení dat služby Cost Management](../cost-management-billing/costs/understand-cost-mgt-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn). K zobrazení dat o nákladech potřebujete alespoň oprávnění ke čtení pro účet Azure. Informace o přiřazování přístupu k datům služby Azure Cost Management najdete v článku [Přiřazení přístupu k datům](../cost-management-billing/costs/assign-access-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

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

### <a name="using-azure-prepayment-credit-with-cognitive-services"></a>Použití předplacených kreditů Azure s Cognitive Services

Za poplatky za Cognitive Services můžete platit pomocí kreditu služby Azure (dříve nazývaného peněžního závazku). Nemůžete však použít kredit plateb Azure k placení poplatků za produkty a služby třetích stran, včetně těch, které jsou z Azure Marketplace.

## <a name="monitor-costs"></a>Sledovat náklady

<!-- Note to Azure service writer: Modify the following as needed for your service. Replace example screenshots with ones taken for your service. If you need assistance capturing screenshots, ask banders for help. -->

Při používání prostředků Azure s Cognitive Services se vám účtují náklady. Náklady na jednotku využívání prostředků Azure se liší podle časových intervalů (sekundy, minuty, hodiny a dny) nebo podle využití jednotek (bajty, megabajtů atd.). Jakmile se začne používat Služba rozpoznávání (nebo Cognitive Services), účtují se náklady a náklady se zobrazí v [analýze nákladů](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

Když použijete analýzu nákladů, zobrazí se Cognitive Services náklady v grafech a tabulkách pro různé časové intervaly. Některé příklady jsou podle dne, aktuálního a předchozího měsíce a roku. Také se zobrazí náklady na rozpočty a předpokládané náklady. Přepínání na delší zobrazení v průběhu času vám může pomáhat identifikovat trendy útraty. A vidíte, kde mohlo dojít k předanému nadměrnému útratu. Pokud jste vytvořili rozpočty, můžete také snadno zjistit, kde jsou překročeny.

Postup zobrazení Cognitive Servicesch nákladů při analýze nákladů:

1. Přihlaste se k webu Azure Portal.
2. Otevřete obor v Azure Portal a v nabídce vyberte **Analýza nákladů** . Například přejděte na **odběry**, v seznamu vyberte předplatné a v nabídce vyberte  **Analýza nákladů** . Vyberte **obor** , který se má při analýze nákladů přepnout na jiný obor.
3. Ve výchozím nastavení se náklady na služby zobrazují v prvním prstenovém grafu. Vyberte oblast v grafu označené Cognitive Services.

Skutečné měsíční náklady se zobrazí při počátečním otevření analýzy nákladů. Tady je příklad zobrazující všechny měsíční náklady na používání.

:::image type="content" source="./media/cost-management/all-costs.png" alt-text="Příklad znázorňující nahromaděné náklady na předplatné":::

- Chcete-li zúžit náklady na jednu službu, například Cognitive Services, vyberte možnost **Přidat filtr** a potom vyberte možnost **název služby**. Pak vyberte **Cognitive Services**.

Tady je příklad, který ukazuje náklady jenom na Cognitive Services.

:::image type="content" source="./media/cost-management/cognitive-services-costs.png" alt-text="Příklad znázorňující nahromaděné náklady na Cognitive Services":::

V předchozím příkladu vidíte aktuální náklady za službu. Zobrazují se také náklady podle oblastí Azure (umístění) a Cognitive Servicesch nákladů podle skupiny prostředků. Z tohoto místa můžete sami prozkoumat náklady.

## <a name="create-budgets"></a>Tvorba rozpočtů

Pro účely řízení nákladů můžete vytvořit [rozpočty](../cost-management-billing/costs/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) a nastavit [upozornění](../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn), která účastníky automaticky upozorňují na anomálie a nebezpečí nadměrných výdajů. Upozornění jsou založena na porovnání útraty s rozpočtem a prahovými hodnotami nákladů. Rozpočty a výstrahy se vytvářejí pro předplatná Azure a skupiny prostředků, takže jsou užitečné v rámci celkové strategie monitorování nákladů. 

Rozpočty je možné vytvořit s filtry pro konkrétní prostředky nebo služby v Azure, pokud chcete mít v monitorování k dispozici větší členitost. Filtry vám pomůžou zajistit, aby nedošlo k náhodnému vytváření nových prostředků s náklady na další peníze. Další informace o možnostech filtru při vytváření rozpočtu najdete v tématu [Možnosti skupiny a filtru](../cost-management-billing/costs/group-filter.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="export-cost-data"></a>Export dat nákladů

[Data nákladů](../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) můžete také exportovat do účtu úložiště. To je užitečné v případě, že potřebujete nebo jiné provádět analýzu dat pro náklady. Například finanční týmy mohou analyzovat data pomocí aplikace Excel nebo Power BI. Náklady můžete exportovat na denní, týdenní nebo měsíční plán a nastavit vlastní rozsah kalendářních dat. Export nákladových dat je doporučený způsob, jak načíst datové sady nákladů.

## <a name="next-steps"></a>Další kroky

- Naučte se [optimalizovat investice do cloudu pomocí Azure cost management](../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Přečtěte si další informace o správě nákladů pomocí [analýzy nákladů](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Přečtěte si, jak se [vyhnout neočekávaným nákladům](../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Využijte kurz učení s asistencí [cost management](/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) .