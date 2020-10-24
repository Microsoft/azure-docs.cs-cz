---
title: Optimalizace nákladů na propustnost v Azure Cosmos DB
description: Tento článek vysvětluje, jak optimalizovat náklady na propustnost pro data uložená v Azure Cosmos DB.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/07/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: d7d77bdb223e8c3b71ef03febd4081d1f63bd1a3
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2020
ms.locfileid: "92475460"
---
# <a name="optimize-provisioned-throughput-cost-in-azure-cosmos-db"></a>Optimalizace nákladů na zřízenou propustnost ve službě Azure Cosmos DB

Díky zajišťování modelu propustnosti nabízí Azure Cosmos DB předvídatelný výkon v jakémkoli měřítku. Zachovávání nebo zřizování propustnosti předem eliminuje u vašeho výkonu "hlučné sousední účinky". Zadáváte přesnou míru propustnosti, kterou potřebujete, a Azure Cosmos DB garantuje nakonfigurovanou propustnost, kterou zajišťuje smlouva SLA.

Můžete začít s minimální propustností 400 RU/s a škálovat až desítky milionů požadavků za sekundu nebo ještě víc. Každý požadavek, který vyřešíte v rámci vašeho kontejneru nebo databáze Azure Cosmos, jako je žádost o čtení, žádost o zápis, požadavek na dotaz, uložené procedury mají odpovídající cenu, která se od zřízené propustnosti odečte. Pokud zřídíte 400 RU/s a vydáte dotaz, který by ru náklady 40, bude možné vystavit 10 takových dotazů za sekundu. Jakýkoli požadavek nad tím, který získá omezení četnosti a žádost by se měla opakovat. Pokud používáte klientské ovladače, podporují logiku automatického opakování.

Propustnost můžete zřídit pro databáze nebo kontejnery a v závislosti na konkrétním scénáři vám obě strategie můžou pomoct ušetřit náklady.

## <a name="optimize-by-provisioning-throughput-at-different-levels"></a>Optimalizace pomocí zřizování propustnosti na různých úrovních

* Při zřizování propustnosti v databázi může všechny kontejnery, například kolekce/tabulky nebo grafy v této databázi, sdílet propustnost na základě zatížení. Propustnost rezervovaný na úrovni databáze je nerovnoměrně sdílená v závislosti na zatížení konkrétní sady kontejnerů.

* Pokud zřizujete propustnost na kontejneru, je zajištěna propustnost tohoto kontejneru, kterou zajišťuje smlouva SLA. Volba klíče logického oddílu je zásadní pro rovnoměrné rozložení zatížení ve všech logických oddílech kontejneru. Další podrobnosti najdete v článcích [dělení](partitioning-overview.md) a [horizontální škálování](partitioning-overview.md) .

Níže jsou uvedeny některé pokyny k rozhodování o strategii zřízené propustnosti:

**Zvažte zajištění propustnosti v databázi Azure Cosmos (obsahující sadu kontejnerů), pokud**:

1. Máte několik desítek Cosmos kontejnerů Azure a chcete sdílet propustnost napříč některými nebo všemi z nich. 

2. Migrujete z databáze s jednou tenantů navrženou tak, aby běžela na virtuálních počítačích hostovaných v IaaS nebo v místním prostředí, například v NoSQL nebo relačních databázích, které Azure Cosmos DB. A pokud máte mnoho kolekcí, tabulek a grafů a nechcete provádět žádné změny v datovém modelu. Upozorňujeme, že pokud při migraci z místní databáze neaktualizujete datový model, může být nutné narušit některé z výhod, které nabízí Azure Cosmos DB. Doporučuje se vždycky znovu přistoupit k datovému modelu a získat tak nejvíc z hlediska výkonu a optimalizovat pro náklady. 

3. Chcete absorbovat neplánované špičky v úlohách na základě propustnosti fondu na úrovni databáze v závislosti na neočekávaném špičkě úlohy. 

4. Místo nastavení konkrétní propustnosti pro jednotlivé kontejnery se zajímáte o získání agregované propustnosti napříč sadou kontejnerů v rámci databáze.

**Zvažte zajištění propustnosti pro jednotlivé kontejnery, pokud:**

1. Máte několik kontejnerů Azure Cosmos. Vzhledem k tomu, že Azure Cosmos DB je nezávislá schématu, kontejner může obsahovat položky s heterogenními schématy a nevyžaduje, aby zákazníci vytvářeli více typů kontejnerů, jeden pro každou entitu. Je vždy možnost zvážit, zda je vhodné seskupit samostatné kontejnery 10-20, které tvoří jeden kontejner. Při 400 ru minimálních objemů pro kontejnery může být sdružování všech 10-20 kontejnerů do jednoho cenově výhodnější. 

2. Chcete řídit propustnost konkrétního kontejneru a získat zaručenou propustnost na daném kontejneru, který je zajištěn smlouvou SLA.

**Vezměte v úvahu hybridní z výše uvedených dvou strategií:**

1. Jak bylo zmíněno dříve, Azure Cosmos DB umožňuje kombinovat a porovnat výše uvedené dvě strategie, takže teď můžete mít v databázi Azure Cosmos nějaké kontejnery, které mohou sdílet propustnost zřízenou v databázi, a také některé kontejnery ve stejné databázi, které mohou mít vyhrazené množství zřízené propustnosti. 

2. Výše uvedené strategie můžete použít k tomu, aby se nacházela s hybridní konfigurací, ve které je zajištěná propustnost v úrovni databáze s některými kontejnery, které mají vyhrazenou propustnost.

Jak je znázorněno v následující tabulce v závislosti na volbě rozhraní API, můžete zajistit propustnost v různých členitcích.

|Rozhraní API|Pro **sdílenou** propustnost nakonfigurujte |U **vyhrazené** propustnosti nakonfigurujte |
|----|----|----|
|SQL API|databáze|Kontejner|
|Rozhraní API služby Azure Cosmos DB pro MongoDB|Databáze|Kolekce|
|Rozhraní Cassandra API|Prostor klíčů|Tabulka|
|Rozhraní Gremlin API|Databázový účet|Graph|
|Rozhraní Table API|Databázový účet|Tabulka|

Díky zajištění propustnosti na různých úrovních můžete optimalizovat náklady na základě charakteristik vašich úloh. Jak bylo zmíněno dříve, můžete programově a kdykoli zvětšit nebo zmenšit zřízenou propustnost pro jednotlivé kontejnery nebo souhrnně napříč sadou kontejnerů. Díky elastickému škálování propustnosti při změnách zatížení platíte jenom za propustnost, kterou jste nakonfigurovali. Pokud je váš kontejner nebo sada kontejnerů distribuován napříč několika oblastmi, je zaručená propustnost, kterou nakonfigurujete na kontejneru nebo sadě kontejnerů, ve všech oblastech.

## <a name="optimize-with-rate-limiting-your-requests"></a>Optimalizace pomocí hodnocení – omezení vašich požadavků

Pro úlohy, které nejsou citlivé na latenci, můžete zřídit menší propustnost a nechat aplikaci omezit rychlost, když Skutečná propustnost překročí zřízenou propustnost. Server bude žádost bez jakýchkoli požadavků `RequestRateTooLarge` (kód stavu HTTP 429) a vrátí `x-ms-retry-after-ms` hlavičku udávající, jak dlouho (v milisekundách) musí uživatel čekat, než bude požadavek opakovat. 

```html
HTTP Status 429, 
 Status Line: RequestRateTooLarge 
 x-ms-retry-after-ms :100
```

### <a name="retry-logic-in-sdks"></a>Logika opakování v sadách SDK 

Nativní sady SDK (.NET/.NET Core, Java, Node.js a Python) implicitně zachytí tuto odpověď, a to s ohledem na server, který je zadaný jako hlavička znovu, a zkuste žádost zopakovat. Pokud k účtu nebudete mít souběžně více klientů, další pokus bude úspěšný.

Pokud máte více než jednoho klienta, který se v současné době průběžně pracuje konzistentně nad rámec požadavků, výchozí počet opakování, který je aktuálně nastavený na 9, nemusí být dostatečný. V takových případech klient vyvolá `RequestRateTooLargeException` aplikaci se stavovým kódem 429. Výchozí počet opakování lze změnit nastavením v `RetryOptions` instanci ConnectionPolicy. Ve výchozím nastavení se `RequestRateTooLargeException` stavový kód 429 vrátí po kumulativní čekací době 30 sekund, pokud požadavek nadále funguje nad sazbou požadavku. K tomu dojde i v případě, že aktuální počet opakování je menší než maximální počet opakování, výchozí hodnota je 9 nebo uživatelem definovaná hodnota. 

[MaxRetryAttemptsOnThrottledRequests](/dotnet/api/microsoft.azure.documents.client.retryoptions.maxretryattemptsonthrottledrequests?preserve-view=true&view=azure-dotnet) je nastavené na hodnotu 3, takže v tomto případě platí, že pokud je operace požadavku omezená na překročení rezervované propustnosti kontejneru, operace požadavku se třikrát pokusí vyvoláním výjimky do aplikace. [MaxRetryWaitTimeInSeconds](/dotnet/api/microsoft.azure.documents.client.retryoptions.maxretrywaittimeinseconds?preserve-view=true&view=azure-dotnet#Microsoft_Azure_Documents_Client_RetryOptions_MaxRetryWaitTimeInSeconds) je nastavená na 60, takže v tomto případě je výjimka kumulativního opakování pokusu v sekundách od prvního požadavku delší než 60 sekund.

```csharp
ConnectionPolicy connectionPolicy = new ConnectionPolicy(); 
connectionPolicy.RetryOptions.MaxRetryAttemptsOnThrottledRequests = 3; 
connectionPolicy.RetryOptions.MaxRetryWaitTimeInSeconds = 60;
```

## <a name="partitioning-strategy-and-provisioned-throughput-costs"></a>Strategie dělení a náklady na zřízenou propustnost

Dobrá strategie vytváření oddílů je důležitá pro optimalizaci nákladů v Azure Cosmos DB. Zajistěte, aby nedošlo k žádnému zkosení oddílů, které jsou zpřístupněny prostřednictvím metrik úložiště. Zajistěte, aby nedošlo k žádnému zkosení propustnosti oddílu, který je vystavený s metrikami propustnosti. Zajistěte, aby nedošlo k žádnému zkosení na konkrétní klíče oddílu. Dominantní klíče v úložišti jsou zpřístupněny prostřednictvím metrik, ale klíč bude závislý na vašem vzoru přístupu k aplikaci. Nejlépe se zamyslete nad správným klíčem logického oddílu. Očekává se, že klíč oddílu bude mít následující vlastnosti:

* Vyberte klíč oddílu, který rovnoměrně rozšíří úlohy napříč všemi oddíly a rovnoměrně v průběhu času. Jinými slovy, neměli byste mít některé klíče na většinu dat a některé klíče s méně nebo bez dat. 

* Vyberte klíč oddílu, který umožní, aby byly vzory přístupu rovnoměrně rozloženy mezi logické oddíly. Zatížení je rozumně i napříč všemi klíči. Jinými slovy, většina úloh by neměla být zaměřena na několik specifických klíčů. 

* Vyberte klíč oddílu, který má velký rozsah hodnot. 

Základní nápad je rozprostřít data a aktivity ve vašem kontejneru napříč sadou logických oddílů, aby bylo možné distribuovat prostředky pro ukládání a propustnost dat napříč logickými oddíly. Kandidáti na klíče oddílů můžou zahrnovat vlastnosti, které se v dotazech často zobrazují jako filtr. Dotazy lze efektivně směrovat zahrnutím klíče oddílu do predikátu filtru. Díky takové strategii dělení bude optimalizace zřízené propustností mnohem jednodušší. 

### <a name="design-smaller-items-for-higher-throughput"></a>Návrh menších položek pro vyšší propustnost 

Poplatek za požadavek nebo náklady na zpracování požadavků na danou operaci je přímo koreluje s velikostí položky. Operace s velkými položkami budou mít více než operace s menšími položkami. 

## <a name="data-access-patterns"></a>Vzory přístupu k datům 

Je vždy dobrým zvykem logicky oddělit vaše data do logických kategorií podle toho, jak často k datům přistupujete. Oddělením IT jako horká, střední nebo studená data můžete vyladit spotřebované úložiště a požadovanou propustnost. V závislosti na četnosti přístupu můžete data umístit do samostatných kontejnerů (například tabulky, grafy a kolekce) a doladit zajištěné propustnosti pro ně, aby vyhovovaly potřebám daného segmentu dat. 

Kromě toho, pokud používáte Azure Cosmos DB a víte, že nebudete Hledat podle určitých hodnot dat nebo k nim jen zřídka, měli byste uložit komprimované hodnoty těchto atributů. Pomocí této metody ušetříte prostor úložiště, Rejstříkový prostor a zřízenou propustnost a výsledkem jsou nižší náklady.

## <a name="optimize-by-changing-indexing-policy"></a>Optimalizace změnou zásad indexování 

Ve výchozím nastavení Azure Cosmos DB automaticky indexuje všechny vlastnosti každého záznamu. Cílem je usnadnit vývoj a zajistit špičkový výkon v mnoha různých typech dotazů ad hoc. Pokud máte velké záznamy s tisíci vlastností, nemusíte platit náklady na propustnost při indexování každé vlastnosti, zejména pokud se dotazuje pouze na 10 nebo 20 těchto vlastností. Díky lepšímu způsobu, jak získat popisovač na konkrétní úlohu, je naše příručka vyladit zásady indexů. Úplné podrobnosti o zásadách indexování Azure Cosmos DB najdete [tady](index-policy.md). 

## <a name="monitoring-provisioned-and-consumed-throughput"></a>Monitorování zřízené a spotřebované propustnosti 

Můžete monitorovat celkový počet ru zřízené, počet omezených požadavků a také počet ru, které jste v Azure Portal využili. Následující obrázek ukazuje příklad metriky využití:

:::image type="content" source="./media/optimize-cost-throughput/monitoring.png" alt-text="Monitorovat jednotky žádosti v Azure Portal":::

Můžete také nastavit výstrahy, abyste zkontrolovali, jestli počet neomezených požadavků překročí určitou prahovou hodnotu. Další podrobnosti najdete v článku [jak monitorovat Azure Cosmos DB](use-metrics.md) . Tyto výstrahy můžou poslat e-mailem správcům účtů nebo volat vlastní Webhook HTTP nebo funkci Azure, aby se automaticky zvýšila zajištěná propustnost. 

## <a name="scale-your-throughput-elastically-and-on-demand"></a>Škálujte svou propustnost elastickě a na vyžádání 

Vzhledem k tomu, že se vám bude účtovat zajištěná propustnost, která vyhovuje zřízené propustnosti vašim potřebám, vám může pomáhat se vyhnout poplatkům za nevyužitou propustnost. Podle potřeby můžete škálovat zřízenou propustnost nahoru nebo dolů. Pokud jsou potřebné propustnosti hodně předvídatelné, můžete použít Azure Functions a použít Trigger časovače ke [zvýšení nebo snížení propustnosti podle plánu](scale-on-schedule.md). 

* Monitorování spotřeby ru a poměr požadavků, které jsou omezené, můžou odhalit, že v průběhu celého dne nebo týdne nemusíte udržovat v průběhu celé konstanty. Můžete obdržet méně provozu v noci nebo během víkendu. Pomocí Azure Portal nebo Azure Cosmos DB nativních sad SDK nebo REST API můžete kdykoli škálovat zřízenou propustnost. REST API Azure Cosmos DB poskytuje koncové body pro programové aktualizace úrovně výkonu kontejnerů, které usnadňují úpravu propustnosti kódu v závislosti na čase dne nebo dne v týdnu. Operace se provádí bez výpadků a obvykle se projeví za méně než minutu. 

* Jedna z oblastí, které byste měli škálovat, je při ingestování dat do Azure Cosmos DB například během migrace dat. Po dokončení migrace můžete nastavit škálování zřízené propustnosti za účelem zpracování stabilního stavu řešení.  

* Pamatujte si, že účtování je v členitosti na jednu hodinu, takže nebudete ukládat žádné peníze, pokud jste provedli změnu zajištěné propustnosti častěji než jednou za hodinu.

## <a name="determine-the-throughput-needed-for-a-new-workload"></a>Určení propustnosti potřebného pro nové zatížení 

K určení zřízené propustnosti pro novou úlohu můžete použít následující postup: 

1. Proveďte počáteční přibližné hodnocení pomocí plánovače kapacity a upravte své odhady pomocí Azure Cosmos Exploreru v Azure Portal. 

2. Doporučuje se vytvořit kontejnery s vyšší propustností, než se očekávalo, a pak podle potřeby škálovat dolů. 

3. Doporučuje se použít jednu z nativních sad Azure Cosmos DB SDK, abyste využili výhody automatických opakovaných pokusů, když se požadavky získají s omezením četnosti. Pokud pracujete na platformě, která není podporovaná a používá REST API Cosmos DB, implementujte vlastní zásady opakování pomocí `x-ms-retry-after-ms` hlavičky. 

4. Ujistěte se, že kód aplikace bez problémů podporuje případ, kdy se všechny opakované pokusy nezdaří. 

5. Můžete nakonfigurovat výstrahy z Azure Portal, abyste získali oznámení pro omezení četnosti. Po posledních 15 minutách můžete začít s konzervativními limity, jako je 10 – omezené žádosti za posledních 15 minut a přejít na další pravidla Eager, až si vydáte skutečnou spotřebu. Omezení příležitostné míry jsou podrobná, ukazují, že hrajete s omezeními, která jste nastavili a která je přesně to, co chcete udělat. 

6. Použijte monitorování, abyste porozuměli vzoru provozu, takže můžete zvážit nutnost dynamického přizpůsobení zřizování propustnosti v průběhu dne nebo týdne. 

7. Pravidelně Sledujte svůj zřízený poměr propustnosti a využití, abyste se ujistili, že jste nezřídili více než požadovaný počet kontejnerů a databází. Nízká výše zřízená propustnost je dobrým bezpečnostním kontrolou.  

### <a name="best-practices-to-optimize-provisioned-throughput"></a>Osvědčené postupy pro optimalizaci zřízené propustnosti 

Následující kroky vám pomůžou zajistit, aby vaše řešení byla při použití Azure Cosmos DB vysoce škálovatelná a nákladově efektivní.  

1. Pokud jste významně využili zajištěné propustnosti napříč kontejnery a databázemi, měli byste zkontrolovat ru zřízené vs spotřebované ru a vyladit úlohy.  

2. Jednou z metod pro odhad množství rezervované propustnosti, kterou vaše aplikace vyžaduje, je zaznamenat poplatky za RU jednotky žádosti spojené s běžícími typickými operacemi na reprezentativním kontejneru Azure Cosmos nebo databázi, kterou vaše aplikace používá, a pak odhadnout počet operací, které předpokládáte za sekundu. Nezapomeňte měřit a zahrnovat i typické dotazy a jejich využití. Informace o tom, jak odhadnout náklady na dotazy pomocí kódu programu nebo pomocí portálu, najdete v tématu [optimalizace nákladů na dotazy](./optimize-cost-reads-writes.md). 

3. Dalším způsobem, jak získat operace a jejich náklady v ru, je povolit protokoly Azure Monitor, což vám poskytne rozpis operace/trvání a poplatků za požadavek. Azure Cosmos DB poskytuje pro každou operaci poplatek za požadavky, takže každý poplatek za operaci lze uložit zpět z odpovědi a pak použít k analýze. 

4. Zajištěné propustnosti můžete elasticky škálovat nahoru a dolů, protože potřebujete přizpůsobit potřebám vašich úloh. 

5. Můžete přidávat a odebírat oblasti přidružené k vašemu účtu Azure Cosmos, jak potřebujete, a řídit náklady. 

6. Ujistěte se, že máte dokonce i distribuci dat a zatížení napříč logickými oddíly vašich kontejnerů. Pokud máte Nerovnoměrné rozdělení oddílů, může to vést k zajištění vyššího objemu propustnosti, než je požadovaná hodnota. Pokud zjistíte, že máte nakloněnou distribuci, doporučujeme, aby se zatížení rovnoměrně rozdělilo napříč oddíly nebo znovu rozdělit data. 

7. Pokud máte mnoho kontejnerů a tyto kontejnery nevyžadují SLA, můžete použít nabídku založenou na databázích pro případy, kdy se SLA na propustnost kontejneru nevztahuje. Měli byste určit, které z kontejnerů Azure Cosmos chcete migrovat do nabídky propustnosti na úrovni databáze, a pak je migrovat pomocí řešení změn na základě kanálu. 

8. Zvažte použití "Cosmos DB úrovně Free" (zdarma po dobu jednoho roku), vyzkoušejte Cosmos DB (až na tři oblasti) nebo proveďte stažení Cosmos DB emulátoru pro scénáře vývoje a testování. Pomocí těchto možností pro testování vývoje můžete významně snížit náklady.  

9. Můžete dále provádět optimalizace nákladů na konkrétní úlohy – například zvýšení velikosti dávek, čtení vyrovnávání zatížení napříč několika oblastmi a odstraňování duplicitních dat (Pokud je to možné).

10. Díky Azure Cosmos DB rezervované kapacity můžete pro tři roky získat až 65% významné slevy. Model rezervované kapacity Azure Cosmos DB je předem stanovený závazek na jednotky požadavků, které jsou potřeba v průběhu času. Tyto slevy jsou vrstveny, takže čím více jednotek požadavků budete používat v delší době, tím déle bude vaše sleva. Tyto slevy se projeví okamžitě. Všechny ru použité nad zřízené hodnoty se účtují na základě nerezervovaných nákladů na kapacitu. Další podrobnosti najdete v tématu [Cosmos DB rezervovanou kapacitu](cosmos-db-reserved-capacity.md)). Zvažte zakoupení rezervované kapacity k dalšímu snížení nákladů na náklady na zajištění propustnosti.  

## <a name="next-steps"></a>Další kroky

Další informace o optimalizaci nákladů v Azure Cosmos DB najdete v následujících článcích:

* Další informace o [optimalizaci pro vývoj a testování](optimize-dev-test.md)
* Další informace o [Azure Cosmos DB vyúčtování](understand-your-bill.md)
* Další informace o [optimalizaci nákladů na úložiště](optimize-cost-storage.md)
* Další informace o [optimalizaci nákladů na čtení a zápisy](optimize-cost-reads-writes.md)
* Další informace o [optimalizaci nákladů na dotazy](./optimize-cost-reads-writes.md)
* Další informace o [optimalizaci nákladů na účty Azure Cosmos ve více oblastech](optimize-cost-regions.md)
