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
ms.openlocfilehash: 9c18a2c74d03a636a0865f3008eb421ab8d7412d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "68781437"
---
1. Na portálu klikněte na **+ Vytvořit prostředek**.
2. Do vyhledávacího pole zadejte **Brána místní sítě** a pak spusťte hledání stisknutím klávesy **Enter**. Zobrazí se seznam výsledků. Klikněte na **Brána místní sítě** a pak kliknutím na **Vytvořit** otevřete stránku **Vytvořit bránu místní sítě**.

   ![Vytvoření brány místní sítě](./media/vpn-gateway-add-lng-rm-portal-include/local-network-gateway.png "Vytvoření brány místní sítě")

3. Na stránce **Vytvořit bránu místní sítě** zadejte hodnoty brány místní sítě.

   - **Název**: Zadejte název objektu brány místní sítě.
   - **IP adresa**: Toto je veřejná IP adresa zařízení VPN, ke kterému se má Azure připojit. Zadejte platnou veřejnou IP adresu. Pokud momentálně nemáte IP adresu, můžete použít hodnoty uvedené v příkladu, ale budete se muset vrátit zpět a nahradit zástupnou IP adresu veřejnou IP adresou svého zařízení VPN. V opačném případe se Azure nebude moci připojit.
   - **Adresní prostor** odkazuje na rozsahy adres sítě, kterou tato místní síť představuje. Můžete přidat více různých rozsahů adres. Zkontrolujte, že se zadané rozsahy nepřekrývají s rozsahy jiných sítí, ke kterým se budete chtít připojit. Azure bude směrovat zadaný rozsah adres na místní IP adresu zařízení VPN. *Pokud se chcete připojit ke své místní lokalitě, použijte tady vlastní hodnoty, a ne hodnoty uvedené v příkladu*.
   - **Konfigurovat nastavení protokolu BGP:** Používejte jenom při konfiguraci BGP. V jiných případech tuto možnost nevybírejte.
   - **Předplatné**: Zkontrolujte, že se zobrazuje správné předplatné.
   - **Skupina prostředků**: Vyberte skupinu prostředků, kterou chcete použít. Můžete vytvořit novou skupinu prostředků, nebo vybrat skupinu prostředků, kterou jste už vytvořili.
   - **Umístění**: Vyberte umístění, ve kterém se tento objekt vytvoří. Můžete vybrat stejné umístění, ve kterém se nachází vaše virtuální síť, ale není to povinné.

4. Po dokončení zadávání hodnot kliknutím na tlačítko **vytvořit** vytvořte bránu.
