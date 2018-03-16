---
title: "Monitorování synchronizaci dat Azure SQL s OMS Log Analytics | Microsoft Docs"
description: "Naučte se monitorovat synchronizaci dat SQL Azure s využitím analýzy protokolů OMS"
services: sql-database
ms.date: 11/07/2017
ms.topic: article
ms.service: sql-database
author: douglaslMS
ms.author: douglasl
manager: craigg
ms.custom: data-sync
ms.openlocfilehash: 423614227b16ad6cfa6dadfab1c5f2b93c5bbe62
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2018
---
# <a name="monitor-sql-data-sync-preview-with-oms-log-analytics"></a>Synchronizaci dat SQL (Preview) monitorování s OMS analýzy protokolů 

Zkontrolujte protokol aktivit synchronizaci dat SQL a zjištění chyby a upozornění, dříve bylo nutné ručně zkontrolujte synchronizaci dat SQL na portálu Azure nebo pomocí Powershellu nebo rozhraní REST API. Postupujte podle kroků v tomto článku nakonfigurovat vlastní řešení, který zlepšuje synchronizaci dat možností monitorování. Toto řešení vyhovovat vašemu scénáři můžete přizpůsobit.

Přehled synchronizaci dat SQL najdete v tématu [synchronizaci dat mezi několika databází cloudu a místně s synchronizaci dat SQL Azure (Preview)](sql-database-sync-data.md).

## <a name="monitoring-dashboard-for-all-your-sync-groups"></a>Řídicí panel monitorování pro všechny skupiny pro synchronizaci 

Již nepotřebujete projděte protokol každé skupiny synchronizace jednotlivě, abyste zjišťování problémů. Všechny skupiny pro synchronizaci z jakéhokoli z vašich předplatných na jednom místě, můžete monitorovat pomocí vlastní zobrazení OMS (Operations Management Suite). Toto zobrazení zobrazí informace, které záleží zákazníkům synchronizaci dat SQL.

![Řídicí panel monitorování pro synchronizaci dat](media/sql-database-sync-monitor-oms/sync-monitoring-dashboard.png)

## <a name="automated-email-notifications"></a>Automatizované e-mailových oznámení

Již nepotřebujete protokolu ručně na portálu Azure nebo pomocí prostředí PowerShell nebo rozhraní REST API. S [analýzy protokolů OMS](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview), můžete vytvářet výstrahy, které přejít přímo na e-mailové adresy osob, které potřebujete vědět, když dojde k chybě.

![Data synchronizace e-mailových oznámení](media/sql-database-sync-monitor-oms/sync-email-notifications.png)

## <a name="how-do-you-set-up-these-monitoring-features"></a>Jak můžete nastavit tyto funkce sledování? 

Implementujte vlastní OMS řešení monitorování pro synchronizaci dat SQL za méně než hodinu provedením následujících akcí:

Je třeba nakonfigurovat tři komponenty:

-   Powershellový runbook ke kanálu data protokolu synchronizaci dat SQL k OMS.

-   Upozornění analýzy protokolů OMS pro e-mailová oznámení.

-   Zobrazení OMS pro monitorování.

### <a name="samples-to-download"></a>Ukázky ke stažení

Stáhněte si následující dvě ukázky:

-   [Runbook Powershellu protokolu synchronizace dat](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/DataSyncLogPowerShellRunbook.ps1)

-   [Zobrazení OMS protokolu synchronizace dat](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/DataSyncLogOmsView.omsview)

### <a name="prerequisites"></a>Požadavky

Ujistěte se, že jste nastavili následujících akcí:

-   Účet automatizace Azure

-   Propojit s pracovní prostor OMS analýzy protokolů

## <a name="powershell-runbook-to-get-sql-data-sync-log"></a>Powershellový Runbook získat protokolu synchronizaci dat SQL 

Použijte Powershellový runbook hostované ve službě Azure Automation k synchronizaci dat SQL data protokolu pro vyžádání obsahu a jeho odeslání na OMS. Ukázkový skript je zahrnuta. Předpokladem je musíte mít účet Azure Automation. Pak budete muset vytvořit sadu runbook a naplánovat jeho spuštění. 

### <a name="create-a-runbook"></a>Vytvoření runbooku

Další informace o vytvoření sady runbook najdete v tématu [Můj první Powershellový runbook](https://docs.microsoft.com/azure/automation/automation-first-runbook-textual-powershell).

1.  V rámci účtu Azure Automation vyberte **Runbooky** kartě pod automatizaci procesů.

2.  Vyberte **přidat Runbook** v levém horním rohu stránky sady Runbook.

3.  Vyberte **importovat stávající Runbook**.

4.  V části **soubor sady Runbook**, použijte danou `DataSyncLogPowerShellRunbook` souboru. Nastavte **typ Runbooku** jako `PowerShell`. Dejte runbooku název.

5.  Vyberte **Vytvořit**. Nyní máte sady runbook.

6.  V rámci účtu Azure Automation, vyberte **proměnné** kartě části sdílené prostředky.

7.  Vyberte **přidat proměnnou** na stránce proměnné. Vytvořte proměnnou pro uložení čas posledního provedení pro sadu runbook. Pokud máte více sad runbook, musíte pro každou sadu runbook jednu proměnnou.

8.  Nastavte název proměnné jako `DataSyncLogLastUpdatedTime` a nastavte její typ jako data a času.

9.  Vyberte sadu runbook a klikněte na tlačítko Upravit v horní části stránky.

10. Proveďte požadované změny potřebné pro váš účet a konfiguraci synchronizaci dat SQL. (Podrobnější informace najdete v tématu ukázkový skript.)

    1.  Informace o Azure.

    2.  Informace o skupině synchronizace.

    3.  Informace o OMS. Tyto informace najdete na portálu OMS | Nastavení | Připojené zdroje. Další informace o odeslání dat k analýze protokolů najdete v tématu [odesílat data k analýze protokolů s rozhraním API týkající se kolekce dat protokolu HTTP (preview)](../log-analytics/log-analytics-data-collector-api.md).

11. Spuštění sady runbook v testovací podokno. Zkontrolujte, ujistěte se, že byla úspěšná.

    Pokud máte chyby, ujistěte se, že máte nejnovější modul prostředí PowerShell nainstalovaný. Nejnovější modul prostředí PowerShell v můžete nainstalovat **moduly Galerie** ve vašem účtu Automation.

12. Klikněte na tlačítko **publikování**

### <a name="schedule-the-runbook"></a>Plán sady runbook

Při plánování sady runbook:

1.  V části sada runbook, vyberte **plány** na kartě prostředky.

2.  Vyberte **přidat plán** na stránce plány.

3.  Vyberte **propojit plán s runbookem**.

4.  Vyberte **vytvořte nový plán.**

5.  Nastavit **opakování** opakovaná sadu interval chcete a. Použijte stejný interval zde ve skriptu a v OMS.

6.  Vyberte **Vytvořit**.

### <a name="check-the-automation"></a>Zkontrolujte automatizace

Chcete-li sledovat, jestli vaše automation běží podle očekávání, v části **přehled** u vašeho účtu automation najít **Statistika projektu** zobrazit v části **monitorování**. Připnete na řídicí panel pro snadné prohlížení tohoto zobrazení. Úspěšné spuštění sady runbook zobrazit jako "Dokončeno" a spustí se nezdařilo zobrazí jako "Se nezdařilo."

## <a name="create-an-oms-log-reader-alert-for-email-notifications"></a>Vytvořit výstrahu čtečky protokolu OMS e-mailových oznámení

Vytvořit výstrahu, která využívá analýzy protokolů OMS, provádět následující akce. Předpokladem je musíte mít analýzy protokolů propojit s pracovní prostor služby OMS.

1.  Na portálu OMS vyberte **hledání protokolů**.

2.  Vytvořte dotaz, který vyberte s chybami a upozorněními synchronizace skupinou v rámci intervalu, které jste vybrali. Příklad:

    `Type=DataSyncLog\_CL LogLevel\_s!=Success| measure count() by SyncGroupName\_s interval 60minute`

3.  Po spuštění dotazu, vyberte na zvonek, která uvádí, že **výstrahy**.

4.  V části **vygenerovat upozornění na základě**, vyberte **metriky měření**.

    1.  Nastavte agregační hodnotu na **větší než**.

    2.  Po **větší než**, zadejte prahovou hodnotu před přijímat oznámení. V synchronizaci dat se očekává přechodné chyby. Ke snížení šumu, nastavena na 5 prahovou hodnotu.

5.  V části **akce**, nastavte **oznámení e-mailem** na "Ano". Zadejte příjemce požadované e-mailů.

6.  Klikněte na **Uložit**. Zadaným příjemcům nyní přijímat e-mailová oznámení, pokud dojde k chybám.

## <a name="create-an-oms-view-for-monitoring"></a>Vytvoření zobrazení OMS pro monitorování

Tento krok vytvoří zobrazení o OMS vizuálně monitorovat všechny zadané synchronizace skupiny. Zobrazení obsahuje několik komponent:

-   Dlaždice přehledu, který ukazuje, kolik chyby, úspěchy a upozornění mají všechny synchronizace skupiny.

-   Dlaždice pro všechny skupiny pro synchronizaci, která ukazuje počet chyby a upozornění na skupinu synchronizace. Skupiny bez problémů nezobrazí na tuto dlaždici.

-   Dlaždice pro každou skupinu synchronizace, který se zobrazuje číslo chyby, upozornění, úspěchy, a poslední chybové zprávy.

Pokud chcete nakonfigurovat OMS zobrazení, proveďte následující akce:

1.  Na domovské stránce OMS vyberte plus na levé straně otevřete **Návrhář zobrazení**.

2.  Vyberte **Import** na horním panelu návrháře zobrazení. Pak vyberte ukázkový soubor "DataSyncLogOMSView".

3.  Ukázkové zobrazení je pro správu dvě synchronizace skupiny. Upravte toto zobrazení přizpůsobit váš scénář. Klikněte na tlačítko **upravit** a proveďte následující změny:

    1.  Podle potřeby vytvořte nové objekty "a seznam prstenec" z galerie.

    2.  V každé dlaždice aktualizujte dotazy s informacemi.

        1.  Na každou dlaždici změňte interval TimeStamp_t podle potřeby.

        2.  Na dlaždicích pro každou skupinu synchronizace aktualizuje názvy synchronizace skupiny.

    3.  Na každé dlaždici aktualizujte název podle potřeby.

4.  Klikněte na tlačítko **Uložit** a zobrazení je připravený.

## <a name="cost-of-this-solution"></a>Náklady tohoto řešení

Ve většině případů toto řešení je bezplatná.

**Azure Automation:** může být náklady spojené s účet Azure Automation, v závislosti na vaší využití. První 500 počet minut za měsíc dobu běhu úloh jsou volné. Ve většině případů se očekává toto řešení použít méně než 500 minut za měsíc. Abyste se vyhnuli poplatky, naplánujte spuštění v intervalu dvě hodiny nebo víc sady runbook. Další informace najdete v tématu [ceny automatizace](https://azure.microsoft.com/pricing/details/automation/).

**Analýzy protokolů OMS:** může být s náklady spojené s OMS v závislosti na vaší využití. Úroveň free zahrnuje 500 MB ingestovaný dat za den. Ve většině případů se očekává toto řešení ingestovat menší než 500 MB za den. Chcete-li snížit využití, použijte jen selhání filtrování zahrnuté v sadě runbook. Pokud používáte více než 500 MB za den, upgradujte na úroveň placené, aby se zabránilo Analytics zastavení, když je dosaženo omezení na. Další informace najdete v tématu [analýzy protokolů ceny](https://azure.microsoft.com/pricing/details/log-analytics/).

## <a name="code-samples"></a>Ukázky kódů

Stáhněte si ukázky kódu, které jsou popsané v tomto článku v následujících umístěních:

-   [Runbook Powershellu protokolu synchronizace dat](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/DataSyncLogPowerShellRunbook.ps1)

-   [Zobrazení OMS protokolu synchronizace dat](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/DataSyncLogOmsView.omsview)

## <a name="next-steps"></a>Další kroky
Další informace o synchronizaci dat SQL najdete v tématu:

-   [Synchronizaci dat mezi několika databází cloudu a místně s synchronizaci dat SQL Azure](sql-database-sync-data.md)
-   [Nastavit synchronizaci dat SQL Azure](sql-database-get-started-sql-data-sync.md)
-   [Osvědčené postupy pro synchronizaci dat SQL Azure](sql-database-best-practices-data-sync.md)
-   [Řešení problémů s synchronizaci dat SQL Azure](sql-database-troubleshoot-data-sync.md)

-   Dokončete příklady prostředí PowerShell, které ukazují, jak nakonfigurovat synchronizaci dat SQL:
    -   [Pomocí prostředí PowerShell k synchronizaci mezi více databází Azure SQL](scripts/sql-database-sync-data-between-sql-databases.md)
    -   [Synchronizace mezi databáze SQL Azure a místní databáze SQL serveru pomocí prostředí PowerShell](scripts/sql-database-sync-data-between-azure-onprem.md)

-   [Stáhněte si dokumentaci rozhraní API REST synchronizaci dat SQL](https://github.com/Microsoft/sql-server-samples/raw/master/samples/features/sql-data-sync/Data_Sync_Preview_REST_API.pdf?raw=true)

Další informace o databázi SQL najdete v tématu:

-   [Databáze SQL – přehled](sql-database-technical-overview.md)
-   [Správa životního cyklu databáze](https://msdn.microsoft.com/library/jj907294.aspx)
