---
title: Doporučení zabezpečení pro Azure IoT | Microsoft Docs
description: Tento článek shrnuje další kroky, které zajišťují zabezpečení ve vašem řešení Azure IoT Hub.
author: dsk-2015
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 06/26/2019
ms.author: dkshir
ms.custom: security-recommendations
ms.openlocfilehash: 55ca189d5f4622a395ffe603d7f0d6764db82f3d
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/21/2019
ms.locfileid: "69877224"
---
# <a name="security-recommendations-for-azure-internet-of-things-iot-deployment"></a>Doporučení zabezpečení pro nasazení Azure Internet věcí (IoT)

Tento článek obsahuje doporučení zabezpečení pro služby Azure IoT. Implementace těchto doporučení vám pomůže splnit vaše povinnosti zabezpečení jako zákazník Azure IoT a zlepšit celkové zabezpečení vašich řešení IoT. Další informace o vnitřních funkcích zabezpečení poskytovaných službou Azure IoT najdete v článku [zabezpečení IoT od základů](iot-security-ground-up.md).

## <a name="general"></a>Obecné

| Doporučení | Komentáře |
|-|-|
| Stále aktuální | Používejte nejnovější verze podporovaných platforem, programovacích jazyků, protokolů a platforem. |
| Zachování ověřovacích klíčů v bezpečí | Po nasazení Udržujte ID zařízení a jejich ověřovací klíče fyzicky bezpečně. Vyhnete se tak poškození zařízení jako registrovaného zařízení. |
| Pokud je to možné, používejte sady SDK zařízení | Sady SDK pro zařízení implementují celou řadu funkcí zabezpečení, například šifrování, ověřování atd., které vám pomůžou při vývoji robustní a zabezpečené aplikace zařízení. Další informace najdete v tématu [pochopení a používání sad SDK pro Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks) . |


## <a name="identity-and-access-management"></a>Správa identit a přístupu

| Doporučení | Komentáře |
|-|-|
| Definování řízení přístupu pro centrum | [Pochopení a definování typu přístupu ke](iot-security-deployment.md#securing-the-cloud) každé komponentě bude mít vaše řešení IoT Hub, a to na základě funkcí. Povolená oprávnění jsou *Read*, *RegistryReadWrite*, *ServiceConnect*a *DeviceConnect*registru. Výchozí [zásady sdíleného přístupu ve službě IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security#access-control-and-permissions) vám pomůžou také definovat oprávnění pro jednotlivé komponenty na základě její role. |
| Definování řízení přístupu pro back-endové služby | Data ingestovaná pomocí řešení IoT Hub můžou využívat jiné služby Azure, jako jsou [Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/), [Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/), [App Service](https://docs.microsoft.com/azure/app-service/), [Logic Apps](https://docs.microsoft.com/azure/logic-apps/)a [BLOB Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction). Ujistěte se, že rozumíte a povolíte odpovídající přístupová oprávnění pro tyto služby. |


## <a name="data-protection"></a>Ochrana dat

| Doporučení | Komentáře |
|-|-|
| Zabezpečené ověřování zařízení | Zajistěte zabezpečenou komunikaci mezi zařízeními a centrem IoT pomocí jedinečného [klíče identity nebo tokenu zabezpečení](iot-security-deployment.md#iot-hub-security-tokens)nebo [certifikátu X. 509 na zařízení](iot-security-deployment.md#x509-certificate-based-device-authentication) pro každé zařízení. Použijte odpovídající metodu pro [použití tokenů zabezpečení na základě zvoleného protokolu (MQTT, AMQP nebo https)](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security). |
| Zabezpečená komunikace zařízení | IoT Hub zabezpečení připojení k zařízením pomocí standardu TLS (Transport Layer Security), který podporuje verze 1,2 a 1,0. K zajištění maximálního zabezpečení použijte protokol [TLS 1,2](https://tools.ietf.org/html/rfc5246) . |
| Zabezpečená komunikace služby | IoT Hub poskytuje koncové body pro připojení ke službám back-end, jako je [Azure Storage](/azure/storage/) nebo [Event Hubs](/azure/event-hubs) jenom pomocí protokolu TLS, a nezveřejňuje se žádný koncový bod na nešifrovaném kanálu. Jakmile tato data dorazí na tyto back-endové služby pro ukládání a analýzu, nezapomeňte využít příslušné metody zabezpečení a šifrování pro danou službu a chránit citlivé informace na back-endu. |


## <a name="networking"></a>Sítě

| Doporučení | Komentáře |
|-|-|
| Ochrana přístupu k zařízením | Udržujte hardwarové porty ve vašich zařízeních na minimum, aby nedocházelo k nežádoucímu přístupu. Kromě toho mechanismy sestavení pro prevenci nebo detekci fyzické manipulace se zařízením. Podrobnosti najdete v článku [osvědčené postupy zabezpečení IoT](iot-security-best-practices.md) . |
| Sestavit zabezpečený hardware | Zahrňte funkce zabezpečení, jako je šifrované úložiště nebo čip TPM (Trusted Platform Module), aby se zařízení a infrastruktura lépe zabezpečily. Udržujte operační systém zařízení a ovladače upgradovány na nejnovější verze a v případě, že je to možné místo, nainstalujte antivirové a antimalwarové funkce. Přečtěte si [architekturu zabezpečení IoT](iot-security-architecture.md) , abyste zjistili, jak to může přispět k zmírnění několika bezpečnostních hrozeb. |


## <a name="monitoring"></a>Monitorování

| Doporučení | Komentáře |
|-|-|
| Monitorování neoprávněného přístupu k vašim zařízením |  Pomocí funkce protokolování v operačním systému zařízení můžete monitorovat veškerá porušení zabezpečení nebo fyzické falšování zařízení nebo jeho portů. |
| Monitorování řešení IoT z cloudu | Monitorujte celkový stav IoT Hub řešení pomocí [metrik v Azure monitor](https://docs.microsoft.com/azure/iot-hub/iot-hub-metrics). |
| Nastavení diagnostiky | Pozorně sledujte své operace protokolováním událostí ve vašem řešení a poté odesláním diagnostických protokolů Azure Monitor, abyste získali přehled o výkonu. Další informace najdete [v tématu monitorování a Diagnostika problémů ve službě IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-monitor-resource-health) . |

## <a name="next-steps"></a>Další postup

V případě pokročilých scénářů, které zahrnují Azure IoT, možná budete muset zvážit další požadavky na zabezpečení. Další pokyny najdete v tématu [Architektura zabezpečení IoT](iot-security-architecture.md) .

