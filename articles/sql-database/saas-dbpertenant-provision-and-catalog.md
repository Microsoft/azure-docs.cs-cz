---
title: Zřízení nových klientů ve víceklientské aplikaci
description: Zjistěte, jak zřídit a katalogizovat nové klienty v víceklientské aplikaci SaaS azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 09/24/2018
ms.openlocfilehash: 6ec8f8835e925663fc6ac21a6eb1df09d6927109
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74132107"
---
# <a name="learn-how-to-provision-new-tenants-and-register-them-in-the-catalog"></a>Naučte se zřídit nové klienty a zaregistrovat je v katalogu

V tomto kurzu se dozvíte, jak zřídit a katalogizovat vzory SaaS. Také se dozvíte, jak jsou implementovány v wingtip tickets saas databáze na klienta aplikace. Vytvořit a inicializovat nové databáze klientů a zaregistrovat je v katalogu klienta aplikace. Katalog je databáze, která udržuje mapování mezi mnoha klienty aplikace SaaS a jejich daty. Katalog hraje důležitou roli při směrování žádostí o aplikace a požadavky na správu do správné databáze.

V tomto kurzu se naučíte:

> [!div class="checklist"]
>
> * Zřízení jednoho nového klienta.
> * Zřízení dávky dalších klientů.


Předpokladem dokončení tohoto kurzu je splnění následujících požadavků:

* Wingtip Tickets SaaS databáze na tenanta aplikace se nasadí. Informace o nasazení za méně než pět minut najdete v [tématu Nasazení a prozkoumání aplikace Wingtip Tickets SaaS pro každý klient .](saas-dbpertenant-get-started-deploy.md)
* Je nainstalované prostředí Azure PowerShell. Další informace najdete v tématu [Začínáme s Azure PowerShellem](https://docs.microsoft.com/powershell/azure/get-started-azureps).

## <a name="introduction-to-the-saas-catalog-pattern"></a>Úvod do vzoru katalogu SaaS

V databázi couval víceklientské aplikace SaaS, je důležité vědět, kde jsou uloženy informace pro každého klienta. Ve vzoru katalogu SaaS databáze katalogu se používá k uložení mapování mezi každý klient a databáze, ve kterém jsou uložena jejich data. Tento vzor platí vždy, když jsou data klienta distribuována ve více databázích.

Každý klient je identifikován klíčem v katalogu, který je mapován na umístění jejich databáze. V aplikaci Wingtip Tickets je klíč vytvořen z hash názvu klienta. Toto schéma umožňuje aplikaci vytvořit klíč z názvu klienta zahrnuté v adrese URL aplikace. Lze použít další schémata klíčů klienta.

Katalog umožňuje změnit název nebo umístění databáze s minimálním dopadem na aplikaci. V modelu více klientské databáze tato funkce také umožňuje přesunutí klienta mezi databázemi. Katalog lze také použít k označení, zda klient nebo databáze je offline pro údržbu nebo jiné akce. Tato funkce je prozkoumána v [kurzu obnovení jednoho klienta](saas-dbpertenant-restore-single-tenant.md).

Katalog také můžete uložit další metadata klienta nebo databáze, jako je například verze schématu, plán služeb nebo SLA nabízené klientům. Katalog může ukládat další informace, které umožňují správu aplikací, zákaznickou podporu nebo DevOps.

Kromě aplikace SaaS katalog umožne databázové nástroje. V wingtip tickets saas databáze na tenanta ukázka katalogu se používá k povolení dotazu mezi klienty, který je prozkoumán v [kurzu vykazování Ad hoc](saas-tenancy-cross-tenant-reporting.md). Správa úloh mezi databázemi je prozkoumána v kurzech [správy schématu](saas-tenancy-schema-management.md) a [analýzy tenanta.](saas-tenancy-tenant-analytics.md)

Ve vzorcích Wingtip Tickets SaaS je katalog implementován pomocí funkcí správy střepu [klientské knihovny elastické databáze (EDCL).](sql-database-elastic-database-client-library.md) Edcl je k dispozici v jazyce Java a rozhraní .NET Framework. Edcl umožňuje aplikaci vytvářet, spravovat a používat mapování oddílů zálohy databáze.

Mapa štřepů obsahuje seznam úlomků (databází) a mapování mezi klíči (tenanty) a štrůdly. Edcl funkce se používají při zřizování klienta k vytvoření položek v mapě střepů. Používají je aplikace za běhu pro připojení ke správné databázi. Edcl ukládá do mezipaměti informace o připojení minimalizovat provoz do databáze katalogu a urychlit aplikaci.

> [!IMPORTANT]
> Data mapování jsou přístupná v databázi katalogu, ale *neupravujte je*. Upravte data mapování pouze pomocí api klientské knihovny elastické databáze. Přímé manipulace s daty mapování hrozí poškození katalogu a není podporován.


## <a name="introduction-to-the-saas-provisioning-pattern"></a>Úvod do vzoru zřizování SaaS

Když přidáte nového klienta v aplikaci SaaS, která používá databázový model jednoho klienta, musíte zřídit novou databázi klienta. Databáze musí být vytvořena v příslušné matné vrstvě umístění a služby. Musí být také inicializována s příslušnými daty schématu a referenční. A musí být registrována v katalogu pod příslušným klíčem klienta.

Různé přístupy k zřizování databáze lze použít. Můžete spouštět skripty SQL, nasadit bacpac nebo zkopírovat databázi šablon.

Zřizování databáze musí být součástí strategie správy schématu. Musíte se ujistit, že nové databáze jsou zřízeny s nejnovější schéma. Tento požadavek je prozkoumán v [kurzu správy schématu](saas-tenancy-schema-management.md).

Wingtip Tickets databáze na tenanta zřizuje nové klienty zkopírováním databáze šablony s názvem _basetenantdb_, která je nasazena na serveru katalogu. Zřizování lze integrovat do aplikace jako součást prostředí registrace. To také může být podporována v offline pomocí skriptů. Tento kurz zkoumá zřizování pomocí Prostředí PowerShell.

Zřizovací skripty zkopírují databázi _basetenantdb_ a vytvoří novou databázi klienta v elastickém fondu. Databáze klienta je vytvořena na serveru klienta mapované na _nový alias DNS klienta._ Tento alias udržuje odkaz na server používaný k zřízení nových klientů a je aktualizován tak, aby odkazoval na server klienta pro obnovení v kurzech zotavení po havárii ([zotavení po havárii pomocí georestoreu](saas-dbpertenant-dr-geo-restore.md), [ZOTAVENÍ pomocí georeplikace](saas-dbpertenant-dr-geo-replication.md)). Skripty pak inicializovat databázi s informacemi specifické pro klienta a zaregistrovat v mapě oddílu oddílu katalogu. Databáze klienta jsou uvedeny názvy na základě názvu klienta. Toto schéma pojmenování není důležitou součástí vzoru. Katalog mapuje klíč klienta na název databáze, takže lze použít libovolnou konvenci pojmenování.


## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Získejte skripty aplikace SaaS pro jednotlivé klienty wingtip tickets

Wingtip Tickets SaaS skripty a zdrojový kód aplikace jsou k dispozici v [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) GitHub úložiště. Podívejte se na [obecné pokyny pro](saas-tenancy-wingtip-app-guidance-tips.md) kroky ke stažení a odblokování wingtip vstupenky SaaS skripty.


## <a name="provision-and-catalog-detailed-walkthrough"></a>Podrobný postup zřizování a přidání tenantů do katalogu

Chcete-li pochopit, jak aplikace Wingtip Tickets implementuje nové zřizování klienta, přidejte zarážku a postupujte podle pracovního postupu při zřizování tenanta.

1. V prostředí PowerShell ISE otevřete ... \\Learning Modules\\ProvisionAndCatalog\\_Demo-ProvisionAndCatalog.ps1_ a nastavte následující parametry:

   * **$TenantName** = název nového místa (například *Bushwillow Blues*).
   * **$VenueType** = jeden z předem definovaných typů míst konání: _blues, klasická hudba, tanec, jazz, judo, motoristické závody, víceúčelové, operní, rockové, fotbalové_.
   * **$DemoScenario** = **1**, Zřízení *jednoho klienta*.

2. Chcete-li přidat zarážku, vložte kurzor na libovolné místo na řádek s *nápisem Nový klient .* Poté stiskněte klávesu F9.

   ![Zarážka](media/saas-dbpertenant-provision-and-catalog/breakpoint.png)

3. Skript spustíte stisknutím klávesy F5.

4. Po spuštění skriptu zastaví na zarážky, stisknutím klávesy F11 krok do kódu.

   ![Ladění](media/saas-dbpertenant-provision-and-catalog/debug.png)



Trasujte spuštění skriptu pomocí možností nabídky **Ladění.** Stisknutím kláves F10 a F11 přesunete nebo dostoupíte do volavaná funkce. Další informace o ladění skriptů prostředí PowerShell naleznete [v tématu Tipy pro práci se skripty prostředí PowerShell a ladění](https://docs.microsoft.com/powershell/scripting/components/ise/how-to-debug-scripts-in-windows-powershell-ise).


Není nutné explicitně dodržovat tento pracovní postup. Vysvětluje, jak ladit skript.

* **Importujte modul CatalogAndDatabaseManagement.psm1.** Poskytuje abstrakce na úrovni katalogu a klienta přes funkce [správy střepů.](sql-database-elastic-scale-shard-map-management.md) Tento modul zapouzdřuje většinu vzoru katalogu a stojí za prozkoumání.
* **Importujte modul SubscriptionManagement.psm1.** Obsahuje funkce pro přihlášení k Azure a výběr předplatného Azure, se kterým chcete pracovat.
* **Získejte podrobnosti o konfiguraci.** Krok do Get-Configuration pomocí F11 a uvidíte, jak je zadána konfigurace aplikace. Názvy prostředků a další hodnoty specifické pro aplikaci jsou zde definovány. Tyto hodnoty neměňte, dokud se neseznámíte se skripty.
* **Získejte objekt katalogu.** Krok do Get-Catalog, který skládá a vrátí objekt katalogu, který se používá ve skriptu vyšší úrovně. Tato funkce používá funkce správy sít, které jsou importovány z **AzureShardManagement.psm1**. Objekt katalogu se skládá z následujících prvků:

   * $catalogServerFullyQualifiedName je vytvořen pomocí standardního stopka plus uživatelské jméno: _catalog-\<user\>.database.windows .net_.
   * $catalogDatabaseName se načte z této konfigurace: *tenantcatalog*.
   * Objekt $shardMapManager se inicializuje z databáze katalogu.
   * Objekt $shardMap se inicializuje z mapy horizontálního dělení _tenantcatalog_ v databázi katalogu. Objekt katalogu je složen a vrácen. Používá se ve scénáři vyšší úrovně.
* **Vypočítejte nový klíč klienta.** K vytvoření klíče tenanta z jeho jména se používá funkce hash.
* **Zkontrolujte, zda klíč klienta již existuje.** Katalog je kontrolována ujistěte se, že klíč je k dispozici.
* **Ke zřízení databáze tenanta se použije New-TenantDatabase.** Pomocí F11 můžete krok ovat, jak se databáze zřápápomocí [šablony Azure Resource Manager](../azure-resource-manager/resource-manager-template-walkthrough.md).

    Název databáze je sestavený z názvu tenanta, aby bylo jasné, jakému tenantovi příslušný horizontální oddíl patří. Můžete také použít jiné konvence pojmenování databáze. Šablona Správce prostředků vytvoří databázi klienta zkopírováním databáze šablony _(baseTenantDB_) na serveru katalogu. Jako alternativu můžete vytvořit databázi a inicializovat ji importem bacpac. Nebo můžete spustit inicializační skript z dobře známého umístění.

    Šablona Správce prostředků je ve složce ...\Learning Modules\Common\: *tenantdatabasecopytemplate.json*

* **Databáze klienta je dále inicializována.** Místo konání (nájemce) jméno a typ místa jsou přidány. Můžete také provést další inicializaci zde.

* **Databáze klienta je registrována v katalogu.** Je registrována pomocí *Add-TenantDatabaseToCatalog* pomocí klíče klienta. Ke vstupu do podrobností použijte funkční klávesu F11.

    * Databáze katalogu se přidá do mapy horizontálního dělení (tj. do seznamu známých databází).
    * Vytvoří se mapování, které propojí hodnotu klíče s horizontálním oddílem.
    * Další metadata o tenantovi (název místa konání) se přidá do tabulky Tenants v katalogu. Tabulka Tenants není součástí schématu správy síta a není nainstalována edcl. Tato tabulka ukazuje, jak lze rozšířit databázi katalogu pro podporu dalších dat specifických pro aplikaci.


Po dokončení zřizování se spuštění vrátí do původního skriptu *Demo-ProvisionAndCatalog.* Otevře se stránka **Události** pro nového klienta v prohlížeči.

   ![Stránka Události](media/saas-dbpertenant-provision-and-catalog/new-tenant.png)


## <a name="provision-a-batch-of-tenants"></a>Zřízení dávky klientů

Toto cvičení stanoví dávku 17 nájemníků. Doporučujeme zřídit tuto dávku klientů před spuštěním jiných wingtip tickets saas databáze na tenanta kurzy. Existuje více než jen několik databází pro práci.

1. V prostředí PowerShell ISE otevřete ... \\Výukové moduly\\ProvisionAndCatalog\\*Demo-ProvisionAndCatalog.ps1*. Změňte *parametr $DemoScenario* na 3:

   * **$DemoScenario** = **3**, *Zřídit dávku nájemníků*.
2. Skript spustíte stisknutím klávesy F5.

Skript nasadí dávku dalších tenantů. Používá [šablonu Azure Resource Manager,](../azure-resource-manager/resource-manager-template-walkthrough.md) která řídí dávky a delegáti zřizování každé databáze na propojenou šablonu. Tento způsob použití šablon umožňuje Azure Resource Manageru zprostředkovat skriptu proces zřizování. Šablony zřizují databáze paralelně a v případě potřeby zpracovávají opakované pokusy. Skript je idempotentní, takže pokud selže nebo se z nějakého důvodu zastaví, spusťte jej znovu.

### <a name="verify-the-batch-of-tenants-that-successfully-deployed"></a>Ověřte dávku klientů, kteří byli úspěšně nasazeni

* Na [webu Azure Portal](https://portal.azure.com)vyhledejte seznam serverů a otevřete server *tenantů1.* Vyberte **databáze SQL**a ověřte, zda je dávka dalších 17 databází nyní v seznamu.

   ![Seznam databází](media/saas-dbpertenant-provision-and-catalog/database-list.png)



## <a name="other-provisioning-patterns"></a>Další způsoby zřizování

Další zřizování vzory nejsou zahrnuty v tomto kurzu:

**Pre-provisioning databáze**: Pre-zřizování vzor využívá skutečnost, že databáze v elastickém fondu nepřidávají další náklady. Fakturace je pro elastický fond, ne databáze. Nečinné databáze nespotřebovávají žádné prostředky. Tím, že předem zřídí databáze ve fondu a jejich přidělení v případě potřeby, můžete zkrátit čas přidat klienty. Počet předem zřízených databází lze upravit podle potřeby zachovat vyrovnávací paměť vhodnou pro předpokládanou míru zřizování.

**Automatické zřizování**: V automatickézřizování vzor zřizování zřizování služby zřídí servery, fondy a databáze automaticky podle potřeby. Pokud chcete, můžete zahrnout databáze předběžného zřizování do elastických fondů. Pokud jsou databáze vyřazeny z provozu a odstraněny, mezery v elastických fondech mohou být vyplněny službou zřizování. Taková služba může být jednoduché nebo složité, jako je například zpracování zřizování napříč více zeměpisných oblastí a nastavení geografické replikace pro zotavení po havárii.

Se vzorem automatického zřizování klientská aplikace nebo skript odešle požadavek na zřízení do fronty, která má být zpracována službou zřizování. Potom dotazování služby k určení dokončení. Pokud se používá předběžné zřizování, požadavky jsou zpracovány rychle. Služba zřazuje náhradní databázi na pozadí.


## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
>
> * Zřízení jednoho nového klienta.
> * Zřízení dávky dalších klientů.
> * Krok do podrobností zřizování klientů a jejich registraci do katalogu.

Vyzkoušejte [kurz sledování výkonu](saas-dbpertenant-performance-monitoring.md).

## <a name="additional-resources"></a>Další zdroje

* Další [kurzy, které staví na wingtip vstupenky SaaS databáze-per-tenant aplikace](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Klientská knihovna Elastic Database](sql-database-elastic-database-client-library.md)
* [Ladění skriptů v prostředí Windows PowerShell ISE](https://docs.microsoft.com/powershell/scripting/components/ise/how-to-debug-scripts-in-windows-powershell-ise)
