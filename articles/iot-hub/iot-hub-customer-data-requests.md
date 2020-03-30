---
title: Požadavky na zákaznická data pro zařízení Azure IoT Hub
description: Většina zařízení spravovaných v Azure IoT Hub nejsou osobní, ale některé jsou. Tento článek popisuje možnost správců exportovat nebo odstranit osobní data ze zařízení.
author: robinsh
ms.author: robinsh
ms.date: 05/16/2018
ms.topic: conceptual
ms.service: iot-hub
services: iot-hub
ms.openlocfilehash: a05fbf6e1908f88014cd8da99fafb875de033f45
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79499312"
---
# <a name="summary-of-customer-data-request-features"></a>Souhrn funkcí požadavků na zákaznická data

Azure IoT Hub je cloudová služba založená na rozhraní REST API zaměřená na podnikové zákazníky, která umožňuje zabezpečenou obousměrnou komunikaci mezi miliony zařízení a rozdělenou službou Azure.

[!INCLUDE [gdpr-related guidance](../../includes/gdpr-intro-sentence.md)]

Jednotlivým zařízením je přiřazen identifikátor zařízení (ID zařízení) správcem klienta. Data zařízení jsou založena na přiřazeném ID zařízení. Společnost Microsoft neudržuje žádné informace a nemá přístup k datům, která by umožnila ID zařízení ke korelaci uživatelů.

Mnoho zařízení spravovaných v Azure IoT Hub nejsou osobní zařízení, například kancelářský termostat nebo tovární robot. Zákazníci však mohou považovat některá zařízení za osobně identifikovatelná a podle svého uvážení mohou udržovat své vlastní metody sledování aktiv nebo inventáře, které spojují zařízení s jednotlivci. Azure IoT Hub spravuje a ukládá všechna data přidružená k zařízením, jako by to byla osobní data.

Správci tenanta můžou použít portál Azure nebo rozhraní REST služby ke splnění požadavků na informace exportem nebo odstraněním dat přidružených k ID zařízení.

Pokud používáte funkci směrování služby Azure IoT Hub k předávání zpráv zařízení do jiných služeb, pak požadavky na data musí provádět správce klienta pro každý koncový bod směrování, aby bylo možné dokončit úplný požadavek na dané zařízení. Další podrobnosti naleznete v referenční dokumentaci pro každý koncový bod. Další informace o podporovaných koncových bodech najdete [v tématu Reference - IoT Hub koncové body](iot-hub-devguide-endpoints.md).

Pokud používáte funkci integrace Azure Event Grid služby Azure IoT Hub, pak požadavky na data musí provádět správce tenanta pro každého odběratele těchto událostí. Další informace najdete v tématu [React to IoT Hub events by using Event Grid](iot-hub-event-grid.md).

Pokud k vytvoření diagnostických protokolů použijete funkci integrace Azure Monitoru služby Azure IoT Hub, musí požadavky na data provádět správce klienta proti uloženým protokolům. Další informace najdete [v tématu Sledování stavu služby Azure IoT Hub](iot-hub-monitor-resource-health.md).

## <a name="deleting-customer-data"></a>Odstranění dat zákazníků

Správci tenantů můžou pomocí okna zařízení IoT rozšíření Azure IoT Hub na webu Azure Portal odstranit zařízení, které odstraní data přidružená k tomuto zařízení.

Je také možné provádět operace odstranění pro zařízení používající rest API. Další informace naleznete v [tématu Service - Delete Device](/rest/api/iothub/service/registrymanager/deletedevice).

## <a name="exporting-customer-data"></a>Export dat zákazníků

Správci tenantů můžou využít kopírování a vkládání v podokně zařízení IoT rozšíření Azure IoT Hub na webu Azure Portal k exportu dat přidružených k zařízení.

Je také možné provádět operace exportu pro zařízení používající rest API. Další informace naleznete v [tématu Service - Get Device](/rest/api/iothub/service/registrymanager/getdevice).

> [!NOTE]
> Při použití služeb společnosti Microsoft enterprise, Microsoft generuje některé informace, označované jako protokoly generované systémem. Některé protokoly generované systémem Azure IoT Hub nejsou přístupné nebo exportovatelné správci klienta. Tyto protokoly představují věcné úkony prováděné v rámci služby a diagnostické údaje týkající se jednotlivých zařízení.

## <a name="links-to-additional-documentation"></a>Odkazy na další dokumentaci

Úplná dokumentace pro azure iot hub service api je umístěn na [ioT hub service API](https://docs.microsoft.com/rest/api/iothub/service/configuration).
