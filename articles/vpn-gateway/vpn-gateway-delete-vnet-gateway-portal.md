---
title: 'Azure VPN Gateway: odstranění brány: portál'
description: Odstranění brány virtuální sítě pomocí Azure Portal
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.date: 10/16/2020
ms.author: cherylmc
ms.topic: how-to
ms.openlocfilehash: 7d9ae31b5701707589d79fd5f3d7eb0802038eb9
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2020
ms.locfileid: "92148214"
---
# <a name="delete-a-virtual-network-gateway-using-the-portal"></a>Odstranění brány virtuální sítě pomocí portálu

> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-delete-vnet-gateway-portal.md)
> * [PowerShell](vpn-gateway-delete-vnet-gateway-powershell.md)
> * [PowerShell (Classic)](vpn-gateway-delete-vnet-gateway-classic-powershell.md)

Tento článek vám pomůže odstranit bránu virtuální sítě. Existuje několik různých přístupů, které můžete provést, pokud chcete odstranit bránu pro konfiguraci služby VPN Gateway.

* Pokud chcete odstranit vše a začít znovu, jako v případě testovacího prostředí můžete odstranit skupinu prostředků. Když odstraníte skupinu prostředků, odstraní se všechny prostředky v rámci této skupiny. Tato metoda se doporučuje jenom v případě, že nechcete uchovávat žádné prostředky ve skupině prostředků. Pomocí tohoto přístupu nemůžete selektivně odstranit jenom pár prostředků.

* Pokud chcete zachovat některé prostředky ve vaší skupině prostředků, bude odstranění brány virtuální sítě poněkud složitější. Než budete moct odstranit bránu virtuální sítě, musíte nejdřív odstranit všechny prostředky, které jsou na bráně závislé. Postup závisí na typu připojení, která jste vytvořili, a závislých prostředcích pro každé připojení.

> [!IMPORTANT]
> Postupy v tomto článku se týkají modelu nasazení Resource Manager. Pokud chcete odstranit bránu VPN nasazenou pomocí modelu nasazení Classic, použijte postup uvedený v článku [odstranění brány: klasický](vpn-gateway-delete-vnet-gateway-classic-powershell.md) .

## <a name="delete-a-vpn-gateway"></a>Odstranění brány VPN

Pokud chcete odstranit bránu virtuální sítě, musíte nejdřív odstranit všechny prostředky, které se vztahují k bráně virtuální sítě. Prostředky je nutné z důvodu závislostí odstranit v určitém pořadí.

[!INCLUDE [delete gateway](../../includes/vpn-gateway-delete-vnet-gateway-portal-include.md)]

V tomto okamžiku se odstraní Brána virtuální sítě.

### <a name="to-delete-additional-resources"></a>Odstranění dalších prostředků

Následující kroky vám pomůžou odstranit prostředky, které už nepoužíváte.

#### <a name="to-delete-the-local-network-gateway"></a>Odstranění brány místní sítě

1. Ve **všech prostředcích**vyhledejte brány místní sítě, které byly přidruženy ke každému připojení.
1. V okně **Přehled** pro bránu místní sítě klikněte na **Odstranit**.

#### <a name="to-delete-the-public-ip-address-resource-for-the-gateway"></a>Odstranění prostředku veřejné IP adresy pro bránu

1. V části **všechny prostředky**vyhledejte prostředek veřejné IP adresy, který byl přidružen k bráně. Pokud byla brána virtuální sítě aktivní – aktivní, zobrazí se dvě veřejné IP adresy.
1. Na stránce **Přehled** pro veřejnou IP adresu klikněte na **Odstranit**a potvrďte to kliknutím na **Ano** .

#### <a name="to-delete-the-gateway-subnet"></a>Postup odstranění podsítě brány

1. V části **všechny prostředky**vyhledejte virtuální síť. 
1. V okně **podsítě** klikněte na **GatewaySubnet**a pak klikněte na **Odstranit**. 
1. Kliknutím na **Ano** potvrďte, že chcete odstranit podsíť brány.

## <a name="delete-a-vpn-gateway-by-deleting-the-resource-group"></a><a name="deleterg"></a>Odstranění brány VPN odstraněním skupiny prostředků

Pokud si nejste jisti tím, že si z vašich prostředků ve skupině prostředků zachováte nějaké prostředky a chcete ji jenom začít znovu, můžete odstranit celou skupinu prostředků. To je rychlý způsob, jak odebrat vše. Následující postup se vztahuje pouze na model nasazení Správce prostředků.

1. V části **všechny prostředky**vyhledejte skupinu prostředků a kliknutím otevřete toto okno.
1. Klikněte na **Odstranit**. V okně Odstranit Zobrazte ovlivněné prostředky. Ujistěte se, že chcete odstranit všechny tyto prostředky. V takovém případě použijte postup v části odstranění brány VPN v horní části tohoto článku.
1. Pokud chcete pokračovat, zadejte název skupiny prostředků, kterou chcete odstranit, a pak klikněte na **Odstranit**.
