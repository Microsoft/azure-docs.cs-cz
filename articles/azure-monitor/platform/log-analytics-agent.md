---
title: Shromažďování dat protokolu agenta Azure Log Analytics | Dokumentace Microsoftu
description: Toto téma vám pomůže pochopit postupy shromažďování dat a monitorování počítačů hostovaných v Azure, místně nebo jiné cloudové prostředí pomocí služby Log Analytics.
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
ms.date: 07/23/2019
ms.author: magoedte
ms.openlocfilehash: 653355af7dcb0b30c3deb444fcfe4b4ff76e7e77
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2019
ms.locfileid: "68424113"
---
# <a name="collect-log-data-with-the-log-analytics-agent"></a>Shromažďovat data protokolu pomocí agenta Log Analytics

Agent Azure Log Analytics, dříve označovaný jako Microsoft Monitoring Agent (MMA) nebo agent OMS Linux, byl vyvinutý pro komplexní správu na místních počítačích, počítače monitorované [System Center Operations Manager](https://docs.microsoft.com/system-center/scom/)a virtuální počítače v jakémkoli cloudu. Agenti systému Windows a Linux se připojují k Azure Monitor a ukládají shromážděná data protokolu z různých zdrojů v pracovním prostoru Log Analytics a také všechny jedinečné protokoly nebo metriky, jak jsou definovány v řešení monitorování. 

Tento článek obsahuje podrobný přehled o agent, system a požadavky na síť a různých metodách nasazení.

## <a name="overview"></a>Přehled

![Log Analytics agenta komunikace diagramu](./media/log-analytics-agent/log-analytics-agent-01.png)

Před analýzou a působením shromážděných dat je nejprve nutné nainstalovat a připojit agenty pro všechny počítače, které chcete odeslat data službě Azure Monitor. Můžete nainstalovat agenty na virtuální počítače Azure pomocí rozšíření virtuálního počítače Azure Log Analytics pro Windows a Linux a pro počítače v hybridním prostředí pomocí instalačního programu, příkazového řádku, nebo s Desired State Configuration (DSC) ve službě Azure Automation. 

Agent pro Linux a Windows komunikuje odchozí komunikaci s Azure Monitorovou službou přes port TCP 443 a pokud se počítač připojí přes Internet přes bránu firewall nebo proxy server a komunikuje přes Internet, prostudujte si níže uvedené požadavky, abyste pochopili konfiguraci sítě. požadovanou. Pokud vaše zásady zabezpečení IT neumožňují, aby se počítače v síti připojovaly k Internetu, můžete nastavit [bránu Log Analytics](gateway.md) a potom nakonfigurovat agenta tak, aby se připojil přes bránu k Azure monitor protokolů. Agent pak může získat informace o konfiguraci a odesílat shromážděná data v závislosti na tom, jaká pravidla shromažďování dat a řešení pro monitorování jste povolili v pracovním prostoru. 

Pokud sledujete počítač s System Center Operations Manager 2012 R2 nebo novějším, může být u služby Azure Monitor více domácích, aby bylo možné shromažďovat data a přecházet ke službě a nadále je sledovat pomocí [Operations Manager](../../azure-monitor/platform/om-agents.md). U počítačů se systémem Linux agent nezahrnuje komponentu služby Health Service, protože má agenta pro Windows, a informace se shromažďují a zpracovává management server jejím jménem. Vzhledem k tomu, že se počítače se systémem Linux monitorují různě pomocí Operations Manager, nezískávají data ani shromažďují data přímo a předají se přes skupinu pro správu, jako je systém spravovaný agentem Windows. V důsledku toho tento scénář není podporován u počítačů se systémem Linux, které jsou součástí Operations Manager a je třeba nakonfigurovat počítač se systémem Linux tak, aby [hlásil do Operations Manager skupinu pro správu](../platform/agent-manage.md#configure-agent-to-report-to-an-operations-manager-management-group) a Log Analytics pracovní prostor ve dvou krocích.

Windows agent může hlásit až čtyři pracovních prostorů Log Analytics, zatímco agenta pro Linux podporuje jenom sestavy do jednoho pracovního prostoru.  

Agent pro Linux a Windows není dostupný jenom pro připojení k Azure Monitor, podporuje taky Azure Automation pro hostování role Hybrid Runbook Worker a dalších služeb, jako je [Change Tracking](../../automation/change-tracking.md), [Update Management](../../automation/automation-update-management.md)a [Azure Security Center ](../../security-center/security-center-intro.md). Další informace o roli pracovního procesu Hybrid Runbook Worker, naleznete v tématu [Azure Automation Hybrid Runbook Worker](../../automation/automation-hybrid-runbook-worker.md).  

## <a name="supported-windows-operating-systems"></a>Podporované operační systémy Windows
Pro agenta Windows se oficiálně podporuje následující verze operačního systému Windows:

* Windows Server. 2019
* Windows Server 2008 R2, 2012, 2012 R2, 2016, verze 1709 a 1803
* Windows 7 SP1, Windows 8 Enterprise a pro a Windows 10 Enterprise a pro

>[!NOTE]
>I když je agent Log Analytics pro systém Windows navržený tak, aby podporoval scénáře monitorování serveru, uvědomujeme si, že můžete spustit klienta Windows pro podporu úloh konfigurovaných a optimalizovaných pro serverový operační systém. Agent podporuje klienta systému Windows, ale naše řešení monitorování se nesoustředí na scénáře monitorování klientů, pokud není výslovně uvedeno.

## <a name="supported-linux-operating-systems"></a>Podporované operační systémy a Linux

Tato část obsahuje podrobnosti o podporovaných distribucích systému Linux.

Počínaje verzí vydanou po. srpna 2018, provádíme následující změny k náš model podpory:  

* Pouze server, které jsou podporovány verze, ne klienta.  
* Nové verze [distribucích schválených pro Azure Linux](../../virtual-machines/linux/endorsed-distros.md) se podporují vždycky.  
* Všechny dílčí verze se podporují pro všechny hlavní verze uvedené.
* Verze, které uplynuly od výrobce datum ukončení podpory nejsou podporovány.  
* Nová verze AMI nejsou podporovány.  
* Pouze verze, na kterých běží SSL 1.x ve výchozím nastavení jsou podporovány.

>[!NOTE]
>Pokud používáte verzi, která se momentálně nepodporuje a nebude zarovnat na náš model podpory a distribuce, doporučujeme, abyste rozvětvili toto úložiště potvrdil, že podpory společnosti Microsoft nebude poskytovat pomoc s rozvětveného agenta verze.

* Linux Amazon 2017.09 (x 64)
* Linux centOS 6 (x86/x64) a 7 (x 64)  
* Oracle Linux 6 a 7 (x86/x64) 
* Red Hat Enterprise Linux Server 6 (x86/x64) a 7 (x 64)
* Debian GNU/Linux 8 a 9 (x86/x64)
* Ubuntu 14.04 LTS (x86/x64), 16.04 LTS (x86/x64) a 18.04 LTS (x64)
* SUSE Linux Enterprise Server 12 (x64) a 15 (x64)

>[!NOTE]
>OpenSSL 1.1.0 je podporována pouze na platformách x86_x64 (64 bitů) a OpenSSL dříve, než 1.x se nepodporuje na libovolné platformě.
>

### <a name="agent-prerequisites"></a>Předpoklady pro agenta

V následující tabulce jsou vysvětlené balíčky požadované pro podporované distribuce Linux, na které se agent nainstaluje.

|Požadovaný balíček |Popis |Minimální verze |
|-----------------|------------|----------------|
|Glibc |    Knihovna GNU C | 2.5-12 
|OpenSSL    | Knihovny OpenSSL | 1,0. x nebo 1.1. x |
|Curl | Webový klient s kudrlinkou | 7.15.5 |
|Python – ctypes | | 
|MODULU | Moduly připojitelné k ověřování | | 

>[!NOTE]
>Aby bylo možné shromažďovat zprávy syslog, jsou vyžadovány buď rsyslog, nebo syslog-ng. Démon procesu syslog výchozí verze 5 Red Hat Enterprise Linux, CentOS a Oracle Linux verze (sysklog) není podporována pro shromažďování událostí protokolu syslog. Aby bylo možné shromažďovat data syslog z této verze těchto distribucí, je třeba nainstalovat démona rsyslog a nakonfigurovat tak, aby nahradila sysklog.

## <a name="tls-12-protocol"></a>Protokol TLS 1.2

Aby se zajistilo zabezpečení přenášených dat do protokolů Azure Monitor, důrazně doporučujeme nakonfigurovat agenta tak, aby používal minimálně protokol TLS (Transport Layer Security) 1,2. Starší verze z protokolu TLS/Secure Sockets Layer (SSL) bylo zjištěno ohrožen a stále aktuálně fungují povolit zpětnou kompatibilitu, ale jsou **ale nedoporučený krok**.  Další informace najdete v tématu [odesílání dat pomocí protokolu TLS 1.2](../../azure-monitor/platform/data-security.md#sending-data-securely-using-tls-12). 

## <a name="network-firewall-requirements"></a>Požadavky na bránu firewall sítě

Níže uvedené informace uvádějí informace o konfiguraci proxy serveru a brány firewall vyžadované pro systémy Linux a Agent pro Windows ke komunikaci s protokoly Azure Monitor.  

|Prostředek agenta|Porty |Směr |Obejít kontrolu protokolu HTTPS|
|------|---------|--------|--------|   
|*.ods.opinsights.azure.com |Port 443 |Odchozí|Ano |  
|*.oms.opinsights.azure.com |Port 443 |Odchozí|Ano |  
|*.blob.core.windows.net |Port 443 |Odchozí|Ano |  
|*.azure-automation.net |Port 443 |Odchozí|Ano |  

Informace o bráně firewall požadované pro Azure Government najdete v tématu [správa Azure Government](../../azure-government/documentation-government-services-monitoringandmanagement.md#azure-monitor-logs). 

Pokud budete chtít použít Azure Automation Hybrid Runbook Worker a připojte se k registraci ve službě Automation použití sad runbook ve vašem prostředí, musí mít přístup k portu a adresy URL popisované v [konfigurace sítě pro zajištění Funkce hybrid Runbook Worker](../../automation/automation-hybrid-runbook-worker.md#network-planning). 

Agent systému Windows a Linux podporuje komunikaci buď prostřednictvím proxy server, nebo Log Analytics bránu, aby Azure Monitor pomocí protokolu HTTPS.  Jsou podporovány anonymní i základní ověřování (uživatelské jméno/heslo).  Pro připojení přímo ke službě Windows agenta konfiguraci proxy serveru zadané během instalace nebo [po nasazení](agent-manage.md#update-proxy-settings) v Ovládacích panelech nebo pomocí Powershellu.  

Pro linuxového agenta proxy serveru zadané během instalace nebo [po instalaci](agent-manage.md#update-proxy-settings) úpravou konfiguračního souboru proxy.conf.  Hodnota konfigurace proxy agenta systému Linux má následující syntaxi:

`[protocol://][user:password@]proxyhost[:port]`

> [!NOTE]
> Pokud váš proxy server nevyžaduje ověření, agenta pro Linux ještě vyžaduje poskytnutí pseudo uživatele a hesla. To může být libovolné uživatelské jméno nebo heslo.

|Vlastnost| Popis |
|--------|-------------|
|Protocol (Protokol) | https |
|uživatel | Volitelné uživatelské jméno pro ověřování proxy serveru |
|heslo | Volitelné heslo pro ověření proxy serverem |
|proxyhost | Adresa nebo plně kvalifikovaný název domény proxy serveru/Log Analytics gateway |
|port | Volitelné nastavení portu brány proxy serveru/Log Analytics |

Příklad: `https://user01:password@proxy01.contoso.com:30443`

> [!NOTE]
> Pokud používáte speciální znaky, jako "\@" své heslo, obdržíte chybu připojení proxy server vzhledem k tomu, že hodnota je analyzovat.  Chcete-li tento problém obejít, kódování heslo v adrese URL, pomocí nástroje, jako například [URLDecode](https://www.urldecoder.org/).  

## <a name="install-and-configure-agent"></a>Instalace a konfigurace agenta

Propojení počítačů v rámci předplatného Azure nebo hybridního prostředí s využitím protokolů Azure Monitor můžete dosáhnout pomocí různých metod v závislosti na vašich požadavcích. V této tabulce najdete jednotlivé metody k určení, který nejlépe fungovat ve vaší organizaci.

|Zdroj | Metoda | Popis|
|-------|-------------|-------------|
|Virtuální počítač Azure| -Log Analytics virtuálního počítače rozšíření pro [Windows](../../virtual-machines/extensions/oms-windows.md) nebo [Linux](../../virtual-machines/extensions/oms-linux.md) pomocí rozhraní příkazového řádku Azure nebo pomocí šablony Azure Resource Manageru<br>- [Ručně z portálu Azure portal](../../azure-monitor/learn/quick-collect-azurevm.md?toc=/azure/azure-monitor/toc.json). | Rozšíření na virtuální počítače Azure nainstaluje agenta Log Analytics a zapisuje do existujícího pracovního prostoru Azure Monitor.|
| Hybridní počítač s Windows|- [Ruční instalace](agent-windows.md)<br>- [Azure Automation DSC](agent-windows.md#install-the-agent-using-dsc-in-azure-automation)<br>- [Šablony Resource Manageru pomocí služby Azure Stack](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/MicrosoftMonitoringAgent-ext-win) |Nainstalujte Microsoft Monitoring agent z příkazového řádku nebo pomocí automatizovaného metody jako je Azure Automation DSC, [System Center Configuration Manager](https://docs.microsoft.com/sccm/apps/deploy-use/deploy-applications), nebo pomocí šablony Azure Resource Manageru, pokud jste nasadili Microsoft Azure Stack ve vašem datovém centru.| 
| Hybridní počítač s Linuxem| [Ruční instalace](../../azure-monitor/learn/quick-collect-linux-computer.md)|Instalace agenta pro Linux volání skript obálky hostovaná na Githubu. | 
| System Center Operations Manager|[Integrace Operations Manageru s Log Analytics](../../azure-monitor/platform/om-agents.md) | Umožňuje konfigurovat integraci mezi protokoly Operations Manager a Azure Monitor a předávat shromážděná data z počítačů s Windows, která se vytvářejí do skupiny pro správu.|  

## <a name="next-steps"></a>Další postup

* Kontrola [zdroje dat](../../azure-monitor/platform/agent-data-sources.md) pochopit zdroje dat dostupné pro shromažďování dat ze systému Windows nebo Linux. 

* Další informace o [protokolu dotazy](../../azure-monitor/log-query/log-query-overview.md) analyzovat data shromážděná ze zdrojů dat a jejich řešení. 

* Další informace o [řešení monitorování](../../azure-monitor/insights/solutions.md) , které doplňují do Azure monitoru a také shromažďovat data do pracovního prostoru Log Analytics.
