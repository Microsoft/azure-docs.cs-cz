---
title: Vývoj pro mobilní zařízení pomocí sad SDK Azure IoT | Dokumentace Microsoftu
description: Pro vývojáře průvodce – Další informace o vývoji pro mobilní zařízení pomocí sady SDK služby Azure IoT Hub.
author: yzhong94
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/16/2018
ms.author: yizhon
ms.openlocfilehash: 5256a58a2b68584888abcac915392d8e389e9772
ms.sourcegitcommit: d61faf71620a6a55dda014a665155f2a5dcd3fa2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54054533"
---
# <a name="develop-for-mobile-devices-using-azure-iot-sdks"></a>Vývoj pro mobilní zařízení pomocí sad SDK Azure IoT

Věci v Internet of Things mohou odkazovat na širokou škálu zařízení s různými schopností: senzorů, mikrokontrolerů po, chytrými zařízeními, průmyslové brány a dokonce i mobilní zařízení.  Mobilní zařízení může být zařízení IoT, kde odesílá telemetrická data typu zařízení cloud a spravovaná v cloudu.  Může být zařízení s back-end služby aplikací v jazyce, který spravuje jiná zařízení IoT.  V obou případech [sady SDK služby Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks) slouží k vývoji aplikací, které fungují pro mobilní zařízení.  

## <a name="develop-for-native-ios-platform"></a>Vývoj pro platformu nativní aplikace pro iOS

Sady SDK služby Azure IoT Hub poskytovat podporu platformu nativní aplikace pro iOS prostřednictvím Azure IoT Hub C SDK.  Jeho si můžete představit jako iOS SDK, která můžete začlenit ve vašem projektu Swift a Objective C XCode.  Existují dva způsoby, jak používat sadu SDK C v iOS:

* Přímo pomocí knihoven CocoaPod v projektu XCode.  
* Stáhněte si zdrojový kód pro SDK pro jazyk C a sestavení pro následující platformy iOS [sestavit instrukci](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md) pro MacOS.  

C SDK pro Azure IoT Hub je napsána v C99 pro zajištění maximální přenositelnosti na různé platformy.  Přenos proces zahrnuje vrstvu dynamického zajišťování přijetí pro konkrétní platformy součásti, které se nachází tady pro zápis [iOS](https://github.com/Azure/azure-c-shared-utility/tree/master/pal/ios-osx).  Funkce v jazyce C SDK můžete využít na platformě iOS, včetně Azure IoT Hub primitivy podporované a funkce specifické pro sadu SDK jako zásady sítě spolehlivosti opakování.  Rozhraní pro iOS SDK je také podobné rozhraní pro Azure IoT Hub C SDK.  

Tato dokumentace provede postupy při vývoji aplikace zařízení nebo aplikace služby na zařízení s iOS:

* [Rychlý start: Odesílání telemetrických dat ze zařízení do služby IoT hub](quickstart-send-telemetry-ios.md)  
* [Odesílání zpráv z cloudu do zařízení pomocí služby IoT hub](iot-hub-ios-swift-c2d.md) 

### <a name="develop-with-azure-iot-hub-cocoapod-libraries"></a>Vývoj s využitím Azure IoT Hub CocoaPod knihovny

Sady SDK služby Azure IoT Hub uvolní sadu knihoven CocoaPod Objective-C pro vývoj pro iOS.  Nejnovější seznam CocoaPod knihovny najdete v tématu [CocoaPods pro Microsoft Azure IoT](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/samples/ios/CocoaPods.md).  Po příslušné knihovny jsou začleněny do projektu XCode, existují dva způsoby, jak napsat kód týkající se služby IoT Hub:

* Objective C funkce: Pokud váš projekt je napsán v jazyce Objective-C, můžete volat rozhraní API z Azure IoT Hub C SDK přímo.  Pokud váš projekt je napsané ve Swiftu, můžete volat `@objc func` před vytvořením funkce a pokračujte na zápis všech logics související s Azure IoT Hub pomocí jazyka C nebo Objective-C kódu.  Řadu ukázek v představením toho, jak lze nalézt v [ukázkové úložiště](https://github.com/Azure-Samples/azure-iot-samples-ios).  

* Ukázky C zahrnovat: Pokud jste vytvořili aplikaci pro zařízení jazyka C, můžete na něj mohli odkazovat přímo ve vašem projektu XCode:
    * Přidáte soubor sample.c z XCode do svého projektu XCode.  
    * Hlavičkový soubor přidáte do vašich závislostí.  Soubor hlaviček je součástí [ukázkové úložiště](https://github.com/Azure-Samples/azure-iot-samples-ios) jako příklad. Další informace, navštivte prosím stránku dokumentace společnosti Apple pro [Objective-C](https://developer.apple.com/documentation/objectivec).

## <a name="develop-for-android-platform"></a>Vývoj pro platformu Android
Sada Azure IoT Hub Java SDK podporuje platformy Android.  Pro konkrétní verzi rozhraní API, testovat, navštivte prosím náš [stránku podpory platformy](iot-hub-device-sdk-platform-support.md) nejnovější aktualizace.

Tato dokumentace provede postupy při vývoji aplikace zařízení nebo aplikace služby na zařízeních s Androidem pomocí Gradle a Android Studio:

* [Rychlý start: Odesílání telemetrických dat ze zařízení do služby IoT hub](quickstart-send-telemetry-android.md)  
* [Rychlý start: Řízení zařízení](quickstart-control-device-android.md) 

## <a name="next-steps"></a>Další postup

* [Reference k rozhraní API REST centra IoT](https://docs.microsoft.com/rest/api/iothub/)
* [Azure IoT C SDK zdrojového kódu](https://github.com/Azure/azure-iot-sdk-c)
