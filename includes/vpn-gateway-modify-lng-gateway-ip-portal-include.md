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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "67174842"
---
### <a name="to-modify-the-local-network-gateway-ip-address---no-gateway-connection"></a><a name="gwipnoconnection"></a>Změna IP adresy brány místní sítě - žádné připojení brány

Pomocí tohoto příkladu upravte bránu místní sítě, která nemá připojení brány. Při upravování této hodnoty můžete také zároveň upravit předpony adres.

1. V prostředku brány místní sítě klikněte v části **Nastavení** na **položku Konfigurace**.
2. V poli **IP adresa** upravte adresu IP.
3. Kliknutím na **Uložit** nastavení uložte.

### <a name="to-modify-the-local-network-gateway-ip-address---existing-gateway-connection"></a><a name="gwipwithconnection"></a>Úprava IP adresy místní síťové brány - existující připojení brány

Chcete-li upravit bránu místní sítě, která má připojení, je třeba nejprve odebrat připojení. Po odebrání připojení můžete upravit IP adresu brány a vytvořit nové připojení. Můžete také zároveň upravit předpony adresy. Způsobí to určitý výpadek připojení VPN. Při upravování IP adresy brány není potřeba odstraňovat bránu VPN. Stačí jenom odebrat připojení.
 
#### <a name="1-remove-the-connection"></a>1. Odstraňte připojení.

1. V prostředku brány místní sítě klikněte v části **Nastavení** na **položku Připojení**.
2. Klepněte na tlačítko **...** na řádku připojení a potom klepněte na tlačítko **Odstranit**.
3. Kliknutím na **Uložit** nastavení uložte.

#### <a name="2-modify-the-ip-address"></a>2. Upravte IP adresu.

Můžete také zároveň upravit předpony adresy.

1. V poli **IP adresa** upravte adresu IP.
2. Kliknutím na **Uložit** nastavení uložte.

#### <a name="3-recreate-the-connection"></a>3. Znovu vytvořte připojení.

1. Přejděte do brány virtuální sítě pro vaši virtuální síť. (Není brána místní sítě.)
2. V bráně virtuální sítě klikněte v části **Nastavení** na **položku Připojení**.
3. Kliknutím na **tlačítko + Přidat** otevřete okno Přidat **připojení.**
4. Znovu vytvořte připojení.
5. Chcete-li vytvořit připojení, klepněte na tlačítko **OK.**