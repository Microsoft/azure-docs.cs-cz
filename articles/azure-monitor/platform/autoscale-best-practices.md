---
title: Osvědčené postupy pro automatické škálování
description: Vzory automatického škálování v Azure Web Apps, Virtual Machine Scale sets a cloudové služby
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 07/07/2017
ms.author: ancav
ms.component: autoscale
ms.openlocfilehash: b34d5f061eae297b635e2249143240668a4c2b08
ms.sourcegitcommit: e37fa6e4eb6dbf8d60178c877d135a63ac449076
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/13/2018
ms.locfileid: "53326040"
---
# <a name="best-practices-for-autoscale"></a>Osvědčené postupy pro automatické škálování
Automatické škálování služby Azure Monitor se týká pouze [Virtual Machine Scale Sets](https://azure.microsoft.com/services/virtual-machine-scale-sets/), [Cloud Services](https://azure.microsoft.com/services/cloud-services/), [App Service – Web Apps](https://azure.microsoft.com/services/app-service/web/), a [služby API Management](https://docs.microsoft.com/azure/api-management/api-management-key-concepts).

## <a name="autoscale-concepts"></a>Koncepty automatického škálování
* Prostředek může mít pouze *jeden* nastavení automatického škálování
* Nastavení automatického škálování může mít jednu nebo více profilů a každý profil může mít jeden nebo více pravidel automatického škálování.
* Nastavení automatického škálování se škáluje instance vodorovně, což je *si* zvýšením počtu instancí a *v* snížením počtu instancí.
  Nastavení automatického škálování má maximální, minimální a výchozí hodnota instancí.
* Úloha automatického škálování vždy přečte související metriky pro škálování, kontrole, jestli se má, kterou překřížilo nakonfigurovanou prahovou hodnotu pro horizontální navýšení kapacity nebo škálování na méně instancí. Můžete zobrazit seznam metrik tohoto automatického škálování můžete škálovat na [běžné metriky automatického škálování Azure monitoru](autoscale-common-metrics.md).
* Všechny prahové hodnoty se vypočítávají na úrovni instance. Například "horizontální navýšení kapacity o jednu instanci když průměrné využití procesoru > 80 % když je počet instancí 2", znamená to horizontální navýšení kapacity, když průměrné využití procesoru napříč všemi instancemi je větší než 80 %.
* Všechny chyby automatického škálování jsou protokolovány do protokolu aktivit. Potom můžete nakonfigurovat [upozornění protokolu aktivit](./../../azure-monitor/platform/activity-log-alerts.md) tak, aby můžete být upozorněni prostřednictvím e-mailu, SMS nebo webhook pokaždé, když dojde chybě automatického škálování.
* Všechny akce úspěšné škálování podobně, jsou odeslány do protokolu aktivit. Upozornění protokolu aktivit se potom můžete nakonfigurovat tak, aby můžete být upozorněni prostřednictvím e-mailu, SMS nebo webhook vždycky, když je akce úspěšná automatického škálování. Můžete také nakonfigurovat e-mailu nebo webhooková oznámení v oznámení pro akce úspěšné škálování prostřednictvím karty oznámení v nastavení automatického škálování.

## <a name="autoscale-best-practices"></a>Osvědčené postupy automatického škálování
Použijte následující osvědčené postupy při použití automatického škálování.

### <a name="ensure-the-maximum-and-minimum-values-are-different-and-have-an-adequate-margin-between-them"></a>Zajištění maximální a minimální hodnoty se liší a mít odpovídající okraje mezi nimi
Pokud máte nastavení, které má minimálně = 2, maximální = 2 a aktuální počet instancí je 2, můžete dojít k žádné akci škálování. Zachovejte adekvátní okraje mezi počty maximální a minimální instance, které jsou (včetně). Automatické škálování vždy mezi tyto limity škálování.

### <a name="manual-scaling-is-reset-by-autoscale-min-and-max"></a>Ruční škálování je resetovat pomocí automatického škálování min a max
Pokud je ručně aktualizovat na hodnotu nad nebo pod maximální počet instancí, modul automatického škálování se automaticky škáluje zpět do (Pokud se níže) minimální nebo maximální počet (Pokud je vyšší než). Například nastavte rozsah dlouhý 3 až 6. Pokud máte jednu běžící instanci, modul automatického škálování se škáluje na tři instance při příštím spuštění. Podobně pokud ručně nastavíte škálování na osmi instancí, při dalším spuštění automatického škálování se bude škálovat ji zpět do šesti instance při příštím spuštění.  Ruční škálování je dočasný, není-li obnovit také pravidla automatického škálování.

### <a name="always-use-a-scale-out-and-scale-in-rule-combination-that-performs-an-increase-and-decrease"></a>Vždy používejte kombinaci pravidlo pro horizontální navýšení kapacity a škálování na méně instancí, který provádí zvýšení a snížení
Pokud používáte pouze jednu část kombinace, automatické škálování pouze provést akci v jednom směru (scale out nebo in), dokud nedosáhne maximální nebo minimální OST počtů definovány v profilu. Tento způsob není ideální, ideálně chcete vertikálně navýšit kapacitu v časech vysokého využití, aby se zajistila dostupnost prostředku. Podobně čas od času nízké využití chcete, aby váš prostředek můžete vertikálně snížit kapacitu, takže byste mohli realizovat úspory nákladů.

### <a name="choose-the-appropriate-statistic-for-your-diagnostics-metric"></a>Zvolte odpovídající Statistika metriky diagnostiky
Diagnostických metrik, můžete vybrat mezi *průměrné*, *minimální*, *maximální* a *celkový* jako ke škálování podle metriky. Je nejběžnější statistiky *průměrné*.

### <a name="choose-the-thresholds-carefully-for-all-metric-types"></a>Zvolte prahové hodnoty pečlivě pro všechny typy metriky
Doporučujeme pečlivou volbou různé prahové hodnoty pro horizontální navýšení kapacity a škálování na méně instancí podle praktické situacích.

Jsme *není vhodné řešit podobné* nastavení automatického škálování, jako jsou v příkladech níže se stejnou nebo velice podobných prahové hodnoty pro navýšení nebo snížení podmínky:

* Instance se tak zvýší o 1 při počítat počet vláken < = 600
* Snížit instancí o 1 při počítat počet vláken > = 600

Pojďme se podívat na příklad toho, co může vést k chování, které možná připadat složité. Vezměte v úvahu následující posloupnosti.

1. Předpokládejme, existují dvě instance na začátku a pak se 625 zvětšuje průměrný počet vláken na instanci.
2. Automatické škálování se škálovat přidáním třetí instance.
3. V dalším kroku se předpokládá, že počet průměrné vláken v instanci spadá do 575.
4. Před škálování, automatické škálování se pokusí odhadnout, co koncový stav bude, pokud ji škálovat v. Například 575 x 3 (aktuální počet instancí) = 1,725 / 2 (konečný počet instancí, když kapacitu vertikálně snížit) = 862.5 vlákna. To znamená, že automatické škálování byste museli okamžitě horizontální navýšení kapacity znovu i po škálovat, pokud počet vláken průměrné zůstává stejná nebo i spadá pouze malé množství. Ale pokud vertikálně navýšit znovu, celý proces by opakovat, což vede k nekonečné smyčce.
5. Abyste předešli této situaci (dále jen "flapping"), automatického škálování není snižovat vůbec. Místo toho přeskočí a přehodnotí podmínka znovu při příštím provádí úlohy služby. Protože automatické škálování se nezobrazilo fungovat, když počet průměrné vláken byl 575 to můžete zaměnit mnoho lidí.

Odhad při škálování na slouží vyhnout "netřepotá" situacích, kdy akce škálování na méně instancí a horizontální navýšení kapacity průběžně přejít vpřed a zpět. Toto chování zachovejte v paměti, když zvolíte stejný prahové hodnoty pro horizontální navýšení kapacity a.

Doporučujeme, abyste zvolíte odpovídající okraj, mezi úpravy rozsahu a prahové hodnoty. Jako příklad zvažte následující kombinace lepší pravidlo.

* Instance se tak zvýší o 1 počítat při využití procesoru % > = 80
* Snížit instancí o 1 počítat při využití procesoru % < = 60

V tomto případě  

1. Předpokládejme, že existují začít s 2 instance.
2. Pokud průměrné využití procesoru % napříč instancemi přejde na 80, automatického škálování horizontálně navýší kapacitu, přidání třetí instance.
3. Nyní předpokládejme, že v čase využití procesoru % spadá do 60.
4. Pravidlo automatického škálování pro škálování na méně instancí odhady koncový stav, kdyby se škálování na méně instancí. Třeba 60 x 3 (aktuální počet instancí) = 180 / 2 (konečný počet instancí, když kapacitu vertikálně snížit) = 90. Takže automatického škálování není škálování na méně instancí vzhledem k tomu, že by musel horizontální navýšení kapacity ihned. Místo toho přeskočí škálování.
5. Kontroly, automatického škálování další čas procesoru i nadále spadají do 50. Odhadne znovu – instance 50 x 3 = 150 / 2 instancí = 75, což nedosahuje prahové hodnoty 80, horizontální navýšení kapacity, provede se škálování v úspěšně 2 instance.

### <a name="considerations-for-scaling-threshold-values-for-special-metrics"></a>Důležité informace pro změnu velikosti prahové hodnoty pro speciální metriky
 Prahová hodnota pro speciální metriky, jako je délka metrika úložiště nebo fronty Service Bus, je průměrný počet zpráv, které jsou k dispozici na aktuální počet instancí. Pečlivě určit prahovou hodnotu pro tuto metriku.

Pojďme ho ilustrují příklad k zajištění, že rozumíte lepší chování.

* Instance zvýšit počet 1 při počet zpráv fronty úložiště > = 50
* Když počet zpráv fronty úložiště snížit instancí podle počtu 1 < = 10

Vezměte v úvahu následující posloupnost:

1. Existují dvě instance fronty úložiště.
2. Zachovat přicházející zprávy a při kontrole fronty úložiště, celkový počet přečte 50. Můžete předpokládat, že tohoto automatického škálování by měl spustit akci škálování na víc systémů. Mějte však na paměti, že je stále 50/2 = 25 zpráv na instanci. Horizontální navýšení kapacity, takže nebude fungovat. Pro první Škálováním nestane celkový počet zpráv ve frontě úložiště by měl být 100.
3. V dalším kroku se předpokládá, že celkový počet zpráv dosáhne 100.
4. 3. instanci fronty úložiště se přidá z důvodu akce škálování na víc systémů.  Další akce škálování se neprovede, dokud celkový počet zpráv ve frontě dosáhne 150, protože 150/3 = 50.
5. Nyní získá menší počet zpráv ve frontě. Tři instancemi první akci škálování na méně instancí se stane, když celkový počet zpráv ve všech frontách přidat až pro 30, protože 30/3 = 10 zpráv na instanci, která je prahová hodnota škálování na méně instancí.

### <a name="considerations-for-scaling-when-multiple-profiles-are-configured-in-an-autoscale-setting"></a>Důležité informace pro změnu velikosti při více profilů jsou nakonfigurované v nastavení automatického škálování
V nastavení automatického škálování, můžete použít výchozí profil, který se použije vždy bez závislost na plán nebo času, nebo můžete zvolit opakované profil nebo na pevné období s rozsahem data a času.

Když automatické škálování služby zpracovává jejich, vždy kontroluje v následujícím pořadí:

1. Pevné datum profilu
2. Opakované profilu
3. Výchozí profil ("Always")

Pokud je splněna podmínka profilu, automatické škálování neprovede kontrolu další podmínky profilu pod ní. Automatické škálování zpracovává současně pouze jeden profil. To znamená, že pokud chcete také zahrnout podmínku zpracování z profilu nižší úroveň, je nutné zahrnout pravidla i v aktuální profil.

Pojďme se podívat na to využitím příkladu:

Následující obrázek ukazuje nastavení automatického škálování pomocí výchozího profilu minimálního instancí = 2 a maximální instance = 10. V tomto příkladu pravidla jsou nakonfigurovány pro horizontální navýšení kapacity při počet zpráv ve frontě je větší než 10 a škálování na méně instancí když počet zpráv ve frontě je menší než 3. Takže teď můžete škálovat prostředek mezi 2 až 10 instancí.

Kromě toho je opakované profil nastavený pro pondělí. Nastavení pro minimální instance = 3 a maximální instance = 10. To znamená, že v pondělí, automatického škálování první kontroluje tuto podmínku, pokud je počet instancí dvě, provede se škálování nové minimálně tři. Za předpokladu, automatického škálování i nadále najít tento profil stav vyhovuje (pondělí), zpracují jen bázi procesoru scale-out/v nakonfigurovaná pravidla pro tento profil. V tuto chvíli jej nekontroluje délka fronty. Ale pokud chcete také podmínku délka fronty ke kontrole, měli byste zahrnout pravidla z výchozí profil také v profilu Monday.

Podobně když automatické škálování přepne zpět na výchozí profil, ji nejprve zkontroluje, pokud jsou splněny podmínky minimální a maximální. Pokud je počet instancí v době 12, provede se škálování v 10, maximální hodnotu povolenou pro výchozí profil.

![Nastavení automatického škálování](./media/autoscale-best-practices/insights-autoscale-best-practices-2.png)

### <a name="considerations-for-scaling-when-multiple-rules-are-configured-in-a-profile"></a>Důležité informace pro změnu velikosti při více pravidel jsou nakonfigurované v profilu
Existují případy, kdy budete muset nastavit víc pravidel v profilu. Následující sadu pravidel automatického škálování při pomocí služby jsou nastaveny více pravidel.

Na *horizontální navýšení kapacity*, automatické škálování se spustí při splnění libovolného pravidla.
Na *škálování na méně instancí*, automatické škálování vyžaduje všechna pravidla splnit.

Pro ilustraci, se předpokládá, že máte následující čtyři pravidla automatického škálování:

* Pokud využití procesoru < 30 %, škálování na méně instancí o 1
* Pokud paměti < 50 %, škálování na méně instancí o 1
* Pokud využití procesoru > 75 %, horizontální navýšení kapacity o 1
* Pokud paměti > 75 %, horizontální navýšení kapacity o 1

Potom se provede následující:

* Pokud je 76 % procesoru a paměti je 50 %, jsme horizontální navýšení kapacity.
* Pokud je 50 % využití procesoru a paměti je 76 % jsme horizontální navýšení kapacity.

Na druhé straně, pokud využití procesoru činí 25 % a paměť je 51 % automatického škálování nemá **není** škálování na méně instancí. Za účelem škálování na méně instancí, využití procesoru musí být 29 % a paměti 49 %.

### <a name="always-select-a-safe-default-instance-count"></a>Vždy vyberete možnost bezpečného výchozí počet instancí
Výchozí počet instancí je důležité, že automatické škálování dá škálovat služby na tento počet při metriky nejsou k dispozici. Proto vyberte výchozí počet instancí, který je bezpečný pro vaše úlohy.

### <a name="configure-autoscale-notifications"></a>Konfigurace oznámení o automatickém škálování
Automatické škálování se příspěvek do protokolu aktivit, pokud kterákoli z následujících podmínek:

* Automatické škálování vydává operace škálování
* Automatické škálování služby úspěšně dokončil akci škálování
* Automatické škálování služby se nedaří provést akci škálování.
* Metriky nejsou k dispozici pro službu automatického škálování pro rozhodnutí škálování.
* Metriky jsou k dispozici (obnovení) znovu, aby rozhodnutí o škálování.

Upozornění protokolu aktivit můžete použít také k monitorování stavu modul automatického škálování. Tady jsou příklady, které [vytvořit aktivitu protokolu výstrahy monitorovat všechny operace modul automatického škálování v rámci předplatného](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-alert) nebo [vytvořit aktivitu protokolu výstrahy pro monitorování všech neúspěšných automaticky škálovat škálovací v / horizontální navýšení kapacity operací na vaše předplatné](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-failed-alert).

Kromě použití upozornění protokolu aktivit, můžete také nakonfigurovat e-mailu nebo webhooková oznámení v oznámení pro akce úspěšné škálování prostřednictvím karty oznámení v nastavení automatického škálování.

## <a name="next-steps"></a>Další kroky
- [Vytvoření aktivitu protokolu výstrahy monitorovat všechny operace modul automatického škálování v rámci předplatného.](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-alert)
- [Vytvoření aktivitu protokolu výstrahy pro monitorování všech neúspěšných automaticky škálovat škálovací v / horizontální navýšení kapacity operace v rámci předplatného](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-failed-alert)
