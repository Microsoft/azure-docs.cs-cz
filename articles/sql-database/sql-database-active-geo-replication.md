---
title: Aktivní geografická replikace – Azure SQL Database
description: Aktivní geografickou replikaci můžete použít k vytvoření čitelných sekundárních databází jednotlivých databází ve stejném nebo jiném datovém centru (oblasti).
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
ms.date: 07/09/2019
ms.openlocfilehash: 74cbb9fa5a00b287746afd92fe74f50bfa19110b
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2019
ms.locfileid: "73691314"
---
# <a name="creating-and-using-active-geo-replication"></a>Vytváření a používání aktivní geografické replikace

Aktivní geografická replikace je Azure SQL Database funkce, která umožňuje vytvářet čitelné sekundární databáze pro jednotlivé databáze na serveru SQL Database ve stejném nebo jiném datovém centru (oblasti).

> [!NOTE]
> Aktivní geografická replikace není podporována spravovanou instancí. Pro geografické převzetí služeb při selhání u spravovaných instancí použijte [skupiny automatického převzetí služeb při selhání](sql-database-auto-failover-group.md).

Aktivní geografická replikace je navržená jako řešení pro provozní kontinuitu, které umožňuje aplikaci provádět rychlé zotavení po havárii jednotlivých databází v případě regionálních havárií nebo výpadku velkého rozsahu. Pokud je geografická replikace povolená, může aplikace iniciovat převzetí služeb při selhání sekundární databází v jiné oblasti Azure. Ve stejných nebo různých oblastech se podporuje až čtyři sekundární databáze a sekundární je taky možné použít pro dotazy přístupu jen pro čtení. Převzetí služeb při selhání je nutné iniciovat ručně aplikací nebo uživatelem. Po převzetí služeb při selhání má nový primární server jiný koncový bod připojení. Následující diagram znázorňuje typickou konfiguraci geograficky redundantní cloudové aplikace pomocí aktivní geografické replikace.

![Aktivní geografická replikace](./media/sql-database-active-geo-replication/geo-replication.png )

> [!IMPORTANT]
> SQL Database podporuje také skupiny automatického převzetí služeb při selhání. Další informace najdete v tématu používání [skupin s automatickým převzetím služeb při selhání](sql-database-auto-failover-group.md). Aktivní geografická replikace se taky nepodporuje pro databáze vytvořené v rámci spravované instance. Zvažte použití [skupin převzetí služeb při selhání](sql-database-auto-failover-group.md) se spravovanými instancemi.

Pokud z nějakého důvodu dojde k selhání vaší primární databáze nebo stačí, abyste ji natrvaloi offline, můžete iniciovat převzetí služeb při selhání do kterékoli z vašich sekundárních databází. Při aktivaci převzetí služeb při selhání na jednu ze sekundárních databází se všechny ostatní sekundární repliky automaticky propojí s novým primárním objektem.

Replikaci a převzetí služeb při selhání jednotlivé databáze nebo sady databází můžete spravovat na serveru nebo v elastickém fondu pomocí aktivní geografické replikace. Můžete to udělat pomocí:

- [Azure Portal](sql-database-geo-replication-portal.md)
- [PowerShell: izolovaná databáze](scripts/sql-database-setup-geodr-and-failover-database-powershell.md)
- [PowerShell: elastický fond](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md)
- [Transact-SQL: jedna databáze nebo elastický fond](/sql/t-sql/statements/alter-database-azure-sql-database)
- [REST API: izolovaná databáze](https://docs.microsoft.com/rest/api/sql/replicationlinks)


Aktivní geografická replikace využívá technologii služby [Always On](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server) SQL Server k asynchronní replikaci potvrzených transakcí v primární databázi do sekundární databáze pomocí izolace snímků. Skupiny s automatickým převzetím služeb při selhání poskytují sémantiku skupiny nad aktivní geografickou replikací, ale používá se stejný mechanismus asynchronní replikace. V kterémkoli okamžiku může být sekundární databáze za primární databází mírně nepatrná, takže sekundární data jsou zaručena tak, aby nikdy nepoužívala částečné transakce. Redundance mezi jednotlivými oblastmi umožňuje aplikacím rychle se zotavit z trvalé ztráty celého datového centra nebo částí datacentra, které způsobují přírodní katastrofy, závažné lidské chyby nebo škodlivé činy. Konkrétní data bodu obnovení najdete v článku [Přehled provozní kontinuity](sql-database-business-continuity.md).

> [!NOTE]
> Pokud dojde k selhání sítě mezi dvěma oblastmi, opakujeme každých 10 sekund, než se znovu naváže připojení.
> [!IMPORTANT]
> Aby bylo zaručeno, že před převzetím služeb při selhání bude kritická Změna primární databáze replikována na sekundární, můžete vynutit synchronizaci, aby se zajistila replikace kritických změn (například aktualizace hesla). Vynucená synchronizace má vliv na výkon, protože blokuje volající vlákno, dokud nebudou všechny potvrzené transakce replikovány. Podrobnosti najdete v tématu [sp_wait_for_database_copy_sync](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync). Pokud chcete monitorovat prodlevu replikace mezi primární databází a geograficky sekundárním, přečtěte si článek [Sys. DM _geo_replication_link_status](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database).

Následující obrázek ukazuje příklad aktivní geografické replikace nakonfigurované s primárním umístěním v Střed USA – sever oblasti a sekundární v Střed USA – jih oblasti.

![vztah geografické replikace](./media/sql-database-active-geo-replication/geo-replication-relationship.png)

Vzhledem k tomu, že jsou sekundární databáze čitelné, lze je použít k přesměrování zatížení, která jsou určena jen pro čtení, jako jsou úlohy vytváření sestav. Pokud používáte aktivní geografickou replikaci, je možné vytvořit sekundární databázi ve stejné oblasti s primárním, ale nezvyšuje odolnost aplikace proti závažným chybám. Pokud používáte skupiny s automatickým převzetím služeb při selhání, sekundární databáze je vždy vytvořená v jiné oblasti.

Kromě zotavení po havárii aktivní geografická replikace může být použita v následujících scénářích:

- **Migrace databáze**: můžete použít aktivní geografickou replikaci k migraci databáze z jednoho serveru na jiný online s minimálními výpadky.
- **Upgrady aplikací**: během upgradování aplikace můžete vytvořit další sekundární kopii jako back-mailové kopie.

Aby bylo možné dosáhnout reálné provozní kontinuity, Přidání redundance databáze mezi datacentry je pouze součástí řešení. Kompletní obnovení aplikace (služby) po závažných chybách vyžaduje obnovení všech součástí, které tvoří službu a všechny závislé služby. Mezi tyto komponenty patří klientský software (například prohlížeč s vlastním JavaScriptem), webové front-endy, úložiště a DNS. Je velmi důležité, aby všechny součásti byly odolné vůči stejnou selhání a byly k dispozici v rámci plánované doby obnovení (RTO) vaší aplikace. Proto je potřeba identifikovat všechny závislé služby a porozumět zárukám a funkcím, které poskytují. Pak musíte provést vhodné kroky, abyste zajistili, že vaše služba funguje při převzetí služeb při selhání služeb, na kterých závisí. Další informace o návrhu řešení pro zotavení po havárii najdete v tématu [navrhování cloudových řešení pro zotavení po havárii pomocí aktivní geografické replikace](sql-database-designing-cloud-solutions-for-disaster-recovery.md).

## <a name="active-geo-replication-terminology-and-capabilities"></a>Aktivní terminologie a funkce pro geografickou replikaci

- **Automatická asynchronní replikace**

  Můžete vytvořit pouze sekundární databázi přidáním do existující databáze. Sekundární je možné vytvořit na jakémkoli serveru Azure SQL Database. Po vytvoření se sekundární databáze naplní daty zkopírovanými z primární databáze. Tento proces se označuje jako osazení. Po vytvoření a osazení sekundární databáze se aktualizace primární databáze asynchronně replikují do sekundární databáze automaticky. Asynchronní replikace znamená, že transakce jsou potvrzeny v primární databázi předtím, než budou replikovány do sekundární databáze.

- **Čitelné sekundární databáze**

  Aplikace může získat přístup k sekundární databázi pro operace jen pro čtení pomocí stejných nebo různých objektů zabezpečení používaných pro přístup k primární databázi. Sekundární databáze pracují v režimu izolace snímků, aby bylo zajištěno, že replikace aktualizací primárního (log Play) není zpožděna dotazy provedenými na sekundárním počítači.

> [!NOTE]
> Pokud jsou v primární databázi aktualizace schématu, je opětovné přehrání protokolu zpožděno v sekundární databázi. Druhá z nich vyžaduje zámek schématu na sekundární databázi.
> [!IMPORTANT]
> Geografickou replikaci můžete použít k vytvoření sekundární databáze ve stejné oblasti jako primární. Tuto sekundární službu můžete použít k vyrovnávání zatížení úloh jen pro čtení ve stejné oblasti. Sekundární databáze ve stejné oblasti ale neposkytuje další odolnost proti chybám, a proto není vhodným cílem převzetí služeb při selhání pro zotavení po havárii. Nebude taky zaručit izolaci zóny dostupnosti. K dosažení izolace zóny dostupnosti použijte úroveň služby pro důležité nebo Premium pro podnik s [redundantní konfigurací zóny](sql-database-high-availability.md#zone-redundant-configuration) .   
>

- **Plánované převzetí služeb při selhání**

  Plánované převzetí služeb při selhání přepíná role primárních a sekundárních databází po dokončení úplné synchronizace. Jedná se o online operaci, která nevede ke ztrátě dat. Čas operace závisí na velikosti transakčního protokolu na primárním počítači, který je třeba synchronizovat. Plánované převzetí služeb při selhání je navržené pro následující scénáře: (a), aby se prováděly postupy zotavení po havárii v produkčním prostředí, když je ztráta dat přijatelná; (b) Chcete-li databázi přemístit do jiné oblasti, a (c) pro návrat databáze do primární oblasti po omezení výpadku (navrácení služeb po obnovení).

- **Neplánované převzetí služeb při selhání**

  Neplánované nebo vynucené převzetí služeb při selhání okamžitě přepne sekundární roli do primární role bez jakékoli synchronizace s primární. Všechny transakce potvrzené na primární, ale nereplikované do sekundárního bude ztracena. Tato operace je navržena jako metoda obnovení během výpadků, když není primární přístup dostupný, ale dostupnost databáze se musí rychle obnovit. Když je původní primární databáze zpátky online, automaticky se znovu připojí a stane se novou sekundární. Všechny nesynchronizované transakce před převzetím služeb při selhání se zachovají do záložního souboru, ale nebudou synchronizovány s novým primárním rozhraním, aby nedocházelo ke konfliktům. Tyto transakce bude nutné ručně sloučit s nejnovější verzí primární databáze.
 
- **Více čitelných sekundárních**

  Pro každou primární databázi je možné vytvořit až 4 sekundární databáze. Pokud je k dispozici pouze jedna sekundární databáze a dojde k jejímu vyčerpání, je aplikace vystavena vyššímu riziku, dokud nebude vytvořena nová sekundární databáze. Pokud existuje více sekundárních databází, zůstane aplikace chráněná i v případě, že se jedna ze sekundárních databází nezdařila. K horizontálnímu navýšení kapacity úloh jen pro čtení můžete použít taky další sekundární procesy.

  > [!NOTE]
  > Pokud používáte aktivní geografickou replikaci k vytvoření globálně distribuované aplikace a potřebujete poskytnout přístup k datům ve více než čtyřech oblastech, můžete vytvořit sekundární sekundární (což je proces označovaný jako zřetězení). Tímto způsobem můžete dosáhnout prakticky neomezené škály replikace databáze. Řetězení navíc snižuje režii replikace z primární databáze. Kompromis je zvýšená prodleva při replikaci na nejvyšší úrovni sekundární databáze.

- **Geografická replikace databází v elastickém fondu**

  Každá sekundární databáze se může samostatně zúčastnit elastického fondu nebo nemusí být v jakémkoli elastickém fondu. Volba fondu pro jednotlivé sekundární databáze je oddělená a nezávisí na konfiguraci žádné jiné sekundární databáze (ať už primární nebo sekundární). Každý elastický fond je obsažený v jedné oblasti, takže více sekundárních databází ve stejné topologii nemůže nikdy sdílet elastický fond.


- **Převzetí služeb při selhání a navrácení služeb po obnovení**

  Sekundární databázi lze explicitně přepnout na primární roli kdykoli pomocí aplikace nebo uživatele. Během reálného výpadku by se měla použít možnost Neplánováno, která okamžitě propaguje sekundární objekt jako primární. Po opětovném obnovení primárních obnovení a jejich opětovném zpřístupnění systém automaticky označí obnovenou primární roli jako sekundární a uvede ji v aktuálním stavu. Z důvodu asynchronního charakteru replikace může během neplánovaných převzetí služeb při selhání dojít ke ztrátě malých objemů dat v případě, že primární operace proběhne úspěšně, než dojde k replikaci posledních změn sekundárního. Při převzetí služeb při selhání primárním objektem s více sekundárními počítači překonfiguruje systém automaticky znovu konfiguraci replikačních vztahů a propojí zbývající sekundární prostředí s nově povýšenou primární databází bez nutnosti zásahu uživatele. Po výpadku, který způsobil převzetí služeb při selhání, může být vhodné aplikaci vrátit do primární oblasti. K tomu je třeba vyvolat příkaz pro převzetí služeb při selhání s možností "plánováno".

## <a name="preparing-secondary-database-for-failover"></a>Příprava sekundární databáze pro převzetí služeb při selhání

Aby vaše aplikace mohla hned po převzetí služeb při selhání přistupovat k nové primární databázi, ujistěte se, že požadavky na ověřování pro sekundární server a databázi jsou správně nakonfigurované. Podrobnosti najdete v tématu [SQL Database Security po zotavení po havárii](sql-database-geo-replication-security-config.md). Aby se zajistilo dodržování předpisů po převzetí služeb při selhání, ujistěte se, že zásady uchovávání záloh v sekundární databázi odpovídají primárnímu. Tato nastavení nejsou součástí databáze a nereplikují se. Ve výchozím nastavení se sekundární bude konfigurovat s výchozí dobou uchování PITR sedmi dnů. Podrobnosti najdete v tématu [SQL Database automatizované zálohy](sql-database-automated-backups.md).

> [!IMPORTANT]
> Pokud je vaše databáze členem skupiny převzetí služeb při selhání, nemůžete iniciovat převzetí služeb při selhání pomocí příkazu geografické replikace faiover. Pro skupinu zvažte použití příkazu pro převzetí služeb při selhání. Pokud potřebujete převzít služby při selhání pro jednotlivé databáze, musíte je nejdřív odebrat ze skupiny převzetí služeb při selhání. Podrobnosti najdete v tématu [skupiny převzetí služeb při selhání](sql-database-auto-failover-group.md) . 


## <a name="configuring-secondary-database"></a>Konfigurace sekundární databáze

U primárních i sekundárních databází je potřeba, aby měly stejnou úroveň služby. Také se důrazně doporučuje, aby se sekundární databáze vytvořila se stejnou výpočetní velikostí (DTU nebo virtuální jádra) jako primární. Pokud primární databáze má velkou zátěžovou úlohu pro zápis, může se stát, že se sekundární s nižší výpočetní velikostí nedokáže s ním udržet. Způsobí prodlevu opakování u sekundárního a potenciálního nedostupnosti. U sekundární databáze, která zaostává za primární databází, také hrozí rozsáhlá ztráta dat v případě, že je potřeba provést vynucené převzetí služeb při selhání. Aby se tato rizika zmírnila, efektivní aktivní geografická replikace omezí rychlost protokolu primárního, aby bylo možné její sekundární zachycení zachytit. Druhou příčinou nevyvážené sekundární konfigurace je to, že po převzetí služeb při selhání bude výkon aplikace ovlivněn z důvodu nedostatečné výpočetní kapacity nového primárního objektu. Bude nutné provést upgrade na vyšší výpočetní výkon na potřebnou úroveň, dokud nebude výpadek omezen. 


> [!IMPORTANT]
> Publikovaný cíl RPO = 5 sec nelze zaručit, pokud je sekundární databáze konfigurována se stejnou velikostí výpočetní velikosti jako primární. 


Pokud se rozhodnete vytvořit sekundární s nižší výpočetní velikostí, graf procenta v/v v modulu Azure Portal poskytuje dobrý způsob, jak odhadnout minimální výpočetní Velikost sekundárního počítače, který je nutný k udržení zatížení replikace. Pokud je vaše primární databáze například P6 (1000 DTU) a její hodnota v/v v% je 50%, musí být sekundární hodnota aspoň P4 (500 DTU). Data v/v protokolu můžete také načíst pomocí zobrazení databáze [Sys. resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) nebo [Sys. DM _db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) .  Omezování je hlášeno jako stav čekání HADR_THROTTLE_LOG_RATE_MISMATCHED_SLO v zobrazeních databáze [Sys. DM _exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) a [Sys. DM _os_wait_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql) . 

Další informace o SQL Database velikosti výpočetních prostředků najdete v tématu [co jsou SQL Database úrovně služeb](sql-database-purchase-models.md).

## <a name="keeping-credentials-and-firewall-rules-in-sync"></a>Udržování synchronizovaných přihlašovacích údajů a pravidel brány firewall

Pro geograficky replikované databáze doporučujeme použít [pravidla brány firewall na úrovni databáze](sql-database-firewall-configure.md) , aby se tato pravidla mohla replikovat s databází, aby všechny sekundární databáze měla stejná pravidla FIREWALLU protokolu IP jako primární. Tento přístup eliminuje potřebu zákazníků ručně konfigurovat a udržovat pravidla brány firewall na serverech hostujících primární i sekundární databázi. Podobně použití [obsažených uživatelů databáze](sql-database-manage-logins.md) k přístupu k datům zajišťuje, že primární i sekundární databáze vždy mají stejné přihlašovací údaje uživatele, takže během převzetí služeb při selhání nedochází k výpadkům v důsledku neshody s přihlašovacími údaji a hesly. Díky přidání [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md)můžou zákazníci spravovat přístup uživatelů k primární i sekundární databázi a eliminují nutnost spravovat přihlašovací údaje v databázích zcela.

## <a name="upgrading-or-downgrading-primary-database"></a>Upgrade nebo downgrade primární databáze

Primární databázi můžete upgradovat nebo downgradovat na jinou výpočetní velikost (v rámci stejné úrovně služby, ne mezi Pro obecné účely a Pro důležité obchodní informace), aniž byste museli odpojit sekundární databáze. Při upgradu doporučujeme nejdřív upgradovat sekundární databázi a potom upgradovat primární. Když se downgrade, obrátí se pořadí: nejprve downgrade na primární a pak na downgrade sekundární. Když provedete upgrade nebo downgrade databáze na jinou úroveň služby, toto doporučení se vynutilo.

> [!NOTE]
> Pokud jste sekundární databázi vytvořili jako součást konfigurace skupiny převzetí služeb při selhání, nedoporučuje se ji převést na downgrade sekundární databáze. Tím zajistíte, že vaše datová úroveň má dostatečnou kapacitu pro zpracování pravidelného zatížení po aktivaci převzetí služeb při selhání.

> [!IMPORTANT]
> Primární databáze ve skupině převzetí služeb při selhání se nedá škálovat na vyšší úroveň, pokud se sekundární databáze nejdříve škáluje na vyšší úroveň. Pokud se pokusíte škálovat primární databázi před škálováním sekundární databáze, může se zobrazit následující chyba:
>
> `Error message: The source database 'Primaryserver.DBName' cannot have higher edition than the target database 'Secondaryserver.DBName'. Upgrade the edition on the target before upgrading the source.`
>

## <a name="preventing-the-loss-of-critical-data"></a>Zabránění ztrátě důležitých dat

V důsledku vysoké latence sítí WAN používá průběžné kopírování mechanismus asynchronní replikace. Asynchronní replikace způsobuje nenevyhnutelnou ztrátu dat, pokud dojde k selhání. Některé aplikace ale nemusí vyžadovat žádnou ztrátu dat. Aby bylo možné tyto kritické aktualizace chránit, může vývojář aplikace volat systémovou proceduru [sp_wait_for_database_copy_sync](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync) okamžitě po potvrzení transakce. Volání **sp_wait_for_database_copy_sync** blokuje volající vlákno, dokud se poslední potvrzená transakce nepřenesla do sekundární databáze. Nečeká ale na přenesení transakcí a jejich potvrzení na sekundárním počítači. **sp_wait_for_database_copy_sync** je vymezen na konkrétní odkaz průběžné kopírování. Tento postup může volat každý uživatel s právy pro připojení k primární databázi.

> [!NOTE]
> **sp_wait_for_database_copy_sync** zabraňuje ztrátě dat po převzetí služeb při selhání, ale nezaručuje úplnou synchronizaci pro přístup pro čtení. Zpoždění způsobené voláním procedury **sp_wait_for_database_copy_sync** může být významné a závisí na velikosti transakčního protokolu v době volání.

## <a name="monitoring-geo-replication-lag"></a>Sledování prodlevy geografické replikace

Chcete-li monitorovat prodlevu s ohledem na RPO, použijte *replication_lag_sec* sloupec [Sys. DM _geo_replication_link_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database) na primární databázi. V sekundách se zobrazuje prodleva mezi transakcemi potvrzenými na primárním a trvalým sekundárním počítači. Například Pokud je hodnota prodlevy 1 sekunda, znamená to, že v tomto okamžiku došlo k výpadku primárního objektu a dojde k zahájení převzetí služeb při selhání. 1 sekunda z posledních přechodů nebude uložena. 

Chcete-li změřit prodlevu s ohledem na změny primární databáze, které byly aplikovány na sekundární databázi, tj. k dispozici pro čtení ze sekundární databáze, porovnejte *last_commit* čas v sekundární databázi se stejnou hodnotou v primární databázi.

> [!NOTE]
> Někdy *replication_lag_sec* v primární databázi má hodnotu null, což znamená, že primární aktuálně nevíte, jak daleko je sekundární.   K tomu obvykle dochází po restartování procesu a mělo by to být přechodný stav. Zvažte možnost upozorňování aplikace, pokud *replication_lag_sec* vrátí hodnotu null po delší dobu. To by znamenalo, že sekundární databáze nemůže komunikovat s primárním kvůli chybě trvalého připojení. Existují taky podmínky, které by mohly způsobit, že rozdíl mezi *last_commit* časem v sekundárním a primárním databázi bude velký. Například Pokud se potvrzení provede na primárním počítači po dlouhou dobu bez jakýchkoli změn, bude rozdíl přejít až na velkou hodnotu, než se rychle vrátí na 0. Pokud rozdíl mezi těmito dvěma hodnotami zůstane dlouhý po dlouhou dobu, zvažte jeho chybový stav.


## <a name="programmatically-managing-active-geo-replication"></a>Programová správa aktivní geografické replikace

Jak je popsáno výše, aktivní geografická replikace se dá spravovat taky programově pomocí Azure PowerShell a REST API. V následujících tabulkách jsou popsány sady příkazů, které jsou k dispozici. Aktivní geografická replikace obsahuje sadu Azure Resource Manager rozhraní API pro správu, včetně rutin [Azure SQL Database REST API](https://docs.microsoft.com/rest/api/sql/) a [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview). Tato rozhraní API vyžadují použití skupin prostředků a podporují zabezpečení na základě rolí (RBAC). Další informace o tom, jak implementovat role přístupu, najdete v tématu [Access Control na základě rolí v Azure](../role-based-access-control/overview.md).

### <a name="t-sql-manage-failover-of-single-and-pooled-databases"></a>T-SQL: Správa převzetí služeb při selhání pro jednotlivé a sdružené databáze

> [!IMPORTANT]
> Tyto příkazy Transact-SQL se vztahují jenom na aktivní geografickou replikaci a nevztahují se na skupiny převzetí služeb při selhání. Nevztahují se také na spravované instance, protože podporují pouze skupiny převzetí služeb při selhání.

| Příkaz | Popis |
| --- | --- |
| [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current) |Pro vytvoření sekundární databáze pro existující databázi a spuštění replikace dat použijte argument přidat sekundární na SERVER. |
| [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current) |Použití převzetí služeb při selhání nebo FORCE_FAILOVER_ALLOW_DATA_LOSS k přepnutí sekundární databáze na primární pro zahájení převzetí služeb při selhání |
| [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current) |Pomocí odebrat sekundární na serveru ukončete replikaci dat mezi SQL Database a zadanou sekundární databází. |
| [sys. Geo _replication_links](/sql/relational-databases/system-dynamic-management-views/sys-geo-replication-links-azure-sql-database) |Vrátí informace o všech stávajících odkazech replikace pro každou databázi na serveru Azure SQL Database. |
| [sys. DM _geo_replication_link_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database) |Získá čas poslední replikace, prodlevu poslední replikace a další informace o odkazu replikace pro danou databázi SQL. |
| [sys. DM _operation_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) |Zobrazuje stav všech databázových operací, včetně stavu replikačních odkazů. |
| [sp_wait_for_database_copy_sync](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync) |způsobí, že aplikace počká, až budou všechny potvrzené transakce replikovány a potvrzeny aktivní sekundární databází. |
|  | |

### <a name="powershell-manage-failover-of-single-and-pooled-databases"></a>PowerShell: Správa převzetí služeb při selhání pro jednotlivé a sdružené databáze

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Modul PowerShell Azure Resource Manager je stále podporován Azure SQL Database, ale všechny budoucí vývojové prostředí jsou pro modul AZ. SQL. Tyto rutiny naleznete v tématu [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenty pro příkazy v modulech AZ a v modulech AzureRm jsou v podstatě identické.

| Rutina | Popis |
| --- | --- |
| [Get-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabase) |Získá jednu nebo více databází. |
| [New-AzSqlDatabaseSecondary](https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabasesecondary) |Vytvoří sekundární databázi pro existující databázi a spustí replikaci dat. |
| [Set-AzSqlDatabaseSecondary](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabasesecondary) |Přepne sekundární databázi na primární a zahájí tak převzetí služeb při selhání. |
| [Remove-AzSqlDatabaseSecondary](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqldatabasesecondary) |Ukončí replikaci dat mezi službou SQL Database a zadanou sekundární databází. |
| [Get-AzSqlDatabaseReplicationLink](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasereplicationlink) |Získá vazby geografické replikace mezi službou Azure SQL Database a skupinou prostředků nebo SQL Serverem. |
|  | |

> [!IMPORTANT]
> Ukázkové skripty najdete v tématu [Konfigurace a převzetí služeb při selhání v izolovaných databázích pomocí aktivní geografické replikace](scripts/sql-database-setup-geodr-and-failover-database-powershell.md) a [Konfigurace a převzetí služeb při selhání databáze ve fondu pomocí aktivní geografické replikace](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md).

### <a name="rest-api-manage-failover-of-single-and-pooled-databases"></a>REST API: Správa převzetí služeb při selhání pro jednotlivé a sdružené databáze

| Rozhraní API | Popis |
| --- | --- |
| [Vytvořit nebo aktualizovat databázi (createMode = Restore)](https://docs.microsoft.com/rest/api/sql/databases/createorupdate) |Vytvoří, aktualizuje nebo obnoví primární nebo sekundární databázi. |
| [Získat stav databáze pro vytvoření nebo aktualizaci](https://docs.microsoft.com/rest/api/sql/databases/createorupdate) |Vrátí stav během operace vytvoření. |
| [Nastavit sekundární databázi jako primární (plánované převzetí služeb při selhání)](https://docs.microsoft.com/rest/api/sql/replicationlinks/failover) |Nastaví, která sekundární databáze je primární databází převzetím služeb při selhání z aktuální primární databáze. **Tato možnost není pro spravovanou instanci podporována.**|
| [Nastavit sekundární databázi jako primární (neplánované převzetí služeb při selhání)](https://docs.microsoft.com/rest/api/sql/replicationlinks/failoverallowdataloss) |Nastaví, která sekundární databáze je primární databází převzetím služeb při selhání z aktuální primární databáze. Tato operace může způsobit ztrátu dat. **Tato možnost není pro spravovanou instanci podporována.**|
| [Získat odkaz replikace](https://docs.microsoft.com/rest/api/sql/replicationlinks/get) |Získá konkrétní odkaz replikace pro danou databázi SQL v partnerství geografické replikace. Načte informace viditelné v zobrazení katalogu sys. Geo _replication_links. **Tato možnost není pro spravovanou instanci podporována.**|
| [Odkazy replikace – seznam podle databáze](https://docs.microsoft.com/rest/api/sql/replicationlinks/listbydatabase) | Získá všechny odkazy replikace pro danou databázi SQL v partnerství geografické replikace. Načte informace viditelné v zobrazení katalogu sys. Geo _replication_links. |
| [Odstranit odkaz replikace](https://docs.microsoft.com/rest/api/sql/replicationlinks/delete) | Odstraní odkaz replikace databáze. Nejde provést během převzetí služeb při selhání. |
|  | |

## <a name="next-steps"></a>Další kroky

- Ukázkové skripty najdete v těchto tématech:
  - [Konfigurace a převzetí služeb při selhání izolované databáze s využitím aktivní geografické replikace](scripts/sql-database-setup-geodr-and-failover-database-powershell.md)
  - [Konfigurace a převzetí služeb při selhání databáze ve fondu s využitím aktivní geografické replikace](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md)
- SQL Database podporuje také skupiny automatického převzetí služeb při selhání. Další informace najdete v tématu používání [skupin s automatickým převzetím služeb při selhání](sql-database-auto-failover-group.md).
- Přehled provozní kontinuity a scénářů najdete v tématu [Přehled provozní kontinuity](sql-database-business-continuity.md) .
- Další informace o Azure SQL Database automatizovaných zálohách najdete v tématu [SQL Database automatizované zálohy](sql-database-automated-backups.md).
- Další informace o použití automatizovaných záloh pro obnovení najdete v tématu [obnovení databáze ze zálohy iniciované službou](sql-database-recovery-using-backups.md).
- Další informace o požadavcích na ověřování nového primárního serveru a databáze najdete v článku [SQL Database zabezpečení po zotavení po havárii](sql-database-geo-replication-security-config.md).
