---
title: Plánování a správa nákladů pro Azure Cosmos DB
description: Zjistěte, jak plánovat a spravovat náklady pro Azure Cosmos DB pomocí analýzy nákladů na webu Azure Portal.
author: SnehaGunda
ms.author: sngun
ms.custom: subject-cost-optimization
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/02/2020
ms.openlocfilehash: cf259dfa0b00393cf0fee259b199527569ba1792
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77152580"
---
# <a name="plan-and-manage-costs-for-azure-cosmos-db"></a>Plánování a správa nákladů pro Azure Cosmos DB

Tento článek popisuje, jak můžete plánovat a spravovat náklady pro Azure Cosmos DB. Nejprve použijete kalkulačku kapacity Azure Cosmos DB k plánování nákladů před přidáním jakýchkoli prostředků. Dále, když přidáte prostředky Azure, můžete zkontrolovat odhadované náklady. Až začnete používat prostředky Azure Cosmos DB, použijte funkce správy nákladů k nastavení rozpočtů a monitorování nákladů. Můžete také zkontrolovat předpokládané náklady a identifikovat trendy výdajů a určit oblasti, ve kterých chcete jednat.

Pochopte, že náklady na Azure Cosmos DB jsou jenom část měsíčních nákladů ve vaší azure účtu. Pokud používáte jiné služby Azure, budou se vám účtovat všechny služby a prostředky Azure používané ve vašem předplatném Azure, včetně služeb třetích stran. Tento článek vysvětluje, jak plánovat a spravovat náklady pro Azure Cosmos DB. Až budete seznámeni s náklady na správu azure cosmos db, můžete použít podobné metody pro správu nákladů pro všechny služby Azure používané ve vašem předplatném.

## <a name="prerequisites"></a>Požadavky

Analýza nákladů podporuje různé typy účtů Azure. Úplný seznam podporovaných typů účtů si můžete prohlédnout v článku [Vysvětlení dat služby Cost Management](../cost-management-billing/costs/understand-cost-mgt-data.md). Pokud chcete zobrazit data o nákladech, potřebujete přinejmenším oprávnění ke čtení k vašemu účtu Azure. Informace o přiřazování přístupu k datům služby Azure Cost Management najdete v článku [Přiřazení přístupu k datům](../cost-management-billing/costs/assign-access-acm-data.md).

## <a name="review-estimated-costs-with-capacity-calculator"></a>Kontrola odhadovaných nákladů pomocí kalkulátoru kapacity

Pomocí [kalkulátoru kapacity Azure Cosmos DB](https://cosmos.azure.com/capacitycalculator/) můžete odhadnout náklady před vytvořením prostředků v účtu Azure Cosmos. Kalkulačka kapacity se používá k získání odhadu požadované propustnosti a nákladů na vaše pracovní vytížení. Konfigurace databází a kontejnerů Služby Azure Cosmos se správným množstvím zřízené propustnosti nebo [jednotek požadavků (RU/s)](request-units.md)pro vaše úlohy je nezbytná pro optimalizaci nákladů a výkonu. Musíte zadat podrobnosti, jako je typ rozhraní API, počet oblastí, velikost položky, požadavky na čtení a zápis za sekundu, celkový počet dat uložených pro získání odhadu nákladů. Další informace o kalkulátoru kapacity najdete v článku [odhadu.](estimate-ru-with-capacity-planner.md)

Následující snímek obrazovky ukazuje odhad propustnosti a nákladů pomocí kalkulátoru kapacity:

![Odhad nákladů v kalkulátoru kapacity Azure Cosmos DB](./media/plan-manage-costs/capacity-calculator-cost-estimate.png)

## <a name="review-estimated-costs-from-the-azure-portal"></a>Kontrola odhadovaných nákladů z portálu Azure

Při vytváření prostředků Azure Cosmos DB z portálu Azure můžete zobrazit odhadované náklady. Pomocí následujících kroků zkontrolujte odhad nákladů:

1. Přihlaste se k portálu Azure a přejděte na svůj účet Azure Cosmos.
1. Přejděte do **Průzkumníka dat**.
1. Vytvořte nový kontejner, jako je například kontejner grafu.
1. Zadejte propustnost požadovanou pro vaše úlohy, jako je například 400 RU/s. Po zadání hodnoty propustnosti uvidíte odhad cen, jak je znázorněno na následujícím snímku obrazovky:

   ![Odhad nákladů na webu Azure Portal](./media/plan-manage-costs/cost-estimate-portal.png)

Pokud má vaše předplatné Azure limit útraty, Azure vám zabrání v tom, abyste utratili přes částku kreditu. Při vytváření a používání prostředků Azure se používají vaše kredity. Po dosažení limitu úvěru jsou prostředky, které jste nasadili, po zbytek fakturačního období zakázány. Limit kreditu nemůžete změnit, ale můžete ho odebrat. Další informace o limitech útraty najdete v [tématu Limit útraty Azure](../billing/billing-spending-limit.md).

## <a name="use-budgets-and-cost-alerts"></a>Použití rozpočtů a upozornění na náklady

Pro účely řízení nákladů můžete vytvořit [rozpočty](../cost-management/tutorial-acm-create-budgets.md) a nastavit upozornění, která účastníky automaticky upozorňují na anomálie a nebezpečí nadměrných výdajů. Upozornění jsou založena na porovnání útraty s rozpočtem a prahovými hodnotami nákladů. Rozpočty a výstrahy se vytvářejí pro předplatná Azure a skupiny prostředků, takže jsou užitečné jako součást celkové strategie monitorování nákladů. Mohou však mít omezené funkce pro správu jednotlivých nákladů na služby Azure, jako jsou náklady na Azure Cosmos DB, protože jsou navržené ke sledování nákladů na vyšší úrovni.

## <a name="monitor-costs"></a>Sledování nákladů

Při používání prostředků s Azure Cosmos DB vám vznikají náklady. Jednotkové náklady na využití zdrojů se liší podle časových intervalů (sekund, minut, hodin a dnů) nebo podle využití jednotky požadavku. Jakmile se spustí využití Azure Cosmos DB, vzniknou náklady a můžete je zobrazit v podokně [analýzy nákladů](../cost-management/quick-acm-cost-analysis.md) na webu Azure Portal.

Při použití analýzy nákladů můžete zobrazit náklady na Azure Cosmos DB v grafech a tabulkách pro různé časové intervaly. Některé příklady jsou podle dne, aktuálního, předchozího měsíce a roku. Můžete také zobrazit náklady na rozpočty a předpokládané náklady. Přechod na delší zobrazení v průběhu času vám může pomoci identifikovat trendy výdajů a zjistit, kde mohlo dojít k nadměrným výdajům. Pokud jste vytvořili rozpočty, můžete také snadno zjistit, kde byly překročeny. Zobrazení nákladů služby Azure Cosmos DB v analýze nákladů:

1. Přihlaste se k [portálu Azure](https://portal.azure.com).

1. Otevřete okno **Řízení nákladů + Fakturace,** v nabídce vyberte **Řízení nákladů** a pak vyberte **Analýza nákladů**. Potom můžete změnit obor pro konkrétní předplatné z rozbalovací **nabídky Obor.**

1. Ve výchozím nastavení jsou náklady na všechny služby zobrazeny v prvním grafu koblih. Vyberte oblast v grafu s názvem "Azure Cosmos DB".

1. Pokud chcete zúžit náklady na jednu službu, jako je Azure Cosmos DB, vyberte **Přidat filtr** a pak vyberte **Název služby**. Potom zvolte **Azure Cosmos DB** ze seznamu. Tady je příklad, který ukazuje náklady pouze za Azure Cosmos DB:
 
   ![Sledování nákladů pomocí podokna Analýza nákladů](./media/plan-manage-costs/cost-analysis-pane.png)

V předchozím příkladu uvidíte aktuální náklady na Azure Cosmos DB za měsíc únor. Grafy také obsahují náklady na Azure Cosmos DB podle umístění a podle skupiny prostředků.

## <a name="next-steps"></a>Další kroky

Další informace o tom, jak ceny v Azure Cosmos DB fungují, najdete v následujících článcích:

* [Cenový model ve službě Azure Cosmos DB](how-pricing-works.md)
* [Optimalizace nákladů na zřízenou propustnost ve službě Azure Cosmos DB](optimize-cost-throughput.md)
* [Optimalizace nákladů na dotazování ve službě Azure Cosmos DB](optimize-cost-queries.md)
* [Optimalizace nákladů na úložiště v Azure Cosmos DB](optimize-cost-storage.md)