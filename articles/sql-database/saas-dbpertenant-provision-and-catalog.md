---
title: Zřídit nové klienty ve víceklientském aplikaci, která používá Azure SQL Database | Microsoft Docs
description: Zjistěte, jak zřídit a nové klienty v Azure SQL Database víceklientské aplikace SaaS v katalogu
keywords: kurz k sql database
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.custom: scale out apps
ms.topic: article
ms.date: 08/11/2017
ms.author: sstein
ms.openlocfilehash: 1accc672e396c5a9405369654f9bc4f8463c9afc
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="learn-how-to-provision-new-tenants-and-register-them-in-the-catalog"></a>Zjistěte, jak zřídit nové klienty a zaregistrujte je v katalogu

V tomto kurzu zjistěte, jak zřídit a vzory SaaS v katalogu. Také zjistíte, jak jste implementována v aplikaci SaaS lístky Wingtip databáze za klienta. Vytvoření a inicializace nové databáze klienta a zaregistrujte je v katalogu klienta aplikace. Katalog je databáze, která zajišťuje mapování mezi aplikace SaaS velký počet klientů a jejich data. Katalog hraje důležitou roli v odkazovat aplikace a správu žádosti o databázi správné.

V tomto kurzu se naučíte:

> [!div class="checklist"]

> * Zřídit jednoho nového klienta.
> * Zřídit dávky další klienty.


Předpokladem dokončení tohoto kurzu je splnění následujících požadavků:

* Nasazení aplikace SaaS lístky Wingtip databáze za klienta. Nasazení za méně než pět minut, najdete v části [nasazení a seznamte se s databáze za klienta aplikace SaaS lístky Wingtip](saas-dbpertenant-get-started-deploy.md).
* Je nainstalované prostředí Azure PowerShell. Další informace najdete v tématu [Začínáme s Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

## <a name="introduction-to-the-saas-catalog-pattern"></a>Úvod do katalogu vzoru SaaS

V databázi zálohovanou víceklientské aplikaci SaaS je důležité vědět, se uloží informace pro každého klienta. Ve vzoru katalogu SaaS je databáze katalogu používané pro udržení mapování mezi každého klienta a databázi, ve kterém jsou uchovávána data. Tento vzor platí vždy, když klient dat je distribuován do více databází.

Každý klient je identifikována klíč v katalogu, která se mapuje na umístění jejich databáze. V aplikaci Wingtip lístky klíč je vytvořen z hodnoty hash názvu klienta. Toto schéma umožňuje aplikaci vytvořit klíč z názvu klienta v adrese URL aplikace. Můžete použít jiná schémata klíče klienta.  

Katalogu umožňuje název nebo umístění databáze se musí změnit s minimálním dopadem na aplikaci. V modelu víceklientské databáze tato možnost rovněž umožňuje přesun klienta mezi databází. Katalogu můžete použít také k označení, zda klient nebo databáze je offline z důvodu údržby nebo jiné akce. Tato funkce je prozkoumali v [obnovení jednoho klienta kurzu](saas-dbpertenant-restore-single-tenant.md).

Katalog také můžete uložit další klient nebo metadata databáze, jako je verze schématu, plán služeb nebo SLA nabízené klientům. Další informace, které umožňuje správu aplikací, na zákaznickou podporu nebo DevOps může ukládat do katalogu. 

Nad rámec aplikace SaaS můžete povolit katalogu Nástroje databáze. V ukázce databáze za klienta Wingtip lístky SaaS katalogu slouží k povolení klienta mezi dotaz, což je prozkoumali v [Ad-hoc reporting kurzu](saas-tenancy-cross-tenant-reporting.md). Správa úloh mezidatabázové je prozkoumali v [Správa schématu](saas-tenancy-schema-management.md) a [klienta analytics](saas-tenancy-tenant-analytics.md) kurzy. 

V ukázkách Wingtip lístky SaaS, je implementována katalogu pomocí funkcím pro správu horizontálního oddílu [klientské knihovny pro elastické databáze (EDCL)](sql-database-elastic-database-client-library.md). EDCL je k dispozici v jazyce Java a rozhraní .NET Framework. EDCL umožňuje aplikaci vytvářet, spravovat a použít mapování horizontálních databáze zálohována. 

Mapování horizontálních obsahuje seznam horizontálních oddílů (databáze) a mapování mezi klíče (klienty) a horizontálních oddílů. Funkce EDCL se používají během zřizování vytvořit položky v mapě horizontálního oddílu klienta. Jste použili v době běhu aplikace pro připojení k databázi správné. EDCL ukládá do mezipaměti informace o připojení a minimalizovat provoz do databáze katalogu zrychlit aplikace. 

> [!IMPORTANT]
> Mapování data jsou přístupná v katalogu databázi, ale *nemáte upravit*. Upravte mapování dat pouze pomocí rozhraní API knihovny klienta elastické databáze. Přímá manipulace s mapování dat rizika poškozování katalogu a není podporována.


## <a name="introduction-to-the-saas-provisioning-pattern"></a>Úvod do zřizování vzor SaaS

Při přidání nového klienta do aplikace SaaS, která používá model databáze jednoho klienta, je třeba zřídit novou databázi klienta. Databáze musí vytvořit v příslušné umístění a vrstvy služeb. Je také nutné inicializovat pomocí příslušného schématu a referenční data. A musí být zaregistrovány v katalogu pod klíčem odpovídající klienta. 

Můžete použít různý přístup k zřizování databáze. Můžete spustit skripty SQL, nasazení souboru bacpac nebo zkopírujte šablonu databáze. 

Zřizování, musí být součástí strategie správy schématu databáze. Musí se ujistěte, že nové databáze zřízený s nejnovější schématu. Tento požadavek je prozkoumali v [schématu správu kurzu](saas-tenancy-schema-management.md). 

Databáze za klienta aplikace Wingtip lístky zřídí nové klienty pomocí kopírování šablony databáze s názvem _basetenantdb_, který je nasazen na serveru katalogu. Zřizování se dají integrovat do aplikace jako součást registrace prostředí. Je také může být podporováno offline pomocí skriptů. V tomto kurzu jsou zde popsány zřizování pomocí prostředí PowerShell. 

Zřizování skriptů kopie _basetenantdb_ databáze slouží k vytvoření nové databáze klienta v elastickém fondu. Skripty pak inicializace databáze s informace specifické pro klienta a zaregistrovat ji v mapě horizontálního oddílu katalogu. Databáze klienta jsou názvy zadaným na základě názvu klienta. Toto schéma pojmenování není důležitou součástí vzoru. Klíč tenanta název databáze, proto můžete použít všechny zásady vytváření názvů mapuje katalogu. 


## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Získat skripty databáze za klienta aplikace SaaS Wingtip lístky

Adresář Wingtip lístky SaaS skripty a zdrojový kód aplikace, které jsou k dispozici v [WingtipTicketsSaaS DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) úložiště GitHub. Podívejte se [obecné pokyny](saas-tenancy-wingtip-app-guidance-tips.md) kroky, jak stáhnout a odblokování skripty Wingtip lístky SaaS.


## <a name="provision-and-catalog-detailed-walkthrough"></a>Podrobný postup zřizování a přidání tenantů do katalogu

Abyste pochopili, jak aplikace Wingtip lístky implementuje nového klienta zřizování, přidejte zarážku a pracovní postup při zřízení klienta.

1. V prostředí PowerShell ISE otevřete... \\Learning moduly\\ProvisionAndCatalog\\_ukázku ProvisionAndCatalog.ps1_ a nastavit následující parametry:

   * **$TenantName** = název nového místa (například *Bushwillow Blues*).
   * **$VenueType** = jeden z typů předdefinovaného místo: _modré, classicalmusic, tance, jazz, judo, motorových racing víceúčelových, opera, rockmusic, fotbalový_.
   * **$DemoScenario** = **1**, *zřídit jednoho klienta*.

2. Pokud chcete přidat zarážky, umístěte kurzor kdekoli na řádku, která uvádí, že *nového klienta,*. Stiskněte klávesu F9.

   ![Zarážek](media/saas-dbpertenant-provision-and-catalog/breakpoint.png)

3. Pokud chcete spustit skript, stisknutím klávesy F5.

4. Po spuštění skriptu se zastaví u zarážky, stisknutím klávesy F11 kroku do kódu.

   ![Ladění](media/saas-dbpertenant-provision-and-catalog/debug.png)



Trasování pomocí skriptu **ladění** možnosti nabídky. Stiskněte klávesu F10 a F11 krok přes nebo do volaných funkcí. Další informace o ladění skriptů prostředí PowerShell najdete v tématu [tipy k ladění skriptů prostředí PowerShell a práce s](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise).


Není nutné explicitně postupujte podle tento pracovní postup. Vysvětluje, jak laďte skript.

* **Naimportujte modul CatalogAndDatabaseManagement.psm1.** Nabízí katalog a klienta úrovni abstrakce přes [horizontálního oddílu správy](sql-database-elastic-scale-shard-map-management.md) funkce. Tento modul zapouzdří většinu vzoru katalogu a je vhodné využít.
* **Naimportujte modul SubscriptionManagement.psm1.** Obsahuje funkce pro přihlášení do Azure a výběr předplatného Azure, kterou chcete pracovat.
* **Získáte podrobnosti o konfiguraci.** Krok do Get-konfigurace pomocí F11 a v tom, jak je zadána konfigurace aplikace. Zde jsou definovány názvy prostředků a jiné hodnoty, konkrétní aplikace. Tyto hodnoty nemáte změnit, dokud se seznámíte s skripty.
* **Získání objektu katalogu.** Krok do Get-katalogu, která vytvoří a vrátí objekt katalog, který se používá ve skriptu vyšší úrovně. Tato funkce využívá funkce správy horizontálního oddílu, které jsou importovány z **AzureShardManagement.psm1**. Objekt katalogu se skládá z následujících elementů:

   * $catalogServerFullyQualifiedName je vytvořený pomocí standardní stem a své uživatelské jméno: _katalogu -\<uživatele\>. database.windows .net_.
   * $catalogDatabaseName se načte z této konfigurace: *tenantcatalog*.
   * Objekt $shardMapManager se inicializuje z databáze katalogu.
   * Objekt $shardMap se inicializuje z mapy horizontálního dělení _tenantcatalog_ v databázi katalogu. Objekt katalogu je sestavit a vrácena. Používá se ve skriptu vyšší úrovně.
* **Vypočítá nový klíč klienta.** K vytvoření klíče tenanta z jeho jména se používá funkce hash.
* **Zkontrolujte, zda klíč klienta již existuje.** Katalog zkontrolován, ujistěte se, že klíč je k dispozici.
* **Ke zřízení databáze tenanta se použije New-TenantDatabase.** Krok v tom, jak je databáze přístupová práva pomocí pomocí F11 [šablony Azure Resource Manageru](../azure-resource-manager/resource-manager-template-walkthrough.md).

    Název databáze je sestavený z názvu tenanta, aby bylo jasné, jakému tenantovi příslušný horizontální oddíl patří. Můžete taky použít zásady vytváření názvů jiné databáze. Šablony Resource Manageru vytvoří databázi klienta zkopírováním šablonu databáze (_baseTenantDB_) na serveru katalogu. Alternativně můžete vytvořit databázi a provést jeho inicializaci importováním souboru bacpac. Nebo můžete spustit skript inicializaci ze známých umístění.

    Šablona Resource Manager je ve složce Modules\Common\ ...\Learning: *tenantdatabasecopytemplate.json*

* **Databáze klienta další inicializovaná.** Název místo (klientů) a typ místo se přidají. Můžete také provést další inicializace sem.

* **Databáze klienta se registruje v katalogu.** Není zaregistrována *přidat TenantDatabaseToCatalog* pomocí klíče klienta. Použití F11 přejde na podrobné informace:

    * Databáze katalogu se přidá do mapy horizontálního dělení (tj. do seznamu známých databází).
    * Vytvoří se mapování, které propojí hodnotu klíče s horizontálním oddílem.
    * Další metadata o klientovi (název místo) se přidá do tabulky klienty v katalogu. Klienti tabulky není součástí schématu správu horizontálního oddílu a ve EDCL není nainstalována. Tato tabulka ukazuje, jak může být databáze katalogu rozšířené k podpoře další data specifické pro aplikaci.


Jakmile bude zřizování dokončeno, provádění vrátí do původního *ukázku ProvisionAndCatalog* skriptu. **Události** otevře se stránka pro nového klienta v prohlížeči.

   ![Stránka události](media/saas-dbpertenant-provision-and-catalog/new-tenant.png)


## <a name="provision-a-batch-of-tenants"></a>Zřídit dávky klientů

Toto cvičení zřídí dávky 17 klientů. Doporučujeme, abyste před zahájením dalších kurzech databáze za klienta Wingtip lístky SaaS zřízení tuto dávku klientů. Existuje více než několik databází pro práci s.

1. V prostředí PowerShell ISE otevřete... \\Učení moduly\\ProvisionAndCatalog\\*ukázku ProvisionAndCatalog.ps1*. Změna *$DemoScenario* parametru 3:

   * **$DemoScenario** = **3**, *zřídit dávky klienty*.
2. Pokud chcete spustit skript, stisknutím klávesy F5.

Skript nasadí dávku dalších tenantů. Používá [šablony Azure Resource Manageru](../azure-resource-manager/resource-manager-template-walkthrough.md) , řídí dávky a deleguje zřizování každou databázi do propojené šablony. Tento způsob použití šablon umožňuje Azure Resource Manageru zprostředkovat skriptu proces zřizování. Šablony zřídit databází v paralelní a popisovač opakovaných pokusů, v případě potřeby. Skript je idempotent, takže pokud selže nebo zastaví z jakéhokoli důvodu, potom ho spusťte znovu.

### <a name="verify-the-batch-of-tenants-that-successfully-deployed"></a>Ověřte dávku klienty, kteří úspěšně nasazen.

* V [portál Azure](https://portal.azure.com), přejděte do seznamu serverů a otevřete *tenants1* serveru. Vyberte **databází SQL**a ověřte, že dávka 17 další databáze je nyní v seznamu.

   ![Seznam databází](media/saas-dbpertenant-provision-and-catalog/database-list.png)



## <a name="other-provisioning-patterns"></a>Další způsoby zřizování

Dalšími zřizování vzory nejsou zahrnuty v tomto kurzu:

**Předem zřizování databáze**: předem zřizování vzor zneužije skutečnost, že databáze v elastickém fondu nepřidáte zpoplatněné. Fakturuje se pro elastický fond není databáze. Nečinnosti databáze využívat žádné prostředky. Předem zřizování databází ve fondu a přidělování je podle potřeby můžete zkrátit dobu přidat klienty. Počet databází předem zřizovat lze upravit podle potřeby zachovat vhodný pro předpokládaného zřizování rychlost vyrovnávací paměti.

**Automatické zřizování**: ve vzoru zřizování automatické zřizování služby zřídí servery, fondy a databáze automaticky podle potřeby. Pokud chcete, můžete zahrnout předem zřizování databází v elastické fondy. Pokud jsou databáze vyřazena z provozu a odstranit, lze také vyplnit mezery v elastické fondy zřizování službou. Tato služba může být jednoduché nebo komplexní, jako je například zpracování zřizování napříč několika zeměpisných oblastí a nastavení geografická replikace pro zotavení po havárii. 

Pomocí vzoru automatické zřizování klientská aplikace nebo skriptu odešle požadavek na zřizování do fronty pro zpracování zřizování služby. Pak dotazuje službu, kterou chcete určit dokončení. Pokud se používá předem zřizování, jsou požadavky zpracovávány rychle. Služba zřídí nahrazení databáze na pozadí.


## <a name="next-steps"></a>Další postup

V tomto kurzu jste se naučili:

> [!div class="checklist"]

> * Zřídit jednoho nového klienta.
> * Zřídit dávky další klienty.
> * Krok na podrobné informace o zřizování klientů a jejich registrace do katalogu.

Zkuste [kurzu monitorování výkonu](saas-dbpertenant-performance-monitoring.md).

## <a name="additional-resources"></a>Další zdroje informací:

* Další [návodů, které vychází z aplikace SaaS lístky Wingtip databáze za klienta](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Klientská knihovna Elastic Database](sql-database-elastic-database-client-library.md)
* [Ladění skriptů v systému Windows PowerShell ISE](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise)
