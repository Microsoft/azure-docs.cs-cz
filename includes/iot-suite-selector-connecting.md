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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "67175443"
---
> [!div class="op_single_selector"]
> * [C ve Windows](../articles/iot-accelerators/iot-accelerators-connecting-devices.md)
> * [C v Linuxu](../articles/iot-accelerators/iot-accelerators-connecting-devices-linux.md)
> * [C v Raspberry Pi](../articles/iot-accelerators/iot-accelerators-connecting-pi-c.md)
> * [Node.js (obecné)](../articles/iot-accelerators/iot-accelerators-connecting-devices-node.md)
> * [Node.js v Raspberry Pi](../articles/iot-accelerators/iot-accelerators-connecting-pi-node.md)
> * [Sada MXChip IoT DevKit](../articles/iot-accelerators/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2.md)

V tomto kurzu implementujete **chladicí** zařízení, které pošle následující telemetrii do [akcelerátoru řešení](../articles/iot-accelerators/about-iot-accelerators.md)vzdáleného monitorování:

* Teplota
* Tlak
* Vlhkost

Pro zjednodušení kód generuje ukázkové hodnoty telemetrie pro **chlazení**. Ukázku můžete roztáhnout připojením skutečných senzorů k vašemu zařízení a odesláním skutečné telemetrie.

Ukázkové zařízení také:

* Odešle metadata do řešení, aby bylo možné popsat jeho schopnosti.
* Reaguje na akce aktivované na stránce **zařízení** v řešení.
* Reaguje na změny konfigurace odesílané ze stránky **zařízení** v řešení.

K dokončení tohoto kurzu potřebujete mít aktivní účet Azure. Pokud účet nemáte, můžete si během několika minut vytvořit bezplatný zkušební účet. Podrobnosti najdete v článku [Bezplatná zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).

## <a name="before-you-start"></a>Než začnete

Než napíšete kód pro vaše zařízení, nasaďte akcelerátor řešení vzdáleného monitorování a přidejte do řešení nové reálné zařízení.

### <a name="deploy-your-remote-monitoring-solution-accelerator"></a>Nasazení akcelerátoru řešení vzdáleného monitorování

**Chladicí** zařízení, které v tomto kurzu vytvoříte, odesílá data do instance akcelerátoru řešení [vzdáleného monitorování](../articles/iot-accelerators/quickstart-remote-monitoring-deploy.md) . Pokud jste ve svém účtu Azure ještě nezřídili akcelerátor řešení vzdáleného monitorování, přečtěte si téma [nasazení akcelerátoru řešení vzdáleného monitorování](../articles/iot-accelerators/quickstart-remote-monitoring-deploy.md) .

Po dokončení procesu nasazení pro řešení vzdáleného monitorování kliknutím na **Spustit** otevřete řídicí panel řešení v prohlížeči.

![Řídicí panel řešení](media/iot-suite-selector-connecting/dashboard.png)

### <a name="add-your-device-to-the-remote-monitoring-solution"></a>Přidání zařízení do řešení vzdáleného monitorování

> [!NOTE]
> Pokud jste už zařízení ve svém řešení přidali, můžete tento krok přeskočit. Další krok ale vyžaduje připojovací řetězec zařízení. Připojovací řetězec zařízení můžete načíst z [Azure Portal](https://portal.azure.com) nebo pomocí nástroje [AZ IoT](https://docs.microsoft.com/cli/azure/iot?view=azure-cli-latest) CLI.

Aby se zařízení mohlo připojit k akcelerátoru řešení, musí identifikovat sebe, aby IoT Hub s použitím platných přihlašovacích údajů. Po přidání zařízení do řešení máte možnost Uložit připojovací řetězec zařízení, který tyto přihlašovací údaje obsahuje. Připojovací řetězec zařízení zahrnete do klientské aplikace dále v tomto kurzu.

Pokud chcete přidat zařízení do řešení vzdáleného monitorování, na stránce **Device Explorer** v řešení proveďte následující kroky:

1. Zvolte **+ nové zařízení**a pak jako **typ zařízení**zvolte **reálné** :

    ![Přidání skutečného zařízení](media/iot-suite-selector-connecting/devicesprovision.png)

1. Jako ID zařízení zadejte **fyzický-chladicí** zařízení. Vyberte možnosti **symetrického klíče** a **Automatické generování klíčů** :

    ![Zvolit možnosti zařízení](media/iot-suite-selector-connecting/devicesoptions.png)

1. Zvolte **Použít**. Pak si poznamenejte hodnoty primárního klíče pro **ID zařízení**, **primární klíč**a **připojovací řetězec** :

    ![Načíst přihlašovací údaje](media/iot-suite-selector-connecting/credentials.png)

Nyní jste přidali reálné zařízení do akcelerátoru řešení vzdáleného monitorování a poznamenali jste jeho připojovací řetězec zařízení. V následujících částech implementujete klientskou aplikaci, která pro připojení k vašemu řešení používá připojovací řetězec zařízení.

Klientská aplikace implementuje vestavěný model **chladicího** zařízení. Model zařízení akcelerátoru řešení určuje následující informace o zařízení:

* Vlastnosti zařízení hlásí řešení. Například zařízení **chlazení** hlásí informace o jeho firmwaru a umístění.
* Typy telemetrie, které zařízení odesílá do řešení. Například **chladicí** zařízení odesílá hodnoty teploty, vlhkosti a tlaku.
* Metody, které můžete naplánovat v řešení, které se má spustit na zařízení. Například **chladicí** zařízení musí implementovat metody **restart**, **FirmwareUpdate**, **EmergencyValveRelease**a **IncreasePressure** .
