---
title: Kurz k šifrování a aktivaci zařízení Azure Stack hraničního konektoru R v Azure Portal | Microsoft Docs
description: Kurz pro nasazení Azure Stack hraničních zařízení na Mini R vám dává pokyn k zašifrování a aktivaci fyzického zařízení.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 10/22/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to activate Azure Stack Edge Mini R so I can use it to transfer data to Azure.
ms.openlocfilehash: e5076888e692ad16e89f30d455e7f8c4e22a41b9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96468344"
---
# <a name="tutorial-activate-azure-stack-edge-mini-r"></a>Kurz: Aktivace Azure Stack hraničního konektoru R

V tomto kurzu se dozvíte, jak můžete aktivovat Azure Stack hraničních zařízení R pomocí místního webového uživatelského rozhraní.

Dokončení procesu aktivace může trvat přibližně 15 minut.

V tomto kurzu jste se dozvěděli o:

> [!div class="checklist"]
> * Předpoklady
> * Aktivace fyzického zařízení

## <a name="prerequisites"></a>Předpoklady

Předtím, než nakonfigurujete a nastavíte Azure Stack hraničních zařízení v jazyce R, ujistěte se, že:

* Pro vaše fyzické zařízení: 
    
    - Nainstalovali jste fyzické zařízení, jak je podrobně popsáno v části [Install Azure Stack Edge Mini R](azure-stack-edge-mini-r-deploy-install.md).
    - Nakonfigurovali jste nastavení sítě a COMPUTE sítě, jak je popsáno v části [Konfigurace sítě, výpočetní sítě, webového proxy serveru](azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy.md) .
    - Nahráli jste vlastní certifikáty na zařízení, pokud jste změnili název zařízení nebo doménu DNS přes stránku **zařízení** . Tyto kroky jsou podrobně popsané v [části Konfigurace certifikátů, sítě VPN a šifrování v klidovém umístění](azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption.md). Pokud jste tento krok neudělali, aktivace se zablokuje.
    - Nakonfigurovali jste u svého zařízení šifrování v klidovém prostředí. Pokud jste tento krok neudělali, zobrazí se během aktivace zařízení chyba a aktivace se zablokuje. Další informace najdete [v části Konfigurace certifikátů, sítě VPN a šifrování v klidovém](azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption.md)prostředí.
    
* Máte aktivační klíč ze služby Azure Stack Edge, kterou jste vytvořili pro správu Azure Stack hraničního zařízení R. Další informace najdete v [přípravě na nasazení Azure Stack hraniční Mini R](azure-stack-edge-mini-r-deploy-prep.md).


## <a name="activate-the-device"></a>Aktivovat zařízení

1. V místním webovém uživatelském rozhraní zařízení, **navštivte stránku Začínáme** .
2. Na dlaždici **Aktivace** vyberte **aktivovat**. 

    ![Místní webové uživatelské rozhraní "Podrobnosti o cloudu" Stránka 1](./media/azure-stack-edge-mini-r-deploy-activate/activation-1.png)
    
3. V podokně **Aktivace** :
    1. Zadejte **aktivační klíč** , který jste získali v [části získání aktivačního klíče pro Azure Stack Edge pro R](azure-stack-edge-pro-r-deploy-prep.md#get-the-activation-key).

    1. Můžete povolit proaktivní shromažďování protokolů, aby Microsoft mohl shromažďovat protokoly na základě stavu zařízení. Protokoly shromážděné tímto způsobem jsou odesílány do účtu Azure Storage.
    
    1. Vyberte **Použít**.

    ![Místní webové uživatelské rozhraní "Podrobnosti o cloudu" – Stránka 2](./media/azure-stack-edge-mini-r-deploy-activate/activation-2.png)


5. Nejdřív se zařízení aktivuje. Pak se zobrazí výzva ke stažení souboru klíče.
    
    ![Local web UI "Podrobnosti o cloudu" Stránka 3](./media/azure-stack-edge-mini-r-deploy-activate/activation-3.png)
    
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
      "SEDEncryptionExternalKey": "xZM/vC7GxjqHZ3VMo7xSs/wH9rRsF/PNM+mOsZ+GaL0=",
      "ServiceEncryptionKey": "<Azure service encryption key>"
    }
    ```        
 
    Různé klíče jsou vysvětleny v následující tabulce:
    
    |Pole  |Description  |
    |---------|---------|
    |`Id`    | Toto je ID zařízení.        |
    |`DataVolumeBitLockerExternalKeys`|Toto jsou klíče BitLockeru pro datové disky a slouží k obnovení místních dat v zařízení.|
    |`SystemVolumeBitLockerRecoveryKey`| Toto je klíč BitLockeru pro systémový svazek. Tento klíč pomáhá obnovit konfiguraci systému a systémová data pro vaše zařízení. |
    |`SEDEncryptionExternalKey`| Zadaný uživatel nebo klíč vygenerovaný systémem slouží k ochraně místních datových jednotek, které mají integrované šifrování. |
    |`ServiceEncryptionKey`| Tento klíč chrání tok dat prostřednictvím služby Azure. Tento klíč zajistí, že ohrožení zabezpečení služby Azure nebude mít za následek ohrožení uložených informací. |

6. Přejít na stránku **Přehled** . Stav zařízení by se měl zobrazovat jako **aktivovaný**.

    ![Místní webové uživatelské rozhraní "Podrobnosti o cloudu" na stránce 4](./media/azure-stack-edge-mini-r-deploy-activate/activation-4.png)
 
Aktivace zařízení je dokončená. V zařízení teď můžete přidat sdílené složky.

Pokud narazíte na nějaké problémy při aktivaci, přečtěte si [řešení potíží s aktivací a Azure Key Vault chyby](azure-stack-edge-gpu-troubleshoot-activation.md#activation-errors).

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se dozvěděli o:

> [!div class="checklist"]
> * Předpoklady
> * Aktivace fyzického zařízení

Pokud se chcete dozvědět, jak přenést data pomocí Azure Stackového Miniho zařízení R, přečtěte si:

> [!div class="nextstepaction"]
> [Přenos dat pomocí Azure Stackového Miniku R](./azure-stack-edge-j-series-deploy-add-shares.md)
