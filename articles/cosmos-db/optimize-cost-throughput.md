---
title: Optimalizace nákladů na propustnost v Azure Cosmos DB
description: Tento článek vysvětluje, jak optimalizovat náklady na propustnost pro data uložená v Azure Cosmos DB.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: c80ab4acd745717e2e68ae7d9dc818594ad1ce9e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79501464"
---
# <a name="optimize-provisioned-throughput-cost-in-azure-cosmos-db"></a>Optimalizace nákladů na zřízenou propustnost ve službě Azure Cosmos DB

Díky nabídce zřízeného modelu propustnosti nabízí Azure Cosmos DB předvídatelný výkon v libovolném měřítku. Rezervace nebo zřizování propustnosti dopředu eliminuje "hlučný soused efekt" na výkon. Zadáte přesné množství propustnost, kterou potřebujete a Azure Cosmos DB zaručuje nakonfigurovanou propustnost, podporovanou sla.

Můžete začít s minimální propustností 400 RU/s a škálovat až desítky milionů požadavků za sekundu nebo dokonce více. Každý požadavek, který vystavíte proti kontejneru nebo databázi Azure Cosmos, jako je požadavek na čtení, požadavek na zápis, požadavek na dotaz, uložené procedury mají odpovídající náklady, které se odečtou z vaší zřízené propustnosti. Pokud zřídíte 400 RU/s a vydáte dotaz, který stojí 40 ru, budete moci vydat 10 takových dotazů za sekundu. Jakýkoli požadavek nad rámec, který bude mít rychlost-omezené a měli byste opakovat požadavek. Pokud používáte klientské ovladače, podporují logiku automatického opakování.

Propustnost můžete zřídit pro databáze nebo kontejnery a v závislosti na konkrétním scénáři vám obě strategie můžou pomoct ušetřit náklady.

## <a name="optimize-by-provisioning-throughput-at-different-levels"></a>Optimalizujte zřizováním propustností na různých úrovních

* Pokud zřídíte propustnost v databázi, všechny kontejnery, například kolekce/tabulky/grafy v rámci této databáze můžete sdílet propustnost na základě zatížení. Propustnost vyhrazená na úrovni databáze je sdílena nerovnoměrně, v závislosti na zatížení na konkrétní sadu kontejnerů.

* Pokud zřídíte propustnost na kontejneru, propustnost je zaručena pro tento kontejner, podporovaný SLA. Volba klíče logického oddílu je rozhodující pro rovnoměrné rozložení zatížení napříč všemi logickými oddíly kontejneru. Další podrobnosti najdete [v článku Dělení](partitioning-overview.md) a [vodorovné škálování](partition-data.md) článků.

Níže jsou uvedeny některé pokyny pro rozhodnutí o strategii zřízené propustnost:

**Zvažte zřizování propustnost v databázi Azure Cosmos (obsahující sadu kontejnerů), pokud**:

1. Máte několik desítek kontejnerů Azure Cosmos a chcete sdílet propustnost přes některé nebo všechny z nich. 

2. Migrujete z databáze s jedním tenantem určené ke spuštění na virtuálních počítačích hostovaných iaaS nebo místně, například NoSQL nebo relační databáze do Azure Cosmos DB. A pokud máte mnoho kolekcí / tabulek / grafů a nechcete provádět žádné změny datového modelu. Všimněte si, že budete muset ohrozit některé výhody, které nabízí Azure Cosmos DB, pokud neaktualizujete datový model při migraci z místní databáze. Doporučujeme vždy znovu získat přístup k datovému modelu, abyste získali co nejvíce z hlediska výkonu a také optimalizovali náklady. 

3. Chcete absorbovat neplánované špičky v úlohách na základě sdružené propustnost na úrovni databáze vystavené neočekávanému nárůstu zatížení. 

4. Namísto nastavení konkrétní propustnost na jednotlivé kontejnery, máte hlad o získání agregační propustnost přes sadu kontejnerů v rámci databáze.

**Zvažte zřizování propustnost na jednotlivé matné kontejneru, pokud:**

1. Máte několik kontejnerů Azure Cosmos. Vzhledem k tomu, že Azure Cosmos DB je bez ohledu na schéma, kontejner může obsahovat položky, které mají heterogenní schémata a nevyžaduje, aby zákazníci vytvořili více typů kontejnerů, jeden pro každou entitu. Je vždy možnost zvážit, zda seskupení samostatné řekněme 10-20 kontejnerů do jednoho kontejneru má smysl. S minimálně 400 RU pro kontejnery, sdružování všech 10-20 kontejnerů do jednoho může být nákladově efektivnější. 

2. Chcete řídit propustnost na konkrétní kontejner a získat zaručenou propustnost na daný kontejner zálohovaný SLA.

**Vezměme si hybrid výše uvedených dvou strategií:**

1. Jak již bylo zmíněno dříve, Azure Cosmos DB umožňuje kombinovat výše uvedené dvě strategie, takže teď můžete mít některé kontejnery v databázi Azure Cosmos, které mohou sdílet propustnost zzřízenou v databázi, stejně jako některé kontejnery v rámci stejné databáze , které mohou mít vyhrazené částky zřízená propustnost. 

2. Výše uvedené strategie můžete použít přijít s hybridní konfiguraci, kde máte obě úrovně databáze zřízená propustnost s některými kontejnery s vyhrazenou propustnost.

Jak je znázorněno v následující tabulce, v závislosti na volbě rozhraní API můžete zřídit propustnost v různých rozlišovacích podmínek.

|rozhraní API|Pro **sdílenou** propustnost nakonfigurujte |Pro **vyhrazenou** propustnost nakonfigurujte |
|----|----|----|
|SQL API|Databáze|Kontejner|
|Rozhraní API služby Azure Cosmos DB pro MongoDB|Databáze|Kolekce|
|Rozhraní Cassandra API|Prostor klíče|Table|
|Rozhraní Gremlin API|Databázový účet|Graph|
|Rozhraní Table API|Databázový účet|Table|

Zřízením propustnost na různých úrovních můžete optimalizovat náklady na základě charakteristik úlohy. Jak již bylo zmíněno dříve, můžete programově a kdykoli zvýšit nebo snížit zřízenou propustnost pro jednotlivé kontejnery nebo společně v rámci sady kontejnerů. Pružně škálování propustnost jako změny pracovního vytížení, platíte pouze za propustnost, kterou jste nakonfigurovali. Pokud je váš kontejner nebo sada kontejnerů distribuována ve více oblastech, pak je zaručeno, že propustnost, kterou nakonfigurujete v kontejneru nebo sada kontejnerů, bude k dispozici ve všech oblastech.

## <a name="optimize-with-rate-limiting-your-requests"></a>Optimalizujte s omezením rychlosti vašich požadavků

Pro úlohy, které nejsou citlivé na latenci, můžete zřídit menší propustnost a nechat aplikaci zpracovávat omezení rychlosti, když skutečná propustnost překročí zřízené propustnosti. Server preventivně ukončí požadavek `RequestRateTooLarge` (stavový kód HTTP 429) a vrátí `x-ms-retry-after-ms` záhlaví označující dobu v milisekundách, po kterou musí uživatel před opakováním požadavku počkat. 

```html
HTTP Status 429, 
 Status Line: RequestRateTooLarge 
 x-ms-retry-after-ms :100
```

### <a name="retry-logic-in-sdks"></a>Logika opakování v sadách SDK 

Nativní sady SDK (jádra.NET/.NET, java, node.js a Python) implicitně zachycují tuto odpověď, respektují hlavičku po opakování zadané serverem a opakují požadavek. Pokud váš účet není přistupovat současně více klientů, další opakování bude úspěšné.

Pokud máte více než jednoho klienta, který kumulativně pracuje konzistentně nad sazbu požadavku, nemusí být výchozí počet opakování, který je aktuálně nastaven na hodnotu 9, dostačující. V takových případech klient vyvolá `RequestRateTooLargeException` se stavovým kódem 429 do aplikace. Výchozí počet opakování lze změnit nastavením `RetryOptions` instance ConnectionPolicy. Ve výchozím `RequestRateTooLargeException` nastavení se stavovým kódem 429 je vrácena po kumulativní čekací doby 30 sekund, pokud požadavek nadále pracovat nad sazbu požadavku. K tomu dochází i v případě, že aktuální počet opakování je menší než maximální počet opakování, ať už je to výchozí hodnota 9 nebo uživatelem definovaná hodnota. 

[MaxRetryAttemptsOnThrottledRequests](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.retryoptions.maxretryattemptsonthrottledrequests?view=azure-dotnet) je nastavena na 3, takže v tomto případě pokud je operace požadavku omezena překročením vyhrazené propustnosti pro kontejner, operace požadavku opakování třikrát před vyvoláním výjimky do aplikace. [MaxRetryWaitTimeInSeconds](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.retryoptions.maxretrywaittimeinseconds?view=azure-dotnet#Microsoft_Azure_Documents_Client_RetryOptions_MaxRetryWaitTimeInSeconds) je nastavena na 60, takže v tomto případě pokud kumulativní opakování čekací doba v sekundách od prvního požadavku překročí 60 sekund, je vyvolána výjimka.

```csharp
ConnectionPolicy connectionPolicy = new ConnectionPolicy(); 
connectionPolicy.RetryOptions.MaxRetryAttemptsOnThrottledRequests = 3; 
connectionPolicy.RetryOptions.MaxRetryWaitTimeInSeconds = 60;
```

## <a name="partitioning-strategy-and-provisioned-throughput-costs"></a>Strategie dělení a náklady na zřízenou propustnost

Dobrá strategie dělení je důležitá pro optimalizaci nákladů v Azure Cosmos DB. Ujistěte se, že neexistuje žádné zkosení oddílů, které jsou vystaveny prostřednictvím metriky úložiště. Ujistěte se, že neexistuje žádné zkosení propustnost pro oddíl, který je vystaven s propustnost metriky. Ujistěte se, že neexistuje žádný zkosení směrem k určité klíče oddílu. Dominantní klíče v úložišti jsou vystaveny prostřednictvím metriky, ale klíč bude záviset na vzoru přístupu k aplikaci. Nejlepší je přemýšlet o správném logickém klíči oddílu. Očekává se, že dobrý klíč oddílu bude mít následující charakteristiky:

* Zvolte klíč oddílu, který rovnoměrně rozloží úlohy napříč všemi oddíly a rovnoměrně v průběhu času. Jinými slovy, neměli byste mít některé klíče s většinou dat a některé klíče s méně nebo žádná data. 

* Zvolte klíč oddílu, který umožňuje rovnoměrně rozprostřené vzory přístupu mezi logické oddíly. Úloha je přiměřeně i napříč všemi klíči. Jinými slovy, většina úlohy by neměla být zaměřena na několik konkrétních klíčů. 

* Zvolte klíč oddílu, který má širokou škálu hodnot. 

Základní myšlenkou je rozložení dat a aktivity v kontejneru mezi sadu logických oddílů, aby prostředky pro ukládání dat a propustnost lze distribuovat mezi logické oddíly. Kandidáti pro klíče oddílů mohou obsahovat vlastnosti, které se v dotazech často zobrazují jako filtr. Dotazy lze efektivně směrovat zahrnutím klíče oddílu do predikátu filtru. S takovou strategií dělení bude optimalizace zřízená propustnost mnohem jednodušší. 

### <a name="design-smaller-items-for-higher-throughput"></a>Návrh menších položek pro vyšší propustnost 

Poplatek za požadavek nebo náklady na zpracování požadavku dané operace přímo souvisí s velikostí položky. Operace s velkými položkami budou stát více než operace s menšími položkami. 

## <a name="data-access-patterns"></a>Vzory přístupu k datům 

Je vždy vhodné logicky oddělit data do logických kategorií na základě toho, jak často k těmto datům přistupujete. Kategorizací jako horká, střední nebo studená data můžete doladit úložiště spotřebované a požadovanou propustnost. V závislosti na četnosti přístupu můžete data umístit do samostatných kontejnerů (například tabulek, grafů a kolekcí) a doladit zřízenou propustnost na nich tak, aby vyhovovala potřebám tohoto segmentu dat. 

Kromě toho pokud používáte Azure Cosmos DB a víte, že nebudete hledat podle určitých datových hodnot nebo k nim budete mít jen zřídka přístup, měli byste uložit komprimované hodnoty těchto atributů. Pomocí této metody ušetříte úložný prostor, indexovací prostor a zřízenou propustnost a výsledkem budou nižší náklady.

## <a name="optimize-by-changing-indexing-policy"></a>Optimalizace změnou zásad indexování 

Ve výchozím nastavení Azure Cosmos DB automaticky indexuje každou vlastnost každého záznamu. To je určen pro usnadnění vývoje a zajištění vynikající výkon v mnoha různých typů dotazů ad hoc. Pokud máte velké záznamy s tisíci vlastnostmi, nemusí být platba nákladů na propustnost pro indexování každé vlastnosti užitečná, zejména pokud se dotazujete pouze na 10 nebo 20 těchto vlastností. Jak se dostanete blíže k získání popisovač na konkrétní pracovní vytížení, naše pokyny je vyladit zásady indexu. Podrobné informace o zásadách indexování Azure Cosmos DB najdete [zde](indexing-policies.md). 

## <a name="monitoring-provisioned-and-consumed-throughput"></a>Monitorování zřízené a spotřebované propustnosti 

Můžete sledovat celkový počet zřízených ru, počet požadavků s omezenou sazbou a také počet ru, které jste spotřebované na webu Azure Portal. Následující obrázek znázorňuje ukázkovou metriku využití:

![Monitorování jednotek požadavků na webu Azure Portal](./media/optimize-cost-throughput/monitoring.png)

Můžete také nastavit výstrahy pro kontrolu, zda počet požadavků s omezenou mírou překračuje určitou prahovou hodnotu. Další informace najdete v článku [Jak monitorovat Azure Cosmos DB](use-metrics.md) další podrobnosti. Tyto výstrahy můžete odeslat e-mail správcům účtu nebo volání vlastní HTTP Webhook nebo funkce Azure automaticky zvýšit zřízenou propustnost. 

## <a name="scale-your-throughput-elastically-and-on-demand"></a>Elastické měřítko propustnost i na vyžádání 

Vzhledem k tomu, že se vám účtují zřízené propustnost, odpovídající zřízená propustnost vašim potřebám vám pomůže vyhnout se poplatkům za nevyužitou propustnost. Zřízenou propustnost můžete kdykoli podle potřeby škálovat. Pokud jsou vaše potřeby propustnost velmi předvídatelné, můžete použít funkce Azure a pomocí aktivační události časovače [zvýšit nebo snížit propustnost podle plánu](scale-on-schedule.md). 

* Sledování spotřeby vašich ru a poměr požadavků s omezenou mírou může odhalit, že není nutné udržovat zřízené po celý den nebo týden. V noci nebo o víkendu můžete dostat menší provoz. Pomocí azure portálu nebo Azure Cosmos DB nativní sady SDK nebo rozhraní REST API můžete kdykoli škálovat zřízené propustnost. Rozhraní REST rozhraní REST služby Azure Cosmos DB poskytuje koncové body pro programovou aktualizaci úrovně výkonu kontejnerů, takže je jednoduché upravit propustnost z vašeho kódu v závislosti na denní době nebo dni v týdnu. Operace se provádí bez prostojů a obvykle se projeví za méně než minutu. 

* Jednou z oblastí, které byste měli škálovat propustnost je při ingestování dat do Azure Cosmos DB, například během migrace dat. Po dokončení migrace můžete škálovat zřízenou propustnost dolů tak, aby zpracovával ustálený stav řešení.  

* Nezapomeňte, že fakturace je rozlišovací na jednu hodinu, takže nebudete šetřit žádné peníze, pokud změníte zřízenou propustnost častěji než jednu hodinu najednou.

## <a name="determine-the-throughput-needed-for-a-new-workload"></a>Určení propustnosti potřebné pro nové pracovní vytížení 

Chcete-li určit zřízenou propustnost pro nové zatížení, můžete použít následující kroky: 

1. Proveďte počáteční hrubé vyhodnocení pomocí plánovače kapacit a upravte odhady pomocí Průzkumníka Azure Cosmos na webu Azure Portal. 

2. Doporučujeme vytvořit kontejnery s vyšší propustností, než bylo očekáváno, a pak škálování dolů podle potřeby. 

3. Doporučujeme použít jeden z nativních sad Azure Cosmos DB SDK s využitím výhod automatické hoopakování při získání požadavků s omezenou sazbou. Pokud pracujete na platformě, která není podporována a používat rozhraní REST API Cosmos `x-ms-retry-after-ms` DB, implementujte vlastní zásady opakování pomocí záhlaví. 

4. Ujistěte se, že kód aplikace řádně podporuje případ, když všechny pokusy nezdaří. 

5. Můžete nakonfigurovat výstrahy z portálu Azure dostávat oznámení o omezení rychlosti. Můžete začít s konzervativními limity, jako jsou požadavky s omezenými 10 sazbami za posledních 15 minut, a přepnout na dychtivější pravidla, jakmile zjistíte skutečnou spotřebu. Občasné limity sazby jsou v pořádku, ukazují, že hrajete s limity, které jste nastavili, a to je přesně to, co chcete dělat. 

6. Pomocí monitorování pochopte svůj vzor provozu, abyste mohli zvážit potřebu dynamicky upravit zřizování propustnosti během dne nebo týdne. 

7. Pravidelně monitorujte svůj zřízený a spotřebovaný poměr propustnosti a ujistěte se, že jste nezpracovali víc než požadovaný počet kontejnerů a databází. S trochu nad zřízenou propustností je dobrá bezpečnostní kontrola.  

### <a name="best-practices-to-optimize-provisioned-throughput"></a>Doporučené postupy pro optimalizaci zřízené propustnosti 

Následující kroky vám pomůžou, aby vaše řešení byla vysoce škálovatelná a nákladově efektivní při používání Azure Cosmos DB.  

1. Pokud jste výrazně překročili zřízenou propustnost napříč kontejnery a databázemi, měli byste zkontrolovat zřízené ru zřízené vs spotřebované ru a doladit úlohy.  

2. Jednou z metod pro odhad velikosti rezervované propustnosti vyžadované vaší aplikací je zaznamenat poplatek RU jednotky požadavku spojený se spuštěním typických operací proti reprezentativnímu kontejneru nebo databázi Azure Cosmos používané vaší aplikací a pak odhadněte počet operací, které očekáváte k provedení každé sekundy. Nezapomeňte měřit a zahrnout typické dotazy a jejich použití také. Informace o tom, jak programově odhadnout náklady ru na dotazy nebo pomocí portálu, najdete [v tématu Optimalizace nákladů na dotazy](../synapse-analytics/sql-data-warehouse/backup-and-restore.md). 

3. Dalším způsobem, jak získat operace a jejich náklady v ru, je povolení protokolů Azure Monitor, které vám poskytnou rozpis operace nebo doby trvání a poplatek za požadavek. Azure Cosmos DB poskytuje poplatek za požadavek pro každou operaci, takže každý poplatek za operaci lze uložit zpět z odpovědi a pak použít pro analýzu. 

4. Můžete elasticky vertikálně vertikálně vertikálně navýšit a snížit zřízenou propustnost, jak potřebujete, aby vyhovovalvašim potřebám úlohy. 

5. Můžete přidat a odebrat oblasti přidružené k účtu Azure Cosmos podle potřeby a řídit náklady. 

6. Ujistěte se, že máte i distribuci dat a úloh napříč logickými oddíly kontejnerů. Pokud máte nerovnoměrné rozdělení oddílu, to může způsobit zřízení vyšší množství propustnost než hodnota, která je potřeba. Pokud zjistíte, že máte zkosené rozdělení, doporučujeme rovnoměrně distribuovat úlohy mezi oddíly nebo přerozdělit data. 

7. Pokud máte mnoho kontejnerů a tyto kontejnery nevyžadují sla, můžete použít nabídku založenou na databázi pro případy, kdy se nevztahují na kontejner propustnost. Měli byste určit, které z kontejnerů Azure Cosmos, které chcete migrovat do nabídky propustnost na úrovni databáze a pak je migrovat pomocí řešení založeného na kanálu změn. 

8. Zvažte použití "Cosmos DB Free Tier" (zdarma po dobu jednoho roku), Zkuste Cosmos DB (až tři oblasti) nebo ke stažení Cosmos DB emulátor pro scénáře pro vývoj a testování. Pomocí těchto možností pro test-dev, můžete podstatně snížit své náklady.  

9. Můžete dále provádět optimalizace nákladů specifické pro úlohu – například zvýšení dávky velikosti, vyrovnávání zatížení čtení ve více oblastech a de-duplikace dat, pokud je to možné.

10. S rezervovanou kapacitou Azure Cosmos DB můžete získat významné slevy až 65 % po dobu tří let. Azure Cosmos DB rezervované kapacity modelu je předem závazek na jednotky požadavků potřebné v průběhu času. Slevy jsou odstupňovány tak, že čím více jednotek požadavků používáte po delší období, tím více bude vaše sleva. Tyto slevy jsou uplatněny okamžitě. Všechny ru používané nad zřízenými hodnotami se účtují na základě nerezervovaných nákladů na kapacitu. Další podrobnosti najdete v [části Rezervovaná kapacita Cosmos DB](cosmos-db-reserved-capacity.md)). Zvažte zakoupení rezervované kapacity, abyste dále snížili náklady na zřízenou propustnost.  

## <a name="next-steps"></a>Další kroky

Další informace o optimalizaci nákladů v Azure Cosmos DB najdete v následujících článcích:

* Další informace o [optimalizaci pro vývoj a testování](optimize-dev-test.md)
* Další informace o [vysvětlení vaší faktury z DB Služby Azure Cosmos](understand-your-bill.md)
* Další informace o [optimalizaci nákladů na úložiště](optimize-cost-storage.md)
* Další informace o [optimalizaci nákladů na čtení a zápisy](optimize-cost-reads-writes.md)
* Další informace o [optimalizaci nákladů na dotazy](optimize-cost-queries.md)
* Další informace o [optimalizaci nákladů na účty Azure Cosmos s více oblastmi](optimize-cost-regions.md)

