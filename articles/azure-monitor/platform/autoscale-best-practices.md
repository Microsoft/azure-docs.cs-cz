---
title: Doporučené postupy pro automatické škálování
description: Vzory automatického škálování v Azure pro webové aplikace, škálovací sady virtuálních strojů a cloudové služby
ms.topic: conceptual
ms.date: 07/07/2017
ms.subservice: autoscale
ms.openlocfilehash: a05cf87e660cc6c388ea2055bb174c47b99da4a3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79248915"
---
# <a name="best-practices-for-autoscale"></a>Osvědčené postupy pro automatické škálování
Automatické škálování Azure Monitor uplatní jenom [pro škálovací sady virtuálních strojů](https://azure.microsoft.com/services/virtual-machine-scale-sets/), [cloudové služby](https://azure.microsoft.com/services/cloud-services/), [aplikační služby – webové aplikace](https://azure.microsoft.com/services/app-service/web/)a [služby správy rozhraní API](https://docs.microsoft.com/azure/api-management/api-management-key-concepts).

## <a name="autoscale-concepts"></a>Koncepty automatického škálování

* Prostředek může mít pouze *jedno* nastavení automatického škálování.
* Nastavení automatického škálování může mít jeden nebo více profilů a každý profil může mít jedno nebo více pravidel automatického škálování.
* Nastavení automatického škálování škáluje instance vodorovně, což je *mimo* zvýšením instance a *snížením* počtu instancí.
  Nastavení automatického škálování má maximální, minimální a výchozí hodnotu instancí.
* Úloha automatického škálování vždy přečte přidružené metriky škálovat podle, kontrola, pokud překročila nakonfigurovanou prahovou hodnotu pro horizontální navýšení kapacity nebo škálování. Můžete zobrazit seznam metrik, které automatické škálování můžete škálovat podle na [Azure Monitor automatické škálování běžné metriky](autoscale-common-metrics.md).
* Všechny prahové hodnoty se počítají na úrovni instance. Například "horizontální navýšení kapacity podle jedné instance při průměrné > procesoru 80 %, když počet instancí je 2", znamená horizontální navýšení kapacity, když průměrný procesor ve všech instancích je větší než 80 %.
* Všechny selhání automatického škálování jsou zaznamenány do protokolu aktivit. Potom můžete nakonfigurovat [výstrahu protokolu aktivit,](./../../azure-monitor/platform/activity-log-alerts.md) abyste mohli být upozorněni prostřednictvím e-mailu, SMS nebo webhooků, kdykoli dojde k selhání automatického škálování.
* Podobně jsou všechny úspěšné akce škálování zaúčtovány do protokolu aktivit. Potom můžete nakonfigurovat výstrahu protokolu aktivit, abyste mohli být upozorněni prostřednictvím e-mailu, SMS nebo webhooků, kdykoli dojde k úspěšné akci automatického škálování. Můžete také nakonfigurovat e-mailová oznámení nebo oznámení webhooku, abyste byli upozorněni na úspěšné akce škálování prostřednictvím karty oznámení v nastavení automatického škálování.

## <a name="autoscale-best-practices"></a>Doporučené postupy automatického škálování

Při používání automatického škálování používejte následující doporučené postupy.

### <a name="ensure-the-maximum-and-minimum-values-are-different-and-have-an-adequate-margin-between-them"></a>Ujistěte se, že se maximální hodnota liší od minimální hodnoty a že mezi nimi je přiměřené rozpětí

Pokud máte nastavení, které má minimum=2, maximum=2 a počet aktuální instance je 2, může dojít k žádné akci měřítka. Udržujte odpovídající rozpětí mezi maximální a minimální počet instancí, které jsou včetně. Automatické škálování se vždy škáluje mezi těmito omezeními.

### <a name="manual-scaling-is-reset-by-autoscale-min-and-max"></a>Ruční škálování se resetuje podle minimální a maximální hodnoty automatického škálování

Pokud ručně aktualizujete počet instancí na hodnotu nad nebo pod maximem, modul automatického škálování se automaticky změní na minimální (pokud je níže) nebo na maximum (pokud je výše). Například nastavíte rozsah mezi 3 a 6. Pokud máte jednu spuštěnou instanci, modul automatického škálování se při dalším spuštění změní na tři instance. Podobně pokud ručně nastavíte měřítko na osm instancí, při příštím spuštění automatické škálování se zmenší zpět na šest instancí při dalším spuštění.  Ruční škálování je dočasné, pokud neresetujete také pravidla automatického škálování.

### <a name="always-use-a-scale-out-and-scale-in-rule-combination-that-performs-an-increase-and-decrease"></a>Vždy používejte kombinaci pravidel škálování a škálování, která provádí zvýšení a snížení
Pokud použijete pouze jednu část kombinace, automatické škálování provede akci pouze v jednom směru (horizontální navýšení kapacity nebo v) dokud nedosáhne maxima nebo minimálního počtu instancí definovaných v profilu. To není optimální, v ideálním případě chcete, aby váš prostředek vertikálně navýšit kapacitu v době vysoké využití k zajištění dostupnosti. Podobně v době nízké spotřeby chcete, aby se váš zdroj zmenšuje, abyste mohli realizovat úspory nákladů.

### <a name="choose-the-appropriate-statistic-for-your-diagnostics-metric"></a>Vyberte vhodnou statistiku pro konkrétní diagnostickou metriku
Pro metriky diagnostiky můžete zvolit mezi *průměrem*, *minimem*, *maximem* a *součtem* jako metriku, podle které se má škálovat. Nejběžnější statistika je *Průměr*.

### <a name="choose-the-thresholds-carefully-for-all-metric-types"></a>Pečlivě vyberte prahové hodnoty pro všechny typy metrik
Doporučujeme pečlivě zvolit různé prahové hodnoty pro horizontální navýšení kapacity a škálování na základě praktických situací.

*Nedoporučujeme* nastavení automatického škálování, jako jsou níže uvedené příklady se stejnými nebo podobnými prahovými hodnotami pro out a v podmínkách:

* Zvýšit počet instancí o 1 počet při počet vláken >= 600
* Snížení počtu instancí o 1 počet při <počet vláken = 600

Podívejme se na příklad toho, co může vést k chování, které se může zdát matoucí. Zvažte následující pořadí.

1. Předpokládejme, že existují dvě instance začít a pak průměrný počet podprocesů na instanci zvětšuje na 625.
2. Automatické škálování se škáluje přidání třetí instance.
3. Dále předpokládejme, že průměrný počet podprocesů napříč instancí klesne na 575.
4. Před škálování dolů, automatické škálování se pokusí odhadnout, jaký bude konečný stav, pokud měřítko v. Například 575 x 3 (počet aktuálních instancí) = 1,725 / 2 (konečný počet instancí při zmenšení na zmenšení) = 862,5 podprocesů. To znamená, že automatické škálování bude muset okamžitě horizontální navýšení kapacity znovu i poté, co škálovat v, pokud průměrný počet vláken zůstává stejný, nebo dokonce klesne jen malé množství. Pokud by se však znovu zvětšila, celý proces by se opakoval, což by vedlo k nekonečné smyčce.
5. Aby se zabránilo této situaci (označované jako "mávání"), automatické škálování není škálovat vůbec. Místo toho přeskočí a znovu přehodnotí podmínku při příštím spuštění úlohy služby. Stav prolnutí může zmást mnoho lidí, protože automatické škálování by se nezdálo, že bude fungovat, když průměrný počet vláken byl 575.

Odhad během škálování je určen k tomu, aby se zabránilo "mávání" situacích, kdy akce škálování a škálování na více let neustále přecvačy tam a zpět. Toto chování mějte na paměti, když zvolíte stejné prahové hodnoty pro horizontální navýšení kapacity a in.

Doporučujeme zvolit odpovídající rozpětí mezi horizontálním navýšením kapacity a prahovými hodnotami. Jako příklad zvažte následující kombinaci lepších pravidel.

* Zvýšení počtu instancí o 1 počet, když cpu% >= 80
* Snížení počtu instancí o 1 při % procesoru <= 60

V tomto případě  

1. Předpokládejme, že existují 2 instance začít.
2. Pokud průměrný procesor% napříč instancemi přejde na 80, automatické škálování se škáluje přidání třetí instance.
3. Nyní předpokládejme, že v průběhu času CPU% klesne na 60.
4. Pravidlo škálování automatického škálování odhaduje konečný stav, pokud by bylo měřítko. Například 60 x 3 (počet aktuálních instancí) = 180 / 2 (konečný počet instancí při zmenšení na zmenšení) = 90. Takže automatické škálování není scale-in, protože by musel horizontální navýšení kapacity znovu okamžitě. Místo toho přeskočí škálování dolů.
5. Při příští kontrole automatického škálování procesor nadále klesá na 50. Znovu odhadne - 50 x 3 instance = 150 / 2 instance = 75, což je pod prahovou hodnotou horizontálnínavýšení kapacity 80, takže se úspěšně škáluje na 2 instance.

### <a name="considerations-for-scaling-threshold-values-for-special-metrics"></a>Důležité informace o prahových hodnotách škálování pro speciální metriky
 Pro speciální metriky, jako je metrika délka fronty úložiště nebo servisní sběrnice, je prahová hodnota průměrný počet zpráv dostupných na aktuální počet instancí. Pečlivě zvolte prahovou hodnotu pro tuto metriku.

Podívejme se na příklad, abychom zajistili, že lépe porozumíte chování.

* Zvýšení počtu instancí o 1 počet při počtu zpráv fronty úložiště >= 50
* Snížení počtu instancí o 1 počet při počtu zpráv fronty úložiště <= 10

Zvažte následující pořadí:

1. Existují dvě instance fronty úložiště.
2. Zprávy stále přicházejí a při kontrole fronty úložiště, celkový počet přečte 50. Můžete předpokládat, že automatické škálování by měla spustit akci horizontálnínavýšení kapacity. Všimněte si však, že je stále 50/2 = 25 zpráv na instanci. Takže horizontální navýšení kapacity nedojde. Pro první horizontální navýšení kapacity by měl být celkový počet zpráv ve frontě úložiště 100.
3. Dále předpokládejme, že celkový počet zpráv dosáhne 100.
4. Třetí instance fronty úložiště je přidána z důvodu akce horizontálnínavýšení kapacity.  Další akce horizontálního navýšení kapacity nedojde, dokud celkový počet zpráv ve frontě dosáhne 150, protože 150/3 = 50.
5. Nyní se počet zpráv ve frontě zmenší. Se třemi instancemi první akce škálování v případě, že celkový počet zpráv ve všech frontách přidat až 30, protože 30/3 = 10 zpráv na instanci, což je prahová hodnota škálování.

### <a name="considerations-for-scaling-when-multiple-profiles-are-configured-in-an-autoscale-setting"></a>Důležité informace o škálování v případě více nakonfigurovaných profilů v nastavení automatického škálování
V nastavení automatického škálování můžete zvolit výchozí profil, který je vždy použit bez závislosti v plánu nebo čase, nebo můžete zvolit opakující se profil nebo profil pro pevné období s datem a časovým rozsahem.

Když je služba automatického škálování zpracovává, vždy kontroluje v následujícím pořadí:

1. Profil pevného data
2. Opakující se profil
3. Výchozí ("Vždy") profil

Pokud je splněna podmínka profilu, automatické škálování nekontroluje další podmínku profilu pod ní. Automatické škálování zpracovává pouze jeden profil najednou. To znamená, že pokud chcete zahrnout také podmínku zpracování z profilu nižší vrstvy, musíte zahrnout tato pravidla také v aktuálním profilu.

Podívejme se na příklad:

Následující obrázek znázorňuje nastavení automatického škálování s výchozím profilem minimálních instancí = 2 a maximální instance = 10. V tomto příkladu jsou pravidla nakonfigurována tak, aby se škálovala, když je počet zpráv ve frontě větší než 10 a škálování, pokud je počet zpráv ve frontě menší než tři. Takže nyní prostředek můžete škálovat mezi dvěma a deseti instancemi.

Kromě toho je na pondělí nastaven opakující se profil. Je nastavena pro minimální instance = 3 a maximální instance = 10. To znamená, že v pondělí první automatické škálování kontroly pro tuto podmínku, pokud počet instancí je dva, se škáluje na nové minimum tři. Tak dlouho, dokud automatické škálování nadále najít tento profil podmínky uzavřeno (pondělí), pouze zpracovává škálování na základě procesoru/v pravidla nakonfigurovaná pro tento profil. V tomto okamžiku nekontroluje délku fronty. Pokud však chcete zkontrolovat také podmínku délky fronty, měli byste tato pravidla zahrnout také z výchozího profilu do pondělního profilu.

Podobně při automatické škálování přepne zpět na výchozí profil, nejprve zkontroluje, zda jsou splněny minimální a maximální podmínky. Pokud je počet instancí v době 12, změní měřítko na 10, což je maximální povolené pro výchozí profil.

![nastavení automatického škálování](./media/autoscale-best-practices/insights-autoscale-best-practices-2.png)

### <a name="considerations-for-scaling-when-multiple-rules-are-configured-in-a-profile"></a>Důležité informace o škálování v případě více nakonfigurovaných pravidel v profilu

Existují případy, kdy budete muset nastavit více pravidel v profilu. Následující pravidla automatického škálování jsou používány modul automatického škálování při více pravidel jsou nastaveny.

Při *horizontálním navýšení kapacity*se automatické škálování spustí, pokud je splněno jakékoli pravidlo.
Při *škálování*vyžaduje automatické škálování splnění všech pravidel.

Pro ilustraci předpokládejme, že máte následující čtyři pravidla automatického škálování:

* Pokud < cpu 30 %, škálování v šaku
* Pokud paměť < 50 %, škálování v rozsahu 1
* Pokud procesor > 75 %, horizontální navýšení kapacity o 1
* Pokud paměť > 75 %, horizontální navýšení kapacity o 1

Pak dojde k následujícímu:

* Pokud je procesor 76% a paměť je 50%, jsme se škálovat.
* Pokud je procesor 50% a paměť je 76%, horizontální navýšení kapacity.

Na druhou stranu, pokud cpu je 25% a paměť je 51% automatické škálování **není** scale-in. Chcete-li škálovat, procesor musí být 29 % a paměť 49 %.

### <a name="always-select-a-safe-default-instance-count"></a>Vždy vyberte bezpečný výchozí počet instancí
Výchozí počet instancí je důležité, protože automatické škálování škáluje službu na tento počet, když metriky nejsou k dispozici. Proto vyberte výchozí počet instancí, který je bezpečný pro vaše úlohy.

### <a name="configure-autoscale-notifications"></a>Konfigurace oznámení automatického škálování
Automatické škálování bude účtovat do protokolu aktivit, pokud dojde k některé z následujících podmínek:

* Automatické škálování vydává operaci škálování.
* Služba automatického škálování úspěšně dokončí akci škálování.
* Služba automatického škálování neprovede akci škálování.
* Metriky nejsou k dispozici pro službu automatického škálování, aby bylo možné provést rozhodnutí škálování.
* Metriky jsou k dispozici (obnovení) znovu učinit rozhodnutí škálování.

Můžete také použít výstrahu protokolu aktivit ke sledování stavu modulu automatického škálování. Zde jsou příklady pro [vytvoření výstrahy protokolu aktivit pro sledování všech operací motoru automatického škálování v rámci předplatného](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-alert) nebo k vytvoření [výstrahy protokolu aktivit, která bude sledovat všechny neúspěšné operace automatického škálování v nebo horizontálním navýšení kapacity ve vašem předplatném](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-failed-alert).

Kromě použití upozornění protokolu aktivit můžete také nakonfigurovat e-mailová oznámení nebo oznámení webhooku, abyste získali upozornění na úspěšné akce škálování prostřednictvím karty Oznámení v nastavení automatického škálování.

## <a name="next-steps"></a>Další kroky
- [Vytvořte výstrahu protokolu aktivit a sledujte všechny operace motoru automatického škálování v rámci předplatného.](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-alert)
- [Vytvoření výstrahy protokolu aktivit ke sledování všech neúspěšných operací automatického škálování v rámci/škálování v rámci předplatného](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-failed-alert)

