---
title: Připojení k Azure Data Lake Storage Gen1 z virtuální sítě | Dokumentace Microsoftu
description: Připojení k Azure Data Lake Storage Gen1 z virtuálních sítí Azure
services: data-lake-store,data-catalog
documentationcenter: ''
author: esung22
manager: jhubbard
editor: cgronlun
ms.assetid: 683fcfdc-cf93-46c3-b2d2-5cb79f5e9ea5
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.date: 01/31/2018
ms.author: elsung
ms.openlocfilehash: 130d0154fc0558ae7284e8407ba88fda3a2a53d5
ms.sourcegitcommit: 794bfae2ae34263772d1f214a5a62ac29dcec3d2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2018
ms.locfileid: "44391296"
---
# <a name="access-azure-data-lake-storage-gen1-from-vms-within-an-azure-vnet"></a>Přístup k Azure Data Lake Storage Gen1 z virtuálních počítačů v rámci virtuální síť Azure
Azure Data Lake Storage Gen1 je služba PaaS, která běží na veřejné internetové IP adresy. Libovolný server, který se může připojit k veřejnému Internetu obvykle může připojit k Azure Data Lake Storage Gen1 a koncových bodů. Ve výchozím nastavení všechny virtuální počítače, které jsou ve virtuálních sítích Azure přístup k Internetu a proto přístup k Azure Data Lake Storage Gen1. Nicméně je možné nakonfigurovat virtuální počítače ve virtuální síti nemáte přístup k Internetu. Pro tyto virtuální počítače přístup k Azure Data Lake Storage Gen1 omezen také. Blokování veřejný přístup k Internetu pro virtuální počítače ve virtuálních sítích Azure lze provést pomocí kteréhokoli z následujících postupů:

* Tím, že nakonfigurujete skupiny zabezpečení sítě (NSG)
* Pomocí konfigurace trasy definované uživatelem (UDR)
* Výměnou tras přes protokol BGP (odvětví standardní protokol dynamického směrování), když se používá služba ExpressRoute, tento blok přístup k Internetu

V tomto článku se dozvíte, jak povolit přístup k Azure Data Lake Storage Gen1 z virtuálních počítačů Azure, která byla omezena na přístup k prostředkům pomocí jedné ze tří metod uvedených výše.

## <a name="enabling-connectivity-to-azure-data-lake-storage-gen1-from-vms-with-restricted-connectivity"></a>Povolení připojení k Azure Data Lake Storage Gen1 z virtuálních počítačů s připojením s omezeným přístupem
Pro přístup k Azure Data Lake Storage Gen1 z takových virtuálních počítačů, je nutné nakonfigurovat je pro přístup k adresu IP, které je k dispozici účet Azure Data Lake Storage Gen1. IP adresy pro vaše účty Data Lake Storage Gen1 můžete identifikovat pomocí překladu názvů DNS z vašich účtů (`<account>.azuredatalakestore.net`). K překladu názvů DNS z vašich účtů, můžete použít nástroje, jako **nslookup**. Otevřete příkazový řádek ve vašem počítači a spusťte následující příkaz:

    nslookup mydatastore.azuredatalakestore.net

Výstup se podobá následující. Hodnoty s **adresu** vlastnost je IP adresa spojená s vaším účtem Data Lake Storage Gen1.

    Non-authoritative answer:
    Name:    1434ceb1-3a4b-4bc0-9c69-a0823fd69bba-mydatastore.projectcabostore.net
    Address:  104.44.88.112
    Aliases:  mydatastore.azuredatalakestore.net


### <a name="enabling-connectivity-from-vms-restricted-by-using-nsg"></a>Povolení připojení z virtuálních počítačů s omezením pomocí specifikátoru pomocí skupiny zabezpečení sítě
Když se pravidlo skupiny zabezpečení sítě používá k blokování přístupu k Internetu, můžete vytvořit další skupiny zabezpečení sítě, která umožňuje přístup k Data Lake Storage Gen1 IP adresu. Další informace o pravidlech NSG najdete v tématu [přehled skupin zabezpečení sítě](../virtual-network/security-overview.md). Pokyny k vytvoření skupin zabezpečení sítě najdete v tématu [jak vytvořit skupinu zabezpečení sítě](../virtual-network/tutorial-filter-network-traffic.md).

### <a name="enabling-connectivity-from-vms-restricted-by-using-udr-or-expressroute"></a>Povolení připojení z virtuálních počítačů s omezením pomocí specifikátoru pomocí směrování definované uživatelem nebo ExpressRoute
V případě trasy, trasy definované uživatelem nebo trasy protokolu BGP výměně používají k blokování přístupu k Internetu, je potřeba nakonfigurovat tak, aby virtuální počítače v těchto podsítích, můžete přístup ke koncovým bodům Gen1 úložiště Data Lake speciální trasy. Další informace najdete v tématu [trasy definované uživatelem přehled](../virtual-network/virtual-networks-udr-overview.md). Pokyny k vytvoření trasy definované uživatelem, najdete v článku [vytvářet trasy definované uživatelem v Resource Manageru](../virtual-network/tutorial-create-route-table-powershell.md).

### <a name="enabling-connectivity-from-vms-restricted-by-using-expressroute"></a>Povolení připojení z virtuálních počítačů s omezením pomocí specifikátoru pomocí ExpressRoute
Pokud je nakonfigurovaná okruhu ExpressRoute, dostanete na místních serverech Gen1 úložiště Data Lake prostřednictvím veřejného partnerského vztahu. Další podrobnosti o konfiguraci ExpressRoute pro veřejný partnerský vztah je k dispozici na [nejčastější dotazy k ExpressRoute](../expressroute/expressroute-faqs.md).

## <a name="see-also"></a>Další informace najdete v tématech
* [Přehled služby Azure Data Lake Storage Gen1](data-lake-store-overview.md)
* [Zabezpečení dat uložených v Azure Data Lake Storage Gen1](data-lake-store-security-overview.md)

