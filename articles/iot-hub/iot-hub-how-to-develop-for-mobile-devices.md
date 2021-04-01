---
title: Vývoj pro mobilní zařízení pomocí sad Azure IoT SDK | Microsoft Docs
description: Příručka pro vývojáře – Přečtěte si, jak vyvíjet mobilní zařízení pomocí sad Azure IoT Hub SDK.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/16/2018
ms.author: robinsh
ms.openlocfilehash: 63fade17d10189cbe8e775b2a859902f1c2bb004
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "92151634"
---
# <a name="develop-for-mobile-devices-using-azure-iot-sdks"></a>Vývoj pro mobilní zařízení pomocí sad Azure IoT SDK

Věci v Internet věcí se můžou týkat široké škály zařízení s různou schopností: senzory, mikrořadiče, inteligentní zařízení, průmyslové brány a dokonce mobilní zařízení.  Mobilním zařízením může být zařízení IoT, kde odesílá telemetrii ze zařízení do cloudu a spravuje je Cloud.  Může se také jednat o zařízení, na kterém běží aplikace služby back-end, která spravuje jiná zařízení IoT.  V obou případech můžete použít sady [Azure IoT Hub SDK](./iot-hub-devguide-sdks.md) pro vývoj aplikací, které fungují pro mobilní zařízení.  

## <a name="develop-for-native-ios-platform"></a>Vývoj pro nativní platformu iOS

Sady SDK pro Azure IoT Hub poskytují nativní podporu platformy iOS prostřednictvím Azure IoT Hub C SDK.  Můžete si ho představit jako sadu SDK pro iOS, kterou můžete začlenit do svého projektu SWIFT nebo objektivu C XCode.  Existují dva způsoby, jak použít sadu C SDK v systému iOS:

* Použijte přímo knihovny CocoaPod v projektu XCode.  
* Stáhněte si zdrojový kód pro C SDK a sestavte pro platformu iOS podle [pokynů pro sestavení](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md) pro MacOS.  

Sada Azure IoT Hub C SDK je napsaná v C99 pro maximální přenositelnost na různé platformy.  Proces přenosu zahrnuje zápis vrstvy tenkého přijetí pro součásti specifické pro platformu, které najdete tady pro [iOS](https://github.com/Azure/azure-c-shared-utility/tree/master/pal/ios-osx).  Funkce v sadě C SDK je možné využít na platformě iOS, včetně podporovaných primitiv IoT Hub Azure a funkcí specifických pro sadu SDK, jako jsou zásady opakování pro spolehlivost sítě.  Rozhraní pro sadu iOS SDK je podobné rozhraní pro Azure IoT Hub C SDK.  

Tato dokumentace vás seznámí s postupem vývoje aplikace zařízení nebo aplikace služby na zařízení s iOS:

* [Rychlý start: Odesílání telemetrických dat ze zařízení do centra IoT](quickstart-send-telemetry-ios.md)  
* [Posílání zpráv z cloudu do zařízení ve službě IoT Hub](iot-hub-ios-swift-c2d.md) 

### <a name="develop-with-azure-iot-hub-cocoapod-libraries"></a>Vývoj s využitím CocoaPod knihoven Azure IoT Hub

Azure IoT Hub SDK uvolňují sadu účelových knihoven C CocoaPod pro vývoj pro iOS.  Nejnovější seznam knihoven CocoaPod najdete v tématu [CocoaPods for Microsoft Azure IoT](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/samples/ios/CocoaPods.md).  Jakmile jsou relevantní knihovny začleněny do projektu XCode, existují dva způsoby, jak napsat kód související s IoT Hub:

* Cíl C – funkce: Pokud je projekt napsán v cíli – C, můžete volat rozhraní API přímo z Azure IoT Hub C SDK.  Pokud je projekt napsán v SWIFT, můžete volat `@objc func` před vytvořením funkce a pokračovat v psaní všech logics souvisejících se službou Azure IoT Hub pomocí jazyka c nebo objektivního kódu jazyka c.  V [ukázkovém úložišti](https://github.com/Azure-Samples/azure-iot-samples-ios)se dají najít sady ukázek, které demonstrují obojí.  

* Zahrnutí ukázek C: Pokud jste napsali aplikaci pro zařízení v jazyce C, můžete na ni odkazovat přímo v projektu XCode:
    * Do projektu XCode přidejte soubor Sample. c z XCode.  
    * Přidejte soubor hlaviček do závislosti.  Hlavičkový soubor je v [ukázkovém úložišti](https://github.com/Azure-Samples/azure-iot-samples-ios) obsažen jako příklad. Další informace najdete na stránce dokumentace společnosti Apple pro [cíl-C](https://developer.apple.com/documentation/objectivec).

## <a name="develop-for-android-platform"></a>Vývoj pro platformu Android
Sada SDK pro Azure IoT Hub Java podporuje platformu Android.  U konkrétní testované verze rozhraní API navštivte prosím nejnovější aktualizaci na naší [stránce podpory platformy](iot-hub-device-sdk-platform-support.md) .

Tato dokumentace vás seznámí s postupem vývoje aplikace zařízení nebo aplikace služby na zařízení s Androidem pomocí Gradle a Android Studio:

* [Rychlý start: Odesílání telemetrických dat ze zařízení do centra IoT](quickstart-send-telemetry-android.md)  
* [Rychlý Start: řízení zařízení](quickstart-control-device-android.md) 

## <a name="next-steps"></a>Další kroky

* [Odkaz na IoT Hub REST API](/rest/api/iothub/)
* [Zdrojový kód sady SDK Azure IoT C](https://github.com/Azure/azure-iot-sdk-c)