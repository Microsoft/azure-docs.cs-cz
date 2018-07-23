---
title: Simulované Raspberry Pi do cloudu (Node.js) – webový simulátor Raspberry Pi připojení ke službě Azure IoT Hub | Dokumentace Microsoftu
description: Webový simulátor Raspberry Pi připojte ke službě Azure IoT Hub pro Raspberry Pi k odesílání dat do cloudu Azure.
author: rangv
manager: ''
keywords: Simulátor Raspberry pi, azure iot raspberry pi, raspberry pi iot hub, raspberry pi odesílání dat do cloudu, raspberry pi s cloudem
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 04/11/2018
ms.author: rangv
ms.openlocfilehash: 2dd9b14ebd7e64a1073ab773b2f1ac8d8c05ac0a
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/20/2018
ms.locfileid: "39185243"
---
# <a name="connect-raspberry-pi-online-simulator-to-azure-iot-hub-nodejs"></a>Připojte se ke službě Azure IoT Hub (Node.js) online simulátor Raspberry Pi

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

V tomto kurzu začnete pomocí seznámení se základy práce se simulátor Raspberry Pi online. Pak zjistěte, jak hladce simulátor Pi do cloudu s využitím [Azure IoT Hub](about-iot-hub.md). 

Pokud máte fyzických zařízení, navštivte [připojte Raspberry Pi pro službu Azure IoT Hub](iot-hub-raspberry-pi-kit-node-get-started.md) začít. 

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

## <a name="what-you-do"></a>Co můžete dělat

* Naučte se základy simulátor Raspberry Pi online.
* Vytvoření služby IoT hub.
* Registrace zařízení ve službě IoT hub pro číslo pí.
* Spusťte ukázkovou aplikaci v Pi odeslat data ze simulovaných senzorů do služby IoT hub.

Připojení simulovaného Raspberry Pi do služby IoT hub, kterou vytvoříte. Potom spustíte ukázkovou aplikaci se simulátorem ke generování data ze senzorů. A konečně odeslat data ze senzorů do služby IoT hub.

## <a name="what-you-learn"></a>Co se naučíte

* Postup vytvoření služby Azure IoT hub a získat nový připojovací řetězec zařízení. Pokud nemáte účet Azure [vytvořit Bezplatný zkušební účet Azure](https://azure.microsoft.com/free/) za několik minut.
* Jak pracovat s online simulátor Raspberry Pi.
* Jak odeslat data ze senzorů do služby IoT hub.

## <a name="overview-of-raspberry-pi-web-simulator"></a>Přehled webový simulátor Raspberry Pi

Klikněte na tlačítko Spustit simulátor Raspberry Pi online.

> [!div class="button"]
<a href="https://azure-samples.github.io/raspberry-pi-web-simulator/#GetStarted" target="_blank">Spusťte simulátor Raspberry Pi</a>

Existují tři oblasti v webový simulátor.
1. Oblast sestavení – výchozí okruh je, že přístup k na Pi BME280 snímačů a kontrolku LED. V oblasti uzamčeno ve verzi preview to aktuálně neumožňuje přizpůsobení.
2. Kódování oblast – editor online kódu vám umožňuje psát kód s Raspberry Pi. Ukázková aplikace výchozí umožňuje shromažďovat data ze senzorů z BME280 snímačů a odesílá do služby Azure IoT Hub. Aplikace je plně kompatibilní s skutečných platformy zařízení. 
3. Integrovaná konzola okno - zobrazí výstup kódu. V horní části tohoto okna jsou tři tlačítka.
   * **Spustit** – spusťte aplikaci v oblasti kódování.
   * **Resetovat** -obnovíte výchozí ukázkovou aplikaci v oblasti kódování.
   * **Sbalení a rozbalení** – na pravé straně je tlačítko pro rozbalení/sbalení v okně konzoly.

> [!NOTE] 
Webový simulátor Raspberry Pi je teď dostupná ve verzi preview. Jsme rádi bychom znali váš hlas v [Gitteru Chatroom](https://gitter.im/Microsoft/raspberry-pi-web-simulator). Zdrojový kód je ve veřejné [Githubu](https://github.com/Azure-Samples/raspberry-pi-web-simulator).

![Přehled platformy online simulátoru](media/iot-hub-raspberry-pi-web-simulator/0_overview.png)

[!INCLUDE [iot-hub-get-started-create-hub-and-device](../../includes/iot-hub-get-started-create-hub-and-device.md)]


## <a name="run-a-sample-application-on-pi-web-simulator"></a>Spustit ukázkovou aplikaci v simulátoru web Pi

1. V oblasti kódování, ujistěte se, že pracujete na výchozí ukázkovou aplikaci. Nahraďte zástupný text v řádku 15 připojovací řetězec pro zařízení Azure IoT hub.
   ![Nahraďte připojovací řetězec zařízení](media/iot-hub-raspberry-pi-web-simulator/1_connectionstring.png)

2. Klikněte na tlačítko **spustit** nebo typ `npm start` ke spuštění aplikace.


Zobrazí se následující výstup, který zobrazuje data ze senzorů a zprávy, které se odesílají do služby IoT hub ![výstup – data ze senzorů odeslané do služby IoT hub z Raspberry Pi](media/iot-hub-raspberry-pi-web-simulator/2_run_application.png)


## <a name="next-steps"></a>Další postup

Jste spustili ukázkovou aplikaci shromažďovat data ze senzorů a odesílat je do služby IoT hub.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
