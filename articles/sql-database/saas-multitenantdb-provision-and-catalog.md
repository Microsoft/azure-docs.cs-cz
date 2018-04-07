---
title: Zřídit v SaaS víceklientské Azure | Microsoft Docs
description: Zjistěte, jak zřídit a nové klienty v aplikaci SaaS víceklientské Azure SQL Database v katalogu
keywords: kurz k sql database
services: sql-database
author: MightyPen
manager: craigg
ms.reviewer: billgib;andrela;genemi
ms.service: sql-database
ms.custom: saas apps
ms.topic: article
ms.date: 04/01/2018
ms.author: billgib
ms.openlocfilehash: cb0229731d8fd1495173915b3bc95b48538f1e85
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="provision-and-catalog-new-tenants-in-a-saas-application-using-a-sharded-multi-tenant-azure-sql-database"></a>Zřizování a katalog novým tenantům v aplikaci SaaS pomocí horizontálně dělené víceklientské databázi Azure SQL

Tento článek se zabývá zřizování a katalogizaci nové klienty v *horizontálně dělené databáze víceklientské* modelu nebo vzor.

Tento článek má dvě hlavní části:

- [Koncepční diskusní](#goto_2_conceptual) zajišťování a nové klienty do katalogu.

- [Kurz](#goto_1_tutorial) který označuje kód skriptu prostředí PowerShell, který provede zřizování a do katalogu.
    - Tento kurz používá aplikace SaaS lístky Wingtip přizpůsobit vzoru víceklientské horizontálně dělené databáze.

<a name="goto_2_conceptual"/>

## <a name="database-pattern"></a>Vzor databáze

V této části, a navíc několik více prostředků, postupujte podle, popisují koncepty vzoru víceklientské horizontálně dělené databáze.

V tomto modelu horizontálně dělené víceklientské obsahují schémata tabulek uvnitř každou databázi klíč klienta v primární klíč tabulky, které ukládají data klientů. Klíč tenanta umožňuje každé jednotlivé databázi pro ukládání 0, 1 nebo velký počet klientů. Použití horizontálně dělené databází usnadňuje aplikace systému pro podporu velký počet klientů. Veškerá data pro všechny jednoho klienta je uložena v jedné databáze. Velký počet klientů, které jsou distribuovány mezi mnoha databázemi horizontálně dělené. Databáze katalogu ukládá mapování každého klienta do své databáze.

#### <a name="isolation-versus-lower-cost"></a>Izolace a nižší náklady

Klienta, který má databáze na sebe sama požívá výhod izolace. Klient může mít databázi obnovit do předchozího stavu, bez se omezený dopad na ostatních klientů. Výkon databáze může být nastaven na optimalizaci pro jednoho klienta, znovu bez nutnosti ohrozit s jinými klienty. Problém je, že izolace stojí více než nákladů na databázi sdílet s jinými klienty.

Při zřízení nového klienta do databáze ji můžete sdílet s jinými klienty nebo může být umístěn do své vlastní nové databáze. Můžete později rozmyslíte a přesunout databáze do jiné situaci.

Databáze s více klienty a jeden klientů jsou kombinované ve stejné aplikaci SaaS, chcete-li optimalizovat náklady nebo izolace pro každého klienta.

   ![Horizontálně dělené databáze víceklientské aplikace pomocí katalogu klienta](media/saas-multitenantdb-provision-and-catalog/MultiTenantCatalog.png)

## <a name="tenant-catalog-pattern"></a>Vzor katalogu klienta

Až budete mít dvě nebo více databází, že každý obsahovat alespoň jeden klienta, musí mít způsob, jak zjistit, které databáze ukládá klienta týkající se aktuální aplikace. Databáze katalogu ukládá toto mapování.

#### <a name="tenant-key"></a>Klíč tenanta

Pro každého klienta můžete aplikaci Wingtip odvozovat jedinečný klíč, který je klíč klienta. Aplikace extrahuje název klienta z adresy URL pro webovou stránku. Aplikace vytvoří hodnotu hash název k získání klíče. Aplikace používá klíč přístup ke katalogu. Křížové odkazy katalogu se informace o databázi, ve kterém je uložený klienta. Aplikace používá informace o databázi připojit. Můžete také použít jiná schémata klíče klienta.

Použití katalog umožňuje název nebo umístění databáze klienta se musí změnit po zřízení bez přerušení aplikace. V modelu databáze více klientů může katalogu vyrovnávat přesun klienta mezi databází.

#### <a name="tenant-metadata-beyond-location"></a>Metadata klienta nad rámec umístění

Katalog můžete také určit, zda klient je offline z důvodu údržby nebo jiné akce. A katalogu lze rozšířit pro uložení další klienta nebo metadata databáze, jako je například následující položky:
- Úroveň služby nebo edici databáze.
- Verze schématu databáze.
- Název klienta a jeho SLA (smlouvu o úrovni služeb).
- Informace, chcete-li povolit správu aplikací, na zákaznickou podporu nebo devops procesy.  

Katalog lze použít také k povolení správy schématu sestav, mezi klienta a extrahování dat pro účely analýzy. 

### <a name="elastic-database-client-library"></a>Klientská knihovna Elastic Database 

V Wingtip, katalogu je implementována ve *tenantcatalog* databáze. *Tenantcatalog* je vytvořený pomocí funkce správy horizontálního oddílu [elastické databáze klienta knihovny (EDCL)](sql-database-elastic-database-client-library.md). Umožňuje aplikaci vytvářet, spravovat a používat knihovny *mapování horizontálních* uloženy v databázi. Mapování horizontálních křížové odkazy klíč klienta s jeho horizontálních znamená jeho horizontálně dělené databáze.

Při zřizování klienta EDCL funkcí lze z aplikace nebo skripty prostředí PowerShell k vytvoření položky v mapě horizontálního oddílu. Později EDCL funkce slouží k připojení k databázi správné. EDCL ukládá do mezipaměti informace o připojení k minimalizovat provoz na databázi katalogu a urychlit proces připojení.

> [!IMPORTANT]
> Proveďte *není* upravovat data v databázi katalogu prostřednictvím přímého přístupu! Přímá aktualizace nejsou podporovány z důvodu vysoké riziko poškození dat. Místo toho upravte mapování dat pouze pomocí rozhraní API EDCL.

## <a name="tenant-provisioning-pattern"></a>Vzor zřizování klienta

#### <a name="checklist"></a>Kontrolní seznam

Pokud chcete zřídit nového klienta do existující sdílené databáze, sdílené databáze, musí odpovědět na tyto otázky:
- Nemusí dostatek místa pro nového klienta?
- Nemusí tabulky s nezbytné referenčních dat pro nového klienta, nebo lze přidat data?
- Nemusí jeho odpovídající variantu základní schéma pro nového klienta?
- Je v příslušné zeměpisné polohy blíží k novému klientovi?
- Je na vrstvě správné služeb pro nového klienta?

Když chcete k novému klientovi izolaci ve vlastní databázi, můžete vytvořit tak, aby vyhovovala specifikace pro klienta.

Po dokončení zřizování je nutné zaregistrovat klienta v katalogu. Nakonec klient mapování lze přidat tak, aby odkazovaly odpovídající ID horizontálního oddílu.

#### <a name="template-database"></a>Šablona databáze

Poskytnutí databáze provádění skripty SQL, nasazení souboru bacpac nebo kopírování šablony databáze. Aplikace pro adresář Wingtip zkopírujte databázi šablony k vytvoření nové databáze klienta.

Podobně jako všechny aplikace bude Wingtip momentální v čase. V některých případech Wingtip bude vyžadovat změny databáze. Změny mohou zahrnovat následující položky:
- Schéma nové nebo změněné.
- Nové nebo změněné referenční data.
- Úlohy údržby databáze běžné zajistit optimální výkon.

U SaaS aplikace musí být tyto změny nasazeny koordinovaně u potenciálně velkého počtu klientských databází. Pro tyto změny se v budoucnu klienta databáze musí být součástí procesu zřizování. Tento problém je prozkoumali další v [schématu správu kurzu](saas-tenancy-schema-management.md).

#### <a name="scripts"></a>Scripts

Zřizování skripty klienta v tomto kurzu podporují obě z následujících scénářů:
- Zřizování klienta do existující databázi sdílet s jinými klienty.
- Zřizování klienta do svou vlastní databázi.

Klienta data se pak inicializovat a registrované v mapě horizontálního oddílu katalogu. V ukázkové aplikace, databáze, které obsahují víc klientů mají obecný název, například *tenants1* nebo *tenants2*. Databáze, které obsahují jednoho klienta jsou uvedeny název klienta. Konkrétní zásady vytváření názvů používá v ukázce nejsou důležitou součástí vzor, podle použití katalogu umožňuje libovolný název a přiřadit k databázi.  

<a name="goto_1_tutorial"/>

## <a name="tutorial-begins"></a>Zahájení kurzu

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Přidělení klienta do databáze více klientů
> * Přidělení klienta do jednoho klienta databáze
> * Zřídit dávky klienty do databáze více klientů a jednoho klienta
> * Registrovat databáze a klienta mapování v katalogu

#### <a name="prerequisites"></a>Požadavky

Předpokladem dokončení tohoto kurzu je splnění následujících požadavků:

- Prostředí Azure PowerShell je nainstalované. Podrobnosti najdete v článku [Začínáme s prostředím Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

- Nasazení aplikace Wingtip lístky SaaS víceklientské databáze. Nasazení za méně než pět minut najdete v tématu [nasazení a seznamte se s aplikací Wingtip lístky SaaS víceklientské databáze](saas-multitenantdb-get-started-deploy.md)

- Získáte adresář Wingtip skripty a zdrojového kódu:
    - Adresář Wingtip lístky SaaS víceklientské databázové skripty a zdrojový kód aplikace, které jsou k dispozici v [WingtipTicketsSaaS MultitenantDB](https://github.com/microsoft/WingtipTicketsSaaS-MultiTenantDB) úložiště GitHub.
    - Najdete v článku [obecné pokyny](saas-tenancy-wingtip-app-guidance-tips.md) kroky, jak stáhnout a odblokování Wingtip skripty. 

## <a name="provision-a-tenant-into-a-database-shared-with-other-tenants"></a>Přidělení klienta do databáze *sdílené* s jinými klienty

V této části najdete seznam hlavní akce pro zřizování, které se provádějí pomocí skriptů prostředí PowerShell. Pak použijete PowerShell ISE ladicí program k krok prostřednictvím skriptů zobrazíte akce v kódu.

#### <a name="major-actions-of-provisioning"></a>Hlavní akce zřizování

Toto jsou klíčové prvky zřizování pracovního postupu, které v průběhu:

- **Vypočítat nového klíče tenanta**: funkce hash se používá k vytvoření klíče klienta z název klienta.
- **Zkontrolujte, jestli klíč klienta již existuje**: katalog bude zkontrolován klíč již zaregistrován.
- **Inicializace klienta v databázi klienta výchozí**: databázi klienta je aktualizováno za účelem přidání nové informace o klientovi.  
- **Registrace klienta v katalogu**: mapování mezi nový klíč klienta a stávající databázi tenants1 je přidaných do katalogu. 
- **Přidejte název klienta do tabulky rozšíření katalogu**: název místo se přidá do tabulky klienty v katalogu.  Přidání popisuje, jak lze rozšířit databázi katalogu pro podporu dalších dat specifické pro aplikaci.
- **Otevřete stránku události pro nového klienta**: *Bushwillow modré* události stránka se otevře v prohlížeči.

   ![stránka events](media/saas-multitenantdb-provision-and-catalog/bushwillow.png)

#### <a name="debugger-steps"></a>Ladicí program

Abyste pochopili, jak aplikace Wingtip implementuje zřizování ve sdílené databázi nového klienta, přidejte zarážek a krok v pracovním postupu:

1. V *prostředí PowerShell ISE*, otevřete... \\Learning moduly\\ProvisionTenants\\*ukázku ProvisionTenants.ps1* a nastavit následující parametry:
   - **$TenantName** = **Bushwillow modré**, název nové místo.
   - **$VenueType** = **modré**, jeden z typů předdefinovaného místo: modré, classicalmusic, tance, jazz, judo, motorracing víceúčelových, opera, rockmusic, fotbalový (malá, bez mezer).
   - **$DemoScenario** = **1**, zřídit klienta v databázi sdílené s jinými klienty.

2. Přidejte zarážku umístěním kurzoru kdekoli v řádku 38, řádek, která uvádí, že: *nového klienta,*a potom stiskněte klávesu **F9**.

   ![přerušení](media/saas-multitenantdb-provision-and-catalog/breakpoint.png)

3. Spusťte skript stisknutím **F5**.

4. Po spuštění skriptu se zastaví u zarážky, stiskněte klávesu **F11** ke kroku do kódu.

   ![ladit](media/saas-multitenantdb-provision-and-catalog/debug.png)

5. Trasování spuštění skriptu na pomocí **ladění** možnosti nabídky, **F10** a **F11**, aby krok přes nebo do volaných funkcí.

Další informace o ladění skriptů prostředí PowerShell najdete v tématu [tipy k ladění skriptů prostředí PowerShell a práce s](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise).

## <a name="provision-a-tenant-in-its-own-database"></a>Zřídit klienta v jeho *vlastní* databáze

#### <a name="major-actions-of-provisioning"></a>Hlavní akce zřizování

Toto jsou klíčové prvky, které jednotlivé kroky při trasování skriptu pracovního postupu:

- **Vypočítat nového klíče tenanta**: funkce hash se používá k vytvoření klíče klienta z název klienta.
- **Zkontrolujte, jestli klíč klienta již existuje**: katalog bude zkontrolován klíč již zaregistrován.
- **Vytvoření nové databáze klienta**: databáze je vytvořená tak, že zkopírujete *basetenantdb* databáze pomocí šablony Resource Manageru.  Nový název databáze je založena na název klienta.
- **Přidat databáze do katalogu**: nové databáze klienta se zaregistruje jako horizontálního oddílu v katalogu.
- **Inicializace klienta v databázi klienta výchozí**: databázi klienta je aktualizováno za účelem přidání nové informace o klientovi.  
- **Registrace klienta v katalogu**: mapování mezi nový klíč klienta a *sequoiasoccer* databáze je přidaných do katalogu.
- **Název klienta se přidá do katalogu**: název místo se přidá do tabulky rozšíření klientů v katalogu.
- **Otevřete stránku události pro nového klienta**: *Sequoia fotbalový* události stránka se otevře v prohlížeči.

   ![stránka events](media/saas-multitenantdb-provision-and-catalog/sequoiasoccer.png)

#### <a name="debugger-steps"></a>Ladicí program

Nyní provede proces skriptu při vytváření klienta ve vlastní databázi:

1. Pořád ještě v... \\Learning moduly\\ProvisionTenants\\*ukázku ProvisionTenants.ps1* nastavit následující parametry:
   - **$TenantName** = **sequoia fotbalový**, název nové místo.
   - **$VenueType** = **fotbalový**, jeden z typů předdefinovaného místo: modré, classicalmusic, tance, jazz, judo, motorracing víceúčelových, opera, rockmusic, fotbalový (malá písmena, bez mezer).
   - **$DemoScenario** = **2**, ke zřízení klienta jako svou vlastní databázi.

2. Přidejte novou zarážku umístěním kurzoru kdekoli v řádku 57, řádek, která uvádí, že:  *& &nbsp;$PSScriptRoot\New-TenantAndDatabase '*a stiskněte klávesu **F9**.

   ![přerušení](media/saas-multitenantdb-provision-and-catalog/breakpoint2.png)

3. Spusťte skript stisknutím **F5**.

4. Po spuštění skriptu se zastaví u zarážky, stiskněte klávesu **F11** ke kroku do kódu.  Použití **F10** a **F11** krok přes a zanořte se do funkce pro sledování spuštění.

## <a name="provision-a-batch-of-tenants"></a>Zřídit dávky klientů

Toto cvičení zřídí dávky 17 klientů. Doporučuje se, že zřídit tuto dávku klienty před zahájením dalších kurzech Wingtip lístků, proto nejsou další databáze pro práci s.

1. V *prostředí PowerShell ISE*, otevřete... \\Learning moduly\\ProvisionTenants\\*ukázku ProvisionTenants.ps1* a změňte *$DemoScenario* parametr 4:
   - **$DemoScenario** = **4**, ke zřízení dávky klienty do sdílené databáze.

2. Stiskněte **F5** a spusťte skript.

### <a name="verify-the-deployed-set-of-tenants"></a>Ověřte sadu nasazených klientů 

V této fázi mít směs klienty nasadit do sdílené databáze a klienty nasazený do vlastní databáze. Portál Azure slouží ke kontrole databáze vytvořené. V [portál Azure](https://portal.azure.com), otevřete **tenants1-mt -\<uživatele\>**  procházením seznamu serverů SQL server.  **Databází SQL** seznamu by měla obsahovat sdílený **tenants1** databáze a databáze pro klienty, kteří jsou v jejich vlastní databázi:

   ![seznam databází](media/saas-multitenantdb-provision-and-catalog/Databases.png)

A portálu Azure se zobrazí klienta databáze, neumožňuje najdete v části klienty *uvnitř* sdílenou databázi. Úplný seznam klientů, které si můžete prohlédnout ve **události rozbočovače** webová stránka Wingtip a procházením katalogu.

#### <a name="using-wingtip-tickets-events-hub-page"></a>Pomocí stránky centra událostí Wingtip lístky

Otevřete Centrum událostí stránku v prohlížeči (http:events.wingtip-mt.\<uživatele\>. trafficmanager.net)  

#### <a name="using-catalog-database"></a>Pomocí katalogu databáze

Úplný seznam klientů a příslušné databáze pro každou je k dispozici v katalogu. Zobrazení SQL je za předpokladu, že spojení název klienta pro název databáze. Zobrazení přehledně znázorňuje hodnotu rozšíření metadata, která je uložené v katalogu.
- Je k dispozici v databázi tenantcatalog zobrazení SQL.
- Název klienta je uložené v tabulce klientů.
- Název databáze je ukládat do tabulek správy horizontálního oddílu.

1. V SQL Server Management Studio (SSMS) připojit k serveru klienty na **katalogu strojový překladů.\<uživatele\>. database.windows.net**, s přihlášením = **vývojáře**a heslo = **P@ssword1**

    ![Dialogové okno připojení aplikace SSMS](media/saas-multitenantdb-provision-and-catalog/SSMSConnection.png)

2. V Průzkumníku objektů aplikace SSMS, přejděte do zobrazení *tenantcatalog* databáze.

3. Klikněte pravým tlačítkem na zobrazení *TenantsExtended* a zvolte **vyberte Top 1000 řádky**. Poznamenejte si mapování mezi název klienta a databáze pro jiné klienty.

    ![Zobrazení ExtendedTenants v aplikaci SSMS](media/saas-multitenantdb-provision-and-catalog/extendedtenantsview.png)
      
## <a name="other-provisioning-patterns"></a>Další způsoby zřizování

Tato část pojednává o dalších zajímavých vzorců zřizování.

#### <a name="pre-provisioning-databases-in-elastic-pools"></a>Předem zřizování databází v elastické fondy

Předem zřizování vzor zneužije skutečnost, že při použití elastické fondy, fakturace je pro fond není databáze. Proto databáze může být přidán do fondu elastické databáze, než jsou potřeba aniž by docházelo k zpoplatněné. Tuto předem Provisioning výrazně zkracuje čas potřebný k přidělení klienta do databáze. Počet databází předem zřizovat lze upravit podle potřeby zachovat vhodný pro předpokládaného zřizování rychlost vyrovnávací paměti.

#### <a name="auto-provisioning"></a>Automatické zřizování

Ve vzoru automatické zřizování vyhrazené zřizování služby slouží ke zřízení servery, fondy a databáze automaticky podle potřeby. Tato automatizace obsahuje předem zajišťování databází v elastické fondy. A pokud databáze jsou vyřazena z provozu a odstranit, mezery, které tím se vytvoří v elastické fondy může být vyplněny zřizování služby podle potřeby.

Tento typ automatizované služby může být jednoduché nebo komplexní. Například automatizace může zpracovat zřizování napříč několika zeměpisných oblastí a může nastavit geografická replikace pro zotavení po havárii. S vzoru automatické zřizování by klientská aplikace nebo skriptu odešlete požadavek zřizování do fronty pro zpracování zřizování služby. Tento skript by pak dotazování ke zjištění dokončení. Pokud předem zřizování se používá, požadavky by zpracovávány rychle, zatímco služba pozadí by spravovat zřizování databázi nahrazení.

## <a name="additional-resources"></a>Další zdroje informací:

<!-- - Additional [tutorials that build upon the Wingtip SaaS application](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)-->
- [Klientská knihovna Elastic Database](sql-database-elastic-database-client-library.md)
- [Ladění skriptů v integrovaném skriptovacím prostředí (ISE) v prostředí Windows PowerShell](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise)


## <a name="next-steps"></a>Další postup

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Zřídit jednoho nového klienta do sdílené databáze více klientů a vlastní databáze
> * Zřídit dávku dalších tenantů.
> * Projděte podrobnosti o zřizování klientů a jejich registrace do katalogu

Zkuste [kurzu monitorování výkonu](saas-multitenantdb-performance-monitoring.md).

