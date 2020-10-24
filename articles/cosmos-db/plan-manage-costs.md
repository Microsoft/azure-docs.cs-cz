---
title: Plánování a Správa nákladů na Azure Cosmos DB
description: Naučte se plánovat a spravovat náklady na Azure Cosmos DB pomocí analýzy nákladů v Azure Portal.
author: SnehaGunda
ms.author: sngun
ms.custom: subject-cost-optimization
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/19/2020
ms.openlocfilehash: 278603887fe7d47b4be52b04f9f0864be1a1b75b
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2020
ms.locfileid: "92482243"
---
# <a name="plan-and-manage-costs-for-azure-cosmos-db"></a>Plánování a Správa nákladů na Azure Cosmos DB

Tento článek popisuje, jak můžete plánovat a spravovat náklady na Azure Cosmos DB:

- Odhad toho, co budou vaše náklady před vytvořením jakýchkoli prostředků
- Projděte si odhadované náklady při zahájení používání prostředků.
- Použití funkcí pro správu nákladů k nastavení rozpočtů a sledování nákladů
- Projděte si předpovědi předpokládaných nákladů a Identifikujte trendy útraty, které odhalí oblasti, kde byste mohli chtít působit.

Pochopení, že náklady na Azure Cosmos DB jsou jenom částí měsíčních nákladů na faktuře Azure. Pokud používáte další služby Azure, účtuje se vám všechny služby a prostředky Azure, které se používají ve vašem předplatném Azure, včetně služeb třetích stran. Tento článek vysvětluje, jak naplánovat a spravovat náklady na Azure Cosmos DB. Až budete obeznámeni se správou nákladů na Azure Cosmos DB, můžete použít podobné metody pro správu nákladů na všechny služby Azure používané ve vašem předplatném.

## <a name="prerequisites"></a>Požadavky

Analýza nákladů podporuje různé typy účtů Azure. Úplný seznam podporovaných typů účtů si můžete prohlédnout v článku [Vysvětlení dat služby Cost Management](../cost-management-billing/costs/understand-cost-mgt-data.md). Pokud chcete zobrazit data o nákladech, potřebujete přinejmenším oprávnění ke čtení k vašemu účtu Azure. Informace o přiřazování přístupu k datům služby Azure Cost Management najdete v článku [Přiřazení přístupu k datům](../cost-management-billing/costs/assign-access-acm-data.md).

## <a name="provisioned-throughput-or-serverless"></a>Zřízená propustnost nebo bez serveru

Azure Cosmos DB podporuje dva typy režimů kapacity: [zřízená propustnost](set-throughput.md) a bez [serveru](serverless.md). Způsob, jakým se vám budou účtovat poplatky za Azure Cosmos DB, se mezi těmito dvěma režimy liší, takže je důležité zvolit ten, který pro vaše zatížení funguje nejlépe. Pokyny a doporučení k tomu, jak vybrat, najdete v článku [Jak zvolit mezi zřízenou propustností a bez serveru](throughput-serverless.md) .

## <a name="estimating-provisioned-throughput-costs-with-capacity-calculator"></a>Odhad nákladů na zajištěné propustnosti pomocí kalkulačky kapacity

Pokud plánujete používat Azure Cosmos DB v režimu zřízené propustnosti, můžete pomocí [kalkulačky Azure Cosmos DB Capacity](https://cosmos.azure.com/capacitycalculator/) odhadnout náklady ještě před vytvořením prostředků v účtu Azure Cosmos. Kalkulačka kapacity slouží k získání odhadu požadované propustnosti a nákladů na vaše zatížení. Konfigurace databází a kontejnerů Azure Cosmos s využitím správného množství zřízené propustnosti nebo [jednotek žádostí (ru/s)](request-units.md)pro vaše zatížení je základem pro optimalizaci nákladů a výkonu. Musíte zadat podrobnosti, jako je typ rozhraní API, počet oblastí, velikost položky, počet žádostí o čtení a zápis za sekundu, celkovou data uložená k získání odhadu nákladů. Další informace o kalkulačkě kapacity najdete v článku [odhad](estimate-ru-with-capacity-planner.md) .

Následující snímek obrazovky ukazuje odhad propustnosti a nákladů pomocí kalkulačky kapacity:

:::image type="content" source="./media/plan-manage-costs/capacity-calculator-cost-estimate.png" alt-text="Odhad nákladů v programu Kalkulačka kapacity Azure Cosmos DB":::

## <a name="estimating-serverless-costs"></a>Odhad nákladů bez serveru

Pokud plánujete používat Azure Cosmos DB v režimu bez serveru, musíte odhadnout, kolik [jednotek požadavků](request-units.md) a GB úložiště můžete využívat měsíčně. Vyhodnocením počtu databázových operací, které by se vystavily v měsíci, můžete odhadnout požadované množství jednotek žádostí a vynásobit jejich množství odpovídajícími náklady na RU. V následující tabulce jsou uvedeny odhadované poplatky za RU za běžné databázové operace:

| Operace | Odhadované náklady | Poznámky |
| --- | --- | --- |
| Vytvořit položku | 5 ru | Průměrné náklady na položku 1 KB s méně než 5 vlastnostmi pro index |
| Aktualizace položky | 10 RU | Průměrné náklady na položku 1 KB s méně než 5 vlastnostmi pro index |
| Číst jednotlivou položku podle jejího ID a klíče oddílu (bod-čtení) | 1 RU | Průměrné náklady za položku v 1 KB |
| Odstranění položky | 5 ru | |
| Spustit dotaz | 10 RU | Průměrné náklady na dotaz, který plně využívá [indexování](index-overview.md) a vrací 100 výsledků nebo méně |

> [!IMPORTANT] 
> Věnujte pozornost komentářům z výše uvedené tabulky. Pro přesnější odhad skutečných nákladů na vaše operace můžete použít [emulátor Azure Cosmos](local-emulator.md) a [měřit přesné náklady na ru za vaše operace](find-request-unit-charge.md). I když emulátor Azure Cosmos nepodporuje bez serveru, oznamuje za databázové operace Standard RU a dá se použít pro tento odhad.

Po vypočítání celkového počtu jednotek žádostí a GB úložiště, které budete pravděpodobně spotřebovávat za měsíc, vrátí následující vzorec odhad nákladů: **([počet jednotek žádosti]/1 000 000 × $0,25) + ([GB úložiště] * $0,25)**.

> [!NOTE]
> Náklady zobrazené v předchozím příkladu jsou pouze pro demonstrační účely. Nejnovější informace o cenách najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/cosmos-db/) .

## <a name="review-estimated-costs-from-the-azure-portal"></a>Kontrola odhadovaných nákladů z Azure Portal

Při zahájení používání prostředků Azure Cosmos DB z Azure Portal můžete zobrazit odhadované náklady. Odhad nákladů můžete zkontrolovat pomocí následujících kroků:

1. Přihlaste se k Azure Portal a přejděte k účtu Azure Cosmos.
1. Přejít na oddíl **Přehled** .
1. Podívejte se na **nákladový** graf v dolní části. Tento graf znázorňuje odhad vašich aktuálních nákladů v rámci konfigurovatelného časového období:
1. Vytvořte nový kontejner, jako je například kontejner grafu.
1. Zadejte propustnost požadovanou pro vaše zatížení, například 400 RU/s. Po zadání hodnoty propustnosti uvidíte odhad ceny, jak je znázorněno na následujícím snímku obrazovky:

   :::image type="content" source="./media/plan-manage-costs/cost-estimate-portal.png" alt-text="Odhad nákladů v programu Kalkulačka kapacity Azure Cosmos DB":::

## <a name="use-budgets-and-cost-alerts"></a>Použití rozpočtů a upozornění na náklady

Pro účely řízení nákladů můžete vytvořit [rozpočty](../cost-management-billing/costs/tutorial-acm-create-budgets.md) a nastavit upozornění, která účastníky automaticky upozorňují na anomálie a nebezpečí nadměrných výdajů. Upozornění jsou založena na porovnání útraty s rozpočtem a prahovými hodnotami nákladů. Rozpočty a výstrahy se vytvářejí pro předplatná Azure a skupiny prostředků, takže jsou užitečné v rámci celkové strategie monitorování nákladů. Můžou ale mít omezené funkce pro správu jednotlivých nákladů na služby Azure, jako jsou náklady na Azure Cosmos DB, protože jsou navržené tak, aby sledovaly náklady na vyšší úrovni.

Pokud má vaše předplatné Azure limit útraty, Azure vám zabrání v útratě za vaši kreditní částku. Při vytváření a používání prostředků Azure se vaše kredity používají. Po dosažení limitu kreditu budou prostředky, které jste nasadili, zakázané pro zbytek tohoto fakturačního období. Limit kreditu nemůžete změnit, ale můžete ho odebrat. Další informace o limitech útraty najdete v tématu [limit útraty Azure](../cost-management-billing/manage/spending-limit.md).

## <a name="monitor-costs"></a>Sledovat náklady

Při používání prostředků s Azure Cosmos DB se účtují náklady. Náklady na jednotku využívání prostředků se liší podle časových intervalů (sekundy, minuty, hodiny a dny) nebo podle využití jednotek žádosti. Jakmile se začne Azure Cosmos DB, účtují se náklady a můžete je zobrazit v podokně [Analýza nákladů](../cost-management-billing/costs/quick-acm-cost-analysis.md) v Azure Portal.

Při použití analýzy nákladů můžete zobrazit Azure Cosmos DB náklady v grafech a tabulkách v různých časových intervalech. Některé příklady jsou podle dne, aktuálního, předchozího měsíce a roku. Můžete také zobrazit náklady na rozpočty a předpokládané náklady. Přepínáním na delší zobrazení v průběhu času vám může pomáhat identifikovat trendy útraty a zjistit, kde došlo k nadměrnému útratě. Pokud jste vytvořili rozpočty, můžete také snadno zjistit, kde byly překročeny. Postup zobrazení Azure Cosmos DBch nákladů při analýze nákladů:

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com).

1. Otevřete okno **cost management + fakturace** , v nabídce vyberte možnost **Správa nákladů** a pak vyberte **Analýza nákladů**. V rozevíracím seznamu **Rozsah** pak můžete změnit obor pro konkrétní předplatné.

1. Ve výchozím nastavení jsou náklady na všechny služby zobrazeny v prvním prstenovém grafu. Vyberte oblast v grafu s názvem "Azure Cosmos DB".

1. Chcete-li zúžit náklady na jednu službu, například Azure Cosmos DB, vyberte možnost **Přidat filtr** a potom vyberte možnost **název služby**. Pak ze seznamu vyberte **Azure Cosmos DB** . Tady je příklad, který ukazuje náklady jenom pro Azure Cosmos DB:
 
   :::image type="content" source="./media/plan-manage-costs/cost-analysis-pane.png" alt-text="Odhad nákladů v programu Kalkulačka kapacity Azure Cosmos DB":::

V předchozím příkladu vidíte aktuální náklady za Azure Cosmos DB v měsíci v únoru. Grafy také obsahují náklady na Azure Cosmos DB podle umístění a podle skupiny prostředků.

## <a name="next-steps"></a>Další kroky

Další informace o tom, jak ceny fungují Azure Cosmos DB, najdete v následujících článcích:

* [Cenový model ve službě Azure Cosmos DB](how-pricing-works.md)
* [Optimalizace nákladů na zřízenou propustnost ve službě Azure Cosmos DB](optimize-cost-throughput.md)
* [Optimalizace nákladů na dotazování ve službě Azure Cosmos DB](./optimize-cost-reads-writes.md)
* [Optimalizace nákladů na úložiště v Azure Cosmos DB](optimize-cost-storage.md)