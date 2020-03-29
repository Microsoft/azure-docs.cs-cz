---
title: Pochopení zpracování času ve službě Azure Stream Analytics
description: Zjistěte, jak funguje zpracování času ve službě Azure Stream Analytics, například jak zvolit nejlepší čas zahájení, jak zpracovat pozdní a rané události a metriky zpracování času.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/05/2018
ms.openlocfilehash: 367b7c2e1ce1c8b3c0dbc02003218b76096b409d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75354651"
---
# <a name="understand-time-handling-in-azure-stream-analytics"></a>Pochopení zpracování času ve službě Azure Stream Analytics

V tomto článku se zabýváme tím, jak můžete provést volby návrhu k řešení praktických problémů s časem ve službě Azure Stream Analytics. Rozhodnutí o návrhu zpracování času úzce souvisejí s faktory řazení událostí.

## <a name="background-time-concepts"></a>Koncepty času na pozadí

Chcete-li lépe zarámovat diskusi, definujte některé koncepty pozadí:

- **Čas události**: Čas, kdy došlo k původní události. Například, když se jedoucí auto na dálnici blíží k mýtné budce.

- **Doba zpracování**: Čas, kdy událost dosáhne systému zpracování a je pozorována. Například, když snímač mýtného stánku vidí auto a počítačový systém trvá několik okamžiků, než data zpracuje.

- **Vodoznak**: Značka času události, která označuje, do jakého bodu byly události příchozí do streamovacího procesoru. Vodoznaky umožňují systému označit jasný pokrok při požití událostí. Podle povahy datových proudů se data příchozích událostí nikdy nezastaví, takže vodoznaky označují průběh do určitého bodu v datovém proudu.

   Koncept vodoznaku je důležitý. Vodoznaky umožňují službě Stream Analytics určit, kdy může systém vytvářet úplné, správné a opakovatelné výsledky, které není nutné zasunout. Zpracování lze provést zaručeným způsobem, který je předvídatelný a opakovatelný. Například pokud přepočítání je třeba provést pro některé podmínky zpracování chyb, vodoznaky jsou bezpečné počáteční a koncové body.

Jako další zdroje na toto téma, viz Tyler Akidau blogu [Streaming 101](https://www.oreilly.com/ideas/the-world-beyond-batch-streaming-101) a [Streaming 102](https://www.oreilly.com/ideas/the-world-beyond-batch-streaming-102).

## <a name="choosing-the-best-starting-time"></a>Výběr nejlepšího počátečního času

Stream Analytics poskytuje uživatelům dvě možnosti pro výdej času události:

1. **Čas příjezdu**  

   Čas příjezdu je přiřazen ve vstupním zdroji, když událost dosáhne zdroje. Čas příjezdu můžete přistupovat pomocí vlastnosti **EventEnqueuedUtcTime** pro vstupy centra událostí, vlastnost **IoTHub.EnqueuedTime** pro službu IoT Hub a pomocí vlastnosti **BlobProperties.LastModified** pro vstup objektu blob.

   Použití čas příjezdu je výchozí chování a nejlépe použít pro scénáře archivace dat, kde není nutná žádná časová logika.

2. **Čas aplikace** (také pojmenovaný Čas události)

   Čas aplikace je přiřazen při generování události a je součástí datové části události. Chcete-li zpracovat události podle času aplikace, použijte **klauzuli Timestamp by** ve výběrovém dotazu. Pokud **timestamp podle** klauzule chybí, události jsou zpracovány podle času doručení.

   Je důležité použít časové razítko v datové části, pokud se jedná o temporální logiku. Tímto způsobem lze započítávat zpoždění ve zdrojovém systému nebo v síti.

## <a name="how-time-progresses-in-azure-stream-analytics"></a>Jak ve Službě Azure Stream Analytics postupuje čas

Při použití času aplikace je časový průběh založen na příchozích událostech. Je obtížné pro systém zpracování datového proudu vědět, pokud nejsou žádné události, nebo pokud jsou zpožděny události. Z tohoto důvodu Azure Stream Analytics generuje heuristické vodoznaky následujícími způsoby pro každý vstupní oddíl:

1. Kdykoli dojde k příchozí události, vodoznak je největší čas události, který jsme dosud viděli, mínus velikost okna tolerance mimo pořadí.

2. Vždy, když se nejedná o příchozí událost, vodoznak je aktuální odhadovaný čas příjezdu (uplynulý čas na pozadí virtuálního počítači zpracování událostí od posledního okamžiku vstupní události je vidět plus čas příchodu vstupní události) minus pozdní doručení tolerance okna.

   Čas příjezdu lze pouze odhadnout, protože skutečný čas příjezdu se generuje na vstupní události broker, jako jsou centra událostí, a ne Azure Stream Analytics virtuálního počítače zpracování událostí.

Konstrukce slouží dvěma dalším účelům, kromě generování vodoznaků:

1. Systém generuje výsledky včas s příchozími událostmi nebo bez něj.

   Máte kontrolu nad tím, jak včas chtějí zobrazit výstupní výsledky. Na webu Azure Portal můžete na stránce **řazení událostí** úlohy Stream Analytics nakonfigurovat nastavení události **mimo pořadí.** Při konfiguraci tohoto nastavení zvažte kompromis timeliness s tolerancí událostí mimo pořadí v datovém proudu událostí.

   Pozdní příjezd tolerance okno je důležité, aby generování vodoznaků, a to i v nepřítomnosti příchozích událostí. V době může být období, kdy žádné příchozí události přijít, například když vstupní datový proud události je řídký. Tento problém je umocněn použitím více oddílů ve vstupní mstivosti událostí.

   Systémy zpracování dat streamování bez pozdního příchodu toleranční okno může trpět zpožděné výstupy při vstupy jsou řídké a více oddílů jsou používány.

2. Chování systému musí být opakovatelné. Opakovatelnost je důležitou vlastností systému zpracování dat datových proudů.

   Vodoznak je odvozen od času příjezdu a času aplikace. Oba jsou trvalé v zprostředkovatele událostí, a proto opakovatelné. V případě, že čas příjezdu musí být odhadnutv nepřítomnosti událostí, Azure Stream Analytics deníky odhadovaný čas doručení pro opakovatelnost během přehrání za účelem obnovení selhání.

Všimněte si, že pokud se rozhodnete použít **čas příjezdu** jako čas události, není nutné konfigurovat toleranci mimo pořadí a pozdní toleranci doručení. Vzhledem k tomu, že je zaručeno, že **čas příjezdu** se ve vstupním zprostředkovateli událostí pravděpodobně zvýší, Azure Stream Analytics jednoduše ignoruje konfigurace.

## <a name="late-arriving-events"></a>Pozdní příjezdové akce

Podle definice pozdní hod u pozdního doručení azure stream analytics pro každou příchozí událost porovná **čas události** s **časem příjezdu**; Pokud je čas události mimo okno tolerance, můžete nakonfigurovat systém tak, aby událost buď přešel, nebo upravil čas události tak, aby byl v rámci tolerance.

Vezměte v úvahu, že po vodoznaky jsou generovány, služba může potenciálně přijímat události s časem události nižší než vodoznak. Službu můžete nakonfigurovat tak, aby tyto události **buď přetavila,** nebo **upravit** čas události na hodnotu vodoznaku.

Jako součást úpravy **je system.timerazítk** události nastaven na novou hodnotu, ale samotné časové pole **události** se nezmění. Tato úprava je jediná situace, kdy **se časové razítko** události může lišit od hodnoty v časovém poli události a může způsobit generování neočekávaných výsledků.

## <a name="handling-time-variation-with-substreams"></a>Změna doby zpracování s dílčími proudy

Heuristický mechanismus generování vodoznaku popsaný zde funguje dobře ve většině případů, kdy je čas většinou synchronizován mezi různými odesílateli událostí. V reálném životě, zejména v mnoha scénářích IoT, však systém má malou kontrolu nad hodinami na odesílatele událostí. Odesílatelé událostí mohou být všechny druhy zařízení v terénu, možná na různých verzích hardwaru a softwaru.

Namísto použití globálního vodoznaku pro všechny události ve vstupním oddílu má Stream Analytics jiný mechanismus nazývaný substreamy, který vám pomůže. Můžete využít substreams ve vaší práci písemně pracovní dotaz, který používá [**klauzule TIMESTAMP BY**](/stream-analytics-query/timestamp-by-azure-stream-analytics) a klíčové slovo **OVER**. Chcete-li označit dílčí datový proud, zadejte název klíčového slova **OVER,** například `deviceid`, aby systém u platého sloupce uplatňuji časové zásady. Každý podproud získá svůj vlastní nezávislý vodoznak. Tento mechanismus je užitečné povolit včasné generování výstupu, při práci s velké hodiny zkosení nebo zpoždění sítě mezi odesílateli událostí.

Substreamy jsou jedinečné řešení poskytované službou Azure Stream Analytics a nejsou nabízeny jinými systémy pro zpracování dat streamování. Stream Analytics použije okno tolerance pozdního doručení na příchozí události při použití substreamů. Výchozí nastavení (5 sekund) je pravděpodobně příliš malé pro zařízení s odlišnými časovými razítky. Doporučujeme začít s 5 minut, a provést úpravy podle jejich zařízení hodiny zkosení vzor.

## <a name="early-arriving-events"></a>Události včasného příjezdu

Možná jste si všimli jiného konceptu nazvaného okno předčasného příjezdu, který vypadá jako opak okna tolerance pozdního příjezdu. Toto okno je opraveno na 5 minut a slouží jinému účelu od pozdního příjezdu.

Vzhledem k tomu, že Azure Stream Analytics zaručuje, že vždy generuje úplné výsledky, můžete zadat pouze **čas zahájení úlohy** jako první výstupní čas úlohy, nikoli vstupní čas. Čas zahájení úlohy je vyžadován tak, aby bylo zpracováno celé okno, nikoli pouze ze středu okna.

Stream Analytics pak odvozuje počáteční čas ze specifikace dotazu. Protože je však zprostředkovatel vstupních událostí indexován pouze podle času doručení, systém musí přeložit čas počáteční události do času příjezdu. Systém může spustit zpracování událostí z tohoto okamžiku ve vstupním zprostředkovateli událostí. S častým limitem pro příchod oken je překlad jednoduchý. Začíná čas události mínus 5minutové okno předčasně. Tento výpočet také znamená, že systém zahodí všechny události, které jsou vidět s čas události 5 minut větší než čas příjezdu.

Tento koncept se používá k zajištění zpracování je opakovatelné bez ohledu na to, kde začnete výstup z. Bez takového mechanismu by nebylo možné zaručit opakovatelnost, jak tvrdí mnoho jiných streamovacích systémů.

## <a name="side-effects-of-event-ordering-time-tolerances"></a>Vedlejší účinky časových tolerancí řazení událostí

Úlohy Služby Stream Analytics mají několik možností **řazení událostí.** Dvě lze nakonfigurovat na webu Azure Portal: nastavení **události mimo pořadí** (tolerance mimo pořadí) a **události, které dorazí pozdní** nastavení (pozdní doručení tolerance). Tolerance **předčasného příjezdu** je pevná a nelze ji upravit. Tyto časové zásady používá Stream Analytics k zajištění silných záruk. Tato nastavení však mají některé někdy neočekávané důsledky:

1. Náhodné odeslání událostí, které jsou příliš brzy.

   Časné události by neměly být vydávány normálně. Je možné, že časné události jsou odesílány do výstupu, pokud jsou hodiny odesílatele spuštěny příliš rychle. Všechny události včasného příjezdu jsou vynechány, takže neuvidíte žádnou z nich z výstupu.

2. Odesílání starých událostí do centra událostí, která mají být zpracována službou Azure Stream Analytics.

   Zatímco staré události se mohou zpočátku zdát neškodné, z důvodu použití tolerance pozdního příjezdu, staré události mohou být vynechány. Pokud jsou události příliš staré, hodnota **System.Timestamp** se změní během ingestování události. Z důvodu tohoto chování je azure stream analytics vhodnější pro scénáře zpracování událostí téměř v reálném čase namísto historických scénářů zpracování událostí. Události, **které dorazí pozdě** čas na největší možnou hodnotu (20 dní) obejít toto chování v některých případech.

3. Výstupy se zdají být zpožděny.

   První vodoznak je generován v době výpočtu: **maximální čas události, který** systém dosud doporučoval, mínus velikost okna tolerance mimo pořadí. Ve výchozím nastavení je tolerance mimo pořadí nakonfigurována na nulu (00 minut a 00 sekund). Pokud jej nastavíte na vyšší, nenulovou časovou hodnotu, první výstup úlohy streamování je zpožděn o tuto hodnotu času (nebo vyšší) z důvodu prvního času vodoznaku, který je vypočten.

4. Vstupy jsou řídké.

   Pokud v daném oddílu není žádný vstup, čas vodoznaku se vypočítá jako **čas příjezdu** mínus okno tolerance pozdního doručení. V důsledku toho pokud vstupní události jsou zřídka a řídké, výstup může být zpožděn o tuto dobu. Výchozí **události, které dorazí pozdní** hodnota je 5 sekund. Měli byste očekávat, že některé zpoždění při odesílání vstupníudálosti jeden po druhém, například. Zpoždění může zhoršit, když nastavíte **události, které dorazí pozdě** okno na velkou hodnotu.

5. **System.Timestamp** hodnota se liší od času v časovém poli **události.**

   Jak je popsáno výše, systém upravuje čas události podle mimopořadí tolerance nebo pozdní doručení tolerance okna. **System.Timestamp** hodnota události je upravena, ale ne časové pole **události.**

## <a name="metrics-to-observe"></a>Metriky ke sledování

Můžete sledovat řadu efektů tolerance času řazení událostí prostřednictvím [metrik úlohy Stream Analytics](stream-analytics-monitoring.md). Následující metriky jsou relevantní:

|Metrika  | Popis  |
|---------|---------|
| **Události mimo pořadí** | Označuje počet událostí přijatých mimo pořadí, které byly vynechány nebo upraveno časové razítko. Tato metrika je přímo ovlivněna konfigurací nastavení **Události mimo pořadí** na stránce řazení **událostí** na úloze na webu Azure Portal. |
| **Události pozdního vstupu** | Označuje počet událostí, které přicházejí pozdě ze zdroje. Tato metrika zahrnuje události, které byly vynechány nebo byly upraveny jejich časové razítko. Tato metrika je přímo ovlivněna konfigurací **události, které dorazí pozdě** nastavení na stránce **pořadí událostí** na úlohy na webu Azure Portal. |
| **Události včasného vstupu** | Označuje počet událostí, které přicházejí brzy ze zdroje, které byly vynechány nebo jejich časové razítko bylo upraveno, pokud jsou více než 5 minut dříve. |
| **Zpoždění vodoznaku** | Označuje zpoždění úlohy zpracování dat datových proudů. Další informace naleznete v následující části.|

## <a name="watermark-delay-details"></a>Podrobnosti o zpoždění vodoznaku

Metrika **zpoždění vodoznaku** se vypočítá jako čas nástěnných hodin uzlu zpracování mínus největší vodoznak, který dosud viděl. Další informace naleznete v [příspěvku blogu o zpoždění vodoznaku](https://azure.microsoft.com/blog/new-metric-in-azure-stream-analytics-tracks-latency-of-your-streaming-pipeline/).

Může existovat několik důvodů, proč je tato hodnota metriky větší než 0 při normálním provozu:

1. Vlastní zpoždění zpracování kanálu streamování. Obvykle je toto zpoždění nominální.

2. Okno tolerance mimo pořadí zavedlo zpoždění, protože vodoznak je zmenšen o velikost okna tolerance.

3. Okno pozdního příjezdu zavedlo zpoždění, protože vodoznak je zmenšen o velikost okna tolerance.

4. Hodiny zkosení uzlu zpracování generování metriky.

Existuje řada dalších omezení prostředků, které mohou způsobit zpomalení kanálu streamování. Metrika zpoždění vodoznaku se může zvýšit v důsledku:

1. Nedostatek prostředků zpracování v Stream Analytics pro zpracování objemu vstupních událostí. Informace o škálování prostředků naleznete v [tématu Principy a úpravy jednotek streamování](stream-analytics-streaming-unit-consumption.md).

2. Nedostatek propustnost v rámci zprostředkovatelů vstupní události, takže jsou omezeny. Možná řešení najdete [v tématu Automaticky vertikálně navýšit kapacitu jednotek propustností centra událostí Azure](../event-hubs/event-hubs-auto-inflate.md).

3. Výstupní propady nejsou zřízeny s dostatečnou kapacitou, takže jsou omezeny. Možná řešení se značně liší v závislosti na chuti používané výstupní služby.

## <a name="output-event-frequency"></a>Frekvence výstupních událostí

Azure Stream Analytics používá průběh vodoznaku jako jedinou aktivační událost k vytváření výstupních událostí. Vzhledem k tomu, že vodoznak je odvozen ze vstupních dat, je opakovatelný během obnovení selhání a také v opětovném zpracování iniciovaném uživatelem.

Při použití [windowed agregace](stream-analytics-window-functions.md), služba vytváří pouze výstupy na konci oken. V některých případech mohou uživatelé chtít zobrazit částečné agregace generované z oken. Částečné agregace nejsou aktuálně podporované ve službě Azure Stream Analytics.

V jiných řešeních streamování mohou být výstupní události zhmotněny v různých spouštěcích bodech v závislosti na vnějších okolnostech. V některých řešeních je možné, že výstupní události pro dané časové okno mohou být generovány vícekrát. Při upřesnění vstupních hodnot se agregační výsledky stávají přesnějšími. Události by mohly být nejprve spekulovány a revidovány v průběhu času. Pokud je například určité zařízení offline ze sítě, může systém použít odhadovanou hodnotu. Později se stejné zařízení přepne do sítě. Potom skutečná data událostí mohou být zahrnuty do vstupního datového proudu. Výstup je výsledkem zpracování tohoto časového okna vytváří přesnější výstup.

## <a name="illustrated-example-of-watermarks"></a>Ilustrovaný příklad vodoznaků

Následující obrázky ilustrují průběh vodoznaků za různých okolností.

V této tabulce jsou uvedena ukázková data, která jsou uvedena níže. Všimněte si, že čas události a čas příjezdu se liší, někdy odpovídající a někdy ne.

| Čas události | Čas příjezdu | DeviceId |
| --- | --- | --- |
| 12:07 | 12:07 | device1
| 12:08 | 12:08 | device2
| 12:17 | 12:11 | device1
| 12:08 | 12:13 | zařízení3
| 12:19 | 12:16 | device1
| 12:12 | 12:17 | zařízení3
| 12:17 | 12:18 | device2
| 12:20 | 12:19 | device2
| 12:16 | 12:21 | zařízení3
| 12:23 | 12:22 | device2
| 12:22 | 12:24 | device2
| 12:21 | 12:27 | zařízení3

Na tomto obrázku se používají následující tolerance:

- Okna pro předčasný příjezd je 5 minut
- Pozdní příjezdové okno je 5 minut
- Okno pro přiobjednání je 2 minuty

1. Obrázek vodoznaku postupujícího těmito událostmi:

   ![Ilustrace vodoznaku Azure Stream Analytics](media/stream-analytics-time-handling/WaterMark-graph-1.png)

   Pozoruhodné procesy znázorněné na předchozí grafice:

   1. První událost (device1) a druhá událost (device2) mají zarovnané časy a jsou zpracovány bez úprav. Vodoznak postupuje na každé události.

   2. Při zpracování třetí události (device1) čas příjezdu (12:11) předchází čas události (12:17). Událost dorazila o 6 minut dříve, takže událost je zrušena kvůli pětiminutové toleranci předčasného příjezdu.

      Vodoznak nepostupuje v tomto případě předčasné události.

   3. Čtvrtá událost (device3) a pátá událost (device1) mají zarovnané časy a jsou zpracovány bez úprav. Vodoznak postupuje na každé události.

   4. Při zpracování šesté události (device3) čas příjezdu (12:17) a čas události (12:12) je pod úrovní vodoznaku. Čas události se upraví na úroveň vodoznaku (12:17).

   5. Při zpracování dvanácté události (device3) je čas příjezdu (12:27) 6 minut před časem události (12:21). Platí se podmínky pozdního příjezdu. Čas události je upraven (12:22), který je nad vodoznakem (12:21), takže není aplikována žádná další úprava.

2. Druhý příklad vodoznaku postupuje bez politiky včasného příjezdu:

   ![Azure Stream Analytics žádné včasné zásady vodoznak ilustrace](media/stream-analytics-time-handling/watermark-graph-2.png)

   V tomto příkladu se neuplatňuje žádné zásady předčasného příjezdu. Odlehlé události, které dorazí brzy zvýšit vodoznak výrazně. Všimněte si, že třetí událost (deviceId1 v čase 12:11) není vynechána v tomto scénáři a vodoznak je aktivována na 12:15. Čtvrtý čas události je upraven dopředu 7 minut (12:08 až 12:15).

3. Na posledním obrázku se používají dílčí proudy (OVER DeviceId). Sleduje se více vodoznaků, jeden na jeden proud. Existuje méně událostí s jejich časy upravené jako výsledek.

   ![Azure Stream Analytics podstreamuje ilustraci vodoznaku](media/stream-analytics-time-handling/watermark-graph-3.png)

## <a name="next-steps"></a>Další kroky

- [Důležité informace o pořadí událostí Azure Stream Analytics](stream-analytics-out-of-order-and-late-events.md)
- [Metriky úloh Služby Stream Analytics](stream-analytics-monitoring.md)
