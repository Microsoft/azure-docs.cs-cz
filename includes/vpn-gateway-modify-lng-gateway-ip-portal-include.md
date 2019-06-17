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
ms.openlocfilehash: 52084b065ef65a69a6691b6646d1e199f011910d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66121016"
---
### <a name="gwipnoconnection"></a> Úprava IP adresy brány místní sítě – žádné připojení brány

Pomocí tohoto příkladu upravte bránu místní sítě, která nemá připojení brány. Při upravování této hodnoty můžete také zároveň upravit předpony adres.

1. V prostředku brány místní sítě v **nastavení** klikněte na tlačítko **konfigurace**.
2. V **IP adresu** pole, změnit IP adresu.
3. Kliknutím na **Uložit** nastavení uložte.

### <a name="gwipwithconnection"></a>Úprava IP adresy místní síťové brány – existující připojení brány

Pokud chcete upravit bránu místní sítě, která nemá připojení, musíte nejprve odeberete připojení. Po odebrání připojení můžete upravit IP adresu brány a vytvořit nové připojení. Můžete také zároveň upravit předpony adresy. Způsobí to určitý výpadek připojení VPN. Při upravování IP adresy brány není potřeba odstraňovat bránu VPN. Stačí jenom odebrat připojení.
 
#### <a name="1-remove-the-connection"></a>1. Odeberte připojení.

1. V prostředku brány místní sítě v **nastavení** klikněte na tlačítko **připojení**.
2. Klikněte na tlačítko **...**  na řádek pro připojení, potom klikněte na tlačítko **odstranit**.
3. Klikněte na tlačítko **Uložit** uložte nastavení.

#### <a name="2-modify-the-ip-address"></a>2. Úprava IP adresy.

Můžete také zároveň upravit předpony adresy.

1. V **IP adresu** pole, změnit IP adresu.
2. Kliknutím na **Uložit** nastavení uložte.

#### <a name="3-recreate-the-connection"></a>3. Znovu vytvořte připojení.

1. Přejděte k bráně virtuální sítě pro vaši virtuální síť. (Nikoli brány místní sítě.)
2. Na Brána virtuální sítě v **nastavení** klikněte na tlačítko **připojení**.
3. Klikněte na tlačítko **+ přidat** otevřít **přidat připojení** okno.
4. Znovu vytvořte připojení.
5. Klikněte na tlačítko **OK** k vytvoření připojení.