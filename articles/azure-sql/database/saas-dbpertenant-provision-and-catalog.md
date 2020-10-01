---
title: Zřizování nových tenantů ve víceklientské aplikaci
description: Naučte se zřizovat a zařadit nové klienty do Azure SQL Database aplikace SaaS víceklientské architektury.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 09/24/2018
ms.openlocfilehash: bc649551986190f944e3225ff0914d091acd3f88
ms.sourcegitcommit: 4bebbf664e69361f13cfe83020b2e87ed4dc8fa2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/01/2020
ms.locfileid: "91619691"
---
# <a name="learn-how-to-provision-new-tenants-and-register-them-in-the-catalog"></a>Naučte se zřizovat nové klienty a registrovat je v katalogu.
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

V tomto kurzu se naučíte zřídit a zařadit vzory SaaS do katalogu. Naučíte se také, jak jsou implementovány v aplikaci Wingtip Tickets SaaS Database-per-tenant. Vytváříte a inicializujete nové databáze tenantů a zaregistrujete je v katalogu tenanta aplikace. Katalog je databáze, která uchovává mapování mezi klienty aplikace SaaS a jejich daty. Katalog hraje důležitou roli při směrování požadavků na aplikace a správu do správné databáze.

V tomto kurzu se naučíte:

> [!div class="checklist"]
>
> * Zřídit jednoho nového tenanta.
> * Zřídit dávku dalších tenantů.


Předpokladem dokončení tohoto kurzu je splnění následujících požadavků:

* Nasadí se aplikace Wingtip Tickets SaaS Database-na tenanta. Pokud ho chcete nasadit za méně než pět minut, přečtěte si téma [nasazení a prozkoumání aplikace Wingtip Tickets SaaS Database-per-tenant](../../sql-database/saas-dbpertenant-get-started-deploy.md).
* Je nainstalované prostředí Azure PowerShell. Další informace najdete v tématu [Začínáme s Azure PowerShellem](https://docs.microsoft.com/powershell/azure/get-started-azureps).

## <a name="introduction-to-the-saas-catalog-pattern"></a>Seznámení se vzorem katalogu SaaS

V aplikaci SaaS s více klienty je důležité znát, kde jsou uložené informace pro každého tenanta. Ve vzorovém katalogu SaaS se databáze katalogu používá k uchování mapování mezi jednotlivými klienty a databází, ve které jsou data uložená. Tento model se vztahuje vždy, když jsou data tenanta distribuována napříč více databázemi.

Každý tenant je identifikovaný klíčem v katalogu, který je namapován na umístění své databáze. V aplikaci Wingtip Tickets se klíč vytvoří z hodnoty hash názvu tenanta. Toto schéma umožňuje aplikaci vytvořit klíč z názvu tenanta, který je součástí adresy URL aplikace. Můžete použít další schémata klíčů tenanta.

Katalog umožňuje změnit název nebo umístění databáze s minimálním dopadem na aplikaci. Ve víceklientském databázovém modelu se tato možnost taky vejde na přesun klienta mezi databázemi. Katalog je také možné použít k označení toho, zda je klient nebo databáze v režimu offline kvůli údržbě nebo jiným akcím. Tato funkce se prozkoumá v [kurzu obnovení jednoho tenanta](saas-dbpertenant-restore-single-tenant.md).

Katalog taky může ukládat další metadata tenanta nebo databáze, jako je například verze schématu, plán služby nebo SLA nabízená klientům. Katalog může obsahovat další informace, které umožňují správu aplikací, zákaznickou podporu nebo DevOps.

Kromě aplikace SaaS může katalog povolit databázové nástroje. V ukázce SaaS datatickets pro každý tenant se v katalogu používá k povolení dotazu pro více tenantů, který se zkoumá v [kurzu generování sestav ad hoc](saas-tenancy-cross-tenant-reporting.md). Správa úloh mezi databázemi je prozkoumání v kurzech [pro správu schématu](saas-tenancy-schema-management.md) a [tenanta Analytics](saas-tenancy-tenant-analytics.md) .

V ukázkách Wingtip Tickets SaaS se katalog implementuje pomocí funkcí správy horizontálních oddílů v [klientské knihovně nástroje elastic Database (EDCL)](elastic-database-client-library.md). EDCL je k dispozici v jazyce Java a .NET Framework. EDCL umožňuje aplikaci vytvářet, spravovat a používat mapu horizontálních oddílů zálohovanou databází.

Mapa horizontálních oddílů obsahuje seznam horizontálních oddílů (databází) a mapování mezi klíči (klienty) a horizontálních oddílů. Funkce EDCL se používají během zřizování tenanta k vytvoření položek v mapě horizontálních oddílů. Používají se v době běhu v aplikacích pro připojení ke správné databázi. EDCL ukládá informace o připojení do mezipaměti pro minimalizaci provozu do databáze katalogu a urychlení aplikace.

> [!IMPORTANT]
> Data mapování jsou přístupná v databázi katalogu, ale *neupravujete je*. Upraví data mapování jenom pomocí Elastic Database klientské knihovny API. Přímá manipulace s riziky mapování dat, která poškozují katalog a nejsou podporovaná.


## <a name="introduction-to-the-saas-provisioning-pattern"></a>Úvod do vzoru zřizování SaaS

Když přidáte nového tenanta do aplikace SaaS, která používá databázový model s jedním klientem, musíte zřídit novou databázi tenanta. Databáze musí být vytvořena v příslušném umístění a vrstvě služby. Musí se taky inicializovat pomocí příslušného schématu a referenčních dat. A musí být zaregistrované v katalogu pod příslušným klíčem tenanta.

Můžete použít různé přístupy k zřizování databáze. Můžete spouštět skripty SQL, nasazovat BacPac nebo kopírovat šablonu databáze.

Zřizování databáze musí být součástí strategie správy schématu. Je potřeba zajistit, aby se nové databáze zřídily s nejnovějším schématem. Tento požadavek se prozkoumá v [kurzu správy schématu](saas-tenancy-schema-management.md).

Aplikace Wingtip Tickets Database – pro každého tenanta zřídí nové klienty zkopírováním databáze šablony s názvem _basetenantdb_, která je nasazena na serveru katalogu. Zřizování se dá do aplikace integrovat jako součást prostředí pro registraci. Můžete ho taky podporovat offline pomocí skriptů. V tomto kurzu se seznámíte s zřizováním pomocí prostředí PowerShell.

Skripty pro zřizování kopírují databázi _basetenantdb_ , aby se vytvořila nová databáze tenanta v elastickém fondu. Databáze tenanta se vytvoří na serveru tenanta namapovaném na alias DNS _newtenant_ . Tento alias udržuje odkaz na server, který se používá ke zřízení nových tenantů a aktualizuje se tak, aby odkazoval na server tenanta pro obnovení v kurzech pro zotavení po havárii ([Dr pomocí Restore](../../sql-database/saas-dbpertenant-dr-geo-restore.md), [Dr pomocí inreplikace](../../sql-database/saas-dbpertenant-dr-geo-replication.md)). Skripty pak inicializují databázi s informacemi, které jsou specifické pro tenanta, a zaregistrují je v mapě horizontálních oddílů katalogu. K databázím tenanta se přidávají názvy na základě názvu tenanta. Toto schéma pojmenovávání není důležitou součástí vzoru. Katalog mapuje klíč tenanta na název databáze, takže je možné použít jakékoli zásady vytváření názvů.


## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Získání skriptů aplikace Wingtip Tickets SaaS Database – pro klienty

V úložišti GitHubu [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) jsou k dispozici skripty a zdrojový kód aplikace Wingtip Tickets SaaS. Projděte si [Obecné pokyny](saas-tenancy-wingtip-app-guidance-tips.md) ke stažení a odblokování skriptů Wingtip Tickets SaaS.


## <a name="provision-and-catalog-detailed-walkthrough"></a>Podrobný postup zřizování a přidání tenantů do katalogu

Aby bylo možné pochopit, jak aplikace Wingtip Tickets implementuje nové zřizování tenanta, přidejte zarážku a Sledujte pracovní postup při zřizování tenanta.

1. V prostředí PowerShell ISE otevřete... \\ Výukové moduly \\ ProvisionAndCatalog \\ _Demo-ProvisionAndCatalog.ps1_ a nastavte následující parametry:

   * **$TenantName** = název nového místa (například *Bushwillow Blues*).
   * **$VenueType** = jeden z předdefinovaných typů místa: _blues, ClassicalMusic, roztancoval, jazz, Judo, motorové dostihy, Multipurpose, Opera, rockmusic, fotbal_.
   * **$DemoScenario**  =  **1** *zřízení jednoho tenanta*.

2. Chcete-li přidat zarážku, umístěte kurzor na libovolné místo na řádku, který říká *New-tenant*. Pak stiskněte F9.

   ![Snímek obrazovky se zvýrazněním nového tenanta, který je zvýrazněný pro přidání zarážky.](./media/saas-dbpertenant-provision-and-catalog/breakpoint.png)

3. Pokud chcete skript spustit, stiskněte klávesu F5.

4. Po zastavení spuštění skriptu na zarážce stiskněte klávesu F11 ke kroku do kódu.

   ![Ladění](./media/saas-dbpertenant-provision-and-catalog/debug.png)



Trasovat provádění skriptu pomocí možností nabídky **ladění** . Stisknutím klávesy F10 a F11 přejděte mezi nebo do volaných funkcí. Další informace o ladění skriptů PowerShellu najdete v tématu [o práci se skripty PowerShellu a jejich ladění](https://docs.microsoft.com/powershell/scripting/components/ise/how-to-debug-scripts-in-windows-powershell-ise).


Tento pracovní postup nemusíte explicitně sledovat. Vysvětluje, jak skript ladit.

* **Importujte modul CatalogAndDatabaseManagement. psm1.** Poskytuje katalog a abstrakci na úrovni tenanta v rámci funkcí [správy horizontálních oddílů](elastic-scale-shard-map-management.md) . Tento modul zapouzdřuje většinu vzorů katalogu a je prozkoumávat.
* **Importujte modul SubscriptionManagement. psm1.** Obsahuje funkce pro přihlášení k Azure a výběr předplatného Azure, se kterým chcete pracovat.
* **Získat podrobnosti o konfiguraci.** Krok do získání konfigurace pomocí klávesy F11 a podívejte se, jak je zadaná konfigurace aplikace. Tady jsou definované názvy prostředků a další hodnoty specifické pro danou aplikaci. Tyto hodnoty neměňte, dokud nebudete znát skripty.
* **Získá objekt katalogu.** Krok do příkazového katalogu Get-Catalog, který vytváří a vrací objekt katalogu, který se používá ve skriptu vyšší úrovně. Tato funkce používá funkce správy horizontálních oddílů, které jsou importovány z **AzureShardManagement. psm1**. Objekt Catalog se skládá z následujících prvků:

   * $catalogServerFullyQualifiedName je vytvořen pomocí standardního kmene plus vaše uživatelské jméno: _Catalog- \<user\> . Database. Windows .NET_.
   * $catalogDatabaseName se načte z této konfigurace: *tenantcatalog*.
   * Objekt $shardMapManager se inicializuje z databáze katalogu.
   * Objekt $shardMap se inicializuje z mapy horizontálního dělení _tenantcatalog_ v databázi katalogu. Objekt katalogu se skládá a vrátí. Používá se ve skriptu vyšší úrovně.
* **Vypočítejte nový klíč tenanta.** K vytvoření klíče tenanta z jeho jména se používá funkce hash.
* **Ověřte, zda klíč tenanta již existuje.** V katalogu se ověří, že je klíč k dispozici.
* **Ke zřízení databáze tenanta se použije New-TenantDatabase.** Použijte klávesu F11 ke krokování toho, jak se databáze zřídí pomocí [šablony Azure Resource Manager](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md).

    Název databáze je sestavený z názvu tenanta, aby bylo jasné, jakému tenantovi příslušný horizontální oddíl patří. Můžete také použít jiné zásady vytváření názvů databází. Šablona Správce prostředků vytvoří databázi tenanta zkopírováním databáze šablony (_baseTenantDB_) na serveru katalogu. Jako alternativu můžete vytvořit databázi a inicializovat ji pomocí importu BacPac. Případně můžete spustit inicializační skript z dobře známého umístění.

    Šablona Správce prostředků je ve složce. ..\Learning Modules\Common\: *tenantdatabasecopytemplate.jsna*

* **Databáze tenanta je dále inicializovaná.** Přidají se název místa (tenant) a typ místa konání. Můžete také provést další inicializaci.

* **Databáze tenanta je registrovaná v katalogu.** Zaregistruje se v *Add-TenantDatabaseToCatalog* pomocí klíče tenanta. Ke vstupu do podrobností použijte funkční klávesu F11.

    * Databáze katalogu se přidá do mapy horizontálního dělení (tj. do seznamu známých databází).
    * Vytvoří se mapování, které propojí hodnotu klíče s horizontálním oddílem.
    * Další metadata o klientovi (název místa) se přidají do tabulky tenantů v katalogu. Tabulka tenantů není součástí schématu správy horizontálních oddílů a není nainstalovaná EDCL. Tato tabulka ukazuje, jak lze databázi katalogu rozšířit tak, aby podporovala další data specifická pro danou aplikaci.


Po dokončení zřizování se spuštění vrátí do původního skriptu *demo-ProvisionAndCatalog* . Otevře se stránka **události** pro nového tenanta v prohlížeči.

   ![Stránka události](./media/saas-dbpertenant-provision-and-catalog/new-tenant.png)


## <a name="provision-a-batch-of-tenants"></a>Zřízení dávky tenantů

Toto cvičení zřídí dávku o 17 klientech. Doporučujeme, abyste tuto dávku tenantů zřídili před spuštěním dalších kurzů Wingtip Tickets SaaS Database-per-tenant. Existuje více než jen několik databází, se kterými pracujete.

1. V prostředí PowerShell ISE otevřete... \\ Výukové moduly \\ ProvisionAndCatalog \\ *Demo-ProvisionAndCatalog.ps1*. Změňte parametr *$DemoScenario* na 3:

   * **$DemoScenario**  =  **3** *zřiďte dávku tenantů*.
2. Pokud chcete skript spustit, stiskněte klávesu F5.

Skript nasadí dávku dalších tenantů. Používá [šablonu Azure Resource Manager](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md) , která řídí dávku a deleguje zřizování každé databáze s propojenou šablonou. Tento způsob použití šablon umožňuje Azure Resource Manageru zprostředkovat skriptu proces zřizování. Šablony zřídí v případě potřeby databáze paralelně a pokusy o opakované pořizování. Skript je idempotentní, takže pokud z nějakého důvodu dojde k chybě nebo jeho zastavení, spusťte ho znovu.

### <a name="verify-the-batch-of-tenants-that-successfully-deployed"></a>Ověření dávky klientů, které se úspěšně nasadily

* V [Azure Portal](https://portal.azure.com)přejděte na seznam serverů a otevřete Server *tenants1* . Vyberte **databáze SQL**a ověřte, že je v seznamu teď v dávce 17 dalších databází.

   ![Seznam databází](./media/saas-dbpertenant-provision-and-catalog/database-list.png)



## <a name="other-provisioning-patterns"></a>Další způsoby zřizování

Další vzory zřizování nezahrnuté v tomto kurzu:

**Předběžné zřizování databází**: vzor předběžného zřizování zneužije skutečnost, že databáze v elastickém fondu nezahrnují dodatečné náklady. Fakturuje se pro elastický fond, ne pro databáze. Nečinné databáze nevyužívají žádné prostředky. Po předběžném zřizování databází ve fondu a jejich přidělování v případě potřeby můžete zkrátit dobu přidávání klientů. Počet databází, které jsou předem zřízené, se dá upravit podle potřeby, aby se zajistila vyrovnávací paměť vhodná pro předpokládanou rychlost zřizování.

**Automatické zřizování**: ve vzorech automatického zřizování zřídí služba automaticky servery, fondy a databáze podle potřeby. Pokud chcete, můžete zahrnout předběžné zřizování databází v elastických fondech. Pokud jsou databáze vyřazeny z provozu a odstraněny, může služba zřizování vyplňovat mezery v elastických fondech. Taková služba může být jednoduchá nebo složitá, jako je například manipulace s zřizováním napříč několika geografickými oblastmi a nastavení geografické replikace pro zotavení po havárii.

Pomocí vzoru automatického zřizování odešle klientská aplikace nebo skript požadavek na zřízení do fronty, kterou má zpracovat služba zřizování. Pak se dotáže služby na určení dokončení. Je-li použito předběžné zřizování, požadavky jsou zpracovávány rychle. Služba zřídí náhradní databázi na pozadí.


## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
>
> * Zřídit jednoho nového tenanta.
> * Zřídit dávku dalších tenantů.
> * Projděte si podrobnosti o zřizování klientů a jejich registraci do katalogu.

Vyzkoušejte si [kurz k monitorování výkonu](../../sql-database/saas-dbpertenant-performance-monitoring.md).

## <a name="additional-resources"></a>Další zdroje informací

* Další [kurzy, které se vytvářejí na základě aplikace Wingtip Tickets SaaS Database-per-tenant](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Klientská knihovna Elastic Database](elastic-database-client-library.md)
* [Ladit skripty v Integrované skriptovací prostředí (ISE) v prostředí Windows PowerShell](https://docs.microsoft.com/powershell/scripting/components/ise/how-to-debug-scripts-in-windows-powershell-ise)
