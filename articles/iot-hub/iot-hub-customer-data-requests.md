---
title: Zákaznická data žádosti o funkce
description: Souhrnné informace o zákaznická data žádosti o funkce
author: dominicbetts
ms.author: dobett
manager: timlt
ms.date: 05/16/2018
ms.topic: conceptual
ms.service: iot-hub
services: iot-hub
ms.openlocfilehash: 862ed2b38521cac3a4c83a9dc90db78f9f030ff1
ms.sourcegitcommit: f31bfb398430ed7d66a85c7ca1f1cc9943656678
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/28/2018
ms.locfileid: "47451812"
---
# <a name="summary-of-customer-data-request-features"></a>Souhrnné informace o zákaznická data žádosti o funkce

Azure IoT Hub je založená na rozhraní REST API Cloudová služba určenou pro podnikové zákazníky, která umožňuje zabezpečenou obousměrnou komunikaci mezi miliony zařízení a oddílů služby Azure.

[!INCLUDE [gdpr-related guidance](../../includes/gdpr-intro-sentence.md)]

Jednotlivá zařízení přiřazený identifikátor zařízení (ID zařízení) správcem tenanta. Data zařízení se vycházející z ID přiděleného zařízení. Společnost Microsoft udržuje žádné informace a nemá přístup k datům, která by umožnilo ID zařízení na uživatele korelace.

Mnoho zařízení spravovaná ve službě Azure IoT Hub nejsou osobní zařízení, třeba office Termostat nebo objekt pro vytváření robota. Zákazníci mohou, ale vezměte v úvahu některé zařízení, která budou identifikovatelné osobní údaje a podle vlastního uvážení může udržovat vlastní prostředek nebo metod, které jsou zařízení, která jednotlivcům sledování inventáře. Azure IoT Hub spravuje a ukládá všechna data přidružená k zařízení, jako by šlo osobní údaje.

Správce tenanta můžete použít na webu Azure portal nebo rozhraní REST API služby ke splnění požadavků na informace o exportu nebo odstraněním data přidružená k ID zařízení.

Pokud používáte funkci směrování služby Azure IoT Hub pro předávání zpráv zařízení do jiných služeb, musí být požadavky na data provedena správcem tenanta pro každý koncový bod směrování aby bylo možné dokončit žádost o úplné pro dané zařízení. Další podrobnosti najdete v tématu referenční dokumentaci pro každý koncový bod. Další informace o podporovaných koncových bodech, najdete v části [Reference – koncové body IoT Hubu](iot-hub-devguide-endpoints.md).

Pokud používáte funkci Integrace služby Azure Event Grid služby Azure IoT Hub, musí být požadavky na data provedena správcem tenanta pro každý předplatitel tyto události. Další informace najdete v tématu [reagovat na události služby IoT Hub s využitím služby Event Grid](iot-hub-event-grid.md).

Pokud použijete k vytvoření diagnostické protokoly Azure monitoru funkci Integrace služby Azure IoT Hub, musí správce tenanta proti uložené protokoly provádět požadavky na data. Další informace najdete v tématu [monitorování stavu služby Azure IoT Hub](iot-hub-monitor-resource-health.md).

## <a name="deleting-customer-data"></a>Odstraňuje se zákaznická data

Správce tenanta můžete použít okno zařízení IoT rozšíření Azure IoT Hub na webu Azure Portal se odstranit zařízení, které odstraní data přidružená k tomuto zařízení.

Je také možné provést operace odstranění pro zařízení pomocí rozhraní REST API. Další informace najdete v tématu [Service – odstranit zařízení](/rest/api/iothub/service/deletedevice).

## <a name="exporting-customer-data"></a>Export zákaznických dat

Správce tenanta můžete využívat kopírování a vložte do okna zařízení IoT rozšíření Azure IoT Hub na webu Azure Portal k exportu data přidružená k zařízení.

Je také možné provést operacích exportu proveďte pro zařízení pomocí rozhraní REST API. Další informace najdete v tématu [Service – získání zařízení](/rest/api/iothub/service/getdevice).

> [!NOTE]
> Při použití služby enterprise od Microsoftu, Microsoft generuje určité informace, označované jako protokoly generované systémem. Některé služby Azure IoT Hub systémem generované protokoly nejsou přístupné nebo exportovat správci tenanta. Tyto protokoly představují skutečné akce provedené v rámci služby a diagnostických dat týkající se pro jednotlivá zařízení.

## <a name="links-to-additional-documentation"></a>Odkazy na další dokumentaci

Kompletní dokumentaci k rozhraní API služby Azure IoT Hub se nachází na [rozhraní API služby IoT Hub](https://docs.microsoft.com/rest/api/iothub/service).
