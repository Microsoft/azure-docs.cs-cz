---
title: Kurz pro více tenantů SaaS
description: Zřizování a katalog nových tenantů pomocí samostatného modelu aplikace
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
ms.openlocfilehash: 2343800f8801105ca75f285972b441ecb027d1a0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92793241"
---
# <a name="provision-and-catalog-new-tenants-using-the--application-per-tenant-saas-pattern"></a>Zřízení a zařazení nových tenantů pomocí aplikace na SaaS vzor pro každého tenanta
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Tento článek se zabývá zřizováním a katalogem nových klientů pomocí samostatné aplikace na vzor SaaS pro tenanta.
Tento článek obsahuje dvě hlavní části:
* Koncepční diskuze o zřizování a vytváření katalogu nových tenantů
* Kurz, ve kterém se zvýrazňuje ukázkový kód PowerShellu, který provádí zřizování a katalogizaci
    * V tomto kurzu se používá ukázková SaaS aplikace Wingtip Tickets, která je přizpůsobená samostatné aplikaci na vzor klienta.

## <a name="standalone-application-per-tenant-pattern"></a>Vzor samostatné aplikace na tenanta

Samostatná aplikace na model tenanta je jedním z několika vzorů pro víceklientské aplikace SaaS.  V tomto modelu se pro každého tenanta zřídí samostatná aplikace. Aplikace zahrnuje komponenty na úrovni aplikace a Azure SQL Database.  Každá klientská aplikace se dá nasadit v předplatném dodavatele.  Alternativně nabízí Azure [program spravovaných aplikací](../../azure-resource-manager/managed-applications/overview.md) , ve kterém můžete aplikaci nasadit v předplatném tenanta a spravovat ji dodavatel jménem tenanta.

   ![vzor aplikace na tenanta](./media/saas-standaloneapp-provision-and-catalog/standalone-app-pattern.png)

Při nasazování aplikace pro tenanta se aplikace a databáze zřídí v nové skupině prostředků vytvořené pro tenanta.  Použití samostatných skupin prostředků izoluje prostředky aplikací jednotlivých klientů a umožňuje je spravovat nezávisle. V rámci každé skupiny prostředků je každá instance aplikace nakonfigurovaná tak, aby k ní měla přímo přístup odpovídající databáze.  Tento model připojení se liší od dalších vzorů, které používají Katalog ke zprostředkovateli připojení mezi aplikací a databází.  A protože neexistuje sdílení prostředků, musí být každá databáze tenanta zřízena s dostatečnými prostředky pro zpracování zatížení ve špičce. Tento model je využíván pro SaaS aplikace s menším počtem tenantů, kde existuje silný důraz na izolaci tenanta a méně důraz na náklady na prostředky.

## <a name="using-a-tenant-catalog-with-the-application-per-tenant-pattern"></a>Použití katalogu tenantů s aplikací na model klienta

I když jsou aplikace a databáze jednotlivých tenantů plně izolované, můžou různé scénáře správy a analýzy fungovat napříč klienty.  Například použití změny schématu pro novou verzi aplikace vyžaduje změny schématu v každé databázi tenanta. Scénáře vytváření sestav a analýzy mohou také vyžadovat přístup ke všem databázím tenanta bez ohledu na to, kde jsou nasazeny.

   ![Diagram, který ukazuje, jak používat katalog tenanta s aplikací na model klienta.](./media/saas-standaloneapp-provision-and-catalog/standalone-app-pattern-with-catalog.png)

Katalog tenanta obsahuje mapování mezi identifikátorem tenanta a databází tenanta, takže se identifikátor dá přeložit na název serveru a databáze.  V aplikaci Wingtip SaaS je identifikátor tenanta vypočítaný jako hodnota hash názvu tenanta, i když se dají použít jiné systémy.  I když samostatné aplikace nepotřebují ke správě připojení katalog, katalog se dá použít k určení rozsahu dalších akcí pro sadu databází tenantů. Elastický dotaz může například použít katalog k určení sady databází, napříč kterými jsou dotazy distribuované pro vytváření sestav napříč klienty.

## <a name="elastic-database-client-library"></a>Klientská knihovna Elastic Database

V ukázkové aplikaci Wingtip se katalog implementuje pomocí funkcí správy horizontálních oddílů [klientské knihovny nástroje elastic Database](elastic-database-client-library.md) (EDCL).  Knihovna umožňuje aplikaci vytvořit, spravovat a použít mapu horizontálních oddílů, která je uložená v databázi. V ukázce lístku Wingtip je katalog uložen v databázi *katalogu tenanta* .  Horizontálních oddílů mapuje klíč tenanta na horizontálních oddílů (databáze), ve kterém jsou uložená data tenanta.  Funkce EDCL spravují *globální mapu horizontálních oddílů* uloženou v tabulkách v databázi *katalogu tenanta* a *místní mapu horizontálních oddílů* uloženou v každém horizontálních oddílů.

Funkce EDCL se dají volat z aplikací nebo skriptů PowerShellu pro vytváření a správu položek v mapě horizontálních oddílů. Další funkce EDCL lze použít k načtení sady horizontálních oddílů nebo ke správné databázi pro daný klíč tenanta.

> [!IMPORTANT]
> Neupravujte data v databázi katalogu ani místní mapu horizontálních oddílů v databázích klienta přímo. Přímé aktualizace nejsou podporovány z důvodu vysokého rizika poškození dat. Místo toho upravte mapování dat pouze pomocí rozhraní EDCL API.

## <a name="tenant-provisioning"></a>Zřizování tenanta

Každý tenant vyžaduje novou skupinu prostředků Azure, která se musí vytvořit předtím, než bude možné v ní zřídit prostředky. Jakmile skupina prostředků existuje, můžete použít šablonu Azure Resource Management k nasazení součástí aplikace a databáze a pak nakonfigurovat připojení k databázi. Pro inicializaci schématu databáze může šablona importovat soubor BacPac.  Případně je možné databázi vytvořit jako kopii databáze šablony.  Databáze se pak dále aktualizuje s počátečními daty místa a zaregistrovanými v katalogu.

## <a name="tutorial"></a>Kurz

Co se v tomto kurzu naučíte:

* Zřízení katalogu
* Registrace ukázkových databází tenantů, které jste nasadili dříve v katalogu
* Zřídit dalšího tenanta a zaregistrovat ho v katalogu

Šablona Azure Resource Manager slouží k nasazení a konfiguraci aplikace, vytvoření databáze tenanta a následnému importu souboru BacPac pro inicializaci. Žádost o import může být před provedením akce zařazena do fronty několik minut.

Na konci tohoto kurzu máte sadu samostatných klientských aplikací s každou databází zaregistrovanou v katalogu.

## <a name="prerequisites"></a>Předpoklady

Předpokladem dokončení tohoto kurzu je splnění následujících požadavků:

* Je nainstalované prostředí Azure PowerShell. Podrobnosti najdete v článku [Začínáme s prostředím Azure PowerShell](/powershell/azure/get-started-azureps).
* Nasadí se tři ukázkové klientské aplikace. Pokud chcete tyto aplikace nasadit za méně než pět minut, přečtěte si téma [nasazení a prozkoumání samostatného vzoru aplikace SaaS lístky](./saas-standaloneapp-get-started-deploy.md).

## <a name="provision-the-catalog"></a>Zřízení katalogu

V této úloze se dozvíte, jak zřídit katalog používaný k registraci všech databází tenanta. V této lekci:

* **Zřízení databáze katalogu** pomocí šablony Azure Resource Management. Databáze je inicializovaná importem souboru BacPac.
* **Zaregistrujte ukázkové klientské aplikace** , které jste nasadili dříve.  Každý tenant je zaregistrován pomocí klíče vytvořeného z hodnoty hash názvu tenanta.  Název tenanta je také uložen v tabulce rozšíření v katalogu.

1. V PowerShellu ISE otevřete *. ..\Learning Modules\UserConfig.PSM* a aktualizujte **\<user\>** hodnotu na hodnotu, kterou jste použili při nasazování tří ukázkových aplikací.  **Uložte soubor**.
1. V PowerShellu ISE otevřete *. ..\Learning Modules\ProvisionTenants\Demo-ProvisionAndCatalog.ps1* a nastavte **$Scenario = 1**. Nasaďte katalog tenanta a zaregistrujte předem definované klienty.

1. Přidejte zarážku tak, že umístíte kurzor na libovolné místo na řádku, který říká, `& $PSScriptRoot\New-Catalog.ps1` a pak stiskněte **F9**.

    ![Nastavení zarážky pro trasování](./media/saas-standaloneapp-provision-and-catalog/breakpoint.png)

1. Spusťte skript stisknutím klávesy **F5**.
1.  Po zastavení spuštění skriptu na zarážce stiskněte klávesu **F11** ke kroku do skriptu New-Catalog.ps1.
1.  Sledujte provádění skriptu pomocí možností nabídky ladění, F10 a F11, abyste mohli přenášet nebo nazývat na volané funkce.
    *   Další informace o ladění skriptů PowerShellu najdete v tématu [o práci se skripty PowerShellu a jejich ladění](/powershell/scripting/components/ise/how-to-debug-scripts-in-windows-powershell-ise).

Po dokončení skriptu bude katalog existovat a budou zaregistrováni všichni ukázkový tenant.

Teď se podívejte na prostředky, které jste vytvořili.

1. Otevřete [Azure Portal](https://portal.azure.com/) a procházejte skupinami prostředků.  Otevřete skupinu **Wingtip-SA-Catalog- \<user\>** Resource a poznamenejte si Server katalogu a databázi.
1. Otevřete databázi na portálu a v nabídce na levé straně vyberte *Průzkumník dat* .  Klikněte na příkaz Login (přihlášení) a zadejte heslo = **P \@ ssword1**.


1. Prozkoumejte schéma databáze *tenantcatalog* .
   * Objekty ve `__ShardManagement` schématu jsou k dispozici v klientské knihovně elastic Database.
   * `Tenants`Tabulka a `TenantsExtended` zobrazení jsou rozšíření přidaná v ukázce, která demonstrují způsob rozšíření katalogu, aby bylo možné poskytnout další hodnotu.
1. Spusťte dotaz, `SELECT * FROM dbo.TenantsExtended` .

   ![Průzkumník dat](./media/saas-standaloneapp-provision-and-catalog/data-explorer-tenantsextended.png)

    Jako alternativu k používání Průzkumník dat se můžete připojit k databázi z SQL Server Management Studio. Provedete to tak, že se připojíte k serveru Wingtip-


    Všimněte si, že byste neměli upravovat data přímo v katalogu – vždycky používejte rozhraní API pro správu horizontálních oddílů.

## <a name="provision-a-new-tenant-application"></a>Zřízení nové klientské aplikace

V této úloze se dozvíte, jak zřídit jednu klientskou aplikaci. V této lekci:

* **Vytvořte pro tenanta novou skupinu prostředků** .
* **Aplikaci a databázi zřiďte** do nové skupiny prostředků pomocí šablony Azure Resource Management.  Tato akce zahrnuje inicializaci databáze pomocí společného schématu a referenčních dat importováním souboru BacPac.
* **Inicializujte databázi se základními informacemi o tenantovi**. Tato akce zahrnuje určení typu místa, který určuje fotografii použitou jako pozadí na webu s událostmi.
* **Zaregistrujte databázi v databázi katalogu**.

1. V PowerShellu ISE otevřete *. ..\Learning Modules\ProvisionTenants\Demo-ProvisionAndCatalog.ps1* a nastavte **$Scenario = 2**. Nasazení katalogu tenanta a registrace předem definovaných tenantů

1. Vložte zarážku do skriptu tak, že umístíte kurzor na libovolné místo na řádku 49, který uvádí, `& $PSScriptRoot\New-TenantApp.ps1` a pak stiskněte **F9**.
1. Spusťte skript stisknutím klávesy **F5**.
1.  Po zastavení spuštění skriptu na zarážce stiskněte klávesu **F11** ke kroku do skriptu New-Catalog.ps1.
1.  Sledujte provádění skriptu pomocí možností nabídky ladění, F10 a F11, abyste mohli přenášet nebo nazývat na volané funkce.

Po zřízení tenanta se otevře web s událostmi nového tenanta.

   ![červená Javorová dostiha](./media/saas-standaloneapp-provision-and-catalog/redmapleracing.png)

Pak můžete zkontrolovat nové prostředky vytvořené v Azure Portal.

   ![zdroje červené javorové dostihy](./media/saas-standaloneapp-provision-and-catalog/redmapleracing-resources.png)


## <a name="to-stop-billing-delete-resource-groups"></a>Pokud chcete zastavit fakturaci, odstraňte skupiny prostředků.

Po dokončení průzkumu ukázky odstraňte všechny skupiny prostředků, které jste vytvořili, abyste zastavili související fakturaci.

## <a name="additional-resources"></a>Další zdroje informací

- Další informace o aplikacích pro více tenantů SaaS Database najdete v tématu [vzory návrhu pro víceklientské aplikace SaaS](saas-tenancy-app-design-patterns.md).

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se dozvěděli:

> [!div class="checklist"]
> * Postup nasazení samostatné aplikace SaaS lístky Wingtip Tickets
> * O serverech a databázích, které tvoří aplikaci.
> * Jak odstranit ukázkové prostředky pro zastavení související fakturace

Můžete prozkoumat, jak se katalog používá k podpoře různých scénářů pro více tenantů, a to pomocí verze databáze na straně klienta [aplikace Wingtip lístky SaaS](./saas-dbpertenant-wingtip-app-overview.md).