---
title: Vysoká dostupnost v Azure Cosmos DB
description: Tento článek popisuje, jak Azure Cosmos DB poskytuje vysokou dostupnost
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/06/2019
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: 2afeae937d56a84c39167ad55a57c86f2623e52d
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81382706"
---
# <a name="high-availability-with-azure-cosmos-db"></a>Zajištění vysoké dostupnosti pomocí Azure Cosmos DB

Azure Cosmos DB transparentně replikuje vaše data ve všech oblastech Azure přidružených k vašemu účtu Cosmos. Cosmos DB využívá více vrstev redundance pro vaše data, jak je znázorněno na následujícím obrázku:

![Fyzické dělení](./media/high-availability/cosmosdb-data-redundancy.png)

- Data v kontejnerech Cosmos je [vodorovně rozdělena na oddíly](partitioning-overview.md).

- V rámci každé oblasti je každý oddíl chráněn sadou replik se všemi zápisy replikovaných a trvale potvrzených většinou replik. Repliky jsou distribuovány v až 10-20 domén selhání.

- Každý oddíl ve všech oblastech je replikován. Každá oblast obsahuje všechny datové oddíly kontejneru Cosmos a může přijímat zápisy a obsluhovat čtení.  

Pokud je váš účet Cosmos distribuován v oblastech *N* Azure, bude existovat alespoň *N* x 4 kopie všech vašich dat. Kromě poskytování přístupu k datům s nízkou latencí a škálování propustnosti zápisu a čtení v oblastech přidružených k vašemu účtu Cosmos, další oblasti (vyšší *N)* dále zlepšuje dostupnost.  

## <a name="slas-for-availability"></a>SLA pro dostupnost

Jako globálně distribuovaná databáze cosmos DB poskytuje komplexní smlouvy SLA, které zahrnují propustnost, latenci na 99 percentilu, konzistenci a vysokou dostupnost. V následující tabulce jsou uvedeny záruky vysoké dostupnosti poskytované Cosmos DB pro účty s jednou a více oblastmi. Pro vysokou dostupnost vždy nakonfigurujte účty Cosmos tak, aby měly více oblastí zápisu.

|Typ operace  | Jeden region |Více oblastí (zápisy v jedné oblasti)|Více oblastí (zápisy ve více oblastech) |
|---------|---------|---------|-------|
|Píše    | 99,99    |99,99   |99,999|
|Čte     | 99,99    |99,999  |99,999|

> [!NOTE]
> V praxi je skutečná dostupnost zápisu pro ohraničenou neaktuálnost, relaci, konzistentní předponu a případné modely konzistence výrazně vyšší než publikované sla. Skutečná dostupnost čtení pro všechny úrovně konzistence je výrazně vyšší než publikované sla.

## <a name="high-availability-with-cosmos-db-in-the-event-of-regional-outages"></a>Vysoká dostupnost s využitím služby Cosmos DB v případě oblastních výpadků

Oblastní výpadky nejsou nijak neobvyklé a služba Azure Cosmos DB zajišťuje, aby vaše databáze byla vždy vysoce dostupná. Následující podrobnosti zachycují chování Cosmos DB během výpadku v závislosti na konfiguraci účtu Cosmos:

- S Cosmos DB, před operace zápisu je potvrzena klientovi, data je trvale potvrzena kvora replik v rámci oblasti, která přijímá operace zápisu.

- Účty s více oblastmi nakonfigurované s oblastmi s více zápisy budou vysoce dostupné pro zápisy i čtení. Místní převzetí služeb při selhání jsou okamžitá a nevyžadují žádné změny z aplikace.

- Účty s jedním regionem mohou po regionálním výpadku ztratit dostupnost. Vždy se doporučuje nastavit **alespoň dvě oblasti** (nejlépe alespoň dvě oblasti zápisu) s vaším účtem Cosmos, abyste zajistili vysokou dostupnost za všech okolností.

### <a name="multi-region-accounts-with-a-single-write-region-write-region-outage"></a>Účty s více oblastmi s oblastí s jedním zápisem (výpadek oblasti zápisu)

- Během výpadku oblasti zápisu bude účet Cosmos automaticky podporovat sekundární oblast jako novou primární oblast zápisu, když je nakonfigurované **automatické převzetí služeb při selhání** na účtu Azure Cosmos. Pokud je tato možnost povolena, dojde k převzetí služeb při selhání do jiné oblasti v pořadí podle priority oblasti, kterou jste zadali.
- Pokud dříve ovlivněné oblasti je zpět do režimu online, všechna data zápisu, která nebyla replikována v případě selhání oblasti, je k dispozici prostřednictvím [informačního kanálu konflikty](how-to-manage-conflicts.md#read-from-conflict-feed). Aplikace můžete číst konflikty kanálu, vyřešit konflikty na základě logiky specifické pro aplikaci a zapsat aktualizovaná data zpět do kontejneru Azure Cosmos podle potřeby.
- Jakmile se dříve ovlivněná oblast zápisu obnoví, bude automaticky k dispozici jako oblast pro čtení. Můžete přepnout zpět do obnovené oblasti jako oblast zápisu. Oblasti můžete přepínat pomocí [PowerShellu, Azure CLI nebo portálu Azure](how-to-manage-database-account.md#manual-failover). Neexistuje **žádná ztráta dat nebo dostupnosti** před, během nebo po přepnutí oblasti zápisu a vaše aplikace je i nadále vysoce dostupná.

> [!IMPORTANT]
> Důrazně doporučujeme nakonfigurovat účty Azure Cosmos používané pro produkční úlohy tak, aby **umožňovaly automatické převzetí služeb při selhání**. Ruční převzetí služeb při selhání vyžaduje připojení mezi sekundární a primární oblasti zápisu k dokončení kontroly konzistence, aby se zajistilo, že během převzetí služeb při selhání nedojde ke ztrátě dat. Pokud primární oblast není k dispozici, tato kontrola konzistence nelze dokončit a ruční převzetí služeb při selhání nebude úspěšné, což vede ke ztrátě dostupnosti zápisu.

### <a name="multi-region-accounts-with-a-single-write-region-read-region-outage"></a>Účty s více oblastmi s oblastí s jedním zápisem (výpadek oblasti čtení)

- Během výpadku oblasti čtení Cosmos účty pomocí jakékoli úrovně konzistence nebo silné konzistence se třemi nebo více oblastí čtení zůstane vysoce dostupné pro čtení a zápisy.
- Ovlivněná oblast se automaticky odpojí a bude označena offline. [Sady Azure Cosmos DB SDK](sql-api-sdk-dotnet.md) přesměrují čtení volání do další dostupné oblasti v seznamu upřednostňovaných oblastí.
- Pokud není žádná oblast ze seznamu upřednostňovaných oblastí k dispozici, volání se automaticky vrátí na aktuální oblast zápisu.
- V kódu aplikace nejsou vyžadovány žádné změny pro zpracování výpadku oblasti čtení. Když je ovlivněná oblast čtení opět online, automaticky se synchronizuje s aktuální oblastí zápisu a bude opět k dispozici pro zpracování požadavků na čtení.
- Následná čtení se přesměrují na zotavenou oblast bez toho, aby se musel nějak změnit kód aplikace. Během převzetí služeb při selhání a opětovné připojení dříve selhání oblasti, čtení konzistence záruky nadále dodrženy Cosmos DB.

> [!IMPORTANT]
> Účty Azure Cosmos pomocí silné konzistence se dvěma nebo méně oblastmi čtení ztratí dostupnost zápisu během výpadku oblasti čtení, ale bude udržovat dostupnost čtení pro zbývající oblasti.

- I ve vzácné a nešťastné události, když oblast Azure je trvale neopravitelné, dojde ke ztrátě dat, pokud je váš účet Cosmos s více oblastmi nakonfigurovaný s *konzistencí Strong.* V případě trvale neopravitelné oblasti zápisu, víceoblastového účtu Cosmos nakonfigurovaného s konzistencí ohraničené neaktuárnosti, je okno potenciální ztráty dat omezeno na okno neaktuálnosti (*K* nebo *T),* kde se aktualizuje K= 100 000 a T = 5 minut. Pro relace, konzistentní předpona a případné konzistence úrovně potenciální ztráty dat okno je omezena na maximálně 15 minut. Další informace o cílech RTO a RPO pro Azure Cosmos DB najdete v [tématu Úrovně konzistence a odolnost dat](consistency-levels-tradeoffs.md#rto)

## <a name="availability-zone-support"></a>Podpora zóny dostupnosti

Kromě odolnosti proti chybám mezi oblastmi teď můžete povolit **redundanci zóny** při výběru oblasti, kterou chcete přidružit k databázi Azure Cosmos.

Díky podpoře zóny dostupnosti Azure Cosmos DB zajistí, že repliky jsou umístěny ve více zónách v rámci dané oblasti, aby byla zajištěna vysoká dostupnost a odolnost při selhání zón. V této konfiguraci nejsou žádné změny latence a dalších sla. V případě selhání jedné zóny redundantace zóny poskytuje úplnou životnost dat s RPO = 0 a dostupnost s RTO = 0.

Redundance zóny je *doplňkovou funkcí* [funkce replikace s více servery.](how-to-multi-master.md) Samotné redundanci zóny se nelze spoléhat na dosažení regionální odolnosti. Například v případě regionálních výpadků nebo přístupu s nízkou latencí v rámci oblastí se doporučuje mít kromě redundance zóny více oblastí zápisu.

Při konfiguraci zápisů ve více oblastech pro váš účet Azure Cosmos se můžete přihlásit k redundanci zóny bez dalších nákladů. V opačném případě se podívejte na níže uvedenou poznámku týkající se cen za podporu redundance zóny. Redundanci zóny můžete povolit v existující oblasti vašeho účtu Azure Cosmos odebráním oblasti a jeho přidáním zpět s povolenou redundancí zóny.

Tato funkce je dostupná v následujících oblastech Azure:

- Spojené království – jih

- Jihovýchodní Asie

- USA – východ

- USA – východ 2

- USA – střed

- Západní Evropa

- USA – západ 2

> [!NOTE]
> Povolení zón dostupnosti pro jeden účet Azure Cosmos v jedné oblasti bude mít za následek poplatky, které jsou rovnocenné přidání další oblasti do vašeho účtu. Podrobnosti o cenách najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/cosmos-db/) a náklady na více oblastí v článcích [Azure Cosmos DB.](optimize-cost-regions.md)

Následující tabulka shrnuje vysokou dostupnost různých konfigurací účtů:

|Klíčové ukazatele výkonu  |Jedna oblast bez zón dostupnosti (mimo AZ)  |Jedna oblast se zónami dostupnosti (AZ)  |Zápisy ve více oblastech se zónami dostupnosti (AZ, 2 oblasti) – nejdoporučované nastavení |
|---------|---------|---------|---------|
|Zapsat dostupnost SLA | 99,99 % | 99,99 % | 99,999 % |
|Číst dostupnost SLA  | 99,99 % | 99,99 % | 99,999 % |
|Price | Sazba za účtování v jedné oblasti | Fakturační sazba zóny dostupnosti v jedné oblasti | Fakturační sazba pro více oblastí |
|Selhání zóny – ztráta dat | Ztráta dat | Žádná ztráta dat | Žádná ztráta dat |
|Selhání zóny – dostupnost | Ztráta dostupnosti | Žádná ztráta dostupnosti | Žádná ztráta dostupnosti |
|Latence čtení | Průřez | Průřez | Nízká |
|Latence zápisu | Průřez | Průřez | Nízká |
|Regionální výpadek – ztráta dat | Ztráta dat |  Ztráta dat | Ztráta dat <br/><br/> Při použití konzistence ohraničené neaktuálnosti s více hlavním serverem a více než jednou oblastí je ztráta dat omezena na ohraničenou neaktuálnost nakonfigurovanou ve vašem účtu. <br /><br />Ztrátě dat během regionálního výpadku se můžete vyhnout konfigurací silné konzistence s více oblastmi. Tato možnost přichází s kompromisy, které ovlivňují dostupnost a výkon. Lze jej nakonfigurovat pouze u účtů, které jsou konfigurovány pro zápisy v jedné oblasti. |
|Regionální výpadek – dostupnost | Ztráta dostupnosti | Ztráta dostupnosti | Žádná ztráta dostupnosti |
|Propustnost | X Zřízená propustnost RU/s | X Zřízená propustnost RU/s | 2X zřízená propustnost RU/s <br/><br/> Tento konfigurační režim vyžaduje dvojnásobnou propustnost ve srovnání s jednou oblastí se zónami dostupnosti, protože existují dvě oblasti. |

> [!NOTE]
> Chcete-li povolit podporu zóny dostupnosti pro víceoblastí účtu Azure Cosmos, musí mít účet povolen zápisy s více hlavními servery.

Redundanci zóny můžete povolit při přidávání oblasti do nových nebo existujících účtů Azure Cosmos. Chcete-li povolit redundanci zóny na vašem `isZoneRedundant` účtu `true` Azure Cosmos, měli byste nastavit příznak pro konkrétní umístění. Tento příznak můžete nastavit v rámci vlastnosti locations. Například následující fragment prostředí PowerShell umožňuje redundanci zóny pro oblast Jihovýchodní Asie:

```powershell
$locations = @(
    @{ "locationName"="Southeast Asia"; "failoverPriority"=0; "isZoneRedundant"= "true" },
    @{ "locationName"="East US"; "failoverPriority"=1; "isZoneRedundant"= "true" }
)
```

Následující příkaz ukazuje, jak povolit redundanci zóny pro oblasti "EastUS" a "WestUS2":

```azurecli-interactive
az cosmosdb create \
  --name mycosmosdbaccount \
  --resource-group myResourceGroup \
  --kind GlobalDocumentDB \
  --default-consistency-level Session \
  --locations regionName=EastUS failoverPriority=0 isZoneRedundant=True \
  --locations regionName=WestUS2 failoverPriority=1 isZoneRedundant=True
```

Zóny dostupnosti můžete povolit pomocí portálu Azure při vytváření účtu Azure Cosmos. Při vytváření účtu nezapomeňte povolit **geografickou redundanci**, **zápisy ve více oblastech**a zvolte oblast, kde jsou podporovány zóny dostupnosti:

![Povolení zón dostupnosti pomocí portálu Azure](./media/high-availability/enable-availability-zones-using-portal.png) 

## <a name="building-highly-available-applications"></a>Vytváření vysoce dostupných aplikací

- Chcete-li zajistit vysokou dostupnost zápisu a čtení, nakonfigurujte svůj účet Cosmos tak, aby se rozprostíral alespoň ve dvou oblastech s oblastmi s více zápisy. Tato konfigurace bude poskytovat nejvyšší dostupnost, nejnižší latence a nejlepší škálovatelnost pro čtení i zápisy podporované sla. Další informace najdete v tématu [konfigurace účtu Cosmos s více oblastmi zápisu](tutorial-global-distribution-sql-api.md).

- Pro účty Cosmos s více oblastmi, které jsou nakonfigurované s oblastí s jedním zápisem, [povolte automatické převzetí služeb při selhání pomocí Azure CLI nebo portálu Azure](how-to-manage-database-account.md#automatic-failover). Po povolení automatického převzetí služeb při selhání, kdykoli dojde k regionální havárii, Cosmos DB automaticky převezme převzetí služeb při selhání vašeho účtu.  

- I v případě, že váš účet Azure Cosmos je vysoce dostupné, vaše aplikace nemusí být správně navržentak, aby zůstali vysoce dostupné. Chcete-li otestovat vysokou dostupnost aplikace od konce, v rámci testovacího testování aplikací nebo zotavení po havárii (DR) dočasně zakažte automatické převzetí služeb při selhání pro účet, vyvoláte [ruční převzetí služeb při selhání pomocí prostředí PowerShell, Azure CLI nebo portálazure ,](how-to-manage-database-account.md#manual-failover)pak sledujte převzetí služeb při selhání vaší aplikace. Po dokončení můžete znovu přejít na službu po obnovení do primární oblasti a obnovit automatické převzetí služeb při selhání pro účet.

- V rámci globálně distribuované databázové prostředí existuje přímý vztah mezi úroveň konzistence a odolnost dat v přítomnosti výpadku celé oblasti. Při vývoji plánu kontinuity provozu je třeba pochopit maximální přijatelnou dobu, než se aplikace plně zotaví po rušivé události. Doba potřebná k úplnému obnovení aplikace se označuje jako cíl doby obnovení (RTO). Musíte také pochopit maximální dobu nedávných aktualizací dat, kterou aplikace může tolerovat ztrátu při obnovení po rušivé události. Časový interval aktualizací, které si můžete dovolit ztratit, se označuje jako cíl bodu obnovení (RPO). Informace o rpo a rto pro Azure Cosmos DB najdete v [tématu Úrovně konzistence a odolnost dat](consistency-levels-tradeoffs.md#rto)

## <a name="next-steps"></a>Další kroky

Dále si můžete přečíst následující články:

- [Kompromisy dostupnosti a výkonu pro různé úrovně konzistence](consistency-levels-tradeoffs.md)
- [Zřízená propustnost globálního škálování](scaling-throughput.md)
- [Globální distribuce – pod pokličkou](global-dist-under-the-hood.md)
- [Úrovně konzistence v Azure Cosmos DB](consistency-levels.md)
- [Jak nakonfigurovat účet Cosmos s více oblastmi zápisu](how-to-multi-master.md)
