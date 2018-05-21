---
title: zahrnout soubor
description: zahrnout soubor
services: iot-suite
author: dominicbetts
ms.service: iot-suite
ms.topic: include
ms.date: 04/24/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 500e335d0b2eddc56cdfb9828236bc4676d9b6aa
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
---
> [!div class="op_single_selector"]
> * [C ve Windows](../articles/iot-accelerators/iot-accelerators-connecting-devices.md)
> * [C v Linuxu](../articles/iot-accelerators/iot-accelerators-connecting-devices-linux.md)
> * [Node.js (obecné)](../articles/iot-accelerators/iot-accelerators-connecting-devices-node.md)
> * [Node.js v Raspberry Pi](../articles/iot-accelerators/iot-accelerators-connecting-pi-node.md)
> * [C v Raspberry Pi](../articles/iot-accelerators/iot-accelerators-connecting-pi-c.md)

V tomto kurzu budete implementovat **chladič** zařízení, která odesílá následující telemetrii na vzdálené monitorování [řešení akcelerátoru](../articles/iot-accelerators/iot-accelerators-what-are-solution-accelerators.md):

* Teplota
* Tlak
* Vlhkost

Pro jednoduchost, vygeneruje kód ukázkové hodnoty telemetrických dat pro **chladič**. Ukázku můžete rozšířit připojením skutečné snímače do svého zařízení a odesláním skutečné telemetrie.

Ukázka zařízení také:

* Odešle metadata do řešení popisují možnosti.
* Reaguje na akcích aktivovaných v **zařízení** stránky v řešení.
* Odesílání reaguje na změny konfigurace z **zařízení** stránky v řešení.

K dokončení tohoto kurzu potřebujete mít aktivní účet Azure. Pokud účet nemáte, můžete si během několika minut vytvořit bezplatný zkušební účet. Podrobnosti najdete v článku [Bezplatná zkušební verze Azure](http://azure.microsoft.com/pricing/free-trial/).

## <a name="before-you-start"></a>Než začnete

Než napíšete kód pro vaše zařízení, nasaďte vaše vzdálené monitorování akcelerátoru řešení a přidejte nový fyzického zařízení k řešení.

### <a name="deploy-your-remote-monitoring-solution-accelerator"></a>Nasazení vaší vzdálené monitorování řešení akcelerátoru

**Chladič** zařízení v tomto kurzu vytvoříte odesílá data do instance [vzdálené monitorování](../articles/iot-suite/iot-suite-remote-monitoring-explore.md) akcelerátoru řešení. Pokud jste ještě nezřídili vzdálené monitorování akcelerátoru řešení v účtu Azure, najdete v části [nasazení vzdálené monitorování řešení akcelerátoru](../articles/iot-accelerators/iot-accelerators-remote-monitoring-deploy.md)

Při procesu nasazení pro dokončení řešení vzdáleného monitorování, klikněte na tlačítko **spusťte** otevřete řídicí panel řešení v prohlížeči.

![Řídicí panel řešení](media/iot-suite-selector-connecting/dashboard.png)

### <a name="add-your-device-to-the-remote-monitoring-solution"></a>Přidání zařízení do řešení vzdáleného monitorování

> [!NOTE]
> Pokud jste již přidali zařízení ve vašem řešení, můžete tento krok přeskočit. Dalším krokem však vyžaduje připojovací řetězec zařízení. Můžete načíst připojovací řetězec zařízení z [portál Azure](https://portal.azure.com) nebo pomocí [az iot](https://docs.microsoft.com/cli/azure/iot?view=azure-cli-latest) nástroj příkazového řádku.

U zařízení pro připojení k akcelerátoru řešení se musí identifikovat do služby IoT Hub pomocí platných přihlašovacích údajů. Máte možnost uložit zařízení připojovací řetězec, který obsahuje tyto přihlašovací údaje, když přidáte zařízení řešení. Připojovací řetězec zařízení můžete zahrnout do klientské aplikace později v tomto kurzu.

Chcete-li přidat zařízení do řešení vzdáleného monitorování, proveďte následující kroky na **zařízení** stránky v řešení:

1. Zvolte **+ nové zařízení**a potom zvolte **fyzické** jako **typ zařízení**:

    ![Přidat fyzického zařízení](media/iot-suite-selector-connecting/devicesprovision.png)

1. Zadejte **fyzického chladič** jako ID zařízení. Vyberte **symetrický klíč** a **automaticky generovat klíče** možnosti:

    ![Vyberte možnosti zařízení](media/iot-suite-selector-connecting/devicesoptions.png)

1. Zvolte **použít**. Potom si poznamenejte **ID zařízení**, **primární klíč**, a **primární klíč pro řetězec připojení** hodnoty:

    ![Načíst přihlašovací údaje](media/iot-suite-selector-connecting/credentials.png)

Nyní jste přidali fyzického zařízení pro vzdálené monitorování akcelerátoru řešení a jsou uvedené jeho zařízení připojovací řetězec. V následujících částech můžete implementovat aplikace klienta, která používá zařízení připojovací řetězec pro připojení k řešení.

Klientská aplikace implementuje integrované **chladič** model zařízení. Model zařízení akcelerátoru řešení určuje toto zařízení:

* Vlastnosti zařízení sestav řešení. Například **chladič** zařízení hlásí informace o jeho firmware a umístění.
* Typy telemetrických dat, které zařízení odesílá do řešení. Například **chladič** zařízení odesílá přetížení hodnoty, vlhkosti a teploty.
* Metody můžete naplánovat z řešení na zařízení spouštět. Například **chladič** zařízení musí implementovat **restartovat**, **FirmwareUpdate**, **EmergencyValveRelease**, a  **IncreasePressure** metody.