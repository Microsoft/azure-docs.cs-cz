---
title: Kurz SaaS víceklientské – Azure SQL Database | Microsoft Docs
description: Nasazení a prozkoumejte samostatná aplikace SaaS jednoho klienta, která používá databázi SQL Azure.
keywords: kurz k sql database
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.custom: scale out apps
ms.topic: article
ms.date: 04/01/2018
ms.author: genemi
ms.openlocfilehash: 86a5bc31639cbbcdac1468f3bc2e35a547068882
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="deploy-and-explore-a-standalone-single-tenant-application-that-uses-azure-sql-database"></a>Nasazení a prozkoumejte samostatná jednoho klienta aplikace, která používá databázi SQL Azure

V tomto kurzu nasazení a prozkoumat Wingtip lístky SaaS ukázkové aplikace vyvinuté pomocí samostatné aplikace nebo aplikace na klienta, vzoru.  Aplikace je navržená ke prezentují funkce databáze SQL Azure, které zjednodušují povolení víceklientské SaaS scénáře.

Samostatné aplikace nebo aplikace na klienta vzor nasadí instance aplikace pro každého klienta.  Každá aplikace je nakonfigurované pro konkrétní klienta a nasadit ve skupině samostatné prostředků Azure. Několik instancí aplikace připravené k zajištění víceklientské řešení. Tento vzor je vhodná pro menší počet klientů, kde izolaci klientů je nejvyšší prioritou. Azure má partnera programy, které povolit materiály, které lze nasadit do předplatného klienta a spravovaný poskytovatelem služby jménem klienta. 

V tomto kurzu nasadíte tři samostatné aplikace pro tři klienty do vašeho předplatného Azure.  Máte úplný přístup k prozkoumávání a pracovat jednotlivé součásti aplikace.

Zdrojový kód a správu skripty aplikace jsou k dispozici v [WingtipTicketsSaaS StandaloneApp](https://github.com/Microsoft/WingtipTicketsSaaS-StandaloneApp) úložiště GitHub.


V tomto kurzu se dozvíte:

> [!div class="checklist"]
> * Postup nasazení Wingtip lístky SaaS samostatné aplikace.
> * Kde lze získat zdrojovému kódu aplikace a skripty pro správu.
> * O servery a databáze, které tvoří aplikace.

Další kurzy, budou vydané. Se vám umožní zkoumat celou řadu scénářů správy na základě vzoru pro tuto aplikaci.   

## <a name="deploy-the-wingtip-tickets-saas-standalone-application"></a>Nasazení aplikace SaaS samostatné Wingtip lístky

Nasazení aplikace pro tři zadaný klienty:

1. Klikněte na každou blue **nasadit do Azure** tlačítko otevřete šablonu nasazení v [portál Azure](https://portal.azure.com). Každá šablona vyžaduje dvě hodnoty parametrů; název pro novou skupinu prostředků a uživatelské jméno, která odlišuje od jiných nasazení aplikace pro toto nasazení. Dalším krokem poskytuje podrobnosti pro tyto hodnoty nastavení.<br><br>
    <a href="http://aka.ms/deploywingtipsa-contoso" target="_blank"><img style="vertical-align:middle" src="media/saas-standaloneapp-get-started-deploy/deploy.png"/></a> &nbsp; **Vzájemné součinnosti Hall contoso**
<br><br>
    <a href="http://aka.ms/deploywingtipsa-dogwood" target="_blank"><img style="vertical-align:middle" src="media/saas-standaloneapp-get-started-deploy/deploy.png"/></a> &nbsp; **Svída Dojo**
<br><br>
    <a href="http://aka.ms/deploywingtipsa-fabrikam" target="_blank"><img style="vertical-align:middle" src="media/saas-standaloneapp-get-started-deploy/deploy.png"/></a> &nbsp; **Společnost Fabrikam Jazz křížovou kartou**

2. Zadejte požadovaný parametr hodnoty pro každé nasazení.

    > [!IMPORTANT]
    > Některé ověřování a server brány firewall jsou záměrně zabezpečená pro demonstrační účely. **Vytvořit novou skupinu prostředků** pro každé nasazení aplikace.  Nepoužívejte existující skupinu prostředků. Tato aplikace nebo všechny prostředky, který vytváří, nepoužívejte pro produkční prostředí. Až skončíte s aplikacemi zastavit související fakturace, odstraňte všechny skupiny prostředků.

    Je nejvhodnější použít jenom malá písmena, číslice a pomlčky v názvy prostředků.
    * Pro **skupiny prostředků**, vyberte možnost vytvořit nový a potom zadejte název pro skupinu prostředků, malá písmena. **adresář Wingtip-sa -\<venueName\>-\<uživatele\>**  je doporučené vzor.  Pro \<venueName\>, nahraďte název místo bez mezer. Pro \<uživatele\>, nahraďte hodnotu uživatele z následujícího seznamu.  Tento vzor, může být názvy skupin prostředků *wingtip sa contosoconcerthall af1*, *wingtip sa dogwooddojo af1*, *wingtip sa fabrikamjazzclub af1*.
    * Vyberte **umístění** z rozevíracího seznamu.

    * Pro **uživatele** -doporučujeme hodnotu krátké uživatele, jako je například vašimi iniciálami a číslice: například *af1*.


3. **Nasaďte aplikaci**.

    * Kliknutím na tlačítko přijmout podmínky a ujednání.
    * Klikněte na **Koupit**.

4. Monitorování stavu všech tří nasazení kliknutím **oznámení** (ikonu zvonku napravo od pole hledání). Nasazení aplikace trvá přibližně pět minut.


## <a name="run-the-applications"></a>Spuštění aplikací

Aplikace umožňující prezentovat místa, které jsou hostiteli události.  Místa jsou klienty aplikace. Každý místo získá přizpůsobené webového serveru do seznamu svoje události a prodej lístků. Typy místo obsahují vzájemné součinnosti místnosti, jazz kluby a sportu kluby. V ukázce Určuje typ místo fotografie pozadí zobrazený na webu místo.   V modelu samostatné aplikace má každý místo instanci samostatné aplikace s vlastní samostatná databáze SQL.

1. Otevřete stránku události pro všechny tři klienty v záložkách samostatné prohlížeče:

    - http://events.contosoconcerthall.&lt;user&gt;.trafficmanager.net
    - http://events.dogwooddojo.&lt;user&gt;.trafficmanager.net
    - http://events.fabrikamjazzclub.&lt;user&gt;.trafficmanager.net

    (V každou adresu URL, nahraďte &lt;uživatele&gt; s hodnotou uživatele vaše nasazení.)

   ![Události](./media/saas-standaloneapp-get-started-deploy/fabrikam.png)

K řízení distribuce příchozí požadavky a používá aplikace [ *Azure Traffic Manager*](../traffic-manager/traffic-manager-overview.md). Každá instance konkrétního klienta aplikace obsahuje název klienta jako součást názvu domény v adrese URL. Všechny klienta adresy URL zahrnují konkrétní **uživatele** hodnotu. Adresy URL použijte následující formát:
- http://events.&lt;venuename&gt;.&lt;user&gt;.trafficmanager.net

Každý klient databáze **umístění** je zahrnuta v nastavení aplikace odpovídající nasazené aplikace.

V produkčním prostředí, obvykle můžete vytvořit záznam CNAME DNS do [ *nasměrování internetové domény společnosti* ](../traffic-manager/traffic-manager-point-internet-domain.md) na adresu URL profil správce provozu.


## <a name="explore-the-servers-and-tenant-databases"></a>Prozkoumejte serverů a klientů databáze

Podívejme se na některé prostředky, které byly nasazeny:

1. V [portál Azure](http://portal.azure.com), přejděte do seznamu skupin prostředků.
2. Měli byste vidět tři klienta skupiny prostředků.
3. Otevřete **wingtip-sa-fabrikam -&lt;uživatele&gt;**  skupinu prostředků, která obsahuje prostředky pro nasazení křížovou kartou Jazz Fabrikam.  **Fabrikamjazzclub -&lt;uživatele&gt;**  server obsahuje **fabrikamjazzclub** databáze.

Každá databáze klienta je 50 DTU *samostatné* databáze.

## <a name="additional-resources"></a>Další zdroje informací:

<!--
* Additional [tutorials that build on the Wingtip SaaS application](sql-database-wtp-overview.md#sql-database-wingtip-saas-tutorials)
* To learn about elastic pools, see [*What is an Azure SQL elastic pool*](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-pool)
* To learn about elastic jobs, see [*Managing scaled-out cloud databases*](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-jobs-overview)
-->

- Další informace o víceklientské aplikace SaaS najdete v tématu [vzory pro víceklientské aplikace SaaS návrhu](saas-tenancy-app-design-patterns.md).

 
## <a name="delete-resource-groups-to-stop-billing"></a>Odstranění skupiny prostředků zastavit fakturace ##

Po dokončení pomocí vzorku odstraňte všechny skupiny prostředků, který jste vytvořili pro zastavení přidružené fakturace.

## <a name="next-steps"></a>Další postup

V tomto kurzu jste se dozvěděli:

> [!div class="checklist"]
> * Postup nasazení Wingtip lístky SaaS samostatné aplikace.
> * O servery a databáze, které tvoří aplikace.
> * Postup odstranění ukázkové prostředky zastavit související fakturace.

Zkuste [zřídit a katalog](saas-standaloneapp-provision-and-catalog.md) kurz, ve kterém bude prozkoumat použití katalogu klientů, která umožňuje celou řadu scénářů napříč klienta jako jsou například analýzy schématu správy a klientů.
 

