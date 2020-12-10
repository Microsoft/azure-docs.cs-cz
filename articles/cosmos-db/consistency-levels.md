---
title: Úrovně konzistence ve službě Azure Cosmos DB
description: Azure Cosmos DB má pět úrovní konzistence, které vám pomůžou vyvážit případné kompromisy v konzistenci, dostupnosti a latenci.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/12/2020
ms.openlocfilehash: 965e4a8cd704670ec06ae6b927b97c3a8b93030c
ms.sourcegitcommit: dea56e0dd919ad4250dde03c11d5406530c21c28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/09/2020
ms.locfileid: "96938660"
---
# <a name="consistency-levels-in-azure-cosmos-db"></a>Úrovně konzistence ve službě Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Distribuované databáze, které spoléhají na replikaci při vysoké dostupnosti, nízké latenci nebo obojí, musí učinit zásadní kompromis mezi úrovní konzistence čtení, dostupností, latencí a propustností podle definice [PACLC věta](https://en.wikipedia.org/wiki/PACELC_theorem). Linearizability modelu silné konzistence je Gold Standard pro programovatelnost dat. Přidává ale strméou cenu z vyšších latencí zápisu, protože data se musí replikovat a potvrzovat napříč velkými vzdálenostmi. Silná konzistence může mít taky vliv na omezenou dostupnost (během selhání), protože data nejde replikovat a potvrzovat v každé oblasti. Konečná konzistence nabízí vyšší dostupnost a lepší výkon, ale jejich aplikace je obtížnější, protože data nemusí být zcela konzistentní napříč všemi oblastmi.

Většina komerčně dostupných distribuovaných databází NoSQL dostupných na trhu nabízí jenom silnou a konečnou konzistenci. Azure Cosmos DB nabízí pět jasně definovaných úrovní. Od nejsilnějších po nejslabších úrovních jsou tyto úrovně:

- *Silná*
- *Ohraničená neaktuálnost*
- *Relace*
- *Konzistentní předpona*
- *Konečné*

Každá úroveň poskytuje kompromisy k dostupnosti a výkonu. Následující obrázek znázorňuje různé úrovně konzistence jako spektrum.

:::image type="content" source="./media/consistency-levels/five-consistency-levels.png" alt-text="Konzistence jako spektrum" border="false" :::

Úrovně konzistence jsou nezávislá oblasti a jsou zaručené pro všechny operace bez ohledu na oblast, ze které jsou předávány čtení a zápisy, počet oblastí přidružených k vašemu účtu Azure Cosmos nebo zda je váš účet nakonfigurován s jednou nebo více oblastmi zápisu.

## <a name="consistency-levels-and-azure-cosmos-db-apis"></a>Úrovně konzistence a rozhraní API služby Cosmos DB

Azure Cosmos DB poskytuje nativní podporu pro rozhraní API kompatibilní se síťovými protokoly pro oblíbené databáze. Patří mezi ně MongoDB, Apache Cassandra, Gremlin a Azure Table Storage. Při použití rozhraní Gremlin API a rozhraní API pro tabulky se používá výchozí úroveň konzistence nakonfigurovaná na účtu Azure Cosmos. Podrobnosti o mapování na úrovni konzistence mezi rozhraní API Cassandra nebo rozhraní API pro MongoDB a Azure Cosmos DB úrovně konzistence najdete v tématu [rozhraní API Cassandra mapování konzistence](cassandra-consistency.md) a [rozhraní API pro mapování konzistence MongoDB](mongodb-consistency.md).

## <a name="scope-of-the-read-consistency"></a>Rozsah konzistence čtení

Konzistence čtení se vztahuje na jeden obor operace čtení v rámci logického oddílu. Operaci čtení je možné vystavit pomocí vzdáleného klienta nebo uložené procedury.

## <a name="configure-the-default-consistency-level"></a>Konfigurace výchozí úrovně konzistence

Výchozí úroveň konzistence můžete v účtu Azure Cosmos nakonfigurovat kdykoli. Výchozí úroveň konzistence nakonfigurovaná na vašem účtu se vztahuje na všechny databáze a kontejnery Azure Cosmos pod tímto účtem. Všechny čtení a dotazy vydané pro kontejner nebo databázi používají ve výchozím nastavení zadanou úroveň konzistence. Další informace najdete v tématu Postup [Konfigurace výchozí úrovně konzistence](how-to-manage-consistency.md#configure-the-default-consistency-level). U konkrétního požadavku můžete také přepsat výchozí úroveň konzistence. Další informace najdete v článku postup [přepsání výchozí úrovně konzistence](how-to-manage-consistency.md?#override-the-default-consistency-level) .

> [!IMPORTANT]
> Po změně výchozí úrovně konzistence se musí znovu vytvořit jakákoli instance sady SDK. To se dá udělat restartováním aplikace. Tím se zajistí, že sada SDK bude používat novou výchozí úroveň konzistence.

## <a name="guarantees-associated-with-consistency-levels"></a>Záruky spojené s úrovněmi konzistence

Azure Cosmos DB zaručuje, že 100 procento žádostí o čtení odpovídá záruku konzistence pro zvolenou úroveň konzistence. Přesné definice pěti úrovní konzistence v Azure Cosmos DB používání jazyka TLA + Specification jsou k dispozici v úložišti GitHub [Azure-Cosmos-tla](https://github.com/Azure/azure-cosmos-tla) .

Sémantika pěti úrovní konzistence je popsána zde:

- **Strong**: silná konzistence nabízí záruku linearizability. Linearizability odkazuje na obsluhu souběžných požadavků. U čtení je zaručeno, že vrátí nejnovější potvrzenou verzi položky. Klient nikdy nevidí nepotvrzené nebo částečné zápisy. Uživatelům se vždycky ručí, že si přečtou poslední potvrzený zápis.

  Následující obrázek znázorňuje silnou konzistenci se hudebními poznámkami. Po zapsání dat do oblasti "Západní USA 2" se při čtení dat z jiných oblastí zobrazí nejnovější hodnota:

  :::image type="content" source="media/consistency-levels/strong-consistency.gif" alt-text="Ilustrace silné úrovně konzistence":::

- **Ohraničená neaktuálnost**: čtení jsou zaručena, aby se zaručila záruka konzistentní s předponou. Čtení můžou na konci zápisu zajímat maximálně *"K"* verzí (to znamená "aktualizace") položky nebo časového intervalu *"T"* , podle toho, co je dosaženo jako první. Jinými slovy, pokud vyberete možnost ohraničená neaktuálnost, lze nakonfigurovat "zastaralost" dvěma způsoby:

- Počet verzí položky (*KB*)
- Čtení s časovým intervalem (*T*) může odvést zpoždění za zápisy.

Pro účet s jednou oblastí je minimální hodnota *K* a *T* 10 operací zápisu nebo 5 sekund. Pro účty ve více oblastech je minimální hodnota *K* a *T* 100 000 operací zápisu nebo 300 sekund.

Ohraničená neaktuálnost nabízí celkové globální pořadí mimo "okno pro zastaralost". Když klient provádí operace čtení v rámci oblasti, která přijímá zápisy, záruky poskytnuté konzistencí neaktuálnosti jsou stejné jako záruky silnými konzistencí. Vzhledem k tomu, že se v okně zastaralosti blíží čas nebo aktualizace, podle toho, co je blíže, služba omezí nové zápisy, aby replikace mohla zachytit a přijmout záruku konzistence.

V rámci okna zastaralost poskytuje ohraničená neaktuálnost následující záruky konzistence:

- Konzistence klientů ve stejné oblasti pro účet s oblastí pro zápis s jednou oblastí zápisu = Strong
- Konzistence klientů v různých oblastech pro účet s jednou oblastí zápisu = konzistentní předpona
- Konzistence klientů zapisujících do jedné oblasti pro účet s více oblastmi zápisu = konzistentní předpona
- Konzistence pro klienty zapisujících do různých oblastí pro účet s více oblastmi zápisu = případné

  Ohraničená neaktuálnost se často volí globálně distribuovanými aplikacemi, které očekávají nízkou latenci zápisu, ale vyžadují celkovou záruku globální objednávky. Ohraničená neaktuálnost je ideální pro aplikace, které nabízí spolupráci skupin a sdílení, burzovní, doplňování a publikování a zařazování do fronty atd. Následující obrázek znázorňuje konzistenci s ohraničenou neaktuálností pomocí hudebních poznámek. Po zapsání dat do oblasti "Západní USA 2" přečtou oblasti "Východní USA 2" a "Austrálie – východ" písemnou hodnotu na základě nakonfigurovaného maximálního času prodlevy nebo maximálního počtu operací:

  :::image type="content" source="media/consistency-levels/bounded-staleness-consistency.gif" alt-text="Ilustrace úrovně konzistence s ohraničenou kostarou":::

- **Relace**: v rámci jediného čtení klientské relace jsou zaručené respektování konzistentní předpony, monotónní čtení, monotónní zápisu, čtení a zápisů a záruky za zápis. Předpokládá se jedna relace "zapisovače" nebo sdílení tokenu relace pro více modulů pro zápis.

Klientům mimo relaci, která provádí zápis, se zobrazí následující záruky:

- Konzistence pro klienty ve stejné oblasti pro účet s jednou oblastí zápisu = konzistentní předpona
- Konzistence klientů v různých oblastech pro účet s jednou oblastí zápisu = konzistentní předpona
- Konzistence klientů zapisujících do jedné oblasti pro účet s více oblastmi zápisu = konzistentní předpona
- Konzistence klientů zapisujících do více oblastí pro účet s více oblastmi zápisu = případné

  Konzistence relací je nejčastěji používaná úroveň konzistence pro jednu oblast i pro globálně distribuované aplikace. Poskytuje latence zápisu, dostupnost a propustnost čtení srovnatelné s tím, že má konečnou konzistenci, ale také poskytuje záruky konzistence, které vyhovují potřebám aplikací zapsaných v kontextu uživatele. Následující obrázek znázorňuje konzistenci relace se hudebními poznámkami. "Západní USA 2 Writer" a "Západní USA 2 Reader" používají stejnou relaci (relaci A), aby obě současně četly stejná data. Vzhledem k tomu, že oblast Austrálie – východ používá "relaci B", získá data později, ale ve stejném pořadí jako zápisy.

  :::image type="content" source="media/consistency-levels/session-consistency.gif" alt-text="Ilustrace úrovně konzistence relace":::

- **Konzistentní předpona**: vrácené aktualizace obsahují předponu všech aktualizací bez mezer. Konzistentní předpony úrovně konzistence, které nemají nikdy vidět zápisy mimo pořadí.

Pokud byla zápisy provedena v pořadí `A, B, C` , klient uvidí buď `A` , `A,B` nebo `A,B,C` , ale nikdy mimo pořadí, například `A,C` nebo `B,A,C` . Konzistentní předpona poskytuje latence zápisu, dostupnost a propustnost čtení srovnatelné s tím, že má konečnou konzistenci, ale také poskytuje pořadí záruk, které vyhovuje potřebám scénářů, ve kterých je pořadí důležité.

Níže jsou uvedené záruky konzistence pro konzistentní předpony:

- Konzistence pro klienty ve stejné oblasti pro účet s jednou oblastí zápisu = konzistentní předpona
- Konzistence klientů v různých oblastech pro účet s jednou oblastí zápisu = konzistentní předpona
- Konzistence klientů zapisujících do jedné oblasti pro účet s více oblastmi pro zápis = konzistentní předpona
- Konzistence klientů zapisujících do více oblastí pro účet s více oblastmi zápisu = případný

Následující obrázek znázorňuje konzistenci předpon konzistence se hudebními poznámkami. Ve všech oblastech čtení nikdy nevidí zápisy mimo pořadí:

  :::image type="content" source="media/consistency-levels/consistent-prefix.gif" alt-text="Obrázek konzistentní předpony":::

- Kdy **: neexistuje** záruka na řazení pro čtení. Pokud nedojde k žádným dalším operacím zápisu, repliky se nakonec konvergují.  
Konečná konzistence představuje slabší formu konzistence, protože klient může číst hodnoty, které jsou starší než ty, které se předtím četly. Konečná konzistence je ideální, pokud aplikace nevyžaduje žádné záruky na řazení. Mezi příklady patří počet re, podobně jako u jiných než vlákenných komentářů. Následující obrázek znázorňuje konečnou konzistenci se hudebními poznámkami.

  :::image type="content" source="media/consistency-levels/eventual-consistency.gif" alt-text="viIllustration s konečnou konzistencí":::

## <a name="consistency-guarantees-in-practice"></a>Záruky konzistence v praxi

V praxi můžete často získat silnější záruky konzistence. Záruky konzistence pro operaci čtení odpovídají aktuálnosti a objednání stavu databáze, který požadujete. Konzistence čtení je vázána na řazení a šíření operací zápisu a aktualizace.  

Pokud v databázi neexistují žádné operace zápisu, může být operace čtení s úrovněmi konzistence s **případnými**, **relačními** nebo **konzistentními předponami** stejná jako operace čtení se silným stupněm konzistence.

Pokud je váš účet Azure Cosmos nakonfigurovaný s úrovní konzistence jinou než silná konzistence, můžete zjistit pravděpodobnost, že klienti mohou získat silné a konzistentní čtení pro vaše úlohy, a to tak, že se podíváte na metriku služby PBS ( *probabilistically Bounded* ). Tato metrika se zveřejňuje v Azure Portal. Další informace najdete v tématu [monitorování metriky služby PBS (probabilistically Bounded)](how-to-manage-consistency.md#monitor-probabilistically-bounded-staleness-pbs-metric).

Pravděpodobnostní s ohraničenou neaktuálností ukazuje, jak je to vaše konečná konzistence. Tato metrika poskytuje přehled o tom, jak často můžete získat silnější konzistenci než úroveň konzistence, kterou jste v účtu Azure Cosmos aktuálně nakonfigurovali. Jinými slovy můžete zobrazit pravděpodobnost (měřeno v milisekundách) pro získání silně konzistentních čtení pro kombinaci oblastí zápisu a čtení.

## <a name="consistency-levels-and-latency"></a>Úrovně konzistence a latence

Latence čtení pro všechny úrovně konzistence je vždycky zaručená za méně než 10 milisekund na 99. percentilu. Průměrná latence čtení v 50. percentilu má obvykle 4 milisekundy nebo méně.

Latence zápisu pro všechny úrovně konzistence je vždycky zaručená za méně než 10 milisekund na 99. percentilu. Průměrná latence zápisu na 50. percentilu je obvykle 5 milisekund nebo méně. Účty Azure Cosmos, které jsou v několika oblastech a jsou nakonfigurované se silnými konzistencí, představují výjimku z této záruky.

### <a name="write-latency-and-strong-consistency"></a>Latence zápisu a silná konzistence

U účtů Azure Cosmos nakonfigurovaných se silnou konzistencí s více než jednou oblastí se latence zápisu rovná dvojnásobku doby odezvy (RTT) mezi kteroukoli ze dvou nejvzdálenějších oblastí a 10 milisekundami v 99 percentilu. Vysoká doba odezvy sítě mezi oblastmi se přeloží na vyšší latenci pro Cosmos DB požadavky, protože silná konzistence dokončí operaci jenom poté, co zaručí, že se potvrdila ve všech oblastech v rámci účtu.

Přesná latence RTT je funkce rychlosti a topologie sítě Azure. Azure Networking neposkytuje žádnou latenci SLA pro dobu odezvy mezi dvěma oblastmi Azure, ale zveřejňuje [statistiku latence pro službu Azure Network Round-Trip](../networking/azure-network-latency.md). Pro váš účet Azure Cosmos se latence replikace zobrazují v Azure Portal. Pokud chcete monitorovat latence replikace mezi různými oblastmi, které jsou přidružené k vašemu účtu Azure Cosmos, můžete použít Azure Portal (Přejít do okna metrika a vybrat kartu konzistence).

> [!IMPORTANT]
> Při vysoké latenci zápisu je ve výchozím nastavení zablokovaná silná konzistence pro účty s oblastmi zahrnujícími více než 5000 mil (8000 kilometrů). Pokud chcete tuto funkci povolit, obraťte se prosím na podporu.

## <a name="consistency-levels-and-throughput"></a>Úrovně konzistence a propustnost

- V případě silné a ohraničené neaktuálnosti se čtení provádí se dvěma replikami ve čtyři sadě replik (minoritní kvorum), aby poskytovala záruky konzistence. Relace, konzistentní předpona a konečné čtení pro jednu repliku. Výsledkem je, že pro stejný počet jednotek žádosti je propustnost čtení pro silná a ohraničená neaktuálnost v polovině dalších úrovní konzistence.

- Pro daný typ operace zápisu, například INSERT, Replace, Upsert a DELETE, je propustnost zápisu pro jednotky požadavků stejná pro všechny úrovně konzistence.

|**Úroveň konzistence**|**Čtení kvora**|**Zápisy kvora**|
|--|--|--|
|**Silná**|Místní menšina|Globální většina|
|**Ohraničená neaktuálnost**|Místní menšina|Místní většina|
|**Relace**|Jedna replika (pomocí tokenu relace)|Místní většina|
|**Konzistentní předpona**|Jedna replika|Místní většina|
|**Konečné**|Jedna replika|Místní většina|

> [!NOTE]
> Náklady na čtení v úrovni RU/s pro místní minoritní čtení jsou dvojnásobné od slabší úrovně konzistence, protože čtení se provádí ze dvou replik, které poskytují záruky konzistence pro silnou a ohraničenou neaktuálnost.

## <a name="consistency-levels-and-data-durability"></a><a id="rto"></a>Úrovně konzistence a trvanlivost dat

V globálně distribuovaném databázovém prostředí existuje přímý vztah mezi úrovní konzistence a odolností s daty v oblasti výpadku v rámci oblasti. Při vývoji plánu provozní kontinuity musíte pochopit maximální přijatelnou dobu, než se aplikace kompletně obnoví po přerušení události. Čas potřebný k úplnému obnovení aplikace je známý jako **cíl doby obnovení** (**RTO**). Také je potřeba porozumět maximálnímu intervalu nedávných aktualizací dat, které může aplikace tolerovat při obnovování po přerušení události. Časové období aktualizací, které můžete chtít ztratit, se označuje jako **cíl bodu obnovení** (**RPO**).

Následující tabulka definuje vztah mezi modelem konzistence a odolností dat při výpadku oblasti v rámci sítě. Je důležité si uvědomit, že v distribuovaném systému, a to i se silnou konzistencí, není možné mít distribuovanou databázi s cílem RPO a RTO nula z důvodu [Cap věta](https://en.wikipedia.org/wiki/CAP_theorem).

|**Oblast (y)**|**Režim replikace**|**Úroveň konzistence**|**RPO**|**RTO**|
|---------|---------|---------|---------|---------|
|1|Jedna nebo více oblastí zápisu|Jakákoli úroveň konzistence|< 240 minut|<1 týden|
|>1|Jedna oblast zápisu|Relace, konzistentní předpona, případný|< 15 minut|< 15 minut|
|>1|Jedna oblast zápisu|Omezená neaktuálnost|*K*  &  *T*|< 15 minut|
|>1|Jedna oblast zápisu|Silná|0|< 15 minut|
|>1|Více oblastí zápisu|Relace, konzistentní předpona, případný|< 15 minut|0|
|>1|Více oblastí zápisu|Omezená neaktuálnost|*K*  &  *T*|0|

*K* = počet verzí *"K"* (tj. aktualizace) položky.

*T* = časový interval *"t"* od poslední aktualizace.

Pro účet s jednou oblastí je minimální hodnota *K* a *T* 10 operací zápisu nebo 5 sekund. Pro účty ve více oblastech je minimální hodnota *K* a *T* 100 000 operací zápisu nebo 300 sekund. Tato definice definuje minimální RPO pro data při použití ohraničené neaktuálnosti.

## <a name="strong-consistency-and-multiple-write-regions"></a>Silná konzistence a vícenásobné oblasti zápisu

Účty Cosmos nakonfigurované s více oblastmi zápisu nelze nakonfigurovat pro zajištění vysoké konzistence, protože distribuovaný systém nemůže poskytnout RPO s hodnotou 0 a RTO nula. Navíc neexistují žádné výhody latence zápisu na používání silné konzistence s více oblastmi zápisu, protože zápis do jakékoli oblasti musí být replikován a potvrzen do všech nakonfigurovaných oblastí v rámci účtu. Výsledkem je stejná latence zápisu jako jeden účet oblasti pro zápis.

## <a name="additional-reading"></a>Další materiály ke čtení

Další informace o konceptech konzistence najdete v následujících článcích:

- [Vysoké úrovně TLA + specifikace pro pět úrovní konzistence nabízených službou Azure Cosmos DB](https://github.com/Azure/azure-cosmos-tla)
- [Vysvětlení konzistence replikovaných dat prostřednictvím Doug Terryu (video)](https://www.youtube.com/watch?v=gluIh8zd26I)
- [Vysvětlení konzistence replikovaných dat vysvětlených prostřednictvím baseballu (dokument White Paper) Doug Terry](https://www.microsoft.com/research/publication/replicated-data-consistency-explained-through-baseball/)
- [Záruka na relaci pro slabě konzistentní replikovaná data](https://dl.acm.org/citation.cfm?id=383631)
- [Kompromisy konzistence v moderních systémech Distributed Database System design: CAP je jenom součástí tohoto scénáře.](https://www.computer.org/csdl/magazine/co/2012/02/mco2012020037/13rRUxjyX7k)
- [Pravděpodobnostníá neaktuálnost (PBS) pro praktické částečné kvora](https://vldb.org/pvldb/vol5/p776_peterbailis_vldb2012.pdf)
- [Nakonec konzistentní – znovu navštěvované](https://www.allthingsdistributed.com/2008/12/eventually_consistent.html)

## <a name="next-steps"></a>Další kroky

Další informace o úrovních konzistence v Azure Cosmos DB najdete v následujících článcích:

- [Konfigurace výchozí úrovně konzistence](how-to-manage-consistency.md#configure-the-default-consistency-level)
- [Přepsání výchozí úrovně konzistence](how-to-manage-consistency.md#override-the-default-consistency-level)
- [Azure Cosmos DB SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_3/)
