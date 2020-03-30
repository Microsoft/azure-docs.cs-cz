---
title: zahrnout soubor
description: zahrnout soubor
services: iot-suite
author: dominicbetts
ms.service: iot-suite
ms.topic: include
ms.date: 09/17/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: ca4bd3d3b40934323bab8036f3ce72e9281f1de4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "67175443"
---
> [!div class="op_single_selector"]
> * [C ve Windows](../articles/iot-accelerators/iot-accelerators-connecting-devices.md)
> * [C v Linuxu](../articles/iot-accelerators/iot-accelerators-connecting-devices-linux.md)
> * [C v Raspberry Pi](../articles/iot-accelerators/iot-accelerators-connecting-pi-c.md)
> * [Node.js (obecné)](../articles/iot-accelerators/iot-accelerators-connecting-devices-node.md)
> * [Node.js v Raspberry Pi](../articles/iot-accelerators/iot-accelerators-connecting-pi-node.md)
> * [Sada MXChip IoT DevKit](../articles/iot-accelerators/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2.md)

V tomto kurzu implementovat **chladicí zařízení,** které odesílá následující telemetrie do [akcelerátoru řešení](../articles/iot-accelerators/about-iot-accelerators.md)vzdáleného monitorování :

* Teplota
* Tlak
* Vlhkost

Pro jednoduchost kód generuje ukázkové hodnoty telemetrie pro **chladič**. Můžete rozšířit vzorek připojením skutečných senzorů k vašemu zařízení a odesláním skutečné telemetrie.

Vzorové zařízení také:

* Odešle metadata do řešení k popisu jeho schopnosti.
* Reaguje na akce aktivované ze stránky **Zařízení** v řešení.
* Reaguje na změny konfigurace odeslané ze stránky **Zařízení** v řešení.

K dokončení tohoto kurzu potřebujete mít aktivní účet Azure. Pokud účet nemáte, můžete si během několika minut vytvořit bezplatný zkušební účet. Podrobnosti najdete v článku [Bezplatná zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).

## <a name="before-you-start"></a>Než začnete

Než napíšete libovolný kód pro vaše zařízení, nasaďte akcelerátor řešení vzdáleného monitorování a přidejte do něj nové skutečné zařízení.

### <a name="deploy-your-remote-monitoring-solution-accelerator"></a>Nasazení akcelerátoru řešení vzdáleného monitorování

**Zařízení chladiče,** které vytvoříte v tomto kurzu, odesílá data do instance akcelerátoru řešení [vzdáleného monitorování.](../articles/iot-accelerators/quickstart-remote-monitoring-deploy.md) Pokud jste ještě nezřídit akcelerátor řešení vzdáleného monitorování ve vašem účtu Azure, [přečtěte si informace o nasazení akcelerátoru řešení vzdáleného monitorování.](../articles/iot-accelerators/quickstart-remote-monitoring-deploy.md)

Po dokončení procesu nasazení řešení vzdáleného monitorování otevřete řídicí panel řešení v prohlížeči kliknutím na **tlačítko Spustit.**

![Řídicí panel řešení](media/iot-suite-selector-connecting/dashboard.png)

### <a name="add-your-device-to-the-remote-monitoring-solution"></a>Přidání zařízení do řešení vzdáleného monitorování

> [!NOTE]
> Pokud jste již přidali zařízení v řešení, můžete tento krok přeskočit. Další krok však vyžaduje připojovací řetězec zařízení. Připojovací řetězec zařízení můžete načíst z [webu Azure Portal](https://portal.azure.com) nebo pomocí nástroje [az iot](https://docs.microsoft.com/cli/azure/iot?view=azure-cli-latest) CLI.

Aby se zařízení mohlo připojit k akcelerátoru řešení, musí se identifikovat ve službě IoT Hub pomocí platných přihlašovacích údajů. Máte možnost uložit připojovací řetězec zařízení, který obsahuje tato pověření při přidání zařízení do řešení. V tomto kurzu zahrnete připojovací řetězec zařízení do klientské aplikace dále.

Chcete-li přidat zařízení do řešení vzdáleného monitorování, proveďte následující kroky na stránce **Průzkumník zařízení** v řešení:

1. Zvolte **+ Nové zařízení**a pak jako typ **zařízení**zvolte **Skutečné** :

    ![Přidání skutečného zařízení](media/iot-suite-selector-connecting/devicesprovision.png)

1. Zadejte **Fyzický chladič** jako ID zařízení. Zvolte volby **Symetrické klávesy** **a Automatické generování kláves:**

    ![Volba možností zařízení](media/iot-suite-selector-connecting/devicesoptions.png)

1. Zvolte **Použít**. Potom poznamenejte **hodnoty ID zařízení**, **primárního klíče**a **připojovacího řetězce:**

    ![Načíst pověření](media/iot-suite-selector-connecting/credentials.png)

Nyní jste přidali skutečné zařízení do akcelerátoru řešení vzdáleného monitorování a poznamenali jste jeho připojovací řetězec zařízení. V následujících částech implementujete klientskou aplikaci, která používá připojovací řetězec zařízení pro připojení k řešení.

Klientská aplikace implementuje model integrovaného **zařízení chladiče.** Model akcelerátoru řešení určuje následující informace o zařízení:

* Vlastnosti zařízení hlásí řešení. Například **zařízení Chiller** hlásí informace o jeho firmware a umístění.
* Typy telemetrie zařízení odešle do řešení. Například **chladicí** zařízení odesílá hodnoty teploty, vlhkosti a tlaku.
* Metody, které můžete naplánovat z řešení ke spuštění v zařízení. Například **zařízení chiller** musí implementovat **Reboot**, **FirmwareUpdate**, **EmergencyValveRelease**a **IncreasePressure** metody.
