---
title: Spuštění modulu GPU na zařízení s grafickým procesorem Microsoft Azure Stack Edge pro | Microsoft Docs
description: Popisuje, jak nakonfigurovat a spustit modul na GPU na zařízení Azure Stack Edge pro pomocí Azure Portal.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/25/2020
ms.author: alkohli
ms.openlocfilehash: 8b9f1180639f638e72fdea2f87958628a2e9e86b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90891462"
---
# <a name="configure-and-run-a-module-on-gpu-on-azure-stack-edge-pro-device"></a>Konfigurace a spuštění modulu na GPU na zařízení Azure Stack Edge pro

Vaše zařízení Azure Stack Edge pro obsahuje jednu nebo více grafických procesorů (GPU). GPU je oblíbená volba pro výpočty AI, protože nabízí možnosti paralelního zpracování a jsou rychlejší při vykreslování obrázků než procesory středního zpracování. Další informace o GPU obsaženém v zařízení Azure Stack Edge pro najdete v části [technické specifikace pro zařízení Azure Stack Edge pro](azure-stack-edge-gpu-technical-specifications-compliance.md).

Tento článek popisuje, jak nakonfigurovat a spustit modul na GPU na zařízení Azure Stack Edge pro. V tomto článku použijete veřejně dostupná **čísla** kontejnerů, která se napíší pro grafické procesory NVIDIA T4. Tento postup můžete použít ke konfiguraci jakýchkoli dalších modulů publikovaných pomocí NVIDIA pro tyto GPU.


## <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, že:

1. Máte přístup k zařízení s povoleným grafickým procesorem s jedním uzlem Azure Stack Edge pro. Toto zařízení je aktivované pomocí prostředku v Azure.  

## <a name="configure-module-to-use-gpu"></a>Konfigurovat modul pro použití GPU

Pokud chcete nakonfigurovat modul pro použití GPU na zařízení Azure Stack Edge pro ke spuštění modulu, postupujte podle těchto kroků.

1. V Azure Portal přejdete k prostředku, který je přidružený k vašemu zařízení. 

2. Začněte tím, že přejdete na **Edge compute >**. Na dlaždici **konfigurace hraničního výpočtu** vyberte Konfigurovat.

    ![Konfigurovat modul pro použití GPU 1](media/azure-stack-edge-j-series-configure-gpu-modules/configure-compute-1.png)

3. V okně **Konfigurace hraničních výpočtů** :

    1. Pro **IoT Hub**vyberte **vytvořit novou**.
    2. Zadejte název prostředku IoT Hub, který chcete pro své zařízení vytvořit. Pokud chcete použít bezplatnou úroveň, vyberte existující prostředek. 
    3. Poznamenejte si IoT Edge zařízení a zařízení brány IoT, která jsou vytvořená pomocí prostředku IoT Hub. Tyto informace budete používat v pozdějších krocích.

    ![Konfigurovat modul pro použití GPU 2](media/azure-stack-edge-j-series-configure-gpu-modules/configure-compute-2.png)

4. Vytvoření prostředku IoT Hub trvá několik minut. Po vytvoření prostředku na dlaždici **konfigurace hraničního výpočtu** vyberte **Zobrazit konfiguraci** a zobrazte podrobnosti o IoT Hub prostředku.

    ![Nakonfigurovat modul na použití GPU 4](media/azure-stack-edge-j-series-configure-gpu-modules/configure-compute-4.png)

5. Přejít na **automatickou správu zařízení > IoT Edge**.

    ![Nakonfigurovat modul pro použití GPU 6](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-2.png)

    V pravém podokně se zobrazí IoT Edge zařízení přidružené k vašemu zařízení Azure Stack Edge pro. To odpovídá IoT Edge zařízení, které jste vytvořili v předchozím kroku při vytváření prostředku IoT Hub. 
    
6. Vyberte toto IoT Edge zařízení.

   ![Nakonfigurovat modul pro použití GPU 7](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-3.png)

7.  Vyberte **Set modules** (Nastavit moduly).

    ![Nakonfigurovat modul pro použití GPU 8](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-4.png)

8. Vyberte **+ Přidat** a pak vyberte **+ IoT Edge modul**. 

    ![Nakonfigurovat modul pro použití GPU 9](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-5.png)

9. Na kartě **Přidat modul IoT Edge** :

    1. Zadejte **identifikátor URI image**. Tady budete používat veřejně dostupné **číslice** modulu NVIDIA. 
    
    2. Nastavte **zásady restartování** na **vždycky**.
    
    3. Nastavte **požadovaný stav** na **spuštěno**.
    
    ![Nakonfigurovat modul pro použití GPU 10](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-6.png)

10. Na kartě **proměnné prostředí** zadejte název proměnné a odpovídající hodnotu. 

    1. Pokud chcete, aby aktuální modul používal na tomto zařízení jeden grafický procesor, použijte NVIDIA_VISIBLE_DEVICES. 

    2. Nastavte hodnotu na 0 nebo 1. Tím se zajistí, že zařízení pro tento modul používá aspoň jeden grafický procesor. Když nastavíte hodnotu na 0, 1, to znamená, že tento modul používá GPU na vašem zařízení.

        ![Konfigurovat modul pro použití GPU 11](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-7.png)

        Další informace o proměnných prostředí, které můžete použít s grafickým procesorem NVIDIA, najdete v části [modul runtime kontejneru NVIDIA](https://github.com/NVIDIA/nvidia-container-runtime#environment-variables-oci-spec).

    > [!NOTE]
    > GPU se dá mapovat jenom na jeden modul. Modul může ale používat jeden nebo žádné GPU. 

11. Zadejte název svého modulu. V tomto okamžiku se můžete rozhodnout, že chcete poskytnout možnost vytvoření kontejneru a upravit nastavení s dvojitým použitím modulu, nebo pokud je dokončený, vyberte **Přidat**. 

    ![Nakonfigurovat modul pro použití GPU 12](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-8.png)

12. Ujistěte se, že je modul spuštěný, a vyberte **zkontrolovat + vytvořit**.    

    ![Nakonfigurovat modul pro použití GPU 13](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-9.png)

13. Na kartě **Revize + vytvořit** se zobrazí možnosti nasazení, které jste vybrali. Zkontrolujte možnosti a vyberte **vytvořit**.
    
    ![Nakonfigurovat modul pro použití GPU 14](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-10.png)

14. Poznamenejte si **běhový stav** modulu. 
    
    ![Nakonfigurovat modul pro použití GPU 15](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-11.png)

    Nasazení modulu trvá několik minut. Vyberte **aktualizovat** a měli byste vidět, že je **spuštěná**aktualizace **stavu modulu runtime** .

    ![Konfigurovat modul pro použití GPU 16](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-12.png)


## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [proměnných prostředí, které můžete použít s grafickým procesorem NVIDIA](https://github.com/NVIDIA/nvidia-container-runtime#environment-variables-oci-spec).
