---
title: Kurz SaaS víceklientské – Azure SQL Database | Microsoft Docs
description: Zřizování a katalog nové klienty pomocí samostatné aplikace vzoru
keywords: kurz k sql database
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.custom: SaaS
ms.topic: conceptual
ms.date: 01/31/2018
ms.author: billgib
ms.openlocfilehash: 0f2495ddc5d5053582d67bd44cdf80d018f79e42
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34646149"
---
# <a name="provision-and-catalog-new-tenants-using-the--application-per-tenant-saas-pattern"></a>Zřizování a katalog nové klienty používání aplikace pro každého klienta vzor SaaS

Tento článek se zabývá zřizování a nové klienty pomocí samostatné aplikace každého klienta SaaS vzor do katalogu.
Tento článek má dvě hlavní části:
* Koncepční informace o zřizování a nové klienty do katalogu
* Kurz, který označuje ukázkový kód prostředí PowerShell, který provede zřizování a do katalogu
    * Tento kurz používá aplikace SaaS ukázka Wingtip lístky přizpůsobit samostatné aplikace na vzor klienta.

## <a name="standalone-application-per-tenant-pattern"></a>Samostatné aplikace za klienta vzor
Samostatné aplikace na vzor klienta je jedním z několika vzory pro víceklientské aplikace SaaS.  V tomto vzoru se zřizuje samostatné aplikace pro každého klienta. Aplikace se skládá z úrovně součásti aplikace a SQL database.  Každá aplikace klienta se dá nasadit v rámci předplatného od dodavatele.  Alternativně Azure nabízí [spravovaných aplikací programu](https://docs.microsoft.com/azure/managed-applications/overview) ve kterém aplikace lze nasadit v rámci předplatného klienta a spravovat dodavatelem jménem klienta. 

   ![vzor aplikace za klienta](media/saas-standaloneapp-provision-and-catalog/standalone-app-pattern.png)

Pokud nasazujete aplikaci pro klienta, aplikace a databáze jsou zřízené v novou skupinu prostředků vytvořen pro klienta.  Použití skupin prostředků samostatné izoluje prostředky aplikace každého klienta a umožňuje je spravovat nezávisle. V rámci jednotlivých skupin prostředků je každá instance aplikace nakonfigurovaná pro přístup k jeho odpovídající databázi přímo.  Tento model připojení se liší od jiných vzorů, které použití katalogu pro zprostředkovatele připojení mezi aplikací a databází.  A protože neexistuje žádný prostředek sdílení, každou databázi klienta musí být zřízená s dostatkem prostředků pro zpracování jeho zátěž ve špičce. Tento vzor obvykle má být použit pro aplikace SaaS ve službě méně klientů, pokud existuje velkému důrazu na klienta, izolace a méně důraz na náklady na zdroje.  

## <a name="using-a-tenant-catalog-with-the-application-per-tenant-pattern"></a>Pomocí klienta katalog aplikací podle vzoru klienta
Každý klient aplikace a databáze jsou plně izolovaném, může pracovat různé správy a scénáře analýzy mezi klienty.  Například použití změny schématu pro nové verze aplikace vyžaduje změny schématu databáze každého klienta. Scénáře vytváření sestav a analýzy může také vyžadovat přístup pro všechny databáze klienta bez ohledu na to, kde byla nasazena.

   ![vzor aplikace za klienta](media/saas-standaloneapp-provision-and-catalog/standalone-app-pattern-with-catalog.png)

Katalog klienta obsahuje mapování mezi identifikátor klienta a databází klienta, což identifikátor být vyřešen k serveru a název databáze.  V aplikaci Wingtip SaaS identifikátor klienta se počítá jako hodnota hash název klienta, přestože by bylo možné použít jiná schémata.  Při samostatné aplikace nepotřebujete katalogu ke správě připojení, katalog lze použít k určení rozsahu další akce sadu databází, klienta. Elastické dotazu lze například použít katalogu k určení sady databází, přes které jsou distribuovány dotazy pro vytváření sestav napříč klienta.

## <a name="elastic-database-client-library"></a>Klientská knihovna Elastic Database
V ukázkové aplikaci Wingtip katalogu je implementováno modulem horizontálního oddílu funkcím pro správu [klientské knihovny pro elastické databáze](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-database-client-library) (EDCL).  Knihovny umožňuje aplikaci vytvářet, spravovat a použít horizontálního oddílu mapu, která je uložená v databázi. V ukázce Wingtip lístky katalog je uložen v *klienta katalogu* databáze.  Horizontálního oddílu mapuje klíče tenanta k horizontálního oddílu (databáze) ve kterém jsou uložena data tohoto klienta.  Spravovat funkce EDCL *mapy globální horizontálních* ukládat do tabulek v *katalogu klienta* databáze a *mapy místní horizontálních* uložené v každé horizontálního oddílu.

Funkce EDCL lze volat z aplikací a skriptů prostředí PowerShell k vytváření a správě položky v mapě horizontálního oddílu. Další funkce EDCL slouží k načtení sadu horizontálních oddílů nebo se připojit ke správné databázi pro daný klíč klienta. 
    
> [!IMPORTANT] 
> Data v katalogu databáze nebo mapovat místní horizontálního oddílu v databázích klienta přímo upravovat. Přímá aktualizace nejsou podporovány z důvodu vysoké riziko poškození dat. Místo toho upravte mapování dat pouze pomocí rozhraní API EDCL.

## <a name="tenant-provisioning"></a>Zřizování klienta 
Každý klient vyžaduje novou skupinu prostředků Azure, které musí být vytvořen před prostředky se dá zřídit v něm. Jakmile skupině prostředků existuje, šablonu Správa prostředků Azure slouží k nasazení součásti aplikace a databáze a pak nakonfigurujte připojení k databázi. K chybě při inicializaci schématu databáze, můžete importovat šablony souboru bacpac soubor.  Nebo databázi lze vytvořit jako kopii databáze, šablony'.  Databáze je pak další aktualizovány místní počáteční příslušnost data a zaregistrovány v katalogu.

## <a name="tutorial"></a>Kurz

V tomto kurzu se naučíte:
* Zřídit katalog
* Zaregistrovat ukázkové databáze klienta, které jste nasadili dříve v katalogu
* Zřídit další klienta a její registrace v katalogu

Šablonu Azure Resource Manager slouží k nasazení a konfigurace aplikace, vytvořit databázi klienta a následným importem souboru bacpac soubor, který chcete provést jeho inicializaci. Žádost o import může ve frontě pro několik minut, než je reagovali.

Na konci tohoto kurzu máte sadu samostatné klienta aplikace, s každou databázi zaregistrovány v katalogu.

## <a name="prerequisites"></a>Požadavky
Předpokladem dokončení tohoto kurzu je splnění následujících požadavků: 
* Prostředí Azure PowerShell je nainstalované. Podrobnosti najdete v článku [Začínáme s prostředím Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).
* Tři ukázkových aplikací klienta jsou nasazeny. K nasazení těchto aplikací za méně než pět minut, naleznete v [nasazení a seznamte se s vzoru Wingtip lístky SaaS samostatné aplikace](https://docs.microsoft.com/azure/sql-database/saas-standaloneapp-get-started-deploy).

## <a name="provision-the-catalog"></a>Zřídit katalogu
V této úloze zjistěte, jak zřídit katalogu používá k registraci všech databází klienta. Vaším úkolem je: 
* **Zřídit databázi katalogu** pomocí šablony správu prostředků Azure. Databáze je inicializován importováním souboru bacpac souboru.  
* **Zaregistrovat ukázkových aplikací klienta** který jste dříve nasadili.  Každý klient je zaregistrován pomocí klíče vytvářejí na základě hodnota hash název klienta.  Název klienta je také uložené v tabulce rozšíření v katalogu.

1. V prostředí PowerShell ISE otevřete *...\Learning Modules\UserConfig.psm* a aktualizovat **\<uživatele\>** hodnotu s hodnotou nepoužívá při nasazení tři ukázkové aplikace.  **Uložte soubor**.  
1. V prostředí PowerShell ISE otevřete *...\Learning Modules\ProvisionTenants\Demo-ProvisionAndCatalog.ps1* a nastavte **$Scenario = 1**. Nasazení klienta katalogu a zaregistrujte předem definované klienty.

1. Přidejte zarážku kdekoli umístěním kurzor na řádek s informacemi o tom, `& $PSScriptRoot\New-Catalog.ps1`a potom stiskněte klávesu **F9**.

    ![nastavení zarážek pro trasování](media/saas-standaloneapp-provision-and-catalog/breakpoint.png)

1. Spusťte skript stisknutím **F5**. 
1.  Po spuštění skriptu se zastaví u zarážky, stiskněte klávesu **F11** krokem do nového Catalog.ps1 skriptu.
1.  Trasování pomocí nabídky možností ladění, F10 a F11 provádění skriptu, aby krok přes nebo ve volání funkce.
    *   Další informace o ladění skriptů prostředí PowerShell najdete v tématu [tipy k ladění skriptů prostředí PowerShell a práce s](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise).

Po dokončení skriptu, kde budou existovat katalogu a všichni klienti ukázka níž bude zaregistrována. 

Nyní se podívejte na prostředky, které jste vytvořili.

1. Otevřete [portál Azure](https://portal.azure.com/) a procházet skupiny prostředků.  Otevřete **wingtip-sa-katalogu -\<uživatele\>**  prostředků skupiny a poznamenejte si server katalogu a databáze.
1. Otevřít databázi v portálu a vyberte *Průzkumníku dat* z nabídky na levé straně.  Klikněte na příkaz přihlášení a pak zadejte heslo = **P@ssword1**.


1. Prozkoumejte schéma *tenantcatalog* databáze.  
   * Objekty `__ShardManagement` schématu jsou poskytované nástrojem klientské knihovny elastické databáze.
   * `Tenants` Tabulky a `TenantsExtended` zobrazení jsou přidány v ukázce rozšíření, která ukazují, jak můžete rozšířit katalogu definovat další hodnotu.
1. Spusťte dotaz, `SELECT * FROM dbo.TenantsExtended`.          

   ![Průzkumníku dat](media/saas-standaloneapp-provision-and-catalog/data-explorer-tenantsextended.png)

    Jako alternativu k použití Průzkumníku dat můžete připojit k databázi z SQL Server Management Studio. K tomuto účelu připojte k serveru wingtip- 

    
    Poznámka, byste neměli upravovat data přímo v katalogu - vždy pomocí rozhraní API pro správu horizontálního oddílu. 

## <a name="provision-a-new-tenant-application"></a>Zřídit novou aplikaci klienta

V této úloze zjistěte, jak zřídit jednoho klienta aplikace. Vaším úkolem je:  
* **Vytvořit novou skupinu prostředků** pro klienta. 
* **Zřizování aplikace a databáze** do nové skupiny prostředků pomocí šablony správu prostředků Azure.  Tato akce zahrnuje inicializace databáze s společné schéma a referenčních dat importováním souboru bacpac souboru. 
* **Inicializace databáze s informace o základní klienta**. Tato akce zahrnuje určení místo typu, který určuje fotografie použít jako na pozadí na webu jeho události. 
* **Zaregistrovat databázi v databázi katalogu**. 

1. V prostředí PowerShell ISE otevřete *...\Learning Modules\ProvisionTenants\Demo-ProvisionAndCatalog.ps1* a nastavte **$Scenario = 2**. Nasaďte katalogu klienta a zaregistrujte předem definované klientů

1. Přidat zarážky ve skriptu umístěním kurzoru kdekoli na řádku 49 s informacemi o tom, `& $PSScriptRoot\New-TenantApp.ps1`a potom stiskněte klávesu **F9**.
1. Spusťte skript stisknutím **F5**. 
1.  Po spuštění skriptu se zastaví u zarážky, stiskněte klávesu **F11** krokem do nového Catalog.ps1 skriptu.
1.  Trasování pomocí nabídky možností ladění, F10 a F11 provádění skriptu, aby krok přes nebo ve volání funkce.

Po zřízená klienta, je otevřené webu události k novému klientovi.

   ![Red javor racing](media/saas-standaloneapp-provision-and-catalog/redmapleracing.png)

Pak si můžete prohlédnout nové prostředky, které jsou vytvořené na portálu Azure. 

   ![Red javor dostihy prostředky](media/saas-standaloneapp-provision-and-catalog/redmapleracing-resources.png)


## <a name="to-stop-billing-delete-resource-groups"></a>Chcete-li zastavit fakturace, odstraňte skupiny prostředků ##

Po dokončení zkoumat ukázku odstraňte všechny skupiny prostředků, který jste vytvořili pro zastavení přidružené fakturace.

## <a name="additional-resources"></a>Další zdroje informací:

- Další informace o víceklientské SaaS databázových aplikací najdete v tématu [vzory pro víceklientské aplikace SaaS návrhu](saas-tenancy-app-design-patterns.md).

## <a name="next-steps"></a>Další postup

V tomto kurzu jste se dozvěděli:

> [!div class="checklist"]
> * Postup nasazení Wingtip lístky SaaS samostatné aplikace.
> * O servery a databáze, které tvoří aplikace.
> * Postup odstranění ukázkové prostředky zastavit související fakturace.

Můžete prozkoumat použití katalogu pro podporu různých scénářů napříč klienta pomocí verze databáze za klienta [aplikace SaaS lístky Wingtip](https://docs.microsoft.com/azure/sql-database/saas-dbpertenant-wingtip-app-overview).  
