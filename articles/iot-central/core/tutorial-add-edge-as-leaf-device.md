---
title: Kurz – přidání zařízení Azure IoT Edge do Azure IoT Central | Microsoft Docs
description: Kurz – jako operátor přidání zařízení Azure IoT Edge do aplikace Azure IoT Central
author: rangv
ms.author: rangv
ms.date: 05/29/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom:
- mvc
- device-developer
- iot-edge
ms.openlocfilehash: 373d144b4df818a075f0088e9cbf31cb5027e747
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "101724876"
---
# <a name="tutorial-add-an-azure-iot-edge-device-to-your-azure-iot-central-application"></a>Kurz: Přidání zařízení Azure IoT Edge do aplikace Azure IoT Central

*Tento článek se týká operátorů, tvůrců řešení a vývojářů zařízení.*

V tomto kurzu se dozvíte, jak nakonfigurovat a přidat zařízení Azure IoT Edge do aplikace Azure IoT Central. V tomto kurzu se používá virtuální počítač se systémem Linux IoT Edge k simulaci IoT Edge zařízení. Zařízení IoT Edge používá modul, který generuje simulovanou telemetrii o životním prostředí. Telemetrii můžete zobrazit na řídicím panelu v aplikaci IoT Central.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření šablony zařízení pro IoT Edge zařízení
> * Vytvoření zařízení IoT Edge v IoT Central
> * Nasazení simulovaného zařízení IoT Edge do virtuálního počítače se systémem Linux

## <a name="prerequisites"></a>Předpoklady

Dokončete průvodce [vytvořením aplikace Azure IoT Central](./quick-deploy-iot-central.md) pro vytvoření IoT Central aplikace pomocí vlastní šablony **aplikace > vlastní** .

K dokončení kroků v tomto kurzu potřebujete aktivní předplatné Azure.

Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

Stáhněte soubor manifestu IoT Edge z GitHubu. Klikněte pravým tlačítkem na následující odkaz a vyberte **Uložit odkaz jako**: [EnvironmentalSensorManifest.jszapnuto](https://raw.githubusercontent.com/Azure-Samples/iot-central-docs-samples/master/iotedge/EnvironmentalSensorManifest.json) .

## <a name="create-device-template"></a>Vytvořit šablonu zařízení

V této části vytvoříte IoT Central šablonu zařízení pro IoT Edge zařízení. Naimportujete IoT Edge manifest, abyste mohli začít, a pak upravíte šablonu pro přidání definic a zobrazení telemetrie:

### <a name="import-manifest-to-create-template"></a>Importovat manifest pro vytvoření šablony

Vytvoření šablony zařízení z IoT Edge manifestu:

1. V aplikaci IoT Central přejděte na **šablony zařízení** a vyberte **+ Nový**.

1. Na stránce **Vybrat typ šablony** vyberte dlaždici **Azure IoT Edge** . Pak vyberte **Další: přizpůsobit**.

1. Na stránce **nahrát manifest nasazení Azure IoT Edge** jako název šablony zařízení zadejte *hraniční zařízení snímače prostředí* . Pak vyberte **Procházet** a nahrajte **EnvironmentalSensorManifest.js** , který jste předtím stáhli. Pak vyberte **Další: zkontrolovat**.

1. Na stránce **Kontrola** vyberte **vytvořit**.

1. V modulu **SimulatedTemperatureSensor** vyberte rozhraní pro **správu** , aby se zobrazily tyto dvě vlastnosti definované v manifestu:

:::image type="content" source="media/tutorial-add-edge-as-leaf-device/imported-manifest.png" alt-text="Šablona zařízení vytvořená z manifestu IoT Edge":::

> [!TIP]
> Tento manifest nasazení vyžádá image modulu z Azure Container Registry úložiště, které nevyžaduje žádné přihlašovací údaje pro připojení. Pokud chcete použít image modulu z privátního úložiště, nastavte přihlašovací údaje registru kontejneru v manifestu.

### <a name="add-telemetry-to-manifest"></a>Přidat telemetrii do manifestu

Manifest IoT Edge nedefinuje telemetrii, kterou modul odesílá. Do šablony zařízení v IoT Central přidáte definice telemetrie. Modul **SimulatedTemperatureSensor** odesílá zprávy telemetrie, které vypadají jako následující JSON:

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

Rozhraní pro **správu** teď zahrnuje typy telemetrie **počítačů**, **ambientních** a **timeCreated** :

:::image type="content" source="media/tutorial-add-edge-as-leaf-device/manage-interface.png" alt-text="Rozhraní s typy telemetrie počítačů a okolí":::

### <a name="add-views-to-template"></a>Přidat zobrazení do šablony

Šablona zařízení ještě nemá zobrazení, které umožňuje operátorovi zobrazit telemetrii ze zařízení IoT Edge. Přidání zobrazení do šablony zařízení:

1. V šabloně **zařízení Edge pro senzory prostředí** vyberte **zobrazení** .

1. Na stránce **Vyberte, pokud chcete přidat nové zobrazení** vyberte dlaždici **zařízení** .

1. Změňte název zobrazení tak, aby se *zobrazila IoT Edge telemetrie zařízení*.

1. Vyberte typy telemetrie **okolí** a **počítače** . Pak vyberte **Přidat dlaždici**.

1. Vyberte **Uložit** a uložte **zobrazení IoT Edge zobrazení telemetrie zařízení** .

:::image type="content" source="media/tutorial-add-edge-as-leaf-device/template-telemetry-view.png" alt-text="Šablona zařízení s zobrazením telemetrie":::

### <a name="publish-the-template"></a>Publikování šablony

Než budete moct přidat zařízení, které používá šablonu **zařízení Edge pro senzory prostředí** , je nutné šablonu publikovat.

Přejděte do šablony **zařízení Edge pro senzory prostředí** a vyberte **publikovat**. V části **Publikovat tuto šablonu zařízení na panelu aplikace** vyberte **publikovat** a publikujte šablonu:

:::image type="content" source="media/tutorial-add-edge-as-leaf-device/publish-template.png" alt-text="Publikování šablony zařízení":::

## <a name="add-iot-edge-device"></a>Přidat IoT Edge zařízení

Teď jste publikovali šablonu **zařízení hraničního senzoru pro životní prostředí** , můžete do své aplikace IoT Central přidat zařízení:

1. V aplikaci IoT Central přejděte na stránku **zařízení** a v seznamu dostupných šablon vyberte **zařízení Edge pro senzory prostředí** .

1. Vyberte **+ Nová** a přidejte ze šablony nové zařízení. Na stránce **vytvořit nové zařízení** vyberte **vytvořit**.

Nyní máte nové zařízení se **zaregistrovaným** stavem:

:::image type="content" source="media/tutorial-add-edge-as-leaf-device/new-device.png" alt-text="Nové, registrované zařízení":::

### <a name="get-the-device-credentials"></a>Získání přihlašovacích údajů zařízení

Když později v tomto kurzu nasadíte IoT Edge zařízení, budete potřebovat přihlašovací údaje, které zařízení umožní připojit se k vaší aplikaci IoT Central. Přihlašovací údaje pro získání zařízení:

1. Na stránce **zařízení** vyberte zařízení, které jste vytvořili.

1. Vyberte **Connect** (Připojit).

1. Na stránce **připojení zařízení** si poznamenejte **Rozsah ID**, **ID zařízení** a **primární klíč**. Tyto hodnoty použijete později.

1. Vyberte **Zavřít**.

Nyní jste dokončili konfiguraci aplikace IoT Central, aby bylo možné zařízení IoT Edge připojit.

## <a name="deploy-an-iot-edge-device"></a>Nasazení zařízení IoT Edge

V tomto kurzu použijete virtuální počítač se systémem Linux Azure IoT Edge, který se vytvoří v Azure a simuluje IoT Edge zařízení. Pokud chcete vytvořit virtuální počítač s povolenou IoT Edge ve vašem předplatném Azure, klikněte na:

[![Tlačítko Nasazení do Azure pro iotedge-vm-deploy](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fazure%2Fiotedge-vm-deploy%2Fmaster%2FedgeDeploy.json)

Na stránce **vlastní nasazení** :

1. Vyberte své předplatné Azure.

1. Vyberte **vytvořit nový** a vytvořte novou skupinu prostředků s názvem *Central-Edge-RG*.

1. Vyberte oblast, která je blízko vás.

1. Přidejte jedinečnou **předponu popisku DNS** , jako je *Contoso-Central-Edge*.

1. Vyberte uživatelské jméno správce pro virtuální počítač.

1. Jako připojovací řetězec zadejte *TEMP* . Později nakonfigurujete zařízení tak, aby se připojovalo pomocí DPS.

1. Přijměte výchozí hodnoty velikosti virtuálního počítače, verze Ubuntu a umístění.

1. Jako typ ověřování vyberte **heslo** .

1. Zadejte heslo pro virtuální počítač.

1. Pak vyberte **zkontrolovat + vytvořit**.

1. Zkontrolujte volby a pak vyberte **vytvořit**:

    :::image type="content" source="media/tutorial-add-edge-as-leaf-device/vm-deployment.png" alt-text="Vytvoření virtuálního počítače s IoT Edge":::

Dokončení nasazení trvá několik minut. Po dokončení nasazení přejděte do skupiny prostředků **centrálního Edge-RG** v Azure Portal.

### <a name="configure-the-iot-edge-vm"></a>Konfigurace virtuálního počítače s IoT Edge

Konfigurace IoT Edge na virtuálním počítači pro použití DPS k registraci a připojení k vaší IoT Central aplikaci:

1. Ve skupině prostředků **Contoso-Edge-RG** vyberte instanci virtuálního počítače.

1. V části **Podpora a řešení potíží** vyberte **sériová konzola**. Pokud budete vyzváni ke konfiguraci diagnostiky spouštění, postupujte podle pokynů na portálu.

1. Po stisknutí klávesy **ENTER** se zobrazí `login:` výzva. Zadejte své uživatelské jméno a heslo pro přihlášení.

1. Spusťte následující příkaz, který zkontroluje IoT Edge verzi modulu runtime. V době psaní je verze 1.0.9.1:

    ```bash
    sudo iotedge --version
    ```

1. Pomocí `nano` editoru otevřete soubor IoT Edge config. yaml:

    ```bash
    sudo nano /etc/iotedge/config.yaml
    ```

1. Posuňte se dolů, dokud se nezobrazí `# Manual provisioning configuration` . Odkomentujte následující tři řádky, jak je znázorněno v následujícím fragmentu kódu:

    ```yaml
    # Manual provisioning configuration
    #provisioning:
    #  source: "manual"
    #  device_connection_string: "temp"
    ```

1. Posuňte se dolů, dokud se nezobrazí `# DPS symmetric key provisioning configuration` . Odkomentujte následující osm řádků, jak je znázorněno v následujícím fragmentu kódu:

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

    > [!TIP]
    > Zajistěte, aby před ním zůstala žádná mezera. `provisioning:`

1. Nahraďte `{scope_id}` **rozsahem ID** , který jste si poznamenali dříve.

1. Nahraďte `{registration_id}` **ID zařízení** , které jste si poznamenali dříve.

1. Nahraďte `{symmetric_key}` **primárním klíčem** , který jste si poznamenali dříve.

1. Uložte změny (**CTRL-O**) a ukončete Editor (**CTRL-X**) `nano` .

1. Spusťte následující příkaz, který restartuje IoT Edge démon:

    ```bash
    sudo systemctl restart iotedge
    ```

1. Chcete-li zjistit stav modulů IoT Edge, spusťte následující příkaz:

    ```bash
    iotedge list
    ```

    Následující vzorový výstup zobrazuje spuštěné moduly:

    ```bash
    NAME                        STATUS           DESCRIPTION      CONFIG
    SimulatedTemperatureSensor  running          Up 20 seconds    mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0
    edgeAgent                   running          Up 27 seconds    mcr.microsoft.com/azureiotedge-agent:1.0
    edgeHub                     running          Up 22 seconds    mcr.microsoft.com/azureiotedge-hub:1.0
    ```

    > [!TIP]
    > Možná budete muset počkat na spuštění všech modulů.

## <a name="view-the-telemetry"></a>Zobrazení telemetrie

Simulované IoT Edge zařízení teď běží na virtuálním počítači. Ve vaší aplikaci IoT Central je stav zařízení nyní **zřízený** na stránce **zařízení** :

:::image type="content" source="media/tutorial-add-edge-as-leaf-device/provisioned-device.png" alt-text="Zřízené IoT Edge zařízení":::

Telemetrii můžete zobrazit ze zařízení na stránce **zobrazení IoT Edge telemetrie zařízení** :

:::image type="content" source="media/tutorial-add-edge-as-leaf-device/device-telemetry-view.png" alt-text="Telemetrie zařízení":::

Na stránce **moduly** se zobrazuje stav IoT Edge modulů na zařízení:

:::image type="content" source="media/tutorial-add-edge-as-leaf-device/edge-module-status.png" alt-text="Stav modulu zařízení":::

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud máte v úmyslu pokračovat v práci s IoT Edgem virtuálním počítačem, můžete všechny prostředky, které jste použili v tomto kurzu, zachovat a znovu použít. V opačném případě můžete odstranit prostředky, které jste vytvořili v tomto kurzu, abyste se vyhnuli dalším poplatkům:

* Pokud chcete odstranit IoT Edge virtuální počítač a jeho přidružené prostředky, odstraňte v Azure Portal skupinu prostředků **Contoso-Edge-RG** .
* Chcete-li odstranit aplikaci IoT Central, přejděte na stránku **aplikace** v části **Správa** aplikace a vyberte možnost **Odstranit**.

Jako vývojář nebo operátor řešení teď, když jste se seznámili s tím, jak pracovat s IoT Edge zařízení v IoT Central, je navržený další krok:

> [!div class="nextstepaction"]
> [Použití skupin zařízení k analýze telemetrie zařízení](./tutorial-use-device-groups.md)

## <a name="next-steps"></a>Další kroky

Jako vývojář zařízení teď, když jste se naučili pracovat se zařízeními IoT Edge a spravovat je v IoT Central, je navržený další krok Přečtěte si:

> [!div class="nextstepaction"]
> [Vývoj IoT Edgech modulů](../../iot-edge/tutorial-develop-for-linux.md)