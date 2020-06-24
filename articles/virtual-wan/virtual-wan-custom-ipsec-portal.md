---
title: 'Konfigurace vlastních zásad IPsec pro Azure Virtual WAN: portál | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat vlastní zásadu IPsec pro Azure Virtual WAN pomocí portálu.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 10/14/2019
ms.author: cherylmc
ms.openlocfilehash: 0ea4523d1558f6887e1aef344198026591dac617
ms.sourcegitcommit: 4ac596f284a239a9b3d8ed42f89ed546290f4128
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/12/2020
ms.locfileid: "84752618"
---
# <a name="configure-a-custom-ipsec-policy-for-virtual-wan-using-the-portal"></a>Konfigurace vlastní zásady IPsec pro virtuální síť WAN pomocí portálu

V Azure Portal můžete nakonfigurovat vlastní zásadu IPsec pro virtuální síť WAN. Vlastní zásady jsou užitečné, pokud chcete, aby obě strany (místní i brána Azure VPN Gateway) používaly stejné nastavení pro protokol IKE fáze 1 a IKE fáze 2.

## <a name="working-with-custom-policies"></a>Práce s vlastními zásadami

[!INCLUDE [IPsec](../../includes/virtual-wan-ipsec-custom-include.md)]

## <a name="configure-a-policy"></a>Konfigurace zásady

1. **Vyhledejte virtuální rozbočovač**. V prohlížeči přejděte na web [Azure Portal](https://aka.ms/azurevirtualwanpreviewfeatures) a přihlaste se pomocí svého účtu Azure. Vyhledejte virtuální rozbočovač pro svůj web.
2. **Vyberte lokalitu VPN**. Na stránce centra vyberte lokalitu VPN, pro kterou chcete nastavit vlastní zásady.

   ![vybrali](./media/virtual-wan-custom-ipsec-portal/locate.png)
3. **Upravte připojení VPN**. V **místní nabídce** **...** vyberte **Upravit připojení VPN**.

   ![upravit](./media/virtual-wan-custom-ipsec-portal/contextmenu.png)
4. **Nakonfigurujte nastavení**. Na stránce **Upravit připojení VPN** nakonfigurujte nastavení. Vyberte **Uložit** a uložte nastavení.

   ![Konfigurace a uložení](./media/virtual-wan-custom-ipsec-portal/edit.png)

## <a name="next-steps"></a>Další kroky

Další informace o službě Virtual WAN najdete v článku [Přehled služby Virtual WAN](virtual-wan-about.md).