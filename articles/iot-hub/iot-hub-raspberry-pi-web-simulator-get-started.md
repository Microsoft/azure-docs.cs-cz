---
title: Simulated malin pí do cloudu (Node.js) – simulátoru webové platformy malin připojit ke službě Azure IoT Hub | Microsoft Docs
description: Simulátor webové platformy malin připojte ke službě Azure IoT Hub malin pí k odesílání dat do cloudu Azure.
author: rangv
manager: ''
keywords: Simulátor Malinová pi, platformy azure iot Malinová, malinová platformy iot hub, malinová pí odesílání dat do cloudu, malinová pí do cloudu
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 04/11/2018
ms.author: rangv
ms.openlocfilehash: dc8216f6b0a610b556b94637970bfd3e721f38d2
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34636218"
---
# <a name="connect-raspberry-pi-online-simulator-to-azure-iot-hub-nodejs"></a>Připojení k Azure IoT Hub (Node.js) online simulátoru malin platformy

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

V tomto kurzu zahájíte učení základní informace o práci s online simulátoru malin pí. Pak zjistíte, jak se bezproblémově připojit simulátoru pí do cloudu pomocí [Azure IoT Hub](iot-hub-what-is-iot-hub.md). 

Pokud máte fyzické zařízení, navštivte [pí malin připojit ke službě Azure IoT Hub](iot-hub-raspberry-pi-kit-node-get-started.md) začít pracovat. 

<p>
<div id="diag" style="width:100%; text-align:center">
<a href="https://azure-samples.github.io/raspberry-pi-web-simulator/#getstarted" target="_blank">
<img src="media/iot-hub-raspberry-pi-web-simulator/3_banner.png" alt="Connect Raspberry Pi web simulator to Azure IoT Hub" width="400">
</div>
<p>
<div id="button" style="width:100%; text-align:center">
<a href="https://azure-samples.github.io/raspberry-pi-web-simulator/#Getstarted" target="_blank">
<img src="media/iot-hub-raspberry-pi-web-simulator/6_button_default.png" alt="Start Raspberry Pi simulator" width="400" onmouseover="this.src='media/iot-hub-raspberry-pi-web-simulator/5_button_click.png';" onmouseout="this.src='media/iot-hub-raspberry-pi-web-simulator/6_button_default.png';">
</div>

## <a name="what-you-do"></a>Co dělat

* Přečtěte si základní informace o online simulátoru malin pí.
* Vytvoření služby IoT hub.
* Registrovat zařízení pro platformy ve službě IoT hub.
* Spuštění ukázkové aplikace na platformy k odesílání dat simulované senzor do služby IoT hub.

Simulované pí malin připojení do služby IoT hub, který vytvoříte. Pak spusťte ukázkovou aplikaci s simulátoru ke generování dat snímačů. Nakonec odeslat data snímačů do služby IoT hub.

## <a name="what-you-learn"></a>Co se naučíte

* Postup vytvoření služby Azure IoT hub a získat nový připojovací řetězec zařízení. Pokud nemáte účet Azure [vytvořit Bezplatný zkušební účet Azure](https://azure.microsoft.com/free/) za několik minut.
* Jak pracovat s online simulátoru malin pí.
* Jak odesílat data snímačů do služby IoT hub.

## <a name="overview-of-raspberry-pi-web-simulator"></a>Přehled simulátoru webové platformy malin

Klikněte na tlačítko spustit online simulátoru malin pí.

> [!div class="button"]
<a href="https://azure-samples.github.io/raspberry-pi-web-simulator/#GetStarted" target="_blank">Spustit simulátoru Malinová platformy</a>

Existují tři oblasti v simulátoru web.
1. Sestavení oblast – výchozí okruh je, že na Pi připojí senzor BME280 a Indikátor. Oblasti uzamčeno ve verzi preview proto nyní nemůže provádět přizpůsobení.
2. Kódování oblast – editor online kód pro vás kódu s použitím malin pí. Ukázkovou aplikaci výchozí pomáhá ke shromažďování dat snímačů z BME280 senzor a odesílá do služby Azure IoT Hub. Aplikace je plně kompatibilní se skutečné platformy zařízení. 
3. Okno integrovaného konzoly - zobrazuje výstup z vašeho kódu. V horní části tohoto okna existují tři tlačítka.
   * **Spustit** -aplikaci spustit v oblasti kódování.
   * **Resetování** -oblasti kódování resetovat na výchozí ukázkovou aplikaci.
   * **Násobek/rozšířit** -na pravé straně je tlačítko pro vás násobek nebo rozbalte v okně konzoly.

> [!NOTE] 
Simulátor webové platformy malin je teď dostupná ve verzi preview. Rádi bychom se slyšet váš hlas v [Gitter Chatroom](https://gitter.im/Microsoft/raspberry-pi-web-simulator). Zdrojový kód je veřejný na [Githubu](https://github.com/Azure-Samples/raspberry-pi-web-simulator).

![Přehled platformy online simulátoru](media/iot-hub-raspberry-pi-web-simulator/0_overview.png)

[!INCLUDE [iot-hub-get-started-create-hub-and-device](../../includes/iot-hub-get-started-create-hub-and-device.md)]


## <a name="run-a-sample-application-on-pi-web-simulator"></a>Spouštět ukázkovou aplikaci v simulátoru webové platformy

1. V oblasti kódování, zkontrolujte, zda že pracujete na výchozí ukázkovou aplikaci. Nahraďte zástupný symbol v řádku 15 připojovací řetězec pro zařízení Azure IoT hub.
   ![Nahraďte řetězec připojení zařízení](media/iot-hub-raspberry-pi-web-simulator/1_connectionstring.png)

2. Klikněte na tlačítko **spustit** nebo typ `npm start` ke spuštění aplikace.


Byste měli vidět následující výstup, který popisuje data snímačů a zprávy, které se odesílají do služby IoT hub ![výstup - data snímačů odeslaný malin pí do služby IoT hub](media/iot-hub-raspberry-pi-web-simulator/2_run_application.png)


## <a name="next-steps"></a>Další postup

Spustíte ukázkovou aplikaci pro shromažďování dat snímačů a odeslat do služby IoT hub.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
