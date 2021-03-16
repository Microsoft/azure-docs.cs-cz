---
title: Řešení potíží s virtuálními počítači – přehled
description: Řešení potíží s instalací VM Insights
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/15/2021
ms.custom: references_regions
ms.openlocfilehash: 59b4f38efde2416687702647031d2b37553ff8ed
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2021
ms.locfileid: "103555411"
---
# <a name="troubleshoot-vm-insights"></a>Řešení potíží s virtuálními počítači – přehled
Tento článek obsahuje informace o řešení potíží, pokud máte problémy s povolením nebo používáním služby VM Insights.

## <a name="cannot-enable-vm-insights-on-a-machine"></a>Na počítači nejde povolit přehledy virtuálních počítačů.
Při připojování virtuálního počítače Azure z Azure Portal dojde k následujícím krokům:

- V případě výběru této možnosti se vytvoří výchozí pracovní prostor Log Analytics.
- Agent Log Analytics je nainstalovaný na virtuálních počítačích Azure pomocí rozšíření virtuálního počítače, pokud je agent už nainstalovaný.
- Agent závislostí je nainstalován na virtuálních počítačích Azure pomocí rozšíření, pokud je to určeno jako povinné.
  
Během procesu připojování se každý z těchto kroků ověří a na portálu se zobrazí stav oznámení. Konfigurace pracovního prostoru a instalace agenta obvykle trvá 5 až 10 minut. Bude trvat dalších 5 až 10 minut, než se data budou k dispozici pro zobrazení na portálu.

Pokud se zobrazí zpráva, že virtuální počítač musí být připojen po provedení procesu registrace, počkejte až 30 minut, než se proces dokončí. Pokud se problém opakuje, zjistěte možné příčiny v následujících částech.

### <a name="is-the-virtual-machine-running"></a>Je virtuální počítač spuštěný?
 Pokud je virtuální počítač v době běhu vypnutý, je v současné době vypnutý nebo byl zapnutý pouze v poslední době, takže nebudete mít k dispozici data, která by bylo možné zobrazit, dokud data nepřijdete.

### <a name="is-the-operating-system-supported"></a>Je operační systém podporovaný?
Pokud operační systém není v seznamu [podporovaných operačních systémů](vminsights-enable-overview.md#supported-operating-systems) , pak se rozšíření nepodaří nainstalovat a zobrazí se tato zpráva, že čekáme na doručení dat.

### <a name="did-the-extension-install-properly"></a>Bylo rozšíření správně nainstalováno?
Pokud se stále zobrazuje zpráva, že je potřeba připojit virtuální počítač, může to znamenat, že se některé z těchto rozšíření nepodařilo správně nainstalovat. Zkontrolujte stránku **rozšíření** pro váš virtuální počítač v Azure Portal a ověřte, že jsou uvedena následující rozšíření.

| Operační systém | Agenti | 
|:---|:---|
| Windows | MicrosoftMonitoringAgent<br>Microsoft. Azure. Monitoring. DependencyAgent |
| Linux | OMSAgentForLinux<br>DependencyAgentForLinux |

Pokud v seznamu nainstalovaných rozšíření nenajdete obě rozšíření pro váš operační systém, je potřeba je nainstalovat. Pokud jsou tato rozšíření uvedena, ale jejich stav se nezobrazuje jako *úspěšné zřizování*, je třeba toto rozšíření odebrat a znovu nainstalovat.

### <a name="do-you-have-connectivity-issues"></a>Máte potíže s připojením?
Pro počítače s Windows můžete k identifikaci problému s připojením použít nástroj  *TestCloudConnectivity* . Tento nástroj je ve výchozím nastavení nainstalován s agentem ve složce *%systemroot%\Program Files\Microsoft monitoring Agent\Agent*. Spusťte nástroj z příkazového řádku se zvýšenými oprávněními. Vrátí výsledky a zvýrazní, kde test selže. 

![Nástroj TestCloudConnectivity](media/vminsights-troubleshoot/test-cloud-connectivity.png)

### <a name="more-agent-troubleshooting"></a>Další řešení potíží s agentem

V následujících článcích najdete informace o řešení potíží s agentem Log Analytics:

- [Řešení potíží s agentem Log Analytics pro Windows](../agents/agent-windows-troubleshoot.md)
- [Řešení potíží s agentem Log Analytics pro Linux](../agents/agent-linux-troubleshoot.md)

## <a name="performance-view-has-no-data"></a>Zobrazení výkonu neobsahuje žádná data.
Pokud se agenti jeví, aby se nainstalovaly správně, ale v zobrazení výkonu nevidíte žádná data, podívejte se na následující části, kde najdete možné příčiny.

### <a name="has-your-log-analytics-workspace-reached-its-data-limit"></a>Dosáhl by váš pracovní prostor Log Analytics dosaženo svého limitu dat?
Ověřte [rezervace kapacity a ceny pro přijímání dat](https://azure.microsoft.com/pricing/details/monitor/).

### <a name="is-your-virtual-machine-sending-log-and-performance-data-to-azure-monitor-logs"></a>Odesílá váš virtuální počítač data protokolů a výkonu do Azure Monitor protokolů?

Otevřete Log Analytics z části **protokoly** v nabídce Azure Monitor v Azure Portal. Pro váš počítač spusťte následující dotaz:

```kuso
Usage 
| where Computer == "my-computer" 
| summarize sum(Quantity), any(QuantityUnit) by DataType
```

Pokud nevidíte žádná data, může se stát, že máte problémy s agentem. Informace o řešení potíží s agentem najdete v části výše.

## <a name="virtual-machine-doesnt-appear-in-map-view"></a>V zobrazení mapy se virtuální počítač nezobrazuje.

### <a name="is-the-dependency-agent-installed"></a>Je nainstalován agent závislostí?
 Použijte informace v částech výše a zjistěte, jestli je agent závislostí nainstalovaný a funguje správně.

### <a name="are-you-on-the-log-analytics-free-tier"></a>Jste na Log Analytics úrovně Free?
[Úroveň free Log Analytics](https://azure.microsoft.com/pricing/details/monitor/) Toto je starší cenový plán, který umožňuje až pět jedinečných Service map počítačů. Žádné další počítače s v řešení Service Map nezobrazí, a to ani v případě, že předchozích pět počítačů již neodesílá data.

### <a name="is-your-virtual-machine-sending-log-and-performance-data-to-azure-monitor-logs"></a>Odesílá váš virtuální počítač data protokolů a výkonu do Azure Monitor protokolů?
Pomocí dotazu protokolu v [zobrazení výkonu neobsahuje žádná data](#performance-view-has-no-data) k určení, jestli se shromažďují data pro virtuální počítač. Pokud se neshromažďují data, pomocí nástroje TestCloudConnectivity popsaného výše určete, jestli máte problémy s připojením.


## <a name="virtual-machine-appears-in-map-view-but-has-missing-data"></a>Virtuální počítač se zobrazuje v zobrazení mapy, ale obsahuje chybějící data.
Pokud je virtuální počítač v zobrazení mapy, je agent závislostí nainstalovaný a spuštěný, ale ovladač jádra se nenačte. Ověřte soubor protokolu v následujících umístěních:

| Operační systém | Protokol | 
|:---|:---|
| Windows | C:\Program Files\Microsoft – Agent\logs\wrapper.log závislostí |
| Linux | /var/opt/microsoft/dependency-agent/log/service.log |

Poslední řádky souboru by měly obsahovat informace o tom, proč se jádro nenačetlo. Například pokud jste jádro aktualizovali, nemusí být podporované v Linuxu.
## <a name="next-steps"></a>Další kroky

- Podrobnosti o připojování agentů pro virtuální počítače najdete v tématu [Povolení přehledu služby VM Insights](vminsights-enable-overview.md).
