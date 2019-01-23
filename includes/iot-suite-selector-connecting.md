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
ms.openlocfilehash: 64cbf8f8704dbb216a15247caf741ff43690496a
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2019
ms.locfileid: "54457047"
---
> [!div class="op_single_selector"]
> * [C ve Windows](../articles/iot-accelerators/iot-accelerators-connecting-devices.md)
> * [C v Linuxu](../articles/iot-accelerators/iot-accelerators-connecting-devices-linux.md)
> * [C v Raspberry Pi](../articles/iot-accelerators/iot-accelerators-connecting-pi-c.md)
> * [Node.js (obecné)](../articles/iot-accelerators/iot-accelerators-connecting-devices-node.md)
> * [Node.js v Raspberry Pi](../articles/iot-accelerators/iot-accelerators-connecting-pi-node.md)
> * [MXChip IoT DevKit](../articles/iot-accelerators/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2.md)

V tomto kurzu je implementovat **chladič** zařízení, která odesílá následující telemetrii do vzdáleného monitorování [akcelerátor řešení](../articles/iot-accelerators/about-iot-accelerators.md):

* Teplota
* Tlak
* Vlhkost

Kód pro zjednodušení generuje ukázkové hodnoty telemetrie pro **chladič**. Můžete ukázku rozšířit připojením skutečných senzorů k zařízení a odesíláním skutečné telemetrie.

Ukázka zařízení také:

* Odešle metadata do řešení popsat její možnosti.
* Reaguje na akcích aktivovaných v **zařízení** stránku v řešení.
* Odesilatel reaguje na změny konfigurace **zařízení** stránku v řešení.

K dokončení tohoto kurzu potřebujete mít aktivní účet Azure. Pokud účet nemáte, můžete si během několika minut vytvořit bezplatný zkušební účet. Podrobnosti najdete v článku [Bezplatná zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).

## <a name="before-you-start"></a>Než začnete

Předtím, než začnete psát kód pro vaše zařízení, nasaďte akcelerátor řešení vzdálené monitorování a přidat nový skutečné zařízení do řešení.

### <a name="deploy-your-remote-monitoring-solution-accelerator"></a>Nasazení akcelerátor řešení vzdálené monitorování

**Chladič** zařízení v tomto kurzu vytvoříte, odesílá data do instance [vzdálené monitorování](../articles/iot-accelerators/quickstart-remote-monitoring-deploy.md) akcelerátor řešení. Pokud jste ještě nezřídili akcelerátor řešení vzdálené monitorování ve svém účtu Azure, přečtěte si téma [nasazení akcelerátoru řešení vzdáleného monitorování](../articles/iot-accelerators/quickstart-remote-monitoring-deploy.md)

Po dokončení procesu nasazení pro řešení vzdáleného monitorování klikněte na tlačítko **spuštění** otevřete řídicí panel řešení ve vašem prohlížeči.

![Řídicí panel řešení](media/iot-suite-selector-connecting/dashboard.png)

### <a name="add-your-device-to-the-remote-monitoring-solution"></a>Přidání zařízení do řešení vzdáleného monitorování

> [!NOTE]
> Pokud jste už přidali zařízení ve vašem řešení, můžete tento krok přeskočit. Dalším krokem však vyžaduje připojovací řetězec zařízení. Můžete načíst připojovací řetězec zařízení z [webu Azure portal](https://portal.azure.com) nebo pomocí [az iot](https://docs.microsoft.com/cli/azure/iot?view=azure-cli-latest) nástroj rozhraní příkazového řádku.

Aby zařízení mohlo připojit k akcelerátoru řešení musí se identifikovat do služby IoT Hub pomocí platných přihlašovacích údajů. Máte možnost Uložit připojovací řetězec zařízení, která obsahuje tyto přihlašovací údaje při přidávání zařízení do řešení. Připojovací řetězec zařízení můžete zahrnout do klientské aplikace později v tomto kurzu.

Přidání zařízení do řešení vzdáleného monitorování, proveďte následující kroky na **zařízení** stránku v řešení:

1. Zvolte **+ nové zařízení**a klikněte na tlačítko **fyzické** jako **typ zařízení**:

    ![Přidání skutečného zařízení](media/iot-suite-selector-connecting/devicesprovision.png)

1. Zadejte **fyzické chladič** jako ID zařízení. Zvolte **symetrický klíč** a **automaticky vygenerovat klíče** možnosti:

    ![Zvolte možnosti zařízení](media/iot-suite-selector-connecting/devicesoptions.png)

1. Zvolte **Použít**. Potom si poznamenejte **ID zařízení**, **primární klíč**, a **primární klíč připojovacího řetězce** hodnoty:

    ![Načtení přihlašovacích údajů](media/iot-suite-selector-connecting/credentials.png)

Právě jste přidali skutečné zařízení k akcelerátoru řešení vzdáleného monitorování a jste si poznamenali svůj připojovací řetězec zařízení. V následujících částech můžete implementovat klientská aplikace, která používá připojovací řetězec zařízení pro připojení k vašemu řešení.

Klientská aplikace implementuje předdefinované **chladič** model zařízení. Model zařízení akcelerátoru řešení určuje následující informace o zařízení:

* Vlastnosti zařízení odesílá do řešení. Například **chladič** zařízení hlásí informace o jeho firmwaru a umístění.
* Typy telemetrie zařízení odešle do řešení. Například **chladič** zařízení odesílá teploty, vlhkosti a hodnotami.
* Metody, které můžete naplánovat z řešení pro spuštění na zařízení. Například **chladič** zařízení musí implementovat **restartovat**, **FirmwareUpdate**, **EmergencyValveRelease**, a  **IncreasePressure** metody.
