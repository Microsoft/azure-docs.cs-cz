---
title: Kurz pro filtrování, analýza dat s výpočetním prostředím v Azure Stack GPU pro procesory | Microsoft Docs
description: Naučte se konfigurovat výpočetní roli na GPU Azure Stack Edge pro a použít ji k transformaci dat před odesláním do Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 03/08/2021
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to configure compute on Azure Stack Edge Pro so I can use it to transform the data before sending it to Azure.
ms.openlocfilehash: 750b7a8367d46434f48626268a0eb37c9edddfb1
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/11/2021
ms.locfileid: "102633528"
---
# <a name="tutorial-configure-compute-on-azure-stack-edge-pro-gpu-device"></a>Kurz: Konfigurace výpočtů na zařízeních GPU Azure Stack Edge pro

<!--ALPA WILL VERIFY - [!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

V tomto kurzu se dozvíte, jak nakonfigurovat výpočetní roli a vytvořit cluster Kubernetes na zařízení Azure Stack Edge pro. 

Dokončení této procedury může trvat přibližně 20 až 30 minut.


V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Konfigurace COMPUTE
> * Získat koncové body Kubernetes

 
## <a name="prerequisites"></a>Požadavky

Před nastavením výpočetní role na zařízení Azure Stack Edge pro se ujistěte, že:

- Aktivovali jste zařízení Azure Stack Edge pro, jak je popsáno v tématu [aktivace Azure Stack Edge pro](azure-stack-edge-gpu-deploy-activate.md).
- Ujistěte se, že jste postupovali podle pokynů v části [Povolení výpočetní sítě](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md#enable-compute-network) a:
    - Bylo povoleno síťové rozhraní pro výpočetní výkon.
    - Přiřazená IP adresa uzlu Kubernetes a IP adresy externích služeb Kubernetes

## <a name="configure-compute"></a>Konfigurace COMPUTE

[!INCLUDE [configure-compute](../../includes/azure-stack-edge-gateway-configure-compute.md)]

## <a name="get-kubernetes-endpoints"></a>Získat koncové body Kubernetes

Chcete-li nakonfigurovat klienta pro přístup ke clusteru Kubernetes, budete potřebovat koncový bod Kubernetes. Pomocí těchto kroků můžete získat koncový bod rozhraní Kubernetes API z místního uživatelského rozhraní vašeho zařízení Azure Stack Edge pro.

1. V místním webovém uživatelském rozhraní zařízení klikněte na stránku **zařízení** .
2. V části **koncové body zařízení** zkopírujte koncový bod **služby API Kubernetes** . Tento koncový bod je řetězec v následujícím formátu: `https://compute.<device-name>.<DNS-domain>[Kubernetes-cluster-IP-address]` . 

    ![Stránka zařízení v místním uživatelském rozhraní](./media/azure-stack-edge-gpu-create-kubernetes-cluster/device-kubernetes-endpoint-1.png)

3. Uložte řetězec koncového bodu. Tento řetězec koncového bodu budete používat později, když konfigurujete klienta pro přístup ke clusteru Kubernetes prostřednictvím kubectl.

4. I když jste v místním webovém uživatelském rozhraní, můžete:

    - Přejít na Kubernetes API, vyberte **Upřesnit nastavení** a Stáhněte si rozšířený konfigurační soubor pro Kubernetes. 

        ![Stránka zařízení v místním uživatelském rozhraní 1](./media/azure-stack-edge-gpu-deploy-configure-compute/download-advanced-config-1.png)

        Pokud jste získali klíč od Microsoftu (výběr uživatelů může mít klíč), můžete použít tento konfigurační soubor.

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
