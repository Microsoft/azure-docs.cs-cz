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
ms.openlocfilehash: c538c510eeafacd1596fdeb1b4593919e6236cf0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92487045"
---
1. Z [Azure Portal](https://portal.azure.com)v části **Hledat prostředky, služby a dokumenty (G +/)** zadejte **Brána místní sítě**. Ve výsledcích hledání vyhledejte **bránu místní sítě** v **Marketplace** a vyberte ji. Tím otevřete stránku **vytvořit bránu místní sítě** .
1. Na stránce **Vytvořit bránu místní sítě** zadejte hodnoty brány místní sítě.

   :::image type="content" source="./media/vpn-gateway-add-local-network-gateway-portal-ip-empty/create-ip.png" alt-text="Vytvoření brány místní sítě s IP adresou":::

   * **Název**: Zadejte název objektu brány místní sítě.
   * **Koncový bod:** Vyberte typ koncového bodu pro místní zařízení VPN – **IP adresa** nebo plně **kvalifikovaný název domény (plně kvalifikovaný název domény)**.
      * **IP adresa**: Pokud máte pro vaše zařízení VPN STATICKOU veřejnou IP adresu, vyberte možnost IP adresa a zadejte IP adresu, jak je znázorněno v příkladu. Toto je veřejná IP adresa zařízení VPN, ke kterému se má připojit brána Azure VPN. Pokud momentálně nemáte IP adresu, můžete použít hodnoty uvedené v příkladu, ale budete se muset vrátit zpět a nahradit zástupnou IP adresu veřejnou IP adresou svého zařízení VPN. V opačném případe se Azure nebude moci připojit.
   * **Adresní prostor** odkazuje na rozsahy adres sítě, kterou tato místní síť představuje. Můžete přidat více různých rozsahů adres. Zkontrolujte, že se zadané rozsahy nepřekrývají s rozsahy jiných sítí, ke kterým se budete chtít připojit. Azure bude směrovat zadaný rozsah adres na místní IP adresu zařízení VPN. *Pokud se chcete připojit ke své místní lokalitě, použijte tady vlastní hodnoty, a ne hodnoty uvedené v příkladu*.
   * **Konfigurovat nastavení protokolu BGP:** Používejte jenom při konfiguraci BGP. V jiných případech tuto možnost nevybírejte.
   * **Předplatné**: Zkontrolujte, že se zobrazuje správné předplatné.
   * **Skupina prostředků**: Vyberte skupinu prostředků, kterou chcete použít. Můžete vytvořit novou skupinu prostředků, nebo vybrat skupinu prostředků, kterou jste už vytvořili.
   * **Umístění:** Umístění je stejné jako **oblast** v jiném nastavení. Vyberte umístění, ve kterém bude tento objekt vytvořen. Můžete vybrat stejné umístění, ve kterém se nachází vaše virtuální síť, ale není to povinné.

1. Po dokončení zadávání hodnot výběrem tlačítka **vytvořit** v dolní části stránky vytvořte bránu místní sítě.
