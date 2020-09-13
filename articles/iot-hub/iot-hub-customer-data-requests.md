---
title: Žádosti o zákaznická data pro zařízení Azure IoT Hub
description: Většina zařízení spravovaných v Azure IoT Hub není osobní, ale některé jsou. Tento článek se domluví o správcích, kteří můžou exportovat nebo odstranit osobní údaje ze zařízení.
author: robinsh
ms.author: robinsh
ms.date: 05/16/2018
ms.topic: conceptual
ms.service: iot-hub
services: iot-hub
ms.openlocfilehash: ebc73c75d9a23a53ac94f28208251dcff83e684a
ms.sourcegitcommit: 3fc3457b5a6d5773323237f6a06ccfb6955bfb2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2020
ms.locfileid: "90030697"
---
# <a name="customer-data-request-features-for-azure-iot-hub-devices"></a>Funkce pro žádosti o zákaznických datech pro zařízení Azure IoT Hub

Azure IoT Hub je cloudová služba založená na REST API, která je zaměřená na podnikové zákazníky, která umožňuje zabezpečenou obousměrnou komunikaci mezi miliony zařízení a službou Azure dělenou službou.

[!INCLUDE [gdpr-related guidance](../../includes/gdpr-intro-sentence.md)]

Jednotlivým zařízením je přiřazen identifikátor zařízení (ID zařízení) správce klienta. Data zařízení jsou založená na ID přiřazeného zařízení. Společnost Microsoft nespravuje žádné informace a nemá přístup k datům, která by umožňovala korelaci uživatelů s ID zařízení.

Mnohé ze zařízení spravovaných v Azure IoT Hub nejsou osobní zařízení, například Kancelářský termostat nebo robot pro tovární navýšení. Zákazníci si ale můžou všimnout, že některá zařízení budou identifikovatelné osobně a na základě jejich uvážení můžou udržovat své vlastní prostředky nebo metody sledování inventáře, které propojují zařízení s jednotlivci. Azure IoT Hub spravuje a ukládá všechna data, která jsou přidružená k zařízením, jako by šlo o osobní údaje.

Správci klientů můžou pomocí Azure Portal nebo rozhraní REST API služby vyplnit žádosti o informace tím, že exportují nebo odstraňují data přidružená k ID zařízení.

Pokud používáte funkci směrování služby Azure IoT Hub k přeposílání zpráv zařízení na jiné služby, musí být požadavky na data provedeny správcem tenanta pro každý koncový bod směrování, aby bylo možné dokončit úplný požadavek na dané zařízení. Další podrobnosti najdete v referenční dokumentaci pro každý koncový bod. Další informace o podporovaných koncových bodech naleznete v tématu [IoT Hub koncových bodů reference](iot-hub-devguide-endpoints.md).

Pokud používáte funkci Azure Event Grid Integration služby Azure IoT Hub, musí být požadavky na data provedeny správcem tenanta pro každého předplatitele těchto událostí. Další informace najdete v tématu [reakce na události IoT Hub pomocí Event Grid](iot-hub-event-grid.md).

Pokud používáte funkci Azure Monitor Integration služby Azure IoT Hub k vytváření protokolů diagnostiky, musí být požadavky na data provedeny správcem klienta proti uloženým protokolům. Další informace najdete v tématu [monitorování stavu Azure IoT Hub](iot-hub-monitor-resource-health.md).

## <a name="deleting-customer-data"></a>Odstraňují se zákaznická data

Správci klientů mohou pomocí okna zařízení IoT v rozšíření Azure IoT Hub v Azure Portal odstranit zařízení, které odstraní data přidružená k tomuto zařízení.

Je také možné provádět operace odstranění pro zařízení pomocí rozhraní REST API. Další informace najdete v tématu [Služba – odstranění zařízení](/azure/iot-hub/iot-c-sdk-ref/iothub-registrymanager-h/iothubregistrymanager-deletedevice).

## <a name="exporting-customer-data"></a>Export zákaznických dat

Správci klientů můžou využít kopírování a vkládání v podokně zařízení IoT rozšíření Azure IoT Hub v Azure Portal k exportu dat přidružených k zařízení.

Je také možné provádět operace exportu pro zařízení pomocí rozhraní REST API. Další informace najdete v tématu [Služba – získat zařízení](/azure/iot-hub/iot-c-sdk-ref/iothub-registrymanager-h/iothubregistrymanager-getdevice).

> [!NOTE]
> Když použijete podnikové služby společnosti Microsoft, vygeneruje společnost Microsoft nějaké informace, které se nazývají systémem generované protokoly. Některé protokoly generované systémem Azure IoT Hub nejsou pro správce klientů k dispozici nebo je můžou exportovat. Tyto protokoly představují věcné akce prováděné v rámci služby a diagnostická data související s jednotlivými zařízeními.

## <a name="links-to-additional-documentation"></a>Odkazy na další dokumentaci

Úplná dokumentace pro rozhraní API služby Azure IoT Hub se nachází v [rozhraních API služby IoT Hub](https://docs.microsoft.com/rest/api/iothub/service/configuration).
