---
title: Vysoká dostupnost v Azure Cosmos DB
description: Tento článek popisuje, jak Azure Cosmos DB poskytuje vysokou dostupnost.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/31/2019
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: 4b039e777748499e1b9a2a120e9498d94066b735
ms.sourcegitcommit: d585cdda2afcf729ed943cfd170b0b361e615fae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/31/2019
ms.locfileid: "68688288"
---
# <a name="high-availability-with-azure-cosmos-db"></a>Vysoká dostupnost s Azure Cosmos DB

Azure Cosmos DB transparentně replikuje vaše data ve všech oblastech Azure přidružených k vašemu účtu Cosmos. Cosmos DB využívá více vrstev redundance pro vaše data, jak je znázorněno na následujícím obrázku:

![Fyzické dělení](./media/high-availability/cosmosdb-data-redundancy.png)

- Data v kontejnerech Cosmos jsou [horizontálně rozdělená na oddíly](partitioning-overview.md).

- V rámci každé oblasti je každý oddíl chráněný sadou replik, která má všechny replikované zápisy a trvale potvrzené většinou replik. Repliky jsou distribuované napříč až 10-20 domén selhání.

- Všechny oddíly napříč všemi oblastmi se replikují. Každá oblast obsahuje všechny oddíly dat Cosmos kontejneru a může přijímat zápisy a poskytovat čtení.  

Pokud je váš účet Cosmos distribuován mezi *n* oblastí Azure, bude k dispozici alespoň *n* x 4 kopií všech vašich dat. Kromě poskytování přístupu k datům s nízkou latencí a škálování pro zápis/čtení napříč oblastmi přidruženými k vašemu účtu Cosmos mají více oblastí (vyšší než *N*) lepší dostupnost.  

## <a name="slas-for-availability"></a>SLA pro dostupnost

Jako globálně distribuovaná databáze Cosmos DB poskytuje komplexní SLA zahrnující propustnost, latenci 99 percentilu, konzistenci a vysokou dostupnost. Následující tabulka uvádí záruky vysoké dostupnosti poskytované Cosmos DB pro účty s jednou a více oblastmi. Pro zajištění vysoké dostupnosti vždy nakonfigurujte účty Cosmos tak, aby měly více oblastí pro zápis.

|Typ operace  | Jedna oblast |Více oblastí (zápisy v jedné oblasti)|Více oblastí (zápisy ve více oblastech) |
|---------|---------|---------|-------|
|Zápisy    | 99,99    |99,99   |99,999|
|Čtení     | 99,99    |99,999  |99,999|

> [!NOTE]
> V praxi je skutečná dostupnost zápisu pro ohraničenou neaktuálnost, relaci, konzistentní předponu a konečné modely konzistence podstatně vyšší než u publikovaných SLA. Skutečná dostupnost čtení pro všechny úrovně konzistence je podstatně vyšší než u publikovaného SLA.

## <a name="high-availability-with-cosmos-db-in-the-event-of-regional-outages"></a>Vysoká dostupnost s Cosmos DB v případě regionálních výpadků

Oblastní výpadky nejsou nijak neobvyklé a služba Azure Cosmos DB zajišťuje, aby vaše databáze byla vždy vysoce dostupná. Následující podrobnosti zachytí Cosmos DB chování během výpadku v závislosti na konfiguraci účtu Cosmos:

- V případě Cosmos DB před potvrzením operace zápisu klientovi je data trvale potvrzená kvorem replik v rámci oblasti, která přijímá operace zápisu.

- Účty s více oblastmi nakonfigurované s oblastí s vícenásobným zápisem budou vysoce dostupné pro zápis i čtení. Místní převzetí služeb při selhání je okamžité a nevyžaduje žádné změny aplikace.

- **Účty s více oblastmi s jednou oblastí zápisu (výpadek oblasti zápisu):** 
  * Během výpadku oblasti zápisu zůstanou tyto účty vysoce dostupné pro čtení. Aby požadavky na zápis byly úspěšné, musíte zapnout možnost **Povolit automatické převzetí služeb při selhání** v účtu Azure Cosmos. Povolením této možnosti dojde k převzetí služeb při selhání ovlivněné oblasti v jiné oblasti v pořadí podle priority oblasti. 
  * Když je dříve ovlivněná oblast zpět online, všechna data zápisu, která byla nereplikována v případě selhání oblasti, jsou zpřístupněna prostřednictvím [kanálu konfliktů](how-to-manage-conflicts.md#read-from-conflict-feed). Aplikace mohou číst kanál konfliktů, řešit konflikty na základě logiky specifické pro aplikaci a podle potřeby zapisovat aktualizovaná data zpátky do kontejneru Azure Cosmos. 
  * Jakmile se výše ovlivněné oblasti zápisu napřed mění, bude automaticky dostupná jako oblast pro čtení. Můžete přejít zpátky do obnovené oblasti jako oblast zápisu. Oblasti můžete přepínat pomocí rozhraní příkazového [řádku Azure CLI nebo Azure Portal](how-to-manage-database-account.md#manual-failover). Neexistují **žádná data ani ztráta dostupnosti** před, během nebo po přepnutí oblasti zápisu a aplikace bude dál vysoce dostupná. 

- **Účty s více oblastmi s jednou oblastí zápisu (výpadek při čtení oblasti):** 
  * Při výpadku oblasti čtení zůstanou tyto účty vysoce dostupné pro čtení a zápis. 
  * Ovlivněná oblast je automaticky odpojena od oblasti pro zápis a bude označena jako offline. Sady [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md) přesměrují volání čtení do další dostupné oblasti v seznamu upřednostňovaných oblastí. 
  * Pokud není k dispozici žádná z oblastí v seznamu upřednostňovaných oblastí, volání se automaticky vrátí do aktuální oblasti pro zápis. 
  * V kódu aplikace se nevyžadují žádné změny pro zpracování výpadku oblasti čtení. Když je však ovlivněná oblast zpět online, dříve ovlivněná oblast čtení se automaticky synchronizuje s aktuální oblastí pro zápis a bude opět k dispozici, aby mohla zpracovávat požadavky na čtení. 
  * Následná čtení jsou přesměrována do obnovené oblasti bez nutnosti jakýchkoli změn kódu aplikace. V průběhu převzetí služeb při selhání a opětovném připojení k dříve neúspěšnému výskytu se Přečtěte záruky konzistence, které Cosmos DB.

- Účty v jedné oblasti mohou ztratit dostupnost po oblastním výpadku. Vždycky se doporučuje nastavit **aspoň dvě oblasti** (nejlépe dvě oblasti zápisu) s vaším účtem Cosmos, abyste zajistili vysokou dostupnost ve všech časech.

- I ve výjimečné a unfortunate události, když je oblast Azure trvale nezotavitelné, nedochází k žádné ztrátě dat, pokud je váš účet Cosmos ve více oblastech nakonfigurovaný s výchozí úrovní konzistence *Strong*. V případě trvale nezotavitelné oblasti zápisu pro účty Cosmos s více oblastmi nakonfigurovanou s konzistencí s ohraničenou kočárkou je možné časové období ztráty dat omezit na okno zastaralosti (*K* nebo *T*); v případě relace, konzistentní a konečné úrovně konzistence je možné okno ztráty dat omezit na maximálně pět sekund. 

## <a name="availability-zone-support"></a>Podpora zón dostupnosti

Azure Cosmos DB je globálně distribuovaná databázová služba s více hlavními servery, která poskytuje vysokou dostupnost a odolnost během regionálních výpadků. Kromě odolnosti mezi oblastmi teď můžete při výběru oblasti, která má být přidružena k databázi Azure Cosmos, povolit **redundanci zóny** . 

Díky podpoře zón dostupnosti Azure Cosmos DB zajistí, aby se repliky v rámci dané oblasti umístily do několika zón, aby poskytovaly vysokou dostupnost a odolnost během selhání oblastí. V této konfiguraci nejsou žádné změny latence a jiné SLA. V případě selhání jedné zóny poskytuje redundance zóny plnou odolnost dat s cílem RPO = 0 a dostupnost s RTO = 0. 

Redundance zóny je *doplňkovou* funkcí pro replikaci s [více hlavními servery](how-to-multi-master.md) . Na redundanci zóny se nedá spoléhat, aby bylo možné dosáhnout místní odolnosti. Například v případě místního výpadku nebo přístupu s nízkou latencí v rámci oblastí doporučujeme mít kromě redundance zóny více oblastí pro zápis. 

Když konfigurujete zápisy ve více oblastech pro účet Azure Cosmos, můžete se odhlásit k redundanci zóny bez dalších poplatků. V opačném případě se podívejte na následující poznámku týkající se cen pro podporu redundance zóny. Redundanci zóny můžete povolit ve stávající oblasti svého účtu Azure Cosmos tak, že odstraníte oblast a přidáte ji zpátky s povolenou redundancí zóny.

Tato funkce je k dispozici v následujících oblastech Azure:

* Velká Británie – jih
* Jihovýchodní Asie 
* East US
* Východní USA 2 
* Střední USA
* Západní Evropa
* USA – západ 2

> [!NOTE] 
> Povolení Zóny dostupnosti pro jednu oblast účtu Azure Cosmos bude mít za následek poplatky, které se rovnají přidání další oblasti do svého účtu. Podrobnosti o cenách najdete na stránce s [cenami](https://azure.microsoft.com/pricing/details/cosmos-db/) a s [náklady na více oblastí v Azure Cosmos DB](optimize-cost-regions.md) článcích. 

Následující tabulka shrnuje schopnost vysoké dostupnosti u různých konfigurací účtu: 

|KLÍČOVÝ UKAZATEL VÝKONU  |Jedna oblast bez Zóny dostupnosti (ne AZ)  |Jedna oblast s Zóny dostupnostiem (AZ)  |Zápisy ve více oblastech pomocí Zóny dostupnosti (AZ, 2 Regions) – Doporučené nastavení |
|---------|---------|---------|---------|
|Smlouva SLA o dostupnosti pro zápis     |   99,99 %      |    99,99 %     |  99.999%  |
|SLA dostupnosti pro čtení   |   99,99 %      |   99,99 %      |  99.999%       |
|Cena  |  Fakturační sazba jedné oblasti |  Fakturační sazba zóny dostupnosti v jedné oblasti |  Fakturační sazba pro více oblastí       |
|Selhání zón – ztráta dat   |  Ztráta dat  |   Žádná ztráta dat |   Žádná ztráta dat  |
|Selhání zón – dostupnost |  Ztráta dostupnosti  | Žádná ztráta dostupnosti  |  Žádná ztráta dostupnosti  |
|Latence čtení    |  Mezi oblastmi    |   Mezi oblastmi   |    Nízká  |
|Latence zápisu    |   Mezi oblastmi   |  Mezi oblastmi    |   Nízká   |
|Oblastní výpadek – ztráta dat    |   Ztráta dat      |  Ztráta dat       |   Ztráta dat <br/><br/> Při použití ohraničené konzistence neaktuálnosti s více hlavními a více než jednou oblastí je ztráta dat omezená na ohraničenou neomezenou funkčnost nakonfigurovanou na vašem účtu. <br/><br/> Ztrátě dat během místního výpadku je možné vyhnout konfigurací silné konzistence s více oblastmi. Tato možnost se týká kompromisů, které mají vliv na dostupnost a výkon.      |
|Oblastní výpadek – dostupnost  |  Ztráta dostupnosti       |  Ztráta dostupnosti       |  Žádná ztráta dostupnosti  |
|Propustnost    |  Zajištěná propustnost X RU/s      |  Zajištěná propustnost X RU/s       |  dostupná propustnost dvojnásobných RU/s <br/><br/> Tento režim konfigurace vyžaduje dvojnásobek objemu propustnosti v porovnání s jednou oblastí s Zóny dostupnosti, protože existují dvě oblasti.   |

> [!NOTE] 
> Aby bylo možné povolit podporu zón dostupnosti pro účet Azure Cosmos ve více oblastech, musí mít tento účet povolený zápis s více hlavními servery.


Pokud přidáte oblast do nových nebo existujících účtů Azure Cosmos, můžete povolit redundanci zóny. V současné době můžete redundanci zóny Povolit jenom pomocí šablon Azure Portal, PowerShellu a Azure Resource Manager. Pokud chcete povolit redundanci zóny na svém účtu Azure Cosmos, měli byste nastavit `isZoneRedundant` příznak na `true` pro konkrétní umístění. Tento příznak můžete nastavit ve vlastnosti umístění. Například následující fragment kódu prostředí PowerShell umožňuje redundanci zóny pro oblast jihovýchodní Asie:

```powershell
$locations = @( 
    @{ "locationName"="Southeast Asia"; "failoverPriority"=0; "isZoneRedundant"= "true" }, 
    @{ "locationName"="East US"; "failoverPriority"=1 } 
) 
```

Zóny dostupnosti můžete povolit pomocí Azure Portal při vytváření účtu Azure Cosmos. Když vytváříte účet, ujistěte se, že jste povolili zápis **geografické redundance**, **více oblastí**a zvolíte oblast, kde se zóny dostupnosti podporují: 

![Povolit Zóny dostupnosti pomocí Azure Portal](./media/high-availability/enable-availability-zones-using-portal.png) 

## <a name="building-highly-available-applications"></a>Vytváření vysoce dostupných aplikací

- Abyste zajistili vysokou dostupnost zápisu a čtení, nakonfigurujte účet Cosmos tak, aby zahrnoval nejméně dvě oblasti s více oblastmi zápisu. Tato konfigurace poskytuje nejvyšší dostupnost, nejnižší latenci a nejlepší škálovatelnost pro čtení i zápis zálohovaných službou SLA. Další informace najdete v tématu [Konfigurace účtu Cosmos s více oblastmi zápisu](tutorial-global-distribution-sql-api.md).

- U účtů Cosmos s více oblastmi, které jsou nakonfigurované s jednou oblastí pro zápis, můžete [Povolit automatické převzetí služeb při selhání pomocí Azure CLI nebo Azure Portal](how-to-manage-database-account.md#automatic-failover). Po povolení automatického převzetí služeb při selhání dojde k automatickému převzetí služeb při selhání Cosmos DB, když dojde k místní havárii.  

- I když je váš účet Cosmos vysoce dostupný, vaše aplikace nemusí být správně navržená tak, aby zůstala vysoce dostupná. K otestování komplexní vysoké dostupnosti vaší aplikace pravidelně vyvolají [ruční převzetí služeb při selhání pomocí Azure CLI nebo Azure Portal](how-to-manage-database-account.md#manual-failover)jako součást testování aplikace nebo procházení po havárii (Dr).

- V rámci globálně distribuovaného databázového prostředí existuje přímý vztah mezi úrovní konzistence a odolností dat při výpadku v rámci oblasti. Při vývoji plánu provozní kontinuity musíte pochopit maximální přijatelnou dobu, než se aplikace kompletně obnoví po přerušení události. Čas potřebný k úplnému obnovení aplikace je známý jako cíl doby obnovení (RTO). Také je potřeba porozumět maximálnímu intervalu nedávných aktualizací dat, které může aplikace tolerovat při obnovování po přerušení události. Časový interval aktualizací, které si můžete dovolit ztratit, se označuje jako cíl bodu obnovení (RPO). Pokud chcete zobrazit RPO a RTO pro Azure Cosmos DB, přečtěte si část [úrovně konzistence a odolnost dat](consistency-levels-tradeoffs.md#rto) .

## <a name="next-steps"></a>Další postup

Dále si můžete přečíst následující články:

* [Dostupnost a výkon kompromisy pro různé úrovně konzistence](consistency-levels-tradeoffs.md)
* [Globální škálování zřízené propustnosti](scaling-throughput.md)
* [Globální distribuce – pod kapotou](global-dist-under-the-hood.md)
* [Úrovně konzistence v Azure Cosmos DB](consistency-levels.md)
* [Jak nakonfigurovat účet Cosmos s více oblastmi zápisu](how-to-multi-master.md)
