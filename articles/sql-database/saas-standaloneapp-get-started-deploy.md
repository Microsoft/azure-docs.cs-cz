---
title: Kurz pro jednoho tenanta SaaS – Azure SQL Database | Dokumentace Microsoftu
description: Nasazení a zkoumání SaaS aplikace samostatné jednoho tenanta, využívající Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MightyPen
ms.author: genemi
ms.reviewer: sstein
manager: craigg
ms.date: 09/19/2018
ms.openlocfilehash: 85c5ff33fbf5979dd07ab27ccf5993149151b38a
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51252407"
---
# <a name="deploy-and-explore-a-standalone-single-tenant-application-that-uses-azure-sql-database"></a>Nasazení a zkoumání samostatné aplikace jednoho tenanta, který používá Azure SQL Database

V tomto kurzu nasazení a zkoumání SaaS aplikace Wingtip Tickets ukázkové aplikace vyvinuté pomocí samostatné aplikace nebo aplikace na tenanta, vzor.  Aplikace je navržená tak, aby ukazovala funkce služby Azure SQL Database, které zjednodušují povolení scénářů SaaS s více tenanty.

Samostatná aplikace nebo aplikace na tenanta vzor nasadí instance aplikace pro každého tenanta.  Každá aplikace je nakonfigurovaná pro konkrétního tenanta a nasazených ve skupině samostatné prostředky Azure. Více instancí aplikace připravené k poskytování řešení více tenantů. Tento model je nejvhodnější pro menší počet klientů, kde je hlavní prioritou izolaci klientů. Azure nabízí partner programy, které umožňují prostředkům má být nasazen do předplatného klienta a spravované poskytovatelem služby jménem klienta. 

V tomto kurzu se naučíte se nasadit tři samostatné aplikace pro tři tenanty do vašeho předplatného Azure.  Máte plný přístup k prozkoumání a pracovat jednotlivé součásti aplikace.

Zdrojový kód a správu skriptů aplikace jsou k dispozici v [WingtipTicketsSaaS StandaloneApp](https://github.com/Microsoft/WingtipTicketsSaaS-StandaloneApp) úložiště GitHub. Aplikace byla vytvořena pomocí sady Visual Studio 2015 a není úspěšně otevřete a kompilace v sadě Visual Studio 2017 bez aktualizace.


V tomto kurzu se dozvíte:

> [!div class="checklist"]
> * Postup nasazení samostatné aplikace Wingtip Tickets SaaS.
> * Kde lze získat zdrojový kód aplikace a skripty pro správu.
> * O serverech a databází, které tvoří aplikaci.

Další kurzy budou vydané. Že vám umožní prozkoumejte celou řadu scénářů správy založené na tomto modelu aplikací.   

## <a name="deploy-the-wingtip-tickets-saas-standalone-application"></a>Nasazení samostatného SaaS aplikaci Wingtip Tickets

Nasazení aplikace pro tři zadané klienty:

1. Klikněte na každý modrá **nasadit do Azure** tlačítko otevře šablonu nasazení v [webu Azure portal](https://portal.azure.com). Každá šablona vyžaduje dvě hodnoty parametrů; název pro novou skupinu prostředků a uživatelskému jménu, která odlišuje od ostatních nasazení aplikace tohoto nasazení. Dalším krokem poskytuje podrobnosti pro nastavení tyto hodnoty.<br><br>
    <a href="https://aka.ms/deploywingtipsa-contoso" target="_blank"><img style="vertical-align:middle" src="media/saas-standaloneapp-get-started-deploy/deploy.png"/></a> &nbsp; **Contoso koncertní Hall**
<br><br>
    <a href="https://aka.ms/deploywingtipsa-dogwood" target="_blank"><img style="vertical-align:middle" src="media/saas-standaloneapp-get-started-deploy/deploy.png"/></a> &nbsp; **Svída Dojo**
<br><br>
    <a href="https://aka.ms/deploywingtipsa-fabrikam" target="_blank"><img style="vertical-align:middle" src="media/saas-standaloneapp-get-started-deploy/deploy.png"/></a> &nbsp; **Fabrikam Jazz Club**

2. Zadejte požadované hodnoty parametrů pro každé nasazení.

    > [!IMPORTANT]
    > Některé ověřování a server brány firewall jsou pro demonstrační účely záměrně nezabezpečené. **Vytvořit novou skupinu prostředků** pro každé nasazení aplikace.  Nepoužívejte stávající skupiny prostředků. Tato aplikace nebo všechny prostředky, které vytvoří, nepoužívejte pro produkční prostředí. Odstraňte všechny skupiny prostředků, když jste hotovi s aplikacemi k zastavení souvisejícího účtování.

    Je nejvhodnější použít jenom malá písmena, číslice a pomlčky v názvech zdrojů.
    * Pro **skupiny prostředků**, vyberte možnost vytvořit nový a potom zadejte název pro skupinu prostředků, malá písmena. **Wingtip-sa -\<venueName\>-\<uživatele\>**  je doporučený vzor.  Pro \<venueName\>, nahraďte název příslušností bez mezer. Pro \<uživatele\>, nahraďte hodnotu uživatele z následujícího seznamu.  V tomto modelu může být názvy skupin prostředků *wingtip-sa-contosoconcerthall-af1*, *wingtip-sa-dogwooddojo-af1*, *wingtip-sa-fabrikamjazzclub-af1*.
    * Vyberte **umístění** z rozevíracího seznamu.

    * Pro **uživatele** – doporučujeme hodnotu krátký uživatele, třeba vaše iniciály, a číslice: například *af1*.


3. **Nasaďte aplikaci**.

    * Klikněte na tlačítko souhlas s podmínkami a ujednáními.
    * Klikněte na **Koupit**.

4. Monitorování stavu všech tří nasazení kliknutím **oznámení** (ikona zvonku napravo od vyhledávacího pole). Nasazení aplikace trvá přibližně 5 minut.


## <a name="run-the-applications"></a>Spuštění aplikací

Aplikace prezentuje místa, které jsou hostiteli události.  Místa jsou klienty aplikace. Každé místo dostane individuální webu výpis svých akcí a prodej lístků. Typy míst zahrnují koncertní sály, jazzové kluby a sportovní kluby. V ukázce Určuje typ místa fotografie pozadí na webu místa.   V aplikaci samostatný model má každé místo instance samostatné aplikace s vlastní samostatná databáze SQL.

1. Otevřete stránku události pro každé tři tenantů v samostatných prohlížeče karty:

    - http://events.contosoconcerthall.&lt; Uživatel&gt;. trafficmanager.net
    - http://events.dogwooddojo.&lt; Uživatel&gt;. trafficmanager.net
    - http://events.fabrikamjazzclub.&lt; Uživatel&gt;. trafficmanager.net

    (V každé adrese URL nahradit &lt;uživatele&gt; s hodnotou uživatele vašeho nasazení.)

   ![Události](./media/saas-standaloneapp-get-started-deploy/fabrikam.png)

K řízení distribuce příchozích požadavků, tato aplikace používá [ *Azure Traffic Manager*](../traffic-manager/traffic-manager-overview.md). Každá instance aplikace specifickým pro tenanta zahrnuje název tenanta jako část názvu domény v adrese URL. Všechny tenantů zahrnují konkrétní adresy URL **uživatele** hodnotu. Adresy URL použijte následující formát:
- http://events.&lt; venuename&gt;.&lt; Uživatel&gt;. trafficmanager.net

Každý tenant databáze **umístění** je zahrnuta v aplikaci nastavení odpovídající nasazené aplikace.

V produkčním prostředí, obvykle můžete vytvořit záznam CNAME DNS k [ *nasměrování internetové domény společnosti* ](../traffic-manager/traffic-manager-point-internet-domain.md) na adresu URL profilu traffic Manageru.


## <a name="explore-the-servers-and-tenant-databases"></a>Zkoumání serverů a databází tenantů

Pojďme se podívat na některé z nasazených prostředků:

1. V [webu Azure portal](http://portal.azure.com), přejděte na seznam skupin prostředků.
2. Měli byste vidět tři tenanta skupiny prostředků.
3. Otevřít **wingtip-sa-fabrikam -&lt;uživatele&gt;**  skupinu prostředků, která obsahuje prostředky pro nasazení společnosti Fabrikam Jazz Club.  **Fabrikamjazzclub -&lt;uživatele&gt;**  obsahuje server **fabrikamjazzclub** databáze.

Všechny databáze tenantů jsou 50 DTU *samostatné* databáze.

## <a name="additional-resources"></a>Další zdroje informací:

<!--
* Additional [tutorials that build on the Wingtip SaaS application](sql-database-wtp-overview.md#sql-database-wingtip-saas-tutorials)
* To learn about elastic pools, see [*What is an Azure SQL elastic pool*](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-pool)
* To learn about elastic jobs, see [*Managing scaled-out cloud databases*](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-jobs-overview)
-->

- Další informace o víceklientské aplikace SaaS, naleznete v tématu [vzory návrhu pro víceklientské aplikace SaaS](saas-tenancy-app-design-patterns.md).

 
## <a name="delete-resource-groups-to-stop-billing"></a>Odstranění skupiny prostředků to ukončit účtování ##

Po dokončení pomocí ukázky, odstraňte všechny skupiny prostředků vytvořené jak ukončit fakturaci související.

## <a name="next-steps"></a>Další postup

V tomto kurzu jste se dozvěděli:

> [!div class="checklist"]
> * Postup nasazení samostatné aplikace Wingtip Tickets SaaS.
> * O serverech a databází, které tvoří aplikaci.
> * Jak odstranit ukázkové prostředky k zastavení souvisejícího účtování.

Zkuste [Provision and Catalog](saas-standaloneapp-provision-and-catalog.md) kurz, ve kterém bude prozkoumat použití katalogu tenantů, která umožňuje celou řadu scénářů napříč tenanty, jako je schéma analytics správy a klientů.
 

