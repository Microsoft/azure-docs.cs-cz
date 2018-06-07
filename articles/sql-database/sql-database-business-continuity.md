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
ms.date: 04/04/2018
ms.author: sashan
ms.reviewer: carlrab
ms.openlocfilehash: 0399b9037e162aa712b87b498b968750226af23a
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34646384"
---
# <a name="overview-of-business-continuity-with-azure-sql-database"></a>Přehled provozní kontinuity se službou Azure SQL Database

Tento přehled popisuje možnosti, které služba Azure SQL Database nabízí pro zajištění provozní kontinuity a zotavení po havárii. Další informace o možnostech, doporučení a kurzy pro obnovení z rušivý událostí, které může dojít ke ztrátě dat nebo způsobit, že databáze a aplikace k dispozici. Zjistěte, co dělat, pokud uživatele nebo aplikace chyba ovlivňuje integritu dat, oblast Azure má výpadek nebo pokud vaše aplikace vyžaduje údržby.

## <a name="sql-database-features-that-you-can-use-to-provide-business-continuity"></a>Funkce služby SQL Database, pomocí kterých můžete zajistit provozní kontinuitu

SQL Database poskytuje řadu funkcí provozní kontinuity, včetně automatizovaných záloh a volitelné replikace databáze. Každá má jiné vlastnosti ohledně odhadovaného času obnovení (ERT) a potenciální ztráty dat posledních transakcí. Jakmile tyto možnosti pochopíte, můžete si mezi nimi vybírat a ve většině scénářů je spolu kombinovat a používat pro různé scénáře. Při vývoji plánu provozní kontinuity musíte pochopit maximální přijatelnou dobu úplného obnovení aplikace po ničivé události – to je vaše plánovaná doba obnovení (RTO). Také musíte porozumět maximální objem dat poslední aktualizace (časový interval) aplikace může tolerovat ztráty při obnovování po nepříjemným událostem – to je cíl bodu obnovení (RPO).

Následující tabulka porovnává vložit a plánovaný bod obnovení pro jednotlivé úrovně služby pro tři nejběžnější scénáře.

| Schopnost | Basic | Standard | Premium  | Obecné použití | Pro důležité obchodní informace
| --- | --- | --- | --- |--- |--- |
| Obnovení k určitému bodu v čase ze zálohy |Libovolný bod obnovení do 7 dní |Libovolný bod obnovení do 35 dní |Libovolný bod obnovení do 35 dní |Všechny body obnovení v rámci nastaveném časovém intervalu (až 35 dnů)|Všechny body obnovení v rámci nastaveném časovém intervalu (až 35 dnů)|
| Geografické obnovení ze zálohy geograficky replikované |ERT < 12 h, RPO < 1 h |ERT < 12 h, RPO < 1 h |ERT < 12 h, RPO < 1 h |ERT < 12 h, RPO < 1 h|ERT < 12 h, RPO < 1 h|
| Obnovení z úložiště záloh Azure Backup Vault |ERT < 12 h, RPO < 1 týden |ERT < 12 h, RPO < 1 týden |ERT < 12 h, RPO < 1 týden |ERT < 12 h, RPO < 1 týden|ERT < 12 h, RPO < 1 týden|
| Aktivní geografickou replikaci |ERT < 30 s, RPO < 5 s |ERT < 30 s, RPO < 5 s |ERT < 30 s, RPO < 5 s |ERT < 30 s, RPO < 5 s|ERT < 30 s, RPO < 5 s|

### <a name="use-point-in-time-restore-to-recover-a-database"></a>Použít obnovení bodu v čase k obnovení databáze

SQL Database automaticky provede kombinaci databáze úplné zálohování každý týden, databáze rozdílové zálohy každou hodinu a transakce protokolu zálohování každých pět - deset minut chránit vaši firmu před ztrátou dat. Pokud používáte [na základě DTU nákupní model](sql-database-service-tiers-dtu.md), pak tyto zálohy jsou uloženy v úložišti RA-GRS 35 dní pro databáze v úrovních služeb Standard a Premium a 7 dní pro databáze ve vrstvě služby na úrovni Basic. Pokud doba uchovávání vaší úrovně služby nevyhovuje požadavkům vaší organizace, můžete dobu uchovávání prodloužit [změnou úrovně služby](sql-database-service-tiers-dtu.md#choosing-a-service-tier-in-the-dtu-based-purchasing-model). Pokud používáte [nákupní model (preview) na základě vCore](sql-database-service-tiers-vcore.md), uchovávání záloh je konfigurovatelné až na 35 dnů v obecné účely a kritické podnikové úrovně. Pro zajištění ochrany před výpadkem datového centra se úplné a rozdílové zálohy databáze také replikují do [spárovaného datového centra](../best-practices-availability-paired-regions.md). Další informace najdete v tématu [automatické zálohování databází](sql-database-automated-backups.md).

Pokud maximální doba uchování podporované možnosti PITR není dostatečná pro aplikaci, můžete ji rozšířit tak, že nakonfigurujete zásady dlouhodobé uchovávání informací (zleva doprava) pro databáze. Další informace najdete v tématu [Dlouhodobé uchovávání](sql-database-long-term-retention.md).

Tyto automatické zálohy databáze můžete použít k obnovení databáze po různých ničivých událostech, a to jak v rámci vašeho datového centra, tak do jiného datového centra. Při použití automatických záloh databáze závisí odhadovaný čas obnovení na několika faktorech. Patří mezi ně celkový počet obnovovaných databází ve stejném regionu a ve stejnou dobu, velikost databáze, velikost protokolu transakcí a šířka pásma sítě. Doba obnovení je obvykle menší než 12 hodin. Při obnovování do jiné oblasti dat je potenciální ztráta dat omezena na 1 hodinu díky geograficky redundantnímu úložišti s rozdílovými zálohami prováděnými každou hodinu.

> [!IMPORTANT]
> Pokud chcete provést obnovení pomocí automatizovaného zálohování, musíte být členem role Přispěvatel SQL Serveru nebo vlastník předplatného – viz [RBAC: Předdefinované role](../role-based-access-control/built-in-roles.md). Obnovení můžete provést pomocí webu Azure Portal, prostředí PowerShell nebo rozhraní REST API. Nelze použít jazyk Transact-SQL.
>

Automatizované zálohování použijte pro zajištění provozní kontinuity a jako mechanismus obnovení, pokud vaše aplikace:

* Není považována za klíčovou.
* Nemá vazbu SLA - výpadek 24 hodin nebo již nemá za následek finanční odpovědnosti.
* Pracuje s nízkou mírou změn dat (málo transakcí za hodinu) a ztráta změn provedených během až jedné hodiny je přijatelnou ztrátou dat.
* Je citlivá na změny nákladů.

Pokud potřebujete rychlejší obnovení, použijte [aktivní geografickou replikací](sql-database-geo-replication-overview.md) (popsané dále). Pokud potřebujete mít možnost obnovit data z období starší než 35 dní, použijte [dlouhodobé uchovávání](sql-database-long-term-retention.md). 

### <a name="use-active-geo-replication-and-auto-failover-groups-in-preview-to-reduce-recovery-time-and-limit-data-loss-associated-with-a-recovery"></a>Pomocí aktivní geografickou replikaci a automatické převzetí služeb při selhání skupiny (v preview) můžete zkrátit čas obnovení a omezit ztrátě dat, které jsou přidružené k obnovení

Kromě použití zálohy databáze pro obnovení databáze, pokud dojde k přerušení obchodní, můžete použít [aktivní geografickou replikací](sql-database-geo-replication-overview.md) nakonfigurovat databázi mít až čtyři čitelný sekundární databáze v oblastech podle svého výběru. Tyto sekundární databáze jsou synchronizovány s primární databází pomocí mechanismu asynchronní replikace. Tato funkce slouží k ochraně proti přerušení obchodní, pokud dojde k výpadku datacentra nebo během upgradu aplikace. Aktivní geografickou replikaci lze také geograficky rozmístěné uživatelům poskytovat lepší výkon dotazů pro dotazy jen pro čtení.

Pokud chcete povolit automatické a transparentní převzetí služeb při selhání by měl uspořádání geograficky replikované databáze do skupiny pomocí [-automatické převzetí služeb při selhání skupiny](sql-database-geo-replication-overview.md) funkce SQL Database (-preview).

Pokud primární databáze neočekávaně přejde do režimu offline nebo je potřeba vzít do offline režimu pro údržbu aktivity, můžete rychle zvýšit úroveň sekundárního stane primárním serverem (také nazývané převzetí služeb při selhání) a konfigurace aplikací pro připojení k propagovaných primární. Pokud vaše aplikace se připojuje k databázím pomocí naslouchacího procesu skupiny převzetí služeb při selhání, nemusíte měnit konfiguraci řetězce připojení SQL po převzetí služeb při selhání. U plánovaného převzetí služeb při selhání nedojde ke ztrátě dat. V případě neplánovaného převzetí služeb při selhání může kvůli povaze asynchronní replikace dojít ke ztrátě menšího množství dat u posledních transakcí. Použití skupin automatické převzetí služeb při selhání (v preview), můžete upravit zásady převzetí služeb při selhání tak, aby potenciální ztráty dat co nejmenší. Po převzetí služeb při selhání můžete později provést navrácení služeb po obnovení – buď podle plánu, nebo když se datové centrum vrátí do režimu online. Ve všech případech se uživatelé setkají s krátkým výpadkem a budou se muset znovu připojit.

> [!IMPORTANT]
> Používání aktivní geografické replikace a skupiny automatické převzetí služeb při selhání (v preview), musíte být vlastníkem předplatného nebo mít oprávnění správce v systému SQL Server. Můžete nakonfigurovat a převzít služby Azure pomocí portálu, prostředí PowerShell nebo rozhraní REST API pomocí oprávnění předplatného Azure nebo pomocí jazyka Transact-SQL s oprávněními systému SQL Server.
> 

Použijte active geografická replikace a automatické převzetí služeb při selhání skupiny (ve verzi preview), pokud vaše aplikace splňuje některé z těchto kritérií:

* Je zvlášť důležitá.
* Má smlouvu SLA, která nepovoluje výpadek delší než 24 hodin.
* Výpadek může mít za následek finanční odpovědnosti.
* Pracuje s vysokou mírou změn dat a ztráta dat za jednu hodinu je nepřijatelná.
* Další náklady na aktivní geografickou replikaci jsou nižší než potenciální finanční závazky a související ztráta podnikání.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-protecting-important-DBs-from-regional-disasters-is-easy/player]
>

## <a name="recover-a-database-after-a-user-or-application-error"></a>Obnovení databáze po chybě uživatele nebo aplikace

Žádná je ideální! Uživatel může omylem odstranit některá data, nedopatřením smazat důležitou tabulku nebo dokonce smazat celou databázi. Nebo může aplikace kvůli vadě náhodou přepsat dobrá data chybnými daty.

V takovém scénáři máte následující možnosti obnovení.

### <a name="perform-a-point-in-time-restore"></a>Obnovení k určitému bodu v čase
Pomocí automatizovaného zálohování můžete obnovit kopii databáze do známého bodu v čase, kdy byla v pořádku, za předpokladu, že čas spadá do doby uchovávání databáze. Po obnovení databáze můžete původní databázi nahradit obnovenou databází nebo zkopírovat potřebná data z obnovené databáze do původní databáze. Pokud se databáze používá aktivní geografickou replikaci, doporučujeme kopírování požadovaná data z obnovené kopie do původní databáze. Pokud je původní databáze nahradit obnovenou databázi, budete muset překonfigurovat a poté opakujte synchronizaci aktivní geografickou replikaci (který může trvat poměrně nějakou dobu pro velké databáze). Při tím se obnoví databázi na poslední dostupný bod v čase, obnovení sekundární geograficky do libovolného bodu v čase není aktuálně podporován.

Další informace a podrobné pokyny k obnovení databáze do určitého bodu v čase pomocí webu Azure Portal nebo prostředí PowerShell najdete v části [Obnovení k určitému bodu v čase](sql-database-recovery-using-backups.md#point-in-time-restore). Obnovení nelze provést pomocí jazyka Transact-SQL.

### <a name="restore-a-deleted-database"></a>Obnovení odstraněné databáze
Pokud se odstraní databáze, ale neodstraní se logický server, můžete odstraněnou databázi obnovit do bodu, kdy byla odstraněna. Tak se záloha databáze obnoví do stejného logického serveru SQL, ze kterého byla odstraněna. Můžete ji obnovit s použitím původního názvu nebo pro obnovenou databázi zadat nový název.

Další informace a podrobné pokyny k obnovení odstraněné databáze pomocí webu Azure Portal nebo prostředí PowerShell najdete v části [Obnovení odstraněné databáze](sql-database-recovery-using-backups.md#deleted-database-restore). Obnovení nelze provést pomocí jazyka Transact-SQL.

> [!IMPORTANT]
> Pokud byl odstraněn logický server, není možné obnovit odstraněnou databázi.


### <a name="restore-backups-from-long-term-retention"></a>Obnovit zálohy z dlouhodobé uchovávání

Pokud došlo k chybě ztrátou dat mimo aktuální dobu uchování pro automatizované zálohování a je databáze nakonfigurovaná pro dlouhodobé uchovávání, můžete pro novou databázi obnovit z úplné zálohy v úložišti zleva doprava. V tuto chvíli můžete původní databázi nahradit obnovenou databází nebo zkopírovat potřebná data z obnovené databáze do původní databáze. Pokud budete potřebovat načíst starší verzi databáze před upgradem hlavní aplikace, splnit žádost od auditory nebo právní pořadí, že můžete vytvořit databázi pomocí úplné zálohy uložené v trezoru zálohování Azure.  Další informace najdete v tématu [Dlouhodobé uchovávání](sql-database-long-term-retention.md).

## <a name="recover-a-database-to-another-region-from-an-azure-regional-data-center-outage"></a>Obnovení databáze do jiné oblasti po výpadku regionálního datového centra Azure
<!-- Explain this scenario -->

Přestože je taková situace výjimečná, i u datového centra Azure může dojít k výpadku. Při výpadku dojde k narušení provozu, které může trvat jen několik minut nebo až několik hodin.

* Jednou z možností je počkat, až výpadek skončí a databáze se vrátí do režimu online. Tento postup funguje pro aplikace, které si mohou dovolit mít databázi v režimu offline. Například vývojový projekt nebo bezplatná zkušební verze, na které nemusíte neustále pracovat. Je-li v datovém centru k výpadku, si nejste jisti, jak dlouho může poslední se výpadek, tak tato možnost funguje jenom v případě nepotřebujete databáze nějakou dobu.
* Další možností je buď selhání přes na jiné datové oblasti používáte-li aktivní geografickou replikaci nebo obnovit databázi pomocí zálohy geograficky redundantní databáze (geografické obnovení). Převzetí služeb při selhání trvá jenom pár sekund, při obnovení databáze ze zálohy trvá hodin.

Po provedení akce, jak dlouho trvalo obnovit a kolik ztráty dat platit závisí na tom, jak se rozhodnete použít tyto funkce kontinuity obchodních v aplikaci. Ve skutečnosti můžete použít kombinaci zálohy databáze a aktivní geografickou replikací v závislosti na požadavcích vaší aplikace. Diskuzi o aspektech návrhu aplikací pro samostatné databáze a pro elastické fondy pomocí těchto funkcí provozní kontinuity najdete v tématech [Návrh aplikace pro zotavení po havárii cloudu](sql-database-designing-cloud-solutions-for-disaster-recovery.md) a [Strategie zotavení po havárii elastických fondů](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).

Následující části obsahují základní informace o postup obnovit pomocí zálohy databáze nebo aktivní geografickou replikací. Podrobné pokyny, včetně plánování požadavky, postup obnovení post a informace o tom, jak simulovat výpadku provádět postupu zotavení po havárii, najdete v části [obnovení databáze serveru SQL výpadku](sql-database-disaster-recovery.md).

### <a name="prepare-for-an-outage"></a>Příprava na výpadek
Bez ohledu na funkce provozní kontinuity, které používáte, musíte:

* Určit a připravit cílový server, včetně pravidel brány firewall na úrovni serveru, přihlášení a oprávnění na úrovni hlavní databáze
* Určit, jak přesměrovat klienty a klientské aplikace na nový server
* Zdokumentovat další závislosti, například nastavení auditování a výstrahy

Pokud není připravíte správně, přináší aplikace online po převzetí služeb při selhání nebo obnovení databáze bude vyžadovat čas navíc a pravděpodobně také vyžadují, řešení potíží s v době přízvuk - chybný kombinaci.

### <a name="fail-over-to-a-geo-replicated-secondary-database"></a>Převzetí služeb při selhání do geograficky replikované sekundární databáze
Pokud se používá aktivní geografickou replikaci a skupiny automatické převzetí služeb při selhání (v preview) jako váš mechanismus obnovení, můžete nakonfigurovat zásady pro automatické převzetí služeb při selhání nebo použít [ruční převzetí služeb při selhání](sql-database-disaster-recovery.md#fail-over-to-geo-replicated-secondary-server-in-the-failover-group). Po zahájení převzetí způsobí, že sekundární stane nový primární a připravené k záznamu nové transakce a reagovat na dotazy – s minimálními ztrátami dat dat, ještě nebyl replikován. Informace o navrhování proces převzetí služeb při selhání najdete v tématu [návrhu aplikace pro zotavení po havárii cloudu](sql-database-designing-cloud-solutions-for-disaster-recovery.md).

> [!NOTE]
> Pokud datové centrum přejde do režimu online původní základní barvy automaticky znovu připojit k nové primární a sekundární databáze je k. Pokud potřebujete přemístit primární zpět na původní oblast, můžete spustit plánované převzetí služeb při selhání ručně (navrácení služeb po obnovení). 
> 

### <a name="perform-a-geo-restore"></a>Provedení geografické obnovení
Pokud používáte automatizované zálohování s replikací geograficky redundantní úložiště jako váš mechanismus obnovení [zahájení obnovení databáze pomocí geografické obnovení](sql-database-disaster-recovery.md#recover-using-geo-restore). K obnovení obvykle dojde během 12 hodin – se ztrátou dat až za jednu hodinu v závislosti na času pořízení a replikace poslední hodinové rozdílové zálohy. Dokud se obnovení nedokončí, databáze není schopná zaznamenávat žádné transakce ani reagovat na dotazy. Při tím se obnoví databázi na poslední dostupný bod v čase, obnovení sekundární geograficky do libovolného bodu v čase není aktuálně podporován.

> [!NOTE]
> Pokud datové centrum přejde do režimu online, před přepnutím aplikace do obnovené databáze, můžete zrušit obnovení.  
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
Někdy aplikace musí být převedeno do režimu offline z důvodu plánované údržby, jako je například upgradu aplikace. [Spravovat aplikace upgrady](sql-database-manage-application-rolling-upgrade.md) popisuje, jak pomocí aktivní geografickou replikaci můžete povolit postupné upgrady vaší cloudové aplikace minimalizovat prostoje při upgradech a zadejte cestu obnovení, pokud dojde k chybě. 

## <a name="next-steps"></a>Další postup
Diskuzi o aspektech návrhu aplikací pro samostatné databáze a pro elastické fondy najdete v tématech [Návrh aplikace pro zotavení po havárii cloudu](sql-database-designing-cloud-solutions-for-disaster-recovery.md) a [Strategie zotavení po havárii elastických fondů](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).
