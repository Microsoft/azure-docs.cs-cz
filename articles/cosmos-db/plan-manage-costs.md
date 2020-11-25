---
title: Plánování a Správa nákladů na Azure Cosmos DB
description: Naučte se plánovat a spravovat náklady na Azure Cosmos DB pomocí analýzy nákladů v Azure Portal.
author: SnehaGunda
ms.author: sngun
ms.custom: subject-cost-optimization
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/19/2020
ms.openlocfilehash: c7db48a1df61472de8676192f267493232139b6e
ms.sourcegitcommit: 2e9643d74eb9e1357bc7c6b2bca14dbdd9faa436
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96030811"
---
# <a name="plan-and-manage-costs-for-azure-cosmos-db"></a>Plánování a Správa nákladů na Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Tento článek popisuje, jak můžete plánovat a spravovat náklady na Azure Cosmos DB. Nejprve pomocí kalkulačky kapacity Azure Cosmos DB odhadnout náklady na zatížení před vytvořením jakýchkoli prostředků. Později můžete zkontrolovat odhadované náklady a začít vytvářet prostředky.

Po zahájení práce s prostředky Azure Cosmos DB použijte funkce Cost Management k nastavení rozpočtů a monitorování nákladů. Můžete si také projít předpokládané náklady a identifikovat trendy útraty, které identifikují oblasti, kde můžete chtít pracovat. Náklady na Azure Cosmos DB jsou jenom částí měsíčních nákladů na faktuře Azure. I když tento článek vysvětluje, jak naplánovat a spravovat náklady na Azure Cosmos DB, účtují se vám všechny služby a prostředky Azure, které se používají ve vašem předplatném Azure, včetně služeb třetích stran.

## <a name="prerequisites"></a>Požadavky

### <a name="provisioned-throughput-or-serverless"></a>Zřízená propustnost nebo bez serveru

Azure Cosmos DB podporuje dva typy režimů kapacity: [zřízená propustnost](set-throughput.md) a bez [serveru](serverless.md). Způsob, jakým se vám budou účtovat poplatky za Azure Cosmos DB, se mezi těmito dvěma režimy liší, takže je důležité zvolit ten, který pro vaše zatížení funguje nejlépe. Pokyny a doporučení k tomu, jak vybrat, najdete v článku [Jak zvolit mezi zřízenou propustností a bez serveru](throughput-serverless.md) .

### <a name="cost-analysis"></a>Analýza nákladů

Analýza nákladů v Cost Management podporuje většinu typů účtů Azure, ale ne všechny. Úplný seznam podporovaných typů účtů si můžete prohlédnout v článku [Vysvětlení dat služby Cost Management](https://docs.microsoft.com/azure/cost-management-billing/costs/understand-cost-mgt-data?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn). K zobrazení dat o nákladech potřebujete alespoň oprávnění ke čtení pro účet Azure. Informace o přiřazování přístupu k datům služby Azure Cost Management najdete v článku [Přiřazení přístupu k datům](https://docs.microsoft.com/azure/cost-management/assign-access-acm-data?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="estimating-provisioned-throughput-costs-before-using-azure-cosmos-db"></a>Odhad nákladů na zajištěné propustnost před použitím Azure Cosmos DB

Pokud plánujete používat Azure Cosmos DB v režimu zřízené propustnosti, můžete pomocí [kalkulačky Azure Cosmos DB Capacity](https://cosmos.azure.com/capacitycalculator/) odhadnout náklady ještě před vytvořením prostředků v účtu Azure Cosmos. Kalkulačka kapacity slouží k získání odhadu požadované propustnosti a nákladů na vaše zatížení. Konfigurace databází a kontejnerů Azure Cosmos s využitím správného množství zřízené propustnosti nebo [jednotek žádostí (ru/s)](request-units.md)pro vaše zatížení je základem pro optimalizaci nákladů a výkonu. Musíte zadat podrobnosti, jako je typ rozhraní API, počet oblastí, velikost položky, počet žádostí o čtení a zápis za sekundu, celkovou data uložená k získání odhadu nákladů. Další informace o kalkulačkě kapacity najdete v článku [odhad](estimate-ru-with-capacity-planner.md) .

Následující snímek obrazovky ukazuje odhad propustnosti a nákladů pomocí kalkulačky kapacity:

:::image type="content" source="./media/plan-manage-costs/capacity-calculator-cost-estimate.png" alt-text="Odhad nákladů v programu Kalkulačka kapacity Azure Cosmos DB":::

## <a name="estimating-serverless-costs-before-using-azure-cosmos-db"></a><a id="estimating-serverless-costs"></a> Odhad nákladů bez serveru před použitím Azure Cosmos DB

Pokud plánujete používat Azure Cosmos DB v režimu bez serveru, musíte odhadnout, kolik [jednotek požadavků](request-units.md) a GB úložiště můžete využívat měsíčně. Vyhodnocením počtu databázových operací, které by se vystavily v měsíci, můžete odhadnout požadované množství jednotek žádostí a vynásobit jejich množství odpovídajícími náklady na RU. V následující tabulce jsou uvedeny odhadované poplatky za RU za běžné databázové operace:

| Operace | Odhadované náklady | Poznámky |
| --- | --- | --- |
| Vytvoření položky | 5 ru | Průměrné náklady na položku 1 KB s méně než 5 vlastnostmi pro index |
| Aktualizace položky | 10 RU | Průměrné náklady na položku 1 KB s méně než 5 vlastnostmi pro index |
| Číst jednotlivou položku podle jejího ID a klíče oddílu (bod-čtení) | 1 RU | Průměrné náklady za položku v 1 KB |
| Odstranění položky | 5 ru | |
| Spustit dotaz | 10 RU | Průměrné náklady na dotaz, který plně využívá [indexování](index-overview.md) a vrací 100 výsledků nebo méně |

> [!IMPORTANT] 
> Věnujte pozornost komentářům z výše uvedené tabulky. Pro přesnější odhad skutečných nákladů na vaše operace můžete použít [emulátor Azure Cosmos DB](local-emulator.md) a [změřit přesné náklady na ru za vaše operace](find-request-unit-charge.md). I když emulátor Azure Cosmos DB nepodporuje bez serveru, oznamuje za databázové operace standardní poplatek a dá se použít pro tento odhad.

Po vypočítání celkového počtu jednotek žádostí a GB úložiště, které budete pravděpodobně spotřebovávat za měsíc, vrátí následující vzorec odhad nákladů: **([počet jednotek žádosti]/1 000 000 × $0,25) + ([GB úložiště] * $0,25)**.

> [!NOTE]
> Náklady zobrazené v předchozím příkladu jsou pouze pro demonstrační účely. Nejnovější informace o cenách najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/cosmos-db/) .

## <a name="review-estimated-costs-in-the-azure-portal"></a>Zjištění odhadovaných nákladů na webu Azure Portal

Při zahájení používání prostředků Azure Cosmos DB z Azure Portal můžete zobrazit odhadované náklady. Odhad nákladů můžete zkontrolovat pomocí následujících kroků:

1. Přihlaste se k Azure Portal a přejděte k účtu Azure Cosmos.
1. Přejít na oddíl **Přehled** .
1. Podívejte se na **nákladový** graf v dolní části. Tento graf znázorňuje odhad vašich aktuálních nákladů v rámci konfigurovatelného časového období:
1. Vytvořte nový kontejner, jako je například kontejner grafu.
1. Zadejte propustnost požadovanou pro vaše zatížení, například 400 RU/s. Po zadání hodnoty propustnosti uvidíte odhad ceny, jak je znázorněno na následujícím snímku obrazovky:

   :::image type="content" source="./media/plan-manage-costs/cost-estimate-portal.png" alt-text="Odhad nákladů v Azure Portal":::

Pokud má vaše předplatné Azure limit útraty, Azure vám zabrání v útratě za vaši kreditní částku. Při vytváření a používání prostředků Azure se vaše kredity používají. Po dosažení limitu kreditu budou prostředky, které jste nasadili, zakázané pro zbytek tohoto fakturačního období. Limit kreditu nemůžete změnit, ale můžete ho odebrat. Další informace o limitech útraty najdete v tématu [limit útraty Azure](https://docs.microsoft.com/azure/billing/billing-spending-limit).

Za poplatky za Azure Cosmos DB se můžete účtovat pomocí kreditu za Azure smlouva Enterprise peněžního závazku. Kredity peněžních závazků ale nemůžete použít k placení poplatků za produkty a služby třetích stran, včetně těch z Azure Marketplace.

## <a name="monitor-costs"></a>Sledovat náklady

Při používání prostředků s Azure Cosmos DB se účtují náklady. Náklady na jednotku využívání prostředků se liší podle časových intervalů (sekundy, minuty, hodiny a dny) nebo podle využití jednotek žádosti. Jakmile se začne Azure Cosmos DB, účtují se náklady a můžete je zobrazit v podokně [Analýza nákladů](https://docs.microsoft.com/azure/cost-management/quick-acm-cost-analysis?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) v Azure Portal.

Při použití analýzy nákladů můžete zobrazit Azure Cosmos DB náklady v grafech a tabulkách v různých časových intervalech. Některé příklady jsou podle dne, aktuálního, předchozího měsíce a roku. Můžete také zobrazit náklady na rozpočty a předpokládané náklady. Přepínáním na delší zobrazení v průběhu času vám může pomáhat identifikovat trendy útraty a zjistit, kde došlo k nadměrnému útratě. Pokud jste vytvořili rozpočty, můžete také snadno zjistit, kde byly překročeny. 

Postup zobrazení Azure Cosmos DBch nákladů při analýze nákladů:

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com).

1. Otevřete obor v Azure Portal a v nabídce vyberte **Analýza nákladů** . Například přejděte na **odběry**, v seznamu vyberte předplatné a v nabídce vyberte  **Analýza nákladů** . Vyberte **obor** , který se má při analýze nákladů přepnout na jiný obor.

1. Ve výchozím nastavení jsou náklady na všechny služby zobrazeny v prvním prstenovém grafu. Vyberte oblast v grafu s názvem "Azure Cosmos DB".

1. Chcete-li zúžit náklady na jednu službu, například Azure Cosmos DB, vyberte možnost **Přidat filtr** a potom vyberte možnost **název služby**. Pak ze seznamu vyberte **Azure Cosmos DB** . Tady je příklad, který ukazuje náklady jenom pro Azure Cosmos DB:
 
   :::image type="content" source="./media/plan-manage-costs/cost-analysis-pane.png" alt-text="Sledovat náklady pomocí podokna analýza nákladů":::

V předchozím příkladu vidíte aktuální náklady za Azure Cosmos DB v měsíci v únoru. Grafy také obsahují náklady na Azure Cosmos DB podle umístění a podle skupiny prostředků.

## <a name="create-budgets"></a>Tvorba rozpočtů

Pro účely řízení nákladů můžete vytvořit [rozpočty](https://docs.microsoft.com/azure/cost-management/tutorial-acm-create-budgets?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) a nastavit [upozornění](https://docs.microsoft.com/azure/cost-management/cost-mgt-alerts-monitor-usage-spending?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn), která účastníky automaticky upozorňují na anomálie a nebezpečí nadměrných výdajů. Upozornění jsou založena na porovnání útraty s rozpočtem a prahovými hodnotami nákladů. Rozpočty a výstrahy se vytvářejí pro předplatná Azure a skupiny prostředků, takže jsou užitečné v rámci celkové strategie monitorování nákladů. 

Rozpočty je možné vytvořit s filtry pro konkrétní prostředky nebo služby v Azure, pokud chcete mít v monitorování k dispozici větší členitost. Filtry vám pomůžou zajistit, aby nedošlo k náhodnému vytváření nových prostředků s náklady na další peníze. Další informace o možnostech filtru při vytváření rozpočtu najdete v tématu [Možnosti skupiny a filtru](https://docs.microsoft.com/azure/cost-management-billing/costs/group-filter?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="export-cost-data"></a>Export dat nákladů

[Data nákladů](https://docs.microsoft.com/azure/cost-management-billing/costs/tutorial-export-acm-data?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) můžete také exportovat do účtu úložiště. To je užitečné v případě, že potřebujete nebo jiné provádět analýzu dat pro náklady. Například finanční týmy mohou analyzovat data pomocí aplikace Excel nebo Power BI. Náklady můžete exportovat na denní, týdenní nebo měsíční plán a nastavit vlastní rozsah kalendářních dat. Export nákladových dat je doporučený způsob, jak načíst datové sady nákladů.

## <a name="next-steps"></a>Další kroky

Další informace o tom, jak ceny fungují Azure Cosmos DB, najdete v následujících článcích:

* [Cenový model ve službě Azure Cosmos DB](how-pricing-works.md)
* [Optimalizace nákladů na zřízenou propustnost ve službě Azure Cosmos DB](optimize-cost-throughput.md)
* [Optimalizace nákladů na dotazování ve službě Azure Cosmos DB](./optimize-cost-reads-writes.md)
* [Optimalizace nákladů na úložiště v Azure Cosmos DB](optimize-cost-storage.md)
* Naučte se [optimalizovat investice do cloudu pomocí Azure cost management](https://docs.microsoft.com/azure/cost-management-billing/costs/cost-mgt-best-practices?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
* Přečtěte si další informace o správě nákladů pomocí [analýzy nákladů](https://docs.microsoft.com/azure/cost-management-billing/costs/quick-acm-cost-analysis?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
* Přečtěte si, jak se [vyhnout neočekávaným nákladům](https://docs.microsoft.com/azure/cost-management-billing/manage/getting-started?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
* Využijte kurz učení s asistencí [cost management](https://docs.microsoft.com/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) .
