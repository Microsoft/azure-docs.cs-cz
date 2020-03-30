---
title: 'Aplikace SaaS: Geograficky redundantní zálohy pro zotavení po havárii'
description: Naučte se používat geograficky redundantní zálohy Azure SQL Database k obnovení víceklientské aplikace SaaS v případě výpadku.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: AyoOlubeko
ms.author: craigg
ms.reviewer: sstein
ms.date: 01/14/2019
ms.openlocfilehash: 270fc157fa14efa19ed30d35b614fb769804b72e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73826459"
---
# <a name="use-geo-restore-to-recover-a-multitenant-saas-application-from-database-backups"></a>Použití geografického obnovení k obnovení víceklientské aplikace SaaS ze záloh databáze

Tento kurz zkoumá úplný scénář zotavení po havárii pro víceklientské aplikace SaaS implementované s databází na model klienta. Geografické [obnovení](sql-database-recovery-using-backups.md) slouží k obnovení databáze katalogu a klienta z automaticky udržovaných geograficky redundantních záloh do alternativní oblasti obnovení. Po vyřešení výpadku použijete [geografickou replikaci](sql-database-geo-replication-overview.md) k repatriaci změněných databází do původní oblasti.

![Geografická obnova-architektura](media/saas-dbpertenant-dr-geo-restore/geo-restore-architecture.png)

Geografické obnovení je řešení zotavení po havárii s nejnižšími náklady pro Azure SQL Database. Obnovení z geograficky redundantních záloh však může mít za následek ztrátu dat až jednu hodinu. To může trvat značnou dobu, v závislosti na velikosti každé databáze. 

> [!NOTE]
> Obnovte aplikace s nejnižšímožnou rpo a RTO pomocí geografické replikace namísto geografického obnovení.

Tento kurz zkoumá pracovní postupy obnovení i repatriace. Získáte informace o těchto tématech:
> [!div class="checklist"]
> 
> * Synchronizace databáze a informace o konfiguraci elastického fondu do katalogu klienta.
> * Nastavte prostředí zrcadlového obrazu v oblasti obnovení, která zahrnuje aplikace, servery a fondy.   
> * Obnovte databáze katalogu a klienta pomocí geografického obnovení.
> * Geografické replikace slouží k repatriaci katalogu klienta a změněných databází klientů po vyřešení výpadku.
> * Aktualizujte katalog při obnovení (nebo repatriaci) každé databáze ke sledování aktuálního umístění aktivní kopie databáze každého klienta.
> * Ujistěte se, že databáze aplikací a tenanta jsou vždy umístěny ve stejné oblasti Azure, abyste snížili latenci. 
 

Před zahájením tohoto kurzu vyplňte následující požadavky:
* Nasazení databáze Wingtip Tickets SaaS na aplikaci klienta. Nasazení za méně než pět minut, najdete v [tématu nasazení a prozkoumání Wingtip Tickets SaaS databáze na klienta aplikace](saas-dbpertenant-get-started-deploy.md). 
* Nainstalujte Azure PowerShell. Podrobnosti najdete [v tématu Začínáme s Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

## <a name="introduction-to-the-geo-restore-recovery-pattern"></a>Úvod do geografického obnovení systému obnovy

Zotavení po havárii (DR) je důležitým aspektem pro mnoho aplikací, ať už z důvodů dodržování předpisů nebo kontinuity provozu. Pokud dojde k dlouhodobému výpadku služby, dobře připravený plán zotavení po havárii může minimalizovat narušení podnikání. Plán zotavení po havárii založený na geografickém obnovení musí dosáhnout několika cílů:
 * Rezervujte si co nejrychleji veškerou potřebnou kapacitu ve zvolené oblasti obnovení, abyste zajistili, že je k dispozici pro obnovení databází klientů.
 * Vytvořte prostředí pro obnovení zrcadlového obrazu, které odráží původní fond a konfiguraci databáze. 
 * Povolit zrušení procesu obnovení v polovině letu, pokud původní oblast vrátí do režimu online.
 * Povolte zřizování klienta rychle, aby nové připojení klienta můžete restartovat co nejdříve.
 * Být optimalizovány pro obnovení klientů v pořadí podle priority.
 * Být optimalizovány tak, aby se klienti online co nejdříve tím, že dělá kroky paralelně, kde je to praktické.
 * Buďte odolné vůči selhání, restartovatelné a idempotentní.
 * Repatriovat databáze do své původní oblasti s minimálním dopadem na klienty při výpadku je vyřešen.  

> [!NOTE]
> Aplikace je obnovena do spárované oblasti oblasti, ve které je aplikace nasazena. Další informace najdete v tématu [Azure spárované oblasti](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).   

Tento kurz používá funkce Azure SQL Database a platformy Azure k řešení těchto problémů:

* [Šablony Azure Resource Manageru](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-create-first-template), které vám vyhradí veškerou potřebnou kapacitu co nejrychleji. Šablony Azure Resource Manager se používají k zřízení zrcadlový obraz původní ch serverů a elastické fondy v oblasti obnovení. Samostatný server a fond jsou také vytvořeny pro zřizování nových klientů.
* [Klientská knihovna elastické databáze](sql-database-elastic-database-client-library.md) (EDCL) k vytvoření a údržbě katalogu databáze klienta. Rozšířený katalog obsahuje pravidelně aktualizovaný fond a informace o konfiguraci databáze.
* [Funkce obnovení správy svižných položek svižného](sql-database-elastic-database-recovery-manager.md) místa edcl, udržovat položky umístění databáze v katalogu během obnovení a repatriace.  
* [Geografické obnovení , chcete-li](sql-database-disaster-recovery.md)obnovit databáze katalogu a klienta z automaticky udržovaných geograficky redundantních záloh. 
* [Asynchronní operace obnovení](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations), odeslané v pořadí priorit klienta, jsou zařazeny do fronty pro každý fond systémem a zpracovány v dávkách, takže fond není přetížený. Tyto operace lze zrušit před nebo během provádění v případě potřeby.   
* [Geografická replikace](sql-database-geo-replication-overview.md), repatriace databází do původní oblasti po výpadku. Neexistuje žádná ztráta dat a minimální dopad na klienta při použití geografické replikace.
* [Aliasy DNS serveru SQL server](dns-alias-overview.md), aby se proces synchronizace katalogu mohl připojit k aktivnímu katalogu bez ohledu na jeho umístění.  

## <a name="get-the-disaster-recovery-scripts"></a>Získejte skripty pro zotavení po havárii

Skripty zotavení po havárii použité v tomto kurzu jsou k dispozici v [databázi Wingtip Tickets SaaS na klienta úložiště GitHub](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant). Podívejte se na [obecné pokyny pro](saas-tenancy-wingtip-app-guidance-tips.md) kroky ke stažení a odblokování skriptů pro správu wingtip vstupenek.

> [!IMPORTANT]
> Stejně jako všechny skripty správy Wingtip Tickets, skripty zotavení po Havárii jsou ukázkové kvality a nemají být použity v produkčním prostředí.

## <a name="review-the-healthy-state-of-the-application"></a>Kontrola stavu aplikace v pořádku
Před zahájením procesu obnovení zkontrolujte normální stav v pořádku aplikace.

1. Ve webovém prohlížeči otevřete centrumhttp://events.wingtip-dpt.&ltudálostí&gt;Wingtip Tickets &lt;&gt; ( ;uživatel .trafficmanager.net, nahraďte uživatele uživatelskou hodnotou nasazení).
    
   Přejděte do dolní části stránky a všimněte si názvu a umístění serveru katalogu v zápatí. Umístění je oblast, ve které jste nasadili aplikaci.    

   > [!TIP]
   > Najeďte myší na místo pro zvětšení displeje.

   ![Události rozbočovače v původním stavu](media/saas-dbpertenant-dr-geo-restore/events-hub-original-region.png)

2. Vyberte tenanta Koncertní síně Contoso a otevřete jeho stránku události.

   V zápatí si všimněte názvu serveru klienta. Umístění je stejné jako umístění serveru katalogu.

   ![Contoso Koncertní sál původní region](media/saas-dbpertenant-dr-geo-restore/contoso-original-location.png) 

3. Na [webu Azure Portal](https://portal.azure.com)zkontrolujte a otevřete skupinu prostředků, ve které jste aplikaci nasadili.

   Všimněte si prostředků a oblasti, ve které jsou nasazeny součásti služby aplikace a servery SQL Database.

## <a name="sync-the-tenant-configuration-into-the-catalog"></a>Synchronizace konfigurace klienta do katalogu

V této úloze spustíte proces synchronizace konfigurace serverů, elastických fondů a databází do katalogu klienta. Tyto informace se později používají ke konfiguraci prostředí zrcadlového obrazu v oblasti obnovení.

> [!IMPORTANT]
> Pro jednoduchost proces synchronizace a další dlouhotrvající procesy obnovení a repatriace jsou implementovány v těchto ukázkách jako místní úlohy nebo relace prostředí PowerShell, které běží pod přihlášením klientského uživatele. Platnost ověřovacích tokenů vydaných při přihlášení vyprší po několika hodinách a úlohy se pak nezdaří. Ve scénáři výroby by měly být implementovány dlouhotrvající procesy jako spolehlivé služby Azure nějakého druhu, spuštěné pod instančníobjekt. Viz [Použití Azure PowerShellu k vytvoření instančního objektu s certifikátem](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authenticate-service-principal). 

1. V prostředí PowerShell ISE otevřete soubor ...\Learning Modules\UserConfig.psm1. Nahraďte `<resourcegroup>` a `<user>` na řádcích 10 a 11 hodnotou použitou při nasazení aplikace. Uložte soubor.

2. V prostředí PowerShell ISE otevřete skript ...\Learning Modules\Business Continuity and Disaster Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1.

    V tomto kurzu spustíte každý scénář v tomto skriptu prostředí PowerShell, takže tento soubor nechte otevřený.

3. Nastavte následující:

    $DemoScenario = 1: Spusťte úlohu na pozadí, která synchronizuje informace o konfiguraci serveru klienta a fondu do katalogu.

4. Chcete-li spustit skript synchronizace, vyberte možnost F5. 

    Tyto informace se později používají k zajištění, že obnovení vytvoří zrcadlový obraz serverů, fondů a databází v oblasti obnovení.  

    ![Proces synchronizace](media/saas-dbpertenant-dr-geo-restore/sync-process.png)

Ponechte okno Prostředí PowerShell spuštěné na pozadí a pokračujte zbytkem tohoto kurzu.

> [!NOTE]
> Proces synchronizace se připojuje ke katalogu pomocí aliasu DNS. Alias je upraven během obnovení a repatriace tak, aby ukazoval na aktivní katalog. Proces synchronizace udržuje katalog aktuální s všechny změny konfigurace databáze nebo fondu provedené v oblasti obnovení. Během repatriace jsou tyto změny použity na ekvivalentní prostředky v původní oblasti.

## <a name="geo-restore-recovery-process-overview"></a>Přehled procesu obnovení geografického obnovení

Proces obnovení geografického obnovení nasazuje aplikaci a obnovuje databáze ze záloh do oblasti obnovení.

Proces obnovení provádí následující akce:

1. Zakáže koncový bod Azure Traffic Manager pro webovou aplikaci v původní oblasti. Zakázání koncového bodu zabrání uživatelům připojení k aplikaci v neplatném stavu by původní oblast přepne do režimu online během obnovení.

2. Zřídí server katalogu obnovení v oblasti obnovení, geo obnoví databázi katalogu a aktualizuje alias activecatalog tak, aby ukazoval na obnovený server katalogu. Změna aliasu katalogu zajistí, že se proces synchronizace katalogu vždy synchronizuje s aktivním katalogem.

3. Označí všechny existující klienty v katalogu obnovení jako offline, aby se zabránilo přístupu k databázím klientů před jejich obnovením.

4. Zřídí instanci aplikace v oblasti obnovení a nakonfiguruje ji tak, aby používala obnovený katalog v této oblasti. Chcete-li zachovat latenci na minimum, ukázková aplikace je navržena tak, aby se vždy připojila k databázi klienta ve stejné oblasti.

5. Zřaží server a elastický fond, ve kterém jsou zřízeny nové klienty. Vytváření těchto prostředků zajišťuje, že zřizování nových klientů není v rozporu s obnovení mnohých klientů.

6. Aktualizuje nový alias klienta tak, aby ukazoval na server pro nové databáze klientů v oblasti obnovení. Změna tohoto aliasu zajistí, že databáze pro všechny nové klienty jsou zřízeny v oblasti obnovení.
        
7. Zřídí servery a elastické fondy v oblasti obnovení pro obnovení databází klientů. Tyto servery a fondy jsou zrcadlovým obrazem konfigurace v původní oblasti. Zřizování fondů předem rezervuje kapacitu potřebnou k obnovení všech databází.

    Výpadek v oblasti může být značný tlak na prostředky, které jsou k dispozici ve spárované oblasti. Pokud se spoléháte na geografické obnovení pro zotavení po havárii, doporučujeme rychle si vyhradit prostředky. Zvažte geografickou replikaci, pokud je důležité, aby byla aplikace obnovena v určité oblasti. 

8. Povolí koncový bod Traffic Managerpro webovou aplikaci v oblasti obnovení. Povolení tohoto koncového bodu umožňuje aplikaci zřídit nové klienty. V této fázi jsou stávající klienti stále offline.

9. Odešle dávky požadavků na obnovení databází v pořadí podle priority. 

    * Listy jsou uspořádány tak, aby databáze byly obnoveny paralelně ve všech fondech.  

    * Požadavky na obnovení jsou odeslány asynchronně, takže jsou odeslány rychle a zařazeny do fronty pro spuštění v každém fondu.

    * Vzhledem k tomu, že požadavky na obnovení jsou zpracovávány paralelně ve všech fondech, je lepší distribuovat důležité klienty v mnoha fondech. 

10. Monitoruje službu SQL Database a určuje, kdy jsou databáze obnoveny. Po obnovení databáze klienta je označena online v katalogu a je zaznamenán a rowversion sum pro databázi klienta. 

    * K databázím klienta může aplikace přistupovat, jakmile jsou označeny online v katalogu.

    * Součet hodnot rowversion v databázi klienta je uložen v katalogu. Tato částka funguje jako otisk prstu, který umožňuje procesu repatriace k určení, zda byla databáze aktualizována v oblasti obnovení.       

## <a name="run-the-recovery-script"></a>Spuštění skriptu pro obnovení

> [!IMPORTANT]
> Tento kurz obnovuje databáze z geograficky redundantních záloh. Přestože tyto zálohy jsou obvykle k dispozici do 10 minut, může trvat až hodinu. Skript se pozastaví, dokud nebudou k dispozici.

Představte si, že je výpadek v oblasti, ve které je aplikace nasazena a spustit skript pro obnovení:

1. Ve skriptu PowerShell ISE nastavte ve skriptu ...\Learning Modules\Business Continuity and Disaster Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1 následující hodnotu:

    $DemoScenario = 2: Obnovení aplikace do oblasti obnovení obnovením z geograficky redundantních záloh.

2. Chcete-li skript spustit, vyberte možnost F5.  

    * Skript se otevře v novém okně Prostředí PowerShell a spustí sadu úloh prostředí PowerShell, které běží paralelně. Tyto úlohy obnovit servery, fondy a databáze do oblasti obnovení.

    * Oblast obnovení je spárovaná oblast přidružená k oblasti Azure, ve které jste nasadili aplikaci. Další informace najdete v tématu [Azure spárované oblasti](https://docs.microsoft.com/azure/best-practices-availability-paired-regions). 

3. Sledujte stav procesu obnovení v okně Prostředí PowerShell.

    ![Proces obnovení](media/saas-dbpertenant-dr-geo-restore/dr-in-progress.png)

> [!NOTE]
> Chcete-li prozkoumat kód úloh pro obnovení, projděte si skripty prostředí PowerShell ve složce ...\Learning Modules\Business Continuity and Disaster Recovery\DR-RestoreFromBackup\RecoveryJobs.

## <a name="review-the-application-state-during-recovery"></a>Kontrola stavu aplikace během obnovení
Zatímco koncový bod aplikace je ve Správci provozu zakázán, aplikace není k dispozici. Katalog je obnoven a všichni klienti jsou označeny offline. Koncový bod aplikace v oblasti obnovení je pak povolena a aplikace je zpět do režimu online. Přestože aplikace je k dispozici, klienti se zobrazí v offline v centru událostí, dokud jejich databáze jsou obnoveny. Je důležité navrhnout aplikaci pro zpracování offline databází klientů.

* Po obnovení databáze katalogu, ale dříve, než jsou klienti zpět online, aktualizujte centrum událostí Wingtip Tickets ve webovém prohlížeči.

  * V zápatí všimněte si, že název serveru katalogu má nyní příponu -recovery a je umístěn v oblasti obnovení.

  * Všimněte si, že klienti, které ještě nejsou obnoveny jsou označeny jako offline a nejsou volitelné.   
 
    ![Proces obnovení](media/saas-dbpertenant-dr-geo-restore/events-hub-tenants-offline-in-recovery-region.png)    

  * Pokud otevřete stránku událostí klienta přímo v době, kdy je klient offline, stránka zobrazí oznámení klienta offline. Pokud je například koncertní síň Contoso offline, zkuste otevřít http://events.wingtip-dpt.&lt.user&gt;.trafficmanager.net/contosoconcerthall.

    ![Proces obnovení](media/saas-dbpertenant-dr-geo-restore/dr-in-progress-offline-contosoconcerthall.png)

## <a name="provision-a-new-tenant-in-the-recovery-region"></a>Zřízení nového klienta v oblasti obnovení
Ještě před obnovením databází klientů můžete zřídit nové klienty v oblasti obnovení. Nové databáze klienta zřízené v oblasti obnovení jsou repatriovány s obnovené databáze později.   

1. Ve skriptu PowerShell ISE nastavte ve skriptu ...\Learning Modules\Business Continuity and Disaster Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1 následující vlastnost:

    $DemoScenario = 3: Zřízení nového klienta v oblasti obnovení.

2. Chcete-li skript spustit, vyberte možnost F5.

3. Hawthorn Hall události stránka se otevře v prohlížeči při zajišťování dokončí. 

    Všimněte si, že databáze Hawthorn Hall se nachází v oblasti obnovení.

    ![Hawthorn Hall zřízena v oblasti obnovy](media/saas-dbpertenant-dr-geo-restore/hawthorn-hall-provisioned-in-recovery-region.png)

4. V prohlížeči aktualizujte stránku centra událostí Wingtip Tickets, abyste viděli Hawthorn Hall. 

    Pokud jste zřídili Hawthorn Hall bez čekání na ostatní nájemníky k obnovení, ostatní klienti mohou být stále offline.

## <a name="review-the-recovered-state-of-the-application"></a>Zkontrolujte obnovený stav aplikace

Po dokončení procesu obnovení aplikace a všichni klienti jsou plně funkční v oblasti obnovení. 

1. Po zobrazení v okně konzoly PowerShell indikuje všechny klienty jsou obnoveny, aktualizujte události centra. 

    Všichni nájemníci se objeví online, včetně nového nájemníka Hawthorn Hall.

    ![Obnovený a nové klienty v centru událostí](media/saas-dbpertenant-dr-geo-restore/events-hub-with-hawthorn-hall.png)

2. Klikněte na Koncertní síň Contoso a otevřete její stránku akcí. 

    V zápatí všimněte si, že databáze je umístěna na serveru pro obnovení umístěném v oblasti obnovení.

    ![Contoso v oblasti obnovy](media/saas-dbpertenant-dr-geo-restore/contoso-recovery-location.png)

3. Na [webu Azure Portal](https://portal.azure.com)otevřete seznam skupin prostředků.  

    Všimněte si skupiny prostředků, kterou jste nasadili, plus skupinu prostředků pro obnovení s příponou -recovery. Skupina prostředků pro obnovení obsahuje všechny prostředky vytvořené během procesu obnovení a nové prostředky vytvořené během výpadku. 

4. Otevřete skupinu prostředků pro obnovení a všimněte si následujících položek:

   * Verze pro obnovení katalogu a tenants1 servery s příponou -recovery. Obnovené databáze katalogu a klienta na těchto serverech mají názvy používané v původní oblasti.

   * Tenants2-dpt-&lt;user&gt;-recovery SQL server. Tento server se používá pro zřizování nových klientů během výpadku.

   * Služba aplikace s názvem&lt;events-wingtip-dpt- recoveryregion&gt;-&lt;uživatele&gt;, což je instance obnovení aplikace události.

     ![Prostředky contoso v oblasti obnovení](media/saas-dbpertenant-dr-geo-restore/resources-in-recovery-region.png) 
    
5. Otevřete&lt;klienty2-dpt-&gt;user-recovery SQL server. Všimněte si, že obsahuje databázi hawthornhall a elastického fondu Pool1. Hawthornhall databáze je nakonfigurován jako elastické databáze ve fondu pool1 elastického fondu.

## <a name="change-the-tenant-data"></a>Změna dat klienta 
V této úloze můžete aktualizovat jednu z obnovených databází klienta. Proces repatriace zkopíruje obnovené databáze, které byly změněny na původní oblast. 

1. V prohlížeči najděte seznam událostí pro koncertní síň Contoso, procházejte události a všimněte si poslední události, Vážně Strausse.

2. Ve skriptu PowerShell ISE nastavte ve skriptu ...\Learning Modules\Business Continuity and Disaster Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1 následující hodnotu:

    $DemoScenario = 4: Odstraňte událost z klienta v oblasti obnovení.

3. Chcete-li skript spustit, vyberte možnost F5.

4. Aktualizujte stránku událostí vhttp://events.wingtip-dpt.&ltKoncertní&gt;síni Contoso ( ;uživatel .trafficmanager.net/contosoconcerthall) a všimněte si, že událost Seriously Strauss chybí.

V tomto okamžiku v kurzu jste obnovili aplikaci, která je nyní spuštěna v oblasti obnovení. Zřídili jste nového klienta v oblasti obnovení a upravená data jednoho z obnovených klientů.  

> [!NOTE]
> Ostatní kurzy v ukázce nejsou navrženy tak, aby s aplikací ve stavu obnovení. Pokud chcete prozkoumat další výukové programy, ujistěte se, že repatriovat aplikaci jako první.

## <a name="repatriation-process-overview"></a>Přehled procesu repatriace

Proces repatriace vrátí aplikaci a její databáze do původní oblasti po vyřešení výpadku.

![Repatriace geografického obnovení](media/saas-dbpertenant-dr-geo-restore/geo-restore-repatriation.png) 

Proces:

1. Zastaví všechny probíhající aktivity obnovení a zruší všechny nevyřízené nebo za letu požadavky na obnovení databáze.

2. Znovu se aktivuje v původní databáze klienta oblasti, které nebyly změněny od výpadku. Tyto databáze zahrnují ty, které ještě nebyly obnoveny, a ty, které byly obnoveny, ale později se nezměnily. Reaktivované databáze jsou přesně tak, jak naposledy přistupovat jejich tenanty.

3. Zřídí zrcadlový obraz serveru nového klienta a elastického fondu v původní oblasti. Po dokončení této akce je nový alias klienta aktualizován tak, aby ukazoval na tento server. Aktualizace aliasu způsobí, že nové připojení klienta dojde v původní oblasti namísto oblasti obnovení.

3. Používá geografickou replikaci k přesunutí katalogu do původní oblasti z oblasti obnovení.

4. Aktualizuje konfiguraci fondu v původní oblasti, takže je konzistentní se změnami, které byly provedeny v oblasti obnovení během výpadku.

5. Vytvoří požadované servery a fondy pro hostování všech nových databází vytvořených během výpadku.

6. Používá geografickou replikaci k repatriaci obnovených databází klientů, které byly aktualizovány po obnovení a všechny nové databáze klientů zřízené během výpadku. 

7. Vyčistí prostředky vytvořené v oblasti obnovení během procesu obnovení.

Chcete-li omezit počet databází klienta, které je třeba repatriovat, kroky 1 až 3 jsou provedeny okamžitě.  

Krok 4 se provádí pouze v případě, že katalog v oblasti obnovení byl změněn během výpadku. Katalog je aktualizován, pokud jsou vytvořeny nové klienty nebo pokud je v oblasti obnovení změněna konfigurace databáze nebo fondu.

Je důležité, aby krok 7 způsobuje minimální narušení klientů a dojde ke ztrátě žádných dat. K dosažení tohoto cíle proces používá geografickou replikaci.

Před každou databázi je geograficky replikovány, odpovídající databáze v původní oblasti je odstraněn. Databáze v oblasti obnovení je pak geograficky replikována a vytvoří sekundární repliku v původní oblasti. Po dokončení replikace je klient označen offline v katalogu, což přeruší všechna připojení k databázi v oblasti obnovení. Databáze je pak převzetí míse, což způsobí, že všechny čekající transakce zpracovat na sekundární, takže žádná data jsou ztracena. 

Při převzetí služeb při selhání jsou role databáze obráceny. Sekundární v původní oblasti se stane primární databáze pro čtení a zápis a databáze v oblasti obnovení se stane sekundární jen pro čtení. Položka klienta v katalogu je aktualizována tak, aby odkazovala na databázi v původní oblasti a klient je označen online. V tomto okamžiku je dokončena repatriace databáze. 

Aplikace by měly být zapsány s logikou opakování, aby bylo zajištěno, že se automaticky znovu připojí, když jsou přerušena připojení. Při použití katalogu zprostředkovat opětovné připojení, připojují se k repatriované databáze v původní oblasti. I když krátké odpojení je často nevšiml, můžete se rozhodnout repatriovat databáze mimo pracovní dobu.

Po repatriaci databáze lze sekundární databázi v oblasti obnovení odstranit. Databáze v původní oblasti pak znovu spoléhá na geografické obnovení pro ochranu zotavení po Havárii.

V kroku 8 jsou odstraněny prostředky v oblasti obnovení, včetně serverů pro obnovení a fondů.

## <a name="run-the-repatriation-script"></a>Spuštění skriptu repatriace
Představme si, že výpadek je vyřešen a spusťte skript repatriace.

Pokud jste postupovali podle kurzu, skript okamžitě znovu aktivuje Fabrikam Jazz Club a Dogwood Dojo v původním regionu, protože jsou nezměněny. To pak repatriuje nový nájemce, Hawthorn Hall, a Contoso koncertní síň, protože to bylo upraveno. Skript také repatriuje katalog, který byl aktualizován, když hawthorn hall byl zřízen.
  
1. Ve skriptu PowerShell ISE ve skriptu ...\Learning Modules\Business Continuity and Disaster Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1 ověřte, zda je proces synchronizace katalogu stále spuštěn v instanci prostředí PowerShell. V případě potřeby jej restartujte nastavením:

    $DemoScenario = 1: Spusťte synchronizaci informací o konfiguraci serveru klienta, fondu a databáze do katalogu.

    Chcete-li skript spustit, vyberte možnost F5.

2.  Chcete-li zahájit proces repatriace, nastavte:

    $DemoScenario = 5: Repatriovat aplikaci do původní oblasti.

    Chcete-li spustit skript pro obnovení v novém okně prostředí PowerShell, vyberte Možnost F5. Repatriace trvá několik minut a lze sledovat v okně Prostředí PowerShell.

3. Během spuštění skriptu aktualizujte stránkuhttp://events.wingtip-dpt.&ltcentra&gt;událostí ( ;uživatel .trafficmanager.net).

    Všimněte si, že všichni klienti jsou online a přístupné v průběhu tohoto procesu.

4. Otevřete jej vyberte Fabrikam Jazz Club. Pokud jste tohoto klienta neupravili, všimněte si z zápatí, že server se již vrátil na původní server.

5. Otevřete nebo aktualizujte stránku událostí v koncertní síni Contoso. Všimněte si z zápatí, že zpočátku databáze je stále na serveru pro obnovení . 

6. Po dokončení procesu repatriace aktualizujte stránku události v koncertní síni Contoso a všimněte si, že databáze je nyní v původní oblasti.

7. Znovu osvěžte centrum událostí a otevřete Hawthorn Hall. Všimněte si, že jeho databáze je také umístěn v původní oblasti. 

## <a name="clean-up-recovery-region-resources-after-repatriation"></a>Vyčištění prostředků oblasti obnovení po repatriaci
Po dokončení repatriace je bezpečné odstranit prostředky v oblasti obnovení. 

> [!IMPORTANT]
> Tyto prostředky okamžitě odstraňte, abyste zastavili všechny fakturace.

Proces obnovení vytvoří všechny prostředky pro obnovení ve skupině prostředků pro obnovení. Proces čištění odstraní tuto skupinu prostředků a odebere všechny odkazy na prostředky z katalogu. 

1. Ve skriptu PowerShell ISE ve skriptu ...\Learning Modules\Business Continuity and Disaster Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1 nastavte:
    
    $DemoScenario = 6: Odstraňte zastaralé prostředky z oblasti obnovení.

2. Chcete-li skript spustit, vyberte možnost F5.

Po vyčištění skriptů je aplikace zpět tam, kde začala. V tomto okamžiku můžete skript spustit znovu nebo vyzkoušet další kurzy.

## <a name="designing-the-application-to-ensure-that-the-app-and-the-database-are-co-located"></a>Návrh aplikace, aby bylo zajištěno, že aplikace a databáze jsou umístěny společně 
Aplikace je navržena tak, aby se vždy připojovala z instance ve stejné oblasti jako databáze klienta. Tento návrh snižuje latenci mezi aplikací a databází. Tato optimalizace předpokládá, že interakce mezi aplikacemi a databázemi je chatovatelnější než interakce mezi uživateli a aplikacemi.  

Databáze klienta může být rozložena na obnovu a původní oblasti po určitou dobu během repatriace. Pro každou databázi aplikace vyhledá oblast, ve které je databáze umístěna, provedením vyhledávání DNS na názvu serveru klienta. V databázi SQL database je název serveru alias. Název aliasovaného serveru obsahuje název oblasti. Pokud aplikace není ve stejné oblasti jako databáze, přesměruje na instanci ve stejné oblasti jako databázový server. Přesměrování na instanci ve stejné oblasti jako databáze minimalizuje latenci mezi aplikací a databází.  

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:
> [!div class="checklist"]
> 
> * Katalog klienta slouží k uchovávaní pravidelně aktualizovaných konfiguračních informací, které umožňují vytvoření prostředí pro obnovení zrcadlového obrazu v jiné oblasti.
> * Obnovte databáze Azure SQL do oblasti obnovení pomocí geografického obnovení.
> * Aktualizujte katalog klienta tak, aby odrážel obnovená umístění databáze klienta. 
> * Pomocí aliasu DNS povolte aplikaci připojení ke katalogu klienta bez změny konfigurace.
> * Geografické replikace slouží k repatriaci obnovených databází do původní oblasti po vyřešení výpadku.

Zkuste [zotavení po havárii pro víceklientské aplikace SaaS pomocí databáze geografické replikace](saas-dbpertenant-dr-geo-replication.md) kurz y naučit, jak používat geografickou replikaci výrazně zkrátit čas potřebný k obnovení rozsáhlé víceklientské aplikace.

## <a name="additional-resources"></a>Další zdroje

[Další výukové programy, které vycházejí z aplikace Wingtip SaaS](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
