---
title: Převzetí služeb při selhání skupiny a aktivní geografickou replikaci - Azure SQL Database | Microsoft Docs
description: Použití skupin automatické převzetí služeb při selhání s aktivní geografickou replikací a povolte autoomatic převzetí služeb při selhání v případě výpadku.
services: sql-database
author: anosov1960
manager: craigg
ms.service: sql-database
ms.custom: business continuity
ms.topic: article
ms.date: 04/04/2018
ms.author: sashan
ms.reviewer: carlrab
ms.openlocfilehash: fc3dca82bea17b44f66b433f59e5861da3bb0ca2
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="overview-failover-groups-and-active-geo-replication"></a>Přehled: Převzetí služeb při selhání skupiny a aktivní geografickou replikaci
Aktivní geografickou replikaci můžete nakonfigurovat až čtyři čitelný sekundární databáze v umístění center stejný nebo jiný dat (oblastí). Sekundární databáze jsou k dispozici pro dotazování a převzetí služeb při selhání v případě výpadku datacentra nebo neschopnost se připojit k primární databázi. Převzetí služeb při selhání musí ručně zahájit aplikace uživatele. Po převzetí služeb při selhání má nový primární koncový bod jiné připojení. 

> [!NOTE]
> Aktivní geografickou replikací je k dispozici pro všechny databáze na všech úrovních služby ve všech oblastech.
>  

Automatické převzetí služeb při selhání skupiny Azure SQL Database (-preview) je navržená tak, aby automaticky spravovat relace geografická replikace, připojení a převzetí služeb při selhání ve velkém měřítku funkce SQL Database. Pomocí něho zákazníků získáte možnost automaticky obnovit více souvisejících databáze v sekundární oblasti po závažné regionální chyby nebo další neplánované události, které dojít ke ztrátě úplné nebo částečné dostupnosti služby SQL Database v primární oblasti. Kromě toho použitím čitelný sekundární databáze k přesměrování zpracování úlohy jen pro čtení.  Protože skupiny automatické převzetí služeb při selhání zahrnují více databází, musí být nakonfigurované na primárním serveru. Primární a sekundární servery musí být ve stejném předplatném. Automatické převzetí služeb při selhání skupiny podporují replikace všech databází ve skupině jenom jedné sekundární server v jiné oblasti. Aktivní geografickou replikaci, bez skupiny automatické převzetí služeb při selhání umožňuje až čtyři sekundární databáze v libovolné oblasti.

Pokud používáte aktivní geografickou replikaci a z některého důvodu vaše selže primární databáze nebo jednoduše do režimu offline, můžete spustit převzetí služeb při selhání k žádné ze sekundárních databází. Při převzetí služeb při selhání se aktivuje na jednu sekundární databáze, všechny ostatní sekundární repliky jsou automaticky propojení se nový primární. Pokud používáte skupiny automatické převzetí služeb při selhání (v preview) ke správě obnovení databáze a všechny výpadek, který má dopad na jeden nebo několik databází ve výsledcích skupiny v automatické převzetí služeb při selhání. Můžete nakonfigurovat zásady automatické převzetí služeb při selhání, který bude nejlépe vyhovovat potřebám vaší aplikace, nebo můžete odhlásit a použít ruční aktivaci. Kromě toho skupiny automatické převzetí služeb při selhání (v preview) nabízí pro čtení a zápis a koncových bodů naslouchání jen pro čtení, které zůstanou beze změny během převzetí služeb při selhání. Při použití aktivace ruční nebo automatické převzetí služeb při selhání, převzetí služeb při selhání primární přepne všechny sekundární databáze ve skupině. Po dokončení převzetí služeb při selhání databázi se automaticky aktualizuje záznam DNS pro přesměrování koncových bodů pro novou oblast.

Můžete spravovat replikace a převzetí služeb při selhání může jedna databáze nebo sada databází na serveru nebo ve fondu elastické databáze používá aktivní geografickou replikaci. Můžete to, že pomocí 

- [Portálu Azure](sql-database-geo-replication-portal.md)
- [Prostředí PowerShell: Izolované databáze](scripts/sql-database-setup-geodr-and-failover-database-powershell.md)
- [Prostředí PowerShell: Elastický fond](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md)
- [Prostředí PowerShell: Převzetí služeb při selhání skupiny](scripts/sql-database-setup-geodr-failover-database-failover-group-powershell.md)
- [Příkaz Transact-SQL: Jedné databáze nebo elastického fondu](/sql/t-sql/statements/alter-database-azure-sql-database)
- [REST API: Izolované databáze](/rest/api/sql/replicationlinks/failover)
- [Rozhraní API REST: Převzetí služeb při selhání skupiny](/rest/api/sql/failovergroups/failover). 
 
Po převzetí služeb při selhání Ujistěte se, že požadavky na ověřování pro server a databáze jsou nakonfigurovány na nový primární. Podrobnosti najdete v tématu [zabezpečení SQL Database po zotavení po havárii](sql-database-geo-replication-security-config.md). To platí i na aktivní skupiny geografická replikace a automatické převzetí služeb při selhání (v preview).

Využívá aktivní geografickou replikací [Always On](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server) technologii SQL serveru na asynchronně replikovat potvrzené transakce v databázi primární na sekundární databázi pomocí číst izolaci snímku potvrdit (RCSI). Automatické převzetí služeb při selhání skupiny poskytují sémantiku skupiny nad aktivní geografickou replikaci, ale se používá stejný mechanismus asynchronní replikaci. V libovolném časovém okamžiku sekundární databáze může být mírně za primární databázi, sekundární data záruku, že se nikdy mít částečné transakce. Mezi oblastmi redundance umožňuje aplikacím rychle zotavit trvalé ztrátě celého datového centra nebo částí datacentru způsobené přírodní katastrofy, lidské chyby závažné nebo škodlivý jednání. Konkrétní data plánovaný bod obnovení naleznete na adrese [přehled kontinuity podnikových procesů](sql-database-business-continuity.md).

Následující obrázek znázorňuje příklad aktivní geografickou replikací nakonfigurované s primární v oblasti severní jihu USA a sekundární v oblasti jihu USA.

![geografická replikace relace](./media/sql-database-active-geo-replication/geo-replication-relationship.png)

Protože jsou čitelný sekundární databáze, můžete použít k přesměrování zpracování úlohy jen pro čtení, například úlohy sestav. Pokud používáte aktivní geografickou replikaci, je možné vytvořit sekundární databázi ve stejné oblasti s primární, ale nezvyšuje aplikace odolnost proti závažné selhání. Pokud používáte skupiny automatické převzetí služeb při selhání (v preview), je vždy vytvořit sekundární databáze v jiné oblasti.

Kromě po havárii obnovení aktivní geografickou replikaci lze použít v následujících scénářích:

* **Migrace databáze**: aktivní geografickou replikaci můžete použít k migraci databáze z jednoho serveru na jiný online s minimální výpadky.
* **Upgrady aplikací**: můžete vytvořit další sekundární jako kopii back selhání během upgradů aplikací.

Přidání redundance databáze mezi datovými centry je zajistit skutečné provozní kontinuitu pouze část řešení. Aplikaci (služba) na kompletní obnovení po závažné chybě vyžaduje obnovení všechny součásti, které tvoří službu a všechny závislé služby. Všechny tyto komponenty příklady klientský software (například prohlížeč s vlastní JavaScript), webové servery front-end, úložiště a DNS. Je důležité, aby všechny součásti jsou odolné vůči selhání stejné a bude k dispozici v rámci cíli času obnovení (RTO) vaší aplikace. Proto musíte určit všechny závislé služby a porozumět záruky a možnosti, které poskytují. Pak musíte provést odpovídající kroky zajistit, aby funkcí služby během převzetí služeb při selhání služeb, na kterých závisí. Další informace o návrhu řešení pro zotavení po havárii, najdete v části [návrhu cloudové řešení pro obnovení po havárii pomocí aktivní geografickou replikaci](sql-database-designing-cloud-solutions-for-disaster-recovery.md).

## <a name="active-geo-replication-capabilities"></a>Možnosti aktivní geografickou replikaci
Tato funkce aktivní geografickou replikací poskytuje následující základní možnosti:
* **Automatické asynchronní replikaci**: přidáním k existující databázi lze vytvořit pouze sekundární databáze. Sekundární lze vytvořit v jakékoli serveru Azure SQL Database. Po vytvoření se zobrazí v sekundární databázi daty zkopírovanými z primární databáze. Tento proces se označuje jako synchronizace replik indexů. Po sekundární databáze byla vytvořena a nasadí, aktualizace primární databázi se asynchronně replikují do sekundární databáze automaticky. Asynchronní replikaci znamená, že transakce jsou potvrzeno u primární databázi, předtím, než se replikují do sekundární databáze. 
* **Čitelný sekundární databáze**: aplikace, můžete přístup k sekundární databázi operacím jen pro čtení pomocí objekty zabezpečení stejný nebo jiný používá pro přístup k primární databázi. Sekundární databáze v režimu izolace snímku zajistit replikaci aktualizací primární (protokol opětovného přehrání) není zpožděn dotazy spouštěné na sekundárním fungovat.

   > [!NOTE]
   > Pokud jsou dostupné aktualizace schématu na primárním opětovného přehrání protokolu zpožděno v sekundární databázi. Druhá možnost vyžaduje zámek schématu v sekundární databázi. 
   > 

* **Více čitelný sekundární repliky**: dvě nebo více sekundárních databází zvýšit redundance a úroveň ochrany pro primární databáze a aplikace. Existuje více sekundární databáze, i nadále aplikace chráněn, i když dojde k selhání jednoho sekundární databáze. Pokud existuje pouze jedna sekundární databáze, a se nezdaří, aplikace je vystaven vyšší riziko teprve po vytvoření nové sekundární databáze.

   > [!NOTE]
   > Pokud používáte k sestavení globálně distribuované aplikace a potřebujete poskytovat přístup jen pro čtení k datům ve více než čtyři oblasti aktivní geografickou replikaci, můžete vytvořit sekundární sekundární (Tento proces se označuje jako řetězení). Tímto způsobem můžete dosáhnout prakticky neomezené škálování replikace databáze. Kromě toho řetězení snižuje zatížení replikace z primární databáze. O kompromisu je prodleva vyšší replikace na listu většinu sekundární databáze. 
   >

* **Podpora fondu elastické databáze**: aktivní geografickou replikací je možné nakonfigurovat pro všechny databáze v každém elastického fondu. Sekundární databáze může být v jiném elastického fondu. Pro normální databáze lze sekundární elastický fond a naopak naopak tak dlouho, dokud úrovně služeb jsou stejné. 
* **Úroveň výkonu konfigurovat sekundární databáze**: primární i sekundární databáze musí mít stejnou vrstvu služeb. Sekundární databáze může být vytvořen pomocí nižší úroveň výkonu (Dtu) než primární. Tato možnost není doporučeno pro aplikace s aktivitou write vysoké databáze, protože vyšší replikace funkce lag zvyšuje riziko ztráty významné dat po selhání. Kromě toho po převzetí služeb při selhání aplikace je ovlivněn výkon dokud nový primární je upgrade na vyšší úroveň výkonu. Graf procento vstupně-výstupní operace protokolu na portál Azure poskytuje vhodný způsob, jak odhad úrovně výkonu minimální sekundární, který je požadován pro udržení zatížení replikace. Například, pokud je primární databáze P6 (1 000 DTU) a protokol vstupně-výstupní operace v procentech je 50 % sekundární musí být alespoň P4 (500 DTU). Můžete také načíst data protokolu vstupně-výstupní operace s využitím [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) nebo [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) databáze zobrazení.  Další informace o úrovně výkonu databáze SQL najdete v tématu [co jsou úrovních služby databáze SQL](sql-database-service-tiers.md). 
* **Uživatelem řízená převzetí služeb při selhání a navrácení služeb po obnovení**: sekundární databáze je explicitně možné přepnout do primární role kdykoli aplikace nebo uživatele. Během výpadku skutečné možnost "neplánované" by měl použít, která okamžitě zvýší úroveň sekundárního jako primární. Při selhání primární obnoví a opět k dispozici, systém automaticky označí jako sekundární obnovené primární a převeďte ho do aktuální s nový primární. Vzhledem k asynchronní povaze replikace malé množství dat může dojít ke ztrátě během neplánované převzetí služeb při selhání před replikuje nejnovější změny sekundární selže primární. Při selhání primárního serveru s více sekundárních systém automaticky změní konfiguraci relace replikace a propojuje zbývající sekundární repliky na nově propagovaných primární bez nutnosti zásahu uživatele. Jakmile se výpadek, která způsobila, že převzetí služeb při selhání zmírnit, může být žádoucí návrat aplikace na primární oblasti. Kvůli tomu by měla být volána příkaz převzetí služeb při selhání s parametrem "plánované". 
* **Udržování synchronizace přihlašovacích údajů a pravidel brány firewall**: doporučujeme používat [databáze pravidla brány firewall](sql-database-firewall-configure.md) pro geograficky replikované databáze, takže tato pravidla je možné replikovat s databází a zkontrolujte všechny sekundární databáze stejná pravidla brány firewall jako primární. Tento přístup eliminuje potřebu zákazníkům ručně konfigurovat a spravovat pravidla brány firewall na servery, které hostují jak primární a sekundární databáze. Podobně [obsažené uživatelů databáze](sql-database-manage-logins.md) pro data přístup zajišťuje primární i sekundární databáze mají vždy stejné přihlašovací údaje uživatele, takže během převzetí služeb při selhání není k dispozici žádné přerušení z důvodu neshody s jména a hesla. Po přidání [Azure Active Directory](../active-directory/active-directory-whatis.md), zákazníků můžete spravovat přístup uživatelů k primární i sekundární databáze a odstraňuje potřebu správy pověření v databázích úplně.

## <a name="auto-failover-group-capabilities"></a>Možnosti skupiny-automatické převzetí služeb při selhání

Funkce skupiny-automatické převzetí služeb při selhání poskytuje výkonné abstrakce aktivní geografickou replikací podporou úrovně replikační skupiny a automatické převzetí služeb při selhání. Kromě toho se odeberou nezbytné, chcete-li změnit připojovací řetězec SQL po převzetí služeb při selhání tím, že poskytuje další naslouchací proces koncových bodů. 

* **Převzetí služeb při selhání skupiny**: jednu nebo více skupin převzetí služeb při selhání můžete vytvořit mezi dvěma servery v různých oblastech (primární a sekundární servery). Každá skupina může obsahovat jednu nebo několik databází, které se obnoví jako jednotku, v případě, že všechny nebo některé primární databáze k dispozici z důvodu výpadku v primární oblasti.  
* **Primární server**: server, který je hostitelem primární databází ve skupině převzetí služeb při selhání.
* **Sekundární server**: server, který je hostitelem sekundární databází ve skupině převzetí služeb při selhání. Sekundární server nemůže být ve stejné oblasti jako primární server.
* **Přidávání databází do převzetí služeb při selhání skupiny**: několik databází v rámci serveru nebo fondu elastické databáze můžete vložit do stejné skupiny převzetí služeb při selhání. Pokud přidáte samostatná databáze ke skupině, automaticky vytvoří sekundární databáze pomocí stejného vydání a úroveň výkonu. Pokud je primární databází v elastickém fondu, sekundární je automaticky vytvořen ve elastický fond se stejným názvem. Pokud přidáte databázi, která už má sekundární databáze v sekundárním serveru, geografická replikace zdědí skupině.

   > [!NOTE]
   > Při přidávání databázi, která už má sekundární databáze na serveru, který není součástí skupiny převzetí služeb při selhání, se vytvoří nový sekundární v sekundárním serveru. 
   >

* **Naslouchací proces pro čtení a zápis převzetí služeb při selhání skupiny**: záznam DNS CNAME je vytvořen jako  **&lt;název skupiny pro převzetí služeb při selhání&gt;. database.windows.net** který odkazuje na adresu URL aktuální primární server. To umožňuje aplikacím SQL pro čtení a zápis se transparentně znovu připojit k primární databázi, když se změní primární po převzetí služeb při selhání. 
* **Převzetí služeb při selhání jen pro čtení naslouchací proces skupiny**: záznam DNS CNAME je vytvořen jako  **&lt;název skupiny pro převzetí služeb při selhání&gt;. secondary.database.windows.net** který odkazuje na adresu URL na sekundární server. To umožňuje aplikacím SQL jen pro čtení transparentně připojit k sekundární databázi pomocí zadaného pravidla Vyrovnávání zatížení. Volitelně můžete zadat, pokud chcete jen pro čtení provozu, který se automaticky přesměruje na primární server, když sekundární server není k dispozici.
* **Zásady automatické převzetí služeb při selhání**: ve výchozím nastavení je skupině převzetí služeb při selhání nakonfigurovaná pomocí zásad automatické převzetí služeb při selhání. Systém se aktivuje převzetí služeb při selhání při selhání je zjištěna. Pokud chcete řídit pracovní postup převzetí služeb při selhání z aplikace, můžete vypnout automatické převzetí služeb při selhání. 
* **Ruční převzetí služeb při selhání**: převzetí služeb při selhání můžete spustit ručně kdykoli bez ohledu na konfiguraci automatického převzetí služeb při selhání. Pokud automatické převzetí služeb při selhání zásady nejsou nakonfigurované, ruční převzetí služeb při selhání bude potřeba k obnovení databází ve skupině pro převzetí služeb při selhání. Můžete zahájit vynucené nebo popisný převzetí služeb při selhání (úplná synchronizace dat). K tomu může přemístit na primární oblasti aktivního serveru. Po dokončení převzetí služeb při selhání se automaticky aktualizují záznamy DNS zajistit připojení ke správnému serveru.
* **Období odkladu ztráty dat.**: protože primárních a sekundárních databází se synchronizují pomocí asynchronní replikaci, převzetí služeb při selhání může dojít ke ztrátě dat. Zásady automatické převzetí služeb při selhání tak, aby odrážela vaší aplikace odolnost proti ztrátě dat můžete přizpůsobit. Nakonfigurováním **GracePeriodWithDataLossHours**, můžete řídit, jak dlouho systém čekat před zahájením převzetí služeb při selhání, který je nejspíš ztrátě dat výsledek. 

   > [!NOTE]
   > Když systém zjistí, že je stále online databází ve skupině (například se výpadek pouze ovlivněn rovině řízení služby), okamžitě bez ohledu na hodnotu, která nastavuje aktivujepřevzetíslužebpřiselháníseúplnásynchronizacedat(popisnýpřevzetíslužebpřiselhání) **GracePeriodWithDataLossHours**. To zaručuje, že nedochází ke ztrátě dat během obnovení. Období odkladu se projeví pouze v případě, že popisný převzetí služeb při selhání není možné. Pokud se výpadek zmírnit před vypršení časového limitu, není aktivována převzetí služeb při selhání.
   >

* **Více skupin převzetí služeb při selhání**: můžete nakonfigurovat více skupin převzetí služeb při selhání pro stejného páru serverů k řízení rozsahu převzetí služeb při selhání. Každá skupina převezme nezávisle. Pokud vaše aplikace víceklientské používá elastické fondy, můžete tato funkce kombinovat primární a sekundární databáze v každém fondu. Tímto způsobem můžete snížit dopad výpadku na polovinu ze klienty.

## <a name="best-practices-of-building-highly-available-service"></a>Osvědčené postupy vytváření službu s vysokou dostupností

K vytvoření vysoce dostupnou službu, která používá databázi Azure SQL, postupujte podle těchto pokynů:

- **Použití převzetí služeb při selhání skupiny**: jednu nebo více skupin převzetí služeb při selhání můžete vytvořit mezi dvěma servery v různých oblastech (primární a sekundární servery). Každá skupina může obsahovat jednu nebo několik databází, které se obnoví jako jednotku, v případě, že všechny nebo některé primární databáze k dispozici z důvodu výpadku v primární oblasti. Skupina převzetí služeb při selhání vytvoří geograficky sekundární databáze se stejný cíl služby jako primární. Pokud přidáte existující relaci geografická replikace ke skupině převzetí služeb při selhání, zkontrolujte, zda že GEO sekundární je nakonfigurován pomocí stejné cíle na úrovni služby jako primární.
- **Použijte naslouchací proces pro čtení a zápis pro pracovní vytížení OLTP**: při provádění operací OLTP, použijte  **&lt;název skupiny pro převzetí služeb při selhání&gt;. database.windows.net** jako server jsou adresy URL a připojení automaticky přesměruje na primární. Tato adresa URL nezmění po převzetí služeb při selhání.  
Všimněte si, že že převzetí služeb při selhání zahrnuje aktualizace, které se DNS záznam, připojení klienta přesměrování na nový primární pouze po aktualizovat DNS mezipaměti klienta.
- **Použijte jen pro čtení naslouchací proces pro pracovní vytížení jen pro čtení**: Pokud máte logicky izolované jen pro čtení zatížení, která je odolný vůči chybám pro určité typu prošlostí dat, sekundární databázi můžete v aplikaci. Pro relace jen pro čtení, použijte  **&lt;název skupiny pro převzetí služeb při selhání&gt;. secondary.database.windows.net** jako server adresy URL a připojení se automaticky přesměruje na sekundární. Dále je doporučeno, abyste určili v připojovacím řetězci číst záměr pomocí **ApplicationIntent = jen pro čtení**. 
- **Připravit pro snížení výkonu**: SQL rozhodovací převzetí služeb při selhání je nezávislé na zbytek dalších služeb používaných nebo aplikace. Aplikace může být "smíšený" s některé součásti v jedné oblasti a některé v jiném. Abyste se vyhnuli snížení výkonu, zajistěte nasazení redundantní aplikace v oblasti zotavení po Havárii a postupujte podle pokynů v tomto článku.  
Poznámka: v oblasti zotavení po Havárii aplikace nebude muset použít jiné připojovací řetězec.  
- **Připravit na ztrátu dat**: Pokud je zjištěn výpadek, SQL automaticky aktivuje převzetí služeb při selhání pro čtení a zápis, pokud je nulové ztráty dat na našem známo. V opačném čeká po dobu určenou ve **GracePeriodWithDataLossHours**. Pokud jste zadali **GracePeriodWithDataLossHours**, připravit ztráty dat. Obecně platí během výpadky, Azure upřednostňuje dostupnost. Pokud nechcete ztrátě dat, nezapomeňte nastavit **GracePeriodWithDataLossHours** na dostatečně velký počet, jako je například 24 hodin. 

> [!IMPORTANT]
> Elastické fondy s Dtu 800 nebo méně a více než 250 databází pomocí geografická replikace může dojít k potížím, včetně již plánované převzetí služeb při selhání a snížení výkonu.  Tyto problémy budou s větší pravděpodobností vyskytují pro zatížení s intenzivním zápisu, když jsou koncové body geografická replikace daleko od podle Geografie, nebo když několik sekundární koncových bodů se používají pro každou databázi.  Příznaky tyto problémy jsou vypsány při geografická replikace funkce lag zvyšuje v čase.  Toto opoždění je možné monitorovat pomocí [sys.dm_geo_replication_link_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database).  Pokud dojde k těmto problémům, jejich zmírnění zahrnují zvýšit počet jednotek Dtu fondu nebo snížit počet geograficky replikované databází ve stejném fondu.

## <a name="upgrading-or-downgrading-a-primary-database"></a>Upgrade nebo přechod na starší verzi primární databáze
Můžete upgradovat nebo starší verzi primární databázi na jiný výkonu úroveň (v rámci stejné služby vrstvě) bez odpojení žádné sekundární databáze. Při upgradu, doporučujeme nejprve upgradovat sekundární databáze a pak upgradovat primární. Když přechod na starší verzi, změnit pořadí: nejdřív se downgradovat primární a poté downgradovat sekundární. Pokud upgradujete nebo starší verzi databáze do jiné služby vrstvy, prosazuje se toto doporučení. 

> [!NOTE]
> Pokud jste vytvořili sekundární databáze v rámci konfigurace skupiny převzetí služeb při selhání se nedoporučuje na starší verzi sekundární databázi. To je potřeba zajistit, že datové vrstvy má dostatečnou kapacitu pro zpracování běžné zatížení po aktivaci převzetí služeb při selhání. 
>

## <a name="preventing-the-loss-of-critical-data"></a>Zabránění ztrátě důležitých dat
Z důvodu vysoké latenci sítě WAN průběžná kopie používá mechanismus asynchronní replikaci. Asynchronní replikaci lze ztrátu dat nevyhnutelné když dojde k chybě. Nedošlo ke ztrátě dat však mohou vyžadovat některé aplikace. Chcete-li chránit tyto důležité aktualizace, vývojář aplikací zavolat [uložená procedura sp_wait_for_database_copy_sync](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync) postup systému ihned po potvrzení transakce. Volání metody **uložená procedura sp_wait_for_database_copy_sync** blokuje volající vlákno, dokud poslední potvrzené transakce bylo přeneseno do sekundární databáze. Však není čekat na přenášená transakce přehrány a potvrzeno u sekundární. **uložená procedura sp_wait_for_database_copy_sync** je vymezen na určité průběžná kopie odkaz. Tento postup můžete volat každý uživatel s právy k připojení k primární databázi.

> [!NOTE]
> **uložená procedura sp_wait_for_database_copy_sync** zabrání ztrátě dat. po převzetí služeb při selhání, ale nezaručí úplnou synchronizaci pro přístup pro čtení. Zpoždění způsobené **uložená procedura sp_wait_for_database_copy_sync** volání procedur může být významný a závisí na velikosti transakčního protokolu v době volání. 
> 

## <a name="programmatically-managing-failover-groups-and-active-geo-replication"></a>Prostřednictvím kódu programu Správa skupin převzetí služeb při selhání a aktivní geografickou replikaci
Jak je popsáno dříve, skupiny automatické převzetí služeb při selhání (v preview) a aktivní geografickou replikaci lze spravovat také programově pomocí Azure PowerShell a rozhraní REST API. Následující tabulky popisují sadu příkazů, které jsou k dispozici.

**Rozhraní API Azure Resource Manager a na základě rolí zabezpečení**: aktivní geografickou replikací zahrnuje sadu rozhraní API Správce Azure Resource Manager pro správu, včetně [Azure SQL Database REST API](https://docs.microsoft.com/rest/api/sql/) a [Azure Rutiny prostředí PowerShell](https://docs.microsoft.com/powershell/azure/overview). Tato rozhraní API vyžadují použití skupin prostředků a podporují zabezpečení na základě role (RBAC). Další informace o tom, jak implementovat přístup rolí najdete v tématu [řízení přístupu](../role-based-access-control/overview.md).

## <a name="manage-sql-database-failover-using-transact-sql"></a>Správa SQL databáze převzetí služeb při selhání pomocí jazyka Transact-SQL

| Příkaz | Popis |
| --- | --- |
| [Příkaz ALTER DATABASE (databáze Azure SQL)](/sql/t-sql/statements/alter-database-azure-sql-database) |Přidat sekundární SERVER ON argument použít k vytvoření sekundární databáze pro stávající databáze a spustí replikaci dat |
| [Příkaz ALTER DATABASE (databáze Azure SQL)](/sql/t-sql/statements/alter-database-azure-sql-database) |Pomocí funkce převzetí služeb při selhání nebo FORCE_FAILOVER_ALLOW_DATA_LOSS přepínač sekundární databáze, která bude primární k zahájení převzetí služeb při selhání |
| [Příkaz ALTER DATABASE (databáze Azure SQL)](/sql/t-sql/statements/alter-database-azure-sql-database) |Odebrat sekundární SERVER ON použijte k ukončení replikaci dat mezi SQL Database a zadaný sekundární databázi. |
| [Sys.geo_replication_links (Azure SQL Database)](/sql/relational-databases/system-dynamic-management-views/sys-geo-replication-links-azure-sql-database) |Vrátí informace o všechna existující replikační připojení pro každou databázi na logického serveru Azure SQL Database. |
| [Sys.dm_geo_replication_link_status (Azure SQL Database)](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database) |Získá čas poslední replikace, poslední replikace funkce lag a další informace o propojení replikace pro danou databázi SQL. |
| [Sys.dm_operation_status (Azure SQL Database)](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) |Zobrazuje stav pro všechny databázové operace, včetně stav odkazů replikace. |
| [sp_wait_for_database_copy_sync (Azure SQL Database)](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync) |způsobí, že aplikace počkejte, dokud všechny potvrzené transakce jsou replikována a potvrzena aktivní sekundární databáze. |
|  | |

## <a name="manage-sql-database-failover-using-powershell"></a>Správa SQL databáze převzetí služeb při selhání pomocí prostředí PowerShell

| Rutina | Popis |
| --- | --- |
| [Get-AzureRmSqlDatabase](/powershell/module/azurerm.sql/get-azurermsqldatabase) |Získá jednu nebo více databází. |
| [New-AzureRmSqlDatabaseSecondary](/powershell/module/azurerm.sql/new-azurermsqldatabasesecondary) |Vytvoří sekundární databázi pro existující databázi a spustí replikaci dat. |
| [Set-AzureRmSqlDatabaseSecondary](/powershell/module/azurerm.sql/set-azurermsqldatabasesecondary) |Přepne sekundární databázi na primární a zahájí tak převzetí služeb při selhání. |
| [Remove-AzureRmSqlDatabaseSecondary](/powershell/module/azurerm.sql/remove-azurermsqldatabasesecondary) |Ukončí replikaci dat mezi službou SQL Database a zadanou sekundární databází. |
| [Get-AzureRmSqlDatabaseReplicationLink](/powershell/module/azurerm.sql/get-azurermsqldatabasereplicationlink) |Získá vazby geografické replikace mezi službou Azure SQL Database a skupinou prostředků nebo SQL Serverem. |
| [New-AzureRmSqlDatabaseFailoverGroup](/powershell/module/azurerm.sql/set-azurermsqldatabasefailovergroup) |   Tento příkaz vytvoří skupinu převzetí služeb při selhání a zaregistruje ho na primární a sekundární servery|
| [Remove-AzureRmSqlDatabaseFailoverGroup](/powershell/module/azurerm.sql/remove-azurermsqldatabasefailovergroup) | Odebere skupinu převzetí služeb při selhání ze serveru a odstraní všechny sekundární databáze zahrnuté skupině |
| [Get-AzureRmSqlDatabaseFailoverGroup](/powershell/module/azurerm.sql/get-azurermsqldatabasefailovergroup) | Načte konfiguraci skupiny převzetí služeb při selhání |
| [Set-AzureRmSqlDatabaseFailoverGroup](/powershell/module/azurerm.sql/set-azurermsqldatabasefailovergroup) |   Upraví konfiguraci převzetí služeb při selhání skupiny |
| [Switch-AzureRMSqlDatabaseFailoverGroup](/powershell/module/azurerm.sql/switch-azurermsqldatabasefailovergroup) | Aktivační události převzetí služeb při selhání skupiny převzetí služeb při selhání na sekundární server |
|  | |

> [!IMPORTANT]
> Ukázkové skripty, najdete v části [konfigurace a převzetí služeb při selhání jedné databáze používá aktivní geografickou replikaci](scripts/sql-database-setup-geodr-and-failover-database-powershell.md), [konfigurace a převzetí služeb při selhání ve fondu databáze používá aktivní geografickou replikaci](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md), a [ Konfigurace a převzetí služeb při selhání převzetí služeb při selhání skupiny pro jednu databázi (preview)](scripts/sql-database-setup-geodr-failover-database-failover-group-powershell.md).
>

## <a name="manage-sql-database-failover-using-the-rest-api"></a>Správa SQL databáze převzetí služeb při selhání pomocí rozhraní REST API
| Rozhraní API | Popis |
| --- | --- |
| [Vytvoření nebo aktualizace databáze (createMode = obnovit)](/rest/api/sql/Databases/CreateOrUpdate) |Vytvoří, aktualizuje nebo obnoví primární nebo sekundární databáze. |
| [Získat vytvořit nebo aktualizovat stav databáze](/rest/api/sql/Databases/CreateOrUpdate) |Vrátí stav během operace vytvoření. |
| [Nastavit sekundární databáze jako primární (plánované převzetí služeb při selhání)](/rest/api/sql/replicationlinks/failover) |Nastaví které repliky databáze je primární podle převzetí služeb při selhání z aktuální primární repliku databáze. |
| [Nastavit sekundární databáze jako primární (neplánované převzetí služeb při selhání)](/rest/api/sql/replicationlinks/failoverallowdataloss) |Nastaví které repliky databáze je primární podle převzetí služeb při selhání z aktuální primární repliku databáze. Tato operace může způsobit ztrátu dat. |
| [Získání odkazu replikace](/rest/api/sql/replicationlinks/get) |Získá konkrétní replikace odkaz pro danou databázi SQL ve spolupráci se geografická replikace. Načítá informace zobrazené v zobrazení katalogu sys.geo_replication_links. |
| [Propojení replikace - seznamu databáze](/rest/api/sql/replicationlinks/listbydatabase) | Získá všechny odkazy replikace pro danou databázi SQL ve spolupráci se geografická replikace. Načítá informace zobrazené v zobrazení katalogu sys.geo_replication_links. |
| [Odstranit propojení replikace](/rest/api/sql/databases/delete) | Odstraní propojení replikace databáze. Nelze provést během převzetí služeb při selhání. |
| [Vytvořit nebo aktualizovat skupinu převzetí služeb při selhání](/rest/api/sql/failovergroups/createorupdate) | Vytvoří nebo aktualizuje skupinu převzetí služeb při selhání |
| [Odstranit skupinu převzetí služeb při selhání](/rest/api/sql/failovergroups/delete) | Odebere skupinu převzetí služeb při selhání ze serveru |
| [Převzetí služeb při selhání (plánovaná)](/rest/api/sql/failovergroups/failover) | Selhání se z aktuální primárního serveru na tento server. |
| [Vynucené převzetí služeb při selhání povolit ztrátě dat.](/rest/api/sql/failovergroups/forcefailoverallowdataloss) |přes ails z aktuální primárního serveru na tomto serveru. Tato operace může způsobit ztrátu dat. |
| [Get převzetí služeb při selhání skupiny](/rest/api/sql/failovergroups/get) | Načte skupinu převzetí služeb při selhání. |
| [Seznam skupin převzetí služeb při selhání serverem](/rest/api/sql/failovergroups/listbyserver) | Seznam skupin převzetí služeb při selhání na serveru. |
| [Převzetí služeb při selhání skupiny aktualizací](/rest/api/sql/failovergroups/update) | Aktualizuje skupinu převzetí služeb při selhání. |
|  | |

## <a name="next-steps"></a>Další postup
* Ukázkové skripty najdete v části:
   - [Konfigurace a převzetí služeb při selhání izolované databáze s využitím aktivní geografické replikace](scripts/sql-database-setup-geodr-and-failover-database-powershell.md)
   - [Konfigurace a převzetí služeb při selhání databáze ve fondu s využitím aktivní geografické replikace](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md)
   - [Konfigurace a převzetí služeb při selhání skupiny převzetí služeb při selhání pro izolovanou databázi (Preview)](scripts/sql-database-setup-geodr-failover-database-failover-group-powershell.md)
* Přehled kontinuity obchodních a scénářů najdete v tématu [obchodní kontinuity přehled](sql-database-business-continuity.md)
* Další informace o Azure SQL Database automatizované zálohování najdete v tématu [automatizované zálohování SQL Database](sql-database-automated-backups.md).
* Další informace o použití automatizované zálohování pro obnovení, najdete v části [obnovit databázi ze zálohy spouštěná služba](sql-database-recovery-using-backups.md).
* Další informace o požadavky na ověřování pro nové primární server a databáze najdete v tématu [zabezpečení SQL Database po zotavení po havárii](sql-database-geo-replication-security-config.md).

