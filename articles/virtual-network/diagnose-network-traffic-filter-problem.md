---
title: Diagnostika potíží s filtrováním virtuální počítač síťové přenosy | Dokumentace Microsoftu
description: Zjistěte, jak Diagnostika potíží s filtrováním virtuální počítač síťové přenosy zobrazením platná pravidla zabezpečení pro virtuální počítač.
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
ms.openlocfilehash: 366ff0b59835ca3a28cafd5de77c0bd645ff58c5
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46984224"
---
# <a name="diagnose-a-virtual-machine-network-traffic-filter-problem"></a>Diagnostika potíží s filtrováním na provoz sítě virtuálního počítače

V tomto článku se dozvíte, jak chcete diagnostikovat problém filtrování provozu sítě zobrazením pravidla skupiny zabezpečení sítě (NSG) zabezpečení, které platí pro virtuální počítač (VM).

Skupiny zabezpečení sítě vám umožňují řídit typy přenosů tento tok do a z virtuálního počítače. Přidružíte skupinu NSG k podsíti ve virtuální síti Azure, síťové rozhraní připojené k virtuálnímu počítači, nebo obojí. Platná pravidla zabezpečení použijí k síťovému rozhraní se, že agregace pravidel, které existují v této skupině přidružené k síťovému rozhraní a podsíť síťového rozhraní se nachází v. Pravidla v odlišné skupiny Nsg můžete někdy konfliktu mezi sebou a mít vliv na připojení k síti Virtuálních počítačů. Můžete zobrazit všechna platná pravidla zabezpečení ze skupin zabezpečení sítě, které se použijí pro síťová rozhraní Virtuálního počítače. Pokud nejste obeznámeni s virtuální sítí, síťových rozhraní nebo NSG koncepty, najdete v článku [Přehled virtuálních sítí](virtual-networks-overview.md), [síťové rozhraní](virtual-network-network-interface.md), a [přehledskupinzabezpečenísítě](security-overview.md).

## <a name="scenario"></a>Scénář

Při pokusu o připojení k virtuálnímu počítači přes port 80 z Internetu, ale nepovede. Pokud chcete zjistit, proč nelze přistoupit k portu 80 z Internetu, můžete zobrazit platná pravidla zabezpečení pro síťové rozhraní pomocí Azure [portál](#diagnose-using-azure-portal), [PowerShell](#diagnose-using-powershell), nebo [příkazovéhořádkuAzure](#diagnose-using-azure-cli).

Následující kroky předpokládají, že máte existující virtuální počítač chcete zobrazit platná pravidla zabezpečení pro. Pokud nemáte existující virtuální počítač nasadit nejdřív [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) nebo [Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) virtuální počítač k dokončení úkolů v tomto článku se. V příkladech v tomto článku jsou určené pro virtuální počítač s názvem *myVM* se síťovým rozhraním s názvem *myVMVMNic*. Virtuální počítač a síťového rozhraní jsou ve skupině prostředků s názvem *myResourceGroup*a jsou v *USA – východ* oblasti. Změňte hodnoty v krocích, podle potřeby, pro virtuální počítač se Diagnostika pro problém.

## <a name="diagnose-using-azure-portal"></a>Diagnostikovat pomocí webu Azure portal

1. Přihlaste se k Azure [portál](https://portal.azure.com) s účtem Azure, který má [potřebná oprávnění](virtual-network-network-interface.md#permissions).
2. V horní části stránky na webu Azure portal zadejte do vyhledávacího pole název virtuálního počítače. Když se ve výsledcích hledání zobrazí název virtuálního počítače, vyberte ji.
3. V části **nastavení**vyberte **sítě**, jak je znázorněno na následujícím obrázku:

   ![Zobrazit pravidla zabezpečení](./media/diagnose-network-traffic-filter-problem/view-security-rules.png)

   Pravidla vidíte uvedený na předchozím obrázku jsou pro síťové rozhraní s názvem **myVMVMNic**. Zjistíte, že existují **PŘÍCHOZÍ pravidla portů** pro síťové rozhraní ze dvou skupin zabezpečení jinou síť:
   
   - **mySubnetNSG**: přidruženou k podsíti, síťové rozhraní nachází.
   - **myVMNSG**: přidruženou k síťové rozhraní ve virtuálním počítači s názvem **myVMVMNic**.

   Pravidlo s názvem **DenyAllInBound** je co brání příchozí komunikace k virtuálnímu počítači přes port 80 z Internetu, jak je popsáno v [scénář](#scenario). Pravidla seznamů *0.0.0.0/0* pro **zdroj**, což zahrnuje Internetu. Žádné pravidlo s vyšší prioritou (nižším číslem) umožňuje port 80 příchozí. Aby bylo možné portem 80 příchozí provoz do virtuálního počítače z Internetu, přečtěte si téma [vyřešit problém](#resolve-a-problem). Další informace o pravidlech zabezpečení a jak Azure aplikuje je najdete v tématu [skupiny zabezpečení sítě](security-overview.md).

   V dolní části obrázku se zobrazí také **ODCHOZÍ pravidla portů**. V části, která jsou odchozí port pravidla pro síťové rozhraní. I když obrázek zobrazuje pouze čtyři příchozích pravidel pro jednotlivé skupiny NSG, skupin může mít mnoho více než čtyři pravidla. Na obrázku vidíte **VirtualNetwork** pod **zdroj** a **cílové** a **AzureLoadBalancer** pod  **Zdroj**. **Virtuální síť** a **AzureLoadBalancer** jsou [značky služeb](security-overview.md#service-tags). Značky služby představuje skupinu předpon IP adres a tím pomáhá minimalizovat složitost vytváření pravidla zabezpečení.

4. Ujistěte se, že virtuální počítač ve spuštěném stavu a pak vyberte **platná pravidla zabezpečení**, jak je znázorněno na předchozím obrázku, pokud chcete zobrazit platná pravidla zabezpečení, je znázorněno na následujícím obrázku:

   ![Zobrazit platná pravidla zabezpečení](./media/diagnose-network-traffic-filter-problem/view-effective-security-rules.png)

   S pravidly uvedenými jsou že stejné jako jste viděli v kroku 3, i když existují různých kartách pro zabezpečení sítě přidruženou k síťovému rozhraní a podsítě. Jak je vidět na obrázku, zobrazují se jenom prvních 50 pravidel. Chcete-li stáhnout soubor CSV obsahující všechna pravidla, vyberte **Stáhnout**.

   Pokud chcete zobrazit, které předpony adres jednotlivé značky služby představuje, vyberte pravidlo, jako je například pravidlo s názvem **AllowAzureLoadBalancerInbound**. Následující obrázek znázorňuje předpony pro **AzureLoadBalancer** značka služby:

   ![Zobrazit platná pravidla zabezpečení](./media/diagnose-network-traffic-filter-problem/address-prefixes.png)

   I když **AzureLoadBalancer** značka služby představuje pouze jednu předponu, další značky služeb představují několik předpon.

5. V předchozích krocích jsme si ukázali pravidla zabezpečení pro síťové rozhraní s názvem **myVMVMNic**, ale viděli jsme také síťové rozhraní s názvem **myVMVMNic2** v některé z předchozích snímků. Virtuální počítač v tomto příkladu má dvě síťová rozhraní k němu připojená. Platná pravidla zabezpečení mohou být různé pro každé síťové rozhraní.

   Pokud chcete zobrazit pravidla pro **myVMVMNic2** síťové rozhraní, vyberte ji. Jak je znázorněno na následujícím obrázku, síťové rozhraní má stejná pravidla přidružené k jeho podsítě jako **myVMVMNic** síťové rozhraní, protože obě síťová rozhraní jsou ve stejné podsíti. Pokud přidružíte skupinu NSG k podsíti, se použijí její pravidla pro všechna síťová rozhraní v podsíti.

   ![Zobrazit pravidla zabezpečení](./media/diagnose-network-traffic-filter-problem/view-security-rules2.png)

   Na rozdíl od **myVMVMNic** síťového rozhraní **myVMVMNic2** síťové rozhraní nemá skupinu zabezpečení sítě přidruženou k němu. Každé síťové rozhraní a podsítě může mít nula nebo jedna skupina zabezpečení sítě přidružené k němu. Skupiny zabezpečení sítě přidružené k každé síťové rozhraní nebo podsíť může být stejný nebo jiný. Při výběru, můžete přiřadit stejnou skupinu zabezpečení sítě pro libovolný počet síťových rozhraní a podsítí.

I když platná pravidla zabezpečení se zobrazit pomocí virtuálního počítače, můžete také zobrazit platná pravidla zabezpečení prostřednictvím osobně:
- **Síťové rozhraní**: Zjistěte, jak [zobrazit síťové rozhraní](virtual-network-network-interface.md#view-network-interface-settings).
- **Skupina zabezpečení sítě**: Zjistěte, jak [zobrazit skupinu NSG](manage-network-security-group.md#view-details-of-a-network-security-group).

## <a name="diagnose-using-powershell"></a>Diagnostikovat pomocí Powershellu

Můžete spouštět příkazy, které následují v [Azure Cloud Shell](https://shell.azure.com/powershell), nebo pomocí prostředí PowerShell z vašeho počítače. Azure Cloud Shell je bezplatné interaktivní prostředí. Má předinstalované obecné nástroje Azure, které jsou nakonfigurované pro použití s vaším účtem. Při spuštění PowerShell z počítače, je nutné *AzureRM* modul prostředí PowerShell, verze 6.0.1 nebo novější. Spustit `Get-Module -ListAvailable AzureRM` v počítači nainstalovanou verzi zjistíte. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-azurerm-ps). Pokud používáte PowerShell místně, musíte také spustit `Login-AzureRmAccount` přihlášení k Azure pomocí účtu, který má [potřebná oprávnění](virtual-network-network-interface.md#permissions)].

Získání platná pravidla zabezpečení pro síťové rozhraní s [Get-AzureRmEffectiveNetworkSecurityGroup](/powershell/module/azurerm.network/get-azurermeffectivenetworksecuritygroup). Následující příklad získá platná pravidla zabezpečení pro síťové rozhraní s názvem *myVMVMNic*, která je ve skupině prostředků s názvem *myResourceGroup*:

```azurepowershell-interactive
Get-AzureRmEffectiveNetworkSecurityGroup `
  -NetworkInterfaceName myVMVMNic interface `
  -ResourceGroupName myResourceGroup
```

Výstup se vrátí ve formátu json. Ve výstupu najdete v tématu [interpretaci výstupu příkazu](#interpret-command-output).
Výstup je vrácena pouze v případě, že skupina zabezpečení sítě je přidružený k síťovému rozhraní a podsíti, síťové rozhraní se nachází v. Virtuální počítač musí být v běžícím stavu. Virtuální počítač může mít několik síťových rozhraní pomocí různých skupin zabezpečení sítě použít. Při odstraňování potíží, spusťte příkaz pro každé síťové rozhraní.

Pokud stále máte problém připojením, přečtěte si téma [další diagnostiky](#additional-diagnosis) a [aspekty](#considerations).

Pokud neznáte název síťového rozhraní, ale znáte název je síťové rozhraní připojené k virtuálnímu počítači, tyto příkazy vrátí ID všechna síťová rozhraní připojená k virtuálnímu počítači:

```azurepowershell-interactive
$VM = Get-AzureRmVM -Name myVM -ResourceGroupName myResourceGroup
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

Pokud k dokončení úkolů v tomto článku pomocí příkazů rozhraní příkazového řádku Azure (CLI), buď spusťte příkazy [Azure Cloud Shell](https://shell.azure.com/bash), nebo pomocí rozhraní příkazového řádku z vašeho počítače. Tento článek vyžaduje použití Azure CLI verze 2.0.32 nebo novější. Nainstalovanou verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli). Pokud používáte Azure CLI místně, musíte také spustit `az login` a přihlásit se do Azure pomocí účtu, který má [potřebná oprávnění](virtual-network-network-interface.md#permissions).

Získání platná pravidla zabezpečení pro síťové rozhraní s [az network nic list-effective-nsg](/cli/azure/network/nic#az-network-nic-list-effective-nsg). Následující příklad získá platná pravidla zabezpečení pro síťové rozhraní s názvem *myVMVMNic* , který je ve skupině prostředků s názvem *myResourceGroup*:

```azurecli-interactive
az network nic list-effective-nsg \
  --name myVMVMNic \
  --resource-group myResourceGroup
```

Výstup se vrátí ve formátu json. Ve výstupu najdete v tématu [interpretaci výstupu příkazu](#interpret-command-output).
Výstup je vrácena pouze v případě, že skupina zabezpečení sítě je přidružený k síťovému rozhraní a podsíti, síťové rozhraní se nachází v. Virtuální počítač musí být v běžícím stavu. Virtuální počítač může mít několik síťových rozhraní pomocí různých skupin zabezpečení sítě použít. Při odstraňování potíží, spusťte příkaz pro každé síťové rozhraní.

Pokud stále máte problém připojením, přečtěte si téma [další diagnostiky](#additional-diagnosis) a [aspekty](#considerations).

Pokud neznáte název síťového rozhraní, ale znáte název je síťové rozhraní připojené k virtuálnímu počítači, tyto příkazy vrátí ID všechna síťová rozhraní připojená k virtuálnímu počítači:

```azurecli-interactive
az vm show \
  --name myVM \
  --resource-group myResourceGroup
```

V rámci vrácené výstupu se zobrazí informace podobně jako v následujícím příkladu:

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

Ve výstupu předchozí název síťového rozhraní je *myVMVMNic rozhraní*.

## <a name="interpret-command-output"></a>Interpretace výstup příkazu

Bez ohledu na to, zda jste použili [PowerShell](#diagnose-using-powershell), nebo [rozhraní příkazového řádku Azure](#diagnose-using-azure-cli) a Diagnostikujte problém, zobrazí se výstup, který obsahuje následující informace:

- **Skupinu zabezpečení sítě**: ID skupiny zabezpečení sítě.
- **Přidružení**: zda je skupina zabezpečení sítě přidružená k *NetworkInterface* nebo *podsítě*. Pokud je skupina zabezpečení sítě přidružené k oběma, výstup se vrací s **– NetworkSecurityGroup**, **přidružení**, a **EffectiveSecurityRules**, pro jednotlivé skupiny NSG. Pokud skupina zabezpečení sítě je přidružením nebo zrušením přidružení bezprostředně před spuštěním příkazu zobrazíte platná pravidla zabezpečení, budete muset Počkejte několik sekund, aby se změny tak, aby odrážely ve výstupu příkazu.
- **EffectiveSecurityRules**: vysvětlení každé vlastnosti je podrobně popsaná v [vytvořit pravidlo zabezpečení](manage-network-security-group.md#create-a-security-rule). Pravidlo názvy začíná *defaultSecurityRules /* jsou výchozí pravidla zabezpečení, které existují v každé skupině NSG. Pravidlo názvy začíná *securityRules /* pravidla, která jste vytvořili. Pravidla, která určují [značka služby](security-overview.md#service-tags), jako například **Internet**, **VirtualNetwork**, a **AzureLoadBalancer** pro  **destinationAddressPrefix** nebo **hodnotu sourceAddressPrefix** také mít hodnoty pro vlastnosti, **expandedDestinationAddressPrefix** vlastnost. **ExpandedDestinationAddressPrefix** seznamů vlastností všechny předpony adres, které jsou reprezentovány ve značce služby.

Pokud se vám zobrazit duplicitní pravidla uvedená ve výstupu, je to, protože je skupina zabezpečení sítě přidružené k síťovému rozhraní i podsíť. Obě mají stejné výchozí pravidla skupin zabezpečení sítě a může mít duplicitní další pravidla, pokud vytvoříte vlastní pravidla, které jsou stejné v obou skupin zabezpečení sítě.

Pravidlo s názvem **defaultSecurityRules/DenyAllInBound** je co brání příchozí komunikace k virtuálnímu počítači přes port 80 z Internetu, jak je popsáno v [scénář](#scenario). Žádné pravidlo s vyšší prioritou (nižším číslem) umožňuje port 80 příchozí provoz z Internetu.

## <a name="resolve-a-problem"></a>Řešení problémů

Ať už používáte Azure [portál](#diagnose-using-azure-portal), [PowerShell](#diagnose-using-powershell), nebo [rozhraní příkazového řádku Azure](#diagnose-using-azure-cli) a Diagnostikujte problém v [scénář](#scenario) v tomto článek, řešení je vytvořit pravidlo zabezpečení sítě s následujícími vlastnostmi:

| Vlastnost                | Hodnota                                                                              |
|---------                |---------                                                                           |
| Zdroj                  | Všechny                                                                                |
| Rozsahy zdrojových portů      | Všechny                                                                                |
| Cíl             | IP adresa virtuálního počítače, rozsah IP adres nebo všechny adresy v podsíti. |
| Rozsahy cílových portů | 80                                                                                 |
| Protocol (Protokol)                | TCP                                                                                |
| Akce                  | Povolit                                                                              |
| Priorita                | 100                                                                                |
| Název                    | Povolit HTTP-All                                                                     |

Po vytvoření pravidla, port 80 je povolený příchozí provoz z Internetu, protože je vyšší než výchozí pravidlo zabezpečení s názvem prioritu pravidla *DenyAllInBound*, které znemožňuje provoz. Zjistěte, jak [vytvořit pravidlo zabezpečení](manage-network-security-group.md#create-a-security-rule). Pokud různé skupiny zabezpečení sítě jsou přidružené k síťovému rozhraní i podsíti, musíte vytvořit pravidlo stejné v obou skupin zabezpečení sítě.

Když Azure zpracují příchozí provoz, zpracovává pravidla skupiny zabezpečení sítě přidruženou k podsíti (pokud existuje přidruženou skupinu NSG) a pak ho zpracuje pravidla zabezpečení sítě přidruženou k síťovému rozhraní. Pokud je skupina zabezpečení sítě přidružené k síťovému rozhraní a podsítě, port musí být otevřen v obou skupin zabezpečení sítě pro přenosy na virtuální počítač. K usnadnění správy a komunikační problémy, doporučujeme vám, že přidružíte skupinu NSG k podsíti, nikoli jednotlivým síťovým rozhraním. Pokud virtuální počítače v podsíti, pravidla zabezpečení, můžete Zkontrolujte síťový rozhraní členy skupiny zabezpečení aplikací (ASG) a zadat ASG jako zdroj a cíl pravidla zabezpečení. Další informace o [skupiny zabezpečení aplikací](security-overview.md#application-security-groups).

Pokud stále máte problémy s komunikací, přečtěte si téma [aspekty](#considerations) a [další diagnostiky](#additional-dignosis).

## <a name="considerations"></a>Požadavky

Při řešení potíží s připojením problémy, zvažte následující body:

* Výchozí pravidla zabezpečení blokovat příchozí přístup z Internetu a pouze povolit příchozí provoz z virtuální sítě. Povolit příchozí přenosy z Internetu, přidejte pravidla zabezpečení s vyšší prioritou než výchozí pravidla. Další informace o [výchozích pravidlech zabezpečení](security-overview.md#default-security-rules), nebo jak [přidat pravidlo zabezpečení](manage-network-security-group.md#create-a-security-rule).
* Pokud mají partnerský vztah virtuálních sítí, ve výchozím nastavení, **VIRTUAL_NETWORK** značka služby se automaticky rozšíří a zahrne předpony pro partnerských virtuálních sítích. Chcete-li řešit jakékoli problémy spojené s partnerský vztah virtuální sítě, můžete zobrazit předponám v **ExpandedAddressPrefix** seznamu. Další informace o [partnerský vztah virtuální sítě](virtual-network-peering-overview.md) a [značky služeb](security-overview.md#service-tags).
* Platná pravidla zabezpečení se zobrazují pouze pro síťové rozhraní, pokud je skupina zabezpečení sítě přidružené k síťové rozhraní Virtuálního počítače a, nebo podsíť, a pokud je virtuální počítač ve spuštěném stavu.
* Pokud neexistují žádné skupiny zabezpečení sítě přidružené k síťovému rozhraní nebo podsíť, a vy musíte [veřejnou IP adresu](virtual-network-public-ip-address.md) přiřazené k virtuálnímu počítači, všechny porty jsou otevřené pro příchozí přístup z a odchozí přístup k kdekoli. Pokud virtuální počítač má veřejnou IP adresu, doporučujeme použít skupinu zabezpečení sítě k podsíti síťové rozhraní.

## <a name="additional-diagnosis"></a>Další diagnóza

* Chcete-li spustit rychlé otestování k určení, pokud je povolený provoz do nebo z virtuálního počítače, použijte [ověření toku protokolu IP](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md) funkce služby Azure Network Watcher. Ověření toku protokolu IP, zjistíte, pokud je povolený nebo zakázaný provoz. Pokud byl odepřen, ověření toku protokolu IP, zjistíte, které pravidlo zabezpečení se odepření provozu.
* Pokud nejsou žádná pravidla zabezpečení způsobí připojení k síti Virtuálního počítače selže, může být problém z důvodu:
  * Software brány firewall v rámci operačního systému Virtuálního počítače
  * Trasy nakonfigurované pro virtuální zařízení nebo místní přenosy. Je možné přesměrovat přenosy z Internetu do vaší místní síti prostřednictvím [vynucené tunelování](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Pokud vynutíte tunelového propojení internetový provoz do virtuálního zařízení nebo v místním, nebudete moci připojit k virtuálnímu počítači z Internetu. Zjistěte, jak diagnostikovat problémy trasy, které by mohly bránit toku provozu z virtuálního počítače, naleznete v tématu [diagnostikovat problém směrování provozu sítě virtuálních počítačů](diagnose-network-routing-problem.md).

## <a name="next-steps"></a>Další postup

- Další informace o úlohách, vlastnosti a nastavení [skupinu zabezpečení sítě](manage-network-security-group.md#work-with-network-security-groups) a [pravidla zabezpečení](manage-network-security-group.md#work-with-security-rules).
- Další informace o [výchozích pravidlech zabezpečení](security-overview.md#default-security-rules), [značky služeb](security-overview.md#service-tags), a [zpracování pravidla zabezpečení pro příchozí a odchozí provoz Azure](security-overview.md#network-security-groups) pro virtuální počítač.
