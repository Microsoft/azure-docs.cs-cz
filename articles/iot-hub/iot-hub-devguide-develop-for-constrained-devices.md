---
title: Azure IoT Hub vyvíjet pro omezená zařízení pomocí IoT Hub C SDK
description: Příručka pro vývojáře – pokyny k vývoji používání sad SDK Azure IoT pro omezená zařízení.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/24/2018
ms.author: robinsh
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 5a43eb2537ebc09ffcb524a4426d7a8c9bec560b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96499998"
---
# <a name="develop-for-constrained-devices-using-azure-iot-c-sdk"></a>Vývoj pro omezená zařízení pomocí sady Azure IoT C SDK

Sada Azure IoT Hub C SDK je napsaná v ANSI C (C99), která je vhodná pro provoz řady platforem s malými nároky na disk a paměť. Doporučená paměť RAM je minimálně 64 KB, ale přesné nároky na paměť závisí na použitém protokolu, počtu otevřených připojení a na cílové platformě.
> [!NOTE]
> * Sada Azure IoT C SDK pravidelně zveřejňuje informace o spotřebě prostředků, které vám pomůžou s vývojem.  Podívejte se na naše [úložiště GitHub](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/c_sdk_resource_information.md) a Prohlédněte si nejnovější srovnávací testy.
>

Sada C SDK je k dispozici ve formě balíčku z apt-get, NuGet a MBED. Chcete-li cílit na omezená zařízení, můžete chtít sestavit sadu SDK místně pro vaši cílovou platformu. Tato dokumentace ukazuje, jak odebrat některé funkce, abyste mohli zmenšit nároky sady C SDK pomocí [cmake](https://cmake.org/). Tato dokumentace navíc popisuje programovací modely osvědčených postupů pro práci s omezenými zařízeními.

## <a name="building-the-c-sdk-for-constrained-devices"></a>Sestavení sady C SDK pro omezená zařízení

Sestavte sadu C SDK pro omezená zařízení.

> [!NOTE]
> Vložená sada C SDK je alternativou pro omezená zařízení, která podporují přístup k síti BYON (Přineste si vlastní síť). Vývojáři IoT mají volnost v navýšení klienta MQTT, TLS a zásuvky pro vytvoření řešení zařízení. [Přečtěte si další informace o vložené sadě C SDK](https://github.com/Azure/azure-sdk-for-c/tree/master/sdk/docs/iot).

### <a name="prerequisites"></a>Předpoklady

Postupujte podle pokynů v [Průvodci nastavením sady c SDK](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md) a připravte vývojové prostředí pro sestavení sady C SDK. Předtím, než se dostanete k kroku sestavování pomocí cmake, můžete vyvolat příznaky cmake a odebrat nepoužívané funkce.

### <a name="remove-additional-protocol-libraries"></a>Odebrat další knihovny protokolů

C SDK podporuje dnes pět protokolů: MQTT, MQTT přes WebSocket, AMQPs, AMQP přes WebSocket a HTTPS. Většina scénářů vyžaduje jeden až dva protokoly běžící na klientovi, proto můžete z SDK odebrat knihovnu protokolů, kterou nepoužíváte. Další informace o výběru vhodného komunikačního protokolu pro váš scénář najdete v části [Výběr komunikačního protokolu IoT Hub](iot-hub-devguide-protocols.md). MQTT je například odlehčený protokol, který je často lépe vhodný pro omezená zařízení.

Knihovny AMQP a HTTP můžete odebrat pomocí následujícího příkazu cmake:

```
cmake -Duse_amqp=OFF -Duse_http=OFF <Path_to_cmake>
```

### <a name="remove-sdk-logging-capability"></a>Odebrání funkce protokolování sady SDK

Sada C SDK poskytuje rozsáhlé protokolování v celé aplikaci, které vám pomůžou s laděním. Možnosti protokolování pro produkční zařízení můžete odebrat pomocí následujícího příkazu cmake:

```
cmake -Dno_logging=OFF <Path_to_cmake>
```

### <a name="remove-upload-to-blob-capability"></a>Odebrat možnost Odeslat do objektu BLOB

K nahrávání velkých souborů můžete Azure Storage použít integrovanou funkci v sadě SDK. Azure IoT Hub slouží jako dispečer k přidruženému Azure Storagemu účtu. Tuto funkci můžete použít k posílání mediálních souborů, velkých dávek telemetrie a protokolů. Můžete získat další informace o [nahrávání souborů pomocí IoT Hub](iot-hub-devguide-file-upload.md). Pokud vaše aplikace tuto funkci nevyžaduje, můžete tuto funkci odebrat pomocí následujícího příkazu cmake:

```
cmake -Ddont_use_uploadtoblob=ON <Path_to_cmake>
```

### <a name="running-strip-on-linux-environment"></a>Spuštění Stripe v prostředí systému Linux

Pokud se vaše binární soubory spouštějí v systému Linux, můžete pomocí [příkazu Stripe](https://en.wikipedia.org/wiki/Strip_(Unix)) zmenšit velikost konečné aplikace po zkompilování.

```
strip -s <Path_to_executable>
```

## <a name="programming-models-for-constrained-devices"></a>Programovací modely pro omezená zařízení

Pak se podívejte na programové modely pro omezená zařízení.

### <a name="avoid-using-the-serializer"></a>Nepoužívejte serializátor

Sada C SDK má volitelný [serializátor jazyka c SDK](https://github.com/Azure/azure-iot-sdk-c/tree/master/serializer), který umožňuje použití tabulek deklarativního mapování k definování metod a vlastností, které jsou v zařízení. Serializátor je navržený tak, aby zjednodušil vývoj, ale přináší režijní náklady, které nejsou pro omezená zařízení optimální. V takovém případě zvažte použití primitivních klientských rozhraní API a analýzu formátu JSON pomocí zjednodušeného analyzátoru, jako je [Parson](https://github.com/kgabis/parson).

### <a name="use-the-lower-layer-_ll_"></a>Použít nižší vrstvu (_chny_)

Sada C SDK podporuje dva programovací modely. Jedna sada má rozhraní API s _ll_ vpony, která představuje nižší vrstvu. Tato sada rozhraní API je světlejší o váhu a neprovádí se žádná pracovní vlákna, což znamená, že uživatel musí ručně řídit plánování. Například pro klienta zařízení lze _rozhraní API najít_ v tomto [hlavičkovém souboru](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/inc/iothub_device_client_ll.h). 

Další sadou rozhraní API bez indexu _ll_ se říká výhodná vrstva, kde je pracovní vlákno automaticky prospuninstelné. Například rozhraní API vrstvy pro usnadnění pro klienta zařízení najdete v tomto [hlavičkovém souboru klienta zařízení IoT](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/inc/iothub_device_client.h). U omezených zařízení, kde může každé dodatečné vlákno probírat značné procento systémových prostředků, _zvažte použití rozhraní_ API pro všechny.

## <a name="next-steps"></a>Další kroky

Další informace o architektuře sady SDK Azure IoT C:
-    [Zdrojový kód sady SDK Azure IoT C](https://github.com/Azure/azure-iot-sdk-c/)
-    [Sada SDK pro zařízení Azure IoT pro jazyk C Úvod](iot-hub-device-sdk-c-intro.md)
