---
title: Předávání data pro vytváření sestav k analýze protokolů Azure Automation DSC.
description: Tento článek ukazuje, jak odeslat požadovaného stavu konfigurace (DSC) data pro vytváření sestav k Log Analytics k poskytování správy a další aspekty.
services: automation
ms.service: automation
ms.component: dsc
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: d01042a02f2339f039f23d4f6e021de503dc3815
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
---
# <a name="forward-azure-automation-dsc-reporting-data-to-log-analytics"></a>Předávání data pro vytváření sestav k analýze protokolů Azure Automation DSC.

Automatizace může odesílat data stavu uzlu DSC do pracovního prostoru analýzy protokolů.  
Stav dodržování předpisů se zobrazí na portálu Azure nebo v prostředí PowerShell pro uzly a pro jednotlivé prostředky DSC v konfigurace uzlu. Pomocí analýzy protokolů můžete:

* Získat informace o dodržování předpisů pro spravované uzly a jednotlivé prostředky
* Aktivovat e-mail nebo upozornění na základě stavu dodržování předpisů
* Zápis pokročilými dotazy napříč spravované uzly
* Vazbu mezi stav dodržování předpisů v účtech Automation
* Vizualizace historii uzlu dodržování předpisů v čase

## <a name="prerequisites"></a>Požadavky

Chcete-li zahájit odesílání sestav Automation DSC k analýze protokolů, je třeba:

* Listopadu 2016 nebo novější vydání [prostředí Azure PowerShell](/powershell/azure/overview) (v2.3.0).
* Účet Azure Automation. Další informace najdete v tématu [Začínáme s Azure Automation.](automation-offering-get-started.md)
* Pracovní prostor analýzy protokolů se **automatizace a řízení** nabídky služeb. Další informace najdete v tématu [začít pracovat s analýzy protokolů](../log-analytics/log-analytics-get-started.md).
* Nejméně jeden uzel Azure Automation DSC. Další informace najdete v tématu [registrace počítačů pro správu Azure Automation DSC.](automation-dsc-onboarding.md)

## <a name="set-up-integration-with-log-analytics"></a>Nastavení integrace s analýzy protokolů

Pokud chcete začít, importování dat z Azure Automation DSC do analýzy protokolů, proveďte následující kroky:

1. Přihlaste se k účtu Azure v prostředí PowerShell. V tématu [přihlásit pomocí prostředí Azure PowerShell](https://docs.microsoft.com/powershell/azure/authenticate-azureps?view=azurermps-4.0.0)
1. Získat _ResourceId_ účtu automation spuštěním následujícího příkazu Powershellu: (Pokud máte více než jeden účet automation, zvolte _ResourceID_ pro účet, který chcete nakonfigurovat).

  ```powershell
  # Find the ResourceId for the Automation Account
  Find-AzureRmResource -ResourceType "Microsoft.Automation/automationAccounts"
  ```
1. Získat _ResourceId_ pracovního prostoru analýzy protokolů spuštěním následujícího příkazu Powershellu: (Pokud máte více než jednoho pracovního prostoru, vyberte _ResourceID_ pro pracovní prostor, kterou chcete konfigurovat).

  ```powershell
  # Find the ResourceId for the Log Analytics workspace
  Find-AzureRmResource -ResourceType "Microsoft.OperationalInsights/workspaces"
  ```
1. Spusťte následující příkaz prostředí PowerShell, nahraďte `<AutomationResourceId>` a `<WorkspaceResourceId>` s _ResourceId_ hodnoty ze všech předchozích kroků:

  ```powershell
  Set-AzureRmDiagnosticSetting -ResourceId <AutomationResourceId> -WorkspaceId <WorkspaceResourceId> -Enabled $true -Categories "DscNodeStatus"
  ```

Pokud chcete zastavit import dat z Azure Automation DSC do analýzy protokolů, spusťte následující příkaz prostředí PowerShell.

```powershell
Set-AzureRmDiagnosticSetting -ResourceId <AutomationResourceId> -WorkspaceId <WorkspaceResourceId> -Enabled $false -Categories "DscNodeStatus"
```

## <a name="view-the-dsc-logs"></a>Zobrazit protokoly DSC

Po nastavení integrace s analýzy protokolů pro data Automation DSC, **hledání protokolů** tlačítko se zobrazí na **uzly DSC** okno účtu automation. Klikněte **hledání protokolů** tlačítko Zobrazit protokoly pro data uzlu DSC.

![Tlačítko vyhledat protokolu](media/automation-dsc-diagnostics/log-search-button.png)

**Hledání protokolů** otevře se okno a zobrazí **DscNodeStatusData** operaci pro každý uzel DSC a **DscResourceStatusData** operace pro každou [prostředek DSC](https://msdn.microsoft.com/powershell/dsc/resources) volat v konfiguraci uzlu použitý pro tento uzel.

**DscResourceStatusData** operace obsahuje informace o chybě pro veškeré prostředky DSC, které se nezdařilo.

Klikněte na každou operaci v seznamu se zobrazí data pro tuto operaci.

Můžete také zobrazit protokoly [hledání v analýzy protokolů. V tématu [najít data pomocí protokolu hledání](../log-analytics/log-analytics-log-searches.md).
Zadejte následující dotaz k vyhledání protokolů DSC: `Type=AzureDiagnostics ResourceProvider="MICROSOFT.AUTOMATION" Category = "DscNodeStatus"`

Také můžete zúžit dotaz podle názvu operaci. Například: ' typ = AzureDiagnostics ResourceProvider = "MICROSOFT. Kategorie AUTOMATIZACE"="DscNodeStatus"OperationName ="DscNodeStatusData"

### <a name="send-an-email-when-a-dsc-compliance-check-fails"></a>Odeslat e-mail, když se nezdaří kontrola dodržování předpisů DSC

Jednou z našich žádostem nejvyšší zákazníků je pro možnost odesílat e-mailem nebo jako text v případě problémů s konfigurací DSC.   

Pokud chcete vytvořit pravidlo výstrahy, začněte vytvořením hledání protokolů pro záznamy sestavy DSC, které by měla vyvolat výstrahu.  Klikněte **výstraha** tlačítko Vytvořit a nakonfigurovat pravidlo výstrahy.

1. Na stránce Přehled protokolu Analytics klikněte na tlačítko **hledání protokolů**.
1. Vytvoření vyhledávací dotaz protokolu pro upozornění zadáním následujících hledání do pole dotazu:  `Type=AzureDiagnostics Category=DscNodeStatus NodeName_s=DSCTEST1 OperationName=DscNodeStatusData ResultType=Failed`

  Pokud jste nastavili protokolů z více než jeden účet Automation nebo odběr do pracovního prostoru, můžete je seskupovat vaše předplatné a účet Automation.  
  Název účtu Automation může být odvozen z pole prostředků do vyhledávání DscNodeStatusData.  
1. Chcete-li otevřít **přidat pravidlo výstrahy** obrazovky, klikněte na tlačítko **výstrah** v horní části stránky. Další informace o možnostech konfigurace upozornění najdete v tématu [výstrahy v analýzy protokolů](../log-analytics/log-analytics-alerts.md#alert-rules).

### <a name="find-failed-dsc-resources-across-all-nodes"></a>Vyhledání chybných prostředky DSC pro všechny uzly

Jednou z výhod použití analýzy protokolů je, že můžete vyhledat selhání kontroly mezi uzly.
Vyhledejte všechny instance prostředků DSC, které se nezdařilo.

1. Na stránce Přehled protokolu Analytics klikněte na tlačítko **hledání protokolů**.
1. Vytvoření vyhledávací dotaz protokolu pro upozornění zadáním následujících hledání do pole dotazu:  `Type=AzureDiagnostics Category=DscNodeStatus OperationName=DscResourceStatusData ResultType=Failed`

### <a name="view-historical-dsc-node-status"></a>Zobrazit historická DSC uzlu stav

Nakonec můžete vizualizovat váš historie stavu uzlu DSC v čase.  
Tento dotaz můžete použít k vyhledání stav váš stav uzlu DSC v čase.

`Type=AzureDiagnostics ResourceProvider="MICROSOFT.AUTOMATION" Category=DscNodeStatus NOT(ResultType="started") | measure Count() by ResultType interval 1hour`  

Tato akce zobrazí graf stavu uzlu v čase.

## <a name="log-analytics-records"></a>Záznamy služby Log Analytics

Diagnostika z Azure Automation vytvoří dvě kategorie záznamů v analýzy protokolů.

### <a name="dscnodestatusdata"></a>DscNodeStatusData

| Vlastnost | Popis |
| --- | --- |
| TimeGenerated |Datum a čas, kdy byla spuštěna kontrola dodržování zásad. |
| OperationName |DscNodeStatusData |
| ResultType |Zda je uzel kompatibilní. |
| NodeName_s |Název uzlu spravované. |
| NodeComplianceStatus_s |Zda je uzel kompatibilní. |
| DscReportStatus |Kontrola dodržování zásad jestli proběhla úspěšně. |
| ConfigurationMode | Jak se konfigurace použije k uzlu. Možné hodnoty jsou __"ApplyOnly"__,__"ApplyandMonitior"__, a __"ApplyandAutoCorrect"__. <ul><li>__ApplyOnly__: DSC aplikuje konfiguraci a další nic neprovádí, pokud je novou konfiguraci instaluje na cílovém uzlu, nebo když je novou konfiguraci vyžádat ze serveru. Po počáteční aplikaci novou konfiguraci DSC nekontroluje odlišily z dříve nakonfigurované stavu. DSC se pokusí použít konfiguraci, dokud nebude úspěšná, až poté __ApplyOnly__ projeví. </li><li> __ApplyAndMonitor__: Toto je výchozí hodnota. Všechny nové konfigurace se vztahuje LCM. Po počáteční aplikaci novou konfiguraci Pokud cílový uzel drifts z požadovaný stav sestavy DSC nesoulad mezi databází v protokolech. DSC se pokusí použít konfiguraci, dokud nebude úspěšná, až poté __ApplyAndMonitor__ projeví.</li><li>__ApplyAndAutoCorrect__: platí všechny nové konfigurace DSC. Po počáteční aplikaci novou konfiguraci Pokud cílový uzel drifts z požadovaný stav DSC sestavy nesoulad mezi databází v protokolech a pak znovu použije aktuální konfiguraci.</li></ul> |
| HostName_s | Název uzlu spravované. |
| IP adresa | Adresa IPv4 spravovaný uzel. |
| Kategorie | DscNodeStatus |
| Prostředek | Název účtu služby Azure Automation. |
| Tenant_g | Identifikátor GUID, který identifikuje klienta pro volajícího. |
| NodeId_g |Identifikátor GUID, který identifikuje spravovaný uzel. |
| DscReportId_g |Identifikátor GUID, který identifikuje sestavy. |
| LastSeenTime_t |Datum a čas, kdy byla sestava posledního zobrazení. |
| ReportStartTime_t |Datum a čas spuštění sestavy. |
| ReportEndTime_t |Datum a čas dokončení sestavy. |
| NumberOfResources_d |Počet prostředků DSC volat v konfiguraci použít k uzlu. |
| SourceSystem | Jak analýzy protokolů shromáždit data. Vždy *Azure* Azure Diagnostics. |
| ID prostředku |Určuje účet Azure Automation. |
| ResultDescription | Popis pro tuto operaci. |
| SubscriptionId | Předplatné Azure Id (GUID) pro účet služby Automation. |
| ResourceGroup | Název skupiny prostředků pro účet služby Automation. |
| ResourceProvider | MICROSOFT.AUTOMATION |
| ResourceType | AUTOMATIONACCOUNTS |
| CorrelationId |Identifikátor GUID, který se o Id korelace sestavy dodržování předpisů. |

### <a name="dscresourcestatusdata"></a>DscResourceStatusData

| Vlastnost | Popis |
| --- | --- |
| TimeGenerated |Datum a čas, kdy byla spuštěna kontrola dodržování zásad. |
| OperationName |DscResourceStatusData|
| ResultType |Zda je prostředek kompatibilní. |
| NodeName_s |Název uzlu spravované. |
| Kategorie | DscNodeStatus |
| Prostředek | Název účtu služby Azure Automation. |
| Tenant_g | Identifikátor GUID, který identifikuje klienta pro volajícího. |
| NodeId_g |Identifikátor GUID, který identifikuje spravovaný uzel. |
| DscReportId_g |Identifikátor GUID, který identifikuje sestavy. |
| DscResourceId_s |Název instance prostředků DSC. |
| DscResourceName_s |Název prostředku DSC. |
| DscResourceStatus_s |Zda je prostředek DSC v dodržování předpisů. |
| DscModuleName_s |Název modulu prostředí PowerShell, který obsahuje prostředek DSC. |
| DscModuleVersion_s |Verze modulu PowerShell, který obsahuje prostředek DSC. |
| DscConfigurationName_s |Název konfigurace použít k uzlu. |
| ErrorCode_s | Kód chyby, pokud prostředek se nezdařilo. |
| ErrorMessage_s |Chybová zpráva, pokud prostředek se nezdařilo. |
| DscResourceDuration_d |Doba v sekundách, které byly spuštěny prostředek DSC. |
| SourceSystem | Jak analýzy protokolů shromáždit data. Vždy *Azure* Azure Diagnostics. |
| ID prostředku |Určuje účet Azure Automation. |
| ResultDescription | Popis pro tuto operaci. |
| SubscriptionId | Předplatné Azure Id (GUID) pro účet služby Automation. |
| ResourceGroup | Název skupiny prostředků pro účet služby Automation. |
| ResourceProvider | MICROSOFT.AUTOMATION |
| ResourceType | AUTOMATIONACCOUNTS |
| CorrelationId |Identifikátor GUID, který se o Id korelace sestavy dodržování předpisů. |

## <a name="summary"></a>Souhrn

Odeslání dat Automation DSC k analýze protokolů, lze získat lepší přehled o stavu uzly Automation DSC podle:

* Nastavení výstrah upozornění v případě, že se vyskytl problém
* Pomocí vlastních zobrazení a vyhledávací dotazy k vizualizaci výsledky sady runbook, stav úlohy sady runbook a další související klíčové ukazatele nebo metriky.  

Log Analytics poskytuje lepší viditelnost provozní data Automation DSC a může pomoct adresu incidenty rychleji.  

## <a name="next-steps"></a>Další postup

* Další informace o tom, jak vytvořit různé vyhledávací dotazy a kontrolujte protokoly Automation DSC s analýzy protokolů najdete v tématu [hledání přihlásit analýzy protokolů](../log-analytics/log-analytics-log-searches.md)
* Další informace o používání Azure Automation DSC, najdete v části [Začínáme s Azure Automation DSC.](automation-dsc-getting-started.md)
* Další informace o analýzy protokolů a kolekci zdrojů dat naleznete v tématu [shromažďování Azure úložiště dat v přehledu analýzy protokolů](../log-analytics/log-analytics-azure-storage.md)

