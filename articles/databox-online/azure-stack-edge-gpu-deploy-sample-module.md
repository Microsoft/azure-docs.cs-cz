---
title: Nasadit modul GPU na zařízení s grafickým procesorem Azure Stack Edge pro | Microsoft Docs
description: V této části najdete popis postupu povolení výpočetní služby a zajištění, aby vaše zařízení Azure Stack Edge pro bylo připravené pomocí místního uživatelského rozhraní.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/31/2020
ms.author: alkohli
ms.openlocfilehash: 1f16ef0ede25f17acb915a7812ae5b15b45f78a4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90899726"
---
# <a name="deploy-a-gpu-enabled-iot-module-on-azure-stack-edge-pro-gpu-device"></a>Nasazení modulu IoT s povoleným GPU na zařízení s grafickým procesorem Azure Stack Edge pro

Tento článek popisuje, jak nasadit modul IoT Edge s povoleným grafickým procesorem na zařízení GPU Azure Stack Edge pro. 

V tomto článku získáte informace o těchto tématech:
  - Připravte Azure Stack Edge pro pro spuštění modulu GPU.
  - Stáhněte a nainstalujte si vzorový kód z úložiště Git.
  - Sestavte řešení a vygenerujte manifest nasazení.
  - Nasaďte řešení do Azure Stack Edge pro zařízení.
  - Monitorujte výstup modulu.


## <a name="about-sample-module"></a>O ukázkovém modulu

Vzorový modul GPU v tomto článku zahrnuje vzorový kód PyTorch a TensorFlow srovnávacího testu pro procesor s grafickým procesorem.

## <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, že máte následující:

- Máte přístup k zařízení s povoleným grafickým procesorem s jedním uzlem Azure Stack Edge pro. Toto zařízení je aktivované pomocí prostředku v Azure. Viz [Aktivace zařízení](azure-stack-edge-gpu-deploy-activate.md).
- Na tomto zařízení jste nakonfigurovali výpočetní výkon. Postupujte podle kroků v [kurzu: Konfigurace výpočetních prostředků na zařízení Azure Stack Edge pro](azure-stack-edge-gpu-deploy-configure-compute.md).
- Azure Container Registry (ACR). Přejděte do okna **přístupové klíče** a poznamenejte si přihlašovací server ACR, uživatelské jméno a heslo. Další informace najdete na stránce [rychlý Start: Vytvoření privátního registru kontejnerů pomocí Azure Portal](../container-registry/container-registry-get-started-portal.md#create-a-container-registry).
- Následující prostředky pro vývoj na klientovi Windows:
    - [Azure CLI 2,0 nebo novější](https://aka.ms/installazurecliwindows)
    - [Docker CE](https://store.docker.com/editions/community/docker-ce-desktop-windows). Možná budete muset vytvořit účet ke stažení a instalaci softwaru.
    - [Visual Studio Code](https://code.visualstudio.com/)  
    - [Azure IoT Edge rozšíření pro Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge)    
    - [Rozšíření Pythonu pro Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-python.python)    
    - [Python 3](https://www.python.org/)    
    - PIP pro instalaci balíčků Pythonu (obvykle zahrnutých v instalaci Pythonu)

## <a name="get-the-sample-code"></a>Získání ukázkového kódu

1. [V ukázkách Azure se dostanete do vzorů inteligentního Edge Azure](https://github.com/azure-samples/azure-intelligent-edge-patterns). Naklonujte nebo Stáhněte soubor zip pro kód. 

    ![Stáhnout soubor zip](media/azure-stack-edge-gpu-deploy-sample-module/download-zip-file-1.png)

    Extrahujte soubory ze souboru ZIP. Můžete také klonovat ukázky.

    ```json
    git clone https://github.com/Azure-Samples/azure-intelligent-edge-patterns.git
    ```

## <a name="build-and-deploy-module"></a>Sestavit a nasadit modul

1. Otevřete složku **GpuReferenceModules** v Visual Studio Code.

    ![Otevřete GPUReferenceModules v VS Code](media/azure-stack-edge-gpu-deploy-sample-module/open-folder-gpu-sample-1.png)

2. Otevřete *deployment.template.js* a Identifikujte parametry, na které odkazuje registr kontejneru. V tomto souboru se používají CONTAINER_REGISTRY_USERNAME, CONTAINER_REGISTRY_PASSWORD a CONTAINER_REGISTRY_NAME.

    ```json
        {
      "$schema-template": "2.0.0",
      "modulesContent": {
        "$edgeAgent": {
          "properties.desired": {
            "schemaVersion": "1.0",
            "runtime": {
              "type": "docker",
              "settings": {
                "minDockerVersion": "v1.25",
                "loggingOptions": "",
                "registryCredentials": {
                  "${CONTAINER_REGISTRY_NAME}":{
                  "username": "$CONTAINER_REGISTRY_USERNAME",
                  "password": "$CONTAINER_REGISTRY_PASSWORD",
                  "address": "${CONTAINER_REGISTRY_NAME}.azurecr.io"
                  }
                }
              }
            },
    ```
3. Vytvoří nový soubor. Vyplňte hodnoty pro parametry registru kontejnerů (použijte ty, které jste identifikovali v předchozím kroku) následujícím způsobem: 

    ```json
    CONTAINER_REGISTRY_NAME=<YourContainerRegistryName>
    CONTAINER_REGISTRY_USERNAME=<YourContainerRegistryUserName>
    CONTAINER_REGISTRY_PASSWORD=<YourContainerRegistryPassword>
    ```
    Ukázkový soubor *. env* je zobrazený níže:
    
    ![Vytvoření a uložení souboru. env](media/azure-stack-edge-gpu-deploy-sample-module/create-save-env-file-1.png)

4. Uložte soubor jako soubor *. env* do složky **SampleSolution** .

5. Pokud se chcete přihlásit k Docker, zadejte do Visual Studio Code integrovaného terminálu následující příkaz. 

    ```json
    docker login -u <CONTAINER_REGISTRY_USERNAME> -p <CONTAINER_REGISTRY_PASSWORD> <CONTAINER_REGISTRY_NAME>
    ```
    V Azure Portal přejděte do části **přístupové klíče** registru kontejneru. Zkopírujte a použijte název registru, heslo a přihlašovací server.

    ![Přístupové klíče ve vašem registru kontejneru](media/azure-stack-edge-gpu-deploy-sample-module/container-registry-access-keys-1.png)

    Po zadání přihlašovacích údajů se přihlášení zdaří.

    ![Úspěšné přihlášení](media/azure-stack-edge-gpu-deploy-sample-module/successful-sign-in-1.png)

6. Nahrajte image do služby Azure Container Registry. V Průzkumníku VS Code vyberte a klikněte pravým tlačítkem na **deployment.template.jsna** soubor a pak vyberte **sestavení a nabízené IoT Edge řešení**. 

    ![Sestavování a nabízených IoT Edge řešení](media/azure-stack-edge-gpu-deploy-sample-module/build-push-iot-edge-solution-1.png)   

    Pokud nejsou nainstalované rozšíření Python a Python, budou se instalovat při sestavení a nabízení řešení. To ale by vedlo k delší době sestavení. 

    Po dokončení tohoto kroku se zobrazí modul ve vašem registru kontejneru.

    ![Modul v registru kontejnerů](media/azure-stack-edge-gpu-deploy-sample-module/module-container-registry-1.png)    


7. Chcete-li vytvořit manifest nasazení, klikněte pravým tlačítkem myši na **deployment.template.js** a pak vyberte **generovat manifest nasazení IoT Edge**. 

    ![Generovat manifest nasazení IoT Edge](media/azure-stack-edge-gpu-deploy-sample-module/generate-iot-edge-deployment-manifest-1.png)  

    Oznámení informuje o cestě, ve které byl vygenerován manifest nasazení. Manifest je `deployment.amd64.json` soubor vygenerovaný ve složce **config** . 

8. Vyberte **deployment.amd64.js** v souboru ve složce **config** a pak zvolte **vytvořit nasazení pro jedno zařízení**. Nepoužívejte **deployment.template.jsv** souboru. 

    ![Vytvoření nasazení pro jedno zařízení](media/azure-stack-edge-gpu-deploy-sample-module/create-deployment-single-device-1.png)  

    V okně **výstup** by se měla zobrazit zpráva, že nasazení proběhlo úspěšně.

    ![Nasazení bylo úspěšné ve výstupu.](media/azure-stack-edge-gpu-deploy-sample-module/deployment-succeeded-output-1.png) 

## <a name="monitor-the-module"></a>Monitorovat modul  

1. Na paletě příkazů VS Code spusťte **Azure IoT Hub: Select IoT Hub** (Azure IoT Hub: Vybrat IoT Hub).

2. Zvolte předplatné a centrum IoT obsahující zařízení IoT Edge, které chcete nakonfigurovat. V takovém případě vyberte předplatné, které se používá k nasazení Azure Stack Edge pro zařízení, a vyberte IoT Edge zařízení, které jste vytvořili pro zařízení Azure Stack Edge pro. K tomu dochází při konfiguraci výpočetní kapacity prostřednictvím Azure Portal v předchozích krocích.

3. V Průzkumníku VS Code rozbalte část Azure IoT Hub. V části **zařízení**by se mělo zobrazit zařízení IoT Edge odpovídající vašemu zařízení Azure Stack Edge pro. 

    1. Vyberte toto zařízení, klikněte pravým tlačítkem myši a vyberte možnost **Spustit sledování vestavěného koncového bodu události**.
  
        ![Spustit monitorování](media/azure-stack-edge-gpu-deploy-sample-module/monitor-builtin-event-endpoint-1.png)  

    2. Přejděte na **zařízení > moduly** a měli byste vidět, že je spuštěný **modul GPU** .

        ![Modul v IoT Hub](media/azure-stack-edge-gpu-deploy-sample-module/module-iot-hub-1.png)  

    3. VS Code terminál by měl také zobrazit IoT Hub události jako výstup monitorování pro zařízení Azure Stack Edge pro.

        ![Monitorování výstupu](media/azure-stack-edge-gpu-deploy-sample-module/monitor-events-output-1.png) 

        Zjistíte, že čas potřebný k provedení stejné sady operací (5000 iterací transformace obrazce) procesorem GPU je větší než procesor.

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o tom, jak [nakonfigurovat GPU pro použití modulu](azure-stack-edge-j-series-configure-gpu-modules.md).
