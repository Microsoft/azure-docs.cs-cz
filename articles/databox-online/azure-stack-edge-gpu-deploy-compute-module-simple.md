---
title: Použití modulu IoT Edge k nasazení výpočetních úloh na Azure Stack Edge pro s grafickým procesorem | Microsoft Docs
description: Naučte se spouštět výpočetní úlohy pomocí předem vytvořeného modulu IoT Edge na zařízení GPU Azure Stack Edge pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: d10e27c80a9253de7482644debd19debce8f4e50
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2021
ms.locfileid: "106055293"
---
# <a name="tutorial-run-a-compute-workload-with-iot-edge-module-on-azure-stack-edge-pro-gpu"></a>Kurz: spuštění výpočetních úloh s modulem IoT Edge v GPU pro procesor Azure Stack Edge pro

[!INCLUDE [applies-to-GPU-and-pro-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-sku.md)]

V tomto kurzu se dozvíte, jak spustit výpočetní úlohy pomocí modulu IoT Edge na zařízení GPU Azure Stack Edge pro. Po konfiguraci výpočetní služby bude zařízení data transformovat, než je odešle do Azure.

Dokončení této procedury může trvat přibližně 10 až 15 minut.


V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Konfigurace COMPUTE
> * Přidat sdílené složky
> * Přidání výpočetního modulu
> * Ověření transformace dat a jejich přenos

 
## <a name="prerequisites"></a>Požadavky

Než nastavíte výpočetní roli na zařízení GPU Azure Stack Edge pro, ujistěte se, že:

- Aktivovali jste zařízení Azure Stack Edge pro, jak je popsáno v tématu [aktivace Azure Stack Edge pro](azure-stack-edge-gpu-deploy-activate.md).
- Máte modul IoT Edge, který můžete spustit na svých datech. V tomto kurzu jsme použili `filemove2` modul, který přesouvá data z hraničního místního sdílení na zařízení do sdílené složky Edge, ze které data přecházejí Azure Storage účtu.


## <a name="configure-compute"></a>Konfigurace COMPUTE

[!INCLUDE [configure-compute](../../includes/azure-stack-edge-gateway-configure-compute.md)]


## <a name="add-shares"></a>Přidat sdílené složky

Pro jednoduché nasazení v tomto kurzu budete potřebovat dvě sdílené složky: jednu hranovou sdílenou složku a další hranovou místní sdílenou složku.

1. Pokud chcete na zařízení přidat hraniční sdílenou složku, proveďte následující kroky:

    1. V prostředku Azure Stack Edge přejít do **složky Cloud Storage gateway > sdílené složky**.
    2. Na panelu příkazů vyberte **+ Přidat sdílenou složku**.
    3. V okně **Přidat sdílenou složku** zadejte název sdílené složky a vyberte typ sdílené složky.
    4. Pokud chcete připojit hraniční sdílenou složku, zaškrtněte políčko pro **použití sdílené složky s hraničními výpočty**.
    5. Vyberte **účet úložiště**, **službu úložiště**, stávající uživatel a pak vyberte **vytvořit**.

        ![Přidat hraniční sdílenou složku](./media/azure-stack-edge-gpu-deploy-compute-module-simple/add-edge-share-1.png) 


    > [!NOTE]
    > Aby bylo možné připojit sdílenou složku systému souborů NFS ke výpočetním prostředím, musí být výpočetní síť nakonfigurovaná ve stejné podsíti jako virtuální IP adresa systému souborů NFS. Podrobnosti o tom, jak nakonfigurovat výpočetní síť, najdete v tématu [Povolení služby COMPUTE Network na Azure Stack Edge pro](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md).

    Vytvoří se sdílená složka Edge a obdržíte oznámení o úspěšném vytvoření. Seznam sdílených složek se může aktualizovat, ale musíte počkat na dokončení vytváření sdílené složky.

2. Pokud chcete na zařízení přidat hraniční místní sdílenou složku, opakujte všechny kroky v předchozím kroku a zaškrtněte políčko **Konfigurovat jako hraniční místní sdílení**. Data v místní sdílené složce zůstanou v zařízení.

    ![Přidat místní sdílenou položku Edge](./media/azure-stack-edge-gpu-deploy-compute-module-simple/add-edge-share-2.png)

    Pokud jste vytvořili místní sdílenou složku NFS, ke zkopírování souborů do sdílené složky použijte následující příkaz vzdálené synchronizace (rsync):

    `rsync <source file path> < destination file path>`

    Další informace o tomto `rsync` příkazu najdete v [ `Rsync` dokumentaci](https://www.computerhope.com/unix/rsync.htm).
 
3. Pokud chcete zobrazit aktualizovaný seznam sdílených složek, přejděte do **brány cloudového úložiště > sdílené složky** .

    ![Aktualizovaný seznam sdílených složek](./media/azure-stack-edge-gpu-deploy-compute-module-simple/add-edge-share-3.png) 
 

## <a name="add-a-module"></a>Přidat modul

Můžete přidat vlastní nebo předem sestavený modul. Zařízení nepochází s předem vytvořenými nebo vlastními moduly. Další informace o tom, jak vytvořit vlastní modul, najdete v tématu [vývoj modulu C# pro zařízení Azure Stack Edge pro](./azure-stack-edge-gpu-create-iot-edge-module.md).

V této části přidáte vlastní modul do zařízení IoT Edge, které jste vytvořili v tématu [vývoj modulu C# pro vaši Azure Stack Edge pro](./azure-stack-edge-gpu-create-iot-edge-module.md). Tento vlastní modul přebírá soubory z hraniční místní sdílené složky na hraničním zařízení a přesouvá je do hraniční sdílené složky (Cloud) na zařízení. Sdílená složka cloudu pak tyto soubory vloží do účtu služby Azure Storage, který je přidružený ke sdílené složce cloudu.

Chcete-li přidat modul, proveďte následující kroky:

1. Přejít na **IoT Edge > moduly**. Na panelu příkazů vyberte **+ Přidat modul**. 

2. V okně **Přidat modul** zadejte následující hodnoty:

    
    |Pole  |Hodnota  |
    |---------|---------|
    |Název     | Jedinečný název modulu. Tento modul je kontejner Docker, který můžete nasadit do zařízení IoT Edge, které je přidružené k vaší Azure Stack Edge pro.        |
    |Identifikátor URI image     | Identifikátor URI image pro odpovídající image kontejneru pro modul        |
    |Požadovány přihlašovací údaje     | Pokud je zaškrtnuto, uživatelské jméno a heslo slouží k načtení modulů s porovnávací adresou URL.        |
    |Vstupní sdílená složka     | Vyberte vstupní sdílenou složku. V tomto případě je v tomto případě vstupní sdílená složka Edge v místním prostředí. Pomocí tohoto modulu se přesunuly soubory z místní sdílené složky Edge do hraniční sdílené složky, kam se nahrají do cloudu.        |
    |Výstupní sdílená složka     | Vyberte výstupní sdílenou složku. Sdílená složka Edge je v tomto případě výstupní sdílenou složkou.        |
    |Typ aktivační události     | Vyberte ze **souboru** nebo **plánu**. Triggery souborů se aktivují vždy, když dojde k události souboru, jako je zápis souboru do vstupní sdílené složky. Naplánované triggery se aktivují v závislosti na vámi definovaném plánu.         |
    |Název aktivační události     | Jedinečný název aktivační události.         |
    |Proměnné prostředí| Volitelné informace, které vám pomůžou definovat prostředí, ve kterém se modul spustí   |

    ![Přidat a nakonfigurovat modul](./media/azure-stack-edge-gpu-deploy-compute-module-simple/add-module-1.png)

3. Vyberte **Přidat**. Modul se přidá. Stránka **IoT Edge > moduly** se aktualizuje, aby označovala, že je modul nasazený. Běhový stav modulu, který jste přidali, by měl být *spuštěný*.

    ![Nasazený modul](./media/azure-stack-edge-gpu-deploy-compute-module-simple/add-module-2.png)

### <a name="verify-data-transform-and-transfer"></a>Ověření transformace dat a jejich přenos

Posledním krokem je ujistit se, že modul běží a data se zpracovávají podle očekávání. Běhový stav modulu by měl být spuštěný pro vaše IoT Edge zařízení v prostředku IoT Hub.

Pokud chcete ověřit, jestli je modul spuštěný, a zpracovávat data podle očekávání, udělejte toto:


1. V Průzkumníku souborů se připojte ke sdíleným složkám Edge i k okrajům, které jste vytvořili dříve. Postup najdete v tématu. 

    ![Připojení ke sdíleným složkám pro místní Cloud a Edge](./media/azure-stack-edge-gpu-deploy-compute-module-simple/verify-data-1.png) 
 
1. Přidejte data do místní sdílené složky.

    ![Soubor se zkopíroval do místní sdílené složky Edge.](./media/azure-stack-edge-gpu-deploy-compute-module-simple/verify-data-2.png) 
 
   Data se přesunou do cloudové sdílené složky.

    ![Soubor se přesunul do sdílené cloudové složky Edge.](./media/azure-stack-edge-gpu-deploy-compute-module-simple/verify-data-3.png)  

   Data se pak odešlou ze sdílené složky cloudu do účtu úložiště. Chcete-li zobrazit data, můžete použít Průzkumník služby Storage nebo Azure Storage na portálu.

    ![Ověřit data v účtu úložiště](./media/azure-stack-edge-gpu-deploy-compute-module-simple/verify-data-4.png)
 
Dokončili jste proces ověření.


## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Konfigurace COMPUTE
> * Přidat sdílené složky
> * Přidání výpočetního modulu
> * Ověření transformace dat a jejich přenos

Informace o tom, jak spravovat zařízení Azure Stack Edge pro, najdete tady:

> [!div class="nextstepaction"]
> [Použití místního webového uživatelského rozhraní pro správu Azure Stack Edge pro](azure-stack-edge-gpu-manage-access-power-connectivity-mode.md)