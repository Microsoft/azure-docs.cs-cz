---
title: Diagnostika problému směrování virtuálního počítače Azure | Dokumenty společnosti Microsoft
description: Zjistěte, jak diagnostikovat problém směrování virtuálního počítače zobrazením efektivních tras pro virtuální počítač.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/30/2018
ms.author: kumud
ms.openlocfilehash: 13d74fbb4a7c133ca2365fd2cbfce4b3d2bea72e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75350605"
---
# <a name="diagnose-a-virtual-machine-routing-problem"></a>Diagnostika problému směrování virtuálního počítače

V tomto článku se dozvíte, jak diagnostikovat problém směrování zobrazením trasy, které jsou účinné pro síťové rozhraní ve virtuálním počítači (VM). Azure vytvoří několik výchozích tras pro každou podsíť virtuální sítě. Výchozí trasy Azure můžete přepsat definováním tras v tabulce postupu a následným přičleněním směrovací tabulky k podsíti. Kombinace tras, které vytvoříte, výchozí chodů Azure a všech tras šířených z vaší místní sítě prostřednictvím brány Azure VPN (pokud je vaše virtuální síť připojená k vaší místní síti) prostřednictvím protokolu hraniční brány (BGP), je efektivní trasy pro všechna síťová rozhraní v podsíti. Pokud nejste obeznámeni s koncepty virtuální sítě, síťového rozhraní nebo směrování, přečtěte si informace [o přehledu virtuální sítě](virtual-networks-overview.md), [síťovém rozhraní](virtual-network-network-interface.md)a [přehledu směrování](virtual-networks-udr-overview.md).

## <a name="scenario"></a>Scénář

Pokusíte se připojit k virtuálnímu virtuálnímu zařízení, ale připojení se nezdaří. Pokud chcete zjistit, proč se nemůžete připojit k virtuálnímu počítači, můžete zobrazit efektivní trasy pro síťové rozhraní pomocí [portálu](#diagnose-using-azure-portal)Azure , [PowerShellu](#diagnose-using-powershell)nebo [rozhraní příkazového řádku Azure](#diagnose-using-azure-cli).

Kroky, které následují předpokládat, že máte existující virtuální hod pro zobrazení efektivní trasy pro. Pokud nemáte existující virtuální počítač, nejprve nasaďte [virtuální počítač s Linuxem](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) nebo [Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) k dokončení úkolů v tomto článku. Příklady v tomto článku jsou pro virtuální hod s názvem *myVM* se síťovým rozhraním s názvem *myVMNic1*. Virtuální jazyk a síťové rozhraní jsou ve skupině prostředků s názvem *myResourceGroup*a jsou v oblasti *USA – východ.* Podle potřeby změňte hodnoty v krocích pro virtuální ho disponující problém.

## <a name="diagnose-using-azure-portal"></a>Diagnostika pomocí portálu Azure

1. Přihlaste se k [portálu](https://portal.azure.com) Azure pomocí účtu Azure, který má [potřebná oprávnění](virtual-network-network-interface.md#permissions).
2. V horní části portálu Azure zadejte název virtuálního počítače, který je ve spuštěném stavu, do vyhledávacího pole. Když se ve výsledcích hledání zobrazí název virtuálního počítače, vyberte ho.
3. V části **Nastavení** vlevo vyberte **Síť**a přejděte na prostředek síťového rozhraní výběrem jeho názvu.
     ![Zobrazit síťová rozhraní](./media/diagnose-network-routing-problem/view-nics.png)
4. Vlevo vyberte **Efektivní trasy**. Efektivní trasy pro síťové rozhraní s názvem **myVMNic1** jsou ![zobrazeny na následujícím obrázku: Zobrazit efektivní trasy](./media/diagnose-network-routing-problem/view-effective-routes.png)

    Pokud existuje více síťových rozhraní připojených k virtuálnímu počítači, můžete zobrazit efektivní trasy pro libovolné síťové rozhraní jeho výběrem. Vzhledem k tomu, že každé síťové rozhraní může být v jiné podsíti, může mít každé síťové rozhraní různé efektivní trasy.

    V příkladu zobrazeném na předchozím obrázku jsou uvedené trasy výchozí trasy, které Azure vytvoří pro každou podsíť. Váš seznam obsahuje alespoň tyto trasy, ale může mít další trasy, v závislosti na možnostech, které jste povolili pro vaši virtuální síť, jako je například peered s jinou virtuální sítí nebo připojení k místní síti prostřednictvím brány Azure VPN. Další informace o jednotlivých trasách a dalších trasách, které se mohou zobrazit v síťovém rozhraní, naleznete [v tématu Směrování provozu ve virtuální síti](virtual-networks-udr-overview.md). Pokud seznam obsahuje velký počet tras, může být pro vás snazší vybrat **možnost Stáhnout**, stáhnout soubor CSV se seznamem tras.

I když efektivní trasy byly zobrazeny prostřednictvím virtuálního virtuálního soudu v předchozích krocích, můžete také zobrazit efektivní trasy prostřednictvím:
- **Individuální síťové rozhraní**: Přečtěte [si,](virtual-network-network-interface.md#view-network-interface-settings)jak zobrazit síťové rozhraní .
- **Individuální tabulka tras**: Přečtěte si, jak [zobrazit směrovací tabulku](manage-route-table.md#view-details-of-a-route-table).

## <a name="diagnose-using-powershell"></a>Diagnostika pomocí PowerShellu

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Můžete spustit příkazy, které následují v [Prostředí Azure Cloud Shell](https://shell.azure.com/powershell), nebo spuštěním Prostředí PowerShell z vašeho počítače. Azure Cloud Shell je bezplatná interaktivní prostředí. Má předinstalované obecné nástroje Azure, které jsou nakonfigurované pro použití s vaším účtem. Pokud spustíte PowerShell z počítače, budete potřebovat modul Azure PowerShell, verze 1.0.0 nebo novější. Spuštěním `Get-Module -ListAvailable Az` v počítači vyhledejte nainstalovanou verzi. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-Az-ps). Pokud používáte PowerShell místně, musíte se `Connect-AzAccount` taky spustit a přihlásit se k Azure pomocí účtu, který má [potřebná oprávnění](virtual-network-network-interface.md#permissions).

Získejte efektivní trasy pro síťové rozhraní s [Get-AzEffectiveRouteTable](/powershell/module/az.network/get-azeffectiveroutetable). Následující příklad získá efektivní trasy pro síťové rozhraní s názvem *myVMNic1*, který je ve skupině prostředků s názvem *myResourceGroup*:

```azurepowershell-interactive
Get-AzEffectiveRouteTable `
  -NetworkInterfaceName myVMNic1 `
  -ResourceGroupName myResourceGroup `
  | Format-Table
```

Informace vrácené ve výstupu naleznete v tématu [Přehled směrování](virtual-networks-udr-overview.md). Výstup je vrácena pouze v případě, že virtuální ms je ve stavu spuštění. Pokud existuje více síťových rozhraní připojených k virtuálnímu počítači, můžete zkontrolovat efektivní trasy pro každé síťové rozhraní. Vzhledem k tomu, že každé síťové rozhraní může být v jiné podsíti, může mít každé síťové rozhraní různé efektivní trasy. Pokud potíže s komunikací přetrvávají, přečtěte si [další diagnózu](#additional-diagnosis) a [důležité informace](#considerations).

Pokud neznáte název síťového rozhraní, ale znáte název virtuálního virtuálního serveru, ke kterému je síťové rozhraní připojeno, následující příkazy vrátí ID všech síťových rozhraní připojených k virtuálnímu virtuálnímu zařízení:

```azurepowershell-interactive
$VM = Get-AzVM -Name myVM `
  -ResourceGroupName myResourceGroup
$VM.NetworkProfile
```

Obdržíte výstup podobný následujícímu příkladu:

```powershell
NetworkInterfaces
-----------------
{/subscriptions/<ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMNic1
```

V předchozím výstupu je název síťového rozhraní *myVMNic1*.

## <a name="diagnose-using-azure-cli"></a>Diagnostika pomocí azure cli

Můžete spustit příkazy, které následují v [prostředí Azure Cloud Shell](https://shell.azure.com/bash), nebo spuštěním příkazového příkazu příkazového příkazu z vašeho počítače. Tento článek vyžaduje Azure CLI verze 2.0.32 nebo novější. Nainstalovanou verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli). Pokud používáte Azure CLI místně, musíte také `az login` spustit a přihlásit se do Azure s účtem, který má [potřebná oprávnění](virtual-network-network-interface.md#permissions).

Získejte efektivní trasy pro síťové rozhraní s [az network nic show-effective-route-table](/cli/azure/network/nic#az-network-nic-show-effective-route-table). Následující příklad získá efektivní trasy pro síťové rozhraní s názvem *myVMNic1,* která je ve skupině prostředků s názvem *myResourceGroup*:

```azurecli-interactive
az network nic show-effective-route-table \
  --name myVMNic1 \
  --resource-group myResourceGroup
```

Informace vrácené ve výstupu naleznete v tématu [Přehled směrování](virtual-networks-udr-overview.md). Výstup je vrácena pouze v případě, že virtuální ms je ve stavu spuštění. Pokud existuje více síťových rozhraní připojených k virtuálnímu počítači, můžete zkontrolovat efektivní trasy pro každé síťové rozhraní. Vzhledem k tomu, že každé síťové rozhraní může být v jiné podsíti, může mít každé síťové rozhraní různé efektivní trasy. Pokud potíže s komunikací přetrvávají, přečtěte si [další diagnózu](#additional-diagnosis) a [důležité informace](#considerations).

Pokud neznáte název síťového rozhraní, ale znáte název virtuálního virtuálního serveru, ke kterému je síťové rozhraní připojeno, následující příkazy vrátí ID všech síťových rozhraní připojených k virtuálnímu virtuálnímu zařízení:

```azurecli-interactive
az vm show \
  --name myVM \
  --resource-group myResourceGroup
```

## <a name="resolve-a-problem"></a>Řešení problému

Řešení problémů se směrováním se obvykle skládá z:

- Přidání vlastní trasy přepsat jednu z výchozích tras Azure. Přečtěte si, jak [přidat vlastní trasu](manage-route-table.md#create-a-route).
- Změňte nebo odeberte vlastní trasu, která může způsobit směrování do nežádoucího umístění. Přečtěte si, jak [změnit](manage-route-table.md#change-a-route) nebo [odstranit](manage-route-table.md#delete-a-route) vlastní trasu.
- Zajištění, že směrovací tabulka obsahující všechny vlastní trasy, které jste definovali, je přidružena k podsíti, ve které se síťové rozhraní nachází. Přečtěte si, jak [přidružit směrovací tabulku k podsíti](manage-route-table.md#associate-a-route-table-to-a-subnet).
- Zajištění, že zařízení, jako je brána Azure VPN nebo síťová virtuální zařízení, která jste nasadili, jsou ovladatelná. Pomocí funkce [diagnostiky SÍTĚ](../network-watcher/diagnose-communication-problem-between-networks.md?toc=%2fazure%2fvirtual-network%2ftoc.json) pomocí nástroje Sledování sítě určete případné problémy s bránou Azure VPN.

Pokud potíže s komunikací přetrvávají, přečtěte [si informace a](#considerations) další diagnózu.

## <a name="considerations"></a>Požadavky

Při řešení potíží s komunikací zvažte následující body:

- Směrování je založeno na nejdelší shodě předpony (LPM) mezi trasami, které jste definovali, protokolhraniční brány (BGP) a systémové trasy. Pokud existuje více než jedna trasa se stejnou shodou LPM, je trasa vybrána na základě jejího počátku v pořadí uvedeném v [přehledu směrování](virtual-networks-udr-overview.md#how-azure-selects-a-route). Díky efektivním trasám můžete zobrazit pouze efektivní trasy, které odpovídají LPM, a to na základě všech dostupných tras. Když vidíte, jak jsou trasy vyhodnocovány pro síťové rozhraní, je mnohem jednodušší řešit konkrétní trasy, které mohou mít vliv na komunikaci z vašeho virtuálního počítače.
- Pokud jste definovali vlastní trasy do síťového virtuálního zařízení (NVA), s *virtuálním zařízením* jako dalším typem směrování, ujistěte se, že předávání IP je povoleno na síťovém virtuálním zařízení příjem přenosy nebo pakety jsou vynechány. Další informace o [povolení předávání IP adres pro síťové rozhraní](virtual-network-network-interface.md#enable-or-disable-ip-forwarding). Kromě toho operační systém nebo aplikace v rámci síťového virtuálního zařízení musí být také schopen předávat síťový provoz a být k tomu nakonfigurován.
- Pokud jste vytvořili trasu 0.0.0.0/0, veškerý odchozí internetový provoz je směrován na další směrování, které jste zadali, například na bránu NVA nebo VPN. Vytvoření takové trasy se často označuje jako vynucené tunelování. Vzdálená připojení pomocí protokolů RDP nebo SSH z Internetu do virtuálního počítače nemusí fungovat s touto trasou, v závislosti na tom, jak další směrování zpracovává provoz. Vynucené tunelové propojení lze povolit:
    - Při použití sítě VPN typu site-to-site vytvořením trasy s dalším typem směrování *brány VPN*. Další informace o [konfiguraci vynuceného tunelového propojení](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
    - Pokud 0.0.0.0/0 (výchozí trasa) je inzerován přes BGP prostřednictvím brány virtuální sítě při použití site-to-site VPN nebo ExpressRoute okruhu. Přečtěte si další informace o používání protokolu BGP s [vpn site-to-site](../vpn-gateway/vpn-gateway-bgp-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) nebo [ExpressRoute](../expressroute/expressroute-routing.md?toc=%2fazure%2fvirtual-network%2ftoc.json#ip-addresses-used-for-azure-private-peering).
- Aby provoz partnerského vztahu virtuální sítě fungoval správně, musí existovat systémová trasa s dalším typem partnerského *vztahu virtuální sítě* pro rozsah předponek partnerského vztahu virtuální sítě. Pokud taková trasa neexistuje a propojení partnerského vztahu virtuální sítě je **připojeno**:
    - Počkejte několik sekund a akci opakujte. Pokud se jedná o nově vytvořené propojení partnerského vztahu, příležitostně trvá rozšíření tras do všech síťových rozhraní v podsíti. Další informace o partnerskésíti virtuálních sítí najdete v tématu [Přehled partnerského vztahu virtuální sítě](virtual-network-peering-overview.md) a správa [partnerského vztahu virtuální sítě](virtual-network-manage-peering.md).
    - Pravidla skupiny zabezpečení sítě mohou mít vliv na komunikaci. Další informace naleznete [v tématu Diagnostika problému s filtrem síťového provozu virtuálních strojů](diagnose-network-traffic-filter-problem.md).
- Přestože Azure přiřazuje výchozí trasy ke každému síťovému rozhraní Azure, pokud máte k virtuálnímu počítači připojeno více síťových rozhraní, jenom primárnímu síťovému rozhraní je přiřazena výchozí trasa (0.0.0.0/0) nebo brána v rámci operačního systému virtuálního počítače. Zjistěte, jak vytvořit výchozí trasu pro sekundární síťová rozhraní připojená k virtuálnímu počítači [se systémem Windows](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) nebo [Linux.](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) Další informace o [primárních a sekundárních síťových rozhraních](virtual-network-network-interface-vm.md#constraints).

## <a name="additional-diagnosis"></a>Další diagnóza

* Chcete-li spustit rychlý test k určení dalšího typu směrování pro provoz určený do umístění, použijte [další směrování](../network-watcher/diagnose-vm-network-routing-problem.md?toc=%2fazure%2fvirtual-network%2ftoc.json) funkce Azure Network Watcher. Další směrování vám řekne, jaký je další typ směrování pro přenosy určené do zadaného umístění.
* Pokud neexistují žádné trasy, které by způsobovaly selhání síťové komunikace virtuálního počítače, může být problém způsoben softwarem brány firewall spuštěným v operačním systému virtuálního počítače.
* Pokud [vynucujete tunelování](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) provozu na místní zařízení prostřednictvím brány VPN nebo nva, nemusí být možné připojení k virtuálnímu počítači z internetu, v závislosti na tom, jak jste nakonfigurovali směrování pro zařízení. Zkontrolujte, zda směrování, které jste nakonfigurovali pro zařízení, směruje provoz na veřejnou nebo privátní IP adresu pro virtuální hod.
* Pomocí funkce [poradce při potížích s připojením](../network-watcher/network-watcher-connectivity-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) sledovacího programu sítě určete příčiny směrování, filtrování a v operačním systému problémů s odchozí komunikací.

## <a name="next-steps"></a>Další kroky

- Seznamte se se všemi úkoly, vlastnostmi a nastaveními [směrové tabulky a tras](manage-route-table.md).
- Seznamte se se všemi [dalšími typy směrování, systémovými trasami a způsobem, jakým Azure vybírá trasu](virtual-networks-udr-overview.md).
