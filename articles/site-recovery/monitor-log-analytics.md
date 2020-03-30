---
title: Monitorování azure site recovery pomocí protokolů monitorování Azure
description: Zjistěte, jak monitorovat Azure Site Recovery pomocí protokolů monitorování Azure (Log Analytics)
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/15/2019
ms.author: raynew
ms.openlocfilehash: f20d0d38a7fbd831d3e97a69373bac04b9b330aa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74133413"
---
# <a name="monitor-site-recovery-with-azure-monitor-logs"></a>Monitorování Site Recovery s využitím protokolů Azure Monitoru

Tento článek popisuje, jak sledovat počítače replikované azure [site recovery](site-recovery-overview.md), pomocí [protokolů monitorování Azure](../azure-monitor/platform/data-platform-logs.md)a Log [Analytics](../azure-monitor/log-query/log-query-overview.md).

Protokoly monitorování Azure poskytují datovou platformu protokolu, která shromažďuje protokoly aktivity a diagnostiky spolu s dalšími daty monitorování. V rámci protokolů monitorování Azure používáte analýzu protokolů k zápisu a testování dotazů protokolu a k interaktivní analýze dat protokolu. Můžete vizualizovat a dotazovat výsledky protokolu a nakonfigurovat výstrahy tak, aby akce na základě monitorovaných dat.

Pro site recovery můžete protokoly monitorování Azure, které vám pomohou provést následující akce:

- **Sledování stavu a stavu obnovení webu**. Můžete například sledovat stav replikace, testovat stav převzetí služeb při selhání, události obnovení lokality, cíle bodů obnovení (RPO) pro chráněné počítače a rychlost výměny disků/dat.
- **Nastavte výstrahy pro obnovení webu**. Můžete například nakonfigurovat výstrahy pro stav počítače, test stavu převzetí služeb při selhání nebo stav úlohy obnovení lokality.

Použití protokolů monitorování Azure s obnovením lokality se podporuje pro replikaci **Azure to Azure** a pro replikaci **Virtuálního počítače VM ware a na replikaci Azure.**

> [!NOTE]
> Chcete-li získat protokoly dat konve a protokoly rychlosti odesílání pro vmware a fyzické počítače, je třeba nainstalovat agenta monitorování společnosti Microsoft na procesní mši. Tento agent odesílá protokoly replikačních počítačů do pracovního prostoru. Tato funkce je k dispozici pouze pro verzi agenta mobility 9.30.

## <a name="before-you-start"></a>Než začnete

Zde je seznam toho, co k tomu potřebujete:

- Alespoň jeden počítač chráněný v trezoru služby Recovery Services.
- Pracovní prostor Analýzy protokolů pro ukládání protokolů obnovení webu. [Přečtěte si o](../azure-monitor/learn/quick-create-workspace.md) nastavení pracovního prostoru.
- Základní znalosti o tom, jak psát, spouštět a analyzovat dotazy protokolu v Log Analytics. [Další informace](../azure-monitor/log-query/get-started-portal.md).

Doporučujeme, abyste si před zahájením prověřili [běžné otázky týkající se monitorování.](monitoring-common-questions.md)

## <a name="configure-site-recovery-to-send-logs"></a>Konfigurace obnovení webu pro odesílání protokolů

1. V úschovně klikněte na **Nastavení** > diagnostiky Přidat diagnostické**nastavení**.

    ![Výběr diagnostického protokolování](./media/monitoring-log-analytics/add-diagnostic.png)

2. V **nastavení diagnostiky**zadejte název a zaškrtněte políčko **Odeslat do analýzy protokolů**.
3. Vyberte předplatné protokolů monitorování Azure a pracovní prostor Log Analytics.
4. V přepínači vyberte **Diagnostika Azure.**
5. Ze seznamu protokolů vyberte všechny protokoly s předponou **AzureSiteRecovery**. Pak klikněte na **OK**.

    ![Výběr pracovního prostoru](./media/monitoring-log-analytics/select-workspace.png)

Protokoly obnovení webu se začnou podávat do tabulky (**AzureDiagnostics**) ve vybraném pracovním prostoru.

## <a name="configure-microsoft-monitoring-agent-on-the-process-server-to-send-churn-and-upload-rate-logs"></a>Konfigurace agenta monitorování společnosti Microsoft na procesním serveru za účelem odesílání protokolů rychlosti změn a odesílání

Informace o rychlosti konve dat a informace o rychlosti odesílání dat zdrojových dat pro vaše počítače VMware/fyzické počítače můžete zachytit v místním prostředí. Chcete-li to povolit, je nutné nainstalovat na procesní server agenta společnosti Microsoft.

1. Přejděte do pracovního prostoru Log Analytics a klikněte na **Upřesnit nastavení**.
2. Klikněte na stránku **Připojené zdroje** a dále vyberte **windows servery**.
3. Stáhněte si agenta Windows (64 bit) na procesním serveru. 
4. [Získání ID a klíče pracovního prostoru](../azure-monitor/platform/agent-windows.md#obtain-workspace-id-and-key)
5. [Konfigurace agenta pro použití protokolu TLS 1.2](../azure-monitor/platform/agent-windows.md#configure-agent-to-use-tls-12)
6. [Dokončete instalaci agenta](../azure-monitor/platform/agent-windows.md#install-the-agent-using-setup-wizard) poskytnutím získaného ID pracovního prostoru a klíče.
7. Po dokončení instalace přejděte do pracovního prostoru Log Analytics a klikněte na **Upřesnit nastavení**. Přejděte na stránku **Data** a dále klikněte na **čítače výkonu systému Windows**. 
8. Kliknutím na **'+'** přidáte následující dva čítače s intervalem vzorkování 300 sekund:

        ASRAnalytics(*)\SourceVmChurnRate 
        ASRAnalytics(*)\SourceVmThrpRate 

Data rychlosti změn a nahrávání se začnou připájet do pracovního prostoru.


## <a name="query-the-logs---examples"></a>Dotaz na protokoly - příklady

Data z protokolů načítáte pomocí dotazů protokolů napsaných [pomocí dotazovacího jazyka Kusto](../azure-monitor/log-query/get-started-queries.md). Tato část obsahuje několik příkladů běžných dotazů, které můžete použít pro monitorování site recovery.

> [!NOTE]
> Některé příklady používají **replicationProviderName_s** nastaveny na **A2A**. Tím se načte virtuální počítače Azure, které se replikují do sekundární oblasti Azure pomocí site recovery. V těchto příkladech můžete nahradit **A2A** **inMageAzureV2**, pokud chcete načíst místní virtuální počítače VMware nebo fyzické servery, které jsou replikovány do Azure pomocí site recovery.


### <a name="query-replication-health"></a>Stav replikace dotazu

Tento dotaz vykreslí výsečový graf pro aktuální stav replikace všech chráněných virtuálních počítačích Azure, rozdělených do tří stavů: Normální, Upozornění nebo Kritické.

```
AzureDiagnostics  
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s  
| project name_s , replicationHealth_s  
| summarize count() by replicationHealth_s  
| render piechart   
```
### <a name="query-mobility-service-version"></a>Verze služby Mobility dotazu

Tento dotaz vykreslí výsečový graf pro virtuální počítače Azure replikované pomocí site recovery, rozdělené podle verze agenta mobility, který jsou spuštěny.

```
AzureDiagnostics  
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s  
| project name_s , agentVersion_s  
| summarize count() by agentVersion_s  
| render piechart 
```

### <a name="query-rpo-time"></a>Čas rpo dotazu

Tento dotaz vykreslí pruhový graf virtuálních počítačích Azure replikovaných pomocí site recovery, rozdělený podle cíle bodu obnovení (RPO): Méně než 15 minut, mezi 15 a 30 minut, více než 30 minut.

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

![RPO dotazu](./media/monitoring-log-analytics/example1.png)

### <a name="query-site-recovery-jobs"></a>Úlohy obnovení webu dotazu

Tento dotaz načte všechny úlohy obnovení webu (pro všechny scénáře zotavení po havárii), které se aktivují za posledních 72 hodin a jejich stav dokončení.

```
AzureDiagnostics  
| where Category == "AzureSiteRecoveryJobs"  
| where TimeGenerated >= ago(72h)   
| project JobName = OperationName , VaultName = Resource , TargetName = affectedResourceName_s, State = ResultType  
```

### <a name="query-site-recovery-events"></a>Události obnovení webu dotazu

Tento dotaz načte všechny události obnovení webu (pro všechny scénáře zotavení po havárii) vyvolané v posledních 72 hodinách, spolu s jejich závažnosti. 

```
AzureDiagnostics   
| where Category == "AzureSiteRecoveryEvents"   
| where TimeGenerated >= ago(72h)   
| project AffectedObject=affectedResourceName_s , VaultName = Resource, Description_s = healthErrors_s , Severity = Level  
```

### <a name="query-test-failover-state-pie-chart"></a>Stav převzetí služeb při selhání testu dotazu (výsečový graf)

Tento dotaz vykreslí výsečový graf pro stav převzetí služeb při selhání testu virtuálních počítačích Azure replikované s site recovery.

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

Tento dotaz vykreslí tabulku pro stav převzetí služeb při selhání testu virtuálních počítačích Azure replikované s site recovery.

```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)   
| where isnotempty(failoverHealth_s) and isnotnull(failoverHealth_s)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| project VirtualMachine = name_s , VaultName = Resource , TestFailoverStatus = failoverHealth_s 
```

### <a name="query-machine-rpo"></a>RPO dotazovacího stroje

Tento dotaz vykresluje graf trendů, který sleduje rpo konkrétního virtuálního počítače Azure (ContosoVM123) za posledních 72 hodin.

```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where TimeGenerated > ago(72h)  
| where isnotempty(name_s) and isnotnull(name_s)   
| where name_s == "ContosoVM123"  
| project TimeGenerated, name_s , RPO_in_seconds = rpoInSeconds_d   
| render timechart 
```
![RPO dotazovacího stroje](./media/monitoring-log-analytics/example2.png)

### <a name="query-data-change-rate-churn-and-upload-rate-for-an-azure-vm"></a>Rychlost dotazování na změnu dat (konve) a rychlost odesílání pro virtuální počítač Azure

Tento dotaz vykresluje graf trendů pro konkrétní virtuální počítač Azure (ContosoVM123), který představuje rychlost změny dat (počet bajtů za sekundu) a rychlost odesílání dat. 

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
![Změna dat dotazu](./media/monitoring-log-analytics/example3.png)

### <a name="query-data-change-rate-churn-and-upload-rate-for-a-vmware-or-physical-machine"></a>Rychlost změny dat dotazu (konve) a rychlost odesílání pro počítač VMware nebo fyzický počítač

> [!Note]
> Ujistěte se, že jste nastavili agenta monitorování na procesním serveru pro načtení těchto protokolů. Odkazovat [kroky ke konfiguraci agenta monitorování](#configure-microsoft-monitoring-agent-on-the-process-server-to-send-churn-and-upload-rate-logs).

Tento dotaz vykresluje graf trendů pro konkrétní disk **disk0** replikované **položky win-9r7sfh9qlru**, který představuje rychlost změny dat (Zápis bajtů za sekundu) a rychlost odesílání dat. Název disku naleznete v okně **Disky** replikované položky v trezoru služeb pro obnovení. Název instance, který má být použit v dotazu, je název DNS počítače následovaný _ a název disku jako v tomto příkladu.

```
Perf
| where ObjectName == "ASRAnalytics"
| where InstanceName contains "win-9r7sfh9qlru_disk0"
| where TimeGenerated >= ago(4h) 
| project TimeGenerated ,CounterName, Churn_MBps = todouble(CounterValue)/5242880 
| render timechart
```
Process Server odesílá tato data každých 5 minut do pracovního prostoru Analýzy protokolů. Tyto datové body představují průměr vypočítaný po dobu 5 minut.

### <a name="query-disaster-recovery-summary-azure-to-azure"></a>Souhrn zotavení po havárii dotazu (Azure to Azure)

Tento dotaz vykreslí souhrnnou tabulku pro virtuální počítače Azure replikované do sekundární oblasti Azure.  Zobrazuje název virtuálního zařízení, stav replikace a ochrany, rpo, test stavu převzetí služeb při selhání, verzi agenta mobility, všechny aktivní chyby replikace a zdrojové umístění.

```
AzureDiagnostics 
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| project VirtualMachine = name_s , Vault = Resource , ReplicationHealth = replicationHealth_s, Status = protectionState_s, RPO_in_seconds = rpoInSeconds_d, TestFailoverStatus = failoverHealth_s, AgentVersion = agentVersion_s, ReplicationError = replicationHealthErrors_s, SourceLocation = primaryFabricName_s 
```

### <a name="query-disaster-recovery-summary-vmwarephysical-servers"></a>Souhrn zotavení po havárii dotazu (VMware/fyzické servery)

Tento dotaz vykresluje souhrnnou tabulku pro virtuální počítače VMware a fyzické servery replikované do Azure.  Zobrazuje název počítače, stav replikace a ochrany, rpo, test stavu převzetí služeb při selhání, verzi agenta mobility, všechny aktivní chyby replikace a příslušný procesní server.

```
AzureDiagnostics  
| where replicationProviderName_s == "InMageAzureV2"   
| where isnotempty(name_s) and isnotnull(name_s)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| project VirtualMachine = name_s , Vault = Resource , ReplicationHealth = replicationHealth_s, Status = protectionState_s, RPO_in_seconds = rpoInSeconds_d, TestFailoverStatus = failoverHealth_s, AgentVersion = agentVersion_s, ReplicationError = replicationHealthErrors_s, ProcessServer = processServerName_g  
```

## <a name="set-up-alerts---examples"></a>Nastavení výstrah – příklady

Výstrahy obnovení webu můžete nastavit na základě dat Azure Monitor. [Přečtěte si další informace](../azure-monitor/platform/alerts-log.md#managing-log-alerts-from-the-azure-portal) o nastavení upozornění protokolu. 

> [!NOTE]
> Některé příklady používají **replicationProviderName_s** nastaveny na **A2A**. To nastaví výstrahy pro virtuální počítače Azure, které se replikují do sekundární oblasti Azure. V těchto příkladech můžete nahradit **A2A** **inMageAzureV2,** pokud chcete nastavit výstrahy pro místní virtuální počítače VMware nebo fyzické servery replikované do Azure.

### <a name="multiple-machines-in-a-critical-state"></a>Více počítačů v kritickém stavu

Nastavte výstrahu, pokud více než 20 replikovaných virtuálních počítačů Azure přejde do kritického stavu.

```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where replicationHealth_s == "Critical"  
| where isnotempty(name_s) and isnotnull(name_s)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| summarize count() 
```
Pro výstrahu nastavte **prahovou hodnotu** na 20.

### <a name="single-machine-in-a-critical-state"></a>Jeden stroj v kritickém stavu

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
Pro výstrahu nastavte **prahovou hodnotu** na hodnotu 1.

### <a name="multiple-machines-exceed-rpo"></a>Více počítačů překračuje RPO

Nastavte výstrahu, pokud rpo pro více než 20 virtuálních počítačů Azure překročí 30 minut.
```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)   
| where rpoInSeconds_d > 1800  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| project name_s , rpoInSeconds_d   
| summarize count()  
```
Pro výstrahu nastavte **prahovou hodnotu** na 20.

### <a name="single-machine-exceeds-rpo"></a>Jeden stroj překračuje RPO

Nastavte výstrahu, pokud rpo pro jeden virtuální počítač Azure překročí 30 minut.

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
Pro výstrahu nastavte **prahovou hodnotu** na hodnotu 1.

### <a name="test-failover-for-multiple-machines-exceeds-90-days"></a>Test převzetí služeb při selhání pro více počítačů přesahuje 90 dnů

Nastavte výstrahu, pokud poslední úspěšné zkušební převzetí služeb při selhání bylo více než 90 dní, pro více než 20 virtuálních m. . 

```
AzureDiagnostics  
| where replicationProviderName_s == "A2A"   
| where Category == "AzureSiteRecoveryReplicatedItems"  
| where isnotempty(name_s) and isnotnull(name_s)   
| where lastSuccessfulTestFailoverTime_t <= ago(90d)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| summarize count()  
```
Pro výstrahu nastavte **prahovou hodnotu** na 20.

### <a name="test-failover-for-single-machine-exceeds-90-days"></a>Zkušební převzetí služeb při selhání pro jeden počítač přesahuje 90 dní

Nastavte výstrahu, pokud poslední úspěšné testovací převzetí služeb při selhání pro konkrétní virtuální ms bylo před více než 90 dny.
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
Pro výstrahu nastavte **prahovou hodnotu** na hodnotu 1.

### <a name="site-recovery-job-fails"></a>Úloha obnovení webu se nezdaří.

Nastavte výstrahu, pokud úloha obnovení webu (v tomto případě úloha Znovu chránit) selže pro jakýkoli scénář obnovení webu během posledního dne. 
```
AzureDiagnostics   
| where Category == "AzureSiteRecoveryJobs"   
| where OperationName == "Reprotect"  
| where ResultType == "Failed"  
| summarize count()  
```

Pro výstrahu nastavte **hodnotu Threshold** na hodnotu 1 a **Období** na 1440 minut, chcete-li zkontrolovat selhání v poslední den.

## <a name="next-steps"></a>Další kroky

[Přečtěte si o](site-recovery-monitor-and-troubleshoot.md) integrovaném monitorování site recovery.
