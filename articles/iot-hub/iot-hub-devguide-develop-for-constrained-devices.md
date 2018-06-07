---
title: Azure IoT Hub vývoji pro omezené zařízení | Microsoft Docs
description: Příručka vývojáře – pokyny pro vývoj pomocí sady SDK služby Azure IoT pro omezené zařízení.
services: iot-hub
documentationcenter: c
author: yzhong94
manager: timlt
editor: ''
ms.assetid: 979136db-c92d-4288-870c-f305e8777bdd
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/24/2018
ms.author: yizhon
ms.openlocfilehash: 62065b78e3f8191c6423ba9dba4a8f7d16fad114
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34654843"
---
# <a name="develop-for-constrained-devices-using-azure-iot-sdks"></a>Vývoj pro omezené zařízení pomocí sady SDK služby Azure IoT

## <a name="develop-using-azure-iot-hub-c-sdk"></a>Vývoj pomocí sady SDK Azure IoT Hub C
Azure IoT Hub C SDK je napsán v ANSI C (C99), takže je vhodné pracovat mnoha různých platforem s malé nároky disku a paměť.  Doporučená velikost paměti RAM je minimálně 64 KB, ale nároky na paměť přesný závisí na protokol použitý, počet připojení otevřené, jakož i cílové platformy.

C SDK je k dispozici v podobě balíčku z výstižný get, NuGet a MBED.  Pokud chcete zacílit omezené zařízení, můžete sestavení sady SDK místně pro svou cílovou platformu. Tato dokumentace ukazuje, jak k odebrání některých funkcí zmenšit nároky na používání sady SDK C [cmake][lnk-cmake].  Kromě toho tato dokumentace popisuje nejlepší postup programovací modely pro práci s omezené zařízení.

### <a name="building-the-c-sdk-for-constrained-devices"></a>Sestavení sady SDK C pro omezené zařízení
#### <a name="prerequisites"></a>Požadavky
Potom zadejte [Průvodce nastavením] [ lnk-devbox-setup] Příprava vývojového prostředí pro vytváření C sady SDK.  Předtím, než dojde k kroku pro vytvoření s cmake, můžete vyvolat cmake příznaky tak, aby odebrat nepoužité funkce.

#### <a name="remove-additional-protocol-libraries"></a>Odeberte další protokol knihovny
C SDK podporuje pět protokoly dnes: MQTT, MQTT přes protokol WebSocket, AMQPs, AMQP prostřednictvím protokolu WebSocket a HTTPS.    Většina scénářů vyžaduje jednu až dvě protokoly spuštění v klientském počítači, proto je možné odebrat protokol knihovna, kterou nepoužíváte ze sady SDK.  Další informace o volbě příslušnou komunikační protokol, pro váš scénář naleznete v tomto [dokumentu][lnk-choosing-protocol].  Například MQTT je zjednodušený protokol, který je často vhodnější pro omezené zařízení.

Můžete odebrat AMQP a HTTP knihovny pomocí následujícího příkazu cmake:
```
cmake -Duse_amqp=OFF -Duse_http=OFF <Path_to_cmake>
```

#### <a name="remove-sdk-logging-capability"></a>Odebrání možnosti protokolování v sadě SDK
C SDK poskytuje rozsáhlé protokolování v rámci pomoci při ladění. Můžete odebrat možnosti protokolování pro produkční zařízení pomocí následujícího příkazu cmake:
```
cmake -Dno_logging=OFF <Path_to_cmake>
```

#### <a name="remove-upload-to-blob-capability"></a>Odebrat odesílání do objektu blob schopností
Můžete nahrát velkých souborů do služby Azure Storage pomocí integrované funkce v sadě SDK.  Azure IoT Hub funguje jako dispečera do přidruženého účtu úložiště Azure.  Tato funkce slouží k odesílání souborů médií, velké telemetrie dávky a protokoly.  Další informace o nahrání souborů službou IoT Hub v tomto [dokumentu][lnk-hub-file-upload].  Pokud aplikace nevyžaduje tuto funkci, můžete tuto funkci pomocí následujícího příkazu cmake odebrat:
```
cmake -Ddont_use_uploadtoblob=ON <Path_to_cmake>
```
#### <a name="running-strip-on-linux-environment"></a>Spuštěné pruhu v prostředí Linux
Pokud vaše binární soubory spustit v systému Linux, můžete využít [pruhu příkaz] [ lnk-strip] ke snížení velikosti konečné aplikace po kompilace.
```
strip -s <Path_to_executable>
```

### <a name="programming-models-for-constrained-devices"></a>Programování modely pro omezené zařízení
#### <a name="avoid-using-the-serializer"></a>Nepoužívejte serializátor
Sada SDK C obsahuje volitelný [serializátor][lnk-serializer], která umožňuje použít k definování metody a vlastnosti zařízení twin deklarativní mapování tabulek.  Serializátor je navržena pro zjednodušení vývoj, ale přidá režie, což není optimální pro omezené zařízení.  V tomto případě zvažte použití jednoduchých rozhraní API klienta a analyzovat json pomocí lightweight analyzátor [parson][lnk-parson].

#### <a name="use-the-lower-layer-ll"></a>Použití nižší vrstvy (_UDOU_)
C SDK podporuje dva programovací modely.  Má jedna sada rozhraní API se _UDOU_ infix, což je zkratka pro nižší úrovně.  Tato sada rozhraní API jsou světlejší váhy a není začne pracovat pracovních vláken, což znamená, že uživatel musí ručně řídit plánování.  Například pro klienta zařízení _UDOU_ rozhraní API najdete v tomto [soubor hlaviček](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/inc/iothub_device_client_ll.h).  Další sadu rozhraní API bez _UDOU_ index se nazývá vrstva pohodlí, kde je automaticky spouštějí pracovní vlákno.  Například pohodlí vrstvu rozhraní API pro klienta zařízení najdete v tomto [soubor hlaviček](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/inc/iothub_device_client.h).  Omezené zařízení, kde každé další vlákno, může trvat dlouhou procento prostředky systému, zvažte použití _UDOU_ rozhraní API.

## <a name="next-steps"></a>Další postup
Další informace o architektuře sady SDK Azure IoT C:
-   [Azure IoT C SDK zdrojového kódu](https://github.com/Azure/azure-iot-sdk-c/)
-   [Pro zařízení Azure IoT SDK Úvod C](https://docs.microsoft.com/azure/iot-hub/iot-hub-device-sdk-c-intro)

------
[lnk-cmake]: https://cmake.org/
[lnk-devbox-setup]:  https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md
[lnk-choosing-protocol]: https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-protocols
[lnk-hub-file-upload]: https://docs.microsoft.com/en-us/azure/iot-hub/iot-hub-devguide-file-upload
[lnk-strip]: https://en.wikipedia.org/wiki/Strip_(Unix)
[lnk-serializer]: https://github.com/Azure/azure-iot-sdk-c/tree/master/serializer
[lnk-parson]: https://github.com/kgabis/parson