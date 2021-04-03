---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 10/27/2020
ms.author: alkohli
ms.openlocfilehash: fa65a7354112a2b220686372459b348d45832dd9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96466636"
---
V místním webovém uživatelském rozhraní zařízení proveďte následující kroky. Tento krok trvá přibližně 15 minut, včetně nahrávání konfiguračního souboru sítě VPN (nebo souboru s označením služby). 

1. Přejít na **konfigurační > VPN**. Vyberte **Konfigurovat**.

    ![Konfigurace místního uživatelského rozhraní 1](../articles/databox-online/media/azure-stack-edge-pro-r-configure-vpn-powershell/configure-vpn-local-ui-1.png)

2. V okně **Konfigurace sítě VPN** :

    - Povolte **nastavení sítě VPN**.
    - Zadejte **sdílený tajný klíč sítě VPN**. Toto je sdílený klíč, který jste zadali při vytváření prostředku připojení Azure VPN.
    - Zadejte **IP adresu brány VPN** . Toto je IP adresa brány místní sítě Azure.
    - V případě **skupiny PFS** vyberte **None (žádné**). 
    - V případě **skupiny DH** vyberte **skupina2**.
    - V případě **metody integrity protokolu IPSec** vyberte **SHA256**.
    - V případě **konstant transformace šifry IPSec** vyberte **GCMAES256**.
    - Pro **transformační konstanty ověřování IPsec** vyberte **GCMAES256**.
    - V případě **metody šifrování IKE** vyberte **AES256**.
    - Vyberte **Použít**.

        ![Konfigurace místního uživatelského rozhraní 2](../articles/databox-online/media/azure-stack-edge-pro-r-configure-vpn-powershell/configure-vpn-local-ui-2.png)

    Další informace o podporovaných kryptografických algoritmech najdete v části [informace o kryptografických požadavcích a branách Azure VPN](../articles/vpn-gateway/vpn-gateway-about-compliance-crypto.md#ipsecike-policy-faq). 

3. Pokud chcete nahrát konfigurační soubor směrování sítě VPN, vyberte **nahrát**. 

    ![Konfigurace místního uživatelského rozhraní 3](../articles/databox-online/media/azure-stack-edge-pro-r-configure-vpn-powershell/configure-vpn-local-ui-3.png)

    - Přejděte do souboru *JSON* značek služby, který jste stáhli v místním systému v předchozím kroku.
    - Vyberte oblast jako oblast Azure, která je přidružená k zařízení, virtuální síti a branám.
    - Vyberte **Použít**.

        ![Konfigurace místního uživatelského rozhraní 4](../articles/databox-online/media/azure-stack-edge-pro-r-configure-vpn-powershell/configure-vpn-local-ui-4.png)
    
    Nahrávání trvá přibližně 7-8 minut na zařízení.

4. Chcete-li přidat trasy specifické pro klienta, nakonfigurujte rozsahy IP adres, pro které bude k dispozici pouze pomocí sítě VPN. 

    - V části **rozsahy IP adres, ke kterým má být přístup jenom přes VPN**, vyberte **Konfigurovat**.
    - Zadejte platný rozsah IPv4 a vyberte **Přidat**. Opakováním kroků přidejte další rozsahy.
    - Vyberte **Použít**.

        ![Konfigurace místního uživatelského rozhraní 5](../articles/databox-online/media/azure-stack-edge-pro-r-configure-vpn-powershell/configure-vpn-local-ui-5.png)

