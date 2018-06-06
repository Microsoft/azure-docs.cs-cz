---
title: Virtuální počítač síťový provoz filtru problém diagnostikovat | Microsoft Docs
description: Naučte se Diagnostika virtuálního počítače síťový provoz filtru problém zobrazením pravidla efektivní zabezpečení pro virtuální počítač.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: a54feccf-0123-4e49-a743-eb8d0bdd1ebc
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/29/2018
ms.author: jdial
ms.openlocfilehash: 1c33a75363eec2b4e338ba64e3d1ad877d8b1610
ms.sourcegitcommit: 4f9fa86166b50e86cf089f31d85e16155b60559f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2018
ms.locfileid: "34757223"
---
# <a name="diagnose-a-virtual-machine-network-traffic-filter-problem"></a>Diagnostikovat problém filtru provoz sítě virtuálního počítače

V tomto článku zjistěte, jak diagnostikovat potíže filtru provoz v síti zobrazením pravidel zabezpečení skupiny (NSG) zabezpečení sítě, které jsou platné pro virtuální počítač (VM).

Skupiny Nsg umožní určit typy provozu s tokem do/z virtuálního počítače. Přidružíte skupinu NSG k podsíti v Azure virtuální sítě, síťové rozhraní připojené k virtuálnímu počítači, nebo obojí. Efektivní zabezpečení pravidla použije k síťovému rozhraní jsou agregaci pravidla, které existují v této skupině přiřazené k síťovému rozhraní a podsíť síťového rozhraní. Pravidla v odlišné skupiny Nsg můžete někdy vzájemném konfliktu a mít vliv na připojení k síti Virtuálního počítače. Můžete zobrazit všechna pravidla efektivní zabezpečení ze skupiny Nsg, které jsou použity na rozhraní sítě Virtuálního počítače. Pokud si nejste obeznámeni s virtuální sítě, síťové nebo NSG koncepty, najdete v části [Přehled virtuálních sítí](virtual-networks-overview.md), [síťové rozhraní](virtual-network-network-interface.md), a [skupiny zabezpečení sítě přehled](security-overview.md).

## <a name="scenario"></a>Scénář

Pokusí se připojit k virtuálnímu počítači přes port 80 z Internetu, ale připojení se nezdaří. Pokud chcete zjistit, proč nelze přístup k portu 80 z Internetu, můžete zobrazit pravidla efektivní zabezpečení pro síťové rozhraní pomocí třídy Azure [portál](#diagnose-using-azure-portal), [prostředí PowerShell](#diagnose-using-powershell), nebo [rozhraní příkazového řádku Azure](#diagnose-using-azure-cli).

Následující postup předpokládá, že máte existující virtuální počítač k zobrazení pravidel efektivní zabezpečení pro. Pokud nemáte existující virtuální počítač, nejprve nasadit [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) nebo [Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) virtuálních počítačů k dokončení úkolů v tomto článku se. V příkladech v tomto článku jsou pro virtuální počítač s názvem *Můjvp* s síťové rozhraní s názvem *myVMVMNic*. Virtuální počítač a síťového rozhraní jsou ve skupině prostředků s názvem *myResourceGroup*a jsou v *východní USA* oblast. Změňte hodnoty v krocích, podle potřeby, pro virtuální počítač se diagnostiky problému pro.

## <a name="diagnose-using-azure-portal"></a>Diagnostikovat pomocí portálu Azure

1. Přihlaste se k Azure [portál](https://portal.azure.com) s účtem Azure, který má [potřebná oprávnění](virtual-network-network-interface.md#permissions).
2. V horní části portálu Azure zadejte do vyhledávacího pole název virtuálního počítače. Když ve výsledcích hledání se zobrazí název virtuálního počítače, vyberte ho.
3. V části **nastavení**, vyberte **sítě**, jak je znázorněno na následujícím obrázku:

    ![Zobrazení pravidla zabezpečení](./media/diagnose-network-traffic-filter-problem/view-security-rules.png)

    Pravidla se zobrazí uvedené na předchozím obrázku se pro síťové rozhraní s názvem **myVMVMNic**. Uvidíte, že existují **PŘÍCHOZÍ pravidla portu** pro síťové rozhraní ze dvou skupin zabezpečení jinou síť:- **mySubnetNSG**: přidružené k podsíti, ke které je v rozhraní sítě.
        - **myVMNSG**: přidružený k síťovému rozhraní ve virtuálním počítači s názvem **myVMVMNic**.

    Pravidlo s názvem **DenyAllInBound** je co znemožňuje příchozí komunikaci k virtuálnímu počítači přes port 80 a z Internetu, jak je popsáno v [scénář](#scenario). Seznamy pravidlo *0.0.0.0/0* pro **zdroj**, což zahrnuje Internetu. Žádné pravidlo s vyšší prioritou (nižší číslo) umožňuje port 80 příchozí. K povolení portu 80 příchozí k virtuálnímu počítači z Internetu, najdete v části [vyřešit problém](#resolve-a-problem). Další informace o pravidlech zabezpečení a jak se vztahují Azure je najdete v tématu [skupin zabezpečení sítě](security-overview.md).

    V dolní části na obrázku, můžete také zjistit **ODCHOZÍ pravidla portu**. V části, která jsou pravidla odchozí port pro síťové rozhraní. Když na obrázku se zobrazí pouze čtyři příchozích pravidel pro jednotlivé skupiny NSG, proto může mít mnoho více než čtyři pravidla. Na obrázku vidíte **virtuální síť** pod **zdroj** a **cílové** a **AzureLoadBalancer** pod  **Zdroj**. **Virtuální síť** a **AzureLoadBalancer** jsou [služby značky](security-overview.md#service-tags). Značky služby představují skupinu předpony IP adres, abyste minimalizovali složitost pro vytvoření pravidla zabezpečení.

4. Zajistěte, aby byl virtuální počítač v běžícím stavu a pak vyberte **pravidla efektivní zabezpečení**, jak je vidět na předchozím obrázku, zobrazíte pravidla efektivní zabezpečení, která je znázorněno na následujícím obrázku:

    ![Zobrazení pravidla efektivní zabezpečení](./media/diagnose-network-traffic-filter-problem/view-effective-security-rules.png)

    Pravidla uvedené jsou že stejné jako jste viděli v kroku 3, i když existují různých kartách pro NSG přidruženou rozhraní sítě a podsítě. Jak vidíte na obrázku, jsou uvedeny pouze prvních 50 pravidla. Chcete-li stáhnout soubor .csv, který obsahuje všechna pravidla, vyberte **Stáhnout**.

    V tématu, které předpony jednotlivé značky služby představuje, vyberte pravidlo, jako je například pravidlo s názvem **AllowAzureLoadBalancerInbound**. Následující obrázek znázorňuje předpony **AzureLoadBalancer** služby značky:

    ![Zobrazení pravidla efektivní zabezpečení](./media/diagnose-network-traffic-filter-problem/address-prefixes.png)

    I když **AzureLoadBalancer** výrobní číslo představuje pouze jednu předponu, jiné značky služby představují několik předpon.

4. Předchozí kroky vám ukázal, pravidla zabezpečení pro síťové rozhraní s názvem **myVMVMNic**, ale také jste viděli síťové rozhraní s názvem **myVMVMNic2** v některé z předchozích obrázky. Virtuální počítač v tomto příkladu má dvě rozhraní sítě, které jsou k němu připojen. Efektivní zabezpečení pravidla mohou být různé pro každé síťové rozhraní.

    Chcete-li zobrazit pravidla pro **myVMVMNic2** síťové rozhraní, vyberte ji. Jak je znázorněno na obrázku, který následuje, rozhraní sítě má stejná pravidla přidružené k jeho podsíti jako **myVMVMNic** síťové rozhraní, protože obě rozhraní sítě jsou ve stejné podsíti. Pokud přidružíte skupinu NSG k podsíti, jeho pravidla platí pro všechna síťová rozhraní v podsíti.

    ![Zobrazení pravidla zabezpečení](./media/diagnose-network-traffic-filter-problem/view-security-rules2.png)

    Na rozdíl od **myVMVMNic** síťového rozhraní, **myVMVMNic2** síťové rozhraní nemá skupinu zabezpečení sítě spojenou k němu. Každé rozhraní sítě a podsítě může být nula nebo jeden NSG přidružené k němu. NSG přidružený ke každé rozhraní sítě nebo podsítě může být stejné nebo jiné. A zvolte můžete přidružit stejnou skupinu zabezpečení sítě na libovolný počet síťových rozhraní a podsítě.

I když pravidla efektivní zabezpečení měla zobrazit pomocí virtuální počítač, můžete také zobrazit pravidla efektivní zabezpečení prostřednictvím:
- **Jednotlivá síťová rozhraní**: Zjistěte, jak [zobrazení síťové rozhraní](virtual-network-network-interface.md#view-network-interface-settings).
- **Jednotlivé skupiny NSG**: Zjistěte, jak [zobrazit skupinu NSG](manage-network-security-group.md#view-details-of-a-network-security-group).

## <a name="diagnose-using-powershell"></a>Diagnostikovat pomocí prostředí PowerShell

Můžete spouštět příkazy, které následují v [prostředí cloudu Azure](https://shell.azure.com/powershell), nebo pomocí spouštění prostředí PowerShell z vašeho počítače. Prostředí cloudové služby Azure je interaktivní prostředí volné. Má předinstalované obecné nástroje Azure, které jsou nakonfigurované pro použití s vaším účtem. Pokud spustíte prostředí PowerShell z vašeho počítače, musíte *AzureRM* modul prostředí PowerShell, verze 6.0.1 nebo novější. Spustit `Get-Module -ListAvailable AzureRM` na počítači s nainstalovanou verzi. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-azurerm-ps). Pokud používáte PowerShell místně, musíte také spustit `Login-AzureRmAccount` k přihlášení do Azure pomocí účtu, který má [potřebná oprávnění](virtual-network-network-interface.md#permissions)].

Získat efektivní zabezpečení pravidla pro síťové rozhraní s [Get-AzureRmEffectiveNetworkSecurityGroup](/powershell/module/azurerm.network/get-azurermeffectivenetworksecuritygroup). Následující příklad získá pravidla efektivní zabezpečení pro síťové rozhraní s názvem *myVMVMNic*, která je ve skupině prostředků s názvem *myResourceGroup*:

```azurepowershell-interactive
Get-AzureRmEffectiveNetworkSecurityGroup `
  -NetworkInterfaceName myVMVMNic interface `
  -ResourceGroupName myResourceGroup
```

Výstup se vrátí ve formátu json. Pochopit výstup, najdete v části [interpretovat výstupu příkazu](#interpret-command-output).
Výstup je vrácena pouze pokud je skupina NSG přidružený rozhraní sítě, podsítě, které je v rozhraní sítě nebo obojí. Virtuální počítač musí být ve stavu spuštěno. Virtuální počítač může mít více síťových rozhraní s odlišné skupiny Nsg použije. Při odstraňování problémů s, spusťte příkaz pro každé síťové rozhraní.

Pokud máte pořád s připojení problém, přečtěte si téma [další diagnostiky](#additional-diagnosis) a [aspekty](#considerations).

Pokud neznáte název síťového rozhraní, ale znáte název síťové rozhraní je připojen k virtuálnímu počítači, následující příkazy vrátí ID všech síťových rozhraní připojených k virtuálnímu počítači:

```azurepowershell-interactive
$VM = Get-AzureRmVM -Name myVM -ResourceGroupName myResourceGroup
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

Pokud používáte rozhraní příkazového řádku Azure (CLI) příkazy k dokončení úloh v tomto článku, buď spusťte příkazy [prostředí cloudu Azure](https://shell.azure.com/bash), nebo spuštěním rozhraní příkazového řádku z vašeho počítače. Tento článek vyžaduje Azure CLI verze 2.0.32 nebo novější. Nainstalovanou verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI 2.0](/cli/azure/install-azure-cli). Pokud používáte Azure CLI místně, musíte také spustit `az login` a přihlaste se k Azure pomocí účtu, který má [potřebná oprávnění](virtual-network-network-interface.md#permissions).

Získat efektivní zabezpečení pravidla pro síťové rozhraní s [az sítě seskupování seznamu platné nsg](/cli/azure/network/nic#az-network-nic-list-effective-nsg). Následující příklad získá pravidla efektivní zabezpečení pro síťové rozhraní s názvem *myVMVMNic* , je ve skupině prostředků s názvem *myResourceGroup*:

```azurecli-interactive
az network nic list-effective-nsg \
  --name myVMVMNic \
  --resource-group myResourceGroup
```

Výstup se vrátí ve formátu json. Pochopit výstup, najdete v části [interpretovat výstupu příkazu](#interpret-command-output).
Výstup je vrácena pouze pokud je skupina NSG přidružený rozhraní sítě, podsítě, které je v rozhraní sítě nebo obojí. Virtuální počítač musí být ve stavu spuštěno. Virtuální počítač může mít více síťových rozhraní s odlišné skupiny Nsg použije. Při odstraňování problémů s, spusťte příkaz pro každé síťové rozhraní.

Pokud máte pořád s připojení problém, přečtěte si téma [další diagnostiky](#additional-diagnosis) a [aspekty](#considerations).

Pokud neznáte název síťového rozhraní, ale znáte název síťové rozhraní je připojen k virtuálnímu počítači, následující příkazy vrátí ID všech síťových rozhraní připojených k virtuálnímu počítači:

```azurecli-interactive
az vm show \
  --name myVM \
  --resource-group myResourceGroup
```

V rámci vrácený výstup najdete informace, podobně jako v následujícím příkladu:

```azurecli
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

V předchozích výstup, je název síťového rozhraní *myVMVMNic rozhraní*.

## <a name="interpret-command-output"></a>Interpretace výstupu příkazu

Bez ohledu na to, zda jste použili [prostředí PowerShell](#diangose-using-powershell), nebo [rozhraní příkazového řádku Azure](#diagnose-using-azure-cli) a diagnostikovat problém, obdržíte výstupu, který obsahuje následující informace:

- **Skupinu zabezpečení sítě**: ID skupiny zabezpečení sítě.
- **Přidružení**: zda skupinu zabezpečení sítě je přidružen k *NetworkInterface* nebo *podsítě*. Pokud je přidružena k obě skupiny NSG, vrátí se výstup s **skupinu zabezpečení sítě**, **přidružení**, a **EffectiveSecurityRules**, pro jednotlivé skupiny NSG. Pokud NSG přidružené nebo zrušit přidružení bezprostředně před spuštěním příkazu zobrazíte pravidla efektivní zabezpečení, možná budete muset Počkejte několik sekund pro změnu tak, aby odrážela ve výstupu příkazu.
- **EffectiveSecurityRules**: vysvětlení každé vlastnosti je podrobně popsaná v [vytvořit pravidlo zabezpečení](manage-network-security-group.md#create-a-security-rule). Pravidla pro názvy kterými *defaultSecurityRules /* se výchozí pravidla zabezpečení, které existují v každé skupině. Pravidla pro názvy kterými *securityRules /* jsou pravidla, které jste vytvořili. Pravidla určující [služby značky](security-overview.md#service-tags), například **Internet**, **virtuální síť**, a **AzureLoadBalancer** pro  **destinationAddressPrefix** nebo **sourceAddressPrefix** vlastnosti, také mít hodnoty **expandedDestinationAddressPrefix** vlastnost. **ExpandedDestinationAddressPrefix** vlastnost zobrazí seznam všech předpon adres reprezentována výrobní číslo.

Pokud se zobrazí duplicitní pravidla uvedeného ve výstupu, je to, protože je skupina NSG přidružená k rozhraní sítě a podsítě. Obou skupin Nsg mít stejnou výchozí pravidla a může mít další duplicitní pravidla, pokud jste vytvořili vlastní pravidla, které jsou stejné ve obě skupiny Nsg.

Pravidlo s názvem **defaultSecurityRules/DenyAllInBound** je co znemožňuje příchozí komunikaci k virtuálnímu počítači přes port 80 a z Internetu, jak je popsáno v [scénář](#scenario). Žádné pravidlo s vyšší prioritou (nižší číslo) umožňuje port 80 příchozí z Internetu.

## <a name="resolve-a-problem"></a>Vyřešte problém

Ať už používáte Azure [portál](#diagnose-using-azure-portal), [prostředí PowerShell](#diagnose-using-powershell), nebo [rozhraní příkazového řádku Azure](#diagnose-using-azure-cli) a diagnostikovat problém uvedené v [scénář](#scenario) v tomto článek, řešení je k vytvoření pravidla zabezpečení sítě s následujícími vlastnostmi:

| Vlastnost                | Hodnota                                                                              |
|---------                |---------                                                                           |
| Zdroj                  | Všechny                                                                                |
| Rozsahy zdrojových portů      | Všechny                                                                                |
| Cíl             | IP adresu virtuálního počítače, rozsah IP adres nebo všechny adresy v podsíti. |
| Rozsahy cílových portů | 80                                                                                 |
| Protocol (Protokol)                | TCP                                                                                |
| Akce                  | Povolit                                                                              |
| Priorita                | 100                                                                                |
| Název                    | Povolit všechny HTTP                                                                     |

Po vytvoření pravidla, je port 80 povolený příchozí z Internetu, protože Priorita pravidla je vyšší než výchozí pravidlo zabezpečení s názvem *DenyAllInBound*, které znemožňuje provoz. Zjistěte, jak [vytvořit pravidlo zabezpečení](manage-network-security-group.md#create-a-security-rule). Pokud jsou odlišné skupiny Nsg k síťové rozhraní a podsíť, musíte vytvořit stejného pravidla v obou skupin Nsg.

Pokud Azure procesy příchozí provoz, zpracovává pravidla v této skupině přidružené k podsíti (Pokud je skupina NSG přidružená) a pak ho zpracuje pravidla v této skupině přidružené k síťové rozhraní. Pokud je skupina NSG přidružené k rozhraní sítě a podsítě, port musí být otevřen v obou skupin Nsg pro provoz připojit virtuální počítač. K usnadnění správy a komunikace problémy, doporučujeme, aby přidružíte skupinu NSG k podsíti, nikoli jednotlivých síťových rozhraní. Pokud virtuální počítače v podsíti potřebovat zabezpečení různých pravidel, můžete Zkontrolujte síťový rozhraní členy skupiny zabezpečení aplikací (ASG) a zadejte ASG jako zdrojové a cílové pravidlo zabezpečení. Další informace o [skupin zabezpečení aplikací](security-overview.md#application-security-groups).

Pokud máte pořád problémy komunikace, přečtěte si téma [aspekty](#considerations) a [další diagnostiky](#additional-dignosis).

## <a name="considerations"></a>Požadavky

Při řešení potíží s připojením problémy, zvažte následující body:

* Výchozí pravidla zabezpečení blokují příchozí přístup z Internetu a pouze povolit příchozí provoz z virtuální sítě. Chcete-li povolit příchozí přenosy z Internetu, přidejte pravidla zabezpečení s vyšší prioritou než výchozí pravidla. Další informace o [výchozí pravidla zabezpečení](security-overview.md#default-security-rules), nebo jak [přidat pravidlo zabezpečení](manage-network-security-group.md#create-a-security-rule).
* Pokud máte peered virtuální sítě, ve výchozím nastavení, **VIRTUAL_NETWORK** výrobní číslo se automaticky rozšíří a bude obsahovat předpon pro peered virtuální sítě. Chcete-li vyřešit případné problémy spojené s virtuální sítě partnerského vztahu, můžete zobrazit předpony v **ExpandedAddressPrefix** seznamu. Další informace o [partnerský vztah virtuální sítě](virtual-network-peering-overview.md) a [služby značky](security-overview.md#service-tags).
* Efektivní zabezpečení pravidla se zobrazují pouze pro síťové rozhraní, pokud je skupina NSG přidružená síťové rozhraní Virtuálního počítače a, nebo podsíť, a pokud je virtuální počítač v běžícím stavu.
* Pokud neexistují žádné skupiny zabezpečení sítě spojených s rozhraní sítě nebo podsítě, a budete mít [veřejnou IP adresu](virtual-network-public-ip-address.md) přiřazené k virtuálnímu počítači, jsou všechny porty otevřené pro příchozí přístup z a odchozí přístup k odkudkoli. Pokud virtuální počítač má veřejnou IP adresu, doporučujeme použít skupinu NSG k podsíti síťové rozhraní.

## <a name="additional-diagnosis"></a>Další diagnóza

* Spustit Rychlé test ke zjištění, pokud provoz je povolený do nebo z virtuálního počítače, použijte [IP tok ověření](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md) schopností sledovací proces sítě Azure. Tok IP ověřte zjistíte, pokud provoz povolený nebo zakázaný. Pokud byl odepřen, IP toku ověřte zjistíte, které pravidlo zabezpečení je odepřen, provoz.
* Pokud neexistují žádná pravidla zabezpečení připojení k síti Virtuálního počítače k selhání způsobuje, může být problém z důvodu:
  * Software brány firewall běžící v rámci operačního systému Virtuálního počítače
  * Trasy nakonfigurované pro virtuální zařízení nebo místní provoz. Internetové přenosy můžete přesměrovat do místní sítě prostřednictvím [vynucené tunelování](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Pokud můžete vynutit tunelové propojení internetové přenosy na virtuální zařízení nebo na místní, nemusí být možné se připojit k virtuálnímu počítači z Internetu. Další postupy k diagnostikování problémů trasy, které by mohly bránit tok provozu z virtuálního počítače najdete v tématu [diagnostikovat problém směrování provozu sítě virtuálního počítače](diagnose-network-routing-problem.md).

## <a name="next-steps"></a>Další postup

- Další informace o úlohách, vlastnosti a nastavení [skupinu zabezpečení sítě](manage-network-security-group.md#work-with-network-security-groups) a [pravidla zabezpečení](manage-network-security-group.md#work-with-security-rules).
- Další informace o [výchozí pravidla zabezpečení](security-overview.md#default-security-rules), [služby značky](security-overview.md#service-tags), a [jak Azure zpracovává pravidla zabezpečení pro příchozí a odchozí přenosy](security-overview.md#network-security-groups) pro virtuální počítač.
