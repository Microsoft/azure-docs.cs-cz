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
ms.date: 07/01/2019
ms.author: magoedte
ms.openlocfilehash: 583845b2ea63efd42f382c9c150de650f34bafed
ms.sourcegitcommit: 6cb4dd784dd5a6c72edaff56cf6bcdcd8c579ee7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2019
ms.locfileid: "67514101"
---
# <a name="collect-log-data-with-the-log-analytics-agent"></a>Shromažďování dat protokolu agenta Log Analytics

Agenta Azure Log Analytics, dříve označované jako agenta Microsoft Monitoring Agent (MMA) nebo OMS Linux byla vyvinuta pro komplexní správu napříč místním počítačům počítačů monitorovaných [System Center Operations Manager ](https://docs.microsoft.com/system-center/scom/), virtuálních počítačů a v kterémkoliv cloudu. Agenti Windows a Linuxem připojit k Azure Monitor a ukládání shromážděných dat protokolu z různých zdrojů v pracovního prostoru Log Analytics a také všechny jedinečné protokolů nebo metrik, jak jsou definovány v řešení pro monitorování. 

Tento článek obsahuje podrobný přehled o agent, system a požadavky na síť a různých metodách nasazení.   

## <a name="overview"></a>Přehled

![Log Analytics agenta komunikace diagramu](./media/log-analytics-agent/log-analytics-agent-01.png)

Před analýzy a funguje shromážděných dat, musíte nejprve nainstalovat a připojení agentů pro všechny počítače, které chcete odesílat data do služby Azure Monitor. Můžete nainstalovat agenty na virtuální počítače Azure pomocí rozšíření virtuálního počítače Azure Log Analytics pro Windows a Linux a pro počítače v hybridním prostředí pomocí instalačního programu, příkazového řádku, nebo s Desired State Configuration (DSC) ve službě Azure Automation. 

Agenta pro Linux a Windows komunikuje přes TCP port 443 odchozí do služby Azure Monitor a pokud se počítač připojuje prostřednictvím brány firewall nebo proxy serveru pro komunikaci přes Internet, zkontrolujte níže uvedené požadavky pro lepší pochopení konfigurace sítě povinné. Pokud zásady zabezpečení IT neumožňují počítače v síti pro připojení k Internetu, můžete nastavit [Log Analytics gateway](gateway.md) a potom nakonfigurujte agenta připojit přes bránu na protokoly Azure monitoru. Agenta můžete zobrazit informace o konfiguraci a odeslat data shromážděná v závislosti na tom, jaká data kolekce pravidel a monitorování řešení jste povolili ve vašem pracovním prostoru. 

Pokud jsou monitorování počítače pomocí System Center Operations Manager 2012 R2 nebo novější, může být s více adresami pomocí služby Azure Monitor pro shromažďování dat a předat službě a stále má sledovat [nástroje Operations Manager](../../azure-monitor/platform/om-agents.md). Počítači se systémem Linux agent neobsahuje součást služby stavu agenta Windows nemá a informace se shromažďují a zpracovává pomocí serveru pro správu na jejím jménem. Protože počítače se systémem Linux jsou monitorovány jinak než pomocí nástroje Operations Manager, nejsou přijímat konfigurace nebo shromažďovat data přímo a vpřed mezi skupinou pro správu jako spravované pomocí agentů systému Windows. Proto tento scénář není podporován s Linuxem počítačích odesílajících sestavy do nástroje Operations Manager a je nutné nakonfigurovat počítač s Linuxem pro [sestavy pro skupinu pro správu nástroje Operations Manager](../platform/agent-manage.md#configure-agent-to-report-to-an-operations-manager-management-group) a pracovní prostor Log Analytics ve dvou jednotlivé kroky.

Windows agent může hlásit až čtyři pracovních prostorů Log Analytics, zatímco agenta pro Linux podporuje jenom sestavy do jednoho pracovního prostoru.  

Agenta pro Linux a Windows není jenom pro připojení k Azure Monitor, ale také podporuje Azure Automation k hostování role pracovního procesu Hybrid Runbook a dalším službám, jako [řešení Change Tracking](../../automation/change-tracking.md), [Update Management](../../automation/automation-update-management.md), a [Azure Security Center](../../security-center/security-center-intro.md). Další informace o roli pracovního procesu Hybrid Runbook Worker, naleznete v tématu [Azure Automation Hybrid Runbook Worker](../../automation/automation-hybrid-runbook-worker.md).  

## <a name="supported-windows-operating-systems"></a>Podporované operační systémy Windows
Pro agenta Windows se oficiálně podporuje následující verze operačního systému Windows:

* Windows Server 2019
* Windows Server 2008 R2, 2012, 2012 R2, 2016, verze 1709 a 1803
* Windows 7 SP1 a novější

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
* SUSE Linux Enterprise Server 12 (x 64) a 15 (x 64)

>[!NOTE]
>OpenSSL 1.1.0 je podporována pouze na platformách x86_x64 (64 bitů) a OpenSSL dříve, než 1.x se nepodporuje na libovolné platformě.
>

### <a name="agent-prerequisites"></a>Požadavky agenta

Následující tabulka obsahuje balíčky požadované pro podporovaných distribucích systému Linux, který se nainstaluje agenta na.

|Požadovaný balíček |Popis |Minimální verze |
|-----------------|------------|----------------|
|Glibc |    Knihovna GNU C | 2.5-12 
|Openssl    | Knihovny OpenSSL | 1.0.x nebo 1.1.x |
|Curl | cURL webového klienta | 7.15.5 |
|Python ctypes | | 
|PAM | Moduly PAM | | 

>[!NOTE]
>Rsyslog nebo syslog-ng je potřeba shromažďovat zprávy syslog. Démon procesu syslog výchozí verze 5 Red Hat Enterprise Linux, CentOS a Oracle Linux verze (sysklog) není podporována pro shromažďování událostí protokolu syslog. Pro shromažďování syslogu z této verze těchto distribucí, by měla být nainstalovaná a nakonfigurovaná pro nahradit sysklog proces démona řešení rsyslog.

## <a name="tls-12-protocol"></a>Protokol TLS 1.2
– Pomáhat zajistit zabezpečení dat při přenosu do protokolů Azure Monitor, důrazně doporučujeme, abyste ke konfiguraci agenta pro použití s alespoň zabezpečení TLS (Transport Layer) 1.2. Starší verze z protokolu TLS/Secure Sockets Layer (SSL) bylo zjištěno ohrožen a stále aktuálně fungují povolit zpětnou kompatibilitu, ale jsou **ale nedoporučený krok**.  Další informace najdete v tématu [odesílání dat pomocí protokolu TLS 1.2](../../azure-monitor/platform/data-security.md#sending-data-securely-using-tls-12). 

## <a name="network-firewall-requirements"></a>Požadavky na bránu firewall sítě
Informace o pod seznamem proxy a firewallu informace o konfiguraci vyžadované pro systémy Linux a Windows agenta pro komunikaci s protokoly Azure monitoru.  

|Prostředek agenta|Porty |Směr |Obejít kontrolu protokolu HTTPS|
|------|---------|--------|--------|   
|*.ods.opinsights.azure.com |Port 443 |Odchozí|Ano |  
|*.oms.opinsights.azure.com |Port 443 |Odchozí|Ano |  
|*.blob.core.windows.net |Port 443 |Odchozí|Ano |  
|*.azure-automation.net |Port 443 |Odchozí|Ano |  

Informace o bráně firewall vyžadované pro Azure Government, najdete v části [správy Azure Government](../../azure-government/documentation-government-services-monitoringandmanagement.md#azure-monitor-logs). 

Pokud budete chtít použít Azure Automation Hybrid Runbook Worker a připojte se k registraci ve službě Automation použití sad runbook ve vašem prostředí, musí mít přístup k portu a adresy URL popisované v [konfigurace sítě pro zajištění Funkce hybrid Runbook Worker](../../automation/automation-hybrid-runbook-worker.md#network-planning). 

Agent Windows a Linuxu podporuje komunikaci prostřednictvím serveru proxy nebo brány Log Analytics pro monitorování Azure pomocí protokolu HTTPS.  Jsou podporovány anonymní i základní ověřování (uživatelské jméno/heslo).  Pro připojení přímo ke službě Windows agenta konfiguraci proxy serveru zadané během instalace nebo [po nasazení](agent-manage.md#update-proxy-settings) v Ovládacích panelech nebo pomocí Powershellu.  

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
Připojování počítačů v předplatném Azure nebo hybridního prostředí přímo s protokoly Azure monitoru lze provést různými způsoby v závislosti na vašich požadavcích. V této tabulce najdete jednotlivé metody k určení, který nejlépe fungovat ve vaší organizaci.

|Zdroj | Metoda | Popis|
|-------|-------------|-------------|
|Virtuální počítač Azure| -Log Analytics virtuálního počítače rozšíření pro [Windows](../../virtual-machines/extensions/oms-windows.md) nebo [Linux](../../virtual-machines/extensions/oms-linux.md) pomocí rozhraní příkazového řádku Azure nebo pomocí šablony Azure Resource Manageru<br>- [Ručně z portálu Azure portal](../../azure-monitor/learn/quick-collect-azurevm.md?toc=/azure/azure-monitor/toc.json). | Rozšíření na virtuální počítače Azure nainstaluje agenta Log Analytics a zapisuje do existujícího pracovního prostoru Azure Monitor.|
| Hybridní počítač s Windows|- [Ruční instalace](agent-windows.md)<br>- [Azure Automation DSC](agent-windows.md#install-the-agent-using-dsc-in-azure-automation)<br>- [Šablony Resource Manageru pomocí služby Azure Stack](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/MicrosoftMonitoringAgent-ext-win) |Nainstalujte Microsoft Monitoring agent z příkazového řádku nebo pomocí automatizovaného metody jako je Azure Automation DSC, [System Center Configuration Manager](https://docs.microsoft.com/sccm/apps/deploy-use/deploy-applications), nebo pomocí šablony Azure Resource Manageru, pokud jste nasadili Microsoft Azure Stack ve vašem datovém centru.| 
| Hybridní počítač s Linuxem| [Ruční instalace](../../azure-monitor/learn/quick-collect-linux-computer.md)|Instalace agenta pro Linux volání skript obálky hostovaná na Githubu. | 
| System Center Operations Manager|[Integrace Operations Manageru s Log Analytics](../../azure-monitor/platform/om-agents.md) | Konfigurace integrace mezi Operations Manageru a Azure Monitor protokoly předávat data shromážděná z Windows počítačích odesílajících sestavy do skupiny pro správu.|  

## <a name="next-steps"></a>Další postup

* Kontrola [zdroje dat](../../azure-monitor/platform/agent-data-sources.md) pochopit zdroje dat dostupné pro shromažďování dat ze systému Windows nebo Linux. 

* Další informace o [protokolu dotazy](../../azure-monitor/log-query/log-query-overview.md) analyzovat data shromážděná ze zdrojů dat a jejich řešení. 

* Další informace o [řešení monitorování](../../azure-monitor/insights/solutions.md) , které doplňují do Azure monitoru a také shromažďovat data do pracovního prostoru Log Analytics.
