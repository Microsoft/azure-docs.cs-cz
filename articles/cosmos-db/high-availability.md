---
title: Vysoká dostupnost v Azure Cosmos DB
description: Tento článek popisuje, jak Azure Cosmos DB poskytuje vysokou dostupnost.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/13/2020
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: 2fb8b24d5d44ced8f9e363008354acf5bc2fde40
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93081871"
---
# <a name="how-does-azure-cosmos-db-provide-high-availability"></a>Jak Azure Cosmos DB poskytovat vysokou dostupnost
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB poskytuje vysokou dostupnost dvěma hlavním způsobem. Nejprve Azure Cosmos DB replikuje data mezi oblastmi konfigurovanými v rámci účtu Cosmos. Druhý Azure Cosmos DB udržuje 4 repliky dat v rámci oblasti.

Azure Cosmos DB je globálně distribuovaná databázová služba a je základní službou v Azure. Ve výchozím nastavení je dostupná ve [všech oblastech, kde je Azure k dispozici](https://azure.microsoft.com/global-infrastructure/services/?products=cosmos-db&regions=all). K vašemu účtu Azure Cosmos můžete přidružit libovolný počet oblastí Azure a vaše data se automaticky a transparentně replikují. Můžete kdykoli přidat nebo odebrat oblast pro účet Azure Cosmos. Cosmos DB je k dispozici ve všech pěti různých cloudových prostředích Azure dostupných zákazníkům:

* **Veřejný cloud Azure** , který je k dispozici globálně.

* **Azure Čína 21Vianet** je k dispozici prostřednictvím jedinečného partnerství mezi společnostmi Microsoft a 21Vianet, jedním z největších poskytovatelů internetových služeb v Číně.

* **Azure Německo** poskytuje služby v rámci modelu důvěryhodného data, který zajišťuje, že zákaznická data zůstanou v Německu pod kontrolou T-Systems International GmbH, dceřinou společností pro německé Telekom, která funguje jako důvěryhodný datový zplnomocněnec.

* **Azure Government** je k dispozici ve čtyřech oblastech v USA státním úřadům USA a jejich partnerům.

* **Azure Government pro Ministerstvo obrany (DOD)** je k dispozici ve dvou oblastech v USA do ministerstva obrany USA.

V rámci jedné oblasti Azure Cosmos DB udržuje čtyři kopie vašich dat jako repliky v rámci fyzických oddílů, jak je znázorněno na následujícím obrázku:

:::image type="content" source="./media/high-availability/cosmosdb-data-redundancy.png" alt-text="Fyzické dělení" border="false":::

* Data v kontejnerech Azure Cosmos jsou [horizontálně rozdělená na oddíly](partitioning-overview.md).

* Sada oddílů je kolekcí více sad replik. V rámci každé oblasti je každý oddíl chráněný sadou replik, která má všechny replikované zápisy a trvale potvrzené většinou replik. Repliky jsou distribuované napříč až 10-20 domén selhání.

* Všechny oddíly napříč všemi oblastmi se replikují. Každá oblast obsahuje všechny datové oddíly kontejneru Azure Cosmos a může přijímat zápisy a poskytovat čtení.  

Pokud je váš účet Azure Cosmos distribuován mezi *n* oblastí Azure, bude k dispozici alespoň *n* x 4 kopií všech vašich dat. Pokud účet Azure Cosmos ve více než 2 oblastech vylepšuje dostupnost vaší aplikace a poskytuje nízkou latenci napříč přidruženými oblastmi.

## <a name="slas-for-availability"></a>SLA pro dostupnost

Jako globálně distribuovaná databáze Azure Cosmos DB poskytuje komplexní SLA zahrnující propustnost, latenci 99 percentilu, konzistenci a vysokou dostupnost. Následující tabulka uvádí záruky vysoké dostupnosti poskytované Azure Cosmos DB pro účty s jednou a více oblastmi. Pro zajištění vysoké dostupnosti vždy nakonfigurujte účty Azure Cosmos tak, aby měly více oblastí pro zápis.

|Typ operace  | Jedna oblast |Více oblastí (zápisy v jedné oblasti)|Více oblastí (zápisy ve více oblastech) |
|---------|---------|---------|-------|
|Zápisy    | 99,99    |99,99   |99,999|
|Čtení     | 99,99    |99,999  |99,999|

> [!NOTE]
> V praxi je skutečná dostupnost zápisu pro ohraničenou neaktuálnost, relaci, konzistentní předponu a konečné modely konzistence podstatně vyšší než u publikovaných SLA. Skutečná dostupnost čtení pro všechny úrovně konzistence je podstatně vyšší než u publikovaného SLA.

## <a name="high-availability-with-azure-cosmos-db-in-the-event-of-regional-outages"></a>Vysoká dostupnost s Azure Cosmos DB v případě regionálních výpadků

Ve výjimečných případech regionálního výpadku Azure Cosmos DB zajišťuje, že je databáze vždy vysoce dostupná. Následující podrobnosti zachytí Azure Cosmos DB chování během výpadku v závislosti na konfiguraci účtu Azure Cosmos:

* V případě Azure Cosmos DB před potvrzením operace zápisu klientovi je data trvale potvrzená kvorem replik v rámci oblasti, která přijímá operace zápisu. Další podrobnosti najdete v tématu [úrovně a propustnosti konzistence](./consistency-levels.md#consistency-levels-and-throughput) .

* Účty s více oblastmi nakonfigurované s oblastí s vícenásobným zápisem budou vysoce dostupné pro zápis i čtení. Místní převzetí služeb při selhání se zjistilo a zpracovává se v klientovi Azure Cosmos DB. Jsou také okamžité a nevyžadují žádné změny z aplikace.

* Účty v jedné oblasti mohou ztratit dostupnost po oblastním výpadku. Vždycky se doporučuje nastavit **aspoň dvě oblasti** (nejlépe dvě oblasti zápisu) s vaším účtem Azure Cosmos, abyste zajistili vysokou dostupnost ve všech časech.

### <a name="multi-region-accounts-with-a-single-write-region-write-region-outage"></a>Účty s více oblastmi s oblastí s jedním zápisem (při výpadku oblasti zápisu)

* Při výpadku oblasti zápisu bude účet Azure Cosmos automaticky propagovat sekundární oblast jako novou primární oblast pro zápis, když je v účtu Azure Cosmos nakonfigurovaná **možnost povolit automatické převzetí služeb při selhání** . Pokud je tato možnost povolená, dojde k převzetí služeb při selhání do jiné oblasti v pořadí podle priority oblasti, kterou jste zadali.

* Když je dříve ovlivněná oblast zpět online, všechna data zápisu, která nebyla replikována v případě selhání oblasti, jsou zpřístupněna prostřednictvím [kanálu konfliktů](how-to-manage-conflicts.md#read-from-conflict-feed). Aplikace mohou číst kanál konfliktů, řešit konflikty na základě logiky specifické pro aplikaci a podle potřeby zapisovat aktualizovaná data zpátky do kontejneru Azure Cosmos.

* Jakmile se výše ovlivněné oblasti zápisu napřed mění, bude automaticky dostupná jako oblast pro čtení. Můžete přejít zpátky do obnovené oblasti jako oblast zápisu. Oblasti můžete přepínat pomocí [PowerShellu, rozhraní příkazového řádku Azure nebo Azure Portal](how-to-manage-database-account.md#manual-failover). Neexistují **žádná data ani ztráta dostupnosti** před, během nebo po přepnutí oblasti zápisu a aplikace bude dál vysoce dostupná.

> [!IMPORTANT]
> Důrazně doporučujeme, abyste nakonfigurovali účty Azure Cosmos používané pro produkční úlohy, aby se **povolilo automatické převzetí služeb při selhání** . Ruční převzetí služeb při selhání vyžaduje připojení mezi sekundární a primární oblastí pro zápis k dokončení kontroly konzistence, aby při převzetí služeb při selhání nedošlo ke ztrátě dat. Pokud není primární oblast k dispozici, tato kontrola konzistence nemůže být dokončena a ruční převzetí služeb při selhání nebude úspěšné, což vede ke ztrátě dostupnosti zápisu po dobu trvání regionu v oblasti výpadku.

### <a name="multi-region-accounts-with-a-single-write-region-read-region-outage"></a>Účty s více oblastmi s jednou oblastí zápisu (nevýpadek oblasti čtení)

* Při výpadku oblasti čtení budou účty Azure Cosmos s využitím jakékoli úrovně konzistence nebo silné konzistence se třemi nebo více oblastmi pro čtení pořád vysoce dostupné pro čtení a zápisy.

* Účty Azure Cosmos s využitím silné konzistence se dvěma nebo méně oblastmi pro čtení (které zahrnují oblast pro čtení & zápisu) ztratí během výpadku oblasti čtení oprávnění ke čtení zápisu.

* Ovlivněná oblast je automaticky odpojena a bude označena jako offline. Sady [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md) přesměrují volání čtení do další dostupné oblasti v seznamu upřednostňovaných oblastí.

* Pokud není žádná oblast ze seznamu upřednostňovaných oblastí k dispozici, volání se automaticky vrátí na aktuální oblast zápisu.

* V kódu aplikace se nevyžadují žádné změny pro zpracování výpadku oblasti čtení. Když je oblast s ovlivněným čtením zpět online, bude automaticky synchronizována s aktuální oblastí pro zápis a bude opět k dispozici pro poskytování požadavků na čtení.

* Následná čtení se přesměrují na zotavenou oblast bez toho, aby se musel nějak změnit kód aplikace. V průběhu převzetí služeb při selhání a opětovném připojení k dříve neúspěšnému výskytu se Přečtěte záruky konzistence, které Azure Cosmos DB.

* I ve výjimečné a unfortunate události, když je oblast Azure trvale nezotavitelné, nedochází k žádné ztrátě dat, pokud je váš účet Azure Cosmos ve více oblastech nakonfigurovaný se *silnými* konzistencí. V případě trvale nezotavitelné oblasti zápisu se jedná o účet Azure Cosmos ve více oblastech nakonfigurovaný s kostarou konzistencí, což je potenciální okno ztráty dat omezené na okno zastaralosti ( *k* nebo *T* ), kde k = 100 000 aktualizací a t = 5 minut. V případě relace, konzistentní a konečné úrovně konzistence je možné okno ztráty dat omezit na maximálně 15 minut. Další informace o cílech RTO a RPO pro Azure Cosmos DB najdete v tématu [úrovně konzistence a odolnost dat](./consistency-levels.md#rto) .

## <a name="availability-zone-support"></a>Podpora zón dostupnosti

Kromě odolnosti mezi oblastmi teď můžete při výběru oblasti, která má být přidružena k databázi Azure Cosmos, povolit **redundanci zóny** .

Díky podpoře zón dostupnosti Azure Cosmos DB zajistí, aby se repliky v rámci dané oblasti umístily do několika zón, aby poskytovaly vysokou dostupnost a odolnost během selhání oblastí. V této konfiguraci nejsou žádné změny latence a jiné SLA. V případě selhání jedné zóny poskytuje redundance zóny plnou odolnost dat s cílem RPO = 0 a dostupnost s RTO = 0.

Redundance zóny je *doplňková schopnost* [replikace v rámci funkce zápisu ve více oblastech](how-to-multi-master.md) . Na redundanci zóny se nedá spoléhat, aby bylo možné dosáhnout místní odolnosti. Například v případě místního výpadku nebo přístupu s nízkou latencí v rámci oblastí doporučujeme mít kromě redundance zóny více oblastí pro zápis.

Když konfigurujete zápisy ve více oblastech pro účet Azure Cosmos, můžete se odhlásit k redundanci zóny bez dalších poplatků. V opačném případě se podívejte na následující poznámku týkající se cen pro podporu redundance zóny. Redundanci zóny můžete povolit ve stávající oblasti svého účtu Azure Cosmos tak, že odstraníte oblast a přidáte ji zpátky s povolenou redundancí zóny.

Tato funkce je dostupná v těchto oblastech: *Velká Británie – jih, jihovýchodní Asie, východní USA, východní USA 2, střed USA, západní Evropa, západní USA 2, Japonsko – východ, Severní Evropa, Francie – střed, Austrálie – východ, východní USA 2 oblasti EUAP* .

Následující tabulka shrnuje schopnost vysoké dostupnosti u různých konfigurací účtu:

|KPI  |Jedna oblast bez Zóny dostupnosti (ne AZ)  |Jedna oblast s Zóny dostupnostiem (AZ)  |Zápisy ve více oblastech pomocí Zóny dostupnosti (AZ, 2 Regions) – Doporučené nastavení |
|---------|---------|---------|---------|
|Smlouva SLA o dostupnosti pro zápis | 99,99 % | 99,99 % | 99,999 % |
|SLA dostupnosti pro čtení  | 99,99 % | 99,99 % | 99,999 % |
|Cena | Fakturační sazba jedné oblasti | Fakturační sazba zóny dostupnosti v jedné oblasti | Fakturační sazba pro více oblastí |
|Selhání zón – ztráta dat | Ztráta dat | Žádná ztráta dat | Žádná ztráta dat |
|Selhání zón – dostupnost | Ztráta dostupnosti | Žádná ztráta dostupnosti | Žádná ztráta dostupnosti |
|Latence čtení | Mezi oblastmi | Mezi oblastmi | Nízká |
|Latence zápisu | Mezi oblastmi | Mezi oblastmi | Nízká |
|Oblastní výpadek – ztráta dat | Ztráta dat |  Ztráta dat | Ztráta dat <br/><br/> Při použití ohraničené konzistence neaktuálnosti s více oblastmi zápisu a více než jednou oblastí je ztráta dat omezená na ohraničenou neomezenou funkčnost nakonfigurovanou na vašem účtu. <br /><br />Nemůžete zabránit ztrátě dat při regionálním výpadku tím, že nakonfigurujete silnou konzistenci s více oblastmi. Tato možnost je součástí kompromisů, které mají vliv na dostupnost a výkon. Dá se nakonfigurovat jenom pro účty, které jsou nakonfigurované pro zápisy v jedné oblasti. |
|Oblastní výpadek – dostupnost | Ztráta dostupnosti | Ztráta dostupnosti | Žádná ztráta dostupnosti |
|Propustnost | Zajištěná propustnost X RU/s | Zajištěná propustnost X RU/s × 1,25 | dostupná propustnost dvojnásobných RU/s <br/><br/> Tento režim konfigurace vyžaduje dvojnásobek objemu propustnosti v porovnání s jednou oblastí s Zóny dostupnosti, protože existují dvě oblasti. |

> [!NOTE]
> Aby bylo možné povolit podporu zón dostupnosti pro účet Azure Cosmos ve více oblastech, musí mít účet povolené zápisy ve více oblastech.

Pokud přidáte oblast do nových nebo existujících účtů Azure Cosmos, můžete povolit redundanci zóny. Pokud chcete povolit redundanci zóny na svém účtu Azure Cosmos, měli byste nastavit `isZoneRedundant` příznak na `true` pro konkrétní umístění. Tento příznak můžete nastavit ve vlastnosti umístění. Například následující fragment kódu prostředí PowerShell umožňuje redundanci zóny pro oblast jihovýchodní Asie:

Zóny dostupnosti lze povolit prostřednictvím:

* [Azure Portal](how-to-manage-database-account.md#addremove-regions-from-your-database-account)

* [Azure PowerShell](manage-with-powershell.md#create-account)

* [Azure CLI](manage-with-cli.md#add-or-remove-regions)

* [Šablony Azure Resource Manager](./manage-with-templates.md)

## <a name="building-highly-available-applications"></a>Vytváření vysoce dostupných aplikací

* V těchto událostech zkontrolujte očekávané [chování sady SDK Azure Cosmos](troubleshoot-sdk-availability.md) a které jsou konfigurace, které to ovlivňují.

* Abyste zajistili vysokou dostupnost zápisu a čtení, nakonfigurujte účet Azure Cosmos tak, aby zahrnoval alespoň dvě oblasti s více oblastmi zápisu. Tato konfigurace poskytuje nejvyšší dostupnost, nejnižší latenci a nejlepší škálovatelnost pro čtení i zápis zálohovaných službou SLA. Další informace najdete v tématu [Konfigurace účtu Azure Cosmos s více oblastmi zápisu](tutorial-global-distribution-sql-api.md).

* Pro účty Azure Cosmos s více oblastmi, které jsou nakonfigurované s jednou oblastí pro zápis, [umožňují automatické převzetí služeb při selhání pomocí Azure CLI nebo Azure Portal](how-to-manage-database-account.md#automatic-failover). Po povolení automatického převzetí služeb při selhání dojde k automatickému převzetí služeb při selhání Cosmos DB, když dojde k místní havárii.  

* I když je váš účet Azure Cosmos vysoce dostupný, vaše aplikace nemusí být správně navržená tak, aby zůstala vysoce dostupná. K otestování komplexní vysoké dostupnosti vaší aplikace, jako součást testování aplikací nebo zotavení po havárii (DR), můžete dočasně zakázat automatické převzetí služeb při selhání pro účet, vyvolat [ruční převzetí služeb při selhání pomocí PowerShellu, rozhraní příkazového řádku Azure nebo Azure Portal](how-to-manage-database-account.md#manual-failover)a potom monitorovat převzetí služeb při selhání vaší aplikace. Po dokončení můžete navrátit služby po obnovení do primární oblasti a obnovit automatické převzetí služeb při selhání pro tento účet.

* V rámci globálně distribuovaného databázového prostředí existuje přímý vztah mezi úrovní konzistence a odolností dat při výpadku v rámci oblasti. Při vývoji plánu provozní kontinuity musíte pochopit maximální přijatelnou dobu, než se aplikace kompletně obnoví po přerušení události. Čas potřebný k úplnému obnovení aplikace je známý jako cíl doby obnovení (RTO). Také je potřeba porozumět maximálnímu intervalu nedávných aktualizací dat, které může aplikace tolerovat při obnovování po přerušení události. Časový interval aktualizací, které si můžete dovolit ztratit, se označuje jako cíl bodu obnovení (RPO). Pokud chcete zobrazit RPO a RTO pro Azure Cosmos DB, přečtěte si část [úrovně konzistence a odolnost dat](./consistency-levels.md#rto) .

## <a name="next-steps"></a>Další kroky

Dále si můžete přečíst následující články:

* [Kompromisy týkající se dostupnosti a výkonu pro různé úrovně konzistence](./consistency-levels.md)

* [Zřízená propustnost globálního škálování](./request-units.md)

* [Globální distribuce – pod pokličkou](global-dist-under-the-hood.md)

* [Úrovně konzistence ve službě Azure Cosmos DB](consistency-levels.md)

* [Jak nakonfigurovat účet Cosmos s více oblastmi zápisu](how-to-multi-master.md)

* [Chování sady SDK v prostředích s více oblastmi](troubleshoot-sdk-availability.md)