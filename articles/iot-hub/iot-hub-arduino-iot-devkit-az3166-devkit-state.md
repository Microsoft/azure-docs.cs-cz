---
title: Použití dvojčat zařízení Azure k ovládání MXChip IoT DevKit uživatele LED | Dokumentace Microsoftu
description: V tomto kurzu se naučíte se stavy DevKit monitorovat a kontrolovat uživatele LED s dvojčaty zařízení Azure IoT Hub.
author: liydu
manager: jeffya
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 04/04/2018
ms.author: liydu
ms.openlocfilehash: e955d21132dda6caa137ad3b5de9d00ccf7ed1b4
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55811048"
---
# <a name="mxchip-iot-devkit"></a>Sada MXChip IoT DevKit

V tomto příkladu můžete použít ke sledování stavů informace a senzor MXChip IoT DevKit Wi-Fi a řídit barev uživatelské LED s využitím dvojčat zařízení Azure IoT Hub.

## <a name="what-you-learn"></a>Co se naučíte

- Jak monitorovat stavy MXChip IoT DevKit senzoru.

- Jak barvy DevKit RGB LED s pomocí dvojčata zařízení v Azure.

## <a name="what-you-need"></a>Co potřebujete

- Nastavení vývojového prostředí pomocí následujících [– Příručka Začínáme](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started).

- V okně terminálu GitBash (nebo jiné rozhraní příkazového řádku Gitu) zadejte následující příkazy:

   ```bash
   git clone https://github.com/DevKitExamples/DevKitState.git
   cd DevKitState
   code .
   ```

## <a name="provision-azure-services"></a>Zřízení služby Azure

1. Klikněte na tlačítko **úlohy** rozevírací nabídky v aplikaci Visual Studio Code a vyberte **spustit úlohu...**   -  **zřizování cloudu**.

2. Pokroku ve studiu se zobrazí v části **TERMINÁLU** karty **úvodní** panelu.

3. Po zobrazení výzvy se zprávou *jaké předplatné chcete zvolit*, vyberte předplatné.

4. Vyberte nebo zvolte skupinu prostředků. 
 
   > [!NOTE]
   > Pokud již máte free služby IoT Hub, můžete tento krok přeskočit.

5. Po zobrazení výzvy se zprávou *jaké služby IoT hub chcete zvolit*vyberte nebo vytvořte Centrum IoT.

6. Podobně jako *aplikace function app: názvem aplikace function app: xxx*, se zobrazí. Poznamenejte si název aplikace funkcí; použije se v pozdějším kroku.

7. Čekat pro nasazení šablony Azure Resource Manageru k dokončení, což je uvedeno, kdy se zpráva *nasazení šablony Resource Manageru: Provádí* se zobrazí.

## <a name="deploy-function-app"></a>Nasadit aplikaci Function App

1. Klikněte na tlačítko **úlohy** rozevírací nabídky v aplikaci Visual Studio Code a vyberte **spustit úlohu...**   -  **nasazení cloudu**.

2. Počkejte na dokončení; procesu nahrávání kódu aplikace – funkce zpráva *nasadí aplikaci function app: Provádí* se zobrazí.

## <a name="configure-iot-hub-device-connection-string-in-devkit"></a>Nakonfigurovat připojovací řetězec zařízení služby IoT Hub v DevKit

1. Připojení MXChip IoT DevKit k vašemu počítači.

2. Klikněte na tlačítko **úlohy** rozevírací nabídky v aplikaci Visual Studio Code a vyberte **spustit úlohu...**   -  **config připojení zařízení**

3. MXChip IoT DevKit, stiskněte a podržte tlačítko **A**, stiskněte **resetování** tlačítka a potom verzi **A** aby DekKit zadejte režim konfigurace.

4. Počkejte na Dokončit připojovací řetězec konfigurace procesu.

## <a name="upload-arduino-code-to-devkit"></a>Nahrání kódu Arduino DevKit

S MXChip IoT DevKit připojíte k počítači:

1. Klikněte na tlačítko **úlohy** rozevírací nabídky v aplikaci Visual Studio Code a vyberte **spustit úlohu sestavení...** Arduino nákresu je zkompilován a nahráli DevKit.

2. Když náčrt úspěšně odeslal *sestavení a nahrání náčrt: Úspěch* se zobrazí zpráva.

## <a name="monitor-devkit-state-in-browser"></a>Stav DevKit sledování v prohlížeči

1. Ve webovém prohlížeči otevřete `DevKitState\web\index.html` souboru –, který byl vytvořen při co budete potřebovat kroku.

2. Zobrazí se na následující stránce webu:![Zadejte název aplikace funkcí.](media/iot-hub-arduino-iot-devkit-az3166-devkit-state/devkit-state-function-app-name.png)

3. Zadejte název aplikace funkcí, které jste si poznamenali dříve.

4. Klikněte na tlačítko **připojit** tlačítko

5. Během několika sekund stránka se obnoví a zobrazí stav připojení Wi-Fi DevKit a stav každého připojení senzory.

## <a name="control-the-devkits-user-led"></a>Ovládací prvek DevKit uživatele LED

1. Klikněte na symbol LED uživatel na obrázku webové stránky.

2. Během několika sekund obrazovky aktualizuje a zobrazí aktuální stav barev uživatelské Indikátor.

3. Zkuste změnit hodnotu barvy RGB LED kliknutím na různých místech v ovládacích prvcích posuvníku RGB.

## <a name="example-operation"></a>Příklad operace

![Příklad postupu testu](media/iot-hub-arduino-iot-devkit-az3166-devkit-state/devkit-state.gif)

> [!NOTE]
> Zobrazí se nezpracovaná data objektu dvojče zařízení na webu Azure portal: IoT Hub -\> zařízení IoT –\> *\<zařízení\>*  - \> dvojče zařízení.

## <a name="next-steps"></a>Další postup

Jste se naučili, jak:
- Připojení MXChip IoT DevKit zařízení k akcelerátor řešení vzdáleného monitorování Azure IoT.
- Smysl a barvy DevKit RGB LED s pomocí funkce dvojčat zařízení Azure IoT.

Tady jsou další navrhované kroky:

* [Azure IoT vzdálené monitorování přehled akcelerátorů řešení](https://docs.microsoft.com/azure/iot-suite/)
* [Připojení MXChip IoT DevKit zařízení do aplikace Azure IoT Central](https://docs.microsoft.com/microsoft-iot-central/howto-connect-devkit)
