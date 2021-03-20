---
title: Nastavení protokolu Kerberos služba KDC (Key Distribution Center) virtuální plocha Windows proxy serveru – Azure
description: Jak nastavit fond hostitelů pro virtuální počítače s Windows tak, aby používal proxy služba KDC (Key Distribution Center) protokolu Kerberos.
author: Heidilohr
ms.topic: how-to
ms.date: 01/30/2021
ms.author: helohr
manager: lizross
ms.openlocfilehash: 102ddc1c8937c66a92416ddb6d5f2d25f2a3c349
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "99219651"
---
# <a name="configure-a-kerberos-key-distribution-center-proxy-preview"></a>Konfigurace proxy služba KDC (Key Distribution Center) protokolu Kerberos (Preview)

> [!IMPORTANT]
> Tato funkce je aktuálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučujeme ji používat pro produkční úlohy. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

V tomto článku se dozvíte, jak nakonfigurovat proxy služba KDC (Key Distribution Center) protokolu Kerberos (KDC) pro fond hostitelů (Preview). Tento proxy server umožňuje organizacím ověřovat pomocí protokolu Kerberos mimo jejich podnikové hranice. Pomocí služby KDC proxy můžete například povolit ověřování pomocí čipové karty u externích klientů.

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
