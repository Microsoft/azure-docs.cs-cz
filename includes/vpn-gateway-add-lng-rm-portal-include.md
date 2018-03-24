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
ms.openlocfilehash: 932aab3f16a571d4c83c77c1cc2274ae60ea3d35
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
1. Na portálu v části **Všechny prostředky** klikněte na **+Přidat**.
2. V **všechno, co** stránky vyhledávacího pole, typ **brány místní sítě**, a pak klepněte na vrátí seznam prostředků. Kliknutím na **Brána místní sítě** otevřete stránku a potom kliknutím na **Vytvořit** otevřete stránku **Vytvořit bránu místní sítě**.

  ![Vytvoření brány místní sítě](./media/vpn-gateway-add-lng-rm-portal-include/lng.png)
3. Na stránce **Vytvořit bránu místní sítě** zadejte hodnoty brány místní sítě.

  - **Název**: Zadejte název objektu brány místní sítě. Pokud je to možné, používejte něco intuitivní, například **ClassicVNetLocal** nebo **TestVNet1Local**. Díky tomu je snazší identifikaci bránu místní sítě na portálu.
  - **IP adresa:** zadejte platné veřejné **IP adresu** pro zařízení VPN nebo brány virtuální sítě, ke kterému se chcete připojit.

    * **Pokud tento místní síť představuje místní umístění:** zadat adresu veřejnou IP adresu, kterou chcete připojit k zařízení VPN. IP adresa nemůže být za serverem NAT a musí být dostupná pro Azure.
    * **Pokud tato místní síť představuje jiné virtuální síti:** zadejte veřejnou IP adresu, která byla přiřazena bránu virtuální sítě pro tuto virtuální síť.
    * **Pokud ještě nemáte IP adresu:** tvoří platný zástupný IP adresa a pak se vrátit a změnit toto nastavení před připojením.
  - **Adresní prostor** odkazuje na rozsahy adres sítě, kterou tato místní síť představuje. Můžete přidat více různých rozsahů adres. Ujistěte se, že rozsahy, který zde určíte nepřekrývají s rozsahy jiné sítě, ke které se připojujete.
  - **Konfigurovat nastavení protokolu BGP:** Používejte jenom při konfiguraci BGP. V jiných případech tuto možnost nevybírejte.
  - **Předplatné**: Zkontrolujte, že se zobrazuje správné předplatné.
  - **Skupina prostředků**: Vyberte skupinu prostředků, kterou chcete použít. Můžete vytvořit novou skupinu prostředků, nebo vybrat skupinu prostředků, kterou jste už vytvořili.
  - **Umístění**: Vyberte umístění, ve kterém se tento objekt vytvoří. Můžete vybrat stejné umístění, ve kterém se nachází vaše virtuální síť, ale není to povinné.
4. Kliknutím na **Vytvořit** vytvořte bránu místní sítě.