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
ms.openlocfilehash: 19b8a73835e8ac5ecaac7b42793140325964d17c
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73522548"
---
Pokud chcete vytvořit virtuální síť v modelu nasazení Resource Manageru pomocí webu Azure Portal, postupujte podle následujících kroků. Použijte **příkladové hodnoty** , pokud používáte tyto kroky jako kurz. Pokud se nejedná o kurz, nezapomeňte ukázkové hodnoty nahradit svými. Další informace o práci s virtuálními sítěmi najdete v článku [Přehled virtuálních sítí](../articles/virtual-network/virtual-networks-overview.md).

>[!NOTE]
>Aby se tato virtuální síť připojila k místnímu umístění, budete se muset domluvit se správcem vaší místní sítě a vyčlenit rozsah IP adres, který můžete použít speciálně pro tuto virtuální síť. Pokud existuje duplicitní rozsah adres na obou stranách připojení VPN, provoz se nemusí směrovat očekávaným způsobem. Pokud navíc chcete připojit tuto virtuální síť k jiné virtuální síti, adresní prostor se nesmí překrývat s jinou virtuální sítí. Podle toho pečlivě naplánujte konfiguraci sítě.
>

1. V nabídce [Azure Portal](https://portal.azure.com) vyberte **vytvořit prostředek**. 

   ![Vytvoření prostředku v Azure Portal](./media/vpn-gateway-create-virtual-network-portal-include/azure-portal-create-resource.png)
2. Do pole **Hledat na Marketplace** zadejte text „Virtuální síť“. Ve vráceném seznamu vyhledejte položku **Virtuální síť** a kliknutím otevřete stránku **Virtuální síť**.
3. Klikněte na **Vytvořit**. Tím se otevře stránka **vytvořit virtuální síť** .
4. Na stránce **Vytvořit virtuální síť** nakonfigurujte nastavení virtuální sítě. Po vyplnění polí se červený vykřičník změní na zelenou značku zaškrtnutí, pokud jsou znaky zadané do pole platné. Použijte následující hodnoty:

   - **Název**: VNet1
   - **Adresní prostor**: 10.1.0.0/16
   - **Předplatné**: Ověřte, jestli je předplatné uvedené jako ten, který chcete použít. Předplatná můžete měnit prostřednictvím rozevíracího seznamu.
   - **Skupina prostředků**: TestRG1 (Pokud chcete vytvořit novou skupinu, klikněte na **vytvořit novou** ).
   - **Umístění**: východní USA
   - **Podsíť**: front-end
   - **Rozsah adres**: 10.1.0.0/24

   ![Stránka pro vytvoření virtuální sítě](./media/vpn-gateway-create-virtual-network-portal-include/create-virtual-network1.png)
5. Ponechte DDoS jako Basic, koncové body služby jako zakázané a firewall jako zakázaný.
6. Kliknutím na **Vytvořit** vytvořte síť VNet.