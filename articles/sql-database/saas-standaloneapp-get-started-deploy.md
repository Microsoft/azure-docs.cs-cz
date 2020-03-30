---
title: Kurz SaaS s jedním tenantem
description: Nasaďte a prozkoumejte samostatnou aplikaci SaaS s jedním tenantem, která používá Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MightyPen
ms.author: genemi
ms.reviewer: sstein
ms.date: 11/07/2018
ms.openlocfilehash: e3afc8aa58551b995070ffaca978c8e7c8454da3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73822137"
---
# <a name="deploy-and-explore-a-standalone-single-tenant-application-that-uses-azure-sql-database"></a>Nasazení a prozkoumání samostatné aplikace s jedním klientem, která používá Azure SQL Database

V tomto kurzu nasadíte a prozkoumáte ukázkovou aplikaci Wingtip Tickets SaaS vyvinutou pomocí samostatné aplikace nebo vzoru pro jednotlivé aplikace.  Aplikace je navržena tak, aby reprezentovala funkce Azure SQL Database, které zjednodušují povolení víceklientských scénářů SaaS.

Samostatná aplikace nebo vzor aplikace na tenanta nasazuje instanci aplikace pro každého klienta.  Každá aplikace je nakonfigurovaná pro konkrétního klienta a nasazená v samostatné skupině prostředků Azure. Více instancí aplikace jsou zřízeny poskytnout řešení s více tenanty. Tento vzor je nejvhodnější pro menší čísla, tenantů, kde je nejvyšší prioritou izolace klienta. Azure má partnerské programy, které umožňují nasazení prostředků do předplatného klienta a spravované poskytovatelem služeb jménem klienta. 

V tomto kurzu nasadíte do předplatného Azure tři samostatné aplikace pro tři klienty.  Máte plný přístup k prozkoumání a práci s jednotlivými součástmi aplikace.

Zdrojový kód aplikace a skripty pro správu jsou k dispozici v úložišti [WingtipTicketsSaaS-StandaloneApp](https://github.com/Microsoft/WingtipTicketsSaaS-StandaloneApp) GitHub. Aplikace byla vytvořena pomocí Visual Studia 2015 a neúspěšně se otevře a nezkompiluje v sadě Visual Studio 2019 bez aktualizace.


V tomto kurzu se dozvíte:

> [!div class="checklist"]
> * Jak nasadit wingtip vstupenky SaaS samostatná aplikace.
> * Kde získat zdrojový kód aplikace a skripty pro správu.
> * O serverech a databázích, které tvoří aplikaci.

Budou vydány další kurzy. Umožní vám prozkoumat řadu scénářů správy založených na tomto vzoru aplikace.   

## <a name="deploy-the-wingtip-tickets-saas-standalone-application"></a>Nasazení samostatné aplikace Wingtip Tickets SaaS

Nasazení aplikace pro tři poskytnuté klienty:

1. Kliknutím na každé modré tlačítko **Nasazení do Azure** otevřete šablonu nasazení na webu Azure [Portal](https://portal.azure.com). Každá šablona vyžaduje dvě hodnoty parametrů; název pro novou skupinu prostředků a uživatelské jméno, které odlišuje toto nasazení od ostatních nasazení aplikace. V dalším kroku jsou uvedeny podrobnosti pro nastavení těchto hodnot.<br><br>
    <a href="https://aka.ms/deploywingtipsa-contoso" target="_blank"><img style="vertical-align:middle" src="media/saas-standaloneapp-get-started-deploy/deploy.png"/></a>&nbsp; **Koncertní síň Contoso**
<br><br>
    <a href="https://aka.ms/deploywingtipsa-dogwood" target="_blank"><img style="vertical-align:middle" src="media/saas-standaloneapp-get-started-deploy/deploy.png"/></a>&nbsp; **Dříno Dojo**
<br><br>
    <a href="https://aka.ms/deploywingtipsa-fabrikam" target="_blank"><img style="vertical-align:middle" src="media/saas-standaloneapp-get-started-deploy/deploy.png"/></a>&nbsp; **Jazzový klub Fabrikam**

2. Zadejte požadované hodnoty parametrů pro každé nasazení.

    > [!IMPORTANT]
    > Některé brány firewall ověřování a serveru jsou záměrně nezabezpečené pro demonstrační účely. **Vytvořte novou skupinu prostředků** pro každé nasazení aplikace.  Nepoužívejte existující skupinu prostředků. Nepoužívejte tuto aplikaci ani žádné prostředky, které vytvoří, pro výrobu. Po dokončení s aplikacemi odstraňte všechny skupiny prostředků, které zastaví související fakturaci.

    V názvech prostředků je nejvhodnější použít pouze malá písmena, čísla a pomlčky.
    * Ve **skupině Prostředků**vyberte Vytvořit nový a zadejte malý název pro skupinu prostředků. **wingtip-sa-\<\>-\<venueName\> uživatel** je doporučený vzor.  V \<případě\>venueName nahraďte název místa žádným mezerama. Pro \<\>uživatele nahraďte uživatelskou hodnotu zespodu.  S tímto vzorem, názvy zdrojů skupiny by mohly být *wingtip-sa-contosoconcerthall-af1*, *wingtip-sa-dogwooddojo-af1*, *wingtip-sa-fabrikamjazzclub-af1*.
    * V rozevíracím seznamu vyberte **umístění.**

    * Pro **uživatele** - Doporučujeme krátkou uživatelskou hodnotu, například iniciály plus číslici: například *af1*.


3. **Nasaďte aplikaci**.

    * Klikněte pro souhlas s podmínkami.
    * Klikněte na **Koupit**.

4. Sledujte stav všech tří nasazení kliknutím na **Notifications** (ikona zvonku napravo od vyhledávacího pole). Nasazení aplikací trvá přibližně pět minut.


## <a name="run-the-applications"></a>Spuštění aplikací

Aplikace představuje místa, která hostí události.  Místa jsou nájemci aplikace. Každé místo dostane personalizované webové stránky, aby seznam svých akcí a prodávat vstupenky. Místo konání zahrnuje koncertní sály, jazzové kluby a sportovní kluby. V ukázce typ místa konání určuje fotografii na pozadí zobrazenou na webových stránkách místa konání.   V modelu samostatné aplikace má každé místo samostatné instance aplikace s vlastní samostatnou databází SQL.

1. Otevřete stránku události pro každého ze tří klientů v samostatných kartách prohlížeče:

   - http://events.contosoconcerthall.&lt;uživatel&gt;.trafficmanager.net
   - http://events.dogwooddojo.&lt;uživatel&gt;.trafficmanager.net
   - http://events.fabrikamjazzclub.&lt;uživatel&gt;.trafficmanager.net

     (V každé adrese &lt;&gt; URL nahraďte uživatele uživatelskou hodnotou nasazení.)

   ![Události](./media/saas-standaloneapp-get-started-deploy/fabrikam.png)

K řízení distribuce příchozích požadavků aplikace používá [*Azure Traffic Manager*](../traffic-manager/traffic-manager-overview.md). Každá instance aplikace specifické pro klienta obsahuje název klienta jako součást názvu domény v adrese URL. Všechny adresy URL klienta obsahují **konkrétní** uživatelskou hodnotu. Adresy URL se řídí následujícím formátem:
- http://events.&lt;název&gt;místa . &lt;uživatel&gt;.trafficmanager.net

Databáze každého klienta **Umístění** je součástí nastavení aplikace odpovídající nasazené aplikace.

V produkčním prostředí obvykle vytvoříte záznam CNAME DNS, který [*nasměruje firemní internetovou doménu*](../traffic-manager/traffic-manager-point-internet-domain.md) na adresu URL profilu správce provozu.


## <a name="explore-the-servers-and-tenant-databases"></a>Prozkoumejte servery a databáze klientů

Podívejme se na některé z prostředků, které byly nasazeny:

1. Na [webu Azure Portal](https://portal.azure.com)přejděte do seznamu skupin prostředků.
2. Měli byste vidět tři skupiny prostředků klienta.
3. Otevřete skupinu prostředků **uživatele&lt;&gt; wingtip-sa-fabrikam,** která obsahuje prostředky pro nasazení fabrikam Jazz Clubu.  **Fabrikamjazzclub-&lt;uživatelský&gt; ** server obsahuje **databázi fabrikamjazzclub.**

Každá databáze klienta je *samostatná* databáze 50 DTU.

## <a name="additional-resources"></a>Další zdroje

<!--
* Additional [tutorials that build on the Wingtip SaaS application](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* To learn about elastic pools, see [*What is an Azure SQL elastic pool*](sql-database-elastic-pool.md)
* To learn about elastic jobs, see [*Managing scaled-out cloud databases*](elastic-jobs-overview.md)
-->

- Další informace o víceklientských aplikacích SaaS najdete [v tématu Návrhové vzory pro víceklientské aplikace SaaS](saas-tenancy-app-design-patterns.md).

 
## <a name="delete-resource-groups-to-stop-billing"></a>Odstranění skupin prostředků za účelem zastavení fakturace ##

Po dokončení použití ukázky odstraňte všechny skupiny prostředků, které jste vytvořili, abyste zastavili přidruženou fakturaci.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se dozvěděli:

> [!div class="checklist"]
> * Jak nasadit wingtip vstupenky SaaS samostatná aplikace.
> * O serverech a databázích, které tvoří aplikaci.
> * Jak odstranit ukázkové prostředky k zastavení související fakturace.

Dále vyzkoušejte kurz [Zřízení a katalog,](saas-standaloneapp-provision-and-catalog.md) ve kterém budete zkoumat použití katalogu klientů, který umožňuje řadu scénářů mezi tenanty, jako je správa schématu a analýzy klienta.
 

