---
title: Nasazení vlastní simulované zařízení – Azure IoT | Dokumentace Microsoftu
description: Tato příručka ukazuje, jak nasadit vlastní simulovaného zařízení k akcelerátoru řešení vzdáleného monitorování.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 08/15/2018
ms.topic: conceptual
ms.openlocfilehash: f073637810e9ed1acdf37b0e541ca3f1d518de2a
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/31/2018
ms.locfileid: "43345244"
---
# <a name="deploy-a-new-simulated-device"></a>Nasazení nového simulovaného zařízení

Vzdálené monitorování a simulace zařízení akcelerátory řešení oba umožňují definovat simulovaných zařízení. Tento článek ukazuje, jak nasadit vlastní chladič typ zařízení a nového typu zařízení žárovky akcelerátor řešení vzdálené monitorování.

Kroky v tomto článku předpokládají, že jste dokončili [vytvoření a testování nového simulovaného zařízení](iot-accelerators-remote-monitoring-create-simulated-device.md) postupy průvodce a soubory, které definují přizpůsobené chladič a nové typy zařízení žárovky.

Kroky v této příručce s postupy ukazují, jak do:

1. Použití SSH pro přístup k systému souborů virtuálního počítače, který je hostitelem akcelerátoru řešení vzdáleného monitorování.

1. Konfigurace Dockeru se načíst modely zařízení z umístění mimo kontejner Dockeru.

1. Spuštění pomocí vlastního modelu souborů akcelerátoru řešení vzdáleného monitorování.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

K dokončení kroků v této příručce s postupy, budete potřebovat aktivní předplatné Azure.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Tato příručka, budete potřebovat:

- Nasazená instance [akcelerátor řešení vzdálené monitorování](https://www.azureiotsolutions.com/Accelerators#solutions/types/RM2).
- Místní **bash** prostředí ke spuštění `ssh` a `scp` příkazy. Na Windows, snadný způsob, jak nainstalovat **bash** je instalace [git](https://git-scm.com/download/win).
- Soubory modelu vlastní zařízení, jako jsou ty, které jsou popsané v [vytvoření a testování nového simulovaného zařízení](iot-accelerators-remote-monitoring-create-simulated-device.md).

[!INCLUDE [iot-solution-accelerators-access-vm](../../includes/iot-solution-accelerators-access-vm.md)]

## <a name="configure-docker"></a>Konfigurace Dockeru

V této části nakonfigurujete Docker načíst soubory modelu zařízení z **/tmp/devicemodels** složky ve virtuálním počítači, spíše než z uvnitř kontejneru Docker. Spusťte příkazy v tomto oddílu **bash** skořápce na místním počítači:

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
    devicesimulation:
      image: azureiotpcs/device-simulation-dotnet:1.0.0
      networks:
        - default_net
      depends_on:
        - storageadapter
      environment:
        - PCS_IOTHUB_CONNSTRING
        - PCS_STORAGEADAPTER_WEBSERVICE_URL=http://storageadapter:9022/v1
        - PCS_AUTH_ISSUER
        - PCS_AUTH_AUDIENCE
        - PCS_AUTH_REQUIRED
        - PCS_CORS_WHITELIST
        - PCS_APPLICATION_SECRET
      # How one could mount custom device models
      volumes:
        - /tmp/devicemodels:/app/webservice/data/devicemodels:ro
    ```

    Uložte změny.

1. Zkopíruje existující soubory modelu zařízení z kontejneru do nového umístění. Nejdříve vyhledejte ID kontejneru pro kontejner simulaci zařízení:

    ```sh
    docker ps
    ```

    Zkopírujte soubory model zařízení, které chcete **tmp** složky ve virtuálním počítači. Následující příkaz předpokládá ID kontejneru je c378d6878407 – tuto hodnotu nahraďte ID vašeho zařízení simulace kontejneru:

    ```sh
    docker cp c378d6878407:/app/webservice/data/devicemodels /tmp
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

Teď můžete své modely vlastních zařízení v řešení vzdáleného monitorování:

1. Spuštění vzdálené monitorování řídicího panelu z [akcelerátory řešení IoT Microsoft Azure](https://www.azureiotsolutions.com/Accelerators#dashboard).

1. Použití **zařízení** stránky a přidat simulované zařízení. Při přidání nového simulovaného zařízení, jsou vaše nové modely zařízení si můžete vybrat.

1. Zobrazení telemetrie zařízení a volat metody zařízení mohou pomocí řídicího panelu.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud budete chtít dále zkoumat, ponechte nasazení akcelerátoru řešení vzdáleného monitorování.

Pokud akcelerátor řešení už nepotřebujete, odstraňte ho z [zřídili řešení](https://www.azureiotsolutions.com/Accelerators#dashboard) stránky, že ji vyberete a pak kliknutím na **odstranit řešení**.

## <a name="next-steps"></a>Další postup

Tato příručka vám ukázali, jak nasadit modely vlastní zařízení k akcelerátoru řešení vzdáleného monitorování. Navrhované dalším krokem je další způsob [připojení fyzických zařízení k řešení vzdáleného monitorování](iot-accelerators-connecting-devices-node.md).
