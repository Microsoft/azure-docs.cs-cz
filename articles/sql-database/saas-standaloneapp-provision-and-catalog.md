---
title: Víceklientský kurz SaaS
description: Zřízení a katalog nových klientů pomocí samostatného vzoru aplikace
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: billgib
ms.date: 09/24/2018
ms.openlocfilehash: 02682a18f14e7ecbf5b42783ab84a1b55a4bb77b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74133123"
---
# <a name="provision-and-catalog-new-tenants-using-the--application-per-tenant-saas-pattern"></a>Zřízení a katalog nových klientů pomocí aplikace na vzor SaaS klienta

Tento článek popisuje zřizování a katalogizaci nových klientů pomocí samostatné aplikace na vzor SaaS klienta.
Tento článek má dvě hlavní části:
* Koncepční diskuse o zřizování a katalogizaci nových klientů
* Kurz, který zvýrazní ukázkový kód Prostředí PowerShell, který provádí zřizování a katalogizaci
    * Kurz používá wingtip vstupenky ukázkové aplikace SaaS, přizpůsobené samostatné aplikace na vzorek klienta.

## <a name="standalone-application-per-tenant-pattern"></a>Samostatná aplikace podle vzoru klienta

Samostatná aplikace na vzor klienta je jedním z několika vzorů pro víceklientské aplikace SaaS.  V tomto vzoru se zřaží samostatná aplikace pro každého klienta. Aplikace obsahuje součásti na úrovni aplikace a databázi SQL.  Každá aplikace klienta se dá nasadit v předplatném dodavatele.  Alternativně Azure nabízí [program spravovaných aplikací,](https://docs.microsoft.com/azure/managed-applications/overview) ve kterém lze aplikaci nasadit v předplatném klienta a spravovat dodavatelem jménem klienta.

   ![vzor aplikace pro jednotlivé klienty](media/saas-standaloneapp-provision-and-catalog/standalone-app-pattern.png)

Při nasazování aplikace pro klienta se aplikace a databáze zřaží v nové skupině prostředků vytvořené pro klienta.  Použití samostatných skupin prostředků izoluje prostředky aplikace každého klienta a umožňuje jejich správou nezávisle. V rámci každé skupiny prostředků je každá instance aplikace nakonfigurována pro přímý přístup k odpovídající databázi.  Tento model připojení kontrastuje s jinými vzory, které používají katalog pro zprostředkovatele připojení mezi aplikací a databází.  A jako neexistuje žádné sdílení prostředků, každá databáze klienta musí být zřízené s dostatečnými prostředky pro zpracování jeho zatížení ve špičce. Tento vzor inklinuje k použití pro aplikace SaaS s menším počtem klientů, kde je silný důraz na izolaci klienta a menší důraz na náklady na prostředky.

## <a name="using-a-tenant-catalog-with-the-application-per-tenant-pattern"></a>Použití katalogu klienta s aplikací na vzor klienta

Zatímco aplikace a databáze každého klienta jsou plně izolované, různé scénáře správy a analýzy mohou fungovat napříč tenanty.  Například použití změny schématu pro novou verzi aplikace vyžaduje změny schématu každé databáze klienta. Scénáře vytváření sestav a analýzy mohou také vyžadovat přístup ke všem databázím klienta bez ohledu na to, kde jsou nasazeny.

   ![vzor aplikace pro jednotlivé klienty](media/saas-standaloneapp-provision-and-catalog/standalone-app-pattern-with-catalog.png)

Katalog klienta obsahuje mapování mezi identifikátorem klienta a databází klienta, což umožňuje přeložit identifikátor na název serveru a databáze.  V aplikaci Wingtip SaaS se identifikátor klienta vypočítá jako hodnotu hash názvu klienta, i když se dá použít jiná schémata.  Zatímco samostatné aplikace nepotřebují katalog pro správu připojení, katalog lze použít k oboru jiné akce na sadu databází klientů. Elastický dotaz můžete například použít katalog k určení sady databází, přes které jsou distribuovány dotazy pro vytváření sestav mezi klienty.

## <a name="elastic-database-client-library"></a>Klientská knihovna elastické databáze

V ukázkové aplikaci Wingtip je katalog implementován funkcemi správy střepu [klientské knihovny elastické databáze](sql-database-elastic-database-client-library.md) (EDCL).  Knihovna umožňuje aplikaci vytvářet, spravovat a používat mapu střepů, která je uložena v databázi. V wingtip tickets ukázka katalogu je uložen v databázi *katalogu klienta.*  Úlomek mapuje klíč klienta na oddíl (databázi), ve kterém jsou uložena data tohoto klienta.  Edcl funkce spravovat *globální mapy střepů* uložené v tabulkách v databázi *katalogu klienta* a *místní mapy střepů* uložené v každém úlovku.

Funkce EDCL lze volat z aplikací nebo skriptů prostředí PowerShell k vytvoření a správě položek v mapě střepů. Jiné funkce EDCL lze načíst sadu štíl nebo připojit ke správné databázi pro daný klíč klienta.

> [!IMPORTANT]
> Neupravujte data v databázi katalogu nebo místní mapy střepů v databázích klienta přímo. Přímé aktualizace nejsou podporovány z důvodu vysokého rizika poškození dat. Místo toho upravte data mapování pouze pomocí portů EDCL.

## <a name="tenant-provisioning"></a>Zřizování klientů

Každý klient vyžaduje novou skupinu prostředků Azure, která musí být vytvořena, než v ní bude možné zřídit prostředky. Jakmile existuje skupina prostředků, šablonu Azure Resource Management lze použít k nasazení součástí aplikace a databáze a potom nakonfigurovat připojení k databázi. Chcete-li inicializovat schéma databáze, šablona můžete importovat soubor bacpac.  Alternativně databáze může být vytvořena jako kopie databáze šablony.  Databáze je pak dále aktualizována počátečními údaji o místě konání a registrována v katalogu.

## <a name="tutorial"></a>Kurz

Co se v tomto kurzu naučíte:

* Zřízení katalogu
* Registrace ukázkových databází klienta, které jste nasadili dříve v katalogu
* Zřízení dalšího klienta a jeho registrace v katalogu

Šablona Azure Resource Manager se používá k nasazení a konfiguraci aplikace, vytvoření databáze klienta a importovat soubor bacpac pro její inicializaci. Požadavek na import může být zařazen do fronty několik minut před tím, než je napaden.

Na konci tohoto kurzu máte sadu samostatných aplikací klienta, s každou databázi registrovanou v katalogu.

## <a name="prerequisites"></a>Požadavky

Předpokladem dokončení tohoto kurzu je splnění následujících požadavků:

* Je nainstalované prostředí Azure PowerShell. Podrobnosti najdete v článku [Začínáme s prostředím Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).
* Tři ukázkové aplikace klienta se nasazují. Informace o nasazení těchto aplikací za méně než pět minut najdete v [tématu Nasazení a prozkoumání vzoru samostatné aplikace Wingtip Tickets SaaS](saas-standaloneapp-get-started-deploy.md).

## <a name="provision-the-catalog"></a>Zřízení katalogu

V této úloze se dozvíte, jak zřídit katalog slouží k registraci všech databází klienta. Vaším úkolem je:

* **Zřídit databázi katalogu** pomocí šablony správy prostředků Azure. Databáze je inicializována importem souboru bacpac.
* **Zaregistrujte ukázkové aplikace klienta,** které jste nasadili dříve.  Každý klient je registrován pomocí klíče vytvořeného z hash názvu klienta.  Název klienta je také uložen v tabulce rozšíření v katalogu.

1. V prostředí PowerShell ISE otevřete *...\Learning Modules\UserConfig.psm* a aktualizujte ** \<\> uživatelskou** hodnotu na hodnotu, kterou jste použili při nasazování tří ukázkových aplikací.  **Uložte soubor**.
1. V prostředí PowerShell ISE otevřete *...\Výukové moduly\ProvisionTenants\Demo-ProvisionAndCatalog.ps1* a nastavte **$Scenario = 1**. Nasaďte katalog klienta a zaregistrujte předdefinované klienty.

1. Přidejte zarážku tak, že kurzor `& $PSScriptRoot\New-Catalog.ps1`na libovolné místo na řádku, který říká , a stiskněte **klávesu F9**.

    ![nastavení zarážky pro trasování](media/saas-standaloneapp-provision-and-catalog/breakpoint.png)

1. Spusťte skript stisknutím **klávesy F5**.
1.  Po spuštění skriptu se zastaví na zarážky, stisknutím **klávesy F11** krok do skriptu New-Catalog.ps1.
1.  Trasujte spuštění skriptu pomocí možností nabídky Ladění F10 a F11 krokovat nebo do volaných funkcí.
    *   Další informace o ladění skriptů prostředí PowerShell naleznete [v tématu Tipy pro práci se skripty prostředí PowerShell a ladění](https://docs.microsoft.com/powershell/scripting/components/ise/how-to-debug-scripts-in-windows-powershell-ise).

Po dokončení skriptu bude existovat katalog a všichni ukázkové klienti budou registrováni.

Nyní se podívejte na zdroje, které jste vytvořili.

1. Otevřete [portál Azure](https://portal.azure.com/) a procházejte skupiny prostředků.  Otevřete skupinu prostředků **uživatele\> \<wingtip-sa-catalog-** a poznamenejte si server katalogu a databázi.
1. Otevřete databázi na portálu a v levé nabídce vyberte *Průzkumník dat.*  Klepněte na příkaz Přihlásit se a zadejte heslo = **P\@ssword1**.


1. Prozkoumejte schéma databáze *katalogu klientů.*
   * Všechny objekty `__ShardManagement` ve schématu jsou poskytovány klientskou knihovnou elastické databáze.
   * Tabulka `Tenants` a `TenantsExtended` zobrazení jsou rozšíření přidané v ukázce, které ukazují, jak můžete rozšířit katalog poskytnout další hodnotu.
1. Spusťte `SELECT * FROM dbo.TenantsExtended`dotaz, .

   ![průzkumník dat](media/saas-standaloneapp-provision-and-catalog/data-explorer-tenantsextended.png)

    Jako alternativu k použití Průzkumníka dat se můžete připojit k databázi z SQL Server Management Studio. Chcete-li to provést, připojte se k serveru


    Všimněte si, že byste neměli upravovat data přímo v katalogu - vždy používejte rozhraní API pro správu svižných bitových žpě.

## <a name="provision-a-new-tenant-application"></a>Zřízení nové aplikace klienta

V této úloze se dozvíte, jak zřídit jednu klienta aplikace. Vaším úkolem je:

* **Vytvořte novou skupinu prostředků** pro klienta.
* **Zřizování aplikace a databáze** do nové skupiny prostředků pomocí šablony správy prostředků Azure.  Tato akce zahrnuje inicializaci databáze s běžnými daty schématu a odkazů importem souboru bacpac.
* **Inicializovat databázi se základními informacemi o klientovi**. Tato akce zahrnuje určení typu místa konání, které určuje fotografii použitou jako pozadí na webu událostí.
* **Zaregistrujte databázi v databázi katalogu**.

1. V prostředí PowerShell ISE otevřete *...\Výukové moduly\ProvisionTenants\Demo-ProvisionAndCatalog.ps1* a nastavte **$Scenario = 2**. Nasazení katalogu klientů a registrace předdefinovaných klientů

1. Přidejte zarážku do skriptu umístěním kurzoru `& $PSScriptRoot\New-TenantApp.ps1`na řádek 49, který říká , a stiskněte **klávesu F9**.
1. Spusťte skript stisknutím **klávesy F5**.
1.  Po spuštění skriptu se zastaví na zarážky, stisknutím **klávesy F11** krok do skriptu New-Catalog.ps1.
1.  Trasujte spuštění skriptu pomocí možností nabídky Ladění F10 a F11 krokovat nebo do volaných funkcí.

Po zřízení klienta se otevře web událostí nového klienta.

   ![červený javor závodní](media/saas-standaloneapp-provision-and-catalog/redmapleracing.png)

Potom můžete zkontrolovat nové prostředky vytvořené na webu Azure Portal.

   ![červený javor závodní zdroje](media/saas-standaloneapp-provision-and-catalog/redmapleracing-resources.png)


## <a name="to-stop-billing-delete-resource-groups"></a>Ukončení fakturace odstraněním skupin prostředků

Po dokončení zkoumání ukázky odstraňte všechny skupiny prostředků, které jste vytvořili, abyste zastavili přidruženou fakturaci.

## <a name="additional-resources"></a>Další zdroje

- Další informace o víceklientských databázových aplikacích SaaS najdete [v tématu Návrhové vzory pro víceklientské aplikace SaaS](saas-tenancy-app-design-patterns.md).

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se dozvěděli:

> [!div class="checklist"]
> * Jak nasadit wingtip vstupenky SaaS samostatná aplikace.
> * O serverech a databázích, které tvoří aplikaci.
> * Jak odstranit ukázkové prostředky k zastavení související fakturace.

Můžete prozkoumat, jak se katalog používá k podpoře různých scénářů mezi klienty pomocí verze aplikace [Wingtip Tickets SaaS](saas-dbpertenant-wingtip-app-overview.md).
