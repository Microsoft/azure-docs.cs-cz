---
title: Kurz SaaS pro více tenantů – Azure SQL Database | Dokumentace Microsoftu
description: Zřízení a katalogizace nových tenantů pomocí vzoru aplikace s samostatné
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: billgib
manager: craigg
ms.date: 01/31/2018
ms.openlocfilehash: 92a1745f8da9783a22c7cbf417acb0709759f41c
ms.sourcegitcommit: 715813af8cde40407bd3332dd922a918de46a91a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "47054297"
---
# <a name="provision-and-catalog-new-tenants-using-the--application-per-tenant-saas-pattern"></a>Zřízení a katalogizace nových tenantů v aplikaci na model SaaS tenanta

Tento článek popisuje zřízení a katalogizace nových tenantů pomocí samostatné aplikace na tenanta model SaaS.
Tento článek obsahuje dvě hlavní části:
* Koncepční informace o zřízení a katalogizace nových tenantů
* Kurz, který zvýrazňuje ukázkový kód Powershellu, který provede zřízení a katalogizace
    * V tomto kurzu použijete Wingtip Tickets ukázkové SaaS aplikace upravených pro samostatnou aplikaci za vzor tenanta.

## <a name="standalone-application-per-tenant-pattern"></a>Samostatná aplikace za vzor tenanta
Samostatná aplikace za vzor tenanta je jedním z několika způsoby pro víceklientské aplikace SaaS.  V tomto vzoru se zřizuje samostatnou aplikaci pro každého tenanta. Aplikace se skládá z úrovně součásti aplikace a SQL database.  Každou klientskou aplikaci je možné nasadit v rámci předplatného od příslušného dodavatele.  Alternativně Azure nabízí [spravovaných aplikací programu](https://docs.microsoft.com/azure/managed-applications/overview) v které aplikace je možné nasadit v předplatném tenanta a spravuje dodavatele jménem klienta. 

   ![vzor aplikací na tenanta](media/saas-standaloneapp-provision-and-catalog/standalone-app-pattern.png)

Při nasazování aplikace pro klienta, aplikace a databáze jsou zřízené v nové skupiny prostředků vytvořené pro tenanta.  Pomocí samostatných skupin prostředků izoluje jednotlivé tenanty aplikační prostředky a umožňuje je spravovat nezávisle. V rámci jednotlivých skupin prostředků je každá instance aplikace nakonfigurovaná pro přístup k jeho odpovídající databázi přímo.  Tento model připojení se liší od jiných vzorků, které používají katalogu pro zprostředkovatele připojení mezi aplikací a databáze.  A protože neexistuje žádný prostředek pro sdílení obsahu, všechny databáze tenantů musí být zřízená s dostatkem prostředků pro zpracování jeho zátěž ve špičce. Tento model se obvykle dá použít pro aplikace SaaS s menším počtem klientů, ve kterých je důrazu na tenanta izolace a nižší důraz na náklady na prostředky.  

## <a name="using-a-tenant-catalog-with-the-application-per-tenant-pattern"></a>Použití katalogu tenanta s aplikaci za vzor tenanta
Každý tenant aplikace a databáze jsou plně oddělené, různé scénáře analýzy a správy fungovat mezi tenanty.  Například použití změnu schématu pro novou verzi aplikace vyžaduje změny schématu všechny databáze tenantů. Scénáře vytváření sestav a analýzy může také vyžadovat přístup na všech databázích tenantů bez ohledu na to, kde jsou nasazeny.

   ![vzor aplikací na tenanta](media/saas-standaloneapp-provision-and-catalog/standalone-app-pattern-with-catalog.png)

Tenant katalog obsahuje mapování mezi identifikátor tenanta a databází tenantů, což identifikátor pro přeložit na serveru a název databáze.  V aplikaci SaaS aplikace Wingtip identifikátor tenanta je vypočítán jako hodnotu hash názvu tenanta, i když by bylo možné použít jiná schémata.  Zatímco samostatné aplikace nemusí katalogu pro správu připojení, katalog lze použít k určení rozsahu další akce pro sadu databází tenantů. Elastický dotaz můžete například použít katalogu určit sadu databází, ve kterých jsou distribuované dotazy pro vytváření sestav napříč tenanty.

## <a name="elastic-database-client-library"></a>Klientská knihovna Elastic Database
Katalog je v ukázkové aplikaci Wingtip implementované funkcí správy horizontálních oddílů [Klientská knihovna Elastic Database](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-database-client-library) (EDCL).  Knihovny umožňuje aplikaci vytvářet, spravovat a používat mapy horizontálních oddílů, který je uložen v databázi. V ukázce Wingtip Tickets je uložené v katalogu v *katalogu tenanta* databáze.  Horizontální oddíl mapuje klíče tenanta do horizontálního oddílu (databáze) ve které je uložený data vašeho tenanta.  EDCL funkcí Správa *mapy horizontálních oddílů globální* ukládají do tabulek v *katalogu tenanta* databáze a *mapy horizontálních oddílů místní* uložených v jednotlivých horizontálních oddílech.

EDCL funkce lze volat z aplikací nebo skriptů prostředí PowerShell k vytváření a správě položek v mapě horizontálních oddílů. Další funkce EDCL slouží k načtení nastavení horizontálními oddíly nebo se připojte ke správné databázi pro danou klíč tenanta. 
    
> [!IMPORTANT] 
> Neprovádět úpravy dat v databázi katalogu nebo mapy horizontálních oddílů místní databáze tenanta přímo. Kvůli vysokému riziku poškození dat nepodporuje přímé aktualizace. Místo toho upravte mapování dat pomocí rozhraní API EDCL pouze.

## <a name="tenant-provisioning"></a>Zřizování klientů 
Každý tenant vyžaduje novou skupinu prostředků Azure, kterou musí vytvořit předtím, než je možné zřizovat prostředky v ní. Jakmile se skupina prostředků existuje, šablony Azure Resource Manageru je možné nasadit komponenty aplikace a databáze, a potom nakonfigurujte připojení k databázi. Inicializace schématu databáze, můžete šablonu import souboru bacpac.  Alternativně lze vytvořit databázi jako kopii databáze "Šablona".  Databáze je pak dále aktualizován s daty počáteční míst a zaregistrované v katalogu.

## <a name="tutorial"></a>Kurz

V tomto kurzu se naučíte:
* Zřízení katalogu
* Zaregistrujte ukázkové databáze tenanta, které jste nasadili dříve v katalogu
* Zřízení dalších tenantů a zaregistrujete ho v katalogu

Šablony Azure Resource Manageru se používá k nasazení a konfigurace aplikace, vytvoření databáze tenanta a pak import souboru bacpac k inicializaci ho. Požadavek na importování může být zařazené do fronty pro několik minut, než je actioned.

Na konci tohoto kurzu máte sadu aplikací samostatné tenanty, každá databáze zaregistrované v katalogu.

## <a name="prerequisites"></a>Požadavky
Předpokladem dokončení tohoto kurzu je splnění následujících požadavků: 
* Prostředí Azure PowerShell je nainstalované. Podrobnosti najdete v článku [Začínáme s prostředím Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).
* Jsou tři ukázkové aplikace tenanta nasazené. Tyto aplikace nasadit za méně než pět minut, najdete v článku [nasazení a zkoumání aplikaci Wingtip Tickets SaaS samostatný model](https://docs.microsoft.com/azure/sql-database/saas-standaloneapp-get-started-deploy).

## <a name="provision-the-catalog"></a>Zřízení katalogu
V této úloze se dozvíte, jak ke zřízení katalogu použili k registraci ve všech databázích tenantů. Vaším úkolem je: 
* **Zřízení databáze katalogu** pomocí šablony Azure resource Manageru. Databáze je inicializován pomocí import souboru bacpac.  
* **Registrace tenanta ukázkových aplikací** , který jste předtím nasadili.  Každý tenant je zaregistrovaný používá klíč vytvořený z hodnoty hash názvu tenanta.  Název tenanta je také uložena v tabulce rozšíření v katalogu.

1. Otevřít v PowerShell ISE *...\Learning Modules\UserConfig.psm* a aktualizovat **\<uživatele\>** hodnotu k hodnotě nepoužívá při nasazení tři ukázkové aplikace.  **Uložte soubor**.  
1. Otevřít v PowerShell ISE *...\Learning Modules\ProvisionTenants\Demo-ProvisionAndCatalog.ps1* a nastavte **$Scenario = 1**. Nasaďte katalogu tenanta a zaregistrujte předem definovaných tenantů.

1. Přidejte zarážku vložením ukazatel myši kamkoli na řádek, který říká, `& $PSScriptRoot\New-Catalog.ps1`a potom stiskněte klávesu **F9**.

    ![nastavením zarážky pro trasování](media/saas-standaloneapp-provision-and-catalog/breakpoint.png)

1. Spuštění skriptu stisknutím kombinace kláves **F5**. 
1.  Po spuštění skriptu se zastaví na zarážce, stiskněte klávesu **F11** krok do New-Catalog.ps1 skriptu.
1.  Trasování provádění skriptu pomocí možnosti nabídky ladění, F10 a F11, Krokovat přes nebo do volané funkce.
    *   Další informace o ladění skriptů Powershellu najdete v tématu [tipy pro práci s a ladění skriptů Powershellu](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise).

Po dokončení skriptu budou existovat v katalogu a ukázkoví tenanti se zaregistruje. 

Nyní se podívejte na prostředky, které jste vytvořili.

1. Otevřít [webu Azure portal](https://portal.azure.com/) a procházet skupiny prostředků.  Otevřít **wingtip-sa-catalog -\<uživatele\>**  prostředek skupiny a poznamenejte si katalog serveru a databáze.
1. Otevřete portál a vyberte databázi *Průzkumník dat* z nabídky na levé straně.  Klikněte na příkaz Login a pak zadejte heslo = **P@ssword1**.


1. Prozkoumejte schéma *tenantcatalog* databáze.  
   * Objekty v `__ShardManagement` schématu je k dispozici v klientské knihovně Elastic Database.
   * `Tenants` Tabulky a `TenantsExtended` zobrazení jsou rozšíření přidat ve vzorku, které ukazují, jak můžete rozšířit katalogu zvyšujících hodnotu.
1. Spusťte dotaz, `SELECT * FROM dbo.TenantsExtended`.          

   ![Průzkumník dat](media/saas-standaloneapp-provision-and-catalog/data-explorer-tenantsextended.png)

    Jako alternativu k použití Průzkumníku dat můžete připojit k databázi z SQL Server Management Studio. Provedete to tak, připojte se k serveru aplikace wingtip- 

    
    Všimněte si, že byste neměli upravovat data přímo v katalogu - vždy používat rozhraní API pro správu horizontálních oddílů. 

## <a name="provision-a-new-tenant-application"></a>Zřízení nového tenanta aplikace

V této úloze se dozvíte, jak zřídit jednoho tenanta aplikaci. Vaším úkolem je:  
* **Vytvořit novou skupinu prostředků** pro příslušného tenanta. 
* **Zřídit aplikace a databáze** do nové skupiny prostředků pomocí šablony Azure resource Manageru.  Tato akce zahrnuje inicializace databáze s společné schéma a referenční data importem souboru bacpac. 
* **Inicializace databáze s informace o základní tenanta**. Tato akce zahrnuje určení typu míst, který určuje fotografie používá jako pozadí na webu jeho události. 
* **Registrace databáze v databázi katalogu**. 

1. Otevřít v PowerShell ISE *...\Learning Modules\ProvisionTenants\Demo-ProvisionAndCatalog.ps1* a nastavte **$Scenario = 2**. Nasaďte katalogu tenanta a zaregistrujte předem definovaných tenantů

1. Přidání zarážky ve skriptu vložením ukazatel myši kamkoli na řádku 49, že `& $PSScriptRoot\New-TenantApp.ps1`a potom stiskněte klávesu **F9**.
1. Spuštění skriptu stisknutím kombinace kláves **F5**. 
1.  Po spuštění skriptu se zastaví na zarážce, stiskněte klávesu **F11** krok do New-Catalog.ps1 skriptu.
1.  Trasování provádění skriptu pomocí možnosti nabídky ladění, F10 a F11, Krokovat přes nebo do volané funkce.

Po zřízení tenanta se otevře web události nového tenanta.

   ![Red maple racing](media/saas-standaloneapp-provision-and-catalog/redmapleracing.png)

Pak si můžete prohlédnout nové prostředky vytvořené v rámci webu Azure portal. 

   ![Red maple závodní prostředky](media/saas-standaloneapp-provision-and-catalog/redmapleracing-resources.png)


## <a name="to-stop-billing-delete-resource-groups"></a>To ukončit účtování, odstranění skupiny prostředků ##

Po dokončení ukázky, odstraňte všechny skupiny prostředků vytvořené jak ukončit fakturaci související.

## <a name="additional-resources"></a>Další zdroje informací:

- Další informace o víceklientské aplikace SaaS databáze najdete v tématu [vzory návrhu pro víceklientské aplikace SaaS](saas-tenancy-app-design-patterns.md).

## <a name="next-steps"></a>Další postup

V tomto kurzu jste se dozvěděli:

> [!div class="checklist"]
> * Postup nasazení samostatné aplikace Wingtip Tickets SaaS.
> * O serverech a databází, které tvoří aplikaci.
> * Jak odstranit ukázkové prostředky k zastavení souvisejícího účtování.

Můžete prozkoumat, jak se používá v katalogu pro podporu různých scénářů napříč tenanty pomocí verze databáze na tenanta [aplikace SaaS aplikace Wingtip Tickets](https://docs.microsoft.com/azure/sql-database/saas-dbpertenant-wingtip-app-overview).  
