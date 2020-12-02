---
title: Kurz aktivace zařízení Azure Stack Edge pro R v Azure Portal
description: Kurz nasazení Azure Stack Edge pro R vám dává pokyn k aktivaci fyzického zařízení.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 10/19/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to activate Azure Stack Edge Pro R device so I can use it to transfer data to Azure.
ms.openlocfilehash: c1408c25ea7e5d805d17e084899b0ffeb6cfec28
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/01/2020
ms.locfileid: "96468127"
---
# <a name="tutorial-activate-azure-stack-edge-pro-r-device"></a>Kurz: aktivace zařízení Azure Stack Edge pro R

V tomto kurzu se dozvíte, jak můžete aktivovat zařízení Azure Stack Edge pro R pomocí místního webového uživatelského rozhraní.

Dokončení procesu aktivace může trvat přibližně 5 minut.

V tomto kurzu jste se dozvěděli o:

> [!div class="checklist"]
> * Předpoklady
> * Aktivace fyzického zařízení

## <a name="prerequisites"></a>Předpoklady

Před konfigurací a nastavením Azure Stack hraničního zařízení pro R se ujistěte, že:

* Pro vaše fyzické zařízení: 
    
    - Nainstalovali jste fyzické zařízení, jak je popsáno v části [instalace Azure Stack Edge pro R](azure-stack-edge-pro-r-deploy-install.md).
    - Nakonfigurovali jste nastavení sítě a COMPUTE sítě, jak je popsáno v části [Konfigurace sítě, výpočetní sítě, webového proxy serveru](azure-stack-edge-pro-r-deploy-configure-network-compute-web-proxy.md) .
    - Nahráli jste vlastní nebo vygenerovali certifikáty zařízení v zařízení, pokud jste změnili název zařízení nebo doménu DNS přes stránku **zařízení** . Pokud jste tento krok neudělali, zobrazí se během aktivace zařízení chyba a aktivace se zablokuje. Další informace najdete v na webu [Konfigurace certifikátů](azure-stack-edge-placeholder.md).
    
* Máte aktivační klíč ze služby Azure Stack Edge, kterou jste vytvořili pro správu zařízení Azure Stack Edge pro R. Další informace najdete na webu [Příprava nasazení Azure Stack Edge pro R](azure-stack-edge-pro-r-deploy-prep.md).


## <a name="activate-the-device"></a>Aktivovat zařízení

1. V místním webovém uživatelském rozhraní zařízení, **navštivte stránku Začínáme** .
2. Na dlaždici **Aktivace** vyberte **aktivovat**. 

    ![Místní webové uživatelské rozhraní "Podrobnosti o cloudu" Stránka 1](./media/azure-stack-edge-pro-r-deploy-activate/activate-1.png)
    
3. V podokně **Aktivace** :
    1. Zadejte **aktivační klíč** , který jste získali v [části získání aktivačního klíče pro Azure Stack Edge pro R](azure-stack-edge-pro-r-deploy-prep.md#get-the-activation-key).

    1. Můžete povolit proaktivní shromažďování protokolů, aby Microsoft mohl shromažďovat protokoly na základě stavu zařízení. Protokoly shromážděné tímto způsobem jsou odesílány do účtu Azure Storage.
    
    1. Vyberte **Použít**.

    ![Místní webové uživatelské rozhraní "Podrobnosti o cloudu" – Stránka 2](./media/azure-stack-edge-pro-r-deploy-activate/activate-2.png)


5. Nejdřív se zařízení aktivuje. Pak se zobrazí výzva ke stažení souboru klíče.
    
    ![Local web UI "Podrobnosti o cloudu" Stránka 3](./media/azure-stack-edge-pro-r-deploy-activate/activate-3.png)
    
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
> * Předpoklady
> * Aktivace fyzického zařízení

Informace o tom, jak přenést data pomocí Azure Stack hraničního zařízení pro R, najdete v těchto tématech:

> [!div class="nextstepaction"]
> [Přenos dat pomocí Azure Stack Edge pro R](./azure-stack-edge-j-series-deploy-add-shares.md)
