---
title: Nasazení vlastních simulovaných zařízení ioT – Azure | Dokumenty společnosti Microsoft
description: Tento návod vám ukáže, jak nasadit vlastní simulovaná zařízení do akcelerátoru řešení vzdáleného monitorování.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 08/15/2018
ms.topic: conceptual
ms.openlocfilehash: 7cbab38db859935c9f4490d79a131d6c9a7e302b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "66427563"
---
# <a name="deploy-a-new-simulated-device"></a>Nasazení nového simulovaného zařízení

Akcelerátory řešení Vzdálené monitorování a Simulace zařízení umožňují definovat vlastní simulovaná zařízení. Tento článek ukazuje, jak nasadit vlastní typ chladicího zařízení a nový typ zařízení žárovky do akcelerátoru řešení vzdáleného monitorování.

Kroky v tomto článku předpokládají, že jste dokončili [vytvořit a otestovat nové simulované zařízení](iot-accelerators-remote-monitoring-create-simulated-device.md) návod a mají soubory, které definují vlastní chladič a nové typy zařízení žárovky.

Postup v tomto návodu vám ukáže, jak:

1. Pomocí SSH můžete přistupovat k systému souborů virtuálního počítače, který je hostitelem akcelerátoru řešení vzdáleného monitorování.

1. Nakonfigurujte Docker k načtení modelů zařízení z umístění mimo kontejner Dockeru.

1. Spusťte akcelerátor řešení vzdáleného monitorování pomocí vlastních souborů modelu zařízení.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

K dokončení kroků v tomto návodu, budete potřebovat aktivní předplatné Azure.

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

## <a name="prerequisites"></a>Požadavky

Chcete-li postupovat podle tohoto návodu, potřebujete:

- Nasazená instance [akcelerátoru řešení vzdáleného monitorování](https://www.azureiotsolutions.com/Accelerators#solutions/types/RM2).
- Místní **bash** shell pro `ssh` `scp` spuštění příkazů a. V systému Windows, snadný způsob, jak nainstalovat **bash** je nainstalovat [git](https://git-scm.com/download/win).
- Vlastní soubory modelu zařízení, například ty, které jsou popsány v [části Vytvoření a testování nového simulovaného zařízení](iot-accelerators-remote-monitoring-create-simulated-device.md).

[!INCLUDE [iot-solution-accelerators-access-vm](../../includes/iot-solution-accelerators-access-vm.md)]

## <a name="configure-docker"></a>Konfigurace Dockeru

V této části nakonfigurujete Docker tak, aby načítat soubory modelu zařízení ze složky **/tmp/devicemodels** ve virtuálním počítači, nikoli z kontejneru Dockeru. Spusťte příkazy v této části v **bash** shellu na místním počítači:

V této části nakonfigurujete Docker tak, aby načítat soubory modelu zařízení ze složky **/tmp/devicemodels** ve virtuálním počítači, nikoli z kontejneru Dockeru. Spusťte příkazy v této části v **bash** shellu na místním počítači:

1. Pomocí SSH se můžete připojit k virtuálnímu počítači v Azure z místního počítače. Následující příkaz předpokládá, že veřejná IP adresa virtuálního počítače **vm-vikxv** je **104.41.128.108** -- nahraďte tuto hodnotu veřejnou IP adresou vašeho virtuálního počítače z předchozí části:

   ```sh
    ssh azureuser@104.41.128.108
    ```

    Podle pokynů se přihlaste k virtuálnímu počítači pomocí hesla, které jste nastavili v předchozí části.

1. Nakonfigurujte simulační službu zařízení tak, aby načítaly modely zařízení mimo kontejner. Nejprve otevřete konfigurační soubor Dockeru:

    ```sh
    sudo nano /app/docker-compose.yml
    ```

    Vyhledejte nastavení kontejneru **simulace zařízení** a upravte nastavení **svazků,** jak je znázorněno v následujícím fragmentu:

    ```yml
    devicesimulation:
      image: azureiotpcs/device-simulation-dotnet:1.0.0
      networks:
        - default_net
      depends_on:
        - storageadapter
      environment:
        - PCS_KEYVAULT_NAME
        - PCS_AAD_APPID
        - PCS_AAD_APPSECRET
      # How one could mount custom device models
      volumes:
        - /tmp/devicemodels:/app/webservice/data/devicemodels:ro
    ```

    Uložte změny.

1. Zkopírujte existující soubory modelu zařízení z kontejneru do nového umístění. Nejprve vyhledejte ID kontejneru pro kontejner simulace zařízení:

    ```sh
    sudo docker ps
    ```

    Potom zkopírujte soubory modelu zařízení do složky **tmp** ve virtuálním počítači. Následující příkaz předpokládá, že ID kontejneru je c378d6878407 – nahraďte tuto hodnotu ID kontejneru simulace zařízení:

    ```sh
    sudo docker cp c378d6878407:/app/webservice/data/devicemodels /tmp
    sudo chown -R azureuser /tmp/devicemodels/
    ```

    Nechte **bash** okno s vaší relace SSH otevřené.

1. Zkopírujte soubory vlastního modelu zařízení do virtuálního počítače. Spusťte tento příkaz v jiném **bash** shellu v počítači, kde jste vytvořili vlastní modely zařízení. Nejprve přejděte do místní složky, která obsahuje soubory JSON modelu zařízení. Následující příkazy předpokládají, že veřejná IP adresa virtuálního počítače je **104.41.128.108** -- nahraďte tuto hodnotu veřejnou IP adresou vašeho virtuálního počítače. Po zobrazení výzvy zadejte heslo virtuálního počítače:

    ```sh
    scp *json azureuser@104.41.128.108:/tmp/devicemodels
    cd scripts
    scp *js azureuser@104.41.128.108:/tmp/devicemodels/scripts
    ```

1. Restartujte kontejner Dockeru simulace zařízení a použijte nové modely zařízení. Spusťte následující příkazy v **bash** shellu s otevřenou relací SSH do virtuálního počítače:

    ```sh
    sudo /app/start.sh
    ```

    Pokud chcete zobrazit stav spuštěných kontejnerů Dockeru a jejich ID kontejnerů, použijte následující příkaz:

    ```sh
    sudo docker ps
    ```

    Pokud chcete zobrazit protokol z kontejneru simulace zařízení, spusťte následující příkaz. Nahraďte ID kontejneru ID id kontejneru simulace zařízení:

    ```sh
    sudo docker logs -f 5d3f3e78822e
    ```

## <a name="run-simulation"></a>Spustit simulaci

Nyní můžete použít vlastní modely zařízení v řešení vzdáleného monitorování:

1. Spusťte řídicí panel vzdáleného monitorování z [akcelerátorů řešení IoT Microsoft Azure](https://www.azureiotsolutions.com/Accelerators#dashboard).

1. Pomocí stránky **Zařízení** můžete přidat simulovaná zařízení. Když přidáte nové simulované zařízení, budou k dispozici nové modely zařízení.

1. Řídicí panel můžete použít k zobrazení telemetrie zařízení a metody volání zařízení.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud máte v plánu prozkoumat další, ponechte akcelerátor řešení vzdáleného monitorování nasazený.

Pokud už akcelerátor řešení nepotřebujete, odstraňte ho ze stránky [Zřízená řešení,](https://www.azureiotsolutions.com/Accelerators#dashboard) vyberete ho a kliknete na **Odstranit řešení**.

## <a name="next-steps"></a>Další kroky

Tato příručka vám ukázala, jak nasadit vlastní modely zařízení do akcelerátoru řešení vzdáleného monitorování. Dalším navrhovaným krokem je naučit se [připojit skutečné zařízení k řešení vzdáleného monitorování](iot-accelerators-connecting-devices-node.md).
