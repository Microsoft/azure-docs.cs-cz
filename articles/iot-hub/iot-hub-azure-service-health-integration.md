---
title: Podívejte se na službu Azure IoT Hub a stav prostředků | Microsoft Docs
description: K monitorování IoT Hub použijte Azure Service Health a Azure Resource Health
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/21/2020
ms.author: robinsh
ms.custom:
- amqp
- 'Role: Cloud Development'
- 'Role: Technical Support'
- devx-track-csharp
ms.openlocfilehash: 27fca7b76ab148fc355eb7d52ee0cbcbd3540458
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/26/2020
ms.locfileid: "92548485"
---
# <a name="check-iot-hub-service-and-resource-health"></a>Ověřit stav služby IoT Hub Service a prostředku

Služba Azure IoT Hub se integruje se [službou Azure Service Health](../service-health/overview.md) a poskytuje vám možnost monitorování stavu na úrovni služby pro IoT Hub službu a jednotlivá centra IoT. Můžete také nastavit výstrahy, které budou upozorňovány, když se změní stav služby IoT Hub nebo centra IoT. Služba Azure Service Health je kombinací tří menších služeb: Azure Resource Health, Azure Service Health a na stránce stavu Azure. Části v tomto článku popisují každou službu a její vztah k IoT Hub podrobněji.

Služba Azure Service Health vám pomůže monitorovat události na úrovni služby, jako jsou výpadky a upgrady, které by mohly ovlivnit dostupnost služby IoT Hub a vašich jednotlivých Center IoT. IoT Hub se taky integruje s Azure Monitor a poskytuje IoT Hub metriky platformy a protokoly prostředků IoT Hub, které můžete použít k monitorování provozních chyb a podmínek, ke kterým dochází v konkrétní službě IoT Hub. Další informace najdete v tématu [monitorování IoT Hub](monitor-iot-hub.md).

## <a name="check-health-of-an-iot-hub-with-azure-resource-health"></a>Zkontroluje stav služby IoT Hub pomocí Azure Resource Health

Azure Resource Health je součástí služby Azure Service Health, která sleduje stav jednotlivých prostředků. Stav služby IoT Hub můžete kontrolovat přímo z portálu.

Pokud chcete zobrazit historii stavu a stavu vašeho centra IoT pomocí portálu, postupujte podle následujících kroků:

1. V [Azure Portal](https://portal.azure.com)v Azure Portal přejít do služby IoT Hub.

1. V levém podokně v části **Podpora a řešení potíží** vyberte **Resource Health** .

    :::image type="content" source="./media/iot-hub-azure-service-health-integration/iot-hub-resource-health.png" alt-text="Stránka stavu prostředků pro Centrum IoT":::

Další informace o Azure Resource Health a způsobu interpretace dat o stavu naleznete v tématu [Resource Health přehled](../service-health/resource-health-overview.md) v dokumentaci k Azure Service Health.

Můžete také vybrat možnost **Přidat upozornění na stav prostředku** a nastavit upozornění, která se aktivují, když se změní stav služby IoT Hub. Další informace najdete v tématu [Konfigurace výstrah pro události stavu služby](../service-health/alerts-activity-log-service-notifications-portal.md) a související témata v dokumentaci Azure Service Health.

## <a name="check-health-of-iot-hubs-in-your-subscription-with-azure-service-health"></a>Projděte si stav služby IoT Hub ve vašem předplatném pomocí Azure Service Health

Pomocí Azure Service Health můžete ve svém předplatném kontrolovat stav všech rozbočovačů IoT.

Pokud chcete zjistit stav vašich Center IoT, postupujte takto:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

2. Přejděte na **Service Health**  >  **Resource Health** .

3. V rozevíracích seznamech vyberte své předplatné a pak jako typ prostředku vyberte **IoT Hub** .

Další informace o Azure Service Health a způsobu interpretace dat o stavu naleznete v tématu [Service Health přehled](../service-health/service-health-overview.md) v dokumentaci k Azure Service Health.

Informace o tom, jak nastavit výstrahy pomocí Azure Service Health, najdete v tématu [Konfigurace výstrah pro události stavu služby](../service-health/alerts-activity-log-service-notifications-portal.md) a související témata v dokumentaci k Azure Service Health.

## <a name="check-health-of-the-iot-hub-service-by-region-on-azure-status-page"></a>Zjištění stavu služby IoT Hub Service podle oblasti na stavové stránce Azure

Pokud chcete zobrazit stav IoT Hub a dalších služeb podle oblasti světa, můžete použít [stránku stavu Azure](https://status.azure.com/status). Další informace o stránce stavu Azure najdete v tématu [Přehled stavu Azure](../service-health/azure-status-overview.md) v dokumentaci k Azure Service Health.

## <a name="next-steps"></a>Další kroky

* Podrobnosti o Azure Service Health, Azure Resource Health a stavové stránce Azure najdete v tématu [Azure Service Health Service](../service-health/overview.md) .
* Popis monitorování Azure IoT Hub najdete v tématu [monitorování azure IoT Hub](monitor-iot-hub.md) .
