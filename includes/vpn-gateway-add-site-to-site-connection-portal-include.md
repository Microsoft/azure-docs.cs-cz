---
title: zahrnout soubor
description: zahrnout soubor
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 04/04/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 0dcb591db5f487a103feccf92ffa577a1cbf8b23
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
ms.locfileid: "30921426"
---
1. Přejděte na stránku brány virtuální sítě a otevřete ji. K oknu se dostanete několika způsoby. K bráně VNet1GW se dostanete tak, že přejdete do části **TestVNet1 -> Přehled -> Připojená zařízení -> VNet1GW**.
2. Na stránce brány VNet1GW klikněte na **Připojení**. V horní části okna Připojení klikněte na **+ Přidat** a otevřete stránku **Přidat připojení**.

  ![Vytvoření připojení typu site-to-site](./media/vpn-gateway-add-site-to-site-connection-portal-include/configure-site-to-site-connection.png)
3. Na stránce **Přidat připojení** nakonfigurujte hodnoty pro vaše připojení.

  - **Název**: Zadejte název připojení.
  - **Typ připojení**: Vyberte **Site-to-site (IPSec)**.
  - **Brána virtuální sítě**: Tato hodnota je pevně daná, protože se připojujete z této brány.
  - **Brána místní sítě**: Klikněte na **Zvolit bránu místní sítě** a vyberte bránu místní sítě, kterou chcete použít.
  - **Sdílený klíč**: Tato hodnota musí odpovídat hodnotě, kterou používáte pro místní zařízení VPN. V příkladu se používá abc123, ale můžete (a měli byste) používat něco složitějšího. Důležité je, aby hodnota, kterou zde zadáte, byla stejná jako hodnota, kterou zadáte při konfiguraci zařízení VPN.
  - Hodnoty položek **Předplatné**, **Skupina prostředků** a **Umístění** jsou pevné.

4. Vytvořte připojení kliknutím na **OK**. Na obrazovce bude blikat text *Vytváří se připojení*.
5. Připojení si můžete zobrazit na stránce **Připojení** brány virtuální sítě. Stav se změní z *Neznámý* na *Připojování* a potom na *Úspěch*.