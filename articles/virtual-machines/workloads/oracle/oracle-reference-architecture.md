---
title: Referenční architektury pro databáze Oracle v Azure | Dokumenty společnosti Microsoft
description: Odkazuje na architektury pro spouštění databází Oracle Database Enterprise Edition na virtuálních počítačích Microsoft Azure.
services: virtual-machines-linux
author: BorisB2015
manager: gwallace
tags: ''
ms.service: virtual-machines
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 12/13/2019
ms.author: borisb
ms.custom: ''
ms.openlocfilehash: bbb6665299ce9b6521eeb8801d8621dfbdc17f4a
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81683492"
---
# <a name="reference-architectures-for-oracle-database-enterprise-edition-on-azure"></a>Referenční architektury pro Oracle Database Enterprise Edition v Azure

Tato příručka podrobně popisuje informace o nasazení vysoce dostupné databáze Oracle v Azure. Kromě toho se tato příručka ponoří do aspekty zotavení po havárii. Tyto architektury byly vytvořeny na základě nasazení zákazníků. Tato příručka se vztahuje pouze na oracle database enterprise edition.

Máte-li zájem dozvědět se více o maximalizaci výkonu databáze Oracle, přečtěte [si informace o architektu databáze Oracle DB](oracle-design.md).

## <a name="assumptions"></a>Předpoklady

- Rozumíte různým konceptům Azure, jako jsou [zóny dostupnosti.](../../../availability-zones/az-overview.md)
- Používáte oracle database enterprise edition 12c nebo novější
- Jste si vědomi a berete na vědomí licenční důsledky při používání řešení v tomto článku

## <a name="high-availability-for-oracle-databases"></a>Vysoká dostupnost databází Oracle

Dosažení vysoké dostupnosti v cloudu je důležitou součástí plánování a návrhu každé organizace. Microsoft Azure nabízí [zóny dostupnosti](../../../availability-zones/az-overview.md) a skupiny dostupnosti (které se používají v oblastech, kde nejsou k dispozici zóny dostupnosti). Přečtěte si další informace o [správě dostupnosti virtuálních počítačů,](../../../virtual-machines/linux/manage-availability.md) které můžete navrhnout pro cloud.

Kromě nástrojů a nabídek nativních pro cloud poskytuje společnost Oracle řešení pro vysokou dostupnost, jako je [oracle data guard](https://docs.oracle.com/en/database/oracle/oracle-database/18/sbydb/introduction-to-oracle-data-guard-concepts.html#GUID-5E73667D-4A56-445E-911F-1E99092DD8D7), ochrana dat s [FSFO](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/dgbkr/index.html), [Sharding](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/admin/sharding-overview.html)a [GoldenGate,](https://www.oracle.com/middleware/technologies/goldengate.html) která lze nastavit v Azure. Tato příručka popisuje referenční architektury pro každé z těchto řešení.

Nakonec při migraci nebo vytváření aplikací pro cloud je důležité vyladit kód aplikace a přidat vzory nativní pro cloud, jako je [například vzor opakování](https://docs.microsoft.com/azure/architecture/patterns/retry) a [vzor jističe](https://docs.microsoft.com/azure/architecture/patterns/circuit-breaker). Další vzory definované v [průvodci cloudové vzory návrhu](https://docs.microsoft.com/azure/architecture/patterns/) může pomoci vaší aplikaci být odolnější.

### <a name="oracle-rac-in-the-cloud"></a>Oracle RAC v cloudu

Oracle Real Application Cluster (RAC) je řešení společnosti Oracle, které pomáhá zákazníkům dosáhnout vysokých propustnosti tím, že má mnoho instancí přístupu k jednomu databázovému úložišti (vzor architektury Shared-all). Zatímco Oracle RAC lze také použít pro vysokou dostupnost v místním prostředí, Oracle RAC sám nelze použít pro vysokou dostupnost v cloudu, protože chrání pouze před selháním na úrovni instance a nikoli proti selhání na úrovni racku nebo datového centra. Z tohoto důvodu společnost Oracle doporučuje používat službu Oracle Data Guard s databází (ať už je to jedna instance nebo RAC) pro vysokou dostupnost. Zákazníci obecně vyžadují vysokou sla pro spuštění jejich kritické aplikace. Oracle RAC není v současné době certifikován nebo podporován společností Oracle v Azure. Azure však nabízí funkce, jako je Azure nabízí zóny dostupnosti a plánovaná okna údržby, které pomáhají chránit před chybami na úrovni instancí. Kromě toho mohou zákazníci používat technologie jako Oracle Data Guard, Oracle GoldenGate a Oracle Sharding pro vysoký výkon a odolnost tím, že chrání své databáze před rackovými a geopolitickými selháními na úrovni datových center a geopolitických selhání.

Při provozování databází Oracle ve více [zónách dostupnosti](https://docs.microsoft.com/azure/availability-zones/az-overview) ve spojení se službou Oracle Data Guard nebo GoldenGate mohou zákazníci získat tarif s dostupností 99,99 %. V oblastech Azure, kde ještě nejsou k dispozici zóny dostupnosti, můžou zákazníci používat [sady dostupnosti](https://docs.microsoft.com/azure/virtual-machines/linux/manage-availability#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy) a dosáhnout doby dostupnosti s la 99,95 %.

>Poznámka: Můžete mít cíl uptime, který je mnohem vyšší než doba sla poskytované společností Microsoft.

## <a name="disaster-recovery-for-oracle-databases"></a>Zotavení po havárii pro databáze Oracle

Při hostování důležitých aplikací v cloudu je důležité navrhnout vysokou dostupnost a zotavení po havárii.

Oracle Database Enterprise Edition je služba Oracle Data Guard užitečnou funkcí pro zotavení po havárii. Můžete nastavit instanci pohotovostní databáze ve [spárované oblasti Azure](/azure/best-practices-availability-paired-regions) a nastavit převzetí služeb při selhání služby Data Guard pro zotavení po havárii. Pro nulovou ztrátu dat doporučujeme kromě aktivní ochrany dat nasadit instanci Oracle Data Guard Far Sync. 

Pokud vaše aplikace povolí latenci, zvažte nastavení instance Data Guard Far Sync v jiné zóně dostupnosti, než je primární databáze Oracle (je vyžadováno důkladné testování). Režim **maximální dostupnosti** slouží k nastavení synchronního přenosu souborů opakování do instance Far Sync. Tyto soubory jsou pak přeneseny asynchronně do databáze v pohotovostním režimu. 

Pokud vaše aplikace neumožňuje ztrátu výkonu při nastavování instance Far Sync v jiné zóně dostupnosti v režimu **maximální dostupnosti** (synchronní), můžete nastavit instanci Far Sync ve stejné zóně dostupnosti jako primární databáze. Chcete-li získat větší dostupnost, zvažte nastavení více instancí Far Sync v blízkosti primární databáze a alespoň jedné instance v blízkosti databáze v pohotovostním režimu (pokud se role přejde). Další informace o řešení Oracle Data Guard Far Sync načtěte v této [bílé knize oracle active data guard far sync](https://www.oracle.com/technetwork/database/availability/farsync-2267608.pdf).

Při použití databází Oracle Standard Edition existují řešení pro vlastní potřebu, jako je pohotovostní režim DBVisit, která umožňují nastavit vysokou dostupnost a zotavení po havárii.

## <a name="reference-architectures"></a>Referenční architektury

### <a name="oracle-data-guard"></a>Oracle Data Guard

Oracle Data Guard zajišťuje vysokou dostupnost, ochranu dat a zotavení po havárii podnikových dat. Ochrana dat udržuje pohotovostní databáze jako transakce konzistentní kopie primární databáze. V závislosti na vzdálenosti mezi primární a sekundární databází a toleranci aplikace pro latenci můžete nastavit synchronní nebo asynchronní replikaci. Pokud pak primární databáze není k dispozici z důvodu plánovaného nebo neplánovaného výpadku, funkce Zabránění dat může přepnout libovolnou pohotovostní databázi na primární roli a minimalizovat prostoje. 

Při použití služby Oracle Data Guard můžete také otevřít sekundární databázi pro účely jen pro čtení. Tato konfigurace se nazývá Active Data Guard. Oracle Database 12c představila funkci nazvanou Instance vzdálené synchronizace datových strůjců dat. Tato instance umožňuje nastavit konfiguraci nulové ztráty dat databáze Oracle bez nutnosti ohrožení výkonu.

> [!NOTE]
> Aktivní ochrana dat vyžaduje další licencování. Tato licence je také vyžadována pro použití funkce Far Sync. Obraťte se na svého zástupce společnosti Oracle a prodiskutujte licenční důsledky.

#### <a name="oracle-data-guard-with-fsfo"></a>Ochrana dat Oracle s FSFO
Oracle Data Guard s převzetím služeb při selhání rychlého spuštění (FSFO) může poskytnout další odolnost proti chybám nastavením zprostředkovatele na samostatném počítači. Zprostředkovatel ochrany dat a sekundární databáze spustit pozorovatele a sledovat primární databáze pro prostoje. To umožňuje redundanci v nastavení pozorovatele služby Data Guard. 

S databází Oracle Database verze 12.2 a vyšší je také možné nakonfigurovat více pozorovatelů pomocí jediné konfigurace zprostředkovatele Oracle Data Guard. Toto nastavení poskytuje další dostupnost v případě, že jeden pozorovatel a sekundární databáze zkušenosti prostoje. Data Guard Broker je lehký a může být hostován na relativně malém virtuálním počítači. Další informace o službě Data Guard Broker a jeho výhodách naleznete v dokumentaci k tomuto tématu společnosti [Oracle.](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/dgbkr/oracle-data-guard-broker-concepts.html)

Následující diagram je doporučená architektura pro použití služby Oracle Data Guard v Azure se zónami dostupnosti. Tato architektura umožňuje získat sla s 99,99 % k získání sla s 99,99 %.

![Oracle Database využívající zóny dostupnosti s zprostředkovatelem ochrany dat – FSFO](./media/oracle-reference-architecture/oracledb_dg_fsfo_az.png)

V předchozím diagramu klientský systém přistupuje k vlastní aplikaci s back-endem Oracle prostřednictvím webu. Webový front-end je konfigurován v systému vyrovnávání zatížení. Webový front-end provede volání na příslušný aplikační server pro zpracování práce. Aplikační server se dotazuje primární databáze Oracle. Databáze Oracle byla nakonfigurována pomocí [virtuálního počítače optimalizovaného](../../../virtual-machines/windows/sizes-memory.md) pro hyperthreaded paměť s [omezenými jádrovými virtuálními procesory,](../../../virtual-machines/windows/constrained-vcpu.md) aby se ušetřily náklady na licencování a maximalizoval výkon. Pro výkon a vysokou dostupnost se používá více prémiových nebo ultra disků (spravované disky).

Databáze Oracle jsou umístěny ve více zónách dostupnosti pro vysokou dostupnost. Každá zóna se skládá z jednoho nebo více datových center vybavených nezávislým napájením, chlazením a sítí. Aby byla zajištěna odolnost proti chybám, jsou ve všech povolených oblastech nastaveny minimálně tři samostatné zóny. Fyzické oddělení zón dostupnosti v rámci oblasti chrání data před selháním datového centra. Kromě toho dva pozorovatelé FSFO jsou nastaveny ve dvou zónách dostupnosti k zahájení a převzetí služeb při selhání databáze sekundární, když dojde k výpadku. 

Můžete nastavit další pozorovatele a/nebo pohotovostní databáze v jiné zóně dostupnosti (AZ 1, v tomto případě) než zóna zobrazená v předchozí architektuře. Databáze Oracle jsou monitorovány z důvodu dostupnosti a výkonu pomocí řešení Oracle Enterprise Manager (OEM). OEM také umožňuje generovat různé zprávy o výkonu a využití.

V oblastech, kde nejsou podporovány zóny dostupnosti, můžete použít skupiny dostupnosti k nasazení databáze Oracle database vysoce dostupným způsobem. Sady dostupnosti umožňují dosáhnout dostupnosti virtuálního virtuálního bylu 99,95 %. Následující diagram je referenční architekturou tohoto použití:

![Oracle Database využívající dostupnost s úložištěm dat Broker - FSFO](./media/oracle-reference-architecture/oracledb_dg_fsfo_as.png)

> [!NOTE]
> * Virtuální ms Oracle Enterprise Manager nemusí být umístěn v sadě dostupnosti, protože se nasazuje pouze jedna instance oem.
> * Ultra disky nejsou aktuálně podporovány v konfiguraci sady dostupnosti.

#### <a name="oracle-data-guard-far-sync"></a>Vzdálená synchronizace oracle data guard

Oracle Data Guard Far Sync poskytuje pro databáze Oracle databázi nulovou ztrátu dat. Tato funkce umožňuje chránit před ztrátou dat v případě selhání databázového počítače. Oracle Data Guard Far Sync musí být nainstalován na samostatný virtuální počítač. Far Sync je zjednodušená instance Oracle, která má pouze řídicí soubor, soubor hesel, spfile a pohotovostní protokoly. Neexistují žádné datové soubory nebo soubory protokolu rego. 

Pro ochranu proti nulové ztrátě dat musí existovat synchronní komunikace mezi primární databází a instancí Far Sync. Far Sync instance obdrží předělat z primární synchronním způsobem a předává ji okamžitě do všech pohotovostních databází asynchronním způsobem. Toto nastavení také snižuje režii na primární databázi, protože má pouze k odeslání opakování do instance Far Sync, nikoli všechny pohotovostní databáze. Pokud se instance far sync nezdaří, funkce Zabránění dat automaticky používá asynchronní přenos do sekundární databáze z primární databáze k zachování ochrany téměř nulové ztráty dat. Pro větší odolnost proti chybám mohou zákazníci nasadit více instancí far sync pro každou instanci databáze (primární a sekundární).

Následující diagram je architektura s vysokou dostupností pomocí vzdálené synchronizace oracle data guard:

![Databáze Oracle využívající zóny dostupnosti s aplikací Data Guard Far Sync & Broker - FSFO](./media/oracle-reference-architecture/oracledb_dg_fs_az.png)

V předchozí architektuře je instance Far Sync nasazená ve stejné zóně dostupnosti jako instance databáze, aby se snížila latence mezi těmito dvěma. V případech, kdy je aplikace citlivá na latenci, zvažte nasazení databáze a instance Far Sync nebo instance ve [skupině umístění bezkontaktní](../../../virtual-machines/linux/proximity-placement-groups.md).

Následující diagram je architektura využívající řešení Oracle Data Guard FSFO a Far Sync k dosažení vysoké dostupnosti a zotavení po havárii:

![Databáze Oracle Database využívá zóny dostupnosti pro zotavení po havárii pomocí služby Data Guard Far Sync & Broker – FSFO](./media/oracle-reference-architecture/oracledb_dg_fs_az_dr.png)

### <a name="oracle-goldengate"></a>Oracle GoldenGate

GoldenGate umožňuje výměnu a manipulaci s daty na úrovni transakcí mezi více heterogenními platformami v rámci celého podniku. Přesune potvrzené transakce s integritou transakcí a minimální režií na stávající infrastrukturu. Jeho modulární architektura poskytuje flexibilitu pro extrahování a replikaci vybraných datových záznamů, transakčních změn a změn DDL (jazyka definice dat) v různých topologii.

Oracle GoldenGate umožňuje nakonfigurovat databázi pro vysokou dostupnost poskytováním obousměrné replikace. To umožňuje nastavit **konfiguraci s více předlohy** nebo **aktivní aktivní .** Následující diagram je doporučená architektura pro oracle goldengate aktivní aktivní nastavení v Azure. V následující architektuře byla databáze Oracle nakonfigurována pomocí [virtuálního počítače optimalizovaného](../../../virtual-machines/windows/sizes-memory.md) pro hyperthreaded paměť s [omezenými jádrovými virtuálními procesory,](../../../virtual-machines/windows/constrained-vcpu.md) aby se ušetřily náklady na licencování a maximalizoval výkon. Pro výkon a dostupnost se používá více disků premium nebo ultra disků (spravovaných disků).

![Oracle Database využívající zóny dostupnosti s zprostředkovatelem ochrany dat – FSFO](./media/oracle-reference-architecture/oracledb_gg_az.png)

> [!NOTE]
> Podobnou architekturu lze nastavit pomocí sad dostupnosti v oblastech, kde zóny dostupnosti nejsou aktuálně k dispozici.

Oracle GoldenGate obsahuje procesy, jako je Extrakce, Pump a Replicat, které vám pomohou asynchronně replikovat data z jednoho databázového serveru Oracle na jiný. Tyto procesy umožňují nastavit obousměrnou replikaci, abyste zajistili vysokou dostupnost databáze, pokud je k dispozici prostoje na úrovni zóny. V předchozím diagramu proces extrakce běží na stejném serveru jako databáze Oracle, zatímco procesy Data Pump a Replicat jsou spuštěny na samostatném serveru ve stejné zóně dostupnosti. Proces Replikace se používá k příjmu dat z databáze v jiné zóně dostupnosti a potvrzení dat do databáze Oracle v zóně dostupnosti. Podobně proces datového čerpadla odesílá data, která byla extrahována procesem extrakce, procesu Replikátora v jiné zóně dostupnosti. 

Zatímco předchozí diagram architektury zobrazuje proces datového čerpadla a replikace nakonfigurovaný na samostatném serveru, můžete nastavit všechny procesy Oracle GoldenGate na stejném serveru na základě kapacity a využití serveru. Vždy se podívejte do sestavy AWR a metriky v Azure pochopit způsob využití vašeho serveru.

Při nastavování obousměrné replikace Oracle GoldenGate v různých zónách dostupnosti nebo v různých oblastech je důležité zajistit, aby latence mezi různými součástmi byla přijatelná pro vaši aplikaci. Latence mezi zónami dostupnosti a oblastmi se může lišit a závisí na několika faktorech. Doporučujeme nastavit testy výkonu mezi aplikační vrstvou a databázovou vrstvou v různých zónách nebo oblastech dostupnosti a potvrdit, že splňuje požadavky na výkon vaší aplikace.

Aplikační vrstvu lze nastavit ve vlastní podsíti a databázovou vrstvu lze rozdělit do vlastní podsítě. Pokud je to možné, zvažte použití [Azure Application Gateway](../../../application-gateway/overview.md) k vyrovnávání zatížení provozu mezi aplikačními servery. Azure Application Gateway je robustní webový vyrovnávání zatížení. Poskytuje spřažení relací založených na souborech cookie, která udržuje relaci uživatele na stejném serveru, čímž minimalizuje konflikty v databázi. Alternativy k aplikační bráně jsou [Azure Load Balancer](../../../load-balancer/load-balancer-overview.md) a [Azure Traffic Manager](../../../traffic-manager/traffic-manager-overview.md).

### <a name="oracle-sharding"></a>Střepy Oracle

Sharding je vzor datové vrstvy, který byl zaveden v oracle 12.2. Umožňuje vodorovně rozdělit a škálovat data napříč nezávislými databázemi. Jedná se o architekturu sdílení bez sdílení, kde je každá databáze hostována na vyhrazeném virtuálním počítači, který kromě odolnosti proti chybám a zvýšené dostupnosti umožňuje vysokou propustnost čtení a zápisu. Tento vzor eliminuje jednotlivé body selhání, poskytuje izolaci chyb a umožňuje postupné upgrady bez výpadků. Prostoje jednoho úlomku nebo selhání na úrovni datového centra nemá vliv na výkon nebo dostupnost ostatních úlomků v jiných datových centrech. 

Sharding je vhodný pro aplikace OLTP s vysokou propustností, které si nemohou dovolit žádné prostoje. Všechny řádky se stejným klíčem srážlivosti jsou vždy zaručeno, že se na stejném oddílu, čímž se zvyšuje výkon poskytuje vysokou konzistenci. Aplikace, které používají sharding musí mít dobře definovaný datový model a strategii distribuce dat (konzistentní hash, rozsah, seznam nebo složený), který primárně přistupuje k datům pomocí klíče s ráždí (například *customerId* nebo *accountNum).* Sharding také umožňuje ukládat konkrétní sady dat blíže ke koncovým zákazníkům, což vám pomůže splnit vaše požadavky na výkon a dodržování předpisů.

Doporučujeme replikovat úlomky pro vysokou dostupnost a zotavení po havárii. Toto nastavení lze provést pomocí technologií Oracle, jako je Oracle Data Guard nebo Oracle GoldenGate. Jednotka replikace může být oddíl, část oddílu nebo skupina úlomků. Dostupnost databáze s oddíly není ovlivněna výpadku nebo zpomalení jednoho nebo více oddílů. Pro vysokou dostupnost pohotovostní chrliče lze umístit do stejné zóny dostupnosti, kde jsou umístěny primární úlomky. Pro zotavení po havárii mohou být pohotovostní střepy umístěny v jiné oblasti. Můžete také nasadit úlomky ve více oblastech sloužit provoz v těchto oblastech. Další informace o konfiguraci vysoké dostupnosti a replikace vaší databáze s chybově roztřesené databáze naleznete v [dokumentaci k řešení Oracle Sharding](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-high-availability.html).

Oracle Sharding se skládá především z následujících součástí. Další informace o těchto součástech naleznete v [dokumentaci oracle sharding](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-overview.html):

- **Katalog střepu** – speciální databáze Oracle, která je trvalým úložištěm pro všechna konfigurační data databáze svižných kódů. Všechny změny konfigurace, jako je například přidání nebo odebrání štřepů, mapování dat a DDLs v databázi střepů jsou iniciovány v katalogu štrůdků. Katalog úlomků také obsahuje hlavní kopii všech duplicitních tabulek v SDB. 

  Katalog štřepů používá materializovaná zobrazení k automatické replikaci změn do duplicitních tabulek ve všech šmejdech. Databáze katalogu štřepů také funguje jako koordinátor dotazů používaný ke zpracování dotazů s více oddíly a dotazů, které neurčují klíč pro říť. 
  
  Doporučeným osvědčeným postupem je používat službu Oracle Data Guard ve spojení se zónami dostupnosti nebo sadami dostupnosti pro vysokou dostupnost katalogu střepů. Dostupnost katalogu střepů nemá žádný vliv na dostupnost databáze s oddíly. Prostoje v katalogu střepů ovlivňuje pouze operace údržby a multishard dotazy během krátké období, které dokončí převzetí služeb při selhání služby Data Guard. Online transakce jsou i nadále směrovány a prováděny SDB a nejsou ovlivněny výpadku katalogu.

- **Ředitelé střepů** – zjednodušené služby, které je třeba nasadit v každé oblasti nebo zóně dostupnosti, ve které jsou vaše oddíly. Ředitelé střepů jsou globální správci služeb nasazené v kontextu oracle sharding. Pro vysokou dostupnost se doporučuje nasadit alespoň jeden ředitel štřepů v každé zóně dostupnosti, ve které existují vaše oddíly. 

  Při připojení k databázi zpočátku, informace o směrování je nastaven a střep y ředitele a je uložen do mezipaměti pro následné požadavky, obcházet oddíl usměrňovacího adresáře. Jakmile je relace vytvořena s úlomkem, všechny dotazy SQL a DML jsou podporovány a provedeny v rozsahu daného oddílu. Toto směrování je rychlé a používá se pro všechny úlohy OLTP, které provádějí transakce uvnitř sutry. Doporučujeme používat přímé směrování pro všechny úlohy OLTP, které vyžadují nejvyšší výkon a dostupnost. Mezipaměť směrování se automaticky aktualizuje, když se oddíl oddílu stane nedostupným nebo dojde ke změnám topologie řícení. 
  
  Pro vysoce výkonné směrování závislé na datech společnost Oracle doporučuje používat fond připojení při přístupu k datům v databázi s velkým než se řazenou. Fondy připojení Oracle, knihovny specifické pro jazyk a ovladače podporují oracle sharding. Další podrobnosti naleznete v dokumentaci k [řešení Oracle Sharding.](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-overview.html#GUID-3D41F762-BE04-486D-8018-C7A210D809F9)

- **Globální služba** - Globální služba je podobná pravidelné databázové službě. Kromě všech vlastností databázové služby má globální služba vlastnosti pro dělené databáze, jako je například spřažení oblastí mezi klienty a střepem a tolerance zpoždění replikace. Pouze jedna globální služba musí být vytvořena pro čtení a zápis dat do nebo z databáze s oddíly. Při použití active data guard a nastavení jen pro čtení repliky šdrupy, můžete vytvořit jinou službu gGobal pro úlohy jen pro čtení. Klient může použít tyto globální služby pro připojení k databázi.

- **Shard databáze** - Shard databáze jsou databáze Oracle. Každá databáze je replikována pomocí služby Oracle Data Guard v konfiguraci brokera s povoleným funkcí FSFO (Fast-Start). Není nutné nastavit převzetí služeb při selhání služby Data Guard a replikaci na každém úlovku. To je automaticky nakonfigurována a nasazena při vytvoření sdílené databáze. Pokud se konkrétní úlomek nezdaří, služba Oracle Sharing automaticky převezme služby při selhání databázových připojení z primárního do pohotovostního režimu.

Databáze Oracle můžete nasadit a spravovat se dvěma rozhraními: Oracle Enterprise `GDSCTL` Manager Cloud Control GUI a/nebo nástroj příkazového řádku. Můžete dokonce sledovat různé úlomky pro dostupnost a výkon pomocí cloudového řízení. Příkaz `GDSCTL DEPLOY` automaticky vytvoří úlomky a jejich příslušné posluchače. Kromě toho tento příkaz automaticky nasadí konfiguraci replikace používanou pro vysokou dostupnost na úrovni střepu určenou správcem.

Existují různé způsoby, jak protřepávat databázi:

* Systémově spravované dělení – automaticky distribuuje mezi úlomky pomocí dělení
* Uživatelem definované meziříže - Umožňuje zadat mapování dat na úlomky, které funguje dobře, když existují regulační požadavky nebo požadavky na lokalizaci dat)
* Kompozitní střepování – kombinace systémově spravovaného a uživatelem definovaného síně pro různé _prostory sypu_
* Dílčí oddíly tabulky - Podobně jako běžná dělená tabulka.

Přečtěte si další informace o různých [metodách střepování](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-methods.html) v dokumentaci společnosti Oracle.

Zatímco rozdělená databáze může vypadat jako jedna databáze pro aplikace a vývojáře, při migraci z databáze bez oddílů na rozdělené databáze, pečlivé plánování je nutné určit, které tabulky budou duplikovány versus sříznuté. 

Duplicitní tabulky jsou uloženy na všech šdrupu, zatímco rozdělení tabulky jsou distribuovány mezi různé oddíly. Doporučujeme duplikovat malé a rozměrové tabulky a distribuovat/rozdělit tabulky faktů. Data lze načíst do vaší databáze s rozdělením pomocí katalogu střepů jako centrální koordinátor nebo spuštěním datového čerpadla na každém úlomku. Další informace o [migraci dat do databáze s velkým a roztřesenou databází](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-loading-data.html) naleznete v dokumentaci společnosti Oracle.

#### <a name="oracle-sharding-with-data-guard"></a>Oracle Sharding s ochranou dat

Oracle Data Guard lze použít pro sounění pomocí systémově spravované, uživatelem definované a složené metody síní.

Následující diagram je referenční architektura pro Oracle Sharding s Oracle Data Guard používá pro vysokou dostupnost každého úlomku. Diagram architektury znázorňuje _metodu složeného rážení_. Diagram architektury se pravděpodobně bude lišit pro aplikace s různými požadavky na lokalitu dat, vyrovnávání zatížení, vysokou dostupnost, zotavení po havárii atd. Oracle Horizontální hod umožňuje splnit tyto požadavky a škálovat horizontálně a efektivně tím, že poskytuje tyto možnosti. Podobnou architekturu lze dokonce nasadit pomocí oracle goldengate.

![Oracle Database Sharding pomocí zón dostupnosti s zprostředkovatelem ochrany dat - FSFO](./media/oracle-reference-architecture/oracledb_dg_sh_az.png)

Zatímco systémspravované rozdělení se spraje je nejjednodušší konfigurovat a spravovat, uživatelem definované rozdělení nebo složené rozdělení je vhodné pro scénáře, kde jsou data a aplikace geograficky distribuovány nebo ve scénářích, kde je třeba mít kontrolu nad replikací každého oddílu. 

V předchozí architektuře složené horizontálního dělení se používá ke geografické distribuci dat a horizontální škálování na kapacitu aplikačních vrstev. Složené synění je kombinací systémově spravovaného a uživatelem definovaného síně a poskytuje tak výhody obou metod. V předchozím scénáři data je nejprve oddíly mezi více mezery oddílu oddělené podle oblasti. Potom data je dále rozdělena podle konzistentní hash přes více oddílů v prostoru shardspace. Každý shardspace obsahuje více shardgroups. Každá skupina oddílů má více oddílů a je "jednotka" replikace, v tomto případě. Každá skupina shardgroup obsahuje všechna data v prostoru shardspace. Shardgroups A1 a B1 jsou primární shardgroups, zatímco shardgroups A2 a B2 jsou pohotovostní režimy. Můžete zvolit, aby jednotlivé úlomky byly jednotkou replikace, nikoli skupinou oddílů.

V předchozí architektuře gsm/shard ředitel je nasazen v každé zóně dostupnosti pro vysokou dostupnost. Doporučujeme nasadit alespoň jednoho gsm/shard ředitele pro datové centrum nebo oblast. Instance aplikačního serveru je navíc nasazena v každé zóně dostupnosti, která obsahuje skupinu shardgroup. Toto nastavení umožňuje aplikaci zachovat latenci mezi aplikačním serverem a databází/shardgroup nízkou. Pokud databáze selže, aplikační server ve stejné zóně jako pohotovostní databáze může zpracovávat požadavky po přechodu role databáze. Azure Application Gateway a ředitel střepů sledovat latence požadavku a odpovědi a požadavky na trasu odpovídajícím způsobem.

Z hlediska aplikace klientský systém provede požadavek na Azure Application Gateway (nebo jiné technologie vyrovnávání zatížení v Azure), který přesměruje požadavek do oblasti nejblíže ke klientovi. Azure Application Gateway také podporuje rychlé relace, takže všechny požadavky přicházející ze stejného klienta jsou směrovány na stejný aplikační server. Aplikační server používá sdružování připojení v ovladačích přístupu k datům. Tato funkce je k dispozici v ovladačích, jako je JDBC, ODP.NET, OCI atd. Ovladače mohou rozpoznat klíče s ráždí zadané jako součást požadavku. [Oracle Universal Connection Pool (UCP)](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/jjucp/ucp-database-sharding-overview.html) pro klienty JDBC může umožnit klientům aplikací než Oracle, jako jsou Apache Tomcat a IIS, pracovat s řešením Oracle Sharding. 

Během počátečního požadavku se aplikační server připojí k řediteli střepu ve své oblasti, aby získal informace o směrování pro oddíl, ke kterému musí být požadavek směrován. Na základě klíče stvrdosložek prošel, ředitel směruje aplikační server do příslušného oddílu. Aplikační server ukládá tyto informace do mezipaměti vytvořením mapy a pro následné požadavky obchází oddíl usměrňovače a směruje požadavky přímo do oddílu.

#### <a name="oracle-sharding-with-goldengate"></a>Oracle Sharding s GoldenGate

Následující diagram je referenční architektura pro Oracle Sharding s Oracle GoldenGate pro vysokou dostupnost v regionu každého úlomku. Na rozdíl od předchozí architektury tato architektura zobrazuje pouze vysokou dostupnost v rámci jedné oblasti Azure (více zón dostupnosti). Jeden by mohl nasadit s vysokou dostupností databáze s vysokou dostupností (podobně jako v předchozím příkladu) pomocí Oracle GoldenGate.

![Oracle Database Sharding pomocí zón dostupnosti s GoldenGate](./media/oracle-reference-architecture/oracledb_gg_sh_az.png)

Předchozí referenční architektura používá _systémem spravované_ období je schopen data rozdísnit. Vzhledem k tomu, že replikace Oracle GoldenGate se provádí na úrovni bloku dat, lze replikovat polovinu dat replikovaných do jednoho úlomku do jiného oddílu. Druhá polovina může být replikována na jiný úlomek. 

Způsob replikace dat závisí na faktor replikace. S faktorem replikace 2 budete mít dvě kopie každého bloku dat ve třech oddílech oddílů ve skupině symy. Podobně s replikačnífaktor 3 a tři úlomky ve skupině shardgroup, všechna data v každém oddílu budou replikovány do všech ostatních oddílů ve skupině shardgroup. Každý oddíl ve skupině shardgroup může mít jiný faktor replikace. Toto nastavení vám pomůže definovat vysokou dostupnost a návrh zotavení po havárii efektivně v rámci skupiny shardgroup a napříč více skupinami shardgroups.

V předchozí architektuře shardgroup A a shardgroup B oba obsahují stejná data, ale jsou umístěny v různých zónách dostupnosti. Pokud mají skupina shardgroup A i shardgroup B stejný replikační faktor 3, bude každý řádek nebo blok vaší rozdělené tabulky replikován 6krát ve dvou skupinách shardgroups. Pokud shardgroup A má replikační faktor 3 a shardgroup B má faktor replikace 2, každý řádek nebo blok bude replikován 5 krát přes dvě skupiny shardgroups.

Toto nastavení zabraňuje ztrátě dat, pokud dojde k selhání na úrovni instance nebo dostupnosti zóny. Aplikační vrstva je schopna číst z a zapisovat do každého úlomku. Chcete-li minimalizovat konflikty, Oracle Sharding určuje "hlavní blok bloku" pro každý rozsah hodnot hash. Tato funkce zajišťuje, že požadavky na zápisy pro konkrétní blok jsou směrovány na odpovídající blok. Oracle GoldenGate navíc poskytuje automatické zjišťování konfliktů a řešení pro zpracování všech konfliktů, které mohou vzniknout. Další informace a omezení implementace technologie GoldenGate s řešením Oracle Sharding naleznete v dokumentaci společnosti Oracle o používání [databáze Oracle GoldenGate s roztřesenou databází](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-high-availability.html#GUID-4FC0AC46-0B8B-4670-BBE4-052228492C72).

V předchozí architektuře gsm/shard ředitel je nasazen v každé zóně dostupnosti pro vysokou dostupnost. Doporučujeme nasadit alespoň jednoho gsm/shard ředitele pro datové centrum nebo oblast. Instance aplikačního serveru je navíc nasazena v každé zóně dostupnosti, která obsahuje skupinu shardgroup. Toto nastavení umožňuje aplikaci zachovat latenci mezi aplikačním serverem a databází/shardgroup nízkou. Pokud databáze selže, aplikační server ve stejné zóně jako pohotovostní databáze může zpracovávat požadavky po přechodu role databáze. Azure Application Gateway a ředitel střepů sledovat latence požadavku a odpovědi a požadavky na trasu odpovídajícím způsobem.

Z hlediska aplikace klientský systém provede požadavek na Azure Application Gateway (nebo jiné technologie vyrovnávání zatížení v Azure), který přesměruje požadavek do oblasti nejblíže ke klientovi. Azure Application Gateway také podporuje rychlé relace, takže všechny požadavky přicházející ze stejného klienta jsou směrovány na stejný aplikační server. Aplikační server používá sdružování připojení v ovladačích přístupu k datům. Tato funkce je k dispozici v ovladačích, jako je JDBC, ODP.NET, OCI atd. Ovladače mohou rozpoznat klíče s ráždí zadané jako součást požadavku. [Oracle Universal Connection Pool (UCP)](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/jjucp/ucp-database-sharding-overview.html) pro klienty JDBC může umožnit klientům aplikací než Oracle, jako jsou Apache Tomcat a IIS, pracovat s řešením Oracle Sharding. 

Během počátečního požadavku se aplikační server připojí k řediteli střepu ve své oblasti, aby získal informace o směrování pro oddíl, ke kterému musí být požadavek směrován. Na základě klíče stvrdosložek prošel, ředitel směruje aplikační server do příslušného oddílu. Aplikační server ukládá tyto informace do mezipaměti vytvořením mapy a pro následné požadavky obchází oddíl usměrňovače a směruje požadavky přímo do oddílu.

## <a name="patching-and-maintenance"></a>Opravy a údržba

Při nasazování úloh Oracle do Azure se Microsoft postará o všechny opravy na úrovni hostitelského operačního systému. Veškerá plánovaná údržba na úrovni operačního režimu je zákazníkům sdělena předem, aby zákazník mohl tuto plánovanou údržbu. Dva servery ze dvou různých zón dostupnosti nejsou nikdy záplatovány současně. Další informace o údržbě a opravách virtuálních počítačů najdete v tématu [Správa dostupnosti virtuálních počítačů.](../../../virtual-machines/linux/manage-availability.md) 

Oprava operačního systému virtuálního počítače lze automatizovat pomocí [Azure Automation](../../../automation/automation-tutorial-update-management.md). Opravy a údržbu databáze Oracle lze automatizovat a naplánovat pomocí [Azure Pipelines](/azure/devops/pipelines/get-started/what-is-azure-pipelines?view=azure-devops) nebo [Azure Automation,](../../../automation/automation-tutorial-update-management.md) abyste minimalizovali prostoje. Informace o tom, jak jej lze použít v kontextu databází Oracle, najdete v tématu [Průběžné doručování a modrozelená nasazení.](/azure/devops/learn/what-is-continuous-delivery)

## <a name="architecture-and-design-considerations"></a>Aspekty architektury a designu

- Zvažte použití [virtuálního počítače optimalizovaného](../../../virtual-machines/windows/sizes-memory.md) pro hyperthreaded paměť s [omezenými jádrovými virtuálními procesory](../../../virtual-machines/windows/constrained-vcpu.md) pro váš virtuální počítač Oracle Database, abyste ušetřili náklady na licencování a maximalizovali výkon. Výkon a dostupnost použijte více disků premium nebo ultra (spravované disky).
- Při použití spravovaných disků se název disku nebo zařízení může při restartování změnit. Doporučujeme použít zařízení UUID namísto názvu, abyste zajistili, že vaše připojení zůstanou přetrvávat po restartování. Více informací naleznete [zde](../../../virtual-machines/linux/configure-raid.md#add-the-new-file-system-to-etcfstab).
- Pomocí zón dostupnosti můžete dosáhnout vysoké dostupnosti v oblasti.
- Zvažte použití ultra disků (jsou-li k dispozici) nebo prémiových disků pro databázi Oracle.
- Zvažte nastavení pohotovostní databáze Oracle v jiné oblasti Azure pomocí služby Oracle Data Guard.
- Zvažte použití [skupin umístění bezkontaktní komunikace](../../../virtual-machines/linux/co-location.md#proximity-placement-groups) ke snížení latence mezi vaší aplikací a databázovou vrstvou.
- Nastavte [řešení Oracle Enterprise Manager](https://docs.oracle.com/en/enterprise-manager/) pro správu, monitorování a protokolování.
- Zvažte použití nástroje Oracle Automatic Storage Management (ASM) pro zjednodušenou správu úložišť pro vaši databázi.
- Azure [Pipelines](/azure/devops/pipelines/get-started/what-is-azure-pipelines) slouží ke správě oprav a aktualizací databáze bez prostojů.
- Vyladit kód aplikace přidat cloud nativní vzory, jako je [například opakování vzor](/azure/architecture/patterns/retry), [jistič vzor](/azure/architecture/patterns/circuit-breaker)a další vzory definované v [cloudu vzory návrhu průvodce,](/azure/architecture/patterns/) které mohou pomoci vaše aplikace být odolnější.

## <a name="next-steps"></a>Další kroky

Projděte si následující referenční články společnosti Oracle, které se vztahují k vašemu scénáři.

- [Úvod do služby Oracle Data Guard](https://docs.oracle.com/en/database/oracle/oracle-database/18/sbydb/introduction-to-oracle-data-guard-concepts.html#GUID-5E73667D-4A56-445E-911F-1E99092DD8D7)
- [Koncepty zprostředkovatelů Oracle Data Guard](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/dgbkr/oracle-data-guard-broker-concepts.html)
- [Konfigurace technologie Oracle GoldenGate pro vysokou dostupnost aktivní aktivní](https://docs.oracle.com/goldengate/1212/gg-winux/GWUAD/wu_bidirectional.htm#GWUAD282)
- [Přehled řešení Oracle Sharding](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-overview.html)
- [Oracle Active Data Guard daleko synchronizuje nulovou ztrátu dat na libovolnou vzdálenost](https://www.oracle.com/technetwork/database/availability/farsync-2267608.pdf)
