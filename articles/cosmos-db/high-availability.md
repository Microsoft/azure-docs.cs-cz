---
title: Vysoká dostupnost v Azure Cosmos DB
description: Tento článek popisuje, jak Azure Cosmos DB poskytuje vysokou dostupnost.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/05/2021
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: ac1e77d99707cdaa34ef42eb9b327a62f4e864c0
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2021
ms.locfileid: "107365361"
---
# <a name="how-does-azure-cosmos-db-provide-high-availability"></a>Jak Azure Cosmos DB poskytovat vysokou dostupnost
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB poskytuje vysokou dostupnost dvěma hlavním způsobem. Nejprve Azure Cosmos DB replikuje data mezi oblastmi konfigurovanými v rámci účtu Cosmos. Druhý Azure Cosmos DB udržuje 4 repliky dat v rámci oblasti.

Azure Cosmos DB je globálně distribuovaná databázová služba a je základní služba dostupná ve [všech oblastech, kde je Azure k dispozici](https://azure.microsoft.com/global-infrastructure/services/?products=cosmos-db&regions=all). K vašemu účtu Azure Cosmos můžete přidružit libovolný počet oblastí Azure a vaše data se automaticky a transparentně replikují. Můžete kdykoli přidat nebo odebrat oblast pro účet Azure Cosmos. Cosmos DB je k dispozici ve všech pěti různých cloudových prostředích Azure dostupných zákazníkům:

* **Veřejný cloud Azure** , který je k dispozici globálně.

* **Azure Čína 21Vianet** je k dispozici prostřednictvím jedinečného partnerství mezi společnostmi Microsoft a 21Vianet, jedním z největších poskytovatelů internetových služeb v Číně.

* **Azure Německo** poskytuje služby v rámci modelu důvěryhodného data, který zajišťuje, že zákaznická data zůstanou v Německu pod kontrolou T-Systems International GmbH, dceřinou společností pro německé Telekom, která funguje jako důvěryhodný datový zplnomocněnec.

* **Azure Government** je k dispozici ve čtyřech oblastech v USA státním úřadům USA a jejich partnerům.

* **Azure Government pro Ministerstvo obrany (DOD)** je k dispozici ve dvou oblastech v USA do ministerstva obrany USA.

V rámci jedné oblasti Azure Cosmos DB udržuje čtyři kopie vašich dat jako repliky v rámci fyzických oddílů, jak je znázorněno na následujícím obrázku:

:::image type="content" source="./media/high-availability/cosmosdb-data-redundancy.png" alt-text="Fyzické dělení" border="false":::

* Data v kontejnerech Azure Cosmos jsou [horizontálně rozdělená na oddíly](partitioning-overview.md).

* Sada oddílů je kolekcí více sad replik. V rámci každé oblasti je každý oddíl chráněný sadou replik, která má všechny replikované zápisy a trvale potvrzené většinou replik. Repliky jsou distribuované napříč až 10-20 domén selhání.

* Všechny oddíly napříč všemi oblastmi se replikují. Každá oblast obsahuje všechny datové oddíly kontejneru Azure Cosmos a může sloužit ke čtení i zápisu, pokud je povolené zápisy ve více oblastech.  

Pokud je váš účet Azure Cosmos distribuován mezi *n* oblastí Azure, bude k dispozici alespoň *n* x 4 kopií všech vašich dat. Pokud účet Azure Cosmos ve více než 2 oblastech vylepšuje dostupnost vaší aplikace a poskytuje nízkou latenci napříč přidruženými oblastmi.

## <a name="slas-for-availability"></a>SLA pro dostupnost

Azure Cosmos DB poskytuje komplexní SLA zahrnující propustnost, latenci 99 percentilu, konzistenci a vysokou dostupnost. Následující tabulka uvádí záruky vysoké dostupnosti poskytované Azure Cosmos DB pro účty s jednou a více oblastmi. Pro zajištění vyšší dostupnosti pro zápis nakonfigurujte účet Azure Cosmos tak, aby měl více oblastí pro zápis.

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

> [!IMPORTANT]
> Pokud používáte rozhraní API SQL, je nutné nakonfigurovat sadu Cosmos DB SDK tak, aby používala všechny zadané oblasti pro čtení, aby bylo možné využít vyšší dostupnost. Další informace najdete v [tomto článku](troubleshoot-sdk-availability.md) .

### <a name="multi-region-accounts-with-a-single-write-region-write-region-outage"></a>Účty s více oblastmi s oblastí s jedním zápisem (při výpadku oblasti zápisu)

* Při výpadku oblasti zápisu bude účet Azure Cosmos automaticky propagovat sekundární oblast jako novou primární oblast pro zápis, když je v účtu Azure Cosmos nakonfigurovaná **možnost povolit automatické převzetí služeb při selhání** . Pokud je tato možnost povolená, dojde k převzetí služeb při selhání do jiné oblasti v pořadí podle priority oblasti, kterou jste zadali.

* Mějte na paměti, že ruční převzetí služeb při selhání by se nemělo aktivovat a nebude úspěšné v případě výpadku zdrojové nebo cílové oblasti. Důvodem je kontrola konzistence, kterou vyžaduje postup převzetí služeb při selhání, které vyžaduje připojení mezi oblastmi.

* Když je dříve ovlivněná oblast zpět online, všechna data zápisu, která nebyla replikována v případě selhání oblasti, jsou zpřístupněna prostřednictvím [kanálu konfliktů](how-to-manage-conflicts.md#read-from-conflict-feed). Aplikace mohou číst kanál konfliktů, řešit konflikty na základě logiky specifické pro aplikaci a podle potřeby zapisovat aktualizovaná data zpátky do kontejneru Azure Cosmos.

* Jakmile se výše ovlivněné oblasti zápisu napřed mění, bude automaticky dostupná jako oblast pro čtení. Můžete přejít zpátky do obnovené oblasti jako oblast zápisu. Oblasti můžete přepínat pomocí [PowerShellu, rozhraní příkazového řádku Azure nebo Azure Portal](how-to-manage-database-account.md#manual-failover). Neexistují **žádná data ani ztráta dostupnosti** před, během nebo po přepnutí oblasti zápisu a aplikace bude dál vysoce dostupná.

> [!IMPORTANT]
> Důrazně doporučujeme, abyste nakonfigurovali účty Azure Cosmos používané pro produkční úlohy, aby se **povolilo automatické převzetí služeb při selhání**. To umožňuje Cosmos DB převzetí služeb při selhání databáze účtů, aby se automaticky dostupná oblasti. Pokud tato konfigurace není k dispozici, účet bude mít za následek ztrátu dostupnosti zápisu pro celou dobu výpadku oblasti zápisu, protože ruční převzetí služeb při selhání nebude úspěšné kvůli nedostatku možnosti připojení k oblasti.

### <a name="multi-region-accounts-with-a-single-write-region-read-region-outage"></a>Účty s více oblastmi s jednou oblastí zápisu (nevýpadek oblasti čtení)

* Při výpadku oblasti čtení budou účty Azure Cosmos s využitím jakékoli úrovně konzistence nebo silné konzistence se třemi nebo více oblastmi pro čtení pořád vysoce dostupné pro čtení a zápisy.

* Účty Azure Cosmos s využitím silné konzistence se třemi oblastmi (jeden zápis, dvě čtení) zachovávají dostupnost zápisu během výpadku oblasti čtení. U účtů se dvěma oblastmi a s povoleným automatickým převzetím služeb při selhání přestane účet přijímat zápisy, dokud se tato oblast neoznačí jako neúspěšná a dojde k automatickému

* Ovlivněná oblast je automaticky odpojena a bude označena jako offline. Sady [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md) přesměrují volání čtení do další dostupné oblasti v seznamu upřednostňovaných oblastí.

* Pokud není žádná oblast ze seznamu upřednostňovaných oblastí k dispozici, volání se automaticky vrátí na aktuální oblast zápisu.

* V kódu aplikace se nevyžadují žádné změny pro zpracování výpadku oblasti čtení. Když je oblast s ovlivněným čtením zpět online, bude automaticky synchronizována s aktuální oblastí pro zápis a bude opět k dispozici pro poskytování požadavků na čtení.

* Následná čtení se přesměrují na zotavenou oblast bez toho, aby se musel nějak změnit kód aplikace. V průběhu převzetí služeb při selhání a opětovném připojení k dříve neúspěšnému výskytu se Přečtěte záruky konzistence, které Azure Cosmos DB.

* I ve výjimečné a unfortunate události, když je oblast Azure trvale nezotavitelné, nedochází k žádné ztrátě dat, pokud je váš účet Azure Cosmos ve více oblastech nakonfigurovaný se *silnými* konzistencí. V případě trvale nezotavitelné oblasti zápisu se jedná o účet Azure Cosmos ve více oblastech nakonfigurovaný s konzistencí s ohraničenou kočárkou, což je potenciální okno ztráty dat omezené na okno zastaralosti (*k* nebo *T*), kde k = 100 000 aktualizací nebo t = 5 minut, což nastane dřív. V případě relace, konzistentní a konečné úrovně konzistence je možné okno ztráty dat omezit na maximálně 15 minut. Další informace o cílech RTO a RPO pro Azure Cosmos DB najdete v tématu [úrovně konzistence a odolnost dat](./consistency-levels.md#rto) .

## <a name="availability-zone-support"></a>Podpora zón dostupnosti

Kromě odolnosti mezi oblastmi Azure Cosmos DB taky při výběru oblasti pro přidružení k vašemu účtu Azure Cosmos podporuje **redundanci zón** v podporovaných oblastech.

Díky podpoře zón dostupnosti (AZ) zajistí Azure Cosmos DB, aby se repliky umístily do několika zón v rámci dané oblasti, aby se zajistila vysoká dostupnost a odolnost pro oblasti selhání. Zóny dostupnosti poskytovat smlouvu SLA o 99,995% dostupnosti bez změny latence. V případě selhání jedné zóny poskytuje redundance zóny plnou odolnost dat s cílem RPO = 0 a dostupnost s RTO = 0. Redundance zóny je doplňkovou možností regionální replikace. Na redundanci zóny se nedá spoléhat, aby bylo možné dosáhnout místní odolnosti.

Redundanci zóny je možné nakonfigurovat jenom při přidávání nové oblasti do účtu Azure Cosmos. U existujících oblastí je možné povolit redundanci zóny tím, že ji odeberete a pak ji přidáte zpátky se zapnutou redundancí zóny. Pro účet s jednou oblastí to vyžaduje přidání jedné další oblasti do dočasného převzetí služeb při selhání a následnou odebrání a přidání požadované oblasti s povoleným redundancí zóny.

Když konfigurujete zápisy ve více oblastech pro účet Azure Cosmos, můžete se odhlásit k redundanci zóny bez dalších poplatků. V opačném případě se podívejte na ceny pro podporu redundance zóny v následující tabulce. Seznam oblastí, ve kterých jsou dostupné zóny dostupnosti, najdete v tématu [zóny dostupnosti](../availability-zones/az-region.md).

Následující tabulka shrnuje schopnost vysoké dostupnosti u různých konfigurací účtu:

|KPI|Jedna oblast bez AZs|Jedna oblast s AZs|Více oblastí, zápisy v jedné oblasti s AZs|Vícenásobné zápisy ve více oblastech pomocí AZs|
|---------|---------|---------|---------|---------|
|Smlouva SLA o dostupnosti pro zápis | 99,99 % | 99,995% | 99,995% | 99,999 % |
|SLA dostupnosti pro čtení  | 99,99 % | 99,995% | 99,995% | 99,999 % |
|Selhání zón – ztráta dat | Ztráta dat | Žádná ztráta dat | Žádná ztráta dat | Žádná ztráta dat |
|Selhání zón – dostupnost | Ztráta dostupnosti | Žádná ztráta dostupnosti | Žádná ztráta dostupnosti | Žádná ztráta dostupnosti |
|Oblastní výpadek – ztráta dat | Ztráta dat |  Ztráta dat | Závislá na úrovni konzistence. Další informace najdete v tématu [konzistence, dostupnost a kompromisy](./consistency-levels.md) týkající se výkonu. | Závislá na úrovni konzistence. Další informace najdete v tématu [konzistence, dostupnost a kompromisy](./consistency-levels.md) týkající se výkonu.
|Oblastní výpadek – dostupnost | Ztráta dostupnosti | Ztráta dostupnosti | Neztráta dostupnosti pro neúspěch oblasti čtení, dočasné pro selhání oblasti zápisu | Žádná ztráta dostupnosti |
|Cena (***1** _) | – | Zřízená sazba za RU/s × 1,25 | Zřízená sazba za RU/s x 1,25 (_ *_2_* *) | Rychlost zápisu ve více oblastech |

***1*** pro jednotky žádosti bez serveru se vynásobí koeficientem 1,25.

***2*** 1,25 sazba se aplikuje jenom na ty oblasti, ve kterých je povolen AZ.

Zóny dostupnosti lze povolit prostřednictvím:

* [Azure Portal](how-to-manage-database-account.md#addremove-regions-from-your-database-account)

* [Azure PowerShell](manage-with-powershell.md#create-account)

* [Azure CLI](manage-with-cli.md#add-or-remove-regions)

* [Šablony Azure Resource Manageru](./manage-with-templates.md)

## <a name="building-highly-available-applications"></a>Vytváření vysoce dostupných aplikací

* V těchto událostech zkontrolujte očekávané [chování sady SDK Azure Cosmos](troubleshoot-sdk-availability.md) a které jsou konfigurace, které to ovlivňují.

* Abyste zajistili vysokou dostupnost zápisu a čtení, nakonfigurujte účet Azure Cosmos tak, aby zahrnoval alespoň dvě oblasti s více oblastmi zápisu. Tato konfigurace poskytuje nejvyšší dostupnost, nejnižší latenci a nejlepší škálovatelnost pro čtení i zápis zálohovaných službou SLA. Další informace najdete v tématu [Konfigurace účtu Azure Cosmos s více oblastmi zápisu](tutorial-global-distribution-sql-api.md).

* Pro účty Azure Cosmos s více oblastmi, které jsou nakonfigurované s jednou oblastí pro zápis, [umožňují automatické převzetí služeb při selhání pomocí Azure CLI nebo Azure Portal](how-to-manage-database-account.md#automatic-failover). Po povolení automatického převzetí služeb při selhání dojde k automatickému převzetí služeb při selhání Cosmos DB, když dojde k místní havárii.  

* I když je váš účet Azure Cosmos vysoce dostupný, vaše aplikace nemusí být správně navržená tak, aby zůstala vysoce dostupná. K otestování komplexní vysoké dostupnosti vaší aplikace, jako součást testování aplikací nebo zotavení po havárii (DR), můžete dočasně zakázat automatické převzetí služeb při selhání pro účet, vyvolat [ruční převzetí služeb při selhání pomocí PowerShellu, rozhraní příkazového řádku Azure nebo Azure Portal](how-to-manage-database-account.md#manual-failover)a potom monitorovat převzetí služeb při selhání vaší aplikace. Po dokončení můžete navrátit služby po obnovení do primární oblasti a obnovit automatické převzetí služeb při selhání pro tento účet.

> [!IMPORTANT]
> Nevolejte ruční převzetí služeb při selhání při Cosmos DB ve zdrojové nebo cílové oblasti, protože vyžaduje, aby připojení oblastí udržovalo konzistenci dat a nebude úspěšné.

* V rámci globálně distribuovaného databázového prostředí existuje přímý vztah mezi úrovní konzistence a odolností dat při výpadku v rámci oblasti. Při vývoji plánu provozní kontinuity musíte pochopit maximální přijatelnou dobu, než se aplikace kompletně obnoví po přerušení události. Čas potřebný k úplnému obnovení aplikace je známý jako cíl doby obnovení (RTO). Také je potřeba porozumět maximálnímu intervalu nedávných aktualizací dat, které může aplikace tolerovat při obnovování po přerušení události. Časový interval aktualizací, které si můžete dovolit ztratit, se označuje jako cíl bodu obnovení (RPO). Pokud chcete zobrazit RPO a RTO pro Azure Cosmos DB, přečtěte si část [úrovně konzistence a odolnost dat](./consistency-levels.md#rto) .

## <a name="what-to-expect-during-a-cosmos-db-region-outage"></a>Co očekávat při výpadku Cosmos DB oblasti

U účtů s jednou oblastí budou klienti zacházet s ztrátou dostupnosti čtení a zápisu.

Účty s více oblastmi budou mít různá chování v závislosti na následující tabulce.

| Oblasti zápisu | Automatické převzetí služeb při selhání | Co očekávat | Co dělat |
| -- | -- | -- | -- |
| Jedna oblast zápisu | Není povoleno | V případě výpadku v oblasti čtení budou všichni klienti přesměrováni do jiných oblastí. Žádná ztráta dostupnosti čtení nebo zápisu. Nedochází ke ztrátě dat. <p/> V případě výpadku v oblasti zápisu budou klienti zacházet ze ztráty dostupnosti zápisu. Pokud není vybraná silná úroveň konzistence, některá data možná nebudou replikována do zbývajících aktivních oblastí. To závisí na úrovni consistenvy vybrané, jak je popsáno v [této části](consistency-levels.md#rto). Pokud ovlivněná oblast utrpí trvalou ztrátu dat, může dojít ke ztrátě nereplikovaných dat. <p/> Cosmos DB obnoví dostupnost zápisu automaticky, když výpadek skončí. | Během výpadku zajistěte, aby ve zbývajících oblastech byla zajištěna dostatečná ru podpora pro čtení provozu. <p/> Nespouštějte *během* výpadku ruční převzetí služeb při selhání, protože to nebude úspěšné. <p/> Když se výpadek nachází, znovu nastavte zřízené ru podle potřeby. |
| Jedna oblast zápisu | Povoleno | V případě výpadku v oblasti čtení budou všichni klienti přesměrováni do jiných oblastí. Žádná ztráta dostupnosti čtení nebo zápisu. Nedochází ke ztrátě dat. <p/> V případě výpadku v oblasti zápisu budou klienti zacházet ze ztráty dostupnosti, dokud Cosmos DB automaticky nevybere novou oblast jako novou oblast zápisu podle vašich požadavků. Pokud není vybraná silná úroveň konzistence, některá data možná nebudou replikována do zbývajících aktivních oblastí. To závisí na úrovni consistenvy vybrané, jak je popsáno v [této části](consistency-levels.md#rto). Pokud ovlivněná oblast utrpí trvalou ztrátu dat, může dojít ke ztrátě nereplikovaných dat. | Během výpadku zajistěte, aby ve zbývajících oblastech byla zajištěna dostatečná ru podpora pro čtení provozu. <p/> Nespouštějte *během* výpadku ruční převzetí služeb při selhání, protože to nebude úspěšné. <p/> Když je výpadek, můžete oblast pro zápis přesunout zpátky do původní oblasti a podle potřeby znovu upravit zřízené ru. Účty, které používají rozhraní API SQL, můžou v [informačním kanálu konfliktů](how-to-manage-conflicts.md#read-from-conflict-feed)obnovit taky nereplikovaná data v neúspěšné oblasti. |
| Více oblastí zápisu | Neuvedeno | Žádná ztráta dostupnosti čtení nebo zápisu. <p/> Nedávno aktualizovaná data v oblasti selhání se můžou unavilable ve zbývajících aktivních oblastech. Případná, konzistentní předpona a úrovně konzistence relace zaručují neaktuálnost <15mins. U ohraničené neaktuálnosti jsou zaručeny méně než K aktualizací nebo T sekund v závislosti na konfiguraci. Pokud ovlivněná oblast utrpí trvalou ztrátu dat, může dojít ke ztrátě nereplikovaných dat. | Během výpadku zajistěte, aby ve zbývajících oblastech byla zajištěna dostatečná ru podpora dalšího provozu. <p/> V případě výpadku můžete podle potřeby znovu upravit zřízené ru. Pokud je to možné, Cosmos DB bude automaticky obnovovat nereplikovaná data v neúspěšné oblasti pomocí nakonfigurované metody řešení konfliktů pro účty SQL API a poslední zápis WINS pro účty pomocí jiných rozhraní API. |

## <a name="next-steps"></a>Další kroky

Dále si můžete přečíst následující články:

* [Kompromisy týkající se dostupnosti a výkonu pro různé úrovně konzistence](./consistency-levels.md)

* [Zřízená propustnost globálního škálování](./request-units.md)

* [Globální distribuce – pod pokličkou](global-dist-under-the-hood.md)

* [Úrovně konzistence ve službě Azure Cosmos DB](consistency-levels.md)

* [Jak nakonfigurovat účet Cosmos s více oblastmi zápisu](how-to-multi-master.md)

* [Chování sady SDK v prostředích s více oblastmi](troubleshoot-sdk-availability.md)