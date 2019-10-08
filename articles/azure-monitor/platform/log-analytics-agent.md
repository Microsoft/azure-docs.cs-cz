---
title: Shromažďování dat protokolu pomocí agenta služby Azure Log Analytics | Microsoft Docs
description: Toto téma vám pomůže pochopit, jak shromažďovat data a monitorovat počítače hostované v Azure, v místním prostředí nebo v jiném cloudovém prostředí s Log Analytics.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/07/2019
ms.author: magoedte
ms.openlocfilehash: 1eec8b79579a1c4d5c889b830706299ccc25e88b
ms.sourcegitcommit: f9e81b39693206b824e40d7657d0466246aadd6e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2019
ms.locfileid: "72035645"
---
# <a name="collect-log-data-with-the-log-analytics-agent"></a>Shromažďovat data protokolu pomocí agenta Log Analytics

Agent Azure Log Analytics, dříve označovaný jako Microsoft Monitoring Agent (MMA) nebo agent OMS Linux, byl vyvinutý pro komplexní správu na místních počítačích, počítače monitorované [System Center Operations Manager](https://docs.microsoft.com/system-center/scom/)a virtuální počítače v jakémkoli cloudu. Agenti systému Windows a Linux se připojují k Azure Monitor a ukládají shromážděná data protokolu z různých zdrojů v pracovním prostoru Log Analytics a také všechny jedinečné protokoly nebo metriky, jak jsou definovány v řešení monitorování. 

Tento článek poskytuje podrobný přehled požadavků na agenty, systém a síť a různé metody nasazení.

## <a name="overview"></a>Přehled

![Komunikační diagram agenta Log Analytics](./media/log-analytics-agent/log-analytics-agent-01.png)

Před analýzou a působením shromážděných dat je nejprve nutné nainstalovat a připojit agenty pro všechny počítače, které chcete odeslat data službě Azure Monitor. Agenty můžete nainstalovat na virtuální počítače Azure pomocí rozšíření virtuálního počítače Azure Log Analytics pro Windows a Linux a pro počítače v hybridním prostředí pomocí instalačního programu, příkazového řádku nebo s požadovanou konfigurací stavu (DSC) v Azure Automation. 

Agent pro Linux a Windows komunikuje odchozí komunikaci s Azure Monitorovou službou přes port TCP 443 a pokud se počítač připojí přes Internet přes bránu firewall nebo proxy server a komunikuje přes Internet, prostudujte si níže uvedené požadavky, abyste pochopili konfiguraci sítě. požadovanou. Pokud vaše zásady zabezpečení IT neumožňují, aby se počítače v síti připojovaly k Internetu, můžete nastavit [bránu Log Analytics](gateway.md) a potom nakonfigurovat agenta tak, aby se připojil přes bránu k Azure monitor protokolů. Agent pak může získat informace o konfiguraci a odesílat shromážděná data v závislosti na tom, jaká pravidla shromažďování dat a řešení pro monitorování jste povolili v pracovním prostoru. 

Při použití agentů Log Analytics ke shromažďování dat je potřeba pochopit následující informace, aby bylo možné naplánovat nasazení agenta:

* Chcete-li shromažďovat data z agentů systému Windows, můžete [nakonfigurovat každého agenta tak, aby se nahlásily do jednoho nebo více pracovních prostorů](agent-windows.md), a to i v případě, že se hlásí do skupiny pro správu System Center Operations Manager Agent pro Windows může hlásit až čtyři pracovní prostory.
* Agent pro Linux nepodporuje více domovských stránek a může nahlásit pouze jeden pracovní prostor.
* Agent systému Windows podporuje [Standard FIPS 140](https://docs.microsoft.com/windows/security/threat-protection/fips-140-validation)a Agent pro Linux ho nepodporuje.  

Pokud používáte System Center Operations Manager 2012 R2 nebo novější:

* Každá skupina pro správu Operations Manager může být [připojená pouze k jednomu pracovnímu prostoru](om-agents.md).
* Počítače se systémem Linux vykazující skupinu pro správu musí být nakonfigurovány tak, aby nahlásily přímo do Log Analytics pracovního prostoru. Pokud již počítače se systémem Linux hlásí přímo do pracovního prostoru a chcete je monitorovat pomocí Operations Manager, postupujte podle těchto kroků a [nahlaste se skupině pro správu Operations Manager](agent-manage.md#configure-agent-to-report-to-an-operations-manager-management-group).
* Log Analytics agenta pro Windows můžete nainstalovat do počítače s Windows a podávat ho do obou Operations Manager integrovaných v pracovním prostoru a v jiném pracovním prostoru.

Agent pro Linux a Windows není dostupný jenom pro připojení k Azure Monitor, podporuje taky Azure Automation pro hostování role Hybrid Runbook Worker a dalších služeb, jako je [Change Tracking](../../automation/change-tracking.md), [Update Management](../../automation/automation-update-management.md)a [Azure Security Center ](../../security-center/security-center-intro.md). Další informace o Hybrid Runbook Worker roli najdete v tématu [Azure Automation Hybrid Runbook Worker](../../automation/automation-hybrid-runbook-worker.md).  

## <a name="supported-windows-operating-systems"></a>Podporované operační systémy Windows

Pro agenta Windows jsou oficiálně podporované následující verze operačního systému Windows:

* Windows Server 2019
* Windows Server 2008 SP2 (x64), 2008 R2, 2012, 2012 R2, 2016, verze 1709 a 1803
* Windows 7 SP1, Windows 8 Enterprise a pro a Windows 10 Enterprise a pro

>[!NOTE]
>I když je agent Log Analytics pro systém Windows navržený tak, aby podporoval scénáře monitorování serveru, uvědomujeme si, že můžete spustit klienta Windows pro podporu úloh konfigurovaných a optimalizovaných pro serverový operační systém. Agent podporuje klienta systému Windows, ale naše řešení monitorování se nesoustředí na scénáře monitorování klientů, pokud není výslovně uvedeno.

## <a name="supported-linux-operating-systems"></a>Podporované operační systémy Linux

V této části najdete podrobné informace o podporovaných distribucích systému Linux.

Od verzí vydaných po srpna 2018 provedeme následující změny modelu podpory:  

* Podporovány jsou pouze verze serveru, nikoli klient.  
* Nové verze [Azure Linux schválené distribuce](../../virtual-machines/linux/endorsed-distros.md) se vždycky podporují.  
* Všechny dílčí verze jsou podporovány pro každou hlavní verzi uvedenou v seznamu.
* Verze, které předaly datum ukončení podpory svého výrobce, nejsou podporovány.  
* Nové verze AMI se nepodporují.  
* Podporují se jenom verze standardu SSL 1. x.

>[!NOTE]
>Pokud používáte distribuce nebo verzi, která není v současné době podporovaná a nerovná se k našemu modelu podpory, doporučujeme, abyste toto úložiště rozpustili a potvrdili, že podpora Microsoftu nebude poskytovat pomoc s rozvětvené verze agenta.

* Amazon Linux 2017,09 (x64)
* CentOS Linux 6 (x86/x64) a 7 (x64)  
* Oracle Linux 6 a 7 (x86/x64) 
* Red Hat Enterprise Linux Server 6 (x86/x64) a 7 (x64)
* Debian GNU/Linux 8 a 9 (x86/x64)
* Ubuntu 14,04 LTS (x86/x64), 16,04 LTS (x86/x64) a 18,04 LTS (x64)
* SUSE Linux Enterprise Server 12 (x64) a 15 (x64)

>[!NOTE]
>OpenSSL 1.1.0 je podporován pouze na platformách x86_x64 (64 bitů) a OpenSSL starších než 1. x není na žádné platformě podporován.
>

### <a name="agent-prerequisites"></a>Předpoklady pro agenta

V následující tabulce jsou vysvětlené balíčky požadované pro podporované distribuce Linux, na které se agent nainstaluje.

|Požadovaný balíček |Popis |Minimální verze |
|-----------------|------------|----------------|
|Glibc |    Knihovna GNU C | 2,5 – 12 
|OpenSSL    | Knihovny OpenSSL | 1,0. x nebo 1.1. x |
|Curl | Webový klient s kudrlinkou | 7.15.5 |
|Python – ctypes | | 
|MODULU | Moduly připojitelné k ověřování | | 

>[!NOTE]
>Aby bylo možné shromažďovat zprávy syslog, jsou vyžadovány buď rsyslog, nebo syslog-ng. Výchozí démon procesu Syslog verze 5 Red Hat Enterprise Linux, CentOS a verze Oracle Linux (sysklog) není pro shromažďování událostí syslog podporován. Aby bylo možné shromažďovat data syslog z této verze těchto distribucí, je třeba nainstalovat démona rsyslog a nakonfigurovat tak, aby nahradila sysklog.

## <a name="tls-12-protocol"></a>Protokol TLS 1,2

Aby se zajistilo zabezpečení přenášených dat do protokolů Azure Monitor, důrazně doporučujeme nakonfigurovat agenta tak, aby používal minimálně protokol TLS (Transport Layer Security) 1,2. Zjistili jsme, že starší verze TLS/SSL (Secure Sockets Layer) (SSL) jsou zranitelné a i když stále fungují k tomu, aby se zajistila zpětná kompatibilita, **nedoporučuje**se.  Další informace najdete v [zabezpečeném posílání dat pomocí TLS 1,2](../../azure-monitor/platform/data-security.md#sending-data-securely-using-tls-12). 

## <a name="network-firewall-requirements"></a>Požadavky na bránu firewall sítě

Níže uvedené informace uvádějí informace o konfiguraci proxy serveru a brány firewall vyžadované pro systémy Linux a Agent pro Windows ke komunikaci s protokoly Azure Monitor.  

|Prostředek agenta|Porty |Směr |Obejít kontrolu protokolu HTTPS|
|------|---------|--------|--------|   
|*.ods.opinsights.azure.com |Port 443 |Odchozí|Ano |  
|*.oms.opinsights.azure.com |Port 443 |Odchozí|Ano |  
|*.blob.core.windows.net |Port 443 |Odchozí|Ano |  
|*.azure-automation.net |Port 443 |Odchozí|Ano |  

Informace o bráně firewall požadované pro Azure Government najdete v tématu [správa Azure Government](../../azure-government/documentation-government-services-monitoringandmanagement.md#azure-monitor-logs). 

Pokud plánujete použít Azure Automation Hybrid Runbook Worker k připojení a registraci ve službě Automation pro použití runbooků ve vašem prostředí, musí mít přístup k číslu portu a adresám URL popsaným v tématu [Konfigurace sítě pro hybridní Runbook. Pracovní proces](../../automation/automation-hybrid-runbook-worker.md#network-planning). 

Agent systému Windows a Linux podporuje komunikaci buď prostřednictvím proxy server, nebo Log Analytics bránu, aby Azure Monitor pomocí protokolu HTTPS.  Podporují se anonymní i základní ověřování (uživatelské jméno a heslo).  Pro agenta Windows připojeného přímo ke službě je konfigurace proxy serveru určena během instalace nebo [po nasazení](agent-manage.md#update-proxy-settings) z ovládacích panelů nebo pomocí PowerShellu.  

V případě agenta pro Linux je proxy server zadáno během instalace nebo [po instalaci](agent-manage.md#update-proxy-settings) úpravou konfiguračního souboru proxy. conf.  Hodnota konfigurace proxy agenta pro Linux má následující syntaxi:

`[protocol://][user:password@]proxyhost[:port]`

> [!NOTE]
> Pokud vaše proxy server nevyžaduje ověření, agent pro Linux stále vyžaduje zadání pseudo uživatele nebo hesla. Může to být jakékoli uživatelské jméno nebo heslo.

|Vlastnost| Popis |
|--------|-------------|
|Protocol (Protokol) | https |
|Uživatelský | Volitelné uživatelské jméno pro ověřování proxy |
|heslo | Volitelné heslo pro ověřování proxy serveru |
|proxyhost | Adresa nebo plně kvalifikovaný název domény brány proxy server/Log Analytics |
|port | Volitelné číslo portu pro bránu proxy server/Log Analytics |

Příklad: `https://user01:password@proxy01.contoso.com:30443`

> [!NOTE]
> Pokud v hesle použijete speciální znaky, jako je například "\@", obdržíte chybu připojení k proxy, protože je nesprávně analyzována hodnota.  Pokud chcete tento problém obejít, zakódovat heslo v adrese URL pomocí nástroje, jako je [URLDecode](https://www.urldecoder.org/).  

## <a name="install-and-configure-agent"></a>Instalace a konfigurace agenta

Propojení počítačů v rámci předplatného Azure nebo hybridního prostředí s využitím protokolů Azure Monitor můžete dosáhnout pomocí různých metod v závislosti na vašich požadavcích. Následující tabulka zvýrazňuje jednotlivé metody, abyste zjistili, které funkce ve vaší organizaci nejlépe vyhovuje.

|Zdroj | Metoda | Popis|
|-------|-------------|-------------|
|Virtuální počítač Azure| – Log Analytics rozšíření virtuálního počítače pro [Windows](../../virtual-machines/extensions/oms-windows.md) nebo [Linux](../../virtual-machines/extensions/oms-linux.md) pomocí Azure CLI nebo šablony Azure Resource Manager<br>- [ručně z Azure Portal](../../azure-monitor/learn/quick-collect-azurevm.md?toc=/azure/azure-monitor/toc.json)<br>- [Azure Security Center Automatické zřizování](../../security-center/security-center-enable-data-collection.md)| – Rozšíření nainstaluje agenta Log Analytics na virtuální počítače Azure a zaregistruje je do existujícího pracovního prostoru Azure Monitor.<br>– Azure Security Center můžou zřídit agenta Log Analytics na všech podporovaných virtuálních počítačích Azure a všech nově vytvořených, pokud ji povolíte pro monitorování ohrožení zabezpečení a hrozeb. Pokud je povoleno, budou zřízeny všechny nové nebo existující virtuální počítače bez nainstalovaného agenta.|
| Hybridní počítač s Windows|[Ruční instalace](agent-windows.md) - <br>- [Azure Automation DSC](agent-windows.md#install-the-agent-using-dsc-in-azure-automation)<br>- [Správce prostředků šablona s Azure Stack](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/MicrosoftMonitoringAgent-ext-win) |Nainstalujte agenta Microsoft Monitoring Agent z příkazového řádku nebo pomocí automatizované metody, jako je Azure Automation DSC, [System Center Configuration Manager](https://docs.microsoft.com/sccm/apps/deploy-use/deploy-applications)nebo se šablonou Azure Resource Manager, pokud jste nasadili Microsoft Azure Stack do svého Standard.| 
| Hybridní počítač s Linuxem| [Ruční instalace](../../azure-monitor/learn/quick-collect-linux-computer.md)|Nainstalujte agenta pro Linux volání skriptu obálky hostovaného na GitHubu. | 
| System Center Operations Manager|[Integrace Operations Manager s Log Analytics](../../azure-monitor/platform/om-agents.md) | Umožňuje konfigurovat integraci mezi protokoly Operations Manager a Azure Monitor a předávat shromážděná data z počítačů s Windows, která se vytvářejí do skupiny pro správu.|  

## <a name="next-steps"></a>Další kroky

* Projděte si [zdroje dat](../../azure-monitor/platform/agent-data-sources.md) , abyste porozuměli zdrojům dat, které jsou k dispozici pro shromažďování dat ze systému Windows nebo Linux. 

* Přečtěte si o [dotazech protokolů](../../azure-monitor/log-query/log-query-overview.md) , které analyzují data shromážděná ze zdrojů dat a řešení. 

* Přečtěte si o [řešeních monitorování](../../azure-monitor/insights/solutions.md) , která přidávají funkce pro Azure monitor a také shromažďovat data do pracovního prostoru Log Analytics.
