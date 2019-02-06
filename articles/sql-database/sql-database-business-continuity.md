---
title: Provozní kontinuita v cloudu – obnovení databází – SQL Database | Dokumentace Microsoftu
description: Zjistěte, jak Azure SQL Database podporuje provozní kontinuitu v cloudu a obnovení databází a jak pomáhá udržovat klíčové cloudové aplikace v chodu.
keywords: provozní kontinuita, provozní kontinuita v cloudu, zotavení databáze po havárii, obnovení databáze
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: 998d4f45a2d4956e0e230bcf00636a965c35dd97
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/06/2019
ms.locfileid: "55755165"
---
# <a name="overview-of-business-continuity-with-azure-sql-database"></a>Přehled provozní kontinuity se službou Azure SQL Database

**Kontinuita podnikových procesů** ve službě Azure SQL Database odkazuje na mechanismy, zásady a postupy, které vaší firmě umožní pokračovat i v případě přerušení, zejména pro její výpočetní infrastruktury. Ve většině případů Azure SQL Database bude zpracovávat rušivé události, které může dojít v cloudovém prostředí a udržovat vaše aplikace a obchodní procesy spuštění. Existují však některé rušivé události, které nemohou být zpracovány SQL Database jako:

- Uživatel omylem odstranit nebo aktualizovat řádek v tabulce.
- Útočník úspěšně odstranit data nebo odstranit databázi.
- Zemětřesení způsobila výpadku a dočasné zakázané datového centra.

Tyto případy nemůže ovlivnit služba Azure SQL Database, bylo by proto nutné použít funkce provozní kontinuity ve službě SQL Database, která umožňuje obnovení dat a vaše aplikace spuštěná.

Tento přehled popisuje možnosti, které služba Azure SQL Database nabízí pro zajištění provozní kontinuity a zotavení po havárii. Další informace o možnosti, doporučení a kurzy pro zotavení z ničivých událostí, které by mohly způsobit ztrátu dat nebo nedostupnost databáze a aplikace přestanou být dostupné. Zjistěte, co můžete dělat, když chyba uživatele nebo aplikace ovlivňuje integritu dat, má k výpadku oblasti Azure nebo aplikace vyžaduje údržbu.

## <a name="sql-database-features-that-you-can-use-to-provide-business-continuity"></a>Funkce služby SQL Database, pomocí kterých můžete zajistit provozní kontinuitu

Z hlediska databáze existují čtyři hlavní scénáře potenciální přerušení:

- Místní selhání hardwaru nebo softwaru by to ovlivnilo uzel databáze, jako je například selhání disku.
- Poškození nebo odstranění dat obvykle ji způsobuje chybu aplikace nebo lidské chyby. Taková selhání jsou vnitřně specifické pro aplikaci a nelze jako pravidlo zjistit nebo automaticky omezeny infrastrukturu.
- Výpadku datového centra, pravděpodobně způsobené přírodními pohromami. Tento scénář vyžaduje určitou úroveň geografické redundance pomocí aplikace převzetí služeb při selhání na alternativních datové centrum.
- Upgrade nebo údržbu chyby neočekávané problémy, ke kterým dochází při plánované upgrady nebo údržby tak, aby aplikace nebo databáze může vyžadovat rychlé vrácení zpět do stavu předchozího databáze.

SQL Database nabízí několik funkce provozní kontinuity, včetně automatizovaných záloh a volitelné replikace databáze, která lze zmírnit tyto scénáře. Nejprve je třeba pochopit, jak SQL Database [architektura pro vysokou dostupnost](sql-database-high-availability.md) zajišťuje 99,99 % dostupnost a odolnost proti chybám pro některé rušivé události, které by mohly ovlivnit obchodních procesů.
Potom se dozvíte další mechanismy, které můžete použít k zotavení z ničivých událostí, které nemohou být zpracovány architektura pro vysokou dostupnost databáze SQL, jako například:

- [Dočasné tabulky](sql-database-temporal-tables.md) vám umožní obnovit verze řádků z libovolného bodu v čase.
- [Integrované automatické zálohování](sql-database-automated-backups.md) a [obnovení k časovému okamžiku](sql-database-recovery-using-backups.md#point-in-time-restore) umožňuje obnovit kompletní databáze do určitého bodu v čase za posledních 35 dnů.
- Je možné [obnovení odstraněné databáze](sql-database-recovery-using-backups.md#deleted-database-restore) do bodu, ve kterém byl odstraněn, pokud **databáze SQL serveru, nebyla Odstraněná**.
- [Dlouhodobé uchovávání záloh](sql-database-long-term-retention.md) umožňuje držet krok zálohy na 10 let.
- [Aktivní geografická replikace](sql-database-active-geo-replication.md) vám umožní vytvořit čitelné repliky a ruční převzetí služeb při selhání pro všechny repliky v případě upgrade na data center výpadku nebo aplikace.
- [Automatické převzetí služeb při selhání skupiny](sql-database-auto-failover-group.md#auto-failover-group-terminology-and-capabilities) umožňuje aplikaci automaticky obnovení v případě výpadku datového centra.

Každá má jiné vlastnosti ohledně odhadovaného času obnovení (ERT) a potenciální ztráty dat posledních transakcí. Jakmile tyto možnosti pochopíte, můžete si mezi nimi vybírat a ve většině scénářů je spolu kombinovat a používat pro různé scénáře. Při vývoji plánu provozní kontinuity musíte pochopit maximální přijatelnou dobu, než úplného obnovení aplikace po ničivé události. Čas potřebný pro aplikaci, aby se úplně zotavily se označuje jako plánovaná doba obnovení (RTO). Také musíte pochopit maximální období posledních aktualizací dat (časový interval) aplikace může tolerovat možnost, ztráty při obnovení po ničivé události. Časové období aktualizací, které si může dovolit přijít o se označuje jako cíl bodu obnovení (RPO).

Následující tabulka porovnává ERT a RPO pro každou vrstvu služby pro tři nejběžnější scénáře.

| Schopnost | Basic | Standard | Premium | Obecné použití | Pro důležité obchodní informace
| --- | --- | --- | --- |--- |--- |
| Obnovení k určitému bodu v čase ze zálohy |Libovolný bod obnovení do 7 dní |Libovolný bod obnovení do 35 dní |Libovolný bod obnovení do 35 dní |Libovolný bod obnovení v rámci nakonfigurované doby (až po 35 dnů)|Libovolný bod obnovení v rámci nakonfigurované doby (až po 35 dnů)|
| Geografické obnovení z geograficky replikovaných záloh |ERT < 12 h<br> RPO < 1 h |ERT < 12 h<br>RPO < 1 h |ERT < 12 h<br>RPO < 1 h |ERT < 12 h<br>RPO < 1 h|ERT < 12 h<br>RPO < 1 h|
| Skupiny automatického převzetí služeb při selhání |RTO = 1 h<br>RPO < 5s |RTO = 1 h<br>RPO < 5 s |RTO = 1 h<br>RPO < 5 s |RTO = 1 h<br>RPO < 5 s|RTO = 1 h<br>RPO < 5 s|

## <a name="recover-a-database-to-the-existing-server"></a>Obnovení databáze do existujícího serveru

SQL Database automaticky provádí kombinaci zálohování úplné databáze každý týden, všeobecně přijatá každých 12 hodin rozdílovými zálohami a zálohami protokolů každých 5 až 10 minut. Chcete-li chránit vaši firmu před ztrátou dat transakcí. Zálohy jsou uložené v úložišti RA-GRS po dobu 35 dní pro všechny úrovně služby s výjimkou základních jednotek DTU úrovně služeb ukládat zálohy po dobu 7 dní. Další informace najdete v tématu [automatické zálohování databází](sql-database-automated-backups.md). Můžete obnovit existující databáze formulář automatizovaných záloh k dřívějšímu bodu v čase jako novou databázi na stejný server SQL Database pomocí webu Azure portal, Powershellu nebo rozhraní REST API. Další informace najdete v tématu [v daném okamžiku obnovení](sql-database-recovery-using-backups.md#point-in-time-restore).

Pokud maximální podporované v daném okamžiku obnovit (PITR) doba uchovávání není pro vaši aplikaci dostatečná, můžete ji rozšířit konfigurací zásad dlouhodobého uchovávání (LTR) pro databáze. Další informace najdete v tématu [dlouhodobého uchovávání záloh](sql-database-long-term-retention.md).

Tyto automatické zálohy databáze můžete použít k obnovení databáze po různých ničivých událostech, a to jak v rámci vašeho datového centra, tak do jiného datového centra. Čas obnovení je obvykle méně než 12 hodin. Může trvat delší dobu velmi velký nebo aktivní databázi obnovit. Při použití automatických záloh databáze závisí odhadovaný čas obnovení na několika faktorech. Patří mezi ně celkový počet obnovovaných databází ve stejném regionu a ve stejnou dobu, velikost databáze, velikost protokolu transakcí a šířka pásma sítě. Další informace o čase obnovení najdete v tématu [databáze čas obnovení](sql-database-recovery-using-backups.md#recovery-time). Při obnovování do jiné oblasti dat je potenciální ztráta dat je omezena na 1 hodinu s využitím geograficky redundantní zálohy.

Automatizované zálohování použijte a [obnovení k určitému bodu v čase](sql-database-recovery-using-backups.md#point-in-time-restore) jako obchodní kontinuity podnikových procesů a zotavení mechanismus Pokud vaše aplikace:

- Není považována za klíčovou.
- Nemá zavazující smlouvu SLA – výpadek 24 hodin nebo již nemá za následek finanční závazky.
- Pracuje s nízkou mírou změn dat (málo transakcí za hodinu) a ztráta změn provedených během až jedné hodiny je přijatelnou ztrátou dat.
- Je citlivá na změny nákladů.

Pokud potřebujete rychlejší obnovení, použijte [aktivní geografickou replikaci](sql-database-active-geo-replication.md) nebo [-automatické převzetí služeb při selhání skupiny](sql-database-auto-failover-group.md). Pokud potřebujete mít možnost obnovit data z období staršího než 35 dní, použijte [dlouhodobé uchovávání](sql-database-long-term-retention.md).

## <a name="recover-a-database-to-another-region"></a>Obnovení databáze do jiné oblasti

Přestože je taková situace výjimečná, i u datového centra Azure může dojít k výpadku. Při výpadku dojde k narušení provozu, které může trvat jen několik minut nebo až několik hodin.

- Jednou z možností je počkat, až výpadek skončí a databáze se vrátí do režimu online. Tento postup funguje pro aplikace, které si mohou dovolit mít databázi v režimu offline. Například vývojový projekt nebo bezplatná zkušební verze, na které nemusíte neustále pracovat. Pokud datové centrum má k výpadku, můžete není známo, jak dlouho může trvat výpadek, proto tato možnost funguje jenom v případě nepotřebujete databázi nějakou dobu.
- Další možností je k obnovení databáze na libovolném serveru v libovolné oblasti Azure pomocí [geograficky redundantních záloh databáze](sql-database-recovery-using-backups.md#geo-restore) (geografické obnovení). Geografické obnovení pomocí geograficky redundantní zálohy jako zdroj a slouží k obnovení databáze, i když je nejsou dostupné kvůli výpadku databáze nebo datového centra.
- Nakonec můžete rychle obnovení po výpadku Pokud jste nakonfigurovali buď geografické repliky pomocí [aktivní geografickou replikaci](sql-database-active-geo-replication.md) nebo [-automatické převzetí služeb při selhání skupiny](sql-database-auto-failover-group.md) vaší databáze nebo databáze. Podle svého výběru: z těchto technologií můžete použít ruční nebo automatické převzetí služeb při selhání. Během převzetí služeb při selhání, samotný trvá jenom několik sekund, služba bude trvat nejméně 1 hodinu jej aktivovat. To je nezbytné k zajištění, že převzetí služeb při selhání je podloženo škálování výpadek. Navíc převzetí služeb při selhání může dojít ke ztrátě malé vzhledem k povaze asynchronní replikace. Zobrazit tabulku uvedenou výše v tomto článku najdete podrobnosti-automatické převzetí služeb při selhání RTO a RPO.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-protecting-important-DBs-from-regional-disasters-is-easy/player]
>
> [!IMPORTANT]
> Pokud chcete použít aktivní geografickou replikaci a skupiny – automatické převzetí služeb při selhání, musí být vlastníkem předplatného nebo mít oprávnění správce v systému SQL Server. Můžete nakonfigurovat a převzetí služeb při selhání pomocí Azure portal, Powershellu nebo rozhraní REST API použití oprávnění pro předplatné Azure nebo pomocí příkazů jazyka Transact-SQL s oprávněními systému SQL Server.

Použití skupin – automatické převzetí služeb při selhání, pokud vaše aplikace splňuje některá z těchto kritérií:

- Je zvlášť důležitá.
- Má smlouvy o úrovni služeb (SLA), který neumožňuje nejméně 12 hodin výpadku.
- Výpadek může mít za následek finanční závazky.
- Pracuje s vysokou mírou dat změnit a 1 hodina ztráty dat není platný.
- Další náklady na aktivní geografickou replikaci jsou nižší než potenciální finanční závazky a související ztráta podnikání.

Když přijmete opatření, jak dlouho trvá, vám umožní obnovit a množství ztracených dat v závisí na tom, jak se rozhodnete pomocí těchto funkcí provozní kontinuity ve vaší aplikaci. Ve skutečnosti můžete rozhodnout pro použití kombinace záloh databáze a aktivní geografickou replikaci v závislosti na požadavcích aplikace. Diskuzi o aspektech návrhu aplikací pro samostatné databáze a pro elastické fondy pomocí těchto funkcí provozní kontinuity, naleznete v tématu [návrh aplikace pro zotavení po havárii cloudu](sql-database-designing-cloud-solutions-for-disaster-recovery.md) a [Elastic fond strategie zotavení po havárii](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).

Následující části poskytují přehled postupů k obnovení pomocí záloh databáze nebo aktivní geografické replikace. Podrobné pokyny, včetně plánování požadavky, postupů po obnovení a informace o simulaci výpadku provedení postupu zotavení po havárii, najdete v článku [obnovení služby SQL Database po výpadku](sql-database-disaster-recovery.md).

### <a name="prepare-for-an-outage"></a>Příprava na výpadek

Bez ohledu na funkce provozní kontinuity, které používáte, musíte:

- Určit a připravit cílový server, včetně pravidel brány firewall na úrovni serveru IP, přihlášení a oprávnění na úrovni hlavní databáze.
- Určit, jak přesměrovat klienty a klientské aplikace na nový server
- Zdokumentovat další závislosti, například nastavení auditování a výstrahy

Pokud jste není nepřipravíte, může převod aplikací online po převzetí služeb při selhání nebo obnovení databáze bude vyžadovat čas navíc a pravděpodobně také vyžadovat řešení potíží ve vypjaté situaci - dobrá kombinace.

### <a name="fail-over-to-a-geo-replicated-secondary-database"></a>Převzetí služeb při selhání do geograficky replikované sekundární databáze

Pokud používáte jako mechanismus obnovení aktivní geografickou replikaci a skupiny – automatické převzetí služeb při selhání, můžete nakonfigurovat zásadu automatického převzetí služeb při selhání nebo použít [ruční převzetí služeb při selhání](sql-database-disaster-recovery.md#fail-over-to-geo-replicated-secondary-server-in-the-failover-group). Po zahájení převzetí služeb způsobí, že sekundární k nové primární a připravená zaznamenávat nové transakce a reagovat na dotazy – minimální ztráty dat ještě nebyl replikován. Informace o návrhu procesu převzetí služeb při selhání najdete v tématu [návrh aplikace pro zotavení po havárii cloudu](sql-database-designing-cloud-solutions-for-disaster-recovery.md).

> [!NOTE]
> Pokud datové centrum vrátí do režimu online staré primárek automaticky znovu připojit k nové primární a k sekundární databází. Pokud budete potřebovat k přesunutí primární zpět do původní oblasti, můžete spustit plánované převzetí služeb při selhání ručně (navrácení služeb po obnovení).

### <a name="perform-a-geo-restore"></a>Provedení geografického obnovení

Pokud používáte automatizovaných záloh s geograficky redundantní úložiště (standardně povoleno), můžete obnovit pomocí databáze [geografické obnovení](sql-database-disaster-recovery.md#recover-using-geo-restore). Obnovení obvykle dojde během 12 hodin – se ztrátou dat až jednu hodinu v závislosti při poslední záloha protokolu byla přijata a replikovat. Dokud se obnovení nedokončí, databáze není schopná zaznamenávat žádné transakce ani reagovat na dotazy. Mějte na paměti, geografické obnovení pouze obnoví databázi k poslední dostupný bod v čase.

> [!NOTE]
> Pokud datové centrum vrátí do režimu online předtím, než přepnete aplikace k obnovené databázi, můžete zrušit obnovení.

### <a name="perform-post-failover--recovery-tasks"></a>Provedení úloh po převzetí služeb při selhání nebo obnovení

Po obnovení s použitím libovolného mechanismu musíte provést následující dodatečné úlohy, abyste pro uživatele zprovoznili své aplikace:

- Přesměrujte klienty a klientské aplikace na nový server a obnovenou databázi.
- Zajistěte příslušná pravidla brány firewall na úrovni serveru IP jsou nastavené pro uživatele, aby připojení nebo použít [brány firewall na úrovni databáze](sql-database-firewall-configure.md#manage-ip-firewall-rules-using-the-azure-portal) umožňující příslušných pravidel.
- Ujistěte se, že se používají odpovídající přihlášení a oprávnění na úrovni hlavní databáze (nebo použijte [obsažené uživatelé](https://docs.microsoft.com/sql/relational-databases/security/contained-database-users-making-your-database-portable))
- Podle potřeby nakonfigurujte auditování.
- Podle potřeby nakonfigurujte výstrahy.

> [!NOTE]
> Pokud používáte skupiny převzetí služeb při selhání a připojit se k nim pomocí lstener čtení i zápis, přesměrování po převzetí služeb při selhání aplikace se stane automaticky a transparentně.

## <a name="upgrade-an-application-with-minimal-downtime"></a>Upgrade aplikace s minimálními výpadky

Někdy aplikace musí být převedeno do režimu offline kvůli plánované údržbě, jako je upgrade aplikace. [Správa upgradů aplikací](sql-database-manage-application-rolling-upgrade.md) popisuje, jak pomocí aktivní geografické replikace povolit postupné upgrady cloudových aplikací, abyste minimalizovali prostoje během upgradu a zadejte cestu k obnovení, pokud se něco nepovede.

## <a name="next-steps"></a>Další postup

Diskuzi o aspektech návrhu aplikací pro samostatné databáze a pro elastické fondy najdete v tématu [návrh aplikace pro zotavení po havárii cloudu](sql-database-designing-cloud-solutions-for-disaster-recovery.md) a [strategie zotavení po havárii Elastických fondů](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).
