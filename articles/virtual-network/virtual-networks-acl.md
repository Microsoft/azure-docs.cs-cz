---
title: Co je seznam řízení přístupu síti Azure?
description: Další informace o seznamy řízení přístupu v Azure
services: virtual-network
documentationcenter: na
author: genlin
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
ms.openlocfilehash: 3a7155380a51273d376226c6be7a004f386181ce
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "61035247"
---
# <a name="what-is-an-endpoint-access-control-list"></a>Co je seznam řízení přístupu koncový bod?

> [!IMPORTANT]
> Azure má dva různé [modely nasazení](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json) pro vytváření a práci s prostředky: Resource Manager a classic. Tento článek se věnuje použití klasického modelu nasazení. Společnost Microsoft doporučuje, aby většina nových nasazení používala model nasazení Resource Manager. 

Seznam řízení přístupu (ACL) koncového bodu je rozšířením zabezpečení, která je k dispozici pro nasazení vašeho řešení Azure. Seznam ACL umožňuje selektivně povolovat nebo zamítat provoz pro koncový bod virtuálního počítače. Tato funkce filtrování paketů poskytuje další vrstvu zabezpečení. Můžete určit síť seznamy ACL pro koncové body pouze. Nelze zadat seznamu ACL portu pro virtuální síť nebo podsíť konkrétní obsažené ve virtuální síti. Doporučujeme použít skupiny zabezpečení sítě (Nsg) namísto seznamy ACL, kdykoli je to možné. Při použití skupin zabezpečení sítě, seznam řízení přístupu koncový bod bude nahrazen a už nebudou vynucené. Další informace o skupinách Nsg najdete v tématu [přehled skupin zabezpečení sítě](security-overview.md)

Seznamy ACL můžete konfigurovat pomocí Powershellu nebo na webu Azure portal. Konfigurace seznamu ACL sítě pomocí prostředí PowerShell, najdete v článku [seznamy Správa řízení přístupu pro koncové body pomocí prostředí PowerShell](virtual-networks-acl-powershell.md). Ke konfiguraci seznamu ACL k síti pomocí webu Azure portal, najdete v článku [postup nastavení koncových bodů pro virtuální počítač](../virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

Seznamy ACL sítě, můžete provést pomocí následujících akcí:

* Výběrově povolit nebo zakázat příchozí provoz na základě vzdálené podsítě rozsah IPv4 adres do vstupního koncového bodu virtuálního počítače.
* Zakázaných IP adres
* Vytvoření více pravidel na koncový bod virtuálního počítače
* Použití pravidla řazení zajistit správnou sadu pravidel, která se použijí pro koncový bod daným virtuálním počítačem (nejnižší a nejvyšší)
* Zadejte seznam ACL pro konkrétní vzdálené podsítě IPv4 adresu.

Zobrazit [omezeních Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits) článku pro omezení seznamu ACL.

## <a name="how-acls-work"></a>Fungování seznamů řízení přístupu
Seznamu ACL portu je objekt, který obsahuje seznam pravidel. Při vytváření seznamu ACL portu a použijte ji pro koncový bod virtuálního počítače, paket filtrování probíhá na uzlu hostitele virtuálního počítače. To znamená, že provoz z vzdálené IP adresy se filtrují podle adresa uzlu hostitele pro odpovídající pravidla seznamu ACL místo na vašem virtuálním počítači. To zabrání tomu, aby váš virtuální počítač výdaje cenný cyklů procesoru na filtrování paketů.

Když je virtuální počítač, je výchozí seznam ACL umístit do místem pro všechny příchozí přenosy blokovat. Nicméně pokud koncový bod vytvořen pro (port 3389), pak výchozí seznam ACL je upravit tak, aby umožňují veškerý příchozí provoz pro tento koncový bod. Do tohoto koncového bodu je pak povolené příchozí provoz z žádné vzdálené podsítě a není třeba zřizovat brány firewall. Všechny ostatní porty jsou blokovány pro příchozí provoz, pokud jsou koncové body vytvořené pro tyto porty. Ve výchozím nastavení je povolen odchozí provoz.

**Příklad tabulky výchozího seznamu ACL**

| **Pravidlo #** | **Vzdálené podsítě** | **Koncový bod** | **Povolení/zákazu** |
| --- | --- | --- | --- |
| 100 |0.0.0.0/0 |3389 |Povolení |

## <a name="permit-and-deny"></a>Povolit a odepřít
Selektivně povolovat nebo odpírají síťový provoz pro vstupní koncový bod virtuálního počítače tak, že vytvoříte pravidla, která určují "povoleno" nebo "Zakázat". Je důležité si uvědomit, že ve výchozím nastavení, když se koncový bod, jsou povoleny všechny přenosy do koncového bodu. Z tohoto důvodu je důležité pochopit, jak vytvořit pravidla povolení/zákazu a umístit je do správné pořadí podle priority, pokud chcete, aby detailní kontroly nad síťovými přenosy, které můžete zvolit, aby k dosažení koncový bod virtuálního počítače.

Body ke zvážení:

1. **Žádný seznam ACL –** ve výchozím nastavení když se koncový bod, můžeme povolit všechny pro koncový bod.
2. **Povolit -** při přidání jedné nebo více "povoleno" rozsahy jsou odepření jiné rozsahy ve výchozím nastavení. Pouze pakety z povolených rozsah IP adres bude komunikovat s koncový bod virtuálního počítače.
3. **Odepřít -** při přidání jedné nebo více "Zakázat" rozsahy jsou umožňující všechny rozsahy provozu ve výchozím nastavení.
4. **Povolit a Odepřít -** můžete použít kombinaci "povoleno" a "Zakázat", pokud chcete vyčlenit konkrétní rozsah IP adres povolen nebo odepřen přístup.

## <a name="rules-and-rule-precedence"></a>Priorita pravidla a pravidla
Seznamy ACL sítě můžete nastavit na konkrétní virtuální počítač koncových bodů. Například můžete určit síť seznamu ACL pro koncový bod protokolu RDP vytvořit na virtuálním počítači, který zámky dolů přístup pro určité IP adresy. Následující tabulka ukazuje způsob, jak udělit přístup k veřejné virtuální IP adresy (VIP) určité oblasti tak, aby povolovala přístup pro protokol RDP. Jiné vzdálené IP je odepřen. Budeme postupovat podle *nejnižší má přednost před* pravidlo pořadí.

### <a name="multiple-rules"></a>Více pravidel
V následujícím příkladu, pokud chcete povolit přístup ke koncovému bodu protokolu RDP pouze z dva veřejné rozsahy IPv4 adres (65.0.0.0/8 a 159.0.0.0/8), lze dosáhnout zadáním dvou *povolit* pravidla. V takovém případě od protokolu RDP se vytvoří ve výchozím nastavení pro virtuální počítač, můžete zamezit přístup k portu RDP na základě vzdálené podsítě. Následující příklad ukazuje způsob, jak udělit přístup k veřejné virtuální IP adresy (VIP) určité oblasti tak, aby povolovala přístup pro protokol RDP. Jiné vzdálené IP je odepřen. Tento postup funguje, protože seznamy ACL sítě lze nastavit pro koncový bod konkrétního virtuálního počítače a ve výchozím nastavení je odepřen přístup.

**Příklad: více pravidel**

| **Pravidlo #** | **Vzdálené podsítě** | **Koncový bod** | **Povolení/zákazu** |
| --- | --- | --- | --- |
| 100 |65.0.0.0/8 |3389 |Povolení |
| 200 |159.0.0.0/8 |3389 |Povolení |

### <a name="rule-order"></a>Pořadí pravidel
Protože více pravidel se dá nastavit pro koncový bod, musí existovat způsob, jak uspořádat pravidla, aby bylo možné určit pravidlo, které má přednost. Pořadí pravidel určuje prioritu. Následující seznamy ACL sítě *nejnižší má přednost před* pravidlo pořadí. V následujícím příkladu je koncový bod na portu 80 selektivně udělen přístup k pouze určité rozsahy IP adres. Pokud chcete nastavit tuto konfiguraci, máme pravidlo odepření (pravidlo \# 100) pro adresy v prostoru 175.1.0.1/24. Druhé pravidlo je poté zadané s prioritou 200, který povoluje přístup ke všem adresám pod 175.0.0.0/8.

**Příklad – prioritu pravidla**

| **Pravidlo #** | **Vzdálené podsítě** | **Koncový bod** | **Povolení/zákazu** |
| --- | --- | --- | --- |
| 100 |175.1.0.1/24 |80 |Odepřít |
| 200 |175.0.0.0/8 |80 |Povolení |

## <a name="network-acls-and-load-balanced-sets"></a>Seznamy ACL sítě a sady s vyrovnáváním zatížení
Seznamy ACL sítě se dá nastavit na koncový bod s vyrovnáváním zatížení sadě. Pokud je seznam ACL zadaný pro sadu s vyrovnáváním zatížení, sítě seznamu ACL platí pro všechny virtuální počítače v dané sadě s vyrovnáváním zatížení. Například pokud skupinu s vyrovnáváním zatížení sada se vytvoří s "Port 80" a sada s vyrovnáváním zatížení obsahuje 3 virtuální počítače, vytvořit síť seznamu ACL na koncovém bodu "Port 80" jednoho virtuálního počítače automaticky platit pro ostatní virtuální počítače.

![Seznamy ACL sítě a sady s vyrovnáváním zatížení](./media/virtual-networks-acl/IC674733.png)

## <a name="next-steps"></a>Další kroky
[Spravovat seznamy řízení přístupu pro koncové body pomocí prostředí PowerShell](virtual-networks-acl-powershell.md)

