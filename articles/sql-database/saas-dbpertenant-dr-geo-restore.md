---
title: Použít geograficky redundantní zálohy Azure SQL Database pro zotavení po havárii aplikace SaaS | Dokumentace Microsoftu
description: Naučte se používat Azure SQL Database geograficky redundantní zálohy k obnovení v případě výpadku víceklientské aplikace SaaS
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: AyoOlubeko
ms.author: ayolubek
ms.reviewer: sstein
manager: craigg
ms.date: 10/05/2018
ms.openlocfilehash: 784fd1797f2a4f85842666c9fa987a2decc933c2
ms.sourcegitcommit: 0bb8db9fe3369ee90f4a5973a69c26bff43eae00
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2018
ms.locfileid: "48868713"
---
# <a name="use-geo-restore-to-recover-a-multitenant-saas-application-from-database-backups"></a>Geografické obnovení použijte k obnovení ze zálohy databáze víceklientské aplikace SaaS

Tento kurz se věnuje celé zotavení po havárii pro víceklientské aplikace SaaS s databází na tenanta modelu implementovaná. Použijete [geografické obnovení](https://docs.microsoft.com/azure/sql-database/sql-database-recovery-using-backups) obnovovat databáze katalogu a tenanta z automaticky udržována geograficky redundantní zálohy do určitá alternativní oblast. Jakmile se výpadek vyřeší, použijete [geografickou replikaci](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-overview) repatriovat základní změny databáze do jejich původního oblasti.

![Architektura geografické obnovení](media/saas-dbpertenant-dr-geo-restore/geo-restore-architecture.png)

Geografické obnovení je řešení zotavení po havárii nejnižšími náklady pro službu Azure SQL Database. Nicméně obnovení z geograficky redundantní zálohy může dojít ke ztrátě až jednu hodinu. Může trvat docela dlouho, v závislosti na velikosti každé databázi. 

> [!NOTE]
> Obnovení aplikace s nejnižší možné RPO a RTO pomocí georeplikace místo geografické obnovení.

Tento kurz se věnuje obnovení a vrácení pracovních postupů. Získáte informace o těchto tématech:
> [!div class="checklist"]

>* Synchronizace databáze a elastický fond konfigurační informace do katalogu tenanta.
>* Nastavte zrcadlový obraz prostředí v oblasti obnovení, který zahrnuje aplikace, servery a fondy.   
>* Obnovení databáze katalogu a tenanta pomocí geografického obnovení.
>* Použití geografické replikace repatriovat základní katalogu tenanta a změněných tenantských databázích, až se výpadek vyřeší.
>* Aktualizovat katalog, jak jednotlivé databáze obnovit (nebo repatriated) ke sledování aktuálního umístění aktivní kopie databáze na každého tenanta.
>* Ujistěte se, že aplikace a databáze tenanta vždy ve společném umístění do stejné oblasti Azure pro snížení latence. 
 

Než začnete tento kurz, zajistěte splnění následujících požadavků:
* Nasazení SaaS aplikace Wingtip Tickets databáze na tenanta aplikaci. Nasazení za méně než pět minut najdete v tématu [nasazení a zkoumání SaaS aplikace Wingtip Tickets databáze na tenanta aplikaci](saas-dbpertenant-get-started-deploy.md). 
* Nainstalujte Azure PowerShell. Podrobnosti najdete v tématu [Začínáme s Azure Powershellem](https://docs.microsoft.com/powershell/azure/get-started-azureps).

## <a name="introduction-to-the-geo-restore-recovery-pattern"></a>Úvod do modelu obnovení geografické obnovení

Zotavení po havárii (DR) je pro mnoho aplikací, což je důležité pro zajištění souladu s legislativními nebo obchodní kontinuity podnikových procesů. V případě výpadku služby dlouhotrvající, dobře připravený plán zotavení po Havárii můžete minimalizovat narušení činnosti firmy. Plán zotavení po Havárii A geografické obnovení podle musíte provést několik cílů:
 * Rezervujte všechny potřebné kapacitu v oblasti vybrané obnovení co nejrychleji k zajištění, že je k dispozici pro obnovení databází tenantů.
 * Zřízení prostředí obnovení zrcadlový obraz, který odráží původní konfiguraci fondu a databáze. 
 * Povolit zrušení v procesu obnovení v polovině letu, pokud původní oblast vrátí do režimu online.
 * Povolte rychlé zřizování, můžete nové registrace tenanta co nejdříve restartovat klientů.
 * Optimalizované obnovení tenantů v pořadí podle priority.
 * Co nejdříve získat online tenantů provedením kroků paralelně, kde je to praktické optimalizovat.
 * Být odolné vůči selhání, dá se restartovat a idempotentní.
 * Až se výpadek vyřeší, repatriovat základní databáze do jejich původního oblasti s minimálním dopadem na klienty.  

> [!NOTE]
> Aplikace se obnoví do spárovaná oblast, ve kterém je aplikace nasazená. Další informace najdete v tématu [spárovaných oblastech Azure](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).   

Tento kurz používá funkce služby Azure SQL Database a platformy Azure do řeší tyto problémy:

* [Šablony Azure Resource Manageru](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-create-first-template), co nejrychleji rezervovat všechny potřebné kapacitu. Šablony Azure Resource Manageru se používají ke zřízení zrcadlový obraz původní servery a elastických fondů v oblasti obnovení. Samostatný server a fondu se vytvoří také pro zřizování nových tenantů.
* [Klientská knihovna elastic Database](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-database-client-library) (EDCL) k vytváření a údržbě databáze katalogu tenanta. Rozšířené katalogu obsahuje pravidelně aktualizovat informace o konfiguraci fondu a databáze.
* [Funkce obnovení správy horizontálních oddílů](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-database-recovery-manager) z EDCL, chcete-li zachovat databázi umístění položky v katalogu během obnovení a vrácení.  
* [Geografické obnovení](https://docs.microsoft.com/azure/sql-database/sql-database-disaster-recovery), obnovovat databáze katalogu a tenanta z automaticky udržována geograficky redundantní zálohy. 
* [Operace obnovení asynchronní](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations), odesílají v pořadí podle priority tenanta, jsou zařazená do fronty pro každý fond systému a zpracování v dávkách tak fondu není přetížen. Tyto operace může být zrušen před nebo během provádění v případě potřeby.   
* [Geografická replikace](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-overview), repatriovat základní databáze do původní oblasti po výpadek. Při použití geografické replikace není bez ztráty dat a minimální dopad na tenanta.
* [Aliasy DNS serveru SQL](https://docs.microsoft.com/azure/sql-database/dns-alias-overview), aby byl proces synchronizace katalogu pro připojení k aktivní katalogu, bez ohledu na jeho umístění.  

## <a name="get-the-disaster-recovery-scripts"></a>Získat skripty pro zotavení po havárii

Zotavení po Havárii skripty používané v tomto kurzu jsou k dispozici v [SaaS aplikace Wingtip Tickets databáze na tenanta úložiště GitHub](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant). Podívejte se [obecné pokyny](saas-tenancy-wingtip-app-guidance-tips.md) pokyny ke stažení a odblokování Wingtip Tickets skripty pro správu.

> [!IMPORTANT]
> Stejně jako všechny Wingtip Tickets správu skripty zotavení po Havárii skripty jsou kvalita ukázky a není určen k použití v produkčním prostředí.

## <a name="review-the-healthy-state-of-the-application"></a>Kontrola stavu v pořádku aplikace
Před zahájením procesu obnovení, zkontrolujte stav v pořádku normální aplikace.

1. Ve webovém prohlížeči otevřete Centrum událostí Wingtip Tickets (http://events.wingtip-dpt.&lt; uživatel&gt;. trafficmanager.net, nahraďte &lt;uživatele&gt; s hodnotou uživatele vašeho nasazení).
    
   Přejděte do dolní části stránky a Všimněte si, že název serveru katalogu a umístění v zápatí. Umístění je oblast, ve které jste nasadili aplikaci.    

   > [!TIP]
   > Najeďte myší umístění zvětšíte zobrazení.

   ![Události v pořádku stav rozbočovače v původní oblast](media/saas-dbpertenant-dr-geo-restore/events-hub-original-region.png)

2. Vyberte tenanta Hall koncertní Contoso a otevřete její stránku událostí.

   V zápatí Všimněte si, že název serveru vašeho tenanta. Umístění je stejné jako umístění pro server katalogu.

   ![Původní oblast Hall koncertní contoso](media/saas-dbpertenant-dr-geo-restore/contoso-original-location.png) 

3. V [webu Azure portal](https://portal.azure.com)zkontrolujte a otevřete skupinu prostředků, ve kterém jste aplikaci nasadili.

   Všimněte si, že prostředky a oblasti, ve které jsou nasazené součásti aplikace služby a servery SQL Database.

## <a name="sync-the-tenant-configuration-into-the-catalog"></a>Synchronizace konfigurace tenantů do katalogu

V této úloze spustí se proces synchronizovat konfiguraci serverů, elastických fondů a databází do katalogu tenanta. Tyto informace slouží později ke konfiguraci prostředí zrcadlový obraz v oblasti obnovení.

> [!IMPORTANT]
> Pro zjednodušení procesu synchronizace a dalších dlouho běžící obnovení a vrácení procesy jsou implementovány v těchto ukázkách jako místní úlohy prostředí PowerShell nebo relace, které běží v rámci vašeho klienta přihlášení uživatele. Ověřování tokenů vydaných při přihlášení vyprší po několik hodin a pak se nezdaří úlohy. V případě produkčního prostředí v dlouho běžící procesy by měla být implementována jako spolehlivé služby Azure ve výši určitého druhu spuštěný pod instančního objektu. Zobrazit [pomocí prostředí Azure PowerShell k vytvoření instančního objektu s certifikátem](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authenticate-service-principal). 

1. V prostředí PowerShell ISE otevřete soubor Modules\UserConfig.psm1 ...\Learning. Nahraďte `<resourcegroup>` a `<user>` na řádku 10 a 11 s hodnotou použitou při nasazení aplikace. Uložte soubor.

2. V prostředí PowerShell ISE otevřete ...\Learning skript Modules\Business kontinuity podnikových procesů a Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1 po havárii.

    V tomto kurzu, můžete spouštět jednotlivé scénáře v tento skript Powershellu, takže tento soubor uchovává otevřít.

3. Nastavte následující hodnotu:

    $DemoScenario = 1: spuštění úlohy na pozadí, která se synchronizuje tenanta serveru a informace o konfiguraci fondu do katalogu.

4. Pro spuštění skriptu synchronizovat, stisknutím klávesy F5. 

    Tyto informace slouží novější k zajištění, že obnovení vytvoří zrcadlový obraz serverů, fondů a databází v oblasti obnovení.  

    ![Procesu synchronizace](media/saas-dbpertenant-dr-geo-restore/sync-process.png)

Nechte okno prostředí PowerShell běžet na pozadí a pokračujte zbývající části tohoto kurzu.

> [!NOTE]
> Procesu synchronizace se připojí ke katalogu prostřednictvím DNS alias. Alias je změněna během obnovení a vrácení tak, aby odkazoval na aktivní katalogu. Procesu synchronizace udržuje katalogu aktuální s databáze nebo fondu změny konfigurace provedené v oblasti obnovení. Během navracení tyto změny se použijí na ekvivalentní prostředky v původní oblast.

## <a name="geo-restore-recovery-process-overview"></a>Přehled procesu obnovení geografické obnovení

Proces obnovení geografické obnovení nasadí aplikaci a obnovení databáze ze zálohy do oblasti pro zotavení.

Proces obnovení provede následující akce:

1. Zakáže Azure Traffic Manager koncový bod pro webovou aplikaci v původní oblast. Zakázání koncového bodu zabraňuje uživatelům v připojení k aplikaci v neplatném stavu by měla původní oblast režimu online při obnovení.

2. Zřizuje a obnovení katalogu server v oblasti pro zotavení, geografické obnovení databáze katalogu a aktualizuje activecatalog aliasu, aby odkazoval na server obnovený katalogu. Změna alias katalogu zajistí, že proces synchronizace katalogu vždy synchronizuje se katalog aktivní.

3. Označí všechny stávajících tenantů v katalogu obnovení jako v režimu offline chcete zabránit přístupu do databází tenantů, předtím, než se obnoví.

4. Zřídí instanci aplikace v oblasti obnovení a nakonfiguruje ho použití obnovené katalogu v dané oblasti. Pokud chcete zachovat čekací doba na minimum, zajišťuje ukázkovou aplikaci vždy připojí k databázi tenanta ve stejné oblasti.

5. Zřídí serveru a elastický fond, ve kterém jsou zřízení nových tenantů. Vytvoření těchto prostředků zajišťuje, že zřizování nových tenantů není v konfliktu s využitím stávajících tenantů.

6. Aktualizuje nový alias klienta tak, aby odkazoval na server pro nové databáze tenanta v oblasti obnovení. Změnou tohoto aliasu zajistí, že databáze pro všechny nové tenanty jsou zřízené v oblasti obnovení.
        
7. Zřídí servery a elastických fondů v oblasti obnovení pro obnovení databází tenantů. Tyto servery a fondy jsou zrcadlový obraz konfigurace v původní oblast. Zřizování fondů ještě před zahájením rezervuje kapacita potřebné k obnovení všech databází.

    Kvůli výpadku v oblasti může umístit významné tlak na prostředky k dispozici v párované oblasti. Pokud se spoléháte na geografické obnovení pro zotavení po Havárii, doporučujeme rychle rezervaci prostředků. Vezměte v úvahu geografickou replikaci v případě, že je velmi důležité, že aplikace je obnovena v určité oblasti. 

8. Umožňuje koncových bodů Traffic Manageru pro webovou aplikaci v oblasti obnovení. Povolení tohoto koncového bodu umožňuje, aby aplikace ke zřízení nových tenantů. V této fázi jsou stále v režimu offline tenantům.

9. Odešle dávky žádostí o obnovení databází v pořadí podle priority. 

    * Dávky jsou uspořádány tak, aby databáze obnoveny paralelně napříč všechny fondy.  

    * Obnovení žádosti je odeslán asynchronně odeslané rychle a zařadily do fronty ke spuštění v každý fond.

    * Protože obnovení žádosti se zpracovávají paralelně napříč všechny fondy, je lepší distribuovat důležité tenanty napříč mnoha fondy. 

10. Monitoruje službu SQL Database k určení, kdy obnovit databáze. Po obnovení databáze tenanta, je označena v katalogu online a je zaznamenána rowversion součet databáze tenantů. 

    * Databáze tenantů přístupný aplikace poté, co jsou označeny v katalogu online.

    * Součet hodnot rowversion databáze tenanta je uložené v katalogu. Tato částka se chová jako otisků prstů, která umožňuje procesu navracení k určení, pokud databáze byla aktualizována v oblasti obnovení.       

## <a name="run-the-recovery-script"></a>Spusťte skript pro obnovení

> [!IMPORTANT]
> V tomto kurzu obnoví databáze z geograficky redundantní zálohy. I když tyto zálohy jsou obvykle dostupné v rámci 10 minut, může trvat až hodinu. Skript se pozastaví, dokud jsou k dispozici.

Představte si, že dojde k výpadku v oblasti, ve kterém je aplikace nasazené a spusťte skript pro obnovení:

1. V prostředí PowerShell ISE v ...\Learning Modules\Business kontinuity podnikových procesů a po havárii Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1 skriptu, nastavte následující hodnotu:

    $DemoScenario = 2: obnovení z geograficky redundantní zálohy obnovit aplikaci do oblasti pro zotavení.

2. Pro spuštění skriptu, stisknutím klávesy F5.  

    * Skript se otevře v novém okně prostředí PowerShell a pak spustí sadu úloh Powershellu, které běží paralelně. Tyto úlohy obnovení serverů, fondů a databází do oblasti pro zotavení.

    * Oblast pro obnovení je spárované oblasti přidružené k oblasti Azure, ve které jste nasadili aplikaci. Další informace najdete v tématu [spárovaných oblastech Azure](https://docs.microsoft.com/azure/best-practices-availability-paired-regions). 

3. Monitorování stavu procesu obnovení v okně Powershellu.

    ![Proces obnovení](media/saas-dbpertenant-dr-geo-restore/dr-in-progress.png)

> [!NOTE]
> Prozkoumejte kód pro úlohy obnovení, najdete v tématu Powershellových skriptů v složce ...\Learning Modules\Business kontinuity podnikových procesů a Recovery\DR-RestoreFromBackup\RecoveryJobs po havárii.

## <a name="review-the-application-state-during-recovery"></a>Zkontrolujte stav aplikace během obnovení
Zatímco koncový bod aplikace je v Traffic Manageru zakázán, aplikace je k dispozici. Obnovení katalogu a jsou označeny za všechny tenanty v režimu offline. Koncový bod aplikace v oblasti obnovení pak zapnutý a aplikace je zpátky do online režimu. I když je aplikace dostupná, tenantů zobrazovat jako offline v Centru událostí až se obnoví jejich databáze. Je důležité při návrhu vaší aplikace pro zpracování databází tenantů v režimu offline.

* Po databáze katalogu byla obnovena, ale předtím, než klienti se zpátky do online režimu, aktualizujte Centrum akcí Wingtip Tickets ve webovém prohlížeči.

    * V zápatí, Všimněte si, že název serveru katalogu teď má příponu – obnovení a nachází se v oblasti obnovení.

    * Všimněte si, že klienty, kteří se ještě neobnoví jsou označeny jako offline a se nedá vybrat.   
 
    ![Proces obnovení](media/saas-dbpertenant-dr-geo-restore/events-hub-tenants-offline-in-recovery-region.png)    

    * Pokud otevřete stránku události klienta přímo tenanta je offline, na stránce se zobrazí oznámení offline tenanta. Například pokud Hall koncertní Contoso je v režimu offline, zkuste otevřít http://events.wingtip-dpt.&lt; uživatel&gt;.trafficmanager.net/contosoconcerthall.

    ![Proces obnovení](media/saas-dbpertenant-dr-geo-restore/dr-in-progress-offline-contosoconcerthall.png)

## <a name="provision-a-new-tenant-in-the-recovery-region"></a>Zřízení nového tenanta v oblasti obnovení
Ještě předtím, než se obnoví databází tenantů, můžete zřídit nové tenanty v oblasti obnovení. Nová databáze tenantů zřízené v oblasti obnovení jsou repatriated s obnovenou databází později.   

1. V prostředí PowerShell ISE v ...\Learning Modules\Business kontinuity podnikových procesů a po havárii Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1 skriptu, nastavte následující vlastnost:

    $DemoScenario = 3: zřízení nového tenanta v oblasti obnovení.

2. Pro spuštění skriptu, stisknutím klávesy F5.

3. Při zřizování dokončí, otevře se stránka událostí Hawthorn vedení se zaměstnanci v prohlížeči. 

    Všimněte si, že Hawthorn Hall databáze se nachází v oblasti obnovení.

    ![Hawthorn Hall zřízené v oblasti obnovení](media/saas-dbpertenant-dr-geo-restore/hawthorn-hall-provisioned-in-recovery-region.png)

4. V prohlížeči aktualizujte stránku centra událostí Wingtip Tickets zobrazíte Hawthorn Hall zahrnuty. 

    I když jste zřídili Hawthorn Hall bez čekání na jiných tenantů, obnovení, může být offline jiných tenantů.

## <a name="review-the-recovered-state-of-the-application"></a>Zkontrolujte obnovený stav aplikace

Po dokončení procesu obnovení, aplikace a všech tenantů jsou plně funkční v oblasti obnovení. 

1. Po zobrazení v okně konzoly Powershellu označuje, že se obnoví všech tenantů, aktualizujte Centrum akcí. 

    Online, včetně nového tenanta organizace Hawthorn se zobrazí všechny klienty.

    ![obnovená a nové tenanty v Centru událostí](media/saas-dbpertenant-dr-geo-restore/events-hub-with-hawthorn-hall.png)

2. Klikněte na vzájemné součinnosti organizace Contoso a otevřete její stránku události. 

    V zápatí Všimněte si, že je databáze umístěna na serveru pro obnovení nachází v oblasti obnovení.

    ![Contoso v oblasti obnovení](media/saas-dbpertenant-dr-geo-restore/contoso-recovery-location.png)

3. V [webu Azure portal](https://portal.azure.com), otevřete seznam skupin prostředků.  

    Všimněte si, že skupiny prostředků, kterou jste nasadili, a skupina prostředků pro obnovení, s příponou - obnovení. Skupina prostředků pro obnovení obsahuje všechny prostředky vytvořené během procesu obnovení, plus nových prostředků vytvořených během výpadku. 

4. Otevřete skupinu prostředků, obnovení a Všimněte si, že následující položky:

    * Verze obnovení serverů katalogu a tenants1 s příponou - obnovení. Obnovené databáze katalogu a tenanta na těchto serverech všechny mají názvy používanými v původní oblast.

    * Tenants2-dpt -&lt;uživatele&gt;– obnovení SQL serveru. Tento server slouží ke zřizování nových tenantů během výpadku.

    * Služby app service s názvem události-wingtip-dpt -&lt;recoveryregion&gt;-&lt;uživatele&gt;, což je instance obnovení události aplikace.

    ![Contoso prostředky v oblasti obnovení](media/saas-dbpertenant-dr-geo-restore/resources-in-recovery-region.png) 
    
5. Otevřete tenants2-dpt -&lt;uživatele&gt;– obnovení SQL serveru. Všimněte si, že obsahuje hawthornhall databáze a elastického fondu Pool1. Hawthornhall databáze je nakonfigurovaný jako elastické databáze v elastickém fondu Pool1.

## <a name="change-the-tenant-data"></a>Změnit data tenanta 
V této úloze můžete aktualizovat některou z obnovené tenantských databázích. Tyto kopie procesu navrácení obnovit databáze, které se změnily a původní oblast. 

1. V prohlížeči najít seznamu událostí pro vzájemné součinnosti Hall Contoso, procházet události a Všimněte si, že poslední události, vážně Strauss.

2. V prostředí PowerShell ISE v ...\Learning Modules\Business kontinuity podnikových procesů a po havárii Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1 skriptu, nastavte následující hodnotu:

    $DemoScenario = 4: odstranění události z tenanta v oblasti obnovení.

3. Spustit skript, stisknutím klávesy F5.

4. Aktualizujte stránku události Hall koncertní Contoso (http://events.wingtip-dpt.&lt; uživatel&gt;.trafficmanager.net/contosoconcerthall) a Všimněte si, že události vážně Strauss chybí.

V tuto chvíli v tomto kurzu jste obnovili aplikace, která je nyní spuštěna v oblasti obnovení. Zřízení nového tenanta v oblasti obnovení a upravená data jednoho z obnovené tenantů.  

> [!NOTE]
> Další kurzy v ukázce nejsou určeny pro provoz aplikace ve stavu obnovení. Pokud chcete prozkoumat další kurzy, ujistěte se, že jste nejdřív repatriovat základní aplikace.

## <a name="repatriation-process-overview"></a>Přehled procesu navracení

Proces vrácení vrátí aplikace a jeho databázím na jeho původní oblast, až se výpadek vyřeší.

![Geografické obnovení navracení](media/saas-dbpertenant-dr-geo-restore/geo-restore-repatriation.png) 

Proces:

1. Zastaví jakékoliv probíhající obnovení činnosti a zruší všechny žádosti o obnovení databáze nevyřízené nebo vydávaných za pochodu.

2. Znovu aktivuje v původní databáze tenantů oblasti, které nebyly změněny od výpadek. Tyto databáze obsahují těmi zatím ne obnovit a těmi obnovit, ale nebyl změněn. později. Opětovně databáze se stejným způsobem jako poslední přístup svým klientům.

3. Zřizuje nového tenanta serveru a elastického fondu v oblasti původní zrcadlový obraz. Po dokončení této akce se nový alias tenanta se aktualizuje tak, aby odkazoval na tomto serveru. Aktualizace aliasu pro způsobí, že nové registrace tenanta vyskytuje v oblasti původní místo oblast pro obnovení.

3. Geografická replikace se používá k přesunutí katalogu původní oblastí z oblasti pro zotavení.

4. Aktualizace konfigurace fondu v původní oblasti tak, aby byl konzistentní vzhledem k aplikacím, které byly provedeny v oblasti obnovení během výpadku.

5. Vytvoří požadované servery a fondy, které chcete hostovat žádné nové databáze vytvořené během výpadku.

6. Používá geografickou replikaci, která repatriovat základní obnovit tenanta, který po obnovení databáze, které byly aktualizovány a všechny nové databáze tenantů zřídit během výpadku. 

7. Vyčistí prostředky vytvořené v oblasti obnovení během procesu obnovení.

Pokud chcete omezit počet databází tenantů, které je potřeba mít repatriated, se kroky 1 až 3 provádějí okamžitě.  

Krok 4 se provádí, pouze pokud katalogu v oblasti obnovení byla změněna během výpadku. Katalog se aktualizuje, pokud jsou vytvořeny nové tenanty, nebo pokud žádnou konfiguraci databáze nebo fondu se změnilo v oblasti obnovení.

Je důležité, že krok 7 způsobí, že minimálním dopadem na klienty a se neztratila žádná data. K dosažení tohoto cíle, proces používá geografickou replikaci.

Předtím, než je každá databáze geograficky replikované, odpovídající databáze v původní oblasti se odstraní. Databáze v oblasti obnovení je geograficky replikované, vytváří se sekundární repliky v původní oblast. Po dokončení replikace tenanta je označena jako offline v katalogu, který přeruší všechna připojení k databázi v oblasti obnovení. Databáze je pak převzetí služeb při selhání, způsobí všechny čekající transakce ke zpracování na sekundárním proto žádná data se ztratí. 

Na převzetí služeb při selhání databázové role jsou vrátit zpět. Sekundární v oblasti původní stane primární databáze pro čtení i zápis a databáze v oblasti obnovení bude sekundární jen pro čtení. Položka tenanta v katalogu je aktualizován, aby na databázi v původní oblasti a tenanta je označen online. Vrácení databáze v tomto okamžiku je dokončena. 

Aplikace by měla být zapsaný s logikou opakování Ujistěte se, že se automaticky znovu připojit po připojení je přerušené. Při použití katalogu pro zprostředkování opětovné připojení, připojí se ke repatriated databáze v původní oblast. I když stručný odpojení je často není si všimli, můžete zvolit repatriovat základní databáze mimo pracovní dobu.

Poté, co je repatriated databázi, je možné odstranit sekundární databáze v oblasti obnovení. Databáze v oblasti původní potom využívá znovu geografické obnovení pro ochranu zotavení po Havárii.

V kroku 8 se odstraní prostředky v oblasti pro zotavení, včetně obnovení serverů a fondů.

## <a name="run-the-repatriation-script"></a>Spusťte skript vrácení
Představte si je výpadek vyřešen a spusťte skript pro vrácení.

Pokud jste postupovali podle tohoto kurzu, skript okamžitě znovu aktivuje Fabrikam Jazz Club a svída Dojo v původní oblasti, protože jsou beze změny. Potom repatriates nového tenanta, Hawthorn Hall a Contoso koncertní Hall, protože byla změněna. Skript také repatriates katalog, který byl aktualizován při zřizování Hawthorn vedení se zaměstnanci.
  
1. V prostředí PowerShell ISE v ...\Learning skript Modules\Business kontinuity podnikových procesů a Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1 po havárii ověřte, že se proces synchronizace katalogu běží pořád v jeho instance prostředí PowerShell. V případě potřeby restartujte nastavením:

    $DemoScenario = 1: zahájení synchronizace serveru tenanta, fondu a informace o konfiguraci databáze do katalogu.

    Pro spuštění skriptu, stisknutím klávesy F5.

2.  Pokud chcete spustit proces navracení, nastavte:

    $DemoScenario = 5: repatriovat základní aplikaci do jeho původního oblasti.

    Ke spuštění skriptu pro obnovení v novém okně prostředí PowerShell, stisknutím klávesy F5. Navracení trvá několik minut a můžete ji sledovat v okně Powershellu.

3. Když je spuštěný skript, aktualizujte stránku centra událostí (http://events.wingtip-dpt.&lt; uživatel&gt;. trafficmanager.net).

    Všimněte si, že jsou všechny tenanty online a dostupné během tohoto procesu.

4. Vyberte Fabrikam Jazz Club ho otevřete. Pokud upravíte neměli tohoto tenanta, Všimněte si, že v zápatí, že server je už vrátit zpět na původní server.

5. Otevřete nebo aktualizujte stránku události Hall koncertní Contoso. Všimněte si, že v zápatí, že na začátku databázi je uložena na serveru – pro obnovení. 

6. Aktualizujte stránku Contoso koncertní Hall události po dokončení procesu vrácení a Všimněte si, že databáze je nyní váš původním oblasti.

7. Znovu aktualizujte Centrum akcí a otevřete Hawthorn vedení se zaměstnanci. Všimněte si, že databázi je také umístěná na původní oblast. 

## <a name="clean-up-recovery-region-resources-after-repatriation"></a>Vyčištění prostředků oblast pro obnovení po vrácení
Po dokončení navrácení jde bezpečně odstranit prostředky v oblasti obnovení. 

> [!IMPORTANT]
> Odstraňte tyto prostředky okamžitě, abyste zastavili veškeré účtování pro ně.

Proces obnovení vytvoří recovery prostředky ve skupině prostředků pro obnovení. Proces vyčištění odstraní tuto skupinu prostředků a odebere všechny odkazy na prostředky z katalogu. 

1. V prostředí PowerShell ISE ...\Learning skriptu Modules\Business kontinuity podnikových procesů a Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1 po havárii nastavte:
    
    $DemoScenario = 6: odstranění zastaralých prostředky z oblasti pro zotavení.

2. Pro spuštění skriptu, stisknutím klávesy F5.

Po vyčištění skriptů, aplikace je zpět ve kterém byl spuštěn. V tomto okamžiku můžete znovu spusťte skript nebo vyzkoušet ostatní kurzy.

## <a name="designing-the-application-to-ensure-that-the-app-and-the-database-are-co-located"></a>Návrh aplikace k zajištění, že aplikace a databáze jsou umístěné 
Aplikace je navržena pro vždy připojení z instance ve stejné oblasti jako databáze tenanta. Tento návrh snižuje latence mezi aplikace a databáze. Tato optimalizace se předpokládá, že interakce aplikace k databázi je chattier než interakce aplikace uživatele.  

Databáze tenanta můžou být rozložená v obnovení a původní oblasti nechystáte nějakou dobu, během navracení. Pro každou databázi aplikace vyhledá oblast, ve kterém je databáze umístěna tímto způsobem vyhledávání DNS na název serveru tenanta. Ve službě SQL Database je název serveru alias. Název serveru alias obsahuje název oblasti. Pokud aplikace není ve stejné oblasti jako databáze, přesměruje do instance ve stejné oblasti jako databázového serveru. Přesměrování instance ve stejné oblasti jako databáze minimalizuje latenci mezi aplikace a databáze.  

## <a name="next-steps"></a>Další postup

V tomto kurzu jste se naučili:
> [!div class="checklist"]

>* Použití katalogu tenanta pro uložení pravidelně aktualizovat konfigurační informace, které umožňuje obnovení prostředí zrcadlový obraz má být vytvořen v jiné oblasti.
>* Obnovení databází Azure SQL s využitím geografického obnovení do oblasti pro zotavení.
>* Aktualizujte katalog klienta tak, aby odrážely umístění tenanta obnovené databáze. 
>* Umožňuje aplikaci připojovat ke katalogu tenanta v celém bez změny konfigurace pomocí DNS alias.
>* Použití geografické replikace repatriovat základní obnovené databáze do jejich původního oblasti po výpadku je vyřešený.

Zkuste [zotavení po havárii pro víceklientské aplikace SaaS s využitím geografické replikace databáze](saas-dbpertenant-dr-geo-replication.md) kurzu se dozvíte, jak pomocí georeplikace výrazně zkracuje dobu potřebnou k obnovení víceklientské aplikace ve velkém měřítku.

## <a name="additional-resources"></a>Další zdroje informací:

[Další kurzy, které vycházejí z aplikace SaaS aplikace Wingtip](https://docs.microsoft.com/azure/sql-database/sql-database-wtp-overview#sql-database-wingtip-saas-tutorials)
