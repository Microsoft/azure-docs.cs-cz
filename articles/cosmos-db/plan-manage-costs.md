---
title: Plánování a Správa nákladů na Azure Cosmos DB
description: Naučte se plánovat a spravovat náklady na Azure Cosmos DB pomocí analýzy nákladů v Azure Portal.
author: SnehaGunda
ms.author: sngun
ms.custom: subject-cost-optimization
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/02/2020
ms.openlocfilehash: 42421f745759d9aee75b285c3fbc6ea7217ba5c0
ms.sourcegitcommit: 23604d54077318f34062099ed1128d447989eea8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/20/2020
ms.locfileid: "85112697"
---
# <a name="plan-and-manage-costs-for-azure-cosmos-db"></a>Plánování a Správa nákladů na Azure Cosmos DB

Tento článek popisuje, jak můžete plánovat a spravovat náklady na Azure Cosmos DB. Nejprve pomocí kalkulačky Azure Cosmos DB kapacity pomůžete plánovat náklady před přidáním prostředků. V dalším kroku můžete při přidávání prostředků Azure zkontrolovat odhadované náklady. Po zahájení práce s prostředky Azure Cosmos DB použijte funkce pro správu nákladů a nastavte rozpočty a sledujte náklady. Můžete si také projít předpokládané náklady a identifikovat trendy útraty, které identifikují oblasti, kde můžete chtít pracovat.

Pochopení, že náklady na Azure Cosmos DB jsou jenom částí měsíčních nákladů na faktuře Azure. Pokud používáte další služby Azure, účtuje se vám všechny služby a prostředky Azure, které se používají ve vašem předplatném Azure, včetně služeb třetích stran. Tento článek vysvětluje, jak naplánovat a spravovat náklady na Azure Cosmos DB. Až budete obeznámeni se správou nákladů na Azure Cosmos DB, můžete použít podobné metody pro správu nákladů na všechny služby Azure používané ve vašem předplatném.

## <a name="prerequisites"></a>Požadavky

Analýza nákladů podporuje různé typy účtů Azure. Úplný seznam podporovaných typů účtů si můžete prohlédnout v článku [Vysvětlení dat služby Cost Management](../cost-management-billing/costs/understand-cost-mgt-data.md). Pokud chcete zobrazit data o nákladech, potřebujete přinejmenším oprávnění ke čtení k vašemu účtu Azure. Informace o přiřazování přístupu k datům služby Azure Cost Management najdete v článku [Přiřazení přístupu k datům](../cost-management-billing/costs/assign-access-acm-data.md).

## <a name="review-estimated-costs-with-capacity-calculator"></a>Kontrola odhadovaných nákladů pomocí kalkulačky kapacity

Pomocí [kalkulačky Azure Cosmos DB kapacity](https://cosmos.azure.com/capacitycalculator/) můžete odhadnout náklady ještě před vytvořením prostředků v účtu Azure Cosmos. Kalkulačka kapacity slouží k získání odhadu požadované propustnosti a nákladů na vaše zatížení. Konfigurace databází a kontejnerů Azure Cosmos s využitím správného množství zřízené propustnosti nebo [jednotek žádostí (ru/s)](request-units.md)pro vaše zatížení je základem pro optimalizaci nákladů a výkonu. Musíte zadat podrobnosti, jako je typ rozhraní API, počet oblastí, velikost položky, počet žádostí o čtení a zápis za sekundu, celkovou data uložená k získání odhadu nákladů. Další informace o kalkulačkě kapacity najdete v článku [odhad](estimate-ru-with-capacity-planner.md) .

Následující snímek obrazovky ukazuje odhad propustnosti a nákladů pomocí kalkulačky kapacity:

:::image type="content" source="./media/plan-manage-costs/capacity-calculator-cost-estimate.png" alt-text="Odhad nákladů v programu Kalkulačka kapacity Azure Cosmos DB":::

## <a name="review-estimated-costs-from-the-azure-portal"></a>Kontrola odhadovaných nákladů z Azure Portal

Při vytváření Azure Cosmos DBch prostředků z Azure Portal můžete zobrazit odhadované náklady. Odhad nákladů můžete zkontrolovat pomocí následujících kroků:

1. Přihlaste se k Azure Portal a přejděte k účtu Azure Cosmos.
1. Přejít na **Průzkumník dat**.
1. Vytvořte nový kontejner, jako je například kontejner grafu.
1. Zadejte propustnost požadovanou pro vaše zatížení, například 400 RU/s. Po zadání hodnoty propustnosti uvidíte odhad ceny, jak je znázorněno na následujícím snímku obrazovky:

   :::image type="content" source="./media/plan-manage-costs/cost-estimate-portal.png" alt-text="Odhad nákladů v Azure Portal":::

Pokud má vaše předplatné Azure limit útraty, Azure vám zabrání v útratě za vaši kreditní částku. Při vytváření a používání prostředků Azure se vaše kredity používají. Po dosažení limitu kreditu budou prostředky, které jste nasadili, zakázané pro zbytek tohoto fakturačního období. Limit kreditu nemůžete změnit, ale můžete ho odebrat. Další informace o limitech útraty najdete v tématu [limit útraty Azure](../billing/billing-spending-limit.md).

## <a name="use-budgets-and-cost-alerts"></a>Použití rozpočtů a upozornění na náklady

Pro účely řízení nákladů můžete vytvořit [rozpočty](../cost-management/tutorial-acm-create-budgets.md) a nastavit upozornění, která účastníky automaticky upozorňují na anomálie a nebezpečí nadměrných výdajů. Upozornění jsou založena na porovnání útraty s rozpočtem a prahovými hodnotami nákladů. Rozpočty a výstrahy se vytvářejí pro předplatná Azure a skupiny prostředků, takže jsou užitečné v rámci celkové strategie monitorování nákladů. Můžou ale mít omezené funkce pro správu jednotlivých nákladů na služby Azure, jako jsou náklady na Azure Cosmos DB, protože jsou navržené tak, aby sledovaly náklady na vyšší úrovni.

## <a name="monitor-costs"></a>Sledovat náklady

Při používání prostředků s Azure Cosmos DB se účtují náklady. Náklady na jednotku využívání prostředků se liší podle časových intervalů (sekundy, minuty, hodiny a dny) nebo podle využití jednotek žádosti. Jakmile se začne Azure Cosmos DB, účtují se náklady a můžete je zobrazit v podokně [Analýza nákladů](../cost-management/quick-acm-cost-analysis.md) v Azure Portal.

Při použití analýzy nákladů můžete zobrazit Azure Cosmos DB náklady v grafech a tabulkách v různých časových intervalech. Některé příklady jsou podle dne, aktuálního, předchozího měsíce a roku. Můžete také zobrazit náklady na rozpočty a předpokládané náklady. Přepínáním na delší zobrazení v průběhu času vám může pomáhat identifikovat trendy útraty a zjistit, kde došlo k nadměrnému útratě. Pokud jste vytvořili rozpočty, můžete také snadno zjistit, kde byly překročeny. Postup zobrazení Azure Cosmos DBch nákladů při analýze nákladů:

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com).

1. Otevřete okno **cost management + fakturace** , v nabídce vyberte možnost **Správa nákladů** a pak vyberte **Analýza nákladů**. V rozevíracím seznamu **Rozsah** pak můžete změnit obor pro konkrétní předplatné.

1. Ve výchozím nastavení jsou náklady na všechny služby zobrazeny v prvním prstenovém grafu. Vyberte oblast v grafu s názvem "Azure Cosmos DB".

1. Chcete-li zúžit náklady na jednu službu, například Azure Cosmos DB, vyberte možnost **Přidat filtr** a potom vyberte možnost **název služby**. Pak ze seznamu vyberte **Azure Cosmos DB** . Tady je příklad, který ukazuje náklady jenom pro Azure Cosmos DB:
 
   :::image type="content" source="./media/plan-manage-costs/cost-analysis-pane.png" alt-text="Sledovat náklady pomocí podokna analýza nákladů":::

V předchozím příkladu vidíte aktuální náklady za Azure Cosmos DB v měsíci v únoru. Grafy také obsahují náklady na Azure Cosmos DB podle umístění a podle skupiny prostředků.

## <a name="next-steps"></a>Další kroky

Další informace o tom, jak ceny fungují Azure Cosmos DB, najdete v následujících článcích:

* [Cenový model ve službě Azure Cosmos DB](how-pricing-works.md)
* [Optimalizace nákladů na zřízenou propustnost ve službě Azure Cosmos DB](optimize-cost-throughput.md)
* [Optimalizace nákladů na dotazování ve službě Azure Cosmos DB](optimize-cost-queries.md)
* [Optimalizace nákladů na úložiště v Azure Cosmos DB](optimize-cost-storage.md)