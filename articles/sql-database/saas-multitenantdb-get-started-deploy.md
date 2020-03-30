---
title: Nasazení rozdělené víceklientské databáze aplikace SaaS
description: Nasazení a prozkoumání oborové wingtip vstupenky SaaS víceklientské databázové aplikace, která demonstruje vzory SaaS pomocí Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MightyPen
ms.author: genemi
ms.reviewer: billgib, stein
ms.date: 10/16/2018
ms.openlocfilehash: 3277318e01362df8fc21ff7ca769aaeb8006abc6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73827993"
---
# <a name="deploy-and-explore-a-sharded-multi-tenant-application"></a>Nasazení a prozkoumání rozdělené víceklientské aplikace

V tomto kurzu nasadíte a prozkoumáte ukázkovou víceklientské aplikace SaaS s názvem Wingtip Tickets. Aplikace Wingtip Tickets je navržena tak, aby prezentovala funkce Azure SQL Database, které zjednodušují implementaci scénářů SaaS.

Tato implementace aplikace Wingtip Tickets používá tříděný vzor databáze s více klienty. Stor z rážetek je podle identifikátoru klienta. Data klienta se distribuují do určité databáze podle hodnot identifikátoru klienta. 

Tento vzor databáze umožňuje uložit jeden nebo více klientů v každém oddílu nebo databáze. Můžete optimalizovat pro nejnižší náklady tím, že každá databáze bude sdílena více klienty. Nebo můžete optimalizovat pro izolaci tím, že každý databázový obchod pouze jeden klient. Vaše volba optimalizace může být provedena nezávisle pro každého konkrétního klienta. Vaše volba může být provedena při prvním uložení klienta, nebo si to můžete později rozmyslet. Aplikace je navržena tak, aby dobře fungovala v obou směrech.

## <a name="app-deploys-quickly"></a>Aplikace se nasazuje rychle

Aplikace běží v cloudu Azure a používá Azure SQL Database. Následující část nasazení poskytuje modré tlačítko **Nasazení do Azure.** Po stisknutí tlačítka se aplikace plně nasadí do vašeho předplatného Azure do pěti minut. Máte plný přístup k práci s jednotlivými součástmi aplikace.

Aplikace se nasazuje s daty pro tři ukázkové klienty. Klienti jsou uloženy společně v jedné databázi více klientů.

Zdrojový kód C# a PowerShell pro Wingtip Tickets si může stáhnout kdokoli ze [svého úložiště GitHub][link-github-wingtip-multitenantdb-55g].

## <a name="learn-in-this-tutorial"></a>Další informace v tomto kurzu

> [!div class="checklist"]
> - Jak nasadit aplikaci Wingtip Tickets SaaS.
> - Kde získat zdrojový kód aplikace a skripty pro správu.
> - O serverech a databázích, které tvoří aplikaci.
> - Jak jsou klienti mapována na svá data s *katalogem*.
> - Jak zřídit nového klienta.
> - Jak sledovat aktivitu klienta v aplikaci.

K dispozici je řada souvisejících kurzů, které vycházejí z tohoto počátečního nasazení. Kurzy prozkoumat celou řadu vzorů návrhu a správy SaaS. Při práci prostřednictvím kurzů, doporučujeme krokovat poskytované skripty vidět, jak jsou implementovány různé vzory SaaS.

## <a name="prerequisites"></a>Požadavky

Předpokladem dokončení tohoto kurzu je splnění následujících požadavků:

- Je nainstalovaný nejnovější Azure PowerShell. Podrobnosti najdete [v tématu Začínáme s Azure PowerShell][link-azure-get-started-powershell-41q].

## <a name="deploy-the-wingtip-tickets-app"></a>Nasazení aplikace Wingtip Tickets

### <a name="plan-the-names"></a>Naplánujte názvy

V krocích této části zadáte *uživatelskou* hodnotu, která se používá k zajištění, že názvy prostředků jsou globálně jedinečné, a název *skupiny prostředků,* která obsahuje všechny prostředky vytvořené nasazením aplikace. Pro osobu jménem *Ann Finley*, doporučujeme:
- *Uživatel:* **af1**  *(Jejich iniciály, plus číslice. Pokud aplikaci nasadíte podruhé, použijte jinou hodnotu (např. af2).)*
- *Skupina prostředků:* **wingtip-mt-af1** *(wingtip-mt označuje, že se jedná o vrstvenou víceklientské aplikace.*

Vyberte si jména a zapište si je. 

### <a name="steps"></a>Kroky

1. Klikněte na následující modré tlačítko **Nasadit do Azure.**
   - Otevře portál Azure se šablonou nasazení Wingtip Tickets SaaS.

     [![Tlačítko pro nasazení do Azure.][image-deploy-to-azure-blue-48d]][link-aka-ms-deploywtp-mtapp-52k]

1. Zadejte požadované hodnoty parametrů pro nasazení.

    > [!IMPORTANT]
    > Pro tuto ukázku nepoužívejte žádné již existující skupiny prostředků, servery nebo fondy. Místo toho zvolte **Vytvořit novou skupinu prostředků**. Po ukončení práce s aplikací tuto skupinu prostředků odstraňte, aby se zastavilo související účtování.
    > Nepoužívejte tuto aplikaci ani žádné prostředky, které vytvoří, pro výrobu. Některé aspekty ověřování a nastavení brány firewall serveru jsou v aplikaci záměrně nezabezpečené, aby se usnadnila demonstrace.

    - Pro **skupinu prostředků** – vyberte **vytvořit nový**a zadejte **název** pro skupinu prostředků (rozlišují malá a velká písmena).
        - V rozevíracím seznamu vyberte **umístění.**
    - Pro **uživatele** – doporučujeme zvolit **krátkou** uživatelskou hodnotu.

1. **Nasaďte aplikaci**.

    - Klikněte pro souhlas s podmínkami.
    - Klikněte na **Koupit**.

1. Sledování stavu nasazení kliknutím na **notifications**, což je ikona zvonku napravo od vyhledávacího pole. Nasazení aplikace Wingtip trvá přibližně pět minut.

   ![nasazení bylo úspěšné](media/saas-multitenantdb-get-started-deploy/succeeded.png)

## <a name="download-and-unblock-the-management-scripts"></a>Stažení a odblokování skriptů pro správu

Během nasazování aplikace stáhněte zdrojový kód aplikace a skripty pro správu.

> [!NOTE]
> Spustitelný obsah (skripty, knihovny DLL) může být blokován systémem Windows při stahování souborů zip z externího zdroje a extrahování. Při extrahování skriptů ze souboru ZIP odblokujte soubor ZIP před extrahováním pomocí následujících kroků. Odblokováním souboru ZIP zajistíte, že budou moci být skripty spuštěny.

1. Přejděte na [úložiště WingtipTicketsSaaS-MultiTenantDb GitHub](https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDb).
2. Klepněte na **možnost Klonovat nebo stáhnout**.
3. Klikněte na **Stáhnout ZIP** a uložte soubor.
4. Klepněte pravým tlačítkem myši na soubor **WingtipTicketsSaaS-MultiTenantDb-master.zip** a vyberte **příkaz Vlastnosti**.
5. Na kartě **Obecné** vyberte **Odblokovat**a klepněte na **použít**.
6. Klikněte na tlačítko **OK**.
7. Extrahujte soubory.

Skripty jsou umístěny v *.. WingtipTicketsSaaS-MultiTenantDb-master\\Learning Modules.\\ \\*

## <a name="update-the-configuration-file-for-this-deployment"></a>Aktualizace konfiguračního souboru pro toto nasazení

Před spuštěním skriptů nastavte *skupinu prostředků* a *uživatelské* hodnoty v **souboru UserConfig.psm1**. Nastavte tyto proměnné na stejné hodnoty, které jste nastavili během nasazení.

1. Otevřít... \\Výukové moduly\\*UserConfig.psm1* v prostředí *PowerShell ISE*.
2. Aktualizujte *název_skupiny ResourceGroupName* a *Name* pomocí konkrétních hodnot pro nasazení (pouze na řádcích 10 a 11).
3. Uložte změny.

Hodnoty nastavené v tomto souboru jsou používány všemi skripty, takže je důležité, aby byly přesné. Pokud aplikaci znovu nasadíte, musíte zvolit různé hodnoty pro uživatele a skupinu prostředků. Potom znovu aktualizujte soubor UserConfig.psm1 novými hodnotami.

## <a name="run-the-application"></a>Spuštění aplikace

V aplikaci Wingtip jsou nájemníci místy. Místo konání může být koncertní sál, sportovní klub, nebo jakékoliv jiné místo, které hostí akce. Místa zaregistrovat v Wingtip jako zákazníci a identifikátor klienta je generovánpro každé místo. Každé místo uvádí své nadcházející akce v Wingtip, takže veřejnost si může koupit vstupenky na akce.

Každé místo dostane personalizovanou webovou aplikaci, která uvádí jejich události a prodává vstupenky. Každá webová aplikace je nezávislá a izolovaná od ostatních klientů. Interně v Azure SQL Database, každý data pro každého klienta je uložena v databázi s oddíly více klientů, ve výchozím nastavení. Všechna data jsou označena identifikátorem klienta.

Webová stránka centra událostí v centru **centrální** ho poskytuje seznam odkazů na klienty ve vašem konkrétním nasazení. Pomocí následujících kroků vyzkoušejte webovou stránku **Centra událostí** a individuální webovou aplikaci:

1. Otevřete **centrum Události** ve webovém prohlížeči:
   - http://events.wingtip-mt.&lt;uživatel&gt;.trafficmanager.net &nbsp; * &lt;(Nahraďte&gt; uživatele uživatelskou hodnotou nasazení.)*

     ![centrum akcí](media/saas-multitenantdb-get-started-deploy/events-hub.png)

2. V **Centru akcí** klikněte na **Fabrikam Jazz Club**.

   ![Události](./media/saas-multitenantdb-get-started-deploy/fabrikam.png)

### <a name="azure-traffic-manager"></a>Azure Traffic Manager

K řízení distribuce příchozích požadavků používá aplikace Wingtip [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md). Stránka události pro každého klienta obsahuje název klienta v jeho adrese URL. Každá adresa URL také obsahuje vaši konkrétní uživatelskou hodnotu. Každá adresa URL se řídí zobrazeným formátem pomocí následujících kroků:

- http://events.wingtip-mt.&lt;uživatel&gt;.trafficmanager.net/*fabrikamjazzclub*

1. Aplikace události analyzuje název klienta z adresy URL. Název klienta je *fabrikamjazzclub* v předchozím příkladu URL.
2. Aplikace pak zařazuje název klienta k vytvoření klíče pro přístup ke katalogu pomocí [správy mapy svižné bitové části](sql-database-elastic-scale-shard-map-management.md).
3. Aplikace najde klíč v katalogu a získá odpovídající umístění databáze klienta.
4. Aplikace používá informace o poloze k vyhledání a přístup u jedné databáze, která obsahuje všechna data pro klienta.

### <a name="events-hub"></a>Centrum události

1. **Centrum událostí** uvádí všechny klienty, kteří jsou registrováni v katalogu a jejich místa.
2. Centrum **Události** používá rozšířená metadata v katalogu k načtení názvu klienta přidruženého ke každému mapování k vytvoření adres URL.

V produkčním prostředí obvykle vytvoříte záznam CNAME DNS, který [nasměruje firemní internetovou doménu](../traffic-manager/traffic-manager-point-internet-domain.md) na profil správce provozu.

## <a name="start-generating-load-on-the-tenant-databases"></a>Spuštění generování zatížení databází tenantů

Nyní, když je aplikace nasazena, pojďme ji do práce! Ukázkový skript PowerShell *Uvaděče* spustí úlohu spuštěnou pro každého klienta. Skutečné zatížení mnoha aplikací SaaS je obvykle sporadické a nepředvídatelné. Chcete-li simulovat tento typ zatížení, generátor vytváří zatížení distribuované mezi všechny klienty. Zatížení zahrnuje náhodné shluky na každém tenantovi dochází v randomizovaných intervalech. Trvá několik minut, než se objeví vzor zatížení, takže je nejlepší nechat generátor běžet alespoň tři nebo čtyři minuty před sledováním zatížení.

1. V *prostředí PowerShell ISE*otevřete ... \\Výukové moduly\\\\Utility*Demo-LoadGenerator.ps1* skript.
2. Stisknutím **F5** spusťte skript s generátorem zatížení (zatím nechejte nastavené výchozí hodnoty parametrů).

Skript *Demo-LoadGenerator.ps1* otevře další relaci prostředí PowerShell, ve které běží generátor zatížení. Generátor zatížení běží v této relaci jako úloha v popředí, která vyvolá úlohy generování zatížení na pozadí, jeden pro každého klienta.

Po spuštění úlohy v popředí zůstane ve stavu vyvolání úlohy. Úloha spustí další úlohy na pozadí pro všechny nové klienty, které jsou následně zřízeny.

Ukončení relace prostředí PowerShell zastaví všechny úlohy.

Můžete chtít restartovat relaci generátoru zatížení použít různé hodnoty parametrů. Pokud ano, zavřete relaci generování prostředí PowerShell a znovu spusťte *soubor Demo-LoadGenerator.ps1*.

## <a name="provision-a-new-tenant-into-the-sharded-database"></a>Zřízení nového klienta do databáze s rozdělením.

Počáteční nasazení zahrnuje tři ukázkové klienty v *databázi Tenants1.* Pojďme vytvořit jiného klienta a sledovat jeho účinky na nasazené aplikace. V tomto kroku stisknutím jedné klávesy vytvoříte nového klienta:

1. Otevřít... \\Učení\\moduly zřízení\\a katalog*Ukáznní ProvisionTenants.ps1* v *Prostředí PowerShell ISE*.
2. Stisknutím **klávesy F5** (nikoli **F8)** spusťte skript (prozatím ponechte výchozí hodnoty).

   > [!NOTE]
   > Skripty prostředí PowerShell je nutné spustit pouze stisknutím klávesy **F5,** nikoli stisknutím **klávesy F8** pro spuštění vybrané části skriptu. Problém s **F8** je, že *$PSScriptRoot* proměnná není vyhodnocena. Tato proměnná je potřeba mnoho skriptů pro navigaci ve složkách, vyvolání jiných skriptů nebo import modulů.

Nový klient Red Maple Racing je přidán do databáze *Tenants1* a registrován v katalogu. Ve vašem prohlížeči se otevře web **událostí** prodeje vstupenek nového klienta:

![Nový tenant](./media/saas-multitenantdb-get-started-deploy/red-maple-racing.png)

Aktualizujte **centrum Události**a nový klient se nyní zobrazí v seznamu.

## <a name="provision-a-new-tenant-in-its-own-database"></a>Zřízení nového klienta ve vlastní databázi

Oborový model s více tenanty umožňuje zvolit, zda chcete zřídit nového klienta do databáze, která obsahuje další klienty, nebo do vlastní databáze. Tenant izolovaný ve své vlastní databázi využívá následující výhody:

- Výkon databáze klienta lze spravovat bez nutnosti ohrožení s potřebami jiných klientů.
- V případě potřeby databáze lze obnovit do dřívějšího bodu v čase, protože žádné jiné klienty by byly ovlivněny.

Můžete se rozhodnout umístit zákazníky s bezplatnou zkušební verzí nebo ekonomické zákazníky do databází s více klienty. Každý prémiový tenant můžete umístit do vlastní vyhrazené databáze. Pokud vytvoříte velké množství databází, které obsahují pouze jednoho klienta, můžete je všechny spravovat společně v elastickém fondu a optimalizovat náklady na prostředky.

Dále zřídíme jiného klienta, tentokrát ve vlastní databázi:

1. In... \\\\Learning Modules Provision\\and Catalog*Demo-ProvisionTenants.ps1*, upravte *$TenantName* s **salix salsa**, *$VenueType* **k tanci** a *$Scenario* na **2**.

2. Dalším stisknutím **klávesy F5** spusťte skript.
    - Tento **tisk F5** zřazuje nového klienta v samostatné databázi. Databáze a klient jsou registrovány v katalogu. Pak se prohlížeč otevře na stránce Události klienta.

   ![Stránka akcí Salix Salsa](./media/saas-multitenantdb-get-started-deploy/salix-salsa.png)

   - Přejděte do dolní části stránky. Tam v banneru se zobrazí název databáze, ve kterém jsou uložena data klienta.

3. Aktualizujte **centrum Událostí** a dva noví klienti se nyní zobrazí v seznamu.

## <a name="explore-the-servers-and-tenant-databases"></a>Prozkoumejte servery a databáze klientů

Nyní se podíváme na některé z prostředků, které byly nasazeny:

1. Na [webu Azure Portal](https://portal.azure.com)přejděte do seznamu skupin prostředků. Otevřete skupinu prostředků, kterou jste vytvořili při nasazení aplikace.

   ![skupina prostředků](./media/saas-multitenantdb-get-started-deploy/resource-group.png)

2. Klikněte na **&lt;uživatelský&gt; server catalog-mt.** Server katalogu obsahuje dvě databáze s názvem *tenantcatalog* a *basetenantdb*. Databáze *basetenantdb* je prázdná databáze šablon. Zkopíruje se k vytvoření nové databáze klienta, ať už se používá pro mnoho klientů nebo pouze jednoho klienta.

   ![katalogový server](./media/saas-multitenantdb-get-started-deploy/catalog-server.png)

3. Vraťte se do skupiny prostředků a vyberte *tenants1-mt* server, který obsahuje databáze klientů.
    - Databáze tenants1 je databáze s více klienty, ve které jsou uloženy původní tři klienti a první klient, který jste přidali. Je konfigurován jako databáze 50 DTU Standard.
    - V databázi **salixsalsa** je taneční místo Salix Salsa považováno za svého jediného nájemce. Ve výchozím nastavení je konfigurována jako databáze standardní edice s 50 dtu.

   ![server klientů](./media/saas-multitenantdb-get-started-deploy/tenants-server.png)

## <a name="monitor-the-performance-of-the-database"></a>Sledování výkonu databáze

Pokud generátor zatížení běží několik minut, je k dispozici dostatek telemetrie, aby se podíval na možnosti monitorování databáze integrované do portálu Azure.

1. Přejděte na **uživatelský&gt; server&lt;tenantů1 mt** a kliknutím na **tenanty1** zobrazte využití prostředků pro databázi, která má čtyři klienty. Každý klient je vystaven sporadickému těžkému zatížení z generátoru zatížení:

   ![sledovat klienty1](./media/saas-multitenantdb-get-started-deploy/monitor-tenants1.png)

   Graf využití DTU pěkně ilustruje, jak víceklientské databáze může podporovat nepředvídatelné zatížení v mnoha tenantů. V tomto případě generátor zatížení používá sporadické zatížení zhruba 30 DTU pro každého klienta. Toto zatížení se rovná 60 % využití databáze 50 DTU. Špičky, které přesahují 60 % jsou výsledkem zatížení, které je aplikováno na více než jednoho klienta současně.

2. Přejděte na **uživatelský&gt; server&lt;tenantů1-mt** a klikněte na databázi **salixsalsa.** V této databázi, která obsahuje pouze jednoho klienta, můžete zobrazit využití prostředků.

   ![databáze salixsalsa](./media/saas-multitenantdb-get-started-deploy/monitor-salix.png)

Generátor zatížení používá podobné zatížení pro každého klienta, bez ohledu na to, ve které databázi se každý klient nachází. S pouze jeden klient v **databázi salixsalsa,** můžete vidět, že databáze může udržet mnohem vyšší zatížení než databáze s několika klienty. 

### <a name="resource-allocations-vary-by-workload"></a>Přidělení prostředků se liší podle pracovního vytížení

Někdy databáze s více klienty vyžaduje více prostředků pro dobrý výkon než databáze jednoho klienta, ale ne vždy. Optimální přidělení prostředků závisí na konkrétní charakteristiky pracovního vytížení pro klienty ve vašem systému.

Úlohy generované skriptem generátoru zatížení jsou pouze pro ilustrační účely.

## <a name="additional-resources"></a>Další zdroje

- Další informace o víceklientských aplikacích SaaS najdete [v tématu Návrhové vzory pro víceklientské aplikace SaaS](saas-tenancy-app-design-patterns.md).

- Další informace o elastických fondech najdete v tématu:

  - [Elastické fondy umožňují spravovat a škálovat více databází Azure SQL](sql-database-elastic-pool.md)
  - [Horizontální navýšení kapacity s Azure SQL Database](sql-database-elastic-scale-introduction.md)

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se dozvěděli:

> [!div class="checklist"]
> - Jak nasadit wingtip vstupenky SaaS víceklientské databázové aplikace.
> - O serverech a databázích, které tvoří aplikaci.
> - Klienti jsou mapovány na svá data s *katalogem*.
> - Jak zřídit nové klienty do databáze s více klienty a databáze jednoho klienta.
> - Jak zobrazit využití fondu pro sledování aktivity klienta.
> - Jak odstranit ukázkové prostředky k zastavení související fakturace.

Nyní zkuste [zřízení a katalog tutorial](sql-database-saas-tutorial-provision-and-catalog.md).


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

[image-deploy-to-azure-blue-48d]: media/saas-multitenantdb-get-started-deploy/deploy.png "Tlačítko pro nasazení do Azure."

