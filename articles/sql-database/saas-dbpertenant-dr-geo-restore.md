---
title: Zotavení po havárii pro aplikace SaaS využívající geograficky redundantní zálohy databáze Azure SQL Database | Microsoft Docs
description: Naučte se používat Azure SQL Database geograficky redundantní zálohy obnovit víceklientské aplikace SaaS v případě výpadku
keywords: kurz k sql database
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.custom: saas apps
ms.topic: article
ms.date: 04/16/2018
ms.author: ayolubek
ms.openlocfilehash: a677e6eb583e293f83df824804aa4cd6f8f5d778
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2018
---
# <a name="recover-a-multi-tenant-saas-application-using-geo-restore-from-database-backups"></a>Obnovit víceklientské aplikace SaaS pomocí geografické obnovení ze zálohy databáze

V tomto kurzu prozkoumejte úplné zotavení po havárii pro víceklientské aplikace SaaS implementovaná pomocí modelu databáze za klienta. Používáte [ _geografické obnovení_ ](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-recovery-using-backups) k obnovení databází katalogu a klientů z automaticky zachována geograficky redundantní zálohy do oblast alternativní obnovení. Když se výpadek vyřešen, můžete použít [ _geografická replikace_ ](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-geo-replication-overview) repatriovat základní změněné databáze do jejich původního oblasti.

![Architektura geografické obnovení](media/saas-dbpertenant-dr-geo-restore/geo-restore-architecture.png)

Geografické obnovení je řešení zotavení po havárii nejnižší náklady pro databáze SQL.  Ale obnovení z geograficky redundantní zálohy může dojít ke ztrátě dat až jednu hodinu a může trvat delší dobu, v závislosti na velikosti jednotlivých databází. **Chcete-li obnovit aplikací s nejnižší možné plánovaný bod obnovení a RTO, použijte geografická replikace místo geografické obnovení**.

V tomto kurzu prozkoumá obnovení a navracení pracovních postupů. Získáte informace o těchto tématech:
> [!div class="checklist"]

>* Databáze Sync a informace o konfiguraci elastického fondu do katalogu klienta
>* Nastavení prostředí zrcadlený obraz v oblasti "využití", která obsahuje aplikace, servery a fondy    
>* Obnovení databáze katalogu a klienta pomocí _geografické obnovení_
>* Repatriovat základní katalogu klienta a databází změněné klienta pomocí _geografická replikace_ po vyřešení se výpadek
>* Aktualizovat katalog každou databázi obnovit (nebo repatriated) sledovat aktuální umístění aktivní kopie databáze každého klienta
>* Ujistěte se, databázi aplikace a klient vždy společně umístěné ve stejné oblasti Azure ke snížení latence  
 

Před zahájením tohoto kurzu, zkontrolujte, zda že jsou dokončit následující požadavky:
* Databáze SaaS lístky Wingtip za klienta aplikace se nasazuje. Nasazení za méně než pět minut najdete v tématu [nasazení a seznamte se s SaaS lístky Wingtip databáze za klienta aplikace](saas-dbpertenant-get-started-deploy.md)  
* Je nainstalované prostředí Azure PowerShell. Podrobnosti najdete v článku [Začínáme s prostředím Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

## <a name="introduction-to-the-geo-restore-recovery-pattern"></a>Úvod do vzoru obnovení geografické obnovení

Zotavení po havárii (DR) je důležitá poznámka pro mnoho aplikací pro dodržování předpisů nebo kontinuity podnikových procesů. Mělo by být výpadku provozu v případě delších služeb, dobře připravený plán zotavení po Havárii minimalizovat narušení firmy. Plán A zotavení po Havárii založené na geografické obnovení musí provést několik cíle:
 * Rezervujte všech potřebných kapacity v oblasti zvolené obnovení co nejrychleji aby se zajistilo, že je k dispozici pro obnovení databází klienta.
 * Vytvoření a obnovení prostředí zrcadlený obraz, původní konfigurace fondu a databáze, který 
 * Musí být možné zrušit v procesu obnovení v polovině letu, pokud je původní oblast dodává opět online.
 * Povolit rychlé zřizování, nové registrace klienta můžete restartovat co nejdříve klienta  
 * Optimalizované pro obnovení klienty v pořadí podle priority
 * Optimalizované pro získání klientů online co nejdříve provedením kroků v paralelní tam, kde je to praktické
 * Být odolné vůči selhání, dá se restartovat a idempotent
 * Repatriovat základní databáze do jejich původního oblasti s minimálním dopadem na klienty, když se se výpadek vyřeší.  

> [!Note]
> Aplikace je obnovena do _spárované oblast_ oblasti, ve kterém je aplikace nasazená. Další informace najdete v tématu [Azure spárovat oblasti](https://docs.microsoft.com/en-us/azure/best-practices-availability-paired-regions).   



Tyto problémy řešeny v tomto kurzu, pomocí funkce Azure SQL Database a platformy Azure:

* [Šablony Azure Resource Manageru](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-create-first-template), co nejrychleji rezervovat všechny potřebné kapacity. Šablony Azure Resource Manageru se používají ke zřízení zrcadlený obraz původní serverů a elastické fondy v oblasti obnovení. Samostatný server a fondu jsou také vytvořili pro zajišťování nové klienty. 
* [Klientská knihovna pro elastické databáze](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-elastic-database-client-library) (EDCL) pro vytváření a údržbu databáze katalog klienta.  Katalog je rozšířen o pravidelně aktualizovat informace o konfiguraci fondu a databáze.
* [Funkce obnovení správy horizontálního oddílu](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-database-recovery-manager) z EDCL k udržování databáze umístění položky v katalogu během obnovení a navracení.  
* [Geografické obnovení](https://docs.microsoft.com/azure/sql-database/sql-database-disaster-recovery), k obnovení databází katalogu a klientů z automaticky zachována geograficky redundantní zálohy. 
* [Operace asynchronní obnovení](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations) odesílají v pořadí podle priority klienta, které jsou zařazeny do fronty pro každý fond v systému a zpracovány v dávkách tak fondu není přetížený. Tato operace se dají zrušit před nebo během provádění v případě potřeby.    
* [Geografická replikace](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-overview), repatriovat základní databáze na původní oblast po se výpadek. Použití geografická replikace zajišťuje, že žádné ztrátě dat a minimální dopad na klienta.
* [SQL server DNS aliasy](https://docs.microsoft.com/azure/sql-database/dns-alias-overview) umožňující procesu synchronizace katalogu pro připojení ke katalogu active bez ohledu na jeho umístění.  

## <a name="get-the-disaster-recovery--scripts"></a>Získat skripty pro zotavení po havárii 

Zotavení po Havárii skripty použité v tomto kurzu jsou k dispozici v [SaaS lístky Wingtip databáze za klienta úložiště GitHub](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant). Podívejte se [obecné pokyny](saas-tenancy-wingtip-app-guidance-tips.md) kroky, jak stáhnout a odblokování skripty správy Wingtip lístků.
> [!IMPORTANT]
> Podobně jako všechny lístky Wingtip správu skripty skripty zotavení po Havárii jsou ukázka kvality a není určen k použití v produkčním prostředí.   

## <a name="review-the-healthy-state-of-the-application"></a>Kontrola stavu v pořádku aplikace
Před zahájením procesu obnovení, zkontrolujte stav v pořádku normální aplikace.
1. Ve webovém prohlížeči, otevřete Centrum událostí lístky Wingtip (http://events.wingtip-dpt.&lt; Uživatel&gt;. trafficmanager.net - nahradit &lt;uživatele&gt; s hodnotou uživatele vašeho nasazení).
    * Přejděte do dolní části stránky a Všimněte si katalogu serveru název a umístění v zápatí.  Umístění je oblast, ve kterém je aplikace nasazená.    
        TIP: najeďte myší umístění zvětšení zobrazení.

    ![Události v pořádku stav rozbočovače v původní oblasti](media/saas-dbpertenant-dr-geo-restore/events-hub-original-region.png)

1. Klikněte na klienta Hall vzájemné součinnosti Contoso a otevřete stránku s jeho událostí.
    * V zápatí Všimněte si, název serveru klientů. Umístění bude stejná jako server katalogu umístění.

    ![Původní oblast Hall vzájemné součinnosti contoso](media/saas-dbpertenant-dr-geo-restore/contoso-original-location.png)    
1. V [portál Azure](https://portal.azure.com)zkontrolujte a otevřete skupinu prostředků, ve kterém je aplikace nasazená.
    * Všimněte si, prostředky a oblasti, ve které jsou nasazeny komponenty služby aplikací a serverů SQL Database.

## <a name="sync-tenant-configuration-into-catalog"></a>Konfiguraci synchronizace klienta do katalogu

V této úloze spustí se proces pro synchronizaci konfigurace serverů, elastické fondy a databáze do katalogu klienta.  Tyto informace se později používá ke konfiguraci prostředí zrcadlený obraz v oblasti obnovení.

> [!IMPORTANT]
> Pro zjednodušení procesu synchronizace a dalších dlouho běžící procesy obnovení a navracení se implementují ve tyto ukázky jako místní prostředí Powershell úlohy nebo relace, které běží pod vaše přihlášení uživatele klienta. Tokeny ověřování při přihlášení vyprší po několik hodin a pak se nezdaří úlohy. V případě produkční dlouho běžící procesy by měla být implementována jako spolehlivé služby Azure určitého druhu spuštěna pod hlavní název služby. V tématu [použití Azure PowerShell k vytvoření objektu služby pomocí certifikátu](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-authenticate-service-principal). 

1. V _prostředí PowerShell ISE_, otevřete soubor Modules\UserConfig.psm1 ...\Learning. Nahraďte `<resourcegroup>` a `<user>` na řádky 10 a 11 hodnota použitá při nasazení aplikace.  Uložte soubor!

2. V *prostředí PowerShell ISE*, otevřete ...\Learning Modules\Business kontinuity a po havárii Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1 skriptu.
    *  Během tohoto kurzu budete spouštět každý scénáře v této prostředí PowerShell skriptu tak zachovat tento soubor otevřít.

3. Nastavte následující:
    * **$DemoScenario = 1**, spusťte úlohu na pozadí, který je synchronizován serveru klienta a informace o konfiguraci fondu do katalogu

3. Stiskněte klávesu **F5** pro spuštění skriptu synchronizace. 
    *  Tyto informace se později používá k zajištění, že obnovení vytvoří zrcadlený obraz serverů, fondy a databáze v oblasti obnovení.  
![Procesu synchronizace](media/saas-dbpertenant-dr-geo-restore/sync-process.png)

Ponechte okno prostředí PowerShell spuštěná na pozadí a další tohoto kurzu.

> [!Note]
> Procesu synchronizace se připojí ke katalogu prostřednictvím DNS alias. Během obnovení a navracení tak, aby odkazoval na aktivní katalogu je upraven alias. Procesu synchronizace udržuje katalogu v aktuálním stavu pomocí databáze nebo fond změny konfigurace provedené v oblasti obnovení.  Během navracení tyto změny se použijí na ekvivalentní prostředky v původní oblasti.

## <a name="geo-restore-recovery-process-overview"></a>Přehled procesu obnovení geografické obnovení

Proces obnovení geografické obnovení nasadí aplikaci a obnoví databáze do oblasti obnovení ze zálohy.

Proces obnovení provede následující akce:

1. Zakáže koncový bod Traffic Manager pro webovou aplikaci v původní oblasti. Zakázání koncového bodu zabraňuje uživatelům v připojení k aplikaci v neplatném stavu oblasti původní pocházet online během obnovení.

1. Zřizuje katalogu server pro obnovení v oblasti obnovení geografické obnovení databáze katalogu a aktualizací _activecatalog_ alias tak, aby odkazoval na server obnovený katalogu.  
    * Změna alias katalogu zajišťuje, že proces synchronizace katalogu vždy synchronizuje se ke katalogu aktivní.

1. Označí všechny stávající klienty v katalogu obnovení jako v režimu offline chcete zabránit přístupu do klienta databáze, před jejich obnovení.

1. Instance aplikace v oblasti obnovení poskytuje a konfiguruje ho na použití katalogu obnovené v této oblasti.
    * Aby latence na minimum je ukázková aplikace navržené tak, aby vždycky připojuje k databázi klienta ve stejné oblasti.

1. Zřídí server a elastického fondu, ve které se budou zřizovat nové klienty. Vytváření těchto prostředků zajistí, že zřizování nové klienty není v konfliktu se obnovení tenantům.

1. Aktualizuje alias nového klienta tak, aby odkazoval na server pro nové databáze klienta v oblasti obnovení. Změna tento alias zajistí, že je databáze pro všechny nové klienty zřízený v oblasti obnovení.
        
1. Zřizuje servery a elastické fondy v oblasti obnovení pro obnovení databáze klienta. Tyto servery a fondy jsou zrcadlený obraz konfigurace v původní oblast.  Zřizování fondy počáteční si vyhrazuje kapacity potřebné k obnovení všech databází.
    * Výpadek v oblasti může umístit významné tlak na dostupné prostředky v oblasti spárované.  Pokud byste tedy spoléhat na geografické obnovení pro zotavení po Havárii, pak rychle rezervování prostředků je vhodné. Zvažte použití geografická replikace, pokud je velmi důležité, že aplikace je nutné obnovit v určité oblasti. 

1. Umožňuje koncový bod Traffic Manager pro webovou aplikaci v oblasti obnovení. Povolení tento koncový bod umožňuje aplikaci zřídit nové klienty. V této fázi existující klienti jsou stále v režimu offline.

1. Odešle dávky žádostí o obnovení databází v pořadí podle priority. 
    * Dávky jsou uspořádány tak, aby databáze jsou obnovovat paralelně mezi všechny fondy.  
    * Obnovení požadavky jsou odeslán asynchronně, takže jsou odeslána rychle a zařazených do fronty pro spuštění v každém fondu.
    * Protože žádosti o obnovení se zpracovávají paralelně mezi všechny fondy, je lepší distribuci důležité klientů přes mnoho fondů. 

1. Monitoruje službu SQL database k určení, kdy jsou obnovit databáze. Po obnovení databáze klienta je označené v katalogu online a se zaznamenává součet rowversion pro databázi klienta. 
    * Klient databáze byla přístupná pomocí aplikace hned, jak jsou označeny v katalogu online.
    * Součet hodnot rowversion v databázi klienta je uložené v katalogu. Tento součet funguje jako otisků prstů, která umožňuje proces navracení k určení, zda databáze byl aktualizován v oblasti obnovení.      

## <a name="run-the-recovery-script"></a>Spusťte skript obnovení

> [!IMPORTANT]
> V tomto kurzu obnoví databáze z geograficky redundantní zálohy. I když tyto zálohy jsou obvykle dostupné v rámci 10 minut, může trvat až jednu hodinu, než jsou k dispozici. Skript se pozastaví, než jsou k dispozici.  Doba nutná k získání kávy!

Nyní Představte si v oblasti, ve kterém je aplikace nasazena a spusťte skript obnovení je výpadek:

1. V *prostředí PowerShell ISE*, v ...\Learning Modules\Business kontinuity a po havárii Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1 skriptu, nastavte následující hodnoty:
    * **$DemoScenario = 2**, obnovit aplikace do oblasti obnovení tím, že obnovení ze zálohy geograficky redundantní

1. Stisknutím klávesy **F5** spusťte skript.  
    * Skript se otevře v novém okně prostředí PowerShell a pak spustí sadu úlohy prostředí PowerShell, které spouštějí paralelně.  Tyto úlohy obnovení serverů, fondy a databáze do oblasti obnovení. 
    * Oblast pro obnovení je _spárované oblast_ přidružené k oblasti Azure, ve kterém je aplikace nasazena. Další informace najdete v tématu [Azure spárovat oblasti](https://docs.microsoft.com/en-us/azure/best-practices-availability-paired-regions). 

1. Monitorování stavu procesu obnovení v okně prostředí PowerShell.

    ![Proces obnovení](media/saas-dbpertenant-dr-geo-restore/dr-in-progress.png)

>Chcete-li prozkoumat kód pro úlohy obnovení, zkontrolujte skriptů prostředí PowerShell ve složce Modules\Business kontinuitu a po havárii Recovery\DR-RestoreFromBackup\RecoveryJobs ...\Learning.

## <a name="review-the-application-state-during-recovery"></a>Zkontrolujte stav aplikace během obnovení
Když koncový bod aplikace je v Traffic Manageru zakázán, aplikace není k dispozici. Je obnoven katalogu a všechny klienty, které jsou označeny offline.  Koncový bod aplikace v oblasti obnovení je pak povolená a aplikace je zpět do režimu online. I když aplikace je k dispozici, klienti se offline v Centru událostí, dokud se obnovit své databáze. Je důležité při návrhu vaší aplikace pro zpracování databáze offline klienta.

1. Po katalogu byla databáze obnovena, ale předtím, než klienti zpět do režimu online, aktualizujte Centrum událostí lístky Wingtip ve webovém prohlížeči.
    * V zápatí, Všimněte si teď obsahuje název serveru katalogu _-obnovení_ přípona a se nachází v oblasti obnovení.
    * Všimněte si, že klienty, kteří ještě nejsou obnoveny jsou označeny jako offline a se nedá vybrat.   
 
    ![Proces obnovení](media/saas-dbpertenant-dr-geo-restore/events-hub-tenants-offline-in-recovery-region.png)    

    * Pokud otevřete stránku události klienta přímo při klienta je v režimu offline, na stránce se zobrazuje oznámení klienta na offline. Například pokud Contoso vzájemné součinnosti Hall offline, pokusí otevřít http://events.wingtip-dpt.&lt; Uživatel&gt;.trafficmanager.net/contosoconcerthall ![proces obnovení](media/saas-dbpertenant-dr-geo-restore/dr-in-progress-offline-contosoconcerthall.png)

## <a name="provision-a-new-tenant-in-the-recovery-region"></a>Zřízení nového klienta v oblasti obnovení
Ještě dříve, než jsou obnovit databáze klienta, můžete zřídit nové klienty v oblasti obnovení. Nové databáze klienta, které jsou zřízené v oblasti obnovení bude repatriated s obnovenou databází později.   

1. V *prostředí PowerShell ISE*, v ...\Learning Modules\Business kontinuity a po havárii Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1 skriptu, nastavte následující vlastnost:
    * **$DemoScenario = 3**, zřízení nového klienta v oblasti obnovení

1. Stisknutím klávesy **F5** spusťte skript. 

1. Po dokončení zřizování, otevře se stránka události Hawthorn Hall v prohlížeči. 
    * Všimněte si, že Hawthorn Hall databáze se nachází v oblasti obnovení.
    ![Hawthorn Hall zřízené v oblasti obnovení](media/saas-dbpertenant-dr-geo-restore/hawthorn-hall-provisioned-in-recovery-region.png)

1. V prohlížeči aktualizujte stránku Wingtip lístky události rozbočovače zobrazíte Hawthorn Hall zahrnuté. 
    * Pokud jste zřídili Hawthorn Hall bez čekání na další klienty k obnovení, může být offline ostatních klientů.

## <a name="review-the-recovered-state-of-the-application"></a>Zkontrolujte obnovené stav aplikace

Po dokončení procesu obnovení, aplikace a všichni klienti jsou plně funkční v oblasti obnovení. 

1. Po zobrazení v okně konzoly prostředí PowerShell znamená, že se obnoví všechny klienty, aktualizujte Centrum událostí.  Klienti se zobrazí všechny online, včetně nového klienta, Hawthorn Hall.

    ![obnovená a nové klienty v Centru událostí](media/saas-dbpertenant-dr-geo-restore/events-hub-with-hawthorn-hall.png)

1. Klikněte na vzájemné součinnosti Hall Contoso a otevřete stránku s jeho události.
    * V zápatí Všimněte si, že databáze se nachází na serveru pro obnovení, který je umístěný v oblasti obnovení.

    ![Contoso v oblasti obnovení](media/saas-dbpertenant-dr-geo-restore/contoso-recovery-location.png)

1. V [portál Azure](https://portal.azure.com), otevře se seznam skupin prostředků.  
    * Všimněte si skupinu prostředků, která jste nasadili, plus skupině prostředků obnovení pomocí _-obnovení_ příponu.  Skupiny prostředků pro obnovení obsahuje všechny prostředky vytvořené během procesu obnovení, plus nové prostředky, které se vytvoří během se výpadek.  

1. Otevřete skupinu prostředků, obnovení a Všimněte si následující položky:
    * Verze obnovení serverů katalogu a tenants1 s _-obnovení_ příponu.  Obnovená databáze katalogu a klienta na těchto serverech všechny mít názvy používaných v původní oblasti.

    * _Tenants2-dpt -&lt;uživatele&gt;-obnovení_ systému SQL server.  Tento server se používá pro zřizování nové klienty během se výpadek.
    *   Aplikace služby s názvem, _události-wingtip-dpt -&lt;recoveryregion&gt;-&lt;uživatele & gt_; což je instance obnovení systému aplikace události. 

    ![Contoso v oblasti obnovení](media/saas-dbpertenant-dr-geo-restore/resources-in-recovery-region.png)   
    
1. Otevřete _tenants2-dpt -&lt;uživatele&gt;-obnovení_ systému SQL server.  Všimněte si obsahuje databázi _hawthornhall_ a elastický fond _Pool1_.  _Hawthornhall_ databáze je nakonfigurovaný jako elastické databáze v _Pool1_ elastického fondu.

## <a name="change-tenant-data"></a>Změna dat klienta 
V této úloze aktualizujete jedna z databází obnovené klienta. Proces navracení zkopíruje obnovené databáze, které se změnily původní oblasti. 

1. V prohlížeči najít seznam události pro vzájemné součinnosti Hall Contoso, posuňte události a poznamenejte si poslední událost _vážně Strauss_.

1. V *prostředí PowerShell ISE*, v ...\Learning Modules\Business kontinuity a po havárii Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1 skriptu, nastavte následující hodnotu:
    * **$DemoScenario = 4**, odstranit událost z klienta v oblasti obnovení
1. Stiskněte klávesu **F5** se spustit skript.
1. Aktualizujte stránku události Hall vzájemné součinnosti Contoso (http://events.wingtip-dpt.&lt; Uživatel&gt;.trafficmanager.net/contosoconcerthall) a Všimněte si, že událost vážně Strauss chybí.

V tomto okamžiku v tomto kurzu jste obnovili aplikaci, které je nyní spuštěna v oblasti obnovení.  Zřízení nového klienta v oblasti obnovení a upravit data jednoho obnovené klientů.  

> [!Note]
> Další kurzy v ukázce nejsou určeny pro provoz aplikace ve stavu obnovení. Pokud chcete prozkoumat další kurzy, je nutné nejprve repatriovat základní aplikace.

## <a name="repatriation-process-overview"></a>Přehled procesu navracení

Proces navracení obnoví aplikace a její databáze pro její původní oblast po se výpadek vyřeší.

![Navracení geografické obnovení](media/saas-dbpertenant-dr-geo-restore/geo-restore-repatriation.png) 


Proces:

1. Zastaví jakékoliv probíhající obnovení činnosti a zruší všechny žádosti o obnovení databáze nezpracovaných nebo během letu.

1. Znovu aktivuje v původní databáze klienta oblasti, které nebyly změněny od se výpadek.  To zahrnuje databáze, které nebyly byla obnovena a databází, které byly obnoveny, ale nebyly později změnit. Opětovně aktivovaných databáze jsou přesně jako poslední přístup svým klientům.

1. Zřídí zrcadlený obraz nové klienty serveru a elastického fondu v původní oblast. Po dokončení této akce alias nového klienta je aktualizována tak, aby odkazoval na tento server. Aktualizace alias způsobí, že nové registrace klienta v oblasti původní místo oblasti obnovení.

1. Pomocí geografická replikace, přesune katalogu původní oblast z oblasti obnovení.

1. Aktualizace konfigurace fondu v původní oblasti tak, aby byl konzistentní s změny provedené v oblasti obnovení během se výpadek.

1. Vytvoří požadované servery a fondy k hostování žádné nové databáze vytvořené během se výpadek.

1. Pomocí geografická replikace, repatriates obnovit klienta, které se po obnovení databáze, které byly aktualizovány a všechny nové databáze klienta zřízenou během se výpadek. 

1. Vyčistí prostředky vytvořené během procesu obnovení v oblasti obnovení.

Pokud chcete omezit počet databází klienta, které musí být repatriated, jsou rychle provést kroky 1 – 3.  

Krok 4 se provádí, pouze pokud katalogu v oblasti obnovení byl upraven během se výpadek. Katalog se aktualizuje, pokud jsou vytvořeny nové klienty nebo pokud dojde ke změně konfigurace všechny databáze nebo fondu v oblasti obnovení.

Je důležité, aby krok 7 způsobí, že minimálním dopadem na klienty a dojde ke ztrátě žádná data. K dosažení tohoto cíle, proces používá _geografická replikace_.

Před každou databázi geograficky replikované, odpovídající databáze v původní oblast, se odstraní. Databáze v oblasti obnovení je pak geograficky replikované, vytvoření sekundární repliky v původní oblasti. Po dokončení replikace je označena klienta v katalogu, která dělí všechna připojení k databázi v oblasti obnovení offline. Databáze je pak převzetí služeb při selhání, způsobuje všechny čekající transakce, které mají být zpracovány na sekundárním, budou ztraceny žádná data. Na převzetí služeb při selhání databázové role se vrátit zpět.  Sekundární v oblasti původní bude primární databáze pro čtení a zápis a databáze v oblasti obnovení bude sekundární jen pro čtení. Klienta položku v katalogu je aktualizovat tak, aby odkazovaly na databázi v původní oblasti a klienta je označen jako online.  V tomto okamžiku navracení databáze je dokončena. 

Aplikace by měly být zapsány s logikou opakování užitečnou při Ujistěte se, že se znovu připojit automaticky při připojení jsou přerušená.  Po novém připojení pomocí katalogu Zprostředkovatel připojení, jejich připojení k databázi repatriated v původní oblasti. I když je často není si všimli stručný odpojení, můžete se rozhodnout repatriovat základní databáze mimo pracovní dobu. 

Jakmile je repatriated databáze, sekundární databázi v oblasti obnovení lze odstranit. Databáze v oblasti původní pak spoléhá na geografické obnovení pro zotavení po Havárii ochranu znovu.

V kroku 8 se odstraní prostředky v oblasti obnovení, včetně obnovení serverů a fondů.

## <a name="run-the-repatriation-script"></a>Spusťte skript navracení
Nyní Pojďme Představte si je vyřešen a spusťte skript navracení se výpadek.

Pokud jste postupovali podle kurzu, skript okamžitě znovu aktivuje křížovou Fabrikam Jazz kartou a svída Dojo v původní oblasti, protože jsou beze změny. Potom repatriates k novému klientovi Hawthorn Hall a vzájemné součinnosti Hall Contoso, protože byla změněna. Skript také repatriates katalogu, která byla aktualizována při zřizování Hawthorn Hall.
  
1. V *prostředí PowerShell ISE*v ...\Learning Modules\Business kontinuity a skript Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1 po havárii.

1. Ověřte, zda procesu synchronizace katalogu je stále spuštěna v jeho instance prostředí PowerShell.  V případě potřeby restartujte nastavením:
    * **$DemoScenario = 1**, počáteční synchronizace serveru klienta, fondu a informace o konfiguraci databáze do katalogu
    * Stisknutím klávesy **F5** spusťte skript.
1.  Potom zahájíte proces navracení nastavte:
    * **$DemoScenario = 5**, repatriovat základní aplikace do jeho původní oblast
    * Stiskněte klávesu **F5** pro spuštění skriptu obnovení v novém okně prostředí PowerShell.  Navracení bude trvat několik minut a lze sledovat v okně prostředí PowerShell.
1. Je spuštěn skript, aktualizujte stránku centra událostí (http://events.wingtip-dpt.&lt; Uživatel&gt;. trafficmanager.net)
    * Všimněte si, že všichni klienti jsou online a dostupné v průběhu tohoto procesu.
1. Klikněte na křížovou kartou Jazz Fabrikam ho otevřete. Pokud jste nezměnila tohoto klienta, oznámení z zápatí, zda je server již vrátit zpět na původní server.
1. Otevřete nebo aktualizujte stránku události Hall vzájemné součinnosti Contoso a Všimněte si ze zápatí, že databáze je stále na _-obnovení_ server původně.  
1. Aktualizujte stránku události Contoso vzájemné součinnosti Hall po dokončení procesu navracení a Všimněte si, že databáze je nyní ve vaší původní oblasti.
1. Znovu aktualizujte Centrum událostí a otevřete Hawthorn Hall a Všimněte si, že svou databázi nachází taky v původní oblast. 

## <a name="clean-up-recovery-region-resources-after-repatriation"></a>Vyčištění prostředků oblasti obnovení po vrácení
Po dokončení navrácení je bezpečné odstranit prostředky v oblasti obnovení. 

> [!IMPORTANT]
> Odstraňte tyto prostředky neprodleně o zastavení všech fakturace pro ně.

Proces obnovení vytvoří všechny prostředky obnovení ve skupině prostředků pro obnovení. Proces vyčištění se odstranit tuto skupinu prostředků a odeberte všechny odkazy na prostředky z katalogu. 

1. V *prostředí PowerShell ISE*v ...\Learning Modules\Business kontinuity a skriptu Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1 po havárii, sada:
    * **$DemoScenario = 6**, odstranit zastaralé prostředky z oblasti obnovení

1. Stisknutím klávesy **F5** spusťte skript.

Po čištění skripty, aplikace je zpátky kde jeho spuštění.  Můžete znovu spustit skript, nebo vyzkoušet ostatní kurzy v tomto okamžiku.

## <a name="designing-the-application-to-ensure-app-and-database-are-colocated"></a>Navrhování aplikací, aby aplikace a databáze jsou umístěna společně 
Aplikace je navržena tak, aby vždycky připojuje z instance ve stejné oblasti jako databázi klienta. Tento návrh snižuje latence mezi aplikací a databáze. Tato optimalizace předpokládá, že aplikace do databáze interakce je chattier než interakce uživatele aplikace.  

Databáze klienta může být rozloženy obnovení a oblastí, původní nějakou dobu během navracení.  Pro každou databázi vyhledá aplikace oblast, ve kterém je databáze umístěna pomocí tohoto postupu vyhledání DNS u klientů název serveru. V databázi SQL je název serveru alias. Název serveru alias obsahuje název oblasti. Pokud aplikace není ve stejné oblasti jako databáze, je přesměrován na instanci ve stejné oblasti jako databázového serveru.  Přesměrování na instanci ve stejné oblasti jako databáze minimalizuje latenci mezi aplikací a databáze.  

## <a name="next-steps"></a>Další postup

V tomto kurzu jste se naučili:
> [!div class="checklist"]

>* Použití katalogu klienta pro uložení informace o pravidelně aktualizovat konfiguraci, což umožňuje prostředí zrcadlený obraz obnovení má být vytvořen v jiné oblasti
>* Použít geografické obnovení k obnovení databází Azure SQL do oblasti obnovení
>* Aktualizace katalogu klienta tak, aby odrážela umístění databáze obnovené klienta  
>* Povolit aplikaci připojit ke katalogu klienta v rámci bez změny konfigurace pomocí DNS alias
>* Použít geografická replikace repatriovat základní obnovené databáze do jejich původního oblasti po výpadku vyřešen.

Nyní, zkuste [zotavení po havárii pro víceklientské aplikace SaaS pomocí geografická replikace databáze](saas-dbpertenant-dr-geo-replication.md) se dozvíte, jak chcete výrazně snížit čas potřebný k obnovení ve velkém měřítku víceklientské aplikace pomocí geografická replikace.

## <a name="additional-resources"></a>Další zdroje informací:

* [Další kurzy, které stavět na adresář Wingtip SaaS aplikace](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-wtp-overview#sql-database-wingtip-saas-tutorials)
