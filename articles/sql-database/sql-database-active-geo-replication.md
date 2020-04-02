---
title: Aktivní geografická replikace
description: Pomocí aktivní geografické replikace můžete vytvořit čitelné sekundární databáze jednotlivých databází ve stejném nebo jiném datovém centru (oblasti).
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
ms.date: 02/17/2020
ms.openlocfilehash: b80b58d64ea27df95c2704243d8a89fa6ca12e2a
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548514"
---
# <a name="creating-and-using-active-geo-replication"></a>Vytváření a používání aktivní geografické replikace

Aktivní geografická replikace je funkce Azure SQL Database, která umožňuje vytvářet čitelné sekundární databáze jednotlivých databází na serveru SQL Database ve stejném nebo jiném datovém centru (oblasti).

> [!NOTE]
> Aktivní geografická replikace není spravovanou instancí podporována. Pro geografické převzetí služeb při selhání spravovaných instancí použijte [skupiny automatického převzetí služeb při selhání](sql-database-auto-failover-group.md).

Aktivní geografická replikace je navržena jako řešení kontinuity provozu, které umožňuje aplikaci provádět rychlé zotavení po havárii jednotlivých databází v případě regionální havárie nebo rozsáhlého výpadku. Pokud je povolena geografická replikace, aplikace může zahájit převzetí služeb při selhání do sekundární databáze v jiné oblasti Azure. Až čtyři sekundární jsou podporovány ve stejné nebo různé oblasti a sekundární lze také použít pro dotazy přístup jen pro čtení. Převzetí služeb při selhání musí být zahájeno ručně aplikací nebo uživatelem. Po převzetí služeb při selhání má nové primární má jiný koncový bod připojení. Následující diagram znázorňuje typickou konfiguraci geograficky redundantní cloudové aplikace pomocí aktivní geografické replikace.

![aktivní geografická replikace](./media/sql-database-active-geo-replication/geo-replication.png )

> [!IMPORTANT]
> SQL Database také podporuje skupiny automatického převzetí služeb při selhání. Další informace naleznete v tématu použití [skupin s automatickým převzetím služeb při selhání](sql-database-auto-failover-group.md). Aktivní geografická replikace také není podporována pro databáze vytvořené v rámci spravované instance. Zvažte použití [skupin s podporou převzetí služeb při selhání](sql-database-auto-failover-group.md) se spravovanými instancemi.

Pokud z nějakého důvodu primární databáze selže nebo jednoduše je třeba převést do offline, můžete zahájit převzetí služeb při selhání do některé z sekundárních databází. Při aktivaci převzetí služeb při selhání do jedné ze sekundárních databází, všechny ostatní sekundární jsou automaticky propojeny s novou primární.

Replikaci a převzetí služeb při selhání jednotlivých databází nebo sady databází na serveru nebo v elastickém fondu můžete spravovat pomocí aktivní geografické replikace. Můžete to udělat pomocí:

- [Portál Azure](sql-database-geo-replication-portal.md)
- [PowerShell: Jedna databáze](scripts/sql-database-setup-geodr-and-failover-database-powershell.md)
- [PowerShell: Elastický fond](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md)
- [Transact-SQL: Jedna databáze nebo elastický fond](/sql/t-sql/statements/alter-database-azure-sql-database)
- [ROZHRANÍ REST API: Jedna databáze](https://docs.microsoft.com/rest/api/sql/replicationlinks)


Aktivní geografická replikace využívá [technologii Always On](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server) serveru SQL Server k asynchronní replikaci potvrzených transakcí v primární databázi do sekundární databáze pomocí izolace snímků. Skupiny s automatickým převzetím služeb při selhání poskytují sémantiku skupiny nad aktivní geografickou replikací, ale používá se stejný mechanismus asynchronní replikace. Zatímco v daném okamžiku sekundární databáze může být mírně za primární databáze, sekundární data je zaručeno, že nikdy částečné transakce. Redundance mezi oblastmi umožňuje aplikacím rychle se zotavit z trvalé ztráty celého datového centra nebo částí datového centra způsobených přírodními katastrofami, katastrofickými lidskými chybami nebo škodlivými činy. Konkrétní data RPO naleznete na [přehledu kontinuity provozu](sql-database-business-continuity.md).

> [!NOTE]
> Pokud dojde k selhání sítě mezi dvěma oblastmi, opakujíme každých 10 sekund obnovit připojení.
> [!IMPORTANT]
> Chcete-li zaručit, že kritická změna v primární databázi je replikována do sekundární před převzetím služeb při selhání, můžete vynutit synchronizaci zajistit replikaci kritických změn (například aktualizace hesla). Vynucená synchronizace má vliv na výkon, protože blokuje volající vlákno, dokud nejsou replikovány všechny potvrzené transakce. Podrobnosti naleznete [v sp_wait_for_database_copy_sync](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync). Chcete-li sledovat zpoždění replikace mezi primární databází a geograficky sekundární, naleznete [v sys.dm_geo_replication_link_status](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database).

Následující obrázek znázorňuje příklad aktivní geografické replikace nakonfigurované s primární v oblasti USA – sever – střed a sekundární v oblasti USA – střed.

![vztah geografické replikace](./media/sql-database-active-geo-replication/geo-replication-relationship.png)

Vzhledem k tomu, že sekundární databáze jsou čitelné, lze je použít k přepočtu úloh jen pro čtení, jako jsou úlohy vykazování. Pokud používáte aktivní geografickou replikaci, je možné vytvořit sekundární databázi ve stejné oblasti s primární, ale nezvyšuje odolnost aplikace vůči katastrofickým selháním. Pokud používáte skupiny automatického převzetí služeb při selhání, sekundární databáze je vždy vytvořena v jiné oblasti.

Kromě zotavení po havárii lze aktivní geografickou replikaci použít v následujících scénářích:

- **Migrace databáze**: Aktivní geografickou replikaci můžete použít k migraci databáze z jednoho serveru na jiný online s minimálními prostoji.
- **Upgrady aplikací**: Můžete vytvořit další sekundární jako zpět nouzi kopii během upgradu aplikace.

Přidání redundance databáze mezi datová centra je pouze součástí řešení, aby bylo dosaženo skutečné kontinuity podnikání. Obnovení aplikace (služby) end-to-end po selhání katastrofální vyžaduje obnovení všech součástí, které tvoří službu a všechny závislé služby. Příklady těchto součástí zahrnují klientský software (například prohlížeč s vlastním JavaScriptem), webové front-endy, úložiště a DNS. Je důležité, aby všechny součásti jsou odolné vůči stejným selháním a budou k dispozici v rámci cíle doby obnovení (RTO) vaší aplikace. Proto je třeba identifikovat všechny závislé služby a pochopit záruky a možnosti, které poskytují. Potom je nutné provést odpovídající kroky k zajištění, že vaše služba funguje během převzetí služeb převzetí služeb, na kterých závisí. Další informace o navrhování řešení pro zotavení po havárii naleznete v [tématu Návrh cloudových řešení pro zotavení po havárii pomocí aktivní geografické replikace](sql-database-designing-cloud-solutions-for-disaster-recovery.md).

## <a name="active-geo-replication-terminology-and-capabilities"></a>Aktivní terminologie a možnosti geografické replikace

- **Automatická asynchronní replikace**

  Sekundární databázi lze vytvořit pouze přidáním do existující databáze. Sekundární lze vytvořit v libovolném serveru Azure SQL Database. Po vytvoření je sekundární databáze naplněna daty zkopírovanými z primární databáze. Tento proces se nazývá výsev. Po vytvoření a osivu sekundární databáze jsou aktualizace primární databáze automaticky replikovány do sekundární databáze. Asynchronní replikace znamená, že transakce jsou potvrzeny v primární databázi před jejich replikací do sekundární databáze.

- **Čitelné sekundární databáze**

  Aplikace může přistupovat k sekundární databázi pro operace jen pro čtení pomocí stejné nebo různé objekty zabezpečení používané pro přístup k primární databázi. Sekundární databáze pracují v režimu izolace snímek zajistit replikace aktualizace primární (záznam protokolu) není zpožděn dotazy spuštěny na sekundární.

> [!NOTE]
> Záznam protokolu je zpožděn v sekundární databázi, pokud jsou aktualizace schématu na Primární. Ten vyžaduje zámek schématu v sekundární databázi.
> [!IMPORTANT]
> Geografickou replikaci můžete použít k vytvoření sekundární databáze ve stejné oblasti jako primární. Toto sekundární můžete použít k vyrovnávání zatížení úlohjen pro čtení ve stejné oblasti. Sekundární databáze ve stejné oblasti však neposkytuje další odolnost proti chybám, a proto není vhodným cílem převzetí služeb při selhání pro zotavení po havárii. Také nebude zaručit izolaci zóny dostupnosti. K dosažení izolace zóny dostupnosti použijte úroveň služeb Business critical nebo Premium s [redundantní konfigurací zóny.](sql-database-high-availability.md#zone-redundant-configuration)   
>

- **Plánované převzetí služeb při selhání**

  Plánované převzetí služeb při selhání přepne role primární a sekundární databáze po dokončení úplné synchronizace. Jedná se o online operaci, která nevede ke ztrátě dat. Čas operace závisí na velikosti transakční protokol na primární, které je třeba synchronizovat. Plánované převzetí služeb při selhání je určeno pro následující scénáře: (a) provádět cvičení zotavení po havárii v produkčním prostředí, pokud ztráta dat není přijatelná; b) přemístit databázi do jiného regionu; a (c) vrátit databázi do primární oblasti po výpadku byl zmírněn (navrácení služeb po obnovení).

- **Neplánované převzetí služeb při selhání**

  Neplánované nebo vynucené převzetí služeb při selhání okamžitě přepne sekundární primární roli bez synchronizace s primární. Všechny transakce potvrzené primární, ale nejsou replikovány do sekundární budou ztraceny. Tato operace je navržena jako metoda obnovení během výpadků, když primární není přístupný, ale dostupnost databáze musí být rychle obnovena. Když je původní primární je zpět online, bude automaticky znovu připojit a stát se novým sekundární. Všechny nesynchronizované transakce před převzetím služeb při selhání budou zachovány v záložním souboru, ale nebudou synchronizovány s novým primárním, aby se zabránilo konfliktům. Tyto transakce budou muset být ručně sloučeny s nejnovější verzí primární databáze.
 
- **Více čitelných sekundárních**

  Pro každou primární databázi lze vytvořit až 4 sekundární databáze. Pokud existuje pouze jedna sekundární databáze a selže, aplikace je vystavena vyššímu riziku, dokud není vytvořena nová sekundární databáze. Pokud existuje více sekundárních databází, aplikace zůstane chráněna i v případě, že jedna ze sekundárních databází selže. Další sekundární sekundární kapacity lze také škálovat úlohy jen pro čtení

  > [!NOTE]
  > Pokud používáte aktivní geografickou replikaci k vytvoření globálně distribuované aplikace a potřebujete poskytnout přístup jen pro čtení k datům ve více než čtyřech oblastech, můžete vytvořit sekundární sekundární (proces označovaný jako řetězení). Tímto způsobem můžete dosáhnout prakticky neomezené škálování replikace databáze. Kromě toho zřetězení snižuje režii replikace z primární databáze. Kompromis je zvýšené zpoždění replikace v sekundárních databázích nejvíce listových.

- **Geografická replikace databází v elastickém fondu**

  Každá sekundární databáze se může samostatně účastnit elastického fondu nebo vůbec nebýt v žádném elastickém fondu. Volba fondu pro každou sekundární databázi je samostatná a nezávisí na konfiguraci jiné sekundární databáze (primární nebo sekundární). Každý elastický fond je obsažen v rámci jedné oblasti, proto více sekundárních databází ve stejné topologii nikdy sdílet elastický fond.


- **Uživatelem řízené převzetí služeb při selhání a navrácení služeb po selhání**

  Sekundární databázi lze explicitně přepnout na primární roli kdykoli aplikací nebo uživatelem. Během skutečného výpadku by měla být použita možnost "neplánované", která okamžitě podporuje sekundární, aby byla primární. Pokud se selhání primární obnoví a je k dispozici znovu, systém automaticky označí obnovené primární jako sekundární a přivést ji aktuální s novou primární. Vzhledem k asynchronní povaze replikace může dojít ke ztrátě malého množství dat během neplánovaných převzetí služeb při selhání, pokud primární selže před replikací nejnovějších změn sekundární. Při primární s více sekundárních sekundárních selhání převzetí selhání, systém automaticky překonfiguruje vztahy replikace a propojí zbývající sekundární s nově povýšen primární bez nutnosti zásahu uživatele. Po výpadku, který způsobil převzetí služeb při selhání je zmírněna, může být žádoucí vrátit aplikaci do primární oblasti. Chcete-li to provést, příkaz převzetí služeb při selhání by měl být vyvolán pomocí možnosti "plánované".

## <a name="preparing-secondary-database-for-failover"></a>Příprava sekundární databáze pro převzetí služeb při selhání

Chcete-li zajistit, aby vaše aplikace mohla okamžitě přistupovat k nové primární po převzetí služeb při selhání, ujistěte se, že požadavky na ověřování pro sekundární server a databázi jsou správně nakonfigurovány. Podrobnosti naleznete v [tématu sql database security after disaster recovery](sql-database-geo-replication-security-config.md). Chcete-li zaručit dodržování předpisů po převzetí služeb při selhání, ujistěte se, že zásady uchovávání záloh na sekundární databázi odpovídají zásadě primární. Tato nastavení nejsou součástí databáze a nejsou replikována. Ve výchozím nastavení bude sekundární nakonfigurován s výchozí dobu uchovávání PITR sedm dní. Podrobnosti naleznete v [tématu automatické zálohování databáze SQL](sql-database-automated-backups.md)Database .

> [!IMPORTANT]
> Pokud je databáze členem skupiny s podporou převzetí služeb při selhání, nelze zahájit její převzetí služeb při selhání pomocí příkazu faiover geografické replikace. Zvažte použití příkazu převzetí služeb při selhání pro skupinu. Pokud potřebujete převzetí služeb při selhání jednotlivé databáze, je nutné odebrat ze skupiny převzetí služeb při selhání jako první. Podrobnosti naleznete ve [skupinách s podporou převzetí služeb při selhání.](sql-database-auto-failover-group.md) 


## <a name="configuring-secondary-database"></a>Konfigurace sekundární databáze

Primární i sekundární databáze musí mít stejnou úroveň služby. Důrazně se také doporučuje, aby sekundární databáze byla vytvořena se stejnou výpočetní velikostí (DTU nebo virtuálními jádry) jako primární. Pokud primární databáze dochází k velké zatížení zápisu, sekundární s nižší výpočetní velikost nemusí být schopen držet krok s ním. To způsobí zpoždění opakování na sekundární a potenciální nedostupnost. Sekundární databáze, která zaostává za primární také rizika velké ztráty dat v případě, že vynucené převzetí služeb při selhání je požadováno. Chcete-li tato rizika zmírnit, efektivní aktivní geografická replikace omezí primární rychlost protokolu, aby jeho sekundární databáze dohnala. Dalším důsledkem nevyvážené sekundární konfigurace je, že po převzetí služeb při selhání bude výkon aplikace trpět z důvodu nedostatečné výpočetní kapacity nové primární. Bude nutné upgradovat na vyšší výpočetní prostředky na potřebnou úroveň, což nebude možné, dokud nebude zmírněn výpadek. 


> [!IMPORTANT]
> Publikovaný RPO = 5 sekund nelze zaručit, pokud sekundární databáze je nakonfigurován se stejnou velikostí výpočetních prostředků jako primární. 


Pokud se rozhodnete vytvořit sekundární s nižší výpočetní velikost, graf procentuálního indexu VO na webu Azure Portal poskytuje dobrý způsob, jak odhadnout minimální výpočetní velikost sekundární, která je vyžadována k udržení zatížení replikace. Například pokud primární databáze je P6 (1000 DTU) a jeho vi procent protokolu je 50 % sekundární musí být alespoň P4 (500 DTU). Můžete také načíst data vi protokolu pomocí zobrazení [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) nebo [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) databáze.  Omezení je hlášeno jako HADR_THROTTLE_LOG_RATE_MISMATCHED_SLO stavu čekání v zobrazeních databáze [sys.dm_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) a [sys.dm_os_wait_stats.](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql) 

Další informace o výpočetnívelikosti databáze SQL naleznete v tématu [Co jsou vrstvy databázové služby SQL](sql-database-purchase-models.md).

## <a name="cross-subscription-geo-replication"></a>Geografická replikace s křížovým odběrem

Chcete-li nastavit aktivní geografickou replikaci mezi dvěma databázemi, které patří do různých předplatných (ať už pod stejným klientem nebo ne), musíte postupovat podle zvláštního postupu popsaného v této části.  Postup je založen na příkazech SQL a vyžaduje: 

- Vytvoření privilegovaného přihlášení na obou serverech
- Přidání adresy IP do seznamu povolených klientů provádějících změnu na obou serverech (například ip adresy hostitele se systémem SQL Server Management Studio). 

Klient provádějící změny potřebuje přístup k síti k primárnímu serveru. Přestože musí být do seznamu povolených adres na sekundárním serveru přidána stejná adresa IP klienta, není připojení k sekundárnímu serveru nezbytně nutné. 

### <a name="on-the-master-of-the-primary-server"></a>Na hlavním serveru primárního serveru

1. Přidejte adresu IP do seznamu povolených klientů provádějících změny (další informace naleznete v [tématu Konfigurace brány firewall](sql-database-firewall-configure.md)). 
1. Vytvořte přihlašovací údaje vyhrazené pro nastavení aktivní geografické replikace (a podle potřeby upravte přihlašovací údaje):

   ```sql
   create login geodrsetup with password = 'ComplexPassword01'
   ```

1. Vytvořte odpovídajícího uživatele a přiřaďte ho k roli dbmanager: 

   ```sql
   create user geodrsetup for login geodrsetup
   alter role dbmanager add member geodrsetup
   ```

1. Poznamenejte si SID nového přihlášení pomocí tohoto dotazu: 

   ```sql
   select sid from sys.sql_logins where name = 'geodrsetup'
   ```

### <a name="on-the-source-database-on-the-primary-server"></a>Ve zdrojové databázi na primárním serveru

1. Vytvořte uživatele pro stejné přihlášení:

   ```sql
   create user geodrsetup for login geodrsetup
   ```

1. Přidejte uživatele do role db_owner:

   ```sql
   alter role db_owner add member geodrsetup
   ```

### <a name="on-the-master-of-the-secondary-server"></a>Na hlavním serveru sekundárního serveru 

1. Přidejte adresu IP do seznamu povolených položek klienta, který změny provádí. Musí mít stejnou přesnou IP adresu primárního serveru. 
1. Vytvořte stejné přihlášení jako na primárním serveru pomocí stejného uživatelského jména heslo a SID: 

   ```sql
   create login geodrsetup with password = 'ComplexPassword01', sid=0x010600000000006400000000000000001C98F52B95D9C84BBBA8578FACE37C3E
   ```

1. Vytvořte odpovídajícího uživatele a přiřaďte ho k roli dbmanager:

   ```sql
   create user geodrsetup for login geodrsetup;
   alter role dbmanager add member geodrsetup
   ```

### <a name="on-the-master-of-the-primary-server"></a>Na hlavním serveru primárního serveru

1. Přihlaste se k hlavnímu serveru primárního serveru pomocí nového přihlášení. 
1. Vytvořte sekundární repliku zdrojové databáze na sekundárním serveru (podle potřeby upravte název databáze a název serveru):

   ```sql
   alter database dbrep add secondary on server <servername>
   ```

Po počátečním nastavení lze odebrat vytvořené uživatele, přihlášení a pravidla brány firewall. 


## <a name="keeping-credentials-and-firewall-rules-in-sync"></a>Synchronizace pověření a pravidel brány firewall

Doporučujeme používat [pravidla brány firewall IP na úrovni databáze](sql-database-firewall-configure.md) pro geograficky replikované databáze, aby tato pravidla mohla být replikována spolu s databází, aby bylo zajištěno, že všechny sekundární databáze mají stejná pravidla brány firewall IP jako primární. Tento přístup eliminuje potřebu zákazníků ručně konfigurovat a udržovat pravidla brány firewall na serverech hostujících primární i sekundární databázi. Podobně pomocí [obsažené uživatele databáze](sql-database-manage-logins.md) pro přístup k datům zajišťuje primární a sekundární databáze vždy mají stejná pověření uživatele, takže během převzetí služeb při selhání, není žádné přerušení z důvodu neshody s přihlášení a hesla. Díky přidání [služby Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md)mohou zákazníci spravovat přístup uživatelů k primárním i sekundárním databázím a eliminovat potřebu úplné správy přihlašovacích údajů v databázích.

## <a name="upgrading-or-downgrading-primary-database"></a>Upgrade nebo snížení stupně utajení primární databáze

Primární databázi můžete upgradovat nebo depozitovat na jinou výpočetní velikost (v rámci stejné úrovně služby, nikoli mezi obecnými a důležitými pro podniky), aniž byste odpojili sekundární databáze. Při upgradu doporučujeme nejprve inovovat sekundární databázi a potom inovovat primární. Při downgradu pořadí, obrácení pořadí: downgrade primární první a potom downgrade sekundární. Při upgradu nebo downgrade databáze na jinou úroveň služby, toto doporučení je vynuceno.

> [!NOTE]
> Pokud jste vytvořili sekundární databázi jako součást konfigurace skupiny převzetí služeb při selhání, nedoporučuje se přechod na nižší verzi sekundární databáze. Tím zajistíte, že vaše datová vrstva má dostatečnou kapacitu pro zpracování běžné úlohy po aktivaci převzetí služeb při selhání.

> [!IMPORTANT]
> Primární databáze ve skupině převzetí služeb při selhání nelze škálovat na vyšší úroveň, pokud sekundární databáze je nejprve škálované na vyšší vrstvu. Pokud se pokusíte škálovat primární databázi před škálování sekundární databáze, může se zobrazit následující chyba:
>
> `Error message: The source database 'Primaryserver.DBName' cannot have higher edition than the target database 'Secondaryserver.DBName'. Upgrade the edition on the target before upgrading the source.`
>

## <a name="preventing-the-loss-of-critical-data"></a>Předcházení ztrátě důležitých dat

Vzhledem k vysoké latenci rozsáhlých sítí používá souvislá kopie mechanismus asynchronní replikace. Asynchronní replikace způsobí, že některé ztráty dat nevyhnutelné, pokud dojde k selhání. Některé aplikace však může vyžadovat žádnou ztrátu dat. Chcete-li chránit tyto důležité aktualizace, může vývojář aplikace zavolat [proceduru sp_wait_for_database_copy_sync](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync) systému ihned po potvrzení transakce. Volání **sp_wait_for_database_copy_sync** blokuje volající vlákno, dokud nebyla do sekundární databáze přenesena poslední potvrzená transakce. Však nečeká na přehrání předávaných transakcí a potvrzené na sekundární. **sp_wait_for_database_copy_sync** je vymezen na konkrétní souvislé kopírovací propojení. Tento postup může volat každý uživatel s právy připojení k primární databázi.

> [!NOTE]
> **sp_wait_for_database_copy_sync** zabraňuje ztrátě dat po převzetí služeb při selhání, ale nezaručuje úplnou synchronizaci pro přístup pro čtení. Zpoždění způsobené volání procedury **sp_wait_for_database_copy_sync** může být významné a závisí na velikosti transakční protokol v době volání.

## <a name="monitoring-geo-replication-lag"></a>Sledování zpoždění geografické replikace

Chcete-li sledovat zpoždění s ohledem na RPO, použijte *replication_lag_sec* sloupec [sys.dm_geo_replication_link_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database) v primární databázi. Zobrazuje prodlevy v sekundách mezi transakce potvrzené na primární a trvalé na sekundární. Například Pokud je hodnota lagu 1 sekunda, znamená to, že pokud je primární ovlivněna výpadku v tomto okamžiku a převzetí služeb při selhání je zahájeno, 1 sekunda z nejnovějších přechodů nebudou uloženy. 

Chcete-li měřit zpoždění s ohledem na změny v primární databázi, které byly použity na sekundární, tj *last_commit.*

> [!NOTE]
> Někdy *replication_lag_sec* v primární databázi má hodnotu NULL, což znamená, že primární neví, jak daleko je sekundární.   K tomu obvykle dochází po restartování procesu a měla by být přechodná podmínka. Zvažte upozornění aplikace, pokud *replication_lag_sec* vrátí hodnotu NULL po delší dobu. To by znamenalo, že sekundární databáze nemůže komunikovat s primární z důvodu selhání trvalépřipojení. Existují také podmínky, které by mohly způsobit rozdíl mezi *last_commit* čas na sekundární a na primární databázi, aby se stal velký. Například Pokud potvrzení je provedena na primární po dlouhé období žádné změny, rozdíl vyskočí na velkou hodnotu před rychle vrátí na 0. Považujte to za chybový stav, když rozdíl mezi těmito dvěma hodnotami zůstává velký po dlouhou dobu.


## <a name="programmatically-managing-active-geo-replication"></a>Programová správa aktivní geografické replikace

Jak již bylo popsáno dříve, aktivní geografickou replikaci lze také spravovat programově pomocí Azure PowerShell a rozhraní REST API. Následující tabulky popisují sadu příkazů, které jsou k dispozici. Aktivní geografická replikace zahrnuje sadu rozhraní API Azure Pro Správce prostředků pro správu, včetně [rozhraní AZURE SQL Database REST API](https://docs.microsoft.com/rest/api/sql/) a rutin Azure [PowerShell](https://docs.microsoft.com/powershell/azure/overview). Tato řešení API vyžadují použití skupin prostředků a podporují zabezpečení založené na rolích (RBAC). Další informace o tom, jak implementovat přístupové role, najdete v [tématu Řízení přístupu na základě rolí Azure](../role-based-access-control/overview.md).

### <a name="t-sql-manage-failover-of-single-and-pooled-databases"></a>T-SQL: Správa převzetí služeb při selhání jedné a sdružené databáze

> [!IMPORTANT]
> Tyto příkazy Transact-SQL se vztahují pouze na aktivní geografickou replikaci a nevztahují se na skupiny s podporou převzetí služeb při selhání. Jako takové se také nevztahují na spravované instance, protože podporují pouze skupiny s podporou převzetí služeb při selhání.

| Příkaz | Popis |
| --- | --- |
| [ZMĚNIT DATABÁZI](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current) |Použití argumentu ADD SECONDARY ON SERVER k vytvoření sekundární databáze pro existující databázi a spuštění replikace dat |
| [ZMĚNIT DATABÁZI](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current) |Použití funkce FAILOVER nebo FORCE_FAILOVER_ALLOW_DATA_LOSS k přepnutí sekundární databáze jako primární pro zahájení převzetí služeb při selhání |
| [ZMĚNIT DATABÁZI](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current) |Pomocí funkce REMOVE SECONDARY ON SERVER ukončete replikaci dat mezi databází SQL a zadanou sekundární databází. |
| [sys.geo_replication_links](/sql/relational-databases/system-dynamic-management-views/sys-geo-replication-links-azure-sql-database) |Vrátí informace o všech existujících replikačních odkazech pro každou databázi na serveru Azure SQL Database. |
| [sys.dm_geo_replication_link_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database) |Získá čas poslední replikace, poslední zpoždění replikace a další informace o propojení replikace pro danou databázi SQL. |
| [sys.dm_operation_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) |Zobrazuje stav všech databázových operací včetně stavu replikačních propojení. |
| [sp_wait_for_database_copy_sync](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync) |způsobí, že aplikace čekat, dokud všechny potvrzené transakce jsou replikovány a potvrzeny aktivní sekundární databáze. |
|  | |

### <a name="powershell-manage-failover-of-single-and-pooled-databases"></a>Prostředí PowerShell: Správa převzetí služeb při selhání jedné a sdružené databáze

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Modul PowerShell Azure Resource Manager je stále podporovaný službou Azure SQL Database, ale veškerý budoucí vývoj je pro modul Az.Sql. Tyto rutiny naleznete v tématu [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenty pro příkazy v modulu Az a v modulech AzureRm jsou v podstatě identické.

| Rutina | Popis |
| --- | --- |
| [Databáze Get-AzSql](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabase) |Získá jednu nebo více databází. |
| [New-AzSqlDatabaseSekundární](https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabasesecondary) |Vytvoří sekundární databázi pro existující databázi a spustí replikaci dat. |
| [Set-AzSqlDatabaseSekundární](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabasesecondary) |Přepne sekundární databázi na primární a zahájí tak převzetí služeb při selhání. |
| [Odebrat-AzSqlDatabaseSekundární](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqldatabasesecondary) |Ukončí replikaci dat mezi službou SQL Database a zadanou sekundární databází. |
| [Get-AzSqlDatabaseReplicationLink](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasereplicationlink) |Získá vazby geografické replikace mezi službou Azure SQL Database a skupinou prostředků nebo SQL Serverem. |
|  | |

> [!IMPORTANT]
> Ukázkové skripty naleznete [v tématu Konfigurace a převzetí služeb při selhání jedné databáze pomocí aktivní geografické replikace](scripts/sql-database-setup-geodr-and-failover-database-powershell.md) [a Konfigurace a převzetí služeb při selhání sdružené databáze pomocí aktivní geografické replikace](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md).

### <a name="rest-api-manage-failover-of-single-and-pooled-databases"></a>ROZHRANÍ REST API: Správa převzetí služeb při selhání jedné a sdružené databáze

| rozhraní API | Popis |
| --- | --- |
| [Vytvořit nebo aktualizovat databázi (createMode=Obnovit)](https://docs.microsoft.com/rest/api/sql/databases/createorupdate) |Vytvoří, aktualizuje nebo obnoví primární nebo sekundární databázi. |
| [Získat stav vytvořit nebo aktualizovat databázi](https://docs.microsoft.com/rest/api/sql/databases/createorupdate) |Vrátí stav během operace vytvoření. |
| [Nastavit sekundární databázi jako primární (plánované převzetí služeb při selhání)](https://docs.microsoft.com/rest/api/sql/replicationlinks/failover) |Nastaví, která sekundární databáze je primární převzetím služby při selhání z aktuální primární databáze. **Tato možnost není podporována pro spravovanou instanci.**|
| [Nastavit sekundární databázi jako primární (neplánované převzetí služeb při selhání)](https://docs.microsoft.com/rest/api/sql/replicationlinks/failoverallowdataloss) |Nastaví, která sekundární databáze je primární převzetím služby při selhání z aktuální primární databáze. Tato operace může mít za následek ztrátu dat. **Tato možnost není podporována pro spravovanou instanci.**|
| [Získat odkaz replikace](https://docs.microsoft.com/rest/api/sql/replicationlinks/get) |Získá konkrétní replikační odkaz pro danou databázi SQL v partnerství geografické replikace. Načte informace viditelné v zobrazení katalogu sys.geo_replication_links. **Tato možnost není podporována pro spravovanou instanci.**|
| [Replikační odkazy – seznam podle databáze](https://docs.microsoft.com/rest/api/sql/replicationlinks/listbydatabase) | Získá všechna propojení replikace pro danou databázi SQL v partnerství geografické replikace. Načte informace viditelné v zobrazení katalogu sys.geo_replication_links. |
| [Odstranit propojení replikace](https://docs.microsoft.com/rest/api/sql/replicationlinks/delete) | Odstraní propojení replikace databáze. Nelze provést během převzetí služeb při selhání. |
|  | |

## <a name="next-steps"></a>Další kroky

- Ukázkové skripty naleznete v tématu:
  - [Konfigurace a převzetí služeb při selhání izolované databáze s využitím aktivní geografické replikace](scripts/sql-database-setup-geodr-and-failover-database-powershell.md)
  - [Konfigurace a převzetí služeb při selhání databáze ve fondu s využitím aktivní geografické replikace](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md)
- SQL Database také podporuje skupiny automatického převzetí služeb při selhání. Další informace naleznete v tématu použití [skupin s automatickým převzetím služeb při selhání](sql-database-auto-failover-group.md).
- Přehled kontinuity provozu a scénáře najdete v tématu [Přehled kontinuity provozu](sql-database-business-continuity.md)
- Informace o automatickém zálohování azure sql database najdete v tématu [automatické zálohování databáze SQL Database](sql-database-automated-backups.md).
- Další informace o použití automatického zálohování pro obnovení naleznete [v tématu Obnovení databáze ze záloh iniciovaných službou](sql-database-recovery-using-backups.md).
- Další informace o požadavcích na ověřování pro nový primární server a databázi naleznete [v tématu zabezpečení databáze SQL po zotavení po havárii](sql-database-geo-replication-security-config.md).
