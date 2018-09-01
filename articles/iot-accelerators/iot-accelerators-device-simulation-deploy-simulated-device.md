---
title: Nasazení vlastní simulované zařízení – Azure IoT | Dokumentace Microsoftu
description: Tato příručka ukazuje, jak nasadit vlastní simulovaného zařízení k akcelerátoru řešení simulace zařízení.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 08/14/2018
ms.topic: conceptual
ms.openlocfilehash: e51d0330c3ed804bff8cdb06265bb8c39141733f
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/31/2018
ms.locfileid: "43345148"
---
# <a name="deploy-a-new-simulated-device"></a>Nasazení nového simulovaného zařízení

Vzdálené monitorování a simulace zařízení akcelerátory řešení oba umožňují definovat simulovaných zařízení. Tento článek ukazuje, jak nasadit vlastní chladič typ zařízení a nového typu zařízení žárovky akcelerátor řešení simulace zařízení.

Kroky v tomto článku předpokládají, že jste dokončili [vytvoření a testování nového simulovaného zařízení](iot-accelerators-remote-monitoring-create-simulated-device.md) postupy průvodce a soubory, které definují přizpůsobené chladič a nové typy zařízení žárovky.

Kroky v této příručce s postupy ukazují, jak do:

1. Použití SSH pro přístup k systému souborů virtuálního počítače, který je hostitelem akcelerátor řešení simulaci zařízení.

1. Konfigurace Dockeru se načíst modely zařízení z umístění mimo kontejner Dockeru.

1. Spuštění simulace použití vlastního modelu souborů.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

K dokončení kroků v této příručce s postupy, budete potřebovat aktivní předplatné Azure.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Tato příručka, budete potřebovat:

- Nasazená instance [akcelerátor řešení simulace zařízení](https://www.azureiotsolutions.com/Accelerators#solutions/types/DS).
- Místní **bash** prostředí ke spuštění `ssh` a `scp` příkazy. Na Windows, snadný způsob, jak nainstalovat **bash** je instalace [git](https://git-scm.com/download/win).
- Soubory modelu vlastní zařízení, jako jsou ty, které jsou popsané v [vytvoření a testování nového simulovaného zařízení](iot-accelerators-remote-monitoring-create-simulated-device.md).

[!INCLUDE [iot-solution-accelerators-access-vm](../../includes/iot-solution-accelerators-access-vm.md)]

## <a name="configure-docker"></a>Konfigurace Dockeru

V této části nakonfigurujete Docker načíst soubory modelu zařízení z **/tmp/devicemodels** složky ve virtuálním počítači, spíše než z uvnitř kontejneru Docker. Spusťte příkazy v tomto oddílu **bash** skořápce na místním počítači:

1. Pomocí SSH se připojte k virtuálnímu počítači v Azure z místního počítače. Následující příkaz předpokládá veřejnou IP adresu virtuálního počítače **vm vikxv** je **104.41.128.108** --tuto hodnotu nahraďte veřejnou IP adresu vašeho virtuálního počítače z předchozí části:

   ```sh
    ssh azureuser@104.41.128.108
    ```

    Postupujte podle pokynů se přihlaste k virtuálnímu počítači s heslem, které jste nastavili v předchozím oddílu.

1. Konfigurace služby simulace zařízení se načíst modely zařízení z vně kontejneru. Nejprve otevřete konfigurační soubor Docker:

    ```sh
    sudo nano /app/docker-compose.yml
    ```

    Vyhledejte nastavení **devicesimulation** kontejneru a úpravy **svazky** nastavení, jak je znázorněno v následujícím fragmentu kódu:

    ```yml
    # To mount custom device models, upload the files to the VM, edit and uncomment the following line:
          - /tmp/devicemodels:/app/webservice/data/devicemodels:ro
    # To mount a custom service configuration, create a custom file, edit and uncomment the following line:
    #     - /app/custom-device-simulation-appsettings.ini:/app/webservice/appsettings.ini:ro
    ```

    Uložte změny.

1. Vytvoření složky pro uložení souborů JSON a skriptu:

    ```sh
    sudo mkdir -p /tmp/devicemodels/scripts
    ```

    Zachovat **bash** okno s otevřít relaci SSH.

1. Zkopírujte soubory modelu vlastní zařízení k virtuálnímu počítači. Spusťte tento příkaz v jiném **bash** skořápce na počítači, ve které jste vytvořili své vlastní zařízení modely. Nejprve přejděte do místní složky, která obsahuje soubory JSON model zařízení. Tyto příkazy předpokládají veřejnou IP adresu virtuálního počítače je **104.41.128.108** --tuto hodnotu nahraďte veřejnou IP adresu vašeho virtuálního počítače. Zadejte své heslo virtuálního počítače po zobrazení výzvy:

    ```sh
    scp *json azureuser@104.41.128.108:/tmp/devicemodels
    cd scripts
    scp *js azureuser@104.41.128.108:/tmp/devicemodels/scripts
    ```

1. Kontejner Dockeru simulace zařízení, aby používal nové modely zařízení restartujte. Spuštěním následujících příkazů **bash** skořápce s otevřít relaci SSH k virtuálnímu počítači:

    ```sh
    sudo /app/start.sh
    ```

    Pokud chcete zobrazit stav spuštěné kontejnery Docker a jejich ID kontejneru, použijte následující příkaz:

    ```sh
    docker ps
    ```

    Pokud chcete zobrazit v protokolu z kontejneru simulace zařízení, spusťte následující příkaz. ID kontejneru nahraďte ID vašeho kontejneru simulaci zařízení:

    ```sh
    docker logs -f 5d3f3e78822e
    ```

## <a name="run-simulation"></a>Spustit simulaci

Teď můžete spouštět simulace použití modelů vlastní zařízení:

1. Spuštění simulace vaše zařízení webové uživatelské rozhraní z [akcelerátory řešení IoT Microsoft Azure](https://www.azureiotsolutions.com/Accelerators#dashboard).

1. Pomocí webového uživatelského rozhraní pro konfiguraci a spuštění simulace pomocí jedné z vašich modelů vlastní zařízení.

1. Když spustíte simulaci, klikněte na tlačítko **metriky zobrazení IoT Hub na webu Azure Portal** monitorování simulace. Alternativně můžete následující příkaz rozhraní příkazového řádku Azure k monitorování zařízení do cloudu provoz. Nahraďte názvem služby IoT hub, název vašeho centra:

    ```azurecli-interactive
    az iot hub monitor-events -n contoso-simulation9dc75
    ```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete pokračovat v prozkoumávání, ponechte akcelerátor řešení simulace zařízení nasazený.

Pokud akcelerátor řešení už nepotřebujete, odstraňte ho z [zřídili řešení](https://www.azureiotsolutions.com/Accelerators#dashboard) stránky, že ji vyberete a pak kliknutím na **odstranit řešení**.

## <a name="next-steps"></a>Další postup

Tato příručka vám ukázali, jak nasazovat modely vlastní zařízení k akcelerátoru řešení simulaci zařízení. Navrhované dalším krokem je další informace o [schématu modelu zařízení](iot-accelerators-device-simulation-device-schema.md).
