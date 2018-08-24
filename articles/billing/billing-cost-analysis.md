---
title: Prozkoumejte Azure náklady pomocí cost analysis | Dokumentace Microsoftu
description: Tento článek vám pomůže používat analýzu nákladů můžete zkoumat a analyzovat vaše náklady organizace na Azure.
services: billing
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 08/16/2018
ms.topic: conceptual
ms.service: billing
manager: dougeby
ms.custom: ''
ms.openlocfilehash: db06956e01ed38ce1f8ee0ce92526be16733fd73
ms.sourcegitcommit: 58c5cd866ade5aac4354ea1fe8705cee2b50ba9f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/24/2018
ms.locfileid: "42818120"
---
# <a name="explore-and-analyze-costs-with-cost-analysis"></a>Zkoumání a analýzy nákladů pomocí analýzy nákladů

Než budete moct správně ovládat a optimalizovat náklady na Azure, potřebujete pochopit, odkud pochází náklady v rámci vaší organizace. Také je užitečné vědět, kolik peněz služby nákladů a na jaké prostředí a systémy. Přehled o celé spektrum nákladů je zásadně důležité pochopit přesně organizační útraty vzory, které je možné použít k vynucení náklady kontrolní mechanismy, jako je rozpočtů.

V tomto článku použijte analýzy nákladů můžete zkoumat a analyzovat vaše organizace náklady. Zobrazit souhrnné náklady ve vaší organizaci k pochopení, ve kterém jsou náklady na operace a identifikaci trendů výdajů. Zobrazení celkové náklady v čase k odhadu měsíčně, čtvrtletně, a dokonce i roční trendů ve vývoji ve vztahu k rozpočtu nákladů. Rozpočet pomáhá dodržovat finančních omezení nebo zobrazení denní nebo měsíční náklady a izolovat útraty nesrovnalosti. Zároveň si můžete stáhnout aktuální sestavu dat pro další analýzu, nebo pro použití v externím systému.

## <a name="requirements"></a>Požadavky

Analýza nákladů je k dispozici všem zákazníkům Enterprise Agreement (EA). Alespoň jedna z následujících oborů zobrazení datům služby cost musí mít oprávnění ke čtení.

- Azure EA fakturační účet (zápis)
- Předplatné Azure EA
- Skupiny prostředků předplatného Azure EA

## <a name="review-costs-in-cost-analysis"></a>Náklady na revize v analýzy nákladů

Pokud chcete zkontrolovat svoje náklady pomocí cost analysis, otevřete na webu Azure portal a přejděte na adresu **Správa nákladů a fakturace** &gt; **fakturaci účtů** &gt; vyberte příslušné smlouvy EA fakturační účet &gt; v části Cost Management, vyberte **analýza nákladů**.

Zobrazení analýzy počáteční náklady se týká následujících oblastí:

**Celkový počet** – ukazuje celkové náklady pro aktuální měsíc.

**Rozpočet** – zobrazuje plánované útraty omezení pro vybraný obor, pokud je k dispozici.

**Začnou** – zobrazuje celkový počet operace denní útraty, od začátku měsíce. Pokud máte [vytvořili rozpočtu](billing-cost-management-budget-scenario.md#create-the-azure-budget) pro váš fakturační účet nebo předplatné, můžete rychle zobrazit vaší útraty trendů ve vztahu k rozpočtu. Najeďte myší datum, které chcete celkové náklady pro daný den.

**Kontingenční grafy (prstencový)** – zadejte dynamické pivotů. Rozdělit náklady podle společnou sadu standardních vlastností. Zobrazit grafy nejdůležitější pro nejnižší náklady na operace pro aktuální měsíc. Grafy můžete kdykoli změnit tak, že vyberete jiný pivot. Ve výchozím nastavení jsou náklady na rozdělené podle služby (kategorie měřiče), umístění (oblast) a podřízeném oboru (například registračních účtů pod fakturační účty, skupiny prostředků v rámci předplatná a prostředky v rámci skupiny prostředků).

![Počáteční zobrazení analýzy nákladů](./media/billing-cost-analysis/cost-analysis-01.png)



## <a name="customizing-cost-views"></a>Přizpůsobení zobrazení nákladů

Výchozí zobrazení obsahuje rychlé odpovědi na běžné dotazy jako:

- Kolik výdaje?
- Bude Moje rozpočtu neopustí?

Existují však mnoha případech, kdy potřebujete hlubší analýzu. Vlastní nastavení se spustí v horní části stránky se výběr data.

Ve výchozím nastavení zobrazuje náklady na analýzu dat pro aktuální měsíc. Pomocí modulu pro výběr data rychle přepínat na poslední měsíc, tento měsíc, tohoto čtvrtletí kalendáře, tato kalendářní rok nebo vlastní rozsah podle vašeho výběru. Výběr minulý měsíc je nejrychlejší způsob, jak analyzovat faktuře nejnovější Azure a snadno sjednotit poplatky. Aktuální čtvrtletí a roku možnosti usnadnění sledování nákladů s rozpočty dlouhodobější. Nebo podrobné nebo širší rozsah dat, můžete vybrat z jednoho dne, posledních sedmi dnů nebo cokoli již rok předcházející aktuální měsíc.

![Výběr data](./media/billing-cost-analysis/date-selector.png)

Také ve výchozím nastavení, cost analysis zobrazí **nahromaděné** náklady. Celkové náklady zahrnují všechny poplatky za každý den kromě předchozích dnech neustále rostoucí zobrazení jednou denně, výši nákladů. Toto zobrazení je optimalizovaná pro zobrazení, jak se trendy proti rozpočet pro vybraný časový rozsah.

K dispozici je také **denní** zobrazení. Ukazuje náklady za každý den a nezobrazí growth trend. Denní zobrazení je optimalizovaná pro zobrazení nesrovnalosti jako náklady na zásobníku nebo ponořit den. Když vyberete rozpočtu, denní zobrazení také ukazuje odhad každodenní rozpočtu může vypadat. Pokud váš denní náklady jsou konzistentně nad odhadované denní rozpočtu, pak můžete očekávat, že budete překročí měsíční rozpočtu. Odhadované denní rozpočet je jednoduše způsob pro lepší vizualizaci vašemu rozpočtu na nižší úrovni. Pokud máte kolísání denní náklady, je odhad každodenní rozpočtu porovnání měsíční rozpočtu méně přesné.

![Denní zobrazení](./media/billing-cost-analysis/daily-view.png)

Je možné **Seskupit podle** k výběru kategorie skupině chcete-li změnit data zobrazí v horní části celkový grafu. Seskupení umožňuje rychle zjistíte, jak vaší útraty jsou rozdělené do kategorií podle typu prostředku. Tady je přehled služby Azure za poslední měsíc zobrazení.

![Seskupené denní souhrnné zobrazení](./media/billing-cost-analysis/grouped-daily-accum-view.png)

Grafy jsou v horním celkový počet zobrazení. Je použijte k zobrazení různých seskupování a filtrování kategorií naleznete v tématu. Když vyberete kategorii všechny skupiny, je kompletní sadu dat pro celkový počet zobrazení v dolní části zobrazení. Tady je příklad pro skupiny prostředků.

![Úplná data pro aktuální zobrazení](./media/billing-cost-analysis/full-data-set.png)

### <a name="download-cost-analysis-data"></a>Stáhnout data analýzy nákladů

Pokud jste **Stáhnout** informace z analýza nákladů, soubor CSV se vygeneruje pro všechna data aktuálně zobrazený na portálu Azure portal. Pokud jste použili nějaké filtry nebo seskupení, pak jsou zahrnuté v souboru. Některé podkladová data pro začátek celkový graf, který není aktivně zobrazí na portálu je také součástí souboru CSV.

## <a name="next-steps"></a>Další postup

Zobrazit další [dokumentace ke službě správy nákladů a fakturací Azure](billing-cost-management-budget-scenario.md).
