---
title: Upgrade virtuální sítě Azure z úrovně Basic na standardní-Azure Portal | Microsoft Docs
description: Pro větší funkčnost můžete upgradovat typ virtuální sítě WAN.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 11/04/2019
ms.author: cherylmc
ms.openlocfilehash: 6290d89ed0ee539b4df4c2c8bc9070097da98c81
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "91447363"
---
# <a name="upgrade-a-virtual-wan-from-basic-to-standard"></a>Upgrade virtuální sítě WAN z úrovně Basic na standard

Tento článek vám pomůže upgradovat základní síť WAN na standardní síť WAN. Typ "základní" síť WAN vytvoří všechna centra uvnitř IT jako základní centra SKU. V základním centru je jenom funkcí VPN typu Site-to-site. Typ standard sítě WAN vytvoří všechna rozbočovače v rámci IT jako standardní skladové centra SKU. Pokud používáte standardní centra, můžete povolit ExpressRoute, uživatele (Point-to-site) VPN, celou síť rozbočovač a průjezd VNet-to-VNet prostřednictvím Center Azure.

Následující tabulka uvádí dostupné konfigurace pro každý typ sítě WAN:

[!INCLUDE [Basic and Standard SKUs](../../includes/virtual-wan-standard-basic-include.md)]

## <a name="to-change-the-virtual-wan-type"></a>Změna typu virtuální sítě WAN

1. Na stránce pro virtuální síť WAN vyberte **Konfigurace** a otevřete stránku konfigurace.

   ![Snímek obrazovky se stránkou "konfigurace" se textovým polem o upgradu na virtuální síť WAN se standardním typem zvýrazněnou na konci.](./media/upgrade-virtual-wan/1.png)
2. Jako typ virtuální sítě WAN vyberte v rozevíracím seznamu možnost **standardní** .

   ![Snímek obrazovky, který zobrazuje rozevírací nabídku "typ virtuální sítě WAN".](./media/upgrade-virtual-wan/2.png)
3. Pochopte, že pokud upgradujete na standardní virtuální síť WAN, nebudete se moct vrátit zpátky na základní virtuální síť WAN. Vyberte **Potvrdit** , pokud chcete provést upgrade.

   ![Snímek obrazovky se zobrazeným dialogovým oknem pro potvrzení upgradu](./media/upgrade-virtual-wan/4.png)
4. Po uložení změn bude stránka Virtual WAN vypadat podobně jako v tomto příkladu.

   ![Diagram virtuální sítě WAN](./media/upgrade-virtual-wan/5.png)

## <a name="next-steps"></a>Další kroky

Další informace o službě Virtual WAN najdete v článku [Přehled služby Virtual WAN](virtual-wan-about.md).