---
title: Převzetí služeb při selhání skupiny a aktivní geografická replikace – Azure SQL Database | Dokumentace Microsoftu
description: Použití skupin – automatické převzetí služeb při selhání pomocí aktivní geografické replikace a povolení automatického převzetí služeb při selhání v případě výpadku.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: carlrab
manager: craigg
ms.date: 12/03/2018
ms.openlocfilehash: de439683082909e65d285a7946a71eb781287937
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52843474"
---
# <a name="overview-active-geo-replication-and-auto-failover-groups"></a>Přehled: Aktivní geografickou replikaci a automatické převzetí služeb při selhání skupiny

Aktivní geografická replikace je funkce Azure SQL Database, která vám umožní vytvořit čitelné repliky jednotlivé databáze ve stejném nebo jiném datovém centru (oblast).

![Geografická replikace](./media/sql-database-geo-replication-failover-portal/geo-replication.png )

Aktivní geografická replikace je navržená jako řešení obchodní kontinuity podnikových procesů, které umožňuje aplikaci provést zotavení po havárii rychle jednotlivé databáze v případě regionálních disastor nebo výpadek velkého rozsahu. Pokud je povolené geografickou replikaci, aplikaci můžete spustit převzetí služeb při selhání do sekundární databáze v jiné oblasti Azure. V jedné nebo několika oblastech se podporují až čtyři sekundární databáze a sekundárních replik lze také pro dotazy přístup jen pro čtení. Převzetí služeb při selhání musí ručně zahájit uživatel nebo aplikace. Po převzetí služeb při selhání má nový primární koncový bod jiné připojení.

> [!NOTE]
> Aktivní geografická replikace je k dispozici pro všechny databáze ve všech úrovních služby ve všech oblastech.
> Aktivní geografická replikace není podporována pro databáze vytvořené v rámci Managed Instance. Zvažte použití [skupiny převzetí služeb při selhání](#auto-failover-group-capabilities) pomocí spravovaných instancí.

Automatické převzetí služeb při selhání skupiny je rozšířením aktivní geografickou replikaci. Je navržená ke správě převzetí služeb při selhání současně pomocí iniciované aplikací převzetí služeb při selhání několika databází geograficky replikovaného nebo delegováním převzetí služeb při selhání má být provedena po službu SQL Database podle kritérií definovaných uživatelem. Ten umožňuje automaticky obnovit více související databáze v sekundární oblasti po závažnému selhání nebo jiné neplánované události, jehož výsledkem úplné nebo částečné ztrátě dostupnosti služby SQL Database v primární oblasti. Kromě toho můžete použít čitelné sekundární databáze k přesměrování zpracování úlohy dotazu jen pro čtení. Protože-automatické převzetí služeb při selhání skupiny zahrnují více databází, musí být nakonfigurované tyto databáze na primárním serveru. Primární a sekundární servery pro databáze ve skupině převzetí služeb při selhání musí být ve stejném předplatném. Automatické převzetí služeb při selhání skupiny podporu replikace všech databází ve skupině jenom jednu sekundární server v jiné oblasti.

> [!NOTE]
> Aktivní geografickou replikaci použijte, pokud požadujete více sekundárních replik.
> [!IMPORTANT]
> Podpora – automatické převzetí služeb při selhání pro Managed instance je ve verzi public preview.

Pokud používáte aktivní geografickou replikaci a pro žádné z důvodu vaší primární databáze selže nebo jednoduše musí být uveden do režimu offline, můžete spustit převzetí služeb při selhání pro žádnou z vaší sekundární databází. Při aktivaci převzetí služeb při selhání na jednu sekundární databází, všechny ostatní sekundární repliky jsou automaticky propojení se nový primární. Pokud používáte – automatické převzetí služeb při selhání skupiny pro správu obnovení databáze a jakémkoli výpadku, který má vliv na jeden nebo několik databází v seskupení výsledků v automatické převzetí služeb při selhání. Můžete nakonfigurovat zásady automatické převzetí služeb při selhání, která nejlépe vyhovuje potřebám aplikace, nebo můžete odhlásit a použít ruční aktivaci. Kromě toho-automatické převzetí služeb při selhání skupiny poskytují čtení i zápis a koncové body naslouchacího zápisu jen pro čtení, které zůstávají beze změny během převzetí služeb při selhání. Ať už používáte aktivace ruční nebo automatické převzetí služeb při selhání, převzetí služeb při selhání přepne všechny sekundární databáze ve skupině na primární. Po dokončení převzetí služeb při selhání databáze se automaticky aktualizuje záznam DNS k přesměrování koncové body do nové oblasti.

Může spravovat replikaci a převzetí služeb při selhání jednotlivých databází nebo sadu databází na serveru nebo do elastického fondu pomocí aktivní geografické replikace. Můžete provést, že při použití:

- [Azure Portal](sql-database-geo-replication-portal.md)
- [Prostředí PowerShell: Izolované databáze](scripts/sql-database-setup-geodr-and-failover-database-powershell.md)
- [Prostředí PowerShell: Elastický fond](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md)
- [Prostředí PowerShell: Skupiny převzetí služeb při selhání](scripts/sql-database-setup-geodr-failover-database-failover-group-powershell.md)
- [Jazyka Transact-SQL: Elastický fond nebo izolovanou databázi](/sql/t-sql/statements/alter-database-azure-sql-database)
- [Rozhraní REST API: Izolované databáze](https://docs.microsoft.com/rest/api/sql/replicationlinks)
- [Rozhraní REST API: Převzetí služeb při selhání skupiny](https://docs.microsoft.com/rest/api/sql/failovergroups).

Po převzetí služeb při selhání Ujistěte se, že požadavky na ověřování pro server a databáze jsou nakonfigurovány na nový primární. Podrobnosti najdete v tématu [zabezpečení služby SQL Database po zotavení po havárii](sql-database-geo-replication-security-config.md). To se vztahuje i na aktivní geografickou replikaci a automatické převzetí služeb při selhání skupiny.

Aktivní geografická replikace využívá [Always On](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server) technologie SQL serveru na asynchronní potvrzené transakce u primární databáze replikace do sekundární databáze pomocí izolace snímku. Automatické převzetí služeb při selhání skupiny poskytují sémantiku skupiny nad aktivní geografickou replikaci, ale používá stejné mechanismu asynchronní replikace. Když v libovolném časovém okamžiku sekundární databáze může být mírně za primární databáze, je zaručeno, že sekundární data nikdy nemůžete mít částečné transakce. Redundance mezi oblastmi umožňuje aplikacím rychle obnoven k trvalé ztrátě celého datového centra nebo částí datacentra způsobené přírodními katastrofami, katastrofální lidské chyby nebo škodlivý funguje. Konkrétní cíle bodu obnovení dat lze nalézt v [přehled kontinuity](sql-database-business-continuity.md).

Následující obrázek znázorňuje příklad aktivní geografickou replikaci nakonfigurovaná s primárním v oblasti střed USA – sever a sekundární v oblasti střed USA – jih.

![relaci geografické replikace](./media/sql-database-active-geo-replication/geo-replication-relationship.png)

Protože jsou čitelné sekundární databáze, můžete použít k přesměrování zpracování úlohy jen pro čtení jako úlohy vytváření sestav. Pokud používáte aktivní geografickou replikaci, je možné vytvořit sekundární databáze ve stejné oblasti s primární, ale nezvyšuje katastrofických selhání odolnost aplikace. Pokud používáte skupiny automatické převzetí služeb při selhání, se vždy vytvoří sekundární databáze v jiné oblasti.

Kromě po havárii je možné obnovení aktivní geografickou replikaci v následujících scénářích:

- **Migrace databáze**: aktivní geografickou replikaci můžete migrace databáze z jednoho serveru na jiné online s minimálními výpadky.
- **Upgrady aplikací**: můžete vytvořit další sekundární jako kopii zpětného navrácení služeb po během upgradu aplikace.

Pro dosažení skutečné obchodní kontinuity podnikových procesů, je přidání redundance databáze mezi datacentry pouze část řešení. Obnovení aplikaci (služba) end až do konce po závažnému selhání vyžaduje obnovení všech komponent, které tvoří službu a všechny závislé služby. Příklady těchto komponent: klientský software (například prohlížeč s vlastní JavaScriptu), webových front-endů, úložiště a DNS. Je velmi důležité, že všechny komponenty jsou odolné vůči selhání stejné a budou k dispozici v rámci plánovaná doba obnovení (RTO) vaší aplikace. Proto budete muset Identifikujte všechny závislé služby a pochopit, záruky a možnosti, které poskytují. Poté je nutné provést odpovídající kroky zajistit, aby vaše funkce služeb během převzetí služeb při selhání služby, na kterých závisí. Další informace o návrhu řešení pro zotavení po havárii najdete v tématu [návrhu cloudových řešení pro použití pro zotavení po havárii aktivní geografickou replikaci](sql-database-designing-cloud-solutions-for-disaster-recovery.md).

## <a name="active-geo-replication-capabilities"></a>Funkce aktivní geografické replikace

Funkce aktivní geografickou replikaci poskytuje následující základní možnosti:

- **Automatická asynchronní replikace**

 Sekundární databázi lze vytvořit pouze tak, že přidáte do existující databáze. Sekundární lze vytvořit v libovolné serveru Azure SQL Database. Po vytvoření sekundární databáze se vyplní daty zkopírovanými z primární databáze. Tento proces se označuje jako synchronizace replik indexů. Jakmile sekundární databáze se vytvoří a nasadí, aktualizace na primární databázi se asynchronně replikují do sekundární databáze automaticky. Asynchronní replikace znamená, že transakce usilujeme o to u primární databáze předtím, než se replikují do sekundární databáze.

- **Sekundární databáze pro čtení**

  Aplikace můžete přistupovat k sekundární databáze pro operace jen pro čtení pomocí objektů stejného nebo jiného zabezpečení použít pro přístup k primární databáze. Sekundární databáze, pracují v režimu izolace snímku zajistit replikaci aktualizací primární (protokol opětovného přehrání) není opožděná. dotazy se prováděné na sekundární.

  > [!NOTE]
  > Protokol opětovného přehrání je zpožděno v sekundární databázi, pokud nejsou aktualizace schématu na primárním. Druhá možnost vyžaduje schéma zámek na sekundární databázi.

- **Více čitelné sekundární databáze**

  Dvě nebo více sekundárních databází můžete zvýšit redundance a úroveň ochrany pro primární databáze a aplikace. Existuje více sekundární databází, i nadále aplikace chráněn, i v případě, že jedna sekundární databáze se nezdaří. Pokud existuje pouze jedna sekundární databáze a selže, aplikace je přístupný vyšší riziko dokud se vytvoří nová sekundární databáze.

  > [!NOTE]
  > Pokud používáte aktivní geografickou replikaci vytvářet globálně distribuované aplikace a potřebují mít možnost poskytnout přístup jen pro čtení k datům ve více než čtyř oblastí, můžete vytvořit sekundární sekundární databáze (proces označovaný jako řetězení). Tímto způsobem můžete dosáhnout téměř neomezené škálování replikace databáze. Kromě toho řetězení snižuje režijní náklady replikace z primární databáze. Nutný kompromis je prodleva zvýšenou replikace u sekundárních databází nejvíce listů.

- **Podpora elastického fondu databází**

  Každou repliku můžete samostatně součástí elastického fondu nebo nesmí být v každém elastického fondu vůbec. Volba fond pro každou repliku je oddělená a nezávisí na konfiguraci jiné repliky (ať už primární nebo sekundární). Každý elastický fond je obsažen v rámci jedné oblasti, proto může sdílet víc replik v topologii stejné nikdy elastického fondu.

- **Konfigurovat výpočty velikosti sekundární databáze**

  Primární a sekundární databáze musí mít stejné úrovně služeb. Také důrazně doporučujeme, že se vytvoří sekundární databáze se stejnou velikostí výpočetní prostředky (počet jednotek Dtu nebo virtuálních jader) jako primární. Sekundární s menší velikostí výpočetních ohrožen zvýšenou replikace prodlevy, potenciální nedostupnost sekundární databáze a proto riziko ztráty dat podstatné po převzetí služeb při. V důsledku toho publikované cíle bodu obnovení = 5 s nemůže být zaručena. Další rizika je, že po převzetí služeb při selhání vaší aplikace bude mít dopad na výkon kvůli nedostatku výpočetní kapacity nový primární dokud nebude upgradována na vyšší výpočetní velikost. Doba upgradu závisí na velikosti databáze. Kromě toho právě takové upgrade vyžaduje, že primární i sekundární databáze jsou online a proto nelze dokončit, dokud je zmírněna výpadek. Pokud se rozhodnete vytvořit sekundární s menší velikostí výpočetních, graf procento protokolu vstupně-výstupních operací na portálu Azure portal poskytuje vhodný způsob, jak odhadovat velikost minimální výpočetní sekundární databáze, která je nutná pro udržení zatížení replikace. Například, pokud primární databáze je P6 (1 000 DTU) a jeho protokolem procent vstupně-výstupních operací je sekundární musí být nejméně 50 % P4 (500 DTU). Můžete také načíst data protokolu vstupně-výstupních operací s využitím [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) nebo [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) databáze zobrazení.  Další informace o velikostech výpočetních SQL Database najdete v tématu [co jsou úrovně služby SQL Database](sql-database-service-tiers.md).

- **Řízené uživatelem převzetí služeb při selhání a navrácení služeb po obnovení**

  Sekundární databáze lze explicitně přepnout do primární role v každém okamžiku aplikace nebo uživatele. Během výpadku skutečné "neplánované" možnost by měla sloužit, která okamžitě podporuje sekundární jako primární. Při selhání primární obnoví a je opět k dispozici, systém automaticky označí jako sekundární obnovené primární a ji používalo nový primární. Protože potřebujeme asynchronní replikaci malé množství dat může dojít ke ztrátě během neplánované převzetí služeb při selhání selže primární před replikuje nejnovější změny do sekundární. Když primární s více sekundárních replik převezme služby při selhání, systém automaticky změní konfiguraci relace replikace a odkazy zbývající sekundární databáze na primární připojovaly bez nutnosti zásahu uživatele. Po vyřešení výpadek, který způsobil převzetí služeb při selhání může být žádoucí aplikace co nejdříve do primární oblasti. K tomuto účelu by měl vyvolat příkaz převzetí služeb při selhání s parametrem "plánovanou".

- **Udržování synchronizace přihlašovacích údajů a pravidla brány firewall**

  Doporučujeme používat [pravidla brány firewall databáze](sql-database-firewall-configure.md) pro geograficky replikované databáze, tak tato pravidla se dají replikovat s databází a zkontrolujte všechny sekundární databáze mají stejná pravidla brány firewall jako primární. Tento přístup se eliminuje potřeba zákazníkům ručně konfigurovat a spravovat pravidla brány firewall na servery, které hostují jak primární a sekundární databází. Podobně použití [uživatelé databáze s omezením](sql-database-manage-logins.md) dat přístup zajistí primární i sekundární databáze vždy stejné přihlašovací údaje uživatele, takže při selhání, není k dispozici žádné přerušení z důvodu neshody se uživatelská jména a hesla. Přidání [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md), zákazníci můžou spravovat přístup uživatelů k primární i sekundární databáze a eliminují ke správě pověření v databázích úplně se vynechá.

## <a name="auto-failover-group-capabilities"></a>Funkce skupiny-automatické převzetí služeb při selhání

Funkce automatického převzetí služeb skupiny poskytuje výkonné abstrakce aktivní geografickou replikaci díky podpoře replikace na úrovni skupiny a automatické převzetí služeb při selhání. Kromě toho odebere potřeba změnit připojovací řetězec SQL po převzetí služeb při selhání tím, že poskytuje další naslouchací proces koncové body.

> [!IMPORTANT]
> Managed instance podporu-automatické převzetí služeb při selhání skupiny jako funkce ve verzi public preview. Zobrazit [osvědčené postupy používání skupin převzetí služeb při selhání pomocí spravovaných instancí](#best-practices-of-using-failover-groups-with-managed-instances)

- **Skupiny převzetí služeb při selhání**

  Jeden nebo více skupin převzetí služeb při selhání můžete vytvořit mezi dvěma servery v různých oblastech (primární a sekundární server). Každá skupina může zahrnovat jednu nebo několik databází, které se dají obnovit jako jednotka v případě, že některá nebo všechna primární databáze nedostupné kvůli výpadku v primární oblasti.  

- **Primární server**

  Server, který je hostitelem primární databází ve skupině převzetí služeb při selhání.

- **Sekundární server**

  Server, který je hostitelem sekundární databází ve skupině převzetí služeb při selhání. Sekundární server nemůže být ve stejné oblasti jako primární server.

- **Přidávání databází do skupiny převzetí služeb při selhání**

  Několik databází v rámci serveru nebo v rámci elastického fondu můžete umístit do stejné skupiny převzetí služeb při selhání. Pokud chcete přidat jednu databázi do skupiny, automaticky vytvoří sekundární databáze pomocí stejné velikosti edition a výpočetní výkon. Pokud je primární databáze v elastickém fondu, se automaticky vytvoří sekundární v elastickém fondu se stejným názvem. Pokud chcete přidat databáze, která už má sekundární databáze v sekundární server, geografická replikace je zděděno ve skupině.

  > [!NOTE]
  > Při přidávání databázi, která už má sekundární databáze na serveru, který není součástí skupiny převzetí služeb při selhání, se vytvoří nový sekundární v sekundárním serveru.
  > [!IMPORTANT]
  > Ve spravované instanci jsou replikovány všem uživatelským databázím. Nelze vybrat podmnožinu uživatelských databází pro replikaci ve skupině převzetí služeb při selhání.

- **Naslouchací proces pro čtení i zápis skupiny převzetí služeb při selhání**

  Záznam DNS CNAME, který je vytvořen jako  **&lt;název skupiny převzetí služeb při selhání&gt;. database.windows.net** , která odkazuje na adresu URL aktuální primární server. Umožňuje aplikacím SQL pro čtení a zápis se transparentně znovu připojit k primární databáze, pokud se primární změní po převzetí služeb při selhání.

- **Převzetí služeb při selhání jen pro čtení naslouchacího procesu skupiny**

  Záznam DNS CNAME, který je vytvořen jako  **&lt;název skupiny převzetí služeb při selhání&gt;. secondary.database.windows.net** , která odkazuje na adresu URL na sekundární server. Umožňuje aplikacím SQL jen pro čtení transparentně připojení k sekundární databázi pomocí zadaného pravidla Vyrovnávání zatížení.

  > [!IMPORTANT]
  > Po vytvoření skupiny převzetí služeb při selhání na spravované instanci záznamy DNS CNAME pro naslouchací procesy jsou **&lt;název skupiny převzetí služeb při selhání&gt;.&lt; zone_id&gt;. database.windows.net a &lt;název skupiny převzetí služeb při selhání&gt;.secondary.&lt; zone_id&gt;. database.windows.net. Zobrazit [osvědčené postupy pro skupiny převzetí služeb při selhání pomocí spravovaných instancí](#best-practices-of-using-failover-groups-for-business-continuity-with-managed-instances) pro podrobné informace o použití zone_id s spravované instance.

- **Automatické převzetí služeb při selhání zásad**

  Ve výchozím nastavení skupiny převzetí služeb při selhání se nakonfigurují zásady automatické převzetí služeb při selhání. Systém aktivuje převzetí služeb při selhání, když se zjistí selhání a období odkladu vypršelo. Systému musíte ověřit, že výpadek nelze možné zmírnit zajištěním integrovanou vysokou dostupnost infrastruktury služby SQL Database z důvodu škálování dopad. Pokud chcete řídit pracovní postup převzetí služeb při selhání z aplikace, můžete vypnout automatické převzetí služeb při selhání.

- **Zásada převzetí služeb při selhání jen pro čtení**

  Ve výchozím nastavení je zakázána převzetí služeb při selhání naslouchacího zápisu jen pro čtení. Zajišťuje, že není při offline sekundární dopad na výkon primární. Ale to také znamená, že relace jen pro čtení nebudete moct připojit, dokud je obnovit sekundární. Pokud jste nejde tolerovat výpadku pro relace jen pro čtení a lze dočasně použít primární pro jen pro čtení pro čtení i zápis přenosy i za cenu potenciální snížení výkonu z primární, můžete povolit převzetí služeb při selhání pro naslouchací proces jen pro čtení. V takovém případě provozu jen pro čtení automaticky přesměrováni na primární server Pokud sekundární server není k dispozici.  

- **Ruční převzetí služeb při selhání**

  Převzetí služeb při selhání můžete spustit ručně kdykoli bez ohledu na to, konfiguraci automatického převzetí služeb při selhání. Pokud není nakonfigurované zásady pro automatické převzetí služeb při selhání, ruční převzetí služeb při selhání je potřeba obnovit databáze ve skupině převzetí služeb při selhání. Můžete zahájit vynucené nebo popisný převzetí služeb při selhání (se úplná synchronizace dat). Ten může použít k přesunutí aktivního serveru do primární oblasti. Po dokončení převzetí služeb při selhání se automaticky aktualizují záznamy DNS pro zajištění možnosti připojení ke správnému serveru.

- **Období odkladu se ztrátou dat**

  Vzhledem k tomu, že primární a sekundární databáze nejsou synchronizovány, použití asynchronní replikace, převzetí služeb může způsobit ztrátu dat. Zásady automatické převzetí služeb při selhání tak, aby odrážely tolerance vaší aplikace ke ztrátě dat. můžete přizpůsobit. Tím, že nakonfigurujete **GracePeriodWithDataLossHours**, můžete řídit, jak dlouho systém čeká před inicializací převzetí služeb při selhání, která by mohla ztrátě dat výsledků.

  > [!NOTE]
  > Když systém zjistí, že databáze ve skupině jsou stále online (například výpadek pouze ovlivněné roviny řízení služby), okamžitě aktivuje převzetí služeb při selhání se úplná synchronizace dat (popisný převzetí služeb při selhání) bez ohledu na hodnotu nastavenou  **GracePeriodWithDataLossHours**. To zaručuje, že nedochází ke ztrátě dat během obnovení. Období odkladu se projeví pouze v případě, že popisný převzetí služeb při selhání není možné. Pokud výpadek vyřešení před vypršením platnosti období odkladu, převzetí služeb při selhání není aktivována.

- **Více skupin převzetí služeb při selhání**

  Můžete nakonfigurovat více skupin převzetí služeb při selhání pro stejného páru servery pod kontrolou škálování převzetí služeb při selhání. Každá skupina převezme nezávisle na sobě. Pokud vaše aplikace s více tenanty pomocí elastických fondů, můžete tuto funkci kombinovat primární a sekundární databáze v každém fondu. Tímto způsobem můžete snížit dopad výpadku pouze polovinu klientům.

  > [!IMPORTANT]
  > Managed Instance nepodporuje více skupin převzetí služeb při selhání.

## <a name="best-practices-of-using-failover-groups-with-single-and-pooled-databases"></a>Osvědčené postupy používání skupin převzetí služeb při selhání s databázemi ve fondu a jeden

Při návrhu služby s kontinuita podnikových procesů v paměti, dodržujte následující obecné pokyny:

- **Použití jednoho nebo několika skupin převzetí služeb při selhání pro správu převzetí služeb při selhání několika databází**

  Jeden nebo více skupin převzetí služeb při selhání můžete vytvořit mezi dvěma servery v různých oblastech (primární a sekundární server). Každá skupina může zahrnovat jednu nebo několik databází, které se dají obnovit jako jednotka v případě, že některá nebo všechna primární databáze nedostupné kvůli výpadku v primární oblasti. Skupiny převzetí služeb při selhání vytvoří geograficky sekundární databázi se stejný cíl služby jako primární. Pokud chcete přidat existující relaci geografické replikace do skupiny převzetí služeb při selhání, nezapomeňte že GEO-secondary má nakonfigurovanou na stejné úrovně služeb a velikost výpočetního jako primární.

- **Pro úlohy OLTP použijte naslouchací proces pro čtení i zápis**

  Při provádění operací s online zpracováním transakcí, použití  **&lt;název skupiny převzetí služeb při selhání&gt;. database.windows.net** jako server pro adresu URL a připojení se automaticky přesměrovaní na primární. Tuto adresu URL nezměnil převzetí služeb při selhání. Všimněte si, že že převzetí služeb při selhání zahrnuje aktualizaci, kterou záznam DNS, takže připojení klientů se přesměrují na nový primární až po klientské mezipaměti DNS je aktualizováno.

- **Pro úlohy jen pro čtení použijte naslouchacího zápisu jen pro čtení**

  Pokud nemáte logicky izolované úlohy jen pro čtení, který je odolný vůči určitým odolnost dat, můžete v aplikaci použít sekundární databáze. Pro relace jen pro čtení, použijte  **&lt;název skupiny převzetí služeb při selhání&gt;. secondary.database.windows.net** jako server pro adresu URL a připojení se automaticky přesměrovaní na sekundární. Doporučuje se také, abyste určili v připojovacím řetězci číst záměr pomocí **ApplicationIntent = ReadOnly**.

- **Připravit pro snížení výkonu**

  Rozhodnutí převzetí služeb při selhání SQL je nezávislá na zbývající části aplikace nebo jiných služeb, které využívají. Aplikace může být "směšovat" s některými komponentami, v jedné oblasti a některé v jiném. Abyste se vyhnuli zjištěním snížení výkonnosti, zkontrolujte nasazení redundantního aplikace v oblasti zotavení po Havárii a postupujte podle těchto [pokyny pro zabezpečení sítě](#failover-groups-and-network-security).

  > [!NOTE]
  > Aplikace v oblasti zotavení po Havárii nebude muset použít jiné připojovací řetězec.  

- **Příprava ke ztrátě dat.**

  Pokud se zjistí výpadku SQL čeká dobu určenou podle **GracePeriodWithDataLossHours**. Výchozí hodnota je 1 hodina. Pokud si nemůžete dovolit ztrátu dat, nezapomeňte nastavit **GracePeriodWithDataLossHours** na dostatečně velký počet, jako je 24 hodin. Pomocí ručně vytvořená skupina převzetí služeb při selhání nezdaří zpět ze sekundární do primární.

> [!IMPORTANT]
> Elastické fondy s 800 nebo menší počet Dtu a víc než 250 databází s využitím geografické replikace se setkat s problémy, včetně už plánované převzetí služeb při selhání a snížení výkonu.  Tyto problémy budou pravděpodobně probíhat pro úlohy náročné na zápis, když geografickou replikaci koncové body jsou daleko od podle zeměpisné oblasti, nebo když více koncových bodů sekundární se používají pro každou databázi.  Mezi příznaky tyto problémy jsou označeny při prodleva geografické replikace se zvyšuje v čase.  Toto opoždění je možné monitorovat pomocí [sys.dm_geo_replication_link_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database).  Pokud dojde k těmto problémům, zmírnění zahrnují zvýšení počtu jednotek Dtu fondů nebo snížit počet geograficky replikovaných databází ve stejném fondu.

## <a name="best-practices-of-using-failover-groups-with-managed-instances"></a>Osvědčené postupy používání skupin převzetí služeb při selhání pomocí spravovaných instancí

Pokud vaše aplikace používá spravované instance jako datovou vrstvu, postupujte při návrhu pro kontinuitu podnikových tyto obecné pokyny:

- **Vytvořit sekundární instance ve stejné zóně DNS jako primární instance**

  Když je vytvořena nová instance, jedinečné id je automaticky generována jako zóna DNS a název DNS instance součástí. Vícedoménové (SAN) certifikátu pro tuto instanci zřizován s polem SAN v podobě &lt;zone_id&gt;. database.windows.net. Tento certifikát slouží k ověření připojení klienta do instance ve stejné zóně DNS. K zajištění bez přerušení připojení k primární instance po převzetí služeb při selhání primární i sekundární instancí musí být ve stejné zóně DNS. Když je aplikace připravená pro produkční nasazení, vytvoření sekundární instance v různých oblastech a ujistěte se, že zóna DNS, která sdílí s primární instance. To se provádí tak, že zadáte `DNS Zone Partner` nepovinný parametr `create instance` příkaz prostředí PowerShell.

- **Povolit replikaci mezi dvěma instancemi**

  Vzhledem k tomu, že každá instance je izolovaný v své vlastní virtuální sítě, musí být povoleno dvě obousměrné přenosy mezi těmito virtuálními sítěmi. Zobrazit [replikaci s využitím SQL Database Managed Instance](replication-with-sql-database-managed-instance.md).

- **Konfigurace převzetí služeb při selhání skupiny pro správu převzetí služeb při selhání celé instance**

  Skupiny převzetí služeb při selhání bude spravovat převzetí služeb při selhání všechny databáze v instanci. Když se vytvoří skupina, bude každou databázi v instanci automaticky geograficky replikované sekundární instanci. Skupiny převzetí služeb při selhání nelze použít k zahájení částečné převzetí služeb při selhání dílčí sady databází.

  > [!IMPORTANT]
  > Pokud databáze je odebrán z primární instance, to se také vloží automaticky na geograficky sekundární instanci.

- **Pro úlohy OLTP použijte naslouchací proces pro čtení i zápis**

  Při provádění operací s online zpracováním transakcí, použití &lt;název skupiny převzetí služeb při selhání&gt;.&lt; zone_id&gt;. database.windows.net jako adresu URL serveru a připojení se automaticky přesměrovaní na primární. Tuto adresu URL nezměnil převzetí služeb při selhání. Převzetí služeb zahrnuje aktualizace záznamu DNS, takže připojení klientů se přesměrují na nový primární až po klientské mezipaměti DNS je aktualizováno. Protože sekundární instance sdílí s primární zóny DNS, klientská aplikace bude moct znovu připojit k němu pomocí stejného certifikátu SAN.

- **Připojte se přímo do geograficky replikované sekundární databáze pro dotazy jen pro čtení**

  Pokud nemáte logicky izolované úlohy jen pro čtení, který je odolný vůči určitým odolnost dat, můžete v aplikaci použít sekundární databáze. Chcete-li připojit přímo do geograficky replikované sekundární databáze, použijte &lt;server&gt;.secondary.&lt; zone_id&gt;. database.windows.net jako adresu URL serveru a připojení se provádí přímo do geograficky replikované sekundární databáze.

  > [!NOTE]
  > V některých úrovně služby Azure SQL Database podporuje použití [repliky jen pro čtení](sql-database-read-scale-out.md) načíst vyrovnávat zatížení dotazu jen pro čtení použitím kapacita jednu repliku pouze pro čtení a `ApplicationIntent=ReadOnly` parametr v připojení řetězec. Když nakonfigurujete geograficky replikované sekundární můžete tuto funkci pro připojení k buď jen pro čtení repliky v primárním umístění nebo v geograficky replikovaného umístění.
  > - Pro připojení k repliky jen pro čtení v primárním umístění, pomocí &lt;název skupiny převzetí služeb při selhání&gt;.&lt; zone_id&gt;. database.windows.net.
  > - Pro připojení k repliky jen pro čtení v primárním umístění, pomocí &lt;název skupiny převzetí služeb při selhání&gt;.secondary.&lt; zone_id&gt;. database.windows.net.
- **Připravit pro snížení výkonu**

  Rozhodnutí převzetí služeb při selhání SQL je nezávislá na zbývající části aplikace nebo jiných služeb, které využívají. Aplikace může být "směšovat" s některými komponentami, v jedné oblasti a některé v jiném. Abyste se vyhnuli zjištěním snížení výkonnosti, zkontrolujte nasazení redundantního aplikace v oblasti zotavení po Havárii a postupujte podle pokynů zabezpečení sítě v tomto článku <link>.

- **Příprava ke ztrátě dat.**

  Pokud se zjistí výpadku SQL automaticky spustí převzetí služeb při selhání pro čtení i zápis, pokud není nulová ztráta dat na nejlepší naši znalostní báze. V opačném případě čeká období, které jste zadali GracePeriodWithDataLossHours. Pokud jste zadali GracePeriodWithDataLossHours, se připravit únikem informací. Obecně platí Azure během výpadků, upřednostňuje dostupnost. Pokud si nemůžete dovolit ztrátu dat, nezapomeňte nastavit GracePeriodWithDataLossHours do dostatečně velké množství, jako je 24 hodin.

> [!IMPORTANT]
> Pomocí ručně vytvořená skupina převzetí služeb při selhání můžete přesunout primárek zpět do původního umístění: při výpadku, který způsobil převzetí služeb při selhání je zmírnit, můžete přesunout primární databáze do původního umístění. K tomu by mělo zahájit ruční převzetí služeb při selhání skupiny.

> [!IMPORTANT]
> Aktualizace DNS naslouchacího procesu pro čtení a zápis se stane hned převzetí služeb při selhání je zahájené. Tato operace nesmí dojít ke ztrátě. Proces přepínání databázové role, ale může trvat až 5 minut, než se za normálních podmínek. Dokud se nedokončí, zůstanou některé databáze v nové primární instance jen pro čtení. Pokud převzetí služeb při selhání se inicializuje pomocí prostředí PowerShell celá operace je synchronní. Pokud se inicializuje, pomocí webu Azure portal rozhraní označí stav dokončení. Pokud se inicializuje, pomocí rozhraní REST API, použijte standardní ARM dotazovací mechanismus monitorování pro dokončení.

## <a name="failover-groups-and-network-security"></a>Skupiny převzetí služeb při selhání a zabezpečení sítě

U některých aplikací, které vyžadují pravidla zabezpečení, že síťový přístup k datové vrstvě je omezen na konkrétní součást nebo komponenty, například virtuální počítač webová služba atd. Tento požadavek uvádí některé běžné problémy pro návrh obchodní kontinuity podnikových procesů a používání skupin převzetí služeb při selhání. Při implementaci těchto s omezeným přístupem, byste měli zvážit následující možnosti.

### <a name="using-failover-groups-and-virtual-network-rules"></a>Použití pravidel virtuální sítě a převzetí služeb při selhání skupiny

Pokud používáte [koncové body služeb virtuální sítě a pravidel](sql-database-vnet-service-endpoint-rule-overview.md) k omezení přístupu ke službě SQL database, mějte na paměti tohoto koncového bodu služby každá virtuální síť se vztahuje na jenom jedné oblasti Azure. Koncový bod neumožňuje jiných oblastech tak, aby přijímal komunikaci z podsítě. Proto pouze klientské aplikace nasazené ve stejné oblasti můžete připojit k primární databáze. Vzhledem k tomu, převzetí služeb při selhání výsledkem klientských relací SQL přesměrován na server v jiné oblasti (sekundární), tyto relace se nezdaří, pokud pochází z klienta mimo tuto oblast. Z tohoto důvodu zásady automatické převzetí služeb při selhání není možné, pokud jsou zúčastněné servery součástí pravidla virtuální sítě. Pro podporu ruční převzetí služeb při selhání, postupujte podle těchto kroků:

1. Zřízení redundantních kopií front-endových komponent vaší aplikace (webové služby, virtuální počítače atd.) v sekundární oblasti
2. Konfigurace [pravidel virtuální sítě](sql-database-vnet-service-endpoint-rule-overview.md) jednotlivě pro primární a sekundární server
3. Povolit [front-endu převzetí služeb při selhání pomocí konfigurace Traffic Manageru](sql-database-designing-cloud-solutions-for-disaster-recovery.md#scenario-1-using-two-azure-regions-for-business-continuity-with-minimal-downtime)
4. Spustit ruční převzetí služeb při selhání, pokud je zjištěn výpadek. Tato možnost je optimalizovaná pro aplikace, které vyžadují konzistentní latence mezi front-endu a datovou vrstvou a podporuje obnovení v případě front-endu, datové vrstvě nebo obojí jsou ovlivněny výpadek.

> [!NOTE]
> Pokud používáte **naslouchacího zápisu jen pro čtení** Vyrovnávání zatížení úlohy jen pro čtení, ujistěte se, že tuto úlohu je proveden v virtuálního počítače nebo jiného prostředku v sekundární oblasti, aby se mohl připojit k sekundární databázi.

### <a name="using-failover-groups-and-sql-database-firewall-rules"></a>Používání skupin převzetí služeb při selhání a pravidla brány firewall služby SQL database

Vyžaduje-li plánu kontinuity podnikových procesů pomocí automatického převzetí služeb při selhání skupiny převzetí služeb při selhání, můžete omezit přístup k databázi SQL pomocí pravidel brány firewall tradiční.  Pro podporu automatického převzetí služeb při selhání, postupujte podle těchto kroků:

1. [Vytvoření veřejné IP adresy](../virtual-network/virtual-network-public-ip-address.md#create-a-public-ip-address)
2. [Vytvoření veřejného load balanceru úrovně](../load-balancer/quickstart-create-basic-load-balancer-portal.md#create-a-basic-load-balancer) a přiřazovat veřejné IP adresy.
3. [Vytvoření virtuální sítě a virtuální počítače](../load-balancer/quickstart-create-basic-load-balancer-portal.md#create-back-end-servers) front-endových komponent
4. [Vytvořit skupinu zabezpečení sítě](../virtual-network/security-overview.md) a konfigurovat příchozí připojení.
5. Ujistěte se, že odchozí připojení jsou otevřená ke službě Azure SQL database s použitím "Sql" [značka služby](../virtual-network/security-overview.md#service-tags).
6. Vytvoření [pravidlo brány firewall služby SQL database](sql-database-firewall-configure.md) které povolí příchozí provoz z veřejné IP adresy, které jste vytvořili v kroku 1.

Další informace o tom, jak nakonfigurovat odchozí přístup a jaké IP adresy k použití v pravidlech brány firewall najdete v tématu [odchozí připojení nástroje pro vyrovnávání zatížení](../load-balancer/load-balancer-outbound-connections.md).

Konfiguraci uvedené výš se zajistí, že automatické převzetí služeb při selhání nebude blokovat připojení z front-endových komponent a předpokládá, že aplikace může tolerovat možnost, delší latence mezi front-endu a datovou vrstvou.

> [!IMPORTANT]
> Pro zajištění kontinuity obchodních procesů pro regionální výpadky musíte zajistit geografickou redundanci pro front-endových komponent a databáze.

## <a name="enabling-replication-between-managed-instances-and-their-vnets"></a>Povolení replikace mezi spravované instance a jejich virtuálních sítí

Když nastavíte skupiny převzetí služeb při selhání mezi primárním a sekundárním spravované instance ve dvou různých oblastech, každá instance je izolovaná pomocí nezávislé virtuální sítě. Chcete-li povolit replikaci provoz mezi těmito virtuálními sítěmi, postupujte takto:

1. Připojení přes buď okruhu Express Route (https://docs.microsoft.com/azure/expressroute/expressroute-circuit-peerings) nebo brány sítě VPN (https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).
2. Nastavení jednotlivých skupinách NSG tak, aby porty 5022 a rozsahu 11000 12000 jsou otevřené pro příchozí připojení z druhé virtuální sítě.

## <a name="upgrading-or-downgrading-a-primary-database"></a>Upgrade nebo při downgradu primární databáze

Můžete upgradovat nebo downgradovat primární databáze do různých výpočetních velikost (v rámci stejné úrovně služeb, ne mezi pro obecné účely a pro důležité obchodní informace) bez odpojení všechny sekundární databáze. Při upgradu, doporučujeme nejprve upgradovat sekundární databáze a pak upgradovat primární. Při downgradu, pořadí: nejprve downgradovat primární a poté downgradovat sekundární. Když upgradujete nebo starší verzi databáze, kterou chcete vrstvu různé služby, se vynucuje toto doporučení.

> [!NOTE]
> Pokud jste vytvořili jako součást konfigurace skupiny převzetí služeb při selhání do sekundární databáze není doporučeno downgradovat sekundární databáze. Tím je zajištěno, že datová vrstva má dostatečnou kapacitu pro zpracování pravidelné zatížení po aktivaci převzetí služeb při selhání.

## <a name="preventing-the-loss-of-critical-data"></a>Zabránění ztrátě důležitých dat

Z důvodu vysoké latenci sítě WAN průběžného kopírování používá mechanismu asynchronní replikace. Asynchronní replikace umožňuje ztrátu některých dat. nevyhnutelné Pokud dojde k chybě. Některé aplikace ale můžou vyžadovat bez ztráty. K ochraně těchto důležitých aktualizací, můžete volat vývojář aplikace [uložená procedura sp_wait_for_database_copy_sync](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync) systému postup ihned po potvrzení transakce. Volání **uložená procedura sp_wait_for_database_copy_sync** blokuje volající vlákno, dokud se poslední potvrzené transakce bylo přeneseno do sekundární databáze. Ale to nečeká přenášená transakcí, které chcete znovu přehrát a potvrzené na sekundární. **uložená procedura sp_wait_for_database_copy_sync** je vymezen na odkaz na konkrétní průběžného kopírování. Tento postup můžete volat každý uživatel s právy k připojení k primární databáze.

> [!NOTE]
> **uložená procedura sp_wait_for_database_copy_sync** zabrání ztrátě dat po převzetí služeb při selhání, ale nezaručuje úplnou synchronizaci pro oprávnění ke čtení. Zpoždění způsobené **uložená procedura sp_wait_for_database_copy_sync** TDS může být významný a závisí na velikosti protokolů transakcí v době volání.

## <a name="programmatically-managing-failover-groups-and-active-geo-replication"></a>Programová správa skupiny převzetí služeb při selhání a aktivní geografické replikace

Jak je popsáno výše, skupiny automatické převzetí služeb při selhání a aktivní geografickou replikaci můžete také spravovat prostřednictvím kódu programu pomocí prostředí Azure PowerShell a rozhraní REST API. Následující tabulky popisují sadu příkazů, které jsou k dispozici. Aktivní geografická replikace zahrnuje sadu rozhraní API Azure Resource Manageru pro správu, včetně [REST API služby Azure SQL Database](https://docs.microsoft.com/rest/api/sql/) a [rutin prostředí Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview). Tato rozhraní API vyžaduje použití skupin prostředků a podporu zabezpečení na základě role (RBAC). Další informace o tom, jak implementovat přístup rolí, najdete v části [řízení přístupu](../role-based-access-control/overview.md).

### <a name="manage-sql-database-failover-using-transact-sql"></a>Správa SQL database převzetí služeb při selhání pomocí příkazů jazyka Transact-SQL

> [!IMPORTANT]
> Tyto příkazy jazyka Transact-SQL pouze platí pro aktivní geografickou replikaci a se nevztahují na skupiny převzetí služeb při selhání. V důsledku toho se také nevztahují na Managed instance, jak podporují pouze skupiny převzetí služeb při selhání.

| Příkaz | Popis |
| --- | --- |
| [Příkaz ALTER DATABASE (Azure SQL Database)](/sql/t-sql/statements/alter-database-azure-sql-database) |Přidat sekundární SERVER ON argument použít k vytvoření sekundární databáze pro stávající databáze a spustí replikaci dat. |
| [Příkaz ALTER DATABASE (Azure SQL Database)](/sql/t-sql/statements/alter-database-azure-sql-database) |Pomocí převzetí služeb při selhání nebo FORCE_FAILOVER_ALLOW_DATA_LOSS můžete přepínat sekundární databázi na primární a zahájí tak převzetí služeb při selhání |
| [Příkaz ALTER DATABASE (Azure SQL Database)](/sql/t-sql/statements/alter-database-azure-sql-database) |Můžete odebrat sekundární SERVER ON ukončí replikaci dat mezi SQL Database a zadanou sekundární databází. |
| [Sys.geo_replication_links (Azure SQL Database)](/sql/relational-databases/system-dynamic-management-views/sys-geo-replication-links-azure-sql-database) |Vrátí informace o veškerá existující propojení replikace pro každou databázi na logickém serveru Azure SQL Database. |
| [Sys.dm_geo_replication_link_status (Azure SQL Database)](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database) |Získá čas poslední replikace, prodleva poslední replikace a další informace o propojení replikace pro danou databázi SQL. |
| [Sys.dm_operation_status (Azure SQL Database)](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) |Zobrazuje stav všech databázových operací včetně stav odkazů replikace. |
| [sp_wait_for_database_copy_sync (Azure SQL Database)](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync) |způsobí, že aplikace počkejte, dokud všechny potvrzené transakce jsou replikována a projdou potvrzením většinovým aktivní sekundární databáze. |
|  | |

### <a name="manage-sql-database-failover-using-powershell"></a>Správa SQL database převzetí služeb při selhání pomocí Powershellu

#### <a name="managing-failover-with-single-and-pooled-databases"></a>Správa převzetí služeb při selhání s databázemi ve fondu a jeden

| Rutina | Popis |
| --- | --- |
| [Přidat AzureRmSqlDatabaseToFailoverGroup](https://docs.microsoft.com/powershell/module/azurerm.sql/add-azurermsqldatabasetofailovergroup)|Přidá jednu nebo více databází do skupiny převzetí služeb při selhání služby Azure SQL Database|
| [Get-AzureRmSqlDatabase](https://docs.microsoft.com/powershell/module/azurerm.sql/get-azurermsqldatabase) |Získá jednu nebo více databází. |
| [New-AzureRmSqlDatabaseSecondary](https://docs.microsoft.com/powershell/module/azurerm.sql/new-azurermsqldatabasesecondary) |Vytvoří sekundární databázi pro existující databázi a spustí replikaci dat. |
| [Set-AzureRmSqlDatabaseSecondary](https://docs.microsoft.com/powershell/module/azurerm.sql/set-azurermsqldatabasesecondary) |Přepne sekundární databázi na primární a zahájí tak převzetí služeb při selhání. |
| [Remove-AzureRmSqlDatabaseSecondary](https://docs.microsoft.com/powershell/module/azurerm.sql/remove-azurermsqldatabasesecondary) |Ukončí replikaci dat mezi službou SQL Database a zadanou sekundární databází. |
| [Get-AzureRmSqlDatabaseReplicationLink](https://docs.microsoft.com/powershell/module/azurerm.sql/get-azurermsqldatabasereplicationlink) |Získá vazby geografické replikace mezi službou Azure SQL Database a skupinou prostředků nebo SQL Serverem. |
| [New-AzureRmSqlDatabaseFailoverGroup](https://docs.microsoft.com/powershell/module/azurerm.sql/set-azurermsqldatabasefailovergroup) |Tento příkaz vytvoří skupinu převzetí služeb při selhání a zaregistruje ho na primární a sekundární server|
| [Remove-AzureRmSqlDatabaseFailoverGroup](https://docs.microsoft.com/powershell/module/azurerm.sql/remove-azurermsqldatabasefailovergroup) | Odebere skupinu převzetí služeb při selhání ze serveru a odstranění všech sekundárních databází zahrnuté skupiny |
| [Get-AzureRmSqlDatabaseFailoverGroup](https://docs.microsoft.com/powershell/module/azurerm.sql/get-azurermsqldatabasefailovergroup) | Načte konfiguraci skupiny převzetí služeb při selhání |
| [Set-AzureRmSqlDatabaseFailoverGroup](https://docs.microsoft.com/powershell/module/azurerm.sql/set-azurermsqldatabasefailovergroup) |Upraví konfiguraci skupiny převzetí služeb při selhání |
| [Switch-AzureRMSqlDatabaseFailoverGroup](https://docs.microsoft.com/powershell/module/azurerm.sql/switch-azurermsqldatabasefailovergroup) | Aktivační události převzetí služeb při selhání skupiny převzetí služeb při selhání na sekundární server |
|  | |

> [!IMPORTANT]
> Ukázky skriptů, najdete v části [konfigurace a převzetí služeb při selhání izolované databáze pomocí aktivní geografické replikace](scripts/sql-database-setup-geodr-and-failover-database-powershell.md), [konfigurace a převzetí služeb při selhání pomocí aktivní geografické replikace databázi ve fondu](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md), a [ Konfigurace a převzetí služeb při selhání skupiny převzetí služeb pro izolovanou databázi](scripts/sql-database-setup-geodr-failover-database-failover-group-powershell.md).
>

#### <a name="managing-failover-groups-with-managed-instances-preview"></a>Správa skupin převzetí služeb při selhání s spravované instance (preview)

##### <a name="install-the-newest-pre-release-version-of-powershell"></a>Nainstalujte nejnovější verzi předběžné verze prostředí PowerShell

1. Aktualizace modulu powershellget 1.6.5 (nebo nejnovější verze preview). Zobrazit [Powershellu ve verzi preview webu](https://www.powershellgallery.com/packages/AzureRM.Sql/4.11.6-preview).

   ```Powershell
      install-module powershellget -MinimumVersion 1.6.5 -force
   ```

2. V novém okně prostředí powershell spusťte následující příkazy:

   ```Powershell
      import-module powershellget
      get-module powershellget #verify version is 1.6.5 (or newer)
      install-module azurerm.sql -RequiredVersion 4.5.0-preview -AllowPrerelease –Force
      import-module azurerm.sql
   ```

##### <a name="prerequisites-before-setting-up"></a>Požadavky před nastavením

- Spravovaných instancí musí být v různých zeměpisných oblastech.
- Vaše sekundární musí být prázdný (žádné uživatelské databáze).
- Primární a sekundární spravovaných instancí musí být ve stejné skupině prostředků.
- Virtuální sítě, které spravovaných instancí jsou součástí musí být [připojení přes bránu VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). VNet Peering se nepodporuje.
- Těmito dvěma podsítěmi Managed Instance nemůže mít překrývající se IP adresy.
- Budete muset nastavit vaše skupiny zabezpečení sítě (NSG) takové, které porty 5022 a rozsahu 11000 ~ 12000 jsou otevřené připojení z druhé virtuální sítě. Tím se povolí provoz replikace mezi instancemi.
- Musíte nakonfigurovat zóny DNS & partnera zóny Dns. Zóny DNS je vlastnost Managed Instance. Představuje součást název hostitele, který následuje název spravované Instance a předchází. database.windows.net příponu. Je generována jako náhodný řetězec během vytváření prvního Managed Instance v každé virtuální síti. Zóna DNS se nemůže modifikovat po vytvoření spravované instance a spravovaných instancí v rámci stejné virtuální síti sdílet stejnou hodnotu zóny DNS. Kvůli nastavení skupiny převzetí služeb při selhání spravovaných instancí Managed Instance primární a sekundární Managed Instance musí sdílet stejnou hodnotu zóny databází. To můžete provést zadáním `DnsZonePartner` parametr při vytváření sekundární Managed Instance. Definuje vlastnost partnera zóny DNS Managed Instance pro sdílení ve skupině převzetí služeb při selhání instancí s. Předáním do id prostředku jiné mi jako vstup DnsZonePartner, v tuto chvíli vytváří Managed Instance dědí stejnou hodnotu zóny DNS partnera, spravované Instance.

##### <a name="powershell-commandlets-to-create-an-instance-failover-group"></a>Rutiny prostředí PowerShell k vytvoření instance skupiny převzetí služeb při selhání

| Rozhraní API | Popis |
| --- | --- |
| Nové AzureRmSqlDatabaseInstanceFailoverGroup |Tento příkaz vytvoří skupinu převzetí služeb při selhání a zaregistruje ho na primární a sekundární server|
| Set-AzureRmSqlDatabaseInstanceFailoverGroup |Upraví konfiguraci skupiny převzetí služeb při selhání|
| Get-AzureRmSqlDatabaseInstanceFailoverGroup |Načte konfiguraci skupiny převzetí služeb při selhání|
| Přepínač AzureRmSqlDatabaseInstanceFailoverGroup |Aktivační události převzetí služeb při selhání skupiny převzetí služeb při selhání na sekundární server|

### <a name="manage-sql-database-failover-using-the-rest-api"></a>Správa SQL database převzetí služeb při selhání pomocí rozhraní REST API

#### <a name="managing-failover-with-single-and-pooled-databases-using-rest-api"></a>Správa převzetí služeb při selhání jednoho a ve fondu s databázemi s využitím rozhraní REST API

| Rozhraní API | Popis |
| --- | --- |
| [Vytvořit nebo aktualizovat databázi (createMode = obnovení)](https://docs.microsoft.com/rest/api/sql/databases/createorupdate) |Vytvoří, aktualizuje nebo obnoví primární nebo sekundární databáze. |
| [Získat vytvořit nebo aktualizovat stav databáze](https://docs.microsoft.com/rest/api/sql/databases/createorupdate) |Vrátí stav během operace vytvoření. |
| [Nastavit jako primární (plánované převzetí služeb při selhání) sekundární databáze](https://docs.microsoft.com/rest/api/sql/replicationlinks/failover) |Nastaví, které replika databáze je primární podle přebírání služeb při selhání z aktuální primární repliku databáze. **Tato možnost není podporována pro Managed Instance.**|
| [Nastavit jako primární (neplánované převzetí služeb při selhání) sekundární databáze](https://docs.microsoft.com/rest/api/sql/replicationlinks/failoverallowdataloss) |Nastaví, které replika databáze je primární podle přebírání služeb při selhání z aktuální primární repliku databáze. Tato operace může dojít ke ztrátě. **Tato možnost není podporována pro Managed Instance.**|
| [Získání odkazu replikace](https://docs.microsoft.com/rest/api/sql/replicationlinks/get) |Získá konkrétní replikační připojení pro danou databázi SQL ve spolupráci geografickou replikaci. Načte informace v zobrazení katalogu sys.geo_replication_links viditelné. **Tato možnost není podporována pro Managed Instance.**|
| [Propojení replikace - seznam podle databáze](https://docs.microsoft.com/rest/api/sql/replicationlinks/listbydatabase) | Získá všechny odkazy replikace pro danou databázi SQL ve spolupráci geografickou replikaci. Načte informace v zobrazení katalogu sys.geo_replication_links viditelné. |
| [Odstranit propojení replikace](https://docs.microsoft.com/rest/api/sql/replicationlinks/delete) | Odstraní propojení replikace databáze. Nelze provést během převzetí služeb při selhání. |
| [Vytvořit nebo aktualizovat skupinu převzetí služeb při selhání](https://docs.microsoft.com/rest/api/sql/failovergroups/createorupdate) | Vytvoří nebo aktualizuje skupinu převzetí služeb při selhání |
| [Odstranění skupiny převzetí služeb při selhání](https://docs.microsoft.com/rest/api/sql/failovergroups/delete) | Odebere skupinu převzetí služeb při selhání ze serveru |
| [Převzetí služeb při selhání (plánovaná)](https://docs.microsoft.com/rest/api/sql/failovergroups/failover) | Převezme služby při selhání z aktuálního primárního serveru na tomto serveru. |
| [Povolit Vynucené převzetí služeb při selhání ztráty dat](https://docs.microsoft.com/rest/api/sql/failovergroups/forcefailoverallowdataloss) |přes ails z aktuálního primárního serveru na tomto serveru. Tato operace může dojít ke ztrátě. |
| [Získat skupinu převzetí služeb při selhání](https://docs.microsoft.com/rest/api/sql/failovergroups/get) | Načte skupinu převzetí služeb při selhání. |
| [Seznam skupiny převzetí služeb při selhání serverem](https://docs.microsoft.com/rest/api/sql/failovergroups/listbyserver) | Seznam skupin převzetí služeb při selhání na serveru. |
| [Aktualizace skupiny převzetí služeb při selhání](https://docs.microsoft.com/rest/api/sql/failovergroups/update) | Aktualizace skupiny převzetí služeb při selhání. |
|  | |

#### <a name="managing-failover-groups-with-managed-instances-using-rest-api-preview"></a>Správa skupin převzetí služeb při selhání s Managed instance pomocí rozhraní REST API (preview)

| Rozhraní API | Popis |
| --- | --- |
| [Vytvořit nebo aktualizovat skupinu převzetí služeb při selhání](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/createorupdate) | Vytvoří nebo aktualizuje skupinu převzetí služeb při selhání |
| [Odstranění skupiny převzetí služeb při selhání](https://docs.microsoft.com/rest/api/instancefailovergroups/delete) | Odebere skupinu převzetí služeb při selhání ze serveru |
| [Převzetí služeb při selhání (plánovaná)](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/failover) | Převezme služby při selhání z aktuálního primárního serveru na tomto serveru. |
| [Povolit Vynucené převzetí služeb při selhání ztráty dat](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/forcefailoverallowdataloss) |přes ails z aktuálního primárního serveru na tomto serveru. Tato operace může dojít ke ztrátě. |
| [Získat skupinu převzetí služeb při selhání](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/get) | Načte skupinu převzetí služeb při selhání. |
| [Seznam skupiny převzetí služeb při selhání – seznam podle umístění](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/listbylocation) | Seznam skupin převzetí služeb při selhání na místě. |

## <a name="next-steps"></a>Další postup

- Ukázky skriptů najdete tady:
  - [Konfigurace a převzetí služeb při selhání izolované databáze s využitím aktivní geografické replikace](scripts/sql-database-setup-geodr-and-failover-database-powershell.md)
  - [Konfigurace a převzetí služeb při selhání databáze ve fondu s využitím aktivní geografické replikace](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md)
  - [Konfigurace a převzetí služeb při selhání skupiny převzetí služeb při selhání pro izolovanou databázi](scripts/sql-database-setup-geodr-failover-database-failover-group-powershell.md)
- Přehled zajištění provozní kontinuity podnikání a scénáře, naleznete v tématu [přehled zajištění provozní kontinuity firmy](sql-database-business-continuity.md)
- Další informace o Azure SQL Database, automatické zálohování, naleznete v tématu [automatické zálohování SQL Database](sql-database-automated-backups.md).
- Další informace o obnovení pomocí automatizovaného zálohování, naleznete v tématu [obnovení databáze ze záloh spouštěných službou](sql-database-recovery-using-backups.md).
- Další informace o požadavcích na ověřování pro novým primárním serverem a databází, naleznete v tématu [zabezpečení služby SQL Database po zotavení po havárii](sql-database-geo-replication-security-config.md).
