---
title: Osvědčené postupy pro automatické škálování
description: Automatické škálování vzorů v Azure pro Web Apps, škálování sady virtuálních počítačů a Cloud Services
ms.topic: conceptual
ms.date: 07/07/2017
ms.subservice: autoscale
ms.openlocfilehash: 9aaf9525f2fedee67a86011e938b8e995ccfe9fe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "100611002"
---
# <a name="best-practices-for-autoscale"></a>Osvědčené postupy pro automatické škálování
Automatické škálování Azure Monitor platí jenom pro služby [Virtual Machine Scale Sets](https://azure.microsoft.com/services/virtual-machine-scale-sets/), [Cloud Services](https://azure.microsoft.com/services/cloud-services/), [App Service-Web Apps](https://azure.microsoft.com/services/app-service/web/)a [API Management](../../api-management/api-management-key-concepts.md).

## <a name="autoscale-concepts"></a>Koncepty automatického škálování

* Prostředek může mít jenom *jedno* nastavení automatického škálování.
* Nastavení automatického škálování může mít jeden nebo víc profilů a každý profil může mít jedno nebo víc pravidel automatického škálování.
* Nastavení automatického škálování škáluje instance vodorovně, což je zvýšení počtu instancí a *v* systému tím, *že se zmenší* počet instancí.
  Nastavení automatického škálování má maximálně, minimální a výchozí hodnotu instancí.
* Úloha automatického škálování vždycky přečte přidruženou metriku ke škálování podle a zkontroluje, jestli překročila nakonfigurovanou prahovou hodnotu pro škálování na více instancí nebo pro škálování na více instancí. Můžete si prohlédnout seznam metrik, pomocí kterých se dá automatické škálování škálovat, na [Azure monitor automatické škálování běžných metrik](autoscale-common-metrics.md).
* Všechny prahové hodnoty jsou vypočítány na úrovni instance. Například "horizontální navýšení kapacity na jednu instanci, pokud je průměrný procesor > 80%, pokud je počet instancí 2", znamená horizontální navýšení kapacity, pokud je průměrná hodnota procesoru napříč všemi instancemi větší než 80%.
* Všechny chyby automatického škálování se zaznamenávají do protokolu aktivit. Pak můžete nakonfigurovat [upozornění protokolu aktivit](../alerts/activity-log-alerts.md) tak, abyste se mohli informovat prostřednictvím e-mailu, SMS nebo webhooků, kdykoli dojde k selhání automatického škálování.
* Podobně se všechny úspěšné akce škálování publikují do protokolu aktivit. Pak můžete nakonfigurovat upozornění protokolu aktivit tak, abyste se mohli informovat prostřednictvím e-mailu, SMS nebo webhooků, kdykoli dojde k úspěšné akci automatického škálování. Můžete také nakonfigurovat oznámení e-mailu nebo Webhooku, abyste se dostali na úspěšné akce škálování přes kartu oznámení v nastavení automatického škálování.

## <a name="autoscale-best-practices"></a>Osvědčené postupy automatického škálování

Při použití automatického škálování používejte následující osvědčené postupy.

### <a name="ensure-the-maximum-and-minimum-values-are-different-and-have-an-adequate-margin-between-them"></a>Ujistěte se, že se maximální hodnota liší od minimální hodnoty a že mezi nimi je přiměřené rozpětí

Pokud máte nastavení minimum=2 a maximum=2 a aktuální počet instancí je 2, k žádné akci škálování nemůže dojít. Udržujte přiměřené rozpětí mezi maximálním a minimálním počtem instancí. Automatické škálování vždy provádí škálování mezi těmito limity.

### <a name="manual-scaling-is-reset-by-autoscale-min-and-max"></a>Ruční škálování se resetuje podle minimální a maximální hodnoty automatického škálování

Pokud ručně aktualizujete počet instancí na hodnotu vyšší nebo nižší než maximum, modul automatického škálování se automaticky změní na minimum (Pokud je uvedeno níže) nebo na maximum (Pokud je uvedeno výše). Například nastavíte rozsah mezi 3 a 6. Pokud máte jednu spuštěnou instanci, modul automatického škálování se při příštím spuštění škáluje na tři instance. Podobně platí, že pokud jste ručně nastavili měřítko na osm instancí, při dalším spuštění bude automatické škálování v dalším spuštění škálovat až na šest instancí.  Ruční škálování je dočasné, Pokud neobnovíte také pravidla automatického škálování.

### <a name="always-use-a-scale-out-and-scale-in-rule-combination-that-performs-an-increase-and-decrease"></a>Vždy používejte kombinaci možností škálování na více instancí a škálování na více instancí, která provádí zvýšení a snížení
Použijete-li pouze jednu část kombinace, automatické škálování provede akci pouze v jednom směru (horizontální navýšení kapacity nebo v), dokud nedosáhne maximálního počtu nebo minimálních počtů instancí definovaných v profilu. To není optimální, v ideálním případě byste se chtěli škálovat prostředky v časech vysokého využití, aby se zajistila dostupnost. Podobně v případě nízkého využití, které chcete, aby se váš prostředek škáloval dolů, takže můžete realizovat úspory nákladů.

### <a name="choose-the-appropriate-statistic-for-your-diagnostics-metric"></a>Vyberte vhodnou statistiku pro konkrétní diagnostickou metriku
Pro diagnostické metriky můžete zvolit z *průměrných*, *minimálních*, *maximálních* a *celkových* hodnot jako metriku pro horizontální navýšení kapacity. Nejběžnějšími statistikami je *průměr*.

### <a name="choose-the-thresholds-carefully-for-all-metric-types"></a>Pečlivě vyberte prahové hodnoty pro všechny typy metrik
Doporučujeme pečlivě zvolit různé prahové hodnoty pro horizontální navýšení kapacity a škálování v závislosti na praktických situacích.

V níže uvedených příkladech *nedoporučujeme* nastavení automatického škálování, jako jsou následující příklady, a to s použitím stejných nebo podobných mezních hodnot a v podmínkách:

* Zvýšení počtu instancí o 1, když je počet vláken vyšší nebo roven 600
* Snížení počtu instancí o 1, když je počet vláken nižší nebo roven 600

Pojďme se podívat na příklad toho, co může vést k chování, které se může zdát matoucí. Vezměte v úvahu následující pořadí.

1. Předpokládejme, že existují dvě instance, které začínají a pak průměrný počet vláken na instanci roste na 625.
2. Automatické škálování škáluje přidání třetí instance.
3. Dále předpokládáme, že průměrná hodnota počtu vláken napříč instancí spadá do 575.
4. Před horizontálním snížením kapacity se automatické škálování pokusí odhadnout, co má konečný stav, pokud se škáluje. Například 575 x 3 (aktuální počet instancí) = 1 725/2 (konečný počet instancí při horizontálním navýšení kapacity) = 862,5 vlákna. To znamená, že automatické škálování se musí okamžitě škálovat znovu i po horizontálním navýšení kapacity, pokud průměrný počet vláken zůstane stejný, nebo dokonce klesá jenom o malou částku. Pokud se však znovu škáluje, celý proces se opakuje, což vede k nekonečné smyčce.
5. Aby nedošlo k této situaci (s termínem "přepíná"), automatické škálování se vůbec nezvětšuje. Místo toho přeskočí a znovu vyhodnotí podmínku znovu při příštím spuštění úlohy služby. Stav přepíná může Zaměňujte mnoho lidí, protože funkce automatického škálování by nedokázala fungovat, když byl průměrný počet vláken 575.

Odhad během škálování je určený k tomu, aby se předešlo situacím "přepíná", kdy se akce škálování a škálování na více instancí průběžně přecházejí zpátky a zpátky. Mějte na paměti, že pokud vyberete stejné prahové hodnoty pro škálování na více instancí a v, mějte na paměti toto chování.

Doporučujeme vybrat odpovídající okraj mezi škálováním na více instancí a mezními hodnotami. Zvažte například následující kombinaci lepšího pravidla.

* Zvýšit počet instancí o 1, když je procesor% >= 80
* Snížit počet instancí o 1, když je procesor% <= 60

V tomto případě  

1. Předpokládejme, že existují 2 instance, se kterými můžete začít.
2. Pokud průměrné procento PROCESORů napříč instancemi přechází na 80, automatické škálování škáluje přidání třetí instance.
3. Nyní předpokládejme, že v průběhu času procento kapacity procesoru klesne na 60.
4. Pravidlo pro horizontální navýšení kapacity odhaduje konečný stav, pokud by bylo možné ho škálovat. Například 60 x 3 (aktuální počet instancí) = 180/2 (konečný počet instancí při horizontálním navýšení kapacity) = 90. Takže automatické škálování se neškáluje, protože by bylo nutné provést horizontální navýšení kapacity okamžitě. Místo toho přeskočí horizontální navýšení kapacity.
5. Při příští kontrole automatického škálování CPU pokračuje v dosahování 50. Odhadne se znovu – 50 × 3 instance = 150/2 instance = 75, což je pod prahovou hodnotou pro horizontální na80 výšení kapacity, takže se úspěšně škáluje na 2 instance.

> [!NOTE]
> Pokud modul automatického škálování detekuje přepíná může být výsledkem škálování na cílový počet instancí, pokusí se také škálovat na jiný počet instancí mezi aktuálním počtem a cílovým počtem. Pokud k přepíná nedojde v tomto rozsahu, automatické škálování bude pokračovat v operaci škálování s novým cílem.

### <a name="considerations-for-scaling-threshold-values-for-special-metrics"></a>Důležité informace o prahových hodnotách škálování pro speciální metriky
 Pro speciální metriky, jako je například úložiště nebo Service Bus metrika délky fronty, je prahová hodnota průměrný počet dostupných zpráv na aktuální počet instancí. Pečlivě vyberte prahovou hodnotu pro tuto metriku.

Podívejme se na to s příkladem, abyste zajistili lepší pochopení chování.

* Zvýšit počet instancí o 1, když se počet zpráv ve frontě úložiště >= 50
* Snížit počet instancí za 1, když se počet zpráv ve frontě úložiště <= 10

Vezměte v úvahu následující sekvenci:

1. Existují dvě instance fronty úložiště.
2. Zprávy budou průběžně zobrazovat a při kontrole fronty úložiště celkový počet čtení je 50. Můžete předpokládat, že automatické škálování by mělo začínat akci škálování na více instancí. Upozorňujeme však, že je stále 50/2 = 25 zpráv na instanci. Horizontální navýšení kapacity tedy neproběhne. Pro první škálování na více instancí by měl být celkový počet zpráv ve frontě úložiště 100.
3. V dalším kroku se předpokládá, že celkový počet zpráv dosáhne 100.
4. V důsledku akce škálování na více instancí se přidá třetí instance fronty úložiště.  Další akce horizontálního navýšení kapacity nebude provedena, dokud celkový počet zpráv ve frontě nedosáhne 150, protože 150/3 = 50.
5. Počet zpráv ve frontě je nyní menší. Při třech instancích se první akce škálování provede, když celkový počet zpráv ve všech frontách přistává až 30, protože 30/3 = 10 zpráv na instanci, což je prahová hodnota pro horizontální navýšení kapacity.

### <a name="considerations-for-scaling-when-multiple-profiles-are-configured-in-an-autoscale-setting"></a>Důležité informace o škálování v případě více nakonfigurovaných profilů v nastavení automatického škálování
V nastavení automatického škálování můžete zvolit výchozí profil, který se vždycky použije bez závislosti na plánu nebo času, nebo můžete zvolit periodický profil nebo profil pro pevné období s rozsahem data a času.

Když je služba automatického škálování zpracovává, vždy kontroluje následující pořadí:

1. Pevný profil data
2. Periodický profil
3. Výchozí profil (Always)

Pokud je splněna podmínka profilu, automatické škálování nekontroluje další podmínku profilu pod ní. Automatické škálování zpracuje pouze jeden profil v jednom okamžiku. To znamená, že pokud chcete také zahrnout podmínku zpracování z profilu nižší úrovně, musíte zahrnout tato pravidla i do aktuálního profilu.

Pojďme se podívat na příklad:

Následující obrázek ukazuje nastavení automatického škálování s výchozím profilem s minimálními instancemi, které mají minimální počet instancí = 2 a maximální počet instancí = 10. V tomto příkladu jsou pravidla nakonfigurovaná pro horizontální navýšení kapacity, pokud je počet zpráv ve frontě větší než 10 a v případě, že počet zpráv ve frontě je menší než tři, se škálováním. Takže teď je možné prostředek škálovat mezi dvěma i deseti instancemi.

K dispozici je také opakovaný profil nastavený pro pondělí. Nastavuje se pro minimální instance = 3 a maximální počet instancí = 10. To znamená, že pokud je počet instancí v pondělí, při prvním automatickém škálování pro tento stav se změní na nové minimum na tři. Pokud má automatické škálování pořád stejný stav pro tento profil (pondělí), zpracuje jenom pravidla škálování na straně procesoru (v/v), která jsou nakonfigurovaná pro tento profil. V tuto chvíli se nekontroluje délka fronty. Pokud ale chcete zkontrolovat také podmínku délka fronty, měli byste zahrnout tato pravidla z výchozího profilu i do vašeho profilu v pondělí.

Podobně když se automatické škálování přepne zpátky na výchozí profil, nejprve zkontroluje, jestli jsou splněné minimální a maximální podmínky. Pokud je počet instancí v čase 12, zmenší se na 10, což je maximum povolené pro výchozí profil.

![nastavení automatického škálování](./media/autoscale-best-practices/insights-autoscale-best-practices-2.png)

### <a name="considerations-for-scaling-when-multiple-rules-are-configured-in-a-profile"></a>Důležité informace o škálování v případě více nakonfigurovaných pravidel v profilu

V některých případech může být nutné nastavit více pravidel v profilu. Následující pravidla automatického škálování používá modul automatického škálování, když je nastaveno více pravidel.

Při *horizontálním* navýšení kapacity se automatické škálování spustí, pokud je splněno nějaké pravidlo.
Při *horizontálním* navýšení kapacity vyžaduje automatické škálování splnění všech pravidel.

K ilustraci se předpokládá, že máte následující čtyři pravidla automatického škálování:

* Pokud je procesor < 30%, škálování podle 1
* Pokud paměť < 50%, škálování podle 1
* Pokud je procesor > 75%, škálovat podle 1
* Pokud paměť > 75%, škálování podle 1

Pak dojde k následujícímu:

* Pokud je procesor 76% a paměť je 50%, horizontální navýšení kapacity.
* Pokud je procesor 50% a paměť je 76%, horizontální navýšení kapacity.

Na druhou stranu platí, že pokud je procesor 25% a paměť je 51% automatické škálování **se neškáluje** . Aby bylo možné škálovat, musí být procesor o velikosti 29% až 49%.

### <a name="always-select-a-safe-default-instance-count"></a>Vždy vyberte bezpečný výchozí počet instancí
Výchozí počet instancí je důležitý, protože funkce automatického škálování škáluje vaši službu na tento počet, když nejsou dostupné metriky. Proto vyberte výchozí počet instancí, který bude pro vaše úlohy bezpečný.

### <a name="configure-autoscale-notifications"></a>Konfigurace oznámení automatického škálování
Automatické škálování bude odesílat do protokolu aktivit, pokud dojde k některé z následujících podmínek:

* Automatické škálování vydá operaci škálování.
* Služba automatického škálování úspěšně dokončí akci škálování.
* Služba automatického škálování nemůže provést akci škálování.
* Metriky nejsou k dispozici pro službu automatického škálování, aby bylo možné provést rozhodnutí o škálování.
* Metriky jsou k dispozici (obnovení) znovu, aby bylo možné rozhodnutí škálovat.
* Automatické škálování detekuje přepíná a přerušuje pokus o škálování. V této situaci se zobrazí typ protokolu `Flapping` . Pokud se to zobrazí, zvažte, jestli jsou vaše prahové hodnoty příliš úzké.
* Automatické škálování detekuje přepíná, ale pořád dokáže úspěšně škálovat. V této situaci se zobrazí typ protokolu `FlappingOccurred` . Pokud se to zobrazí, modul automatického škálování se pokusil škálovat (např. ze 4 instancí na 2), ale zjistil, že by to způsobilo přepíná. Místo toho se modul automatického škálování změnil na jiný počet instancí (například pomocí 3 instancí namísto 2), který už nezpůsobuje přepíná, takže se na tento počet instancí škáluje.

K monitorování stavu modulu automatického škálování můžete použít také upozornění protokolu aktivit. Tady jsou příklady, jak [vytvořit upozornění protokolu aktivit pro monitorování všech operací automatického škálování v rámci předplatného](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-alert) nebo [Vytvoření upozornění protokolu aktivit pro monitorování všech neúspěšných škálování automatického škálování ve vašem předplatném a na horizontálním](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-failed-alert)navýšení kapacity.

Kromě používání výstrah protokolu aktivit můžete také nakonfigurovat oznámení e-mailu nebo Webhooku, abyste získali oznámení o úspěšných akcích škálování prostřednictvím karty oznámení v nastavení automatického škálování.

## <a name="next-steps"></a>Další kroky
- [Vytvořením upozornění protokolu aktivit můžete monitorovat všechny operace modulu automatického škálování v rámci vašeho předplatného.](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-alert)
- [Vytvoření upozornění protokolu aktivit pro monitorování všech neúspěšných škálování pro automatické škálování v rámci předplatného nebo horizontálního navýšení kapacity](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-failed-alert)

