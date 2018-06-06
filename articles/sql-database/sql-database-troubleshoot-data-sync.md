---
title: Řešení potíží s synchronizaci dat Azure SQL (Preview) | Microsoft Docs
description: Zjistěte, jak řešení běžných problémů s synchronizaci dat SQL Azure (Preview).
services: sql-database
ms.date: 04/01/2018
ms.topic: conceptual
ms.service: sql-database
author: douglaslMS
ms.author: douglasl
manager: craigg
ms.custom: data-sync
ms.openlocfilehash: 8c3476a81c10c9e1754302da4ac5c703ce7375bc
ms.sourcegitcommit: 4f9fa86166b50e86cf089f31d85e16155b60559f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2018
ms.locfileid: "34757532"
---
# <a name="troubleshoot-issues-with-sql-data-sync-preview"></a>Řešení problémů s synchronizaci dat SQL (Preview)

Tento článek popisuje, jak řešení známých problémů s synchronizaci dat SQL Azure (Preview). Pokud dojde řešení problému, je tady uvedené.

Přehled synchronizaci dat SQL (Preview) najdete v tématu [synchronizaci dat mezi několika databází cloudu a místně s synchronizaci dat SQL Azure (Preview)](sql-database-sync-data.md).

## <a name="sync-issues"></a>Nezdařené synchronizace

### <a name="sync-fails-in-the-portal-ui-for-on-premises-databases-that-are-associated-with-the-client-agent"></a>Selhání synchronizace na portálu uživatelského rozhraní pro místní databáze, které jsou spojeny s klientským agentem

#### <a name="description-and-symptoms"></a>Popis a symptomy

Synchronizace se nezdaří na portál synchronizaci dat SQL (Preview) uživatelského rozhraní pro místní databáze, které jsou spojeny s agentem. V místním počítači, který je agent spuštěn zobrazí System.IO.IOException chyby v protokolu událostí. Chyby Řekněme, že na disku není dostatek místa.

#### <a name="resolution"></a>Řešení

Vytvořte další místo na jednotku, na kterém se nachází adresáři % TEMP %.

### <a name="my-sync-group-is-stuck-in-the-processing-state"></a>Moje skupina synchronizace je zablokované ve stavu zpracování

#### <a name="description-and-symptoms"></a>Popis a symptomy

Synchronizace skupiny v synchronizaci dat SQL (Preview) byla ve stavu zpracování po dlouhou dobu. Nebude reagovat na **Zastavit** příkazu a protokoly zobrazit žádné nové položky.

#### <a name="cause"></a>Příčina

Ve skupině synchronizace se zablokuje ve stavu zpracování může způsobit některé z následujících podmínek:

-   **Klientský agent je v režimu offline**. Ujistěte se, že agenta klienta je online a potom akci opakujte.

-   **Agenta klienta je odinstalovaný nebo chybí**. Pokud agenta klienta je odinstalovaný nebo jinak chybí:

    1. Odeberte soubor XML agenta z instalační složky synchronizaci dat SQL (Preview), pokud soubor existuje.
    2. Nainstalujte agenta na místním počítači (může to být stejné nebo do jiného počítače). Potom odešlete klíč agenta, který se generuje na portálu pro agenta, který se zobrazuje v režimu offline.

-   **Zastavení služby synchronizaci dat SQL**.

    1. V **spustit** nabídky, vyhledejte **služby**.
    2. Ve výsledcích hledání vyberte **služby**.
    3. Najít **synchronizaci dat SQL (Preview)** služby.
    4. Pokud je stav služby **Zastaveno**, klikněte pravým tlačítkem na název služby a pak vyberte **spustit**.

#### <a name="resolution"></a>Řešení

Pokud předchozí informace není vaší skupiny synchronizace přesunout mimo stav zpracování, Microsoft Support můžete obnovit stav synchronizace skupiny. Tak, aby měl váš stav synchronizace skupiny resetovat, v [fórum Azure SQL Database](https://social.msdn.microsoft.com/Forums/azure/home?forum=ssdsgetstarted), vytvořit příspěvek. V příspěvku zahrnují svoje ID předplatného a ID skupiny synchronizace pro skupinu, které je třeba obnovit. Microsoft Support pracovník bude reagovat na vaši post a bude informovat, když byl obnoven stav.

### <a name="i-see-erroneous-data-in-my-tables"></a>V tématu chybná data ve svých tabulek

#### <a name="description-and-symptoms"></a>Popis a symptomy

Pokud tabulky, které mají stejný název, ale jsou z jiné databázi schémata jsou součástí synchronizace, se po synchronizaci zobrazit chybná data v tabulkách.

#### <a name="cause"></a>Příčina

Zřizování synchronizaci dat SQL (Preview) používá stejné tabulky sledování pro tabulky, které mají stejný název, ale jsou v různých schématech. Z toho důvodu z obou tabulek se projeví ve stejné tabulce sledování. To způsobí, že změny chybné dat během synchronizace.

#### <a name="resolution"></a>Řešení

Ujistěte se, že názvy tabulek, které jsou součástí synchronizace se liší, i v případě, že tabulky patří do různých schémata v databázi.

### <a name="i-see-inconsistent-primary-key-data-after-a-successful-sync"></a>V tématu nekonzistentní data primárního klíče po úspěšné synchronizaci

#### <a name="description-and-symptoms"></a>Popis a symptomy

Synchronizace se hlásí jako úspěšné a v protokolu se zobrazuje žádné řádky se nezdařilo nebo bylo vynecháno, ale zjistíte, že primární klíče dat není konzistentní mezi databází ve skupině synchronizace.

#### <a name="cause"></a>Příčina

Tento výsledek je záměrné. Změny v jakékoli sloupec primárního klíče za následek nekonzistentní data v řádcích změnou primární klíč.

#### <a name="resolution"></a>Řešení

Nechcete-li tento problém, ujistěte se, že žádná data v sloupec primárního klíče se změnila.

Chcete-li tento problém vyřešit, po došlo k chybě, odstraníte řádek, který má nekonzistentní data z všechny koncové body ve skupině synchronizace. Potom znovu vložte řádek.

### <a name="i-see-a-significant-degradation-in-performance"></a>Najdete v části významnému zhoršení výkonu

#### <a name="description-and-symptoms"></a>Popis a symptomy

Výkon výrazně zhorší, pravděpodobně do bodu, kde nelze otevřít i rozhraní synchronizace Data.

#### <a name="cause"></a>Příčina

Nejpravděpodobnější příčinou je smyčku synchronizace. Smyčku synchronizace nastane, když skupiny synchronizace pomocí synchronizace A aktivuje synchronizaci pomocí synchronizace skupině B, potom aktivuje skupiny synchronizace pomocí synchronizace A. Skutečné situace může být složitější a ho mohou zahrnovat více než dvě skupiny pro synchronizaci ve smyčce. Je problém se cyklické spuštění synchronizace, která je způsobena navzájem překrývající se skupiny pro synchronizaci.

#### <a name="resolution"></a>Řešení

Nejlepší je prevence. Ujistěte se, nemáte žádné cyklické odkazy v synchronizaci skupin. Libovolný řádek, který se synchronizuje pouze jedna skupina synchronizace nelze synchronizovat pomocí jiné synchronizace skupiny.

### <a name="i-see-this-message-cannot-insert-the-value-null-into-the-column-column-column-does-not-allow-nulls-what-does-this-mean-and-how-can-i-fix-it"></a>Tato zpráva: "nelze vložit hodnoty NULL do sloupce \<sloupec\>. Sloupec nepovoluje hodnoty Null." Co to znamená, a jak jej lze odstranit? 
Tato chybová zpráva znamená, že mezi dvěma následující problémy došlo:
-  Tabulka nemá primární klíč. Chcete-li tento problém vyřešit, přidáte do všech tabulek, které jste se synchronizuje se primární klíč.
-  Neexistuje klauzule WHERE v příkazu CREATE INDEX. Tato podmínka nemůže pracovat s synchronizaci dat (Preview). Chcete-li tento problém vyřešit, odeberte klauzuli WHERE nebo ručně změnit všechny databáze. 
 
### <a name="how-does-data-sync-preview-handle-circular-references-that-is-when-the-same-data-is-synced-in-multiple-sync-groups-and-keeps-changing-as-a-result"></a>Jak pracovat synchronizaci dat (Preview). cyklické odkazy? To znamená, když stejná data je synchronizovaný v několika skupinách pro synchronizaci a udržuje v důsledku změny?
Synchronizaci dat (Preview) nemůže pracovat. cyklické odkazy. Ujistěte se, že je vyhnout. 

## <a name="client-agent-issues"></a>Problémy s klientem agenta

### <a name="the-client-agent-install-uninstall-or-repair-fails"></a>Klientský agent instalace, odinstalace nebo opravte selže

#### <a name="cause"></a>Příčina

Mnoho scénářů může dojít k této chybě. Pokud chcete určit konkrétní příčinu této chyby, prohlédněte si protokoly.

#### <a name="resolution"></a>Řešení

Pokud chcete vyhledat konkrétní příčinu selhání, generovat a v protokolech Instalační služby systému Windows. Můžete zapnout protokolování na příkazovém řádku. Například pokud stažený soubor AgentServiceSetup.msi LocalAgentHost.msi, generovat a zkontrolujte soubory protokolů pomocí následujících příkazových řádků:

-   Pro instalaci: `msiexec.exe /i SQLDataSyncAgent-Preview-ENU.msi /l\*v LocalAgentSetup.InstallLog`
-   Pro odinstaluje: `msiexec.exe /x SQLDataSyncAgent-se-ENU.msi /l\*v LocalAgentSetup.InstallLog`

Můžete také zapnout protokolování pro všechny instalace, které se provádí pomocí Instalační služby systému Windows. Článek znalostní báze Microsoft Knowledge Base [povolení protokolování Instalační služby systému Windows](https://support.microsoft.com/help/223300/how-to-enable-windows-installer-logging) poskytuje řešení jedním kliknutím zapnout protokolování pro Instalační služby systému Windows. Nabízí taky umístění protokolů.

### <a name="my-client-agent-doesnt-work"></a>Moje Klientský agent nebude fungovat.

#### <a name="description-and-symptoms"></a>Popis a symptomy

Při pokusu o použití agenta klienta získáte následujících zpráv:

"Synchronizace se nezdařila s výjimkou, ke které došlo k chybě při pokusu o deserializaci www.microsoft.com/.../05:GetBatchInfoResult parametr. Informace naleznete v části ve vlastnosti InnerException."

"Zpráva o vnitřní výjimce: typ 'Microsoft.Synchronization.ChangeBatch' je neplatný typ kolekce, protože nemá výchozí konstruktor."

#### <a name="cause"></a>Příčina

Jedná se o známý problém s instalací synchronizaci dat SQL (Preview). Nejpravděpodobnější příčinou této zprávy je jedním z následujících akcí:

-   Se systémem Windows 8 Developer Preview.
-   Máte nainstalované rozhraní .NET Framework 4.5.

#### <a name="resolution"></a>Řešení

Ujistěte se, že instalujete agenta klienta na počítači se systémem Windows 8 Developer Preview, a že není nainstalováno rozhraní .NET Framework 4.5.

### <a name="my-client-agent-doesnt-work-after-i-cancel-the-uninstall"></a>Moje Klientský agent nefunguje po zruším odinstalace

#### <a name="description-and-symptoms"></a>Popis a symptomy

Klientský agent nebude fungovat, i po zrušení jeho odinstalaci.

#### <a name="cause"></a>Příčina

K tomu dochází, protože klientský agent pro synchronizaci dat SQL (Preview) nepodporuje ukládat přihlašovací údaje.

#### <a name="resolution"></a>Řešení

Můžete vyzkoušet tyto dvě řešení:

-   Pomocí services.msc znovu zadat přihlašovací údaje pro agenta klienta.
-   Odinstalujte tohoto agenta klienta a pak nainstalujte novou. Stáhněte a nainstalujte nejnovější verzi agenta klienta z [Download Center](http://go.microsoft.com/fwlink/?linkid=221479).

### <a name="my-database-isnt-listed-in-the-agent-list"></a>Databáze není uveden v seznamu agenta

#### <a name="description-and-symptoms"></a>Popis a symptomy

Při pokusu o přidání existující databázi systému SQL Server do skupiny synchronizace databáze se nezobrazí v seznamu agentů.

#### <a name="cause"></a>Příčina

Tento problém může způsobit tyto scénáře:

-   Klientský agent a synchronizace skupiny jsou v různých datových centrech.
-   Klientský agent seznam databází není aktuální.

#### <a name="resolution"></a>Řešení

Řešení závisí na příčinu.

- **Klientský agent a synchronizace skupiny jsou v různých datových centrech**

    Agenta klienta a skupiny synchronizace musí být ve stejném datovém centru. Chcete-li nastavit tuto možnost, máte dvě možnosti:

    -   Vytvořte nový agent v datovém centru, kde je umístěna skupina synchronizace. Pak zaregistrujte databáze se tohoto agenta.
    -   Odstraňte aktuální synchronizace skupiny. Pak znovu vytvořte skupiny synchronizace v datovém centru, kde se nachází agent.

- **Klientský agent seznam databází není aktuální**

    Zastavte a potom restartujte službu agenta klienta.

    Místní agent stáhne seznam přidružených databázích pouze u prvního odeslání klíč agenta. Seznam přidružených databází na následné agenta klíče odesílání se nestahuje ho. Databáze, které jsou registrovány během přesunutí agenta nejsou zobrazena v původní instance agenta.

### <a name="client-agent-doesnt-start-error-1069"></a>Agent klienta nelze spustit (chyba. 1069)

#### <a name="description-and-symptoms"></a>Popis a symptomy

Zjistíte, že agent není spuštěn v počítači, který je hostitelem systému SQL Server. Při pokusu o ruční spuštění agenta, se zobrazí dialogové okno se zobrazí se zpráva "Chyba. 1069: službu se nepodařilo spustit z důvodu selhání přihlášení."

![Dialogové okno chyby. 1069 synchronizace dat](media/sql-database-troubleshoot-data-sync/sync-error-1069.png)

#### <a name="cause"></a>Příčina

Pravděpodobnou příčinou této chyby je, že vzhledem k tomu, že jste vytvořili agenta a agenta heslo došlo ke změně hesla na místním serveru.

#### <a name="resolution"></a>Řešení

Aktualizace agenta hesla pro vaše aktuální heslo serveru:

1. Najděte agenta klienta Preview service synchronizaci dat SQL (Preview).  
    a. Vyberte **spustit**.  
    b. Do vyhledávacího pole zadejte **services.msc**.  
    c. Ve výsledcích hledání vyberte **služby**.  
    d. V **služby** okno, přejděte k položce pro **synchronizaci dat SQL (Preview) agenta Preview**.  
2. Klikněte pravým tlačítkem na **synchronizaci dat SQL (Preview) agenta Preview**a potom vyberte **Zastavit**.
3. Klikněte pravým tlačítkem na **synchronizaci dat SQL (Preview) agenta Preview**a potom vyberte **vlastnosti**.
4. Na **synchronizaci dat SQL (Preview) agenta Náhled Vlastnosti**, vyberte **přihlásit** kartě.
5. V **heslo** zadejte heslo.
6. V **Potvrdit heslo** pole, zadejte znovu heslo.
7. Vyberte **Apply** (Použít) a pak vyberte **OK**.
8. V **služby** okna, klikněte pravým tlačítkem myši **Preview agenta synchronizaci dat SQL (Preview)** služby a potom klikněte na **spustit**.
9. Zavřít **služby** okno.

### <a name="i-cant-submit-the-agent-key"></a>Mohu nelze odeslat klíč agenta

#### <a name="description-and-symptoms"></a>Popis a symptomy

Po vytvoření nebo znovu vytvořte klíč pro agenta, pokusíte odeslat klíč prostřednictvím SqlAzureDataSyncAgent aplikace. Odesílání se nepodaří dokončit.

![Dialogové okno chyby synchronizace – klíč agenta nelze odeslat.](media/sql-database-troubleshoot-data-sync/sync-error-cant-submit-agent-key.png)

Než budete pokračovat, zkontrolujte následující podmínky:

-   Je spuštěna služba systému Windows pro synchronizaci dat SQL (Preview).  
-   Účet služby pro službu systému Windows verze Preview synchronizaci dat SQL (Preview) má přístup k síti.    
-   Odchozí port 1433 je otevřen v místní bráně firewall pravidla.
-   Místní IP adresa se přidá k serveru nebo databáze pravidlo brány firewall pro databázi synchronizace metadat.

#### <a name="cause"></a>Příčina

Klíč agenta jednoznačně identifikuje každého agenta na místní. Klíč musí splňovat dvě podmínky:

-   Klíč agenta klienta na serveru pro synchronizaci dat SQL (Preview) a místní počítač musí být identický.
-   Klíč agenta klienta lze použít pouze jednou.

#### <a name="resolution"></a>Řešení

Pokud agenta není funkční, je to, protože jedna nebo obě tyto podmínky nejsou splněné. Pokud chcete získat agenta znovu fungovat:

1. Vygenerujte nový klíč.
2. Použít nový klíč k agentovi.

Chcete-li použít nový klíč k agentovi:

1. V Průzkumníku souborů přejděte do adresáře instalace agenta. Výchozí instalační adresář je C:\\Program Files (x86)\\synchronizaci dat Microsoft SQL.
2. Dvakrát klikněte na podadresáře bin.
3. Otevřete aplikaci SqlAzureDataSyncAgent.
4. Vyberte **odeslání klíč agenta**.
5. V poskytnutém prostoru vložte klíč ze schránky.
6. Vyberte **OK**.
7. Ukončete program.

### <a name="the-client-agent-cant-be-deleted-from-the-portal-if-its-associated-on-premises-database-is-unreachable"></a>Klientský agent nelze odstranit z portálu, pokud jeho přidružené místní databázi nedostupný

#### <a name="description-and-symptoms"></a>Popis a symptomy

Místní koncový bod (tj. databáze), je zaregistrován k synchronizaci dat SQL (Preview) Klientský agent přestane být nedostupný, nelze odstranit agenta klienta.

#### <a name="cause"></a>Příčina

Místní agent nelze odstranit, protože je nedostupný databáze pořád bude registrovaný s agentem. Při pokusu o odstranění agenta proces odstraňování se pokusí připojit databázi, která se nezdaří.

#### <a name="resolution"></a>Řešení

Použití "Vynutit odstranění" odstranit databázi nedostupný.

> [!NOTE]
> Pokud synchronizaci metadat tabulky zůstaly po "Vynutit odstranění", použijte deprovisioningutil.exe k jejich vyčištění.

### <a name="local-sync-agent-app-cant-connect-to-the-local-sync-service"></a>Místní aplikace agenta synchronizace se nemůže připojit k místní synchronizační služby

#### <a name="resolution"></a>Řešení

Vyzkoušejte následující kroky:

1. Ukončete aplikaci.  
2. Otevření panelu součást služby.  
    a. Do vyhledávacího pole na hlavním panelu, zadejte **services.msc**.  
    b. Ve výsledcích hledání klikněte dvakrát na **služby**.  
3. Zastavit **synchronizaci dat SQL (Preview) Preview** služby.
4. Restartujte **synchronizaci dat SQL (Preview) Preview** služby.  
5. Aplikaci znovu otevřete.

## <a name="setup-and-maintenance-issues"></a>Problémy s instalací a údržby

### <a name="i-get-a-disk-out-of-space-message"></a>Zobrazí se zpráva "nedostatek místa na disku"

#### <a name="cause"></a>Příčina

Pokud potřebujete odstranit zanechání nepotřebných souborů, pravděpodobně se zobrazí zpráva "nedostatek místa na disku". To může být způsoben antivirovým softwarem nebo soubory jsou otevřené, kdy operace delete.

#### <a name="resolution"></a>Řešení

Ručně odstraňte synchronizace souborů, které jsou ve složce % temp % (`del \*sync\* /s`). Potom odstraňte podadresáře ve složce % temp %.

> [!IMPORTANT]
> Neodstraňujte žádné soubory, když probíhá synchronizace.

### <a name="i-cant-delete-my-sync-group"></a>Nelze odstranit mé skupině synchronizace

#### <a name="description-and-symptoms"></a>Popis a symptomy

Pokus o odstranění skupiny synchronizace se nezdaří.

#### <a name="causes"></a>Způsobí, že

Některé z následujících scénářů může způsobit selhání odstranění skupiny synchronizace:

-   Agenta klienta je offline.
-   Agenta klienta je odinstalovaný nebo chybí. 
-   Databáze je offline. 
-   Skupiny synchronizace je zřizování nebo synchronizaci. 

#### <a name="resolution"></a>Řešení

Chcete-li vyřešit nepodařilo se odstranit skupinu synchronizace:

-   Zkontrolujte, zda je klientský agent online a potom akci opakujte.
-   Pokud agenta klienta je odinstalovaný nebo jinak chybí:  
    a. Odeberte soubor XML agenta z instalační složky synchronizaci dat SQL (Preview), pokud soubor existuje.  
    b. Nainstalujte agenta na místním počítači (může to být stejné nebo do jiného počítače). Potom odešlete klíč agenta, který se generuje na portálu pro agenta, který se zobrazuje v režimu offline.
-   Ujistěte se, zda je spuštěna služba synchronizaci dat SQL (Preview):  
    a. V **spustit** nabídky, vyhledejte **služby**.  
    b. Ve výsledcích hledání vyberte **služby**.  
    c. Najít **synchronizaci dat SQL (Preview) Preview** služby.  
    d. Pokud je stav služby **Zastaveno**, klikněte pravým tlačítkem na název služby a pak vyberte **spustit**.
-   Zkontrolujte, zda jsou všechny online databází SQL a databáze systému SQL Server.
-   Počkejte na dokončení procesu zřizování nebo synchronizace a poté opakujte odstranění skupiny synchronizace.

### <a name="i-cant-unregister-an-on-premises-sql-server-database"></a>I nelze zrušit registraci místní databázi systému SQL Server

#### <a name="cause"></a>Příčina

S největší pravděpodobností pokoušíte se zrušit registraci databázi, která již byla odstraněna.

#### <a name="resolution"></a>Řešení

Chcete-li zrušit registraci místní databázi systému SQL Server, vyberte databázi a pak vyberte **Vynutit odstranění**.

Tato operace selže-li odebrat databázi ze skupiny synchronizace:

1. Zastavte a znovu spusťte službu Hostitel agenta klienta:  
    a. Vyberte **spustit** nabídky.  
    b. Do vyhledávacího pole zadejte **services.msc**.  
    c. V **programy** části hledání výsledků podokně dvakrát klikněte na **služby**.  
    d. Klikněte pravým tlačítkem myši **synchronizaci dat SQL (Preview)** služby.  
    e. Pokud je služba spuštěná, zastavte ji.  
    f. Klikněte pravým tlačítkem na službu a pak vyberte **spustit**.  
    g. Zkontrolujte, zda je databáze pořád zaregistrovaný. Pokud je již zaregistrován, jste hotovi. Jinak pokračujte dalším krokem.
2. Otevřete aplikaci agent klienta (SqlAzureDataSyncAgent).
3. Vyberte **upravit pověření**a pak zadejte přihlašovací údaje pro databázi.
4. Pokračujte zrušení registrace.

### <a name="i-dont-have-sufficient-privileges-to-start-system-services"></a>Nemáte dostatečná oprávnění pro spouštění systémových služeb

#### <a name="cause"></a>Příčina

K této chybě dojde ve dvou situacích:
-   Uživatelské jméno a heslo jsou nesprávné.
-   Zadaný uživatelský účet nemá dostatečná oprávnění přihlásit jako službu.

#### <a name="resolution"></a>Řešení

Udělit protokolu na jako-service pověření pro uživatelský účet:

1. Přejděte na **spustit** > **ovládací panely** > **nástroje pro správu** > **místních zásad zabezpečení**  >  **Místní zásady** > **uživatelských práv správu**.
2. Vyberte **přihlásit jako službu**.
3. V **vlastnosti** dialogové okno pole, přidejte uživatelský účet.
4. Vyberte **Apply** (Použít) a pak vyberte **OK**.
5. Zavřete všechna okna.

### <a name="a-database-has-an-out-of-date-status"></a>Databáze má "Zastaralé" stav

#### <a name="cause"></a>Příčina

Synchronizaci dat SQL (Preview) odebere databází, které byly offline ze služby pro 45 dnů a víc (jak je počítáno od okamžiku, kdy databázi přešel do režimu offline). Pokud databáze je v režimu offline pro 45 dnů nebo déle a pak přejde do režimu online, je její stav **zastaralé**.

#### <a name="resolution"></a>Řešení

Se můžete vyhnout **zastaralé** stavu tím, že zajistí, že žádná z databází přechodu do offline režimu pro 45 dnů nebo déle.

Pokud je stav do databáze **zastaralé**:

1. Odebrat databázi, která má **zastaralé** stav ze synchronizace skupiny.
2. Přidejte databázi zpět do skupiny synchronizace v.

> [!WARNING]
> Přijdete o všechny změny provedené tuto databázi v době, kdy byl v režimu offline.

### <a name="a-sync-group-has-an-out-of-date-status"></a>Synchronizace skupiny má "Zastaralé" stav

#### <a name="cause"></a>Příčina

Pokud jeden nebo více změn se nepodaří použít pro dobu uchování celou 45 dní, se může stát zastaralé synchronizace skupiny.

#### <a name="resolution"></a>Řešení

Aby nedošlo **zastaralé** stav synchronizace skupiny, podívejte se na výsledky úlohy synchronizace v prohlížeči historie v pravidelných intervalech. Prozkoumat a vyřešit všechny změny, které se nepodařilo použít.

Pokud je stav skupiny synchronizace **zastaralé**, odstraňte skupinu synchronizace a poté je znovu vytvořte.

### <a name="a-sync-group-cant-be-deleted-within-three-minutes-of-uninstalling-or-stopping-the-agent"></a>Nelze odstranit skupinu synchronizace do tří minut po odinstalaci nebo zastavení agenta

#### <a name="description-and-symptoms"></a>Popis a symptomy

Nelze odstranit skupinu synchronizace do tří minut po odinstalaci nebo zastavení přidružené Klientský agent pro synchronizaci dat SQL (Preview).

#### <a name="resolution"></a>Řešení

1. Odebrání skupiny pro synchronizaci agenty přidružené synchronizace jsou online (doporučeno).
2. Pokud agenta je offline, ale je nainstalován, převeďte ho do online režimu v místním počítači. Počkejte stav agenta se objeví jako **Online** na portálu pro synchronizaci dat SQL (Preview). Potom odeberte synchronizace skupiny.
3. Pokud agenta je offline, protože byla odinstalována:  
    a.  Odeberte soubor XML agenta z instalační složky synchronizaci dat SQL (Preview), pokud soubor existuje.  
    b.  Nainstalujte agenta na místním počítači (může to být stejné nebo do jiného počítače). Potom odešlete klíč agenta, který se generuje na portálu pro agenta, který se zobrazuje v režimu offline.  
    c. Pokuste se odstranit skupinu synchronizace.

### <a name="what-happens-when-i-restore-a-lost-or-corrupted-database"></a>Co se stane, když obnovím ztracené nebo poškozená databáze?

Pokud ke ztrátě nebo poškozená databáze obnovit ze zálohy, může být nonconvergence dat ve skupině synchronizace, ke které databáze patří.

## <a name="next-steps"></a>Další postup
Další informace o synchronizaci dat SQL (Preview) najdete v tématu:

-   [Synchronizaci dat mezi několika databází cloudu a místně s synchronizaci dat SQL Azure (Preview)](sql-database-sync-data.md)  
-   [Nastavit synchronizaci dat SQL Azure (Preview)](sql-database-get-started-sql-data-sync.md)  
-   [Osvědčené postupy pro synchronizaci dat SQL Azure (Preview)](sql-database-best-practices-data-sync.md)  
-   [Synchronizaci dat Azure SQL (Preview) monitorování s analýzy protokolů](sql-database-sync-monitor-oms.md)  
-   Dokončete příklady prostředí PowerShell, které ukazují, jak nakonfigurovat synchronizaci dat SQL (Preview):  
    -   [Synchronizace mezi několika databázemi SQL Azure pomocí PowerShellu](scripts/sql-database-sync-data-between-sql-databases.md)  
    -   [Použití PowerShellu k synchronizaci mezi službou Azure SQL Database a místní databází SQL Serveru](scripts/sql-database-sync-data-between-azure-onprem.md)  
-   [Stáhněte si dokumentaci rozhraní API REST synchronizaci dat SQL (Preview)](https://github.com/Microsoft/sql-server-samples/raw/master/samples/features/sql-data-sync/Data_Sync_Preview_REST_API.pdf?raw=true)

Další informace o databázi SQL najdete v tématu:

-   [Přehled služby SQL Database](sql-database-technical-overview.md)
-   [Správa životního cyklu databáze](https://msdn.microsoft.com/library/jj907294.aspx)
