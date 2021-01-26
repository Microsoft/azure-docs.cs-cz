---
title: Nastavení protokolu Kerberos služba KDC (Key Distribution Center) virtuální plocha Windows proxy serveru – Azure
description: Jak nastavit fond hostitelů pro virtuální počítače s Windows tak, aby používal proxy služba KDC (Key Distribution Center) protokolu Kerberos.
author: Heidilohr
ms.topic: how-to
ms.date: 01/26/2021
ms.author: helohr
manager: lizross
ms.openlocfilehash: f123659941eaeb3b8ceeb6a999abf836eb9cf5ea
ms.sourcegitcommit: 95c2cbdd2582fa81d0bfe55edd32778ed31e0fe8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/26/2021
ms.locfileid: "98798386"
---
# <a name="configure-a-kerberos-key-distribution-center-proxy"></a>Konfigurace proxy služba KDC (Key Distribution Center) protokolu Kerberos

V tomto článku se dozvíte, jak nakonfigurovat proxy služby KDC (Key Distribiution Center) protokolu Kerberos pro fond hostitelů. Tento proxy server umožňuje organizacím ověřovat pomocí protokolu Kerberos mimo jejich podnikové hranice. Pomocí služby KDC proxy můžete například povolit ověřování pomocí čipové karty u externích klientů.

## <a name="how-to-configure-the-kdc-proxy"></a>Jak nakonfigurovat proxy služby KDC

Konfigurace proxy služby KDC:

1. Přihlaste se k Azure Portal jako správce.

2. Přejít na stránku virtuální počítač s Windows.

3. Vyberte fond hostitelů, pro který chcete povolit službu KDC proxy, a pak vyberte **Vlastnosti protokolu RDP**.

    > [!div class="mx-imgBorder"]
    > ![Snímek obrazovky stránky Azure Portal znázorňující uživatele, který vybírá fondy hostitelů, potom název ukázkového fondu hostitelů a pak vlastnosti protokolu RDP.](media/rdp-properties.png)

4. Vyberte kartu **Upřesnit** a pak zadejte hodnotu v následujícím formátu bez mezer:

    > kdcproxyname: s:\<fqdn\>

    > [!div class="mx-imgBorder"]
    > ![Snímek obrazovky zobrazující vybranou kartu Upřesnit s hodnotou zadanou v kroku 4.](media/advanced-tab-selected.png)

5. Vyberte **Uložit**.

6. Vybraný fond hostitelů by teď měl začít vystavovat soubory připojení RDP s polem kdcproxyname, které jste zahrnuli.

## <a name="next-steps"></a>Další kroky

Role RDGateway ve službě Vzdálená plocha zahrnuje službu KDC proxy. V tématu [nasazení role Brána VP na virtuálním počítači s Windows](rd-gateway-role.md) najdete, jak nastavit jako cíl pro virtuální počítač s Windows.
