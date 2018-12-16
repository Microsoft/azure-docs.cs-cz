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
ms.openlocfilehash: d9825ea41937dc9436fe8b465b48b378e13407c1
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/15/2018
ms.locfileid: "53444044"
---
1. Na portálu v části **Všechny prostředky** klikněte na **+Přidat**.
2. V **všechno, co** stránku vyhledávací pole, typ **bránu místní sítě**, pak klikněte na tlačítko se seznam prostředků. Kliknutím na **Brána místní sítě** otevřete stránku a potom kliknutím na **Vytvořit** otevřete stránku **Vytvořit bránu místní sítě**.

   ![Vytvoření brány místní sítě](./media/vpn-gateway-add-lng-rm-portal-include/lng.png)
3. Na stránce **Vytvořit bránu místní sítě** zadejte hodnoty brány místní sítě.

   - **Jméno:** Zadejte název objektu brány místní sítě. Pokud je to možné, používat něco intuitivní, jako například **ClassicVNetLocal** nebo **TestVNet1Local**. Díky tomu snadněji identifikovat bránu místní sítě na portálu.
   - **IP adresa:** Zadejte platnou veřejnou **IP adresu** pro zařízení VPN nebo bránu virtuální sítě, ke kterému chcete připojit.

     * **Pokud tato místní síť představuje místní umístění:** Zadejte veřejnou IP adresu zařízení VPN, ke které se chcete připojit. IP adresa nemůže být za serverem NAT a musí být dostupná pro Azure.
     * **Pokud tato místní síť představuje jinou virtuální síť:** Zadejte veřejnou IP adresu, která byla přiřazena k bráně virtuální sítě pro takovou virtuální síť.
     * **Pokud ještě nemáte IP adresu:** Můžete společně tvoří platný zástupnou IP adresu a potom vrátit a změnit toto nastavení před připojením.
   - **Adresní prostor** odkazuje na rozsahy adres sítě, kterou tato místní síť představuje. Můžete přidat více různých rozsahů adres. Ujistěte se, že zde zadané rozsahy nepřekrývají s rozsahy jiných sítí, ke kterým se můžete připojit.
   - **Konfigurujte nastavení protokolu BGP:** Používejte jenom při konfiguraci BGP. V jiných případech tuto možnost nevybírejte.
   - **Předplatné:** Ověřte, že se zobrazuje správné předplatné.
   - **Skupina prostředků:** Vyberte skupinu prostředků, kterou chcete použít. Můžete vytvořit novou skupinu prostředků, nebo vybrat skupinu prostředků, kterou jste už vytvořili.
   - **Umístění:** Vyberte umístění, které tento objekt se vytvoří v. Můžete vybrat stejné umístění, ve kterém se nachází vaše virtuální síť, ale není to povinné.

4. Kliknutím na **Vytvořit** vytvořte bránu místní sítě.
