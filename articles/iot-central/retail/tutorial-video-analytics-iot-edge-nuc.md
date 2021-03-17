---
title: Kurz – vytvoření instance IoT Edge analýzy videí ve službě Azure IoT Central (Intel NUC)
description: V tomto kurzu se dozvíte, jak vytvořit instanci IoT Edge analýzy videí pro použití s šablonou aplikace pro video Analytics – objekt a pohyb.
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.author: nandab
author: KishorIoT
ms.date: 07/27/2020
ms.openlocfilehash: 64cdb41540d9750be8664dc60c2b6ceda6c324ca
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/08/2021
ms.locfileid: "99831922"
---
# <a name="tutorial-create-an-iot-edge-instance-for-video-analytics-intel-nuc"></a>Kurz: vytvoření instance IoT Edge pro video Analytics (Intel NUC)

Azure IoT Edge je plně spravovaná služba, která místně zajišťuje cloudovou logiku nasazením a spuštěním:

* Vlastní logika
* Služby Azure
* Umělá inteligence

V IoT Edge se tyto služby spouštějí přímo na zařízeních IoT pro různé platformy a umožňují bezpečné spuštění řešení IoT a škálování v cloudu nebo offline.

V tomto kurzu se dozvíte, jak nainstalovat a nakonfigurovat modul runtime IoT Edge na zařízení Intel NUC.

V tomto kurzu se naučíte:
> [!div class="checklist"]
> * Aktualizace a konfigurace IoT Edge
> * Nastavení brány IoT Edge
> * Připojit k zařízení Intel NUC místní fotoaparát kompatibilní s ONVIF

## <a name="prerequisites"></a>Požadavky

* Než začnete, měli byste provést předchozí [aplikaci Live video Analytics v azure IoT Central (Yolo V3)](./tutorial-video-analytics-create-app-yolo-v3.md) nebo [vytvořit video Analytics v kurzu Azure IoT Central (OpenVINO &trade; )](tutorial-video-analytics-create-app-openvino.md).
* Zařízení, jako je Intel NUC, na kterém běží Linux, který může spouštět kontejnery Docker a má dostatečné výpočetní výkon pro spuštění analýzy videí.
* V zařízení je nainstalovaný a spuštěný [modul runtime IoT Edge](../../iot-edge/how-to-install-iot-edge.md) .
* Je možné se připojit k zařízení IoT Edge z počítače s Windows, budete potřebovat [klienta ssh](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html) pro výstup nebo ekvivalentní nástroj.
* Budete také potřebovat předplatné Azure. Pokud předplatné Azure nemáte, můžete si ho na [stránce pro registraci k Azure](https://aka.ms/createazuresubscription)vytvořit zdarma.

## <a name="configure-the-iot-edge-device"></a>Konfigurace zařízení IoT Edge

Pokud ve svém počítači Intel NUC nemáte nainstalovaný modul runtime IoT Edge, přečtěte si téma [Instalace modulu runtime Azure IoT Edge v pokynech pro systémy Linux založené na Debian](../../iot-edge/how-to-install-iot-edge.md) .

Aktualizace modulu runtime IoT Edge:

1. Připojte se k zařízení IoT Edge pomocí nástroje pro výstup.

1. Spusťte následující příkazy, abyste aktualizovali a zkontrolovali verzi modulu runtime IoT Edge. V době psaní je verze 1.0.9:

    ```bash
    sudo apt-get update
    sudo apt-get install libiothsm iotedge
    sudo iotedge --version
    ```

1. Pomocí následujících příkazů vytvořte složky, které nasazení používá, s potřebnými oprávněními:

    ```bash
    sudo mkdir -p /data/storage
    sudo mkdir -p /data/media
    sudo chmod -R 777 /data
    ```

Přidání *state.js* do složky */data/storage* v zařízení IoT Edge:

1. Pomocí textového editoru otevřete *state.js* v souboru ve složce *lva-Configuration* na místním počítači.

1. `system`Zástupné symboly a aktualizujte `iotCentral > properties` pomocí řetězcových hodnot, které popisují vaše zařízení brány. Tyto hodnoty můžete zobrazit později v řídicím panelu aplikace IoT Central.

1. Aktualizujte `iotCentral > appKeys` zástupné symboly hodnotami, které jste si poznamenali v souboru *scratchpad.txt* v předchozím kurzu. Uložte změny.

1. Pomocí nástroje pro výstupy na `scp` příkazovém řádku zkopírujte *state.js* do souboru, který jste právě upravovali do složky */Data/Storage* na zařízení IoT Edge. Tento příklad používá `192.168.0.144` jako ukázkovou IP adresu, nahradí ji IP adresou vašeho zařízení IoT Edge:

    ```cmd
    scp state.json YourUserName@192.168.0.144:/data/storage/state.json`
    ```

Nakonfigurujte IoT Edge k registraci a připojení k aplikaci IoT Central:

1. Připojte se k zařízení IoT Edge pomocí nástroje pro výstup.

1. `nano`K otevření souboru IoT Edge config. yaml použijte textový editor, například.

    ```bash
    sudo nano /etc/iotedge/config.yaml
    ```

    > [!WARNING]
    > Soubory YAML nemůžou pro odsazení použít tabulátory, místo toho použijte dva mezery. Položky nejvyšší úrovně nemohou mít počáteční prázdné znaky.

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

1. Nahraďte `{scope_id}` **rozsahem ID** , který jste si poznamenali v souboru *scratchpad.txt* v předchozím kurzu.

1. Nahraďte `{registration_id}` *bránou-001* a zařízením, které jste vytvořili v předchozím kurzu.

1. Nahraďte `{symmetric_key}` **primárním klíčem** pro zařízení **Gateway – 001** , na které jste si poznamenali v souboru *scratchpad.txt* v předchozím kurzu.

1. Spusťte následující příkaz, který restartuje IoT Edge démon:

    ```bash
    sudo systemctl restart iotedge
    ```

1. Chcete-li zjistit stav modulů IoT Edge, spusťte následující příkaz:

    ```bash
    iotedge list
    ```

    Výstup z příkazu zkontrolují ukazuje pět spuštěných modulů. Stav spuštěných modulů můžete zobrazit také v aplikaci IoT Central.

    > [!TIP]
    > Můžete znovu spustit tento příkaz, abyste zkontrolovali stav. Možná budete muset počkat na spuštění všech modulů.

Pokud se moduly IoT Edge nespustí správně, přečtěte si téma [řešení potíží se zařízením IoT Edge](../../iot-edge/troubleshoot.md).

## <a name="collect-the-rtsp-stream-from-your-camera"></a>Shromažďovat datový proud RTSP z kamery

Identifikujte adresy URL datového proudu RTSP pro kamery připojené k vašemu IoT Edge zařízení, například:

`rtsp://192.168.1.64:554/Streaming/Channels/101/`

> [!TIP]
> Zkuste zobrazit datový proud kamery na IoT Edgeovém počítači pomocí přehrávače médií, jako je například VLC.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud jste s aplikací hotoví, můžete odebrat všechny prostředky, které jste vytvořili následujícím způsobem:

1. V aplikaci IoT Central přejděte na stránku **aplikace** v části **Správa** . Vyberte **Odstranit**.
1. V Azure Portal odstraňte skupinu prostředků **lva-RG** .
1. V místním počítači zastavte kontejner Docker pro **amp Viewer** .

## <a name="next-steps"></a>Další kroky

Nyní jste nasadili modul runtime IoT Edge a moduly LVA do zařízení brány Intel NUC.

Pokud chcete spravovat kamery, postupujte podle dalšího kurzu:

> [!div class="nextstepaction"]
> [Monitorování a Správa aplikace video Analytics – objekt a detekce pohybu](./tutorial-video-analytics-manage.md)