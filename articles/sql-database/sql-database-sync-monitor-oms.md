---
title: Monitorování synchronizace dat SQL pomocí protokolů Azure Monitoru
description: Zjistěte, jak monitorovat Azure SQL Data Sync pomocí protokolů Azure Monitoru
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: data sync
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 12/20/2018
ms.openlocfilehash: 5f5980f74b24cd972d43e9b05d4a5d623e6e3d2f
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81383692"
---
# <a name="monitor-sql-data-sync-with-azure-monitor-logs"></a>Monitorování synchronizace dat SQL pomocí protokolů Azure Monitoru 

Chcete-li zkontrolovat protokol aktivit synchronizace dat SQL a zjistit chyby a upozornění, dříve jste museli zkontrolovat synchronizaci dat SQL ručně na webu Azure Portal nebo použít PowerShell nebo rozhraní REST API. Podle pokynů v tomto článku nakonfigurujte vlastní řešení, které vylepšuje možnosti monitorování synchronizace dat. Toto řešení můžete přizpůsobit tak, aby vyhovovalo vašemu scénáři.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Přehled Synchronizace dat SQL najdete v tématu [Synchronizace dat mezi několika cloudovými a místními databázemi pomocí Synchronizace dat SQL Azure](sql-database-sync-data.md).

> [!IMPORTANT]
> Azure SQL Data Sync v tuto chvíli **nepodporuje** spravovanou instanci Azure SQL Database.

## <a name="monitoring-dashboard-for-all-your-sync-groups"></a>Řídicí panel monitorování pro všechny skupiny synchronizace 

Již není nutné prohlížet protokoly každé skupiny synchronizace jednotlivě hledat problémy. Všechny skupiny synchronizace můžete sledovat z libovolného předplatného na jednom místě pomocí vlastního zobrazení Azure Monitoru. Toto zobrazení zobrazí informace, které jsou důležité pro zákazníky SQL Data Sync.

![Řídicí panel monitorování synchronizace dat](media/sql-database-sync-monitor-oms/sync-monitoring-dashboard.png)

## <a name="automated-email-notifications"></a>Automatická e-mailová oznámení

Už nemusíte kontrolovat protokol ručně na webu Azure Portal nebo prostřednictvím Prostředí PowerShell nebo rozhraní REST API. Pomocí protokolů Azure Monitor můžete vytvářet [výstrahy,](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview)které přejdou přímo na e-mailové adresy lidí, kteří je potřebují vidět, když dojde k chybě.

![E-mailová oznámení synchronizace dat](media/sql-database-sync-monitor-oms/sync-email-notifications.png)

## <a name="how-do-you-set-up-these-monitoring-features"></a>Jak nastavujete tyto funkce monitorování? 

Implementujte vlastní řešení monitorování protokolů Azure Monitor pro synchronizaci dat SQL za méně než hodinu provedením následujících akcí:

Je třeba nakonfigurovat tři součásti:

-   Runbook prostředí PowerShell pro přenos dat protokolu synchronizace dat SQL do protokolů Azure Monitor.

-   Výstraha Azure Monitor pro e-mailová oznámení.

-   Zobrazení monitorování Azure pro monitorování.

### <a name="samples-to-download"></a>Ukázky ke stažení

Stáhněte si následující dva ukázky:

-   [Runbook prostředí PowerShell protokolu synchronizace dat](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/DataSyncLogPowerShellRunbook.ps1)

-   [Zobrazení monitoru Azure pro synchronizaci dat](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/DataSyncLogOmsView.omsview)

### <a name="prerequisites"></a>Požadavky

Ujistěte se, že jste nastavili následující věci:

-   Účet Azure Automation

-   Pracovní prostor služby Log Analytics

## <a name="powershell-runbook-to-get-sql-data-sync-log"></a>Aplikace PowerShell Runbook pro získání protokolu synchronizace dat SQL 

Pomocí runbooku PowerShellu hostované v Azure Automation můžete vytáhnout data protokolu synchronizace dat SQL a odeslat je do protokolů Azure Monitor. Ukázkový skript je součástí. Jako předpoklad musíte mít účet Azure Automation. Pak musíte vytvořit runbook a naplánovat jeho spuštění. 

### <a name="create-a-runbook"></a>Vytvoření runbooku

Další informace o vytvoření runbooku najdete [v tématu Moje první runbook prostředí PowerShell](https://docs.microsoft.com/azure/automation/automation-first-runbook-textual-powershell).

1.  V části účet Azure Automation vyberte kartu **Runbook** v části Automatizace procesů.

2.  V levém horním rohu stránky Runbook vyberte **Přidat runbook.**

3.  Vyberte **Importovat existující sady Runbook**.

4.  V části **Soubor runbooku**použijte daný `DataSyncLogPowerShellRunbook` soubor. Nastavte **typ sady Runbook** jako `PowerShell`. Pojmenujte runbook.

5.  Vyberte **Vytvořit**. Nyní máte runbook.

6.  V části účet Azure Automation vyberte kartu **Proměnné** v části Sdílené prostředky.

7.  Na stránce Proměnné vyberte **Přidat proměnnou.** Vytvořte proměnnou pro uložení času posledního spuštění pro runbook. Pokud máte více runbooků, potřebujete pro každou runbooku jednu proměnnou.

8.  Nastavte název proměnné `DataSyncLogLastUpdatedTime` jako a nastavte její Typ jako DateTime.

9.  Vyberte runbook a klikněte na tlačítko upravit v horní části stránky.

10. Proveďte změny požadované pro váš účet a konfiguraci synchronizace dat SQL. (Podrobnější informace naleznete v ukázkovém skriptu.)

    1.  Informace o Azure.

    2.  Synchronizovat informace o skupině.

    3.  Azure Monitor protokoluje informace. Najděte tyto informace na Webu Azure Portal | Nastavení | Připojené zdroje. Další informace o odesílání dat do protokolů Azure Monitor u nás najdete v tématu [Odesílání dat do protokolů Azure Monitor pomocí rozhraní HTTP Data Collector API (preview).](../azure-monitor/platform/data-collector-api.md)

11. Spusťte runbook v podokně test. Zkontrolujte, zda byla úspěšná.

    Pokud máte chyby, ujistěte se, že máte nainstalovaný nejnovější modul PowerShellu. Nejnovější modul Prostředí PowerShell můžete nainstalovat v **Galerii modulů** ve svém účtu Automation.

12. Klikněte na **Publikovat.**

### <a name="schedule-the-runbook"></a>Naplánování runbooku

Jak naplánovat runbook:

1.  V části Runbook vyberte kartu **Plány** v části Zdroje.

2.  Na stránce Plány vyberte **Přidat plán.**

3.  Vyberte **Propojit plán se souborem Runbook**.

4.  Vyberte **Vytvořit nový plán.**

5.  Nastavte opakování na **Opakované** a nastavte požadovaný interval. Použijte stejný interval tady, ve skriptu a v protokolech Azure Monitor.

6.  Vyberte **Vytvořit**.

### <a name="check-the-automation"></a>Zkontrolujte automatizaci

Chcete-li sledovat, zda je automatizace spuštěna podle očekávání, vyhledejte v části **Přehled** účtu automatizace zobrazení **Statistika úloh** v části **Monitorování**. Připnete toto zobrazení na řídicí panel pro snadné zobrazení. Úspěšné spuštění runbooku se zobrazí jako "Dokončeno" a Neúspěšné spuštění se zobrazí jako "Neúspěšné".

## <a name="create-an-azure-monitor-reader-alert-for-email-notifications"></a>Vytvoření výstrahy čtečky monitorů Azure pro e-mailová oznámení

Chcete-li vytvořit výstrahu, která používá protokoly Azure Monitor, postupujte takto. Jako předpoklad musíte mít protokoly Azure Monitor propojené s pracovním prostorem Log Analytics.

1.  Na webu Azure Portal vyberte **Hledání protokolů**.

2.  Vytvořte dotaz pro výběr chyb a upozornění podle skupiny synchronizace v rámci vybraného intervalu. Příklad:

    `DataSyncLog_CL | where LogLevel_s != "Success" | summarize AggregatedValue = count() by bin(TimeGenerated,60m),SyncGroupName_s`

3.  Po spuštění dotazu vyberte zvonek s **upozorněním**.

4.  V části **Generovat výstrahu na základě**vyberte **metrický měření**.

    1.  Nastavte agregační hodnotu na **hodnotu Větší než**.

    2.  Po **větší než**, zadejte prahovou hodnotu, která má uplynout před přijetím oznámení. V synchronizaci dat se očekávají přechodné chyby. Chcete-li snížit hluk, nastavte prahovou hodnotu na 5.

5.  V části **Akce**nastavte **e-mailové oznámení** na "Ano". Zadejte požadované příjemce e-mailu.

6.  Klikněte na **Uložit**. Zadali příjemci nyní obdrží e-mailová oznámení, když dojde k chybám.

## <a name="create-an-azure-monitor-view-for-monitoring"></a>Vytvoření zobrazení monitoru Azure pro monitorování

Tento krok vytvoří zobrazení Azure Monitor vizuálně sledovat všechny zadané skupiny synchronizace. Zobrazení obsahuje několik součástí:

-   Dlaždice přehledu, která ukazuje, kolik chyb, úspěchů a upozornění mají všechny skupiny synchronizace.

-   Dlaždice pro všechny skupiny synchronizace, která zobrazuje počet chyb a upozornění na skupinu synchronizace. Skupiny bez problémů se na této dlaždici nezobrazí.

-   Dlaždice pro každou skupinu synchronizace, která zobrazuje počet chyb, úspěchů a upozornění a nedávné chybové zprávy.

Pokud chcete nakonfigurovat zobrazení Azure Monitoru, postupujte takto:

1.  Na domovské stránce pracovního prostoru Log Analytics vyberte vyberte plus vlevo a otevřete **návrháře zobrazení**.

2.  Na horním panelu návrháře zobrazení vyberte **Importovat.** Pak vyberte ukázkový soubor DataSyncLogOMSView.

3.  Ukázkové zobrazení je pro správu dvou skupin synchronizace. Upravte toto zobrazení tak, aby odpovídalo vašemu scénáři. Klikněte na **upravit** a proveďte následující změny:

    1.  Podle potřeby vytvořte nové objekty "Donut & List" z galerie.

    2.  V každé dlaždici aktualizujte dotazy informacemi.

        1.  Na každé dlaždici změňte interval TimeStamp_t podle potřeby.

        2.  Na dlaždicích pro každou skupinu synchronizace aktualizujte názvy skupin synchronizace.

    3.  Na každé dlaždici podle potřeby aktualizujte název.

4.  Klikněte na **Uložit** a zobrazení je připravené.

## <a name="cost-of-this-solution"></a>Náklady na toto řešení

Ve většině případů je toto řešení zdarma.

**Azure Automation:** V závislosti na vašem využití mohou být u účtu Azure Automation účtovány náklady. Prvních 500 minut běhu úlohy za měsíc je zdarma. Ve většině případů se očekává, že toto řešení bude používat méně než 500 minut za měsíc. Chcete-li se vyhnout poplatkům, naplánujte spuštění runbooku v intervalu dvou hodin nebo více. Další informace najdete v [tématu Automatizace cen](https://azure.microsoft.com/pricing/details/automation/).

**Protokoly Azure Monitor:** V závislosti na vašem využití mohou být s protokoly Azure Monitoru spojeny náklady. Úroveň free zahrnuje 500 MB ingestovaných dat za den. Ve většině případů se očekává, že toto řešení ingestovat méně než 500 MB za den. Chcete-li snížit využití, použijte filtrování pouze selhání zahrnuté v sada runbook. Pokud používáte více než 500 MB za den, upgradujte na placenou úroveň, abyste se vyhnuli riziku zastavení analýzy při dosažení omezení. Další informace najdete v tématu [Azure Monitor protokoly cen](https://azure.microsoft.com/pricing/details/log-analytics/).

## <a name="code-samples"></a>Ukázky kódů

Stáhněte si ukázky kódu popsané v tomto článku z následujících umístění:

-   [Runbook prostředí PowerShell protokolu synchronizace dat](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/DataSyncLogPowerShellRunbook.ps1)

-   [Zobrazení monitoru Azure pro synchronizaci dat](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/DataSyncLogOmsView.omsview)

## <a name="next-steps"></a>Další kroky
Další informace o Synchronizaci dat SQL:

-   Přehled – [Synchronizace dat mezi několika cloudovými a místními databázemi pomocí Azure SQL Data Sync](sql-database-sync-data.md)
-   Nastavení synchronizace dat
    - Na portálu – [kurz: Nastavení synchronizace dat SQL pro synchronizaci dat mezi databází Azure SQL a SQL Server em i v místním prostředí](sql-database-get-started-sql-data-sync.md)
    - S využitím PowerShellu
        -  [Synchronizace mezi několika databázemi Azure SQL pomocí PowerShellu](scripts/sql-database-sync-data-between-sql-databases.md)
        -  [Použití PowerShellu k synchronizaci mezi službou Azure SQL Database a místní databází SQL Serveru](scripts/sql-database-sync-data-between-azure-onprem.md)
-   Agent synchronizace dat – [agent synchronizace dat pro synchronizaci dat Azure SQL](sql-database-data-sync-agent.md)
-   Doporučené postupy – [doporučené postupy pro Azure SQL Data Sync](sql-database-best-practices-data-sync.md)
-   Poradce při potížích – [řešení problémů se synchronizací dat Azure SQL](sql-database-troubleshoot-data-sync.md)
-   Aktualizace schématu synchronizace
    -   S Transact-SQL – [automatizace replikace změn schématu v Synchronizaci dat Azure SQL](sql-database-update-sync-schema.md)
    -   S PowerShellem – [k aktualizaci schématu synchronizace v existující skupině synchronizace použijte PowerShell](scripts/sql-database-sync-update-schema.md)

Další informace o službě SQL Database:

-   [Přehled služby SQL Database](sql-database-technical-overview.md)
-   [Správa životního cyklu databáze](https://msdn.microsoft.com/library/jj907294.aspx)
