---
title: Nasadit modul GPU na zařízení Microsoft Azure Stack Edge z Azure Marketplace | Microsoft Docs
description: V této části najdete popis postupu povolení výpočetní služby a zajištění, aby vaše zařízení Azure Stack Edge bylo připravené pomocí místního uživatelského rozhraní.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 09/09/2020
ms.author: alkohli
ms.openlocfilehash: 7d29408c126844615666a4ab217027b6ae1d037d
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2020
ms.locfileid: "89659947"
---
# <a name="deploy-a-gpu-enabled-iot-module-from-azure-marketplace-on-azure-stack-edge-gpu-device"></a>Nasazení modulu IoT s povoleným GPU z Azure Marketplace na zařízení s grafickým procesorem Azure Stack Edge

Tento článek popisuje, jak nasadit modul IoT Edge s grafickým procesorem (GPU), který je povolený, z Azure Marketplace na Azure Stack hraničním zařízení. 

V tomto článku získáte informace o těchto tématech:
  - Příprava Azure Stackho Edge na spuštění modulu GPU
  - Stáhněte a nasaďte modul IoT s povoleným GPU z Azure Marketplace.
  - Monitorujte výstup modulu.

## <a name="about-sample-module"></a>O ukázkovém modulu

Vzorový modul GPU v tomto článku zahrnuje vzorový kód PyTorch a TensorFlow srovnávacího testu pro procesor s grafickým procesorem.

## <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, že máte následující:

- Máte přístup k zařízení s povoleným grafickým procesorem s jedním uzlem Azure Stack Edge. Toto zařízení je aktivované pomocí prostředku v Azure. 
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

4. Vyberte **pokračovat** a potvrďte podmínkami použití a zásady ochrany osobních údajů poskytovatele. 

    ![Získat vzorový modul](media/azure-stack-edge-gpu-deploy-sample-module-marketplace/terms-of-use-1.png)

5. Vyberte předplatné, které jste použili k nasazení Azure Stack hraničního zařízení.

    ![Výběr předplatného](media/azure-stack-edge-gpu-deploy-sample-module-marketplace/select-subscription-1.png)

6. Zadejte název služby IoT Hub, kterou jste vytvořili při konfiguraci Azure Stack hraničního zařízení. Pokud chcete najít tento název služby IoT Hub, v Azure Portal přejít na prostředek Azure Stack Edge přidružený k vašemu zařízení. 

    1. V nabídce v levém podokně přejděte na **Edge compute > Začínáme**. 

    1. Na dlaždici **konfigurace hraničního výpočtu** vyberte **Zobrazit konfiguraci**. 

        ![Zobrazit konfiguraci výpočtů](media/azure-stack-edge-gpu-deploy-sample-module-marketplace/view-config-1.png)

    1. V okně **Konfigurace hraničních výpočtů** :

        1. Poznamenejte si službu IoT Hub, která byla vytvořena při konfiguraci COMPUTE na zařízení Azure Stack Edge.
        2. Poznamenejte si název zařízení IoT Edge, které bylo vytvořeno při konfiguraci Compute. Tento název použijete v následujícím kroku.

        ![Konfigurace výpočtů na hraničních zařízeních](media/azure-stack-edge-gpu-deploy-sample-module/view-compute-config-1.png)

10. Vyberte **nasadit do zařízení**.

11. Zadejte název zařízení IoT Edge nebo vyberte **Najít zařízení**   , která chcete procházet mezi zařízeními zaregistrovanými v centru.

    ![Najít zařízení](media/azure-stack-edge-gpu-deploy-sample-module-marketplace/find-device-1.png)

12. Vyberte **vytvořit**   , pokud chcete pokračovat v standardním procesu konfigurace manifestu nasazení, včetně přidání dalších modulů, pokud je to potřeba. Podrobnosti o novém modulu, jako je identifikátor URI obrázku, možnosti vytvoření a požadované vlastnosti, jsou předdefinovány, ale lze je změnit.

    ![Výběr možnosti vytvoření](media/azure-stack-edge-gpu-deploy-sample-module-marketplace/target-devices-iot-edge-module-1.png)


13. Ověřte, že je modul nasazený ve vašem IoT Hub v Azure Portal. Vyberte zařízení, vyberte **nastavit moduly**   a modul by měl být uveden v části **IoT Edge moduly**   .

    ![Výběr možnosti vytvoření](media/azure-stack-edge-gpu-deploy-sample-module-marketplace/running-module-iotres-1.png)

## <a name="monitor-the-module"></a>Monitorovat modul  

1. Na paletě příkazů VS Code spusťte **Azure IoT Hub: Select IoT Hub** (Azure IoT Hub: Vybrat IoT Hub).

2. Zvolte předplatné a centrum IoT obsahující zařízení IoT Edge, které chcete nakonfigurovat. V takovém případě vyberte předplatné, které se používá k nasazení Azure Stack hraničního zařízení, a vyberte IoT Edge zařízení vytvořené pro Azure Stack hraniční zařízení. K tomu dochází při konfiguraci výpočetní kapacity prostřednictvím Azure Portal v předchozích krocích.

3. V Průzkumníku VS Code rozbalte část Azure IoT Hub. V části **zařízení**by se mělo zobrazit zařízení IoT Edge odpovídající vašemu zařízení Azure Stack Edge. 

    1. Vyberte toto zařízení, klikněte pravým tlačítkem myši a vyberte možnost **Spustit sledování vestavěného koncového bodu události**.
  
        ![Spustit monitorování](media/azure-stack-edge-gpu-deploy-sample-module/monitor-builtin-event-endpoint-1.png)  

    2. Přejděte na **zařízení > moduly** a měli byste vidět, že je spuštěný **modul GPU** .

    3. VS Code terminál by měl také zobrazit IoT Hub události jako výstup monitorování pro zařízení Azure Stack Edge.

        ![Monitorování výstupu](media/azure-stack-edge-gpu-deploy-sample-module/monitor-events-output-1.png) 

        Zjistíte, že čas potřebný k provedení stejné sady operací (5000 iterací transformace obrazce) procesorem GPU je větší než procesor.

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o tom, jak [nakonfigurovat GPU pro použití modulu](azure-stack-edge-j-series-configure-gpu-modules.md).
