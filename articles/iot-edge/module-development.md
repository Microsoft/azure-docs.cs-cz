---
title: Vývoj modulů pro hraniční Azure IoT | Microsoft Docs
description: Informace o vytváření vlastních modulů pro Azure IoT Edge
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 10/05/2017
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: dbbd07e93602855afb0c9755e8872e0b46557611
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37030015"
---
# <a name="understand-the-requirements-and-tools-for-developing-iot-edge-modules"></a>Pochopení požadavků a nástrojů pro vývoj modulů IoT Edge

Tento článek vysvětluje, jaké funkce jsou k dispozici při zápisu aplikace, které běží jako modul IoT okraj a jak chcete využít výhod je.

## <a name="iot-edge-runtime-environment"></a>Běhové prostředí IoT Edge
Modul runtime IoT Edge poskytuje infrastrukturu integrovat funkce více modulů IoT okraj a k jejich nasazení do zařízení IoT okraj. Na vysoké úrovni může být žádné program zabalené jako modul IoT okraj. Plně využít IoT okraje komunikace a funkce správy, ale program spuštěný v modulu můžete připojit k místní centra IoT Edge, integrované v modulu runtime IoT okraj.

## <a name="using-the-iot-edge-hub"></a>Pomocí centra IoT Edge
Centra IoT Edge poskytují dva hlavní funkce: proxy server a služby IoT Hub a místní komunikaci.

### <a name="iot-hub-primitives"></a>IoT Hub primitiv
IoT Hub vidí a modul instance analogicky na zařízení, v tom smyslu, že IT oddělení:

* má twin modul, který se odlišuje a je izolovaná od [dvojče zařízení] [ lnk-devicetwin] a dalších modulu dvojčata zařízení;
* můžete odeslat [zpráv typu zařízení cloud][lnk-iothub-messaging];
* může přijímat [přímé metody] [ lnk-methods] zaměřené na svou identitu.

Modul v současné době nelze přijímat zprávy typu cloud zařízení ani používat funkci nahrávání souboru.

Při psaní modulu, můžete jednoduše použít [sady SDK zařízení Azure IoT] [ lnk-devicesdk] připojení k centru IoT okraj a používat výše uvedené funkce, jako při používání služby IoT Hub s aplikací zařízení, jedinou rozdílem je, že z vaší aplikace back-end, máte k odkazování na identitě modulu místo identitu tohoto zařízení.

V tématu [vývoj a nasazení modul IoT Edge simulované zařízení] [ lnk-tutorial2] příklad modulu aplikace, která odesílá zprávy typu zařízení cloud a používá dvojici modulu.

### <a name="device-to-cloud-messages"></a>Zprávy typu zařízení-cloud
Chcete-li povolit komplexní zpracování zpráv typu zařízení cloud, IoT Edge hub zajišťuje deklarativní směrování zpráv mezi moduly a mezi moduly a IoT Hub.
To umožňuje moduly zachytávat a zpracování zpráv odeslaných z ostatních modulů a šířit je do komplexní kanály.
Článek [složení modulu] [ lnk-module-comp] vysvětluje, jak vytvořit modulů do komplexní kanály pomocí trasy.

Modul IoT hraniční jinak než běžné aplikace zařízení IoT Hub, může přijímat zprávy typu zařízení cloud, které probíhá směrovány přes proxy server pomocí jeho místní hraniční IoT hub, aby bylo možné zpracovat je.

Centra IoT Edge rozšíří zprávy, které mají podle deklarativní tras, které jsou popsané v modulu [složení modulu] [ lnk-module-comp] článku. Při vývoji modul IoT Edge, jak je znázorněno v tomto kurzu, může přijímat tyto zprávy nastavením obslužné rutiny zpráv, [vývoj a nasazení modul IoT Edge simulované zařízení][lnk-tutorial2].

Aby bylo možné zjednodušit proces vytváření tras, IoT Edge přidá koncept modulu *vstupní* a *výstup* koncové body. Modul může přijímat všechny zprávy typu zařízení cloud směrovány do něj bez zadání žádný vstup a odesílat zprávy typu zařízení cloud bez zadání žádný výstup.
Pomocí explicitní vstupy a výstupy, ale usnadňuje pravidla směrování pochopit. V tématu [složení modulu] [ lnk-module-comp] Další informace o pravidla směrování a vstupních a výstupních koncových bodů pro moduly.

Nakonec zpráv typu zařízení cloud zpracovávaných rozbočovače hraniční jsou razítkem s následujícími vlastnostmi systému:

| Vlastnost | Popis |
| -------- | ----------- |
| $connectionDeviceId | ID zařízení klienta, který odeslal zprávu |
| $connectionModuleId | ID modulu modul, který odeslal zprávu |
| $inputName | Zadejte, který se tato zpráva zobrazila. Nesmí být prázdné. |
| $outputName | Výstup používá k odeslání zprávy. Nesmí být prázdné. |

### <a name="connecting-to-iot-edge-hub-from-a-module"></a>Připojování k hraniční IoT hub z modulu
Připojení k místní hraniční IoT hub z modulu zahrnuje dva kroky: používat připojovací řetězec, pokud při spuštění modulu runtime IoT okraj a ujistěte se, že vaše aplikace přijímá certifikát předložený IoT Edge hub na daném zařízení.

Připojovací řetězec k použití je vloženy IoT Edge runtime v proměnné prostředí `EdgeHubConnectionString`. Díky tomu je k dispozici žádné program, který chce použít.

Analogicky, je certifikát, který chcete použít k ověření připojení rozbočovače IoT Edge vloženy IoT Edge runtime v souboru, jehož cesta je k dispozici v proměnné prostředí `EdgeModuleCACertificateFile`.

Tento kurz [vývoj a nasazení modul IoT Edge simulované zařízení] [ lnk-tutorial2] ukazuje, jak zajistit, že je certifikát v úložišti počítače ve vaší aplikaci modulu. Je zřejmé jiným způsobem tak, aby důvěřovala připojení, pomocí které pracují certifikátu.

## <a name="packaging-as-an-image"></a>Balení jako obrázek
Okraj IoT moduly jsou dodávány s imagí Dockeru.
Jak je znázorněno v tomto kurzu můžete použít přímo nástrojů Docker nebo Visual Studio Code [vývoj a nasazení modul IoT Edge simulované zařízení][lnk-tutorial2].

## <a name="next-steps"></a>Další postup

Po vyvíjíte modul, zjistěte, jak [nasadit a monitorovat moduly IoT Edge ve velkém měřítku][lnk-howto-deploy].

[lnk-devicesdk]: ../iot-hub/iot-hub-devguide-sdks.md
[lnk-devicetwin]: ../iot-hub/iot-hub-devguide-device-twins.md
[lnk-iothub-messaging]: ../iot-hub/iot-hub-devguide-messaging.md
[lnk-methods]: ../iot-hub/iot-hub-devguide-direct-methods.md
[lnk-tutorial2]: tutorial-csharp-module.md
[lnk-module-comp]: module-composition.md
[lnk-howto-deploy]: how-to-deploy-monitor.md
