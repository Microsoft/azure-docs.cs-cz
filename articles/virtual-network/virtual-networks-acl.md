---
title: Co je seznam řízení přístupu k síti Azure?
description: Další informace o seznamech řízení přístupu v Azure
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
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
ms.openlocfilehash: 5e410dc4e7f47223430497825dc2a26e41c6af67
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/15/2020
ms.locfileid: "75979558"
---
# <a name="what-is-an-endpoint-access-control-list"></a>Co je seznam řízení přístupu ke koncovému bodu?

> [!IMPORTANT]
> Azure má dva různé [modely nasazení](../azure-resource-manager/management/deployment-models.md?toc=%2fazure%2fvirtual-network%2ftoc.json) pro vytváření prostředků a práci s nimi: Správce prostředků a Classic. Tento článek se věnuje použití klasického modelu nasazení. Microsoft doporučuje, aby většina nových nasazení používala model nasazení Správce prostředků. 

Seznam řízení přístupu (ACL) koncového bodu je rozšířením zabezpečení, které je dostupné pro vaše nasazení Azure. Seznam ACL poskytuje možnost selektivního povolení nebo odepření provozu pro koncový bod virtuálního počítače. Tato schopnost filtrování paketů poskytuje další úroveň zabezpečení. Můžete zadat seznam ACL sítě jenom pro koncové body. Nemůžete zadat seznam ACL pro virtuální síť nebo konkrétní podsíť obsaženou ve virtuální síti. Doporučuje se používat skupiny zabezpečení sítě (skupin zabezpečení sítě) místo seznamů ACL, kdykoli je to možné. Při použití skupin zabezpečení sítě se seznam řízení přístupu pro koncové body nahradí a už se neuplatní. Další informace o skupin zabezpečení sítě najdete v tématu [Přehled skupiny zabezpečení sítě](security-overview.md) .

Seznamy řízení přístupu (ACL) se dají nakonfigurovat pomocí PowerShellu nebo Azure Portal. Pokud chcete nakonfigurovat seznam ACL sítě pomocí PowerShellu, přečtěte si téma [Správa seznamů řízení přístupu pro koncové body pomocí PowerShellu](virtual-networks-acl-powershell.md). Pokud chcete nakonfigurovat seznam ACL sítě pomocí Azure Portal, přečtěte si téma [nastavení koncových bodů na virtuální počítač](../virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

Pomocí seznamů ACL pro síť můžete provádět následující akce:

* Selektivně povolte nebo zakažte příchozí provoz založený na rozsahu IPv4 adres vzdálené podsítě na vstupní koncový bod virtuálního počítače.
* Zakázané IP adresy
* Vytvoření více pravidel na koncový bod virtuálního počítače
* Pořadí pravidel použijte k zajištění správné sady pravidel pro daný koncový bod virtuálního počítače (od nejnižší po nejvyšší).
* Zadejte seznam ACL pro konkrétní IPv4 adresu vzdálené podsítě.

Omezení seznamu ACL najdete v článku věnovaném [omezením Azure](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits) .

## <a name="how-acls-work"></a>Jak fungují seznamy řízení přístupu
Seznam ACL je objekt, který obsahuje seznam pravidel. Když vytvoříte seznam ACL a použijete ho pro koncový bod virtuálního počítače, bude filtrování paketů provedeno na uzlu hostitele vašeho virtuálního počítače. To znamená, že přenos ze vzdálených IP adres je filtrován podle uzlu hostitele pro porovnání pravidel seznamu ACL, nikoli na vašem VIRTUÁLNÍm počítači. Tím zabráníte tomu, aby váš virtuální počítač strávil na filtrování paketů drahé cykly procesoru.

Když se vytvoří virtuální počítač, zavede se výchozí seznam ACL pro blokování všech příchozích přenosů. Pokud se ale pro (port 3389) vytvoří koncový bod, pak se upraví výchozí seznam ACL, aby se povolil veškerý příchozí provoz tohoto koncového bodu. Příchozí provoz ze všech vzdálených podsítí je pak povolen pro daný koncový bod a není nutné žádné zřizování brány firewall. Všechny ostatní porty jsou blokované pro příchozí provoz, pokud se pro tyto porty nevytvoří koncové body. Odchozí přenosy jsou ve výchozím nastavení povolené.

**Příklad výchozí tabulky ACL**

| **Pravidlo #** | **Vzdálená podsíť** | **Koncový bod** | **Povolit/odepřít** |
| --- | --- | --- | --- |
| 100 |0.0.0.0/0 |3389 |Uvádění |

## <a name="permit-and-deny"></a>Povolit a odepřít
Můžete selektivně povolit nebo zamítnout síťový provoz pro vstupní koncový bod virtuálního počítače vytvořením pravidel, která určují možnost "Povolit" nebo "Odepřít". Při vytvoření koncového bodu je důležité poznamenat, že ve výchozím nastavení je pro koncový bod povolený veškerý provoz. Z tohoto důvodu je důležité pochopit, jak vytvořit pravidla povolení nebo odepření a umístit je do správného pořadí priorit, pokud chcete detailní kontrolu nad síťovými přenosy, které jste zvolili, aby bylo možné dosáhnout tohoto koncového bodu virtuálního počítače.

Body, které je potřeba vzít v úvahu:

1. **Žádný seznam ACL –** Ve výchozím nastavení se při vytvoření koncového bodu povoluje vše pro koncový bod.
2. **Povolit –** Když přidáte jeden nebo více rozsahů "Povolit", ve výchozím nastavení odepřete všechny ostatní rozsahy. Pouze pakety z povoleného rozsahu IP adres budou moci komunikovat s koncovým bodem virtuálního počítače.
3. **Odepřít –** Když přidáte jeden nebo víc rozsahů "Odepřít", ve výchozím nastavení povolíte všechny ostatní rozsahy provozu.
4. **Kombinace povolení a zamítnutí –** Pokud chcete vyčlenit konkrétní rozsah IP adres, který se má povolit nebo odepřít, můžete použít kombinaci "Permit" a "deny".

## <a name="rules-and-rule-precedence"></a>Pravidla a Priorita pravidla
V konkrétních koncových bodech virtuálních počítačů je možné nastavit seznamy ACL sítě. Můžete například zadat seznam ACL sítě pro koncový bod RDP vytvořený na virtuálním počítači, který zamkne přístup pro určité IP adresy. Následující tabulka ukazuje způsob, jak udělit přístup k veřejným virtuálním IP adresám (VIP) pro určitý rozsah, aby bylo možné povolit přístup pro protokol RDP. Všechny ostatní vzdálené IP adresy jsou odepřeny. Sledujeme pořadí pravidel podle *priority* .

### <a name="multiple-rules"></a>Více pravidel
Pokud chcete povolit přístup ke koncovému bodu RDP jenom ze dvou rozsahů veřejných IPv4 adres (65.0.0.0/8 a 159.0.0.0/8), můžete to dosáhnout zadáním dvou pravidel *Povolení* . V takovém případě, protože protokol RDP je ve výchozím nastavení vytvořen pro virtuální počítač, může být vhodné uzamknout přístup k portu RDP na základě vzdálené podsítě. Následující příklad ukazuje způsob, jak udělit přístup k veřejným virtuálním IP adresám (VIP) pro určitý rozsah, aby bylo možné povolit přístup pro protokol RDP. Všechny ostatní vzdálené IP adresy jsou odepřeny. To funguje, protože seznamy ACL sítě je možné nastavit pro konkrétní koncový bod virtuálního počítače a ve výchozím nastavení je přístup odepřen.

**Příklad – více pravidel**

| **Pravidlo #** | **Vzdálená podsíť** | **Koncový bod** | **Povolit/odepřít** |
| --- | --- | --- | --- |
| 100 |65.0.0.0/8 |3389 |Uvádění |
| 200 |159.0.0.0/8 |3389 |Uvádění |

### <a name="rule-order"></a>Pořadí pravidel
Vzhledem k tomu, že pro koncový bod lze zadat více pravidel, musí existovat způsob, jak uspořádat pravidla, aby bylo možné určit, které pravidlo má přednost. Pořadí pravidel určuje prioritu. Seznam ACL pro sítě dodržuje pořadí pravidel s *prioritou nejnižší* . V následujícím příkladu má koncový bod na portu 80 selektivně udělený přístup pouze k určitým rozsahům IP adres. Pro konfiguraci tohoto nastavení máme pravidlo Odepřít (pravidlo \# 100) pro adresy v 175.1.0.1/24 prostoru. Pak je zadané druhé pravidlo s prioritou 200, které umožňuje přístup ke všem ostatním adresám v 175.0.0.0/8.

**Příklad – priorita pravidla**

| **Pravidlo #** | **Vzdálená podsíť** | **Koncový bod** | **Povolit/odepřít** |
| --- | --- | --- | --- |
| 100 |175.1.0.1/24 |80 |Zamítnout |
| 200 |175.0.0.0/8 |80 |Uvádění |

## <a name="network-acls-and-load-balanced-sets"></a>Seznamy ACL sítě a sady s vyrovnáváním zatížení
U koncového bodu sady s vyrovnáváním zatížení je možné zadat seznamy ACL pro síť. Pokud je pro skupinu s vyrovnáváním zatížení zadaný seznam řízení přístupu (ACL), použije se seznam ACL sítě pro všechny virtuální počítače v této skupině s vyrovnáváním zatížení. Pokud je například vytvořena sada s vyrovnáváním zatížení s názvem "port 80" a skupina s vyrovnáváním zatížení obsahuje 3 virtuální počítače, bude seznam ACL sítě vytvořený na koncovém bodu "port 80" v jednom virtuálním počítači automaticky platit pro ostatní virtuální počítače.

![Seznamy ACL sítě a sady s vyrovnáváním zatížení](./media/virtual-networks-acl/IC674733.png)

## <a name="next-steps"></a>Další kroky
[Správa seznamů řízení přístupu pro koncové body pomocí prostředí PowerShell](virtual-networks-acl-powershell.md)

