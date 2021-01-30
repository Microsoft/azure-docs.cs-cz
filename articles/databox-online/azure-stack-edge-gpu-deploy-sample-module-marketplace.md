---
title: Nasadit modul GPU na zařízení Microsoft Azure Stack Edge pro ze Azure Marketplace | Microsoft Docs
description: Popisuje, jak nasadit modul IoT s povoleným grafickým procesorem na zařízení s grafickým procesorem Azure Stack Edge pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 01/28/2021
ms.author: alkohli
ms.openlocfilehash: a2c46e8a7cae9ddba9606abf75ac022e804fde9c
ms.sourcegitcommit: 1a98b3f91663484920a747d75500f6d70a6cb2ba
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2021
ms.locfileid: "99062492"
---
# <a name="deploy-a-gpu-enabled-iot-module-from-azure-marketplace-on-azure-stack-edge-pro-gpu-device"></a>Nasazení modulu IoT s povoleným grafickým procesorem z Azure Marketplace na zařízení GPU pro Azure Stack Edge pro

Tento článek popisuje, jak nasadit modul IoT Edge s grafickým procesorem (GPU), který je povolený, z Azure Marketplace na zařízení Azure Stack Edge pro. 

V tomto článku získáte informace o těchto tématech:
  - Připravte Azure Stack Edge pro pro spuštění modulu GPU.
  - Stáhněte a nasaďte modul IoT s povoleným GPU z Azure Marketplace.
  - Monitorujte výstup modulu.

## <a name="about-sample-module"></a>O ukázkovém modulu

Vzorový modul GPU v tomto článku zahrnuje vzorový kód PyTorch a TensorFlow srovnávacího testu pro procesor s grafickým procesorem.

## <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, že máte následující:

- Máte přístup k zařízení s povoleným grafickým procesorem s jedním uzlem Azure Stack Edge. Toto zařízení se aktivuje pomocí prostředku v Azure. 
- Na tomto zařízení jste nakonfigurovali výpočetní výkon. Postupujte podle kroků v [kurzu: Konfigurace výpočtů na zařízení Azure Stack Edge](azure-stack-edge-gpu-deploy-configure-compute.md).
- Následující prostředky pro vývoj na klientovi Windows:
    - [Visual Studio Code](https://code.visualstudio.com/)  
    - [Azure IoT Edge rozšíření pro Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge)   


## <a name="get-module-from-azure-marketplace"></a>Získat modul z Azure Marketplace

1. Procházet všechny [aplikace v Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps).

    ![Procházet aplikace v Azure Marketplace](media/azure-stack-edge-gpu-deploy-sample-module-marketplace/browse-apps-marketplace-1.png)

2. Vyhledejte **Začínáme s grafickým procesorem**.

    ![Vyhledat ukázkový modul GPU](media/azure-stack-edge-gpu-deploy-sample-module-marketplace/search-gpu-sample-module-1.png)

3. Vyberte **získat hned**.

    ![Získat vzorový modul](media/azure-stack-edge-gpu-deploy-sample-module-marketplace/get-sample-module-1.png)

4. Vyberte **pokračovat** a potvrďte podmínkami použití a zásady ochrany osobních údajů poskytovatele. 

    ![Získat vzorový modul 2](media/azure-stack-edge-gpu-deploy-sample-module-marketplace/terms-of-use-1.png)

5. Vyberte předplatné, které jste použili k nasazení Azure Stack hraničního zařízení pro.

    ![Výběr předplatného](media/azure-stack-edge-gpu-deploy-sample-module-marketplace/select-subscription-1.png)

6. Zadejte název služby IoT Hub, kterou jste vytvořili při konfiguraci zařízení Azure Stack Edge pro. Pokud chcete najít tento název služby IoT Hub, v Azure Portal přejít na prostředek Azure Stack Edge přidružený k vašemu zařízení. 

    1. V nabídce v levém podokně přejděte na **Edge services > IoT Edge**. 

        ![Zobrazit konfiguraci výpočtů](media/azure-stack-edge-gpu-deploy-sample-module-marketplace/view-config-1.png)

    1. Přejít na **vlastnosti**. 

        1. Poznamenejte si službu IoT Hub, která byla vytvořena při konfiguraci výpočtů na zařízení Azure Stack Edge pro.
        2. Poznamenejte si název zařízení IoT Edge, které bylo vytvořeno při konfiguraci Compute. Tento název použijete v následujícím kroku.

        ![Konfigurace výpočtů na hraničních zařízeních](media/azure-stack-edge-gpu-deploy-sample-module/view-compute-config-1.png)

10. Vyberte **nasadit do zařízení**.

11. Zadejte název zařízení IoT Edge nebo vyberte **Najít zařízení** , která chcete procházet mezi zařízeními zaregistrovanými v centru.

    ![Najít zařízení](media/azure-stack-edge-gpu-deploy-sample-module-marketplace/find-device-1.png)

12. Vyberte **vytvořit** , pokud chcete pokračovat v standardním procesu konfigurace manifestu nasazení, včetně přidání dalších modulů, pokud je to potřeba. Podrobnosti o novém modulu, jako je identifikátor URI obrázku, možnosti vytvoření a požadované vlastnosti, jsou předdefinovány, ale lze je změnit.

    ![Výběr možnosti vytvoření](media/azure-stack-edge-gpu-deploy-sample-module-marketplace/target-devices-iot-edge-module-1.png)


13. Ověřte, že je modul nasazený ve vašem IoT Hub v Azure Portal. Vyberte zařízení, vyberte **nastavit moduly** a modul by měl být uveden v části **IoT Edge moduly** .

    ![Vyberte vytvořit 2.](media/azure-stack-edge-gpu-deploy-sample-module-marketplace/running-module-iotres-1.png)

## <a name="monitor-the-module"></a>Monitorovat modul  

1. Na paletě příkazů VS Code spusťte **Azure IoT Hub: Select IoT Hub** (Azure IoT Hub: Vybrat IoT Hub).

2. Zvolte předplatné a centrum IoT obsahující zařízení IoT Edge, které chcete nakonfigurovat. V takovém případě vyberte předplatné, které se používá k nasazení Azure Stack Edge pro zařízení, a vyberte IoT Edge zařízení, které jste vytvořili pro zařízení Azure Stack Edge pro. K tomu dochází při konfiguraci výpočetní kapacity prostřednictvím Azure Portal v předchozích krocích.

3. V Průzkumníku VS Code rozbalte část Azure IoT Hub. V části **zařízení** by se mělo zobrazit zařízení IoT Edge odpovídající vašemu zařízení Azure Stack Edge pro. 

    1. Vyberte toto zařízení, klikněte pravým tlačítkem myši a vyberte možnost **Spustit sledování vestavěného koncového bodu události**.
  
        ![Spustit monitorování](media/azure-stack-edge-gpu-deploy-sample-module/monitor-builtin-event-endpoint-1.png)  

    2. Přejděte na **zařízení > moduly** a měli byste vidět, že je spuštěný **modul GPU** .

    3. VS Code terminál by měl také zobrazit IoT Hub události jako výstup monitorování pro zařízení Azure Stack Edge pro.

        ![Monitorování výstupu](media/azure-stack-edge-gpu-deploy-sample-module/monitor-events-output-1.png) 

        Zjistíte, že čas potřebný k provedení stejné sady operací (5000 iterací transformace obrazce) procesorem GPU je větší než procesor.

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o tom, jak [nakonfigurovat GPU pro použití modulu](azure-stack-edge-j-series-configure-gpu-modules.md).
