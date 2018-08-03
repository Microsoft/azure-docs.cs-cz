---
title: Řešení potíží s synchronizace dat Azure SQL | Dokumentace Microsoftu
description: Zjistěte, jak řešit běžné problémy se synchronizací dat SQL Azure.
services: sql-database
ms.date: 07/16/2018
ms.topic: conceptual
ms.service: sql-database
author: allenwux
ms.author: xiwu
manager: craigg
ms.custom: data-sync
ms.openlocfilehash: 2853310df1183a8b32450c5b5c79289b78d1a26d
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39422497"
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

- [Agent klienta nainstalovat, odinstalujete nebo opravíte selže](#agent-install)

- [Klientský agent nebude fungovat po můžu zrušit odinstalaci](#agent-uninstall)

- [Moje databáze není uvedená v seznamu agenta](#agent-list)

- [Klientský agent aktualizace softwaru nelze spustit (Chyba 1069)](#agent-start)

- [Nemůžu Odeslat klíč agenta](#agent-key)

- [Klientský agent nelze odstranit z portálu, pokud jeho přidružené místní databáze nedostupný](#agent-delete)

- [Místní Agent synchronizace aplikace se nemůže připojit k místní synchronizační služby](#agent-connect)

### <a name="agent-install"></a> Agent klienta nainstalovat, odinstalujete nebo opravíte selže

- **Příčina**. Mnoho scénářů může vést k této chybě. Chcete-li zjistit, konkrétní příčinu této chyby, podívejte se na protokoly.

- **Rozlišení**. Pokud chcete najít konkrétní příčinu selhání, generovat a podívejte se na protokoly Instalační služby systému Windows. Můžete zapnout protokolování na příkazovém řádku. Například pokud stažený soubor AgentServiceSetup.msi LocalAgentHost.msi, generovat a prozkoumejte soubory protokolu s použitím následujících příkazových řádků:

    -   Pro instalace: `msiexec.exe /i SQLDataSyncAgent-Preview-ENU.msi /l\*v LocalAgentSetup.InstallLog`
    -   Pro odinstaluje: `msiexec.exe /x SQLDataSyncAgent-se-ENU.msi /l\*v LocalAgentSetup.InstallLog`

    Můžete také zapnout protokolování pro všechna zařízení, které se provádí pomocí Instalační služby systému Windows. Článek znalostní báze Microsoft [jak povolit protokolování Instalační služby systému Windows](https://support.microsoft.com/help/223300/how-to-enable-windows-installer-logging) poskytuje řešení jedním kliknutím k zapnutí protokolování pro Instalační služby systému Windows. Poskytuje také umístění protokolů.

### <a name="agent-uninstall"></a> Klientský agent nebude fungovat po můžu zrušit odinstalaci

Klientský agent nebude fungovat, i když můžete zrušit jeho odinstalaci.

- **Příčina**. K tomu dochází, protože klientský agent synchronizace dat SQL nejsou uloženy přihlašovací údaje.

- **Rozlišení**. Můžete vyzkoušet tyto dvě řešení:

    -   Zadejte znovu přihlašovací údaje pro agenta klienta pomocí modulu services.msc.
    -   Odinstalujte tohoto agenta klienta a pak nainstalovat novou. Stáhněte a nainstalujte nejnovějšího agenta klienta [Download Center](http://go.microsoft.com/fwlink/?linkid=221479).

### <a name="agent-list"></a> Moje databáze není uvedená v seznamu agenta

Při pokusu o přidání existující databáze SQL serveru do skupiny synchronizace databáze nezobrazí v seznamu agentů.

Tyto scénáře mohou způsobit potíže:

- **Příčina**. Skupina synchronizace a agent klienta jsou v různých datových centrech.

- **Rozlišení**. Klientský agent a skupinu synchronizace musí být ve stejném datacentru. Chcete-li toto nastavení, máte dvě možnosti:

    -   Vytvořte nového agenta v datovém centru, kde se nachází skupina synchronizace. Databáze potom zaregistrujte tohoto agenta.
    -   Odstraňte aktuální skupinu synchronizace. Potom znovu vytvořte skupinu synchronizace v datacentru, kde se nachází agent.

- **Příčina**. Klientský agent seznamu databází, které není aktuální.

- **Rozlišení**. Zastavte a restartujte službu agenta klienta.

    Místní agent přitom stáhne seznam přidružených databázích pouze při prvním odeslání klíč agenta. To se nestahuje seznam přidružených databázích v odesílání následných agenta klíče. Databáze, které jsou registrovány během jako přesunutí na agenta nezobrazují v původní instance agenta.

### <a name="agent-start"></a> Klientský agent aktualizace softwaru nelze spustit (Chyba 1069)

Zjistíte, zda není spuštěn agent na počítači, který je hostitelem systému SQL Server. Při pokusu o ruční spuštění agenta, se zobrazí dialogové okno, které se zobrazí zpráva "chyby 1069: Služba se nespustila kvůli selhání přihlášení."

![Dialogové okno chyby 1069 synchronizace dat](media/sql-database-troubleshoot-data-sync/sync-error-1069.png)

- **Příčina**. Pravděpodobnou příčinou této chyby je, že heslo na místním serveru změnila od jeho vytvoření agenta a agent heslo.

- **Rozlišení**. Aktualizace agenta hesla pro vaše aktuální heslo serveru:

  1. Vyhledejte službu agenta klienta synchronizace dat SQL.  
    a. Vyberte **Start**.  
    b. Do vyhledávacího pole zadejte **services.msc**.  
    c. Ve výsledcích hledání vyberte **služby**.  
    d. V **služby** okno, přejděte na položku pro **agenta synchronizace dat SQL**.  
  1. Klikněte pravým tlačítkem na **agenta synchronizace dat SQL**a pak vyberte **Zastavit**.
  1. Klikněte pravým tlačítkem na **agenta synchronizace dat SQL**a pak vyberte **vlastnosti**.
  1. Na **vlastnosti agenta synchronizace dat SQL**, vyberte **přihlášení** kartu.
  1. V **heslo** pole, zadejte své heslo.
  1. V **Potvrdit heslo** pole, zadejte znovu své heslo.
  1. Vyberte **Apply** (Použít) a pak vyberte **OK**.
  1. V **služby** okna, klikněte pravým tlačítkem na **agenta synchronizace dat SQL** služby a potom klikněte na tlačítko **Start**.
  1. Zavřít **služby** okna.

### <a name="agent-key"></a> Nemůžu Odeslat klíč agenta

Po vytvoření nebo znovu vytvořit klíč pro agenta, pokusu o odeslání klíč prostřednictvím SqlAzureDataSyncAgent aplikace. Odeslání nepodaří dokončit.

![Dialogové okno chyby synchronizace – nejde odeslat klíč agenta](media/sql-database-troubleshoot-data-sync/sync-error-cant-submit-agent-key.png)

- **Požadavky**. Než budete pokračovat, zkontrolujte následující požadavky:

  - Se službou Windows synchronizace dat SQL.

  - Účet služby pro službu Windows synchronizace dat SQL má přístup k síti.

  - Odchozí port 1433 je otevřen v místní bráně firewall pravidla.

  - Místní IP adresa se přidá na server nebo pravidlo brány firewall databáze pro databázi metadat synchronizace.

- **Příčina**. Klíč agenta jednoznačně identifikuje každého místního agenta. Klíč musí splňovat dvě podmínky:

  -   Klíč agenta klienta v synchronizaci dat SQL serveru a místní počítač musí být identické.
  -   Klíč agenta klienta lze použít pouze jednou.

- **Rozlišení**. Pokud agenta není funkční, bude to, že jedna nebo obě tyto podmínky nejsou splněné. Pokud chcete získat agenta znovu pracovat:

  1. Generovat nový klíč.
  1. Nový klíč platí pro agenta.

  Chcete-li použít nový klíč k agentovi:

  1. V Průzkumníku souborů přejděte do instalační adresář agenta. Výchozí adresář instalace je C:\\Program Files (x86)\\synchronizace dat Microsoft SQL.
  1. Dvakrát klikněte na panel podadresáře bin.
  1. Otevřete aplikaci SqlAzureDataSyncAgent.
  1. Vyberte **odešlete klíč agenta**.
  1. V poskytnutém prostoru vložte klíč ze schránky.
  1. Vyberte **OK**.
  1. Program ukončíte.

### <a name="agent-delete"></a> Klientský agent nelze odstranit z portálu, pokud jeho přidružené místní databáze nedostupný

Pokud místní koncový bod (to znamená, že databáze), který je registrovaný pomocí synchronizace dat SQL klientského agenta se nestane nedostupnou, nelze odstranit agenta klienta.

- **Příčina**. Místní agent nejde odstranit, protože do nedostupný databáze pořád bude registrovaný s agentem. Při pokusu o odstranění agenta proces odstranění se pokusí kontaktovat databázi, která se nezdaří.

- **Rozlišení**. Použití "Vynutit odstranění" do nedostupný databázi odstranit.

> [!NOTE]
> Pokud tabulky metadat synchronizace zůstanou po "Vynutit odstranění", použijte `deprovisioningutil.exe` ho PROČISTIT.

### <a name="agent-connect"></a> Místní Agent synchronizace aplikace se nemůže připojit k místní synchronizační služby

- **Rozlišení**. Vyzkoušejte následující kroky:

  1. Ukončete aplikaci.  
  1. Otevřete Panel služeb součástí.  
    a. Do vyhledávacího pole na hlavním panelu, zadejte **services.msc**.  
    b. Ve výsledcích hledání poklikejte na **služby**.  
  1. Zastavit **synchronizace dat SQL** služby.
  1. Restartujte **synchronizace dat SQL** služby.  
  1. Znovu otevřete aplikaci.

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
-   [Stažení dokumentace k rozhraní REST API Synchronizace dat SQL](https://github.com/Microsoft/sql-server-samples/raw/master/samples/features/sql-data-sync/Data_Sync_Preview_REST_API.pdf?raw=true)

Další informace o službě SQL Database najdete v tématu:

-   [Přehled služby SQL Database](sql-database-technical-overview.md)
-   [Správa životního cyklu databáze](https://msdn.microsoft.com/library/jj907294.aspx)
