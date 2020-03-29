---
title: Řešení potíží se synchronizací dat SQL
description: Přečtěte si, jak řešit běžné problémy se synchronizací dat Azure SQL.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: data sync
ms.devlang: ''
ms.topic: conceptual
author: allenwux
ms.author: xiwu
ms.reviewer: carlrab
ms.date: 12/20/2018
ms.openlocfilehash: 8e223d0c6243bfddc1e5a56867c4c69de5e2a62e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73822458"
---
# <a name="troubleshoot-issues-with-sql-data-sync"></a>Řešení potíží s funkcí Synchronizace dat SQL

Tento článek popisuje, jak řešit známé problémy se synchronizací dat Azure SQL. Pokud existuje řešení problému, je k dispozici zde.

Přehled Synchronizace dat SQL najdete v tématu [Synchronizace dat mezi několika cloudovými a místními databázemi pomocí Synchronizace dat SQL Azure](sql-database-sync-data.md).

> [!IMPORTANT]
> Azure SQL Data Sync v tuto chvíli **nepodporuje** spravovanou instanci Azure SQL Database.

## <a name="sync-issues"></a>Problémy se synchronizací

- [Synchronizace se nezdaří v portálu uhlavního prostředí pro místní databáze, které jsou přidruženy k agentovi klienta](#sync-fails)

- [Moje synchronizační skupina se zasekla ve stavu zpracování](#sync-stuck)

- [V tabulkách se zobrazují chybné údaje](#sync-baddata)

- [Po úspěšné synchronizaci se zobrazují nekonzistentní data primárního klíče](#sync-pkdata)

- [Vidím významné zhoršení výkonu](#sync-perf)

- [Zobrazuje se tato zpráva: "Nelze vložit \<hodnotu NULL do sloupce sloupce>. Sloupec neumožňuje nulls." Co to znamená a jak to mohu opravit?](#sync-nulls)

- [Jak synchronizace dat zpracovává cyklické odkazy? To znamená, že když jsou stejná data synchronizována ve více skupinách synchronizace a v důsledku toho se neustále mění?](#sync-circ)

### <a name="sync-fails-in-the-portal-ui-for-on-premises-databases-that-are-associated-with-the-client-agent"></a><a name="sync-fails"></a>Synchronizace se nezdaří v portálu uhlavního prostředí pro místní databáze, které jsou přidruženy k agentovi klienta

Synchronizace se nezdaří v uzly portálu SQL Data Sync pro místní databáze, které jsou přidruženy k agentovi klienta. V místním počítači, ve který je spuštěn agent, se v protokolu událostí zobrazí chyby System.IO.IOException. Chyby říkají, že disk nemá dostatek místa.

- **Příčina**. Jednotka nemá dostatek místa.

- **Rozlišení**. Vytvořte více místa na jednotce, na které je umístěn adresář %TEMP%.

### <a name="my-sync-group-is-stuck-in-the-processing-state"></a><a name="sync-stuck"></a>Moje synchronizační skupina se zasekla ve stavu zpracování

Synchronizační skupina v synchronizaci dat SQL je ve stavu zpracování po dlouhou dobu. Nereaguje na příkaz **stop** a protokoly nezobrazují žádné nové položky.

Některé z následujících podmínek může mít za následek synchronizační skupiny se zasekl ve stavu zpracování:

- **Příčina**. Klientský agent je offline.

- **Rozlišení**. Ujistěte se, že je klientský agent online, a zkuste to znovu.

- **Příčina**. Klientský agent je odinstalovaný nebo chybí.

- **Rozlišení**. Pokud je klientský agent odinstalovaný nebo chybí z jiného důvodu:

    1. Odeberte z instalační složky Synchronizace dat SQL soubor XML agenta, pokud existuje.
    1. Nainstalujte agenta na místní počítač (může se jednat o stejný nebo jiný počítač). Pak odešlete klíč agenta, který se vygeneruje na portálu, pro agenta, který se zobrazuje ve stavu offline.

- **Příčina**. Služba Synchronizace dat SQL je zastavená.

- **Rozlišení**. Restartujte službu SYNCHRONIZACE dat SQL.

    1. V nabídce **Start** vyhledejte **služby**.
    1. Ve výsledcích hledání vyberte **položku Služby**.
    1. Vyhledejte službu **SYNCHRONIZACE dat SQL.**
    1. Pokud je stav **služby Zastaveno**, klepněte pravým tlačítkem myši na název služby a pak vyberte **příkaz Start**.

> [!NOTE]
> Pokud předchozí informace nepřesunou vaši skupinu synchronizace ze stavu zpracování, podpora Microsoftu může obnovit stav vaší skupiny synchronizace. Chcete-li obnovit stav skupiny synchronizace, vytvořte ve [fóru Azure SQL Database](https://social.msdn.microsoft.com/Forums/azure/home?forum=ssdsgetstarted)příspěvek. Do příspěvku uveďte ID předplatného a ID skupiny synchronizace pro skupinu, kterou je třeba resetovat. Pracovník podpory společnosti Microsoft odpoví na váš příspěvek a dá vám vědět, kdy byl stav resetován.

### <a name="i-see-erroneous-data-in-my-tables"></a><a name="sync-baddata"></a>V tabulkách se zobrazují chybné údaje

Pokud tabulky, které mají stejný název, ale které jsou z různých schémat databáze jsou zahrnuty v synchronizaci, zobrazí chybná data v tabulkách po synchronizaci.

- **Příčina**. Proces zřizování synchronizace dat SQL používá stejné tabulky sledování pro tabulky, které mají stejný název, ale které jsou v různých schématech. Z tohoto důvodu se změny z obou tabulek projeví ve stejné tabulce sledování. To způsobí chybné změny dat během synchronizace.

- **Rozlišení**. Ujistěte se, že názvy tabulek, které jsou zapojeny do synchronizace se liší, i v případě, že tabulky patří do různých schémat v databázi.

### <a name="i-see-inconsistent-primary-key-data-after-a-successful-sync"></a><a name="sync-pkdata"></a>Po úspěšné synchronizaci se zobrazují nekonzistentní data primárního klíče

Synchronizace je hlášena jako úspěšná a protokol nezobrazuje žádné neúspěšné nebo přeskočené řádky, ale zjistíte, že data primárního klíče jsou nekonzistentní mezi databázemi ve skupině synchronizace.

- **Příčina**. Tento výsledek je záměrné. Změny ve sloupci primárního klíče mají za následek nekonzistentní data v řádcích, kde byl změněn primární klíč.

- **Rozlišení**. Chcete-li tomuto problému zabránit, ujistěte se, že se nezmění žádná data ve sloupci primárního klíče. Chcete-li tento problém vyřešit poté, co k němu došlo, odstraňte řádek, který obsahuje nekonzistentní data ze všech koncových bodů ve skupině synchronizace. Potom znovu vložte řádek.

### <a name="i-see-a-significant-degradation-in-performance"></a><a name="sync-perf"></a>Vidím významné zhoršení výkonu

Váš výkon výrazně snižuje, možná do bodu, kdy nelze ani otevřít ui synchronizace dat.

- **Příčina**. Nejpravděpodobnější příčinou je synchronizační smyčka. Synchronizační smyčka nastane, když synchronizace podle skupiny synchronizace A spustí synchronizaci podle skupiny synchronizace B, která pak spustí synchronizaci podle skupiny synchronizace A. Skutečná situace může být složitější a může zahrnovat více než dvě skupiny synchronizace ve smyčce. Problém je, že je cyklické spouštění synchronizace, která je způsobena skupiny synchronizace překrývající se navzájem.

- **Rozlišení**. Nejlepší oprava je prevence. Ujistěte se, že ve skupinách synchronizace nemáte cyklické odkazy. Žádný řádek, který je synchronizován podle jedné skupiny synchronizace, nelze synchronizovat jinou skupinou synchronizace.

### <a name="i-see-this-message-cannot-insert-the-value-null-into-the-column-column-column-does-not-allow-nulls-what-does-this-mean-and-how-can-i-fix-it"></a><a name="sync-nulls"></a>Zobrazuje se tato zpráva: "Nelze vložit \<hodnotu NULL do sloupce sloupce>. Sloupec neumožňuje nulls." Co to znamená a jak to mohu opravit? 
Tato chybová zpráva označuje, že došlo k jednomu ze dvou následujících problémů:
-  Tabulka nemá primární klíč. Chcete-li tento problém vyřešit, přidejte primární klíč ke všem synchronizovaním tabulkám.
-  V příkazu CREATE INDEX je klauzule WHERE. Synchronizace dat nezpracovává tuto podmínku. Chcete-li tento problém vyřešit, odeberte klauzuli WHERE nebo ručně proveďte změny ve všech databázích. 
 
### <a name="how-does-data-sync-handle-circular-references-that-is-when-the-same-data-is-synced-in-multiple-sync-groups-and-keeps-changing-as-a-result"></a><a name="sync-circ"></a>Jak synchronizace dat zpracovává cyklické odkazy? To znamená, že když jsou stejná data synchronizována ve více skupinách synchronizace a v důsledku toho se neustále mění?
Synchronizace dat nezpracovává cyklické odkazy. Ujistěte se, že se jim vyhnout. 

## <a name="client-agent-issues"></a>Problémy s agentem klienta

Informace o řešení problémů s klientským agentem naleznete [v tématu Poradce při potížích s agentem synchronizace dat](sql-database-data-sync-agent.md#agent-tshoot).

## <a name="setup-and-maintenance-issues"></a>Problémy s instalací a údržbou

- [Zobrazuje se zpráva "disk mimo místo"](#setup-space)

- [Nelze odstranit skupinu synchronizace](#setup-delete)

- [Nelze zrušit registraci místní databáze serveru SQL Server](#setup-unreg)

- [Nemám dostatečná oprávnění ke spuštění systémových služeb](#setup-perms)

- [Databáze má stav "Out-of-Date"](#setup-date)

- [Synchronizační skupina má stav "Out-of-Date"](#setup-date2)

- [Skupinu synchronizace nelze odstranit do tří minut od odinstalování nebo zastavení agenta.](#setup-delete2)

- [Co se stane, když obnovím ztracenou nebo poškozenou databázi?](#setup-restore)

### <a name="i-get-a-disk-out-of-space-message"></a><a name="setup-space"></a>Zobrazuje se zpráva "disk mimo místo"

- **Příčina**. Pokud je třeba odstranit zbývající soubory, může se zobrazit zpráva "disk mimo místo". Příčinou může být antivirový software nebo soubory jsou otevřeny při pokusu o odstranění.

- **Rozlišení**. Ručně odstraňte synchronizační soubory, které jsou`del \*sync\* /s`ve složce %temp% ( ). Potom odstraňte podadresáře ve složce %temp%.

> [!IMPORTANT]
> Během synchronizace neodstraňujte žádné soubory.

### <a name="i-cant-delete-my-sync-group"></a><a name="setup-delete"></a>Nelze odstranit skupinu synchronizace

Pokus o odstranění skupiny synchronizace se nezdaří. Některý z následujících scénářů může způsobit selhání odstranění skupiny synchronizace:

- **Příčina**. Klientský agent je offline.

- **Rozlišení**. Ujistěte se, že agent klienta je online a akci opakujte.

- **Příčina**. Klientský agent je odinstalovaný nebo chybí.

- **Rozlišení**. Pokud je klientský agent odinstalovaný nebo chybí z jiného důvodu:  
    a. Odeberte z instalační složky Synchronizace dat SQL soubor XML agenta, pokud existuje.  
    b. Nainstalujte agenta na místní počítač (může se jednat o stejný nebo jiný počítač). Pak odešlete klíč agenta, který se vygeneruje na portálu, pro agenta, který se zobrazuje ve stavu offline.

- **Příčina**. Databáze je offline.

- **Rozlišení**. Ujistěte se, že databáze SQL a SQL Server jsou všechny online.

- **Příčina**. Skupina synchronizace zřizuje nebo synchronizuje.

- **Rozlišení**. Počkejte, dokud proces zřizování nebo synchronizace dokončí a opakujte odstranění skupiny synchronizace.

### <a name="i-cant-unregister-an-on-premises-sql-server-database"></a><a name="setup-unreg"></a>Nelze zrušit registraci místní databáze serveru SQL Server

- **Příčina**. S největší pravděpodobností se pokoušíte zrušit registraci databáze, která již byla odstraněna.

- **Rozlišení**. Chcete-li zrušit registraci místní databáze serveru SQL Server, vyberte databázi a vyberte **možnost Vynutit odstranění**.

  Pokud se této operaci nepodaří odebrat databázi ze skupiny synchronizace:

  1. Zastavit a restartovat hostitelskou službu agenta klienta:  
    a. Vyberte nabídku **Start.**  
    b. Do vyhledávacího pole zadejte **services.msc**.  
    c. V části **Programy** v podokně výsledků hledání poklepejte na **položku Služby**.  
    d. Klikněte pravým tlačítkem myši na službu **SYNCHRONIZACE dat SQL.**  
    e. Pokud je služba spuštěna, zastavte ji.  
    f. Klikněte pravým tlačítkem myši na službu a potom vyberte **příkaz Start**.  
    g. Zkontrolujte, zda je databáze stále registrována. Pokud již není registrována, jste hotovi. V opačném případě pokračujte dalším krokem.
  1. Otevřete aplikaci klientského agenta (SqlAzureDataSyncAgent).
  1. Vyberte **Upravit pověření**a zadejte pověření pro databázi.
  1. Pokračujte zrušením registrace.

### <a name="i-dont-have-sufficient-privileges-to-start-system-services"></a><a name="setup-perms"></a>Nemám dostatečná oprávnění ke spuštění systémových služeb

- **Příčina**. K této chybě dochází ve dvou situacích:
  -   Uživatelské jméno a/nebo heslo jsou nesprávné.
  -   Zadaný uživatelský účet nemá dostatečná oprávnění k přihlášení jako služba.

- **Rozlišení**. Udělte uživatelskému účtu přihlašovací údaje jako služby:

  1. Přejděte na **spouštění** > **nástrojů** > pro**správu místních** > **zásad** > zabezpečení**Správa uživatelských práv místních****zásad** > .
  1. Vyberte **možnost Přihlásit se jako služba**.
  1. V dialogovém okně **Vlastnosti** přidejte uživatelský účet.
  1. Vyberte **Apply** (Použít) a pak vyberte **OK**.
  1. Zavřete všechna okna.

### <a name="a-database-has-an-out-of-date-status"></a><a name="setup-date"></a>Databáze má stav "Out-of-Date"

- **Příčina**. SQL Data Sync odebere databáze, které byly offline ze služby po dobu 45 dnů nebo více (jak se počítá od okamžiku, kdy databáze přešel do režimu offline). Pokud je databáze offline po dobu 45 dnů nebo déle a poté se vrátí do režimu online, je její stav **zastaralý**.

- **Rozlišení**. Můžete se vyhnout **zastaralý** stav tím, že zajistíte, že žádná z vašich databází přejít do offline po dobu 45 dnů nebo více.

  Pokud je stav databáze **zastaralý**:

  1. Odeberte databázi, která má zastaralý stav **ze** skupiny synchronizace.
  1. Přidejte databázi zpět do skupiny synchronizace.

  > [!WARNING]
  > Ztratíte všechny změny provedené v této databázi v době, kdy byla offline.

### <a name="a-sync-group-has-an-out-of-date-status"></a><a name="setup-date2"></a>Synchronizační skupina má stav "Out-of-Date"

- **Příčina**. Pokud se jedna nebo více změn neuplatní po celou dobu uchovávání 45 dnů, může být synchronizační skupina zastaralá.

- **Rozlišení**. Chcete-li se vyhnout **zastaralému** stavu pro synchronizační skupinu, pravidelně prověřujte výsledky úloh synchronizace v prohlížeči historie. Prozkoumejte a vyřešte všechny změny, které se nepoužijí.

  Pokud je stav skupiny synchronizace **zastaralý**, odstraňte skupinu synchronizace a znovu ji vytvořte.

### <a name="a-sync-group-cant-be-deleted-within-three-minutes-of-uninstalling-or-stopping-the-agent"></a><a name="setup-delete2"></a>Skupinu synchronizace nelze odstranit do tří minut od odinstalování nebo zastavení agenta.

Skupinu synchronizace nelze odstranit do tří minut od odinstalování nebo zastavení přidruženého agenta klienta synchronizace dat SQL.

- **Rozlišení**.

  1. Odeberte skupinu synchronizace, pokud jsou přidružení agenti synchronizace online (doporučeno).
  1. Pokud je agent offline, ale je nainstalován, přepězte jej do režimu online v místním počítači. Počkejte na stav agenta se zobrazí jako **Online** na portálu synchronizace dat SQL. Potom odeberte skupinu synchronizace.
  1. Pokud je agent offline, protože byl odinstalován:  
    a.  Odeberte z instalační složky Synchronizace dat SQL soubor XML agenta, pokud existuje.  
    b.  Nainstalujte agenta na místní počítač (může se jednat o stejný nebo jiný počítač). Pak odešlete klíč agenta, který se vygeneruje na portálu, pro agenta, který se zobrazuje ve stavu offline.  
    c. Pokuste se odstranit skupinu synchronizace.

### <a name="what-happens-when-i-restore-a-lost-or-corrupted-database"></a><a name="setup-restore"></a>Co se stane, když obnovím ztracenou nebo poškozenou databázi?

Pokud obnovíte ztracenou nebo poškozenou databázi ze zálohy, může být nekonvergence dat ve skupinách synchronizace, do kterých databáze patří.

## <a name="next-steps"></a>Další kroky
Další informace o synchronizaci dat SQL naleznete v tématu:

-   Přehled – [Synchronizace dat mezi několika cloudovými a místními databázemi pomocí Azure SQL Data Sync](sql-database-sync-data.md)
-   Nastavení synchronizace dat
    - Na portálu – [kurz: Nastavení synchronizace dat SQL pro synchronizaci dat mezi databází Azure SQL a SQL Server em i v místním prostředí](sql-database-get-started-sql-data-sync.md)
    - S využitím PowerShellu
        -  [Synchronizace mezi několika databázemi Azure SQL pomocí PowerShellu](scripts/sql-database-sync-data-between-sql-databases.md)
        -  [Použití PowerShellu k synchronizaci mezi službou Azure SQL Database a místní databází SQL Serveru](scripts/sql-database-sync-data-between-azure-onprem.md)
-   Agent synchronizace dat – [agent synchronizace dat pro synchronizaci dat Azure SQL](sql-database-data-sync-agent.md)
-   Doporučené postupy – [doporučené postupy pro Azure SQL Data Sync](sql-database-best-practices-data-sync.md)
-   Monitor – [monitorování synchronizace dat SQL pomocí protokolů Azure Monitoru](sql-database-sync-monitor-oms.md)
-   Aktualizace schématu synchronizace
    -   S Transact-SQL – [automatizace replikace změn schématu v Synchronizaci dat Azure SQL](sql-database-update-sync-schema.md)
    -   S PowerShellem – [k aktualizaci schématu synchronizace v existující skupině synchronizace použijte PowerShell](scripts/sql-database-sync-update-schema.md)

Další informace o databázi SQL naleznete v tématu:

-   [Přehled služby SQL Database](sql-database-technical-overview.md)
-   [Správa životního cyklu databáze](https://msdn.microsoft.com/library/jj907294.aspx)
