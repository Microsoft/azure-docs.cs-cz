---
title: Funkce požadavek dat zákazníka
author: dominicbetts
ms.author: dobett
ms.date: 05/16/2018
ms.topic: conceptual
ms.service: iot-hub
ms.openlocfilehash: 3af76fe22e93b6c5d502733196994bda61b9a93e
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
---
# <a name="summary-of-customer-data-request-features"></a>Souhrn funkcí požadavek data zákazníků

Azure IoT Hub je založené na REST API Cloudová služba zaměřený na podnikových zákazníků, která umožňuje zabezpečený, obousměrnou komunikaci mezi miliony zařízení a oddílů služby Azure.

[!INCLUDE [gdpr-intro-sentence](../../includes/gdpr-intro-sentence.md)]

Jednotlivých zařízení se přiřadí identifikátor zařízení (ID zařízení) pomocí Správce klienta. Data ze zařízení podle ID přiřazená zařízení. Společnost Microsoft udržuje žádné informace a nemá přístup k datům, které by umožnilo zařízení ID korelace uživatele.

Mnoho zařízení spravovaných v Azure IoT Hub nejsou osobní zařízení, například služby office Termostat nebo vytváření robot. Zákazníci, však zvažte některá zařízení jako osobní identifikovatelné a podle svého uvážení mohou zachovat své vlastní prostředek nebo inventáře sledování metody, které tie zařízení jednotlivcům. Azure IoT Hub spravuje a ukládá všechna data přidružená k zařízení, jako by šlo osobní data.

Správci klienta můžete použít portál Azure nebo rozhraní API služby REST ke splnění požadavků na informace o exportu nebo odstraněním data související s ID zařízení.

Pokud používáte funkci směrování služby Azure IoT Hub pro předávání zpráv zařízení k jiným službám, musí být požadavky na data provést správcem klienta pro každý koncový bod směrování aby bylo možné dokončit žádost o úplné pro dané zařízení. V tématu referenční dokumentaci každý koncový bod pro další podrobnosti. Další informace o podporovaných koncových bodů najdete v tématu [odkaz – koncové body centra IoT](iot-hub-devguide-endpoints.md).

Pokud používáte funkci Azure událostí mřížky integrace služby Azure IoT Hub, musí být požadavky na data provést správcem klienta pro každou odběratele z těchto událostí. Další informace najdete v tématu [reagování na události služby IoT Hub pomocí mřížky událostí](iot-hub-event-grid.md).

Pokud používáte funkci monitorování Azure integrace služby Azure IoT Hub pro vytvoření diagnostické protokoly, musí správce klienta uložené protokoly provést požadavky na data. Další informace najdete v tématu [monitorování stavu služby Azure IoT Hub](iot-hub-monitor-resource-health.md).

## <a name="deleting-customer-data"></a>Odstraňování dat zákazníka

Správci klienta můžete použít okno zařízení IoT rozšíření Azure IoT Hub v portálu Azure k odstranění zařízení, které odstraní data přidružená k tomuto zařízení.

Také je možné provádět operace delete pro zařízení pomocí rozhraní REST API. Další informace najdete v tématu [rozhraní Api pro zařízení – odstranit zařízení](https://docs.microsoft.com/rest/api/iothub/deviceapi/deletedevice).

## <a name="exporting-customer-data"></a>Export dat zákazníka

Správci klienta můžete využívat kopírování a vložení v rámci okna zařízení IoT rozšíření Azure IoT Hub v portálu Azure exportovat data přidružená k zařízení.

Také je možné provádět operace exportu pro zařízení pomocí rozhraní REST API. Další informace najdete v tématu [rozhraní Api pro zařízení - získat zařízení](https://docs.microsoft.com/rest/api/iothub/deviceapi/getdevice).

> [!NOTE]
> Při použití služby enterprise společnosti Microsoft generuje některé informace, které jsou známé jako protokoly generované systémem. Některé protokoly generované systémem Azure IoT Hub nejsou přístupné nebo exportovatelný Správci klienta. Tyto protokoly tvoří konkrétní akce prováděné v rámci služby a diagnostická data související s jednotlivých zařízení.

## <a name="links-to-additional-documentation"></a>Odkazy na další dokumentaci

Úplnou dokumentaci k rozhraní API služby Azure IoT Hub zařízení se nachází v [ https://docs.microsoft.com/rest/api/iothub/deviceapi ](https://docs.microsoft.com/rest/api/iothub/deviceapi).