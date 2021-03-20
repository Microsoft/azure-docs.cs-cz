---
title: Připojení k Azure Data Lake Storage Gen1 z virtuální sítě | Microsoft Docs
description: Naučte se, jak povolit přístup k Azure Data Lake Storage Gen1 z virtuálních počítačů Azure, které mají omezený přístup k prostředkům.
services: data-lake-store,data-catalog
documentationcenter: ''
author: esung22
manager: mtillman
editor: cgronlun
ms.assetid: 683fcfdc-cf93-46c3-b2d2-5cb79f5e9ea5
ms.service: data-lake-store
ms.devlang: na
ms.topic: how-to
ms.date: 01/31/2018
ms.author: elsung
ms.openlocfilehash: e319cf9dfc01546607e20572c5bf4930fd974c75
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92104032"
---
# <a name="access-azure-data-lake-storage-gen1-from-vms-within-an-azure-vnet"></a>Přístup k Azure Data Lake Storage Gen1 z virtuálních počítačů v rámci virtuální sítě Azure
Azure Data Lake Storage Gen1 je služba PaaS, která běží na veřejných internetových IP adresách. Libovolný server, který se může připojit k veřejnému Internetu, se obvykle může připojit k Azure Data Lake Storage Gen1 koncovým bodům. Ve výchozím nastavení mají všechny virtuální počítače v Azure virtuální sítě přístup k Internetu, takže mají přístup k Azure Data Lake Storage Gen1. Je ale možné nakonfigurovat virtuální počítače ve virtuální síti, které nemají přístup k Internetu. Pro tyto virtuální počítače je také omezen přístup k Azure Data Lake Storage Gen1. Blokování veřejného přístupu k Internetu pro virtuální počítače v Azure virtuální sítě se dá udělat pomocí některého z následujících přístupů:

* Konfigurací skupin zabezpečení sítě (NSG)
* Konfigurací uživatelem definovaných tras (UDR)
* Když vyměňujete trasy prostřednictvím protokolu BGP (standardní protokol dynamického směrování), bude se při použití ExpressRoute blokovat přístup k Internetu.

V tomto článku se dozvíte, jak povolit přístup k Azure Data Lake Storage Gen1 z virtuálních počítačů Azure, které jsou omezené pro přístup k prostředkům pomocí jedné ze tří výše uvedených metod.

## <a name="enabling-connectivity-to-azure-data-lake-storage-gen1-from-vms-with-restricted-connectivity"></a>Povolení připojení k Azure Data Lake Storage Gen1 z virtuálních počítačů s omezeným připojením
Pokud chcete získat přístup k Azure Data Lake Storage Gen1 z takových virtuálních počítačů, musíte je nakonfigurovat pro přístup k IP adrese pro oblast, ve které je účet Azure Data Lake Storage Gen1 dostupný. IP adresy pro oblasti vašeho účtu Data Lake Storage Gen1 můžete určit tak, že vyřešíte názvy DNS vašich účtů ( `<account>.azuredatalakestore.net` ). K překladu názvů DNS vašich účtů můžete použít nástroje, jako je například **nslookup**. V počítači otevřete příkazový řádek a spusťte následující příkaz:

```console
nslookup mydatastore.azuredatalakestore.net
```

Výstup se podobá následujícímu. Hodnota proti vlastnosti **adresa** je IP adresa přidružená k vašemu Data Lake Storage Gen1 účtu.

```output
Non-authoritative answer:
Name:    1434ceb1-3a4b-4bc0-9c69-a0823fd69bba-mydatastore.projectcabostore.net
Address:  104.44.88.112
Aliases:  mydatastore.azuredatalakestore.net
```


### <a name="enabling-connectivity-from-vms-restricted-by-using-nsg"></a>Povolení připojení z virtuálních počítačů, které jsou omezené pomocí NSG
Pokud se k zablokování přístupu k Internetu použije pravidlo NSG, můžete vytvořit další NSG, které umožní přístup k Data Lake Storage Gen1 IP adrese. Další informace o pravidlech NSG najdete v tématu [Přehled skupin zabezpečení sítě](../virtual-network/network-security-groups-overview.md). Pokyny, jak vytvořit skupin zabezpečení sítě, najdete v tématu [jak vytvořit skupinu zabezpečení sítě](../virtual-network/tutorial-filter-network-traffic.md).

### <a name="enabling-connectivity-from-vms-restricted-by-using-udr-or-expressroute"></a>Povolení připojení z virtuálních počítačů, které jsou omezené pomocí UDR nebo ExpressRoute
Když se k zablokování přístupu k Internetu používají trasy udr nebo protokolu BGP, je nutné nakonfigurovat speciální trasu, aby virtuální počítače v těchto podsítích měly přístup k Data Lake Storage Gen1 koncovým bodům. Další informace najdete v tématu [Přehled uživatelsky definovaných tras](../virtual-network/virtual-networks-udr-overview.md). Pokyny k vytváření udr najdete v tématu [vytvoření udr v Správce prostředků](../virtual-network/tutorial-create-route-table-powershell.md).

### <a name="enabling-connectivity-from-vms-restricted-by-using-expressroute"></a>Povolení připojení z virtuálních počítačů, které jsou omezené pomocí ExpressRoute
Když je nakonfigurovaný okruh ExpressRoute, můžou místní servery získat přístup k Data Lake Storage Gen1 prostřednictvím veřejného partnerského vztahu. Další podrobnosti o konfiguraci ExpressRoute pro veřejný partnerský vztah najdete v tématu [Nejčastější dotazy k ExpressRoute](../expressroute/expressroute-faqs.md).

## <a name="see-also"></a>Viz také
* [Přehled Azure Data Lake Storage Gen1](data-lake-store-overview.md)
* [Zabezpečení dat uložených ve službě Azure Data Lake Storage Gen1](data-lake-store-security-overview.md)