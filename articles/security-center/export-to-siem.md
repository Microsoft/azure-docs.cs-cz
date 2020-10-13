---
title: Streamujte výstrahy z Azure Security Center do systémů SIEM (Security Information and Event Management) a dalších řešení monitorování.
description: Naučte se streamovat výstrahy zabezpečení do Azure Sentinel, řešení systémů Siem, společnosti nebo ITSM jiných výrobců.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 10/06/2020
ms.author: memildin
ms.openlocfilehash: 0800c0d6fb2cf57b919d29ac354d2d89c06c7aeb
ms.sourcegitcommit: ba7fafe5b3f84b053ecbeeddfb0d3ff07e509e40
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/12/2020
ms.locfileid: "91946658"
---
# <a name="stream-alerts-to-a-siem-soar-or-it-service-management-solution"></a>Streamování výstrah do řešení pro správu služeb SIEM, společnosti nebo IT

Azure Security Center může streamovat výstrahy zabezpečení do nejoblíbenějších informací o zabezpečení a správy událostí (SIEM), automatizované reakce orchestrace zabezpečení (společnosti) a řešení IT Service Management (ITSM).

K dispozici jsou nástroje Azure-Native pro zajištění, že můžete zobrazit data výstrah ve všech nejoblíbenějších řešeních používaných dnes, včetně těchto:

- **Azure Sentinel**
- **Splunk Enterprise a Splunk Cloud**
- **QRadar IBM**
- **ServiceNow**
- **ArcSight**
- **Power BI**
- **Palo Alto Networks**

## <a name="stream-alerts-to-azure-sentinel"></a>Streamování výstrah do Azure Sentinel 

Security Center nativně integruje s Azure Sentinel, nativním cloudovým SIEM a společnosti řešením Azure. 

[Přečtěte si další informace o Sentinel Azure](../sentinel/overview.md).

### <a name="azure-sentinels-connectors-for-security-center"></a>Konektory Azure Sentinel pro Security Center

Azure Sentinel obsahuje integrované konektory pro Azure Security Center na úrovni předplatného a tenanta:

- [Streamování výstrah do Azure Sentinel na úrovni předplatného](../sentinel/connect-azure-security-center.md)
- [Připojení všech předplatných ve vašem tenantovi k Azure Sentinel](https://techcommunity.microsoft.com/t5/azure-sentinel/azure-security-center-auto-connect-to-sentinel/ba-p/1387539) 

### <a name="configure-ingestion-of-all-audit-logs-into-azure-sentinel"></a>Konfigurace ingestování všech protokolů auditu do Azure Sentinel 

Další možností, jak prozkoumat výstrahy Security Center v rámci Azure Sentinel, je streamovat protokoly auditu do Azure Sentinel:
    - [Připojení událostí zabezpečení systému Windows](../sentinel/connect-windows-security-events.md)
    - [Shromažďování dat ze zdrojů se systémem Linux pomocí protokolu syslog](../sentinel/connect-syslog.md)
    - [Připojit data z protokolu aktivit Azure](../sentinel/connect-azure-activity.md)

> [!TIP]
> Azure Sentinel se účtuje na základě objemu dat přijatých k analýze v rámci Azure Sentinel a uložených v pracovním prostoru Azure Monitor Log Analytics. Sentinel Azure nabízí flexibilní a předvídatelný cenový model. [Další informace najdete na stránce s cenami služby Azure Sentinel](https://azure.microsoft.com/pricing/details/azure-sentinel/).


## <a name="stream-alerts-with-microsoft-graph-security-api"></a>Streamování výstrah pomocí rozhraní API pro Microsoft Graph zabezpečení

Security Center nabízí okamžitou integraci s rozhraním API Microsoft Graph Security. Nevyžaduje se žádná konfigurace a neúčtují se žádné další náklady. 

Toto rozhraní API můžete použít ke streamování výstrah z **celého tenanta** (a dat z mnoha dalších produktů zabezpečení Microsoftu) do systémů Siem třetích stran a dalších oblíbených platforem:

- Cloud Splunk Enterprise **a Splunk**  -  [Použití Add-On Microsoft Graph Security API pro Splunk](https://splunkbase.splunk.com/app/4564/) 
- **Power BI**  -  [Připojení k rozhraní API zabezpečení Microsoft Graph v Power BI Desktop](https://docs.microsoft.com/power-bi/connect-data/desktop-connect-graph-security)
- **ServiceNow**  -  [Podle pokynů nainstalujte a nakonfigurujte aplikaci Microsoft Graph Security API z úložiště ServiceNow](https://docs.servicenow.com/bundle/orlando-security-management/page/product/secops-integration-sir/secops-integration-ms-graph/task/ms-graph-install.html)
- **QRadar**  -  [Modul podpory zařízení IBM pro Azure Security Center přes rozhraní API Microsoft Graph](https://www.ibm.com/support/knowledgecenter/SS42VS_DSM/com.ibm.dsm.doc/c_dsm_guide_ms_azure_security_center_overview.html) 
- **Palo Alto Networks**, **anomálie**, pro **hledání**, **inspark**a další [Microsoft Graph rozhraní API pro zabezpečení](https://www.microsoft.com/security/business/graph-security-api#office-MultiFeatureCarousel-09jr2ji) .

[Přečtěte si další informace o rozhraní API pro Microsoft Graph zabezpečení](https://www.microsoft.com/security/business/graph-security-api).


## <a name="stream-alerts-with-azure-monitor"></a>Streamování výstrah pomocí Azure Monitor 

Pro streamování výstrah do **ArcSight**, **Splunk**, **SumoLogic**, serverů syslog, **LogRhythm**, **LOGZ.IO platformy pro pozorování cloudu**a dalších řešení monitorování. připojení Security Center ke službě Azure monitor prostřednictvím Azure Event Hubs:

1. Umožněte [průběžný export](continuous-export.md) pro streamování Security Center výstrahy do vyhrazeného centra událostí Azure na úrovni předplatného. 
    > [!TIP]
    > Pokud to chcete udělat na úrovni skupiny pro správu pomocí Azure Policy, přečtěte si téma [Vytvoření průběžného exportu konfigurací automatizace ve velkém měřítku](continuous-export.md?tabs=azure-policy#configure-continuous-export-at-scale-using-the-supplied-policies) .

1. [Připojte centrum událostí Azure k preferovanému řešení pomocí integrovaných konektorů Azure monitor](../azure-monitor/platform/stream-monitoring-data-event-hubs.md#partner-tools-with-azure-monitor-integration).

1. Volitelně můžete streamovat nezpracované protokoly do centra událostí Azure a připojit se k preferovanému řešení. Další informace najdete v informacích o [monitorování dostupných dat](../azure-monitor/platform/stream-monitoring-data-event-hubs.md#monitoring-data-available).

> [!TIP]
> Chcete-li zobrazit schémata událostí exportovaných datových typů, navštivte [schéma událostí centra událostí](https://aka.ms/ASCAutomationSchemas).


## <a name="next-steps"></a>Další kroky

Tato stránka vysvětluje, jak zajistit, aby byla data výstrah Azure Security Center k dispozici v nástroji pro SIEM, společnosti nebo ITSM. Související materiály najdete v tématech:

- [Co je Azure Sentinel?](../sentinel/overview.md)
- [Ověřování výstrah v Azure Security Center](security-center-alert-validation.md) – ověřte, jestli jsou vaše výstrahy správně nakonfigurované.
- [Průběžně exportovat výstrahy a doporučení zabezpečení](continuous-export.md)