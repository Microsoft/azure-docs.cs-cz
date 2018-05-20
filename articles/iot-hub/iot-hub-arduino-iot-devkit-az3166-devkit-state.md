---
title: K řízení MXChip IoT DevKit uživatele DIODU používejte dvojčata zařízení Azure | Microsoft Docs
description: V tomto kurzu zjistěte, jak sledovat DevKit stavy a řídit uživatele DIODU s dvojčata zařízení Azure IoT Hub.
services: iot-hub
documentationcenter: ''
author: liydu
manager: timlt
tags: ''
keywords: ''
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/04/2018
ms.author: liydu
ms.openlocfilehash: 3678eb20429d9e5123272499d474fdec2cdab065
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
---
# <a name="mxchip-iot-devkit"></a>MXChip IoT DevKit

V tomto příkladu můžete použít k monitorování stavy informace a senzor MXChip IoT DevKit Wi-Fi a k řízení barva DIODU uživatele pomocí dvojčata zařízení Azure IoT Hub.

## <a name="what-you-learn"></a>Co se naučíte

- Postup sledování stavy senzor MXChip IoT DevKit.
- Jak používat k ovládání barva RGB DIODU DevKit dvojčata zařízení Azure.

## <a name="what-you-need"></a>Co potřebujete

- Nastavení vývojového prostředí pomocí následujících [– Příručka Začínáme](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started).
- Z vaší okno terminálu Git Bash (nebo jiných rozhraní příkazového řádku Git) zadejte následující příkazy:
    - `git clone https://github.com/DevKitExamples/DevKitState.git`
    - `cd DevKitState`
    - `code .`

## <a name="provision-azure-services"></a>Zřídit služby Azure

1. Klikněte **úlohy** rozevírací nabídky v Visual Studio Code a vyberte **spustit úlohu...**   -  **cloudu provision**.
2. Průběh se zobrazí v části **Terminálové** kartě **úvodní** panelu.
3. Po zobrazení výzvy se zprávou *jaké předplatné chcete zvolit*, vyberte předplatné.
4. Vyberte nebo zvolte skupinu prostředků. 
 
    > [!NOTE]
    > Pokud již máte bezplatné Centrum IoT, bude tento krok přeskočen.

5. Po zobrazení výzvy se zprávou *jaké služby IoT hub chcete zvolit*vyberte nebo vytvořte služby IoT Hub.
6. Něco podobná *aplikaci funkce: název aplikace funkci: xxx*, se zobrazí. Poznamenejte si název funkce aplikace; použije se v pozdější fázi.
7. Počkejte nasazení šablony Azure Resource Manager ukončíte, což je uvedené, kdy se zpráva *nasazení šablony správce prostředků: provádí* se zobrazí.

## <a name="deploy-function-app"></a>Nasazení aplikace – funkce

1. Klikněte **úlohy** rozevírací nabídky v Visual Studio Code a vyberte **spustit úlohu...**   -  **nasazení cloudu**.
2. Počkejte kód aplikace funkce odesílání na dokončení; zpráva *nasadí aplikaci funkce: provádí* se zobrazí.

## <a name="configure-iot-hub-device-connection-string-in-devkit"></a>Konfigurace služby IoT Hub zařízení připojovací řetězec v DevKit

1. Vaše IoT DevKit MXChip připojení k vašemu počítači.
2. Klikněte **úlohy** rozevírací nabídky v Visual Studio Code a vyberte **spustit úlohu...**   -  **připojení konfigurace zařízení**
3. Na MXChip IoT DevKit, stiskněte a podržte tlačítko **A**, stiskněte **resetovat** tlačítko a tlačítko pro uvolnění **A** aby DekKit zadejte režim konfigurace.
4. Počkejte, než pro proces konfigurace připojovací řetězec dokončit.

## <a name="upload-arduino-code-to-devkit"></a>Nahrajte do DevKit Arduino kódu

S vaší IoT DevKit MXChip připojené k počítači:
1. Klikněte **úlohy** rozevírací nabídky v Visual Studio Code a vyberte **spustit úlohu vytvoření...** Nákresu Arduino kompiluje a nahrané do DevKit.
2. Když nákresu odeslal úspěšně, *sestavení & Nahrát načrtnout: Úspěch* se zobrazí zpráva.

## <a name="monitor-devkit-state-in-browser"></a>Stav DevKit sledování v prohlížeči

1. Ve webovém prohlížeči, otevřete `DevKitState\web\index.html` souboru –, který jste vytvořili během [co potřebujete](#whatyouneed) krok.
2. Zobrazí se následující webové stránce:![Zadejte název aplikace funkce.](media/iot-hub-arduino-iot-devkit-az3166-devkit-state/devkit-state-function-app-name.png)
1. Zadejte název funkce aplikace, které jste si poznamenali dříve.
2. Klikněte **Connect** tlačítko
3. Během pár sekund stránka se obnoví a zobrazí stav připojení Wi-Fi DevKit a stav každé ze senzorů zařadit.

## <a name="control-the-devkits-user-led"></a>Řízení uživatele DevKit DIODU

1. Klikněte na grafiku DIODU uživatele na obrázku webové stránky.
2. Na obrazovce během pár sekund, aktualizuje a ukazuje aktuální stav uživatele Indikátor barvy.
3. Zkuste změnit hodnotu barva RGB DIODU kliknutím v různých umístěních na ovládací prvky posuvníku RGB.

## <a name="example-operation"></a>Příklad operace

![Příklad postupu testu](media/iot-hub-arduino-iot-devkit-az3166-devkit-state/devkit-state.gif)

> [!NOTE]
> Zobrazí se nezpracovaná data tohoto dvojče zařízení na portálu Azure: IoT Hub -\> zařízení IoT -\> *\<zařízení\>*  - \> dvojče zařízení.

## <a name="next-steps"></a>Další postup

Jste se naučili, jak:
- Připojte zařízení MXChip IoT DevKit k vaší akcelerátoru řešení vzdáleného monitorování Azure IoT.
- Pomocí funkce dvojčata zařízení Azure IoT smysl a řídit barva RGB DIODU DevKit.

Tady jsou navrhované další kroky:

* [Azure IoT vzdálené monitorování přehled řešení akcelerátoru](https://docs.microsoft.com/azure/iot-suite/)
* [Připojte zařízení MXChip IoT DevKit do aplikace Azure IoT centrální](https://docs.microsoft.com/microsoft-iot-central/howto-connect-devkit)
