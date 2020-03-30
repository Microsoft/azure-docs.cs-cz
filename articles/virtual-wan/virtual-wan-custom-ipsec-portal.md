---
title: 'Konfigurace vlastních zásad Protokolu IPsec pro virtuální síť Azure WAN: Portál | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat vlastní zásady IPsec pro Azure Virtual WAN pomocí portálu.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 10/14/2019
ms.author: cherylmc
ms.openlocfilehash: f37d7f3bfac37253339aab3493fb2c444900e099
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73515743"
---
# <a name="configure-a-custom-ipsec-policy-for-virtual-wan-using-the-portal"></a>Konfigurace vlastní zásady protokolu IPsec pro virtuální síť WAN pomocí portálu

Vlastní zásady Protokolu IPsec pro virtuální síť WAN můžete nakonfigurovat na webu Azure Portal. Vlastní zásady jsou užitečné, pokud chcete, aby obě strany (místní a brána Azure VPN) používaly stejné nastavení pro ike fáze 1 a IKE fáze 2.

## <a name="working-with-custom-policies"></a>Práce s vlastními zásadami

[!INCLUDE [IPsec](../../includes/virtual-wan-ipsec-custom-include.md)]

## <a name="configure-a-policy"></a>Konfigurace zásady

1. **Vyhledejte virtuální rozbočovač**. V prohlížeči přejděte na web [Azure Portal](https://aka.ms/azurevirtualwanpreviewfeatures) a přihlaste se pomocí svého účtu Azure. Vyhledejte virtuální rozbočovač pro svůj web.
2. **Vyberte web VPN**. Na centrální stránce vyberte web VPN, pro který chcete nastavit vlastní zásady.

   ![Vyberte](./media/virtual-wan-custom-ipsec-portal/locate.png)
3. **Upravte připojení VPN**. V **místní nabídce** **...** vyberte možnost Upravit **připojení VPN**.

   ![upravit](./media/virtual-wan-custom-ipsec-portal/contextmenu.png)
4. **Konfigurace nastavení**. Na stránce **Upravit připojení VPN** nakonfigurujte nastavení nastavení. Chcete-li uložit nastavení, vyberte **Uložit.**

   ![konfigurace a uložení](./media/virtual-wan-custom-ipsec-portal/edit.png)

## <a name="next-steps"></a>Další kroky

Další informace o službě Virtual WAN najdete v článku [Přehled služby Virtual WAN](virtual-wan-about.md).