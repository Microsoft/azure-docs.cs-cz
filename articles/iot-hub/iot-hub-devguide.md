---
title: Příručka pro vývojáře pro službu Azure IoT Hub | Dokumentace Microsoftu
description: Příručka pro vývojáře Azure IoT Hub obsahuje diskuse o koncové body, zabezpečení, registr identit, správy zařízení, přímé metody, dvojčata zařízení, nahrávání souborů, úlohy, dotazovací jazyk služby IoT Hub a zasílání zpráv.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/29/2018
ms.author: dobett
ms.openlocfilehash: ecbec614bd0d1b043faf6ad002b05d4acdcc4ab4
ms.sourcegitcommit: 3856c66eb17ef96dcf00880c746143213be3806a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/02/2018
ms.locfileid: "48041743"
---
# <a name="azure-iot-hub-developer-guide"></a>Příručka pro vývojáře Azure IoT Hub

Azure IoT Hub je plně spravovaná služba, která pomáhá povolit spolehlivou a zabezpečenou obousměrnou komunikaci mezi miliony zařízení a back-endu řešení.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Azure IoT Hub vám nabízí:

* Zabezpečená komunikace s použitím přihlašovacích údajů na zařízení zabezpečení a řízení přístupu.

* Několik možností komunikace typu zařízení cloud a cloud zařízení velkém měřítku.

* Dotazovatelné úložiště pro informace o stavu jednotlivých zařízení a metadata.

* Připojení zařízení snadno knihovny zařízení pro Nejoblíbenější jazyky a platformy.

Tato příručka pro vývojáře IoT Hub obsahuje následující články:

* [Pokyny k komunikace typu zařízení cloud](iot-hub-devguide-d2c-guidance.md) umožňuje výběr mezi zprávy typu zařízení cloud, ohlášené vlastnosti dvojčete zařízení a odesílání souborů.

* [Pokyny k komunikaci typu cloud zařízení](iot-hub-devguide-c2d-guidance.md) pomáhá vybrat mezi přímé metody, dvojče zařízení požadované vlastnosti a zprávy typu cloud zařízení.

* [Typu zařízení cloud a cloud zařízení zasílání zpráv pomocí služby IoT Hub](iot-hub-devguide-messaging.md) popisuje funkce zasílání zpráv (typu zařízení cloud a cloud zařízení), které služba IoT Hub zpřístupní.

  * [Odesílání zpráv typu zařízení cloud do služby IoT Hub](iot-hub-devguide-messages-d2c.md).

  * [Čtení zpráv typu zařízení cloud z integrovaného koncového bodu](iot-hub-devguide-messages-read-builtin.md).

  * [Použití vlastních koncových bodů a pravidel směrování pro zprávy typu zařízení cloud](iot-hub-devguide-messages-read-custom.md).

  * [Odesílat zprávy typu cloud zařízení ze služby IoT Hub](iot-hub-devguide-messages-c2d.md).

  * [Vytvoření a čtení zpráv IoT Hubu](iot-hub-devguide-messages-construct.md).

* [Nahrání souborů ze zařízení](iot-hub-devguide-file-upload.md) popisuje, jak můžete nahrát soubory ze zařízení. Článek obsahuje také informace o tématech, jako je oznámení, že proces nahrávání můžete odesílat.

* [Správa identit zařízení ve službě IoT Hub](iot-hub-devguide-identity-registry.md)popisuje, jaké informace úložiště registru identit IoT hub. Tento článek také popisuje, jak můžete používat a upravte ho.

* [Řízení přístupu ke službě IoT Hub](iot-hub-devguide-security.md) popisuje model zabezpečení umožňuje udělit přístup k funkcím služby IoT Hub pro obě zařízení a cloudové komponenty. Tento článek obsahuje informace o používání tokeny a podrobnosti, které můžete udělit oprávnění a certifikáty X.509.

* [Použití dvojčat zařízení k synchronizaci stavu a konfigurace](iot-hub-devguide-device-twins.md) popisuje *dvojče zařízení* koncept. Tento článek také popisuje funkce dvojčata zařízení zpřístupnit, například synchronizace zařízení s jeho dvojče zařízení. Tento článek obsahuje informace o data uložená ve dvojčeti zařízení.

* [Vyvolání přímé metody v zařízení](iot-hub-devguide-direct-methods.md) popisuje životní cyklus přímé metody. Tento článek popisuje, jak vyvolávat metody v zařízení v back endové aplikace a zpracovávají přímé metody v zařízení.

* [Plánování úloh na několika zařízeních](iot-hub-devguide-jobs.md) popisuje, jak můžete plánovat úlohy na několika zařízeních. Tento článek popisuje, jak odesílat úlohy, které provádějí úlohy jako přímé metody, aktualizace zařízení, dvojče zařízení pomocí provádí. Také popisuje, jak zadávat dotazy na stav úlohy.

* [Odkaz – volba komunikačního protokolu](iot-hub-devguide-protocols.md) popisuje komunikační protokoly, že podporuje pro komunikaci služby IoT Hub a uvádí porty, které by se mělo otevřít.

* [Reference – koncové body IoT Hubu](iot-hub-devguide-endpoints.md) popisuje různé koncové body, které každý IoT hub zpřístupní pro operace modulu runtime a správy. Tento článek také popisuje, jak můžete vytvořit další koncové body služby IoT hub a jak používat hraniční brána umožňuje připojení ke koncovým bodům vaší služby IoT Hub v nestandardních situacích.

* [Reference – dotazovací jazyk služby IoT Hub pro dvojčata zařízení, úlohy a směrování zpráv](iot-hub-devguide-query-language.md) popisuje tento dotazovací jazyk služby IoT, který vám umožňuje načíst informace z vašeho centra o dvojčata zařízení a úlohy.

* [Reference – kvót a omezování](iot-hub-devguide-quotas-throttling.md) shrnuje kvóty nastavit ve službě IoT Hub a omezení šířky pásma, které nastane při překročení kvóty.

* [Odkaz – ceny](iot-hub-devguide-pricing.md) obsahuje obecné informace o různých SKU a ceny služby IoT Hub a podrobnosti o tom, jak se měří různým funkcím služby IoT Hub jako zprávy ve službě IoT Hub.

* [Reference – zařízením a službou SDK](iot-hub-devguide-sdks.md) obsahuje seznam sad SDK Azure IoT pro vývoj aplikací pro zařízení a služby, které komunikují s centrem IoT. Tento článek obsahuje odkazy na online dokumentaci k rozhraní API.

* [Reference – podpora IoT Hub MQTT](iot-hub-mqtt-support.md) poskytuje podrobné informace o tom, jak služby IoT Hub podporuje protokolu MQTT. Tento článek popisuje podporu pro protokol MQTT integrované do sady SDK Azure IoT a poskytuje informace o přímo pomocí protokolu MQTT.

* [Glosář](iot-hub-devguide-glossary.md) seznam běžných termínů souvisejících se službou IoT Hub.