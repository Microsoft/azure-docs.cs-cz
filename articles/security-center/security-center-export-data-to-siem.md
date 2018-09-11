---
title: Azure data zabezpečení export do SIEM – konfigurace kanálu [Preview] | Dokumentace Microsoftu
description: Tento článek dokumenty produkci získání Azure security center protokolů do SIEM
services: security-center
documentationcenter: na
author: Barclayn
manager: MBaldwin
editor: ''
ms.assetid: ''
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/01/2018
ms.author: barclayn
ms.openlocfilehash: aede60a729fe9c0594ded485e189c0b467e34271
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2018
ms.locfileid: "44298229"
---
# <a name="azure-security-data-export-to-siem--pipeline-configuration-preview"></a>Azure data zabezpečení export do SIEM – konfigurace kanálu [Preview]

Tento dokument obsahuje podrobnosti o postupu při exportu dat zabezpečení Azure Security Center do SIEM.

Zpracované události vytvořené službou Azure Security Center se publikují do Azure [protokolu aktivit](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md), jeden z protokolu typy dostupné v nástroji Azure Monitor. Azure Monitor nabízí konsolidované kanálu pro směrování všech dat monitorování do nástroje SIEM. To se provádí Streamovat data do centra událostí, kde ji potom je mohly vyžádat do nástroje partnera.

Tento kanál používá [Azure Monitoring jeden kanál](../monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs.md) pro získání přístupu k data monitorování z vašeho prostředí Azure. To umožňuje snadno nastavit sady Siem a monitorovací nástroje tato data využívají.

Tento oddíl popisuje, jak nakonfigurovat data Streamovat do centra událostí. Kroky předpokládají, že už máte Azure Security Center nakonfigurovat ve svém předplatném Azure.

Podrobný přehled

![Základní přehled](media/security-center-export-data-to-siem/overview.png)

## <a name="what-is-the-azure-security-data-exposed-to-siem"></a>Co je Azure security data vystavená do systému SIEM?

V této verzi preview zveřejňujeme [výstrahy zabezpečení.](../security-center/security-center-managing-and-responding-alerts.md) V nadcházejících vydáních jsme se rozšířit datové sady s doporučení týkající se zabezpečení.

## <a name="how-to-setup-the-pipeline"></a>Jak nastavit kanál? 

### <a name="create-an-event-hub"></a>Vytvoření centra událostí 

Než začnete, budete muset [vytvořte obor názvů služby Event Hubs](../event-hubs/event-hubs-create.md). Tento obor názvů a centra událostí je cílem pro všemi daty monitorování.

### <a name="stream-the-azure-activity-log-to-event-hubs"></a>Stream protokolů aktivit Azure do služby Event Hubs

Najdete v následujícím článku [streamování protokolu aktivit do služby Event Hubs](../monitoring-and-diagnostics/monitoring-stream-activity-logs-event-hubs.md)

### <a name="install-a-partner-siem-connector"></a>Nainstalovat partnerský konektor SIEM 

Směrování dat monitorování do centra událostí pomocí Azure Monitor umožňuje snadnou integraci s partnerem SIEM a monitorovací nástroje.

Odkazovat na následující odkaz zobrazíte seznam [podporovány systémy Siem](../monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs.md#what-can-i-do-with-the-monitoring-data-being-sent-to-my-event-hub)

## <a name="example-for-querying-data"></a>Příklad pro dotazování na data 

Tady je několik Splunk dotazů, které můžete použít k načítání dat výstrah:

| **Popis dotazu**                                | **Dotaz**                                                                                                                              |
|---------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------|
| Všechny výstrahy                                              | index = hlavní Microsoft.Security/locations/alerts                                                                                         |
| Shrnout počet operací podle názvu             | index = hlavní sourcetype = "amal: zabezpečení" \| tabulky operationName \| statistiky count podle operationName                                |
| Získání informací o upozornění: čas, název, stavu, ID a předplatné | index = hlavní Microsoft.Security/locations/alerts \| tabulky \_čas, properties.eventName, stavu, properties.operationId, am_subscriptionId |


## <a name="next-steps"></a>Další postup

- [Podporovaných sad Siem](../monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs.md#what-can-i-do-with-the-monitoring-data-being-sent-to-my-event-hub)
- [Streamování protokolů aktivit do služby Event Hubs](../monitoring-and-diagnostics/monitoring-stream-activity-logs-event-hubs.md)
- [Výstrahy zabezpečení.](../security-center/security-center-managing-and-responding-alerts.md)