---
title: Nasazení bezstavové aplikace Kubernetes na GPU Azure Stack Edge pro pomocí IoT Edge modulu | Microsoft Docs
description: Popisuje, jak nasadit bezstavovou aplikaci Kubernetes na zařízení GPU Azure Stack Edge pro pomocí IoT Edge modulu, ke kterému se přistupoval prostřednictvím externí IP adresy.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 4eda6eaf69fad6f8600651660ae4ac6223fe8f8c
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/07/2021
ms.locfileid: "102438059"
---
# <a name="use-iot-edge-module-to-run-a-kubernetes-stateless-application-on-your-azure-stack-edge-pro-gpu-device"></a>Použití modulu IoT Edge ke spuštění bezstavové aplikace Kubernetes na zařízení GPU Azure Stack Edge pro

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Tento článek popisuje, jak můžete použít modul IoT Edge k nasazení bezstavové aplikace na zařízení Azure Stack Edge pro.

Pokud chcete nasadit bezstavovou aplikaci, proveďte následující kroky:

- Před nasazením modulu IoT Edge zajistěte, aby byly požadavky dokončeny.
- Přidejte modul IoT Edge pro přístup k výpočetní síti na Azure Stack Edge pro.
- Ověřte, zda má modul přístup k aktivovanému síťovému rozhraní.

V tomto článku s návodem k předvedení scénáře použijete modul aplikace webserver.

## <a name="prerequisites"></a>Požadavky

Než začnete, budete potřebovat:

- Zařízení Azure Stack Edge pro. Ujistěte se, že:

    - V zařízení jsou nakonfigurovaná nastavení výpočetního sítě.
    - Zařízení se aktivuje podle kroků v [kurzu: aktivace zařízení](azure-stack-edge-gpu-deploy-activate.md).
- Dokončili jste **konfiguraci výpočetního** kroku podle [kurzu: Konfigurace výpočetních prostředků na zařízení Azure Stack Edge pro](azure-stack-edge-gpu-deploy-configure-compute.md) na vašem zařízení. Vaše zařízení by mělo mít přidružený prostředek IoT Hub, zařízení IoT a IoT Edge zařízení.


## <a name="add-webserver-app-module"></a>Přidat modul aplikace pro webserver

Pokud chcete přidat modul aplikace webserver do zařízení Azure Stack Edge pro, proveďte následující kroky.

1. V IoT Hub prostředku, který je přidružený k vašemu zařízení, přejít na **Automatické řízení zařízení > IoT Edge**.
1. Vyberte a klikněte na zařízení IoT Edge přidružené k vašemu zařízení Azure Stack Edge pro. 

    ![Vybrat IoT Edge zařízení](media/azure-stack-edge-gpu-deploy-stateless-application-iot-edge-module/select-iot-edge-device-1.png)  

1. Vyberte **Set modules** (Nastavit moduly). V **Nastavení moduly na zařízení** vyberte **+ Přidat** a pak vyberte **IoT Edge modul**.

    ![Vybrat IoT Edge modul](media/azure-stack-edge-gpu-deploy-stateless-application-iot-edge-module/select-iot-edge-module-1.png)

1. V **modulu přidat IoT Edge**:

    1. Zadejte **název** modulu aplikace webserver, který chcete nasadit.
    2. Na kartě **nastavení modulu** zadejte **identifikátor URI image** pro Image modulu. Načte se modul, který odpovídá zadanému názvu a značkám. V takovém případě `nginx:stable` načte stabilní image Nginx (označená jako stabilní) z veřejného [úložiště Docker](https://hub.docker.com/_/nginx/).

        ![Přidat modul IoT Edge](media/azure-stack-edge-gpu-deploy-stateless-application-iot-edge-module/set-module-settings-1.png)    

    3. Na kartě **možnosti vytvoření kontejneru** vložte následující vzorový kód:  

        ```
        {
            "HostConfig": {
                "PortBindings": {
                    "80/tcp": [
                        {
                            "HostPort": "8080"
                        }
                    ]
                }
            }
        }
        ```

        Tato konfigurace umožňuje přístup k modulu pomocí výpočetní síťové IP adresy přes *http* na portu TCP 8080 (výchozí port serveru WebServer je 80). Vyberte **Přidat**.

        ![Zadání informací o portu v okně IoT Edge vlastní modul](media/azure-stack-edge-gpu-deploy-stateless-application-iot-edge-module/verify-module-status-1.png)

    4. Vyberte **Zkontrolovat a vytvořit**. Prohlédněte si podrobnosti o modulu a vyberte **vytvořit**.

## <a name="verify-module-access"></a>Ověřit přístup k modulu

1. Ověřte, že je modul úspěšně nasazený a běží. Na kartě **moduly** by měl být **spuštěn** běhový stav modulu.  

    ![Ověřte, že je spuštěný stav modulu.](media/azure-stack-edge-gpu-deploy-stateless-application-iot-edge-module/verify-module-status-1.png)

1. Pokud chcete získat externí koncový bod aplikace webserver, [přejděte na řídicí panel Kubernetes](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md#access-dashboard). 
1. V levém podokně řídicího panelu filtrovat podle oboru názvů **iotedge** . Přejít na **zjišťování a vyrovnávání zatížení > služby**. V seznamu služeb, které jsou uvedené, vyhledejte externí koncový bod pro modul aplikace webserver App. 

    ![Připojení k aplikaci WebServer na externím koncovém bodu](media/azure-stack-edge-gpu-deploy-stateless-application-iot-edge-module/connect-external-endpoint-1.png)

1. Vyberte externí koncový bod a otevřete tak nové okno prohlížeče.

    Měla by se zobrazit, že je spuštěná aplikace na serveru.

    ![Ověřit připojení k modulu přes zadaný port](media/azure-stack-edge-gpu-deploy-stateless-application-iot-edge-module/verify-webserver-app-1.png)

## <a name="next-steps"></a>Další kroky

- Zjistěte, jak zobrazit stavovou aplikaci pomocí modulu IoT Edge.<!--insert link-->.
