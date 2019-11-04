---
title: Upgrade virtuální sítě Azure z úrovně Basic na standardní-Azure Portal | Microsoft Docs
description: Pro větší funkčnost můžete upgradovat typ virtuální sítě WAN.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: cherylmc
ms.openlocfilehash: 83fd5bafb5496908403c50dc0e000fd33a836c95
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73515808"
---
# <a name="upgrade-a-virtual-wan-from-basic-to-standard"></a>Upgrade virtuální sítě WAN z úrovně Basic na standard

Tento článek vám pomůže upgradovat základní síť WAN na standardní síť WAN. Typ "základní" síť WAN vytvoří všechna centra uvnitř IT jako základní centra SKU. V základním centru je jenom funkcí VPN typu Site-to-site. Typ standard sítě WAN vytvoří všechna rozbočovače v rámci IT jako standardní skladové centra SKU. Pokud používáte standardní centra, můžete povolit ExpressRoute, uživatele (Point-to-site) VPN, celou síť rozbočovač a průjezd VNet-to-VNet prostřednictvím Center Azure.

Následující tabulka uvádí dostupné konfigurace pro každý typ sítě WAN:

[!INCLUDE [Basic and Standard SKUs](../../includes/virtual-wan-standard-basic-include.md)]

## <a name="to-change-the-virtual-wan-type"></a>Změna typu virtuální sítě WAN

1. Na stránce pro virtuální síť WAN vyberte **Konfigurace** a otevřete stránku konfigurace.

   ![Diagram virtuální sítě WAN](./media/upgrade-virtual-wan/1.png)
2. V rozevíracím seznamu typ virtuální sítě WAN vyberte možnost **Standard** .

   ![Diagram virtuální sítě WAN](./media/upgrade-virtual-wan/2.png)
3. Pochopte, že pokud upgradujete na standardní virtuální síť WAN, nebudete se moct vrátit zpátky na základní virtuální síť WAN. Vyberte **Potvrdit** , pokud chcete provést upgrade.

   ![Diagram virtuální sítě WAN](./media/upgrade-virtual-wan/4.png)
4. Po uložení změn bude stránka Virtual WAN vypadat podobně jako v tomto příkladu.

   ![Diagram virtuální sítě WAN](./media/upgrade-virtual-wan/5.png)

## <a name="next-steps"></a>Další kroky

Další informace o službě Virtual WAN najdete v článku [Přehled služby Virtual WAN](virtual-wan-about.md).