---
title: Provozní kontinuita v cloudu – obnovení databází – SQL Database | Dokumentace Microsoftu
description: Zjistěte, jak Azure SQL Database podporuje provozní kontinuitu v cloudu a obnovení databází a jak pomáhá udržovat klíčové cloudové aplikace v chodu.
keywords: provozní kontinuita, provozní kontinuita v cloudu, zotavení databáze po havárii, obnovení databáze
services: sql-database
author: anosov1960
manager: craigg
ms.service: sql-database
ms.custom: business continuity
ms.topic: conceptual
ms.workload: On Demand
ms.date: 07/16/2018
ms.author: sashan
ms.reviewer: carlrab
ms.openlocfilehash: dfea1587cddbf7440771ca7007928f7e4054f61a
ms.sourcegitcommit: e32ea47d9d8158747eaf8fee6ebdd238d3ba01f7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/17/2018
ms.locfileid: "39092286"
---
# <a name="overview-of-business-continuity-with-azure-sql-database"></a>Přehled provozní kontinuity se službou Azure SQL Database

Tento přehled popisuje možnosti, které služba Azure SQL Database nabízí pro zajištění provozní kontinuity a zotavení po havárii. Další informace o možnosti, doporučení a kurzy pro zotavení z ničivých událostí, které by mohly způsobit ztrátu dat nebo nedostupnost databáze a aplikace přestanou být dostupné. Zjistěte, co můžete dělat, když chyba uživatele nebo aplikace ovlivňuje integritu dat, má k výpadku oblasti Azure nebo aplikace vyžaduje údržbu.

## <a name="sql-database-features-that-you-can-use-to-provide-business-continuity"></a>Funkce služby SQL Database, pomocí kterých můžete zajistit provozní kontinuitu

SQL Database poskytuje řadu funkcí provozní kontinuity, včetně automatizovaných záloh a volitelné replikace databáze. Každá má jiné vlastnosti ohledně odhadovaného času obnovení (ERT) a potenciální ztráty dat posledních transakcí. Jakmile tyto možnosti pochopíte, můžete si mezi nimi vybírat a ve většině scénářů je spolu kombinovat a používat pro různé scénáře. Při vývoji plánu provozní kontinuity musíte pochopit maximální přijatelnou dobu úplného obnovení aplikace po ničivé události – to je vaše plánovaná doba obnovení (RTO). Také musíte pochopit maximální objem dat poslední aktualizace (časový interval) aplikace může tolerovat možnost, ztráty při obnovení po ničivé události – to je váš cíl bodu obnovení (RPO).

Následující tabulka porovnává ERT a RPO pro každou vrstvu služby pro tři nejběžnější scénáře.

| Schopnost | Basic | Standard | Premium  | Obecné použití | Pro důležité obchodní informace
| --- | --- | --- | --- |--- |--- |
| Obnovení k určitému bodu v čase ze zálohy |Libovolný bod obnovení do 7 dní |Libovolný bod obnovení do 35 dní |Libovolný bod obnovení do 35 dní |Libovolný bod obnovení v rámci nakonfigurované doby (až po 35 dnů)|Libovolný bod obnovení v rámci nakonfigurované doby (až po 35 dnů)|
| Geografické obnovení z geograficky replikovaných záloh |ERT < 12 h, RPO < 1 h |ERT < 12 h, RPO < 1 h |ERT < 12 h, RPO < 1 h |ERT < 12 h, RPO < 1 h|ERT < 12 h, RPO < 1 h|
| Obnovení z úložiště záloh Azure Backup Vault |ERT < 12 h, RPO < 1 týden |ERT < 12 h, RPO < 1 týden |ERT < 12 h, RPO < 1 týden |ERT < 12 h, RPO < 1 týden|ERT < 12 h, RPO < 1 týden|
| Aktivní geografická replikace |ERT < 30 s, RPO < 5 s |ERT < 30 s, RPO < 5 s |ERT < 30 s, RPO < 5 s |ERT < 30 s, RPO < 5 s|ERT < 30 s, RPO < 5 s|

### <a name="use-point-in-time-restore-to-recover-a-database"></a>Obnovení databáze pomocí obnovení k určitému bodu v čase

SQL Database automaticky provádí kombinaci zálohování úplné databáze každý týden, rozdílovými zálohami prováděnými každou hodinu a transakce zálohu protokolu každých 5 - 10 minut chránit vaši firmu před ztrátou dat. Pokud používáte [nákupní model založený na DTU](sql-database-service-tiers-dtu.md), pak tyto zálohy jsou uložené v úložišti RA-GRS po dobu 35 dní pro databáze v úrovních služby Standard a Premium a 7 dní pro databáze na úrovni služby Basic. Pokud doba uchovávání vaší úrovně služby nevyhovuje požadavkům vaší organizace, můžete dobu uchovávání prodloužit [změnou úrovně služby](sql-database-single-database-scale.md). Pokud používáte [nákupní model založený na virtuálních jádrech](sql-database-service-tiers-vcore.md), uchovávání záloh probíhá možností konfigurace až na 35 dní pro obecné účely a kritické obchodní vrstvy. Pro zajištění ochrany před výpadkem datového centra se úplné a rozdílové zálohy databáze také replikují do [spárovaného datového centra](../best-practices-availability-paired-regions.md). Další informace najdete v tématu [automatické zálohování databází](sql-database-automated-backups.md).

Pokud maximální podporované v daném okamžiku obnovit (PITR) doba uchovávání není pro vaši aplikaci dostatečná, můžete ji rozšířit konfigurací zásad dlouhodobého uchovávání (LTR) pro databáze. Další informace najdete v tématu [automatizovaných záloh](sql-database-automated-backups.md) a [dlouhodobého uchovávání záloh](sql-database-long-term-retention.md).

Tyto automatické zálohy databáze můžete použít k obnovení databáze po různých ničivých událostech, a to jak v rámci vašeho datového centra, tak do jiného datového centra. Při použití automatických záloh databáze závisí odhadovaný čas obnovení na několika faktorech. Patří mezi ně celkový počet obnovovaných databází ve stejném regionu a ve stejnou dobu, velikost databáze, velikost protokolu transakcí a šířka pásma sítě. Čas obnovení je obvykle méně než 12 hodin. Může trvat delší dobu velmi velký nebo aktivní databázi obnovit. Další podrobnosti o čase obnovení najdete v tématu [databáze čas obnovení](sql-database-recovery-using-backups.md#recovery-time). Při obnovování do jiné oblasti dat je potenciální ztráta dat omezena na 1 hodinu díky geograficky redundantnímu úložišti s rozdílovými zálohami prováděnými každou hodinu.

> [!IMPORTANT]
> Pokud chcete provést obnovení pomocí automatizovaného zálohování, musíte být členem role Přispěvatel SQL Serveru nebo vlastník předplatného – viz [RBAC: Předdefinované role](../role-based-access-control/built-in-roles.md). Obnovení můžete provést pomocí webu Azure Portal, prostředí PowerShell nebo rozhraní REST API. Nelze použít jazyk Transact-SQL.
>

Automatizované zálohování použijte pro zajištění provozní kontinuity a jako mechanismus obnovení, pokud vaše aplikace:

* Není považována za klíčovou.
* Nemá zavazující smlouvu SLA – výpadek 24 hodin nebo již nemá za následek finanční závazky.
* Pracuje s nízkou mírou změn dat (málo transakcí za hodinu) a ztráta změn provedených během až jedné hodiny je přijatelnou ztrátou dat.
* Je citlivá na změny nákladů.

Pokud potřebujete rychlejší obnovení, použijte [aktivní geografickou replikaci](sql-database-geo-replication-overview.md) (věnujeme se jí). Pokud potřebujete mít možnost obnovit data z období staršího než 35 dní, použijte [dlouhodobé uchovávání](sql-database-long-term-retention.md). 

### <a name="use-active-geo-replication-and-auto-failover-groups-to-reduce-recovery-time-and-limit-data-loss-associated-with-a-recovery"></a>Zkrácení času obnovení a omezení ztráty dat spojené s obnovením pomocí aktivní geografické replikace a Automatická – převzetí služeb při selhání skupiny

Kromě používání záloh databáze k obnovení, pokud dojde k narušení činnosti firmy, můžete použít [aktivní geografickou replikaci](sql-database-geo-replication-overview.md) nakonfigurovat databázi, aby měla až čtyři čitelné sekundární databáze v oblastech podle vašeho výběru. Tyto sekundární databáze jsou synchronizovány s primární databází pomocí mechanismu asynchronní replikace. Tato funkce slouží k ochraně před narušením provozu, pokud dojde k výpadku datového centra nebo během upgradu aplikace. Aktivní geografickou replikaci můžete také použít k zajištění lepšího výkonu dotazů jen pro čtení pro uživatele rozptýlené.

Povolit automatické a transparentní převzetí služeb při selhání geograficky replikované databáze by měl uspořádat do skupin pomocí [-automatické převzetí služeb při selhání skupiny](sql-database-geo-replication-overview.md) funkce služby SQL Database.

Pokud primární databáze neočekávaně přejde do režimu offline nebo je potřeba vyzkoušejte si ho do offline režimu údržby, můžete rychle povýšit sekundární primární (také nazývané převzetí služeb při selhání) a konfigurace aplikací pro připojení k primární zvýšenou úrovní. Pokud vaše aplikace připojuje k databázím pomocí naslouchacího procesu skupiny převzetí služeb při selhání, není nutné měnit konfiguraci řetězec připojení SQL po převzetí služeb při selhání. U plánovaného převzetí služeb při selhání nedojde ke ztrátě dat. V případě neplánovaného převzetí služeb při selhání může kvůli povaze asynchronní replikace dojít ke ztrátě menšího množství dat u posledních transakcí. Pomocí skupin automatického – převzetí služeb při selhání, můžete upravit zásady převzetí služeb při selhání minimalizovat potenciální ztráty dat. Po převzetí služeb při selhání můžete později provést navrácení služeb po obnovení – buď podle plánu, nebo když se datové centrum vrátí do režimu online. Ve všech případech se uživatelé setkají s krátkým výpadkem a budou se muset znovu připojit.

> [!IMPORTANT]
> Pokud chcete použít aktivní geografickou replikaci a skupiny – automatické převzetí služeb při selhání, musí být vlastníkem předplatného nebo mít oprávnění správce v systému SQL Server. Můžete nakonfigurovat a převzetí služeb při selhání pomocí Azure portal, Powershellu nebo rozhraní REST API použití oprávnění pro předplatné Azure nebo pomocí příkazů jazyka Transact-SQL s oprávněními systému SQL Server.
> 

Aktivní geografická replikace a Automatická – převzetí služeb při selhání skupiny použijte, pokud vaše aplikace splňuje některá z těchto kritérií:

* Je zvlášť důležitá.
* Má smlouvu SLA, která nepovoluje výpadek delší než 24 hodin.
* Výpadek může mít za následek finanční závazky.
* Pracuje s vysokou mírou změn dat a ztráta dat za jednu hodinu je nepřijatelná.
* Další náklady na aktivní geografickou replikaci jsou nižší než potenciální finanční závazky a související ztráta podnikání.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-protecting-important-DBs-from-regional-disasters-is-easy/player]
>

## <a name="recover-a-database-after-a-user-or-application-error"></a>Obnovení databáze po chybě uživatele nebo aplikace

Nikdo není dokonalý! Uživatel může omylem odstranit některá data, nedopatřením smazat důležitou tabulku nebo dokonce smazat celou databázi. Nebo může aplikace kvůli vadě náhodou přepsat dobrá data chybnými daty.

V takovém scénáři máte následující možnosti obnovení.

### <a name="perform-a-point-in-time-restore"></a>Obnovení k určitému bodu v čase
Pomocí automatizovaného zálohování můžete obnovit kopii databáze do známého bodu v čase, kdy byla v pořádku, za předpokladu, že čas spadá do doby uchovávání databáze. Po obnovení databáze můžete původní databázi nahradit obnovenou databází nebo zkopírovat potřebná data z obnovené databáze do původní databáze. Pokud databáze používá aktivní geografickou replikaci, doporučujeme zkopírovat požadovaná data z obnovené kopie do původní databáze. Pokud je původní databázi nahradit obnovenou databází, musíte překonfigurovat a znovu synchronizovat aktivní geografickou replikaci (která může u velkých databází poměrně nějakou dobu trvat). Při obnovení databáze na poslední dostupný bod v čase, obnovení geo-secondary do libovolného bodu v čase se momentálně nepodporuje.

Další informace a podrobné pokyny k obnovení databáze do určitého bodu v čase pomocí webu Azure Portal nebo prostředí PowerShell najdete v části [Obnovení k určitému bodu v čase](sql-database-recovery-using-backups.md#point-in-time-restore). Obnovení nelze provést pomocí jazyka Transact-SQL.

### <a name="restore-a-deleted-database"></a>Obnovení odstraněné databáze
Pokud se odstraní databáze, ale neodstraní se logický server, můžete odstraněnou databázi obnovit do bodu, kdy byla odstraněna. Tak se záloha databáze obnoví do stejného logického serveru SQL, ze kterého byla odstraněna. Můžete ji obnovit s použitím původního názvu nebo pro obnovenou databázi zadat nový název.

Další informace a podrobné pokyny k obnovení odstraněné databáze pomocí webu Azure Portal nebo prostředí PowerShell najdete v části [Obnovení odstraněné databáze](sql-database-recovery-using-backups.md#deleted-database-restore). Obnovení nelze provést pomocí jazyka Transact-SQL.

> [!IMPORTANT]
> Pokud byl odstraněn logický server, není možné obnovit odstraněnou databázi.


### <a name="restore-backups-from-long-term-retention"></a>Obnovení z dlouhodobě uchovávaných záloh

Pokud je databáze nakonfigurovaná pro dlouhodobé uchovávání došlo ke ztrátě dat mimo aktuální dobu uchovávání automatizovaných záloh, můžete do nové databáze obnovit z úplné zálohy v úložišti zleva doprava. V tuto chvíli můžete původní databázi nahradit obnovenou databází nebo zkopírovat potřebná data z obnovené databáze do původní databáze. Pokud potřebujete načíst starší verzi databáze před upgradem hlavní aplikace, by vyhovovaly žádosti o auditorů nebo soudnímu, že můžete vytvořit databázi pomocí úplné zálohy uložené v trezoru záloh Azure.  Další informace najdete v tématu [Dlouhodobé uchovávání](sql-database-long-term-retention.md).

## <a name="recover-a-database-to-another-region-from-an-azure-regional-data-center-outage"></a>Obnovení databáze do jiné oblasti po výpadku regionálního datového centra Azure
<!-- Explain this scenario -->

Přestože je taková situace výjimečná, i u datového centra Azure může dojít k výpadku. Při výpadku dojde k narušení provozu, které může trvat jen několik minut nebo až několik hodin.

* Jednou z možností je počkat, až výpadek skončí a databáze se vrátí do režimu online. Tento postup funguje pro aplikace, které si mohou dovolit mít databázi v režimu offline. Například vývojový projekt nebo bezplatná zkušební verze, na které nemusíte neustále pracovat. Pokud datové centrum má k výpadku, můžete není známo, jak dlouho může trvat výpadek, proto tato možnost funguje jenom v případě nepotřebujete databázi nějakou dobu.
* Další možností je buď selhání přes do jiné oblasti dat Pokud používáte aktivní geografickou replikaci, nebo obnovení databáze pomocí geograficky redundantních záloh databáze (geografické obnovení). Převzetí služeb při selhání trvá jenom pár sekund při obnovení databáze ze záloh trvá hodiny.

Když přijmete opatření, jak dlouho trvá, vám umožní obnovit a množství ztracených dat v závisí na tom, jak se rozhodnete pomocí těchto funkcí provozní kontinuity ve vaší aplikaci. Ve skutečnosti můžete rozhodnout pro použití kombinace záloh databáze a aktivní geografickou replikaci v závislosti na požadavcích aplikace. Diskuzi o aspektech návrhu aplikací pro samostatné databáze a pro elastické fondy pomocí těchto funkcí provozní kontinuity najdete v tématech [Návrh aplikace pro zotavení po havárii cloudu](sql-database-designing-cloud-solutions-for-disaster-recovery.md) a [Strategie zotavení po havárii elastických fondů](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).

Následující části poskytují přehled postupů k obnovení pomocí záloh databáze nebo aktivní geografické replikace. Podrobné pokyny, včetně plánování požadavky, postupů po obnovení a informace o simulaci výpadku provedení postupu zotavení po havárii, najdete v článku [obnovení služby SQL Database po výpadku](sql-database-disaster-recovery.md).

### <a name="prepare-for-an-outage"></a>Příprava na výpadek
Bez ohledu na funkce provozní kontinuity, které používáte, musíte:

* Určit a připravit cílový server, včetně pravidel brány firewall na úrovni serveru, přihlášení a oprávnění na úrovni hlavní databáze
* Určit, jak přesměrovat klienty a klientské aplikace na nový server
* Zdokumentovat další závislosti, například nastavení auditování a výstrahy

Pokud jste není nepřipravíte, může převod aplikací online po převzetí služeb při selhání nebo obnovení databáze bude vyžadovat čas navíc a pravděpodobně také vyžadovat řešení potíží ve vypjaté situaci - dobrá kombinace.

### <a name="fail-over-to-a-geo-replicated-secondary-database"></a>Převzetí služeb při selhání do geograficky replikované sekundární databáze
Pokud používáte jako mechanismus obnovení aktivní geografickou replikaci a skupiny – automatické převzetí služeb při selhání, můžete nakonfigurovat zásadu automatického převzetí služeb při selhání nebo použít [ruční převzetí služeb při selhání](sql-database-disaster-recovery.md#fail-over-to-geo-replicated-secondary-server-in-the-failover-group). Po zahájení převzetí služeb způsobí, že sekundární k nové primární a připravená zaznamenávat nové transakce a reagovat na dotazy – minimální ztráty dat ještě nebyl replikován. Informace o návrhu procesu převzetí služeb při selhání najdete v tématu [návrh aplikace pro zotavení po havárii cloudu](sql-database-designing-cloud-solutions-for-disaster-recovery.md).

> [!NOTE]
> Pokud datové centrum vrátí do režimu online staré primárek automaticky znovu připojit k nové primární a k sekundární databází. Pokud budete potřebovat k přesunutí primární zpět do původní oblasti, můžete spustit plánované převzetí služeb při selhání ručně (navrácení služeb po obnovení). 
> 

### <a name="perform-a-geo-restore"></a>Provedení geografického obnovení
Pokud používáte automatizované zálohování s replikací geograficky redundantního úložiště jako mechanismus obnovení, [zahajte obnovení databáze pomocí geografického obnovení](sql-database-disaster-recovery.md#recover-using-geo-restore). K obnovení obvykle dojde během 12 hodin – se ztrátou dat až za jednu hodinu v závislosti na času pořízení a replikace poslední hodinové rozdílové zálohy. Dokud se obnovení nedokončí, databáze není schopná zaznamenávat žádné transakce ani reagovat na dotazy. Při obnovení databáze na poslední dostupný bod v čase, obnovení geo-secondary do libovolného bodu v čase se momentálně nepodporuje.

> [!NOTE]
> Pokud datové centrum vrátí do režimu online předtím, než přepnete aplikace k obnovené databázi, můžete zrušit obnovení.  
>
>

### <a name="perform-post-failover--recovery-tasks"></a>Provedení úloh po převzetí služeb při selhání nebo obnovení
Po obnovení s použitím libovolného mechanismu musíte provést následující dodatečné úlohy, abyste pro uživatele zprovoznili své aplikace:

* Přesměrujte klienty a klientské aplikace na nový server a obnovenou databázi.
* Ujistěte se, že platí odpovídající pravidla brány firewall na úrovni serveru, aby se uživatelé mohli připojit (nebo použijte [brány firewall na úrovni databáze](sql-database-firewall-configure.md#creating-and-managing-firewall-rules)).
* Ujistěte se, že se používají odpovídající přihlášení a oprávnění na úrovni hlavní databáze (nebo použijte [obsažené uživatelé](https://msdn.microsoft.com/library/ff929188.aspx))
* Podle potřeby nakonfigurujte auditování.
* Podle potřeby nakonfigurujte výstrahy.

## <a name="upgrade-an-application-with-minimal-downtime"></a>Upgrade aplikace s minimálními výpadky
Někdy aplikace musí být převedeno do režimu offline kvůli plánované údržbě, jako je upgrade aplikace. [Správa upgradů aplikací](sql-database-manage-application-rolling-upgrade.md) popisuje, jak pomocí aktivní geografické replikace povolit postupné upgrady cloudových aplikací, abyste minimalizovali prostoje během upgradu a zadejte cestu k obnovení, pokud se něco nepovede. 

## <a name="next-steps"></a>Další postup
Diskuzi o aspektech návrhu aplikací pro samostatné databáze a pro elastické fondy najdete v tématech [Návrh aplikace pro zotavení po havárii cloudu](sql-database-designing-cloud-solutions-for-disaster-recovery.md) a [Strategie zotavení po havárii elastických fondů](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).
