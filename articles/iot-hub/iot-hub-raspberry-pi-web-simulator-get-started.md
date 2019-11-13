---
title: Připojení webového simulátoru malin. PI do Azure IoT Hub (Node. js)
description: Připojte webový simulátor malinu pi k Azure IoT Hub pro malinu PI pro posílání dat do cloudu Azure.
author: wesmc7777
manager: philmea
keywords: od malinu PI simulátoru, Azure IoT malin PI, malin PI IoT Hub, Malina Pi odeslat data do cloudu, malin.
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 04/11/2018
ms.author: wesmc
ms.openlocfilehash: efbe41be6c923f3547df86fd6faeb56bff5e0802
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2019
ms.locfileid: "73954532"
---
# <a name="connect-raspberry-pi-online-simulator-to-azure-iot-hub-nodejs"></a>Připojení simulátoru malinu PI online k Azure IoT Hub (Node. js)

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

V tomto kurzu začnete seznámení se základy práce s simulátorem procesu malin. PI v online studiu. Pak se dozvíte, jak bez problémů připojit simulátor PI ke cloudu pomocí služby [Azure IoT Hub](about-iot-hub.md).

Pokud máte fyzická zařízení, začněte tím, že přejdete na web [propojit malin PI s Azure IoT Hub](iot-hub-raspberry-pi-kit-node-get-started.md) .

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

## <a name="what-you-do"></a>Co dělat

* Naučte se základy simulátoru malin PI online.

* Vytvořte centrum IoT.

* Zaregistrujte zařízení pro PI ve službě IoT Hub.

* Spusťte ukázkovou aplikaci na pi k odeslání dat simulovaného senzoru do služby IoT Hub.

Spojte simulovanou instanci malin. až do služby IoT Hub, kterou vytvoříte. Pak spustíte ukázkovou aplikaci s simulátorem, která vygeneruje data snímače. Nakonec odešlete data ze senzorů do služby IoT Hub.

## <a name="what-you-learn"></a>Co se naučíte

* Jak vytvořit službu Azure IoT Hub a získat nový připojovací řetězec zařízení. Pokud účet Azure nemáte, [Vytvořte si bezplatný zkušební účet Azure](https://azure.microsoft.com/free/) během několika minut.

* Jak pracovat s simulátorem malin PI online

* Jak odesílat data ze senzorů do služby IoT Hub.

## <a name="overview-of-raspberry-pi-web-simulator"></a>Přehled webového simulátoru malinu PI

Kliknutím na toto tlačítko spustíte program malin PI online simulátor.

> [!div class="button"]
> <a href="https://azure-samples.github.io/raspberry-pi-web-simulator/#GetStarted" target="_blank">Spustit simulátor malinu PI</a>

Webové simulátory mají tři oblasti.

1. Oblast sestavení – výchozí okruh je, že se PI připojí ke senzoru BME280 a LED. Tato oblast je ve verzi Preview zamčená, takže aktuálně nemůžete provádět vlastní nastavení.

2. Oblast kódování – online editor kódu, který vám umožní kódovat kód s maliny PI. Výchozí ukázková aplikace pomáhá shromažďovat data ze senzorů BME280 a odesílá je do Azure IoT Hub. Aplikace je plně kompatibilní se skutečnými zařízeními PI. 

3. Integrované okno konzoly – zobrazuje výstup kódu. V horní části tohoto okna jsou k dispozici tři tlačítka.

   * **Spustit** – spusťte aplikaci v oblasti kódování.

   * **Resetovat** – obnoví výchozí ukázkovou aplikaci v oblasti kódování.

   * **Skládání/rozbalení** – na pravé straně je tlačítko pro skládání/rozbalení okna konzoly.

> [!NOTE]
> Webový simulátor malin. PI je teď dostupný ve verzi Preview. Rádi uslyšíme váš hlas v [Gitteru chatroom](https://gitter.im/Microsoft/raspberry-pi-web-simulator). Zdrojový kód je veřejný na [GitHubu](https://github.com/Azure-Samples/raspberry-pi-web-simulator).

![Přehled simulátoru PI online](media/iot-hub-raspberry-pi-web-simulator/0-overview.png)

## <a name="create-an-iot-hub"></a>Vytvoření centra IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registrace nového zařízení ve službě IoT Hub

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="run-a-sample-application-on-pi-web-simulator"></a>Spuštění ukázkové aplikace na webové simulátoru PI

1. V oblasti kódování se ujistěte, že pracujete s výchozí ukázkovou aplikací. Zástupný text na řádku 15 nahraďte připojovacím řetězcem zařízení služby Azure IoT Hub.
1. 
   ![Nahraďte připojovací řetězec zařízení.](media/iot-hub-raspberry-pi-web-simulator/1-connectionstring.png)

2. Spusťte aplikaci výběrem **Spustit** nebo zadáním `npm start`.

Měl by se zobrazit následující výstup, který zobrazuje data senzoru a zprávy, které se odesílají do služby IoT Hub ![dat výstupního snímače odeslaných z maliny PI do služby IoT Hub](media/iot-hub-raspberry-pi-web-simulator/2-run-application.png)

## <a name="read-the-messages-received-by-your-hub"></a>Přečtěte si zprávy přijaté vaším rozbočovačem.

Jedním ze způsobů, jak monitorovat zprávy přijaté službou IoT Hub ze simulovaného zařízení, je použití nástrojů Azure IoT pro Visual Studio Code. Další informace najdete v tématu [použití nástrojů Azure IoT pro Visual Studio Code k posílání a přijímání zpráv mezi zařízením a IoT Hub](iot-hub-vscode-iot-toolkit-cloud-device-messaging.md).

Další způsoby, jak zpracovávat data odesílaná vaším zařízením, můžete pokračovat k další části.

## <a name="next-steps"></a>Další kroky

Spustili jste ukázkovou aplikaci, která shromáždí data ze senzorů a pošle ji do služby IoT Hub.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
