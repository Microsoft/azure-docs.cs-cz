---
title: zahrnout soubor
description: zahrnout soubor
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/22/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 48f33514510618abadf329a11a9ab71a020be0bd
ms.sourcegitcommit: 4b76c284eb3d2b81b103430371a10abb912a83f4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/01/2020
ms.locfileid: "92479548"
---
1. Otevřete stránku pro bránu virtuální sítě. Můžete přejít na bránu tak, že přejdete na **název sítě VNet-> Overview-> připojená zařízení – > název brány** , i když existuje několik dalších způsobů, jak také přejít.
1. Na stránce brány vyberte **připojení** . V horní části stránky připojení vyberte **+ Přidat** a otevřete stránku **Přidat připojení** .

   :::image type="content" source="./media/vpn-gateway-add-site-to-site-connection-portal-include/connection.png" alt-text="Připojení Site-to-site":::
1. Na stránce **Přidat připojení** nakonfigurujte hodnoty pro vaše připojení.

   * **Název** : Zadejte název připojení.
   * **Typ připojení:** Vyberte **site-to-Site (IPSec)** .
   * **Brána virtuální sítě** : Tato hodnota je pevně daná, protože se připojujete z této brány.
   * **Brána místní sítě:** Vyberte **zvolit bránu místní sítě** a vyberte bránu místní sítě, kterou chcete použít.
   * **Sdílený klíč** : Tato hodnota musí odpovídat hodnotě, kterou používáte pro místní zařízení VPN. V příkladu se používá abc123, ale můžete (a měli byste) používat něco složitějšího. Důležité je, aby hodnota, kterou zde zadáte, byla stejná jako hodnota, kterou zadáte při konfiguraci zařízení VPN.
   * Nechte **použít privátní IP adresu Azure** nezaškrtnutou.
   * Nechejte **možnost povolit protokol BGP** nezaškrtnutou.
   * Vyberte **IKEv2** .
   * Hodnoty položek **Předplatné** , **Skupina prostředků** a **Umístění** jsou pevné.

1. Vyberte **OK** a vytvořte připojení. Na obrazovce bude blikat text *Vytváří se připojení* .
1. Připojení si můžete zobrazit na stránce **Připojení** brány virtuální sítě. Stav se změní z *Neznámý* na *Připojování* a potom na *Úspěch* .
