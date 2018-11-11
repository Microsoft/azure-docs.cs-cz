---
title: Řešení potíží s synchronizace dat Azure SQL | Dokumentace Microsoftu
description: Zjistěte, jak řešit běžné problémy se synchronizací dat SQL Azure.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: data sync
ms.devlang: ''
ms.topic: conceptual
author: allenwux
ms.author: xiwu
ms.reviewer: douglasl
manager: craigg
ms.date: 07/16/2018
ms.openlocfilehash: 44bf04d3840009b9408ccfc51fdcefa7c7e116cb
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2018
ms.locfileid: "51278935"
---
# <a name="troubleshoot-issues-with-sql-data-sync"></a>Řešení potíží se synchronizací dat SQL

Tento článek popisuje řešení známých problémů se synchronizací dat SQL Azure. Pokud je řešení problému, je tady uvedená.

Přehled Synchronizace dat SQL najdete v tématu [Synchronizace dat mezi několika cloudovými a místními databázemi pomocí Synchronizace dat SQL Azure](sql-database-sync-data.md).

## <a name="sync-issues"></a>Problémům se synchronizací

- [Synchronizace se nezdaří v místních databází, které jsou spojeny s agenta klienta uživatelské rozhraní portálu](#sync-fails)

- [Moje skupina synchronizace se zasekla v automatickém stavu zpracování](#sync-stuck)

- [Zobrazí chybná data ve svých tabulek](#sync-baddata)

- [Po úspěšné synchronizaci zobrazí nekonzistentní data primárního klíče](#sync-pkdata)

- [Najdete v článku významné snížení výkonu](#sync-perf)

- [Zobrazí tato zpráva: "nelze vložit hodnoty NULL do sloupce <column>. Sloupec nepovoluje hodnoty nulls." Co to znamená, a jak ho můžou opravit?](#sync-nulls)

- [Jak synchronizovat Data zpracovává. cyklické odkazy? To znamená, když na stejná data se synchronizují do více skupin synchronizace a neustále mění díky tomu?](#sync-circ)

### <a name="sync-fails"></a> Synchronizace se nezdaří v místních databází, které jsou spojeny s agenta klienta uživatelské rozhraní portálu

Synchronizace se nezdaří v uživatelské rozhraní portálu synchronizace dat SQL pro místní databáze, které jsou spojeny s klientského agenta. V místním počítači, na kterém běží agent se zobrazí System.IO.IOException chyby v protokolu událostí. Chyby Řekněme, že disk nemá dostatek místa.

- **Příčina**. Jednotka nemá dostatek místa.

- **Rozlišení**. Vytvořte více místa na disku, na kterém je umístěn adresáři % TEMP %.

### <a name="sync-stuck"></a> Moje skupina synchronizace se zasekla v automatickém stavu zpracování

Skupina synchronizace v synchronizaci dat SQL bylo ve stavu zpracování po dlouhou dobu. Nebude odpovídat **Zastavit** příkazu a protokoly zobrazit žádné nové položky.

Ve skupině synchronizace právě zablokované ve stavu zpracování může způsobit některý z následujících podmínek:

- **Příčina**. Klientský agent je v režimu offline

- **Rozlišení**. Ujistěte se, že agenta klienta je online a pak to zkuste znovu.

- **Příčina**. Klientský agent je odinstalovaný nebo chybí.

- **Rozlišení**. Pokud agenta klienta je odinstalovaný nebo jinak chybí:

    1. Pokud soubor existuje, odstraňte soubor XML agenta z instalační složky sady synchronizace dat SQL.
    1. Nainstalujte agenta na místním počítači (může být stejný nebo jiný počítač). Pak odešlete klíč agenta, který se generuje na portálu pro agenta, který se zobrazuje v režimu offline.

- **Příčina**. Služba synchronizace dat SQL je zastavená.

- **Rozlišení**. Restartujte službu synchronizace dat SQL.

    1. V **Start** nabídky, vyhledejte **služby**.
    1. Ve výsledcích hledání vyberte **služby**.
    1. Najít **synchronizace dat SQL** služby.
    1. Pokud je stav služby **Zastaveno**, klikněte pravým tlačítkem na název služby a potom vyberte **Start**.

> [!NOTE]
> Pokud se výše uvedených informací čárka nepohybuje vaší skupině synchronizace stavu zpracování, Microsoft Support můžete obnovit stav synchronizace skupiny. Mít stav synchronizace skupiny, které obnoví nastavení, v [fórum pro Azure SQL Database](https://social.msdn.microsoft.com/Forums/azure/home?forum=ssdsgetstarted), vytvoří příspěvek. V příspěvku zahrnují ID vašeho předplatného a ID skupiny synchronizace pro skupinu, která je potřeba resetovat. Pracovníkem Microsoft Support bude reagovat na váš příspěvek a vám dá vědět, kdy byl obnoven stav.

### <a name="sync-baddata"></a> Zobrazí chybná data ve svých tabulek

Tabulky, které mají stejný název, ale jsou z různých databázových schématech je zahrnuta v synchronizaci, uvidíte chybná data v tabulkách po synchronizaci.

- **Příčina**. Synchronizace dat SQL procesu zřizování používá stejné tabulky sledování pro tabulky, které mají stejný název, ale které jsou v různých schémat. Z toho důvodu se projeví změny z obou tabulek ve stejné tabulce sledování. To způsobí, že změny chybná data během synchronizace.

- **Rozlišení**. Ujistěte se, že názvy tabulek, které se účastní synchronizace se liší, i v případě, že tabulky patří do různých schémat v databázi.

### <a name="sync-pkdata"></a> Po úspěšné synchronizaci zobrazí nekonzistentní data primárního klíče

Synchronizace se hlásí jako úspěšné a protokol obsahuje žádné řádky se nezdařilo nebo bylo přeskočeno, ale zjistíte, že data primárního klíče je nekonzistentní mezi databázemi ve skupině synchronizace.

- **Příčina**. Tento výsledek chování je záměrné. Změny v libovolný sloupec primárního klíče za následek nekonzistentní data v řádcích, kde byl změněn primární klíč.

- **Rozlišení**. Tomuto problému předejít, ujistěte se, že žádná data v sloupec primárního klíče se nemění. Chcete-li vyřešit tento problém po došlo k chybě, odstraňte řádek, který má nekonzistentní data ze všech koncových bodů ve skupině synchronizace. Vložte řádku.

### <a name="sync-perf"></a> Najdete v článku významné snížení výkonu

Výkon výrazně zhorší, případně do bodu, ve kterém nelze otevřít i uživatelské rozhraní služby Data synchronizace.

- **Příčina**. Nejpravděpodobnější příčinou je smyčka synchronizace. Synchronizace smyčky nastane skupině synchronizace pomocí synchronizace A aktivuje synchronizovat pomocí synchronizace skupiny B, aktivuje se synchronizovat pomocí synchronizace skupiny A. Skutečné situace může být složitější, a to může zahrnovat víc než dvou skupin synchronizace ve smyčce. Tento problém je, že je kruhové aktivace synchronizace, který způsobuje vzájemně překrývající se skupiny synchronizace.

- **Rozlišení**. Nejlepší je ochrany před únikem informací. Ujistěte se, že není nutné cyklické odkazy do skupiny synchronizace. Všechny řádky, které se synchronizuje podle jednu skupinu synchronizace nejde synchronizovat jinou skupinou synchronizace.

### <a name="sync-nulls"></a> Zobrazí tato zpráva: "nelze vložit hodnoty NULL do sloupce <column>. Sloupec nepovoluje hodnoty nulls." Co to znamená, a jak ho můžou opravit? 
Tato chybová zpráva indikuje, že jeden z následujících dvou problémů došlo k chybě:
-  Tabulka nemá primární klíč. Chcete-li vyřešit tento problém, přidáte do všech tabulek, které synchronizujete primární klíč.
-  Existuje klauzule WHERE v příkazu CREATE INDEX. Synchronizace dat není zpracovat tento stav. Chcete-li vyřešit tento problém, odeberte klauzuli WHERE nebo ručně provést změny na všechny databáze. 
 
### <a name="sync-circ"></a> Jak synchronizovat Data zpracovává. cyklické odkazy? To znamená, když na stejná data se synchronizují do více skupin synchronizace a neustále mění díky tomu?
Synchronizace dat nezpracuje. cyklické odkazy. Ujistěte se, že jim vyhnout. 

## <a name="client-agent-issues"></a>Problémy agenta klienta

Řešení potíží s klientského agenta, najdete v článku [problémy řešit agenta synchronizace dat](sql-database-data-sync-agent.md#agent-tshoot).

## <a name="setup-and-maintenance-issues"></a>Problémy s instalací a údržba

- [Zobrazí se zpráva "nedostatek místa na disku"](#setup-space)

- [Nemůžu odstranit Moje skupina synchronizace](#setup-delete)

- [Můžu nelze zrušit registraci k místní databázi SQL serveru](#setup-unreg)

- [Nemáte dostatečná oprávnění ke spouštění systémových služeb](#setup-perms)

- [Databáze je "Zastaralé" stavu](#setup-date)

- [Skupina synchronizace se stavem "Zastaralé"](#setup-date2)

- [V rámci tří minut odinstalace nebo zastavení agenta nelze odstranit skupinu synchronizace](#setup-delete2)

- [Co se stane, když obnovit ztracené nebo poškozené databáze?](#setup-restore)

### <a name="setup-space"></a> Zobrazí se zpráva "nedostatek místa na disku"

- **Příčina**. Pokud třeba odstranit zanechání nepotřebných souborů, může se zobrazit zpráva "nedostatek místa na disku". To může způsobovat antivirový software nebo soubory jsou otevřené, kdy nedochází k pokusům o operace odstranění.

- **Rozlišení**. Odstraňte ručně synchronizovat soubory, které jsou ve složce % temp % (`del \*sync\* /s`). Odstraňte podsložky ve složce % temp %.

> [!IMPORTANT]
> Neodstraňujte žádné soubory, zatímco probíhá synchronizace.

### <a name="setup-delete"></a> Nemůžu odstranit Moje skupina synchronizace

Pokus o odstranění skupiny synchronizace se nezdaří. Některé z následujících scénářů může mít za následek selhání se odstranit skupinu synchronizace:

- **Příčina**. Klientský agent je v režimu offline.

- **Rozlišení**. Ujistěte se, že je agent klienta online a pak to zkuste znovu.

- **Příčina**. Klientský agent je odinstalovaný nebo chybí.

- **Rozlišení**. Pokud agenta klienta je odinstalovaný nebo jinak chybí:  
    a. Pokud soubor existuje, odstraňte soubor XML agenta z instalační složky sady synchronizace dat SQL.  
    b. Nainstalujte agenta na místním počítači (může být stejný nebo jiný počítač). Pak odešlete klíč agenta, který se generuje na portálu pro agenta, který se zobrazuje v režimu offline.

- **Příčina**. Databáze je offline.

- **Rozlišení**. Ujistěte se, že jsou všechny online databáze SQL serveru a databázím SQL.

- **Příčina**. Skupina synchronizace se zřizování nebo synchronizace.

- **Rozlišení**. Počkejte na dokončení procesu zřizování nebo synchronizace a potom opakovat pokus o odstranění skupiny synchronizace.

### <a name="setup-unreg"></a> Můžu nelze zrušit registraci k místní databázi SQL serveru

- **Příčina**. Pravděpodobně se pokoušíte zrušit registraci databázi, která již byla odstraněna.

- **Rozlišení**. Pokud chcete zrušit registraci k místní databázi SQL serveru, vyberte databázi a pak vyberte **Vynutit odstranění**.

  Pokud tuto operaci se nepodařilo odebrat databázi ze skupiny synchronizace:

  1. Zastavte a restartujte službu Hostitel agenta klienta:  
    a. Vyberte **Start** nabídky.  
    b. Do vyhledávacího pole zadejte **services.msc**.  
    c. V **programy** podokno, výsledky hledání oddílu dvakrát klikněte na panel **služby**.  
    d. Klikněte pravým tlačítkem myši **synchronizace dat SQL** služby.  
    e. Pokud je služba spuštěná, zastavte ho.  
    f. Klikněte pravým tlačítkem na službu a pak vyberte **Start**.  
    g. Zkontrolujte, zda je databáze pořád zaregistrovaný. Pokud už je zaregistrovaný, jste hotovi. V opačném případě pokračujte dalším krokem.
  1. Otevřete aplikaci agenta klienta (SqlAzureDataSyncAgent).
  1. Vyberte **upravit přihlašovací údaje**a pak zadejte přihlašovací údaje pro databázi.
  1. Pokračujte v rušení registrace.

### <a name="setup-perms"></a> Nemáte dostatečná oprávnění ke spouštění systémových služeb

- **Příčina**. K této chybě dojde ve dvou situacích:
  -   Uživatelské jméno nebo heslo nejsou správné.
  -   Zadaný uživatelský účet nemá dostatečná oprávnění k přihlášení jako služba.

- **Rozlišení**. Udělit log na as-a-service přihlašovací údaje pro uživatelský účet:

  1. Přejděte na **Start** > **ovládací panely** > **nástroje pro správu** > **místní zásady zabezpečení**  >  **Místní zásady** > **uživatele Rights Management**.
  1. Vyberte **přihlásit jako službu**.
  1. V **vlastnosti** dialogovém okně Přidat uživatelský účet.
  1. Vyberte **Apply** (Použít) a pak vyberte **OK**.
  1. Zavřete všechna okna.

### <a name="setup-date"></a> Databáze je "Zastaralé" stavu

- **Příčina**. Synchronizace dat SQL odstraní databáze, které byly offline ze služby pro 45 dnů a víc (jak se počítá od doby, kdy databáze přešel do režimu offline). Pokud databáze je v režimu offline pro 45 dnů nebo déle a pak přejde do režimu online, je jeho stav **zastaralé**.

- **Rozlišení**. Můžete se vyhnout **zastaralé** stavu tím, že zajišťuje, že žádná z vašich databází přejdou do režimu offline pro 45 dnů nebo déle.

  Pokud je stav vaší databáze **zastaralé**:

  1. Odebrat databázi, která má **zastaralé** stav od skupiny synchronizace.
  1. Přidat databázi zpět do skupiny synchronizace.

  > [!WARNING]
  > Můžete ztratit všechny změny provedené u této databáze, když byl v režimu offline.

### <a name="setup-date2"></a> Skupina synchronizace se stavem "Zastaralé"

- **Příčina**. Pokud jeden nebo více změn platí pro celý uchovávají 45 dní, skupině synchronizace můžete začnou být zastaralé.

- **Rozlišení**. Aby se zabránilo **zastaralé** stavu pro skupinu synchronizace, podívejte se na výsledky úlohy synchronizace v prohlížeči historie v pravidelných intervalech. Prozkoumat a vyřešit všechny změny, které se nepodařilo použít.

  Pokud je stav synchronizace skupiny **zastaralé**, odstranit skupinu synchronizace a pak ji znovu vytvořit.

### <a name="setup-delete2"></a> V rámci tří minut odinstalace nebo zastavení agenta nelze odstranit skupinu synchronizace

V rámci tří minut odinstalace nebo zastavení přidružené klientského agenta synchronizace dat SQL nelze odstranit skupinu synchronizace.

- **Rozlišení**.

  1. Odstranit skupinu synchronizace agenti přidružené synchronizace jsou online (doporučeno).
  1. Pokud agent je v režimu offline, ale je nainstalovaná, přepněte na místní počítač je online. Počkejte, stav agenta jako **Online** na portálu pro synchronizaci dat SQL. Potom odeberte skupinu synchronizace.
  1. Pokud je agent offline, protože byla odinstalována:  
    a.  Pokud soubor existuje, odstraňte soubor XML agenta z instalační složky sady synchronizace dat SQL.  
    b.  Nainstalujte agenta na místním počítači (může být stejný nebo jiný počítač). Pak odešlete klíč agenta, který se generuje na portálu pro agenta, který se zobrazuje v režimu offline.  
    c. Pokuste se odstranit skupinu synchronizace.

### <a name="setup-restore"></a> Co se stane, když obnovit ztracené nebo poškozené databáze?

Pokud obnovujete ztracené nebo poškozené databáze ze zálohy, může být konvergence dat do skupiny synchronizace, na které databáze patří.

## <a name="next-steps"></a>Další postup
Další informace o synchronizaci dat SQL najdete v tématu:

-   [Synchronizace dat mezi několika cloudovými a místními databázemi pomocí Synchronizace dat SQL Azure](sql-database-sync-data.md)  
-   [Nastavení Synchronizace dat SQL Azure](sql-database-get-started-sql-data-sync.md)  
-   [Osvědčené postupy pro Synchronizaci dat SQL Azure](sql-database-best-practices-data-sync.md)  
-   [Monitorování Synchronizace dat SQL Azure s využitím Log Analytics](sql-database-sync-monitor-oms.md)  
-   Úplné příklady PowerShellu ukazující konfiguraci Synchronizace dat SQL:  
    -   [Synchronizace mezi několika databázemi SQL Azure pomocí PowerShellu](scripts/sql-database-sync-data-between-sql-databases.md)  
    -   [Použití PowerShellu k synchronizaci mezi službou Azure SQL Database a místní databází SQL Serveru](scripts/sql-database-sync-data-between-azure-onprem.md)  

Další informace o službě SQL Database najdete v tématu:

-   [Přehled služby SQL Database](sql-database-technical-overview.md)
-   [Správa životního cyklu databáze](https://msdn.microsoft.com/library/jj907294.aspx)
