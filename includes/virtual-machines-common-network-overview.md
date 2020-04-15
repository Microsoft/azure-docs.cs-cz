---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines-windows
author: cynthn
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 11/01/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: b5d46caa80f3f0aaeeb18bd919dafccf628c5faf
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81384951"
---
Když vytváříte virtuální počítač Azure, musíte vytvořit [virtuální síť](../articles/virtual-network/virtual-networks-overview.md) (VNet), nebo použít existující VNet. Také musíte rozhodnout, jak budou vaše virtuální počítače v síti VNet dostupné. Je důležité [plánovat před vytvořením prostředků](../articles/virtual-network/virtual-network-vnet-plan-design-arm.md) a dobře porozumět [omezením síťových prostředků](../articles/azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits).

Na následujícím obrázku jsou virtuální počítače vyobrazené jako webové a databázové servery. Každá sada virtuálních počítačů je zařazena do oddělené podsítě VNet.

![Virtuální síť Azure](./media/virtual-machines-common-network-overview/vnetoverview.png)

Virtuální síť můžete vytvořit před vytvořením virtuálního virtuálního montu nebo můžete vytvořit virtuální ho. Pro podporu komunikace s virtuálním počítačem můžete vytvořit tyto prostředky:

- Síťová rozhraní
- IP adresy
- Virtuální sítě a podsítě

Vedle těchto základních prostředků byste měli zvážit také následující volitelné:

- Skupiny zabezpečení sítě
- Nástroje pro vyrovnávání zatížení 

## <a name="network-interfaces"></a>Síťová rozhraní

[Síťové rozhraní (NIC)](../articles/virtual-network/virtual-network-network-interface.md) je propojení mezi virtuálním počítačem a virtuální sítí (VNet). Virtuální počítač musí mít alespoň jedno síťové rozhraní, ale může jich mít víc, v závislosti na své velikosti. Přečtěte si, kolik nic podporuje každá velikost virtuálního počítače pro [Windows](../articles/virtual-machines/windows/sizes.md) nebo [Linux](../articles/virtual-machines/linux/sizes.md).

Můžete vytvořit virtuální počítač s více síťové karty a přidat nebo odebrat síťové karty prostřednictvím životního cyklu virtuálního počítače. Více síťových rozhraní umožňuje virtuálnímu počítači připojit se k různým podsítím a odesílat nebo přijímat přenosy přes nejvhodnější rozhraní. Virtuální počítače s libovolným počtem síťových rozhraní může existovat ve stejné sadě dostupnosti, až do čísla podporované velikosti virtuálního počítače. 

Každé síťové rozhraní přidružené k virtuálnímu počítači musí být ve stejném umístění a předplatném Azure jako samotný virtuální počítač. Každé síťové rozhraní musí být připojené k virtuální síti VNet, která je ve stejném umístění a předplatném Azure jako síťové rozhraní. Můžete změnit podsíť virtuálního zařízení je připojen po jeho vytvoření, ale nemůžete změnit virtuální síť. Každé nic připojené k virtuálnímu počítači je přiřazena mac adresa, která se nezmění, dokud se virtuální počítač odstraní.

Tato tabulka shrnuje metody, které můžete použít k vytvoření síťového rozhraní.

| Metoda | Popis |
| ------ | ----------- |
| portál Azure | Když vytvoříte virtuální počítač na webu Azure Portal, síťové rozhraní se vytvoří automaticky (nejde použít síťovou kartu vytvořenou samostatně). Portál vytvoří virtuální počítač s jedinou síťovou kartou. Pokud chcete vytvořit virtuální počítač s více než jedním síťovým rozhraním, musíte použít jinou metodu. |
| [Azure PowerShell](../articles/virtual-machines/windows/multiple-nics.md) | Pomocí [rozhraní New-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/new-aznetworkinterface) s parametrem **-PublicIpAddressId** zadejte identifikátor veřejné IP adresy, kterou jste dříve vytvořili. |
| [Azure CLI](../articles/virtual-machines/linux/multiple-nics.md) | Chcete-li zadat identifikátor veřejné IP adresy, kterou jste dříve vytvořili, použijte [az network nic create](https://docs.microsoft.com/cli/azure/network/nic) s parametrem **--public-ip-address.** |
| [Šablona](../articles/virtual-network/template-samples.md) | Jako vodítko při nasazování síťového rozhraní pomocí šablony použijte článek věnovaný [síťovému rozhraní ve virtuální síti s veřejnou IP adresou](https://github.com/Azure/azure-quickstart-templates/tree/master/101-nic-publicip-dns-vnet). |

## <a name="ip-addresses"></a>IP adresy 

Síťovému rozhraní v Azure můžete přiřadit tyto typy [IP adres](../articles/virtual-network/virtual-network-ip-addresses-overview-arm.md):

- **Veřejné IP adresy** – Slouží k příchozí a odchozí komunikaci (bez překladu adres (NAT)) s internetem a dalšími prostředky Azure, které nejsou připojené k virtuální síti. Přiřazení veřejné IP adresy síťovému rozhraní je volitelné. Veřejné IP adresy mají nominální poplatek a je zde maximální počet, který lze použít na jedno předplatné.
- **Privátní IP adresy** – Slouží ke komunikaci v rámci virtuální sítě, místní sítě a internetu (s NAT). Virtuálnímu počítači je nutné přiřadit alespoň jeden privátní IP adresu. Další informace o NAT v Azure najdete v článku [Principy odchozích připojení v Azure](../articles/load-balancer/load-balancer-outbound-connections.md).

Veřejné IP adresy můžete přiřadit virtuálním počítačům nebo internetovým nástrojům pro vyrovnávání zatížení. Privátní IP adresy můžete přiřadit virtuálním počítačům a interním nástrojům pro vyrovnávání zatížení. K přiřazování IP adres virtuálním počítačům se používá síťové rozhraní.

Existují dvě metody pro přidělování IP adres prostředkům – dynamická a statická. Výchozí metoda přidělení je dynamická, při které IP adresa není přidělená v okamžiku svého vytvoření. Místo toho se IP adresa přidělí po vytvoření virtuálního počítače nebo spuštění zastaveného virtuálního počítače. Když tento virtuální počítač zastavíte nebo odstraníte, IP adresa se uvolní. 

Pokud chcete zajistit, aby IP adresa virtuálního počítače zůstala stejná, můžete explicitně nastavit statickou metodu přidělování. V tomto případě se IP adresa přiřadí okamžitě. Uvolní se, jenom když virtuální počítač odstraníte nebo změníte jeho metodu přidělování na dynamickou.
    
Tato tabulka shrnuje metody, které můžete použít k vytvoření IP adresy.

| Metoda | Popis |
| ------ | ----------- |
| [portál Azure](../articles/virtual-network/virtual-network-deploy-static-pip-arm-portal.md) | Veřejné IP adresy jsou ve výchozím nastavení dynamické a adresy, které jsou k nim přidružené, se mohou změnit, pokud se příslušný virtuální počítač zastaví nebo odstraní. Pokud chcete zajistit, aby virtuální počítač vždycky používal stejnou veřejnou IP adresu, vytvořte statickou veřejnou IP adresu. Ve výchozím nastavení portál při vytváření virtuálního počítače přiřadí síťovému rozhraní dynamickou privátní IP adresu. Po vytvoření virtuálního počítačů můžete změnit tuto adresu IP na statickou.|
| [Azure PowerShell](../articles/virtual-network/virtual-network-deploy-static-pip-arm-ps.md) | [New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress) s parametrem **-AllocationMethod** se používá jako dynamický nebo statický. |
| [Azure CLI](../articles/virtual-network/virtual-network-deploy-static-pip-arm-cli.md) | Použijte [az network public-ip create](https://docs.microsoft.com/cli/azure/network/public-ip) s parametrem **--allocation-method** Dynamic nebo Static. |
| [Šablona](../articles/virtual-network/template-samples.md) | Jako vodítko při nasazování veřejné IP adresy pomocí šablony použijte článek věnovaný [síťovému rozhraní ve virtuální síti s veřejnou IP adresou](https://github.com/Azure/azure-quickstart-templates/tree/master/101-nic-publicip-dns-vnet). |

Vytvořenou veřejnou IP adresu můžete přidružit k virtuálnímu počítači tak, že ji přiřadíte síťovému rozhraní.

## <a name="virtual-network-and-subnets"></a>Virtuální sítě a podsítě

Podsíť je rozsah IP adres ve virtuální síti. Virtuální síť můžete z organizačních a bezpečnostních důvodů rozdělit do několika podsítí. Každé síťové rozhraní ve virtuálním počítači je připojené k jedné podsíti v rámci jedné virtuální sítě. Síťová rozhraní připojená k podsítím (stejným nebo různým) v rámci jedné virtuální sítě můžou navzájem komunikovat bez jakékoli další konfigurace.

Při nastavování virtuální sítě se zadává topologii, včetně dostupných adresních prostorů a podsítí. Pokud virtuální síť má být připojená k dalším virtuálním nebo místním sítím, musíte vybrat rozsahy adres, které se nepřekrývají. IP adresy jsou soukromé a nelze k nim přistupovat z Internetu, což platilo pouze pro nesměrovatelné IP adresy, jako je 10.0.0.0/8, 172.16.0/12 nebo 192.168.0.0/16. V současnosti Azure považuje všechny rozsah adres za součást privátního IP adresního prostoru virtuální sítě, který je dostupný jenom v rámci příslušné virtuální sítě, vzájemně propojených virtuálních sítí a místního umístění. 

Pokud pracujete v organizaci, ve které je za interní sítě zodpovědný někdo jiný, měli byste se před výběrem adresního prostoru na tuto osobu obrátit. Ujistěte se, že se nepřekrývají, a dejte jim vědět, že prostor, který chcete použít, aby se nepokoušeli používat stejný rozsah IP adres. 

Ve výchozím nastavení mezi podsítěmi neexistuje žádná hranice zabezpečení, takže virtuální počítače v každé z těchto podsítí mohou vzájemně komunikovat. Můžete ale nastavit skupiny zabezpečení sítě (NSG), které umožňují řídit tok do a ze sítí a také do a z virtuálních počítačů. 

Tato tabulka shrnuje metody, které můžete použít k vytvoření virtuální sítě a podsítí.    

| Metoda | Popis |
| ------ | ----------- |
| [portál Azure](../articles/virtual-network/quick-create-portal.md) | Pokud při vytváření virtuálního počítače necháte Azure vytvořit virtuální síť, bude její název kombinací názvu skupiny prostředků, která tuto virtuální síť obsahuje, a řetězce **-vnet**. Adresní prostor je 10.0.0.0/24, název požadované podsítě je **default** a rozsah adres podsítě je 10.0.0.0/24. |
| [Azure PowerShell](../articles/virtual-network/quick-create-powershell.md) | Pomocí [new-azvirtualnetworksubnetconfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworkSubnetConfig) a [new-azvirtualnetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork) k vytvoření podsítě a virtuální sítě. Můžete také použít [Add-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/Az.Network/Add-AzVirtualNetworkSubnetConfig) přidat podsíť do existující virtuální sítě. |
| [Azure CLI](../articles/virtual-network/quick-create-cli.md) | Podsíť a virtuální síť se vytvoří ve stejnou dobu. Pro [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet) zadejte parametr **--subnet-name** s názvem podsítě. |
| Šablona | Nejjednodušší způsob, jak vytvořit virtuální síť a podsítě, je stáhnout existující šablonu, například [virtuální síť se dvěma podsítěmi](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vnet-two-subnets), a upravit ji podle svých potřeb. |

## <a name="network-security-groups"></a>Skupiny zabezpečení sítě

[Skupina zabezpečení sítě (NSG)](../articles/virtual-network/virtual-network-vnet-plan-design-arm.md) obsahuje seznam pravidel seznamu řízení přístupu (ACL), která povolují nebo zamítají síťový provoz pro podsítě, síťová rozhraní nebo oboje. Skupiny NSG můžou být přidružené buď k podsítím, nebo k jednotlivým síťovým rozhraním připojeným k podsíti. Pokud je skupina zabezpečení sítě přidružená k podsíti, pravidla seznamu ACL platí pro všechny virtuální počítače v této podsíti. Provoz směřující do konkrétního síťového rozhraní se navíc dá omezit tím, že se přímo k tomuto síťovému rozhraní přidruží skupina NSG.

Skupiny NSG obsahují dvě sady pravidel: příchozí a odchozí. Priorita pravidla musí být v rámci jednotlivých sad jedinečná. Každé pravidlo má vlastnosti, které popisují protokol, zdrojový a cílový rozsah portů, předpony adres, směr přenosu, prioritu a typ přístupu. 

Všechny skupiny NSG obsahují sadu výchozích pravidel. Výchozí pravidla se nedají odstranit, ale protože je jim přiřazená nejnižší priorita, dají se přepsat pravidly, která vytvoříte. 

Pokud přidružíte skupinu NSG k síťové kartě, pravidla pro přístup k síti obsažená v této skupině se použijí jenom na tuto síťovou kartu. Pokud se skupina NSG použije pro jedno síťové rozhraní ve virtuálním počítači s několika síťovými rozhraními, neovlivní provoz směřující do ostatních síťových rozhraní. K síťovému rozhraní (nebo virtuálnímu počítači, podle modelu nasazení) a podsíti, ke které se síťové rozhraní nebo virtuální počítač váže, můžete přidružit různé skupiny NSG. Priorita se určuje na základě směru provozu.

Při plánování virtuálních počítačů a virtuální sítě nezapomeňte [naplánovat](../articles/virtual-network/virtual-network-vnet-plan-design-arm.md) skupiny NSG.

Tato tabulka shrnuje metody, které můžete použít k vytvoření skupiny zabezpečení sítě.

| Metoda | Popis |
| ------ | ----------- |
| [portál Azure](../articles/virtual-network/tutorial-filter-network-traffic.md) | Když vytvoříte virtuální počítač na webu Azure Portal, automaticky se vytvoří skupina NSG a přidruží se k síťovému rozhraní, které tento portál vytvořil. Název této skupiny NSG je kombinací názvu virtuálního počítače a řetězce **-nsg**. Tato skupina NSG obsahuje jedno příchozí pravidlo s prioritou 1 000, nastavenou službou RDP, protokolem TCP, portem 3389 a akcí nastavenou na Povolit. Pokud chcete k tomuto virtuálnímu počítači povolit další příchozí provoz, musíte do této skupiny NSG přidat další pravidla. |
| [Azure PowerShell](../articles/virtual-network/tutorial-filter-network-traffic.md) | Použijte [new-aznetworksecurityruleconfig](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecurityruleconfig) a zadejte požadované informace o pravidlech. K vytvoření skupiny zabezpečení sítě použijte [skupinu New-AzNetworkSecurityGroup.](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecuritygroup) Pomocí [nástroje Set-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/set-azvirtualnetworksubnetconfig) nakonfigurujte skupinu zabezpečení sítě pro podsíť. Pomocí [set-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/set-azvirtualnetwork) přidejte nsg do virtuální sítě. |
| [Azure CLI](../articles/virtual-network/tutorial-filter-network-traffic-cli.md) | K úvodnímu vytvoření NSG použijte [az network nsg create](https://docs.microsoft.com/cli/azure/network/nsg). Pro přidání pravidel k NSG použijte [az network nsg rule create](https://docs.microsoft.com/cli/azure/network/nsg/rule). K přidání NSG do podsítě použijte [az network vnet subnet update](https://docs.microsoft.com/cli/azure/network/vnet/subnet). |
| [Šablona](../articles/virtual-network/template-samples.md) | Jako vodítko při nasazování skupiny zabezpečení sítě pomocí šablony použijte článek věnovaný [vytvoření skupiny zabezpečení sítě](https://github.com/Azure/azure-quickstart-templates/tree/master/101-security-group-create). |

## <a name="load-balancers"></a>Nástroje pro vyrovnávání zatížení

[Azure Load Balancer](../articles/load-balancer/load-balancer-overview.md) poskytuje vašim aplikacím vysokou dostupnost a síťový výkon. Nástroj pro vyrovnávání zatížení je možné nakonfigurovat tak, aby [vyrovnával příchozí přenosy z Internetu](../articles/load-balancer/load-balancer-internet-overview.md) k virtuálním počítačům nebo aby [vyrovnával přenosy mezi virtuálními počítači ve virtuální síti](../articles/load-balancer/load-balancer-internal-overview.md). Nástroj pro vyrovnávání zatížení může také vyrovnávat přenosy mezi místními a virtuálními počítači na různých místech v síti nebo předávat externí přenosy konkrétnímu virtuálnímu počítači.

Nástroj pro vyrovnávání zatížení mapuje příchozí a odchozí provoz mezi svou veřejnou IP adresou a portem a privátní IP adresou a portem virtuálního počítače.

Když vytvoříte nástroj pro vyrovnávání zatížení, musíte taky zvážit tyto konfigurační prvky:

- **Konfigurace IP front-endu** – Nástroj pro vyrovnávání zatížení může obsahovat jednu nebo více front-endových IP adres. Tyto IP adresy slouží jako vstup pro přenos.
- **Fond back-endových adres** – IP adresy, které jsou přiřazené síťovému rozhraní, na které se distribuuje zatížení.
- **[Předávání portů](../articles/load-balancer/tutorial-load-balancer-port-forwarding-portal.md)** – definuje, jak příchozí přenosy procházejí front-endovou IP adresou a distribuují se do back-endové IP adresy využívající příchozí pravidla NAT.
- **Pravidla nástroje pro vyrovnávání zatížení** – Mapují konkrétní kombinaci front-endové IP adresy a portu na sadu kombinací back-endových IP adres a portů. Nástroj pro vyrovnávání zatížení může mít několik pravidel vyrovnávání zatížení. Každé pravidlo je kombinací front-endové IP adresy a portu a back-endové IP adresy a portu přidružených k virtuálním počítačům.
- **[Sondy](../articles/load-balancer/load-balancer-custom-probe-overview.md)** – Monitorují stav virtuálních počítačů. Když sonda přestane reagovat, nástroj pro vyrovnávání zatížení zastaví odesílání nových připojení k virtuálnímu počítači, který není v pořádku. Stávající připojení to neovlivní a nová připojení jsou odesílaná virtuálním počítačům, které jsou v pořádku.
- **[Odchozí pravidla](../articles/load-balancer/load-balancer-outbound-rules-overview.md)** – odchozí pravidlo konfiguruje odchozí překlad síťových adres (NAT) pro všechny virtuální počítače nebo instance identifikované back-endovým fondem standardního nástroje pro vyrovnávání zatížení, které mají být přeloženy do front-endu.

Tato tabulka shrnuje metody, které můžete použít k vytvoření internetového nástroje pro vyrovnávání zatížení.

| Metoda | Popis |
| ------ | ----------- |
| portál Azure |  Internetový [provoz vyvažuje zatížení virtuálních počítačích pomocí portálu Azure](../articles/load-balancer/tutorial-load-balancer-standard-manage-portal.md). |
| [Azure PowerShell](/azure/load-balancer/load-balancer-get-started-ilb-arm-ps) | Chcete-li zadat identifikátor veřejné IP adresy, kterou jste dříve vytvořili, použijte [New-AzLoadBalancerFrontendIpConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerfrontendipconfig) s parametrem **-PublicIpAddress.** Pomocí [nástroje New-AzLoadBalancerBackendAddressComfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig) vytvořte konfiguraci fondu adres back-end. Pomocí [nástroje New-AzLoadBalancerInboundNatRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerinboundnatruleconfig) vytvořte příchozí pravidla NAT přidružená k konfiguraci ip adresy front-endu, kterou jste vytvořili. Použijte [New-AzLoadBalancerProbeConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerprobeconfig) k vytvoření sond, které potřebujete. Pomocí [nástroje New-AzLoadBalancerRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerruleconfig) vytvořte konfiguraci nástroje pro vyrovnávání zatížení. K vytvoření vykladače zatížení použijte [new-azloadbalancer.](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancer)|
| [Azure CLI](../articles/load-balancer/load-balancer-get-started-internet-arm-cli.md) | Pro vytvoření úvodní konfigurace nástroje pro vyrovnávání zatížení použijte [az network lb create](https://docs.microsoft.com/cli/azure/network/lb). Pro přidání veřejné IP adresy, kterou jste vytvořili dřív, použijte [az network lb frontend-ip create](https://docs.microsoft.com/cli/azure/network/lb/frontend-ip). K přidání konfigurace fondu back-endových adres použijte [az network lb address-pool create](https://docs.microsoft.com/cli/azure/network/lb/address-pool). K přidání pravidel NAT použijte [az network lb inbound-nat-rule create](https://docs.microsoft.com/cli/azure/network/lb/inbound-nat-rule). K přidání pravidel nástroje pro vyrovnávání zatížení použijte [az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule). K přidání sond použijte [az network lb probe create](https://docs.microsoft.com/cli/azure/network/lb/probe). |
| [Šablona](../articles/load-balancer/quickstart-load-balancer-standard-public-template.md) | Jako vodítko při nasazování nástroje pro vyrovnávání zatížení pomocí šablony použijte článek věnovaný [vytvoření dvou virtuálních počítačů v nástroji pro vyrovnávání zatížení a konfiguraci pravidel NAT v tomto prostředí](https://github.com/Azure/azure-quickstart-templates/tree/master/101-load-balancer-standard-create). |
    
Tato tabulka shrnuje metody, které můžete použít k vytvoření interního nástroje pro vyrovnávání zatížení.

| Metoda | Popis |
| ------ | ----------- |
| portál Azure | Zatížení [interního provozu můžete vyvážit pomocí vykladače zatížení na webu Azure Portal](../articles/load-balancer/tutorial-load-balancer-standard-internal-portal.md). |
| [Azure PowerShell](../articles/load-balancer/load-balancer-get-started-ilb-arm-ps.md) | Chcete-li zadat privátní ADRESU IP v podsíti sítě, použijte [parametr New-AzLoadBalancerFrontendIpConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerfrontendipconfig) s parametrem **-PrivateIpAddress.** Pomocí [nástroje New-AzLoadBalancerBackendAddressComfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig) vytvořte konfiguraci fondu adres back-end. Pomocí [nástroje New-AzLoadBalancerInboundNatRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerinboundnatruleconfig) vytvořte příchozí pravidla NAT přidružená k konfiguraci ip adresy front-endu, kterou jste vytvořili. Použijte [New-AzLoadBalancerProbeConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerprobeconfig) k vytvoření sond, které potřebujete. Pomocí [nástroje New-AzLoadBalancerRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerruleconfig) vytvořte konfiguraci nástroje pro vyrovnávání zatížení. K vytvoření vykladače zatížení použijte [new-azloadbalancer.](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancer)|
| [Azure CLI](../articles/load-balancer/load-balancer-get-started-ilb-arm-cli.md) | Pro vytvoření úvodní konfigurace nástroje pro vyrovnávání zatížení použijte příkaz [az network lb create](https://docs.microsoft.com/cli/azure/network/lb). K definování privátní IP adresy použijte [az network lb frontend-ip create](https://docs.microsoft.com/cli/azure/network/lb/frontend-ip) s parametrem **--private-ip-address**. K přidání konfigurace fondu back-endových adres použijte [az network lb address-pool create](https://docs.microsoft.com/cli/azure/network/lb/address-pool). K přidání pravidel NAT použijte [az network lb inbound-nat-rule create](https://docs.microsoft.com/cli/azure/network/lb/inbound-nat-rule). K přidání pravidel nástroje pro vyrovnávání zatížení použijte [az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule). K přidání sond použijte [az network lb probe create](https://docs.microsoft.com/cli/azure/network/lb/probe).|
| [Šablona](../articles/load-balancer/load-balancer-get-started-ilb-arm-template.md) | Jako vodítko při nasazování nástroje pro vyrovnávání zatížení pomocí šablony použijte článek věnovaný [vytvoření dvou virtuálních počítačů v nástroji pro vyrovnávání zatížení a konfiguraci pravidel NAT v tomto prostředí](https://github.com/Azure/azure-quickstart-templates/tree/master/201-2-vms-internal-load-balancer). |

### <a name="virtual-machine-scale-sets"></a>Škálovací sady virtuálních počítačů

Při práci se sadami škálování virtuálních strojů a nástrojpro vyrovnávání zatížení je třeba vzít v úvahu následující:

* **Škálovací sady virtuálních strojů nemohou používat stejný nástroj pro vyrovnávání zatížení**.
* **Port Forwarding a příchozí pravidla NAT**:
  * Každá škálovací sada virtuálního počítače musí mít pravidlo příchozího nat.
  * Při použití škálovací sady virtuálních strojů v back-endovém fondu nástroje pro vyrovnávání zatížení se automaticky vytvoří výchozí příchozí pravidla NAT, je to záměrné.
* **Pravidla vyrovnávání zatížení**:
  * Při použití škálovací sady virtuálních strojů v back-endovém fondu nástroje pro vyrovnávání zatížení se automaticky vytvoří výchozí pravidlo vyrovnávání zatížení, je to záměrné.
* **Odchozí pravidla**:
  *  Chcete-li vytvořit odchozí pravidlo pro fond back-endu, na který je již odkazováno pravidlem vyrovnávání zatížení, musíte nejprve označit **"Vytvořit implicitní odchozí pravidla"** jako **Ne** na portálu při vytvoření pravidla vyrovnávání příchozího zatížení.

  :::image type="content" source="./media/virtual-machines-common-network-overview/vmsslb.png" alt-text="Vytvoření pravidla vyrovnávání zatížení" border="true":::

Následující metody lze použít k nasazení škálovací sady virtuálních strojů s existujícím nástrojem pro vyrovnávání zatížení Azure.

* [Nakonfigurujte škálovací sadu virtuálních strojů pomocí existujícího nástroje Provynaci zatížení Azure pomocí portálu Azure](../articles/load-balancer/configure-vm-scale-set-portal.md).
* [Nakonfigurujte škálovací sadu virtuálních strojů pomocí existujícího nástroje Provynaci zatížení Azure pomocí Azure PowerShellu](../articles/load-balancer/configure-vm-scale-set-powershell.md).
* [Nakonfigurujte škálovací sadu virtuálních strojů pomocí existujícího nástroje pro vyrovnávání zatížení Azure pomocí rozhraní příkazového příkazového příkazu Kontu Azure](../articles/load-balancer/configure-vm-scale-set-cli.md).

## <a name="vms"></a>Virtuální počítače

Virtuální počítače je možné vytvořit ve stejné virtuální síti. Ke vzájemnému propojení můžou využít privátní IP adresy. Propojit se můžou v případě, že jsou v různých podsítích, a není přitom potřeba konfigurovat bránu nebo využívat veřejné IP adresy. Pokud chcete umístit virtuální počítač do virtuální sítě, vytvořte virtuální síť a potom jednotlivé virtuální počítače při jejich vytváření přiřazujte do této virtuální sítě a požadované podsítě. Virtuální počítače získají svoje síťové nastavení sítě během nasazení nebo spuštění.  

IP adresa se virtuálním počítačům přiřazuje v okamžiku jejich nasazení. Pokud do virtuální sítě nebo podsítě nasadíte několik virtuálních počítačů, IP adresy se jim přiřazují při spouštění. Můžete také přidělit statickou IP virtuálnímu počítačům. Pokud přidělíte statickou IP adresu, měli byste zvážit použití určité podsítě, abyste zabránili náhodnému opětovnému použití statické IP adresy pro jiný virtuální virtuální soud.  

Pokud vytvoříte virtuální počítač a chcete ho později migrovat do virtuální sítě, nejde o jednoduchou změnu konfigurace. Virtuální počítač je v takovém případě potřeba do virtuální sítě znovu nasadit. Nejjednodušší způsob, jak znovu nasadit virtuální počítač, je odstranit tento počítač, ale ne disky, které jsou k němu připojené, a potom ho znovu vytvořit ve virtuální síti s využitím původních disků. 

Tato tabulka shrnuje metody, které můžete použít k vytvoření virtuálního počítač ve virtuální síti.

| Metoda | Popis |
| ------ | ----------- |
| [portál Azure](../articles/virtual-machines/windows/quick-create-portal.md) | Využívá výchozí síťové nastavení, které bylo popsané dřív, k vytvoření virtuálního počítače s jedním síťovým rozhraním. K vytvoření virtuálního počítače s několika síťovými rozhraními musíte použít jinou metodu. |
| [Azure PowerShell](../articles/virtual-machines/windows/tutorial-manage-vm.md) | Zahrnuje použití [add-AzVMNetworkInterface](https://docs.microsoft.com/powershell/module/az.compute/add-azvmnetworkinterface) k přidání síťové rozhraní, které jste dříve vytvořili do konfigurace virtuálního počítače. |
| [Azure CLI](../articles/virtual-machines/linux/create-cli-complete.md) | Vytvořte a připojte virtuální ho svirtuální ho svitek, podsíť a síťovou síť, které se vytvářejí jako jednotlivé kroky. |
| [Šablona](../articles/virtual-machines/windows/ps-template.md) | Jako vodítko při nasazování virtuálního počítače pomocí šablony použijte článek věnovaný [velmi jednoduchému nasazení virtuálního počítače s Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows). |

## <a name="next-steps"></a>Další kroky
Kroky týkající se správy virtuálních sítí Azure pro virtuální počítače specifické pro virtuální počítače najdete [v](../articles/virtual-machines/windows/tutorial-virtual-network.md) tématu Windows nebo [Linux](../articles/virtual-machines/linux/tutorial-virtual-network.md) kurzy.

Existují také návody, jak vyvážit virtuální počítače a vytvořit vysoce dostupné aplikace pro [Windows](../articles/virtual-machines/windows/tutorial-load-balancer.md) nebo [Linux](../articles/virtual-machines/linux/tutorial-load-balancer.md).

- Naučte se konfigurovat [trasy definované uživatelem a předávání IP](../articles/virtual-network/virtual-networks-udr-overview.md). 
- Naučte se konfigurovat [připojení mezi virtuálními sítěmi](../articles/vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md).
- Naučte se [řešit potíže s trasami](../articles/virtual-network/diagnose-network-routing-problem.md).
- Další informace o [šířce pásma sítě virtuálních strojů](../articles/virtual-network/virtual-machine-network-throughput.md).
