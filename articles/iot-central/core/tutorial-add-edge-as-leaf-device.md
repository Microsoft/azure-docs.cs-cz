---
title: Přidání zařízení Azure IoT Edge do Azure IoT Central | Microsoft Docs
description: Jako operátor přidejte do aplikace Azure IoT Central Azure IoT Edge zařízení.
author: rangv
ms.author: rangv
ms.date: 12/09/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: c60cf4b90b089d271c0ccd91031420efe9017b1e
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "81758158"
---
# <a name="tutorial-add-an-azure-iot-edge-device-to-your-azure-iot-central-application"></a>Kurz: Přidání zařízení Azure IoT Edge do aplikace Azure IoT Central

*Tento článek se týká tvůrců řešení a vývojářů zařízení.*

V tomto kurzu se dozvíte, jak nakonfigurovat a přidat zařízení Azure IoT Edge do aplikace Azure IoT Central. V tomto kurzu se k simulaci IoT Edge zařízení používá virtuální počítač Linux s podporou IoT Edge (VM) z Azure Marketplace. Zařízení IoT Edge používá modul, který generuje simulovanou telemetrii o životním prostředí. Telemetrii můžete zobrazit na řídicím panelu v aplikaci IoT Central.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření šablony zařízení pro IoT Edge zařízení
> * Vytvoření zařízení IoT Edge v IoT Central
> * Nasazení simulovaného zařízení IoT Edge do virtuálního počítače se systémem Linux

## <a name="prerequisites"></a>Požadavky

Dokončete průvodce [vytvořením aplikace Azure IoT Central](./quick-deploy-iot-central.md) pro vytvoření IoT Central aplikace pomocí vlastní šablony **aplikace > vlastní** .

K dokončení kroků v tomto kurzu potřebujete aktivní předplatné Azure.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

Stáhněte soubor manifestu IoT Edge z GitHubu. Klikněte pravým tlačítkem na následující odkaz a vyberte **Uložit odkaz jako**: [EnvironmentalSensorManifest. JSON.](https://raw.githubusercontent.com/Azure-Samples/iot-central-docs-samples/master/iotedge/EnvironmentalSensorManifest.json)

## <a name="create-device-template"></a>Vytvořit šablonu zařízení

V této části vytvoříte šablonu zařízení pro IoT Edge zařízení, které se připojuje k vaší aplikaci IoT Central. Naimportujete IoT Edge manifest, abyste mohli začít, a pak upravíte šablonu pro přidání definic a zobrazení telemetrie:

### <a name="import-manifest-to-create-template"></a>Importovat manifest pro vytvoření šablony

Vytvoření šablony zařízení z IoT Edge manifestu:

1. V aplikaci IoT Central přejděte na **šablony zařízení** a vyberte **+ Nový**.

1. Na stránce **Vybrat typ šablony** vyberte dlaždici **Azure IoT Edge** . Pak vyberte **Další: přizpůsobit**.

1. Na stránce **nahrát manifest nasazení Azure IoT Edge** vyberte **Procházet** a nahrajte **EnvironmentalSensorManifest. JSON** , který jste předtím stáhli. Pak vyberte **Další: zkontrolovat**.

1. Na stránce **Kontrola** vyberte **vytvořit**.

1. Po vytvoření šablony změňte její název na *hraniční zařízení snímače prostředí*.

1. V modulu **SimulatedTemperatureSensor** vyberte rozhraní pro **správu** , aby se zobrazily tyto dvě vlastnosti definované v manifestu:

![Šablona zařízení vytvořená z manifestu IoT Edge](./media/tutorial-add-edge-as-leaf-device/imported-manifest.png)

### <a name="add-telemetry-to-manifest"></a>Přidat telemetrii do manifestu

Manifest IoT Edge nedefinuje telemetrii, kterou modul odesílá. Do šablony zařízení je nutné přidat definice telemetrie. Modul **SimulatedTemperatureSensor** odesílá zprávy telemetrie, které vypadají jako následující JSON:

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

1. V šabloně **zařízení hraničního senzoru pro životní prostředí** vyberte rozhraní pro **správu** .

1. Vyberte **+ Přidat možnost**. Jako **Zobrazovaný název** zadejte *počítač* a ujistěte se, že je **typ schopnosti** **telemetrie**.

1. Jako typ schématu vyberte **objekt** a potom vyberte **definovat**. Na stránce definice objektu přidejte *teplotu* a *tlak* jako atributy typu **Double** a pak vyberte **použít**.

1. Vyberte **+ Přidat možnost**. Jako **Zobrazovaný název** zadejte *okolí* a ujistěte se, že je **typ schopnosti** **telemetrie**.

1. Jako typ schématu vyberte **objekt** a potom vyberte **definovat**. Na stránce definice objektu přidejte *teplotu* a *vlhkost* jako atributy typu **Double** a pak vyberte **použít**.

1. Vyberte **+ Přidat možnost**. Jako **Zobrazovaný název** zadejte *timeCreated* a ujistěte se, že je **typ schopnosti** **telemetrie**.

1. Jako typ schématu vyberte **DateTime** .

1. Vyberte **Uložit** a aktualizujte šablonu.

Rozhraní pro **správu** teď zahrnuje typy telemetrie **počítačů**, **ambientních**a **timeCreated** :

![Rozhraní s typy telemetrie počítačů a okolí](./media/tutorial-add-edge-as-leaf-device/manage-interface.png)

### <a name="add-views-to-template"></a>Přidat zobrazení do šablony

Šablona zařízení ještě nemá zobrazení, které umožňuje operátorovi zobrazit telemetrii ze zařízení IoT Edge. Přidání zobrazení do šablony zařízení:

1. V šabloně **zařízení Edge pro senzory prostředí** vyberte **zobrazení** .

1. Na stránce **Vyberte, pokud chcete přidat nové zobrazení** vyberte dlaždici **zařízení** .

1. Změňte název zobrazení tak, aby se *zobrazila IoT Edge telemetrie zařízení*.

1. Vyberte typy telemetrie **okolí** a **počítače** . Pak vyberte **Přidat dlaždici**.

1. Vyberte **Uložit** a uložte **zobrazení IoT Edge zobrazení telemetrie zařízení** .

![Šablona zařízení s zobrazením telemetrie](./media/tutorial-add-edge-as-leaf-device/template-telemetry-view.png)

### <a name="publish-the-template"></a>Publikování šablony

Než budete moct přidat zařízení, které používá šablonu **zařízení Edge pro senzory prostředí** , je nutné šablonu publikovat.

Přejděte do šablony **zařízení Edge pro senzory prostředí** a vyberte **publikovat**. Pak vyberte **publikovat** a publikujte šablonu:

![Publikování šablony zařízení](./media/tutorial-add-edge-as-leaf-device/publish-template.png)

## <a name="add-iot-edge-device"></a>Přidat IoT Edge zařízení

Teď jste publikovali šablonu **zařízení hraničního senzoru pro životní prostředí** , můžete do své aplikace IoT Central přidat zařízení:

1. V aplikaci IoT Central přejděte na stránku **zařízení** a v seznamu dostupných šablon vyberte **zařízení Edge pro senzory prostředí** .

1. Tuto **+** možnost vyberte, pokud chcete přidat nové zařízení ze šablony. Na stránce **vytvořit nové zařízení** vyberte **vytvořit**.

Nyní máte nové zařízení se **zaregistrovaným**stavem:

![Publikování šablony zařízení](./media/tutorial-add-edge-as-leaf-device/new-device.png)

### <a name="get-the-device-credentials"></a>Získání přihlašovacích údajů zařízení

Když později v tomto kurzu nasadíte IoT Edge zařízení, budete potřebovat přihlašovací údaje, které zařízení umožní připojit se k vaší aplikaci IoT Central. Přihlašovací údaje pro získání zařízení:

1. Na stránce **zařízení** vyberte zařízení, které jste vytvořili.

1. Vyberte **Connect** (Připojit).

1. Na stránce **připojení zařízení** si poznamenejte **Rozsah ID**, **ID zařízení**a **primární klíč**. Tyto hodnoty použijete později.

1. Vyberte **Zavřít**.

Nyní jste dokončili konfiguraci aplikace IoT Central, aby bylo možné zařízení IoT Edge připojit.

## <a name="deploy-an-iot-edge-device"></a>Nasazení zařízení IoT Edge

V tomto kurzu použijete virtuální počítač se systémem Linux Azure IoT Edge, který se vytvoří v Azure a simuluje IoT Edge zařízení. Vytvoření virtuálního počítače s povolenou IoT Edge:

1. V Azure Marketplace přejděte na [Azure IoT Edge v Ubuntu](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft_iot_edge.iot_edge_vm_ubuntu?tab=Overview) . Pak vyberte **získat hned**.

1. Na stránce **vytvořit tuto aplikaci na Azure** vyberte **pokračovat**. Tento odkaz vás přesměruje na Azure Portal, kde se možná budete muset přihlásit ke svému předplatnému Azure.

1. Na stránce **Azure IoT Edge na Ubuntu** v Azure Portal vyberte **vytvořit**.

1. Na stránce **Vytvoření základu > pro virtuální počítač** :

    - Vyberte své předplatné Azure.
    - Vytvořte novou skupinu prostředků s názvem **IoT-Edge-Devices**.
    - Použijte název virtuálního počítače: **iotedgevm**.
    - Vyberte nejbližší oblast.
    - Nastavte typ ověřování na **heslo**.
    - Vyberte uživatelské jméno a heslo.
    - Ostatní možnosti můžete ponechat na jejich výchozích hodnotách.
    - Vyberte **Zkontrolovat a vytvořit**.

1. Po dokončení ověření vyberte **vytvořit**.

Po několika minutách po dokončení nasazení vyberte **Přejít k prostředku**.

### <a name="provision-vm-as-an-iot-edge-device"></a>Zřízení virtuálního počítače jako zařízení IoT Edge 

Zřízení virtuálního počítače jako zařízení IoT Edge:

1. V části **Podpora a řešení potíží** vyberte **sériová konzola**.

1. Po **Enter** stisknutí klávesy ENTER `login:` se zobrazí výzva. Zadejte své uživatelské jméno a heslo pro přihlášení.

1. Spusťte následující příkaz, který zkontroluje IoT Edge verzi modulu runtime. V době psaní je verze 1.0.8:

    ```bash
    sudo iotedge --version
    ```

1. Pomocí `nano` editoru otevřete soubor IoT Edge config. yaml:

    ```bash
    sudo nano /etc/iotedge/config.yaml
    ```

1. Posuňte se dolů, `# Manual provisioning configuration`dokud se nezobrazí. Odkomentujte následující tři řádky, jak je znázorněno v následujícím fragmentu kódu:

    ```yaml
    # Manual provisioning configuration
    #provisioning:
    #  source: "manual"
    #  device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
    ```

1. Posuňte se dolů, `# DPS symmetric key provisioning configuration`dokud se nezobrazí. Odkomentujte následující osm řádků, jak je znázorněno v následujícím fragmentu kódu:

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

1. Nahraďte `{scope_id}` **rozsahem ID** , který jste si poznamenali dříve.

1. Nahraďte `{registration_id}` **ID zařízení** , které jste si poznamenali dříve.

1. Nahraďte `{symmetric_key}` **primárním klíčem** , který jste si poznamenali dříve.

1. Uložte změny (**CTRL-O**) a ukončete `nano` Editor (**CTRL-X**).

1. Spusťte následující příkaz, který restartuje IoT Edge démon:

    ```bash
    sudo systemctl restart iotedge
    ```

1. Chcete-li zjistit stav modulů IoT Edge, spusťte následující příkaz:

    ```bash
    iotedge list
    ```

    Výstup bude vypadat nějak takto:

    ```bash
    NAME                        STATUS           DESCRIPTION      CONFIG
    SimulatedTemperatureSensor  running          Up 20 seconds    mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0
    edgeAgent                   running          Up 27 seconds    mcr.microsoft.com/azureiotedge-agent:1.0
    edgeHub                     running          Up 22 seconds    mcr.microsoft.com/azureiotedge-hub:1.0
    ```

## <a name="view-the-telemetry"></a>Zobrazit telemetrii

Simulované IoT Edge zařízení teď běží na virtuálním počítači. Ve vaší aplikaci IoT Central je stav zařízení nyní **zřízený** na stránce **zařízení** :

![Zřízené zařízení](./media/tutorial-add-edge-as-leaf-device/provisioned-device.png)

Telemetrii můžete zobrazit na stránce **zobrazení IoT Edge telemetrie zařízení** :

![Telemetrie zařízení](./media/tutorial-add-edge-as-leaf-device/device-telemetry-view.png)

Na stránce **moduly** se zobrazuje stav IoT Edgech modulů:

![Telemetrie zařízení](./media/tutorial-add-edge-as-leaf-device/edge-module-status.png)

## <a name="next-steps"></a>Další kroky

Jako vývojář zařízení teď, když jste se naučili pracovat se zařízeními IoT Edge a spravovat je v IoT Central, je navržený další krok Přečtěte si:

<!-- Next how-tos in the sequence -->

> [!div class="nextstepaction"]
> [Připojte se k Azure IoT Central](./concepts-get-connected.md)
