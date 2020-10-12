---
title: zahrnout soubor
description: zahrnout soubor
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 08/02/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 5149973fe63f867b49e55c970779c005e12536b9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "68780228"
---
1. Otevřete stránku pro bránu virtuální sítě. K oknu se dostanete několika způsoby. Přejít na bránu můžete tak, že přejdete na **název sítě VNet-> Overview-> připojená zařízení – > název vaší brány**.
2. Na stránce brány klikněte na **připojení**. V horní části okna Připojení klikněte na **+ Přidat** a otevřete stránku **Přidat připojení**.

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
