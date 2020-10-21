---
title: 'Aplikace SaaS: geograficky redundantní zálohy pro zotavení po havárii'
description: Naučte se používat Azure SQL Database geograficky redundantní zálohy k obnovení víceklientské aplikace SaaS v případě výpadku.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/14/2019
ms.openlocfilehash: 620a5dad7966347667e0a0a50eb30d562ab700b2
ms.sourcegitcommit: 03713bf705301e7f567010714beb236e7c8cee6f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/21/2020
ms.locfileid: "92330100"
---
# <a name="use-geo-restore-to-recover-a-multitenant-saas-application-from-database-backups"></a>Použití geografického obnovení k obnovení víceklientské aplikace SaaS ze záloh databáze
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

V tomto kurzu se seznámíte s úplným scénářem zotavení po havárii pro víceklientské aplikace SaaS, která je implementovaná u databáze na model tenanta. [Geografické obnovení](recovery-using-backups.md) použijete k obnovení katalogu a databází tenantů z automatického udržování geograficky redundantních záloh do alternativní oblasti obnovení. Po vyřešení výpadku použijete [geografickou replikaci](active-geo-replication-overview.md) k vrácení změněných databází do jejich původní oblasti.

![Diagram znázorňuje původní oblast a oblasti obnovení, které mají aplikaci, katalog, původní nebo zrcadlené image serverů a fondů, automatické zálohování do úložiště, s oblastí obnovení, která přijímá geografickou replikaci zálohy a má server a fond pro nové klienty.](./media/saas-dbpertenant-dr-geo-restore/geo-restore-architecture.png)

Geografické obnovení je řešení zotavení po havárii s nejnižšími náklady pro Azure SQL Database. Obnovení z geograficky redundantní zálohy ale může způsobit ztrátu dat o jednu hodinu. Může to trvat značnou dobu v závislosti na velikosti každé databáze. 

> [!NOTE]
> Místo geografického obnovení obnovte aplikace s nejnižšími možnými možnostmi RPO a RTO pomocí geografické replikace.

V tomto kurzu se seznámíte s pracovními postupy pro obnovení i revracení. Získáte informace o těchto tématech:
> [!div class="checklist"]
> 
> * Synchronizovat informace o konfiguraci databáze a elastického fondu do katalogu tenantů.
> * Nastavte prostředí zrcadlení imagí v oblasti obnovení, která zahrnuje aplikace, servery a fondy.   
> * Obnovte katalog a databáze tenantů pomocí geografického obnovení.
> * Geografickou replikaci můžete použít k vrácení katalogu tenanta a změně databází tenanta po vyřešení výpadku.
> * Aktualizujte katalog, protože se každá databáze obnovila (nebo se převedla), aby sledovala aktuální umístění aktivní kopie databáze každého klienta.
> * Zajistěte, aby byla databáze aplikace a tenanta stále společně umístěná ve stejné oblasti Azure, aby se snížila latence. 
 

Než začnete s tímto kurzem, proveďte následující požadavky:
* Nasaďte aplikaci Wingtip Tickets SaaS Database na klienta. Pokud ho chcete nasadit za méně než pět minut, přečtěte si téma [nasazení a prozkoumání aplikace Wingtip Tickets SaaS Database na klienta](saas-dbpertenant-get-started-deploy.md). 
* Nainstalujte Azure PowerShell. Podrobnosti najdete v tématu [Začínáme s Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

## <a name="introduction-to-the-geo-restore-recovery-pattern"></a>Úvod do vzoru obnovení geografického obnovení

Zotavení po havárii (DR) je důležité pro mnoho aplikací, ať už z důvodů dodržování předpisů nebo provozní kontinuity. Pokud dojde k dlouhodobému výpadku služby, může být dobře připravený plán DR minimalizován při narušení podniku. Plán zotavení po havárii založený na geografickém obnovení musí dosáhnout několika cílů:
 * Rezervujte veškerou potřebnou kapacitu ve zvolené oblasti obnovení co nejrychleji, abyste měli jistotu, že je k dispozici pro obnovení databází tenantů.
 * Navažte prostředí pro obnovení zrcadlového obrázku, které odráží původní fond a konfiguraci databáze. 
 * Povolí zrušení procesu obnovení v polovině letu, pokud se původní oblast vrátí zpět do režimu online.
 * Umožněte rychlé zřizování tenanta, takže se nový tenant může restartovat co nejdříve.
 * Optimalizujte tak, aby se klienti obnovili v pořadí podle priority.
 * Optimalizujte tak, aby co nejdříve prováděli klienty, a to tak, že provedete kroky paralelně, kde to bude praktické.
 * Je odolná vůči selhání, restartně a idempotentní.
 * V případě, že se výpadek vyřeší, repatriate databáze do jejich původní oblasti s minimálním dopadem na klienty.  

> [!NOTE]
> Aplikace se obnoví do spárované oblasti v oblasti, ve které je aplikace nasazená. Další informace najdete v tématu [spárované oblasti Azure](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).   

V tomto kurzu se používá funkce Azure SQL Database a platforma Azure k řešení těchto problémů:

* [Azure Resource Manager šablony](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-create-first-template), aby bylo možné co nejrychleji rezervovat veškerou potřebnou kapacitu. Šablony Azure Resource Manager slouží ke zřízení zrcadlové image původních serverů a elastických fondů v oblasti obnovení. Pro zřizování nových tenantů se taky vytvoří samostatný server a fond.
* [Elastic Database klientské knihovny](elastic-database-client-library.md) (EDCL) pro vytvoření a údržbu katalogu databáze klienta. Rozšířený katalog obsahuje pravidelně aktualizované informace o fondu a konfiguraci databáze.
* [Horizontálních oddílů funkce obnovení](elastic-database-recovery-manager.md) EDCL pro správu záznamů umístění databáze v katalogu během obnovování a vracení.  
* [Geografické obnovení](../../key-vault/general/disaster-recovery-guidance.md), obnovení katalogu a databází klientů z automatického udržování geograficky redundantních záloh. 
* [Operace asynchronního obnovení](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations), které jsou odesílány v pořadí priority tenanta, jsou zařazeny do fronty pro každý fond systémem a zpracovány v dávkách, takže fond není přetížen. Tyto operace můžete v případě potřeby zrušit před nebo během provádění.   
* [Geografická replikace](active-geo-replication-overview.md), která po výpadku repatria databáze do původní oblasti. Při použití geografické replikace nedochází k žádné ztrátě dat a minimálnímu dopadu na tenanta.
* [Aliasy DNS serveru SQL](../../sql-database/dns-alias-overview.md), aby se proces synchronizace katalogu mohl připojit k aktivnímu katalogu bez ohledu na jeho umístění.  

## <a name="get-the-disaster-recovery-scripts"></a>Získat skripty pro zotavení po havárii

Skripty DR použité v tomto kurzu jsou k dispozici v [úložišti GitHub lístky SaaS Database per tenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant). Projděte si [Obecné pokyny](saas-tenancy-wingtip-app-guidance-tips.md) ke stažení a odblokování skriptů pro správu lístků Wingtip Tickets.

> [!IMPORTANT]
> Stejně jako všechny skripty pro správu lístků Wingtip mají skripty DR vzorek kvality a nemusejí být používány v produkčním prostředí.

## <a name="review-the-healthy-state-of-the-application"></a>Zkontrolujte dobrý stav aplikace.
Než začnete s procesem obnovení, zkontrolujte normální dobrý stav aplikace.

1. Ve webovém prohlížeči otevřete centrum událostí Wingtip lístky ( http://events.wingtip-dpt.&lt ; User &gt; . trafficmanager.NET, nahraďte &lt; uživatele &gt; hodnotou uživatele vašeho nasazení).
    
   Posuňte se do dolní části stránky a Všimněte si názvu a umístění serveru katalogu v zápatí. Umístění je oblast, ve které jste nasadili aplikaci.    

   > [!TIP]
   > Pokud chcete zobrazení zvětšit, najeďte myší na jeho umístění.

   ![Stav v pořádku v centru událostí v původní oblasti](./media/saas-dbpertenant-dr-geo-restore/events-hub-original-region.png)

2. Vyberte tenanta společnosti Contoso hala a otevřete jeho stránku události.

   V zápatí si všimněte názvu serveru klienta. Umístění je stejné jako umístění serveru katalogu.

   ![Původní oblast společné místnosti společnosti Contoso](./media/saas-dbpertenant-dr-geo-restore/contoso-original-location.png)   

3. V [Azure Portal](https://portal.azure.com)zkontrolujte a otevřete skupinu prostředků, ve které jste aplikaci nasadili.

   Všimněte si prostředků a oblasti, ve které jsou nasazené součásti služby App Service a SQL Database.

## <a name="sync-the-tenant-configuration-into-the-catalog"></a>Synchronizace konfigurace tenanta do katalogu

V této úloze zahájíte proces synchronizace konfigurace serverů, elastických fondů a databází do katalogu tenantů. Tyto informace se používají později ke konfiguraci prostředí zrcadlení imagí v oblasti obnovení.

> [!IMPORTANT]
> Pro zjednodušení se v těchto ukázkách implementují proces synchronizace a další dlouhodobé procesy obnovení a převracení, jako místní úlohy PowerShellu nebo relace spouštěné pod přihlášením uživatele klienta. Tokeny ověřování vystavené při vypršení platnosti přihlášení po několika hodinách a úlohy se pak nezdaří. V produkčním scénáři by dlouhotrvající procesy měly být implementovány jako spolehlivé služby Azure v nějakém typu, a to za provozu v instančním objektu. Další informace najdete v tématu [použití Azure PowerShell k vytvoření instančního objektu s certifikátem](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authenticate-service-principal). 

1. V prostředí PowerShell ISE otevřete soubor Modules\UserConfig.psm1. ..\Learning. Nahraďte `<resourcegroup>` a `<user>` na řádcích 10 a 11 hodnotou použitou při nasazení aplikace. Uložte soubor.

2. V prostředí PowerShell ISE otevřete skript. ..\Learning Modules\Business kontinuita a Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1 skriptu pro zotavení po havárii.

    V tomto kurzu spustíte všechny scénáře v tomto skriptu PowerShellu, takže tento soubor zůstane otevřený.

3. Nastavte následující:

    $DemoScenario = 1: spustí úlohu na pozadí, která synchronizuje informace o konfiguraci tenanta serveru a fondu do katalogu.

4. Pokud chcete skript pro synchronizaci spustit, vyberte F5. 

    Tyto informace se používají později, aby se zajistilo, že obnovení vytvoří zrcadlový obraz serverů, fondů a databází v oblasti obnovení.  

    ![Proces synchronizace](./media/saas-dbpertenant-dr-geo-restore/sync-process.png)

Nechte okno PowerShellu spuštěné na pozadí a pokračujte zbývající částí tohoto kurzu.

> [!NOTE]
> Proces synchronizace se k katalogu připojí prostřednictvím aliasu DNS. Alias se během obnovování mění a vrátí, aby odkazoval na aktivní katalog. Proces synchronizace udržuje katalog v aktuálním stavu pomocí všech změn konfigurace databáze nebo fondu provedené v oblasti obnovení. Během navracení jsou tyto změny aplikovány na ekvivalentní prostředky v původní oblasti.

## <a name="geo-restore-recovery-process-overview"></a>Přehled procesu obnově geografického obnovení

Proces obnovení geografického obnovení nasadí aplikaci a obnoví databáze ze záloh do oblasti obnovení.

Proces obnovení provádí následující akce:

1. Zakáže koncový bod Azure Traffic Manager pro webovou aplikaci v původní oblasti. Zakázáním koncového bodu zabráníte uživatelům v připojení k aplikaci v neplatném stavu, aby byla původní oblast během obnovování online.

2. Zřídí Server katalogu obnovení v oblasti obnovení, geograficky obnoví databázi katalogu a aktualizuje activecatalog alias tak, aby odkazoval na obnovený server katalogu. Změna aliasu katalogu zajistí, že se proces synchronizace katalogu vždy synchronizuje do aktivního katalogu.

3. Označí všechny existující klienty v katalogu obnovení jako offline, aby se zabránilo přístupu k databázím tenanta před jejich obnovením.

4. Zřídí instanci aplikace v oblasti obnovení a nakonfiguruje ji pro použití obnoveného katalogu v této oblasti. Pro zajištění minimální latence je ukázková aplikace navržená tak, aby se vždy připojovala k databázi tenanta ve stejné oblasti.

5. Zřídí server a elastický fond, ve kterém se zřídí noví klienti. Vytváření těchto prostředků zajistí, že zřizování nových klientů nekoliduje s obnovením stávajících klientů.

6. Aktualizuje nový alias tenanta tak, aby odkazoval na server pro nové databáze tenantů v oblasti obnovení. Změnou tohoto aliasu zajistíte, aby se databáze pro všechny nové klienty zřídily v oblasti obnovení.
        
7. Zřídí servery a elastické fondy v oblasti obnovení pro obnovení databází tenantů. Tyto servery a fondy jsou zrcadlovou imagí konfigurace v původní oblasti. Zřizování fondů předem rezervuje kapacitu potřebnou k obnovení všech databází.

    Výpadek v oblasti může znamenat výrazný tlak na prostředky, které jsou k dispozici v spárované oblasti. Pokud se vám spoléháte na geografickou obnovu pro DR, doporučí se jim rychlé zachovávání prostředků. Pokud je důležité, aby se aplikace obnovila v konkrétní oblasti, zvažte geografickou replikaci. 

8. Povolí koncový bod Traffic Manager pro webovou aplikaci v oblasti obnovení. Povolením tohoto koncového bodu umožníte aplikaci zřídit nové klienty. V této fázi jsou stávající klienti stále v režimu offline.

9. Odesílá dávky žádostí o obnovení databází v pořadí podle priority. 

    * Dávky jsou uspořádány tak, aby se databáze souběžně obnovily napříč všemi fondy.  

    * Žádosti o obnovení se odesílají asynchronně, aby se odeslaly rychle a zařadily do fronty pro spuštění v každém fondu.

    * Vzhledem k tomu, že požadavky na obnovení jsou zpracovávány paralelně napříč všemi fondy, je lepší distribuovat důležité klienty napříč mnoha fondy. 

10. Monitoruje službu, aby zjistila, kdy se databáze obnovily. Po obnovení databáze tenanta je tato databáze označena online v katalogu a bude zaznamenána rowversion suma pro databázi tenanta. 

    * K databázím tenanta může aplikace přicházet ihned po označení online v katalogu.

    * Součet hodnot rowversion v databázi tenanta je uložen v katalogu. Tato suma funguje jako otisk prstu, který umožňuje procesu repatriace zjistit, jestli se databáze aktualizovala v oblasti obnovení.       

## <a name="run-the-recovery-script"></a>Spuštění skriptu pro obnovení

> [!IMPORTANT]
> Tento kurz obnoví databáze z geograficky redundantních záloh. I když jsou tyto zálohy obvykle k dispozici do 10 minut, může trvat až hodinu. Skript se pozastaví, dokud nebudou k dispozici.

Představte si výpadky v oblasti, ve které je aplikace nasazená, a spusťte skript pro obnovení:

1. V ISE PowerShellu ve skriptu. ..\Learning Modules\Business kontinuita a Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1 pro zotavení po havárii nastavte následující hodnotu:

    $DemoScenario = 2: Obnovte aplikaci do oblasti obnovení obnovením z geograficky redundantního zálohování.

2. Pokud chcete skript spustit, vyberte F5.  

    * Skript se otevře v novém okně PowerShellu a potom spustí sadu úloh prostředí PowerShell, které běží paralelně. Tyto úlohy obnoví z oblasti obnovení servery, fondy a databáze.

    * Oblast obnovení je spárovaná oblast přidružená k oblasti Azure, ve které jste aplikaci nasadili. Další informace najdete v tématu [spárované oblasti Azure](https://docs.microsoft.com/azure/best-practices-availability-paired-regions). 

3. Monitorujte stav procesu obnovení v okně PowerShellu.

    ![Snímek obrazovky, který zobrazuje okno prostředí PowerShell, kde můžete monitorovat stav procesu obnovení.](./media/saas-dbpertenant-dr-geo-restore/dr-in-progress.png)

> [!NOTE]
> Pokud chcete prozkoumat kód pro úlohy obnovení, zkontrolujte skripty PowerShellu ve složce. ..\Learning Modules\Business kontinuita a zotavení po havárii Recovery\DR-RestoreFromBackup\RecoveryJobs.

## <a name="review-the-application-state-during-recovery"></a>Kontrola stavu aplikace během obnovování
I když je koncový bod aplikace v Traffic Manager zakázán, aplikace není k dispozici. Katalog se obnoví a všichni klienti jsou označeni jako offline. Koncový bod aplikace v oblasti obnovení je pak povolen a aplikace je znovu online. I když je aplikace k dispozici, klienti se v centru událostí zobrazí v režimu offline, dokud se neobnoví jejich databáze. Je důležité navrhnout aplikaci pro zpracování offline databází klientů.

* Po obnovení databáze katalogu, ale ještě před tím, než se klienti vrátí do režimu online, aktualizujte centrum událostí Wingtip lístky ve webovém prohlížeči.

  * V zápatí si všimněte, že název serveru katalogu má teď příponu obnovení a je umístěný v oblasti obnovení.

  * Všimněte si, že klienti, kteří ještě nejsou obnoveni, jsou označeni jako offline a nelze je vybrat.   
 
    ![Proces obnovení](./media/saas-dbpertenant-dr-geo-restore/events-hub-tenants-offline-in-recovery-region.png)  

  * Pokud otevřete stránku události tenanta přímo v době, kdy je klient v režimu offline, zobrazí se stránka s oznámením klienta offline. Pokud je například contoso v režimu offline, zkuste otevřít http://events.wingtip-dpt.&lt ; User &gt; . trafficmanager.NET/contosoconcerthall.

    ![Snímek obrazovky zobrazující stránku offline události](./media/saas-dbpertenant-dr-geo-restore/dr-in-progress-offline-contosoconcerthall.png)

## <a name="provision-a-new-tenant-in-the-recovery-region"></a>Zřízení nového tenanta v oblasti obnovení
Dokonce i před obnovením databází klientů můžete zřídit nové klienty v oblasti obnovení. Nové databáze tenantů zřízené v oblasti obnovení jsou později vráceny se obnovenými databázemi.   

1. V ISE PowerShellu ve skriptu. ..\Learning Modules\Business kontinuita a Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1 pro zotavení po havárii nastavte následující vlastnost:

    $DemoScenario = 3: zřízení nového tenanta v oblasti obnovení.

2. Pokud chcete skript spustit, vyberte F5.

3. Po dokončení zřizování se v prohlížeči otevře stránka události Hawthorn hala. 

    Všimněte si, že databáze Hawthorn hala se nachází v oblasti obnovení.

    ![Hawthorn zřízené v oblasti obnovení](./media/saas-dbpertenant-dr-geo-restore/hawthorn-hall-provisioned-in-recovery-region.png)

4. V prohlížeči aktualizujte stránku centra událostí Wingtip lístky, aby se zobrazila zahrnutá Hawthorn hala. 

    Pokud jste zřídili Hawthorn místnosti, aniž byste čekali na obnovení ostatních tenantů, můžou být ostatní klienti stále offline.

## <a name="review-the-recovered-state-of-the-application"></a>Zkontrolujte obnovený stav aplikace.

Po dokončení procesu obnovení jsou aplikace a všichni klienti plně funkční v oblasti obnovení. 

1. Po zobrazení v okně konzoly PowerShellu se zobrazí oznámení, že se obnoví všechny klienty, a aktualizujte centrum událostí. 

    Všichni klienti se zobrazí online, včetně nového tenanta, Hawthorn hala.

    ![Obnovená a noví klienti v centru událostí](./media/saas-dbpertenant-dr-geo-restore/events-hub-with-hawthorn-hall.png)

2. Klikněte na položku contoso. hala a otevřete její stránku události. 

    V zápatí si všimněte, že se databáze nachází na serveru pro obnovení, který se nachází v oblasti obnovení.

    ![Contoso v oblasti obnovení](./media/saas-dbpertenant-dr-geo-restore/contoso-recovery-location.png)

3. V [Azure Portal](https://portal.azure.com)otevřete seznam skupin prostředků.  

    Všimněte si skupiny prostředků, kterou jste nasadili, a skupiny prostředků obnovení s příponou obnovení. Skupina prostředků obnovení obsahuje všechny prostředky vytvořené během procesu obnovení a navíc nové prostředky vytvořené během výpadku. 

4. Otevřete skupinu prostředků obnovení a Všimněte si následujících položek:

   * Verze pro obnovení katalogu a serverů tenants1 s příponou obnovení. Všechny obnovené databáze katalogu a klientů na těchto serverech mají všechny názvy používané v původní oblasti.

   * SQL Server tenants2-DPT- &lt; User &gt; -Recovery. Tento server se používá ke zřízení nových tenantů během výpadku.

   * App Service s názvem Events – Wingtip-DPT- &lt; recoveryregion &gt; - &lt; User &gt; , což je instance obnovení aplikace události.

     ![Prostředky contoso v oblasti obnovení](./media/saas-dbpertenant-dr-geo-restore/resources-in-recovery-region.png) 
    
5. Otevřete SQL Server tenants2-DPT- &lt; User &gt; -Recovery. Všimněte si, že obsahuje databázi hawthornhall a elastický fond Pool1. Databáze hawthornhall je nakonfigurovaná jako elastická databáze v elastickém fondu Pool1.

## <a name="change-the-tenant-data"></a>Změna dat tenanta 
V této úloze aktualizujete jednu z obnovených databází tenantů. Proces vrácení se kopíruje obnovené databáze, které byly změněny do původní oblasti. 

1. V prohlížeči Najděte seznam událostí pro společnost Contoso. Procházejte události a Všimněte si poslední události, vážně Strauss.

2. V ISE PowerShellu ve skriptu. ..\Learning Modules\Business kontinuita a Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1 pro zotavení po havárii nastavte následující hodnotu:

    $DemoScenario = 4: odstranění události z klienta v oblasti obnovení.

3. Pokud chcete skript spustit, vyberte F5.

4. Aktualizujte stránku s událostmi ve firmě contoso ( http://events.wingtip-dpt.&lt ; User &gt; . trafficmanager.NET/contosoconcerthall) a Všimněte si, že chybí událost vážně Strauss.

V tomto okamžiku v tomto kurzu jste obnovili aplikaci, která je teď spuštěná v oblasti obnovení. V oblasti obnovení jste zřídili nového tenanta a upravili data jednoho z obnovených tenantů.  

> [!NOTE]
> Jiné kurzy v ukázce nejsou určeny ke spuštění aplikace ve stavu obnovení. Pokud chcete prozkoumat jiné kurzy, nezapomeňte aplikaci napřed sami navrácením.

## <a name="repatriation-process-overview"></a>Přehled procesu navracení

Po vyřešení výpadku proces repatriace vrátí aplikaci a její databáze do její původní oblasti.

![Vrácení se změnami geografického obnovení](./media/saas-dbpertenant-dr-geo-restore/geo-restore-repatriation.png)   

Proces:

1. Zastaví všechny probíhající aktivity obnovení a zruší všechny nedokončené nebo v letadlové žádosti o obnovení databáze.

2. Znovu aktivuje v databázích tenanta původní oblasti, které se od posledního výpadku nezměnily. Tyto databáze obsahují ještě Neobnovené a ty, které se obnovily, ale nezměnily. Opětovně aktivované databáze jsou přesně tak, jak je klienti jejich klientů naposledy otevřeli.

3. Zřídí zrcadlový obraz serveru a elastického fondu nového tenanta v původní oblasti. Po dokončení této akce se nový alias tenanta aktualizuje tak, aby odkazoval na tento server. Aktualizace aliasu způsobí, že se nové připojení tenanta vyskytne v původní oblasti namísto oblasti obnovení.

3. Používá geografickou replikaci k přesunutí katalogu do původní oblasti z oblasti obnovení.

4. Aktualizuje konfiguraci fondu v původní oblasti tak, aby byla konzistentní se změnami provedenými v oblasti obnovení během výpadku.

5. Vytvoří požadované servery a fondy pro hostování všech nových databází vytvořených během výpadku.

6. Pomocí geografické replikace vrátí obnovené databáze tenantů aktualizované po obnovení a všechny nové databáze tenantů zřízené během výpadku. 

7. Vyčistí během procesu obnovení prostředky vytvořené v oblasti obnovení.

Chcete-li omezit počet databází tenanta, které je nutné vrátit zpět, kroky 1 až 3 se zobrazí okamžitě.  

Krok 4 se provede jenom v případě, že se katalog v oblasti obnovení změnil během výpadku. Katalog se aktualizuje, pokud se vytvoří noví klienti nebo dojde ke změně konfigurace databáze nebo fondu v oblasti obnovení.

Je důležité, aby krok 7 způsobil u klientů minimální přerušení a nedošlo ke ztrátě dat. K dosažení tohoto cíle proces používá geografickou replikaci.

Před tím, než se každá databáze geograficky replikuje, se odstraní odpovídající databáze v původní oblasti. Databáze v oblasti obnovení se pak geograficky replikuje a vytvoří sekundární repliku v původní oblasti. Po dokončení replikace se tenant označí jako offline v katalogu, což zruší všechna připojení k databázi v oblasti obnovení. U databáze dojde k převzetí služeb při selhání, což způsobí, že všechny probíhající transakce čekají na sekundární zpracování, takže se neztratí žádná data. 

Při převzetí služeb při selhání jsou databázové role obrácené. Sekundární v původní oblasti se stal primární databází pro čtení i zápis a databáze v oblasti obnovení se bude nacházet jako sekundární databáze jen pro čtení. Záznam tenanta v katalogu se aktualizuje tak, aby odkazoval na databázi v původní oblasti, a klient je označený jako online. V tomto okamžiku je vrácení repatriace databáze dokončeno. 

Aplikace by měly být napsány pomocí logiky opakování, aby bylo zajištěno, že se znovu připojí automaticky v případě přerušení připojení. Když používají Katalog ke zprostředkovateli opětovného připojení, připojí se k předané databázi v původní oblasti. I když se krátké odpojení často nevšimlo, můžete se rozhodnout, že budete databáze převádět z pracovní doby.

Po převrácení databáze je možné odstranit sekundární databázi v oblasti obnovení. Databáze v původní oblasti pak znovu spoléhá na geografickou obnovu pro ochranu DR.

V kroku 8 se odstraní prostředky v oblasti obnovení, včetně serverů a fondů pro obnovení.

## <a name="run-the-repatriation-script"></a>Spuštění skriptu pro vrácení
Představte si, že se výpadek vyřeší a spustí se skript pro vrácení.

Pokud jste postupovali podle tohoto kurzu, skript okamžitě znovu aktivuje Fabrikam jazz klub a Dogwood Dojo v původní oblasti, protože se nezměnily. Pak převrácení nového tenanta, Hawthorn místnosti a společnosti Contoso, protože byla upravena. Skript také převrácení katalogu, který byl aktualizován při zřízení Hawthorn místnosti.
  
1. V ISE PowerShellu ve skriptu ..\Learning a Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1 pro zotavení po havárii ověřte, že proces synchronizace katalogu pořád běží v instanci PowerShellu. V případě potřeby ho restartujte nastavením:

    $DemoScenario = 1: Spustit synchronizaci informací o serveru klienta, fondu a konfiguraci databáze do katalogu.

    Pokud chcete skript spustit, vyberte F5.

2.  Pak zahajte proces navracení, nastavte:

    $DemoScenario = 5: převrácení aplikace do původní oblasti.

    Pokud chcete skript pro obnovení spustit v novém okně PowerShellu, vyberte F5. Vrácení může trvat několik minut a může se monitorovat v okně PowerShellu.

3. Když je skript spuštěný, aktualizujte stránku centra událostí ( http://events.wingtip-dpt.&lt ; User &gt; . trafficmanager.NET).

    Všimněte si, že všichni klienti jsou online a přístupné v celém rámci tohoto procesu.

4. Vyberte klubu společnosti Fabrikam jazz a otevřete ji. Pokud jste tento tenant nezměnili, Všimněte si od zápatí, že server je již vrácen na původní server.

5. Otevřete nebo aktualizujte stránku s událostmi ve službě contoso. Všimněte si, že ve výchozím zápatí je databáze stále na serveru pro obnovení. 

6. Po dokončení procesu vrácení se změnami aktualizujte stránku akcí společnosti Contoso hala a Všimněte si, že databáze je nyní ve vaší původní oblasti.

7. Znovu aktualizujte centrum událostí a otevřete Hawthorn hala. Všimněte si, že se jeho databáze nachází také v původní oblasti. 

## <a name="clean-up-recovery-region-resources-after-repatriation"></a>Vyčištění prostředků oblasti obnovení po vrácení
Po vrácení se změnami je bezpečné prostředky odstranit v oblasti obnovení. 

> [!IMPORTANT]
> Odstraňte tyto prostředky s výzvou k okamžitému zastavení všech faktur.

Proces obnovení vytvoří všechny prostředky obnovení ve skupině prostředků pro obnovení. Proces čištění odstraní tuto skupinu prostředků a odebere všechny odkazy na prostředky z katalogu. 

1. V ISE PowerShellu ve skriptu. ..\Learning Modules\Business kontinuita a Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1 pro zotavení po havárii nastavte:
    
    $DemoScenario = 6: odstranit zastaralé prostředky z oblasti obnovení.

2. Pokud chcete skript spustit, vyberte F5.

Po vyčištění skriptů se aplikace vrátí do místa, kde byla spuštěna. V tuto chvíli můžete skript spustit znovu nebo si vyzkoušet jiné kurzy.

## <a name="designing-the-application-to-ensure-that-the-app-and-the-database-are-co-located"></a>Návrh aplikace, aby se zajistilo, že se aplikace a databáze společně nacházejí 
Aplikace je navržena tak, aby se vždy připojovala z instance ve stejné oblasti jako databáze klienta. Tento návrh zkracuje latenci mezi aplikací a databází. Tato optimalizace předpokládá, že interakce mezi aplikacemi a databází je chattier než interakce mezi uživatelem a aplikací.  

Databáze tenantů se můžou během navracení mezi obnovením a původními oblastmi rozložit na určitou dobu. U každé databáze aplikace vyhledává oblast, ve které se databáze nachází, pomocí vyhledávání DNS v názvu serveru tenanta. Název serveru je alias. Název serveru s aliasem obsahuje název oblasti. Pokud aplikace není ve stejné oblasti jako databáze, přesměruje ji na instanci ve stejné oblasti jako server. Přesměrování na instanci ve stejné oblasti, ve které databáze minimalizuje latenci mezi aplikací a databází.  

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:
> [!div class="checklist"]
> 
> * Katalog tenantů použijte k uchovávání pravidelně aktualizovaných konfiguračních informací, což umožňuje vytvořit prostředí pro obnovení zrcadlových imagí v jiné oblasti.
> * Obnovte databáze do oblasti obnovení pomocí geografického obnovení.
> * Aktualizujte katalog tenanta, aby odrážel obnovená umístění databáze tenanta. 
> * Pomocí aliasu DNS můžete aplikaci povolit, aby se připojila k katalogu tenantů v rámci bez opakované konfigurace.
> * Geografická replikace slouží k vrácení obnovených databází do původní oblasti po vyřešení výpadku.

Vyzkoušejte [zotavení po havárii pro víceklientské aplikace SaaS s využitím kurzu geografické replikace databáze](../../sql-database/saas-dbpertenant-dr-geo-replication.md) a Naučte se, jak pomocí geografické replikace výrazně zkrátit dobu potřebnou k obnovení rozsáhlé aplikace pro více tenantů.

## <a name="additional-resources"></a>Další zdroje informací

[Další kurzy, které se vytvářejí na aplikaci Wingtip SaaS](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
