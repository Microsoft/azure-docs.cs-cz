---
title: Připojení IoT DevKit k řešení vzdáleného monitorování – Azure | Microsoft Docs
description: V této příručce se dozvíte, jak odeslat telemetrii ze senzorů na zařízení IoT DevKit AZ3166 na základě akcelerátoru řešení vzdáleného monitorování pro monitorování a vizualizaci.
author: isabelcabezasm
manager: ''
ms.service: iot-accelerators
services: iot-accelerators
ms.devlang: c
ms.topic: conceptual
ms.date: 11/29/2018
ms.author: isacabe
ms.openlocfilehash: ed14d4cb79cb5794fa666f1bb96e062d69128d0f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87337666"
---
# <a name="connect-an-iot-devkit-device-to-the-remote-monitoring-solution-accelerator"></a>Připojení zařízení IoT DevKit k akcelerátoru řešení vzdáleného monitorování

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

V této příručce se dozvíte, jak spustit ukázkovou aplikaci na zařízení IoT DevKit. Vzorový kód pošle telemetrii ze senzorů na zařízení DevKit do akcelerátoru řešení.

[MXChip IoT DevKit](https://aka.ms/iot-devkit) je vše v Arduino kompatibilní vývěsce s bohatými periferními zařízeními a snímači. Můžete ji vyvíjet pro IT pomocí [Azure IoT Device Workbench](https://aka.ms/iot-workbench) nebo rozšíření sady [nástrojů Azure iot Tools](https://aka.ms/azure-iot-tools) v Visual Studio Code. [Katalog projektů](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) obsahuje ukázkové aplikace, které vám pomůžou prototypovat řešení IoT.

## <a name="before-you-begin"></a>Než začnete

K dokončení kroků v tomto kurzu nejdříve proveďte následující úlohy:

* Připravte své DevKit podle kroků v části [připojení IoT DEVKIT AZ3166 do Azure IoT Hub v cloudu](/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started).

## <a name="open-sample-project"></a>Otevřít ukázkový projekt

Chcete-li otevřít ukázku vzdáleného monitorování v VS Code:

1. Ujistěte se, že vaše aplikace IoT DevKit není k vašemu počítači. Nejprve začněte VS Code a pak připojte DevKit k počítači.

1. Kliknutím `F1` otevřete paletu příkazů, zadáte a vyberete **Azure IoT Device Workbench: otevřít příklady...**. Pak jako panel vyberte **IoT DevKit** .

1. Najděte **vzdálené monitorování** a klikněte na **otevřít ukázku**. Otevře se nové okno VS Code, ve kterém se zobrazí složka projektu:

   ![IoT Workbench, vyberte příklad vzdáleného monitorování.](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/iot-workbench-example.png)

## <a name="configure-the-device"></a>Konfigurace zařízení

Konfigurace IoT Hubho připojovacího řetězce zařízení na zařízení DevKit:

1. Přepněte IoT DevKit do **režimu konfigurace**:

    * Podržte tlačítko **A**.
    * Vložení a uvolnění tlačítka **obnovit**

1. Na obrazovce se zobrazí ID DevKit a `Configuration` .

    ![Konfigurační režim IoT DevKit](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/devkit-configuration-mode.png)

1. Stisknutím **klávesy F1** otevřete paletu příkazů, potom zadejte a vyberte **Azure IoT Device Workbench: Konfigurace nastavení zařízení... > připojovací řetězec zařízení**.

1. Vložte připojovací řetězec, který jste zkopírovali dříve, a stisknutím klávesy **ENTER** nakonfigurujte zařízení.

## <a name="build-the-code"></a>Sestavení kódu

Postup sestavení a nahrání kódu zařízení:

1. Stisknutím `F1` otevřete paletu příkazů, zadáte a vyberete **Azure IoT Device Workbench: nahrání kódu zařízení**:

1. VS Code zkompiluje a nahraje kód do zařízení DevKit:

    ![IoT Workbench: nahrání zařízení >](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/iot-workbench-device-uploaded.png)

1. Zařízení DevKit se restartuje a spustí kód, který jste nahráli.

## <a name="test-the-sample"></a>Test ukázky

Pokud chcete ověřit, že ukázková aplikace, kterou jste nahráli do zařízení DevKit, funguje, proveďte následující kroky:

### <a name="view-the-telemetry-sent-to-remote-monitoring-solution"></a>Zobrazit telemetrii odeslanou do řešení vzdáleného monitorování

Po spuštění ukázkové aplikace pošle zařízení DevKit telemetrii ze svých dat senzorů přes Wi-Fi do akcelerátoru řešení. Zobrazení telemetrie:

1. Přejděte na řídicí panel řešení a klikněte na **Device Explorer**.

1. Klikněte na název zařízení zařízení DevKit. na kartě na pravé straně můžete zobrazit telemetrii z DevKit v reálném čase:

    ![Data senzorů v Azure IoT Suite](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-dashboard.png)

### <a name="control-the-devkit-device"></a>Řízení zařízení DevKit

Akcelerátor řešení vzdáleného monitorování umožňuje vzdálené řízení zařízení. Vzorový kód implementuje tři metody, které lze zobrazit v části **Metoda** , když vyberete zařízení na stránce **Device Explorer** :

![Metody IoT DevKit](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-methods.png)

Chcete-li změnit barvu jedné z DevKit diody LED, použijte metodu **LedColor** :

1. Vyberte název zařízení ze seznamu zařízení a klikněte na **úlohy**:

    ![Vytvoření úlohy](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-job.png)

1. Nakonfigurujte úlohy pomocí následujících hodnot a klikněte na **použít**:

   * Vyberte úlohu: **metoda Run**
   * Název metody: **LedColor**
   * Název úlohy: **ChangeLedColor**

     ![Nastavení úlohy](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/iot-suite-change-color.png)

1. Po několika sekundách barva INDIKÁTORu RGB (pod tlačítkem A) na DevKit změny:

    ![DevKit Red LED pro IoT](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-devkit-led.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud se budete chtít přesunout ke kurzům, ponechte akcelerátor řešení pro vzdálené monitorování nasazený.

Pokud už akcelerátor řešení nepotřebujete, odstraňte ho ze stránky zřízené řešení, a to tak, že ho vyberete a pak kliknete na odstranit řešení:

![Odstranění řešení](media/quickstart-remote-monitoring-deploy/deletesolution.png)

## <a name="problems-and-feedback"></a>Problémy a zpětná vazba

Pokud narazíte na nějaké problémy, přečtěte si [Nejčastější dotazy k IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) nebo se můžete obrátit na nás s použitím následujících kanálů:

* [Gitter.im](https://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili připojit zařízení DevKit k akcelerátoru řešení vzdáleného monitorování, je tady několik navrhovaných dalších kroků:

* [Přehled akcelerátorů řešení Azure IoT](https://docs.microsoft.com/azure/iot-accelerators/)
* [Přizpůsobení uživatelského rozhraní](iot-accelerators-remote-monitoring-customize.md)
