---
title: Resetování a opětovná aktivace zařízení Azure Stack Edge pro | Microsoft Docs
description: Přečtěte si, jak vymazat data z a pak znovu aktivovat zařízení Azure Stack Edge pro.
services: databox
author: v-dalc
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/01/2020
ms.author: alkohli
ms.openlocfilehash: 4026bac9818b14c33c05d99caff4052adad196c3
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101744953"
---
# <a name="reset-and-reactivate-your-azure-stack-edge-pro-device"></a>Resetování a opětovná aktivace zařízení Azure Stack Edge pro

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Tento článek popisuje, jak resetovat, znovu nakonfigurovat a znovu aktivovat zařízení Azure Stack Edge pro, pokud máte problémy se zařízením nebo pokud potřebujete začít od začátku z nějakého jiného důvodu.

Po resetování zařízení, aby se data odebrala, budete muset zařízení znovu aktivovat jako nový prostředek. Resetování zařízení odebere konfiguraci zařízení, takže budete muset zařízení znovu nakonfigurovat přes místní webové uživatelské rozhraní.

V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
>
> * Vymazání dat z datových disků v zařízení
> * Opětovná aktivace zařízení vytvořením nové objednávky, opětovnou konfigurací zařízení a aktivací

## <a name="reset-data-from-the-device"></a>Resetovat data ze zařízení

Pokud chcete vymazat data z datových disků vašeho zařízení, musíte zařízení resetovat. 

Než obnovíte tovární nastavení, v případě potřeby vytvořte v zařízení kopii místních dat. Data ze zařízení můžete zkopírovat do kontejneru Azure Storage.

>[!IMPORTANT]
> Když zařízení resetujete, smaže se všechna místní data a zatížení ze zařízení, které se nedá vrátit zpět. Resetujte zařízení jenom v případě, že chcete afresh zařízení spustit.

Zařízení můžete obnovit v místním webovém uživatelském rozhraní nebo v PowerShellu. Pokyny pro PowerShell najdete v tématu [resetování zařízení](./azure-stack-edge-connect-powershell-interface.md#reset-your-device).

[! ZAHRNOUT] [obnovit data ze zařízení](../../includes/azure-stack-edge-device-reset.md)

## <a name="reactivate-device"></a>Znovu aktivovat zařízení

Po resetování zařízení bude nutné zařízení znovu aktivovat jako nový prostředek. Po umístění nové objednávky budete muset znovu nakonfigurovat a znovu aktivovat nový prostředek.

Pokud chcete znovu aktivovat stávající zařízení, postupujte podle těchto kroků:

1. Pomocí postupu v části [Vytvoření nového prostředku](azure-stack-edge-gpu-deploy-prep?tabs=azure-portal#create-a-new-resource)vytvořte novou objednávku pro existující zařízení. Na kartě **adresa pro expedici** vyberte **již zařízení**.

   ![Zadat žádné nové zařízení v dodací adrese](./media/azure-stack-edge-reset-reactivate-device/create-resource-with-no-new-device.png)

1. [Získejte aktivační klíč](azure-stack-edge-gpu-deploy-prep?tabs=azure-portal#get-the-activation-key).

1. [Připojte se k zařízení](azure-stack-edge-gpu-deploy-connect.md).

1. [Nakonfigurujte síť pro zařízení](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md).

1. [Nakonfigurujte nastavení zařízení](azure-stack-edge-gpu-deploy-set-up-device-update-time.md).

1. [Konfigurace certifikátů](azure-stack-edge-gpu-deploy-configure-certificates.md).

1. [Aktivujte zařízení](databox-online/azure-stack-edge-gpu-deploy-activate.md).

## <a name="next-steps"></a>Další kroky

- Přečtěte si, jak se [připojit k zařízení Azure Stack Edge pro](azure-stack-edge-gpu-deploy-connect.md).
