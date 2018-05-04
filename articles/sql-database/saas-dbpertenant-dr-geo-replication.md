---
title: Zotavení po havárii pro aplikace SaaS používá geografickou replikaci, Azure SQL Database | Microsoft Docs
description: Naučte se používat Azure SQL Database geografické repliky pro obnovení víceklientské aplikace SaaS v případě výpadku
keywords: kurz k sql database
services: sql-database
author: AyoOlubeko
manager: craigg
ms.service: sql-database
ms.custom: saas apps
ms.topic: article
ms.date: 04/09/2018
ms.author: ayolubek
ms.openlocfilehash: 3b2b1b767b26d844046d545e3d587621c5d14995
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="disaster-recovery-for-a-multi-tenant-saas-application-using-database-geo-replication"></a>Zotavení po havárii pro víceklientské aplikace SaaS pomocí geografická replikace databáze

V tomto kurzu prozkoumejte úplné zotavení po havárii pro víceklientské aplikace SaaS implementovaná pomocí modelu databáze za klienta. Chcete-li chránit aplikace od výpadku, použijte [ _geografická replikace_ ](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-overview) k vytvoření repliky pro databáze katalogu a klienta v oblast alternativní obnovení. Pokud dojde k výpadku, můžete rychle převzetí služeb při selhání na tyto repliky obnovit normální podnikové operace. V převzetí služeb při selhání databáze v oblasti původní stát sekundární repliky databáze v oblasti obnovení. Jakmile tyto repliky dostane zpět online budou automaticky aktualizovány stavu databáze v oblasti obnovení. Jakmile se výpadek vyřešen, selžou zpět do databáze v oblasti původní produkční.

Tento tento kurz popisuje, jak převzetí služeb při selhání a navrácení služeb po obnovení pracovních postupů. Dozvíte, jak:
> [!div classs="checklist"]

>* Databáze Sync a informace o konfiguraci elastického fondu do katalogu klienta
>* Nastavení prostředí obnovení v alternativní oblasti, která obsahuje aplikace, servery a fondy
>* Použití _geografická replikace_ replikace databáze katalogu a klienta do oblasti obnovení
>* Selhání aplikace a databáze katalogu a klienta pro danou oblast pro obnovení 
>* Později, selhání aplikace, databáze katalogu a klienta zpět na původní oblast po vyřešení se výpadek
>* Aktualizovat katalog jako každou databázi klienta při selhání ke sledování primární umístění databáze každého klienta
>* Ujistěte se, společně klienta aplikace a v primární databázi jsou vždy umístěné ve stejné oblasti Azure ke snížení latence  
 

Před zahájením tohoto kurzu, zkontrolujte, zda že jsou dokončit následující požadavky:
* Databáze SaaS lístky Wingtip za klienta aplikace se nasazuje. Nasazení za méně než pět minut najdete v tématu [nasazení a seznamte se s SaaS lístky Wingtip databáze za klienta aplikace](saas-dbpertenant-get-started-deploy.md)  
* Je nainstalované prostředí Azure PowerShell. Podrobnosti najdete v článku [Začínáme s prostředím Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

## <a name="introduction-to-the-geo-replication-recovery-pattern"></a>Úvod do vzoru obnovení geografická replikace

![Architektura obnovení](media/saas-dbpertenant-dr-geo-replication/recovery-architecture.png)
 
Zotavení po havárii (DR) je důležitá poznámka pro mnoho aplikací pro dodržování předpisů nebo kontinuity podnikových procesů. Mělo by být výpadku provozu v případě delších služeb, dobře připravený plán zotavení po Havárii minimalizovat narušení firmy. Pomocí geografická replikace poskytuje nejnižší plánovaný bod obnovení a RTO udržováním repliky databáze v oblasti obnovení, která může převzít služby při selhání v krátké Všimněte.

Plán A zotavení po Havárii založené na geografická replikace se skládá ze tří různých částí:
* Nastavení – vytváření a údržbě prostředí obnovení
* Obnovení – převzetí služeb při selhání aplikace a databáze pro obnovení prostředí, když dojde k výpadku
* Navracení - převzetí služeb při selhání aplikace a databáze zpět do původní oblast po vyřešení aplikace 

Všechny části musí být považovány za pečlivě, zejména v případě, že operační ve velkém měřítku. Plán celkové, musí provést několik cíle:

* Nastavení
    * Vytvořit a udržovat zrcadlový prostředí v oblasti obnovení. Vytváření elastické fondy a replikuje všechny samostatné databáze v tomto prostředí obnovení si vyhrazuje kapacity v oblasti obnovení. Zachování toto prostředí zahrnuje replikaci nové databáze klienta jako, které byly povoleny.  
* Obnovení
    * Používá-li škálovat rozbalovací obnovení prostředí každodenní náklady minimalizovat, fondy a samostatné databáze musí být rozšířit tak, aby získat úplné provozní kapacitu v oblasti obnovení
    * Povolit zřizování v oblasti obnovení co nejdříve nového klienta  
    * Optimalizované pro obnovení klienty v pořadí podle priority
    * Optimalizované pro získání klientů online co nejrychleji provedením kroků v paralelní tam, kde je to praktické
    * Být odolné vůči selhání, dá se restartovat a idempotent
    * Možné proces střední přecházejí zrušit, pokud původní oblast pochází opět online.
* Navracení 
    * Selhání databáze z oblasti obnovení do repliky v původní oblasti s minimálním dopadem klientům: bez ztráty dat a minimální doba offline každého klienta.   

Tyto problémy řešeny v tomto kurzu, pomocí funkce Azure SQL Database a platformy Azure:

* [Šablony Azure Resource Manageru](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-create-first-template), co nejrychleji rezervovat všechny potřebné kapacity. Šablony Azure Resource Manageru se používají ke zřízení zrcadlený obraz provozních serverů a elastické fondy v oblasti obnovení.
* [Geografická replikace](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-overview), chcete-li vytvořit asynchronně replikované sekundární databáze jen pro čtení pro všechny databáze. Během výpadku můžete převzetí služeb při selhání replik v oblasti obnovení.  Jakmile se výpadek vyřešen, selžou zpět do databáze v oblasti původní bez ztráty dat.
* [Asynchronní](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations) operace převzetí služeb při selhání se odesílají v pořadí podle priority klienta, chcete-li minimalizovat dobu převzetí služeb při selhání velkého počtu databází.
* [Funkce obnovení správy horizontálního oddílu](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-database-recovery-manager), chcete-li změnit během obnovení a navracení položky databáze v katalogu. Tyto funkce umožňují aplikace pro připojení k databázím klienta bez ohledu na umístění bez nutnosti měnit aplikace.
* [SQL server DNS aliasy](https://docs.microsoft.com/azure/sql-database/dns-alias-overview), zapněte bezproblémové zřizování nových klientů, bez ohledu na to, které oblasti aplikace pracuje v. Aliasy DNS se taky používají k povolit procesu synchronizace katalogu pro připojení ke katalogu active bez ohledu na jeho umístění.

## <a name="get-the-disaster-recovery-scripts"></a>Získat skripty pro zotavení po havárii 

> [!IMPORTANT]
> Podobně jako všechny lístky Wingtip správu skripty skripty zotavení po Havárii jsou ukázka kvality a není určen k použití v produkčním prostředí. 

Obnovení skripty použité v tomto kurzu a Wingtip aplikace zdrojového kódu jsou k dispozici v [SaaS lístky Wingtip databáze za klienta úložiště GitHub](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant/). Podívejte se [obecné pokyny](saas-tenancy-wingtip-app-guidance-tips.md) kroky, jak stáhnout a odblokování skripty správy Wingtip lístků.

## <a name="tutorial-overview"></a>Přehled kurzu
V tomto kurzu použijete nejprve geografická replikace k vytvoření repliky Wingtip lístky aplikace a její databáze v jiné oblasti. Potom můžete převzetí služeb při selhání do této oblasti k simulaci obnovení výpadku. Po dokončení v oblasti obnovení je plně funkční aplikaci.

Později v samostatných navracení kroku, můžete převzetí služeb při selhání databáze katalogu a klientů v oblasti obnovení do původní oblasti. Aplikace a databáze zůstanou k dispozici v rámci navracení. Po dokončení v původní oblasti je plně funkční aplikaci.

> [!Note]
> Aplikace je obnovena do _spárované oblast_ oblasti, ve kterém je aplikace nasazená. Další informace najdete v tématu [Azure spárovat oblasti](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

## <a name="review-the-healthy-state-of-the-application"></a>Kontrola stavu v pořádku aplikace

Před zahájením procesu obnovení, zkontrolujte stav v pořádku normální aplikace.
1. Ve webovém prohlížeči, otevřete Centrum událostí lístky Wingtip (http://events.wingtip-dpt.&lt; Uživatel&gt;. trafficmanager.net - nahradit &lt;uživatele&gt; s hodnotou uživatele vašeho nasazení).
    * Přejděte do dolní části stránky a Všimněte si katalogu serveru název a umístění v zápatí. Umístění je oblast, ve kterém je aplikace nasazená.
    *TIP: Najeďte myší umístění zvětšení zobrazení. * 
     ![Události v pořádku stav rozbočovače v původní oblasti](media/saas-dbpertenant-dr-geo-replication/events-hub-original-region.png)

2. Klikněte na klienta Hall vzájemné součinnosti Contoso a otevřete stránku s jeho událostí.
    * V zápatí Všimněte si, název serveru klienta. Umístění bude stejná jako server katalogu umístění.

3. V [portál Azure](https://portal.azure.com), otevřete skupinu prostředků, ve kterém je aplikace nasazená
    * Všimněte si, oblast, ve kterém jsou servery nasazené. 

## <a name="sync-tenant-configuration-into-catalog"></a>Konfiguraci synchronizace klienta do katalogu

V této úloze spustit proces, který synchronizuje konfiguraci serverů, elastické fondy a databáze do katalogu klienta. Proces udržuje tyto informace v aktuálním stavu v katalogu.  Proces funguje s katalogem aktivní, zda v původní oblasti nebo v oblasti obnovení. Informace o konfiguraci se používá jako součást procesu obnovení k zajištění obnovení prostředí je v souladu s původní prostředí a, pak později během navracení zajistit původní oblast je konzistentní s veškeré změny provedené v obnovení prostředí. Katalog se také používá ke sledování stavu obnovení prostředků klienta

> [!IMPORTANT]
> Pro jednoduchost proces synchronizace a dalších dlouho běžící procesy obnovení a navracení implementované v těchto kurzech jako místní prostředí Powershell úlohy nebo relace, které běží pod vaše přihlášení uživatele klienta. Tokeny ověřování při přihlášení vyprší po několik hodin a pak se nezdaří úlohy. V případě produkční dlouho běžící procesy by měla být implementována jako spolehlivé služby Azure určitého druhu spuštěna pod hlavní název služby. V tématu [použití Azure PowerShell k vytvoření objektu služby pomocí certifikátu](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authenticate-service-principal).

1. V _prostředí PowerShell ISE_, otevřete soubor Modules\UserConfig.psm1 ...\Learning. Nahraďte `<resourcegroup>` a `<user>` na řádky 10 a 11 hodnota použitá při nasazení aplikace.  Uložte soubor!

2. V *prostředí PowerShell ISE*, otevřete ...\Learning Modules\Business kontinuitu a po havárii Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1 skript a nastavte:
    * **$DemoScenario = 1**, spusťte úlohu na pozadí, který je synchronizován serveru klienta a informace o konfiguraci fondu do katalogu

3. Stiskněte klávesu **F5** pro spuštění skriptu synchronizace. Novou relaci prostředí PowerShell je otevřen pro synchronizaci konfigurace prostředků klienta.
![Procesu synchronizace](media/saas-dbpertenant-dr-geo-replication/sync-process.png)

Ponechte okno prostředí PowerShell spuštěná na pozadí a další kurzu. 

> [!Note]
> Procesu synchronizace se připojí ke katalogu prostřednictvím DNS alias. Tento alias se upravují během obnovení a navracení tak, aby odkazoval na aktivní katalogu. Procesu synchronizace udržuje katalogu v aktuálním stavu pomocí databáze nebo fond změny konfigurace provedené v oblasti obnovení.  Během navracení tyto změny se použijí na ekvivalentní prostředky v původní oblasti.

## <a name="create-secondary-database-replicas-in-the-recovery-region"></a>Vytváření replik sekundární databáze v oblasti obnovení

V této úloze spustit proces, který nasadí instanci duplicitní aplikace a v oblasti obnovení replikuje katalogu a všechny databáze klienta.

> [!Note]
> V tomto kurzu přidá geografická replikace ochrany na adresář Wingtip lístky ukázkovou aplikaci. V případě produkční aplikace, která používá geografická replikace by zřídí každého klienta s databází geograficky replikované od samého počátku. V tématu [navrhování služeb s vysokou dostupností pomocí Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-designing-cloud-solutions-for-disaster-recovery#scenario-1-using-two-azure-regions-for-business-continuity-with-minimal-downtime)

1. V *prostředí PowerShell ISE*, otevřete ...\Learning Modules\Business kontinuitu a po havárii Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1 skript a nastavte následující hodnoty:
    * **$DemoScenario = 2**, vytvořte zrcadlený obraz obnovení prostředí a replikaci databáze katalogu a klienta

2. Stisknutím klávesy **F5** spusťte skript. Novou relaci prostředí PowerShell je otevřen pro vytvoření repliky.
![Procesu synchronizace](media/saas-dbpertenant-dr-geo-replication/replication-process.png)  

## <a name="review-the-normal-application-state"></a>Zkontrolujte stav normální aplikace
V tomto okamžiku aplikace běží normálně v původní oblasti a nyní je chráněn geografická replikace.  Jen pro čtení sekundární repliky, existují v oblasti obnovení pro všechny databáze. 
1. Na portálu Azure, podívejte se na skupiny prostředků a Všimněte si, že byl vytvořen skupinu prostředků s - přípona obnovení v oblasti obnovení. 

1. Prozkoumejte prostředky ve skupině prostředků pro obnovení.  

1. Klikněte na databázi Contoso vzájemné součinnosti Hall v _tenants1-dpt -&lt;uživatele&gt;-obnovení_ serveru.  Na levé straně klikněte na geografická replikace. 

    ![Vzájemné součinnosti contoso geografická replikace propojení](media/saas-dbpertenant-dr-geo-replication/contoso-geo-replication.png) 

V oblasti mapy Všimněte si geografická replikace propojení mezi v oblasti původní primární a sekundární v oblasti obnovení.  

## <a name="fail-over-the-application-into-the-recovery-region"></a>Selhání aplikace do oblasti obnovení

### <a name="geo-replication-recovery-process-overview"></a>Přehled procesu obnovení geografická replikace

Obnovení skript provede následující úlohy:

1. Zakáže koncový bod Traffic Manager pro webovou aplikaci v původní oblasti. Zakázání koncového bodu zabraňuje uživatelům v připojení k aplikaci v neplatném stavu oblasti původní pocházet online během obnovení.

1. Používá Vynucené převzetí služeb při selhání databáze katalogu v oblasti obnovení, chcete-li primární databáze a aktualizuje _activecatalog_ alias tak, aby odkazoval na server pro obnovení katalogu.

1. Aktualizace _newtenant_ alias tak, aby odkazoval na server klienta v oblasti obnovení. Změna tento alias zajistí, že je databáze pro všechny nové klienty zřízený v oblasti obnovení. 

1. Označí všechny stávající klienty v katalogu obnovení jako v režimu offline chcete zabránit přístupu do databáze klienta předtím, než jsou při selhání.

1. Aktualizace konfigurace všech elastické fondy a replikované samostatné databáze v oblasti obnovení pro zrcadlení jejich konfigurace v původní oblasti. (Tato úloha je třeba, pouze pokud fondy nebo replikované databáze v prostředí obnovení zmenšování během normálních operací, jak snížit náklady).

1. Umožňuje koncový bod Traffic Manager pro webovou aplikaci v oblasti obnovení. Povolení tento koncový bod umožňuje aplikaci zřídit nové klienty. V této fázi existující klienti jsou stále v režimu offline.

1. Odešle, že dávky žádostí o vynutit převzít databází v pořadí podle priority.
    * Dávky jsou uspořádány tak, aby databáze jsou převzetí služeb při selhání paralelně mezi všechny fondy.
    * Žádosti o převzetí služeb při selhání se odešlou pomocí asynchronních operací tak, aby se odešlou rychle a víc požadavků lze současně zpracovávat.

   > [!Note]
   > Ve scénáři s výpadku primární databáze v původní oblasti jsou offline.  Vynucené převzetí služeb při selhání na sekundární přerušení připojení k primární bez pokusu o použití jakýchkoli zbytkové transakcí zařazených do fronty. Ve scénáři zotavení po Havárii procházení jako tento kurz Pokud žádnou aktivitu aktualizace v době převzetí služeb při selhání je možné ztrátě dat. Později během navracení, když jste převzetí služeb při selhání databází v oblasti obnovení zpět na původní oblast, normální převzetí služeb při selhání se používá k Ujistěte se, že nedošlo ke ztrátě dat.

1. Monitoruje službu SQL database k určení, kdy nepodařilo databází přes. Jakmile databáze klienta se při selhání, aktualizuje katalogu zaznamenávat stav obnovení databáze klienta a označit jako online klienta.
    * Klient databáze byla přístupná pomocí aplikace hned, jak jsou označeny v katalogu online.
    * Součet hodnot rowversion v databázi klienta je uložené v katalogu. Tato hodnota se chová jako otisků prstů, která umožňuje proces navracení k určení, zda databáze byl aktualizován v oblasti obnovení.

### <a name="run-the-script-to-fail-over-to-the-recovery-region"></a>Spusťte skript pro převzetí služeb při selhání oblasti obnovení

Nyní Představte si v oblasti, ve kterém je aplikace nasazena a spusťte skript obnovení je výpadek:

1. V *prostředí PowerShell ISE*, otevřete ...\Learning Modules\Business kontinuitu a po havárii Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1 skript a nastavte následující hodnoty:
    * **$DemoScenario = 3**, obnovit aplikace do oblasti obnovení pomocí převzetí služeb při selhání pro repliky

2. Stisknutím klávesy **F5** spusťte skript.  
    * Skript se otevře v novém okně prostředí PowerShell a pak spustí řadu úloh prostředí PowerShell, které spustit souběžně. Tyto úlohy převzít databáze klienta do oblasti pro obnovení.
    * Oblast pro obnovení je _spárované oblast_ přidružené k oblasti Azure, ve kterém je aplikace nasazena. Další informace najdete v tématu [Azure spárovat oblasti](https://docs.microsoft.com/azure/best-practices-availability-paired-regions). 

3. Monitorování stavu procesu obnovení v okně prostředí PowerShell.
    ![Proces převzetí služeb při selhání](media/saas-dbpertenant-dr-geo-replication/failover-process.png)

> [!Note]
> Chcete-li prozkoumat kód pro úlohy obnovení, zkontrolujte skriptů prostředí PowerShell ve složce Modules\Business kontinuitu a po havárii Recovery\DR-FailoverToReplica\RecoveryJobs ...\Learning.

### <a name="review-the-application-state-during-recovery"></a>Zkontrolujte stav aplikace během obnovení
Když koncový bod aplikace je v Traffic Manageru zakázán, aplikace není k dispozici. Po katalogu při selhání oblasti obnovení a všichni klienti označena offline, aplikace se vrátí do režimu online. I když aplikace je k dispozici, zobrazí offline v Centru událostí každého klienta, dokud svou databázi při selhání. Je důležité při návrhu vaší aplikace pro zpracování databáze offline klienta.

1. Okamžitě po databáze katalogu byla obnovena, aktualizujte Centrum událostí lístky Wingtip ve webovém prohlížeči.
    * V zápatí, Všimněte si teď obsahuje název serveru katalogu _-obnovení_ přípona a se nachází v oblasti obnovení.
    * Všimněte si, že jsou označeny jako offline klienty, kteří ještě nejsou obnoveny a se nedá vybrat.  

    > [!Note]
    > Pouze několik databází obnovení nebudete moci aktualizujte stránku prohlížeče, než je obnovení dokončeno, tak ke klientům se nemusí zobrazit v době, kdy jsou offline. 
 
    ![Centra událostí do offline režimu](media/saas-dbpertenant-dr-geo-replication/events-hub-offlinemode.png) 

    * Pokud otevřete stránku události offline klienta přímo, zobrazí se oznámení klienta na offline. Například pokud Contoso vzájemné součinnosti Hall offline, pokusí otevřít http://events.wingtip-dpt.&lt; Uživatel&gt;.trafficmanager.net/contosoconcerthall ![stránku Contoso Offline](media/saas-dbpertenant-dr-geo-replication/dr-in-progress-offline-contosoconcerthall.png) 

### <a name="provision-a-new-tenant-in-the-recovery-region"></a>Zřízení nového klienta v oblasti obnovení
Ještě před všechny existující databáze klienta mít převzetí služeb při selhání, můžete zřídit nové klienty v oblasti obnovení.  

1. V *prostředí PowerShell ISE*, otevřete ...\Learning Modules\Business kontinuitu a po havárii Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1 skript a nastavte následující vlastnost:
    * **$DemoScenario = 4**, zřízení nového klienta v oblasti obnovení

2. Stiskněte klávesu **F5** spusťte skript a zajištění k novému klientovi. 

3. Stránka události Hawthorn Hall otevře v prohlížeči se po dokončení. Poznámka od zápatí, že je databáze Hawthorn Hall zřízený v oblasti obnovení.
    ![Stránka události hawthorn Hall](media/saas-dbpertenant-dr-geo-replication/hawthornhallevents.png) 

4. V prohlížeči aktualizujte stránku Wingtip lístky události rozbočovače zobrazíte Hawthorn Hall zahrnuté. 
    * Pokud jste zřídili Hawthorn Hall bez čekání na další klienty k obnovení, může být offline ostatních klientů.


## <a name="review-the-recovered-state-of-the-application"></a>Zkontrolujte obnovené stav aplikace

Po dokončení procesu obnovení, aplikace a všichni klienti jsou plně funkční v oblasti obnovení. 

1. Po zobrazení v okně konzoly prostředí PowerShell znamená, že se obnoví všechny klienty, aktualizujte Centrum událostí.  Klienti se zobrazí všechny online, včetně nového klienta, Hawthorn Hall.

    ![obnovená a nové klienty v Centru událostí](media/saas-dbpertenant-dr-geo-replication/events-hub-with-hawthorn-hall.png)

2. V [portál Azure](https://portal.azure.com), otevře se seznam skupin prostředků.  
    * Všimněte si skupinu prostředků, která jste nasadili, plus skupině prostředků obnovení pomocí _-obnovení_ příponu.  Skupiny prostředků pro obnovení obsahuje všechny prostředky vytvořené během procesu obnovení, plus nové prostředky, které se vytvoří během se výpadek.  

3. Otevřete skupinu prostředků, obnovení a Všimněte si následující položky:
    * Verze obnovení serverů katalogu a tenants1 s _-obnovení_ příponu.  Obnovená databáze katalogu a klienta na těchto serverech všechny mít názvy používaných v původní oblasti.

    * _Tenants2-dpt -&lt;uživatele&gt;-obnovení_ systému SQL server.  Tento server se používá pro zřizování nové klienty během se výpadek.
    *   Aplikace služby s názvem, _události-wingtip-dpt -&lt;recoveryregion&gt;-&lt;uživatele & gt_; což je instance obnovení systému aplikace události. 

    ![Prostředky Azure obnovení ](media/saas-dbpertenant-dr-geo-replication/resources-in-recovery-region.png)    
    
4. Otevřete _tenants2-dpt -&lt;uživatele&gt;-obnovení_ systému SQL server.  Všimněte si obsahuje databázi _hawthornhall_ a elastický fond _Pool1_.  _Hawthornhall_ databáze je nakonfigurovaný jako elastické databáze v _Pool1_ elastického fondu.

5. Přejděte zpět do skupiny prostředků a klikněte na databázi Contoso vzájemné součinnosti Hall v _tenants1-dpt -&lt;uživatele&gt;-obnovení_ serveru. Na levé straně klikněte na geografická replikace.
    
    ![Contoso databáze po převzetí služeb při selhání](media/saas-dbpertenant-dr-geo-replication/contoso-geo-replication-after-failover.png)

## <a name="change-tenant-data"></a>Změna dat klienta 
V této úloze aktualizujete jedna z databází klienta. 

1. V prohlížeči najít seznamu událostí pro Hall vzájemné součinnosti Contoso a poznamenejte si název poslední události.
2. V *prostředí PowerShell ISE*, v ...\Learning Modules\Business kontinuity a po havárii Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1 skriptu, nastavte následující hodnotu:
    * **$DemoScenario = 5** odstranit událost z klienta v oblasti obnovení
3. Stiskněte klávesu **F5** se spustit skript
4. Aktualizujte stránku události Hall vzájemné součinnosti Contoso (http://events.wingtip-dpt.&lt; Uživatel&gt;.trafficmanager.net/contosoconcerthall - nahraďte &lt;uživatele&gt; s hodnotou uživatele vašeho nasazení) a Všimněte si, že poslední událost byla odstraněna.

## <a name="repatriate-the-application-to-its-original-production-region"></a>Repatriovat základní aplikace pro její původní produkční oblast

Tato úloha repatriates aplikace na jeho původní oblast. Ve scénáři skutečné by zahájit navracení po vyřešení se výpadek.

### <a name="repatriation-process-overview"></a>Přehled procesu navracení

![Architektura navracení](media/saas-dbpertenant-dr-geo-replication/repatriation-architecture.png)

Proces navracení:
1. Zruší všechny žádosti o obnovení databáze nezpracovaných nebo během letu.
2. Aktualizace _newtenant_ alias tak, aby odkazoval na server s klienty v oblasti původu. Změna tento alias zajistí, že databáze pro všechny nové klienty bude nyní zajištěna v oblasti původu.
3. Doplňuje žádná data změněné klienta do původní oblasti
4. Převezme klient databází v pořadí podle priority.

Převzetí služeb při selhání přesune efektivně databáze na původní oblast. Při databáze při selhání, jsou všechny otevřené připojení vyřazen a databáze není k dispozici pro několik sekund. Aplikace by měly být zapsány s logikou opakování užitečnou při Ujistěte se, že se znovu připojit.  I když tento stručný odpojení je často není si všimli, můžete se rozhodnout repatriovat základní databáze mimo pracovní dobu. 


### <a name="run-the-repatriation-script"></a>Spusťte skript navracení
Nyní Pojďme Představte si je vyřešen a spusťte skript navracení se výpadek.

1. V *prostředí PowerShell ISE*v ...\Learning Modules\Business kontinuity a skript Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1 po havárii.

2. Ověřte, zda procesu synchronizace katalogu je stále spuštěna v jeho instance prostředí PowerShell.  V případě potřeby restartujte nastavením:
    * **$DemoScenario = 1**, počáteční synchronizace serveru klienta, fondu a informace o konfiguraci databáze do katalogu
    * Stisknutím klávesy **F5** spusťte skript.

3.  Potom zahájíte proces navracení nastavte:
    * **$DemoScenario = 6**, repatriovat základní aplikace do jeho původní oblast
    * Stiskněte klávesu **F5** pro spuštění skriptu obnovení v novém okně prostředí PowerShell.  Navracení bude trvat několik minut a lze sledovat v okně prostředí PowerShell.
    ![Proces navracení](media/saas-dbpertenant-dr-geo-replication/repatriation-process.png)

4. Je spuštěn skript, aktualizujte stránku centra událostí (http://events.wingtip-dpt.&lt; Uživatel&gt;. trafficmanager.net)
    * Všimněte si, že všichni klienti jsou online a dostupné v průběhu tohoto procesu.

5. Po dokončení navracení aktualizujte centrem událostí a otevřete stránku události Hawthorn Hall. Všimněte si, že má byl původní oblasti repatriated tuto databázi.
    ![Centra událostí repatriated](media/saas-dbpertenant-dr-geo-replication/events-hub-repatriated.png)


## <a name="designing-the-application-to-ensure-app-and-database-are-colocated"></a>Navrhování aplikací, aby aplikace a databáze jsou umístěna společně 
Aplikace je navržena tak, aby vždycky připojuje z instance ve stejné oblasti jako databázi klienta. Tento návrh snižuje latence mezi aplikací a databáze. Tato optimalizace předpokládá, že aplikace do databáze interakce je chattier než interakce uživatele aplikace.  

Databáze klienta může být rozloženy obnovení a oblastí, původní nějakou dobu během navracení. Pro každou databázi vyhledá aplikace oblast, ve kterém je databáze umístěna pomocí tohoto postupu vyhledání DNS u klientů název serveru. V databázi SQL je název serveru alias. Název serveru alias obsahuje název oblasti. Pokud aplikace není ve stejné oblasti jako databáze, je přesměrován na instanci ve stejné oblasti jako databázového serveru.  Přesměrování na instanci ve stejné oblasti jako databáze minimalizuje latenci mezi aplikací a databáze. 

## <a name="next-steps"></a>Další postup

V tomto kurzu jste se naučili:
> [!div classs="checklist"]

>* Databáze Sync a informace o konfiguraci elastického fondu do katalogu klienta
>* Nastavení prostředí obnovení v alternativní oblasti, která obsahuje aplikace, servery a fondy
>* Použití _geografická replikace_ replikace databáze katalogu a klienta do oblasti obnovení
>* Selhání aplikace a databáze katalogu a klienta pro danou oblast pro obnovení 
>* Navrácení služeb po obnovení databáze aplikace, katalogu a klienta původní oblast, jakmile se výpadek vyřešen

Další informace o technologiích, které poskytuje Azure SQL database k povolení kontinuity podnikových procesů v [obchodní kontinuity přehled](sql-database-business-continuity.md) dokumentaci.

## <a name="additional-resources"></a>Další zdroje informací:

* [Další kurzy, které stavět na adresář Wingtip SaaS aplikace](https://docs.microsoft.com/azure/sql-database/sql-database-wtp-overview#sql-database-wingtip-saas-tutorials)
