---
title: Správa zařízení Azure IoT Hub cloudu zasílání zpráv s platformou iothub-explorer | Microsoft Docs
description: Naučte se používat nástroj iothub-explorer rozhraní příkazového řádku k monitorování zařízení cloudu zprávy (D2C) a odesílat cloudu na zařízení (C2D) zpráv ve službě Azure IoT Hub.
services: iot-hub
documentationcenter: ''
author: rangv
manager: timlt
tags: ''
keywords: iothub Průzkumníku cloudu zařízení zasílání zpráv a iot hub cloudu do zařízení, cloudu pro zasílání zpráv zařízení
ms.assetid: 04521658-35d3-4503-ae48-51d6ad3c62cc
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 4/11/2018
ms.author: rangv
ms.openlocfilehash: ed15f7749676d8a7f0c2ef8b23e238f7bc90e341
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="use-iothub-explorer-to-send-and-receive-messages-between-your-device-and-iot-hub"></a>Použití iothub Průzkumníka k odesílání a přijímání zpráv mezi zařízením a IoT Hub

![Diagram začátku do konce](media/iot-hub-get-started-e2e-diagram/2.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

[iothub-explorer](https://github.com/azure/iothub-explorer) má několik příkazů, který usnadňuje správu IoT Hub. Tento kurz se zaměřuje na použití iothub Průzkumníka odesílat a přijímat zprávy mezi zařízením a službou IoT hub.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="what-you-will-learn"></a>Co se dozvíte

Zjistíte, jak používat iothub-explorer ke sledování zpráv typu zařízení cloud a pro odesílání zpráv typu cloud zařízení. Zprávy typu zařízení cloud může být data snímačů, který shromažďuje vaše zařízení a pak odešle do služby IoT hub. Zprávy typu cloud zařízení může být příkazy, které služby IoT hub odešle do zařízení blikání DIODU, která je připojena k zařízení.

## <a name="what-you-will-do"></a>Co provedete

- Pomocí iothub Průzkumníka sledování zpráv typu zařízení cloud.
- Iothub-explorer použijte k odesílání zpráv typu cloud zařízení.

## <a name="what-you-need"></a>Co potřebujete

- Kurz [nastavit vaše zařízení](iot-hub-raspberry-pi-kit-node-get-started.md) dokončit, která zahrnuje následující požadavky:
  - Aktivní předplatné Azure.
  - V rámci svého předplatného služby Azure IoT hub.
  - Klientská aplikace, která odesílá zprávy do služby Azure IoT hub.
- iothub-explorer. ([Nainstalovat iothub-explorer](https://github.com/azure/iothub-explorer))

## <a name="monitor-device-to-cloud-messages"></a>Sledování zpráv typu zařízení cloud

Pokud chcete monitorovat zpráv odesílaných ze zařízení do služby IoT hub, postupujte takto:

1. Otevřete okno konzoly.
1. Spusťte následující příkaz:

   ```bash
   iothub-explorer monitor-events <device-id> --login "<IoTHubConnectionString>"
   ```

   > [!Note]
   > Získat `<device-id>` a `<IoTHubConnectionString>` ze služby IoT hub. Ujistěte se, že jste dokončili kurz předchozí. Nebo můžete zkusit použít `iothub-explorer monitor-events <device-id> --login "HostName=<my-hub>.azure-devices.net;SharedAccessKeyName=<my-policy>;SharedAccessKey=<my-policy-key>"` Pokud máte `HostName`, `SharedAccessKeyName` a `SharedAccessKey`.

## <a name="send-cloud-to-device-messages"></a>Odesílání zpráv z cloudu do zařízení

Odeslat zprávu na vaše zařízení ze služby IoT hub, postupujte takto:

1. Otevřete okno konzoly.
1. Spuštění relace ve službě IoT hub spuštěním následujícího příkazu:

   ```bash
   iothub-explorer login `<IoTHubConnectionString>`
   ```

1. Odeslat zprávu na vaše zařízení tak, že spustíte následující příkaz:

   ```bash
   iothub-explorer send <device-id> <message>
   ```

Příkaz bliká DIODU, která je připojena k zařízení a odešle zprávu do zařízení.

> [!Note]
> Není nutné zařízení k odeslání příkazu samostatné ack zpět do služby IoT hub po přijetí zprávy.

## <a name="next-steps"></a>Další postup

Když jste se naučili sledování zpráv typu zařízení cloud a odesílání zpráv typu cloud zařízení mezi zařízení IoT a Azure IoT Hub.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
