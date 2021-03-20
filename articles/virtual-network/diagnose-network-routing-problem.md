---
title: Diagnostika problému s směrováním virtuálních počítačů Azure | Microsoft Docs
description: Podívejte se, jak diagnostikovat problém s směrováním virtuálního počítače zobrazením efektivních tras pro virtuální počítač.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/30/2018
ms.author: kumud
ms.openlocfilehash: 1c23244707179e05c63ed44b5915e58eefd3f4a3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "84705045"
---
# <a name="diagnose-a-virtual-machine-routing-problem"></a>Diagnostika problému s směrováním virtuálního počítače

V tomto článku se dozvíte, jak diagnostikovat problém s směrováním zobrazením tras, které jsou platné pro síťové rozhraní ve virtuálním počítači (VM). Azure vytvoří několik výchozích tras pro každou podsíť virtuální sítě. Výchozí trasy Azure můžete přepsat tak, že definujete trasy v tabulce směrování a potom přiřadíte směrovací tabulku k podsíti. Kombinace tras, které vytvoříte, výchozích tras Azure a všech tras, které se šíří z vaší místní sítě prostřednictvím služby Azure VPN Gateway (Pokud je vaše virtuální síť připojená k vaší místní síti) prostřednictvím protokolu BGP (Border Gateway Protocol), jsou platné trasy pro všechna síťová rozhraní v podsíti. Pokud nejste obeznámeni s koncepcí služby Virtual Network, síťového rozhraní nebo směrování, přečtěte si téma [Přehled virtuální sítě](virtual-networks-overview.md), [síťové rozhraní](virtual-network-network-interface.md)a [Směrování](virtual-networks-udr-overview.md).

## <a name="scenario"></a>Scenario

Pokusíte se připojit k virtuálnímu počítači, ale připojení se nezdaří. Abyste zjistili, proč se nemůžete připojit k virtuálnímu počítači, můžete zobrazit efektivní trasy pro síťové rozhraní pomocí webu Azure [Portal](#diagnose-using-azure-portal), [PowerShellu](#diagnose-using-powershell)nebo rozhraní příkazového [řádku Azure CLI](#diagnose-using-azure-cli).

Následující postup předpokládá, že máte existující virtuální počítač pro zobrazení efektivních tras pro. Pokud nemáte existující virtuální počítač, nasaďte nejdřív virtuální počítač se systémem [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) nebo [Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) , abyste mohli dokončit úkoly v tomto článku. Příklady v tomto článku jsou pro virtuální počítač s názvem *myVM* se síťovým rozhraním s názvem *myVMNic1*. Virtuální počítač a síťové rozhraní jsou ve skupině prostředků s názvem *myResourceGroup* a jsou v oblasti *východní USA* . Podle potřeby změňte hodnoty v krocích pro virtuální počítač, pro který chcete problém diagnostikovat.

## <a name="diagnose-using-azure-portal"></a>Diagnostika pomocí Azure Portal

1. Přihlaste se k webu Azure [Portal](https://portal.azure.com) pomocí účtu Azure, který má [potřebná oprávnění](virtual-network-network-interface.md#permissions).
2. V horní části Azure Portal do vyhledávacího pole zadejte název virtuálního počítače, který je ve stavu spuštěno. Jakmile se ve výsledcích hledání zobrazí název virtuálního počítače, vyberte ho.
3. V části **Nastavení** vlevo vyberte **sítě** a přejděte na prostředek síťového rozhraní, a to tak, že vyberete jeho název.
     ![Zobrazit síťová rozhraní](./media/diagnose-network-routing-problem/view-nics.png)
4. Na levé straně vyberte **efektivní trasy**. Na následujícím obrázku jsou zobrazeny efektivní trasy pro síťové rozhraní s názvem **myVMNic1** : ![ zobrazení efektivních tras](./media/diagnose-network-routing-problem/view-effective-routes.png)

    Pokud existuje více síťových rozhraní připojených k virtuálnímu počítači, můžete zobrazit efektivní trasy pro jakékoli síťové rozhraní, a to tak, že je vyberete. Vzhledem k tomu, že každé síťové rozhraní může být v jiné podsíti, může mít každé síťové rozhraní různé efektivní trasy.

    V příkladu zobrazeném na předchozím obrázku jsou uvedené trasy výchozí trasy, které Azure vytvoří pro každou podsíť. Váš seznam má alespoň tyto trasy, ale může mít další trasy v závislosti na možnostech, které jste povolili pro virtuální síť, jako je například partnerský vztah s jinou virtuální sítí nebo připojenou k místní síti prostřednictvím služby Azure VPN Gateway. Další informace o jednotlivých trasách a dalších trasách, které se vám můžou zobrazit pro vaše síťové rozhraní, najdete v tématu [směrování provozu virtuální sítě](virtual-networks-udr-overview.md). Pokud váš seznam obsahuje velký počet tras, může být snazší vybrat **Stáhnout** a stáhnout soubor. CSV se seznamem tras.

I když se v předchozích krocích prohlédly efektivní trasy, můžete si také zobrazit efektivní trasy prostřednictvím:
- **Individuální síťové rozhraní**: Naučte se [zobrazovat síťové rozhraní](virtual-network-network-interface.md#view-network-interface-settings).
- **Tabulka jednotlivých směrovacích cest**: Naučte se, jak [Zobrazit směrovací tabulku](manage-route-table.md#view-details-of-a-route-table).

## <a name="diagnose-using-powershell"></a>Diagnostika pomocí prostředí PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Můžete spustit příkazy, které následují v [Azure Cloud Shell](https://shell.azure.com/powershell), nebo spuštěním PowerShellu z počítače. Azure Cloud Shell je bezplatné interaktivní prostředí. Má předinstalované obecné nástroje Azure, které jsou nakonfigurované pro použití s vaším účtem. Pokud spustíte PowerShell z počítače, budete potřebovat modul Azure PowerShell, verze 1.0.0 nebo novější. Pokud `Get-Module -ListAvailable Az` chcete najít nainstalovanou verzi, spusťte v počítači. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-Az-ps). Pokud používáte prostředí PowerShell místně, je také třeba spustit `Connect-AzAccount` pro přihlášení k Azure pomocí účtu, který má [potřebná oprávnění](virtual-network-network-interface.md#permissions).

Získejte efektivní trasy pro síťové rozhraní s [Get-AzEffectiveRouteTable](/powershell/module/az.network/get-azeffectiveroutetable). Následující příklad získá efektivní trasy pro síťové rozhraní s názvem *myVMNic1*, které je ve skupině prostředků s názvem *myResourceGroup*:

```azurepowershell-interactive
Get-AzEffectiveRouteTable `
  -NetworkInterfaceName myVMNic1 `
  -ResourceGroupName myResourceGroup `
  | Format-Table
```

Informace vrácené ve výstupu jsou popsány v tématu [Přehled směrování](virtual-networks-udr-overview.md). Výstup se vrátí jenom v případě, že je virtuální počítač ve spuštěném stavu. Pokud je k virtuálnímu počítači připojeno více síťových rozhraní, můžete si projít platné trasy pro každé síťové rozhraní. Vzhledem k tomu, že každé síťové rozhraní může být v jiné podsíti, může mít každé síťové rozhraní různé efektivní trasy. Pokud stále dochází k potížím s komunikací, přečtěte si další [informace o](#considerations) [diagnostice](#additional-diagnosis) a důležitých bodech.

Pokud název síťového rozhraní neznáte, ale znáte název virtuálního počítače, ke kterému je síťové rozhraní připojené, následující příkazy vrátí identifikátory všech síťových rozhraní připojených k virtuálnímu počítači:

```azurepowershell-interactive
$VM = Get-AzVM -Name myVM `
  -ResourceGroupName myResourceGroup
$VM.NetworkProfile
```

Zobrazí se výstup podobný následujícímu příkladu:

```powershell
NetworkInterfaces
-----------------
{/subscriptions/<ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMNic1
```

V předchozím výstupu je název síťového rozhraní *myVMNic1*.

## <a name="diagnose-using-azure-cli"></a>Diagnostika pomocí Azure CLI

Můžete spustit příkazy, které následují v  [Azure Cloud Shell](https://shell.azure.com/bash), nebo spuštěním rozhraní příkazového řádku z počítače. Tento článek vyžaduje Azure CLI verze 2.0.32 nebo novější. Nainstalovanou verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli). Pokud používáte Azure CLI místně, musíte taky spustit `az login` a přihlásit se k Azure pomocí účtu, který má [potřebná oprávnění](virtual-network-network-interface.md#permissions).

Získejte efektivní trasy pro síťové rozhraní pomocí [AZ Network nic show-efektivní-Route-Table](/cli/azure/network/nic#az-network-nic-show-effective-route-table). Následující příklad získá efektivní trasy pro síťové rozhraní s názvem *myVMNic1* , které je ve skupině prostředků s názvem *myResourceGroup*:

```azurecli-interactive
az network nic show-effective-route-table \
  --name myVMNic1 \
  --resource-group myResourceGroup
```

Informace vrácené ve výstupu jsou popsány v tématu [Přehled směrování](virtual-networks-udr-overview.md). Výstup se vrátí jenom v případě, že je virtuální počítač ve spuštěném stavu. Pokud je k virtuálnímu počítači připojeno více síťových rozhraní, můžete si projít platné trasy pro každé síťové rozhraní. Vzhledem k tomu, že každé síťové rozhraní může být v jiné podsíti, může mít každé síťové rozhraní různé efektivní trasy. Pokud stále dochází k potížím s komunikací, přečtěte si další [informace o](#considerations) [diagnostice](#additional-diagnosis) a důležitých bodech.

Pokud název síťového rozhraní neznáte, ale znáte název virtuálního počítače, ke kterému je síťové rozhraní připojené, následující příkazy vrátí identifikátory všech síťových rozhraní připojených k virtuálnímu počítači:

```azurecli-interactive
az vm show \
  --name myVM \
  --resource-group myResourceGroup
```

## <a name="resolve-a-problem"></a>Vyřešit problém

Řešení potíží se směrováním se obvykle skládá z těchto příčin:

- Přidáním vlastní trasy můžete přepsat jednu z výchozích tras Azure. Přečtěte si, jak [Přidat vlastní trasu](manage-route-table.md#create-a-route).
- Změna nebo odebrání vlastní trasy, která může způsobit směrování do nepožadovaného umístění. Přečtěte si, jak [změnit](manage-route-table.md#change-a-route) nebo [Odstranit](manage-route-table.md#delete-a-route) vlastní trasu.
- Ujistěte se, že směrovací tabulka obsahující všechny vlastní trasy, které jste definovali, je přidružená k podsíti, ve které je síťové rozhraní. Přečtěte si, jak [přidružit směrovací tabulku k podsíti](manage-route-table.md#associate-a-route-table-to-a-subnet).
- Ujistěte se, že jsou funkční zařízení, jako je Azure VPN Gateway nebo síťová virtuální zařízení, která jste nasadili. Využijte možnosti [diagnostiky sítě VPN](../network-watcher/diagnose-communication-problem-between-networks.md?toc=%2fazure%2fvirtual-network%2ftoc.json) Network Watcher k určení jakýchkoli problémů se službou Azure VPN Gateway.

Pokud stále dochází k potížím s komunikací, přečtěte si téma [předpoklady](#considerations) a další Diagnostika.

## <a name="considerations"></a>Požadavky

Při řešení potíží s komunikací Vezměte v úvahu následující body:

- Směrování vychází z nejdelší shody předpony (systémových souborů) mezi trasami, které jste definovali, protokolem BGP (Border Gateway Protocol) a systémovými trasami. Pokud existuje více než jedna trasa se stejnou shodou se systémem, pak se vybere trasa na základě jejího původu v pořadí uvedeném v části [Přehled směrování](virtual-networks-udr-overview.md#how-azure-selects-a-route). V případě efektivních tras můžete zobrazit pouze efektivní trasy, které představují shodu se systémem, na základě všech dostupných tras. Podívejte se, jak se vyhodnocuje trasy pro síťové rozhraní, což usnadňuje řešení potíží specifických tras, které mohou mít vliv na komunikaci z vašeho virtuálního počítače.
- Pokud jste definovali vlastní trasy k síťovému virtuálnímu zařízení (síťové virtuální zařízení) s *virtuálním zařízením* jako s typem dalšího segmentu směrování, ujistěte se, že je v síťové virtuální zařízení přijímání přenosů povolené předávání IP, nebo se pakety zahozeny. Přečtěte si další informace o [Povolení předávání IP pro síťové rozhraní](virtual-network-network-interface.md#enable-or-disable-ip-forwarding). Kromě toho musí být operační systém nebo aplikace v rámci síťové virtuální zařízení také schopné předávat síťový provoz a nakonfigurované tak, aby to provedla.
- Pokud jste vytvořili trasu k 0.0.0.0/0, veškerý odchozí internetový provoz se směruje na další segment směrování, který jste zadali, jako je třeba síťové virtuální zařízení nebo VPN Gateway. Vytvoření takové trasy se často označuje jako vynucené tunelování. Vzdálená připojení pomocí protokolů RDP nebo SSH z Internetu k vašemu VIRTUÁLNÍmu počítači nemusí v závislosti na tom, jak další segment směrování zpracovává provoz, fungovat s touto trasou. Je možné povolit vynucené tunelování:
    - Při použití sítě VPN typu Site-to-site vytvořte trasu s typem dalšího segmentu směrování *VPN Gateway*. Přečtěte si další informace o [konfiguraci vynuceného tunelování](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
    - Pokud je hodnota 0.0.0.0/0 (výchozí trasa) inzerována přes protokol BGP prostřednictvím brány virtuální sítě při použití sítě VPN typu Site-to-site nebo okruhu ExpressRoute. Přečtěte si další informace o použití protokolu BGP se sítí [VPN typu Site-to-site](../vpn-gateway/vpn-gateway-bgp-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) nebo [ExpressRoute](../expressroute/expressroute-routing.md?toc=%2fazure%2fvirtual-network%2ftoc.json#ip-addresses-used-for-azure-private-peering).
- Aby provoz partnerských vztahů virtuálních sítí fungoval správně, musí existovat systémová trasa s typem dalšího segmentu směrování *partnerských vztahů* virtuálních sítí pro rozsah předpon partnerských virtuálních sítí. Pokud taková trasa neexistuje a propojení partnerských vztahů virtuálních sítí je **připojené**:
    - Počkejte několik sekund a zkuste to znovu. Pokud se jedná o nově zavedený partnerský odkaz, může občas trvat delší dobu, než se rozšíří trasy na všechna síťová rozhraní v podsíti. Další informace o partnerském vztahu virtuálních sítí najdete v tématu [Přehled partnerských vztahů virtuálních sítí](virtual-network-peering-overview.md) a [Správa partnerského vztahu virtuálních sítí](virtual-network-manage-peering.md).
    - Pravidla skupiny zabezpečení sítě můžou mít vliv na komunikaci. Další informace najdete v tématu [Diagnostika problému s filtrováním síťového provozu virtuálního počítače](diagnose-network-traffic-filter-problem.md).
- I když Azure přiřadí výchozí trasy ke každému síťovému rozhraní Azure, pokud máte k virtuálnímu počítači více síťových rozhraní, přiřadí se výchozí trasa (0.0.0.0/0) nebo brána v operačním systému virtuálního počítače. Naučte se vytvořit výchozí trasu pro sekundární síťová rozhraní připojená k virtuálnímu počítači se [systémem Windows](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) nebo [Linux](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) . Přečtěte si další informace o [primárních a sekundárních síťových rozhraních](virtual-network-network-interface-vm.md#constraints).

## <a name="additional-diagnosis"></a>Další Diagnostika

* Chcete-li spustit rychlý test pro určení typu dalšího segmentu směrování pro provoz určený k umístění, použijte funkci [Next Hop](../network-watcher/diagnose-vm-network-routing-problem.md?toc=%2fazure%2fvirtual-network%2ftoc.json) služby Azure Network Watcher. Další segment směrování oznamuje, co je typ dalšího segmentu směrování pro provoz určený do zadaného umístění.
* Pokud neexistují žádné trasy způsobující selhání síťové komunikace virtuálního počítače, může to být způsobeno tím, že je software brány firewall spuštěný v operačním systému virtuálního počítače.
* Pokud [vynutíte tunelový](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) přenos do místního zařízení prostřednictvím brány VPN nebo síťové virtuální zařízení, možná se nebudete moci připojit k virtuálnímu počítači z Internetu, a to v závislosti na tom, jak jste nakonfigurovali směrování pro zařízení. Potvrďte, že směrování, které jste nakonfigurovali pro zařízení, směruje provoz do veřejné nebo soukromé IP adresy pro virtuální počítač.
* Pomocí možnosti [řešení potíží s připojením](../network-watcher/network-watcher-connectivity-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) pro Network Watcher určete směrování, filtrování a příčiny potíží s odchozí komunikací v operačním systému.

## <a name="next-steps"></a>Další kroky

- Přečtěte si o všech úkolech, vlastnostech a nastaveních [směrovací tabulky a tras](manage-route-table.md).
- Přečtěte si o všech [typech dalších segmentů směrování, systémových trasách a způsobu, jakým Azure vybírá trasu](virtual-networks-udr-overview.md).
