---
title: Integrace brány Firewall na Azure s Azure Load balancer úrovně Standard
description: Zjistěte, jak integrovat Brána Firewall služby Azure s Azure Load balancer úrovně Standard
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 1/14/2019
ms.author: victorh
ms.openlocfilehash: 9d10383b84cbd1b85216cb9ae4582db4c7a10743
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2019
ms.locfileid: "54268070"
---
# <a name="integrate-azure-firewall-with-azure-standard-load-balancer"></a>Integrace brány Firewall na Azure s Azure Load balancer úrovně Standard

Brána Firewall Azure můžete integrovat do virtuální sítě pomocí Azure Load balancer úrovně Standard (veřejné nebo interní). Potřebujete vědět problém s asymetrickým směrováním, které mohou narušit funkce s scénář nástroje pro vyrovnávání zatížení veřejnou.

Další informace o nástroji Azure Load Balancer, naleznete v tématu [co je Azure Load Balancer?](../load-balancer/load-balancer-overview.md)

## <a name="public-load-balancer"></a>Veřejný load balancer

S nástrojem veřejný load balancer se nasadí nástroj pro vyrovnávání zatížení veřejnou front-endovou IP adresu.

### <a name="asymmetric-routing"></a>Asymetrické směrování

Asymetrické směrování je, když paket, který přijímá jednu cestu do cíle a používá jinou cestu při návratu ke zdroji. Problému může dojít, pokud má podsíť brány firewall na privátní IP adresa a nástroj pro vyrovnávání zatížení veřejnou výchozí trasa. V takovém případě příchozí provoz nástroje pro vyrovnávání zatížení je přijatá přes jeho veřejné IP adresy, ale návratový cesty prochází brány firewall na privátní IP adresu. Vzhledem k tomu, že brána firewall je stavový, zruší vracející paketů, protože brána firewall nemá žádné informace o navázanou relaci.

### <a name="fix-the-routing-issue"></a>Opravte chybu směrování

Když nasadíte do podsítě brány Firewall Azure, jeden krokem je vytvoření výchozí trasy pro směrování paketů podsítě prostřednictvím brány firewall na privátní IP adresy na AzureFirewallSubnet. Další informace najdete v tématu [kurzu: Nasazení a konfiguraci brány Firewall Azure pomocí webu Azure portal](tutorial-firewall-deploy-portal.md#create-a-default-route).

Když představovat brány firewall na váš scénář nástroje pro vyrovnávání zatížení, budete chtít internetové přenosy do režimu přes veřejnou IP adresu brány firewall. Odtud bránu firewall platí jeho pravidla brány firewall a NAT pakety na služby Vyrovnávání zatížení veřejnou IP adresu. Je to, kde k problému dochází. Pakety doručení na veřejnou IP adresu brány firewall na, ale vraťte se do brány firewall přes privátní IP adresu (s použitím výchozí trasa).
K tomuto problému vyhnout, vytvoření trasy další hostitele pro brány firewall na veřejnou IP adresu. Přejděte na veřejnou IP adresu brány firewall se směrují přes Internet. Tím se vyhnete trvá výchozí trasu k privátní IP adresu brány firewall.

Například následující trasy jsou brány firewall na veřejnou IP adresu 13.86.122.41 a privátní IP adresu 10.3.1.4.

![Tabulka směrování](media/integrate-lb/route-table.png)

## <a name="internal-load-balancer"></a>Interní nástroj pro vyrovnávání zatížení

Nástroje pro vyrovnávání zatížení interní nástroj pro vyrovnávání zatížení, je nasazeným privátní front-endovou IP adresu.

Neexistuje žádný problém s asymetrickým směrováním v tomto scénáři. Příchozí pakety přejít na veřejnou IP adresu brány firewall na získat přeložit na privátní IP adresu nástroje pro vyrovnávání zatížení a vrátí do brány firewall na privátní IP adresu pomocí stejný návratový cesty.

Ano můžete nasadit tento scénář podobný scénář nástroje pro vyrovnávání zatížení veřejnou, ale bez nutnosti pro bránu firewall veřejná IP adresa směrování hostitele.

## <a name="additional-security"></a>Zvýšení zabezpečení

Chcete-li dále zvýšit zabezpečení vašeho scénáře s vyrovnáváním zatížení, můžete použít skupiny zabezpečení sítě (Nsg).

Můžete například vytvořit skupinu zabezpečení sítě na podsíť back-endu, kde jsou umístěné virtuální počítače s vyrovnáváním zatížení. Umožňuje příchozí provoz pocházející z brány firewall protokolu IP adresa/port.

Další informace o skupinách Nsg najdete v tématu [skupiny zabezpečení](../virtual-network/security-overview.md).

## <a name="next-steps"></a>Další postup

- Zjistěte, jak [nasazení a konfiguraci brány Firewall Azure](tutorial-firewall-deploy-portal.md).