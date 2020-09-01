---
title: Kurz, jak filtrovat, analyzovat data s výpočetními prostředky na Azure Stack GPU na okraji | Microsoft Docs
description: Naučte se, jak nakonfigurovat výpočetní roli na grafickém procesoru Azure Stack Edge a použít ho k transformaci dat před odesláním do Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 08/28/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to configure compute on Azure Stack Edge so I can use it to transform the data before sending it to Azure.
ms.openlocfilehash: a5e0817050f8411dfc7fc64edae21f1b927e4e5c
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/01/2020
ms.locfileid: "89262984"
---
# <a name="tutorial-configure-compute-on-azure-stack-edge-gpu-device"></a>Kurz: Konfigurace výpočetních prostředků na zařízení GPU na Azure Stack hraničních zařízeních

<!--[!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

V tomto kurzu se dozvíte, jak nakonfigurovat výpočetní roli a vytvořit v zařízení Azure Stack Edge cluster Kubernetes. 

Dokončení této procedury může trvat přibližně 20 až 30 minut.


V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Konfigurace COMPUTE
> * Získat koncové body Kubernetes

 
## <a name="prerequisites"></a>Požadavky

Před nastavením výpočetní role na zařízení Azure Stack Edge se ujistěte, že:

- Aktivovali jste Azure Stack hraniční zařízení, jak je popsáno v tématu [aktivace Azure Stack Edge](azure-stack-edge-gpu-deploy-activate.md).
- Ujistěte se, že jste postupovali podle pokynů v části [Povolení výpočetní sítě](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md#enable-compute-network) a:
    - Bylo povoleno síťové rozhraní pro výpočetní výkon.
    - Přiřazená IP adresa uzlu Kubernetes a IP adresy externích služeb Kubernetes

## <a name="configure-compute"></a>Konfigurace COMPUTE

Pokud chcete nakonfigurovat výpočetní výkon na Azure Stack hraničních zařízeních, vytvoříte prostředek IoT Hub prostřednictvím Azure Portal.

1. V Azure Portal prostředku Azure Stack Edge si Projděte **Přehled**. V pravém podokně na dlaždici **výpočty** **vyberte Začínáme**.

    ![Začínáme se službou COMPUTE](./media/azure-stack-edge-j-series-deploy-configure-compute/configure-compute-1.png)

2. Na dlaždici **Konfigurace hraničních výpočtů** vyberte **Konfigurovat výpočetní**prostředky.

    ![Konfigurace COMPUTE](./media/azure-stack-edge-j-series-deploy-configure-compute/configure-compute-2.png)

3. V okně **Konfigurace hraničních výpočtů** zadejte následující:

   
    |Pole  |Hodnota  |
    |---------|---------|
    |IoT Hub     | Vyberte z **nových** nebo **existujících**. <br> Ve výchozím nastavení se k vytvoření prostředku IoT používá standardní vrstva (S1). Pokud chcete použít prostředek IoT úrovně Free, vytvořte ho a pak vyberte existující prostředek. <br> V každém případě IoT Hub prostředek používá stejné předplatné a skupinu prostředků, kterou používá prostředek Azure Stack Edge.     |
    |Name     |Zadejte název prostředku IoT Hub.         |

    ![Začínáme se službou COMPUTE](./media/azure-stack-edge-j-series-deploy-configure-compute/configure-compute-3.png)

4. Vyberte **Create** (Vytvořit). Vytvoření prostředku IoT Hub trvá několik minut. Po vytvoření prostředku IoT Hub se aktualizuje a zobrazí **se konfigurace výpočtů na dlaždici** . 

    ![Začínáme se službou COMPUTE](./media/azure-stack-edge-j-series-deploy-configure-compute/configure-compute-4.png)

5. Pokud chcete ověřit, jestli je role hraničního výpočtu nakonfigurovaná, vyberte na dlaždici **Konfigurovat výpočty** možnost **Zobrazit výpočetní** prostředky.
    
    ![Začínáme se službou COMPUTE](./media/azure-stack-edge-j-series-deploy-configure-compute/configure-compute-5.png)

    > [!NOTE]
    > Pokud je dialogové okno **Konfigurovat výpočetní** prostředí zavřené, než se IoT Hub přidruží k hraničnímu zařízení Azure Stack Edge, IoT Hub se vytvoří, ale v konfiguraci výpočtů se nezobrazuje. 
    
Když se na hraničním zařízení nastaví role hraničního zpracování, vytvoří se dvě zařízení: zařízení IoT a zařízení IoT Edge. Obě zařízení je možné zobrazit v prostředku IoT Hub. V tomto zařízení IoT Edge je spuštěn také modul runtime IoT Edge. V tomto okamžiku je k dispozici pouze Platforma Linux pro vaše zařízení IoT Edge.

Konfigurace výpočetních prostředků na pozadí může trvat 20-30 minut, než se vytvoří virtuální počítače a Kubernetes cluster. 

Po úspěšné konfiguraci výpočtů v Azure Portal existuje cluster Kubernetes a výchozí uživatel přidružený k oboru názvů IoT (obor názvů System řízený pomocí Azure Stack Edge). 

## <a name="get-kubernetes-endpoints"></a>Získat koncové body Kubernetes

Chcete-li nakonfigurovat klienta pro přístup ke clusteru Kubernetes, budete potřebovat koncový bod Kubernetes. Pomocí těchto kroků můžete získat koncový bod rozhraní Kubernetes API z místního uživatelského rozhraní vašeho zařízení Azure Stack Edge.

1. V místním webovém uživatelském rozhraní zařízení klikněte na stránku **zařízení** .
2. V části **koncové body zařízení**zkopírujte koncový bod **služby API Kubernetes** . Tento koncový bod je řetězec v následujícím formátu: `https://compute.<device-name>.<DNS-domain>[Kubernetes-cluster-IP-address]` . 

    ![Stránka zařízení v místním uživatelském rozhraní](./media/azure-stack-edge-j-series-create-kubernetes-cluster/device-kubernetes-endpoint-1.png)

3. Uložte řetězec koncového bodu. Použijete ji později při konfiguraci klienta pro přístup ke clusteru Kubernetes prostřednictvím kubectl.

4. I když jste v místním webovém uživatelském rozhraní, můžete:

    - Přejít na Kubernetes API, vyberte **Upřesnit nastavení** a Stáhněte si rozšířený konfigurační soubor pro Kubernetes. 

        ![Stránka zařízení v místním uživatelském rozhraní 1](./media/azure-stack-edge-gpu-deploy-configure-compute/download-advanced-config-1.png)

        Pokud jste k dispozici klíč od Microsoftu (vyberte uživatele může mít), můžete použít tento konfigurační soubor.

        ![Stránka zařízení v místním uživatelském rozhraní 2](./media/azure-stack-edge-gpu-deploy-configure-compute/download-advanced-config-2.png)

    - Můžete také přejít na koncový bod **řídicího panelu Kubernetes** a stáhnout `aseuser` konfigurační soubor. 
    
        ![Stránka zařízení v místním uživatelském rozhraní 3](./media/azure-stack-edge-gpu-deploy-configure-compute/download-aseuser-config-1.png)

        `aseuser`Konfigurační soubor umožňuje ladit všechny problémy související s `iotedge` oborem názvů v clusteru Kubernetes. Další informace najdete v tématu [ladění problémů Kubernetes](azure-stack-edge-gpu-connect-powershell-interface.md#debug-kubernetes-issues-related-to-iot-edge). 


## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Konfigurace COMPUTE
> * Získat koncové body Kubernetes


Informace o tom, jak spravovat Azure Stack hraniční zařízení, najdete v těchto tématech:

> [!div class="nextstepaction"]
> [Použití místního webového uživatelského rozhraní pro správu Azure Stackho Edge](azure-stack-edge-manage-access-power-connectivity-mode.md)
