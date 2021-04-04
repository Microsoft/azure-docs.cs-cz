---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 10/27/2020
ms.author: alkohli
ms.openlocfilehash: efa9e996cbfbc8954c294b4da8900b1d5bcbeeed
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96466642"
---
K ověření sítě VPN můžete vytvořit účet úložiště, který může být k dispozici jenom pro virtuální síť, kterou jste vytvořili. Pomocí těchto kroků vytvořte a přidružte tento účet úložiště k virtuální síti, kterou jste vytvořili:

1. Vytvoření účtu úložiště Můžete použít účet úložiště, který plánujete použít se zařízením Azure Stack Edge. Zkuste získat přístup k účtu úložiště z externí sítě (mimo vybranou síť). Účet úložiště by měl být přístupný.
2. V Azure Portal přejít na účet úložiště. 
3. Přejít na **brány firewall a virtuální sítě**. V pravém podokně pro možnost **Povolení přístupu z** vyberte **vybrané sítě**. V části **zabezpečení našeho účtu úložiště s virtuálními sítěmi** vyberte **+ Přidat existující virtuální síť.**

    ![Ověřit VPN 1](../articles/databox-online/media/azure-stack-edge-pro-r-configure-vpn-powershell/verify-vpn-1.png)

4. V okně **Přidat sítě** :

    - Vyberte předplatné. Jedná se o stejné předplatné, které je přidruženo k vašemu Azure Stackmu prostředku Edge/Data Box Gateway. 
    - V rozevíracím seznamu vyberte virtuální síť, kterou chcete přidružit k tomuto účtu úložiště. Vyberte virtuální síť, kterou jste vytvořili v předchozím kroku.
    - V části **podsítě** vyberte **_výchozí_* _ a _GatewaySubnet *. Koncové body služby budou povolené pro tyto kombinace virtuální sítě a podsítě. Povolení přístupu trvá až 15 minut.
    - Vyberte **Povolit**.

    ![Ověření sítě VPN 2](../articles/databox-online/media/azure-stack-edge-pro-r-configure-vpn-powershell/verify-vpn-2.png)
    
4. Uložit **Nastavení**.

    ![Ověření sítě VPN 3](../articles/databox-online/media/azure-stack-edge-pro-r-configure-vpn-powershell/verify-vpn-3.png)

5. Po uložení nastavení můžete zobrazit podsítě, pro které je virtuální síť povolená.

    ![Ověření sítě VPN 4](../articles/databox-online/media/azure-stack-edge-pro-r-configure-vpn-powershell/verify-vpn-4.png)

5. Pokud chcete ověřit, že data se teď budou nacházet jenom přes VPN, postupujte podle těchto kroků: 
    - Zkuste získat přístup k účtu úložiště z externí sítě (mimo vybranou síť). Účet úložiště by neměl být přístupný. 
    - Zkuste získat přístup k účtu úložiště z virtuální sítě nebo podsítí, které jste povolili ve vybraných sítích. Účet úložiště by měl být přístupný. 
 
K tomuto účtu úložiště se dostanete jenom v případě, že je povolená síť VPN. Pokud síť VPN zakážete, budete také muset upravit nastavení sítě účtu úložiště. 

Další informace najdete v, pokud chcete [nakonfigurovat Azure Storage brány firewall a virtuální sítě](../articles/storage/common/storage-network-security.md). 

