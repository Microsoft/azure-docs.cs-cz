---
title: Optimalizace úloh Azure pomocí skóre služby Advisor
description: Využijte Azure Advisor skóre a využijte Azure na maximum.
ms.topic: article
ms.date: 09/09/2020
ms.openlocfilehash: 11b20bc3b4d604d3a7ff4608cd1c21f129c1cb6d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "97630118"
---
# <a name="optimize-azure-workloads-by-using-advisor-score"></a>Optimalizace úloh Azure pomocí skóre služby Advisor

## <a name="introduction-to-advisor-score"></a>Úvod do skóre Advisoru

Azure Advisor poskytuje doporučení pro osvědčené postupy pro vaše úlohy. Tato doporučení jsou přizpůsobená a umožňují vám tyto akce:

* Vylepšete stav vašich úloh a optimalizujte nasazení Azure.
* Proaktivně zabráníte nejdůležitějším problémům pomocí následujících osvědčených postupů.
* Vyhodnoťte vaše úlohy Azure proti pěti pilířům [Microsoft Azure Well-Architected Framework](/azure/architecture/framework/).

Skóre Advisoru jako základní funkce Advisoru vám může přispět k efektivnímu a efektivnímu dosažení těchto cílů.

Abyste mohli Azure využít na maximum, je důležité pochopit, kde jste v rámci své cesty optimalizace úloh. Potřebujete zjistit, které služby nebo prostředky jsou dobře spotřebované a které ne. Dále budete chtít zjistit, jak upřednostnit vaše akce na základě doporučení a maximalizovat tak výsledek.

Je také důležité sledovat pokrok, který v této cestě optimalizace vytváříte, a nahlásit ho. Díky hodnocení Advisoru můžete snadno provádět všechny tyto akce s novým prostředím gamifikační.

V rámci vašeho přizpůsobeného cloudového konzultanta Azure Advisor průběžně vyhodnocuje telemetrii využití a konfiguraci prostředků, aby kontroloval osvědčené postupy pro obor. Poradce pak agreguje své závěry do jednoho skóre. Díky tomuto skóre můžete rychle zjistit, jestli jste provedli nezbytné kroky k vytváření spolehlivých, zabezpečených a nákladově efektivních řešení.

Skóre služby Advisor se skládá z celkového skóre, které může být dále rozděleno na pět kategorií skóre. Jedno skóre pro každou kategorii Advisoru představuje pět pilířů Well-Architected Frameworku.

Průběh můžete sledovat v průběhu času zobrazením celkového skóre a skóre kategorie pomocí každodenních, týdenních a měsíčních trendů. Můžete také nastavit srovnávací testy, které vám pomůžou dosáhnout vašich cílů.

 ![Snímek obrazovky zobrazující stránku skóre Advisoru](./media/advisor-score-1.png)

## <a name="interpret-an-advisor-score"></a>Interpretace skóre služby Advisor

Advisor zobrazuje celkové skóre služby Advisor a rozpis pro kategorie poradce v procentech. Skóre 100% v jakékoli kategorii znamená, že všechny prostředky vyhodnocené poradcem dodržují osvědčené postupy, které doporučuje poradce. Na druhém konci spektra znamená skóre 0%, že žádný z vašich prostředků vyhodnocený poradcem podle doporučení poradce. Pomocí těchto zrn skóre můžete snadno dosáhnout tohoto toku:

* **Skóre Advisoru** vám pomůže podle směrného plánu, jak vaše zatížení nebo odběry provádějí na základě skóre služby Advisor. Můžete si také prohlédnout historické trendy a porozumět tomu, co trend je.
* **Skóre podle kategorií** pro každé doporučení informuje o tom, že nedokončená doporučení zlepšují skóre. Tyto hodnoty odrážejí váhu doporučení a předpokládanou snadnou implementaci. Tyto faktory vám pomůžou zajistit, aby bylo možné získat co nejvíc hodnot s vaším časem. Také vám pomůžou s určováním priorit.
* **Význam skóre kategorií** pro každé doporučení vám pomůže určit prioritu akcí nápravy pro jednotlivé kategorie.

Příspěvek každého doporučení na skóre vaší kategorie se na stránce hodnocení služby **Advisor** v Azure Portal zobrazuje jasně. Můžete zvýšit skóre jednotlivých kategorií podle procenta, které je uvedené ve sloupci **potenciální nárůst skóre** . Tato hodnota odráží jak váhu doporučení v rámci kategorie, tak předpokládanou snadnou implementaci pro řešení potenciálně nejjednodušších úkolů. Zaměření na doporučení s největším dopadem na skóre vám pomůže s tím, jak budete mít čas.

![Snímek obrazovky zobrazující dopad skóre Advisoru](./media/advisor-score-2.png)

Pokud některá doporučení služby Advisor nejsou relevantní pro jednotlivé prostředky, můžete tato doporučení odložit nebo zrušit. Budou vyloučeny ze výpočtu skóre při další aktualizaci. Poradce taky použije tento vstup jako další zpětnou vazbu ke zlepšení modelu.

## <a name="how-is-an-advisor-score-calculated"></a>Jak se počítá skóre služby Advisor?

Advisor zobrazuje skóre kategorií a celkové skóre Advisoru jako procenta. Skóre 100% v jakékoli kategorii znamená všechny prostředky, které *Advisor vyhodnotil*, podle osvědčených postupů, které doporučuje poradce. Na druhém konci spektra znamená skóre 0%, že žádný z vašich prostředků, který Advisor vyhodnotil, sleduje doporučení poradce.

**Každá z pěti kategorií má nejvyšší možná skóre 100.** Vaše celkové skóre služby Advisor se vypočítá jako součet každé platné skóre kategorie, dělené součtem nejvyššího možného skóre ze všech platných kategorií. U většiny předplatných to znamená, že nástroj Advisor přidá skóre z každé kategorie a vydělí je 500. *Jednotlivé skóre kategorií se ale vypočítávají jenom v případě, že použijete prostředky, které služba Advisor vyhodnocuje*.

### <a name="advisor-score-calculation-example"></a>Příklad výpočtu skóre Advisoru

* **Skóre jednoho předplatného:** Tento příklad představuje jednoduchý význam všech hodnocení kategorií služby Advisor pro vaše předplatné. Pokud jsou skóre kategorie poradce- **náklady** = 73, **spolehlivost** = 85, **provozní excelence** = 77 a **výkon** = 100, bude skóre poradce (73 + 85 + 77 + 100)/(4x100) = 0,84% nebo 84%.
* **Skóre více předplatných:** Pokud je vybráno více předplatných, budou celkové vygenerované výsledky Advisoru agregované podle skóre kategorií. V tomto příkladu se skóre každé kategorie poradce agreguje na základě prostředků spotřebovaných předplatnými. Po vyhodnocení vážené agregované kategorie pomocí Poradce provede nástroj Advisor jednoduché průměrné výpočty, které vám poskytnou celkové skóre předplatných.

### <a name="scoring-methodology"></a>Metodologie bodování

Výpočet skóre Advisoru se dá shrnout ve čtyřech krocích:

1. Poradce vypočítá *maloobchodní náklady na ovlivněné prostředky*. Tyto prostředky jsou ve vašich předplatných, které mají aspoň jedno doporučení v Advisoru.
1. Advisor vypočítá *maloobchodní náklady na vyhodnocené prostředky*. Tyto prostředky jsou monitorované nástrojem Advisor bez ohledu na to, jestli mají nějaká doporučení nebo ne.
1. Pro každý typ doporučení Advisor vypočítá *dobrý poměr prostředků*. Tento poměr je maloobchodní náklady na ovlivněné prostředky dělené maloobchodními náklady na vyhodnocené prostředky.
1. Poradce používá tři dodatečné váhy na poměr prostředků v pořádku v každé kategorii:

   * Doporučení s větším dopadem jsou vážená těžší než doporučení s nižším dopadem.
   * Prostředky s dlouhotrvajícími doporučeními se budou počítat více oproti vašemu skóre.
   * Prostředky, které odložíte nebo zrušíte v poradci, se úplně ze výpočtu skóre odeberou.

Poradce tento model použije na úrovni kategorie služby Advisor, aby poskytoval hodnocení pro jednotlivé kategorie. **Zabezpečení** používá model [zabezpečeného skóre](../security-center/secure-score-security-controls.md#introduction-to-secure-score) . Jednoduchý průměr vytvoří konečné skóre poradce.

## <a name="advisor-score-faqs"></a>Nejčastější dotazy k skóre Advisoru

### <a name="how-often-is-my-score-refreshed"></a>Jak často se moje skóre aktualizuje?

Vaše skóre se aktualizuje aspoň jednou denně.

### <a name="why-do-some-recommendations-have-the-empty---value-in-the-category-score-impact-column"></a>Proč některá doporučení mají ve sloupci dopad skóre kategorie prázdné hodnoty "-"?

Advisor okamžitě nezahrne nová doporučení nebo doporučení s nedávnými změnami v modelu bodování. Po krátké zkušební době se obvykle několik týdnů zahrne do skóre.

### <a name="why-is-the-cost-score-impact-greater-for-some-recommendations-even-if-they-have-lower-potential-savings"></a>Proč je skóre nákladů větší pro některá doporučení, i když mají nižší potenciální úspory?

Vaše skóre **nákladů** odráží jak vaše potenciální úspory z nevyužitých prostředků, tak i předpokládaný začátek implementace těchto doporučení. Například dodatečná váha se aplikuje na ovlivněné prostředky, které jsou nečinné po delší dobu, a to i v případě, že jsou potenciální úspory nižší.

### <a name="why-dont-i-have-a-score-for-one-or-more-categories-or-subscriptions"></a>Proč nemám skóre pro jednu nebo více kategorií nebo odběrů?

Poradce generuje skóre jenom pro kategorie a odběry, které mají prostředky vyhodnocené pomocí poradce.

### <a name="what-if-a-recommendation-isnt-relevant"></a>Co když doporučení není důležité?

Pokud doporučení z poradce zrušíte, bude vynecháno při výpočtu skóre. Chybějící doporučení také pomáhají Poradce při vylepšování kvality doporučení.

### <a name="why-did-my-score-change"></a>Proč se moje skóre změnila?

Skóre se může změnit, pokud opravíte ovlivněné prostředky tím, že přijmete osvědčené postupy, které doporučuje poradce. Pokud jste vy nebo kdokoli s oprávněními k vašemu předplatnému změnili nebo vytvořili nové prostředky, můžete se také podívat na kolísání skóre. Vaše skóre vychází z poměru prostředků, které mají vliv na náklady vzhledem k celkové ceně všech prostředků.

### <a name="how-does-advisor-calculate-the-retail-cost-of-resources-on-a-subscription"></a>Jak Advisor vypočítá maloobchodní cenu prostředků v rámci předplatného?

Advisor používá tarify průběžných plateb publikované v [cenách Azure](https://azure.microsoft.com/pricing/). Tyto sazby neodrážejí žádné použitelné slevy. Sazby se pak vynásobí množstvím využití v posledním dni, kdy byl prostředek přidělen. Výsledkem vynechání slev z výpočtu nákladů na prostředky je hodnocení, které je srovnatelné v rámci předplatných, klientů a registrací, kde se můžou slevy lišit.

### <a name="do-i-need-to-view-the-recommendations-in-advisor-to-get-points-for-my-score"></a>Potřebuji zobrazit doporučení v poradci, abyste získali body pro své skóre?

No. Vaše skóre odráží, jestli jste přijali osvědčené postupy, které doporučuje poradce, a to i v případě, že tyto osvědčené postupy proaktivně nebudete zobrazovat v Advisoru.

### <a name="does-the-scoring-methodology-differentiate-between-production-and-dev-test-workloads"></a>Rozlišuje se metodologie bodování mezi provozními a testovacími a testovacími úlohami?

Ne, zatím ne. Pokud se tyto prostředky používají pro vývoj a testování a doporučení se nevztahují, můžete ale zrušit doporučení na jednotlivých prostředcích.

### <a name="can-i-compare-scores-between-a-subscription-with-100-resources-and-a-subscription-with-100000-resources"></a>Můžu porovnat skóre mezi předplatným s 100 prostředky a předplatným s 100 000mi prostředky?

Metodologie bodování je určena k řízení počtu prostředků v předplatném a v kombinaci služeb. Předplatná s méně prostředky můžou mít vyšší nebo nižší skóre než předplatná s více prostředky.

### <a name="what-does-it-mean-when-i-see-coming-soon-in-the-score-impact-column"></a>Co to znamená, když se ve sloupci dopad skóre zobrazuje "už brzy"?

Tato zpráva znamená, že doporučení je nové a pracujeme na tom, jak to přináší model skóre Advisor. Až se toto nové doporučení považuje za výpočet skóre, zobrazí se pro vaše doporučení hodnota dopadu skóre.

### <a name="does-my-score-depend-on-how-much-i-spend-on-azure"></a>Záleží na tom, kolik jsem v Azure stráví?

No. Vaše skóre nemusí nutně odrazovat, kolik vám strávíte. Zbytečné útraty budou mít za následek nižší **cenové** skóre.

## <a name="access-advisor-score"></a>Skóre přístupového Advisoru

Skóre Advisoru je ve verzi Public Preview ve Azure Portal. V levém podokně v části **Poradce** najdete informace v tématu **skóre Advisoru**.

![Snímek obrazovky, který zobrazuje vstupní bod skóre pro skóre.](./media/advisor-score-3.png)

## <a name="next-steps"></a>Další kroky

Další informace o doporučeních služby Advisor najdete v těchto tématech:

* [Úvod do Advisoru](advisor-overview.md)
* [Začínáme se službou Advisor](advisor-get-started.md)
* [Doporučení Advisoru týkající se cen](advisor-cost-recommendations.md)
* [Doporučení pro výkon Advisoru](advisor-performance-recommendations.md)
* [Doporučení pro zabezpečení Advisoru](advisor-security-recommendations.md)
* [Doporučení provozní kvality Advisoru](advisor-operational-excellence-recommendations.md)
