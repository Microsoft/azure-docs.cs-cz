---
title: Vývoj pro mobilní zařízení pomocí sady SDK služby Azure IoT | Microsoft Docs
description: Vývojáře průvodce – informace o vývoji pro mobilní zařízení pomocí sady SDK služby Azure IoT Hub.
services: iot-hub
documentationcenter: ''
author: yzhong94
manager: timlt
editor: ''
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/16/2018
ms.author: yizhon
ms.openlocfilehash: 824d577a4709a388bd858bdda6ef5f11f53dde58
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/01/2018
---
# <a name="develop-for-mobile-devices-using-azure-iot-sdks"></a>Vývoj pro mobilní zařízení pomocí sady SDK služby Azure IoT

Co v Internetu věcí mohou odkazovat na širokou škálu zařízení s různými možnostmi: senzorů, microcontrollers, inteligentní zařízení, průmyslových brány a i mobilní zařízení.  Mobilní zařízení může být zařízení IoT, kde je odesílání telemetrických dat ze zařízení cloud a spravuje cloudu.  Také může být spuštěna služba back endu aplikace, která spravuje jiná zařízení IoT zařízení.  V obou případech [sady SDK služby Azure IoT Hub] [ lnk-sdk-overview] lze použít k vývoji aplikací, které fungují pro mobilní zařízení.  

## <a name="develop-for-native-ios-platform"></a>Vývoj pro platformu nativní aplikace pro iOS

Sady SDK služby Azure IoT Hub poskytovat podporu platformy nativní aplikace pro iOS pomocí sady SDK Azure IoT Hub C.  To si lze představit jako iOS SDK, která můžete začlenit do projektu Swift nebo Objective C XCode.  Existují dva způsoby, jak používat sadu SDK C v systému iOS:
- Přímo pomocí knihoven CocoaPod v projektu XCode.  
- Stažení zdrojového kódu pro C sady SDK a build pro tyto platformy iOS [sestavení instrukce] [ lnk-c-devbox] pro systému MacOS.  

Azure IoT Hub C SDK je napsán v C99 pro maximální přenositelnost na různých platformách.  Přenosem proces zahrnuje zápis vrstvu dynamické přijetí pro komponenty specifických pro platformy, které se nachází tady pro [iOS][lnk-ios-pal].  Funkce v sadě SDK C můžete využít na platformě iOS, včetně primitiv Azure IoT Hub podporována a funkce specifické pro sadu SDK, jako opakujte zásady pro spolehlivost sítě.  Rozhraní pro iOS SDK je také podobná rozhraní Azure IoT Hub C sady SDK.  

Tato dokumentace provede jak vyvíjet aplikace zařízení nebo aplikace služby na zařízení s iOS:
- [Rychlý úvod: Odeslání telemetrie ze zařízení do služby IoT hub][lnk-device-ios-quickstart]  
- [Odesílání zpráv z cloudu do zařízení s centrem IoT][lnk-service-ios-quickstart]  

### <a name="develop-with-azure-iot-hub-cocoapod-libraries"></a>Vývoj pomocí Azure IoT Hub CocoaPod knihovny

Sady SDK služby Azure IoT Hub uvolní sada knihoven jazyka Objective-C CocoaPod pro vývoj pro iOS.  Nejnovější seznam CocoaPod knihovny najdete v sekci [CocoaPods pro Microsoft Azure IoT][lnk-cocoapod-list].  Jakmile relevantní knihovny jsou součástí projektu XCode, zápis IoT Hub související kód dvěma způsoby:
- Funkce Objective C: Pokud váš projekt je napsán v jazyce Objective-C, můžete volat rozhraní API z Azure IoT Hub C SDK přímo.  Pokud váš projekt je napsána v Swift, můžete zavolat ``@objc func`` před vytváření funkce a přejít k zápisu všech logics související s Azure IoT Hub pomocí jazyka C nebo jazyka Objective-C kódu.  Sady vzorků ukázka, jak lze nalézt v [úložiště ukázkové][lnk-ios-samples-repo].  
- Začlenit ukázky C: Pokud jste napsali aplikace C zařízení, můžete ho odkazovat přímo ve vašem projektu XCode:
    - Přidejte soubor sample.c do projektu XCode z XCode.  
    - Přidejte soubor hlavičky k vaší závislosti.  Záhlaví souboru je součástí [úložiště ukázkové] [ lnk-ios-samples-repo] jako příklad.  Další informace, navštivte stránky dokumentace společnosti Apple pro [jazyka Objective-C](https://developer.apple.com/documentation/objectivec).

## <a name="next-steps"></a>Další postup
* [Referenční dokumentace rozhraní API REST centra IoT][lnk-rest-ref]
* [Azure IoT C SDK zdrojového kódu][lnk-c-sdk]

[lnk-sdk-overview]: https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks
[lnk-c-devbox]: https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md
[lnk-device-ios-quickstart]:https://docs.microsoft.com/azure/iot-hub/quickstart-send-telemetry-ios
[lnk-service-ios-quickstart]: https://docs.microsoft.com/azure/iot-hub/iot-hub-ios-swift-c2d
[lnk-cocoapod-list]: https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/samples/ios/CocoaPods.md
[lnk-ios-samples-repo]: https://github.com/Azure-Samples/azure-iot-samples-ios
[lnk-ios-pal]: https://github.com/Azure/azure-c-shared-utility/tree/master/pal/ios-osx
[lnk-c-sdk]: https://github.com/Azure/azure-iot-sdk-c
[lnk-rest-ref]: https://docs.microsoft.com/rest/api/iothub/
