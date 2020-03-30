---
title: Připojení webového simulátoru Raspberry Pi k Azure IoT Hub (Node.js)
description: Připojte webový simulátor Raspberry Pi do služby Azure IoT Hub pro Raspberry Pi a odesílejte data do cloudu Azure.
author: wesmc7777
manager: philmea
keywords: raspberry pi simulátor, azurový iot malina pi, raspberry pi iot hub, raspberry pi posílat data do cloudu, raspberry pi do cloudu
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 04/11/2018
ms.author: wesmc
ms.openlocfilehash: efbe41be6c923f3547df86fd6faeb56bff5e0802
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73954532"
---
# <a name="connect-raspberry-pi-online-simulator-to-azure-iot-hub-nodejs"></a>Připojení online simulátoru Raspberry Pi k Azure IoT Hub (Node.js)

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

V tomto tutoriálu začnete tím, že se naučíte základy práce s online simulátorem Raspberry Pi. Pak se dozvíte, jak bez problémů připojit pi simulátor u cloudu pomocí [Azure IoT Hub](about-iot-hub.md).

Pokud máte fyzická zařízení, navštivte [Connect Raspberry Pi do Azure IoT Hub](iot-hub-raspberry-pi-kit-node-get-started.md) a můžete začít.

<p>
<div id="diag" style="width:100%; text-align:center">
<a href="https://azure-samples.github.io/raspberry-pi-web-simulator/#getstarted" target="_blank">
<img src="media/iot-hub-raspberry-pi-web-simulator/3-banner.png" alt="Connect Raspberry Pi web simulator to Azure IoT Hub" width="400">
</div>
<p>
<div id="button" style="width:100%; text-align:center">
<a href="https://azure-samples.github.io/raspberry-pi-web-simulator/#Getstarted" target="_blank">
<img src="media/iot-hub-raspberry-pi-web-simulator/6-button-default.png" alt="Start Raspberry Pi simulator" width="400" onmouseover="this.src='media/iot-hub-raspberry-pi-web-simulator/5-button-click.png';" onmouseout="this.src='media/iot-hub-raspberry-pi-web-simulator/6-button-default.png';">
</div>

## <a name="what-you-do"></a>Co děláte

* Naučte se základy online simulátoru Raspberry Pi.

* Vytvořte centrum IoT.

* Zaregistrujte zařízení pro Pi ve svém centru IoT.

* Spusťte ukázkovou aplikaci na Pi a odešlete simulovaná data senzorů do centra IoT hub.

Připojte simulované Raspberry Pi k centru IoT hubu, který vytvoříte. Potom spustíte ukázkovou aplikaci se simulátorem pro generování dat senzoru. Nakonec odešlete data senzoru do centra IoT hub.

## <a name="what-you-learn"></a>Co se naučíte

* Jak vytvořit azure iot hub a získat nový řetězec připojení zařízení. Pokud nemáte účet Azure, [vytvořte si bezplatný zkušební účet Azure](https://azure.microsoft.com/free/) během několika minut.

* Jak pracovat s online simulátorem Raspberry Pi.

* Jak odeslat data ze senzorů do centra IoT.

## <a name="overview-of-raspberry-pi-web-simulator"></a>Přehled webového simulátoru Raspberry Pi

Klikněte na tlačítko pro spuštění Online simulátoru Raspberry Pi.

> [!div class="button"]
> <a href="https://azure-samples.github.io/raspberry-pi-web-simulator/#GetStarted" target="_blank">Spustit Raspberry Pi Simulátor</a>

Ve webovém simulátoru jsou tři oblasti.

1. Oblast sestavení: Výchozí okruh je takový, že zařízení Raspberry Pi je připojené k senzoru BME280 a diodě LED. Ve verzi Preview je tato oblast zamknutá, takže aktuálně neumožňuje přizpůsobení.

2. Oblast kódování: Online editor kódu vám umožňuje psát kód pro Raspberry Pi. Výchozí ukázková aplikace vám pomůže shromažďovat data ze senzoru BME280 a odesílat je do vaší instance služby Azure IoT Hub. Aplikace je plně kompatibilní se skutečnými zařízeními Raspberry Pi. 

3. Integrované okno konzoly: Zobrazuje výstup vašeho kódu. V horní části tohoto okna jsou tři tlačítka.

   * **Run** (Spustit): Spustí aplikaci zobrazenou v oblasti kódování.

   * **Reset** (Obnovit): Obnoví v oblasti kódování výchozí ukázkovou aplikaci.

   * **Tlačítko pro sbalení a rozbalení**: Na pravé straně je tlačítko pro sbalení nebo rozbalení okna konzoly.

> [!NOTE]
> Webový simulátor Raspberry Pi je nyní k dispozici ve verzi preview. Rádi bychom slyšeli váš hlas v [Gitter Chatroom](https://gitter.im/Microsoft/raspberry-pi-web-simulator). Zdrojový kód je veřejný na [GitHubu](https://github.com/Azure-Samples/raspberry-pi-web-simulator).

![Přehled Pi online simulátoru](media/iot-hub-raspberry-pi-web-simulator/0-overview.png)

## <a name="create-an-iot-hub"></a>Vytvoření centra IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registrace nového zařízení v centru IoT hub

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="run-a-sample-application-on-pi-web-simulator"></a>Spuštění ukázkové aplikace na webovém simulátoru Pi

1. Ověřte, že v oblasti pro zápis kódu pracujete s výchozí ukázkovou aplikací. Zástupný text na řádku 15 nahraďte připojovacím řetězcem zařízení v centru Azure IoT Hub.
1. 
   ![Nahrazení připojovacího řetězec zařízení](media/iot-hub-raspberry-pi-web-simulator/1-connectionstring.png)

2. **Chcete-li** `npm start` aplikaci spustit nebo spustit, vyberte spustit nebo zadat.

Měli byste vidět následující výstup, který zobrazuje data ze senzorů a ![zprávy, které jsou odesílány do výstupu centra IoT Hub - data senzorů odeslaná z Raspberry Pi do centra IoT](media/iot-hub-raspberry-pi-web-simulator/2-run-application.png)

## <a name="read-the-messages-received-by-your-hub"></a>Čtení zpráv přijatých vaším centrem

Jedním ze způsobů, jak sledovat zprávy přijaté vaším centrem IoT hub ze simulovaného zařízení, je použití nástrojů Azure IoT tools pro kód Visual Studia. Další informace najdete [v tématu Použití nástrojů Azure IoT pro kód Visual Studia k odesílání a přijímání zpráv mezi zařízením a službou IoT Hub](iot-hub-vscode-iot-toolkit-cloud-device-messaging.md).

Další způsoby zpracování dat odeslaných zařízením najdete v další části.

## <a name="next-steps"></a>Další kroky

Spouštěli jste ukázkovou aplikaci pro shromažďování dat senzorů a jejich odeslání do služby IoT hub.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
