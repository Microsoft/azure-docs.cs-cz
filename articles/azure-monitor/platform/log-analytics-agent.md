---
title: Přehled agenta Log Analytics
description: Toto téma vám pomůže pochopit, jak shromažďovat data a monitorovat počítače hostované v Azure, v místním prostředí nebo v jiném cloudovém prostředí s Log Analytics.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/21/2020
ms.openlocfilehash: 8a086830398555d962bb13d1d9b0fea3554f7924
ms.sourcegitcommit: 3fc3457b5a6d5773323237f6a06ccfb6955bfb2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2020
ms.locfileid: "90032516"
---
# <a name="log-analytics-agent-overview"></a>Přehled agenta Log Analytics
Agent Azure Log Analytics shromažďuje telemetrie z virtuálních počítačů s Windows a Linux v jakémkoli cloudu, v místních počítačích a monitorované pomocí [System Center Operations Manager](/system-center/scom/) a odesílá je do vašeho pracovního prostoru Log Analytics v Azure monitor. Agent Log Analytics také podporuje přehledy a další služby v Azure Monitor, jako jsou [Azure monitor pro virtuální počítače](../insights/vminsights-enable-overview.md), [Azure Security Center](../../security-center/index.yml)a [Azure Automation](../../automation/automation-intro.md). Tento článek poskytuje podrobný přehled požadavků na agenty, systém a síť a metody nasazení.

> [!NOTE]
> Můžete se také podívat na Log Analytics agenta označovaného jako Microsoft Monitoring Agent (MMA) nebo agent OMS Linux.

## <a name="comparison-to-azure-diagnostics-extension"></a>Porovnání s rozšířením Azure Diagnostics
[Rozšíření Azure Diagnostics](diagnostics-extension-overview.md) v Azure monitor můžete použít také ke shromažďování dat monitorování z hostovaného operačního systému virtuálních počítačů Azure. V závislosti na vašich požadavcích se můžete rozhodnout použít buď nebo. Podrobné porovnání agentů Azure Monitor najdete v tématu [přehled Azure Monitorch agentů](agents-overview.md) . 

Mezi hlavní rozdíly, které je potřeba vzít v úvahu, patří:

- Rozšíření Azure Diagnostics se dá použít jenom s virtuálními počítači Azure. Agent Log Analytics se dá použít s virtuálními počítači v Azure, jinými cloudy a místním prostředím.
- Rozšíření Azure Diagnostics odesílá data do Azure Storage, [Azure monitor metriky](data-platform-metrics.md) (pouze Windows) a Event Hubs. Agent Log Analytics odesílá data do [protokolů Azure monitor](data-platform-logs.md).
- Agent Log Analytics je vyžadován pro [řešení](../monitor-reference.md#insights-and-core-solutions), [Azure monitor pro virtuální počítače](../insights/vminsights-overview.md)a další služby, jako je například [Azure Security Center](../../security-center/index.yml).

## <a name="costs"></a>Náklady
Pro agenta Log Analytics se neúčtují žádné náklady, ale za ingestování dat vám můžou být účtovány poplatky. Podrobné informace o cenách shromažďovaných v pracovním prostoru Log Analytics najdete v části [Správa využití a nákladů pomocí protokolů Azure monitor](manage-cost-storage.md) .

## <a name="supported-operating-systems"></a>Podporované operační systémy

 Seznam verzí operačních systémů Windows a Linux podporovaných agentem Log Analytics najdete v části [podporované operační systémy](agents-overview.md#supported-operating-systems) . 


## <a name="data-collected"></a>Shromažďovaná data
Následující tabulka uvádí typy dat, které můžete nakonfigurovat Log Analytics pracovní prostor pro shromažďování ze všech připojených agentů. Podívejte se na téma [co je monitorované pomocí Azure monitor?](../monitor-reference.md) v seznamu přehledů, řešení a dalších řešení, která používají agenta Log Analytics ke shromažďování dalších typů dat.

| Zdroj dat | Popis |
| --- | --- |
| [Protokoly událostí systému Windows](data-sources-windows-events.md) | Informace odesílané systému protokolování událostí systému Windows. |
| [Syslog](data-sources-syslog.md)                     | Informace odesílané systému protokolování událostí pro Linux. |
| [Výkon](data-sources-performance-counters.md)  | Číselné hodnoty, které měří výkon různých aspektů operačního systému a zatížení. |
| [Protokoly IIS](data-sources-iis-logs.md)                 | Informace o použití pro weby služby IIS běžící v hostovaném operačním systému. |
| [Vlastní protokoly](data-sources-custom-logs.md)           | Události z textových souborů v počítačích se systémem Windows i Linux. |

## <a name="data-destinations"></a>Cíle dat
Agent Log Analytics odesílá data do pracovního prostoru Log Analytics v Azure Monitor. Agent systému Windows může být více vícedomý pro posílání dat do několika pracovních prostorů a System Center Operations Manager skupin pro správu. Agent pro Linux může být odeslán pouze do jednoho cíle, buď z pracovního prostoru, nebo do skupiny pro správu.

## <a name="other-services"></a>Další služby
Agent pro Linux a Windows není dostupný jenom pro připojení k Azure Monitor. Jiné služby, například Azure Security Center a služba Azure Sentinel, spoléhají na agenta a jeho připojené Log Analytics pracovním prostoru. Agent také podporuje Azure Automation pro hostování role Hybrid Runbook Worker a dalších služeb, jako jsou [Change Tracking](../../automation/change-tracking.md), [Update Management](../../automation/update-management/update-mgmt-overview.md)a [Azure Security Center](../../security-center/security-center-intro.md). Další informace o Hybrid Runbook Worker roli najdete v tématu [Azure Automation Hybrid Runbook Worker](../../automation/automation-hybrid-runbook-worker.md).  

## <a name="workspace-and-management-group-limitations"></a>Omezení skupiny pro pracovní prostor a skupinu pro správu

Podrobné informace o připojení agenta ke skupině pro správu Operations Manager najdete v tématu [Konfigurace agenta pro nahlášení do Operations Manager skupiny pro správu](agent-manage.md#configure-agent-to-report-to-an-operations-manager-management-group) .

* Agenti Windows se můžou připojit až ke čtyřem pracovním prostorům, a to i v případě, že jsou připojení ke skupině pro správu System Center Operations Manager.
* Agent systému Linux nepodporuje více domovských skupin a může se připojit pouze k jednomu pracovnímu prostoru nebo skupině pro správu.
  

## <a name="security-limitations"></a>Omezení zabezpečení

* Agenti Windows a Linux podporují [Standard FIPS 140](/windows/security/threat-protection/fips-140-validation), ale [jiné typy posílení zabezpečení nemusejí být podporované](agent-linux.md#supported-linux-hardening).


## <a name="installation-options"></a>Možnosti instalace

Existuje několik metod, jak nainstalovat agenta Log Analytics a připojit počítač k Azure Monitor v závislosti na vašich požadavcích. V následujících částech jsou uvedeny možné metody pro různé typy virtuálních počítačů.

### <a name="azure-virtual-machine"></a>Virtuální počítač Azure

- [Azure monitor pro virtuální počítače](../insights/vminsights-enable-overview.md) poskytuje více metod umožňujících škálování agentů. To zahrnuje instalaci agenta Log Analytics a agenta závislostí. 
- [Azure Security Center může zřídit agenta Log Analytics](../../security-center/security-center-enable-data-collection.md) na všech podporovaných virtuálních počítačích Azure a všech nově vytvořených, pokud ji povolíte pro monitorování ohrožení zabezpečení a hrozeb.
- Log Analytics rozšíření virtuálního počítače pro [Windows](../../virtual-machines/extensions/oms-windows.md) nebo [Linux](../../virtual-machines/extensions/oms-linux.md) se dá nainstalovat s Azure Portal, Azure CLI, Azure PowerShell nebo šablonou Azure Resource Manager.
- Ruční instalace pro jednotlivé virtuální počítače Azure [z Azure Portal](../learn/quick-collect-azurevm.md?toc=%2fazure%2fazure-monitor%2ftoc.json).


### <a name="windows-virtual-machine-on-premises-or-in-another-cloud"></a>Virtuální počítač s Windows v místním prostředí nebo v jiném cloudu 

- [Ručně nainstalujte](agent-windows.md) agenta z příkazového řádku.
- Automatizujte instalaci pomocí [Azure Automation DSC](agent-windows.md#install-agent-using-dsc-in-azure-automation).
- Použití [šablony Správce prostředků s Azure Stack](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/MicrosoftMonitoringAgent-ext-win)

### <a name="linux-virtual-machine-on-premises-or-in-another-cloud"></a>Virtuální počítač se systémem Linux místně nebo v jiném cloudu

- [Ručně nainstalujte](../learn/quick-collect-linux-computer.md) agenta, který zavolá obálku, která je hostovaná na GitHubu.
- System Center Operations Manager | [Integruje Operations Manager s Azure monitor](./om-agents.md) a předává shromážděná data z počítačů s Windows, která se vytvářejí do skupiny pro správu.

## <a name="workspace-id-and-key"></a>ID a klíč pracovního prostoru
Bez ohledu na to, jakou metodu instalace použijete, budete potřebovat ID a klíč pracovního prostoru pro Log Analytics pracovní prostor, ke kterému se bude agent připojovat. Vyberte pracovní prostor z nabídky **Log Analytics pracovní prostory** v Azure Portal. Potom v části **Nastavení** vyberte **Správa agentů** . 

[![Podrobnosti o pracovním prostoru](media/log-analytics-agent/workspace-details.png)](media/log-analytics-agent/workspace-details.png#lightbox)

## <a name="tls-12-protocol"></a>Protokol TLS 1,2

Abychom zajistili zabezpečení dat při přenosu do Azure Monitor protokolů, důrazně doporučujeme nakonfigurovat agenta tak, aby používal minimálně protokol TLS (Transport Layer Security) 1,2. Zjistili jsme, že starší verze TLS/SSL (Secure Sockets Layer) (SSL) jsou zranitelné a i když stále fungují k tomu, aby se zajistila zpětná kompatibilita, **nedoporučuje**se.  Další informace najdete v [zabezpečeném posílání dat pomocí TLS 1,2](data-security.md#sending-data-securely-using-tls-12). 

## <a name="network-requirements"></a>Požadavky sítě
Agent pro Linux a Windows komunikuje odchozí komunikaci s Azure Monitor službou přes port TCP 443. Pokud se počítač připojuje přes Internet přes bránu firewall nebo proxy server pro komunikaci přes Internet, zkontrolujte níže uvedené požadavky, abyste pochopili požadovanou konfiguraci sítě. Pokud vaše zásady zabezpečení IT neumožňují, aby se počítače v síti připojovaly k Internetu, můžete nastavit [bránu Log Analytics](gateway.md) a potom nakonfigurovat agenta tak, aby se připojil přes bránu k Azure monitor. Agent pak může získat informace o konfiguraci a shromažďovat shromážděná data.

![Komunikační diagram agenta Log Analytics](./media/log-analytics-agent/log-analytics-agent-01.png)

Následující tabulka uvádí informace o konfiguraci proxy serveru a brány firewall vyžadované pro agenty systému Linux a Windows ke komunikaci s protokoly Azure Monitor.

### <a name="firewall-requirements"></a>Požadavky na bránu firewall

|Prostředek agenta|Porty |Směr |Obejít kontrolu protokolu HTTPS|
|------|---------|--------|--------|   
|*.ods.opinsights.azure.com |Port 443 |Odchozí|Yes |  
|*.oms.opinsights.azure.com |Port 443 |Odchozí|Yes |  
|*.blob.core.windows.net |Port 443 |Odchozí|Yes |
|*.azure-automation.net |Port 443 |Odchozí|Yes |

Informace o bráně firewall požadované pro Azure Government najdete v tématu [správa Azure Government](../../azure-government/compare-azure-government-global-azure.md#azure-monitor). 

Pokud plánujete použít Azure Automation Hybrid Runbook Worker k připojení a registraci ve službě Automation pro použití sad Runbook nebo řešení správy ve vašem prostředí, musí mít přístup k číslu portu a adresám URL popsaným v tématu [Konfigurace sítě pro Hybrid Runbook Worker](../../automation/automation-hybrid-runbook-worker.md#network-planning). 

### <a name="proxy-configuration"></a>Konfigurace proxy serveru

Agent systému Windows a Linux podporuje komunikaci buď prostřednictvím proxy server, nebo Log Analytics bránu, aby Azure Monitor pomocí protokolu HTTPS.  Podporují se anonymní i základní ověřování (uživatelské jméno a heslo).  Pro agenta Windows připojeného přímo ke službě je konfigurace proxy serveru určena během instalace nebo [po nasazení](agent-manage.md#update-proxy-settings) z ovládacích panelů nebo pomocí PowerShellu.  

V případě agenta pro Linux je proxy server zadáno během instalace nebo [po instalaci](agent-manage.md#update-proxy-settings) úpravou konfiguračního souboru proxy. conf.  Hodnota konfigurace proxy agenta pro Linux má následující syntaxi:

`[protocol://][user:password@]proxyhost[:port]`

|Vlastnost| Popis |
|--------|-------------|
|Protokol | HTTPS |
|uživatel | Volitelné uživatelské jméno pro ověřování proxy |
|heslo | Volitelné heslo pro ověřování proxy serveru |
|proxyhost | Adresa nebo plně kvalifikovaný název domény brány proxy server/Log Analytics |
|port | Volitelné číslo portu pro bránu proxy server/Log Analytics |

Příklad: `https://user01:password@proxy01.contoso.com:30443`

> [!NOTE]
> Pokud v hesle použijete speciální znaky, jako je například " \@ ", obdržíte chybu připojení k proxy, protože je nesprávně analyzována hodnota.  Pokud chcete tento problém obejít, zakódovat heslo v adrese URL pomocí nástroje, jako je [URLDecode](https://www.urldecoder.org/).  



## <a name="next-steps"></a>Další kroky

* Projděte si [zdroje dat](agent-data-sources.md) , abyste porozuměli zdrojům dat, které jsou k dispozici pro shromažďování dat ze systému Windows nebo Linux. 
* Přečtěte si o [dotazech protokolů](../log-query/log-query-overview.md) , které analyzují data shromážděná ze zdrojů dat a řešení. 
* Přečtěte si o [řešeních monitorování](../insights/solutions.md) , která přidávají funkce pro Azure monitor a také shromažďovat data do pracovního prostoru Log Analytics.

