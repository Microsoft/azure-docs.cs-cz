---
title: "Nasaďte aplikaci SaaS horizontálně dělené víceklientské databáze, která používá Azure SQL Database | Microsoft Docs"
description: "Nasazení a prozkoumejte víceklientské databázovou aplikaci horizontálně dělené Wingtip lístky SaaS, která demonstruje SaaS vzory pomocí Azure SQL Database."
keywords: kurz k sql database
services: sql-database
author: MightyPen
manager: craigg
ms.service: sql-database
ms.custom: scale out apps
ms.workload: data-management
ms.topic: article
ms.date: 12/18/2017
ms.author: genemi
ms.openlocfilehash: 82983c8926bdf3c7256c529448e5b4f3b2c2381c
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2018
---
# <a name="deploy-and-explore-a-sharded-multi-tenant-application-that-uses-azure-sql-database"></a>Nasazení a prozkoumejte horizontálně dělené víceklientské aplikace, která používá Azure SQL Database

V tomto kurzu nasazení a prozkoumat ukázkové aplikace SaaS více klientů, která je s názvem Wingtip lístků. Adresář Wingtip lístky aplikace je navržená ke prezentují funkce databáze SQL Azure, které usnadňují implementaci SaaS scénářů.

Tato implementace Wingtip lístky aplikace využívá vzor horizontálně dělené víceklientské databáze. Identifikátor klienta, je horizontálního dělení. Data klienta je distribuován do konkrétní databázi podle hodnot identifikátoru klienta. 

Tento vzor databáze umožňuje ukládání jednoho nebo více klientů v každé horizontálního oddílu nebo databáze. Můžete optimalizovat pro nižší cenu tak, že každou databázi sdílet víc klientů. Nebo můžete optimalizovat pro izolaci tak, že každá databáze uložit pouze jednoho klienta. Zvoleného optimalizace můžete provést samostatně pro každou konkrétní klienta. Zvoleného mohou být provedeny, když klient nejdřív uložen, nebo můžete později změnit své rozhodnutí. Aplikace je určena pro dobře v obou případech.

#### <a name="app-deploys-quickly"></a>Rychle nasazuje aplikace

Aplikace běží v cloudu Azure a používá databázi SQL Azure. Následující části nasazení poskytuje modrý **nasadit do Azure** tlačítko. Při stisknutí tlačítka se k předplatnému Azure do pěti minut plně nasazení aplikace. Máte plný přístup k práci s jednotlivé součásti aplikace.

Aplikace je nasazená s daty pro tři ukázkové klienty. Klienti jsou uloženy společně v jedné databáze více klientů.

Každý, kdo můžete stáhnout zdrojového kódu C# a prostředí PowerShell pro adresář Wingtip lístky z [jeho úložiště GitHub][link-github-wingtip-multitenantdb-55g].

#### <a name="learn-in-this-tutorial"></a>Další informace v tomto kurzu

> [!div class="checklist"]
> - Postup nasazení aplikace SaaS Wingtip lístků.
> - Kde lze získat zdrojovému kódu aplikace a skripty pro správu.
> - O servery a databáze, které tvoří aplikace.
> - Jak klienti jsou mapované na svoje data pomocí *katalogu*.
> - Jak zřídit nového klienta.
> - Postup sledování klientů aktivity v aplikaci.

Je k dispozici řadu související návodů, které stavět na toto počáteční nasazení. Kurzů k prozkoumejte rozsah SaaS návrhu a správu vzory. Při práci prostřednictvím kurzů k vám doporučujeme, aby se krok prostřednictvím zadané skriptů, které chcete zobrazit, jak jsou implementované různé vzorce SaaS.

## <a name="prerequisites"></a>Požadavky

Předpokladem dokončení tohoto kurzu je splnění následujících požadavků:

- Je nainstalována nejnovější prostředí Azure PowerShell. Podrobnosti najdete v tématu [Začínáme s Azure Powershellem][link-azure-get-started-powershell-41q].

## <a name="deploy-the-wingtip-tickets-app"></a>Nasazení adresář Wingtip lístky aplikace

#### <a name="plan-the-names"></a>Plánování názvy

Kroky v této části, poskytnete *uživatele* hodnotu, která se používá k zajištění jsou globálně jedinečné názvy prostředků a název *skupiny prostředků* obsahující všechny prostředky vytvořeny při nasazení aplikace. Pro osoby s názvem *Ann Finley*, doporučujeme:
- *Uživatel:* **af1***(jeho iniciály a číslice. Použít jinou hodnotu (např. af2) Pokud nasazujete aplikaci ještě jednou.)*
- *Skupina prostředků:* **wingtip. dpt af1** *(wingtip dpt signalizuje to databáze za klienta aplikace. Připojování af1 název uživatele koreluje s názvy prostředků, které obsahuje název skupiny prostředků.)*

Teď zvolte názvy a poznamenejte si je. 

#### <a name="steps"></a>Kroky

1. Klikněte na následující blue **nasadit do Azure** tlačítko.
    - Otevře se na portálu Azure pomocí šablony nasazení Wingtip lístky SaaS.

    [![Tlačítko pro nasazení do Azure.][image-deploy-to-azure-blue-48d]][link-aka-ms-deploywtp-mtapp-52k]

1. Zadejte hodnoty parametrů požadovaných pro nasazení.

    > [!IMPORTANT]
    > U této ukázce nepoužívejte žádné existující skupiny prostředků, serverům nebo fondů. Místo toho vyberte **vytvořit novou skupinu prostředků**. Po ukončení práce s aplikací tuto skupinu prostředků odstraňte, aby se zastavilo související účtování.
    > Tato aplikace nebo všechny prostředky, který vytváří, nepoužívejte pro produkční prostředí. Některé aspekty ověřování a nastavení brány firewall serveru, jsou záměrně nezabezpečené v aplikaci usnadňuje ukázky.

    - Pro **skupiny prostředků** – vyberte **vytvořit nový**a potom zadejte **název** pro skupinu prostředků (malá a velká písmena).
        - Vyberte **umístění** z rozevíracího seznamu.
    - Pro **uživatele** -doporučujeme, abyste zvolili malou **uživatele** hodnotu.

1. **Nasaďte aplikaci**.

    - Kliknutím na tlačítko přijmout podmínky a ujednání.
    - Klikněte na **Koupit**.

1. Monitorování stavu nasazení kliknutím **oznámení**, který je na ikonu zvonku napravo od pole hledání. Nasazení aplikace Wingtip trvá přibližně pět minut.

   ![nasazení bylo úspěšné](media/saas-multitenantdb-get-started-deploy/succeeded.png)

## <a name="download-and-unblock-the-management-scripts"></a>Stáhněte si a odblokování skripty správy

Když je nasazení aplikace, stáhněte si skripty aplikace zdrojový kód a správu.

> [!NOTE]
> Spustitelný soubor obsah (skripty, knihovny DLL) mohou být blokovány Windows, když jsou soubory zip stažené z externího zdroje a rozbalené. Při extrahování skripty ze souboru zip, použijte následující postup odblokování před extrahování souboru ZIP. Ve odblokování soubor .zip, zajistíte, že je povoleno spustit skripty.

1. Přejděte do [úložiště GitHub WingtipTicketsSaaS MultiTenantDb](https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDb).
2. Klikněte na tlačítko **klonovat nebo stáhnout**.
3. Klikněte na tlačítko **stáhnout ZIP** a soubor uložte.
4. Klikněte pravým tlačítkem myši **WingtipTicketsSaaS-MultiTenantDb-master.zip** soubor a vyberte **vlastnosti**.
5. Na **Obecné** vyberte **Odblokovat**a klikněte na tlačítko **použít**.
6. Klikněte na **OK**.
7. Extrahujte soubory.

Skripty, které jsou umístěné v *... \\WingtipTicketsSaaS-MultiTenantDb-master\\Learning moduly\\*  složky.

## <a name="update-the-configuration-file-for-this-deployment"></a>Aktualizace konfiguračního souboru pro toto nasazení

Před spuštěním všech skriptů, nastavte *skupiny prostředků* a *uživatele* hodnoty v **UserConfig.psm1**. Nastavte tyto proměnné na stejné hodnoty, které nastavíte během nasazení.

1. Otevřete... \\Učení moduly\\*UserConfig.psm1* v *prostředí PowerShell ISE*.
2. Aktualizace *ResourceGroupName* a *název* s konkrétními hodnotami pro vaše nasazení (na řádky 10 a 11 pouze).
3. Uložte změny.

S hodnotami nastavenými v tomto souboru se používají ve všech skriptů, proto je důležité, zda že je přesné. Pokud aplikaci znovu nasaďte, musíte zvolit různé hodnoty pro uživatele a skupiny prostředků. Potom aktualizujte soubor UserConfig.psm1 znovu s novými hodnotami.

## <a name="run-the-application"></a>Spuštění aplikace

V aplikaci Wingtip tenanty místa. Místo, může být vzájemné součinnosti hall, sportu křížovou kartou nebo jakéhokoli jiného umístění, který je hostitelem události. Místa zaregistrovat v Wingtip jako zákazníků a identifikátor tenanta se generuje pro každou místo. Každý místo uvádí nadcházející události v Wingtip, takže veřejnosti můžete zakoupit lístky k událostem.

Každý místo získá přizpůsobené webové aplikace do seznamu svoje události a prodej lístků. Každý webová aplikace je nezávislá a izolované od ostatních klientů. Interně ve službě Azure SQL Database, každou v horizontálně dělené víceklientské databázi, je ve výchozím nastavení uchovávají data pro každého klienta. Identifikátor klienta je označené všechna data.

Centrálního **události rozbočovače** webová stránka obsahuje seznam odkazů na klienty v konkrétní nasazení. Použijte následující postup můžete vyzkoušet **události rozbočovače** webové stránky a jednotlivé webové aplikace:

1. Otevřete **události rozbočovače** ve webovém prohlížeči:
    - http://events.Wingtip-mt.&lt;uživatele&gt;. trafficmanager.net &nbsp; *(Nahraďte &lt;uživatele&gt; s hodnotou uživatele vaše nasazení.)*

    ![centrum akcí](media/saas-multitenantdb-get-started-deploy/events-hub.png)

2. V **Centru akcí** klikněte na **Fabrikam Jazz Club**.

   ![Události](./media/saas-multitenantdb-get-started-deploy/fabrikam.png)

#### <a name="azure-traffic-manager"></a>Azure Traffic Manager

K řízení distribuce příchozích požadavků, Wingtip aplikace používá [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md). Stránka události pro každého klienta zahrnuje název klienta v jeho adresa URL. Každou adresu URL také obsahuje konkrétní hodnota uživatele. Každou adresu URL dodržuje uvedené formát pomocí následujících kroků:

- http://events.wingtip-mt.&lt;user&gt;.trafficmanager.net/*fabrikamjazzclub*

1. Aplikace události analyzuje název klienta z adresy URL. Je název klienta *fabrikamjazzclub* v předchozí příklad adresy URL.
2. Aplikace pak hashuje název klienta pro vytvoření klíče pro přístup k katalogu pomocí [horizontálního oddílu mapy správu](sql-database-elastic-scale-shard-map-management.md).
3. Aplikace vyhledá klíč v katalogu a získá odpovídající umístění databáze klienta.
4. Aplikace používá informace o umístění k vyhledání a přístup k jedné databáze, která obsahuje všechna data pro klienta.

#### <a name="events-hub"></a>Centra událostí

1. **Události rozbočovače** obsahuje seznam všech klientů, které jsou zaregistrovány v katalogu a jejich místa.
2. **Události rozbočovače** používá rozšířené metadata v katalogu načíst název klienta, které jsou spojené s každou mapování k vytvoření adresy URL.

V produkčním prostředí, obvykle vytvořit záznam CNAME DNS do [nasměrování internetové domény společnosti](../traffic-manager/traffic-manager-point-internet-domain.md) pro profil správce provozu.

## <a name="start-generating-load-on-the-tenant-databases"></a>Spuštění generování zatížení databází tenantů

Teď, když je aplikace nasazena, přidejme ho na spolupráci! *Ukázku LoadGenerator* zátěži pro každého klienta spustí skript prostředí PowerShell. Skutečné zatížení velký počet aplikací SaaS je obvykle ojediněle a nepředvídatelným. Pro simulaci tento typ zatížení, vytvoří generátor zatížení rozdělené mezi všechny klienty. Zatížení zahrnuje náhodnou shluky na každého klienta, ke kterým dochází v náhodnou intervalech. Trvá několik minut pro vzor zatížení objeví, proto je vhodné umožníte generátor spustit alespoň tři nebo čtyři minut před monitorování zatížení.

1. V *prostředí PowerShell ISE*, otevřete... \\Learning moduly\\nástroje\\*ukázku LoadGenerator.ps1* skriptu.
2. Stisknutím **F5** spusťte skript s generátorem zatížení (zatím nechejte nastavené výchozí hodnoty parametrů).

*Ukázku LoadGenerator.ps1* skript se otevře jiná relace prostředí PowerShell, kde je spuštěna generátor zatížení. Generátor zatížení se spustí v této relaci jako popředí úlohu, která volá zatížení generování úlohy na pozadí, jednu pro každého klienta.

Po spuštění úlohy popředí, zůstane ve stavu vyvolání úlohy. Úloha spustí další úlohy pro jakékoli nové klienty, které jsou následně zřízené.

Ukončení relace prostředí PowerShell zastaví všechny úlohy.

Můžete chtít restartovat relace generátor zatížení použít jiné hodnoty parametru. Pokud ano, zavřete generování relace prostředí PowerShell a poté znovu spusťte *ukázku LoadGenerator.ps1*.

## <a name="provision-a-new-tenant-into-the-sharded-database"></a>Zřízení nového klienta do horizontálně dělené databáze

Počáteční nasazení obsahuje tři ukázkové klienty v *Tenants1* databáze. Umožňuje vytvořit jiného klienta a sledovat její vliv na nasazené aplikace. V tomto kroku stiskněte jeden klíč k vytvoření nového klienta:

1. Otevřete... \\Učení moduly\\zřídit a katalog\\*ukázku ProvisionTenants.ps1* v *prostředí PowerShell ISE*.
2. Stiskněte klávesu **F5** (ne **F8**) pro spuštění skriptu (ponechte výchozí hodnoty pro nyní).

   > [!NOTE]
   > Je nutné spustit skripty prostředí PowerShell pouze stisknutím **F5** klíče není stisknutím **F8** spustit vybranou část skriptu. Problém s **F8** je, že *$PSScriptRoot* proměnné, nebude hodnocen. Tato proměnná je potřeba mnoho skriptů přejděte složek, nebo volat jiné skripty nebo naimportovat moduly.

K novému klientovi Red javor Racing je přidán do *Tenants1* databáze a zaregistrovány v katalogu. Je k novému klientovi prodávané ticket **události** lokality se otevře v prohlížeči:

![Nový tenant](./media/saas-multitenantdb-get-started-deploy/red-maple-racing.png)

Obnovit **události rozbočovače**, a k novému klientovi se teď zobrazí v seznamu.

## <a name="provision-a-new-tenant-in-its-own-database"></a>Zřízení nového klienta ve vlastní databázi

Horizontálně dělené víceklientského modelu můžete zvolit, jestli ke zřízení nového klienta do databáze, která obsahuje jiných klientů nebo do databáze svůj vlastní. Izolované ve vlastní databázi klienta má následující výhody:
- Výkon databáze klienta se dají spravovat bez nutnosti ohrozit s potřebami ostatních klientů.
- V případě potřeby databázi lze obnovit do dřívějšího bodu v čase, protože žádné z ostatních klientů bude mít vliv.

Můžete zvolit umístit zákazníků bez zkušební verze nebo hospodářství zákazníků do databáze více klientů. Každý klient premium by mohlo do své vlastní vyhrazené databáze. Pokud vytvoříte velký počet databází, které obsahují pouze jednoho klienta, můžete je spravovat všechny souhrnně v elastickém fondu za účelem optimalizace prostředků náklady.

V dalším kroku jsme zřídit jiného klienta, tentokrát ve vlastní databázi:

1. V... \\Learning moduly\\zřídit a katalog\\*ukázku ProvisionTenants.ps1*, upravit *$TenantName* k **Salix Salsa**, *$VenueType* k **tance** a *$Scenario* k **2**.

2. Stiskněte klávesu **F5** pro spuštění skriptu znovu.
    - To **F5** stiskněte zřídí k novému klientovi v samostatné databáze. Databáze a klienta jsou zaregistrovány v katalogu. Potom prohlížeči se otevře na stránku událostí klienta.

   ![Stránka události Salix Salsa](./media/saas-multitenantdb-get-started-deploy/salix-salsa.png)

   - Přejděte do dolní části stránky. Existuje v informační zprávě zobrazí název databáze, ve kterém je uložený dat klienta.

3. Obnovit **události rozbočovače** a dva nové klienty se teď zobrazí v seznamu.

## <a name="explore-the-servers-and-tenant-databases"></a>Prozkoumejte serverů a klientů databáze

Nyní podíváme na některé prostředky, které byly nasazeny:

1. V [portál Azure](http://portal.azure.com), přejděte do seznamu skupin prostředků. Otevřete skupinu prostředků, kterou jste vytvořili při nasazení aplikace.

   ![skupina prostředků](./media/saas-multitenantdb-get-started-deploy/resource-group.png)

2. Klikněte na tlačítko **katalogu mt&lt;uživatele&gt;**  serveru. Na server katalogu obsahuje dvě databáze s názvem *tenantcatalog* a *basetenantdb*. *Basetenantdb* databáze je databáze prázdné šablony. Je zkopírován do vytvoření nového klienta databáze, jestli se používá pro velký počet klientů nebo jedním klienta.

   ![katalogový server](./media/saas-multitenantdb-get-started-deploy/catalog-server.png)

3. Přejděte zpět do skupiny prostředků a vyberte *tenants1-mt* serveru, který obsahuje databáze klienta.
    - Databázi tenants1 je víceklientské databáze, v níž jsou uloženy původní tři klienti plus první klient, který jste přidali. Je nakonfigurován jako 50 standardní DTU databáze.
    - **Salixsalsa** databáze obsahuje místo tance Salix Salsa jako jeho pouze klienta. Ve výchozím nastavení je nakonfigurován jako Standard edition databáze s 50 Dtu.

   ![Klienti serveru](./media/saas-multitenantdb-get-started-deploy/tenants-server.png)


## <a name="monitor-the-performance-of-the-database"></a>Monitorování výkonu databáze nástroje

Pokud generátor zatížení běží několik minut, dost telemetrických dat je k dispozici podívejte se na databáze monitorování funkce integrovaná v portálu Azure.

1. Vyhledejte **tenants1-mt&lt;uživatele&gt;**  serveru a klikněte na **tenants1** zobrazení využití prostředků pro databázi, která se nachází čtyři klienty. Každý klient podléhá ojediněle intenzivně zatěžován z generátor zatížení:

   ![monitorování tenants1](./media/saas-multitenantdb-get-started-deploy/monitor-tenants1.png)

   Graf využití DTU vhodně ilustruje jak databázi víceklientské nepředvídatelným zatížení napříč velký počet klientů. V takovém případě je generátor zatížení použití ojediněle zatížení zhruba 30 Dtu každému klientovi. Tato zatížení rovná využití 60 %, 50 DTU databáze. Špičky delší než 60 % jsou výsledkem zatížení, které bylo použito pro víc klientů současně.

2. Vyhledejte **tenants1-mt&lt;uživatele&gt;**  serveru a klikněte na **salixsalsa** databáze. Zobrazí se využití prostředků u této databáze, který obsahuje pouze jeden klienta.

   ![salixsalsa databáze](./media/saas-multitenantdb-get-started-deploy/monitor-salix.png)

Generátor zatížení aplikuje podobné zatížení každému klientovi, bez ohledu na to, které databázové každého klienta je v. Pouze jeden klientovi v **salixsalsa** databáze, uvidíte, že databáze může tolerovat mnohem vyšší zatížení než databáze s několika klienty. 

#### <a name="resource-allocations-vary-by-workload"></a>Přidělování prostředků se liší podle pracovního vytížení

Někdy víceklientské databáze vyžaduje více prostředků pro dobrý výkon než databáze jednoho klienta, ale ne vždy. Optimální přidělení prostředků závisí na vlastnosti určité zatížení pro klienty ve vašem systému.

Úlohy generované skript generátor zatížení jsou obrázek pouze pro účely.

## <a name="additional-resources"></a>Další zdroje informací:

- Další informace o víceklientské aplikace SaaS najdete v tématu [vzory pro víceklientské aplikace SaaS návrhu](saas-tenancy-app-design-patterns.md).

- Další informace o elastické fondy najdete v tématu:
    - [Elastických fondů pomáhají spravovat a škálování více databází Azure SQL](sql-database-elastic-pool.md)
    - [Horizontální navýšení kapacity s Azure SQL Database](sql-database-elastic-scale-introduction.md)

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se dozvěděli:

> [!div class="checklist"]
> - Postup nasazení aplikace Wingtip lístky SaaS víceklientské databáze.
> - O serverů a databází, které tvoří aplikace.
> - Klienti jsou namapované na svoje data pomocí *katalogu*.
> - Jak zřídit nové klienty do více klientů databáze a databáze jednoho klienta.
> - Postup zobrazení využití fondu monitorování klienta.
> - Postup odstranění ukázkové prostředky zastavit související fakturace.

Nyní zkuste [kurzu zřizování a katalog](sql-database-saas-tutorial-provision-and-catalog.md).


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

