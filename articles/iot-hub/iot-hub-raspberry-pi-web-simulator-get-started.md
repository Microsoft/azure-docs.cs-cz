---
title: Připojení webového simulátoru malinu PI do Azure IoT Hub (Node.js)
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
ms.custom:
- 'Role: Cloud Development'
- devx-track-js
ms.openlocfilehash: 702dee108577665eded6dd1a92203236d74e866e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "91308326"
---
# <a name="connect-raspberry-pi-online-simulator-to-azure-iot-hub-nodejs"></a>Propojení online simulátoru pro malinu PI do Azure IoT Hub (Node.js)

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

V tomto kurzu začnete seznámení se základy práce s simulátorem procesu malin. PI v online studiu. Pak se dozvíte, jak bez problémů připojit simulátor PI ke cloudu pomocí služby [Azure IoT Hub](about-iot-hub.md).

<p>
<div id="diag" style="width:100%; text-align:center">
<img src="media/iot-hub-raspberry-pi-web-simulator/3-banner.png" alt="Connect Raspberry Pi web simulator to Azure IoT Hub" width="400">
</div>
</p>
<p>
<div id="button" style="width:100%; text-align:center">
<a href="https://azure-samples.github.io/raspberry-pi-web-simulator/#getstarted" target="_blank">
<img src="media/iot-hub-raspberry-pi-web-simulator/6-button-default.png" alt="Start Raspberry Pi simulator" width="400" onmouseover="this.src='media/iot-hub-raspberry-pi-web-simulator/5-button-click.png';" onmouseout="this.src='media/iot-hub-raspberry-pi-web-simulator/6-button-default.png';">
</a>
</div>
</p>

Pokud máte fyzická zařízení, začněte tím, že přejdete na web [propojit malin PI s Azure IoT Hub](iot-hub-raspberry-pi-kit-node-get-started.md) .

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

1. Oblast sestavení: Výchozí okruh je takový, že zařízení Raspberry Pi je připojené k senzoru BME280 a diodě LED. Ve verzi Preview je tato oblast zamknutá, takže aktuálně neumožňuje přizpůsobení.

2. Oblast kódování: Online editor kódu vám umožňuje psát kód pro Raspberry Pi. Výchozí ukázková aplikace vám pomůže shromažďovat data ze senzoru BME280 a odesílat je do vaší instance služby Azure IoT Hub. Aplikace je plně kompatibilní se skutečnými zařízeními Raspberry Pi. 

3. Integrované okno konzoly: Zobrazuje výstup vašeho kódu. V horní části tohoto okna jsou tři tlačítka.

   * **Run** (Spustit): Spustí aplikaci zobrazenou v oblasti kódování.

   * **Reset** (Obnovit): Obnoví v oblasti kódování výchozí ukázkovou aplikaci.

   * **Tlačítko pro sbalení a rozbalení**: Na pravé straně je tlačítko pro sbalení nebo rozbalení okna konzoly.

> [!NOTE]
> Webový simulátor malin. PI je teď dostupný ve verzi Preview. Rádi uslyšíme váš hlas v [Gitteru chatroom](https://gitter.im/Microsoft/raspberry-pi-web-simulator). Zdrojový kód je veřejný na [GitHubu](https://github.com/Azure-Samples/raspberry-pi-web-simulator).

![Přehled simulátoru PI online](media/iot-hub-raspberry-pi-web-simulator/0-overview.png)

## <a name="create-an-iot-hub"></a>Vytvoření centra IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registrace nového zařízení ve službě IoT Hub

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="run-a-sample-application-on-pi-web-simulator"></a>Spuštění ukázkové aplikace na webovém simulátoru Pi

1. Ověřte, že v oblasti pro zápis kódu pracujete s výchozí ukázkovou aplikací. Zástupný text na řádku 15 nahraďte připojovacím řetězcem zařízení v centru Azure IoT Hub.
1. 
   ![Nahrazení připojovacího řetězec zařízení](media/iot-hub-raspberry-pi-web-simulator/1-connectionstring.png)

2. Vyberte **Spustit** nebo zadejte `npm start` a spusťte aplikaci.

Měl by se zobrazit následující výstup, který zobrazuje data ze senzorů a zprávy, které se odesílají do vašich ![ dat ze senzorů výstupu služby IoT Hub odeslaných z malin. PI do služby IoT Hub.](media/iot-hub-raspberry-pi-web-simulator/2-run-application.png)

## <a name="read-the-messages-received-by-your-hub"></a>Přečtěte si zprávy přijaté vaším rozbočovačem.

Jedním ze způsobů, jak monitorovat zprávy přijaté službou IoT Hub ze simulovaného zařízení, je použití nástrojů Azure IoT pro Visual Studio Code. Další informace najdete v tématu [použití nástrojů Azure IoT pro Visual Studio Code k posílání a přijímání zpráv mezi zařízením a IoT Hub](iot-hub-vscode-iot-toolkit-cloud-device-messaging.md).

Další způsoby, jak zpracovávat data odesílaná vaším zařízením, můžete pokračovat k další části.

## <a name="next-steps"></a>Další kroky

Spustili jste ukázkovou aplikaci, která shromáždí data ze senzorů a pošle ji do služby IoT Hub.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
