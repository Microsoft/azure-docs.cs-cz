---
title: 'Brána Azure VPN: Odstranění brány: portál'
description: Odstraňte bránu virtuální sítě pomocí portálu Azure v modelu nasazení Správce prostředků.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.date: 10/23/2018
ms.author: cherylmc
ms.topic: conceptual
ms.openlocfilehash: fba213be2de3b747d3ba962674a03c0bc7df3ed5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75863661"
---
# <a name="delete-a-virtual-network-gateway-using-the-portal"></a>Odstranění brány virtuální sítě pomocí portálu

> [!div class="op_single_selector"]
> * [Portál Azure](vpn-gateway-delete-vnet-gateway-portal.md)
> * [PowerShell](vpn-gateway-delete-vnet-gateway-powershell.md)
> * [PowerShell (Classic)](vpn-gateway-delete-vnet-gateway-classic-powershell.md)

Tento článek obsahuje pokyny k odstranění bran Azure VPN nasazených pomocí modelu nasazení Správce prostředků. Existuje několik různých přístupů, které můžete provést, když chcete odstranit bránu virtuální sítě pro konfiguraci brány VPN.

- Pokud chcete odstranit vše a začít znovu, jako v případě testovacího prostředí, můžete odstranit skupinu prostředků. Když odstraníte skupinu prostředků, odstraní všechny prostředky v rámci skupiny. Tato metoda se doporučuje pouze v případě, že nechcete zachovat žádné prostředky ve skupině prostředků. Pomocí tohoto přístupu nelze selektivně odstranit pouze několik prostředků.

- Pokud chcete zachovat některé prostředky ve skupině prostředků, odstranění brány virtuální sítě se stane o něco složitější. Před odstraněním brány virtuální sítě je nutné nejprve odstranit všechny prostředky, které jsou na bráně závislé. Kroky, které provedete, závisí na typu připojení, které jste vytvořili, a na závislých prostředcích pro každé připojení.

> [!IMPORTANT]
> Následující pokyny popisují, jak odstranit brány Azure VPN nasazené pomocí modelu nasazení Správce prostředků. Chcete-li odstranit bránu VPN nasazenou pomocí klasického modelu nasazení, použijte Azure PowerShell, jak je popsáno [zde](vpn-gateway-delete-vnet-gateway-classic-powershell.md).


## <a name="delete-a-vpn-gateway"></a>Odstranění brány VPN

Chcete-li odstranit bránu virtuální sítě, musíte nejprve odstranit každý prostředek, který se týkající se brány virtuální sítě. Prostředky musí být odstraněny v určitém pořadí z důvodu závislostí.

[!INCLUDE [delete gateway](../../includes/vpn-gateway-delete-vnet-gateway-portal-include.md)]

V tomto okamžiku je odstraněna brána virtuální sítě. Další kroky vám pomohou odstranit všechny prostředky, které se již nepoužívají.

### <a name="to-delete-the-local-network-gateway"></a>Odstranění brány místní sítě

1. V **části Všechny prostředky**vyhledejte brány místní sítě, které byly přidruženy ke každému připojení.
2. V okně **Přehled** pro bránu místní sítě klepněte na tlačítko **Odstranit**.

### <a name="to-delete-the-public-ip-address-resource-for-the-gateway"></a>Odstranění prostředku veřejné IP adresy pro bránu

1. Ve **všech prostředcích**vyhledejte prostředek veřejné IP adresy, který byl přidružen k bráně. Pokud brána virtuální sítě byla aktivní aktivní, zobrazí se dvě veřejné IP adresy. 
2. Na stránce **Přehled** veřejné IP adresy potvrďte klepnutím na **tlačítko Odstranit**a **potvrďte ano.**

### <a name="to-delete-the-gateway-subnet"></a>Odstranění podsítě brány

1. Ve **všech prostředcích**vyhledejte virtuální síť. 
2. V okně **Podsítě** klepněte na **položku GatewaySubnet**a potom klepněte na tlačítko **Odstranit**. 
3. Klepnutím na tlačítko **Ano** potvrďte, že chcete podsíť brány odstranit.

## <a name="delete-a-vpn-gateway-by-deleting-the-resource-group"></a><a name="deleterg"></a>Odstranění brány VPN odstraněním skupiny prostředků

Pokud se neobáváte zachování žádného z prostředků ve skupině prostředků a chcete začít znovu, můžete odstranit celou skupinu prostředků. To je rychlý způsob, jak odstranit všechno. Následující kroky platí pouze pro model nasazení Resource Manager.

1. V **části Všechny prostředky**vyhledejte skupinu prostředků a klepnutím otevřete okno.
2. Klepněte na **tlačítko Odstranit**. V okně Delete zobrazte ovlivněné prostředky. Ujistěte se, že chcete odstranit všechny tyto prostředky. Pokud ne, použijte postup v části Odstranit bránu VPN v horní části tohoto článku.
3. Chcete-li pokračovat, zadejte název skupiny prostředků, kterou chcete odstranit, a klepněte na tlačítko **Odstranit**.
