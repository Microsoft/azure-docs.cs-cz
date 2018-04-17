---
title: Co je síť Azure seznam řízení přístupu?
description: Další informace o seznamy řízení přístupu v Azure
services: virtual-network
documentationcenter: na
author: genli
manager: cshepard
editor: ''
tags: azure-service-management
ms.assetid: 83d66c84-8f6b-4388-8767-cd2de3e72d76
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: genli
ms.openlocfilehash: b2239ae6393e74a518522594d36f7b9c30d2a6f7
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="what-is-an-endpoint-access-control-list"></a>Co je seznam řízení přístupu koncový bod?

> [!IMPORTANT]
> Azure má dva různé [modely nasazení](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json) pro vytváření a práci s prostředky: Resource Manager a Klasický model. Tento článek se věnuje použití klasického modelu nasazení. Společnost Microsoft doporučuje, aby většina nových nasazení používala model nasazení Resource Manager. 

Seznam řízení přístupu (ACL) koncového bodu je prvkem zvýšení zabezpečení, která je k dispozici pro vaše nasazení Azure. Seznam ACL umožňuje selektivně povolit nebo zakázat přenosů pro koncový bod virtuálního počítače. Tato funkce filtrování paketů poskytuje další úroveň zabezpečení. Můžete zadat sítě ACL pro koncové body pouze. Nelze zadat seznam ACL pro virtuální síť nebo podsíť konkrétní obsažené ve virtuální síti. Doporučujeme používat skupiny zabezpečení sítě (Nsg) namísto seznamy ACL, kdykoli je to možné. Další informace o skupinách Nsg najdete v tématu [přehled skupiny zabezpečení sítě](virtual-networks-nsg.md)

Seznamy ACL můžete nakonfigurovat pomocí prostředí PowerShell nebo portálu Azure. Konfigurace sítě ACL pomocí prostředí PowerShell najdete v tématu [seznamy řízení přístupu pro správu pro koncové body pomocí prostředí PowerShell](virtual-networks-acl-powershell.md). Konfigurace sítě ACL pomocí portálu Azure najdete v tématu [jak nastavit koncové body k virtuálnímu počítači](../virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

Pomocí seznamů ACL sítě, můžete provést následující:

* Selektivně povolit nebo zakázat příchozí přenosů na základě vzdálené podsítě rozsah IPv4 adres pro vstupní koncový bod virtuálního počítače.
* Zakázaných IP adresy
* Vytvořením několika pravidel pro koncový bod virtuálního počítače
* Použití pravidla řazení zajistit správnou sadu pravidel, se použijí na koncový bod daným virtuálním počítačem (nejnižší a nejvyšší)
* Zadejte seznam ACL pro určité vzdálené podsítě IPv4 adresu.

Najdete v článku [Azure omezuje](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits) článku omezení řízení přístupu.

## <a name="how-acls-work"></a>Jak fungují seznamy ACL
Seznam ACL je objekt, který obsahuje seznam pravidel. Při vytvoření seznamu ACL a použijte ho pro koncový bod virtuálního počítače, filtrování paketů probíhá na uzlu hostitele virtuálního počítače. To znamená, že provoz z vzdálené IP adresy se filtrují podle uzlu hostitele pro odpovídající pravidla seznamu ACL místo na vašem virtuálním počítači. Virtuální počítač zabrání výdaje drahocenný cyklů procesoru na filtrování paketů.

Při vytváření virtuálního počítače, je výchozí seznam ACL uveďte zavedené blokovat veškerý příchozí provoz. Ale pokud koncový bod vytvořen pro (port 3389), pak výchozí seznamu ACL je upravit tak, aby povolit veškerý příchozí provoz pro tento koncový bod. Příchozí přenosy z jakékoli vzdálené podsítě je pak mohou tohoto koncového bodu a žádné brány firewall zřizování je požadovaná. Všechny ostatní porty jsou zablokovány pro příchozí provoz, pokud jsou koncové body vytvořené pro tyto porty. Ve výchozím nastavení je povoleno odchozí provoz.

**Příklad seznamu ACL výchozí tabulky**

| **Pravidlo č** | **Vzdálené podsítě** | **koncový bod** | **Povolení nebo odepření** |
| --- | --- | --- | --- |
| 100 |0.0.0.0/0 |3389 |Povolit |

## <a name="permit-and-deny"></a>Povolit a odepřít
Můžete selektivně povolit nebo odepřít síťových přenosů pro vstupní koncový bod virtuálního počítače vytvořením pravidel, které určují "Povolit" nebo "deny". Je důležité si uvědomit, že ve výchozím nastavení, když je vytvořen koncový bod, jsou povoleny všechny přenosy ke koncovému bodu. Z tohoto důvodu je důležité pochopit, jak vytvořit pravidla povolení nebo odepření a umístit je ve správném pořadí priorit, pokud chcete, aby podrobnou kontrolu nad síťový provoz, který můžete zvolit, aby k dosažení koncový bod virtuálního počítače.

Body vzít v úvahu:

1. **Žádné ACL –** ve výchozím nastavení při vytvoření koncový bod jsme povolit všechny pro koncový bod.
2. **Povolit -** při přidávání jednoho nebo více "Povolit" rozsahy jsou odepření všechny ostatní rozsahy ve výchozím nastavení. Jenom pakety z povolených rozsah IP adres bude komunikovat s koncový bod virtuálního počítače.
3. **Odepřít -** při přidávání jednoho nebo více "deny" rozsahy jsou umožňující všechny rozsahy provozu ve výchozím nastavení.
4. **Kombinace povolení a Odepřít -** můžete použít kombinaci "Povolit" a "deny", pokud chcete vyčlenit konkrétní rozsah IP má být povolen nebo odepřen přístup.

## <a name="rules-and-rule-precedence"></a>Priorita pravidla a pravidla
Seznamy ACL sítě můžete nastavit na konkrétní virtuální počítač koncové body. Můžete například zadat síť seznamu ACL pro koncový bod RDP vytvořit na virtuálním počítači, jaké zámky dolů přístup pro určité IP adresy. Následující tabulka ukazuje způsob k udělení přístupu k veřejné virtuální IP adresy (VIP) určité rozsahu tak, aby povolovala přístup pro protokol RDP. Jiné vzdálené IP je odepřen. Jsme podle *nejnižší má přednost před* pravidlo pořadí.

### <a name="multiple-rules"></a>Více pravidel
V následujícím příkladu, pokud chcete povolit přístup ke koncovému bodu protokolu RDP pouze z dva veřejné rozsahy IPv4 adres (65.0.0.0/8 a 159.0.0.0/8), lze dosáhnout zadáním dva *povolit* pravidla. V takovém případě vzhledem k tomu, že protokol RDP se vytvoří ve výchozím nastavení pro virtuální počítač, můžete zablokovat přístup k portu RDP na základě vzdálené podsítě. Následující příklad ukazuje způsob k udělení přístupu k veřejné virtuální IP adresy (VIP) určité rozsahu tak, aby povolovala přístup pro protokol RDP. Jiné vzdálené IP je odepřen. Toto funguje, protože sítě seznamy ACL se dá nastavit pro koncový bod konkrétní virtuální počítač a ve výchozím nastavení je odepřen přístup.

**Příklad – více pravidel**

| **Pravidlo č** | **Vzdálené podsítě** | **koncový bod** | **Povolení nebo odepření** |
| --- | --- | --- | --- |
| 100 |65.0.0.0/8 |3389 |Povolit |
| 200 |159.0.0.0/8 |3389 |Povolit |

### <a name="rule-order"></a>Pořadí pravidel
Vzhledem k tomu, že pro koncový bod lze zadat více pravidel, musí být způsob, jak uspořádat pravidla, aby bylo možné zjistit, které pravidlo přednost. Pořadí pravidlo určuje prioritu. Sítě postupujte podle seznamů řízení přístupu *nejnižší má přednost před* pravidlo pořadí. V následujícím příkladu je koncový bod na portu 80 selektivně udělen přístup k jenom určitých rozsahů IP adres. To můžete nakonfigurovat, máme pravidlo odepření (pravidlo \# 100) pro adresy v prostoru 175.1.0.1/24. Druhé pravidlo je potom zadat s prioritou 200, která umožňuje přístup k všechny adresy v rámci 175.0.0.0/8.

**Příklad – pravidla priorit**

| **Pravidlo č** | **Vzdálené podsítě** | **koncový bod** | **Povolení nebo odepření** |
| --- | --- | --- | --- |
| 100 |175.1.0.1/24 |80 |Odepřít |
| 200 |175.0.0.0/8 |80 |Povolit |

## <a name="network-acls-and-load-balanced-sets"></a>Seznamy ACL sítě a nastaví vyrovnáváním zatížení
Seznamy ACL sítě lze na sadu koncový bod Vyrovnávání zatížení. Pokud je seznam ACL zadaná pro skupinu s vyrovnáváním zatížení, sítě ACL platí pro všechny virtuální počítače v dané sadě skupinu s vyrovnáváním zatížení. Například pokud skupinu s vyrovnáváním zatížení sady je vytvořena s "Port 80" a skupinu s vyrovnáváním zatížení obsahuje 3 virtuální počítače, vytvořit síť seznamu řízení přístupu na koncovém bodu "Port 80" jednoho virtuálního počítače se automaticky použije pro ostatní virtuální počítače.

![Seznamy ACL sítě a nastaví vyrovnáváním zatížení](./media/virtual-networks-acl/IC674733.png)

## <a name="next-steps"></a>Další kroky
[Správa seznamů řízení přístupu pro koncové body pomocí prostředí PowerShell](virtual-networks-acl-powershell.md)

