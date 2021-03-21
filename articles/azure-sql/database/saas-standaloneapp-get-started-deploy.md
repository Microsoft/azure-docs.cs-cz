---
title: Kurz pro jeden tenant SaaS
description: Nasaďte a prozkoumejte samostatnou aplikaci SaaS s jedním tenantů, která používá Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 11/07/2018
ms.openlocfilehash: 896f4edb1e94c1eca06b046382727a5042375e3b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92793275"
---
# <a name="deploy-and-explore-a-standalone-single-tenant-application-that-uses-azure-sql-database"></a>Nasazení a prozkoumání samostatné aplikace pro jednoho tenanta, která používá Azure SQL Database
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

V tomto kurzu nasadíte a prozkoumáte ukázkovou aplikaci Wingtip Tickets SaaS, která byla vyvinutá pomocí samostatné aplikace, nebo podle vzoru aplikace na tenanta.  Aplikace je navržená tak, aby předvedla funkce Azure SQL Database, které zjednodušují SaaS scénáře pro víceklientské klienty.

Samostatná aplikace nebo model aplikace na tenanta nasadí instanci aplikace pro každého tenanta.  Každá aplikace je nakonfigurovaná pro konkrétního tenanta a je nasazená v samostatné skupině prostředků Azure. Víc instancí aplikace se zřizuje za účelem poskytování víceklientské řešení. Tento model je nejvhodnější pro menší počty klientů, u kterých je izolací tenanta nejvyšší prioritou. Azure má partnerské programy, které umožňují nasazení prostředků do předplatného tenanta a spravované poskytovatelem služeb na účet tenanta. 

V tomto kurzu nasadíte tři samostatné aplikace pro tři klienty do svého předplatného Azure.  Máte plný přístup k prozkoumávání a práci s jednotlivými součástmi aplikace.

Zdrojový kód aplikace a skripty pro správu jsou k dispozici v úložišti GitHub [WingtipTicketsSaaS-StandaloneApp](https://github.com/Microsoft/WingtipTicketsSaaS-StandaloneApp) . Aplikace byla vytvořena pomocí sady Visual Studio 2015 a neúspěšně otevřena a kompilována v aplikaci Visual Studio 2019 bez aktualizace.


V tomto kurzu se dozvíte:

> [!div class="checklist"]
> * Postup nasazení samostatné aplikace SaaS lístky Wingtip Tickets
> * Kde získat zdrojový kód aplikace a skripty pro správu.
> * O serverech a databázích, které tvoří aplikaci.

Budou vydány další kurzy. Umožní vám prozkoumat řadu scénářů správy na základě tohoto vzoru aplikace.   

## <a name="deploy-the-wingtip-tickets-saas-standalone-application"></a>Nasazení samostatné aplikace SaaS lístky Wingtip Tickets

Nasazení aplikace pro tři poskytnuté klienty:

1. Kliknutím na každé modré tlačítko **nasadit do Azure** otevřete šablonu nasazení v [Azure Portal](https://portal.azure.com). Každá šablona vyžaduje dvě hodnoty parametrů; název nové skupiny prostředků a uživatelské jméno, které toto nasazení odlišuje od ostatních nasazení aplikace. Další krok poskytuje podrobnosti o nastavení těchto hodnot.

   **Kohalace společnosti Contoso**   
   [![Obrázek znázorňující tlačítko s názvem "nasadit do Azure".](media/saas-standaloneapp-get-started-deploy/deploy.png)](https://aka.ms/deploywingtipsa-contoso)

   **Dogwood Dojo**   
   [![Obrázek znázorňující tlačítko s názvem "nasadit do Azure".](media/saas-standaloneapp-get-started-deploy/deploy.png)](https://aka.ms/deploywingtipsa-dogwood)

   **Fabrikam jazz – klub**   
   [![Obrázek znázorňující tlačítko s názvem "nasadit do Azure".](media/saas-standaloneapp-get-started-deploy/deploy.png)](https://aka.ms/deploywingtipsa-fabrikam)
 
2. Zadejte požadované hodnoty parametrů pro každé nasazení.

    > [!IMPORTANT]
    > Některá ověřování a brány firewall serveru jsou pro demonstrační účely záměrně nezabezpečené. **Vytvořte novou skupinu prostředků** pro každé nasazení aplikace.  Nepoužívejte existující skupinu prostředků. Nepoužívejte tuto aplikaci ani žádné prostředky, které vytvoří, pro produkční prostředí. Odstraňte všechny skupiny prostředků, až skončíte s aplikacemi, aby se zastavily související fakturace.

    Doporučuje se v názvech prostředků používat jenom malá písmena, číslice a spojovníky.
    * V poli **Skupina prostředků** vyberte vytvořit novou a zadejte pro skupinu prostředků malý název. **Wingtip-SA- \<venueName\> - \<user\>** je doporučený vzor.  V případě \<venueName\> nahraďte název místa bez mezer. V případě \<user\> nahraďte hodnotu uživatele níže.  V tomto vzoru můžou být názvy skupin prostředků *Wingtip-SA-contosoconcerthall-AF1*, *Wingtip-SA-dogwooddojo-AF1*, *Wingtip-SA-fabrikamjazzclub-AF1*.
    * Z rozevíracího seznamu vyberte **umístění** .

    * Pro **uživatele** – doporučujeme krátkou hodnotu uživatele, jako je například vaše iniciály a číslice: například *AF1*.


3. **Nasaďte aplikaci**.

    * Kliknutím souhlasíte s podmínkami a ujednáními.
    * Klikněte na **Koupit**.

4. Kliknutím na **oznámení** (ikona zvonku napravo od vyhledávacího pole) Sledujte stav všech tří nasazení. Nasazení aplikací trvá přibližně pět minut.


## <a name="run-the-applications"></a>Spuštění aplikací

Aplikace prezentuje místa, která hostují události.  Místo jsou klienty aplikace. Každé místo získá přizpůsobený web k vypsání svých událostí a prodeji lístků. Mezi typy míst patří kosálye, klub nápadný a sportovní klub. V ukázce typ místa určuje fotografii na pozadí zobrazené na webu místa konání.   V samostatném modelu aplikace má každé místo samostatnou instanci aplikace s vlastním samostatným Azure SQL Database.

1. Otevřete stránku události pro každého ze tří tenantů na samostatných záložkách prohlížeče:

   - http://events.contosoconcerthall.&lt; User &gt; . trafficmanager.NET
   - http://events.dogwooddojo.&lt; User &gt; . trafficmanager.NET
   - http://events.fabrikamjazzclub.&lt; User &gt; . trafficmanager.NET

     (V každé adrese URL nahraďte &lt; uživatele &gt; hodnotou uživatele vašeho nasazení.)

   ![Události](./media/saas-standaloneapp-get-started-deploy/fabrikam.png)

Aby bylo možné řídit distribuci příchozích požadavků, aplikace používá [*Azure Traffic Manager*](../../traffic-manager/traffic-manager-overview.md). Každá instance aplikace konkrétního tenanta obsahuje název tenanta jako součást názvu domény v adrese URL. Všechny adresy URL tenanta obsahují konkrétní hodnotu **uživatele** . Adresy URL následují po následujícím formátu:
- http://events.&lt; promístit &gt; . &lt; User &gt; . trafficmanager.NET

**Umístění** databáze každého tenanta je zahrnuté v nastavení aplikace odpovídající nasazené aplikace.

V produkčním prostředí obvykle vytvoříte záznam DNS CNAME k [*nasměrování internetové domény společnosti*](../../traffic-manager/traffic-manager-point-internet-domain.md) na adresu URL profilu Traffic Manageru.


## <a name="explore-the-servers-and-tenant-databases"></a>Prozkoumejte servery a databáze tenantů

Pojďme se podívat na některé z nasazených prostředků:

1. V [Azure Portal](https://portal.azure.com)přejděte do seznamu skupin prostředků.
2. Měli byste vidět tři skupiny prostředků tenanta.
3. Otevřete skupinu prostředků **Wingtip-SA-Fabrikam &lt; - &gt; User** , která obsahuje prostředky pro nasazení klubu společnosti Fabrikam Jazz.  **Fabrikamjazzclub- &lt; &gt; User** Server obsahuje databázi **fabrikamjazzclub** .

Každá databáze tenanta je *samostatná* databáze 50 DTU.

## <a name="additional-resources"></a>Další zdroje informací

<!--
* Additional [tutorials that build on the Wingtip SaaS application](./saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* To learn about elastic pools, see [*What is an Azure SQL elastic pool*](elastic-pool-overview.md)
* To learn about elastic jobs, see [*Managing scaled-out cloud databases*](./elastic-jobs-overview.md)
-->

- Další informace o víceklientské aplikacích SaaS najdete v tématu [vzory návrhu pro víceklientské aplikace SaaS](saas-tenancy-app-design-patterns.md).

 
## <a name="delete-resource-groups-to-stop-billing"></a>Odstraněním skupin prostředků zastavte fakturaci ##

Po dokončení používání ukázky odstraňte všechny skupiny prostředků, které jste vytvořili, abyste zastavili související fakturaci.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se dozvěděli:

> [!div class="checklist"]
> * Postup nasazení samostatné aplikace SaaS lístky Wingtip Tickets
> * O serverech a databázích, které tvoří aplikaci.
> * Jak odstranit ukázkové prostředky pro zastavení související fakturace

V dalším kroku si vyzkoušíte kurz [zřízení a katalog](saas-standaloneapp-provision-and-catalog.md) , ve kterém budete zkoumat použití katalogu tenantů, který umožňuje využít řadu scénářů mezi klienty, jako je Správa schématu a analýza klientů.
