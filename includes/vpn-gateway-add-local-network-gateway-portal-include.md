---
title: zahrnout soubor
description: zahrnout soubor
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 12/19/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 753153d27a47c81854bb661d523a878aa7820f87
ms.sourcegitcommit: e68df5b9c04b11c8f24d616f4e687fe4e773253c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/20/2018
ms.locfileid: "53654287"
---
1. Na portálu klikněte na **+ Vytvořit prostředek**.
2. Do vyhledávacího pole zadejte **Brána místní sítě** a pak spusťte hledání stisknutím klávesy **Enter**. Zobrazí se seznam výsledků. Klikněte na **Brána místní sítě** a pak kliknutím na **Vytvořit** otevřete stránku **Vytvořit bránu místní sítě**.

   ![Vytvoření brány místní sítě](./media/vpn-gateway-add-local-network-gateway-portal-include/create-local-network-gateway.png "Vytvoření brány místní sítě")

3. Na stránce **Vytvořit bránu místní sítě** zadejte hodnoty brány místní sítě.

  - **Jméno:** Zadejte název objektu brány místní sítě.
  - **IP adresa:** Toto je veřejnou IP adresu zařízení VPN, který chcete pro připojení k Azure. Zadejte platnou veřejnou IP adresu. Pokud momentálně nemáte IP adresu, můžete použít hodnoty uvedené v příkladu, ale budete se muset vrátit zpět a nahradit zástupnou IP adresu veřejnou IP adresou svého zařízení VPN. V opačném případe se Azure nebude moci připojit.
  - **Adresní prostor** odkazuje na rozsahy adres sítě, kterou tato místní síť představuje. Můžete přidat více různých rozsahů adres. Zkontrolujte, že se zadané rozsahy nepřekrývají s rozsahy jiných sítí, ke kterým se budete chtít připojit. Azure bude směrovat zadaný rozsah adres na místní IP adresu zařízení VPN. *Pokud se chcete připojit ke své místní lokalitě, použijte tady vlastní hodnoty, a ne hodnoty uvedené v příkladu*.
  - **Konfigurujte nastavení protokolu BGP:** Používejte jenom při konfiguraci BGP. V jiných případech tuto možnost nevybírejte.
  - **Předplatné:** Ověřte, že se zobrazuje správné předplatné.
  - **Skupina prostředků:** Vyberte skupinu prostředků, kterou chcete použít. Můžete vytvořit novou skupinu prostředků, nebo vybrat skupinu prostředků, kterou jste už vytvořili.
  - **Umístění:** Vyberte umístění, které tento objekt se vytvoří v. Můžete vybrat stejné umístění, ve kterém se nachází vaše virtuální síť, ale není to povinné.

4. Až zadáte všechny hodnoty, klikněte na tlačítko **Vytvořit** v dolní části stránky a vytvořte bránu místní sítě.
