---
title: Integrace s protokoly Azure Monitor
description: V tomto článku se dozvíte, jak odeslat údaje o konfiguraci požadovaného stavu z konfigurace stavu Azure Automation do protokolů Azure Monitor.
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.date: 11/06/2018
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
manager: carmonm
ms.openlocfilehash: 8952ea87cfd9317225ecb9e313174f8d1fe8e519
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2021
ms.locfileid: "107834799"
---
# <a name="integrate-with-azure-monitor-logs"></a>Integrace s protokoly Azure Monitor

Konfigurace stavu Azure Automation uchovává data o stavu uzlu po dobu 30 dnů. Data o stavu uzlu můžete odeslat do svého pracovního prostoru Log Analytics, pokud upřednostňujete zachování těchto dat po delší dobu. Stav dodržování předpisů je viditelný v Azure Portal nebo pomocí PowerShellu pro uzly a pro jednotlivé prostředky DSC v konfiguracích uzlů. 

Protokoly Azure Monitor poskytují větší provozní viditelnost dat konfigurace stavu automatizace a můžou rychleji řešit incidenty. Díky protokolům Azure Monitor můžete:

- Získejte informace o dodržování předpisů pro spravované uzly a jednotlivé prostředky.
- Aktivace e-mailu nebo výstrahy na základě stavu dodržování předpisů.
- Zapište rozšířené dotazy napříč vašimi spravovanými uzly.
- Korelovat stav dodržování předpisů napříč účty Automation.
- Pomocí vlastních zobrazení a vyhledávacích dotazů můžete vizualizovat výsledky Runbooku, stav úlohy Runbooku a další související klíčové ukazatele nebo metriky.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Požadavky

Chcete-li začít odesílat sestavy konfigurace stavu automatizace do Azure Monitor protokolů, budete potřebovat:

- Vydání [Azure PowerShell](/powershell/azure/) (v 2.3.0) v listopadu 2016 nebo novějším.
- Účet Azure Automation. Další informace najdete v [úvodu k Azure Automation](automation-intro.md).
- Pracovní prostor Log Analytics pomocí nabídky služby Automation & Control. Další informace najdete v tématu [Začínáme s Log Analytics v Azure monitor](../azure-monitor/logs/log-analytics-tutorial.md).
- Nejméně jeden uzel Konfigurace stavu Azure Automation. Další informace najdete v tématu věnovaném [připojování počítačů pro správu podle konfigurace stavu Azure Automation](automation-dsc-onboarding.md).
- Modul [xDscDiagnostics](https://www.powershellgallery.com/packages/xDscDiagnostics/2.7.0.0) verze 2.7.0.0 nebo vyšší. Pokyny k instalaci najdete v tématu [řešení potíží Azure Automation konfiguraci požadovaného stavu](./troubleshoot/desired-state-configuration.md).

## <a name="set-up-integration-with-azure-monitor-logs"></a>Nastavení integrace s protokoly Azure Monitor

Pokud chcete začít importovat data z konfigurace stavu Azure Automation do protokolů Azure Monitor, proveďte následující kroky:

1. Přihlaste se ke svému účtu Azure v PowerShellu. Přečtěte si téma [přihlášení pomocí Azure PowerShell](/powershell/azure/authenticate-azureps).
1. ID prostředku účtu Automation získáte spuštěním následující rutiny prostředí PowerShell. Pokud máte více než jeden účet Automation, vyberte ID prostředku pro účet, který chcete konfigurovat.

   ```powershell
   # Find the ResourceId for the Automation account
   Get-AzResource -ResourceType 'Microsoft.Automation/automationAccounts'
   ```

1. Spusťte následující rutinu prostředí PowerShell a Získejte ID prostředku Log Analytics pracovního prostoru. Pokud máte více než jeden pracovní prostor, vyberte ID prostředku pro pracovní prostor, který chcete konfigurovat.

   ```powershell
   # Find the ResourceId for the Log Analytics workspace
   Get-AzResource -ResourceType 'Microsoft.OperationalInsights/workspaces'
   ```

1. Spusťte následující rutinu prostředí PowerShell a nahraďte `<AutomationResourceId>` a `<WorkspaceResourceId>` `ResourceId` hodnotami z každého z předchozích kroků.

   ```powershell
   Set-AzDiagnosticSetting -ResourceId <AutomationResourceId> -WorkspaceId <WorkspaceResourceId> -Enabled $true -Category 'DscNodeStatus'
   ```

1. Pokud chcete zastavit import dat z konfigurace stavu Azure Automation do protokolů Azure Monitor, spusťte následující rutinu prostředí PowerShell.

   ```powershell
   Set-AzDiagnosticSetting -ResourceId <AutomationResourceId> -WorkspaceId <WorkspaceResourceId> -Enabled $false -Category 'DscNodeStatus'
   ```

## <a name="view-the-state-configuration-logs"></a>Zobrazit protokoly konfigurace stavu

Po nastavení integrace s protokoly Azure Monitor pro data konfigurace stavu automatizace je můžete zobrazit výběrem možnosti **protokoly** v části **monitorování** v levém podokně stránky Konfigurace stavu (DSC).

![Protokoly](media/automation-dsc-diagnostics/automation-dsc-logs-toc-item.png)

Otevře se podokno prohledávání protokolu s oblastí dotazu vymezenou pro váš prostředek účtu Automation. Protokoly konfigurace stavu pro operace DSC můžete vyhledat hledáním v Azure Monitor protokoly. Záznamy o operacích DSC jsou uložené v `AzureDiagnostics` tabulce. Pokud například chcete najít uzly, které nedodržují předpisy, zadejte následující dotaz.

```AzureDiagnostics
| where Category == 'DscNodeStatus' 
| where OperationName contains 'DSCNodeStatusData'
| where ResultType != 'Compliant'
```

Podrobnosti filtrování:

* Filtrujte na `DscNodeStatusData` vrácení operací pro každý uzel Konfigurace stavu.
* Filtrujte na `DscResourceStatusData` vrácení operací pro každý prostředek DSC s názvem v konfiguraci uzlu použité pro daný prostředek. 
* `DscResourceStatusData`Pokud chcete vrátit informace o chybách pro všechny prostředky DSC, které selžou, vyfiltrujte.

Další informace o vytváření dotazů protokolu pro hledání dat najdete v tématu [Přehled dotazů protokolu v Azure monitor](../azure-monitor/logs/log-query-overview.md).

### <a name="send-an-email-when-a-state-configuration-compliance-check-fails"></a>Odeslat e-mail, když se nepovede ověřit dodržování předpisů konfigurace stavu

Jednou z našich nejlepších žádostí o zákazníky je možnost Odeslat e-mailem nebo text v případě, že se něco pokazilo s konfigurací DSC.

Chcete-li vytvořit pravidlo výstrahy, začněte tím, že vytvoříte hledání protokolu pro záznamy sestavy konfigurace stavu, které by měly vyvolat výstrahu. Kliknutím na tlačítko **nové pravidlo výstrahy** vytvoříte a nakonfigurujete pravidlo výstrahy.

1. Na stránce Přehled pracovního prostoru Log Analytics klikněte na **protokoly**.
1. Vytvořte dotaz hledání protokolu pro upozornění zadáním následujícího hledání do pole dotazu:  `Type=AzureDiagnostics Category='DscNodeStatus' NodeName_s='DSCTEST1' OperationName='DscNodeStatusData' ResultType='Failed'`

   Pokud jste v pracovním prostoru nastavili protokoly z více než jednoho účtu Automation nebo předplatného, můžete své výstrahy seskupit podle předplatného a účtu Automation. `Resource`V poli hledání záznamů odvodit název účtu Automation `DscNodeStatusData` .
1. Obrazovku **vytvořit pravidlo** otevřete kliknutím na **nové pravidlo výstrahy** v horní části stránky. 

Další informace o možnostech konfigurace výstrahy najdete v tématu [Vytvoření pravidla výstrahy](../azure-monitor/alerts/alerts-metric.md).

### <a name="find-failed-dsc-resources-across-all-nodes"></a>Najít neúspěšné prostředky DSC ve všech uzlech

Jednou z výhod používání protokolů Azure Monitor je, že můžete vyhledat neúspěšné kontroly napříč uzly. Pokud chcete najít všechny instance prostředků DSC, které selhaly:

1. Na stránce Přehled pracovního prostoru Log Analytics klikněte na **protokoly**.
1. Vytvořte dotaz hledání protokolu pro upozornění zadáním následujícího hledání do pole dotazu:  `Type=AzureDiagnostics Category='DscNodeStatus' OperationName='DscResourceStatusData' ResultType='Failed'`

### <a name="view-historical-dsc-node-status"></a>Zobrazit historický stav uzlu DSC

K vizualizaci historie stavu uzlu DSC v průběhu času můžete použít tento dotaz:

`Type=AzureDiagnostics ResourceProvider="MICROSOFT.AUTOMATION" Category=DscNodeStatus NOT(ResultType="started") | measure Count() by ResultType interval 1hour`

Tento dotaz zobrazí graf stavu uzlu v čase.

## <a name="azure-monitor-logs-records"></a>Záznamy protokolu Azure Monitor

Diagnostika Azure Automation v protokolech Azure Monitor vytvořit dvě kategorie záznamů:

* Data o stavu uzlu ( `DscNodeStatusData` )
* Data stavu prostředku ( `DscResourceStatusData` )

### <a name="dscnodestatusdata"></a>DscNodeStatusData

| Vlastnost | Popis |
| --- | --- |
| TimeGenerated |Datum a čas spuštění kontroly dodržování předpisů. |
| OperationName |`DscNodeStatusData`. |
| ResultType |Hodnota, která označuje, zda je uzel kompatibilní. |
| NodeName_s |Název spravovaného uzlu. |
| NodeComplianceStatus_s |Hodnota stavu, která určuje, zda je uzel kompatibilní. |
| DscReportStatus |Hodnota stavu označující, zda byla kontroly dodržování předpisů úspěšně spuštěna. |
| ConfigurationMode | Režim použitý k aplikování konfigurace na uzel. Možné hodnoty: <ul><li>`ApplyOnly`: DSC aplikuje konfiguraci a nedělá nic dalšího, pokud se do cílového uzlu nevloží nová konfigurace nebo když se ze serveru načte nová konfigurace. Po počátečním použití nové konfigurace DSC nekontroluje posun od dříve nakonfigurovaného stavu. DSC se pokusí použít konfiguraci, dokud není úspěšná, než se `ApplyOnly` hodnota uplatní. </li><li>`ApplyAndMonitor`: Toto je výchozí hodnota. LCM aplikuje všechny nové konfigurace. Pokud se cílový uzel po počáteční aplikaci posune od požadovaného stavu, DSC nahlásí nesoulad v protokolech. DSC se pokusí použít konfiguraci, dokud není úspěšná, než se `ApplyAndMonitor` hodnota uplatní.</li><li>`ApplyAndAutoCorrect`: DSC aplikuje všechny nové konfigurace. Pokud se po počátečním použití nové konfigurace Posune cílový uzel od požadovaného stavu, DSC nahlásí nesoulad v protokolech a pak znovu použije aktuální konfiguraci.</li></ul> |
| HostName_s | Název spravovaného uzlu. |
| IPAddress | IPv4 adresa spravovaného uzlu. |
| Kategorie | `DscNodeStatus`. |
| Prostředek | Název Azure Automation účtu. |
| Tenant_g | Identifikátor GUID, který identifikuje tenanta pro daného volajícího. |
| NodeId_g | Identifikátor GUID, který identifikuje spravovaný uzel. |
| DscReportId_g | Identifikátor GUID, který identifikuje sestavu |
| LastSeenTime_t | Datum a čas, kdy se sestava naposledy zobrazila |
| ReportStartTime_t | Datum a čas spuštění sestavy. |
| ReportEndTime_t | Datum a čas dokončení sestavy |
| NumberOfResources_d | Počet prostředků DSC, které jsou volány v konfiguraci použité pro uzel. |
| SourceSystem | Zdrojový systém, který označuje, jak Azure Monitor protokoly shromáždily data. Vždy `Azure` pro diagnostiku Azure. |
| ResourceId |Identifikátor prostředku Azure Automation účtu. |
| ResultDescription | Popis prostředku pro tuto operaci. |
| SubscriptionId | ID předplatného Azure (GUID) pro účet Automation. |
| ResourceGroup | Název skupiny prostředků pro účet Automation. |
| ResourceProvider | Microsoft. Automation. |
| ResourceType | AUTOMATIONACCOUNTS. |
| CorrelationId | Identifikátor GUID, který představuje korelační identifikátor sestavy dodržování předpisů. |

### <a name="dscresourcestatusdata"></a>DscResourceStatusData

| Vlastnost | Popis |
| --- | --- |
| TimeGenerated |Datum a čas spuštění kontroly dodržování předpisů. |
| OperationName |`DscResourceStatusData`.|
| ResultType |Zda je prostředek kompatibilní. |
| NodeName_s |Název spravovaného uzlu. |
| Kategorie | DscNodeStatus. |
| Prostředek | Název Azure Automation účtu. |
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
| SourceSystem | Jak Azure Monitor protokoly shromáždily data. Vždy `Azure` pro diagnostiku Azure. |
| ResourceId |Identifikátor účtu Azure Automation. |
| ResultDescription | Popis této operace |
| SubscriptionId | ID předplatného Azure (GUID) pro účet Automation. |
| ResourceGroup | Název skupiny prostředků pro účet Automation. |
| ResourceProvider | Microsoft. Automation. |
| ResourceType | AUTOMATIONACCOUNTS. |
| CorrelationId |Identifikátor GUID, který představuje ID korelace sestavy dodržování předpisů. |

## <a name="next-steps"></a>Další kroky

- Přehled najdete v tématu [Přehled konfigurace stavu Azure Automation](automation-dsc-overview.md).
- Informace o tom, jak začít, najdete v tématu Začínáme [s konfigurací stavu Azure Automation](automation-dsc-getting-started.md).
- Další informace o kompilaci konfigurací DSC, abyste je mohli přiřadit cílovým uzlům, najdete v tématu [kompilace konfigurací DSC v konfiguraci stavu Azure Automation](automation-dsc-compile.md).
- Referenční informace k rutinám PowerShellu najdete v tématu [AZ. Automation](/powershell/module/az.automation).
- Informace o cenách najdete v tématu [Azure Automation ceny konfigurace stavu](https://azure.microsoft.com/pricing/details/automation/).
- Příklad použití konfigurace stavu Azure Automation v kanálu nepřetržitého nasazení najdete v tématu [Nastavení průběžného nasazování s čokoládou](automation-dsc-cd-chocolatey.md).
- Další informace o tom, jak vytvořit různé vyhledávací dotazy a zkontrolovat protokoly konfigurace stavu automatizace pomocí protokolů Azure Monitor, najdete [v tématu prohledávání protokolů v protokolech Azure monitor](../azure-monitor/logs/log-query-overview.md).
- Další informace o Azure Monitor protokolů a zdrojích shromažďování dat najdete [v tématu shromažďování dat služby Azure Storage v protokolech Azure monitor přehled](../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace).
