---
title: Kurz SaaS pro databázi na klienta
description: Nasazení a prozkoumejte víceklientské aplikace Wingtip Tickets SaaS, která demonstruje vzor databáze na tenanta a další vzory SaaS pomocí Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MightyPen
ms.author: genemi
ms.reviewer: sstein
ms.date: 01/25/2019
ms.openlocfilehash: 31f712f80ee2492e4bbaec99bd093d46f9d04e6d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73823999"
---
# <a name="deploy-and-explore-a-multitenant-saas-app-that-uses-the-database-per-tenant-pattern-with-sql-database"></a>Nasazení a prozkoumání víceklientské aplikace SaaS, která používá vzor databáze na klienta s databází SQL Database

V tomto kurzu nasadíte a prozkoumáte wingtip tickets saas databáze na klienta aplikace (Wingtip). Aplikace používá vzor databáze na tenanta k ukládání dat více klientů. Aplikace je navržena tak, aby prezentace funkce Azure SQL Database, které zjednodušují, jak povolit scénáře SaaS.

Pět minut po výběru **nasadit do Azure**, máte víceklientské aplikace SaaS. Aplikace obsahuje databázi SQL, která běží v cloudu. Aplikace se nasadí se třemi ukázkovými klienty, každý s vlastní databází. Všechny databáze jsou nasazeny do elastického fondu SQL. Aplikace se nasadí do vašeho předplatného Azure. Máte plný přístup k prozkoumání a práci s jednotlivými součástmi aplikace. Zdrojový kód aplikace C# a skripty pro správu jsou k dispozici v [úložišti WingtipTicketsSaaS-DbPerTenant GitHub][github-wingtip-dpt].

V tomto kurzu se dozvíte:

> [!div class="checklist"]
> - Jak nasadit aplikaci Wingtip SaaS.
> - Kde získat zdrojový kód aplikace a skripty pro správu.
> - O serverech, fondech a databázích, které tvoří aplikaci.
> - Jak jsou klienti mapována na svá data s *katalogem*.
> - Jak zřídit nového klienta.
> - Jak sledovat aktivitu klienta v aplikaci.

Řada [souvisejících výukových programů](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials) nabízí prozkoumat různé vzory návrhu a správy SaaS. Kurzy sestavení mimo toto počáteční nasazení. Při použití kurzů, můžete prozkoumat poskytované skripty zobrazíte, jak jsou implementovány různé vzory SaaS. Skripty ukazují, jak funkce databáze SQL zjednodušují vývoj aplikací SaaS.

## <a name="prerequisites"></a>Požadavky

Chcete-li dokončit tento kurz, ujistěte se, že je nainstalovaný Azure PowerShell. Další informace najdete v tématu [Začínáme s Azure PowerShellem](https://docs.microsoft.com/powershell/azure/get-started-azureps).

## <a name="deploy-the-wingtip-tickets-saas-application"></a>Nasazení aplikace Wingtip Tickets SaaS

### <a name="plan-the-names"></a>Naplánujte názvy

V krocích této části zadáte uživatelskou hodnotu, která se používá k ujistěte se, že názvy prostředků jsou globálně jedinečné. Můžete také zadat název pro skupinu prostředků, která obsahuje všechny prostředky vytvořené nasazení mnosy. Pro fiktivní osobu jménem Ann Finley, doporučujeme:

- **Uživatel**: *af1* se skládá z iniciál Ann Finley plus číslice. Pokud aplikaci nasadíte podruhé, použijte jinou hodnotu. Příkladem je af2.
- **Skupina prostředků**: *wingtip-dpt-af1* označuje, že se jedná o databázi na klienta aplikace. Připojte uživatelské jméno af1 ke korelaci názvu skupiny prostředků s názvy prostředků, které obsahuje.

Vyberte si jména a zapište si je.

### <a name="steps"></a>Kroky

1. Pokud chcete na webu Azure Portal otevřít šablonu nasazení Wingtip Tickets SaaS pro jednotlivé klienty, vyberte **Nasazení do Azure**.

   <a href="https://aka.ms/deploywingtipdpt" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"/></a>

1. Zadejte hodnoty do šablony pro požadované parametry.

    > [!IMPORTANT]
    > Některé brány firewall ověřování a serveru jsou záměrně nezabezpečené pro demonstrační účely. Doporučujeme vytvořit novou skupinu prostředků. Nepoužívejte existující skupiny prostředků, servery nebo fondy. Nepoužívejte tuto aplikaci, skripty nebo žádné nasazené prostředky pro produkční prostředí. Po dokončení s aplikací odstraňte tuto skupinu prostředků, abyste zastavili související fakturaci.

    - **Skupina prostředků**: Vyberte **Vytvořit nový**a zadejte jedinečný název, který jste pro skupinu prostředků zvolili dříve.
    - **Umístění**: V rozevíracím seznamu vyberte umístění.
    - **Uživatel**: Použijte hodnotu uživatelského jména, kterou jste zvolili dříve.

1. Nasaďte aplikaci.

    a. Vyberte, chcete-li souhlasit s podmínkami.

    b. Vyberte **Koupit**.

1. Chcete-li sledovat stav nasazení, vyberte **Oznámení** (ikona zvonku napravo od vyhledávacího pole). Nasazení aplikace Wingtip Tickets SaaS trvá přibližně pět minut.

   ![Nasazení proběhlo úspěšně.](media/saas-dbpertenant-get-started-deploy/succeeded.png)

## <a name="download-and-unblock-the-wingtip-tickets-management-scripts"></a>Stažení a odblokování skriptů pro správu vstupenek Wingtip

Zatímco se aplikace nasazuje, stáhněte zdrojový kód a skripty pro správu.

> [!IMPORTANT]
> Spustitelný obsah (skripty a knihovny DLL) může být systémem Windows blokován při stahování souborů ZIP z externího zdroje a extrahování. Před extrahování skriptů odblokujte soubor ZIP následujícím postupem. Odblokování zajistí, že skripty mohou být spuštěny.

1. Přejděte na [úložiště WingtipTicketsSaaS-DbPerTenant GitHub][github-wingtip-dpt].
1. Vyberte **Clone or download** (Naklonovat nebo stáhnout).
1. Vyberte **Stáhnout zip**a uložte soubor.
1. Klepněte pravým tlačítkem myši na soubor **WingtipTicketsSaaS-DbPerTenant-master.zip** a vyberte **příkaz Vlastnosti**.
1. Na kartě **Obecné** vyberte **Odblokovat** > **použít**.
1. Vyberte **OK**a extrahujte soubory

Skripty jsou umístěny v ... \\WingtipTicketsSaaS-DbPerTenant-master\\Learning Modules.

## <a name="update-the-user-configuration-file-for-this-deployment"></a>Aktualizace konfiguračního souboru uživatele pro toto nasazení

Před spuštěním skriptů aktualizujte skupinu prostředků a uživatelské hodnoty v souboru Konfigurace uživatele. Nastavte tyto proměnné na hodnoty, které jste použili během nasazení.

1. V prostředí PowerShell ISE otevřete ... \\Výukové moduly\\**UserConfig.psm1**
1. Aktualizujte **název_skupiny ResourceGroupName** a **Name** pomocí konkrétních hodnot pro nasazení (pouze na řádcích 10 a 11).
1. Uložte změny.

Tyto hodnoty jsou odkazovány téměř v každém skriptu.

## <a name="run-the-application"></a>Spuštění aplikace

Aplikace představuje místa, která hostí události. Místo konání zahrnuje koncertní sály, jazzové kluby a sportovní kluby. V Wingtip Tickets jsou místa registrována jako nájemníci. Být nájemcem dává místo snadný způsob, jak seznam událostí a prodávat vstupenky svým zákazníkům. Každé místo dostane personalizované webové stránky, aby seznam svých akcí a prodávat vstupenky.

Interně v aplikaci každý klient získá databázi SQL nasazenou do elastického fondu SQL.

Centrální **stránka Centra událostí** obsahuje seznam odkazů na klienty ve vašem nasazení.

1. Pomocí adresy URL otevřete centrum Události http://events.wingtip-dpt.&ltve&gt;webovém prohlížeči: ;uživatel .trafficmanager.net. Nahraďte &lt;uživatele&gt; uživatelskou hodnotou nasazení.

    ![Centrum události](media/saas-dbpertenant-get-started-deploy/events-hub.png)

2. V centru Události vyberte **Fabrikam Jazz Club.**

    ![Události](./media/saas-dbpertenant-get-started-deploy/fabrikam.png)

### <a name="azure-traffic-manager"></a>Azure Traffic Manager

Aplikace Wingtip používá [*Azure Traffic Manager*](../traffic-manager/traffic-manager-overview.md) k řízení distribuce příchozích požadavků. Adresa URL pro přístup ke stránce událostí pro konkrétního klienta používá následující formát:

- http://events.wingtip-dpt.&lt;uživatel&gt;.trafficmanager.net/fabrikamjazzclub

    Části předchozího formátu jsou vysvětleny v následující tabulce.

    | Část URL        | Popis       |
    | :-------------- | :---------------- |
    | http://events.wingtip-dpt | Události části aplikace Wingtip.<br /><br /> *-dpt* odlišuje *databázi na tenanta* implementaci Wingtip Tickets od ostatních implementací. Příklady jsou *implementace jedné* aplikace na tenanta (*-sa*) nebo *víceklientské databáze* (*-mt*). |
    | . * &lt;uživatel&gt;* | *af1* v příkladu. |
    | .trafficmanager.net/ | Traffic Manager, základní adresa URL. |
    | fabrikamjazzclub | Identifikuje klienta s názvem Fabrikam Jazz Club. |
    | &nbsp; | &nbsp; |

- Název klienta je analyzován z adresy URL aplikací události.
- Název klienta se používá k vytvoření klíče.
- Klíč se používá pro přístup ke katalogu k získání umístění databáze klienta.
  - Katalog je implementován pomocí *správy map svižniny*.
- Centrum Události používá rozšířená metadata v katalogu k vytvoření adres URL stránky seznamu událostí pro každého klienta.

V produkčním prostředí obvykle vytvoříte záznam CNAME DNS, který [*nasměruje firemní internetovou doménu*](../traffic-manager/traffic-manager-point-internet-domain.md) na název DNS traffic manageru.

> [!NOTE]
> Nemusí být okamžitě zřejmé, jaké je použití správce provozu v tomto kurzu. Cílem této série kurzů je předvést vzory, které mohou zpracovat rozsah složitého produkčního prostředí. V takovém případě byste například měli více webových aplikací distribuovaných po celém světě, které by byly umístěny společně s databázemi, a k směrování mezi těmito instancemi byste potřebovali správce provozu.
Další sada výukových programů, které ilustruje použití správce provozu i když jsou [geo-obnovení](saas-dbpertenant-dr-geo-restore.md) a [geo-replikace](saas-dbpertenant-dr-geo-replication.md) kurzy. V těchto kurzech správce provozu se používá k přechodu na instanci obnovení aplikace SaaS v případě regionálního výpadku.

## <a name="start-generating-load-on-the-tenant-databases"></a>Spuštění generování zatížení databází tenantů

Teď, když je aplikace nasazená, zatáčme ji do práce.

Ukázkový skript PowerShell *Uvaděč* spustí úlohu, která běží proti všem databázím klienta. Skutečné zatížení mnoha aplikací SaaS je sporadické a nepředvídatelné. Chcete-li simulovat tento typ zatížení, generátor vytváří zatížení s randomizované špičky nebo shluky aktivity na každém klientovi. K shlukům dochází v náhodných intervalech. Trvá několik minut, než se objeví vzor zatížení. Nechte generátor běžet alespoň tři nebo čtyři minuty, než budete sledovat zatížení.

1. V prostředí PowerShell ISE otevřete ... \\Výukové moduly\\\\Utility*Demo-LoadGenerator.ps1* skript.
2. Stisknutím klávesy F5 spusťte skript a spusťte generátor zatížení. Prozatím ponechte výchozí hodnoty parametrů.
3. Přihlaste se ke svému účtu Azure a v případě potřeby vyberte předplatné, které chcete použít.

Skript generátoru zatížení spustí úlohu na pozadí pro každou databázi v katalogu a poté se zastaví. Pokud znovu spustíte skript generátoru zatížení, zastaví všechny úlohy na pozadí, které jsou spuštěny před spuštěním nové.

### <a name="monitor-the-background-jobs"></a>Sledování úloh na pozadí

Pokud chcete řídit a sledovat úlohy na pozadí, použijte následující rutiny:

- `Get-Job`
- `Receive-Job`
- `Stop-Job`

### <a name="demo-loadgeneratorps1-actions"></a>Akce Demo-LoadGenerator.ps1

*Demo-LoadGenerator.ps1* napodobuje aktivní zatížení transakcí odběratele. Následující kroky popisují posloupnost akcí, které *demo-LoadGenerator.ps1* iniciuje:

1. *Demo-LoadGenerator.ps1* spustí *LoadGenerator.ps1* v popředí.

    - Oba soubory PS1 jsou uloženy pod\\složkami Výukové moduly Utilities\\.

2. *LoadGenerator.ps1* smyčky přes všechny databáze klientů v katalogu.

3. *LoadGenerator.ps1* spustí úlohu prostředí PowerShell na pozadí pro každou databázi klienta:

    - Ve výchozím nastavení jsou úlohy na pozadí spuštěny po dobu 120 minut.
    - Každá úloha způsobí zatížení na základě procesoru na jedné databázi klienta spuštěním *sp_CpuLoadGenerator*. Intenzita a doba trvání zatížení `$DemoScenario`se liší v závislosti na .
    - *sp_CpuLoadGenerator* smyčky kolem příkazu SQL SELECT, který způsobuje vysoké zatížení procesoru. Časový interval mezi problémy SELECT se liší v závislosti na hodnotách parametrů a vytváří tak kontrolovatelné zatížení procesoru. Úrovně zatížení a intervaly jsou randomizovány, aby simulovaly realističtější zatížení.
    - Tento soubor .SQL je uložen v části *WingtipTenantDB\\\\dbo StoredProcedures\\*.

4. Pokud `$OneTime = $false`generátor zatížení spustí úlohy na pozadí a pak pokračuje v běhu. Každých 10 sekund monitoruje všechny nové klienty, které jsou zřízeny. Pokud nastavíte `$OneTime = $true`, LoadGenerator spustí úlohy na pozadí a potom přestane běžet v popředí. Pro účely tohoto `$OneTime = $false`kurzu ponechte .

   Chcete-li zastavit nebo restartovat generátor zatížení, použijte operaci Ctrl-C nebo Stop Ctrl-Break.

   Pokud necháte generátor zatížení spuštěný v popředí, použijte jinou instanci prostředí PowerShell ISE ke spuštění jiných skriptů prostředí PowerShell.

&nbsp;

Než budete pokračovat v další části, nechte generátor zatížení spuštěný ve stavu vyvolání úlohy.

## <a name="provision-a-new-tenant"></a>Zřízení nového tenanta

Počáteční nasazení vytvoří tři ukázkové klienty. Teď vytvoříte jiného klienta, abyste viděli dopad na nasazenou aplikaci. V aplikaci Wingtip je pracovní postup pro zřízení nových klientů vysvětlen v [kurzu Provision and catalog](saas-dbpertenant-provision-and-catalog.md). V této fázi vytvoříte nového klienta, který trvá méně než jednu minutu.

1. Otevřete novou powershellovou ISE.
2. Otevřít... \\Výukové moduly\Zřizování a katalog\\*Demo-ProvisionAndCatalog.ps1*.
3. Skript spustíte stisknutím klávesy F5. Prozatím ponechte výchozí hodnoty.

   > [!NOTE]
   > Mnoho skriptů Wingtip SaaS používá *$PSScriptRoot* k procházení složek pro volání funkcí v jiných skriptech. Tato proměnná je vyhodnocena pouze v případě, že je úplný skript spuštěn stisknutím klávesy F5.Zvýraznění a spuštění výběru pomocí F8 může mít za následek chyby. Skripty spusťte stisknutím klávesy F5.

Nová databáze klienta je:

- Vytvořeno v elastickém fondu SQL.
- Inicializován.
- Registrováno v katalogu.

Po úspěšném zřizování se v prohlížeči zobrazí web *Události* nového klienta.

![Nový tenant](./media/saas-dbpertenant-get-started-deploy/red-maple-racing.png)

Aktualizujte centrum Události, aby se nový klient zobrazil v seznamu.

## <a name="explore-the-servers-pools-and-tenant-databases"></a>Zkoumání serverů, fondů a databází tenantů

Teď, když jste začali spouštět zatížení proti kolekci klientů, podívejme se na některé prostředky, které byly nasazeny.

1. Na [webu Azure Portal](https://portal.azure.com)vyhledejte seznam serverů SQL. Potom otevřete **katalog-dpt-USER&lt;&gt; ** server.
    - Katalogový server obsahuje dvě databáze, **katalog klientů** a **basetenantdb** (šablona databáze, která je zkopírována k vytvoření nových klientů).

   ![Databáze](./media/saas-dbpertenant-get-started-deploy/databases.png)

2. Vraťte se do seznamu serverů SQL.

3. Otevřete **&lt;tenants1-dpt-&gt; USER** server, který obsahuje databáze klienta.

4. Viz následující položky:

    - Každá databáze klienta je **databáze Elastic Standard** ve standardním fondu 50 eDTU.
    - Databáze Red Maple Racing je databáze klienta, kterou jste zřídit dříve.

   ![Server s databázemi](./media/saas-dbpertenant-get-started-deploy/server.png)

## <a name="monitor-the-pool"></a>Monitorování fondu

Po *LoadGenerator.ps1* běží několik minut, dostatek dat by měly být k dispozici začít hledat na některé možnosti monitorování. Tyto funkce jsou integrovány do fondů a databází.

Přejděte na **klienty serveru1-dpt-&lt;uživatel&gt;** a vyberte **Pool1** pro zobrazení využití prostředků pro fond. V následujících grafech běžel generátor zatížení po dobu jedné hodiny.

   ![Fond monitorování](./media/saas-dbpertenant-get-started-deploy/monitor-pool.png)

- První graf s označením **Využití prostředků**zobrazuje využití fondu eDTU.
- Druhý graf ukazuje využití eDTU pěti nejaktivnějších databází ve fondu.

Dva grafy ilustrují, že elastické fondy a SQL Database jsou vhodné pro nepředvídatelné úlohy aplikací SaaS. Grafy ukazují, že čtyři databáze jsou každý prasknutí až 40 eDTU, a přesto všechny databáze jsou pohodlně podporovány fondu 50 eDTU. Fond 50 eDTU může podporovat ještě těžší úlohy. Pokud databáze jsou zřízeny jako jediné databáze, každý z nich musí být S2 (50 DTU) pro podporu shluky. Náklady na čtyři jednotlivé databáze S2 je téměř trojnásobek ceny bazénu. V reálných situacích zákazníci SQL Database spustit až 500 databází ve 200 fondech eDTU. Další informace naleznete v [kurzu sledování výkonu](saas-dbpertenant-performance-monitoring.md).

## <a name="additional-resources"></a>Další zdroje

- Další informace naleznete v [dalších kurzech, které se staví na wingtip tickets saas databáze na klienta aplikace](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials).
- Další informace o elastických fondech najdete [v tématu Co je elastický fond Azure SQL?](sql-database-elastic-pool.md).
- Další informace o elastických úlohách najdete [v tématu Správa cloudových databází s horizontálním navýšením kapacity](elastic-jobs-overview.md).
- Další informace o víceklientských aplikacích SaaS najdete [v tématu Návrhové vzory pro víceklientské aplikace SaaS](saas-tenancy-app-design-patterns.md).

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se dozvěděli:

> [!div class="checklist"]
> - Jak nasadit aplikaci Wingtip Tickets SaaS.
> - O serverech, fondech a databázích, které tvoří aplikaci.
> - Jak jsou klienti mapována na svá data s *katalogem*.
> - Jak zřídit nové nájemníky.
> - Jak zobrazit využití fondu pro sledování aktivity klienta.
> - Jak odstranit ukázkové prostředky k zastavení související fakturace.

Dále zkuste [zřízení a katalog výuky](saas-dbpertenant-provision-and-catalog.md).

<!-- Link references. -->

[github-wingtip-dpt]: https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant
