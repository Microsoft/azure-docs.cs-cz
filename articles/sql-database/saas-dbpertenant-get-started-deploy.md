---
title: Databáze za klienta SaaS kurz – Azure SQL Database | Microsoft Docs
description: Nasazení a prozkoumejte Wingtip lístky SaaS víceklientské aplikaci, která demonstruje vzoru databáze za klienta a dalšími SaaS vzory pomocí Azure SQL Database.
keywords: kurz k sql database
services: sql-database
author: MightyPen
manager: craigg
ms.service: sql-database
ms.custom: scale out apps
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: genemi
ms.openlocfilehash: 95d4fc1886e16785b6de8f3a395b218b66d193ff
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34645357"
---
# <a name="deploy-and-explore-a-multitenant-saas-app-that-uses-the-database-per-tenant-pattern-with-sql-database"></a>Nasazení a prozkoumejte víceklientské aplikace SaaS, která využívá vzor databáze na klienta s databází SQL

V tomto kurzu nasazení a prozkoumejte databáze za klienta aplikace SaaS lístky Wingtip (Wingtip). Aplikace používá k ukládání dat více klientů vzor databáze za klienta. Tato aplikace je určená k prezentují funkce databáze SQL Azure, které zjednodušují postup povolení SaaS scénáře.

Pět minut po výběru **nasadit do Azure**, máte víceklientské aplikace SaaS. Aplikace zahrnuje SQL database, která běží v cloudu. Aplikace se nasazuje s tři ukázkové klientů, každý s svou vlastní databázi. Všechny databáze jsou nasazeny do elastický fond SQL. Aplikace se nasadí do vašeho předplatného Azure. Máte úplný přístup k prozkoumávání a pracovat s jednotlivé součásti aplikace. Jsou k dispozici v C# zdrojovému kódu aplikace a skripty správy [úložiště GitHub WingtipTicketsSaaS DbPerTenant][github-wingtip-dpt].

V tomto kurzu se dozvíte:

> [!div class="checklist"]
> - Postup nasazení aplikace Wingtip SaaS.
> - Kde lze získat zdroj aplikace kód a správu skripty.
> - O serverech, fondy a databází, které tvoří aplikace.
> - Jak klienti jsou mapované na svoje data pomocí *katalogu*.
> - Jak zřídit nového klienta.
> - Postup sledování klientů aktivity v aplikaci.

A [série kurzů související](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials) nabízí a prozkoumejte různé způsoby SaaS návrhu a správu. Kurzů k sestavení nad rámec tohoto počáteční nasazení. Pokud použijete kurzy, můžete zkontrolovat zadané skripty, které chcete zobrazit, jak jsou implementované různé vzorce SaaS. Skripty ukazují, jak funkcí služby SQL Database zjednodušují vývoj aplikací SaaS.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto kurzu, ujistěte se, že je nainstalovaný Azure PowerShell. Další informace najdete v tématu [Začínáme s Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

## <a name="deploy-the-wingtip-tickets-saas-application"></a>Nasazení aplikace SaaS Wingtip lístky

#### <a name="plan-the-names"></a>Plánování názvy

Kroky v této části zadejte hodnotu uživatele, který se používá k zajištění, že názvy prostředků jsou globálně jedinečný. Je také zadat název pro skupinu prostředků, který obsahuje všechny prostředky vytvořeny při nasazení aplikace. Pro fiktivní osobu s názvem Ann Finley doporučujeme:

- **Uživatel**: *af1* je tvořena Ann Finley iniciály a číslice. Pokud nasazujete aplikaci ještě jednou, použijte jinou hodnotu. Příkladem je af2.
- **Skupina prostředků**: *wingtip. dpt af1* určuje toto je aplikace databáze za klienta. Připojte af1 název uživatele ke korelaci název skupiny prostředků s názvy prostředků, které obsahuje.

Teď zvolte názvy a poznamenejte si je. 

#### <a name="steps"></a>Kroky

1. Otevřete šablonu nasazení databáze za klienta SaaS Wingtip lístků na portálu Azure, vyberte **nasadit do Azure**.

   <a href="https://aka.ms/deploywingtipdpt" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a>

2. Zadejte hodnoty pro požadované parametry v šabloně.

    > [!IMPORTANT]
    > Některé ověřování a server brány firewall jsou záměrně zabezpečená pro demonstrační účely. Doporučujeme vám, že vytvoříte novou skupinu prostředků. Nepoužívejte existující skupiny prostředků, serverům nebo fondů. Pro produkční prostředí používat tuto aplikaci, skripty nebo všechny nasazené prostředky. Tato skupina prostředků odstraňte, když jste hotovi s aplikací, aby zastavit související fakturace.

    - **Skupina prostředků**: vyberte **vytvořit nový**a zadejte jedinečný název, který jste vybrali dříve pro skupinu prostředků. 
    - **Umístění**: v rozevíracím seznamu vyberte umístění.
    - **Uživatel**: použijte hodnotu uživatelského jména jste zvolili dříve.

3. Nasazení aplikace.

    a. Vyberte, chcete-li s podmínkami a ujednáními.

    b. Vyberte **nákupu**.

4. Pokud chcete monitorovat stav nasazení, vyberte **oznámení** (ikonu zvonku napravo od pole hledání). Nasazení aplikace SaaS lístky Wingtip trvá přibližně pět minut.

   ![Nasazení bylo úspěšné.](media/saas-dbpertenant-get-started-deploy/succeeded.png)

## <a name="download-and-unblock-the-wingtip-tickets-management-scripts"></a>Stáhněte si a odblokování skripty správy Wingtip lístky

Při aplikace nasadí, stáhněte si skripty zdrojový kód a správu.

> [!IMPORTANT]
> Spustitelný soubor obsah (skripty a knihovny DLL) mohou být blokovány Windows, když jsou stažené z externího zdroje a rozbalené soubory .zip. Postup odblokování souboru ZIP, před extrahovat skripty. Odblokování zajistí, že je povoleno spustit skripty.

1. Vyhledejte [úložiště GitHub WingtipTicketsSaaS DbPerTenant][github-wingtip-dpt].
2. Vyberte **klonovat nebo stáhnout**.
3. Vyberte **stáhnout ZIP**a pak soubor uložte.
4. Klikněte pravým tlačítkem myši **WingtipTicketsSaaS-DbPerTenant-master.zip** souboru a potom vyberte **vlastnosti**.
5. Na **Obecné** vyberte **Odblokovat** > **použít**.
6. Vyberte **OK**a extrahování souborů

Skripty, které jsou umístěné v... \\WingtipTicketsSaaS-DbPerTenant-master\\Learning moduly složky.

## <a name="update-the-user-configuration-file-for-this-deployment"></a>Aktualizovat konfigurační soubor uživatele pro toto nasazení

Než spustíte všechny skripty, aktualizujte hodnoty prostředku skupiny a uživatele v souboru konfigurace uživatele. Nastavte tyto proměnné na hodnoty, které jste použili při nasazení.

1. V prostředí PowerShell ISE otevřete... \\Učení moduly\\**UserConfig.psm1** 
2. Aktualizace **ResourceGroupName** a **název** s konkrétními hodnotami pro vaše nasazení (na řádky 10 a 11 pouze).
3. Uložte změny.

Tyto hodnoty se odkazuje v téměř každý skript.

## <a name="run-the-application"></a>Spuštění aplikace

Aplikace umožňující prezentovat místa, které jsou hostiteli události. Typy místo obsahují vzájemné součinnosti místnosti, jazz kluby a sportu kluby. V Wingtip lístky místa je zaregistrovaný jako klienty. Probíhá klienta dává místo snadný způsob seznam událostí a prodej lístků zákazníkům. Každý místo získá přizpůsobený web seznamu svoje události a prodej lístků.

Interně v aplikaci, každý klient získá databázi SQL, který je nasazený do elastický fond SQL.

Centrálního **události rozbočovače** stránka obsahuje seznam odkazů klientům ve vašem nasazení.

1. Použijte adresu URL v prohlížeči otevřete Centrum událostí: http://events.wingtip-dpt.&lt; uživatele&gt;. trafficmanager.net. SUBSTITUTE &lt;uživatele&gt; s hodnotou uživatele vaše nasazení.

    ![Centra událostí](media/saas-dbpertenant-get-started-deploy/events-hub.png)

2. Vyberte **křížovou kartou Fabrikam Jazz** v Centru událostí.

    ![Události](./media/saas-dbpertenant-get-started-deploy/fabrikam.png)

#### <a name="azure-traffic-manager"></a>Azure Traffic Manager

Adresář Wingtip aplikace používá [ *Azure Traffic Manager* ](../traffic-manager/traffic-manager-overview.md) k řízení distribuce příchozí požadavky. Adresa URL pro přístup k stránky události pro konkrétní klienta používá následující formát:

- http://events.wingtip-dpt.&lt; uživatele&gt;.trafficmanager.net/fabrikamjazzclub

    V následující tabulce jsou vysvětleny části předchozí formátu.

    | Část adresy URL        | Popis       |
    | :-------------- | :---------------- |
    | http://events.wingtip-dpt | Části události Wingtip aplikace.<br /><br /> *-dpt* odlišuje *databáze za klienta* implementace Wingtip lístků oproti jiným implementacím. Příklady *samostatné* aplikace za klienta (*-sa*) nebo *víceklientské databáze* (*- mt*) implementace. |
    | .  *&lt;uživatele&gt;* | *af1* v příkladu. |
    | .trafficmanager.net/ | Správce provozu základní adresu URL. |
    | fabrikamjazzclub | Identifikuje klienta s názvem křížovou kartou Jazz Fabrikam. |
    | &nbsp; | &nbsp; |

* Název klienta je analyzována z adresy URL aplikací události.
* Název klienta slouží k vytvoření klíče.
* Používá se pro přístup ke katalogu k získání umístění databáze klienta.
    - Katalog je implementována pomocí *horizontálního oddílu mapy správu*.
* Centra událostí použije k vytvoření adresy URL stránky seznamu události pro každého klienta rozšířené metadata v katalogu.

V produkčním prostředí, obvykle můžete vytvořit záznam CNAME DNS do [ *nasměrování internetové domény společnosti* ](../traffic-manager/traffic-manager-point-internet-domain.md) jako název DNS Traffic Manager.

## <a name="start-generating-load-on-the-tenant-databases"></a>Spuštění generování zatížení databází tenantů

Teď, když je aplikace nasazena, přidejme ho na spolupráci.

*Ukázku LoadGenerator* pracovních úloh, které spouští všechny databáze klienta spustí skript prostředí PowerShell. Skutečné zatížení velký počet aplikací SaaS je výskyt občasný a nepředvídatelným. Pro simulaci tento typ zatížení, vytvoří generátor zatížení s náhodnou špičky nebo shluky aktivit na každého klienta. Shluky docházet v náhodnou intervalech. Trvá několik minut pro vzor zatížení objeví. Umožní generátor spustit pro alespoň tři nebo čtyři minut, než monitorování zatížení.

1. V prostředí PowerShell ISE, otevřete... \\Learning moduly\\nástroje\\*ukázku LoadGenerator.ps1* skriptu.
2. Stisknutím klávesy F5 spusťte skript a spusťte generátor zatížení. Ponechte výchozí hodnoty parametrů pro nyní.
3. Přihlaste se k účtu Azure a vyberte odběr, který chcete použít, v případě potřeby.

Zatížení generátor skript spustí úlohu na pozadí pro každou databázi v katalogu a poté se zastaví. Pokud skript generátor zatížení, zastaví úlohy na pozadí, které jsou spuštěny před spuštěním nové.

#### <a name="monitor-the-background-jobs"></a>Sledování úloh na pozadí

Pokud chcete řídit a monitorovat úlohy na pozadí, použijte následující rutiny:

- `Get-Job`
- `Receive-Job`
- `Stop-Job`

#### <a name="demo-loadgeneratorps1-actions"></a>Demo-LoadGenerator.ps1 akce

*Demo-LoadGenerator.ps1* napodobuje aktivní úlohu transakcí odběratele. Následující kroky popisují posloupnost akcí, *ukázku LoadGenerator.ps1* zahájí:

1. *Demo-LoadGenerator.ps1* spustí *LoadGenerator.ps1* v popředí.

    - Oba soubory .ps1 jsou uložené ve složkách Learning moduly\\nástroje\\.

2. *LoadGenerator.ps1* smyčky prostřednictvím všechny databáze klienta v katalogu.

3. *LoadGenerator.ps1* spustí úlohu na pozadí prostředí PowerShell pro každou databázi klienta:

    - Ve výchozím nastavení spustí úlohy na pozadí po dobu 120 minut.
    - Každá úloha způsobí, že zatížení procesoru založené na jednoho klienta databáze spuštěním *sp_CpuLoadGenerator*. Intenzita a dobu trvání zatížení se liší v závislosti na `$DemoScenario`. 
    - *sp_CpuLoadGenerator* smyčky kolem příkazu SQL SELECT, který způsobuje vysoké zatížení procesoru. Časový interval mezi problémy SELECT se liší podle hodnoty parametrů k vytvoření ovladatelné zatížení procesoru. Úrovní zátěže a intervaly se náhodně mění k simulaci realističtější zatížením.
    - Tento soubor .sql je uložen pod *WingtipTenantDB\\dbo\\StoredProcedures\\*.

4. Pokud `$OneTime = $false`, generátor zatížení spustí úlohy na pozadí a potom pracuje. Každých 10 sekund, sleduje pro žádné nové klienty, které jsou zřízené. Pokud nastavíte `$OneTime = $true`, LoadGenerator spustí úlohy na pozadí a poté se zastaví spuštěná v popředí. V tomto kurzu ponechte `$OneTime = $false`.

  Pokud chcete zastavit nebo restartovat generátor zatížení pomocí Ctrl-C nebo zastavit operaci Ctrl-Break. 

  Pokud necháte generátor zatížení spuštěná v popředí, použijte jiná instance prostředí PowerShell ISE ke spouštění dalších skriptů prostředí PowerShell.

&nbsp;

Než budete pokračovat v další části, ponechte generátor zatížení spuštěna ve stavu vyvolání úlohy.

## <a name="provision-a-new-tenant"></a>Zřízení nového tenanta

Počáteční nasazení vytvoří tři ukázkové klientů. Teď vytvoříte jiného klienta a zobrazte dopad na nasazené aplikace. V aplikaci Wingtip je podrobně pracovního postupu zřídit nové klienty [kurzu zřizování a katalog](saas-dbpertenant-provision-and-catalog.md). V této fázi můžete vytvořit nového klienta, který přebírá méně než jedna minuta.

1. Otevřete nové prostředí PowerShell ISE.
2. Otevřete... \\Učení Modules\Provision a katalog\\*ukázku ProvisionAndCatalog.ps1*.
3. Pokud chcete spustit skript, stisknutím klávesy F5. Ponechte výchozí hodnoty pro nyní.

   > [!NOTE]
   > Použít mnoho skriptů Wingtip SaaS *$PSScriptRoot* procházení složek volat funkce v jiné skripty. Tato proměnná se vyhodnocují jenom v případě, že úplné skript se spustí, stisknutím klávesy F5. Zvýraznění a spuštění výběr s F8 může vést k chybám. Pokud chcete spustit skripty, stisknutím klávesy F5.

Novou databázi klienta je:

- Vytvořit ve fondu elastické databáze SQL.
- Inicializovat.
- Zaregistrovány v katalogu.

Po úspěšném zřízení *události* server k novému klientovi se zobrazí v prohlížeči.

![Nový tenant](./media/saas-dbpertenant-get-started-deploy/red-maple-racing.png)

Aktualizujte Centrum událostí aby k novému klientovi v seznamu se zobrazí.

## <a name="explore-the-servers-pools-and-tenant-databases"></a>Zkoumání serverů, fondů a databází tenantů

Teď, když jste spuštění, spuštění zátěžového proti kolekci klientů, podíváme se na některé prostředky, které byly nasazeny.

1. V [portál Azure](http://portal.azure.com), přejděte do seznamu serverů SQL Server. Otevřete **katalogu-dpt -&lt;uživatele&gt;**  serveru.
    - Na server katalogu obsahuje dvě databáze, **tenantcatalog** a **basetenantdb** (šablony databáze, která se zkopírují k vytvoření nové klienty).

   ![Databáze](./media/saas-dbpertenant-get-started-deploy/databases.png)

2. Vraťte se zpátky do seznamu serverů SQL.

3. Otevřete **tenants1-dpt -&lt;uživatele&gt;**  serveru, který obsahuje databáze klienta.

4. Viz následující položky:

    - Každá databáze klienta je **elastické standardní** databáze ve fondu standardní 50 eDTU.
    - Red javor Racing databáze je klienta databáze, kterou jste zřídili dříve.

   ![Server s databází](./media/saas-dbpertenant-get-started-deploy/server.png)

## <a name="monitor-the-pool"></a>Monitorování fondu

Po *LoadGenerator.ps1* spuštění pro několik minut, dostatek data by měla být k dispozici pro zahájení vyhledávání v některé možnosti monitorování. Tyto možnosti jsou integrovány do fondů a databází.

Přejděte na server **tenants1-dpt -&lt;uživatele&gt;** a vyberte **Pool1** zobrazení využití prostředků pro fond. V následující grafy generátor zatížení spustili jednu hodinu.

   ![Monitorování fondu](./media/saas-dbpertenant-get-started-deploy/monitor-pool.png)

- První graf, s názvem bez přípony **využití prostředků**, zobrazuje fond využití eDTU.
- Druhý graf zobrazuje využití eDTU pět Nejaktivnější databází ve fondu.

Dvou grafech znázorňují elastické fondy a databáze SQL se skvěle hodí pro úlohy nepředvídatelným aplikací SaaS. Grafy ukazují, že jsou čtyři databáze každý bursting až o 40 Edtu a ještě jsou všechny databáze pohodlně podporován 50 eDTU fondu. 50 eDTU fondu může podporovat i větší zatížení. Pokud jsou databáze zřízené jako samostatné databáze, každé z nich musí být S2 (50 DTU) pro podporu shluky. Náklady na čtyři databáze S2 samostatné je téměř třikrát cena fondu. V situacích, reálného databáze SQL zákazníci spustit až 500 databází ve fondech 200 eDTU. Další informace najdete v tématu [kurzu monitorování výkonu](saas-dbpertenant-performance-monitoring.md).

## <a name="additional-resources"></a>Další zdroje informací:

- Další informace najdete v části Další [návodů, které vychází z databáze za klienta aplikace SaaS lístky Wingtip](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials).
- Další informace o elastické fondy najdete v tématu [co je fondu elastické databáze Azure SQL?](sql-database-elastic-pool.md).
- Další informace o elastické úlohy najdete v tématu [spravovat cloudové upraveným databáze](sql-database-elastic-jobs-overview.md).
- Další informace o víceklientské aplikace SaaS najdete v tématu [vzory pro víceklientské aplikace SaaS návrhu](saas-tenancy-app-design-patterns.md).


## <a name="next-steps"></a>Další postup

V tomto kurzu jste se dozvěděli:

> [!div class="checklist"]
> - Postup nasazení aplikace SaaS Wingtip lístků.
> - O serverech, fondy a databází, které tvoří aplikace.
> - Jak klienti jsou mapované na svoje data pomocí *katalogu*.
> - Jak zřídit nové klienty.
> - Postup zobrazení využití fondu monitorování klienta.
> - Postup odstranění ukázkové prostředky zastavit související fakturace.

Zkuste [kurzu zřizování a katalog](saas-dbpertenant-provision-and-catalog.md).



<!-- Link references. -->

[github-wingtip-dpt]: https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant 

