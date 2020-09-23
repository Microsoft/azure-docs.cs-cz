---
title: Připojení zdrojů dat ke službě Azure Sentinel | Microsoft Docs
description: Naučte se, jak propojit zdroje dat, jako je Microsoft 365 Defender (dříve Microsoft Threat Protection), Microsoft 365 a Office 365, Azure AD, ATP a Cloud App Security do Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/08/2020
ms.author: yelevin
ms.openlocfilehash: 63acffd16fa4374d4f8541a9d3327b29a3641f71
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90895799"
---
# <a name="connect-data-sources"></a>Připojení zdrojů dat

Jakmile povolíte Azure Sentinel, je nejdřív potřeba udělat připojení ke zdrojům dat. Služba Azure Sentinel je dodávána s řadou konektorů pro řešení Microsoftu, které jsou dostupné mimo pole, a poskytuje integraci v reálném čase, včetně Microsoft 365 Defender (dříve Microsoft Threat Protection), Microsoft 365ch zdrojů (včetně Office 365), Azure AD, Microsoft Defenderu pro identitu (dřív Azure ATP), Microsoft Cloud App Security a dalších. Kromě toho jsou k dispozici Integrované konektory k širšímu ekosystému zabezpečení pro řešení jiných výrobců než Microsoftu. Pomocí protokolu CEF (Common Event Format), syslog nebo REST-API můžete také připojit zdroje dat k Azure Sentinel.

1. V nabídce vyberte **datové konektory**. Tato stránka vám umožní zobrazit úplný seznam konektorů, které poskytuje Azure Sentinel, a jejich stav. Vyberte konektor, který chcete připojit, a vyberte **stránku otevřít konektor**. 

   ![Galerie datových konektorů](./media/collect-data/collect-data-page.png)

1. Na stránce konkrétní konektor se ujistěte, že jste splnili všechny předpoklady, a postupujte podle pokynů pro připojení dat ke službě Azure Sentinel. Může chvíli trvat, než se protokoly spustí synchronizace s Sentinel Azure. Po připojení se zobrazí souhrn dat v grafu **přijatá data** a stav připojení datových typů.

   ![Konfigurace datových konektorů](./media/collect-data/opened-connector-page.png)
  
1. Kliknutím na kartu **Další kroky** zobrazíte seznam předem připraveného obsahu Azure Sentinel pro konkrétní datový typ.

   ![Další kroky pro konektory](./media/collect-data/data-insights.png)
 

## <a name="data-connection-methods"></a>Metody datového připojení

Služba Azure Sentinel podporuje následující metody datového připojení:

- **Integrace služeb s**službami:<br> Některé služby jsou propojené nativně, jako jsou AWS a služby Microsoftu, využívají tyto služby Azure Foundation k předem připravené integraci. následující řešení je možné připojit několika kliknutími:
    - [Amazon Web Services – CloudTrail](connect-aws.md)
    - [Aktivita Azure](connect-azure-activity.md)
    - [Azure Active Directory](connect-azure-active-directory.md) – protokoly auditu a protokoly přihlášení
    - [Azure AD Identity Protection](connect-azure-ad-Identity-protection.md)
    - [Microsoft Defender pro identitu](connect-azure-atp.md) (dříve Rozšířená ochrana před internetovými útoky Azure)
    - [Azure Information Protection](connect-azure-information-protection.md)
    - [Azure Security Center](connect-azure-security-center.md) – výstrahy z řešení Azure Defenderu
    - [Cloud App Security](connect-cloud-app-security.md)
    - [Server DNS](connect-dns.md)
    - [Office 365](connect-office-365.md)
    - [Microsoft Defender pro koncový bod](connect-microsoft-defender-advanced-threat-protection.md) (dříve Rozšířená ochrana před internetovými útoky v programu Microsoft Defender)
    - [Firewall webových aplikací Microsoft](connect-microsoft-waf.md)
    - [Brána Windows Firewall](connect-windows-firewall.md)
    - [Události zabezpečení systému Windows](connect-windows-security-events.md)

- **Externí řešení prostřednictvím rozhraní API**: některé zdroje dat jsou propojené pomocí rozhraní API, která jsou k dispozici v připojeném zdroji dat. Většina technologií zabezpečení obvykle poskytuje sadu rozhraní API, pomocí kterých lze načíst protokoly událostí. Rozhraní API se připojují k Azure Sentinel a shromažďují konkrétní datové typy a odesílají je do Azure Log Analytics. Mezi zařízení připojená přes rozhraní API patří:
    
    - [Alcide kAudit](connect-alcide-kaudit.md)
    - [Barracuda WAF](connect-barracuda.md)
    - [Barracuda CloudGen Firewall](connect-barracuda-cloudgen-firewall.md)
    - [Citrix Analytics (Security)](connect-citrix-analytics.md)
    - [F5 BIG-IP](connect-f5-big-ip.md)
    - [Forcepoint DLP](connect-forcepoint-dlp.md)
    - [Protokoly Perimeteru 81](connect-perimeter-81-logs.md)
    - [Squadra Technologies secRMM](connect-squadra-secrmm.md)
    - [Symantec ICDX](connect-symantec.md)
    - [Zimperium](connect-zimperium-mtd.md)


- **Externí řešení prostřednictvím agenta**: službu Azure Sentinel je možné připojit prostřednictvím agenta k jakémukoli jinému zdroji dat, který může provádět streamování protokolů v reálném čase pomocí protokolu syslog.

    Většina zařízení používá protokol syslog k posílání zpráv o událostech, které obsahují samotný protokol a data o protokolu. Formát protokolů se liší, ale většina zařízení podporuje formátování založené na CEF pro data protokolu. 

    Agent Azure Sentinel, který je ve skutečnosti agentem Log Analytics, převede protokoly ve formátu CEF do formátu, který je možné pomocí Log Analytics ingestovat. V závislosti na typu zařízení se agent nainstaluje buď přímo na zařízení, nebo na vyhrazeném serveru pro přeposílání protokolů se systémem Linux. Agent pro Linux přijímá události z procesu démona syslog přes protokol UDP, ale pokud se očekává, že počítač se systémem Linux shromáždí velký objem událostí syslog, pošle se přes protokol TCP z procesu démona syslog do agenta a tam, kde Log Analytics.

    - **Brány firewall, proxy servery a koncové body:**
        - [AI Vectra Detect](connect-ai-vectra-detect.md)
        - [Check Point](connect-checkpoint.md)
        - [Cisco ASA](connect-cisco.md)
        - [ExtraHop Reveal(x)](connect-extrahop.md)
        - [F5 ASM](connect-f5.md)
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
    - Servery s Linuxem
    - Další cloudy
    
## <a name="agent-connection-options"></a>Možnosti připojení agenta<a name="agent-options"></a>

Aby bylo možné připojit externí zařízení ke službě Azure Sentinel, musí být agent nasazen na vyhrazeném počítači (VM nebo místně), aby podporoval komunikaci mezi zařízením a službou Azure Sentinel. Agenta můžete nasadit automaticky nebo ručně. Automatické nasazení je dostupné jenom v případě, že váš vyhrazený počítač je nový virtuální počítač, který vytváříte v Azure. 


![CEF v Azure](./media/connect-cef/cef-syslog-azure.png)

Případně můžete agenta nasadit ručně na existující virtuální počítač Azure, na virtuální počítač v jiném cloudu nebo na místní počítač.

![Místní CEF](./media/connect-cef/cef-syslog-onprem.png)

## <a name="map-data-types-with-azure-sentinel-connection-options"></a>Mapování datových typů na možnosti připojení služby Azure Sentinel


| **Datový typ** | **Jak se připojit** | **Datový konektor?** | **Komentáře** |
|------|---------|-------------|------|
| AWSCloudTrail | [Připojení AWS](connect-aws.md) | &#10003; | |
| AzureActivity | Přehled [připojení aktivit](connect-azure-activity.md) a [protokolů aktivit](../azure-monitor/platform/platform-logs-overview.md) Azure| &#10003; | |
| AuditLogs | [Připojení Azure AD](connect-azure-active-directory.md)  | &#10003; | |
| SigninLogs | [Připojení Azure AD](connect-azure-active-directory.md)  | &#10003; | |
| AzureFirewall |[Diagnostika Azure](../firewall/tutorial-diagnostics.md) | &#10003; | |
| InformationProtectionLogs_CL  | [Sestavy Azure Information Protection](https://docs.microsoft.com/azure/information-protection/reports-aip)<br>[Připojení služby Azure Information Protection](connect-azure-information-protection.md)  | &#10003; | To obvykle používá funkci **InformationProtectionEvents** společně s datovým typem. Další informace najdete v tématu [Postup úpravy sestav a vytváření vlastních dotazů](https://docs.microsoft.com/azure/information-protection/reports-aip#how-to-modify-the-reports-and-create-custom-queries) .|
| AzureNetworkAnalytics_CL  | [Analýza](../network-watcher/traffic-analytics.md) provozu [analytického schématu](../network-watcher/traffic-analytics.md) provozu  | | |
| CommonSecurityLog  | [Připojit CEF](connect-common-event-format.md)  | &#10003; | |
| OfficeActivity | [Připojení Office 365](connect-office-365.md) | &#10003; | |
| SecurityEvents | [Připojení událostí zabezpečení systému Windows](connect-windows-security-events.md)  | &#10003; | Sešity nezabezpečených protokolů najdete v tématu [Nastavení sešitu nezabezpečených protokolů](/azure/sentinel/quickstart-get-visibility#use-built-in-workbooks) .  |
| Syslog | [Připojení Syslogu](connect-syslog.md) | &#10003; | |
| Firewall webových aplikací Microsoft (WAF) – (AzureDiagnostics) |[Připojení brány firewall webových aplikací od Microsoftu](connect-microsoft-waf.md) | &#10003; | |
| SymantecICDx_CL | [Připojit Symantec](connect-symantec.md) | &#10003; | |
| ThreatIntelligenceIndicator  | [Připojení analýzy hrozeb](connect-threat-intelligence.md)  | &#10003; | |
| VMConnection <br> ServiceMapComputer_CL<br> ServiceMapProcess_CL|  [Mapa služby Azure Monitor](../azure-monitor/insights/service-map.md)<br>[Azure Monitor připojování k VIRTUÁLNÍm počítačům](../azure-monitor/insights/vminsights-onboard.md) <br> [Povolení Azure Monitorch přehledů virtuálních počítačů](../azure-monitor/insights/vminsights-enable-overview.md) <br> [Použití samostatného připojení k virtuálnímu počítači](../azure-monitor/insights/vminsights-enable-single-vm.md)<br>  [Použití při připojování prostřednictvím zásad](../azure-monitor/insights/vminsights-enable-at-scale-policy.md)| &#10007; | Sešit VM Insights  |
| DnsEvents | [Připojit DNS](connect-dns.md) | &#10003; | |
| W3CIISLog | [Připojit protokoly služby IIS](../azure-monitor/platform/data-sources-iis-logs.md)  | &#10007; | |
| WireData | [Připojení dat o kabelech](../azure-monitor/insights/wire-data.md) | &#10007; | |
| WindowsFirewall | [Připojit bránu Windows Firewall](connect-windows-firewall.md) | &#10003; | |
| AADIP SecurityAlert  | [Připojení Azure AD Identity Protection](connect-azure-ad-identity-protection.md)  | &#10003; | |
| AATP SecurityAlert  | [Připojení programu Microsoft Defender k identitě](connect-azure-atp.md) (dříve atp. Azure) | &#10003; | |
| SecurityAlert ASC  | [Připojení výstrah v programu Azure Defender](connect-azure-security-center.md) z Azure Security Center  | &#10003; | |
| MCAS SecurityAlert  | [Připojit Microsoft Cloud App Security](connect-cloud-app-security.md)  | &#10003; | |
| SecurityAlert | | | |
| Sysmon (událost) | [Připojit Sysmon](https://azure.microsoft.com/blog/detecting-in-memory-attacks-with-sysmon-and-azure-security-center)<br> [Připojit události systému Windows](../azure-monitor/platform/data-sources-windows-events.md) <br> [Získání analyzátoru Sysmon](https://github.com/Azure/Azure-Sentinel/blob/master/Parsers/Sysmon/Sysmon-v10.42-Parser.txt)| &#10007; | Kolekce Sysmon není ve výchozím nastavení ve virtuálních počítačích nainstalovaná. Další informace o tom, jak nainstalovat agenta Sysmon, najdete v tématu [Sysmon](https://docs.microsoft.com/sysinternals/downloads/sysmon). |
| ConfigurationData  | [Automatizace inventáře virtuálních počítačů](../automation/automation-vm-inventory.md)| &#10007; | |
| ConfigurationChange  | [Automatizace sledování virtuálních počítačů](../automation/change-tracking.md) | &#10007; | |
| F5 BIG-IP | [Připojení F5 BIG-IP](https://devcentral.f5.com/s/articles/Integrating-the-F5-BIGIP-with-Azure-Sentinel)  | &#10007; | |
| McasShadowItReporting  |  | &#10007; | |
| Barracuda_CL | [Připojení Barracudy](connect-barracuda.md) | &#10003; | |


## <a name="next-steps"></a>Další kroky

- Abyste mohli začít používat službu Azure Sentinel, potřebujete Microsoft Azure předplatné. Pokud nemáte předplatné, můžete si zaregistrovat [bezplatnou zkušební verzi](https://azure.microsoft.com/free/).
- Naučte se, jak začlenit [data do Azure Sentinel](quickstart-onboard.md)a [získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
