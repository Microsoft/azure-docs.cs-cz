---
title: Příručka vývojáře pro Azure IoT Hub | Microsoft Docs
description: Příručka pro vývojáře Azure IoT Hub obsahuje diskusí koncových bodů, zabezpečení, registru identit, správu zařízení, přímé metody, dvojčata zařízení, nahrávání souborů, úlohy, dotazovací jazyk IoT Hub a zasílání zpráv.
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: d534ff9d-2de5-4995-bb2d-84a02693cb2e
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/29/2018
ms.author: dobett
ms.openlocfilehash: 31ccf65ccd2ab4e9673900efb1de357ab450b799
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2018
---
# <a name="azure-iot-hub-developer-guide"></a>Příručka vývojáře pro službu Azure IoT Hub

Azure IoT Hub je plně spravovaná služba, která pomáhá povolit spolehlivou a zabezpečenou obousměrnou komunikaci mezi miliony zařízení a back-end řešení.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Azure IoT Hub nabízí:

* Zabezpečená komunikace pomocí přihlašovacích údajů na zařízení zabezpečení a řízení přístupu.
* Více možností velkého rozsahu komunikace zařízení cloud a cloud zařízení.
* Dotazovatelné úložiště informace o stavu licence vázané na zařízení a metadata.
* Připojení snadno zařízení s knihovny zařízení pro Nejoblíbenější jazyky a platformy.

Tato příručka vývojáře IoT Hub obsahuje v následujících článcích:

* [Komunikace zařízení cloud pokyny] [ lnk-d2c-guidance] pomůže vám zvolit zpráv typu zařízení cloud, dvojče zařízení hlášené vlastností a nahrávání souborů.
* [Cloud zařízení komunikace pokyny] [ lnk-c2d-guidance] pomůže vám zvolit přímé metody dvojče zařízení požadované vlastnosti a zprávy typu cloud zařízení.
* [Zařízení cloud a cloud zařízení zasílání zpráv službou IoT Hub] [ devguide-messaging] popisuje funkce zasílání zpráv (zařízení cloud a z cloudu do zařízení), které IoT Hub zpřístupní.
  * [Odesílání zpráv typu zařízení cloud do služby IoT Hub][devguide-messages-d2c].
  * [Číst zprávy typu zařízení cloud z předdefinovaných koncového bodu][devguide-builtin].
  * [Použít vlastní koncové body a pravidla směrování pro zprávy typu zařízení cloud][devguide-custom].
  * [Odesílání zpráv typu cloud zařízení ze služby IoT Hub][devguide-messages-c2d].
  * [Vytvoření a čtení zpráv služby IoT Hub][devguide-format].
* [Odeslání souborů ze zařízení] [ devguide-upload] popisuje, jak mohou odesílat soubory ze zařízení. Článek obsahuje také informace o oblastech, jako je oznámení, že proces odesílání může odesílat.
* [Správa identit zařízení IoT hub] [ devguide-identities] popisuje, jaké informace úložiště registru identit IoT hub. Článek také popisuje, jak můžete používat a upravit ho.
* [Řízení přístupu ke službě IoT Hub] [ devguide-security] popisuje model zabezpečení, používá k udělení přístupu k funkci IoT Hub pro zařízení a cloudových součásti. Článek obsahuje informace o používání tokeny a certifikáty X.509 a podrobnosti, které můžete udělit oprávnění.
* [Pomocí dvojčata zařízení synchronizovat stavu a konfigurace] [ devguide-device-twins] popisuje *dvojče zařízení* koncept. Článek také popisuje dvojčata zařízení funkce vystavit, jako je například synchronizace zařízení s jeho dvojče zařízení. Článek obsahuje informace o data uložená v dvojče zařízení.
* [Volání metody přímé na zařízení] [ devguide-directmethods] popisuje životní cyklus přímá metoda. Článek popisuje, jak volat metody na zařízení z back-end aplikace a zpracování přímá metoda ve vašem zařízení.
* [Plánování úloh na několika zařízeních] [ devguide-jobs] popisuje, jak lze naplánovat úlohy na několika zařízeních. Článek popisuje, jak k odesílání úloh, které provádějí úlohy, jako provádění přímé metodu, aktualizace zařízení pomocí dvojče zařízení. Také popisuje, jak zjistit stav úlohy.
* [Reference – volba komunikační protokol] [ devguide-protocol] popisuje komunikační protokoly, že podporuje pro komunikaci zařízení IoT Hub a seznam portů, kterým by se mělo otevřít.
* [Odkaz – koncové body centra IoT] [ devguide-endpoints] popisuje různé koncových bodů, které každý IoT hub zpřístupní pro operace runtime a správy. Článek také popisuje, jak můžete vytvořit další koncové body ve službě IoT hub a jak používat brána pole pro umožnění připojení k vaší koncové body centra IoT v nestandardním scénáře.
* [Referenční dokumentace - IoT Hub dotazovacího jazyka pro dvojčata zařízení, úlohy a směrování zpráv] [ devguide-query] popisuje této služby IoT Hub dotazovací jazyk, který vám umožňuje načíst informace z vašeho centra o úlohách a dvojčata zařízení.
* [Referenční dokumentace - kvót a omezování] [ devguide-quotas] shrnuje kvóty nastavit ve službě IoT Hub a bude omezení, která nastane, když překročí kvótu.
* [Reference – ceny] [ devguide-pricing] obsahuje obecné informace o různých SKU a cenách služby IoT Hub a podrobnosti o tom, jak jsou různé funkce IoT Hub měřené jako zprávy službou IoT Hub.
* [Referenční dokumentace - zařízení a služby sady SDK] [ devguide-sdks] uvádí SDK služby Azure IoT pro vývoj aplikací pro zařízení a služby, které spolupracují se službou IoT hub. Článek obsahuje odkazy na online dokumentaci k rozhraní API.
* [Odkaz – Podpora IoT Hub MQTT] [ devguide-mqtt] poskytuje podrobné informace o tom, jak IoT Hub podporuje protokol MQTT. Článek popisuje podporu pro protokol MQTT integrované do SDK služby Azure IoT a poskytuje informace o používání protokolu MQTT přímo.
* [Glosář] [ devguide-glossary] seznam běžných termínů spojených se IoT Hub.

[devguide-messaging]: iot-hub-devguide-messaging.md
[devguide-upload]: iot-hub-devguide-file-upload.md
[devguide-identities]: iot-hub-devguide-identity-registry.md
[devguide-security]: iot-hub-devguide-security.md
[devguide-device-twins]: iot-hub-devguide-device-twins.md
[devguide-directmethods]: iot-hub-devguide-direct-methods.md
[devguide-jobs]: iot-hub-devguide-jobs.md
[devguide-endpoints]: iot-hub-devguide-endpoints.md
[devguide-quotas]: iot-hub-devguide-quotas-throttling.md
[devguide-query]: iot-hub-devguide-query-language.md
[devguide-sdks]: iot-hub-devguide-sdks.md
[devguide-mqtt]: iot-hub-mqtt-support.md
[devguide-glossary]: iot-hub-devguide-glossary.md
[devguide-pricing]: iot-hub-devguide-pricing.md
[lnk-c2d-guidance]: iot-hub-devguide-c2d-guidance.md
[lnk-d2c-guidance]: iot-hub-devguide-d2c-guidance.md
[devguide-messages-d2c]: iot-hub-devguide-messages-d2c.md
[devguide-builtin]: iot-hub-devguide-messages-read-builtin.md
[devguide-custom]: iot-hub-devguide-messages-read-custom.md
[devguide-messages-c2d]: iot-hub-devguide-messages-c2d.md
[devguide-format]: iot-hub-devguide-messages-construct.md
[devguide-protocol]: iot-hub-devguide-protocols.md
