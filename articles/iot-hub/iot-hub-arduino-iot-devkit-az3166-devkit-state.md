---
title: Použití dvojčat zařízení Azure k ovládání LED LED uživatele MXChip IoT DevKit | Dokumenty společnosti Microsoft
description: V tomto kurzu se dozvíte, jak sledovat stavy DevKitu a řídit LED led uživatele pomocí dvojčat zařízení Azure IoT Hub.
author: liydu
manager: jeffya
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 04/04/2018
ms.author: liydu
ms.openlocfilehash: deb1ea8c7b41ad48bddebfbed1b15c667ee0071a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73483937"
---
# <a name="mxchip-iot-devkit"></a>Sada MXChip IoT DevKit

Tento příklad můžete použít ke sledování mxchip IoT DevKit WiFi informace a stavy senzorů a řídit barvu led diody uživatele pomocí dvojčatzařízení Azure IoT Hub.

## <a name="what-you-learn"></a>Co se naučíte

- Jak sledovat stavy snímače MXChip IoT DevKit.

- Jak používat dvojčata zařízení Azure k ovládání barvy RGB LED devkitu.

## <a name="what-you-need"></a>Co potřebujete

- Nastavte vývojové prostředí podle [pokynů Začínáme](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started).

- Z okna terminálu GitBash (nebo jiného rozhraní příkazového řádku Git) zadejte následující příkazy:

   ```bash
   git clone https://github.com/DevKitExamples/DevKitState.git
   cd DevKitState
   code .
   ```

## <a name="provision-azure-services"></a>Poskytování služeb Azure

1. Klikněte na rozevírací nabídku **Úkoly** v kódu sady Visual Studio a vyberte **Spustit úlohu...**  -  **poskytování cloudu**.

2. Váš postup se zobrazí na kartě **TERMINÁL** **na** uvítacím panelu.

3. Po zobrazení výzvy se zprávou *Jaké předplatné chcete vybrat*, vyberte předplatné.

4. Vyberte nebo zvolte skupinu prostředků. 
 
   > [!NOTE]
   > Pokud už máte bezplatný IoT Hub, můžete tento krok přeskočit.

5. Po zobrazení výzvy se *zprávou, kterou službu IoT hub chcete vybrat*, vyberte nebo vytvořte službu IoT Hub.

6. Něco podobného *funkci app: funkce název aplikace: xxx*, se zobrazí. Poznamenejte si název aplikace funkce; bude použit v pozdějším kroku.

7. Počkejte na dokončení nasazení šablony Azure Resource Manager, což je uvedeno při *nasazení šablony správce prostředků zprávy: Je* zobrazeno.

## <a name="deploy-function-app"></a>Nasadit aplikaci funkcí

1. Klikněte na rozevírací nabídku **Úkoly** v kódu sady Visual Studio a vyberte **Spustit úlohu...**  -  **cloud-deploy**.

2. Počkejte na dokončení procesu nahrávání kódu aplikace funkce. aplikace funkce zprávy *nasazuje: Hotovo* se zobrazí.

## <a name="configure-iot-hub-device-connection-string-in-devkit"></a>Konfigurace připojovacího řetězce zařízení služby IoT Hub v aplikaci DevKit

1. Připojte svůj MXChip IoT DevKit k počítači.

2. Klikněte na rozevírací nabídku **Úkoly** v kódu sady Visual Studio a vyberte **Spustit úlohu...**  -  **config-device-connection**

3. Na MXChip IoT DevKit stiskněte a podržte tlačítko **A**, stiskněte tlačítko **Reset** a poté uvolněte tlačítko **A,** aby dekKit přepnul do konfiguračního režimu.

4. Počkejte na dokončení procesu konfigurace připojovacího řetězce.

## <a name="upload-arduino-code-to-devkit"></a>Nahrát arduino kód devKit

S připojeným mxchip iot devkit em a k počítači:

1. Klikněte na rozevírací nabídku **Úkoly** v kódu Sady Visual Studio a vyberte **Spustit úlohu sestavení...** Arduino skica je sestavena a nahrána do DevKitu.

2. Po úspěšném nahrání skici se zobrazí zpráva *build & Upload Sketch: success.*

## <a name="monitor-devkit-state-in-browser"></a>Sledování stavu DevKitu v prohlížeči

1. Ve webovém prohlížeči `DevKitState\web\index.html` otevřete soubor, který byl vytvořen během kroku Co potřebujete.

2. Zobrazí se následující webová stránka:![Zadejte název aplikace funkce.](media/iot-hub-arduino-iot-devkit-az3166-devkit-state/devkit-state-function-app-name.png)

3. Zadejte název aplikace funkce, kterou jste si zapsali dříve.

4. Klikněte na tlačítko **Připojit**

5. Během několika sekund se stránka aktualizuje a zobrazí stav připojení WiFi devKitu a stav každého z palubních senzorů.

## <a name="control-the-devkits-user-led"></a>Ovládání uživatelské LED diody DevKit

1. Klikněte na obrázek LED uživatele na obrázku webové stránky.

2. Během několika sekund se obrazovka aktualizuje a zobrazí aktuální stav barev led diody uživatele.

3. Zkuste změnit hodnotu barvy RGB LED kliknutím na různá místa na ovládacích prvcích posuvníku RGB.

## <a name="example-operation"></a>Příklad operace

![Příklad zkušebního postupu](media/iot-hub-arduino-iot-devkit-az3166-devkit-state/devkit-state.gif)

> [!NOTE]
> Nezpracovaná data dvojčete zařízení najdete na\> webu Azure Portal: IoT Hub – zařízení IoT –\> * \<dvojče zařízení\> *  - \> zařízení.

## <a name="next-steps"></a>Další kroky

Naučili jste se, jak:
- Připojte zařízení MXChip IoT DevKit k akcelerátoru řešení Vzdálené monitorování Azure IoT.
- Pomocí funkce dvojčat zařízení Azure IoT můžete vycítit a řídit barvu RGB LED devkitu.

Zde jsou navrhované další kroky:

* [Přehled akcelerátoru řešení vzdáleného monitorování Azure IoT](https://docs.microsoft.com/azure/iot-suite/)
* [Připojení zařízení MXChip IoT DevKit k aplikaci Azure IoT Central](/azure/iot-central/core/howto-connect-devkit)
