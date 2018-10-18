---
title: Azure SQL Database – nejčastější dotazy | Dokumentace Microsoftu
description: Odpovědi na běžné dotazy zákazníků, požádejte o cloudových databází Azure SQL Database, od Microsoftu relační databázový systém (RDBMS) a databáze jako služba v cloudu.
services: sql-database
ms.service: sql-database
ms.subservice: ''
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 10/17/2018
ms.openlocfilehash: 61950a1afda1fefdfab95f6d63420387dc2e2ac7
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2018
ms.locfileid: "49378631"
---
# <a name="sql-database-frequently-asked-questions-faq"></a>SQL Database – nejčastější dotazy (FAQ)

## <a name="what-is-the-current-version-of-sql-database"></a>Co je aktuální verze služby SQL Database

Je aktuální verze služby SQL Database V12. Verze 11 verze je vyřazený.

## <a name="what-is-the-sla-for-sql-database"></a>Co je smlouva SLA pro službu SQL Database

Garantujeme 99,99 % času, můžete mít k dispozici připojení mezi Microsoft Azure SQL Database a naší internetovou bránou bez ohledu na vaší úrovni služby. 0,01 % je vyhrazené pro převzetí služeb při selhání, opravy a upgrady. Další informace najdete v tématu [SLA](http://azure.microsoft.com/support/legal/sla/). Informace o architektuře dostupnost služby Azure SQL Database najdete v tématu [vysokou dostupnost a Azure SQL Database](sql-database-high-availability.md).

## <a name="can-i-control-when-patching-downtime-occurs"></a>Můžete řídit při použití dílčích oprav výpadek nastane

Ne. Dopad opravy není obecně souborům Pokud jste [použít logiku opakování](sql-database-develop-overview.md#resiliency) ve vaší aplikaci.

## <a name="what-is-the-new-vcore-based-purchasing-model-for-azure-sql-database"></a>Co je nového založený na virtuálních jádrech nákupní model pro službu Azure SQL Database

Nový nákupní model doplňuje stávající model založený na DTU. Model založený na virtuálních jádrech je účelem je poskytnout zákazníkům flexibilitu, kontrolu, transparentnost a jednoduchý způsob převodu místních požadavků na zatížení do cloudu. Také umožňuje zákazníkům škálovat své výpočetní prostředky a úložiště prostředků na základě jejich potřebám provádění úloh. Izolovaná databáze a elastický fond možnosti použití modelu virt. jader jsou také nárok až na 30 procent spoření s [zvýhodněné hybridní využití Azure pro SQL Server](../virtual-machines/windows/hybrid-use-benefit-licensing.md). Zobrazit [nákupní model založený na DTU](sql-database-service-tiers-dtu.md) a [nákupní model založený na virtuálních jádrech](sql-database-service-tiers-vcore.md) Další informace.

## <a name="what-is-a-vcore"></a>Co je virtuální jádro

Virtuální jádro reprezentuje logický procesor nabízený s možností volby mezi generacemi hardwaru. Gen 4 logické procesory jsou založeny na Intel E5-2673 v3 (Haswell) 2,4 GHz procesorech a logické procesory generace 5 jsou založené na Intel E5-2673 v4 (Broadwell) 2.3 GHz procesorech.

## <a name="is-moving-to-the-vcore-based-model-required"></a>Přechází na model založený na virtuálních jádrech vyžaduje

Ne, zavedení modelu založeného na virtuálních jádrech pro varianty nasazení izolovaná databáze a elastický fond je odrazem našeho úsilí možnosti volby zákazníků a flexibilitu. Pokud zákazníci chtějí pokračovat v používání model založený na DTU, není třeba provádět žádnou akci se toto oznámení a jejich prostředí a fakturace se nezmění.

V mnoha případech můžou aplikace využít jednoduchost předem nakonfigurované sady prostředků. Proto jsme dál nabízí, a podporují tyto možnosti založený na DTU pro naše zákazníky. Pokud je nevyužíváte a splňuje vaše podnikové požadavky, měli byste pokračovat Uděláte to tak.

Modely založené na DTU a na virtuálních jádrech budou i nadále koexistovat. Spouštíme model založený na virtuálních jádrech v reakci na požadavky zákazníků o zajištění větší transparentnosti databázových prostředků a schopnost škálovat své výpočetní a úložnou kapacitu nezávisle. Model založený na virtuálních jádrech také umožňuje další úspory pro zákazníky s aktivním programem Software Assurance prostřednictvím zvýhodněné hybridní využití Azure pro SQL Server.

## <a name="how-should-i-choose-between-the-dtu-based-purchasing-model-vs-the-vcore-based-purchasing-model"></a>Čeho si mám vybrat mezi vs založený na DTU nákupní model nákupní model založený na virtuálních jádrech

Jednotka přenosu dat (DTU) se měří na základě výkonu procesoru, paměti a operací čtení a zápisu. Velikosti výpočetních založený na DTU představují předem nakonfigurované sady prostředků jednotky různé úrovně výkonu aplikace. Zákazníci, kteří nechtějí starat o podkladové prostředky a dáváte přednost jednoduchosti, kterou nabízejí předem nakonfigurované sady prostředků při placení pevnou měsíční zjistit model založený na DTU více odpovídají jejich potřebám. Pro zákazníky, kteří potřebovat lepší přehled o příslušné prostředky nebo se škálovat nezávisle pro zajištění optimálního výkonu, ale model založený na virtuálních jádrech budou nejlepší volbou.  Kromě toho pokud zákazník má aktivní Software Assurance (SA) pro SQL Server, mohou využít své stávající investice a ušetřit až 30 % s [zvýhodněné hybridní využití Azure pro SQL Server](../virtual-machines/windows/hybrid-use-benefit-licensing.md).  Možnosti v rámci každé tyto modely nákupu poskytují výhody plně spravované služby, jako jsou automatizované zálohy, opravy a aktualizace softwaru.

## <a name="what-is-the-azure-hybrid-benefit-for-sql-server"></a>Co je zvýhodněné hybridní využití Azure pro SQL Server

[Zvýhodněné hybridní využití Azure pro SQL Server](../virtual-machines/windows/hybrid-use-benefit-licensing.md) pomáhá maximalizovat využití stávajících investic do licencí a Zrychlete svůj přechod do cloudu. Zvýhodněné hybridní využití Azure pro SQL Server je benefit založené na Azure, umožňující využijte svoje licence SQL Server s programem Software Assurance a platit tak sníženou sazbu ("základní tarif") pro službu SQL Database. Zvýhodněné hybridní využití Azure pro SQL Server je k dispozici ve verzi public preview založený na virtuálních jádrech nákupní model pro izolované databáze SQL Database a elastické fondy. Může použít tuto výhodu, i v případě, že SKU není aktivní, ale mějte na paměti, že se základní sazba se použije od okamžiku, vyberte na portálu Azure portal. Kredity se nevydávají zpětně.

## <a name="are-there-dual-use-rights-with-azure-hybrid-benefit-for-sql-server"></a>Existují dvojího užití práva s programem Azure Hybrid Benefit pro SQL Server

Máte 180denní dvojí použití práv licence, ujistěte se, že migrace běží bez problémů. Po uplynutí této doby 180denní licenci systému SQL Server jde použít jenom v cloudu ve službě SQL Database a nemá žádné dvojí použití práv v místním prostředí i v cloudu.

## <a name="how-does-azure-hybrid-benefit-for-sql-server-differ-from-license-mobility"></a>Jak zvýhodněné hybridní využití Azure pro SQL Server se liší od mobilitu licencí

V současné době nabízíme využijete mobilitu licencí zákazníkům s SQL serverem s programem Software Assurance, umožňující opětovné přiřazení licence na sdílené servery třetích stran. Tento benefit je možné na Azure IaaS a EC2 služby AWS.
Zvýhodněné hybridní využití Azure pro SQL Server se liší od mobilitu licencí v dvě klíčové oblasti:

- Poskytuje ekonomické přínosy pro přesun vysoce virtualizované úlohy do Azure. SQL EE zákazníci mohou získat 4 jádra v Azure v SKU pro obecné účely pro každé jádro, které vlastní v místním pro vysoce virtualizované aplikace. Mobility licencí v rámci nepovoluje žádné náklady na zvláštní výhody pro přesun virtualizovaných úloh do cloudu.
- Poskytuje pro cíl PaaS v Azure (SQL Database Managed Instance), která je vysoce kompatibilní s místním SQL serverem

## <a name="what-are-the-specific-rights-of-the-azure-hybrid-benefit-for-sql-server"></a>Jaké jsou konkrétní práva zvýhodněné hybridní využití Azure pro SQL Server

Zákazníci SQL Database bude mít následující práva spojená s programem Azure Hybrid Benefit pro SQL Server:

|Licence nároky na místo|Co dělá zvýhodněné hybridní využití Azure pro SQL Server získat?|
|---|---|
|Zákazníci jádra SQL Server Enterprise Edition s SA|<li>Můžete platit základní sazba pro obecné účely nebo SKU pro důležité obchodní</li><br><li>1 jádro v místním = 4 jádra v SKU pro obecné účely</li><br><li>1 jádro v místním = 1 jádro v SKU pro důležité obchodní</li>|
|SQL Server Standard Edition core zákazníkům s SA|<li>Můžete platit základní míra na SKU pro obecné účely pouze</li><br><li>1 jádro v místním = 1 jádro v SKU pro obecné účely</li>|
|||

## <a name="is-the-vcore-based-model-available-to-sql-database-managed-instance"></a>Model založený na virtuálních jádrech je dostupný pro SQL Database Managed Instance

[Spravovaná Instance](sql-database-managed-instance.md) je dostupné pouze model založený na virtuálních jádrech. Další informace najdete v tématu [stránce s cenami SQL Database](https://azure.microsoft.com/pricing/details/sql-database/managed/).

## <a name="does-the-cost-of-compute-and-storage-depend-on-the-service-tier-that-i-choose"></a>Závisí náklady na výpočetní výkon a úložiště na úrovni služby, které mám zvolit

Náklady na výpočetní odráží celkové výpočetní kapacitu, pro kterého je zřízené pro aplikaci. V rámci úrovně služeb pro důležité obchodní informace můžeme automaticky přidělit nejméně 3 repliky. Cena vCore tak, aby odrážela tuto další přidělování výpočetních prostředků, je přibližně 2.7 x vyšší v pro důležité obchodní informace. Ze stejného důvodu vyšší úložiště cen za GB na úrovni pro důležité obchodní informace odráží vysoké vstupně-výstupní operace a úložiště SSD s nízkou latencí. Ve stejnou dobu náklady na úložiště pro zálohování není různých vzhledem k tomu v obou případech používáme třídu úložiště úrovně standard.

## <a name="how-am-i-charged-for-storage---based-on-what-i-configure-upfront-or-on-what-the-database-uses"></a>Jak mi budete účtovat úložiště – na základě můžu nakonfigurovat předem nebo databáze používá

Různé typy úložiště se účtují různě. Pro ukládání dat bude vám účtována zřízeného úložiště na základě maximální velikost databáze nebo fondu, které vyberete. Náklady nemění, není-li snížit nebo zvýšit maximální. Úložiště zálohování je spojen s automatickým zálohám vaší instance a dynamicky přidělit. Prodloužení doby uchovávání záloh zvyšuje požadavky na úložiště zálohování vaší instance. Za úložiště zálohování do 100 % celkového zajišťovaného úložiště serveru se neplatí žádné dodatečné poplatky. Využití úložiště zálohování je účtovat GB za měsíc. Když například máte databázové úložiště velikosti 100 GB, získáte 100 GB úložiště zálohování zdarma. Ale pokud je záloha mít objem 110 GB, zaplatíte za dalších 10 GB.

Pro úložiště záloh u jediné databáze bude se vám účtovat na základě průběžné pro úložiště, která byla přidělena k zálohování databáze minus velikosti databáze. Pro úložiště záloh elastického fondu bude se vám účtovat na základě průběžné pro úložiště, která byla přidělena k zálohování databáze všech databází ve fondu minus maximální velikost dat elastického fondu. Libovolný nárůst velikosti databáze nebo elastického fondu nebo zvýšení rychlost transakcí vyžaduje další úložiště a zvyšuje se tím vaše faktura za úložiště zálohování.  Pokud zvýšíte maximální velikost dat, je toto nové množství odečte od velikost úložiště pro zálohování se fakturuje.

## <a name="how-do-i-select-the-right-sku-when-converting-an-existing-database-to-the-new-service-tiers"></a>Jak se při převodu existující databáze na nové úrovně služeb vyberte správné SKU

Pro stávající aplikace SQL Database s použitím modelu založeného na DTU služba úrovni General Purpose je srovnatelná se na úrovni Standard. Úroveň pro důležité obchodní informace služby je srovnatelné s úrovní Premium. V obou případech byste měli přidělit nejméně 1 virtuální jádro pro každý 100 DTU, které vaše aplikace používá v modelu založeném na DTU.

## <a name="do-the-new-vcore-based-service-tiers-offer-the-compute-sizes-compatible-with-all-existing-compute-sizes"></a>Nové úrovně služeb založený na virtuálních jádrech nabízí kompatibilní se stávajícími všech velikostí výpočetních výpočtů velikostí

Nové úrovně služeb založený na virtuálních jádrech nabízet srovnatelné výkonu pro všechny elastických fondů a databází pomocí 100 Dtu nebo více.  Budeme nadále přidat že další výpočetní velikosti postupně tak, aby vyhovovaly dílčí úlohy 100 DTU.

## <a name="are-there-any-database-feature-differences-between-the-existing-dtu-based-and-new-vcore-based-service-tiers"></a>Existují jakékoli databázi rozdíly ve funkcích mezi stávající úrovně založený na DTU a nové služby založený na virtuálních jádrech

Nové úrovně služeb pro podporu nadmnožinu funkcí dostupných prostřednictvím aktuální založený na DTU nabídek. Další funkce zahrnují sadu zobrazení další dynamické správy (DMV) a možnosti konfigurace dalších prostředků.

## <a name="if-my-database-is-cpu-bound-and-does-not-use-much-storage-can-i-increase-the-compute-without-paying-for-extra-storage"></a>Pokud Moje databáze je vázané na procesor a nepoužívá velké úložiště, můžete zvýšit tak výpočetní prostředky bez nutnosti platit za dodatečné úložiště

Ano, můžete nezávisle na sobě vyberte úroveň výpočetního výkonu, vaše aplikace potřebuje a zachovat úložiště beze změny. Úložiště lze nastavit v rozsahu od 32GB.

## <a name="will-the-new-vcore-based-tiers-support-point-in-time-restore-pitr-for-35-days-as-today"></a>Budou podporovat nové úrovně teď založený na virtuálních jádrech bodu v čase (PITR) po dobu 35 dní jako dnes

Můžete nakonfigurovat uchovávání záloh pro PITR mezi 7 až 35 dnů. Úložiště zálohování bude účtovat samostatně na základě skutečného úložiště využití při překročení rovna hodnotě maximální velikost dat velikost úložiště. Ve verzi preview ve výchozím nastavení Doba uchování PITR je nastaven na 7 dní. Maximální velikost dat je v mnoha případech dostatečné pro uložení zálohy za 7 dní.

## <a name="why-do-you-allow-selection-of-the-hardware-generation-for-compute"></a>Proč vám umožní výběr generace hardwaru pro službu compute

Naším cílem je umožnit maximální flexibilitu, aby mohli vybrat konfiguraci výkonu, který přesně odpovídá potřebám vaší aplikace. Gen4 hardware nabízí podstatně více paměti na vCore. Gen5 hardware však umožňuje vertikálně navýšit kapacitu výpočetních prostředků mnohem vyšší. Další informace najdete v tématu [nákupní modelu virt. jader](sql-database-service-tiers-vcore.md)

## <a name="do-i-need-to-take-my-application-offline-to-convert-from-a-dtu-based-database-to-a-vcore-based-service-tier"></a>Je nutné nastavit aplikaci offline převést z databáze založený na DTU pro vrstvu služby založený na virtuálních jádrech

Nové úrovně služeb nabízejí jednoduchý způsob online převodu, který je obdobou stávajícího procesu upgradu databází z úrovně služeb Standard na Premium (a naopak). Tento převod lze inicializovat pomocí webu Azure portal, Powershellu, rozhraní příkazového řádku Azure, T-SQL nebo rozhraní REST API. Zobrazit [Správa izolovaných databází](sql-database-single-database-scale.md) a [Správa elastických fondů](sql-database-elastic-pool.md).

## <a name="can-i-convert-a-database-from-a-vcore-based-service-tier-to-a-dtu-based-one"></a>Můžu převést databázi z vrstvy služby s využitím virtuálních jader na jeden založený na DTU

Ano, můžete snadno převést databázi do jakékoli objective podporované výkonu pomocí webu Azure portal, Powershellu, rozhraní příkazového řádku Azure, T-SQL nebo rozhraní REST API. Zobrazit [Správa izolovaných databází](sql-database-single-database-scale.md) a [Správa elastických fondů](sql-database-elastic-pool.md).

## <a name="can-i-upgrade-or-downgrade-between-the-general-purpose-and-business-critical-service-tiers"></a>Můžete upgradovat nebo downgradovat mezi úrovněmi služeb pro obecné účely a pro důležité obchodní informace

Ano, s určitými omezeními. Cíl SKU, musí splňovat maximální databáze nebo elastického fondu velikost, které jste nakonfigurovali pro vaše stávající nasazení. Pokud používáte [zvýhodněné hybridní využití Azure pro SQL Server](../virtual-machines/windows/hybrid-use-benefit-licensing.md), SKU pro důležité obchodní je dostupná jenom pro zákazníky s licencemi Enterprise Edition. Pouze zákazníci, kteří migrovat z místního na použití programu zvýhodněné hybridní využití Azure pro SQL Server s licencemi Enterprise Edition pro obecné účely můžete upgradovat na důležité obchodní informace. Podrobnosti najdete v tématu [jaké jsou konkrétní práva zvýhodněné hybridní využití Azure pro SQL Server](../virtual-machines/windows/hybrid-use-benefit-licensing.md)?

Tento převod nemá za následek výpadek a lze inicializovat pomocí webu Azure portal, Powershellu, rozhraní příkazového řádku Azure, T-SQL nebo rozhraní REST API. Zobrazit [Správa izolovaných databází](sql-database-single-database-scale.md) a [Správa elastických fondů](sql-database-elastic-pool.md).

## <a name="i-am-using-a-premium-rs-database-that-will-not-be-generally-available---can-i-upgrade-it-to-a-new-tier-and-achieve-a-similar-priceperformance-benefit"></a>Používám přestanou být obecně dostupná databáze úrovně Premium RS – můžu upgradovat na novou úroveň a dosáhnout podobné výhody poměr cena/výkon

Vzhledem k tomu modelu virt. jader umožňuje nezávislé řízení množství zřízených výpočetních a úložných, můžete efektivněji spravovat výsledné náklady díky tomu je atraktivní cíl pro databáze Premium RS. Kromě toho [zvýhodněné hybridní využití Azure pro SQL Server](../virtual-machines/windows/hybrid-use-benefit-licensing.md) poskytuje podstatné slevu, pokud se používá model založený na virtuálních jádrech.

## <a name="how-often-can-i-adjust-the-resources-per-pool"></a>Jak často můžu měnit prostředky na fond

Tak často, jak chcete. Zobrazit [Správa elastických fondů](sql-database-elastic-pool.md).

## <a name="how-long-does-it-take-to-change-the-service-tier-or-compute-size-of-a-single-database-or-move-a-database-in-and-out-of-an-elastic-pool"></a>Jak dlouho trvá Změna úrovně služby nebo vypočítat velikost u jediné databáze nebo přesunutí databáze do elastického fondu

Změna úrovně služby databáze a přesuny fondu vyžaduje databáze, které se mají zkopírovat na platformě jako operaci na pozadí. Změna úrovně služby může trvat pár minut i několik hodin v závislosti na velikosti databáze. V obou případech se databáze zůstat online a dostupné během přesunu. Podrobnosti o změně izolované databáze najdete v tématu [Změna úrovně služby databáze](sql-database-service-tiers-dtu.md).

## <a name="when-should-i-use-a-single-database-vs-elastic-databases"></a>Kdy mám použít izolované databáze a elastické databáze

Obecně platí, elastické fondy jsou navrženy pro typické [software jako služba (SaaS) aplikací vzor](sql-database-design-patterns-multi-tenancy-saas-applications.md), ve kterých je jednu databázi tenanta nebo zákazníka. Nakupování jednotlivých databází a zřizovat a měnícím ve špičce pro každou databázi je často není hospodárné z hlediska nákladů. S fondy spravovat celkový výkon fondu a databáze škálování nahoru i dolů automaticky. Inteligentní modul Azure doporučuje fond databází při využití vzoru zaručuje ho. Podrobnosti najdete v tématu [elastického fondu pokyny](sql-database-elastic-pool.md).

## <a name="how-does-the-usage-of-sql-database-using-the-dtu-based-purchasing-model-show-up-on-my-bill"></a>Jak využití SQL Database s použitím nákupní model založený na DTU objeví v mém vyúčtování

SQL Database účtuje předvídatelná hodinová sazba na základě [zakoupení modelu](sql-database-service-tiers-dtu.md). Skutečné využití je vypočítán a poměrně po hodinách, takže na faktuře můžou objevit necelé hodiny. Například pokud databáze existuje po dobu 12 hodin v měsíci, zobrazí faktuře využití 0,5.

## <a name="what-if-a-single-database-is-active-for-less-than-an-hour-or-uses-a-higher-service-tier-for-less-than-an-hour"></a>Co když je aktivní kratší dobu než hodinu nebo využívá vyšší úroveň služby pro kratší dobu než hodinu izolovanou databázi

Se vám účtovat každá hodina existence databáze pomocí nejvyšší úroveň služby a vypočítat velikost, která během této hodiny využívalo, bez ohledu na využití nebo na to, jestli byl databáze aktivní kratší dobu než hodinu. Například pokud vytvoření izolované databáze a po pěti minutách ji odstraníte vyúčtování projeví účtovat jedna hodina používání databáze.

Příklady:

- Pokud vytvoříte základní databáze a pak ho ihned upgradujete na úroveň Standard S1, jsou za první hodinu účtovat sazba Standard S1.
- Pokud spustíte upgrade databáze z Basic na úroveň Premium ve 22:00 hodin a upgrade dokončí v 1:35 čas. následujícího dne bude se vám účtovat sazba Premium spouští v 1:00
- Pokud spustíte downgrade databáze z úrovně Premium na Basic v 11:00 a ten se dokončí v 14:15:00, pak databázi se účtuje sazba za úroveň Premium až do 3:00 hodin, po jejímž uplynutí ho se zpoplatňují sazbou za základní.

## <a name="how-does-elastic-pool-usage-using-the-dtu-based-purchasing-model-show-up-on-my-bill"></a>Jak využití elastického fondu pomocí nákupní model založený na DTU objeví v mém vyúčtování

Elastický fond účtuje zobrazit nahoru na faktuře jako Elastických jednotkách Dtu (Edtu) nebo virtuálních jader a úložiště v přírůstcích po zobrazený na [stránku s cenami](https://azure.microsoft.com/pricing/details/sql-database/). Neplatí žádné poplatky za databáze pro elastické fondy. Účtuje se vám každá hodina existence fondu na eDTU nejvyšší nebo virtuálních jader, bez ohledu na využití nebo na to, jestli byl fondu aktivní kratší dobu než hodinu.

Založený na DTU nákupní model příklady:

- Pokud vytvoříte standardní elastického fondu s 200 Edtu v 11:18:00, přidáním pěti databází do fondu, bude vám účtována 200 Edtu za celou hodinu počínaje 11: 00 ve zbývající části dne.
- Databáze 1 na 2 den v 5:05:00., začne přijímat 50 Edtu a obsahuje stabilní přes den. Databáze 2 – 5 kolísat mezi 0 a 80 jednotek Edtu. Během dne přidáte pět databází, které využívají různé Edtu během dne. 2. den je den plný účtuje ve výši 200 eDTU.
- Na 3, den v 5: 00. můžete přidat další 15 databází. Využití databáze se zvyšuje v průběhu dne do bodu, pokud se rozhodnete zvýšit počet jednotek Edtu pro fond z hodnoty 200 na 400 ve 20:05 Náklady na úrovni 200 eDTU byly v platnosti až 20: 00 a zvýší na 400 jednotek Edtu pro zbývající čtyři hodiny.

## <a name="how-are-elastic-pool-billed-for-the-dtu-based-purchasing-model"></a>Jak se fakturuje základě DTU elastického fondu zakoupení modelu

Elastické fondy se účtují po následující vlastnosti:

- Elastický fond se účtuje po jeho vytvoření, i když nejsou žádné databáze ve fondu.
- Elastický fond se účtuje po hodinách. Jedná se o stejné měření četnost jako u velikostí výpočetních izolovaných databází.
- Pokud je velikost elastického fondu, není až do dokončení operace změny velikosti fondu účtovat podle nové množství prostředků. To se používá stejný vzor jako v případě změny velikosti výpočetního izolovaných databází.
- Cena elastického fondu je založená na prostředky ve fondu. Cena elastického fondu nezávisí na počtu a využívání elastických databází v rámci něj.

Podrobnosti najdete v tématu [SQL Database – ceny](https://azure.microsoft.com/pricing/details/sql-database/), [nákupní model založený na DTU](sql-database-service-tiers-dtu.md), a [nákupní model založený na virtuálních jádrech](sql-database-service-tiers-vcore.md).

## <a name="how-does-the-vcore-based-usage-show-up-in-my-bill"></a>Jak využití založený na virtuálních jádrech objeví v mém vyúčtování

Model založený na virtuálních jádrech služba se fakturuje na předvídatelné hodinové sazby založené na úrovni služeb, zřízených výpočetních jádrech, zřízeném úložišti v GB/měsíc a využitém úložišti zálohování. Pokud místo pro zálohování překračuje celkovou velikost databáze (to znamená 100 % velikosti databáze), existuje další poplatky. hodiny virtuálních jader, nakonfigurované úložiště databáze, využité vstupně-výstupní operace a úložiště pro zálohování je jasně uvedeno na faktuře je uvedena, a usnadnit si můžete zobrazit podrobnosti o prostředky, které jste už použili. Úložiště zálohování až pro 100 % maximální velikosti databáze je zahrnuta, za které se účtují v využité GB za měsíc v daném měsíci.

Příklad:

- Pokud databáze SQL existuje 12 hodin v měsíci, zobrazí na faktuře využití po dobu 12 hodin virtuálních jader. Databáze SQL zřídila dalších 100 GB úložiště, zobrazí na faktuře využití úložiště v jednotkách GB/měsíc poměrným přepočítáním po hodinách a počet využitých v daném měsíci.
- Pokud SQL database aktivní méně než hodinu, bude se vám účtovat pro každou hodinu existence databáze pomocí nejvyšší vybraná úroveň služby a zřízené úložiště a vstupně-výstupní operace, která během této hodiny využívalo, bez ohledu na využití nebo na to, zda byla databáze aktivní méně než hodiny.
- Pokud vytvoříte Managed Instance a po pěti minutách ji odstraníte, bude se vám účtovat jedna hodina používání databáze.
- Když vytvoříte Managed Instance pro obecné účely s 8 virtuálními jádry a pak hned upgradujete na 16 virtuálních jader, bude se vám za první hodinu účtovat sazba za 16 virtuálních jader.

> [!NOTE]
> Po omezenou dobu poplatky za zálohování a vstupně-výstupní operace neúčtují žádné poplatky.

## <a name="how-does-the-use-of-active-geo-replication-in-an-elastic-pool-show-up-on-my-bill"></a>Jak funguje pomocí aktivní geografické replikace v zobrazit elastický fond se na mém vyúčtování

Na rozdíl od izolovaných databází pomocí [aktivní geografickou replikaci](sql-database-geo-replication-overview.md) díky elastickým databázím nemá přímý vliv na fakturaci.  Budou účtovat pouze prostředky zřízené pro jednotlivé fondy (fond primární a sekundární fond)

## <a name="how-does-the-use-of-the-auditing-feature-impact-my-bill"></a>Jak použít funkci auditování ovlivnit mém vyúčtování

Auditování je integrovaná do služby SQL Database bez dalších poplatků a je k dispozici na všech úrovních služby. Ale pro ukládání protokolů auditu, auditování používá funkce, platí účtu služby Azure Storage a účtovat sazby platné pro tabulky a fronty ve službě Azure Storage podle velikosti protokolů auditu.

## <a name="how-do-i-reset-the-password-for-the-server-admin"></a>Jak resetuji heslo správce serveru

V [webu Azure portal](https://portal.azure.com), klikněte na tlačítko **SQL servery**, vyberte server ze seznamu a potom klikněte na tlačítko **resetovat heslo**.

## <a name="how-do-i-manage-databases-and-logins"></a>Jak můžu spravovat databází a přihlašovacích údajů

Zobrazit [Správa databází a přihlášení](sql-database-manage-logins.md).

> [!NOTE]
> Po vytvoření nelze změnit název účtu správce serveru.

## <a name="how-do-i-make-sure-only-authorized-ip-addresses-are-allowed-to-access-a-server"></a>Jak můžu ujistit, přístup k serveru jsou povoleny pouze IP adres ověřených

Zobrazit [postupy: Konfigurace nastavení brány firewall pro SQL Database](sql-database-configure-firewall-settings.md).

## <a name="what-is-an-expected-replication-lag-when-geo-replicating-a-database-between-two-regions-within-the-same-azure-geography"></a>Co je očekávané replikace prodlevy při geografickou replikaci databáze mezi dvěma oblastmi ve stejné zeměpisné oblasti Azure

Aktuálně podporujeme plánovaný bod obnovení pět sekund a zpoždění replikace byla menší než, že když geo-secondary hostována ve službě Azure nedoporučuje spárované oblasti a v rámci stejné úrovně služeb.

## <a name="what-is-an-expected-replication-lag-when-geo-secondary-is-created-in-the-same-region-as-the-primary-database"></a>Co je očekávané replikace prodlevy při geo-secondary ve stejné oblasti jako primární databáze

Na základě empirických dat, není příliš mnoho rozdíl mezi uvnitř oblasti a zpoždění replikace mezi oblastmi po Azure se doporučuje spárovaná oblast se používá.

## <a name="if-there-is-a-network-failure-between-two-regions-how-does-the-retry-logic-work-when-geo-replication-is-set-up"></a>Pokud je mezi dvěma oblastmi selhání sítě, jak logiku opakování funguje při nastavení geografické replikace

Pokud dojde k odpojení, budeme opakovat každých 10 sekund a znovu navázat spojení.

## <a name="what-can-i-do-to-guarantee-that-a-critical-change-on-the-primary-database-is-replicated"></a>Co mám dělat zaručí, že se replikují důležité změny u primární databáze

Je geograficky sekundární repliku s async a jsme Nepokoušejte se zachovat v úplnou synchronizaci s primární. Ale zajišťuje metodu k vynucení synchronizace k zajištění replikace důležité změny (například aktualizace hesla). Vynucené synchronizace ovlivňuje výkon, protože blokuje volající vlákno, dokud se replikují všechny potvrzené transakce. Podrobnosti najdete v tématu [uložená procedura sp_wait_for_database_copy_sync](https://msdn.microsoft.com/library/dn467644.aspx).

## <a name="what-tools-are-available-to-monitor-the-replication-lag-between-the-primary-database-and-geo-secondary"></a>Jaké nástroje jsou k dispozici pro sledování je zpoždění replikace mezi primární databází a geo-secondary.

Zveřejňujeme prodlevy v reálném čase replikace mezi primární databází a geo-secondary prostřednictvím zobrazení dynamické správy. Podrobnosti najdete v tématu [sys.dm_geo_replication_link_status](https://msdn.microsoft.com/library/mt575504.aspx).

## <a name="to-move-a-database-to-a-different-server-in-the-same-subscription"></a>Chcete-li přesunout databázi na jiný server v rámci stejného předplatného

V [webu Azure portal](https://portal.azure.com), klikněte na tlačítko **databází SQL**, vyberte databázi ze seznamu a potom klikněte na tlačítko **kopírování**. Zobrazit [kopírovat databázi Azure SQL](sql-database-copy.md) další podrobnosti.

## <a name="to-move-a-database-between-subscriptions"></a>Přesun databáze mezi předplatnými

V [webu Azure portal](https://portal.azure.com), klikněte na tlačítko **SQL servery** a pak vyberte server, který je hostitelem databáze ze seznamu. Klikněte na tlačítko **přesunout**a pak vyberte prostředky k přesunutí a předplatné, které chcete přesunout.