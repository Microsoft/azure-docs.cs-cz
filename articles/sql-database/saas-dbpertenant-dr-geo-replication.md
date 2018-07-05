---
title: Zotavení po havárii pro aplikace SaaS využívající Azure SQL Database geografickou replikaci | Dokumentace Microsoftu
description: Další informace o použití Azure SQL Database geo repliky na obnovení v případě výpadku aplikace SaaS s více tenanty
keywords: kurz k sql database
services: sql-database
author: AyoOlubeko
manager: craigg
ms.service: sql-database
ms.custom: saas apps
ms.topic: conceptual
ms.date: 04/09/2018
ms.author: ayolubek
ms.openlocfilehash: f2ad92118c00f08e5dcdd4a8a12f007308b3fbd1
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/04/2018
ms.locfileid: "34645789"
---
# <a name="disaster-recovery-for-a-multi-tenant-saas-application-using-database-geo-replication"></a>Zotavení po havárii pro aplikace SaaS s více tenanty pomocí geografické replikace databáze

V tomto kurzu si projít úplné zotavení po havárii pro aplikace SaaS s více tenanty implementované pomocí model databáze na tenanta. K ochraně aplikace před výpadku, můžete použít [ _geografickou replikaci_ ](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-overview) v oblasti obnovení alternativního vytvoření repliky pro databáze katalogu a tenanta. Pokud dojde k výpadku, rychlé převzetí služeb při selhání na tyto repliky obnovit normální obchodní operace. Na převzetí služeb při selhání databáze v původní oblasti budou sekundární repliky databáze v oblasti obnovení. Jakmile tyto repliky přejdou do režimu online, automaticky dohnat na stav databáze v oblasti obnovení. Jakmile se výpadek vyřeší, převezme služby při zpět do databáze v oblasti původní produkčního prostředí.

Tento kurz se věnuje na převzetí služeb při selhání a navrácení služeb po obnovení pracovních postupů. Se dozvíte, jak:
> [!div classs="checklist"]

>* Synchronizace databáze a elastický fond konfigurační informace do katalogu tenanta
>* Nastavení prostředí obnovení ve alternativní oblast, které najdete aplikace, servery a fondy
>* Použití _geografickou replikaci_ k replikaci databáze katalogu a tenanta do oblasti pro zotavení
>* Převzetí služeb při selhání aplikace a databáze katalogu a tenanta do oblasti pro zotavení 
>* Později, převzetí služeb při selhání aplikace, databáze katalogu a tenanta zpět na původní oblast po skončení výpadku se funkčnost
>* Aktualizovat katalog, protože každá databáze tenanta se převzaly při selhání a sledovat primární umístění databáze každý tenant
>* Ujistěte se, aplikace a primární tenant databáze jsou vždy umístěny společně, ve stejné oblasti Azure pro snížení latence  
 

Před zahájením tohoto kurzu, ujistěte se, že se provádějí následující požadavky:
* Nasazení SaaS aplikace Wingtip Tickets databáze na tenanta aplikaci. Nasazení za méně než pět minut najdete v tématu [nasazení a zkoumání SaaS aplikace Wingtip Tickets databáze na tenanta aplikaci.](saas-dbpertenant-get-started-deploy.md)  
* Je nainstalované prostředí Azure PowerShell. Podrobnosti najdete v článku [Začínáme s prostředím Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

## <a name="introduction-to-the-geo-replication-recovery-pattern"></a>Úvod do modelu obnovení geografické replikace

![Architektura obnovení](media/saas-dbpertenant-dr-geo-replication/recovery-architecture.png)
 
Zotavení po havárii (DR) je pro mnoho aplikací, což je důležité pro zajištění souladu s legislativními nebo obchodní kontinuity podnikových procesů. Mělo by být dlouhotrvající výpadku, dobře připravený plán zotavení po Havárii můžete minimalizovat narušení činnosti firmy. Udržováním repliky databáze v oblasti obnovení, které mohou být přebrány do na krátké Všimněte si, že použití geografické replikace poskytuje nejnižší cíl bodu obnovení a RTO.

Založené na geografické replikace plánu zotavení po Havárii A zahrnuje tři samostatné části:
* Nastavení – vytváření a údržbu obnovení prostředí
* Obnovení – převzetí služeb při selhání aplikace a databáze pro obnovení prostředí, pokud dojde k výpadku,
* Navracení – převzetí služeb při selhání aplikace a databáze zpět do původní oblasti po vyřešení aplikace 

Všechny části považuje pečlivě, zejména v případě, že provoz ve velkém měřítku. Celkově plánu musí provádět několik cílů:

* Nastavení
    * Vybudování a udržování zrcadlový prostředí v oblasti obnovení. Vytváření elastických fondů a které se replikují všechny samostatné databáze v tomto prostředí obnovení rezervuje kapacitu v oblasti obnovení. Správa tohoto prostředí zahrnuje replikaci nových databází tenantů, jako jsou zřízené.  
* Obnovení
    * Použití prostředí škálovat dolů obnovení minimalizovat náklady na každodenní fondy a samostatné databáze musí se dá škálovat získat plně funkční kapacity v oblasti obnovení
    * Povolit zřizování v oblasti obnovení co nejdříve nového tenanta  
    * Optimalizované obnovení tenantů v pořadí podle priority
    * Optimalizovat pro získání tenantů online tak rychle tam, kde je to praktické, a to provedením kroků paralelně
    * Být odolné vůči selhání, dá se restartovat a idempotentní
    * Možné zrušit proces v polovině letu, pokud původní oblast je opět online.
* Navracení 
    * Převzetí služeb při selhání databáze z oblasti obnovení repliky v původní oblasti s minimálním dopadem na tenantů: bez ztráty dat a minimální období offline každého tenanta.   

V tomto kurzu se tyto výzvy se tak vyřeší, pomocí funkce služby Azure SQL Database a platformy Azure:

* [Šablony Azure Resource Manageru](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-create-first-template), co nejrychleji rezervovat všechny potřebné kapacitu. Šablony Azure Resource Manageru se používají ke zřízení zrcadlový obraz produkční servery a elastických fondů v oblasti obnovení.
* [Geografická replikace](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-overview), vytvořit asynchronně replikovanou sekundární databáze jen pro čtení pro všechny databáze. Během výpadku převezme služby při selhání v oblasti obnovení repliky.  Až se výpadek vyřeší, služeb při selhání zpátky do databáze v původní oblasti bez ztráty.
* [Asynchronní](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations) operace převzetí služeb při selhání se odesílají v pořadí podle priority tenanta, chcete-li minimalizovat čas převzetí služeb při selhání pro velkým počtem databází.
* [Funkce obnovení správy horizontálních oddílů](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-database-recovery-manager), chcete-li změnit během obnovení a vrácení databáze položky v katalogu. Tyto funkce umožňují aplikace pro připojení k databází tenantů, bez ohledu na umístění bez nutnosti měnit aplikace.
* [Aliasy DNS serveru SQL](https://docs.microsoft.com/azure/sql-database/dns-alias-overview)umožnit bezproblémové zřizování nových tenantů, bez ohledu na to, jakou oblast aplikace pracuje v. Aliasy DNS používají také umožňuje procesu synchronizace katalogu pro připojení k aktivní katalogu, bez ohledu na jeho umístění.

## <a name="get-the-disaster-recovery-scripts"></a>Získat skripty pro zotavení po havárii 

> [!IMPORTANT]
> Stejně jako všechny Wingtip Tickets správu skripty zotavení po Havárii skripty jsou kvalita ukázky a není určen k použití v produkčním prostředí. 

Obnovení jsou k dispozici v skripty používané v tomto kurzu a zdrojový kód aplikace Wingtip [SaaS aplikace Wingtip Tickets databáze na tenanta úložiště GitHub](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant/). Podívejte se [obecné pokyny](saas-tenancy-wingtip-app-guidance-tips.md) pokyny ke stažení a odblokování Wingtip Tickets skripty pro správu.

## <a name="tutorial-overview"></a>Přehled kurzu
V tomto kurzu použijete první geografické replikace k vytvoření repliky aplikací Wingtip Tickets a jeho databázím v jiné oblasti. Potom převzetí služeb při selhání do této oblasti k simulaci obnovení po výpadku. Jakmile budete hotovi, v oblasti obnovení je plně funkční aplikace.

Později v samostatných vrácení kroku, převzetí služeb při selhání databáze katalogu a tenanta v oblasti obnovení do původní oblasti. Aplikace a databází zůstanou k dispozici v rámci navracení. Jakmile budete hotovi, v oblasti původní je plně funkční aplikace.

> [!Note]
> Aplikace se obnoví do _spárované oblasti_ oblasti, ve kterém je aplikace nasazená. Další informace najdete v tématu [spárovaných oblastech Azure](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

## <a name="review-the-healthy-state-of-the-application"></a>Kontrola stavu v pořádku aplikace

Před zahájením procesu obnovení, zkontrolujte stav v pořádku normální aplikace.
1. Ve webovém prohlížeči otevřete Centrum akcí aplikace Wingtip Tickets (http://events.wingtip-dpt.&lt; uživatel&gt;. trafficmanager.net - nahradit &lt;uživatele&gt; s hodnotou uživatele vašeho nasazení).
    * Přejděte do dolní části stránky a Všimněte si, že název serveru katalogu a umístění v zápatí. Umístění je oblast, ve které jste nasadili aplikaci.
    *TIP: Najeďte myší umístění zvětšíte zobrazení. * 
     ![Události v pořádku stav rozbočovače v původní oblast](media/saas-dbpertenant-dr-geo-replication/events-hub-original-region.png)

2. Klikněte na tlačítko v tenantovi Contoso koncertní Hall a otevřete její stránku událostí.
    * V zápatí Všimněte si, že název serveru tenanta. Umístění bude stejné jako umístění pro server katalogu.

3. V [webu Azure portal](https://portal.azure.com), otevřete skupinu prostředků, ve kterém chcete aplikaci nasadit
    * Všimněte si, že oblast, ve kterém jsou servery nasazeny. 

## <a name="sync-tenant-configuration-into-catalog"></a>Konfigurace synchronizace tenantů do katalogu

V této úloze spustí se proces, který synchronizuje konfiguraci serverů, elastické fondy a databáze do katalogu tenanta. Proces udržuje tyto informace v aktuálním stavu v katalogu.  Tento proces funguje v katalogu, aktivní, ať už v původní oblasti nebo v oblasti obnovení. Informace o konfiguraci se používá jako součást procesu obnovení k zajištění obnovení prostředí je konzistentní s původní prostředí, a potom později během navracení k zajištění původní oblast se stane konzistentní s veškeré změny provedené v obnovení prostředí. Katalog se používá také k udržovat přehled o stavu obnovení prostředků tenanta

> [!IMPORTANT]
> Pro zjednodušení procesu synchronizace a dalších dlouho běžící procesy obnovení a vrácení jsou implementovány v těchto kurzech jako místní úlohy prostředí Powershell nebo relace, které běží v rámci vašeho klienta přihlášení uživatele. Tokeny ověřování při přihlášení vyprší po několik hodin a pak se nezdaří úlohy. V případě produkčního prostředí v dlouho běžící procesy by měla být implementována jako spolehlivé služby Azure ve výši určitého druhu spuštěný pod instančního objektu. Zobrazit [pomocí prostředí Azure PowerShell k vytvoření instančního objektu s certifikátem](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authenticate-service-principal).

1. V _prostředí PowerShell ISE_, otevřete soubor Modules\UserConfig.psm1 ...\Learning. Nahraďte `<resourcegroup>` a `<user>` na řádku 10 a 11 s hodnotou použitou při nasazení aplikace.  Uložte soubor!

2. V *prostředí PowerShell ISE*, otevřete ...\Learning skript Modules\Business kontinuity podnikových procesů a Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1 po havárii a nastavte:
    * **$DemoScenario = 1**, spusťte úlohu na pozadí, která se synchronizuje tenanta serveru a informace o konfiguraci fondu do katalogu

3. Stisknutím klávesy **F5** ke spuštění skriptu synchronizace. Synchronizovat konfiguraci prostředků klienta je otevřít novou relaci Powershellu.
![Procesu synchronizace](media/saas-dbpertenant-dr-geo-replication/sync-process.png)

Nechte okno prostředí PowerShell běžet na pozadí a pokračujte zbývající část tohoto kurzu. 

> [!Note]
> Procesu synchronizace se připojí ke katalogu prostřednictvím DNS alias. Tento alias je změněna během obnovení a vrácení tak, aby odkazoval na aktivní katalogu. Procesu synchronizace udržuje katalogu v aktuálním stavu pomocí databáze nebo fondu změny konfigurace provedené v oblasti obnovení.  Během navracení tyto změny se použijí na ekvivalentní prostředky v původní oblast.

## <a name="create-secondary-database-replicas-in-the-recovery-region"></a>Vytváření replik sekundární databáze v oblasti obnovení

V této úloze spustí se proces, který nasadí instanci duplicitní aplikace a katalogu a všech databázích tenantů se replikuje do oblasti pro zotavení.

> [!Note]
> V tomto kurzu přidá ochrany geografické replikace do ukázkové aplikace Wingtip Tickets. V případě produkčního prostředí pro aplikace, který používá geografické replikace by každý tenant zřídí s geograficky replikovaná databáze od samého počátku. Zobrazit [navrhování službami s vysokou dostupností s využitím Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-designing-cloud-solutions-for-disaster-recovery#scenario-1-using-two-azure-regions-for-business-continuity-with-minimal-downtime)

1. V *prostředí PowerShell ISE*otevřete ...\Learning skript Modules\Business kontinuity podnikových procesů a Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1 po havárii a nastavte následující hodnoty:
    * **$DemoScenario = 2**vytvořte zrcadlový obraz obnovení prostředí a replikace databáze katalogu a tenanta

2. Stisknutím klávesy **F5** spusťte skript. Otevře novou relaci Powershellu k vytvoření repliky.
![Procesu synchronizace](media/saas-dbpertenant-dr-geo-replication/replication-process.png)  

## <a name="review-the-normal-application-state"></a>Zkontrolujte stav normální aplikace
V tomto okamžiku aplikace běží normálně. v původní oblasti a nyní je chráněn geografickou replikaci.  Existují sekundární repliky jen pro čtení v oblasti obnovení pro všechny databáze. 
1. Na webu Azure Portal, podívejte se na skupiny prostředků a Všimněte si, že se vytvořila skupinu prostředků s – přípona obnovení v oblasti obnovení. 

1. Prozkoumejte prostředky ve skupině prostředků pro obnovení.  

1. Klikněte na databázi Hall koncertní Contoso na _tenants1-dpt -&lt;uživatele&gt;– obnovení_ serveru.  Na levé straně klikněte na geografickou replikaci. 

    ![Contoso koncertní odkaz geografické replikace](media/saas-dbpertenant-dr-geo-replication/contoso-geo-replication.png) 

Poznamenejte si odkaz geografické replikace mezi primární databází v původní oblasti a v oblasti obnovení sekundární v mapování oblastí Azure.  

## <a name="fail-over-the-application-into-the-recovery-region"></a>Převzetí služeb při selhání aplikace do oblasti pro zotavení

### <a name="geo-replication-recovery-process-overview"></a>Přehled procesu obnovení geografické replikace

Skript pro obnovení provádí následující úlohy:

1. Zakáže koncových bodů Traffic Manageru pro webovou aplikaci v původní oblast. Zakázání koncového bodu zabraňuje uživatelům v připojení k aplikaci v neplatném stavu by měla původní oblast režimu online při obnovení.

1. Používá Vynucené převzetí služeb při selhání databáze katalogu v oblasti obnovení k němu primární databáze a aktualizuje _activecatalog_ aliasu, aby odkazoval na server katalogu obnovení.

1. Aktualizace _newtenant_ aliasu, aby odkazoval na server tenanta v oblasti obnovení. Změnou tohoto aliasu zajistí, že databáze pro všechny nové tenanty jsou zřízené v oblasti obnovení. 

1. Označí všechny stávajících tenantů v katalogu obnovení jako v režimu offline chcete zabránit přístupu do databází tenantů, než při selhání.

1. Aktualizuje konfiguraci všech elastických fondů a replikované samostatných databází v oblasti pro zotavení pro zrcadlení jejich konfigurace v původní oblast. (Tato úloha je potřeba pouze v případě fondů nebo replikované databáze v prostředí obnovení jsou kapacitu vertikálně snížit během normální provoz a snižovat náklady).

1. Umožňuje koncových bodů Traffic Manageru pro webovou aplikaci v oblasti obnovení. Povolení tohoto koncového bodu umožňuje, aby aplikace ke zřízení nových tenantů. V této fázi jsou stále v režimu offline tenantům.

1. Odesílá se, že dávky požadavků pro vynucení převzetí služeb při selhání databáze v pořadí podle priority.
    * Dávky jsou uspořádány tak, aby databáze jsou převzetí služeb při selhání paralelně napříč všechny fondy.
    * Převzetí služeb při selhání žádostí jsou odeslány pomocí asynchronní operace, takže se odešlou rychle a víc požadavků, které je možné zpracovávat současně.

   > [!Note]
   > Ve scénáři výpadku primární databází v původní oblasti jsou offline.  Vynucené převzetí služeb při selhání na sekundární konce připojení na primární bez pokusu o použití jakýchkoli plyne ze zbytkových transakcí zařazených do fronty. Ve scénáři zotavení po Havárii procházení stejně jako v tomto kurzu Pokud aktivita všechny aktualizace v okamžiku převzetí služeb při selhání je možné ztrátě dat. Později při navracení, při převzetí služeb při selhání databáze v oblasti obnovení zpět do původní oblasti normální převzetí služeb při selhání se používá k Ujistěte se, že nedochází ke ztrátě dat.

1. Monitoruje službu SQL database k určení, kdy bylo provedeno databáze přes. Jakmile databáze tenanta se převzaly při selhání, aktualizuje katalogu zaznamenat stav obnovení databáze tenanta a označit jako online tenanta.
    * Databáze tenantů přístupný aplikace poté, co jsou označeny v katalogu online.
    * Součet hodnot rowversion databáze tenanta je uložené v katalogu. Tato hodnota se chová jako otisků prstů, která umožňuje procesu navracení k určení, zda databáze byl aktualizován v oblasti obnovení.

### <a name="run-the-script-to-fail-over-to-the-recovery-region"></a>Spusťte skript pro převzetí služeb při selhání na oblast pro obnovení

Nyní imagine, dojde k výpadku v oblasti, ve kterém je aplikace nasazené a spusťte skript pro obnovení:

1. V *prostředí PowerShell ISE*otevřete ...\Learning skript Modules\Business kontinuity podnikových procesů a Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1 po havárii a nastavte následující hodnoty:
    * **$DemoScenario = 3**, obnovit aplikaci do oblasti pro zotavení podle přebírání služeb při selhání replik

2. Stisknutím klávesy **F5** spusťte skript.  
    * Skript se otevře v novém okně prostředí PowerShell a pak spustí řadu úloh Powershellu, které běží paralelně. Tyto úlohy převzetí služeb při selhání oblast pro obnovení databází tenantů.
    * Oblast pro obnovení je _spárované oblasti_ přidružené k oblasti Azure, ve které jste nasadili aplikaci. Další informace najdete v tématu [spárovaných oblastech Azure](https://docs.microsoft.com/azure/best-practices-availability-paired-regions). 

3. Monitorování stavu procesu obnovení v okně Powershellu.
    ![proces převzetí služeb při selhání](media/saas-dbpertenant-dr-geo-replication/failover-process.png)

> [!Note]
> Prozkoumejte kód pro úlohy obnovení, najdete v tématu Powershellových skriptů v složce ...\Learning Modules\Business kontinuity podnikových procesů a Recovery\DR-FailoverToReplica\RecoveryJobs po havárii.

### <a name="review-the-application-state-during-recovery"></a>Zkontrolujte stav aplikace během obnovení
Zatímco koncový bod aplikace je v Traffic Manageru zakázán, aplikace je k dispozici. Poté, co katalogu se převzaly při selhání na oblast pro obnovení a označena za všechny tenanty v režimu offline, aplikace je opět nepřipojí online. I když je aplikace dostupná, každý tenant zdá se být offline v Centru událostí až do své databáze se převzaly při selhání. Je důležité při návrhu vaší aplikace pro zpracování databází tenantů v režimu offline.

1. Okamžitě po databáze katalogu byla obnovena, aktualizujte Centrum akcí lístků Wingtip ve webovém prohlížeči.
    * V zápatí, Všimněte si, že teď má název serveru katalogu _– obnovení_ přípony a nachází se v oblasti obnovení.
    * Všimněte si, že klienty, kteří se ještě neobnoví, jsou označeny jako offline. proto se nedá vybrat.  

    > [!Note]
    > Jenom několik databází k obnovení nebudete moci aktualizujte prohlížeč, předtím, než se obnovení dokončí, aby klienti nemusí zobrazit, když budou offline. 
 
    ![Centrum akcí do offline režimu](media/saas-dbpertenant-dr-geo-replication/events-hub-offlinemode.png) 

    * Otevřete stránku offline tenanta události přímo, se zobrazí oznámení tenanta na offline. Například pokud Hall koncertní Contoso je v režimu offline, zkuste otevřít http://events.wingtip-dpt.&lt; uživatel&gt;.trafficmanager.net/contosoconcerthall ![stránka Contoso Offline](media/saas-dbpertenant-dr-geo-replication/dr-in-progress-offline-contosoconcerthall.png) 

### <a name="provision-a-new-tenant-in-the-recovery-region"></a>Zřízení nového tenanta v oblasti obnovení
Ještě předtím, než všechny existující databáze tenanta máte převzetí služeb při selhání, můžete zřídit nové tenanty v oblasti obnovení.  

1. V *prostředí PowerShell ISE*otevřete ...\Learning skript Modules\Business kontinuity podnikových procesů a Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1 po havárii a nastavte následující vlastnost:
    * **$DemoScenario = 4**, zřízení nového tenanta v oblasti obnovení

2. Stisknutím klávesy **F5** ke spuštění skriptu a zřízení nového tenanta. 

3. Po dokončení v prohlížeči otevře se stránka Hawthorn Hall události. Poznámka: v zápatí, že je databáze Hawthorn Hall zřízené v oblasti obnovení.
    ![Stránka události hawthorn Hall](media/saas-dbpertenant-dr-geo-replication/hawthornhallevents.png) 

4. V prohlížeči aktualizujte stránku Centrum akcí lístků Wingtip zobrazíte Hawthorn Hall zahrnuty. 
    * Pokud jste zřídili Hawthorn Hall bez čekání na jiných tenantů, obnovení, může být jiných tenantů stále offline.


## <a name="review-the-recovered-state-of-the-application"></a>Zkontrolujte obnovený stav aplikace

Po dokončení procesu obnovení, aplikace a všech tenantů jsou plně funkční v oblasti obnovení. 

1. Po zobrazení v okně konzoly Powershellu označuje, že se obnoví všech tenantů, aktualizujte Centrum akcí.  Klienti se zobrazí online, včetně nového tenanta, Hawthorn vedení se zaměstnanci.

    ![obnovená a nové tenanty v Centru událostí](media/saas-dbpertenant-dr-geo-replication/events-hub-with-hawthorn-hall.png)

2. V [webu Azure portal](https://portal.azure.com), otevřete seznam skupin prostředků.  
    * Všimněte si, že skupiny prostředků, kterou jste nasadili, a navíc skupiny prostředků pro obnovení se _– obnovení_ příponu.  Skupina prostředků pro obnovení obsahuje všechny prostředky vytvořené během procesu obnovení, plus nových prostředků vytvořených během výpadku.  

3. Otevřete skupinu prostředků, obnovení a Všimněte si, že následující položky:
    * Verze obnovení serverů katalogu a tenants1 s _– obnovení_ příponu.  Obnovené databáze katalogu a tenanta na těchto serverech všechny mají názvy používanými v původní oblast.

    * _Tenants2-dpt -&lt;uživatele&gt;– obnovení_ systému SQL server.  Tento server slouží ke zřizování nových tenantů během výpadku.
    *   App Service s názvem, _události-wingtip-dpt -&lt;recoveryregion&gt;-&lt;uživatele & gt_; což je instance obnovení události aplikace. 

    ![Prostředky Azure pro zotavení ](media/saas-dbpertenant-dr-geo-replication/resources-in-recovery-region.png)    
    
4. Otevřít _tenants2-dpt -&lt;uživatele&gt;– obnovení_ systému SQL server.  Všimněte si, že obsahuje databázi _hawthornhall_ a elastický fond _Pool1_.  _Hawthornhall_ databáze je nakonfigurovaný jako elastické databáze v _Pool1_ elastického fondu.

5. Přejděte zpět do skupiny prostředků a klikněte na databázi Hall koncertní Contoso na _tenants1-dpt -&lt;uživatele&gt;– obnovení_ serveru. Na levé straně klikněte na geografickou replikaci.
    
    ![Contoso databáze po převzetí služeb při selhání](media/saas-dbpertenant-dr-geo-replication/contoso-geo-replication-after-failover.png)

## <a name="change-tenant-data"></a>Změna tenanta dat 
V této úloze budete aktualizovat některou z databází tenantů. 

1. V prohlížeči najděte seznamu událostí pro vzájemné součinnosti organizace Contoso a poznamenejte si název poslední události.
2. V *prostředí PowerShell ISE*, v ...\Learning skript Modules\Business kontinuity podnikových procesů a Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1 po havárii, nastavte následující hodnotu:
    * **$DemoScenario = 5** odstraňte událost z klienta v oblasti obnovení
3. Stisknutím klávesy **F5** a spustit skript
4. Aktualizujte stránku události Hall koncertní Contoso (http://events.wingtip-dpt.&lt; uživatel&gt;.trafficmanager.net/contosoconcerthall – nahraďte &lt;uživatele&gt; s hodnotou uživatele vašeho nasazení) a Všimněte si, že poslední událost byla odstraněna.

## <a name="repatriate-the-application-to-its-original-production-region"></a>Repatriovat základní aplikaci, aby jeho původní produkční oblasti

Tato úloha repatriates aplikace do jeho původního oblasti. V reálné situaci by zahájit navracení, až se výpadek vyřeší.

### <a name="repatriation-process-overview"></a>Přehled procesu navracení

![Architektura navracení](media/saas-dbpertenant-dr-geo-replication/repatriation-architecture.png)

Proces vrácení:
1. Zruší všechny žádosti o obnovení databáze nevyřízené nebo vydávaných za pochodu.
2. Aktualizace _newtenant_ aliasu, aby odkazoval na klienty server v oblasti původu. Změnou tohoto aliasu zajistí, že databáze pro všechny nové tenanty nyní se zřídí v oblasti původu.
3. Nasazení nasazuje žádná data změněné tenanta k původní oblast
4. Převezme služby při selhání databáze tenantů v pořadí podle priority.

Převzetí služeb při selhání efektivně přesune databázi do původního oblasti. Když databázi převezme služby při selhání, všechna otevřená připojení se zahodí a databáze není k dispozici pro několik sekund. Aplikace by měla být zapsaný s logikou opakování Ujistěte se, že se znovu připojit.  I když tento stručný odpojení je často není si všimli, můžete repatriovat základní databáze mimo pracovní dobu. 


### <a name="run-the-repatriation-script"></a>Spusťte skript vrácení
Nyní Pojďme imagine je výpadek vyřešen a spusťte skript pro vrácení.

1. V *prostředí PowerShell ISE*...\Learning Modules\Business kontinuity podnikových procesů a skriptů Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1 po havárii.

2. Ověřte, zda proces synchronizace katalogu je stále spuštěna v jeho instance prostředí PowerShell.  V případě potřeby restartujte nastavením:
    * **$DemoScenario = 1**, počáteční synchronizace serveru tenanta, fondu a informace o konfiguraci databáze do katalogu
    * Stisknutím klávesy **F5** spusťte skript.

3.  Pokud chcete spustit proces navracení, nastavte:
    * **$DemoScenario = 6**, repatriovat základní aplikaci do její původní oblast
    * Stisknutím klávesy **F5** ke spuštění skriptu pro obnovení v novém okně prostředí PowerShell.  Navracení bude trvat několik minut a můžete ji sledovat v okně Powershellu.
    ![Navracení procesu](media/saas-dbpertenant-dr-geo-replication/repatriation-process.png)

4. Když je spuštěný skript, aktualizujte stránku centra událostí (http://events.wingtip-dpt.&lt; uživatel&gt;. trafficmanager.net)
    * Všimněte si, že jsou všechny tenanty online a dostupné během tohoto procesu.

5. Po dokončení navracení aktualizujte Centrum akcí a otevřete stránku události pro Hawthorn vedení se zaměstnanci. Všimněte si, že tato databáze obsahuje byla repatriated původní oblast.
    ![Centrum akcí repatriated](media/saas-dbpertenant-dr-geo-replication/events-hub-repatriated.png)


## <a name="designing-the-application-to-ensure-app-and-database-are-colocated"></a>Návrh aplikace, aby aplikace a databáze jsou společně umístěná 
Aplikace je navržený tak, aby se vždycky připojuje z instance ve stejné oblasti jako databáze tenantů. Tento návrh snižuje latence mezi aplikace a databáze. Tato optimalizace se předpokládá, že interakce aplikace k databázi je chattier než interakce aplikace uživatele.  

Databáze tenantů může rozděleny mezi obnovení a původní oblastech nechystáte nějakou dobu, během navracení. Pro každou databázi aplikace vyhledá oblast, ve kterém je databáze umístěna tímto způsobem vyhledávání DNS na název serveru tenanta. Ve službě SQL Database je název serveru alias. Název serveru alias obsahuje název oblasti. Pokud aplikace není ve stejné oblasti jako databáze, přesměruje do instance ve stejné oblasti jako databázového serveru.  Přesměrování instance ve stejné oblasti jako databáze minimalizuje latenci mezi aplikací a databází. 

## <a name="next-steps"></a>Další postup

V tomto kurzu jste se naučili:
> [!div classs="checklist"]

>* Synchronizace databáze a elastický fond konfigurační informace do katalogu tenanta
>* Nastavení prostředí obnovení ve alternativní oblast, které najdete aplikace, servery a fondy
>* Použití _geografickou replikaci_ k replikaci databáze katalogu a tenanta do oblasti pro zotavení
>* Převzetí služeb při selhání aplikace a databáze katalogu a tenanta do oblasti pro zotavení 
>* Navrácení služeb po obnovení databáze aplikace, katalogu a tenanta na původní oblast po skončení výpadku se funkčnost

Další informace o technologiích Azure SQL database poskytuje umožňující kontinuita podnikových procesů v [přehled zajištění provozní kontinuity firmy](sql-database-business-continuity.md) dokumentaci.

## <a name="additional-resources"></a>Další zdroje informací:

* [Další kurzy, které vycházejí z aplikace SaaS aplikace Wingtip](https://docs.microsoft.com/azure/sql-database/sql-database-wtp-overview#sql-database-wingtip-saas-tutorials)
