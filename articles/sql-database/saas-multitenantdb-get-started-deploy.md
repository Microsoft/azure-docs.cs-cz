---
title: Nasazení SaaS aplikace s horizontálně dělené databázi s více tenanty využívající Azure SQL Database | Dokumentace Microsoftu
description: Nasazení a zkoumání horizontálně dělené aplikace databázi s více tenanty SaaS aplikace Wingtip Tickets, který ukazuje vzorce SaaS pomocí Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MightyPen
ms.author: genemi
ms.reviewer: billgib, stein
manager: craigg
ms.date: 10/16/2018
ms.openlocfilehash: cb64b96f179bd71c3f885a54b17decb794b40c1a
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/01/2019
ms.locfileid: "55567379"
---
# <a name="deploy-and-explore-a-sharded-multi-tenant-application"></a>Nasazení a zkoumání horizontálně dělené aplikace s více tenanty

V tomto kurzu nasazení a zkoumání ukázkové SaaS aplikace více tenantů, který je pojmenován Wingtip Tickets. Aplikace Wingtip Tickets je určen, aby ukazovala funkce služby Azure SQL Database, které usnadňují provádění scénáře SaaS.

Tato implementace aplikace Wingtip Tickets používá model horizontálně dělené databázi s více tenanty. Identifikátor tenanta je horizontální dělení. Data tenanta je distribuován do konkrétní databázi podle hodnot identifikátoru tenanta. 

Tento model databáze umožňuje ukládat jednoho nebo více klientů v jednotlivých horizontálních oddílů nebo databáze. Můžete provést optimalizaci nejnižší náklady tím, že každá databáze být sdílen více tenantů. Nebo můžete optimalizovat pro zajištění izolace tak, že každá databáze uložit pouze jednoho tenanta. Podle vašeho výběru optimalizace lze nezávisle pro každý konkrétního tenanta. Podle vašeho výběru můžete vzít v úvahu tenanta nejprve uložená, nebo později změníte své rozhodnutí. Aplikace je navržen pro práci i v obou případech.

## <a name="app-deploys-quickly"></a>Rychle nasazuje aplikace

Aplikace běží v cloudu Azure a využívá Azure SQL Database. V následující části nasazení poskytuje modrá **nasadit do Azure** tlačítko. Při stisknutí tlačítka se ke svému předplatnému Azure během pěti minut plně nasazení aplikace. Máte plný přístup k práci s jednotlivé součásti aplikace.

Aplikace se nasazuje s daty pro tři ukázkoví tenanti. Klienti jsou uložená společně v jedné databázi s více tenanty.

Každý, stáhněte si zdrojový kód C# a PowerShell pro Wingtip Tickets z [své úložiště GitHub][link-github-wingtip-multitenantdb-55g].

## <a name="learn-in-this-tutorial"></a>Další informace v tomto kurzu

> [!div class="checklist"]
> - Jak nasadit aplikaci SaaS aplikace Wingtip Tickets.
> - Kde lze získat zdrojový kód aplikace a skripty pro správu.
> - O serverech a databází, které tvoří aplikaci.
> - Jak se tenanti jsou namapovaní na svá data pomocí *katalogu*.
> - Jak zřídit nového tenanta.
> - Jak monitorovat aktivity tenanta v aplikaci.

Nabízíme řadu souvisejících kurzů, které vycházejí z tohoto počátečního nasazení. V kurzech prozkoumat vzorů návrhu a správy SaaS rozsahu. Při práci těmito kurzy jsou ukončena. doporučujeme projít zadané skripty, abyste zjistili, jak se implementují různé vzorce SaaS.

## <a name="prerequisites"></a>Požadavky

Předpokladem dokončení tohoto kurzu je splnění následujících požadavků:

- Je nainstalovaný nejnovější Azure PowerShell. Podrobnosti najdete v tématu [Začínáme s Azure Powershellem][link-azure-get-started-powershell-41q].

## <a name="deploy-the-wingtip-tickets-app"></a>Nasazení Wingtip Tickets aplikace

### <a name="plan-the-names"></a>Plánování názvy

V krocích v této části zadáte *uživatele* hodnotu, která se používá k zajištění jsou globálně jedinečné názvy prostředků a název *skupiny prostředků* obsahující všechny prostředky vytvořené v rámci nasazení aplikace. Pro osoby s názvem *Ann Finley*, doporučujeme:
- *Uživatel:* **af1**  *(její iniciály, a navíc číslici. Použít jinou hodnotu (například af2) Pokud nasadíte aplikaci podruhé.)*
- *Skupina prostředků:* **wingtip-mt – af1** *(wingtip-mt označuje to horizontálně dělené aplikace s více tenanty. Přidávání af1 uživatelské jméno koreluje název skupiny prostředků s názvy prostředků, které obsahuje.)*

Teď zvolte názvy a zapište si. 

### <a name="steps"></a>Kroky

1. Klikněte na následující modrá **nasadit do Azure** tlačítko.
    - Otevře se na webu Azure portal se šablonou nasazení SaaS aplikace Wingtip Tickets.

    [![Tlačítko pro nasazení do Azure.][image-deploy-to-azure-blue-48d]][link-aka-ms-deploywtp-mtapp-52k]

1. Zadejte hodnoty parametrů požadované pro nasazení.

    > [!IMPORTANT]
    > Pro tuto ukázku se nepoužívá žádné existující skupiny prostředků, serverům nebo fondy. Místo toho zvolte **vytvořit novou skupinu prostředků**. Po ukončení práce s aplikací tuto skupinu prostředků odstraňte, aby se zastavilo související účtování.
    > Tato aplikace nebo všechny prostředky, které vytvoří, nepoužívejte pro produkční prostředí. Některé aspekty ověřování a nastavení brány firewall serveru jsou v aplikaci usnadňuje demonstraci záměrně nezabezpečené.

    - Pro **skupiny prostředků** – vyberte **vytvořit nový**a potom zadejte **název** pro skupinu prostředků (rozlišuje velikost písmen).
        - Vyberte **umístění** z rozevíracího seznamu.
    - Pro **uživatele** – doporučujeme, abyste zvolili krátké **uživatele** hodnotu.

1. **Nasaďte aplikaci**.

    - Klikněte na tlačítko souhlas s podmínkami a ujednáními.
    - Klikněte na **Koupit**.

1. Monitorování stavu nasazení kliknutím **oznámení**, což je ikona zvonku napravo od vyhledávacího pole. Nasazení aplikace Wingtip trvá přibližně 5 minut.

   ![nasazení bylo úspěšné](media/saas-multitenantdb-get-started-deploy/succeeded.png)

## <a name="download-and-unblock-the-management-scripts"></a>Stáhněte si a odblokování skripty pro správu

Při nasazení aplikace, stáhněte si zdrojový kód a správu skriptů aplikace.

> [!NOTE]
> Windows může blokovat spustitelný obsah (skripty, knihovny DLL) při stahování z externího zdroje a extrahovat soubory zip. Při extrahování skripty ze souboru zip, postupujte následovně Pokud chcete odblokovat soubor .zip před extrahováním. Podle odblokování soubor .zip, zajistíte, že je povolené spouštět skripty.

1. Přejděte do [úložiště GitHub WingtipTicketsSaaS MultiTenantDb](https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDb).
2. Klikněte na tlačítko **klonovat nebo stáhnout**.
3. Klikněte na tlačítko **stáhnout ZIP** a soubor uložte.
4. Klikněte pravým tlačítkem myši **WingtipTicketsSaaS-MultiTenantDb-master.zip** a vyberte možnost **vlastnosti**.
5. Na **Obecné** kartu, vyberte možnost **Odblokovat**a klikněte na tlačítko **použít**.
6. Klikněte na **OK**.
7. Extrahujte soubory.

Tyto skripty jsou umístěny v *... \\WingtipTicketsSaaS-MultiTenantDb-master\\Learning Modules\\*  složky.

## <a name="update-the-configuration-file-for-this-deployment"></a>Aktualizovat konfigurační soubor pro toto nasazení

Než spustíte všechny skripty, nastavte *skupiny prostředků* a *uživatele* hodnoty v **UserConfig.psm1**. Nastavení těchto proměnných na stejné hodnoty, které jste nastavili při nasazování.

1. Otevřete... \\Learning Modules\\*UserConfig.psm1* v *prostředí PowerShell ISE*.
2. Aktualizace *ResourceGroupName* a *název* s konkrétní hodnoty pro vaše nasazení (na řádcích 10 a 11 pouze).
3. Uložte změny.

Hodnoty nastavené v tomto souboru jsou používány všechny skripty, takže je důležité, že jsou správné. Pokud aplikaci znovu nasaďte, je třeba zvolit jiné hodnoty pro uživatele a skupiny prostředků. Potom aktualizujte soubor UserConfig.psm1 znovu s novými hodnotami.

## <a name="run-the-application"></a>Spuštění aplikace

V aplikaci Wingtip klientům se registrují. Jako místo, může být informovalo, sportu club nebo na jiném místě, který je hostitelem události. Místech zaregistrovat v Wingtip jako zákazníci a identifikátor tenanta je generován pro každé místo. Každé místo uvádí jeho nadcházejících událostí v Wingtip, takže veřejnosti můžete zakoupit lístky pro události.

Každé místo dostane vlastní webovou aplikaci pro výpis svých akcí a prodej lístků. Každá webová aplikace je nezávislou a izolovanou od ostatních tenantů. Interně ve službě Azure SQL Database, každý, ve které je ve výchozím nastavení uloženy horizontálně dělené databázi s více tenanty, data pro každého tenanta. Identifikátor tenanta je označené všechna data.

Centrální **Centrum akcí** webová stránka obsahuje seznam odkazů na klienty v určité nasazení. Pomocí následujícího postupu práce **Centrum akcí** webové stránky a jednotlivé webové aplikace:

1. Otevřít **Centrum akcí** ve webovém prohlížeči:
    - http://events.wingtip-mt.&lt; Uživatel&gt;. trafficmanager.net &nbsp; *(nahradit &lt;uživatele&gt; s hodnotou uživatele vašeho nasazení.)*

    ![centrum akcí](media/saas-multitenantdb-get-started-deploy/events-hub.png)

2. V **Centru akcí** klikněte na **Fabrikam Jazz Club**.

   ![Události](./media/saas-multitenantdb-get-started-deploy/fabrikam.png)

### <a name="azure-traffic-manager"></a>Azure Traffic Manager

K řízení distribuce příchozích požadavků, aplikace Wingtip používá [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md). Stránka události pro každého klienta obsahuje název tenanta v adrese URL. Každá adresa URL obsahuje také vaše konkrétní hodnotu uživatele. Každá adresa URL dodržuje zobrazených formátu pomocí následujících kroků:

- http://events.wingtip-mt.&lt;user&gt;.trafficmanager.net/*fabrikamjazzclub*

1. Aplikace akcí analyzuje název tenanta z adresy URL. Je název tenanta *fabrikamjazzclub* v předchozí příklad adresy URL.
2. Aplikace pak hash názvu tenanta k vytvoření klíče pro přístup ke katalogu pomocí [správy mapování horizontálních oddílů](sql-database-elastic-scale-shard-map-management.md).
3. Aplikace vyhledá klíč v katalogu a získá odpovídající umístění databáze tenanta.
4. Aplikace používá informace o geografickém umístění k vyhledání a přístupu k jedné databázi, která obsahuje veškerá data pro tenanta.

### <a name="events-hub"></a>Centrum akcí

1. **Centrum akcí** obsahuje seznam všech tenantů, které jsou zaregistrovány v katalogu a jejich místa.
2. **Centrum akcí** pomocí rozšířených metadat v katalogu pro načtení názvu tenanta, které jsou spojené s každou mapování k vytvoření adresy URL.

V produkčním prostředí, obvykle vytvoříte záznam CNAME DNS k [nasměrování internetové domény společnosti](../traffic-manager/traffic-manager-point-internet-domain.md) na profil traffic Manageru.

## <a name="start-generating-load-on-the-tenant-databases"></a>Spuštění generování zatížení databází tenantů

Teď, když je aplikace nasazena, teď to Dejme pracovat! *Demo-LoadGenerator* Powershellový skript spustí úlohu spuštěnou pro každého tenanta. Skutečná zátěž velký počet aplikací SaaS je obvykle sporadické a nepředvídatelné. Simulace tohoto typu zatížení, vytváří generátor zatížení rozdělené mezi všechny tenanty. Zatížení zahrnuje náhodnou nárůsty zatížení na každého tenanta, ke kterým dochází v náhodnými intervaly. Trvá několik minut pro vzorek zatížení objeví, takže je vhodné nechte generátor běžet alespoň tři nebo čtyři minut, než budete monitorovat zatížení.

1. V *prostředí PowerShell ISE*, otevřete... \\Learning Modules\\nástroje\\*Demo-LoadGenerator.ps1* skriptu.
2. Stisknutím **F5** spusťte skript s generátorem zatížení (zatím nechejte nastavené výchozí hodnoty parametrů).

*Demo-LoadGenerator.ps1* skript otevře jinou relaci Powershellu, kde se spouští generátor zatížení. Generátor zatížení spouští v této relaci jako úlohu popředí, která volá generování zatížení úlohy na pozadí, jednu pro každého tenanta.

Po spuštění úlohy popředí, zůstane ve stavu, volání úlohy. Úlohy na pozadí další spuštění úlohy pro jakékoli nové klienty, které jsou následně zřízené.

Zavření relace Powershellu se ukončí všechny úlohy.

Můžete chtít restartovat relaci generátor zatížení použít jiné hodnoty parametru. Pokud ano, ukončete generování relaci Powershellu a pak znovu spusťte *Demo-LoadGenerator.ps1*.

## <a name="provision-a-new-tenant-into-the-sharded-database"></a>Zřízení nového tenanta do horizontálně dělené databáze

Počáteční nasazení obsahuje tři ukázkoví tenanti v *Tenants1* databáze. Pojďme vytvořit jiného tenanta a sledovat její vliv na nasazenou aplikaci. V tomto kroku stiskněte jeden klíč k vytvoření nového tenanta:

1. Otevřete... \\Learning Modules\\Provision and Catalog\\*Demo-ProvisionTenants.ps1* v *prostředí PowerShell ISE*.
2. Stisknutím klávesy **F5** (ne **F8**) spusťte skript (zatím nechejte nastavené výchozí hodnoty).

   > [!NOTE]
   > Je nutné spustit skripty prostředí PowerShell pouze stisknutím klávesy **F5** klíčů, nikoli stisknutím kombinace kláves **F8** spustit vybrané části skriptu. Problém s **F8** je, že *$PSScriptRoot* proměnnou, nebude hodnocen. Tato proměnná je potřeba při mnoho skriptů k procházení složek vyvolat další skripty nebo importovat další moduly.

K přidání nového tenanta Red Maple Racing *Tenants1* databáze a zaregistrované v katalogu. Nového tenanta uživatele, prodej lístků **události** v prohlížeči se otevře web:

![Nový tenant](./media/saas-multitenantdb-get-started-deploy/red-maple-racing.png)

Aktualizovat **Centrum akcí**, a nového tenanta se zobrazí v seznamu.

## <a name="provision-a-new-tenant-in-its-own-database"></a>Zřízení nového tenanta v její vlastní databázi

Horizontálně dělené víceklientského modelu můžete zvolit, jestli se ke zřízení nového tenanta do databáze, která obsahuje jiných klientů nebo do databáze svůj vlastní. Izolovali ve vlastní databázi tenanta má následující výhody:

- Výkon databáze tenanta se dají spravovat bez nutnosti ohrozit uspokojení požadavků ostatních tenantů.
- V případě potřeby databázi můžete obnovit k dřívějšímu bodu v čase, protože žádní další tenanti by byly ovlivněny.

Můžete převést bezplatné zkušební verze zákazníci nebo zákazníci ekonomiku do databáze s více tenanty. Každý tenant premium můžete umístit do své vlastní vyhrazený databáze. Pokud vytváříte mnoho databází, které obsahují pouze jednoho tenanta, je můžete spravovat všechny společně v elastickém fondu optimalizovat náklady na prostředky.

V dalším kroku zřídit jiného tenanta, tentokrát v její vlastní databázi:

1. V... \\Learning Modules\\Provision and Catalog\\*Demo-ProvisionTenants.ps1*, upravte *$TenantName* k **Salix Salsa**, *$VenueType* k **dance** a *$Scenario* k **2**.

2. Stisknutím klávesy **F5** znovu spusťte skript.
    - To **F5** stiskněte zřizuje nového tenanta v samostatné databáze. Databáze a tenanta se zaregistruje do katalogu. Potom v prohlížeči otevře stránku událostí tenanta.

   ![Stránka události Salix Salsa](./media/saas-multitenantdb-get-started-deploy/salix-salsa.png)

   - Přejděte do dolní části stránky. Existuje v informační zprávě se zobrazí název databáze, ve kterém jsou data tenanta uložená.

3. Aktualizovat **Centrum akcí** a dvou nových tenantů se teď zobrazí v seznamu.

## <a name="explore-the-servers-and-tenant-databases"></a>Zkoumání serverů a databází tenantů

Teď můžeme podívat na některé z nasazených prostředků:

1. V [webu Azure portal](http://portal.azure.com), přejděte na seznam skupin prostředků. Otevřete skupinu prostředků, kterou jste vytvořili při nasazení aplikace.

   ![skupina prostředků](./media/saas-multitenantdb-get-started-deploy/resource-group.png)

2. Klikněte na tlačítko **katalogu mt&lt;uživatele&gt;**  serveru. Server katalogu obsahuje dvě databáze s názvem *tenantcatalog* a *basetenantdb*. *Basetenantdb* databáze je databáze prázdnou šablonu. Chcete-li vytvořit novou databázi tenantů, je zkopírován, jestli se použije pro více tenantů nebo jen jednoho tenanta.

   ![katalogový server](./media/saas-multitenantdb-get-started-deploy/catalog-server.png)

3. Vraťte se zpět do skupiny prostředků a vyberte *tenants1-mt* serveru, který obsahuje databáze tenantů.
    - Databáze tenants1 je víceklientskou databázi, ve kterém jsou uložené původní tři tenanty a první tenanta, které jste přidali. Je nakonfigurovaný jako 50 DTU standardní databáze.
    - **Salixsalsa** databáze obsahuje příslušností dance Salix Salsa jako její jediný tenant. Ve výchozím nastavení je nakonfigurován jako standardní edici databáze s 50 Dtu.

   ![tenanti serveru](./media/saas-multitenantdb-get-started-deploy/tenants-server.png)

## <a name="monitor-the-performance-of-the-database"></a>Monitorování výkonu databáze nástroje

Pokud generátor zatížení běžel několik minut, je podívat se na databázi monitorování součástí na webu Azure portal k dispozici dost telemetrických dat.

1. Přejděte **tenants1-mt&lt;uživatele&gt;**  serveru a klikněte na **tenants1** zobrazení využití prostředků databáze, který v sobě obsahuje čtyři tenantů. Každý tenant se může ojediněle případě velkého zatížení z generátor zatížení:

   ![monitorování tenants1](./media/saas-multitenantdb-get-started-deploy/monitor-tenants1.png)

   Graf využití DTU přehledně znázorňuje, jak databázi s více tenanty podporují nepředvídatelné zatížení napříč velký počet klientů. Generátor zatížení v tomto případě je použití sporadické zatížení zhruba 30 jednotek dtu na každého tenanta. Tato zatížení odpovídá 60 % využití 50 DTU databáze. Špičky, které překročí 60 % jsou výsledkem použití do více než jednoho tenanta ve stejnou dobu zatížení.

2. Přejděte **tenants1-mt&lt;uživatele&gt;**  serveru a klikněte na **salixsalsa** databáze. V této databázi, která obsahuje jenom k jednomu tenantovi můžete zobrazit využití prostředků.

   ![salixsalsa databáze](./media/saas-multitenantdb-get-started-deploy/monitor-salix.png)

Generátor zatížení je ke každému tenantovi, bez ohledu na to, kterou databázi každý tenant je použití podobné zatížení. Pomocí pouze jednoho tenanta v **salixsalsa** databáze, uvidíte, že databáze může tolerovat mnohem větší zatížení než databáze s více tenanty. 

### <a name="resource-allocations-vary-by-workload"></a>Přidělení prostředků se liší podle úloh

Někdy víceklientskou databázi s vyžaduje víc prostředků pro dobrý výkon, než se databáze s jedním tenantem, ale ne vždy. Optimální přidělení prostředků závisí na charakteristikách konkrétní úloh pro klienty ve vašem systému.

Úlohy vygenerovaný skript generátor zatížení se jen jako ukázka.

## <a name="additional-resources"></a>Další materiály

- Další informace o víceklientské aplikace SaaS, naleznete v tématu [vzory návrhu pro víceklientské aplikace SaaS](saas-tenancy-app-design-patterns.md).

- Další informace o elastických fondech najdete v tématu:

  - [Elastické fondy umožňují spravovat a škálovat několika databázemi Azure SQL](sql-database-elastic-pool.md)
  - [Horizontální navýšení kapacity s Azure SQL Database](sql-database-elastic-scale-introduction.md)

## <a name="next-steps"></a>Další postup

V tomto kurzu jste se dozvěděli:

> [!div class="checklist"]
> - Jak nasadit aplikaci databázi s více tenanty SaaS aplikace Wingtip Tickets.
> - Informace o serveru a databáze, které aplikaci tvoří.
> - Tenanti jsou namapovaní na svá data pomocí *katalogu*.
> - Jak zřídit nové tenanty v databázi s více tenanty a databáze s jedním tenantem.
> - Jak zobrazit využití fondu k monitorování aktivity tenanta.
> - Jak odstranit ukázkové prostředky k zastavení souvisejícího účtování.

Teď si vyzkoušejte [Provision and catalog kurzu](sql-database-saas-tutorial-provision-and-catalog.md).


<!--  Link references.

A [series of related tutorials] is available that build upon this initial deployment.
[link-wtp-overivew-jumpto-saas-tutorials-97j]: saas-multitenantdb-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials

-->

[link-aka-ms-deploywtp-mtapp-52k]: http://aka.ms/deploywtp-mtapp


[link-azure-get-started-powershell-41q]: https://docs.microsoft.com/powershell/azure/get-started-azureps

[link-github-wingtip-multitenantdb-55g]: https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDB/



<!--  Image references.

[image-deploy-to-azure-blue-48d]: http://aka.ms/deploywtp-mtapp "Button for Deploy to Azure."
-->

[image-deploy-to-azure-blue-48d]: media/saas-multitenantdb-get-started-deploy/deploy.png "Tlačítko pro nasazení do Azure."

