---
title: Monitorování Synchronizace dat SQL pomocí protokolů Azure Monitor
description: Naučte se monitorovat Synchronizace dat SQL pomocí protokolů Azure Monitor.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: data sync, sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/20/2018
ms.openlocfilehash: bd2f0a3e41508dd41dc0e5b62532e21677987845
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91332929"
---
# <a name="monitor-sql-data-sync-with-azure-monitor-logs"></a>Monitorování Synchronizace dat SQL pomocí protokolů Azure Monitor 

Chcete-li kontrolovat Synchronizace dat SQL protokolu aktivit a zjišťovat chyby a upozornění, dříve bylo nutné Synchronizace dat SQL ručně kontrolovat v Azure Portal nebo použít PowerShell nebo REST API. Podle kroků v tomto článku můžete nakonfigurovat vlastní řešení, které vylepšuje prostředí pro monitorování synchronizace dat. Toto řešení můžete přizpůsobit tak, aby vyhovovalo vašemu scénáři.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

Přehled Synchronizace dat SQL najdete v tématu [synchronizace dat napříč několika cloudy a místními databázemi pomocí synchronizace dat SQL v Azure](sql-data-sync-data-sql-server-sql-database.md).

> [!IMPORTANT]
> Synchronizace dat SQL v tuto **chvíli nepodporuje spravovanou** instanci SQL Azure.

## <a name="monitoring-dashboard-for-all-your-sync-groups"></a>Řídicí panel monitorování pro všechny vaše skupiny synchronizace 

Už nemusíte procházet protokoly každé skupiny synchronizace jednotlivě, abyste mohli hledat problémy. Všechny skupiny synchronizace můžete monitorovat z libovolného předplatného na jednom místě pomocí vlastního zobrazení Azure Monitor. Toto zobrazení objednává informace, které se týkají Synchronizace dat SQL zákazníky.

![Řídicí panel monitorování synchronizace dat](./media/sql-data-sync-monitor-sync/sync-monitoring-dashboard.png)

## <a name="automated-email-notifications"></a>Automatizovaná e-mailová oznámení

Protokol už nemusíte kontrolovat ručně v Azure Portal nebo prostřednictvím PowerShellu nebo REST API. Pomocí [protokolů Azure monitor](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview)můžete vytvářet výstrahy, které přímo přejdou na e-mailové adresy osob, které je potřebují zobrazit, když dojde k chybě.

![E-mailová oznámení synchronizace dat](./media/sql-data-sync-monitor-sync/sync-email-notifications.png)

## <a name="how-do-you-set-up-these-monitoring-features"></a>Jak nastavíte tyto funkce monitorování? 

Implementací vlastního řešení Azure Monitor protokolů monitorování pro Synchronizace dat SQL během méně než hodiny proveďte následující akce:

Je nutné nakonfigurovat tři komponenty:

-   PowerShellový Runbook, který bude zadávat data protokolu Synchronizace dat SQL do Azure Monitor protokolů.

-   Výstraha Azure Monitor pro e-mailová oznámení

-   Zobrazení Azure Monitor pro monitorování.

### <a name="samples-to-download"></a>Ukázky ke stažení

Stáhněte si následující dvě ukázky:

-   [PowerShellový Runbook protokolu synchronizace dat](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/DataSyncLogPowerShellRunbook.ps1)

-   [Zobrazení Azure Monitor synchronizace dat](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/DataSyncLogOmsView.omsview)

### <a name="prerequisites"></a>Požadavky

Ujistěte se, že jste nastavili následující věci:

-   Účet Azure Automation

-   Pracovní prostor služby Log Analytics

## <a name="powershell-runbook-to-get-sql-data-sync-log"></a>PowerShellový Runbook pro získání protokolu Synchronizace dat SQL 

Použijte PowerShellový Runbook hostovaný v Azure Automation k vyžádání dat protokolu Synchronizace dat SQL a jejich odeslání do protokolů Azure Monitor. Je zahrnutý vzorový skript. Je nutné, abyste měli účet Azure Automation. Pak je potřeba vytvořit Runbook a naplánovat jeho spuštění. 

### <a name="create-a-runbook"></a>Vytvoření runbooku

Další informace o vytváření sad Runbook najdete v tématu [můj první powershellový Runbook](https://docs.microsoft.com/azure/automation/automation-first-runbook-textual-powershell).

1.  V části účet Azure Automation vyberte v části automatizace procesu kartu **Runbooky** .

2.  Vyberte **Přidat sadu Runbook** v levém horním rohu stránky sady Runbook.

3.  Vyberte **importovat existující Runbook**.

4.  V části **soubor sady Runbook**použijte daný `DataSyncLogPowerShellRunbook` soubor. Nastavte **typ Runbooku** jako `PowerShell` . Zadejte název Runbooku.

5.  Vyberte **Vytvořit**. Nyní máte sadu Runbook.

6.  V části účet Azure Automation vyberte v části sdílené prostředky kartu **proměnné** .

7.  Na stránce proměnné vyberte **přidat proměnnou** . Vytvořte proměnnou pro uložení poslední doby spuštění Runbooku. Pokud máte více sad Runbook, budete pro každou sadu Runbook potřebovat jednu proměnnou.

8.  Nastavte název proměnné jako `DataSyncLogLastUpdatedTime` DateTime a nastavte jeho typ jako DateTime.

9.  Vyberte Runbook a klikněte na tlačítko Upravit v horní části stránky.

10. Proveďte změny požadované pro váš účet a konfiguraci Synchronizace dat SQL. (Podrobnější informace najdete v ukázkovém skriptu.)

    1.  Informace o Azure.

    2.  synchronizovat informace o skupině.

    3.  Azure Monitor protokoluje informace. Tyto informace najdete v Azure Portal | Nastavení | Připojené zdroje. Další informace o posílání dat do protokolů Azure Monitor najdete v tématu [posílání dat do Azure monitor protokolů pomocí rozhraní API kolekce dat http (Preview)](../../azure-monitor/platform/data-collector-api.md).

11. Spusťte sadu Runbook v podokně test. Ověřte, zda bylo úspěšné.

    Pokud máte chyby, ujistěte se, že máte nainstalovaný nejnovější modul PowerShellu. Nejnovější modul prostředí PowerShell můžete nainstalovat do **Galerie modulů** v účtu Automation.

12. Klikněte na **publikovat** .

### <a name="schedule-the-runbook"></a>Naplánování runbooku

Postup při plánování sady Runbook:

1.  V rámci sady Runbook vyberte v části prostředky kartu **plány** .

2.  Na stránce plány vyberte **Přidat plán** .

3.  Vyberte **připojit plán k sadě Runbook**.

4.  Vyberte **vytvořit nový plán.**

5.  Nastavte **opakování** na opakující se a nastavte interval, který chcete. Ve skriptu a v protokolech Azure Monitor použijte stejný interval.

6.  Vyberte **Vytvořit**.

### <a name="check-the-automation"></a>Zkontroluje automatizaci.

Pokud chcete monitorovat, jestli je vaše automatizace spuštěná podle očekávání, najděte v části **Přehled** pro svůj účet Automation zobrazení **Statistika úlohy** v části **monitorování**. Toto zobrazení můžete připnout na řídicí panel, abyste ho mohli snadno prohlížet. Úspěšná spuštění Runbooku se zobrazí jako dokončená a neúspěšná spuštění se zobrazí jako neúspěšná.

## <a name="create-an-azure-monitor-reader-alert-for-email-notifications"></a>Vytvoření výstrahy čtečky Azure Monitor pro e-mailová oznámení

Chcete-li vytvořit výstrahu, která používá protokoly Azure Monitor, proveďte následující akce. V takovém případě musíte mít Azure Monitor protokoly propojené s pracovním prostorem Log Analytics.

1.  V Azure Portal vyberte **prohledávání protokolu**.

2.  Vytvořte dotaz pro výběr chyb a upozornění podle skupin synchronizace v intervalu, který jste vybrali. Příklad:

    `DataSyncLog_CL | where LogLevel_s != "Success" | summarize AggregatedValue = count() by bin(TimeGenerated,60m),SyncGroupName_s`

3.  Po spuštění dotazu vyberte zvonek, který říká **výstrahu**.

4.  V části **Generovat výstrahu na základě**vyberte **měření metriky**.

    1.  Nastavte agregovanou hodnotu na **větší než**.

    2.  Po **větší než**zadejte prahovou hodnotu, která uplyne před přijetím oznámení. V synchronizaci dat se očekávají přechodné chyby. Chcete-li snížit šum, nastavte prahovou hodnotu na 5.

5.  V části **Akce**nastavte **e-mailové oznámení** na Ano. Zadejte požadované příjemce e-mailu.

6.  Klikněte na **Uložit**. Zadaní příjemci teď při výskytu chyby dostanou e-mailová oznámení.

## <a name="create-an-azure-monitor-view-for-monitoring"></a>Vytvoření zobrazení Azure Monitor pro monitorování

Tento krok vytvoří zobrazení Azure Monitor pro vizuální monitorování všech zadaných skupin synchronizace. Zobrazení obsahuje několik součástí:

-   Dlaždice s přehledem, která zobrazuje počet chyb, úspěchů a upozornění všech skupin synchronizace.

-   Dlaždice všech skupin synchronizace, které zobrazují počet chyb a upozornění na skupinu synchronizace. Na této dlaždici se neobjeví skupiny bez problémů.

-   Dlaždice pro každou skupinu synchronizace, která zobrazuje počet chyb, úspěšnosti a upozornění a nedávné chybové zprávy.

Chcete-li nakonfigurovat zobrazení Azure Monitor, proveďte následující akce:

1.  Na domovské stránce Log Analytics pracovního prostoru vyberte symbol plus vlevo a otevřete tak **návrháře zobrazení**.

2.  V horním panelu návrháře zobrazení vyberte **importovat** . Pak vyberte vzorový soubor "DataSyncLogOMSView".

3.  Ukázkové zobrazení je pro správu dvou skupin synchronizace. Upravte toto zobrazení tak, aby vyhovovalo vašemu scénáři. Klikněte na **Upravit** a proveďte následující změny:

    1.  Podle potřeby vytvořte v galerii nový objekt "prstencový & list".

    2.  V každé dlaždici aktualizujte dotazy pomocí vašich informací.

        1.  Na každé dlaždici změňte interval TimeStamp_t podle potřeby.

        2.  Na dlaždicích pro každou skupinu synchronizace aktualizujte názvy skupin synchronizace.

    3.  Na každé dlaždici podle potřeby aktualizujte název.

4.  Klikněte na **Uložit** a zobrazení je připravené.

## <a name="cost-of-this-solution"></a>Náklady na toto řešení

Ve většině případů je toto řešení zdarma.

**Azure Automation:** V závislosti na vašem využití můžou být účtovány náklady na účet Azure Automation. Prvních 500 minut času běhu úlohy za měsíc je zdarma. Ve většině případů se očekává, že toto řešení bude používat méně než 500 minut za měsíc. Pokud se chcete vyhnout poplatkům, naplánujte spuštění sady Runbook v intervalu dvou hodin nebo více. Další informace najdete v tématu [ceny služby Automation](https://azure.microsoft.com/pricing/details/automation/).

**Protokoly Azure monitor:** V závislosti na vašem použití můžou být v protokolech Azure Monitor přidružené náklady. Úroveň Free zahrnuje 500 MB přijatých dat za den. Ve většině případů by toto řešení mělo ingestovat méně než 500 MB za den. Chcete-li snížit využití, použijte filtrování pouze pro neúspěšné zahrnutí do sady Runbook. Pokud používáte více než 500 MB za den, upgradujte na placenou úroveň, abyste se vyhnuli nebezpečí zastavení analýz při dosažení omezení. Další informace najdete v tématu [Azure monitor protokolu ceny](https://azure.microsoft.com/pricing/details/log-analytics/).

## <a name="code-samples"></a>Ukázky kódů

Ukázky kódu popsané v tomto článku si stáhněte z těchto umístění:

-   [PowerShellový Runbook protokolu synchronizace dat](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/DataSyncLogPowerShellRunbook.ps1)

-   [Zobrazení Azure Monitor synchronizace dat](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/DataSyncLogOmsView.omsview)

## <a name="next-steps"></a>Další kroky
Další informace o Synchronizaci dat SQL:

-   Přehled – [synchronizace dat napříč několika cloudy a místními databázemi pomocí synchronizace dat SQL v Azure](sql-data-sync-data-sql-server-sql-database.md)
-   Nastavení synchronizace dat
    - Na portálu – [kurz: nastavení synchronizace dat SQL pro synchronizaci dat mezi Azure SQL Database a SQL Server](sql-data-sync-sql-server-configure.md)
    - S využitím PowerShellu
        -  [Použití PowerShellu k synchronizaci mezi několika databázemi v Azure SQL Database](scripts/sql-data-sync-sync-data-between-sql-databases.md)
        -  [Použití PowerShellu k synchronizaci mezi databází v Azure SQL Database a databází v instanci SQL Server](scripts/sql-data-sync-sync-data-between-azure-onprem.md)
-   Agent synchronizace dat – [Agent synchronizace dat pro synchronizace dat SQL v Azure](sql-data-sync-agent-overview.md)
-   Osvědčené postupy – [osvědčené postupy pro synchronizace dat SQL v Azure](sql-data-sync-best-practices.md)
-   Řešení potíží – [řešení potíží s synchronizace dat SQL v Azure](sql-data-sync-troubleshoot.md)
-   Aktualizace schématu synchronizace
    -   Pomocí jazyka Transact-SQL – [Automatizace replikace změn schématu v synchronizace dat SQL v Azure](sql-data-sync-update-sync-schema.md)
    -   Prostředí PowerShell – [použití PowerShellu k aktualizaci schématu synchronizace v existující skupině synchronizace](scripts/update-sync-schema-in-sync-group.md)

Další informace o službě SQL Database:

-   [Přehled služby SQL Database](../../sql-database/sql-database-technical-overview.md)
-   [Správa životního cyklu databáze](https://msdn.microsoft.com/library/jj907294.aspx)
