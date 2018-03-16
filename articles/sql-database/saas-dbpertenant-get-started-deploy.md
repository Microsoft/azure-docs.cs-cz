---
title: "Databáze za klienta SaaS kurz – Azure SQL Database | Microsoft Docs"
description: "Nasazení a prozkoumejte aplikace víceklientské Wingtip lístky SaaS, která demonstruje databáze za klienta a ostatní SaaS vzorce používání Azure SQL Database."
keywords: kurz k sql database
services: sql-database
author: MightyPen
manager: craigg
ms.service: sql-database
ms.custom: scale out apps
ms.topic: article
ms.date: 12/07/2017
ms.author: genemi
ms.openlocfilehash: 8aa4c8691093779bd8febee80fca88efad93dc00
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2018
---
# <a name="deploy-and-explore-a-multi-tenant-saas-application-that-uses-the-database-per-tenant-pattern-with-azure-sql-database"></a>Nasazení a prozkoumejte aplikace SaaS více klientů, která používá databázi za vzor klienta s Azure SQL Database

V tomto kurzu nasadíte a prozkoumejte SaaS lístky Wingtip *databáze za klienta* aplikace (Wingtip). Aplikace používá databáze za klienta vzor pro ukládání dat více klientů. Tato aplikace je určená k prezentují funkce databáze SQL Azure, které zjednodušují povolení SaaS scénáře.

Pět minut, po kliknutí na tlačítko modré tlačítko označené **nasadit do Azure**, je k dispozici víceklientské aplikace SaaS. Aplikace zahrnuje Azure SQL Database běží v cloudu Microsoft Azure. Aplikace se nasazuje s tři ukázkové klientů, každý s svou vlastní databázi. Všechny databáze jsou nasazeny do SQL *elastický fond*. Aplikace se nasadí do vašeho předplatného Azure. Máte úplný přístup k prozkoumávání a pracovat s jednotlivé součásti aplikace. Jsou k dispozici v C# zdrojovému kódu aplikace a skripty správy [úložiště GitHub WingtipTicketsSaaS DbPerTenant][github-wingtip-dpt].

V tomto kurzu se dozvíte:

> [!div class="checklist"]
> - Postup nasazení aplikace Wingtip SaaS.
> - Kde lze získat zdrojovému kódu aplikace a skripty pro správu.
> - O serverech, fondy a databází, které tvoří aplikace.
> - Jak klienti jsou mapované na svoje data pomocí *katalogu*.
> - Jak zřídit nového klienta.
> - Postup sledování klientů aktivity v aplikaci.

A [série kurzů související](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials) nabízí a prozkoumejte různé způsoby SaaS návrhu a správu. Kurzů k sestavení nad rámec tohoto počáteční nasazení.
Při použití kurzů k uvidíte, jak jsou implementované různé vzorce SaaS tak, že prověří zadaný skripty.
Skripty ukazují, jak funkcí služby SQL Database zjednodušují vývoj aplikací SaaS.

## <a name="prerequisites"></a>Požadavky

Předpokladem dokončení tohoto kurzu je splnění následujících požadavků:

* Je nainstalované prostředí Azure PowerShell. Podrobnosti najdete v tématu [Začínáme s Azure Powershellem](https://docs.microsoft.com/powershell/azure/get-started-azureps).

## <a name="deploy-the-wingtip-tickets-saas-application"></a>Nasazení aplikace SaaS Wingtip lístky

#### <a name="plan-the-names"></a>Plánování názvy

Kroky v této části, poskytnete *uživatele* hodnotu, která se používá k zajištění jsou globálně jedinečné názvy prostředků a název *skupiny prostředků* obsahující všechny prostředky vytvořeny při nasazení aplikace. Pro osoby s názvem *Ann Finley*, doporučujeme:
- *Uživatel:* **af1***(jeho iniciály a číslice. Použít jinou hodnotu (např. af2) Pokud nasazujete aplikaci ještě jednou.)*
- *Skupina prostředků:* **wingtip. dpt af1** *(wingtip dpt signalizuje to databáze za klienta aplikace. Připojování af1 název uživatele koreluje s názvy prostředků, které obsahuje název skupiny prostředků.)*

Teď zvolte názvy a poznamenejte si je. 

#### <a name="steps"></a>Kroky

1. Otevřít databázi SaaS lístky Wingtip za šablonu nasazení klienta na portálu Azure, kliknutím blue **nasadit do Azure** tlačítko.

   <a href="https://aka.ms/deploywingtipdpt" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a>

1. Do šablony zadejte hodnoty pro požadované parametry:

    > [!IMPORTANT]
    > Některá ověřování a brány firewall serveru jsou pro demonstrační účely záměrně nezabezpečené. Doporučujeme vám *vytvořit novou skupinu prostředků*. Nepoužívejte existující skupiny prostředků, serverům nebo fondů. Nepoužívejte této aplikace, skripty nebo všechny nasazené prostředky pro produkční prostředí. Po ukončení práce s aplikací tuto skupinu prostředků odstraňte, aby se zastavilo související účtování.

    - **Skupina prostředků** – vyberte **vytvořit nový** a poskytují jedinečný **název** dříve jste zvolili pro skupinu prostředků. 
    - **Umístění** – vyberte **umístění** z rozevíracího seznamu.
    - **Uživatel** -použít hodnotu uživatelského jména jste zvolili dříve.

1. Nasazení aplikace.

    - Kliknutím na tlačítko přijmout podmínky a ujednání.
    - Klikněte na **Koupit**.

1. Monitorování stavu nasazení kliknutím **oznámení**, který je na ikonu zvonku napravo od pole hledání. Nasazení aplikace SaaS lístky Wingtip trvá přibližně pět minut.

   ![nasazení bylo úspěšné](media/saas-dbpertenant-get-started-deploy/succeeded.png)

## <a name="download-and-unblock-the-wingtip-tickets-management-scripts"></a>Stáhněte si a odblokování skripty správy Wingtip lístky

Zatímco je nasazení aplikace, stáhněte si skripty zdrojový kód a správu.

> [!IMPORTANT]
> Spustitelný soubor obsah (skripty, knihovny DLL) mohou být blokovány Windows, když jsou stažené z externího zdroje a rozbalené soubory .zip. Při extrahování skripty ze souboru zip, použijte následující postup odblokování před extrahování souboru ZIP. Odblokování zajistí, že je povoleno spustit skripty.

1. Vyhledejte [úložiště GitHub WingtipTicketsSaaS DbPerTenant][github-wingtip-dpt].
2. Klikněte na tlačítko **klonovat nebo stáhnout**.
3. Klikněte na tlačítko **stáhnout ZIP**a pak soubor uložte.
4. Klikněte pravým tlačítkem myši **WingtipTicketsSaaS-DbPerTenant-master.zip** souboru a potom vyberte **vlastnosti**.
5. Na **Obecné** vyberte **Odblokovat**a potom klikněte na **použít**.
6. Klikněte na **OK**.
7. Extrahujte soubory.

Skripty, které jsou umístěné v *... \\WingtipTicketsSaaS-DbPerTenant-master\\Learning moduly* složky.

## <a name="update-the-user-configuration-file-for-this-deployment"></a>Aktualizovat konfigurační soubor uživatele pro toto nasazení

Před spuštěním všech skriptů, aktualizovat *skupiny prostředků* a *uživatele* hodnoty ve *konfiguraci uživatele* souboru. Nastavte tyto proměnné na hodnoty, které jste použili při nasazení.

1. V **prostředí PowerShell ISE**, otevřete... \\Učení moduly\\**UserConfig.psm1** 
2. Aktualizace **ResourceGroupName** a **název** s konkrétními hodnotami pro vaše nasazení (na řádky 10 a 11 pouze).
3. Uložte změny!

Tyto hodnoty se odkazuje v téměř každý skript.

## <a name="run-the-application"></a>Spuštění aplikace

Aplikace umožňující prezentovat místa, které jsou hostiteli události. Typy místo obsahují vzájemné součinnosti místnosti, jazz kluby a sportu kluby. V Wingtip lístky místa je zaregistrovaný jako klienty. Probíhá klienta dává místo snadný způsob seznam událostí a prodej lístků zákazníkům. Každý místo získá přizpůsobené webu seznamu svoje události a prodej lístků.

Interně v aplikaci, každý klient získá databázi SQL, který je nasazený do fondu elastické databáze SQL.

Centrálního **události rozbočovače** stránka obsahuje seznam odkazů klientům ve vašem nasazení.

1. Otevřete *události rozbočovače* ve webovém prohlížeči: http://events.wingtip-dpt.&lt;uživatele&gt;. trafficmanager.net (Nahraďte &lt;uživatele&gt; s hodnotou uživatele vašeho nasazení):

    ![centrum akcí](media/saas-dbpertenant-get-started-deploy/events-hub.png)

2. V *Centru akcí* klikněte na **Fabrikam Jazz Club**.

    ![Události](./media/saas-dbpertenant-get-started-deploy/fabrikam.png)

#### <a name="azure-traffic-manager"></a>Azure Traffic Manager

Adresář Wingtip aplikace používá [ *Azure Traffic Manager* ](../traffic-manager/traffic-manager-overview.md) k řízení distribuce příchozí požadavky. Adresa URL pro přístup k stránky události pro konkrétní klienta používá následující formát:

- http://events.wingtip-dpt.&lt;user&gt;.trafficmanager.net/fabrikamjazzclub

V následující tabulce jsou vysvětleny části předchozí formátu.

| Část adresy URL        | Popis       |
| :-------------- | :---------------- |
| http://events.wingtip-dpt | Části události Wingtip aplikace.<br /><br /> *-dpt* odlišuje *databáze za klienta* implementace Wingtip lístků oproti jiným implementacím. Například *samostatné* aplikace na klienta (*-sa*), nebo *víceklientské databáze* (*- mt*) implementace. |
| .*&lt;user&gt;* | *af1* v našem příkladu. |
| .trafficmanager.net/ | Azure Traffic Manager základní adresu URL. |
| fabrikamjazzclub | Identifikuje klienta s názvem *křížovou Fabrikam Jazz kartou*. |
| &nbsp; | &nbsp; |

1. Název klienta je analyzována z adresy URL aplikací události.
2. Název klienta slouží k vytvoření klíče.
3. Používá se pro přístup ke katalogu k získání umístění databáze klienta.
    - Katalog je implementovaná pomocí *horizontálního oddílu mapy správu*.
4. *Události rozbočovače* používá rozšířené metadata v katalogu, můžete vytvořit seznam adres URL stránky události pro každého klienta.

V produkčním prostředí, obvykle můžete vytvořit záznam CNAME DNS do [ *nasměrování internetové domény společnosti* ](../traffic-manager/traffic-manager-point-internet-domain.md) jako název DNS správce provozu.

## <a name="start-generating-load-on-the-tenant-databases"></a>Spuštění generování zatížení databází tenantů

Teď, když je aplikace nasazena, přidejme ho na spolupráci!
*Ukázku LoadGenerator* zatížení spuštěným pro všechny databáze klienta spustí skript prostředí PowerShell.
Skutečné zatížení velký počet aplikací SaaS je výskyt občasný a nepředvídatelným.
Pro simulaci tento typ zatížení, vytvoří generátor zatížení s náhodnou špičky nebo shluky aktivit na každého klienta.
Shluky docházet v náhodnou intervalech.
Trvá několik minut pro vzor zatížení objeví. Proto je nejlepší umožníte generátor spustit alespoň tři nebo čtyři minut před monitorování zatížení.

1. V *prostředí PowerShell ISE*, otevřete... \\Learning moduly\\nástroje\\*ukázku LoadGenerator.ps1* skriptu.
1. Stiskněte klávesu **F5** a spusťte skript spustit generátor zatížení. (Ponechte výchozí hodnoty parametrů pro teď.)
1. Zobrazí se výzva k přihlášení k účtu Azure a v případě potřeby vyberte předplatné chcete použít.

Zatížení generátor skript spustí úlohu na pozadí pro každou databázi v katalogu a poté se zastaví.  Pokud skript generátor zatížení, bude nejprve zastavit úlohy na pozadí, které jsou spuštěny před zahájením nové.

#### <a name="monitor-the-background-jobs"></a>Sledování úloh na pozadí

Pokud chcete řídit a monitorovat úlohy na pozadí, můžete použít následující rutiny:

- `Get-Job`
- `Receive-Job`
- `Stop-Job`

#### <a name="demo-loadgeneratorps1-actions"></a>Demo-LoadGenerator.ps1 akce

*Demo-LoadGenerator.ps1* napodobuje aktivní úlohu transakcí odběratele. Následující kroky popisují posloupnost akcí, *ukázku LoadGenerator.ps1* zahájí:

1. *Demo-LoadGenerator.ps1* spustí *LoadGenerator.ps1* v popředí.
    - Oba tyto soubory .ps1 jsou uložené ve složkách *Learning moduly\\nástroje\\*.

1. *LoadGenerator.ps1* smyčky prostřednictvím všechny databáze klienta v katalogu.

1. *LoadGenerator.ps1* spustí úlohu na pozadí prostředí PowerShell pro každou databázi klienta: 
    - Ve výchozím nastavení úlohy na pozadí běží po dobu 120 minut.
    - Každá úloha způsobí, že zatížení procesoru založené na jednoho klienta databáze spuštěním *sp_CpuLoadGenerator*.  Intenzita a dobu trvání zatížení se liší v závislosti na `$DemoScenario`. 
    - *sp_CpuLoadGenerator* smyčky kolem příkazu SQL SELECT, který způsobuje vysoké zatížení procesoru. Časový interval mezi problémy SELECT se liší podle hodnoty parametrů k vytvoření ovladatelné zatížení procesoru. Úrovní zátěže a intervaly se náhodně mění k simulaci realističtější zatížením.
    - Tento soubor .sql je uložen pod *WingtipTenantDB\\dbo\\StoredProcedures\\*.

1. Pokud `$OneTime = $false`, generátor zatížení spustí úlohy na pozadí a potom pracuje, monitorování každých 10 sekund pro žádné nové klienty, které jsou zřízené. Pokud nastavíte `$OneTime = $true`, bude LoadGenerator spuštění úlohy na pozadí a poté jej zastavit spuštěná v popředí. V tomto kurzu ponechte `$OneTime = $false`.

  Pokud chcete zastavit nebo restartovat generátor zatížení pomocí Ctrl-C nebo zastavit operaci Ctrl-Break. 

  Pokud necháte generátor zatížení spuštěná v popředí, použijte jiná instance prostředí PowerShell ISE ke spouštění dalších skriptů prostředí PowerShell.

&nbsp;

Před pokračováním v další části, ponechte generátor zatížení spuštěna ve stavu vyvolání úlohy.

## <a name="provision-a-new-tenant"></a>Zřízení nového tenanta

Počáteční nasazení vytvoří tři ukázkové klientů. Teď vytvoříte jiného klienta a zobrazte dopad na nasazené aplikace. V aplikaci Wingtip je podrobně pracovního postupu zřídit nové klienty [kurzu zřizování a katalog](saas-dbpertenant-provision-and-catalog.md). V této fázi můžete vytvořit nového klienta, který přebírá méně než jedna minuta.

1. Otevřete nový *prostředí PowerShell ISE*.
1. Otevřete... \\Učení Modules\Provision a katalog\\*ukázku ProvisionAndCatalog.ps1* .
2. Stisknutím klávesy **F5** spusťte skript. (Ponechte výchozí hodnoty pro nyní).

   > [!NOTE]
   > Použít mnoho skriptů Wingtip SaaS *$PSScriptRoot* k procházení složek volat funkce v jiné skripty. Tato proměnná je Vyhodnocená jenom při úplné skript se spustí, stisknutím klávesy **F5**.  Zvýraznění a spuštění výběr **F8** může vést k chybám. Proto spouštět skripty stisknutím **F5**.

Novou databázi klienta je:

- Vytvořit ve fondu elastické databáze SQL.
- Inicializovat.
- Zaregistrovány v katalogu.

Po úspěšném zřízení *události* server k novému klientovi se zobrazí v prohlížeči:

![Nový tenant](./media/saas-dbpertenant-get-started-deploy/red-maple-racing.png)

Obnovit *události rozbočovače* aby nového klienta, které jsou uvedeny v tomto seznamu.

## <a name="explore-the-servers-pools-and-tenant-databases"></a>Zkoumání serverů, fondů a databází tenantů

Teď, když jste spuštění, spuštění zátěžového proti kolekci klientů, podíváme se na některé prostředky, které byly nasazeny:

1. V [portál Azure](http://portal.azure.com), přejděte do seznamu serverů SQL Server a pak otevřete **katalogu-dpt -&lt;uživatele&gt;**  serveru.
    - Na server katalogu obsahuje dvě databáze, **tenantcatalog** a **basetenantdb** (šablony databáze, která se zkopírují k vytvoření nové klienty).

   ![databáze](./media/saas-dbpertenant-get-started-deploy/databases.png)

2. Vraťte se zpátky do seznamu serverů SQL.

3. Otevřete **tenants1-dpt -&lt;uživatele&gt;**  serveru, který obsahuje databáze klienta.

4. Viz následující položky:
    - Každá databáze klienta je *elastické standardní* databáze ve fondu standardní 50 eDTU.
    - *Red javor Racing* databáze, kterou jste zřídili dříve databáze klienta.

   ![server](./media/saas-dbpertenant-get-started-deploy/server.png)

## <a name="monitor-the-pool"></a>Monitorování fondu

Po *LoadGenerator.ps1* spuštění pro několik minut, dostatek data by měla být k dispozici pro zahájení vyhledávání v některé možnosti monitorování. Tyto možnosti jsou integrovány do fondů a databází.

Přejděte na server **tenants1-dpt -&lt;uživatele&gt;**a klikněte na tlačítko **Pool1** zobrazení využití prostředků pro fond. V následující grafy generátor zatížení spustili jednu hodinu.

   ![monitorování fondu](./media/saas-dbpertenant-get-started-deploy/monitor-pool.png)

- První graf, s názvem bez přípony **využití prostředků**, zobrazuje fond využití eDTU.
- Druhý graf zobrazuje využití eDTU pět Nejaktivnější databází ve fondu.

Dvou grafech znázorňují elastické fondy a databáze SQL se skvěle hodí pro úlohy nepředvídatelným aplikací SaaS.
Grafy ukazují, že jsou 4 databáze každý bursting až o 40 Edtu a ještě jsou všechny databáze pohodlně podporován 50 eDTU fondu. 50 eDTU fondu může podporovat i větší zatížení.
Pokud byly zřízení jako samostatné databáze by jednotlivé databáze musí být S2 (50 DTU) pro podporu shluky.
Náklady na 4 samostatné S2 databází by téměř 3krát cena fondu.
V situacích, reálného databáze SQL zákazníci spustit až 500 databází ve fondech 200 eDTU.
Další informace najdete v [kurzu monitorování výkonu](saas-dbpertenant-performance-monitoring.md).

## <a name="additional-resources"></a>Další zdroje informací:

- Další [návodů, které vychází z databáze SaaS lístky Wingtip za klienta aplikace](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
- Další informace o elastických fondech najdete v tématu [*Co je elastický fond Azure*](sql-database-elastic-pool.md).
- Další informace o elastických úlohách najdete v tématu [*Správa cloudových databází s horizontálním navýšením kapacity*](sql-database-elastic-jobs-overview.md).
- Další informace o SaaS aplikacích s více tenanty najdete v tématu [*Vzory návrhu pro SaaS aplikace s více tenanty*](saas-tenancy-app-design-patterns.md).


## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se dozvěděli:

> [!div class="checklist"]
> - Postup nasazení aplikace SaaS Wingtip lístky
> - O serverech, fondech a databázích, které aplikaci tvoří
> - Tenanti jsou namapovaní na svá data pomocí *katalogu*
> - Jak zřídit nové tenanty
> - Jak zobrazit využití fondu k monitorování aktivity tenanta
> - Jak odstranit ukázkové prostředky k zastavení souvisejícího účtování

Zkuste [kurzu zřizování a katalog](saas-dbpertenant-provision-and-catalog.md).



<!-- Link references. -->

[github-wingtip-dpt]: https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant 

