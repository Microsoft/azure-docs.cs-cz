---
title: Vlastní simulovaná zařízení ve službě IoT Deploy – Azure | Microsoft Docs
description: V této příručce se dozvíte, jak nasadit vlastní simulovaná zařízení do akcelerátoru řešení vzdáleného monitorování.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 08/15/2018
ms.topic: conceptual
ms.openlocfilehash: 7cbab38db859935c9f4490d79a131d6c9a7e302b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "66427563"
---
# <a name="deploy-a-new-simulated-device"></a>Nasazení nového simulovaného zařízení

Akcelerátory řešení vzdáleného monitorování a simulace zařízení umožňují definovat vaše vlastní simulovaná zařízení. V tomto článku se dozvíte, jak nasadit přizpůsobený typ chladicího zařízení a nový typ zařízení žárovky do akcelerátoru řešení vzdáleného monitorování.

V krocích v tomto článku se předpokládá, že jste dokončili Průvodce [vytvořením a otestováním nového simulovaného zařízení](iot-accelerators-remote-monitoring-create-simulated-device.md) s postupem a máte soubory definující přizpůsobený chladicí a nové typy zařízení žárovky.

Postup v tomto průvodci vám ukáže, jak:

1. Použijte SSH pro přístup k systému souborů virtuálního počítače, který je hostitelem akcelerátoru řešení vzdáleného monitorování.

1. Nakonfigurujte Docker tak, aby načetl modely zařízení z umístění mimo kontejner Docker.

1. Spusťte akcelerátor řešení vzdáleného monitorování pomocí vlastních souborů modelu zařízení.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

K dokončení kroků v tomto průvodci, potřebujete aktivní předplatné Azure.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Pokud chcete postupovat podle tohoto návodu, budete potřebovat:

- Nasazená instance [akcelerátoru řešení vzdáleného monitorování](https://www.azureiotsolutions.com/Accelerators#solutions/types/RM2).
- Místní prostředí **bash** pro spuštění `ssh` `scp` příkazů a. V systému Windows je snadný způsob, jak nainstalovat **bash** , je nainstalovat [Git](https://git-scm.com/download/win).
- Vlastní soubory modelu zařízení, například ty, které jsou popsány v tématu [Vytvoření a otestování nového simulovaného zařízení](iot-accelerators-remote-monitoring-create-simulated-device.md).

[!INCLUDE [iot-solution-accelerators-access-vm](../../includes/iot-solution-accelerators-access-vm.md)]

## <a name="configure-docker"></a>Konfigurovat Docker

V této části nakonfigurujete Docker tak, aby načetl soubory modelů zařízení ze složky **/TMP/DeviceModels** ve virtuálním počítači místo zevnitř kontejneru Docker. Spusťte příkazy v této části v **bash** prostředí v místním počítači:

V této části nakonfigurujete Docker tak, aby načetl soubory modelů zařízení ze složky **/TMP/DeviceModels** ve virtuálním počítači místo zevnitř kontejneru Docker. Spusťte příkazy v této části v **bash** prostředí v místním počítači:

1. Pomocí SSH se připojte k virtuálnímu počítači v Azure z místního počítače. Následující příkaz předpokládá, že veřejná IP adresa virtuálního počítače **VM-vikxv** je **104.41.128.108** – nahraďte tuto hodnotu veřejnou IP adresou vašeho virtuálního počítače z předchozí části:

   ```sh
    ssh azureuser@104.41.128.108
    ```

    Podle zobrazených výzev se přihlaste k virtuálnímu počítači pomocí hesla, které jste nastavili v předchozí části.

1. Nakonfigurujte službu pro simulaci zařízení, aby se načetly modely zařízení mimo kontejner. Nejdřív otevřete konfigurační soubor Docker:

    ```sh
    sudo nano /app/docker-compose.yml
    ```

    Vyhledejte nastavení pro kontejner **devicesimulation** a upravte nastavení **svazků** , jak je znázorněno v následujícím fragmentu kódu:

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

1. Zkopírujte existující soubory modelu zařízení z kontejneru do nového umístění. Nejdřív vyhledejte ID kontejneru pro kontejner simulace zařízení:

    ```sh
    sudo docker ps
    ```

    Pak zkopírujte soubory modelu zařízení do složky **TMP** ve virtuálním počítači. Následující příkaz předpokládá, že ID kontejneru je c378d6878407 – nahraďte tuto hodnotu ID kontejneru pro simulaci zařízení:

    ```sh
    sudo docker cp c378d6878407:/app/webservice/data/devicemodels /tmp
    sudo chown -R azureuser /tmp/devicemodels/
    ```

    Nechte okno **bash** v otevřené relaci SSH.

1. Zkopírujte vlastní soubory modelu zařízení do virtuálního počítače. Spusťte tento příkaz v jiném prostředí **bash** na počítači, ve kterém jste vytvořili vlastní modely zařízení. Nejprve přejděte do místní složky, která obsahuje soubory JSON modelu zařízení. V následujících příkazech se předpokládá, že veřejná IP adresa virtuálního počítače je **104.41.128.108** – nahraďte tuto hodnotu veřejnou IP adresou vašeho virtuálního počítače. Po zobrazení výzvy zadejte heslo k virtuálnímu počítači:

    ```sh
    scp *json azureuser@104.41.128.108:/tmp/devicemodels
    cd scripts
    scp *js azureuser@104.41.128.108:/tmp/devicemodels/scripts
    ```

1. Pokud chcete používat nové modely zařízení, restartujte kontejner Docker pro simulaci zařízení. V prostředí **bash** spusťte následující příkazy s otevřenou relací SSH k virtuálnímu počítači:

    ```sh
    sudo /app/start.sh
    ```

    Pokud chcete zobrazit stav spuštěných kontejnerů Docker a jejich ID kontejnerů, použijte následující příkaz:

    ```sh
    sudo docker ps
    ```

    Pokud chcete zobrazit protokol z kontejneru simulace zařízení, spusťte následující příkaz. Nahraďte ID kontejneru ID kontejneru simulace zařízení:

    ```sh
    sudo docker logs -f 5d3f3e78822e
    ```

## <a name="run-simulation"></a>Spustit simulaci

V řešení vzdáleného monitorování teď můžete používat vlastní modely zařízení:

1. Spusťte řídicí panel vzdáleného monitorování z [Microsoft Azure akcelerátory řešení IoT](https://www.azureiotsolutions.com/Accelerators#dashboard).

1. Na stránce **zařízení** můžete přidat simulovaná zařízení. Když přidáte nové simulované zařízení, budou dostupné nové modely zařízení, které si můžete vybrat.

1. Řídicí panel můžete použít k zobrazení telemetrie zařízení a volání metod zařízení.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud plánujete prozkoumat další, ponechte nasazený akcelerátor řešení vzdáleného monitorování.

Pokud už akcelerátor řešení nepotřebujete, odstraňte ho ze stránky [zřízené řešení](https://www.azureiotsolutions.com/Accelerators#dashboard) , a to tak, že ho vyberete a pak kliknete na **Odstranit řešení**.

## <a name="next-steps"></a>Další kroky

Tato příručka vám ukázala, jak nasadit vlastní modely zařízení do akcelerátoru řešení vzdáleného monitorování. Navržený další krok se naučíte [připojit reálné zařízení k řešení vzdáleného monitorování](iot-accelerators-connecting-devices-node.md).
