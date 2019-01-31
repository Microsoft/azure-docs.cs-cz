---
title: Optimalizace propustnosti nákladů ve službě Azure Cosmos DB
description: Tento článek vysvětluje, jak optimalizovat propustnost náklady pro data uložená ve službě Azure Cosmos DB.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: rimman
ms.openlocfilehash: 443bf5694515720b1b865c310e70ca9c45add262
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55465584"
---
# <a name="optimize-provisioned-throughput-cost-in-azure-cosmos-db"></a>Optimalizujte náklady zřízenou propustnost v databázi Azure Cosmos DB

Tím, že nabízí model zřízená propustnost, Azure Cosmos DB nabízí předvídatelný výkon v libovolném měřítku. Rezervace nebo zřizování propustnosti předem eliminuje "efekt hlučného souseda" na výkon. Určete přesné množství propustnost, které potřebujete a Azure Cosmos DB zaručuje nakonfigurované propustnost, zajištěné smlouvou SLA.

Můžete začít s minimální propustnost 400 RU/s a škálovat až na desítky milionů požadavků za sekundu nebo ještě víc. Každý požadavek, který se vydáte na váš kontejner Azure Cosmos nebo databáze, jako je například požadavek čtení, zápis požadavku, požadavek, uložených procedur mít odpovídající cenu, která se odečte od zřízené propustnosti. Pokud zřídíte 400 RU/s a vydat dotaz, který stojí 40 RU, budou moct vystavovat 10 takové dotazy za sekundu. Všechny požadavky nad rámec, který se zobrazí míry časově omezené a měli byste opakovat požadavek. Pokud používáte ovladače klienta, podporují automatické logika.

Můžete zřídit propustnost v databázích nebo kontejnery a jednotlivých strategií vám pomohou ušetřit na nákladech v závislosti na scénáři.

## <a name="optimize-by-provisioning-throughput-at-different-levels"></a>Optimalizace propustnosti na různých úrovních zřízením

* Pokud zřizujete propustnost v databázi, všechny kontejnery, například kolekce/tabulek a grafů v rámci této databáze můžete sdílet propustnosti na základě zatížení. Rezervované na úrovni databáze, propustnost se sdílí nerovnoměrně, v závislosti na zatížení na konkrétní sadu kontejnerů.

* Pokud zřizujete propustnosti na kontejner, propustnost je zaručeno, že pro tento kontejner se opírá o smlouvě SLA. Klíč logického oddílu je zásadní pro rozdělení zatížení na všechny logické oddíly kontejneru. Zobrazit [dělení](partitioning-overview.md) a [horizontální škálování](partition-data.md) články pro další podrobnosti.

Tady jsou některé pokyny při rozhodování o strategii zřízená propustnost:

**Zvažte zřízení propustnost v databázi Azure Cosmos DB (obsahující sadu kontejnerů) Pokud**:

1. Máte několik desítek kontejnery Azure Cosmos a chcete sdílet propustnost napříč některé nebo všechny z nich. 

2. Při migraci z jednoho tenanta databáze navržen pro spouštění na IaaS hostované virtuální počítače nebo na místě, například NoSQL nebo relačních databází do služby Azure Cosmos DB. A pokud máte mnoho kolekcí, tabulky a grafy a nechcete, aby před každou změnou do datového modelu. Mějte na paměti, bude pravděpodobně nutné ohrozit některé z výhod nabízených službou Azure Cosmos DB, pokud nejsou aktualizace datového modelu, při migraci z místní databáze. Doporučujeme vždy zásada datového modelu k plnému využití z hlediska výkonu a také k optimalizaci nákladů. 

3. Chcete vyrovnat se s neplánované špičky v úlohách tím, že ve fondu propustnosti na úrovni databáze, které jsou vystaveny neočekávané nárůst zatížení. 

4. Namísto nastavení konkrétní propustnosti pro jednotlivé kontejnery které jsou pro vás o tom, jak agregovanou propustnost mezi sadu kontejnery v rámci databáze.

**Vezměte v úvahu zřizování propustnosti na kontejner, pokud:**

1. Máte několik kontejnerů Azure Cosmos. Protože Azure Cosmos DB je nezávislý na schématu, kontejner může obsahovat položky, které mají heterogenní schémata a nevyžaduje, aby zákazníkům vytvoření více typů kontejneru, jeden pro každou entitu. Vždycky je, že možnost zvažte, pokud seskupení samostatné Řekněme, že 10-20 kontejnerů do jednoho kontejneru dává smysl. S minimální pro kontejnery 400 ru sdružování všech 10-20 kontejnerů do jednoho může být cenově výhodnější. 

2. Chcete řídit propustnosti na konkrétní kontejner a získat garantovanou propustnost na daném kontejneru zajištěné smlouvou SLA.

**Vezměte v úvahu v hybridní kombinaci výše uvedených dvou strategií:**

1. Jak bylo zmíněno výše, Azure Cosmos DB umožňuje kombinovat a párovat výše uvedených dvou strategií, takže teď můžou mít některé kontejnery v rámci databáze Azure Cosmos, které může sdílet na databázi, tak některé kontejnery ve stejné databázi zřízené propustnosti , který vyhrazené množství zřízenou propustnost. 

2. Můžete provést výše uvedených strategií a navrhněte jako hybridní konfigurace, kde jsou obě databáze úrovně zřízená propustnost s některé kontejnery nutnosti mít vyhrazené propustnosti.

Jak je znázorněno v následující tabulce, v závislosti na výběru rozhraní API, můžete zřídit propustnost v přírůstcích po různých.

|Rozhraní API|Pro **sdílené** propustnost a konfigurace |Pro **vyhrazené** propustnost a konfigurace |
|----|----|----|
|SQL API|Databáze|Kontejner|
|Rozhraní API služby Azure Cosmos DB pro MongoDB|Databáze|Kolekce|
|Rozhraní Cassandra API|Prostor klíčů|Table|
|Rozhraní Gremlin API|Databázový účet|Graph|
|Rozhraní Table API|Databázový účet|Table|

Podle zřizování propustnosti na různých úrovních můžete optimalizovat náklady na základě charakteristiky vašich úloh. Jak už bylo zmíněno dříve, můžete prostřednictvím kódu programu a při každé zvýšení počtu čas nebo snížit vaše zřízená propustnost pro buď jednotlivé kontejnery nebo souhrnně mezi sadu kontejnerů. Podle elastické škálování propustnosti jako změny pracovního vytížení, platíte jenom za propustnost, kterou jste nakonfigurovali. Pokud je váš kontejner nebo sadu kontejnerů distribuovaná napříč několika oblastmi, pak propustnost můžete nakonfigurovat v kontejneru nebo sadu kontejnerů je zaručeno, které musí být dostupné ve všech oblastech.

## <a name="optimize-with-rate-limiting-your-requests"></a>Optimalizace s žádostí o omezení četnosti

Pro úlohy, které nejsou citlivá na latenci můžete zajistit méně propustnost a umožní aplikaci řešit omezování rychlosti při Skutečná propustnost překračuje zřízenou propustnost. Server preventivně ukončení požadavku s RequestRateTooLarge (kód stavu HTTP 429), který se vrátit `x-ms-retry-after-ms` záhlaví určující dobu v milisekundách, které musí uživatel čekat před opakováním žádosti. 

```html
HTTP Status 429, 
 Status Line: RequestRateTooLarge 
 x-ms-retry-after-ms :100
```

### <a name="retry-logic-in-sdks"></a>Logika opakování v sady SDK 

Nativním sadám SDK (.NET/.NET Core, Javy, Node.js a Python) implicitně zachytit tuto odpověď, respektují zadaný server hlavičkou retry-after a opakujte žádost. Pokud váš účet je současně přistupuje více klientů, bude při dalším pokusu úspěšné.

Pokud máte více než jednoho klienta kumulativně provozní konzistentně výše je frekvence požadavků, nemusí být dostatečné výchozí počet opakování aktuálně, který je aktuálně nastavený na 9. V takovém případě se vyvolá klienta `DocumentClientException` se stavem kódu 429 do aplikace. Výchozí počet opakování můžete změnit tak, že nastavíte `RetryOptions` ConnectionPolicy instance. Ve výchozím nastavení je vrácena DocumentClientException se stavovým kódem 429 po uplynutí určité doby kumulativní Počkejte 30 sekund, pokud požadavek dál pracovat nad frekvence požadavků. K tomu dojde i v případě aktuální počet opakování je menší než počet opakování, jde o výchozí hodnotu 9 nebo uživatelem definovanou hodnotu. 

[MaxRetryAttemptsOnThrottledRequests](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.retryoptions.maxretryattemptsonthrottledrequests?view=azure-dotnet#Microsoft_Azure_Documents_Client_RetryOptions_MaxRetryAtte) nastavená na 3, tak v takovém případě pokud operace požadavku je rychlostně omezena překračuje rezervovanou propustnost pro kolekce, operace požadavku se opakuje třikrát před vyvoláním výjimka k aplikaci.  [MaxRetryWaitTimeInSeconds](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.retryoptions.maxretrywaittimeinseconds?view=azure-dotnet#Microsoft_Azure_Documents_Client_RetryOptions_MaxRetryWaitTimeInSeconds) nastavena na 60, tak v tomto případě pokud čeká kumulativní opakovat po dobu v sekundách od prvního požadavku překročí 60 sekund, je vyvolána výjimka.

```csharp
ConnectionPolicy connectionPolicy = new ConnectionPolicy(); 

connectionPolicy.RetryOptions.MaxRetryAttemptsOnThrottledRequests = 3; 

connectionPolicy.RetryOptions.MaxRetryWaitTimeInSeconds = 60;
```

## <a name="partitioning-strategy-and-provisioned-throughput-costs"></a>Strategie dělení a náklady na zřízená propustnost

Dobrou strategii dělení je důležité pro zajištění optimalizace nákladů ve službě Azure Cosmos DB. Ujistěte se, že neexistuje žádné nerovnoměrné rozdělení oddílů, které jsou vystaveny prostřednictvím metrik úložiště. Ujistěte se, že je žádnému zkosení propustnosti pro oddíl, který je přístupný pomocí metriky propustnosti. Ujistěte se, že je na konkrétní oddíl klíče žádnému zkosení. Dominantní klíče ve službě storage jsou vystaveny prostřednictvím metriky, ale klíč bude závisí na vaší aplikace vzor přístupu. Je vhodné uvažovat o klíči přímo logického oddílu. Vhodným klíčem oddílu se očekává, že mají následující vlastnosti:

* Zvolte klíč oddílu, který se šíří zatížení rovnoměrně mezi všechny oddíly a rovnoměrně v čase. Jinými slovy by neměl mít některé klíče s většinou data a některé klíče s menší než nebo žádná data. 

* Zvolte klíč oddílu, který umožňuje přístup k vzory rovnoměrně rozloženy logické oddíly. Zatížení je poměrně i přes všechny klíče. Jinými slovy většinou zatížení by neměl zaměřit na konkrétní pár klíčů. 

* Zvolte klíč oddílu, který má široký rozsah hodnot. 

Základní myšlenka je dat a aktivity ve vašem kontejneru rozloženy sadu logické oddíly, aby prostředky úložiště dat a propustnost je možné distribuovat napříč logickými oddíly. Kandidáty pro klíče oddílů může obsahovat vlastnosti, které se často zobrazují jako filtr v dotazech. Dotazy můžete efektivně směrovat včetně klíče oddílu v predikátu filtru. Díky takové strategie dělení optimalizace zřízená propustnost bude mnohem jednodušší. 

### <a name="design-smaller-items-for-higher-throughput"></a>Návrh menší položky vyšší propustnost 

Velikost položky jsou korelována přímo zátěž žádostí nebo náklady zpracování požadavku na danou operaci. Operace na velkých položek stojí více než operace na menší položky. 

## <a name="data-access-patterns"></a>Vzory přístupu k datům 

Je vždy vhodné k logickému oddělení dat do logických kategorií v závislosti na tom, jak často přístup k datům. Zařazením jako aktivní, střední nebo studená data můžete podrobně upravit využité úložiště a požadované propustnosti. V závislosti na četnosti přístupu můžete umístit data do samostatné kontejnerů (například tabulky, grafy a kolekce) a vyladění zřízenou propustnost na nich tak, aby vyhovovaly potřebám tohoto segmentu data. 

Kromě toho pokud používáte službu Azure Cosmos DB a víte, aby hledat podle určité hodnoty data nebo přistupuje zřídka je, měli byste uložit komprimované hodnotami těchto atributů. Pomocí této metody uložit v prostoru úložiště, index adresní prostor a zřízenou propustnost a za následek nižší náklady.

## <a name="optimize-by-changing-indexing-policy"></a>Optimalizovat tak, že změníte zásady indexování 

Ve výchozím nastavení služby Azure Cosmos DB automaticky indexuje každou vlastnost každý záznam. To je určené pro vývoj a zajištění špičkového výkonu napříč mnoha různých typů dotazů ad-hoc. Pokud máte velké záznamy s tisíci vlastnosti, nemusí být užitečné, zejména v případě, že pouze dotazovat 10 nebo 20 z těchto vlastností za náklady na propustnost indexování každé vlastnosti. Až se blíže k získání popisovače na vaše konkrétní úlohy, je naše doprovodné materiály k vyladění indexu zásady. Najdete všechny podrobnosti o službě Azure Cosmos DB zásady indexování [tady](indexing-policies.md). 

## <a name="monitoring-provisioned-and-consumed-throughput"></a>Monitorování zřízené a spotřebované propustnost 

Můžete monitorovat celkový počet ru zřízených, počet požadavků na rychlost časově omezené, jakož i počet jednotek ru využili jste na webu Azure Portal. Metrika využití příklad na následujícím obrázku:

![Monitorování jednotek žádosti na webu Azure Portal](./media/optimize-cost-throughput/monitoring.png)

Můžete také nastavit výstrahy pro kontrolu, pokud frekvence časově omezené požadavků překračuje konkrétních mezních hodnot. Zobrazit [monitorování služby Azure Cosmos DB](use-metrics.md) , kde najdete další podrobnosti. Tyto výstrahy můžete poslat e-mail správci účtu nebo telefonním čísle vlastní Webhooku protokolu HTTP nebo funkci Azure pro automatické zvýšení zřízenou propustnost. 

## <a name="scale-your-throughput-elastically-and-on-demand"></a>Elasticky škálovat propustnost a na vyžádání 

Vzhledem k tomu, že se vám účtuje zřízenou propustnost, odpovídajícím zřízená propustnost podle vašich potřeb vám může pomoct vyhnout poplatkům za nepoužívané propustnost. Zřízená propustnost můžete vertikálně navýšit nebo snížit kapacitu kdykoli a podle potřeby.  

* Sledování využití vašich ru a poměr míra časově omezené požadavky může odhalit, že není nutné udržovat zřízené v rámci konstantní v průběhu dne nebo týdne. Můžete obdržet menší provoz v noci nebo během víkendu. Pomocí webu Azure portal nebo Azure Cosmos DB nativním sadám SDK nebo rozhraní REST API můžete škálovat vaše zřízená propustnost v každém okamžiku. Rozhraní REST API služby Azure Cosmos DB poskytuje koncové body se programově aktualizovat úroveň výkonu kontejnery díky tomu je jednoduché a upravte propustnost v kódu v závislosti na čas, den nebo den v týdnu. Operace je provedena bez odstávky a obvykle projeví za méně než jednu minutu. 

* Jednu z oblastí by se měly škálovat propustnost je při můžete ingestovat data do služby Azure Cosmos DB, například při migraci dat. Po dokončení migrace můžete zřízenou propustnost vertikálně snížit kapacitu pro zpracování takového řešení stabilního stavu.  

* Nezapomeňte, že fakturace je na členitosti jednu hodinu, tak peníze za nebude uložit, pokud se změní zřízenou propustnost častěji než jednu hodinu po jednom.

## <a name="determine-the-throughput-needed-for-a-new-workload"></a>Zjistit propustnost potřebné pro nový pracovní postup 

Pokud chcete zjistit zřízená propustnost pro nový pracovní postup, můžete použít následující kroky: 

1. Proveďte počáteční hrubý testování, pomocí capacity planner a upravte svoje odhady pomocí Průzkumníka služby Azure Cosmos na webu Azure Portal. 

2. Doporučuje se vytvořit kontejnery s vyšší propustnost, než se očekává a potom škálování podle potřeby. 

3. Doporučujeme použít jednu z nativní Azure Cosmos DB SDK těžit z automatické opakování pokusů při žádosti o získání míra časově omezené. Pokud pracujete na platformě, která není podporována a používat rozhraní REST API služby Cosmos DB, implementovat vlastní zásadu opakování pomocí `x-ms-retry-after-ms` záhlaví. 

4. Ujistěte se, že a kódu aplikace, když selžou všechny pokusy podporuje případu bez výpadku. 

5. Můžete nakonfigurovat výstrahy z portálu Azure portal k přijímání oznámení pro omezování rychlosti. Můžete začít s konzervativní omezení, jako je 10 požadavků míra časově omezený za posledních 15 minut a přepnete se do pravidla více nemůžou dočkat, až po zjištění skutečného využití. Příležitostné omezení přenosové rychlosti, se dají, že zobrazí se, že se přehrávání s limity nastavíte, a to je přesně co chcete udělat. 

6. Využít monitorování ke porozumět provozu vzorku, aby se zohlednilo potřeba dynamicky upravovat zřizování propustnosti průběhu dne nebo týdne. 

7. Sledujte vaše zřízená vs. spotřebovaná propustnost poměr pravidelně a ujistěte se, že nebyly zřízený víc než požadovaný počet kontejnerů a databáze. Trochu přes zřízená propustnost je dobré bezpečnostní kontrola.  

### <a name="best-practices-to-optimize-provisioned-throughput"></a>Osvědčené postupy pro optimalizaci zřízená propustnost 

Následující kroky umožňují vaše řešení byla vysoce škálovatelný a hospodárný při použití služby Azure Cosmos DB.  

1. Pokud máte výrazně za zřízenou propustnost mezi kontejnery a databází, je vhodné zkontrolovat ru zřízených Vs spotřebovaných ru a vylaďte úlohy.  

2. Jedním ze způsobů pro odhad množství vyhrazenou propustností, které jsou požadovány příslušnou aplikací je k zaznamenání požadavku jednotky RU poplatky se spouštěním typických operací proti reprezentativní kontejneru Azure Cosmos nebo databáze, které používá vaše aplikace a potom odhadněte počet operací, které očekáváte, že pokud chcete provést každou sekundu. Nezapomeňte pro měření a zahrnují typické dotazy a také jejich použití. Další informace tom, jak odhadnout náklady za RU dotazů prostřednictvím kódu programu nebo použití portálu najdete v tématu [optimalizovat náklady na dotazy](online-backup-and-restore.md). 

3. Dalším způsobem, jak získat operací a náklady v RU, je povolení Log Analytics, vám dá přehled/doby provádění operací a požadavku poplatky. Azure Cosmos DB poskytuje zátěž žádostí pro všechny operace, takže každý poplatek za operaci můžete ukládat zpět z odpovědi a pak použije k analýze. 

4. Dá se pružně škálovat nahoru a dolů zřízená propustnost podle potřeby tak, aby vyhovovaly vašim potřebám pracovního vytížení. 

5. Můžete přidávat a odebírat oblastmi spojenými s vaším účtem Azure Cosmos, podle potřeby a mít náklady pod kontrolou. 

6. Ujistěte se, že máte rovnoměrnou distribuci přenosu dat a úloh napříč logickými oddíly vaše kontejnery. Pokud máte oddílu nerovnoměrné distribuce, může dojít ke zřízení větší počet propustnost než hodnota, která je potřeba. Pokud určíte, že máte asymetrické rozdělení, doporučujeme opětovná distribuce zatížení rovnoměrně napříč oddíly nebo změnit rozdělení data. 

7. Pokud máte mnoho kontejnerů a tyto kontejnery nevyžadují smlouvy o úrovni služeb, můžete použít nabídky založené na databázi případech, kde za propustnosti kontejneru smlouvy SLA se nevztahují. Byste měli identifikovat, které chcete migrovat na úrovni propustnosti databáze Azure Cosmos kontejnery nabízejí a pak je migrujte pomocí řešení založené na informační kanál změn. 

8. Zvažte použití "Cosmos DB zdarma vrstvy" (zdarma po dobu jednoho roku), zkuste Cosmos DB (až 3 oblasti) nebo ke stažení emulátoru služby Cosmos DB pro scénáře vývoje/testování. Pomocí těchto možností pro vývoj testu může podstatně snížit náklady.  

9. Úlohy specifické možnosti optimalizace – například nákladů můžete provádět další zvýšení čtení velikost dávky, Vyrovnávání zatížení napříč několika oblastmi a rušit duplikace dat, pokud je k dispozici.

10. S Azure Cosmos DB vyhrazené kapacity získáte výrazné slevy až 65 % tři roky. Azure Cosmos DB – model rezervované kapacity je počátečního závazku využívání služeb na potřeby časem jednotek žádostí. Slevy proběhne tak, aby další jednotky žádosti použijte po delší dobu, tím více bude slevy. Tyto slevy okamžitě. Všechny jednotky ru využité nad zřízené hodnoty se účtují podle náklady než vyhrazené kapacity. Zobrazit [Cosmos DB rezervované kapacity](cosmos-db-reserved-capacity.md)) pro další podrobnosti. Zvažte, jestli nekoupit záložní kapacitu na dále snížit své náklady na zřízenou propustnost.  

## <a name="next-steps"></a>Další postup

Potom můžete přejít k další informace o optimalizaci nákladů ve službě Azure Cosmos DB najdete v následujících článcích:

* Další informace o [optimalizace pro vývoj a testování](optimize-dev-test.md)
* Další informace o [vysvětlení vašeho vyúčtování Azure Cosmos DB](understand-your-bill.md)
* Další informace o [optimalizovat náklady na úložiště](optimize-cost-storage.md)
* Další informace o [optimalizovat náklady na operace čtení a zápisy](optimize-cost-reads-writes.md)
* Další informace o [optimalizovat náklady na dotazy](optimize-cost-queries.md)
* Další informace o [optimalizovat náklady na účty ve více oblastech Azure Cosmos](optimize-cost-regions.md)

