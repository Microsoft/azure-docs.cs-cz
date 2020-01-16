---
title: Co je Azure Load Balancer?
titleSuffix: Azure Load Balancer
description: Přehled funkcí, architektury a implementace Azure Load Balanceru. Přečtěte si, jak Load Balancer funguje a jak ho používat v cloudu.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
Customer intent: As an IT administrator, I want to learn more about the Azure Load Balancer service and what I can use it for.
ms.devlang: na
ms.topic: overview
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 1/14/2019
ms.author: allensu
ms.openlocfilehash: dc986d40d50b93720c87ba36d265ed3044b0abc9
ms.sourcegitcommit: 05cdbb71b621c4dcc2ae2d92ca8c20f216ec9bc4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/16/2020
ms.locfileid: "76045397"
---
# <a name="what-is-azure-load-balancer"></a>Co je Azure Load Balancer?

*Vyrovnávání zatížení* označuje rovnoměrně distribuci zatížení (příchozí síťový provoz) napříč skupinou back-end prostředků nebo serverů. Azure nabízí [celou řadu možností pro vyrovnávání zatížení](https://docs.microsoft.com/azure/architecture/guide/technology-choices/load-balancing-overview) , ze kterých si můžete vybrat podle svých potřeb. Tento dokument popisuje Azure Load Balancer.

Azure Load Balancer funguje ve vrstvě čtyř modelu propojení Open Systems (OSI). Je jedním kontaktním bodem pro klienty. Nástroj pro vyrovnávání zatížení distribuuje nové příchozí toky, které dorazí na front-end služby Vyrovnávání zatížení do instancí back-endu fondu. Tyto toky jsou podle nakonfigurovaných pravidel vyrovnávání zatížení a sond stavu. Instance fondu back-endu můžou být Azure Virtual Machines nebo instancemi v sadě škálování virtuálního počítače.


**[Veřejný Nástroj pro vyrovnávání zatížení](./concepts-limitations.md#publicloadbalancer)** může poskytovat odchozí připojení virtuálních počítačů v rámci vaší virtuální sítě. Tato připojení se provádí pomocí překladu svých privátních IP adres na veřejné IP adresy. Veřejné nástroje pro vyrovnávání zatížení se používají k vyrovnávání zatížení internetového provozu na vašich virtuálních počítačích.

**[Interní (nebo soukromý) Nástroj pro vyrovnávání zatížení](./concepts-limitations.md#internalloadbalancer)** se používá, pokud jsou privátní IP adresy potřeba jenom na front-endu. Interní nástroje pro vyrovnávání zatížení se používají k vyrovnávání zatížení provozu v rámci virtuální sítě. Front-endu nástroje pro vyrovnávání zatížení je možné v hybridním scénáři použít z místní sítě.

<div align="center">
  <img src='./media/load-balancer-overview/IC744147.png'>
</div>

*Obrázek: vyrovnávání vícevrstvých aplikací s využitím veřejného i interního Load Balancer*

Další informace o jednotlivých součástech nástroje pro vyrovnávání zatížení najdete v tématu [Azure Load Balancer – komponenty a omezení](./concepts-limitations.md) .

>[!NOTE]
> Společnost Microsoft doporučuje [Standard Load Balancer](./load-balancer-standard-overview.md).
Samostatné virtuální počítače, skupiny dostupnosti a škálovací sady virtuálních počítačů je možné připojit pouze k jedné skladové položce, nikdy k oběma. Load Balancer a SKU veřejné IP adresy se musí shodovat, když je používáte s veřejnými IP adresami. Load Balancer a veřejné SKU IP nejsou proměnlivé.

## <a name="why-use-azure-load-balancer"></a>Proč použít Azure Load Balancer?
Pomocí Azure Load Balancer můžete škálovat aplikace a vytvářet vysoce dostupné služby. Nástroj pro vyrovnávání zatížení podporuje scénáře příchozího i odchozího přenosu. Load Balancer poskytuje nízkou latenci a vysokou propustnost a škáluje až milionů toků pro všechny aplikace TCP a UDP.

Mezi klíčové scénáře, které můžete provádět pomocí Azure Load Balancer patří:

- Vyrovnávání zatížení **[interního](https://docs.microsoft.com/azure/load-balancer/tutorial-load-balancer-standard-manage-portal)** a **[externího](https://docs.microsoft.com/azure/load-balancer/tutorial-load-balancer-standard-internal-portal)** provozu do virtuálních počítačů Azure.

- Zvyšte dostupnost díky distribuci prostředků **[v rámci](https://docs.microsoft.com/azure/load-balancer/tutorial-load-balancer-standard-public-zonal-portal)** zón a **[mezi](https://docs.microsoft.com/azure/load-balancer/tutorial-load-balancer-standard-public-zone-redundant-portal)** nimi.

- Konfigurace **[odchozího připojení](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections)** pro virtuální počítače Azure

- Pomocí **[sond stavu](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview)** můžete monitorovat prostředky s vyrovnáváním zatížení.

- Využívejte **[předávání portů](https://docs.microsoft.com/azure/load-balancer/tutorial-load-balancer-port-forwarding-portal)** pro přístup k virtuálním počítačům ve virtuální síti pomocí veřejné IP adresy a portu.

- Povolí podporu pro **[Vyrovnávání zatížení](https://docs.microsoft.com/azure/virtual-network/virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell)** **[IPv6](https://docs.microsoft.com/azure/virtual-network/ipv6-overview)** .

- Využijte **[metriky a diagnostiky](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-diagnostics)** pro Azure Load Balancer s **[Azure monitor](https://docs.microsoft.com/azure/azure-monitor/overview)** .

- Vyrovnávání zatížení služeb na **[několika portech, několika IP adresách nebo obojím](https://docs.microsoft.com/azure/load-balancer/load-balancer-multivip-overview)** .

- Přesuňte **[interní](https://docs.microsoft.com/azure/load-balancer/move-across-regions-internal-load-balancer-portal)** a **[externí](https://docs.microsoft.com/azure/load-balancer/move-across-regions-external-load-balancer-portal)** prostředky nástroje pro vyrovnávání zatížení napříč oblastmi Azure.

- Vyrovnávání zatížení TCP a UDP na všech portech současně pomocí **[portů ha](https://docs.microsoft.com/azure/load-balancer/load-balancer-ha-ports-overview)** .

## <a name="pricing"></a>Ceny

Účtuje se Standard Load Balancer využití.

* Počet nakonfigurovaných pravidel vyrovnávání zatížení a odchozích pravidel. Pravidla příchozího překladu adres (NAT) se nepočítají v celkovém počtu pravidel.
* Množství zpracovaných příchozích a odchozích dat nezávisle na pravidlech.

Informace o cenách Standard Load Balancer najdete v tématu [Load Balancer ceny](https://azure.microsoft.com/pricing/details/load-balancer/).

Load Balancer úrovně Basic se nabízí zdarma.

## <a name="sla"></a>Smlouva SLA

Informace o smlouvě SLA pro Standard Load Balancer najdete v tématu [SLA pro Load Balancer](https://aka.ms/lbsla).

## <a name="next-steps"></a>Další kroky

Pokud chcete začít s používáním Load Balancer, přečtěte si téma [Vytvoření veřejné Standard Load Balancer](quickstart-load-balancer-standard-public-portal.md) .

Další informace o omezeních Azure Load Balancer a součástech najdete v tématu [Azure Load Balancer koncepty a omezeních](./concepts-limitations.md) .
