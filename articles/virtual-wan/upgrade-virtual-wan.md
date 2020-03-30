---
title: Upgrade virtuální sítě AZURE wan ze základní na standardní – portál Azure | Dokumenty společnosti Microsoft
description: Pro lepší funkčnost můžete upgradovat virtuální typ sítě WAN.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: cherylmc
ms.openlocfilehash: 83fd5bafb5496908403c50dc0e000fd33a836c95
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73515808"
---
# <a name="upgrade-a-virtual-wan-from-basic-to-standard"></a>Upgrade virtuální sítě WAN ze základní na standardní

Tento článek vám pomůže upgradovat základní wan na standardní WAN. Typ Wan Basic vytvoří všechny rozbočovače uvnitř jako základní rozbočovače sku. V centru Basic jste omezeni pouze na funkce VPN site-to-site. Typ WAN standardu vytvoří všechny rozbočovače uvnitř jako standardní rozbočovače SKU. Když používáte standardní rozbočovače, můžete povolit ExpressRoute, User (Point-to-site) VPN, úplné síťové centrum a tranzit virtuální sítě na virtuální síť přes centra Azure.

V následující tabulce jsou uvedeny konfigurace, které jsou k dispozici pro každý typ sítě WAN:

[!INCLUDE [Basic and Standard SKUs](../../includes/virtual-wan-standard-basic-include.md)]

## <a name="to-change-the-virtual-wan-type"></a>Změna typu virtuální sítě WAN

1. Na stránce virtuální sítě WAN otevřete stránku Konfigurace kliknutím na **konfiguraci.**

   ![Diagram virtuální sítě WAN](./media/upgrade-virtual-wan/1.png)
2. Pro typ Virtuální WAN vyberte v rozevíracím seznamu **standardní.**

   ![Diagram virtuální sítě WAN](./media/upgrade-virtual-wan/2.png)
3. Uvědomte si, že pokud upgradujete na standardní virtuální wan, nelze vrátit zpět do základní virtuální sítě WAN. Vyberte **Potvrdit,** jestli chcete upgradovat.

   ![Diagram virtuální sítě WAN](./media/upgrade-virtual-wan/4.png)
4. Po uložení změny vypadá virtuální stránka WAN podobně jako v tomto příkladu.

   ![Diagram virtuální sítě WAN](./media/upgrade-virtual-wan/5.png)

## <a name="next-steps"></a>Další kroky

Další informace o službě Virtual WAN najdete v článku [Přehled služby Virtual WAN](virtual-wan-about.md).