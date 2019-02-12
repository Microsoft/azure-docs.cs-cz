---
title: Vysvětlení čas zpracování ve službě Azure Stream Analytics
description: Další informace o čas zpracování ve službě Azure Stream Analytics
author: jasonwhowell
ms.author: zhongc
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 02/05/2018
ms.openlocfilehash: 4accff7410d17e76a000b7cef957b75c65a16960
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2019
ms.locfileid: "56008388"
---
# <a name="understand-time-handling-in-azure-stream-analytics"></a>Vysvětlení čas zpracování ve službě Azure Stream Analytics

V tomto článku probereme, jak můžete provést volby při návrhu k řešení praktických čas zpracování problémy ve službě Azure Stream Analytics. Čas zpracování návrhu, že rozhodnutí, která jsou úzce souvisí s faktory řazení událostí. Další informace najdete v článku související: [Azure Stream Analytics události pořadí aspekty](stream-analytics-out-of-order-and-late-events.md).

## <a name="background-time-concepts"></a>Koncepty čas

Na lepší rámec diskuse nadefinujeme některé koncepty:

- **Čas události**: Čas, kdy původní události došlo. Například pokud přesunutí automobilu na komunikacích se blíží stánku linka.

- **Doba zpracování**: Čas události dosáhne systému zpracování a se vyskytuje. Například když snímačů z mýtných bran linka uvidí Auto a počítači chvíli trvá zpracovat data.

- **Vodoznak**: Značka čas události, která označuje všechny události až do bodu byl příchozího přenosu dat do datového proudu procesoru. Vodoznaky nechejte systém indikoval průběh vymazat ingestuje události. Vzhledem k povaze datové proudy příchozích dat událostí nikdy nezastaví, takže vodoznaky informace o průběhu do určité míry v datovém proudu.

   Vodoznak koncept je důležitý. Vodoznaky povolit Stream Analytics můžete určit, kdy systém může vytvářet kompletní a správný a opakovatelné výsledky, které nemusí být stažen. Zaručené tak, aby je předvídatelný a Opakovatelný může dokončení zpracování. Například pokud počet slov je třeba udělat pro podmínku ošetření některých chyb, vodoznaky jsou bezpečné počáteční a koncové body.

Jako další zdroje informací k tomuto tématu, najdete v blogových příspěvků Tyler Akidau [streamování 101](https://www.oreilly.com/ideas/the-world-beyond-batch-streaming-101) a [streamování 102](https://www.oreilly.com/ideas/the-world-beyond-batch-streaming-102).

## <a name="choosing-the-best-starting-time"></a>Výběr nejlepší počáteční čas

Stream Analytics poskytuje uživatelům dvě možnosti pro výběr čas události:

1. **Čas přijetí**  

   Čas přijetí jsou přiřazeny v vstupní zdroj události dosáhne zdroji. Čas doručení přístupné prostřednictvím **EventEnqueuedUtcTime** vlastnost pro Event Hubs vstupy **IoTHub.EnqueuedTime** vlastnost pro službu IoT Hub a pomocí **BlobProperties.LastModified**  vlastností pro objekt blob vstup.

   Použití čas doručení je výchozí chování a nejvhodnější používat pro scénáře, archivaci dat tam, kde je nezbytné žádné temporální logiku.

2. **Čas aplikace** (nazývaná také čas události)

   Čas aplikace se přiřadí při vygenerování události a je součástí datové části události. Chcete-li zpracovávat události podle času aplikace, použijte **časového razítka podle** klauzule v dotazu select. Pokud **časového razítka podle** chybí klauzule, čas doručení zpracovává události.

   Je třeba použít časové razítko v datové části, když je zahrnuta temporální logiku. Tímto způsobem, zpoždění ve zdrojovém systému nebo v síti můžou být zahrnuté.

## <a name="how-time-progresses-in-azure-stream-analytics"></a>Jak se postupuje času ve službě Azure Stream Analytics

Při použití aplikace průběhu času podle příchozí události. Je obtížné pro systém vědět, pokud neexistují žádné události, nebo pokud jsou zpožděné události zpracování datových proudů. Z tohoto důvodu se generuje Azure Stream Analytics heuristické vodoznaky pro každý vstupní oddíl následujícími způsoby:

1. Pokaždé, když neexistuje žádné příchozí události, mez je největší čas události, které jsme zatím viděli minus velikost okna pro toleranci mimo pořadí.

2. Pokaždé, když neexistuje žádné příchozí události, vodoznaku je aktuální čas odhadované doručení (uplynulý čas na pozadí zpracovává události od poslední návštěvy vstupní události je zobrazena plus danou vstupní čas přijetí události virtuálního počítače) minus pozdní okno tolerance pozdního přijetí.

   Čas doručení pouze se dá odhadnout, vzhledem k tomu, že čas doručení skutečné vygenerovaný na zprostředkovatele vstupních událostí, jako jsou Event Hubs a ne Azure Stream Analytics virtuální počítač zpracováním událostí.

Návrh má dva další účely, kromě generování vodoznaky:

1. Systém vygeneruje výsledky včas s nebo bez něj příchozí události.

   Budete mít kontrolu nad jak včas chce vidět výstupní výsledky. Na webu Azure Portal na **řazení událostí** stránky vaší úlohy Stream Analytics můžete nakonfigurovat **události mimo pořadí** nastavení. Při konfiguraci tohoto nastavení, zvažte kompromis včasnosti s tolerance mimo pořadí událostí do datového proudu událostí.

   Pozdní okno tolerance pozdního přijetí je důležité zachovat generování vodoznaky, dokonce i v případě neexistence příchozí události. V některých případech může být určité odkud pochází žádné příchozí události, například pokud je zhuštěný vstupního datového proudu událostí. Tento problém je vyvolána malé vzdálenosti mezi použitím většího počtu oddílů ve vstupní události Service broker.

   Streamování systémy zpracování dat bez pozdní okno tolerance pozdního přijetí může být velmi krátká zpožděné výstupy při vstupy jsou zhuštěné a jsou používány více oddílů.

2. Chování systému musí být opakovatelné. Opakovatelnosti je důležité vlastnosti systému zpracování dat streamování.

   Vodoznak je odvozený od čas doručení a čas aplikace. Obě jsou trvalé v zprostředkovatele událostí a proto opakovatelné. V případě čas doručení musí odhadnout neexistence událostí, Azure Stream Analytics deníky čas odhadované doručení pro opakovatelnost během opětovného přehrání pro účely obnovení po selhání.

Všimněte si, že pokud budete chtít použít **čas doručení** jako čas události, není nutné konfigurovat tolerance mimo pořadí a tolerance pozdního přijetí. Protože **čas doručení** je zaručeno, že bude zvětšovat zvětšovat ve vstupní události Service broker, Azure Stream Analytics jednoduše ignoruje konfigurace.

## <a name="late-arriving-events"></a>Opravovány událostí

Podle definice pozdní okno tolerance pozdního přijetí pro každého příchozí události, porovnává Azure Stream Analytics **čas události** s **čas doručení**; Pokud čas události je mimo okno tolerance je můžete nakonfigurovat systém a vyřadit události nebo upravit události. čas musí být v tolerance.

Vezměte v úvahu, že po vygenerování vodoznaky, můžete službu potenciálně přijímat události s nižší než vodoznak čas události. Můžete nakonfigurovat službu buď **vyřadit** tyto události nebo **upravit** čas události hodnoty meze.

Jako součást úpravy, události od **System.Timestamp** je nastavena na novou hodnotu, ale **čas události** vlastní pole se nezmění. Toto nastavení je jedině tam, kde události **System.Timestamp** může lišit od hodnoty v poli času události a může vést k neočekávaným výsledkům, které chcete vygenerovat.

## <a name="handling-time-variation-with-substreams"></a>Čas změny s substreams zpracování

Generování mechanismus heuristické vodoznak popsaný tady funguje dobře ve většině případů, ve kterém je většinou synchronizované mezi různými odesílatelé událostí. Však v reálném životě, zejména v mnoha scénářích IoT, systém má málo kontrolu nad hodin na odesílatelé událostí. Odesílatelé událostí může být nejrůznější zařízení v terénu, možná na různé verze hardwaru a softwaru.

Místo použití meze globální do všech událostí do vstupního oddílu, Stream Analytics má jiný mechanismus volá substreams vám pomůže. V úloze, můžete využít substreams napsáním úlohy dotaz, který používá [ **TIMESTAMP BY** ](/stream-analytics-query/timestamp-by-azure-stream-analytics) klauzule a klíčové slovo **přes**. Pokud chcete určit, podproudů, zadejte název sloupce klíče po **přes** – klíčové slovo, například `deviceid`tak, aby systém použije zásady času podle sloupce. Každý podproudů získá svůj vlastní nezávislý vodoznak. Tento mechanismus je užitečné pro povolení generování včas výstupu, kdy zabývají velké hodiny odchylek nebo sítě zpoždění mezi odesílatelé událostí.

Substreams jsou jedinečné řešení, které poskytuje služba Azure Stream Analytics a nejsou nabízeny jiných datových proudů zpracování dat systémů. Stream Analytics se vztahuje na příchozí události v případě substreams používají opožděné okno tolerance pozdního přijetí. Ve výchozím nastavení (5 sekund) je pravděpodobně příliš malá pro zařízení s časovými razítky snižování. Doporučujeme začít s 5 minut a provést úpravy podle způsobu posun hodin jejich zařízení.

## <a name="early-arriving-events"></a>Časná opravovány události

Mohli jste si všimnout jiné koncept volá včasné doručení okna, které vypadá jako opak pozdní okno tolerance pozdního přijetí. Toto okno je pevně nastavena na 5 minut a slouží k jinému účelu z pozdní doručení, jeden.

Azure Stream Analytics zaručuje vždy generuje úplné výsledky, můžete zadat jenom **úlohy počáteční čas** jako výstup poprvé úlohy, není vstupní čas. Čas spuštění úlohy je potřeba zpracovat okně dokončeno, ne jenom ze střední části okna.

Stream Analytics je počáteční čas pak odvozen od specifikace dotazu. Ale vzhledem k tomu, že vstupní události zprostředkovatele je pouze indexované podle čas doručení, systém má přeložit počáteční čas na čas doručení událostí. Od tohoto okamžiku ve vstupní události Service broker může systém zahájit zpracování událostí. Časná opravovány okno omezení překlad je jednoduché. Čas události mínus 5 minut dřívější opravovány okno se spouští. Tento výpočet také znamená, že systém zahodí všechny události, které se zobrazují s dobu 5 minut více, než čas doručení událostí.

Tento koncept se používá k zajištění, že zpracování je opakovatelné bez ohledu na to, kde můžete začít výstup z. Bez tohoto mechanismu ho by se možné zaručit opakovatelnost jako řada jiných datových proudů systémů deklarací že dělají.

## <a name="side-effects-of-event-ordering-time-tolerances"></a>Vedlejší efekty čas tolerance řazení událostí

Úlohy Stream Analytics obsahuje několik **řazení událostí** možnosti. Dvě lze nastavit na webu Azure Portal: **události mimo pořadí** nastavení (tolerance mimo pořadí) a **události, které dorazí pozdě** nastavení (tolerance pozdního přijetí). **Včasné doručení** proti chybám je pevná a nejde ho upravit. Tyto zásady času se používají ve Stream Analytics k poskytování silné záruky. Ale tato nastavení mají některé někdy neočekávané důsledky:

1. Náhodně odesílání událostí, které jsou příliš stará.

   Dřívější události by neměly obsahovat výstup normálně. Je možné, že dřívější události se odešlou do výstupu, pokud se hodiny odesílatele běží příliš rychle ale. Všechny události předčasného opravovány se zahodí, takže se některé z nich ve výstupu nezobrazí.

2. Staré události odeslání do Event Hubs slouží ke zpracování Azure Stream Analytics.

   Zatímco starých událostí se může zdát neškodné na nejprve z důvodu aplikace tolerance pozdního přijetí, může být zrušená staré události. Pokud tyto události jsou příliš stará **System.Timestamp** je hodnota změněna během příjem událostí. Kvůli tomuto chování aktuálně Azure Stream Analytics je vhodnější pro scénáře, namísto zpracování historických událostí scénáře pro zpracování událostí téměř v reálném čase. Můžete nastavit **události, které dorazí pozdě** uvedení na nejvyšší možné hodnotu (20 dnů) Chcete-li toto chování v některých případech.

3. Výstupy se zdá být zpožděné.

   První mez je vygenerováno počítané: **čas maximální události** systému sledoval probíhající zatím minus velikost okna pro toleranci mimo pořadí. Tolerance mimo pořadí je ve výchozím nastavení nakonfigurovaná na nula (00 minut a 00 sekund). Pokud ji nastavíte na hodnotu vyšší, nenulové času, první výstup úlohy streamování je zpožděné podle této hodnoty čas (nebo vyšší) z důvodu vodoznak prvním se počítá.

4. Vstupy jsou zhuštěný.

   Pokud v daném oddílu neexistuje žádný vstup, čas vodoznak se počítá jako **čas doručení** minus pozdní okno tolerance pozdního přijetí. V důsledku toho pokud vstupní události jsou zřídka a zhuštěné, výstup můžete změnit podle tohoto množství času. Výchozí hodnota **události, které dorazí pozdě** hodnota je 5 sekund. Měli byste očekávat zobrazíte některých zpoždění při odesílání vstupní události najednou, např. Zpoždění, získáte horší, když nastavíte **události, které dorazí pozdě** okno velké hodnoty.

5. **System.Timestamp** hodnota se liší od času v **čas události** pole.

   Jak je popsáno výše, nastaví čas události tolerance mimo pořadí nebo pozdní windows tolerance pozdního přijetí systém. **System.Timestamp** hodnota události upravena, ale ne **čas události** pole.

## <a name="metrics-to-observe"></a>Sledujte metriky

Můžete sledovat počet řazení čas efektů proti chybám prostřednictvím událostí [metriky úlohy Stream Analytics](stream-analytics-monitoring.md). Relevantní jsou následující metriky:

|Metrika  | Popis  |
|---------|---------|
| **Události mimo pořadí** | Označuje číslo události mimo pořadí, které byly vyřazeny nebo zadaný upravené časové razítko přijetí. Tato metrika je přímo vliv konfiguraci **události mimo pořadí** nastavení **řazení událostí** stránka úlohy na webu Azure Portal. |
| **Pozdní vstupní události** | Označuje číslo, které zkazit ze zdroje událostí. Tato metrika zahrnuje události, které byly vynechány nebo měli jejich časového razítka byla upravena. Tato metrika je přímo vliv konfiguraci **události, které dorazí pozdě** nastavení **řazení událostí** stránka úlohy na webu Azure Portal. |
| **Události předčasného vstupu** | Označuje počet událostí přicházejících již v rané fázi ze zdroje, které buď byla vyřazena, nebo jejich časového razítka byl upraven, pokud jsou nad rámec již v rané fázi 5 minut. |
| **Zpoždění vodoznak** | Určuje zpoždění úlohy zpracování dat streamování. Zobrazit další informace v následující části.|

## <a name="watermark-delay-details"></a>Podrobnosti zpoždění vodoznak

**Vodoznak zpoždění** metrika je vypočítán jako skutečný čas zpracování uzlu minus největší vodoznak se zatím viděli. Další informace najdete v tématu [vodoznak zpoždění blogový příspěvek](https://azure.microsoft.com/blog/new-metric-in-azure-stream-analytics-tracks-latency-of-your-streaming-pipeline/).

Může existovat několik důvodů, proč tato hodnota metriky je větší než 0. v rámci normálního provozu:

1. Vlastní zpracování zpoždění streamovacího kanálu. Obvykle je toto zpoždění nominální.

2. Okno tolerance mimo pořadí zavedené zpoždění, protože vodoznak se sníží velikost okna pro toleranci.

3. Pozdní okno přijetí zavedené zpoždění, protože vodoznak se sníží velikost okna pro toleranci.

4. Hodin zkreslit zpracování uzlu generování metriku.

Existuje mnoho dalších prostředků omezení, která může způsobit streamovacího kanálu zpomalovat. Metrika vodoznak zpoždění může narůstat z důvodu:

1. Ve službě Stream Analytics ke zpracování objemu události vstupu není dostatek prostředků zpracování. Pokud chcete vertikálně navýšit kapacitu prostředků, přečtěte si téma [principy a úpravy jednotek streamování](stream-analytics-streaming-unit-consumption.md).

2. Není dostatek propustnost v rámci zprostředkovatelé vstupních událostí, takže se omezují. Možná řešení, najdete v části [automaticky vertikálně navýšit kapacitu jednotek propustnosti centra událostí Azure](../event-hubs/event-hubs-auto-inflate.md).

3. Výstupní jímky nezřídil s dostatečnou kapacitou, aby se omezují. Možná řešení značně záviset na flavor výstup používaných služeb.

## <a name="output-event-frequency"></a>Frekvence událostí výstupu

Azure Stream Analytics používá pokroku vodoznak jako pouze aktivační událost pro vytvoření výstupních událostí. Protože vodoznak je odvozen ze vstupních dat, je opakovatelné během obnovení po selhání a také v uživatelem iniciované přepracování.

Při použití [agregací](stream-analytics-window-functions.md), kterou služba produkuje pouze výstupů na konci okna. V některých případech může být vhodné uživatele zobrazíte částečné agregace vygenerované z ovládacího prvku windows. Částečné agregace nejsou aktuálně podporované ve službě Azure Stream Analytics.

V jiných datových proudů řešení může být vyhodnocena výstupních událostí v různých fázích aktivační událost, v závislosti na externí okolností. Je možné v některých řešeních, že výstupní události pro dané časové okno několik generovat více než jednou. Vstupní hodnoty jsou kontrast, se stanou přesnější agregovat výsledky. Události by mohla spekulaci na první a upravené v čase. Například při určitých zařízení je offline ze sítě, může použít odhadovanou hodnotu na systému. Později na stejném zařízení převede do režimu online k síti. Potom data skutečné události mohou být zařazeny do vstupního datového proudu. Výsledky výstupu z dané časové okno zpracování vytváří přesnější výstup.

## <a name="illustrated-example-of-watermarks"></a>Ilustrované příklad vodoznaky

Následující obrázky znázorňují postup vodoznaky za různých okolností.

Tato tabulka ukazuje ukázková data, která je zobrazena níže. Všimněte si, že času události a čas přijetí lišit, někdy párování a někdy není.

| Čas události | Čas přijetí | DeviceId |
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

Na tomto obrázku se používají tyto odchylky:

- Včasné doručení windows je 5 minut
- Pozdní opravovány interval je 5 minut
- Okna pro změnu uspořádání činí 2 minuty

1. Obrázek vodoznaku průběh prostřednictvím těchto událostí:

   ![Obrázek vodoznaku Azure Stream Analytics](media/stream-analytics-time-handling/WaterMark-graph-1.png)

   Významné procesů znázorněných na předchozím obrázku:

   1. První událost (zařízení1) a druhá událost (device2) přizpůsobily časy a jsou zpracovány bez úprav. Vodoznak postupuje u každé události.

   2. Při zpracování třetí událost (zařízení1) čas přijetí (12:11) předchází čas události (12:17). Události byly přijaty již v rané fázi, 6 minut, takže události se ignoroval kvůli dřívější tolerance pozdního přijetí 5 minut.

      Vodoznak nebude pokračovat v tomto případě počáteční událost.

   3. Čtvrtý událost (device3) a pátý událost (zařízení1) přizpůsobily časy a jsou zpracovány bez úprav. Vodoznak postupuje u každé události.

   4. Při zpracování šestého událostí (device3) čas přijetí (12:17) a čas události (12:12) je nižší než úroveň vodoznak. Čas události se upraví na úroveň označit vody (12:17).

   5. Při zpracování devátého událostí (device3), je čas přijetí (12:27) 6 minut před časem čas události (12:21). Pozdní přijetí zásady platí. Čas události je upravené (12:22), což je víc než horní meze (12:21) proto žádná další úpravy platí.

2. Druhý obrázek vodoznaku průběh bez dřívější zásady doručení:

   ![Azure Stream Analytics žádné dřívější vodoznak obrázek zásad](media/stream-analytics-time-handling/watermark-graph-2.png)

   V tomto příkladu nejsou použity žádné včasného zásady doručení. Pravdu události, které přicházejí již v rané fázi výrazně zvýšit vodoznak. Všimněte si, že třetí událost v tomto scénáři není Vyřazená (deviceId1 v čase 12:11) a vodoznak je vyvolávána s cílem 12:15. Čtvrtý čas události v důsledku je upravené dopředné 7 minut (12:08 až 12:15).

3. Na posledním obrázku se používají substreams (přes DeviceId). Více vodoznaky jsou sledovány, jeden pro každý datový proud. Existují menší počet událostí s úspěšností upravena v důsledku.

   ![Azure Stream Analytics substreams obrázek vodoznaku](media/stream-analytics-time-handling/watermark-graph-3.png)

## <a name="next-steps"></a>Další postup

- [Důležité informace pořadí událostí Azure Stream Analytics](stream-analytics-out-of-order-and-late-events.md)
- [Metriky úlohy Stream Analytics](stream-analytics-monitoring.md)
