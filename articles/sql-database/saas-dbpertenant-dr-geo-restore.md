---
title: Použít geograficky redundantní zálohy databáze SQL Azure pro zotavení po havárii aplikace SaaS | Microsoft Docs
description: Naučit se používat Azure SQL Database geograficky redundantní zálohy obnovit víceklientské aplikace SaaS v případě výpadku
keywords: kurz k sql database
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.custom: saas apps
ms.topic: article
ms.date: 04/16/2018
ms.author: ayolubek
ms.openlocfilehash: 8fd25e13f6796b8be99ad3efd425bcde7bca3905
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="use-geo-restore-to-recover-a-multitenant-saas-application-from-database-backups"></a>Pomocí geografické obnovení ze zálohy databáze obnovit víceklientské aplikace SaaS

V tomto kurzu prozkoumá úplné zotavení po havárii pro víceklientské aplikace SaaS implementuje pomocí databáze pro každý model klienta. Používáte [geografické obnovení](https://docs.microsoft.com/azure/sql-database/sql-database-recovery-using-backups) k obnovení databází katalogu a klientů z automaticky zachována geograficky redundantní zálohy do oblast alternativní obnovení. Když se výpadek vyřešen, můžete použít [geografická replikace](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-overview) repatriovat základní změněné databáze do jejich původního oblasti.

![Architektura geografické obnovení](media/saas-dbpertenant-dr-geo-restore/geo-restore-architecture.png)

Geografické obnovení je řešení zotavení po havárii nejnižší náklady pro Azure SQL Database. Ale obnovení z geograficky redundantní zálohy může způsobit ztrátu dat až jednu hodinu. Může trvat velmi dlouho, v závislosti na velikosti jednotlivých databází. 

> [!NOTE]
> Aplikace s nejnižší možné plánovaný bod obnovení a RTO obnovte pomocí geografická replikace místo geografické obnovení.

V tomto kurzu prozkoumá obnovení a navracení pracovních postupů. Získáte informace o těchto tématech:
> [!div class="checklist"]

>* Synchronizace databáze a informace o konfiguraci elastického fondu do katalogu klienta.
>* Nastavení prostředí zrcadlený obraz v oblasti obnovení, který obsahuje aplikace, servery a fondy.   
>* Obnovení databáze katalogu a klienta pomocí geografické obnovení.
>* Geografická replikace použijte repatriovat základní katalogu klienta a změněné klienta databáze, jakmile se výpadek je vyřešený.
>* Aktualizovat katalog každou databázi obnovit (nebo repatriated) sledovat aktuální umístění aktivní kopie databáze každého klienta.
>* Ujistěte se, že aplikace a databáze klienta vždy společně se nacházejí ve stejné oblasti Azure ke snížení latence. 
 

Než začnete tento kurz, splnit následující požadavky:
* Nasazení databáze Wingtip lístky SaaS jednu aplikaci klienta. Nasazení za méně než pět minut najdete v tématu [nasazení a seznamte se s SaaS lístky Wingtip databáze za klienta aplikace](saas-dbpertenant-get-started-deploy.md). 
* Nainstalujte Azure PowerShell. Podrobnosti najdete v tématu [Začínáme s Azure Powershellem](https://docs.microsoft.com/powershell/azure/get-started-azureps).

## <a name="introduction-to-the-geo-restore-recovery-pattern"></a>Úvod do vzoru obnovení geografické obnovení

Zotavení po havárii (DR) je důležitá poznámka pro mnoho aplikací pro dodržování předpisů nebo kontinuity podnikových procesů. Pokud dojde k výpadku provozu v případě delších služby, přerušení obchodní minimalizovat dobře připravený plán zotavení po Havárii. Plán A zotavení po Havárii založené na geografické obnovení musí provést několik cíle:
 * Rezervujte všech potřebných kapacity v oblasti zvolené obnovení co nejrychleji zajistit, že je k dispozici pro obnovení databází klienta.
 * Vytvoření a obnovení prostředí zrcadlený obraz, původní konfigurace fondu a databáze, který. 
 * Zrušení v procesu obnovení v polovině letu povolit, pokud původní oblast přejde do režimu online.
 * Povolte rychlé zřizování, nové registrace klienta můžete restartovat co nejdříve klienta.
 * Optimalizované obnovení klienty v pořadí podle priority.
 * Chcete-li získat online klientů co nejdříve provedením kroků v paralelní tam, kde je to praktické optimalizovat.
 * Být odolné vůči selhání, dá se restartovat a idempotent.
 * Repatriovat základní databáze do jejich původního oblasti s minimálním dopadem na klienty, když se se výpadek vyřeší.  

> [!NOTE]
> Aplikace je obnovena do oblasti spárované oblasti, ve kterém je aplikace nasazená. Další informace najdete v tématu [Azure spárovat oblasti](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).   

Tento kurz používá funkce Azure SQL Database a platformy Azure vyřešit tyto problémy:

* [Šablony Azure Resource Manageru](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-create-first-template), co nejrychleji rezervovat všechny potřebné kapacity. Šablony Azure Resource Manageru se používají ke zřízení zrcadlený obraz původní serverů a elastické fondy v oblasti obnovení. Samostatný server a fondu jsou také vytvořili pro zajišťování nové klienty.
* [Klientská knihovna pro elastické databáze](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-database-client-library) (EDCL) pro vytváření a údržbu databáze katalog klienta. Rozšířené katalogu zahrnuje pravidelně aktualizovat informace o konfiguraci fondu a databáze.
* [Funkce obnovení správy horizontálního oddílu](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-database-recovery-manager) z EDCL, k udržování databáze umístění položky v katalogu během obnovení a navracení.  
* [Geografické obnovení](https://docs.microsoft.com/azure/sql-database/sql-database-disaster-recovery), k obnovení databází katalogu a klientů z automaticky zachována geograficky redundantní zálohy. 
* [Operace asynchronní obnovení](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations), odesílají v pořadí podle priority klienta, jsou zařazeny do fronty pro každý fond v systému a zpracovány v dávkách tak fondu není přetížený. Tato operace se dají zrušit před nebo během provádění v případě potřeby.   
* [Geografická replikace](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-overview), repatriovat základní databáze na původní oblast po se výpadek. Při použití geografická replikace není bez ztráty dat a minimální dopad na klienta.
* [SQL server DNS aliasy](https://docs.microsoft.com/azure/sql-database/dns-alias-overview), aby procesu synchronizace katalogu pro připojení ke katalogu active bez ohledu na jeho umístění.  

## <a name="get-the-disaster-recovery-scripts"></a>Získat skripty pro zotavení po havárii

Zotavení po Havárii skripty použité v tomto kurzu jsou k dispozici v [SaaS lístky Wingtip databáze za klienta úložiště GitHub](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant). Podívejte se [obecné pokyny](saas-tenancy-wingtip-app-guidance-tips.md) kroky, jak stáhnout a odblokování skripty správy Wingtip lístků.

> [!IMPORTANT]
> Podobně jako všechny lístky Wingtip správu skripty skripty zotavení po Havárii jsou ukázka kvality a není určen k použití v produkčním prostředí.

## <a name="review-the-healthy-state-of-the-application"></a>Kontrola stavu v pořádku aplikace
Před zahájením procesu obnovení, zkontrolujte stav v pořádku normální aplikace.

1. Ve webovém prohlížeči, otevřete Centrum událostí Wingtip lístků (http://events.wingtip-dpt.&lt; Uživatel&gt;. trafficmanager.net, nahraďte &lt;uživatele&gt; s hodnotou uživatele vašeho nasazení).
    
   Přejděte do dolní části stránky a Všimněte si katalogu serveru název a umístění v zápatí. Umístění je oblast, ve kterém je aplikace nasazená.    

   > [!TIP]
   > Najeďte myší umístění zvětšení zobrazení.

   ![Události v pořádku stav rozbočovače v původní oblasti](media/saas-dbpertenant-dr-geo-restore/events-hub-original-region.png)

2. Vyberte klienta Hall vzájemné součinnosti Contoso a otevřete stránku s jeho událostí.

   V zápatí Všimněte si, název serveru klienta. Umístění je stejný jako server katalogu umístění.

   ![Původní oblast Hall vzájemné součinnosti contoso](media/saas-dbpertenant-dr-geo-restore/contoso-original-location.png) 

3. V [portál Azure](https://portal.azure.com)zkontrolujte a otevřete skupinu prostředků, ve kterém je aplikace nasazená.

   Všimněte si, prostředky a oblasti, ve které jsou nasazeny komponenty služby aplikací a serverů SQL Database.

## <a name="sync-the-tenant-configuration-into-the-catalog"></a>Synchronizovat konfiguraci klienta do katalogu

V této úloze spustí se proces pro synchronizaci konfigurace serverů, elastické fondy a databáze do katalogu klienta. Tyto informace se později používá ke konfiguraci prostředí zrcadlený obraz v oblasti obnovení.

> [!IMPORTANT]
> Pro zjednodušení procesu synchronizace a dalších dlouho běžící obnovení a procesy navracení se implementují ve tyto ukázky jako místní prostředí PowerShell úlohy nebo relace, které běží pod vaše přihlášení uživatele klienta. Tokeny ověřování vydán při přihlášení vyprší po několik hodin, a pak úloh nezdaří. V případě produkční dlouho běžící procesy by měla být implementována jako spolehlivé služby Azure určitého druhu spuštěna pod hlavní název služby. V tématu [použití Azure PowerShell k vytvoření objektu služby pomocí certifikátu](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authenticate-service-principal). 

1. V integrovaném Skriptovacím prostředí PowerShell otevřete soubor Modules\UserConfig.psm1 ...\Learning. Nahraďte `<resourcegroup>` a `<user>` na řádky 10 a 11 hodnota použitá při nasazení aplikace. Uložte soubor.

2. V integrovaném Skriptovacím prostředí PowerShell otevřete ...\Learning Modules\Business kontinuity a po havárii Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1 skriptu.

    V tomto kurzu spuštění každého ze scénářů v tento skript prostředí PowerShell, takže zachovat tento soubor otevřít.

3. Nastavte následující:

    $DemoScenario = 1: spuštění úlohu na pozadí, který je synchronizován serveru klienta a informace o konfiguraci fondu do katalogu.

4. Chcete-li spustit skript synchronizace, vyberte F5. 

    Tyto informace se později používá k zajištění, že obnovení vytvoří zrcadlený obraz serverů, fondy a databáze v oblasti obnovení.  

    ![Procesu synchronizace](media/saas-dbpertenant-dr-geo-restore/sync-process.png)

Ponechte okno prostředí PowerShell spuštěná na pozadí a další tohoto kurzu.

> [!NOTE]
> Procesu synchronizace se připojí ke katalogu prostřednictvím DNS alias. Během obnovení a navracení tak, aby odkazoval na aktivní katalogu je upraven alias. Procesu synchronizace udržuje katalogu aktuální s databáze nebo fond změny konfigurace provedené v oblasti obnovení. Během navracení tyto změny se použijí na ekvivalentní prostředky v původní oblasti.

## <a name="geo-restore-recovery-process-overview"></a>Přehled procesu obnovení geografické obnovení

Proces obnovení geografické obnovení nasadí aplikaci a obnoví databáze do oblasti obnovení ze zálohy.

Proces obnovení provede následující akce:

1. Zakáže koncový bod Azure Traffic Manager pro webovou aplikaci v původní oblasti. Zakázání koncového bodu zabraňuje uživatelům v připojení k aplikaci v neplatném stavu oblasti původní pocházet online během obnovení.

2. Zřizuje a obnovení katalogu serveru v oblasti obnovení geografické obnovení databáze katalogu a aktualizuje alias activecatalog tak, aby odkazoval na server obnovený katalogu. Změna alias katalogu zajišťuje, že proces synchronizace katalogu vždy synchronizuje ke katalogu aktivní.

3. Označí všechny stávající klienty v katalogu obnovení jako v režimu offline chcete zabránit přístupu do klienta databáze, před jejich obnovení.

4. Instance aplikace v oblasti obnovení poskytuje a konfiguruje ho na použití katalogu obnovené v této oblasti. Aby latence na minimum ukázková aplikace umožňuje vždy připojí k databázi klienta ve stejné oblasti.

5. Zřídí server a elastického fondu, ve které jsou zřízené nové klienty. Vytváření těchto prostředků zajistí, že zřizování nové klienty není v konfliktu se obnovení tenantům.

6. Aktualizuje nový alias klienta tak, aby odkazoval na server pro nové databáze klienta v oblasti obnovení. Změna tento alias zajistí, že je databáze pro všechny nové klienty zřízený v oblasti obnovení.
        
7. Zřizuje servery a elastické fondy v oblasti obnovení pro obnovení databáze klienta. Tyto servery a fondy jsou zrcadlený obraz konfigurace v původní oblast. Zřizování fondy předem si vyhrazuje kapacity potřebné k obnovení všech databází.

    Výpadek v oblasti může umístit významné tlak na dostupné prostředky v oblasti spárované. Pokud byste tedy spoléhat na geografické obnovení pro zotavení po Havárii, pak rychle rezervování prostředků je vhodné. Geografická replikace zvažte Pokud je velmi důležité, aby aplikace je obnovena v určité oblasti. 

8. Umožňuje koncový bod Traffic Manager pro webovou aplikaci v oblasti obnovení. Povolení tento koncový bod umožňuje aplikaci zřídit nové klienty. V této fázi existující klienti jsou stále v režimu offline.

9. Odešle dávky žádostí o obnovení databází v pořadí podle priority. 

    * Dávky jsou uspořádány tak, aby databáze jsou obnovovat paralelně mezi všechny fondy.  

    * Obnovení požadavky jsou odeslán asynchronně, takže jsou odeslána rychle a zařazených do fronty pro spuštění v každém fondu.

    * Protože žádosti o obnovení se zpracovávají paralelně mezi všechny fondy, je lepší distribuci důležité klientů přes mnoho fondů. 

10. Monitoruje službu SQL Database k určení, kdy jsou obnovit databáze. Po obnovení databáze klienta, je označena v katalogu online a se zaznamenává součet rowversion pro databázi klienta. 

    * Klient databáze byla přístupná pomocí aplikace hned, jak jsou označeny v katalogu online.

    * Součet hodnot rowversion v databázi klienta je uložené v katalogu. Tento součet funguje jako otisků prstů, která umožňuje proces navracení k určení, pokud v oblasti obnovení byla aktualizována databázi.       

## <a name="run-the-recovery-script"></a>Spusťte skript obnovení

> [!IMPORTANT]
> V tomto kurzu obnoví databáze z geograficky redundantní zálohy. I když tyto zálohy jsou obvykle dostupné v rámci 10 minut, může trvat až jednu hodinu. Skript se pozastavuje, dokud jsou k dispozici.

Představte si, že v oblasti, ve kterém je aplikace nasazena a spusťte skript obnovení výpadek:

1. Ve skriptu nástroje ...\Learning Modules\Business kontinuitu a Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1 po havárii v PowerShell ISE nastavte následující hodnotu:

    $DemoScenario = 2: obnovení aplikace do oblasti obnovení obnovením z geograficky redundantní zálohy.

2. Chcete-li spustit skript, vyberte F5.  

    * Skript se otevře v novém okně prostředí PowerShell a pak spustí sadu úlohy prostředí PowerShell, které spouštějí paralelně. Tyto úlohy obnovení serverů, fondy a databáze do oblasti obnovení.

    * Spárované oblast přidruženou oblasti Azure, ve kterém je aplikace nasazena, je oblast pro obnovení. Další informace najdete v tématu [Azure spárovat oblasti](https://docs.microsoft.com/azure/best-practices-availability-paired-regions). 

3. Monitorování stavu procesu obnovení v okně prostředí PowerShell.

    ![Proces obnovení](media/saas-dbpertenant-dr-geo-restore/dr-in-progress.png)

> [!NOTE]
> Chcete-li prozkoumat kód pro úlohy obnovení, zkontrolujte skriptů prostředí PowerShell ve složce Modules\Business kontinuitu a po havárii Recovery\DR-RestoreFromBackup\RecoveryJobs ...\Learning.

## <a name="review-the-application-state-during-recovery"></a>Zkontrolujte stav aplikace během obnovení
Když koncový bod aplikace je v Traffic Manageru zakázán, aplikace není k dispozici. Je obnoven katalogu a všechny klienty, které jsou označeny offline. Koncový bod aplikace v oblasti obnovení je pak povolená a aplikace je zpět do režimu online. I když aplikace je k dispozici, klienti se offline v Centru událostí, dokud se obnovit své databáze. Je důležité při návrhu vaší aplikace pro zpracování databáze offline klienta.

* Po katalogu byla databáze obnovena, ale předtím, než klienti zpět do režimu online, aktualizujte Centrum událostí Wingtip lístky ve webovém prohlížeči.

    * V zápatí, Všimněte si teď obsahuje název serveru katalogu příponu – obnovení a se nachází v oblasti obnovení.

    * Všimněte si, že klienty, kteří ještě nejsou obnoveny jsou označeny jako offline a se nedá vybrat.   
 
    ![Proces obnovení](media/saas-dbpertenant-dr-geo-restore/events-hub-tenants-offline-in-recovery-region.png)    

    * Pokud otevřete stránku události klienta přímo při klienta je v režimu offline, na stránce se zobrazuje oznámení klienta do režimu offline. Například pokud Contoso vzájemné součinnosti Hall offline, pokusí otevřít http://events.wingtip-dpt.&lt; Uživatel&gt;.trafficmanager.net/contosoconcerthall.

    ![Proces obnovení](media/saas-dbpertenant-dr-geo-restore/dr-in-progress-offline-contosoconcerthall.png)

## <a name="provision-a-new-tenant-in-the-recovery-region"></a>Zřízení nového klienta v oblasti obnovení
Ještě dříve, než jsou obnovit databáze klienta, můžete zřídit nové klienty v oblasti obnovení. Nové databáze klienta, které jsou zřízené v oblasti obnovení jsou repatriated s obnovenou databází později.   

1. V prostředí PowerShell ISE ve ...\Learning Modules\Business kontinuitu a po havárii Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1 skriptu, nastavte následující vlastnost:

    $DemoScenario = 3: zřízení nového klienta v oblasti obnovení.

2. Chcete-li spustit skript, vyberte F5.

3. Při zřizování dokončeno, otevře se stránka události Hawthorn Hall v prohlížeči. 

    Všimněte si, že Hawthorn Hall databáze se nachází v oblasti obnovení.

    ![Hawthorn Hall zřízené v oblasti obnovení](media/saas-dbpertenant-dr-geo-restore/hawthorn-hall-provisioned-in-recovery-region.png)

4. V prohlížeči aktualizujte stránku centra událostí Wingtip lístky zobrazíte Hawthorn Hall zahrnuté. 

    I když jste zřídili Hawthorn Hall bez čekání na další klienty k obnovení, může být offline ostatních klientů.

## <a name="review-the-recovered-state-of-the-application"></a>Zkontrolujte obnovené stav aplikace

Po dokončení procesu obnovení, aplikace a všichni klienti jsou plně funkční v oblasti obnovení. 

1. Po zobrazení v okně konzoly prostředí PowerShell znamená, že se obnoví všechny klienty, aktualizujte Centrum událostí. 

    Online, včetně nového klienta, Hawthorn Hall se zobrazí všechny klienty.

    ![obnovená a nové klienty v Centru událostí](media/saas-dbpertenant-dr-geo-restore/events-hub-with-hawthorn-hall.png)

2. Klikněte na vzájemné součinnosti Hall Contoso a otevřete stránku s jeho události. 

    V zápatí Všimněte si, že se databáze nachází na serveru pro obnovení, který je umístěný v oblasti obnovení.

    ![Contoso v oblasti obnovení](media/saas-dbpertenant-dr-geo-restore/contoso-recovery-location.png)

3. V [portál Azure](https://portal.azure.com), otevře se seznam skupin prostředků.  

    Všimněte si skupinu prostředků, která jste nasadili, a skupina prostředků obnovení, s příponou – obnovení. Skupiny prostředků pro obnovení obsahuje všechny prostředky vytvořené během procesu obnovení, plus nové prostředky, které se vytvoří během se výpadek. 

4. Otevřete skupinu prostředků, obnovení a Všimněte si následující položky:

    * Verze obnovení serverů katalogu a tenants1 s příponou – obnovení. Obnovená databáze katalogu a klienta na těchto serverech všechny mít názvy používaných v původní oblasti.

    * Tenants2-dpt -&lt;uživatele&gt;-obnovení SQL serveru. Tento server se používá pro zřizování nové klienty během se výpadek.

    * Aplikace služby s názvem události-wingtip-dpt -&lt;recoveryregion&gt;-&lt;uživatele&gt;, což je instance obnovení systému aplikace události.

    ![Contoso prostředky v oblasti obnovení](media/saas-dbpertenant-dr-geo-restore/resources-in-recovery-region.png) 
    
5. Otevřete tenants2-dpt -&lt;uživatele&gt;-obnovení SQL serveru. Všimněte si, že obsahuje hawthornhall databáze a Pool1 elastického fondu. Databázi hawthornhall je nakonfigurovaný jako elastické databáze ve fondu elastické Pool1.

## <a name="change-the-tenant-data"></a>Změna dat klienta 
V této úloze aktualizujete jedna z databází obnovené klienta. Kopie procesu navracení obnovit databáze, které se změnily původní oblasti. 

1. V prohlížeči najít seznamu událostí pro Hall vzájemné součinnosti Contoso, posuňte události a Všimněte si poslední událost, vážně Strauss.

2. Ve skriptu nástroje ...\Learning Modules\Business kontinuitu a Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1 po havárii v PowerShell ISE nastavte následující hodnotu:

    $DemoScenario = 4: odstranění události z klienta v oblasti obnovení.

3. Chcete-li spustit skript, vyberte F5.

4. Aktualizujte stránku události Hall vzájemné součinnosti Contoso (http://events.wingtip-dpt.&lt; Uživatel&gt;.trafficmanager.net/contosoconcerthall) a Všimněte si, že událost vážně Strauss chybí.

V tomto okamžiku v tomto kurzu jste obnovili aplikaci, které je nyní spuštěna v oblasti obnovení. Zřízení nového klienta v oblasti obnovení a upravit data jednoho obnovené klientů.  

> [!NOTE]
> Další kurzy v ukázce nejsou určeny pro provoz aplikace ve stavu obnovení. Pokud chcete prozkoumat další kurzy, je nutné nejprve repatriovat základní aplikace.

## <a name="repatriation-process-overview"></a>Přehled procesu navracení

Proces navracení obnoví aplikace a její databáze pro její původní oblast po se výpadek vyřeší.

![Navracení geografické obnovení](media/saas-dbpertenant-dr-geo-restore/geo-restore-repatriation.png) 

Proces:

1. Zastaví jakékoliv probíhající obnovení činnosti a zruší všechny žádosti o obnovení databáze nezpracovaných nebo během letu.

2. Znovu aktivuje v původní databáze klienta oblasti, které nebyly změněny od se výpadek. Tyto databáze patří těch, které ještě není obnoven a ty obnovit, ale nebyl změněn. potom. Opětovně aktivovaných databáze jsou přesně jako poslední přístup svým klientům.

3. Zřídí zrcadlený obraz k novému klientovi serveru a elastického fondu v původní oblasti. Po dokončení této akce nový alias klienta je aktualizována tak, aby odkazoval na tento server. Aktualizace alias způsobí, že nové registrace klienta v oblasti původní místo oblasti obnovení.

3. Geografická replikace se používá pro přesun katalogu původní oblast z oblasti obnovení.

4. Aktualizace konfigurace fondu v původní oblasti tak, aby byl konzistentní s změny, které byly provedeny v oblasti obnovení během se výpadek.

5. Vytvoří požadované servery a fondy k hostování žádné nové databáze vytvořené během se výpadek.

6. Používá geografická replikace repatriovat základní obnovit klienta, které se po obnovení databáze, které byly aktualizovány a všechny nové databáze klienta zřízenou během se výpadek. 

7. Vyčistí prostředky vytvořené během procesu obnovení v oblasti obnovení.

Pokud chcete omezit počet databází klienta, které musí být repatriated, jsou okamžitě provést kroky 1 až 3.  

Krok 4 se provádí, pouze pokud katalogu v oblasti obnovení byl upraven během se výpadek. Katalog se aktualizuje, pokud jsou vytvořeny nové klienty nebo pokud dojde ke změně konfigurace všechny databáze nebo fondu v oblasti obnovení.

Je důležité, aby krok 7 způsobí, že minimálním dopadem na klienty a dojde ke ztrátě žádná data. K dosažení tohoto cíle, využívá proces geografická replikace.

Před každou databázi geograficky replikované, odpovídající databáze v původní oblast, se odstraní. Databáze v oblasti obnovení je pak geograficky replikované, vytvoření sekundární repliky v původní oblasti. Po dokončení replikace je označena klienta v katalogu, která dělí všechna připojení k databázi v oblasti obnovení offline. Databáze je pak převzít služby při selhání, způsobuje všechny čekající transakce ke zpracování na sekundárním proto žádná data se ztratí. 

Na převzetí služeb při selhání databázové role se vrátit zpět. Sekundární v oblasti původní bude primární databáze pro čtení a zápis a databáze v oblasti obnovení bude sekundární jen pro čtení. Klienta položku v katalogu je aktualizovat tak, aby odkazovaly na databázi v původní oblasti a klienta je označené online. V tomto okamžiku navracení databáze je dokončena. 

Aplikace by měly být zapsány s logikou opakování užitečnou při Ujistěte se, že se znovu připojit automaticky při připojení jsou přerušená. Při použití katalogu na zprostředkovatel obnovení připojení, jejich připojení k databázi repatriated v původní oblasti. I když je často není si všimli stručný odpojení, můžete zvolit repatriovat základní databáze mimo pracovní dobu.

Po databáze je repatriated, sekundární databázi v oblasti obnovení nelze odstranit. Databáze v oblasti původní pak využívá znovu geografické obnovení pro ochranu zotavení po Havárii.

V kroku 8 se odstraní prostředky v oblasti obnovení, včetně obnovení serverů a fondů.

## <a name="run-the-repatriation-script"></a>Spusťte skript navracení
Představte si můžeme je vyřešen a spusťte skript navracení se výpadek.

Pokud jste postupovali podle kurzu, skript okamžitě znovu aktivuje křížovou Fabrikam Jazz kartou a svída Dojo v původní oblasti, protože jsou beze změny. Potom repatriates k novému klientovi Hawthorn Hall a vzájemné součinnosti Hall Contoso, protože byla změněna. Skript také repatriates katalogu, která byla aktualizována při zřizování Hawthorn Hall.
  
1. V prostředí PowerShell ISE v ...\Learning Modules\Business kontinuity a po havárii Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1 skriptu ověřte, zda procesu synchronizace katalogu je stále spuštěna v jeho instance prostředí PowerShell. V případě potřeby restartujte nastavením:

    $DemoScenario = 1: spuštění synchronizace serveru klienta, fondu a informace o konfiguraci databáze do katalogu.

    Chcete-li spustit skript, vyberte F5.

2.  Potom zahájíte proces navracení nastavte:

    $DemoScenario = 5: repatriovat základní aplikace do jeho původní oblast.

    Chcete-li spustit skript obnovení v novém okně prostředí PowerShell, vyberte F5. Navracení trvá několik minut a lze sledovat v okně prostředí PowerShell.

3. Je spuštěn skript, aktualizujte stránku centra událostí (http://events.wingtip-dpt.&lt; Uživatel&gt;. trafficmanager.net).

    Všimněte si, že všichni klienti jsou online a dostupné v průběhu tohoto procesu.

4. Vyberte křížovou kartou Jazz Fabrikam ho otevřete. Pokud změníte nebyla tohoto klienta, oznámení z zápatí, zda je server již vrátit zpět na původní server.

5. Otevřete nebo aktualizujte stránku události Hall vzájemné součinnosti Contoso. Všimněte si ze zápatí, že na začátku databázi je uložena na serveru – obnovení. 

6. Aktualizujte stránku Hall vzájemné součinnosti Contoso události, až se dokončí proces vrácení a Všimněte si, že databáze je nyní ve vašem regionu původní.

7. Znovu aktualizujte centrem událostí a otevřete Hawthorn Hall. Všimněte si, že jeho databáze nachází taky v původní oblasti. 

## <a name="clean-up-recovery-region-resources-after-repatriation"></a>Vyčištění prostředků oblasti obnovení po vrácení
Po dokončení navrácení je bezpečné odstranit prostředky v oblasti obnovení. 

> [!IMPORTANT]
> Odstraňte tyto prostředky neprodleně o zastavení všech fakturace pro ně.

Proces obnovení vytvoří všechny prostředky obnovení ve skupině prostředků pro obnovení. Proces vyčištění odstraní tato skupina prostředků a odebere všechny odkazy na prostředky z katalogu. 

1. V prostředí PowerShell ISE ve ...\Learning skriptu Modules\Business kontinuitu a Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1 po havárii nastavte:
    
    $DemoScenario = 6: odstranit zastaralé prostředky z oblasti obnovení.

2. Chcete-li spustit skript, vyberte F5.

Po čištění skripty, aplikace je zpátky kde jeho spuštění. V tomto okamžiku můžete znovu spustit skript nebo vyzkoušet ostatní kurzy.

## <a name="designing-the-application-to-ensure-that-the-app-and-the-database-are-co-located"></a>Navrhování aplikací zajistit, že aplikace a databáze jsou společně umístěné 
Aplikace je navržená ke z instance ve stejné oblasti jako databáze klienta se vždy připojují. Tento návrh snižuje latence mezi aplikací a databáze. Tato optimalizace předpokládá, že aplikace do databáze interakce je chattier než interakce uživatele aplikace.  

Databáze klienta může být rozloženy obnovení a oblastí, původní nějakou dobu během navracení. Pro každou databázi vyhledá aplikace oblast, ve kterém je databáze umístěna pomocí tohoto postupu vyhledání DNS u klientů název serveru. V databázi SQL je název serveru alias. Název serveru alias obsahuje název oblasti. Pokud aplikace není ve stejné oblasti jako databáze, je přesměrován na instanci ve stejné oblasti jako databázového serveru. Přesměrování na instanci ve stejné oblasti jako databáze minimalizuje latenci mezi aplikace a databáze.  

## <a name="next-steps"></a>Další postup

V tomto kurzu jste se naučili:
> [!div class="checklist"]

>* Použití katalogu klienta pro uložení konfigurace pravidelně aktualizovat informace, které umožňují obnovení prostředí zrcadlený obraz má být vytvořen v jiné oblasti.
>* Obnovení databází Azure SQL do oblasti obnovení pomocí geografické obnovení.
>* Aktualizace katalogu klienta tak, aby odrážela umístění klienta obnovené databáze. 
>* Povolit aplikaci připojit ke katalogu klienta v rámci bez změny konfigurace pomocí DNS alias.
>* Geografická replikace použijte repatriovat základní obnovené databáze do jejich původního oblasti po výpadku je vyřešený.

Zkuste [zotavení po havárii pro víceklientské aplikace SaaS pomocí geografická replikace databáze](saas-dbpertenant-dr-geo-replication.md) kurzu se dozvíte, jak používat geografická replikace výrazně zkrátit čas potřebný k obnovení ve velkém měřítku víceklientské aplikace.

## <a name="additional-resources"></a>Další zdroje informací:

[Další kurzy, které stavět na adresář Wingtip SaaS aplikace](https://docs.microsoft.com/azure/sql-database/sql-database-wtp-overview#sql-database-wingtip-saas-tutorials)
