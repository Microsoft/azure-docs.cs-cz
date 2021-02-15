---
title: Referenční dokumentace schématu zdroje dat služby Azure Sentinel
description: V tomto článku jsou uvedená schémata zdrojů dat Azure a třetích stran podporovaná službou Azure Sentinel s odkazy na jejich referenční dokumentaci.
author: batamig
ms.author: bagol
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: reference
ms.custom: ''
ms.date: 01/14/2021
ms.openlocfilehash: b5d53ec6c6a8002c72a53d6928d56e55d520ef38
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2021
ms.locfileid: "100390823"
---
# <a name="data-source-schema-reference"></a>Referenční dokumentace schématu zdroje dat

Tento článek obsahuje seznam podporovaných schémat zdrojů dat Azure a třetích stran s odkazy na jejich referenční dokumentaci.

## <a name="azure-data-sources"></a>Zdroje dat Azure

| Typ                             | Zdroj dat             | Log Analytics TableName | Odkaz na schéma |
| -------------------------------- | ---------------------- | ---------------------- | ---------------- |
| **Azure**                            | Azure Active Directory | SigninEvents           | [Vlastnosti přihlášení sestav aktivit služby Azure AD](/graph/api/resources/signin#properties) |
| **Azure**                            | Azure Active Directory | AuditLogs              | [Odkaz na Azure Monitor AuditLogs](/azure/azure-monitor/reference/tables/auditlogs) |
| **Azure**                            | Azure Active Directory | AzureActivity          | [Odkaz na Azure Monitor AzureActivity](/azure/azure-monitor/reference/tables/azureactivity) |
| **Azure**                            | Office                 | OfficeActivity         | Schémata rozhraní API pro činnost správy Office 365: <br>- [Společné schéma ](/office/office-365-management-api/office-365-management-activity-api-schema#common-schema)   <br>- [Schéma správce Exchange ](/office/office-365-management-api/office-365-management-activity-api-schema#exchange-admin-schema) <br>- [Schéma poštovní schránky serveru Exchange](/office/office-365-management-api/office-365-management-activity-api-schema#exchange-mailbox-schema)  <br>- [Základní schéma služby SharePoint](/office/office-365-management-api/office-365-management-activity-api-schema#sharepoint-base-schema)   <br>- [Operace se soubory SharePointu](/office/office-365-management-api/office-365-management-activity-api-schema#sharepoint-file-operations) |
| **Azure**                            | Azure Key Vault         | AzureDiagnostics       | [Odkaz na Azure Monitor AzureDiagnostics](/azure/azure-monitor/reference/tables/azurediagnostics) |
| **Hostitel**                             | Linux                  | Syslog                 | [Odkaz na Azure Monitor syslog](/azure/azure-monitor/reference/tables/syslog) |
| **Síť**                          | Protokoly IIS               | W3CIISLog              | [Odkaz na Azure Monitor W3CIISLog](/azure/azure-monitor/reference/tables/w3ciislog) |
| **Síť**                          | VMinsights             | VMConnection           | [Odkaz na Azure Monitor VMConnection](/azure/azure-monitor/reference/tables/vmconnection) |
| **Síť**                          | Řešení pro přenos dat     | WireData               | [Odkaz na Azure Monitor WireData](/azure/azure-monitor/reference/tables/wiredata) |
| **Síť**                          | Protokoly toku NSG          | AzureNetworkAnalytics  | [Agregace schématu a dat v Analýza provozu](/azure/network-watcher/traffic-analytics-schema) |
| | | | |

> [!NOTE]
> Další informace najdete v tématu celý [Azure monitor odkaz na data](/azure/azure-monitor/reference/).
>
## <a name="3rd-party-vendor-data-sources"></a>zdroje dat dodavatelů třetích stran

Následující tabulka uvádí podporované dodavatele třetích stran a jejich protokol syslog nebo Common Event Format (CEF) – dokumentace k mapování pro různé podporované typy protokolů, které obsahují mapování polí CEF a ukázkové protokoly pro každý typ kategorie.

| Typ |    Dodavatel |    Produkt | Log Analytics TableName | Odkaz na pole CEF – mapování  |
| ----- | ----- | ----- | ----- |----- |
| **Síť** | Palo Alto   | OPERAČNÍ SYSTÉM PAN    | CommonSecurityLog |   [Průvodce integrací běžné události s operačním systémem 9,0](https://docs.paloaltonetworks.com/content/dam/techdocs/en_US/pdf/cef/pan-os-90-cef-configuration-guide.pdf) (hledání *formátů protokolu ve stylu CEF*) |
| **Síť** | Check Point  |ALL   | CommonSecurityLog | [Popis polí protokolu](https://supportcenter.checkpoint.com/supportcenter/portal?eventSubmit_doGoviewsolutiondetails=&solutionid=sk109795)       |
| **Síť** | Fortigate   | ALL   | CommonSecurityLog | [Struktura schématu protokolu](https://docs.fortinet.com/document/fortigate/6.2.3/fortios-log-message-reference/738142/log-schema-structure)         |
| **Síť** | Barracuda | Firewall webových aplikací |  CommonSecurityLog   | [Jak nakonfigurovat syslog a další protokoly](https://campus.barracuda.com/product/webapplicationfirewall/doc/4259935/how-to-configure-syslog-and-other-logs/)  |
| **Síť** | Cisco | ASA | CommonSecurityLog | [Zprávy syslog pro řady Cisco ASA](https://www.cisco.com/c/en/us/td/docs/security/asa/syslog/b_syslog/about.html)    |
| **Síť** | Cisco | Firepower   | CommonSecurityLog | [Zprávy syslogu pro ochranu před hrozbami Cisco FirePOWER](https://www.cisco.com/c/en/us/td/docs/security/firepower/Syslogs/b_fptd_syslog_guide.pdf)    |
| **Síť** | Cisco   | Sluneční  | Vlastní tabulka protokolů  | [Formáty a správa verzí protokolů](https://docs.umbrella.com/deployment-umbrella/docs/log-formats-and-versioning)   |
| **Síť**   | Cisco | Meraki    | CommonSecurityLog |   [Typy událostí syslog a ukázky protokolů](https://documentation.meraki.com/zGeneral_Administration/Monitoring_and_Reporting/Syslog_Event_Types_and_Log_Samples)    |
| **Síť**   | Zscaler | Služba pro streamování nano (NSS)|   CommonSecurityLog | [Formátování informačních kanálů NSS](https://help.zscaler.com/zia/documentation-knowledgebase/analytics/nss/nss-feeds/formatting-nss-feeds) (jenom protokoly web, firewall, DNS a Tunnel) |
| **Síť**   |F5 | Big-IP LTM|    CommonSecurityLog|  [Zprávy o událostech a typy útoků](https://techdocs.f5.com/kb/en-us/products/big-ip_ltm/manuals/product/bigip-external-monitoring-implementations-13-0-0/15.html)  |
| **Síť** | F5  | Big-IP ASM|    CommonSecurityLog|  [Protokolování událostí zabezpečení aplikace](https://techdocs.f5.com/kb/en-us/products/big-ip_asm/manuals/product/asm-implementations-13-1-0/14.html)                                                           |
| **Síť** | Citrix  |Firewall webových aplikací   | CommonSecurityLog|    [Podpora protokolování CEF (Common Event Format) v bráně firewall pro aplikace](https://support.citrix.com/article/CTX136146) <br>  [Odkaz na zprávu protokolu syslog pro NetScaler 12,0](https://developer-docs.citrix.com/projects/netscaler-syslog-message-reference/en/12.0/)   |
|**Hostitel** |Symantec | Symantec Endpoint Protection Manager (SEPM) | CommonSecurityLog|[Nastavení externího protokolování a úrovně závažnosti událostí protokolu pro Endpoint Protection Manager](https://support.symantec.com/us/en/article.tech171741.html)|
|**Hostitel** |Trend Micro |Vše |CommonSecurityLog | [Mapování obsahu syslog – CEF](https://docs.trendmicro.com/en-us/enterprise/control-manager-70/appendices/syslog-mapping-cef.aspx) |
| | | | | |

## <a name="next-steps"></a>Další kroky

Další informace o podporovaných konektorech Sentinel Azure, jako jsou CEF, syslog, Direct, agent a vlastní konektory:

- [Připojení zdrojů dat](connect-data-sources.md)

- [Azure Sentinel syslog, CEF a další konektory třetích stran](https://techcommunity.microsoft.com/t5/azure-sentinel/azure-sentinel-syslog-cef-and-other-3rd-party-connectors-grand/ba-p/803891)