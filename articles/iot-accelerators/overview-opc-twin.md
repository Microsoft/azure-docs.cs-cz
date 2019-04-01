---
title: Co je Správa zařízení Azure IoT OPC UA | Dokumentace Microsoftu
description: Přehled dvojčete OPC
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: overview
ms.service: iot-industrialiot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 3bbc20aa61faafb1405fa756b334bae05ec4129b
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2019
ms.locfileid: "58759242"
---
# <a name="what-is-azure-iot-open-platform-communications-opc-device-management"></a>Co je Správa zařízení Azure IoT otevřené platformy komunikací OPC ()?
Správa zařízení Azure IoT OPC UA, označované také jako Dvojčete OPC se skládá z mikroslužeb, které slouží k připojení cloudu a objekt pro vytváření sítí Azure IoT Edge a IoT Hub. Dvojče OPC poskytuje zjišťování, registrace a vzdáleného řízení průmyslová zařízení prostřednictvím rozhraní REST API. Dvojče OPC nevyžaduje architekturu Unified OPC (OPC UA) sady SDK, je programování jazykově nezávislé a můžou být součástí bez serveru pracovního postupu. Tento článek popisuje několik případů použití Dvojčat OPC.

## <a name="discovery-and-control"></a>Zjišťování a řízení
Pro jednoduché pro registraci a zjišťování můžete použít dvojče OPC.

### <a name="simple-discovery-and-registration"></a>Jednoduché zjišťování a registrace
Dvojče OPC umožňuje objekt pro vytváření operátory pro skenování sítě factory tak, aby servery OPC UA lze zjistit a zaregistrován. Jako alternativu operátory objekt pro vytváření také ručně registrovat pomocí adresy URL pro zjišťování známého zařízení OPC UA. Například pro připojení pro všechna zařízení OPC UA po instalaci brány IoT Edge se modul OPC Dvojčete ve výrobě, factory operátora vzdáleně spustit prohledávání sítě a vizuálně zobrazit všechny servery OPC UA. 

### <a name="simple-control"></a>Jednoduché ovládací prvek
Dvojče OPC umožňuje operátorům factory reagovat na události a překonfigurujte jejich factory floor počítačů z cloudu automaticky nebo ručně v reálném čase. Dvojče OPC poskytuje rozhraní REST API můžete volat služby na serveru OPC UA, vyhledejte jeho adresní prostor ke čtení a zápis proměnné a spouštět metody. Například kotel používá teploty klíčový ukazatel výkonu pro řízení výrobní linky. Senzor teploty publikuje změny v datech použití vydavatele OPC. Factory operátora obdrží upozornění, že teplota dosáhla prahová hodnota. Výrobní linky chladí automaticky prostřednictvím OPC Dvojčete. Factory operátora informován o nástrojů dolů.

## <a name="authentication"></a>Authentication
Pro jednoduché ověřování a pro jednoduché vývojové prostředí můžete použít dvojče OPC.

### <a name="simple-authentication"></a>Jednoduché ověřování 
Dvojče OPC používá ověřování pomocí Azure Active Directory AAD a auditování od konce do konce. Například dvojče OPC umožní aplikaci postavené na dvojče OPC, chcete-li zjistit, co má operátor provádí na počítači. Na straně počítače je prostřednictvím OPC UA auditování. Na straně cloudu je pomocí ukládání protokolu auditu neměnné klientů a ověřování AAD pomocí rozhraní REST API.

### <a name="simple-developer-experience"></a>Jednoduché vývojářské prostředí 
Dvojče OPC jde použít s aplikace napsané v libovolném programovacím jazyce prostřednictvím rozhraní REST API. Jako vývojáři integrovat do řešení klienta OPC UA, není nutné znalosti o sadě SDK OPC UA. Dvojče OPC můžete bez problémů integrovat do architektury bezstavové, bez serveru. Například web vývojáře plnohodnotných, který vyvíjí aplikaci pro řídicí panel upozornění a události můžou psát logiku pro reakci na události v jazyce JavaScript nebo TypeScript pomocí OPC Dvojčete bez znalosti jazyka C, C#, nebo toto plně implementováno zásobníku OPC UA. 

## <a name="next-steps"></a>Další postup

Teď, když jste se dozvěděli o Dvojčete OPC a jeho použití, je zde navrhované další krok:

> [!div class="nextstepaction"]
> [Co je trezor OPC](overview-opc-twin-architecture.md)