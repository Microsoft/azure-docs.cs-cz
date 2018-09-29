---
title: Azure IoT Hub vývoj pro zařízení s omezenými pomocí jazyka C SDK pro IoT Hub | Dokumentace Microsoftu
description: Příručka pro vývojáře – pokyny pro vývoj pomocí sady Azure IoT SDK pro zařízení s omezením.
author: yzhong94
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/24/2018
ms.author: yizhon
ms.openlocfilehash: 683f3ca88c349fef31f9647566dbed8a840f94dd
ms.sourcegitcommit: f31bfb398430ed7d66a85c7ca1f1cc9943656678
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/28/2018
ms.locfileid: "47451727"
---
# <a name="develop-for-constrained-devices-using-azure-iot-c-sdk"></a>Vývoj pro omezené zařízení pomocí sady Azure IoT C SDK

C SDK pro Azure IoT Hub je zapsána ve standardu ANSI C (C99), takže je vhodné pracovat různých platformách s malou stopu disku a paměti. Doporučená velikost paměti RAM je minimálně 64 KB, ale přesné paměťové nároky závisí na protokol použitý, počet otevřených připojení, stejně jako cílové platformy.

SDK pro jazyk C je k dispozici v balíčku formuláře pomocí apt-get, NuGet a MBED. Chcete-li cílit na zařízení s omezením, je sestavení sady SDK pro cílovou platformu. Tato dokumentace ukazuje, jak odebrat některé funkce pro zmenšení nároky pomocí SDK pro jazyk C [cmake](https://cmake.org/). Kromě toho tato dokumentace popisuje osvědčený postup programovacích modelů pro práci s omezeným zařízení.

## <a name="building-the-c-sdk-for-constrained-devices"></a>Sestavení C SDK pro zařízení s omezením

Sestavení C sady SDK pro zařízení s omezením.

### <a name="prerequisites"></a>Požadavky

Použít tento [Příručka instalace SDK pro jazyk C](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md) Příprava vývojového prostředí pro sestavení sady SDK C. Než se ponoříte do kroku pro sestavení pomocí cmake, můžete vyvolat cmake příznaky odebrat nepoužívané funkce.

### <a name="remove-additional-protocol-libraries"></a>Odebrat další protokol knihovny

SDK pro jazyk C podporuje pět protokoly ještě dnes: protokol MQTT, MQTT přes protokol WebSocket, AMQPs, AMQP přes WebSocket a HTTPS. Většina scénářů vyžaduje jeden až dva protokoly spuštění v klientském počítači, proto můžete odebrat protokolu knihovny, které nepoužíváte ze sady SDK. Další informace o výběru příslušné komunikační protokol, pro váš scénář lze najít v [vyberte komunikační protokol IoT Hub](iot-hub-devguide-protocols.md). Například MQTT je jednoduchý protokol, který je často vhodnější pro zařízení s omezením.

Můžete odebrat knihovny AMQP a HTTP pomocí následujícího příkazu cmake:

```
cmake -Duse_amqp=OFF -Duse_http=OFF <Path_to_cmake>
```

### <a name="remove-sdk-logging-capability"></a>Odebrání možnosti protokolování sady SDK

SDK pro jazyk C poskytuje rozsáhlou protokolování v rámci pomoci s laděním. Můžete odebrat možnosti protokolování pro produkční zařízení pomocí následujícího příkazu cmake:

```
cmake -Dno_logging=OFF <Path_to_cmake>
```

### <a name="remove-upload-to-blob-capability"></a>Odebrat nahrávání do objektu blob funkce

Nahrávání velkých souborů do služby Azure Storage pomocí integrované funkce v sadě SDK. Azure IoT Hub funguje jako dispečer do přidruženého účtu Azure Storage. Tato funkce slouží k odesílání mediálních souborů, velké telemetrických dat dávek a protokoly. Další informace získáte [nahrávání souborů pomocí služby IoT Hub](iot-hub-devguide-file-upload.md). Pokud vaše aplikace nevyžaduje, aby tuto funkci, můžete odebrat tuto funkci pomocí následujícího příkazu cmake:

```
cmake -Ddont_use_uploadtoblob=ON <Path_to_cmake>
```

### <a name="running-strip-on-linux-environment"></a>Spuštěné pruhu v prostředí Linux

Pokud vaše binární soubory spustit v systému Linux, můžete využít [pruhu příkaz](https://en.wikipedia.org/wiki/Strip_(Unix)) ke zmenšení velikosti konečné aplikace po kompilaci.

```
strip -s <Path_to_executable>
```

## <a name="programming-models-for-constrained-devices"></a>Programovací modely pro omezené zařízení

Podívejte se v dalším kroku na programovací modely pro zařízení s omezením.

### <a name="avoid-using-the-serializer"></a>Vyhněte se použití serializátoru

SDK pro jazyk C obsahuje volitelný [serializátoru C SDK](https://github.com/Azure/azure-iot-sdk-c/tree/master/serializer), která umožňuje definovat metody a vlastnosti dvojčat zařízení pomocí deklarativní mapování tabulek. Serializátor je navržené pro zjednodušení vývoje, ale přidá režie, což není ideální pro zařízení s omezením. V takovém případě zvažte použití primitivní klientských rozhraní API a parsování formátu JSON pomocí jednoduchý analyzátor [parson](https://github.com/kgabis/parson).

### <a name="use-the-lower-layer-ll"></a>Použití nižší vrstvě (_LL_)

SDK pro jazyk C podporuje dvou programovacích modelů. Jedna sada obsahuje rozhraní API pomocí _LL_ infix, což je zkratka pro nižší úrovně. Tato sada API se světlejší váhu a není uveďte do provozu pracovní vlákna, což znamená, že uživatel musí ručně řídit plánování. Například pro klienta zařízení _LL_ rozhraní API najdete v tomto [hlavičkový soubor](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/inc/iothub_device_client_ll.h). 

Další sadu rozhraní API bez _LL_ index se nazývá pohodlí vrstva, ve kterém je pracovní podproces podařilo automaticky. Například pohodlí vrstvu rozhraní API pro klienty zařízení najdete v tomto [klienta zařízení IoT hlavičkový soubor](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/inc/iothub_device_client.h). Pro omezené zařízení, kde každé další vlákno může trvat značné procento systémové prostředky, zvažte použití _LL_ rozhraní API.

## <a name="next-steps"></a>Další postup

Další informace o architektuře Azure IoT C SDK:
-   [Azure IoT C SDK zdrojového kódu](https://github.com/Azure/azure-iot-sdk-c/)
-   [Pro zařízení Azure IoT SDK pro C Úvod](iot-hub-device-sdk-c-intro.md)
