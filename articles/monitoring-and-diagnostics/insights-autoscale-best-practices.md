---
title: Osvědčené postupy pro automatické škálování
description: Vzory škálování v Azure Web Apps, sady škálování virtuálního počítače a cloudové služby
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 07/07/2017
ms.author: ancav
ms.component: autoscale
ms.openlocfilehash: e9fc5a4c5d2e23750493cc320dffa380724347fb
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35262439"
---
# <a name="best-practices-for-autoscale"></a>Osvědčené postupy pro automatické škálování
V tomto článku se dozvíte, jaké osvědčené postupy pro škálování v Azure. Azure monitorování škálování se vztahují pouze na [sady škálování virtuálního počítače](https://azure.microsoft.com/services/virtual-machine-scale-sets/), [cloudové služby](https://azure.microsoft.com/services/cloud-services/), a [služby App Service – webové aplikace](https://azure.microsoft.com/services/app-service/web/). Jinými službami Azure použít různé metody škálování.

## <a name="autoscale-concepts"></a>Koncepty škálování
* Prostředek může mít pouze *jeden* nastavení automatického škálování
* Nastavení automatického škálování může mít jeden nebo více profilů a každý profil může mít jeden nebo více pravidel škálování.
* Nastavení automatického škálování škáluje instance vodorovně, což je *out* zvýšením instance a *v* snížením počtu instancí.
  Nastavení automatického škálování má maximální, minimální a výchozí hodnota instancí.
* Úloha automatického škálování vždy přečte přidružené metriky škálování, kontrole, jestli se překročila nakonfigurovanou prahovou hodnotu pro škálování nebo škálování. Můžete zobrazit seznam metrik této škálování můžete škálovat podle na [běžné metriky automatického škálování Azure monitorování](insights-autoscale-common-metrics.md).
* Všechny prahové hodnoty jsou vypočítávány na úrovni instance. Například "škálování odhlašování 1 instancí při průměrná procesoru > 80 %, pokud je počet instancí 2", znamená Škálováním na více systémů, když průměrné využití procesoru ve všech instancích je větší než 80 %.
* Všechny chyby při škálování jsou zaznamenány do protokolu aktivit. Potom můžete nakonfigurovat [výstraha aktivity protokolu](./monitoring-activity-log-alerts.md) tak, aby vám může informování prostřednictvím e-mailu, SMS, webhooku atd., vždy, když dojde selhání škálování.
* Všechny akce úspěšné škálování podobně jsou odeslány do protokolu činnosti. Potom můžete nakonfigurovat výstrahu protokolu aktivitu tak, aby můžete být upozorněni prostřednictvím e-mailu, SMS, webhooků, atd. pokaždé, když je akce škálování úspěšné. Můžete také nakonfigurovat oznámení e-mailu nebo webhooku chcete dostat upozornění pro akce úspěšné škálování prostřednictvím karty oznámení v nastavení automatického škálování.

## <a name="autoscale-best-practices"></a>Osvědčené postupy škálování
Použijte následující osvědčené postupy při používání automatického škálování.

### <a name="ensure-the-maximum-and-minimum-values-are-different-and-have-an-adequate-margin-between-them"></a>Ujistěte se, maximální a minimální hodnoty se liší a mít odpovídající okraje mezi nimi
Pokud máte nastavení, která má minimální = 2, maximální = 2 a aktuální počet instancí je 2, můžete dojít k žádné akci škálování. Zachovat odpovídající okraje mezi počty maximální a minimální instance, které jsou inkluzivní. Škálování vždy škáluje mezi tyto limity.

### <a name="manual-scaling-is-reset-by-autoscale-min-and-max"></a>Ruční škálování při obnovení škálování min a max
Pokud je ručně aktualizovat na hodnotu vyšší nebo nižší než maximální počet instancí, modul škálování automaticky přizpůsobí zpět na minimální (Pokud se níže) nebo maximální (Pokud je vyšší než). Například můžete nastavit rozsah 3 až 6. Pokud máte jeden spuštěnou instanci, modul škálování škáluje na 3 instance při příštím spuštění. Podobně pokud ručně nastavit stupnici na 8 instance, při příštím spuštění automatického škálování se bude škálovat ji zpět 6 instance při příštím spuštění.  Ruční škálování je velmi dočasný, pokud resetujete také pravidel škálování.

### <a name="always-use-a-scale-out-and-scale-in-rule-combination-that-performs-an-increase-and-decrease"></a>Vždy používejte kombinaci Škálováním na více systémů a škálování pravidla, která provádí zvýšení a snížení
Pokud používáte pouze jednou ze součástí sady kombinace, automatické škálování škálování in, který jednotné nebo ve, dokud maximální nebo minimální, je dosaženo.

### <a name="choose-the-appropriate-statistic-for-your-diagnostics-metric"></a>Zvolte odpovídající statistiky pro vaše metrika diagnostiky
Diagnostika metriky, můžete vybrat mezi *průměrná*, *minimální*, *maximální* a *celkový* jako škálovat podle metriky. Nejběžnější Statistika je *průměrná*.

### <a name="choose-the-thresholds-carefully-for-all-metric-types"></a>Zvolte prahové hodnoty pro všechny typy metriky pečlivě
Doporučujeme pečlivě výběr různé prahové hodnoty pro Škálováním na více systémů a škálování in podle praktické situacích.

Jsme *nedoporučujeme* nastavení automatického škálování jako příklady níže s stejné nebo velmi podobné prahové hodnoty pro odhlásit a v podmínkách:

* Zvýšit instancí 1 počet při počtu vláken < = 600
* Snižte instancí 1 počet při počtu vláken > = 600

Podívejme se na příklad co může vést k chování, které se může zdát matoucí. Vezměte v úvahu následující pořadí.

1. Předpokládejme, existují 2 instance na začátku a pak průměrný počet vláken na instanci zvětšuje, aby 625.
2. Škálování horizontálně navýší kapacitu, přidání 3. instance.
3. V dalším kroku předpokládá, že počet průměrná vláken v instanci spadá do 575.
4. Před škálování směrem dolů, bude škálování pokusí odhadnout, jaké konečného stavu, pokud škálovat v. Například 575 x 3 (aktuální počet instancí) = 1,725 nebo 2 (konečný počet instancí při zmenšování) = 862.5 vláken. To znamená, že se okamžitě Škálováním na více systémů znovu i po škálovat, pokud počet průměrná vláken zůstává stejná nebo i spadá pouze malé množství škálování. Ale pokud ji škálovat znovu celý proces by opakujte, vedlo k nekonečné smyčce.
5. Abyste předešli této situaci (dále jen "flapping"), škálování není snižovat vůbec. Místo toho přeskočí a reevaluates podmínku znovu při příštím provede úlohu služby. Protože škálování nezobrazilo fungovat, když byl vlákno průměrný počet 575 to může matou mnoho uživatelů.

Odhad během škálování in slouží ke vyhnout "netřepotá" situace, kdy akce škálování in a škálování průběžně přejděte a zpět. Toto chování ponechat v paměti, když zvolíte stejné prahové hodnoty pro Škálováním na více systémů a.

Doporučujeme vybrat odpovídající okraje mezi horizontální a prahové hodnoty. Jako příklad zvažte následující kombinace lepší pravidlo.

* Zvýšit instancí 1 počet při využití procesoru % > = 80
* Snižte instancí 1 počet při % využití procesoru < = 60

V takovém případě  

1. Předpokládejme, že se začínat 2 instance.
2. Pokud průměrné využití procesoru % napříč instancemi přejde do 80, automatické škálování horizontálně navýší kapacitu, přidání třetí instance.
3. Nyní předpokládejme, že v čase využití procesoru % spadá do 60.
4. Škálování na škálování v pravidlo odhadne konečného stavu by měla pro škálování v. Například 60 x 3 (aktuální počet instancí) = 180 nebo 2 (konečný počet instancí při zmenšování) = 90. Proto škálování není škálování – v vzhledem k tomu, že by musel Škálováním na více systémů ihned. Místo toho přeskočí škálování směrem dolů.
5. Kontroluje, další škálování čas procesoru pokračuje tak, aby spadal do 50. Odhadne znovu - instance 50 x 3 = 150 / 2 instance = 75, což je pod prahovou hodnotou škálování 80, takže se sníží kapacitu, poklesne úspěšně na 2 instance.

### <a name="considerations-for-scaling-threshold-values-for-special-metrics"></a>Důležité informace pro škálování prahové hodnoty pro speciální metriky
 Prahová hodnota speciální metrik, jako je délka metrika úložiště nebo frontou Service Bus, je průměrný počet zpráv, které jsou k dispozici na aktuální počet instancí. Pečlivě zvolte prahová hodnota pro tuto metriku.

Pojďme doložit jej příklad zajistit, že rozumíte lepší chování.

* Zvýšit instancí podle počtu 1, když počet zprávy fronty úložiště > = 50
* Snižte instancí 1 počet při fronty úložiště zpráv počet < = 10

Vezměte v úvahu následující sekvenci:

1. Existují 2 instance fronty úložiště.
2. Zachovat zprávy přicházející a při kontrole fronty úložiště, celkový počet přečte 50. Může předpokládat, že tento škálování by se měl spustit akci škálování. Ale Všimněte si, že je stále 50/2 = 25 zprávy na jednu instanci. Škálováním na více systémů, takže nedojde. První Škálováním na více systémů provést celkový počet zpráv ve frontě úložiště by měl být 100.
3. V dalším kroku předpokládá, že celkový počet zpráv dosáhne 100.
4. 3. instance fronty úložiště se přidá kvůli akce škálování.  Další akce Škálováním na více systémů se neprovede, dokud celkový počet zpráv ve frontě nedosáhne 150, protože 150/3 = 50.
5. Nyní získá menší počet zpráv ve frontě. S 3 instancí, je první škálování akcí se stane, když celkový počet zpráv ve všech frontách přidat až 30, protože 30/3 = 10 zprávy každou instanci, která je prahová hodnota měřítka v.

### <a name="considerations-for-scaling-when-multiple-profiles-are-configured-in-an-autoscale-setting"></a>Důležité informace pro škálování při více profilů konfigurované v nastavení automatického škálování
V nastavení automatického škálování, můžete výchozí profil, který se použije vždy bez závislost na plán nebo čas, nebo můžete opakovaně profil nebo po stanovenou dobu s rozsahem datum a čas.

Při škálování služby je zpracuje, vždy kontroluje v následujícím pořadí:

1. Opravené datum profilu
2. Opakované profilu
3. Výchozí profil ("vždy")

Pokud je splněna podmínka profilu, nekontroluje škálování další podmínky profil pod ním. Škálování zpracovává jenom jeden profil najednou. To znamená, že pokud chcete, aby zahrnovala podmínku zpracování z profilu nižší úrovně, je nutné zahrnout tato pravidla také aktuální profil.

Pojďme si to pomocí příklad:

Následující obrázek ukazuje na nastavení automatického škálování s minimální instancí výchozí profil = 2 a maximální instancí = 10. V tomto příkladu pravidla jsou nakonfigurovány pro škálované při počet zpráv ve frontě je větší než 10 škálování in a když počet zpráv ve frontě je menší než 3. Prostředek, takže teď můžete škálovat mezi instancemi 2 až 10.

Kromě toho je profil opakovaně nastavit pro pondělí. Nastavení pro minimální instance = 3 a maximální instancí = 10. To znamená v pondělí, první čas škálování kontroluje pro tuto podmínku, pokud je počet instancí 2, lze škálovat na nové minimálně 3. Tak dlouho, dokud škálování nadále najít tuto podmínku profil shodná (pondělí), pouze zpracovává bázi procesoru Škálováním na více systémů nebo v nakonfigurovaná pravidla pro tento profil. V tomto okamžiku nekontroluje se pro délku fronty. Ale pokud také chcete podmínku délka fronty, která má být zaškrtnuto, by měla obsahovat tato pravidla z výchozí profil také váš profil pondělí.

Podobně když škálování přepne zpět na výchozí profil, nejdřív zkontroluje, pokud jsou splněny podmínky minimální a maximální. Pokud se počet instancí v době 12, přizpůsobí se v 10, maximum povolené pro výchozí profil.

![nastavení automatického škálování](./media/insights-autoscale-best-practices/insights-autoscale-best-practices-2.png)

### <a name="considerations-for-scaling-when-multiple-rules-are-configured-in-a-profile"></a>Důležité informace pro škálování při nastavení víc pravidel v profilu
Existují případy, kdy budete muset nastavit více pravidel v profilu. Služby používají používá následující sadu pravidel škálování více pravidel jsou nastavena.

Na *škálovat*, škálování spustí, pokud je splněna žádné pravidlo.
Na *škálování v*, automatické škálování vyžadují všechna pravidla, které musí být splněné.

Pro ilustraci, Předpokládejme, že máte následující pravidla 4 škálování:

* Pokud procesoru < 30 % škálování v 1
* Pokud škálování v 1 paměti < 50 %
* Pokud > 75 % využití procesoru, horizontální o 1
* Pokud paměti > 75 %, horizontální o 1

Potom se provede těchto kroků:

* Pokud je 76 % procesoru a paměti je 50 %, jsme Škálováním na více systémů.
* Pokud je 50 % využití procesoru a paměti je % 76 jsme Škálováním na více systémů.

Na druhé straně, pokud využití procesoru činí 25 % a paměti je 51 % automatického škálování nemá **není** škálování v. Za účelem škálování v, využití procesoru musí být 29 % a paměť 49 %.

### <a name="always-select-a-safe-default-instance-count"></a>Vždy vybrat bezpečné výchozí počet instancí
Výchozí počet instancí je důležité automatické škálování škáluje služby do tohoto počtu metriky nejsou k dispozici. Proto vyberte výchozí počet instancí, který je bezpečný pro úlohy.

### <a name="configure-autoscale-notifications"></a>Konfigurace automatického škálování oznámení
Škálování bude odeslána do protokolu činnosti, pokud dojde k některé z následujících podmínek:

* Škálování vydává operace škálování
* Škálování služby úspěšném dokončení akce škálování
* Škálování služby se nepodaří akci škálování.
* Metriky nejsou k dispozici pro škálování služby k provedení rozhodnutí škálování.
* Metriky jsou k dispozici (obnovení) znovu k provedení rozhodnutí škálování.

Můžete taky výstrahu protokolu aktivit k monitorování stavu modulu škálování. Zde jsou příklady [vytvořit aktivity protokolu výstrahy monitorovat všechny operace škálování modul vaše předplatné](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-alert) nebo [vytvořit aktivity protokolu výstrahy monitorovat všechny neúspěšné automatické škálování měřítka ve / škálovat operací na vaše předplatné](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-failed-alert).

Kromě použití aktivity protokolu výstrahy, můžete také nakonfigurovat oznámení e-mailu nebo webhooku chcete dostat upozornění pro akce úspěšné škálování prostřednictvím karty oznámení v nastavení automatického škálování.

## <a name="next-steps"></a>Další kroky
- [Vytvoření aktivity protokolu výstrahy monitorovat všechny operace škálování modul vaše předplatné.](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-alert)
- [Vytvoření aktivity protokolu výstrahy monitorovat všechny neúspěšné automatické škálování měřítka ve / škálovat operací na vaše předplatné](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-failed-alert)
