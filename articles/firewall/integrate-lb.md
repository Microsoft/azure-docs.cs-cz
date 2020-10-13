---
title: Integrace Azure Firewallu s využitím služby Azure Standard Load Balancer
description: Azure Firewall můžete integrovat do virtuální sítě s využitím Azure Standard Load Balancer (veřejné nebo interní).
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 09/25/2020
ms.author: victorh
ms.openlocfilehash: 3bde4c11e9dc34be13efb25864fe75054d22bddb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91363099"
---
# <a name="integrate-azure-firewall-with-azure-standard-load-balancer"></a>Integrace Azure Firewallu s využitím služby Azure Standard Load Balancer

Azure Firewall můžete integrovat do virtuální sítě s využitím Azure Standard Load Balancer (veřejné nebo interní). 

Při návrhu se dává přednost integraci interního nástroje pro vyrovnávání zatížení se službou Azure Firewall, protože tento návrh je mnohem jednodušší. Pokud už máte nasazený veřejný nástroj pro vyrovnávání zatížení, který chcete zachovat, můžete použít i ten. Musíte však vědět o problému s asymetrickým směrováním, který může narušit fungování ve scénáři s veřejným nástrojem pro vyrovnávání zatížení.

Další informace o Azure Load Balancer najdete v tématu [co je Azure Load Balancer?](../load-balancer/load-balancer-overview.md)

## <a name="public-load-balancer"></a>Veřejný nástroj pro vyrovnávání zatížení

Pomocí veřejného nástroje pro vyrovnávání zatížení se nástroj pro vyrovnávání zatížení nasadí s veřejnou IP adresou front-endu.

### <a name="asymmetric-routing"></a>Asymetrické směrování

Asymetrické směrování je místo, kde paket přijímá jednu cestu k cíli a při návratu do zdroje používá jinou cestu. K tomuto problému dochází, když má podsíť výchozí trasu k privátní IP adrese brány firewall a používáte veřejný Nástroj pro vyrovnávání zatížení. V tomto případě se příchozí provoz nástroje pro vyrovnávání zatížení přijímá prostřednictvím veřejné IP adresy, ale návratová cesta prochází přes privátní IP adresu brány firewall. Vzhledem k tomu, že brána firewall je stavová, je vrácen návratový paket, protože brána firewall neví o takové zavedené relaci.

### <a name="fix-the-routing-issue"></a>Oprava problému s směrováním

Když nasadíte Azure Firewall do podsítě, jedním krokem je vytvoření výchozí trasy pro směrování paketů pomocí privátní IP adresy brány firewall umístěné na AzureFirewallSubnet. Další informace najdete v tématu [kurz: nasazení a konfigurace Azure firewall pomocí Azure Portal](tutorial-firewall-deploy-portal.md#create-a-default-route).

Když zavedete bránu firewall do svého scénáře nástroje pro vyrovnávání zatížení, budete chtít, aby se internetový provoz přihlásil přes veřejnou IP adresu brány firewall. Odtud brána firewall použije pravidla brány firewall a zanat pakety do veřejné IP adresy nástroje pro vyrovnávání zatížení. K tomuto problému dochází. Pakety přicházejí do veřejné IP adresy brány firewall, ale do brány firewall se vrátí pomocí privátní IP adresy (pomocí výchozí trasy).
Chcete-li se tomuto problému vyhnout, vytvořte další trasu hostitele pro veřejnou IP adresu brány firewall. Pakety směrované do veřejné IP adresy brány firewall se směrují přes Internet. Tím předejdete převzetí výchozí trasy k privátní IP adrese brány firewall.

![Asymetrické směrování](media/integrate-lb/Firewall-LB-asymmetric.png)

### <a name="route-table-example"></a>Příklad směrovací tabulky

Například následující trasy jsou pro bránu firewall na veřejné IP adrese 20.185.97.136 a privátní IP adresa 10.0.1.4.

> [!div class="mx-imgBorder"]
> ![Směrovací tabulka](media/integrate-lb/route-table.png)

### <a name="nat-rule-example"></a>Příklad pravidla překladu adres (NAT)

V následujícím příkladu pravidlo překladu adres (NAT) překládá provoz protokolu RDP do brány firewall na 20.185.97.136 na nástroj pro vyrovnávání zatížení na 20.42.98.220:

> [!div class="mx-imgBorder"]
> ![Pravidlo překladu adres (NAT)](media/integrate-lb/nat-rule-02.png)

### <a name="health-probes"></a>Sondy stavu

Pamatujte, že pokud používáte testy stavu TCP na port 80 nebo sondy HTTP/HTTPS, musíte mít spuštěnou webovou službu na hostitelích ve fondu nástroje pro vyrovnávání zatížení.

## <a name="internal-load-balancer"></a>Interní nástroj pro vyrovnávání zatížení

S interním nástrojem pro vyrovnávání zatížení je nasazený nástroj pro vyrovnávání zatížení s privátní IP adresou front-endu.

V tomto scénáři neexistuje žádný problém s asymetrickým směrováním. Příchozí pakety přicházejí do veřejné IP adresy brány firewall, přeloží se na privátní IP adresu nástroje pro vyrovnávání zatížení a potom se vrátí k privátní IP adrese brány firewall pomocí stejné návratové cesty.

Proto můžete tento scénář nasadit podobně jako veřejný scénář nástroje pro vyrovnávání zatížení, ale bez nutnosti trasy hostitele veřejné IP adresy brány firewall.

>[!NOTE]
>Virtuální počítače ve fondu back-end nebudou mít odchozí připojení k Internetu s touto konfigurací. </br> Další informace o poskytování odchozího připojení najdete v těchto tématech: </br> **[Odchozí připojení v Azure](../load-balancer/load-balancer-outbound-connections.md)**</br> Možnosti pro poskytování připojení: </br> **[Konfigurace nástroje pro vyrovnávání zatížení – pouze odchozí](../load-balancer/egress-only.md)** </br> [**Co je Virtual Network NAT?**](../virtual-network/nat-overview.md)


## <a name="additional-security"></a>Dodatečné zabezpečení

Chcete-li dále zvýšit zabezpečení vašeho scénáře s vyrovnáváním zatížení, můžete použít skupiny zabezpečení sítě (skupin zabezpečení sítě).

Můžete například vytvořit NSG v podsíti back-endu, kde jsou umístěné virtuální počítače s vyrovnáváním zatížení. Povolí příchozí provoz pocházející z IP adresy nebo portu brány firewall.

![Skupina zabezpečení sítě](media/integrate-lb/nsg-01.png)

Další informace o skupin zabezpečení sítě najdete v tématu [skupiny zabezpečení](../virtual-network/security-overview.md).

## <a name="next-steps"></a>Další kroky

- Přečtěte si, jak [nasadit a nakonfigurovat Azure firewall](tutorial-firewall-deploy-portal.md).