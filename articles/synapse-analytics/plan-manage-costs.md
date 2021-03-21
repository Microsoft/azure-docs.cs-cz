---
title: Plánování správy nákladů pro Azure synapse Analytics
description: Naučte se plánovat a spravovat náklady na Azure synapse Analytics pomocí analýzy nákladů v Azure Portal.
author: julieMSFT
ms.author: jrasnick
ms.custom: subject-cost-optimization
ms.service: synapse-analytics
ms.topic: how-to
ms.date: 12/09/2020
ms.openlocfilehash: 06586b5bf20619f57b2ad1c3d5de84dd61952261
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102561241"
---
# <a name="plan-and-manage-costs-for-azure-synapse-analytics"></a>Plánování a Správa nákladů pro Azure synapse Analytics

Tento článek popisuje, jak naplánovat a spravovat náklady na Azure synapse Analytics. Nejprve pomocí cenové kalkulačky Azure pomůže plánovat náklady na Azure synapse ještě před tím, než přidáte prostředky pro službu, abyste mohli odhadnout náklady. V dalším kroku můžete při přidávání prostředků Azure synapse zkontrolovat odhadované náklady.

Po zahájení práce s prostředky Azure synapse použijte funkce Cost Management k nastavení rozpočtů a monitorování nákladů. Můžete si také projít předpokládané náklady a identifikovat trendy útraty, které identifikují oblasti, kde můžete chtít pracovat. Náklady na Azure synapse jsou jenom částí měsíčních nákladů na faktuře Azure. I když tento článek vysvětluje, jak naplánovat a spravovat náklady na Azure synapse, budou se vám účtovat všechny služby a prostředky Azure, které se používají ve vašem předplatném Azure, včetně služeb třetích stran.

## <a name="prerequisites"></a>Předpoklady

Analýza nákladů v Cost Management podporuje většinu typů účtů Azure, ale ne všechny. Úplný seznam podporovaných typů účtů si můžete prohlédnout v článku [Vysvětlení dat služby Cost Management](../cost-management-billing/costs/understand-cost-mgt-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn). K zobrazení dat o nákladech potřebujete alespoň oprávnění ke čtení pro účet Azure. Informace o přiřazování přístupu k datům služby Azure Cost Management najdete v článku [Přiřazení přístupu k datům](../cost-management-billing/costs/assign-access-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="estimate-costs-before-using-azure-synapse-analytics"></a>Odhad nákladů před použitím Azure synapse Analytics

Pomocí [cenové kalkulačky Azure](https://azure.microsoft.com/pricing/calculator/) můžete odhadnout náklady předtím, než přidáte Azure synapse Analytics.

Azure synapse má různé prostředky, které mají různé poplatky, jak je vidět níže v odhadu nákladů. 

![Příklad znázorňující odhadované náklady v cenové kalkulačkě Azure](./media/plan-manage-costs/cost-estimate.png)

## <a name="understand-the-full-billing-model-for-azure-synapse-analytics"></a>Princip úplného fakturačního modelu pro Azure synapse Analytics

Azure synapse běží na infrastruktuře Azure, která při nasazení nového prostředku nasazuje náklady společně s Azure synapse. Je důležité pochopit, že další infrastruktura by mohla navýšit náklady. Pokud provedete změny nasazených prostředků, musíte tyto náklady spravovat. 

### <a name="costs-that-typically-accrue-with-azure-synapse-analytics"></a>Náklady, které obvykle nabíhají s Azure synapse Analytics

Při vytváření prostředků pro Azure synapse se také vytvoří prostředky pro jiné služby Azure. Mezi ně patří:

- Data Lake Storage Gen2

 ### <a name="costs-might-accrue-after-resource-deletion"></a>Náklady se můžou po odstranění prostředku časově rozlišit.

Po odstranění prostředků Azure synapse můžou dál existovat následující prostředky. Nadále se účtují náklady, dokud je neodstraníte.

- Data Lake Storage Gen2

### <a name="using-azure-prepayment-credit-with-azure-synapse"></a>Používání předplacených kreditů Azure s Azure synapse 

Za poplatky za Azure synapse můžete platit pomocí kreditu za předplacenou službu Azure (dříve označovaný jako peněžní závazek). Nemůžete však použít kredit plateb Azure k placení poplatků za produkty a služby třetích stran, včetně těch, které jsou z Azure Marketplace.

## <a name="review-estimated-costs-in-the-azure-portal"></a>Zjištění odhadovaných nákladů na webu Azure Portal

Při vytváření prostředků pro Azure synapse Analytics se zobrazí odhadované náklady. Pracovní prostor má fond SQL bez serveru vytvořený s pracovním prostorem. Fond SQL bez serveru se vám nebude účtovat, dokud nespustíte dotazy. V pracovním prostoru se musí vytvořit jiné prostředky, jako jsou vyhrazené fondy SQL a fondy Apache Spark bez serveru.

Postup vytvoření <ResourceName> a zobrazení odhadované ceny:

1. Přejděte ke službě v Azure Portal.
2. Vytvořte prostředek.
3. Přečtěte si očekávanou cenu zobrazenou v souhrnu.
4. Dokončete vytváření prostředku.

![Příklad znázorňující odhadované náklady při vytváření prostředku](./media/plan-manage-costs/create-workspace-cost.png)


Pokud má vaše předplatné Azure limit útraty, Azure vám zabrání v útratě za vaši kreditní částku. Při vytváření a používání prostředků Azure se vaše kredity používají. Po dosažení limitu kreditu budou prostředky, které jste nasadili, zakázané pro zbytek tohoto fakturačního období. Limit kreditu nemůžete změnit, ale můžete ho odebrat. Další informace o limitech útraty najdete v tématu [limit útraty Azure](../cost-management-billing/manage/spending-limit.md).

## <a name="monitor-costs"></a>Sledovat náklady

Při používání prostředků Azure synapse se účtují náklady. Náklady na jednotku využívání prostředků Azure se liší podle časových intervalů (sekundy, minuty, hodiny a dny) nebo podle využití jednotek (bajty, megabajtů atd.). Jakmile začnete využívat prostředky v Azure synapse, účtují se náklady a náklady se zobrazí v [analýze nákladů](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

Když použijete analýzu nákladů, zobrazí se vám náklady na Azure synapse Analytics v grafech a tabulkách pro různé časové intervaly. Některé příklady jsou podle dne, aktuálního a předchozího měsíce a roku. Také se zobrazí náklady na rozpočty a předpokládané náklady. Přepínání na delší zobrazení v průběhu času vám může pomáhat identifikovat trendy útraty. A vidíte, kde mohlo dojít k předanému nadměrnému útratu. Pokud jste vytvořili rozpočty, můžete také snadno zjistit, kde jsou překročeny.

Zobrazení nákladů na Azure synapse při analýze nákladů:

1. Přihlaste se k webu Azure Portal.
2. Otevřete obor, buď předplatné nebo skupinu prostředků, v Azure Portal a v nabídce vyberte **Analýza nákladů** . Například přejděte na **odběry**, v seznamu vyberte předplatné a v nabídce vyberte  **Analýza nákladů** . Vyberte **obor** , který se má při analýze nákladů přepnout na jiný obor.
3. Ve výchozím nastavení se náklady na služby zobrazují v prvním prstenovém grafu. Vyberte oblast v grafu s názvem Azure synapse.

Skutečné měsíční náklady se zobrazí při počátečním otevření analýzy nákladů. Tady je příklad zobrazující všechny měsíční náklady na používání.

![Příklad znázorňující nahromaděné náklady na předplatné](./media/plan-manage-costs/actual-monthly-costs.png)

- Pokud chcete zúžit náklady na jednu službu, jako je Azure synapse, vyberte **Přidat filtr** a pak vyberte **název služby**. Pak vyberte **Azure synapse Analytics**.

Tady je příklad, který ukazuje náklady jenom na Azure synapse.

![Příklad znázorňující akumulované náklady pro ServiceName](./media/plan-manage-costs/filtered-monthly-costs.png)

V předchozím příkladu vidíte aktuální náklady za službu. Zobrazují se také náklady podle oblastí Azure (umístění) a ceny Azure synapse podle skupiny prostředků. Z tohoto místa můžete sami prozkoumat náklady.

## <a name="create-budgets"></a>Tvorba rozpočtů

Pro účely řízení nákladů můžete vytvořit [rozpočty](../cost-management-billing/costs/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) a nastavit [upozornění](../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn), která účastníky automaticky upozorňují na anomálie a nebezpečí nadměrných výdajů. Upozornění jsou založena na porovnání útraty s rozpočtem a prahovými hodnotami nákladů. Rozpočty a výstrahy se vytvářejí pro předplatná Azure a skupiny prostředků, takže jsou užitečné v rámci celkové strategie monitorování nákladů. 

Rozpočty je možné vytvořit s filtry pro konkrétní prostředky nebo služby v Azure, pokud chcete mít v monitorování k dispozici větší členitost. Filtry vám pomůžou zajistit, aby nedošlo k náhodnému vytváření nových prostředků s náklady na další peníze. Další informace o možnostech filtru při vytváření rozpočtu najdete v tématu [Možnosti skupiny a filtru](../cost-management-billing/costs/group-filter.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="export-cost-data"></a>Export dat nákladů

[Data nákladů](../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) můžete také exportovat do účtu úložiště. To je užitečné v případě, že potřebujete nebo jiné provádět analýzu dat pro náklady. Například finanční týmy mohou analyzovat data pomocí aplikace Excel nebo Power BI. Náklady můžete exportovat na denní, týdenní nebo měsíční plán a nastavit vlastní rozsah kalendářních dat. Export nákladových dat je doporučený způsob, jak načíst datové sady nákladů.


## <a name="other-ways-to-manage-and-reduce-costs-for-azure-synapse"></a>Další způsoby správy a snížení nákladů pro Azure synapse 

### <a name="serverless-sql-pool"></a>Bezserverový fond SQL

Další informace o nákladech pro fond SQL bez serveru najdete [v tématu cost management pro fond SQL bez serveru ve službě Azure synapse Analytics](./sql/data-processed.md) .

### <a name="dedicated-sql-pool"></a>Vyhrazený fond SQL

Můžete řídit náklady na vyhrazený fond SQL tím, že prostředek pozastavíte, když ho nepoužíváte. Pokud například nebudete databázi používat během noci a na víkendech, můžete ji během těchto časů pozastavit a obnovit během dne. Další informace najdete v tématu [pozastavení a obnovení výpočtů ve vyhrazeném fondu SQL prostřednictvím Azure Portal](./sql-data-warehouse/pause-and-resume-compute-portal.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

### <a name="serverless-apache-spark-pool"></a>Fond Apache Spark bez serveru

Pokud chcete řídit náklady na fond Apache Spark bez serveru, povolte funkci automatického pozastavení Apache Spark serveru a nastavte svůj časový limit odpovídajícím způsobem.  Pokud pro vývoj používáte synapse Studio, aplikace Studio pošle zprávu Keep Alive, aby zachovala aktivní relaci, která je také konfigurovatelná, takže nastavte pro automatické pozastavení hodnotu krátkého časového limitu.  Až skončíte, zavřete relaci a fond Apache Spark se po dosažení hodnoty časového limitu automaticky zastaví.
 
Během vývoje vytvořte více Apache Sparkch definicí fondů různých velikostí.  Vytváření definic Apache Spark fondů je bezplatné a účtují se vám jenom poplatky za využití.  Využití Apache Spark ve službě Azure synapse se účtuje za vCore hodinu a poměrná po minutě.  Například používejte malé fondy pro vývoj kódu a ověřování při použití větších velikostí fondů pro testování výkonu.


### <a name="data-integration---pipelines-and-data-flows"></a>Integrace dat – kanály a toky dat 

Další informace o cenách při integraci dat najdete v tématu [plánování a Správa nákladů pro Azure Data Factory](../data-factory/plan-manage-costs.md)

## <a name="next-steps"></a>Další kroky

- Naučte se [optimalizovat investice do cloudu pomocí Azure cost management](../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Přečtěte si další informace o správě nákladů pomocí [analýzy nákladů](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Přečtěte si, jak se [vyhnout neočekávaným nákladům](../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Využijte kurz učení s asistencí [cost management](/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) .
- Seznamte se s plánováním a správou nákladů na [Azure Machine Learning](../machine-learning/concept-plan-manage-cost.md)