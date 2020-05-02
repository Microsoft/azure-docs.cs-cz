---
title: Typy Azure Load Balancer
description: Přehled typů Azure Load Balancer
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/30/2020
ms.author: allensu
ms.openlocfilehash: 31e2bf19967bb8870ee6ab75687bb3fcc37373f7
ms.sourcegitcommit: 1895459d1c8a592f03326fcb037007b86e2fd22f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/01/2020
ms.locfileid: "82629970"
---
# <a name="azure-load-balancer-types"></a>Typy Azure Load Balancer

Azure Load Balancer má dva typy a dvě SKU.

## <a name="public-load-balancer"></a><a name = "publicloadbalancer"></a>Veřejný Nástroj pro vyrovnávání zatížení

Veřejný Nástroj pro vyrovnávání zatížení mapuje veřejnou IP adresu a port příchozího provozu na privátní IP adresu a port virtuálního počítače. Nástroj pro vyrovnávání zatížení mapuje jiný způsob provozování odezvy z virtuálního počítače. Pomocí pravidel vyrovnávání zatížení můžete distribuovat konkrétní typy provozu napříč několika virtuálními počítači nebo službami. Můžete například rozložit zatížení provozu webových požadavků mezi několik webových serverů.

>[!NOTE]
>Pro každou skupinu dostupnosti můžete implementovat jenom jeden veřejný Nástroj pro vyrovnávání zatížení a jeden interní nástroj pro vyrovnávání zatížení.

Následující obrázek znázorňuje koncový bod s vyrovnáváním zatížení pro webový provoz, který je sdílen mezi třemi virtuálními počítači pro veřejný a port TCP 80. Tyto tři virtuální počítače se nacházejí ve skupině s vyrovnáváním zatížení.

![Příklad veřejného nástroje pro vyrovnávání zatížení](./media/load-balancer-overview/load-balancer.png)

*Obrázek: vyrovnávání webového provozu pomocí veřejného nástroje pro vyrovnávání zatížení*

Internetoví klienti odesílají žádosti webové stránky na veřejnou IP adresu webové aplikace na portu TCP 80. Azure Load Balancer distribuuje požadavky mezi tři virtuální počítače v sadě s vyrovnáváním zatížení. Další informace o algoritmech vyrovnávání zatížení najdete v tématu [koncepty nástroje pro vyrovnávání zatížení](concepts.md).

Ve výchozím nastavení Azure Load Balancer distribuuje síťový provoz rovnoměrně mezi několik instancí virtuálních počítačů. Můžete také nakonfigurovat spřažení relací. Další informace najdete v tématu [Konfigurace distribučního režimu pro Azure Load Balancer](load-balancer-distribution-mode.md).

## <a name="internal-load-balancer"></a><a name = "internalloadbalancer"></a>Interní nástroj pro vyrovnávání zatížení

Interní nástroj pro vyrovnávání zatížení distribuuje provoz do prostředků, které jsou uvnitř virtuální sítě. Azure omezuje přístup k IP adresám front-end virtuální sítě, které mají vyrovnaný vyrovnávání zatížení. 

Front-endové IP adresy a virtuální sítě se nikdy přímo nezveřejňují do internetového koncového bodu. Interní obchodní aplikace se spouštějí v Azure a přistupuje se k nim v rámci Azure nebo z místních prostředků.

Interní nástroj pro vyrovnávání zatížení umožňuje následující typy vyrovnávání zatížení:

* **V rámci virtuální sítě**: Vyrovnávání zatížení virtuálních počítačů ve virtuální síti až do sady virtuálních počítačů, které jsou ve stejné virtuální síti.
* **Pro virtuální síť mezi místními sítěmi**: Vyrovnávání zatížení z místních počítačů do sady virtuálních počítačů, které jsou ve stejné virtuální síti.
* **Pro vícevrstvé aplikace**: Vyrovnávání zatížení pro vícevrstvé aplikace s přístupem k Internetu, kde back-endové vrstvy nejsou přístupné z Internetu. Úrovně back-endu vyžadují vyrovnávání zatížení provozu z internetových vrstev. Podívejte se na další obrázek.
* **Pro obchodní aplikace:** Vyrovnávání zatížení pro obchodní aplikace hostované v Azure bez dalšího hardwaru nebo softwaru nástroje pro vyrovnávání zatížení. Tento scénář zahrnuje místní servery, které jsou v sadě počítačů, jejichž provoz je vyrovnaný vyrovnávání zatížení.

![Příklad interního Load Balanceru](./media/load-balancer-overview/load-balancer.png)

*Obrázek: vyrovnávání vícevrstvých aplikací s využitím veřejného i interního nástroje pro vyrovnávání zatížení*

## <a name="load-balancer-sku-comparison"></a><a name="skus"></a>Porovnání skladových položek Load Balanceru

Nástroj pro vyrovnávání zatížení podporuje skladové položky Basic i Standard. Tyto SKU se liší ve scénáři škálování, funkce a ceny. Pomocí nástroje Load Balancer úrovně Basic lze vytvořit libovolný scénář, který je možné použít pro nástroj pro vyrovnávání zatížení.

Porovnání a vysvětlení rozdílů najdete v následující tabulce. Další informace najdete v tématu [Přehled služby Azure Standard Load Balancer](load-balancer-standard-overview.md).

>[!NOTE]
> Microsoft doporučuje službu Load Balancer úrovně Standard.
Samostatné virtuální počítače, skupiny dostupnosti a škálovací sady virtuálních počítačů je možné připojit pouze k jedné skladové položce, nikdy k oběma. Nástroj pro vyrovnávání zatížení a SKU veřejné IP adresy se musí shodovat, když je používáte s veřejnými IP adresami. Služba Vyrovnávání zatížení a SKU veřejné IP adresy nejsou proměnlivé.

[!INCLUDE [comparison table](../../includes/load-balancer-comparison-table.md)]

Další informace najdete v tématu [omezení nástroje pro vyrovnávání zatížení](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#load-balancer). Podrobnosti o Load Balanceru úrovně Standard najdete v [přehledu](load-balancer-standard-overview.md), na stránce s [cenami](https://aka.ms/lbpricing) a ve [smlouvě SLA](https://aka.ms/lbsla).

## <a name="next-steps"></a>Další kroky

- Pokud chcete začít s používáním Load Balancer, přečtěte si téma [Vytvoření veřejné Standard Load Balancer](quickstart-load-balancer-standard-public-portal.md) .
- Přečtěte si o používání [Standard Load Balancer a zóny dostupnosti](load-balancer-standard-availability-zones.md).
- Seznamte se s [sondami stavu](load-balancer-custom-probe-overview.md).
- Přečtěte si o použití [Load Balancer pro odchozí připojení](load-balancer-outbound-connections.md).
- Přečtěte si o [Standard Load Balancer s pravidly pro vyrovnávání zatížení portů vysoké dostupnosti](load-balancer-ha-ports-overview.md).
- Přečtěte si další informace o [skupinách zabezpečení sítě](../virtual-network/security-overview.md).