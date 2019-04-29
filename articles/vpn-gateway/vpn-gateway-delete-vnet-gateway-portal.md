---
title: 'Odstraňte bránu virtuální sítě: Azure portal: Resource Manager | Dokumentace Microsoftu'
description: Odstraňte bránu virtuální sítě pomocí webu Azure portal v modelu nasazení Resource Manager.
services: vpn-gateway
documentationcenter: na
author: WenJason
ms.service: vpn-gateway
origin.date: 10/23/2018
ms.date: 03/04/2019
ms.author: v-jay
ms.openlocfilehash: 387b4e982772f22453876e1ea8b9e7c4039601c4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60845681"
---
# <a name="delete-a-virtual-network-gateway-using-the-portal"></a>Odstranit bránu virtuální sítě pomocí portálu

> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-delete-vnet-gateway-portal.md)
> * [PowerShell](vpn-gateway-delete-vnet-gateway-powershell.md)
> * [PowerShell (Classic)](vpn-gateway-delete-vnet-gateway-classic-powershell.md)

Tento článek obsahuje pokyny k odstraňování bran Azure VPN Gateway nasazené pomocí modelu nasazení Resource Manageru. Existuje několik různých přístupů, které můžete provést, pokud chcete odstranit bránu virtuální sítě pro konfiguraci brány VPN.

- Pokud chcete vymazat úplně všechno a začít znovu, jde například o testovacím prostředí, můžete odstranit skupinu prostředků. Při odstranění skupiny prostředků odstraní všechny prostředky v rámci skupiny. Tato metoda se doporučuje jenom Pokud už nechcete zachovat prostředky ve skupině prostředků. Nelze odstranit selektivně pouze několik prostředků, pomocí tohoto přístupu.

- Pokud chcete zachovat některé z prostředků ve vaší skupině prostředků, odstraňuje se Brána virtuální sítě se stane něco složitější. Než budete moct odstranit bránu virtuální sítě, musíte nejprve odstranit všechny prostředky, které jsou závislé na bráně. Kroky, které sledujete závisí na typu připojení, které jste vytvořili a všechny závislé prostředky pro každé připojení.

> [!IMPORTANT]
> Následující pokyny popisují, jak odstranit bran Azure VPN Gateway nasazené pomocí modelu nasazení Resource Manageru. Odstranění brány VPN nasazené pomocí modelu nasazení classic, použijte prosím prostředí Azure PowerShell podle popisu [tady](vpn-gateway-delete-vnet-gateway-classic-powershell.md).


## <a name="delete-a-vpn-gateway"></a>Odstranění brány VPN

Pokud chcete odstranit bránu virtuální sítě, musíte nejprve odstranit jednotlivé prostředky, které se vztahují k bráně virtuální sítě. Musí se odstranit prostředky v určitém pořadí z důvodu závislosti.

[!INCLUDE [delete gateway](../../includes/vpn-gateway-delete-vnet-gateway-portal-include.md)]

V tomto okamžiku odstranění brány virtuální sítě. Další kroky můžete odstranit všechny prostředky, které jsou již nejsou déle používány.

### <a name="to-delete-the-local-network-gateway"></a>Chcete-li odstranit bránu místní sítě

1. V **všechny prostředky**, vyhledejte brány místní sítě, které byly přidruženy k každé připojení.
2. Na **přehled** okno pro bránu místní sítě, klikněte na tlačítko **odstranit**.

### <a name="to-delete-the-public-ip-address-resource-for-the-gateway"></a>Chcete-li odstranit prostředek veřejné IP adresy pro bránu

1. V **všechny prostředky**, vyhledejte prostředek veřejné IP adresy, která je přidružená k bráně. Pokud bránu virtuální sítě byla aktivní aktivní, zobrazí se dvě veřejné IP adresy. 
2. Na **přehled** stránky pro veřejnou IP adresu, klikněte na tlačítko **odstranit**, pak **Ano** potvrďte.

### <a name="to-delete-the-gateway-subnet"></a>Chcete-li odstranit podsíť brány

1. V **všechny prostředky**, vyhledejte virtuální sítě. 
2. Na **podsítě** okna, klikněte na tlačítko **GatewaySubnet**, pak klikněte na tlačítko **odstranit**. 
3. Klikněte na tlačítko **Ano** potvrďte, že chcete odstranit podsíť brány.

## <a name="deleterg"></a>Odstranění brány VPN tak, že odstraníte skupinu prostředků

Pokud nejste obavy o zachování všech vašich prostředků ve skupině prostředků a chcete začít, můžete odstranit celou skupinu prostředků. Toto je rychlý způsob, jak odebrat všechno, co. Následující postup se vztahuje pouze na modelu nasazení Resource Manager.

1. V **všechny prostředky**, vyhledejte skupinu prostředků a kliknutím otevřete okno.
2. Klikněte na **Odstranit**. V okně Odstranit zobrazení ovlivněných prostředků. Ujistěte se, že chcete odstranit všechny tyto prostředky. Pokud ne, postupujte podle kroků v odstranění brány VPN gateway v horní části tohoto článku.
3. Chcete-li pokračovat, zadejte název skupiny prostředků, kterou chcete odstranit a potom klikněte na **odstranit**.

<!--Update_Description: wording update-->