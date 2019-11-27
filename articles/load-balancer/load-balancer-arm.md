---
title: Podpora Azure Resource Manager Load Balancer
description: V tomto článku použijte Azure PowerShell a šablony s Azure Load Balancer
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/19/2019
ms.author: allensu
ms.openlocfilehash: 57a60a65dee995695224aa1b451e848ea8768ab1
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2019
ms.locfileid: "74215390"
---
# <a name="azure-resource-manager-support-with-azure-load-balancer"></a>Podpora Azure Resource Manager s Azure Load Balancer



Azure Resource Manager je upřednostňovaná architektura pro správu služeb v Azure. Azure Load Balancer můžete spravovat pomocí rozhraní API a nástrojů založených na Azure Resource Manager.

## <a name="concepts"></a>Koncepty

V Správce prostředků Azure Load Balancer obsahuje následující podřízené prostředky:

* Konfigurace front-endové IP adresy – Nástroj pro vyrovnávání zatížení může zahrnovat jednu nebo víc IP adres front-endu, jinak se říká virtuální IP adresa (VIP). Tyto IP adresy slouží jako vstup pro přenos.
* Fond adres back-endu – tento fond je kolekcí IP adres přidružených k síťové kartě virtuálního počítače, do které je zatížení distribuováno.
* Pravidla vyrovnávání zatížení – vlastnost pravidla mapuje danou kombinaci IP adresy a portu front-endu na sadu IP adres back-endu a kombinaci portů. Jeden nástroj pro vyrovnávání zatížení může mít několik pravidel vyrovnávání zatížení. Každé pravidlo je kombinací IP adresy front-endu a portu a back-endové IP adresy a portu přidružených k virtuálním počítačům.
* Sondy – sondy umožňují sledovat stav instancí virtuálních počítačů. Pokud se sonda stavu nezdařila, instance virtuálního počítače se automaticky převezme z rotace.
* Pravidla příchozího překladu adres (NAT) – pravidla překladu adres (NAT) definující příchozí přenos přes front-end IP adresu a distribuované do back-endové IP adresy.

![](./media/load-balancer-arm/load-balancer-arm.png)

## <a name="quickstart-templates"></a>Šablony Rychlý start

Azure Resource Manager umožňuje zřizovat aplikace pomocí deklarativní šablony. S jednou šablonou můžete nasadit několik služeb společně s jejich závislostmi. Stejnou šablonu můžete použít k opakovanému nasazení aplikace během každé fáze životního cyklu této aplikace.

Šablony mohou zahrnovat definice pro:
* **Virtual Machines**
* **Virtuální sítě**
* **Skupiny dostupnosti**
* **Síťová rozhraní (nic)**
* **Účty úložiště**
* **Nástroje pro vyrovnávání zatížení**
* **Skupiny zabezpečení sítě**
* **Veřejné IP adresy.** 

Pomocí šablon můžete vytvořit vše, co potřebujete pro složitou aplikaci. Soubor šablony lze zkontrolovat do systému správy obsahu pro správu verzí a spolupráci.

[Další informace o šablonách](../azure-resource-manager/resource-manager-template-walkthrough.md)

[Další informace o síťových prostředcích](../networking/networking-overview.md)

Informace o šablonách pro rychlý Start pomocí Azure Load Balancer najdete v [úložišti GitHub](https://github.com/Azure/azure-quickstart-templates) , které je hostitelem sady šablon generovaných komunitou.

Příklady šablon:

* [2 virtuální počítače v Load Balancer a pravidla vyrovnávání zatížení](https://go.microsoft.com/fwlink/?LinkId=544799)
* [2 virtuální počítače ve virtuální síti s interními Load Balancer a pravidly nástroje pro vyrovnávání zatížení](https://go.microsoft.com/fwlink/?LinkId=544800)
* [2 virtuální počítače v nástroji pro vyrovnávání zatížení a konfigurují pravidla překladu adres (NAT) na kg](https://go.microsoft.com/fwlink/?LinkId=544801)

## <a name="setting-up-azure-load-balancer-with-a-powershell-or-cli"></a>Nastavení Azure Load Balancer pomocí PowerShellu nebo rozhraní příkazového řádku

Začínáme s rutinami Azure Resource Manager, nástroji příkazového řádku a rozhraními REST API

* K vytvoření Load Balancer můžete použít [rutiny sítě Azure](https://docs.microsoft.com/powershell/module/az.network#networking) .
* [Postup vytvoření nástroje pro vyrovnávání zatížení pomocí Azure Resource Manager](load-balancer-get-started-ilb-arm-ps.md)
* [Použití Azure CLI se správou prostředků Azure](../xplat-cli-azure-resource-manager.md)
* [Rozhraní REST API pro Load Balancer](https://msdn.microsoft.com/library/azure/mt163651.aspx)

## <a name="next-steps"></a>Další kroky

Začněte [vytvářet internetový nástroj pro vyrovnávání zatížení](load-balancer-get-started-internet-arm-ps.md) a nakonfigurujte typ [distribučního režimu](load-balancer-distribution-mode.md) pro konkrétní chování síťového provozu.

Naučte se spravovat [Nastavení časových limitů TCP nečinných pro nástroj pro vyrovnávání zatížení](load-balancer-tcp-idle-timeout.md). Tato nastavení jsou důležitá, pokud vaše aplikace potřebuje pro servery za nástrojem pro vyrovnávání zatížení zůstat připojení naživu.
