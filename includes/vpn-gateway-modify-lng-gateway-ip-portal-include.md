---
title: zahrnout soubor
description: zahrnout soubor
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: a929149f115d716bf7f9d850abe5ba97bd5a8189
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
### <a name="gwipnoconnection"></a> Chcete-li upravit IP adresu brány místní sítě – žádné připojení brány

Pomocí tohoto příkladu upravte bránu místní sítě, která nemá připojení brány. Při upravování této hodnoty můžete také zároveň upravit předpony adres.

1. Na bráně místní sítě prostředku v **nastavení** klikněte na tlačítko **konfigurace**.
2. V **IP adresu** pole, je třeba změnit IP adresu.
3. Kliknutím na **Uložit** nastavení uložte.

### <a name="gwipwithconnection"></a>Chcete-li upravit místní síťová brána brány IP adresa - existující připojení brány

Pokud chcete upravit bránu místní sítě, který má připojení, musíte nejdříve odebrat připojení. Po odebrání připojení můžete upravit IP adresu brány a vytvořit nové připojení. Můžete také zároveň upravit předpony adresy. Způsobí to určitý výpadek připojení VPN. Při upravování IP adresy brány není potřeba odstraňovat bránu VPN. Stačí jenom odebrat připojení.
 
#### <a name="1-remove-the-connection"></a>1. Odeberte připojení.

1. Na bráně místní sítě prostředku v **nastavení** klikněte na tlačítko **připojení**.
2. Klikněte **...**  na řádek pro připojení klikněte **odstranit**.
3. Klikněte na tlačítko **Uložit** uložte nastavení.

#### <a name="2-modify-the-ip-address"></a>2. Upravte IP adresu.

Můžete také zároveň upravit předpony adresy.

1. V **IP adresu** pole, je třeba změnit IP adresu.
2. Kliknutím na **Uložit** nastavení uložte.

#### <a name="3-recreate-the-connection"></a>3. Znovu vytvořte připojení.

1. Přejděte k bráně virtuální sítě pro virtuální síť. (Nikoli brány místní sítě.)
2. Na bráně virtuální sítě v **nastavení** klikněte na tlačítko **připojení**.
3. Klikněte **+ přidat** otevřete **přidat připojení** okno.
4. Znovu vytvořte připojení.
5. Klikněte na tlačítko **OK** k vytvoření připojení.