---
title: Poskytování víceklientských služeb SaaS
description: Zjistěte, jak zřídit a katalogizovat nové klienty v aplikaci SaaS s více klienty Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MightyPen
ms.author: genemi
ms.reviewer: billgib,andrela,stein
ms.date: 09/24/2018
ms.openlocfilehash: 4ea18ee23d845b2d16209b23de14dc3cd70aaa59
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74133145"
---
# <a name="provision-and-catalog-new-tenants-in-a-saas-application-using-a-sharded-multi-tenant-azure-sql-database"></a>Zřizování a katalogkatalogování nových klientů v aplikaci SaaS pomocí vrstvené víceklientské databáze Azure SQL pro více klientů

Tento článek popisuje zřizování a katalogizaci nových klientů, ve *víceklientské oborové databázi* modelu nebo vzoru.

Tento článek má dvě hlavní části:

- [Koncepční diskuse](#goto_2_conceptual) o zřizování a katalogizaci nových klientů.

- [Kurz,](#goto_1_tutorial) který zvýrazní kód skriptu Prostředí PowerShell, který provádí zřizování a katalogizaci.
  - Kurz používá wingtip vstupenky SaaS aplikace, přizpůsobené víceklientské oborové databáze vzor.

<a name="goto_2_conceptual"/>

## <a name="database-pattern"></a>Vzor databáze

Tato část a několik dalších, které následují, diskutovat o koncepty víceklientské oborové databázi vzor.

V tomto víceklientské oborové množiny modelu schémata tabulky uvnitř každé databáze zahrnují klíč klienta v primárním klíči tabulek, které ukládají data klienta. Klíč klienta umožňuje každé jednotlivé databáze pro uložení 0, 1 nebo mnoho klientů. Použití databáze s oddíly usnadňuje aplikačnímu systému podporu velmi velkého počtu klientů. Všechna data pro jednoho klienta je uložena v jedné databázi. Velký počet klientů jsou distribuovány v mnoha rozdělených databází. Databáze katalogu ukládá mapování každého klienta do své databáze.

#### <a name="isolation-versus-lower-cost"></a>Izolace versus nižší náklady

Klient, který má databázi pro sebe, využívá výhod izolace. Klient může mít databázi obnovena na dřívější datum, aniž by byl omezen dopadem na ostatní klienty. Výkon databáze lze naladit optimalizovat pro jednoho klienta, znovu bez nutnosti kompromisu s ostatními klienty. Problém je, že izolace stojí více než náklady na sdílení databáze s ostatními klienty.

Když je zřízena nového klienta, může sdílet databázi s jinými klienty, nebo jej lze umístit do své vlastní nové databáze. Později si můžete změnit názor a přesunout databázi do jiné situace.

Databáze s více klienty a jedním tenantem jsou smíchány ve stejné aplikaci SaaS, aby se optimalizovaly náklady nebo izolace pro každého klienta.

   ![Databázová aplikace s více klienty s katalogem klienta s oborovými obory](media/saas-multitenantdb-provision-and-catalog/MultiTenantCatalog.png)

## <a name="tenant-catalog-pattern"></a>Vzor katalogu klienta

Pokud máte dvě nebo více databází, z nichž každá obsahuje alespoň jednoho klienta, aplikace musí mít způsob, jak zjistit, která databáze ukládá klienta aktuálního zájmu. Databáze katalogu ukládá toto mapování.

#### <a name="tenant-key"></a>Klíč klienta

Pro každého klienta wingtip aplikace může odvodit jedinečný klíč, který je klíč klienta. Aplikace extrahuje název klienta z adresy URL webové stránky. Aplikace zapisuje název k získání klíče. Aplikace používá klíč pro přístup ke katalogu. Katalog křížové odkazy informace o databázi, ve kterém je uložen klienta. Aplikace používá informace o databázi pro připojení. Lze také použít další schémata klíče klienta.

Použití katalogu umožňuje změnit název nebo umístění databáze klienta po zřizování bez narušení aplikace. V modelu databáze s více klienty katalog umisťuje přesunutí klienta mezi databázemi.

#### <a name="tenant-metadata-beyond-location"></a>Metadata klienta mimo umístění

Katalog může také určit, zda je klient offline pro údržbu nebo jiné akce. A katalog lze rozšířit do uložení dalších metadat klienta nebo databáze, například následující položky:
- Úroveň služby nebo edice databáze.
- Verze schématu databáze.
- Název klienta a jeho Smlouva SLA (smlouva o úrovni služeb).
- Informace umožňující správu aplikací, zákaznickou podporu nebo devops procesy.

Katalog lze také povolit vytváření sestav mezi klienty, správu schématu a výpis dat pro analytické účely.

### <a name="elastic-database-client-library"></a>Klientská knihovna elastické databáze

V Wingtip katalog je implementována v databázi *katalogu tenantcatalog.* *Katalog tenantů* je vytvořen pomocí funkcí správy střepů [klientské knihovny elastické databáze (EDCL).](sql-database-elastic-database-client-library.md) Knihovna umožňuje aplikaci vytvářet, spravovat a používat *mapu střepů,* která je uložena v databázi. Mapa střepů křížově odkazuje na klíč klienta s jeho úlomkem, což znamená jeho oborovou databázi.

Během zřizování klienta edcl funkce lze použít z aplikací nebo skripty Prostředí PowerShell k vytvoření položek v mapě střepů. Později edcl funkce lze použít pro připojení ke správné databázi. Edcl ukládá informace o připojení, aby se minimalizoval provoz v databázi katalogu a urychlil proces připojení.

> [!IMPORTANT]
> *Neupravujte* data v databázi katalogu prostřednictvím přímého přístupu! Přímé aktualizace nejsou podporovány z důvodu vysokého rizika poškození dat. Místo toho upravte data mapování pouze pomocí portů EDCL.

## <a name="tenant-provisioning-pattern"></a>Vzor zřizování klienta

#### <a name="checklist"></a>Kontrolní seznam

Pokud chcete zřídit nového klienta do existující sdílené databáze, sdílené databáze, musíte se zeptat na následující otázky:
- Zbývá mu dost místa pro nového nájemníka?
- Obsahuje tabulky s potřebnými referenčními daty pro nového klienta, nebo je lze přidat?
- Má odpovídající variantu základního schématu pro nového klienta?
- Je v příslušné zeměpisné poloze v blízkosti nového klienta?
- Je na správné úrovni služby pro nového klienta?

Pokud chcete, aby byl nový tenant izolován ve své vlastní databázi, můžete ho vytvořit tak, aby splňoval specifikace pro klienta.

Po dokončení zřizování je nutné zaregistrovat klienta v katalogu. Nakonec mapování klienta lze přidat k odkazování na příslušný oddíl.

#### <a name="template-database"></a>Databáze šablon

Zřízení databáze spuštěním skriptů SQL, nasazení bacpac nebo zkopírováním databáze šablony. Aplikace Wingtip zkopírují databázi šablon a vytvoří nové databáze klientů.

Jako každá aplikace, Wingtip se bude vyvíjet v průběhu času. V době, Wingtip bude vyžadovat změny v databázi. Změny mohou zahrnovat následující položky:
- Nové nebo změněné schéma.
- Nová nebo změněná referenční data.
- Rutinní úlohy údržby databáze pro zajištění optimálního výkonu aplikace.

U SaaS aplikace musí být tyto změny nasazeny koordinovaně u potenciálně velkého počtu klientských databází. Pro tyto změny být v budoucích databázích tenanta, je třeba začlenit do procesu zřizování. Tato výzva je dále prozkoumána v [kurzu pro správu schématu](saas-tenancy-schema-management.md).

#### <a name="scripts"></a>Scripts

Zřizování skriptů klienta v tomto kurzu podporují oba následující scénáře:
- Zřizování klienta do existující databáze sdílené s ostatními klienty.
- Zřizování klienta do vlastní databáze.

Data klienta je pak inicializována a registrována v mapě oddílu oddílu katalogu. V ukázkové aplikaci databáze, které obsahují více klientů jsou uvedeny obecný název, jako jsou *tenants1* nebo *tenants2*. Databáze, které obsahují jednoho klienta jsou uvedeny název klienta. Konkrétní konvence pojmenování použité v ukázce nejsou důležitou součástí vzoru, protože použití katalogu umožňuje přiřadit databázi libovolný název.

<a name="goto_1_tutorial"/>

## <a name="tutorial-begins"></a>Začíná kurz

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Zřízení klienta do databáze s více klienty
> * Zřízení klienta do databáze s jedním tenantem
> * Zřízení dávky klientů do databází s více klienty i do databází s jedním tenantem
> * Registrace mapování databáze a klienta v katalogu

#### <a name="prerequisites"></a>Požadavky

Předpokladem dokončení tohoto kurzu je splnění následujících požadavků:

- Je nainstalované prostředí Azure PowerShell. Podrobnosti najdete v článku [Začínáme s prostředím Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

- Wingtip Vstupenky SaaS Víceklientdatabáze aplikace je nasazena. Informace o nasazení za méně než pět minut najdete v [tématu Nasazení a prozkoumání aplikace Wingtip Tickets SaaS Multi-tenant Database](saas-multitenantdb-get-started-deploy.md)

- Získejte wingtip skripty a zdrojový kód:
    - Wingtip Tickets SaaS Multi-tenant databázové skripty a zdrojový kód aplikace jsou k dispozici v [WingtipTicketsSaaS-MultitenantDB](https://github.com/microsoft/WingtipTicketsSaaS-MultiTenantDB) Úložiště GitHub.
    - Podívejte se na [obecné pokyny](saas-tenancy-wingtip-app-guidance-tips.md) pro kroky ke stažení a odblokování wingtip skriptů.

## <a name="provision-a-tenant-into-a-database-shared-with-other-tenants"></a>Zřízení klienta do databáze *sdílené* s ostatními klienty

V této části se zobrazí seznam hlavních akcí pro zřizování, které jsou prováděny skripty prostředí PowerShell. Potom pomocí ladicího programu Prostředí PowerShell ISE krokovat skripty zobrazit akce v kódu.

#### <a name="major-actions-of-provisioning"></a>Hlavní opatření v oblasti zřizování

Níže jsou uvedeny klíčové prvky pracovního postupu zřizování krokování:

- **Vypočítat nový klíč klienta**: Funkce hash se používá k vytvoření klíče klienta z názvu klienta.
- **Zkontrolujte, zda klíč klienta již existuje**: Katalog je zkontrolován, aby se zajistilo, že klíč ještě nebyl zaregistrován.
- **Inicializovat klienta ve výchozí databázi klienta**: Databáze klienta se aktualizuje a přidá nové informace o klientovi.
- **Registrace klienta v katalogu**: Mapování mezi novým klíčem klienta a existující databází tenantů1 je přidáno do katalogu.
- **Přidejte název klienta do tabulky rozšíření katalogu**: Název místa je přidán do tabulky Klienti v katalogu.  Toto přidání ukazuje, jak lze rozšířit databázi katalogu pro podporu dalších dat specifických pro aplikaci.
- **Otevřít události stránku pro nového klienta**: *Bushwillow Blues* události stránka je otevřena v prohlížeči.

   ![stránka events](media/saas-multitenantdb-provision-and-catalog/bushwillow.png)

#### <a name="debugger-steps"></a>Kroky ladicího programu

Chcete-li pochopit, jak aplikace Wingtip implementuje nové zřizování klienta ve sdílené databázi, přidejte zarážku a krokovat pracovní postup:

1. V *prostředí PowerShell ISE*otevřete ... \\Learning Modules\\ProvisionTenants\\*Demo-ProvisionTenants.ps1* a nastavte následující parametry:
   - **$TenantName** = **Bushwillow Blues**, název nového místa konání.
   - **$VenueType** = **blues**, jeden z předem definovaných typů míst konání: blues, klasická hudba, tanec, jazz, judo, motorracing, víceúčelový, opera, rockmusic, fotbal (malá písmena, žádné mezery).
   - **$DemoScenario** = **1**, zřídit klienta ve sdílené databázi s ostatními klienty.

2. Přidejte zarážku tak, že kurzor na libovolné místo na řádku 38, řádek, který říká: *Nový tenant '* a stiskněte **klávesu F9**.

   ![přerušení](media/saas-multitenantdb-provision-and-catalog/breakpoint.png)

3. Spusťte skript stisknutím **klávesy F5**.

4. Po spuštění skriptu zastaví na zarážky, stisknutím **klávesy F11** krok do kódu.

   ![ladit](media/saas-multitenantdb-provision-and-catalog/debug.png)

5. Trasujte spuštění skriptu pomocí možností nabídky **Ladění** **F10** a **F11**, abyste přešli nebo do volaných funkcí.

Další informace o ladění skriptů prostředí PowerShell naleznete [v tématu Tipy pro práci se skripty prostředí PowerShell a ladění](https://docs.microsoft.com/powershell/scripting/components/ise/how-to-debug-scripts-in-windows-powershell-ise).

## <a name="provision-a-tenant-in-its-own-database"></a>Zřízení klienta ve *vlastní* databázi

#### <a name="major-actions-of-provisioning"></a>Hlavní opatření v oblasti zřizování

Níže jsou uvedeny klíčové prvky pracovního postupu, kterým procházíte při trasování skriptu:

- **Vypočítat nový klíč klienta**: Funkce hash se používá k vytvoření klíče klienta z názvu klienta.
- **Zkontrolujte, zda klíč klienta již existuje**: Katalog je zkontrolován, aby se zajistilo, že klíč ještě nebyl zaregistrován.
- **Vytvořit novou databázi klienta**: Databáze se vytvoří zkopírováním databáze *basetenantdb* pomocí šablony Správce prostředků.  Nový název databáze je založen na názvu klienta.
- **Přidat databázi do katalogu**: Nová databáze klienta je registrována jako oddíl oddílu v katalogu.
- **Inicializovat klienta ve výchozí databázi klienta**: Databáze klienta se aktualizuje a přidá nové informace o klientovi.
- **Registrace klienta v katalogu**: Mapování mezi novým klíčem klienta a *sequoiasoccer* databáze je přidán do katalogu.
- **Název klienta je přidán do katalogu**: Název místa je přidán do tabulky rozšíření Klienti v katalogu.
- **Otevřít stránku Události pro nového klienta**: Stránka *Sequoia Soccer* Events se otevře v prohlížeči.

   ![stránka events](media/saas-multitenantdb-provision-and-catalog/sequoiasoccer.png)

#### <a name="debugger-steps"></a>Kroky ladicího programu

Nyní projděte proces skriptu při vytváření klienta ve své vlastní databázi:

1. Stále v ... \\Learning Modules\\ProvisionTenants\\*Demo-ProvisionTenants.ps1* nastavit následující parametry:
   - **$TenantName** = **Sequoia Soccer**, název nového místa konání.
   - **$VenueType** = **fotbal**, jeden z předem definovaných typů míst konání: blues, klasická hudba, tanec, jazz, judo, motorracing, víceúčelový, opera, rockmusic, fotbal (malá písmena, žádné prostory).
   - **$DemoScenario** = **2**, zřídit klienta do své vlastní databáze.

2. Přidejte novou zarážku umístěním kurzoru na libovolné místo na řádku 57, na řádek s nápisem: * & &nbsp;$PSScriptRoot\New-TenantAndDatabase "* a stiskněte **klávesu F9**.

   ![přerušení](media/saas-multitenantdb-provision-and-catalog/breakpoint2.png)

3. Spusťte skript stisknutím **klávesy F5**.

4. Po spuštění skriptu zastaví na zarážky, stisknutím **klávesy F11** krok do kódu.  Pomocí **F10** a **F11** krokovat a krok ovat funkce pro sledování provádění.

## <a name="provision-a-batch-of-tenants"></a>Zřízení dávky klientů

Toto cvičení stanoví dávku 17 nájemníků. Doporučujeme zřídit tuto dávku klientů před zahájením jiných kurzů wingtip tickets, aby bylo více databází, se kterými můžete pracovat.

1. V *prostředí PowerShell ISE*otevřete ... \\\\Learning Modules\\ProvisionTenants*Demo-ProvisionTenants.ps1* a změňte parametr *$DemoScenario* na 4:
   - **$DemoScenario** = **4**, zřídit dávku klientů do sdílené databáze.

2. Stiskněte **F5** a spusťte skript.

### <a name="verify-the-deployed-set-of-tenants"></a>Ověření nasazené sady klientů

V této fázi máte kombinaci klientů nasazených do sdílené databáze a tenantů nasazených do vlastních databází. Portál Azure slouží ke kontrole vytvořených databází. Na [webu Azure Portal](https://portal.azure.com)otevřete **server tenants1-mt-\<USER\> ** procházením seznamu serverů SQL.  Seznam **databází SQL** by měl obsahovat sdílenou **databázi tenantů1** a databáze pro klienty, které jsou ve vlastní databázi:

   ![seznam databází](media/saas-multitenantdb-provision-and-catalog/Databases.png)

Zatímco portál Azure zobrazuje databáze klientů, neumožňuje zobrazit klienty *uvnitř* sdílené databáze. Úplný seznam klientů lze vidět na webové stránce **Centra událostí** wingtip a procházením katalogu.

#### <a name="using-wingtip-tickets-events-hub-page"></a>Použití stránky centra událostí Wingtip Tickets

Otevřete stránku Centrum událostí v prohlížeči (http:events.wingtip-mt.\<USER\>.trafficmanager.net)

#### <a name="using-catalog-database"></a>Použití databáze katalogu

Úplný seznam klientů a odpovídající databáze pro každého je k dispozici v katalogu. Je k dispozici zobrazení SQL, které spojuje název klienta s názvem databáze. Zobrazení pěkně ukazuje hodnotu rozšíření metadata, která je uložena v katalogu.
- Zobrazení SQL je k dispozici v databázi katalogu klientů.
- Název klienta je uložen v tabulce Klienti.
- Název databáze je uložen v tabulkách Správy skládek.

1. V SQL Server Management Studio (SSMS), připojte se k serveru klientů na **catalog-mt USER\<\>.database.windows.net**, s Login = **developer**, a Heslo = P **\@smemeč1**

    ![Dialogové okno připojení SSMS](media/saas-multitenantdb-provision-and-catalog/SSMSConnection.png)

2. V Průzkumníku objektů SSMS přejděte zobrazení v databázi *katalogu klientů.*

3. Klikněte pravým tlačítkem myši na zobrazení *TenantsExtended* a zvolte **Vybrat prvních 1000 řádků**. Všimněte si mapování mezi názvem klienta a databáze pro různé klienty.

    ![RozšířenéTenants zobrazení v SSMS](media/saas-multitenantdb-provision-and-catalog/extendedtenantsview.png)

## <a name="other-provisioning-patterns"></a>Další způsoby zřizování

Tato část popisuje další zajímavé vzory zřizování.

#### <a name="pre-provisioning-databases-in-elastic-pools"></a>Předběžné zřizování databází v elastických fondech

Vzor předběžnézřivací využití skutečnosti, že při použití elastických fondů fakturace je pro fond není databází. Databáze tedy mohou být přidány do elastického fondu dříve, než jsou potřeba bez dodatečných nákladů. Toto předběžné zobrazení výrazně zkracuje dobu, která je čas emitována do databáze. Počet předem zřízených databází lze upravit podle potřeby zachovat vyrovnávací paměť vhodnou pro předpokládanou míru zřizování.

#### <a name="auto-provisioning"></a>Automatické zřizování

Ve vzoru automatického zřizování se vyhrazená zřizovací služba používá k automatickému zřizování serverů, fondů a databází podle potřeby. Tato automatizace zahrnuje předběžné zřizování databází v elastických fondech. A pokud databáze jsou vyřazeny z provozu a odstraněny, mezery, které vytvoří v elastických fondech může být vyplněna zřizovací služby podle potřeby.

Tento typ automatizované služby může být jednoduchý nebo složitý. Automatizace může například zpracovat zřizování napříč více zeměpisnými oblastmi a může nastavit geografickou replikaci pro zotavení po havárii. Se vzorem automatického zřizování by klientská aplikace nebo skript odeslaly požadavek na zřizování do fronty, která má být zpracována zřizovací službou. Skript by pak dotazování zjistit dokončení. Pokud se používá předběžné zřizování, požadavky by byly zpracovány rychle, zatímco služba na pozadí by spravovala zřizování náhradní databáze.

## <a name="additional-resources"></a>Další zdroje

<!-- - Additional [tutorials that build upon the Wingtip SaaS application](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)-->
- [Klientská knihovna Elastic Database](sql-database-elastic-database-client-library.md)
- [Ladění skriptů v prostředí Windows PowerShell ISE](https://docs.microsoft.com/powershell/scripting/components/ise/how-to-debug-scripts-in-windows-powershell-ise)


## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Zřízení jednoho nového klienta do sdílené databáze s více klienty a vlastní databáze
> * Zřídit dávku dalších tenantů.
> * Krokovat podrobnosti zřizování klientů a jejich registraci do katalogu

Vyzkoušejte [kurz sledování výkonu](saas-multitenantdb-performance-monitoring.md).

