---
title: Připojení zdrojů dat ke službě Azure Sentinel | Microsoft Docs
description: Naučte se připojit zdroje dat ke službě Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: angrobe
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/04/2019
ms.author: yelevin
ms.openlocfilehash: 4060cfe08e91c87467a8ef6801adab6f027473bf
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2020
ms.locfileid: "83696863"
---
# <a name="connect-data-sources"></a>Připojení zdrojů dat

Aby bylo možné připojit se ke službě Azure Sentinel, musíte se nejdřív připojit ke zdrojům dat. Služba Azure Sentinel se dodává s řadou konektorů pro řešení Microsoftu, které jsou dostupné okamžitě a poskytuje integraci v reálném čase, včetně řešení ochrany před internetovými útoky Microsoftu a Microsoft 365 zdrojů, včetně Office 365, Azure AD, Azure ATP a Microsoft Cloud App Security a dalších. Kromě toho jsou k dispozici Integrované konektory k širšímu ekosystému zabezpečení pro řešení jiných výrobců než Microsoftu. Pro připojení zdrojů dat k Azure Sentinel taky můžete použít také běžné formáty událostí, syslog nebo REST API.  

1. V nabídce vyberte **datové konektory**. Tato stránka vám umožní zobrazit úplný seznam konektorů, které poskytuje Azure Sentinel, a jejich stav. Vyberte konektor, který chcete připojit, a vyberte **stránku otevřít konektor**. 

   ![Sběrače dat](./media/collect-data/collect-data-page.png)

1. Na stránce konkrétní konektor se ujistěte, že jste splnili všechny předpoklady, a postupujte podle pokynů pro připojení dat ke službě Azure Sentinel. Může chvíli trvat, než se protokoly spustí synchronizace s Sentinel Azure. Po připojení se zobrazí souhrn dat v grafu **přijatá data** a stav připojení datových typů.

   ![Kolektory připojení](./media/collect-data/opened-connector-page.png)
  
1. Kliknutím na kartu **Další kroky** zobrazíte seznam předem připraveného obsahu Azure Sentinel pro konkrétní datový typ.

   ![Sběrače dat](./media/collect-data/data-insights.png)
 

## <a name="data-connection-methods"></a>Metody datového připojení

Služba Azure Sentinel podporuje následující metody datového připojení:

- **Integrace služeb s**službami:<br> Některé služby jsou propojené nativně, jako jsou AWS a služby Microsoftu, využívají tyto služby Azure Foundation k předem připravené integraci. následující řešení je možné připojit několika kliknutími:
    - [Amazon Web Services – CloudTrail](connect-aws.md)
    - [Aktivita Azure](connect-azure-activity.md)
    - [Protokoly a přihlášení k auditu Azure AD](connect-azure-active-directory.md)
    - [Azure AD Identity Protection](connect-azure-ad-Identity-protection.md)
    - [Rozšířená ochrana před internetovými útoky Azure](connect-azure-atp.md)
    - [Azure Information Protection](connect-azure-information-protection.md)
    - [Azure Security Center](connect-azure-security-center.md)
    - [Cloud App Security](connect-cloud-app-security.md)
    - [Server DNS](connect-dns.md)
    - [Office 365](connect-office-365.md)
    - [Ochrana ATP v programu Microsoft Defender](connect-microsoft-defender-advanced-threat-protection.md)
    - [Firewall webových aplikací Microsoft](connect-microsoft-waf.md)
    - [Brána Windows Firewall](connect-windows-firewall.md)
    - [Události zabezpečení systému Windows](connect-windows-security-events.md)

- **Externí řešení prostřednictvím rozhraní API**: některé zdroje dat jsou propojené pomocí rozhraní API, která jsou k dispozici v připojeném zdroji dat. Většina technologií zabezpečení obvykle poskytuje sadu rozhraní API, pomocí kterých lze načíst protokoly událostí. Rozhraní API se připojují k Azure Sentinel a shromažďují konkrétní datové typy a odesílají je do Azure Log Analytics. Mezi zařízení připojená přes rozhraní API patří:
    - [Barracuda](connect-barracuda.md)
    - [Barracuda CloudGen Firewall](connect-barracuda-cloudgen-firewall.md)
    - [Citrix Analytics (Security)](connect-citrix-analytics.md)
    - [F5 BIG-IP](connect-f5-big-ip.md)
    - [Forcepoint DLP](connect-forcepoint-dlp.md)
    - [Squadra Technologies secRMM](connect-squadra-secrmm.md)
    - [Symantec ICDX](connect-symantec.md)
    - [Zimperium](connect-zimperium-mtd.md)


- **Externí řešení prostřednictvím agenta**: službu Azure Sentinel je možné připojit ke všem dalším zdrojům dat, které mohou provádět streamování protokolů v reálném čase pomocí protokolu syslog prostřednictvím agenta. <br>Většina zařízení používá protokol syslog k posílání zpráv o událostech, které obsahují samotný protokol a data o protokolu. Formát protokolů se liší, ale většina zařízení podporuje formátování založené na formátu CEF (Common Event Format) pro data protokolů. <br>Agent Azure Sentinel, který je založen na agentu Log Analytics, převede protokoly formátované CEF do formátu, který lze ingestovat pomocí Log Analytics. V závislosti na typu zařízení se agent nainstaluje buď přímo na zařízení, nebo na vyhrazený server Linux. Agent pro Linux přijímá události z procesu démona syslog přes protokol UDP, ale pokud se očekává, že počítač se systémem Linux shromáždí velký objem událostí syslog, pošle se přes protokol TCP z procesu démona syslog do agenta a tam, kde Log Analytics.
    - Brány firewall, proxy servery a koncové body:
        - [Check Point](connect-checkpoint.md)
        - [Cisco ASA](connect-cisco.md)
        - [ExtraHop Reveal(x)](connect-extrahop.md)
        - [F5](connect-f5.md)
        - [Produkty Forcepoint](connect-forcepoint-casb-ngfw.md)
        - [Fortinet](connect-fortinet.md)
        - [Palo Alto Networks](connect-paloalto.md)
        - [One Identity Safeguard](connect-one-identity.md)
        - [Jiná zařízení CEF](connect-common-event-format.md)
        - [Jiná zařízení syslog](connect-syslog.md)
        - [Trend Micro Deep Security](connect-trend-micro.md)
        - [Zscaler](connect-zscaler.md)
    - Řešení ochrany před únikem informací
    - [Poskytovatelé analýzy hrozeb](connect-threat-intelligence.md)
    - [Počítače DNS](connect-dns.md) – agent nainstalovaný přímo na počítači DNS
    - Servery Linux
    - Další cloudy
    
## <a name="agent-connection-options"></a>Možnosti připojení agenta<a name="agent-options"></a>

Aby bylo možné připojit externí zařízení ke službě Azure Sentinel, musí být agent nasazen na vyhrazeném počítači (VM nebo místně), aby podporoval komunikaci mezi zařízením a službou Azure Sentinel. Agenta můžete nasadit automaticky nebo ručně. Automatické nasazení je dostupné jenom v případě, že váš vyhrazený počítač je nový virtuální počítač, který vytváříte v Azure. 


![CEF v Azure](./media/connect-cef/cef-syslog-azure.png)

Případně můžete agenta nasadit ručně na existující virtuální počítač Azure, na virtuální počítač v jiném cloudu nebo na místní počítač.

![Místní CEF](./media/connect-cef/cef-syslog-onprem.png)

## <a name="map-data-types-with-azure-sentinel-connection-options"></a>Mapování datových typů na možnosti připojení služby Azure Sentinel


| **Datový typ** | **Jak se připojit** | **Datový konektor?** | **Komentáře** |
|------|---------|-------------|------|
| AWSCloudTrail | [Připojení AWS](connect-aws.md) | V | |
| AzureActivity | Přehled [připojení aktivit](connect-azure-activity.md) a [protokolů aktivit](../azure-monitor/platform/platform-logs-overview.md) Azure| V | |
| AuditLogs | [Připojení Azure AD](connect-azure-active-directory.md)  | V | |
| SigninLogs | [Připojení Azure AD](connect-azure-active-directory.md)  | V | |
| AzureFirewall |[Diagnostika Azure](../firewall/tutorial-diagnostics.md) | V | |
| InformationProtectionLogs_CL  | [Sestavy Azure Information Protection](https://docs.microsoft.com/azure/information-protection/reports-aip)<br>[Připojení služby Azure Information Protection](connect-azure-information-protection.md)  | V | To obvykle používá funkci **InformationProtectionEvents** společně s datovým typem. Další informace najdete v tématu [Postup úpravy sestav a vytváření vlastních dotazů](https://docs.microsoft.com/azure/information-protection/reports-aip#how-to-modify-the-reports-and-create-custom-queries) .|
| AzureNetworkAnalytics_CL  | [Analýza](../network-watcher/traffic-analytics.md) provozu [analytického schématu](../network-watcher/traffic-analytics.md) provozu  | | |
| CommonSecurityLog  | [Připojit CEF](connect-common-event-format.md)  | V | |
| OfficeActivity | [Připojení Office 365](connect-office-365.md) | V | |
| SecurityEvents | [Připojení událostí zabezpečení systému Windows](connect-windows-security-events.md)  | V | Sešity nezabezpečených protokolů najdete v tématu [Nastavení sešitu nezabezpečených protokolů](/azure/sentinel/quickstart-get-visibility#use-built-in-workbooks) .  |
| Syslog | [Připojení Syslogu](connect-syslog.md) | V | |
| Firewall webových aplikací Microsoft (WAF) – (AzureDiagnostics) |[Připojení brány firewall webových aplikací od Microsoftu](connect-microsoft-waf.md) | V | |
| SymantecICDx_CL | [Připojit Symantec](connect-symantec.md) | V | |
| ThreatIntelligenceIndicator  | [Připojení analýzy hrozeb](connect-threat-intelligence.md)  | V | |
| VMConnection <br> ServiceMapComputer_CL<br> ServiceMapProcess_CL|  [Mapa služby Azure Monitor](../azure-monitor/insights/service-map.md)<br>[Azure Monitor připojování k VIRTUÁLNÍm počítačům](../azure-monitor/insights/vminsights-onboard.md) <br> [Povolení Azure Monitorch přehledů virtuálních počítačů](../azure-monitor/insights/vminsights-enable-overview.md) <br> [Použití samostatného připojení k virtuálnímu počítači](../azure-monitor/insights/vminsights-enable-single-vm.md)<br>  [Použití při připojování prostřednictvím zásad](../azure-monitor/insights/vminsights-enable-at-scale-policy.md)| × | Sešit VM Insights  |
| DnsEvents | [Připojit DNS](connect-dns.md) | V | |
| W3CIISLog | [Připojit protokoly služby IIS](../azure-monitor/platform/data-sources-iis-logs.md)  | × | |
| WireData | [Připojení dat o kabelech](../azure-monitor/insights/wire-data.md) | × | |
| WindowsFirewall | [Připojit bránu Windows Firewall](connect-windows-firewall.md) | V | |
| AADIP SecurityAlert  | [Připojení Azure AD Identity Protection](connect-azure-ad-identity-protection.md)  | V | |
| AATP SecurityAlert  | [Připojení Azure ATP](connect-azure-atp.md) | V | |
| SecurityAlert ASC  | [Připojení služby Azure Security Center](connect-azure-security-center.md)  | V | |
| MCAS SecurityAlert  | [Připojit Microsoft Cloud App Security](connect-cloud-app-security.md)  | V | |
| SecurityAlert | | | |
| Sysmon (událost) | [Připojit Sysmon](https://azure.microsoft.com/blog/detecting-in-memory-attacks-with-sysmon-and-azure-security-center)<br> [Připojit události systému Windows](../azure-monitor/platform/data-sources-windows-events.md) <br> [Získání analyzátoru Sysmon](https://github.com/Azure/Azure-Sentinel/blob/master/Parsers/Sysmon/Sysmon-v10.42-Parser.txt)| × | Kolekce Sysmon není ve výchozím nastavení ve virtuálních počítačích nainstalovaná. Další informace o tom, jak nainstalovat agenta Sysmon, najdete v tématu [Sysmon](https://docs.microsoft.com/sysinternals/downloads/sysmon). |
| ConfigurationData  | [Automatizace inventáře virtuálních počítačů](../automation/automation-vm-inventory.md)| × | |
| ConfigurationChange  | [Automatizace sledování virtuálních počítačů](../automation/change-tracking.md) | × | |
| F5 BIG-IP | [Připojení F5 BIG-IP](https://devcentral.f5.com/s/articles/Integrating-the-F5-BIGIP-with-Azure-Sentinel)  | × | |
| McasShadowItReporting  |  | × | |
| Barracuda_CL | [Připojení Barracudy](connect-barracuda.md) | V | |


## <a name="next-steps"></a>Další kroky

- Abyste mohli začít používat službu Azure Sentinel, potřebujete Microsoft Azure předplatné. Pokud nemáte předplatné, můžete si zaregistrovat [bezplatnou zkušební verzi](https://azure.microsoft.com/free/).
- Naučte se, jak začlenit [data do Azure Sentinel](quickstart-onboard.md)a [získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
