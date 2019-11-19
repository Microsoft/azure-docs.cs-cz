---
title: Integrace Azure Firewallu s využitím služby Azure Standard Load Balancer
description: Azure Firewall můžete integrovat do virtuální sítě s využitím Azure Standard Load Balancer (veřejné nebo interní).
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/19/2019
ms.author: victorh
ms.openlocfilehash: 91f34d06532b2d7f56d293df40939212a4f3d68c
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/19/2019
ms.locfileid: "74167071"
---
# <a name="integrate-azure-firewall-with-azure-standard-load-balancer"></a>Integrace Azure Firewallu s využitím služby Azure Standard Load Balancer

Azure Firewall můžete integrovat do virtuální sítě s využitím Azure Standard Load Balancer (veřejné nebo interní). 

Upřednostňovaným návrhem je integrace interního nástroje pro vyrovnávání zatížení s bránou firewall Azure, protože to je mnohem jednodušší návrh. Veřejný Nástroj pro vyrovnávání zatížení můžete použít, pokud už máte nasazený a chcete ho ponechat na svém místě. Je však třeba mít na paměti, že se jedná o problém s asymetrickým směrováním, který může přerušit funkčnost ve scénáři veřejného nástroje pro vyrovnávání zatížení.

Další informace o Azure Load Balancer najdete v tématu [co je Azure Load Balancer?](../load-balancer/load-balancer-overview.md)

## <a name="public-load-balancer"></a>Veřejný Nástroj pro vyrovnávání zatížení

Pomocí veřejného nástroje pro vyrovnávání zatížení se nástroj pro vyrovnávání zatížení nasadí s veřejnou IP adresou front-endu.

### <a name="asymmetric-routing"></a>Asymetrické směrování

Asymetrické směrování je místo, kde paket přijímá jednu cestu k cíli a při návratu do zdroje používá jinou cestu. K tomuto problému dochází, když má podsíť výchozí trasu k privátní IP adrese brány firewall a používáte veřejný Nástroj pro vyrovnávání zatížení. V tomto případě se příchozí provoz nástroje pro vyrovnávání zatížení přijímá prostřednictvím veřejné IP adresy, ale návratová cesta prochází přes privátní IP adresu brány firewall. Vzhledem k tomu, že brána firewall je stavová, se vrátí vracený paket, protože brána firewall tuto vytvořenou relaci neznají.

### <a name="fix-the-routing-issue"></a>Oprava problému s směrováním

Když nasadíte Azure Firewall do podsítě, jedním krokem je vytvoření výchozí trasy pro směrování paketů pomocí privátní IP adresy brány firewall umístěné na AzureFirewallSubnet. Další informace najdete v tématu [kurz: nasazení a konfigurace Azure firewall pomocí Azure Portal](tutorial-firewall-deploy-portal.md#create-a-default-route).

Když zavedete bránu firewall do svého scénáře nástroje pro vyrovnávání zatížení, budete chtít, aby se internetový provoz přihlásil přes veřejnou IP adresu brány firewall. Odtud brána firewall použije pravidla brány firewall a zanat pakety do veřejné IP adresy nástroje pro vyrovnávání zatížení. K tomuto problému dochází. Pakety přicházejí do veřejné IP adresy brány firewall, ale do brány firewall se vrátí pomocí privátní IP adresy (pomocí výchozí trasy).
Chcete-li se tomuto problému vyhnout, vytvořte další trasu hostitele pro veřejnou IP adresu brány firewall. Pakety směrované do veřejné IP adresy brány firewall se směrují přes Internet. Tím předejdete převzetí výchozí trasy k privátní IP adrese brány firewall.

![Asymetrické směrování](media/integrate-lb/Firewall-LB-asymmetric.png)

Například následující trasy jsou pro bránu firewall na veřejné IP adrese 13.86.122.41 a privátní IP adresa 10.3.1.4.

![Tabulka směrování](media/integrate-lb/route-table.png)

## <a name="internal-load-balancer"></a>Interní nástroj pro vyrovnávání zatížení

S interním nástrojem pro vyrovnávání zatížení je nasazený nástroj pro vyrovnávání zatížení s privátní IP adresou front-endu.

V tomto scénáři neexistuje žádný problém s asymetrickým směrováním. Příchozí pakety přicházejí do veřejné IP adresy brány firewall, přeloží se na privátní IP adresu nástroje pro vyrovnávání zatížení a potom se vrátí k privátní IP adrese brány firewall pomocí stejné návratové cesty.

Proto můžete tento scénář nasadit podobně jako veřejný scénář nástroje pro vyrovnávání zatížení, ale bez nutnosti trasy hostitele veřejné IP adresy brány firewall.

## <a name="additional-security"></a>Dodatečné zabezpečení

Chcete-li dále zvýšit zabezpečení vašeho scénáře s vyrovnáváním zatížení, můžete použít skupiny zabezpečení sítě (skupin zabezpečení sítě).

Můžete například vytvořit NSG v podsíti back-endu, kde jsou umístěné virtuální počítače s vyrovnáváním zatížení. Povolí příchozí provoz pocházející z IP adresy nebo portu brány firewall.

Další informace o skupin zabezpečení sítě najdete v tématu [skupiny zabezpečení](../virtual-network/security-overview.md).

## <a name="next-steps"></a>Další kroky

- Přečtěte si, jak [nasadit a nakonfigurovat Azure firewall](tutorial-firewall-deploy-portal.md).