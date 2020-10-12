---
title: zahrnout soubor
description: zahrnout soubor
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 09/17/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: f708052e6c1b474d1447a352d10eb1defe311fc4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91024811"
---
1. Na portálu klikněte na **+ Vytvořit prostředek**.
2. Do vyhledávacího pole zadejte **Brána místní sítě** a pak spusťte hledání stisknutím klávesy **Enter**. Zobrazí se seznam výsledků. Klikněte na **Brána místní sítě** a pak kliknutím na **Vytvořit** otevřete stránku **Vytvořit bránu místní sítě**.

   :::image type="content" source="./media/vpn-gateway-add-lng-rm-portal-include/create-local-gateway-ip.png" alt-text="Vytvoření brány místní sítě":::

3. Na stránce **Vytvořit bránu místní sítě** zadejte hodnoty brány místní sítě.

   - **Název**: Zadejte název objektu brány místní sítě.
   - **Koncový bod:** Vyberte typ koncového bodu pro místní zařízení VPN – **IP adresa** nebo plně **kvalifikovaný název domény (plně kvalifikovaný název domény)**.
      - **IP adresa**: Pokud máte pro vaše zařízení VPN STATICKOU veřejnou IP adresu, vyberte možnost IP adresa a zadejte IP adresu, jak je znázorněno v příkladu. Toto je veřejná IP adresa zařízení VPN, ke kterému se má připojit brána Azure VPN. Pokud momentálně nemáte IP adresu, můžete použít hodnoty uvedené v příkladu, ale budete se muset vrátit zpět a nahradit zástupnou IP adresu veřejnou IP adresou svého zařízení VPN. V opačném případe se Azure nebude moci připojit.
      - **Plně kvalifikovaný název domény**: Pokud máte dynamickou veřejnou IP adresu, která se může po určité době změnit, obvykle určená vaším poskytovatelem internetových služeb, můžete použít konstantní název DNS s dynamickou službou DNS, který odkazuje na aktuální veřejnou IP adresu vašeho zařízení VPN. Vaše brána Azure VPN vám vyřeší plně kvalifikovaný název domény a určí veřejnou IP adresu, ke které se připojí. Níže uvedený snímek obrazovky ukazuje příklad použití plně kvalifikovaného názvu domény místo IP adresy.
   - **Adresní prostor** odkazuje na rozsahy adres sítě, kterou tato místní síť představuje. Můžete přidat více různých rozsahů adres. Zkontrolujte, že se zadané rozsahy nepřekrývají s rozsahy jiných sítí, ke kterým se budete chtít připojit. Azure bude směrovat zadaný rozsah adres na místní IP adresu zařízení VPN. *Pokud se chcete připojit ke své místní lokalitě, použijte tady vlastní hodnoty, a ne hodnoty uvedené v příkladu*.
   - **Konfigurovat nastavení protokolu BGP:** Používejte jenom při konfiguraci BGP. V jiných případech tuto možnost nevybírejte.
   - **Předplatné**: Zkontrolujte, že se zobrazuje správné předplatné.
   - **Skupina prostředků**: Vyberte skupinu prostředků, kterou chcete použít. Můžete vytvořit novou skupinu prostředků, nebo vybrat skupinu prostředků, kterou jste už vytvořili.
   - **Umístění**: Vyberte umístění, ve kterém se tento objekt vytvoří. Můžete vybrat stejné umístění, ve kterém se nachází vaše virtuální síť, ale není to povinné.

    Jedná se o stejnou stránku, ale má zvýrazněný plně kvalifikovaný název domény:
   
   :::image type="content" source="./media/vpn-gateway-add-lng-rm-portal-include/create-local-gateway-fqdn.png" alt-text="Vytvoření brány místní sítě":::
   
   > [!NOTE]
   >
   > * Azure VPN podporuje pro každý plně kvalifikovaný název domény jenom jednu IPv4 adresu. Pokud se název domény přeloží na více IP adres, Azure VPN Gateway použije první IP adresu vrácenou servery DNS. Chcete-li eliminovat nejistotu, doporučujeme, aby se plně kvalifikovaný název domény vždy přeložil na jednu adresu IPv4. Protokol IPv6 není podporován.
   > * Azure VPN Gateway udržuje mezipaměť DNS v intervalu 5 minut. Brána se pokusí přeložit plně kvalifikované názvy domény jenom pro odpojené tunely. Resetování brány bude také spouštět rozlišení FQDN.
   >

4. Po dokončení zadávání hodnot vyberte kliknutím na tlačítko **vytvořit** bránu.
