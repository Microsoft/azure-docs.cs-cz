---
title: Azure data zabezpečení exportovat do systému SIEM - kanálu konfigurace [Preview] | Microsoft Docs
description: Tento článek dokumenty produktu získání Azure security center protokoly server SIEM
services: security-center
documentationcenter: na
author: Barclayn
manager: MBaldwin
editor: ''
ms.assetid: ''
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/01/2018
ms.author: barclayn
ms.openlocfilehash: 7a0a72a25010952f13eb190f0e0a1a65cc6d42d3
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/09/2018
ms.locfileid: "29124829"
---
# <a name="azure-security-data-export-to-siem--pipeline-configuration-preview"></a>Azure data zabezpečení exportovat do systému SIEM - kanálu konfigurace [Preview]

Tento dokument obsahuje podrobnosti o postupu při exportu dat zabezpečení Azure Security Center na server SIEM.

Zpracování události vytvořeného pomocí služby Azure Security Center jsou publikovány do služby Azure [protokol aktivit](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md), jeden protokol typy k dispozici prostřednictvím Azure monitorování. Monitorování Azure nabízí konsolidované kanálu pro monitorování data směrování do nástroje a SIEM. K tomu je potřeba streamování tato data do centra událostí, kde ji můžete vyžádat pak do nástroje a partnera.

Tento kanál používá [Azure Monitoring jeden kanál](../monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs.md) pro získání přístupu k datům monitorování z prostředí Azure. To umožňuje snadno nastavit systémů Siem a nástrojů pro monitorování, které využívají data.

Další části popisují, jak můžete nakonfigurovat datový tok dat do centra událostí. Postup předpokládá, že už máte Azure Security Center nakonfigurované ve vašem předplatném Azure.

Podrobný přehled

![Přehled](media/security-center-export-data-to-siem/overview.png)

## <a name="what-is-the-azure-security-data-exposed-to-siem"></a>Co je Azure zabezpečení dat vystavených do systému SIEM?

V této verzi preview zveřejňujeme [výstrahy zabezpečení.](../security-center/security-center-managing-and-responding-alerts.md) V budoucích verzích jsme se zlepšit komunikaci v datové sadě s doporučení zabezpečení.

## <a name="how-to-setup-the-pipeline"></a>Jak nastavit kanál? 

### <a name="create-an-event-hub"></a>Vytvoření centra událostí 

Než začnete, budete muset [vytvoření oboru názvů Event Hubs](../event-hubs/event-hubs-create.md). Tento obor názvů a centra událostí je cílem pro všechna vaše data monitorování.

### <a name="stream-the-azure-activity-log-to-event-hubs"></a>Datový proud protokolu Azure činnosti do centra událostí

Podrobnosti najdete v článku znalostní báze [datový proud protokolu aktivit do centra událostí](../monitoring-and-diagnostics/monitoring-stream-activity-logs-event-hubs.md)

### <a name="install-a-partner-siem-connector"></a>Instalace konektoru SIEM partnera 

Směrování monitorování data do centra událostí s monitorováním Azure umožňuje snadnou integraci s partnera SIEM a nástrojů pro monitorování.

Naleznete na následující odkaz zobrazíte seznam [podporované systémů Siem](../monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs.md#what-can-i-do-with-the-monitoring-data-being-sent-to-my-event-hub)

## <a name="example-for-querying-data"></a>Příklad pro dotazování na data 

Tady je několik Splunk dotazy, které můžete použít k dat výstrah pro vyžádání obsahu:

| **Popis dotazu**                                | **Dotaz**                                                                                                                              |
|---------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------|
| Všechny výstrahy                                              | index=main Microsoft.Security/locations/alerts                                                                                         |
| Shrnout podle názvu jejich počet operací             | index = hlavní sourcetype = "amal: zabezpečení" \| operationName tabulky \| statistiky počet podle operationName                                |
| Získání informací o upozornění: čas, název, stav, ID a předplatného | index = hlavní Microsoft.Security/locations/alerts \| tabulky \_čas, properties.eventName, stavu, properties.operationId, am_subscriptionId |


## <a name="next-steps"></a>Další postup

- [Podporované systémů Siem](../monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs.md#what-can-i-do-with-the-monitoring-data-being-sent-to-my-event-hub)
- [Streamování protokolů aktivit do služby Event Hubs](../monitoring-and-diagnostics/monitoring-stream-activity-logs-event-hubs.md)
- [Výstrahy zabezpečení.](../security-center/security-center-managing-and-responding-alerts.md)