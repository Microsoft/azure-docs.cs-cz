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
ms.openlocfilehash: 0270fa207b782d2d89f2408a380b766f58fa05cb
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
1. Na portálu v části **Všechny prostředky** klikněte na **+Přidat**. 
2. Do vyhledávacího pole na stránce **Všechno** zadejte **Brána místní sítě** a potom kliknutím vyhledejte. Zobrazí se seznam. Kliknutím na **Brána místní sítě** otevřete stránku a potom kliknutím na **Vytvořit** otevřete stránku **Vytvořit bránu místní sítě**.

  ![Vytvoření brány místní sítě](./media/vpn-gateway-add-lng-s2s-rm-portal-include/createlng.png)

3. Na stránce **Vytvořit bránu místní sítě** zadejte hodnoty brány místní sítě.

  - **Název**: Zadejte název objektu brány místní sítě.
  - **IP adresa**: Toto je veřejná IP adresa zařízení VPN, ke kterému se má Azure připojit. Zadejte platnou veřejnou IP adresu. IP adresa nemůže být za serverem NAT a musí být dostupná pro Azure. Pokud momentálně nemáte IP adresu, můžete použít hodnoty uvedené na obrázku, ale budete se muset vrátit zpět a nahradit zástupnou IP adresu veřejnou IP adresou svého zařízení VPN. V opačném případe se Azure nebude moci připojit.
  - **Adresní prostor** odkazuje na rozsahy adres sítě, kterou tato místní síť představuje. Můžete přidat více různých rozsahů adres. Zkontrolujte, že se zadané rozsahy nepřekrývají s rozsahy jiných sítí, ke kterým se budete chtít připojit. Azure bude směrovat zadaný rozsah adres na místní IP adresu zařízení VPN. *Použijte zde vlastní hodnoty, ne hodnoty zobrazené na snímku obrazovky.*
  - **Konfigurovat nastavení protokolu BGP:** Používejte jenom při konfiguraci BGP. V jiných případech tuto možnost nevybírejte.
  - **Předplatné**: Zkontrolujte, že se zobrazuje správné předplatné.
  - **Skupina prostředků**: Vyberte skupinu prostředků, kterou chcete použít. Můžete vytvořit novou skupinu prostředků, nebo vybrat skupinu prostředků, kterou jste už vytvořili.
  - **Umístění**: Vyberte umístění, ve kterém se tento objekt vytvoří. Můžete vybrat stejné umístění, ve kterém se nachází vaše virtuální síť, ale není to povinné.

4. Až zadáte všechny hodnoty, klikněte na **Vytvořit** v dolní části stránky a vytvořte bránu místní sítě.