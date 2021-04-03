---
title: Zřízení ve více tenantůch SaaS
description: Naučte se zřizovat a zařadit nové klienty do Azure SQL Database aplikace SaaS multi-tenant.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 09/24/2018
ms.openlocfilehash: eddb0c8339069025f0742e9bcbc371efbef094ee
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "92793326"
---
# <a name="provision-and-catalog-new-tenants-in-a-saas-application-using-a-sharded-multi-tenant-azure-sql-database"></a>Zřízení a katalog nových tenantů v aplikaci SaaS s využitím víceklientské Azure SQL Database horizontálně dělené
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Tento článek se zabývá zřizováním a katalogem nových tenantů v modelu *horizontálně dělené Database nebo modelu databáze pro více tenantů* .

Tento článek obsahuje dvě hlavní části:

- [Koncepční diskuze](#goto_2_conceptual) o zřizování a katalogu nových tenantů.

- [Kurz](#goto_1_tutorial) , který zvýrazní kód skriptu PowerShellu, který provádí zřizování a vytváření katalogu.
  - V tomto kurzu se používá aplikace Wingtip Tickets SaaS, která je přizpůsobená ke vzoru databáze pro více tenantů horizontálně dělené.

<a name="goto_2_conceptual"></a>

## <a name="database-pattern"></a>Vzor databáze

V této části se podíváme na koncepty modelu horizontálně dělené Database pro více tenantů.

V tomto horizontálně dělené modelu pro více tenantů zahrnuje schémata tabulek uvnitř každé databáze klíč tenanta v primárním klíči tabulek, které ukládají data tenanta. Klíč tenanta umožňuje, aby každá jednotlivé databáze ukládala 0, 1 nebo mnoho klientů. Použití databází horizontálně dělené usnadňuje systému aplikace podporu velmi velkého počtu klientů. Všechna data pro jednoho tenanta jsou uložená v jedné databázi. Velký počet klientů se distribuuje napříč mnoha horizontálně dělené databázemi. Databáze katalogu ukládá mapování každého tenanta do své databáze.

#### <a name="isolation-versus-lower-cost"></a>Izolace oproti nižším nákladům

Tenant, který má databázi samu sobě, má přednost před výhodami izolace. V tenantovi může být databáze obnovená na dřívější datum bez omezení dopadu na jiné klienty. Výkon databáze se dá vyladit tak, aby se optimalizoval jenom pro jednoho tenanta, a to bez nutnosti napadnout s ostatními klienty. Problémem je, že při sdílení databáze s jinými klienty se náklady na více než náklady na oddělení IT.

Když je zřízen nový tenant, může sdílet databázi s ostatními klienty nebo může být umístěn do své vlastní nové databáze. Později můžete změnit svůj názor a přesunout databázi do jiné situace.

Databáze s více klienty a jedenmi klienty se míchají ve stejné aplikaci SaaS a optimalizují náklady nebo izolaci pro každého tenanta.

   ![Aplikace pro více tenantů horizontálně dělené Database s katalogem tenantů](./media/saas-multitenantdb-provision-and-catalog/MultiTenantCatalog.png)

## <a name="tenant-catalog-pattern"></a>Vzor katalogu tenanta

Pokud máte dvě nebo více databází, které obsahují alespoň jeden tenant, aplikace musí mít způsob, jak zjistit, která databáze ukládá tenanta aktuálního zájmu. Databáze katalogu toto mapování ukládá.

#### <a name="tenant-key"></a>Klíč tenanta

Pro každého tenanta může aplikace Wingtip odvodit jedinečný klíč, což je klíč tenanta. Aplikace extrahuje název tenanta z adresy URL webové stránky. Aplikace vyhodnotí název, aby získala klíč. Aplikace používá klíč pro přístup ke katalogu. Katalog křížových odkazů na informace o databázi, ve které je klient uložený. Aplikace použije informace databáze k připojení. Lze také použít jiná schémata klíčů tenanta.

Použití katalogu umožňuje po zřízení změnit název nebo umístění databáze tenanta, aniž by došlo k přerušení aplikace. V modelu databáze s více klienty se katalog vejde na přesun tenanta mezi databázemi.

#### <a name="tenant-metadata-beyond-location"></a>Metadata tenanta za rámec umístění

Katalog také může označovat, zda je klient v režimu offline kvůli údržbě nebo jiným akcím. A katalog můžete rozšířit tak, aby ukládal další metadata tenanta nebo databáze, například následující položky:
- Vrstva služby nebo edice databáze.
- Verze schématu databáze
- Název tenanta a jeho SLA (smlouva o úrovni služeb).
- Informace, které umožňují správu aplikací, zákaznickou podporu nebo DevOps procesy.

Katalog je také možné použít k povolení generování sestav mezi klienty, správy schématu a extrakce dat pro účely analýzy.

### <a name="elastic-database-client-library"></a>Klientská knihovna Elastic Database

V Wingtip se katalog implementuje v databázi *tenantcatalog* . *Tenantcatalog* se vytvoří pomocí funkcí správy horizontálních oddílů [klientské knihovny nástroje elastic Database (EDCL)](elastic-database-client-library.md). Knihovna umožňuje aplikaci vytvořit, spravovat a použít *mapu horizontálních oddílů* , která je uložená v databázi. Mapa horizontálních oddílů křížově odkazuje na klíč tenanta s jeho horizontálních oddílů, což znamená jeho databázi horizontálně dělené.

Během zřizování tenanta je možné pomocí aplikací nebo skriptů PowerShellu vytvořit v mapě horizontálních oddílů funkce EDCL. Později lze funkce EDCL použít pro připojení ke správné databázi. EDCL ukládá informace o připojení do mezipaměti pro minimalizaci provozu databáze katalogu a urychlení procesu připojování.

> [!IMPORTANT]
> *Neupravujte* data v databázi katalogu pomocí přímého přístupu. Přímé aktualizace nejsou podporovány z důvodu vysokého rizika poškození dat. Místo toho upravte mapování dat pouze pomocí rozhraní EDCL API.

## <a name="tenant-provisioning-pattern"></a>Model zřizování tenanta

#### <a name="checklist"></a>Kontrolní seznam

Pokud chcete zřídit nového tenanta do existující sdílené databáze sdílené databáze, musíte požádat o následující otázky:
- Je pro nového tenanta k dispozici dostatek místa?
- Obsahuje tabulka s nezbytnými referenčními daty pro nového tenanta nebo je možné přidat data?
- Má odpovídající variace základního schématu pro nového tenanta?
- Je v příslušném geografickém umístění blízko nového tenanta?
- Je to u správné úrovně služby pro nového tenanta?

Pokud chcete, aby byl nový tenant izolovaný ve vlastní databázi, můžete ho vytvořit tak, aby splňoval specifikace pro tenanta.

Po dokončení zřizování musíte klienta zaregistrovat v katalogu. Nakonec můžete přidat mapování tenanta pro odkazování na příslušné horizontálních oddílů.

#### <a name="template-database"></a>Databáze šablon

Databázi zřiďte spuštěním skriptů SQL, nasazením BacPac nebo zkopírováním databáze šablony. Aplikace Wingtip Apps kopírují šablonu databáze pro vytvoření nových databází tenanta.

Podobně jako u jakékoli aplikace se společnost Wingtip v průběhu času vyvíjí. V případě potřeby bude společnost Wingtip vyžadovat změny v databázi. Změny mohou zahrnovat následující položky:
- Nové nebo změněné schéma.
- Nová nebo změněná referenční data.
- Rutinní úlohy údržby databáze k zajištění optimálního výkonu aplikace.

U SaaS aplikace musí být tyto změny nasazeny koordinovaně u potenciálně velkého počtu klientských databází. Aby se tyto změny projevily v budoucích databázích tenanta, musí být začleněny do procesu zřizování. Tato výzva se podrobněji prozkoumá v [kurzu správy schématu](saas-tenancy-schema-management.md).

#### <a name="scripts"></a>Skripty

Skripty zřizování klientů v tomto kurzu podporují oba tyto scénáře:
- Zřizování tenanta do existující databáze sdílené s ostatními klienty.
- Zřizování tenanta ve vlastní databázi.

Data tenanta se pak inicializují a zaregistrují v mapě horizontálních oddílů katalogu. V ukázkové aplikaci se k databázím, které obsahují více tenantů, přidává obecný název, jako je například *tenants1* nebo *tenants2*. K databázím, které obsahují jednoho tenanta, se přidává název tenanta. Konkrétní konvence pojmenování používané v ukázce nejsou důležitou součástí vzoru, protože použití katalogu umožňuje přiřadit k databázi libovolný název.

<a name="goto_1_tutorial"></a>

## <a name="tutorial-begins"></a>Začátek kurzu

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Zřízení tenanta v databázi s více klienty
> * Zřízení tenanta v databázi s jedním klientem
> * Zřízení dávky tenantů do databází s více klienty a s jedním tenantům
> * Registrace databáze a mapování tenanta v katalogu

#### <a name="prerequisites"></a>Požadavky

Předpokladem dokončení tohoto kurzu je splnění následujících požadavků:

- Je nainstalované prostředí Azure PowerShell. Podrobnosti najdete v článku [Začínáme s prostředím Azure PowerShell](/powershell/azure/get-started-azureps).

- Nasadí se aplikace SaaS pro víceklientské klienty. Nasazení za méně než pět minut najdete v tématu [nasazení a prozkoumání SaaS aplikace pro více tenantů](./saas-multitenantdb-get-started-deploy.md) .

- Získejte skripty a zdrojový kód pro společnost Wingtip:
    - V úložišti GitHubu [WingtipTicketsSaaS-MultitenantDB](https://github.com/microsoft/WingtipTicketsSaaS-MultiTenantDB) jsou k dispozici skripty SaaS s více klienty a zdrojový kód aplikace.
    - Pokyny ke stažení a odblokování skriptů Wingtip najdete v části [Obecné pokyny](saas-tenancy-wingtip-app-guidance-tips.md) .

## <a name="provision-a-tenant-into-a-database-shared-with-other-tenants"></a>Zřízení tenanta v databázi *sdílené* s ostatními klienty

V této části se zobrazí seznam hlavních akcí pro zřizování, které provádí skripty PowerShellu. Pak použijete ladicí program PowerShellu ISE ke krokování skriptů pro zobrazení akcí v kódu.

#### <a name="major-actions-of-provisioning"></a>Hlavní akce zřizování

Níže jsou uvedené klíčové prvky pracovního postupu zřizování, který provedete následujícím postupem:

- **Vypočítat nový klíč tenanta**: funkce hash se používá k vytvoření klíče tenanta z názvu tenanta.
- **Zkontrolujte, jestli klíč tenanta už existuje**: katalog se zkontroluje, aby se zajistilo, že klíč ještě není zaregistrovaný.
- **Inicializace tenanta ve výchozí databázi tenanta**: databáze tenanta se aktualizuje, aby se přidaly nové informace o tenantovi.
- **Registrovat tenanta v katalogu**: mapování mezi novým klíčem tenanta a stávající databází tenants1 se přidá do katalogu.
- **Přidejte název tenanta do tabulky přípon katalogu**: název místa se přidá do tabulky tenantů v katalogu.  V tomto dodatku se dozvíte, jak se dá databáze katalogu rozšířit, aby podporovala další data specifická pro danou aplikaci.
- **Otevřít stránku události pro nového tenanta**: stránka události *Blues Bushwillow* se otevře v prohlížeči.

   ![Snímek obrazovky, který zobrazuje stránku události pro nového tenanta.](./media/saas-multitenantdb-provision-and-catalog/bushwillow.png)

#### <a name="debugger-steps"></a>Kroky ladicího programu

Chcete-li pochopit, jak aplikace Wingtip implementuje nové zřizování tenanta ve sdílené databázi, přidejte zarážku a krok do pracovního postupu:

1. V *prostředí POWERSHELL ISE* otevřete... \\ Výukové moduly \\ ProvisionTenants \\ *Demo-ProvisionTenants.ps1* a nastavte následující parametry:
   - **$TenantName**  =  **Bushwillow Blues**, název nového místa.
   - **$VenueType**  =  **Blues**, jeden z předdefinovaných typů místa: blues, ClassicalMusic, roztancoval, jazz, Judo, motorracing, Multipurpose, Opera, rockmusic, fotbal (malá písmena, bez mezer).
   - **$DemoScenario**  =  **1**, pokud chcete zřídit tenanta ve sdílené databázi s ostatními klienty.

2. Přidejte zarážku tak, že umístíte kurzor kamkoli na řádek 38, řádek, který uvádí: *New-tenant*, a pak stiskněte **F9**.

   ![Snímek obrazovky, který zvýrazní řádek, který obsahuje nového tenanta.](./media/saas-multitenantdb-provision-and-catalog/breakpoint.png)

3. Spusťte skript stisknutím klávesy **F5**.

4. Po zastavení spuštění skriptu na zarážce stiskněte klávesu **F11** pro krok do kódu.

   ![Snímek obrazovky zobrazuje Integrované skriptovací prostředí (ISE) v prostředí Windows PowerShell s otevřenou nabídkou ladění a krok do výběru.](./media/saas-multitenantdb-provision-and-catalog/debug.png)

5. Sledujte provádění skriptu pomocí možností nabídky **ladění** , **F10** a **F11**, abyste mohli přenášet nebo nazývat na volané funkce.

Další informace o ladění skriptů PowerShellu najdete v tématu [o práci se skripty PowerShellu a jejich ladění](/powershell/scripting/components/ise/how-to-debug-scripts-in-windows-powershell-ise).

## <a name="provision-a-tenant-in-its-own-database"></a>Zřízení tenanta ve *vlastní* databázi

#### <a name="major-actions-of-provisioning"></a>Hlavní akce zřizování

Níže jsou uvedené klíčové prvky pracovního postupu, který jste procházeli během trasování skriptu:

- **Vypočítat nový klíč tenanta**: funkce hash se používá k vytvoření klíče tenanta z názvu tenanta.
- **Zkontrolujte, jestli klíč tenanta už existuje**: katalog se zkontroluje, aby se zajistilo, že klíč ještě není zaregistrovaný.
- **Vytvoření nové databáze tenanta**: databáze je vytvořena zkopírováním databáze *basetenantdb* pomocí šablony Správce prostředků.  Název nové databáze je založen na názvu tenanta.
- **Přidat databázi do katalogu**: Nová databáze tenanta je registrovaná jako horizontálních oddílů v katalogu.
- **Inicializace tenanta ve výchozí databázi tenanta**: databáze tenanta se aktualizuje, aby se přidaly nové informace o tenantovi.
- **Registrovat tenanta v katalogu**: mapování mezi novým klíčem tenanta a databází *sequoiasoccer* se přidá do katalogu.
- **Do katalogu se přidá název tenanta**: místo, do kterého se přidá název místa, se přidá do tabulky rozšíření tenantů v katalogu.
- **Otevřít stránku události pro nového tenanta**: stránka události *Sequoia fotbal* se otevře v prohlížeči.

   ![stránka events](./media/saas-multitenantdb-provision-and-catalog/sequoiasoccer.png)

#### <a name="debugger-steps"></a>Kroky ladicího programu

Nyní projdete procesem skriptu při vytváření tenanta ve vlastní databázi:

1. Pořád v... \\ Výukové moduly \\ ProvisionTenants \\ *Demo-ProvisionTenants.ps1* nastavte následující parametry:
   - **$TenantName**  =  **Sequoia fotbal**, název nového místa.
   - **$VenueType**  =  **fotbalový**, jeden z předdefinovaných typů místa: blues, ClassicalMusic, roztancoval, jazz, Judo, motorracing, Multipurpose, Opera, rockmusic, fotbal (malý případ, bez mezer).
   - **$DemoScenario**  =  **2**, pokud chcete zřídit tenanta ve své vlastní databázi.

2. Přidejte novou zarážku tak, že umístíte kurzor kamkoli na řádek 57, řádek, který uvádí: *& &nbsp; $PSScriptRoot \new-tenantanddatabase '*, a stiskněte **F9**.

   ![přerušení](./media/saas-multitenantdb-provision-and-catalog/breakpoint2.png)

3. Spusťte skript stisknutím klávesy **F5**.

4. Po zastavení spuštění skriptu na zarážce stiskněte klávesu **F11** ke kroku do kódu.  Pomocí nástroje **F10** a **klávesy F11** proveďte krokování a zajděte do funkce a sledujte spuštění.

## <a name="provision-a-batch-of-tenants"></a>Zřízení dávky tenantů

Toto cvičení zřídí dávku o 17 klientech. Než začnete používat jiné kurzy pro lístky Wingtip, doporučujeme zřídit tuto dávku tenantů, aby bylo možné pracovat s více databázemi.

1. V *prostředí POWERSHELL ISE* otevřete... \\ Výukové moduly \\ ProvisionTenants \\ *Demo-ProvisionTenants.ps1* a změňte parametr *$DemoScenario* na 4:
   - **$DemoScenario**  =  **4**, aby bylo možné zřídit dávku tenantů do sdílené databáze.

2. Stiskněte **F5** a spusťte skript.

### <a name="verify-the-deployed-set-of-tenants"></a>Ověření nasazené sady tenantů

V této fázi máte kombinaci klientů nasazených do sdílené databáze a klientů nasazených do jejich vlastních databází. Azure Portal lze použít ke kontrole databází, které byly vytvořeny. V [Azure Portal](https://portal.azure.com)otevřete **tenants1-MT- \<USER\>** server tak, že přejdete na seznam serverů SQL.  Seznam **databází SQL** by měl zahrnovat sdílenou databázi **tenants1** a databáze pro klienty, kteří jsou ve své vlastní databázi:

   ![seznam databází](./media/saas-multitenantdb-provision-and-catalog/Databases.png)

I když Azure Portal zobrazuje databáze tenantů, neumožňuje zobrazit klienty *ve* sdílené databázi. Úplný seznam tenantů najdete na webové stránce **centra událostí** pro společnost Wingtip a procházením katalogu.

#### <a name="using-wingtip-tickets-events-hub-page"></a>Použití stránky centra událostí Wingtip lístky

Otevřete stránku centra událostí v prohlížeči (http: Events. Wingtip-Mt. \<USER\> . trafficmanager.net)

#### <a name="using-catalog-database"></a>Používání databáze katalogu

Úplný seznam tenantů a odpovídající databáze pro každou z nich jsou k dispozici v katalogu. Je k dispozici zobrazení SQL, které spojuje název tenanta s názvem databáze. Přehledně ukazuje hodnotu rozšíření metadat, která jsou uložena v katalogu.
- Zobrazení SQL je k dispozici v databázi tenantcatalog.
- Název tenanta je uložený v tabulce tenantů.
- Název databáze je uložený v tabulkách pro správu horizontálních oddílů.

1. V SQL Server Management Studio (SSMS) se připojte k serveru tenantů v **katalogu – Mt. \<USER\> .. database.windows.net**, s přihlašovacími údaji = **Developer** a Password = **P \@ ssword1**

    ![Dialogové okno připojení SSMS](./media/saas-multitenantdb-provision-and-catalog/SSMSConnection.png)

2. V Průzkumník objektů SSMS přejděte do zobrazení v databázi *tenantcatalog* .

3. Klikněte pravým tlačítkem na *TenantsExtended* zobrazení a zvolte **Vybrat prvních 1000 řádků**. Všimněte si mapování mezi názvem klienta a databází pro různé klienty.

    ![Zobrazení ExtendedTenants v SSMS](./media/saas-multitenantdb-provision-and-catalog/extendedtenantsview.png)

## <a name="other-provisioning-patterns"></a>Další způsoby zřizování

Tato část popisuje další zajímavé vzory zřizování.

#### <a name="pre-provisioning-databases-in-elastic-pools"></a>Předběžné zřizování databází v elastických fondech

Vzor předběžného zřizování zneužije skutečnost, že při použití elastických fondů je faktura pro fond nikoli databáze. Proto je možné databáze přidat do elastického fondu, aby byly potřeba, aniž by to mělo za sebou dodatečné náklady. Tato předběžná revize významně zkracuje čas potřebný k zřízení tenanta v databázi. Počet databází, které jsou předem zřízené, se dá upravit podle potřeby, aby se zajistila vyrovnávací paměť vhodná pro předpokládanou rychlost zřizování.

#### <a name="auto-provisioning"></a>Automatické zřizování

Ve vzorech automatického zřizování se vyhrazená služba zřizování používá ke zřízení serverů, fondů a databází automaticky podle potřeby. Tato automatizace zahrnuje předběžné zřizování databází v elastických fondech. A pokud jsou databáze vyřazeny z provozu a odstraněny, mezery vytvořené v elastických fondech mohou být podle potřeby vyplněny službou zřizování.

Tento typ automatizované služby může být jednoduchý nebo složitý. Automatizace může například zvládnout zřizování napříč několika geografickými oblastmi a může nastavit geografickou replikaci pro zotavení po havárii. Pomocí vzoru automatického zřizování odešle klientská aplikace nebo skript požadavek na zřízení do fronty, kterou má zpracovat služba zřizování. Skript by se pak mohl dotázat, aby se zjistilo dokončení. Při použití předběžného zřizování se požadavky budou zpracovávat rychle, zatímco služba na pozadí by spravovala zřizování náhradní databáze.

## <a name="additional-resources"></a>Další zdroje informací

<!-- - Additional [tutorials that build upon the Wingtip SaaS application](./saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)-->
- [Klientská knihovna Elastic Database](elastic-database-client-library.md)
- [Postup ladění skriptů v Integrované skriptovací prostředí (ISE) v prostředí Windows PowerShell](/powershell/scripting/components/ise/how-to-debug-scripts-in-windows-powershell-ise)


## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Zřízení jednoho nového tenanta do sdílené databáze s více klienty a její vlastní databáze
> * Zřídit dávku dalších tenantů.
> * Projděte si podrobné informace o zřizování klientů a jejich registraci do katalogu.

Vyzkoušejte si [kurz k monitorování výkonu](./saas-multitenantdb-performance-monitoring.md).