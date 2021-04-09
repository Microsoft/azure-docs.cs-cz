---
title: Diagnostika problému s filtrem síťového provozu virtuálního počítače | Microsoft Docs
description: Přečtěte si, jak diagnostikovat problém filtru síťových přenosů virtuálních počítačů, a to zobrazením platných pravidel zabezpečení pro virtuální počítač.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: a54feccf-0123-4e49-a743-eb8d0bdd1ebc
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/29/2018
ms.author: kumud
ms.openlocfilehash: 73562d8d32f265fa43ca80d2f8d4f84b1b631ec6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98223665"
---
# <a name="diagnose-a-virtual-machine-network-traffic-filter-problem"></a>Diagnostika problému s filtrováním síťového provozu virtuálního počítače

V tomto článku se dozvíte, jak diagnostikovat problém s filtrováním síťových přenosů, a to zobrazením pravidel zabezpečení skupiny zabezpečení sítě (NSG), která jsou platná pro virtuální počítač (VM).

Skupin zabezpečení sítě vám umožní řídit typy provozu, které se budou směrovat do virtuálního počítače a z něj. NSG můžete přidružit k podsíti ve službě Azure Virtual Network, síťovém rozhraní připojenému k virtuálnímu počítači nebo obojímu. Platná pravidla zabezpečení používaná pro síťové rozhraní jsou agregace pravidel, která existují v NSG přidružených k síťovému rozhraní, a v podsíti, ve které se síťové rozhraní nachází. Pravidla v různých skupin zabezpečení sítě můžou někdy kolidovat a ovlivnit připojení k síti virtuálního počítače. Můžete zobrazit všechna platná pravidla zabezpečení z skupin zabezpečení sítě, která se aplikují na síťová rozhraní vašeho virtuálního počítače. Pokud nejste obeznámeni s koncepty virtuální sítě, síťového rozhraní nebo NSG, přečtěte si téma Přehled [virtuální sítě](virtual-networks-overview.md), [síťové rozhraní](virtual-network-network-interface.md)a [skupiny zabezpečení sítě](./network-security-groups-overview.md).

## <a name="scenario"></a>Scenario

Pokusíte se připojit k virtuálnímu počítači přes port 80 z Internetu, ale připojení se nezdaří. Pokud chcete zjistit, proč nemůžete získat přístup k portu 80 z Internetu, můžete zobrazit platná pravidla zabezpečení pro síťové rozhraní pomocí webu Azure [Portal](#diagnose-using-azure-portal), [PowerShellu](#diagnose-using-powershell)nebo rozhraní příkazového [řádku Azure CLI](#diagnose-using-azure-cli).

Následující postup předpokládá, že máte existující virtuální počítač pro zobrazení platných pravidel zabezpečení pro. Pokud nemáte existující virtuální počítač, nasaďte nejdřív virtuální počítač se systémem [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) nebo [Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) , abyste mohli dokončit úkoly v tomto článku. Příklady v tomto článku jsou pro virtuální počítač s názvem *myVM* se síťovým rozhraním s názvem *myVMVMNic*. Virtuální počítač a síťové rozhraní jsou ve skupině prostředků s názvem *myResourceGroup* a jsou v oblasti *východní USA* . Podle potřeby změňte hodnoty v krocích pro virtuální počítač, pro který chcete problém diagnostikovat.

## <a name="diagnose-using-azure-portal"></a>Diagnostika pomocí Azure Portal

1. Přihlaste se k webu Azure [Portal](https://portal.azure.com) pomocí účtu Azure, který má [potřebná oprávnění](virtual-network-network-interface.md#permissions).
2. V horní části Azure Portal do vyhledávacího pole zadejte název virtuálního počítače. Jakmile se ve výsledcích hledání zobrazí název virtuálního počítače, vyberte ho.
3. V části **Nastavení** vyberte **sítě**, jak je znázorněno na následujícím obrázku:

   ![Snímek obrazovky ukazuje Azure Portal s nastavením sítě pro moji síťovou kartu V m.](./media/diagnose-network-traffic-filter-problem/view-security-rules.png)

   Pravidla zobrazená na předchozím obrázku jsou pro síťové rozhraní s názvem **myVMVMNic**. Vidíte, že pro síťové rozhraní existují **pravidla příchozího portu** ze dvou různých skupin zabezpečení sítě:
   
   - **mySubnetNSG**: přidruženo k podsíti, ve které se nachází síťové rozhraní.
   - **myVMNSG**: přidruženo k síťovému rozhraní ve virtuálním počítači s názvem **myVMVMNic**.

   Pravidlo s názvem **DenyAllInBound** to brání příchozí komunikaci virtuálního počítače přes port 80 z Internetu, jak je popsáno ve [scénáři](#scenario). Pravidlo obsahuje hodnotu *0.0.0.0/0* pro **zdroj**, který zahrnuje Internet. Žádné jiné pravidlo s vyšší prioritou (nižším číslem) umožňuje příchozí port 80. Pokud chcete virtuálnímu počítači povolit příchozí port 80 z Internetu, přečtěte si téma [řešení problému](#resolve-a-problem). Další informace o pravidlech zabezpečení a o tom, jak je Azure používá, najdete v tématu [skupiny zabezpečení sítě](./network-security-groups-overview.md).

   V dolní části obrázku se zobrazí také **pravidla odchozího portu**. V části se nacházejí pravidla odchozího portu pro síťové rozhraní. I když obrázek zobrazuje pouze čtyři příchozí pravidla pro každý NSG, vaše skupin zabezpečení sítě může mít mnoho více než čtyři pravidla. Na obrázku vidíte **VirtualNetwork** pod položkou **zdroj** a **cíl** a **AzureLoadBalancer** pod položkou **source**. **VirtualNetwork** a **AzureLoadBalancer** jsou [značky služeb](./network-security-groups-overview.md#service-tags). Značky služby reprezentují skupinu předpon IP adres, které vám pomůžou minimalizovat složitost pro vytváření pravidel zabezpečení.

4. Ujistěte se, že je virtuální počítač ve stavu spuštěno, a pak vyberte **platná pravidla zabezpečení**, jak je znázorněno na předchozím obrázku, abyste zobrazili platná pravidla zabezpečení, která jsou znázorněná na následujícím obrázku:

   ![Snímek obrazovky zobrazuje podokno efektivní pravidla zabezpečení s vybraným vybraným a AllowAzureLoadBalancerInbound příchozím pravidlem.](./media/diagnose-network-traffic-filter-problem/view-effective-security-rules.png)

   Uvedená pravidla se shodují s pravidly, která jste viděli v kroku 3, i když jsou k dispozici různé karty NSG přidružené k síťovému rozhraní a podsíti. Jak vidíte na obrázku, zobrazují se jenom první pravidla 50. Pokud chcete stáhnout soubor. csv, který obsahuje všechna pravidla, vyberte **Stáhnout**.

   Chcete-li zjistit, které předpony jednotlivých značek služeb představují, vyberte pravidlo, například pravidlo s názvem **AllowAzureLoadBalancerInbound**. Následující obrázek ukazuje předpony pro značku služby **AzureLoadBalancer** :

   ![Snímek obrazovky se zobrazí předpony adres pro zadané AllowAzureLoadBalancerInbound.](./media/diagnose-network-traffic-filter-problem/address-prefixes.png)

   I když značka služby **AzureLoadBalancer** představuje pouze jednu předponu, jiné značky služby představují několik předpon.

5. Předchozí kroky ukázaly pravidla zabezpečení pro síťové rozhraní s názvem **myVMVMNic**, ale v některých předchozích obrázcích jste také viděli síťové rozhraní s názvem **myVMVMNic2** . Virtuální počítač v tomto příkladu obsahuje dvě síťová rozhraní, která jsou k němu připojená. Platná pravidla zabezpečení mohou být pro každé síťové rozhraní odlišná.

   Pokud se chcete podívat na pravidla síťového rozhraní **myVMVMNic2** , vyberte ho. Jak je znázorněno na následujícím obrázku, síťové rozhraní má stejná pravidla jako síťové rozhraní **myVMVMNic** , protože obě síťová rozhraní jsou ve stejné podsíti. Když přidružíte NSG k podsíti, uplatní se jejich pravidla na všechna síťová rozhraní v podsíti.

   ![Snímek obrazovky se zobrazí Azure Portal s nastaveními sítě pro síťovou kartu V m V M – 2.](./media/diagnose-network-traffic-filter-problem/view-security-rules2.png)

   Na rozdíl od síťového rozhraní **myVMVMNic** nemá síťové rozhraní **myVMVMNic2** přiřazenou skupinu zabezpečení sítě. Ke každému síťovému rozhraní a podsíti může být přidružena nula nebo jedna z nich NSG. NSG přidružené k jednotlivým síťovým rozhraním nebo podsíti můžou být stejné nebo jiné. Stejnou skupinu zabezpečení sítě můžete přidružit k tolika síťovým rozhraním a podsítím, kolik jich zvolíte.

I když jste přes virtuální počítač viděli skutečná pravidla zabezpečení, můžete si také zobrazit skutečná pravidla zabezpečení prostřednictvím jednotlivce:
- **Síťové rozhraní**: Naučte se [zobrazovat síťové rozhraní](virtual-network-network-interface.md#view-network-interface-settings).
- **NSG**: Naučte se, jak [Zobrazit NSG](manage-network-security-group.md#view-details-of-a-network-security-group).

## <a name="diagnose-using-powershell"></a>Diagnostika pomocí prostředí PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Můžete spustit příkazy, které následují v [Azure Cloud Shell](https://shell.azure.com/powershell), nebo spuštěním PowerShellu z počítače. Azure Cloud Shell je bezplatné interaktivní prostředí. Má předinstalované obecné nástroje Azure, které jsou nakonfigurované pro použití s vaším účtem. Pokud spustíte PowerShell z počítače, budete potřebovat modul Azure PowerShell, verze 1.0.0 nebo novější. Pokud `Get-Module -ListAvailable Az` chcete najít nainstalovanou verzi, spusťte v počítači. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-az-ps). Pokud používáte PowerShell místně, je také potřeba spustit `Connect-AzAccount` pro přihlášení k Azure pomocí účtu, který má [potřebná oprávnění](virtual-network-network-interface.md#permissions).

Získejte platná pravidla zabezpečení pro síťové rozhraní pomocí [Get-AzEffectiveNetworkSecurityGroup](/powershell/module/az.network/get-azeffectivenetworksecuritygroup). Následující příklad získá platná pravidla zabezpečení pro síťové rozhraní s názvem *myVMVMNic*, které je ve skupině prostředků s názvem *myResourceGroup*:

```azurepowershell-interactive
Get-AzEffectiveNetworkSecurityGroup `
  -NetworkInterfaceName myVMVMNic `
  -ResourceGroupName myResourceGroup
```

Výstup se vrátí ve formátu JSON. Chcete-li pochopit výstup, přečtěte si téma [Interpretace výstupu příkazu](#interpret-command-output).
Výstup je vrácen pouze v případě, že je NSG přidružen k síťovému rozhraní, podsíti, ve které je síťové rozhraní, nebo obojí. Virtuální počítač musí být ve spuštěném stavu. Virtuální počítač může mít několik síťových rozhraní s různými skupin zabezpečení sítě. Při řešení potíží spusťte příkaz pro každé síťové rozhraní.

Pokud stále dochází k potížím s připojením, přečtěte si další [informace o](#considerations) [diagnostice](#additional-diagnosis) a důležitých bodech.

Pokud název síťového rozhraní neznáte, ale znáte název virtuálního počítače, ke kterému je síťové rozhraní připojené, následující příkazy vrátí identifikátory všech síťových rozhraní připojených k virtuálnímu počítači:

```azurepowershell-interactive
$VM = Get-AzVM -Name myVM -ResourceGroupName myResourceGroup
$VM.NetworkProfile
```

Zobrazí se výstup podobný následujícímu příkladu:

```output
NetworkInterfaces
-----------------
{/subscriptions/<ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic
```

V předchozím výstupu je název síťového rozhraní *myVMVMNic*.

## <a name="diagnose-using-azure-cli"></a>Diagnostika pomocí Azure CLI

Pokud k dokončení úkolů v tomto článku používáte příkazy rozhraní příkazového řádku Azure (CLI), buď spusťte příkazy v [Azure Cloud Shell](https://shell.azure.com/bash), nebo spuštěním rozhraní příkazového řádku z počítače. Tento článek vyžaduje Azure CLI verze 2.0.32 nebo novější. Nainstalovanou verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli). Pokud používáte Azure CLI místně, musíte taky spustit `az login` a přihlásit se k Azure pomocí účtu, který má [potřebná oprávnění](virtual-network-network-interface.md#permissions).

Získejte platná pravidla zabezpečení pro síťové rozhraní pomocí [AZ Network nic list-efektivní-NSG](/cli/azure/network/nic#az-network-nic-list-effective-nsg). Následující příklad získá platná pravidla zabezpečení pro síťové rozhraní s názvem *myVMVMNic* , které je ve skupině prostředků s názvem *myResourceGroup*:

```azurecli-interactive
az network nic list-effective-nsg \
  --name myVMVMNic \
  --resource-group myResourceGroup
```

Výstup se vrátí ve formátu JSON. Chcete-li pochopit výstup, přečtěte si téma [Interpretace výstupu příkazu](#interpret-command-output).
Výstup je vrácen pouze v případě, že je NSG přidružen k síťovému rozhraní, podsíti, ve které je síťové rozhraní, nebo obojí. Virtuální počítač musí být ve spuštěném stavu. Virtuální počítač může mít několik síťových rozhraní s různými skupin zabezpečení sítě. Při řešení potíží spusťte příkaz pro každé síťové rozhraní.

Pokud stále dochází k potížím s připojením, přečtěte si další [informace o](#considerations) [diagnostice](#additional-diagnosis) a důležitých bodech.

Pokud název síťového rozhraní neznáte, ale znáte název virtuálního počítače, ke kterému je síťové rozhraní připojené, následující příkazy vrátí identifikátory všech síťových rozhraní připojených k virtuálnímu počítači:

```azurecli-interactive
az vm show \
  --name myVM \
  --resource-group myResourceGroup
```

V rámci vráceného výstupu se zobrazí podobné informace jako v následujícím příkladu:

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

V předchozím výstupu je název síťového rozhraní *myVMVMNic Interface*.

## <a name="interpret-command-output"></a>Interpretovat výstup příkazu

Bez ohledu na to, jestli jste pro diagnostiku problému použili [PowerShell](#diagnose-using-powershell)nebo [Azure CLI](#diagnose-using-azure-cli) , se zobrazí výstup, který obsahuje následující informace:

- **NetworkSecurityGroup**: ID skupiny zabezpečení sítě.
- **Asociace**: Určuje, jestli je skupina zabezpečení sítě přidružená k *NetworkInterface* nebo *podsíti*. Pokud je NSG k oběma, vrátí se výstup s **NetworkSecurityGroup**, **asociací** a **EffectiveSecurityRules** pro každé NSG. Pokud je NSG přidružená nebo nepřidružená bezprostředně před spuštěním příkazu k zobrazení platných pravidel zabezpečení, možná budete muset počkat několik sekund, než se změna projeví ve výstupu příkazu.
- **EffectiveSecurityRules**: vysvětlení jednotlivých vlastností je podrobně popsáno v [článku Vytvoření pravidla zabezpečení](manage-network-security-group.md#create-a-security-rule). Názvy pravidel s *defaultSecurityRules/* jsou výchozí pravidla zabezpečení, která existují v každé NSG. Názvy pravidel s *securityRules/* jsou pravidla, která jste vytvořili. Pravidla, která určují [značku služby](./network-security-groups-overview.md#service-tags), jako je například **Internet**, **VirtualNetwork** a **AzureLoadBalancer** pro vlastnosti **DestinationAddressPrefix** nebo **sourceAddressPrefix** , mají také hodnoty pro vlastnost **expandedDestinationAddressPrefix** . Vlastnost **expandedDestinationAddressPrefix** vypíše všechny předpony adres reprezentované značkou služby.

Pokud se ve výstupu zobrazí duplicitní pravidla, je to způsobeno tím, že NSG je přidružen k síťovému rozhraní i podsíti. Obě skupin zabezpečení sítě mají stejná výchozí pravidla a můžou mít další duplicitní pravidla, pokud jste vytvořili vlastní pravidla, která jsou stejná v obou skupin zabezpečení sítě.

Pravidlo s názvem **defaultSecurityRules/DenyAllInBound** znemožňuje příchozí komunikaci s virtuálním počítačem přes port 80 z Internetu, jak je popsáno ve [scénáři](#scenario). Žádné jiné pravidlo s vyšší prioritou (nižším číslem) povoluje port 80 příchozí z Internetu.

## <a name="resolve-a-problem"></a>Vyřešit problém

Ať už pomocí webu Azure [Portal](#diagnose-using-azure-portal), [PowerShellu](#diagnose-using-powershell)nebo rozhraní příkazového [řádku Azure CLI](#diagnose-using-azure-cli) Diagnostikujte problém prezentovaný ve [scénáři](#scenario) v tomto článku, vytvořte pravidlo zabezpečení sítě s následujícími vlastnostmi:

| Vlastnost                | Hodnota                                                                              |
|---------                |---------                                                                           |
| Zdroj                  | Libovolný                                                                                |
| Rozsahy zdrojových portů      | Všechny                                                                                |
| Cíl             | IP adresa virtuálního počítače, rozsah IP adres nebo všechny adresy v podsíti. |
| Rozsahy cílových portů | 80                                                                                 |
| Protokol                | TCP                                                                                |
| Akce                  | Povolit                                                                              |
| Priorita                | 100                                                                                |
| Název                    | Povolení – HTTP – vše                                                                     |

Po vytvoření pravidla je port 80 povolený pro příchozí připojení z Internetu, protože priorita pravidla je vyšší než výchozí pravidlo zabezpečení s názvem *DenyAllInBound*, které zakazuje provoz. Přečtěte si, jak [vytvořit pravidlo zabezpečení](manage-network-security-group.md#create-a-security-rule). Pokud jsou k síťovému rozhraní i podsíti přidruženy různé skupin zabezpečení sítě, musíte stejné pravidlo vytvořit v obou skupin zabezpečení sítě.

Když Azure zpracovává příchozí provoz, zpracovává pravidla v NSG přidružených k podsíti (pokud existuje přidružená NSG) a zpracovává pravidla v NSG přidružených k síťovému rozhraní. Pokud je k síťovému rozhraní a podsíti přidružená NSG, musí být port otevřený v obou skupin zabezpečení sítě, aby se provoz dostal k virtuálnímu počítači. Aby se usnadnila Správa a komunikace, doporučujeme přidružit NSG k podsíti místo individuálních síťových rozhraní. Pokud virtuální počítače v podsíti potřebují odlišná pravidla zabezpečení, můžete učinit síťová rozhraní členy skupiny zabezpečení aplikace (ASG) a určit ASG jako zdroj a cíl pravidla zabezpečení. Přečtěte si další informace o [skupinách zabezpečení aplikací](./network-security-groups-overview.md#application-security-groups).

Pokud stále dochází k potížím s komunikací, přečtěte si téma [předpoklady](#considerations) a další Diagnostika.

## <a name="considerations"></a>Požadavky

Při odstraňování problémů s připojením Vezměte v úvahu následující body:

* Výchozí pravidla zabezpečení blokují příchozí přístup z Internetu a povolují pouze příchozí provoz z virtuální sítě. Pokud chcete povolit příchozí provoz z Internetu, přidejte pravidla zabezpečení s vyšší prioritou než výchozí pravidla. Přečtěte si další informace o [výchozích pravidlech zabezpečení](./network-security-groups-overview.md#default-security-rules)nebo o tom, jak [Přidat pravidlo zabezpečení](manage-network-security-group.md#create-a-security-rule).
* Pokud máte partnerské virtuální sítě, ve výchozím nastavení se značka služby **VIRTUAL_NETWORK** automaticky rozbalí, aby zahrnovala předpony pro partnerské virtuální sítě. Pokud chcete řešit problémy související s partnerským vztahem virtuální sítě, můžete si zobrazit předpony v seznamu **ExpandedAddressPrefix** . Přečtěte si další informace o [partnerském vztahu virtuálních sítí](virtual-network-peering-overview.md) a [značkách služeb](./network-security-groups-overview.md#service-tags).
* Platná pravidla zabezpečení se zobrazují jenom pro síťové rozhraní, pokud je k dispozici NSG přidružené k síťovému rozhraní virtuálního počítače a, nebo podsíť, a pokud je virtuální počítač ve spuštěném stavu.
* Pokud k síťovému rozhraní nebo podsíti nejsou přidružené žádné skupin zabezpečení sítě a máte přiřazenou [veřejnou IP adresu](virtual-network-public-ip-address.md) k virtuálnímu počítači, budou všechny porty otevřené pro příchozí přístup a odchozí přístup odkudkoli. Pokud má virtuální počítač veřejnou IP adresu, doporučujeme použít NSG k podsíti síťového rozhraní.

## <a name="additional-diagnosis"></a>Další Diagnostika

* Pokud chcete spustit rychlý test, abyste zjistili, jestli je povolený provoz do nebo z virtuálního počítače, použijte funkci [ověření toku IP](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md) pro Azure Network Watcher. Při ověřování toků IP adres se dozvíte, jestli je provoz povolený nebo zakázaný. Pokud je odepřený, ověří tok IP adres, které pravidlo zabezpečení odepře přenos.
* Pokud neexistují žádná pravidla zabezpečení, která by způsobila selhání síťového připojení k síti virtuálního počítače, může problém způsobovat tyto příčiny:
  * Software brány firewall běžící v operačním systému virtuálního počítače
  * Trasy nakonfigurované pro virtuální zařízení nebo místní provoz. Internetový provoz se dá přes [vynucené tunelové propojení](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md?toc=%2fazure%2fvirtual-network%2ftoc.json)přesměrovat do místní sítě. Pokud vynutíte tunelový přenos v Internetu na virtuální zařízení nebo v místním prostředí, možná se nebudete moci připojit k virtuálnímu počítači z Internetu. Informace o tom, jak diagnostikovat problémy s směrováním, které by mohly bránit toku provozu z virtuálního počítače, najdete v tématu [Diagnostika problému se směrováním provozu sítě virtuálních počítačů](diagnose-network-routing-problem.md).

## <a name="next-steps"></a>Další kroky

- Přečtěte si o všech úlohách, vlastnostech a nastaveních pro [skupinu zabezpečení sítě](manage-network-security-group.md#work-with-network-security-groups) a [pravidla zabezpečení](manage-network-security-group.md#work-with-security-rules).
- Přečtěte si o [výchozích pravidlech zabezpečení](./network-security-groups-overview.md#default-security-rules), [značkách služby](./network-security-groups-overview.md#service-tags)a [o tom, jak Azure zpracovává pravidla zabezpečení pro příchozí a odchozí provoz](./network-security-groups-overview.md#network-security-groups) pro virtuální počítač.