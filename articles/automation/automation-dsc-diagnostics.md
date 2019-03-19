---
title: Předávání Azure Automation stavu konfigurační data pro generování sestav na protokoly Azure monitoru
description: Tento článek ukazuje, jak odeslat Desired State Configuration (DSC) data z Azure Automation stavu konfigurace na protokoly Azure monitoru pro generování sestav k poskytování dalších přehledů a správu.
services: automation
ms.service: automation
ms.subservice: dsc
author: bobbytreed
ms.author: robreed
ms.date: 11/06/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 8898280e887392591873f1fc832bfd0c105689fe
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2019
ms.locfileid: "58097282"
---
# <a name="forward-azure-automation-state-configuration-reporting-data-to-azure-monitor-logs"></a>Předávání Azure Automation stavu konfigurační data pro generování sestav na protokoly Azure monitoru

Konfigurace Azure Automation stavu uchovává data o stavu uzlu po dobu 30 dnů.
Do pracovního prostoru Log Analytics můžete odeslat data o stavu uzlu, pokud chcete tato data uchovávat po delší dobu.
Stav dodržování předpisů se zobrazí na webu Azure Portal nebo v prostředí PowerShell pro uzly a pro jednotlivé prostředky DSC v konfigurace uzlu.
Protokoly Azure Monitor vám umožní:

- Získejte informace o dodržování předpisů pro spravované uzly i jednotlivé prostředky
- Aktivace e-mailem nebo výstrahu na základě stavu dodržování předpisů
- Zápis upřesňující dotazy na spravované uzly
- Stav dodržování předpisů korelaci účty služby Automation
- Vizualizovat historii uzel dodržování předpisů v čase

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Požadavky

Aby začalo odesílat sestavy stav konfigurace Automation do protokolů Azure Monitor, budete potřebovat:

- Listopad 2016 nebo novější verze [prostředí Azure PowerShell](/powershell/azure/overview) (v2.3.0).
- Účet Azure Automation. Další informace najdete v tématu [Začínáme s Azure Automation.](automation-offering-get-started.md)
- Pracovní prostor Log Analytics se **Automation and Control** nabídky služeb. Další informace najdete v tématu [začít pracovat s protokoly Azure monitoru](../log-analytics/log-analytics-get-started.md).
- Nejméně jeden uzel konfigurace stavu služby Azure Automation. Další informace najdete v tématu [připojování počítačů pro správu podle konfigurace stavu služby Azure Automation](automation-dsc-onboarding.md)

## <a name="set-up-integration-with-azure-monitor-logs"></a>Nastavení integrace s protokoly Azure monitoru

Pokud chcete začít, import dat z Azure Automation DSC do protokolů Azure Monitor, proveďte následující kroky:

1. Přihlaste se ke svému účtu Azure v prostředí PowerShell. Zobrazit [přihlášení pomocí Azure Powershellu](https://docs.microsoft.com/powershell/azure/authenticate-azureps?view=azurermps-4.0.0)
1. Získejte _ResourceId_ vašeho účtu automation, spuštěním následujícího příkazu Powershellu: (Pokud máte více než jeden účet služby automation, zvolte _ResourceID_ pro účet, který chcete nakonfigurovat).

   ```powershell
   # Find the ResourceId for the Automation Account
   Get-AzureRmResource -ResourceType 'Microsoft.Automation/automationAccounts'
   ```

1. Získejte _ResourceId_ pracovního prostoru Log Analytics, spuštěním následujícího příkazu Powershellu: (Pokud máte více než jeden pracovní prostor, zvolte _ResourceID_ pracovního prostoru, kterou chcete nakonfigurovat).

   ```powershell
   # Find the ResourceId for the Log Analytics workspace
   Get-AzureRmResource -ResourceType 'Microsoft.OperationalInsights/workspaces'
   ```

1. Spuštěním následujícího příkazu Powershellu nahraďte `<AutomationResourceId>` a `<WorkspaceResourceId>` s _ResourceId_ hodnoty ze všech předchozích kroků:

   ```powershell
   Set-AzureRmDiagnosticSetting -ResourceId <AutomationResourceId> -WorkspaceId <WorkspaceResourceId> -Enabled $true -Categories 'DscNodeStatus'
   ```

Pokud chcete zastavit import dat z Azure Automation stavu konfigurace na protokoly Azure monitoru, spusťte následující příkaz Powershellu:

```powershell
Set-AzureRmDiagnosticSetting -ResourceId <AutomationResourceId> -WorkspaceId <WorkspaceResourceId> -Enabled $false -Categories 'DscNodeStatus'
```

## <a name="view-the-state-configuration-logs"></a>Zobrazit protokoly konfigurace stavu

Po nastavení integrace s protokoly Azure monitoru pro vaše data Stav konfigurace Automation **prohledávání protokolů** tlačítko se zobrazí na **uzly DSC** okno vašeho účtu automation. Klikněte na tlačítko **prohledávání protokolů** tlačítko k zobrazení protokolů pro data uzlu DSC.

![Tlačítko hledání protokolů](media/automation-dsc-diagnostics/log-search-button.png)

**Prohledávání protokolů** se otevře okno a objeví se **DscNodeStatusData** operace pro každý uzel konfigurace stavu a **DscResourceStatusData** operace pro každou [ Prostředek DSC](/powershell/dsc/resources) volána v konfiguraci uzlu použitý pro tento uzel.

**DscResourceStatusData** operace obsahuje informace o chybě pro veškeré prostředky DSC, které se nezdařilo.

Klikněte na každou operaci v seznamu zobrazíte data pro danou operaci.

Protokoly můžete zobrazit také tak, že v protokoly Azure monitoru.
Zobrazit [našla data pomocí prohledávání protokolů](../log-analytics/log-analytics-log-searches.md).
Zadejte následující dotaz pro vyhledání protokolů konfigurace stavu: `Type=AzureDiagnostics ResourceProvider='MICROSOFT.AUTOMATION' Category='DscNodeStatus'`

Můžete také zúžení dotazu podle názvu operace. Příklad: `Type=AzureDiagnostics ResourceProvider='MICROSOFT.AUTOMATION' Category='DscNodeStatus' OperationName='DscNodeStatusData'`

### <a name="send-an-email-when-a-state-configuration-compliance-check-fails"></a>Odeslání e-mailu, když selže kontrola dodržování předpisů konfigurace stavu

Jedním z našich požadavků důležité zákazníky je pro možnost odeslání e-mailu nebo odeslání zprávy, když dojde k nějaké chybě s konfigurací DSC.

Pokud chcete vytvořit pravidlo upozornění, začněte vytvořením prohledávání protokolu pro záznamy sestavy stav konfigurace, které by měla vyvolat výstrahu. Klikněte na tlačítko **+ nové pravidlo upozornění** tlačítko a vytvořte a nakonfigurujte pravidlo upozornění.

1. Na stránce Přehled pracovního prostoru Log Analytics, klikněte na tlačítko **protokoly**.
1. Vytvoření vyhledávací dotaz protokolu upozornění tak, že zadáte následující vyhledávání do pole dotazu:  `Type=AzureDiagnostics Category='DscNodeStatus' NodeName_s='DSCTEST1' OperationName='DscNodeStatusData' ResultType='Failed'`

   Pokud jste nastavili protokoly z více než jeden účet služby Automation nebo odběru do pracovního prostoru, můžete je seskupovat vaše předplatné a účet Automation.  
   Název účtu služby Automation může být odvozena z pole zdroje ve službě search DscNodeStatusData.  
1. Chcete-li otevřít **vytvořit pravidlo** obrazovce, klikněte na tlačítko **+ nové pravidlo upozornění** v horní části stránky. Další informace o možnostech konfigurace upozornění, najdete v části [vytvoření pravidla upozornění](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md).

### <a name="find-failed-dsc-resources-across-all-nodes"></a>Najít nevydařené zdroje DSC napříč všemi uzly

Jednou z výhod pomocí protokolů Azure Monitor je, že můžete vyhledat neúspěšné kontroly mezi uzly.
Všechny instance prostředků DSC, které se nepodařilo najít.

1. Na stránce Přehled pracovního prostoru Log Analytics, klikněte na tlačítko **protokoly**.
1. Vytvoření vyhledávací dotaz protokolu upozornění tak, že zadáte následující vyhledávání do pole dotazu:  `Type=AzureDiagnostics Category='DscNodeStatus' OperationName='DscResourceStatusData' ResultType='Failed'`

### <a name="view-historical-dsc-node-status"></a>Zobrazení historických stav uzlu DSC

Nakonec můžete chtít vizualizovat historii stav uzlu DSC v čase.  
Tento dotaz můžete použít k vyhledání pro stav stav uzlu DSC v čase.

`Type=AzureDiagnostics ResourceProvider="MICROSOFT.AUTOMATION" Category=DscNodeStatus NOT(ResultType="started") | measure Count() by ResultType interval 1hour`  

Zobrazí se graf stav uzlu v čase.

## <a name="azure-monitor-logs-records"></a>Zaznamenává údaje o Azure Monitor

Diagnostika ve službě Azure Automation vytvoří dvě kategorie záznamy v protokolech Azure Monitor.

### <a name="dscnodestatusdata"></a>DscNodeStatusData

| Vlastnost | Popis |
| --- | --- |
| TimeGenerated |Datum a čas, kdy se spustila kontrola dodržování předpisů. |
| OperationName |DscNodeStatusData |
| Hodnota resultType |Určuje, zda je uzel kompatibilní. |
| NodeName_s |Název spravovaných uzlů. |
| NodeComplianceStatus_s |Určuje, zda je uzel kompatibilní. |
| DscReportStatus |Kontrola dodržování předpisů, jestli proběhla úspěšně. |
| ConfigurationMode | Jak tato konfigurace používá k uzlu. Možné hodnoty jsou __"ApplyOnly"__,__"ApplyandMonitior"__, a __"ApplyandAutoCorrect"__. <ul><li>__ApplyOnly__: DSC aplikuje konfiguraci a neprovede žádnou další akci, pokud nová konfigurace se vloží do cílového uzlu nebo když nová konfigurace se načítají ze serveru. Po počáteční použití nové konfigurace DSC nekontroluje odchylky od dříve nakonfigurované stavu. DSC se pokusí použít konfiguraci, dokud nebude úspěšná, až poté __ApplyOnly__ projeví. </li><li> __ApplyAndMonitor__: Toto je výchozí hodnota. LCM platí všechny nové konfigurace. Po počáteční aplikaci novou konfiguraci Pokud cílový uzel drifts z požadovaného stavu sestavy DSC nesrovnalosti v protokolech. DSC se pokusí použít konfiguraci, dokud nebude úspěšná, až poté __ApplyAndMonitor__ projeví.</li><li>__ApplyAndAutoCorrect__: DSC platí všechny nové konfigurace. Po počáteční aplikaci novou konfiguraci Pokud cílový uzel drifts z požadovaného stavu DSC sestavy nesrovnalosti v protokolech a pak znovu použije aktuální konfiguraci.</li></ul> |
| HostName_s | Název spravovaných uzlů. |
| IP adresa | Adresa IPv4 spravovaných uzlů. |
| Kategorie | DscNodeStatus |
| Prostředek | Název účtu Azure Automation. |
| Tenant_g | Identifikátor GUID, který identifikuje klienta pro volajícího. |
| NodeId_g |Identifikátor GUID, který identifikuje spravovaných uzlů. |
| DscReportId_g |Identifikátor GUID, který identifikuje sestavy. |
| LastSeenTime_t |Datum a čas, kdy byl naposledy zobrazené sestavy. |
| ReportStartTime_t |Datum a čas spuštění sestavy. |
| ReportEndTime_t |Datum a čas dokončení sestavy. |
| NumberOfResources_d |Počet prostředků DSC se volá v konfiguraci použitý k uzlu. |
| SourceSystem | Jak protokoly Azure monitoru shromažďuje data. Vždy *Azure* Azure Diagnostics. |
| ResourceId |Určuje účet Azure Automation. |
| resultDescription | Popis pro tuto operaci. |
| SubscriptionId | Předplatné Azure Id (GUID) pro účet Automation. |
| ResourceGroup | Název skupiny prostředků pro účet Automation. |
| ResourceProvider | MICROSOFT.AUTOMATION |
| ResourceType | AUTOMATIONACCOUNTS |
| CorrelationId |Identifikátor GUID, který se o Id korelace sestavy dodržování předpisů. |

### <a name="dscresourcestatusdata"></a>DscResourceStatusData

| Vlastnost | Popis |
| --- | --- |
| TimeGenerated |Datum a čas, kdy se spustila kontrola dodržování předpisů. |
| OperationName |DscResourceStatusData|
| Hodnota resultType |Určuje, zda je zdroj kompatibilní. |
| NodeName_s |Název spravovaných uzlů. |
| Kategorie | DscNodeStatus |
| Prostředek | Název účtu Azure Automation. |
| Tenant_g | Identifikátor GUID, který identifikuje klienta pro volajícího. |
| NodeId_g |Identifikátor GUID, který identifikuje spravovaných uzlů. |
| DscReportId_g |Identifikátor GUID, který identifikuje sestavy. |
| DscResourceId_s |Název instance prostředku DSC. |
| DscResourceName_s |Název prostředku DSC. |
| DscResourceStatus_s |Určuje, zda je prostředek DSC v dodržování předpisů. |
| DscModuleName_s |Název modulu prostředí PowerShell, který obsahuje prostředek DSC. |
| DscModuleVersion_s |Verze modulu PowerShell, který obsahuje prostředek DSC. |
| DscConfigurationName_s |Název konfigurace použít k uzlu. |
| ErrorCode_s | Kód chyby, pokud prostředek se nepovedlo. |
| ErrorMessage_s |Chybová zpráva, pokud prostředek se nepovedlo. |
| DscResourceDuration_d |Čas v sekundách, které byly spuštěny prostředků DSC. |
| SourceSystem | Jak protokoly Azure monitoru shromažďuje data. Vždy *Azure* Azure Diagnostics. |
| ResourceId |Určuje účet Azure Automation. |
| resultDescription | Popis pro tuto operaci. |
| SubscriptionId | Předplatné Azure Id (GUID) pro účet Automation. |
| ResourceGroup | Název skupiny prostředků pro účet Automation. |
| ResourceProvider | MICROSOFT.AUTOMATION |
| ResourceType | AUTOMATIONACCOUNTS |
| CorrelationId |Identifikátor GUID, který se o Id korelace sestavy dodržování předpisů. |

## <a name="summary"></a>Souhrn

Odesláním automatizace stavu konfiguračních dat do protokolů Azure Monitor můžete získat lepší přehled o stavu vaší automatizace stavu konfigurace uzlů podle:

- Nastavení výstrah, které vás upozorní, když dojde k problému
- Použití vlastní zobrazení a vyhledávací dotazy k vizualizaci výsledků sad runbook, stav úlohy runbooku a další související klíčových indikátorů nebo metriky.  

Protokoly Azure monitoru poskytuje větší provozní viditelnost ke svým datům stav konfigurace Automation a pomůžou řešit incidenty rychleji.

## <a name="next-steps"></a>Další postup

- Přehled najdete v tématu [konfigurace stavu služby Azure Automation](automation-dsc-overview.md)
- Abyste mohli začít, najdete v článku [Začínáme s Azure Automation stavu konfigurace](automation-dsc-getting-started.md)
- Další informace o kompilaci konfigurace DSC, takže můžete je přiřadit k cílové uzly, naleznete v tématu [kompilace konfigurací v konfiguraci stavu služby Azure Automation](automation-dsc-compile.md)
- Reference k rutinám Powershellu najdete v části [rutiny Azure Automation stavu konfigurace](/powershell/module/azurerm.automation/#automation)
- Informace o cenách najdete v tématu [ceny konfigurace stavu služby Azure Automation](https://azure.microsoft.com/pricing/details/automation/)
- Příklad použití Azure Automation stav konfigurace v kanálu průběžného nasazování najdete v tématu [nepřetržité nasazení pomocí Azure Automation konfigurace stavu a Chocolatey](automation-dsc-cd-chocolatey.md)
- Další informace o tom, jak vytvářet různé vyhledávací dotazy a kontrolovat stav konfigurace Automation protokoly s protokoly Azure monitoru, naleznete v tématu [prohledávání protokolu ve službě Azure Monitor protokoly](../log-analytics/log-analytics-log-searches.md)
- Další informace o zdrojích pro shromažďování dat a protokolování Azure Monitor, naleznete v tématu [protokoly shromažďování dat úložiště Azure ve službě Azure Monitor – přehled](../azure-monitor/platform/collect-azure-metrics-logs.md)
