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
ms.openlocfilehash: 8b338f25e9771f5947fd494cfb00d0f6cb9ef67a
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "75450843"
---
1. Výběrem **možnosti Připojit weby VPN** otevřete stránku **Připojit weby.**

    ![Připojit](./media/virtual-wan-tutorial-connect-vpn-site-include/connect.png "připojit")

   Vyplňte následující pole:

   * Zadejte předsdílený klíč. Pokud nezadáte klíč, Azure automaticky vygeneruje jeden pro vás.
   * Vyberte nastavení protokolu a protokolu IPsec. Viz [podrobnosti o výchozí/vlastní ipsec] (https://docs.microsoft.com/azure/virtual-wan/virtual-wan-ipsec)
   * Vyberte příslušnou možnost pro **šíření výchozí trasy**. Možnost **Povolit** umožňuje virtuálnímu rozbočovači šířit naučenou výchozí trasu k tomuto připojení. Tento příznak umožňuje šíření výchozí trasy k připojení pouze v případě, že výchozí trasa je již zjistit virtuální wan rozbočovač v důsledku nasazení brány firewall v rozbočovači, nebo pokud jiná připojená lokalita má vynucené tunelové propojení povoleno. Výchozí trasa nepochází z rozbočovače Virtuální WAN.

2. Vyberte **Connect** (Připojit).
3. Během několika minut se na webu zobrazí stav připojení a připojení.

   ![status](./media/virtual-wan-tutorial-connect-vpn-site-include/status.png "status")

   **Stav připojení:** Toto je stav prostředku Azure pro připojení, které spojuje web VPN s bránou VPN centra Azure Hub. Jakmile bude tato operace roviny ovládacího prvku úspěšná, brána Azure VPN a místní zařízení VPN přistoupí k navázání připojení.

   **Stav připojení:** Toto je stav skutečného připojení (datové cesty) mezi bránou VPN Azure v rozbočovači a na webu VPN. Může zobrazit některý z následujících stavů:

    * **Neznámý**: Tento stav se obvykle zobrazí, pokud back-endové systémy pracují na přechodu na jiný stav.
    * **Připojení**: Brána Azure VPN se pokouší oslovit skutečný místní web VPN.
    * **Připojení**: Připojení se nastoupí mezi bránou Azure VPN a místním webem VPN.
    * **Odpojeno**: Tento stav se zhlédne, pokud z nějakého důvodu (místní nebo v Azure) bylo připojení odpojeno.
4. V rámci webu sítě VPN centra můžete provést další akce: 

   * Upravte nebo odstraňte připojení VPN.
   * Odstraňte web na webu Azure Portal.
   * Stáhněte si konfiguraci specifickou pro pobočku, kde nazíte podrobnosti o straně Azure pomocí kontextové nabídky (...) vedle webu. Pokud chcete stáhnout konfiguraci pro všechny připojené weby ve vašem centru, vhorní nabídce vyberte **Stáhnout konfiguraci VPN.**
