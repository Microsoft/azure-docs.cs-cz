---
title: Připojení k Azure Data Lake Storage Gen1 z virtuálních vnet | Dokumenty společnosti Microsoft
description: Připojení k Azure Data Lake Storage Gen1 z virtuálních disekonů Azure
services: data-lake-store,data-catalog
documentationcenter: ''
author: esung22
manager: mtillman
editor: cgronlun
ms.assetid: 683fcfdc-cf93-46c3-b2d2-5cb79f5e9ea5
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.date: 01/31/2018
ms.author: elsung
ms.openlocfilehash: c8d028a981d7811ed2c864db5750afc83ab93b2b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "60878864"
---
# <a name="access-azure-data-lake-storage-gen1-from-vms-within-an-azure-vnet"></a>Přístup k Azure Data Lake Storage Gen1 z virtuálních počítačů v rámci virtuální sítě Azure
Azure Data Lake Storage Gen1 je služba PaaS, která běží na veřejných internetových IP adresách. Každý server, který se může připojit k veřejnému Internetu, se obvykle může připojit také ke koncovým bodům Azure Data Lake Storage Gen1. Ve výchozím nastavení mohou všechny virtuální počítače, které jsou v virtuálních počítačích Azure, přistupovat k internetu, a proto mají přístup k Azure Data Lake Storage Gen1. Je však možné nakonfigurovat virtuální počítače ve virtuální síti tak, aby neměly přístup k Internetu. Pro takové virtuální počítače je omezen také přístup k Azure Data Lake Storage Gen1. Blokování veřejného přístupu k Internetu pro virtuální počítače v virtuálních počítačích Azure lze provést pomocí některého z následujících přístupů:

* Konfigurací skupin zabezpečení sítě (NSG)
* Konfigurací uživatelem definovaných tras (UDR)
* Výměnou tras prostřednictvím protokolu BGP (standardní dynamický směrovací protokol), pokud je použit ExpressRoute, které blokují přístup k Internetu

V tomto článku se dozvíte, jak povolit přístup k Azure Data Lake Storage Gen1 z virtuálních počítačů Azure, které byly omezeny na přístup k prostředkům pomocí jedné ze tří metod uvedených dříve.

## <a name="enabling-connectivity-to-azure-data-lake-storage-gen1-from-vms-with-restricted-connectivity"></a>Povolení připojení k Azure Data Lake Storage Gen1 z virtuálních počítačů s omezeným připojením
Chcete-li získat přístup k Azure Data Lake Storage Gen1 z těchto virtuálních počítačů, musíte je nakonfigurovat pro přístup k IP adrese pro oblast, kde je k dispozici účet Azure Data Lake Storage Gen1. Ip adresy pro oblasti účtu Data Lake Storage Gen1 můžete identifikovat překladem`<account>.azuredatalakestore.net`názvů DNS svých účtů ( . Chcete-li přeložit názvy DNS svých účtů, můžete použít nástroje, jako je **například nslookup**. Otevřete v počítači příkazový řádek a spusťte následující příkaz:

    nslookup mydatastore.azuredatalakestore.net

Výstup se podobá následující. Hodnota oproti vlastnosti **Address** je IP adresa přidružená k vašemu účtu Data Lake Storage Gen1.

    Non-authoritative answer:
    Name:    1434ceb1-3a4b-4bc0-9c69-a0823fd69bba-mydatastore.projectcabostore.net
    Address:  104.44.88.112
    Aliases:  mydatastore.azuredatalakestore.net


### <a name="enabling-connectivity-from-vms-restricted-by-using-nsg"></a>Povolení připojení z virtuálních zařízení omezené pomocí služby NSG
Pokud se k blokování přístupu k Internetu používá pravidlo nsg, můžete vytvořit jiný soubor zabezpečení sítě, který umožňuje přístup k IP adrese Data Lake Storage Gen1. Další informace o pravidlech skupiny zabezpečení sítě naleznete v [tématu Přehled skupin zabezpečení sítě](../virtual-network/security-overview.md). Pokyny k vytvoření skupin nsg naleznete [v tématu Vytvoření skupiny zabezpečení sítě](../virtual-network/tutorial-filter-network-traffic.md).

### <a name="enabling-connectivity-from-vms-restricted-by-using-udr-or-expressroute"></a>Povolení připojení z virtuálních zařízení omezené pomocí UDR nebo ExpressRoute
Při trasy, buď UDR nebo BGP-vyměnili trasy, se používají k zablokování přístupu k Internetu, speciální trasa musí být nakonfigurovántak, aby virtuální počítače v těchto podsítí přístup data Lake Storage Gen1 koncové body. Další informace naleznete v [tématu Přehled tras definovaných uživatelem](../virtual-network/virtual-networks-udr-overview.md). Pokyny k vytváření záznamů UDR naleznete [v tématu Vytvoření záznamů UDR ve Správci prostředků](../virtual-network/tutorial-create-route-table-powershell.md).

### <a name="enabling-connectivity-from-vms-restricted-by-using-expressroute"></a>Povolení připojení z virtuálních zařízení omezené pomocí ExpressRoute
Když je nakonfigurován okruh ExpressRoute, místní servery mohou přistupovat k datovému úložišti Data Lake Storage Gen1 prostřednictvím veřejného partnerského vztahu. Další podrobnosti o konfiguraci ExpressRoute pro veřejný partnerský vztah jsou k dispozici na [dotazech ExpressRoute FAQ](../expressroute/expressroute-faqs.md).

## <a name="see-also"></a>Viz také
* [Přehled Azure Data Lake Storage Gen1](data-lake-store-overview.md)
* [Zabezpečení dat uložených ve službě Azure Data Lake Storage Gen1](data-lake-store-security-overview.md)

