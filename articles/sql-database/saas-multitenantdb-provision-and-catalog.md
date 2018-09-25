---
title: Zřizování v SaaS s více tenanty Azure | Dokumentace Microsoftu
description: Další informace o zřízení a katalogizace nových tenantů v SaaS aplikaci více tenantů Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MightyPen
ms.author: genemi
ms.reviewer: billgib,andrela,stein
manager: craigg
ms.date: 04/01/2018
ms.openlocfilehash: e37bc5f46a1a56357e3dff9d1f67de7dcc2537b0
ms.sourcegitcommit: 715813af8cde40407bd3332dd922a918de46a91a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "47055301"
---
# <a name="provision-and-catalog-new-tenants-in-a-saas-application-using-a-sharded-multi-tenant-azure-sql-database"></a>Zřízení a katalogizace nových tenantů v SaaS aplikace využívající horizontálně dělené databázi s více tenanty Azure SQL

Tento článek popisuje zřízení a katalogizace nových tenantů v *horizontálně dělené databázi s více tenanty* model nebo model.

Tento článek obsahuje dvě hlavní části:

- [Rozbor](#goto_2_conceptual) zřízení a katalogizace nových tenantů.

- [Kurz](#goto_1_tutorial) , který zvýrazňuje kód skriptu prostředí PowerShell, který provede zřízení a katalogizace.
    - V tomto kurzu použijete aplikaci SaaS aplikace Wingtip Tickets přizpůsobený vzor horizontálně dělené databázi s více tenanty.

<a name="goto_2_conceptual"/>

## <a name="database-pattern"></a>Vzor databáze

V této části a navíc několik více, postupujte podle podívat a probrat si koncepty vzor horizontálně dělené databázi s více tenanty.

V tomto modelu horizontálně dělené více tenantů schémata tabulek v každé databázi zahrnovat klíče tenanta ve primárního klíče tabulky, které ukládání dat tenantů. Klíč tenanta umožňuje každé jednotlivé databáze k ukládání 0, 1 nebo velký počet klientů. Použití horizontálně dělené databáze umožňuje snadno pro aplikaci systému pro podporu velkého počtu klientů. Všechna data pro jednoho tenanta je uložen v jedné databázi. Velký počet klientů distribuují napříč mnoha horizontálně dělené databáze. Databáze katalogu obsahuje mapování jednotlivých klientů ke své databázi.

#### <a name="isolation-versus-lower-cost"></a>Izolace a nižší náklady

Tenant, který má databázi na sebe sama využívá výhod izolace. Tenanta k dispozici databáze obnovit k dřívějšímu datu bez jsou omezené na základě dopadu na ostatní tenanty. Výkon databáze můžete ladit znovu optimalizovat pro jednoho tenanta, aniž by přitom ohrozil s jinými tenanty. Problém je, že izolace je nákladnější než to stojí databázi sdílet s jinými tenanty.

Při zřízení nového tenanta databáze ji můžete sdílet s jinými tenanty, nebo může být umístěn do nové databáze. Můžete později změníte své rozhodnutí a přesuňte databázi do jiné situaci.

Databáze s více tenanty a jeden tenantů jsou kombinované ve stejné aplikaci SaaS, optimalizovat náklady nebo izolace pro každého tenanta.

   ![Aplikace horizontálně dělené databázi s více tenanty pomocí katalogu tenanta](media/saas-multitenantdb-provision-and-catalog/MultiTenantCatalog.png)

## <a name="tenant-catalog-pattern"></a>Vzor katalogu tenanta

Až budete mít dvě nebo více databází, že každý obsahuje minimálně jednoho tenanta, aplikace musí mít způsob, jak zjistit, které databáze ukládá tenanta aktuální zájmu. Databáze katalogu ukládá toto mapování.

#### <a name="tenant-key"></a>klíč tenanta

Pro každého klienta můžete aplikaci Wingtip odvozovat jedinečný klíč, který je klíč tenanta. Aplikace extrahuje název tenanta z adresy URL webové stránky. Aplikace vytvoří hodnotu hash název, který má získat klíč. Aplikace používá klíč pro přístup ke katalogu. Katalog křížové odkazy na informace o databázi, ve kterém je uložen tenanta. Aplikace používá informace o databázi připojit. Můžete také použít jiná schémata klíče tenanta.

Použití katalogu umožňuje název nebo umístění databáze tenanta se změnit po zřízení pravidla bez narušení běžného aplikace. V databázi s více tenanty model obsáhne katalogu přesun tenanta mezi databázemi.

#### <a name="tenant-metadata-beyond-location"></a>Metadata tenanta nad rámec umístění

V katalogu lze také určit, jestli je klient v režimu offline za účelem údržby nebo jiné akce. A je možné rozšířit katalog pro uložení další tenanta nebo metadata databáze, jako je například následující položky:
- Úroveň služby nebo edice databáze.
- Verze schématu databáze.
- Název klienta a jeho SLA (smlouva o úrovni služeb).
- Informace, které umožní správu aplikací, zákaznická podpora nebo procesů devops.  

Katalogu je také možné povolit generování sestav, Správa schématu mezi tenanty a extrahovat data pro účely analýzy. 

### <a name="elastic-database-client-library"></a>Klientská knihovna Elastic Database 

Ve službě Wingtip, katalogu implementovaná v *tenantcatalog* databáze. *Tenantcatalog* je vytvořený pomocí funkcí správy horizontálních oddílů [knihovna klienta elastické databáze (EDCL)](sql-database-elastic-database-client-library.md). Umožňuje aplikaci vytvářet, spravovat a použití knihovny *mapy horizontálních oddílů* , která je uložena v databázi. Mapy horizontálních oddílů křížové odkazy klíče tenanta s jeho části, to znamená její horizontálně dělené databáze.

Během zřizování klientů EDCL funkcí lze z aplikace nebo skripty prostředí PowerShell k vytvoření položky v objektu map horizontálních oddílů. Později EDCL funkcí je možné se připojit ke správné databázi. EDCL ukládá do mezipaměti informace o připojení k minimalizovat provoz v databázi katalogu a zrychlení procesu připojování.

> [!IMPORTANT]
> Proveďte *není* upravovat data v databázi katalogu prostřednictvím přímého přístupu! Kvůli vysokému riziku poškození dat nepodporuje přímé aktualizace. Místo toho upravte mapování dat pomocí rozhraní API EDCL pouze.

## <a name="tenant-provisioning-pattern"></a>Vzor zřizování tenantů

#### <a name="checklist"></a>Kontrolní seznam

Pokud chcete zřízení nového tenanta do existující sdílené databáze, sdílené databáze, musí odpovědět na tyto otázky:
- Nemá dostatek místa pro nového klienta?
- Nemá tabulky s nezbytné referenční data pro nového klienta, nebo můžete přidat data?
- Nemá odpovídající variantu základní schéma pro nového klienta?
- Je v příslušné zeměpisné oblasti blízko nového tenanta?
- Je na úroveň požadovanou službu pro nového klienta?

Pokud chcete tohoto nového tenanta můžete izolovali ve vlastní databázi, můžete vytvořit tak, aby vyhovovala specifikace pro příslušného tenanta.

Jakmile bude zřizování dokončeno, je nutné zaregistrovat tenanta v katalogu. Nakonec mapování tenanta můžete přidat tak, aby odkazovaly odpovídajících horizontálních oddílů.

#### <a name="template-database"></a>Nevyužije šablonu databáze

Zřízení databáze provádění skriptů SQL, nasazení souboru bacpac a kopírování databáze šablony. Aplikace Wingtip kopírování databáze šablony k vytvoření nových databází tenantů.

Jako každá aplikace Wingtip bude časem vyvíjet. V některých případech Wingtip bude vyžadovat změny v databázi. Změny mohou zahrnovat následující položky:
- Schéma nové nebo změněné.
- Nové nebo změněné referenční data.
- Úlohy údržby databáze rutinní zajistit optimální výkon aplikace.

U SaaS aplikace musí být tyto změny nasazeny koordinovaně u potenciálně velkého počtu klientských databází. Tyto změny se v budoucích databází tenantů musí být zahrnut do procesu zřizování. Prozkoumat tento problém v [kurzu Správa schématu](saas-tenancy-schema-management.md).

#### <a name="scripts"></a>Scripts

Skripty zřizování tenantů v tomto kurzu podporovat obě z následujících scénářů:
- Zřizování klienta do existující databáze sdílené s jinými tenanty.
- Zřizování klienta do jeho vlastní databáze.

Data tenanta je pak inicializován a zaregistrované v katalogu mapy horizontálních oddílů. V ukázkové aplikaci, databáze, které obsahují víc klientů jsou uvedeny obecný název, jako například *tenants1* nebo *tenants2*. Databáze, které obsahují jednoho tenanta se přiřadí název vašeho tenanta. Konkrétní pojmenování konvencemi použitými v ukázce nejsou zásadní součástí tohoto vzoru, protože umožňuje použít katalog libovolný název, který má být přiřazena k databázi.  

<a name="goto_1_tutorial"/>

## <a name="tutorial-begins"></a>Zahájení kurzu

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Zřizování tenanta do databáze více tenanty
> * Zřizování tenanta do databáze s jedním tenantem
> * Zřízení dávky tenantů do databáze více tenantů a jednoho tenanta
> * Registrovat databáze a mapování v katalogu tenanta

#### <a name="prerequisites"></a>Požadavky

Předpokladem dokončení tohoto kurzu je splnění následujících požadavků:

- Prostředí Azure PowerShell je nainstalované. Podrobnosti najdete v článku [Začínáme s prostředím Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

- Databázi s více tenanty SaaS aplikace Wingtip Tickets aplikace je nasazená. Nasazení za méně než pět minut najdete v tématu [nasazení a zkoumání aplikace Wingtip Tickets SaaS víceklientskou databází](saas-multitenantdb-get-started-deploy.md)

- Získání skriptů aplikace Wingtip a zdrojový kód:
    - Jsou k dispozici v databázi s více tenanty SaaS aplikace Wingtip Tickets skripty a zdrojový kód aplikace [WingtipTicketsSaaS MultitenantDB](https://github.com/microsoft/WingtipTicketsSaaS-MultiTenantDB) úložiště GitHub.
    - Zobrazit [obecné pokyny](saas-tenancy-wingtip-app-guidance-tips.md) pokyny ke stažení a odblokování skriptů aplikace Wingtip. 

## <a name="provision-a-tenant-into-a-database-shared-with-other-tenants"></a>Zřízení databáze tenanta *sdílené* s jinými tenanty

V této části se zobrazí seznam hlavní akce pro zřizování, které se provádějí pomocí skriptů prostředí PowerShell. Pak použijete PowerShell ISE ladicí program na procházení skriptů zobrazíte akce v kódu.

#### <a name="major-actions-of-provisioning"></a>Hlavní akce zřizování

Toto jsou klíčové prvky pracovního postupu zřizování Procházet:

- **Vypočítejte klíč nového tenanta**: funkce hash se používá k vytvoření klíče tenanta z názvu tenanta.
- **Zkontrolujte, jestli klíč tenanta už existuje**: katalog zajistit klíč už registrovaný.
- **Inicializace klienta v databázi tenanta výchozí**: databáze tenanta je aktualizováno za účelem přidání nové informace o tenantovi.  
- **Registrace klienta v katalogu**: mapování mezi nového klíče tenanta a existující databázi tenants1 je přidaných do katalogu. 
- **Přidání názvu tenanta do tabulky katalog rozšíření**: název místa se přidá do tabulky Tenantů v katalogu.  Toto přidání ukazuje, jak je možné rozšířit databáze katalogu pro podporu dalších dat specifické pro aplikaci.
- **Otevřete stránku události pro nového klienta**: *Bushwillow Blues* události stránka se otevře v prohlížeči.

   ![stránka events](media/saas-multitenantdb-provision-and-catalog/bushwillow.png)

#### <a name="debugger-steps"></a>Ladicí program

Abyste pochopili, jak aplikaci Wingtip implementované zřizování ve sdílené databázi nového tenanta, přidejte přerušení a projděte si pracovní postup:

1. V *prostředí PowerShell ISE*, otevřete... \\Learning Modules\\ProvisionTenants\\*Demo-ProvisionTenants.ps1* a nastavit následující parametry:
   - **$TenantName** = **Bushwillow Blues**, název nového místa.
   - **$VenueType** = **blues**, jeden předdefinovaných typů místa: blues, classicalmusic, dance, jazz, judo, motorracing, multipurpose, opera, rockmusic, soccer (malá, bez mezer).
   - **$DemoScenario** = **1**, zřídit klienta v databázi sdílené s jinými tenanty.

2. Přidejte zarážku vložením ukazatel myši kamkoli na řádku 38, řádek, který říká: *New-Tenant '* a potom stiskněte klávesu **F9**.

   ![přerušení](media/saas-multitenantdb-provision-and-catalog/breakpoint.png)

3. Spuštění skriptu stisknutím kombinace kláves **F5**.

4. Po spuštění skriptu se zastaví na zarážce, stiskněte klávesu **F11** k krokování s vnořením do kódu.

   ![ladit](media/saas-multitenantdb-provision-and-catalog/debug.png)

5. Trasování provádění skriptu pomocí **ladění** možnosti nabídky **F10** a **F11**, Krokovat přes nebo do volané funkce.

Další informace o ladění skriptů Powershellu najdete v tématu [tipy pro práci s a ladění skriptů Powershellu](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise).

## <a name="provision-a-tenant-in-its-own-database"></a>Zřízení tenanta v jeho *vlastní* databáze

#### <a name="major-actions-of-provisioning"></a>Hlavní akce zřizování

Toto jsou klíčové prvky, které projdete při trasování skriptu pracovního postupu:

- **Vypočítejte klíč nového tenanta**: funkce hash se používá k vytvoření klíče tenanta z názvu tenanta.
- **Zkontrolujte, jestli klíč tenanta už existuje**: katalog zajistit klíč už registrovaný.
- **Vytvoření nové databáze tenanta**: databáze se vytvoří tak, že zkopírujete *basetenantdb* databáze pomocí šablony Resource Manageru.  Nový název databáze je založen na název vašeho tenanta.
- **Přidání databáze do katalogu**: nové databáze tenanta se zaregistruje jako horizontálních oddílů v katalogu.
- **Inicializace klienta v databázi tenanta výchozí**: databáze tenanta je aktualizováno za účelem přidání nové informace o tenantovi.  
- **Registrace klienta v katalogu**: mapování mezi nového klíče tenanta a *sequoiasoccer* databáze je přidaných do katalogu.
- **Název tenanta je přidaných do katalogu**: název místa se přidá do tabulky rozšíření Tenantů v katalogu.
- **Otevřete stránku události pro nového klienta**: *Sequoia Soccer* události stránka se otevře v prohlížeči.

   ![stránka events](media/saas-multitenantdb-provision-and-catalog/sequoiasoccer.png)

#### <a name="debugger-steps"></a>Ladicí program

Teď provede procesem skriptu při vytváření klienta ve vlastní databázi:

1. Pořád ještě v... \\Learning Modules\\ProvisionTenants\\*Demo-ProvisionTenants.ps1* nastavit následující parametry:
   - **$TenantName** = **sequoia fotbalového**, název nového místa.
   - **$VenueType** = **fotbalového**, jeden předdefinovaných typů místa: blues, classicalmusic, dance, jazz, judo, motorracing, multipurpose, opera, rockmusic, soccer (malými písmeny, bez mezer).
   - **$DemoScenario** = **2**, zřídit samostatné databáze tenanta.

2. Přidat novou zarážku vložením ukazatel myši kamkoli na řádku 57, řádek, který říká:  *& &nbsp;$PSScriptRoot\New-TenantAndDatabase "* a stiskněte klávesu **F9**.

   ![přerušení](media/saas-multitenantdb-provision-and-catalog/breakpoint2.png)

3. Spuštění skriptu stisknutím kombinace kláves **F5**.

4. Po spuštění skriptu se zastaví na zarážce, stiskněte klávesu **F11** k krokování s vnořením do kódu.  Použití **F10** a **F11** Krokovat přes a můžete krokovat s vnořením funkce pro sledování spuštění.

## <a name="provision-a-batch-of-tenants"></a>Zřízení dávky tenantů

V tomto cvičení zřídíte dávku 17 tenantů. Doporučuje se že před spuštěním další kurzy Wingtip Tickets, takže existují další databáze pro práci s zřídíte tuto dávku tenantů.

1. V *prostředí PowerShell ISE*, otevřete... \\Learning Modules\\ProvisionTenants\\*Demo-ProvisionTenants.ps1* a změnit *$DemoScenario* parametr 4:
   - **$DemoScenario** = **4**, ke zřízení dávky tenantů do sdílené databáze.

2. Stiskněte **F5** a spusťte skript.

### <a name="verify-the-deployed-set-of-tenants"></a>Ověřit nasazenou sadu tenantů 

V této fázi budete mít kombinaci tenantů nasadí do sdílené databáze a nasadí do svých vlastních databází tenantů. Na webu Azure portal slouží ke kontrole vytvořené databáze. V [webu Azure portal](https://portal.azure.com), otevřete **tenants1-mt –\<uživatele\>**  server tak, že přejdete do seznamu serverů SQL Server.  **Databází SQL** seznam by měl obsahovat sdílený **tenants1** databáze a databáze pro tenanty, které jsou v jejich vlastní databázi:

   ![seznam databází](media/saas-multitenantdb-provision-and-catalog/Databases.png)

Na webu Azure portal se zobrazí databáze tenanta, neumožňuje klientům v tématu *uvnitř* sdílenou databázi. Úplný seznam tenanti si můžete prohlédnout ve **Centrum akcí** webovou stránku aplikace Wingtip a tak, že přejdete do katalogu.

#### <a name="using-wingtip-tickets-events-hub-page"></a>Pomocí aplikace Wingtip Tickets stránka centra událostí

Otevřete Centrum akcí stránku v prohlížeči (http:events.wingtip-mt.\<uživatele\>. trafficmanager.net)  

#### <a name="using-catalog-database"></a>Pomocí databáze katalogů

Úplný seznam klientů a odpovídající databáze pro každou je k dispozici v katalogu. Zobrazení SQL je za předpokladu, že spojení tenanta name na název databáze. Zobrazení krásně ukazuje, jakou hodnotu rozšíření metadata, která je uložena v katalogu.
- Je zobrazení SQL dostupných v databázi tenantcatalog.
- Název tenanta uložená v tabulce Tenantů.
- Název databáze se ukládají do tabulek Shard Management.

1. V SQL Server Management Studio (SSMS), připojte se k serveru tenantů v **katalogu pro mt.\<uživatele\>. database.windows.net**, přihlášení = **developer**a heslo = **P@ssword1**

    ![Dialogové okno připojení aplikace SSMS](media/saas-multitenantdb-provision-and-catalog/SSMSConnection.png)

2. V Průzkumníku objektů aplikace SSMS přejděte do zobrazení v *tenantcatalog* databáze.

3. Klikněte pravým tlačítkem na zobrazení *TenantsExtended* a zvolte **vybrat prvních 1000 řádků**. Všimněte si mapování mezi název tenanta a databáze pro různé tenanty.

    ![ExtendedTenants zobrazení v aplikaci SSMS](media/saas-multitenantdb-provision-and-catalog/extendedtenantsview.png)
      
## <a name="other-provisioning-patterns"></a>Další způsoby zřizování

Tato část popisuje další zajímavé způsoby zřizování.

#### <a name="pre-provisioning-databases-in-elastic-pools"></a>Předběžným zřizováním databází v elastických fondech

Předem zřizovaných způsob využívá skutečnost, že při použití elastických fondů, fakturace není pro fond databází. Databáze je proto přidat do elastického fondu předtím, než se v případě potřeby zapíná bez dalších nákladů na dalších poplatků. Tato předem Provisioning výrazně snižuje čas potřebný ke zřízení tenanta do databáze. Počet předběžně zřizovaných databází je možné upravit tak, aby měli k předpokládanému tempu zřizování tenantů.

#### <a name="auto-provisioning"></a>Automatické zřizování

V automatickém zřizování odešle vyhrazená zřizovací služba se používá ke zřízení serverů, fondů a databází automaticky podle potřeby. Tato automatizace obsahuje předběžným zřizováním databází v elastických fondech. A pokud jsou vyřazeny z provozu databáze a odstranit, můžete zadáním mezery, které tím se vytvoří v elastických fondech zřizovací služba podle potřeby.

Tento typ automatizované služby může být jednoduché nebo složité. Například automatizace dokáže zpracovat zřizování v různých geografických oblastech a můžou nastavit geografickou replikaci pro zotavení po havárii. Se vzorkem automatického zřizování by klientská aplikace nebo skript odeslat žádost o zřízení do fronty pro zpracování služby zřizování. Skript by pak dotazovat na dokončení zjišťování. Pokud použití předběžného zřizování požadavky rychle zpracovává zatímco na pozadí služby spravovat zřizování náhradní databáze.

## <a name="additional-resources"></a>Další zdroje informací:

<!-- - Additional [tutorials that build upon the Wingtip SaaS application](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)-->
- [Klientská knihovna Elastic Database](sql-database-elastic-database-client-library.md)
- [Ladění skriptů v integrovaném skriptovacím prostředí (ISE) v prostředí Windows PowerShell](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise)


## <a name="next-steps"></a>Další postup

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Zřídit jednoho nového tenanta do sdílené víceklientských databáze a svou vlastní databázi
> * Zřídit dávku dalších tenantů.
> * Projít podrobnosti o zřizování tenantů a zaregistrujete je do katalogu

Zkuste [kurz o monitorování výkonu](saas-multitenantdb-performance-monitoring.md).

