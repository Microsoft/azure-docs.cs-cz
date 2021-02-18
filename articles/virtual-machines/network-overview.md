---
title: Virtuální sítě
description: Přečtěte si o sítích v souvislosti s vytvářením virtuálních počítačů se systémem Linux v Azure.
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.subservice: networking
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 07/17/2017
ms.author: cynthn
ms.openlocfilehash: 20cc67d5c6436d7c0f44071509e13af324a88eea
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100578871"
---
# <a name="virtual-networks-and-virtual-machines-in-azure"></a>Virtuální sítě a virtuální počítače v Azure 

Když vytváříte virtuální počítač Azure, musíte vytvořit [virtuální síť](../virtual-network/virtual-networks-overview.md) (VNet), nebo použít existující VNet. Také musíte rozhodnout, jak budou vaše virtuální počítače v síti VNet dostupné. Je důležité [plánovat před vytvořením prostředků](../virtual-network/virtual-network-vnet-plan-design-arm.md) a dobře porozumět [omezením síťových prostředků](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits).

Na následujícím obrázku jsou virtuální počítače vyobrazené jako webové a databázové servery. Každá sada virtuálních počítačů je zařazena do oddělené podsítě VNet.

![Virtuální síť Azure](./media/virtual-machines-common-network-overview/vnetoverview.png)

Virtuální síť můžete vytvořit předtím, než vytvoříte virtuální počítač, nebo můžete vytvořit virtuální počítač. Pro podporu komunikace s virtuálním počítačem můžete vytvořit tyto prostředky:

- Síťová rozhraní
- IP adresy
- Virtuální sítě a podsítě

Vedle těchto základních prostředků byste měli zvážit také následující volitelné:

- Skupiny zabezpečení sítě
- Nástroje pro vyrovnávání zatížení 

## <a name="network-interfaces"></a>Síťová rozhraní

[Síťové rozhraní (nic)](../virtual-network/virtual-network-network-interface.md) je propojení mezi virtuálním počítačem a virtuální sítí (VNET). Virtuální počítač musí mít alespoň jedno síťové rozhraní, ale může jich mít víc, v závislosti na své velikosti. Další informace o tom, kolik síťových adaptérů podporuje jednotlivé velikosti virtuálních počítačů, najdete v tématu [velikosti virtuálních počítačů](sizes.md).

Můžete vytvořit virtuální počítač s více síťovými rozhraními a v průběhu životního cyklu virtuálního počítače síťová rozhraní přidávat nebo odebírat. Více síťových rozhraní umožňuje virtuálnímu počítači připojit se k různým podsítím a odesílat nebo přijímat provoz přes nejvhodnější rozhraní. Ve stejné skupině dostupnosti můžou existovat virtuální počítače s jakýmkoli počtem síťových rozhraní, a to až do maximálního počtu, který velikost virtuálního počítače podporuje. 

Každé síťové rozhraní přidružené k virtuálnímu počítači musí být ve stejném umístění a předplatném Azure jako samotný virtuální počítač. Každé síťové rozhraní musí být připojené k virtuální síti VNet, která je ve stejném umístění a předplatném Azure jako síťové rozhraní. Po vytvoření virtuálního počítače můžete změnit podsíť, ke které je připojený, ale nemůžete změnit virtuální síť. Každé síťové rozhraní připojené k virtuálnímu počítači má přiřazenou adresu MAC, která se nezmění, dokud se virtuální neodstraní.

Tato tabulka shrnuje metody, které můžete použít k vytvoření síťového rozhraní.

| Metoda | Popis |
| ------ | ----------- |
| portál Azure | Když vytvoříte virtuální počítač na webu Azure Portal, síťové rozhraní se vytvoří automaticky (nejde použít síťovou kartu vytvořenou samostatně). Portál vytvoří virtuální počítač s jedinou síťovou kartou. Pokud chcete vytvořit virtuální počítač s více než jedním síťovým rozhraním, musíte použít jinou metodu. |
| [Azure PowerShell](./windows/multiple-nics.md) | Pomocí [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) s parametrem **-PublicIpAddressId** zadejte identifikátor veřejné IP adresy, kterou jste vytvořili dříve. |
| [Azure CLI](./linux/multiple-nics.md) | Pokud chcete zadat identifikátor veřejné IP adresy, kterou jste vytvořili dřív, použijte příkaz [AZ Network nic Create](/cli/azure/network/nic) s parametrem **--Public-IP-Address** . |
| [Šablona](../virtual-network/template-samples.md) | Jako vodítko při nasazování síťového rozhraní pomocí šablony použijte článek věnovaný [síťovému rozhraní ve virtuální síti s veřejnou IP adresou](https://github.com/Azure/azure-quickstart-templates/tree/master/101-nic-publicip-dns-vnet). |

## <a name="ip-addresses"></a>IP adresy 

Síťovému rozhraní v Azure můžete přiřadit tyto typy [IP adres](../virtual-network/public-ip-addresses.md):

- **Veřejné IP adresy** – Slouží k příchozí a odchozí komunikaci (bez překladu adres (NAT)) s internetem a dalšími prostředky Azure, které nejsou připojené k virtuální síti. Přiřazení veřejné IP adresy síťovému rozhraní je volitelné. Veřejné IP adresy mají nominální poplatek a maximální počet, který je možné použít pro každé předplatné.
- **Privátní IP adresy** – Slouží ke komunikaci v rámci virtuální sítě, místní sítě a internetu (s NAT). Virtuálnímu počítači je nutné přiřadit alespoň jeden privátní IP adresu. Další informace o NAT v Azure najdete v článku [Principy odchozích připojení v Azure](../load-balancer/load-balancer-outbound-connections.md).

Veřejné IP adresy můžete přiřadit virtuálním počítačům nebo internetovým nástrojům pro vyrovnávání zatížení. Privátní IP adresy můžete přiřadit virtuálním počítačům a interním nástrojům pro vyrovnávání zatížení. K přiřazování IP adres virtuálním počítačům se používá síťové rozhraní.

Existují dvě metody pro přidělování IP adres prostředkům – dynamická a statická. Výchozí metoda přidělení je dynamická, při které IP adresa není přidělená v okamžiku svého vytvoření. Místo toho se IP adresa přidělí po vytvoření virtuálního počítače nebo spuštění zastaveného virtuálního počítače. Když tento virtuální počítač zastavíte nebo odstraníte, IP adresa se uvolní. 

Pokud chcete zajistit, aby IP adresa virtuálního počítače zůstala stejná, můžete explicitně nastavit statickou metodu přidělování. V takovém případě se IP adresa přiřadí okamžitě. Uvolní se, jenom když virtuální počítač odstraníte nebo změníte jeho metodu přidělování na dynamickou.
    
Tato tabulka shrnuje metody, které můžete použít k vytvoření IP adresy.

| Metoda | Popis |
| ------ | ----------- |
| [Azure Portal](../virtual-network/virtual-network-deploy-static-pip-arm-portal.md) | Veřejné IP adresy jsou ve výchozím nastavení dynamické a adresy, které jsou k nim přidružené, se mohou změnit, pokud se příslušný virtuální počítač zastaví nebo odstraní. Pokud chcete zajistit, aby virtuální počítač vždycky používal stejnou veřejnou IP adresu, vytvořte statickou veřejnou IP adresu. Ve výchozím nastavení portál při vytváření virtuálního počítače přiřadí síťovému rozhraní dynamickou privátní IP adresu. Po vytvoření virtuálního počítače můžete tuto IP adresu změnit na statickou.|
| [Azure PowerShell](../virtual-network/virtual-network-deploy-static-pip-arm-ps.md) | Použijte [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) s parametrem **-element allocationmethod** jako dynamickou nebo statickou. |
| [Azure CLI](../virtual-network/virtual-network-deploy-static-pip-arm-cli.md) | Použijte [az network public-ip create](/cli/azure/network/public-ip) s parametrem **--allocation-method** Dynamic nebo Static. |
| [Šablona](../virtual-network/template-samples.md) | Jako vodítko při nasazování veřejné IP adresy pomocí šablony použijte článek věnovaný [síťovému rozhraní ve virtuální síti s veřejnou IP adresou](https://github.com/Azure/azure-quickstart-templates/tree/master/101-nic-publicip-dns-vnet). |

Vytvořenou veřejnou IP adresu můžete přidružit k virtuálnímu počítači tak, že ji přiřadíte síťovému rozhraní.

## <a name="virtual-network-and-subnets"></a>Virtuální sítě a podsítě

Podsíť je rozsah IP adres ve virtuální síti. Virtuální síť můžete z organizačních a bezpečnostních důvodů rozdělit do několika podsítí. Každé síťové rozhraní ve virtuálním počítači je připojené k jedné podsíti v rámci jedné virtuální sítě. Síťová rozhraní připojená k podsítím (stejným nebo různým) v rámci jedné virtuální sítě můžou navzájem komunikovat bez jakékoli další konfigurace.

Při nastavování virtuální sítě se zadává topologii, včetně dostupných adresních prostorů a podsítí. Pokud virtuální síť má být připojená k dalším virtuálním nebo místním sítím, musíte vybrat rozsahy adres, které se nepřekrývají. IP adresy jsou privátní a nelze k nim přicházet z Internetu, což platilo pouze pro IP adresy, které nejsou směrovatelné jako 10.0.0.0/8, 172.16.0.0/12 nebo 192.168.0.0/16. V současnosti Azure považuje všechny rozsah adres za součást privátního IP adresního prostoru virtuální sítě, který je dostupný jenom v rámci příslušné virtuální sítě, vzájemně propojených virtuálních sítí a místního umístění. 

Pokud pracujete v organizaci, ve které je za interní sítě zodpovědný někdo jiný, měli byste se před výběrem adresního prostoru na tuto osobu obrátit. Zajistěte, aby se nepřekrývaly a aby věděly, které místo chcete použít, aby se nepokusily použít stejný rozsah IP adres. 

Ve výchozím nastavení mezi podsítěmi neexistuje žádná hranice zabezpečení, takže virtuální počítače v každé z těchto podsítí mohou vzájemně komunikovat. Můžete ale nastavit skupiny zabezpečení sítě (NSG), které umožňují řídit tok do a ze sítí a také do a z virtuálních počítačů. 

Tato tabulka shrnuje metody, které můžete použít k vytvoření virtuální sítě a podsítí.    

| Metoda | Popis |
| ------ | ----------- |
| [Azure Portal](../virtual-network/quick-create-portal.md) | Pokud při vytváření virtuálního počítače necháte Azure vytvořit virtuální síť, bude její název kombinací názvu skupiny prostředků, která tuto virtuální síť obsahuje, a řetězce **-vnet**. Adresní prostor je 10.0.0.0/24, název požadované podsítě je **default** a rozsah adres podsítě je 10.0.0.0/24. |
| [Azure PowerShell](../virtual-network/quick-create-powershell.md) | K vytvoření podsítě a virtuální sítě použijete [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworkSubnetConfig) a [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) . K přidání podsítě do existující virtuální sítě můžete také použít [příkaz Add-AzVirtualNetworkSubnetConfig](/powershell/module/Az.Network/Add-AzVirtualNetworkSubnetConfig) . |
| [Azure CLI](../virtual-network/quick-create-cli.md) | Podsíť a virtuální síť se vytvoří ve stejnou dobu. Pro [az network vnet create](/cli/azure/network/vnet) zadejte parametr **--subnet-name** s názvem podsítě. |
| Template (Šablona) | Nejjednodušší způsob, jak vytvořit virtuální síť a podsítě, je stáhnout existující šablonu, například [Virtual Network se dvěma podsítěmi](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vnet-two-subnets), a upravit ji podle svých potřeb. |

## <a name="network-security-groups"></a>Skupiny zabezpečení sítě

[Skupina zabezpečení sítě (NSG)](../virtual-network/virtual-network-vnet-plan-design-arm.md) obsahuje seznam pravidel seznamu řízení přístupu (ACL), která povolují nebo zamítají síťový provoz pro podsítě, síťová rozhraní nebo oboje. Skupiny NSG můžou být přidružené buď k podsítím, nebo k jednotlivým síťovým rozhraním připojeným k podsíti. Pokud je skupina zabezpečení sítě přidružená k podsíti, pravidla seznamu ACL platí pro všechny virtuální počítače v této podsíti. Provoz směřující do konkrétního síťového rozhraní se navíc dá omezit tím, že se přímo k tomuto síťovému rozhraní přidruží skupina NSG.

Skupiny NSG obsahují dvě sady pravidel: příchozí a odchozí. Priorita pravidla musí být v rámci jednotlivých sad jedinečná. Každé pravidlo má vlastnosti, které popisují protokol, zdrojový a cílový rozsah portů, předpony adres, směr přenosu, prioritu a typ přístupu. 

Všechny skupiny NSG obsahují sadu výchozích pravidel. Výchozí pravidla se nedají odstranit, ale protože je jim přiřazená nejnižší priorita, dají se přepsat pravidly, která vytvoříte. 

Pokud přidružíte skupinu NSG k síťové kartě, pravidla pro přístup k síti obsažená v této skupině se použijí jenom na tuto síťovou kartu. Pokud se skupina NSG použije pro jedno síťové rozhraní ve virtuálním počítači s několika síťovými rozhraními, neovlivní provoz směřující do ostatních síťových rozhraní. K síťovému rozhraní (nebo virtuálnímu počítači, podle modelu nasazení) a podsíti, ke které se síťové rozhraní nebo virtuální počítač váže, můžete přidružit různé skupiny NSG. Priorita se určuje na základě směru provozu.

Při plánování virtuálních počítačů a virtuální sítě nezapomeňte [naplánovat](../virtual-network/virtual-network-vnet-plan-design-arm.md) skupiny NSG.

Tato tabulka shrnuje metody, které můžete použít k vytvoření skupiny zabezpečení sítě.

| Metoda | Popis |
| ------ | ----------- |
| [Azure Portal](../virtual-network/tutorial-filter-network-traffic.md) | Když vytvoříte virtuální počítač na webu Azure Portal, automaticky se vytvoří skupina NSG a přidruží se k síťovému rozhraní, které tento portál vytvořil. Název této skupiny NSG je kombinací názvu virtuálního počítače a řetězce **-nsg**. Tato skupina NSG obsahuje jedno příchozí pravidlo s prioritou 1 000, nastavenou službou RDP, protokolem TCP, portem 3389 a akcí nastavenou na Povolit. Pokud chcete k tomuto virtuálnímu počítači povolit další příchozí provoz, musíte do této skupiny NSG přidat další pravidla. |
| [Azure PowerShell](../virtual-network/tutorial-filter-network-traffic.md) | Použijte [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) a poskytněte informace o požadovaném pravidle. Pomocí [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) vytvořte NSG. Pomocí [set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) nakonfigurujte NSG pro podsíť. K přidání NSG do virtuální sítě použijte [set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) . |
| [Azure CLI](../virtual-network/tutorial-filter-network-traffic-cli.md) | K úvodnímu vytvoření NSG použijte [az network nsg create](/cli/azure/network/nsg). Pro přidání pravidel k NSG použijte [az network nsg rule create](/cli/azure/network/nsg/rule). K přidání NSG do podsítě použijte [az network vnet subnet update](/cli/azure/network/vnet/subnet). |
| [Šablona](../virtual-network/template-samples.md) | Jako vodítko při nasazování skupiny zabezpečení sítě pomocí šablony použijte článek věnovaný [vytvoření skupiny zabezpečení sítě](https://github.com/Azure/azure-quickstart-templates/tree/master/101-security-group-create). |

## <a name="load-balancers"></a>Nástroje pro vyrovnávání zatížení

[Azure Load Balancer](../load-balancer/load-balancer-overview.md) zajišťuje vysokou dostupnost a výkon sítě pro vaše aplikace. Nástroj pro vyrovnávání zatížení je možné nakonfigurovat tak, aby [vyrovnával příchozí přenosy z Internetu](../load-balancer/components.md#frontend-ip-configurations) k virtuálním počítačům nebo aby [vyrovnával přenosy mezi virtuálními počítači ve virtuální síti](../load-balancer/components.md#frontend-ip-configurations). Nástroj pro vyrovnávání zatížení může také vyrovnávat přenosy mezi místními a virtuálními počítači na různých místech v síti nebo předávat externí přenosy konkrétnímu virtuálnímu počítači.

Nástroj pro vyrovnávání zatížení mapuje příchozí a odchozí provoz mezi svou veřejnou IP adresou a portem a privátní IP adresou a portem virtuálního počítače.

Když vytvoříte nástroj pro vyrovnávání zatížení, musíte taky zvážit tyto konfigurační prvky:

- **Konfigurace front-ENDOVÉ IP** adresy – Nástroj pro vyrovnávání zatížení může zahrnovat jednu nebo více front-endové IP adresy. Tyto IP adresy slouží jako vstup pro přenos.
- **Fond back-endových adres** – IP adresy, které jsou přiřazené síťovému rozhraní, na které se distribuuje zatížení.
- **[Přesměrování portů](../load-balancer/tutorial-load-balancer-port-forwarding-portal.md)** – definuje způsob, jakým příchozí přenos prochází přes front-end IP adresu a distribuuje se do back-endové IP adresy s využitím příchozích pravidel NAT.
- **Pravidla nástroje pro vyrovnávání zatížení** – Mapují konkrétní kombinaci front-endové IP adresy a portu na sadu kombinací back-endových IP adres a portů. Nástroj pro vyrovnávání zatížení může mít několik pravidel vyrovnávání zatížení. Každé pravidlo je kombinací front-endové IP adresy a portu a back-endové IP adresy a portu přidružených k virtuálním počítačům.
- **[Sondy](../load-balancer/load-balancer-custom-probe-overview.md)** – Monitorují stav virtuálních počítačů. Když sonda přestane reagovat, nástroj pro vyrovnávání zatížení zastaví odesílání nových připojení k virtuálnímu počítači, který není v pořádku. Stávající připojení to neovlivní a nová připojení jsou odesílaná virtuálním počítačům, které jsou v pořádku.
- **[Odchozí pravidla](../load-balancer/load-balancer-outbound-connections.md#outboundrules)** – odchozí pravidlo konfiguruje odchozí překlad síťových adres (NAT) pro všechny virtuální počítače nebo instance identifikované back-end fondem vašich Standard Load Balancer, které se mají přeložit do front-endu.

Tato tabulka shrnuje metody, které můžete použít k vytvoření internetového nástroje pro vyrovnávání zatížení.

| Metoda | Popis |
| ------ | ----------- |
| portál Azure |  [Pomocí Azure Portal můžete vyrovnávat zatížení internetového provozu na virtuálních počítačích](../load-balancer/quickstart-load-balancer-standard-public-portal.md). |
| [Azure PowerShell](../load-balancer/quickstart-load-balancer-standard-public-powershell.md) | Pokud chcete zadat identifikátor veřejné IP adresy, kterou jste vytvořili dřív, použijte [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig) s parametrem **-PublicIpAddress** . Pomocí [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig) vytvořte konfiguraci fondu back-end adres. Pomocí [New-AzLoadBalancerInboundNatRuleConfig](/powershell/module/az.network/new-azloadbalancerinboundnatruleconfig) vytvořte pravidla příchozího překladu adres (NAT) přidružená ke konfiguraci front-endové IP adresy, kterou jste vytvořili. Pomocí [New-AzLoadBalancerProbeConfig](/powershell/module/az.network/new-azloadbalancerprobeconfig) můžete vytvořit sondy, které potřebujete. Pomocí [New-AzLoadBalancerRuleConfig](/powershell/module/az.network/new-azloadbalancerruleconfig) vytvořte konfiguraci nástroje pro vyrovnávání zatížení. K vytvoření nástroje pro vyrovnávání zatížení použijte [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer) .|
| [Azure CLI](../load-balancer/quickstart-load-balancer-standard-public-cli.md) | Pro vytvoření úvodní konfigurace nástroje pro vyrovnávání zatížení použijte [az network lb create](/cli/azure/network/lb). Pro přidání veřejné IP adresy, kterou jste vytvořili dřív, použijte [az network lb frontend-ip create](/cli/azure/network/lb/frontend-ip). K přidání konfigurace fondu back-endových adres použijte [az network lb address-pool create](/cli/azure/network/lb/address-pool). K přidání pravidel NAT použijte [az network lb inbound-nat-rule create](/cli/azure/network/lb/inbound-nat-rule). K přidání pravidel nástroje pro vyrovnávání zatížení použijte [az network lb rule create](/cli/azure/network/lb/rule). K přidání sond použijte [az network lb probe create](/cli/azure/network/lb/probe). |
| [Šablona](../load-balancer/quickstart-load-balancer-standard-public-template.md) | Použití [3 virtuálních počítačů v Load Balancer](https://github.com/Azure/azure-quickstart-templates/tree/master/101-load-balancer-standard-create) jako Průvodce nasazením nástroje pro vyrovnávání zatížení pomocí šablony. |
    
Tato tabulka shrnuje metody, které můžete použít k vytvoření interního nástroje pro vyrovnávání zatížení.

| Metoda | Popis |
| ------ | ----------- |
| portál Azure | [Zatížení interního provozu můžete vyrovnávat pomocí nástroje pro vyrovnávání zatížení v Azure Portal](../load-balancer/quickstart-load-balancer-standard-internal-portal.md). |
| [Azure PowerShell](../load-balancer/quickstart-load-balancer-standard-internal-powershell.md) | Pokud chcete v podsíti sítě zadat privátní IP adresu, použijte příkaz [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig) s parametrem **-PrivateIpAddress** . Pomocí [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig) vytvořte konfiguraci fondu back-end adres. Pomocí [New-AzLoadBalancerInboundNatRuleConfig](/powershell/module/az.network/new-azloadbalancerinboundnatruleconfig) vytvořte pravidla příchozího překladu adres (NAT) přidružená ke konfiguraci front-endové IP adresy, kterou jste vytvořili. Pomocí [New-AzLoadBalancerProbeConfig](/powershell/module/az.network/new-azloadbalancerprobeconfig) můžete vytvořit sondy, které potřebujete. Pomocí [New-AzLoadBalancerRuleConfig](/powershell/module/az.network/new-azloadbalancerruleconfig) vytvořte konfiguraci nástroje pro vyrovnávání zatížení. K vytvoření nástroje pro vyrovnávání zatížení použijte [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer) .|
| [Azure CLI](../load-balancer/quickstart-load-balancer-standard-internal-cli.md) | Pro vytvoření úvodní konfigurace nástroje pro vyrovnávání zatížení použijte příkaz [az network lb create](/cli/azure/network/lb). K definování privátní IP adresy použijte [az network lb frontend-ip create](/cli/azure/network/lb/frontend-ip) s parametrem **--private-ip-address**. K přidání konfigurace fondu back-endových adres použijte [az network lb address-pool create](/cli/azure/network/lb/address-pool). K přidání pravidel NAT použijte [az network lb inbound-nat-rule create](/cli/azure/network/lb/inbound-nat-rule). K přidání pravidel nástroje pro vyrovnávání zatížení použijte [az network lb rule create](/cli/azure/network/lb/rule). K přidání sond použijte [az network lb probe create](/cli/azure/network/lb/probe).|
| [Šablona](../load-balancer/quickstart-load-balancer-standard-internal-template.md) | Jako vodítko pro nasazení nástroje pro vyrovnávání zatížení pomocí šablony použijte [2 virtuální počítače v Load Balancer](https://github.com/Azure/azure-quickstart-templates/tree/master/201-2-vms-internal-load-balancer) . |

### <a name="virtual-machine-scale-sets"></a>Škálovací sady virtuálních počítačů

Další informace o nástroji pro vyrovnávání zatížení a službě Virtual Machine Scale Sets najdete v tématu [sítě pro Azure Virtual Machine Scale Sets](../virtual-machine-scale-sets/virtual-machine-scale-sets-networking.md).

## <a name="vms"></a>Virtuální počítače

Virtuální počítače je možné vytvořit ve stejné virtuální síti. Ke vzájemnému propojení můžou využít privátní IP adresy. Propojit se můžou v případě, že jsou v různých podsítích, a není přitom potřeba konfigurovat bránu nebo využívat veřejné IP adresy. Pokud chcete umístit virtuální počítač do virtuální sítě, vytvořte virtuální síť a potom jednotlivé virtuální počítače při jejich vytváření přiřazujte do této virtuální sítě a požadované podsítě. Virtuální počítače získají svoje síťové nastavení sítě během nasazení nebo spuštění.  

IP adresa se virtuálním počítačům přiřazuje v okamžiku jejich nasazení. Pokud do virtuální sítě nebo podsítě nasadíte několik virtuálních počítačů, IP adresy se jim přiřazují při spouštění. K virtuálnímu počítači můžete také přidělit statickou IP adresu. Pokud přidělíte statickou IP adresu, měli byste zvážit použití konkrétní podsítě, abyste se vyhnuli nechtěnému opakovanému použití statické IP adresy pro jiný virtuální počítač.  

Pokud vytvoříte virtuální počítač a chcete ho později migrovat do virtuální sítě, nejde o jednoduchou změnu konfigurace. Virtuální počítač je v takovém případě potřeba do virtuální sítě znovu nasadit. Nejjednodušší způsob, jak znovu nasadit virtuální počítač, je odstranit tento počítač, ale ne disky, které jsou k němu připojené, a potom ho znovu vytvořit ve virtuální síti s využitím původních disků. 

Tato tabulka shrnuje metody, které můžete použít k vytvoření virtuálního počítač ve virtuální síti.

| Metoda | Popis |
| ------ | ----------- |
| [Azure Portal](./windows/quick-create-portal.md) | Využívá výchozí síťové nastavení, které bylo popsané dřív, k vytvoření virtuálního počítače s jedním síťovým rozhraním. K vytvoření virtuálního počítače s několika síťovými rozhraními musíte použít jinou metodu. |
| [Azure PowerShell](./windows/tutorial-manage-vm.md) | Zahrnuje použití [Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface) k přidání síťového rozhraní, které jste předtím vytvořili do konfigurace virtuálního počítače. |
| [Azure CLI](./linux/create-cli-complete.md) | Vytvořte virtuální počítač a připojte ho k virtuální síti, podsíti a síťovému adaptéru, který sestaví jako jednotlivé kroky. |
| [Šablona](./windows/ps-template.md) | Jako vodítko při nasazování virtuálního počítače pomocí šablony použijte článek věnovaný [velmi jednoduchému nasazení virtuálního počítače s Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows). |

## <a name="next-steps"></a>Další kroky
Kroky specifické pro virtuální počítače týkající se správy virtuálních sítí Azure pro virtuální počítače najdete v kurzech pro [Windows](../virtual-machines/windows/tutorial-virtual-network.md) nebo [Linux](../virtual-machines/linux/tutorial-virtual-network.md) .

K dispozici jsou také kurzy pro vyrovnávání zatížení virtuálních počítačů a vytváření vysoce dostupných aplikací pro [Windows](../virtual-machines/windows/tutorial-load-balancer.md) nebo [Linux](../virtual-machines/linux/tutorial-load-balancer.md).

- Naučte se konfigurovat [trasy definované uživatelem a předávání IP](../virtual-network/virtual-networks-udr-overview.md). 
- Naučte se konfigurovat [připojení mezi virtuálními sítěmi](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md).
- Naučte se [řešit potíže s trasami](../virtual-network/diagnose-network-routing-problem.md).
- Přečtěte si další informace o [šířce pásma sítě virtuálních počítačů](../virtual-network/virtual-machine-network-throughput.md).