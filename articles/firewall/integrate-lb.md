---
title: Integrace Azure Firewallu s využitím služby Azure Standard Load Balancer
description: Azure Firewall můžete integrovat do virtuální sítě pomocí nástroje Azure Standard Load Balancer (veřejné ho nebo interní).
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 02/28/2020
ms.author: victorh
ms.openlocfilehash: ab9a500d9535b55702b8baff15f8cc47e6ac2c86
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78196693"
---
# <a name="integrate-azure-firewall-with-azure-standard-load-balancer"></a>Integrace Azure Firewallu s využitím služby Azure Standard Load Balancer

Azure Firewall můžete integrovat do virtuální sítě pomocí nástroje Azure Standard Load Balancer (veřejné ho nebo interní). 

Upřednostňovaným návrhem je integrace interního zařízení pro vyrovnávání zatížení s bránou firewall Azure, protože se jedná o mnohem jednodušší návrh. Veřejný vyvyčovávač zatížení můžete použít, pokud již máte jeden nasazený a chcete jej zachovat na místě. Musíte si však být vědomi asymetrického problému směrování, který může přerušit funkčnost ve scénáři veřejného vyrovnávání zatížení.

Další informace o Azure Load Balancer najdete v tématu [Co je Azure Balancer?](../load-balancer/load-balancer-overview.md)

## <a name="public-load-balancer"></a>Veřejný odvykač zatížení

S veřejný vyrovnávání zatížení, vyrovnávání zatížení je nasazen s veřejnou front-end IP adresu.

### <a name="asymmetric-routing"></a>Asymetrické směrování

Asymetrické směrování je místo, kde paket trvá jednu cestu k cíli a trvá jinou cestu při návratu ke zdroji. K tomuto problému dochází, když podsíť má výchozí trasu jít na privátní IP adresu brány firewall a používáte veřejný vyrovnávání zatížení. V tomto případě je příchozí provoz vykladače zatížení přijat prostřednictvím veřejné IP adresy, ale zpáteční cesta prochází privátní IP adresou brány firewall. Vzhledem k tomu, že brána firewall je stavová, zahodí vracející se paket, protože brána firewall si není vědoma takové zavedené relace.

### <a name="fix-the-routing-issue"></a>Oprava problému se směrováním

Při nasazení brány Azure Firewall do podsítě je jedním krokem vytvoření výchozí trasy pro podsíť, která řídí pakety prostřednictvím privátní IP adresy brány firewall umístěné v síti AzureFirewallSubnet. Další informace najdete [v tématu Výuka: Nasazení a konfigurace brány Azure Firewall pomocí portálu Azure](tutorial-firewall-deploy-portal.md#create-a-default-route).

Když zavedete bránu firewall do scénáře vyrovnávání zatížení, chcete, aby váš internetový provoz přišel prostřednictvím veřejné IP adresy brány firewall. Odtud brána firewall použije pravidla brány firewall a NAT pakety na veřejnou IP adresu vašeho vykladače zatížení. To je místo, kde dochází k problému. Pakety přicházejí na veřejnou IP adresu brány firewall, ale vrátí se do brány firewall přes privátní IP adresu (pomocí výchozí trasy).
Chcete-li se tomuto problému vyhnout, vytvořte další trasu hostitele pro veřejnou IP adresu brány firewall. Pakety na veřejnou IP adresu brány firewall jsou směrovány přes Internet. Tím se zabrání výchozí trasu na privátní IP adresu brány firewall.

![Asymetrické směrování](media/integrate-lb/Firewall-LB-asymmetric.png)

### <a name="route-table-example"></a>Příklad tabulky trasy

Například následující trasy jsou pro bránu firewall na veřejné IP adrese 20.185.97.136 a privátní IP adresu 10.0.1.4.

> [!div class="mx-imgBorder"]
> ![Směrovací tabulka](media/integrate-lb/route-table.png)

### <a name="nat-rule-example"></a>Příklad pravidla NAT

V následujícím příkladu pravidlo NAT překládá provoz RDP na bránu firewall na 20.185.97.136 na vyrovnávání zatížení na 20.42.98.220:

> [!div class="mx-imgBorder"]
> ![Pravidlo NAT](media/integrate-lb/nat-rule-02.png)

### <a name="health-probes"></a>Sondy stavu

Nezapomeňte, že musíte mít webovou službu spuštěnou na hostitelích ve fondu nástroj pro vyrovnávání zatížení, pokud používáte sondy stavu TCP na port 80 nebo http/https sondy.

## <a name="internal-load-balancer"></a>Interní nástroj pro vyrovnávání zatížení

S interním balancer, vyrovnávání zatížení je nasazen s privátní front-end IP adresu.

Neexistuje žádný asymetrický problém směrování s tímto scénářem. Příchozí pakety dorazí na veřejnou IP adresu brány firewall, přeloží se na privátní IP adresu vykladače zatížení a pak se vrátí na privátní IP adresu brány firewall pomocí stejné zpáteční cesty.

Takže můžete nasadit tento scénář podobný scénáři veřejného vyrovnávání zatížení, ale bez nutnosti trasy hostitele veřejné IP adresy firewallu.

## <a name="additional-security"></a>Dodatečné zabezpečení

Chcete-li dále zvýšit zabezpečení scénáře s vyrovnáváním zatížení, můžete použít skupiny zabezpečení sítě (NSGs).

Například můžete vytvořit skupinu sítě sítě v podsíti back-end, kde jsou umístěny virtuální počítače s vyrovnáváním zatížení. Povolit příchozí přenosy pocházející z adresy/portu IP brány firewall.

![Skupina zabezpečení sítě](media/integrate-lb/nsg-01.png)

Další informace o skupinách zabezpečení zabezpečení naleznete v [tématu Skupiny zabezpečení](../virtual-network/security-overview.md).

## <a name="next-steps"></a>Další kroky

- Přečtěte si, jak [nasadit a nakonfigurovat bránu Azure Firewall](tutorial-firewall-deploy-portal.md).