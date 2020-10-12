---
title: Principy zpracování času v Azure Stream Analytics
description: Naučte se, jak zvolit nejlepší čas spuštění, zpracovávat zpožděné a včasné události a o zpracování metrik v Azure Stream Analytics.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/11/2020
ms.openlocfilehash: 0830a8b552283b5b39fa78c505ed177d1959989f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "83640035"
---
# <a name="understand-time-handling-in-azure-stream-analytics"></a>Principy zpracování času v Azure Stream Analytics

V tomto článku se naučíte, jak vytvořit návrh a vyřešit tak praktické problémy s zpracováním v Azure Stream Analytics úlohách. Rozhodnutí při návrhu zpracování se úzce vztahují k faktorům řazení událostí.

## <a name="background-time-concepts"></a>Koncepty času na pozadí

K lepšímu orámování diskuze můžeme definovat několik konceptů na pozadí:

- **Čas události**: čas, kdy došlo k původní události. Například když se na silničním automobilu blíží placená kabina.

- **Doba zpracování**: čas, kdy událost dosáhne systému zpracování a je zaznamenána. Například když senzor rádiového kabiny uvidí auto a počítačový systém bude chvíli zpracovávat data.

- **Vodoznak**: Značka času události, která indikuje, k jakým událostem bodu došlo na vstupu procesoru streamování. Meze umožňují systému označovat nejasný průběh přijímání událostí. V rámci povaze datových proudů se příchozí data události nikdy nezastaví, takže vodoznaky indikují průběh určitého bodu v datovém proudu.

   Koncept meze je důležitý. Meze umožňují Stream Analytics určit, kdy systém může vydávat kompletní, správné a opakující se výsledky, které není potřeba odvolávat. Zpracování lze provést předvídatelným a opakovaným způsobem. Pokud je třeba pro určitou podmínku zpracování chyb provést přepočet, jsou vodoznaky bezpečné počínaje počátečním a koncovým bodem.

Další materiály k tomuto subjektu najdete v článku blogové příspěvky Tyler Akidau [streamování 101](https://www.oreilly.com/ideas/the-world-beyond-batch-streaming-101) a [streaming 102](https://www.oreilly.com/ideas/the-world-beyond-batch-streaming-102).

## <a name="choose-the-best-starting-time"></a>Zvolit nejlepší čas spuštění

Stream Analytics poskytuje uživatelům dvě možnosti pro dobu výdeje události: čas přijetí a čas aplikace.

### <a name="arrival-time"></a>Čas doručení

Čas doručení se přiřadí ve vstupním zdroji, když událost dosáhne zdroje. Čas doručení můžete získat pomocí vlastnosti **EventEnqueuedUtcTime** pro vstup Event Hubs, vlastnost **IoTHub. EnqueuedTime** pro IoT Hub Input a vlastnost **BlobProperties. LastModified** pro vstup objektu BLOB.

Ve výchozím nastavení se používá čas doručení, který se nejlépe používá pro scénáře archivace dat, kde není nutná dočasná logika.

### <a name="application-time-also-named-event-time"></a>Čas aplikace (také název čas události)

Čas aplikace je přiřazen při vygenerování události a je součástí datové části události. Chcete-li zpracovat události podle času aplikace, použijte klauzuli **timestamp by** v dotazu Select. Pokud chybí **časové razítko** , zpracují se události podle času doručení.

V datové části je důležité použít časové razítko, pokud je časová logika zapojená do systému na zpoždění ve zdrojovém systému nebo v síti. Čas přiřazený k události je k dispozici v [systému. ČASOVÉ RAZÍTKo](https://docs.microsoft.com/stream-analytics-query/system-timestamp-stream-analytics).

## <a name="how-time-progresses-in-azure-stream-analytics"></a>Jak dlouho probíhá Azure Stream Analytics

Když použijete čas aplikace, je průběh času založen na příchozích událostech. Pro systém zpracování datových proudů je obtížné zjistit, jestli neexistují žádné události, nebo pokud dojde ke zpoždění událostí. Z tohoto důvodu Azure Stream Analytics vygeneruje heuristické meze následujícími způsoby pro každý vstupní oddíl:

* Když dojde k jakékoli příchozí události, je vodoznak největší čas události Stream Analytics ještě daleko mínus velikost okna tolerance mimo pořadí.

* V případě, že není žádná příchozí událost, je vodoznak aktuální odhadovaná doba doručení mínus okno tolerance zpožděného doručení. Odhadovaná doba doručení je čas, který uplynul od poslední výskytu vstupní události a čas doručení vstupní události.

   Čas doručení lze odhadnout pouze v případě, že čas skutečného doručení je vygenerován ve zprostředkovateli vstupních událostí, jako je například Event Hubs, ani na virtuálním počítači Azure Stream Analytics zpracování událostí.

Návrh obsluhuje dva další účely, kromě generování vodoznaků:

1. Systém generuje výsledky včas s příchozími událostmi nebo bez nich.

   Máte kontrolu nad tím, jak včas chcete zobrazit výsledky výstupu. V Azure Portal na stránce **řazení událostí** úlohy Stream Analytics můžete nakonfigurovat nastavení **události mimo pořadí** . Když toto nastavení nakonfigurujete, zvažte kompromisy s tolerancí pro události mimo pořadí v datovém proudu událostí.

   Okno tolerance pozdního doručení je nezbytné pro zachování vodoznaků, a to i v případě absence příchozích událostí. V některých případech může nastat období, kdy nepřicházejí příchozí události, jako by byl vstupní datový proud události zhuštěný. Tento problém je exacerbated pomocí více oddílů ve zprostředkovateli událostí vstupu.

   V případě, že jsou vstupy zhuštěné a používají se víc oddílů, můžou se systémy zpracování dat streamování bez zpožděných odchylek od opožděného doručení zpomalit.

2. Chování systému je třeba opakovat. Opakovatelnost je důležitou vlastností systému pro zpracování datových proudů.

   Vodoznak je odvozen z doby doručení a času použití. Obě jsou trvale v zprostředkovateli událostí, a proto je lze opakovat. Při odhadu času doručení v případě absence událostí Azure Stream Analytics deníky odhadovanou dobu doručení pro opakovatelnost během opakovaného přehrávání pro obnovení po selhání.

Pokud se rozhodnete použít **čas přijetí** jako čas události, nemusíte konfigurovat toleranci mimo pořadí a zpoždění pro doručení. Vzhledem k tomu, že je zaručeno zvýšení **času doručení** ve zprostředkovateli událostí vstupu, Azure Stream Analytics jednoduše ignoruje konfigurace.

## <a name="late-arriving-events"></a>Pozdě přicházející události

Podle definice okna tolerance opožděného doručení pro každou příchozí událost Azure Stream Analytics porovnává **čas události** s **časem doručení**. Pokud je čas události mimo okno tolerance, můžete nakonfigurovat systém tak, aby událost vynechal, nebo upravit čas události, který má být v rámci tolerance.

Po vygenerování vodoznaků může služba potenciálně přijímat události s časem události nižší, než je mez. Službu můžete nakonfigurovat tak, aby buď **vyřadí** tyto události, nebo **upravte** čas události na hodnotu meze.

V rámci úpravy je **System. timestamp** události nastaven na novou hodnotu, ale pole **čas události** se nezměnilo. Tato úprava je jedinou situací, kdy se systém událostí **. časové razítko** může lišit od hodnoty v poli čas události a může způsobit vygenerování neočekávaných výsledků.

## <a name="handle-time-variation-with-substreams"></a>Zpracovat časovou variaci s podproudy

Popsaný mechanizmus generování heuristického vodoznaku dobře funguje ve většině případů, kdy je čas většinou synchronizovaný mezi různými odesílateli událostí. V reálném čase, zejména v mnoha scénářích IoT, má však systém malou kontrolu nad hodinami v odesílajících událostech. Odesílatelé událostí můžou být v poli nejrůznější zařízení, třeba na různých verzích hardwaru a softwaru.

Místo použití meze, která je globální pro všechny události ve vstupním oddílu, Stream Analytics má jiný mechanismus s názvem **podproudy**. Můžete využít podproudy v rámci úlohy vytvořením dotazu úlohy, který používá klauzuli [**timestamp by**](/stream-analytics-query/timestamp-by-azure-stream-analytics) a klíčové slovo **over**. Chcete-li určit podproud, zadejte název klíčového sloupce za klíčovým slovem **over** , `deviceid` takže systém použije pro tento sloupec zásady času. Každý podproud získá vlastní nezávislá vodoznak. Tento mechanismus je vhodný k tomu, aby bylo možné včasné generování výstupu při zpracování velkých hodin nebo zpoždění sítě mezi odesílateli událostí.

Podproudy jsou jedinečné řešení poskytované Azure Stream Analytics a nejsou nabízeny jinými systémy pro zpracování dat streamování.

Když použijete podproudy, Stream Analytics pro příchozí události použít okno tolerance zpoždění doručení. Tolerance pozdního doručení Určuje maximální hodnotu, o kterou mohou jiné podproudy navzájem nacházet. Pokud je například zařízení 1 v časovém razítku 1 a zařízení 2 je na časovém razítku 2, tolerance doručení je zpožděna 2 minus časové razítko 1. Výchozí nastavení je 5 sekund a je pro zařízení s odlišnými časovými razítky asi příliš malé. Doporučujeme, abyste začali s 5 minutami a provedli úpravy podle jejich modelu zkosení hodin zařízení.

## <a name="early-arriving-events"></a>Události předčasného doručení

Možná jste si všimli dalšího konceptu s názvem okno předčasného doručení, které vypadá podobně jako opak okna tolerance opožděného doručení. Toto okno je pevně nastavené na 5 minut a v okně tolerance pozdního doručení slouží k jinému účelu.

Vzhledem k tomu, že Azure Stream Analytics zaručuje úplné výsledky, můžete jako první výstupní čas úlohy zadat jenom **čas spuštění úlohy** , ne vstupní čas. Čas spuštění úlohy je vyžadován, aby bylo dokončeno celé okno, nikoli pouze uprostřed okna.

Stream Analytics odvodí čas zahájení ze specifikace dotazu. Jelikož je však zprostředkovatel vstupních událostí indexován pouze pomocí doby doručení, systém musí přeložit čas počáteční události na čas doručení. Systém může zahájit zpracování událostí z tohoto bodu ve zprostředkovateli událostí vstupu. V případě limitu předčasného přihlašování je převod jednoduchý: počáteční čas události mínus okno s předčasném doručením 5 minut. Tento výpočet také znamená, že systém vyřazuje všechny události, které se zobrazují jako čas události 5 minut dříve než čas doručení. [Metrika událostí počátečního vstupu](stream-analytics-monitoring.md) se zvyšuje při vyřazení událostí.

Tento koncept slouží k zajištění toho, aby se zpracování opakovalo bez ohledu na to, kde začínáte výstup. Bez takového mechanismu by nebylo možné zaručit opakovatelnost, protože to mnoho dalších nárokových systémů streamování udělá.

## <a name="side-effects-of-event-ordering-time-tolerances"></a>Vedlejší účinky odchylek času řazení událostí

Úlohy Stream Analytics mají několik možností **řazení událostí** . Dva můžou být nakonfigurované v Azure Portal: nastavení **události mimo pořadí** (tolerance mimo pořadí) a **události, které dorazí na nastavení zpožděné** (pozdní tolerance doručení). Tolerance **počátečního doručení** je pevná a nelze ji upravit. Tyto časové zásady používá Stream Analytics k poskytování silných záruk. Tato nastavení ale mají některé někdy neočekávané důsledky:

1. Nechtěně odesílá události, které jsou příliš brzy.

   Počáteční události by se neměly normálně zacházet. Je možné, že se události předčasného odeslání do výstupu, pokud jsou hodiny odesilatele spuštěné příliš rychle. Všechny události předčasného doručení jsou vyřazené, takže se z výstupu nezobrazí žádné z nich.

2. Posílání starých událostí Event Hubs ke zpracování pomocí Azure Stream Analytics.

   I když se staré události mohou nacházet jako neškodné, z důvodu uplatnění tolerance pozdního doručení mohou být staré události vyřazeny. Pokud jsou události příliš staré, hodnota **System. timestamp** se během příjmu události změní. V důsledku tohoto chování je aktuálně Azure Stream Analytics více možností pro scénáře zpracování událostí prakticky v reálném čase místo historických scénářů zpracování událostí. Můžete nastavit **události, které dorazí pozdě** na největší možnou hodnotu (20 dní), aby se toto chování mohlo v některých případech vyřešit.

3. Výstupy se zdají být zpožděné.

   První meze se vygeneruje v počítaném čase: **Maximální doba události** , kterou systém doposud zjistil, mínus velikost okna tolerance mimo pořadí. Ve výchozím nastavení je tolerance mimo pořadí nakonfigurovaná na hodnotu nula (00 minut a 00 sekund). Když nastavíte tuto hodnotu na vyšší, nenulovou časovou hodnotu, bude první výstup úlohy streamování zpožděn o tuto hodnotu času (nebo vyšší) z důvodu vypočtené doby prvního vodoznaku.

4. Vstupy jsou zhuštěné.

   Pokud v daném oddílu není žádný vstup, čas meze se vypočítává jako **čas doručení** mínus okno tolerance zpožděného doručení. Výsledkem je, že pokud jsou vstupní události zřídka a zhuštěné, může být výstup po určitou dobu zpožděn. Výchozí **události, které přijdou do pozdní hodnoty,** jsou 5 sekund. Měli byste očekávat, že při posílání vstupních událostí dojde k jednomu zpoždění, například. Při nastavování **událostí, které přijdou zpožděným** oknu na velkou hodnotu, může dojít ke horšímu zpoždění.

5. Hodnota **System. timestamp** se liší od času v poli **Doba události** .

   Jak bylo popsáno dříve, systém upraví čas události v oknech tolerance mimo pořadí nebo zpoždění doručení. Hodnota **System. timestamp** události je upravena, ale ne pole **čas události** . To lze použít k identifikaci událostí, které byly upraveny pomocí časových razítek. V případě, že systém změnil časové razítko z důvodu jedné z tolerancí, obvykle jsou stejné.

## <a name="metrics-to-observe"></a>Metriky, které se mají sledovat

Pomocí [metriky úloh Stream Analytics](stream-analytics-monitoring.md)můžete sledovat počet efektů tolerance doby řazení událostí. Jsou relevantní následující metriky:

|Metrika  | Popis  |
|---------|---------|
| **Události mimo pořadí** | Označuje počet událostí, které byly obdrženy mimo pořadí, které byly buď vyřazeny nebo předány upravené časové razítko. Tato metrika je přímo ovlivněna konfigurací nastavení **události mimo pořadí** na stránce **řazení událostí** na úloze v Azure Portal. |
| **Zpožděné vstupní události** | Určuje počet událostí přicházejících pozdě ze zdroje. Tato metrika zahrnuje události, které byly vyřazeny nebo bylo upraveno jejich časové razítko. Tato metrika je přímo ovlivněna konfigurací **událostí, které dorazí** na stránce **řazení událostí** na úlohu v Azure Portal. |
| **Události předčasného vstupu** | Určuje počet událostí přicházejících do začátku ze zdroje, který byl buď vyřazen, nebo jejich časové razítko bylo upraveno, pokud jsou starší než 5 minut. |
| **Zpoždění vodoznaku** | Určuje zpoždění úlohy zpracování streamování dat. Další informace najdete v následující části.|

## <a name="watermark-delay-details"></a>Podrobnosti o zpoždění vodoznaku

Metrika **zpoždění meze** je vypočítána jako čas na zdi v uzlu zpracování minus největší vodoznak, který zatím viděli. Další informace najdete v [blogovém příspěvku o zpoždění vodoznaku](https://azure.microsoft.com/blog/new-metric-in-azure-stream-analytics-tracks-latency-of-your-streaming-pipeline/).

Tato hodnota metriky je větší než 0 v normálním provozu, může to mít několik příčin:

1. Vlastní zpoždění zpracování kanálu streamování. Obvykle je toto zpoždění nominální.

2. Okno tolerance mimo pořadí zavedlo zpoždění, protože meze je zmenšena o velikost okna tolerance.

3. Okno opožděné doručení zavedlo zpoždění, protože meze je zmenšena o velikost okna tolerance.

4. Časový posun uzlu zpracování, který generuje metriku

Existuje několik dalších omezení prostředků, které můžou způsobit zpomalení kanálu streamování. Metrika zpoždění meze může proniknout z těchto důvodů:

1. Pro zpracování objemu vstupních událostí není v Stream Analytics dostatek prostředků pro zpracování. Postup pro horizontální navýšení kapacity najdete v tématu [pochopení a úprava jednotek streamování](stream-analytics-streaming-unit-consumption.md).

2. V rámci zprostředkovatelů vstupních událostí není dostatečná propustnost, takže jsou omezeny. Možná řešení najdete v tématu [Automatické škálování jednotek propustnosti Azure Event Hubs](../event-hubs/event-hubs-auto-inflate.md).

3. Výstupní jímky nejsou zřízené s dostatečnou kapacitou, takže jsou omezené. Možná řešení se výrazně liší v závislosti na charakteru používané výstupní služby.

## <a name="output-event-frequency"></a>Frekvence výstupní události

Azure Stream Analytics využívá jako jediný Trigger k vytváření výstupních událostí průběh meze. Vzhledem k tomu, že je vodoznak odvozen ze vstupních dat, je možné ho opakovat během obnovování selhání a také při opakovaném zpracování iniciované uživatelem. Při použití [agregací](stream-analytics-window-functions.md)s využitím okna vytvoří služba pouze výstupy na konci okna. V některých případech mohou uživatelé chtít zobrazit částečné agregace vygenerované v systému Windows. Částečné agregace nejsou aktuálně podporovány v Azure Stream Analytics.

V jiných řešeních pro streamování můžou být výstupní události v různých spouštěcích bodech materializované v závislosti na externích okolnostech. V některých řešeních je možné, že výstupní události pro dané časové okno můžou být vygenerované víckrát. Vzhledem k upřesnění vstupních hodnot se agregované výsledky stanou přesnější. Události by se daly v prvé době spekulativní a v průběhu času byly revidovány. Pokud je třeba určité zařízení offline ze sítě, může systém použít odhadovanou hodnotu. Později bude stejné zařízení online v síti. Skutečná data události pak mohou být součástí vstupního datového proudu. Výstup výsledky ze zpracování, které vytváří přesnější výstup.

## <a name="illustrated-example-of-watermarks"></a>Ukázka zobrazených vodoznaků

Následující obrázky znázorňují, jak vodoznaky probíhají v různých případech.

V této tabulce jsou uvedena ukázková data, která jsou v grafu níže. Všimněte si, že čas události a čas doručení se liší, někdy se shodují a někdy ne.

| Čas události | Čas doručení | DeviceId |
| --- | --- | --- |
| 12:07 | 12:07 | device1
| 12:08 | 12:08 | device2
| 12:17 | 12:11 | device1
| 12:08 | 12:13 | device3
| 12:19 | 12:16 | device1
| 12:12 | 12:17 | device3
| 12:17 | 12:18 | device2
| 12:20 | 12:19 | device2
| 12:16 | 12:21 | device3
| 12:23 | 12:22 | device2
| 12:22 | 12:24 | device2
| 12:21 | 12:27 | device3

Na tomto obrázku jsou použity následující tolerance:

- Okna včasného doručení je 5 minut.
- Okno s pozdním doručením je 5 minut.
- Změna pořadí okna je 2 minuty.

1. Ukázka průběhu těchto událostí:

   ![Obrázek vodoznaku Azure Stream Analytics](media/stream-analytics-time-handling/WaterMark-graph-1.png)

   Významné procesy znázorněné na předchozím obrázku:

   1. První událost (zařízení1) a druhá událost (Device2) zarovnala časy a jsou zpracovávány bez úprav. Meze probíhá u každé události.

   2. Když je zpracována třetí událost (zařízení1), doba doručení (12:11) předchází času události (12:17). Událost se dorazila 6 minut včas, takže se událost zahodila z důvodu tolerance prvotního doručení 5 minut.

      V tomto případě předčasné události v tomto případě vodoznak nepokračuje.

   3. Čtvrtá událost (device3) a pátá událost (zařízení1) zarovnala časy a jsou zpracovávány bez úprav. Meze probíhá u každé události.

   4. Po zpracování šesté události (device3) je čas doručení (12:17) a čas události (12:12) pod úrovní meze. Čas události se upraví na úroveň hladiny vodního měřítka (12:17).

   5. Po zpracování dvanácté události (device3) je čas doručení (12:27) 6 minut před časem události (12:21). Použije se zásada pozdního doručení. Doba události je upravena (12:22), která přesahuje meze (12:21), takže se nepoužije žádná další úprava.

2. Druhá ukázka průběhu meze bez zásad prvotního doručení:

   ![Obrázek neAzure Stream Analytics žádného vodoznaku prvotní zásady](media/stream-analytics-time-handling/watermark-graph-2.png)

   V tomto příkladu nejsou aplikovány žádné zásady předčasného doručení. Izolované události, které přinášejí předčasné zvýšení meze. Všimněte si, že třetí událost (deviceId1 v čase 12:11) není v tomto scénáři zahozena a mez je vyvolána na 12:15. Čtvrtá doba události se v důsledku toho upraví o 7 minut (12:08 až 12:15).

3. V konečné ilustraci se používají podproudy (přes DeviceId). Je sledováno více vodoznaků, jeden pro každý datový proud. V důsledku toho je počet událostí s jejich časem upravený.

   ![Obrázek vodoznaku Azure Stream Analytics podproudy](media/stream-analytics-time-handling/watermark-graph-3.png)

## <a name="next-steps"></a>Další kroky

- [Azure Stream Analytics – požadavky na pořadí událostí](stream-analytics-out-of-order-and-late-events.md)
- [Stream Analytics metriky úloh](stream-analytics-monitoring.md)
