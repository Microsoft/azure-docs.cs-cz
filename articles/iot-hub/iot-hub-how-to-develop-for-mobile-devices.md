---
title: Vývoj pro mobilní zařízení pomocí sad Azure IoT SDK | Dokumenty společnosti Microsoft
description: Průvodce pro vývojáře – Přečtěte si, jak vyvíjet pro mobilní zařízení pomocí sad Azure IoT Hub SDK.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/16/2018
ms.author: robinsh
ms.openlocfilehash: 945b02003a443c04e692fdc06ca5714de362d074
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "68883085"
---
# <a name="develop-for-mobile-devices-using-azure-iot-sdks"></a>Vývoj pro mobilní zařízení pomocí sad Azure IoT SDK

Věci na internetu věcí mohou odkazovat na širokou škálu zařízení s různou schopností: senzory, mikrokontroléry, inteligentní zařízení, průmyslové brány a dokonce i mobilní zařízení.  Mobilní zařízení může být zařízení IoT, kde odesílá telemetrii zařízení do cloudu a spravuje cloud.  Může to být také zařízení se spuštěnou aplikací back-end služby, která spravuje další zařízení IoT.  V obou případech lze sady [Azure IoT Hub SDK](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks) použít k vývoji aplikací, které fungují pro mobilní zařízení.  

## <a name="develop-for-native-ios-platform"></a>Vývoj pro nativní platformu iOS

Sady Azure IoT Hub SDK poskytují nativní podporu platformy iOS prostřednictvím sady Azure IoT Hub C SDK.  Můžete si to myslet jako iOS SDK, který můžete začlenit do projektu Swift nebo objective C XCode.  Sada C SDK v iOS lze používat dvěma způsoby:

* Knihovny CocoaPod můžete používat přímo v projektu XCode.  
* Stáhněte si zdrojový kód pro C SDK a sestavení pro platformu iOS podle [pokynů sestavení](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md) pro MacOS.  

Azure IoT Hub C SDK je napsaný v C99 pro maximální přenositelnost na různé platformy.  Proces přenosu zahrnuje psaní tenké vrstvy přijetí pro komponenty specifické pro platformu, které lze nalézt zde pro [iOS](https://github.com/Azure/azure-c-shared-utility/tree/master/pal/ios-osx).  Funkce v c SDK lze využít na platformě iOS, včetně podporovaných primitivních hubů Azure IoT Hub a funkcí specifických pro SDK, jako jsou zásady opakování pro spolehlivost sítě.  Rozhraní pro iOS SDK je také podobné rozhraní pro Azure IoT Hub C SDK.  

Tyto dokumentace vás provedou vývojem aplikace nebo služby zařízení na zařízení se systémem iOS:

* [Rychlý start: Odesílání telemetrických dat ze zařízení do centra IoT](quickstart-send-telemetry-ios.md)  
* [Odesílání zpráv z cloudu do zařízení pomocí služby IoT hub](iot-hub-ios-swift-c2d.md) 

### <a name="develop-with-azure-iot-hub-cocoapod-libraries"></a>Vývoj s knihovnami Azure IoT Hub CocoaPod

Sady Azure IoT Hub SDK vydávají sadu knihoven CocoaPod Objective-C pro vývoj iOS.  Nejnovější seznam knihoven CocoaPod najdete v tématu [CocoaPods for Microsoft Azure IoT](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/samples/ios/CocoaPods.md).  Jakmile jsou příslušné knihovny začleněny do projektu XCode, existují dva způsoby, jak napsat kód související s IoT Hub:

* Funkce cíle C: Pokud je váš projekt napsaný v objective-C, můžete volat api z Azure IoT Hub C SDK přímo.  Pokud váš projekt je napsán v `@objc func` Swift, můžete volat před vytvořením funkce a pokračovat v psaní všech logik souvisejících s Azure IoT Hub pomocí kódu C nebo Objective-C.  Sada vzorků demonstrujících obojí lze nalézt v [ukázkovém úložišti](https://github.com/Azure-Samples/azure-iot-samples-ios).  

* Začlenit ukázky C: Pokud jste napsali aplikaci zařízení C, můžete na něj odkazovat přímo v projektu XCode:
    * Přidejte soubor sample.c do projektu XCode z XCode.  
    * Přidejte soubor záhlaví do závislosti.  Jako příklad je součástí [ukázkového úložiště](https://github.com/Azure-Samples/azure-iot-samples-ios) soubor záhlaví. Další informace naleznete na stránce dokumentace společnosti Apple pro [objective-C](https://developer.apple.com/documentation/objectivec).

## <a name="develop-for-android-platform"></a>Vývoj pro platformu Android
Azure IoT Hub Java SDK podporuje platformu Android.  Pro konkrétní verzi rozhraní API testovány, navštivte naši [stránku podpory platformy](iot-hub-device-sdk-platform-support.md) pro nejnovější aktualizace.

Tyto dokumentace procházet, jak vytvořit aplikaci zařízení nebo servisní aplikace na zařízení se systémem Android pomocí Gradle a Android Studio:

* [Rychlý start: Odesílání telemetrických dat ze zařízení do centra IoT](quickstart-send-telemetry-android.md)  
* [Úvodní příručka: Ovládání zařízení](quickstart-control-device-android.md) 

## <a name="next-steps"></a>Další kroky

* [Odkaz na rozhraní REST centra IoT](https://docs.microsoft.com/rest/api/iothub/)
* [Zdrojový kód Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c)
