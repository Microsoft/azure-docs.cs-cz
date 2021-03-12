---
title: Příručka pro vývojáře pro Azure IoT Hub | Microsoft Docs
description: Příručka pro vývojáře pro Azure IoT Hub obsahuje diskuze koncových bodů, zabezpečení, registru identit, správy zařízení, přímých metod, vyzdvojení zařízení, nahrávání souborů, úloh, dotazovacího jazyka IoT Hub a zasílání zpráv.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/29/2018
ms.custom: mqtt
ms.openlocfilehash: 1fcf560ba1fef137a28ab1872635cc2182cd07e1
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/11/2021
ms.locfileid: "103009194"
---
# <a name="azure-iot-hub-developer-guide"></a>Příručka pro vývojáře Azure IoT Hub

Azure IoT Hub je plně spravovaná služba, která pomáhá zajistit spolehlivou a zabezpečenou obousměrnou komunikaci mezi miliony zařízení a back-endu řešení.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Azure IoT Hub poskytuje:

* Zabezpečte komunikaci pomocí zabezpečovacích přihlašovacích údajů pro jednotlivá zařízení a řízení přístupu.

* Několik možností komunikace na úrovni zařízení-Cloud a Cloud-zařízení.

* Queryable úložiště informací o stavu jednotlivých zařízení a meta data.

* Snadné připojení zařízení s knihovnami zařízení pro nejoblíbenější jazyky a platformy.

Tento IoT Hub příručka pro vývojáře obsahuje následující články:

* [Doprovodné materiály k komunikaci](iot-hub-devguide-d2c-guidance.md) mezi zařízeními a cloudem vám pomůžou vybrat si mezi zprávami ze zařízení do cloudu, nahlášenými vlastnostmi a nahráním souboru.

* [Průvodce komunikací z cloudu na zařízení](iot-hub-devguide-c2d-guidance.md) vám pomůže vybrat mezi přímými metodami a požadovanými vlastnostmi a zprávami z cloudu na zařízení.

* [Zasílání zpráv ze zařízení do cloudu a z cloudu do zařízení pomocí IoT Hub](iot-hub-devguide-messaging.md) popisuje funkce zasílání zpráv (zařízení-Cloud a Cloud-zařízení), které IoT Hub zpřístupňuje.

  * [Posílání zpráv ze zařízení do cloudu do IoT Hub](iot-hub-devguide-messages-d2c.md).

  * [Umožňuje číst zprávy ze zařízení do cloudu z integrovaného koncového bodu](iot-hub-devguide-messages-read-builtin.md).

  * [Používejte vlastní koncové body a pravidla směrování pro zprávy ze zařízení do cloudu](iot-hub-devguide-messages-read-custom.md).

  * [Posílání zpráv z cloudu na zařízení z IoT Hub](iot-hub-devguide-messages-c2d.md).

  * [Vytváření a čtení IoT Hubch zpráv](iot-hub-devguide-messages-construct.md).

* [Nahrání souborů ze zařízení](iot-hub-devguide-file-upload.md) popisuje, jak můžete nahrávat soubory ze zařízení. Článek obsahuje také informace o tématech, jako jsou oznámení, která může proces nahrávání odeslat.

* [Správa identit zařízení v IoT Hub](iot-hub-devguide-identity-registry.md) popisuje informace o úložištích registru identit v centru IoT Hub. Tento článek také popisuje, jak k němu můžete přistupovat a jak ho upravovat.

* [Řízení přístupu k IoT Hub](iot-hub-devguide-security.md) popisuje model zabezpečení, který se používá k udělení přístupu k funkcím IoT Hub pro zařízení i cloudové komponenty. Tento článek obsahuje informace o používání tokenů a certifikátů X. 509 a podrobnosti o oprávněních, která můžete udělit.

* [Použití vláken zařízení k synchronizaci stavu a konfigurací](iot-hub-devguide-device-twins.md) popisuje koncept s *dvojitým zařízením* . Tento článek také popisuje stav vystavení vystavení zařízení, například synchronizace zařízení s jeho dvojitou funkčním zařízením. Tento článek obsahuje informace o datech uložených v zařízení.

* [Vyvolání přímé metody v zařízení](iot-hub-devguide-direct-methods.md) popisuje životní cyklus přímé metody. Tento článek popisuje, jak na zařízení volat metody z vaší back-endové aplikace a jak na svém zařízení zpracovat přímo metodu.

* [Plánování úloh na několika zařízeních](iot-hub-devguide-jobs.md) popisuje, jak můžete plánovat úlohy na více zařízeních. V tomto článku se dozvíte, jak odesílat úlohy, které provádějí úlohy, jako je provádění přímé metody a aktualizace zařízení pomocí vlákna zařízení. Také popisuje, jak zadat dotaz na stav úlohy.

* [Referenční informace – volba komunikačního protokolu](iot-hub-devguide-protocols.md) popisuje komunikační protokoly, které IoT Hub podporuje pro komunikaci zařízení, a uvádí porty, které se mají otevřít.

* [Odkazy-IoT Hub koncové body](iot-hub-devguide-endpoints.md) popisují různé koncové body, které jednotlivé služby IoT Hub zpřístupňují pro operace za běhu a správu. Článek také popisuje, jak můžete vytvořit další koncové body ve službě IoT Hub a jak používat bránu pole k umožnění připojení k vašim IoT Hubm koncovým bodům ve scénářích, které nejsou standardní.

* [Referenční jazyk IoT Hub dotazování pro vlákna, úlohy a směrování zpráv v zařízeních](iot-hub-devguide-query-language.md) popisuje, že IoT Hub dotazovací jazyk, který umožňuje načíst informace z vašeho centra o nečinnosti zařízení a úlohy.

* [Referenční kvóty a omezování](iot-hub-devguide-quotas-throttling.md) shrnují kvóty nastavené ve službě IoT Hub a omezování, ke kterému dochází, když překročíte kvótu.

* [Referenční ceny](iot-hub-devguide-pricing.md) poskytují obecné informace o různých SKU a cenách pro IoT Hub a podrobnosti o tom, jak jsou různé funkce IoT Hub měřeny jako zprávy IoT Hub.

* Sady [SDK pro referenční zařízení a služby](iot-hub-devguide-sdks.md) obsahují sady SDK Azure IoT pro vývoj aplikací pro zařízení a služby, které komunikují se službou IoT Hub. Článek obsahuje odkazy na online dokumentaci k rozhraní API.

* [Odkaz IoT Hub podpora MQTT](iot-hub-mqtt-support.md) poskytuje podrobné informace o tom, jak IoT Hub podporuje protokol MQTT. Článek popisuje podporu protokolu MQTT integrovaného se službou Azure IoT SDK a poskytuje informace o přímém použití protokolu MQTT.
