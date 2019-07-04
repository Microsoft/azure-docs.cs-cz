---
title: Doporučení zabezpečení pro Azure IoT | Dokumentace Microsoftu
description: Tento článek shrnuje další kroky k zajištění zabezpečení v rámci vašeho řešení Azure IoT Hub.
author: dsk-2015
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 06/26/2019
ms.author: dkshir
ms.openlocfilehash: d079e082fb8ac90f398e46f283bd1e33e2b4ab40
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2019
ms.locfileid: "67463081"
---
# <a name="security-recommendations-for-azure-internet-of-things-iot-deployment"></a>Doporučení zabezpečení pro nasazení Azure Internet of Things (IoT)

Tento článek obsahuje doporučení týkající se zabezpečení pro služby Azure IoT. Implementace těchto doporučení zlepšíte celkové zabezpečení vašich řešení IoT nápovědy splnit vaše povinnosti: zabezpečení jako zákazník Azure IoT a se. Další informace o vnitřní zabezpečení funkcí poskytovaných službou Azure IoT, najdete v článku [zabezpečení IoT od počátku](iot-security-ground-up.md).

## <a name="general"></a>Obecné

| Doporučení | Komentáře |
|-|-|
| Udržujte si přehled | Použijte nejnovější verze sady podporované platformy, programovacích jazyků, protokoly a rozhraní. |
| Bezpečnost ověřovací klíče | Bezpečnost ID zařízení a jejich klíče ověřování fyzicky po nasazení. Tím se vyhnete krycí škodlivý zařízení jako registrovaná zařízení. |
| Pomocí sad SDK zařízení, pokud je to možné | Sady SDK pro zařízení implementovat celou řadu funkcí zabezpečení, jako je šifrování, ověřování a tak dále, které vám pomáhají při vývoji aplikace zabezpečené a robustní zařízení. Zobrazit [principy a použití sady SDK služby Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks) Další informace. |


## <a name="identity-and-access-management"></a>Správa identit a přístupu

| Doporučení | Komentáře |
|-|-|
| Definování řízení přístupu pro rozbočovač | [Pochopení a definovat typ přístupu](iot-security-deployment.md#securing-the-cloud) ve vašem řešení služby IoT Hub bude mít jednotlivé komponenty založené na funkce. Jsou povolená oprávnění *čtení registru*, *RegistryReadWrite*, *ServiceConnect*, a *DeviceConnect*. Výchozí [sdílené zásady přístupu ve službě IoT hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security#access-control-and-permissions) může také pomoct definovat oprávnění pro jednotlivé komponenty, na základě jeho role. |
| Definování řízení přístupu pro back-endových služeb | Dat přijatý službou IoT Hub řešení mohou být spotřebovány dalšími službami Azure, jako například [Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/), [Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/), [služby App Service](https://docs.microsoft.com/azure/app-service/), [Logic Apps](https://docs.microsoft.com/azure/logic-apps/), a [úložiště objektů Blob](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction). Ujistěte se, že k pochopení a povolit příslušná přístupová oprávnění, jak je uvedeno pro tyto služby. |


## <a name="data-protection"></a>Ochrana dat

| Doporučení | Komentáře |
|-|-|
| Ověřování pomocí zabezpečených zařízení | Zajištění zabezpečené komunikace mezi vaším zařízením a centrem IoT s použitím buď [klíče jedinečné identity nebo zabezpečení token](iot-security-deployment.md#iot-hub-security-tokens), nebo [certifikát X.509 na zařízení](iot-security-deployment.md#x509-certificate-based-device-authentication) pro každé zařízení. Použijte vhodnou metodu pro [použít tokeny zabezpečení na základě zvolené protokolu (protokol MQTT, AMQP nebo HTTPS)](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security). |
| Zabezpečená komunikace zařízení | IoT Hub zabezpečí připojení k zařízení pomocí standard zabezpečení TLS (Transport Layer), podporuje verze 1.2, 1.2 a 1.0. Použití [TLS 1.2](https://tools.ietf.org/html/rfc5246) k zajištění maximálního zabezpečení. |
| Zabezpečená komunikace služeb | IoT Hub poskytuje koncové body pro připojení k back-endových služeb, jako [služby Azure Storage](/azure/storage/) nebo [Event Hubs](/azure/event-hubs) používání pouze protokolu TLS a žádný koncový bod je vystaven na nešifrovaný kanál. Po těchto dat dosáhne tyto back-endových služeb pro úložiště a analýzu, nezapomeňte použít odpovídající metody zabezpečení a šifrování za danou službu a chránit citlivé informace na back-endu. |


## <a name="networking"></a>Sítě

| Doporučení | Komentáře |
|-|-|
| Ochrana přístupu k zařízení | Nechat hardwaru porty v zařízení, úplné minimum, aby se zabránilo nechtěnému. Kromě toho sestavení mechanismů, zabránit nebo zjistit případnou manipulaci fyzické zařízení. Čtení [osvědčené postupy zabezpečení IoT](iot-security-best-practices.md) podrobnosti. |
| Sestavení a bezpečný hardware | Začleňte funkce zabezpečení, jako je šifrovaná úložiště nebo Trusted Platform Module (TPM), k lepšímu zabezpečení zařízení a infrastrukturu. Operační systém zařízení a ovladače upgradovat na nejnovější verze a v případě povoluje místa, nainstalujte antivirového a antimalwarového možnosti. Čtení [architektury zabezpečení IoT](iot-security-architecture.md) pochopit, jak to může pomoci zmírnit několik bezpečnostních hrozeb. |


## <a name="monitoring"></a>Monitorování

| Doporučení | Komentáře |
|-|-|
| Monitorování neoprávněného přístupu k zařízení |  Pomocí funkce protokolování pro váš operační systém zařízení monitorovat všechny porušení zabezpečení nebo manipulaci fyzické zařízení nebo jeho porty. |
| Monitorování řešení IoT z cloudu | Monitorování celkového stavu pomocí řešení služby IoT Hub [metriky ve službě Azure Monitor](https://docs.microsoft.com/azure/iot-hub/iot-hub-metrics). |
| Nastavení diagnostiky | Zblízka sledujte vaše operace protokolování událostí ve vašem řešení, a potom odešlete diagnostických protokolů do Azure monitoru, získat přehled o výkonu. Čtení [monitorování a Diagnostika problémů ve službě IoT hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-monitor-resource-health) Další informace. |

## <a name="next-steps"></a>Další postup

Pro pokročilé scénáře zahrnující Azure IoT budete muset vzít v úvahu další požadavky na zabezpečení. Zobrazit [architektury zabezpečení IoT](iot-security-architecture.md) další pokyny.

