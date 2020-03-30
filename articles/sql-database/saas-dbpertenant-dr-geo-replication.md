---
title: Zotavení po havárii pro aplikace SaaS s geografickou replikací
description: Zjistěte, jak pomocí geografických replik Azure SQL Database obnovit víceklientskou aplikaci SaaS v případě výpadku.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: AyoOlubeko
ms.author: craigg
ms.reviewer: sstein
ms.date: 01/25/2019
ms.openlocfilehash: 0668ccf5ceb972dd120e4e3f37be6d879a12d0a7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73811707"
---
# <a name="disaster-recovery-for-a-multi-tenant-saas-application-using-database-geo-replication"></a>Zotavení po havárii pro víceklientské aplikace SaaS pomocí geografické replikace databáze

V tomto kurzu prozkoumat úplný scénář zotavení po havárii pro víceklientské aplikace SaaS implementované pomocí modelu databáze na tenanta. Chcete-li aplikaci chránit před výpadkem, použijte [_geografickou replikaci_](sql-database-geo-replication-overview.md) k vytvoření replik pro katalog a databáze klientů v oblasti alternativního obnovení. Pokud dojde k výpadku, rychle převzetí služeb při selhání na tyto repliky obnovit normální obchodní operace. Při převzetí služeb při selhání se databáze v původní oblasti stanou sekundárními replikami databází v oblasti obnovení. Jakmile tyto repliky vrátí do režimu online, automaticky dojdou ke stavu databází v oblasti obnovení. Po vyřešení výpadku se vrátíte zpět do databází v původní produkční oblasti.

Tento kurz zkoumá pracovní postupy převzetí služeb při selhání i navrácení služeb po selhání. Dozvíte se, jak provést tyto akce:
> [!div class="checklist"]
> 
> * Synchronizace informací o konfiguraci databáze a elastického fondu do katalogu klienta
> * Nastavení prostředí pro obnovení v alternativní oblasti, které zahrnuje aplikace, servery a fondy
> * Použití _geografické replikace_ k replikaci databází katalogu a klienta do oblasti obnovení
> * Převzetí služeb při selhání databáze aplikace a katalogu a klienta do oblasti obnovení 
> * Později převzetí služeb při selhání aplikace, katalogu a databáze klienta zpět do původní oblasti po výpadku je vyřešen
> * Aktualizace katalogu jako každá databáze klienta je převzetí služby při selhání ke sledování primárního umístění databáze každého klienta
> * Ujistěte se, že databáze aplikace a primárního tenanta jsou vždy umístěny ve stejné oblasti Azure, aby se snížila latence.  
 

Před zahájením tohoto kurzu se ujistěte, že jsou dokončeny následující požadavky:
* Wingtip Tickets SaaS databáze na klienta aplikace se nasadí. Pokud chcete nasadit za méně než pět minut, [přečtěte si informace o nasazení a prozkoumání databáze Wingtip Tickets SaaS na klienta.](saas-dbpertenant-get-started-deploy.md)  
* Je nainstalované prostředí Azure PowerShell. Podrobnosti najdete v článku [Začínáme s prostředím Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

## <a name="introduction-to-the-geo-replication-recovery-pattern"></a>Úvod do vzoru obnovení geografické replikace

![Architektura obnovení](media/saas-dbpertenant-dr-geo-replication/recovery-architecture.png)
 
Zotavení po havárii (DR) je důležitým aspektem pro mnoho aplikací, ať už z důvodů dodržování předpisů nebo kontinuity provozu. Dojde-li k dlouhodobému výpadku služby, může dobře připravený plán zotavení po havárii minimalizovat narušení podnikání. Použití geografické replikace poskytuje nejnižší RPO a RTO udržováním replik y databáze v oblasti obnovení, které lze v krátké době převést na služby při selhání.

Plán zotavení po havárii založený na geografické replikaci se skládá ze tří různých částí:
* Set-up - vytvoření a údržba prostředí pro využití
* Obnovení – převzetí služeb při selhání aplikace a databází do prostředí pro obnovení, pokud dojde k výpadku,
* Repatriace – převzetí služeb při selhání aplikace a databází zpět do původní oblasti po vyřešení aplikace 

Všechny díly musí být pečlivě zváženy, zejména pokud pracují ve velkém měřítku. Celkově musí plán dosáhnout několika cílů:

* Nastavení
    * Vytvořte a udržujte prostředí zrcadlového obrazu v oblasti obnovení. Vytvoření elastických fondů a replikace všech databází v tomto prostředí pro obnovení rezervuje kapacitu v oblasti obnovení. Udržování tohoto prostředí zahrnuje replikaci nových databází klientů při jejich zřizování.  
* Obnovení
    * Pokud se prostředí pro horizontální navýšení kapacity používá k minimalizaci každodenních nákladů, musí být fondy a databáze rozšířeny tak, aby získaly plnou provozní kapacitu v oblasti obnovení.
    * Povolit nové zřizování klienta v oblasti obnovení co nejdříve  
    * Být optimalizovány pro obnovení klientů v pořadí podle priority
    * Být optimalizovánpro získání nájemníků on-line co nejrychleji tím, že dělá kroky paralelně, kde praktické
    * Buďte odolní vůči selhání, restartování a idempotentní
    * Pokud se původní oblast vrátí do úpletu, je možné proces zrušit v polovině letu.
* Repatriace 
    * Databáze převzetí služeb při selhání z oblasti obnovení do replik v původní oblasti s minimálním dopadem na klienty: žádná ztráta dat a minimální doba offline na klienta.   

V tomto kurzu se tyto problémy řeší pomocí funkcí Azure SQL Database a platformy Azure:

* [Šablony Azure Resource Manageru](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-create-first-template), které vám vyhradí veškerou potřebnou kapacitu co nejrychleji. Šablony Azure Resource Manager se používají k zřízení zrcadlové image produkčních serverů a elastických fondů v oblasti obnovení.
* [Geografická replikace , chcete-li](sql-database-geo-replication-overview.md)vytvořit asynchronně replikované sekundární databáze jen pro čtení pro všechny databáze. Během výpadku převzetí služeb při selhání repliky v oblasti obnovení.  Po vyřešení výpadku se vrátíte zpět do databází v původní oblasti bez ztráty dat.
* [Asynchronní](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations) operace převzetí služeb při selhání odeslané v pořadí priorit klienta, aby se minimalizovala doba převzetí služeb při selhání pro velký počet databází.
* [Funkce obnovení správy svižnosti úložná](sql-database-elastic-database-recovery-manager.md)moc , chcete-li změnit položky databáze v katalogu během obnovení a repatriace. Tyto funkce umožňují aplikaci připojit k databázím klienta bez ohledu na umístění bez změny konfigurace aplikace.
* [Aliasy DNS serveru SQL server](dns-alias-overview.md), které umožňují bezproblémové zřizování nových klientů bez ohledu na to, ve které oblasti aplikace pracuje. Aliasy DNS se také používají k tomu, aby se proces synchronizace katalogu mohl připojit k aktivnímu katalogu bez ohledu na jeho umístění.

## <a name="get-the-disaster-recovery-scripts"></a>Získejte skripty pro zotavení po havárii 

> [!IMPORTANT]
> Stejně jako všechny skripty správy Wingtip Tickets, skripty zotavení po Havárii jsou ukázkové kvality a nemají být použity v produkčním prostředí. 

Skripty pro obnovení použité v tomto kurzu a zdrojový kód aplikace Wingtip jsou k dispozici v [databázi Wingtip Tickets SaaS na tenanta úložiště GitHub](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant/). Podívejte se na [obecné pokyny pro](saas-tenancy-wingtip-app-guidance-tips.md) kroky ke stažení a odblokování skriptů pro správu wingtip vstupenek.

## <a name="tutorial-overview"></a>Přehled kurzu
V tomto kurzu nejprve použít geografickou replikaci k vytvoření repliky aplikace Wingtip Tickets a její databáze v jiné oblasti. Potom převzetí služeb při selhání do této oblasti simulovat zotavuje z výpadku. Po dokončení aplikace je plně funkční v oblasti obnovení.

Později v samostatném kroku repatriace převzetí služeb při selhání katalogu a klienta databází v oblasti obnovení do původní oblasti. Aplikace a databáze zůstávají k dispozici po celou dobu repatriace. Po dokončení je aplikace plně funkční v původní oblasti.

> [!Note]
> Aplikace je obnovena do _spárované oblasti_ oblasti, ve které je aplikace nasazena. Další informace najdete v tématu [Azure spárované oblasti](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

## <a name="review-the-healthy-state-of-the-application"></a>Kontrola stavu aplikace v pořádku

Před zahájením procesu obnovení zkontrolujte normální stav v pořádku aplikace.
1. Ve webovém prohlížeči otevřete Centrumhttp://events.wingtip-dpt.&ltudálostí&gt;wingtip tickets &lt;&gt; ( ;uživatel .trafficmanager.net - nahraďte uživatele uživatelskou hodnotou nasazení).
    * Přejděte do dolní části stránky a všimněte si názvu a umístění serveru katalogu v zápatí. Umístění je oblast, ve které jste nasadili aplikaci.
    *TIP: Najeďte myší na místo pro zvětšení displeje.* 
    Události rozbočovače v původním ![stavu](media/saas-dbpertenant-dr-geo-replication/events-hub-original-region.png)

2. Klikněte na nájemce Koncertní síně Contoso a otevřete jeho stránku akce.
    * V zápatí si všimněte názvu serveru klienta. Umístění bude stejné jako umístění serveru katalogu.

3. Na [webu Azure Portal](https://portal.azure.com)otevřete skupinu prostředků, ve které se aplikace nasazuje.
    * Všimněte si oblasti, ve které jsou servery nasazeny. 

## <a name="sync-tenant-configuration-into-catalog"></a>Synchronizace konfigurace klienta do katalogu

V této úloze spustíte proces, který synchronizuje konfiguraci serverů, elastických fondů a databází do katalogu klienta. Tento proces udržuje tyto informace aktuální v katalogu.  Proces pracuje s aktivním katalogem, ať už v původní oblasti nebo v oblasti obnovení. Informace o konfiguraci se používají jako součást procesu obnovení, aby bylo zajištěno, že prostředí pro obnovení je konzistentní s původním prostředím, a později během repatriace, aby bylo zajištěno, že původní oblast bude v souladu se všemi změnami provedenými v prostředí pro obnovu. Katalog se také používá ke sledování stavu obnovení prostředků klienta

> [!IMPORTANT]
> Pro jednoduchost proces synchronizace a další dlouho běžící procesy obnovení a repatriace jsou implementovány v těchto kurzech jako místní úlohy nebo relace prostředí PowerShell, které běží pod přihlášením klientského uživatele. Ověřovací tokeny vydané při přihlášení vyprší po několika hodinách a úlohy se pak nezdaří. Ve scénáři výroby by měly být implementovány dlouhotrvající procesy jako spolehlivé služby Azure nějakého druhu, spuštěné pod instančníobjekt. Viz [Použití Azure PowerShellu k vytvoření instančního objektu s certifikátem](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authenticate-service-principal).

1. V _prostředí PowerShell ISE_otevřete soubor ...\Learning Modules\UserConfig.psm1. Nahraďte `<resourcegroup>` a `<user>` na řádcích 10 a 11 hodnotou použitou při nasazení aplikace.  Uložte soubor!

2. Ve skriptu *PowerShell ISE*otevřete skript ...\Learning Modules\Business Continuity and Disaster Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1 a nastavte:
    * **$DemoScenario = 1**, Spuštění úlohy na pozadí, která synchronizuje server klienta, a informace o konfiguraci fondu do katalogu

3. Stisknutím **klávesy F5** spusťte synchronizační skript. Otevře se nová relace PowerShellu pro synchronizaci konfigurace prostředků klienta.
![Proces synchronizace](media/saas-dbpertenant-dr-geo-replication/sync-process.png)

Ponechte okno Prostředí PowerShell spuštěné na pozadí a pokračujte zbytkem kurzu. 

> [!Note]
> Proces synchronizace se připojuje ke katalogu pomocí aliasu DNS. Tento alias je upraven během obnovení a repatriace tak, aby ukazoval na aktivní katalog. Proces synchronizace udržuje katalog aktuální s všechny změny konfigurace databáze nebo fondu provedené v oblasti obnovení.  Během repatriace jsou tyto změny použity na ekvivalentní prostředky v původní oblasti.

## <a name="create-secondary-database-replicas-in-the-recovery-region"></a>Vytvoření sekundárních replik databáze v oblasti obnovení

V této úloze spustíte proces, který nasadí duplicitní instanci aplikace a replikuje katalog a všechny databáze klientů do oblasti obnovení.

> [!Note]
> Tento kurz přidá ochranu geografické replikace do ukázkové aplikace Wingtip Tickets. Ve scénáři výroby pro aplikaci, která používá geografickou replikaci, by každý klient zřazoval geograficky replikovanou databázi od začátku. Podívejte se na [návrh vysoce dostupných služeb pomocí Azure SQL Database](sql-database-designing-cloud-solutions-for-disaster-recovery.md#scenario-1-using-two-azure-regions-for-business-continuity-with-minimal-downtime)

1. Ve skriptu *PowerShell ISE*otevřete skript ...\Learning Modules\Business Continuity and Disaster Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1 a nastavte následující hodnoty:
    * **$DemoScenario = 2**, Vytvořit prostředí pro obnovení zrcadlového obrazu a replikovat databáze katalogu a klienta

2. Stisknutím klávesy **F5** spusťte skript. K vytvoření replik se otevře nová relace prostředí PowerShell.
![Proces synchronizace](media/saas-dbpertenant-dr-geo-replication/replication-process.png)  

## <a name="review-the-normal-application-state"></a>Kontrola normálního stavu aplikace

V tomto okamžiku aplikace běží normálně v původní oblasti a nyní je chráněn geo-replikace.  Sekundární repliky jen pro čtení existují v oblasti obnovení pro všechny databáze. 

1. Na webu Azure Portal se podívejte na skupiny prostředků a všimněte si, že byla vytvořena skupina prostředků s příponou -recovery v oblasti obnovení. 

2. Prozkoumejte prostředky ve skupině prostředků pro obnovení.  

3. Klikněte na databázi Contoso Concert Hall na _serveru pro obnovení klienty1-dpt-&lt;user-recovery.&gt;_  Klikněte na Geo-Replikace na levé straně. 

    ![Contoso Koncert geo-replikace odkaz](media/saas-dbpertenant-dr-geo-replication/contoso-geo-replication.png) 

V mapě oblastí Azure si všimněte geografického replikačního propojení mezi primárním v původní oblasti a sekundárním v oblasti obnovení.  

## <a name="fail-over-the-application-into-the-recovery-region"></a>Převzetí služeb při selhání aplikace do oblasti obnovení

### <a name="geo-replication-recovery-process-overview"></a>Přehled procesu obnovení geografické replikace

Skript pro obnovení provádí následující úkoly:

1. Zakáže koncový bod Traffic Manageru pro webovou aplikaci v původní oblasti. Zakázání koncového bodu zabrání uživatelům připojení k aplikaci v neplatném stavu by původní oblast přepne do režimu online během obnovení.

1. Použije vynucenou převzetí služeb při selhání databáze katalogu v oblasti obnovení, aby se z ní stalo primární databází, a aktualizuje alias _activecatalog_ tak, aby ukazoval na server katalogu obnovení.

1. Aktualizuje _nový alias tenanta_ tak, aby ukazoval na server klienta v oblasti obnovení. Změna tohoto aliasu zajistí, že databáze pro všechny nové klienty jsou zřízeny v oblasti obnovení. 

1. Označí všechny existující klienty v katalogu obnovení jako offline, aby se zabránilo přístupu k databázím klienta před jejich převzetím služby při selhání.

1. Aktualizuje konfiguraci všech elastických fondů a replikovaných jednotlivých databází v oblasti obnovení tak, aby odrážela jejich konfiguraci v původní oblasti. (Tato úloha je potřebná pouze v případě, že fondy nebo replikované databáze v prostředí pro obnovení jsou zmenšeny během běžných operací, aby se snížily náklady).

1. Povolí koncový bod Traffic Managerpro webovou aplikaci v oblasti obnovení. Povolení tohoto koncového bodu umožňuje aplikaci zřídit nové klienty. V této fázi jsou stávající klienti stále offline.

1. Odešle dávky požadavků na vynucení databází převzetí služeb při selhání v pořadí podle priority.
    * Listy jsou uspořádány tak, aby databáze byly převzetí mašit souběžně ve všech fondech.
    * Požadavky na převzetí služeb při selhání jsou odeslány pomocí asynchronních operací, takže jsou odeslány rychle a více požadavků lze zpracovat současně.

   > [!Note]
   > V případě výpadku primární databáze v původní oblasti jsou offline.  Vynucení převzetí služeb při selhání na sekundární přeruší připojení k primární bez pokusu o použití všechny zbývající transakce ve frontě. Ve scénáři dr. vrtačky, jako je tento kurz, pokud je nějaká aktivita aktualizace v době převzetí služeb při selhání může dojít ke ztrátě dat. Později během repatriace při převzetí služeb při převzetí služeb při převzetí služeb při převzetí služeb při převzetí služeb při převzetí služeb při převzetí služeb při obnovení oblasti zpět do původní oblasti, normální převzetí služeb při selhání se používá k zajištění žádné ztráty dat.

1. Monitoruje databázovou službu SQL a určuje, kdy byly databáze převzetí služeb při selhání. Jakmile je databáze klienta převzetí služeb při selhání, aktualizuje katalog zaznamenat stav obnovení databáze klienta a označit klienta jako online.
    * K databázím klienta může aplikace přistupovat, jakmile jsou označeny online v katalogu.
    * Součet hodnot rowversion v databázi klienta je uložen v katalogu. Tato hodnota funguje jako otisk prstu, který umožňuje procesu repatriace k určení, pokud byla databáze aktualizována v oblasti obnovení.

### <a name="run-the-script-to-fail-over-to-the-recovery-region"></a>Spuštění skriptu pro převzetí služeb při selhání do oblasti obnovení

Nyní si představte, že je výpadek v oblasti, ve které je aplikace nasazena a spustit skript pro obnovení:

1. Ve skriptu *PowerShell ISE*otevřete skript ...\Learning Modules\Business Continuity and Disaster Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1 a nastavte následující hodnoty:
    * **$DemoScenario = 3**, Obnovit aplikaci do oblasti obnovení převzetím systému selhání na repliky

2. Stisknutím klávesy **F5** spusťte skript.  
    * Skript se otevře v novém okně Prostředí PowerShell a spustí řadu úloh prostředí PowerShell, které běží paralelně. Tyto úlohy převzetí služeb při selhání databází klienta do oblasti obnovení.
    * Oblast obnovení je _spárovaná oblast_ přidružená k oblasti Azure, ve které jste nasadili aplikaci. Další informace najdete v tématu [Azure spárované oblasti](https://docs.microsoft.com/azure/best-practices-availability-paired-regions). 

3. Sledujte stav procesu obnovení v okně Prostředí PowerShell.
    ![proces převzetí služeb při selhání](media/saas-dbpertenant-dr-geo-replication/failover-process.png)

> [!Note]
> Chcete-li prozkoumat kód úloh pro obnovení, zkontrolujte skripty prostředí PowerShell ve složce ...\Learning Modules\Business Continuity and Disaster Recovery\DR-FailoverToReplica\RecoveryJobs.

### <a name="review-the-application-state-during-recovery"></a>Kontrola stavu aplikace během obnovení

Zatímco koncový bod aplikace je ve Správci provozu zakázán, aplikace není k dispozici. Po převzetí služeb při selhání katalogu do oblasti obnovení a všechny klienty označené offline, aplikace je přenesena zpět do režimu online. Přestože je aplikace k dispozici, každý klient se zobrazí v offline v centru událostí, dokud jeho databáze je převzetí služeb při selhání. Je důležité navrhnout aplikaci pro zpracování offline databází klientů.

1. Okamžitě po obnovení databáze katalogu aktualizujte centrum událostí wingtip ových lístků ve webovém prohlížeči.
   * V zápatí všimněte si, že název serveru katalogu má nyní příponu _-recovery_ a je umístěn v oblasti obnovení.
   * Všimněte si, že klienti, které ještě nejsou obnoveny, jsou označeny jako offline a nejsou volitelné.  

     > [!Note]
     > S pouze několik databází obnovit, nemusí být možné aktualizovat prohlížeč před dokončením obnovení, takže se nemusí zobrazit klienty, když jsou v offline. 
 
     ![Centrum událostí offline](media/saas-dbpertenant-dr-geo-replication/events-hub-offlinemode.png) 

   * Pokud otevřete stránku Události offline tenanta přímo, zobrazí se oznámení klienta offline. Pokud je například koncertní síň Contoso offline, zkuste otevřít http://events.wingtip-dpt.&ltstránku .user&gt;.trafficmanager.net/contosoconcerthall ![Contoso Offline](media/saas-dbpertenant-dr-geo-replication/dr-in-progress-offline-contosoconcerthall.png) 

### <a name="provision-a-new-tenant-in-the-recovery-region"></a>Zřízení nového klienta v oblasti obnovení
Ještě předtím, než všechny existující databáze klientů byly převzetí služeb při selhání, můžete zřídit nové klienty v oblasti obnovení.  

1. Ve skriptu *PowerShell ISE*otevřete skript ...\Learning Modules\Business Continuity and Disaster Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1 a nastavte následující vlastnost:
    * **$DemoScenario = 4**, Zřízení nového klienta v oblasti obnovení

2. Stisknutím **klávesy F5** spusťte skript a zřídíte nového klienta. 

3. Po dokončení se v prohlížeči otevře stránka událostí Hawthorn Hall. Všimněte si z zápatí, že databáze Hawthorn Hall je zřízena v oblasti obnovení.
    ![Hawthorn Hall Události Page](media/saas-dbpertenant-dr-geo-replication/hawthornhallevents.png) 

4. V prohlížeči aktualizujte stránku Wingtip Tickets Hub, abyste viděli Hawthorn Hall. 
    * Pokud jste zřídili Hawthorn Hall bez čekání na ostatní nájemníky k obnovení, ostatní klienti mohou být stále offline.


## <a name="review-the-recovered-state-of-the-application"></a>Zkontrolujte obnovený stav aplikace

Po dokončení procesu obnovení aplikace a všichni klienti jsou plně funkční v oblasti obnovení. 

1. Jakmile se na displeji v okně konzoly PowerShell indikuje, že jsou všichni klienti obnoveni, aktualizujte centrum Events Hub.  Všichni nájemníci se objeví online, včetně nového nájemníka Hawthorn Hall.

    ![obnovené a noví klienti v centru událostí](media/saas-dbpertenant-dr-geo-replication/events-hub-with-hawthorn-hall.png)

2. Na [webu Azure Portal](https://portal.azure.com)otevřete seznam skupin prostředků.  
    * Všimněte si skupiny prostředků, kterou jste nasadili, plus skupinu prostředků pro obnovení s příponou _-recovery._  Skupina prostředků pro obnovení obsahuje všechny prostředky vytvořené během procesu obnovení a nové prostředky vytvořené během výpadku.  

3. Otevřete skupinu prostředků pro obnovení a všimněte si následujících položek:
   * Verze pro obnovení katalogu a tenants1 servery s _příponou -recovery._  Obnovené databáze katalogu a klienta na těchto serverech mají názvy používané v původní oblasti.

   * _Tenants2-dpt-&lt;user&gt;-recovery_ SQL server.  Tento server se používá pro zřizování nových klientů během výpadku.
   * Služba App Service s _názvem, events-wingtip-dpt-&lt;recoveryregion&gt;-&lt;uživatele&gt_;, což je instance obnovení aplikace Události. 

     ![Prostředky pro obnovení Azure](media/saas-dbpertenant-dr-geo-replication/resources-in-recovery-region.png) 
    
4. Otevřete _&lt;klienty2-dpt-&gt;user-recovery_ SQL server.  Všimněte si, že obsahuje databázi _hawthornhall_ a elastické fondu, _Pool1_.  _Hawthornhall_ databáze je nakonfigurován jako elastické databáze v _pool1_ elastického fondu.

5. Přejděte zpět do skupiny prostředků a klikněte na databázi Contoso Concert Hall na _serveru pro obnovení klienty1-dpt-&lt;user-recovery.&gt;_ Klikněte na Geo-Replikace na levé straně.
    
    ![Databáze Contoso po převzetí služeb při selhání](media/saas-dbpertenant-dr-geo-replication/contoso-geo-replication-after-failover.png)

## <a name="change-tenant-data"></a>Změna dat klienta 
V této úloze můžete aktualizovat jednu z databází klienta. 

1. V prohlížeči najděte seznam událostí koncertní síně Contoso a poznamenejte si název poslední události.
2. Ve skriptu *PowerShell ISE*nastavte ve skriptu ...\Learning Modules\Business Continuity and Disaster Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1 následující hodnotu:
    * **$DemoScenario = 5** Odstranění události z klienta v oblasti obnovení
3. Stisknutím **klávesy F5** spustíte skript.
4. Aktualizujte stránku událostí vhttp://events.wingtip-dpt.&ltkoncertní&gt;síni Contoso ( ;uživatel .trafficmanager.net/contosoconcerthall - nahraďte &lt;uživatele&gt; uživatelskou hodnotou vašeho nasazení) a všimněte si, že poslední událost byla odstraněna.

## <a name="repatriate-the-application-to-its-original-production-region"></a>Repatriovat aplikaci do původního produkčního regionu

Tento úkol repatriuje aplikaci do původní oblasti. V reálném scénáři byste zahájit repatriaci při výpadku je vyřešen.

### <a name="repatriation-process-overview"></a>Přehled procesu repatriace

![Repatriační architektura](media/saas-dbpertenant-dr-geo-replication/repatriation-architecture.png)

Proces repatriace:
1. Zruší všechny nevyřízené nebo za letu požadavky na obnovení databáze.
2. Aktualizuje _nový alias tenanta_ tak, aby ukazoval na server klientů v oblasti původu. Změna tohoto aliasu zajistí, že databáze pro všechny nové klienty budou nyní zřízeny v oblasti původu.
3. Semena všechna změněná data klienta do původní oblasti
4. Převzetí služby při selhání databází klienta v pořadí podle priority.

Převzetí služeb při selhání efektivně přesune databázi do původní oblasti. Při převzetí služby při selhání databáze jsou zrušena všechna otevřená připojení a databáze není k dispozici po dobu několika sekund. Aplikace by měly být napsány s logikou opakování, aby bylo zajištěno, že se znovu připojí.  Ačkoli toto krátké odpojení je často nevšiml, můžete se rozhodnout repatriovat databáze mimo pracovní dobu. 


### <a name="run-the-repatriation-script"></a>Spuštění skriptu repatriace
Nyní si představte, že výpadek je vyřešen a spusťte skript repatriace.

1. Ve skriptu *PowerShell ISE*ve skriptu ...\Learning Modules\Business Continuity and Disaster Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1.

2. Ověřte, zda je proces synchronizace katalogu stále spuštěný v instanci prostředí PowerShell.  V případě potřeby jej restartujte nastavením:
    * **$DemoScenario = 1**, Spustit synchronizaci informací o konfiguraci serveru klienta, fondu a databáze do katalogu
    * Stisknutím klávesy **F5** spusťte skript.

3.  Chcete-li zahájit proces repatriace, nastavte:
    * **$DemoScenario = 6**, Repatriate aplikace do původního regionu
    * Stisknutím **klávesy F5** spusťte skript pro obnovení v novém okně prostředí PowerShell.  Repatriace bude trvat několik minut a lze sledovat v okně Prostředí PowerShell.
    ![Proces repatriace](media/saas-dbpertenant-dr-geo-replication/repatriation-process.png)

4. Během spuštění skriptu aktualizujte stránkuhttp://events.wingtip-dpt.&ltCentrum&gt;událostí ( ;uživatel .trafficmanager.net)
    * Všimněte si, že všichni klienti jsou online a přístupné v průběhu tohoto procesu.

5. Po dokončení repatriace aktualizujte centrum Události a otevřete stránku událostí pro Hawthorn Hall. Všimněte si, že tato databáze byla repatriována do původní oblasti.
    ![Centrum událostí repatriované](media/saas-dbpertenant-dr-geo-replication/events-hub-repatriated.png)


## <a name="designing-the-application-to-ensure-app-and-database-are-colocated"></a>Návrh aplikace pro zajištění spoluumístění aplikace a databáze 
Aplikace je navržena tak, aby se vždy připojuje z instance ve stejné oblasti jako databáze klienta. Tento návrh snižuje latenci mezi aplikací a databází. Tato optimalizace předpokládá, že interakce mezi aplikacemi a databázemi je chatovatelnější než interakce mezi uživateli a aplikacemi.  

Databáze klienta může být rozložena na obnovu a původní oblasti po určitou dobu během repatriace. Pro každou databázi aplikace vyhledá oblast, ve které je databáze umístěna, provedením vyhledávání DNS na názvu serveru klienta. V databázi SQL database je název serveru alias. Název aliasovaného serveru obsahuje název oblasti. Pokud aplikace není ve stejné oblasti jako databáze, přesměruje na instanci ve stejné oblasti jako databázový server.  Přesměrování na instanci ve stejné oblasti jako databáze minimalizuje latenci mezi aplikací a databází. 

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:
> [!div class="checklist"]
> 
> * Synchronizace informací o konfiguraci databáze a elastického fondu do katalogu klienta
> * Nastavení prostředí pro obnovení v alternativní oblasti, které zahrnuje aplikace, servery a fondy
> * Použití _geografické replikace_ k replikaci databází katalogu a klienta do oblasti obnovení
> * Převzetí služeb při selhání databáze aplikace a katalogu a klienta do oblasti obnovení 
> * Po obnovení služby navrácení služeb po obnovení zabezpečení jsou databáze aplikací, katalogů a klientů do původní oblasti po vyřešení výpadku.

Další informace o technologiích, které databáze Azure SQL poskytuje, abyste umožnili kontinuitu podnikání v dokumentaci [k přehledu kontinuity podnikání.](sql-database-business-continuity.md)

## <a name="additional-resources"></a>Další zdroje

* [Další výukové programy, které vycházejí z aplikace Wingtip SaaS](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
