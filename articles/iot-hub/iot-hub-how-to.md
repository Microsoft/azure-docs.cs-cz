---
title: Azure IoT Hub postup | Microsoft Docs
description: Jak se jako vývojář použít k různým funkcím služby IoT Hub?
services: iot-hub
documentationcenter: ''
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: 24376318-5344-4a81-a1e6-0003ed587d53
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/13/2017
ms.author: dobett
ms.openlocfilehash: 9b112d2d7fc1756b74e98335831175f5d4c13320
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2018
---
# <a name="how-to-use-azure-iot-hub"></a>Jak používat Azure IoT Hub

Máte různé možnosti, jak se naučit vyvíjet pro službu IoT Hub:

* Přečtěte si konceptuální články, které funkce IoT Hub podrobně popsány.
* Proveďte jeden z kurzů, které se týkají k různým funkcím služby IoT Hub.

## <a name="developer-guide"></a>Příručka pro vývojáře

Jako vývojář, můžete si přečíst podrobný koncepční informace o IoT Hub v [Příručka vývojáře][lnk-devguide]. Tato příručka obsahuje:

* Podrobný popis všech funkcí služby IoT Hub můžete informace se dozvíte, jak je používat.
* Pokyny o tom, jak zvolit, pokud jsou k dispozici více možností.

## <a name="tutorials"></a>Kurzy

Pokud dáváte přednost Další informace týkající se konkrétních funkcí služby IoT Hub projdete praktických příkladech, existuje několik kurzy lze vybírat. Řadu tyto kurzy jsou k dispozici v více programovacích jazyků. Tyto kurzy patří:

- [Zpracování zpráv typu zařízení cloud IoT Hub pomocí trasy][lnk-routes-tutorial]. V tomto kurzu se dozvíte, jak používat služby IoT Hub směrování pravidla k odesílání zpráv typu zařízení cloud způsobem snadno, založené na konfiguraci.

- [Odesílání zpráv typu cloud zařízení s centrem IoT][lnk-c2d-tutorial]. V tomto kurzu se dozvíte, jak k odesílání zpráv typu cloud zařízení prostřednictvím služby IoT Hub a příjem zpráv typu cloud zařízení na zařízení.

- [Odeslání souborů ze zařízení do cloudu pomocí služby IoT Hub][lnk-upload-tutorial]. V tomto kurzu se dozvíte, jak používat funkce nahrávání souboru služby IoT Hub.

- [Začínáme s dvojčata zařízení][lnk-twin-tutorial]. Tento kurz vás seznámí s dvojčata zařízení, hlášené vlastnosti, požadované vlastnosti a značky. Pomocí dvojčata zařízení synchronizovat hodnoty ve vašich zařízeních.

- [Používat přímé metody][lnk-methods-tutorial]. V tomto kurzu se dozvíte, jak používat přímé metody. Přidejte obslužnou rutinu pro přímé metoda simulovaného zařízení a vyvolání metody přímé ze služby IoT Hub.

- [Začínáme se správou zařízení][lnk-dm-tutorial]. V tomto kurzu se dozvíte, jak používat funkce správy klíčů zařízení jako dvojčata a přímé metody. Pomocí těchto funkcí vzdáleně restartování simulovaného zařízení.

- [Použijte požadované vlastnosti pro konfiguraci zařízení][lnk-properties-tutorial]. Tento kurz ukazuje, že jste způsobu pro použití zařízení pro dvojici potřeby a hlášené vlastnosti, na vzdáleně konfigurace zařízení.

- [Správa zařízení použít k aktualizaci firmwaru zařízení][lnk-jobs-tutorial]. V tomto kurzu se dozvíte, jak používat funkce správy klíčů zařízení jako dvojčata a přímé metody. Zjistíte, jak používat tyto funkce vzdálené aktualizace firmwaru zařízení.

- [Plán a všesměrovým úlohy][lnk-schedule-tutorial]. V tomto kurzu se dozvíte, jak používat požadované vlastnosti a metody pro přímý pro interakci s více zařízení v naplánovaném čase.

## <a name="next-steps"></a>Další postup

Další informace o službě IoT Hub, najdete v článku [Příručka vývojáře][lnk-devguide].

[lnk-devguide]: ./iot-hub-devguide.md
[lnk-routes-tutorial]: ./iot-hub-csharp-csharp-process-d2c.md
[lnk-c2d-tutorial]: ./iot-hub-csharp-csharp-c2d.md
[lnk-upload-tutorial]: ./iot-hub-csharp-csharp-file-upload.md
[lnk-twin-tutorial]: ./iot-hub-node-node-twin-getstarted.md
[lnk-methods-tutorial]: ./iot-hub-node-node-direct-methods.md
[lnk-dm-tutorial]: ./iot-hub-node-node-device-management-get-started.md
[lnk-properties-tutorial]: ./iot-hub-node-node-twin-how-to-configure.md
[lnk-jobs-tutorial]: ./iot-hub-node-node-firmware-update.md
[lnk-schedule-tutorial]: ./iot-hub-node-node-schedule-jobs.md