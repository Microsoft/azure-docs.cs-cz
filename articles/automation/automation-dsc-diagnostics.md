---
title: Přepošlete data sestav stavů Azure Automation configuration do protokolů Azure Monitoru
description: Tento článek ukazuje, jak odesílat data sestavy konfigurace požadovaného stavu (DSC) z konfigurace stavu azure automatizace do protokolů Azure Monitor poskytovat další přehled a správu.
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.date: 11/06/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: a75b71d43b072d366ef2fcb15bf4c901680d48fb
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81383222"
---
# <a name="forward-azure-automation-state-configuration-reporting-data-to-azure-monitor-logs"></a>Přepošlete data sestav stavů Azure Automation configuration do protokolů Azure Monitoru

Konfigurace stavu azure automatizace uchovává data o stavu uzlu po dobu 30 dnů. Pokud chcete tato data uchovávat delší dobu, můžete do pracovního prostoru Log Analytics odesílat data o stavu uzlu. Stav dodržování předpisů je viditelný na webu Azure Portal nebo s PowerShellem, pro uzly a pro jednotlivé prostředky DSC v konfiguracích uzlů. 

Protokoly Azure Monitor udává lepší provozní přehled dat konfigurace stavu automatizace a mohou pomoci rychleji řešit incidenty. Pomocí protokolů Azure Monitor můžete:

- Získejte informace o dodržování předpisů pro spravované uzly a jednotlivé prostředky.
- Aktivuj e-mail nebo výstrahu na základě stavu dodržování předpisů.
- Pište rozšířené dotazy přes spravované uzly.
- Korelujte stav dodržování předpisů napříč účty automatizace.
- Pomocí vlastních zobrazení a vyhledávacích dotazů můžete vizualizovat výsledky sady Runbook, stav úlohy sady Runbook a další související klíčové ukazatele nebo metriky.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

>[!NOTE]
>Tento článek je aktualizovaný a využívá nový modul Az Azure PowerShellu. Můžete dál využívat modul AzureRM, který bude dostávat opravy chyb nejméně do prosince 2020. Další informace o kompatibilitě nového modulu Az a modulu AzureRM najdete v tématu [Seznámení s novým modulem Az Azure PowerShellu](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Pokyny k instalaci modulu AZ na pracovníka hybridní sady Runbook najdete [v tématu Instalace modulu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). U vašeho účtu Automation můžete aktualizovat moduly na nejnovější verzi pomocí [funkce Jak aktualizovat moduly Azure PowerShellu v Azure Automation](automation-update-azure-modules.md).

## <a name="prerequisites"></a>Požadavky

Chcete-li začít odesílat sestavy konfigurace stavu automatizace do protokolů Azure Monitor, potřebujete:

- Verze [Azure PowerShellu](/powershell/azure/overview) z listopadu 2016 nebo novější (verze 2.3.0).
- Účet Azure Automation. Další informace najdete [v tématu Úvod do Azure Automation](automation-intro.md).
- Pracovní prostor Log Analytics s nabídkou služby Automation & Control. Další informace najdete [v tématu Začínáme s Log Analytics v Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal).
- Alespoň jeden uzel konfigurace stavu azure automatizace. Další informace najdete [v tématu Onboarding machines for management by Azure Automation State Configuration](automation-dsc-onboarding.md).
- Modul [xDscDiagnostics](https://www.powershellgallery.com/packages/xDscDiagnostics/2.7.0.0) verze 2.7.0.0 nebo vyšší. Kroky instalace naleznete [v tématu Poradce při potížích s konfigurací požadovaného stavu automatizace Azure](./troubleshoot/desired-state-configuration.md).

## <a name="set-up-integration-with-azure-monitor-logs"></a>Nastavení integrace s protokoly Azure Monitoru

Chcete-li začít importovat data z konfigurace stavu azure automatizace do protokolů Azure Monitor, proveďte následující kroky:

1. Přihlaste se ke svému účtu Azure v PowerShellu. Viz [Přihlášení pomocí Azure PowerShellu](https://docs.microsoft.com/powershell/azure/authenticate-azureps).
1. Získejte ID prostředku vašeho účtu Automation spuštěním následující rutiny prostředí PowerShell. Pokud máte více než jeden účet automatizace, zvolte ID prostředku pro účet, který chcete konfigurovat.

   ```powershell
   # Find the ResourceId for the Automation account
   Get-AzResource -ResourceType 'Microsoft.Automation/automationAccounts'
   ```

1. Získejte ID prostředku pracovního prostoru Analýzy protokolů spuštěním následující rutiny prostředí PowerShell. Pokud máte více než jeden pracovní prostor, zvolte ID prostředku pro pracovní prostor, který chcete konfigurovat.

   ```powershell
   # Find the ResourceId for the Log Analytics workspace
   Get-AzResource -ResourceType 'Microsoft.OperationalInsights/workspaces'
   ```

1. Spusťte následující rutinu prostředí `<AutomationResourceId>` PowerShell, která nahradí a `<WorkspaceResourceId>` nahradí `ResourceId` hodnoty z každého z předchozích kroků.

   ```powershell
   Set-AzDiagnosticSetting -ResourceId <AutomationResourceId> -WorkspaceId <WorkspaceResourceId> -Enabled $true -Category 'DscNodeStatus'
   ```

1. Pokud chcete zastavit import dat z konfigurace stavu automatizace Azure do protokolů Azure Monitor, spusťte následující rutinu Prostředí PowerShell.

   ```powershell
   Set-AzDiagnosticSetting -ResourceId <AutomationResourceId> -WorkspaceId <WorkspaceResourceId> -Enabled $false -Category 'DscNodeStatus'
   ```

## <a name="view-the-state-configuration-logs"></a>Zobrazení protokolů konfigurace stavu

Po nastavení integrace s protokoly Azure Monitor pro vaše data konfigurace stavu automatizace, můžete zobrazit výběrem **protokoly** v části **Monitorování** v levém podokně konfigurace stavu (DSC) stránky.

![Protokoly](media/automation-dsc-diagnostics/automation-dsc-logs-toc-item.png)

Otevře se podokno Hledání protokolu s oblastí dotazu vymezenou pro prostředek účtu automatizace. Můžete prohledávat protokoly konfigurace stavu pro operace DSC hledáním v protokolech Azure Monitor. Záznamy pro operace DSC jsou `AzureDiagnostics` uloženy v tabulce. Chcete-li například najít uzly, které nejsou kompatibilní, zadejte následující dotaz.

```AzureDiagnostics
| where Category == 'DscNodeStatus' 
| where OperationName contains 'DSCNodeStatusData'
| where ResultType != 'Compliant'
```
Podrobnosti o filtrování:

* Filtrovat `DscNodeStatusData` na vrácení operací pro každý uzel konfigurace stavu.
* Filtrovat `DscResourceStatusData` na vrácení operací pro každý prostředek DSC volána v konfiguraci uzlu použít pro tento prostředek. 
* Filtrováním `DscResourceStatusData` můžete vrátit informace o chybě pro všechny prostředky DSC, které se nezdaří.

Další informace o vytváření dotazů protokolu pro hledání dat najdete [v tématu Přehled dotazů protokolu v Azure Monitoru](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview).

### <a name="send-an-email-when-a-state-configuration-compliance-check-fails"></a>Odeslání e-mailu v případě, že se nezdaří kontrola dodržování předpisů konfigurace stavu

Jedním z našich nejlepších požadavků zákazníků je možnost odeslat e-mail nebo textovou zprávu, když se něco pokazí s konfigurací DSC.

Chcete-li vytvořit pravidlo výstrahy, začněte vytvořením hledání protokolu pro záznamy sestavy konfigurace stavu, které by měly vyvolat výstrahu. Klepnutím na tlačítko **Nové pravidlo výstrah vytvořte** a nakonfigurujte pravidlo výstrahy.

1. Na stránce Přehled pracovního prostoru Analýzy protokolů klikněte na **protokoly**.
1. Vytvořte vyhledávací dotaz protokolu pro výstrahu zadáním následujícího hledání do pole dotazu:`Type=AzureDiagnostics Category='DscNodeStatus' NodeName_s='DSCTEST1' OperationName='DscNodeStatusData' ResultType='Failed'`

   Pokud jste nastavili protokoly z více než jednoho účtu automatizace nebo předplatného do pracovního prostoru, můžete seskupit výstrahy podle předplatného a účtu Automation. Odvození názvu účtu `Resource` automatizace z pole při hledání záznamů **DscNodeStatusData.**
1. Pokud chcete otevřít obrazovku **Vytvořit pravidlo,** klikněte v horní části stránky na **Nové pravidlo výstrahy.** 

Další informace o možnostech konfigurace výstrahy naleznete v [tématu Vytvoření pravidla výstrahy](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md).

### <a name="find-failed-dsc-resources-across-all-nodes"></a>Vyhledání neúspěšných prostředků DSC ve všech uzlech

Jednou z výhod použití protokolů Azure Monitor je, že můžete vyhledávat neúspěšné kontroly napříč uzly. Chcete-li najít všechny instance prostředků DSC, které se nezdařily:

1. Na stránce Přehled pracovního prostoru Analýzy protokolů klikněte na **protokoly**.
1. Vytvořte vyhledávací dotaz protokolu pro výstrahu zadáním následujícího hledání do pole dotazu:`Type=AzureDiagnostics Category='DscNodeStatus' OperationName='DscResourceStatusData' ResultType='Failed'`

### <a name="view-historical-dsc-node-status"></a>Zobrazit historický stav uzlu DSC

Chcete-li v průběhu času vizualizovat historii stavu uzlu DSC, můžete použít tento dotaz:

`Type=AzureDiagnostics ResourceProvider="MICROSOFT.AUTOMATION" Category=DscNodeStatus NOT(ResultType="started") | measure Count() by ResultType interval 1hour`

Tento dotaz zobrazuje graf stavu uzlu v čase.

## <a name="azure-monitor-logs-records"></a>Azure Monitor zaznamenává záznamy

Diagnostika Azure Automation vytvoří v protokolech Azure Monitoru dvě kategorie záznamů:

* Údaje o stavu uzlu (**DscNodeStatusData**)
* Údaje o stavu zdroje (**DscResourceStatusData**)

### <a name="dscnodestatusdata"></a>DscNodeStatusData

| Vlastnost | Popis |
| --- | --- |
| TimeGenerated |Datum a čas, kdy byla kontrola dodržování předpisů spuštěna. |
| OperationName |DscNodeStatusData. |
| Type výsledku |Určuje, zda je uzel kompatibilní. |
| NodeName_s |Název spravovaného uzlu. |
| NodeComplianceStatus_s |Určuje, zda je uzel kompatibilní. |
| DscReportStatus |Zda byla kontrola dodržování předpisů úspěšně spuštěna. |
| Konfigurační režim | Jak je konfigurace použita k uzlu. Možné hodnoty: <ul><li>`ApplyOnly`: DSC použije konfiguraci a neprovede nic dalšího, pokud není nová konfigurace posunuta do cílového uzlu nebo pokud je nová konfigurace vytažena ze serveru. Po počáteční aplikaci nové konfigurace DSC nekontroluje posun z dříve nakonfigurovaného stavu. DSC se pokusí použít konfiguraci, dokud `ApplyOnly` je úspěšná, než se hodnota projeví. </li><li>`ApplyAndMonitor`: Toto je výchozí hodnota. LCM použije všechny nové konfigurace. Po počáteční aplikaci nové konfigurace, pokud cílový uzel posune z požadovaného stavu, DSC hlásí nesoulad v protokolech. DSC se pokusí použít konfiguraci, dokud `ApplyAndMonitor` je úspěšná, než se hodnota projeví.</li><li>`ApplyAndAutoCorrect`: DSC použije všechny nové konfigurace. Po počáteční aplikaci nové konfigurace, pokud cílový uzel posune z požadovaného stavu, DSC hlásí nesoulad v protokolech a potom znovu použije aktuální konfiguraci.</li></ul> |
| HostName_s | Název spravovaného uzlu. |
| IP adresa | Adresa IPv4 spravovaného uzlu. |
| Kategorie | DscNodeStatus. |
| Prostředek | Název účtu Azure Automation. |
| Tenant_g | GUID, který identifikuje klienta pro volajícího. |
| NodeId_g |IDENTIFIKÁTOR GUID, který identifikuje spravovaný uzel. |
| DscReportId_g |IDENTIFIKÁTOR GUID, který identifikuje sestavu. |
| LastSeenTime_t |Datum a čas, kdy byla sestava naposledy zobrazena. |
| ReportStartTime_t |Datum a čas, kdy byla sestava spuštěna. |
| ReportEndTime_t |Datum a čas dokončení sestavy. |
| NumberOfResources_d |Počet prostředků DSC volaných v konfiguraci použité pro uzel. |
| SourceSystem | Jak azure monitor protokoly shromažďují data. Vždy "Azure" pro diagnostiku Azure. |
| ResourceId |Identifikátor účtu Azure Automation. |
| VýsledekPopis | Popis této operace. |
| SubscriptionId | ID předplatného Azure (GUID) pro účet automatizace. |
| ResourceGroup | Název skupiny prostředků pro účet automatizace. |
| ResourceProvider | Microsoft. Automatizace. |
| ResourceType | AUTOMATIONACCOUNTS. |
| CorrelationId |IDENTIFIKÁTOR GUID, který je identifikátorem korelace sestavy dodržování předpisů. |

### <a name="dscresourcestatusdata"></a>DscResourceStatusData

| Vlastnost | Popis |
| --- | --- |
| TimeGenerated |Datum a čas, kdy byla kontrola dodržování předpisů spuštěna. |
| OperationName |DscResourceStatusData.|
| Type výsledku |Zda je prostředek kompatibilní. |
| NodeName_s |Název spravovaného uzlu. |
| Kategorie | DscNodeStatus. |
| Prostředek | Název účtu Azure Automation. |
| Tenant_g | GUID, který identifikuje klienta pro volajícího. |
| NodeId_g |IDENTIFIKÁTOR GUID, který identifikuje spravovaný uzel. |
| DscReportId_g |IDENTIFIKÁTOR GUID, který identifikuje sestavu. |
| DscResourceId_s |Název instance prostředku DSC. |
| DscResourceName_s |Název prostředku DSC. |
| DscResourceStatus_s |Zda je prostředek DSC v souladu s předpisy. |
| DscModuleName_s |Název modulu Prostředí PowerShell, který obsahuje prostředek DSC. |
| DscModuleVersion_s |Verze modulu PowerShell, který obsahuje prostředek DSC. |
| DscConfigurationName_s |Název konfigurace použité pro uzel. |
| ErrorCode_s | Kód chyby, pokud se prostředek nezdařil. |
| ErrorMessage_s |Chybová zpráva, pokud se prostředek nezdařil. |
| DscResourceDuration_d |Čas v sekundách, který byl spuštěn prostředek DSC. |
| SourceSystem | Jak azure monitor protokoly shromažďují data. Vždy `Azure` pro diagnostiku Azure. |
| ResourceId |Určuje účet Azure Automation. |
| VýsledekPopis | Popis této operace. |
| SubscriptionId | ID předplatného Azure (GUID) pro účet automatizace. |
| ResourceGroup | Název skupiny prostředků pro účet automatizace. |
| ResourceProvider | Microsoft. Automatizace. |
| ResourceType | AUTOMATIONACCOUNTS. |
| CorrelationId |GUID, což je ID korelace sestavy dodržování předpisů. |


## <a name="next-steps"></a>Další kroky

- Přehled najdete v tématu [Konfigurace stavu automatizace Azure](automation-dsc-overview.md).
- Další informace najdete [v tématu Začínáme s azure automation state configuration](automation-dsc-getting-started.md).
- Další informace o kompilaci konfigurací DSC, abyste je mohli přiřadit k cílovým uzlům, najdete [v tématu Kompilace konfigurací v konfiguraci stavu automatizace Azure](automation-dsc-compile.md).
- Odkaz na rutinu prostředí PowerShell naleznete v tématu [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
).
- Informace o cenách najdete v [tématu Ceny konfigurace stavu Azure Automation](https://azure.microsoft.com/pricing/details/automation/).
- Příklad použití konfigurace stavu azure automatizace v kanálu průběžného nasazení najdete v [tématu průběžné nasazení pomocí konfigurace stavu automatizace Azure a Chocolatey](automation-dsc-cd-chocolatey.md).
- Další informace o tom, jak vytvářet různé vyhledávací dotazy a zkontrolovat protokoly konfigurace stavu automatizace pomocí protokolů Azure Monitor, najdete [v tématu Protokoly protokolů protokolu.](../log-analytics/log-analytics-log-searches.md)
- Další informace o protokolech Azure Monitoru a zdrojích shromažďování dat najdete [v tématu Shromažďování dat úložiště Azure v přehledu protokolů Azure Monitoru](../azure-monitor/platform/collect-azure-metrics-logs.md).
