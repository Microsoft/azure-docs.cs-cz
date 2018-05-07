---
title: Shromažďování dat z prostředí s nástrojem Azure Log Analytics | Microsoft Docs
description: Toto téma vám pomůže pochopit postup shromažďování dat a monitorování počítačů, které jsou hostovány v místní nebo jiné prostředí cloudu s analýzy protokolů.
services: log-analytics
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/02/2018
ms.author: magoedte
ms.openlocfilehash: 1ac956d638be1e79547ff931ba5b0c7e5de1ae65
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/03/2018
---
# <a name="collect-data-from-computers-in-your-environment-with-log-analytics"></a>Shromažďovat data z počítačů ve vašem prostředí s analýzy protokolů

Azure Log Analytics můžete shromažďovat a provádění akcí na data z Windows nebo Linux počítačů umístěných v:

* [Virtuální počítače Azure](log-analytics-quick-collect-azurevm.md) pomocí rozšíření virtuálního počítače analýzy protokolů 
* Vašem datovém centru jako fyzických serverech nebo virtuálních počítačů
* Virtuální počítače ve službě hostovaných v cloudu jako Amazon Web Services (AWS)

Počítače, které jsou hostované ve vašem prostředí může být přímo připojen k analýze protokolů, nebo pokud jste již monitorujete tyto počítače se System Center Operations Manager 2012 R2, 2016 nebo verze 1801, můžete provést integraci Operations spravovat skupiny pro správu s Přihlaste se Analytics a dále udržovat procesy operations vaše IT služeb.  

## <a name="overview"></a>Přehled

![log-analytics-agent-direct-connect-diagram](media/log-analytics-concept-hybrid/log-analytics-on-prem-comms.png)

Před analýza a funguje na shromážděná data, musíte nejprve nainstalovat a připojit agentů pro všechny počítače, které chcete odesílat data do služby analýzy protokolů. Můžete nainstalovat agenty na místní počítače pomocí instalačního programu, příkazového řádku nebo pomocí požadovaného stavu konfigurace (DSC) ve službě Azure Automation. 

Agenta pro Linux a Windows komunikuje přes port 443 protokolu TCP odchozí službou analýzy protokolů a pokud se počítač připojí k serveru brány firewall nebo proxy server komunikovat přes Internet, přečtěte si [v části předpoklady](#prerequisites) na Porozumějte konfiguraci sítě požadované.  Pokud vaše zásady zabezpečení IT neumožňují počítače v síti pro připojení k Internetu, můžete nastavit [OMS brány](log-analytics-oms-gateway.md) a pak nakonfigurujte agenta připojit přes bránu k analýze protokolů. Agent pak může přijímat informace o konfiguraci a odesílat data shromážděná v závislosti na tom, jaká pravidla shromažďování dat a řešení, které jste povolili. 

Pokud sledujete počítači pomocí System Center 2016 - Operations Manager nebo Operations Manager 2012 R2, může být vícedomé službou analýzy protokolů pro shromažďování dat a předání do služby a bude i nadále monitorovat pomocí [nástroje Operations Manager ](log-analytics-om-agents.md). Počítače se systémem Linux monitorovány podle skupiny pro správu nástroje Operations Manager integrovaný s analýzy protokolů neobdrží konfigurace pro zdroje dat a dál shromážděná data prostřednictvím skupiny pro správu. Agent služby Windows může hlásit až čtyři pracovní prostory, zatímco agenta systému Linux podporuje pouze do jednoho pracovního prostoru generování sestav.  

Agenta pro Linux a Windows není jenom pro připojení k analýze protokolů, ale také podporuje Azure Automation hostitelů role pracovního procesu Hybrid Runbook a řešení pro správu jako je sledování změn a Správa aktualizací.  Další informace o roli hybridní pracovní proces Runbooku najdete v tématu [Azure Automation Hybrid Runbook Worker](../automation/automation-offering-get-started.md#automation-architecture-overview).  

## <a name="prerequisites"></a>Požadavky
Než začnete, zkontrolujte následující podrobnosti k ověření, že splňují minimální požadavky na systém.

### <a name="windows-operating-system"></a>Operační systém Windows
Pro agenta Windows oficiálně jsou podporovány následující verze operačního systému Windows:

* Windows Server 2008 Service Pack 1 (SP1) nebo novější
* Windows 7 SP1 a novější.

> [!NOTE]
> Agenta pro Windows podporuje jenom zabezpečení TLS (Transport Layer) 1.0 a 1.1.  

#### <a name="network-configuration"></a>Konfigurace sítě
Informace o následující seznam konfigurace proxy a firewall informace požadované pro agenta systému Windows ke komunikaci s analýzy protokolů. Přenosy jsou odchozí z vaší sítě ke službě Analýza protokolů. 

| Prostředek agenta | Porty | Obejít kontrolu protokolu HTTPS|
|----------------|-------|------------------------|
|*.ods.opinsights.azure.com |443 | Ano |
|*.oms.opinsights.azure.com | 443 | Ano | 
|*.blob.core.windows.net | 443 | Ano | 
|*.azure-automation.net | 443 | Ano | 

### <a name="linux-operating-systems"></a>Operační systémy Linux
Následující Linuxových distribucích jsou oficiálně podporované.  Agenta systému Linux mohou však spustit také na dalších distribuce, které nejsou uvedené.  Pokud není uvedeno jinak, jsou podporovány všechny verze menší pro každou hlavní verzi uvedené.  

* Linux Amazon 2012.09 k 2015.09 (x86/x64)
* CentOS Linux 5, 6 a 7 (x86/x64)  
* Oracle Linux 5, 6 a 7 (x86/x64) 
* Red Hat Enterprise Linux Server 5, 6 a 7 (x86/x64)
* Debian GNU/Linux 6, 7 a 8 (x86/x64)
* Ubuntu 12.04 LTS, 14.04 LTS, 16.04 LTS (x86/x64)
* SUSE Linux Enterprise Server 11 a 12 (x86/x64)

#### <a name="network-configuration"></a>Konfigurace sítě
Informace o následující seznam konfigurace proxy a firewall informace požadované pro Linux agenta pro komunikaci s analýzy protokolů.  

|Prostředek agenta| Porty | Směr |  
|------|---------|--------|  
|*.ods.opinsights.azure.com | Port 443 | Příchozí a odchozí|  
|*.oms.opinsights.azure.com | Port 443 | Příchozí a odchozí|  
|*.blob.core.windows.net | Port 443 | Příchozí a odchozí|  
|*.azure-automation.net | Port 443 | Příchozí a odchozí|  

Agenta systému Linux podporuje komunikaci prostřednictvím serveru proxy nebo brány OMS ke službě Analýza protokolů pomocí protokolu HTTPS.  Anonymní i základní ověřování (uživatelské jméno a heslo) jsou podporovány.  Proxy server lze zadat během instalace nebo úpravou konfiguračního souboru proxy.conf po instalaci.  

Hodnota konfigurace proxy serveru má následující syntaxi:

`[protocol://][user:password@]proxyhost[:port]`

> [!NOTE]
> Pokud proxy server nevyžaduje ověření, agenta systému Linux stále vyžaduje zadání pseudo uživatele nebo hesla. To může být jakékoli uživatelské jméno nebo heslo.

|Vlastnost| Popis |
|--------|-------------|
|Protocol (Protokol) | https |
|uživatel | Volitelné uživatelské jméno pro ověření proxy serverem |
|heslo | Volitelné heslo pro ověření proxy serverem |
|proxyhost | Adresa nebo plně kvalifikovaný název domény serveru nebo OMS proxy serveru brány |
|port | Číslo portu volitelné pro server/OMS proxy serveru brány |

Příklad: `https://user01:password@proxy01.contoso.com:30443`

> [!NOTE]
> Pokud používáte speciální znaky, jako "@" heslo, obdržíte chybu připojení proxy vzhledem k tomu, že je správně analyzovat hodnotu.  Chcete-li tento problém obejít, kódovat heslo v adrese URL pomocí některého nástroje, jako třeba [URLDecode](https://www.urldecoder.org/).  

## <a name="install-and-configure-agent"></a>Instalace a konfigurace agenta 
Připojení místní počítače přímo k Log Analytics lze provést různými způsoby v závislosti na vaše požadavky. V následující tabulce jsou vysvětlené každé metody, který nejlépe fungovat ve vaší organizaci.

|Zdroj | Metoda | Popis|
|-------|-------------|-------------|
| Počítač s Windows|- [Ruční instalace](log-analytics-agent-windows.md)<br>- [Azure Automation DSC.](log-analytics-agent-windows.md#install-the-agent-using-dsc-in-azure-automation)<br>- [Šablony správce prostředků Azure zásobníkem](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/MicrosoftMonitoringAgent-ext-win) |Nainstalujte agenta Microsoft Monitoring z příkazového řádku nebo pomocí metody automatizované například Azure Automation DSC, [System Center Configuration Manager](https://docs.microsoft.com/sccm/apps/deploy-use/deploy-applications), nebo pomocí šablony Azure Resource Manager, pokud jste nasadili Microsoft Azure zásobníku ve vašem datovém centru.| 
|Počítač s Linuxem| [Ruční instalace](log-analytics-quick-collect-linux-computer.md)|Nainstalujte agenta pro Linux volání skript obálku hostované na Githubu. | 
| System Center Operations Manager|[Integrace nástroje Operations Manager s analýzy protokolů](log-analytics-om-agents.md) | Konfigurace integrace mezi nástrojem Operations Manager a analýzy protokolů pro předávání shromažďovat data z počítačů se systémy Linux a Windows zprávy skupinu pro správu.|  

## <a name="next-steps"></a>Další postup

* Zkontrolujte [zdroje dat](log-analytics-data-sources.md) pochopit zdroje dat, která je k dispozici ke shromažďování dat ze systému Windows nebo Linux. 

* Další informace o [protokolu hledání](log-analytics-log-searches.md) analyzovat data shromážděná ze zdrojů dat a řešení. 

* Další informace o [řešení](log-analytics-add-solutions.md) , přidání funkce do analýzy protokolů a také shromažďovat data do úložiště OMS.