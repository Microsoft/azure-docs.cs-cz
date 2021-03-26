---
title: Konfigurace TLS 1,2 na klientech Windows, kteří přistupují k zařízení GPU Azure Stack Edge pro
description: Popisuje postup konfigurace TLS 1,2 na klientech Windows, kteří přistupují k zařízení GPU Azure Stack Edge pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 47a6d3bbebdf3b2b14b1c40d7ea8fc93b4d19c6f
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2021
ms.locfileid: "105565329"
---
# <a name="configure-tls-12-on-windows-clients-accessing-azure-stack-edge-pro-device"></a>Konfigurace TLS 1,2 na klientech Windows, kteří přistupují k zařízení Azure Stack Edge pro

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Pokud pro přístup k zařízení Azure Stack Edge pro používáte klienta se systémem Windows, je nutné nakonfigurovat TLS 1,2 na vašem klientovi. Tento článek poskytuje zdroje informací a pokyny ke konfiguraci TLS 1,2 na vašem klientovi Windows. 

Tady uvedené pokyny jsou založené na testování prováděném na klientovi se systémem Windows Server 2016.

## <a name="configure-tls-12-for-current-powershell-session"></a>Nakonfigurujte TLS 1,2 pro aktuální relaci PowerShellu.

Proveďte následující kroky a nakonfigurujte TLS 1,2 na vašem klientovi.

1. Spusťte PowerShell jako správce.
2. Pro nastavení TLS 1,2 pro aktuální relaci PowerShellu zadejte:
  
    ```azurepowershell
    $TLS12Protocol = [System.Net.SecurityProtocolType] 'Ssl3 , Tls12'
    [System.Net.ServicePointManager]::SecurityProtocol = $TLS12Protocol
    ```
## <a name="configure-tls-12-on-client"></a>Konfigurace TLS 1,2 na klientovi

Pokud chcete pro své prostředí nastavit protokol TLS 1,2 pro systém, postupujte podle pokynů v těchto dokumentech:

- [Obecné – jak povolit TLS 1,2](/windows-server/security/tls/tls-registry-settings#tls-12)
- [Jak povolit TLS 1,2 na klientech](/configmgr/core/plan-design/security/enable-tls-1-2-client)
- [Postup povolení protokolu TLS 1,2 na serverech lokality a vzdálených systémech lokality](/configmgr/core/plan-design/security/enable-tls-1-2-server)
- [Protokoly v TLS/SSL (Schannel SSP)](/windows-server/security/tls/manage-tls#configuring-tls-ecc-curve-order)
- [Šifrovací sady](/windows-server/security/tls/tls-registry-settings#tls-12): konkrétně [Konfigurace pořadí šifrovacích sad TLS](/windows-server/security/tls/manage-tls#configuring-tls-cipher-suite-order) , ujistěte se, že jste si vypíšete aktuální šifrovací sady a v následujícím seznamu předřadíte všechny chybějící:

    - TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
    - TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
    - TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
    - TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384

    Tyto šifrovací sady můžete také přidat přímou úpravou nastavení registru.

    ```azurepowershell
    New-ItemProperty -Path "$HklmSoftwarePath\Policies\Microsoft\Cryptography\Configuration\SSL\00010002" -Name "Functions"  -PropertyType String -Value ("TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384, TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384, TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384,TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384")
    ```

- Postup nastavení eliptických křivek

    Ujistěte se, že jste provedli výpis aktuálních eliptických křivek a v následujícím seznamu předřadíte všechny chybějící:

    - P-256 
    - P-384

    Tyto eliptické křivky můžete přidat také přímo úpravou nastavení registru.
    
    ```azurepowershell
    New-ItemProperty -Path "$HklmSoftwarePath\Policies\Microsoft\Cryptography\Configuration\SSL\00010002" -Name "EccCurves" -PropertyType MultiString -Value @("NistP256", "NistP384")
    ```
    
    - [Nastavte minimální velikost výměny klíčů RSA na 2048](/windows-server/security/tls/tls-registry-settings#keyexchangealgorithm---client-rsa-key-sizes).



## <a name="next-steps"></a>Další kroky

[Připojení k Azure Resource Manager](./azure-stack-edge-gpu-connect-resource-manager.md)