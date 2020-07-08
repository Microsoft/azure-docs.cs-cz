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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "67174842"
---
### <a name="to-modify-the-local-network-gateway-ip-address---no-gateway-connection"></a><a name="gwipnoconnection"></a>Úprava IP adresy místní síťové brány – žádné připojení brány

Pomocí tohoto příkladu upravte bránu místní sítě, která nemá připojení brány. Při upravování této hodnoty můžete také zároveň upravit předpony adres.

1. V prostředku brány místní sítě v části **Nastavení** klikněte na **Konfigurace**.
2. V poli **IP adresa** upravte IP adresu.
3. Kliknutím na **Uložit** nastavení uložte.

### <a name="to-modify-the-local-network-gateway-ip-address---existing-gateway-connection"></a><a name="gwipwithconnection"></a>Úprava IP adresy brány místní sítě – existující připojení brány

Pokud chcete upravit bránu místní sítě, která má připojení, musíte nejdřív odebrat připojení. Po odebrání připojení můžete upravit IP adresu brány a vytvořit nové připojení. Můžete také zároveň upravit předpony adresy. Způsobí to určitý výpadek připojení VPN. Při upravování IP adresy brány není potřeba odstraňovat bránu VPN. Stačí jenom odebrat připojení.
 
#### <a name="1-remove-the-connection"></a>1. odeberte připojení.

1. V prostředku brány místní sítě v části **Nastavení** klikněte na **připojení**.
2. Klikněte na **...** na řádku připojení a pak klikněte na **Odstranit**.
3. Uložte nastavení kliknutím na **Uložit** .

#### <a name="2-modify-the-ip-address"></a>2. Upravte IP adresu.

Můžete také zároveň upravit předpony adresy.

1. V poli **IP adresa** upravte IP adresu.
2. Kliknutím na **Uložit** nastavení uložte.

#### <a name="3-recreate-the-connection"></a>3. znovu vytvořte připojení.

1. Přejděte do brány Virtual Network pro vaši virtuální síť. (Nejedná se o bránu místní sítě.)
2. V bráně Virtual Network vyberte v části **Nastavení** možnost **připojení**.
3. Kliknutím na **+ Přidat** otevřete okno **Přidat připojení** .
4. Znovu vytvořte připojení.
5. Kliknutím na tlačítko **OK** vytvořte připojení.