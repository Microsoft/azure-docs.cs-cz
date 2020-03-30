---
title: Připojení zdrojů dat k Azure Sentinelu | Dokumenty společnosti Microsoft
description: Zjistěte, jak připojit zdroje dat k Azure Sentinelu.
services: sentinel
documentationcenter: na
author: yelevin
manager: angrobe
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: overview
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/04/2019
ms.author: yelevin
ms.openlocfilehash: 9d2d1985b23e1c7f5e0f7d9fd2795bd85e28ace0
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "79240083"
---
# <a name="connect-data-sources"></a>Připojení zdrojů dat

Chcete-li na palubě Azure Sentinel, musíte se nejprve připojit ke zdrojům dat. Azure Sentinel je dodáván s řadou konektorů pro řešení Microsoft, které jsou k dispozici iz v krabici a poskytují integraci v reálném čase, včetně řešení Microsoft Threat Protection, a zdrojů Microsoft 365, včetně Office 365, Azure AD, Azure ATP a Microsoft Cloud App Security a další. Kromě toho existují integrované konektory pro širší ekosystém zabezpečení pro řešení jiných společností než Microsoft. Můžete také použít běžný formát událostí, Syslog nebo REST-API pro připojení zdrojů dat s Azure Sentinel také.  

1. V nabídce vyberte **Datové konektory**. Tato stránka umožňuje zobrazit úplný seznam konektorů, které Poskytuje Azure Sentinel a jejich stav. Vyberte spojnici, kterou chcete připojit, a vyberte **Otevřít stránku konektoru**. 

   ![Sběrače dat](./media/collect-data/collect-data-page.png)

1. Na stránce konkrétní konektor, ujistěte se, že jste splnili všechny požadavky a postupujte podle pokynů pro připojení dat k Azure Sentinelu. Může chvíli trvat, než protokoly spustí synchronizaci s Azure Sentinel. Po připojení se zobrazí souhrn dat v grafu **data přijatá** a stav připojení datových typů.

   ![Připojit kolektory](./media/collect-data/opened-connector-page.png)
  
1. Kliknutím na kartu **Další kroky** získáte seznam obsahu, který azure sentinel poskytuje pro konkrétní datový typ.

   ![Sběrače dat](./media/collect-data/data-insights.png)
 

## <a name="data-connection-methods"></a>Metody datového připojení

Azure Sentinel podporuje následující metody datového připojení:

- **Služba integrace služeb**:<br> Některé služby jsou připojeny nativně, jako jsou služby AWS a Microsoft, tyto služby využívají základ Azure pro integraci předem připravenou integrací, následující řešení lze připojit několika kliknutími:
    - [Amazon webové služby - CloudTrail](connect-aws.md)
    - [Aktivita Azure](connect-azure-activity.md)
    - [Protokoly auditu azure a dioda a přihlášení](connect-azure-active-directory.md)
    - [Azure AD Identity Protection](connect-azure-ad-Identity-protection.md)
    - [Rozšířená ochrana před internetovými útoky Azure](connect-azure-atp.md)
    - [Azure Information Protection](connect-azure-information-protection.md)
    - [Azure Security Center](connect-azure-security-center.md)
    - [Cloud App Security](connect-cloud-app-security.md)
    - [Server DNS](connect-dns.md)
    - [Office 365](connect-office-365.md)
    - [Microsoft Defender ATP](connect-microsoft-defender-advanced-threat-protection.md)
    - [Firewall webových aplikací Microsoft](connect-microsoft-waf.md)
    - [Brána Windows Firewall](connect-windows-firewall.md)
    - [Události zabezpečení systému Windows](connect-windows-security-events.md)

- **Externí řešení prostřednictvím rozhraní API**: Některé zdroje dat jsou propojeny pomocí rozhraní API, která jsou poskytována připojeným zdrojem dat. Většina technologií zabezpečení obvykle poskytuje sadu api, přes které lze načíst protokoly událostí. Api se připojují k Azure Sentinelu a shromažďují konkrétní datové typy a odesílají je do Azure Log Analytics. Mezi spotřebiče připojené prostřednictvím ROZHRANÍ API patří:
    - [Barracuda](connect-barracuda.md)
    - [Barracuda CloudGen Firewall](connect-barracuda-cloudgen-firewall.md)
    - [Citrix Analytics (Security)](connect-citrix-analytics.md)
    - [F5 BIG-IP](connect-f5-big-ip.md)
    - [Forcepoint DLP](connect-forcepoint-dlp.md)
    - [Squadra Technologies secRMM](connect-squadra-secrmm.md)
    - [Symantec ICDX](connect-symantec.md)
    - [Zimperium](connect-zimperium-mtd.md)


- **Externí řešení prostřednictvím agenta**: Azure Sentinel lze připojit ke všem ostatním zdrojům dat, které můžete provádět streamování protokolů v reálném čase pomocí protokolu Syslog prostřednictvím agenta. <br>Většina zařízení používá protokol Syslog k odesílání zpráv událostí, které obsahují samotný protokol a data o protokolu. Formát protokolů se liší, ale většina zařízení podporuje formát společného formátu událostí (CEF) na základě formátování dat protokolů. <br>Agent Azure Sentinel, který je založen na agenta Analýzy protokolů, převede protokoly ve formátu CEF do formátu, který může být ingestován službou Log Analytics. V závislosti na typu zařízení je agent nainstalován buď přímo na zařízení, nebo na vyhrazeném serveru Linux. Agent pro Linux přijímá události od daemonu Syslog přes UDP, ale pokud se očekává, že počítač SLinux shromažďovat velký objem událostí Syslog, jsou odesílány přes TCP z demonu Syslog agentovi a odtud do Log Analytics.
    - Brány firewall, proxy servery a koncové body:
        - [Check Point](connect-checkpoint.md)
        - [Cisco ASA](connect-cisco.md)
        - [ExtraHop Reveal(x)](connect-extrahop.md)
        - [F5](connect-f5.md)
        - [Produkty Forcepoint](connect-forcepoint-casb-ngfw.md)
        - [Fortinet](connect-fortinet.md)
        - [Palo Alto Networks](connect-paloalto.md)
        - [One Identity Safeguard](connect-one-identity.md)
        - [Ostatní zařízení cef](connect-common-event-format.md)
        - [Ostatní zařízení Syslog](connect-syslog.md)
        - [Trend Micro Deep Security](connect-trend-micro.md)
        - [Zscaler](connect-zscaler.md)
    - Řešení DLP
    - [Poskytovatelé informací o hrozbách](connect-threat-intelligence.md)
    - [DNS stroje](connect-dns.md) - agent instalovaný přímo na DNS počítači
    - Linuxové servery
    - Ostatní mraky
    
## <a name="agent-connection-options"></a>Možnosti připojení agenta<a name="agent-options"></a>

Chcete-li připojit externí zařízení k Azure Sentinelu, musí být agent nasazen na vyhrazeném počítači (VM nebo místně) pro podporu komunikace mezi zařízením a Azure Sentinelem. Agenta můžete nasadit automaticky nebo ručně. Automatické nasazení je k dispozici jenom v případě, že váš vyhrazený počítač je nový virtuální počítač, který vytváříte v Azure. 


![CeF v Azure](./media/connect-cef/cef-syslog-azure.png)

Případně můžete agenta nasadit ručně na existující virtuální počítač Azure, na virtuálním počítači v jiném cloudu nebo na místním počítači.

![CeF v prostorách](./media/connect-cef/cef-syslog-onprem.png)

## <a name="map-data-types-with-azure-sentinel-connection-options"></a>Mapování datových typů na možnosti připojení služby Azure Sentinel


| **Datový typ** | **Jak se připojit** | **Datový konektor?** | **Komentáře** |
|------|---------|-------------|------|
| AWSCloudTrail | [Připojení AWS](connect-aws.md) | V | |
| AzureActivity | [Propojení](connect-azure-activity.md) přehled [protokolů](../azure-monitor/platform/platform-logs-overview.md) aktivit a aktivit Azure| V | |
| Auditní protokoly | [Připojení Azure AD](connect-azure-active-directory.md)  | V | |
| SigninLogs | [Připojení Azure AD](connect-azure-active-directory.md)  | V | |
| AzureFirewall |[Diagnostika Azure](../firewall/tutorial-diagnostics.md) | V | |
| InformationProtectionLogs_CL  | [Sestavy Azure Information Protection](https://docs.microsoft.com/azure/information-protection/reports-aip)<br>[Připojení služby Azure Information Protection](connect-azure-information-protection.md)  | V | To obvykle používá **InformationProtectionEvents** funkce kromě datového typu. Další informace naleznete [v tématu Jak upravit sestavy a vytvořit vlastní dotazy](https://docs.microsoft.com/azure/information-protection/reports-aip#how-to-modify-the-reports-and-create-custom-queries)|
| AzureNetworkAnalytics_CL  | [Analýza provozu s analýzou](../network-watcher/traffic-analytics.md) [provozu Analýza provozu](../network-watcher/traffic-analytics.md)  | | |
| CommonSecurityLog  | [Připojit CeF](connect-common-event-format.md)  | V | |
| OfficeActivity | [Připojení Office 365](connect-office-365.md) | V | |
| Události zabezpečení | [Připojení událostí zabezpečení systému Windows](connect-windows-security-events.md)  | V | V sešitech Nezabezpečené protokoly naleznete v tématu [Nastavení sešitu Nezabezpečené protokoly](/azure/sentinel/quickstart-get-visibility#use-built-in-workbooks)  |
| Syslog | [Připojení Syslogu](connect-syslog.md) | V | |
| Brána firewall webových aplikací společnosti Microsoft (WAF) – (AzureDiagnostics) |[Připojení brány firewall webových aplikací společnosti Microsoft](connect-microsoft-waf.md) | V | |
| SymantecICDx_CL | [Připojení společnosti Symantec](connect-symantec.md) | V | |
| ThreatIntelligenceIndicator  | [Připojení analýzy hrozeb](connect-threat-intelligence.md)  | V | |
| Připojení virtuálního připojení <br> ServiceMapComputer_CL<br> ServiceMapProcess_CL|  [Mapa služby Azure Monitor](../azure-monitor/insights/service-map.md)<br>[Registrace přehledů virtuálních počítačích Azure Monitor](../azure-monitor/insights/vminsights-onboard.md) <br> [Povolení přehledů virtuálních montovek Azure Monitor](../azure-monitor/insights/vminsights-enable-overview.md) <br> [Použití jednoho virtuálního počítače při nástupu](../azure-monitor/insights/vminsights-enable-single-vm.md)<br>  [Používání on-boardingu prostřednictvím zásad](../azure-monitor/insights/vminsights-enable-at-scale-policy.md)| × | Sešit Přehledy virtuálních měn  |
| DnsEvents | [Připojení služby DNS](connect-dns.md) | V | |
| W3CIISLog | [Připojení protokolů služby IIS](../azure-monitor/platform/data-sources-iis-logs.md)  | × | |
| Data drátu | [Připojení dat drátu](../azure-monitor/insights/wire-data.md) | × | |
| Brána WindowsFirewall | [Připojení brány Windows Firewall](connect-windows-firewall.md) | V | |
| Bezpečnostní výstraha AADIP  | [Připojení Azure AD Identity Protection](connect-azure-ad-identity-protection.md)  | V | |
| Výstraha zabezpečení AATP  | [Připojení Azure ATP](connect-azure-atp.md) | V | |
| Výstraha zabezpečení asc  | [Připojení služby Azure Security Center](connect-azure-security-center.md)  | V | |
| Výstraha zabezpečení mcas  | [Připojení zabezpečení aplikací Microsoft Cloud](connect-cloud-app-security.md)  | V | |
| Výstraha zabezpečení | | | |
| Sysmon (Událost) | [Připojit Sysmon](https://azure.microsoft.com/blog/detecting-in-memory-attacks-with-sysmon-and-azure-security-center)<br> [Připojit události systému Windows](../azure-monitor/platform/data-sources-windows-events.md) <br> [Získejte Sysmon analyzátor](https://github.com/Azure/Azure-Sentinel/blob/master/Parsers/SysmonParser.txt)| × | Kolekce Sysmon není nainstalována ve výchozím nastavení na virtuálních počítačích. Další informace o instalaci agenta Sysmon naleznete v tématu [Sysmon](https://docs.microsoft.com/sysinternals/downloads/sysmon). |
| ConfigurationData  | [Automatizace inventáře virtuálních montovna](../automation/automation-vm-inventory.md)| × | |
| ConfigurationChange  | [Automatizace sledování virtuálních mís](../automation/change-tracking.md) | × | |
| F5 BIG-IP | [Připojení F5 BIG-IP](https://devcentral.f5.com/s/articles/Integrating-the-F5-BIGIP-with-Azure-Sentinel)  | × | |
| McasShadowItReporting  |  | × | |
| Barracuda_CL | [Připojení Barracudy](connect-barracuda.md) | V | |


## <a name="next-steps"></a>Další kroky

- Abyste mohli začít pracovat s Azure Sentinelem, potřebujete předplatné Microsoft Azure. Pokud nemáte předplatné, můžete si zaregistrovat [bezplatnou zkušební verzi](https://azure.microsoft.com/free/).
- Zjistěte, jak [založit data do Azure Sentinelu](quickstart-onboard.md)a [získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
