---
title: 'Odstranit bránu virtuální sítě: portál Azure: Resource Manager | Microsoft Docs'
description: Odstraňte bránu virtuální sítě pomocí portálu Azure v modelu nasazení Resource Manager.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: ''
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/20/2017
ms.author: cherylmc
ms.openlocfilehash: b014d43ab25124d3e08e19ca190b320fc8456593
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2018
ms.locfileid: "27593452"
---
# <a name="delete-a-virtual-network-gateway-using-the-portal"></a>Odstranit bránu virtuální sítě pomocí portálu

> [!div class="op_single_selector"]
> * [portál Azure Portal](vpn-gateway-delete-vnet-gateway-portal.md)
> * [PowerShell](vpn-gateway-delete-vnet-gateway-powershell.md)
> * [PowerShell (Classic)](vpn-gateway-delete-vnet-gateway-classic-powershell.md)

Tento článek obsahuje pokyny k odstraňování Azure VPN Gateway nasazení pomocí modelu nasazení Resource Manager. Existuje několik způsobů, které můžete provést, pokud chcete odstranit bránu virtuální sítě pro konfiguraci brány VPN.

- Pokud chcete odstranit vše a začít od začátku, jako v případě testovacím prostředí, můžete odstranit skupinu prostředků. Pokud odstraníte skupinu prostředků, odstraní všechny prostředky ve skupině. Tato metoda se doporučuje jenom, pokud chcete zachovat prostředky ve skupině prostředků. Nelze odstranit selektivně pouze několik prostředků pomocí tohoto přístupu.

- Pokud chcete zachovat některé prostředky ve vaší skupině prostředků, odstraňuje se Brána virtuální sítě je něco víc složité. Než budete moct odstranit bránu virtuální sítě, musíte nejprve odstranit všechny prostředky, které jsou závislé na bráně. Kroky, které budete postupovat podle závisí na typu připojení, které jste vytvořili a závislé prostředky pro každé připojení.

> [!IMPORTANT]
> Následující pokyny popisují postup odstranění brány Azure VPN se nasazuje pomocí modelu nasazení Resource Manager. Pokud chcete odstranit bránu VPN se nasazuje pomocí modelu nasazení classic, použijte prostředí Azure PowerShell popsaný [zde](vpn-gateway-delete-vnet-gateway-classic-powershell.md).


## <a name="delete-a-vpn-gateway"></a>Odstranění brány VPN

Pokud chcete odstranit bránu virtuální sítě, musíte nejprve odstranit všechny prostředky, které se vztahují na bránu virtuální sítě. V určitém pořadí závislé položky musí dojít k odstranění prostředků.

[!INCLUDE [delete gateway](../../includes/vpn-gateway-delete-vnet-gateway-portal-include.md)]

V tomto okamžiku je odstranit bránu virtuální sítě. Další kroky můžete odstranit všechny prostředky, které se již nepoužívají.

### <a name="to-delete-the-local-network-gateway"></a>Chcete-li odstranit bránu místní sítě

1. V **všechny prostředky**, vyhledejte brány místní sítě, které byly přidruženy každé připojení.
2. Na **přehled** pro bránu místní sítě, klikněte na **odstranit**.

### <a name="to-delete-the-public-ip-address-resource-for-the-gateway"></a>Odstranit prostředek veřejné IP adresy pro bránu

1. V **všechny prostředky**, vyhledejte prostředek veřejné IP adresy, který byl přidružený k bráně. Pokud brána virtuální sítě byla aktivní aktivní, zobrazí se dvě veřejné IP adresy. 
2. Na **přehled** stránky pro veřejnou IP adresu, klikněte na tlačítko **odstranit**, pak **Ano** k potvrzení.

### <a name="to-delete-the-gateway-subnet"></a>Chcete-li odstranit podsíť brány

1. V **všechny prostředky**, najít virtuální síť. 
2. Na **podsítě** okně klikněte na tlačítko **GatewaySubnet**, pak klikněte na tlačítko **odstranit**. 
3. Klikněte na tlačítko **Ano** potvrďte, že chcete odstranit podsíť brány.

## <a name="deleterg"></a>Odstraňte bránu VPN odstraníte skupinu prostředků

Pokud si nejste zajímá zachování některé z vašich prostředků ve skupině prostředků a chcete začít znovu, můžete odstranit celé skupiny prostředků. To je rychlý způsob, jak odebrat vše. Následující postup se vztahuje pouze na modelu nasazení Resource Manager.

1. V **všechny prostředky**, vyhledejte skupinu prostředků a klikněte na tlačítko otevřete okno.
2. Klikněte na **Odstranit**. V okně Odstranit zobrazení ovlivněných prostředků. Ujistěte se, že chcete odstranit všechny tyto prostředky. Pokud ne, postupujte podle kroků v [odstranit bránu VPN](#deletegw) v horní části tohoto článku.
3. Chcete-li pokračovat, zadejte název skupiny prostředků, který chcete odstranit a pak klikněte na **odstranit**.
