---
title: "Zřídit nové klienty ve víceklientské aplikaci, která používá Azure SQL Database | Microsoft Docs"
description: "Zjistěte, jak zřídit a nové klienty v aplikaci SaaS víceklientské Azure SQL Database v katalogu"
keywords: kurz k sql database
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.custom: scale out apps
ms.topic: article
ms.date: 08/11/2017
ms.author: sstein
ms.openlocfilehash: 21f0bca3a16164ead4e0990842a968fd9b95c33f
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2018
---
# <a name="learn-how-to-provision-new-tenants-and-register-them-in-the-catalog"></a>Zjistěte, jak zřídit nové klienty a zaregistrujte je v katalogu

V tomto kurzu informace o zřizování a katalog vzory SaaS a jak jsou implementované v databázi Wingtip lístky SaaS na aplikaci klienta. Vytvoření a inicializace nové databáze klienta a zaregistrujte je v katalogu klienta aplikace. Katalog je databáze, která zajišťuje mapování mezi aplikace SaaS velký počet klientů a jejich data. Katalog hraje důležitou roli odkazovat aplikace a správu žádosti o databázi správné.  

V tomto kurzu se naučíte:

> [!div class="checklist"]

> * Zřídit jednoho nového tenanta.
> * Zřídit dávku dalších tenantů.


Předpokladem dokončení tohoto kurzu je splnění následujících požadavků:

* Databáze SaaS lístky Wingtip za klienta aplikace se nasazuje. Nasazení za méně než pět minut najdete v tématu [nasazení a seznamte se s databázi Wingtip lístky SaaS na aplikaci klienta](saas-dbpertenant-get-started-deploy.md)
* Je nainstalované prostředí Azure PowerShell. Podrobnosti najdete v článku [Začínáme s prostředím Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

## <a name="introduction-to-the-saas-catalog-pattern"></a>Úvod do katalogu SaaS

V databázi zálohovanou víceklientské aplikaci SaaS je důležité vědět, se uloží informace pro každého klienta. Ve vzoru katalogu SaaS je databáze katalogu používané pro udržení mapování mezi každého klienta a databázi, ve kterém jsou uchovávána data. Tento vzor platí vždy, když klient dat je distribuován do více databází.

Každý klient je identifikována klíč v katalogu, která se mapuje na umístění jejich databáze. V aplikaci Wingtip lístky klíč je vytvořen z hodnoty hash názvu klienta. To umožňuje aplikaci vytvořit klíč z názvu klienta v adrese URL aplikace. Může použít jiná schémata klíče klienta.  

Katalogu umožňuje název nebo umístění databáze se musí změnit s minimálním dopadem na aplikaci.  V databázi víceklientského modelu to rovněž umožňuje, 'Přesun' klienta mezi databázemi.  Katalog lze také označuje, zda klient nebo databáze do režimu offline za účelem údržby nebo jiné akce. To je prozkoumali v [obnovení jednoho klienta kurzu](saas-dbpertenant-restore-single-tenant.md).

Katalog také můžete uložit dalších metadat klienta nebo databáze, například verze schématu, plán služeb nebo SLA nabízet pro klienty, stejně jako ostatní informace, která umožňuje správu aplikací, na zákaznickou podporu nebo devops.  

Nad rámec aplikace SaaS můžete povolit katalogu Nástroje databáze.  V databázi Wingtip lístky SaaS na klienta vzorek, katalogu slouží k povolení klienta mezi dotaz, prozkoumali v [vykazování ad hoc kurzu](saas-tenancy-cross-tenant-reporting.md). Správa úloh mezidatabázové je prozkoumali v [Správa schématu](saas-tenancy-schema-management.md) a [klienta analytics](saas-tenancy-tenant-analytics.md) kurzy. 

V ukázkách Wingtip lístky SaaS katalogu je implementovaná pomocí funkcím pro správu horizontálního oddílu [elastické databáze klienta knihovny (EDCL)](sql-database-elastic-database-client-library.md). EDCL je k dispozici v jazyce Java a rozhraní .net Framework. EDCL umožňuje aplikaci vytvářet, spravovat a použít mapování horizontálních databáze zálohována. Mapování horizontálních obsahuje seznam horizontálních oddílů (databáze) a mapování mezi klíče (klienty) a horizontálních oddílů.  Funkce EDCL se používají během zřizování vytvořit položky v mapě horizontálního oddílu a v době spuštění klienta aplikace pro připojení k databázi správné. EDCL ukládá do mezipaměti informace o připojení a minimalizovat provoz do databáze katalogu zrychlit aplikace.  

> [!IMPORTANT]
> Data o mapování jsou přístupná v katalogové databázi, ale *neupravujte je*! K jejich úpravě používejte jen rozhraní API klientské knihovny Elastic Database. Pokud budete manipulovat přímo s daty mapování, riskujete poškození katalogu, protože takový postup není podporovaný.


## <a name="introduction-to-the-saas-provisioning-pattern"></a>Úvod do zřizování vzor SaaS

Při připojování nového klienta v aplikaci SaaS, kterou používá model databáze jednoho klienta, novou databázi klienta musí být zřízená.  Databáze musí vytvořit v příslušné umístění a úroveň služby, inicializován s odpovídající schématu a referenčních dat a zaregistrovány v katalogu pod klíčem odpovídající klienta.  

Různý přístup k databázi zřizování lze použít, mezi které patří provádění skripty SQL, nasazení souboru bacpac nebo kopírování šablony databáze.  

Zřizování, musí být součástí strategie správy schématu, které musíte zajistit, že nové databáze zřízený s nejnovější schématu databáze. Tento požadavek je prozkoumali v [schématu správu kurzu](saas-tenancy-schema-management.md).  

Databázi Wingtip lístky jednu aplikaci klienta zřídí nové klienty pomocí kopírování šablony databáze s názvem _basetenantdb_, nasazené na serveru katalogu.  Zřizování můžete integrovat do aplikace jako součást registrace prostředí, nebo by se měl podporované v režimu offline pomocí skriptů. V tomto kurzu jsou zde popsány zřizování pomocí prostředí PowerShell. Zřizování skriptů kopie _basetenantdb_ databáze vytvořte novou databázi klienta v elastickém fondu, a inicializace databáze s informacemi o konkrétního klienta a zaregistrovat ho v mapě horizontálního oddílu katalogu.  Databáze klienta se zadaným názvy na základě názvu klienta, ale toto schéma pojmenování není důležitou součástí vzoru – jako katalogu mapuje klíče tenanta na název databáze, které můžete použít všechny zásady vytváření názvů. 


## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Získat aplikační skripty Wingtip lístky SaaS databáze za klienta

Adresář Wingtip lístky SaaS skripty a zdrojový kód aplikace, které jsou k dispozici v [WingtipTicketsSaaS DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) úložiště GitHub. Podívejte se [obecné pokyny](saas-tenancy-wingtip-app-guidance-tips.md) kroky, jak stáhnout a odblokování skripty Wingtip lístky SaaS.


## <a name="provision-and-catalog-detailed-walkthrough"></a>Podrobný postup zřizování a přidání tenantů do katalogu

Abyste pochopili, jak aplikace Wingtip lístky implementuje nového klienta zřizování, přidejte zarážek a krok v pracovním postupu při zřizování klienta:

1. V _prostředí PowerShell ISE_, otevřete... \\Learning moduly\\ProvisionAndCatalog\\_ukázku ProvisionAndCatalog.ps1_ a nastavit následující parametry:
   * **$TenantName** = název nového místa (například *Bushwillow Blues*).
   * **$VenueType** = jeden z typů předdefinovaného místo: _modré, classicalmusic, tance, jazz, judo, motorových racing víceúčelových, opera, rockmusic, fotbalový_.
   * **$DemoScenario** = **1**do *zřídit jednoho klienta*.

1. Přidejte zarážku umístěním kurzoru kdekoli v řádku, která uvádí, že: *nového klienta,*a stiskněte klávesu **F9**.

   ![přerušení](media/saas-dbpertenant-provision-and-catalog/breakpoint.png)

1. Ke spuštění skriptu stiskněte **F5**.

1. Po spuštění skriptu se zastaví u zarážky, stiskněte klávesu **F11** ke kroku do kódu.

   ![Ladění](media/saas-dbpertenant-provision-and-catalog/debug.png)



Trasování spuštění skriptu na pomocí **ladění** možnosti nabídky - **F10** a **F11** krok přes nebo do volaných funkcí. Další informace o ladění skriptů prostředí PowerShell najdete v tématu [tipy k ladění skriptů prostředí PowerShell a práce s](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise).


Následující nejsou explicitně provést kroky, ale vysvětlení, které jednotlivé kroky při ladění skriptu pracovního postupu:

1. **Importuje modul CatalogAndDatabaseManagement.psm1**, který nabízí obecný přehled funkcí pro [správu horizontálních oddílů (Shard Management)](sql-database-elastic-scale-shard-map-management.md) na úrovni katalogu a tenanta. Tento modul zapouzdří většinu vzoru katalogu a je vhodné využít.
1. **Importuje modul SubscriptionManagement.psm1**, který obsahuje funkce pro přihlášení do Azure a výběr předplatného Azure, se kterým pracujete.
1. **Získejte podrobnosti o konfiguraci**. Krok do Get-konfigurace (s F11), abyste viděli, jak je zadána konfigurace aplikace. Názvy prostředků a jiné hodnoty, konkrétní aplikace jsou zde definované, ale nebudou změněna tyto hodnoty, dokud se seznámíte s skripty.
1. **Získejte katalog objektů**. Krok do Get-katalogu, která vytvoří a vrátí objekt katalog, který se používá ve skriptu vyšší úrovně.  Tato funkce využívá funkce správy horizontálního oddílu, které jsou importovány z **AzureShardManagement.psm1**. Objekt katalogu se skládá z následujících elementů:
   * $catalogServerFullyQualifiedName je vytvořený pomocí standardní stem a své uživatelské jméno: _katalogu -\<uživatele\>. database.windows .net_.
   * $catalogDatabaseName se načte z této konfigurace: *tenantcatalog*.
   * Objekt $shardMapManager se inicializuje z databáze katalogu.
   * Objekt $shardMap se inicializuje z mapy horizontálního dělení _tenantcatalog_ v databázi katalogu.
   Objekt katalogu se sestaví, vrátí a použije ve skriptu vyšší úrovně.
1. **Vypočítejte klíč nového tenanta**. K vytvoření klíče tenanta z jeho jména se používá funkce hash.
1. **Zkontrolujte, jestli klíč tenanta ještě neexistuje**. Pokud chcete ověřit dostupnost klíče, zkontrolujte katalog.
1. **Ke zřízení databáze tenanta se použije New-TenantDatabase.** Použití **F11** krok v a v tématu jak databáze je zřízený pomocí [šablony Azure Resource Manageru](../azure-resource-manager/resource-manager-template-walkthrough.md).

    Název databáze se vytvářejí na základě názvu klienta, aby bylo jasné, které horizontálního oddílu patří ke které klientovi, přestože by bylo možné použít zásady vytváření názvů jiné databáze.
    Šablony Resource Manageru vytvoří databázi klienta zkopírováním šablonu databáze (_baseTenantDB_) na serveru katalogu. Jako alternativu může vytvořit databázi a provést jeho inicializaci importováním souboru bacpac nebo spusťte skript inicializaci ze známých umístění.

    Šablona Resource Manager je ve složce Modules\Common\ ...\Learning: *tenantdatabasecopytemplate.json*

1. Po vytvoření databáze klienta je pak další **inicializován s názvem místo (klientů) a typ místo**. Zde také můžete provést další inicializaci.

1. **Databáze klienta je zaregistrován v katalogu** s *přidat TenantDatabaseToCatalog* pomocí klíče klienta. Ke vstupu do podrobností použijte funkční klávesu **F11**.

    * Databáze katalogu se přidá do mapy horizontálního dělení (tj. do seznamu známých databází).
    * Vytvoří se mapování, které propojí hodnotu klíče s horizontálním oddílem.
    * Další metadata o klientovi (název místo) je přidán do *klienty* tabulky v katalogu.  Tabulka klienty není součástí ShardManagement schéma a není nainstalovaná v rámci EDCL.  Tato tabulka ukazuje, jak může být databáze katalogu rozšířené k podpoře další data specifické pro aplikaci.   


Po zřízení dokončení zpracování vrátí do původního *ukázku ProvisionAndCatalog* skript, který se otevře **události** stránky pro nového klienta v prohlížeči:

   ![stránka events](media/saas-dbpertenant-provision-and-catalog/new-tenant.png)


## <a name="provision-a-batch-of-tenants"></a>Zřídit dávky klientů

Toto cvičení zřídí dávky 17 klientů. Doporučuje se, že před spuštěním další adresář Wingtip lístky SaaS databáze za klienta kurzy, takže víc než několik databází pro práci s zřídíte tuto dávku klientů.

1. V *prostředí PowerShell ISE*, otevřete... \\Learning moduly\\ProvisionAndCatalog\\*ukázku ProvisionAndCatalog.ps1* a změňte *$DemoScenario* parametru 3:
   * **$DemoScenario** = **3**do *zřídit dávky klienty*.
1. Stisknutím klávesy **F5** spusťte skript.

Skript nasadí dávku dalších tenantů. Používá [šablony Azure Resource Manageru](../azure-resource-manager/resource-manager-template-walkthrough.md) která řídí batch a který deleguje zřizování každou databázi do propojené šablony. Tento způsob použití šablon umožňuje Azure Resource Manageru zprostředkovat skriptu proces zřizování. Zřízení šablony databáze paralelně a v případě potřeby zpracovává opakování. Skript je idempotent, takže pokud selže nebo zastaví z jakéhokoli důvodu, potom ho spusťte znovu.

### <a name="verify-the-batch-of-tenants-successfully-deployed"></a>Ověření úspěšného nasazení dávky tenantů

* V [portál Azure](https://portal.azure.com), přejděte do seznamu serverů a otevřete *tenants1* serveru. Klikněte na tlačítko **databází SQL** a zkontrolujte dávku 17 další databáze, jsou nyní v seznamu:

   ![seznam databází](media/saas-dbpertenant-provision-and-catalog/database-list.png)



## <a name="other-provisioning-patterns"></a>Další způsoby zřizování

K dalším způsobům zřizování, které nejsou zahrnuty do tohoto kurzu, patří:

**Předběžné zřizování databází.** Předem zřizování vzor zneužije skutečnost, že databáze v elastickém fondu nepřidávejte zpoplatněné. Fakturuje se pro elastický fond není databáze, a nečinnosti databáze využívat žádné prostředky. Předem zřizování databází ve fondu a přidělování je podle potřeby, může výrazně snížit čas registrace klienta. Počet databází předem zřizovat lze upravit podle potřeby zachovat vhodný pro předpokládaného zřizování rychlost vyrovnávací paměti.

**Automatické zřizování.** Ve vzoru automatické zřizování, zřizování serveru zřizuje služby, fondy a databáze automaticky potřeby – včetně předem zřizování databází v elastické fondy, v případě potřeby. A pokud jsou databáze zrušte uvedena do provozu a odstranit, mezery v elastické fondy může být vyplněny zřizování služby. Tato služba může být jednoduché nebo komplexní – například zpracování zřizování napříč několika zeměpisných oblastí a může nastavit geografická replikace pro zotavení po havárii. Pomocí vzoru automatické zřizování klientská aplikace nebo skriptu odešle žádost o zřizování do fronty ke zpracování pomocí služby zřizování a pak cyklicky dotazuje službu, kterou chcete určit dokončení. Pokud se používá předem zřizování, by požadavky zpracovávány rychle službou zřizování nahrazení databáze na pozadí.


## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]

> * Zřídit jednoho nového tenanta.
> * Zřídit dávku dalších tenantů.
> * Krok na podrobné informace o zřizování klientů a jejich registrace do katalogu

Zkuste [kurzu monitorování výkonu](saas-dbpertenant-performance-monitoring.md).

## <a name="additional-resources"></a>Další prostředky

* Další [návodů, které stavět na adresář Wingtip lístky SaaS databáze na aplikaci klienta](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Klientská knihovna Elastic Database](sql-database-elastic-database-client-library.md)
* [Ladění skriptů v integrovaném skriptovacím prostředí (ISE) v prostředí Windows PowerShell](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise)
