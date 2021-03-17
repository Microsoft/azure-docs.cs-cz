---
title: Zotavení po havárii pro aplikace SaaS s geografickou replikací
description: Naučte se používat Azure SQL Database geografické repliky k obnovení SaaS aplikace pro více tenantů v případě výpadku.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/25/2019
ms.openlocfilehash: d2c1ddd1e0b5a080050e1ffeb28eded98dbfea3f
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/17/2020
ms.locfileid: "97652088"
---
# <a name="disaster-recovery-for-a-multi-tenant-saas-application-using-database-geo-replication"></a>Zotavení po havárii pro víceklientské aplikace SaaS s využitím geografické replikace databáze
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

V tomto kurzu se seznámíte s úplným scénářem zotavení po havárii pro víceklientské aplikace SaaS implementované pomocí modelu databáze pro jednotlivé klienty. K ochraně aplikace před výpadkem použijte [_geografickou replikaci_](active-geo-replication-overview.md) k vytvoření replik pro databáze katalogu a tenanta v alternativní oblasti obnovení. Pokud dojde k výpadku, rychle převezmete služby při selhání do těchto replik, aby se obnovily běžné obchodní operace. V případě převzetí služeb při selhání se databáze v původní oblasti stanou sekundárními replikami databází v oblasti obnovení. Jakmile se tyto repliky vrátí do online režimu, budou automaticky zachytit stav databází v oblasti obnovení. Po vyřešení výpadku dojde k navrácení služeb po obnovení do databází v původní produkční oblasti.

Tento kurz se zabývá postupy převzetí služeb při selhání i navrácení služeb po obnovení. Dozvíte se, jak provést tyto akce:
> [!div class="checklist"]
> 
> * Synchronizovat informace o konfiguraci databáze a elastického fondu do katalogu tenantů
> * Nastavení prostředí pro obnovení v jiné oblasti, která zahrnuje aplikace, servery a fondy
> * Použití _geografické replikace_ k replikaci katalogu a databází klientů do oblasti obnovení
> * Převzetí služeb při selhání databáze aplikace a katalogu a klientů do oblasti obnovení 
> * Později dojde k převzetí služeb při selhání aplikace, katalogu a tenantů databází po vyřešení výpadku do původní oblasti.
> * Aktualizujte katalog, protože při převzetí služeb při selhání každé databáze tenanta sledujete primární umístění databáze každého klienta.
> * Zajistěte, aby byla aplikace a primární tenant databáze vždy společně umístěná ve stejné oblasti Azure, aby se snížila latence.  


Před zahájením tohoto kurzu se ujistěte, že jsou splněné následující předpoklady:
* Aplikace Wingtip Tickets SaaS Database na tenanta je nasazená. Postup nasazení za méně než pět minut najdete v tématu [nasazení a prozkoumání aplikace Wingtip Tickets SaaS Database na klienta](saas-dbpertenant-get-started-deploy.md) .  
* Je nainstalované prostředí Azure PowerShell. Podrobnosti najdete v článku [Začínáme s prostředím Azure PowerShell](/powershell/azure/get-started-azureps).

## <a name="introduction-to-the-geo-replication-recovery-pattern"></a>Úvod do vzoru obnovení geografické replikace

![Architektura obnovení](./media/saas-dbpertenant-dr-geo-replication/recovery-architecture.png)

Zotavení po havárii (DR) je důležité pro mnoho aplikací, ať už z důvodů dodržování předpisů nebo provozní kontinuity. Pokud by existovala dlouhodobá výpadka služby, dobře připravený plán DR může minimalizovat dopad na chod firmy. Použití geografické replikace poskytuje nejnižší RPO a RTO tím, že udržuje repliky databáze v oblasti obnovení, která se dá při selhání při krátkém oznámení převzít.

Plán DR na základě geografické replikace se skládá ze tří různých částí:
* Vytvoření a údržba prostředí pro obnovení
* Obnovení – převzetí služeb při selhání aplikací a databází v prostředí obnovení, pokud dojde k výpadku,
* Vrácení zpět – převzetí služeb při selhání aplikace a databází po vyřešení aplikace zpátky do původní oblasti 

Všechny části musí být pečlivě zváženy, zejména pokud pracujete se škálováním. Plán musí dosáhnout několika cílů:

* Nastavení
    * Vytvořte a udržujte prostředí zrcadlového obrazu v oblasti obnovení. Vytvoření elastických fondů a replikace všech databází v tomto prostředí pro obnovení rezervuje kapacitu v oblasti obnovení. Údržba tohoto prostředí zahrnuje replikaci nových databází tenanta, když jsou zřízené.  
* Obnovovací
    * Pokud se pro minimalizaci každodenních nákladů používá prostředí pro obnovení s horizontálním škálováním na víc systémů, musí se fondy a databáze škálovat až po získání plné provozní kapacity v oblasti obnovení.
     * Povolit nové zřizování klientů v oblasti obnovení co nejdříve  
     * Optimalizace pro obnovení tenantů v pořadí podle priority
     * Optimalizujte se tak, aby klienti mohli co nejrychleji získat služby online, a to pomocí kroků paralelně, kde to bude praktické
     * Zajištění odolnosti proti selhání, restartně a idempotentní
     * Pokud se původní oblast vrátí online, je možné proces v polovině letu zrušit.
* Repatriace 
     * Převzetí služeb při selhání databáze z oblasti obnovení do replik v původní oblasti s minimálním dopadem na klienty: žádná ztráta dat a minimální tečka v/v-line na tenanta.

V tomto kurzu se tyto výzvy řeší pomocí funkcí Azure SQL Database a platformy Azure:

* [Azure Resource Manager šablony](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md), aby bylo možné co nejrychleji rezervovat veškerou potřebnou kapacitu. Šablony Azure Resource Manager slouží ke zřízení zrcadlové image produkčních serverů a elastických fondů v oblasti obnovení.
* [Geografická replikace](active-geo-replication-overview.md)pro vytváření asynchronně replikovaných sekundárních souborů jen pro čtení pro všechny databáze. Během výpadku převezmete služby při selhání do replik v oblasti obnovení.  Po vyřešení výpadku dojde k navrácení služeb po obnovení do databází v původní oblasti bez ztráty dat.
* Operace [asynchronního](../../azure-resource-manager/management/async-operations.md) převzetí služeb při selhání se odesílají v pořadí podle priority tenanta, aby se minimalizovala doba selhání pro velký počet databází.
* [Funkce obnovení pro správu horizontálních oddílů](elastic-database-recovery-manager.md), které mění položky databáze v katalogu během obnovování a vracení. Tyto funkce umožňují aplikaci připojovat se k databázím tenantů bez ohledu na umístění bez nutnosti opětovné konfigurace aplikace.
* [Aliasy DNS serveru SQL](./dns-alias-overview.md)umožňují bezproblémové zřizování nových tenantů bez ohledu na to, ve které oblasti aplikace pracuje. Aliasy DNS slouží také k tomu, aby se proces synchronizace katalogu mohl připojit k aktivnímu katalogu bez ohledu na jeho umístění.

## <a name="get-the-disaster-recovery-scripts"></a>Získat skripty pro zotavení po havárii 

> [!IMPORTANT]
> Stejně jako všechny skripty pro správu lístků Wingtip mají skripty DR vzorek kvality a nemusejí být používány v produkčním prostředí. 

Skripty pro obnovení používané v tomto kurzu a zdrojový kód aplikace Wingtip jsou k dispozici v [úložišti GitHub lístky SaaS Database na tenanta](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant/). Projděte si [Obecné pokyny](saas-tenancy-wingtip-app-guidance-tips.md) ke stažení a odblokování skriptů pro správu lístků Wingtip Tickets.

## <a name="tutorial-overview"></a>Přehled kurzu
V tomto kurzu nejprve použijete geografickou replikaci k vytvoření replik aplikace Wingtip Tickets a jejích databází v jiné oblasti. Pak převezmete převzetí služeb při selhání této oblasti, abyste mohli simulovat obnovení z výpadku. Po dokončení aplikace je plně funkční v oblasti obnovení.

Později v kroku samostatného navrácení služeb při selhání převezmete databáze katalogu a klientů v oblasti obnovení do původní oblasti. Aplikace a databáze zůstávají k dispozici v průběhu navracení. Po dokončení aplikace je plně funkční v původní oblasti.

> [!Note]
> Aplikace se obnoví do _spárované oblasti_ v oblasti, ve které je aplikace nasazená. Další informace najdete v tématu [spárované oblasti Azure](../../best-practices-availability-paired-regions.md).

## <a name="review-the-healthy-state-of-the-application"></a>Zkontrolujte dobrý stav aplikace.

Než začnete s procesem obnovení, zkontrolujte normální dobrý stav aplikace.
1. Ve webovém prohlížeči otevřete centrum událostí Wingtip lístky ( http://events.wingtip-dpt.&lt ; User &gt; . trafficmanager.NET – nahraďte &lt; uživatele &gt; hodnotou uživatele vašeho nasazení).
    * Posuňte se do dolní části stránky a Všimněte si názvu a umístění serveru katalogu v zápatí. Umístění je oblast, ve které jste nasadili aplikaci.
    *Tip: Pokud chcete zobrazení zvětšit, najeďte myší na jeho umístění.* 
     ![ Stav v pořádku v centru událostí v původní oblasti](./media/saas-dbpertenant-dr-geo-replication/events-hub-original-region.png)

2. Klikněte na tenanta contoso hala a otevřete jeho stránku události.
    * V zápatí si všimněte názvu tenanta serveru. Umístění bude stejné jako umístění serveru katalogu.

3. V [Azure Portal](https://portal.azure.com)otevřete skupinu prostředků, ve které je aplikace nasazená.
    * Všimněte si oblasti, ve které jsou servery nasazeny. 

## <a name="sync-tenant-configuration-into-catalog"></a>Synchronizovat konfiguraci tenanta do katalogu

V této úloze spustíte proces, který synchronizuje konfiguraci serverů, elastických fondů a databází do katalogu tenantů. Tento proces uchovává tyto informace v katalogu v aktuálním stavu.  Tento proces spolupracuje s aktivním katalogem, ať už v původní oblasti, nebo v oblasti obnovení. Informace o konfiguraci se používají jako součást procesu obnovení, aby se zajistilo, že prostředí pro obnovení je konzistentní s původním prostředím a později během navracení, aby se zajistila konzistence původní oblasti se všemi změnami provedenými v prostředí pro obnovení. Katalog se také používá k udržení přehledu o stavu obnovení prostředků tenanta.

> [!IMPORTANT]
> Pro jednoduchost se v těchto kurzech implementují proces synchronizace a další dlouhodobé procesy obnovení a repatriace jako místní úlohy PowerShellu nebo relace spouštěné v rámci přihlášení uživatele klienta. Tokeny ověřování vydané po přihlášení vyprší po několika hodinách a úlohy se pak nezdaří. V produkčním scénáři by dlouhotrvající procesy měly být implementovány jako spolehlivé služby Azure v nějakém typu, a to za provozu v instančním objektu. Další informace najdete v tématu [použití Azure PowerShell k vytvoření instančního objektu s certifikátem](../../active-directory/develop/howto-authenticate-service-principal-powershell.md).

1. V _prostředí POWERSHELL ISE_ otevřete soubor Modules\UserConfig.psm1. ..\Learning. Nahraďte `<resourcegroup>` a `<user>` na řádcích 10 a 11 hodnotou použitou při nasazení aplikace.  Uložte soubor.

2. V *prostředí POWERSHELL ISE* otevřete skript. ..\Learning Modules\Business kontinuita a Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1 a nastavte skript pro zotavení po havárii a nastavte:
    * **$DemoScenario = 1**, spustí úlohu na pozadí, která synchronizuje Server tenanta a informace o konfiguraci fondu do katalogu.

3. Stisknutím klávesy **F5** spusťte skript synchronizace. Otevře se nová relace PowerShellu pro synchronizaci konfigurace prostředků tenanta.
![Snímek obrazovky, který zobrazuje novou relaci PowerShellu, která je otevřená pro synchronizaci konfigurace prostředků tenanta.](./media/saas-dbpertenant-dr-geo-replication/sync-process.png)

Nechte okno PowerShellu spuštěné na pozadí a pokračujte ve zbývající části tohoto kurzu. 

> [!Note]
> Proces synchronizace se k katalogu připojí prostřednictvím aliasu DNS. Tento alias se během obnovování upraví a repatriace odkazuje na aktivní katalog. Proces synchronizace udržuje v katalogu aktuální informace o změnách konfigurace databáze nebo fondu provedené v oblasti obnovení.  Během navracení jsou tyto změny aplikovány na ekvivalentní prostředky v původní oblasti.

## <a name="create-secondary-database-replicas-in-the-recovery-region"></a>Vytvoření sekundárních replik databáze v oblasti obnovení

V této úloze spustíte proces, který nasadí duplicitní instanci aplikace a replikuje katalog a všechny databáze tenantů do oblasti obnovení.

> [!Note]
> Tento kurz přidává do ukázkové aplikace Wingtip Tickets ochranu geografické replikace. V produkčním scénáři pro aplikaci, která používá geografickou replikaci, se každý tenant zřídí s geograficky replikovanou databází od samého začátku. Viz [Návrh vysoce dostupných služeb pomocí Azure SQL Database](designing-cloud-solutions-for-disaster-recovery.md#scenario-1-using-two-azure-regions-for-business-continuity-with-minimal-downtime)

1. V *prostředí POWERSHELL ISE* otevřete skript. ..\Learning Modules\Business kontinuita a Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1 a nastavte následující hodnoty:
    * **$DemoScenario = 2**, vytvořit prostředí pro obnovení zrcadlové image a replikovat databáze katalogu a tenantů

2. Stisknutím klávesy **F5** spusťte skript. Otevře se nová relace PowerShellu, ve které se vytvoří repliky.
![Proces synchronizace](./media/saas-dbpertenant-dr-geo-replication/replication-process.png)  

## <a name="review-the-normal-application-state"></a>Kontrola normálního stavu aplikace

V tuto chvíli běží aplikace normálně v původní oblasti a teď je chráněná geografickou replikací.  Sekundární repliky jen pro čtení existují v oblasti obnovení pro všechny databáze. 

1. V Azure Portal se podívejte na vaše skupiny prostředků a Všimněte si, že se v oblasti obnovení vytvořila skupina prostředků s příponou obnovení. 

2. Prozkoumejte prostředky ve skupině prostředků pro obnovení.  

3. Na serveru _tenants1-DPT- &lt; User &gt; -Recovery_ klikněte na databázi contoso.  Na levé straně klikněte na Geo-Replication. 

    ![Contoso – geografická replikace – odkaz na replikaci](./media/saas-dbpertenant-dr-geo-replication/contoso-geo-replication.png) 

V mapě oblastí Azure si všimněte odkazu na geografickou replikaci mezi primární oblastí v původní oblasti a sekundární v oblasti obnovení.  

## <a name="fail-over-the-application-into-the-recovery-region"></a>Převzetí služeb při selhání aplikace do oblasti obnovení

### <a name="geo-replication-recovery-process-overview"></a>Přehled procesu obnovení geografické replikace

Skript obnovení provádí následující úlohy:

1. Zakáže koncový bod Traffic Manager pro webovou aplikaci v původní oblasti. Zakázáním koncového bodu zabráníte uživatelům v připojení k aplikaci v neplatném stavu, aby byla původní oblast během obnovování online.

1. Pomocí vynuceného převzetí služeb při selhání databáze katalogu v oblasti obnovení nastaví primární databázi a aktualizuje _activecatalog_ alias tak, aby odkazoval na Server katalogu obnovení.

1. Aktualizuje alias _newtenant_ tak, aby odkazoval na server tenanta v oblasti obnovení. Změnou tohoto aliasu se zajistí, že se databáze pro všechny nové klienty zřídí v oblasti obnovení. 

1. Označí všechny existující klienty v katalogu obnovení jako offline, aby zabránili přístupu k databázím tenanta před jejich převzetím služeb při selhání.

1. Aktualizuje konfiguraci všech elastických fondů a replikovaných izolovaných databází v oblasti obnovení tak, aby odpovídaly jejich konfiguraci v původní oblasti. (Tato úloha je nutná pouze v případě, že fondy nebo replikované databáze v prostředí pro obnovení jsou během normálního provozu škálované za účelem snížení nákladů).

1. Povolí koncový bod Traffic Manager pro webovou aplikaci v oblasti obnovení. Povolením tohoto koncového bodu umožníte aplikaci zřídit nové klienty. V této fázi jsou stávající klienti stále v režimu offline.

1. Odesílá dávky žádostí o vynucení převzetí služeb při selhání databáze v pořadí podle priority.
    * Dávky jsou uspořádané tak, aby se databáze souběžně převzaly napříč všemi fondy.
    * Požadavky na převzetí služeb při selhání jsou odesílány pomocí asynchronních operací, aby byly odesílány rychle a bylo možné zpracovat více požadavků současně.

   > [!Note]
   > Ve scénáři výpadku jsou primární databáze v původní oblasti v režimu offline.  Vynutit převzetí služeb při selhání u sekundárního přerušení připojení k primárnímu systému bez pokusu o použití jakýchkoli zbývajících transakcí ve frontě. Pokud v době převzetí služeb při selhání dojde v postupu zotavení po havárii k nějakým aktivitám aktualizace, může dojít ke ztrátě dat. Když při převzetí služeb při selhání databáze v oblasti obnovení do původní oblasti dojde k převzetí služeb při selhání, dojde k zajištění neztráty dat pomocí normálního převzetí služeb při selhání.

1. Monitoruje službu, aby zjistila, kdy došlo k převzetí služeb při selhání databáze. Po převzetí služeb při selhání databáze tenanta aktualizuje katalog, aby zaznamenal stav obnovení databáze tenanta, a označí tenanta jako online.
    * K databázím tenanta může aplikace přicházet ihned po označení online v katalogu.
    * Součet hodnot rowversion v databázi tenanta je uložen v katalogu. Tato hodnota funguje jako otisk prstu, který umožňuje procesu repatriace zjistit, jestli se databáze v oblasti obnovení aktualizovala.

### <a name="run-the-script-to-fail-over-to-the-recovery-region"></a>Spusťte skript, který převezme služby při selhání do oblasti obnovení.

Nyní si představte, že v oblasti, ve které je aplikace nasazená, existuje výpadek, který spouští skript pro obnovení:

1. V *prostředí POWERSHELL ISE* otevřete skript. ..\Learning Modules\Business kontinuita a Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1 a nastavte následující hodnoty:
    * **$DemoScenario = 3**, obnovte aplikaci do oblasti obnovení převzetím služeb při selhání do replik.

2. Stisknutím klávesy **F5** spusťte skript.  
    * Skript se otevře v novém okně PowerShellu a potom spustí řadu úloh PowerShellu, které běží paralelně. Tyto úlohy převezmou databáze tenantů v oblasti obnovení.
    * Oblast obnovení je _spárovaná oblast_ přidružená k oblasti Azure, ve které jste aplikaci nasadili. Další informace najdete v tématu [spárované oblasti Azure](../../best-practices-availability-paired-regions.md). 

3. Monitorujte stav procesu obnovení v okně PowerShellu.
    ![proces převzetí služeb při selhání](./media/saas-dbpertenant-dr-geo-replication/failover-process.png)

> [!Note]
> Pokud chcete prozkoumat kód pro úlohy obnovení, zkontrolujte skripty PowerShellu ve složce. ..\Learning Modules\Business kontinuita a zotavení po havárii Recovery\DR-FailoverToReplica\RecoveryJobs.

### <a name="review-the-application-state-during-recovery"></a>Kontrola stavu aplikace během obnovování

I když je koncový bod aplikace v Traffic Manager zakázán, aplikace není k dispozici. Po převzetí služeb při selhání služby Catalog v oblasti obnovení a všech klientech označených jako offline se aplikace vrátí zpět do režimu online. I když je aplikace k dispozici, každý klient se v centru událostí zobrazuje v režimu offline, dokud se převezme jeho databáze při selhání. Je důležité navrhnout aplikaci pro zpracování offline databází klientů.

1. Po obnovení databáze katalogu proveďte aktualizaci centra událostí Wingtip lístky ve webovém prohlížeči.
   * V zápatí si všimněte, že název serveru katalogu má teď příponu _obnovení_ a je umístěný v oblasti obnovení.
   * Všimněte si, že klienti, kteří ještě nejsou obnoveni, jsou označeni jako offline a nelze je vybrat.  

     > [!Note]
     > Pokud chcete obnovit jenom pár databází, možná nebudete moct aktualizovat prohlížeč před dokončením obnovení, takže nevidíte klienty, kteří jsou v režimu offline. 

     ![Centrum událostí offline](./media/saas-dbpertenant-dr-geo-replication/events-hub-offlinemode.png) 

   * Pokud otevřete stránku události offline klienta přímo, zobrazí se oznámení "tenant offline". Pokud je například contoso v režimu offline, zkuste otevřít. http://events.wingtip-dpt.&lt Uživatel &gt; . Trafficmanager.NET/contosoconcerthall ![ Contoso offline stránku](./media/saas-dbpertenant-dr-geo-replication/dr-in-progress-offline-contosoconcerthall.png) 

### <a name="provision-a-new-tenant-in-the-recovery-region"></a>Zřízení nového tenanta v oblasti obnovení
I před převzetím služeb při selhání všemi stávajícími databázemi tenantů můžete zřídit nové klienty v oblasti obnovení.  

1. V *prostředí POWERSHELL ISE* otevřete skript. ..\Learning Modules\Business kontinuita a Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1 a nastavte následující vlastnost:
    * **$DemoScenario = 4**, zřízení nového tenanta v oblasti obnovení

2. Stiskněte klávesu **F5** ke spuštění skriptu a zřízení nového tenanta. 

3. Stránka události Hawthorn hala se otevře v prohlížeči, když se dokončí. Všimněte si z zápatí, že databáze Hawthorn hala je zřízena v oblasti obnovení.
    ![Stránka událostí Hawthorn hala](./media/saas-dbpertenant-dr-geo-replication/hawthornhallevents.png) 

4. V prohlížeči aktualizujte stránku centra událostí Wingtip lístky, aby se zobrazila zahrnutá Hawthorn hala. 
    * Pokud jste zřídili Hawthorn místnosti bez čekání na obnovení ostatních tenantů, můžou být ostatní klienti stále offline.


## <a name="review-the-recovered-state-of-the-application"></a>Zkontrolujte obnovený stav aplikace.

Po dokončení procesu obnovení jsou aplikace a všichni klienti plně funkční v oblasti obnovení. 

1. Jakmile se obrazovka v okně konzoly PowerShellu označuje, že se obnoví všichni klienti, aktualizujte centrum událostí.  Všichni klienti se zobrazí online, včetně nového tenanta, Hawthorn hala.

    ![Obnovená a noví klienti v centru událostí](./media/saas-dbpertenant-dr-geo-replication/events-hub-with-hawthorn-hall.png)

2. V [Azure Portal](https://portal.azure.com)otevřete seznam skupin prostředků.  
    * Všimněte si skupiny prostředků, kterou jste nasadili, a skupiny prostředků obnovení s příponou _obnovení_ .  Skupina prostředků obnovení obsahuje všechny prostředky vytvořené během procesu obnovení a navíc nové prostředky vytvořené během výpadku.  

3. Otevřete skupinu prostředků obnovení a Všimněte si následujících položek:
   * Verze pro obnovení katalogu a serverů tenants1 s příponou _obnovení_ .  Všechny obnovené databáze katalogu a klientů na těchto serverech mají všechny názvy používané v původní oblasti.

   * SQL Server _tenants2-DPT- &lt; User &gt; -Recovery_ .  Tento server se používá ke zřízení nových tenantů během výpadku.
   * App Service s názvem _events-DPT- &lt; recoveryregion &gt; - &lt; User&gt_;, což je instance obnovení aplikace události. 

     ![Prostředky služby Azure Recovery](./media/saas-dbpertenant-dr-geo-replication/resources-in-recovery-region.png) 

4. Otevřete SQL Server _tenants2-DPT- &lt; User &gt; -Recovery_ .  Všimněte si, že obsahuje databázi _hawthornhall_ a elastický fond _Pool1_.  Databáze _hawthornhall_ je nakonfigurovaná jako elastická databáze v elastickém fondu _Pool1_ .

5. Přejděte zpátky do skupiny prostředků a v _tenants1-DPT- &lt; User &gt; -Recovery_ Server klikněte na databázi contoso. Na levé straně klikněte na Geo-Replication.

    ![Databáze Contoso po převzetí služeb při selhání](./media/saas-dbpertenant-dr-geo-replication/contoso-geo-replication-after-failover.png)

## <a name="change-tenant-data"></a>Změna dat tenanta 
V této úloze aktualizujete jednu z databází tenantů. 

1. V prohlížeči Najděte seznam událostí pro společnost Contoso a poznamenejte si poslední název události.
2. V *ISE PowerShellu* ve skriptu. ..\Learning Modules\Business kontinuita a Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1 pro zotavení po havárii nastavte následující hodnotu:
    * **$DemoScenario = 5** Odstranění události z tenanta v oblasti obnovení
3. Stisknutím klávesy **F5** spusťte skript.
4. Aktualizujte stránku s událostmi ve službě contoso na úrovni: http://events.wingtip-dpt.&lt User &gt; . trafficmanager.NET/contosoconcerthall – nahraďte &lt; uživatele &gt; hodnotou uživatele vašeho nasazení a Všimněte si, že poslední událost byla odstraněna.

## <a name="repatriate-the-application-to-its-original-production-region"></a>Vrácení aplikace do původní produkční oblasti

Tato úloha navrácení aplikace do původní oblasti. V reálném scénáři by bylo možné iniciovat vrácení při vyřešení výpadku.

### <a name="repatriation-process-overview"></a>Přehled procesu navracení

![Architektura repatriace](./media/saas-dbpertenant-dr-geo-replication/repatriation-architecture.png)

Proces navracení:
1. Zruší všechny nedokončené nebo probíhající žádosti o obnovení databáze.
2. Aktualizuje alias _newtenant_ tak, aby odkazoval na server tenantů v oblasti zdroje. Změnou tohoto aliasu zajistíte, aby se v oblasti původu teď zřídily databáze pro všechny nové klienty.
3. Semena všech změněných dat tenanta do původní oblasti
4. Převzetí služeb při selhání databázemi klientů v pořadí podle priority.

Převzetí služeb při selhání efektivně přesouvá databázi do původní oblasti. Když databáze převezme služby při selhání, všechna otevřená připojení se vynechá a databáze nebude po pár sekundách k dispozici. Aplikace by měly být napsány pomocí logiky opakování, aby bylo zajištěno jejich opětovné připojení.  I když se toto krátké odpojení často nevšimlo, můžete se rozhodnout, že budete databáze převádět z pracovní doby. 


### <a name="run-the-repatriation-script"></a>Spuštění skriptu pro vrácení
Nyní si představte, že se výpadek vyřeší a spustí se skript pro vrácení.

1. V *prostředí POWERSHELL ISE* ve skriptu. ..\Learning Modules\Business kontinuita a Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1 pro zotavení po havárii.

2. Ověřte, jestli je proces synchronizace katalogu pořád spuštěný v instanci PowerShellu.  V případě potřeby ho restartujte nastavením:
    * **$DemoScenario = 1** spusťte synchronizaci informací o serveru klienta, fondu a konfiguraci databáze do katalogu
    * Stisknutím klávesy **F5** spusťte skript.

3.  Pak zahajte proces navracení, nastavte:
    * **$DemoScenario = 6**, převrácení aplikace do původní oblasti
    * Stisknutím klávesy **F5** spusťte skript pro obnovení v novém okně prostředí PowerShell.  Repatrianí bude trvat několik minut a může se monitorovat v okně PowerShellu.
    ![Proces vrácení](./media/saas-dbpertenant-dr-geo-replication/repatriation-process.png)

4. Když je skript spuštěný, aktualizujte stránku centra událostí ( http://events.wingtip-dpt.&lt ; User &gt; . trafficmanager.NET).
    * Všimněte si, že všichni klienti jsou online a přístupné v celém rámci tohoto procesu.

5. Až se repatriace dokončí, aktualizujte centrum událostí a otevřete stránku události pro Hawthorn hala. Všimněte si, že tato databáze byla předaná do původní oblasti.
    ![Centrum událostí se repatriaí](./media/saas-dbpertenant-dr-geo-replication/events-hub-repatriated.png)


## <a name="designing-the-application-to-ensure-app-and-database-are-colocated"></a>Návrh aplikace, aby se zajistilo, že se aplikace a databáze společně nacházejí 
Aplikace je navržena tak, aby se vždy připojovala z instance ve stejné oblasti jako databáze tenanta. Tento návrh zkracuje latenci mezi aplikací a databází. Tato optimalizace předpokládá, že interakce mezi aplikacemi a databází je chattier než interakce mezi uživatelem a aplikací.  

Databáze tenantů se můžou během navracení mezi obnovením a původními oblastmi rozdělit do několika časových oblastí. U každé databáze aplikace vyhledává oblast, ve které se databáze nachází, pomocí vyhledávání DNS v názvu serveru tenanta. V SQL Database název serveru je alias. Název serveru s aliasem obsahuje název oblasti. Pokud aplikace není ve stejné oblasti jako databáze, přesměruje ji na instanci ve stejné oblasti jako server. Přesměrování na instanci ve stejné oblasti, ve které databáze minimalizuje latenci mezi aplikací a databází. 

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:
> [!div class="checklist"]
> 
> * Synchronizovat informace o konfiguraci databáze a elastického fondu do katalogu tenantů
> * Nastavení prostředí pro obnovení v jiné oblasti, která zahrnuje aplikace, servery a fondy
> * Použití _geografické replikace_ k replikaci katalogu a databází klientů do oblasti obnovení
> * Převzetí služeb při selhání databáze aplikace a katalogu a klientů do oblasti obnovení 
> * Po vyřešení výpadku navrátit databáze aplikace, katalogu a tenanta do původní oblasti

Další informace o technologiích Azure SQL Database poskytuje, aby bylo možné zajistit kontinuitu podnikových aplikací v dokumentaci [Přehled provozní kontinuity](business-continuity-high-availability-disaster-recover-hadr-overview.md) .

## <a name="additional-resources"></a>Další zdroje informací

* [Další kurzy, které se vytvářejí na aplikaci Wingtip SaaS](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)