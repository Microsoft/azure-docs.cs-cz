---
title: Předávat data o konfiguraci stavu přeAzure Automation do protokolů Azure Monitor
description: V tomto článku se dozvíte, jak odeslat data pro vytváření sestav požadovaných stavů (DSC) z konfigurace stavu Azure Automation, aby Azure Monitor protokoly poskytovaly další přehledy a správu.
services: automation
ms.service: automation
ms.subservice: dsc
author: bobbytreed
ms.author: robreed
ms.date: 11/06/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 6b7feb1b980054ba224173d5054907879a88cdd5
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/12/2019
ms.locfileid: "68952875"
---
# <a name="forward-azure-automation-state-configuration-reporting-data-to-azure-monitor-logs"></a>Předávat data o konfiguraci stavu přeAzure Automation do protokolů Azure Monitor

Konfigurace stavu Azure Automation uchovává data o stavu uzlu po dobu 30 dnů.
Data o stavu uzlu můžete odeslat do svého pracovního prostoru Log Analytics, pokud upřednostňujete zachování těchto dat po delší dobu.
Stav dodržování předpisů je viditelný v Azure Portal nebo pomocí PowerShellu pro uzly a pro jednotlivé prostředky DSC v konfiguracích uzlů.
Díky protokolům Azure Monitor můžete:

- Získat informace o dodržování předpisů pro spravované uzly a jednotlivé prostředky
- Aktivace e-mailu nebo výstrahy na základě stavu dodržování předpisů
- Zápis pokročilých dotazů napříč vašimi spravovanými uzly
- Korelace stavu dodržování předpisů napříč účty Automation
- Vizualizace Historie dodržování předpisů uzlů v průběhu času

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Požadavky

Chcete-li začít odesílat sestavy konfigurace stavu automatizace do Azure Monitor protokolů, budete potřebovat:

- Vydání [Azure PowerShell](/powershell/azure/overview) (v 2.3.0) v listopadu 2016 nebo novějším.
- Účet Azure Automation. Další informace najdete v tématu [Začínáme s Azure Automation](automation-offering-get-started.md)
- Pracovní prostor Log Analytics pomocí nabídky služby **Automation & Control** . Další informace najdete v tématu [Začínáme s protokoly Azure monitor](../log-analytics/log-analytics-get-started.md).
- Nejméně jeden uzel Konfigurace stavu Azure Automation. Další informace najdete v tématu věnovaném [připojování počítačů pro správu podle konfigurace stavu Azure Automation](automation-dsc-onboarding.md) .
- Modul [xDscDiagnostics](https://www.powershellgallery.com/packages/xDscDiagnostics/2.7.0.0) verze 2.7.0.0 nebo vyšší. Pokyny k instalaci najdete v tématu [zobrazení protokolů DSC v uzlu](./troubleshoot/desired-state-configuration.md#steps-to-troubleshoot-desired-state-configuration-dsc).

## <a name="set-up-integration-with-azure-monitor-logs"></a>Nastavení integrace s protokoly Azure Monitor

Pokud chcete začít importovat data z Azure Automation DSC do protokolů Azure Monitor, proveďte následující kroky:

1. Přihlaste se ke svému účtu Azure v PowerShellu. Viz [přihlášení pomocí Azure PowerShell](https://docs.microsoft.com/powershell/azure/authenticate-azureps)
1. Získejte _ResourceID_ účtu Automation spuštěním následujícího příkazu PowerShellu: (Pokud máte víc než jeden účet Automation, vyberte _ResourceID_ pro účet, který chcete nakonfigurovat).

   ```powershell
   # Find the ResourceId for the Automation Account
   Get-AzResource -ResourceType 'Microsoft.Automation/automationAccounts'
   ```

1. Pomocí následujícího příkazu PowerShellu Získejte _resourceid_ Log Analytics pracovního prostoru: (Pokud máte více než jeden pracovní prostor, vyberte _ResourceID_ pro pracovní prostor, který chcete nakonfigurovat).

   ```powershell
   # Find the ResourceId for the Log Analytics workspace
   Get-AzResource -ResourceType 'Microsoft.OperationalInsights/workspaces'
   ```

1. Spusťte následující příkaz PowerShellu a `<AutomationResourceId>` nahraďte `<WorkspaceResourceId>` hodnoty ResourceID hodnotami a hodnotou _ResourceID_ z každého z předchozích kroků:

   ```powershell
   Set-AzDiagnosticSetting -ResourceId <AutomationResourceId> -WorkspaceId <WorkspaceResourceId> -Enabled $true -Category 'DscNodeStatus'
   ```

Pokud chcete zastavit import dat z konfigurace stavu Azure Automation do protokolů Azure Monitor, spusťte následující příkaz prostředí PowerShell:

```powershell
Set-AzDiagnosticSetting -ResourceId <AutomationResourceId> -WorkspaceId <WorkspaceResourceId> -Enabled $false -Category 'DscNodeStatus'
```

## <a name="view-the-state-configuration-logs"></a>Zobrazit protokoly konfigurace stavu

Po nastavení integrace s protokoly Azure Monitor pro data konfigurace stavu automatizace se v okně **uzly DSC** v účtu Automation zobrazí tlačítko **hledání protokolu** . Kliknutím na tlačítko **hledání protokolu** zobrazíte protokoly pro data uzlu DSC.

![Tlačítko hledání protokolu](media/automation-dsc-diagnostics/log-search-button.png)

Otevře se okno **prohledávání protokolu** a zobrazí se operace **DscNodeStatusData** pro každý uzel Konfigurace stavu a operace **DscResourceStatusData** pro každý [prostředek DSC](/powershell/dsc/resources) s názvem v konfiguraci uzlu použité pro tento uzel.

Operace **DscResourceStatusData** obsahuje informace o chybě pro všechny prostředky DSC, které selhaly.

Kliknutím na jednotlivé operace v seznamu zobrazíte data pro danou operaci.

Protokoly můžete zobrazit také tak, že vyhledáte Azure Monitor protokoly.
Viz [najít data pomocí prohledávání protokolů](../log-analytics/log-analytics-log-searches.md).
Zadejte následující dotaz pro vyhledání protokolů konfigurace stavu:`Type=AzureDiagnostics ResourceProvider='MICROSOFT.AUTOMATION' Category='DscNodeStatus'`

Dotaz můžete také zúžit podle názvu operace. Příklad: `Type=AzureDiagnostics ResourceProvider='MICROSOFT.AUTOMATION' Category='DscNodeStatus' OperationName='DscNodeStatusData'`

### <a name="send-an-email-when-a-state-configuration-compliance-check-fails"></a>Odeslat e-mail, když se nepovede ověřit dodržování předpisů konfigurace stavu

Jednou z našich nejlepších žádostí o zákazníky je možnost Odeslat e-mailem nebo text v případě, že se něco pokazilo s konfigurací DSC.

Chcete-li vytvořit pravidlo výstrahy, začněte tím, že vytvoříte hledání protokolu pro záznamy sestavy konfigurace stavu, které by měly vyvolat výstrahu. Kliknutím na tlačítko **+ nové pravidlo výstrahy** vytvořte a nakonfigurujte pravidlo výstrahy.

1. Na stránce Přehled pracovního prostoru Log Analytics klikněte na **protokoly**.
1. Vytvořte dotaz hledání protokolu pro upozornění zadáním následujícího hledání do pole dotazu:`Type=AzureDiagnostics Category='DscNodeStatus' NodeName_s='DSCTEST1' OperationName='DscNodeStatusData' ResultType='Failed'`

   Pokud jste v pracovním prostoru nastavili protokoly z více než jednoho účtu Automation nebo předplatného, můžete své výstrahy seskupit podle předplatného a účtu Automation.
   Název účtu Automation se dá odvodit z pole prostředků ve vyhledávání DscNodeStatusData.
1. Obrazovku **vytvořit pravidlo** otevřete kliknutím na **+ nové pravidlo výstrahy** v horní části stránky. Další informace o možnostech konfigurace výstrahy najdete v tématu [Vytvoření pravidla výstrahy](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md).

### <a name="find-failed-dsc-resources-across-all-nodes"></a>Najít neúspěšné prostředky DSC ve všech uzlech

Jednou z výhod používání protokolů Azure Monitor je, že můžete vyhledat neúspěšné kontroly napříč uzly.
Najde všechny instance prostředků DSC, které selhaly.

1. Na stránce Přehled pracovního prostoru Log Analytics klikněte na **protokoly**.
1. Vytvořte dotaz hledání protokolu pro upozornění zadáním následujícího hledání do pole dotazu:`Type=AzureDiagnostics Category='DscNodeStatus' OperationName='DscResourceStatusData' ResultType='Failed'`

### <a name="view-historical-dsc-node-status"></a>Zobrazit historický stav uzlu DSC

Nakonec můžete chtít vizualizovat historii stavu uzlu DSC v průběhu času.
Pomocí tohoto dotazu můžete v průběhu času vyhledat stav stavu uzlu DSC.

`Type=AzureDiagnostics ResourceProvider="MICROSOFT.AUTOMATION" Category=DscNodeStatus NOT(ResultType="started") | measure Count() by ResultType interval 1hour`

Tím se zobrazí graf stavu uzlu v čase.

## <a name="azure-monitor-logs-records"></a>Záznamy protokolu Azure Monitor

Diagnostika z Azure Automation vytváří dvě kategorie záznamů v protokolech Azure Monitor.

### <a name="dscnodestatusdata"></a>DscNodeStatusData

| Vlastnost | Popis |
| --- | --- |
| TimeGenerated |Datum a čas spuštění kontroly dodržování předpisů. |
| OperationName |DscNodeStatusData |
| ResultType |Zda je uzel kompatibilní. |
| NodeName_s |Název spravovaného uzlu. |
| NodeComplianceStatus_s |Zda je uzel kompatibilní. |
| DscReportStatus |Zda byla ověření kompatibility úspěšně spuštěna. |
| ConfigurationMode | Způsob použití konfigurace na uzel. Možné hodnoty jsou __"ApplyOnly"__ , __"ApplyandMonitior"__ a __"ApplyandAutoCorrect"__ . <ul><li>__ApplyOnly__: DSC aplikuje konfiguraci a nic dalšího neprovede, pokud se nová konfigurace nevloží do cílového uzlu nebo když se ze serveru načte nová konfigurace. Po počátečním použití nové konfigurace DSC nekontroluje posun od dříve nakonfigurovaného stavu. DSC se pokusí použít konfiguraci, dokud nebude úspěšná, než se __ApplyOnly__ uplatní. </li><li> __ApplyAndMonitor__: Toto je výchozí hodnota. LCM aplikuje všechny nové konfigurace. Pokud se cílový uzel po počáteční aplikaci posune od požadovaného stavu, DSC nahlásí nesoulad v protokolech. DSC se pokusí použít konfiguraci, dokud nebude úspěšná, než se __ApplyAndMonitor__ uplatní.</li><li>__ApplyAndAutoCorrect__: DSC používá všechny nové konfigurace. Pokud se po počátečním použití nové konfigurace Posune cílový uzel od požadovaného stavu, DSC nahlásí nesoulad v protokolech a pak znovu použije aktuální konfiguraci.</li></ul> |
| HostName_s | Název spravovaného uzlu. |
| IP adresa | IPv4 adresa spravovaného uzlu. |
| Category | DscNodeStatus |
| Resource | Název Azure Automation účtu. |
| Tenant_g | Identifikátor GUID, který identifikuje tenanta pro daného volajícího. |
| NodeId_g |Identifikátor GUID, který identifikuje spravovaný uzel. |
| DscReportId_g |Identifikátor GUID, který identifikuje sestavu |
| LastSeenTime_t |Datum a čas, kdy se sestava naposledy zobrazila |
| ReportStartTime_t |Datum a čas spuštění sestavy. |
| ReportEndTime_t |Datum a čas dokončení sestavy |
| NumberOfResources_d |Počet prostředků DSC, které jsou volány v konfiguraci použité pro uzel. |
| SourceSystem | Jak Azure Monitor protokoly shromáždily data. Vždy *Azure* pro diagnostiku Azure. |
| Prostředku |Určuje Azure Automation účet. |
| ResultDescription | Popis této operace |
| SubscriptionId | ID předplatného Azure (GUID) pro účet Automation. |
| Skupina prostředků | Název skupiny prostředků pro účet Automation. |
| ResourceProvider | MICROSOFT.AUTOMATION |
| Typ prostředku | AUTOMATIONACCOUNTS |
| CorrelationId |Identifikátor GUID, který představuje ID korelace sestavy dodržování předpisů. |

### <a name="dscresourcestatusdata"></a>DscResourceStatusData

| Vlastnost | Popis |
| --- | --- |
| TimeGenerated |Datum a čas spuštění kontroly dodržování předpisů. |
| OperationName |DscResourceStatusData|
| ResultType |Zda je prostředek kompatibilní. |
| NodeName_s |Název spravovaného uzlu. |
| Category | DscNodeStatus |
| Resource | Název Azure Automation účtu. |
| Tenant_g | Identifikátor GUID, který identifikuje tenanta pro daného volajícího. |
| NodeId_g |Identifikátor GUID, který identifikuje spravovaný uzel. |
| DscReportId_g |Identifikátor GUID, který identifikuje sestavu |
| DscResourceId_s |Název instance prostředku DSC |
| DscResourceName_s |Název prostředku DSC |
| DscResourceStatus_s |Zda je prostředek DSC v souladu s předpisy. |
| DscModuleName_s |Název modulu prostředí PowerShell, který obsahuje prostředek DSC. |
| DscModuleVersion_s |Verze modulu PowerShellu obsahujícího prostředek DSC |
| DscConfigurationName_s |Název konfigurace, která se pro uzel nastavila. |
| ErrorCode_s | Kód chyby v případě selhání prostředku. |
| ErrorMessage_s |Chybová zpráva v případě neúspěchu prostředku |
| DscResourceDuration_d |Čas v sekundách, po který byl prostředek DSC spuštěný. |
| SourceSystem | Jak Azure Monitor protokoly shromáždily data. Vždy *Azure* pro diagnostiku Azure. |
| Prostředku |Určuje Azure Automation účet. |
| ResultDescription | Popis této operace |
| SubscriptionId | ID předplatného Azure (GUID) pro účet Automation. |
| Skupina prostředků | Název skupiny prostředků pro účet Automation. |
| ResourceProvider | MICROSOFT.AUTOMATION |
| Typ prostředku | AUTOMATIONACCOUNTS |
| CorrelationId |Identifikátor GUID, který představuje ID korelace sestavy dodržování předpisů. |

## <a name="summary"></a>Souhrn

Odesláním dat konfigurace stavu automatizace do Azure Monitor protokolů můžete získat lepší přehled o stavu uzlů konfigurace stavu automatizace pomocí těchto akcí:

- Nastavení výstrah, které vás upozorní, když dojde k problému
- Použití vlastních zobrazení a vyhledávacích dotazů k vizualizaci výsledků Runbooku, stavu úlohy Runbooku a dalších souvisejících klíčových ukazatelů nebo metrik.

Protokoly Azure Monitor poskytují větší provozní viditelnost dat konfigurace stavu automatizace a můžou rychleji řešit incidenty.

## <a name="next-steps"></a>Další kroky

- Přehled najdete v tématu [Konfigurace stavu Azure Automation](automation-dsc-overview.md) .
- Informace o tom, jak začít, najdete v tématu [Začínáme s konfigurací stavu Azure Automation](automation-dsc-getting-started.md) .
- Další informace o kompilaci konfigurací DSC, abyste je mohli přiřadit cílovým uzlům, najdete v tématu [kompilace konfigurací v konfiguraci stavu Azure Automation](automation-dsc-compile.md)
- Referenční informace k rutinám PowerShellu najdete v tématu [rutiny konfigurace stavu Azure Automation](/powershell/module/azurerm.automation/#automation) .
- Informace o cenách najdete v tématu [Azure Automation ceny konfigurace stavu](https://azure.microsoft.com/pricing/details/automation/) .
- Příklad použití konfigurace stavu Azure Automation v kanálu průběžného nasazování najdete v tématu průběžné [nasazování pomocí Azure Automation konfigurace stavu a čokolády](automation-dsc-cd-chocolatey.md) .
- Další informace o tom, jak vytvořit různé vyhledávací dotazy a zkontrolovat protokoly konfigurace stavu automatizace pomocí protokolů Azure Monitor, najdete [v tématu prohledávání protokolů v protokolech Azure monitor](../log-analytics/log-analytics-log-searches.md)
- Další informace o Azure Monitor protokolů a zdrojích shromažďování dat najdete [v tématu shromažďování dat služby Azure Storage v protokolech Azure monitor přehled](../azure-monitor/platform/collect-azure-metrics-logs.md) .
