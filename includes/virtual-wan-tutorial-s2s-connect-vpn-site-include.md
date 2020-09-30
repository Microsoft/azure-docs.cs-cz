---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 11/04/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 897e36a6c5165549d7809512d0298fa2cfed2fa8
ms.sourcegitcommit: 6e1124fc25c3ddb3053b482b0ed33900f46464b3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/15/2020
ms.locfileid: "90606153"
---
1. Výběrem **připojit weby sítě VPN** otevřete stránku **připojit weby** .

    ![Snímek obrazovky se zobrazí podokno připojené weby pro virtuální centrum, které je připravené na předsdílený klíč a přidružená nastavení.](./media/virtual-wan-tutorial-connect-vpn-site-include/connect.png "připojit")

   Vyplňte následující pole:

   * Zadejte předsdílený klíč. Pokud nezadáte klíč, Azure ho pro vás automaticky vygeneruje.
   * Vyberte nastavení protokolu a protokolu IPsec. Podívejte se na [výchozí/vlastní podrobnosti IPSec] (https://docs.microsoft.com/azure/virtual-wan/virtual-wan-ipsec)
   * Vyberte vhodnou možnost pro **rozšířit výchozí trasu**. Možnost **Povolit** umožňuje virtuálnímu rozbočovači rozšířit naučenou výchozí trasu k tomuto připojení. Tento příznak umožňuje rozšíření výchozí trasy na připojení pouze v případě, že je výchozí trasa již vyučena virtuálním centrem sítě WAN v důsledku nasazení brány firewall v centru nebo v případě, že je povoleno vynucené tunelování na jiném připojeném serveru. Výchozí trasa nepochází do virtuálního centra WAN.

2. Vyberte **Připojit**.
3. Během několika minut se v lokalitě zobrazí stav připojení a připojení.

   ![Snímek obrazovky ukazuje připojení lokality V nasazení lokality a stav připojení.](./media/virtual-wan-tutorial-connect-vpn-site-include/status.png "status")

   **Stav připojení:** Toto je stav prostředku Azure pro připojení, které spojuje lokalitu VPN s bránou sítě VPN v centru Azure. Po úspěšné operaci této roviny ovládacího prvku budou brány Azure VPN a místní zařízení VPN pokračovat v navázání připojení.

   **Stav připojení:** Toto je skutečný stav připojení (cesta k datům) mezi bránou VPN Azure v centru a lokalitě VPN. Může zobrazit některý z následujících stavů:

    * **Neznámé**: Tento stav je obvykle zobrazený v případě, že back-end systémy pracují na přechod na jiný stav.
    * **Připojování**: Brána Azure VPN Gateway se snaží o přístup k aktuálnímu místnímu webu VPN.
    * **Připojeno**: připojení mezi službou Azure VPN Gateway a místní sítí VPN je navázáno.
    * **Odpojeno**: Tento stav se zobrazuje, pokud z jakéhokoli důvodu (v místním prostředí nebo v Azure) se připojení odpojilo.
4. V rámci lokality sítě VPN Hub můžete také provést následující akce: 

   * Upravte nebo odstraňte připojení VPN.
   * Odstraní lokalitu v Azure Portal.
   * Stáhněte si konfiguraci specifickou pro každou větev, kde najdete podrobnosti o straně Azure pomocí kontextové nabídky (...) vedle webu. Pokud chcete stáhnout konfiguraci pro všechny připojené weby v centru, vyberte v horní nabídce **Stáhnout konfiguraci sítě VPN** .
