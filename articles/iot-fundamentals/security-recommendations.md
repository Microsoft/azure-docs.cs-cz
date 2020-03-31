---
title: Doporučení zabezpečení pro Azure IoT | Dokumenty společnosti Microsoft
description: Tento článek shrnuje další kroky k zajištění zabezpečení v řešení Azure IoT Hub.
author: dsk-2015
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: article
ms.date: 11/13/2019
ms.author: dkshir
ms.custom: security-recommendations
ms.openlocfilehash: 0ada9a520a5be56444a1c3e746a68dbcf9275686
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74048459"
---
# <a name="security-recommendations-for-azure-internet-of-things-iot-deployment"></a>Doporučení zabezpečení pro nasazení Azure Internet of Things (IoT)

Tento článek obsahuje doporučení zabezpečení pro IoT. Implementace těchto doporučení vám pomůže splnit vaše bezpečnostní povinnosti, jak je popsáno v našem modelu sdílené odpovědnosti. Další informace o tom, co společnost Microsoft dělá pro plnění povinností poskytovatele služeb, našlápne [na sdílené odpovědnosti za cloud computing](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91).

Některá doporučení zahrnutá v tomto článku můžete automaticky sledovat pomocí Azure Security Center. Azure Security Center je první obranná linie ochrany vašich prostředků v Azure. Pravidelně analyzuje stav zabezpečení prostředků Azure k identifikaci potenciálních chyb zabezpečení. Poté poskytuje doporučení, jak je řešit.

- Další informace o doporučeních Azure Security Center najdete [v tématu Doporučení zabezpečení v Azure Security Center](../security-center/security-center-recommendations.md).
- Informace o Azure Security Center najdete v tématu [Co je Azure Security Center?](../security-center/security-center-intro.md)

## <a name="general"></a>Obecné

| Doporučení | Komentáře | Podporováno ASC |
|-|----|--|
| Buďte v obraze | Používejte nejnovější verze podporovaných platforem, programovacích jazyků, protokolů a architektur. | - |
| Zachování bezpečného klíče ověřování | Po nasazení uchovávejte ID zařízení a jejich ověřovací klíče fyzicky v bezpečí. Tím se zabrání škodlivé zařízení maskovat jako registrované zařízení. | - |
| Pokud je to možné, používejte sady SDK zařízení | Sady SDK zařízení implementují různé funkce zabezpečení, jako je šifrování, ověřování a tak dále, které vám pomohou při vývoji robustní a zabezpečené aplikace zařízení. Další informace najdete [v tématu Principy a používání sad SDK služby Azure IoT Hub.](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks) | - |

## <a name="identity-and-access-management"></a>Správa identit a přístupu 

| Doporučení | Komentáře | Podporováno ASC |
|-|----|--|
| Definování řízení přístupu pro rozbočovač | [Pochopit a definovat typ přístupu](iot-security-deployment.md#securing-the-cloud) každé součásti bude mít ve vašem řešení služby IoT Hub, na základě funkce. Povolená oprávnění jsou *Čtení registru*, *RegistryReadWrite*, *ServiceConnect*a *DeviceConnect*. Výchozí [zásady sdíleného přístupu ve službě IoT hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security#access-control-and-permissions) můžou také pomoci definovat oprávnění pro každou komponentu na základě její role. | - |
| Definovat řízení přístupu pro back-endové služby | Data požitá vaším řešením IoT Hub můžou spotřebovávat jiné služby Azure, jako je [Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/), [Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/), [App Service](https://docs.microsoft.com/azure/app-service/), [Logic Apps](https://docs.microsoft.com/azure/logic-apps/)a úložiště [objektů blob](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction). Ujistěte se, že rozumíte příslušným přístupovým oprávněním a povolujete je, jak jsou pro tyto služby zdokumentována. | - |

## <a name="data-protection"></a>Ochrana dat

| Doporučení | Komentáře | Podporováno ASC |
|-|----|--|
| Ověřování zabezpečeného zařízení | Zajistěte zabezpečenou komunikaci mezi vašimi zařízeními a službou IoT hub pomocí [jedinečného klíče identity nebo tokenu zabezpečení](iot-security-deployment.md#iot-hub-security-tokens)nebo [certifikátu X.509 na zařízení](iot-security-deployment.md#x509-certificate-based-device-authentication) pro každé zařízení. Pomocí vhodné metody [použijte tokeny zabezpečení založené na zvoleném protokolu (MQTT, AMQP nebo HTTPS).](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security) | - |
| Zabezpečená komunikace se zařízením | IoT Hub zabezpečuje připojení k zařízením pomocí standardu TLS (Transport Layer Security), který podporuje verze 1.2 a 1.0. Pro zajištění maximální bezpečnosti použijte [TLS 1.2.](https://tools.ietf.org/html/rfc5246) | - |
| Zabezpečená komunikace se službami | IoT Hub poskytuje koncové body pro připojení k back-endovým službám, jako je [Azure Storage](/azure/storage/) nebo [Event Hubs,](/azure/event-hubs) které používají jenom protokol TLS, a žádný koncový bod není vystaven na nešifrovaném kanálu. Jakmile tato data dosáhnou těchto back-endových služeb pro úložiště nebo analýzu, ujistěte se, že používáte vhodné metody zabezpečení a šifrování pro tuto službu a chráníte citlivé informace v back-endu. | - |

## <a name="networking"></a>Síťové služby

| Doporučení | Komentáře | Podporováno ASC |
|-|----|--|
| Ochrana přístupu k zařízením | Udržujte hardwarové porty ve svých zařízeních na minimum, abyste předešli nežádoucímu přístupu. Kromě toho sestavení mechanismy k prevenci nebo detekci fyzické manipulace zařízení. Podrobnosti načláneknete v [doporučených postupech zabezpečení IoT.](iot-security-best-practices.md) | - |
| Vytváření zabezpečeného hardwaru | Začleňte funkce zabezpečení, jako je šifrované úložiště nebo modul TPM (Trusted Platform Module), abyste zajistili větší zabezpečení zařízení a infrastruktury. Udržujte operační systém zařízení a ovladače upgradované na nejnovější verze, a pokud to prostor dovolí, nainstalujte antivirové a antimalwarové funkce. Přečtěte si [architekturu zabezpečení IoT, abyste pochopili,](iot-security-architecture.md) jak to může pomoci zmírnit několik bezpečnostních hrozeb. | - |

## <a name="monitoring"></a>Monitorování

| Doporučení | Komentáře | Podporováno ASC |
|-|----|--|
| Sledování neoprávněného přístupu k vašim zařízením |  Pomocí funkce protokolování operačního systému zařízení můžete sledovat jakékoli narušení zabezpečení nebo fyzické neoprávněnýzásahy zařízení nebo jeho portů. | - |
| Monitorování řešení IoT z cloudu | Sledujte celkový stav vašeho řešení IoT Hub pomocí [metriky v Azure Monitoru](https://docs.microsoft.com/azure/iot-hub/iot-hub-metrics). | - |
| Nastavení diagnostiky | Pečlivě sledujte vaše operace protokolováním událostí ve vašem řešení a následným odesláním diagnostických protokolů do Služby Azure Monitor, abyste získali přehled o výkonu. Další informace najdou další informace načlánekněte [monitor a diagnostikujte problémy ve vašem centru IoT Hub.](https://docs.microsoft.com/azure/iot-hub/iot-hub-monitor-resource-health) | - |

## <a name="next-steps"></a>Další kroky

Pro pokročilé scénáře zahrnující Azure IoT, budete muset zvážit další požadavky na zabezpečení. Další informace najdete v [tématu Architektura zabezpečení IoT.](iot-security-architecture.md)

