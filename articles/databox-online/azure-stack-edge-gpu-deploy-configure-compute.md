---
title: Kurz pro filtrování, analýza dat s výpočetním prostředím v Azure Stack GPU pro procesory | Microsoft Docs
description: Naučte se konfigurovat výpočetní roli na GPU Azure Stack Edge pro a použít ji k transformaci dat před odesláním do Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 08/28/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to configure compute on Azure Stack Edge Pro so I can use it to transform the data before sending it to Azure.
ms.openlocfilehash: 711da24b3edf08f4867109d0d70165955236c39a
ms.sourcegitcommit: a07a01afc9bffa0582519b57aa4967d27adcf91a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "91743416"
---
# <a name="tutorial-configure-compute-on-azure-stack-edge-pro-gpu-device"></a>Kurz: Konfigurace výpočtů na zařízeních GPU Azure Stack Edge pro

<!--[!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

V tomto kurzu se dozvíte, jak nakonfigurovat výpočetní roli a vytvořit cluster Kubernetes na zařízení Azure Stack Edge pro. 

Dokončení této procedury může trvat přibližně 20 až 30 minut.


V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Konfigurace COMPUTE
> * Získat koncové body Kubernetes

 
## <a name="prerequisites"></a>Předpoklady

Před nastavením výpočetní role na zařízení Azure Stack Edge pro se ujistěte, že:

- Aktivovali jste zařízení Azure Stack Edge pro, jak je popsáno v tématu [aktivace Azure Stack Edge pro](azure-stack-edge-gpu-deploy-activate.md).
- Ujistěte se, že jste postupovali podle pokynů v části [Povolení výpočetní sítě](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md#enable-compute-network) a:
    - Bylo povoleno síťové rozhraní pro výpočetní výkon.
    - Přiřazená IP adresa uzlu Kubernetes a IP adresy externích služeb Kubernetes

## <a name="configure-compute"></a>Konfigurace COMPUTE

Pokud chcete nakonfigurovat výpočetní výkon na Azure Stack Edge pro, vytvoříte prostředek IoT Hub prostřednictvím Azure Portal.

1. V Azure Portal prostředku Azure Stack Edge si Projděte **Přehled**. V pravém podokně na dlaždici **výpočty** **vyberte Začínáme**.

    ![Začínáme se službou COMPUTE](./media/azure-stack-edge-j-series-deploy-configure-compute/configure-compute-1.png)

2. Na dlaždici **Konfigurace hraničních výpočtů** vyberte **Konfigurovat výpočetní**prostředky.

    ![Konfigurace COMPUTE](./media/azure-stack-edge-j-series-deploy-configure-compute/configure-compute-2.png)

3. V okně **Konfigurace hraničních výpočtů** zadejte následující:

   
    |Pole  |Hodnota  |
    |---------|---------|
    |IoT Hub     | Vyberte z **nových** nebo **existujících**. <br> Ve výchozím nastavení se k vytváření prostředků IoT používá úroveň Standard (S1). Pokud chcete použít prostředek IoT úrovně Free, vytvořte ho a pak vyberte existující prostředek. <br> V každém případě IoT Hub prostředek používá stejné předplatné a skupinu prostředků, kterou používá prostředek Azure Stack Edge.     |
    |Name     |Zadejte název prostředku IoT Hub.         |

    ![Začínáme s výpočetním využitím 2](./media/azure-stack-edge-j-series-deploy-configure-compute/configure-compute-3.png)

4. Vyberte **Vytvořit**. Vytvoření prostředku IoT Hub trvá několik minut. Po vytvoření prostředku IoT Hub se aktualizuje a zobrazí **se konfigurace výpočtů na dlaždici** . 

    ![Začínáme s výpočetním prostředím 3](./media/azure-stack-edge-j-series-deploy-configure-compute/configure-compute-4.png)

5. Pokud chcete ověřit, jestli je role hraničního výpočtu nakonfigurovaná, vyberte na dlaždici **Konfigurovat výpočty** možnost **Zobrazit výpočetní** prostředky.
    
    ![Začínáme se službou COMPUTE 4](./media/azure-stack-edge-j-series-deploy-configure-compute/configure-compute-5.png)

    > [!NOTE]
    > Pokud je dialogové okno **Konfigurovat výpočetní** prostředí zavřené dřív, než se IoT Hub přidruží k zařízení Azure Stack Edge pro, vytvoří se IoT Hub, ale v konfiguraci výpočtů se nezobrazuje. 
    
Když se na hraničním zařízení nastaví role hraničního zpracování, vytvoří se dvě zařízení: zařízení IoT a zařízení IoT Edge. Obě zařízení je možné zobrazit v prostředku IoT Hub. V tomto zařízení IoT Edge je spuštěn také modul runtime IoT Edge. V tomto okamžiku je k dispozici pouze Platforma Linux pro vaše zařízení IoT Edge.

Konfigurace výpočetních prostředků na pozadí může trvat 20-30 minut, než se vytvoří virtuální počítače a Kubernetes cluster. 

Po úspěšné konfiguraci výpočtů v Azure Portal existuje cluster Kubernetes a výchozí uživatel přidružený k oboru názvů IoT (obor názvů System řízený pomocí Azure Stack Edge pro). 

## <a name="get-kubernetes-endpoints"></a>Získat koncové body Kubernetes

Chcete-li nakonfigurovat klienta pro přístup ke clusteru Kubernetes, budete potřebovat koncový bod Kubernetes. Pomocí těchto kroků můžete získat koncový bod rozhraní Kubernetes API z místního uživatelského rozhraní vašeho zařízení Azure Stack Edge pro.

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

        Tento konfigurační soubor můžete použít k přihlášení do řídicího panelu Kubernetes nebo k ladění všech problémů v clusteru Kubernetes. Další informace najdete v tématu [přístup k řídicímu panelu Kubernetes](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md#access-dashboard). 


## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Konfigurace COMPUTE
> * Získat koncové body Kubernetes


Informace o tom, jak spravovat zařízení Azure Stack Edge pro, najdete tady:

> [!div class="nextstepaction"]
> [Použití místního webového uživatelského rozhraní pro správu Azure Stack Edge pro](azure-stack-edge-manage-access-power-connectivity-mode.md)
