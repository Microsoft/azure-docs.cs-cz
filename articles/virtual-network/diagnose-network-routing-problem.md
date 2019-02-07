---
title: Diagnostika potíží se směrováním virtuálních počítačů Azure | Dokumentace Microsoftu
description: Zjistěte, jak Diagnostika potíží se směrováním virtuálních počítačů pomocí zobrazení efektivních tras pro virtuální počítač.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/30/2018
ms.author: jdial
ms.openlocfilehash: ee807387b5fc7c8a9d42564cdb6cee7d59db2508
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55819786"
---
# <a name="diagnose-a-virtual-machine-routing-problem"></a>Diagnostika potíží se směrováním virtuálních počítačů

V tomto článku se dozvíte, jak k diagnostice potíží se směrováním zobrazením tras, které platí pro síťové rozhraní ve virtuálním počítači (VM). Azure vytvoří několik výchozích tras pro každou podsíť virtuální sítě. Definování tras ve směrovací tabulce a potom přidružení směrovací tabulky k podsíti, můžete přepsat výchozí trasy. Kombinace trasy, které vytvoříte, výchozí trasy a všechny trasy šíří z vaší místní sítě prostřednictvím služby Azure VPN gateway (Pokud je virtuální síť připojená k vaší místní sítě) prostřednictvím protokolu border gateway protocol (BGP), jsou efektivní trasy pro všechna síťová rozhraní v podsíti. Pokud nejste obeznámeni s virtuální sítí, síťových rozhraní nebo směrování koncepty, najdete v článku [Přehled virtuálních sítí](virtual-networks-overview.md), [síťové rozhraní](virtual-network-network-interface.md), a [Přehled směrování](virtual-networks-udr-overview.md).

## <a name="scenario"></a>Scénář

Při pokusu o připojení k virtuálnímu počítači, ale nepovede. Pokud chcete zjistit, proč se nejde připojit k virtuálnímu počítači, můžete zobrazit efektivní trasy pro síťové rozhraní pomocí Azure [portál](#diagnose-using-azure-portal), [PowerShell](#diagnose-using-powershell), nebo [rozhraní příkazového řádku Azure](#diagnose-using-azure-cli).

Následující postup předpokládá, že máte existujícího virtuálního počítače chcete zobrazit efektivní trasy pro. Pokud nemáte existující virtuální počítač nasadit nejdřív [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) nebo [Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) virtuální počítač k dokončení úkolů v tomto článku se. V příkladech v tomto článku jsou určené pro virtuální počítač s názvem *myVM* se síťovým rozhraním s názvem *myVMVMNic*. Virtuální počítač a síťového rozhraní jsou ve skupině prostředků s názvem *myResourceGroup*a jsou v *USA – východ* oblasti. Změňte hodnoty v krocích, podle potřeby, pro virtuální počítač se Diagnostika pro problém.

## <a name="diagnose-using-azure-portal"></a>Diagnostikovat pomocí webu Azure portal

1. Přihlaste se k Azure [portál](https://portal.azure.com) s účtem Azure, který má [potřebná oprávnění](virtual-network-network-interface.md#permissions).
2. V horní části stránky na webu Azure portal zadejte název virtuálního počítače, který je v běžícím stavu, do vyhledávacího pole. Když se ve výsledcích hledání zobrazí název virtuálního počítače, vyberte ji.
3. Vyberte **diagnostikovat a řešit problémy**a potom v části **doporučené kroky**vyberte **efektivní trasy** bodu 7, jak je znázorněno na následujícím obrázku:

    ![Zobrazení efektivních tras](./media/diagnose-network-routing-problem/view-effective-routes.png)

4. Je efektivní trasy pro síťové rozhraní s názvem **myVMVMNic** jsou je znázorněno na následujícím obrázku:

     ![Zobrazení efektivních tras](./media/diagnose-network-routing-problem/effective-routes.png)

    Pokud jsou připojené k virtuálnímu počítači několik síťových rozhraní, můžete zobrazit efektivní trasy pro každé síťové rozhraní tak, že ho vyberete. Protože každé síťové rozhraní může být v jiné podsíti, každé síťové rozhraní může mít různé efektivní trasy.

    V tomto příkladu je znázorněno na předchozím obrázku jsou uvedené trasy výchozí trasy, které Azure vytvoří pro každou podsíť. Seznam obsahuje aspoň tyto trasy, ale mohou mít další trasy, v závislosti na možnosti, které mohou mít povolíte pro vaší virtuální sítě, například je v partnerském vztahu s jinou virtuální sítí nebo připojené k vaší místní síti prostřednictvím služby Azure VPN gateway. Další informace o všech tras a jiných tras, může se zobrazit pro síťové rozhraní, naleznete v tématu [směrování provozu virtuální sítě](virtual-networks-udr-overview.md). Pokud seznam obsahuje velký počet tras, možná bude snáze vybírat **Stáhnout**, chcete-li stáhnout soubor CSV obsahující seznam tras.

I když bylo efektivní trasy zobrazit pomocí virtuálních počítačů v předchozích krocích, můžete také zobrazit efektivní trasy prostřednictvím:
- **Jednotlivých síťových rozhraní**: Zjistěte, jak [zobrazit síťové rozhraní](virtual-network-network-interface.md#view-network-interface-settings).
- **Jednotlivé směrovací tabulka**: Zjistěte, jak [zobrazit tabulku směrování](manage-route-table.md#view-details-of-a-route-table).

## <a name="diagnose-using-powershell"></a>Diagnostikovat pomocí Powershellu

Můžete spouštět příkazy, které následují v [Azure Cloud Shell](https://shell.azure.com/powershell), nebo pomocí prostředí PowerShell z vašeho počítače. Azure Cloud Shell je bezplatné interaktivní prostředí. Má předinstalované obecné nástroje Azure, které jsou nakonfigurované pro použití s vaším účtem. Při spuštění PowerShell z počítače, je nutné *AzureRM* modul prostředí PowerShell, verze 6.0.1 nebo novější. Spustit `Get-Module -ListAvailable AzureRM` v počítači nainstalovanou verzi zjistíte. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/azurerm/install-azurerm-ps). Pokud používáte PowerShell místně, musíte také spustit `Login-AzureRmAccount` přihlášení k Azure pomocí účtu, který má [potřebná oprávnění](virtual-network-network-interface.md#permissions).

Získat efektivní trasy pro síťové rozhraní s [Get-AzureRmEffectiveRouteTable](/powershell/module/azurerm.network/get-azurermeffectiveroutetable). Následující příklad získá efektivní trasy pro síťové rozhraní s názvem *myVMVMNic*, která je ve skupině prostředků s názvem *myResourceGroup*:

```azurepowershell-interactive
Get-AzureRmEffectiveRouteTable `
  -NetworkInterfaceName myVMVMNic `
  -ResourceGroupName myResourceGroup `
  | Format-Table
```

Seznamte se s informacemi vrácena ve výstupu, najdete v článku [Přehled směrování](virtual-networks-udr-overview.md). Výstup je vrácena, pouze pokud je virtuální počítač ve spuštěném stavu. Pokud jsou připojené k virtuálnímu počítači několik síťových rozhraní, můžete zkontrolovat efektivní trasy pro každé síťové rozhraní. Protože každé síťové rozhraní může být v jiné podsíti, každé síťové rozhraní může mít různé efektivní trasy. Pokud stále máte potíže s komunikací, přečtěte si téma [další diagnostiky](#additional-diagnosis) a [aspekty](#considerations).

Pokud neznáte název síťového rozhraní, ale znáte název je síťové rozhraní připojené k virtuálnímu počítači, tyto příkazy vrátí ID všechna síťová rozhraní připojená k virtuálnímu počítači:

```azurepowershell-interactive
$VM = Get-AzureRmVM -Name myVM `
  -ResourceGroupName myResourceGroup
$VM.NetworkProfile
```

Zobrazí se výstup jako v následujícím příkladu:

```powershell
NetworkInterfaces
-----------------
{/subscriptions/<ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic
```

Ve výstupu předchozí název síťového rozhraní je *myVMVMNic*.

## <a name="diagnose-using-azure-cli"></a>Diagnostikovat pomocí rozhraní příkazového řádku Azure

Můžete spouštět příkazy, které následují v [Azure Cloud Shell](https://shell.azure.com/bash), nebo pomocí rozhraní příkazového řádku z vašeho počítače. Tento článek vyžaduje použití Azure CLI verze 2.0.32 nebo novější. Nainstalovanou verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli). Pokud používáte Azure CLI místně, musíte také spustit `az login` a přihlásit se do Azure pomocí účtu, který má [potřebná oprávnění](virtual-network-network-interface.md#permissions).

Získat efektivní trasy pro síťové rozhraní s [az network nic show-effective-route-table](/cli/azure/network/nic#az-network-nic-show-effective-route-table). Následující příklad získá efektivní trasy pro síťové rozhraní s názvem *myVMVMNic* , který je ve skupině prostředků s názvem *myResourceGroup*:

```azurecli-interactive
az network nic show-effective-route-table \
  --name myVMVMNic \
  --resource-group myResourceGroup
```

Seznamte se s informacemi vrácena ve výstupu, najdete v článku [Přehled směrování](virtual-networks-udr-overview.md). Výstup je vrácena, pouze pokud je virtuální počítač ve spuštěném stavu. Pokud jsou připojené k virtuálnímu počítači několik síťových rozhraní, můžete zkontrolovat efektivní trasy pro každé síťové rozhraní. Protože každé síťové rozhraní může být v jiné podsíti, každé síťové rozhraní může mít různé efektivní trasy. Pokud stále máte potíže s komunikací, přečtěte si téma [další diagnostiky](#additional-diagnosis) a [aspekty](#considerations).

Pokud neznáte název síťového rozhraní, ale znáte název je síťové rozhraní připojené k virtuálnímu počítači, tyto příkazy vrátí ID všechna síťová rozhraní připojená k virtuálnímu počítači:

```azurecli-interactive
az vm show \
  --name myVM \
  --resource-group myResourceGroup
```

## <a name="resolve-a-problem"></a>Řešení problémů

Řešení potíží se směrováním obvykle zahrnuje:

- Přidání vlastní trasu k přepsání jeden výchozí trasy. Zjistěte, jak [přidejte vlastní trasu](manage-route-table.md#create-a-route).
- Změnit nebo odebrat vlastní trasu, která může způsobit, že směrování do umístění nežádoucí. Zjistěte, jak [změnit](manage-route-table.md#change-a-route) nebo [odstranit](manage-route-table.md#delete-a-route) vlastní trasy.
- Zajištění, že směrovací tabulka, která obsahuje všechny vlastní trasy, které jste definovali je přidružená k podsíti, síťové rozhraní se nachází v. Zjistěte, jak [přidružení směrovací tabulky k podsíti](manage-route-table.md#associate-a-route-table-to-a-subnet).
- Zajištění, že se provedou zařízení, jako jsou Azure VPN gateway nebo síťových virtuálních zařízení, které jste nasadili. Použití [Diagnostika VPN](../network-watcher/diagnose-communication-problem-between-networks.md?toc=%2fazure%2fvirtual-network%2ftoc.json) schopnost služby Network Watcher zjistit všechny problémy se službou Azure VPN gateway.

Pokud stále máte problémy s komunikací, přečtěte si téma [aspekty](#considerations) a další diagnostiku.

## <a name="considerations"></a>Požadavky

Při řešení potíží s komunikační problémy, zvažte následující body:

- Směrování se na základě nejdelší shody předpony (LPM) mezi trasy, které jste definovali, border gateway protocol (BGP) a systém trasy. Pokud existuje víc tras se stejnou shodou LPM, pak, trasa se vybere na základě původu v uvedeném pořadí v [Přehled směrování](virtual-networks-udr-overview.md#how-azure-selects-a-route). Pomocí efektivních tras uvidí jenom pomocí efektivních tras, které jsou shodou LPM, založené na všech dostupných tras. Zobrazuje, jak se vyhodnocují trasy pro síťové rozhraní umožňuje mnohem snadněji řešení potíží s konkrétní trasami, které může mít vliv na komunikaci z vašeho virtuálního počítače.
- Pokud jste definovali vlastní trasy pro virtuální síťové zařízení (NVA), s *virtuální zařízení* jako typ dalšího segmentu směrování, ujistěte se, že je povolené předávání IP na síťové virtuální zařízení, který provoz přijímá, nebo pakety se zahodí. Další informace o [povolení předávání IP pro síťové rozhraní](virtual-network-network-interface.md#enable-or-disable-ip-forwarding). Kromě toho operačního systému nebo aplikace v rámci síťového virtuálního zařízení musí také mít možnost směrovat síťový provoz a nakonfigurovat tak, aby tak.
- Pokud jste postup 0.0.0.0/0, veškerý odchozí internetový provoz se směruje a dalším segmentem, který jste zadali, například za účelem síťové virtuální zařízení nebo VPN gateway. Vytváření tyto trasy se často označuje jako vynucené tunelování. Tato trasa, v závislosti na tom, jak další směrování zpracovává přenosy nemusí fungovat vzdálená připojení pomocí protokolu RDP nebo SSH protokoly z Internetu k virtuálnímu počítači. Vynucené tunelování se dá nastavit:
    - Při použití sítě site-to-site VPN tak, že vytvoříte trasy s typem dalšího segmentu směrování z *VPN Gateway*. Další informace o [konfigurace vynuceného tunelování](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
    - Pokud 0.0.0.0/0 (výchozí trasa) je inzerovat přes protokol BGP přes bránu virtuální sítě, při použití sítě site-to-site VPN nebo okruhu ExpressRoute. Další informace o použití protokolu BGP se službou [site-to-site VPN](../vpn-gateway/vpn-gateway-bgp-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) nebo [ExpressRoute](../expressroute/expressroute-routing.md?toc=%2fazure%2fvirtual-network%2ftoc.json#ip-addresses-used-for-azure-private-peering).
- Pro vytvoření partnerského vztahu přenosy virtuální sítě mohly fungovat správně, systémová trasa s typem dalšího segmentu směrování z *VNet Peering* musí existovat rozsahu předpon partnerské virtuální síti. Pokud tyto trasy neexistuje a propojení partnerského vztahu virtuální sítě je **připojeno**:
    - Počkejte několik sekund a zkuste to znovu. Pokud se jedná nově zavedených partnerské propojení virtuálních sítí, někdy trvá déle šířil trasy pro všechna síťová rozhraní v podsíti. Další informace o partnerských vztahů virtuálních sítí najdete v tématu [přehled partnerských vztahů virtuálních sítí](virtual-network-peering-overview.md) a [Správa partnerských vztahů virtuálních sítí](virtual-network-manage-peering.md).
    - Pravidla skupiny zabezpečení sítě, může mít vliv na komunikaci. Další informace najdete v tématu [Diagnostika potíží s filtrováním virtuální počítač síťové přenosy](diagnose-network-traffic-filter-problem.md).
- I když Azure přiřadí výchozí trasy pro každé rozhraní sítě Azure, pokud máte několik síťových rozhraní připojených k virtuálnímu počítači, pouze primární síťové rozhraní se přiřadí výchozí trasa (0.0.0.0/0) nebo bránu v operačním systému Virtuálního počítače. Zjistěte, jak vytvořit výchozí trasu pro sekundární síťová rozhraní připojená k [Windows](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) nebo [Linux](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) virtuálního počítače. Další informace o [primární a sekundární síťová rozhraní](virtual-network-network-interface-vm.md#constraints).

## <a name="additional-diagnosis"></a>Další diagnóza

* Chcete-li spustit rychlé otestování určit typ dalšího segmentu směrování pro přenosy směřující do umístění, použijte [dalšího segmentu směrování](../network-watcher/diagnose-vm-network-routing-problem.md?toc=%2fazure%2fvirtual-network%2ftoc.json) funkce služby Azure Network Watcher. Další směrování zjistíte, co je typ dalšího segmentu směrování pro přenosy směřující do zadaného umístění.
* Pokud nejsou žádné trasy. příčinou komunikace sítě Virtuálního počítače selže, problém může být příčinou software brány firewall v rámci operačního systému Virtuálního počítače
* Pokud jste [vynucené tunelování](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) provoz do místního zařízení VPN gateway nebo síťové virtuální zařízení, nebude možné se připojit k virtuálnímu počítači z Internetu, v závislosti na tom, jak jste nakonfigurovali směrování pro zařízení. Potvrďte, že směrování, které jste nakonfigurovali pro zařízení směruje provoz buď veřejné nebo privátní IP adresu pro virtuální počítač.
* Použití [řešení potíží s připojením](../network-watcher/network-watcher-connectivity-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) funkce služby Network Watcher k určení směrování, filtrování a v OS příčiny problémů odchozí komunikaci.

## <a name="next-steps"></a>Další postup

- Další informace o úlohách, vlastnosti a nastavení [trasy tabulky a trasy](manage-route-table.md).
- Další informace o všech [směrování typy, systémové trasy a jak Azure vybírá trasu](virtual-networks-udr-overview.md).
