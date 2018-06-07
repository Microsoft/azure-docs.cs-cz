---
title: Nejčastější dotazy k Azure SQL Database | Microsoft Docs
description: Odpovědi na běžné otázky zákazníků, požádejte o cloudu databáze a databáze SQL Azure, společnosti Microsoft systému správy relačních databází (RDBMS) a databáze jako služba v cloudu.
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: reference
ms.topic: conceptual
ms.date: 04/04/2018
ms.author: carlrab
ms.openlocfilehash: 9bb79a2054a0e51ce435f51a52f964062427cea4
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34647992"
---
# <a name="sql-database-faq"></a>SQL Database – Nejčastější dotazy

## <a name="what-is-the-current-version-of-sql-database"></a>Co je aktuální verze SQL Database?
Je aktuální verze databáze SQL verze 12. Verze 11 verze byla vyřazena.

## <a name="what-is-the-sla-for-sql-database"></a>Co je smlouvě SLA pro databázi SQL?
Nemůžeme zaručit alespoň 99,99 % času, máte připojení mezi vaší Microsoft Azure SQL Database a naše internetovou bránu, bez ohledu na vaše vrstvy služeb. Další informace najdete v tématu [SLA](http://azure.microsoft.com/support/legal/sla/).

## <a name="whatis-the-new-vcore-based-purchasing-model-preview-for-azure-sql-database"></a>Whatis nový na základě vCore nákupní model (preview) pro Azure SQL Database?

Nový model nákupu je kromě existujícího modelu na základě DTU. Model na základě vCore je navržená tak získali zákazníkům flexibilitu, řízení, průhlednost a snadný způsob, jak převede místní požadavků na zatížení do cloudu. Také to umožňuje zákazníkům škálování výpočetní kapacity a úložiště podle jejich potřeb pracovního vytížení. Izolované databáze a možnosti elastického fondu pomocí modelu vCore jsou také vhodné pro až na 30 procent úspor s [výhody použití hybridní Azure pro systém SQL Server](../virtual-machines/windows/hybrid-use-benefit-licensing.md). V tématu [na základě DTU nákupní model](sql-database-service-tiers-dtu.md) a [nákupní model (preview) na základě vCore](sql-database-service-tiers-vcore.md) Další informace. 

## <a name="what-is-a-vcore"></a>Co je virtuální jádro? 
Virtuální základní představuje logický procesor nabízí s možností si vybrat mezi generací hardwaru. Gen 4 logické procesory jsou založené na Intel E5-2673 v3 procesory 2,4 GHz (Haswell) a logických procesorů 5 generace jsou založené na Intel E5-2673 v4 procesory 2.3 GHz (Broadwell).

## <a name="is-moving-to-the-vcore-based-model-required"></a>Vyžaduje se přechod na model založený na virtuálních jádrech?
Ne, zavedení modelu na základě vCore do elastického fondu a možnostmi nasazení jedné databáze odráží projektech pro zákazníka možnost volby a flexibilitu. Pokud zákazníci chtějí pokračovat v používání modelu na základě DTU, není nutné provádět žádnou akci se tato oznámení a jejich prostředí a fakturace zůstane beze změny. 

V mnoha případech může aplikace využít jednoduchost předkonfigurované sady prostředků. Proto jsme nadále nabízejí a podporovat tyto volby na základě DTU pro naše zákazníky. Pokud je používáte a splňuje požadavky vaší organizace, by měly být nadále učinit.

Modely založené na DTU a na virtuálních jádrech budou i nadále koexistovat. Model založený na virtuálních jádrech uvádíme na trh v reakci na požadavky zákazníků o zajištění větší transparentnosti databázových prostředků a možnosti nezávislého škálování výpočetních prostředků a úložiště. Model na základě vCore taky umožňuje dalším úsporám plynoucím pro zákazníky s active Software Assurance prostřednictvím Benefit hybridní Azure pro systém SQL Server.

## <a name="how-should-i-choose-between-the-dtu-based-purchasing-model-vs-the-vcore-based-purchasing-model-preview"></a>Jak mám zvolit mezi vs na základě DTU nákupní model nákupní model (preview) na základě vCore? 
Jednotka přenosu dat (DTU) se měří na základě výkonu procesoru, paměti a operací čtení a zápisu. Úrovně výkonu založené na DTU představují předem nakonfigurované sady prostředků pro zajištění různých úrovní výkonu aplikací. Zákazníci, kteří nechcete starat o základních prostředků a raději jednoduchost předkonfigurované sady při platícího pevná každý měsíc možná modelu na základě DTU vhodnější pro své potřeby. Pro zákazníky, kteří potřebují další aspekty příslušných prostředků nebo potřebujete škálování je nezávisle na nástroji k dosažení optimálního výkonu, ale modelu na základě vCore bude nejlepší volbou.  Kromě toho pokud zákazník má aktivní Software Assurance (SA) pro systém SQL Server, mohou využívat své stávající investice a uložit až 30 % [výhody použití hybridní Azure pro systém SQL Server](../virtual-machines/windows/hybrid-use-benefit-licensing.md).  Možnosti v rámci každé nákupu modely poskytují výhody plně spravované služby, jako je automatické zálohování, softwarové aktualizace a opravy. 

## <a name="what-is-the-azure-hybrid-benefit-for-sql-server"></a>Jaké jsou výhody hybridní Azure pro systém SQL Server? 
[Výhody použití hybridní Azure pro systém SQL Server](../virtual-machines/windows/hybrid-use-benefit-licensing.md) pomáhá maximalizovat hodnotu z vaše aktuální licencování investice a urychlit jejich migraci do cloudu. Výhody založené na Azure, která umožňuje licencí systému SQL Server pomocí programu Software Assurance věnovat snížené (dále jen "základní míra") v databázi SQL Azure hybridní využívat pro SQL Server je. Azure Benefit hybridní pro SQL Server je k dispozici ve verzi public preview na základě vCore nákupu modelu (preview) pro databázi SQL izolované databáze i elastické fondy. Může použít tuto výhodu, i když je aktivní verze SKU, ale Všimněte si, že základní míra platí od okamžiku, vyberte ho v portálu Azure. Kredity se nevydávají zpětně.

## <a name="are-there-dual-use-rights-with-azure-hybrid-benefit-for-sql-server"></a>Existují práva duální použití s Azure hybridní výhody pro SQL Server?
Máte 180 dnů dvojí použití práv licenčních zajistit, že migrace běží bez problémů. Po uplynutí této doby 180denní licenci systému SQL Server lze použít pouze v cloudu v databázi SQL a nemá dvojí použití práva místně a v cloudu.


## <a name="how-does-azure-hybrid-benefit-for-sql-server-differ-from-license-mobility"></a>Jak se liší Azure hybridní Benefit pro SQL Server a mobilita licencí?
V současné době nabízíme zákazníci SQL serveru s licencí programu Software Assurance mobility výhody, které umožňuje opětovné přiřazení jejich licencí, které chcete sdílené servery třetích stran. Tuto výhodu lze použít v Azure IaaS a AWS EC2.
Azure Benefit hybridní pro SQL Server se liší od mobilita licencí v dvě klíčové oblasti:
- Poskytuje hospodářského výhody pro přesun vysoce virtualizované úlohy do Azure. Zákazníci SQL EE můžete získat 4 jádra v Azure v obecné účely SKU pro každý základní vlastní místní pro vysoce virtualizované aplikace. Mobilita licencí nepovoluje žádné speciální nákladů pro přesun virtualizovaných úloh do cloudu.
- Poskytuje pro cíl PaaS v Azure, který je vysoce kompatibilní s systému SQL Server – místní – spravované Instance databáze SQL.

## <a name="what-are-the-specific-rights-of-the-azure-hybrid-benefit-for-sql-server"></a>Jaké jsou konkrétní práva benefitu hybridní Azure pro systém SQL Server?
Databáze SQL zákazníkům bude nemá následující oprávnění přidružené Benefit hybridní Azure pro systém SQL Server:

|Licence nároků|Jaké jsou výhody hybridní Azure pro SQL Server získat?|
|---|---|
|SQL Server Enterprise Edition základní zákazníkům SA|<li>Základní míru v obecné účely nebo kritické SKU obchodní může platit.</li><br><li>1 jádro místní = 4 jádra v obecné účely SKU</li><br><li>1 jádro místní = 1 jádro v obchodní kritické SKU</li>|
|SQL Server Standard Edition základní zákazníkům SA|<li>Může platit základní míra na obecné účely SKU pouze</li><br><li>1 jádro místní = 1 jádro v obecné účely SKU</li>|
|||

## <a name="is-the-vcore-based-model-available-to-sql-database-managed-instance"></a>Je k dispozici pro spravované Instance databáze SQL modelu na základě vCore?
[Spravované Instance](sql-database-managed-instance.md) je k dispozici pouze na základě vCore modelu. Další informace najdete také zjistit [SQL Database stránce s cenami](https://azure.microsoft.com/pricing/details/sql-database/managed/). 

## <a name="does-the-cost-of-compute-and-storage-depend-on-the-service-tier-that-i-choose"></a>Náklady na výpočetních operací a úložiště závisí na vrstvu služby, která zvolit?
Náklady na výpočetní odráží celkový výpočetní kapacitu, který je zajištěný pro aplikaci. V rámci firmy kritické úrovně služby můžeme automaticky přidělit minimálně 3 repliky Always ON. Cena vCore tak, aby odrážela tuto další přidělení výpočetní prostředky, je vyšší v kritické obchodní přibližně 2.7 x. Ze stejného důvodu odráží vyšší úložiště cena za GB v kritické obchodní vrstvu vysoké vstupně-výstupní operace a nízká latence úložiště SSD. Ve stejnou dobu náklady na úložiště zálohy není jiný, protože v obou případech používáme třídu úložiště standard Storage.

## <a name="how-am-i-charged-for-storage---based-on-what-i-configure-upfront-or-on-what-the-database-uses"></a>Jak se účtovat pro úložiště - založené na je možné nakonfigurovat předem nebo databáze používá?
Různé typy úložiště se fakturují jinak. Pro úložiště dat jsou účtovat pro zřízené úložiště na základě maximální databáze nebo velikost fondu, které vyberete. Náklady na nezmění, pokud snížit nebo zvýšit maximální. Úložiště zálohování je přidružené k automatickým zálohám vaší instance. Prodloužení doby uchovávání záloh zvyšuje požadavky na úložiště zálohování vaší instance. Za úložiště zálohování do 100 % celkového zajišťovaného úložiště serveru se neplatí žádné dodatečné poplatky. Další spotřeba úložiště zálohy je účtován v GB za měsíc. Když například máte databázové úložiště velikosti 100 GB, získáte 100 GB úložiště zálohování zdarma. Ale pokud je záloha 110 GB, platíte za dalších 10 GB.

Pro úložiště záloh služby jedné databáze budou se účtovat na základě poměrné pro úložiště, který byl přidělen zálohy databáze minus velikosti databáze. Pro zálohování úložiště elastického fondu budou se účtovat na základě poměrné pro úložiště, který byl přidělen zálohy databáze všech databází ve fondu minus maximální velikost dat elastického fondu. Všechny nárůst velikosti databáze nebo elastického fondu nebo zvyšte v rychlost transakcí, vyžaduje další úložiště a proto zvyšuje vaše faktura úložiště záloh.  Pokud zvýšíte maximální velikost dat, je toto nové množství odečtena od velikost fakturovaná úložiště záloh.

## <a name="how-do-i-select-the-right-sku-when-converting-an-existing-database-to-the-new-service-tiers"></a>Jak se při převodu existující databázi na nové úrovně služeb vyberte správné SKU? 
Pro existující aplikace SQL Database pomocí modelu na základě DTU je srovnatelná na úrovni Standard vrstvy služby obecné účely. Kritické obchodní vrstvy služeb je srovnatelná se úroveň Premium. V obou případech byste měli přidělit nejméně 1 vCore pro každý 100 DTU, které vaše aplikace používá v modelu na základě DTU.

## <a name="do-the-new-vcore-based-service-tiers-offer-the-performance-levels-compatible-with-all-existing-service-level-objectives-slos"></a>Nové úrovně služeb na základě vCore nabízí úrovně výkonu, který je kompatibilní se všemi existujícími cílů na úrovni služby (slo)?
Nové úrovně služeb na základě vCore nabízí možnosti porovnatelný z hlediska výkonu pro všechny elastické fondy a pomocí 100 Dtu nebo více databází.  Budeme dál přidat další objekty slo časem zohlednit dílčí 100 DTU úlohy.

## <a name="are-there-any-database-feature-differences-between-the-existing-dtu-based-and-new-vcore-based-service-tiers"></a>Jsou všechny databáze rozdíly ve funkcích mezi vrstvami existující na základě DTU a nových služeb na základě vCore? 
Nové úrovně služeb podporují nadmnožinou funkce dostupná s aktuální na základě DTU nabídky. Další funkce zahrnují sadu další dynamické správy zobrazení (zobrazení dynamické správy) a dalších prostředků možnosti konfigurace. 

## <a name="if-my-database-is-cpu-bound-and-does-not-use-much-storage-can-i-increase-the-compute-without-paying-for-extra-storage"></a>Pokud databáze je vázané na procesor a nepoužívá velké úložiště, můžete zvýšit výpočetní bez placení pro úložiště?
Ano, můžete nezávisle vybrat úroveň výpočetní aplikace potřebuje a ponechat beze změny úložiště. Úložiště lze nastavit v rozsahu od 32GB. 

## <a name="will-the-new-vcore-based-tiers-support-point-in-time-restore-pitr-for-35-days-as-today"></a>Budou podporovat nové vrstvy na základě vCore bodu v doba obnovení (Možnosti PITR) jako dnes 35 dní? 
Můžete nakonfigurovat uchovávání záloh pro možnosti PITR mezi 7 až 35 dnů. Úložiště zálohování bude vám účtována samostatně podle spotřeby skutečné úložiště, pokud přesahuje množství úložiště, která je rovna maximální velikost dat. Ve verzi preview ve výchozím nastavení Doba uchování možnosti PITR nastavení je to 7 dní. V mnoha případech je maximální velikost dat dostatečná pro ukládání záloh 7 dní.

## <a name="why-do-you-allow-selection-of-the-hardware-generation-for-compute"></a>Proč vám umožní výběr generování hardwaru pro výpočet?
Naším cílem je umožnit maximální flexibilitu, aby mohli vybrat konfigurace výkonu, který přesně odpovídá potřebám aplikace. Výše uvedené tabulce jsou uvedeny rozdíly mezi Gen4 a Gen5. Konkrétně Gen4 hardwaru nabízí podstatně více paměti za vCore. Ale Gen5 hardwaru můžete škálovat výpočetní mnohem vyšší. Chceme mít tyto rozdíly transparentní, tak, aby poměr optimální ceny a výkonu můžete dosáhnout pro vaši aplikaci.

## <a name="do-i-need-to-take-my-application-offline-to-convert-from-a-dtu-based-database-to-a-vcore-based-service-tier"></a>Je potřeba provést Moje aplikace do režimu offline převést z databáze na základě DTU do vrstvy služeb na základě vCore? 
Nové úrovně služeb nabízejí jednoduchý způsob online převodu, který je obdobou stávajícího procesu upgradu databází z úrovně služeb Standard na Premium (a naopak). K tomuto převodu můžete využít portál, ARM, PowerShell, Azure CLI nebo T-SQL. V tématu [spravovat izolované databáze](sql-database-single-database-resources.md) a [spravovat elastické fondy](sql-database-elastic-pool.md).

## <a name="can-i-convert-a-database-from-a-vcore-based-service-tier-to-a-dtu-based-one"></a>Můžete převést databázi z vrstvy služeb na základě vCore do dalšího na základě DTU? 
Ano, můžete snadno převést databázi do jakéhokoli cíle podporované výkonu pomocí portálu nebo programově pomocí portálu, ARM, prostředí PowerShell, rozhraní příkazového řádku Azure nebo T-SQL. V tématu [spravovat izolované databáze](sql-database-single-database-resources.md) a [spravovat elastické fondy](sql-database-elastic-pool.md).

## <a name="can-i-upgrade-or-downgrade-between-the-general-purpose-and-business-critical-service-tiers"></a>Můžete upgradovat nebo starší verzi mezi obecné účely a kritické obchodní úrovně služeb? 
Ano, s určitými omezeními. Cíl SKU musí splňovat maximální databáze nebo velikost elastického fondu, které jste nakonfigurovali pro vaše stávající nasazení. Pokud používáte [výhody použití hybridní Azure pro systém SQL Server](../virtual-machines/windows/hybrid-use-benefit-licensing.md), SKU kritické obchodní dostupný jen pro zákazníky s licencí Enterprise Edition. Pouze zákazníci, kteří migrované z místní pro obecné účely pomocí Azure hybridní výhody pro SQL Server Enterprise Edition licence můžete upgradovat na obchodní kritický. Podrobnosti najdete v tématu [jaké jsou konkrétní práva výhody použití hybridní Azure pro systém SQL Server](../virtual-machines/windows/hybrid-use-benefit-licensing.md)?

Tento převod nevede výpadky a lze inicializovat pomocí portálu, ARM, prostředí PowerShell, rozhraní příkazového řádku Azure nebo T-SQL. V tématu [spravovat izolované databáze](sql-database-single-database-resources.md) a [spravovat elastické fondy](sql-database-elastic-pool.md).

## <a name="i-am-using-a-premium-rs-database-that-will-not-be-generally-available---can-i-upgrade-it-to-a-new-tier-and-achieve-a-similar-priceperformance-benefit"></a>Používám Premium RS databázi, která nebude obecně dostupná – lze upgradovat na novou vrstvu a dosáhnout podobné výhody ceny a výkonu?
Vzhledem k modelu vCore umožňuje nezávislé ovládat velikost zřízeného výpočetních operací a úložiště, můžete efektivněji spravovat výsledné náklady, což atraktivní cíl pro databáze Premium RS. Kromě toho [výhody použití hybridní Azure pro systém SQL Server](../virtual-machines/windows/hybrid-use-benefit-licensing.md) poskytuje výrazně slevu, pokud na základě vCore model se používá. 

## <a name="how-often-can-i-adjust-the-resources-per-pool"></a>Jak často můžete upravit prostředky na každý fond?
Tolikrát, kolikrát chcete. V tématu [spravovat elastické fondy](sql-database-elastic-pool.md).

## <a name="how-long-does-it-take-to-change-the-service-tier-or-performance-level-of-a-single-database-or-move-a-database-in-and-out-of-an-elastic-pool"></a>Jak dlouho trvá změnit úroveň služby výkon nebo úroveň služby jedné databáze nebo přesun databáze do/z fondu elastické databáze?
Změna úrovně služby databáze a přesouvání a deaktivovat fondu vyžaduje databázi, který se má zkopírovat na platformě jako operaci na pozadí. Změna úrovně služby může trvat několik minut až několik hodin v závislosti na velikosti databáze. V obou případech databáze zůstat online a dostupné během přesunu. Podrobnosti o změně izolované databáze najdete v tématu [změnit úroveň služby databázi](sql-database-service-tiers-dtu.md). 

## <a name="when-should-i-use-a-single-database-vs-elastic-databases"></a>Kdy použít jednu databázi oproti elastické databáze?
Obecně platí, elastické fondy navržených pro typické [vzor aplikace software jako služba (SaaS)](sql-database-design-patterns-multi-tenancy-saas-applications.md), kde je jedna databáze na zákazníka nebo klienta. Nakupování jednotlivých databází a jejich předimenzování kvůli měnícím se požadavkům každé databáze ve špičce často není hospodárné z hlediska nákladů. Fondy spravovat celkový výkon fondu a databáze škálovat nahoru a dolů automaticky. Inteligentní modul Azure doporučuje fond pro databáze při vzor používání zaručuje ho. Podrobnosti najdete v tématu [elastického fondu pokyny](sql-database-elastic-pool.md).

## <a name="how-does-the-usage-of-sql-database-using-the-dtu-based-purchasing-model-show-up-on-my-bill"></a>Jak použití databáze SQL pomocí modelu na základě DTU nákupu zobrazit na Moje faktury?
Na základě faktur SQL Database na předvídatelný hodinové sazby [zakoupení modelu](sql-database-service-tiers-dtu.md). Skutečné využití je počítaný a poměrně každou hodinu, takže vaše faktura může zobrazovat zlomků jednu hodinu. Například pokud databáze existuje 12 hodin za měsíc, vaše faktura zobrazuje využití 0,5 dní. 

## <a name="what-if-a-single-database-is-active-for-less-than-an-hour-or-uses-a-higher-service-tier-for-less-than-an-hour"></a>Co v případě jedné databáze je aktivní méně než jednu hodinu nebo používá vyšší úroveň služby pro méně než jednu hodinu?
Fakturuje se pro každou hodinu, kterou databáze existuje, pomocí nejvyšší úroveň služby + úroveň výkonu, které použijí danou dobu, bez ohledu na využití nebo zda byla databáze active méně než jednu hodinu. Například pokud vytvoříte jedné databáze a odstranit ji pěti minutách vaše faktura odráží zdarma pro jednu databázi hodinu. 

Příklady:

* Pokud chcete vytvořit základní databáze a pak okamžitě upgradovat na Standard S1, budeme vám účtovat rychlostí Standard S1 první hodiny.
* Pokud upgradujete databázi z Basic Premium na 10:00. a dokončení upgradu v 1:35 hodin Následující den budou se vám účtovat rychlostí Premium spouštění v 1:00 
* Pokud ponížit na základní databázi z Premium ve 20:00 a dokončení na 14:15:00 a pak databázi je účtován rychlostí Premium až 3:00 hodin, po jejímž uplynutí je účtován základní tempem.

## <a name="how-does-elastic-pool-usage-using-the-dtu-based-purchasing-model-show-up-on-my-bill"></a>Jak využití elastického fondu pomocí modelu na základě DTU nákupu objeví na Moje faktury?
Elastický fond účtuje zobrazit nahoru na vaší faktuře jako elastické jednotky Dtu (Edtu) nebo vCores plus úložiště v přírůstcích po zobrazený na [na stránce s cenami](https://azure.microsoft.com/pricing/details/sql-database/). Je bezplatná jednotlivé databáze pro elastické fondy. Fakturuje se pro každou hodinu, které fond existuje na nejvyšší eDTU nebo vCores, bez ohledu na využití nebo jestli byl fond aktivní méně než jednu hodinu. 

Na základě DTU nákupní model příklady:

* Pokud vytvoříte standardní elastický fond s 200 Edtu v 11:18:00, přidávání pět databází do fondu, budeme vám účtovat 200 Edtu pro celou hodinu od v 11: 00 ve zbývající části dne.
* Den 2 na 5:05:00. 1 databáze začne využívání 50 Edtu a má konstantní prostřednictvím dne. Databáze 2 až 5 kolísá mezi 0 a 80 Edtu. Během dne přidejte pět jiných databází, které využívají různých Edtu během dne. Den 2 je plný den účtovat podle 200 eDTU. 
* Den 3, ve 5: 00. můžete přidat další 15 databáze. Využití databáze se zvyšuje během dne do bodu, kde se rozhodnete zvýšit počet jednotek Edtu fondu z hodnoty 200 na 400 ve 20:05. Poplatky na úrovni 200 eDTU bylo platné až 20: 00 a zvýší na 400 Edtu pro zbývající čtyři hodiny. 

## <a name="how-are-elastic-pool-billed-for-the-dtu-based-purchasing-model"></a>Jak jsou účtovány poplatky za základě DTU elastického fondu zakoupení modelu?
Elastické fondy fakturují za následující vlastnosti:

* Elastický fond se fakturuje po jeho vytvoření i v případě, že nejsou žádné databáze ve fondu.
* Elastický fond se fakturuje každou hodinu. Jedná se o stejné měření četnost jako úrovně výkonu izolovaných databází.
* Pokud se změnila velikost fondu elastické databáze, není až po dokončení operace změny velikosti fondu účtují podle nové objem prostředků. To se následující stejné jako změna úrovně výkonu izolovaných databází.
* Cena fondu elastické databáze je založena na prostředky fondu. Cena fondu elastické databáze je nezávislé na číslo a využití elastické databáze v něm.

Podrobnosti najdete v tématu [SQL Database – ceny](https://azure.microsoft.com/pricing/details/sql-database/), [na základě DTU nákupní model](sql-database-service-tiers-dtu.md), a [nákupní model (preview) na základě vCore](sql-database-service-tiers-vcore.md).

## <a name="how-does-the-vcore-based-usage-show-up-in-my-bill"></a>Jak využití na základě vCore zobrazí v mé faktury? 
V modelu na základě vCore se fakturuje službu na předvídatelný, hodinové míra založené na vrstvě služby zřízené výpočet v vCores, zřízení úložiště v GB za měsíc a spotřebovaného úložiště záloh. Pokud úložiště pro zálohy překračuje velikost celkový databáze (to znamená, 100 % velikosti databáze), existuje další poplatky. vCore hodiny, nakonfigurované databáze úložiště, spotřebované vstupně-výstupní operace a úložiště zálohy je jasně uvedeno v faktury, usnadnit najdete v části Podrobnosti o prostředků, které jste už použili. Zálohování úložiště až 100 % velikosti maximální databáze je součástí, za které se účtují v GB/měsíc použít za měsíc.

Příklad:
- Pokud existuje databáze SQL pro 12 hodin za měsíc, kusovníku zobrazuje využití 12 hodin vCore. Pokud databáze SQL zajištěny další 100 GB úložiště, kusovníku zobrazuje úložiště využití v jednotkách GB/měsíc poměrné každou hodinu a počet IOs spotřebované za měsíc.
- Pokud databáze SQL je aktivní méně než hodinu, se účtují pro každou hodinu, existuje databáze pomocí nejvyšší úroveň služby vybrali, zřízení úložiště a vstupně-výstupní operace, které použijí danou dobu, bez ohledu na využití nebo zda byl aktivní pro méně než databáze jednu hodinu.
- Pokud vytvoříte Managed Instance a po pěti minutách ji odstraníte, bude se vám účtovat jedna hodina používání databáze.
- Když vytvoříte Managed Instance pro obecné účely s 8 virtuálními jádry a pak hned upgradujete na 16 virtuálních jader, bude se vám za první hodinu účtovat sazba za 16 virtuálních jader.

> [!NOTE]
> Po omezenou dobu až 30 2018 června zálohování poplatky a poplatky vstupně-výstupní operace jsou zdarma.

## <a name="how-does-the-use-of-active-geo-replication-in-an-elastic-pool-show-up-on-my-bill"></a>Jak funguje použití aktivní geografickou replikací v elastický fond se zobrazí na Moje faktury?
Na rozdíl od izolovaných databází můžete pomocí [aktivní geografickou replikací](sql-database-geo-replication-overview.md) s elastické databáze nemá přímý vliv fakturace.  Se účtují poplatky za prostředky zřízené pro každou z fondů (fondu primární a sekundární fond)

## <a name="how-does-the-use-of-the-auditing-feature-impact-my-bill"></a>Jak se v mém vyúčtování projeví používání funkce auditování?
Auditování je integrovaná do služby SQL Database bez jakýchkoli nákladů a je k dispozici na všech úrovních služeb. Však k ukládání protokolů auditu, auditování použití funkce, které účet služby Azure Storage a sazby za tabulky a fronty ve službě Azure Storage se vztahují na základě velikosti protokolu auditu.

## <a name="how-do-i-reset-the-password-for-the-server-admin"></a>Jak resetovat heslo správce serveru
V [portál Azure](https://portal.azure.com), klikněte na tlačítko **servery SQL**, vyberte server, ze seznamu a pak klikněte na tlačítko **resetovat heslo**.

## <a name="how-do-i-manage-databases-and-logins"></a>Jak spravovat databází a přihlašovacích údajů?
V tématu [Správa databází a přihlašovacích údajů](sql-database-manage-logins.md).

## <a name="how-do-i-make-sure-only-authorized-ip-addresses-are-allowed-to-access-a-server"></a>Jak se ujistěte se, jsou povoleny pouze autorizovaným IP adres přístup k serveru?
V tématu [postupy: Konfigurace nastavení brány firewall pro službu SQL Database](sql-database-configure-firewall-settings.md).

## <a name="what-is-an-expected-replication-lag-when-geo-replicating-a-database-between-two-regions-within-the-same-azure-geography"></a>Co je očekávané replikace funkce lag při geografickou replikaci databáze mezi dvěma oblastmi v rámci stejné geography Azure?
Jsme se aktuálně podporují RPO pět sekund a je zpoždění replikace byla menší než, když je sekundární geograficky hostované ve službě Azure doporučujeme spárované oblasti a ve stejné vrstvě služby.

## <a name="what-is-an-expected-replication-lag-when-geo-secondary-is-created-in-the-same-region-as-the-primary-database"></a>Co je očekávané replikace funkce lag při geograficky sekundární ve stejné oblasti jako primární databáze?
Na základě na základě zkušeností data, není příliš mnoho rozdíl mezi uvnitř oblasti a prodleva mezi oblast replikace při Azure doporučoval spárované oblast se používá. 

## <a name="if-there-is-a-network-failure-between-two-regions-how-does-the-retry-logic-work-when-geo-replication-is-set-up"></a>Pokud je mezi dvěma oblastmi selhání sítě, jak logika opakovaných pokusů funguje při geografická replikace je nastaven?
Pokud dojde k odpojení, opakované každých 10 sekund znovu navázat připojení.

## <a name="what-can-i-do-to-guarantee-that-a-critical-change-on-the-primary-database-is-replicated"></a>Co můžete dělat zaručit, že se replikují na kritické změnu primární databáze?
Je geo sekundární repliku asynchronní a jsme nesnažte se mějte úplnou synchronizaci s primární. Ale poskytujeme metoda vynutit synchronizaci zajistit replikaci důležité změny (např. aktualizace hesel). Vynucené synchronizace ovlivňuje výkon, protože blokuje volající vlákno, dokud se replikují všechny potvrzené transakce. Podrobnosti najdete v tématu [uložená procedura sp_wait_for_database_copy_sync](https://msdn.microsoft.com/library/dn467644.aspx). 

## <a name="what-tools-are-available-to-monitor-the-replication-lag-between-the-primary-database-and-geo-secondary"></a>Jaké nástroje jsou k dispozici ke sledování replikace prodleva mezi primární databáze a geografická sekundární?
Funkce lag v reálném čase replikace mezi primární databáze a geo sekundární prostřednictvím DMV zveřejňujeme. Podrobnosti najdete v tématu [sys.dm_geo_replication_link_status](https://msdn.microsoft.com/library/mt575504.aspx).

## <a name="to-move-a-database-to-a-different-server-in-the-same-subscription"></a>Chcete-li přesunout databázi na jiný server v rámci stejného předplatného
V [portál Azure](https://portal.azure.com), klikněte na tlačítko **databází SQL**, vyberte databázi ze seznamu a pak klikněte na tlačítko **kopie**. V tématu [zkopírujte Azure SQL database](sql-database-copy.md) další podrobnosti.

## <a name="to-move-a-database-between-subscriptions"></a>Přesunutí databáze mezi předplatnými
V [portál Azure](https://portal.azure.com), klikněte na tlačítko **servery SQL** a pak vyberte server, který je hostitelem databáze ze seznamu. Klikněte na tlačítko **přesunout**a pak vyberte zdroje, které chcete přesunout a předplatné pro přesun do.
