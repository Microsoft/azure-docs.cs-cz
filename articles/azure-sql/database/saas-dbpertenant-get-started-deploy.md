---
title: Kurz pro databáze na tenanta SaaS
description: Nasaďte a prozkoumejte SaaS víceklientské aplikace Wingtip Tickets, která ukazuje vzor databáze na tenanta a další SaaS vzory pomocí Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/25/2019
ms.openlocfilehash: cfe440cb8ac98518547248485201b85dc0d0076d
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91356821"
---
# <a name="deploy-and-explore-a-multitenant-saas-app-that-uses-the-database-per-tenant-pattern-with-azure-sql-database"></a>Nasaďte a prozkoumejte aplikaci víceklientské SaaS, která používá model databáze na tenanta s Azure SQL Database

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

V tomto kurzu nasadíte a prozkoumáte aplikaci Wingtip Tickets SaaS Database-per-tenant (Wingtip). Aplikace používá model databáze pro jednotlivé klienty k ukládání dat více tenantů. Aplikace je navržená tak, aby předvedla funkce Azure SQL Database, které zjednodušují způsob povolení scénářů SaaS.

Pět minut po výběru **nasazení do Azure**máte víceklientské aplikace SaaS. Aplikace obsahuje databázi, která běží v Azure SQL Database. Aplikace se nasazuje se třemi ukázkovými klienty, z nichž každá má svou vlastní databázi. Všechny databáze jsou nasazeny do elastického fondu SQL. Aplikace se nasadí do vašeho předplatného Azure. Máte plný přístup, abyste mohli prozkoumat jednotlivé komponenty aplikace a pracovat s nimi. Zdrojový kód C# aplikace a skripty pro správu jsou k dispozici v [úložišti GitHub WingtipTicketsSaaS-DbPerTenant][github-wingtip-dpt].

V tomto kurzu se dozvíte:

> [!div class="checklist"]
> - Jak nasadit aplikaci Wingtip SaaS
> - Kde získám zdrojový kód aplikace a skripty pro správu.
> - O serverech, fondech a databázích, které tvoří aplikaci.
> - Jak jsou klienti mapováni na svá data pomocí *katalogu*.
> - Jak zřídit nového tenanta.
> - Jak monitorovat aktivitu klienta v aplikaci.

[Řada souvisejících výukových kurzů](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials) k prozkoumávání různých vzorů návrhu a správy SaaS Kurzy se sestavují nad rámec tohoto počátečního nasazení. Při použití kurzů můžete prostudovat poskytnuté skripty, abyste viděli, jak jsou implementované různé vzory SaaS. Skripty ukazují, jak funkce SQL Database zjednodušují vývoj aplikací SaaS.

## <a name="prerequisites"></a>Požadavky

Pro dokončení tohoto kurzu se ujistěte, že je nainstalovaná Azure PowerShell. Další informace najdete v tématu [Začínáme s Azure PowerShellem](https://docs.microsoft.com/powershell/azure/get-started-azureps).

## <a name="deploy-the-wingtip-tickets-saas-application"></a>Nasazení aplikace SaaS lístky Wingtip Tickets

### <a name="plan-the-names"></a>Plánování názvů

V krocích v této části zadáte hodnotu uživatele, která se používá k ujištění, že názvy prostředků jsou globálně jedinečné. Také zadáte název skupiny prostředků, která obsahuje všechny prostředky vytvořené nasazením aplikace. Pro fiktivní osobu s názvem Ann Finley doporučujeme:

- **Uživatel**: *AF1* se skládá z iniciál Ann Finley a číslice. Pokud aplikaci nasadíte podruhé, použijte jinou hodnotu. Příkladem je AF2.
- **Skupina prostředků**: *Wingtip-DPT-AF1* označuje, že se jedná o aplikaci databáze na tenanta. Připojíte uživatelské jméno AF1 a prokorelujte název skupiny prostředků s názvy prostředků, které obsahuje.

Vyberte si své názvy a napište je.

### <a name="steps"></a>Postup

1. Pokud chcete v Azure Portal otevřít šablonu nasazení Database – SaaS pro jednotlivé klienty, vyberte **nasadit do Azure**.

   [![Obrázek znázorňující tlačítko s názvem "nasadit do Azure".](https://azuredeploy.net/deploybutton.png)](https://aka.ms/deploywingtipdpt)

1. Zadejte hodnoty v šabloně pro požadované parametry.

    > [!IMPORTANT]
    > Některá ověřování a brány firewall serveru jsou pro demonstrační účely záměrně nezabezpečené. Doporučujeme vytvořit novou skupinu prostředků. Nepoužívejte existující skupiny prostředků, servery nebo fondy. Nepoužívejte tuto aplikaci, skripty ani žádné nasazené prostředky pro produkční prostředí. Odstraňte tuto skupinu prostředků, až budete hotovi s aplikací, abyste zastavili související fakturaci.

    - **Skupina prostředků**: vyberte **vytvořit novou**a zadejte jedinečný název, který jste zvolili dříve pro skupinu prostředků.
    - **Umístění**: v rozevíracím seznamu vyberte umístění.
    - **Uživatel**: použijte hodnotu uživatelského jména, kterou jste zvolili dříve.

1. Nasaďte aplikaci.

    a. Vyberte, chcete-li souhlasit s podmínkami a ujednáními.

    b. Vyberte **Koupit**.

1. Pokud chcete monitorovat stav nasazení, vyberte **oznámení** (ikona zvonku napravo od pole hledání). Nasazení aplikace SaaS lístky Wingtip se zabere přibližně pět minut.

   ![Nasazení bylo úspěšné.](./media/saas-dbpertenant-get-started-deploy/succeeded.png)

## <a name="download-and-unblock-the-wingtip-tickets-management-scripts"></a>Stažení a odblokování skriptů pro správu lístků Wingtip Tickets

Při nasazení aplikace Stáhněte zdrojový kód a skripty pro správu.

> [!IMPORTANT]
> Obsah spustitelného souboru (skripty a knihovny DLL) může systém Windows zablokovat v případě, že jsou soubory. zip staženy z externího zdroje a extrahovány. Před extrahováním skriptů použijte postup odblokování souboru. zip. Odblokování zajistí, že se skripty můžou spouštět.

1. Přejděte do [úložiště GitHub WingtipTicketsSaaS-DbPerTenant][github-wingtip-dpt].
1. Vyberte **Clone or download** (Naklonovat nebo stáhnout).
1. Vyberte **Stáhnout ZIP**a pak soubor uložte.
1. Klikněte pravým tlačítkem na soubor **WingtipTicketsSaaS-DbPerTenant-master.zip** a pak vyberte **vlastnosti**.
1. Na kartě **Obecné** vyberte **odblokovat**  >  **použít**.
1. Vyberte **OK**a extrahujte soubory.

Skripty se nacházejí v... \\ WingtipTicketsSaaS-DbPerTenant – hlavní \\ Složka výukových modulů

## <a name="update-the-user-configuration-file-for-this-deployment"></a>Aktualizovat konfigurační soubor uživatele pro toto nasazení

Před spuštěním všech skriptů aktualizujte skupinu prostředků a uživatelské hodnoty v konfiguračním souboru uživatele. Nastavte tyto proměnné na hodnoty, které jste použili během nasazování.

1. V prostředí PowerShell ISE otevřete... \\ Výukové moduly \\ **userconfig. psm1**
1. Aktualizujte **ResourceGroupName** a **název** o konkrétní hodnoty pro vaše nasazení (jenom na řádcích 10 a 11).
1. Uložte změny.

Na tyto hodnoty se odkazuje skoro každý skript.

## <a name="run-the-application"></a>Spuštění aplikace

Aplikace prezentuje místa, která hostují události. Mezi typy míst patří kosálye, klub nápadný a sportovní klub. V Lístkech Wingtip jsou místa registrována jako klienti. Cílem je, aby měl klient místo snadný způsob, jak vypisovat události a prodávat lístky svým zákazníkům. Každé místo získá přizpůsobený web k vypsání svých událostí a k prodeji lístků.

Interně v aplikaci každý tenant Získá databázi nasazenou do elastického fondu.

Stránka centra centrálních **událostí** obsahuje seznam odkazů na klienty v nasazení.

1. Pomocí adresy URL otevřete centrum událostí ve webovém prohlížeči: http://events.wingtip-dpt.&lt ; User &gt; . trafficmanager.NET. Nahraďte &lt; uživatele &gt; hodnotou uživatele vašeho nasazení.

    ![Centrum událostí](./media/saas-dbpertenant-get-started-deploy/events-hub.png)

2. V centru událostí vyberte **Fabrikam jazz klub** .

    ![Události](./media/saas-dbpertenant-get-started-deploy/fabrikam.png)

### <a name="azure-traffic-manager"></a>Azure Traffic Manager

Aplikace Wingtip používá k řízení distribuce příchozích žádostí službu [*Azure Traffic Manager*](../../traffic-manager/traffic-manager-overview.md) . Adresa URL pro přístup ke stránce události pro konkrétního klienta používá následující formát:

- http://events.wingtip-dpt.&lt; User &gt; . trafficmanager.NET/fabrikamjazzclub

    Části předchozího formátu jsou vysvětleny v následující tabulce.

    | Část adresy URL        | Popis       |
    | :-------------- | :---------------- |
    | události. Wingtip-DPT | Části události aplikace Wingtip.<br /><br /> *– DPT* rozlišuje implementaci lístků Wingtip *na základě databáze* z jiných implementací. Příklady jsou implementace s *jedním* z aplikací na tenanta (*-SA*) nebo *víceklientské databáze* (*-Mt*). |
    | . * &lt; Uživatel &gt; * | *AF1* v příkladu. |
    | . trafficmanager.net/ | Traffic Manager základní adresa URL. |
    | fabrikamjazzclub | Identifikuje tenanta s názvem Fabrikam nápadný klub. |
    | &nbsp; | &nbsp; |

- Název tenanta se analyzuje z adresy URL aplikace Events.
- Název tenanta se používá k vytvoření klíče.
- Klíč se používá pro přístup ke katalogu, aby bylo možné získat umístění databáze klienta.
  - Katalog je implementován pomocí *správy mapování horizontálních oddílů*.
- Centrum událostí používá rozšířená metadata v katalogu k vytvoření adres URL stránky seznam událostí pro každého tenanta.

V produkčním prostředí obvykle vytvoříte záznam DNS CNAME k [*nasměrování internetové domény společnosti*](../../traffic-manager/traffic-manager-point-internet-domain.md)   na název služby Traffic Manager DNS.

> [!NOTE]
> Nemusí být okamžitě zřejmé, co je v tomto kurzu používání Traffic Manageru. Cílem této série kurzů je předvedení vzorů, které mohou zvládnout škálování složitého provozního prostředí. V takovém případě byste například měli mít k dispozici více webových aplikací, které jsou distribuovány po celém světě, společně umístěné s databázemi. k směrování mezi těmito instancemi budete potřebovat Traffic Manager.
Další sadou výukových programů, které ilustrují použití Traffic Manageru, je to sice [geografické obnovení](../../sql-database/saas-dbpertenant-dr-geo-restore.md) a kurzy [geografické replikace](../../sql-database/saas-dbpertenant-dr-geo-replication.md) . V těchto kurzech se Traffic Manager používá k přepnutí do instance obnovení aplikace SaaS v případě regionálního výpadku.

## <a name="start-generating-load-on-the-tenant-databases"></a>Spuštění generování zatížení databází tenantů

Teď, když je aplikace nasazená, Pojďme ji převést na práci.

Skript prostředí PowerShell *demo-LoadGenerator* spustí úlohy, které běží na všech databázích tenanta. Reálné zatížení mnoha aplikací SaaS je občas a nepředvídatelné. Pro simulaci tohoto typu zatížení generátor generuje zátěž s náhodnými špičkami nebo nárůsty aktivity v každém tenantovi. Shluky se vyskytují v náhodných intervalech. Vzorek zatížení trvá několik minut. Než začnete monitorovat zatížení, počkejte, než se spustí generátor aspoň tři nebo čtyři minuty.

1. V prostředí PowerShell ISE otevřete... \\ Výukové moduly \\ nástroje \\ *Demo-LoadGenerator.ps1* skriptem.
2. Stisknutím klávesy F5 spusťte skript a spusťte generátor zátěže. Pro nyní ponechte výchozí hodnoty parametrů.
3. Přihlaste se ke svému účtu Azure a v případě potřeby vyberte předplatné, které chcete použít.

Skript generátoru zatížení spustí úlohu na pozadí pro každou databázi v katalogu a pak se zastaví. Pokud znovu spustíte skript generátoru zatížení, zastaví se všechny úlohy na pozadí spuštěné před tím, než začnou začít nové.

### <a name="monitor-the-background-jobs"></a>Monitorování úloh na pozadí

Pokud chcete řídit a monitorovat úlohy na pozadí, použijte následující rutiny:

- `Get-Job`
- `Receive-Job`
- `Stop-Job`

### <a name="demo-loadgeneratorps1-actions"></a>Demo-LoadGenerator.ps1 akce

*Demo-LoadGenerator.ps1* napodobuje aktivní úlohu transakcí zákazníků. Následující kroky popisují posloupnost akcí, které *Demo-LoadGenerator.ps1* iniciují:

1. *Demo-LoadGenerator.ps1* spustí *LoadGenerator.ps1* v popředí.

    - Soubory. ps1 jsou uložené v části složky výukové moduly \\ \\ .

2. *LoadGenerator.ps1* smyčky přes všechny databáze tenantů v katalogu.

3. *LoadGenerator.ps1* spustí úlohu PowerShellu na pozadí pro každou databázi tenanta:

    - Ve výchozím nastavení se úlohy na pozadí spouštějí po 120 minut.
    - Každá úloha způsobuje zatížení na základě procesoru v jedné databázi tenanta spuštěním *sp_CpuLoadGenerator*. Intenzita a doba trvání zatížení se liší v závislosti na `$DemoScenario` .
    - *sp_CpuLoadGenerator* cykly kolem příkazu SQL SELECT, který způsobuje vysoké zatížení procesoru. Časový interval mezi chybami výběru se liší podle hodnot parametrů, aby bylo možné vytvořit ověřitelné zatížení procesoru. Úrovně zatížení a intervaly jsou náhodné pro simulaci realističtějších zátěží.
    - Tento soubor. SQL je uložený *v \\ WingtipTenantDB \\ dbo \\ StoredProcedures*.

4. Pokud `$OneTime = $false` , generátor zatížení spustí úlohy na pozadí a pak pokračuje v běhu. Každých 10 sekund monitoruje všechny nově zřízené klienty. Pokud nastavíte `$OneTime = $true` , LoadGenerator spustí úlohy na pozadí a pak zastaví běh v popředí. Pro tento kurz ponechejte `$OneTime = $false` .

   Pokud chcete zastavit nebo restartovat generátor zatížení, použijte CTRL-C nebo ukončit operaci Ctrl + Break.

   Pokud necháte generátor zatížení běžící v popředí, použijte jinou instanci prostředí PowerShell ISE ke spuštění dalších skriptů prostředí PowerShell.

&nbsp;

Než budete pokračovat v další části, ponechte generátor zatížení spuštěný ve stavu vyvolání úlohy.

## <a name="provision-a-new-tenant"></a>Zřízení nového tenanta

Počáteční nasazení vytvoří tři ukázkové klienty. Nyní vytvoříte dalšího tenanta, abyste viděli dopad na nasazenou aplikaci. Pracovní postup pro zřízení nových tenantů v aplikaci Wingtip je vysvětlen v [kurzu zřízení a katalog](saas-dbpertenant-provision-and-catalog.md). V této fázi vytvoříte nového tenanta, který trvá méně než jednu minutu.

1. Otevřete nový PowerShellový ISE.
2. Otevřít... \\ Výukové Modules\Provision a katalogové \\ *Demo-ProvisionAndCatalog.ps1*.
3. Pokud chcete skript spustit, stiskněte klávesu F5. Pro nyní ponechte výchozí hodnoty.

   > [!NOTE]
   > Mnoho skriptů Wingtip SaaS používá *$PSScriptRoot* k procházení složek pro volání funkcí v jiných skriptech. Tato proměnná je vyhodnocena pouze v případě, že je spuštěn celý skript stisknutím klávesy F5.Zvýrazňování a spuštění výběru pomocí klávesy F8 může mít za následek chyby. Chcete-li spustit skripty, stiskněte klávesu F5.

Nová databáze tenanta:

- Vytvoří se v elastickém fondu SQL.
- Inicializované.
- Registrováno v katalogu.

Po úspěšném zřízení se v prohlížeči zobrazí web *událostí* nového tenanta.

![Nový tenant](./media/saas-dbpertenant-get-started-deploy/red-maple-racing.png)

Aktualizujte centrum událostí tak, aby se v seznamu zobrazil nový tenant.

## <a name="explore-the-servers-pools-and-tenant-databases"></a>Zkoumání serverů, fondů a databází tenantů

Teď, když jste spustili zatížení pro kolekci tenantů, si podívejme na některé z nasazených prostředků.

1. V [Azure Portal](https://portal.azure.com)přejděte do seznamu serverů SQL. Pak otevřete **Catalog-DPT- &lt; User &gt; ** Server.
    - Katalogový server obsahuje dvě databáze **tenantcatalog** a **basetenantdb** (databáze šablon, která je zkopírovaná k vytváření nových tenantů).

   ![Snímek obrazovky se stránkou přehled serveru katalogu, která obsahuje dvě databáze.](./media/saas-dbpertenant-get-started-deploy/databases.png)

2. Vraťte se do seznamu serverů SQL.

3. Otevřete **tenants1-DPT- &lt; User &gt; ** Server, který obsahuje databáze tenantů.

4. Podívejte se na následující položky:

    - Každá databáze tenanta je **elastická standardní** databáze ve fondu 50-eDTU úrovně Standard.
    - Červená Javorová databáze je databáze tenanta, kterou jste předtím zřídili.

   ![Server s databázemi](./media/saas-dbpertenant-get-started-deploy/server.png)

## <a name="monitor-the-pool"></a>Monitorování fondu

Až se *LoadGenerator.ps1* spustí několik minut, měli byste k dispozici dostatek dat, abyste mohli začít s prohlížením některých možností monitorování. Tyto funkce jsou integrované do fondů a databází.

Přejděte na server **tenants1-DPT- &lt; User &gt; **a vyberte **Pool1** pro zobrazení využití prostředků pro fond. V následujících grafech byl generátor zatížení spuštěn po jednu hodinu.

   ![Monitorovat fond](./media/saas-dbpertenant-get-started-deploy/monitor-pool.png)

- První graf, označený **využití prostředků**, zobrazuje využití eDTU fondu.
- Druhý graf znázorňuje využití eDTU v pěti nejvíce aktivních databázích ve fondu.

Tyto dva grafy znázorňují, že elastické fondy a SQL Database jsou vhodné pro nepředvídatelné úlohy aplikací SaaS. V grafech se zobrazí, že každý z nich bude mít každý nárůst velikosti až 40 eDTU a všechny databáze jsou ale ve fondu 50-eDTU pohodlně podporované. Fond 50-eDTU může podporovat i těžší úlohy. Pokud jsou databáze zřízené jako samostatné databáze, každá z nich musí být S2 (50 DTU), aby podporovala shluky. Cena za čtyři jednotlivé databáze S2 je téměř třikrát cena fondu. V reálných situacích SQL Database zákazníkům spouštět až 500 databází ve fondech 200 eDTU. Další informace najdete v [kurzu monitorování výkonu](saas-dbpertenant-performance-monitoring.md).

## <a name="additional-resources"></a>Další zdroje

- Další informace najdete v dalších [kurzech, které se sestavují na aplikaci Wingtip Tickets SaaS Database-per-tenant](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials).
- Další informace o elastických fondech najdete v tématu [co je elastický fond Azure SQL?](elastic-pool-overview.md).
- Další informace o elastických úlohách najdete v tématu [Správa cloudových databází s horizontálním škálováním kapacity](../../sql-database/elastic-jobs-overview.md).
- Další informace o víceklientské aplikacích SaaS najdete v tématu [vzory návrhu pro víceklientské aplikace SaaS](saas-tenancy-app-design-patterns.md).

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se dozvěděli:

> [!div class="checklist"]
> - Jak nasadit aplikaci SaaS lístky Wingtip.
> - O serverech, fondech a databázích, které tvoří aplikaci.
> - Jak jsou klienti mapováni na svá data pomocí *katalogu*.
> - Jak zřídit nové klienty.
> - Jak zobrazit využití fondu pro monitorování aktivity tenanta
> - Jak odstranit ukázkové prostředky pro zastavení související fakturace

V dalším kroku si vyzkoušíte [kurz zřízení a katalog](saas-dbpertenant-provision-and-catalog.md).

<!-- Link references. -->

[github-wingtip-dpt]: https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant
