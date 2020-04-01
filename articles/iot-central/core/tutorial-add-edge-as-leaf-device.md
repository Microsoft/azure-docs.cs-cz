---
title: Přidání zařízení Azure IoT Edge do Azure IoT Central | Dokumenty společnosti Microsoft
description: Jako operátor přidejte zařízení Azure IoT Edge do aplikace Azure IoT Central
author: rangv
ms.author: rangv
ms.date: 12/09/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: bf74784998de6bbad6310c48c24d6353512bff13
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "77027731"
---
# <a name="tutorial-add-an-azure-iot-edge-device-to-your-azure-iot-central-application"></a>Kurz: Přidání zařízení Azure IoT Edge do aplikace Azure IoT Central

Tento kurz ukazuje, jak nakonfigurovat a přidat zařízení Azure IoT Edge do aplikace Azure IoT Central. Kurz používá virtuální počítač (VM) s podporou IoT Edge z Azure Marketplace k simulaci zařízení IoT Edge. Zařízení IoT Edge používá modul, který generuje simulovanou telemetrii prostředí. Telemetrická data se zobrazí na řídicím panelu v aplikaci IoT Central.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření šablony zařízení pro zařízení IoT Edge
> * Vytvoření zařízení IoT Edge ve Středu IoT
> * Nasazení simulovaného zařízení IoT Edge do virtuálního počítače s Linuxem

## <a name="prerequisites"></a>Požadavky

Dokončete rychlý start [aplikace Create a Azure IoT Central](./quick-deploy-iot-central.md) a vytvořte aplikaci IoT Central pomocí vlastní šablony vlastní aplikace > vlastní **aplikace.**

K dokončení kroků v tomto kurzu potřebujete aktivní předplatné Azure.

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

Stáhněte si soubor manifestu IoT Edge z GitHubu. Klikněte pravým tlačítkem myši na následující odkaz a pak vyberte **uložit odkaz jako**: [EnvironmentalSensorManifest.json](https://raw.githubusercontent.com/Azure-Samples/iot-central-docs-samples/master/iotedge/EnvironmentalSensorManifest.json)

## <a name="create-device-template"></a>Vytvořit šablonu zařízení

V této části vytvoříte šablonu zařízení pro zařízení IoT Edge, které se připojuje k vaší aplikaci IoT Central. Můžete importovat manifest IoT Edge, abyste mohli začít, a pak upravit šablonu tak, aby přidala definice a zobrazení telemetrie:

### <a name="import-manifest-to-create-template"></a>Importovat manifest pro vytvoření šablony

Vytvoření šablony zařízení z manifestu IoT Edge:

1. V aplikaci IoT Central přejděte na **šablony zařízení** a vyberte **+ Nový**.

1. Na stránce **Vybrat typ šablony** vyberte dlaždici Azure **IoT Edge.** Pak vyberte **Další: Přizpůsobit**.

1. Na stránce **manifestu nasazení Azure IoT Edge** vyberte **Procházet** a nahrajte **environmentalSensorManifest.json,** který jste si stáhli dříve. Pak vyberte **Další: Revize**.

1. Na stránce **Revize** vyberte **Vytvořit**.

1. Po vytvoření šablony změňte její název na *Zařízení pro okraj senzoru prostředí*.

1. Vyberte **rozhraní Manage** v modulu **SimulatedTemperatureSensor,** chcete-li zobrazit dvě vlastnosti definované v manifestu:

![Šablona zařízení vytvořená z manifestu IoT Edge](./media/tutorial-add-edge-as-leaf-device/imported-manifest.png)

### <a name="add-telemetry-to-manifest"></a>Přidání telemetrie do manifestu

Manifest IoT Edge nedefinuje telemetrii, kterou modul odesílá. Do šablony zařízení je nutné přidat definice telemetrie. Modul **SimulatedTemperatureSensor** odesílá telemetrické zprávy, které vypadají jako následující JSON:

```json
{
    "machine": {
        "temperature": 75.0,
        "pressure": 40.2
    },
    "ambient": {
        "temperature": 23.0,
        "humidity": 30.0
    },
    "timeCreated": ""
}
```

Přidání definic telemetrie do šablony zařízení:

1. V yberte **Spravovat** rozhraní v šabloně **Zařízení hraničního zařízení senzoru prostředí.**

1. Vyberte **+ Přidat možnost**. Zadejte *počítač* jako **zobrazovaný název** a ujistěte se, že **typ Schopnost** je **Telemetrie**.

1. Jako typ schématu vyberte **Objekt** a pak vyberte **Definovat**. Na stránce definice objektu přidejte *teplotu* a *tlak* jako atributy typu **Double** a pak vyberte **Použít**.

1. Vyberte **+ Přidat možnost**. Zadejte *okolí* jako **zobrazovaný název** a ujistěte se, že **typ Schopnost** je **telemetrie**.

1. Jako typ schématu vyberte **Objekt** a pak vyberte **Definovat**. Na stránce definice objektu přidejte *teplotu* a *vlhkost* jako atributy typu **Double** a pak vyberte **Použít**.

1. Vyberte **+ Přidat možnost**. Zadejte *timeCreated* jako **zobrazovaný název** a ujistěte se, že **typ schopnosti** je **telemetrie**.

1. Jako typ schématu vyberte **DateTime.**

1. Chcete-li šablonu aktualizovat, vyberte **Uložit.**

Rozhraní **Manage** nyní obsahuje **typy telemetrie počítače**, **okolí**a **timeCreated:**

![Rozhraní s typy telemetrie strojů a okolí](./media/tutorial-add-edge-as-leaf-device/manage-interface.png)

### <a name="add-views-to-template"></a>Přidání zobrazení do šablony

Šablona zařízení ještě nemá zobrazení, které umožňuje operátoru zobrazit telemetrii ze zařízení IoT Edge. Přidání zobrazení do šablony zařízení:

1. V šabloně **Zařízení hraničního senzoru prostředí vyberte** **zobrazení.**

1. Na stránce **Vybrat pro přidání nového zobrazení** vyberte dlaždici **Vizualizace zařízení.**

1. Změňte název zobrazení na *Zobrazit telemetrii zařízení IoT Edge*.

1. Vyberte typy **telemetrie okolí** a **počítače.** Pak vyberte **Přidat dlaždici**.

1. Vyberte **Uložit,** chcete-li uložit zobrazení **telemetrie zařízení IoT Edge.**

![Šablona zařízení s telemetrickým zobrazením](./media/tutorial-add-edge-as-leaf-device/template-telemetry-view.png)

### <a name="publish-the-template"></a>Publikování šablony

Před přidáním zařízení, které používá šablonu **Zařízení hraničního zařízení senzoru prostředí,** je nutné šablonu publikovat.

Přejděte do šablony **Zařízení hraničního senzoru a** vyberte **Publikovat**. Pak vyberte **Publikovat,** chcete-li šablonu publikovat:

![Publikování šablony zařízení](./media/tutorial-add-edge-as-leaf-device/publish-template.png)

## <a name="add-iot-edge-device"></a>Přidání zařízení IoT Edge

Nyní, když jste publikovali šablonu **Zařízení hraničního zařízení senzoru prostředí,** můžete přidat zařízení do aplikace IoT Central:

1. V aplikaci IoT Central přejděte na stránku **Zařízení** a v seznamu dostupných šablon vyberte **zařízení Environmental Sensor Edge Device.**

1. Výběrem vyberte, **+** chcete-li ze šablony přidat nové zařízení. Na stránce **Vytvořit nové zařízení** vyberte **Vytvořit**.

Nyní máte nové zařízení se stavem **Registrováno**:

![Publikování šablony zařízení](./media/tutorial-add-edge-as-leaf-device/new-device.png)

### <a name="get-the-device-credentials"></a>Získání přihlašovacích údajů zařízení

Když nasadíte zařízení IoT Edge později v tomto kurzu, budete potřebovat pověření, která umožňují zařízení pro připojení k aplikaci IoT Central. Získejte přihlašovací údaje zařízení:

1. Na stránce **Zařízení** vyberte zařízení, které jste vytvořili.

1. Vyberte **Connect** (Připojit).

1. Na stránce **Připojení zařízení** poznamenejte si **obor ID**, **ID zařízení**a **primární klíč**. Tyto hodnoty můžete použít později.

1. Vyberte **Zavřít**.

Teď jste dokončili konfiguraci aplikace IoT Central tak, aby se zařízení IoT Edge mohlo připojit.

## <a name="deploy-an-iot-edge-device"></a>Nasazení zařízení IoT Edge

V tomto kurzu použijete virtuální počítač s Azure IoT Edge, vytvořený v Azure k simulaci zařízení IoT Edge. Vytvoření virtuálního zařízení s podporou IoT Edge:

1. Přejděte na [Azure IoT Edge na Ubuntu](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft_iot_edge.iot_edge_vm_ubuntu?tab=Overview) na Azure Marketplace. Pak vyberte **Získat nyní**.

1. Na stránce **Vytvořit tuto aplikaci v Azure** vyberte **Pokračovat**. Tento odkaz vás přenese na portál Azure, kde se možná budete muset přihlásit k předplatnému Azure.

1. Na stránce **Azure IoT Edge na Ubuntu na** webu Azure Portal vyberte **Vytvořit**.

1. Na stránce **Vytvořit virtuální počítač > základy:**

    - Vyberte své předplatné Azure.
    - Vytvořte novou skupinu prostředků nazvanou **zařízení s okrajem iot**.
    - Použijte název virtuálního počítače: **iotedgevm**.
    - Vyberte nejbližší oblast.
    - Nastavte typ ověřování na **heslo**.
    - Zvolte uživatelské jméno a heslo.
    - Ostatní možnosti můžete ponechat na jejich výchozích hodnotách.
    - Vyberte **Zkontrolovat a vytvořit**.

1. Po dokončení ověření vyberte **Vytvořit**.

Po několika minutách po dokončení nasazení vyberte **Přejít na prostředek**.

### <a name="provision-vm-as-an-iot-edge-device"></a>Zřízení virtuálního virtuálního zařízení jako zařízení IoT Edge 

Zřízení virtuálního virtuálního zařízení jako zařízení IoT Edge:

1. V části **Podpora + řešení potíží** vyberte možnost **Sériová konzola**.

1. Stisknutím **klávesy** `login:` Enter zobrazíte výzvu. Zadejte své uživatelské jméno a heslo pro přihlášení.

1. Chcete-li zkontrolovat runtime verzi ioT Edge, spusťte následující příkaz. V době psaní je verze 1.0.8:

    ```bash
    sudo iotedge --version
    ```

1. Pomocí `nano` editoru otevřete soubor config.yaml ioT Edge:

    ```bash
    sudo nano /etc/iotedge/config.yaml
    ```

1. Posuňte `# Manual provisioning configuration`se dolů, dokud se nezobrazí . Zakomentujte další tři řádky, jak je znázorněno v následujícím úryvku:

    ```yaml
    # Manual provisioning configuration
    #provisioning:
    #  source: "manual"
    #  device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
    ```

1. Posuňte `# DPS symmetric key provisioning configuration`se dolů, dokud se nezobrazí . Odkomentujte dalších osm řádků, jak je znázorněno v následujícím úryvku:

    ```yaml
    # DPS symmetric key provisioning configuration
    provisioning:
      source: "dps"
      global_endpoint: "https://global.azure-devices-provisioning.net"
      scope_id: "{scope_id}"
      attestation:
        method: "symmetric_key"
        registration_id: "{registration_id}"
        symmetric_key: "{symmetric_key}"
    ```

1. Nahraďte `{scope_id}` **rozsah id** pro vás udělal poznámku dříve.

1. Nahraďte `{registration_id}` **id zařízení,** které jste si dříve poznamenali.

1. Nahraďte `{symmetric_key}` **primárním klíčem,** který jste si dříve poznamenali.

1. Uložte změny (**Ctrl-O**) a ukončit (**Ctrl-X**) `nano` editor.

1. Spusťte následující příkaz a restartujte daemon IoT Edge:

    ```bash
    sudo systemctl restart iotedge
    ```

1. Chcete-li zkontrolovat stav modulů IoT Edge, spusťte následující příkaz:

    ```bash
    iotedge list
    ```

    Výstup vypadá takto:

    ```bash
    NAME                        STATUS           DESCRIPTION      CONFIG
    SimulatedTemperatureSensor  running          Up 20 seconds    mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0
    edgeAgent                   running          Up 27 seconds    mcr.microsoft.com/azureiotedge-agent:1.0
    edgeHub                     running          Up 22 seconds    mcr.microsoft.com/azureiotedge-hub:1.0
    ```

## <a name="view-the-telemetry"></a>Zobrazení telemetrie

Simulované zařízení IoT Edge je teď spuštěné ve virtuálním provozu. Ve vaší aplikaci IoT Central je teď stav zařízení **zřízený** na stránce **Zařízení:**

![Zřízené zařízení](./media/tutorial-add-edge-as-leaf-device/provisioned-device.png)

Telemetrická data se zobrazí na stránce **telemetrie zařízení View IoT Edge:**

![Telemetrie zařízení](./media/tutorial-add-edge-as-leaf-device/device-telemetry-view.png)

Na stránce **Moduly** se zobrazuje stav modulů IoT Edge:

![Telemetrie zařízení](./media/tutorial-add-edge-as-leaf-device/edge-module-status.png)

## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili pracovat se zařízeními IoT Edge a spravovat je v IoT Central, tady je další doporučený krok:

<!-- Next how-tos in the sequence -->

> [!div class="nextstepaction"]
> [Konfigurace transparentní brány](../../iot-edge/how-to-create-transparent-gateway.md)
