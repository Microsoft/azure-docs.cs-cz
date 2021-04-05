---
title: Plánování a Správa nákladů na Azure SQL Database
description: Naučte se plánovat a spravovat náklady na Azure SQL Database pomocí analýzy nákladů v Azure Portal.
author: stevestein
ms.author: sstein
ms.custom: subject-cost-optimization
ms.service: sql-database
ms.topic: how-to
ms.date: 01/15/2021
ms.openlocfilehash: 56cf30d89460df8ac50d258bd8b29cf4e7236690
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98734626"
---
# <a name="plan-and-manage-costs-for-azure-sql-database"></a>Plánování a Správa nákladů na Azure SQL Database

Tento článek popisuje, jak plánujete a spravovat náklady na Azure SQL Database. Nejprve pomocí cenové kalkulačky Azure přidejte prostředky Azure a Prohlédněte si odhadované náklady. Po zahájení práce s prostředky Azure SQL Database použijte funkce Cost Management k nastavení rozpočtů a monitorování nákladů. Můžete si také projít předpokládané náklady a identifikovat trendy útraty, které identifikují oblasti, kde můžete chtít pracovat. Náklady na Azure SQL Database jsou jenom částí měsíčních nákladů na faktuře Azure. I když tento článek vysvětluje, jak naplánovat a spravovat náklady na Azure SQL Database, účtují se vám všechny služby a prostředky Azure, které se používají ve vašem předplatném Azure, včetně všech služeb třetích stran.


## <a name="prerequisites"></a>Požadavky

Analýza nákladů podporuje většinu typů účtů Azure, ale ne všechny. Úplný seznam podporovaných typů účtů si můžete prohlédnout v článku [Vysvětlení dat služby Cost Management](../../cost-management-billing/costs/understand-cost-mgt-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn). K zobrazení dat o nákladech potřebujete alespoň oprávnění ke čtení pro účet Azure. 

Informace o přiřazování přístupu k datům služby Azure Cost Management najdete v článku [Přiřazení přístupu k datům](../../cost-management-billing/costs/assign-access-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).


## <a name="sql-database-initial-cost-considerations"></a>SQL Database předpoklady počátečních nákladů

Při práci s Azure SQL Database existuje několik funkcí šetřících náklady, které je potřeba vzít v úvahu:


### <a name="vcore-or-dtu-purchasing-models"></a>modely nákupu vCore nebo DTU 

Azure SQL Database podporuje dva nákupní modely: vCore a DTU. Způsob, jakým se vám bude účtovat, se liší mezi nákupními modely, takže je důležité pochopit model, který nejlépe vyhovuje vašim úlohám při plánování a zvažování nákladů. Informace o nákupních modelech vCore a DTU najdete v tématu [Volba mezi nákupními modely Vcore a DTU](purchasing-models.md).


### <a name="provisioned-or-serverless"></a>Zřízené nebo bez serveru

V Azure SQL Database modelu nákupu vCore také podporuje dva typy výpočetních úrovní: zřízená propustnost a bez serveru. Způsob, jakým se vám budou účtovat jednotlivé výpočetní úrovně, se liší, takže je důležité pochopit, co nejlépe vyhovuje vašim úlohám při plánování a zvažování nákladů. Podrobnosti najdete v tématu [Přehled modelu Vcore – výpočetní úrovně](service-tiers-vcore.md#compute-tiers).

V zřízené výpočetní úrovni v rámci nákupního modelu založeném na vCore můžete vyměňovat stávající licence pro zvýhodněné sazby. Podrobnosti najdete v tématu [zvýhodněné hybridní využití Azure (AHB)](../azure-hybrid-benefit.md).

### <a name="elastic-pools"></a>Elastické fondy

Pro prostředí s více databázemi, které mají proměnlivé a nepředvídatelné požadavky na použití, můžou elastické fondy poskytovat úspory nákladů ve srovnání se zřizováním stejného množství izolovaných databází. Podrobnosti najdete v tématu [elastické fondy](elastic-pool-overview.md).

## <a name="estimate-azure-sql-database-costs"></a>Odhad nákladů na Azure SQL Database

Pomocí [cenové kalkulačky Azure](https://azure.microsoft.com/pricing/calculator/) můžete odhadnout náklady na různé konfigurace Azure SQL Database. Informace a ceny na následujícím obrázku jsou například pouze pro účely:

:::image type="content" source="media/cost-management/pricing-calc.png" alt-text="Příklad cenové kalkulačky Azure SQL Database":::

Můžete také odhadnout, jak různé možnosti zásad uchovávání informací ovlivňují náklady. Informace a ceny na následujícím obrázku jsou například pouze pro účely:

:::image type="content" source="media/cost-management/backup-storage.png" alt-text="Příklad cenové kalkulačky Azure SQL Database pro úložiště":::


## <a name="understand-the-full-billing-model-for-azure-sql-database"></a>Princip úplného fakturačního modelu pro Azure SQL Database

Azure SQL Database běží na infrastruktuře Azure, která při nasazení nového prostředku nasazuje náklady společně s Azure SQL Database. Je důležité pochopit, že další infrastruktura by mohla navýšit náklady. Pokud provedete změny nasazených prostředků, musíte tyto náklady spravovat. 


Azure SQL Database (s výjimkou bez serveru) se fakturuje na předvídatelné hodinové sazbě. Pokud je databáze SQL aktivní kratší dobu než 1 hodina, bude se vám účtovat každá hodina existence databáze pomocí nejvyšší vybrané úrovně služby, zřízené úložiště a v/v, které se během této hodiny použily, bez ohledu na využití nebo na to, jestli je databáze aktivní kratší dobu než hodinu.


### <a name="using-monetary-credit-with-azure-sql-database"></a>Použití peněžního kreditu s Azure SQL Database

Za poplatky za Azure SQL Database můžete platit pomocí kreditu služby Azure (dříve nazývaného peněžního závazku). Nemůžete však použít kredit plateb Azure k placení poplatků za produkty a služby třetích stran, včetně těch, které jsou z Azure Marketplace.

## <a name="review-estimated-costs-in-the-azure-portal"></a>Zjištění odhadovaných nákladů na webu Azure Portal

Při provádění procesu vytváření Azure SQL Database můžete zobrazit odhadované náklady během konfigurace výpočetní vrstvy. 

Chcete-li získat přístup k této obrazovce, vyberte na kartě **základy** na stránce **vytvořit SQL Database** možnost **Konfigurovat databázi** . Informace a ceny na následujícím obrázku jsou například pouze pro účely:

  :::image type="content" source="media/cost-management/cost-estimate.png" alt-text="Příklad znázorňující odhad nákladů v Azure Portal":::



Pokud má vaše předplatné Azure limit útraty, Azure vám zabrání v útratě za vaši kreditní částku. Při vytváření a používání prostředků Azure se vaše kredity používají. Po dosažení limitu kreditu budou prostředky, které jste nasadili, zakázané pro zbytek tohoto fakturačního období. Limit kreditu nemůžete změnit, ale můžete ho odebrat. Další informace o limitech útraty najdete v tématu [limit útraty Azure](../../cost-management-billing/manage/spending-limit.md).

## <a name="monitor-costs"></a>Sledovat náklady

Když začnete používat Azure SQL Database, můžete na portálu zobrazit odhadované náklady. Odhad nákladů můžete zkontrolovat pomocí následujících kroků:

1. Přihlaste se k Azure Portal a přejděte do skupiny prostředků Azure SQL Database. Skupinu prostředků můžete najít tak, že přejdete do své databáze a v části **Přehled** vyberete **skupinu prostředků** .
1. V nabídce vyberte **Analýza nákladů**.
1. Zobrazte **nahromaděné náklady** a nastavte graf dole na **název služby**. Tento graf znázorňuje odhad vašich aktuálních SQL Databasech nákladů. Pokud chcete Azure SQL Database zúžit náklady na celou stránku, vyberte **Přidat filtr** a potom vyberte **Azure SQL Database**. Informace a ceny na následujícím obrázku jsou například pouze pro účely:

   :::image type="content" source="media/cost-management/cost-analysis.png" alt-text="Příklad znázorňující akumulované náklady v Azure Portal":::

Z tohoto místa můžete sami prozkoumat náklady. Další informace o různých nastaveních analýzy nákladů najdete v tématu [zahájení analýzy nákladů](../../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="create-budgets"></a>Tvorba rozpočtů

<!-- Note to Azure service writer: Modify the following as needed for your service. -->

Pro účely řízení nákladů můžete vytvořit [rozpočty](../../cost-management-billing/costs/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) a nastavit [upozornění](../../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn), která účastníky automaticky upozorňují na anomálie a nebezpečí nadměrných výdajů. Upozornění jsou založena na porovnání útraty s rozpočtem a prahovými hodnotami nákladů. Rozpočty a výstrahy se vytvářejí pro předplatná Azure a skupiny prostředků, takže jsou užitečné v rámci celkové strategie monitorování nákladů. 

Rozpočty je možné vytvořit s filtry pro konkrétní prostředky nebo služby v Azure, pokud chcete mít v monitorování k dispozici větší členitost. Filtry vám pomůžou zajistit, aby nedošlo k náhodnému vytváření nových prostředků s náklady na další peníze. Další informace o možnostech filtru při vytváření rozpočtu najdete v tématu [Možnosti skupiny a filtru](../../cost-management-billing/costs/group-filter.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="export-cost-data"></a>Export dat nákladů

[Data nákladů](../../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) můžete také exportovat do účtu úložiště. To je užitečné v případě, že potřebujete nebo jiné provádět analýzu dat pro náklady. Například finanční týmy mohou analyzovat data pomocí aplikace Excel nebo Power BI. Náklady můžete exportovat na denní, týdenní nebo měsíční plán a nastavit vlastní rozsah kalendářních dat. Export nákladových dat je doporučený způsob, jak načíst datové sady nákladů.


## <a name="other-ways-to-manage-and-reduce-costs-for-azure-sql-database"></a>Další způsoby správy a snížení nákladů na Azure SQL Database

Azure SQL Database taky umožňuje škálovat prostředky nahoru nebo dolů a řídit náklady podle potřeb vaší aplikace. Podrobnosti najdete v tématu [dynamické škálování prostředků databáze](scale-resources.md).

Šetřete peníze tím, že se potvrdíte rezervací pro výpočetní prostředky po dobu jednoho až tří let. Podrobnosti najdete v tématu [úspory nákladů na prostředky s rezervovanou kapacitou](reserved-capacity-overview.md).


## <a name="next-steps"></a>Další kroky

- Naučte se [optimalizovat investice do cloudu pomocí Azure cost management](../../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Přečtěte si další informace o správě nákladů pomocí [analýzy nákladů](../../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Přečtěte si, jak se [vyhnout neočekávaným nákladům](../../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Využijte kurz učení s asistencí [cost management](/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) .