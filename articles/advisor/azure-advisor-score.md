---
title: Optimalizace úloh Azure pomocí skóre služby Advisor
description: Využijte skóre služby Advisor pro maximum z Azure
ms.topic: article
ms.date: 09/09/2020
ms.openlocfilehash: 29d8480f501a78c1668b52034f439f998419f9d9
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91335615"
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
* **Skóre kategorií služby Advisor** v jednotlivých doporučeních vám řekne, u kterých nezpracovaných doporučení vylepší vaše skóre. Tyto hodnoty odrážejí váhu doporučení i naši předpokládanou snadnou implementaci, abyste se ujistili, že můžete získat co nejvíc hodnot s vaším časem a zároveň vám pomůže s určováním priorit.
* **Skóre kategorií – dopad** jednotlivých doporučení, které vám pomůžou určit prioritu vašich nápravných akcí u každé kategorie

Příspěvek každého doporučení na skóre vaší kategorie se na stránce hodnocení služby Advisor v Azure Portal zobrazuje jasně. Jednotlivé skóre kategorií můžete zvýšit podle procenta, které je uvedené ve sloupci dopad skóre kategorie. Tato hodnota odráží jak váhu doporučení v rámci této kategorie, tak předpokládanou snadnou implementaci pro řešení potenciálních plodů s nízkým zavěšením. Zaměření na doporučení s největším dopadem na skóre vám pomůže s tím, jak budete mít čas.  

![Dopad skóre Advisoru](./media/advisor-score-2.png)

V případě, že některá doporučení poradce nejsou relevantní pro jednotlivé prostředky, můžete tato doporučení odložit nebo zrušit a při příští aktualizaci budou vyloučeny ze výpočtu skóre. Poradce taky použije tento vstup jako další zpětnou vazbu ke zlepšení modelu.

## <a name="how-is-advisor-score-calculated"></a>Jak se počítá skóre Advisoru?
Advisor zobrazuje skóre kategorií a celkové skóre Advisoru jako procenta. Skóre 100% v jakékoli kategorii znamená všechny prostředky, které *Advisor vyhodnotil*, podle osvědčených postupů, které doporučuje poradce. Na druhém konci spektra znamená skóre 0%, že žádný z vašich prostředků vyhodnocený poradcem, podle doporučení poradce. 
**Každá z pěti kategorií má nejvyšší možná skóre 100.** Vaše celkové skóre služby Advisor se vypočítá jako součet každé platné skóre kategorie, dělené součtem nejvyššího možného skóre ze všech platných kategorií. U většiny předplatných to znamená, že služba Advisor přidá skóre z každé kategorie a rozděluje 500. **Jednotlivé skóre kategorií se ale vypočítávají jenom v případě, že použijete prostředky, které jsou vyhodnocené pomocí poradce.**

**Příklad výpočtu skóre Advisoru**
* Skóre jednoho předplatného: Jedná se o jednoduchý význam všech hodnocení kategorií služby Advisor pro vaše předplatné. Pokud má kategorie poradce skóre as-cost = 73, Reliabilit = 85, provozní excelence = 77, výkon = 100; **skóre Advisor** by byl (73 + 85 + 77 + 100)/(4x100) = 0,84 nebo 84%.
* Skóre více předplatných: Pokud je vybráno více předplatných, je celkový počet vygenerovaných výsledků Advisory vážený agregovanou kategorií. V tomto příkladu se skóre každé kategorie poradce agreguje na základě prostředků spotřebovaných předplatnými. Jakmile budeme počítat vážené agregované kategorie, máme jednoduchý význam, který vám poskytne celkové skóre předplatných. 


### <a name="scoring-methodology"></a>Metodologie bodování: 
Výpočet skóre Advisoru se dá shrnout ve čtyřech krocích:
1. Advisor vypočítá **maloobchodní náklady na ovlivněné prostředky**, které jsou prostředky v předplatných, které mají alespoň jedno doporučení v Advisoru.
2. Služba Advisor vypočítá **maloobchodní náklady na vyhodnocené prostředky**, které jsou prostředky monitorovanými poradcem, bez ohledu na to, zda mají nějaká doporučení nebo ne. 
3. Pro každý typ doporučení Advisor vypočítá **dobrý poměr prostředků**, což jsou maloobchodní náklady na ovlivněné prostředky dělené maloobchodními náklady na vyhodnocené prostředky.
4. Poradce používá tři dodatečné váhy na poměr prostředků v pořádku v každé kategorii:
  * Doporučení s větším dopadem jsou vážená těžší, než ta s nižším dopadem.
  * Prostředky s dlouhotrvajícími doporučeními se budou počítat více oproti vašemu skóre.
  * Prostředky, které odložíte nebo zrušíte v poradci, se úplně ze výpočtu skóre odeberou. 
    
Advisor tento model použije na úrovni kategorie služby Advisor (zabezpečení používá model [zabezpečeného hodnocení](https://docs.microsoft.com/azure/security-center/secure-score-security-controls#introduction-to-secure-score) ), který poskytuje skóre pro každou kategorii a další jednoduchý průměr vytvoří konečné skóre poradce.


## <a name="advisor-score-faq"></a>Nejčastější dotazy k skóre Advisoru
* **Jak často se moje skóre aktualizuje?**
Vaše skóre se aktualizuje aspoň jednou denně. 
* **Proč některá doporučení mají ve sloupci dopad skóre kategorie prázdné hodnoty "-"?** Advisor okamžitě nezahrnuje nová doporučení nebo ta s nedávnými změnami v modelu hodnocení. Po krátké zkušební době se většinou několik týdnů zahrne do skóre. 
* **Proč je skóre nákladů větší pro některá doporučení, i když mají nižší potenciální úspory?**
Vaše skóre nákladů odráží jak vaše potenciální úspory z nevyužitých prostředků, tak i předpokládaný začátek implementace těchto doporučení. Například dodatečná váha se aplikuje na ovlivněné prostředky, které jsou nečinné po delší dobu, a to i v případě, že jsou potenciální úspory nižší. 
* **Proč nemám skóre pro jednu nebo více kategorií nebo odběrů?**
Poradce vygeneruje skóre jenom pro kategorie a odběry, které mají prostředky vyhodnocené pomocí poradce.
* **Co když doporučení není relevantní?**
Pokud doporučení z poradce zrušíte, bude vynecháno při výpočtu skóre. Chybějící doporučení také pomáhá Advisoru zlepšit kvalitu doporučení.
* **Proč se moje skóre změnila?** Skóre se může změnit, pokud opravíte ovlivněné prostředky tím, že přijmete osvědčené postupy, které doporučuje poradce. Pokud jste vy nebo kdokoli s oprávněním k vašemu předplatnému změnili nebo vytvořili nové prostředky, můžete se také podívat na kolísání skóre, protože vaše skóre je založeno na poměru prostředků ovlivněných náklady vzhledem k celkovému počtu nákladů na všechny prostředky.
* **Jak Advisor vypočítá maloobchodní cenu prostředků v rámci předplatného?**
Služba Advisor používá sazby za průběžné platby, které jsou publikovány na stránce s cenami Azure.com, což neodráží žádné platné slevy vynásobené množstvím využití v posledním dni přidělení prostředku. Výsledkem vynechání slev z výpočtu nákladů na prostředky je skóre služby Advisor v rámci předplatných, klientů a registrací, kde se můžou slevy lišit. 
* **Potřebuji zobrazit doporučení v poradci, abyste získali body pro své skóre?** No. Vaše skóre odráží, jestli jste přijali osvědčené postupy, které doporučuje poradce, a to i v případě, že tyto osvědčené postupy proaktivně nebudete zobrazovat v Advisoru.
* **Rozlišuje se metodologie bodování mezi provozními a testovacími a testovacími úlohami?**
Ne, zatím ne, ale pokud se tyto prostředky používají pro vývoj a testování, můžete doporučení na jednotlivé prostředky zavřít, ale doporučení se nevztahují.
* **Můžu porovnat skóre mezi předplatným s 100 prostředky a předplatným s 100 000mi prostředky?**
Metodologie bodování je navržená tak, aby se mohla řídit množství prostředků v předplatném a v kombinaci služeb, takže předplatné s menším množstvím prostředků může mít vyšší nebo nižší skóre než předplatná s více prostředky. 
* **Co to znamená, když se ve sloupci dopad skóre zobrazuje "už brzy"?**
To znamená, že se jedná o nové doporučení a pořád pracujeme na tom, jak to přináší náš model skóre Advisoru. Jakmile se toto nové doporučení považuje za výpočet skóre, zobrazí se pro vaše doporučení hodnota dopadu skóre.  
* **Záleží na tom, kolik jsem v Azure stráví?**
Ne, vaše skóre nemusí nutně odrazovat, kolik vám strávíte, a zbytečné útraty budou mít za následek nižší cenové skóre.

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
