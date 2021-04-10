---
title: Řešení potíží s Azure Automation Change Tracking a inventářem
description: V tomto článku se dozvíte, jak řešit problémy s funkcí Azure Automation Change Tracking a inventáře.
services: automation
ms.subservice: change-inventory-management
ms.date: 02/15/2021
ms.topic: troubleshooting
ms.openlocfilehash: dd027f94edad580836f0afb8c7293c81ca77605a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "101723822"
---
# <a name="troubleshoot-change-tracking-and-inventory-issues"></a>Řešení problémů s řešením Change Tracking a Inventory

Tento článek popisuje, jak řešit a řešit problémy s Azure Automation Change Tracking a inventáře. Obecné informace o Change Tracking a inventáři najdete v tématu [přehled Change Tracking a inventáře](../change-tracking/overview.md).

## <a name="general-errors"></a>Obecné chyby

### <a name="scenario-machine-is-already-registered-to-a-different-account"></a><a name="machine-already-registered"></a>Scénář: počítač je už zaregistrovaný na jiný účet.

### <a name="issue"></a>Problém

Zobrazí se následující chybová zpráva:

```error
Unable to Register Machine for Change Tracking, Registration Failed with Exception System.InvalidOperationException: {"Message":"Machine is already registered to a different account."}
```

### <a name="cause"></a>Příčina

Počítač už je nasazený do jiného pracovního prostoru pro Change Tracking.

### <a name="resolution"></a>Řešení

1. Ujistěte se, že váš počítač hlásí správný pracovní prostor. Pokyny k tomu, jak to ověřit, najdete v tématu [ověření připojení agenta k Azure monitor](../../azure-monitor/agents/agent-windows.md#verify-agent-connectivity-to-azure-monitor). Také se ujistěte, že je tento pracovní prostor propojený s vaším účtem Azure Automation. Potvrďte to tak, že přejdete na svůj účet Automation a v části **související prostředky** vyberete **propojený pracovní prostor** .

1. Ujistěte se, že se počítače zobrazí v pracovním prostoru Log Analytics, který je propojený s vaším účtem Automation. Spusťte následující dotaz v pracovním prostoru Log Analytics.

   ```kusto
   Heartbeat
   | summarize by Computer, Solutions
   ```

   Pokud počítač ve výsledcích dotazu nevidíte, nevrátí se v poslední době. Pravděpodobně došlo k potížím s místní konfigurací. Agenta Log Analytics byste měli přeinstalovat.

   Pokud je váš počítač uvedený ve výsledcích dotazu, ověřte pod vlastností řešení, kterou je **sledování změn ve** v seznamu. Tím se ověří, že je zaregistrované u Change Tracking a inventáře. Pokud tomu tak není, vyhledejte problémy s konfigurací oboru. Konfigurace oboru určuje, které počítače jsou nakonfigurované pro Change Tracking a inventář. Pokud chcete nakonfigurovat konfiguraci oboru pro cílový počítač, přečtěte si téma [povolení Change Tracking a inventáře z účtu Automation](../change-tracking/enable-from-automation-account.md).

   V pracovním prostoru spusťte tento dotaz.

   ```kusto
   Operation
   | where OperationCategory == 'Data Collection Status'
   | sort by TimeGenerated desc
   ```

1. Pokud získáte ```Data collection stopped due to daily limit of free data reached. Ingestion status = OverQuota``` výsledek, je dosaženo kvóty definované v pracovním prostoru, která zastavila ukládání dat. V pracovním prostoru můžete přejít na **využití a odhadované náklady**. Vyberte novou **cenovou úroveň** , která vám umožní používat víc dat, nebo klikněte na **denní limit** a odeberte limit.

:::image type="content" source="./media/change-tracking/change-tracking-usage.png" alt-text="Využití a odhadované náklady." lightbox="./media/change-tracking/change-tracking-usage.png":::

Pokud je problém stále nevyřešený, postupujte podle kroků v části [nasazení Hybrid Runbook Worker Windows](../automation-windows-hrw-install.md) a přeinstalujte Hybrid Worker pro Windows. V případě systému Linux postupujte podle pokynů v části  [nasazení Hybrid Runbook Worker pro Linux](../automation-linux-hrw-install.md).

## <a name="windows"></a>Windows

### <a name="scenario-change-tracking-and-inventory-records-arent-showing-for-windows-machines"></a><a name="records-not-showing-windows"></a>Scénář: u počítačů s Windows se nezobrazuje Change Tracking a záznamy inventáře.

#### <a name="issue"></a>Problém

Pro počítače s Windows, které jsou pro tuto funkci povolené, se nezobrazuje žádné Change Tracking a žádné výsledky inventáře.

#### <a name="cause"></a>Příčina

Tato chyba může mít následující příčiny:

* Agent Azure Log Analytics pro Windows není spuštěný.
* Komunikace zpět s účtem Automation je zablokovaná.
* Sady Management Pack pro Change Tracking a inventář se nestáhnou.
* Povolený virtuální počítač může pocházet z klonovaného počítače, který nebyl připraven na přípravu systému (Sysprep) s nainstalovaným agentem Log Analytics pro Windows.

#### <a name="resolution"></a>Řešení

Na počítači Log Analytics agenta, otevřete umístění **C:\Program Files\Microsoft monitoring Agent\Agent\Tools** a spusťte následující příkazy:

```cmd
net stop healthservice
StopTracing.cmd
StartTracing.cmd VER
net start healthservice
```

Pokud stále potřebujete pomoc, můžete shromáždit diagnostické informace a kontaktovat podporu.

> [!NOTE]
> Agent Log Analytics ve výchozím nastavení umožňuje trasování chyb. Chcete-li povolit podrobné chybové zprávy jako v předchozím příkladu, použijte `VER` parametr. Pro trasování informací použijte `INF` při vyvolání `StartTracing.cmd` .

##### <a name="log-analytics-agent-for-windows-not-running"></a>Agent Log Analytics pro Windows není spuštěný.

Ověřte, zda je v počítači spuštěn Agent Log Analytics pro systém Windows (**HealthService.exe**).

##### <a name="communication-to-automation-account-blocked"></a>Komunikace s blokovaným účtem služby Automation

Zkontrolujte Prohlížeč událostí na počítači a vyhledejte všechny události, které obsahují slovo `changetracking` .

Další informace o adresách a portech, které musí být povolené pro Change Tracking a inventarizaci, najdete v tématu [Plánování sítě](../automation-hybrid-runbook-worker.md#network-planning).

##### <a name="management-packs-not-downloaded"></a>Sady Management Pack nebyly staženy

Ověřte, zda jsou místně nainstalovány následující Change Tracking a sady Management Pack inventáře:

* `Microsoft.IntelligencePacks.ChangeTrackingDirectAgent.*`
* `Microsoft.IntelligencePacks.InventoryChangeTracking.*`
* `Microsoft.IntelligencePacks.SingletonInventoryCollection.*`

##### <a name="vm-from-cloned-machine-that-has-not-been-sysprepped"></a>VIRTUÁLNÍ počítač z klonovaného počítače, který nebyl nástroje Sysprep

Pokud používáte Klonovaný obrázek, napřed nejprve nástroj Sysprep a poté nainstalujte agenta Log Analytics pro systém Windows.

## <a name="linux"></a>Linux

### <a name="scenario-no-change-tracking-and-inventory-results-on-linux-machines"></a>Scénář: žádné Change Tracking a výsledky inventáře na počítačích se systémem Linux

#### <a name="issue"></a>Problém

Nevidíte žádné Change Tracking a výsledky inventáře pro počítače se systémem Linux, které jsou pro tuto funkci povoleny. 

#### <a name="cause"></a>Příčina
Tady jsou možné příčiny specifické pro tento problém:
* Agent Log Analytics pro Linux neběží.
* Agent Log Analytics pro Linux není správně nakonfigurovaný.
* Došlo ke konfliktu monitorování integrity souborů (FIM).

#### <a name="resolution"></a>Řešení 

##### <a name="log-analytics-agent-for-linux-not-running"></a>Agent Log Analytics pro Linux není spuštěný.

Ověřte, že je na vašem počítači spuštěn démon pro agenta Log Analytics pro Linux (**omsagent**). Spusťte následující dotaz v pracovním prostoru Log Analytics, který je propojený s vaším účtem Automation.

```loganalytics Copy
Heartbeat
| summarize by Computer, Solutions
```

Pokud se Váš počítač nezobrazuje ve výsledcích dotazu, nedošlo v poslední době k vrácení se změnami. Pravděpodobně došlo k potížím s místní konfigurací a je třeba agenta přeinstalovat. Informace o instalaci a konfiguraci najdete v tématu [shromáždění dat protokolu pomocí agenta Log Analytics](../../azure-monitor/agents/log-analytics-agent.md).

Pokud se Váš počítač zobrazí ve výsledcích dotazu, ověřte konfiguraci oboru. Informace najdete [v tématu cílení řešení monitorování v Azure monitor](../../azure-monitor/insights/solution-targeting.md).

Další řešení potíží s tímto problémem najdete v tématu [problém: nevidíte žádná data pro Linux](../../azure-monitor/agents/agent-linux-troubleshoot.md#issue-you-are-not-seeing-any-linux-data).

##### <a name="log-analytics-agent-for-linux-not-configured-correctly"></a>Agent Log Analytics pro Linux není správně nakonfigurovaný.

Agent Log Analytics pro Linux nemusí být správně nakonfigurovaný pro protokoly a výstupní kolekce příkazového řádku pomocí nástroje kolektor protokolů OMS. Viz [přehled Change Tracking a inventáře](../change-tracking/overview.md).

##### <a name="fim-conflicts"></a>Konflikty FIM

Funkce FIM Azure Security Center možná nesprávně ověřuje integritu souborů Linux. Ověřte, že je produkt FIM funkční a správně nakonfigurovaný pro monitorování souborů systému Linux. Viz [přehled Change Tracking a inventáře](../change-tracking/overview.md).

## <a name="next-steps"></a>Další kroky

Pokud tady nevidíte svůj problém nebo nemůžete problém vyřešit, zkuste další podporu vyzkoušet u některého z následujících kanálů:

* Získejte odpovědi od odborníků na Azure prostřednictvím [fór Azure](https://azure.microsoft.com/support/forums/).
* Připojte se pomocí [@AzureSupport](https://twitter.com/azuresupport) oficiálního Microsoft Azure účtu pro zlepšení prostředí pro zákazníky. Podpora Azure spojuje komunitu Azure s odpověďmi, podporou a odborníky.
* Zasouborové incidenty podpory Azure. Přejít na [web podpory Azure](https://azure.microsoft.com/support/options/)a vyberte **získat podporu**.