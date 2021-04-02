---
title: zahrnout soubor
description: zahrnout soubor
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/22/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 5358bbbca716f5152a943c90cb7a5f735ae12047
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "92479550"
---
1. Z [Azure Portal](https://portal.azure.com)v části **Hledat prostředky, služby a dokumenty (G +/)** zadejte **Brána místní sítě**. Ve výsledcích hledání vyhledejte **bránu místní sítě** v **Marketplace** a vyberte ji. Tím otevřete stránku **vytvořit bránu místní sítě** .
1. Na stránce **Vytvořit bránu místní sítě** zadejte hodnoty brány místní sítě.

   :::image type="content" source="./media/vpn-gateway-add-local-network-gateway-portal-include/create-ip.png" alt-text="Vytvoření brány místní sítě s IP adresou":::

   * **Název**: Zadejte název objektu brány místní sítě.
   * **Koncový bod:** Vyberte typ koncového bodu pro místní zařízení VPN – **IP adresa** nebo plně **kvalifikovaný název domény (plně kvalifikovaný název domény)**.
      * **IP adresa**: Pokud máte pro vaše zařízení VPN STATICKOU veřejnou IP adresu, vyberte možnost IP adresa a zadejte IP adresu, jak je znázorněno v příkladu. Toto je veřejná IP adresa zařízení VPN, ke kterému se má připojit brána Azure VPN. Pokud momentálně nemáte IP adresu, můžete použít hodnoty uvedené v příkladu, ale budete se muset vrátit zpět a nahradit zástupnou IP adresu veřejnou IP adresou svého zařízení VPN. V opačném případe se Azure nebude moci připojit.
      * **Plně kvalifikovaný název domény**: Pokud máte dynamickou veřejnou IP adresu, která se může po určité době změnit, obvykle určená vaším poskytovatelem internetových služeb, můžete použít konstantní název DNS s dynamickou službou DNS, který odkazuje na aktuální veřejnou IP adresu vašeho zařízení VPN. Vaše brána Azure VPN vám vyřeší plně kvalifikovaný název domény a určí veřejnou IP adresu, ke které se připojí. 
   * **Adresní prostor** odkazuje na rozsahy adres sítě, kterou tato místní síť představuje. Můžete přidat více různých rozsahů adres. Zkontrolujte, že se zadané rozsahy nepřekrývají s rozsahy jiných sítí, ke kterým se budete chtít připojit. Azure bude směrovat zadaný rozsah adres na místní IP adresu zařízení VPN. *Pokud se chcete připojit ke své místní lokalitě, použijte tady vlastní hodnoty, a ne hodnoty uvedené v příkladu*.
   * **Konfigurovat nastavení protokolu BGP:** Používejte jenom při konfiguraci BGP. V jiných případech tuto možnost nevybírejte.
   * **Předplatné**: Zkontrolujte, že se zobrazuje správné předplatné.
   * **Skupina prostředků**: Vyberte skupinu prostředků, kterou chcete použít. Můžete vytvořit novou skupinu prostředků, nebo vybrat skupinu prostředků, kterou jste už vytvořili.
   * **Umístění:** Umístění je stejné jako **oblast** v jiném nastavení. Vyberte umístění, ve kterém bude tento objekt vytvořen. Můžete vybrat stejné umístění, ve kterém se nachází vaše virtuální síť, ale není to povinné.

   > [!NOTE]
   >
   > * Azure VPN podporuje pro každý plně kvalifikovaný název domény jenom jednu IPv4 adresu. Pokud se název domény přeloží na více IP adres, Azure VPN Gateway použije první IP adresu vrácenou servery DNS. Abyste vyloučili nejistotu, doporučujeme, aby se plně kvalifikovaný název domény vždycky přeložil na jednu adresu IPv4. Protokol IPv6 není podporován.
   > * Azure VPN Gateway udržuje mezipaměť DNS v intervalu 5 minut. Brána se pokusí přeložit plně kvalifikované názvy domény jenom pro odpojené tunely. Resetování brány bude také spouštět rozlišení FQDN.
   >

1. Po dokončení zadávání hodnot výběrem tlačítka **vytvořit** v dolní části stránky vytvořte bránu místní sítě.
