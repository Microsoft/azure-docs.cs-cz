---
title: Referenční architektury pro databáze Oracle v Azure | Microsoft Docs
description: Odkazuje na architektury pro spouštění Oracle Database Enterprise Edition databází v Microsoft Azure Virtual Machines.
author: dbakevlar
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.date: 12/13/2019
ms.author: kegorman
ms.reviewer: cynthn
ms.openlocfilehash: bbaf34c977546891c6ac05fbd4b5feb15f333e04
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2021
ms.locfileid: "98737808"
---
# <a name="reference-architectures-for-oracle-database-enterprise-edition-on-azure"></a>Referenční architektury pro Oracle Database Enterprise Edition v Azure

V této příručce najdete informace o nasazení databáze Oracle s vysokou dostupností v Azure. Kromě toho tato příručka komentáře na požadavky na zotavení po havárii. Tyto architektury se vytvořily na základě zákaznického nasazení. Tato příručka platí jenom pro Oracle Database Enterprise Edition.

Pokud vás zajímá více o maximalizaci výkonu vaší databáze Oracle, přečtěte si téma [architekt a Oracle DB](oracle-design.md).

## <a name="assumptions"></a>Předpoklady

- Rozumíte různým konceptům Azure, jako jsou [zóny dostupnosti](../../../availability-zones/az-overview.md) .
- Spouštíte Oracle Database Enterprise Edition 12c nebo novější.
- Víte, že máte na paměti vliv na licencování při používání řešení v tomto článku.

## <a name="high-availability-for-oracle-databases"></a>Vysoká dostupnost pro databáze Oracle

Dosažení vysoké dostupnosti v cloudu je důležitou součástí plánování a návrhu každé organizace. Microsoft Azure nabízí [zóny dostupnosti](../../../availability-zones/az-overview.md) a skupiny dostupnosti (budou použity v oblastech, kde jsou zóny dostupnosti k dispozici). Přečtěte si další informace o [správě dostupnosti virtuálních počítačů](../../manage-availability.md) pro návrh pro Cloud.

Kromě nativních nástrojů a nabídek cloudu poskytuje Oracle řešení pro vysokou dostupnost, jako je [Oracle data Guard](https://docs.oracle.com/en/database/oracle/oracle-database/18/sbydb/introduction-to-oracle-data-guard-concepts.html#GUID-5E73667D-4A56-445E-911F-1E99092DD8D7), [Ochrana dat s FSFO](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/dgbkr/index.html), [horizontálního dělení](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/admin/sharding-overview.html)a [GoldenGate](https://www.oracle.com/middleware/technologies/goldengate.html) , která se dají nastavit v Azure. Tento průvodce popisuje referenční architektury pro každé z těchto řešení.

Nakonec při migraci nebo vytváření aplikací pro Cloud je důležité upravit kód aplikace a přidat vzory nativní pro Cloud, jako je [vzor opakování](/azure/architecture/patterns/retry) a [vzor přerušení okruhu](/azure/architecture/patterns/circuit-breaker). Další vzory definované v [Průvodci návrhem cloudových vzorů](/azure/architecture/patterns/) můžou přispět k vyšší odolnosti vaší aplikace.

### <a name="oracle-rac-in-the-cloud"></a>Oracle RAC v cloudu

Oracle Real Application cluster (RAC) je řešení od Oracle, které zákazníkům umožňuje dosáhnout vysoké propustnosti tím, že má mnoho instancí přístup k jednomu úložišti databáze (Shared-All Pattern). I když se dá Oracle RAC použít i pro místní vysokou dostupnost, nedá se samotný Oracle RAC použít k zajištění vysoké dostupnosti v cloudu, protože chrání jenom proti selháním na úrovni instance a ne proti selháním na úrovni skříně nebo datového centra. Z tohoto důvodu Oracle doporučuje použít pro vysokou dostupnost databáze Oracle data Guard (bez ohledu na to, jestli jde o jedinou instanci nebo certifikát RAC). Zákazníci obecně vyžadují vysokou smlouvu SLA pro provozování důležitých aplikací. Oracle RAC není v současné době v Azure certifikovaný ani podporovaný. Azure ale nabízí funkce, jako je Azure, nabízí Zóny dostupnosti a plánovaná časová období údržby, která chrání před selháním na úrovni instance. Kromě toho můžou zákazníci využívat technologie, jako je Oracle data Guard, Oracle GoldenGate a Oracle horizontálního dělení pro zajištění vysokého výkonu a odolnosti díky ochraně jejich databází před rackem a i geograficky neznámým selháním.

Při spouštění databází Oracle v různých [zónách dostupnosti](../../../availability-zones/az-overview.md) ve spojení s Oracle data Guard nebo GoldenGate můžou zákazníci získat smlouvu SLA o provozu 99,99%. V oblastech Azure, kde se ještě nevyskytují zóny dostupnosti, můžou zákazníci používat [skupiny dostupnosti](../../manage-availability.md#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy) a získat smlouvu SLA pro dobu provozu 99,95%.

>Poznámka: je možné, že máte cíl provozu, který je mnohem vyšší než smlouva SLA pro dobu provozu poskytovanou společností Microsoft.

## <a name="disaster-recovery-for-oracle-databases"></a>Zotavení po havárii pro databáze Oracle

Při hostování důležitých podnikových aplikací v cloudu je důležité navrhovat pro vysokou dostupnost a zotavení po havárii.

Pro Oracle Database Enterprise Edition je Oracle data Guard užitečnou funkcí pro zotavení po havárii. Můžete nastavit pohotovostní instanci databáze v [spárované oblasti Azure](../../../best-practices-availability-paired-regions.md) a nastavit převzetí služeb při selhání pro ochranu dat pro zotavení po havárii. U nulových ztrát dat doporučujeme kromě aktivní ochrany dat použít i nasazovat instanci Oracle data Guard Far Sync. 

Zvažte nastavení instance data Guard Far Sync v jiné zóně dostupnosti než v primární databázi Oracle, pokud vaše aplikace povoluje latenci (vyžaduje se důkladné testování). Použijte režim **maximální dostupnosti** k nastavení synchronního přenosu vašich souborů opětovného navýšení na instanci synchronizace. Tyto soubory se pak přenesou asynchronně do pohotovostní databáze. 

Pokud vaše aplikace nedovolí ztrátě výkonu při nastavování daleko synchronizované instance v jiné zóně dostupnosti v režimu **maximální dostupnosti** (synchronní), můžete nastavit skupinu ve stejné zóně dostupnosti jako primární databázi. V případě, že máte k dispozici dostupnost, zvažte nastavení více instancí s úzkým synchronním přechodem na primární databázi a alespoň jednu instanci blízko do pohotovostní databáze (Pokud se role přecházejí). Přečtěte si další informace o Oracle data Guard – Far Sync v tomto dokumentu [White paper pro Oracle Active Data Guard – na nejvyšší synchronizaci](https://www.oracle.com/technetwork/database/availability/farsync-2267608.pdf).

Při používání databází Oracle Standard Edition jsou k dispozici řešení ISV, jako je například pohotovostní DBVisit, které vám umožní nastavit vysokou dostupnost a zotavení po havárii.

## <a name="reference-architectures"></a>Referenční architektury

### <a name="oracle-data-guard"></a>Oracle Data Guard

Oracle data Guard zajišťuje vysokou dostupnost, ochranu dat a zotavení po havárii pro podniková data. Ochrana dat uchovává pohotovostní databáze jako transakční konzistentní kopie primární databáze. V závislosti na vzdálenosti mezi primárními a sekundárními databázemi a odolností aplikace pro latenci můžete nastavit synchronní nebo asynchronní replikaci. Pokud je primární databáze nedostupná z důvodu plánovaného nebo neplánovaného výpadku, může ochrana dat přepnout všechny pohotovostní databáze na primární roli a minimalizovat prostoje. 

Pokud používáte Oracle data Guard, můžete také otevřít sekundární databázi pro účely jen pro čtení. Tato konfigurace se nazývá aktivní ochrana dat. Oracle Database 12c zavedl funkci nazvanou data Guard – instance synchronizace. Tato instance umožňuje nastavit nulovou konfiguraci ztráty dat vaší databáze Oracle bez nutnosti narušit výkon.

> [!NOTE]
> Aktivní ochrana dat vyžaduje další licencování. Tato licence je také nutná k použití funkce Far Sync. Připojte se k vašemu zástupci Oracle a proberte případné důsledky k licencování.

#### <a name="oracle-data-guard-with-fsfo"></a>Oracle data Guard s FSFO
Oracle data Guard s Fast-Start převzetí služeb při selhání (FSFO) může poskytovat další odolnost tím, že na samostatném počítači Nastaví zprostředkovatele. Zprostředkovatel ochrany dat a sekundární databáze spouštějí pozorovatele a sledují primární databázi za výpadky. To umožňuje také redundanci v instalaci pozorovatele data Guard. 

U Oracle Database verze 12,2 a vyšší je také možné nakonfigurovat více pozorovatelů s jednou konfigurací zprostředkovatele ochrany dat Oracle. Tato instalace poskytuje dodatečnou dostupnost v případě výpadku jednoho pozorovatele a sekundárního databázového prostředí. Zprostředkovatel ochrany dat je odlehčený a může být hostovaný na relativně malém virtuálním počítači. Další informace o zprostředkovateli ochrany dat a jeho výhodách najdete v [dokumentaci k Oracle](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/dgbkr/oracle-data-guard-broker-concepts.html) v tomto tématu.

Následující diagram je doporučená architektura pro používání ochrany dat Oracle v Azure se zónami dostupnosti. Tato architektura umožňuje získat smlouvu SLA pro dobu provozu virtuálního počítače 99,99%.

![Diagram, který zobrazuje doporučenou architekturu pro používání ochrany dat Oracle v Azure se zónami dostupnosti.](./media/oracle-reference-architecture/oracledb_dg_fsfo_az.png)

V předchozím diagramu klientský systém přistupuje k vlastní aplikaci pomocí back-endu Oracle přes web. Webový front-end je nakonfigurovaný v nástroji pro vyrovnávání zatížení. Webový front-end provede volání příslušného aplikačního serveru za účelem zpracování práce. Aplikační server se dotazuje na primární databázi Oracle. Databáze Oracle byla nakonfigurovaná pomocí [virtuálního počítače optimalizovaného pro paměť](../../sizes-memory.md) ve vlákně s [omezeným jádrem vCPU](../../../virtual-machines/constrained-vcpu.md) , který šetří náklady na licencování a maximalizuje výkon. Pro výkon a vysokou dostupnost se používá několik disků Premium nebo Ultra (Managed Disks).

Databáze Oracle jsou umístěné v několika zónách dostupnosti pro zajištění vysoké dostupnosti. Každá zóna se skládá z jednoho nebo více datových center vybavených nezávislým napájením, chlazením a sítí. Aby se zajistila odolnost, ve všech povolených oblastech se nastaví minimálně tři samostatné zóny. Fyzické oddělení zón dostupnosti v rámci oblasti chrání data před selháními datových center. Kromě toho se dva FSFO pozorovatelé nastavují ve dvou zónách dostupnosti, aby se při výpadku databáze nastavily a přestaly při selhání. 

Další pozorovatele nebo pohotovostní databáze můžete nastavit v jiné zóně dostupnosti (AZ 1, v tomto případě), než je zóna uvedená v předchozí architektuře. Nakonec se databáze Oracle monitorují pro dobu provozu a výkon pomocí Oracle Enterprise Manageru (OEM). Výrobce OEM také umožňuje vygenerovat různé sestavy o výkonu a využití.

V oblastech, kde se zóny dostupnosti nepodporují, můžete použít skupiny dostupnosti k nasazení Oracle Database vysoce dostupným způsobem. Skupiny dostupnosti umožňují dosáhnout doby provozu virtuálního počítače 99,95%. Následující diagram je referenční architektura tohoto použití:

![Oracle Database používání skupin dostupnosti s zprostředkovatelem data Guard – FSFO](./media/oracle-reference-architecture/oracledb_dg_fsfo_as.png)

> [!NOTE]
> * Virtuální počítač Oracle Enterprise Manageru není potřeba umístit do skupiny dostupnosti, protože je nasazená jenom jedna instance OEM.
> * V konfiguraci skupiny dostupnosti se aktuálně nepodporují disky Ultra.

#### <a name="oracle-data-guard-far-sync"></a>Oracle data Guard – Far synchronizace

Oracle data Guard – Far Synchronization poskytuje pro databáze Oracle nulovou možnost ochrany před únikem informací. Tato funkce umožňuje chránit před ztrátou dat v případě, že se databázový počítač nezdařil. Oracle data Guard – Far Sync se musí nainstalovat na samostatný virtuální počítač. Odlehčená synchronizace je odlehčená instance Oracle, která má pouze řídicí soubor, soubor hesla, SPFile a pohotovostní protokoly. Neexistují žádné datové soubory ani soubory protokolu Rego. 

Pro zajištění nulové ochrany před únikem informací musí existovat synchronní komunikace mezi vaší primární databází a instancí daleko synchronizace. Instance s nastarší synchronizací přijímá v synchronním režimu opakování z primárního serveru a okamžitě ho přesměruje do všech záložních databází. Tato instalace také snižuje režijní náklady na primární databázi, protože je nutné pouze odeslat znovu do instance Far synchronizace, nikoli pro všechny pohotovostní databáze. Pokud se neúspěšná instance synchronizace nezdařila, ochrana dat automaticky používá asynchronní přenos do sekundární databáze z primární databáze za účelem zachování téměř nulové ochrany před únikem informací. Pro zvýšení odolnosti můžou zákazníci nasazovat víc instancí služby Sync na každou instanci databáze (primární a sekundární).

Následující diagram představuje architekturu s vysokou dostupností s využitím nástroje Oracle data Guard – Far Sync:

![Databáze Oracle využívající zóny dostupnosti s data Guard – & Broker – FSFO](./media/oracle-reference-architecture/oracledb_dg_fs_az.png)

V předchozí architektuře je k dispozici daleko synchronizovaná instance, která je nasazená ve stejné zóně dostupnosti jako instance databáze, aby se snížila latence mezi nimi. V případech, kdy je aplikace citlivá na latenci, zvažte nasazení databáze a daleko synchronizovaných instancí nebo instancí ve [skupině umístění blízkosti](../../../virtual-machines/linux/proximity-placement-groups.md).

Následující diagram je architektura, která využívá Oracle data Guard FSFO a Far Sync k zajištění vysoké dostupnosti a zotavení po havárii:

![Oracle Database použití zón dostupnosti pro zotavení po havárii s nástrojem data Guard – & Broker – FSFO](./media/oracle-reference-architecture/oracledb_dg_fs_az_dr.png)

### <a name="oracle-goldengate"></a>Oracle GoldenGate

GoldenGate umožňuje výměnu a manipulaci s daty na úrovni transakcí mezi několika heterogenními platformami v celém podniku. Přesouvá potvrzené transakce s integritou transakcí a minimální režií na stávající infrastruktuře. Jeho modulární architektura poskytuje flexibilitu pro extrakci a replikaci vybraných záznamů dat, transakčních změn a změny v jazyce DDL (Data Definition Language) v nejrůznějších topologiích.

Oracle GoldenGate umožňuje konfiguraci databáze pro zajištění vysoké dostupnosti díky obousměrné replikaci. To vám umožní nastavit konfiguraci s **více hlavními servery** nebo **aktivní-aktivní**. Následující diagram je doporučovanou architekturou pro Oracle GoldenGate Active-Active Setup v Azure. V následující architektuře byla databáze Oracle nakonfigurovaná pomocí [virtuálního počítače optimalizovaného pro paměť](../../sizes-memory.md) ve vlákně s [omezeným jádrem vCPU](../../../virtual-machines/constrained-vcpu.md) , který šetří náklady na licencování a maximalizuje výkon. K výkonu a dostupnosti se používá několik disků Premium nebo Ultra (spravované disky).

![Oracle Database použití zón dostupnosti s zprostředkovatelem ochrany dat – FSFO](./media/oracle-reference-architecture/oracledb_gg_az.png)

> [!NOTE]
> Podobnou architekturu je možné nastavit pomocí skupin dostupnosti v oblastech, kde zóny dostupnosti nejsou aktuálně dostupné.

Oracle GoldenGate má procesy, jako je například extrakce, pumpa a replikace, které vám pomůžou asynchronně replikovat data z jednoho databázového serveru Oracle do jiného. Tyto procesy umožňují nastavit obousměrnou replikaci, aby se zajistila vysoká dostupnost databáze v případě výpadku na úrovni zóny dostupnosti. V předchozím diagramu proces extrakce běží na stejném serveru jako vaše databáze Oracle, zatímco procesy datového čerpadla a replikace se spouštějí na samostatném serveru ve stejné zóně dostupnosti. Proces replikace slouží k přijímání dat z databáze v jiné zóně dostupnosti a k potvrzení dat do databáze Oracle v příslušné zóně dostupnosti. Podobně proces datového čerpadla odesílá data extrahovaná procesem extrakce do procesu replikace v jiné zóně dostupnosti. 

I když v předchozím diagramu architektury vidíte proces datového čerpadla a replikace nakonfigurovaný na samostatném serveru, můžete na stejném serveru nastavit všechny procesy Oracle GoldenGate na základě kapacity a využití vašeho serveru. Pro pochopení způsobu použití serveru si vždy Projděte sestavu AWR a metriky v Azure.

Při nastavování obousměrné replikace Oracle GoldenGate v různých zónách dostupnosti nebo v různých oblastech je důležité zajistit, aby byla latence mezi různými součástmi pro vaši aplikaci přijatelná. Latence mezi zónami a oblastmi dostupnosti se může lišit a závisí na několika faktorech. Doporučuje se nastavit testy výkonu mezi aplikační vrstvou a vaší databázovou vrstvou v různých zónách dostupnosti a/nebo oblastech, abyste ověřili, že vyhovují vašim požadavkům na výkon vaší aplikace.

Aplikační vrstvu lze nastavit ve vlastní podsíti a databázová vrstva může být rozdělena do vlastní podsítě. Pokud je to možné, zvažte použití [Azure Application Gateway](../../../application-gateway/overview.md) k vyrovnávání zatížení provozu mezi aplikačními servery. Azure Application Gateway je robustní nástroj pro vyrovnávání zatížení webového provozu. Poskytuje spřažení relací na základě souborů cookie, které udržuje relaci uživatele na stejném serveru, čímž minimalizuje konflikty v databázi. K Application Gateway jsou [Azure Load Balancer](../../../load-balancer/load-balancer-overview.md) a [Azure Traffic Manager](../../../traffic-manager/traffic-manager-overview.md).

### <a name="oracle-sharding"></a>Oracle horizontálního dělení

Horizontálního dělení je vzor datové vrstvy, který byl představený v Oracle 12,2. Umožňuje horizontálně rozdělit a škálovat data napříč nezávislými databázemi. Nejedná se o architekturu bez sdílené složky, ve které je každá databáze hostovaná na vyhrazeném virtuálním počítači, což umožňuje vysokou propustnost čtení a zápisu kromě odolnosti a zvýšené dostupnosti. Tento model eliminuje jednotlivé body selhání, zajišťuje izolaci chyb a umožňuje provádět průběžné upgrady bez výpadků. Výpadek jednoho horizontálních oddílů nebo selhání na úrovni datového centra nemá vliv na výkon ani dostupnost ostatních horizontálních oddílů v jiných datových centrech. 

Horizontálního dělení je vhodný pro aplikace s vysokou propustností OLTP, které nemůžou dovolit žádné výpadky. Všechny řádky se stejným klíčem horizontálního dělení jsou vždycky zaručené na stejném horizontálních oddílů, což zvyšuje výkon, který zajišťuje vysokou konzistenci. Aplikace, které používají horizontálního dělení, musí mít dobře definovaný datový model a strategii distribuce dat (konzistentní hodnota hash, rozsah, seznam nebo složený), která primárně přistupuje k datům pomocí horizontálního dělení klíče (například *customerId* nebo *accountNum*). Horizontálního dělení také umožňuje ukládat konkrétní sady dat blíže koncovým zákazníkům, což vám pomůže splnit požadavky na výkon a dodržování předpisů.

Doporučuje se replikovat horizontálních oddílů pro zajištění vysoké dostupnosti a zotavení po havárii. Tuto instalaci můžete provést pomocí technologie Oracle, jako je Oracle data Guard nebo Oracle GoldenGate. Jednotkou replikace může být horizontálních oddílů, součást horizontálních oddílů nebo skupina horizontálních oddílů. Dostupnost databáze horizontálně dělené není ovlivněná výpadkem ani zpomalením jednoho nebo více horizontálních oddílů. Pro zajištění vysoké dostupnosti je možné pohotovostní horizontálních oddílů umístit do stejné zóny dostupnosti, kde je umístěn primární horizontálních oddílů. V případě zotavení po havárii může být pohotovostní horizontálních oddílů umístěno v jiné oblasti. Pro obsluhu provozu v těchto oblastech můžete také nasadit horizontálních oddílů v několika oblastech. Přečtěte si další informace o konfiguraci vysoké dostupnosti a replikaci databáze horizontálně dělené v [dokumentaci Oracle horizontálního dělení](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-high-availability.html).

Oracle horizontálního dělení se primárně skládá z následujících součástí. Další informace o těchto součástech najdete v [dokumentaci k Oracle horizontálního dělení](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-overview.html):

- **Horizontálních oddílů Catalog** – speciální databáze Oracle pro zvláštní účely, která je trvalé úložiště pro všechna data konfigurace databáze horizontálních oddílů. Všechny změny konfigurace, jako je přidání nebo odebrání horizontálních oddílů, mapování dat a DDLs v databázi horizontálních oddílů, se iniciují v katalogu horizontálních oddílů. Katalog horizontálních oddílů obsahuje také hlavní kopii všech duplicitních tabulek v SDB. 

  Katalog horizontálních oddílů používá materializovaná zobrazení k automatické replikaci změn do duplikovaných tabulek ve všech horizontálních oddílů. Databáze katalogu horizontálních oddílů funguje taky jako koordinátor dotazů, který se používá ke zpracování dotazů a dotazů multi-horizontálních oddílů, které neurčují klíč horizontálního dělení. 
  
  Použití ochrany dat Oracle ve spojení se zónami dostupnosti nebo skupinami dostupnosti pro vysokou dostupnost katalogu horizontálních oddílů je doporučeným osvědčeným postupem. Dostupnost katalogu horizontálních oddílů nemá žádný vliv na dostupnost databáze horizontálně dělené. Výpadek katalogu horizontálních oddílů má vliv jenom na operace údržby a dotazy multishard v krátké době, kdy se převzetí služeb při selhání dokončí. Online transakce budou i nadále směrovány a spouštěny v rámci SDB a nejsou ovlivněny výpadkem katalogu.

- **Horizontálních oddílů ředitelé** – odlehčené služby, které je potřeba nasadit v každé oblasti nebo zóně dostupnosti, ve které se nachází vaše horizontálních oddílů. Horizontálních oddílů ředitelé mají v kontextu Oracle horizontálního dělení nasazené globální správce služeb. Pro zajištění vysoké dostupnosti doporučujeme nasadit alespoň jednoho horizontálních oddílů ředitele v každé zóně dostupnosti, ve které horizontálních oddílů existuje. 

  Při prvním připojení k databázi se informace o směrování nastaví pomocí horizontálních oddílů ředitele a ukládají se do mezipaměti pro následné žádosti a obcházejí horizontálních oddílůho ředitele. Po vytvoření relace s horizontálních oddílů jsou všechny dotazy a DML SQL podporovány a spuštěny v rozsahu daného horizontálních oddílů. Toto směrování je rychlé a používá se pro všechny OLTP úlohy, které provádějí transakce uvnitř horizontálních oddílů. Doporučuje se používat přímé směrování pro všechny OLTP úlohy, které vyžadují nejvyšší výkon a dostupnost. Mezipaměť směrování se automaticky aktualizuje, když se horizontálních oddílů změní na nedostupný nebo dojde ke změnám topologie horizontálního dělení. 
  
  Pro vysoce výkonné směrování závislé na datech Oracle doporučuje při přístupu k datům v databázi horizontálně dělené použít fond připojení. Fondy připojení Oracle, knihovny specifické pro jazyk a ovladače podporují Oracle horizontálního dělení. Další podrobnosti najdete v [dokumentaci k Oracle horizontálního dělení](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-overview.html#GUID-3D41F762-BE04-486D-8018-C7A210D809F9) .

- **Globální služba** – služba Global Service je podobná běžné databázové službě. Kromě všech vlastností databázové služby má globální služba vlastnosti pro databáze horizontálně dělené, jako je spřažení oblastí mezi klienty a horizontálních oddílů a tolerance prodlevy replikace. Pro čtení a zápis dat do/z databáze horizontálně dělené je potřeba vytvořit jenom jednu globální službu. Při použití Active Data Guard a nastavení replik horizontálních oddílů jen pro čtení můžete vytvořit další službu gGobal pro úlohy jen pro čtení. Klient může tyto globální služby používat pro připojení k databázi.

- Databáze **horizontálních oddílů** – databáze horizontálních oddílů jsou databáze Oracle. Každá databáze se replikuje pomocí Oracle data Guard v konfiguraci zprostředkovatele s povoleným Fast-Start převzetí služeb při selhání (FSFO). Pro každou horizontálních oddílů není nutné nastavovat převzetí služeb při selhání ochrany dat a replikaci. Tato konfigurace se automaticky nakonfiguruje a nasadí při vytvoření sdílené databáze. Pokud se konkrétní horizontálních oddílů nepovede, sdílení Oracle při převzetí služeb při selhání připojení databáze z primárního serveru do úsporného režimu automaticky nefunguje.

Databáze Oracle horizontálně dělené můžete nasadit a spravovat pomocí dvou rozhraní: Oracle Enterprise Manager Cloud Control GUI nebo `GDSCTL` Nástroj příkazového řádku. Můžete dokonce monitorovat různé horizontálních oddílů pro dostupnost a výkon pomocí řízení cloudu. `GDSCTL DEPLOY`Příkaz automaticky vytvoří horizontálních oddílů a jejich příslušné naslouchací procesy. Tento příkaz kromě toho automaticky nasadí konfiguraci replikace použitou pro vysokou dostupnost na úrovni horizontálních oddílů určenou správcem.

Existují různé způsoby, jak horizontálních oddílů databázi:

* Horizontálního dělení spravovaná systémem – automaticky distribuuje mezi horizontálních oddílů pomocí dělení.
* Uživatelsky definované horizontálního dělení – umožňuje určit mapování dat na horizontálních oddílů, které funguje i v případě, že existují regulativní předpisy nebo požadavky na lokalizaci dat.)
* Kompozitní horizontálního dělení – kombinace systému spravovaného systémem a uživatelsky definované horizontálního dělení pro různé _shardspaces_
* Pododdíly tabulky – podobné jako u běžné dělené tabulky.

Přečtěte si další informace o různých [metodách horizontálního dělení](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-methods.html) v dokumentaci Oracle.

I když může databáze horizontálně dělené vypadat jako jediná databáze pro aplikace a vývojáře při migraci z jiné databáze než horizontálně dělené do databáze horizontálně dělené, je potřeba pečlivé plánování, které určuje, které tabulky budou duplikovány versus horizontálně dělené. 

Duplicitní tabulky jsou uloženy ve všech horizontálních oddílů, zatímco tabulky horizontálně dělené jsou distribuovány napříč různými horizontálních oddílů. Doporučení je duplikovat malé a dimenzionální tabulky a distribuovat/horizontálních oddílů tabulky faktů. Data se dají do databáze horizontálně dělené načíst pomocí katalogu horizontálních oddílů jako centrálního koordinátora nebo spuštěním datového čerpadla na každém horizontálních oddílů. Přečtěte si další informace o [migraci dat do databáze horizontálně dělené](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-loading-data.html) v dokumentaci Oracle.

#### <a name="oracle-sharding-with-data-guard"></a>Oracle horizontálního dělení s ochranou dat

Oracle data Guard se dá použít pro horizontálního dělení se systémem spravované, uživatelsky definovanými a složenými horizontálního dělení metodami.

Následující diagram je referenční architektura pro Oracle horizontálního dělení s ochranou dat Oracle, která se používá pro vysokou dostupnost každého horizontálních oddílů. Diagram architektury znázorňuje _složenou metodu horizontálního dělení_. Diagram architektury se pravděpodobně liší pro aplikace s různými požadavky na národní prostředí dat, Vyrovnávání zatížení, vysokou dostupnost, zotavení po havárii atd. a může používat jinou metodu pro horizontálního dělení. Oracle horizontálního dělení vám umožňuje tyto požadavky splnit a vodorovně a efektivně škálovat tím, že poskytuje tyto možnosti. Podobnou architekturu můžete nasadit i pomocí Oracle GoldenGate.

![Oracle Database horizontálního dělení pomocí zón dostupnosti s zprostředkovatelem data Guard – FSFO](./media/oracle-reference-architecture/oracledb_dg_sh_az.png)

I když je systémově spravovaný horizontálního dělení nejjednodušší pro konfiguraci a správu, uživatelsky definované horizontálního dělení nebo kompozitní horizontálního dělení je vhodné pro scénáře, ve kterých jsou vaše data a aplikace geograficky distribuované, nebo ve scénářích, kdy potřebujete mít kontrolu nad replikací každého horizontálních oddílů. 

V předchozí architektuře se složené horizontálního dělení používají k geografické distribuci dat a horizontálnímu škálování vrstev aplikací. Složený horizontálního dělení je kombinací systému a uživatelsky definovaných horizontálního dělení a přináší tak výhody obou metod. V předchozím scénáři se data nejprve horizontálně dělené napříč několika shardspaces oddělenými podle oblasti. Pak jsou data dále rozdělena podle konzistentní hodnoty hash napříč více horizontálních oddílů v shardspace. Každý shardspace obsahuje několik shardgroups. Každý shardgroup má několik horizontálních oddílů a v tomto případě je "jednotkou" replikace. Každý shardgroup obsahuje všechna data v shardspace. Shardgroups a1 a B1 jsou primární Shardgroups, zatímco Shardgroups a2 a B2 jsou pohotovostní. Můžete zvolit, aby jednotlivé horizontálních oddílů byly jednotkou replikace, nikoli shardgroup.

V předchozí architektuře je ředitel GSM/horizontálních oddílů nasazený v každé zóně dostupnosti pro zajištění vysoké dostupnosti. Doporučením je nasadit alespoň jednoho ředitele GSM/horizontálních oddílů na datové centrum/oblast. Kromě toho je instance aplikačního serveru nasazena v každé zóně dostupnosti, která obsahuje shardgroup. Tato instalace umožňuje aplikaci zachovat latenci mezi aplikačním serverem a databází/shardgroup. Pokud databáze dojde k chybě, aplikační server ve stejné zóně jako pohotovostní databáze může zpracovávat požadavky, jakmile dojde k přechodu databázové role. Azure Application Gateway a ředitel horizontálních oddílů udržují odpovídající požadavky na latenci a odezvu a požadavky na směrování.

Z hlediska aplikace vytvoří klientský systém požadavek na službu Azure Application Gateway (nebo jiné technologie Vyrovnávání zatížení v Azure), která požadavek přesměruje na oblast nejbližší klientovi. Azure Application Gateway podporuje také rychlé relace, takže všechny požadavky přicházející ze stejného klienta jsou směrovány na stejný aplikační server. Aplikační server používá sdružování připojení v ovladačích pro přístup k datům. Tato funkce je k dispozici v ovladačích, jako je JDBC, ODP.NET, OCI atd. Ovladače můžou rozpoznat klíče horizontálního dělení zadané v rámci žádosti. [Univerzální fond připojení Oracle (UCP)](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/jjucp/ucp-database-sharding-overview.html) pro klienty JDBC může povolit, aby klienti aplikací od jiných výrobců, jako je Apache Tomcat a služba IIS, pracovali s Oracle horizontálního dělení. 

Během počátečního požadavku se aplikační server připojí k horizontálních oddílů řediteli ve své oblasti a získá informace o směrování pro horizontálních oddílů, na které je potřeba směrovat požadavek. Na základě předaného klíče horizontálního dělení ředitel směruje aplikační server na příslušný horizontálních oddílů. Aplikační server ukládá tyto informace do mezipaměti tím, že vytvoří mapu a pro následné požadavky vynechá horizontálních oddílů ředitel a směruje požadavky přímo na horizontálních oddílů.

#### <a name="oracle-sharding-with-goldengate"></a>Oracle horizontálního dělení s GoldenGate

Následující diagram je referenční architektura pro Oracle horizontálního dělení s Oracle GoldenGate pro vysokou dostupnost každého horizontálních oddílů. Na rozdíl od předchozí architektury se tato architektura portrays jenom vysokou dostupností v rámci jedné oblasti Azure (několik zón dostupnosti). Jedna z nich by mohla nasazovat horizontálně dělené databázi s vysokou dostupností (podobně jako v předchozím příkladu) pomocí Oracle GoldenGate.

![Oracle Database horizontálního dělení pomocí zón dostupnosti s GoldenGate](./media/oracle-reference-architecture/oracledb_gg_sh_az.png)

Předchozí referenční architektura k horizontálních oddílůí dat používá metodu horizontálního dělení _spravovanou systémem_ . Vzhledem k tomu, že se replikace Oracle GoldenGate provádí na úrovni bloku, je možné replikovat data replikovaná na jednu horizontálních oddílů do jiného horizontálních oddílů. Druhou polovinu lze replikovat do jiného horizontálních oddílů. 

Způsob replikace dat závisí na faktoru replikace. Pomocí faktoru replikace 2 budete mít v shardgroup dvě kopie každého bloku dat v rámci tří horizontálních oddílů. Podobně jako faktor replikace 3 a tři horizontálních oddílů ve vaší shardgroup budou všechna data v každém horizontálních oddílů replikována do všech ostatních horizontálních oddílů v shardgroup. Každý horizontálních oddílů v shardgroup může mít jiný faktor replikace. Tato instalace vám pomůže s efektivním definováním vysoké dostupnosti a návrhu zotavení po havárii v rámci shardgroup a napříč několika shardgroups.

V předchozí architektuře shardgroup a a shardgroup B obsahují stejná data, ale nacházejí se v různých zónách dostupnosti. Pokud má shardgroup a a shardgroup B stejný faktor replikace jako 3, každý řádek nebo blok tabulky horizontálně dělené se bude replikovat 6 časů napříč dvěma shardgroups. Pokud shardgroup A má faktor replikace 3 a shardgroup B má faktor replikace 2, každý řádek nebo blok dat se bude replikovat 5 časů na dva shardgroups.

Tato instalace zabrání ztrátě dat, pokud dojde k selhání na úrovni instance nebo zóny dostupnosti. Aplikační vrstva dokáže číst a zapisovat do každého horizontálních oddílů. Pro minimalizaci konfliktů určuje Oracle horizontálního dělení "hlavní blok" pro každou škálu hodnot hash. Tato funkce zajišťuje, aby byly požadavky na konkrétní blok směrovány na odpovídající blok. Kromě toho Oracle GoldenGate poskytuje automatické zjišťování konfliktů a řešení pro zpracování případných konfliktů, ke kterým může dojít. Další informace a omezení implementace GoldenGate pomocí Oracle horizontálního dělení najdete v dokumentaci Oracle věnované použití [Oracle GoldenGate s databází horizontálně dělené](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-high-availability.html#GUID-4FC0AC46-0B8B-4670-BBE4-052228492C72).

V předchozí architektuře je ředitel GSM/horizontálních oddílů nasazený v každé zóně dostupnosti pro zajištění vysoké dostupnosti. Doporučením je nasadit alespoň jednoho ředitele GSM/horizontálních oddílů pro každé datové centrum nebo oblast. Kromě toho je instance aplikačního serveru nasazena v každé zóně dostupnosti, která obsahuje shardgroup. Tato instalace umožňuje aplikaci zachovat latenci mezi aplikačním serverem a databází/shardgroup. Pokud databáze dojde k chybě, aplikační server ve stejné zóně jako pohotovostní databáze může zpracovávat požadavky po přechodu role databáze. Azure Application Gateway a ředitel horizontálních oddílů udržují odpovídající požadavky na latenci a odezvu a požadavky na směrování.

Z hlediska aplikace vytvoří klientský systém požadavek na službu Azure Application Gateway (nebo jiné technologie Vyrovnávání zatížení v Azure), která požadavek přesměruje na oblast nejbližší klientovi. Azure Application Gateway podporuje také rychlé relace, takže všechny požadavky přicházející ze stejného klienta jsou směrovány na stejný aplikační server. Aplikační server používá sdružování připojení v ovladačích pro přístup k datům. Tato funkce je k dispozici v ovladačích, jako je JDBC, ODP.NET, OCI atd. Ovladače můžou rozpoznat klíče horizontálního dělení zadané v rámci žádosti. [Univerzální fond připojení Oracle (UCP)](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/jjucp/ucp-database-sharding-overview.html) pro klienty JDBC může povolit, aby klienti aplikací od jiných výrobců, jako je Apache Tomcat a služba IIS, pracovali s Oracle horizontálního dělení. 

Během počátečního požadavku se aplikační server připojí k horizontálních oddílů řediteli ve své oblasti a získá informace o směrování pro horizontálních oddílů, na které je potřeba směrovat požadavek. Na základě předaného klíče horizontálního dělení ředitel směruje aplikační server na příslušný horizontálních oddílů. Aplikační server ukládá tyto informace do mezipaměti tím, že vytvoří mapu a pro následné požadavky vynechá horizontálních oddílů ředitel a směruje požadavky přímo na horizontálních oddílů.

## <a name="patching-and-maintenance"></a>Opravy a údržba

Při nasazování úloh Oracle do Azure se společnost Microsoft postará o všechny opravy na úrovni operačního systému hostitele. Veškerá plánovaná údržba na úrovni operačního systému je zákazníkům předem sdělena, aby umožnila této plánované údržbě zákazníkům. Dva servery ze dvou různých Zóny dostupnosti se nikdy neopravují současně. Další informace o údržbě virtuálních počítačů a opravách najdete v tématu [Správa dostupnosti virtuálních počítačů](../../manage-availability.md) . 

Opravy operačního systému virtuálního počítače můžete automatizovat pomocí [Azure Automation Update Management](../../../automation/update-management/overview.md). Oprava a údržba databáze Oracle může být automatizovaná a naplánovaná pomocí [Azure Pipelines](/azure/devops/pipelines/get-started/what-is-azure-pipelines) nebo [Azure Automation Update Management](../../../automation/update-management/overview.md) k minimalizaci prostojů. Podívejte se na [průběžné doručování a modré/zelené nasazení](/azure/devops/learn/what-is-continuous-delivery) , které vám pomůžou pochopit, jak se dá používat v kontextu databází Oracle.

## <a name="architecture-and-design-considerations"></a>Požadavky na architekturu a návrh

- Zvažte použití [optimalizovaného paměťového optimalizovaného virtuálního počítače](../../sizes-memory.md) s [omezenými jádry vCPU](../../../virtual-machines/constrained-vcpu.md) pro váš virtuální počítač Oracle Database, který šetří náklady na licencování a maximalizuje výkon. Pro výkon a dostupnost použijte více disků Premium nebo Ultra (spravované disky).
- Při použití spravovaných disků se může při restartování změnit název disku nebo zařízení. Doporučuje se místo názvu použít UUID zařízení, abyste zajistili, že vaše připojení budou v rámci restartování trvalá. Další informace najdete [tady](/previous-versions/azure/virtual-machines/linux/configure-raid#add-the-new-file-system-to-etcfstab).
- Pomocí zón dostupnosti můžete dosáhnout vysoké dostupnosti v oblasti.
- Zvažte použití disků Ultra (Pokud je k dispozici) nebo prémiových disků pro vaši databázi Oracle.
- Zvažte nastavení pohotovostní databáze Oracle v jiné oblasti Azure pomocí ochrany dat Oracle.
- Pokud chcete snížit latenci mezi aplikací a databázovou vrstvou, zvažte použití [skupin umístění blízkosti](../../../virtual-machines/linux/co-location.md#proximity-placement-groups) .
- Nastavte [podnikového správce Oracle](https://docs.oracle.com/en/enterprise-manager/) pro správu, monitorování a protokolování.
- Zvažte použití funkce Oracle pro správu úložiště (ASM) pro zjednodušenou správu úložiště pro vaši databázi.
- Pomocí [Azure Pipelines](/azure/devops/pipelines/get-started/what-is-azure-pipelines) můžete spravovat opravy a aktualizace databáze bez výpadků.
- Přizpůsobte kód aplikace pro přidání nativních vzorů pro Cloud, jako je [vzor opakování](/azure/architecture/patterns/retry), [vzorek přerušení okruhu](/azure/architecture/patterns/circuit-breaker)a další vzory definované v [Průvodci návrhovými vzory cloudu](/azure/architecture/patterns/) , které můžou přispět k vyšší odolnosti vaší aplikace.

## <a name="next-steps"></a>Další kroky

Projděte si následující články o odkazech Oracle, které se vztahují k vašemu scénáři.

- [Úvod do Oracle data Guard](https://docs.oracle.com/en/database/oracle/oracle-database/18/sbydb/introduction-to-oracle-data-guard-concepts.html#GUID-5E73667D-4A56-445E-911F-1E99092DD8D7)
- [Koncepty zprostředkovatele Oracle data Guard](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/dgbkr/oracle-data-guard-broker-concepts.html)
- [Konfigurace Oracle GoldenGate pro Active-Active vysoké dostupnosti](https://docs.oracle.com/goldengate/1212/gg-winux/GWUAD/wu_bidirectional.htm#GWUAD282)
- [Přehled Oracle horizontálního dělení](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-overview.html)
- [Oracle Active Data Guard – vzdálená synchronizace – nulová ztráta dat v libovolné vzdálenosti](https://www.oracle.com/technetwork/database/availability/farsync-2267608.pdf)
