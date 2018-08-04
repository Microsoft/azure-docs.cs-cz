---
title: Shromažďování dat v hybridním prostředí s agentem Azure Log Analytics | Dokumentace Microsoftu
description: Toto téma vám pomůže pochopit postupy shromažďování dat a monitorování počítačů hostované v místním nebo jiné cloudové prostředí pomocí služby Log Analytics.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/03/2018
ms.author: magoedte
ms.component: na
ms.openlocfilehash: 96feb52bd5702c899faa8d845969ae8ba0995504
ms.sourcegitcommit: eaad191ede3510f07505b11e2d1bbfbaa7585dbd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/03/2018
ms.locfileid: "39495352"
---
# <a name="collect-data-in-a-hybrid-environment-with-log-analytics-agent"></a>Shromažďování dat v hybridním prostředí pomocí agenta Log Analytics

Azure Log Analytics můžete shromažďovat a reagovat na data z počítačů s operačním systémem Windows nebo Linux, používané:

* [Virtuální počítače Azure](log-analytics-quick-collect-azurevm.md) pomocí rozšíření Log Analytics pro virtuální počítač 
* Vaše datové centrum jako fyzických serverech nebo virtuálních počítačů
* Virtuální počítače v cloudové službě, například Amazon Web Services (AWS)

Počítačích hostovaných ve vašem prostředí můžete přímo připojené ke službě Log Analytics, nebo pokud už monitorujete tyto počítače pomocí System Center Operations Manager 2012 R2 nebo novější, můžete integrovat vaši skupinu pro správu Operations Manageru se službou Log Analytics a dále udržovat vaše procesy operací služeb IT.  

## <a name="overview"></a>Přehled

![log-analytics-agent-direct-connect-diagram](media/log-analytics-concept-hybrid/log-analytics-on-prem-comms.png)

Před analýzy a funguje shromážděných dat, musíte nejprve nainstalovat a připojení agentů pro všechny počítače, které chcete odesílat data do služby Log Analytics. Můžete nainstalovat agenty na počítače v místním prostředí pomocí instalačního programu, příkazového řádku, nebo s Desired State Configuration (DSC) ve službě Azure Automation. 

Agenta pro Linux a Windows komunikuje přes TCP port 443 odchozí ke službě Log Analytics a pokud se počítač připojí k serveru brány firewall nebo proxy server komunikovat přes Internet, zkontrolujte níže uvedené požadavky pro lepší pochopení konfigurace sítě povinné.  Pokud zásady zabezpečení IT neumožňují počítače v síti pro připojení k Internetu, můžete nastavit [bránu OMS](log-analytics-oms-gateway.md) a potom nakonfigurujte agenta připojit přes bránu do Log Analytics. Agenta můžete zobrazit informace o konfiguraci a odeslat data shromážděná v závislosti na tom, jaká pravidla shromažďování dat a řešeními, která jste povolili. 

Pokud monitorování počítače pomocí System Center Operations Manager 2012 R2 nebo novější, může být s více adresami pomocí služby Log Analytics pro shromažďování dat a předat službě a stále má sledovat [nástroje Operations Manager](log-analytics-om-agents.md). Počítače se systémem Linux monitorovány podle skupiny pro správu nástroje Operations Manager integrovaný s Log Analytics neobdrží konfigurace pro zdroje dat a předat dál shromážděná data prostřednictvím skupiny pro správu. Windows agent může hlásit až čtyři pracovní prostory, zatímco agenta pro Linux podporuje jenom sestavy do jednoho pracovního prostoru.  

Agenta pro Linux a Windows není jenom pro připojení ke službě Log Analytics, ale také podporuje Azure Automation k hostiteli role pracovního procesu Hybrid Runbook a správu řešení, jako je řešení Change Tracking a Update Management.  Další informace o roli pracovního procesu Hybrid Runbook Worker, naleznete v tématu [Azure Automation Hybrid Runbook Worker](../automation/automation-hybrid-runbook-worker.md).  

## <a name="supported-windows-operating-systems"></a>Podporované operační systémy Windows
Pro agenta Windows se oficiálně podporuje následující verze operačního systému Windows:

* Windows Server 2008 Service Pack 1 (SP1) nebo novější
* Windows 7 SP1 a novější.

## <a name="supported-linux-operating-systems"></a>Podporované operační systémy a Linux
Následující Linuxových distribucích se oficiálně podporuje.  Agenta pro Linux může také spustit na jiné distribuce není uvedená.  Pokud není uvedeno jinak, jsou podporovány všechny dílčí verze pro všechny hlavní verze uvedené.  

* Linux Amazon 2012.09 k 2015.09 (x86/x64)
* Linux centOS 5, 6 a 7 (x86/x64)  
* Oracle Linux 5, 6 a 7 (x86/x64) 
* Red Hat Enterprise Linux Server 5, 6 a 7 (x86/x64)
* Debian GNU/Linux 6, 7 a 8 (x86/x64)
* Ubuntu 12.04 LTS, 14.04 LTS, 16.04 LTS (x86/x64)
* SUSE Linux Enterprise Server 11 a 12 (x86/x64)

## <a name="tls-12-protocol"></a>Protokol TLS 1.2
– Pomáhat zajistit zabezpečení dat při přenosu do služby Log Analytics, důrazně doporučujeme, abyste ke konfiguraci agenta pro použití s alespoň zabezpečení TLS (Transport Layer) 1.2. Starší verze z protokolu TLS/Secure Sockets Layer (SSL) bylo zjištěno ohrožen a stále aktuálně fungují povolit zpětnou kompatibilitu, ale jsou **ale nedoporučený krok**.  Další informace najdete v tématu [odesílání dat pomocí protokolu TLS 1.2](log-analytics-data-security.md#sending-data-securely-using-tls-12). 

## <a name="network-firewall-requirements"></a>Požadavky na bránu firewall sítě
Informace o pod seznamem proxy a firewallu informace o konfiguraci vyžadované pro systémy Linux a Windows agenta ke komunikaci se službou Log Analytics.  

|Prostředek agenta|Porty |Směr |Obejít kontrolu protokolu HTTPS|
|------|---------|--------|--------|   
|*.ods.opinsights.azure.com |Port 443 |Příchozí a odchozí|Ano |  
|*.oms.opinsights.azure.com |Port 443 |Příchozí a odchozí|Ano |  
|*.blob.core.windows.net |Port 443 |Příchozí a odchozí|Ano |  
|*.azure-automation.net |Port 443 |Příchozí a odchozí|Ano |  


Pokud budete chtít použít Azure Automation Hybrid Runbook Worker a připojte se k registraci ve službě Automation použití sad runbook ve vašem prostředí, musí mít přístup k portu a adresy URL popisované v [konfigurace sítě pro zajištění Funkce hybrid Runbook Worker](../automation/automation-hybrid-runbook-worker.md#network-planning). 

Agent Windows a Linuxu podporuje komunikaci prostřednictvím serveru proxy nebo bránou OMS ke službě Log Analytics pomocí protokolu HTTPS.  Jsou podporovány anonymní i základní ověřování (uživatelské jméno/heslo).  Pro připojení přímo ke službě Windows agenta konfiguraci proxy serveru zadané během instalace nebo [po nasazení](log-analytics-agent-manage.md#update-proxy-settings) v Ovládacích panelech nebo pomocí Powershellu.  

Pro linuxového agenta proxy serveru zadané během instalace nebo [po instalaci](log-analytics-agent-manage.md#update-proxy-settings) úpravou konfiguračního souboru proxy.conf.  Hodnota konfigurace proxy agenta systému Linux má následující syntaxi:

`[protocol://][user:password@]proxyhost[:port]`

> [!NOTE]
> Pokud váš proxy server nevyžaduje ověření, agenta pro Linux ještě vyžaduje poskytnutí pseudo uživatele a hesla. To může být libovolné uživatelské jméno nebo heslo.

|Vlastnost| Popis |
|--------|-------------|
|Protocol (Protokol) | https |
|uživatel | Volitelné uživatelské jméno pro ověřování proxy serveru |
|heslo | Volitelné heslo pro ověření proxy serverem |
|proxyhost | Adresa nebo plně kvalifikovaný název domény serveru/OMS proxy serveru brány |
|port | Volitelné nastavení portu pro server/OMS proxy serveru brány |

Příklad: `https://user01:password@proxy01.contoso.com:30443`

> [!NOTE]
> Pokud používáte speciální znaky, jako "\@" své heslo, obdržíte chybu připojení proxy server vzhledem k tomu, že hodnota je analyzovat.  Chcete-li tento problém obejít, kódování heslo v adrese URL, pomocí nástroje, jako například [URLDecode](https://www.urldecoder.org/).  

## <a name="install-and-configure-agent"></a>Instalace a konfigurace agenta 
Připojení místních počítačů přímo k Log Analytics můžete provést pomocí různých metod v závislosti na vašich požadavcích. V této tabulce najdete jednotlivé metody k určení, který nejlépe fungovat ve vaší organizaci.

|Zdroj | Metoda | Popis|
|-------|-------------|-------------|
| Počítač s Windows|- [Ruční instalace](log-analytics-agent-windows.md)<br>- [Azure Automation DSC](log-analytics-agent-windows.md#install-the-agent-using-dsc-in-azure-automation)<br>- [Šablony Resource Manageru pomocí služby Azure Stack](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/MicrosoftMonitoringAgent-ext-win) |Nainstalujte Microsoft Monitoring agent z příkazového řádku nebo pomocí automatizovaného metody jako je Azure Automation DSC, [System Center Configuration Manager](https://docs.microsoft.com/sccm/apps/deploy-use/deploy-applications), nebo pomocí šablony Azure Resource Manageru, pokud jste nasadili Microsoft Azure Stack ve vašem datovém centru.| 
|Počítač s Linuxem| [Ruční instalace](log-analytics-quick-collect-linux-computer.md)|Instalace agenta pro Linux volání skript obálky hostovaná na Githubu. | 
| System Center Operations Manager|[Integrace Operations Manageru s Log Analytics](log-analytics-om-agents.md) | Konfigurace integrace mezi Operations Managerem a Log Analytics, aby předával data shromážděná z počítačů se systémy Linux a Windows odesílajících sestavy do skupiny pro správu.|  

## <a name="next-steps"></a>Další postup

* Kontrola [zdroje dat](log-analytics-data-sources.md) pochopit zdroje dat dostupné pro shromažďování dat ze systému Windows nebo Linux. 

* Další informace o [prohledávání protokolů](log-analytics-log-searches.md) analyzovat data shromážděná ze zdrojů dat a jejich řešení. 

* Další informace o [řešení](log-analytics-add-solutions.md) , které doplňují do Log Analytics a také shromažďovat data v úložišti OMS.
