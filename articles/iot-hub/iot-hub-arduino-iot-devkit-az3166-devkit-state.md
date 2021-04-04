---
title: Použití vláken zařízení Azure k řízení MXChip uživatele IoT DevKit | Microsoft Docs
description: V tomto kurzu se dozvíte, jak monitorovat stavy DevKit a řídit, jak se uživatel vystavuje s využitím vlákna zařízení v Azure IoT Hub.
author: liydu
manager: jeffya
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 04/04/2018
ms.author: liydu
ms.openlocfilehash: 8c43c8a0b9fdf30b5ce5ae6ecbf123b563099ff6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "92148781"
---
# <a name="mxchip-iot-devkit"></a>Sada MXChip IoT DevKit

Tento příklad můžete použít ke sledování informací o MXChip IoT DevKit Wi-Fi a stavu senzoru a k řízení barvy uživatele, který využívali v případě nevláken zařízení Azure IoT Hub.

## <a name="what-you-learn"></a>Co se naučíte

- Jak monitorovat stavy MXChip IoT DevKit snímače.

- Jak používat vlákna zařízení v Azure k řízení barvy INDIKÁTORu RGB pro DevKit.

## <a name="what-you-need"></a>Co budete potřebovat

- Pomocí [průvodce Začínáme](./iot-hub-arduino-iot-devkit-az3166-get-started.md)nastavte vývojové prostředí.

- V okně terminálu GitBash (nebo jiném rozhraní příkazového řádku Git) zadejte následující příkazy:

   ```bash
   git clone https://github.com/DevKitExamples/DevKitState.git
   cd DevKitState
   code .
   ```

## <a name="provision-azure-services"></a>Zřizování služeb Azure

1. V Visual Studio Code klikněte na rozevírací nabídku **úlohy** a vyberte **Spustit úlohu...**  -  **zřízení cloudu**.

2. Váš pokrok se zobrazí na kartě **terminál** na **uvítacím** panelu.

3. Po zobrazení výzvy s výzvou, kterou *předplatné chcete vybrat*, vyberte předplatné.

4. Vyberte nebo zvolte skupinu prostředků. 
 
   > [!NOTE]
   > Pokud už máte bezplatný IoT Hub, můžete tento krok přeskočit.

5. Až se zobrazí výzva se zprávou, *jakou IoT Hub chcete vybrat*, vyberte nebo vytvořte IoT Hub.

6. Něco podobného *aplikaci Function App: název aplikace Function App: XXX* se zobrazí. Zapište si název aplikace Function App; bude použit v pozdějším kroku.

7. Počkejte, než se dokončí nasazení šablony Azure Resource Manager, což je uvedeno při *nasazení zprávy Správce prostředků šablony: dokončeno* .

## <a name="deploy-function-app"></a>Nasazení Function App

1. V Visual Studio Code klikněte na rozevírací nabídku **úlohy** a vyberte **Spustit úlohu...**  -  **nasazení v cloudu**

2. Počkejte na dokončení procesu nahrávání kódu aplikace Function App. nasazují se *aplikace funkce zpráva: dokončeno* se zobrazí.

## <a name="configure-iot-hub-device-connection-string-in-devkit"></a>Konfigurace připojovacího řetězce zařízení IoT Hub v DevKit

1. Připojte svůj MXChip IoT DevKit k vašemu počítači.

2. V Visual Studio Code klikněte na rozevírací nabídku **úlohy** a vyberte **Spustit úlohu...**  -  **Konfigurace-zařízení-připojení**

3. V MXChip IoT DevKit stiskněte a podržte tlačítko **a, stiskněte tlačítko** **reset** a pak klikněte na tlačítko **uvolnit a, aby DekKit** zadal režim konfigurace.

4. Počkejte, než se dokončí proces konfigurace připojovacího řetězce.

## <a name="upload-arduino-code-to-devkit"></a>Nahrání kódu Arduino do DevKit

S MXChip IoT DevKit připojeným k vašemu počítači:

1. V Visual Studio Code klikněte na rozevírací nabídku **úlohy** a vyberte **Spustit úlohu sestavení...** Náčrt Arduino se zkompiluje a nahraje na DevKit.

2. Po úspěšném nahrání náčrtu se zobrazí *sestavení & odeslat nákres:* zpráva o úspěchu.

## <a name="monitor-devkit-state-in-browser"></a>Monitorovat stav DevKit v prohlížeči

1. Ve webovém prohlížeči otevřete `DevKitState\web\index.html` soubor, který byl vytvořen během kroku co potřebujete.

2. Zobrazí se následující webová stránka:![Zadejte název aplikace Function App.](media/iot-hub-arduino-iot-devkit-az3166-devkit-state/devkit-state-function-app-name.png)

3. Zadejte název aplikace Function App, kterou jste napsali dříve.

4. Klikněte na tlačítko **připojit** .

5. Během několika sekund se stránka aktualizuje a zobrazí stav připojení Wi-Fi DevKit a stav každého z nich.

## <a name="control-the-devkits-user-led"></a>Řízení INDIKÁTORu uživatele DevKit

1. Na obrázku webové stránky klikněte na obrázek INDIKÁTORu uživatele.

2. Během několika sekund se obrazovka aktualizuje a zobrazí aktuální stav barvy INDIKÁTORu uživatele.

3. Zkuste změnit hodnotu barvy INDIKÁTORu RGB kliknutím na různá umístění v ovládacím prvku posuvníku RGB.

## <a name="example-operation"></a>Ukázková operace

![Příklad testovací procedury](media/iot-hub-arduino-iot-devkit-az3166-devkit-state/devkit-state.gif)

> [!NOTE]
> V Azure Portal vidíte nezpracovaná data, která jsou v zařízení vlákna: IoT Hub- \> IoT zařízení – \> *\<your device\>*  - \> vlákna zařízení.

## <a name="next-steps"></a>Další kroky

Zjistili jste, jak:
- Připojte zařízení IoT DevKit MXChip k akcelerátoru řešení vzdáleného monitorování Azure IoT.
- Použijte funkci revláken zařízení Azure IoT k určení a kontrole barvy INDIKÁTORu DevKitu RGB.

Tady je doporučený další krok: [Přehled akcelerátoru řešení Azure IoT Remote Monitoring](/azure/iot-suite/) .