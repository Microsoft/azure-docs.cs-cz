---
title: Vývoj azure iot hubu pro omezená zařízení pomocí sady IoT Hub C SDK
description: Průvodce pro vývojáře – pokyny, jak vyvíjet pomocí sad Azure IoT SDK pro omezená zařízení.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/24/2018
ms.author: robinsh
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 9010ff582f05e81e17e280e20f180ceccf0e746f
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81733207"
---
# <a name="develop-for-constrained-devices-using-azure-iot-c-sdk"></a>Vývoj pro omezená zařízení pomocí sady Azure IoT C SDK

Azure IoT Hub C SDK je napsaný v ANSI C (C99), což je vhodné pro provoz různých platforem s malým diskem a nároky na paměť. Doporučená paměť RAM je alespoň 64 KB, ale přesná paměťová stopa závisí na použitém protokolu, počtu otevřených připojení a cílové platformě.
> [!NOTE]
> * Azure IoT C SDK pravidelně publikuje informace o spotřebě prostředků, které vám pomohou s vývojem.  Navštivte naše [úložiště GitHub](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/c_sdk_resource_information.md) a přečtěte si nejnovější měřítko.
>

Sada C SDK je k dispozici ve formě balíčku od apt-get, NuGet a MBED. Chcete-li cílit na omezená zařízení, můžete chtít vytvořit sdk místně pro cílovou platformu. Tato dokumentace ukazuje, jak odebrat některé funkce zmenšit stopy C SDK pomocí [cmake](https://cmake.org/). Kromě toho tato dokumentace popisuje osvědčené modely programování pro práci s omezenými zařízeními.

## <a name="building-the-c-sdk-for-constrained-devices"></a>Vytváření sady C SDK pro omezená zařízení

Vytvořte c sdk pro omezená zařízení.

### <a name="prerequisites"></a>Požadavky

Postupujte podle tohoto [průvodce nastavením sady C SDK](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md) a připravte vývojové prostředí pro sestavení sady C SDK. Než se dostanete ke kroku pro vytváření s cmake, můžete vyvolat cmake příznaky odebrat nepoužívané funkce.

### <a name="remove-additional-protocol-libraries"></a>Odebrání dalších knihoven protokolů

C SDK dnes podporuje pět protokolů: MQTT, MQTT přes WebSocket, AMQPs, AMQP přes WebSocket a HTTPS. Většina scénářů vyžaduje jeden až dva protokoly spuštěné v klientovi, proto můžete z sady SDK odebrat knihovnu protokolů, kterou nepoužíváte. Další informace o výběru vhodného komunikačního protokolu pro váš scénář najdete v části [Volba komunikačního protokolu služby IoT Hub](iot-hub-devguide-protocols.md). Například MQTT je lehký protokol, který je často vhodnější pro omezená zařízení.

Knihovny AMQP a HTTP můžete odebrat pomocí následujícího příkazu cmake:

```
cmake -Duse_amqp=OFF -Duse_http=OFF <Path_to_cmake>
```

### <a name="remove-sdk-logging-capability"></a>Odebrání možnosti protokolování sady SDK

Sada C SDK poskytuje rozsáhlé protokolování v celém pomoci s laděním. Možnost protokolování pro produkční zařízení můžete odebrat pomocí následujícího příkazu cmake:

```
cmake -Dno_logging=OFF <Path_to_cmake>
```

### <a name="remove-upload-to-blob-capability"></a>Odebrání funkce odesílání do objektu blob

Velké soubory můžete nahrát do Služby Azure Storage pomocí integrované funkce v sadě SDK. Azure IoT Hub funguje jako dispečer přidruženého účtu Azure Storage. Tuto funkci můžete použít k odesílání mediálních souborů, velkých telemetrických dávek a protokolů. Další informace můžete získat [při nahrávání souborů pomocí ioT hubu](iot-hub-devguide-file-upload.md). Pokud vaše aplikace tuto funkci nevyžaduje, můžete tuto funkci odebrat pomocí následujícího příkazu cmake:

```
cmake -Ddont_use_uploadtoblob=ON <Path_to_cmake>
```

### <a name="running-strip-on-linux-environment"></a>Běžící proužek v prostředí Linuxu

Pokud vaše binární soubory běží v systému Linux, můžete využít [příkaz strip](https://en.wikipedia.org/wiki/Strip_(Unix)) ke snížení velikosti konečné aplikace po kompilaci.

```
strip -s <Path_to_executable>
```

## <a name="programming-models-for-constrained-devices"></a>Programovací modely pro omezená zařízení

Dále se podívejte na programovací modely pro omezená zařízení.

### <a name="avoid-using-the-serializer"></a>Nepoužívejte serializátor

Sada C SDK má volitelný [serializátor sady C SDK](https://github.com/Azure/azure-iot-sdk-c/tree/master/serializer), který umožňuje definovat metody a vlastnosti dvojčete zařízení pomocí deklarativních mapových tabulek. Serializátor je navržen tak, aby zjednodušil vývoj, ale přidává režii, což není optimální pro omezená zařízení. V takovém případě zvažte použití primitivních klientských api a analýzu JSON pomocí zjednodušeného analyzátoru, například [parson](https://github.com/kgabis/parson).

### <a name="use-the-lower-layer-_ll_"></a>Použijte spodní vrstvu (_LL_)

Sada C SDK podporuje dva programovací modely. Jedna sada má api s _ll_ infix, což znamená nižší vrstvu. Tato sada rozhraní API je lehčí a nesnižujte pracovní podprocesy, což znamená, že uživatel musí ručně řídit plánování. Například pro klienta zařízení _ll_ rozhraní API lze nalézt v tomto [souboru záhlaví](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/inc/iothub_device_client_ll.h). 

Další sada rozhraní API bez indexu _LL_ se nazývá vrstva pohodlí, kde se pracovní podproces automaticky otáčí. Například rozhraní API vrstvy pohodlí pro klienta zařízení lze nalézt v tomto [souboru hlavičky klienta zařízení IoT](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/inc/iothub_device_client.h). U omezených zařízení, kde každé další vlákno může trvat podstatné procento systémových prostředků, zvažte _použití_ rozhraní LL API.

## <a name="next-steps"></a>Další kroky

Další informace o architektuře Azure IoT C SDK:
-    [Zdrojový kód Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c/)
-    [Azure IoT zařízení SDK pro zavedení C](iot-hub-device-sdk-c-intro.md)
