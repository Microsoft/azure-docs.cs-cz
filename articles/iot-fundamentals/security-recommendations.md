---
title: Doporučení zabezpečení pro Azure IoT | Microsoft Docs
description: Tento článek shrnuje další kroky, které zajišťují zabezpečení ve vašem řešení Azure IoT Hub.
author: dsk-2015
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: article
ms.date: 09/19/2019
ms.author: dkshir
ms.custom: security-recommendations
ms.openlocfilehash: 7253ec04efe914f68e9798fca49233f11c5102ef
ms.sourcegitcommit: 2ed6e731ffc614f1691f1578ed26a67de46ed9c2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/19/2019
ms.locfileid: "71129798"
---
# <a name="security-recommendations-for-azure-internet-of-things-iot-deployment"></a>Doporučení zabezpečení pro nasazení Azure Internet věcí (IoT)

Tento článek obsahuje doporučení zabezpečení pro Azure App Service. Implementace těchto doporučení vám pomůže splnit vaše povinnosti zabezpečení, jak je popsáno v našem sdíleném modelu zodpovědnosti, a zvýší celkové zabezpečení vašich řešení webových aplikací. Další informace o tom, co společnost Microsoft splní zodpovědností poskytovatele služeb, najdete v článku [sdílené odpovědnosti pro cloud computing](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91/file/153019/1/Shared%20responsibilities%20for%20cloud%20computing.pdf).

Některá doporučení obsažená v tomto článku můžete automaticky monitorovat pomocí Azure Security Center. Azure Security Center je první linií obrany při ochraně vašich prostředků v Azure. Pravidelně analyzuje stav zabezpečení vašich prostředků Azure, aby identifikovala potenciální ohrožení zabezpečení. Pak vám poskytne doporučení, jak je řešit.

- Další informace o Azure Security Center doporučeních najdete v tématu [doporučení zabezpečení v Azure Security Center](../security-center/security-center-recommendations.md).
- Informace o Azure Security Center najdete v [Azure Security Center?](../security-center/security-center-intro.md)

## <a name="recommendations"></a>Doporučení

| Category | Doporučení | Komentáře | Podporováno v ASC |
|-|-|----|--|
| Obecné | Stále aktuální | Používejte nejnovější verze podporovaných platforem, programovacích jazyků, protokolů a platforem. | - |
| Obecné | Zachování ověřovacích klíčů v bezpečí | Po nasazení Udržujte ID zařízení a jejich ověřovací klíče fyzicky bezpečně. Vyhnete se tak poškození zařízení jako registrovaného zařízení. | - |
| Obecné | Pokud je to možné, používejte sady SDK zařízení | Sady SDK pro zařízení implementují celou řadu funkcí zabezpečení, například šifrování, ověřování atd., které vám pomůžou při vývoji robustní a zabezpečené aplikace zařízení. Další informace najdete v tématu [pochopení a používání sad SDK pro Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks) . | - |
| Správa identit a přístupu | Definování řízení přístupu pro centrum | [Pochopení a definování typu přístupu ke](iot-security-deployment.md#securing-the-cloud) každé komponentě bude mít vaše řešení IoT Hub, a to na základě funkcí. Povolená oprávnění jsou *Read*, *RegistryReadWrite*, *ServiceConnect*a *DeviceConnect*registru. Výchozí [zásady sdíleného přístupu ve službě IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security#access-control-and-permissions) vám pomůžou také definovat oprávnění pro jednotlivé komponenty na základě její role. | - |
| Správa identit a přístupu | Definování řízení přístupu pro back-endové služby | Data ingestovaná pomocí řešení IoT Hub můžou využívat jiné služby Azure, jako jsou [Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/), [Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/), [App Service](https://docs.microsoft.com/azure/app-service/), [Logic Apps](https://docs.microsoft.com/azure/logic-apps/)a [BLOB Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction). Ujistěte se, že rozumíte a povolíte odpovídající přístupová oprávnění pro tyto služby. | - |
| Ochrana dat | Zabezpečené ověřování zařízení | Zajistěte zabezpečenou komunikaci mezi zařízeními a centrem IoT pomocí [jedinečného klíče identity nebo tokenu zabezpečení](iot-security-deployment.md#iot-hub-security-tokens)nebo [certifikátu X. 509 na zařízení](iot-security-deployment.md#x509-certificate-based-device-authentication) pro každé zařízení. Použijte odpovídající metodu pro [použití tokenů zabezpečení na základě zvoleného protokolu (MQTT, AMQP nebo https)](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security). | - |
| Ochrana dat | Zabezpečená komunikace zařízení | IoT Hub zabezpečení připojení k zařízením pomocí standardu TLS (Transport Layer Security), který podporuje verze 1,2 a 1,0. K zajištění maximálního zabezpečení použijte protokol [TLS 1,2](https://tools.ietf.org/html/rfc5246) . | - |
| Ochrana dat | Zabezpečená komunikace služby | IoT Hub poskytuje koncové body pro připojení ke službám back-end, jako je [Azure Storage](/azure/storage/) nebo [Event Hubs](/azure/event-hubs) jenom pomocí protokolu TLS, a nezveřejňuje se žádný koncový bod na nešifrovaném kanálu. Jakmile tato data dorazí na tyto back-endové služby pro ukládání a analýzu, nezapomeňte využít příslušné metody zabezpečení a šifrování pro danou službu a chránit citlivé informace na back-endu. | - |
| Sítě | Ochrana přístupu k zařízením | Udržujte hardwarové porty ve vašich zařízeních na minimum, aby nedocházelo k nežádoucímu přístupu. Kromě toho mechanismy sestavení pro prevenci nebo detekci fyzické manipulace se zařízením. Podrobnosti najdete v článku [osvědčené postupy zabezpečení IoT](iot-security-best-practices.md) . | - |
| Sítě | Sestavit zabezpečený hardware | Zahrňte funkce zabezpečení, jako je šifrované úložiště nebo čip TPM (Trusted Platform Module), aby se zařízení a infrastruktura lépe zabezpečily. Udržujte operační systém zařízení a ovladače upgradovány na nejnovější verze a v případě, že je to možné místo, nainstalujte antivirové a antimalwarové funkce. Přečtěte si [architekturu zabezpečení IoT](iot-security-architecture.md) , abyste zjistili, jak to může přispět k zmírnění několika bezpečnostních hrozeb. | - |
| Monitorování | Monitorování neoprávněného přístupu k vašim zařízením |  Pomocí funkce protokolování v operačním systému zařízení můžete monitorovat veškerá porušení zabezpečení nebo fyzické falšování zařízení nebo jeho portů. | - |
| Monitorování | Monitorování řešení IoT z cloudu | Monitorujte celkový stav IoT Hub řešení pomocí [metrik v Azure monitor](https://docs.microsoft.com/azure/iot-hub/iot-hub-metrics). | - |
| Monitorování | Nastavení diagnostiky | Pozorně sledujte své operace protokolováním událostí ve vašem řešení a poté odesláním diagnostických protokolů Azure Monitor, abyste získali přehled o výkonu. Další informace najdete [v tématu monitorování a Diagnostika problémů ve službě IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-monitor-resource-health) . | - |

## <a name="next-steps"></a>Další kroky

V případě pokročilých scénářů, které zahrnují Azure IoT, možná budete muset zvážit další požadavky na zabezpečení. Další pokyny najdete v tématu [Architektura zabezpečení IoT](iot-security-architecture.md) .

