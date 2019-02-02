---
title: Zřízení nových tenantů ve víceklientské aplikaci, která používá Azure SQL Database | Dokumentace Microsoftu
description: Další informace o zřízení a katalogizace nových tenantů ve víceklientské aplikace SaaS Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 09/24/2018
ms.openlocfilehash: aa884f2df76c20d3119022069179b08ba2f2a6b7
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/01/2019
ms.locfileid: "55565208"
---
# <a name="learn-how-to-provision-new-tenants-and-register-them-in-the-catalog"></a>Další informace o zřízení nových tenantů a jejich registrace v katalogu

V tomto kurzu se dozvíte, jak pro zřízení a katalogizace vzorce SaaS. Zjistíte také, jak jsou v nich implementované v aplikaci SaaS aplikace Wingtip Tickets databáze na tenanta. Vytváření a inicializace nových databází tenantů a jejich registrace v katalogu tenanta vaší aplikace. Katalog je databázi, která uchovává mapování různých tenantů aplikací SaaS a jejich data. Katalog hraje důležitou roli při přesměrování aplikace a požadavky sady management ke správné databázi.

V tomto kurzu se naučíte:

> [!div class="checklist"]

> * Zřídíte jednoho nového tenanta.
> * Zřídíte dávku dalších tenantů.


Předpokladem dokončení tohoto kurzu je splnění následujících požadavků:

* Nasazení aplikace SaaS aplikace Wingtip Tickets databáze na tenanta. Nasazení za méně než pět minut, najdete v článku [nasazení a zkoumání SaaS aplikace Wingtip Tickets databáze na tenanta aplikace](saas-dbpertenant-get-started-deploy.md).
* Je nainstalované prostředí Azure PowerShell. Další informace najdete v tématu [Začínáme s Azure PowerShellem](https://docs.microsoft.com/powershell/azure/get-started-azureps).

## <a name="introduction-to-the-saas-catalog-pattern"></a>Úvod do katalogovém systému SaaS

V databázi záložních víceklientskou aplikaci SaaS je důležité vědět, ve kterém je uložené informace každého tenanta. V katalogovém systému SaaS databáze katalogů slouží k uchovávání mapování mezi jednotlivé tenanty a databází, ve kterém jejich data uložená. Tento model se vztahuje pokaždé, když se data tenanta je distribuovaná napříč více databází.

Každý tenant je určeno klíčem v katalogu, která se mapuje na umístění jejich databáze. V aplikaci Wingtip Tickets je klíč tvořený hodnotou hash názvu tenanta. Toto schéma umožňuje aplikaci sestavit z něj klíč z názvu tenanta, který je součástí adresy URL aplikace. Můžete použít jiná schémata klíče tenanta.  

Katalog umožňuje název nebo umístění databáze, kterou chcete změnit s minimálním dopadem na aplikaci. Ve víceklientském databázový model tato funkce také obsáhne přesun tenanta mezi databázemi. Katalogu lze použít také k určení, jestli tenanta nebo databáze je v režimu offline za účelem údržby nebo jiné akce. Tato funkce je prozkoumali v [kurzu obnovení jednoho tenanta](saas-dbpertenant-restore-single-tenant.md).

Katalog také můžete ukládat další tenanta nebo metadata databáze, jako je verze schématu, plán služeb nebo smlouvy o úrovni služeb nabízených klientům. Další informace, které umožňují správu aplikací, zákaznická podpora nebo DevOps může ukládat do katalogu. 

Nad rámec aplikace SaaS můžete povolit katalog databázové nástroje. V ukázce databáze na tenanta SaaS aplikace Wingtip Tickets katalogu slouží k povolení dotazy mezi tenanty, která je prozkoumali v [Ad-hoc, generování sestav kurzu](saas-tenancy-cross-tenant-reporting.md). Mezidatabázové úlohy správy je prozkoumali v [Schema management](saas-tenancy-schema-management.md) a [Tenant analytics](saas-tenancy-tenant-analytics.md) kurzy. 

Ve službě SaaS aplikace Wingtip Tickets Ukázky katalogu implementovaná pomocí funkcí správy horizontálních oddílů [Klientská knihovna Elastic Database (EDCL)](sql-database-elastic-database-client-library.md). EDCL je k dispozici v jazyce Java a .NET Framework. EDCL umožňuje aplikaci vytvářet, spravovat a použít mapy horizontálních oddílů databáze zálohována. 

Mapy horizontálních oddílů obsahuje seznam horizontálních oddílů (databáze) a mapování klíčů (tenantů) a horizontální oddíly. EDCL funkce se používají během zřizování vytvořit položky v objektu map horizontálních oddílů klientů. Slouží v době běhu aplikace pro připojení ke správné databázi. EDCL ukládá do mezipaměti informace o připojení k minimalizovat provoz do databáze katalogu a zrychlil aplikaci. 

> [!IMPORTANT]
> Data o mapování jsou přístupná v katalogové databázi, ale *neupravujte je*. Upravte mapování dat pouze pomocí Elastic Database rozhraní API klientské knihovny. Přímo s daty mapování, riskujete poškození katalogu a není podporována.


## <a name="introduction-to-the-saas-provisioning-pattern"></a>Úvod do služby zřizování model SaaS

Při přidání nového tenanta v aplikaci SaaS, která používá model databáze s jedním tenantem, je třeba zřídit nové databáze tenanta. Databáze musí být vytvořeny úrovně služby a odpovídající umístění. Je také musí inicializovat pomocí příslušného schématu a referenční data. A je nutné jej zaregistrovat v katalogu pod klíčem tenanta odpovídající. 

Můžete použít různé přístupy k zřizování databáze. Můžete spouštět skripty SQL, nasazení souboru bacpac nebo kopírování databáze šablony. 

Zřízení databáze musí být součástí vaší strategie správy schématu. Ujistěte se, že nové databáze se zřizují pomocí nejnovější schéma. Tento požadavek je prozkoumali v [kurzu Správa schématu](saas-tenancy-schema-management.md). 

Databáze na tenanta aplikace Wingtip Tickets zřizuje nové tenanty zkopírováním šablony databázi s názvem _basetenantdb_, který je nasazen na server katalogu. Zřizování je možné integrovat do aplikací jako součást přihlašovacího prostředí. Je také může podporovat offline pomocí skriptů. Tento kurz se věnuje zřizování pomocí prostředí PowerShell. 

Zřizování skripty kopírování _basetenantdb_ databáze pro vytvoření nového tenanta databáze v elastickém fondu. Na serveru tenanta namapované na vytvoření databáze tenanta _newtenant_ DNS alias. Tento alias udržuje odkaz na server použít ke zřízení nových tenantů a aktualizovat tak, aby odkazoval na server pro obnovení tenanta v kurzech pro zotavení po havárii ([zotavení po Havárii pomocí georestore](saas-dbpertenant-dr-geo-restore.md), [zotavení po Havárii pomocígeografickáreplikace](saas-dbpertenant-dr-geo-replication.md)). Skripty pak inicializovat databázi pomocí informací specifických pro tenanta, zaregistrujte je v katalogu mapy horizontálních oddílů. Databáze tenantů jsou křestní jména na základě názvu tenanta. Toto schéma pojmenování není zásadní součástí tohoto vzoru. Katalog mapuje klíč tenanta pro název databáze, takže se dají použít libovolné zásady vytváření názvů. 


## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Získání skriptů aplikace databáze na tenanta SaaS aplikace Wingtip Tickets

SaaS aplikace Wingtip Tickets skripty a zdrojový kód aplikace, které jsou k dispozici v [WingtipTicketsSaaS DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) úložiště GitHub. Podívejte se [obecné pokyny](saas-tenancy-wingtip-app-guidance-tips.md) pokyny ke stažení a odblokování SaaS aplikace Wingtip Tickets skripty.


## <a name="provision-and-catalog-detailed-walkthrough"></a>Podrobný postup zřizování a přidání tenantů do katalogu

Abyste pochopili, jak aplikace Wingtip Tickets implementované zřizování nového tenanta, přidejte zarážku a pracovní postup při zřizování tenanta.

1. V prostředí PowerShell ISE otevřete... \\Learning Modules\\ProvisionAndCatalog\\_Demo-ProvisionAndCatalog.ps1_ a nastavit následující parametry:

   * **$TenantName** = název nového místa (například *Bushwillow Blues*).
   * **$VenueType** = jeden z míst předdefinované typy: _blues, classicalmusic, dance, jazz, judo, zajišťující racing, multipurpose, opera, rockmusic, soccer_.
   * **$DemoScenario** = **1**, *zřizování jednoho tenanta*.

2. Pro přidání zarážky, umístěte kurzor kdekoli na řádek, který říká *New-Tenant '*. Stiskněte klávesu F9.

   ![Zarážky](media/saas-dbpertenant-provision-and-catalog/breakpoint.png)

3. Stisknutím klávesy F5 spusťte skript.

4. Po spuštění skriptu se zastaví na zarážce, stisknutím klávesy F11 lze přepínat mezi Krokovat s vnořením kódu.

   ![Ladění](media/saas-dbpertenant-provision-and-catalog/debug.png)



Trasování provádění skriptu pomocí **ladění** možnosti nabídky. Stiskněte klávesu F10 a F11 kroku přes nebo do volané funkce. Další informace o ladění skriptů Powershellu najdete v tématu [tipy pro práci s a ladění skriptů Powershellu](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise).


Není nutné explicitně postupujte podle tohoto pracovního postupu. Vysvětluje, jak ladit skript.

* **Naimportujte modul CatalogAndDatabaseManagement.psm1.** Poskytuje katalogu a tenanta úrovni abstrakce přes [Shard Management](sql-database-elastic-scale-shard-map-management.md) funkce. Tento modul ukrývá většinu funkcí katalogu a je určitě stojí za prozkoumání.
* **Naimportujte modul SubscriptionManagement.psm1.** Obsahuje funkce pro přihlášení do Azure a výběr předplatného Azure, kterou chcete pracovat.
* **Získáte podrobnosti o konfiguraci.** Krokovat s vnořením Get-konfigurace s použitím F11 a zjistěte, jak je zadaná konfigurace aplikace. Jsou zde definované názvy prostředků a jiné hodnoty konkrétní aplikace. Neměnit tyto hodnoty, dokud nebudete s těmito skripty neseznámíte.
* **Získejte katalog objektů.** Krokovat s vnořením Get-katalog, který vytvoří a vrátí objekt katalogu, který se používá ve skriptu vyšší úrovně. Tato funkce využívá funkce správy horizontálních oddílů, které budou importovány z **AzureShardManagement.psm1**. Objekt katalogu se skládá z následujících elementů:

   * $catalogServerFullyQualifiedName je vytvořený pomocí standardní stem a uživatelské jméno: _catalog -\<uživatele\>. database.windows .net_.
   * $catalogDatabaseName se načte z této konfigurace: *tenantcatalog*.
   * Objekt $shardMapManager se inicializuje z databáze katalogu.
   * Objekt $shardMap se inicializuje z mapy horizontálního dělení _tenantcatalog_ v databázi katalogu. Objekt katalogu se skládá a vrácena. Používá se ve skriptu vyšší úrovně.
* **Vypočítejte klíč nového tenanta.** K vytvoření klíče tenanta z jeho jména se používá funkce hash.
* **Zaškrtněte, pokud klíč tenanta ještě neexistuje.** Katalog Ujistěte se, že klíč je k dispozici.
* **Ke zřízení databáze tenanta se použije New-TenantDatabase.** Pomocí F11 můžete krokovat s vnořením jak je databáze zřízena s použitím [šablony Azure Resource Manageru](../azure-resource-manager/resource-manager-template-walkthrough.md).

    Název databáze je sestavený z názvu tenanta, aby bylo jasné, jakému tenantovi příslušný horizontální oddíl patří. Také můžete použít jiné zásady vytváření názvů databáze. Šablony Resource Manageru se vytvoří databáze tenanta zkopírováním šablony databáze (_baseTenantDB_) na serveru katalogu. Jako alternativu můžete vytvořit databázi a inicializujete importem souboru bacpac. Nebo můžete spustit obstarejte inicializační skript ze známého umístění.

    Šablona Resource Manageru se ve složce Modules\Common\ ...\Learning: *tenantdatabasecopytemplate.json*

* **Dále se inicializuje databáze tenanta.** Místo (tenant) název a typ místa se přidají. Můžete také provést další inicializaci tady.

* **Databáze tenanta se zaregistruje v katalogu.** Je zaregistrován *Add-TenantDatabaseToCatalog* pomocí klíče tenanta. Pomocí F11 vstoupit do podrobností:

    * Databáze katalogu se přidá do mapy horizontálního dělení (tj. do seznamu známých databází).
    * Vytvoří se mapování, které propojí hodnotu klíče s horizontálním oddílem.
    * Další metadata o tenantovi (název místa) se přidá do tabulky Tenantů v katalogu. V tabulce klientů, které nejsou součástí schématu Shard Management a ve EDCL není nainstalována. Tato tabulka ukazuje, jak je možné rozšířit databáze katalogu pro podporu dalších dat specifické pro aplikaci.


Jakmile je zřizování hotové, spuštění se vrátí k původnímu *Demo-ProvisionAndCatalog* skriptu. **Události** pro nového klienta v prohlížeči otevře se stránka.

   ![Stránka události](media/saas-dbpertenant-provision-and-catalog/new-tenant.png)


## <a name="provision-a-batch-of-tenants"></a>Zřízení dávky tenantů

V tomto cvičení zřídíte dávku 17 tenantů. Doporučujeme zřídit tuto dávku tenantů před spuštěním další kurzy SaaS aplikace Wingtip Tickets databáze na tenanta. Existuje více než několik databází pro práci s.

1. V prostředí PowerShell ISE otevřete... \\Learning Modules\\ProvisionAndCatalog\\*Demo-ProvisionAndCatalog.ps1*. Změnit *$DemoScenario* parametr 3:

   * **$DemoScenario** = **3**, *zřízení dávky tenantů*.
2. Stisknutím klávesy F5 spusťte skript.

Skript nasadí dávku dalších tenantů. Používá [šablony Azure Resource Manageru](../azure-resource-manager/resource-manager-template-walkthrough.md) , která dávku řídí a deleguje zřizování každé databáze na propojenou šablonu. Tento způsob použití šablon umožňuje Azure Resource Manageru zprostředkovat skriptu proces zřizování. Šablony zřizují databáze v paralelních aplikací a popisovač opakované pokusy v případě potřeby. Skript je idempotentní, takže pokud selže nebo zastaví z jakéhokoli důvodu, znovu jej spusťte.

### <a name="verify-the-batch-of-tenants-that-successfully-deployed"></a>Ověřte dávku tenantů, které úspěšně nasadil

* V [webu Azure portal](https://portal.azure.com), přejděte na seznam serverů a otevřete *tenants1* serveru. Vyberte **databází SQL**a ověřte, zda batch 17 další databáze je nyní v seznamu.

   ![Seznam databází](media/saas-dbpertenant-provision-and-catalog/database-list.png)



## <a name="other-provisioning-patterns"></a>Další způsoby zřizování

K dalším způsobům zřizování není zahrnuta v tomto kurzu:

**Předběžným zřizováním databází**: Předem zřizovaných způsob využívá skutečnost, že databáze v elastickém fondu nepřidávají další náklady. Účtování probíhá za elastický fond, ne za databáze. Nečinné databáze nespotřebovávají žádné prostředky. Předem zřizovaných databází ve fondu a jejich přidělením v případě potřeby můžete snížit čas na přidání tenantů. Počet předběžně zřizovaných databází je možné upravit tak, aby měli k předpokládanému tempu zřizování tenantů.

**Automatické zřizování**: Ve vzoru automatického zřizování zřizovací služba zřídí serverů, fondů a databází automaticky podle potřeby. Pokud chcete, můžete zahrnout předběžné zřizování databází v elastických fondech. Pokud databáze jsou vyřazeny z provozu a odstranit, lze také vyplnit mezery v elastických fondech zřizovací služba. Tato služba může být jednoduché nebo složité, jako je zpracování zřizování v různých geografických oblastech a nastavení geografickou replikaci pro zotavení po havárii. 

Se vzorkem automatického zřizování odešle klientská aplikace nebo skript žádost o zřízení do fronty ke zpracování zřizovací služba. Poté dotazuje službu dokončení. Pokud použití předběžného zřizování požadavky rychle zpracovává. Služba zřídí náhradní databáze na pozadí.


## <a name="next-steps"></a>Další postup

V tomto kurzu jste se naučili:

> [!div class="checklist"]

> * Zřídíte jednoho nového tenanta.
> * Zřídíte dávku dalších tenantů.
> * Krokovat s vnořením podrobnosti o zřizování tenantů a zaregistrujete je do katalogu.

Zkuste [kurz o monitorování výkonu](saas-dbpertenant-performance-monitoring.md).

## <a name="additional-resources"></a>Další materiály

* Další [kurzů v aplikaci SaaS aplikace Wingtip Tickets databáze na tenanta](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Klientská knihovna Elastic Database](sql-database-elastic-database-client-library.md)
* [Ladění skriptů v prostředí Windows PowerShell ISE](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise)
