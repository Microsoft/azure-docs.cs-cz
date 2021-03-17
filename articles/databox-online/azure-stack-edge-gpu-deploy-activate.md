---
title: Kurz pro aktivaci zařízení Azure Stack Edge pro pomocí GPU v Azure Portal | Microsoft Docs
description: Kurz nasazení Azure Stack Edge pro GPU vám dává pokyn k aktivaci fyzického zařízení.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 10/07/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to activate Azure Stack Edge Pro so I can use it to transfer data to Azure.
ms.openlocfilehash: a767f6f877ae22a311afdb00417efb9f28fdc8f0
ms.sourcegitcommit: 541bb46e38ce21829a056da880c1619954678586
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2020
ms.locfileid: "91938350"
---
# <a name="tutorial-activate-azure-stack-edge-pro-with-gpu"></a>Kurz: Aktivace Azure Stack Edge pro pomocí GPU

V tomto kurzu se dozvíte, jak můžete aktivovat zařízení Azure Stack Edge pro pomocí integrovaného GPU pomocí místního webového uživatelského rozhraní.

Dokončení procesu aktivace může trvat přibližně 5 minut.

V tomto kurzu jste se dozvěděli o:

> [!div class="checklist"]
> * Požadavky
> * Aktivace fyzického zařízení

## <a name="prerequisites"></a>Požadavky

Než nakonfigurujete a nastavíte Azure Stack Edge pro zařízení s grafickým procesorem, ujistěte se, že:

* Pro vaše fyzické zařízení: 
    
    - Nainstalovali jste fyzické zařízení, jak je popsáno v části [instalace Azure Stack Edge pro](azure-stack-edge-gpu-deploy-install.md).
    - Nakonfigurovali jste nastavení sítě a COMPUTE sítě, jak je popsáno v části [Konfigurace sítě, výpočetní sítě, webového proxy serveru](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md) .
    - Nahráli jste vlastní nebo vygenerovali certifikáty zařízení v zařízení, pokud jste změnili název zařízení nebo doménu DNS přes stránku **zařízení** . Pokud jste tento krok neudělali, zobrazí se během aktivace zařízení chyba a aktivace se zablokuje. Další informace najdete v na webu [Konfigurace certifikátů](azure-stack-edge-gpu-deploy-configure-certificates.md).
    
* Máte aktivační klíč ze služby Azure Stack Edge, kterou jste vytvořili pro správu zařízení Azure Stack Edge pro. Další informace najdete na webu [Příprava nasazení Azure Stack Edge pro](azure-stack-edge-gpu-deploy-prep.md).


## <a name="activate-the-device"></a>Aktivovat zařízení

1. V místním webovém uživatelském rozhraní zařízení, **navštivte stránku Začínáme** .
2. Na dlaždici **Aktivace** vyberte **aktivovat**. 

    ![Stránka Podrobnosti cloudu místního webového uživatelského rozhraní](./media/azure-stack-edge-gpu-deploy-activate/activate-1.png)
    
3. V podokně **aktivovat** zadejte **aktivační klíč** , který jste získali v [části získání aktivačního klíče pro Azure Stack Edge pro](azure-stack-edge-gpu-deploy-prep.md#get-the-activation-key).

4. Vyberte **Použít**.

    ![Místní webové uživatelské rozhraní "Podrobnosti o cloudu" – Stránka 2](./media/azure-stack-edge-gpu-deploy-activate/activate-2.png)


5. Nejdřív se zařízení aktivuje. Pak se zobrazí výzva ke stažení souboru klíče.
    
    ![Local web UI "Podrobnosti o cloudu" Stránka 3](./media/azure-stack-edge-gpu-deploy-activate/activate-3.png)
    
    Vyberte **Stáhnout a pokračovat** a uložte *device-serial-no.js* do souboru v bezpečném umístění mimo zařízení. **Tento soubor klíče obsahuje obnovovací klíče pro disk s operačním systémem a datové disky na vašem zařízení**. Tyto klíče mohou být potřeba k usnadnění budoucího obnovení systému.

    Tady je obsah souboru *JSON* :

        
    ```json
    {
      "Id": "<Device ID>",
      "DataVolumeBitLockerExternalKeys": {
        "hcsinternal": "<BitLocker key for data disk>",
        "hcsdata": "<BitLocker key for data disk>"
      },
      "SystemVolumeBitLockerRecoveryKey": "<BitLocker key for system volume>",
      "ServiceEncryptionKey": "<Azure service encryption key>"
    }
    ```
        
 
    Různé klíče jsou vysvětleny v následující tabulce:
    
    |Pole  |Popis  |
    |---------|---------|
    |`Id`    | Toto je ID zařízení.        |
    |`DataVolumeBitLockerExternalKeys`|Jedná se o klíče BitLockeru pro datové disky a používají se k obnovení místních dat v zařízení.|
    |`SystemVolumeBitLockerRecoveryKey`| Toto je klíč BitLockeru pro systémový svazek. Tento klíč pomáhá obnovit konfiguraci systému a systémová data pro vaše zařízení. |
    |`ServiceEncryptionKey`| Tento klíč chrání tok dat prostřednictvím služby Azure. Tento klíč zajistí, že ohrožení zabezpečení služby Azure nebude mít za následek ohrožení uložených informací. |

6. Přejít na stránku **Přehled** . Stav zařízení by se měl zobrazovat jako **aktivovaný**.

    ![Místní webové uživatelské rozhraní "Podrobnosti o cloudu" na stránce 4](./media/azure-stack-edge-gpu-deploy-activate/activate-4.png)
 
Aktivace zařízení je dokončená. V zařízení teď můžete přidat sdílené složky.

Pokud narazíte na nějaké problémy při aktivaci, přečtěte si [řešení potíží s aktivací a Azure Key Vault chyby](azure-stack-edge-gpu-troubleshoot-activation.md#activation-errors).

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se dozvěděli o:

> [!div class="checklist"]
> * Požadavky
> * Aktivace fyzického zařízení

Informace o tom, jak přenést data pomocí zařízení Azure Stack Edge pro, najdete v těchto tématech:

> [!div class="nextstepaction"]
> [Přenos dat pomocí Azure Stack Edge pro](./azure-stack-edge-j-series-deploy-add-shares.md)
