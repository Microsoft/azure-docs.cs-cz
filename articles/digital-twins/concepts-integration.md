---
title: Integrace s dalšími službami
titleSuffix: Azure Digital Twins
description: Seznamte se s požadavky na příchozí a odchozí přenosy při nasazování digitálních vláken Azure.
author: baanders
ms.author: baanders
ms.date: 3/16/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: ba4188445f9bdf791ef760936bd27ac0f507fb4c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "84737168"
---
# <a name="integrate-azure-digital-twins-with-other-services"></a>Integrace digitálních vláken Azure s ostatními službami

Digitální vlákna Azure se obvykle používají společně s jinými službami. Pomocí [**tras událostí**](concepts-route-events.md)přijímá digitální vlákna Azure data z nadřazených služeb, jako je [IoT Hub](../iot-hub/about-iot-hub.md), které slouží k poskytování telemetrie a oznámení. Digitální vlákna Azure taky můžou směrovat data na služby pro ukládání dat, integraci pracovních postupů, analýzy a další použití prostřednictvím služeb pro příjem dat. 

## <a name="data-ingress"></a>Příchozí datové přenosy

Digitální vlákna Azure je možné řídit daty z IoT Hub. Díky tomu můžete shromažďovat telemetrii od fyzických zařízení ve vašem prostředí a zpracovávat tato data pomocí grafu digitálních vláken Azure v cloudu.

Digitální vlákna Azure nemají vestavěnou IoT Hub. Můžete použít existující IoT Hub aktuálně v produkčním prostředí, nebo nasadit novou. Získáte tak úplný přístup ke všem funkcím správy zařízení IoT Hub.

K ingestování dat z IoT Hub do digitálních vláken Azure použijte [funkci Azure](../azure-functions/functions-overview.md). Přečtěte si další informace o tomto modelu v tématu Postupy: ingestování [telemetrie z IoT Hub](how-to-ingest-iot-hub-data.md)nebo si to Vyzkoušejte sami v [kurzu digitálních vláken Azure: připojení kompletního řešení](tutorial-end-to-end.md).

## <a name="data-egress-services"></a>Služby pro výstup dat

Digitální vlákna Azure může posílat data do připojených **koncových bodů**. Podporované koncové body můžou být:
* [Centrum událostí](../event-hubs/event-hubs-about.md)
* [Event Grid](../event-grid/overview.md)
* [Service Bus](../service-bus-messaging/service-bus-messaging-overview.md)

Koncové body jsou připojené k digitálním Vlákenám Azure pomocí rozhraní API pro správu nebo Azure Portal. Přečtěte si další informace o tom, jak připojit koncový bod k digitálním Vlákenám Azure v tématu [Postupy: Správa koncových bodů a tras](how-to-manage-routes.md).

Existuje mnoho dalších služeb, kde můžete chtít data nakonec směrovat, například [Azure Storage](../storage/common/storage-introduction.md) nebo [Time Series Insights](../time-series-insights/time-series-insights-update-overview.md). Pokud chcete odesílat data do služeb, jako jsou, připojte cílovou službu ke koncovému bodu.

Pokud například používáte také [Azure Maps](../azure-maps/about-azure-maps.md) a chcete sladit umístění s dodaným [grafem](concepts-twins-graph.md)digitálních vláken Azure pomocí služby Azure, můžete použít Azure Functions s Event Grid k navázání komunikace mezi všemi službami v nasazení.

## <a name="next-steps"></a>Další kroky

Další informace o koncových bodech a událostech směrování do externích služeb:
* [Koncepty: směrování událostí digitálních vláken Azure](concepts-route-events.md)

Přečtěte si, jak nastavit digitální vlákna Azure k ingestování dat z IoT Hub:
* [Postupy: ingestování telemetrie z IoT Hub](how-to-ingest-iot-hub-data.md)
