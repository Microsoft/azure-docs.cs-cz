---
title: Diagnostika problému s filtrem síťového provozu virtuálních strojů | Dokumenty společnosti Microsoft
description: Zjistěte, jak diagnostikovat problém s filtrem provozu sítě virtuálních strojů zobrazením účinných pravidel zabezpečení pro virtuální počítač.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: a54feccf-0123-4e49-a743-eb8d0bdd1ebc
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/29/2018
ms.author: kumud
ms.openlocfilehash: 6939ea2497a9f12321e1a6dfb9bf9fbb353bc7db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240778"
---
# <a name="diagnose-a-virtual-machine-network-traffic-filter-problem"></a>Diagnostika problému s filtrem síťového provozu virtuálních strojů

V tomto článku se dozvíte, jak diagnostikovat problém s filtrem síťového provozu zobrazením pravidel zabezpečení sítě (NSG), která jsou efektivní pro virtuální počítač (VM).

Skupiny sítě nsg umožňují řídit typy provozu, které toku dovnitř a ven z virtuálního počítače. Skupinu zabezpečení sítě můžete přidružit k podsíti ve virtuální síti Azure, síťovému rozhraní připojenému k virtuálnímu počítači nebo obojímu. Účinná pravidla zabezpečení použitá pro síťové rozhraní jsou agregací pravidel, která existují v souboru zabezpečení sítě přidruženém k síťovému rozhraní, a podsíti, ve které se síťové rozhraní nachází. Pravidla v různých sítích zabezpečení sítě může někdy konflikt mezi sebou a vliv na připojení k síti virtuálního zařízení. Můžete zobrazit všechna platná pravidla zabezpečení ze skupin nsg, které se používají v síťových rozhraních virtuálního počítače. Pokud nejste obeznámeni s koncepty virtuální sítě, síťového rozhraní nebo skupiny zabezpečení sítě, přečtěte si informace o [přehledu virtuální sítě](virtual-networks-overview.md), [síťovém rozhraní](virtual-network-network-interface.md)a [skupinách zabezpečení sítě](security-overview.md).

## <a name="scenario"></a>Scénář

Pokusíte se připojit k virtuálnímu virtuálnímu zařízení přes port 80 z Internetu, ale připojení se nezdaří. Chcete-li zjistit, proč nemáte přístup k portu 80 z Internetu, můžete zobrazit účinná pravidla zabezpečení pro síťové rozhraní pomocí [portálu](#diagnose-using-azure-portal)Azure , [PowerShellu](#diagnose-using-powershell)nebo [rozhraní příkazového řádku Azure](#diagnose-using-azure-cli).

Kroky, které následují předpokládat, že máte existující virtuální hod zobrazit pravidla zabezpečení pro. Pokud nemáte existující virtuální počítač, nejprve nasaďte [virtuální počítač s Linuxem](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) nebo [Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) k dokončení úkolů v tomto článku. Příklady v tomto článku jsou pro virtuální hod s názvem *myVM* se síťovým rozhraním s názvem *myVMVMNic*. Virtuální jazyk a síťové rozhraní jsou ve skupině prostředků s názvem *myResourceGroup*a jsou v oblasti *USA – východ.* Podle potřeby změňte hodnoty v krocích pro virtuální ho disponující problém.

## <a name="diagnose-using-azure-portal"></a>Diagnostika pomocí portálu Azure

1. Přihlaste se k [portálu](https://portal.azure.com) Azure pomocí účtu Azure, který má [potřebná oprávnění](virtual-network-network-interface.md#permissions).
2. V horní části portálu Azure zadejte název virtuálního počítače do vyhledávacího pole. Když se ve výsledcích hledání zobrazí název virtuálního počítače, vyberte ho.
3. V části **NASTAVENÍ**vyberte **Možnost Síť**, jak je znázorněno na následujícím obrázku:

   ![Zobrazit pravidla zabezpečení](./media/diagnose-network-traffic-filter-problem/view-security-rules.png)

   Pravidla uvedená na předchozím obrázku jsou pro síťové rozhraní s názvem **myVMVMNic**. Uvidíte, že existují **pravidla příchozíport pro** síťové rozhraní ze dvou různých skupin zabezpečení sítě:
   
   - **mySubnetNSG**: Přidruženo k podsíti, ve které se nachází síťové rozhraní.
   - **myVMNSG**: Přidruženo k síťovému rozhraní ve Virtuálním virtuálním montovi s názvem **myVMVMNic**.

   Pravidlo s názvem **DenyAllInBound** je to, co brání příchozí komunikaci s virtuálním počítačem přes port 80 z Internetu, jak je popsáno ve [scénáři](#scenario). Pravidlo uvádí *0.0.0.0/0* pro **SOURCE**, který zahrnuje internet. Žádné jiné pravidlo s vyšší prioritou (nižší číslo) umožňuje port 80 příchozí. Pokud chcete povolit příchozí port 80 k virtuálnímu virtuálnímu uživateli z Internetu, [přečtěte si](#resolve-a-problem)informace o řešení problému . Další informace o pravidlech zabezpečení a jejich použití v Azure najdete v [tématu Skupiny zabezpečení sítě](security-overview.md).

   V dolní části obrázku se také zobrazí **pravidla odchozího portu**. Pod tím jsou pravidla odchozího portu pro síťové rozhraní. Ačkoli obrázek zobrazuje pouze čtyři příchozí pravidla pro každý soubor nsg, vaše nsgy může mít mnohem více než čtyři pravidla. Na obrázku se zobrazí **VirtualNetwork** pod **SOURCE** a **DESTINATION** a **AzureLoadBalancer** v části **SOURCE**. **VirtualNetwork** a **AzureLoadBalancer** jsou [značky služeb](security-overview.md#service-tags). Značky služeb představují skupinu předpon IP adres, které pomáhají minimalizovat složitost vytváření pravidel zabezpečení.

4. Ujistěte se, že je virtuální virtuální hotel v běžícím stavu, a pak vyberte **Platná pravidla zabezpečení**, jak je znázorněno na předchozím obrázku, abyste viděli účinná pravidla zabezpečení zobrazená na následujícím obrázku:

   ![Zobrazení účinných pravidel zabezpečení](./media/diagnose-network-traffic-filter-problem/view-effective-security-rules.png)

   Uvedená pravidla jsou stejná jako v kroku 3, i když existují různé karty pro skupiny zabezpečení sítě přidružené k síťovému rozhraní a podsíti. Jak můžete vidět na obrázku, zobrazí se pouze prvních 50 pravidel. Chcete-li stáhnout soubor .csv, který obsahuje všechna pravidla, vyberte **možnost Stáhnout**.

   Chcete-li zjistit, které předpony představují jednotlivé značky služby, vyberte pravidlo, například pravidlo s názvem **AllowAzureLoadBalancerInbound**. Následující obrázek znázorňuje předpony pro značku služby **AzureLoadBalancer:**

   ![Zobrazení účinných pravidel zabezpečení](./media/diagnose-network-traffic-filter-problem/address-prefixes.png)

   Přestože značka služby **AzureLoadBalancer** představuje pouze jednu předponu, jiné značky služby představují několik předpon.

5. Předchozí kroky ukázaly pravidla zabezpečení pro síťové rozhraní s názvem **myVMVMNic**, ale také jste viděli síťové rozhraní s názvem **myVMVMNic2** v některých předchozích obrázků. Virtuální modul v tomto příkladu má dvě síťová rozhraní, která jsou k němu připojena. Efektivní pravidla zabezpečení se mohou lišit pro každé síťové rozhraní.

   Chcete-li zobrazit pravidla pro síťové rozhraní **myVMVMNic2,** vyberte jej. Jak je znázorněno na následujícím obrázku, síťové rozhraní má stejná pravidla přidružená k podsíti jako síťové rozhraní **myVMVMNic,** protože obě síťová rozhraní jsou ve stejné podsíti. Při přidružení skupiny zabezpečení sítě k podsíti se jeho pravidla použijí na všechna síťová rozhraní v podsíti.

   ![Zobrazit pravidla zabezpečení](./media/diagnose-network-traffic-filter-problem/view-security-rules2.png)

   Na rozdíl od síťového rozhraní **myVMVMNic** nemá síťové rozhraní **myVMVMNic2** přidruženou skupinu zabezpečení sítě. Každé síťové rozhraní a podsíť může mít nula nebo jeden, nsg k němu spojené. Skupina zabezpečení sítě přidružená ke každému síťovému rozhraní nebo podsíti může být stejná nebo odlišná. Stejnou skupinu zabezpečení sítě můžete přidružit k tolika síťovým rozhraním a podsítím, kolik chcete.

Přestože účinná pravidla zabezpečení byla zobrazena prostřednictvím virtuálního virtuálního provozu, můžete také zobrazit platná pravidla zabezpečení prostřednictvím jednotlivce:
- **Síťové rozhraní**: Přečtěte [si,](virtual-network-network-interface.md#view-network-interface-settings)jak zobrazit síťové rozhraní .
- **NSG**: Přečtěte si, jak [zobrazit soubor NSG](manage-network-security-group.md#view-details-of-a-network-security-group).

## <a name="diagnose-using-powershell"></a>Diagnostika pomocí PowerShellu

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Můžete spustit příkazy, které následují v [Prostředí Azure Cloud Shell](https://shell.azure.com/powershell), nebo spuštěním Prostředí PowerShell z vašeho počítače. Azure Cloud Shell je bezplatná interaktivní prostředí. Má předinstalované obecné nástroje Azure, které jsou nakonfigurované pro použití s vaším účtem. Pokud spustíte PowerShell z počítače, budete potřebovat modul Azure PowerShell, verze 1.0.0 nebo novější. Spuštěním `Get-Module -ListAvailable Az` v počítači vyhledejte nainstalovanou verzi. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-az-ps). Pokud používáte Prostředí PowerShell místně, musíte `Connect-AzAccount` se také spustit a přihlásit se k Azure pomocí účtu, který má [potřebná oprávnění](virtual-network-network-interface.md#permissions)].

Získejte účinná pravidla zabezpečení pro síťové rozhraní pomocí [skupiny Get-AzEffectiveNetworkSecurityGroup](/powershell/module/az.network/get-azeffectivenetworksecuritygroup). Následující příklad získá účinná pravidla zabezpečení pro síťové rozhraní s názvem *myVMVMNic*, který je ve skupině prostředků s názvem *myResourceGroup*:

```azurepowershell-interactive
Get-AzEffectiveNetworkSecurityGroup `
  -NetworkInterfaceName myVMVMNic `
  -ResourceGroupName myResourceGroup
```

Výstup je vrácen ve formátu json. Chcete-li pochopit výstup, viz [interpretovat výstup příkazu](#interpret-command-output).
Výstup je vrácen pouze v případě, že je k síťovému rozhraní přidružen soubor nSG, podsíť, ve které se síťové rozhraní nachází, nebo obojí. Virtuální ho virtuálního provozu musí být ve spuštěném stavu. Virtuální počítač může mít více síťových rozhraní s různými skupinami zabezpečení sítě použít. Při řešení potíží spusťte příkaz pro každé síťové rozhraní.

Pokud potíže s připojením přetrvávají, přečtěte si [další diagnózu](#additional-diagnosis) a [důležité informace](#considerations).

Pokud neznáte název síťového rozhraní, ale znáte název virtuálního virtuálního serveru, ke kterému je síťové rozhraní připojeno, následující příkazy vrátí ID všech síťových rozhraní připojených k virtuálnímu virtuálnímu zařízení:

```azurepowershell-interactive
$VM = Get-AzVM -Name myVM -ResourceGroupName myResourceGroup
$VM.NetworkProfile
```

Obdržíte výstup podobný následujícímu příkladu:

```output
NetworkInterfaces
-----------------
{/subscriptions/<ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic
```

V předchozím výstupu je název síťového rozhraní *myVMVMNic*.

## <a name="diagnose-using-azure-cli"></a>Diagnostika pomocí azure cli

Pokud pomocí příkazů rozhraní Příkazového řádku Azure (CLI) k dokončení úloh v tomto článku, buď spustit příkazy v [prostředí Azure Cloud Shell](https://shell.azure.com/bash), nebo spuštěním rozhraní příkazového řádku z vašeho počítače. Tento článek vyžaduje Azure CLI verze 2.0.32 nebo novější. Nainstalovanou verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli). Pokud používáte Azure CLI místně, musíte také `az login` spustit a přihlásit se do Azure s účtem, který má [potřebná oprávnění](virtual-network-network-interface.md#permissions).

Získejte účinná pravidla zabezpečení pro síťové rozhraní s [az network nic list-effective-nsg](/cli/azure/network/nic#az-network-nic-list-effective-nsg). Následující příklad získá účinná pravidla zabezpečení pro síťové rozhraní s názvem *myVMVMNic,* která je ve skupině prostředků s názvem *myResourceGroup*:

```azurecli-interactive
az network nic list-effective-nsg \
  --name myVMVMNic \
  --resource-group myResourceGroup
```

Výstup je vrácen ve formátu json. Chcete-li pochopit výstup, viz [interpretovat výstup příkazu](#interpret-command-output).
Výstup je vrácen pouze v případě, že je k síťovému rozhraní přidružen soubor nSG, podsíť, ve které se síťové rozhraní nachází, nebo obojí. Virtuální ho virtuálního provozu musí být ve spuštěném stavu. Virtuální počítač může mít více síťových rozhraní s různými skupinami zabezpečení sítě použít. Při řešení potíží spusťte příkaz pro každé síťové rozhraní.

Pokud potíže s připojením přetrvávají, přečtěte si [další diagnózu](#additional-diagnosis) a [důležité informace](#considerations).

Pokud neznáte název síťového rozhraní, ale znáte název virtuálního virtuálního serveru, ke kterému je síťové rozhraní připojeno, následující příkazy vrátí ID všech síťových rozhraní připojených k virtuálnímu virtuálnímu zařízení:

```azurecli-interactive
az vm show \
  --name myVM \
  --resource-group myResourceGroup
```

V rámci vrácené výstupu se zobrazí informace podobné následujícímu příkladu:

```output
"networkProfile": {
    "additionalProperties": {},
    "networkInterfaces": [
      {
        "additionalProperties": {},
        "id": "/subscriptions/<ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
        "primary": true,
        "resourceGroup": "myResourceGroup"
      },
```

V předchozím výstupu je název síťového rozhraní *myVMVMNic interface*.

## <a name="interpret-command-output"></a>Interpretovat výstup příkazu

Bez ohledu na to, zda jste k diagnostice problému použili [prostředí PowerShell](#diagnose-using-powershell)nebo [azure cli,](#diagnose-using-azure-cli) obdržíte výstup, který obsahuje následující informace:

- **NetworkSecurityGroup**: ID skupiny zabezpečení sítě.
- **Přidružení**: Určuje, zda je skupina zabezpečení sítě přidružena k *rozhraní NetworkInterface* nebo *Podsíti*. Pokud je skupina zabezpečení sítě přidružena k oběma, je výstup vrácen s **networksecuritygroup**, **association**a **effectiveSecurityRules**pro každý soubor zabezpečení sítě. Pokud je skupina zabezpečení zabezpečení přidružena nebo odpojena bezprostředně před spuštěním příkazu k zobrazení účinných pravidel zabezpečení, bude pravděpodobně nutné počkat několik sekund, než se změna projeví ve výstupu příkazu.
- **EffectiveSecurityRules**: Vysvětlení každé vlastnosti je podrobně popsáno v [článku Vytvořit pravidlo zabezpečení](manage-network-security-group.md#create-a-security-rule). Názvy pravidel předchází *defaultSecurityRules/* jsou výchozí pravidla zabezpečení, které existují v každém souboru zabezpečení zabezpečení. Názvy pravidel přednastavené s *securityRules/* jsou pravidla, která jste vytvořili. Pravidla, která určují [značku služby](security-overview.md#service-tags), například **Internet**, **VirtualNetwork**a **AzureLoadBalancer** pro vlastnosti **destinationAddressPrefix** nebo **sourceAddressPrefix,** mají také hodnoty pro **vlastnost expandedDestinationAddressPrefix.** V rozšířené vlastnosti **DestinationAddressPrefix** jsou uvedeny všechny předpony adres reprezentované značkou služby.

Pokud se ve výstupu zobrazují duplicitní pravidla, je to proto, že skupina zabezpečení sítě je přidružena k síťovému rozhraní i k podsíti. Both NSGs have the same default rules, and may have additional duplicate rules, if you've created your own rules that are the same in both NSGs.

Pravidlo s názvem **defaultSecurityRules/DenyAllInBound** je to, co brání příchozí komunikaci s virtuálním počítačem přes port 80 z Internetu, jak je popsáno ve [scénáři](#scenario). Žádné jiné pravidlo s vyšší prioritou (nižší číslo) umožňuje port 80 příchozí z internetu.

## <a name="resolve-a-problem"></a>Řešení problému

Ať už používáte [portál](#diagnose-using-azure-portal)Azure , [PowerShell](#diagnose-using-powershell)nebo [rozhraní příkazového příkazu k Azure](#diagnose-using-azure-cli) k diagnostice problému uvedeného ve [scénáři](#scenario) v tomto článku, řešením je vytvořit pravidlo zabezpečení sítě s následujícími vlastnostmi:

| Vlastnost                | Hodnota                                                                              |
|---------                |---------                                                                           |
| Zdroj                  | Všechny                                                                                |
| Rozsahy zdrojových portů      | Všechny                                                                                |
| Cíl             | IP adresa virtuálního soudu, rozsah IP adres nebo všechny adresy v podsíti. |
| Rozsahy cílových portů | 80                                                                                 |
| Protocol (Protokol)                | TCP                                                                                |
| Akce                  | Povolit                                                                              |
| Priorita                | 100                                                                                |
| Name (Název)                    | Povolit-HTTP-Vše                                                                     |

Po vytvoření pravidla je povolen vstup portu 80 z Internetu, protože priorita pravidla je vyšší než výchozí pravidlo zabezpečení s názvem *DenyAllInBound*, které odepře provoz. Přečtěte si, jak [vytvořit pravidlo zabezpečení](manage-network-security-group.md#create-a-security-rule). Pokud jsou k síťovému rozhraní a podsíti přidruženy různé skupiny zabezpečení sítě, je nutné vytvořit stejné pravidlo v obou skupinách sítě.

Když Azure zpracovává příchozí provoz, zpracovává pravidla v souboru zabezpečení sítě přidružené k podsíti (pokud je přidružený skupinu zabezpečení sítě), a pak zpracuje pravidla v souboru zabezpečení sítě přidružené k síťovému rozhraní. Pokud je k síťovému rozhraní a podsíti přidruženo skupiny zabezpečení sítě, musí být port otevřený v obou skupinách zabezpečení sítě, aby se provoz dostal k virtuálnímu zařízení. Chcete-li usnadnit problémy se správou a komunikací, doporučujeme přidružit skupinu zabezpečení sítě k podsíti, nikoli k jednotlivým síťovým rozhraním. Pokud virtuální moduly v rámci podsítě potřebují různá pravidla zabezpečení, můžete vytvořit členy síťových rozhraní skupiny zabezpečení aplikace (ASG) a zadat asg jako zdroj a cíl pravidla zabezpečení. Další informace o [skupinách zabezpečení aplikací](security-overview.md#application-security-groups).

Pokud potíže s komunikací přetrvávají, přečtěte [si informace a](#considerations) další diagnózu.

## <a name="considerations"></a>Požadavky

Při řešení potíží s připojením zvažte následující body:

* Výchozí pravidla zabezpečení blokují příchozí přístup z Internetu a povolují pouze příchozí provoz z virtuální sítě. Chcete-li povolit příchozí přenosy z Internetu, přidejte pravidla zabezpečení s vyšší prioritou než výchozí pravidla. Přečtěte si další informace o [výchozích pravidlech zabezpečení](security-overview.md#default-security-rules)nebo [o přidání pravidla zabezpečení](manage-network-security-group.md#create-a-security-rule).
* Pokud jste měli partnerský virtuální sítě, **VIRTUAL_NETWORK** značka služby se ve výchozím nastavení automaticky rozbalí tak, aby zahrnovala předpony pro partnerské virtuální sítě. Chcete-li vyřešit všechny problémy související s partnerským vztahem virtuální sítě, můžete zobrazit předpony v seznamu **ExpandedAddressPrefix.** Další informace o [partnerském vztahu virtuální sítě](virtual-network-peering-overview.md) a [značky služeb](security-overview.md#service-tags).
* Účinná pravidla zabezpečení se zobrazují jenom pro síťové rozhraní, pokud je k síťovému rozhraní virtuálního zařízení a podsítě přidruženo k síťovému rozhraní virtuálního zařízení nebo k podsíti a pokud je virtuální modul ve spuštěném stavu.
* Pokud nejsou k síťovému rozhraní nebo podsíti přidruženy žádné skupiny zabezpečení sítě a máte k virtuálnímu virtuálnímu počítačůmu přiřazenou [veřejnou IP adresu,](virtual-network-public-ip-address.md) jsou všechny porty otevřené pro příchozí přístup z libovolného místa a odchozí přístup odkudkoli. Pokud má virtuální modul veřejnou IP adresu, doporučujeme použít skupinu zabezpečení sítě na podsíť síťového rozhraní.

## <a name="additional-diagnosis"></a>Další diagnóza

* Chcete-li spustit rychlý test k určení, pokud je povolen provoz na nebo z virtuálního počítače, použijte [možnost ověření toku IP](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md) Azure Network Watcher. Ověření toku IP vás informuje o tom, zda je provoz povolen nebo odepřen. Pokud je tok ip odepřen, ověření toku IP vám řekne, které pravidlo zabezpečení odepře provoz.
* Pokud neexistují žádná pravidla zabezpečení, která by způsobovala selhání síťového připojení virtuálního zařízení, může být problém způsoben:
  * Software brány firewall spuštěný v operačním systému virtuálního počítače
  * Trasy nakonfigurované pro virtuální zařízení nebo místní provoz. Internetový provoz lze přesměrovat do místní sítě pomocí [vynuceného tunelování](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Pokud vynutíte tunelový internetový provoz na virtuální zařízení nebo místně, nemusí být možné se připojit k virtuálnímu počítači z internetu. Informace o tom, jak diagnostikovat problémy se směrováním, které mohou bránit přenosu provozu z virtuálního počítače, najdete v [tématu Diagnostika problému směrování provozu v síti virtuálních strojů](diagnose-network-routing-problem.md).

## <a name="next-steps"></a>Další kroky

- Seznamte se se všemi úkoly, vlastnostmi a nastaveními [skupiny zabezpečení sítě](manage-network-security-group.md#work-with-network-security-groups) a [pravidel zabezpečení](manage-network-security-group.md#work-with-security-rules).
- Přečtěte si o [výchozích pravidlech zabezpečení](security-overview.md#default-security-rules), [značkách služeb](security-overview.md#service-tags)a [o tom, jak Azure zpracovává pravidla zabezpečení pro příchozí a odchozí provoz](security-overview.md#network-security-groups) pro virtuální počítač.
