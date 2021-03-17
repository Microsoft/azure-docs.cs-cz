---
title: Monitorování Azure Site Recovery pomocí protokolů Azure Monitor
description: Naučte se monitorovat Azure Site Recovery pomocí protokolů Azure Monitor (Log Analytics).
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/15/2019
ms.author: raynew
ms.openlocfilehash: 308e1bcf042feb15179d32844d8c569af6166619
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100571668"
---
# <a name="monitor-site-recovery-with-azure-monitor-logs"></a>Monitorování Site Recovery s využitím protokolů Azure Monitoru

Tento článek popisuje, jak monitorovat počítače replikované pomocí Azure [Site Recovery](site-recovery-overview.md), pomocí [protokolů Azure monitor](../azure-monitor/logs/data-platform-logs.md)a [Log Analytics](../azure-monitor/logs/log-query-overview.md).

Protokoly Azure Monitor poskytují datovou platformu protokolu, která shromažďuje protokoly aktivit a prostředků spolu s dalšími daty monitorování. V protokolech Azure Monitor používáte Log Analytics k zápisu a testování dotazů protokolu a k interaktivní analýze dat protokolu. Můžete vizualizovat výsledky protokolu a dotazovat se na ně a nakonfigurovat výstrahy tak, aby na základě monitorovaných dat probíhat akce.

Pro Site Recovery můžete použít protokoly Azure Monitor, které vám pomůžou s tímto:

- **Monitoruje stav a stav Site Recovery**. Můžete například monitorovat stav replikace, stav testovacího převzetí služeb při selhání, Site Recovery události, cíle bodu obnovení (RPO) pro chráněné počítače a míry změny disků a dat.
- **Nastavte výstrahy pro Site Recovery**. Můžete například nakonfigurovat výstrahy pro stav počítače, stav testovacího převzetí služeb při selhání nebo Site Recovery stav úlohy.

Použití protokolů Azure Monitor s Site Recovery podporuje replikaci z **Azure do Azure** a **virtuální počítač VMware nebo fyzický server do replikace Azure** .

> [!NOTE]
> Chcete-li získat protokoly dat o četnosti a protokoly pro VMware a fyzické počítače, je nutné na procesový server nainstalovat agenta Microsoft Monitoring Agent. Tento Agent odesílá do pracovního prostoru protokoly replikačních počítačů. Tato funkce je k dispozici pouze pro 9,30 verze agenta mobility a vyšší.

## <a name="before-you-start"></a>Než začnete

Zde je seznam toho, co k tomu potřebujete:

- Aspoň jeden počítač chráněný v úložišti Recovery Services.
- Log Analytics pracovní prostor pro ukládání protokolů Site Recovery. [Přečtěte si o](../azure-monitor/logs/quick-create-workspace.md) nastavení pracovního prostoru.
- Základní informace o tom, jak zapisovat, spouštět a analyzovat dotazy protokolu v Log Analytics. [Přečtěte si další informace](../azure-monitor/logs/log-analytics-tutorial.md).

Než začnete, doporučujeme, abyste si přečtěte [běžné otázky týkající se monitorování](monitoring-common-questions.md) .

## <a name="configure-site-recovery-to-send-logs"></a>Konfigurace Site Recovery pro odesílání protokolů

1. V trezoru klikněte na **nastavení diagnostiky**  >  **Přidat nastavení diagnostiky**.

    ![Snímek obrazovky znázorňující možnost Přidat nastavení diagnostiky](./media/monitoring-log-analytics/add-diagnostic.png)

2. V okně **nastavení diagnostiky** zadejte název a zaškrtněte políčko **Odeslat do Log Analytics**.
3. Vyberte odběr Azure Monitor protokoly a pracovní prostor Log Analytics.
4. V přepínači vyberte **Azure Diagnostics** .
5. V seznamu protokol vyberte všechny protokoly s předponou **AzureSiteRecovery**. Pak klikněte na **OK**.

    ![Snímek obrazovky s nastavením diagnostiky](./media/monitoring-log-analytics/select-workspace.png)

Protokoly Site Recovery začnou předávat do tabulky (**AzureDiagnostics**) ve vybraném pracovním prostoru.

## <a name="configure-microsoft-monitoring-agent-on-the-process-server-to-send-churn-and-upload-rate-logs"></a>Konfigurace služby Microsoft Monitoring Agent na procesovém serveru pro odesílání četností a nahrávání protokolů

V místním prostředí můžete zachytit informace o míře četnosti změn dat a rychlost odesílání zdrojových dat pro vaše VMware/fyzické počítače. Chcete-li tuto možnost povolit, musí být na procesovém serveru nainstalována aplikace Microsoft Monitoring Agent.

1. Přejděte do pracovního prostoru Log Analytics a klikněte na **Upřesnit nastavení**.
2. Klikněte na stránku **připojené zdroje** a dále vyberte **Windows servery**.
3. Na procesovém serveru Stáhněte agenta pro Windows (64 bitů). 
4. [Získání ID a klíče pracovního prostoru](../azure-monitor/agents/log-analytics-agent.md#workspace-id-and-key)
5. [Nakonfigurovat agenta na používání protokolu TLS 1,2](../azure-monitor/agents/agent-windows.md#configure-agent-to-use-tls-12)
6. [Dokončete instalaci agenta](../azure-monitor/agents/agent-windows.md#install-agent-using-setup-wizard) poskytnutím získaného ID a klíče pracovního prostoru.
7. Po dokončení instalace přejděte do pracovního prostoru Log Analytics a klikněte na **Upřesnit nastavení**. Přejděte na **datovou** stránku a dále klikněte na **čítače výkonu systému Windows**. 
8. Kliknutím na **+** přidejte následující dva čítače s intervalem vzorkování 300 sekund:

    - ASRAnalytics(*)\SourceVmChurnRate
    - ASRAnalytics(*)\SourceVmThrpRate

Data míry změn a nahrávání začnou dodávat do pracovního prostoru.


## <a name="query-the-logs---examples"></a>Dotazování protokolů – příklady

Data z protokolů načítáte pomocí dotazů protokolu napsaných pomocí [dotazovacího jazyka Kusto](../azure-monitor/logs/get-started-queries.md). V této části najdete několik příkladů běžných dotazů, které můžete použít pro Site Recovery monitorování.

> [!NOTE]
> Některé příklady používají **replicationProviderName_s** nastavené na **A2A**. Tím se načte virtuální počítače Azure, které se replikují do sekundární oblasti Azure pomocí Site Recovery. V těchto příkladech můžete měnit **A2A** pomocí **InMageAzureV2**, pokud chcete načíst místní virtuální počítače VMware nebo fyzické servery, které se replikují do Azure pomocí Site Recovery.


### <a name="query-replication-health"></a>Dotaz na stav replikace

Tento dotaz vykreslí výsečový graf pro aktuální stav replikace všech chráněných virtuálních počítačů Azure, které jsou rozdělené do tří stavů: normální, upozornění nebo kritická.

```
AzureDiagnostics  
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s  
| project name_s , replicationHealth_s  
| summarize count() by replicationHealth_s  
| render piechart   
```
### <a name="query-mobility-service-version"></a>Verze služby mobility dotazů

Tento dotaz vykresluje výsečový graf pro virtuální počítače Azure replikované s Site Recovery, rozepsaný podle verze agenta mobility, na kterém běží.

```
AzureDiagnostics  
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s  
| project name_s , agentVersion_s  
| summarize count() by agentVersion_s  
| render piechart 
```

### <a name="query-rpo-time"></a>Čas RPO dotazu

Tento dotaz znázorňuje pruhový graf virtuálních počítačů Azure replikovaných s Site Recovery, rozepsaný podle cíle bodu obnovení (RPO): méně než 15 minut, mezi 15-30 minutami a více než 30 minutami.

```
AzureDiagnostics 
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)  
| extend RPO = case(rpoInSeconds_d <= 900, "<15Min",   
rpoInSeconds_d <= 1800, "15-30Min", ">30Min")  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s  
| project name_s , RPO  
| summarize Count = count() by RPO  
| render barchart 
```

![Snímek obrazovky znázorňující pruhový graf virtuálních počítačů Azure replikovaných s Site Recovery.](./media/monitoring-log-analytics/example1.png)

### <a name="query-site-recovery-jobs"></a>Úlohy Site Recovery dotazů

Tento dotaz načte všechny úlohy Site Recovery (pro všechny scénáře zotavení po havárii) spouštěné za posledních 72 hodin a jejich stav dokončení.

```
AzureDiagnostics  
| where Category == "AzureSiteRecoveryJobs"  
| where TimeGenerated >= ago(72h)   
| project JobName = OperationName , VaultName = Resource , TargetName = affectedResourceName_s, State = ResultType  
```

### <a name="query-site-recovery-events"></a>Dotazy Site Recovery události

Tento dotaz načte všechny události Site Recovery (pro všechny scénáře zotavení po havárii) vyvolané během posledních 72 hodin, včetně jejich závažnosti. 

```
AzureDiagnostics   
| where Category == "AzureSiteRecoveryEvents"   
| where TimeGenerated >= ago(72h)   
| project AffectedObject=affectedResourceName_s , VaultName = Resource, Description_s = healthErrors_s , Severity = Level  
```

### <a name="query-test-failover-state-pie-chart"></a>Stav testovacího převzetí služeb při selhání dotazu (výsečový graf)

Tento dotaz vykresluje výsečový graf pro stav testovacího převzetí služeb při selhání u virtuálních počítačů Azure replikovaných pomocí Site Recovery.

```
AzureDiagnostics  
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)  
| where isnotempty(failoverHealth_s) and isnotnull(failoverHealth_s)  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s  
| project name_s , Resource, failoverHealth_s  
| summarize count() by failoverHealth_s  
| render piechart 
```

### <a name="query-test-failover-state-table"></a>Stav převzetí služeb při selhání testu dotazu (tabulka)

Tento dotaz vykreslí tabulku pro stav testovacího převzetí služeb při selhání u virtuálních počítačů Azure replikovaných pomocí Site Recovery.

```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)   
| where isnotempty(failoverHealth_s) and isnotnull(failoverHealth_s)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| project VirtualMachine = name_s , VaultName = Resource , TestFailoverStatus = failoverHealth_s 
```

### <a name="query-machine-rpo"></a>Dotaz RPO počítače

Tento dotaz vykresluje graf trendu, který sleduje RPO pro určitý virtuální počítač Azure (ContosoVM123) za posledních 72 hodin.

```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where TimeGenerated > ago(72h)  
| where isnotempty(name_s) and isnotnull(name_s)   
| where name_s == "ContosoVM123"  
| project TimeGenerated, name_s , RPO_in_seconds = rpoInSeconds_d   
| render timechart 
```
![Snímek obrazovky trendového grafu sledujícího cíl bodu obnovení konkrétního virtuálního počítače Azure](./media/monitoring-log-analytics/example2.png)

### <a name="query-data-change-rate-churn-and-upload-rate-for-an-azure-vm"></a>Četnost změn dat dotazů a rychlost nahrávání pro virtuální počítač Azure

Tento dotaz vykreslí graf trendu pro konkrétní virtuální počítač Azure (ContosoVM123), který představuje rychlost změny dat (bajty zápisu za sekundu) a rychlost nahrávání dat. 

```
AzureDiagnostics   
| where Category in ("AzureSiteRecoveryProtectedDiskDataChurn", "AzureSiteRecoveryReplicationDataUploadRate")   
| extend CategoryS = case(Category contains "Churn", "DataChurn",   
Category contains "Upload", "UploadRate", "none")  
| extend InstanceWithType=strcat(CategoryS, "_", InstanceName_s)   
| where TimeGenerated > ago(24h)   
| where InstanceName_s startswith "ContosoVM123"   
| project TimeGenerated , InstanceWithType , Churn_MBps = todouble(Value_s)/1048576   
| render timechart  
```
![snímek obrazovky trendového grafu pro konkrétní virtuální počítač Azure](./media/monitoring-log-analytics/example3.png)

### <a name="query-data-change-rate-churn-and-upload-rate-for-a-vmware-or-physical-machine"></a>Četnost změn dat dotazů a rychlost nahrávání pro VMware nebo fyzický počítač

> [!Note]
> Ujistěte se, že jste na procesovém serveru nastavili agenta monitorování, abyste tyto protokoly načetli. [Postup konfigurace agenta monitorování](#configure-microsoft-monitoring-agent-on-the-process-server-to-send-churn-and-upload-rate-logs)najdete v tématu.

Tento dotaz vykreslí graf trendu pro určitý disk **Disk0** replikované položky **Win-9r7sfh9qlru**, která představuje rychlost změny dat (bajty zápisu za sekundu) a rychlost nahrávání dat. V okně s názvem disku v replikované položce v trezoru služby Recovery Services **najdete okno název** disku. Název instance, který se má použít v dotazu, je název DNS počítače následovaný znakem _ a názvem disku, jako v tomto příkladu.

```
Perf
| where ObjectName == "ASRAnalytics"
| where InstanceName contains "win-9r7sfh9qlru_disk0"
| where TimeGenerated >= ago(4h) 
| project TimeGenerated ,CounterName, Churn_MBps = todouble(CounterValue)/5242880 
| render timechart
```
Procesový Server doručí tato data každých 5 minut do pracovního prostoru Log Analytics. Tyto datové body udávají průměr vypočítaný po dobu 5 minut.

### <a name="query-disaster-recovery-summary-azure-to-azure"></a>Dotaz na souhrn zotavení po havárii (Azure do Azure)

Tento dotaz vykreslí souhrnnou tabulku pro virtuální počítače Azure replikované do sekundární oblasti Azure.  Zobrazuje název virtuálního počítače, replikaci a stav ochrany, RPO, stav převzetí služeb při selhání, verzi agenta mobility, všechny aktivní chyby replikace a zdrojové umístění.

```
AzureDiagnostics 
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| project VirtualMachine = name_s , Vault = Resource , ReplicationHealth = replicationHealth_s, Status = protectionState_s, RPO_in_seconds = rpoInSeconds_d, TestFailoverStatus = failoverHealth_s, AgentVersion = agentVersion_s, ReplicationError = replicationHealthErrors_s, SourceLocation = primaryFabricName_s 
```

### <a name="query-disaster-recovery-summary-vmwarephysical-servers"></a>Dotaz na souhrn zotavení po havárii (VMware/fyzické servery)

Tento dotaz vykreslí souhrnnou tabulku pro virtuální počítače VMware a fyzické servery replikované do Azure.  Zobrazuje název počítače, replikaci a stav ochrany, cíl obnovení, stav převzetí služeb při selhání, verzi agenta mobility, všechny aktivní chyby replikace a příslušný procesový Server.

```
AzureDiagnostics  
| where replicationProviderName_s == "InMageAzureV2"   
| where isnotempty(name_s) and isnotnull(name_s)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| project VirtualMachine = name_s , Vault = Resource , ReplicationHealth = replicationHealth_s, Status = protectionState_s, RPO_in_seconds = rpoInSeconds_d, TestFailoverStatus = failoverHealth_s, AgentVersion = agentVersion_s, ReplicationError = replicationHealthErrors_s, ProcessServer = processServerName_g  
```

## <a name="set-up-alerts---examples"></a>Nastavení upozornění – příklady

Můžete nastavit Site Recovery výstrahy na základě Azure Monitor dat. [Přečtěte si další informace](../azure-monitor/alerts/alerts-log.md#create-a-log-alert-rule-with-the-azure-portal) o nastavení upozornění protokolu. 

> [!NOTE]
> Některé příklady používají **replicationProviderName_s** nastavené na **A2A**. Tím se nastaví výstrahy pro virtuální počítače Azure, které se replikují do sekundární oblasti Azure. V těchto příkladech můžete měnit **A2A** pomocí **InMageAzureV2** , pokud chcete nastavit upozornění pro místní virtuální počítače VMware nebo fyzické servery replikované do Azure.

### <a name="multiple-machines-in-a-critical-state"></a>Více počítačů v kritickém stavu

Nastavte výstrahu, pokud se více než 20 replikovaných virtuálních počítačů Azure přejdou do kritického stavu.

```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where replicationHealth_s == "Critical"  
| where isnotempty(name_s) and isnotnull(name_s)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| summarize count() 
```
Pro tuto výstrahu nastavte **mezní hodnotu** na 20.

### <a name="single-machine-in-a-critical-state"></a>Jeden počítač v kritickém stavu

Nastavte výstrahu, pokud konkrétní replikovaný virtuální počítač Azure přejde do kritického stavu.

```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where replicationHealth_s == "Critical"  
| where name_s == "ContosoVM123"  
| where isnotempty(name_s) and isnotnull(name_s)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| summarize count()  
```
U pole výstraha nastavte **mezní hodnotu** na 1.

### <a name="multiple-machines-exceed-rpo"></a>Více počítačů překročilo RPO

Nastavte výstrahu v případě, že RPO pro více než 20 virtuálních počítačů Azure přesáhne 30 minut.
```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)   
| where rpoInSeconds_d > 1800  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| project name_s , rpoInSeconds_d   
| summarize count()  
```
Pro tuto výstrahu nastavte **mezní hodnotu** na 20.

### <a name="single-machine-exceeds-rpo"></a>Jeden počítač přesahuje RPO

Nastavte výstrahu, pokud RPO pro jeden virtuální počítač Azure přesáhne 30 minut.

```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)   
| where name_s == "ContosoVM123"  
| where rpoInSeconds_d > 1800  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| project name_s , rpoInSeconds_d   
| summarize count()  
```
U pole výstraha nastavte **mezní hodnotu** na 1.

### <a name="test-failover-for-multiple-machines-exceeds-90-days"></a>Testovací převzetí služeb při selhání pro více počítačů překračuje 90 dní.

Nastavte výstrahu, pokud poslední úspěšné testovací převzetí služeb při selhání bylo více než 90 dní, pro více než 20 virtuálních počítačů. 

```
AzureDiagnostics  
| where replicationProviderName_s == "A2A"   
| where Category == "AzureSiteRecoveryReplicatedItems"  
| where isnotempty(name_s) and isnotnull(name_s)   
| where lastSuccessfulTestFailoverTime_t <= ago(90d)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| summarize count()  
```
Pro tuto výstrahu nastavte **mezní hodnotu** na 20.

### <a name="test-failover-for-single-machine-exceeds-90-days"></a>Testovací převzetí služeb při selhání pro jeden počítač překračuje 90 dní.

Nastavte výstrahu v případě, že poslední úspěšné testovací převzetí služeb při selhání pro určitý virtuální počítač bylo před více než 90 dny.
```
AzureDiagnostics  
| where replicationProviderName_s == "A2A"   
| where Category == "AzureSiteRecoveryReplicatedItems"  
| where isnotempty(name_s) and isnotnull(name_s)   
| where lastSuccessfulTestFailoverTime_t <= ago(90d)   
| where name_s == "ContosoVM123"  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| summarize count()  
```
U pole výstraha nastavte **mezní hodnotu** na 1.

### <a name="site-recovery-job-fails"></a>Úloha Site Recovery se nezdařila

Nastavte výstrahu v případě, že úloha Site Recovery (v tomto případě se úloha opětovné ochrany) neprojde jakýmkoli Site Recoverymu scénáři během posledního dne. 
```
AzureDiagnostics   
| where Category == "AzureSiteRecoveryJobs"   
| where OperationName == "Reprotect"  
| where ResultType == "Failed"  
| summarize count()  
```

Pro tuto výstrahu nastavte **prahovou hodnotu** na 1 a **periodu** na 1440 minut, abyste zkontrolovali selhání za poslední den.

## <a name="next-steps"></a>Další kroky

[Přečtěte si informace o](site-recovery-monitor-and-troubleshoot.md) sestaveném Site Recovery monitoring.