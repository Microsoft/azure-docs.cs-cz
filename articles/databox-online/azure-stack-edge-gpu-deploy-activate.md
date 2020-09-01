---
title: Kurz aktivace zařízení Azure Stack Edge pomocí GPU v Azure Portal | Microsoft Docs
description: Kurz nasazení Azure Stack hraničního procesoru vám dává pokyn k aktivaci fyzického zařízení.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 08/31/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to activate Azure Stack Edge so I can use it to transfer data to Azure.
ms.openlocfilehash: 88be4d9753e48f70dae5666e800a54209ed6ba3f
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/01/2020
ms.locfileid: "89267934"
---
# <a name="tutorial-activate-azure-stack-edge-with-gpu"></a>Kurz: Aktivace Azure Stack Edge pomocí GPU

V tomto kurzu se dozvíte, jak můžete aktivovat zařízení Azure Stack Edge pomocí integrovaného GPU pomocí místního webového uživatelského rozhraní.

Dokončení procesu aktivace může trvat přibližně 5 minut.

V tomto kurzu jste se dozvěděli o:

> [!div class="checklist"]
> * Požadavky
> * Aktivace fyzického zařízení

## <a name="prerequisites"></a>Požadavky

Před konfigurací a nastavením Azure Stack hraničního zařízení pomocí GPU se ujistěte, že:

* Pro vaše fyzické zařízení: 
    
    - Nainstalovali jste fyzické zařízení, jak je popsáno v části [instalace Azure Stack Edge](azure-stack-edge-gpu-deploy-install.md).
    - Nakonfigurovali jste nastavení sítě a COMPUTE sítě, jak je popsáno v části [Konfigurace sítě, výpočetní sítě, webového proxy serveru](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md) .
    - Nahráli jste vlastní nebo vygenerovali certifikáty zařízení v zařízení, pokud jste změnili název zařízení nebo doménu DNS přes stránku **zařízení** . Pokud jste tento krok neudělali, zobrazí se během aktivace zařízení chyba a aktivace se zablokuje. Další informace najdete v na webu [Konfigurace certifikátů](azure-stack-edge-gpu-deploy-configure-certificates.md).
    
* Máte aktivační klíč ze služby Azure Stack Edge, kterou jste vytvořili pro správu Azure Stack hraničního zařízení. Další informace najdete na webu [Příprava nasazení Azure Stack Edge](azure-stack-edge-gpu-deploy-prep.md).


## <a name="activate-the-device"></a>Aktivovat zařízení

1. V místním webovém uživatelském rozhraní zařízení, **navštivte stránku Začínáme** .
2. Na dlaždici **Aktivace** vyberte **aktivovat**. 

    ![Stránka Podrobnosti cloudu místního webového uživatelského rozhraní](./media/azure-stack-edge-gpu-deploy-activate/activate-1.png)
    
3. V podokně **aktivovat** zadejte **aktivační klíč** , který jste získali v [části získání aktivačního klíče pro Azure Stack Edge](azure-stack-edge-gpu-deploy-prep.md#get-the-activation-key).

4. Vyberte **Použít**.

    ![Stránka Podrobnosti cloudu místního webového uživatelského rozhraní](./media/azure-stack-edge-gpu-deploy-activate/activate-2.png)


5. Nejdřív se zařízení aktivuje. Pak se zobrazí výzva ke stažení souboru klíče.
    
    ![Stránka Podrobnosti cloudu místního webového uživatelského rozhraní](./media/azure-stack-edge-gpu-deploy-activate/activate-3.png)
    
    Vyberte **Stáhnout soubor klíče** a uložte *keys.js* do souboru v bezpečném umístění mimo zařízení. **Tento soubor klíče obsahuje obnovovací klíče pro disk s operačním systémem a datové disky na vašem zařízení**. Tady je obsah *keys.jsv* souboru:

        
    ```json
    {
      "Id": "1ab3fe39-26e6-4984-bb22-2e02d3fb147e",
      "DataVolumeBitLockerExternalKeys": {
        "hcsinternal": "C086yg1DrPo0DuZB/a7hUh+kBWj804coJfBA9LDzZqw=",
        "hcsdata": "8ohX9bG3YSZl9DZmZLkYl//L9dXi1XiQrqza+iSd64Q="
      },
      "SystemVolumeBitLockerRecoveryKey": "105347-156739-594473-151107-005082-252604-471955-439395",
      "ServiceEncryptionKey": "oEwxNJeULzGRFt6DsLgcLw=="
    }
    ```
        
 
    Následující tabulka popisuje různé klíče:
    
    |Pole  |Popis  |
    |---------|---------|
    |`Id`    | Toto je ID zařízení.        |
    |`DataVolumeBitLockerExternalKeys`|Jedná se o klíče BitLockeru pro datové disky a používají se k obnovení místních dat v zařízení.|
    |`SystemVolumeBitLockerRecoveryKey`| Toto je klíč BitLockeru pro systémový svazek. Tento klíč pomáhá obnovit konfiguraci systému a systémová data pro vaše zařízení. |
    |`ServiceEncryptionKey`| Tento klíč chrání tok dat prostřednictvím služby Azure. Tento klíč zajistí, že ohrožení zabezpečení služby Azure nebude mít za následek ohrožení uložených informací. |

6. Přejít na stránku **Přehled** . Stav zařízení by se měl zobrazovat jako **aktivovaný**.

    ![Stránka Podrobnosti cloudu místního webového uživatelského rozhraní](./media/azure-stack-edge-gpu-deploy-activate/activate-4.png)
 
Aktivace zařízení je dokončená. V zařízení teď můžete přidat sdílené složky.


## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se dozvěděli o:

> [!div class="checklist"]
> * Požadavky
> * Aktivace fyzického zařízení

Informace o tom, jak přenést data pomocí Azure Stack hraniční zařízení, najdete v těchto tématech:

> [!div class="nextstepaction"]
> [Přenos dat pomocí Azure Stack Edge](./azure-stack-edge-j-series-deploy-add-shares.md)
