---
title: Databáze na tenanta SaaS kurz – Azure SQL Database | Dokumentace Microsoftu
description: Nasazení a zkoumání SaaS aplikace Wingtip Tickets víceklientské aplikace, která ukazuje vzor databáze na tenanta a ostatní vzorce SaaS pomocí Azure SQL Database.
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
ms.date: 04/01/2018
ms.openlocfilehash: 77e3cdcbd18a4a5313160b947ce278a75f3e3de3
ms.sourcegitcommit: 715813af8cde40407bd3332dd922a918de46a91a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "47056382"
---
# <a name="deploy-and-explore-a-multitenant-saas-app-that-uses-the-database-per-tenant-pattern-with-sql-database"></a>Nasazení a zkoumání víceklientské aplikace SaaS, která používá vzor databáze na tenanta s využitím SQL Database

V tomto kurzu nasazení a zkoumání SaaS aplikace Wingtip Tickets aplikace databáze na tenanta (Wingtip). Aplikace používá model databáze na tenanta k ukládání dat z více tenantů. Tato aplikace je určená aby ukazovala funkce služby Azure SQL Database, které zjednodušují jak povolit scénáře SaaS.

Pět minut, po výběru **nasadit do Azure**, máte víceklientské aplikace SaaS. Tato aplikace obsahuje databázi SQL, na kterém běží v cloudu. Aplikace se nasazuje se třemi ukázkovými klienty, z nichž každá má svou vlastní databázi. Všechny databáze se nasazují do elastického fondu SQL. Aplikace se nasadí do vašeho předplatného Azure. Máte úplný přístup ke zkoumání a práci s jednotlivých součástí aplikace. Jsou k dispozici v jazyce C# zdrojový kód aplikace a skripty pro správu [úložiště GitHub WingtipTicketsSaaS DbPerTenant][github-wingtip-dpt].

V tomto kurzu se dozvíte:

> [!div class="checklist"]
> - Jak nasadit aplikaci SaaS aplikace Wingtip.
> - Kde lze získat skripty kód a správu zdroje aplikace.
> - O serverů, fondů a databází, které tvoří aplikaci.
> - Jak se tenanti jsou namapovaní na svá data pomocí *katalogu*.
> - Jak zřídit nového tenanta.
> - Jak monitorovat aktivity tenanta v aplikaci.

A [řadu souvisejících kurzů](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials) nabízí k prozkoumání různých vzorů návrhu a správy SaaS. V kurzech vytvořte nad rámec tohoto počátečního nasazení. Při použití v kurzech, můžete zkoumat zadané skripty, abyste zjistili, jak se implementují různé vzorce SaaS. Skripty ukazují, jak funkce služby SQL Database zjednodušuje vývoj aplikací SaaS.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto kurzu, ujistěte se, že je nainstalovaný Azure PowerShell. Další informace najdete v tématu [Začínáme s Azure Powershellem](https://docs.microsoft.com/powershell/azure/get-started-azureps).

## <a name="deploy-the-wingtip-tickets-saas-application"></a>Nasazení aplikace SaaS aplikace Wingtip Tickets

#### <a name="plan-the-names"></a>Plánování názvy

V krocích v této části zadáte hodnotu uživatele, který se používá k Ujistěte se, že názvy prostředků jsou globálně jedinečný. Můžete také zadat název pro skupinu prostředků, která obsahuje všechny prostředky vytvořené v rámci nasazení aplikace. Fiktivní osoby s názvem Ann Finley doporučujeme:

- **Uživatel**: *af1* se skládá ze Ann Finley iniciály, a číslice. Pokud nasadíte aplikaci podruhé, použijte jinou hodnotu. Příkladem je af2.
- **Skupina prostředků**: *wingtip-dpt af1* určuje toto je aplikace databáze na tenanta. Připojte název af1 uživatele ke korelaci název skupiny prostředků s názvy prostředků, které obsahuje.

Teď zvolte názvy a zapište si. 

#### <a name="steps"></a>Kroky

1. Šablona nasazení databáze na tenanta SaaS aplikace Wingtip Tickets otevřít na webu Azure Portal, vyberte **nasadit do Azure**.

   <a href="https://aka.ms/deploywingtipdpt" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a>

1. Zadejte hodnoty požadovaných parametrů v šabloně.

    > [!IMPORTANT]
    > Některé ověřování a server brány firewall jsou pro demonstrační účely záměrně nezabezpečené. Doporučujeme vytvořit novou skupinu prostředků. Nepoužívejte stávající skupiny prostředků, servery ani fondy. Nepoužívejte tuto aplikaci, skripty nebo všechny nasazené prostředky pro produkční prostředí. Odstraňte tuto skupinu prostředků, až budete hotovi s aplikací k zastavení souvisejícího účtování.

    - **Skupina prostředků**: vyberte **vytvořit nový**a zadejte jedinečný název, který jste zvolili dříve pro skupinu prostředků. 
    - **Umístění**: z rozevíracího seznamu vyberte umístění.
    - **Uživatel**: použijte hodnotu uživatelského jména dříve.

1. Nasazení aplikace.

    a. Vyberte svůj souhlas s podmínkami a ujednáními.

    b. Vyberte **Koupit**.

1. Pokud chcete monitorovat stav nasazení, vyberte **oznámení** (ikona zvonku napravo od vyhledávacího pole). Nasazení aplikace SaaS aplikace Wingtip Tickets trvá přibližně 5 minut.

   ![Nasazení bylo úspěšné.](media/saas-dbpertenant-get-started-deploy/succeeded.png)

## <a name="download-and-unblock-the-wingtip-tickets-management-scripts"></a>Stáhněte si a odblokování Správa skriptů aplikace Wingtip Tickets

Při nasazení aplikace, stáhněte si zdrojový kód a správu skripty.

> [!IMPORTANT]
> Spustitelného obsahu (skriptů a knihovny DLL) mohou být blokovány Windows, když jsou stažené z externího zdroje a rozbalené soubory .zip. Postupujte podle kroků odblokujete soubor ZIP, před extrahovat skripty. Odblokování zajistí, že je povolené spouštět skripty.

1. Přejděte [úložiště GitHub WingtipTicketsSaaS DbPerTenant][github-wingtip-dpt].
1. Vyberte **Clone or download** (Naklonovat nebo stáhnout).
1. Vyberte **stáhnout ZIP**a pak soubor uložte.
1. Klikněte pravým tlačítkem myši **WingtipTicketsSaaS-DbPerTenant-master.zip** souboru a pak vyberte **vlastnosti**.
1. Na **Obecné** kartu, vyberte možnost **Odblokovat** > **použít**.
1. Vyberte **OK**a extrahování souborů

Skripty jsou umístěné v... \\WingtipTicketsSaaS-DbPerTenant-master\\složce Learning Modules.

## <a name="update-the-user-configuration-file-for-this-deployment"></a>Aktualizovat konfigurační soubor uživatele pro toto nasazení

Předtím, než spustíte všechny skripty, aktualizujte hodnoty prostředku skupiny a uživatele v souboru konfigurace uživatele. Nastavte tyto proměnné na hodnoty, které jste použili během nasazení.

1. V prostředí PowerShell ISE otevřete... \\Learning Modules\\**UserConfig.psm1** 
1. Aktualizace **ResourceGroupName** a **název** s konkrétní hodnoty pro vaše nasazení (na řádcích 10 a 11 pouze).
1. Uložte změny.

Tyto hodnoty jsou odkazovány v téměř každý skript.

## <a name="run-the-application"></a>Spuštění aplikace

Aplikace prezentuje místa, které jsou hostiteli události. Typy míst zahrnují koncertní sály, jazzové kluby a sportovní kluby. V Wingtip Tickets venues zaregistrovaní jako tenantů. Tenanta se poskytuje jako místo snadno vypisovat akce a prodávat lístky svým zákazníkům. Každé místo dostane individuální webu výpis svých akcí a prodej lístků.

Interně v aplikaci, každý tenant dostane databázi SQL nasazenou do elastického fondu SQL.

Centrální **Centrum akcí** stránka obsahuje seznam odkazů pro tenanty ve vašem nasazení.

1. Použijte adresu URL ve webovém prohlížeči otevřete Centrum událostí: http://events.wingtip-dpt.&lt; uživatel&gt;. trafficmanager.net. Náhradní &lt;uživatele&gt; s hodnotou uživatele vašeho nasazení.

    ![Centrum akcí](media/saas-dbpertenant-get-started-deploy/events-hub.png)

1. Vyberte **Fabrikam Jazz Club** v Centru událostí.

    ![Události](./media/saas-dbpertenant-get-started-deploy/fabrikam.png)

#### <a name="azure-traffic-manager"></a>Azure Traffic Manager

Aplikace Wingtip používá [ *Azure Traffic Manager* ](../traffic-manager/traffic-manager-overview.md) k řízení distribuce příchozích požadavků. Adresa URL pro přístup ke stránce události pro konkrétního tenanta používá následující formát:

- http://events.wingtip-dpt.&lt; Uživatel&gt;.trafficmanager.net/fabrikamjazzclub

    Části předchozí formátu jsou vysvětlené v následující tabulce.

    | Část adresy URL        | Popis       |
    | :-------------- | :---------------- |
    | http://events.wingtip-dpt | Události části aplikace Wingtip.<br /><br /> *-dpt* odlišuje *databáze na tenanta* provádění Wingtip Tickets oproti jiným implementacím. Mezi příklady patří *samostatné* aplikace na tenanta (*-sa*) nebo *víceklientské databáze* (*- mt*) implementace. |
    | .  *&lt;uživatele&gt;* | *af1* v příkladu. |
    | .trafficmanager.net/ | Traffic Manager, základní adresu URL. |
    | fabrikamjazzclub | Identifikuje klienta s názvem společnosti Fabrikam Jazz Club. |
    | &nbsp; | &nbsp; |

* Název tenanta je analyzována z adresy URL pro události aplikace.
* Název tenanta se používá k vytvoření klíče.
* Klíč se používá pro přístup ke katalogu k získání umístění databáze tenanta.
    - Katalog je implementovaný s využitím *správy mapování horizontálních oddílů*.
* Centra událostí pomocí rozšířených metadat v katalogu pro vytvoření adresy URL stránky události seznamu pro každého tenanta.

V produkčním prostředí, obvykle můžete vytvořit záznam CNAME DNS k [ *nasměrování internetové domény společnosti* ](../traffic-manager/traffic-manager-point-internet-domain.md) jako název DNS Traffic Manageru.

## <a name="start-generating-load-on-the-tenant-databases"></a>Spuštění generování zatížení databází tenantů

Teď, když je aplikace nasazena, pusťme to fungovat.

*Demo-LoadGenerator* Powershellový skript spustí úlohu, která běží na všech databázích tenantů. Skutečné zatížení velký počet aplikací SaaS je sporadické a nepředvídatelné. Simulace tohoto typu zatížení, vytváří generátor zatížení s náhodnými špičky nebo nárůstům aktivity na každého tenanta. Nárazové vyskytovat na náhodnými intervaly. Trvá několik minut, než se objeví vzoru zatížení. Nechte generátor spuštění pro alespoň tři nebo čtyři minut, než monitorování zatížení.

1. V prostředí PowerShell ISE, otevřete... \\Learning Modules\\nástroje\\*Demo-LoadGenerator.ps1* skriptu.
1. Stisknutím klávesy F5 spusťte skript a spustit generátor zatížení. Zatím nechejte nastavené výchozí hodnoty parametrů.
1. Přihlaste se ke svému účtu Azure a vyberte předplatné, které chcete použít, pokud je to nutné.

Skript generátor zatížení spouští úlohy na pozadí pro každou databázi v katalogu a poté se zastaví. Pokud znovu spustíte skript generátor zatížení, ukončí všechny úlohy na pozadí, které jsou spuštěny před spuštěním nové značky.

#### <a name="monitor-the-background-jobs"></a>Monitorování úloh na pozadí

Pokud chcete řídit a monitorovat úlohy na pozadí, použijte následující rutiny:

- `Get-Job`
- `Receive-Job`
- `Stop-Job`

#### <a name="demo-loadgeneratorps1-actions"></a>Ukázka LoadGenerator.ps1 akce

*Ukázka LoadGenerator.ps1* napodobuje aktivní úlohy transakcí odběratele. Následující kroky popisují posloupnost akcí, které *Demo-LoadGenerator.ps1* zahájí:

1. *Ukázka LoadGenerator.ps1* spustí *LoadGenerator.ps1* v popředí.

    - Oba soubory .ps1 jsou uložené ve složkách Learning Modules\\nástroje\\.

1. *LoadGenerator.ps1* cyklicky projde všech databázích tenantů v katalogu.

1. *LoadGenerator.ps1* spustí úlohu na pozadí prostředí PowerShell pro každou databázi tenantů:

    - Ve výchozím nastavení spustí úlohy na pozadí po dobu 120 minut.
    - Každá úloha způsobí, že zatížení založený na využití procesoru v databázi jednoho tenanta pomocí provádí *sp_CpuLoadGenerator*. Intenzita a doba trvání zatížení se liší v závislosti na `$DemoScenario`. 
    - *sp_CpuLoadGenerator* smyčky kolem příkazu SQL SELECT, který způsobuje vysoké zatížení CPU. Časový interval mezi problémy SELECT se liší podle hodnoty parametrů k vytvoření může ovládat zatížení procesoru. Pro simulaci zatížení realističtější se náhodně mění úrovně zatížení a intervaly.
    - Tento soubor .sql je uložen pod *WingtipTenantDB\\dbo\\StoredProcedures\\*.

1. Pokud `$OneTime = $false`, generátor zatížení spouští úlohy na pozadí a pak i nadále běžel. Každých 10 sekund, monitoruje pro všechny nové tenanty, které jsou zřízené. Pokud nastavíte `$OneTime = $true`, LoadGenerator spustí úlohy na pozadí a poté se zastaví spuštěná v popředí. Pro účely tohoto kurzu ponechte `$OneTime = $false`.

  Pokud chcete zastavit nebo restartovat generátor zatížení pomocí Ctrl-C nebo zastavení operace Ctrl-Break. 

  Pokud necháte generátor zatížení spuštěná v popředí, pro spuštění jiné skripty prostředí PowerShell použijte jiná instance prostředí PowerShell ISE.

&nbsp;

Než budete pokračovat v další části, nechte generátor zatížení s ve volání úlohy stavu.

## <a name="provision-a-new-tenant"></a>Zřízení nového tenanta

Počáteční nasazení vytvoří tři ukázkoví tenanti. Nyní můžete vytvořit jiného tenanta zobrazení dopadů na nasazené aplikace. V aplikaci Wingtip je podrobně pracovní postup pro zřízení nových tenantů [Provision and catalog kurzu](saas-dbpertenant-provision-and-catalog.md). V této fázi můžete vytvořit nového tenanta, který využívá méně než jedna minuta.

1. Otevřete nový ISE Powershellu.
1. Otevřete... \\Learning Modules\Provision and Catalog\\*Demo-ProvisionAndCatalog.ps1*.
1. Stisknutím klávesy F5 spusťte skript. Zatím nechejte nastavené výchozí hodnoty.

   > [!NOTE]
   > Použít mnoho skriptů SaaS aplikace Wingtip *$PSScriptRoot* procházet složky pro volání funkce v jiné skripty. Tato proměnná je vyhodnocen pouze v případě, že je úplná skript spustit stisknutím klávesy F5. Zvýraznění a spuštění výběru s F8 může vést k chybám. Chcete-li spustit skripty, stiskněte klávesu F5.

Nová databáze tenantů jsou:

- Vytvoří v elastickém fondu SQL.
- Inicializovat.
- Zaregistruje do katalogu.

Po úspěšném zřízení *události* ve vašem prohlížeči zobrazí web nového tenanta.

![Nový tenant](./media/saas-dbpertenant-get-started-deploy/red-maple-racing.png)

Aktualizujte Centrum akcí, chcete-li vytvořit nového tenanta se zobrazí v seznamu.

## <a name="explore-the-servers-pools-and-tenant-databases"></a>Zkoumání serverů, fondů a databází tenantů

Teď, když jste jste spustili zátěž kolekce tenantů, podíváme se na některé z nasazených prostředků.

1. V [webu Azure portal](http://portal.azure.com), přejděte na seznam serverů SQL Server. Otevřete **katalogu-dpt -&lt;uživatele&gt;**  serveru.
    - Server katalogu obsahuje dvě databáze, **tenantcatalog** a **basetenantdb** (Šablona databázi, která se kopíruje při vytváření nových tenantů).

   ![Databáze](./media/saas-dbpertenant-get-started-deploy/databases.png)

1. Vraťte se do seznamu serverů SQL.

1. Otevřít **tenants1-dpt -&lt;uživatele&gt;**  serveru, který obsahuje databáze tenantů.

1. Zobrazit následující položky:

    - Všechny databáze tenantů jsou **elastický Standard** databáze ve fondu standard 50 eDTU.
    - Red Maple Racing databáze je databáze tenanta, kterou jste zřídili dříve.

   ![Server s databázemi](./media/saas-dbpertenant-get-started-deploy/server.png)

## <a name="monitor-the-pool"></a>Monitorování fondu

Po *LoadGenerator.ps1* běhy pro několik minut, dostatek data by měla být k dispozici, abyste mohli začít sledovat některé možnosti monitorování. Tyto možnosti jsou integrované do fondů a databází.

Přejděte na server **tenants1-dpt -&lt;uživatele&gt;** a vyberte **Pool1** zobrazení využití prostředků fondu. V následující grafy generátor zatížení běžel na jednu hodinu.

   ![Monitorování fondu](./media/saas-dbpertenant-get-started-deploy/monitor-pool.png)

- První graf s popiskem **využití prostředků**, zobrazuje fond využití eDTU.
- Druhý graf ukazuje využití eDTU pět Nejaktivnější databází ve fondu.

Dva grafy znázorňují, elastické fondy a databáze SQL se skvěle hodí pro nepředvídatelnými úlohami aplikace SaaS. Grafy ukazují, že jsou čtyři databáze, každý s nárazovým zatížením až 40 Edtu a všechny databáze jsou pohodlně podporován 50 eDTU fondu. 50 eDTU fondu může podporovat těžší úlohy. Pokud databáze jsou zřízené jako izolované databáze, každý z nich musí mít úroveň S2 (50 DTU) pro podporu nárazové. Náklady na čtyřech samostatných databází S2 jsou téměř třikrát cena fondu. V reálných situacích zákazníci SQL Database provozovat až 500 databází ve fondech s 200 eDTU. Další informace najdete v tématu [kurz o monitorování výkonu](saas-dbpertenant-performance-monitoring.md).

## <a name="additional-resources"></a>Další zdroje informací:

- Další informace najdete v části Další [kurzů v aplikaci SaaS aplikace Wingtip Tickets databáze na tenanta](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials).
- Další informace o elastických fondech najdete v tématu [co je elastický fond Azure SQL?](sql-database-elastic-pool.md).
- Další informace o elastických úlohách najdete v tématu [spravovat horizontálním navýšením kapacity databáze](sql-database-elastic-jobs-overview.md).
- Další informace o víceklientské aplikace SaaS, naleznete v tématu [vzory návrhu pro víceklientské aplikace SaaS](saas-tenancy-app-design-patterns.md).


## <a name="next-steps"></a>Další postup

V tomto kurzu jste se dozvěděli:

> [!div class="checklist"]
> - Jak nasadit aplikaci SaaS aplikace Wingtip Tickets.
> - O serverů, fondů a databází, které tvoří aplikaci.
> - Jak se tenanti jsou namapovaní na svá data pomocí *katalogu*.
> - Jak zřídit nové tenanty.
> - Jak zobrazit využití fondu k monitorování aktivity tenanta.
> - Jak odstranit ukázkové prostředky k zastavení souvisejícího účtování.

Zkuste [Provision and catalog kurzu](saas-dbpertenant-provision-and-catalog.md).



<!-- Link references. -->

[github-wingtip-dpt]: https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant 

