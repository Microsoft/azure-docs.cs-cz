---
title: Nasazení aplikace SaaS pro víceklientské databáze horizontálně dělené
description: Nasaďte a prozkoumejte aplikace horizontálně dělené Wingtip Tickets SaaS multi-tenant Database, která ukazuje vzory SaaS pomocí Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 10/16/2018
ms.openlocfilehash: 5363a1b7321bfcbb53b4494b51ee2ea2e7217782
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91619640"
---
# <a name="deploy-and-explore-a-sharded-multi-tenant-application"></a>Nasazení a zkoumání horizontálně dělené aplikace pro více tenantů
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

V tomto kurzu nasadíte a prozkoumáte ukázkovou aplikaci SaaS s více klienty, která má název s názvem lístky Wingtip. Aplikace Wingtip Tickets je navržená tak, aby předvedla funkce Azure SQL Database, které zjednodušují implementaci scénářů SaaS.

Tato implementace aplikace Wingtip Tickets používá horizontálně dělené model databáze s více klienty. Horizontálního dělení je podle identifikátoru tenanta. Data tenanta jsou distribuována do konkrétní databáze podle hodnot identifikátoru tenanta. 

Tento vzor databáze vám umožňuje ukládat do každého horizontálních oddílů nebo databáze jednoho nebo více klientů. Můžete optimalizovat pro nejnižší náklady tím, že se každá databáze sdílí s více klienty. Nebo můžete optimalizovat pro izolaci tím, že všechna úložiště databáze mají jenom jednoho tenanta. Volbu optimalizace můžete nastavit nezávisle pro každého konkrétního tenanta. Vaše volba se dá udělat při prvním uložení tenanta, nebo můžete svoje rozhodnutí později změnit. Aplikace je navržena tak, aby správně fungovala.

## <a name="app-deploys-quickly"></a>Rychlé nasazení aplikací

Aplikace běží v cloudu Azure a používá Azure SQL Database. Následující část nasazení poskytuje modré tlačítko **nasazení do Azure** . Po stisknutí tlačítka se aplikace plně nasadí do vašeho předplatného Azure během pěti minut. Máte plný přístup k práci s jednotlivými součástmi aplikace.

Aplikace je nasazená s daty pro tři ukázkové klienty. Klienti se ukládají společně v jedné víceklientské databázi.

Kdokoli může stáhnout zdrojový kód C# a PowerShell pro lístky Wingtip z [jeho úložiště GitHub][link-github-wingtip-multitenantdb-55g].

## <a name="learn-in-this-tutorial"></a>Naučte se v tomto kurzu.

> [!div class="checklist"]
> - Jak nasadit aplikaci SaaS lístky Wingtip.
> - Kde získat zdrojový kód aplikace a skripty pro správu.
> - O serverech a databázích, které tvoří aplikaci.
> - Jak jsou klienti mapováni na svá data pomocí *katalogu*.
> - Jak zřídit nového tenanta.
> - Jak monitorovat aktivitu klienta v aplikaci.

K dispozici je řada souvisejících kurzů, které se při tomto počátečním nasazení sestavují. Kurzy navrhují řadu SaaSch návrhů a vzorů správy. Při práci v kurzech doporučujeme projít tyto skripty, abyste viděli, jak jsou implementované různé vzory SaaS.

## <a name="prerequisites"></a>Požadavky

Předpokladem dokončení tohoto kurzu je splnění následujících požadavků:

- Je nainstalovaná nejnovější Azure PowerShell. Podrobnosti najdete v tématu [Začínáme s Azure PowerShell][link-azure-get-started-powershell-41q].

## <a name="deploy-the-wingtip-tickets-app"></a>Nasazení aplikace Wingtip lístky

### <a name="plan-the-names"></a>Plánování názvů

V krocích v této části zadáte hodnotu *uživatele* , která se používá k zajištění globálně jedinečného názvu prostředků a názvu *skupiny prostředků* , která obsahuje všechny prostředky vytvořené nasazením aplikace. Pro osobu s názvem *Ann Finley*doporučujeme:
- *Uživatel:* **AF1**  *(jejich iniciály a číslice. Pokud aplikaci nasadíte podruhé, použijte jinou hodnotu (třeba AF2).)*
- *Skupina prostředků:* **Wingtip-MT-AF1** *(Wingtip-MT označuje, že se jedná o víceklientské aplikace horizontálně dělené. připojení uživatelského jména AF1 koreluje název skupiny prostředků s názvy prostředků, které obsahuje.)*

Vyberte si své názvy a napište je. 

### <a name="steps"></a>Postup

1. Klikněte na následující modré tlačítko **nasadit do Azure** .
   - Otevře Azure Portal pomocí šablony nasazení SaaS lístky Wingtip Tickets.

     [![Tlačítko pro nasazení do Azure][image-deploy-to-azure-blue-48d]][link-aka-ms-deploywtp-mtapp-52k]

1. Zadejte požadované hodnoty parametrů pro nasazení.

    > [!IMPORTANT]
    > Pro tuto ukázku nepoužívejte žádné již existující skupiny prostředků, servery nebo fondy. Místo toho vyberte **vytvořit novou skupinu prostředků**. Po ukončení práce s aplikací tuto skupinu prostředků odstraňte, aby se zastavilo související účtování.
    > Nepoužívejte tuto aplikaci ani žádné prostředky, které vytvoří, pro produkční prostředí. Některé aspekty ověřování a nastavení brány firewall serveru jsou v aplikaci záměrně nezabezpečené, aby se usnadnila ukázka.

    - V části **Skupina prostředků** – vyberte **vytvořit novou**a potom zadejte **název** skupiny prostředků (rozlišuje velká a malá písmena).
        - Z rozevíracího seznamu vyberte **umístění** .
    - Pro **uživatele** – doporučujeme zvolit krátkou hodnotu **uživatele** .

1. **Nasaďte aplikaci**.

    - Kliknutím souhlasíte s podmínkami a ujednáními.
    - Klikněte na **Koupit**.

1. Stav nasazení můžete monitorovat kliknutím na **oznámení**, což je ikona zvonku napravo od vyhledávacího pole. Nasazení aplikace Wingtip trvá přibližně pět minut.

   ![nasazení bylo úspěšné](./media/saas-multitenantdb-get-started-deploy/succeeded.png)

## <a name="download-and-unblock-the-management-scripts"></a>Stažení a odblokování skriptů pro správu

Při nasazení aplikace si Stáhněte zdrojový kód aplikace a skripty pro správu.

> [!NOTE]
> Obsah spustitelného souboru (skripty, knihovny DLL) může systém Windows zablokovat, pokud jsou soubory zip staženy z externího zdroje a extrahovány. Při extrakci skriptů ze souboru ZIP použijte následující postup k odblokování souboru. zip před extrakcí. Odblokování souboru. zip zajišťuje, aby bylo možné spouštět skripty.

1. Přejděte do [úložiště GitHub WingtipTicketsSaaS-MultiTenantDb](https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDb).
2. Klikněte na **klonovat nebo stáhnout**.
3. Klikněte na **Stáhnout ZIP** a uložte soubor.
4. Klikněte pravým tlačítkem na soubor **WingtipTicketsSaaS-MultiTenantDb-master.zip** a vyberte **vlastnosti**.
5. Na kartě **Obecné** vyberte **odblokovat**a klikněte na **použít**.
6. Klikněte na **OK**.
7. Extrahujte soubory.

Skripty jsou umístěny v *.. \\ WingtipTicketsSaaS-MultiTenantDb – hlavní složka \\ výukových modulů \\ *

## <a name="update-the-configuration-file-for-this-deployment"></a>Aktualizovat konfigurační soubor pro toto nasazení

Před spuštěním všech skriptů nastavte *skupinu prostředků* a *uživatelské* hodnoty v **userconfig. psm1**. Nastavte tyto proměnné na stejné hodnoty, které jste nastavili během nasazování.

1. Otevřít... \\ Výukové moduly \\ *userconfig. psm1* ve *ISE PowerShellu*.
2. Aktualizujte *ResourceGroupName* a *název* o konkrétní hodnoty pro vaše nasazení (jenom na řádcích 10 a 11).
3. Uložte změny.

Hodnoty nastavené v tomto souboru se používají ve všech skriptech, takže je důležité, aby byly přesné. Pokud aplikaci znovu nasadíte, musíte zvolit jiné hodnoty pro uživatele a skupinu prostředků. Pak znovu aktualizujte soubor UserConfig. psm1 novými hodnotami.

## <a name="run-the-application"></a>Spuštění aplikace

V aplikaci Wingtip jsou klienti místo. Umístění může být sladěná hala, sportovní klub nebo jakékoli jiné místo, které hostuje události. Místa se registrují v Wingtip jako zákazníci a identifikátor tenanta se vygeneruje pro každé místo. Každé místo obsahuje seznam nadcházejících událostí v Wingtip, takže veřejný může koupit lístky s událostmi.

Každé místo získá přizpůsobenou webovou aplikaci pro výpis svých událostí a prodávající lístky. Každá webová aplikace je nezávislá a izolovaná od ostatních tenantů. Ve výchozím nastavení jsou všechna data pro každého tenanta v Azure SQL Database interně uložená v horizontálně dělené databázi s více klienty. Všechna data jsou označena identifikátorem tenanta.

Webová stránka centrálního **centra událostí** obsahuje seznam odkazů na klienty v rámci konkrétního nasazení. Pomocí následujících kroků můžete vyzkoušet webovou stránku **centra událostí** a jednotlivé webové aplikace:

1. Otevřete **centrum událostí** ve webovém prohlížeči:
   - http://events.wingtip-mt.&lt; User &gt; . trafficmanager.NET &nbsp; *(nahraďte &lt; uživatele &gt; hodnotou uživatele vašeho nasazení.)*

     ![centrum akcí](./media/saas-multitenantdb-get-started-deploy/events-hub.png)

2. V **Centru akcí** klikněte na **Fabrikam Jazz Club**.

   ![Události](./media/saas-multitenantdb-get-started-deploy/fabrikam.png)

### <a name="azure-traffic-manager"></a>Azure Traffic Manager

Aby bylo možné řídit distribuci příchozích požadavků, používá aplikace Wingtip [Traffic Manager Azure](../../traffic-manager/traffic-manager-overview.md). Stránka události pro každého tenanta zahrnuje název tenanta v adrese URL. Každá adresa URL obsahuje také konkrétní hodnotu uživatele. Každá adresa URL dodržuje zobrazený formát pomocí následujících kroků:

- http://events.wingtip-mt.&lt; User &gt; . trafficmanager.NET/*fabrikamjazzclub*

1. Aplikace události analyzuje název tenanta z adresy URL. Název tenanta je *fabrikamjazzclub* v předchozím příkladu URL.
2. Aplikace potom vyhodnotí název tenanta vytvořením klíče pro přístup ke katalogu pomocí [správy mapování horizontálních oddílů](elastic-scale-shard-map-management.md).
3. Aplikace nalezne klíč v katalogu a získá odpovídající umístění databáze klienta.
4. Aplikace pomocí informací o poloze hledá a přistupuje k jedné databázi, která obsahuje všechna data pro tenanta.

### <a name="events-hub"></a>Centrum událostí

1. **Centrum událostí** obsahuje seznam všech klientů, kteří jsou zaregistrovaní v katalogu, a jejich místo.
2. **Centrum událostí** používá rozšířená metadata v katalogu k získání názvu klienta přidruženého ke každému mapování za účelem vytvoření adres URL.

V produkčním prostředí obvykle vytvoříte záznam DNS CNAME k [nasměrování internetové domény společnosti](../../traffic-manager/traffic-manager-point-internet-domain.md) na profil Traffic Manageru.

## <a name="start-generating-load-on-the-tenant-databases"></a>Spuštění generování zatížení databází tenantů

Teď, když je aplikace nasazená, Pojďme ji dát do práce! Skript prostředí PowerShell *demo-LoadGenerator* spustí úlohu spuštěnou pro každého tenanta. Reálné zatížení mnoha aplikací SaaS je obvykle občas a nepředvídatelné. Pro simulaci tohoto typu zatížení generátor vytvoří zatížení distribuované napříč všemi klienty. Zatížení zahrnuje náhodné shluky u každého tenanta, ke kterým dochází v náhodných intervalech. Spuštění vzorku zatížení trvá několik minut, takže je před monitorováním zatížení vhodné nechat generátor běžet aspoň tři nebo čtyři minuty.

1. V *prostředí POWERSHELL ISE*otevřete... \\ Výukové moduly \\ nástroje \\ *Demo-LoadGenerator.ps1* skriptem.
2. Stisknutím **F5** spusťte skript s generátorem zatížení (zatím nechejte nastavené výchozí hodnoty parametrů).

Skript *Demo-LoadGenerator.ps1* otevře jinou relaci PowerShellu, ve které se spustí generátor zatížení. Generátor zatížení běží v této relaci jako úloha na popředí, která vyvolá úlohy generování zatížení na pozadí, jednu pro každého tenanta.

Po spuštění úlohy na popředí zůstane ve stavu vyvolání úlohy. Tato úloha spustí další úlohy na pozadí pro všechny nové klienty, které jsou následně zřízeny.

Zavřením relace PowerShellu se zastaví všechny úlohy.

Můžete chtít restartovat relaci generátoru zatížení, aby bylo možné použít jiné hodnoty parametrů. Pokud ano, zavřete relaci generování PowerShellu a pak znovu spusťte *Demo-LoadGenerator.ps1*.

## <a name="provision-a-new-tenant-into-the-sharded-database"></a>Zřízení nového tenanta do databáze horizontálně dělené

Počáteční nasazení zahrnuje tři ukázkové klienty v databázi *Tenants1* . Pojďme vytvořit jiného tenanta a sledovat jeho účinky na nasazené aplikaci. V tomto kroku vytvoříte nového tenanta stisknutím jednoho klávesy:

1. Otevřít... \\ Výukové moduly \\ zřídí a \\ *Demo-ProvisionTenants.ps1* katalogu v *prostředí PowerShell ISE*.
2. Stisknutím klávesy **F5** (ne **F8**) spusťte skript (ponechte výchozí hodnoty nyní).

   > [!NOTE]
   > Skripty PowerShellu je nutné spustit pouze stisknutím klávesy **F5** , nikoli stisknutím klávesy **F8** pro spuštění vybrané části skriptu. Problém s **F8** je, že proměnná *$PSScriptRoot* není vyhodnocena. Tuto proměnnou vyžaduje mnoho skriptů pro procházení složek, vyvolání jiných skriptů nebo importování modulů.

Nový tenant s červenými javory se přidá do databáze *Tenants1* a zaregistruje se v katalogu. V prohlížeči se otevře web s **událostmi** pro prodej lístků pro nové tenanta:

![Nový tenant](./media/saas-multitenantdb-get-started-deploy/red-maple-racing.png)

Aktualizujte **centrum událostí**a nový tenant se teď zobrazí v seznamu.

## <a name="provision-a-new-tenant-in-its-own-database"></a>Zřízení nového tenanta ve vlastní databázi

Model horizontálně dělené multi-tenant vám umožní vybrat, jestli se má nový tenant zřídit do databáze, která obsahuje další klienty, nebo do databáze. Tenant izolovaný ve vlastní databázi má následující výhody:

- Výkon databáze klienta je možné spravovat bez nutnosti napadnout požadavky jiných tenantů.
- V případě potřeby je možné databázi obnovit do dřívějšího bodu v čase, protože by to ovlivnilo žádné jiné klienty.

Do databází s více klienty můžete umístit zákazníky s bezplatnou zkušební verzí nebo ekonomicky úsporné zákazníky. Každý tenant Premium byste mohli přidat do své vlastní vyhrazené databáze. Pokud vytvoříte spoustu databází, které obsahují jenom jednoho tenanta, můžete je spravovat společně v elastickém fondu za účelem optimalizace nákladů na prostředky.

V dalším kroku zřídíme dalšího tenanta, tentokrát ve své vlastní databázi:

1. V... \\ Výukové \\ moduly zřizování a katalog \\ *Demo-ProvisionTenants.ps1*, úpravy *$TenantName* na **Salix Salsa**, *$VenueType* na **roztancoval** a *$Scenario* na **2**.

2. Stisknutím klávesy **F5** spusťte skript znovu.
    - Tím **F5** stisknete tlačítko zřídit nového tenanta v samostatné databázi. Databáze a tenant jsou zaregistrované v katalogu. Pak se prohlížeč otevře na stránce události v tenantovi.

   ![Stránka Salix Salsa události](./media/saas-multitenantdb-get-started-deploy/salix-salsa.png)

   - Posuňte se do dolní části stránky. V informačním proužku se zobrazí název databáze, ve které jsou uložena data tenanta.

3. Aktualizujte **centrum událostí** a v seznamu se teď zobrazí dva noví klienti.

## <a name="explore-the-servers-and-tenant-databases"></a>Prozkoumejte servery a databáze tenantů

Teď se podíváme na některé nasazené prostředky:

1. V [Azure Portal](https://portal.azure.com)přejděte do seznamu skupin prostředků. Otevřete skupinu prostředků, kterou jste vytvořili při nasazení aplikace.

   ![skupina prostředků](./media/saas-multitenantdb-get-started-deploy/resource-group.png)

2. Klikněte na **katalog – &lt; Mt &gt; User** Server. Server katalogu obsahuje dvě databáze s názvem *tenantcatalog* a *basetenantdb*. Databáze *basetenantdb* je prázdná databáze šablon. Kopíruje se, aby se vytvořila nová databáze tenanta, ať už se používá pro mnoho tenantů, nebo jenom pro jednoho tenanta.

   ![katalogový server](./media/saas-multitenantdb-get-started-deploy/catalog-server.png)

3. Vraťte se do skupiny prostředků a vyberte *tenants1-MT* Server, který obsahuje databáze tenantů.
    - Databáze tenants1 je víceklientské databáze, ve které se ukládají původní tři klienty, včetně prvního tenanta, kterého jste přidali. Je nakonfigurovaný jako standardní databáze 50 DTU.
    - Databáze **salixsalsa** obsahuje Salix Salsa roztancoval místo v rámci svého jediného tenanta. Ve výchozím nastavení je nakonfigurovaná jako databáze Standard Edition s 50 DTU.

   ![Server tenantů](./media/saas-multitenantdb-get-started-deploy/tenants-server.png)

## <a name="monitor-the-performance-of-the-database"></a>Sledování výkonu databáze

Pokud byl generátor zatížení spuštěn po dobu několika minut, je k dispozici dostatek telemetrie, aby bylo možné se podívat na možnosti monitorování databáze integrované do Azure Portal.

1. Přejděte na ** &lt; uživatelský &gt; Server tenants1-MT** a kliknutím na **tenants1** Zobrazte využití prostředků pro databázi, která obsahuje čtyři klienty. Každý tenant podléhá velkému zatížení od generátoru zatížení:

   ![monitorovat tenants1](./media/saas-multitenantdb-get-started-deploy/monitor-tenants1.png)

   Graf využití DTU popisuje, jak může databáze s více klienty podporovat nepředvídatelné zatížení napříč mnoha klienty. V tomto případě generátor zatížení aplikuje občasné zatížení zhruba 30 DTU na každého tenanta. Toto zatížení je rovno 60% využití databáze DTU 50. Špičky, které překračují 60%, jsou výsledkem navýšení zatížení na více než jednoho klienta ve stejnou dobu.

2. Přejděte na ** &lt; uživatelský &gt; Server tenants1-MT** a klikněte na databázi **salixsalsa** . Můžete zobrazit využití prostředků v této databázi, která obsahují jenom jednoho tenanta.

   ![databáze salixsalsa](./media/saas-multitenantdb-get-started-deploy/monitor-salix.png)

Generátor zatížení používá podobné zatížení pro každého tenanta bez ohledu na to, ve které databázi se každý tenant nachází. V databázi **salixsalsa** jenom s jedním klientem se můžete podívat, že databáze by mohla tolerovat mnohem větší zatížení než databáze s několika klienty. 

### <a name="resource-allocations-vary-by-workload"></a>Přidělení prostředků se liší podle zatížení.

V některých případech databáze s více klienty vyžaduje více prostředků pro dobrý výkon než databáze s jedním tenanta, ale ne vždy. Optimální přidělování prostředků závisí na konkrétních charakteristikách úloh pro klienty ve vašem systému.

Úlohy vygenerované skriptem generátoru zatížení slouží pouze k ilustraci pro účely.

## <a name="additional-resources"></a>Další zdroje

- Další informace o víceklientské aplikacích SaaS najdete v tématu [vzory návrhu pro víceklientské aplikace SaaS](saas-tenancy-app-design-patterns.md).

- Další informace o elastických fondech najdete v tématech:

  - [Elastické fondy vám pomůžou se správou a škálováním více databází v Azure SQL Database](elastic-pool-overview.md)
  - [Horizontální navýšení kapacity s Azure SQL Database](elastic-scale-introduction.md)

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se dozvěděli:

> [!div class="checklist"]
> - Jak nasadit aplikaci SaaS multi-tenant Database Database aplikace Wingtip lístky
> - O serverech a databázích, které tvoří aplikaci.
> - Klienti jsou namapováni na svá data pomocí *katalogu*.
> - Jak zřídit nové klienty v databázi s více klienty a v databázi s jedním klientem.
> - Jak zobrazit využití fondu pro monitorování aktivity tenanta
> - Jak odstranit ukázkové prostředky pro zastavení související fakturace

Teď Vyzkoušejte [kurz zřízení a katalog](saas-multitenantdb-provision-and-catalog.md).


<!--  Link references.

A [series of related tutorials] is available that build upon this initial deployment.
[link-wtp-overivew-jumpto-saas-tutorials-97j]: saas-multitenantdb-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials

-->

[link-aka-ms-deploywtp-mtapp-52k]: https://aka.ms/deploywtp-mtapp


[link-azure-get-started-powershell-41q]: https://docs.microsoft.com/powershell/azure/get-started-azureps

[link-github-wingtip-multitenantdb-55g]: https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDB/



<!--  Image references.

[image-deploy-to-azure-blue-48d]: https://aka.ms/deploywtp-mtapp "Button for Deploy to Azure."
-->

[image-deploy-to-azure-blue-48d]: media/saas-multitenantdb-get-started-deploy/deploy.png "Tlačítko pro nasazení do Azure"

