---
title: Průvodce pro vývojáře pro Azure IoT Hub | Dokumenty společnosti Microsoft
description: Průvodce vývojáři centra Azure IoT Hub zahrnuje diskuse o koncových bodech, zabezpečení, registru identit, správě zařízení, přímých metodách, dvojčatech zařízení, nahrávání souborů, úlohách, dotazovacím jazyce IoT Hub a zasílání zpráv.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/29/2018
ms.openlocfilehash: 1ff7d430edd3f638ad5efcc5a89604e4ed732211
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "60400146"
---
# <a name="azure-iot-hub-developer-guide"></a>Průvodce vývojáři azure ioT hubu

Azure IoT Hub je plně spravovaná služba, která pomáhá umožnit spolehlivou a zabezpečenou obousměrnou komunikaci mezi miliony zařízení a back-endem řešení.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Azure IoT Hub vám poskytuje:

* Zabezpečte komunikaci pomocí přihlašovacích údajů zabezpečení pro zařízení a řízení přístupu.

* Několik možností hyperškálování zařízení do cloudu a cloud-to-device.

* Dotazovatelné ukládání informací o stavu zařízení a metadat.

* Snadné připojení zařízení s knihovnami zařízení pro nejoblíbenější jazyky a platformy.

Tento průvodce vývojáři služby IoT Hub obsahuje následující články:

* [Pokyny pro komunikaci mezi zařízeními](iot-hub-devguide-d2c-guidance.md) a cloudem vám pomohou vybrat mezi zprávami mezi zařízeními a cloudy, ohlášenými vlastnostmi dvojčete zařízení a nahráváním souborů.

* [Pokyny ke komunikaci](iot-hub-devguide-c2d-guidance.md) mezi cloudem a zařízeními vám pomohou vybrat si mezi přímými metodami, požadovanými vlastnostmi dvojčete zařízení a zprávami mezi cloudy.

* Zasílání zpráv mezi [zařízeními a cloud-to-device pomocí služby IoT Hub](iot-hub-devguide-messaging.md) popisuje funkce zasílání zpráv (zařízení ke cloudu a cloud-to-device), které služba IoT Hub poskytuje.

  * [Odesílejte zprávy mezi zařízeními a cloudy do služby IoT Hub](iot-hub-devguide-messages-d2c.md).

  * [Čtení zpráv mezi zařízeními a cloudy z integrovaného koncového bodu](iot-hub-devguide-messages-read-builtin.md).

  * [Pro zprávy mezi zařízeními a cloudem použijte vlastní koncové body a pravidla směrování](iot-hub-devguide-messages-read-custom.md).

  * [Odesílejte zprávy z cloudu na zařízení z ioT hubu](iot-hub-devguide-messages-c2d.md).

  * [Vytvářejte a čtu zprávy centra IoT Hub](iot-hub-devguide-messages-construct.md).

* [Nahrávání souborů ze zařízení](iot-hub-devguide-file-upload.md) popisuje, jak můžete nahrávat soubory ze zařízení. Článek také obsahuje informace o tématech, jako jsou oznámení, která může proces nahrávání odeslat.

* [Správa identit zařízení v centru IoT Hub](iot-hub-devguide-identity-registry.md)popisuje, jaké informace každý adresář registru identit služby IoT hub ukládá. Článek také popisuje, jak k němu můžete přistupovat a upravovat jej.

* [Řízení přístupu k ioT hubu](iot-hub-devguide-security.md) popisuje model zabezpečení používaný k udělení přístupu k funkcím služby IoT Hub pro zařízení i cloudové komponenty. Článek obsahuje informace o používání tokenů a certifikátů X.509 a podrobnosti o oprávněních, která můžete udělit.

* [Pomocí dvojčat zařízení synchronizujte stav a konfigurace](iot-hub-devguide-device-twins.md) popisují koncept *dvojčete zařízení.* Článek také popisuje dvojčata zařízení funkce vystavit, jako je například synchronizace zařízení s jeho dvojče zařízení. Článek obsahuje informace o datech uložených v dvojčeti zařízení.

* [Vyvolat přímou metodu na zařízení](iot-hub-devguide-direct-methods.md) popisuje životní cyklus přímé metody. Článek popisuje, jak vyvolat metody na zařízení z back-endové aplikace a zpracovat přímou metodu na vašem zařízení.

* [Plánování úloh na více zařízeních](iot-hub-devguide-jobs.md) popisuje, jak můžete naplánovat úlohy na více zařízeních. Článek popisuje, jak odeslat úlohy, které provádějí úlohy jako provádění přímé metody, aktualizace zařízení pomocí dvojčete zařízení. Také popisuje, jak dotaz na stav úlohy.

* [Reference – zvolte komunikační protokol](iot-hub-devguide-protocols.md) popisuje komunikační protokoly, které podporuje služby IoT Hub pro komunikaci zařízení a uvádí porty, které by měly být otevřené.

* [Reference – koncové body centra IoT hub](iot-hub-devguide-endpoints.md) popisuje různé koncové body, které každý ioT hub zveřejňuje pro operace runtime a správy. Článek také popisuje, jak můžete vytvořit další koncové body ve vašem centru IoT hub a jak pomocí brány pole povolit připojení k koncovým bodům služby IoT Hub v nestandardních scénářích.

* [Reference – Dotazovací jazyk služby IoT Hub pro dvojčata zařízení, úlohy a směrování zpráv](iot-hub-devguide-query-language.md) popisuje tento dotazovací jazyk služby IoT Hub, který umožňuje načítat informace z centra o dvojčatech a úlohách vašeho zařízení.

* [Odkaz – kvóty a omezení](iot-hub-devguide-quotas-throttling.md) shrnuje kvóty nastavené ve službě IoT Hub a omezení, ke kterému dochází při překročení kvóty.

* [Reference – ceny](iot-hub-devguide-pricing.md) poskytují obecné informace o různých skum a ceny pro IoT Hub a podrobnosti o tom, jak se různé funkce ioT hubu měří jako zprávy pomocí IoT Hub.

* [Reference – sady SDK pro zařízení a služby](iot-hub-devguide-sdks.md) uvádí sady Azure IoT SDK pro vývoj aplikací pro zařízení a služby, které interagují s vaším centrem IoT. Článek obsahuje odkazy na dokumentaci online rozhraní API.

* [Reference – podpora Služby IoT Hub MQTT](iot-hub-mqtt-support.md) poskytuje podrobné informace o tom, jak služba IoT Hub podporuje protokol MQTT. Článek popisuje podporu protokolu MQTT integrované ho sady Azure IoT SDK a poskytuje informace o použití protokolu MQTT přímo.

* [Glosář](iot-hub-devguide-glossary.md) seznam běžných termínů souvisejících s centrem IoT.