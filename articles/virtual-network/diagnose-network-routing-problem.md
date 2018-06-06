---
title: Diagnostikovat problém směrování virtuální počítač Azure | Microsoft Docs
description: Zjistěte, jak diagnostikovat problém směrování virtuálního počítače zobrazením efektivní trasy pro virtuální počítač.
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
ms.openlocfilehash: 07352a5d7c8b465440efab17c654979662a95f8e
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2018
ms.locfileid: "34702355"
---
# <a name="diagnose-a-virtual-machine-routing-problem"></a>Diagnostikovat problém směrování virtuálního počítače

V tomto článku zjistěte, jak diagnostikovat problém směrování zobrazením tras, které jsou platné pro rozhraní sítě ve virtuálním počítači (VM). Azure vytvoří několik výchozích tras pro každou podsíť virtuální sítě. Definování tras ve směrovací tabulce a pak přidružení tabulku směrování pro podsíť můžete přepsat výchozí trasy Azure. Kombinace tras, které vytvoříte, Azure, je výchozí trasy a všechny trasy rozšíří z vaší místní sítě prostřednictvím služby Azure VPN gateway (Pokud je vaše virtuální síť je připojená k síti na pracovišti) prostřednictvím protokolu border gateway protocol (BGP), jsou efektivní trasy pro všechna síťová rozhraní v podsíti. Pokud si nejste obeznámeni s virtuální sítě, síťové nebo směrování koncepty, najdete v části [Přehled virtuálních sítí](virtual-networks-overview.md), [síťové rozhraní](virtual-network-network-interface.md), a [Přehled směrování](virtual-networks-udr-overview.md).

## <a name="scenario"></a>Scénář

Zkuste se připojit k virtuálnímu počítači, ale připojení se nezdaří. Pokud chcete zjistit, proč se nemůžete připojit k virtuálnímu počítači, můžete zobrazit efektivní trasy pro síťové rozhraní pomocí třídy Azure [portál](#diagnose-using-azure-portal), [prostředí PowerShell](#diagnose-using-powershell), nebo [rozhraní příkazového řádku Azure](#diagnose-using-azure-cli).

Následující postup předpokládá, že máte existující virtuální počítač k efektivní trasy pro zobrazení. Pokud nemáte existující virtuální počítač, nejprve nasadit [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) nebo [Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) virtuálních počítačů k dokončení úkolů v tomto článku se. V příkladech v tomto článku jsou pro virtuální počítač s názvem *Můjvp* s síťové rozhraní s názvem *myVMVMNic*. Virtuální počítač a síťového rozhraní jsou ve skupině prostředků s názvem *myResourceGroup*a jsou v *východní USA* oblast. Změňte hodnoty v krocích, podle potřeby, pro virtuální počítač se diagnostiky problému pro.

## <a name="diagnose-using-azure-portal"></a>Diagnostikovat pomocí portálu Azure

1. Přihlaste se k Azure [portál](https://portal.azure.com) s účtem Azure, který má [potřebná oprávnění](virtual-network-network-interface.md#permissions).
2. V horní části portálu Azure zadejte název pro virtuální počítač, který je v běžícím stavu, do vyhledávacího pole. Když ve výsledcích hledání se zobrazí název virtuálního počítače, vyberte ho.
3. Vyberte **Diagnostikujte a řešení problémů**a potom v části **doporučené kroky**, vyberte **efektivní trasy** v položky 7, jak je znázorněno na následujícím obrázku:

    ![Zobrazit účinné postupy](./media/diagnose-network-routing-problem/view-effective-routes.png)

4. Platné tras pro síťové rozhraní s názvem **myVMVMNic** jsou uvedeny na následujícím obrázku:

     ![Zobrazit účinné postupy](./media/diagnose-network-routing-problem/effective-routes.png)

    Pokud jsou připojené k virtuálnímu počítači více síťových rozhraní, můžete zobrazit výběrem efektivní trasy pro všechny síťové rozhraní. Vzhledem k tomu, že každé rozhraní sítě může být v jiné podsíti, každé rozhraní sítě může mít různé efektivní trasy.

    V příkladu znázorněno na předchozím obrázku jsou trasy uvedené v seznamu výchozích tras, které Azure vytvoří pro každou podsíť. Váš seznam má alespoň tyto trasy, ale mohou mít dodatečné trasy, v závislosti na možnosti, které může mít povolíte pro vaši virtuální síť, jako je například se peered s jinou virtuální sítí nebo připojení k místní síti přes bránu VPN Azure VPN. Další informace o jednotlivých tras a jiných tras, mohou se zobrazit pro síťové rozhraní najdete v tématu [směrování provozu virtuální sítě](virtual-networks-udr-overview.md). Pokud seznam obsahuje velký počet tras, možná bude jednodušší vybírat **Stáhnout**, chcete-li stáhnout soubor CSV obsahující seznam tras.

I když efektivní trasy měla zobrazit pomocí virtuálních počítačů v předchozích krocích, můžete také zobrazit efektivní tras pomocí:
- **Jednotlivá síťová rozhraní**: Zjistěte, jak [zobrazení síťové rozhraní](virtual-network-network-interface.md#view-network-interface-settings).
- **Jednotlivé směrovací tabulku**: Zjistěte, jak [zobrazit směrovací tabulku](manage-route-table.md#view-details-of-a-route-table).

## <a name="diagnose-using-powershell"></a>Diagnostikovat pomocí prostředí PowerShell

Můžete spouštět příkazy, které následují v [prostředí cloudu Azure](https://shell.azure.com/powershell), nebo pomocí spouštění prostředí PowerShell z vašeho počítače. Prostředí cloudové služby Azure je interaktivní prostředí volné. Má předinstalované obecné nástroje Azure, které jsou nakonfigurované pro použití s vaším účtem. Pokud spustíte prostředí PowerShell z vašeho počítače, musíte *AzureRM* modul prostředí PowerShell, verze 6.0.1 nebo novější. Spustit `Get-Module -ListAvailable AzureRM` na počítači s nainstalovanou verzi. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-azurerm-ps). Pokud používáte PowerShell místně, musíte také spustit `Login-AzureRmAccount` k přihlášení do Azure pomocí účtu, který má [potřebná oprávnění](virtual-network-network-interface.md#permissions).

Získat efektivní trasy pro síťové rozhraní s [Get-AzureRmEffectiveRouteTable](/powershell/module/azurerm.network/get-azurermeffectiveroutetable). Následující příklad načte efektivní trasy pro síťové rozhraní s názvem *myVMVMNic*, která je ve skupině prostředků s názvem *myResourceGroup*:

```azurepowershell-interactive
Get-AzureRmEffectiveRouteTable `
  -NetworkInterfaceName myVMVMNic `
  -ResourceGroupName myResourceGroup `
  | Format-Table
```

Zjistit vrácené ve výstupu informace najdete v části [Přehled směrování](virtual-networks-udr-overview.md). Výstup je vrácena pouze pokud je virtuální počítač v běžícím stavu. Pokud jsou připojené k virtuálnímu počítači více síťových rozhraní, můžete zkontrolovat efektivní trasy pro každé síťové rozhraní. Vzhledem k tomu, že každé rozhraní sítě může být v jiné podsíti, každé rozhraní sítě může mít různé efektivní trasy. Pokud k problému v komunikaci s přetrvávají, přečtěte si téma [další diagnostiky](#additional-diagnosis) a [aspekty](#considerations).

Pokud neznáte název síťového rozhraní, ale znáte název síťové rozhraní je připojen k virtuálnímu počítači, následující příkazy vrátí ID všech síťových rozhraní připojených k virtuálnímu počítači:

```azurepowershell-interactive
$VM = Get-AzureRmVM -Name myVM `
  -ResourceGroupName myResourceGroup
$VM.NetworkProfile
```

Zobrazí se výstup podobný v následujícím příkladu:

```powershell
NetworkInterfaces
-----------------
{/subscriptions/<ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic
```

V předchozích výstup, je název síťového rozhraní *myVMVMNic*.

## <a name="diagnose-using-azure-cli"></a>Diagnostikovat pomocí rozhraní příkazového řádku Azure

Můžete spouštět příkazy, které následují v [prostředí cloudu Azure](https://shell.azure.com/bash), nebo spuštěním rozhraní příkazového řádku z vašeho počítače. Tento článek vyžaduje Azure CLI verze 2.0.32 nebo novější. Nainstalovanou verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI 2.0](/cli/azure/install-azure-cli). Pokud používáte Azure CLI místně, musíte také spustit `az login` a přihlaste se k Azure pomocí účtu, který má [potřebná oprávnění](virtual-network-network-interface.md#permissions).

Získat efektivní trasy pro síťové rozhraní s [az sítě seskupování zobrazit platné trasy tabulky](/cli/azure/network/nic#az-network-nic-show-effective-route-table). Následující příklad načte efektivní trasy pro síťové rozhraní s názvem *myVMVMNic* , je ve skupině prostředků s názvem *myResourceGroup*:

```azurecli-interactive
az network nic show-effective-route-table \
  --name myVMVMNic \
  --resource-group myResourceGroup
```

Zjistit vrácené ve výstupu informace najdete v části [Přehled směrování](virtual-networks-udr-overview.md). Výstup je vrácena pouze pokud je virtuální počítač v běžícím stavu. Pokud jsou připojené k virtuálnímu počítači více síťových rozhraní, můžete zkontrolovat efektivní trasy pro každé síťové rozhraní. Vzhledem k tomu, že každé rozhraní sítě může být v jiné podsíti, každé rozhraní sítě může mít různé efektivní trasy. Pokud k problému v komunikaci s přetrvávají, přečtěte si téma [další diagnostiky](#additional-diagnosis) a [aspekty](#considerations).

Pokud neznáte název síťového rozhraní, ale znáte název síťové rozhraní je připojen k virtuálnímu počítači, následující příkazy vrátí ID všech síťových rozhraní připojených k virtuálnímu počítači:

```azurecli-interactive
az vm show \
  --name myVM \
  --resource-group myResourceGroup
```

## <a name="resolve-a-problem"></a>Vyřešte problém

Řešení problémů s směrování obvykle zahrnuje:

- Přidání vlastní trasy k přepsání jednu z výchozích tras Azure. Zjistěte, jak [přidat vlastní trasy](manage-route-table.md#create-a-route).
- Změnit nebo odebrat vlastní trasy, která může způsobit nežádoucí umístění směrování. Zjistěte, jak [změnit](manage-route-table.md#change-a-route) nebo [odstranit](manage-route-table.md#delete-a-route) vlastní trasy.
- Zajištění, že tabulka trasy, která obsahuje všechny vlastní trasy, které jste definovali je přidružena k síťové rozhraní je v podsíti. Zjistěte, jak [přidružit tabulku směrování pro podsíť](manage-route-table.md#associate-a-route-table-to-a-subnet).
- Zajištění, že se provedou zařízení, jako jsou Azure VPN gateway nebo sítě virtuálních zařízení, které jste nasadili. Použití [VPN diagnostiky](../network-watcher/diagnose-communication-problem-between-networks.md?toc=%2fazure%2fvirtual-network%2ftoc.json) schopnost sledovací proces sítě zjistit případné potíže s bránu VPN Azure VPN.

Pokud máte pořád problémy komunikace, přečtěte si téma [aspekty](#considerations) a [další diagnostiky](#additional-dignosis).

## <a name="considerations"></a>Požadavky

Při řešení potíží s komunikační problémy, zvažte následující body:

- Směrování je na základě nejdelší shody předpony (LPM) mezi tras, které jste definovali, border gateway protocol (BGP) a systém trasy. Pokud je více než jeden trasa se stejnou shodou LPM, pak trasa se vybere na základě původu v uvedeném pořadí v [Přehled směrování](virtual-networks-udr-overview.md#how-azure-selects-a-route). S efektivní směrování uvidí jenom účinné postupy, které jsou shodou LPM, založené na všech dostupných tras. Zobrazuje, jak se vyhodnocují trasy pro síťové rozhraní je mnohem jednodušší řešení potíží s konkrétní trasy, které mohou ovlivňovat komunikace z virtuálního počítače.
- Pokud vlastní trasy pro virtuální síťové zařízení (hodnocení chyb zabezpečení), které jste definovali s *virtuální zařízení* jako typ dalšího přechodu, zkontrolujte, že je na hodnocení chyb zabezpečení, který provoz přijímá povoleno předávání IP nebo dojde ke ztrátě paketů. Další informace o [povolíte předávání IP pro síťové rozhraní](virtual-network-network-interface.md#enable-or-disable-ip-forwarding). Kromě toho operační systém nebo aplikace v rámci hodnocení chyb zabezpečení musí taky umět předávat síťového provozu a nakonfigurovat tak, aby tak.
- Pokud jste vytvořili trasu k 0.0.0.0/0, veškerý odchozí internetový provoz se směruje na další směrování, které jste zadali, například za účelem bránu hodnocení chyb zabezpečení nebo sítě VPN. Vytváření tyto trasy se často označuje jako vynuceného tunelování. Vzdálená připojení pomocí protokolu RDP nebo SSH protokolů z Internetu do virtuální počítač nemusí fungovat s postupu, v závislosti na tom, jak další směrování zpracovává provoz. Můžete povolit vynucené tunelování:
    - Při použití sítě site-to-site VPN, tak, že vytvoříte trasy s další typ směrování *brány VPN*. Další informace o [konfigurace vynuceného tunelování](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
    - Pokud 0.0.0.0/0 (výchozí směrování) je inzerované prostřednictvím protokolu BGP prostřednictvím brány virtuální sítě, při použití sítě site-to-site VPN nebo okruhem ExpressRoute. Další informace o používání protokolu BGP se službou [site-to-site VPN](../vpn-gateway/vpn-gateway-bgp-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) nebo [ExpressRoute](../expressroute/expressroute-routing.md?toc=%2fazure%2fvirtual-network%2ftoc.json#ip-addresses-used-for-azure-private-peering).
- Pro partnerského vztahu přenosy virtuální sítě fungovat správně, systémová trasa s další typ směrování *VNet Peering* rozsah předponu peered virtuální sítě, musí existovat. Pokud tyto trasy neexistuje a partnerského vztahu propojení virtuální sítě je **připojeno**:
    - Počkejte několik sekund a opakujte. Pokud je nově vytvořeným partnerského vztahu odkaz, někdy trvá déle šířil trasy na všech síťových rozhraní v podsíti. Další informace o partnerském vztahu virtuální sítě najdete v tématu [partnerského vztahu Přehled virtuálních sítí](virtual-network-peering-overview.md) a [spravovat virtuální sítě partnerský vztah](virtual-network-manage-peering.md).
    - Pravidla skupiny zabezpečení sítě, které mohou ovlivňovat komunikace. Další informace najdete v tématu [diagnostikovat problém filtru virtuálního počítače síťový provoz](diagnose-network-traffic-filter-problem.md).
- I když Azure přiřadí výchozí trasy na každé rozhraní sítě Azure, pokud máte více síťových rozhraní připojených k virtuálnímu počítači, jenom primární síťové rozhraní je přiřazen výchozí trasa (0.0.0.0/0) nebo brány, v rámci operačního systému Virtuálního počítače. Naučte se vytvořit výchozí trasu pro sekundární síťová rozhraní, které jsou připojené k [Windows](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) nebo [Linux](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) virtuálních počítačů. Další informace o [primární a sekundární síťová rozhraní](virtual-network-network-interface-vm.md#constraints).

## <a name="additional-diagnosis"></a>Další diagnóza

* Spustit Rychlé testu určit další typ směrování pro přenosy určené do umístění, použijte [další segment](../network-watcher/diagnose-vm-network-routing-problem.md?toc=%2fazure%2fvirtual-network%2ftoc.json) schopností sledovací proces sítě Azure. Další směrování řekne, co je typ dalšího přechodu pro přenosy určené do zadaného umístění.
* Pokud nejsou žádné trasy způsobuje selhání komunikaci sítě s Virtuálního počítače, může být problém z důvodu software brány firewall běžící v rámci operačního systému Virtuálního počítače
* Pokud jste [vynucené tunelování](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) provoz do místního zařízení prostřednictvím brány sítě VPN nebo hodnocení chyb zabezpečení, nemusí být možné se připojit k virtuálnímu počítači z Internetu, v závislosti na tom, jak jste nakonfigurovali směrování pro zařízení. Potvrďte, že směrování, které jste nakonfigurovali pro zařízení směruje provoz buď veřejných nebo privátních IP adresu pro virtuální počítač.
* Použití [řešení potíží s připojení](../network-watcher/network-watcher-connectivity-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) schopnost sledovací proces sítě určit směrování, filtrování a v OS příčiny problémů odchozí komunikaci.

## <a name="next-steps"></a>Další postup

- Další informace o úlohách, vlastnosti a nastavení [směrovat tabulky a trasy](manage-route-table.md).
- Další informace o všech [dalšího směrování typy, systémové trasy a jakým způsobem Azure vybírá trasu](virtual-networks-udr-overview.md).
