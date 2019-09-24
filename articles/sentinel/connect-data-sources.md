---
title: Připojit zdroje dat ke službě Azure Sentinel? | Microsoft Docs
description: Naučte se připojit zdroje dat ke službě Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: angrobe
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: overview
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: rkarlin
ms.openlocfilehash: d8d3e52882a5cde9b00bf07ded933ae4d45b454b
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2019
ms.locfileid: "71240170"
---
# <a name="connect-data-sources"></a>Připojení zdrojů dat





Aby bylo možné připojit se ke službě Azure Sentinel, musíte se nejdřív připojit ke zdrojům dat. Služba Azure Sentinel se dodává s řadou konektorů pro řešení Microsoftu, které jsou dostupné předem a poskytuje integraci v reálném čase, včetně řešení ochrany před internetovými útoky Microsoftu a Microsoft 365 zdrojů, včetně Office 365, Azure AD, Azure ATP a Microsoft Cloud App Security a další. Kromě toho jsou k dispozici Integrované konektory k širšímu ekosystému zabezpečení pro řešení jiných výrobců než Microsoftu. Pro připojení zdrojů dat k Azure Sentinel taky můžete použít také běžné formáty událostí, syslog nebo REST API.  

1. V nabídce vyberte **datové konektory**. Tato stránka vám umožní zobrazit úplný seznam konektorů, které poskytuje Azure Sentinel, a jejich stav. Vyberte konektor, který chcete připojit, a vyberte **stránku otevřít konektor**. 

   ![Sběrače dat](./media/collect-data/collect-data-page.png)

1. Na stránce konkrétní konektor se ujistěte, že jste splnili všechny předpoklady, a postupujte podle pokynů pro připojení dat ke službě Azure Sentinel. Může chvíli trvat, než se protokoly spustí synchronizace s Sentinel Azure. Po připojení se zobrazí souhrn dat v grafu **přijatá data** a stav připojení datových typů.

   ![Kolektory připojení](./media/collect-data/opened-connector-page.png)
  
1. Kliknutím na kartu **Další kroky** zobrazíte seznam předem připraveného obsahu Azure Sentinel pro konkrétní datový typ.

   ![Sběrače dat](./media/collect-data/data-insights.png)
 

## <a name="data-connection-methods"></a>Metody datového připojení

Služba Azure Sentinel podporuje následující metody datového připojení:

- **Služby Microsoftu**:<br> Služby společnosti Microsoft jsou nativně propojeny, což využívá Azure Foundation k předem připravené integraci, lze pomocí několika kliknutí připojit následující řešení:
    - [Office 365](connect-office-365.md)
    - [Protokoly a přihlášení k auditu Azure AD](connect-azure-active-directory.md)
    - [Aktivita Azure](connect-azure-activity.md)
    - [Azure AD Identity Protection](connect-azure-ad-Identity-protection.md)
    - [Azure Security Center](connect-azure-security-center.md)
    - [Azure Information Protection](connect-azure-information-protection.md)
    - [Rozšířená ochrana před internetovými útoky Azure](connect-azure-atp.md)
    - [Cloud App Security](connect-cloud-app-security.md)
    - [Události zabezpečení systému Windows](connect-windows-security-events.md) 
    - [Brána Windows Firewall](connect-windows-firewall.md)

- **Externí řešení prostřednictvím rozhraní API**: Některé zdroje dat jsou propojeny pomocí rozhraní API, která jsou poskytována připojeným zdrojem dat. Většina technologií zabezpečení obvykle poskytuje sadu rozhraní API, pomocí kterých lze načíst protokoly událostí. Rozhraní API se připojují k Azure Sentinel a shromažďují konkrétní datové typy a odesílají je do Azure Log Analytics. Mezi zařízení připojená přes rozhraní API patří:
    - [Barracuda](connect-barracuda.md)
    - [Symantec](connect-symantec.md)
- **Externí řešení prostřednictvím agenta**: Pomocí protokolu syslog prostřednictvím agenta je možné připojit Azure Sentinel ke všem dalším zdrojům dat, které mohou provádět streamování protokolů v reálném čase pomocí protokolu syslog. <br>Většina zařízení používá protokol syslog k posílání zpráv o událostech, které obsahují samotný protokol a data o protokolu. Formát protokolů se liší, ale většina zařízení podporuje standard CEF (Common Event Format). <br>Agent Azure Sentinel, který je založen na agentu Log Analytics, převede protokoly formátované CEF do formátu, který lze ingestovat pomocí Log Analytics. V závislosti na typu zařízení se agent nainstaluje buď přímo na zařízení, nebo na vyhrazený server Linux. Agent pro Linux přijímá události z procesu démona syslog přes protokol UDP, ale pokud se očekává, že počítač se systémem Linux shromáždí velký objem událostí syslog, pošle se přes protokol TCP z procesu démona syslog do agenta a tam, kde Log Analytics.
    - Brány firewall, proxy servery a koncové body:
        - [F5](connect-f5.md)
        - [Kontrolní bod](connect-checkpoint.md)
        - [Cisco ASA](connect-cisco.md)
        - [Fortinet](connect-fortinet.md)
        - [Palo Alto](connect-paloalto.md)
        - [Jiná zařízení CEF](connect-common-event-format.md)
        - [Jiná zařízení syslog](connect-syslog.md)
    - Řešení ochrany před únikem informací
    - [Poskytovatelé analýzy hrozeb](connect-threat-intelligence.md)
    - [Počítače DNS](connect-dns.md) – agent nainstalovaný přímo na počítači DNS
    - Servery Linux
    - Další cloudy
    
## Možnosti připojení agenta<a name="agent-options"></a>

Aby bylo možné připojit externí zařízení ke službě Azure Sentinel, musí být agent nasazen na vyhrazeném počítači (VM nebo místně), aby podporoval komunikaci mezi zařízením a službou Azure Sentinel. Agenta můžete nasadit automaticky nebo ručně. Automatické nasazení je dostupné jenom v případě, že váš vyhrazený počítač je nový virtuální počítač, který vytváříte v Azure. 


![CEF v Azure](./media/connect-cef/cef-syslog-azure.png)

Případně můžete agenta nasadit ručně na existující virtuální počítač Azure, na virtuální počítač v jiném cloudu nebo na místní počítač.

![Místní CEF](./media/connect-cef/cef-syslog-onprem.png)

## <a name="map-data-types-with-azure-sentinel-connection-options"></a>Mapování datových typů s možnostmi připojení Azure Sentinel


| **Datový typ** | **Jak se připojit** | **Datový konektor?** | **Komentář** |
|------|---------|-------------|------|
| AWSCloudTrail | [Připojit AWS](connect-aws.md) | V | |
| AzureActivity | Přehled [připojení aktivit](connect-azure-activity.md) a [protokolů aktivit](../azure-monitor/platform/activity-logs-overview.md) Azure| V | |
| AuditLogs | [Připojit Azure AD](connect-azure-active-directory.md)  | V | |
| SigninLogs | [Připojit Azure AD](connect-azure-active-directory.md)  | V | |
| AzureFirewall |[Diagnostika Azure](../firewall/tutorial-diagnostics.md) | V | |
| InformationProtectionLogs_CL  | [Sestavy Azure Information Protection](https://docs.microsoft.com/azure/information-protection/reports-aip)<br>[Připojit Azure Information Protection](connect-azure-information-protection.md)  | V | To obvykle používá funkci **InformationProtectionEvents** společně s datovým typem. Další informace najdete v tématu [Postup úpravy sestav a vytváření vlastních dotazů](https://docs.microsoft.com/azure/information-protection/reports-aip#how-to-modify-the-reports-and-create-custom-queries) .|
| AzureNetworkAnalytics_CL  | [Analytické schéma provozu](../network-watcher/traffic-analytics.md) [Analýza provozu](../network-watcher/traffic-analytics.md)  | | |
| CommonSecurityLog  | [Připojit CEF](connect-common-event-format.md)  | V | |
| OfficeActivity | [Připojit sadu Office 365](connect-office-365.md) | V | |
| SecurityEvents | [Připojit události zabezpečení systému Windows](connect-windows-security-events.md)  | V | Sešity nezabezpečených protokolů najdete v tématu [Nastavení sešitu nezabezpečených protokolů](https://blogs.technet.microsoft.com/jonsh/azure-sentinel-insecure-protocols-dashboard-setup/) .  |
| Syslog | [Připojit syslog](connect-syslog.md) | V | |
| Firewall webových aplikací Microsoft (WAF) – (AzureDiagnostics) |[Připojení brány firewall webových aplikací od Microsoftu](connect-microsoft-waf.md) | V | |
| SymantecICDx_CL | [Připojit Symantec](connect-symantec.md) | V | |
| ThreatIntelligenceIndicator  | [Propojit analýzu hrozeb](connect-threat-intelligence.md)  | V | |
| VMConnection <br> ServiceMapComputer_CL<br> ServiceMapProcess_CL|  [Mapa služby Azure Monitor](../azure-monitor/insights/service-map.md)<br>[Azure Monitor připojování k VIRTUÁLNÍm počítačům](../azure-monitor/insights/vminsights-onboard.md) <br> [Povolení Azure Monitorch přehledů virtuálních počítačů](../azure-monitor/insights/vminsights-enable-overview.md) <br> [Použití samostatného připojení k virtuálnímu počítači](../azure-monitor/insights/vminsights-enable-single-vm.md)<br>  [Použití při připojování prostřednictvím zásad](../azure-monitor/insights/vminsights-enable-at-scale-policy.md)| X | Sešit VM Insights  |
| DnsEvents | [Připojit DNS](connect-dns.md) | V | |
| W3CIISLog | [Připojit protokoly služby IIS](../azure-monitor/platform/data-sources-iis-logs.md)  | X | |
| WireData | [Připojení dat o kabelech](../azure-monitor/insights/wire-data.md) | X | |
| WindowsFirewall | [Připojit bránu Windows Firewall](connect-windows-firewall.md) | V | |
| AADIP SecurityAlert  | [Připojení Azure AD Identity Protection](connect-azure-ad-identity-protection.md)  | V | |
| AATP SecurityAlert  | [Připojení Azure ATP](connect-azure-atp.md) | V | |
| SecurityAlert ASC  | [Připojit Azure Security Center](connect-azure-security-center.md)  | V | |
| MCAS SecurityAlert  | [Připojit Microsoft Cloud App Security](connect-cloud-app-security.md)  | V | |
| SecurityAlert | | | |
| Sysmon (událost) | [Připojit Sysmon](https://azure.microsoft.com/blog/detecting-in-memory-attacks-with-sysmon-and-azure-security-center)<br> [Připojit události systému Windows](../azure-monitor/platform/data-sources-windows-events.md) <br> [Získání analyzátoru Sysmon](https://github.com/Azure/Azure-Sentinel/blob/master/Parsers/SysmonParser.txt)| X | Kolekce Sysmon není ve výchozím nastavení ve virtuálních počítačích nainstalovaná. Další informace o tom, jak nainstalovat agenta Sysmon, najdete v tématu [Sysmon](https://docs.microsoft.com/sysinternals/downloads/sysmon). |
| ConfigurationData  | [Automatizace inventáře virtuálních počítačů](../automation/automation-vm-inventory.md)| X | |
| ConfigurationChange  | [Automatizace sledování virtuálních počítačů](../automation/change-tracking.md) | X | |
| F5 BIG-IP | [Připojit F5 – BIG-IP](https://devcentral.f5.com/s/articles/Integrating-the-F5-BIGIP-with-Azure-Sentinel.md)  | X | |
| McasShadowItReporting  |  | X | |
| Barracuda_CL | [Připojit Barracuda](connect-barracuda.md) | V | |


## <a name="next-steps"></a>Další kroky

- Abyste mohli začít používat službu Azure Sentinel, potřebujete Microsoft Azure předplatné. Pokud předplatné nemáte, můžete si zaregistrovat [bezplatnou zkušební verzi](https://azure.microsoft.com/free/).
- Naučte se, jak začlenit [data do Azure Sentinel](quickstart-onboard.md)a [získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
