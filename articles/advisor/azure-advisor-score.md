---
title: Optimalizace úloh Azure pomocí skóre služby Advisor
description: Využijte skóre služby Advisor pro maximum z Azure
ms.topic: article
ms.date: 09/09/2020
ms.openlocfilehash: 720a2b358e35d776a7233452eee2bd69b521654f
ms.sourcegitcommit: 814778c54b59169c5899199aeaa59158ab67cf44
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/13/2020
ms.locfileid: "90056230"
---
# <a name="optimize-azure-workloads-using-advisor-score"></a>Optimalizace úloh Azure pomocí skóre služby Advisor

## <a name="introduction-to-advisor-score"></a>Úvod do skóre Advisoru

Azure Advisor poskytuje doporučení pro osvědčené postupy pro vaše úlohy. Tato doporučení jsou přizpůsobená a umožňují vám tyto akce:
* Zlepšení stav vašich úloh a optimalizace nasazení Azure
* Proaktivně Zabraňte nejdůležitějším problémům pomocí osvědčených postupů
* Vyhodnotit vaše úlohy Azure proti pěti pilířům [Microsoft Azurech dobře navržených architektur](https://docs.microsoft.com/azure/architecture/framework/)

Jako základní funkce Advisoru se navrhuje **hodnocení Advisoru** , které vám pomůže dosáhnout těchto cílů efektivně a efektivně. 

Abyste mohli Azure využít na maximum, je důležité pochopit, kde se nacházíte v rámci své cesty optimalizace zatížení, které služby a prostředky jsou dobře spotřebované a které nejsou. Dále budete chtít zjistit, jak upřednostnit vaše akce na základě doporučení a maximalizovat tak výsledek. Je také důležité sledovat pokrok, který v této cestě optimalizace vytváříte, a nahlásit ho. Díky **hodnocení Advisoru**můžete všechny tyto věci snadno udělat pomocí našeho nového prostředí gamifikační. V rámci vašeho přizpůsobeného cloudového konzultanta Azure Advisor průběžně vyhodnocuje telemetrii využití a konfiguraci prostředků, aby kontroloval osvědčené postupy pro obor. Poradce pak agreguje své závěry do jednoho skóre, takže můžete na první pohled zjistit, jestli máte potřebné kroky k vytváření spolehlivých, zabezpečených a nákladově efektivních řešení. Skóre služby Advisor se skládá z celkového skóre, které může být dále rozděleno na pět kategorií, jednu pro každou kategorii Azure Advisor, která představuje pět pilířů v dobře navrženém rozhraní. Průběh můžete sledovat v průběhu času zobrazením celkového skóre a skóre kategorie pomocí denního, týdenního a měsíčního trendu a můžete nastavit srovnávací testy, které vám pomůžou dosáhnout vašich cílů. 

 ![Prostředí skóre Advisoru](./media/advisor-score-1.png)

## <a name="how-to-consume-advisor-score"></a>Jak využívat skóre Advisoru
Advisor v procentech zobrazuje celkové skóre a rozpisy služby Advisor pro kategorie poradce. Skóre 100% v jakékoli kategorii znamená, že všechny prostředky vyhodnocené poradcem dodržují osvědčené postupy, které doporučuje poradce. Na druhém konci spektra znamená skóre 0%, že žádný z vašich prostředků vyhodnocený poradcem podle doporučení poradce. Pomocí těchto zrn skóre můžete snadno dosáhnout pod tokem:
* **Skóre Advisoru** , které vám pomůže určit, jak vaše zatížení nebo odběry probíhají na základě skóre Advisoru. Můžete si také prohlédnout historické trendy a porozumět tomu, co trend je.
* **Hodnocení kategorií Advisoru** , které vám pomůže pochopit, které kategorie vyžadují větší pozornost a pomáhá při stanovení priorit
* **Potenciální nárůst skóre** každého doporučení, které vám pomůžou určit prioritu vašich nápravných akcí u každé kategorie

Příspěvek každého doporučení na vaše skóre se na stránce Přehled v Azure Portal zobrazí jasně. Můžete zvýšit skóre tím, že přijmete osvědčené postupy a můžete určit prioritu doporučení, která mají největší **možné skóre** , aby se zvýšil průběh s časem, který máte.  

![Dopad skóre Advisoru](./media/advisor-score-2.png)

Vzhledem k tomu, že metodologie bodování Advisoru používá pro dražších a dlouhodobě vydaných doporučení navíc váhu, můžete oprava prostředky s nejvyššími prodejními náklady jako první. V případě, že některá doporučení služby Advisor nejsou relevantní pro jednotlivé prostředky, můžete tato doporučení odstranit, abyste je vyloučili ze výpočtu skóre a mohli odeslat zpětnou vazbu na službu Advisor a vylepšit doporučení. 

## <a name="how-is-advisor-score-calculated"></a>Jak se počítá skóre Advisoru?
Advisor zobrazuje skóre kategorií a celkové skóre Advisoru jako procenta. Skóre 100% v jakékoli kategorii znamená všechny prostředky, které *Advisor vyhodnotil*, podle osvědčených postupů, které doporučuje poradce. Na druhém konci spektra znamená skóre 0%, že žádný z vašich prostředků vyhodnocený poradcem, podle doporučení poradce. 
**Každá z pěti kategorií má nejvyšší možná skóre 100.** Vaše celkové skóre služby Advisor se vypočítá jako součet každé platné skóre kategorie, dělené součtem nejvyššího možného skóre ze všech platných kategorií. U většiny předplatných to znamená, že služba Advisor přidá skóre z každé kategorie a rozděluje 500. **Jednotlivé skóre kategorií se ale vypočítávají jenom v případě, že použijete prostředky, které jsou vyhodnocené pomocí poradce.**

### <a name="scoring-methodology"></a>Metodologie bodování: 
Výpočet skóre Advisoru se dá shrnout ve čtyřech krocích:
1. Advisor vypočítá **denní maloobchodní náklady na ovlivněné prostředky**, což jsou prostředky v předplatných, které mají alespoň jedno doporučení v Advisoru.
2. Advisor vypočítá **denní maloobchodní náklady na vyhodnocené prostředky**, které jsou prostředky monitorovanými poradcem, ať už mají doporučení nebo ne. 
3. Pro každý typ doporučení Advisor vypočítá **dobrý poměr prostředků**, což jsou náklady na ovlivněné prostředky dělené náklady na vyhodnocené prostředky.
4. Poradce používá tři dodatečné váhy na poměr prostředků v pořádku v každé kategorii:
* Doporučení s větším dopadem jsou vážená těžší, než ta s nižším dopadem.
* Prostředky s dlouhotrvajícími doporučeními se budou počítat více oproti vašemu skóre.
* Prostředky, které zrušíte v Advisoru, se úplně ze výpočtu skóre odeberou. 
    
Advisor tento model použije na úrovni kategorie služby Advisor (zabezpečení používá model [zabezpečeného hodnocení](https://docs.microsoft.com/azure/security-center/secure-score-security-controls#introduction-to-secure-score) ), který poskytuje skóre pro každou kategorii a další jednoduchý průměr vytvoří konečné skóre poradce.


## <a name="advisor-score-faq"></a>Nejčastější dotazy k skóre Advisoru
* **Jak často se moje skóre aktualizuje?**
Vaše skóre se aktualizuje aspoň jednou denně. 
* **Potřebuji zobrazit doporučení v Advisoru, abyste získali místo pro svoje skóre?**
No. Vaše skóre odráží, jestli jste přijali osvědčené postupy, které doporučuje poradce, a to i v případě, že tato doporučení nikdy neuvidíte v poradci a aktivně přijmou osvědčené postupy.  
* **Jak Poradce počítá denní maloobchodní náklady na prostředky v rámci předplatného?**
Služba Advisor používá sazby *za průběžné platby* , které jsou publikovány na stránce s cenami Azure.com, což neodráží žádné platné slevy vynásobené množstvím využití v posledním dni přidělení prostředku. Výsledkem vynechání slev z výpočtu nákladů na prostředky je skóre služby Advisor v rámci předplatných, klientů a registrací, kde se můžou slevy lišit. 
* **Co když doporučení není relevantní?**
Pokud doporučení z poradce zrušíte, bude vynecháno při výpočtu skóre. Chybějící doporučení také pomáhá Advisoru zlepšit kvalitu doporučení.
* **Proč se moje skóre změnila?** Skóre se může změnit, pokud opravíte ovlivněné prostředky tím, že přijmete osvědčené postupy, které doporučuje poradce. Pokud jste vy nebo kdokoli s oprávněním k vašemu předplatnému změnili nebo vytvořili nové prostředky, můžete se také podívat na kolísání skóre, protože vaše skóre je založeno na poměru prostředků ovlivněných náklady vzhledem k celkovému počtu nákladů na všechny prostředky.
* **Záleží na tom, kolik jsem v Azure stráví?**
No. Skóre je navrženo tak, aby bylo možné řídit velikost předplatného a kombinační služby. 
* **Rozlišuje se metodologie bodování mezi provozními a testovacími a testovacími úlohami?**
Ne, zatím ne, ale pokud se tyto prostředky používají pro vývoj a testování, můžete doporučení na jednotlivé prostředky zavřít, ale doporučení se nevztahují.
* **Můžu porovnat skóre mezi předplatným s 100 prostředky a předplatným s 100 000mi prostředky?**
Metodologie bodování je navržená tak, aby se mohla řídit množství prostředků v předplatném a v kombinaci služeb, takže předplatné s menším množstvím prostředků může mít vyšší nebo nižší skóre než předplatná s více prostředky. 

## <a name="how-to-access-advisor"></a>Jak získat přístup k Advisoru
Skóre Advisoru je ve verzi Public Preview v Azure Portal. Musíte přejít na část poradce a v levém navigačním podokně se zobrazí skóre Advisoru jako druhá položka nabídky. 

![Bod vstupu skóre Advisoru](./media/advisor-score-3.png)

## <a name="next-steps"></a>Další kroky

Další informace o doporučeních služby Advisor najdete v těchto tématech:
* [Úvod do Advisoru](advisor-overview.md)
* [Začínáme se službou Advisor](advisor-get-started.md)
* [Doporučení Advisoru týkající se cen](advisor-cost-recommendations.md)
* [Doporučení pro výkon Advisoru](advisor-performance-recommendations.md)
* [Doporučení pro zabezpečení Advisoru](advisor-security-recommendations.md)
* [Doporučení provozní kvality Advisoru](advisor-operational-excellence-recommendations.md)
