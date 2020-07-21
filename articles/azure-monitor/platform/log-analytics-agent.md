---
title: Přehled agenta Log Analytics
description: Toto téma vám pomůže pochopit, jak shromažďovat data a monitorovat počítače hostované v Azure, v místním prostředí nebo v jiném cloudovém prostředí s Log Analytics.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/04/2020
ms.openlocfilehash: c6bd45324313ebc44bd4c59cd6f09e2eaab28d32
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/20/2020
ms.locfileid: "86505138"
---
# <a name="log-analytics-agent-overview"></a>Přehled agenta Log Analytics
Agent Azure Log Analytics byl vyvinut pro komplexní správu napříč virtuálními počítači v jakémkoli cloudu, v místních počítačích a monitorované pomocí [System Center Operations Manager](/system-center/scom/). Agenti systému Windows a Linux odesílají shromážděná data z různých zdrojů do vašeho pracovního prostoru Log Analytics v Azure Monitor a také všechny jedinečné protokoly nebo metriky, jak jsou definovány v řešení monitorování. Agent Log Analytics také podporuje přehledy a další služby v Azure Monitor, jako jsou [Azure monitor pro virtuální počítače](../insights/vminsights-enable-overview.md), [Azure Security Center](../../security-center/index.yml)a [Azure Automation](../../automation/automation-intro.md).

Tento článek poskytuje podrobný přehled požadavků na agenty, systém a síť a různé metody nasazení.

> [!NOTE]
> Můžete se také podívat na Log Analytics agenta označovaného jako Microsoft Monitoring Agent (MMA) nebo agent OMS Linux.

> [!NOTE]
> Azure Diagnostics rozšíření je jedním z agentů, které jsou k dispozici ke shromažďování dat monitorování z hostovaného operačního systému výpočetních prostředků. Popis různých agentů a pokynů k výběru vhodných agentů pro vaše požadavky najdete v tématu [přehled Azure Monitorch agentů](agents-overview.md) .

## <a name="comparison-to-azure-diagnostics-extension"></a>Porovnání s rozšířením Azure Diagnostics
[Rozšíření Azure Diagnostics](diagnostics-extension-overview.md) v Azure monitor můžete použít také ke shromažďování dat monitorování z hostovaného operačního systému virtuálních počítačů Azure. V závislosti na vašich požadavcích se můžete rozhodnout použít buď nebo. Podrobné porovnání agentů Azure Monitor najdete v tématu [přehled Azure Monitorch agentů](agents-overview.md) . 

Mezi hlavní rozdíly, které je potřeba vzít v úvahu, patří:

- Rozšíření Azure Diagnostics se dá použít jenom s virtuálními počítači Azure. Agent Log Analytics se dá použít s virtuálními počítači v Azure, jinými cloudy a místním prostředím.
- Rozšíření Azure Diagnostics odesílá data do Azure Storage, [Azure monitor metriky](data-platform-metrics.md) (pouze Windows) a Event Hubs. Agent Log Analytics shromažďuje data do [protokolů Azure monitor](data-platform-logs.md).
- Agent Log Analytics je vyžadován pro [řešení](../monitor-reference.md#insights-and-core-solutions), [Azure monitor pro virtuální počítače](../insights/vminsights-overview.md)a další služby, jako je například [Azure Security Center](../../security-center/index.yml).

## <a name="costs"></a>Náklady
Pro agenta Log Analytics se neúčtují žádné náklady, ale za ingestování dat vám můžou být účtovány poplatky. Podrobné informace o cenách shromažďovaných v pracovním prostoru Log Analytics najdete v části [Správa využití a nákladů pomocí protokolů Azure monitor](manage-cost-storage.md) .

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
Agent Log Analytics odesílá data do pracovního prostoru Log Analytics v Azure Monitor. Agent systému Windows může být více vícedomý pro posílání dat do několika pracovních prostorů a System Center Operations Manager skupin pro správu. Agent pro Linux může odesílat pouze do jednoho cíle.

## <a name="other-services"></a>Další služby
Agent pro Linux a Windows není dostupný jenom pro připojení k Azure Monitor, podporuje taky Azure Automation k hostování role Hybrid Runbook Worker a dalších služeb, jako je [Change Tracking](../../automation/change-tracking.md), [Update Management](../../automation/automation-update-management.md)a [Azure Security Center](../../security-center/security-center-intro.md). Další informace o Hybrid Runbook Worker roli najdete v tématu [Azure Automation Hybrid Runbook Worker](../../automation/automation-hybrid-runbook-worker.md).  

## <a name="installation-and-configuration"></a>Instalace a konfigurace

Při použití agentů Log Analytics ke shromažďování dat je potřeba pochopit následující informace, aby bylo možné naplánovat nasazení agenta:

* Chcete-li shromažďovat data z agentů systému Windows, můžete [nakonfigurovat každého agenta tak, aby se nahlásily do jednoho nebo více pracovních prostorů](agent-windows.md), a to i v případě, že se hlásí do skupiny pro správu System Center Operations Manager Agent pro Windows může hlásit až čtyři pracovní prostory.
* Agent pro Linux nepodporuje více domovských stránek a může nahlásit pouze jeden pracovní prostor.
* Agent systému Windows podporuje [Standard FIPS 140](/windows/security/threat-protection/fips-140-validation)a Agent pro Linux ho nepodporuje.  

Pokud používáte System Center Operations Manager 2012 R2 nebo novější:

* Každá skupina pro správu Operations Manager může být [připojená pouze k jednomu pracovnímu prostoru](om-agents.md).
* Počítače se systémem Linux vykazující skupinu pro správu musí být nakonfigurovány tak, aby nahlásily přímo do Log Analytics pracovního prostoru. Pokud již počítače se systémem Linux hlásí přímo do pracovního prostoru a chcete je monitorovat pomocí Operations Manager, postupujte podle těchto kroků a [nahlaste se skupině pro správu Operations Manager](agent-manage.md#configure-agent-to-report-to-an-operations-manager-management-group).
* Log Analytics agenta pro Windows můžete nainstalovat do počítače s Windows a podávat ho do obou Operations Manager integrovaných v pracovním prostoru a v jiném pracovním prostoru.


Existuje několik metod, jak nainstalovat agenta Log Analytics a připojit počítač k Azure Monitor v závislosti na vašich požadavcích. Následující tabulka zvýrazňuje jednotlivé metody, abyste zjistili, které funkce ve vaší organizaci nejlépe vyhovuje.

|Zdroj | Metoda | Popis|
|-------|-------------|-------------|
|Virtuální počítač Azure| [Ručně z Azure Portal](../../azure-monitor/learn/quick-collect-azurevm.md?toc=/azure/azure-monitor/toc.json) | Zadejte virtuální počítače, které chcete nasadit z pracovního prostoru Log Analytics. |
| | Log Analytics rozšíření virtuálního počítače pro [Windows](../../virtual-machines/extensions/oms-windows.md) nebo [Linux](../../virtual-machines/extensions/oms-linux.md) pomocí Azure CLI nebo šablony Azure Resource Manager | Rozšíření nainstaluje agenta Log Analytics na virtuální počítače Azure a zaregistruje je do existujícího pracovního prostoru Azure Monitor. |
| | [Azure Monitor pro virtuální počítače](../insights/vminsights-enable-overview.md) | Když povolíte monitorování pomocí Azure Monitor pro virtuální počítače, nainstaluje se rozšíření Log Analytics a agent. |
| | [Azure Security Center Automatické zřizování](../../security-center/security-center-enable-data-collection.md) | Azure Security Center může zřídit agenta Log Analytics na všech podporovaných virtuálních počítačích Azure a všech nově vytvořených, pokud ji povolíte pro monitorování ohrožení zabezpečení a hrozeb. Pokud je povoleno, budou zřízeny všechny nové nebo existující virtuální počítače bez nainstalovaného agenta. |
| Hybridní počítač s Windows| [Ruční instalace](agent-windows.md) | Nainstalujte agenta Microsoft Monitoring Agent z příkazového řádku. |
| | [Azure Automation DSC](agent-windows.md#install-the-agent-using-dsc-in-azure-automation) | Automatizujte instalaci pomocí Azure Automation DSC. |
| | [Správce prostředků šablonu s Azure Stack](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/MicrosoftMonitoringAgent-ext-win) | Pokud jste nasadili Microsoft Azure Stack ve svém datovém centru, použijte šablonu Azure Resource Manager.| 
| Hybridní počítač s Linuxem| [Ruční instalace](../../azure-monitor/learn/quick-collect-linux-computer.md)|Nainstalujte agenta pro Linux volání skriptu obálky hostovaného na GitHubu. | 
| System Center Operations Manager|[Integrace Operations Manager s Log Analytics](../../azure-monitor/platform/om-agents.md) | Umožňuje konfigurovat integraci mezi protokoly Operations Manager a Azure Monitor a předávat shromážděná data z počítačů s Windows, která se vytvářejí do skupiny pro správu.|  


## <a name="supported-windows-operating-systems"></a>Podporované operační systémy Windows

Pro agenta Windows jsou oficiálně podporované následující verze operačního systému Windows:

* Windows Server 2019
* Windows Server 2016, verze 1709 a 1803
* Windows Server 2012, 2012 R2
* Windows Server 2008 SP2 (x64), 2008 R2
* Windows 10 Enterprise (včetně více relací) a pro
* Windows 8 Enterprise a pro 
* Windows 7 SP1

>[!NOTE]
>I když je agent Log Analytics pro systém Windows navržený tak, aby podporoval scénáře monitorování serveru, uvědomujeme si, že můžete spustit klienta Windows pro podporu úloh konfigurovaných a optimalizovaných pro serverový operační systém. Agent podporuje klienta systému Windows, ale naše řešení monitorování se nesoustředí na scénáře monitorování klientů, pokud není výslovně uvedeno.

## <a name="supported-linux-operating-systems"></a>Podporované operační systémy Linux

V této části najdete podrobné informace o podporovaných distribucích systému Linux.

Od verzí vydaných po srpna 2018 provedeme následující změny modelu podpory:  

* Podporovány jsou pouze verze serveru, nikoli klient.  
* Zaměřte se na podporu kteréhokoli [distribuce schváleného pro Azure Linux](../../virtual-machines/linux/endorsed-distros.md). Všimněte si, že může dojít k prodlevě mezi novou distribuce/verzí, kterou systém Azure Linux schválil, a podporuje se pro agenta Log Analytics Linux.
* Všechny dílčí verze jsou podporovány pro každou hlavní verzi uvedenou v seznamu.
* Verze, které předaly datum ukončení podpory svého výrobce, nejsou podporovány.  
* Nové verze AMI se nepodporují.  
* Podporují se jenom verze standardu SSL 1. x.

>[!NOTE]
>Pokud používáte distribuce nebo verzi, která není v současné době podporovaná a nerovná se k našemu modelu podpory, doporučujeme, abyste toto úložiště rozpustili a potvrdili, že podpora Microsoftu nebude poskytovat pomoc s rozvětvené verze agenta.


### <a name="python-2-requirement"></a>Požadavek Pythonu 2
 Agent Log Analytics vyžaduje Python 2. Pokud váš virtuální počítač používá distribuce, který ve výchozím nastavení neobsahuje Python 2, musíte ho nainstalovat. Následující vzorové příkazy instalují Python 2 v různých distribuce.

 - Red Hat, CentOS, Oracle:`yum install -y python2`
 - Ubuntu, Debian:`apt-get install -y python2`
 - SUSE`zypper install -y python2`

Spustitelný soubor python2 musí mít alias na Python, a to pomocí následujícího příkazu:

```
alternatives --set python `which python2`
```

### <a name="supported-distros"></a>Podporované distribuce

Pro agenta pro Linux jsou oficiálně podporované následující verze operačního systému Linux:

* Amazon Linux 2017,09 (x64)
* CentOS Linux 6 (x64) a 7 (x64)  
* Oracle Linux 6 a 7 (x64) 
* Red Hat Enterprise Linux Server 6 (x64), 7 (x64) a 8 (x64)
* Debian GNU/Linux 8 a 9 (x64)
* Ubuntu 14,04 LTS (x86/x64), 16,04 LTS (x64) a 18,04 LTS (x64)
* SUSE Linux Enterprise Server 12 (x64) a 15 (x64)

>[!NOTE]
>OpenSSL 1.1.0 se podporuje jenom na platformách x86_x64 (64 bitů) a OpenSSL starších než 1. x se na žádné platformě nepodporuje.
>

### <a name="agent-prerequisites"></a>Předpoklady pro agenta

V následující tabulce jsou vysvětlené balíčky požadované pro podporované distribuce Linux, na které se agent nainstaluje.

|Požadovaný balíček |Popis |Minimální verze |
|-----------------|------------|----------------|
|Glibc |    Knihovna GNU C | 2.5-12 
|Openssl    | Knihovny OpenSSL | 1,0. x nebo 1.1. x |
|Curl | Webový klient s kudrlinkou | 7.15.5 |
|Python – ctypes | | 
|PAM | Pluggable Authentication Modules | | 

>[!NOTE]
>Aby bylo možné shromažďovat zprávy syslog, jsou vyžadovány buď rsyslog, nebo syslog-ng. Výchozí démon procesu Syslog verze 5 Red Hat Enterprise Linux, CentOS a verze Oracle Linux (sysklog) není pro shromažďování událostí syslog podporován. Aby bylo možné shromažďovat data syslog z této verze těchto distribucí, je třeba nainstalovat démona rsyslog a nakonfigurovat tak, aby nahradila sysklog.

## <a name="tls-12-protocol"></a>Protokol TLS 1,2

Abychom zajistili zabezpečení dat při přenosu do Azure Monitor protokolů, důrazně doporučujeme nakonfigurovat agenta tak, aby používal minimálně protokol TLS (Transport Layer Security) 1,2. Zjistili jsme, že starší verze TLS/SSL (Secure Sockets Layer) (SSL) jsou zranitelné a i když stále fungují k tomu, aby se zajistila zpětná kompatibilita, **nedoporučuje**se.  Další informace najdete v [zabezpečeném posílání dat pomocí TLS 1,2](data-security.md#sending-data-securely-using-tls-12). 


## <a name="sha-2-code-signing-support-requirement-for-windows"></a>Požadavek podpory podepisování kódu SHA-2 pro Windows
Agent Windows bude začínat výhradně pomocí podepisování SHA-2 na 17. srpna 2020. Tato změna bude mít dopad na zákazníky využívající agenta Log Analytics na starší verzi operačního systému jako součást jakékoli služby Azure (Azure Monitor, Azure Automation, Azure Update Management, Azure Change Tracking, Azure Security Center, Azure Sentinel, ATP v programu Windows Defender). Tato změna nevyžaduje žádnou akci zákazníka, pokud nepoužíváte agenta na starší verzi operačního systému (Windows 7, Windows Server 2008 R2 a Windows Server 2008). Zákazníci, kteří používají starší verzi operačního systému, musí na svých počítačích provádět následující akce před 17. srpna 2020 nebo jejich agenti přestane odesílat data do svých Log Analyticsch pracovních prostorů:

1. Nainstalujte nejnovější aktualizaci Service Pack pro váš operační systém. Požadované verze service packu jsou:
    - Windows 7 SP1
    - Windows Server 2008 SP2
    - Windows Server 2008 R2 SP1

2. Nainstalujte aktualizace pro systém Windows podepisování SHA-2 pro váš operační systém, jak je popsáno v tématu [2019 požadavky na podporu podepisování kódu pro Windows a WSUS v SHA-2](https://support.microsoft.com/help/4472027/2019-sha-2-code-signing-support-requirement-for-windows-and-wsus) .
3. Aktualizujte na nejnovější verzi agenta Windows (verze 10.20.18029).
4. Doporučuje se nakonfigurovat agenta tak, aby [používal protokol TLS 1,2](agent-windows.md#configure-agent-to-use-tls-12). 


## <a name="network-requirements"></a>Požadavky sítě
Agent pro Linux a Windows komunikuje odchozí komunikaci s Azure Monitor službou přes port TCP 443 a pokud se počítač připojí přes Internet přes bránu firewall nebo proxy server, aby se mohl komunikovat přes Internet, Projděte si níže uvedené požadavky, abyste porozuměli požadované konfiguraci sítě. Pokud vaše zásady zabezpečení IT neumožňují, aby se počítače v síti připojovaly k Internetu, můžete nastavit [bránu Log Analytics](gateway.md) a potom nakonfigurovat agenta tak, aby se připojil přes bránu k Azure monitor protokolů. Agent pak může získat informace o konfiguraci a odesílat shromážděná data v závislosti na tom, jaká pravidla shromažďování dat a řešení pro monitorování jste povolili v pracovním prostoru.

![Komunikační diagram agenta Log Analytics](./media/log-analytics-agent/log-analytics-agent-01.png)

Následující tabulka uvádí informace o konfiguraci proxy serveru a brány firewall vyžadované pro agenty systému Linux a Windows ke komunikaci s protokoly Azure Monitor.

### <a name="firewall-requirements"></a>Požadavky na bránu firewall

|Prostředek agenta|Porty |Směr |Obejít kontrolu protokolu HTTPS|
|------|---------|--------|--------|   
|*.ods.opinsights.azure.com |Port 443 |Odchozí|Ano |  
|*.oms.opinsights.azure.com |Port 443 |Odchozí|Ano |  
|*.blob.core.windows.net |Port 443 |Odchozí|Ano |
|*.azure-automation.net |Port 443 |Odchozí|Ano |

Informace o bráně firewall požadované pro Azure Government najdete v tématu [správa Azure Government](../../azure-government/compare-azure-government-global-azure.md#azure-monitor-logs). 

Pokud plánujete použít Azure Automation Hybrid Runbook Worker k připojení a registraci ve službě Automation pro použití sad Runbook nebo řešení správy ve vašem prostředí, musí mít přístup k číslu portu a adresám URL popsaným v tématu [Konfigurace sítě pro Hybrid Runbook Worker](../../automation/automation-hybrid-runbook-worker.md#network-planning). 

### <a name="proxy-configuration"></a>Konfigurace proxy serveru

Agent systému Windows a Linux podporuje komunikaci buď prostřednictvím proxy server, nebo Log Analytics bránu, aby Azure Monitor pomocí protokolu HTTPS.  Podporují se anonymní i základní ověřování (uživatelské jméno a heslo).  Pro agenta Windows připojeného přímo ke službě je konfigurace proxy serveru určena během instalace nebo [po nasazení](agent-manage.md#update-proxy-settings) z ovládacích panelů nebo pomocí PowerShellu.  

V případě agenta pro Linux je proxy server zadáno během instalace nebo [po instalaci](agent-manage.md#update-proxy-settings) úpravou konfiguračního souboru proxy. conf.  Hodnota konfigurace proxy agenta pro Linux má následující syntaxi:

`[protocol://][user:password@]proxyhost[:port]`

> [!NOTE]
> Pokud vaše proxy server nevyžaduje ověření, agent pro Linux stále vyžaduje zadání pseudo uživatele nebo hesla. Může to být jakékoli uživatelské jméno nebo heslo.

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
