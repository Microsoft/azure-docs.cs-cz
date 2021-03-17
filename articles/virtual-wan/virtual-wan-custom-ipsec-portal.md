---
title: 'Konfigurace vlastních zásad IPsec pro Azure Virtual WAN: portál | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat vlastní zásadu IPsec pro Azure Virtual WAN pomocí portálu.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 10/14/2019
ms.author: cherylmc
ms.openlocfilehash: 07eae453441ea8bff81d7cdb60f9c46c08a22829
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91851165"
---
# <a name="configure-a-custom-ipsec-policy-for-virtual-wan-using-the-portal"></a>Konfigurace vlastní zásady IPsec pro virtuální síť WAN pomocí portálu

V Azure Portal můžete nakonfigurovat vlastní zásadu IPsec pro připojení k virtuální síti WAN VPN. Vlastní zásady jsou užitečné, pokud chcete, aby obě strany (místní i brána Azure VPN Gateway) používaly stejné nastavení pro protokol IKE fáze 1 a IKE fáze 2.

## <a name="working-with-custom-policies"></a>Práce s vlastními zásadami

[!INCLUDE [IPsec](../../includes/virtual-wan-ipsec-custom-include.md)]

## <a name="configure-a-policy"></a>Konfigurace zásady

1. **Vyhledejte virtuální rozbočovač**. V prohlížeči přejděte na web [Azure Portal](https://aka.ms/azurevirtualwanpreviewfeatures) a přihlaste se pomocí svého účtu Azure. Přejděte ke svému virtuálnímu prostředku sítě WAN a vyhledejte virtuální rozbočovač, ke kterému je připojená síť VPN.
2. **Vyberte lokalitu VPN**. Na stránce Přehled centra klikněte na **síť VPN (site-to-site)** a vyberte LOKALITu VPN, pro kterou chcete nastavit vlastní zásadu IPSec.

   ![vybrali](./media/virtual-wan-custom-ipsec-portal/locate.png)
3. **Upravte připojení VPN**. V **místní nabídce** **...** vyberte **Upravit připojení VPN**.

   ![upravit](./media/virtual-wan-custom-ipsec-portal/contextmenu.png)
4. **Nakonfigurujte nastavení**. Na stránce **Upravit připojení VPN** změňte nastavení IPSec z výchozí na vlastní a přizpůsobte zásadu IPSec. Vyberte **Uložit** a uložte nastavení.

   ![Konfigurace a uložení](./media/virtual-wan-custom-ipsec-portal/edit.png)

## <a name="next-steps"></a>Další kroky

Další informace o službě Virtual WAN najdete v článku [Přehled služby Virtual WAN](virtual-wan-about.md).