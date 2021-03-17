---
title: Řešení potíží se synchronizací dat SQL
description: Naučte se, jak identifikovat, řešit potíže a řešit běžné problémy s Synchronizace dat SQL v Azure.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: data sync, sqldbrb=1
ms.devlang: ''
ms.topic: troubleshooting
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/20/2018
ms.openlocfilehash: 02eaec4c86c934e8d2638de1b60aa9267babf7a8
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/28/2020
ms.locfileid: "92790164"
---
# <a name="troubleshoot-issues-with-sql-data-sync"></a>Řešení potíží s funkcí Synchronizace dat SQL
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Tento článek popisuje, jak řešit známé problémy s Synchronizace dat SQL v Azure. Pokud dojde k vyřešení problému, je zde k dispozici.

Přehled Synchronizace dat SQL najdete v tématu [synchronizace dat napříč několika cloudy a místními databázemi pomocí synchronizace dat SQL v Azure](sql-data-sync-data-sql-server-sql-database.md).

> [!IMPORTANT]
> Synchronizace dat SQL v tuto **chvíli nepodporuje spravovanou** instanci SQL Azure.

## <a name="sync-issues"></a>Problémy synchronizace

- [Synchronizace v uživatelském rozhraní portálu pro místní databáze přidružené k agentovi klienta se nezdařila.](#sync-fails)

- [Moje skupina synchronizace je zablokovaná ve stavu zpracování.](#sync-stuck)

- [Zobrazuje se v tabulkách chybná data](#sync-baddata)

- [Po úspěšné synchronizaci se zobrazuje nekonzistentní data primárního klíče](#sync-pkdata)

- [Zobrazuje se významné snížení výkonu.](#sync-perf)

- [Zobrazuje se tato zpráva: "do sloupce nelze vložit hodnotu NULL \<column> . Sloupec nepovoluje hodnoty null. Co to znamená a jak ho můžu opravit?](#sync-nulls)

- [Jak zpracovává cyklické odkazy v synchronizaci dat? To znamená, že když jsou stejná data synchronizovaná ve více skupinách synchronizace a mění se v důsledku změny?](#sync-circ)

### <a name="sync-fails-in-the-portal-ui-for-on-premises-databases-that-are-associated-with-the-client-agent"></a><a name="sync-fails"></a> Synchronizace v uživatelském rozhraní portálu pro místní databáze přidružené k agentovi klienta se nezdařila.

Synchronizace se nezdařila v uživatelském rozhraní portálu Synchronizace dat SQL pro místní databáze, které jsou přidruženy k agentovi klienta. V místním počítači, na kterém je spuštěný agent, se v protokolu událostí zobrazí chyby System. IO. IOException. Chyby říká, že na disku není dostatek místa.

- **Příčina** : Na disku není dostatek místa.

- **Řešení** . Vytvořte více místa na jednotce, na které se nachází adresář% TEMP%.

### <a name="my-sync-group-is-stuck-in-the-processing-state"></a><a name="sync-stuck"></a> Moje skupina synchronizace je zablokovaná ve stavu zpracování.

Skupina synchronizace v Synchronizace dat SQL byla po dlouhou dobu ve stavu zpracování. Nereaguje na příkaz **stop** a protokoly nezobrazí žádné nové položky.

Některá z následujících podmínek může způsobit zablokování skupiny synchronizace ve stavu zpracování:

- **Příčina** : Klientský agent je offline.

- **Řešení** . Ujistěte se, že je klientský agent online, a zkuste to znovu.

- **Příčina** : Klientský agent je odinstalovaný nebo chybí.

- **Řešení** . Pokud je klientský agent odinstalovaný nebo chybí z jiného důvodu:

    1. Odeberte z instalační složky Synchronizace dat SQL soubor XML agenta, pokud existuje.
    1. Nainstalujte agenta na místní počítač (může se jednat o stejný nebo jiný počítač). Pak odešlete klíč agenta, který se vygeneruje na portálu, pro agenta, který se zobrazuje ve stavu offline.

- **Příčina** : Služba Synchronizace dat SQL je zastavená.

- **Řešení** . Restartujte službu Synchronizace dat SQL.

    1. V nabídce **Start** vyhledejte **služby** .
    1. Ve výsledcích hledání vyberte **služby** .
    1. Vyhledejte službu **synchronizace dat SQL** .
    1. Pokud je stav služby **Zastaveno** , klikněte pravým tlačítkem myši na název služby a vyberte možnost **Spustit** .

> [!NOTE]
> Pokud předchozí informace nepřesunou skupinu synchronizace ze stavu zpracování, podpora Microsoftu může obnovit stav skupiny synchronizace. Pokud chcete mít resetování stavu skupiny synchronizace, vytvořte na [stránce s dotazem Microsoft Q&na Azure SQL Database](/answers/topics/azure-sql-database.html)vytvořit příspěvek. V příspěvku zadejte ID předplatného a ID skupiny synchronizace pro skupinu, kterou je nutné resetovat. Podpora Microsoftu inženýr odpoví na váš příspěvek a pošle vám informace o tom, kdy byl stav resetován.

### <a name="i-see-erroneous-data-in-my-tables"></a><a name="sync-baddata"></a> Zobrazuje se v tabulkách chybná data

Pokud jsou tabulky, které mají stejný název, ale které jsou z různých schémat databáze, zahrnuty v synchronizaci, zobrazí se po synchronizaci v tabulkách chybná data.

- **Příčina** : Proces zřizování Synchronizace dat SQL používá stejné sledovací tabulky pro tabulky, které mají stejný název, ale které jsou v různých schématech. Z tohoto důvodu se změny z obou tabulek projeví ve stejné sledovací tabulce. Způsobí to, že během synchronizace dojde k chybným změnám dat.

- **Řešení** . Ujistěte se, že názvy tabulek, které jsou zapojeny do synchronizace, jsou odlišné, a to i v případě, že tabulky patří do různých schémat v databázi.

### <a name="i-see-inconsistent-primary-key-data-after-a-successful-sync"></a><a name="sync-pkdata"></a> Po úspěšné synchronizaci se zobrazuje nekonzistentní data primárního klíče

Synchronizace se nahlásí jako úspěšná a v protokolu se zobrazí žádné neúspěšné nebo vynechané řádky, ale zjistíte, že data primárního klíče jsou nekonzistentní mezi databázemi ve skupině synchronizace.

- **Příčina** : Tento výsledek je záměrné. Změny v jakémkoli sloupci primárního klíče mají za následek nekonzistentní data v řádcích, ve kterých se změnil primární klíč.

- **Řešení** . Pokud se chcete tomuto problému vyhnout, zajistěte, aby se nezměnila žádná data ve sloupci primárního klíče. Chcete-li tento problém vyřešit po jeho výskytu, odstraňte řádek, který obsahuje nekonzistentní data ze všech koncových bodů ve skupině synchronizace. Pak řádek znovu vložte.

### <a name="i-see-a-significant-degradation-in-performance"></a><a name="sync-perf"></a> Zobrazuje se významné snížení výkonu.

Výkon se výrazně snižuje, což může být bod, ve kterém nemůžete ani otevřít uživatelské rozhraní pro synchronizaci dat.

- **Příčina** : Nejpravděpodobnější příčinou je synchronizační smyčka. Ke smyčce synchronizace dojde, když synchronizace podle skupiny synchronizace spustí synchronizaci se skupinou synchronizace B, která potom spustí synchronizaci podle skupiny synchronizace A. Skutečná situace může být složitější a může zahrnovat víc než dvě skupiny synchronizace ve smyčce. Problémem je to, že existuje cyklická aktivace synchronizace, která je způsobená skupinami synchronizace, která se překrývá mezi sebou.

- **Řešení** . Nejlepším řešením je prevence. Ujistěte se, že ve skupinách synchronizace nemáte cyklické odkazy. Každý řádek, který je synchronizovaný jednou skupinou synchronizace, nejde synchronizovat s jinou skupinou synchronizace.

### <a name="i-see-this-message-cannot-insert-the-value-null-into-the-column-column-column-does-not-allow-nulls-what-does-this-mean-and-how-can-i-fix-it"></a><a name="sync-nulls"></a> Zobrazuje se tato zpráva: "do sloupce nelze vložit hodnotu NULL \<column> . Sloupec nepovoluje hodnoty null. Co to znamená a jak ho můžu opravit? 
Tato chybová zpráva znamená, že došlo k jedné ze dvou následujících problémů:
-  Tabulka neobsahuje primární klíč. Chcete-li tento problém vyřešit, přidejte primární klíč ke všem tabulkám, které synchronizujete.
-  V příkazu CREATE INDEX je klauzule WHERE. Synchronizace dat nezpracovává tuto podmínku. Chcete-li tento problém vyřešit, odeberte klauzuli WHERE nebo ručně proveďte změny ve všech databázích. 
 
### <a name="how-does-data-sync-handle-circular-references-that-is-when-the-same-data-is-synced-in-multiple-sync-groups-and-keeps-changing-as-a-result"></a><a name="sync-circ"></a> Jak zpracovává cyklické odkazy v synchronizaci dat? To znamená, že když jsou stejná data synchronizovaná ve více skupinách synchronizace a mění se v důsledku změny?
Synchronizace dat nezpracovává cyklické odkazy. Ujistěte se, že se jim vyhnete. 

## <a name="client-agent-issues"></a>Problémy agenta klienta

Informace o řešení problémů s agentem klienta najdete v tématu řešení potíží s [agentem synchronizace dat](sql-data-sync-agent-overview.md#agent-tshoot).

## <a name="setup-and-maintenance-issues"></a>Problémy s instalací a údržbou

- [Zobrazuje se zpráva o nedostatku místa na disku](#setup-space)

- [Nemůžu odstranit moji skupinu synchronizace](#setup-delete)

- [Nemůžu zrušit registraci databáze SQL Server](#setup-unreg)

- [Nemám dostatečná oprávnění ke spouštění systémových služeb.](#setup-perms)

- [Databáze má stav "zastaralé".](#setup-date)

- [Skupina synchronizace má neaktuální stav](#setup-date2)

- [Skupinu synchronizace nejde odstranit do tří minut od odinstalace nebo zastavení agenta.](#setup-delete2)

- [Co se stane, když obnovíte ztracenou nebo poškozenou databázi?](#setup-restore)

### <a name="i-get-a-disk-out-of-space-message"></a><a name="setup-space"></a> Zobrazuje se zpráva o nedostatku místa na disku

- **Příčina** : Pokud je potřeba odstranit soubory zbylé, může se zobrazit zpráva nedostatek místa na disku. To může být způsobeno antivirovým softwarem nebo soubory, které jsou otevřeny při pokusu o odstranění operací.

- **Řešení** . Ručně odstraňte soubory synchronizace, které jsou ve složce% Temp% ( `del \*sync\* /s` ). Pak odstraňte podadresáře ve složce% Temp%.

> [!IMPORTANT]
> V průběhu synchronizace neodstraňujte žádné soubory.

### <a name="i-cant-delete-my-sync-group"></a><a name="setup-delete"></a> Nemůžu odstranit moji skupinu synchronizace

Pokus o odstranění skupiny synchronizace se nezdaří. Při odstranění skupiny synchronizace může dojít k selhání některého z následujících scénářů:

- **Příčina** : Klientský agent je offline.

- **Řešení** . Zajistěte, aby byl agent klienta online, a akci opakujte.

- **Příčina** : Klientský agent je odinstalovaný nebo chybí.

- **Řešení** . Pokud je klientský agent odinstalovaný nebo chybí z jiného důvodu:  
    a. Odeberte z instalační složky Synchronizace dat SQL soubor XML agenta, pokud existuje.  
    b. Nainstalujte agenta na místní počítač (může se jednat o stejný nebo jiný počítač). Pak odešlete klíč agenta, který se vygeneruje na portálu, pro agenta, který se zobrazuje ve stavu offline.

- **Příčina** : Databáze je offline.

- **Řešení** . Ujistěte se, že jsou vaše databáze všechny online.

- **Příčina** : Skupina synchronizace se zřizuje nebo synchronizuje.

- **Řešení** . Počkejte na dokončení procesu zřizování nebo synchronizace a pak zkuste skupinu synchronizace znovu odstranit.

### <a name="i-cant-unregister-a-sql-server-database"></a><a name="setup-unreg"></a> Nemůžu zrušit registraci databáze SQL Server

- **Příčina** : Pravděpodobně se pokoušíte zrušit registraci databáze, která již byla odstraněna.

- **Řešení** . Pokud chcete zrušit registraci databáze SQL Server, vyberte databázi a pak vyberte **Vynutit odstranění** .

  Pokud se této operaci nepodařilo odebrat databázi ze skupiny synchronizace:

  1. Zastavte a poté restartujte službu hostitel agenta klienta:  
    a. Vyberte nabídku **Start** .  
    b. Do vyhledávacího pole zadejte **Services. msc** .  
    c. V části **programy** v podokně výsledků hledání dvakrát klikněte na položku **služby** .  
    d. Pravým tlačítkem myši klikněte na službu **synchronizace dat SQL** .  
    e. Pokud je služba spuštěná, zastavte ji.  
    f. Pravým tlačítkem myši klikněte na službu a pak vyberte **Spustit** .  
    například Ověřte, zda je databáze stále registrována. Pokud už není zaregistrované, budete hotovi. V opačném případě pokračujte dalším krokem.
  1. Otevřete aplikaci agenta klienta (SqlAzureDataSyncAgent).
  1. Vyberte **Upravit přihlašovací údaje** a potom zadejte přihlašovací údaje pro databázi.
  1. Pokračujte v zrušení registrace.

### <a name="i-dont-have-sufficient-privileges-to-start-system-services"></a><a name="setup-perms"></a> Nemám dostatečná oprávnění ke spouštění systémových služeb.

- **Příčina** : K této chybě dochází ve dvou situacích:
  -   Uživatelské jméno nebo heslo není správné.
  -   Zadaný uživatelský účet nemá dostatečná oprávnění pro přihlášení jako služba.

- **Řešení** . Udělte uživatelskému účtu přihlašovací údaje přihlášení jako služby:

  1. V nabídce **Start**  >  **ovládacích panelů**  >  **Nástroje pro správu**  >  **místní zásady zabezpečení**  >  **Local Policy**  >  **Rights Management uživatel** místní zásady zabezpečení.
  1. Vyberte možnost **Přihlásit se jako služba** .
  1. V dialogovém okně **vlastnosti** přidejte uživatelský účet.
  1. Vyberte **Apply** (Použít) a pak vyberte **OK** .
  1. Zavřete všechna okna.

### <a name="a-database-has-an-out-of-date-status"></a><a name="setup-date"></a> Databáze má stav "zastaralé".

- **Příčina** : Synchronizace dat SQL odebere databáze, které byly od služby offline po dobu 45 dnů nebo více (počítány od doby, kdy byla databáze přepnuta do režimu offline). Pokud je databáze po dobu 45 dnů nebo déle v režimu offline a pak se vrátí zpět do režimu online **, je její stav neaktuální.**

- **Řešení** . Neaktuálnímu stavu **se** můžete vyhnout tak, že zajistíte, aby žádná databáze nepřešla do režimu offline po dobu 45 dnů nebo déle.

  Je-li stav databáze **zastaralá** :

  1. Z synchronizační skupiny odeberte databázi, která má neaktuální **stav.**
  1. Přidejte databázi zpět do skupiny synchronizace.

  > [!WARNING]
  > Ztratíte všechny změny provedené v této databázi v době, kdy byla offline.

### <a name="a-sync-group-has-an-out-of-date-status"></a><a name="setup-date2"></a> Skupina synchronizace má neaktuální stav

- **Příčina** : Pokud se některé změny nepodaří použít po celou dobu uchování 45 dní, může se stát, že se skupina synchronizace zastaralá.

- **Řešení** . Abyste se vyhnuli **zastaralému** stavu pro skupinu synchronizace, Projděte si výsledky úloh synchronizace v prohlížeči historie v pravidelných intervalech. Prozkoumejte a vyřešte všechny změny, které se nezdařily.

  Pokud je stav skupiny synchronizace **neaktuální** , odstraňte skupinu synchronizace a pak ji znovu vytvořte.

### <a name="a-sync-group-cant-be-deleted-within-three-minutes-of-uninstalling-or-stopping-the-agent"></a><a name="setup-delete2"></a> Skupinu synchronizace nejde odstranit do tří minut od odinstalace nebo zastavení agenta.

Nemůžete odstranit skupinu synchronizace během tří minut od odinstalace nebo zastavení přidruženého klientského agenta Synchronizace dat SQL.

- **Řešení** .

  1. Odebere skupinu synchronizace, pokud jsou přidružené agenti synchronizace online (doporučeno).
  1. Pokud je agent offline, ale je nainstalovaný, převeďte ho do online režimu na místním počítači. Počkejte, než se stav agenta zobrazí jako **online** na portálu synchronizace dat SQL. Pak odeberte skupinu synchronizace.
  1. Pokud je agent offline, protože byl odinstalován:  
    a.  Odeberte z instalační složky Synchronizace dat SQL soubor XML agenta, pokud existuje.  
    b.  Nainstalujte agenta na místní počítač (může se jednat o stejný nebo jiný počítač). Pak odešlete klíč agenta, který se vygeneruje na portálu, pro agenta, který se zobrazuje ve stavu offline.  
    c. Pokuste se odstranit skupinu synchronizace.

### <a name="what-happens-when-i-restore-a-lost-or-corrupted-database"></a><a name="setup-restore"></a> Co se stane, když obnovíte ztracenou nebo poškozenou databázi?

Pokud ze zálohy obnovíte ztracenou nebo poškozenou databázi, může dojít k nekonvergenci dat v synchronizačních skupinách, do kterých databáze patří.

## <a name="next-steps"></a>Další kroky
Další informace o Synchronizace dat SQL najdete v tématech:

-   Přehled – [synchronizace dat napříč několika cloudy a místními databázemi pomocí synchronizace dat SQL v Azure](sql-data-sync-data-sql-server-sql-database.md)
-   Nastavení synchronizace dat
    - Na portálu – [kurz: nastavení synchronizace dat SQL pro synchronizaci dat mezi Azure SQL Database a SQL Server](sql-data-sync-sql-server-configure.md)
    - S využitím PowerShellu
        -  [Použití PowerShellu k synchronizaci mezi několika databázemi v Azure SQL Database](scripts/sql-data-sync-sync-data-between-sql-databases.md)
        -  [Použití PowerShellu k synchronizaci mezi databází v Azure SQL Database a databází v instanci SQL Server](scripts/sql-data-sync-sync-data-between-azure-onprem.md)
-   Agent synchronizace dat – [Agent synchronizace dat pro synchronizace dat SQL v Azure](sql-data-sync-agent-overview.md)
-   Osvědčené postupy – [osvědčené postupy pro synchronizace dat SQL v Azure](sql-data-sync-best-practices.md)
-   Monitorování – [monitorování synchronizace dat SQL pomocí protokolů Azure monitor](./monitor-tune-overview.md)
-   Aktualizace schématu synchronizace
    -   Pomocí jazyka Transact-SQL – [Automatizace replikace změn schématu v synchronizace dat SQL v Azure](sql-data-sync-update-sync-schema.md)
    -   Prostředí PowerShell – [použití PowerShellu k aktualizaci schématu synchronizace v existující skupině synchronizace](scripts/update-sync-schema-in-sync-group.md)

Další informace o SQL Database najdete v tématech:

-   [Přehled služby SQL Database](sql-database-paas-overview.md)
-   [Správa životního cyklu databáze](/previous-versions/sql/sql-server-guides/jj907294(v=sql.110))